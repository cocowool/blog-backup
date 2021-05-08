---
title: IPython介绍
date: 2017-12-26 17:27
tag: 
keywords: python, ipython
description: IPython 是 Fernando 在 2001 开始开发的一个交互式的Python解释执行环境。
---

> 本文编写时，IPython最新的版本为6.3和5.4。

### 介绍
IPython 是 Fernando 在 2001 开始开发的一个交互式的Python解释执行环境。众所周知，Python提供了一个交互执行的环境，在命令行输入```python```或者```python3```就可以进入Python的命令行环境，但在实际工作中并不方便，IPython则提供了一个更为强大的环境，主要包括以下几方面内容：

* 一个强大的交互式Shell环境
* [Jupyter](https://jupyter.org)内核
* 支持交互式的数据可视化
* 灵活，可被嵌入到单独的工程中
* 使用简单，并行计算的高效工具
本文依次介绍这几个方面，方便大家熟悉IPython环境。

### Mac 下的安装
安装非常简单，我的Python版本时3.6，所以用了 pip3，如果需要jupyter notebook或者Qt支持，也需要同步安装jupyter。Python数据分析这本书中推荐使用[EDM](https://www.enthought.com/product/enthought-python-distribution)这是一个集成环境，可能对软件依赖管理的比较好，其实自己安装也没什么问题。
```python
pip3 install ipython
pip3 install jupyter

# 如果需要交互式的可视化终端，还需要安装pytq5
pip3 install pyqt5
```

### 交互式Shell环境
命令行输入IPython就可以进入ipython的环境，输入前面包含In和行号，输出包含Out和行号。
![](/20171226-ipython-introduce/39469-20171226172630698-924361539.png)

#### 与操作系统交互
IPython和操作系统结合的非常紧密，可以直接在命令行中执行操作系统的命令。

#### Tab补齐与提示
在IPython的界面中，按Tab键可以做很多工作，如果执行Shell命令，tab可以补齐文件名，如果是Python代码，可以补齐变量名，用```.```之后，可以提示对象包含的属性和方法。
![](/20171226-ipython-introduce/39469-20171227105214354-667896144.png)

#### 记录命令历史
IPython在本地磁盘维护了一个小型的数据库来记录用户输入的历史以及输出的日志，并且提供了几个快捷键，方便大家快速查找历史命令。还可以用```%hist```来查看输入的历史记录。


#### 输入和输出变量
IPython环境对用户的输入和输出都会记录。输入变量保存在```_iX```格式的变量中，X表示行号。如果我们想查看第2行的输入内容，可以使用```_i2```。

#### 目录书签系统
IPython提供了一个简单的目录书签系统，可以方便我们快速切换目录。书签会被自动初始化，这样可以在不同会话间保留。
```python
In [1]: %bookmark -l
Current bookmarks:
baby -> /Users/rousseau/Projects/python.my/pydata-book/datasets/babynames

In [2]: %bookmark moviedb /Users/rousseau/Projects/python.my/pydata-book/datasets/movielens

In [3]: %bookmark -l
Current bookmarks:
baby    -> /Users/rousseau/Projects/python.my/pydata-book/datasets/babynames
moviedb -> /Users/rousseau/Projects/python.my/pydata-book/datasets/movielens
```

#### 对象内省 Comprehensive object introspection
使用 ? 可以查看对象的一些信息。
使用 ?? 可以查看对象的源码（如果可能）

### 魔术命令
IPython中提供了一些以```%```开头的特殊命令，我们称这些命令为魔术命令 Magic Command。魔术命令可以认为是IPython系统中的命令行程序，使用```?```可以查看选项。魔术命令也可以不带百分号直接使用，只要没有定义与其同名的变量就可以，这个技术叫做 automagic ，可以通过```%automagic```打开或关闭。可以在IPython中查看相关文档，输入```%magic```即可。

#### 常用的魔术命令


#### %run 命令
在IPython环境中，所有文件都可以通过%run命令当作Python程序来运行。我理解就是把Python脚本放到一个新的命名空间中进行后台运行，通过IPython交互环境，可以调用现实Python程序中的变量。

#### 执行剪贴板中的代码
对于一线代码片段，可以拷贝到IPython的环境中执行查看结果，但是对于有空行的代码，直接使用操作系统的复制粘贴快捷键会报错，这个时候需要使用```%paste```和```%cpaste```这两个魔术函数。```%paste```函数会将粘贴板中的代码粘贴到交互式命令行中。```%cpaste```函数执行后，进入一个交互环境，只有输入```__```或者Ctrl+D才结束代码的复制。

### 交互式数据可视化
IPython支持给予Qt框架的GUI控制台，如下图所示。如果需要交互环境，需要安装PyQt5。
![](/20171226-ipython-introduce/39469-20171226172644354-1818808889.png)
IPython可以与matplotlib密切结合。
![](/20171226-ipython-introduce/39469-20171227105155104-1875327084.png)

### 开发调试利器

%debug
%pdb

### 参考资料：
1、[IPython](http://ipython.org)
2、[Jupyter](https://jupyter.org)
3、[matplotlib](http://matplotlib.org)
