# 简介

## 正向代理

```
Nginx 不仅可以做反向代理，实现负载均衡。还能用作正向代理来进行上网等功能。 正向代理：如果把局域网外的 Internet 想象成一个巨大的资源库，则局域网中的客户端要访 问 Internet，则需要通过代理服务器来访问，这种代理服务就称为正向代理。
```

![image-20211114194211972](D:/mdimgs/image-20211114194211972.png)

## 反向代理

```
反向代理，其实客户端对代理是无感知的，因为客户端不需要任何配置就可以访问，我们只 需要将请求发送到反向代理服务器，由反向代理服务器去选择目标服务器获取数据后，在返 回给客户端，此时反向代理服务器和目标服务器对外就是一个服务器，暴露的是代理服务器 地址，隐藏了真实服务器 IP 地址。
```

![image-20211114194306665](D:/mdimgs/image-20211114194306665.png)

## 负载均衡

```
    客户端发送多个请求到服务器，服务器处理请求，有一些可能要与数据库进行交互，服务器处理完毕后，再将结果返回给客户端。
     这种架构模式对于早期的系统相对单一，并发请求相对较少的情况下是比较适合的，成本也低。但是随着信息数量的不断增长，访问量和数据量的飞速增长，以及系统业务的复杂度增加，这种架构会造成服务器相应客户端的请求日益缓慢，并发量特别大的时候，还容易造成服务器直接崩溃。很明显这是由于服务器性能的瓶颈造成的问题，那么如何解决这种情
况呢？
 	我们首先想到的可能是升级服务器的配置，比如提高 CPU 执行频率，加大内存等提高机器的物理性能来解决此问题，但是我们知道摩尔定律的日益失效，硬件的性能提升已经不能满足日益提升的需求了。最明显的一个例子，天猫双十一当天，某个热销商品的瞬时访问量是极其庞大的，那么类似上面的系统架构，将机器都增加到现有的顶级物理配置，都是不能够满足需求的。那么怎么办呢？
 	上面的分析我们去掉了增加服务器物理配置来解决问题的办法，也就是说纵向解决问题的办法行不通了，那么横向增加服务器的数量呢？这时候集群的概念产生了，单个服务器解决不了，我们增加服务器的数量，然后将请求分发到各个服务器上，将原先请求集中到单个服务器上的情况改为将请求分发到多个服务器上，将负载分发到不同的服务器，也就是我们所说的负载均衡。
```

![image-20211114194542917](D:/mdimgs/image-20211114194542917.png)

## 动静分离

```
为了加快网站的解析速度，可以把动态页面和静态页面由不同的服务器来解析，加快解析速度。降低原来单个服务器的压力。
```

![image-20211114195248744](D:/mdimgs/image-20211114195248744.png)

# 安装

```
安装依赖
yum -y install make zlib zlib-devel gcc-c++ libtool openssl openssl-devel
1、 解压缩 nginx-xx.tar.gz 包。
2、 进入解压缩目录，执行./configure。
3、 make && make install

查看开放的端口号
firewall-cmd --list-all

设置开放的端口号
firewall-cmd --add-service=http –permanent
sudo firewall-cmd --add-port=80/tcp --permanent

重启防火墙
firewall-cmd –reload

```



# 命令和配置文件

```nginx
启动
在/usr/local/nginx/sbin 目录下执行 ./nginx 

关闭命令
在/usr/local/nginx/sbin 目录下执行 ./nginx -s stop

重新加载命令
在/usr/local/nginx/sbin 目录下执行 ./nginx -s reload

```

### nginx.conf 配置文件

