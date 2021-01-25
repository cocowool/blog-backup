---
title: IE6下实现Width:auto
date: 2012-07-05 21:48:01
tag: 
keywords: ie6, width:auto, ie6 width
description: IE6下实现宽度自适应的方案。
---

看了这个题目，很多人肯定觉得有点太老土了，IE6都快到末路了，不过这个方法确实非常经典，我觉得很有必要记下一笔。

在制作水平菜单的时候，我们经常使用ul和li元素，利用float属性让这些元素在水平的位置上对齐，同时利用width:auto来保持每个菜单随着内容的不同而变化宽度。在多数浏览器上，这个方法都很有效，只是除了IE6之外。

以下面的代码为例：
```html
<style type="text/css">
ul {
height: 30px;
overflow: hidden;
}
ul li {
float: left;
width: auto;
}
ul li a {
display: block;
height: 30px;
}
</style>
<ul>
  <li><a href="#">Link 1</a></li>
  <li><a href="#">Link 2</a></li>
</ul>
```

如何避免这个问题呢？

**Step 1 为IE6单独定制样式**

为了解决问题，我们需要将li元素的宽度设置为0,但是不能改变其他浏览器中的宽度，为此我们必须要用一些小手段，只能在IE6下生效，有三种办法：

1、hack 的手法。
```css
#nav ul li {
  width:auto;
  _width:0;
  float:left;
}
```
2、使用条件表达式
```html
<link rel="stylesheet" href="/css/style.css" type="text/css" media="screen" />
<!--[if lt IE 7]>
<link rel="stylesheet" href="/css/lt-ie7.css" type="text/css" media="screen" />
<![endif]-->
```
这样，只有IE版本小于7是才会加载CSS。

3、使用CSS选择器
```css
#nav ul li {
	width: 0;
	float: left;
}
#nav ul > li {
	width: auto;
}
```
IE6会忽略选择器，因为不支持，而其他浏览器则不会。

**Step 2 Magic**

最关键的让li宽度自适应的办法，是使用 white-space:nowrap ，如下：
```css
#nav ul li {
	width: 0;
	float: left;
	white-space: nowrap;
}
#nav ul > li {
	width: auto;
}
```
怎么样，很神奇吧。

参考资料：
1、[Fix width:auto Floated Elements in IE 6](http://chrisjean.com/2009/09/30/fix-width-auto-floated-elements-in-ie-6/)












