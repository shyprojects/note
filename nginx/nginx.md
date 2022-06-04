# Nginx简介

* 什么是nginx：

  Nginx 是高性能的 HTTP 和反向代理的服务器，处理高并发能力是十分强大的，能经受高负

  载的考验,有报告表明能支持高达 50,000 个并发连接数。

# 反向代理

## 正向代理

需要在客户端配置代理服务器进行指定网站访问

![image-20220530170531579](nginx.assets/image-20220530170531579.png)

## 反向代理

暴露的是代理服务器地址，隐藏了真实服务器IP地址。

![image-20220530170605710](nginx.assets/image-20220530170605710.png)

# 负载均衡

增加服务器的数量，然后将请求分发到各个服务器上，将原先请求集中到单个服务器上的

情况改为将请求分发到多个服务器上，将负载分发到不同的服务器，也就是我们所说的负

载均衡

![image-20220530170801170](nginx.assets/image-20220530170801170.png)

# 动静分离

![image-20220530170824547](nginx.assets/image-20220530170824547.png)

# linux环境下安装Nginx

* 在安装之前需要的几个依赖：

```
c语言编译器gcc：
yum install -y gcc

安装perl库
yum install -y pcre pcre-devel

安装zlib库
yum install -y zlib zlib-devel
```

* 解压缩文件 -zxvf ...
* 进入解压后的目录

* ./configure --prefix=/usr/local/nginx
* make
* make install
* 进入安装好的目录/usr/local/nginx/sbin
* 启动

```
./nginx -v 查看nginx版本号
./nginx 启动
./nginx -s stop 快速停止
./nginx -s quit 优雅关闭，在退出前完成已经接受的连接请求
./nginx -s reload 重新加载配置
```

* 查看地址端口 ip addr

# Nginx的配置文件

* 位置在nginx/conf/nginx.conf

* 内容可以分为三部分：

  * 全局块：配置服务器整体运行的配置指令

    * worker_processes：处理并发数的配置，值越大，可处理的数量就越多

  * events块：影响 Nginx服务器与用户的网络连接

    * worker_connections：设置最大的连接数

  * http块：这算是 Nginx 服务器配置中最频繁的部分，代理、缓存和日志定义等绝大多数功能和第三方模块的配置都在这里，其中包括括http 全局块**、**server 块。

    * http 全局块：http 全局块配置的指令包括文件引入、MIME-TYPE 定义、日志自定义、连接超时时间、单链接请求数上限等

    * server块：这块和虚拟主机有密切关系，虚拟主机从用户角度看，和一台独立的硬件主机是完全一样的，该技术的产生是为了节省互联网服务器硬件成本。

       每个 http 块可以包括多个 server 块，而每个 server 块就相当于一个虚拟主机。

       而每个 server 块也分为全局 server 块，以及可以同时包含多个 locaton 块。

      * 全局 server 块

         最常见的配置是本虚拟机主机的监听配置和本虚拟主机的名称或 IP 配置。

      * **location 块**

         一个 server 块可以配置多个 location 块。

         这块的主要作用是基于 Nginx 服务器接收到的请求字符串（例如 server_name/uri-string），对虚拟主机名称

        （也可以是 IP 别名）之外的字符串（例如 前面的 /uri-string）进行匹配，对特定的请求进行处理。地址定向、数据缓

        存和应答控制等功能，还有许多第三方模块的配置也在这里进行

# 反向代理实现

## 反向代理访问一个tomcat服务器

* 实现效果：在本机上通过域名www.123.com访问虚拟机中的linux系统的80端口，可以响应linux中的tomcat服务器

  ![image-20220530203345987](nginx.assets/image-20220530203345987.png)

* 配置域名与地址的映射

![image-20220530203551674](nginx.assets/image-20220530203551674.png)

![image-20220530203725122](nginx.assets/image-20220530203725122.png)

* 在linux中启动tomcat服务器
* 在nginx进行请求转发的配置（反向代理）

![image-20220530205003403](nginx.assets/image-20220530205003403.png)

* 启动nginx并且测试

## 反向代理访问两个tomcat服务器

* 启动两个tomcat服务器端口号分别为8080和8081
* 在webapps下创建index1/index.html  index1/index.html内容分别为一行8080与8081
* 在nginx中进行配置

```
    server {
        listen       9001;
        server_name  192.168.239.128;

        location ~ /index1/ {
            proxy_pass http://127.0.0.1:8080;
        }
        location ~ /index2/ {
            proxy_pass http://127.0.0.1:8081;
        }
    }
```

* 启动nginx并且进行测试

# 负载均衡实现

* 在上述过程中配置nginx时添加

```
http{
...
	upstream myserver{
		ip_hash;
		server 192.168....:8080 weight=1;
		server 192.168....:8081 weight=1;
		fair;
	}
...
	server{
	...
		location / {
			proxy_pass http://myserver;
		}
	}
}
```

* 两个index.html的路径应该相同

![image-20220531170917850](nginx.assets/image-20220531170917850.png)

* 多次访问返回的页面既有8080也有8081

## 负载均衡策略

* 轮询（默认策略）
  * 每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除
* weight
  * weight代表权重，权重默认为1，权重越高，分配的客户端越多
* ip_hash
  * 每个请求按照访问ip的hash结果分配，这样每个访客固定一个后端服务器，可以解决session的问题
* fair
  * 按照后端服务器的响应时间来分配请求，响应时间短的优先分配