​                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               

# 零基础微服务架构理论入门

- 微服务是一种架构风格
- 一个应用拆分为一组小型服务
- 每个服务运行在自己的进程内，也就是可独立部署和升级
- 服务之间使用轻量级HTTP交互
- 服务围绕业务功能拆分
- 可以由全自动部署机制独立部署
- 去中心化，服务自治。服务可以使用不同的语言、不同的存储技术

# spring cloud 

## 微服务架构编码构建

### **微服务cloud整体聚合父工程Project**

**父工程POM**

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.wcl</groupId>
    <artifactId>springcloudtest</artifactId>
    <version>1.0-SNAPSHOT</version>
    <modules>
        <module>cloud-provider-payment8001</module>
        <module>cloud-consumer-order80</module>
        <module>cloud-api-commons</module>

        <module>cloud-eureka-server7001</module>
    </modules>
    <packaging>pom</packaging>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>12</maven.compiler.source>
        <maven.compiler.target>12</maven.compiler.target>
        <junit.version>4.12</junit.version>
        <lombok.version>1.18.22</lombok.version>
        <log4j.version>1.2.17</log4j.version>
        <mysql.version>8.0.26</mysql.version>
        <druid.version>1.1.6</druid.version>
        <mybatis.spring.boot.version>2.2.0</mybatis.spring.boot.version>
        <mybatis.version>3.5.7</mybatis.version>
    </properties>
<!--    <parent>-->
<!--        <groupId> org.springframework.boot </groupId>-->
<!--        <artifactId> spring-boot-starter-parent </artifactId>-->
<!--        <version> 2.0.3.RELEASE </version>-->
<!--    </parent>-->
    <!-- 子模块继承之后，提供作用：锁定版本+子module不用写groupId和version  -->
    <dependencyManagement>
        <dependencies>

            <!--spring boot 2.6.1-->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
<!--                <version>2.2.2.RELEASE</version>-->
                <version>2.6.1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--spring cloud 2021.0.0-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
<!--                <version>Hoxton.SR1</version>-->
                <version>2021.0.0</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--spring cloud alibaba 2.1.0.RELEASE-->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>2.1.0.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql.version}</version>
                <scope>runtime</scope>
            </dependency>
<!--            <dependency>-->
<!--                <groupId>org.springframework</groupId>-->
<!--                <artifactId>spring-jdbc</artifactId>-->
<!--                <version>5.3.11</version>-->
<!--            </dependency>-->
<!--            <dependency>-->
<!--                <groupId>com.alibaba</groupId>-->
<!--                <artifactId>druid</artifactId>-->
<!--                <version>${druid.version}</version>-->
<!--            </dependency>-->

            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid-spring-boot-starter</artifactId>
                <version>1.1.24</version>
            </dependency>

            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>${mybatis.spring.boot.version}</version>
            </dependency>
            <dependency>
                <groupId>org.mybatis</groupId>
                <artifactId>mybatis</artifactId>
                <version>${mybatis.version}</version>
            </dependency>
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>${junit.version}</version>
            </dependency>
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <version>${log4j.version}</version>
            </dependency>
            <dependency>
                <groupId>org.projectlombok</groupId>
                <artifactId>lombok</artifactId>
                <version>${lombok.version}</version>
                <optional>true</optional>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <fork>true</fork>
                    <addResources>true</addResources>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

### cloud-provider-payment8001微服务提供者支付Module模块

**改POM**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <parent>
        <artifactId>mscloud03</artifactId>
        <groupId>com.atguigu.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-provider-payment8001</artifactId>


    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>
        <!--mysql-connector-java-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <!--jdbc-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

**写YML**

```yaml
server:
  port: 8001

spring:
  application:
    name: cloud-payment-service
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource            # 当前数据源操作类型
    driver-class-name: com.mysql.cj.jdbc.Driver             # mysql驱动包 com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/db2019?useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: 123456


mybatis:
  mapperLocations: classpath:mapper/*.xml
  type-aliases-package: com.wcl.springcloud.entities   # 所有Entity别名类所在包

```

**主启动**

```java
/**
 * @Classname PaymentMain8001
 * @Description TODO
 * @Date 2021/12/7 10:34
 * @Created by 28327
 */

package com.wcl.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class PaymentMain8001 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8001.class, args);
    }
}
```

**entities**

**主实体Payment**

```java
/**
 * @Classname Payment
 * @Description TODO
 * @Date 2021/12/7 10:47
 * @Created by 28327
 */

package com.wcl.springcloud.entities;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.io.Serializable;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class Payment implements Serializable {
    private Long id;
    private String serial;
}
```

**Json封装体CommonResult**

```java
/**
 * @Classname CommonResult
 * @Description TODO
 * @Date 2021/12/7 10:48
 * @Created by 28327
 */

package com.wcl.springcloud.entities;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class CommonResult<T> {
    private Integer code;
    private String message;
    private T data;

    public CommonResult(Integer code, String message) {
        this(code, message, null);
    }
}
```

**dao**

**接口PaymentDao**

```java
package com.wcl.springcloud.dao;

import com.wcl.springcloud.entities.Payment;
import org.apache.ibatis.annotations.Mapper;
import org.springframework.web.bind.annotation.PathVariable;

@Mapper
public interface PaymentDao {
    int createPayment(Payment payment);

    Payment getPaymentById(@PathVariable("id") long id);
}

```

**mybaits的映射文件PaymentMapper.xml**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >

<mapper namespace="com.wcl.springcloud.dao.PaymentDao">

    <insert id="createPayment" parameterType="com.wcl.springcloud.entities.Payment"
            keyProperty="id" useGeneratedKeys="true">
        insert into payment(serial) value (#{serial})
    </insert>

    <!--    配置返回结果集-->
    <resultMap id="BaseResultMap" type="com.wcl.springcloud.entities.Payment">
        <id column="id" property="id"/>
        <result column="serial" property="serial"/>
    </resultMap>
    <select id="getPaymentById" parameterType="long" resultMap="BaseResultMap">
        select *
        from payment
        where id = #{id}
    </select>
</mapper>
```

**service**

**接口PaymentService**

```java
package com.wcl.springcloud.service;

import com.wcl.springcloud.entities.Payment;
import org.springframework.web.bind.annotation.PathVariable;

public interface PaymentService {
    int createPayment(Payment payment);

    Payment getPaymentById(@PathVariable("id") long id);
}

```

**实现类**

```java
/**
 * @Classname PaymentServiceImpl
 * @Description TODO
 * @Date 2021/12/7 11:18
 * @Created by 28327
 */

package com.wcl.springcloud.service.impl;

import com.wcl.springcloud.dao.PaymentDao;
import com.wcl.springcloud.entities.Payment;
import com.wcl.springcloud.service.PaymentService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class PaymentServiceImpl implements PaymentService {
    @Autowired
    private PaymentDao paymentDao;

    @Override
    public int createPayment(Payment payment) {
        return paymentDao.createPayment(payment);
    }

    @Override
    public Payment getPaymentById(long id) {
        return paymentDao.getPaymentById(id);
    }
}
```

**controller**

```java
/**
 * @Classname PaymentController
 * @Description TODO
 * @Date 2021/12/7 11:20
 * @Created by 28327
 */

package com.wcl.springcloud.controlller;

import com.wcl.springcloud.entities.CommonResult;
import com.wcl.springcloud.entities.Payment;
import com.wcl.springcloud.service.impl.PaymentServiceImpl;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

@RestController
@Slf4j
public class PaymentController {
    @Autowired
    private PaymentServiceImpl paymentService;


    @PostMapping("/payment/create")
    public CommonResult createPayment(@RequestBody Payment payment) {
        int result = paymentService.createPayment(payment);
        if (result > 0) {
            log.info("订单创建成功");
            return new CommonResult(200, "订单创建成功" + result, result);

        } else {
            log.info("订单创建失败");
            return new CommonResult(404, "订单创建失败", null);
        }
    }

    @GetMapping("/payment/get/{id}")
    public CommonResult getPaymentById(@PathVariable("id") long id) {
        Payment payment = paymentService.getPaymentById(id);
        if (payment != null) {
            log.info("查询成功" + payment);
            return new CommonResult(200, "查询成功", payment);
        } else {
            log.info("查询失败");
            return new CommonResult(404, "查询失败", null);
        }
    }
}
```

### cloud-consumer-order80微服务消费者订单Module模块

**POM**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>springcloudtest</artifactId>
        <groupId>com.wcl</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-consumer-order80</artifactId>
    <dependencies>
        <dependency><!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
            <groupId>com.wcl</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

</project>
```

**RestTemplate**

RestTemplate提供了多种便捷访问远程Http服务的方法， 
是一种简单便捷的访问restful服务模板类，是Spring提供的用于访问Rest服务的客户端模板工具集

```java
/**
 * @Classname ApplicationContextConfig
 * @Description TODO
 * @Date 2021/12/7 12:14
 * @Created by 28327
 */

package com.wcl.springcloud.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;

@Configuration
public class ApplicationContextConfig {
    //配置微服务之间的横向调用
    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

 **entities**

**主实体Payment**

```java
/**
 * @Classname Payment
 * @Description TODO
 * @Date 2021/12/7 10:47
 * @Created by 28327
 */

package com.wcl.springcloud.entities;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.io.Serializable;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class Payment implements Serializable {
    private Long id;
    private String serial;
}
```

**Json封装体CommonResult**

```java
/**
 * @Classname CommonResult
 * @Description TODO
 * @Date 2021/12/7 10:48
 * @Created by 28327
 */

package com.wcl.springcloud.entities;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class CommonResult<T> {
    private Integer code;
    private String message;
    private T data;

    public CommonResult(Integer code, String message) {
        this(code, message, null);
    }
}
```

**controller**

```java
/**
 * @Classname OrderController
 * @Description TODO
 * @Date 2021/12/7 12:16
 * @Created by 28327
 */

package com.wcl.springcloud.controller;

import com.wcl.springcloud.entities.CommonResult;
import com.wcl.springcloud.entities.Payment;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.client.RestTemplate;

@Slf4j
@RestController
public class OrderController {
    private final String PAYMENT_URL = "http://localhost:8001";
    @Autowired
    private RestTemplate restTemplate;

    @PostMapping("/consumer/payment/create")
    public CommonResult<Payment> createPayment(Payment payment) {
        return restTemplate.postForObject(PAYMENT_URL + "/payment/create", payment, CommonResult.class);
    }

    @GetMapping("/consumer/payment/get/{id}")
    public CommonResult<Payment> getPaymentById(@PathVariable("id") long id) {
        return restTemplate.getForObject(PAYMENT_URL + "/payment/get/" + id, CommonResult.class, id);
    }
}
```

###  工程重构

系统中有重复部分，重构

新建cloud-api-commons

**改pom**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>mscloud03</artifactId>
        <groupId>com.atguigu.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-api-commons</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-all</artifactId>
            <version>5.1.0</version>
        </dependency>
    </dependencies>
</project>
```

 **entities**

**主实体Payment**

```java
/**
 * @Classname Payment
 * @Description TODO
 * @Date 2021/12/7 10:47
 * @Created by 28327
 */

package com.wcl.springcloud.entities;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.io.Serializable;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class Payment implements Serializable {
    private Long id;
    private String serial;
}
```

**Json封装体CommonResult**

```java
/**
 * @Classname CommonResult
 * @Description TODO
 * @Date 2021/12/7 10:48
 * @Created by 28327
 */

package com.wcl.springcloud.entities;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class CommonResult<T> {
    private Integer code;
    private String message;
    private T data;

    public CommonResult(Integer code, String message) {
        this(code, message, null);
    }
}
```

**maven命令clean install**

**订单80和支付8001分别改造**

**删除各自的原先有过的entities文件夹**

**各自粘贴POM内容**

```xml
 <dependency>
 <!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
            <groupId>com.wcl</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
```

## 微服务注册和管理

### eureka

**服务治理**

Spring Cloud 封装了 Netflix 公司开发的 Eureka 模块来实现服务治理

      在传统的rpc远程调用框架中，管理每个服务与服务之间依赖关系比较复杂，管理比较复杂，所以需要使用服务治理，管理服务于服务之间依赖关系，可以实现服务调用、负载均衡、容错等，实现服务发现与注册。

**服务注册与发现**

```
Eureka采用了CS的设计架构，Eureka Server 作为服务注册功能的服务器，它是服务注册中心。而系统中的其他微服务，使用 Eureka的客户端连接到 Eureka Server并维持心跳连接。这样系统的维护人员就可以通过 Eureka Server 来监控系统中各个微服务是否正常运行。
在服务注册与发现中，有一个注册中心。当服务器启动的时候，会把当前自己服务器的信息 比如 服务地址通讯地址等以别名方式注册到注册中心上。另一方（消费者|服务提供者），以该别名的方式去注册中心上获取到实际的服务通讯地址，然后再实现本地RPC调用RPC远程调用框架核心设计思想：在于注册中心，因为使用注册中心管理每个服务与服务之间的一个依赖关系(服务治理概念)。在任何rpc远程框架中，都会有一个注册中心(存放服务地址相关信息(接口地址))
```

下左图是Eureka系统架构，右图是Dubbo的架构，请对比

![image-20211207212724920](D:/mdimgs/image-20211207212724920.png)

**Eureka Server和Eureka Client**

```
Eureka Server提供服务注册服务
各个微服务节点通过配置启动后，会在EurekaServer中进行注册，这样EurekaServer中的服务注册表中将会存储所有可用服务节点的信息，服务节点的信息可以在界面中直观看到。
 
EurekaClient通过注册中心进行访问
是一个Java客户端，用于简化Eureka Server的交互，客户端同时也具备一个内置的、使用轮询(round-robin)负载算法的负载均衡器。在应用启动后，将会向Eureka Server发送心跳(默认周期为30秒)。如果Eureka Server在多个心跳周期内没有接收到某个节点的心跳，EurekaServer将会从服务注册表中把这个服务节点移除（默认90秒）
```

#### 单机模式

##### IDEA生成eurekaServer端服务注册中心类似物业公司

**改POM**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>springcloudtest</artifactId>
        <groupId>com.wcl</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-eureka-server7001</artifactId>
    <dependencies>
        <!--eureka-server-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
            <version>2.2.1.RELEASE</version>
        </dependency>

        <dependency>
            <!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
            <groupId>com.wcl</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

</project>
```

**写YML**

```yaml
eureka:
  instance:
    hostname: localhost #eureka服务端的实例名称
  client:
    #false表示不向注册中心注册自己。
    register-with-eureka: false
    #false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    fetch-registry: false
    service-url:
    #设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址。
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```

**主启动**

```java
/**
 * @Classname EurekaMain7001
 * @Description TODO
 * @Date 2021/12/7 20:44
 * @Created by 28327
 */

package com.wcl.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;


@SpringBootApplication
@EnableEurekaServer
public class EurekaMain7001 {
    public static void main(String[] args) {
        SpringApplication.run(EurekaMain7001.class, args);
    }
}
```

##### EurekaClient端cloud-provider-payment8001

**pom**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>springcloudtest</artifactId>
        <groupId>com.wcl</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-provider-payment8001</artifactId>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>
        <!--mysql-connector-java-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <!--jdbc-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.wcl</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
            <scope>compile</scope>
        </dependency>
        <!--eureka-client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
    </dependencies>
    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

</project>
```

**yml**

```yaml
spring:
  application:
    name: cloud-payment-service
    #注册进Eureka也会应用此名字

eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetchRegistry: true
    service-url:
      defaultZone: http://localhost:7001/eureka
```

**主启动**

```java
/**
 * @Classname PaymentMain8001
 * @Description TODO
 * @Date 2021/12/7 10:34
 * @Created by 28327
 */

package com.wcl.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
public class PaymentMain8001 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8001.class, args);
    }
}
```

##### EurekaClient端cloud-consumer-order80将注册进EurekaServer成为服务消费者consumer

同理

#### 集群构建

##### EurekaServer集群环境构建步骤

**新建cloud-eureka-server7002**

**改POM**

```xml
 
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>mscloud03</artifactId>
        <groupId>com.atguigu.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-eureka-server7002</artifactId>



    <dependencies>
        <!--eureka-server-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
        <dependency>
            <groupId>com.atguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <!--boot web actuator-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--一般通用配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
        </dependency>
    </dependencies>
</project>
```

**修改映射配置**

```
127.0.0.1  eureka7001.com
127.0.0.1  eureka7002.com
```

**修改yml**（相互注册）

**7001**

```yaml
server:
  port: 7001


eureka:
  instance:
    hostname: eureka7001.com #eureka服务端的实例名称
  client:
    register-with-eureka: false     #false表示不向注册中心注册自己。
    fetch-registry: false     #false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    service-url:
      defaultZone: http://eureka7002.com:7002/eureka/
```

**7002**

```yaml
server:
  port: 7002


eureka:
  instance:
    hostname: eureka7002.com #eureka服务端的实例名称
  client:
    register-with-eureka: false     #false表示不向注册中心注册自己。
    fetch-registry: false     #false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/
```

##### 将支付服务8001微服务发布到上面2台Eureka集群配置中

**yml**

```yaml
server:
  port: 8001

spring:
  application:
    name: cloud-payment-service
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource            # 当前数据源操作类型
    driver-class-name: org.gjt.mm.mysql.Driver              # mysql驱动包
    url: jdbc:mysql://localhost:3306/db2019?useUnicode=true&characterEncoding=utf-8&useSSL=false
    username: root
    password: 123456


eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetchRegistry: true
    service-url:
      #defaultZone: http://localhost:7001/eureka
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka  # 集群版

mybatis:
  mapperLocations: classpath:mapper/*.xml
  type-aliases-package: com.atguigu.springcloud.entities    # 所有Entity别名类所在包
```

##### 支付服务提供者8002集群环境构建

**与8001服务几乎一致**

**Controller**

```java
/**
 * @Classname PaymentController
 * @Description TODO
 * @Date 2021/12/7 11:20
 * @Created by 28327
 */

package com.wcl.springcloud.controlller;

import com.wcl.springcloud.entities.CommonResult;
import com.wcl.springcloud.entities.Payment;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.*;
import com.wcl.springcloud.service.impl.PaymentServiceImpl;

@RestController
@Slf4j
public class PaymentController {
    @Autowired
    private PaymentServiceImpl paymentService;

    //识别微服务的标志
    @Value("${server.port}")
    private String serverPort;
    @PostMapping("/payment/create")
    public CommonResult createPayment(@RequestBody Payment payment) {
        int result = paymentService.createPayment(payment);
        if (result > 0) {
            log.info("订单创建成功");
            return new CommonResult(200, "订单创建成功" + result, result);

        } else {
            log.info("订单创建失败");
            return new CommonResult(404, "订单创建失败", null);
        }
    }

    @GetMapping("/payment/get/{id}")
    public CommonResult getPaymentById(@PathVariable("id") long id) {
        Payment payment = paymentService.getPaymentById(id);
        if (payment != null) {
            log.info("查询成功" + payment);
            return new CommonResult(200, "查询成功,端口号为"+serverPort, payment);
        } else {
            log.info("查询失败");
            return new CommonResult(404, "查询失败", null);
        }
    }
}
```

#### 负载均衡

**order服务地址不能写死**

```java
public static final String PAYMENT_SRV = "http://CLOUD-PAYMENT-SERVICE";
```

**config**

```java
@Configuration
public class ApplicationContextBean
{
    @Bean
    @LoadBalanced //使用@LoadBalanced注解赋予RestTemplate负载均衡的能力
    public RestTemplate getRestTemplate()
    {
        return new RestTemplate();
    }
}
```

##### actuator微服务信息完善

##### 主机名称:服务名称修改

**修改cloud-provider-payment8001**

**YML**

```yaml
eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetchRegistry: true
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka  # 集群版
      #defaultZone: http://localhost:7001/eureka  # 单机版
  instance:
    instance-id: payment8001  #名称修改
```

**访问信息有IP信息提示**

```yaml
eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetchRegistry: true
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka  # 集群版
      #defaultZone: http://localhost:7001/eureka  # 单机版
  instance:
    instance-id: payment8001
    prefer-ip-address: true     #访问路径可以显示IP地址
```

##### 服务发现Discovery

**对于注册进eureka里面的微服务，可以通过服务发现来获得该服务的信息**

**修改cloud-provider-payment8001的Controller**

```java
    @Autowired
    private DiscoveryClient discoveryClient;

    @GetMapping(value = "/payment/discovery")
    public Object discovery() {
        List<String> services = discoveryClient.getServices();
        for (String element : services) {
            System.out.println(element);
        }

        List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
        for (ServiceInstance element : instances) {
            System.out.println(element.getServiceId() + "\t" + element.getHost() + "\t" + element.getPort() + "\t"
                    + element.getUri());
        }
        return this.discoveryClient;
    }
```

**main**

```java

@SpringBootApplication
@EnableEurekaClient
@EnableDiscoveryClient
public class PaymentMain8001 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8001.class, args);
    }
}
```

#### Eureka自我保护

**概述**
保护模式主要用于一组客户端和Eureka Server之间存在网络分区场景下的保护。一旦进入保护模式，
**Eureka Server将会尝试保护其服务注册表中的信息，不再删除服务注册表中的数据，也就是不会注销任何微服务。**

**怎么禁止自我保护**

**注册中心eureakeServer端7001**

```yaml
eureka:
  instance:
    hostname: eureka7001.com
  client:
    register-with-eureka: false
    fetch-registry: false
    service-url:
      #defaultZone: http://eureka7002.com:7002/eureka,http://eureka7003.com:7003/eureka
      defaultZone: http://eureka7001.com:7001/eureka
  server:
    #关闭自我保护机制，保证不可用服务被及时踢除
       enable-self-preservation: false
    eviction-interval-timer-in-ms: 2000
```

**生产者客户端eureakeClient端8001**

```yaml
eureka:
  client: #服务提供者provider注册进eureka服务列表内
    service-url:
      register-with-eureka: true
      fetch-registry: true
      # cluster version
      #defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka,http://eureka7003.com:7003/eureka
      # singleton version
      defaultZone: http://eureka7001.com:7001/eureka
#心跳检测与续约时间
#开发时设置小些，保证服务关闭后注册中心能即使剔除服务
  instance:
  #Eureka客户端向服务端发送心跳的时间间隔，单位为秒(默认是30秒)
    lease-renewal-interval-in-seconds: 1
  #Eureka服务端在收到最后一次心跳后等待时间上限，单位为秒(默认是90秒)，超时将剔除服务
    lease-expiration-duration-in-seconds: 2

```

### zookeeper

#### 服务提供者

**新建cloud-provider-payment8004**

**pom**

```xml
<dependencies>
        <!-- SpringBoot整合Web组件 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency><!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
            <groupId>com.atguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <!-- SpringBoot整合zookeeper客户端 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

**yml**

```yaml
#8004表示注册到zookeeper服务器的支付服务提供者端口号
server:
  port: 8004
#服务别名----注册zookeeper到注册中心名称
spring:
  application:
    name: cloud-provider-payment
  cloud:
    zookeeper:
      connect-string: 192.168.182.129:2181
```

**Controller**

```java
/**
 * @Classname PaymentController
 * @Description TODO
 * @Date 2021/12/11 21:33
 * @Created by 28327
 */

package com.wcl.springcloud.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.UUID;

@RestController
public class PaymentController {
    @Value("${server.port}")
    private String serverPort;

    @GetMapping("/payment/consul")
    public String paymentInfo() {
        return "springcloud with consul: " + serverPort + "\t\t" + UUID.randomUUID().toString();
    }
}
```

#### 服务消费者

**pom**

```xml
<dependencies>
        <!-- SpringBoot整合Web组件 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- SpringBoot整合zookeeper客户端 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
            <!--先排除自带的zookeeper-->
            <exclusions>
                <exclusion>
                    <groupId>org.apache.zookeeper</groupId>
                    <artifactId>zookeeper</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <!--添加zookeeper3.4.9版本-->
        <dependency>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
            <version>3.4.9</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

```

**config**

```java
@Configuration
public class Config {
    @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

**YML**

```yaml
server:
  port: 8080
spring:
  application:
    name: cloud-order-service
  cloud:
    #注册到zookeeper地址
    zookeeper:
      connect-string: 192.168.182.129:2181
```

**controller**

```java
/**
 * @Classname OrderController
 * @Description TODO
 * @Date 2021/12/11 20:48
 * @Created by 28327
 */

package com.wcl.springcloud.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

@RestController
public class OrderController {
    public static final String INVOKE_URL = "http://cloud-provider-payment";

    @Autowired
    private RestTemplate restTemplate;

    @GetMapping(value = "/consumer/payment/zk")
    public String paymentInfo() {
        String result = restTemplate.getForObject(INVOKE_URL + "/payment/zk", String.class);
        System.out.println("消费者调用支付服务(zookeeper)--->result:" + result);
        return result;
    }

}
```

### Consul

**简介**

```
服务发现 提供HTTP和DNS两种发现方式。
健康监测 支持多种方式，HTTP、TCP、Docker、Shell脚本定制化监控
KV存储 Key、Value的存储方式
多数据中心 Consul支持多数据中心
可视化Web界面 8500--port
```

#### **环境配置**

```docker
docker pull consul
docker run -it --name=springconsul -p8500:8500 consul
```

#### **服务提供者**

**新建Module支付服务provider8005**

**pom**

```xml
<dependencies>
        <!--SpringCloud consul-server -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-consul-discovery</artifactId>
        </dependency>
        <!-- SpringBoot整合Web组件 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--日常通用jar包配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

**yml**

```yaml
server:
  port: 8005

spring:
  application:
    name: consul-provider-payment
  ####consul注册中心地址
  cloud:
    consul:
      host: 192.168.182.129
      port: 8500
      discovery:
        #hostname: 127.0.0.1
        service-name: ${spring.application.name}
        #心跳机制，防止找不到实例的异常
        heartbeat:
          enabled: true
```

**controller**

```java
@RestController
public class PaymentController {
    @Value("${server.port}")
    private String serverPort;

    @GetMapping("/payment/consul")
    public String paymentInfo() {
        return "springcloud with consul: " + serverPort + "\t\t" + UUID.randomUUID().toString();
    }
}
```

#### 服务消费者

**pom**

```xml
 <dependencies>
        <!--SpringCloud consul-server -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-consul-discovery</artifactId>
        </dependency>
        <!-- SpringBoot整合Web组件 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--日常通用jar包配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

**yml**

```yaml
server:
  port: 8080

spring:
  application:
    name: cloud-consumer-order
  ####consul注册中心地址
  cloud:
    consul:
      host: 192.168.182.129
      port: 8500
      discovery:
        #hostname: 127.0.0.1
        service-name: ${spring.application.name}
        heartbeat:
          enabled: true
```

**config**

与zookeeper注册一致

```java
/**
 * @Classname ConsulConfig
 * @Description TODO
 * @Date 2021/12/11 21:24
 * @Created by 28327
 */

package com.wcl.springcloud.config;

import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;

@Configuration
public class ConsulConfig {
    @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

**controller**

```java
/**
 * @Classname OrderController
 * @Description TODO
 * @Date 2021/12/11 21:25
 * @Created by 28327
 */

package com.wcl.springcloud.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

@RestController
public class OrderController {
    public static final String INVOKE_URL = "http://consul-provider-payment"; //consul-provider-payment

    @Autowired
    private RestTemplate restTemplate;

    @GetMapping(value = "/consumer/payment/consul")
    public String paymentInfo()
    {
        String result = restTemplate.getForObject(INVOKE_URL+"/payment/consul", String.class);
        System.out.println("消费者调用支付服务(consule)--->result:" + result);
        return result;
    }

}
```

### 三个注册中心异同点

**CAP**

```
C:Consistency（强一致性）
A:Availability（可用性）
P:Partition tolerance（分区容错性）
CAP理论关注粒度是数据，而不是整体系统设计的策略
```

**AP(Eureka)**

```
AP架构
当网络分区出现后，为了保证可用性，系统B可以返回旧值，保证系统的可用性。
结论：违背了一致性C的要求，只满足可用性和分区容错，即AP
```

**CP(Zookeeper/Consul)**

```
CP架构
当网络分区出现后，为了保证一致性，就必须拒接请求，否则无法保证一致性
结论：违背了可用性A的要求，只满足一致性和分区容错，即CP
```

## 负载均衡

### Ribbon

#### Ribbon负载均衡演示

**负载均衡+RestTemplate调用**

**架构说明**

总结：Ribbon其实就是一个软负载均衡的客户端组件，
他可以和其他所需请求的客户端结合使用，和eureka结合只是其中的一个实例。

**POM**

```xml
  <!--eureka-client中集成了Ribbon依赖--> 
<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>

  <!--也可以单独引入--> 
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
</dependency>
```

#### **Ribbon核心组件IRule**

```
com.netflix.loadbalancer.RoundRobinRule 轮询
com.netflix.loadbalancer.RandomRule 随机
com.netflix.loadbalancer.RetryRule 先按照RoundRobinRule的策略获取服务，如果获取服务失败则在指定时间内会进行重试，获取可用的服务
WeightedResponseTimeRule 对RoundRobinRule的扩展，响应速度越快的实例选择权重越大，越容易被选择
BestAvailableRule 会先过滤掉由于多次访问故障而处于断路器跳闸状态的服务，然后选择一个并发量最小的服务
AvailabilityFilteringRule 先过滤掉故障实例，再选择并发较小的实例
ZoneAvoidanceRule 默认规则,复合判断server所在区域的性能和server的可用性选择服务器
```

**替换负载均衡模式**

```
修改cloud-consumer-order80
新建com.atguigu.myrule
```

引入依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
</dependency>
  <dependency>
            <groupId>com.netflix.ribbon</groupId>
            <artifactId>ribbon-loadbalancer</artifactId>
            <version>2.3.0</version>
            <scope>compile</scope>
        </dependency>
```

创建配置类

```java
/**
 * @Classname RibbonConfig
 * @Description TODO
 * @Date 2021/12/13 16:45
 * @Created by 28327
 */

package com.wcl.ribbonconfig;

import com.netflix.loadbalancer.IRule;
import com.netflix.loadbalancer.RandomRule;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class RibbonConfig {
    @Bean
    public IRule myRule(){
        return new RandomRule();
    }
}
```

在主启动类上添加

```java
@RibbonClient(name = "CLOUD-PAYMENT-SERVICE",configuration=MySelfRule.class)
```

### OpenFeign

Feign是一个声明式的Web服务客户端，让编写Web服务客户端变得非常容易，只需创建一个接口并在接口上添加注解即可

 **Feign能干什么**

```
Feign旨在使编写Java Http客户端变得更容易。

前面在使用Ribbon+RestTemplate时，利用RestTemplate对http请求的封装处理，形成了一套模版化的调用方法。但是在实际开发中，由于对服务依赖的调用可能不止一处，往往一个接口会被多处调用，所以通常都会针对每个微服务自行封装一些客户端类来包装这些依赖服务的调用。所以，Feign在此基础上做了进一步封装，由他来帮助我们定义和实现依赖服务接口的定义。在Feign的实现下，我们只需创建一个接口并使用注解的方式来配置它(以前是Dao接口上面标注Mapper注解,现在是一个微服务接口上面标注一个Feign注解即可)，即可完成对服务提供方的接口绑定，简化了使用Spring cloud Ribbon时，自动封装服务调用客户端的开发量。
```

**Feign集成了Ribbon**

```
利用Ribbon维护了Payment的服务列表信息，并且通过轮询实现了客户端的负载均衡。而与Ribbon不同的是，通过feign只需要定义服务绑定接口且以声明式的方法，优雅而简单的实现了服务调用
```

-7. Consul服务注册与发现-8.Ribbon负载均衡服务调用1.ApplicationContextBean去掉注解@LoadBalanced2. LoadBalancer接口3. MyLB4. OrderController-5. 测试-9.OpenFeign服务接口调用-17. SpringCloud Alibaba入门简介-18. SpringCloud AlibabaNacos服务注册和配置中心-1. Linux服务器上mysql数据库配置-2. application.properties 配置-3.Linux服务器上nacos的集群配置cluster.conf-4.编辑Nacos的启动脚本startup.sh，使它能够接受不同的启动端口-5. Nginx的配置，由它作为负载均衡器-6.截止到此处，1个Nginx+3个nacos注册中心+1个mysqlSpringCloud第二季2020.3V1.6讲师:尚硅谷周阳-服务提供者YML主启动类业务类Controller-验证测试http://localhost:8006/payment/consul-服务消费者-新建Module消费服务order80cloud-consumerconsul-order80POMYML主启动类配置BeanController验证测试-访问测试地址http://localhost/consumer/payment/consul-三个注册中心异同点-CAPC:Consistency（强一致性）A:Availability（可用性）P:Partition tolerance（分区容错性）CAP理论关注粒度是数据，而不是整体系统设计的策略-经典CAP图AP(Eureka)CP(Zookeeper/Consul)-概述是什么-官网资料https://github.com/Netflix/ribbon/wiki/Getting-Started-Ribbon目前也进入维护模式未来替换方案-能干吗-LB（负载均衡）集中式LB进程内LB前面我们讲解过了80通过轮询负载访问8001/8002-一句话负载均衡+RestTemplate调用-Ribbon负载均衡演示-架构说明总结：Ribbon其实就是一个软负载均衡的客户端组件，他可以和其他所需请求的客户端结合使用，和eureka结合只是其中的一个实例。POM-二说RestTemplate的使用官网getForObject方法/getForEntity方法postForObject/postForEntityGET请求方法POST请求方法-Ribbon核心组件IRule-IRule：根据特定算法中从服务列表中选取一个要访问的服务-com.netflix.loadbalancer.RoundRobinRule轮询-com.netflix.loadbalancer.RandomRule随机-com.netflix.loadbalancer.RetryRule先按照RoundRobinRule的策略获取服务，如果获取服务失败则在指定时间内会进行重试，获取可用的服务-WeightedResponseTimeRule对RoundRobinRule的扩展，响应速度越快的实例选择权重越大，越容易被选择-BestAvailableRule会先过滤掉由于多次访问故障而处于断路器跳闸状态的服务，然后选择一个并发量最小的服务-AvailabilityFilteringRule先过滤掉故障实例，再选择并发较小的实例-ZoneAvoidanceRule默认规则,复合判断server所在区域的性能和server的可用性选择服务器-如何替换修改cloud-consumer-order80注意配置细节-新建packagecom.atguigu.myrule上面包下新建MySelfRule规则类主启动类添加@RibbonClient-测试http://localhost/consumer/payment/get/31-Ribbon负载均衡算法原理RoundRobinRule源码-手写-自己试着写一个本地负载均衡器试试7001/7002集群启动-8001/8002微服务改造controller-80订单微服务改造http://localhost/consumer/payment/lb-概述-OpenFeign是什么Feign是一个声明式的Web服务客户端，让编写Web服务客户端变得非常容易，只需创建一个接口并在接口上添加注解即可-GitHubhttps://github.com/spring-cloud/spring-cloud-openfeign能干嘛Feign和OpenFeign两者区别-OpenFeign使用步骤-接口+注解微服务调用接口+@FeignClient-新建cloud-consumer-feign-order80-Feign在消费端使用-POMYML-主启动@EnableFeignClients-业务类业务逻辑接口+@FeignClient配置调用provider服务-新建PaymentFeignService接口并新增注解@FeignClient@FeignClient控制层Controller-测试先启动2个eureka集群7001/7002再启动2个微服务8001/8002启动OpenFeign启动http://localhost/consumer/payment/get/31Feign自带负载均衡配置项小总结-OpenFeign超时控制-超时设置，故意设置超时演示出错情况服务提供方8001故意写暂停程序服务消费方80添加超时方法PaymentFeignService服务消费方80添加超时方法OrderFeignController-测试http://localhost/consumer/payment/feign/timeout错误页面OpenFeign默认等待1秒钟，超过后报错-是什么OpenFeign默认支持RibbonYML文件里需要开启OpenFeign客户端超时控制-OpenFeign日志打印功能日志打印功能是什么日志级别配置日志beanYML文件里需要开启日志的Feign客户端后台日志查看-概述分布式系统面临的问题是什么-能干嘛服务降级服务熔断接近实时的监控。。。。。。-官网资料https://github.com/Netflix/Hystrix/wiki/How-To-Use被动修复bugs-why会出现SpringCloud alibaba-Spring Cloud Netflix项目进入维护模式-https://spring.io/blog/2018/12/12/spring-cloud-greenwich-rc1-available-now-Spring Cloud Netflix Projects Entering Maintenance Mode什么是维护模式进入维护模式意味着什么呢？-SpringCloud alibaba带来了什么是什么能干嘛-去哪下https://github.com/alibaba/spring-cloud-alibaba/blob/master/README-zh.md怎么玩-官网https://spring.io/projects/spring-cloud-alibaba#overview-Nacos作为服务注册中心演示-为了下一章节演示nacos的负载均衡，参照9001新建90029002其它步骤你懂的-基于Nacos的服务消费者-新建Modulecloudalibaba-consumer-nacos-order83-POM为什么nacos支持负载均衡YML主启动-业务类ApplicationContextBeanOrderNacosController-测试nacos控制台-http://localhost:83/consumer/payment/nacos/13-服务注册中心对比-各种注册中心对比Nacos全景图所示Nacos和CAP-切换Nacos 支持AP和CP模式的切换-Nacos作为服务配置中心演示-Nacos作为配置中心-基础配置cloudalibaba-config-nacos-client3377POM-YMLwhy配置两个-YMLbootstrapapplication主启动-业务类ConfigClientController-@RefreshScope-在Nacos中添加配置信息-Nacos中的匹配规则-理论官网-实操-测试启动前需要在nacos客户端-配置管理-配置管理栏目下有对应的yaml配置文件运行cloud-config-nacos-client3377的主启动类-调用接口查看配置信息http://localhost:3377/config/info-自带动态刷新修改下Nacos中的yaml配置文件，再次调用查看配置的接口，就会发现配置已经刷新-Nacos作为配置中心-分类配置-问题多环境多项目管理-Nacos的图形化管理界面配置管理命名空间Namespace+Group+Data ID三者关系？为什么这么设计？-Case-三种方案加载配置-DataID方案-测试-Group方案-通过Group实现环境区分-bootstrap+application-Namespace方案按照域名配置填写-YML-Nacos集群和持久化配置（重要）-官网说明https://nacos.io/zh-cn/docs/cluster-mode-quick-start.html官网架构图(写的o(╥﹏╥)o)上图官网翻译，真实情况-说明按照上述，我们需要mysql数据库-官网说明https://nacos.io/zh-cn/docs/deployment.html重点说明-Nacos持久化配置解释-Nacos默认自带的是嵌入式数据库derbyhttps://github.com/alibaba/nacos/blob/develop/config/pom.xml-derby到mysql切换配置步骤-nacos-server-1.1.4\nacos\conf目录下找到sql脚本nacos-server-1.1.4\nacos\conf目录下找到application.properties启动Nacos，可以看到是个全新的空记录界面，以前是记录进derby-Linux版Nacos+MySQL生产环境配置预计需要，1个Nginx+3个nacos注册中心+1个mysql-Nacos下载Linux版https://github.com/alibaba/nacos/releases/tag/1.1.4nacos-server-1.1.4.tar.gz解压后安装-集群配置步骤(重点)-测试-微服务cloudalibaba-provider-payment9002启动注册进nacos集群高可用小总结开启/使用-Hystrix官宣，停更进维-https://github.com/Netflix/Hystrix不再接受合并请求https://github.com/alibaba/spring-cloud-alibaba不再发布新版本-Hystrix重要概念-服务降级服务器忙，请稍后再试，不让客户端等待并立刻返回一个友好提示，fallback-哪些情况会出发降级程序运行异常超时服务熔断触发服务降级线程池/信号量打满也会导致服务降级-服务熔断类比保险丝达到最大服务访问后，直接拒绝访问，拉闸限电，然后调用服务降级的方法并返回友好提示-就是保险丝服务的降级->进而熔断->恢复调用链路-服务限流秒杀高并发等操作，严禁一窝蜂的过来拥挤，大家排队，一秒钟N个，有序进行-构建新建cloud-provider-hystrix-payment8001POMYML主启动-业务类servicecontroller-正常测试启动eureka7001启动cloud-provider-hystrix-payment8001-访问-success的方法http://localhost:8001/payment/hystrix/ok/31-每次调用耗费5秒钟http://localhost:8001/payment/hystrix/timeout/31-上述module均OK以上述为根基平台，从正确->错误->降级熔断->恢复-高并发测试上述在非高并发情形下，还能勉强满足  but......-Jmeter压测测试开启Jmeter，来20000个并发压死8001，20000个请求都去访问paymentInfo_TimeOut服务-再来一个访问http://localhost:8001/payment/hystrix/ok/31-看演示结果两个都在自己转圈圈为什么会被卡死-Jmeter压测结论上面还是服务提供者8001自己测试，假如此时外部的消费者80也来访问，那消费者只能干等，最终导致消费端80不满意，服务端8001直接被拖死-看热闹不嫌弃事大，80新建加入-cloud-consumer-feign-hystrix-order80-新建cloud-consumer-feign-hystrix-order80POMYML主启动-业务类PaymentHystrixServiceOrderHystirxController-正常测试http://localhost/consumer/payment/hystrix/ok/31-高并发测试2W个线程压8001消费端80微服务再去访问正常的Ok微服务8001地址http://localhost/consumer/payment/hystrix/ok/32-是什么-一句话屏蔽底层消息中间件的差异,降低切换成本，统一消息的编程模型-官网https://spring.io/projects/spring-cloud-stream#overviewhttps://cloud.spring.io/spring-cloud-static/spring-cloud-stream/3.0.1.RELEASE/reference/html/-Spring Cloud Stream中文指导手册https://m.wang1314.com/doc/webapp/topic/20971999.html-标准MQ-生产者/消费者之间靠消息媒介传递信息内容Message-消息必须走特定的通道消息通道MessageChannel-消息通道里的消息如何被消费呢，谁负责收发处理消息通道MessageChannel的子接口SubscribableChannel，由MessageHandler消息处理器所订阅stream凭什么可以统一底层差异？-16. SpringCloud Sleuth分布式请求链路跟踪-概述-为什么会出现这个技术？需要解决哪些问题？问题-是什么https://github.com/spring-cloud/spring-cloud-sleuthSpring Cloud Sleuth提供了一套完整的服务跟踪的解决方案在分布式系统中提供追踪解决方案并且兼容支持了zipkin解决-搭建链路监控步骤-1. zipkin-下载SpringCloud从F版起已不需要自己构建Zipkin Server了，只需调用jar包即可https://dl.bintray.com/openzipkin/maven/io/zipkin/java/zipkin-server/zipkin-server-2.12.9-exec.jar运行jar-运行控制台http://localhost:9411/zipkin/-术语完整的调用链路上图what-名词解释Trace:类似于树结构的Span集合，表示一条调用链路，存在唯一标识span:表示调用链路来源，通俗的理解span就是一次请求信息-2. 服务提供者cloud-provider-payment8001POMYML业务类PaymentController-3. 服务消费者(调用方)cloud-consumer-order80POMYML业务类OrderController-4. 依次启动eureka7001/8001/8080调用8001几次测试下-5. 打开浏览器访问：http://localhost:9411-会出现以下界面查看查看依赖关系原理-SpringCloud alibaba学习资料获取-英文https://spring-cloud-alibaba-group.github.io/github-pages/greenwich/spring-cloud-alibaba.html-中文https://github.com/alibaba/spring-cloud-alibaba/blob/master/README-zh.md要么转圈圈等待-消费者80，o(╥﹏╥)o-消息驱动概述-设计思想INPUT对应于消费者配置新增

**Feign和OpenFeign的区别**

| Feign                                                        | OpenFeign                                                    |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Feign是Spring Cloud组件中的一个轻量级RESTful的HTTP服务客户端Feign内置了Ribbon，用来做客户端负载均衡，去调用服务注册中心的服务。Feign的使用方式是：使用Feign的注解定义接口，调用这个接口，就可以调用服务注册中心的服务 | OpenFeign是Spring Cloud 在Feign的基础上支持了SpringMVC的注解，如@RequesMapping等等。OpenFeign的@FeignClient可以解析SpringMVC的@RequestMapping注解下的接口，并通过动态代理的方式产生实现类，实现类中做负载均衡并调用其他服务。 |
| <**dependency**>   <**groupId**>org.springframework.cloud</**groupId**>   <**artifactId**>spring-cloud-starter-feign</**artifactId**> </**dependency**> | <**dependency**>   <**groupId**>org.springframework.cloud</**groupId**>   <**artifactId**>spring-cloud-starter-openfeign</**artifactId**> </**dependency**> |

####  OpenFeign的使用步骤

 **新建cloud-consumer-feign-order80**

```
Feign在消费端使用 Feign自带负载均衡配置项
```

 **POM**

```xml
<dependencies>
        <!--openfeign-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <!--eureka client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
       <dependency>
            <groupId>com.wcl</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <!--web-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--一般基础通用配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

**yml**

```yml
server:
  port: 80

eureka:
  client:
    register-with-eureka: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/
```

**主启动**

```java
/**
 * @Classname OrderMain8080
 * @Description TODO
 * @Date 2021/12/18 16:00
 * @Created by 28327
 */

package com.wcl.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.openfeign.EnableFeignClients;

@SpringBootApplication
@EnableFeignClients
public class OrderMain8080 {
    public static void main(String[] args) {
        SpringApplication.run(OrderMain8080.class, args);
    }
```

**业务逻辑接口(service)**

```java
package com.wcl.springcloud.service;

import com.wcl.springcloud.entities.CommonResult;
import com.wcl.springcloud.entities.Payment;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.stereotype.Component;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

@Component
@FeignClient(value = "CLOUD-PAYMENT-SERVICE")
public interface PaymentIFeignService {
    @GetMapping(value = "/payment/get/{id}")
    CommonResult<Payment> getPaymentById(@PathVariable("id") Long id);
}
```

**业务逻辑类(controller)**

```java
@RestController
public class PaymentFeignController {
    @Autowired
    private PaymentIFeignService paymentIFeignService;

    @GetMapping(value = "consumer/payment/get/{id}")
    public CommonResult<Payment> getPaymentById(@PathVariable("id") Long id)
    {
        return paymentIFeignService.getPaymentById(id);
    }
}
```

#### OpenFeign超时控制

```
OpenFeign默认等待1秒钟，超过后报错 
```

```yaml
server:
  port: 8080

eureka:
  client:
    register-with-eureka: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/

ribbon:
  #指的是建立连接后从服务器读取到可用资源所用的时间
  ReadTimeout: 5000
  #指的是建立连接所用的时间，适用于网络状况正常的情况下,两端连接所用的时间
  ConnectTimeout: 5000
```

#### OpenFeign日志打印功能

```
Feign 提供了日志打印功能，我们可以通过配置来调整日志级别，从而了解 Feign 中 Http 请求的细节。
说白了就是对Feign接口的调用情况进行监控和输出
```

**日志级别**

```
NONE：默认的，不显示任何日志；
BASIC：仅记录请求方法、URL、响应状态码及执行时间；
HEADERS：除了 BASIC 中定义的信息之外，还有请求和响应的头信息；
FULL：除了 HEADERS 中定义的信息之外，还有请求和响应的正文及元数据。
```

**配置日志bean**

```java
package com.wcl.springcloud.config;

import feign.Logger;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class ConfigBean {
    @Bean
    Logger.Level feignLoggerLevel() {
        return Logger.Level.FULL;
    }
}
```

**YML文件里需要开启日志的Feign客户端**

```yml
server:
  port: 8080

eureka:
  client:
    register-with-eureka: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/
#设置feign客户端超时时间(OpenFeign默认支持ribbon)
ribbon:
  #指的是建立连接后从服务器读取到可用资源所用的时间
  ReadTimeout: 5000
  #指的是建立连接所用的时间，适用于网络状况正常的情况下,两端连接所用的时间
  ConnectTimeout: 5000
logging:
  level:
    # feign日志以什么级别监控哪个接口
    com.wcl.springcloud.service.PaymentIFeignService: debug
```

## Hystrix服务降级和服务熔断和服务限流

### Hystrix断路器

```
Hystrix是一个用于处理分布式系统的延迟和容错的开源库，在分布式系统里，许多依赖不可避免的会调用失败，比如超时、异常等，Hystrix能够保证在一个依赖出问题的情况下，不会导致整体服务失败，避免级联故障，以提高分布式系统的弹性。
“断路器”本身是一种开关装置，当某个服务单元发生故障之后，通过断路器的故障监控（类似熔断保险丝），向调用方返回一个符合预期的、可处理的备选响应（FallBack），而不是长时间的等待或者抛出调用方无法处理的异常，这样就保证了服务调用方的线程不会被长时间、不必要地占用，从而避免了故障在分布式系统中的蔓延，乃至雪崩。
```

**hystrix案例**

### **新建cloud-provider-hystrix-payment8001**

**POM**

```xml
 <dependencies>
        <!--hystrix-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
            <version>2.2.10.RELEASE</version>
        </dependency>
        <!--eureka client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!--web-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency><!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
            <groupId>com.wcl</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

**yml**

```yml
server:
  port: 8001

spring:
  application:
    name: cloud-provider-hystrix-payment

eureka:
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      #defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka
      defaultZone: http://eureka7001.com:7001/eureka
```

**主启动**

```java
package com.wcl.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
public class HystrixPayment8001 {
    public static void main(String[] args) {
        SpringApplication.run(HystrixPayment8001.class,args);
    }
}
```

**service**

```java
/**
 * @Classname HystrixPaymentService
 * @Description TODO
 * @Date 2021/12/19 17:35
 * @Created by 28327
 */

package com.wcl.springcloud.service;

import org.springframework.stereotype.Service;

import java.util.concurrent.TimeUnit;

@Service
public class HystrixPaymentService {

    /**
     * 正常访问，一切OK
     *
     * @param id
     * @return
     */
    public String paymentInfo_OK(Integer id) {
        return "线程池:" + Thread.currentThread().getName() + "paymentInfo_OK,id: " + id + "\t" + "O(∩_∩)O";
    }

    /**
     * 超时访问，演示降级
     *
     * @param id
     * @return
     */
    public String paymentInfo_TimeOut(Integer id) {
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "线程池:" + Thread.currentThread().getName() + "paymentInfo_TimeOut,id: " + id + "\t" + "O(∩_∩)O，耗费3秒";
    }
}
```

**controller**

```java
/**
 * @Classname HystrixController
 * @Description TODO
 * @Date 2021/12/19 17:37
 * @Created by 28327
 */

package com.wcl.springcloud.controller;

import com.wcl.springcloud.service.HystrixPaymentService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@RestController
@Slf4j
public class HystrixController {
    @Autowired
    private HystrixPaymentService paymentService;

    @Value("${server.port}")
    private String serverPort;


    @GetMapping("/payment/hystrix/ok/{id}")
    public String paymentInfo_OK(@PathVariable("id") Integer id) {
        String result = paymentService.paymentInfo_OK(id);
        log.info("****result: " + result);
        return result;
    }

    @GetMapping("/payment/hystrix/timeout/{id}")
    public String paymentInfo_TimeOut(@PathVariable("id") Integer id) throws InterruptedException {
        String result = paymentService.paymentInfo_TimeOut(id);
        log.info("****result: " + result);
        return result;
    }
}
```

### 新建cloud-consumer-feign-hystrix-order80

**POM**

```xml
   <dependencies>
        <!--openfeign-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <!--hystrix-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
            <version>2.2.10.RELEASE</version>
        </dependency>
        <!--eureka client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
        <dependency>
            <groupId>com.wcl</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <!--web-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--一般基础通用配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

**yml**

```yml
server:
  port: 8080

eureka:
  client:
    register-with-eureka: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/
ribbon:
  ReadTimeout: 5000
  #指的是建立连接所用的时间，适用于网络状况正常的情况下,两端连接所用的时间
  ConnectTimeout: 5000
```

**主启动**

```java
package com.wcl.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.openfeign.EnableFeignClients;

@SpringBootApplication
@EnableFeignClients
public class FeginHystrixOrder80 {
    public static void main(String[] args) {
        SpringApplication.run(FeginHystrixOrder80.class, args);
    }
}
```

**service**

```java

@Component
//指定要使用的微服务
@FeignClient(value = "CLOUD-PROVIDER-HYSTRIX-PAYMENT")
public interface PaymentHystrixService {
    @GetMapping("/payment/hystrix/ok/{id}")
    String paymentInfo_OK(@PathVariable("id") Integer id);

    @GetMapping("/payment/hystrix/timeout/{id}")
    String paymentInfo_TimeOut(@PathVariable("id") Integer id);
}
```

**controller**

```java
/**
 * @Classname OrderHystirxController
 * @Description TODO
 * @Date 2021/12/19 17:54
 * @Created by 28327
 */

package com.wcl.springcloud.controller;

import com.wcl.springcloud.service.PaymentHystrixService;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
public class OrderHystirxController {
    @Resource
    private PaymentHystrixService paymentHystrixService;

    @GetMapping("/consumer/payment/hystrix/ok/{id}")
    public String paymentInfo_OK(@PathVariable("id") Integer id)
    {
        String result = paymentHystrixService.paymentInfo_OK(id);
        return result;
    }

    @GetMapping("/consumer/payment/hystrix/timeout/{id}")
    public String paymentInfo_TimeOut(@PathVariable("id") Integer id)
    {
        String result = paymentHystrixService.paymentInfo_TimeOut(id);
        return result;
    }
}
```

### 进行高并发压力测试分析

**故障现象和导致原因**

8001同一层次的其它接口服务被困死，因为tomcat线程池里面的工作线程已经被挤占完毕

80此时调用8001，客户端访问响应缓慢，转圈圈

正因为有上述故障或不佳表现，才有我们的降级/容错/限流等技术诞生

**解决的要求**

超时导致服务器变慢(转圈)--超时不再等待

出错(宕机或程序运行出错)--出错要有兜底

**解决**

 对方服务(8001)超时了，调用者(80)不能一直卡死等待，必须有服务降级

对方服务(8001)down机了，调用者(80)不能一直卡死等待，必须有服务降级

对方服务(8001)OK，调用者(80)自己出故障或有自我要求（自己的等待时间小于服务提供者），自己处理降级

### 服务降级

**8001先从自身找问题**

```
设置自身调用超时时间的峰值，峰值内可以正常运行，
超过了需要有兜底的方法处理，作服务降级fallback
```

#### **8001fallback**

**service**

```
/**
 * @Classname HystrixPaymentService
 * @Description TODO
 * @Date 2021/12/19 17:35
 * @Created by 28327
 */

package com.wcl.springcloud.service;

import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixProperty;
import org.springframework.stereotype.Service;

import java.util.concurrent.TimeUnit;

@Service
public class HystrixPaymentService {

    /**
     * 正常访问，一切OK
     *
     * @param id
     * @return
     */
    public String paymentInfo_OK(Integer id) {
        return "线程池:" + Thread.currentThread().getName() + "paymentInfo_OK,id: " + id + "\t" + "O(∩_∩)O";
    }

    /**
     * 超时访问，演示降级
     *
     * @param id
     * @return
     */
    @HystrixCommand(fallbackMethod = "paymentInfo_TimeOutHandler", commandProperties = {
            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "3000")
    })
    public String paymentInfo_TimeOut(Integer id) {
        try {
            TimeUnit.SECONDS.sleep(4);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "线程池:" + Thread.currentThread().getName() + "paymentInfo_TimeOut,id: " + id + "\t" + "O(∩_∩)O，耗费3秒";
    }

    public String paymentInfo_TimeOutHandler(Integer id) {
        return "/(ㄒoㄒ)/调用支付接口超时或异常：\t" + "\t当前线程池名字" + Thread.currentThread().getName();
    }
}
```

**主启动类激活**

```java
/**
 * @Classname HystrixPayment8001
 * @Description TODO
 * @Date 2021/12/19 17:33
 * @Created by 28327
 */

package com.wcl.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.circuitbreaker.EnableCircuitBreaker;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
import org.springframework.cloud.netflix.hystrix.EnableHystrix;

@SpringBootApplication
@EnableEurekaClient
//已经弃用，现在使用
//@EnableCircuitBreaker
@EnableHystrix
public class HystrixPayment8001 {
    public static void main(String[] args) {
        SpringApplication.run(HystrixPayment8001.class, args);
    }
}
```

#### **80fallback**

**yml**

```yml
server:
  port: 8080

eureka:
  client:
    register-with-eureka: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/
ribbon:
  ReadTimeout: 5000
  #指的是建立连接所用的时间，适用于网络状况正常的情况下,两端连接所用的时间
  ConnectTimeout: 5000

feign:
  hystrix:
    enabled: true
```

**主启动**

```java
/**
 * @Classname FeginHystrixOrder80
 * @Description TODO
 * @Date 2021/12/19 17:46
 * @Created by 28327
 */

package com.wcl.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.hystrix.EnableHystrix;
import org.springframework.cloud.openfeign.EnableFeignClients;

@SpringBootApplication
@EnableFeignClients
@EnableHystrix
public class FeginHystrixOrder80 {
    public static void main(String[] args) {
        SpringApplication.run(FeginHystrixOrder80.class, args);
    }
}
```

**controller**

```java
/**
 * @Classname OrderHystirxController
 * @Description TODO
 * @Date 2021/12/19 17:54
 * @Created by 28327
 */

package com.wcl.springcloud.controller;

import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixProperty;
import com.wcl.springcloud.service.PaymentHystrixService;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
public class OrderHystirxController {
    @Resource
    private PaymentHystrixService paymentHystrixService;

    @GetMapping("/consumer/payment/hystrix/ok/{id}")
    public String paymentInfo_OK(@PathVariable("id") Integer id) {
        String result = paymentHystrixService.paymentInfo_OK(id);
        return result;
    }

    @HystrixCommand(fallbackMethod = "paymentTimeOutFallbackMethod", commandProperties = {
            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "1500")
    })
    @GetMapping("/consumer/payment/hystrix/timeout/{id}")
    public String paymentInfo_TimeOut(@PathVariable("id") Integer id) {
        String result = paymentHystrixService.paymentInfo_TimeOut(id);
        return result;
    }

    public String paymentTimeOutFallbackMethod(@PathVariable("id") Integer id) {
        return "我是消费者80,对方支付系统繁忙请10秒钟后再试或者自己运行出错请检查自己,o(╥﹏╥)o";
    }
}
```

#### 目前问题

```
每个业务方法对应一个兜底的方法，代码膨胀，
统一和自定义的分开，代码解耦
```

#### **解决问题**

**对80服务进行代码膨胀**

**controller**

```java
/**
 * @Classname OrderHystirxController
 * @Description TODO
 * @Date 2021/12/19 17:54
 * @Created by 28327
 */

package com.wcl.springcloud.controller;

import com.netflix.hystrix.contrib.javanica.annotation.DefaultProperties;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixCommand;
import com.netflix.hystrix.contrib.javanica.annotation.HystrixProperty;
import com.wcl.springcloud.service.PaymentHystrixService;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
//进行全局配置
@DefaultProperties(defaultFallback = "payment_Global_FallbackMethod")
public class OrderHystirxController {
    @Resource
    private PaymentHystrixService paymentHystrixService;
    //默认配置为全局配置
    @HystrixCommand
    @GetMapping("/consumer/payment/hystrix/ok/{id}")
    public String paymentInfo_OK(@PathVariable("id") Integer id) {
        String result = paymentHystrixService.paymentInfo_OK(id);
        return result;
    }

    @HystrixCommand(fallbackMethod = "paymentTimeOutFallbackMethod", commandProperties = {
            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "1500")
    })
    @GetMapping("/consumer/payment/hystrix/timeout/{id}")
    public String paymentInfo_TimeOut(@PathVariable("id") Integer id) {
        String result = paymentHystrixService.paymentInfo_TimeOut(id);
        return result;
    }

    public String paymentTimeOutFallbackMethod(@PathVariable("id") Integer id) {
        return "我是消费者80,对方支付系统繁忙请10秒钟后再试或者自己运行出错请检查自己,o(╥﹏╥)o";
    }
    public String payment_Global_FallbackMethod()
    {
        return "Global异常处理信息，请稍后再试，/(ㄒoㄒ)/~~";
    }
}
```

**对80服务进行代码解耦**

```
服务降级，客户端去调用服务端，碰上服务端宕机或关闭

本次案例服务降级处理是在客户端80实现完成的，与服务端8001没有关系
只需要为Feign客户端定义的接口添加一个服务降级处理的实现类即可实现解耦

未来我们要面对的异常：运行，超时，宕机

业务类逻辑和代码异常逻辑混在了一起
```

**PaymentFallbackService类实现PaymentFeignClientService接口**

```java
/**
 * @Classname PaymentFallbackService
 * @Description TODO
 * @Date 2021/12/19 18:43
 * @Created by 28327
 */

package com.wcl.springcloud.service;

import org.springframework.stereotype.Component;

@Component
public class PaymentFallbackService implements PaymentHystrixService {
    @Override
    public String paymentInfo_OK(Integer id) {
        return "服务调用失败，提示来自：cloud-consumer-feign-order80";
    }

    @Override
    public String paymentInfo_TimeOut(Integer id) {
        return "服务调用失败，提示来自：cloud-consumer-feign-order80";
    }
}
```

**yml**

```yml
server:
  port: 8080

eureka:
  client:
    register-with-eureka: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/
ribbon:
  ReadTimeout: 5000
  #指的是建立连接所用的时间，适用于网络状况正常的情况下,两端连接所用的时间
  ConnectTimeout: 5000

feign:
  hystrix:
    enabled: true
```

**service接口**

```java
@Component
//指定要使用的微服务,指定实现类回调类
@FeignClient(value = "CLOUD-PROVIDER-HYSTRIX-PAYMENT", fallback = PaymentFallbackService.class)
public interface PaymentHystrixService {
    @GetMapping("/payment/hystrix/ok/{id}")
    String paymentInfo_OK(@PathVariable("id") Integer id);

    @GetMapping("/payment/hystrix/timeout/{id}")
    String paymentInfo_TimeOut(@PathVariable("id") Integer id);
}
```

### 服务熔断

```
熔断机制概述
	熔断机制是应对雪崩效应的一种微服务链路保护机制。当扇出链路的某个微服务出错不可用或者响应时间太长时，会进行服务的降级，进而熔断该节点微服务的调用，快速返回错误的响应信息。当检测到该节点微服务调用响应正常后，恢复调用链路。
	在Spring Cloud框架里，熔断机制通过Hystrix实现。Hystrix会监控微服务间调用的状况，当失败的调用到一定阈值，缺省是5秒内20次调用失败，就会启动熔断机制。熔断机制的注解是@HystrixCommand。
```

#### 案例

**修改cloud-provider-hystrix-payment8001**

**PaymentService**

```java
 //服务熔断示例
    @HystrixCommand(fallbackMethod = "paymentCircuitBreaker_fallback", commandProperties = {
        	//开启断路器
            @HystrixProperty(name = "circuitBreaker.enabled", value = "true"),
        	//请求次数
            @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold", value = "10"),
        	//请求时间范围
            @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds", value = "10000"),
        	//请求失败率
            @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage", value = "60"),
    })
    public String paymentCircuitBreaker(@PathVariable("id") Integer id) {
        if (id < 0) {
            throw new RuntimeException("******id 不能负数");
        }
        String serialNumber = IdUtil.simpleUUID();

        return Thread.currentThread().getName() + "\t" + "调用成功，流水号: " + serialNumber;
    }

    public String paymentCircuitBreaker_fallback(@PathVariable("id") Integer id) {
        return "id 不能负数，请稍后再试，/(ㄒoㄒ)/~~   id: " + id;
    }
```

**PaymentController**

```java
 //服务熔断示例
    @GetMapping("/payment/circuit/{id}")
    public String paymentCircuitBreaker(@PathVariable("id") Integer id)
    {
        String result = paymentService.paymentCircuitBreaker(id);
        log.info("****result: "+result);
        return result;
    }
```

#### 熔断类型

```
熔断打开--请求不再进行调用当前服务，内部设置时钟一般为MTTR（平均故障处理时间)，当打开时长达到所设时钟则进入半熔断状态
熔断关闭--熔断关闭不会对服务进行熔断
熔断半开--部分请求根据规则调用当前服务，如果请求成功且符合规则则认为当前服务恢复正常，关闭熔断
```

#### **断路器起作用的时点**

```
涉及到断路器的三个重要参数：快照时间窗、请求总数阀值、错误百分比阀值。
1：快照时间窗：断路器确定是否打开需要统计一些请求和错误数据，而统计的时间范围就是快照时间窗，默认为最近的10秒。
2：请求总数阀值：在快照时间窗内，必须满足请求总数阀值才有资格熔断。默认为20，意味着在10秒内，如果该hystrix命令的调用次数不足20次，即使所有的请求都超时或其他原因失败，断路器都不会打开。
3：错误百分比阀值：当请求总数在快照时间窗内超过了阀值，比如发生了30次调用，如果在这30次调用中，有15次发生了超时异常，也就是超过50%的错误百分比，在默认设定50%阀值情况下，这时候就会将断路器打开。
```

#### **断路器开启或者关闭的条件**

```
当满足一定的阀值的时候（默认10秒内超过20个请求次数）
当失败率达到一定的时候（默认10秒内超过50%的请求失败）
到达以上阀值，断路器将会开启
当开启的时候，所有请求都不会进行转发
一段时间之后（默认是5秒），这个时候断路器是半开状态，会让其中一个请求进行转发。
如果成功，断路器会关闭，若失败，继续开启。重复4和5
```

**断路器开启之后**

```
1：再有请求调用的时候，将不会调用主逻辑，而是直接调用降级fallback。通过断路器，实现了自动地发现错误并将降级逻辑切换为主逻辑，减少响应延迟的效果。
2：原来的主逻辑要如何恢复呢？
对于这一问题，hystrix也为我们实现了自动恢复功能。
当断路器打开，对主逻辑进行熔断之后，hystrix会启动一个休眠时间窗，在这个时间窗内，降级逻辑是临时的成为主逻辑，
当休眠时间窗到期，断路器将进入半开状态，释放一次请求到原来的主逻辑上，如果此次请求正常返回，那么断路器将继续闭合，
主逻辑恢复，如果这次请求依然有问题，断路器继续进入打开状态，休眠时间窗重新计时。
```

**配置**

```java
//========================All
@HystrixCommand(fallbackMethod = "str_fallbackMethod",
        groupKey = "strGroupCommand",
        commandKey = "strCommand",
        threadPoolKey = "strThreadPool",

        commandProperties = {
                // 设置隔离策略，THREAD 表示线程池 SEMAPHORE：信号池隔离
                @HystrixProperty(name = "execution.isolation.strategy", value = "THREAD"),
                // 当隔离策略选择信号池隔离的时候，用来设置信号池的大小（最大并发数）
                @HystrixProperty(name = "execution.isolation.semaphore.maxConcurrentRequests", value = "10"),
                // 配置命令执行的超时时间
                @HystrixProperty(name = "execution.isolation.thread.timeoutinMilliseconds", value = "10"),
                // 是否启用超时时间
                @HystrixProperty(name = "execution.timeout.enabled", value = "true"),
                // 执行超时的时候是否中断
                @HystrixProperty(name = "execution.isolation.thread.interruptOnTimeout", value = "true"),
                // 执行被取消的时候是否中断
                @HystrixProperty(name = "execution.isolation.thread.interruptOnCancel", value = "true"),
                // 允许回调方法执行的最大并发数
                @HystrixProperty(name = "fallback.isolation.semaphore.maxConcurrentRequests", value = "10"),
                // 服务降级是否启用，是否执行回调函数
                @HystrixProperty(name = "fallback.enabled", value = "true"),
                // 是否启用断路器
                @HystrixProperty(name = "circuitBreaker.enabled", value = "true"),
                // 该属性用来设置在滚动时间窗中，断路器熔断的最小请求数。例如，默认该值为 20 的时候，
                // 如果滚动时间窗（默认10秒）内仅收到了19个请求， 即使这19个请求都失败了，断路器也不会打开。
                @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold", value = "20"),
                // 该属性用来设置在滚动时间窗中，表示在滚动时间窗中，在请求数量超过
                // circuitBreaker.requestVolumeThreshold 的情况下，如果错误请求数的百分比超过50,
                // 就把断路器设置为 "打开" 状态，否则就设置为 "关闭" 状态。
                @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage", value = "50"),
                // 该属性用来设置当断路器打开之后的休眠时间窗。 休眠时间窗结束之后，
                // 会将断路器置为 "半开" 状态，尝试熔断的请求命令，如果依然失败就将断路器继续设置为 "打开" 状态，
                // 如果成功就设置为 "关闭" 状态。
                @HystrixProperty(name = "circuitBreaker.sleepWindowinMilliseconds", value = "5000"),
                // 断路器强制打开
                @HystrixProperty(name = "circuitBreaker.forceOpen", value = "false"),
                // 断路器强制关闭
                @HystrixProperty(name = "circuitBreaker.forceClosed", value = "false"),
                // 滚动时间窗设置，该时间用于断路器判断健康度时需要收集信息的持续时间
                @HystrixProperty(name = "metrics.rollingStats.timeinMilliseconds", value = "10000"),
                // 该属性用来设置滚动时间窗统计指标信息时划分"桶"的数量，断路器在收集指标信息的时候会根据
                // 设置的时间窗长度拆分成多个 "桶" 来累计各度量值，每个"桶"记录了一段时间内的采集指标。
                // 比如 10 秒内拆分成 10 个"桶"收集这样，所以 timeinMilliseconds 必须能被 numBuckets 整除。否则会抛异常
                @HystrixProperty(name = "metrics.rollingStats.numBuckets", value = "10"),
                // 该属性用来设置对命令执行的延迟是否使用百分位数来跟踪和计算。如果设置为 false, 那么所有的概要统计都将返回 -1。
                @HystrixProperty(name = "metrics.rollingPercentile.enabled", value = "false"),
                // 该属性用来设置百分位统计的滚动窗口的持续时间，单位为毫秒。
                @HystrixProperty(name = "metrics.rollingPercentile.timeInMilliseconds", value = "60000"),
                // 该属性用来设置百分位统计滚动窗口中使用 “ 桶 ”的数量。
                @HystrixProperty(name = "metrics.rollingPercentile.numBuckets", value = "60000"),
                // 该属性用来设置在执行过程中每个 “桶” 中保留的最大执行次数。如果在滚动时间窗内发生超过该设定值的执行次数，
                // 就从最初的位置开始重写。例如，将该值设置为100, 滚动窗口为10秒，若在10秒内一个 “桶 ”中发生了500次执行，
                // 那么该 “桶” 中只保留 最后的100次执行的统计。另外，增加该值的大小将会增加内存量的消耗，并增加排序百分位数所需的计算时间。
                @HystrixProperty(name = "metrics.rollingPercentile.bucketSize", value = "100"),
                // 该属性用来设置采集影响断路器状态的健康快照（请求的成功、 错误百分比）的间隔等待时间。
                @HystrixProperty(name = "metrics.healthSnapshot.intervalinMilliseconds", value = "500"),
                // 是否开启请求缓存
                @HystrixProperty(name = "requestCache.enabled", value = "true"),
                // HystrixCommand的执行和事件是否打印日志到 HystrixRequestLog 中
                @HystrixProperty(name = "requestLog.enabled", value = "true"),
        },
        threadPoolProperties = {
                // 该参数用来设置执行命令线程池的核心线程数，该值也就是命令执行的最大并发量
                @HystrixProperty(name = "coreSize", value = "10"),
                // 该参数用来设置线程池的最大队列大小。当设置为 -1 时，线程池将使用 SynchronousQueue 实现的队列，
                // 否则将使用 LinkedBlockingQueue 实现的队列。
                @HystrixProperty(name = "maxQueueSize", value = "-1"),
                // 该参数用来为队列设置拒绝阈值。 通过该参数， 即使队列没有达到最大值也能拒绝请求。
                // 该参数主要是对 LinkedBlockingQueue 队列的补充,因为 LinkedBlockingQueue
                // 队列不能动态修改它的对象大小，而通过该属性就可以调整拒绝请求的队列大小了。
                @HystrixProperty(name = "queueSizeRejectionThreshold", value = "5"),
        }
)
public String strConsumer() {
    return "hello 2020";
}
public String str_fallbackMethod()
{
    return "*****fall back str_fallbackMethod";
}
```

### 服务限流

```
spring cloud alibaba的Sentinel中讲解
```

### hystrix工作流程

```
https://github.com/Netflix/Hystrix/wiki/How-it-Works
```

### 服务监控hystrixDashboard

**新建cloud-consumer-hystrix-dashboard9001**

**pom**

```xml
<dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
            <version>2.2.10.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

