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

## 删除固定行

```sh
$ sed '3d' fileName.txt
```



- **/.../**=delimiters
- **n** = line number
- **string** = string found in in line
- **regex** = regular expression corresponding to the searched pattern
- **addr** = address of a line (number or pattern )
- **d** = delete

如何使用sed替换内容，可以有以下的方法：

Remove the 3rd line: 

sed '3d' fileName.txt

Removal of the line containing the string "awk": 

sed '/awk/d' filename.txt

Remove the last line: 

sed '$d' filename.txt

Remove all empty lines: 

sed '/^$/d' filename.txt 

sed '/./!d' filename.txt

Remove line "matched" by a regular expression (by eliminating one containing digital characters (at least 1 digit) located at the end of the line). 

sed '/[0-9/][0-9]*$/d' filename.txt

Removing the interval between lines 7 and 9. 

sed '7,9d' filename.txt

Same operation but replacing the address by "parameters". 

sed '/-Start/,/-End/d' filename.txt

如果希望替换能够永久生效，需要使用 －i 参数

The above examples are only change at the display of the file (stdout1= screen). 

For permanent changes to the old versions (<4) use a temporary file for GNU sed use the **"-i[suffix]"**: 

sed -i".bak" '3d' filename.txt



我在 [SED入门](http://edulinks.cn/2013/09/01/20130901-sed-introduce/) 这篇文章里，简要的介绍了`SED`工具的历史



## 参考资料：

1. [sed delte file lines](http://en.kioskea.net/faq/1451-sed-delete-one-or-more-lines-from-a-file)
2. [SED入门](https://www.cnblogs.com/cocowool/p/3294306.html)

