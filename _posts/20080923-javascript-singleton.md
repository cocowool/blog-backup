---
title: JavaScript设计模式学习（四）单件(Singleton Pattern)
date: 2008-09-23 10:26:01
tag: 
---


单件是JavaScript中最基本、最有用的设计模式，而你今后也会经常的使用这个模式。通过单件，我们可以把统一到一个逻辑单元中并且提供一个唯一的入口，这就保证你所有的引用都是用的这个全局资源。

单件的用途有：一、提供一个Namespacing、二、提供一种被称为branching的技术。

**单件的基本形式：**

/* Basic Singleton Pattern */
**var**Map={
name:"Map G",
createdate:"2008-09-19",

sayMyName:**function**(){
alert("My Name is "+**this**.name);
},
tellMeWords:**function**(arg){
alert("What you told to me is "+arg+"\n I won't tell anyone!")
}
}

**命名空间：**

我们可以给单件提供新的属性，并且能够创建独立的命名空间，而避免我们写的属性或者程序被无意的覆盖。我们接着单件的基本形式中的例子可以创建新的属性：

Map.Property={
createdata:"2000-09-09",
width:"100",
height:"200"
}

Map.Utility={
getSize:**function**(x,y){
**return**x*y;
}
}

这样，同样的createdate属性即便一样，也不会造成程序中的冲突了。

**在单件中添加私有成员：**

利用下划线。最简单、最直接的声明私有变量的方式就是使用下划线。这样就可以使其他程序员明白，这些变量是私有的、只是在对象内部使用，同时也可以使其他程序员避免直接访问他们。

/* Basic Singleton Pattern */
**var**Map={
name:"Map G",
createdate:"2008-09-19",

sayMyName:**function**(){
alert("My Name is "+**this**.name);
},
tellMeWords:**function**(arg){
alert("What you told to me is "+arg+"\n I won't tell anyone!")
},
_isMature:**function**(){
alert("Of Course, I'm Mature!");
}
}

使用Closure。

使用Closure，我们可以实现私有变量和共有变量。因为singleton在调用过程中只会有一个实例，所以我们就不必担心私有成员耗费资源的问题了。例子如下：

MyNamespace={};

MyNamespace.Singleton=(**function**(){
**var**privateAttribute1="Name";
**var**privateAttribute2=true;

**function**privateMethod1(){

}

**return**{
publicAttribute1:"Public",
publicAttribute2:"false",
publicMethod1:**function**(){

}
};
})();

Lazy Instantiation
以上所讨论的各种各样的单件实现，他们都有一个共同点，那就是当代码加载时就立即创建的。如果你有一个需要复杂配置的单件、或者他加载的时候需要占用大量资源，也许我们就该考虑延时实现他了。Lazy Loading就是用来解决这个问题的，常被用在需要加载大量数据的时候。

Lazy Loading singletons与之前的singleton不同，你不能通过MyNamespace.methdName()来进行访问，而必须通过Mynamespace.getInstance().methodName()来访问。GetInstance方法首先检查单件是否进行实例化，如果没有，则创建并返回，下面是例子：

//Lazy instantiation
Car={};
Car.MyPorsche=(**function**(){
**var**uniqueInstance;

**function**CarOfPorsche(){
**var**name="My Favorate Porsche";
**var**buydate="2008-09-22";

**return**{
speed:"120Kmp",
getName:**function**(){
**return**name;
}
};
}

**return**{
getInstance:**function**(){
**if**(!uniqueInstance){
uniqueInstance=CarOfPorsche();
}

**return**uniqueInstance;
}
};
})();

//Normal Singleton
Car.MyBenz=(**function**(){
**var**name="My Benz Car";
**var**buydate="2009-10-10";

**return**{
speed:"140Kmp",
getName:**function**(){
**return**name;
}
};
})();

Lazy loading的一个缺点是增加了代码的复杂性，而且代码开始变得难以理解。在调用的时候，我们需要通过Car.MyPorsche.getInstance().speed来调用，这就增加了代码的复杂行。当然，我们可以通过
**var**porsche=Car.MyPorsche.getInstance();
alert(porsche.getName()+"'s speed is "+porsche.speed);
来简化调用。

**分支(Branching)：**

分支是一项将不同浏览器的区别封装到动态方法中的技术。我们以创建XHR对象为例。对于大多数浏览器，XHR 是XMLHttpRequest对象的一个实例，或者是较早版本的IE的不同ActiveX的一个实例。这个方法总是伴随着对于浏览器的检测，如果我们没用用Branching这项技术，没次我们都需要运行浏览器检测的方法来检查，这降低了代码的效率。

一个更加高效的方式就是当代码加载时，根据浏览器的不同赋予一个固定的值，在不同的浏览器下，我们只执行这个实现的扩展。在运行时刻动态改变一个Function的内容，这正是JavaScript灵活的强大的所在。

那么分支如何应用在单件中呢，看下面的例子：

//Branching
//SimpleXhrFactory Singleton
**var**SimpleXhrFactory=(**function**(){
**var**standard={
createObject:**function**(){
**return****new**XMLHttpRequest();
}
};

**var**activexNew={
createObject:**function**(){
**return****new**ActiveXObject("Msxml2.XMLHTTP");
}
};

**var**activexOld={
createObject:**function**(){
**return****new**ActiveXObject("Microsoft.XMLHTTP");
}
};

**var**testObject;
**try**{
testObject=standard.createObject();
**return**standard;
}**catch**(e){
**try**{
testObject=activexNew.createObject();
**return**activexNew;
}**catch**(e){
**try**{
testObject=activexOld.createObject();
**return**activexOld;
}**catch**(e){
**throw****new**Error("No XMLHTTP Object found in this environment!");
}
}
}
})();

**什么时候使用单件：**

当需要使用命名空间或者模块化你的代码的时候，应该尽量的使用单件。单件是JavaScript中使用最广泛的一个设计模式，无论在什么规模的项目中，你都能看到他的影子。

**使用单件的好处：**

单件的主要作用是提供了一个格式化你的代码的方法。通过吧相似的方法和属性集合到一个单间中，并且只能被实例化一次，你就可以更方便的调试代码。












