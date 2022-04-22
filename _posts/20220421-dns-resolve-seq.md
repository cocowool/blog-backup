---
title: Linux 下域名解析的一些小知识
date: 2022-04-21 20:01:07
keywords: dns, resolv.conf, linux, 域名解析
description:
---

域名解析的过程。

Linux 操作系统中，域名解析的配置主要位于 `/etc/resolv.conf` 文件中。这里面有两个可能平时大家不太关注的参数，一个是域名解析请求超时设置 timeout，一个是域名解析后的重试次数 attempts，默认情况下，默认超时时间是 5秒，默认请求重试次数是 2次。



## 参考资料

1. [resolv.conf 的超时(timeout)与重试(attempts)机制](https://www.cnblogs.com/549294286/p/9332734.html)
2. [linux下DNS设置以及解析顺序](https://www.cnblogs.com/nerrissa/p/4346355.html)