**application.yml**

```yml
server:
  port: 9001
hystrix:
  dashboard:
    proxy-stream-allow-list: "*"
```

**主启动**

```java
@SpringBootApplication
//开启图形化监视
@EnableHystrixDashboard
public class HystrixDashboardMain9001 {
    public static void main(String[] args) {
        SpringApplication.run(HystrixDashboardMain9001.class, args);
    }
}
```

**provider需要配置**

```xml
   <!-- actuator监控信息完善 -->

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

**监视hytrix8001服务**

```java
访问http://localhost:9001/hystrix
填入监控地址 http://localhost:8001/hystrix.stream

若出现Unable to connect to Command Metric Stream
配置如下
/**
 *此配置是为了服务监控而配置，与服务容错本身无关，springcloud升级后的坑
 *ServletRegistrationBean因为springboot的默认路径不是"/hystrix.stream"，
 *只要在自己的项目里配置上下面的servlet就可以了
 写一个配置类
 */
@Bean
public ServletRegistrationBean getServlet() {
    HystrixMetricsStreamServlet streamServlet = new HystrixMetricsStreamServlet();
    ServletRegistrationBean registrationBean = new ServletRegistrationBean(streamServlet);
    registrationBean.setLoadOnStartup(1);
    registrationBean.addUrlMappings("/hystrix.stream");
    registrationBean.setName("HystrixMetricsStreamServlet");
    return registrationBean;
}
```

## 服务网关

### zuul路由网关（不推荐使用）

**概述简介**

```
官网 https://github.com/Netflix/zuul/wiki/Getting-Started
Zuul是一种提供动态路由、监视、弹性、安全性等功能的边缘服务。Zuul是Netflix出品的一个基于JVM路由和服务端的负载均衡器。
API网关为微服务架构中的服务提供了统一的访问入口，客户端通过API网关访问相关服务。API网关的定义类似于设计模式中的门面模式，它相当于整个微服务架构中的门面，所有客户端的访问都通过它来进行路由及过滤。它实现了请求路由、负载均衡、校验过滤、服务容错、服务聚合等功能。
```

#### 路由基本配置

**新建Module模块cloud-zuul-gateway9527**

**pom**

```xml
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

