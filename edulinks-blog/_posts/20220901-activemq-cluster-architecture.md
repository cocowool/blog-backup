---
title: ActiveMQ 常见集群模式
date: 2022-09-01 11:28:27
description: 本文基于 docker-compose 在本地模拟 ActiveMQ 两种常见的集群模式。
keywords: ActiveMQ, ActiveMQ集群, ActiveMQ Cluster, ActiveMQ Master Slave
---

## Master Slave 架构模式

这种模式是基于文件共享锁的高可用集群，个人理解其实是一种 Failover 模式，可以是一主一从，也可以是一主两从。

> 本文使用 Docker 搭建一个主从模式的 ActiveMQ 5.8.0 集群，一个主节点，两个从节点。
>
> ActiveMQ 镜像的创建可以参考 [构建ActiveMQ镜像并运行](http://www.edulinks.cn/2021/10/21/20211021-make-activemq-docker-image/)

```yaml
version: "2.1"
services:
  activemq-A:
    image: cocowool/activemq:5.8.0
    hostname: activemq-A
    expose:
      - "8161"
      - "61613"
    ports:
      - "8161:8161"
      - "61613:61613"
    volumes:
      - ~/Projects/code-space/docker-conf/activemq/activemq-5.8.0-A.xml:/usr/local/apache-activemq-5.8.0/conf/activemq.xml
      - ~/Projects/code-space/docker-conf/activemq/kahadb:/usr/local/apache-activemq-5.8.0/data/kahadb
    networks:
      - default
  activemq-B:
    image: cocowool/activemq:5.8.0
    hostname: activemq-B
    expose:
      - "8162"
      - "61614"
    ports:
      - "8162:8161"
      - "61614:61613"
    volumes:
      - ~/Projects/code-space/docker-conf/activemq/activemq-5.8.0-B.xml:/usr/local/apache-activemq-5.8.0/conf/activemq.xml
      - ~/Projects/code-space/docker-conf/activemq/kahadb:/usr/local/apache-activemq-5.8.0/data/kahadb
    networks:
      - default
```

其实两个配置文件是一样的，关键是将 kahadb 的目录利用本地文件实现在两个容器见共享。验证过程记录。

```shell
$ docker-compose up
Attaching to activemq_activemq-B_1, activemq_activemq-A_1
activemq-B_1  | INFO: Using default configuration
activemq-B_1  | (you can configure options in one of these file: /etc/default/activemq /root/.activemqrc)
activemq-B_1  | 
activemq-B_1  | INFO: Invoke the following command to create a configuration file
activemq-B_1  | /usr/local/apache-activemq-5.8.0/bin/activemq setup [ /etc/default/activemq | /root/.activemqrc ]
activemq-B_1  | 
activemq-A_1  | INFO: Using default configuration
activemq-A_1  | (you can configure options in one of these file: /etc/default/activemq /root/.activemqrc)
activemq-A_1  | 
activemq-A_1  | INFO: Invoke the following command to create a configuration file
activemq-B_1  | INFO: Using java '/usr/local/jdk1.8.0_144/bin/java'
activemq-A_1  | /usr/local/apache-activemq-5.8.0/bin/activemq setup [ /etc/default/activemq | /root/.activemqrc ]
activemq-A_1  | 
activemq-B_1  | INFO: Starting in foreground, this is just for debugging purposes (stop process by pressing CTRL+C)
activemq-A_1  | INFO: Using java '/usr/local/jdk1.8.0_144/bin/java'
activemq-A_1  | INFO: Starting in foreground, this is just for debugging purposes (stop process by pressing CTRL+C)
activemq-B_1  | Java Runtime: Oracle Corporation 1.8.0_144 /usr/local/jdk1.8.0_144/jre
activemq-B_1  |   Heap sizes: current=1005056k  free=989327k  max=1005056k
activemq-B_1  |     JVM args: -Xms1G -Xmx1G -Djava.util.logging.config.file=logging.properties -Dcom.sun.management.jmxremote -Djava.io.tmpdir=/usr/local/apache-activemq-5.8.0/tmp -Dactivemq.classpath=/usr/local/apache-activemq-5.8.0/conf; -Dactivemq.home=/usr/local/apache-activemq-5.8.0 -Dactivemq.base=/usr/local/apache-activemq-5.8.0 -Dactivemq.conf=/usr/local/apache-activemq-5.8.0/conf -Dactivemq.data=/usr/local/apache-activemq-5.8.0/data
activemq-B_1  | Extensions classpath:
activemq-B_1  |   [/usr/local/apache-activemq-5.8.0/lib,/usr/local/apache-activemq-5.8.0/lib/camel,/usr/local/apache-activemq-5.8.0/lib/optional,/usr/local/apache-activemq-5.8.0/lib/web,/usr/local/apache-activemq-5.8.0/lib/extra]
activemq-B_1  | ACTIVEMQ_HOME: /usr/local/apache-activemq-5.8.0
activemq-B_1  | ACTIVEMQ_BASE: /usr/local/apache-activemq-5.8.0
activemq-B_1  | ACTIVEMQ_CONF: /usr/local/apache-activemq-5.8.0/conf
activemq-B_1  | ACTIVEMQ_DATA: /usr/local/apache-activemq-5.8.0/data
activemq-A_1  | Java Runtime: Oracle Corporation 1.8.0_144 /usr/local/jdk1.8.0_144/jre
activemq-A_1  |   Heap sizes: current=1005056k  free=989327k  max=1005056k
activemq-A_1  |     JVM args: -Xms1G -Xmx1G -Djava.util.logging.config.file=logging.properties -Dcom.sun.management.jmxremote -Djava.io.tmpdir=/usr/local/apache-activemq-5.8.0/tmp -Dactivemq.classpath=/usr/local/apache-activemq-5.8.0/conf; -Dactivemq.home=/usr/local/apache-activemq-5.8.0 -Dactivemq.base=/usr/local/apache-activemq-5.8.0 -Dactivemq.conf=/usr/local/apache-activemq-5.8.0/conf -Dactivemq.data=/usr/local/apache-activemq-5.8.0/data
activemq-A_1  | Extensions classpath:
activemq-A_1  |   [/usr/local/apache-activemq-5.8.0/lib,/usr/local/apache-activemq-5.8.0/lib/camel,/usr/local/apache-activemq-5.8.0/lib/optional,/usr/local/apache-activemq-5.8.0/lib/web,/usr/local/apache-activemq-5.8.0/lib/extra]
activemq-A_1  | ACTIVEMQ_HOME: /usr/local/apache-activemq-5.8.0
activemq-A_1  | ACTIVEMQ_BASE: /usr/local/apache-activemq-5.8.0
activemq-A_1  | ACTIVEMQ_CONF: /usr/local/apache-activemq-5.8.0/conf
activemq-A_1  | ACTIVEMQ_DATA: /usr/local/apache-activemq-5.8.0/data
activemq-B_1  | Loading message broker from: xbean:activemq.xml
activemq-A_1  | Loading message broker from: xbean:activemq.xml
activemq-B_1  |  INFO | Refreshing org.apache.activemq.xbean.XBeanBrokerFactory$1@68c4039c: startup date [Wed Sep 07 13:16:20 UTC 2022]; root of context hierarchy
activemq-A_1  |  INFO | Refreshing org.apache.activemq.xbean.XBeanBrokerFactory$1@68c4039c: startup date [Wed Sep 07 13:16:20 UTC 2022]; root of context hierarchy
activemq-B_1  |  INFO | PListStore:[/usr/local/apache-activemq-5.8.0/data/localhost/tmp_storage] started
activemq-B_1  |  INFO | Using Persistence Adapter: KahaDBPersistenceAdapter[/usr/local/apache-activemq-5.8.0/data/kahadb]
activemq-A_1  |  INFO | PListStore:[/usr/local/apache-activemq-5.8.0/data/localhost/tmp_storage] started
activemq-A_1  |  INFO | Using Persistence Adapter: KahaDBPersistenceAdapter[/usr/local/apache-activemq-5.8.0/data/kahadb]
activemq-A_1  |  INFO | Database /usr/local/apache-activemq-5.8.0/data/kahadb/lock is locked... waiting 10 seconds for the database to be unlocked. Reason: java.io.IOException: File '/usr/local/apache-activemq-5.8.0/data/kahadb/lock' could not be locked.
activemq-B_1  |  INFO | KahaDB is version 4
activemq-B_1  |  INFO | Recovering from the journal ...
activemq-B_1  |  INFO | Recovery replayed 1 operations from the journal in 0.022 seconds.
activemq-B_1  |  INFO | Apache ActiveMQ 5.8.0 (localhost, ID:activemq-B-41795-1662556582026-0:1) is starting
activemq-B_1  |  INFO | Listening for connections at: tcp://activemq-B:61616?maximumConnections=1000&wireformat.maxFrameSize=104857600
activemq-B_1  |  INFO | Connector openwire Started
activemq-B_1  |  INFO | Listening for connections at: amqp://activemq-B:5672?maximumConnections=1000&wireformat.maxFrameSize=104857600
activemq-B_1  |  INFO | Connector amqp Started
activemq-B_1  |  INFO | Listening for connections at: stomp://activemq-B:61612?transport.closeAsync=false
activemq-B_1  |  INFO | Connector stomp Started
activemq-B_1  |  INFO | Listening for connections at: stomp+nio://activemq-B:61613?transport.closeAsync=false
activemq-B_1  |  INFO | Connector stomp+nio Started
activemq-B_1  |  INFO | Apache ActiveMQ 5.8.0 (localhost, ID:activemq-B-41795-1662556582026-0:1) started
activemq-B_1  |  INFO | For help or more information please see: http://activemq.apache.org
activemq-B_1  | ERROR | Temporary Store limit is 51200 mb, whilst the temporary data directory: /usr/local/apache-activemq-5.8.0/data/localhost/tmp_storage only has 42398 mb of usable space
activemq-B_1  |  INFO | Web console type: embedded
activemq-B_1  |  INFO | ActiveMQ WebConsole initialized.
activemq-B_1  |  INFO | Initializing Spring FrameworkServlet 'dispatcher'
activemq-B_1  |  INFO | jolokia-agent: No access restrictor found at classpath:/jolokia-access.xml, access to all MBeans is allowed
activemq-A_1  |  INFO | Database /usr/local/apache-activemq-5.8.0/data/kahadb/lock is locked... waiting 10 seconds for the database to be unlocked. Reason: java.io.IOException: File '/usr/local/apache-activemq-5.8.0/data/kahadb/lock' could not be locked.
```

根据启动的顺序不同，A、B 都有可能抢到锁对外提供服务，未抢到锁的实例日志一直会报无法解锁的错误，同时不会对外提供 Web Console 服务。使用 Python 的连接例子，发现 stomp.py 有一个问题不能很好的支持 failover。

## Networks of Brokers

这是一种负载均衡的架构，各个 Broker 通过互相连接并共享队列信息，实现集群的负载均衡，即只要消费者连接到 Broker 网络中任意一台，就可以消费所有的消息。

这种方案主要关注 ActiveMQ 配置中的两个参数

* TransportConnector，传输连接器参数主要控制服务端和客户端之间的通信方式
* NetworkConnector，网络连接器参数主要控制服务端和服务端之间的通信

本文构建了这样一种架构，Broker1 作为与生产者沟通的节点，负责接收消息以及传递消费者反馈给生产者的消息，Broker2、Broker3作为与消费者沟通的节点，支持与大量的消费者建立链接。这种架构适用于消费者数量非常多，通常是超过1万个节点的情况。根据实际生产运行的情况，一台4C16G的虚拟机，一个ActiveMQ实例通常能够承受2000个左右的链接能保持长期稳定运行。

![image-20220914160031714](20220901-activemq-cluster-architecture/image-20220914160031714.png)

具体的 docker 配置如下，相关的文件可以从我的 [Github](https://github.com/cocowool/code-space) 上下载。

```yaml
version: "2.1"
services:
  activemq-broker1:
    image: cocowool/activemq:5.8.0
    hostname: activemq-broker1
    expose:
      - "8161"
      - "61611"
    ports:
      - "8161:8161"
      - "61611:61612"
    volumes:
      - ~/Projects/code-space/docker-conf/activemq-network/activemq-5.8.0-broker1.xml:/usr/local/apache-activemq-5.8.0/conf/activemq.xml
    networks:
      activemq-network:
        ipv4_address: 172.28.1.11
  activemq-broker2:
    image: cocowool/activemq:5.8.0
    hostname: activemq-broker2
    expose:
      - "8162"
      - "61612"
    ports:
      - "8162:8161"
      - "61612:61612"
    volumes:
      - ~/Projects/code-space/docker-conf/activemq-network/activemq-5.8.0-broker2.xml:/usr/local/apache-activemq-5.8.0/conf/activemq.xml
    networks:
      activemq-network:
        ipv4_address: 172.28.1.12
  activemq-broker3:
    image: cocowool/activemq:5.8.0
    hostname: activemq-broker3
    expose:
      - "8163"
      - "61613"
    ports:
      - "8163:8161"
      - "61613:61612"
    volumes:
      - ~/Projects/code-space/docker-conf/activemq-network/activemq-5.8.0-broker3.xml:/usr/local/apache-activemq-5.8.0/conf/activemq.xml
    networks:
      activemq-network:
        ipv4_address: 172.28.1.13
networks:
  activemq-network:
    ipam:
      config:
      - subnet: 172.28.1.0/24

```

执行 `docker-compose up` 命令可以启动三个 ActiveMQ 实例，然后分别通过 http://localhost:8161\8162\8163 可以查看三个实例的控制台，在 Broker2\Broker3 的 Network 页签下可以看到到 Broker1 的链接。我们在生产实践过程中发现过这个连接出问题的情况，此时 ActiveMQ 实例进程并无异常，这时就会影响连接到这个实例的消费者。

![image-20220914172430278](20220901-activemq-cluster-architecture/image-20220914172430278.png)

要测试这种架构的可用性，可以参考 [Python 与 ActiveMQ 交互的一些例子](http://www.edulinks.cn/2022/08/25/20220825-python-manage-activemq/) 中的代码示例，修改对应的端口地址就能看到效果。

这个架构可以配合 Master Slave 进一步优化，如下图所示：

![image-20220914184058344](20220901-activemq-cluster-architecture/image-20220914184058344.png)

生产者连接的实例采用 Master Slave Failover 方式，消费者连接的实例在配置 NetworkConnector 时也采用 Failover 模式，这样 Broker1 就提供了主从的高可用模式，进一步提高了整个集群应对风险的能力。

## 参考资料

1. [ActiveMQ集群安装](https://zhuanlan.zhihu.com/p/218094199)
2. [Persistence vs. Durability in Messaging. Do you know the difference?](https://developers.redhat.com/blog/2016/08/10/persistence-vs-durability-in-messaging)
3. [Shared File System Master Slave](https://activemq.apache.org/shared-file-system-master-slave)
4. [Networks of Brokers](https://activemq.apache.org/networks-of-brokers)
5. [docker-compose 固定IP](https://blog.csdn.net/m0_67401499/article/details/126327120)
6. [ActiveMQ实现负载均衡+高可用部署方案](https://www.jianshu.com/p/0aa5e33fed0c)
7. [ActiveMQ中的NetworkConnector（网络连接器）详解](https://blog.csdn.net/u011165171/article/details/84349468)
