---
title: SQL文件的BOM问题导致的invalid character错误及解决
date: 2017-12-11 19:07
tag: 
keywords: sql, bom, invalid character
description: BOM问题引起的“ORA-00911 invalid character”错误。
---

最近在做数据的搬运工，将Oracle中的数据搬运到ES中，方案很成熟了，使用Logstash的jdbc-input执行SQL，然后将结果输出到ES中。这么简单的问题，在测试环境中测试也一帆风顺，可一上生产环境傻眼了，始终报“ORA-00911 invalid character”的错误。

困扰了好几天，测试环境一直没问题，生产环境不管用多么简单的SQL都出同样的问题。最后，认真看了一下日志，突然发现```feff```是什么鬼？

![](/20171211-error-caused-by-bom/39469-20171211190608665-1739088994.png)

有了这个线索，百度了一下，原来是文件的BOM头，忽然想起来SQL文件是在Windows下用记事本保存为UTF-8格式的。在Linux下重新创建了一个空白文件，将SQL语句拷贝过去，再执行就没问题了。
只能说，尽量还是不要用记事本啊～～

### 如何查看文件是否含有BOM头

#### file 命令
在Linux下，可以简单的使用file命令来查看文件是否含有BOM头。
```bash
[root@bj1eccap01 ~]# file test.txt 
test.txt: UTF-8 Unicode (with BOM) text, with no line terminators
```

#### awk 命令
```bash
[root@bj1eccap01 ~]# hexdump test2.txt 
0000000 bbef 48bf 6c65 6f6c 5720 726f 646c bcef
0000010 0d81 000a                              
0000013
[root@bj1eccap01 ~]# find . -type f -print0 | xargs -0r awk '/^\xEF\xBB\xBF/ {print FILENAME} {nextfile}'
./test2.txt
```

### 删除BOM头

#### sed 命令
```bash
[root@bj1eccap01 ~]# sed -i -e '1s/^\xEF\xBB\xBF//' test2.txt 
[root@bj1eccap01 ~]# find . -type f -print0 | xargs -0r awk '/^\xEF\xBB\xBF/ {print FILENAME} {nextfile}'
[root@bj1eccap01 ~]# hexdump test2.txt 
0000000 6548 6c6c 206f 6f57 6c72 ef64 81bc 0a0d
0000010
```
> 这个问题反过来想，我觉得是 logstash 不支持含有 BOM 头的SQL文件，是不是可以给官方提交一个Feature来解决这个问题？

参考资料：
1、[带BOM的UTF-8](https://www.zhihu.com/question/20167122)
2、[Byte order mark](https://en.wikipedia.org/wiki/Byte_order_mark)
3、[How to check if file has a BOM in utf-8 text](https://unix.stackexchange.com/questions/170775/how-to-check-if-file-has-a-bom-in-utf-8-text)
4、[Using awk/sed to detect/remove the byte order mark (BOM)](http://muzso.hu/2011/11/08/using-awk-sed-to-detect-remove-the-byte-order-mark-bom)

