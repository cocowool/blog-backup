---
title: PhoneGap学习笔记
date: 2012-12-09 20:04
tag: 
keywords: phonegap, 移动APP, Mobile Application
description: 利用PhoneGap可以方便的开发跨平台的移动APP应用。
---

Mobile Application与Web Application的区别：
1、屏幕尺寸区别很大。手机屏幕上是寸土寸金，UI设计还需要考虑到用户手指点击的舒适度。
2、使用的场景不同，手机用户多数在户外的时候使用应用，希望能够在尽可能快的时间内完成任务。
3、用户交互的方式不同。因为手机拥有传感器、指南针等物理部件，因此我们开发程序的时候所需要考虑的会完全不同。
4、手机平台的差异性更加大，不仅操作系统有差异，每个操作系统上的特性都有所不同。

### PhoneGap架构

![](/20121209-phonegap-introduce/201212092003294705.png)

### Android开发环境准备

PhoneGap只是一个框架（Framework），因此并不提供集成开发环境和工具，所有的都需要我们自己准备。
为Android开发所需要准备的材料包括：
1、JDK1.6+
2、Eclipse 3.4 到 3.6
3、Android SDK（Android的SDK可以支持所有版本的OS，需要调试的话还要下载单独的Platform，即AVD）
4、Android ADT Eclipse插件
5、Android AVD for Android 2.2（Android Virtual Device，相当于Platform）
6、PhoneGap SDk for Android

### 具体步骤：
1、下载安装Eclipse。假设你已经在本机安装了JDK1.6+，因为要使用Java Development Environment的支持，所以需要Ecplise版本在3.2以上。Eclipse的下载页面地址：http://www.eclipse.org/downloads/
2、安装Android SDK。下载地址：http://developer.android.com/sdk/index.html
3、安装Eclipse的Android ADT插件
4、如果没有物理的测试机，还需要使用AVD创建模拟器
5、安装PhoneGap库文件（Cordova），PhoneGap捐赠给Apache基金会后，名字改为了Cordova，我们需要从网站上下载源码
现在Google的Android网站上提供了集成环境的下载，这样我们只需要下载一个集成环境，就可以方便de开始开发了。

### 实际的开发案例（Mac OS X下）：
1、将Android的平台工具（platform-tools）和工具（tools）两个目录加入环境变量中
2、进入到Cordova对应的Android版本bin目录下，执行工程创建命令 ./create project_folder package project_name
3、然后再将工程导入到Eclipse中，就可以进行编辑和测试了。

参考资料：
1、Begin PhoneGap
2、PhoneGap
3、Getting Started with Android
4、Cordova
