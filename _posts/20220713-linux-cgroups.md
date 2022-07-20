---
title: 闲聊 namespace 和 cgroups
date: 2022-07-13 20:53:46
keywords: namespace, cgroups, control group, linux, linux cgroups
description:
---

* namespace 的介绍、示例
* cgroups 的介绍、示例

## namespace

Linux namespace 是容器技术的底层支撑，是 Linux 内核为了限制系统资源提供的功能特性。通过 namespace 可以让一部分进程只能看到与自己相关的一些资源，看不到其他进程使用的操作系统资源，避免进程间的相互影响。

Linux 在 2.4 内核中实现了 mount namespace ，并在 2.6 内核中实现了 IPC、Network、PID、UTS 等资源的隔离。

使用 `lsns` 命令可以查看操作系统中命名空间的情况。NS 是 inode number，即命名空间指向的链接文件，如果两个命令空间的ID相同，则表示这两种资源在同一个命名空间中。也可以使用 `ls /proc/[pid]/ns` 命令，以进程的视角查看对应的命名空间。

```sh
$ lsns
        NS TYPE  NPROCS   PID USER COMMAND
4026531836 pid      126     1 root /usr/lib/systemd/systemd --switched-root --system --deserialize 22
4026531837 user     126     1 root /usr/lib/systemd/systemd --switched-root --system --deserialize 22
4026531838 uts      126     1 root /usr/lib/systemd/systemd --switched-root --system --deserialize 22
4026531839 ipc      126     1 root /usr/lib/systemd/systemd --switched-root --system --deserialize 22
4026531840 mnt      123     1 root /usr/lib/systemd/systemd --switched-root --system --deserialize 22
4026531856 mnt        1    18 root kdevtmpfs
4026531956 net      126     1 root /usr/lib/systemd/systemd --switched-root --system --deserialize 22

```

 以一个普通用户执行 `lsns` 命令

```sh
$ lsns
        NS TYPE  NPROCS   PID USER     COMMAND
4026531836 pid        2  4518 testuser -bash
4026531837 user       2  4518 testuser -bash
4026531838 uts        2  4518 testuser -bash
4026531839 ipc        2  4518 testuser -bash
4026531840 mnt        2  4518 testuser -bash
4026531956 net        2  4518 testuser -bash

```

> 看到网上有一些资料介绍 unshare 等操作 namespace 的命令，在 CentOS 7.6 1810 上测试总是报：unshare: unshare failed: Invalid argument 错误。查找到有人在 redhat 的 bugzilla 上问过同样问题，答复这个属于技术预览 Technology Preview ，需要修改参数

## cgroups

cgroups 的全称是 control groups，是 Linux 内核（自2.6版本之后）提供的物理资源隔离机制，从而实现对 Linux 进程或者进程组的资源限制、隔离和统计功能。cgroups 引入了 任务 Task、控制组 control group、层级 hierachy、子系统 subsystem 几个概念。引用知乎 lecury 的一张图来快速了解这几个概念之间的关系。

![img](20220713-linux-cgroups/v2-206ab019ab2d93cd771b545914c0e6ea_1440w.jpg)

得益于 Linux 的虚拟文件系统（VFS），Linux 为用户提供了一个统一的文件系统 API 接口，将 cgroups 的功能和配置暴露给用户。

```sh
# 查看系统的 cgroup 目录
$ ls /sys/fs/cgroup
blkio  cpu  cpuacct  cpu,cpuacct  cpuset  devices  freezer  hugetlb  memory  net_cls  net_cls,net_prio  net_prio  perf_event  pids  systemd

# 查看 subsystem
$ cat /proc/cgroups 
#subsys_name	hierarchy	num_cgroups	enabled
cpuset	7	1	1
cpu	11	1	1
cpuacct	11	1	1
memory	4	1	1
devices	10	20	1
freezer	8	1	1
net_cls	5	1	1
blkio	6	1	1
perf_event	2	1	1
hugetlb	9	1	1
pids	3	1	1
net_prio	5	1	1
```

可以使用 cgexec 命令，在程序运行时指定使用的资源组，来限制程序的资源使用。

> 要使用 cgexec 命令，需要在 Cent OS 7 中安装 `libcgroup-tools` 工具，在 Ubuntu 中安装 `cgroup-tools` 工具。

nice、cpulimit、pstree、nsenter

## 参考资料

1. [Control Group v2](https://www.kernel.org/doc/html/latest/admin-guide/cgroup-v2.html)
2. [Linux资源管理之 cgroups 简介](https://tech.meituan.com/2015/03/31/cgroups.html)
3. [浅谈 linux cgroups 机制](https://zhuanlan.zhihu.com/p/81668069)
4. [nsenter 命令简单介绍](https://blog.csdn.net/qq_35745940/article/details/119900634)
5. [centos - 内核3.10的linux中执行unshare --user /bin/bash报错Invalid argument，是什么原因？](https://www.it1352.com/695828.html)
