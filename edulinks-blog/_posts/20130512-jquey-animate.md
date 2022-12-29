---
title: jQuery的animate函数
date: 2013-05-12 09:45:01
tag: 
keywords: jquery, jquery animate, javascript jquery
description: jQuery提供了一个animate函数，可以通过改变CSS属性来实现一些动画效果。
---


jQuery提供了一个animate函数，可以通过改变CSS属性来实现一些动画效果。

用法如下：

```javascript
.animate( properties[, duration ][, easing ][, complete ])
```

或者

.animate( properties, options )，其中 options 包含了duration、easing、queue、specialEasing、step、progress、complete、done、fail、always等多个属性。

animate函数允许我们在所有使用数字值的CSS属性上创建动画效果。唯一必须的属性是一个 CSS 属性对象。例如：
```javascript
$('.class').animate({
	left:"100"
});
```

**Animation Properties and Values**

所有的可创建动画效果的属性必须是一个单一的数值，例如 宽度(width)、高度(height)、左边距（left）等，但是不能是 background-color。注：要实现颜色的动画效果，必须使用 jQuery.Color() 插件。除非特殊声明，否则这些属性的单位都按照像素处理，可以使用的其他单位还包括 em 和 % 百分比。

除了一些样式属性外，像 scrollTop 和 scrollLeft 这样的一些非样式属性也可以实现动画效果。

缩写的CSS属性不被支持。例如，如果想对边框的宽度做一个动画效果，那么必须提前明确定义边框的宽度，而不能使用 auto。对于字体尺寸，可以使用 fontsize 或者 font-size，而不能使用 font。

对于每一个指定的值，我们可以使用“show”、“hide”、“toggle”三个字符串，来指定在何时使用动画效果。

动画属性也可以使用相对值。如果使用“+=”、“-=”这样的字符串，则会使用给定的步长进行属性值的计算。


**Duration 持续时间**

Duration 的单位为微秒，数值越高动画效果越慢，反之则越快。默认的时间是 400 毫秒。“fast”默认为 200 毫秒，“slow”默认为 600 毫秒。

**Complete Function 完成后的函数动作**

如果指定，complete 回调函数会在动画执行完毕后被触发。这在设计多个顺序发生的动画时特别有用。这个函数没有任何参数，但是 this 被设置为触发动画的DOM元素。如果多个元素发生动画，则回调在每个动画执行完后都会被执行，而不是在所有动画执行完后执行一次。

**Step Function 步长函数**

在animate的第二种调用方式中提供了一个step的选项，这是一个回调函数，可以在每一个step执行时被触发。这个函数在自定义动画类型时非常有用，可以在动画触发后对其做出更改。step函数接收 now 和 fx 两个参数，this 设置为发生动画的DOM元素。

now 表示发生动画效果属性的当前值；
fx 是对 jQuery.fx 的引用，包含了动画元素的一系列属性，例如 fx.elem.id，其中的 start 和 end 是动画属性的初始值和最终值，prop 指被改变的属性。

**Easing 放开动作**

这个属性是一个字符串，来指定动画过程中不同的时间点所使用的速度，默认的属性为“swing”，如果需要动画保持一个均匀的速度，可以使用“linear”。更多的属性可以使用插件，可以参考[jQuery UI suite](http://jqueryui.com/)。

所有的jQuery动画效果，都可以使用 jQuery.fx.off = true 来关闭，实际上是设置了 duration 为0.更多的信息，可以参考 jQuery.fx.off。

利用jQuery的Animate函数和Effect插件，做了一个类似于IBM首页的焦点图切换效果，可以参考[https://github.com/cocowool/RoseFocus](https://github.com/cocowool/RoseFocus)

参考资料：
1、[animate of jQuery](http://api.jquery.com/animate/)
2、[jQuery的Index方法](http://www.w3school.com.cn/jquery/dom_element_methods_index.asp)
3、[jQuery UI effect](http://jqueryui.com/effect/#easing)