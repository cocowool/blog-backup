---
title: IE6利用Javascript进行表单提交遇到的问题
date: 2008-10-15 14:22:01
tag: 
keywords: IE6, javascript
description: IE6下用Javascript提交表单页面不刷新的问题
---

今天碰到了IE6下用Javascript提交表单页面不刷新的问题。
状况是这样的：利用一个超链接进行表单的提交<a href="javascript:void(0);" onclick="submitForm()">提交表单</a>
但是发现要插入的内容已经插入了，页面却不刷新，导致后续的根据提交后返回字符的操作没能够正确执行。

事实上，当单击这个链接到时候，Javascript已经发生并且进行了处理，只是submit()这个事件没有发生，这个情况在IE7里面已经被修正了。
其实A本来就不是用来干submit的活的，所以我们这样用，出了问题也应该理解。

解决这个问题只要把 `javascript:void(0)` 改成 `href="#"` 就可以了。













