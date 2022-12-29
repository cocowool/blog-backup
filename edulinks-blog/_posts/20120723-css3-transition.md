---
title: CSS3 Transition介绍
date: 2012-07-23 21:24:01
tag: 
keywords: css3, css3 transition
description: CSS3提供了一种全新的方式来定义CSS属性改变时的过渡效果，通常在:hover、:focus的条件下触发。
---


CSS3提供了一种全新的方式来定义CSS属性改变时的过渡效果，通常在:hover、:focus的条件下触发。过去，为了实现这种平滑的过渡效果，我们需要借助于Flash技术，现在只需要简单的使用CSS3 Transition的方法，就可以实现。Transition的效果，可以广泛的应用在background-color、width、height、opacity等属性发生改变时。目前，CSS3 Transition模块还在草案阶段，所以规范和语法还未完全确定下来，目前也并不是所有的浏览器支持CSS3，所以还需要一些特殊的代码来实现多浏览器的兼容。


下面用一个简单的例子来说明CSS3 Transition的效果：


Example 1: 没有过渡效果

Example 2: 5秒钟的过渡效果

CSS3 Transition的语法：
```css
transition: <transition> [, <transition>]*
<transition> = <transition-property> <transition-duration> <transition-timing-function> <transition-delay>
```

这种写法是比较简单的写法，我们可以同时写多个Transition效果，每个效果按照固定属性的顺序书写。如下

`transition:left 3s linear, background-color 3s ease;`

transition的属性包括：需要应用的属性transition-property、Transition的时间transition-duration、时间函数transition-timing-function、延迟时间（可选参数）transition-delay。这些属性也可以单独的指定，分别介绍如下：

### transition-property

`transition-property:none | all | [ <IDENT> ] [, <IDENT> ]*`

如果使用all关键字或者省略了transition-property，那么所有发生变化的属性都会应用transition，如果使用none关键字，则不会有transition效果。如果指定了多个属性，那么在其他的transition-*属性中，也需要按照对应的顺序指定。所有支持Transition的属性，可以在[3](http://www.w3.org/TR/css3-transitions/#animatable-properties-)中看到。

### transition-duration

这个属性接受以逗号分割的一系列数值，表示Transition持续的时间，接受2s或者2000ms的形式。默认值为0,表示Transition立即生效。

`transition-duration: <time> [, <time> ]*`

### transition-timing-function

这个属性指定transition的效果，可以通过预定义的函数来实现（ease、linear、ease-in、ease-out、ease-in-out），也可以自己指定函数。

`transition-timing-function:<timing-function> [, <timing-function>]*`

下面看一下预定义的函数的效果
```css
ease
linear
ease-in
ease-out
ease-in-out
```

### transition-delay

这个是一个可选属性，可以用来制定在多长时间后触发transition，默认是0.

`transition-delay: <time> [, <time> ]*`

参考资料：
1、[An Introduction to CSS3 Transitions](http://www.css3.info/preview/css3-transitions/)
2、[CSS3 transition-timing-function使用参考指南](http://www.zhangxinxu.com/css3/css3-transition-timing-function.php)
3、[CSS Transitions](http://www.w3.org/TR/css3-transitions/#animatable-properties-)














