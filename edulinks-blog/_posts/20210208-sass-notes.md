---
title: SASS学习笔记
date: 2021-02-08 10:58:44
tags:
keywords: sass, css, stylesheet
description: Sass是一门CSS编译语言，为CSS赋予了可编程的能力，允许我们使用变量、函数、mixin等手段来进行模块化的CSS开发，减少了大量冗余的代码工作量。
---
Sass是一门CSS编译语言，为CSS赋予了可编程的能力，允许我们使用变量、函数、mixin等手段来进行模块化的CSS开发，减少了大量冗余的代码工作量。

## 1. 语法
Sass支持两种语法

### 1.1 scss
SCSS语法使用`.scss`后缀，SCSS基本上是CSS的超集，也就是绝大多数的CSS语法都可以直接使用。因为与CSS语法相似，这是学习SASS最简单也最流行的一种语法。

```scss
@mixin button-base() {
  @include typography(button);
  @include ripple-surface;
  @include ripple-radius-bounded;

  display: inline-flex;
  position: relative;
  height: $button-height;
  border: none;
  vertical-align: middle;

  &:hover { cursor: pointer; }

  &:disabled {
    color: $mdc-button-disabled-ink-color;
    cursor: default;
    pointer-events: none;
  }
}
```

### 1.2 sass 缩进语法
缩进语法是sass的原生语法，因此使用`.sass`作为文件后缀。这种语法用缩进表示花括号，空号表示花括号的结束。

```sass
@mixin button-base()
  @include typography(button)
  @include ripple-surface
  @include ripple-radius-bounded

  display: inline-flex
  position: relative
  height: $button-height
  border: none
  vertical-align: middle

  &:hover
    cursor: pointer

  &:disabled
    color: $mdc-button-disabled-ink-color
    cursor: default
    pointer-events: none
```

## 2. 文档构成
像大多数CSS文件一样，Sass文件主要由样式规则及规则的属性构成，除此之外还包括一些独有的特性。

> 作者主要用scss语法，因此本文后续介绍中只考虑scss语法，使用sass语法的请自行转换。

### 2.1 样式规则
```scss
.item {
    color: green;
}
```

### 2.2 变量声明
```scss
$bg-color: #fff;
```

### 2.3 @if语句
`@if`语法
```scss
@if expression {
    //CSS codes here
}
```
具体事例
```scss
p {
    @if $bg-color { border:1px solid; }
    @if $box-width > 100 { border: none; }
}
```
还可以接着很多`@else`
```scss
$type: monster;
p {
  @if $type == ocean {
    color: blue;
  } @else if $type == matador {
    color: red;
  } @else if $type == monster {
    color: green;
  } @else {
    color: black;
  }
}
```

### 2.4 @each语句
语法
```scss
@each $var in <list or map>
```

具体示例
```scss
@each $color in red, green, yellow, blue {
    .p_#{$color} {
        background-color: #{$color}
    }
}
```
列表和映射还可以是二维的，如下所示
```scss
@each $color, $border in (aqua, dotted),
                        (red, solid),
                        (green, double){
  .#{$color} {
    background-color : $color;
    border: $border;
  }
}
```
也可以使用映射的方式
```scss
@each $header, $color in (h1: red, h2: green, h3: blue) {
  #{$header} {
    color: $color;
  }
}
```

### 2.5 @error\@warn\@debug
和传统的编程语言一样，在编写sass中的`mixin,function`过程中，如果有变量没有按照期望的格式传递，我们还可以增加错误处理逻辑。
```scss
@mixin reflexive-position($property, $value) {
  @if $property != left and $property != right {
    @error "Property #{$property} must be either left or right.";
  }

  $left-value: if($property == right, initial, $value);
  $right-value: if($property == right, $value, initial);

  left: $left-value;
  right: $right-value;
  [dir=rtl] & {
    left: $right-value;
    right: $left-value;
  }
}

.sidebar {
  @include reflexive-position(top, 12px);
  //       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  // Error: Property top must be either left or right.
}
```

和@error类似的作用，但是@warn并不会停止sass的编译。
```scss
$known-prefixes: webkit, moz, ms, o;

@mixin prefix($property, $value, $prefixes) {
  @each $prefix in $prefixes {
    @if not index($known-prefixes, $prefix) {
      @warn "Unknown prefix #{$prefix}.";
    }

    -#{$prefix}-#{$property}: $value;
  }
  #{$property}: $value;
}

.tilt {
  // Oops, we typo'd "webkit" as "wekbit"!
  @include prefix(transform, rotate(15deg), wekbit ms);
}
```

@debug我想就不用再细说了，这个语句是为了方便我们调试，将一些变量打印出来供分析运行态的。

### 2.6 CSS语句
包括以下几种：
* CSS样式规则 ```h1 { font-size:xxxx; }```
* `@media`,`@font-face`等CSS内置的@规则
* `@include`引用的mixin
* `@at-root`指令，更详细的可以参考[这里](https://sass-lang.com/documentation/at-rules/at-root)，表示选择器嵌套的最外层，对于编译结果来说将使嵌套失效，直接移除父选择器。。

### 2.7 一些顶级的声明
* `@use` 通过命令加载modules，包括sass自带的一些modules，这里面也有作用域的概念，更详细的内容还是参考[官方文档](https://sass-lang.com/documentation/at-rules/use)
* `@import` 应用外部文件
* `@mixin` 定义mixin
* `@function` 定义function

## 3. Expressions
类似于其他语言中的变量类型或操作符的定义。

SASS中包括以下变量类型
* 数字 Numbers，可以是纯数字也可以包括单位，如`12px`
* 字符串 Strings 可以用双引号也可以不用双引号，例如`"Helfetica Nenu"`或者`bold`
* 颜色 Colors 可以用预定义的缩写或者十六进制的写法，例如`#c6538c`或`green`
* 布尔值 boolean ，包括`true`和`false`
* null
* 值列表，例如空格分割的值，比如`padding: 1em 2em 1em 2em;`
* 映射，例如`"background":red, "foreground":pink`

SASS中的操作符包括：
* `==`和`!=`用于检查两个值是否相等
* `+,-,*,%`用来进行数学运算
* `<,<=,>,>=` 用来进行两个值大小的比较
* `+,-,/`用来连接字符串
* `(,)` 用来控制运算的顺序

## 4. 注释的写法
注释分两种`//`开头的注释不会编译到CSS文件中，`/**/`形式的注释会编译到CSS中。
单行注释以`//`开头，`/**/`格式支持多行注释。
```scss
// This comment won't be included in the CSS.

/* But this comment will, except in compressed mode. */

/* It can also contain interpolation:
 * 1 + 1 = #{1 + 1} */

/*! This comment will be included even in compressed mode. */

p /* Multi-line comments can be written anywhere
   * whitespace is allowed. */ .sans {
  font: Helvetica, // So can single-line commments.
        sans-serif;
}
```

### 文档注释
如果使用sass写一些基本的库，可以对`mixin,functions,variables`等编写文档注释，通过SassDoc工具可以提取文档。

文档注释以```///```开头，支持Markdown语法格式。

## 参考资料
1. [SASS Document](https://sass-lang.com/documentation)
2. [scss-@if指令](https://www.cnblogs.com/ibabyli/p/9871236.html)
3. [SCSS @if() 指令](http://www.softwhy.com/article-9916-1.html)
4. [scss-@each指令](https://www.cnblogs.com/ibabyli/p/9871382.html)
5. [sass @error](https://sass-lang.com/documentation/at-rules/error)
