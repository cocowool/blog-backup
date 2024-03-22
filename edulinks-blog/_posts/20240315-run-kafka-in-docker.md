---
title: 快速启动一个容器化Kafka
date: 2024-03-15 09:53:27
keywords:
description:
---

> 本文基于 macOS Monterey 和 Docker 20.10.8，使用了 wurstmeister/kafka 和 wurstmeister/ zookeeper 镜像来搭建服务，读者至少需要准备一个 Docker 环境并提前下载好相关的镜像。

## 简单环境分步操作

最简单的 Kafka 服务运行起来需要一个 Zookeeper 应用和一个 Kafka 应用。
```sh
# 启动 zookeeper 容器，例子里是在前台启动，加上 -d 后可以变为后台
$ docker run --rm --name zookeeper -p 2181:2181 wurstmeister/zookeeper
# 新开一个终端窗口启动 Kafka
$ ocker run --rm --name kafka -p 9092:9092 -e KAFKA_BROKER_ID=0 -e KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181 --link zookeeper -e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://11.136.164.13:9092 -e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 -t wurstmeister/kafka
```

利用命令行工具做消息的生产和接收验证。开两个终端窗口，一个窗口观察收到的消息，一个窗口发送消息。
```sh
# 接收消息的终端
$ docker exec -it kafka sh
# kafka-console-consumer.sh --bootstrap-server 11.136.164.13:9092 --topic tran_raw --from-beginning
```

```sh
# 发送消息的终端
$ docker exec -it kafka sh
# kafka-console-producer.sh --broker-list 11.136.164.13:9092 --topic tran_raw 
> Hello world
```

在 `>` 符号后面输入 Hello World 回车后，就可以在另外一个窗口中看到收到的消息。

## 使用Python验证

通过 msg_sender.py 程序代码验证发送消息。
```python
from confluent_kafka import Producer

p = Producer({'bootstrap.servers': 'localhost:9092'})
p.produce('tran_raw', b'hello world')
p.flush(timeout=30)
```

通过 msg_consumer.py 程序代码验证消费消息。
```python
from confluent_kafka import Consumer


c = Consumer({'bootstrap.servers': 'localhost:9092', 'group.id' : 'test-group'})
c.subscribe(['tran_raw'])

try:
    while True:
        msg = c.poll(timeout=1.0)
        if msg is None:
            continue
        if msg.error():
            print('Error occured: {}'.format(msg.error()))
        print('Message: {}'.format(msg.value().decode('utf-8')))

except KeyboardInterrupt:
    pass

finally:
    c.close()
```

## compose 方式

利用 docker-composed 可以实现一键启动容器化的 Kafka 环境，方便本地调试。
```yaml
version: '2'

services:
  zookeeper:
    image: wurstmeister/zookeeper
    restart: unless-stopped
    hostname: zookeeper
    ports:
      - "2181:2181"
    container_name: zookeeper
  kafka:
    image: wurstmeister/kafka
    ports:
      - "9092:9092"
    environment:
      KAFKA_ADVERTISED_HOST_NAME: kafka
      KAFKA_ADVERTISED_PORT: 9092
      KAFKA_ZOOKEEPER_CONNECT: "zookeeper:2181"
      KAFKA_BROKER_ID: 1
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_CREATE_TOPICS: "test_flink_input"
    depends_on:
      - zookeeper
    container_name: kafka
```

将这个文件保存为 docker-copose.yml 放在一个单独文件夹中，终端环境下 cd 到这个目录执行
```sh
$ docker-compose up
# 如果放入后台执行需要 -d 参数
```

## 参考资料

1. [Docker 安装 Kafka_docker bitnami/kafka](https://blog.csdn.net/qq_42971035/article/details/129697736)
1. [Kafka入门实战教程（2）基于Docker搭建Kafka环境-腾讯云开发者社区](https://cloud.tencent.com/developer/article/2091266)
1. [docker --link容器互联_docker link](https://blog.csdn.net/omaidb/article/details/107845466)
1. [利用docker和docker-compose部署单机kafka](https://zhuanlan.zhihu.com/p/106859654)
