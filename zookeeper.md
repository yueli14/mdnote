## Zookeeper 入门

**概述**

Zookeeper 是一个开源的分布式的，为分布式框架提供协调服务的 Apache 项目。

**Zookeeper工作机制**

基于观察者模式设计的分布式服务管理框架，它负 责 存储和管理大家都关心的数据，然 后接受观察者的 注 册，一旦这些数据的状态发生变化，Zookeeper 就 **将负责通知已经在Zookeeper上注册的那些观察 者**做出相应的反应。

**Zookeeper=文件系统+通知机制**

**特点**

![image-20211209222111643](D:/mdimgs/image-20211209222111643.png)

1）Zookeeper：一个领导者（Leader），多个跟随者（Follower）组成的集群。 

2）集群中只要有半数以上节点存活，Zookeeper集群就能正常服务。所 以Zookeeper适合安装奇数台服务器。

 3）全局数据一致：每个Server保存一份相同的数据副本，Client无论连接到哪个Server，数据都是一致的。 

4）更新请求顺序执行，来自同一个Client的更新请求按其发送顺序依次执行。

 5）数据更新原子性，一次数据更新要么成功，要么失败。

 6）实时性，在一定时间范围内，Client能读到最新数据。

**数据结构**

ZooKeeper 数据模型的结构与 **Unix 文件系统很类似**，整体上可以看作是一棵树，每个 节点称做一个 ZNode。每一个 ZNode 默认能够存储 1MB 的数据，每个 ZNode 都可以通过 其路径唯一标识。

**应用场景**

提供的服务包括：统一命名服务、统一配置管理、统一集群管理、服务器节点动态上下 线、软负载均衡等

## Zookeeper 本地安装

**安装 JDK**

**解压到指定目录**

```
tar -zxvf apache-zookeeper-3.5.7-bin.tar.gz -C /opt/module/
```

**配置修改**

```
将/opt/module/zookeeper-3.5.7/conf 这个路径下的 zoo_sample.cfg 修改为 zoo.cfg；
 	mv zoo_sample.cfg zoo.cfg
打开 zoo.cfg 文件，修改 dataDir 路径：
	dataDir=/opt/module/zookeeper-3.5.7/zkData
在/opt/module/zookeeper-3.5.7/这个目录上创建 zkData 文件夹
	mkdir zkData
```

**操作 Zookeeper**

```
启动 Zookeeper
 	bin/zkServer.sh start
查看进程是否启动
	jps
查看状态
	 bin/zkServer.sh status
启动客户端
	 bin/zkCli.sh
退出客户端
	 quit
停止 Zookeeper
	bin/zkServer.sh stop
```

**配置参数解读**

**tickTime = 2000：通信心跳时间，Zookeeper服务器与客户端心跳时间，单位毫秒**

![image-20211209231337769](D:/mdimgs/image-20211209231337769.png)

**initLimit = 10：LF初始通信时限**

Leader和Follower初始连接时能容忍的最多心跳数（tickTime的数量）

**syncLimit = 5：LF同步通信时限**

Leader和Follower之间通信时间如果超过syncLimit * tickTime，Leader认为Follwer死 掉，从服务器列表中删除Follwer。

**dataDir：保存Zookeeper中的数据**

注意：默认的tmp目录，容易被Linux系统定期删除，所以一般不用默认的tmp目录。

**clientPort = 2181：客户端连接端口，通常不做修改。**

## Zookeeper 集群操作

**集群安装**

在 hadoop102、hadoop103 和 hadoop104 三个节点上都部署 Zookeeper。

**解压安装**

```
 tar -zxvf apache-zookeeper-3.5.7-bin.tar.gz -C /opt/module/
```

**配置服务器编号**

```
在/opt/module/zookeeper-3.5.7/这个目录下创建 zkData
	mkdir zkData
在/opt/module/zookeeper-3.5.7/zkData 目录下创建一个 myid 的文件
	vi myid
在文件中添加与 server 对应的编号（注意：上下不要有空行，左右不要有空格）
	2
注意：添加 myid 文件，一定要在 Linux 里面创建，在 notepad++里面很可能乱码
拷贝配置好的 zookeeper 到其他机器上
	xsync zookeeper-3.5.7
并分别在 hadoop103、hadoop104 上修改 myid 文件中内容为 3、4
```

