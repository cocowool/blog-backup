---
title: Javascript设计模式学习（二）封装续
date: 2008-09-10 10:32:01
tag: 
---


有一个避免其他程序员无意间写出重名函数的办法，在你想作为私有属性或者私有方法的命名前加一个下划线，像这样 this._name = name;，这样虽然不能避免所有的错误，但是能够避免绝大多数的无意识命名引起的冲突。

Scope，Nested Functions，and Closures

在实现私有属性的封装之前，我们需要一些基础的概念支持。在Javascript中，只有函数拥有作用域，也就是说，函数内部定义的变量，在外部是无法访问的。只有函数外部定义的变量，在函数内部才能访问。下面是一个说明的例子：
function foo(){
var = 10;
function bar(){
a *= 2;
}
bar();
return  a;
}
在这个例子中，a被定义在foo函数中，可是函数bar中可以访问a，是因为bar函数被定义在了foo函数中。当bar被执行时，他就能够使a的值翻倍，这是bar在foo内部执行，如果bar在foo外部执行会怎么样呢，看下面的例子：
function foo(){
var a = 10;
function bar(){
a *= 2;
return  a;
}
return bar;
}

var baz = foo();
baz();    //返回20；
baz();    //返回40；
baz();    //返回80；

上例中函数返回的是一个对于bar的引用，把这个引用传递给baz，在执行的时候，他仍可以访问变量a。这是为什么呢？因为Javascript支持语义上作用域，函数运行是在他被定义的域中，而不是他在被执行的域中。这个例子中bar因为被定义在foo的内部，所以当他在外部被执行时，仍然可以访问内部的变量a。
以上就是一个Closure的例子，只有foo中被返回的函数能够访问foo中的变量，最常见的创建Closure的方法就是返回一个Nested Function。

利用Closure创建私有方法
有了上面的基础，我们就能够实现私有方法了，如下：
**var**NewHouse=**function**(hid,hname,address){
**var**_hid,_hname,_address;

**function**isValid(hid){
**if**(hid!=null&&hid!=undefined&hid!=""){
**return**true;
}**else**{
**return**false;
}
}

**this**.getHid=**function**(){
**return**_hid;
}

**this**.setHid=**function**(hid){
**if**(!isValid(hid)){
_hid=hid;
}
}

**this**.getHname=**function**(){
**return**_hname;
}

**this**.setHname=**function**(hname){
_hname=hname||"暂无";
}

**this**.getAddress=**function**(){
**return**_address;
}

**this**.setAddress=**function**(address){
_address=address||"暂无";
}

**this**.setHid(hid);
**this**.setHname(hname);
**this**.setAddress(address);
}

NewHouse.prototype.ShowHouse=**function**(){
document.getElementById("container").innerHTML+="楼盘名称："+**this**.hname+"<br />";
}

可以看到，之前的例子中一直用this关键字来引用属性，而我们这里用了var来定义属性，所以属性在对象外是不能够被访问的。对于一些公共的方法，我们仍然可以利用prototype来定义他们。
这个方法的缺陷是我们每建一个对象，对于内部的方法对要被初始化一下，而这是要消耗内存的，我们实例的对象越多，消耗的内存也就越多。所以这个方法只试用在真正需要私有化属性和方法的地方，其他的地方我们仍然可以用完全暴露的方法来做。












