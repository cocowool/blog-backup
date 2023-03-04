---
title: dirsearch 介绍
date: 2023-01-26 14:35:33
keywords: sqlmap, ctf tools, ctf 工具, ctf 工具合集, ctf 工具介绍
description: dirsearch 能够快速扫描网站中可能存在的攻击点的常见访问路径，减少手工探测的繁琐。
---

在 CTF 比赛中，遇到服务器的场景需要先探索下有没有什么常见的问题点，有经验的可能会尝试 `.git / admin / robots.txt` 等等，但是手工尝试的效率很低，dirsearch 是一个快速批量检查的 Python 程序，能够快速帮我们找到网站的突破口。

## 安装

> 本文介绍的是在 macOS 12.2.1 环境下的使用方法，需要具备 Python3 环境。

在 Terminal 终端窗口中，cd 到希望保存这个工具的目录，执行下面的命令将代码下载到本地。

```sh
$ git clone https://github.com/maurosoria/dirsearch.git
# 安装 dirsearch 运行需要的其他依赖
$ cd dirsearch
# 这里使用了清华大学的镜像源来提高安装速度
$ pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple -r requirements.txt
# 运行一下看能否正常执行
$ python3 dirsearch.py --version
dirsearch v0.4.3
```

## 使用

```sh
# 简单使用
$ python3 dirsearch.py -u https://target
```



## 参考资料

1. [目录扫描工具 dirsearch 用法](https://www.jianshu.com/p/5d99bafb1547)