**配置zoo.cfg文件**

```
重命名/opt/module/zookeeper-3.5.7/conf 这个目录下的 zoo_sample.cfg 为 zoo.cfg
	mv zoo_sample.cfg zoo.cfg
打开 zoo.cfg 文件
	vim zoo.cfg
修改数据存储路径配置
	dataDir=/opt/module/zookeeper-3.5.7/zkData
#增加如下配置
	#######################cluster##########################
    server.2=hadoop102:2888:3888
    server.3=hadoop103:2888:3888
    server.4=hadoop104:2888:3888
配置参数解读
	server.A=B:C:D。
    A 是一个数字，表示这个是第几号服务器；
	集群模式下配置一个文件 myid，这个文件在 dataDir 目录下，这个文件里面有一个数据就是 A 的值，Zookeeper 启动时读取此文件，     拿到里面的数据与 zoo.cfg 里面的配置信息比较从而判断到底是哪个 server。
	B 是这个服务器的地址；
	C 是这个服务器 Follower 与集群中的 Leader 服务器交换信息的端口；
	D 是万一集群中的 Leader 服务器挂了，需要一个端口来重新进行选举，选出一个新的Leader，而这个端口就是用来执行选举时服务器相互     通信的端口。
同步 zoo.cfg 配置文件
	xsync zoo.cfg
```

**集群操作**

```
分别启动 Zookeeper
	bin/zkServer.sh start
查看状态
	 bin/zkServer.sh status
```

**ZK 集群启动停止脚本**

**在 hadoop102 的/home/atguigu/bin 目录下创建脚本**

```
vim zk.sh
```

**在脚本中编写如下内容**

```
#!/bin/bash
case $1 in
"start"){
for i in hadoop102 hadoop103 hadoop104
do
 echo ---------- zookeeper $i 启动 ------------
ssh $i "/opt/module/zookeeper-3.5.7/bin/zkServer.sh 
start"
done
};;
"stop"){
for i in hadoop102 hadoop103 hadoop104
do
 echo ---------- zookeeper $i 停止 ------------ 
ssh $i "/opt/module/zookeeper-3.5.7/bin/zkServer.sh 
stop"
done
};;
"status"){
for i in hadoop102 hadoop103 hadoop104
do
 echo ---------- zookeeper $i 状态 ------------ 
ssh $i "/opt/module/zookeeper-3.5.7/bin/zkServer.sh 
status"
done
};;
esac
```

**增加脚本执行权限**

```
chmod u+x zk.sh
```

**Zookeeper 集群启动脚本**

```
 zk.sh start
```

**Zookeeper 集群停止脚本**

```
zk.sh stop
```

## **选举机制（面试重点）**

**SID**：服务器ID。用来唯一标识一台 ZooKeeper集群中的机器，每台机器不能重 复，和myid一致。

**ZXID**：事务ID。ZXID是一个事务ID，用来 标识一次服务器状态的变更。在某一时刻， 集群中的每台机器的ZXID值不一定完全一 致，这和ZooKeeper服务器对于客户端“更 新请求”的处理逻辑有关。 

**Epoch**：每个Leader任期的代号。没有 Leader时同一轮投票过程中的逻辑时钟值是 相同的。每投完一次票这个数据就会增加

**Zookeeper选举机制——第一次启动**