**yml**

```yml
server:
  port: 9527

spring:
  application:
    name: cloud-zuul-gateway

eureka:
  client:
    service-url:
      #defaultZone: #http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka,http://eureka7003.com:7003/eureka
      defaultZone: http://eureka7001.com:7001/eureka
  instance:
    instance-id: gateway-9527.com
    prefer-ip-address: true 
```

**hosts修改**

```
127.0.0.1  myzuul.com
```

**主启动类**

```java
@SpringBootApplication

@EnableZuulProxy

public class Zuul_9527_StartSpringCloudApp{
    public static void main(String[] args){
   SpringApplication.run(Zuul_9527_StartSpringCloudApp.class, args);

  }

}
```

#### 路由访问映射规则

**工程microservicecloud-zuul-gateway-9527**

##### 代理名称

**yml**

```yml
zuul:
  routes: # 路由映射配置
    mypayment.path: /weixin/**                 #IE地址栏输入的路径
    mypayment.serviceId: cloud-provider-payment   #注册进eureka服务器的地址
```

```url
before

http://myzuul.com:9527/cloud-provider-payment/paymentInfo
after

http://myzuul.com:9527/weixin/paymentInfo
```

**此时问题**

```
路由访问OK http://myzuul.com:9527/weixin/paymentInfo
原路径访问OK http://myzuul.com:9527/cloud-provider-payment/paymentInfo
```

