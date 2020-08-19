---
title: bash中的字符串引用
date: 2009-08-27 23:30:01
tag: 
---

bash中的字符串引用是一件很简单的事情，我们大多数人对此看一眼就能明了，但是今天这个技巧，也许能够帮你在未来节省不少时间。

引用符包括 “（双引号）和 ' （单引号），最基本的用法就是引用字符串。单引号同双引号稍有不同，单引号不支持转义，但是双引号支持。例如：

a="hello \"the\" world"            输出        hello "the" world
a='hello "the" world'              输出     hello "the" world
a='hello \'the\' world'            输出        报错 unexpected EOF while looking for matching ‘
a="hello 'the' world"              输出        hello 'the' world

b="the"
a='hello \"$b\" world'            输出        hello \"$b\" world
a="hello \"$b\" world"            输出        hello "the" world

OK，以上的这些使用都没有问题，我们经常遇到问题的地方是在处理文件名中有空格的文件时。例如：

#/bin/bash

for i in $(find .)
do
echo $i
done

打印的结果是

.
./a.txt
./b.txt
./file
with
space.txt
./quote.sh

一个解决办法是，将原来的字段分隔符（nternal Field Separator ）替换为换行，如下：

#/bin/bash

newline='
'

OIFS=$IFS
IFS=$newline
files=($(find .))
IFS=$OIFS

for i in "${files[@]}"
do
echo $i
done


另外一个问题。当我们需要访问一些单词的时候，如何避免被转义字符所影响。例如：a="hello \"there big\" world"
#!/bin/bash

a="hello \"there big\" world"

for i in $a
do
echo $i
done

程序的输出和我们希望的不太一样，解决的办法是使用eval，如下：

#!/bin/bash

a="hello \"there big\" world"
eval set -- $a

for i in "$@"
do
echo $i
done

看到这里，我们在处理单引号、双引号引用的时候，一般就不会出问题了。











