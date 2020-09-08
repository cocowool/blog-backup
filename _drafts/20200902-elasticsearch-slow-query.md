---
title: Elasticsearch 慢日志
tags: Elasticsearch
category: 运维
---

[TOC]

> 本文所有代码与配置在 Elasticsearch 7.1.1 中运行和验证。

## Elasticsearch 的日志

Elasticsearch 的通过修改 `ES_HOME/config/log4j2.properties` 文件来配置相关的日志记录，日志文件默认存放在 `ES_HOME/logs` 目录下。

Elasticsearch 提供了三个属性

* `${sys:es.logs.base_path}` 在配置文件中将被解析为日志目录
* `${sys:es.logs.cluster_name}` 在配置文件中将被解析为集群名称
* `${sys:es.logs.node_name}` 在配置文件中将被解析为节点名称
* `${sys:file.separator}` 将被解析为路径分隔符

我编写了一套 [`docker-compose`](https://github.com/cocowool/sh-valley/tree/master/docker-conf/elasticstack/es-kibana) 的编排文件，支持一键式的创建一个单独的 `Elasticsearch` 实例和一个单独的 `Kibana` 实例。 默认情况下 `docker` 创建的 `Elasticsearch` 实例会将日志文件输出到 Console 中便于分析，通过修改 `log4j2.properties` 文件，能够将日志输出到指定的目录。

```properties
status = error

# log action execution errors for easier debugging
logger.action.name = org.elasticsearch.action
logger.action.level = debug

appender.rolling.type = RollingFile
appender.rolling.name = rolling
appender.rolling.fileName = ${sys:es.logs.base_path}${sys:file.separator}${sys:es.logs.cluster_name}_server.json
appender.rolling.layout.type = ESJsonLayout
# appender.rolling.layout.type = PatternLayout
appender.rolling.layout.type_name = server
appender.rolling.filePattern = ${sys:es.logs.base_path}${sys:file.separator}${sys:es.logs.cluster_name}-%d{yyyy-MM-dd}-%i.json.gz

appender.rolling.policies.type = Policies
appender.rolling.policies.time.type = TimeBasedTriggeringPolicy
appender.rolling.policies.time.interval = 1
appender.rolling.policies.time.modulate = true 
appender.rolling.policies.size.type = SizeBasedTriggeringPolicy
appender.rolling.policies.size.size = 256MB
appender.rolling.strategy.type = DefaultRolloverStrategy
appender.rolling.strategy.fileIndex = nomax
appender.rolling.strategy.action.type = Delete
appender.rolling.strategy.action.basepath = ${sys:es.logs.base_path}
appender.rolling.strategy.action.condition.type = IfFileName
appender.rolling.strategy.action.condition.glob = ${sys:es.logs.cluster_name}-* 
appender.rolling.strategy.action.condition.nested_condition.type = IfAccumulatedFileSize
appender.rolling.strategy.action.condition.nested_condition.exceeds = 2GB

```

日志文件会生成在镜像的 `/usr/share/elasticsearch/logs` 目录下，我们还可以修改 `docker-compose.yml` 文件，将日志文件夹映射到本地目录，这样我们查看日志文件的时候，就不需要登陆到容器中。

## 无法加载插件的错误

如果启动的时候发现下面的报错，无法加载各种插件，请检查配置文件相关的行尾是否有空格，我遇到这种错误就是因为配置文件的内容从网上拷贝的时候，行尾带了空格。

```sh
elasticsearch_1  | OpenJDK 64-Bit Server VM warning: Option UseConcMarkSweepGC was deprecated in version 9.0 and will likely be removed in a future release.
elasticsearch_1  | 2020-09-08 11:28:45,379 main ERROR Unable to locate plugin type for TimeBasedTriggeringPolicy 
elasticsearch_1  | 2020-09-08 11:28:45,381 main ERROR Unable to locate plugin type for SizeBasedTriggeringPolicy 
elasticsearch_1  | 2020-09-08 11:28:45,382 main ERROR Unable to locate plugin type for Delete 
elasticsearch_1  | 2020-09-08 11:28:45,473 main ERROR Unable to locate plugin for TimeBasedTriggeringPolicy
```

官方文档也给出了友情提示。

> Log4j’s configuration parsing gets confused by any extraneous whitespace; if you copy and paste any Log4j settings on this page, or enter any Log4j configuration in general, be sure to trim any leading and trailing whitespace.

## 日志级别与配置

`Elasticsearch` 默认的日志级别为 `INFO` ，除此之外还提供了 `TRACE` 、`DEBUG`、`WARN`、`ERROR` 等几个日志级别，并且支持对不同的模块设置日志级别。



## 慢查询日志

Elasticsearch提供了慢日志来捕获并记录那些超过指定时间阈值的查询和索引请求。默认慢日志不开启，开启需要定义具体动作（query、fetch或index），期望的事件记录等级（WARN、DEBUG等），以及时间阈值。

```json
PUT /my_index/_settings
{
  "index.search.slowlog.threshold.query.warn" : "10s", 
  "index.search.slowlog.threshold.fetch.debug": "500ms", 
  "index.indexing.slowlog.threshold.index.info": "5s" 
}
```

也可以在 `elasticsearch.yml` 文件里定义这些阈值，没有阈值设置的索引会自动继承在静态配置文件里配置的参数。

设置阈值后，可以通过API动态切换日志级别。

```json
PUT /_cluster/settings
{
  “transient" : {
  	"logger.index.search.slowlog" : "DEBUG",
    "logger.index.indexing.slowlog" : "WARN"
  }
}
```

| 日志内容 | 描述                                                         |
| -------- | ------------------------------------------------------------ |
|          | 检索时间                                                     |
|          | 日志级别                                                     |
|          | 属于哪个阶段的慢日志                                         |
|          | 节点名称                                                     |
|          | 索引名称                                                     |
|          | query执行的分片序号                                          |
|          | 在上述分片所需的处理时间。**注意：在查看慢速日志时，我们希望避免从不同的分片中添加所有时间，因为每个分片可能并行执行** |
|          | 耗费时间（毫秒）                                             |
|          | 命中数                                                       |
|          | search类型（query_then_fetch)                                |
|          | 索引的总分片大小                                             |
| source[] | 执行检索的具体请求内容                                       |

