---
title: 20220312-ctf-pwn-introduce
date: 2022-03-12 23:01:58
keywords: pwn, ctf
description: 本文收集整理了 CTF 比赛 pwn 题型的介绍资料和常用工具，为后续深入了解 pwn 打下基础。
---

pwn 是 CTF 比赛中的一种题目类型，pwn 是从 own 引申出来，在网络文化中习惯上用于嘲笑竞争对手已经被完全击败，如：You just got pwned !

pwn 主要是指利用缓冲区溢出攻破设备或者系统。CTF 比赛中通常会给出一个已经编译好的二进制程序，然后比赛选手通过对程序进行逆向分析和调试来找到可利用的漏洞，并编写利用代码，通过远程代码执行来达到溢出攻击的效果，最终拿到目标机器的 shell 夺取 flag。

众所周知，计算机程序的运行依赖于函数调用栈。栈溢出是指在栈内写入超出长度限制的数据，从而破坏程序运行甚至获得系统控制权的攻击手段。

pwn 攻击主要有四种方法：

* 修改返回地址，让其指向溢出数据中的一段指令（shellcode）
* 修改返回地址，让其指向内存中已有的某个函数（return2libc）
* 修改返回地址，让其指向内存中已有的一段指令（ROP）
* 修改某个被调用函数的地址，让其指向另一个函数（hijack GOT）

## 需要掌握的知识点

* 堆 Heap
* 栈 Stack
* 指针与地址
* 常用的汇编命令

## 常用工具

* gdb
* ida
* checksec
* ROPgadget
* rp++
* ropeme

## 参考资料

1. [Github CTF 练习题](https://link.jianshu.com/?t=https%3A%2F%2Fgithub.com%2Fctfs)
2. [手把手教你栈溢出从入门到放弃（上）](https://zhuanlan.zhihu.com/p/25816426)
3. [手把手教你栈溢出从入门到放弃（下）](https://zhuanlan.zhihu.com/p/25892385)
