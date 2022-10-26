# 三大要素

## 镜像：

```
Docker 镜像（Image）就是一个只读的模板。镜像可以用来创建 Docker 容器，一个镜像可以创建很多容器。
```

### UnionFS（联合文件系统）

```
UnionFS（联合文件系统）：
    Union文件系统（UnionFS）是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下(unite several directories into a single virtual filesystem)。Union 文件系统是 Docker 镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。
特性：
    一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录.
```

### Docker镜像加载原理

```
docker的镜像实际上由一层一层的文件系统组成，这种层级的文件系统UnionFS。

    bootfs(boot file system)主要包含bootloader和kernel, bootloader主要是引导加载kernel, Linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层是bootfs。这一层与我们典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成之后整个内核就都在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。

    rootfs (root file system) ，在bootfs之上。包含的就是典型 Linux 系统中的 /dev, /proc, /bin, /etc 等标准目录和文件。rootfs就是各种不同的操作系统发行版，比如Ubuntu，Centos等等。 

为什么docker会采用这种分层结构
    最大的一个好处就是 - 共享资源
    比如：有多个镜像都从相同的 base 镜像构建而来，那么宿主机只需在磁盘上保存一份base镜像，
    同时内存中也只需加载一份 base 镜像，就可以为所有容器服务了。而且镜像的每一层都可以被共享。
```

![image-20211116211510078](D:/mdimgs/image-20211116211510078.png)

## 容器

```
Docker 利用容器（Container）独立运行的一个或一组应用。容器是用镜像创建的运行实例。

它可以被启动、开始、停止、删除。每个容器都是相互隔离的、保证安全的平台。

可以把容器看做是一个简易版的 Linux 环境（包括root用户权限、进程空间、用户空间和网络空间等）和运行在其中的应用程序。

容器的定义和镜像几乎一模一样，也是一堆层的统一视角，唯一区别在于容器的最上面那一层是可读可写的。
```

### 容器数据卷

```
类比定义：
    有点类似我们Redis里面的rdb和aof文件
特点：
    容器的持久化
    容器间继承+共享数据

容器内添加数据卷
     docker run -it -v /宿主机绝对路径目录:/容器内目录 镜像名
     查看数据卷是否挂载成功 docker inspect 容器ID
     命令(带权限) docker容器只读
     docker run -it -v /宿主机绝对路径目录:/容器内目录:ro 镜像名


DockerFile添加数据卷
    根目录下新建mydocker文件夹并进入
    可在Dockerfile中使用VOLUME指令来给镜像添加一个或多个数据卷
        VOLUME["/dataVolumeContainer","/dataVolumeContainer2","/dataVolumeContainer3"]
        说明：
             出于可移植和分享的考虑，用-v 主机目录:容器目录这种方法不能够直接在Dockerfile中实现。
            由于宿主机目录是依赖于特定宿主机的，并不能够保证在所有的宿主机上都存在这样的特定目录。
    File构建
        # volume test
        FROM centos
        VOLUME ["/dataVolumeContainer1","/dataVolumeContainer2"]
        CMD echo "finished,--------success1"
        CMD /bin/bash
    build后生成镜像
        获得一个新镜像zzyy/centos
    run容器
Docker挂载主机目录Docker访问出现cannot open directory .: Permission denied
    解决办法：在挂载目录后多加一个--privileged=true参数即可

数据卷容器
    命名的容器挂载数据卷，其它容器通过挂载这个(父容器)实现数据共享，挂载数据卷的容器，称之为数据卷容器

    容器间传递共享(--volumes-from)
        先启动一个父容器dc01 在dataVolumeContainer2新增内容
        dc02/dc03继承自dc01 --volumes-from 
            docker run -it --name dc02 --volumes-from dc01 zzyy/centos
            dc02/dc03分别在dataVolumeContainer2各自新增内容
                删除dc01，dc02修改后dc03可否访问
                删除dc02后dc03可否访问
                新建dc04继承dc03后再删除dc03
        结论：容器之间配置信息的传递，数据卷的生命周期一直持续到没有容器使用它为止
```

### DockerFile解析

