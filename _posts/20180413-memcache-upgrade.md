---
title: Memcache的安装与配置
date: 2018-04-13 22:49:11
tag: 
category: 运维
---

因为单位要求修复Memcached的DDOS漏洞，整理了本文。之前的文章[防止Memcached的DDOS攻击另外一个思路](http://www.cnblogs.com/cocowool/p/8665704.html)提到了解决方案，我们使用的版本较低，因此需要对 Memcached 进行升级，有需要的朋友可以参考。

### 旧版本的卸载
如果 Memcached 是通过 yum 或 rpm 安装的，可以通过 yum 删除
```bash
yum remove <package_name>
```

### 新版本的安装
我的环境是 Redhat 6.5 和 6.4，因为最新版的 Memcached 要求 Libevent 2.0 ，系统自带的版本太老了，因此需要先安装 Libevent 再安装 Memcached。我都使用了源码安装的方式，过程比较简单，都是```configure make && make install```。这里只是对遇到的问题总结一下。
> 如果启动的时候看到这个提示：/usr/local/memcached/bin/memcached: error while loading shared libraries: libevent-2.1.so.6: cannot open shared object file: No such file or directory

在 Redhat 6.5 和 6.4 中有不同的解决方案

#### Redhat 6.5
```bash
ln -s /usr/local/lib/libevent-2.1.so.6 /usr/lib64/libevent-2.1.so.6
```

#### Redhat 6.4
```bash
ln -s /usr/local/lib/libevent-2.1.so.6 /usr/lib/libevent-2.1.so.6
```

### 如何把 Memcached 加入到服务中
```bash
cp scripts/memcached.sysv /etc/init.d/memcahced #拷贝启动脚本到init.d目录
ln -s /usr/local/bin/memcached /usr/bin/memcached
chkconfig --add memcached
chkconfig memcached on
```

### Memcache的配置
第一次配置Memcached的时候，需要特别关注 -m, -d, -v 这三个参数。

* -m 设置Memcached可以使用的内存大小（以Mb为单位），Memcached不能完全使用分配的大小，实际上要小一些。所以这个值需要设置在一个安全的范围内。在1.4.x版本或之前，设置为小于48M是不能生效的。
* -d 设置Memcached以守护进行的形式运行。如果使用init脚本启动，则不需要这个参数。
* -v 控制Memcached与STDOUT/STDERR的交互方式。加上这个参数后，可以在命令行中看到Memcache启动和工作过程中的一些输出信息。
* -p 指定监听的端口。TCP -p 修改的时候，只针对TCP的链接请求有效。UDP -U 用来修改UDP的监听端口，UDP对于读取、设置一些比较小的条目时非常有用。如果对于这个端口比较担心，设置为0就可以禁止掉。
* -l 绑定特定的地址
* -s这个配置可以将访问限制在一个单独的本地用户，启用这个配置后，会禁用TCP和UDP的访问。
  连接限制：默认情况下，最大的连接限制是1024。这个的正确配置非常重要，达到最大连接数后，额外的连接会一直等待，直到有多余的slots空闲出来。通过查看“listen_disabled_num”这个状态值能够检查实例是否发生过最大连接数的情况。
  memcached能够轻松的处理访问量的变化，所以即使设置的太高，也不需要担心。但是，设置一定要适合自己的情况，并且有一定的富余。如果你有5个Web前端，每台前端的MaxClients设置为12，那么memcached可能承受的最大访问量就是60。

### ldd 命令

打印依赖的共享库

### LD_DEBUG=libs /usr/local/bin/memcached -v


参考资料：
1、[Memcached Config](http://code.google.com/p/memcached/wiki/NewConfiguringServer)
2、[yum更新软件、删除软件](http://www.jb51.net/LINUXjishu/161302.html)
3、[启动Memcached报错](https://www.cnblogs.com/EasonJim/p/7580220.html#commentform)
4、[error while loading shared libraries: libevent-2.1.so.6 的解决办法](https://blog.csdn.net/aoshilang2249/article/details/78366355)
5、[CentOS 6.6下Memcached 源码安装配置](https://www.linuxidc.com/Linux/2015-09/123019.htm)












