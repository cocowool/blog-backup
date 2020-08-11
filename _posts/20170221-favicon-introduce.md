---
title: 自定义地址栏与收藏夹中的图标
date: 2017-02-21 09:42:01
tag: 
---


## 关于Favicon
favicon 在英文中有几个别名，叫做 shortcut icon，website icon，tab icon，URL icon，bookmark icon，对应中文来说也叫作网页小图标、网站缩略图或收藏夹图标、书签图标。这实际上就是与某个网站或地址关联的图标文件。如果网站上传了这个图标文件，用户使用浏览器（仅限于支持这个功能的浏览器）浏览时，就可以把图标显示在浏览器的地址栏中（有时也会显示在历史记录中），如果添加了收藏则收藏夹中也可以看到图标。现在支持标签页的浏览器，会将图标显示在标签上。对于移动终端，如果在系统中建立了网站的快捷方式，则可以使用图标来作为系统桌面的图标。

## Favicon的历史
1999年三月，微软发布了Internet Explorer 5，是首款支持 favicon 的浏览器。最初，favicon 就是一个放在网站根目录下的名为```favicon.ico```的文件。主要使用在 Internet Explorer 的收藏夹中，如果网站被添加到收藏夹中，那么在地址栏中也会被显示出来。这么做还有一个用意就是统计有多少用户收藏了这个网站（可以在日志中看 favicon 请求的次数）。现代浏览器则不管用户是否收藏该网站，都会把图标显示在地址栏或页签中。参考资料中[Favicon诞生记](https://zhuanlan.zhihu.com/p/19989351)有关于 Favicon 产生之初的有趣故事，大家可以看一看。
[Word Wide Consortium W3C](https://en.wikipedia.org/wiki/World_Wide_Web_Consortium)在HTML 4.01（1999年）中将favicon定为标准，并且在稍后2000年1月份定为 XHTML 1.0的标准。与IE中限制了存放位置和文件类型不同，标准仅是规定了应当使用一个带有```rel```属性的链接元素在```head```区域指定favicon的文件和类型。
2003年，```.ico```被第三方组织[Internet Assigned Numbers Authority IANA](https://en.wikipedia.org/wiki/Internet_Assigned_Numbers_Authority)注册在 MIME type```image/vnd.microsoft.icon```中。
[RFC5988](https://tools.ietf.org/html/rfc5988),在2010年的HTML5标准中，明确了```rel="icon"```的使用方法。```<link rel="shortcut icon" type="image/x-icon" href="favicon.ico" />```这种流行的用法明确了两个关系，一个作为```shortcut```快捷图标和```icon```图标。但是```shortcut```并非标准。在2011年的HTML标准中对此进行了解释

## 使用方法及浏览器支持
基本的 favicon 使用方法如下：
```<link rel="shortcut icon" href="http://example.com/myicon.ico" />```
也可以使用其他类型的文件格式，这需要对应的浏览器支持。如下：
```<link rel="icon" type="image/png" href="http://example.com/myicon.ico" />```
浏览器对于图片格式的支持


## 设备支持
对于使用iOS的苹果（Apple）设备，以及部分安卓（Android）设备，可以使用 添加到主屏幕 Add to Home Screen（Web Clip） 功能在手机的主屏幕上添加一个自定义的图标。这个功能通过以下代码实现```<link rel="apple-touch-icon" ... >```。如果对应的 ico 文件没有找到，则会将网页的截屏显示在桌面上。图标的推荐尺寸随着显示设备的分辨率越来越高，已经从60**60主键增加到了256**256，对于iPad上的图标尺寸，也从76**76一直增加。现在，移动端平台变得越来越庞杂，例如 Google TV 使用 96**96 的尺寸、Chrome 网上商店需要 128*128 的图标。所以在定制尺寸的时候，我们需要考虑到底要支持哪些平台。
下面的声明就考虑了iPhone的不同版本：
```html
<link rel="apple-touch-icon" sizes="57x57" href="/apple-touch-icon-57x57.png"> 
<link rel="apple-touch-icon" sizes="114x114" href="/apple-touch-icon-114x114.png"> 
<link rel="apple-touch-icon" sizes="72x72" href="/apple-touch-icon-72x72.png"> 
<link rel="apple-touch-icon" sizes="144x144" href="/apple-touch-icon-144x144.png"> 
<link rel="apple-touch-icon" sizes="60x60" href="/apple-touch-icon-60x60.png"> 
<link rel="apple-touch-icon" sizes="120x120" href="/apple-touch-icon-120x120.png"> 
<link rel="apple-touch-icon" sizes="76x76" href="/apple-touch-icon-76x76.png"> 
<link rel="apple-touch-icon" sizes="152x152" href="/apple-touch-icon-152x152.png">
```
iOS系统会对icon文件进行优化，增加圆角、阴影，如果不希望系统做这些优化，则可以使用```<link rel="apple-touch-icon-precomposed" href="somepath/image.png" />```

## HTML5 规范
现在的HTML5规范推荐为图标指定不同的尺寸，使用方法为```rel="icon" sizes="空格分割的图标尺寸"```。通过```type="file content-type"```也可以指定多种文件类型，包括微软的```.ico```以及苹果的```.icns```。

## 动画图标
目前有Chrome、Firefox、Opera支持动画图标。

## 遭受的批评
由于总是需要到一个固定位置查找文件，favicon 有时会导致奇怪的页面加载缓慢问题以及日志中不必要的404错误。W3C并没有对rel属性进行标准化，所以诸如```shortcut icon```等关键词仍然被浏览器接收。
Favicons 经常会在针对HTTPS网站的 网络钓鱼（phishing） 或者 网络窃听（eavesdropping） 攻击中被使用。许多浏览器都会将 favicons 显示在浏览器地址栏的左边，而这里经常是用来显示在 HTTPS 网站的证书是否可信，通过使用一个类似的图片来欺骗用户让用户认为这是一个可信站点。一些如 SSLStrip 的中间人攻击工具也使用这种伎俩。为了识别这种伎俩，有的浏览器在Tab页签中显示 favicon ，在地址栏的最左边显示协议的安全状态。

## 在线制作的工具
鉴于所有浏览器都对 ico 支持良好，所以目前大部分的 favicon 还都是 ico 格式的。但是它的局限性越来越明显，对于新的浏览器而言，现在都倾向于用 png 图片。不管怎么样，如果想手工制作出满足这么多平台的图标来非常费时间，但是通过[Real Favicon Generator](http://realfavicongenerator.net/)网站，我们只需要上传一个至少70*70尺寸的图片上去，就可以生成种类丰富的图标。
参考资料：

1. [Favicon](https://en.wikipedia.org/wiki/Favicon)
2. [教你制作完美的Favicon](http://www.wenwenyun.com/w3c/design/2014/1126/3400.html)
3. [Real Favicon Generator](http://realfavicongenerator.net/)
4. [Favicon诞生记](https://zhuanlan.zhihu.com/p/19989351)
5. [弄懂Favicon](http://www.w3cplus.com/css/understand-the-favicon.html)












