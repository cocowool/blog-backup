---
title: 不同浏览器对于换行的处理
date: 2008-10-20 13:21:01
tag: 
---

在一个容器中，如果设定了宽度，一般来说自动换行都是比较正常的，但是如果遇到了连续的英文字符，这个问题就会让人头疼。这不，我们部门的用户在测试的时候输入连续的字符，就出现了容器被撑大而样式变形的情况发生，怎么解决这个问题呢？

<!-- more -->

正常文字的换行（亚洲文字和非亚洲文字）元素拥有默认的 `white-space:normal`，在定义的宽度之后自动换行，如下：

```html
<div id="wrap">正常文字的换行(亚洲文字和非亚洲文字)元素拥有默认的white-space:normal,当定义</div>
<style type='text/css'>
#wrap { white-space : normal; width : 200px; }
</style>
```

**1.（IE浏览器）连续的英文字符和阿拉伯数字,使用word-wrap : break-word ;或者word-break:break-all;实现强制断行**

```html
<style type='text/css'>
#wrap{word-break:break-all; width:200px;}  
#wrap{word-wrap:break-word; width:200px;}
</style>

<div id="wrap">abcdefghijklmnabcdefghijklmnabcdefghijklmn111111111</div>
```
效果：可以实现换行

**2.（Firefox浏览器）连续的英文字符和阿拉伯数字的断行,Firefox的所有版本的没有解决这个问题,我们只有让超出边界的字符隐藏或者,给容器添加滚动条**

```html
<style type='text/css'>
#wrap{word-break:break-all; width:200px; overflow:auto;}
</style>

<div id="wrap">abcdefghijklmnabcdefghijklmnabcdefghijklmn111111111</div>
```

效果：容器正常，内容隐藏

### **对于table**

**1. (IE浏览器)使用 `table-layout:fixed;`强制设置table的宽度,多余内容隐藏**

<table style="table-layout:fixed" width="200">
<tr>
<td>abcdefghigklmnopqrstuvwxyz1234567890ssssssssssssss
</td>
</tr>
</table>
效果：隐藏多余内容

**2.(IE浏览器)使用 table-layout:fixed;强制table的宽度,内层td,th采用word-break : break-all;或者word-wrap : break-word ;换行**

<table width="200" style="table-layout:fixed;">
<tr>
<td width="25%" style="word-break : break-all; ">abcdefghigklmnopqrstuvwxyz 1234567890
</td>
<td style="word-wrap : break-word ;">abcdefghigklmnopqrstuvwxyz 1234567890
</td>
</tr>
</table>
效果：可以换行

**3. (IE浏览器)在td,th中嵌套div,p等采用上面提到的div,p的换行方法**

**4.(Firefox浏览器)使用 table-layout:fixed;强制table的宽度,内层td,th采用word-break : break-all;或者word-wrap : break-word ;换行，使用overflow:hidden;隐藏超出内容，这里overflow:auto;无法起作用
**

<table style="table-layout:fixed" width="200">
<tr>
<td width="25%"  style="word-break : break-all; overflow:hidden; ">abcdefghigklmnopqrstuvwxyz1234567890</td>
<td width="75%" style="word-wrap : break-word; overflow:hidden; ">abcdefghigklmnopqrstuvwxyz1234567890</td>
</tr>
</table>
效果：隐藏多余内容

**5.(Firefox浏览器) 在td,th中嵌套div,p等采用上面提到的对付Firefox的方法**
下面是综合代码

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
<meta http-equiv="Content-Type"content="text/html; charset=gb2312"/>
<title>字符换行</title>
<style type="text/css">
table,td,th,div{border:1px green solid;}
code{font-family:"Courier New", Courier, monospace;}
</style>
</head>
<body>
<h1><code>div</code></h1>
<h1><code>All white-space:normal;</code></h1>
<div style="white-space:normal; width:200px;">Wordwrap still occurs in a td element that has its WIDTH attribute set to a value smaller than the unwrapped content of the cell, even if the noWrap property is set to true. Therefore, the WIDTH attribute takes precedence over the noWrap property in this scenario</div>

<h1><code>IE \ word-wrap : break-word ;</code></h1>
<div style="word-wrap : break-word ; width:200px;">abcdefghijklmnabcdefghijklmnabcdefghijklmn111111111</div>
<h1><code>IE \ word-break:break-all;</code></h1>
<div style="word-break:break-all;width:200px;">abcdefghijklmnabcdefghijklmnabcdefghijklmn111111111</div>

<h1><code>Firefox/ word-break:break-all; overflow:auto;</code></h1>
<div style="word-break:break-all; width:200px; overflow:auto;">abcdefghijklmnabcdefghijklmnabcdefghijklmn111111111</div>
<h1><code>table</code></h1>
<h1><code>table-layout:fixed;</code></h1>
<table style="table-layout:fixed"width="200">
<tr>
<td>abcdefghigklmnopqrstuvwxyz1234567890ssssssssssssss</td>
</tr>
</table>
<h1><code>table-layout:fixed; word-break : break-all; word-wrap : break-word ;</code></h1>
<table width="200"style="table-layout:fixed;">
<tr>
<td width="25%"style="word-break : break-all; ">abcdefghigklmnopqrstuvwxyz1234567890ssssssssssssss</td>
<td style="word-wrap : break-word ;">abcdefghigklmnopqrstuvwxyz1234567890ssssssssssssss</td>
</tr>
</table>
<h1><code>FF \ table-layout:fixed; overflow:hidden;</code></h1>
<table style="table-layout:fixed"width="200">
<tr>
<td width="25%"style="word-break : break-all; overflow:hidden; ">abcdefghigklmnopqrstuvwxyz1234567890</td>
<td width="75%"style="word-wrap : break-word; overflow:hidden; ">abcdefghigklmnopqrstuvwxyz1234567890</td>
</tr>
</table>
</body>
</html>
```