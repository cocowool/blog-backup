---
title: Pandas快速入门（一）
date: 2018-01-19 14:02
keywords: python, pandas, pandas数据分析
description: Pandas数据分析快速入门。
---


### 快速使用
```bash
bogon:Documents rousseau$ ipython --pylab
Python 3.6.0 (v3.6.0:41df79263a11, Dec 22 2016, 17:23:13) 
Type 'copyright', 'credits' or 'license' for more information
IPython 6.2.1 -- An enhanced Interactive Python. Type '?' for help.
Using matplotlib backend: MacOSX
In [1]: import pandas as pd
In [2]: import numpy as np
In [3]: import matplotlib as plt
In [4]: from pandas import Series, DataFrame
```
按照Shell中的语句，就可以进入到Ipython的环境中使用Pandas分析数据，并绘制图表。ipython 环境的具体安装配置在Mac很简单，通过**pip**安装一下就可以，其他操作系统的安装可以自己百度一下。如果没有 ipython 也不要紧，标准的 python 命令行环境下也可以使用。
> 如果你使用 ipython，通过TAB补齐和help功能能给你的工作带来很大的便利。


### 主要数据结构 Series 和 DataFrame 及创建
Pandas里两个主要的数据结构就是 Series 和 DataFrame。Series 类似于Python的字典，DataFrame可以理解为二维表。

#### Series
可以通过传入一个List来创建一个Series对象。
```bash
In [6]: s = pd.Series([1,2,4,5,6,np.nan,7,8])

In [7]: s
Out[7]: 
0    1.0
1    2.0
2    4.0
3    5.0
4    6.0
5    NaN
6    7.0
7    8.0
dtype: float64
```

#### DataFrame
DataFrame 的创建有很多种方式，可以使用 numpy array 或者 Python的字典（包括嵌套字典）。
```python
In [103]: dt = DataFrame({"name":['wang','lv','bao','song'],"sex":['male','female','male','female'],"age":[12,23,35,47]})

In [104]: dt
Out[104]: 
   age  name     sex
0   12  wang    male
1   23    lv  female
2   35   bao    male
3   47  song  female
```

### 查看、选择数据
Series、DataFrame 查看数据的方式可以非常灵活，利用索引、切片，以及一些内置函数。

#### 查看索引，列
接上面的例子
```python
In [118]: dt.index
Out[118]: RangeIndex(start=0, stop=4, step=1)

In [119]: dt.columns
Out[119]: Index(['age', 'name', 'sex'], dtype='object')
```
通过```dt.values```还可以查看底层的数据。

#### 选择查看部分数据
Series和DataFrame可以快速的从整个结果集中选择你需要的数据，包括选择一列或几列、一行或几行，以及通过对值进行筛选选择对应结果集。推荐使用```.at、.iat、.loc、.iloc、.ix```进行选择。
1、获取某一列
```python
In [140]: dt['name']
Out[140]: 
序号
0    wang
1      lv
2     bao
3    song
Name: name, dtype: object
```
2、获取行，对行进行切片
```python
In [143]: dt[1:3]
Out[143]: 
    age name     sex
序号                  
1    23   lv  female
2    35  bao    male
```
3、通过loc选择一行或多行
```python
In [162]: dt.loc[1]
Out[162]: 
age         23
name        lv
sex     female
Name: 1, dtype: object

In [163]: dt.loc[1:3]
Out[163]: 
    age  name     sex
序号                   
1    23    lv  female
2    35   bao    male
3    47  song  female
```
4、同时选择行和列
```python
In [166]: dt.loc[2:3,['name','age']]
Out[166]: 
    name  age
序号           
2    bao   35
3   song   47
```
5、访问某个位置的值
```python
In [168]: dt.at[1,'name']
Out[168]: 'lv'
```
以```i```开头的函数，表示通过具体的位置选择数据，而不是通过标签(即行名或列名)。
6、通过一个列中的值来选择数据
```python
In [170]: dt[ dt.age > 30 ]
Out[170]: 
    age  name     sex
序号                   
2    35   bao    male
3    47  song  female
```
7、通过where来筛选数据
```python
In [183]: dt[dt > 30]
Out[183]: 
     age  name     sex
序号                    
0    NaN  wang    male
1    NaN    lv  female
2   35.0   bao    male
3   47.0  song  female
```

### 数据处理：转置、排序
对于一个DataFrame，可以进行行列的转置，就像Excel中粘贴时交换x/y轴一样。
```python
In [176]: df = DataFrame({"beijing":{"people":3000,"area":5000,"university":200},"guangzhou":{"people":2000,"area":3000,"university":150},"shanghai":{"peo
     ...: ple":2800,"area":2500,"university":190}})

In [177]: df
Out[177]: 
            beijing  guangzhou  shanghai
area           5000       3000      2500
people         3000       2000      2800
university      200        150       190

In [178]: df.T
Out[178]: 
           area  people  university
beijing    5000    3000         200
guangzhou  3000    2000         150
shanghai   2500    2800         190
```
从这个例子可以看出来，构建 DataFrame 对象时，可以采用层次化的 dict 。
排序操作，可以按照列或者行进行排序，非常灵活。
```python
In [194]: df.sort_values(by='beijing')
Out[194]: 
            beijing  guangzhou  shanghai
university      200        150       190
people         3000       2000      2800
area           5000       3000      2500

In [195]: df.sort_values(by='beijing',ascending=False)
Out[195]: 
            beijing  guangzhou  shanghai
area           5000       3000      2500
people         3000       2000      2800
university      200        150       190

In [196]: df.sort_values(axis=1,by='people',ascending=False)
Out[196]: 
            beijing  shanghai  guangzhou
area           5000      2500       3000
people         3000      2800       2000
university      200       190        150
```

### 统计操作
```describe```可以对数据集进行快速的统计分析。
```python
In [198]: df.T.describe()
Out[198]: 
              area       people  university
count     3.000000     3.000000    3.000000
mean   3500.000000  2600.000000  180.000000
std    1322.875656   529.150262   26.457513
min    2500.000000  2000.000000  150.000000
25%    2750.000000  2400.000000  170.000000
50%    3000.000000  2800.000000  190.000000
75%    4000.000000  2900.000000  195.000000
max    5000.000000  3000.000000  200.000000
```
参考资料：
1、[pandas](http://pandas.pydata.org)
2、[十分钟搞定Pandas](https://www.cnblogs.com/chaosimple/p/4153083.html)
3、[利用Python进行数据分析](利用Python进行数据分析)