```
（1）服务器1启 动，发起一次选举。服务器1投自己一票。此时服务器1票数一票，不够半数以上（3票），选举无法完成，服务器1状态保持为
LOOKING；
（2）服务器2启动，再发起一次选举。服务器1和2分别投自己一票并交换选票信息：此时服务器1发现服务器2的myid比自己目前投票推举的（服务器1）大，更改选票为推举服务器2。此时服务器1票数0票，服务器2票数2票，没有半数以上结果，选举无法完成，服务器1，2状态保持LOOKING
（3）服务器3启动，发起一次选举。此时服务器1和2都会更改选票为服务器3。此次投票结果：服务器1为0票，服务器2为0票，服务器3为3票。此时服务器3的票数已经超过半数，服务器3当选Leader。服务器1，2更改状态为FOLLOWING，服务器3更改状态为LEADING；
（4）服务器4启动，发起一次选举。此时服务器1，2，3已经不是LOOKING状态，不会更改选票信息。交换选票信息结果：服务器3为3票，服务器4为1票。此时服务器4服从多数，更改选票信息为服务器3，并更改状态为FOLLOWING；
（5）服务器5启动，同4一样当小弟。
```

**Zookeeper选举机制——非第一次启动**

```
（1）当ZooKeeper集群中的一台服务器出现以下两种情况之一时，就会开始进入Leader选举：
• 服务器初始化启动。
• 服务器运行期间无法和Leader保持连接。
（2）而当一台机器进入Leader选举流程时，当前集群也可能会处于以下两种状态：
• 集群中本来就已经存在一个Leader。
对于第一种已经存在Leader的情况，机器试图去选举Leader时，会被告知当前服务器的Leader信息，对于该机器来说，仅仅需要和Leader机器建立连接，并进行状态同步即可。
• 集群中确实不存在Leader。
假设ZooKeeper由5台服务器组成，SID分别为1、2、3、4、5，ZXID分别为8、8、8、7、7，并且此时SID为3的服务器是Leader。某一时刻，
3和5服务器出现故障，因此开始进行Leader选举。
					（EPOCH，ZXID，SID ）	（EPOCH，ZXID，SID） 		（EPOCH，ZXID，SID ）
SID为1、2、4的机器投票情况： （1，8，1） 			（1，8，2） 					（1，7，4）
选举Leader规则： ①EPOCH大的直接胜出 ②EPOCH相同，事务id大的胜出 ③事务id相同，服务器id大的胜出
```

## 客户端命令行操作

```
help 		显示所有操作命令
ls path 	使用 ls 命令来查看当前 znode 的子节点 [可监听]
					-w 监听子节点变化
					-s 附加次级信息
create 		普通创建
				-s 含有序列
				-e 临时（重启或者超时消失）
get path 		获得节点的值 [可监听]
					-w 监听节点内容变化
					-s 附加次级信息
set 		设置节点的具体值
stat 		查看节点状态
delete 		删除节点
deleteall 		递归删除节点
```

**znode 节点数据信息**

```
ls /		查看当前znode中所包含的内容
ls -s /		查看当前节点详细数据

[zookeeper]cZxid = 0x0
ctime = Thu Jan 01 08:00:00 CST 1970
mZxid = 0x0
mtime = Thu Jan 01 08:00:00 CST 1970
pZxid = 0x0
cversion = -1
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 0
numChildren = 1

（1）czxid：创建节点的事务 zxid
每次修改 ZooKeeper 状态都会产生一个 ZooKeeper 事务 ID。事务 ID 是 ZooKeeper 中所
有修改总的次序。每次修改都有唯一的 zxid，如果 zxid1 小于 zxid2，那么 zxid1 在 zxid2 之
前发生。
（2）ctime：znode 被创建的毫秒数（从 1970 年开始）
（3）mzxid：znode 最后更新的事务 zxid
（4）mtime：znode 最后修改的毫秒数（从 1970 年开始）
（5）pZxid：znode 最后更新的子节点 zxid
（6）cversion：znode 子节点变化号，znode 子节点修改次数
（7）dataversion：znode 数据变化号
（8）aclVersion：znode 访问控制列表的变化号
（9）ephemeralOwner：如果是临时节点，这个是 znode 拥有者的 session id。如果不是
临时节点则是 0。
（10）dataLength：znode 的数据长度
（11）numChildren：znode 子节点数量
```

**监听器原理**

1）首先要有一个main()线程 

2）在main线程中创建Zookeeper客户端，这时就会创建两个线 程，一个负责网络连接通信（connet），一个负责监听（listener）。 3）通过connect线程将注册的监听事件发送给Zookeeper。

 4）在Zookeeper的注册监听器列表中将注册的监听事件添加到列表中。 

