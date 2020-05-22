---
title: Nginx 配置事项总结
tags:
---



## 配置文件介绍

Nginx 最主要的配置文件为 nginx.conf 。Nginx的配置文件一般包括四个部分：

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

## 参考资料

1. [[Nginx 服务器安装及配置文件详解](https://www.cnblogs.com/bluestorm/p/4574688.html)](https://www.cnblogs.com/bluestorm/p/4574688.html)