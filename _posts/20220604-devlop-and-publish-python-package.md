---
title: 开发并发布 Python 包/模块
date: 2022-06-04 19:26:41
keywords: python, python package, python 包, python 包开发
description:
---



## 模块与包

在 Python 中一个文件就是一个 模块 Module，是 Python 中一个单独的命名空间，这个命名空间包括了下面的内容：类、函数、常量、变量等。

当模块越来越多时，我们需要包 Package 来帮我们组织管理，同时包也是 Pypi 上共享的最小单位。包通常对应一个文件夹，包括了一个或者多个模块。在包代码文件夹下一定要有 `__init__.py` 文件，另外可能还有一些 python 文件或者其他包的文件夹。相较于模块来说，包中的内容更多：文档、测试案例、安装方法以及必要的数据文件等等都可以放在包中。

## 本地开发测试

几种方式

* 使用 virtualenv
* 使用集成了测试环境的 IDE，例如 pycharm
* 使用 `sys.path` 

## 发布

发布到 pypi 需要首先有一个 pypi 的账号。

## 参考资料

1. [How to Build your very first python package](https://www.freecodecamp.org/news/build-your-first-python-package/)
2. [Pypi](https://pypi.org/)
3. [Making a Python Package](https://python-packaging-tutorial.readthedocs.io/en/latest/setup_py.html)
3. [How to Create a Python Package](https://www.pythoncentral.io/how-to-create-a-python-package/)
3. [Packaging and distributing projects](https://packaging.python.org/en/latest/guides/distributing-packages-using-setuptools/#working-in-development-mode)
