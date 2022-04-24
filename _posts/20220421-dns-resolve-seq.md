---
title: Linux 下域名解析的一些小知识
date: 2022-04-21 20:01:07
keywords: dns, resolv.conf, linux, 域名解析
description: 
---

在我们使用互联网和移动应用过程中，域名是一项最基础的服务，之前写过一篇文章介绍了 [与域名相关的几个工具](http://www.edulinks.cn/2020/10/24/20201017-commands-to-domain/) ，本文来分析下域名解析的过程以及与此相关的几个重要参数。

## 域名解析过程

当我们在浏览器的地址栏中输入 www.edulinks.cn 的域名，或者当我们编写程序、通过命令行工具访问某个域名时，就开始进入域名解析的过程。

使用浏览器的场景，根据浏览器的不同，可能会提供浏览器层面的缓存。

如果没有浏览器缓存，接下来会查询操作系统的缓存。

如果操作系统没有缓存，接下来会从系统的 hosts 文件中查询，Linux 操作系统的 hosts 文件位于 `/etc/hosts` 。

如果 hosts 中没有记录，就要从本地 DNS 服务器查找域名。

Linux 操作系统中，域名解析的配置主要位于 `/etc/resolv.conf` 文件中。这里面有两个可能平时大家不太关注的参数，一个是域名解析请求超时设置 timeout，一个是域名解析后的重试次数 attempts，默认情况下，默认超时时间是 5秒，默认请求重试次数是 2次。

## 参考资料

1. [resolv.conf 的超时(timeout)与重试(attempts)机制](https://www.cnblogs.com/549294286/p/9332734.html)
2. [linux下DNS设置以及解析顺序](https://www.cnblogs.com/nerrissa/p/4346355.html)
2. [浅谈域名解析过程](https://zhuanlan.zhihu.com/p/58108010)
