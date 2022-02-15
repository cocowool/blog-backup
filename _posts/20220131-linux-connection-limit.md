---
title: Linux 在链接极限场景下的丢包现象
date: 2022-01-31 03:26:57
keywords: 长连接, 10万连接, 瓶颈
description: 本文目的是测试单台服务器网络连接数超过10万时，网卡的各项性能是否会发生变化。
---

## 场景需求

想要测试一台服务器链接数很多的情况下（如超过1万、10万），其他的网络操作（诸如 Ping）是否会发生异常。



## 系统参数

系统文件 `/etc/sysctl`

```conf
# 系统级别最大打开文件
fs.file-max = 100000

# 单用户进程最大文件打开数
fs.nr_open = 100000

# 是否重用, 快速回收time-wait状态的tcp连接
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_tw_recycle = 1

# 单个tcp连接最大缓存byte单位
net.core.optmem_max = 8192

# 可处理最多孤儿socket数量，超过则警告，每个孤儿socket占用64KB空间
net.ipv4.tcp_max_orphans = 10240

# 最多允许time-wait数量
net.ipv4.tcp_max_tw_buckets = 10240

# 从客户端发起的端口范围,默认是32768 61000，则只能发起2w多连接，改为一下值，可一个IP可发起差不多6.4w连接。
net.ipv4.ip_local_port_range = 1024 65535
```

系统文件 `/etc/security/limits.conf`

```conf
# 最大不能超过fs.nr_open值, 分别为单用户进程最大文件打开数，soft指软性限制,hard指硬性限制
* soft nofile 100000
* hard nofile 100000
root soft nofile 100000
root hard nofile 100000
```



## 统计语句

```sh
# 统计操作系统的链接数
$ netstat na | grep ESTABLISHED | wc -l
```

* 1个 Socket 长连接占用内存约为 8kb
* 

## 参考资料

1. [Linux Netstat 统计连接数](https://www.cnblogs.com/kabi/p/7018434.html)
1. [如何模拟单机百万TCP连接](https://blog.51cto.com/u_15060546/2641200)
1. [locust - An open source load testing tool](https://locust.io)
1. [Load Testing with Python: Locust](https://steelkiwi.com/blog/load-testing-python-locust-testing-and-bokeh-vis/)
1. [Achieving 100k connections per second with Elixir](https://stressgrid.com/blog/100k_cps_with_elixir/)
1. [The elixir programming language](https://elixir-lang.org)
1. [Python百万级并发测试指南](https://blog.csdn.net/weixin_32172693/article/details/111902760)
