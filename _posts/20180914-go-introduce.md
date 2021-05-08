---
title: Go学习入门
date: 2018-09-14 11:11
tag: 
keywords: go, go language
description: Go语言宣称为互联网时代的C语言，那她有那些特性值得我们必须学习呢。
---


## 1. 为什么要学习Go
Go语言宣称为互联网时代的C语言，那她有那些特性值得我们必须学习呢：

* 并行与分布式支持。除了我们日常熟悉的进程和线程，Go语言中提供了协程coroutine，从而简化了并行开发的难度。
* 软件工程支持。这体现在很多方面，我觉得Go语言借鉴了Python的做法，通过代码风格规范进行强制统一，从而减少工程管理的难度。
* 编程思想的变化。过去面向过程和面向对象是两大编程流派，Go语言则使用批判吸收的方法，融合众家之长，以更实用的目标作为语言发展的基调。

## 2. Go语言简史
Go语言的历史要从贝尔实验室开始说起，我们知道贝尔实验室计算科学研究中心的肯•汤普逊 Ken Thompson 和丹尼斯 • 里奇 Dennis Ritchie开发了Unix系统以及开发系统所使用的C语言。在这之后他们还开发了Plan 9操作系统。
后来包括Ken Tompson在内的Plan 9 原班人马加入Google，在Google他们创立了Go语言。2007年9月之前Go语言是20%自由时间的实验项目，到了2008年5月Google发现Go语言的巨大潜力，开始全力支持这个项目。Go语言的第一个版本是2009年11月正式对外发布。
Go语言采用BSD授权协议，任何人都可以查看Go语言的所有源代码。
Go语言之所以发展迅猛引人注目，还有一个重要原因是其拥有一个豪华的作者团队。我们来看一下GO的作者都有谁：

* [Ken Thompson](https://en.wikipedia.org/wiki/Ken_Thompson)Unix以及C的开发人员，1983年图灵奖得主
* [Rob Pike](https://en.wikipedia.org/wiki/Rob_Pike)Unix小组成员
* [Robert Griesemer](https://github.com/griesemer)
* [Russ Cox](https://swtch.com/~rsc/)Google Code Search 项目负责人
* [Ian Lance Taylor](https://github.com/ianlancetaylor)GCC社区活跃人物
* [Brad Fitzpatrick](https://en.wikipedia.org/wiki/Brad_Fitzpatrick)Memcached 的作者

## 3. 语言特性

* 自动内存回收。手工管理内存让人十分崩溃，很容易造成内存泄漏问题，目前内存泄漏问题最佳的解决方案就是在语言级别提供GC（Garbage Collection）
* 更丰富的内置类型。Go语言中新增了一些近年来流行语言中提供的一些高级类型，例如map、slice
* 函数多返回值，这个功能是我接触了Python之后才知道的，之前的语言为了返回多个值，通常会创建一个数组或者结构体，现在再也不用发愁了，Go语言率先在静态语言中提供了函数多返回值功能
* 错误处理。引入了三个关键字用于标准的错误处理流程，defer、panic、recover，避免了try catch的使用，减少了代码量
* 匿名函数和闭包，这个特性好像也是动态语言率先使用的
* 类型和接口。类型定义类似于C语言中的结构（struct），引入了“非侵入式”接口概念
* 并发编程。使用goroutine而不是操作系统的并发机制，使用消息传递而不是使用共享内存来通信，让并发编程变得更加轻盈和安全
* 反射 Reflection，Go实现了反射的大部分功能，但没有像Java语言那样内置类型工厂
* 语言交互性。可以在Go语言中使用Cgo的特定语法混合编写C语言代码

## 4. Hello World
```go
package main

/**
 * My First Go Program!
 **/
 import "fmt"
 
 func main() {
     fmt.Println("Hello, world!")
 }
```
其中的一些语法规范

* package 表明代码所属的包，包是Go语言最基本的分发单位，要生成可执行的Go程序，必须有一个main包以及一个main函数
* main 函数，不能带参数，也不能有返回值
* import 用于引入程序依赖的包，注意不能包含在程序中没有用到的包，这一点可以看出Go是极力追求精简的
* 代码注释与C语言一样，支持两种注释方式，单行注释和多行注释
* 花括号不能在行的开头，否则编译会报错
> Python 和 Go 给我印象最深刻的一点是形式即代码，例如原来在PHP中，每段代码的结尾都必须有```;```来表示，而在Python和Go中，通过严格的形式规定，可以减少不必要代码的录入，同时又能够保证参与工程开发的所有人都遵循这个形式。
特别是对于大型的工程，不再通过一些配置文件来指定目录层次或者引用关系，而完全依靠实际的目录层级来推导工程的结构。这样就保证了工程规范能够强制性的被所有人执行，大大提高了协作的效率。


## 5. 编译环境与开发工具
Go语言提供了Windows、Linux、Mac OS三种安装包，因为众所周知的原因，我们没有办法从官网下载，但是大家还是可以从中文站点[下载](https://studygolang.com/dl)
```bash
bogon:~ rousseau$ go version
go version go1.11 darwin/amd64
```
上面的命令执行后如果能够正常输出表示编译环境已经安装好了，可以将hello world程序编译了。将上面的代码保存为hello.go，然后执行。
```bash
bogon:go.my rousseau$ go run hello.go
Hello, world!
```
```go run```命令将编译、链接、 运行三个步骤合为一步，运行后不会再当前目录看到任何中间文件和可执行文件。使用```go build```命令会生成可执行文件。
```bash
bogon:go.my rousseau$ go build hello.go 
bogon:go.my rousseau$ ls
hello		hello.go
bogon:go.my rousseau$ ./hello 
Hello, world!
```
关于开发工具的选择，这个第一要看个人习惯，第二要结合项目的规模和最多使用的一些功能。可以用来编程的工具包括 vim、VS Code、liteIDE、goland等，目前我水平还比较菜，感觉Vim或者VS Code就能满足需求了。

## 6. Go的工程管理
Go消除了工程文件的概念，完全用目录结构和包名来推导工程结构和构建顺序。
看一个简单的命令行计算器工程的目录结构，代码在[这里](https://github.com/cocowool/k8s-go/tree/master/golearn/calcproject)
![](/20180914-go-introduce/39469-20180914111126339-138017365.png)
想要编译这个工程，执行以下命令。
```bash
bogon:calcproject rousseau$ export GOPATH=$(pwd)
bogon:calcproject rousseau$ cd bin
bogon:bin rousseau$ go build calc
bogon:bin rousseau$ ./calc 
Usage: calc command [arguments] ... 

The commands are:
	add	Addition of two values.
	sqrt	Square root of a non-negative value.
```

## 7. 问题追踪和调试

### 7.1 打印日志
Go语言中包含了一个fmt包，包含了大量的打印函数，主要用到的是```Printf()```和```Println()```两个函数。区别就是一个能够换行一个不能换行。

### 7.2 GDB 调试

Go语言编译的二进制程序直接支持GDB调试，具体的细节后续会详细介绍。



## 参考资料:

1. [打不开的 Golang 官网](https://golang.org)
2. [Golang中文网](https://studygolang.com)
3. [LiteIDE](http://liteide.org/en/download/)
4. [Goland](http://www.jetbrains.com/go/)
5. [GDB调试Go程序](http://blog.studygolang.com/2012/12/gdb%E8%B0%83%E8%AF%95go%E7%A8%8B%E5%BA%8F/)












