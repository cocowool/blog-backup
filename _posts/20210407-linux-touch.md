---
title: Linux修改文件创建时间
date: 2021-04-07 08:36:25
tags:
keywords: touch, 文件创建时间, linux touch, linux
category: 通过touch命令可以修改文件创建时间。
---

touch命令一直被我用来作为创建一个空白文件的命令，但是今天我才发现，原来touch命令主要的作用是用来修改文件时间，怪不得我的服务器上被黑客上传的文件都是很久之前的创建时间。
```sh
touch : change file access and modification time

touch [-acfm]
[-r reference-file] [--file=reference-file]
[-t MMDDhhmm[[CC]YY][.ss]]
[-d time] [--date=time] [--time={atime,access,use,mtime,modify}]
[--no-create] [--help] [--version]
file1 [file2 ...]
```
说明：
touch 指令改变档案的时间记录。 ls -l 可以显示档案的时间记录。
参数：
* a 改变档案的读取时间记录。
* m 改变档案的修改时间记录。
* c 假如目的档案不存在，不会建立新的档案。与 –no-create 的效果一样。
* f 不使用，是为了与其他 unix 系统的相容性而保留。
* r 使用参考档的时间记录，与 –file 的效果一样。
* d 设定时间与日期，可以使用各种不同的格式。
* t 设定档案的时间记录，格式与 date 指令相同。
* –no-create 不会建立新档案。
* –help 列出指令格式。
* –version 列出版本讯息。

范例：
最简单的使用方式，将档案的时候记录改为现在的时间。若档案不存在，系统会建立一个新的档案。
```sh
touch file
touch file1 file2
```
将 file 的时间记录改为 5 月 1 日 16 点 8 分，公元2011年
时间的格式可以参考 date 指令，至少需输入 MMDDHHmm ，就是月日时与分。
```sh
touch -c -t 05011608 file
touch -c -t 050116082011 file
```
将 file 的时间记录改变成与 referencefile 一样。
```sh
touch -r referencefile file
```
将 file 的时间记录改成 5月1日16点8分，公元2011年

时间可以使用 am, pm 或是 24 小时的格式，日期可以使用其他格式如 1 May 2011。
touch -d 和 date -s 的用法相同。

如果没有指定日期，默认为系统日期
touch -d 16:08 file
touch -d “16:08″ file
touch -d “4:08pm” file

如果没有指定时间，默认为 00:00:00
touch -d 20110501 file
touch -d “05/01/2011″ file
touch -d “20110511” file
touch -d “4:08pm 05/01/2011″ file
touch -d “20110501 16:08″ file
touch -d “20110501 16:08:00″ file