```
Dockerfile是用来构建Docker镜像的构建文件，是由一系列命令和参数构成的脚本。
构建三步骤
    编写Dockerfile文件
    docker build
    docker run
DockerFile构建过程解析
    Dockerfile内容基础知识
        1：每条保留字指令都必须为大写字母且后面要跟随至少一个参数
        2：指令按照从上到下，顺序执行
        3：#表示注释
        4：每条指令都会创建一个新的镜像层，并对镜像进行提交
    Docker执行Dockerfile的大致流程
        （1）docker从基础镜像运行一个容器
        （2）执行一条指令并对容器作出修改
        （3）执行类似docker commit的操作提交一个新的镜像层
        （4）docker再基于刚提交的镜像运行一个新容器
        （5）执行dockerfile中的下一条指令直到所有指令都执行完成
    总结
        从应用软件的角度来看，Dockerfile、Docker镜像与Docker容器分别代表软件的三个不同阶段，
            *  Dockerfile是软件的原材料
            *  Docker镜像是软件的交付品
            *  Docker容器则可以认为是软件的运行态。
            Dockerfile面向开发，Docker镜像成为交付标准，Docker容器则涉及部署与运维，三者缺一不可，合力充当Docker体系的基            石。
            1 Dockerfile，需要定义一个Dockerfile，Dockerfile定义了进程需要的一切东西。Dockerfile涉及的内容包括执行代码                或者是文件、环境变量、依赖包、运行时环境、动态链接库、操作系统的发行版、服务进程和内核进程(当应用进程需要和系统服                务和内核进程打交道，这时需要考虑如何设计namespace的权限控制)等等;
            2 Docker镜像，在用Dockerfile定义一个文件之后，docker build时会产生一个Docker镜像，当运行 Docker镜像                    时，会真正开始提供服务;
             3 Docker容器，容器是直接提供服务的。
DockerFile体系结构(保留字指令)
    FROM 基础镜像，当前新镜像是基于哪个镜像的
    MAINTAINER 镜像维护者的姓名和邮箱地址
    RUN 容器构建时需要运行的命令
    EXPOSE 当前容器对外暴露出的端口
    WORKDIR 指定在创建容器后，终端默认登陆的进来工作目录，一个落脚点
    ENV 用来在构建镜像过程中设置环境变量
    ADD 将宿主机目录下的文件拷贝进镜像且ADD命令会自动处理URL和解压tar压缩包
    COPY 类似ADD，拷贝文件和目录到镜像中。
         将从构建上下文目录中 <源路径> 的文件/目录复制到新的一层的镜像内的 <目标路径> 位置
    VOLUME 容器数据卷，用于数据保存和持久化工作
    CMD 指定一个容器启动时要运行的命令
        Dockerfile 中可以有多个 CMD 指令，但只有最后一个生效，CMD 会被 docker run 之后的参数替换
    ENTRYPOINT 指定一个容器启动时要运行的命令
                ENTRYPOINT 的目的和 CMD 一样，都是在指定容器启动程序及参数
    ONBUILD 当构建一个被继承的Dockerfile时运行命令，父镜像在被子继承后父镜像的onbuild被触发
```

![image-20211119122204167](D:/mdimgs/image-20211119122204167.png)

```
DockerFile示例
    FROM centos
    MAINTAINER zzyy<zzyy167@126.com>

    ENV MYPATH /usr/local
    WORKDIR $MYPATH

    RUN yum -y install vim
    RUN yum -y install net-tools

    EXPOSE 80

    CMD echo $MYPATH
    CMD echo "success--------------ok"
    CMD /bin/bash




    FROM         centos
    MAINTAINER    zzyy<zzyybs@126.com>
    #把宿主机当前上下文的c.txt拷贝到容器/usr/local/路径下
    COPY c.txt /usr/local/cincontainer.txt
    #把java与tomcat添加到容器中
    ADD jdk-8u171-linux-x64.tar.gz /usr/local/
    ADD apache-tomcat-9.0.8.tar.gz /usr/local/
    #安装vim编辑器
    RUN yum -y install vim
    #设置工作访问时候的WORKDIR路径，登录落脚点
    ENV MYPATH /usr/local
    WORKDIR $MYPATH
    #配置java与tomcat环境变量
    ENV JAVA_HOME /usr/local/jdk1.8.0_171
    ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
    ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.8
    ENV CATALINA_BASE /usr/local/apache-tomcat-9.0.8
    ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin
    #容器运行时监听的端口
    EXPOSE  8080
    #启动时运行tomcat
    # ENTRYPOINT ["/usr/local/apache-tomcat-9.0.8/bin/startup.sh" ]
    # CMD ["/usr/local/apache-tomcat-9.0.8/bin/catalina.sh","run"]
    CMD /usr/local/apache-tomcat-9.0.8/bin/startup.sh && tail -F /usr/local/apache-tomcat-
    9.0.8/bin/logs/catalina.out
```

![image-20211119212140178](D:/mdimgs/image-20211119212140178.png)

## 仓库

```
仓库（Repository）是集中存放镜像文件的场所。
仓库(Repository)和仓库注册服务器（Registry）是有区别的。仓库注册服务器上往往存放着多个仓库，每个仓库中又包含了多个镜像，每个镜像有不同的标签（tag）。

仓库分为公开仓库（Public）和私有仓库（Private）两种形式。
最大的公开仓库是 Docker Hub(https://hub.docker.com/)，
存放了数量庞大的镜像供用户下载。国内的公开仓库包括阿里云 、网易云 等
```

# 安装

