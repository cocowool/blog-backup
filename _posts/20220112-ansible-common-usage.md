---
title: Ansible 常用使用场景
date: 2022-01-12 11:33:50
keywords:
description: 本文整理了 Ansible 常用模块以及常用的使用场景
---



## command 模块

在远程节点上执行命令，使用方式如下：

```sh
$ ansible [host_list] -m command -a "hostname"
```

但这种方式并不是调用 shell 环境去执行命令，所以无法获取环境变量，也不能使用管道符、重定向等。

支持 `chdir` 参数，即执行命令前先切换到指定目录。

```sh
$ ansible [host_list] -m command -a "ls -l chdir=/etc/yum.repos.d"
# command 是 ansible 的默认模块，可以省略
$ ansible [host_list] -a "ls -l chdir=/etc/yum.repos.d"
```

## shell 模块

如果需要在远程的 shell 下执行命令，则需要使用 shell 模块。使用方法与 command 相同，但是命令执行时调用的 `/bin/sh`

```shell
$ ansible [host_list] -m command -a 'echo $LOGNAME'
# 该命令可以打印当前登录的用户名
$ ansible [host_list] -m command -a 'ps -ef | grep java | wc -l'
# 该命令可以统计服务器上运行的 java 程序数量
```

## copy 模块

复制文件，支持从本地复制到服务端，也支持从服务端复制到本地。

```sh
$ ansible [host_list] -m copy -a 'src=/etc/hosts dest=/tmp owner=root mode=0755'
# 从服务端向被管机拷贝文件
$ ansible [host_list] -m copy -a 'content=Hello World! desc/tmp/test.txt owner=root force=yes mode=0755'
# 将内容存为远端文件
```

## 参考资料

1. [Ansible 拷贝文件或目录](https://www.cnblogs.com/pzk7788/p/10213389.html)
2. [Ansible 常用模块](https://www.cnblogs.com/happy-king/p/9013312.html)
3. [Ansible 命令模块](https://blog.csdn.net/bruce_6/article/details/80743578)
