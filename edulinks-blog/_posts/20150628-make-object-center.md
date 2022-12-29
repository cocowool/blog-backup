---
title: 如何让高度、宽度不定的容器保持水平、垂直居中
date: 2015-06-28 22:14:01
tag: 
keywords: css, 水平居中, 垂直居中
description: 使用CSS的方式解决网页元素水平居中、垂直居中的需求。
---

这个题目似乎解决的办法很多，JS是最能够确保各种浏览器中一致性的，但是仍然可以使用CSS的方式来解决。这个问题分解为两个方面，第一解决左右居中的问题，第二解决上下居中的问题。

1、左右居中。

左右居中最为简单，使用 text-align:center; 就可以让绝大多数的对象居中对齐，并且这个属性也获得了几乎全部浏览器的支持。实际上，这个属性定义的是块级对象内部文字的对齐方式，内部的文字或者图像一般是内联对象。

也许有人会提到，为什么不用 margin:0 auto;这个办法呢？这是一个好问题，在做居中布局的页面时，这是我们最常用的让DIV容器居中的办法。margin作用于块级元素，而是否作用于其他内敛元素，不同的浏览器有着不同的解释，因此对于左右居中，没有使用这个方法。

2、上下居中。

上下居中，有两种方法，一种是负margin的办法，这种对于固定宽度的容器，非常的好用。另外一种就是适应于高度不固定的情形，即使用 vertical-align 属性。Vertical-align 属性定义行内元素的基线相对于该元素所在行的基线的垂直对齐。允许指定负长度值和百分比值。这会使元素降低而不是升高。在表单元格中，这个属性会设置单元格框中的单元格内容的对齐方式。

3、最后代码

综上，可以得出对于高度、宽度都不固定的容器，如何让其做到水平、垂直居中：

```html
<!DOCTYPE html>
<html>
	<head>
		<metahttp-equiv=“Content-Type”content=“text/html;charset=utf-8″/>
		<title>水平、垂直居中</title>	
		<style type=“text/css”>
			#container_outer{
				display:table;
				width:1200px;
				height:1000px;
				overflow:hidden;
				margin-left:50px;
				_position:relative;
			}
			#container_inner{
				vertical-align:middle;
				display:table-cell;
				text-align:center;
				_position:absolute;
				_top:50%;
				_left:50%;
			}
			#content{
				color:#fff;
				border:1px solid #000;
				display:inline-block;
				_position:relative;
				_top:-50%;
				_left:-50%;
			}
	</style>
<body>	
<div id=“container_outer”>
  <div id=“container_inner”>
    <div id=“content”>动态内容...
    </div>
  </div>
</div>
</body>
</html>
```


参考资料：
1、[http://blog.mihoweb.com/archives/790.html](http://blog.mihoweb.com/archives/790.html)
2、[CSS text-align](http://www.w3school.com.cn/cssref/pr_text_text-align.asp)
3、[http://www.cnblogs.com/zhwl/p/3529473.html](http://www.cnblogs.com/zhwl/p/3529473.html)
4、[CSS vertical-align](http://www.w3school.com.cn/cssref/pr_pos_vertical-align.asp)
5、[http://www.jb51.net/css/10337.html](http://www.jb51.net/css/10337.html)