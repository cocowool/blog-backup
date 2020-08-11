---
title: 使用Logstash创建ES映射模版并进行数据默认的动态映射规则
date: 2017-08-24 17:03
tag: 
---

> 本文配置为 ELK 即（Elasticsearch、Logstash、Kibana）5.5.1。

Elasticsearch 能够自动检测字段的类型并进行映射，例如引号内的字段映射为 String，不带引号的映射为数字，日期格式的映射为日期等等，这个机制方便了我们快速上手 ELK，但是后期我们经常需要对一些特定的字段进行定制，之前本人有一篇文章进行这方面的尝试[Logstash中如何处理到ElasticSearch的数据映射](http://www.cnblogs.com/cocowool/p/7347069.html)，但对于默认映射规则没有介绍，本文就来探讨一些默认的动态映射规则。

### 开始之前
先拿一个 logstash 的配置文件来看一下
```bash
output {
  elasticsearch {
    hosts => “localhost:9200"
    index => "my_index"
    template => "/data1/cloud/logstash-5.5.1/filebeat-template.json"
    template_name => "my_index"
    template_overwrite => true
  }
  stdout { codec => rubydebug }
}
```
再看一个ES模板配置文件
```javascript
{
  "template" : "logstash*",
  "settings" : {
    "index.number_of_shards" : 5,
    "number_of_replicas" : 1,
    "index.refresh_interval" : "60s"
  },
  "mappings" : {
    "_default_" : {
       "_all" : {"enabled" : true},
       "dynamic_templates" : [ {
         "string_fields" : {
           "match" : "*",
           "match_mapping_type" : "string",
           "mapping" : {
             "type" : "string", "index" : "not_analyzed", "omit_norms" : true, "doc_values": true,
               "fields" : {
                 "raw" : {"type": "string", "index" : "not_analyzed", "ignore_above" : 256,"doc_values": true}
               }
           }
         }
       } ],
       "properties" : {
         "@version": { "type": "string", "index": "not_analyzed" },
         "geoip"  : {
           "type" : "object",
             "dynamic": true,
             "path": "full",
             "properties" : {
               "location" : { "type" : "geo_point" }
             }
         }
       }
    }
  }
}
```
这里关注几个属性```index```、```template_name```、以及模板文件中的```template```。```index```是索引的名称，我们经常会有诸如```index => "logstash-%{+YYYY.MM.dd}”```这样的索引名称，可以按照日期来分割不同的索引。```template_name```对应的是模板名称，```template```这是比较关键的，因为决定了索引是否能够匹配到模板配置，这里应该与```index```相匹配。比如固定的 index 名称，这里就可以是固定名称。对于按日期分隔的，可以使用通配符，例如```logstash-*```。
我就是因为没搞明白这几个属性的对应关系，导致自己的配置没有生效查了很长时间。

参考资料
1、[Logstash中配置默认索引映射（_default_属性）](http://blog.csdn.net/xifeijian/article/details/50823494)
2、[关于动态Mapping和templates](http://ju.outofmemory.cn/entry/50618)












