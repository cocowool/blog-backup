---
title: Python 3.6学习笔记（一）
date: 2017-03-22 14:42
keywords: python, python 3.6, python学习笔记, python基础
description: Python3.6学习笔记，基础的数据类型。
---

> 知识是一座宝库，而实践就是开启这座宝库的钥匙。
**----Thomas Fuller**


## 开始之前



## 基础示例

Python语法基础，python语法比较简单，采用缩紧方式。
```python
# print absolute value of a integer
a = 100
if a >= 0:
    print(a)
else:
    print(-a)
```
可以看到，注释以**#**开头，python的变量不需要任何前缀，行结束不需要结束符号，非常符合我们自然语言的阅读和书写习惯。当语句以```:```结尾时，缩紧的语句视为代码块。
> Python是大小写敏感的，这一点需要特别注意。


### 输入与输出
Python可以使用input()函数读取用户的输入，使用print()进行屏幕的输出。默认情况下，输入的内容为字符数据类型。

## 数据类型

### 整数
Python可以处理任意大小的整数，在程序中的表示方法和数学上的写法一模一样，可以使用```0xff00```的方式表示十六进制。
> Python中使用**/**进行除法运算，得到的结果是浮点数。使用**//**进行除法运算，得到的结果是整数。使用**%**，表示取余数。


### 浮点数
浮点数就是小数，可以使用数学写法，如：```1.23```,```-9.01```,也可以使用科学计数法表示，如：```1.23e9```,```1.2e-5```。

### 字符串
字符串是使用**`**或**”**括起来的任意文本。可以使用***对特殊字符进行转义。可以使用**r''**的形式，表示内部的字符串默认不进行转义。对于字符串内有换行等多行内容的，可以使用**’’’…’’’**的形式，多行字符前也可以加**r*。
> 在最新的Python 3版本中，字符串是以Unicode编码的，也就是说，Python的字符串支持多语言。对于单个字符的编码，Python提供了ord()函数获取字符的整数表示，chr()函数把编码转换为对应的字符。以Unicode表示的str通过**encode()**方法可以编码为指定的bytes，如：```>>> 'ABC'.encode('ascii’)```。反过来，如果我们从网络或磁盘上读取了字节流，那么读到的数据就是bytes。要把bytes变为str，就需要用**decode()**方法

> 在Python中，采用的格式化方式和C语言是一样的，如下:
```>>> 'Hi, %s, you have $%d.' % ('Michael', 1000000)```


### 布尔值
布尔值：**True**、**False**。也可以用布尔代数表示：**3 > 2**,* 3 < 2**。布尔值的运算符号：**and**、**or**、**not*。

### 空值
空值是Python里一个特殊的值，用**None**表示。

### 变量
Python中的变量时动态变量，即变量的属性是在赋值的时候才决定的，变量名称必须是大小写英文、数字和**_**的组合，且不能用数字开头。Python中没有常量的概念，通常使用全部大写的变量来表示常量。

### 列表 list
list 是一种有序的集合，可以随时添加和删除其中的元素。用索引来访问list中每一个位置的元素，索引是从0开始的。当索引超出了范围时，Python会报一个IndexError错误。如果要取最后一个元素，除了计算索引位置外，还可以用-1做索引，直接获取最后一个元素。
```python
>>> classmates = [‘Michael’,’Bob’,’Tracy']
>>> classmates
[‘Michael’,’Bob','Tracy’]
>>> len(classmates)
3
>>> classmates[0]
‘Michael’
>>> classmates.append('Adam’)    #追加元素到末尾
>>> classmates
['Michael', 'Bob', 'Tracy', 'Adam’]
>>> classmates.insert(1, 'Jack’)    #追加元素到指定位置
>>> classmates
['Michael', 'Jack', 'Bob', 'Tracy', ‘Adam’]
>>> classmates.pop()    #删除末尾的元素，使用pop(i)可以删除指定位置的元素
'Adam'
>>> classmates
['Michael', 'Jack', 'Bob', 'Tracy']
```

### 元组 tulp
元组**tulp**也是有序列表，与list的区别在于，一旦初始化就不能修改。没有append、insert等方法。
tulp的定义方式如下：
```python
>>> classmates = (‘Michael’, ‘Bob’, ’Tracy')
```
> tulp 本身的元素不能发生变化，但是如果元素为list，那么list中的内容是可变的。


### 字典 dict
dict 全称 dictionary ，在其他语言中称为 map，在PHP中其实就是 Array，使用键-值(Key-Value)的方式进行存储，具有极快的查找速度。使用范例
```python
>>> d = {'Michael': 95, 'Bob': 75, 'Tracy': 85}    #dict的定义
>>> d['Michael’]    #dict取值的方式
95
>>> d['Adam'] = 67    #dict设置新值的方式
>>> 'Thomas' in d     #判断key是否存在
False
>>> d.get('Thomas’)   #get方式取值，如果不存在则返回None
>>> d.get('Thomas',-1)#指定不存在时的返回值
-1
>>> d.pop('Bob’)      #删除某个key
75
```

### set
set和dict类似，也是一组key的集合，但不存储value。如下示例：
```python
>>> s = set([1, 2, 3])    #初始化时提供一个list作为输入集合
>>> s
{1, 2, 3}
>>> s.add(4)              #使用add方法添加元素
>>> s
{1, 2, 3, 4}
>>> s.remove(2)           #使用remove方法
>>> s
{1, 3, 4}
```
set可以看成数学意义上的无序和无重复元素的集合，因此，两个set可以做数学意义上的交集、并集等操作。

## 控制语句

### 条件判断
条件判断比较简单，主要是不要忘记写**```:```**，看看例子吧。
```python
>>> age = 3
>>> if age >= 18:
...     print('your age is', age)
...     print('adult')
... else:
...     print('your age is', age)
...     print('teenager')
...
your age is 3
teenager
```
更多的条件判断。
```python
>>> age = 3
>>> if age >= 18:
...     print('adult')
... elif age >= 6:
...     print('teenager')
... else:
...     print('kid')
...
kid
```

### 循环
Python中的循环有两种，一种是**for...in**循环，依次把list或tulp中的每个元素迭代出来。
```python
>>> classmates
['Michael', 'Jack', 'Bob', 'Tracy']
>>> for name in classmates:
...     print(name)
...
Michael
Jack
Bob
Tracy
```
第二种循环是**while**循环，只要条件满足，就不断循环，条件不满足时退出循环。
```python
sum = 0
n = 99
while n > 0:
    sum = sum + n
    n = n - 2
print(sum)
```
> **跳出循环**，可以使用**break**跳出循环。
**跳过本次循环**,可以使用**continue**跳过本次循环，继续下一次循环。


## 函数

### 调用函数
Python中内置了很多函数，可以直接调用。在交互模式中，可以通过```help(abs)```查看函数的用法。

### 定义函数
在Python中，定义一个函数要使用```def```语句，依次写出函数名、括号、括号中的参数和冒号:，然后，在缩进块中编写函数体，函数的返回值用```return```语句返回。如果没有return语句，函数执行完毕后也会返回结果，只是结果为None。return None可以简写为return。
示例如下：
```python
def my_abs(x):
    if x >= 0:
        return x
    else:
        return -x
```
> 在Python交互环境中定义函数时，注意Python会出现```…```的提示。函数定义结束后需要按两次回车重新回到>>>提示符下。


### 返回多个值
在其他语言中，一般只能返回一个值或者一个数组、对象，在Python中，可以通过tulp变通的返回多个值。
```python
import math

def move(x, y, step, angle=0):
    nx = x + step * math.cos(angle)
    ny = y - step * math.sin(angle)
    return nx, ny

>>> x, y = move(100, 100, 60, math.pi / 6)
>>> print(x, y)
```
在语法上，返回一个tuple可以省略括号，而多个变量可以同时接收一个tuple，按位置赋给对应的值，所以，Python的函数返回多值其实就是返回一个tuple，但写起来更方便。

### 位置参数
Python的函数定义非常简单，但灵活度却非常大。除了正常定义的必选参数外，还可以使用默认参数、可变参数和关键字参数，使得函数定义出来的接口，不但能处理复杂的参数，还可以简化调用者的代码。
```python
def power(x):        #x 就是一个位置参数
    return x * x

def power(x, n):    #x,n都是位置参数
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s

def power(x, n＝2):    #x,n都是位置参数，n设置了默认值
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s
```
> 有几点要注意：
一是必选参数在前，默认参数在后，否则Python的解释器会报错
二是当函数有多个参数时，把变化大的参数放前面，变化小的参数放后面。变化小的参数就可以作为默认参数。
有多个默认参数时，调用的时候，既可以按顺序提供默认参数。也可以不按顺序提供部分默认参数。当不按顺序提供部分默认参数时，需要把参数名写上。
需要注意的是：**默认参数必须指向不变对象！**


### 可变参数
```python
def calc(*numbers):
    sum = 0
    for n in numbers:
        sum = sum + n * n
    return sum
```
定义可变参数和定义一个list或tuple参数相比，仅仅在参数前面加了一个```*```号。在函数内部，参数numbers接收到的是一个tuple，因此，函数代码完全不变。但是，调用该函数时，可以传入任意个参数，包括0个参数。Python允许你在list或tuple前面加一个*号，把list或tuple的元素变成可变参数传进去。

### 关键字参数
关键字参数允许你传入0个或任意个含参数名的参数，这些关键字参数在函数内部自动组装为一个dict。
```python
def person(name, age, **kw):
    print('name:', name, 'age:', age, 'other:', kw)
```
> 关键字参数有什么用？它可以扩展函数的功能。比如，在person函数里，我们保证能接收到name和age这两个参数，但是，如果调用者愿意提供更多的参数，我们也能收到。试想你正在做一个用户注册的功能，除了用户名和年龄是必填项外，其他都是可选项，利用关键字参数来定义这个函数就能满足注册的需求。

```python
#可以先组装出一个dict，然后，把该dict转换为关键字参数传进去
>>> extra = {'city': 'Beijing', 'job': 'Engineer'}
>>> person('Jack', 24, city=extra['city'], job=extra['job'])
name: Jack age: 24 other: {'city': 'Beijing', 'job': 'Engineer'}

#简化的写法
>>> extra = {'city': 'Beijing', 'job': 'Engineer'}
>>> person('Jack', 24, **extra)
name: Jack age: 24 other: {'city': 'Beijing', 'job': ‘Engineer'}
```
> **extra表示把extra这个dict的所有key-value用关键字参数传入到函数的**kw参数，kw将获得一个dict，注意kw获得的dict是extra的一份拷贝，对kw的改动不会影响到函数外的extra。


### 命名关键字参数
如果要限制关键字参数的名字，就可以用命名关键字参数，例如，只接收city和job作为关键字参数。这种方式定义的函数如下。命名关键字参数需要一个特殊分隔符```*```，```*```后面的参数被视为命名关键字参数。
```python
def person(name, age, *, city, job):
    print(name, age, city, job)

>>> person('Jack', 24, city='Beijing', job='Engineer')
Jack 24 Beijing Engineer
```
> 如果函数定义中已经有了一个可变参数，后面跟着的命名关键字参数就不再需要一个特殊分隔符*了。
```python
def person(name, age, *args, city, job):
  print(name, age, args, city, job)
```


### 参数组合
在Python中定义函数，可以用必选参数、默认参数、可变参数、关键字参数和命名关键字参数，这5种参数都可以组合使用。但是请注意，参数定义的顺序必须是：必选参数、默认参数、可变参数、命名关键字参数和关键字参数。
![](2017-03-22-6599855/39469-20180710163655709-89635310.png)

## 参考资料：
1. [廖雪峰的Python教程](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014316399410395f704750ee9440228135925a6ca1dad8000)
2. [Python安全攻防:渗透测试实战指南](http://www.edulinks.cn)