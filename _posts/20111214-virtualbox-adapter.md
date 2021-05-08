---
title: VirtualBox克隆后无法找到网卡的问题
date: 2011-12-14 11:31:01
tag: 
keywords: virtualbox, virtualbox克隆网卡
description: VirtualBox克隆后无法找到网卡的问题。
---

Virtualbox现在更新到了4.1.6版本，我记得在之前的版本中，镜像的克隆只能通过命令行的方式来进行，现在已经可以通过界面来进行克隆了，可以说非常的方便。

克隆的时候，我选择了Reinitialize the MAC address of all the network cards。
开机启动之后，发现找不到网卡，无论netwok restart还是reboot都是无效，报错如下。

> Device eth0 does not seem to be present, delayinitialization.

使用参考资料中的 mv /etc/udev/rules.d/70-persistent-net.rules ~ 重启也没有用。
然后定睛看了一下这个文件的内容，发现 NAME 的地方配置的有问题，而且克隆后的机器，网卡配置中的MAC地址没有对应的更新，需要手工修改下。

![](/20111214-virtualbox-adapter/201112141131177606.png)

一切完成后，重新启动服务器，网卡正常工作，OK！

参考资料：
1、[Virtualbox克隆](http://zxh116116.iteye.com/blog/1172039)
2、[VMWare克隆或复制虚拟机后找不到网卡的解决方法](http://hi.baidu.com/52xjm/blog/item/0e7cf6fc98474a98b901a0a2.html)
3、[udev - Linux Man Page](http://linux.die.net/man/8/udev)
