---
title: Spark 系列二
date: 2021-10-11 20:49:04
tags:
keywords: spark, 流式计算
description: 整理的关于Spark流式计算的一些学习资料。
---

> 本文介绍Spark Standalone部署方式，Spark同时还支持YARN、Mesos进行资源调度

## 配置
* SPARK_WORKER_INSTANCES
* SPARK_WORKER_CORES

`conf/spark-env.sh` 文件指定了standalone模式的相关参数。

* SPARK_MASTER_HOST
* SPARK_MASTER_PORT
* SPARK_WORKER_CORES
* SPARK_WORKER_MEMORY
* SPARK_CONF_DIR



## 启动Master
在哪台机器上启动，这台机器就成为了master。
```sh
$ ./sbin/start-master.sh
```

## 启动Worker
```sh
$ ./sbin/start-slaves.sh
```

## 验证
```sh
$ jps -l
62227 org.apache.spark.deploy.worker.Worker
62312 sun.tools.jps.Jps
60395 org.apache.spark.deploy.master.Master
```

## 参数
* spark.streaming.receiver.maxRate
* spark.streaming.kafka.maxRatePerPartition
* spark.streaming.backpressure.enabled
* spark.streaming.blockInterval
* spark.default.parallelism

## 高可用集群搭建
Spark高可用集群，是指搭建多个Master，其中一个为ALIVE，另外几个为STANDBY。Master之间状态的同步和切换通过Zookeeper来实现。

主要依靠修改`spark-env.sh`文件中的参数。
- Dspark.deployrecoveryMode=ZOOKEEPER
- Dspark.deploy.zookeeper.url=zk:2181,zk2:2181

## Spark Submit 的两种方式

### Local模式
Local模式通常用于测试，直接`bin/spark-shell`启动即可。

### client 模式
client模式使用6066端口

### cluster 模式
cluster模式使用7077端口

### 相关参数

| 参数名 | 参数说明 | 备注 |
| --- | --- | --- |
| --master | master 的地址，提交任务到哪里执行，例如 spark://host:port,  yarn,  local | |
| --deploy-mode | 在本地 (client) 启动 driver 或在 cluster 上启动，默认是 client | |
| --class | 应用程序的主类，仅针对 java 或 scala 应用 | |
| --name | 应用程序的名称 | |
| --jars | 用逗号分隔的本地 jar 包，设置后，这些 jar 将包含在 driver 和 executor 的 classpath 下 | |
| --packages | 包含在driver 和executor 的 classpath 中的 jar 的 maven 坐标 | |
| --exclude-packages | 为了避免冲突 而指定不包含的 package | |
| --repositories | 远程 repository | |
| --conf PROP=VALUE	|  指定 spark 配置属性的值， 例如 -conf spark.executor.extraJavaOptions="-XX:MaxPermSize=256m" | |
| --properties-file | 加载的配置文件，默认为 conf/spark-defaults.conf | |
| --driver-memory | Driver内存，默认 1G | |
| --driver-java-options	| 传给 driver 的额外的 Java 选项 | |
| --driver-library-path	| 传给 driver 的额外的库路径 | |
| --driver-class-path | 传给 driver 的额外的类路径 | |
| --driver-cores | Driver 的核数，默认是1。在 yarn 或者 standalone 下使用 | |
| --executor-memory | 每个 executor 的内存，默认是1G | |
| --total-executor-cores | 所有 executor 总共的核数。仅仅在 mesos 或者 standalone 下使用 | |
| --num-executors | 启动的 executor 数量。默认为2。在 yarn 下使用 | |
| --executor-core | 每个 executor 的核数。在yarn或者standalone下使用 | |



## Spark Streaming

### Spark Streaming 介绍

Spark Streaming 是Spark API核心的扩展，支持实时数据流的可扩展，高吞吐量，容错流处理。数据可以从Kafka，Flume， Kinesis， 或TCP Socket来源获得，并且可以使用与高级别功能表达复杂的算法来处理map，reduce，join和window。最后，处理后的数据可以推送到文件系统，数据库和实时仪表看板。

Spark Streaming provides a high-level abstraction called discretized stream or DStream, which represents a continuous stream of data.  Internally, a DStream is represented as a sequence of RDDs.

### 原理
* 创建SparkConf
* 创建DStream对象
* 定义DStream的输入源
* 定义DStream的计算和输出
* 启动数据接收和任务处理 `streamingContext.start()`
* 等待处理进程结束 `streamingContext.awaitTermination()`
* 手工终止处理流程 `streamingContext.stop()`

### DStream
DStream表示一系列连贯的数据流，由连续的RDD序列表示。

* 可靠的Receiver，例如Kafka、flume
* 不可靠的Receiver，消费数据后不能向数据源进行确认

### Window Operation
* window length
* sliding interval

### foreachRDD
Spark为DStreams对象提供了几种输出方式，包括`print()\saveAsTextFiles()\saveAsObjectFiles()\saveAsHadoopFiles()\foreachRDD`。最重要也最常用的就是`foreachRDD`，我们一般使用这个函数将计算的结果集存入数据库或其他什么地方。

> DStreams are executed lazily by the output operations, just like RDDs are lazily executed by RDD actions. Specifically, RDD actions inside the DStream output operations force the processing of the received data. Hence, if your application does not have any output operation, or has output operations like dstream.foreachRDD() without any RDD action inside them, then nothing will get executed. The system will simply receive the data and discard it.

> By default, output operations are executed one-at-a-time. And they are executed in the order they are defined in the application.

### Checkpoint
可以定期对RDD做一个检查，然后将状态保存在文件中，如果Spark意外退出，还可以从原来的状态中恢复。

> Accumulator和广播变量不能从Checkpoint中恢复。

### 部署
* Spark Cluster 带有 cluster manager
* APP的Jar包（特别是如果使用了Kafka、Flume这类的高级数据源）
* 为Executor配置足够的内存
* 如果应用需要则配置checkpoint

### 性能
> Note that, if you want to receive multiple streams of data in parallel in your streaming application, you can create multiple input DStreams (discussed further in the Performance Tuning section). This will create multiple receivers which will simultaneously receive multiple data streams. 

> 集群运行时，Core的数量必须大于Receiver的数量，否则只接收数据不处理数据。Extending the logic to running on a cluster, the number of cores allocated to the Spark Streaming application must be more than the number of receivers. Otherwise the system will receive data, but not be able to process it.
> 

* 降低每个批次的处理时间

### 在本地提交任务
```sh
$ ./spark-submit --name Spark-Name --master local[*] --conf spark.executor.extraClassPath=/path/to/class --conf spark.driver.extraClassPath=/path/to/class --class ClassName class-name.jar
```

## 参考资料

1. [Multiple Spark Worker Instances on a single Node. Why more of less is more than less.](https://sonra.io/2015/06/03/multiple-spark-worker-instances-on-a-single-node-why-more-of-less-is-more-than-less/)
2. [Spark Streaming Guide](http://spark.apache.org/docs/latest/streaming-programming-guide.html)
3. [Spark学习（二）Spark高可用集群搭建](https://blog.csdn.net/qq_41851454/article/details/80013831)
4. [SPARK运行模式](https://www.jianshu.com/p/b4d3db386925)