5）Zookeeper监听到有数据或路径变化，就会将这个消息发送给listener线程。

6）listener线程内部调用了process()方法。

![image-20211211125001681](D:/mdimgs/image-20211211125001681.png)

###  客户端 API 操作

**pom**

```xml
<dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-core</artifactId>
            <version>2.8.2</version>

        </dependency>
        <dependency>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
            <version>3.6.3</version>
        </dependency>
    </dependencies>
```

**初始化并且负责监听节点**

```java
 // 注意：逗号左右不能有空格
//ip+端口
    private String connectString = "hadoop102:2181,hadoop103:2181,hadoop104:2181";
//该参数要大于自己配置的参数
    private int sessionTimeout = 35000;
    private ZooKeeper zkClient;

    @Before
    public void init() throws IOException {

        zkClient = new ZooKeeper(connectString, sessionTimeout, new Watcher() {
            @Override
            public void process(WatchedEvent watchedEvent) {
        });
    }

    }
```

**创建一个新的节点**

```java
public void create() throws KeeperException, InterruptedException {
        String nodeCreated = zkClient.create("/wcl. ", "123".getBytes(), 			 	 ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);}
```

**获取子节点并且监听其变化**

```java
public void getChildren() throws KeeperException, InterruptedException {
    List<String> children = zkClient.getChildren("/", true);

    for (String child : children) {
        System.out.println(child);
    }

    // 延时
    Thread.sleep(Long.MAX_VALUE);
}
```

**注册一次生效一次，还需要在进行注册**

```java
zkClient = new ZooKeeper(connectString, sessionTimeout, new Watcher() {
    @Override
    public void process(WatchedEvent watchedEvent) {

        System.out.println("-------------------------------");
        List<String> children = null;
        try {
            children = zkClient.getChildren("/", true);

            for (String child : children) {
                System.out.println(child);
            }

            System.out.println("-------------------------------");
        } catch (KeeperException e) {
            e.printStackTrace();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
});
```

**判断节点是否存在**

```java
@Test
public void exist() throws KeeperException, InterruptedException {

    Stat stat = zkClient.exists("/wcl", false);

    System.out.println(stat==null? "not exist " : "exist");
}
```

### 服务器动态上下线监听

1. 服务器上线的时候其实就是服务器启动时去注册信息（创建的都是临时节点）
2. 客户端获取到当前在线的服务器列表后
3. 服务器节点下线后给集群管理
4. 集群管理服务器节点的下件时间通知给客户端
5. 客户端通过获取服务器列表重选选择服务器

**服务器代码**

获取zookeeper集群的连接，通过zookeeper的构造函数ZooKeeper(connectString, sessionTimeout, new Watcher(){})
将其服务注册到zookeeper集群中，具体通过create的函数，通过获取每个服务器名字、其值、权限、节点类型
执行该函数通过延迟函数

```java
public class DistributeServer {

    private String connectString = "hadoop102:2181,hadoop103:2181,hadoop104:2181";
    private int sessionTimeout = 2000;
    private ZooKeeper zk;

    public static void main(String[] args) throws IOException, KeeperException, InterruptedException {

        DistributeServer server = new DistributeServer();
        // 1 获取zk连接
        server.getConnect();

        // 2 注册服务器到zk集群
        server.regist(args[0]);


        // 3 启动业务逻辑（睡觉）
        server.business();

    }

    private void business() throws InterruptedException {
        Thread.sleep(Long.MAX_VALUE);
    }

    private void regist(String hostname) throws KeeperException, InterruptedException {
        String create = zk.create("/servers/"+hostname, hostname.getBytes(), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.EPHEMERAL_SEQUENTIAL);

        System.out.println(hostname +" is online") ;
    }

    private void getConnect() throws IOException {

        zk = new ZooKeeper(connectString, sessionTimeout, new Watcher() {
            @Override
            public void process(WatchedEvent watchedEvent) {

            }
        });
    }
}

```

