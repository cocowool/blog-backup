---
title: 一些常用的Linux命令
date: 2009-01-16 09:39
updated: 2020-06-10 08:11:11
tag: 
keywords: linux, linux命令, linux常用命令, mount, fdisk, tar, unzip, rar, chown
description: 本文介绍了mount, fdisk, tar, unzip, rar, chown几个常用的Linux命令。
---

### mount

将某个硬盘设备或者硬盘分区挂载到某个目录下，这个命令方便在系统中存在多个linux时，在不同的linux之间拷贝文件。

格式如下：

```sh
mount [-t vfstype] [-o options] device dir
```

### fdisk
当我们需要了解硬盘的分区结构时，我们可以通过这个命令来完成任务。

```sh
fdisk -l
```

### tar

经常从网上下载的一些文件后缀为tar或者gz，这些都是压缩格式的文件，想要用他们就离不开tar了。

```sh
tar -xvf xxxx.tar
```

想要将/home目录下的文件压缩到home.tar中，使用如下命令：

```sh
tar cvf home.tar /home/
```

tar的参数非常的多，这个就需要我们在使用的过程中注意利用man这个助手，来帮助我们更改好的使用每个命令。

### unzip

解压缩zip格式的文件，unzip a可以将压缩包解压到当前文件夹。

### rar

这个命令不是默认的，需要安装软件支持后才能使用，然后通过rar e filename 就可以解压缩文件包了。

### chown

该命令用来更改一个文件或者文件夹所属的用户和用户组，可以从字面上来记住这个命令，change ownership，怎么样，现在很容易就记住了吧。
