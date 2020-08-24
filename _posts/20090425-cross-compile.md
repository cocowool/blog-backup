---
title: Linux下不同文件编码的转换
date: 2009-04-25 17:45:01
tag: 
---

为了纠正自己对于编码的理解错误，加深对于各种不同编码的认识，搜集、学习了一些关于编码的相关资料。
现在对于编码有了一个比较清楚的认识，能够处理一般情况下的乱码问题了。
一、什么是编码。

字符编码(Character Encoding)可以说就是让某一字符序列匹配一个指定集合中的某一东西，常见的例子包括长短电键组合起来表示的摩斯电码(Morse Code)、Baudot code、Unicode和用二进制来表示的ASCII(American Standard Code for Information Interchange)码、这样便能够将文本在计算机中存储和通过通信网络发送出去。

字符表(Character Set)和字符编码(Character Encoding)经常被混淆。实际上Character Set(字符表)只是指一组为特定的Encoding System(编码系统)使用的符号集合，而不包含他们的数字含义或者顺序。而Character Encoding(字符编码)则包括了Character Set（字符表）和每个字符在字符编码集中的表示方法。由于历史的原因，MIME(Multipurpos Internet Mail Extensions)和使用这种编码的系统使用属于“字符集(Charset)”来表示用于将一组字符编码成一系列8位字节数据的整个系统。

摩斯电码(Morse Code)发明于1840年，用来编码字母、数字和拉丁字符，都用长短组合的电键来表示。
ASCII码发明与1963年，是由7位(7-bit)的二进制代码表示字符、数字、符号和固定长度的设备控制代码。同年，IBM使用8位(8-bit)的编码机制实现了ASCII的扩展EBCDIC(IBM's Extended Binary Coded Decimal Interchange Code)。

二、现代编码模型

Unicode和与他同等的标准ISO/IEC10646 UCS(Universal Character Set)共同构成了现代字符编码，为了更详细的解释这两个标准我们需要了解下面的一些概念。

字符表（Character Set或Character repertoire）是一个系统所支持的所有抽象字符的总和。字符表可以是封闭的，例如ASCII和多数ISO/IEC 8859，也可以是开放的，例如Unicode等可以添加新的符号。特定字符表中的字符反映了如何将书写系统分解成线性信息单元的问题。

编码字符集（CCS：Code Character Set）定义了如何使用称为码点的非负整数表示一个字符表。一个完整的字符集和对应的整数一起称为“编码字符集”。多个编码字符集可以表示同样的字符表，例如ISO-8859-1和IBM的代码页037和500覆盖同样的字符表但是将他们映射为不同的代码。

字符编码形式（CEF：Character Encoding Form）定义将编码字符集的整数代码转换成有限大小整数代码值以有利于使用固定位的二进制表示数字的形式的系统存储。

字符编码机制（CES：Character Encoding Scheme）定义固定大小的整数代码如何映射到适合基于8位字节数居的文件系统存储或者基于8位字节网络传输。在多数使用Unicode的场合，一个简单的字符编码机制用来指定每个整数的字节顺序是大字节在先排列顺序（Big-Endian）或者小字节在先排列顺序（Little-Endian）。然而，有些复杂的字符编码机制使用转义序列在几种简单编码机制和用于减小每个单元所用字节数的压缩机制之间切换。

Big endian和Little endian。
“endian”这个词出自《格列佛游记》。小人国的内战就源于吃鸡蛋时是究竟从大头(Big-Endian)敲开还是从小头(Little-Endian)敲开，由此曾发生过六次叛乱，其中一个皇帝送了命，另一个丢了王位。

三、文件编码格式。

从文件编码的方式来看，文件可分为ASCII文件和二进制文件。
ASCII文件也称为文本文件，这种文件在磁盘中存放时每个字符对应一个字节，用于存放对应的ASCII码。
二进制文件是按二进制的编码方式来存放文件的。
二进制文件虽然也可在屏幕上显示，但其内容无法读懂。C系统在处理这些文件时，并不区分类型，都看成是字符流，按字节进行处理。输入输出字符流的开始和结束只由程序控制而不受物理符号(如回车符)的控制。 因此也把这种文件称作“流式文件”。

四、字符编码的转换。

由于目前共存的众多字符编码方法，为了方便，人们开发了很多在不同字符编码机制间转换数据的程序：

多平台方法：
iconv        提供标准的程序和API来进行编码转换；
convert_encoding.py    基于Python的文本文件转换工具；
decodeh.py    提供算法和模块来谈测字符的编码；

Linux:
recode    转换文件编码；
Utrac    转换文件编码；
cstocs    转换文件编码；
convmv    转换文件名编码；
enca    分析给定文件的编码；

Windows:
cscvt    字符集转换工具；

五、Linux下利用Vim查看文件编码和进行编码转换。

在VIM中查看文件编码
:set fileencoding

在VIM中执行文件编码转换
:set fileencoding=utf-8

我的系统Fedora 10的VIM默认是不支持GBK的，如果想让VIM支持，需要修改～/.vimrc
set fileencodings=utf-8,gbk
这样VIM就会按照这个顺序去匹配文件的编码

五、使用命令进行文件编码的转换

可以使用最常用的ICONV命令进行文件编码的转换，格式
iconv -f encoding -t encoding inputfile
更详细的说明可以man iconv


[参考文献]
1、MSDN    http://msdn.microsoft.com/zh-cn/library/x14b16ab(VS.80).aspx
2、文件编码格式    http://blog.chinaunix.net/u2/82877/showart_1892207.html
3、Linux下查看文件编码、文件编码转换和文件名编码    http://www.luoxf.net/wordpress/archives/227
4、字符、字节和编码        http://www.regexlab.com/zh/encoding.htm
5、CSDN相关文章    http://blog.csdn.net/fancyaphy/archive/2006/03/09/619972.aspx
6、WIKI字符编码    http://zh.wikipedia.org/wiki/%E5%AD%97%E7%AC%A6%E7%BC%96%E7%A0%81
7、Character Encoding    http://en.wikipedia.org/wiki/Character_encoding
8、MIME    http://en.wikipedia.org/wiki/MIME











