---
title: 使用正则匹配并显示匹配的内容
date: 2014-11-12 21:14:01
tag: 
keywords: find, find linux, 正则匹配
description: 利用 find 实现将文件中的内容查找并显示出来。
---

最近在解析HTML文件，遇到这样的一个场景。我需要将HTML文件中data-url="xxx"中的xxx查找并显示出来。

首先考虑使用find，但是find只能显示包含查找内容的文件名，不符合场景的需要。

接着考虑使用grep，配合-o参数，确实可以显示匹配到的内容，但是grep正则的时候，总是贪婪匹配，不能够最少匹配。如果非贪婪匹配，则需要使用GNU grep，命令如下：grep -o -P。但是Mac中的grep默认是BSD grep，所以这条路也走不通了。

最后，只有求助于perl的命令行方式来实现了，最后代码如下：

```sh
$ find www.maserati.com.cn -name *html -exec cat {} \; | perl -ne 'print $1 if /data-url=\"(http:\/\/.*?)\"/;'
```

