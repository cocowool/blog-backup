---
title: Memcacheq 安装说明
date: 2011-07-28 18:33:30
tags:
keywords: memcacheq, linux memcacheq, memcacheq 安装
description: 本文介绍如何在Linux操作系统下安装 memcacheq。
---

MEMCACHEQ的特性：
* 简单易用
* 处理速度快
* 多条队列
* 并发性能好
* 与memcache的协议兼容。这就意味着只要装了memcache的extension就可以了，不需要额外的插件。
* 在zend framework中使用也很方便。

## 1. 安装前准备
### libevent 支持 1.4.x or later
```sh
[[root@niutian365.com](mailto:[root@niutian365.com) tmp]# rpm -qa | grep libevent
[[root@niutian365.com](mailto:[root@niutian365.com) tmp]# rpm -qa | grep libevent
   libevent-devel-1.4.13-1
   libevent-1.4.13-1
[[root@niutian365.com](mailto:[root@niutian365.com) tmp]#
```
没有的话自行安装，使用rpm安装或者yum安装。其他请参考以下：Download from <http://monkey.org/~provos/libevent/> How to install libevent:
```sh
$tar xvzf libevent-1.4.x-stable.tar.gz
$cd libevent-1.4.x-stable
$./configure
$make
$sudo make install
On a linux, load .so file by add two line in /etc/ld.so.conf:
/usr/local/lib
/usr/local/BerkeleyDB.4.7/lib
Then, run 'ldconfig'.
```

### Berkeley DB 4.7 or later

由于我的系统没安装Berkeley DB故直接编译安装 Download from <http://www.oracle.com/technetwork/database/berkeleydb/downloads/index.html>
```sh
[[root@niutian365.com](mailto:[root@niutian365.com) tmp]# tar xvzf db-4.7.25.tar.gz
[[root@niutian365.com](mailto:[root@niutian365.com) tmp]# cd db-4.7.25/
[[root@niutian365.com](mailto:[root@niutian365.com) tmp]# cd build_unix/
[[root@niutian365.com](mailto:[root@niutian365.com) tmp]# ../dist/configure --prefix=/usr/local/berkeleydb
[[root@niutian365.com](mailto:[root@niutian365.com) tmp]# make
[[root@niutian365.com](mailto:[root@niutian365.com) tmp]# make install
```

编译完成后，将Berkeley Db运行库的路径添加到系统配置里面
```sh
[[root@niutian365.com](mailto:[root@niutian365.com) tmp]# echo "/usr/local/berkeleydb/lib/" >> /etc/ld.so.conf
[[root@niutian365.com](mailto:[root@niutian365.com) tmp]# /sbin/ldconfig
```

## 2.安装memcacheq-0.2.0
```sh
[[root@niutian365.com](mailto:[root@niutian365.com) tmp]# tar xvzf memcacheq-0.2.0.tar.gz
[[root@niutian365.com](mailto:[root@niutian365.com) tmp]# cd memcacheq-0.2.0
[[root@niutian365.com](mailto:[root@niutian365.com) tmp]# ./configure --prefix=/usr/local/memcacheq --enable-threads
[[root@niutian365.com](mailto:[root@niutian365.com) tmp]# make
[[root@niutian365.com](mailto:[root@niutian365.com) tmp]# make install
```

> 编译出现错误：checking for library containing db_create... no
> configure: error: cannot find [libdb.so](http://libdb.so/) in /usr/local/BerkeleyDB.4.7/lib

需要修改 configure 中的BerkeleyDB中的预编译参数vim configure找到bdbdir="/usr/local/berkeleydb"改为

bdbdir="/usr/local/BerkeleyDB.5.1"再次编译

如果使用不同的Berkeley版本，可以修改Makefile中的版本号，来继续安装

## 3.启动
```sh
[[root@niutian365.com](mailto:[root@niutian365.com) tmp]# /usr/local/memcacheq/bin/memcacheq -d -r -u www -p21201 -H /var/www/liuqian/memq -N -v -L 1024 -B 1024 > /var/www/liuqian/mq_error.log 2>&1
```
参数说明:
* -d 守护进程
* -r Maximize core file limit
* -v 详细输出
* -u 以用户的身份来运行
* -H BDB 文件的保存目录
* -N Performance improvement
* -R 太久的日志文件会被删除。
* -L 日志缓存大小，默认是32K。 1024表示1024K。

其它参数：
* -h help
* -vv 更加详细的输出

不使用-d的话，输出会直接显示到控制台

> 注:指定的用户必须有数据文件的读写权限,如这里的/var/www/liuqian/memq和/var/www/liuqian/目录.

## 4.使用

使用以上命令启动mq后，（注意上面的-B参数表示messag的body长度不能超过1024 bytes），使用mq时只需要用到两个命令：set和get：
```sh
  set <queue name> <flags> 0 <message_len>\r\n
  <put your message body here>\r\n
  STORED\r\n
  get <queue name>\r\n
  VALUE <queue name> <flags> <message_len>\r\n
  <your message body will come here>\r\n
  END\r\n
```

可以看到，和memcache协议基本一致，只是把key name换成queue name，而且在set的命令中，忽略了expire_time的参数。毕竟mq的数据存储是存在berkeleyDB中，做了持久化存储，没有内存的过期时间。

当使用set命令时，就向指定的消息队列中写入了一条新消息，也就是向BerkeleyDB中新insert了一条数据，当使用get命令时，就从 指定队列中取出一条新消息，也就是向BerkeleyDB中delete了一条数据。当使用stats查看一个指定队列时，可以看到这个队列一共接收了多 少消息，其中被取出了多少条。

示例：
```sh
  fengbo@onlinegame-10-121:~$ telnet 127.0.0.1 22202
  Trying 127.0.0.1…
  Connected to 127.0.0.1.
  Escape character is ‘^]’.
  set q4 0 0 5
  hello
  STORED
  set q4 0 0 5
  world
  STORED
  stats queue
  STAT q4 2/0
  END
  get q4
  VALUE q4 0 5
  hello
  END
  stats queue
  STAT q4 2/1
  END
```

上面执行了两次set的命令，使用stats queue查看时，可以看到q4的队列中共有消息2条，已取出0条；当使用get取出第一条后，再此使用stats queue查看，q4中消息有2条，其中已取出1条

## 参考资料：
1. [持久化消息队列之MEMCACHEQ初探及安装使用](http://www.niutian365.com/blog/article.asp?id=463)
2. [MemcacheQ安装与使用](http://web2.0coder.com/archives/197)
3. [MemcacheQ](http://memcachedb.org/memcacheq/)
4. [MemcacheQ安装笔记](http://ooft.iteye.com/blog/495339)