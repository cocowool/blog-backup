---
title: Python3.6学习笔记（二）
date: 2017-03-27 15:27
tag: 
---


## Python 的高级特性

### 切片
对于指定索引范围取值的操作，Python提供了```slice```方法，类似于Excel中数据透视表的切片器。
```python
>>> L = ['Michael', 'Sarah', 'Tracy', 'Bob', 'Jack’]    #声明一个List，取前三个值
>>> L[0:3]    #表示从0开始，到3为止，但是不包括3，后面还可以再接:加上步长
['Michael', 'Sarah', 'Tracy’]
>>> S=list(range(100))    #声明一个0-99的list
>>> S[0:100:9]            #取9的倍数
[0, 9, 18, 27, 36, 45, 54, 63, 72, 81, 90, 99]
```
> tuple也是一种list，唯一区别是tuple不可变。因此，tuple也可以用切片操作，只是操作的结果仍是tuple。


### 迭代 Iteration
给定一个list或tulp，可以通过```for```循环来遍历，这称之为**迭代**。Python中的迭代类似于Javascript，而与PHP或Java采用下标迭代的方式不同。对于Python来说，dict、set、字符串等都是可迭代对象，都可以使用for循环。**只要作用于一个可迭代对象，for循环就可以正常运行**，而我们不太关心该对象究竟是list还是其他数据类型。
判断一个对象是否可迭代，通过collections模块的Iterable类型判断。
```python
>>> from collections import Iterable
>>> isinstance('abc', Iterable) # str是否可迭代
True
>>> isinstance([1,2,3], Iterable) # list是否可迭代
True
>>> isinstance(123, Iterable) # 整数是否可迭代
False
```
> Python内置的enumerate函数可以把一个list变成索引-元素对，这样就可以在for循环中同时迭代索引和元素本身。


### 列表生成式
列表生成式即List Comprehensions，是Python内置的非常简单却强大的可以用来创建list的生成式。写列表生成式时，把要生成的元素放到前面，后面跟for循环，就可以把list创建出来，十分有用，多写几次，很快就可以熟悉这种语法。
```python
tiangan = '甲乙丙丁戊己庚辛壬癸'
dizhi = '子丑寅卯辰巳午未申酉戌亥'

jiazi = [tiangan[x % len(tiangan)] + dizhi[x % len(dizhi)] for x in range(60)]
```
for循环后面还可以加上if判断。
```python
>>> [x * x for x in range(1, 11) if x % 2 == 0]
[4, 16, 36, 64, 100]
```
还可以使用两层循环，可以生成全排列。
```python
>>> [m + n for m in 'ABC' for n in 'XYZ']
['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', ‘CZ']
```

### 生成器
通过列表生成式，我们可以直接创建一个列表。但是，受到内存限制，列表容量肯定是有限的。而且，创建一个包含100万个元素的列表，不仅占用很大的存储空间，如果我们仅仅需要访问前面几个元素，那后面绝大多数元素占用的空间都白白浪费了。
所以，如果列表元素可以按照某种算法推算出来，那我们是否可以在循环的过程中不断推算出后续的元素呢？这样就不必创建完整的list，从而节省大量的空间。在Python中，这种一边循环一边计算的机制，称为生成器：generator。
生成generator有两个办法。
1、只要把一个列表生成式的[]改成()，就创建了一个generator。
```python
>>> L = [x * x for x in range(10)]
>>> L
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
>>> g = (x * x for x in range(10))
>>> g
<generator object <genexpr> at 0x1022ef630>
```
generator保存的是算法，每次调用```next(g)```，就计算出g的下一个元素的值，直到计算到最后一个元素，没有更多的元素时，抛出```StopIteration```的错误。通常，可以使用```for```循环来遍历生成器中的内容。
2、如果算法比较复杂，可以使用函数来实现。
```python
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return 'done'
```
如果一个函数定义中包含yield关键字，那么这个函数就不再是一个普通函数，而是一个generator。函数是顺序执行，遇到return语句或者最后一行函数语句就返回。而变成generator的函数，在每次调用next()的时候执行，遇到yield语句返回，再次执行时从上次返回的yield语句处继续执行。

### 迭代器
这里主要区分```Iterable```和```Iterator```。
对于```Iterable```的数据类型，称之为可迭代对象，可以使用for循环遍历，包括```list```、```tuple```、```dict```、```set```、```str```、生成器以及带```yield```的Generator Function。可以使用```isinstance()```函数判断是否为 Iterable 。
迭代器不仅可以使用```for```循环，还可以使用```next()```函数不断调用返回下一个值，直到最后抛出**StopIteration**错误表示无法继续返回下一个值了。这样，就可以把数据流看作一个有序序列，我们不知道序列的长度，但是可以通过不断的计算获取下一个值。
> 可以使用```iter()```函数把```list```、```dict```、```str```等```Iterable```变成```Iterator```。


