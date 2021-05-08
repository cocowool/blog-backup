---
title: Gearman的问题分析与深入研究
date: 2011-12-03 14:35:01
tag: 
keywords: gearman, gearman php
description: Gearman是基于PHP开发的一款分布式解决方案。
---

Gearman作为一个优秀的分布式解决方案，已经被众多的公司或者团队所采用，我在之前的[一篇文章](http://www.cnblogs.com/cocowool/archive/2011/08/18/2145144.html)中也有过介绍。但是目前对于woker的执行状态和结果监控，特别是放入后台的worker的监控，官方还没有完善的解决方法。最近，为团队内部做了一次分享，我也得以深入的研究了 Gearmand。


**Gearman的主要作用：**
1、Dispatch 任务调度（无负载调度）
2、Interface 多应用语言间接口
3、Parallel 并行计算


**Gearmand的安装**


Gearmand 的安装有多种方式，在 Ubuntu 和 Debian 的系统中已经提供了[在线安装的方法](http://gearman.org/index.php?id=getting_started)。编译安装的过程也比较简单：

```php
tar xzf gearmand-X.Y.tar.gz
cd gearmand-X.Y
./configure
make
make install
```

安装需要系统提供了 libevent，如果没有编译就会报错。编译安装完成后的提示如下：

![](/20111203-php-gearman/111.png)

其中 libsqlite3、lbdrizzle、libmemcached、libpq、tokyocabinet的支持都是对于持久化来说的，如果需要要确保系统中安装了这些数据库。

安装完成后，gearmand 提供了以下这些配置参数

![](/20111203-php-gearman/222.jpg)

**性能的问题：**

gearmand 中使用到了多线程模型，其线程分为三种：

Listening and management thread 监听和管理线程 － 只有一个：主要负责接受连接请求，并分配给I/O线程
I/O Thread I/O 线程 － 可以有很多个：主要负责读写系统调用，解析数据包
Processing Thread 处理线程  － 只有一个：负责管理列表和Hash表，管理唯一Key、任务的Handle、函数、任务队列

刚才在  gearmand 的参数中看到，-t 可以指定I/O线程的数量。如果 没有指定 t 或者 t = 0，则三个线程合为一个，所有的事情都在一个线程中处理。如果 -t 1 则有一个监听和管理线程、一个I/O和处理线程；如果 -t 2 则每个类别一个线程，如果大于2，则创建更多的I/O线程。

官方提到，a 16 core Intel machine is able to process upwards of 50k jobs per second。

**使用中应当关注的几个问题**：

1、Job Server 的单点问题。

实际上 Job Server 是存在单点问题的，我们只能通过增加冗余 Job Server 的方式来解决这个问题。就如官方的图例所示，下图：

![](/20111203-php-gearman/333.jpg)

我们可以通过配置域名、多个服务器配置列表等方法，在遇到其中一个 Job Server 失败时，将 Client 的请求转向另外的 Job Server。这种情况下，Job Server 中原来存在的任务会发生丢失，除非使用了数据库或其他类似的持久化方法。

2、Job Server 重启后，Worker会自动重新注册。

通过 gearmand -vvvvvvv  分析发现，如果 gearmand 关闭并重新启动后，原来的 worker 会马上与 Job Server 建立连接，并询问是否有任务。

![](/20111203-php-gearman/444.jpg)

3、使用持久队列避免任务的丢失

因为 Gearmand 的队列是放在内存中的，所以宕机或重启事件会导致队列的丢失。为了避免这种情况，可以使用持久队列，将队列存储在一个相对中立的位置。注意，持久队列只对于后台任务有效。

4、Gearman Worker 的无缝重启

我在实现Worker的过程中，采用了PHP脚本，脚本调用外部的配置文件。如果外部的配置文件修改后，需要重新启动脚本才能够使配置文件中修改的变量生效，所以想要坐到无缝的重启。即脚本Stop、Start的过程不会影响正在进行的业务。

对于无缝重启的问题，我总结了如下几个思路进行处理，解决的方法：

1、每次修改完代码后，Worker需要手工重启（先杀死然后启动）。
2、在Worker中设置，单次任务循环完成后，就对Worker进行重启。
3、在Worker中添加一个退出函数，如果需要Worker退出的时候，在Client端发送一个优先级比较高的退出调用。
4、在Worker中检查文件是否发生变化，如果发生了变化，退出并重启自身。
5、为Worker编写信号控制，接受重启指令，类似于 http restart graceful 指令

另外，也可以使用 supervisord 。这个工具暂时还没有了解，有兴趣的朋友可以分享一下。

下面是我在部门内部分享时制作的PPT

**[Gearman Introduction](http://www.slideshare.net/Wonwang/gearman-introduction-10452050)**View more[presentations](http://www.slideshare.net/)from[Wonwang](http://www.slideshare.net/Wonwang).

参考资料：
1、[Using memcache to get results from a gearman background job](http://william.shallum.net/random-notes/usingmemcachetogetresultsfromagearmanbackgroundjob)
2、[Gearman](http://gearman.org/)
3、[PHP Worker Script Automatically Restart On Crash/Exit](http://groups.google.com/group/gearman/msg/5314505c52e92b74?dmode=source)
4、[http://www.supervisord.org](http://www.supervisord.org/)