**客户端代码**

获取zookeeper集群的连接，通过zookeeper的构造函数ZooKeeper(connectString, sessionTimeout, new Watcher(){})
客户端通过监听每个节点，具体监听通过getChildren函数，获取其节点位置，以及是否使用初始化的监听函数，true为使用。获取到的都是以列表存在，输出的时候通过遍历实现，输出的还是一些数组格式。将这些数组都封装到一个列表中，最后统一输出列表即可
执行该函数通过延迟函数

```java
public class DistributeClient {

    private String connectString = "hadoop102:2181,hadoop103:2181,hadoop104:2181";
    private int sessionTimeout = 2000;
    private ZooKeeper zk;

    public static void main(String[] args) throws IOException, KeeperException, InterruptedException {
        DistributeClient client = new DistributeClient();

        // 1 获取zk连接
        client.getConnect();

        // 2 监听/servers下面子节点的增加和删除
        client.getServerList();

        // 3 业务逻辑（睡觉）
        client.business();

    }

    private void business() throws InterruptedException {
        Thread.sleep(Long.MAX_VALUE);
    }

    private void getServerList() throws KeeperException, InterruptedException {
        List<String> children = zk.getChildren("/servers", true);

        ArrayList<String> servers = new ArrayList<>();

        for (String child : children) {

            byte[] data = zk.getData("/servers/" + child, false, null);

            servers.add(new String(data));
        }

        // 打印
        System.out.println(servers);
    }

    private void getConnect() throws IOException {
        zk = new ZooKeeper(connectString, sessionTimeout, new Watcher() {
            @Override
            public void process(WatchedEvent watchedEvent) {

                try {
                    getServerList();
                } catch (KeeperException e) {
                    e.printStackTrace();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
    }
}

```

### 分布式锁

创建节点，判断是否是最小的节点
如果不是最小的节点，需要监听前一个的节点

健壮性可以通过CountDownLatch类

**监听函数**

如果集群状态是连接，则释放connectlatch
如果集群类型是删除，且前一个节点的位置等于该节点的文职，则释放该节点

判断节点是否存在不用一直监听
获取节点信息要一直监听`getData`

```java
public class DistributedLock {

    private final String connectString = "hadoop102:2181,hadoop103:2181,hadoop104:2181";
    private final int sessionTimeout = 2000;
    private final ZooKeeper zk;

    private CountDownLatch connectLatch = new CountDownLatch(1);
    private CountDownLatch waitLatch = new CountDownLatch(1);

    private String waitPath;
    private String currentMode;

    public DistributedLock() throws IOException, InterruptedException, KeeperException {

        // 获取连接
        zk = new ZooKeeper(connectString, sessionTimeout, new Watcher() {
            @Override
            public void process(WatchedEvent watchedEvent) {
                // connectLatch  如果连接上zk  可以释放
                if (watchedEvent.getState() == Event.KeeperState.SyncConnected){
                    connectLatch.countDown();
                }

                // waitLatch  需要释放
                if (watchedEvent.getType()== Event.EventType.NodeDeleted && watchedEvent.getPath().equals(waitPath)){
                    waitLatch.countDown();
                }
            }
        });

        // 等待zk正常连接后，往下走程序
        connectLatch.await();

        // 判断根节点/locks是否存在
        Stat stat = zk.exists("/locks", false);

        if (stat == null) {
            // 创建一下根节点
            zk.create("/locks", "locks".getBytes(), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
        }
    }

    // 对zk加锁
    public void zklock() {
        // 创建对应的临时带序号节点
        try {
            currentMode = zk.create("/locks/" + "seq-", null, ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.EPHEMERAL_SEQUENTIAL);

            // wait一小会, 让结果更清晰一些
            Thread.sleep(10);

            // 判断创建的节点是否是最小的序号节点，如果是获取到锁；如果不是，监听他序号前一个节点

            List<String> children = zk.getChildren("/locks", false);

            // 如果children 只有一个值，那就直接获取锁； 如果有多个节点，需要判断，谁最小
            if (children.size() == 1) {
                return;
            } else {
                Collections.sort(children);

                // 获取节点名称 seq-00000000
                String thisNode = currentMode.substring("/locks/".length());
                // 通过seq-00000000获取该节点在children集合的位置
                int index = children.indexOf(thisNode);

                // 判断
                if (index == -1) {
                    System.out.println("数据异常");
                } else if (index == 0) {
                    // 就一个节点，可以获取锁了
                    return;
                } else {
                    // 需要监听  他前一个节点变化
                    waitPath = "/locks/" + children.get(index - 1);
                    zk.getData(waitPath,true,new Stat());

                    // 等待监听
                    waitLatch.await();

                    return;
                }
            }


        } catch (KeeperException e) {
            e.printStackTrace();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }


    }

    // 解锁
    public void unZkLock() {

        // 删除节点
        try {
            zk.delete(this.currentMode,-1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (KeeperException e) {
            e.printStackTrace();
        }

    }

}
```

