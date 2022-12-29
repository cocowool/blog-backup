---
title: IE对文档的解析模式及兼容性问题
date: 2013-04-25 23:44:01
tag: 
keywords: IE, IE文档解析模式, IE兼容性, IE兼容性问题
description: 
---

深入研究这个问题源于最近制作的几个页面，交给前端后，发现在IE8下，对于JS动态控制的内容，页面高度不能够随着动态的调整。

仔细检查后发现问题在于 display:inline-block 这个属性。

inline-block 这个属性确实帮我们解决了不少问题，但是IE8在动态内容的渲染支持上，还是会有奇怪的问题。

最后的解决方法是使用 x-ua-compatible ，来强制IE8使用IE7的模式来解析页面。下面是整理的一些相关的资料。

**IE的文档模式**
Document Compatibility 决定了IE如何渲染你的页面，IE支持不同的 document (compatibility) mode。IE6之后的所有IE浏览器都支持以下几种模式：
Standards mode：就是每个浏览器版本所提供的最新的功能，也是默认的模式；
Quirks mode：这个模式强调兼容性超过标准（[影响可以看这里](http://msdn.microsoft.com/en-us/library/gg558047(v=vs.85).aspx)）
Almost-standards mode：这个模式支持最新标准的API，但是界面渲染还是遵循旧版本的标准。

如果一个页面包含了 <!DOCTYPE> 标签，那么IE浏览器会按照标准模式进行解析。如果一个不包含 <!DOCTYPE> 的页面，IE浏览器使用 Quirks mode 来进行解析，这种情况下可能会有奇怪的事情发生。

大多数时候，我们都使用标准的模式来指定文档模式。这样能够确保适应尽可能多的标准。

<!DOCTYPE html>

有些时候，我们需要指定一些高版本的浏览器使用低版本的文档模式解析页面，这时我们可以使用  x-ua-compatible 头标签来实现。例如：

```html
<html>
<head>
  <!-- Use Internet Explorer 9 Standards mode -->
  <meta http-equiv="x-ua-compatible" content="IE=9">
  <title>My Page</title>
</head>
<body>
	<p>Content goes here.</p>
</body>
</html>
```

上面这段代码，在不同的浏览器版本中，有不同的表现，如下表：



记住以下几个规则：

* 如果页面指定了DOCTYPE并且也使用了x-ua-compatible标签，则x-ua-compatible标签将覆盖DOCTYPE
* 如果浏览器支持x-ua-compatible标签，浏览器会使用他所支持的最高模式显示，则未必是标签指定的版本
* 对于不支持x-ua-compatible标签的老版本浏览器，使用<!DOCTYPE>来决定文档模式
* IE9和之前版本的浏览器，在遇到没有指定<!DOCTYPE>的页面时，使用IE5 Quirks模式解析文档，所以建议所有文档都要包含<!DOCTYPE>

**注意：**所有版本的浏览器在解析 <!DOCTYPE html> 时，都会使用各自版本所支持的最高的标准模式来解析，所以推荐都是用HTML5的标记方式。IE9中，包含框架Frameset的页面，子页面的模式和父页面一致。但是在IE10中，可以分别指定。

**用法：**
x-ua-compatible 头标签大小写不敏感，必须用在 head 中，必须在除 title 外的其他 meta 之前使用。
1、使用一行代码来指定浏览器使用特定的文档模式。
```html
<meta http-equiv="x-ua-compatible" content="IE=9" >
<meta http-equiv="x-ua-compatible" content="IE=8" >
<meta http-equiv="x-ua-compatible" content="IE=7" >
```

2、在一些情况下，我们需要限定浏览器对文档的解析到某一特定版本，或者将浏览器限定到一些旧版本的表现中。可以用如下的方式：
```html
<meta http-equiv="x-ua-compatible" content="IE=EmulateIE9" >
<meta http-equiv="x-ua-compatible" content="IE=EmulateIE8" >
<meta http-equiv="x-ua-compatible" content="IE=EmulateIE7" >
```

使用这种写法，浏览器或者使用标准模式进行解析，或者使用 IE5 Quirks 模式进行解析。

3、为了测试，我们也可以使用下面的语句指定浏览器按照最高的标准模式解析页面。
```html
<meta http-equiv="x-ua-compatible" content="IE=edge" >
```

4、多个模式的指定。我们可以用逗号分割多个版本，这种情况下，浏览器会从这个列表中选择一个他所支持的最高版本来使用标准模式进行渲染。如下面的例子，在IE8进行浏览时，将会使用IE7的标准模式进行渲染，因为他本身不支持IE9和IE10。
```html
<meta http-equiv="x-ua-compatible" content="IE=7,9,10" >
```

参考资料：
1、[ie8 bugs](http://jhop.me/ie8-bugs)
2、[Cross Browser Support for inline-block Styling](http://foohack.com/2007/11/cross-browser-support-for-inline-block-styling/)
3、[使用x-ua-compatible来设置IE兼容模式](http://www.cnblogs.com/nidilzhang/archive/2010/01/09/1642887.html)
4、[MSDN：Define Document compatibility](http://msdn.microsoft.com/en-us/library/cc288325(v=vs.85).aspx#SetMode)
5、[The effects of quirks mode emulation](http://msdn.microsoft.com/en-us/library/gg558047(v=vs.85).aspx)
6、[Specifying legacy document modes](http://msdn.microsoft.com/en-us/library/jj676915(v=vs.85).aspx)
7、[IE9提供四种兼容模式](http://www.iefans.net/ie9-xuanran-moshi-jianrong-jiuban/)