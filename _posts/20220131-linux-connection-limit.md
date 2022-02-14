---
title: Linux 在链接极限场景下的丢包现象
date: 2022-01-31 03:26:57
keywords: 长连接, 10万连接, 瓶颈
description: 本文目的是测试单台服务器网络连接数超过10万时，网卡的各项性能是否会发生变化。
---

## 场景需求

想要测试一台服务器链接数很多的情况下（如超过1万、10万），其他的网络操作（诸如 Ping）是否会发生异常。



## 统计语句

```sh
# 统计操作系统的链接数
$ netstat na | grep ESTABLISHED | wc -l
```



## 参考资料

1. [Linux Netstat 统计连接数](https://www.cnblogs.com/kabi/p/7018434.html)
1. [如何模拟单机百万TCP连接](https://blog.51cto.com/u_15060546/2641200)
1. [locust - An open source load testing tool](https://locust.io)
1. [Load Testing with Python: Locust](https://steelkiwi.com/blog/load-testing-python-locust-testing-and-bokeh-vis/)
1. [Achieving 100k connections per second with Elixir](https://stressgrid.com/blog/100k_cps_with_elixir/)
1. [The elixir programming language](https://elixir-lang.org)