**测试函数**

```java
public class DistributedLockTest {

    public static void main(String[] args) throws InterruptedException, IOException, KeeperException {

       final  DistributedLock lock1 = new DistributedLock();

        final  DistributedLock lock2 = new DistributedLock();

       new Thread(new Runnable() {
           @Override
           public void run() {
               try {
                   lock1.zklock();
                   System.out.println("线程1 启动，获取到锁");
                   Thread.sleep(5 * 1000);

                   lock1.unZkLock();
                   System.out.println("线程1 释放锁");
               } catch (InterruptedException e) {
                   e.printStackTrace();
               }
           }
       }).start();

        new Thread(new Runnable() {
            @Override
            public void run() {

                try {
                    lock2.zklock();
                    System.out.println("线程2 启动，获取到锁");
                    Thread.sleep(5 * 1000);

                    lock2.unZkLock();
                    System.out.println("线程2 释放锁");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }).start();

    }
}

```

### **curator分布式锁**

（1）会话连接是异步的，需要自己去处理。比如使用CountDownLatch
（2）Watch 需要重复注册，不然就不能生效
（3）开发的复杂性还是比较高的
（4）不支持多节点删除和创建。需要自己去递归

**pom**

```xml
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-framework</artifactId>
    <version>4.3.0</version>
</dependency>
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-recipes</artifactId>
    <version>4.3.0</version>
</dependency>
<dependency>
    <groupId>org.apache.curator</groupId>
    <artifactId>curator-client</artifactId>
    <version>4.3.0</version>
</dependency>

```

**代码**

```java
public class CuratorLockTest {

    public static void main(String[] args) {

        // 创建分布式锁1
        InterProcessMutex lock1 = new InterProcessMutex(getCuratorFramework(), "/locks");

        // 创建分布式锁2
        InterProcessMutex lock2 = new InterProcessMutex(getCuratorFramework(), "/locks");

        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    lock1.acquire();
                    System.out.println("线程1 获取到锁");

                    lock1.acquire();
                    System.out.println("线程1 再次获取到锁");

                    Thread.sleep(5 * 1000);

                    lock1.release();
                    System.out.println("线程1 释放锁");

                    lock1.release();
                    System.out.println("线程1  再次释放锁");

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }).start();

        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    lock2.acquire();
                    System.out.println("线程2 获取到锁");

                    lock2.acquire();
                    System.out.println("线程2 再次获取到锁");

                    Thread.sleep(5 * 1000);

                    lock2.release();
                    System.out.println("线程2 释放锁");

                    lock2.release();
                    System.out.println("线程2  再次释放锁");

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }

    private static CuratorFramework getCuratorFramework() {

        ExponentialBackoffRetry policy = new ExponentialBackoffRetry(3000, 3);

        CuratorFramework client = CuratorFrameworkFactory.builder().connectString("hadoop102:2181,hadoop103:2181,hadoop104:2181")
                .connectionTimeoutMs(2000)
                .sessionTimeoutMs(2000)
                .retryPolicy(policy).build();

        // 启动客户端
        client.start();

        System.out.println("zookeeper 启动成功");
        return client;
    }
}

```

