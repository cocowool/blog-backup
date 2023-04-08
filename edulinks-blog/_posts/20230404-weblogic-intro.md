---
title: Weblogic Server 入门介绍
date: 2023-04-04 19:41:53
keywords: WebLogic, weblogic server, oracle WebLogic, weblogic 介绍, weblogic 功能
description: Weblogic Server 是 Oracle 公司开发的一款商业中间件，用于集成、部署和管理大型 Web 应用、网络应用或数据库应用的 Java 应用服务器。本文第一次发布时，官方看到 2020年3月30日，Weblogic Server 14.1.1 发布，企业中普遍使用较多的版本应该还以 11g、12c为主。
---

> 本文首发于 我的博客站点 [大江小浪](http://www.edulinks.cn) -[Weblogic Server 入门介绍](http://edulinks.cn/2023/04/04/20230404-weblogic-intro/) ，欢迎大家访问原文查看。
>
> 本文的操作系统环境为：macOS Monterey Version 12.2.1 
>
> 本文的 Docker 版本为：Docker version 20.10.8, build 3967b7d

## 简介

Weblogic Server 是 Oracle 公司开发的一款商业中间件，用于集成、部署和管理大型 Web 应用、网络应用或数据库应用的 Java 应用服务器。本文第一次发布时，官方看到 2020年3月30日，Weblogic Server 14.1.1 发布，企业中普遍使用较多的版本应该还以 11g、12c为主。

其他比较有名的几款商业中间件还包括 IBM 公司的 Websphere，Sun 公司（已被 Oracle 收购）的 Glassfish、Resin等。Apache Tomcat 是最流行的开源中间件。

国内厂商有东方通 TongWeb。

> Webloigc 最早由 Weblogic 公司开发，后并入 BEA 公司，最终被 Oracle 公司收购。

## 使用容器体验

利用容器和已经构建好的镜像，我们可以快速熟悉 Weblogic 的管理界面和相关操作。如果更加的深入了解，还是建议用传统的方式搭建、部署并调整参数。

```sh
# 这个镜像的 console 用户口令为 weblogic / welcome1
$ docker pull ismaleiva90/weblogic12
# -p 是映射容器端口到本地 7001, --rm 是结束后删除，避免遗留过多的实例
$ docker run -it -p 7001:7001 --rm ismaleiva90/weblogic12
```

启动后，可以通过 http://localhost:7001/console 访问控制台页面。

![image-20230408094441004](20230404-weblogic-intro/image-20230408094441004.png)

## 参考资料

1. [Oracle Weblogic Server](https://www.oracle.com/cn/java/weblogic/)
2. [东方通](https://tongtech.com/dft/pctype/25.html)
3. [Weblogic使用、注意事项和实战](https://zhuanlan.zhihu.com/p/411959879)
4. [使用Docker搭建Weblogic服务](https://blog.csdn.net/m0_67391907/article/details/126553673)
5. [Building WebLogic Server Images on Docker](https://docs.oracle.com/middleware/12211/wls/DOCKR/configuration.htm)