## 函数式编程
我之前熟悉的PHP、C，编写程序多是通过将任务分解为一个个Function，然后组合起来解决问题的，这种分解称为面向过程的程序设计，而函数是面向过程的程序设计的基本单元。
函数式编程（Functional Programming）其思想更接近数学计算。函数式编程就是一种抽象程度很高的编程范式，纯粹的函数式编程语言编写的函数没有变量，因此，任意一个函数，只要输入是确定的，输出就是确定的，这种纯函数我们称之为没有副作用。而允许使用变量的程序设计语言，由于函数内部的变量状态不确定，同样的输入，可能得到不同的输出，因此，这种函数是有副作用的。
Python对函数式编程提供部分支持。

### 高阶函数 High order function

#### 变量可以指向函数
这个对我真是有点颠覆，看下面的例子。
```python
>> abs(-10)
10
>>> abs
<built-in function abs>
>>> f = abs
>>> f(-10)
10
```

### 函数名也是变量
Python中函数名其实就是指向函数的变量。
```python
>>> abs=10
>>> abs
10
>>> abs(-10)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'int' object is not callable
>>> f(-10)
10
```

### 传入函数
函数的参数如果接收一个指向函数的变量，就变成了一个函数接收另一个函数作为参数，这种函数称为高阶函数。函数式编程就是指这种高度抽象的编程范式

### map / reduce
Python内建了```map()```和```reduce()```函数。```map()```函数接收两个参数，一个是**函数**，一个是**Iterable**，map将传入的函数依次作用到序列的每个元素，并把结果作为新的Iterator返回。
```python
>>> def f(x):
...     return x * x
...
>>> r = map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
>>> list(r)
[1, 4, 9, 16, 25, 36, 49, 64, 81]
```
```reduce```把一个函数作用在一个序列[x1, x2, x3, ...]上，这个函数必须接收两个参数，reduce把结果继续和序列的下一个元素做累积计算，其效果就是```reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)```。

### filter
Python内建的函数```filter()```用于过滤序列，其接收一个函数和一个序列，并把传入的函数作用于每个元素，然后根据返回值是True还是False决定是保留还是丢弃该元素。
```python
def is_odd(n):
    return n % 2 == 1

list(filter(is_odd, [1, 2, 4, 5, 6, 9, 10, 15]))
```
> 注意到filter()函数返回的是一个```Iterator```，也就是一个惰性序列，所以要强迫filter()完成计算结果，需要用list()函数获得所有结果并返回list。


### sorted 排序算法
排序是程序中经常用到的算法。无论使用冒泡排序还是快速排序，排序的核心是比较两个元素的大小。Python内置的```sorted()```函数就可以对list进行排序。```sorted()```函数也是一个高阶函数，它还可以接收一个key函数来实现自定义的排序，例如按绝对值大小排序。key指定的函数将作用于list的每一个元素上，并根据key函数返回的结果进行排序。要进行反向排序，不必改动key函数，可以传入第三个参数```reverse=True```。
```python
>>> sorted([36, 5, -12, 9, -21])
[-21, -12, 5, 9, 36]
>>> sorted([36, 5, -12, 9, -21], key=abs)
[5, 9, -12, -21, 36]
```

### 函数作为返回值
高阶函数除了可以接受函数作为参数外，还可以把函数作为结果值返回。如下示例，调用```lazy_sum```时，返回的并不是求和结果，而是求和函数。这种结构称为**闭包 Closure**。
```python
def lazy_sum(*args):
    def sum():
        ax = 0
        for n in args:
            ax = ax + n
        return ax
    return sum
```
> 返回闭包时牢记的一点就是：返回函数不要引用任何循环变量，或者后续会发生变化的变量。


### 匿名函数
在Python中，对匿名函数提供了有限支持。还是以map()函数为例，计算f(x)=x2时，除了定义一个f(x)的函数外，还可以直接传入匿名函数。关键字```lambda```表示匿名函数，冒号前面的x表示函数参数。匿名函数有个限制，就是只能有一个表达式，不用写return，返回值就是该表达式的结果。
```python
>>> list(map(lambda x: x * x, [1, 2, 3, 4, 5, 6, 7, 8, 9]))
[1, 4, 9, 16, 25, 36, 49, 64, 81]
```
> 用匿名函数有个好处，因为函数没有名字，不必担心函数名冲突。此外，匿名函数也是一个函数对象，也可以把匿名函数赋值给一个变量，再利用变量来调用该函数。


