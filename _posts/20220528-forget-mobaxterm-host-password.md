---
title: MobaXterm 主机密码忘记后的找回
date: 2022-05-28 14:58:17
keywords: MobaXterm, 找回密码, MobaXterm 密码
description: MobaXterm 是一款免费的终端工具，忘记密码的朋友可以通过本文找回主机密码。
---

今天记录两个心得收获：

* 找回保存在 MobaXterm 中的主机登录密码
* 在 macOs Termianl 中通过代理 ssh 到远程主机

## 找回密码

运维工程师最重要的工具就是终端软件，虽然 SecureCRT 功能强大，不过毕竟是收费软件，个人一般很少会购买使用。另外也有 putty 这个短小精悍的终端软件，但不支持标签是一个硬伤。

MobaXterm 是一个相较 putty 功能强大的终端软件，支持标签和保存主机列表和主机密码，并且免费版提供的功能足以支撑日常个人使用。MobaXterm 支持设置主密码，主密码是在打开软件的时候首先需要输入的，这样可以避免软件随意被其他人使用，毕竟很多时候我们都是把主机密码保存在软件中。另外也支持在会话中保存主机的地址、用户名、密码，这样对于我们经常使用的主机，就可以免去重复输入用户密码的过程。

时间久了，如果我们要用其他的终端登录主机，或者某些配置需要主机的用户口令时，免费版的 MobaXterm 没有提供查看密码的功能。

但是我自己找了下它的配置文件，在软件的同级目录下可以找到 MobaXterm.ini 这个文件，密码以密文的形式保存在里面。

> 我使用的是绿色版 MobaXterm ，版本为 Personal Edition v20.6 Build 4532

```ini
[Passwords]
ssh22:user@1x.8x.6x.1x=ibG9MTOdkcYd6w==
user@1x.8x.6x.1x=ibG9MTOdkcYd6w==
```

通过面向搜索引擎解决问题，找到了一个开源的解密工具 [how-does-MobaXterm-encrypt-password](https://github.com/HyperSine/how-does-MobaXterm-encrypt-password)

运行程序需要本机具备 Python3 环境，我是在 macOS 下解密的，第一次执行会遇到下面编码的问题

```sh
$ python3 MobaXtermCipher.py dec -p 8888 ibG9MTOdkcYd6w
Traceback (most recent call last):
  File "/Users/shiqiang/Projects/opensource-project/how-does-MobaXterm-encrypt-password/python3/MobaXtermCipher.py", line 330, in <module>
    exit(Main(len(sys.argv), sys.argv))
  File "/Users/shiqiang/Projects/opensource-project/how-does-MobaXterm-encrypt-password/python3/MobaXtermCipher.py", line 309, in Main
    cipher = MobaXtermCryptoSafe(master_password.encode('ansi'))
LookupError: unknown encoding: ansi
```

需要打开编辑器修改 MobaXtermCipher.py 将 `ansi` 修改为 `gbk` ，再重新执行一次就可以获取到明文密码。

## macOS 下通过代理 ssh 到远程主机

> 本文的操作系统版本为 macOS Monterey Version 12.2.1

macOS 在系统便好中设置的代理对 Terminal 无效，查到网上有些办法可以通过环境变量方式来支持 `curl` 、 `wget` 、 `git` 等命令的代理配置，自己测试后对于 `ssh` 命令无效，原来 `ssh` 有专门的参数，实际如果只是 `ssh` 使用代理，只要按照下面的命令执行即可：

```sh
$ ssh -o ProxyCommand="nc -X 5 -x 1x.1x.1x.1x:1080 %h %p" root@1x.8.6.1
```

## 参考资料：

1. [how-does-MobaXterm-encrypt-password](https://github.com/HyperSine/how-does-MobaXterm-encrypt-password)
2. [MobaXterm（非专业版）中登录过且保存密码的服务器，如何获取密码明文？](https://blog.csdn.net/xpx3216/article/details/112563929)
3. [全能终端神器——MobaXterm](https://zhuanlan.zhihu.com/p/56341917)
4. [mac命令行终端下使用代理](https://blog.csdn.net/weixin_43972437/article/details/118724529)
