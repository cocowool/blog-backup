---
title: CHROME对CSS的解析
date: 2009-03-14 12:09:01
tag: 
keywords: css, 居中, chrome
description: Chrome对于CSS居中样式的支持
---

最近完成的一个项目需要做对Chrome的支持，在这个过程中发现了一个Chrome对于Css解析与FF、IE等的区别，在这里总结一下。

项目的需求是需要在屏幕的中央显示一个提示框，这个对于FF和IE都已经很成熟了，利用负边距能够很容易的实现：

首先看一下页面：

```html
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
<head>
<metahttp-equiv="Content-Type"content="text/html; charset=utf-8">
<title>Chrome Position</title>
</head>
<body>
<divid='wrapper'>
<divid='contentbox'>
<p>Here is the centered content!</p>
</div>
</div>
</body>
</html>
```

加入css
```html
<style type='text/css'>
html{height:100%;}
body{margin:0px;padding:0px;position:relative;height:100%;}
#wrapper{width:100%;height:100%;position:relative;; left:0px;top:0px;}
#contentbox{width:400px;height:400px;border:2px solid green;position:relative;top:50%;left:50%;
margin-left:-200px;margin-top:-200px;}
</style>
```

这样，在FF3、IE6/7中都能，很好的实现水平和垂直居中，而在chrome中，出现了问题。

![](/20090314-chrome-css/2009-3-13-12-06-25.png)
最后，发现是chrome对于position的解析和其他浏览器不同，加入css hack for chrome后，一切正常。

```css
body:nth-of-type(1) #contentbox { position:absolute;}
```

![](/20090314-chrome-css/chrome_min.png)