### 装饰器
函数对象有一个__name__属性，可以拿到函数的名字。现在，假设我们要增强now()函数的功能，比如，在函数调用前后自动打印日志，但又不希望修改now()函数的定义，这种在代码运行期间动态增加功能的方式，称之为**“装饰器”（Decorator）**。
```python
def log(func):
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return wrapper
```
借助Python的@语法，把decorator置于函数的定义处。
```python
@log
def now():
    print('2015-3-25')

>>> now()
call now():    #调用now()函数，不仅会运行now()函数本身，还会在运行now()函数前打印一行日志
2015-3-25
```
> 把@log放到now()函数的定义处，相当于执行了语句:```now = log(now)```

对于上面这种写法，如果调用```__name__```方法，返回的函数名为 wrapper ，需要用Python内置的 functools.wraps 来解决。
```python
import functools

def log(func):
    @functools.wraps(func)
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return wrapper
```
```python
import functools

def log(text):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator
```

### 偏函数
Python 的 functools 提供了很多有用的功能，其中一个就是**偏函数（Partial function）**。
```python
def int2(x, base=2):
    return int(x, base)

>>> int2('1000000')
64
>>> int2('1010101')
85
```
如上例子，通过使用偏函数，我们相当于对函数做了一个包装，默认输入了一些函数参数，减少了后续调用时输入的参数个数。实现这个功能，可以借助 functools。
```python
>>> import functools
>>> int2 = functools.partial(int, base=2)
>>> int2('1000000')
64
>>> int2('1010101')
85
>>> int2('1000000', base=10)
1000000
```

## 模块
任何语言要实现一个项目，都离不开文件组织管理。在Python中，一个.py文件就称之为一个模块（Module）。使用模块可以提高代码的可维护性，也可以避免函数名和变量名冲突。但是也要注意，尽量不要与内置函数名字冲突。为了避免模块名冲突，Python又引入了按目录来组织模块的方法，称为包（Package）。
引入了包以后，只要顶层的包名不与别人冲突，那所有模块都不会与别人冲突。每一个包目录下面都会有一个__init__.py的文件，这个文件是必须存在的，否则，Python就把这个目录当成普通目录，而不是一个包。**init**.py可以是空文件，也可以有Python代码，因为__init__.py本身就是一个模块。类似的，可以有多级目录，组成多级层次的包结构。
> 自己创建模块时要注意命名，不能和Python自带的模块名称冲突。例如，系统自带了sys模块，自己的模块就不可命名为sys.py，否则将无法导入系统自带的sys模块。


### 使用模块
看一段代码，引用了```sys```模块，定义了hello模块。
```python
#!/usr/bin/env python3    #标准注释
# -*- coding: utf-8 -*-   #表示.py文件本身使用标准UTF-8编码

' a test module '

__author__ = 'Michael Liao'

import sys

def test():
    args = sys.argv
    if len(args)==1:
        print('Hello, world!')
    elif len(args)==2:
        print('Hello, %s!' % args[1])
    else:
        print('Too many arguments!')

if __name__=='__main__':
    test()
```
导入sys模块后，我们就有了变量sys指向该模块，利用sys这个变量，就可以访问sys模块的所有功能。

### 作用域
正常的函数和变量名是公开的（public），可以被直接引用，比如：abc，x123，PI等。类似```__xxx__```这样的变量是特殊变量，可以被直接引用，但是有特殊用途。类似```_xxx```和```__xxx```这样的函数或变量就是非公开的（private），不应该被直接引用。之所以我们说，private函数和变量**“不应该”**被直接引用，而不是**“不能”**被直接引用，是因为Python并没有一种方法可以完全限制访问private函数或变量，但是，从编程习惯上不应该引用private函数或变量。

### 安装第三方模块
在Python中，安装第三方模块，是通过包管理工具pip完成的。在命令提示符窗口下尝试运行pip，如果Windows提示未找到命令，可以重新运行安装程序添加pip。
> 注意：Mac或Linux上有可能并存Python 3.x和Python 2.x，因此对应的pip命令是```pip3```。

一般来说，第三方库都会在Python官方的pypi.python.org网站注册，要安装一个第三方库，必须先知道该库的名称，可以在官网或者pypi上搜索，比如Pillow的名称叫Pillow，因此，安装Pillow的命令就是
```python
pip install Pillow
```
![](./2017-03-27-6626958/39469-20180710163655709-89635310.png)












