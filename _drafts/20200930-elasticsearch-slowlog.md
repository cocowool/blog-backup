---
title: Elasticsearch 慢查询日志
tags:
---

## Elasticsearch 慢查询日志配置方法

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