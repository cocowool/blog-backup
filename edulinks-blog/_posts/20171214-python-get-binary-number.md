---
title: Python获取数字的二进制值
date: 2017-12-14 14:58:11
keywords: python, python获取数字二进制, 二进制
description: 如何通过Python获取一个整形数字的二进制表示。
---


## 目标
想要获取一个整形数字的二进制表示

> 本文在 Python 3.10.6 环境下验证通过，如果使用 Python 2 相关版本环境，需要适当调整。

## bin 内置函数
看一下官方的解释
Convert an integer number to a binary string prefixed with “0b”. The result is a valid Python expression. If x is not a Python int object, it has to define an **index**() method that returns an integer. Some examples。

```python
>>> bin(3)
'0b11'
>>> bin(-10)
'-0b1010'
```
If prefix “0b” is desired or not, you can use either of the following ways.
```python
>>> format(14, '#b'), format(14, 'b')
('0b1110', '1110')
>>> f'{14:#b}',f'{14:b}'
('0b1110', '1110')
```
可以看到bin函数返回二进制数字表示的形式是采用了负号，而不是补码的形式。那么如何获得补码形式的二进制表示呢，很简单只需要对数值进行与操作就可以。
```python
>>> bin(-27 & 0b1111111111111111)
'0b1111111111100101'
```
这个例子手工指定了位数，也可以用下面带参数的形式
```python
def bindigits(n, bits):
    s = bin(n & int("1"*bits, 2))[2:]
    return ("{0:0>%s}" % (bits)).format(s)

>>> print(bindigits(-31337, 24))
111111111000010110010111
```

## Python中的二进制数据类型 
Python中提供了三种二进制数据类型：
* bytes
* bytearray
* memoryview

### Bytes Object

Bytes对象是不可改变顺序的字节串。

Bytes语法与String类似，只不过需要以```b```开头。

```python
# 单引号
b'still allows embedded "double" quotes'
# 双引号
b"still allows embedded 'single' quotes"
# 三引号
b'''3 single quotes'''
b"""3 double quotes"""
```

只有ASCII字符才可以这么写，除此之外的都必须用bytes的形式来写。
由于2个十六进制数字精确地对应于单个字节，所以十六进制数字是用于描述二进制数据的通常使用的格式。

### Bytearray Object

bytearray 是bytes对象的可变对象。对于bytearray对象没有专用的字面值语法，只能通过构造函数创建。

### memoryview 函数

**memoryview()** 函数返回给定参数的内存查看对象(memory view)。

代码示例

```python
>>> v = memoryview(bytearray("abcefg", "utf-8"))
>>> print(v[1])
98
>>> print(v[-1])
103
>>> print(v[1:4].tobytes())
b'bce'
```



## 参考资料：

1、[Python bin](https://docs.python.org/3/library/functions.html#bin)
2、[Two's Complement Binary in Python?](https://stackoverflow.com/questions/12946116/twos-complement-binary-in-python)
3、[integers](http://docs.python.org/reference/lexical_analysis.html#integers)
