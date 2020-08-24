---
title: Firebug中的console tab使用总结
date: 2009-05-12 10:44:01
tag: 
---

Firebug对于Web开发人员来说，已经成为了不可或缺的工具，但是在我日常的工作中，常常感觉还没有能够深刻的挖掘出她的潜力，今天花了点时间仔细研究了Console和命令行的使用在提高工作效率方面的作用，
记下来和大家分享一下.

Firebug一共有Console,HTML,CSS,Script,DOM,NET六个Tab，今天着重说一下Console的用法。
其实我们对于Console应该非常熟悉，因为这里是Firebug给出各种信息的窗口，而这也正是Console的主要用途，日志记录(Logging)。
除此之外，Console还提供了通过命令行方式来调试Javascript的方法。下面就来学习一下Console的用法。

1、Firefox的日志记录(Logging in Firefox)。
通过Console的记录方法，我们可以不再使用烦人的alert或者document.write方法来进行调试。
Firebug提供了五种日志的类型：
console.log:记录一行信息，无任何图标提示；
console.debug:记录一行信息，带超链接，可以链接到语句调用的地方；
console.error():向控制台中写入错误信息，带错误图标显示和高亮代码链接；
console.info():向控制台中写入提示信息，带信息图标显示和高亮代码链接；
console.warn():向控制台中写入警告信息，带警告图标显示和高亮代码链接；

consle打印字符串支持字符串替换，使用起来就像c里面的printf(“%s",a),支持的类型包括：
%s        string，字符串
%d,%i    整型
%f        浮点
%o        对象
如果使用%o的话，对象就会用绿色的超链接表示出来，单击后会将你带到DOM视图。

2、分组(Grouping)。
如果某一类的信息特别多时，分组就有利于逻辑的划分。
使用上很简单，参见代码。
function consoleGroup(){
var groupname = "Group 1";
console.group("Message group %s", groupname);
console.log("This is the 1 message in %s", groupname);
console.log("This is the 2 message in %s", groupname);
console.log("This is the 3 message in %s", groupname);
console.groupEnd();

goupname = "Group 2";
console.group("Message group %s", goupname);
console.log("This is the 1 message in %s", goupname);

var subgroupname = "Sub group 1";
console.group("Message group %s",subgroupname);
console.log("This is the 1 message in %s", subgroupname);
console.log("This is the 2 message in %s", subgroupname);
console.log("This is the 3 message in %s", subgroupname);
console.groupEnd();

console.log("This is the 2 message in %s", goupname);
console.groupEnd();
}


3、console.dir和console.dirxml
console.dir可以将一个对象的所有方法和属性打印出来，这个方法无疑是非常有用的，我们不再需要object.toString这样的方法支持了，只要有firebug，查看对象也变得很轻松
同时，我们也可以将页面中的元素作为一个对象打印出来，但是你要小心，因为这将输出众多的信息，可能你会迷失在繁杂的信息中而找不到自己需要的条目。
我们可以通过分组将这些大量的信息放入一个分组中，这样可以在逻辑上更清楚一些。
function consoleDir(){
function Car(){
this.Model = "Old Model";
this.getManu = function(){
return "Toyota";
}
}

var objCar = new Car();
console.dir(objCar);
console.dir(zoo);

var groupname = "Css Style";
console.group("The button Style", groupname);
console.dir(document.getElementById('consoledir').style, groupname);
console.groupEnd();
}


console.dirxml    打印出HTML元素的XML表示形式.

4、断言(console.assert())。
console.assert()可以用来判断一个表达式是否正确，如果错误，他就会打印错误信息在控制台窗口中。

5、追踪(console.trace())。
console.trace()是一个非常有趣的功能。我们先来看看官方的解释：打印Javascript执行时刻的堆栈追踪。
这个函数可以打印出程序执行时从起点到终点的路径信息。
比如如果我们想知道某个函数是何时和如何被执行的，我们将console.trace()放在这个函数中，我们就能够的看到这个函数被执行的路径。
这个函数在调试其他人的源代码时非常有用。

6、计时(Timing)。
console.time(timeName)可以用来计时，这个在我们需要知道代码执行效率的时候特别有用，就不用自己造轮子了。
function consoleTime(){
var timeName = "timer1";
console.time(timeName);
var a = 0;
for(var i = 0; i < 100; i++){
for(var j = 0; j < 100; j++){
//                        console.log('Hello world');
a = a + 1;
}
}

console.log("a = %d", a);
console.timeEnd(timeName);
}

7、Javascript分析器(Javascript Profiler)。
我们可以通过代码console.profile('profileName')或者单击Profiler标签来进行Javascript代码执行的分析。这个功能有点类似于console.time()，可以帮助我们评估
代码的表现，但是能够提供比console.time()更详细的信息。

有三种方法可以调用Javascript profiler。一种是在代码中写入分析脚本，一种是单击profile标签，最后还可以在命令行下输入命令来执行。
执行后，可以看到详细的输出结果，下面对各项进行一些说明：
Function Column：显示调用的函数名称；
Call Column：显示调用次数；
Percent Column：显示消耗的时间比；
Own Time：显示函数内部语句执行的时间，不包括调用其他函数的时间；
Time Column：显示函数从开始到结束的执行时间；
Avg Column：平均时间。Avg = Own / Call;
Min & Max Column：显示最小和最大时间；
File Column：函数所在的文件；

8、其他的一些选项。
在Console Tab的最右侧有一个Options的选项，在这里可以自己定义需要显示的错误，其内容很好理解，这里就不多说了。
有一点就是Firebug1.3以后，多了
Show Chrome Errors
Show Chrome Message
等几个选项，这几个选项还没有验证过其具体的作用，哪位知道的可以共享一下。

[参考资料]
1、Firebug Tutorial    http://michaelsync.net/2007/09/09/firebug-tutorial-logging-profiling-and-commandline-part-i
2、Firebug Tutorial    http://michaelsync.net/2007/09/10/firebug-tutorial-logging-profiling-and-commandline-part-ii