##### 原有真实服务名忽略

**YML**

```yml
server:
  port: 9527

spring:
  application:
    name: cloud-zuul-gateway

eureka:
  client:
    service-url:
      #defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka,http://eureka7003.com:7003/eureka
      defaultZone: http://eureka7001.com:7001/eureka
  instance:
    instance-id: gateway-9527.com
    prefer-ip-address: true

zuul:
  ignored-services: cloud-provider-payment
  routes: # 路由映射配置
    mypayment.serviceId: cloud-provider-payment
    mypayment.path: /weixin/**
    mysms.serviceId: cloud-provider-sms
    mysms.path: /mysms/**
```

```yml
zuul: 

  ignored-services: "*"

  routes: 

    mydept.serviceId: microservicecloud-dept

    mydept.path: /mydept/**
```

#### 路由转发和负载均衡功能

##### 服务提供者SMS短信模块

**建模块cloud-provider-sms8008**

**POM**

```xml
<dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

**yml**

```yml
server:
  port: 8008

###服务名称(服务注册到eureka名称)
spring:
    application:
        name: cloud-provider-sms

eureka:
  client: #服务提供者provider注册进eureka服务列表内
    service-url:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka,http://eureka7003.com:7003/eureka
      #defaultZone: http://127.0.0.1:7001/eureka,http://127.0.0.1:7002/eureka
      #defaultZone: http://eureka7001.com:7001/eureka   # eureka集群加@老本版
```

**业务类**

```java
@RestController
public class SMSController
{
    @Value("${server.port}")
    private String serverPort;

    @GetMapping("/sms")
    public String sms()
    {
        return "sms provider service: "+"\t"+serverPort;
    }
}

```

**主启动**

```java
@SpringBootApplication
@EnableEurekaClient
public class MainAppSMS8008
{
    public static void main(String[] args)
    {
        SpringApplication.run(MainAppSMS8008.class,args);
    }
}
```

##### 修改我们的zuul服务9527

**修改YML，体现路由转发和负载均衡**

```yml
server:
  port: 9527

spring:
  application:
    name: cloud-zuul-gateway

eureka:
  client:
    service-url:
      #defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka,http://eureka7003.com:7003/eureka
      defaultZone: http://eureka7001.com:7001/eureka
  instance:
    instance-id: gateway-9527.com
    prefer-ip-address: true

zuul:
  #ignored-services: cloud-provider-payment
  routes: # 路由映射配置
    mypayment.serviceId: cloud-provider-payment
    mypayment.path: /weixin/**
    mysms.serviceId: cloud-provider-sms
    mysms.path: /mysms/**
```

```
由于Zuul自动集成了Ribbon和Hystrix，所以Zuul天生就有负载均衡和服务容错能力
http://myzuul.com:9527/weixin/paymentInfo 负载均衡
http://myzuul.com:9527/mysms/sms 路由转发
```

##### 设置统一公共前缀

**yml**

```yml
zuul: 

  prefix: /wcl

  ignored-services: "*"

  routes: 

    mydept.serviceId: microservicecloud-dept

    mydept.path: /mydept/**
```

```
http://myzuul.com:9527/wcl/weixin/paymentInfo
```

#### 查看路由信息

**pom**

```x'm'l
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

**yml**

```yml
# 开启查看路由的端点
management:
  endpoints:
    web:
      exposure:
        include: 'routes' 
```

**查看路由详细信息**

```
http://localhost:9527/actuator/routes
```

#### 过滤器

**功能**

```
过滤功能负责对请求过程进行额外的处理，是请求校验过滤及服务聚合的基础。
```

**过滤器的生命周期**

![graphic](data:application/octet-stream;base64,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BAKR/0HegEAgEAgE4n5B3oBAIBAIBOJ+yS1vmD9/fqlSpY4cOWKWEQgEAoFA5H9yyxsmT55cpkyZb7/91iz78Mcff6xevTo6Otosezz/+9//1q5dGxkZaZa9rFu3LiIi4q+//jLL3mst16xZ8+eff5pljycmJmbVqlW///67WfZ44uLiwsPDf/31V7Ps8SQkJCxfvvynn34yyx5PUlJSWFjYjRs3zLLHk5ycvHjx4mvXrplljyclJWXRokVXrlwxyx7P+vXrQYYuXbpklj2ejRs3zp0798cffzTLHs/mzZtnz559/vx5s+zxbN26debMmWfPnjXLHs8XX3wxffr006dPm2WP58svv5w6dWpmZqZZ9ni+/vprWIEnT540yx7Pzp07J02adPz4cbPs3XKfffbZ999/b5Y9nvT09E8//dR3ne/btw827dGjR82y9zmeY8eOPXz4sFn2eKB7zJgxBw8eNMseDww/btw432taYZrjx4/fu3evWfZ44HsnTJiwZ88es+zxHDt2bOLEib530Jw4ceLzzz/fsWOHWfY+fRyW9KuvvjLLHs+pU6emTZu2fft2s+x9AzusMVhvZtnjOXfuHKxVWLdm2eO5cOECrHnft61evHgRtg5sI7Ps8cC2W7hwYWpqqln2eGD7wlaGbW2WPR7IgaVLl7pcLrPs8UCeLFu2LDEx0Sx7PL/88suKFSvi4+PNssfz22+/rVy5MjY21iyjlEYpjVL6Fiil805K5x6PwBsgXXr27Dlw4ECz7PFAkvXp06d///5m2cv777/ft29fSFaz7PEMGDDg3Xff9c2/jz766O233/7555/NssczdOjQrl27Xr9+3Sx7PB9//HGnTp18c2v06NHt27f3zSRY/rZt2/rmDdQgrVq1girALHs8UF80b94cKg6z7PFAGjVt2vSHH34wyx4PJF/jxo2/+eYbs+zxQMo2aNDANwMWLFhQt25d3+29ZMmSWrVqQT6ZZY8HdqFq1apBFppljwf26kqVKvlWT7APly9f3vcNILBLlytXbtOmTWbZ44GK4Nlnn/WtemD/hO3iW9G43e6nnnrKt1qBiumZZ56Bfdsse/e9smXLQo1glj2eLVu2vPjii1BlmGXvnbcVK1aECsIsezxQvVapUgVqKLPszbYaNWpAvWaWvftMnTp1YOc3yx7PoUOH6tevP2/ePLPs3UMa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)

##### **ZuulFilter**

**过滤类型**

```
pre：在请求被路由到目标服务前执行，比如权限校验、打印日志等功能；
routing：在请求被路由到目标服务时执行
post：在请求被路由到目标服务后执行，比如给目标服务的响应添加头信息，收集统计数据等功能；
error：请求在其他阶段发生错误时执行。
```

**过滤顺序**

```
数字小的先执行
```

**过滤是否开启**

```
shouldFilter方法为true走
```

**执行逻辑**

```
自己的业务逻辑
```

**案例Case**

```java
前置过滤器，用于在请求路由到目标服务前打印请求日志

业务代码

@Component
@Slf4j
public class PreLogFilter extends ZuulFilter
{
    @Override
    public String filterType()
    {
        return "pre";
    }

    @Override
    public int filterOrder()
    {
        return 1;
    }

    @Override
    public boolean shouldFilter()
    {
        return true;
    }

    @Override
    public Object run() throws ZuulException
    {
        RequestContext requestContext = RequestContext.getCurrentContext();
        HttpServletRequest request = requestContext.getRequest();
        String host = request.getRemoteHost();
        String method = request.getMethod();
        String uri = request.getRequestURI();
        //log.info("=====> Remote host:{},method:{},uri:{}", host, method, uri);
        System.out.println("********"+new Date().getTime());
        return null;
    }
}


yml开关
zuul:
  PreLogFilter:
    pre:
      disable: true
```



### Gateway网关

```
官网 https://cloud.spring.io/spring-cloud-static/spring-cloud-gateway/2.2.1.RELEASE/reference/html/


Gateway是在Spring生态系统之上构建的API网关服务，基于Spring 5，Spring Boot 2和 Project Reactor等技术。
Gateway旨在提供一种简单而有效的方式来对API进行路由，以及提供一些强大的过滤器功能， 例如：熔断、限流、重试等
SpringCloud Gateway 是 Spring Cloud 的一个全新项目，基于 Spring 5.0+Spring Boot 2.0 和 Project Reactor 等技术开发的网关，它旨在为微服务架构提供一种简单有效的统一的 API 路由管理方式。
 
SpringCloud Gateway 作为 Spring Cloud 生态系统中的网关，目标是替代 Zuul，在Spring Cloud 2.0以上版本中，没有对新版本的Zuul 2.0以上最新高性能版本进行集成，仍然还是使用的Zuul 1.x非Reactor模式的老版本。而为了提升网关的性能，SpringCloud Gateway是基于WebFlux框架实现的，而WebFlux框架底层则使用了高性能的Reactor模式通信框架Netty。
 
Spring Cloud Gateway的目标提供统一的路由方式且基于 Filter 链的方式提供了网关基本的功能，例如：安全，监控/指标，和限流。

SpringCloud Gateway 使用的Webflux中的reactor-netty响应式编程组件，底层使用了Netty通讯框架。
```

#### 特性

```
基于Spring Framework 5, Project Reactor 和 Spring Boot 2.0 进行构建；

动态路由：能够匹配任何请求属性；

可以对路由指定 Predicate（断言）和 Filter（过滤器）；

集成Hystrix的断路器功能；

集成 Spring Cloud 服务发现功能；

易于编写的 Predicate（断言）和 Filter（过滤器）；

请求限流功能；

支持路径重写。
```

#### **Spring Cloud Gateway 与 Zuul的区别**

```
在SpringCloud Finchley 正式版之前，Spring Cloud 推荐的网关是 Netflix 提供的Zuul：
1、Zuul 1.x，是一个基于阻塞 I/ O 的 API Gateway

2、Zuul 1.x 基于Servlet 2. 5使用阻塞架构它不支持任何长连接(如 WebSocket) Zuul 的设计模式和Nginx较像，每次 I/ O 操作都是从工作线程中选择一个执行，请求线程被阻塞到工作线程完成，但是差别是Nginx 用C++ 实现，Zuul 用 Java 实现，而 JVM 本身会有第一次加载较慢的情况，使得Zuul 的性能相对较差。

3、Zuul 2.x理念更先进，想基于Netty非阻塞和支持长连接，但SpringCloud目前还没有整合。 Zuul 2.x的性能较 Zuul 1.x 有较大提升。在性能方面，根据官方提供的基准测试， Spring Cloud Gateway 的 RPS（每秒请求数）是Zuul 的 1. 6 倍。

4、Spring Cloud Gateway 建立 在 Spring Framework 5、 Project Reactor 和 Spring Boot 2 之上， 使用非阻塞 API。

5、Spring Cloud Gateway 还 支持 WebSocket， 并且与Spring紧密集成拥有更好的开发体验
```

#### 三大核心概念

**Route(路由)**

```
路由是构建网关的基本模块，它由ID，目标URI，一系列的断言和过滤器组成，如果断言为true则匹配该路由
```

**Predicate(断言）**

```
参考的是Java8的java.util.function.Predicate，开发人员可以匹配HTTP请求中的所有内容(例如请求头或请求参数)，如果请求与断言相匹配则进行路由
```

**Filter(过滤)**

```
指的是Spring框架中GatewayFilter的实例，使用过滤器，可以在请求被路由前或者之后对请求进行修改。
```

**总体**

```
web请求，通过一些匹配条件，定位到真正的服务节点。并在这个转发过程的前后，进行一些精细化控制。

predicate就是我们的匹配条件；

而filter，就可以理解为一个无所不能的拦截器。有了这两个元素，再加上目标uri，就可以实现一个具体的路由了
```

#### Gateway工作流程

```
官网总结：
    客户端向 Spring Cloud Gateway 发出请求。然后在 Gateway Handler Mapping 中找到与请求相匹配的路由，将其发送到 Gateway Web Handler。

    Handler 再通过指定的过滤器链来将请求发送到我们实际的服务执行业务逻辑，然后返回。

    过滤器之间用虚线分开是因为过滤器可能会在发送代理请求之前（“pre”）或之后（“post”）执行业务逻辑。

    Filter在“pre”类型的过滤器可以做参数校验、权限校验、流量监控、日志输出、协议转换等，

    在“post”类型的过滤器中可以做响应内容、响应头的修改，日志的输出，流量监控等有着非常重要的作用。
核心逻辑：
	路由转发+执行过滤器链
```

#### 入门配置

**新建cloud-gateway-gateway9527**

**pom**

```xml
 <!--gateway-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-gateway</artifactId>
            <version>2.2.10.RELEASE</version>
        </dependency>
        <!--eureka-client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
        <dependency>
            <groupId>com.wcl</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <!--一般基础配置类-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

**yml**

```yml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway

eureka:
  instance:
    hostname: cloud-gateway-service
  client: #服务提供者provider注册进eureka服务列表内
    service-url:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://eureka7001.com:7001/eureka
```

**主启动**

```java
@SpringBootApplication
@EnableEurekaClient
public class GateWayMain9527 {
    public static void main(String[] args) {
        SpringApplication.run(GateWayMain9527.class, args);
    }
}
```

**YML新增网关配置**

```yml
spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      routes:
        - id: payment_routh #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          uri: http://localhost:8001          #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**         # 断言，路径相匹配的进行路由，微服务controller访问路径

#        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
#          uri: http://localhost:8001          #匹配后提供服务的路由地址
#          predicates:
#            - Path=/payment/lb/**         # 断言，路径相匹配的进行路由
```

**访问**

```
http://localhost:9527/payment/get/19
```

**编码配置网关**

```java
@Configuration
public class GateWayConfig
{
    /**
     * 配置了一个id为route-name的路由规则，
     * 当访问地址 http://localhost:9527/guonei时会自动转发到地址：http://news.baidu.com/guonei
     * @param builder
     * @return
     */
    @Bean
    public RouteLocator customRouteLocator(RouteLocatorBuilder builder)
    {
        RouteLocatorBuilder.Builder routes = builder.routes();

        routes.route("path_route_atguigu", r -> r.path("/guonei").uri("http://news.baidu.com/guonei")).build();

        return routes.build();

    }
    @Bean
    public RouteLocator customRouteLocator2(RouteLocatorBuilder builder)
    {
        RouteLocatorBuilder.Builder routes = builder.routes();
        routes.route("path_route_atguigu2", r -> r.path("/guoji").uri("http://news.baidu.com/guoji")).build();
        return routes.build();
    }
}
```

#### 通过微服务名实现动态路由

```
默认情况下Gateway会根据注册中心注册的服务列表，
以注册中心上微服务名为路径创建动态路由进行转发，从而实现动态路由的功能
```

**POM**

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

**Yml**

需要注意的是uri的协议为lb，表示启用Gateway的负载均衡功能。

lb://serviceName是spring cloud gateway在微服务中自动为我们创建的负载均衡uri

```yml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true #开启从注册中心动态创建路由的功能，利用微服务名进行路由
      routes:
        - id: payment_routh #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**         # 断言，路径相匹配的进行路由

        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/get/lb      # 断言，路径相匹配的进行路由

eureka:
  instance:
    hostname: cloud-gateway-service
  client: #服务提供者provider注册进eureka服务列表内
    service-url:
      register-with-eureka: true
      fetch-registry: true
      defaultZone: http://eureka7001.com:7001/eureka
```

#### Predicate的使用

```
https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#gateway-request-predicates-factories
```

**After Route Predicate**

**Before Route Predicate**

**Between Route Predicate**

```yml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true #开启从注册中心动态创建路由的功能
      routes:
        - id: payment_routh #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**         # 断言，路径相匹配的进行路由

        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          # uri: http://localhost:8001          #匹配后提供服务的路由地址
          uri: lb://cloud-payment-service #匹配后提供服务的路由地址
          predicates:
       - Path=/payment/lb/**         # 断言，路径相匹配的进行路由
             # 断言，路径相匹配的进行路由
            #- After=2020-02-05T15:10:03.685+08:00[Asia/Shanghai]         # 断言，路径相匹配的进行路由
            #- Before=2020-02-05T15:10:03.685+08:00[Asia/Shanghai]         # 断言，路径相匹配的进行路由
            - Between=2020-02-02T17:45:06.206+08:00[Asia/Shanghai],2020-03-25T18:59:06.206+08:00[Asia/Shanghai]

       # 断言，路径相匹配的进行路由
```

#### Filter的使用

**常用的GatewayFilter**

```
https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#the-addrequestheader-gatewayfilter-factory
```

**自定义过滤器**

```java
/**
 * @Classname GatewayFilterConfig
 * @Description TODO
 * @Date 2021/12/23 21:53
 * @Created by 28327
 */

package com.wcl.springcloud.config;

import lombok.extern.slf4j.Slf4j;
import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.core.Ordered;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Component;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

@Component
@Slf4j
//实现这两个接口，将类注册成组件
public class GatewayFilterConfig implements GlobalFilter, Ordered {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        String username = exchange.getRequest().getQueryParams().getFirst("username");
        if (username == null) {
            log.info("****用户名为null，无法登录");
            exchange.getResponse().setStatusCode(HttpStatus.NOT_ACCEPTABLE);
            return exchange.getResponse().setComplete();
        }

