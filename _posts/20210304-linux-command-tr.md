---
title: Linux下转换文件的换行符
date: 2021-03-03 18:22:46
tags:
keywords:
description: 
---

在不同的文件系统下，文件默认的换行符是不同的。常见的就有 Windows（以\r\n作为换行符）、Unix（以\n作为换行符）、Mac（以\r作为换行符）三种格式。

Linux下有一个非常方便的命令 `tr` 能够快速的实现不同换行符的转换。它的用法是这样的：

```sh
$ tr [-Ccsu] string1 string2
```

通过使用 `tr` ，我们可以很容易的实现 `sed` 的许多基本功能，比如用一个字符替换另一个字符，获取完全去掉一些字符。

回到我们文章开始的需求，实现换行符替换的命令如下：

```sh
#将Mac格式换行符转换为Unix格式换行符
$ tr '\r' '\n' < input-file 

#将Unix格式换行符转换为Mac格式换行符
$ tr '\n' '\r' < input-file

#将Windows格式换行符转换为Unix格式换行符
$ tr -d '\r' < input-file
```

如果需要将替换结果存入文件中，可以使用下面的命令。

```sh
$ tr '\n' '\r' < input-file > output-file
```

### 参考资料：

1. [Convert end-of-line characters](http://hints.macworld.com/article.php?story=20031018164326986)