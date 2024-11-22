---
title: 基于Docker快速搭建Redis集群
date: 2021-02-27 11:52:28
keywords: docker, redis, linux, redis集群
description:  Redis 是一个 Key / Value 数据库，本文介绍了容器化部署主备集群的方法。 
---

## Redis 介绍

[Redis](https://redis.io) 本质上是一个Key / Value 数据库，是与 Memcached 类似的NoSQL数据库。相比 Memcached ，它支持数据持久化到磁盘，支持更多的数据类型（包括 String、List、Set、Hash、Zset），支持数据备份等多种特性在2014年之后取得了快速的发展。

## Docker安装Redis

### 1. 获取Redis镜像

```sh
$ docker pull redis
$ docker images 
REPOSITORY                          TAG                 IMAGE ID            CREATED             SIZE
redis                               6.2.0               cc69ae189a1a        4 days ago          105MB
```

> 写文章时，最新的 redis 镜像版本为 6.2.0

### 2. 启动单个Redis服务

```sh
shiqiang@bogon redis % docker run --rm -p 6379:6379 --name redis -d redis redis-server --appendonly yes
f22a08d103ad0908803da0ba4d58f08717d1e6c1f1064175c22324a274c6bb6b
shiqiang@bogon redis % docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
f22a08d103ad        redis               "docker-entrypoint.s…"   3 seconds ago       Up 2 seconds        0.0.0.0:6379->6379/tcp   redis
shiqiang@bogon redis % docker exec -it redis /bin/bash
root@f22a08d103ad:/data# redis-cli
127.0.0.1:6379> set test 1
OK
127.0.0.1:6379> get test
"1"

```

上面的代码首先创建了一个后台状态运行的reids服务，这个服务还不能用于生产环境，因为配置和数据都是在容器中，当容器重启之后，所有的信息都会丢失，尽可以作为测试学习使用。

随后我们又运行了容器中的 `redis-cli` 命令，验证了与服务端的连接。

如果使用本地的配置文件，则需要如下的命令。

```sh
$ docker run --rm -p 6379:6379 --name redis -d -v "$PWD/redis.conf":/usr/local/etc/redis/redis.conf -v "$PWD/data":/data redis redis-server /usr/local/etc/redis/redis.conf
```

配置文件的样例

```conf
dir /data
logfile /data/redis.log
protected-mode no

appendonly yes
```

## 基于Docker快速构建Redis集群

首先来介绍主从模式的Redis集群，开始之前先做好准备，拉取 Redis 镜像文件，创建对应的配置文件。

```sh
$ docker images
REPOSITORY              TAG                 IMAGE ID            CREATED             SIZE
redis                  6.2.0               cc69ae189a1a        8 days ago          105MB
$ ls -lha
total 24
drwxr-xr-x  7 shiqiang  staff   224B  3  3 13:15 .
drwxr-xr-x  3 shiqiang  staff    96B  3  3 14:25 ..
drwxr-xr-x  2 shiqiang  staff    64B  3  3 13:15 data-master
drwxr-xr-x  2 shiqiang  staff    64B  3  3 13:15 data-slave
-rw-r--r--  1 shiqiang  staff   1.1K  3  3 17:25 docker-compose.yml
-rw-r--r--  1 shiqiang  staff    92B  3  3 13:24 redis_master.conf
-rw-r--r--  1 shiqiang  staff   118B  3  3 14:31 redis_slave.conf
```

配置文件的具体内容可以参考我的 [Github](https://github.com/cocowool/code-space)。

```conf
# Redis-master
dir /data
logfile /data/redis-master.log
protected-mode no

appendonly yes

bind 172.25.0.11

# Redis-slave
dir /data
logfile /data/redis-slave.log
protected-mode no

appendonly yes

bind 172.25.0.12

slaveof 172.25.0.11 6379
```

看一下具体效果：

```sh
# 在一个终端中启动服务
$ docker-compose up
Creating network "master-slave_redis-network" with driver "bridge"
Creating master-slave_redis-slave_1  ... done
Creating master-slave_redis-master_1 ... done
Attaching to master-slave_redis-slave_1, master-slave_redis-master_1

# 打开另外一个终端，进入 redis slave 容器
docker exec -it 655b7f4e8f41 sh
$ redis-cli -h 172.25.0.12
172.25.0.12:6379> set username wang
(error) READONLY You can't write against a read only replica.
172.25.0.12:6379> get username
"wang"
172.25.0.12:6379> get username
"wang"

# 再打开一个终端，进入 redis master 容器
$ docker exec -it 6a5c161840ee sh
# redis-cli -h 172.25.0.11
172.25.0.11:6379> set username wang
OK
172.25.0.11:6379> get username
"wang"

```

## 参考资料

1. [redis 和 memcached区别是什么？](https://blog.csdn.net/weixin_38362455/article/details/86760892)
2. [Compose file version 2 reference](https://docs.docker.com/compose/compose-file/compose-file-v2/)