        return chain.filter(exchange);
    }

    @Override
    //加载配置的顺序，从低到到高
    public int getOrder() {
        return 0;
    }
}
```

## SpringCloud Config服务配置

### 概述

**能干嘛**

```
集中管理配置文件
不同环境不同配置，动态化的配置更新，分环境部署比如dev/test/prod/beta/release
运行期间动态调整配置，不再需要在每个服务部署的机器上编写配置文件，服务会向配置中心统一拉取配置自己的信息
当配置发生变动时，服务不需要重启即可感知到配置的变化并应用新的配置
post、curl访问刷新均可......将配置信息以REST接口的形式暴露
由于SpringCloud Config默认使用Git来存储配置文件(也有其它方式,比如支持SVN和本地文件)，但最推荐的还是Git，而且使用的是http/https访问的形式 
```

**官网**

```
https://cloud.spring.io/spring-cloud-static/spring-cloud-config/2.2.1.RELEASE/reference/html/
```

### **Config服务端配置与测试**

**新建Module模块cloud-config-center-3344它即为Cloud的配置中心模块cloudConfig Center**

**pom**

```xml
   <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-server</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

**yml**

```yml
server:
  port: 3344

spring:
  application:
    name:  cloud-config-center #注册进Eureka服务器的微服务名
  cloud:
    config:
      server:
        git:
          uri: https://gitee.com/yueli14/springcloud-config.git #GitHub上面的git仓库名字
          ####搜索目录
          search-paths:
            - springcloud-config
          username: 2832705815@qq.com
          password: 123456789Wcl.
          default-label: master
#      ####读取分支
#      label: master

#服务注册到eureka地址
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka
```

**主启动**

```java
@SpringBootApplication

@EnableConfigServer
public class ConfigCenterMain3344 {
    public static void main(String[] args) {
        SpringApplication.run(ConfigCenterMain3344.class, args);
    }
}
```

**访问**

```
http://localhost:3344/master/config-dev.yml
http://localhost:3344/config/test/master
```

### Config客户端配置与测试

**新建cloud-config-client-3355**

**pom**

```xml
  <dependencies>
      <!--        引入bootstrap依赖-->
     <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-bootstrap</artifactId>
            <version>3.0.2</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

#### **bootstrap.yml**

```
applicaiton.yml是用户级的资源配置项
bootstrap.yml是系统级的，优先级更加高
Spring Cloud会创建一个“Bootstrap Context”，作为Spring应用的`Application Context`的父上下文。初始化的时候，`Bootstrap Context`负责从外部源加载配置属性并解析配置。这两个上下文共享一个从外部获取的`Environment`。
`Bootstrap`属性有高优先级，默认情况下，它们不会被本地配置覆盖。 `Bootstrap context`和`Application Context`有着不同的约定，所以新增了一个`bootstrap.yml`文件，保证`Bootstrap Context`和`Application Context`配置的分离。
要将Client模块下的application.yml文件改为bootstrap.yml,这是很关键的，
因为bootstrap.yml是比application.yml先加载的。bootstrap.yml优先级高于application.yml
```

```yml
server:
  port: 3355

spring:
  application:
    name: config-client
  cloud:
    #Config客户端配置
    config:
      label: master #分支名称
      name: config #配置文件名称
      profile: dev #读取后缀名称   上述3个综合：master分支上config-dev.yml的配置文件被读取http://config-3344.com:3344/master/config-dev.yml
      uri: http://localhost:3344 #配置中心地址k

#服务注册到eureka地址
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka
```

**主启动**

```java
@SpringBootApplication
@EnableEurekaClient
public class ConfigClientMain3355 {
    public static void main(String[] args) {
        SpringApplication.run(ConfigClientMain3355.class, args);
    }
}
```

### Config客户端之动态刷新

**问题**

```
Linux运维修改GitHub上的配置文件内容做调整
刷新3344，发现ConfigServer配置中心立刻响应
刷新3355，发现ConfigClient客户端没有任何响应
3355没有变化除非自己重启或者重新加载
```

#### **解决**

**修改3355模块**

**pom**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

**yml**

```yml
server:
  port: 3355

spring:
  application:
    name: config-client
  cloud:
    #Config客户端配置
    config:
      label: master #分支名称
      name: config #配置文件名称
      profile: dev #读取后缀名称   上述3个综合：master分支上config-dev.yml的配置文件被读取
      uri: http://localhost:3344 #配置中心地址k

#服务注册到eureka地址
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka
# 暴露监控端点
management:
  endpoints:
    web:
      exposure:
        include: "*"
```

**controller**

```java
@RestController
//开启监控
@RefreshScope
public class ConfigClientController {
    @Value("${config.info}")
    private String configInfo;

    @GetMapping("/configInfo")
    public String getConfigInfo() {
        return configInfo;
    }
}
```

**需要运维人员发送Post请求刷新3355**

```
curl -X POST "http://localhost:3355/actuator/refresh"
```

## SpringCloud Bus服务总线

### **概述**

```
Spring Cloud Bus 配合 Spring Cloud Config 使用可以实现配置的动态刷新。
Spring Cloud Bus是用来将分布式系统的节点与轻量级消息系统链接起来的框架，
它整合了Java的事件处理机制和消息中间件的功能。
Spring Clud Bus目前支持RabbitMQ和Kafka。
Spring Cloud Bus能管理和传播分布式系统间的消息，就像一个分布式执行器，可用于广播状态更改、事件推送等，也可以当作微服务间的通信通道。

什么是总线
在微服务架构的系统中，通常会使用轻量级的消息代理来构建一个共用的消息主题，并让系统中所有微服务实例都连接上来。由于该主题中产生的消息会被所有实例监听和消费，所以称它为消息总线。在总线上的各个实例，都可以方便地广播一些需要让其他连接在该主题上的实例都知道的消息。

基本原理
ConfigClient实例都监听MQ中同一个topic(默认是springCloudBus)。当一个服务刷新数据的时候，它会把这个信息放入到Topic中，这样其它监听同一Topic的服务就能得到通知，然后去更新自身的配置。
```

### SpringCloud Bus动态刷新全局广播

#### 演示广播效果，增加复杂度，再以3355为模板再制作一个3366

**cloud-config-client-3366**

**pom**

```xml
  <dependencies>
      <!--        引入bootstrap依赖-->
  <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-bootstrap</artifactId>
            <version>3.0.2</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

**bootstrap.yml**

```yml
server:
  port: 3355

spring:
  application:
    name: config-client
  cloud:
    #Config客户端配置
    config:
      label: master #分支名称
      name: config #配置文件名称
      profile: dev #读取后缀名称   上述3个综合：master分支上config-dev.yml的配置文件被读取http://config-3344.com:3344/master/config-dev.yml
      uri: http://localhost:3344 #配置中心地址k

#服务注册到eureka地址
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka
# 暴露监控端点
management:
  endpoints:
    web:
      exposure:
        include: "*"
```

**主启动**

```java

@SpringBootApplication
@EnableEurekaClient
public class ConfigClientMain3366 {
    public static void main(String[] args) {
        SpringApplication.run(ConfigClientMain3366.class, args);
    }
}
```

**controller**

```Java
@RestController
@RefreshScope
public class ConfigClientController {
    @Value("${server.port}")
    private String serverPort;

    @Value("${config.info}")
    private String configInfo;

    @GetMapping("/configInfo")
    public String configInfo() {
        return "serverPort: " + serverPort + "\t\n\n configInfo: " + configInfo;
    }
}
```

#### 设计思想

```
1）利用消息总线触发一个客户端/bus/refresh,而刷新所有客户端的配置
2）利用消息总线触发一个服务端ConfigServer的/bus/refresh端点，而刷新所有客户端的配置
图二的架构显然更加适合，图一不适合的原因如下
    打破了微服务的职责单一性，因为微服务本身是业务模块，它本不应该承担配置刷新的职责。
    破坏了微服务各节点的对等性。
    有一定的局限性。例如，微服务在迁移时，它的网络地址常常会发生变化，此时如果想要做到自动刷新，那就会增加更多的修改
```

#### 给cloud-config-center-3344配置中心服务端添加消息总线支持

**pom**

```xml
  <!--添加消息总线RabbitMQ支持-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-bus-amqp</artifactId>
        </dependency>
```

**yml**

```yml
server:
  port: 3344

spring:
  #rabbitmq相关配置
  rabbitmq:
    host: 192.168.182.129
    port: 5672
    username: admin
    password: 123
  application:
    name: cloud-config-center #注册进Eureka服务器的微服务名
  cloud:
    config:
      server:
        git:
          uri: https://gitee.com/yueli14/springcloud-config.git #GitHub上面的git仓库名字
          ####搜索目录
          search-paths:
            - springcloud-config
          username:
          password: 
          default-label: master
#      ####读取分支
#      label: master


##rabbitmq相关配置,暴露bus刷新配置的端点
management:
  endpoints: #暴露bus刷新配置的端点
    web:
      exposure:
        include: 'busrefresh'
  endpoint:
    busrefresh:
      enabled: true


#服务注册到eureka地址
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka
```

#### 给cloud-config-client-3355客户端添加消息总线支持

**pom**

```xml
  <!--添加消息总线RabbitMQ支持-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-bus-amqp</artifactId>
        </dependency>
```

**yml**

```yml
server:
  port: 3355

spring:
  rabbitmq:
    host: 192.168.182.129
    port: 5672
    username: admin
    password: 123
  application:
    name: config-client
  cloud:
    #Config客户端配置
    config:
      label: master #分支名称
      name: config #配置文件名称
      profile: dev #读取后缀名称   上述3个综合：master分支上config-dev.yml的配置文件被读取http://config-3344.com:3344/master/config-dev.yml
      uri: http://localhost:3344 #配置中心地址k

#服务注册到eureka地址
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka
# 暴露监控端点
management:
  endpoints:
    web:
      exposure:
        include: "*"
```

**给cloud-config-client-3366客户端添加消息总线支持**

同理

#### **测试**

```
curl -X POST "http://localhost:3344/actuator/bus-refresh"
一次修改，广播通知，处处生效
```

### SpringCloud Bus动态刷新定点通知

```
公式：http://localhost:配置中心的端口号/actuator/bus-refresh/{destination}

curl -X POST "http://localhost:3344/actuator/bus-refresh/config-client:3355"
```

## SpringCloud Stream消息驱动

### **概述**

```
屏蔽底层消息中间件的差异,降低切换成本，统一消息的编程模型
```

**官网**

```
https://spring.io/projects/spring-cloud
```

### 设计思想

```
在没有绑定器这个概念的情况下，我们的SpringBoot应用要直接与消息中间件进行信息交互的时候，
由于各消息中间件构建的初衷不同，它们的实现细节上会有较大的差异性
通过定义绑定器作为中间层，完美地实现了应用程序与消息中间件细节之间的隔离。
通过向应用程序暴露统一的Channel通道，使得应用程序不需要再考虑各种不同的消息中间件实现。
通过定义绑定器Binder作为中间层，实现了应用程序与消息中间件细节之间的隔离。
Binder可以生成Binding，Binding用来绑定消息容器的生产者和消费者，它有两种类型，INPUT和OUTPUT，INPUT对应于消费者，OUTPUT对应于生产者
Stream中的消息通信方式遵循了发布-订阅模式
```

### Spring Cloud Stream标准流程套路

```
Binder 很方便的连接中间件，屏蔽差异
Channel 通道，是队列Queue的一种抽象，在消息通讯系统中就是实现存储和转发的媒介，通过Channel对队列进行配置
Source和Sink 简单的可理解为参照对象是Spring Cloud Stream自身，从Stream发布消息就是输出，接受消息就是输入。
```

### 消息驱动之生产者

**cloud-stream-rabbitmq-provider8801**

**pom**

```xml
  <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
        </dependency>
        <!--基础配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

**yml**

```yml
server:
  port: 8801

spring:
  application:
    name: microservice-stream-provider-service
  rabbitmq:
    host: 192.168.182.129
    port: 5672
    username: admin
    password: 123
  cloud:
    stream:
      binders:
        defaultRabbit:
          type: rabbit
      bindings:
        output:
          destination: cruiiExchange
          content-type: application/json
          binder: defaultRabbit

eureka:
  client: # 客户端进行Eureka注册的配置
    service-url:
      defaultZone: http://localhost:7001/eureka
  instance:
    lease-renewal-interval-in-seconds: 2 # 设置心跳的时间间隔（默认是30秒）
    lease-expiration-duration-in-seconds: 5 # 如果现在超过了5秒的间隔（默认是90秒）
    instance-id: send-8801.com  # 在信息列表时显示主机名称
    prefer-ip-address: true     # 访问的路径变为IP地址
```

**主启动**

```java
@SpringBootApplication
public class StreamMQMain8801
{
    public static void main(String[] args)
    {
        SpringApplication.run(StreamMQMain8801.class,args);
    }
}
```

**service**

```java
/**
 * @Classname SendMessageService
 * @Description TODO
 * @Date 2021/12/25 14:57
 * @Created by 28327
 */

package com.wcl.springcloud.service.impl;

import com.wcl.springcloud.service.IMessageProvider;
import org.springframework.cloud.stream.annotation.EnableBinding;
import org.springframework.cloud.stream.messaging.Source;
import org.springframework.messaging.MessageChannel;
import org.springframework.messaging.support.MessageBuilder;

import javax.annotation.Resource;
import java.util.UUID;

@EnableBinding(Source.class)// 可以理解为是一个消息的发送管道的定义
public class SendMessageService implements IMessageProvider {
    @Resource
    private MessageChannel output;
    @Override
    public String send() {
        String serial = UUID.randomUUID().toString();
        this.output.send(MessageBuilder.withPayload(serial).build()); // 创建并发送消息
        System.out.println("***serial: "+serial);

        return serial;
    }
}
```

**controlelr**

```java
package com.wcl.springcloud.controller;

import com.wcl.springcloud.service.impl.SendMessageService;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
public class SendMessageController {
    @Resource
    private SendMessageService messageService;
    @GetMapping("/send")
    public String sendMessage(){
      return   messageService.send();
    }
}
```

### 消息驱动之消费者

**cloud-stream-rabbitmq-consumer8802**

**pom**

```xml
<dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--基础配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

```

**yml**

```yml
server:
  port: 8802

spring:
  rabbitmq:
    host: 192.168.182.129
    port: 5672
    username: admin
    password: 123
  application:
    name: cloud-stream-consumer
  cloud:
    stream:
      binders: # 在此处配置要绑定的rabbitmq的服务信息；
        defaultRabbit: # 表示定义的名称，用于于binding整合
          type: rabbit # 消息组件类型
#          environment:   # 设置rabbitmq的相关的环境配置
      bindings: # 服务的整合处理
        input: # 这个名字是一个通道的名称
          destination: cruiiExchange # 表示要使用的Exchange名称定义
          content-type: application/json # 设置消息类型，本次为对象json，如果是文本则设置“text/plain”
          binder: defaultRabbit # 设置要绑定的消息服务的具体设置

eureka:
  client: # 客户端进行Eureka注册的配置
    service-url:
      defaultZone: http://localhost:7001/eureka
  instance:
    lease-renewal-interval-in-seconds: 2 # 设置心跳的时间间隔（默认是30秒）
    lease-expiration-duration-in-seconds: 5 # 如果现在超过了5秒的间隔（默认是90秒）
    instance-id: receive-8802.com  # 在信息列表时显示主机名称
    prefer-ip-address: true     # 访问的路径变为IP地址
```

**主启动**

```java
/**
 * @Classname StreamMQMain8802
 * @Description TODO
 * @Date 2021/12/25 15:24
 * @Created by 28327
 */

package com.wcl.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class StreamMQMain8802
{
    public static void main(String[] args)
    {
        SpringApplication.run(StreamMQMain8802.class,args);
    }
}

```

**业务类**

```java
/**
 * @Classname ReceiveMessageListener
 * @Description TODO
 * @Date 2021/12/25 15:25
 * @Created by 28327
 */

package com.wcl.springcloud.service;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.stream.annotation.EnableBinding;
import org.springframework.cloud.stream.annotation.StreamListener;
import org.springframework.cloud.stream.messaging.Sink;
import org.springframework.messaging.Message;
import org.springframework.stereotype.Component;

@Component
@EnableBinding(Sink.class)
public class ReceiveMessageListener {
    @Value("${server.port}")
    private String port;

    @StreamListener(Sink.INPUT)
    public void receiveMessage(Message<String> message) {
        System.out.println("消费者1号，------->接收到的消息：" + message.getPayload()+"\t port: "+port);
    }
}
```

### 分组消费与持久化

**依照8802，clone出来一份运行8803**

**运行后有两个问题**

```
有重复消费问题
消息持久化问题
```

#### 分组

**原理**

```
微服务应用放置于同一个group中，就能够保证消息只会被其中一个应用消费一次。
不同的组是可以消费的，同一个组内会发生竞争关系，只有其中一个可以消费。
```

**将微服务配置与同一个组内**

```yml
spring:
  rabbitmq:
    host: 192.168.182.129
    port: 5672
    username: admin
    password: 123
  application:
    name: cloud-stream-consumer
  cloud:
    stream:
      binders: # 在此处配置要绑定的rabbitmq的服务信息；
        defaultRabbit: # 表示定义的名称，用于于binding整合
          type: rabbit # 消息组件类型
#          environment:   # 设置rabbitmq的相关的环境配置
      bindings: # 服务的整合处理
        input: # 这个名字是一个通道的名称
          destination: cruiiExchange # 表示要使用的Exchange名称定义
          content-type: application/json # 设置消息类型，本次为对象json，如果是文本则设置“text/plain”
          binder: defaultRabbit # 设置要绑定的消息服务的具体设置
          group: wcl  #配置相同的分组
```

#### **持久化**

```
自定义分组重启服务后会自动去队列拿消息
```

## SpringCloud Sleuth分布式请求链路跟踪

**官网**

```
https://spring.io/projects/spring-cloud-sleuth
```

**概述**

```
在微服务框架中，一个由客户端发起的请求在后端系统中会经过多个不同的的服务节点调用来协同产生最后的请求结果，每一个前段请求都会形成一条复杂的分布式服务调用链路，链路中的任何一环出现高延时或错误都会引起整个请求最后的失败。
```

**新建zipkin服务**

```
docker search zipkin
docker  pull openzipkin/zipkin
docker run -d --restart always -v /etc/localtime:/etc/localtime:ro -p 9411:9411 --name zipkin openzipkin/zipkin

docker  run --name=zipkin -p 9411:9411 -d openzipkin/zipkin
```

#### **服务提供者**

**cloud-provider-payment8001**

**pom**

```xml
  <!--包含了sleuth+zipkin-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zipkin</artifactId>
        </dependency>
