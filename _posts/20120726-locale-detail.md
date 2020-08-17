---
title: locale的详细解释
date: 2012-07-26 21:18:01
tag: 
---


### 一、为什么要关注locale
我们先从一个bug说起：
* Thu Sep 27 2001 Bernhard Rosenkraenzer2.5-0.f.2
- Fix up echo A |grep '[A-Z0-9]' in locales other than C

这时在比较老版本的grep中存在的一个问题。这个bug产生的现象如下：
# echo "aA" | grep '[A-Z]$'
aA
# echo "aA" | grep '[a-z]$'
aA

为什么会发生这种情况呢？在正则表达式中，有一种范围表达式，例如：[0-9]表示从0到9的10个数字，[a-d]，一般我们会认为表示[abcd]四个字符，但实际上也可能表示[aBbCcDd]这7个字符，这个取决于系统的Locale字符集设置。一般LC_ALL为C时，和我们通常认为的序列一致。参考［3］
这也许是一个很古老的bug，但是却引出了对于locale的思考。

### 二、locale到底是什么
locale这个单词中文翻译成地区或者地域，其实这个单词包含的意义要宽泛很多。Locale是根据计算机用户所使用的语言，所在国家或者地区，以及当地的文化传统所定义的一个软件运行时的语言环境。
这个用户环境可以按照所涉及到的文化传统的各个方面分成几个大类，通常包括用户所使用的语言符号及其分类(LC_CTYPE)，数字(LC_NUMERIC)，比较和排序习惯(LC_COLLATE)，时间显示格式(LC_TIME)，货币单位(LC_MONETARY)，信息主要是提示信息,错误信息, 状态信息, 标题, 标签, 按钮和菜单等(LC_MESSAGES)，姓名书写方式(LC_NAME)，地址书写方式(LC_ADDRESS)，电话号码书写方式(LC_TELEPHONE)，度量衡表达方式(LC_MEASUREMENT)，默认纸张尺寸大小(LC_PAPER)和locale对自身包含信息的概述(LC_IDENTIFICATION)。
所以说，locale就是某一个地域内的人们的语言习惯和文化传统和生活习惯。一个地区的locale就是根据这几大类的习惯定义的，这些locale定义文件放在/usr/share/i18n/locales目录下面，例如en_US, zh_CN and de_DE@euro都是locale的定义文件，这些文件都是用文本格式书写的，你可以用写字板打开，看看里边的内容，当然出了有限的注释以外，大部分东西可能你都看不懂，因为是用的Unicode的字符索引方式。
locale把按照所涉及到的文化传统的各个方面分成12个大类，这12个大类分别是：
1、语言符号及其分类(LC_CTYPE)
2、数字(LC_NUMERIC)
3、比较和排序习惯(LC_COLLATE)
4、时间显示格式(LC_TIME)
5、货币单位(LC_MONETARY)
6、信息主要是提示信息,错误信息, 状态信息, 标题, 标签, 按钮和菜单等(LC_MESSAGES)
7、姓名书写方式(LC_NAME)
8、地址书写方式(LC_ADDRESS)
9、电话号码书写方式(LC_TELEPHONE)
10、度量衡表达方式(LC_MEASUREMENT)
11、默认纸张尺寸大小(LC_PAPER)
12、对locale自身包含信息的概述(LC_IDENTIFICATION)。
另外非常重要的一点就是这些分类是彼此独立的，也就是说LC_CTYPE，LC_COLLATE和 LC_MESSAGES等等分类彼此之间是独立的，可以根据用户的需要设定成不同的值。这一点对很多用户是有利的，甚至是必须的。例如，我就需要一个能够输入中文的英文环境，所以我可以把LC_CTYPE设定成zh_CN.GB18030，而其他所有的项都是en_US.UTF-8。

### 三、如何设置locale
设定locale就是设定12大类的locale分类属性，即 12个LC_*。除了这12个变量可以设定以外，为了简便起见，还有两个变量：LC_ALL和LANG。它们之间有一个优先级的关系：LC_ALL>LC_*>LANG。可以这么说，LC_ALL是最上级设定或者强制设定，而LANG是默认设定值。
1、如果你设定了LC_ALL＝zh_CN.UTF-8，那么不管LC_*和LANG设定成什么值，它们都会被强制服从LC_ALL的设定，成为 zh_CN.UTF-8。
2、假如你设定了LANG＝zh_CN.UTF-8，而其他的LC_*=en_US.UTF-8，并且没有设定LC_ALL的话，那么系统的locale设定以LC_*=en_US.UTF-8。
3、假如你设定了LANG＝zh_CN.UTF-8，而其他的LC_*，和LC_ALL均未设定的话，系统会将LC_*设定成默认值，也就是LANG的值 zh_CN.UTF-8 。
4、假如你设定了LANG＝zh_CN.UTF-8，而其他的LC_CTYPE=en_US.UTF-8，其他的LC_*，和LC_ALL均未设定的话，那么系统的locale设定将是：LC_CTYPE=en_US.UTF-8，其余的 LC_COLLATE，LC_MESSAGES等等均会采用默认值，也就是LANG的值，也就是LC_COLLATE＝LC_MESSAGES＝……＝ LC_PAPER＝LANG＝zh_CN.UTF-8。
参考资料
1、grep manpage
2、[locale对字符排序的影响](http://www.linuxeden.com/forum/thread-133147-1-1.html)
3、[一个grep正则表达式匹配字符串的问题](http://bbs.chinaunix.net/thread-3589275-1-1.html)
4、[百度百科 locale](http://baike.baidu.com/view/2129630.htm)
5、[locale详解](http://blog.csdn.net/itblog/article/details/1513673)
6、[locale bug fix](http://rpmfind.net/linux/RPM/redhat/updates/9/i386/grep-2.5.1-7.8.i386.html)
7、[Odd result with [A-Z] and non-C locale](http://bugs.debian.org/cgi-bin/bugreport.cgi?bug=589808)














