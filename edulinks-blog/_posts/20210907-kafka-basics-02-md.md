---
title: Kafka学习笔记二：常用命令
date: 2021-09-07 15:07:48
tags:
keywords: kafka
description: 本篇介绍Kafka的常用命令
---

[TOC]
## 集群管理
### 启动Kafka
```sh
$ ./bin/kafka-server-start.sh -daemon config/server.properties &
```

## 集群信息查询
### 查看Broker列表
```sh
$ ./bin/zookeeper-shell.sh localhost:2181 ls /brokers/ids
```

## 数据查询
### 查看TOPIC列表
```sh
$ ./bin/kafka-topics.sh --list --zookeeper 128.192.138.105:2181
```

### 查看指定的Topic信息
```sh
$ ./bin/kafka-topics.sh --zookeeper 128.192.138.105:2181 --describe --topic topic_name
```

### 查看当前的消费组信息
```sh
$ ./bin/kafka-consumer-groups.sh --bootstrap-server 128.192.116.101:9092 --list
```

### 客户端消费某个Topic
```sh
$ ./bin/kafka-console-consumer.sh --zookeeper 128.192.138.105:2181 --from-beginning --topic topic_name
```

### 查看每个分区的数据量
```sh
$ ./kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list ip:port --time -1 --topic topic-name
```

## 数据操作
### 创建Topic
```sh
$ ./bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
Created topic "test".
```

### 创建一个生产者
```sh
./bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
>a
>b
>c
>d
>a:a
```

### 创建一个消费者
```sh
$ ./bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
a
b
c
d
a:a
```

### 删除一个Topic
```sh
$ ./bin/kafka-topics.sh --delete --zookeeper localhost:2181 --topic test 
```

## 参考资料
1. [kafka常用命令](https://blog.csdn.net/zxy987872674/article/details/72493128)

2. [Kafka【第一篇】Kafka集群搭建](https://www.cnblogs.com/luotianshuai/p/5206662.html)

3. [kafka-producer生产者案例](https://blog.csdn.net/qq_31289187/article/details/81809014)

4. [How to list all available Kafka brokers in a cluster?](https://stackoverflow.com/questions/40146921/how-to-list-all-available-kafka-brokers-in-a-cluster)

   