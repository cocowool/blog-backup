---
title: Linux 在链接极限场景下的丢包现象
date: 2022-01-31 03:26:57
keywords: 长连接, 10万连接, 瓶颈
description: 本文目的是测试单台服务器网络连接数超过10万时，网卡的各项性能是否会发生变化。
---

## 场景需求

想要测试一台服务器链接数很多的情况下（如超过1万、10万），其他的网络操作（诸如 Ping）是否会发生异常。



## 系统参数

### 客户端参数配置

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

### 服务端参数配置

系统文件 `/etc/sysctl.conf`

```conf
# 系统最大文件打开数
fs.file-max = 20000000

# 单个用户进程最大文件打开数
fs.nr_open = 20000000

# 全连接队列长度,默认128
net.core.somaxconn = 10240

# 半连接队列长度，当使用sysncookies无效，默认128
net.ipv4.tcp_max_syn_backlog = 16384
net.ipv4.tcp_syncookies = 0

# 网卡数据包队列长度
net.core.netdev_max_backlog = 41960

# time-wait 最大队列长度
net.ipv4.tcp_max_tw_buckets = 300000

# time-wait 是否重新用于新链接以及快速回收
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_tw_recycle = 1

# tcp报文探测时间间隔, 单位s
net.ipv4.tcp_keepalive_intvl = 30

# tcp连接多少秒后没有数据报文时启动探测报文
net.ipv4.tcp_keepalive_time = 900

# 探测次数
net.ipv4.tcp_keepalive_probes = 3

# 保持fin-wait-2 状态多少秒
net.ipv4.tcp_fin_timeout = 15

# 最大孤儿socket数量,一个孤儿socket占用64KB,当socket主动close掉,处于fin-wait1, last-ack
net.ipv4.tcp_max_orphans = 131072

# 每个套接字所允许得最大缓存区大小
net.core.optmem_max = 819200

# 默认tcp数据接受窗口大小
net.core.rmem_default = 262144
net.core.wmem_default = 262144
net.core.rmem_max = 16777216
net.core.wmem_max = 16777216

# tcp栈内存使用第一个值内存下限, 第二个值缓存区应用压力上限, 第三个值内存上限, 单位为page,通常为4kb
net.ipv4.tcp_mem = 786432 4194304 8388608

# 读, 第一个值为socket缓存区分配最小字节, 第二个，第三个分别被rmem_default, rmem_max覆盖
net.ipv4.tcp_rmem = 4096 4096 4206592

# 写, 第一个值为socket缓存区分配最小字节, 第二个，第三个分别被wmem_default, wmem_max覆盖
net.ipv4.tcp_wmem = 4096 4096 4206592
```

系统文件，`/etc/security/limits.conf`

```conf
# End of file
root soft nofile 2100000
root hard nofile 2100000
* soft nofile 2100000
* hard nofile 2100000
```



## 统计语句

```sh
# 统计操作系统的链接数
$ netstat na | grep ESTABLISHED | wc -l
```

* 1个 Socket 长连接占用内存约为 8kb
* 

> pdsh 全称是 parallel distributed shell ，与 pssh 类似，具有批量在远程目标主机操作的能力。

## 参考资料

1. [Linux Netstat 统计连接数](https://www.cnblogs.com/kabi/p/7018434.html)
1. [如何模拟单机百万TCP连接](https://blog.51cto.com/u_15060546/2641200)
1. [locust - An open source load testing tool](https://locust.io)
1. [Load Testing with Python: Locust](https://steelkiwi.com/blog/load-testing-python-locust-testing-and-bokeh-vis/)
1. [Achieving 100k connections per second with Elixir](https://stressgrid.com/blog/100k_cps_with_elixir/)
1. [The elixir programming language](https://elixir-lang.org)
1. [Python百万级并发测试指南](https://zhuanlan.zhihu.com/p/134603634)
1. [pdsh 使用](https://blog.csdn.net/qq_37865420/article/details/117263959)
