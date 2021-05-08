---
title: CoffeeScript学习笔记
date: 2013-05-21 23:49:01
tag: 
keywords: coffeescript, coffeescript学习笔记
description: CoffeeScript编程语言构建于Javascript之上，它可编译成高效JavaScript。可以在Web浏览器上，或者结合Node.js一类的技术构建服务端应用程序。
---


CoffeeScript编程语言构建于Javascript之上，它可编译成高效JavaScript。可以在Web浏览器上，或者结合Node.js一类的技术构建服务端应用程序。

The golden rule of CoffeeScript is: "It's just JavaScript".

**CoffeeScript的吸引力**

1、提供一种比较简单的语法，减少了样板代码，诸如括号和逗号；
2、使用空格作为一种组织代码块的方法；
3、提供拥有表达函数的简单语法；
4、提供基于类的继承（可选项，但是在进行应用程序开发时非常有用）

**先决条件**

CoffeeScript使用Nodejs的包管理程序，作为Node.js的一个包进行分布。
CoffeeScript必须被编译，其编译器实际上就是使用CoffeeScript编写的，所以，需要一个JavaScript运行时来完成其编译。

**安装方法**

sudo npm install -g coffee-script

安装成功后，我们应该能够在命令行中找到 coffee 命令了。coffee -v 我这里看到的是 1.6.0。使用 coffee 命令，可以将 .coffee 文件编译成 js 文件。使用 coffeescript 的格式来写 javascript 据说可以节省三分之二的代码量哦。

**基本用法**

* -c,--compile          将.coffee编译为.js文件
* -w,--watch            监控文件的变化，并输出监控的结果
* -o,--output [DIR]     将编译的结果文件输出到指定目录
* -p,--print            将编译结果
* -l,--lint             如果安装了 jsl (javascript lint)的话，使用lint检查代码
* -s,--stdio            将其它程序的输出作为coffee的标准输入，并且获得JavaScript的标准输出。
* -e,--eval             命令行的形式

![](/20130521-coffeescript-introduce/21234823-2d3cf3d499174fbe91b146ef9f2c8063.png)

参考资料：
1、[初步了解CoffeeScript：第一部分入门](http://www.ibm.com/developerworks/cn/web/wa-coffee1/index.html)
2、[CoffeeScript](http://coffeescript.org/)
3、[Wiki CoffeeScript](http://zh.wikipedia.org/zh-cn/CoffeeScript)
