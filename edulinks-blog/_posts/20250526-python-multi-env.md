---
title: MacOS 中 Python 多环境管理问题
date: 2025-05-26 08:47:49
keywords: python环境, python多环境管理, macOs python 环境管理
description: 本文整理总结在 Python 3.11 版本之后，如何应对无法使用 pip3 命令安装 MacOS 系统范围的 Python 包方案。
---

自 **Python 3.11** 版本开始，MacOS 系统强制实施 [PEP 668](https://peps.python.org/pep-0668/) 保护机制，禁止直接通过 `pip`、`pip3` 命令安装包到系统级 Python 环境。这是为了避免 Homebrew 等系统包管理器和 `pip` 发生冲突。

当尝试运行 `pip3 install` 时，会遇到如下报错：
```bash
error: externally-managed-environment

× This environment is externally managed
╰─> To install Python packages system-wide, try brew install
    xyz, where xyz is the package you are trying to
    install.
```

## 使用虚拟环境（推荐）

虚拟环境「Virtual Environment」是 Python 开发的标准实践，可为每个项目创建隔离的依赖环境。

1. 创建虚拟环境，在项目所在目录中执行
```bash
$ python3 -m venv myenv	#创建一个命名为 myenv 的虚拟环境
```

2. 激活虚拟环境
```bash
$ source myenv/bin/activate
(myenv) ➜   
```

激活后，终端提示符会显示环境名称。

3. 安装依赖包
```bash
$ pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple pytest
```

4. 退出虚拟环境
```bash
$ dactivate
```

这个方案的优点：
* 安全隔离项目依赖
* 无需管理员权限
* 不会污染系统环境

缺点当然也很明显，每个工程都要对依赖的包重新安装一遍，是不是要考虑工程运行环境的初始化脚本？

## 使用 pipx（适合全局工具）

`pipx` 专门用于安装可执行的 Python 工具（如 black, pytest 等），自动管理独立虚拟环境。

1. 安装 pipx
```bash
$ brew install pipx
$ pipx ensurepath
```

2. 通过 pipx 安装Python工具
```bash
$ pipx install pytest
```

3. 查看已安装工具
```bash
$ pipx list
```

这个方案适用于安装需要全局使用的命令行工具，可以避免与项目本地依赖冲突。

## 用户级安装

这个方案将包安装到当前用户目录，避免写入系统路径。
```bash
$ pip3 install --user -i https://pypi.tuna.tsinghua.edu.cn/simple pytest
```

这种方案的包会安装在 `~/Library/Python/3.x/lib/python/site-packages` 下，并且可能存在项目间的依赖冲突。

## 强制绕过保护（不推荐）

通过 `--break-system-packages` 强制安装到系统环境，不推荐使用。

```bash
$ pip3 install --break-system-packages -i https://pypi.tuna.tsinghua.edu.cn/simple pytest
```


## 参考资料
1. DeepSeek
2. https://peps.python.org/pep-0668/
3. https://wolduinouno.cn/externally-managed-environment/
