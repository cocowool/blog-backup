---
title: Eclipse Galileo SR2 参数配置
date: 2010-03-10 16:31:01
tag: 
keywords: eclipse, eclipse 参数
description: eclipse参数配置
---

要崩溃了，下载了 eclipse-jee-galileo-SR2-win32.zip ，解压缩，运行后提示：
JVM   terminated. Exit   code=-1
Google了一下，问题出在 eclipse.ini 上，但是发现大家解决这个问题的方法真是千奇百怪：
先来看一下原始的配置文件

> -startup
plugins/org.eclipse.equinox.launcher_1.0.201.R35x_v20090715.jar
--launcher.library
plugins/org.eclipse.equinox.launcher.win32.win32.x86_1.0.200.v20090519
-product
org.eclipse.epp.package.jee.product
--launcher.XXMaxPermSize
256M
-showsplash
org.eclipse.platform
--launcher.XXMaxPermSize
256m
-vmargs
-Dosgi.requiredJavaVersion=1.5
-Xms40m
-Xmx512m

方法一：
将换行的部分改成不换行+空格
> --launcher.XXMaxPermSize
256M
-showsplash
org.eclipse.platform
--launcher.XXMaxPermSize
256m

方法二：
调整内存设置
> -vmargs
-Dosgi.requiredJavaVersion=1.5
-Xms40m
-Xmx256m

方法三：
添加虚拟机
> -vm
E:\Program Files\Java\jdk1.6.0_16\bin\javaw.exe

这三个方法都可以使 Galileo 正常启动，由此可见，这个配置文件实在是很难搞明白。
最后，经过试验，如果我希望能够调整 vm 的内存限制的话，必须采用第三种方法，最后的配置文件如下：
> -startup
plugins/org.eclipse.equinox.launcher_1.0.201.R35x_v20090715.jar
--launcher.library
plugins/org.eclipse.equinox.launcher.win32.win32.x86_1.0.200.v20090519
-product
org.eclipse.epp.package.jee.product
--launcher.XXMaxPermSize
256M
-showsplash
org.eclipse.platform
--launcher.XXMaxPermSize
256m
-vm
E:\Program Files\Java\jdk1.6.0_16\bin\javaw.exe
-vmargs
-Dosgi.requiredJavaVersion=1.5
-Xms512m
-Xmx1024m
-XX:+UseParallelGC
-XX:PermSize=256M
-XX:MaxPermSize=512M












