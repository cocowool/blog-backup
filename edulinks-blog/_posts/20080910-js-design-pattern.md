---
title: Javascript设计模式学习（三）更多的高级样式
date: 2008-09-10 22:05:01
tag: 
keywords: Javascript, 设计模式, 接口, 封装
description: 设计模式是面向对象软件设计开发过程中用来解决特定问题的简介而优雅的解决方案，本文是Javascript设计模式的第一篇的续篇，介绍如何通过设计模式实现封装和信息隐藏。
---


静态方法和属性

直接上代码吧
```javascript
//静态成员的例子
var House = (function(){
	//私有的静态属性
	var numOfHouse = 0;
	//私有的静态方法
	function isValid(hid){
		if( hid!=null && hid! = undefined && hid != ""){
			return true;
		}else{
			return false;
		}
	}

	//返回构造器
	return function(hid,hname,address){
		//私有属性
		var _hid,_hname,_address;
		this.getHid = function(){
			return _hid;
		}

		this.setHid = function(hid){
			if(!isValid(hid)){
				_hid=hid;
			}
		}

		this.getHname = function(){
			return _hname;
		}

		this.setHname = function(hname){
			_hname=hname||"暂无";
		}

		this.getAddress = function(){
			return _address;
		}

		this.setAddress = function(address){
			_address=address||"暂无";
		}

		numOfHouse++;
		if(numOfHouse>2)
		throw new Error("House:Only 2 House can be created");

		this.setHid(hid);
		this.setHname(hname);
		this.setAddress(address);
	};
})();

//公共的静态方法
House.washWindow = function(){
	alert("Window Washed!");
}

//Public ,non-privileged method
House.prototype = {
	showHouse : function(){
		document.getElementById("container").innerHTML+="静态楼盘："+ this.getHname()+"<br />";
	}
}

//静态方法调用
function staticHouse(){
 try {
 var staticHouse1= new House('123','HouseName -第一个','Beijing');
alert(staticHouse1.getHname());
staticHouse1.showHouse();
 var staticHouse2= new House('123','HouseName -第二个','Beijing');
alert(staticHouse2.getHname());
staticHouse2.showHouse();
 var staticHouse3= new House('123','HouseName','Beijing');
}
 catch (e){
alert(e);
}
}
```

利用之前学过的作用域以及Closures的方法，我们能够创建既能够被公共访问也能够被私有访问的静态成员。可以说，静态成员的操作都是在类层级上而不是实例层级上。上面例子中最关键的两点：一是构造器放在return中，另外一个是最后跟的一对空的括号，这就使得返回的构造器得到了立即的执行；

常量

常量不过就是不能改变的变量，在JavaScript中，我们可以通过创建私有的变量来模拟常量。
直接上代码吧，这个也比较容易看懂，为类定义了一些常量。


```javascript
//常量的简单示例
var Class=( function (){
  var CLASSNAME="I'm Jack!";
  var CREATEDATE="I'm born on Sep 10!";
  var HOMETOWN="I'm from北京";

  var constants={
    HEIGHT:100,
    WIDTH:400
  }

  this.getName= function (){
    return CLASSNAME;
  }

  this.getConstants= function (name){
    return constants[name];
  }

  return  function (){
    this.showName= function (){
      alert(getName());
  };

  this.showConstant= function (name){
    alert(getConstants(name));
  }
};
})();

function useConstant(){
	var cl= new Class();
	cl.showName();
	cl.showConstant("HEIGHT");
}
```

封装的好处：
封装保证了内部数据的完整性，只允许访问器和存取器来访问数据，这样来保证对数据保存和返回的完整控制。这就减少了我们在其他地方对于数据合法性检查的代码。封装还可以使你的对象尽量的保持独立，这就减少了紧耦合，而这正是面向对象设计的最重要的一条原则。通过封装，你的代码的复用性提高了，而你可以很容易的把他们清理出去。

封装的坏处：
由于内部的方法和变量都是隐藏的，所以对封装过的对象做单元测试变得困难。












