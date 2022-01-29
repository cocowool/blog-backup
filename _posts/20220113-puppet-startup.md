---
title: Puppet 安装使用体验
date: 2022-01-13 08:35:04
tags:
---

前两天写了一篇文章介绍 [Ansible 的安装与使用](http://www.edulinks.cn/2022/01/04/20220104-run-ansible-offline/) 。最近这两天也想研究下 Puppet 比较下两者的区别，在此记录下使用过程。





从组织角度思考开源软件需要落地的几个要素

* 用户认证。每家单位一般都有自己的用户认证和管理体系，对于人数超过一千的单位来说，新的系统进来不可能全部手工录入或导入这些数据。因为这种方式能搞定存量，却搞不定增量，不应当花费额外的资源去做这方面的维护。
* 权限管理。权限管理与组织架构密切相关，包括业务功能、数据功能的权限划分都离不开组织架构的具体情况。每家单位的组织架构又都不尽相同，有垂直型管理的、有扁平管理的，特别是对于存在多个办公地点的公司，可能还有跨部门、跨中心的虚拟团队的组织形式。
* 流程适配。在小的团队里工作，很多事情通过见面聊一会儿就能解决。在大的团队中，如果没有流程来限定做事的顺序，很多事情就会向意想不到的方向发展。因此，如何把开源软件的功能和本单位的流程实际需要结合起来，这中间需要流程平台的粘合，也需要开源软件具备开放的特性。
* 安全审计。根据公司的实际情况需要选择不同的网络安全等级保护标准，这就会服务器的部署、访问提出很多具体的控制性要求。开源软件在落地的时候，这些问题必须由本地化的团队来考虑。另外，公司的审计管理也会提出很多审计方面的要求。

```
cp -rf  /home/ap/fop/uyun/platform/apollo-adminservice/apollo-adminservice-1.5.1.jar.bak202201220005  /home/ap/fop/uyun/platform/apollo-adminservice/apollo-adminservice-1.5.1.jar   &&  cp -rf /home/ap/fop/uyun/platform/apollo-configservice/apollo-configservice-1.5.1.jar.bak202201220002  /home/ap/fop/uyun/platform/apollo-configservice/apollo-configservice-1.5.1.jar  &&  cp -rf /home/ap/fop/uyun/platform/apollo-portal/apollo-portal-1.5.1.jar.bak202201220008  /home/ap/fop/uyun/platform/apollo-portal/apollo-portal-1.5.1.jar

11.85.3.79,11.85.3.80,11.85.3.81

mv /home/ap/fop/uyun/platform/apollo-portal/config/application-github.properties  /home/ap/fop/uyun/platform/apollo-portal/config/application-github.properties.0121bak   &&  mv /home/ap/fop/uyun/platform/apollo-adminservice/config/application-github.properties  /home/ap/fop/uyun/platform/apollo-adminservice/config/application-github.properties.0121bak


mv /home/ap/fop/uyun/platform/apollo-portal/config/application-github.properties.back  /home/ap/fop/uyun/platform/apollo-portal/config/application-github.properties  &&  mv /home/ap/fop/uyun/platform/apollo-adminservice/config/application-github.properties.back  /home/ap/fop/uyun/platform/apollo-adminservice/config/application-github.properties
```