```nginx
位置:/usr/local/nginx/conf/nginx.conf

全局块
    worker_processes  1;
    这是 Nginx 服务器并发处理服务的关键配置，worker_processes 值越大，可以支持的并发处理量也越多，但是会受到硬件、软件等设备的制约

事件块
    events {
        worker_connections  1024;
    }

    events 块涉及的指令主要影响 Nginx 服务器与用户的网络连接，常用的设置包括是否开启对多 work process下的网络连接进行序列化，是否允许同时接收多个网络连接，选取哪种事件驱动模型来处理连接请求，每个 word process 可以同时支持的最大连接数等。上述例子就表示每个 work process 支持的最大连接数为 1024.这部分的配置对 Nginx 的性能影响较大，在实际中应该灵活配置。


http块
    http {
        include       mime.types;
        default_type  application/octet-stream;

        #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
        #                  '$status $body_bytes_sent "$http_referer" '
        #                  '"$http_user_agent" "$http_x_forwarded_for"';

        #access_log  logs/access.log  main;

        sendfile        on;
        #tcp_nopush     on;

        #keepalive_timeout  0;
        keepalive_timeout  65;

        #gzip  on;

        server {
            listen       80;
            server_name  localhost;

            #charset koi8-r;

            #access_log  logs/host.access.log  main;

            location / {
                root   html;
                index  index.html index.htm;
            }
                    #error_page  404              /404.html;

            # redirect server error pages to the static page /50x.html
            #
            error_page   500 502 503 504  /50x.html;
            location = /50x.html {
                root   html;
            }

            # proxy the PHP scripts to Apache listening on 127.0.0.1:80
            #
            #location ~ \.php$ {
            #    proxy_pass   http://127.0.0.1;
            #}

            # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
            #
            #location ~ \.php$ {
            #    root           html;
            #    fastcgi_pass   127.0.0.1:9000;
            #    fastcgi_index  index.php;
            #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
            #    include        fastcgi_params;
            #}

            # deny access to .htaccess files, if Apache's document root
            # concurs with nginx's one
            #
            #location ~ /\.ht {
            #    deny  all;
            #}
        }
    }


http 块也可以包括 http 全局块、server 块。
http 全局块配置的指令包括文件引入、MIME-TYPE 定义、日志自定义、连接超时时间、单链接请求数上限等。
server块和虚拟主机有密切关系，虚拟主机从用户角度看，和一台独立的硬件主机是完全一样的，该技术的产生是为了节省互联网服务器硬件成本。
每个 http 块可以包括多个 server 块，而每个 server 块就相当于一个虚拟主机。
而每个 server 块也分为全局 server 块，以及可以同时包含多个 locaton 块。
1、全局 server 块
 最常见的配置是本虚拟机主机的监听配置和本虚拟主机的名称或 IP 配置。
2、location 块
一个 server 块可以配置多个 location 块。
这块的主要作用是基于 Nginx 服务器接收到的请求字符串（例如 server_name/uri-string），对虚拟主机名称（也可以是 IP 别名）之外的字符串（例如 前面的 /uri-string）进行匹配，对特定的请求进行处理。地址定向、数据缓存和应答控制等功能，还有许多第三方模块的配置也在这里进行。

```

# 原理与优化参数配置

![image-20211114231803084](D:/mdimgs/image-20211114231803084.png)

```
	master-workers 的机制的好处 
    	首先，对于每个 worker 进程来说，独立的进程，不需要加锁，所以省掉了锁带来的开销，同时在编程以及问题查找时，也会方便很    		  多。其次，采用独立的进程，可以让互相之间不会影响，一个进程退出后，其它进程还在工作，服务不会中断，master 进程则很快启动		 新的worker 进程。当然，worker 进程的异常退出，肯定是程序有 bug 了，异常退出，会导致当前 worker 上的所有请求失败，不		过不会影响到所有请求，所以降低了风险。
    需要设置多少个 worker
         Nginx 同 redis 类似都采用了 io 多路复用机制，每个 worker 都是一个独立的进程，但每个进程里只有一个主线程，通过异步          阻塞的方式来处理请求， 即使是千上万个请求也不在话下。每个 worker 的线程可以把一个 cpu 的性能发挥到极致。所以worker          数和服务器的 cpu数相等是最为适宜的。设少了会浪费 cpu，设多了会造成 cpu 频繁切换上下文带来的损耗。
    #设置 worker 数量。
        worker_processes 4
        #work 绑定 cpu(4 work 绑定 4cpu)。
        worker_cpu_affinity 0001 0010 0100 1000
        #work 绑定 cpu (4 work 绑定 8cpu 中的 4 个) 。
        worker_cpu_affinity 0000001 00000010 00000100 00001000
	连接数 worker_connection
    	这个值是表示每个 worker 进程所能建立连接的最大值，所以，一个 nginx 能建立的最大连接数，应该是							worker_connections*worker_processes。当然，这里说的是最大连接数，对于HTTP 请 求 本 地 资 源 来 说 ， 能 够 支 		 持的 最 大 并 发 数 量 是 worker_connections * worker_processes，如果是支持 http1.1 的浏览器每次访问要占两个连		   接，所以普通的静态访问最大并发数是： worker_connections * worker_processes /2，而如果是 HTTP 作 为反向代理来		 说，最大并发数量应该是worker_connections * worker_processes/4。因为作为反向代理服务器，每个并发会建立与客户端的连		 接和与后端服务的连接，会占用两个连接。
```

![image-20211114231818376](D:/mdimgs/image-20211114231818376.png)

# 实例

## 反向代理实例

