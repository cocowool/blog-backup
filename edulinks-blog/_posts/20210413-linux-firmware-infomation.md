---
title: Linux下查看CPU等硬件信息
date: 2021-04-12 20:05:19
tags:
keywords: Linux, Linux CPU, Linux硬件信息, Linux查看硬件信息
description: 经常我们需要确定一些Linux系统下的硬件信息，这些命令可以帮助你。
category: Linux
---

测试机器的硬件信息： 

查看CPU信息（型号） 
```sh
# cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c 
# 8  Intel(R) Xeon(R) CPU            E5410   @ 2.33GHz 
```
(看到有8个逻辑CPU, 也知道了CPU型号) 

```sh
# cat /proc/cpuinfo | grep physical | uniq -c 
      4 physical id      : 0 
      4 physical id      : 1 
```
(说明实际上是两颗4核的CPU) 

```sh
# getconf LONG_BIT 
   32 
```
(说明当前CPU运行在32bit模式下, 但不代表CPU不支持64bit) 

```sh
# cat /proc/cpuinfo | grep flags | grep ' lm ' | wc -l 
   8 
```
(结果大于0, 说明支持64bit计算. lm指long mode, 支持lm则是64bit) 
再完整看cpu详细信息, 不过大部分我们都不关心而已. 

```sh
# dmidecode | grep 'Processor Information' 
```

查看内 存信息 
```sh
# cat /proc/meminfo 
# uname -a 
Linux euis1 2.6.9-55.ELsmp #1 SMP Fri Apr 20 17:03:35 EDT 2007 i686 i686 i386 GNU/Linux 
```
(查看当前操作系统内核信息) 

```sh
# cat /etc/issue | grep Linux 
Red Hat Enterprise Linux AS release 4 (Nahant Update 5) 
```
(查看当前操作系统发行版信息) 

查看机器型号 
```sh
# dmidecode | grep "Product Name"  
```

```sh
# dmesg | grep -i eth
```
查看网卡信息 

### 举例说明
- 查看物理CPU的个数
```sh
#cat /proc/cpuinfo |grep "physical id"|sort |uniq|wc -l 
2 
```
- 查看逻辑CPU的个数
```sh
#cat /proc/cpuinfo |grep "processor"|wc -l 
24 
```

- 查看CPU是几核
```sh
#cat /proc/cpuinfo |grep "cores"|uniq 
6 
```

### 参考资料：
1. [OSChina Linux 查看CPU信息、机器型号等硬件信息](http://www.oschina.net/question/12_30886?sort=time)
2. [【linux命令】lscpu、、etc/cpuinfo详解](http://www.cnblogs.com/mengwenlu/p/4791227.html)