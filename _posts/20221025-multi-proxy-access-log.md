---
title: 穿透多层代理后Apache的Access日志怎么记录
date: 2022-10-25 14:06:30
tags:
keywords: 
description: 
---

现代的Web服务器都提供了访问日志记录的功能，能够记录发起请求的客户端IP，如果客户端通过代理访问我们的站点，也可以通过 `X-Forwarded-For` 字段来记录用户的真实IP（这需要代理服务器提供添加这个头的支持）。



## 参考资料

1. [Why do Apache access logs sometimes have two host ip addresses?](https://serverfault.com/questions/593913/why-do-apache-access-logs-sometimes-have-two-host-ip-addresses)
1. [apache如何开启反向代理？这篇完整教你如何配置](https://baijiahao.baidu.com/s?id=1732054818637750127&wfr=spider&for=pc)
1. [Apache and X-Forwarded-For Header (XFF)](https://www.loadbalancer.org/blog/apache-and-x-forwarded-for-headers/)
1. [Nginx 反向代理详解](https://blog.csdn.net/weixin_52622200/article/details/120979966)
1. [Pound](https://www.apsis.ch/pound.html)
