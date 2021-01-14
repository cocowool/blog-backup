---
title: Firebug Command Line 的使用技巧
date: 2009-05-23 11:15:01
tag: 
keywords: firebug, command line 
description: Command lin 是Firebug中总有用的一个特性。Firebug中的命令行更像“Immediate Window”，你能够在任何时候检测代码中的值，firebug命令行的一个好处就是可以在”设计时“就查看代码。另外还有一个优点就是你可以在命令行写js语句并且让他们立刻执行。
---

Command lin 是Firebug中总有用的一个特性。如果你有Microsoft Visual Studio的使用经验，你就会知道“Immediate Window”和“Watch Window”的作用，我倒是想起了AutoCAD中的Commandline，通过快速的输入命令来完成操作。

Firebug中的命令行更像“Immediate Window”，你能够在任何时候检测代码中的值，firebug命令行的一个好处就是可以在”设计时“就查看代码。另外还有一个优点就是你可以在命令行写js语句并且让他们立刻执行。

firebug commandline的详细的api可以在官方的地址中看到：http://getfirebug.com/commandline.html

下面来详细的介绍一下：

命令行的类型。firebug中有两种类型的命令行：1、单行模式；2、多行模式；

* 1、单行模式。单行模式是firebug的默认命令行模式，它允许我们一次输入一行代码。单行模式的优点是支持自动完成。
  这个很强大的，就像在终端中一样，你还可以使用上下键来调入最近使用过的命令。
* 2、多行模式。多行模式是单行模式的加强版，它允许我们一次输入多行代码并且马上执行。
  这两种模式都有各自的优势，大家可以根据自己的情况选择适合的模式。

Commandline API使用示例。

在使用之前，需要在提醒一下尽管这些API在运行时和DEBUG时都可以使用，但是他们在DEBUG时尤其有用，下面我们就来看看为什么。

API列表：

* 1、$(id)；
* 2、$$(selector);
* 3、$x(path);
* 4、dir(object);
* 5、dirxml(node);
* 6、cd(window);
* 7、clear();
* 8、inspect(object [,tagname]);
* 9、key(object);
* 10、values(object);
* 11、debug(fn) & undebug(fn);
* 12、monitor(fn) & unmonitor(fn);
* 13、monitorEvents(object [, tagname]) & unmonitorEvents(object [, tagName]);
* 14、profile(title) & profileEnd();

1、$(id)。
使用过Prototype的同学看到这个一定很亲切。
对了，他的用法就和Prototype中的$一样，在单行模式中，命令会将选择的元素打印到console中
在多行模式中，你就完全可以像在Prototype中那样使用它了。
对于Prototype不熟悉的同学可以查看参考资料中的连接了解更多的情况。

2、$$()。
返回给定CSS选择器选中的元素数组。
关于CSS Selector（选择器），相信了解一些CSS的人都有一些印象，Jqueyr将Css Selector发扬的很光大。
我在console中试验了几个jquery中的常用写法，看来支持的还是不够好。

3、$x(xpath)。
返回给定xpath下的元素数组。
例如在一个包含多个p的div中，我们可以用下面的代码获得每个p。
var obj = $x('html/body/div/p');
console.log(obj[0].id);
console.log(obj[1].id);
console.log(obj[2].id);

4、dir(object)。
打印一个对象的所有属性，结果形式和在DOM标签中看到的一样。
其实这个用法和console.dir()是一样的。
var obj = $x('html/body/div/p');
dir(obj);

5、dirxml(note)。
打印xml或HTML元素的树形结构。打印的结果就像在HTML标签中看到的一样。
之前我们也接触过console.dirxml()的用法，这两个方法是一样的。

6、cd(window)。
cd是用来在框架布局的不同窗口间切换，不过据说还不是很完善。

7、clear()。
清除控制台的内容，就像在Javascript中使用Console.clear()一样。

8、inspect(object [,TabName])。
在最适合的标签中检测一个元素，也可以通过TabName指定需要打开的标签。
例如：
inspect($('txt1'),'html');
就会打开HTML标签，并且选中id为txt1的元素。

9、keys(boject)。
返回指定名称的对象的所有属性的名称数组。指定的名称可以是Javascript对象，也可以是HTML的DOM元素。

```javascript
function Car(){
  this.Model = "Old Model";
  this.getManufactor = new function(){
	  return "Toyota";
  }
}
var o = new car()
```

我们可以用keys(o)获得car对象的所有属性。

还可以获得HTML元素的所有属性名称，比如 keys($('txt5'));

10、values(object)。
返回对象包含的属性值的数组。
像keys一样，我们既可以用在javascript上，也可以用在html元素上。

11、debug(fn)和undebug(fn)。
在程序的开始处添加或删除断点。

12、monitor(FunctionName)和unmonitor(FunctionName)。
打开或关闭函数调用的记录。
通常，如果我们想知道一个函数是否被执行，我们会加入一个alert()或者console.log()来记录。
这实在是一个累人的工作，特别是如果编辑一个很大的script文件的时候，我们需要找到这个函数，加入alert,console.log
保存然后再运行，我们需要不断地保存、修改、运行来检查函数在那里被执行了。
有了firebug你就不再需要这么累了，你只需要知道这个函数，然后你就可以跟踪它，只要他被执行了，执行情况就会打印在
console窗口中。另外，console还会提供一个指向这个函数的连接。

使用方法    monitor(func1); 找到问题的原因后，我们还可以通过 unmonitor(func1)里解除对于函数的追踪。

13、monitorEvent(object [,types])和 unmonitorEvent(object [,types])。
打开或关闭对于对象动作的记录。

types表示的我们要追踪的事件类型，最常用的就是'mouse'和'key'
完整的参数类型包括：“composition”, “contextmenu”, “drag”, “focus”, “form”, “key”, “load”, “mouse”, “mutation”, “paint”, “scroll”, “text”, “ui”, and “xul”

不过不幸的是，这个API工作地不是很好，希望作者能够在后继的版本中完善一下。

14、profile([title])和profileEnd([title])
对于这个命令，我想看过上一篇的同学应该比较熟悉。这个就和console.profile()是一样的，用来记录页面中js的执行情况
并在结束后给出分析报告。

最后，希望每一位使用firebug来开发的同学，能够提高自己的开发效率。

[参考资料]

1. [firebug commandline](http://michaelsync.net/2007/09/15/firebug-tutorial-commandline-api)
2. [prototype](http://www.prototypejs.org/)












