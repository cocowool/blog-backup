---
title: 查看Linux CPU使用率的7中方法
date: 2021-04-09 09:40:53
tags:
keywords:
description:
category: Linux
---

## 1. uptime

uptime 能够显示系统运行了多长时间，同时也能显示最近的负载情况。

```sh
$ uptime
12:23  up  3:51, 2 users, load averages: 1.85 1.51 1.41
```

其中 load averages 的三个数字表示1分钟、5分钟、15分钟系统的平均负载。

## 2. top

top是最常用的查看系统资源使用情况的工具，包括CPU、内存等等资源。top中看到的load average取自`/proc/loadavg`，三个数字是1、5、15分钟内进程队列中平均进程数，包括正在运行的进程+准备好等待运行的进程。

打开top，可以指定更新的周期。输入H，打开隐藏的线程；输入1，可以显示单核CPU使用情况。

`top -H -b -d 1 -n 200 > top.txt`，每隔1秒统计一次，共200次，显示线程细节，并保存到top.txt中。

其中CPU信息对应的含义如下：
- us是user的意思，统计nice小于等于0的用户空间进程，也即优先级为100~120。
- ni是nice的意思，统计nice大于0的用户空间进程，也即优先级为121~139。
- sys是system的意思，统计内核态运行时间，不包括中断。
- id是idle的意思，几系统处于空闲态。
- wa是iowait的意思，统计io等待时间。
- hi是hardware interrupt，统计硬件中断时间。
- si是software interrupt，统计软中断时间。
- 最后的st是steal的意思。

## 3. sar

sar 命令是 System Activity Reporter 的缩写，能够对系统活动情况给出详细的报告，包括文件系统读写情况、CPU使用情况、内存使用情况、磁盘I/O、进程活动等情况，本文只介绍查看CPU的方法和参数。

> `sar`命令来自于`sysstat`工具包，如果提示`sar`命令不存在，需先安装`sysstat`。

语法

```sh
sar -u [ <时间间隔> [ <次数 ] ]
$ sar -u 1 3

```



## 4. perf

perf 也是一款很厉害的综合性性能分析工具，能够提供更加详细的CPU占用数据分析报告。

```sh
$ perf stat
^C
 Performance counter stats for 'system wide':

          9,417.54 msec cpu-clock                 #    2.000 CPUs utilized          
               444      context-switches          #    0.047 K/sec                  
                 5      cpu-migrations            #    0.001 K/sec                  
                 0      page-faults               #    0.000 K/sec                  
   <not supported>      cycles                                                      
   <not supported>      instructions                                                
   <not supported>      branches                                                    
   <not supported>      branch-misses                                               

       4.708869128 seconds time elapsed
```

perf 适用于程序开发过程中，需要对某些进程的CPU占用做详细分析的场景。

## 5. mpstat

mpstat 命令也是 sysstat 工具包中的一个命令，Multiprocessor Statistics的缩写，mstat 提供实时监控能力。在多CPU的操作系统里，mpstat 不仅能查看所有CPU的平均状况信息，而且能够查看特定cpu信息，mpstat最大的特点是可以查看多核心cpu中每个核心的统计数据。

```sh
$ mpstat
Linux 3.10.0-1160.el7.x86_64 (qking-101) 	2021年04月09日 	_x86_64_	(2 CPU)

23时29分12秒  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
23时29分12秒  all    1.88    0.00    0.97    0.05    0.00    0.01    0.00    0.00    0.00   97.09

```

## 6. vmstat

vmstat 命令可以展现指定时间间隔情况下服务器的状态值，包括CPU使用率，内存使用率，虚拟内存交换情况，IO读写情况等等。一般 vmstat 使用时结合两个数字参数来完成的，第一个参数是采样的时间间隔数，单位是秒，第二个参数是采样的次数。

```sh
$ vmstat 1 5
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  0      0 1230192   2108 445532    0    0   222   130   78  115  1  1 98  0  0
 0  0      0 1230224   2108 445532    0    0     0     0   44   63  0  0 100  0  0
 0  0      0 1230224   2108 445532    0    0     0     0   34   59  0  0 100  0  0
 0  0      0 1230224   2108 445532    0    0     0     0   37   59  0  0 100  0  0
 0  0      0 1230224   2108 445532    0    0     0     0   33   55  0  0 100  0  0
```



## 7. pidstat
pidstat主要用于监控全部或指定进程占用系统资源的情况。pidstat首次运行时显示自系统启动开始的各项统计信息，之后运行pidstat将显示自上次运行该命令以后的统计信息。用户可以通过指定统计的次数和时间来获得所需的统计信息。

> pidstat -p ALL---------------------------显示所有的进程统计信息，包括idle进程。
> pidstat -p ALL -t------------------------更加详细的显示了线程统计信息。
> pidstat [option] interval [count]-----周期采样和采样次数

## 参考资料
1. [Linux CPU占用率监控工具小结](https://www.cnblogs.com/arnoldlu/p/9462221.html)
2. [详解mpstat、iostat、sar、vmstat命令的使用](https://blog.csdn.net/qq_39591494/article/details/78418162)
3. []()
4. []()
5. []()