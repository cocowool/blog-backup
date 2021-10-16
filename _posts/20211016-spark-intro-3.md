---
title: Spark 系列二
date: 2021-10-16 22:24:54
tags:
keywords: spark, 流式计算
description: 整理的关于Spark流式计算的一些学习资料。
---

因为Spark集中使用内存的特性，为Spark程序分配超过100G的堆空间是很常见的事情，这种情况在传统的Java应用中却不常见。

## 查看设备性能
### CPU
```sh
$ top
```
### 内存
```sh
$ free -h
```
### 磁盘
```sh
$ df -lh
$ iostat -x 1s 10
```

使用`vmstat`、`netstat`命令查看。

## 查看进程内存
```sh
$ jmap -heap processId
```

## 查看GC情况

```sh
$ jstat -gcutil processId 1s 60
```

### 打开Executor的GC日志
```sh
--conf spark.executor.extraJavaOptions="-XX:+PrintFlagsFinal -XX:PrintReferenceGC -verbose:gc -XX:+PrintGCDetails -XX:PrintGCTimeStamps -XX:PrintAdaptiveSizePolicy -XX:+UnlockDiagnosticVMOptions -XX:G1SummarizeConcMark"
```

### 打开Driver的GC日志
```sh
--conf spark.driver.extraJavaOptions="-XX:+PrintFlagsFinal -XX:PrintReferenceGC -verbose:gc -XX:+PrintGCDetails -XX:PrintGCTimeStamps -XX:PrintAdaptiveSizePolicy -XX:+UnlockDiagnosticVMOptions -XX:G1SummarizeConcMark"
```

* 是否有内存泄漏。Memory Leak
* 使用工具查看GC的统计数据

当Spark GC频繁发生或者持续很长时间的时候，说明内存没有被有效使用，可以通过强制清理缓存的RDD来提高内存使用效率。

### 优化建议
* 降低`InitiatingHeapOccupancyPercent`的值，默认为45.
* 增加`ConcGCThreads`的值。
* 增加`G1HeapRegionSize`的值，避免创建过大的Heap区域。如果确实程序需要很多的大对象，就需要仔细的调整`-XX:G1HeapWastePercent -XX:G1MixedGCLiveThresholdPercent`参数来避免这种情况。
* 如果想减少STW的数量，可以减小`G1RSetUpdatingPauseTimePercent`参数，同时增加`G1ConcRefinementThreads`
* 对于长时间运行的程序，可以使用`AlwaysPreTouch`参数，这样JVM在启动的时候就会向OS申请必须的内存，从而避免动态的调整。

> 参考配置 `-XX:+UseG1GC -XX:+PrintFlagsFinal -XX:+PrintReferenceGC -verbose:gc -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+PrintAdaptiveSizePolicy -XX:+UnlockDiagnosticVMOptions -XX:+G1SummarizeConcMark -Xms88g -Xmx88g -XX:InitiatingHeapOccupancyPercent=35 -XX:ConcGCThread=20`

## 查看HeapDump
使用`-XX:HeapDumpOnOutOfMemoryError`可以在发生OOM的时候自动生成DUMP文件。

RDD使用的空间越小，Heap使用的空间就越大，更大的Heap空间带来更高的内存使用效率。

## 相关参数
* spark.core.connection.ack.wait.timeout 避免因网络或GC造成任务丢失
* spark.speculation true 开启推测机制，避免某台慢造成影响
* spark.speculation.interval 100 单位毫秒
* spark.speculation.quantile 0.75
* spark.speculation.multiplier 1.5 比其他慢1.5倍时启动推测
* spark.shuffle.io.numConnectionsPerPeer
* spark.storage.memoryFraction 控制cached RDD所占的空间比例 

## 以pid分割spark日志

生产集群上Spark以脚本的方式启动，每个Worker上会启动三个Executor，脚本文件中gc的日志文件写了一个固定目录，结果发现gc日志会产生相互覆盖，对于排查生产问题不利，因此想要每个单独的Executor生产自己的gc日志文件。

查看官方文档，知道可以使用`spark.executor.extraJavaOptions`指定gc的目录，对于如何根据分隔日志，官方的描述感觉并不清晰。

> A string of extra JVM options to pass to executors. For instance, GC settings or other logging. Note that it is illegal to set Spark properties or maximum heap size (-Xmx) settings with this option. Spark properties should be set using a SparkConf object or the spark-defaults.conf file used with the spark-submit script. Maximum heap size settings can be set with spark.executor.memory. **The following symbols, if present will be interpolated: will be replaced by application ID and will be replaced by executor ID. For example, to enable verbose gc logging to a file named for the executor ID of the app in /tmp, pass a 'value' of: -verbose:gc -Xloggc:/tmp/-.gc**

`%p`


## 参考资料
 1. [Spark作业性能优化](https://www.jianshu.com/p/feb9c82ac0a5)
 2. [Spark 任务执行排查慢的问题排查-2](https://www.jianshu.com/p/1f45bb8a81b3)
 3. [Spark job 异常排查-1](https://www.jianshu.com/p/7c74b8690322)
 4. [analyzing java garbage collection logs debugging optimizing apache sparkTuning Java Garbage Collection for Apache Spark Applications](https://anish749.github.io/spark/analyzing-java-garbage-collection-logs-debugging-optimizing-apache-spark/)
 5. [Tuning Java Garbage Collection for Apache Spark Applications](https://databricks.com/blog/2015/05/28/tuning-java-garbage-collection-for-spark-applications.html)
 6. [Troubleshooting Long GC Pauses](https://blogs.oracle.com/poonam/troubleshooting-long-gc-pauses)
 7. [Garbage First Garbage Collector Tuning](https://www.oracle.com/technetwork/articles/java/g1gc-1984535.html)
 8. 1. [Spark Configuration](https://spark.apache.org/docs/latest/configuration.html)
2. [Spark Streaming Logging Configuration](http://shzhangji.com/blog/2015/05/31/spark-streaming-logging-configuration/)
3. [Analyzing Java Garbage Collection Logs for debugging and optimizing Apache Spark jobs](https://anish749.github.io/spark/analyzing-java-garbage-collection-logs-debugging-optimizing-apache-spark/)