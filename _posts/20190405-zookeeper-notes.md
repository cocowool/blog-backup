---
title: Zookeeper学习笔记
date: 2019-04-05 22:07
tag: 
---

目录

* [Zookeeper 介绍](#zookeeper-介绍)
* [Zookeeper的特点](#zookeeper的特点)
* [常用操作](#常用操作)
  * [查看Server运行状态](#查看server运行状态)
  * [客户端连接服务器](#客户端连接服务器)
  * [常用命令](#常用命令)
  * [ACL权限](#acl权限)

* [四字命令](#四字命令)
* [参考资料](#参考资料)



## Zookeeper 介绍
Zookeeper是一个分布式应用程序协调服务，主要负责集中维护配置信息的服务，提供分布式的同步机制。其所有的服务都是做为其他分布式应用的基础。Zookeeper是Google Chubby的一个开源实现，是现在很多分布式应用的重要组件，包括诸如Hadoop、HBase、Kafka等，可以说现在Zookeeper是大家必须掌握的一门软件。

Zookeeper主要提供以下几种服务：

1. 命名服务
2. 配置管理
3. 集群管理
4. 分布式锁
5. 队列管理

## Zookeeper的特点

* 简单：Zookeeper的核心是一个精简的文件系统，它支持一些简单的操作和一些抽象操作，例如，排序和通知。
* 丰富：Zookeeper的原语操作是很丰富的，可实现一些协调数据结构和协议。例如，分布式队列、分布式锁和一组同级别节点中的“领导者选举”。
* 高可用：Zookeeper支持集群模式，可以很容易的解决单点故障问题。
* 松耦合式交互：不同进程间的交互不需要了解彼此，甚至可以不必同时存在，某进程在zookeeper中留下消息后，该进程结束后其它进程还可以读这条消息。
* 资源库：Zookeeper实现了一个关于通用协调模式的开源共享存储库，能使开发者免于编写这类通用协议。

## 常用操作
为了熟悉Zookeeper的常用操作，最简单的办法就是使用Docker。
```bash
$ docker pull zookeeper
# 完成后启动zookeeper
$ docker run -p 2181:2181 zookeeper
# 重新打开一个Terminal界面，查找到当前容器的ID，登陆到容器中
$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS              PORTS                                        NAMES
63c3aa117fca        zookeeper           "/docker-entrypoint.…"   About a minute ago   Up About a minute   2888/tcp, 0.0.0.0:2181->2181/tcp, 3888/tcp   infallible_saha
$ docker exec -it 63c3aa117fca bash
```
现在一个单机版的Zookeeper就运行起来了，可以尝试常用的命令了。

### 查看Server运行状态
```bash
bash-4.4# ./zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /conf/zoo.cfg
Mode: standalone
```

### 客户端连接服务器
```bash
bash-4.4# ./zkCli.sh 
Connecting to localhost:2181
```

### 常用命令

* 查看:```ls```
* 获取节点数据和更新信息:```get /zookeeper```
```bash
[zk: localhost:2181(CONNECTED) 3] get /zookeeper

cZxid = 0x0
ctime = Thu Jan 01 00:00:00 GMT 1970
mZxid = 0x0
mtime = Thu Jan 01 00:00:00 GMT 1970
pZxid = 0x0
cversion = -1
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 0
numChildren = 1
```

* 获取节点的更新信息:```stat /zookeeper```
* 创建节点:```craete [-s] [-e] path data acl```，```-s```表示创建顺序节点，```-e```表示创建临时节点。
```bash
[zk: localhost:2181(CONNECTED) 6] create /spring hello
Created /spring
[zk: localhost:2181(CONNECTED) 9] get /spring
hello
cZxid = 0x4
ctime = Fri Apr 05 09:30:55 GMT 2019
mZxid = 0x4
mtime = Fri Apr 05 09:30:55 GMT 2019
pZxid = 0x4
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 5
numChildren = 0
```

* 修改节点:```set path data [version]```，可以看到dataVersion变成了1
```bash
[zk: localhost:2181(CONNECTED) 11] set /spring spring-v1
cZxid = 0x4
ctime = Fri Apr 05 09:30:55 GMT 2019
mZxid = 0x5
mtime = Fri Apr 05 09:34:19 GMT 2019
pZxid = 0x4
cversion = 0
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 9
numChildren = 0
```

* 删除节点:```delete path [version]```
```bash
[zk: localhost:2181(CONNECTED) 17] delete /spring
[zk: localhost:2181(CONNECTED) 18] get /spring
Node does not exist: /spring
```

* 设置watch事件:```stat path [watch]```
```bash
[zk: localhost:2181(CONNECTED) 33] stat /spring/bootx watch
Node does not exist: /spring/bootx
[zk: localhost:2181(CONNECTED) 34] create /spring/bootx bootx
Created /spring/bootx

WATCHER::
[zk: localhost:2181(CONNECTED) 35] 
WatchedEvent state:SyncConnected type:NodeCreated path:/spring/bootx
```

* 设置watch事件:```get path [watch]```
```bash
[zk: localhost:2181(CONNECTED) 38] get /spring/booty watch
new-boot-y
cZxid = 0xc
ctime = Fri Apr 05 13:38:36 GMT 2019
mZxid = 0xd
mtime = Fri Apr 05 13:38:48 GMT 2019
pZxid = 0xc
cversion = 0
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 10
numChildren = 0
[zk: localhost:2181(CONNECTED) 39] set /spring/booty nnew-boot-y

WATCHER::

WatchedEvent state:SyncConnected type:NodeDataChanged path:/spring/booty
cZxid = 0xc
ctime = Fri Apr 05 13:38:36 GMT 2019
mZxid = 0xe
mtime = Fri Apr 05 13:39:26 GMT 2019
pZxid = 0xc
cversion = 0
dataVersion = 2
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 11
numChildren = 0
```

### ACL权限
ZK的节点有5种操作权限：```CREATE、READ、WRITE、DELETE、ADMIN```也就是 增、删、改、查、管理权限。
> 注：这5种权限中，delete是指对子节点的删除权限，其它4种权限指对自身节点的操作权限。

身份的认证有4种方式：

* world：默认方式，相当于全世界都能访问
* auth：代表已经认证通过的用户(cli中可以通过addauth digest user:pwd 来添加当前上下文中的授权用户)
* digest：即用户名:密码这种方式认证，这也是业务系统中最常用的
* ip：使用Ip地址认证
```bash
[zk: localhost:2181(CONNECTED) 42] getAcl /spring
'world,'anyone
: cdrwa
[zk: localhost:2181(CONNECTED) 43] getAcl /zookeeper
'world,'anyone
: cdrwa
```

## 四字命令
ZooKeeper 支持某些特定的四字命令字母与其的交互。它们大多是查询命令，用来获取 ZooKeeper 服务的当前状态及相关信息。用户在客户端可以通过```telnet```或```nc```向 ZooKeeper 提交相应的命令。 ZooKeeper 常用四字命令如下表所示。

使用方法
```bash
$ echo stat | nc 127.0.0.1 2181
Zookeeper version: 3.4.13-2d71af4dbe22557fda74f9a9b4309b15a7487f03, built on 06/29/2018 04:05 GMT
Clients:
 /172.17.0.1:53548[0](queued=0,recved=1,sent=0)
 /127.0.0.1:56306[1](queued=0,recved=483,sent=485)

Latency min/avg/max: 0/0/45
Received: 500
Sent: 501
Connections: 2
Outstanding: 0
Zxid: 0xe
Mode: standalone
Node count: 8
```

## 参考资料

1. [Apache Zookeeper](https://zookeeper.apache.org)
2. [百度百科 Zookeeper](https://baike.baidu.com/item/zookeeper/4836397?fr=aladdin)
3. [Zookeeper的功能以及工作原理](https://www.cnblogs.com/felixzh/p/5869212.html)
4. [Zookeeper系列一：Zookeeper基础命令操作](https://blog.csdn.net/dandandeshangni/article/details/80558383)
5. [ZooKeeper系列之六：ZooKeeper四字命令](https://blog.csdn.net/shenlan211314/article/details/6187029)












