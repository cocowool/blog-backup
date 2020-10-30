---
title: 使用 sed 进行文本修改的各种姿势
tags:
category: 运维
---

## SED 介绍

`sed`（Stream Editor）意为流编辑器，是Unix常见的命令行程序。是Bell实验室的 [Lee ](http://en.wikipedia.org/wiki/Lee_E._McMahon)[E.McMahon](http://en.wikipedia.org/wiki/Lee_E._McMahon) 在1973年到1974年之间开发完成，目前可以在大多数操作系统中使用。

 `sed` 的出现是作为 `grep` 的一个继任者，因为 `grep` 只能简单的进行查找和替换，但是考虑还可能会有删除等各种需求，McMahon 开发了一个更具通用性的工具。`sed` 著名的语法规则包括使用 `/` 进行模式匹配，以及 `s///` 来进行替代。与同期存在的工具 `ed` 一起，`sed` 的语法影响了后来发展的 ECMAScript 和 Perl。GNU `sed` 添加了很多特性，包括著名的 in-place editing。

## 语法

看一下 `sed` 的基本语法：

```sh
sed [-Ealn] command [file ...]
sed '{[/]<n>|<string>|<regex>[/]}d' <fileName>
sed '{[/]<adr1>[,<adr2>][/]d' <fileName>
```

> The sed utility reads the specified files, or the standard input if no files are specified, modifying the input as specified by a list of commands. The input is then written to the standard output.

`sed` 从文件或者标准输入中获取内容，然后按照命令列表对内容进行修改，最后再将结果发送给标准输出。

## 删除文件的内容

```sh
$ sed '3d' fileName.txt   #删除第3行，并将修改打到标准输出
$ sed -i '3d' fileName.txt #删除第3行，并在文件内生效，后面的命令不再加这个参数

# 删除最后一行
$ sed '$d' fileName.txt

# 删除7-9行
$ sed '7,9d' fileName.txt

# 删除所有空行
$ sed '/^$/d' fileName.txt 
$ sed '/./!d' fileName.txt

# 删除匹配到字符串的行
$ sed '/awk/d' fileName.txt 

# 删除两个匹配之间所有的行
$ sed '/patternA/,/patternB/d' fileName.txt

# 删除匹配的行到第 n(5) 行之间的内容，同理也可以反过来，删除某一行到匹配行之间的内容
$ sed '/pattern/,5d' fileName.txt

# 删除符合多个条件的行
$ sed '/patternA/d;/patternB/d' fileName.txt
```

![image-20201030194502145](20201024-use-sed-replace-text/image-20201030194502145.png)



![image-20201030194207582](20201024-use-sed-replace-text/image-20201030194207582.png)



## 替换文件的内容





## 参考资料：

1. [sed delte file lines](http://en.kioskea.net/faq/1451-sed-delete-one-or-more-lines-from-a-file)
2. [SED入门](https://www.cnblogs.com/cocowool/p/3294306.html)
3. [sed 删除文本中的内容](https://www.cnblogs.com/crazymagic/p/11147988.html)

