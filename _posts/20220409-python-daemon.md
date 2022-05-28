---
title: 用 Python 写一个常驻守护进程
date: 2022-04-09 18:09:42
keywords: python
description: 用 Python 写一个常驻的守护进程，能够接收处理 kill 信号实现优雅重启。
---

> 现在有个需求，希望能够实现一个 Python 启动的进程，运行在后台，支持优雅重启。

按照参考资料 1 的介绍，我写了一个简单的验证脚本，发现在终端打开的情况下进程可以运行，当终端关掉后，进程就退出了，也无法捕获输出任何错误。

```python
# -*- coding: utf-8 -*-

import os
import sys
import atexit
import time
import threading

def ps_daemon(pid_file = None):
    # 从父进程 Fork 子进程出来
    pid = os.fork()
    if pid:
        sys.exit(0)

    os.chdir('/')
    os.umask(0)
    os.setsid()

    sys.stdout.flush()
    sys.stderr.flush()

    if pid_file:
        with open(pid_file, 'w+') as f:
            f.write(str(os.getpid()))
        atexit.register(os.remove, pid_file)

    ps_spider()

def ps_spider():
    while True:
        print("Happly Little spider")
        time.sleep(5)

ps_daemon()
```

使用这个例子编写测试后，深入理解自己的需求，包括：

* 进程在后台运行，终端退出后，程序运行不终止
* 可以向运行的程序发送指令，包括终止和重启
* 收到重启指令后，程序会将正在处理中的任务处理完成后再重启，再重启完成前不处理新的任务



## 参考资料

1. [Python 实现守护进程](https://zhuanlan.zhihu.com/p/25118420)
1. [Python 守护进程实现过程详解](https://www.jb51.net/article/179994.htm)
1. [Python中的守护进程daemon实现](https://www.cnblogs.com/pdblogs/p/15594731.html)

