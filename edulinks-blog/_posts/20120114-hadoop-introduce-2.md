---
title: Hadoop学习笔记二
date: 2012-01-14 21:23:17
tags:
keywords: hadoop, linux
description: 这次学习主要实现了Hadoop的HDFS和Map/Reduce的核心功能。
---

本篇是学习搭建Hadoop分布式环境的笔记，如果你需要在单点环境中进行测试，可以参考我的前一篇文章[Hadoop学习笔记一](http://www.edulinks.cn/2011/12/20/20111220-hadoop-introduce/)。这次学习主要实现了Hadoop的HDFS和Map/Reduce的核心功能，关于Hadoop项目的更多的功能，准备在后续深入的学习。

## 准备工作

1、确保所有必须的软件已经正确安装：Java、SSH等。
2、下载Hadoop软件，我用的是：hadoop-0.20.203.0 的版本。

## 安装

Hadoop集群的安装需要在所有节点上解压下载的Hadoop软件。典型情况下，集群中的一台机器作为NameNode、一台机器作为JobTracker，他们都作为主节点（Master）机器。集群中的其他机器作为DataNode和TaskTracker节点，他们都是从节点。软件的根目录定义为 HADOOP_HOME，所有的机器都有相同的 HADOOP_HOME。

本次练习中，我准备了5台服务器，利用Virtual Box虚拟的，系统为Centos6.0，IP分别为192.168.56.101-192.168.56.105。用户使用为root用户，hadoop使用root用户会有一些问题，可以参考这个系列的上一篇文章解决。

## 配置

配置文件介绍

Hadoop配置主要包括两种类型的配置文件：

1、只读的默认配置：src/core/core-default.xml，src/hdfs/hdfs-default.xml，src/mapred/mapred-default.xml
2、每个站点特定的配置文件：conf/core-site.xml，conf/hdfs-site.xml和conf/mapred-site.xml。

Hadoop的控制脚本位于 bin/ 目录下，每个站点的详细配置可以在 conf/hadoop-env.sh 里指定。

## 站点配置

为了配置Hadoop集群，我们需要配置Hadoop daemons运行的环境以及daemon的运行配置参数。

Hadoop daemon包括 NameNode/DataNode 和 JobTracker/TaskTracker。

Hadoop Daemon运行的环境配置

首先通过修改 conf/hadoop-env.sh 脚本来确保Hadoop daemon的运行环境配置正确。特别是 JAVA_HOME 配置正确，这样hadoop命令才能正常运行。
               
另外两个比较重要的参数是：

HADOOP_LOG_DIR：保存Hadoop daemon日志文件的文件夹。如果不存在，会被自动创建（权限？）。这个选项默认没有打开，我们需要去掉注释打开这儿选项。如果是在生产环境，注意日志的目录大小要够大。

```sh
export HADOOP_LOG_DIR=${HADOOP_HOME}/logs
```

HADOOP_HEAPSIZE：最大可用内存大小，单位为MB。默认的大小为1000MB。

Hadoop Daemon的配置

conf/core-site.xml 中的参数配置：
core-site.xml 是Hadoop的核心配置文件，对应并覆盖core-default.xml中的配置。

fs.default.name     NameNode的URI     例子：hdfs://hostname
     
配置文件示例：
```xml         
<configuration>
    <property>
        <name>fs.default.name</name>
        <value>hdfs://192.168.56.101:9000</value>
    </property>
</configuration>
```
conf/hdfs-site.xml 中的参数配置：
hdfs-site.xml 是HDFS的配置文件，对应并覆盖hdfs-default.xml中的配置项。

dfs.name.dir     物理文件系统的路径，NameNode在此路径下存储namespace和transactions日志信息。如果是由逗号分割的一个文件夹列表，则所有的数据在每个文件夹下都会存在，来实现冗余。
dfs.data.dir     逗号分割的物理文件系统路径，为DataNode生命应当存储文件块的路径。

配置文件示例：
```xml
<configuration>
    <property>
        <name>dfs.name.dir</name>
        <value>/root/hadoop/data/dfs.name.dir</value>
    </property>
    <property>
        <name>dfs.data.dir</name>
        <value>/root/hadoop/data/dfs.data.dir</value>
    </property>
    <property>
        <name>dfs.permissions</name>
        <value>false</value>
    </property>
</configuration>
```

conf/mapred-site.xml 中的参数配置：
mapred-site.xml 是Map/Reduce的配置文件，对应并覆盖mapred-default.xml的配置项。

配置文件示例：
```xml
<configuration>
    <property>
        <name>mapred.job.tracker</name>
        <value>192.168.56.102:9001</value>
    </property>
    <property>
        <name>mapred.system.dir</name>
        <value>/system/mapred.system.dir</value>
    </property>
    <property>
        <name>mapred.local.dir</name>
        <value>/root/hadoop/data/mapred.local.dir</value>
    </property>
</configuration>
```