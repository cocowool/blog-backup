---
title: 开发并发布 Python 包/模块
date: 2022-06-04 19:26:41
keywords: python, python package, python 包, python 包开发
description: 本文总结了 Python 包发布到 pypi 的过程。
---

> 本文在 macOS Monterey 及 Python 3.9.6 环境下测试通过
>
> 本文是面向百度编写的，如果你希望了解这些内容欢迎继续浏览，如果你希望避免踩坑快速通关，可以阅读官方文档 [Packaging Python Projects](https://packaging.python.org/en/latest/tutorials/packaging-projects/)

## 模块与包

在 Python 中一个文件就是一个 模块 Module，是 Python 中一个单独的命名空间，这个命名空间包括了下面的内容：类、函数、常量、变量等。当然，Python 中还支持使用 C 语言编写模块并实现运行时加载，本文不涉及这方面的内容。

> 模块文件的代码中通常不能有任何输出，这些输出在执行 import 的时候就会被执行
> 如果希望以单独脚本形式执行时还是能够输出信息，可以添加 `if __name__ = '__main__'` 判断
> 这是因为 import 的时候，python 会给模块增加 `__name__` 属性，单独执行时 `__name__` 会等于 `__main__`

当模块越来越多时，我们需要包 Package 来帮我们组织管理，同时包也是 Pypi 上共享的最小单位。包通常对应一个文件夹，包括了一个或者多个模块。在包代码文件夹下一定要有 `__init__.py` 文件，另外可能还有一些 python 文件或者其他包的文件夹。相较于模块来说，包中的内容更多：文档、测试案例、安装方法以及必要的数据文件等等都可以放在包中。

本文以  [mp3tag](https://github.com/cocowool/mp3tag) 为示例代码来说明一个包的开发与发布过程，看一下它的目录结构：

```sh
$ tree .            
.
├── LICENSE
├── README.md
├── examples
│   └── tag_example.py
├── mp3tag
│   ├── __init__.py
│   ├── mp3tag.py
│   ├── tag_reader.py
│   └── tag_writer.py
└── setup.py

3 directories, 10 files
```

## 本地开发测试

几种方式

* 使用 virtualenv
* 使用集成了测试环境的 IDE，例如 pycharm
* 使用 `sys.path` 

python 解析器在执行的时候，会从 `sys.path` 变量保存的路径中按顺序搜索引用的包，因此本地测试时，可以通过向这个变量追加路径，来实现本地测试。模块加载后，我们可以通过 ``__file__`` 属性获取模块所在的路径。

```python
>>> import adtk
>>> print(adtk.__file__)
/usr/local/lib/python3.9/site-packages/adtk/__init__.py
```

在这个环节，我们除了编写我们的业务逻辑之外，还需要编写 `setup.py` 文件，还有一种编写 `setup.cfg` 的方法，本文没有采用。

```python
from setuptools import setup, find_packages

VERSION = '0.0.1'
DESCRIPTION = 'MP3 tag information reader and writer.'
LONG_DESCRIPTION = 'MP3 contains many information about the music and stored in the tag. This package support read and write id3v2 tag to the mp3 file.'

setup(
    name='mp3tag',
    version=VERSION,
    author='Wang Shiqiang',
    author_email='cocowool@qq.com',
    description=DESCRIPTION,
    long_description=LONG_DESCRIPTION,
    packages=find_packages(),
    install_requires=[],

    keywords=['mp3','id3','id3v2'],
    classifiers=[]
)
```

## 创建发布文件

在项目的根文件夹下，执行下面的命令，需要提前安装 build 模块。

```sh
$ python3 -m build
* Creating venv isolated environment...
* Installing packages in isolated environment... (setuptools>=42)
```

整个过程比较慢，执行完成后，可以看到 dist 文件夹下多了两个文件。

```sh
$ ll dist                              
total 64
drwxr-xr-x   4 shiqiang  staff   128B Jun  5 15:18 .
drwxr-xr-x  12 shiqiang  staff   384B Jun  5 15:17 ..
-rw-r--r--   1 shiqiang  staff    16K Jun  5 15:18 mp3tag-0.0.1-py3-none-any.whl
-rw-r--r--   1 shiqiang  staff    15K Jun  5 15:17 mp3tag-0.0.1.tar.gz
```

其中 `tar.gz` 文件是安装包，`whl` 结尾的文件是分发说明文件。

## 发布到 pypi

[pypi](https://pypi.org/) 是一个公共的 python 包共享分发平台，全称「Python Package Index」，类似 npmjs。将开发的包发布到 pypi 之后，我们可以方便的通过 `pip3 install` 安装使用我们开发的包，这对于开源社区的发展繁荣是非常好的机制。当然，如果是你一名企业内部的开发者，对于任何引入到工程中的包还是需要注意进行安全检查，避免引入不必要的麻烦。

> 发布到 pypi 需要首先有一个 pypi 的账号，还需要在 pypi 网站上生成 API Key。
>
> 如果使用 twine 上传，需要按照网站的提示准备 .pypirc 文件。
>
> 如果离上一个版本过去的时间比较长，可能存在上传失败的问题，官方提供了一个 [TroubleShooting](https://pypi.org/help/#invalid-auth) 的页面。

```sh
$ python3 -m twine upload dist/*
Uploading distributions to https://upload.pypi.org/legacy/
Enter your username: cocowool
Enter your password: 
Uploading mp3tag-0.0.1-py3-none-any.whl
100% ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 19.8/19.8 kB • 00:01 • 19.7 MB/s
Uploading mp3tag-0.0.1.tar.gz
100% ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 19.5/19.5 kB • 00:00 • 30.9 MB/s

View at:
https://pypi.org/project/mp3tag/0.0.1/
```

## 安装测试

```sh
$ pip3 install mp3tag                                            
Collecting mp3tag
  Downloading mp3tag-0.0.1-py3-none-any.whl (15 kB)
Installing collected packages: mp3tag
Successfully installed mp3tag-0.0.1
```

至此，一个自己开发的 Python 包就发布成功了。

## 参考资料

1. [How to Build your very first python package](https://www.freecodecamp.org/news/build-your-first-python-package/)
2. [Pypi](https://pypi.org/)
3. [Making a Python Package](https://python-packaging-tutorial.readthedocs.io/en/latest/setup_py.html)
4. [How to Create a Python Package](https://www.pythoncentral.io/how-to-create-a-python-package/)
5. [Packaging and distributing projects](https://packaging.python.org/en/latest/guides/distributing-packages-using-setuptools/#working-in-development-mode)
6. [python tutorial : class](https://docs.python.org/3/tutorial/classes.html)
7. [Python Modules and Packages – An Introduction](https://realpython.com/python-modules-packages/)
8. [Packaging Python Projects](https://packaging.python.org/en/latest/tutorials/packaging-projects/)
9. [解决TypeError: 'module' object is not callable问题](https://www.jianshu.com/p/b70068223a4e)
10. [__pycache__ 是个什么鬼？](https://blog.csdn.net/yangchao1125/article/details/106347462/)
