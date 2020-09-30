---
title: Oracle 11g AWR 报告
tags:
---

[TOC]

> 本文面向没有太多 Oracle 基础知识，但是需要通过 AWR 报告来分析数据库性能或排查问题人员，通过对 AWR 报告的简介，了解其包含的主要信息，然后对一些能够帮助我们分析定位问题的章节做一点稍微详细的介绍。通过阅读本文，期望使读者能够快速抓住阅读 AWR 报告的重点，为分析判断数据库性能是否有问题提供帮助。
>
> 本文示例报告基于 Oracle 11.2.0.3.0 版本生成。

## AWR报告简介

AWR是Oracle 10g版本推出的特性，全称叫做 `Automatic Workload Repository 全自动负载信息库` 。AWR通过对比两次快照收集到的统计信息，来生成报表数据，帮助DBA或开发人员了解

## AWR报告结构



## 如何查看数据库的基本信息



## 如何查看数据库的负载



## 报告重点关注的部分

* 操作系统系统负载情况
* 数据库负载情况
* 慢SQL的情况
* Top 5 Timed Foregroud Events

依次说明

## 总结



## 参考资料

1. [oracle11G AWR使用及分析](https://www.cnblogs.com/david-zhang-index/archive/2012/08/21/2649252.html)
2. [等待事件：db file scattered read(离散读）](https://blog.csdn.net/haojiubujian920416/article/details/81506982)
3. []()

