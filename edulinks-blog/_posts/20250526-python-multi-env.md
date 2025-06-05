---
title: MacOS 中 Python 多环境管理问题
date: 2025-05-26 08:47:49
keywords: python环境, python多环境管理, macOs python 环境管理
description: 
---

你的 MacOS 系统上的 Python 环境受系统保护（PEP 668），直接使用 pip 安装包到系统 Python 会破坏依赖管理（如 Homebrew 管理的包）。以下是解决方案：

## 参考资料
1. DeepSeek
