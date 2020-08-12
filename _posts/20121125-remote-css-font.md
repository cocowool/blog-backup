---
title: CSS调用远程字体
date: 2012-11-25 22:24:01
tag: 
---

CSS中的@font-face方法可以调用服务器端的字体。
Demo 可以参考：[http://idv3.sinaapp.com](http://idv3.sinaapp.com/)中标题字体的用法，具体用法如下：


@font-face {
font-family:name;
src: local('Ubuntu'), url(url) format('woff');
sRules
}


兼容性的情况，Chrome、Firefox、Opera均没有问题，IE系列不支持 .ttf 和 .otf。兼容IE需要 .eot 或者 .woff 的字体格式。


看了［参考资料[1](http://www.dearoom.com/blog/css%E8%B0%83%E7%94%A8%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%AB%AF%E5%AD%97%E4%BD%93%E7%9A%84%E6%96%B9%E6%B3%95%E5%92%8C%E5%88%A9%E5%BC%8A/)］的文章，使用 font-face 有不需要本地字库支持就能够保持字体样式一致，而不必使用图片代替的优势，缺点就是有可能会消耗加载的时间。


那么为了更好的使用font-face，我们需要做的就是提高字体的加载及响应时间。


对于字体的Format，主要有以下几种字体：


1、TrueType（.ttf）格式。
.ttf 字体是Windows和Mac的最常见的字体，是一种RAW格式。


2、OpenType（.otf）格式。
.otf字体被认为是一种原始的字体格式，其内置在TrueType的基础上，提供了更多的功能。


3、Web Open Font Format（.woff）格式。
.woff字体是Web字体中最佳格式，是一个TrueType的压缩版本，同时也支持原数据包的分离。


4、Embedded Open Type（.eot）格式。
.eot字体是IE专用字体，可以从TrueType创建此格式字体。


5、SVG（.svg）格式。
.svg字体是基于SVG字体渲染的一种格式。


参考资料：1、[CSS调用服务器端字体的利与弊](http://www.dearoom.com/blog/css%E8%B0%83%E7%94%A8%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%AB%AF%E5%AD%97%E4%BD%93%E7%9A%84%E6%96%B9%E6%B3%95%E5%92%8C%E5%88%A9%E5%BC%8A/)
2、[Google Webfonts](http://www.google.com/webfonts)
3、[EOT网页字体嵌入技术](http://hi.baidu.com/zhy65991/item/e87a8f0c69a2c91deafe38ac)
4、[http://www.fontsquirrel.com/fontface/generator](http://www.fontsquirrel.com/fontface/generator)
5、[CSS的Font－face](http://www.cnblogs.com/rubylouvre/archive/2011/06/19/2084875.html)












