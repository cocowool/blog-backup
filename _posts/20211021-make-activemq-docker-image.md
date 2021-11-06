---
title: 构建ActiveMQ镜像并运行
date: 2021-10-21 14:12:40
keyrowds: docker, ActiveMQ
description: 自己构建一个 ActiveMQ 的镜像并运行服务。
---

## ActiveMQ 介绍

Apache ActiveMQ是Apache软件基金会所研发的开放源代码消息中间件；由于ActiveMQ是一个纯Java程序，因此只需要操作系统支持Java虚拟机，ActiveMQ便可执行。

## 构建ActiveMQ镜像

镜像构建的总体思路是首先引入一个基础的Linux环境，然后添加 jdk 运行环境，继续添加 activemq 软件，最后做一些目录、环境变量、端口的配置，在最后启动应用，具体的 Dockerfile 如下。

```dockerfile
FROM centos

MAINTAINER cocowool@qq.com

ADD jdk-8u144-linux-x64.tar.gz /usr/local
ADD apache-activemq-5.16.3-bin.tar.gz /usr/local
# ADD start.sh /usr/local

WORKDIR /usr/local

# ENV ACTIVEMQ_CONFIG_DIR=/opt/activemq/conf.tmp
# ENV ACTIVEMQ_DATA_DIR=/data/activemq
ENV JAVA_HOME /usr/local/jdk1.8.0_144
ENV ACTIVEMQ_HOME /usr/local/apache-activemq-5.16.3
ENV PATH $JAVA_HOME/bin:$PATH:$ACTIVEMQ_HOME/bin
ENV CLASSPATH .:$JAVA_HOME/lib

EXPOSE 8161/tcp
EXPOSE 61616/tcp
EXPOSE 5672/tcp
EXPOSE 61613/tcp
EXPOSE 1883/tcp
EXPOSE 61614/tcp

# ENTRYPOINT ["sh","/usr/local/apache-activemq-5.16.3/bin/activemq start"]
CMD [ "/usr/local/apache-activemq-5.16.3/bin/activemq", "" ]
```

Dockerfile 编写好之后，放到 activemq 文件夹中，同时将 jdk-8u144-linux-x64.tar.gz \ apache-activemq-5.16.3-bin.tar.gz 也放在同一文件夹下。

```sh
$ ll
total 489384
drwxr-xr-x  5 shiqiang  staff   160B 10 24 11:26 .
drwxr-xr-x  4 shiqiang  staff   128B 10 21 14:21 ..
-rw-r--r--  1 shiqiang  staff   673B 11  6 20:21 Dockerfile
-rwxr-xr-x@ 1 shiqiang  staff    62M 10 24 11:26 apache-activemq-5.16.3-bin.tar.gz
-rw-r--r--@ 1 shiqiang  staff   177M 10 24 11:24 jdk-8u144-linux-x64.tar.gz
$ docker build -t cocowool/activemq:5.16.3 .
```

最后执行 docker build 命令可以生成镜像。

## 在容器中运行ActiveMQ

可以通过下面的命令在当前窗口，以前台的形式运行 activemq。

```
$ docker run --name='activemq' -it -p 8162:8161 --rm cocowool/activemq:
```

> 本文构建的 ActiveMQ 在 macOS 系统中无法通过宿主机访问，但是 webcenter/activemq 支持在 macOS 下的宿主机访问，尝试与 webcenter/activemq 的 Dockerfile 和启动参数，还没有找到原因，留到以后解决吧。

通过容器镜像反馈Dockerfile，可以通过 `docker history` 命令来实现

```sh
$ docker history webcenter/activemq --format {{.CreatedBy}} --no-trunc
```

## 参考资料

1. [制作activemq镜像](https://blog.csdn.net/kq1983/article/details/102793679)
2. [如何制作Docker镜像](https://zhuanlan.zhihu.com/p/122380334)
3. [消息中间件ActiveMQ使用详解](https://www.cnblogs.com/yanfei1819/p/10615605.html)
4. [Building An ActiveMQ Docker Image on Kubernetes](https://dzone.com/articles/building-active-mq-docker-image-on-k8s)
5. [Docker 的(Linux/Mac OS)网络配置问题](https://yuanmomo.net/2019/06/13/docker-network/)
5. [反推Dockerfile](http://pointborn.com/article/2021/3/31/1327.html)

