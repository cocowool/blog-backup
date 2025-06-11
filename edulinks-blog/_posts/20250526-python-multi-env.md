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

你的 MacOS 系统上的 Python 环境受系统保护（PEP 668），直接使用 pip 安装包到系统 Python 会破坏依赖管理（如 Homebrew 管理的包）。以下是解决方案：

## 参考资料
1. DeepSeek
2. https://peps.python.org/pep-0668/
3. https://wolduinouno.cn/externally-managed-environment/