> 默认情况下，ES会记录_source 中前1000个字符到慢日志中。可以用index.search.slowlog.source进行修改。设置为false或0完全跳过日志记录源，设置为true会记录整个源（无论有多大）。

## 参考资料

1. [Elasticsearch调优篇-慢查询分析笔记](https://www.cnblogs.com/hyq0823/p/12165113.html)
2. [Elasticsearch 官方文档](https://www.elastic.co/guide/cn/elasticsearch/guide/current/logging.html)
3. [ES慢查询收集总结](http://www.fblinux.com/?p=1334)
4. [log4j.properties 配置详解](https://www.cnblogs.com/zhangguangxiang/p/12007924.html)
5. [Elasticsearch搭建和日志配置](https://www.jianshu.com/p/35ff16bb5ab1)
6. [Elasticsearch 集群优化-尽可能全面详细](https://www.cnblogs.com/passzhang/p/12666271.html)
7. [Elasticsearch 7.0 之日志配置](http://www.chaiguanxin.com/articles/2019/05/30/1559202725366.html)
8. [Elasticsearch document : Logging configuration](https://www.elastic.co/guide/en/elasticsearch/reference/master/logging.html)
9. [elasticsearch – 错误无法找到插件类型[用于RollingFile和TimeBasedTriggeringPolicy]](http://www.voidcn.com/article/p-kcgkkojf-byt.html)
10. [Elasticsearch Logging Secrets](https://www.elastic.co/cn/blog/elasticsearch-logging-secrets)



