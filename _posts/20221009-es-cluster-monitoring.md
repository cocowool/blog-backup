---
title: 利用xpack监控ES集群
date: 2022-10-09 10:24:54
tags:
keywords: elasticsearch, xpack, es 监控, elasticsearch 监控
description: 
---

在 [Elasticsearch监控指南](http://edulinks.cn/2020/03/15/20200315-es-monitoring-guide/) 和 [Elasticsearch几项关键告警指标](http://edulinks.cn/2020/03/14/20200314-es-monitoring-metric/) 中讨论过一些基础的 elasticsearch 集群概念、监控指标和监控方法，其实 elastic 官方提供了一个非常方便的监控工具，在 elasticsearch 2.4 版本之前是做为 Marvel 插件提供，从 elasticsearch 2.4 之后做为了 X-Pack 模块的一部分，其中监控功能是免费功能，本文简要介绍利用 x-pack 监控 ES 集群的常用方法。

> 本文基于容器环境运行 elasticsearch 6.3.1 版本和 8.4.0 版本进行了测试，在我的 [Github/sh-valley/docker-conf](https://github.com/cocowool/sh-valley) 中提供了基于 docker-compose 的配置文件，可以快速的运行起一个 ES + Kibana 的实例，也可以运行起三个 ES 实例加一个 Kibana 实例，大家可以参考。

在 Elasticsearch 6.3.1 版本中，默认没有开启监控功能。可以通过修改 elasticsearch.yml 文件中`xpack.monitoring.collection.enabled` 参数来开启这个功能，也可以在 Kibana 的监控菜单中在线开启这个功能。

![image-20221009211250033](20221009-es-cluster-monitoring/image-20221009211250033.png)

开启监控的功能后，在 Kibana 中点击监控菜单，能看到监控信息总览的情况。

![image-20221009211548185](20221009-es-cluster-monitoring/image-20221009211548185.png)

## 参考资料

1. [elastic document](https://www.elastic.co/guide/en/elasticsearch/reference/6.3/watcher-getting-started.html)