```

**yml**

```yml
spring:
  application:
    name: cloud-payment-service
    #注册进Eureka也会应用此名字
    #将此微服务注册到zipkin
  zipkin:
    base-url: 192.168.182.129:9411
  sleuth:
    sampler:
      #采样率值介于 0 到 1 之间，1 则表示全部采集
      probability: 1
```

**controller**

```java
  @GetMapping("/payment/zipkin")
    public String paymentZipkin()
    {
        return "hi ,i'am paymentzipkin server fall back，welcome to wcl，O(∩_∩)O哈哈~";
    }
```

#### 服务消费者(调用方)

**cloud-consumer-order80**

**与服务者同理**

**controller**

```java
//zipkin+sleuth
@GetMapping("/consumer/payment/zipkin")
    public String paymentZipkin() {
        return restTemplate.getForObject("http://CLOUD-PAYMENT-SERVICE" + "/payment/zipkin/", String.class);
    }
```

# spring cloud alibaba

**官网**

```
https://spring.io/projects/spring-cloud-alibaba#overview
```

## nacos

```
Nacos 支持AP和CP模式的切换
```

**注意点**

```
官网：http://localhost:8848/nacos/index.html
学习条件：修改cmd配置  将启动模式改为standalone，也就是单机模式，默认模式是以集群模式启动的
```

### 服务管理

#### 基于Nacos的服务提供者

**pom**

```xml
  <!--SpringCloud ailibaba nacos -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
```

**yml**

```yml
server:
  port: 9001

spring:
  application:
    name: nacos-payment-provider
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #配置Nacos地址

management:
  endpoints:
    web:
      exposure:
        include: '*'
```

**主启动**

```java
@SpringBootApplication
@EnableDiscoveryClient
public class ProviderMain9001 {
    public static void main(String[] args) {
        SpringApplication.run(ProviderMain9001.class, args);
    }
}
```

**controller**

```yml
@RestController
public class ProviderController {
    @Value("${server.port}")
    private String serverPort;

    @GetMapping(value = "/payment/nacos/{id}")
    public String getPayment(@PathVariable("id") Integer id) {
        return "nacos registry, serverPort: " + serverPort + "\t id" + id;
    }
}
```

**搭建集群9002copy**

#### 基于Nacos的服务消费者

**pom**

```xml
  <!--SpringCloud ailibaba nacos -->
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>
```

**yml**

```yml
server:
  port: 8883


spring:
  application:
    name: nacos-order-consumer
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848


#消费者将要去访问的微服务名称(注册成功进nacos的微服务提供者)
service-url:
  nacos-user-service: http://nacos-payment-provider 
```

**业务类**

```java
@RestController
public class OrderController {
    @Resource
    private RestTemplate restTemplate;
    @Value("${server.port}")
    private String serverPort;

    @GetMapping(value = "/consumer/payment/nacos/{id}")
    public String getPayment(@PathVariable("id") Integer id) {
        return "nacos registry, serverPort: " + serverPort + "\t id" + id;
    }
}
```

**config**

```java
@Configuration
public class RestConfig {
    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }
}
```

### 配置中心

#### 基础配置

**cloudalibaba-config-nacos-client3377**

**pom**

```xml
<!--nacos-config-->
       <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-bootstrap</artifactId>
        </dependency>
        <!--nacos-config-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
        </dependency>
        <!--nacos-discovery-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
```

**bootstrap.yaml**

```yml
# nacos配置
server:
  port: 3377

spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #Nacos服务注册中心地址
      config:
        server-addr: localhost:8848 #Nacos作为配置中心地址
        file-extension: yaml #指定yaml格式的配置
# ${spring.application.name}-${spring.profile.active}.${spring.cloud.nacos.config.file-extension}
```

**application.yml**

```yml
spring:
  profiles:
    active: dev # 表示开发环境
    #active: test
```

**主启动**

```java
@SpringBootApplication
@EnableDiscoveryClient
public class ConfigMain3377 {
    public static void main(String[] args) {
        SpringApplication.run(ConfigMain3377.class, args);
    }
}
```

**业务类**

```java
@RestController
@RefreshScope //在控制器类加入@RefreshScope注解使当前类下的配置支持Nacos的动态刷新功能。
public class ConfigClientController
{
    @Value("${config.info}")
    private String configInfo;

    @GetMapping("/config/info")
    public String getConfigInfo() {
        return configInfo;
    }
}
```

**测试**

```
在nacos中新建nacos-config-client-dev.yaml文件
然后里面进行详细配置
且自带动态刷新功能
```

#### 分类配置

##### **DataID方案**

指定spring.profile.active和配置文件的DataID来使不同环境下读取不同的配置

默认空间+默认分组+新建dev和test两个DataID

```
nacos-config-client-test.yaml
nacos-config-client-dev.yaml
```

```
spring:
  profiles:
    active: dev # 表示开发环境
    #active: test
```

##### Group方案

通过Group实现环境区分

在bootstrap绑定组

```yml
spring:
  application:
    name: nacos-config-client
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #Nacos服务注册中心地址
      config:
        server-addr: localhost:8848 #Nacos作为配置中心地址
        file-extension: yaml #指定yaml格式的配置
        group: TEST_GROUP 
```

##### Namespace方案

```yml
# nacos注册中心
server:
  port: 3377

spring:
  application:
    name: nacos-order
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #Nacos服务注册中心地址
      config:
        server-addr: localhost:8848 #Nacos作为配置中心地址
        file-extension: yaml #这里我们获取的yaml格式的配置
        namespace: 5da1dccc-ee26-49e0-b8e5-7d9559b95ab0 #流水id号
        #group: DEV_GROUP
        group: TEST_GROUP
```

### 集群和持久化

**官网**

```
https://nacos.io/zh-cn/docs/cluster-mode-quick-start.html
```

#### 持久化

```
Nacos默认自带的是嵌入式数据库derby
nacos-server-1.1.4\nacos\conf目录下找到sql脚本，执行脚本
```

```sql
CREATE DATABASE nacos_config;

