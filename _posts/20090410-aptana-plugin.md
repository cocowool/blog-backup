---
title: Linux下Aptana Plugin For Eclipse的破解
date: 2009-04-10 14:32:01
tag: 
keywords: linux, aptana
description: 以前在Win下使用Aptana Stdio的时候进行过破解，最近一段时间的工作环境切换到了Linux下，而且使用了Aptana的Plugin For Eclipse，过期了一直没有去管，今天花了点时间将他破解掉了。
---

以前在Win下使用Aptana Stdio的时候进行过破解，最近一段时间的工作环境切换到了Linux下，而且使用了Aptana的Plugin For Eclipse，过期了一直没有去管，今天花了点时间将他破解掉了。

<!-- more -->

首先是找到Aptana的核心jar包，在eclipse/plugin/com.aptana.ide.core_1.2.1.020234.jar
这个包中包含了破解的核心文件，这里你要确定你使用的版本，版本号上有细微的差别不会影响破解的进行。

接下来要对这个文件进行操作，谨慎起见最好做一份备份，这是一个很好的习惯。

1、解压缩jar包。
利用jar工具进行
`jar -xvf com.aptana.ide.core_1.2.1.020234.jar`

2、反编译类文件。这里使用jad这个工具来进行java类的反编译，在fedora的源里可以找到这个工具。
`jad -r -ff -d src -s java classes/com/aptana/ide/core/licensing/*.class`
-d 后接的是输出的路径
最后跟要反编译的类文件地址

3、修改ClientKey.java文件
`gedit ./src/com/aptana/ide/core/licensing/ClientKey.java`

这个文件从1.0到1.1有了一些变化，但是不用管
我们主要修改文件中的两个地方：

```java
public String getEmail(){
	return email;    //修改为自己的地址
}

public Calendar getExpiration(){
	Calendar expirationCal = Calendar.getInstance(GMT);
	//expirationCal.setTimeInMillis(expiration);
	expirationCal.set(2020,11,1);    //设置一个过期日期

  return expirationCal;
}
```
4、编译并替换包中ClientKey.class。
使用javac ClientKey.java编译，生成ClientKey.class，然后拷贝到我们解压缩出来的文件夹中

5、重新压缩为jar包，替换原文件。
`jar -cvf com.aptana.ide.core_1.2.2.020234.jar ./*`
将生成的文件替换原来位于eclipse/plugin下的jar包

6、重启eclipse后，你就可以在aptana的licensing中看到过期日期已经设置成了自己想要的时间。











