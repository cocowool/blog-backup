---
title: Java 编译过程
date: 2021-07-15 08:03:14
tags:
keywords: java, java编译
description: 介绍Java编译的方法和参数。
---

## 编译

javac 命令用于编译Java源文件，语法为：`javac [ options ] [ sourcefiles ]

如下示例：
```sh
$ javac Main.java
$ javac -cp 1.jar:./lib/2.jar Main.java
$ javac -Djava.ext.dirs=./lib Main.java
```
其中 options 的主要选项有：
* `-sourcepath <path>`	指定 Java 源文件的路径
* 

### 注意事项
* 一个`java`文件中可以有多个类，但是只能有一个用`public`修饰的类，并且这个类的名字要和`java`文件的名字相同（区分大小写）。
* 如果整个文件都没有 `public`类，则文件名可以与所有类不相同。

## 运行
Java程序运行必须指定一个带有`main`函数的`main class`。

### java 运行
一个工程打成jar包后，可以使用`java -jar`命令运行。
```sh
$ java -jar test.jar
```

如果这个jar包中引用了其他第三方的jar包，可能会出现 `NoClassDefFoundError` 的错误。
```sh
Exception in thread "main" java.lang.NoClassDefFoundError: org/apache/log4j/Logger
	at com.edulinks.Baseline.<clinit>(Baseline.java:8)
```

解决方法有四种种。

#### 第一种 -Xbootclasspath
在运行的时候可以使用如下的参数
* `-Xbootclasspath:` 完全取代系统Java classpath。不建议这么使用，最好是不用。
* `-Xbootclasspath/a:` 在系统的Java classpath后附加，一般用这个。
* `-Xbootclasspath/p:` 在系统的Java classpath前附加，注意使用，可能会造成与系统包冲突。

```sh
$ java -Xbootclasspath/a:lib/metrics-core-2.2.0.jar:lib/slf4j-api-1.7.2.jar -jar target/learn-metrics-1.0.jar
```

第二种，使用`-cp`参数，指定目录和jar包的搜索路径。不支持通配符，需要列出所有的jar包。
```sh
$ java -Djava.ext.dirs=~/lib:/project/lib -cp /project/lib com.edulinks.Main
```

第三种修改MANIFEST。

第四种使用自定义的Classloaer。

## 参考资料
1. [cmd命令javac运行类（依赖jar包）](https://blog.csdn.net/myfmyfmyfmyf/article/details/53179395)
2. [一个.java文件中放多个类的问题](https://www.cnblogs.com/taizhang/p/9001539.html)
3. [JAVA一个文件写多个类 ( 同级类 ) 规则和注意点](https://blog.csdn.net/q5706503/article/details/84349692)
4. [java -jar命令运行jar包时指定外部依赖jar包](https://blog.csdn.net/w47_csdn/article/details/80254459)
5. [JDK中的 javac / java / jar 命令](https://www.cnblogs.com/pengxl/archive/2010/12/10/1902082.html)