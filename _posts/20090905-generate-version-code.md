---
title: Zend Studio小技巧：自动生成版本信息
date: 2009-09-05 23:44:01
tag: 
---

在查看一些PHP源码的时候，经常可以看到一些非常标准的注释信息
$Id: Database.php 4343 2009-05-08 17:04:48Z jheathco $
这个信息里包括了文件名，SVN版本，提交日期，作者，那么这个信息是如何生成的呢，今天终于发现了，原来是SVN提供的一个auto-props的功能。

在我们使用的Zend Studio 6.1.2中，从SVN检出的项目，单击右键，选择 Team->set keyword，然后选择ID，这样就打开了SVN的auto-props的功能。
接下来，只要我们在编写的PHP文件中加入$Id$，提交SVN的时候，客户端会自动将这个替换为上面那种标准的格式，怎么样，感觉很方便吧。

除此之外，还有其他的几个属性可以设置，分别是：LastChangedDate,LastChangedRevision,LastChangedBy,HeadURL。

使用了这个功能，我们在编写自己的代码的时候，就可以生成统一格式的文件信息而不需要自己手工维护了。