```
location匹配符
     1、= ：用于不含正则表达式的 uri 前，要求请求字符串与 uri 严格匹配，如果匹配
    成功，就停止继续向下搜索并立即处理该请求。
     2、~：用于表示 uri 包含正则表达式，并且区分大小写。
     3、~*：用于表示 uri 包含正则表达式，并且不区分大小写。
     4、^~：用于不含正则表达式的 uri 前，要求 Nginx 服务器找到标识 uri 和请求字
    符串匹配度最高的 location 后，立即使用此 location 处理请求，而不再使用 location 
    块中的正则 uri 和请求字符串做匹配。
     注意：如果 uri 包含正则表达式，则必须要有 ~ 或者 ~* 标识。
```

```nginx
实现效果：使用 nginx 反向代理，根据访问的路径跳转到不同端口的服务中
    nginx 监听端口为 9001，
        访问 http://127.0.0.1:9001/edu/ 直接跳转到 127.0.0.1:8081
        访问 http://127.0.0.1:9001/vod/ 直接跳转到 127.0.0.1:8080       
    配置
		http {
        include       mime.types;
        default_type  application/octet-stream;

        #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
        #                  '$status $body_bytes_sent "$http_referer" '
        #                  '"$http_user_agent" "$http_x_forwarded_for"';

        #access_log  logs/access.log  main;

        sendfile        on;
        #tcp_nopush     on;

        #keepalive_timeout  0;
        keepalive_timeout  65;

        #gzip  on;

        server {
        	#监听的本地端口	
            listen       9001;
        	#域名或者ip
            server_name  localhost;

            #charset koi8-r;

            #access_log  logs/host.access.log  main;

            location ~ /vod/ {
                root   html;
                index  index.html index.htm;
            	#反代，向指定ip跳转
            	proxy_pass: http://localhost:8080;
            }
        
          	location ~/edu/ {
                root   html;
                index  index.html index.htm;
            	#反代，向指定ip跳转
            	proxy_pass: http://localhost:8081;
            }
                    #error_page  404              /404.html;

            # redirect server error pages to the static page /50x.html
            #
            error_page   500 502 503 504  /50x.html;
            location = /50x.html {
                root   html;
            }}}
```

## 负载均衡实例

```nginx
配置
    http{
    ...
        upstrean myserver{
            ip_hash;
            server ip weight=1;
            server ip weight=1;
        }
     ...
         server{
            location / {
                ...
                proxy_pass http://myserver;
                proxy_connect_timeout 10;
                ...
            }
            ...
        }
    }

Nginx 提供了几种分配方式(策略)：
    1、轮询（默认）
    	每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器 down 掉，能自动剔除。
    2、weight
        weight 代表权,重默认为 1,权重越高被分配的客户端越多
        指定轮询几率，weight 和访问比率成正比，用于后端服务器性能不均的情况。 
		 upstrean myserver{
            server ip weight=10;
            server ip weight=1;
        }
    3、ip_hash
    	每个请求按访问 ip 的 hash 结果分配，这样每个访客固定访问一个后端服务器，可以解决 session 的问题。 
        upstrean myserver{
                    ip_hash;
                    server ip weight=10;
                    server ip weight=1;
                }
    4、fair（第三方）
    	按后端服务器的响应时间来分配请求，响应时间短的优先分配。
          upstrean myserver{
                            server ip weight=10;
                            server ip weight=1;
    						fair;
                        }
```

## 动静分离实例

```nginx
 server{
            location /www/ {
        		#文件夹相对路径	
       			root /data/;
        		index index.html index.htm;
            }
    	location /image/ {
        		#文件夹相对路径	
       			root /data/;
        		autoindex on;
    }}
```

## nginx 搭建高可用集群

Keepalived+Nginx 高可用集群（主从模式）

![image-20211114223305537](D:/mdimgs/image-20211114223305537.png)

```
配置 /etc/keepalived/keepalived.conf


global_defs { 
     notification_email { 
     acassen@firewall.loc 
     failover@firewall.loc 
     sysadmin@firewall.loc 
     } 
     notification_email_from Alexandre.Cassen@firewall.loc 
     smtp_server 192.168.17.129 
     smtp_connect_timeout 30 
     router_id LVS_DEVEL #访问到主机
    } 
  vrrp_script chk_http_port { 
     script "/usr/local/src/nginx_check.sh" 
     interval 2 #（检测脚本执行的间隔） 
     weight 2} 
 
  vrrp_instance VI_1 { 
     state BACKUP # 备份服务器上将 MASTER 改为 BACKUP 
     interface ens33 //网卡 
     virtual_router_id 51 # 主、备机的 virtual_router_id 必须相同 
     priority 100 # 主、备机取不同的优先级，主机值较大，备份机值较小 
     advert_int 1 
     authentication { 
     auth_type PASS 
     auth_pass 1111 
     } 
   virtual_ipaddress { 
     192.168.17.50 // VRRP H 虚拟地址 
     } 
}
```

