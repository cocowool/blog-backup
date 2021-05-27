---
title: Mac更换SSD硬盘后系统全新安装的方法
date: 2021-05-27 08:21:33
tags:
keywords: Mac
description: Macbook pro更换了SSD硬盘后重新安装操作系统的方法。
---

我有一台2011年初的Macbook Pro，已经升级到了EI Caption，使用的是机械硬盘。为了提高工作效率，准备把光驱位改成SSD硬盘，同时想全新安装OS X EI Caption系统，经过查阅资料，总结出有以下几种办法：

一、最简单也是最慢的办法，直接在线升级。

Macbook Pro 硬盘中默认有一个 Recovery 分区，开机的时候，可以按 Command + R 进入恢复界面。其中有一个选项是从网络重新安装操作系统。这个功能是 Lion （10.7.x）之后引入的，进入到恢复界面后，基本上跟着界面操作说明走就可以了。

使用这个办法，你只需要将原来的硬盘卸下来装入新的SSD，然后再安装。我是将光驱位换成了SSD，安装的时候指定安装到哪个硬盘。

二、另外一个快速一点的方法是使用硬盘盒将SSD装好，然后插入电脑，使用之前下载过的Mac OS X安装文件进行安装，安装的时候将操作系统安装到外部硬盘。安装完成后再替换机械硬盘或者安装到光驱位。

这种办法有一个前提，你需要提前下载系统的安装文件。另外还有就是需要购买一个外置硬盘盒，对于替换的做法比较合适，但对于双硬盘的做法就有一些浪费，安装完成后就会多出来一个硬盘盒。

三、最后一个办法就是创建一个启动盘，不管是USB还是使用外置光驱。对于这种方法，苹果也提供了详细的文档，这里就不再搬砖了。参考 https://support.apple.com/en-us/HT201372。

全新的系统安装完成后，如果你想要从之前的电脑中拷贝数据、应用或者配置文件，可以使用迁移助理（Migration  Assistant），可以选择迁移部分内容。但是对于应用来说，你只能选择迁移全部应用或者不迁移，我觉得如果在今后的版本中，能够让用户选择迁移哪些应用就最好了。

参考资料：
1、[Installing a new SSD drive with clean install in MacBook Pro.](https://www.bleepingcomputer.com/forums/t/631654/installing-a-new-ssd-drive-with-clean-install-in-macbook-pro/)
2、[About MacOS Recovery](https://support.apple.com/en-us/HT201314)
3、[Mountain Lion正式版安装教程](http://apple.tgbus.com/tutorial/system/201111/20111121151703.shtml)
4、[怎样用Time Machine恢复系统](http://mac.pcbeta.com/thread-98886-1-1.html)
