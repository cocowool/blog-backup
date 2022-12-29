---
title: SED入门
date: 2013-09-01 11:05:01
tag: 
keywords: sed, sed linux, sed入门
description: SED的入门介绍，SED（Stream Editor）意为流编辑器，是Unix常见的命令行程序。。
---


使用Linux多年，SED和AWK两大神器却始终无法得心应手的来提高自己的工作效率，每每需要查找替换，都要依赖于ST2等一众图形工具，深感愧疚，乃专门抽时间学习之，志在使之真正成为左右手。

**SED历史**

SED（Stream Editor）意为流编辑器，是Unix常见的命令行程序。是Bell实验室的[Lee](http://en.wikipedia.org/wiki/Lee_E._McMahon)[E.McMahon](http://en.wikipedia.org/wiki/Lee_E._McMahon)在1973年到1974年之间开发完成，目前可以在大多数操作系统中使用。

SED的出现是作为grep的一个继任者，因为grep只能简单的进行查找和替换，但是考虑还可能会有删除等各种需求，McMahon 开发了一个更具通用性的工具。SED著名的语法规则包括使用 / 进行模式匹配，以及 s/// 来进行替代。与同期存在的工具ed一起，sed的语法影响了后来发展的 ECMAScript 和 Perl。GNU sed 添加了很多特性，包括著名的 in-place editing。

**SED基础**

**替换**Substitution：作为流编辑器，默认情况下，SED会将处理结果输出到终端上，我们可以使用重定向符号，来达到生成新文件的目的。

sed 's/regexp/replacement/g' inputFileName > outputFileName

在有些版本的sed中，必须加 -e 参数。

**其他命令**：下面这个命令将文件中的空行或者只包含空格的行删除，其中使用了正则的形式，^表示行首，$表示行尾。

sed '/^ *$/d' inputFileName

**脚本 Scripting**：SED支持将命令写在脚本中，通过脚本的方式调用命令，同样，也支持Unix中的管道处理。

sed -f subst.sed inputFileName > outputFileName

subst.sed 文件的内容
#!/bin/sed -f
s/x/y/g

**原地替换 In-place editing**：这个特性在GNU SED中引入，在GNU版本中，-i后的参数是可选的。后来BSD将SED引入后，对于-i参数的处理有所不同，变成了必须的参数。

sed -i 's/abc/def/' file

**常用SED问题**
**1、Mac下替换文本中的内容**

Mac的Sed和Linux中的稍有不同，如果需要替换文本内容，需要加 -i 的参数。但是加上 -i 后就必须指定备份文件的后缀名，也就是说，执行替换的时候，会自动备份原文件。如果我们不想要备份怎么办，可以用下面的方法：

sed -i '' "s/xx/yy/g" abc.txt

也可以这么写

sed -ig "s/xx/yy/g" abc.txt


参考资料：
1、[SED入门](http://jl453625978.blog.163.com/blog/static/86041705201171511624868/)
2、[Mac OS中用SED命令替换文本内容](http://blog.saymoon.com/2012/04/mac-os-%E4%B8%AD%E7%94%A8-sed-%E5%91%BD%E4%BB%A4-%E6%9B%BF%E6%8D%A2%E6%96%87%E6%9C%AC%E4%B8%AD%E7%9A%84%E6%8C%87%E5%AE%9A%E5%86%85%E5%AE%B9/)
3、[Mac上的SED](http://bukkake.iteye.com/blog/829516)
4、[SED Wiki](http://en.wikipedia.org/wiki/Sed)












