---
title: macOS 下的 python 环境管理
date: 2024-08-30 18:02:09
keywords: macOS, python, python3, python3 环境
description:
---

电脑型号是 Mackbook Pro 2020年款，操作系统是 macOS Monterey。

昨天为了解决终端下 wget 不能使用的问题，使用 `brew` 更新安装了 `openssl` 。

安装的过程中更新了很多语言环境，包括 node、python，检查发现 Pytnon 的环境升级到了 Pyhon 3.12.5 和 Python 2.7.18。

在执行一些历史的代码时，报错 pandas 无法找到，感觉是升级基础环境把相关的依赖包都删掉了，需要重新安装。

尝试重新安装
```sh
$ pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple pandas
error: externally-managed-environment

× This environment is externally managed
╰─> To install Python packages system-wide, try brew install
    xyz, where xyz is the package you are trying to
    install.
    
    If you wish to install a Python library that isn't in Homebrew,
    use a virtual environment:
    
    python3 -m venv path/to/venv
    source path/to/venv/bin/activate
    python3 -m pip install xyz
    
    If you wish to install a Python application that isn't in Homebrew,
    it may be easiest to use 'pipx install xyz', which will manage a
    virtual environment for you. You can install pipx with
    
    brew install pipx
    
    You may restore the old behavior of pip by passing
    the '--break-system-packages' flag to pip, or by adding
    'break-system-packages = true' to your pip.conf file. The latter
    will permanently disable this error.
    
    If you disable this error, we STRONGLY recommend that you additionally
    pass the '--user' flag to pip, or set 'user = true' in your pip.conf
    file. Failure to do this can result in a broken Homebrew installation.
    
    Read more about this behavior here: <https://peps.python.org/pep-0668/>

note: If you believe this is a mistake, please contact your Python installation or OS distribution provider. You can override this, at the risk of breaking your Python installation or OS, by passing --break-system-packages.
hint: See PEP 668 for the detailed specification.
```

报错

## 参考资料
