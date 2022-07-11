---
title: Curl操作Elasticsearch的常用方法
date: 2017-09-27 08:53:01
tag: Elasticsearch
categories: 运维
keywords: elasticsearch, curl elasticsearch
description: Elasticsearch对于文档操作，提供了以下几种API，本文就说明如何使用curl方式来调用这些API。
---

Elasticsearch对于文档操作，提供了以下几种API，本文就说明如何使用curl方式来调用这些API。

### API种类

### 集群操作

查看集群描述和集群版本

```sh
curl -XGET http://192.18.8.7:9203
{
  "name" : "pm_2011_3",
  "cluster_name" : "perf",
  "cluster_uuid" : "xxxxxx-26bA",
  "version" : {
    "number" : "6.3.1",
    "build_flavor" : "default",
    "build_type" : "zip",
    "build_hash" : "eb782d0",
    "build_date" : "2018-06-29T21:59:26.107521Z",
    "build_snapshot" : false,
    "lucene_version" : "7.3.1",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}

```



1、检查集群的健康状态

```sh
curl -XGET http://192.18.8.7:9203/_cat/health?v
epoch      timestamp cluster      status node.total node.data shards  pri relo init unassign pending_tasks max_task_wait_time active_shards_percent
1657161542 10:39:02  performance6 green          31        22  13138 6569    0    0        0             0                  -                100.0%
```



1、获取集群节点



#### 单文档操作API

1、* Index API 索引文档 *
为文档创建索引

```bash
curl -XPUT "http://localhost:9200/twitter/tweet/1"; -H 'Content-Type: application/json' -d'
{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}'
```
返回结果
```javascript
{
    "_shards" : {            
        "total" : 2,        #表示应该在多少个节点执行操作
        "failed" : 0,       #表示失败的个数
        "successful" : 2    #表示成功的个数，正常情况最小应该是1
    },
    "_index" : "twitter",
    "_type" : "tweet",
    "_id" : "1",
    "_version" : 1,
    "created" : true,
    "result" : created
}
```
> 上面的例子，如果索引不存在，则会自动创建索引及动态映射关系。如果想要关掉这两个特性，可以修改节点上配置文件中```action.auto_create_index```以及```index.mapper.dynamic```两项的值为 false。

> 使用这个API发送两次请求，即便插入的数据一模一样，仍然会在索引中创建两个文档。如果不能接受这个结果，那就需要使用 _update API，并将```detect_noop```参数打开。

2、* GET API 获取文档 *
该API能够基于文档ID获取一份格式化的JSON文档。除了支持通过GET获取文档信息，也支持通过HEAD方法检查文档是否存在。
```bash
curl -XGET 'localhost:9200/twitter/tweet/0?pretty'
curl -XHEAD 'localhost:9200/twitter/tweet/0?pretty'
```
返回结果如下
```javascript
{
    "_index" : "twitter",
    "_type" : "tweet",
    "_id" : "0",
    "_version" : 1,
    "found": true,
    "_source" : {
        "user" : "kimchy",
        "date" : "2009-11-15T14:12:12",
        "likes": 0,
        "message" : "trying out Elasticsearch"
    }
}
```
3、* Delete API 删除文档 *
该API允许我们根据ID删除某个索引中的文档。
```bash
curl -XDELETE 'localhost:9200/twitter/tweet/1?pretty'
```
结果如下
```javascript
{
    "_shards" : {
        "total" : 2,
        "failed" : 0,
        "successful" : 2
    },
    "found" : true,
    "_index" : "twitter",
    "_type" : "tweet",
    "_id" : "1",
    "_version" : 2,
    "result": "deleted"
}
```
> 删除文档时，如果该索引不存在，则Elasticsearch会自动创建索引和自动映射关系。这个官方文档中有这个文字，但是我自己实验的却没有这样的结果，而是收到 index_not_found_exception 的错误。

4、* Delete By Query API 根据条件删除 *
该API会对满足查询条件的所有文档执行删除操作。示例如下
```bash
curl -XPOST 'localhost:9200/twitter/_delete_by_query?pretty' -H 'Content-Type: application/json' -d'
{
  "query": { 
    "match": {
      "message": "some message"
    }
  }
}
'
```
返回结果
```javascript
{
  "took" : 147,            #
  "timed_out": false,
  "deleted": 119,
  "batches": 1,
  "version_conflicts": 0,
  "noops": 0,
  "retries": {
    "bulk": 0,
    "search": 0
  },
  "throttled_millis": 0,
  "requests_per_second": -1.0,
  "throttled_until_millis": 0,
  "total": 119,
  "failures" : [ ]
}
```
该API接受的URL参数```pretty```、```refresh```、```wait_for_completion```、```wait_for_active_shards```、```timeout```。如果想要获取正在执行的删除人物，可以通过Task API。
```bash
curl -XGET 'localhost:9200/_tasks?detailed=true&actions=*/delete/byquery&pretty'
```
也可以对删除操作进行取消。
```bash
curl -XPOST 'localhost:9200/_tasks/task_id:1/_cancel?pretty'
```
5、* Update API 更新API *
我觉得这是很多人对ELK误解最深的地方，以为ELK不支持更新功能，数据只能一次性导入，其实ELK是有更新API的。更新API首先从ES获取文档，然后根据请求对文档进行更新，最后将更新保存至服务器。这个过程中使用版本号 Version 来确保文档没有被其他人修改过。
```bash
curl -XPUT 'localhost:9200/test/type1/1?pretty' -H 'Content-Type: application/json' -d'
{
    "counter" : 1,
    "tags" : ["red"]
}
'
```
也可以通过Script进行更新
```bash
curl -XPOST 'localhost:9200/test/type1/1/_update?pretty' -H 'Content-Type: application/json' -d'
{
    "script" : {
        "source": "ctx._source.counter += params.count",
        "lang": "painless",
        "params" : {
            "count" : 4
        }
    }
}
'
```
6、* Update by Query API 根据条件更新 *
类似于根据条件查询，这个API可以根据条件对多个文档进行更新。
```bash
curl -XPOST 'localhost:9200/twitter/_update_by_query?conflicts=proceed&pretty' -H 'Content-Type: application/json' -d'
{
  "query": { 
    "term": {
      "user": "kimchy"
    }
  }
}
'
```

#### 多文档操作API
1、* MULTI GET API 获取多个文档*
MULTI GET API允许我们根据索引、类型和ID来获取多个文档，返回结果放在docs数组中。
```bash
curl -XGET 'localhost:9200/_mget?pretty' -H 'Content-Type: application/json' -d'
{
    "docs" : [
        {
            "_index" : "test",
            "_type" : "type",
            "_id" : "1"
        },
        {
            "_index" : "test",
            "_type" : "type",
            "_id" : "2"
        }
    ]
}
'
```
2、**BULK API**
BULK API提供了在一次请求中更新大量文档的可能，这将极大的提高索引的速度。

3、**Reindex API 重建索引**
```_reindex```的基本工作方式是将一个索引拷贝到新的索引中。

```bash
curl -XPOST 'localhost:9200/_reindex?pretty' -H 'Content-Type: application/json' -d'
{
  "source": {
    "index": "twitter"
  },
  "dest": {
    "index": "new_twitter"
  }
}
'
```
3、**Term Vectors**
TODO 待补充

4、**Multi termvectors API**
TODO 待补充

5、**?refresh**
TODO 待补充

> 本文所有示例基于ELK 5.6。

参考资料：
1、[Elasticsearch Docs](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html)
2、[Optimistic Concurrency Control](https://en.wikipedia.org/wiki/Optimistic_concurrency_control)











