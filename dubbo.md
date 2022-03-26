# 简介

它提供了三大核心能力：面向接口的远程方法调用，智能容错和负载均衡，以及服务自动注册和发现。

https://dubbo.apache.org/zh/docs/introduction/

# 示例

**pom**

```xml
   <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.dubbo</groupId>
            <artifactId>dubbo-spring-boot-starter</artifactId>
            <version>2.7.3</version>
            <exclusions>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.apache.dubbo</groupId>
            <artifactId>dubbo</artifactId>
            <version>2.7.3</version>
        </dependency>
        <dependency>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
            <version>3.3.3</version>
        </dependency>
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-framework</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-recipes</artifactId>
        </dependency>
    </dependencies>
```

**yaml**

```yml
server:
  port: 9001
spring:
  application:
    name: dubbo-provider9001
dubbo:
  protocol:
    name: dubbo
  registry:
    protocol: zookeeper
    address: 192.168.182.129:2181
```

**业务类**

```java
@RestController
public class OrderController {
    @Reference
    private UserServiceImpl userService;

    @GetMapping(value = "/get/user/{id}")
    public String getUser(@PathVariable String id) {
        return userService.getUserAddressById(id).toString();
    }
}
```

**另一个微服务的服务层**

```java
@Service(interfaceName = "UserServiceImpl",interfaceClass = UserService.class)
@Component
public class UserServiceImpl implements UserService {
    @Resource
    UserAddressDao userAddressDao;

    @Override
    public List<UserAddress> getUserAddressList(String userId) {
        return UserAddressDao.getUserAddressById(userId);
    } 
}
```

**主启动**

```java
@EnableDubbo//开启注解功能
@EnableDiscoveryClient
@SpringBootApplication
public class DubboConsumerMain9002 {
    public static void main(String[] args) {
        SpringApplication.run(DubboConsumerMain9002.class, args);
    }
}
```

**看官网吧**

https://dubbo.apache.org/zh/docs/references/configuration/annotation/

**配置**

```
JVM 启动 -D 参数优先，这样可以使用户在部署和启动时进行参数重写，比如在启动时需改变协议的端口。
XML 次之，如果在 XML 中有配置，则 dubbo.properties 中的相应配置项无效。
Properties 最后，相当于缺省值，只有 XML 没有配置时，dubbo.properties 的相应配置项才会生效，通常用于共享公共配置，比如应用名。
```

https://dubbo.apache.org/zh/docs/references/xml/

[高级用法 | Apache Dubbo](https://dubbo.apache.org/zh/docs/advanced/)

# 原理

## rpc原理

![image-20220114164943316](D:/mdimgs/image-20220114164943316.png)

```
一次完整的RPC调用流程（同步调用，异步另说）如下： 
1）服务消费方（client）调用以本地调用方式调用服务； 
2）client stub接收到调用后负责将方法、参数等组装成能够进行网络传输的消息体； 
3）client stub找到服务地址，并将消息发送到服务端； 
4）server stub收到消息后进行解码； 
5）server stub根据解码结果调用本地的服务； 
6）本地服务执行并将结果返回给server stub； 
7）server stub将返回结果打包成消息并发送至消费方； 
8）client stub接收到消息，并进行解码； 
9）服务消费方得到最终结果。
RPC框架的目标就是要2~8这些步骤都封装起来，这些细节对用户来说是透明的，不可见的。
```

## netty原理

![image-20220114165303739](D:/mdimgs/image-20220114165303739.png)

```
Selector 一般称 为选择器 ，也可以翻译为 多路复用器，
Connect（连接就绪）、Accept（接受就绪）、Read（读就绪）、Write（写就绪）
```

netty模型

![image-20220114165332676](D:/mdimgs/image-20220114165332676.png)

## dubbo原理

### 框架设计

![image-20220114165941282](D:/mdimgs/image-20220114165941282.png)

```
	config 配置层：对外配置接口，以 ServiceConfig, ReferenceConfig 为中心，可以直接初始化配置类，也可以通过 spring 解析配置生成配置类
	proxy 服务代理层：服务接口透明代理，生成服务的客户端 Stub 和服务器端 Skeleton, 以 ServiceProxy 为中心，扩展接口为 ProxyFactory
	registry 注册中心层：封装服务地址的注册与发现，以服务 URL 为中心，扩展接口为 RegistryFactory, Registry, RegistryService
	cluster 路由层：封装多个提供者的路由及负载均衡，并桥接注册中心，以 Invoker 为中心，扩展接口为 Cluster, Directory, Router, LoadBalance
	monitor 监控层：RPC 调用次数和调用时间监控，以 Statistics 为中心，扩展接口为 MonitorFactory, Monitor, MonitorService
	protocol 远程调用层：封装 RPC 调用，以 Invocation, Result 为中心，扩展接口为 Protocol, Invoker, Exporter
	exchange 信息交换层：封装请求响应模式，同步转异步，以 Request, Response 为中心，扩展接口为 Exchanger, ExchangeChannel, ExchangeClient, ExchangeServer
	transport 网络传输层：抽象 mina 和 netty 为统一接口，以 Message 为中心，扩展接口为 Channel, Transporter, Client, Server, Codec
	serialize 数据序列化层：可复用的一些工具，扩展接口为 Serialization, ObjectInput, ObjectOutput, ThreadPool
```

### 服务暴露

![image-20220114171846380](D:/mdimgs/image-20220114171846380.png)

### 服务引用

![image-20220114171858319](D:/mdimgs/image-20220114171858319.png)
