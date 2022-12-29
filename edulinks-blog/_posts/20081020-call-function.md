---
title: 由.call引起的
date: 2008-10-20 17:55:01
tag: 
keywords: javascript, call函数
description: 本文详细解释了javascript中call函数的使用方法。
---

以前看过的源码里，也有用到过.call的时候，今天的一个例子，也碰到了，为了彻底的明白这个的用法，Google了一下，找到了一些比较有用的资料。

其实所有的函数都有.call这个函数，之外还有.apply,.prototype,.length等一系列的，详细的列表在MSDN上有，这里只说.call：
第一个参数是要调用的函数对象,在函数体内就是this了.剩余参数就是要传递给这个函数的(参数)值.
例如函数aaa(arg1,arg2)和对象bbb
aaa.call(bbb,arg1,arg2);
等效
bbb.ccc = aaa;
bbb.ccc(arg1, arg2);
delete bbb.ccc;

.call方法实际是产生一个函数的临时调用，调用结束后既进行销毁，这样可以节省资源，也非常灵活。

为了加深对于这个的理解，不妨看一下下面的这些解释：

### **1.函数对象和函数指针**

函数实际上是一个可执行的对象，任何访问函数对象的方式都是函数指针。
不论 使用哪一种方式
```javascript
var a=new Function(...);
function a(...){...}
var a=function(...){...}
```

得到的函数名a都是一个指向函数的指针 可以用var b=a;来创建另外一个指向它的指针。
但是 它们指向同一段函数。因为函数名是一个指针，所以能够被作为参数传递。
()运算符作用于一个函数指针将会执行它所在的函数，当然还会传入参数。

### **2.函数直接量(函数文字量)**

实际上函数直接量这个说法并不准确 但可以通俗地解释一些用法
125 "good"是都是直接量 可以在代码中不通过变量名使用 函数也有类似的用法
function(){...} 代表了一个函数指针的值 它指向function(){...}定义的函数
那么 (function(){...})() 就执行了这个函数 a=function(){...}就创建了这个指针的一个副本

### **3.函数对象的成员**

函数对象继承自object对象 它还有自己的属性和方法
查到的就这么多 来自vs2005的提示
```javascript
//在thisValue对象上执行 参数由数组argArray提供
f.apply(thisValue,argArray);
//在thisValue对象上执行，不是没有参数，要用参数的话直接跟在thisValue后面
f.call(thisValue);
//原型 很常见 前面也用过 不多说了。
f.prototype; 
//参数列表的长度
f.length;

//继承自Object的方法，检查对象是否有指定属性
f.hasOwnProperty(propertyName);
//继承自Object的方法，检查指定属性是否能被for in枚举出来 （除了firefox下的prototype 其他属性都不能被枚举）
f.propertyIsEnumerable(propertyName);
//继承自Object的方法，转换为字符串
f.toLocaleString();
//继承自Object的方法，转换为字符串 在alert document.write等时自动调用
f.toString();
//继承自Object的方法，求值，对象做算术运算时自动调用，如果默认就是toString
f.valueOf();
```

### **4.javascript函数能访问的范围**
javascript函数究竟能访问那些资源？ 包括三部分：函数的作用域、this指针、参数，除此之外函数无法访问任何资源。
如果要比较清晰地理解函数 应当想像函数有三组参数：
第一组是作用域 在定义时确定 是函数定义位置决定的 暂称为外作用域 在函数定义处可以访问的变量和函数在函数中也可以访问，不论调用这个函数的指针被传递到哪里
第二组是this 在函数被调用时确定
如果函数是用()调用的  它默认指向函数所属的对象：如果函数是某个对象的成员 那么this指向这个对象，否则指向全局对象（大多数时候是window）
如果函数是用apply或者call调用的 this指向指定的thisvalue
第三组是参数 在函数被调用时确定 在参数列表中的参数可以直接访问 不再参数列表中的参数可以用arguments访问

### **5.函数作用域的生成与访问**
函数除了能够访问一个作用域之外 每次执行 会生成一个新的作用域 （闭包）
此作用域之外的代码无法访问这一作用域的变量 此作用域内的代码可以访问这个作用域以及此函数定义时的作用域。







