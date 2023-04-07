---
title: Weblogic Server 入门介绍
date: 2023-04-04 19:41:53
keywords: WebLogic, weblogic server, oracle WebLogic, weblogic 介绍, weblogic 功能
description: Weblogic Server 是 Oracle 公司开发的一款商业中间件，用于集成、部署和管理大型 Web 应用、网络应用或数据库应用的 Java 应用服务器。本文第一次发布时，官方看到 2020年3月30日，Weblogic Server 14.1.1 发布，企业中普遍使用较多的版本应该还以 11g、12c为主。
---

## 简介

Weblogic Server 是 Oracle 公司开发的一款商业中间件，用于集成、部署和管理大型 Web 应用、网络应用或数据库应用的 Java 应用服务器。本文第一次发布时，官方看到 2020年3月30日，Weblogic Server 14.1.1 发布，企业中普遍使用较多的版本应该还以 11g、12c为主。

其他比较有名的几款商业中间件还包括 IBM 公司的 Websphere，Sun 公司（已被 Oracle 收购）的 Glassfish、Resin等。Apache Tomcat 是最流行的开源中间件。

国内厂商有东方通 TongWeb。

> Webloigc 最早由 Weblogic 公司开发，后并入 BEA 公司，最终被 Oracle 公司收购。

## 容器化部署

```sh
# 这个镜像的 console 用户口令为 weblogic / welcome1
$ docker pull ismaleiva90/weblogic12
$ docker run -it -p 7001:7001 --rm ismaleiva90/weblogic12
```



## 参考资料

1. [Oracle Weblogic Server](https://www.oracle.com/cn/java/weblogic/)
2. [东方通](https://tongtech.com/dft/pctype/25.html)
3. []()
