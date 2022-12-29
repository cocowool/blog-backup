---
title: CentOs 5.2设置DVD为本地yum源
date: 2010-01-07 16:25:01
tag: 
keywords: centos, yum, 本地YUM源
description: CentOS本地YUM源的配置方法。
---

有很多种方法，这里我用了一个比较偷懒的办法。
进入 /etc/yum.repo.d/ 文件夹
vi Centos-Media.repo
将文件中的 file 位置修改为dvd的位置 /media/dvd，并打开本地源，如下：

```
[c5-media]
name=CentOS-$releasever - Media
baseurl=file:///media/dvd/
gpgcheck=0
enabled=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-5
~
```
保存推出后就可以使用本地的dvd作为yum源了，当然，你要记得把光盘放进去哦