USE nacos_config;
/*
 * Copyright 1999-2018 Alibaba Group Holding Ltd.
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 *      http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 */

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = config_info   */
/******************************************/
CREATE TABLE `config_info` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(255) DEFAULT NULL,
  `content` longtext NOT NULL COMMENT 'content',
  `md5` varchar(32) DEFAULT NULL COMMENT 'md5',
  `gmt_create` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '修改时间',
  `src_user` text COMMENT 'source user',
  `src_ip` varchar(50) DEFAULT NULL COMMENT 'source ip',
  `app_name` varchar(128) DEFAULT NULL,
  `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
  `c_desc` varchar(256) DEFAULT NULL,
  `c_use` varchar(64) DEFAULT NULL,
  `effect` varchar(64) DEFAULT NULL,
  `type` varchar(64) DEFAULT NULL,
  `c_schema` text,
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_configinfo_datagrouptenant` (`data_id`,`group_id`,`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_info';

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = config_info_aggr   */
/******************************************/
CREATE TABLE `config_info_aggr` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(255) NOT NULL COMMENT 'group_id',
  `datum_id` varchar(255) NOT NULL COMMENT 'datum_id',
  `content` longtext NOT NULL COMMENT '内容',
  `gmt_modified` datetime NOT NULL COMMENT '修改时间',
  `app_name` varchar(128) DEFAULT NULL,
  `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_configinfoaggr_datagrouptenantdatum` (`data_id`,`group_id`,`tenant_id`,`datum_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='增加租户字段';


/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = config_info_beta   */
/******************************************/
CREATE TABLE `config_info_beta` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(128) NOT NULL COMMENT 'group_id',
  `app_name` varchar(128) DEFAULT NULL COMMENT 'app_name',
  `content` longtext NOT NULL COMMENT 'content',
  `beta_ips` varchar(1024) DEFAULT NULL COMMENT 'betaIps',
  `md5` varchar(32) DEFAULT NULL COMMENT 'md5',
  `gmt_create` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '修改时间',
  `src_user` text COMMENT 'source user',
  `src_ip` varchar(50) DEFAULT NULL COMMENT 'source ip',
  `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_configinfobeta_datagrouptenant` (`data_id`,`group_id`,`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_info_beta';

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = config_info_tag   */
/******************************************/
CREATE TABLE `config_info_tag` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(128) NOT NULL COMMENT 'group_id',
  `tenant_id` varchar(128) DEFAULT '' COMMENT 'tenant_id',
  `tag_id` varchar(128) NOT NULL COMMENT 'tag_id',
  `app_name` varchar(128) DEFAULT NULL COMMENT 'app_name',
  `content` longtext NOT NULL COMMENT 'content',
  `md5` varchar(32) DEFAULT NULL COMMENT 'md5',
  `gmt_create` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '修改时间',
  `src_user` text COMMENT 'source user',
  `src_ip` varchar(50) DEFAULT NULL COMMENT 'source ip',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_configinfotag_datagrouptenanttag` (`data_id`,`group_id`,`tenant_id`,`tag_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_info_tag';

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = config_tags_relation   */
/******************************************/
CREATE TABLE `config_tags_relation` (
  `id` bigint(20) NOT NULL COMMENT 'id',
  `tag_name` varchar(128) NOT NULL COMMENT 'tag_name',
  `tag_type` varchar(64) DEFAULT NULL COMMENT 'tag_type',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(128) NOT NULL COMMENT 'group_id',
  `tenant_id` varchar(128) DEFAULT '' COMMENT 'tenant_id',
  `nid` bigint(20) NOT NULL AUTO_INCREMENT,
  PRIMARY KEY (`nid`),
  UNIQUE KEY `uk_configtagrelation_configidtag` (`id`,`tag_name`,`tag_type`),
  KEY `idx_tenant_id` (`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_tag_relation';

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = group_capacity   */
/******************************************/
CREATE TABLE `group_capacity` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `group_id` varchar(128) NOT NULL DEFAULT '' COMMENT 'Group ID，空字符表示整个集群',
  `quota` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '配额，0表示使用默认值',
  `usage` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '使用量',
  `max_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个配置大小上限，单位为字节，0表示使用默认值',
  `max_aggr_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '聚合子配置最大个数，，0表示使用默认值',
  `max_aggr_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个聚合数据的子配置大小上限，单位为字节，0表示使用默认值',
  `max_history_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '最大变更历史数量',
  `gmt_create` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '修改时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_group_id` (`group_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='集群、各Group容量信息表';

/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = his_config_info   */
/******************************************/
CREATE TABLE `his_config_info` (
  `id` bigint(64) unsigned NOT NULL,
  `nid` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
  `data_id` varchar(255) NOT NULL,
  `group_id` varchar(128) NOT NULL,
  `app_name` varchar(128) DEFAULT NULL COMMENT 'app_name',
  `content` longtext NOT NULL,
  `md5` varchar(32) DEFAULT NULL,
  `gmt_create` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `gmt_modified` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP,
  `src_user` text,
  `src_ip` varchar(50) DEFAULT NULL,
  `op_type` char(10) DEFAULT NULL,
  `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
  PRIMARY KEY (`nid`),
  KEY `idx_gmt_create` (`gmt_create`),
  KEY `idx_gmt_modified` (`gmt_modified`),
  KEY `idx_did` (`data_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='多租户改造';


/******************************************/
/*   数据库全名 = nacos_config   */
/*   表名称 = tenant_capacity   */
/******************************************/
CREATE TABLE `tenant_capacity` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `tenant_id` varchar(128) NOT NULL DEFAULT '' COMMENT 'Tenant ID',
  `quota` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '配额，0表示使用默认值',
  `usage` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '使用量',
  `max_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个配置大小上限，单位为字节，0表示使用默认值',
  `max_aggr_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '聚合子配置最大个数',
  `max_aggr_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个聚合数据的子配置大小上限，单位为字节，0表示使用默认值',
  `max_history_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '最大变更历史数量',
  `gmt_create` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '修改时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_tenant_id` (`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='租户容量信息表';


CREATE TABLE `tenant_info` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `kp` varchar(128) NOT NULL COMMENT 'kp',
  `tenant_id` varchar(128) default '' COMMENT 'tenant_id',
  `tenant_name` varchar(128) default '' COMMENT 'tenant_name',
  `tenant_desc` varchar(256) DEFAULT NULL COMMENT 'tenant_desc',
  `create_source` varchar(32) DEFAULT NULL COMMENT 'create_source',
  `gmt_create` bigint(20) NOT NULL COMMENT '创建时间',
  `gmt_modified` bigint(20) NOT NULL COMMENT '修改时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_tenant_info_kptenantid` (`kp`,`tenant_id`),
  KEY `idx_tenant_id` (`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='tenant_info';

CREATE TABLE `users` (
	`username` varchar(50) NOT NULL PRIMARY KEY,
	`password` varchar(500) NOT NULL,
	`enabled` boolean NOT NULL
);

CREATE TABLE `roles` (
	`username` varchar(50) NOT NULL,
	`role` varchar(50) NOT NULL,
	UNIQUE INDEX `idx_user_role` (`username` ASC, `role` ASC) USING BTREE
);

CREATE TABLE `permissions` (
    `role` varchar(50) NOT NULL,
    `resource` varchar(255) NOT NULL,
    `action` varchar(8) NOT NULL,
    UNIQUE INDEX `uk_role_permission` (`role`,`resource`,`action`) USING BTREE
);

INSERT INTO users (username, password, enabled) VALUES ('nacos', '$2a$10$EuWPZHzz32dJN7jexM34MOeYirDdFAZm2kuWj7VEOJhhZkDrxfvUu', TRUE);

INSERT INTO roles (username, role) VALUES ('nacos', 'ROLE_ADMIN');
```

**nacos-server-1.1.4\nacos\conf目录下找到application.properties**

```
### If use MySQL as datasource:
 spring.datasource.platform=mysql
### Count of DB:
 db.num=1
### Connect URL of DB:
db.url.0=jdbc:mysql://127.0.0.1:3306/nacos_config?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
db.user.0=root
db.password.0=123456
```

**在nacos安装目录下新建plugins/mysql文件夹，放入对应版本的jar包**

#### 集群搭建

**Nacos下载Linux版,安装备份**

**Linux服务器上mysql数据库配置，nacos-mysql.sql**

**修改application.properties，加入数据库配置**

**Linux服务器上nacos的集群配置cluster.conf**

```
这个IP不能写127.0.0.1，必须是Linux命令hostname -i能够识别的IP
例如 
192.168.1.78:3333
192.168.1.78:3334
```

**执行方式**

```
./startup.sh -p 3333
```

**Nginx的配置，由它作为负载均衡器**

```nginx
upstream cluster{
 	server ip;
 	server ip;
}
server {
	listen port;
	server_name localhost;
	location / {
		proxy_pass http://cluster;
	}
}
```

**指定配置文件启动nginx**

```
./nginx -c /pwd/nginx.conf
```

**修改工程yaml文件中的ip属性**

## sentinel

**官网**

```
https://github.com/alibaba/Sentinel
```

```
java -jar sentinel-dashboard-1.8.3.jar
```

### 初始化演示工程

**新建cloudalibaba-sentinel-service8401**

**pom**

```xml
<dependencies>
        <!--SpringCloud ailibaba nacos -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <!--SpringCloud ailibaba sentinel-datasource-nacos 后续做持久化用到-->
        <dependency>
            <groupId>com.alibaba.csp</groupId>
            <artifactId>sentinel-datasource-nacos</artifactId>
        </dependency>
        <!--SpringCloud ailibaba sentinel -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
        </dependency>
        <!--openfeign-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <!-- SpringBoot整合Web组件+actuator -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--日常通用jar包配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-all</artifactId>
            <version>4.6.3</version>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

    </dependencies>
```

**yaml**

```yml
server:
  port: 8401

spring:
  main:
  #开启循环引用
    allow-circular-references: true
  application:
    name: cloudalibaba-sentinel-service
  cloud:
    nacos:
      discovery:
        #Nacos服务注册中心地址
        server-addr: localhost:8848
    sentinel:
      transport:
        #配置Sentinel dashboard地址
        dashboard: 192.168.182.129:8080
        #默认8719端口，假如被占用会自动从8719开始依次+1扫描,直至找到未被占用的端口
        port: 8719

management:
  endpoints:
    web:
      exposure:
        include: '*'
```

**主启动**

```java
@EnableDiscoveryClient
@SpringBootApplication
public class MainSentinel8401 {
    public static void main(String[] args) {
        SpringApplication.run(MainSentinel8401.class, args);
    }
}
```

**业务类**

```java
@RestController
public class FlowLimitController {
    @GetMapping("/testA")
    public String testA() {
        return "------testA";
    }

    @GetMapping("/testB")
    public String testB() {
        return "------testB";
    }
}
```

### 流控规则

**官网**

```
https://github.com/alibaba/Sentinel/wiki
```



#### **基本介绍**

![image-20220107190032338](D:/mdimgs/image-20220107190032338.png)

#### **流控模式**

**直接(默认)**

表示1秒钟内查询1次就是OK，若超过次数1，就直接-快速失败，报默认错误

![image-20220107192654451](D:/mdimgs/image-20220107192654451.png)

**关联**

当关联的资源达到阈值时，就限流自己或者当与A关联的资源B达到阀值后，就限流A自己，B惹事，A挂了

当关联资源/testB的qps阀值超过1时，就限流/testA的Rest访问地址，当关联资源到阈值后限制配置好的资源名

![image-20220107192819181](D:/mdimgs/image-20220107192819181.png)

**链路**

多个请求调用了同一个微服务，当从某个接口过来的资源达到限流条件时，开启限流。

#### 流控效果

**直接->快速失败(默认的流控处理)**

直接失败，抛出异常

**预热**

默认coldFactor为3，即请求 QPS 从 threshold / 3 开始，经预热时长逐渐升至设定的 QPS 阈值。

![image-20220107194250955](D:/mdimgs/image-20220107194250955.png)

**排队等待**

匀速排队，阈值必须设置为QPS

设置含义：/testA每秒1次请求，超过的话就排队等待，等待的超时时间为20000毫秒。

![image-20220107194317391](D:/mdimgs/image-20220107194317391.png)

### 降级（熔断）规则

#### 基本介绍

```
RT（平均响应时间，秒级）
   平均响应时间  超出阈值 且  在时间窗口内通过的请求>=5，两个条件同时满足后触发降级
   窗口期过后关闭断路器
   RT最大4900（更大的需要通过-Dcsp.sentinel.statistic.max.rt=XXXX才能生效）
异常比列（秒级）
  QPS >= 5 且异常比例（秒级统计）超过阈值时，触发降级；时间窗口结束后，关闭降级
异常数（分钟级）
   异常数（分钟统计）超过阈值时，触发降级；时间窗口结束后，关闭降级
```

#### **降级策略**

- **慢调用比例 (`SLOW_REQUEST_RATIO`)**：选择以慢调用比例作为阈值，需要设置允许的慢调用 RT（即最大的响应时间），请求的响应时间大于该值则统计为慢调用。当单位统计时长（`statIntervalMs`）内请求数目大于设置的最小请求数目，并且慢调用的比例大于阈值，则接下来的熔断时长内请求会自动被熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF-OPEN 状态），若接下来的一个请求响应时间小于设置的慢调用 RT 则结束熔断，若大于设置的慢调用 RT 则会再次被熔断。
- **异常比例 (`ERROR_RATIO`)**：当单位统计时长（`statIntervalMs`）内请求数目大于设置的最小请求数目，并且异常的比例大于阈值，则接下来的熔断时长内请求会自动被熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF-OPEN 状态），若接下来的一个请求成功完成（没有错误）则结束熔断，否则会再次被熔断。异常比率的阈值范围是 `[0.0, 1.0]`，代表 0% - 100%。
- **异常数 (`ERROR_COUNT`)**：当单位统计时长内的异常数目超过阈值之后会自动进行熔断。经过熔断时长后熔断器会进入探测恢复状态（HALF-OPEN 状态），若接下来的一个请求成功完成（没有错误）则结束熔断，否则会再次被熔断。

### 热点key限流

**pom**

```xml
 <dependency>
            <groupId>com.alibaba.csp</groupId>
            <artifactId>sentinel-parameter-flow-control</artifactId>
        </dependency>
```

**controller**

```java
 //演示热点key限流
    @GetMapping("/testHotKey")
    //出现异常后的降级方法
    @SentinelResource(value = "testHotKey",blockHandler = "dealHandler_testHotKey")
    public String testHotKey(@RequestParam(value = "p1",required = false) String p1,

                             @RequestParam(value = "p2",required = false) String p2){
        return "------testHotKey";
    }
    public String dealHandler_testHotKey(String p1, String p2, BlockException exception)
    {
        return "-----dealHandler_testHotKey";
    }
```

**配置**

![image-20220108151125026](D:/mdimgs/image-20220108151125026.png)

```
表示第一个参数访问频率超过一秒一次的时候，将被限流，当参数为特殊值的时候，就有别的阈值
```

### 系统自适应限流

**就是配置整个系统的吞吐量进行限制**

系统保护规则是从应用级别的入口流量进行控制，从单台机器的 load、CPU 使用率、平均 RT、入口 QPS 和并发线程数等几个维度监控应用指标，让系统尽可能跑在最大吞吐量的同时保证系统整体的稳定性。

系统保护规则是应用整体维度的，而不是资源维度的，并且**仅对入口流量生效**。入口流量指的是进入应用的流量（`EntryType.IN`），比如 Web 服务或 Dubbo 服务端接收的请求，都属于入口流量。

系统规则支持以下的模式：

- **Load 自适应**（仅对 Linux/Unix-like 机器生效）：系统的 load1 作为启发指标，进行自适应系统保护。当系统 load1 超过设定的启发值，且系统当前的并发线程数超过估算的系统容量时才会触发系统保护（BBR 阶段）。系统容量由系统的 `maxQps * minRt` 估算得出。设定参考值一般是 `CPU cores * 2.5`。
- **CPU usage**（1.5.0+ 版本）：当系统 CPU 使用率超过阈值即触发系统保护（取值范围 0.0-1.0），比较灵敏。
- **平均 RT**：当单台机器上所有入口流量的平均 RT 达到阈值即触发系统保护，单位是毫秒。
- **并发线程数**：当单台机器上所有入口流量的并发线程数达到阈值即触发系统保护。
- **入口 QPS**：当单台机器上所有入口流量的 QPS 达到阈值即触发系统保护。

### @SentinelResource

#### **按资源名称限流+后续处理**

**controller**

```java
@RestController
public class RateLimitController {
    @GetMapping("/byResource")
    @SentinelResource(value = "byResource",blockHandler = "handleException")
    public CommonResult byResource()
    {
        return new CommonResult(200,"按资源名称限流测试OK",new Payment(2020L,"serial001"));
    }
    public CommonResult handleException(BlockException exception)
    {
        return new CommonResult(444,exception.getClass().getCanonicalName()+"\t 服务不可用");
    }
}
```

**配置**

![image-20220108153842296](D:/mdimgs/image-20220108153842296.png)

#### 按照Url地址限流+后续处理

**业务类**

```java
 @GetMapping("/rateLimit/byUrl")
    @SentinelResource(value = "byUrl")
    public CommonResult byUrl()
    {
        return new CommonResult(200,"按url限流测试OK",new Payment(2020L,"serial002"));
    }
```

**配置**

![image-20220108153930685](D:/mdimgs/image-20220108153930685.png)

#### 问题

```
1    系统默认的，没有体现我们自己的业务要求。

2  依照现有条件，我们自定义的处理方法又和业务代码耦合在一块，不直观。

3  每个业务方法都添加一个兜底的，那代码膨胀加剧。

4  全局统一的处理方法没有体现。
```

#### 客户自定义限流处理逻辑

**自定义限流处理类CustomerBlockHandler**

```java
public class CustomerBlockHandler {
    public static CommonResult handleException(BlockException exception){
        return new CommonResult(2020,"自定义的限流处理信息......CustomerBlockHandler");
    }
}
```

**业务类**

```java
/**
     * 自定义通用的限流处理逻辑，
     blockHandlerClass = CustomerBlockHandler.class
     blockHandler = handleException2
     上述配置：找CustomerBlockHandler类里的handleException2方法进行兜底处理
     */
    /**
     * 自定义通用的限流处理逻辑
     */
    @GetMapping("/rateLimit/customerBlockHandler")
    @SentinelResource(value = "customerBlockHandler",
            blockHandlerClass = CustomerBlockHandler.class, blockHandler = "handleException")
    public CommonResult customerBlockHandler() {
        return new CommonResult(200, "按客户自定义限流处理逻辑");
    }
```

### 服务熔断功能

#### Ribbon系列

##### **提供者9003/9004**

**pom**

```xml
   <dependencies>
        <!--SpringCloud ailibaba nacos -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <dependency><!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
            <groupId>com.wcl</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>
        <!-- SpringBoot整合Web组件 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--日常通用jar包配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

**yml**

```yaml
server:
  port: 9003

spring:
  application:
    name: nacos-payment-provider
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #配置Nacos地址

management:
  endpoints:
    web:
      exposure:
        include: '*'
```

**主启动**

```java
@SpringBootApplication
@EnableDiscoveryClient
public class ProviderMain9004 {
    public static void main(String[] args) {
        SpringApplication.run(ProviderMain9004.class, args);
    }
}
```

**业务类**

```java
@RestController
public class PaymentController {
    @Value("${server.port}")
    private String serverPort;

    public static HashMap<Long, Payment> hashMap = new HashMap<>();

    static {
        hashMap.put(1L, new Payment(1L, "28a8c1e3bc2742d8848569891fb42181"));
        hashMap.put(2L, new Payment(2L, "bba8c1e3bc2742d8848569891ac32182"));
        hashMap.put(3L, new Payment(3L, "6ua8c1e3bc2742d8848569891xt92183"));
    }

    @GetMapping(value = "/paymentSQL/{id}")
    public CommonResult<Payment> paymentSQL(@PathVariable("id") Long id) {
        Payment payment = hashMap.get(id);
        CommonResult<Payment> result = new CommonResult(200, "from mysql,serverPort:  " + serverPort, payment);
        return result;
    }


}
```

##### 消费者84

**pom**

```xml
    <dependencies>
        <!--SpringCloud ailibaba nacos -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <!--SpringCloud ailibaba sentinel -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
        </dependency>
        <!-- 引入自己定义的api通用包，可以使用Payment支付Entity -->
        <dependency>
            <groupId>com.wcl</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <!-- SpringBoot整合Web组件 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--日常通用jar包配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

**yml**

```yml
server:
  port: 84


spring:
  main:
    allow-circular-references: true
  application:
    name: nacos-order-consumer
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848
    sentinel:
      transport:
        #配置Sentinel dashboard地址
        dashboard: localhost:8080
        #默认8719端口，假如被占用会自动从8719开始依次+1扫描,直至找到未被占用的端口
        port: 8719


#消费者将要去访问的微服务名称(注册成功进nacos的微服务提供者)
service-url:
  nacos-user-service: http://nacos-payment-provider
```

**主启动**

```java
@SpringBootApplication
@EnableDiscoveryClient
public class OrderNacosMain84 {
    public static void main(String[] args) {
        SpringApplication.run(OrderNacosMain84.class, args);
    }
}
```

**config**

```java
@Configuration
public class RestTemplateConfig {
    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate()
    {
        return new RestTemplate();
    }
}
```

**业务类**

```java
/**
 * @Classname CircleBreakerController
 * @Description TODO
 * @Date 2022/1/8 16:04
 * @Created by 28327
 */

package com.wcl.cloudalibaba.controller;

import com.alibaba.csp.sentinel.annotation.SentinelResource;
import com.wcl.springcloud.entities.CommonResult;
import com.wcl.springcloud.entities.Payment;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import javax.annotation.Resource;

@RestController
@Slf4j
public class CircleBreakerController {
    public static final String SERVICE_URL = "http://nacos-payment-provider";

    @Resource
    private RestTemplate restTemplate;

    @RequestMapping("/consumer/fallback/{id}")
    @SentinelResource(value = "fallback")

    public CommonResult<Payment> fallback(@PathVariable Long id) {
        CommonResult<Payment> result = restTemplate.getForObject(SERVICE_URL + "/paymentSQL/" + id, CommonResult.class, id);

        if (id == 4) {
            throw new IllegalArgumentException("IllegalArgumentException,非法参数异常....");
        } else if (result.getData() == null) {
            throw new NullPointerException("NullPointerException,该ID没有对应记录,空指针异常");
        }

        return result;
    }
}
```

##### 只配置fallback（可以管理运行时异常）

```java
 @RequestMapping("/consumer/fallback/{id}")
    @SentinelResource(value = "fallback", fallback = "handlerFallback") //fallback负责业务异常
    public CommonResult<Payment> fallback(@PathVariable Long id) {
        CommonResult<Payment> result = restTemplate.getForObject(SERVICE_URL + "/paymentSQL/" + id, CommonResult.class, id);

        if (id == 4) {
            throw new IllegalArgumentException("IllegalArgumentException,非法参数异常....");
        } else if (result.getData() == null) {
            throw new NullPointerException("NullPointerException,该ID没有对应记录,空指针异常");
        }

        return result;
    }

    public CommonResult handlerFallback(@PathVariable Long id, Throwable e) {
        Payment payment = new Payment(id, "null");
        return new CommonResult<>(444, "兜底异常handlerFallback,exception内容  " + e.getMessage(), payment);
    }
```

##### 只配置blockHandler（管理sentinel的配置违规）

```java
 @RequestMapping("/consumer/fallback/{id}")
    @SentinelResource(value = "fallback", blockHandler = "blockHandler") //blockHandler负责在sentinel里面配置的降级限流
    public CommonResult<Payment> fallback(@PathVariable Long id) {
        CommonResult<Payment> result = restTemplate.getForObject(SERVICE_URL + "/paymentSQL/" + id, CommonResult.class, id);
        if (id == 4) {
            throw new IllegalArgumentException("非法参数异常....");
        } else if (result.getData() == null) {
            throw new NullPointerException("NullPointerException,该ID没有对应记录");
        }
        return result;
    }
   public CommonResult blockHandler(@PathVariable Long id, BlockException blockException) {
        Payment payment = new Payment(id, "null");
        return new CommonResult<>(445, "blockHandler-sentinel限流,无此流水: blockException  " + blockException.getMessage(), payment);
    }
```

##### fallback和blockHandler都配置

**优先级blockHandler大于fallback**

```java
  @RequestMapping("/consumer/fallback/{id}")
    @SentinelResource(value = "fallback", blockHandler = "blockHandler", fallback = "handlerFallback")
    //blockHandler负责在sentinel里面配置的降级限流
    public CommonResult<Payment> fallback(@PathVariable Long id) {
        CommonResult<Payment> result = restTemplate.getForObject(SERVICE_URL + "/paymentSQL/" + id, CommonResult.class, id);
        if (id == 4) {
            throw new IllegalArgumentException("非法参数异常....");
        } else if (result.getData() == null) {
            throw new NullPointerException("NullPointerException,该ID没有对应记录");
        }
        return result;
    }

    public CommonResult handlerFallback(@PathVariable Long id, Throwable e) {
        Payment payment = new Payment(id, "null");
        return new CommonResult<>(444, "fallback,无此流水,exception  " + e.getMessage(), payment);
    }

    public CommonResult blockHandler(@PathVariable Long id, BlockException blockException) {
        Payment payment = new Payment(id, "null");
        return new CommonResult<>(445, "blockHandler-sentinel限流,无此流水: blockException  " + blockException.getMessage(), payment);
    }
```

##### 忽略属性（忽略兜底）

```java
 public static final String SERVICE_URL = "http://nacos-payment-provider";

    @Resource
    private RestTemplate restTemplate;

    @RequestMapping("/consumer/fallback/{id}")
    @SentinelResource(value = "fallback", fallback = "handlerFallback", blockHandler = "blockHandler",
            exceptionsToIgnore = {IllegalArgumentException.class})
    public CommonResult<Payment> fallback(@PathVariable Long id)
    {
        CommonResult<Payment> result = restTemplate.getForObject(SERVICE_URL + "/paymentSQL/"+id,CommonResult.class,id);
        if (id == 4) {
            throw new IllegalArgumentException ("非法参数异常....");
        }else if (result.getData() == null) {
            throw new NullPointerException ("NullPointerException,该ID没有对应记录");
        }
        return result;
    }
    public CommonResult handlerFallback(@PathVariable  Long id,Throwable e) {
        Payment payment = new Payment(id,"null");
        return new CommonResult<>(444,"fallback,无此流水,exception  "+e.getMessage(),payment);
    }
    public CommonResult blockHandler(@PathVariable  Long id,BlockException blockException) {
        Payment payment = new Payment(id,"null");
        return new CommonResult<>(445,"blockHandler-sentinel限流,无此流水: blockException  "+blockException.getMessage(),payment);
    }
```

#### Feign系列

**pom**

```xml
<!--        添加此依赖-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-loadbalancer</artifactId>
            <version>2.2.1.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
            <version>2.2.0.RELEASE</version>
        </dependency>
        <!--SpringCloud ailibaba nacos 排除 ribbon依赖-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.cloud</groupId>
                    <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
```

**yml**

```yaml
# 激活Sentinel对Feign的支持
feign:
  sentinel:
    enabled: true  
```

**service接口**

```java
/**
 
 * 使用 fallback 方式是无法获取异常信息的，
 * 如果想要获取异常信息，可以使用 fallbackFactory参数
 */
@FeignClient(value = "nacos-payment-provider",fallback = PaymentFallbackService.class)//调用中关闭9003服务提供者
public interface PaymentService
{
    @GetMapping(value = "/paymentSQL/{id}")
    public CommonResult<Payment> paymentSQL(@PathVariable("id") Long id);
}
```

**service实现类**

```java
@Component
public class PaymentFallbackService implements PaymentService {
    @Override
    public CommonResult<Payment> paymentSQL(Long id) {
        return new CommonResult<>(444, "服务降级返回,没有该流水信息", new Payment(id, "errorSerial......"));
    }
}
```

**controller**

```java
//==================OpenFeign
    @Resource
    private PaymentService paymentService;

    @GetMapping(value = "/consumer/openfeign/{id}")
    public CommonResult<Payment> paymentSQL(@PathVariable("id") Long id) {
        if (id == 4) {
            throw new RuntimeException("没有该id");
        }
        return paymentService.paymentSQL(id);
    }
```

**主启动**

```
@EnableFeignClients
```

### 持久化规则

**pom**

```xml
<!--SpringCloud ailibaba sentinel-datasource-nacos -->
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-datasource-nacos</artifactId>
</dependency>
```

**yml**

```yml
server:
  port: 8401

spring:
  application:
    name: cloudalibaba-sentinel-service
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #Nacos服务注册中心地址
    sentinel:
      transport:
        dashboard: 192.168.182.129:8080#配置Sentinel dashboard地址
        port: 8719
      datasource:
        ds1:
          nacos:
            server-addr: localhost:8848
            dataId: cloudalibaba-sentinel-service
            groupId: DEFAULT_GROUP
            data-type: json
            rule-type: flow

management:
  endpoints:
    web:
      exposure:
        include: '*'

feign:
  sentinel:
    enabled: true # 激活Sentinel对Feign的支持
```

**在nacos中新建cloudalibaba-sentinel-service**

```json
[
    {
        "resource": "/rateLimit/byUrl",
        "limitApp": "default",
        "grade": 1,
        "count": 1,
        "strategy": 0,
        "controlBehavior": 0,
        "clusterMode": false
    }
]
```

**解析**

```
resource：资源名称；
limitApp：来源应用；
grade：阈值类型，0表示线程数，1表示QPS；
count：单机阈值；
strategy：流控模式，0表示直接，1表示关联，2表示链路；
controlBehavior：流控效果，0表示快速失败，1表示Warm Up，2表示排队等待；
clusterMode：是否集群。
```

## Seata

### 基础

**TC (Transaction Coordinator) - 事务协调者**

维护全局和分支事务的状态，驱动全局事务提交或回滚。

**TM (Transaction Manager) - 事务管理器**

定义全局事务的范围：开始全局事务、提交或回滚全局事务。

**RM (Resource Manager) - 资源管理器**

管理分支事务处理的资源，与TC交谈以注册分支事务和报告分支事务的状态，并驱动分支事务提交或回滚。

### 修改file.conf

```json
 db {
    ## the implement of javax.sql.DataSource, such as DruidDataSource(druid)/BasicDataSource(dbcp)/HikariDataSource(hikari) etc.
    datasource = "druid"
    ## mysql/oracle/postgresql/h2/oceanbase etc.
    dbType = "mysql"
    ##8.0之后
    driverClassName = "com.mysql.cj.jdbc.Driver"
    ## if using mysql to store the data, recommend add rewriteBatchedStatements=true in jdbc connection param
    url = "jdbc:mysql://127.0.0.1:3306/seata?rewriteBatchedStatements=true"
    user = "root"
    password = "123456"
    minConn = 5
    maxConn = 100
    globalTable = "global_table"
    branchTable = "branch_table"
    lockTable = "lock_table"
    queryLimit = 100
    maxWait = 5000
  }
```

### **register.conf**

```json
  type = "nacos"

  nacos {
    application = "seata-server"
    serverAddr = "127.0.0.1:8848"
    group = "SEATA_GROUP"
    namespace = ""
    cluster = "default"
    username = ""
    password = ""
  }
```

**看官网吧**

```
https://seata.io/zh-cn/docs/user/quickstart.html
```

