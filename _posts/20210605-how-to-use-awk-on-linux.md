---
title: Linux下使用awk命令使用
date: 2021-06-04 15:46:38
tags:
---

## awk 介绍

awk 是Linux下常用的文本处理命令，它名字取自1977年最初三个开发人员的首字母，他们是： [Alfred Aho](https://en.wikipedia.org/wiki/Alfred_Aho)、[Peter Weinberger](https://en.wikipedia.org/wiki/Peter_J._Weinberger)、[Brian Kernighan](https://en.wikipedia.org/wiki/Brian_Kernighan)，他们三人是AT&T贝尔实验室的Unix开发人员。

## 使用方法



## 常用命令

本文总结了AWK的一些常见用法。

1、打印文件的前两列（空格为分隔符）   

   awk ‘{print $1,$2}’ awkfile

   grep "fangdudu" * | cut -d " " -f3-

2、逗号为分隔符，打印指定的列

   awk -F , '{print $1,$2,$20,$21,$22}' n_epm_user.sql

3、逗号为分隔符，打印符合条件的记录

   awk -F , '{if ($20==1) print $1,$2,$20,$21,$22}' n_epm_user.sql

4、取文件的第五列，并且将部分内容替换为空

   awk '{print gensub(/\/data1\/static\.house\.sina\.com\.cn\/cricfs\//,"","g",$5) }' /data1/logs

## 参考资料

1. [How to Use the awk Command on Linux](https://www.howtogeek.com/562941/how-to-use-the-awk-command-on-linux/)
2. [awk使用手册](http://fanqiang.chinaunix.net/program/other/2005-09-07/3621.shtml)
