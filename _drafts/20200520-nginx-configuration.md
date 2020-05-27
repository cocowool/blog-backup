---
title: Nginx 配置事项总结
tags:
---



## Nginx介绍

Nginx是一款轻量级的Web服务器，由于它占有系统资源少、处理并发能力强，因此Nginx的使用率越来越高，甚至超过了传统老牌的Web服务器Apache httpd。

## Nginx几种工作模式

### Web服务器

Nginx最简单的用法就是作为静态网站的服务端，通过HTTP或HTTPS为用户提供HTML、JS、图像等静态资源的服务。

![image-20200526133029186](20200520-nginx-configuration/image-20200526133029186.png)

### 反向代理

多个客户端给服务器发送请求，Nginx收到请求后，按照一定的规则将请求转发给后端负责处理具体业务的服务器，此时Nginx扮演了反向代理的角色。

![image-20200526131522421](20200520-nginx-configuration/image-20200526131522421.png)

### 负载均衡

我认为负载均衡可以理解为在反向代理基础上的功能增强。当我们后端有多个提供同样业务功能的服务端时，我们希望Nginx能够按照一定的规则将请求流量分发到后端，通常来讲我们希望后端每个服务器上的负载保持在同样的水平线上。这就要求Nginx能够提供不同的负载均衡算法以及对后端服务状态的检查，以便在后端服务出现故障时，及时从转发列表中去掉，避免对客户端请求造成更大的影响。

如下图所示，如果用户对于后端Service的请求速度是400tps，因为后端服务器配置不同，根据配置我们希望达到下图中请求数量的分配就可以用到Nginx的负载均衡能力。

![image-20200527080928412](20200520-nginx-configuration/image-20200527080928412.png)

## 配置文件介绍

Nginx 最主要的配置文件为 nginx.conf ，在启动时可以通过命令`nginx -c`指定要采用的配置文件，默认使用的配置文件时nginx安装路径下的`/config/nginx.conf`。Nginx配置文件以`指令 参数;`的形式Nginx的配置文件一般包括四个部分：

* main 全局设置，这里的设置会影响后续所有的配置
* server 主要用于虚拟主机配置，包括主机域名、IP和端口等
* upstream 上游服务器设置，主要包括反向代理、负载均衡相关配置
* location 匹配URL路径的设置

![image-20200521132343516](20200520-nginx-configuration/image-20200521132343516.png)

## 常用配置项说明

### main 全局配置

全局配置是一些与具体业务功能无关的参数，通常与性能或功能有关。

* worker_processes 2 ： 指定worker角色工作的进程个数
* worker_connections 2048 ：设置每个worker进程能并发处理的最大连接数
* Use epoll：nginx提供了集中事件模型，包括epoll、select等，在linux系统中一般采用epoll模型能够提供非常好的性能

### http服务相关的配置

### server虚拟主机相关配置

* listen ：虚拟机主的监听端口，默认为80，端口号小于1024时需要以root用户启动。
* server_name ： 服务器名称，例如 [www.edulinks.cn](https://www.edulinks.cn)

### upstream配置

### location配置

在http服务中，可以针对某些特定的URL指定一系列配置项

* root：定义根目录的位置
* index：定义默认访问的文件名 

## 参考资料

1. [[Nginx 服务器安装及配置文件详解](https://www.cnblogs.com/bluestorm/p/4574688.html)](https://www.cnblogs.com/bluestorm/p/4574688.html)
2. [nginx docs](https://docs.nginx.com/nginx/admin-guide/basic-functionality/runtime-control/)

