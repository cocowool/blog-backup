---
title: CSS中的计数器
date: 2013-05-17 23:28:01
tag: 
keywords: css, css counter, css 计数器
description: CSS的规范中，有一个很奇特的特性，支持计数器的功能。
---

CSS的规范中，有一个很奇特的特性，支持计数器的功能。

先来看下如何使用：
<section>
<p>Place the flour in a large bowl, make a well in the centre and pour in the milk and eggs. Give the liquid mixture a quick whisk before incorporating the flour. Continue to whisk until you have a smooth batter.</p>
<p>Now add 1 tbsp vegetable oil and whisk thoroughly.</p>
<p>Take a crêpe pan, or large frying pan, dip some kitchen roll in the oil and carefully wipe the inside of the pan. Heat the pan over a medium heat for one minute.</p>
<p>Add just under a ladleful of batter to the pan and immediately start swirling it around the pan to produce a nice even layer.</p>
<p>Cook the pancake for approximately 30-40 seconds. Use a palette knife to lift the pancake carefully to look at the underside to check it is golden-brown before turning over. Cook the other side for approx 30-40 seconds and transfer to a serving plate.</p>
</section>

这段代码表示了做一件事情的顺序，现在我们可以使用CSS的计数器来给这些步骤标注顺序。

下面的代码：
```css
body {
	counter-reset: steps;
}

p{
  color: #242424;
  font-size: 16px;
  line-height: 20px;
}
p:before {
  counter-increment: steps;
  content: "Step " counter(steps) ": ";
  font-weight: bold;
  font-size: 18px;
}
```
在浏览一下看看效果如何？每行之前都有了一个步骤的数字标注，很神奇吧。

这个属性自CSS2.1起开始写入规范，目前大多数主流的浏览器都可以支持，唯一不支持的就是IE7了。

这个特性看起来简单，但是如果我们能够合理的使用，效果还是非常好的。

参考资料：
1、[Learn to count with CSS](http://www.webdesignerdepot.com/2013/05/learn-to-count-with-css/)