---
title: Linux中的普通命令如何以管理员身份运行
date: 2019-03-06 08:36
tag: 
---

> 相信大部分朋友都比较熟悉Windows中的“以管理员身份运行”这个菜单，当我们需要安装的软件需要一些操作系统级别的权限时，我们会使用这个菜单。在Linux中，也有一种方式实现类似Windows这个菜单的效果，允许普通的命令或程序以管理员的身份运行，本文介绍具体实现的方式。

<!-- more -->

## `chmod u+s`

“为了方便普通用户执行一些特权命令，SUID/SGID程序允许普通用户以root身份暂时执行该程序，并在执行结束后再恢复身份。” chmod u+s 就是给某个程序的所有者以suid权限，可以像root用户一样操作。

### 具体的操作方法

操作这些标志与操作文件权限的命令是一样的, 都是通过```chmod```命令，有两种具体的方法：

```bash
$ chmod u+s temp #为temp文件加上setuid标志. (setuid 只对文件有效)
$ chmod g+s tempdir #为tempdir目录加上setgid标志 (setgid 只对目录有效)
$ chmod o+t temp #为temp文件加上sticky标志 (sticky只对文件有效)
```

设置完成后，可以通过```ls -l```查看效果。

```bash
$ ls -l
rwsrw-r--   1 rousseau  staff   85 Sep 22  2017 test1   #表示有setuid标志
rwxrwsrw-   1 rousseau  staff   85 Sep 22  2017 test2   #表示有setgid标志
rwxrw-rwt   1 rousseau  staff   85 Sep 22  2017 test3   #表示有sticky标志
rwSr--r--   1 rousseau  staff   85 Sep 22  2017 test4
```

> 系统是这样规定的, 如果本来在该位上有x, 则这些特殊标志显示为小写字母 (s, s, t). 否则, 显示为大写字母 (S, S, T)

## set uid, gid, sticky bit 权限说明

一个文件都有一个所有者, 表示该文件是谁创建的。同时, 该文件还有一个组编号, 表示该文件所属的组, 一般为文件所有者所属的组。

如果是一个可执行文件, 那么在执行时, 一般该文件只拥有调用该文件的用户具有的权限，而setuid, setgid 可以来改变这种设置。

#### set uid

设置使文件在执行阶段具有文件所有者的权限。典型的文件是```/usr/bin/passwd```如果一般用户执行该文件, 则在执行过程中, 该文件可以获得root权限, 从而可以更改用户的密码。

#### set gid

该权限只对目录有效. 目录被设置该位后, 任何用户在此目录下创建的文件都具有和该目录所属的组相同的组。

#### sticky bit
该位可以理解为防删除位。一个文件是否可以被某用户删除, 主要取决于该文件所属的组是否对该用户具有写权限。 如果没有写权限, 则这个目录下的所有文件都不能被删除, 同时也不能添加新的文件. 如果希望用户能够添加文件，但同时不能删除文件, 则可以对文件使用sticky bit位. 设置该位后, 就算用户对目录具有写权限, 也不能删除该文件。

### 参考资料

1. [chmod g+s\chmod u+s](https://blog.csdn.net/taiyang1987912/article/details/41121131)
2. [Linux中的普通命令如何以管理员身份运行](https://www.cnblogs.com/zjutzz/p/4229856.html)












