---
title: 如何让容器具有固定IP
date: 2019-04-17 19:50
tag: 
keywords: docker, docker网络, docker固定IP, docker自定义网络
description: 对于一些集群软件或者可能会频繁重启的容器服务，如果每次容器ip都会发生变化对于我们开发、测试是非常不方便的，本文介绍如何创建自定义网络并产生固定的容器IP。
---

> 对于一些集群软件或者可能会频繁重启的容器服务，如果每次容器ip都会发生变化对于我们开发、测试是非常不方便的，这就需要我们能够固定容器IP，本文介绍了其中一种方法。

<!-- more -->

Docker安装后，默认会创建三种网络

```bash
$ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
6bdc00ad2a8d        bridge              bridge              local
e373574073e9        host                host                local
d3d47c52f57b        none                null                local
```
网络类型的介绍

* bridge： 默认情况下启动的Docker容器，都是使用 bridge，Docker安装时创建的桥接网络，每次Docker容器重启时，会按照顺序获取对应的IP地址，这个就导致重启下，Docker的IP地址就变了
* host：Docker 容器的网络会附属在主机上，两者是互通的。
* none： docker 容器就不会分配局域网的IP

Docker启动的时候，可以使用```--network```参数指定使用的网络

```bash
$ docker run -itd --name test1 --network bridge --ip 172.17.0.2 tomcat
```
### 创建自定义网络并设置固定IP

在搭建一些集群软件的时候，组件和组件之间需要进行网络通信，这个时候如果每次重启IP都发生变化会很不方便，因此希望能够将容器IP固定下来，这也是可以实现的，具体参考下面的方法。

**1.创建自定义网络**

```bash
$ docker network create --subnet=172.18.0.0/16 es-network
1e3e1eb702176df3e44111970292deaf5def7564135439fae21be489d6a8dcef
$ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
6bdc00ad2a8d        bridge              bridge              local
1e3e1eb70217        es-network          bridge              local
e373574073e9        host                host                local
d3d47c52f57b        none                null                local
```
**2.创建Docker容器**

在创建容器的时候，使用`--net`和`--ip`参数将IP固定为刚才创建的网段内的地址。

```bash
docker run -e ES_JAVA_OPTS="-Xms256m -Xmx256m" -d -p 9202:9202 -p 9302:9302 -v ~/Projects/elk/elk-cluster/data3/es3.yml:/usr/share/elasticsearch/config/elasticsearch.yml -v ~/Projects/elk/elk-cluster/data3:/usr/share/elasticsearch/data --name ES03 --net=es-network --ip=172.18.0.12 --hostname=es-node3 docker.elastic.co/elasticsearch/elasticsearch:6.7.0
```
使用```docker inspect container-id```可以看到当前容器分配的IP就是命令中指定的地址，无论容器重启多少次IP都不会发生变化了。

如果是使用PHP进行开发的朋友，还可以参考我这篇文章[使用Docker快速搭建PHP开发环境](http://www.edulinks.cn/2020/04/17/20200415-qucik-lnmp-dev-environment/)来利用Docker快速搭建PHP开发环境，并且固定Mysql的IP，这样每次重启环境的时候，就不用修改配置文件中数据库的地址了。










