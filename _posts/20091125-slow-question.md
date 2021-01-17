---
title: Apache服务器访问过慢分析及解决
date: 2009-11-25 15:17:01
tag: 
keywords: apache, apache linux, apache访问慢, linux
description: 线上的一台服务器，最近总是出现 访问 很慢的情况发生，点击一个链接要2秒钟以上才能打开，按照我们对于访问人数的估计，服务器应该不至于响应这么慢，从而需要针对这个问题进行分析，来解决网站访问过慢。
---

起因：线上的一台服务器，最近总是出现 访问 很慢的情况发生，点击一个链接要2秒钟以上才能打开，按照我们对于访问人数的估计，服务器应该不至于响应这么慢，从而需要针对这个问题进行分析，来解决网站访问过慢。

分析：
1、首先，在页面访问变慢情况发生时，使用 top 命令查看了服务器的负载情况，发现负载并不高，初步估计不是程序的问题。

2、然后，查看了线程中的 httpd 的数量， `ps -aux | grep httpd | wc -l `发现，线程数已经达到了 apache 设置的最大值。由此断定是网站访问人数过多造成了访问过慢。

3、为了验证，查看了连接数和当前的连接数，分别是

```sh
netstat -ant | grep $ip:80 | wc -l
netstat -ant | grep $ip:80 | grep EST | wc -l
```


发现果然，连接数特别多，远远超过我们的估计值。

4、刚开始的时候，对于服务器的 MPM 配置方式不是特别的熟悉，认为修改服务器配置可以解决问题。主要的配置部分包括 prefork 模式 或者 work 模式的配置，下面是一些简单的介绍。

prefork 模式：
以 prefork 模式工作的 apache 的默认配置：

```httpd
<IfModule mpm_prefork_module>
ServerLimit             2000
StartServers               5    #指定服务器启动时建立的子进程数量
MinSpareServers            5    #指定空闲子进程的最小数量
MaxSpareServers           10    #指定空闲子进程的最大数量
MaxClients               150    #指定同一时间客户端最大接入请求的数量（单个进程并发线程数），任何超过该限制的请求都将进入等候队列，一旦一个连接被释放，队列中的请求将得到服务
MaxRequestsPerChild        0    #指定每个子进程在其生存周期内允许伺服的最大请求数量，默认为10000，0表示子进程永远不结束
</IfModule>
```

prefork 控制进程在最初建立“StartServers”个子进程后，为了满足 MinSpareServers 设置的需要创建一个进程，等待一秒钟，继续创建两个，再等待一秒钟，继续创建四个……如此按指数级增加创建的进程数，最多达到每秒32个，直到满足MinSpareServers设置的值为止。这种模式可以不必在请求到来时再产生新的进程，从而减小了系统开销以增加性能。

MaxSpareServers 设置了最大的空闲进程数，如果空闲进程数大于这个值，Apache会自动kill掉一些多余进程。这个值不要设得过大，但如果设的值比 MinSpareServers小，Apache会自动把其调整为 MinSpareServers+1。如果站点负载较大，可考虑同时加大MinSpareServers和MaxSpareServers。

MaxClients是这些指令中最为重要的一个，设定的是 Apache可以同时处理的请求，是对Apache性能影响最大的参数。其缺省值150是远远不够的，如果请求总数已达到这个值（可通过ps -ef|grep httpd|wc -l来确认），那么后面的请求就要排队，直到某个已处理请求完毕。这就是系统资源还剩下很多而HTTP访问却很慢的主要原因。虽然理论上这个值越大，可以处理的请求就越多，但Apache默认的限制不能大于256。在 apache2 中通过ServerLimit指令无须重编译Apache就可以加大MaxClients。

虽然通过设置ServerLimit，我们可以把MaxClients加得很大，但是往往会适得其反，系统耗光所有内存。以一台服务器为例：内存2G，每个apache进程消耗大约0.5%（可通过ps aux来确认）的内存，也就是10M，这样，理论上这台服务器最多跑200个apache进程就会耗光系统所有内存，所以，设置MaxClients要慎重。

worker 模式：
以 worker 模式工作的 apache 的默认配置为：

```httpd
<IfModule mpm_worker_module>
StartServers               2
MaxClients               150
MinSpareThreads           25
MaxSpareThreads           75
ThreadsPerChild           25
MaxRequestsPerChild        0
</IfModule>
```

