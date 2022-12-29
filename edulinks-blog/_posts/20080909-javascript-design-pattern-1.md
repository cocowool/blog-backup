---
title: Javascript设计模式学习（一）封装和信息隐藏
date: 2008-09-09 14:17
tag: Javascript
keywords: Javascript, 设计模式, 接口, 封装
description: 设计模式是面向对象软件设计开发过程中用来解决特定问题的简介而优雅的解决方案，本文是Javascript设计模式的第一篇，介绍如何通过设计模式实现封装和信息隐藏。
---

> 设计模式是面向对象软件设计开发过程中用来解决特定问题的简介而优雅的解决方案，本文是Javascript设计模式的第一篇，在Javascript中我们也可以使用大部分的设计模式来帮助我们提高代码的编写效率和运行质量。

<!-- more -->

在我们编程的过程中，我们应该尽可能的把数据和函数处理信息隐藏在对象内部，在Javascript中，我们怎样来做呢？

虽然Javascript中没有其他面向对象语言的声明公有和私有的关键字，但是我们仍可以通过一些手段来达到这样的效果。我们可以这样理解封装和信息隐藏，信息隐藏是我们的目标，因为我们不想太多无关的信息暴露在对象之外，而封装就是实现我们目标的方法。封装就可以被定义为在对象内部隐藏数据表达和具体实现的细节。

下面来学习下怎么用Javascript来实现接口：


第一种是：Fully Exposed Method

看例子
```javascript
var HouseItem = function(hid,hname,address){
	if(hid==undefined) thrownewError('Hid is not defined!');

	this.hid=hid;
	this.hname=hname||"暂无";
	this.address=address||"暂无";
}

HouseItem.prototype.display = function(container){
	document.getElementById(container).innerHTML="楼盘名称："+this.hname+"<br />";
}
```
这个中，我们讲一个楼房的项目封装到了一类中，只是我们对于他的属性的有效性检查还不是很完整，而且此时属性的获取或者赋予完全是公开的。

我们可以继续将这个例子完善为
```javascript
var HouseItem = function(hid,hname,address){
	this.setHid(hid);
	this.setHname(hname);
	this.setAddress(address);
}

HouseItem.prototype = {
	getHid : function(){
	returnthis.hid;
	},
	setHid : function(hid){
		if(!this.isValid(hid))
			thrownewError('Hid is not defined!');
			this.hid=hid;
	},
	getHname : function(){
		returnthis.hname;
	},
	setHname : function(hname){
		this.hname=hname||"暂无";
	},
	getAddress : function(){
		returnthis.address;
	},
	setAddress : function(address){
		this.address=address||"暂无";
	},
	isValid : function(hid){
		if(hid!=null&&hid!=undefined&&hid!="") {
			return true;
		}
		else{
			return false;
		}
	},
	showHouse : function(){
		document.getElementById("container").innerHTML+="楼盘名称："+this.hname+"<br />";
	}
}
```
怎么样，现在是不是看起来完善很多了，但这还不是最好的方法，不过这种方法很容易理解，也比较容易上手。

## 参考资料

1. [JS设计模式学习_基础篇](https://segmentfault.com/a/1190000019317291)
2. [JavaScript封装的几种方式](https://www.cnblogs.com/sunliyuan/p/6181895.html)


