---
title: YUI3 Overlay的使用
date: 2010-02-07 23:54:01
tag: 
keywords: yui, yui3, yui3 overlay
description: YUI3的Overlay为我们提供了一个创建叠加层的方便的方式。
---

YUI3的Overlay为我们提供了一个创建叠加层的方便的方式（从名字就可以看出来），他扩展自Widget类。目前的版本，仅支持简单的页面XY定位、相对于一个Node或者viewport对其或居中。下面来体验一下Overlay。
最简单的引用YUI3的方法自然是引用Yahoo服务器上的种子文件，地址如下：

> <script src="http://yui.yahooapis.com/3.0.0/build/yui/yui-min.js"></script>

引用了种子文件之后，我们就可以在我们的应用中使用Overlay了，建立一个新的YUI的命名空间，如下：

```css
YUI().use(‘overlay’,function(Y){
  var overlay = Y.Overlay({
    contentBox:“#MyContent”,
    visible:true,
    width:“20em”,
    height:“20em”,
    xy:[200,200]
  });
  overlay.render();
});

```

先来熟悉一下overlay所支持的属性：

**内容**：
overlay的内容既可以是我们事先写在页面中的，也可以通过script在后期创建。因为使用了标准的模块，所以内容基本上有三个部分：headerContent、bodyContent、footerContent。通过脚本设置内容的时候，既可以直接填入innerHTML，也可以通过传入一个node对象来实现，使用的方式非常的灵活。

**定位**：
Overlay的定位有三种方式，xy、对齐、居中。
xy定位的方式是通过指定x,y或者xy来为叠加层定位，这个定位是基于页面的xy坐标，比如[200,200]是相对于页面左上角分别200px的位置。
对齐(align)，可以将overlay与页面中的某个node进行对齐，通过传入一个两个属性的对象来实现，如下例：
```css
YUI().use(‘overlay’,function(Y){
  var overlay = Y.overlay({
  contentBox:“MyContent”
  align:{
  node:“MyAchor”
  points:[Y.WidgetPositionExt.TL,Y.WidgetPositionExt.TR]

});
```

其中points表示叠加层和要对其的node的位置关系，第一个参数为叠加层，第二个为对齐node，上例的参数表示叠加层的左上角和node的右上角对齐。

**层叠顺序**：

IE6下有一个著名的bug，就是select控件默认总是zindex高于其他所有的层，overlay的shim属性正是考虑了这个问题存在的。这个选项默认在IE6下是打开的，会通过创建iframe的方式来规避这个bug。

使用overlay也好，其他的yui特性也好，感觉非常的方便和灵活，可以说是一个真真正正为开发者考虑的js框架，而我们也应该学习YUI3的这种设计思想和风格，让自己的应用也变得更加灵活和方便。
总体来看，目前YUI3的Overlay基本能够满足我们日常创建一些叠加层的需要，但是还有一些更高级的属性或者控制功能期待在后续的版本中加入进来。

参考资料：
1、[YAHOO YUI3 Overlay](http://developer.yahoo.com/yui/3/overlay/)












