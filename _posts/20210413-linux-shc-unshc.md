---
title: Shell脚本加密与解密
date: 2021-04-13 14:59:22
tags:
keywords: Linux Shell, Shell, Linux, Shell加密, Shell解密
category: Linux
description: shc能够将shell脚本编译加密为可执行文件，unshc能够解密相关文件。
---

shell 脚本为我们日常运维提供了巨大的帮助，但是shell脚本本身是明文，如果里面保存了一些敏感信息，可能会发生信息泄漏。同时shell脚本发布之后，也容易被其他人修改。为了做好敏感信息保护及文件保护，我们可以用 shc 命令将shell脚本生成为一个加密的可执行文件。

### shc 介绍

SHC是一款Shell脚本编译器，它对shell脚本进行转码并加密（RC4加密算法）。这样做的好处是可以避免脚本被意外修改，一定程度上能够保护源码。它首先将shell脚本转化为c语言，然后用c编译器将其编译成可执行文件。所以shc本身并不是真正的编译器，真正的编译过程还是本地的c语言编译器来做的。它只是编码并加密了shell脚本，将其转换为c语言。

shc的作者是西班牙的[Francisco Javier Rosales García](http://www.datsi.fi.upm.es/~frosal/)，他维护shc到了3.8.9。现在github上的[shc](https://github.com/neurobin/shc)由neurobin维护。

### shc 安装

```sh
$ yum install shc
```

### shc 用法

```sh
$ shc Usage: shc [-e date] [-m addr] [-i iopt] [-x cmnd] [-l lopt] [-o outfile] [-rvDSUHCABh] -f script
$ cat test.sh
#!/bin/bash

echo "Hello World!"
$ shc -f test.sh -o test
$ ll
总用量 36
-rwxrwxr-x. 1 root root 11192 4月  13 18:57 test
-rw-r--r--. 1 root root    34 4月  13 18:57 test.sh
-rw-r--r--. 1 root root 17732 4月  13 18:57 test.sh.x.c
$ ./test
Hello World!
$ file test
test: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.32, BuildID[sha1]=7d7deea61fdbd55b3aa5adf3ba3ea22311169487, stripped
```

### unshc 介绍

有人加密，就有人要解密，好在 shc 使用的加密算法比较简单，所以有 unshc 的出现。unshc 的项目地址 [unshc](https://github.com/yanncam/UnSHc)。unshc 是一个脚本，能够将shc加密的可执行文件解密。

## 参考资料：

1. [Wiki shc](https://en.wikipedia.org/wiki/Shc_the_shell_script_compiler)
2. [Unshc](https://github.com/yanncam/UnSHc)
3. [Retrieve plain text script from compiled bash script](https://stackoverflow.com/questions/3408373/retrieve-plain-text-script-from-compiled-bash-script)
4. [Limitations of shc, a Shell Encryption Utility](http://www.linuxjournal.com/article/8256?page=0,0)
5. [SHC Shell Compiler](http://www.linux-magazine.com/Online/Features/SHC-Shell-Compiler)