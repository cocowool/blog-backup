---
title: 30分钟Sehll入门
date: 2020-07-22 19:42:57
tags: shell
---

## Shell 简介

shell是一种特殊的程序，是 `用户与Linux系统内核间的接口`，它解释运行由命令行或文件输入的命令，从而实现用户与内核间的交互。

## 内部命令

内部命令是shell命令的一部分，驻留在系统内存中，不需要创建子进程，例如 `cd exit echo history` 这些命令。

> 以 `/` 开头的命令都不会触发内部命令，例如 `echo` 会调用内部命令，`/bin/echo` 或 `./echo` 则不会调用内部命令。