Worker 由主控制进程生成“StartServers”个子进程，每个子进程中包含固定的ThreadsPerChild线程数，各个线程独立地处理请求。同样，为了不在请求到来时再生成线程，MinSpareThreads和MaxSpareThreads设置了最少和最多的空闲线程数；而MaxClients 设置了同时连入的clients最大总数。如果现有子进程中的线程总数不能满足负载，控制进程将派生新的子进程。

MinSpareThreads和 MaxSpareThreads的最大缺省值分别是75和250。这两个参数对Apache的性能影响并不大，可以按照实际情况相应调节。

ThreadsPerChild是worker MPM中与性能相关最密切的指令。

ThreadsPerChild的最大缺省值是64，如果负载较大，64也是不够的。这时要显式使用 ThreadLimit指令，它的最大缺省值是20000。

Worker模式下所能同时处理的请求总数是由子进程总数乘以ThreadsPerChild 值决定的，应该大于等于MaxClients。如果负载很大，现有的子进程数不能满足时，控制进程会派生新的子进程。默认最大的子进程总数是16，加大时也需要显式声明ServerLimit（最大值是20000）。需要注意的是，如果显式声明了ServerLimit，那么它乘以 ThreadsPerChild的值必须大于等于MaxClients，而且MaxClients必须是ThreadsPerChild的整数倍，否则 Apache将会自动调节到一个相应值。

服务器的apache采用的是 prefork 的工作模式，对 MaxClients 进行了相应的调整，发现服务启动后很短时间，连接数就能够达到最大。

5、后来想到需要查看用户都是访问的那些页面，将配置中的 access_log 打开，发现85%以上的访问都是直接访问的资源文件，由此判定，用户可能使用了多线程的下载工具，或者这些资源遭受了盗链。

6、找到了问题所在，进行解决也就比较好办了。想到了两个方法：
A、对单个IP进行连接的线程限制，不允许多线程连接资源。
对于IP限制，我采用了 mod_limitipconn 这个模块。这个模块的好处是比较简单，缺点是不能够针对单独的文件夹或者文件进行设置，而且不支持虚拟主机。
在 apache 中安装了这个模块后，在配置文件中添加如下几段就可以生效了：
```apache
ExtendedStatus On
< IfModule mod_limitipconn.c >
  < Location / >   # 所有虚拟主机的/目录
    MaxConnPerIP 3     # 每IP只允许3个并发连接
    NoIPLimit image/*  # 对图片不做IP限制
  < /Location >
  < Location /mp3 >  # 所有主机的/mp3目录
    MaxConnPerIP 1         # 每IP只允许一个连接请求
    OnlyIPLimit audio/mpeg video    # 该限制只对视频和音频格式的文件
  < /Location >
< /IfModule >
```
B、添加URL重写，防止盗链。
防止盗链，一个重要的方法就是判断请求的 refer，但是如果使用一些浏览器发出请求的时候将 refer 去掉，或者伪装，这个办法就无能为力了。但是貌似还有更高级的方法，还是可以实现这个功能。
安装apache的 mod_rewrite 模块后，在apache配置文件中添加

```httpd
RewriteEngine On
RewriteCond %{HTTP_REFERER} !^[http://abc.com/.*](http://abc.com/.*)$ [NC]
RewriteCond %{HTTP_REFERER} !^[http://abc.com](http://abc.com)$ [NC]
RewriteCond %{HTTP_REFERER} !^[http://www.abc.com/.*](http://www.abc.com/.*)$ [NC]
RewriteCond %{HTTP_REFERER} !^[http://www.abc.com](http://www.abc.com)$ [NC]
RewriteRule .*\.(gif|jpg|swf)$[http://www.abc.com/about/nolink.png](http://www.abc.com/about/nolink.png)[R,NC]
```

这样盗链的请求会被重定向到一个错误页面，从而减少下载带给服务器的压力。

参考资料：
1、[部署 Apache 的一些技巧。](http://bbs.phpchina.com/viewthread.php?tid=99620)
2、[Apache Server 负载能力测试](http://www.cnscn.org/htm_data/703/0806/15397.html)
3、[Apache AB](http://httpd.apache.org/docs/2.0/programs/ab.html)
4、[Apache的参数设置](http://blog.csdn.net/parine26/archive/2009/05/09/4162455.aspx)
5、[Ab的用法](http://www.lc365.net/blog/b/8621/)
6、[Apache限制连接数和并发数](http://www.diybl.com/course/6_system/linux/Linuxjs/2008927/146147.html)
7、[Apache安装mod_rewrite模块](http://www.admin99.net/read.php/278.htm)
8、[Apache防盗链的简单实现](http://www.xiaohui.com/dev/server/20070330-apache-anti-leech.htm)











