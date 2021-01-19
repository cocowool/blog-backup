---
title: IE支持而Firefox不支持的CSS属性
date: 2010-02-24 19:57:01
tag: 
keywords: ie, firefox, css
description: 居然发现了一个IE支持，而Firefox确不支持的CSS属性。
---

今天，居然发现了一个IE支持，而Firefox确不支持的CSS属性。
```css
.peopleBox a { text-indent:-999px; }
```

测试的时候，发现IE下表现良好，但是Firefox确无动于衷。一直以来，从小到大，思想里从来不会出现这样的情形，所以当时确实是大吃一惊。

Google后才发现:

> This property specifies the indentation of the first line of text in a block. More precisely, it specifies the indentation of the first box that flows into the block's first[line box](http://www.w3.org/TR/CSS21/visuren.html#line-box). The box is indented with respect to the left (or right, for right-to-left layout) edge of the line box. User agents should render this indentation as blank space.

原来只对块级元素起作用，而且只是第一行。看来这些东西记得还是不是很牢固

参考资料：
1、[W3C](http://www.w3.org/TR/CSS21/text.html#indentation-prop)












