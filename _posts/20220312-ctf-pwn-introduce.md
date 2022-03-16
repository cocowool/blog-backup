---
title: 20220312-ctf-pwn-introduce
date: 2022-03-12 23:01:58
keywords:
description:
---

pwn 是 CTF 比赛中的一种题目类型，主要是指利用缓冲区溢出攻破设备或者系统。众所周知，计算机程序的运行依赖于函数调用栈。栈溢出是指在栈内写入超出长度限制的数据，从而破坏程序运行甚至获得系统控制权的攻击手段。

pwn 攻击主要分为四个步骤：

* 修改返回地址，让其指向溢出数据中的一段指令（shellcode）
* 修改返回地址，让其指向内存中已有的某个函数（return2libc）
* 修改返回地址，让其指向内存中已有的一段指令（ROP）
* 修改某个被调用函数的地址，让其指向另一个函数（hijack GOT）

## 需要掌握的知识点

* 堆 Heap
* 栈 Stack
* 指针与地址

## 常用工具

* gdb
* ida
* 

## 参考资料

1. [Github CTF 练习题](https://link.jianshu.com/?t=https%3A%2F%2Fgithub.com%2Fctfs)
2. [手把手教你栈溢出从入门到放弃（上）](https://zhuanlan.zhihu.com/p/25816426)