```dockerfile
见官网 ：https://docs.docker.com/engine/install/centos/

配置阿里云镜像仓库
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

```
mysql挂载
   docker run -p 12345:3306 --name mysql -v /wcl/mysql/conf:/etc/mysql/conf.d -v     /wcl/mysql/logs:/logs -v /wcl/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7

    命令说明：
    -p 12345:3306：将主机的12345端口映射到docker容器的3306端口。
    --name mysql：运行服务名字
    -v /zzyyuse/mysql/conf:/etc/mysql/conf.d ：将主机/zzyyuse/mysql录下的conf/my.cnf 挂载到容器的 
    /etc/mysql/conf.d
    -v /zzyyuse/mysql/logs:/logs：将主机/zzyyuse/mysql目录下的 logs 目录挂载到容器的 /logs。


redis
    docker run -p 6379:6379 -v /wcl/myredis/data:/data -v /wcl/myredis/conf/redis.conf:/usr/local/etc/redis/redis.conf  -d redis:6.2.4 redis-server /usr/local/etc/redis/redis.conf --appendonly yes
```

# 命令

## 帮助命令

```docker
docker version
docker info
docker --help
```

## 镜像命令

```
docker images [OPTIONS]
REPOSITORY：表示镜像的仓库源 TAG：镜像的标签 IMAGE ID：镜像ID CREATED：镜像创建时间 SIZE：镜像大小
OPTIONS说明：
    -a :列出本地所有的镜像（含中间映像层）
    -q :只显示镜像ID。
    --digests :显示a镜像的摘要信息
    --no-trunc :显示完整的镜像信息

docker search [OPTIONS] 镜像名字
OPTIONS说明：
    --no-trunc : 显示完整的镜像描述
    -f : 条件过滤 docker search -f stars=30 tomcat
    --automated : 只列出 automated build类型的镜像

docker pull 镜像名字[:TAG]

docker rmi 某个XXX镜像名字ID
    docker rmi  -f 镜像ID
    docker rmi -f 镜像名1:TAG 镜像名2:TAG 
    docker rmi -f $(docker images -qa)
```

```
docker commit 提交容器副本使之成为一个新的镜像
docker commit -m=“提交的描述信息” -a=“作者” 容器ID 要创建的目标镜像名:[标签名]
```

## 容器命令

```
启动容器 docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
OPTIONS说明：
    --name="容器新名字": 为容器指定一个名称；
    -d: 后台运行容器，并返回容器ID，也即启动守护式容器；
    -i：以交互模式运行容器，通常与 -t 同时使用；
    -t：为容器重新分配一个伪输入终端，通常与 -i 同时使用；
    -P: 随机端口映射；
    -p: 指定端口映射，有以下四种格式
          ip:hostPort:containerPort
          ip::containerPort
          hostPort:containerPort
          containerPort

列出当前所有正在运行的容器 docker ps [OPTIONS]
OPTIONS说明：
     -a :列出当前所有正在运行的容器+历史上运行过的
    -l :显示最近创建的容器。
    -n：显示最近n个创建的容器。
    -q :静默模式，只显示容器编号。
    --no-trunc :不截断输出。

退出容器 
    容器停止退出 exit
    容器不停止退出 ctrl+P+Q

启动容器 docker start 容器ID或者容器名

重启容器 docker restart 容器ID或者容器名

停止容器 docker stop 容器ID或者容器名

强制停止容器 docker kill 容器ID或者容器名

删除已停止的容器 docker rm 容器ID
    一次性删除多个容器 
        docker rm -f $(docker ps -a -q)
        docker ps -a -q | xargs docker rm
```

```
启动守护式容器 docker run -d 容器名 
问题：然后docker ps -a 进行查看, 会发现容器已经退出
    很重要的要说明的一点: Docker容器后台运行,就必须有一个前台进程.
    容器运行的命令如果不是那些一直挂起的命令（比如运行top，tail），就是会自动退出的。
    这个是docker的机制问题,比如你的web容器,我们以nginx为例，正常情况下,我们配置启动服务只需要启动响应的service即可。例如
    service nginx start
    但是,这样做,nginx为后台进程模式运行,就导致docker前台没有运行的应用,
    这样的容器后台启动后,会立即自杀因为他觉得他没事可做了.
    所以，最佳的解决方案是,将你要运行的程序以前台进程的形式


查看容器日志 docker logs -f -t --tail 容器ID 
    *   -t 是加入时间戳
    *   -f 跟随最新的日志打印
    *   --tail 数字 显示最后多少条

查看容器内运行的进程 docker top 容器ID

查看容器内部细节 docker inspect 容器ID

进入正在运行的容器并以命令行交互 
    重新进入docker attach 容器ID
    docker exec -it 容器ID bashShell
        attach 直接进入容器启动命令的终端，不会启动新的进程
        exec 是在容器中打开新的终端，并且可以启动新的进程


从容器内拷贝文件到主机上
    docker cp  容器ID:容器内路径 目的主机路径
```
