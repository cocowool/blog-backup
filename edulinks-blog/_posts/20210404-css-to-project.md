---
title: CSS工程化开发方法及优点
date: 2021-04-04 21:06:28
tags:
keywords: sass, css, scss, css工程化开发
description: 借助SASS或LESS拓展语言的支持，我们能够用工程化的方法开发CSS代码。
---

## 传统 CSS 开发存在的问题
在我2003年刚接触Web开发的时候，一直到2008年左右，从最简单的直接编写JS和CSS，到出现jQuery框架，CSS也开始出现一些基础的包，但当时还是以JS为主。后来的事情大家都知道了，Twwiter发布了Bootstrap框架，对于页面开发的友好度大大提升了。传统的CSS开发具有以下缺点：
* 缺少模块系统
* 没有变量机制
* 嵌套层级的写法造成大量重复输入
* 难于复用

对于小型的Web项目CSS手工写起来还可，一旦工程大起来，如何不同人员的合理分工，如何维护基础变量以及解决复用的问题就变得比较关键，这时就需要工程化的管理思路和工具来进行CSS的开发。

[SASS](http://sass-lang.com) 和 [LESS](http://lesscss.org)是两种最流行的解决方案。通过提供CSS预处理器，为CSS增加了一些编程的特性，将CSS的开发工作变得更为工程化。

## SASS与LESS简介
SASS与LESS都是CSS的拓展语言，less是通过node.js编译，sass通过ruby编译。

## 什么是 SASS 以及 SASS 的历史
> 我选择的是SASS，所以本文重点介绍SASS。

Sass 是世界上最成熟、最稳定、最强大的专业级 CSS 扩展语言！

## SASS 的特性

### 变量 Variables
Sass的变量必须是 `$` 开始，变量名和值使用冒号隔开。
```scss
$mainColor: #0982c1;
$siteWidth: 1024px;
$borderStyle: dotted;
 
body {
  color: $mainColor;
  border: 1px $borderStyle $mainColor;
  max-width: $siteWidth;
}
```

### 嵌套 Nesting
Scss支持嵌套语法。
```scss
section {
    margin: 10px;
    
    nav {
        height: 25px;
        
        a {
            color: #000;
        }
    }
}
```

编译后是
```css
section {
    margin: 10px;
}

section nav {
    height: 25px;
}
section nav a {
    color: #000;
}
```

### 引用 Import

### Mixins
因为不同的浏览器中有些属性前缀不一致，每次写起来就会很累人，这时候可以使用`mixin`功能。
```scss
@mixin transform($property) {
    -webkit-transform: $property;
    -ms-transform: $property;
}
.box { @include transform(rotate(30deg)); }
```

编译之后就是
```css
.box {
  -webkit-transform: rotate(30deg);
  -ms-transform: rotate(30deg);
  transform: rotate(30deg);
}
```

### 扩展/继承 Extent/Inheritance
这是sass中最有用的一个特性，使用`@extend`能让我们在一个和多个选择器间共享一段CSS属性。
```scss
/* This CSS will print because %message-shared is extended. */
%message-shared
  border: 1px solid #ccc
  padding: 10px
  color: #333


// This CSS won't print because %equal-heights is never extended.
%equal-heights
  display: flex
  flex-wrap: wrap


.message
  @extend %message-shared


.success
  @extend %message-shared
  border-color: green


.error
  @extend %message-shared
  border-color: red


.warning
  @extend %message-shared
  border-color: yellow
```
编译后的效果
```css
/* This CSS will print because %message-shared is extended. */
.message, .success, .error, .warning {
  border: 1px solid #ccc;
  padding: 10px;
  color: #333;
}

.success {
  border-color: green;
}

.error {
  border-color: red;
}

.warning {
  border-color: yellow;
}
```

### 操作符 Operator

SASS提供了五种运算符，分别为：

* 赋值运算符。使用 `$main-color: lightpink;` 的方式进行赋值。
* 算术运算符。支持 `+、-、*、/、%` 五种算术运算符。
* 平等算子。支持`==、!=` 两种平等算子。
* 比较运算符。支持 `>、>=、<、<=` 四种比较运算符。
* 逻辑运算符。支持 `And、Or、Not` 三种逻辑运算符。

## 工程实践

基于以上的优点，我在制作自己博客皮肤的时候使用了工程化的方法。大家可以参考我的 [Github]()


参考资料：
1. [前端CSS的工程化——掌握Sass这四大特性就够了](http://www.cnblogs.com/iovec/p/8024408.html)
2. [SASS](http://sass-lang.com)
3. [LESS](http://lesscss.org)
4. [SASS Documentation](http://sass-lang.com/documentation/file.SASS_REFERENCE.html)
5. [sass和less](https://www.jianshu.com/p/6a35a548c9e1)
6. [css系列—sass,scss,less,stylus的比较](https://blog.csdn.net/jiaojsun/article/details/95093505)
7. [webpack配置中的ExtractTextPlugin插件认识](https://www.jianshu.com/p/ed3c98ba5474)
8. [sass guide](https://sass-lang.com/guide)

