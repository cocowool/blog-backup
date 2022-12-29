---
title: Tomcat内存设置
date: 2018-05-28 20:31:02
tags:
keywords: Tomcat,  内存设置, JAVA_OPTS
description: Tomcat内存设置的具体方式。
---

### Windows 下 Bat 方式启动的设置

在 tomcat_home / bin 目录下找到 catalina.bat ，用文本编辑器打开，添加一行：

```bat
set JAVA_OPTS= -Xms1024M -Xmx1024M -XX:PermSize=256M -XX:MaxNewSize=256M -XX:MaxPermSize=256M
```

### Windows 下服务方式启动的设置

修改注册表

HKEY_LOCAL_MACHINE\SOFTWARE\Apache Software Foundation\Tomcat Service Manager\Tomcat5\Parameters\JavaOptions 

### 各参数的解释

参数|描述
--|--
-Xms1024M|初始化堆内存大小
-Xmx1024M|最大堆内存大小
-XX:PermSize=256M|初始化类加载内存池大小
-XX:MaxPermSize=256M|最大类加载内存池大小

### Tomcat 内存溢出的几种情况

Tomcat 内存溢出有几种形式：
#### 1、OutOfMemoryError： Java heap space 堆溢出

JVM堆的设置是指java程序运行过程中JVM可以调配使用的内存空间的设置。JVM在启动的时候会自动设置Heap size的值，其初始空间(即-Xms)是物理内存的1/64，最大空间(-Xmx)是物理内存的1/4。可以利用JVM提供的-Xmn -Xms -Xmx等选项可进行设置。Heap size 的大小是Young Generation 和Tenured Generaion 之和。

在JVM中如果98％的时间是用于GC且可用的Heap size 不足2％的时候将抛出此异常信息。 

#### 2、OutOfMemoryError： PermGen space 

PermGen space 的全称是 Permanent Generation space，是指内存的永久保存区域，这块内存主要是被JVM存放Class和Meta信息的，Class在被Loader时就会被放到PermGen space中，它和存放类实例(Instance)的Heap区域不同，GC(Garbage Collection)不会在主程序运行期对PermGen space进行清理。

#### 3、OutOfMemoryError： unable to create new native thread. 

前两种情况，如果应用本身没有内存泄漏，可以调整JVM参数来解决。

参考资料
1、[tomcat内存设置之startup.bat启动方式](https://blog.csdn.net/longtingjing/article/details/51258388)
2、[Tomcat内存溢出的几种情况](https://blog.csdn.net/dekyou/article/details/51595461)