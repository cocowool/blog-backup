---
title: 常用 Shell 命令
date: 2021-04-12 16:56:07
tags:
keywords:
description:
category: Linux
---

## 基础语法

### 循环
```sh
#!/bin/bash

for i in `seq  1 10`
do
    echo "xxxx"
done
```

### SHELL中的特定变量
1. $# 传递到脚本的参数个数
2. $* 以一个单字符串显示所有向脚本传递的参数。与位置变量不同，此项参数可超过9个
3. $$ 脚本运行的当前进程ID号
4. $! 后台运行的最后一个进程的进程ID号
5. $@ 与 $# 相同，但使用时加引号，并在引号中返回参数个数
6. $- 显示shell使用的当前选项，与SET命令功能相同
7. $? 显示命令最后退出的状态。0表示没有错误，其他任何值表明有错误

### SHELL脚本语法检查
```sh
$ sh -n ***.sh
```
以上命令可以检查shell脚本中是否存在语法错误，如果没有异常输出证明脚本没有明显的语法问题。

```sh
$ sh -vx ***.sh
```
该命令可以看到每行代码的原始命令以及命令执行时的情况，包括运算结果、逻辑判断结果、变量赋值等。

## 常用命令

### 1.查找文件

```sh
$ find . -name 'name'     #默认支持递归查询
$ #查找某个具体日期之后修改过的文件
$ find . -newermt “2015-05-10”
$ #查找某个具体日期之前修改过的文件
$ find . ! -newermt “2015-05-10"
```

### 2. 查找文件后执行命令
```sh
$ find . -name 'name' | xargs -n 1 cat
```

### 3. 比较文件夹
```sh
$ diff -r -x *.bak -c folder1 folder2
```

### 4. 拷贝文件的时候排除svn文件
copy with tar 技巧：
```sh
$ tar --exclude='.svn' -c -f - /path/to/sourcedir/* | (cd /path/to/destdir ; tar xfp -)
$ # 比较懒的方法，拷贝后删除 
$ find /path/to/destdir -name '.svn' -exec rm -r {} \;
$ # svn的导出export功能
$ # 使用rsync方法：
$ rsync -r --exclude=.svn /home/user/progname/ /home/user/progname.copy
```

### 5. 一条循环统计的命令，for\grep\zgrep
```sh
$ for((i=110714;i<110719;i++)); do echo -e "$i\t\c" ; zgrep 'systemnotice' $i/$i.t.leju.com_*.cn.gz | wc -l; done
```
利用循环，分别统计gz文件中含有关键字的记录，同时打印出日期和统计结果
使用到了 echo -e 属性，还用到了 zgrep 来查找经过gzip压缩的文件内容。

扩展用法，利用echo打印三列内容。
```sh
$ for((i=110804;i<110816;i++)); do echo -e "$i\t\c"; echo -n `zgrep '/love/?m=yiqiso&a=showtop HTTP/1.1'  $i/$i.t.leju.com_*.cn.gz | wc -l`;echo -e "\t" `zgrep '/love/?m=yiqiso&a=showtop HTTP/1.1' $i/$i.t.leju.com_*.cn.gz  | awk '{print $1}' | awk -F ":" '{print $2}' | uniq -c | wc -l`; done
```

利用 echo -n 同行打印的功能，将第三行的内容合并到上面一行中
第二个统计数字为日志中的独立IP数目，利用uniq -c 来进行统计

### 6. 修改服务器时间
```sh
 date 显示当前时间
 date -s "2007-08-03 14:15:00" 修改时间
 clock -w 把系统时间写入CMOS

 hwclock --set --date="03/19/2013 19:09:00"
```

### 7. 替换文件某行的内容
```sh
sed '2 s/xxx/bbb/'
```


### 8. 查找前缀和后缀符合特定要求的内容，并进行替换
```sh
1,$s/^BHME\(\S\)\(\S\)\(\S*\)\(\S\)\(\S\)\.jpg/\/BHME\/Source_pic\/\1\5\/\2\4\/BHME\1\2\3\4\5\.jpg/g   
```

### 9. 查找文件内容中含有特定字符的文件，并列出文件列表
```sh
find . -type f  -exec grep 'anlian' -l {} \;
```

### 10. 查找文件内容并删除保存
```sh
sed -i '/aaa/d'  filename
```


### 11. 查找第五列的内容，并且取部分内容
```sh
for i in `awk '{print gensub(/\/data1\/static\.house\.sina\.com\.cn\/cricfs\//,"","g",$5) }' /data1/logs/vfslog/received/111122/111122-upload.file.dc.cric.com.log`;do `rsync -Rtp $i 172.16.244.156::upload_file_dc_cric_com_storage/`; done;
```

### 12. 查找并杀死符合条件的进程
```sh
$ kill -9 `ps -ef | grep php | grep -v grep | awk '{print $2}'`     
# 这个命令可以杀死一条符合条件的进程
$ ps -ef | grep background |awk '{print $2}' | sed "s/^/kill -9 /g"|sh - 
# 这样就可以杀死多个进程
```

### 13. 批量生成建表语句
```sh
     for((i=0;i<=100;i++)); do sed s/#/$i/ test >> sql; done;
```

### 14. 批量修改文件名

Linux有一个很好用的rename命令，格式如下：rename oldpattern newpattern files
可惜在Mac下没有原生的命令支持。

还可以使用脚本的方式，其中用到了sed的替换指令。
```sh
for i in *;do OUT=$(echo $i | sed 's/ *//g');mv "$i" $OUT;done;
```
完全使用sed的一个方案，看不太明白
```sh
ls | sed -n '/ /{h;s/ //g;G;s/ /\\ /g;s/\(.*\)\n\(.*\)/mv \2 \1/e}'
```

### 15. 查看系统的发行版本

```sh
cat /etc/issue
cat /etc/redhat-release
```

### 16. 添加路由
```sh
route add -net 10.71.32.0/24 gw 172.16.228.1

删除路由

route del -net 0.0.0.0 gw 192.168.56.1
```

### 17. SED查找内容并输出
```sh
查询匹配的内容并输出

sed -n '/17\/Apr\/2011/,$p' /data0/bbslog/110423/i.house.sina.com.cn_244192.log  > output_test
```

### 18. 时间处理
```sh
# 查看当前时间戳
$ date +%s

# 查看指定时间的时间戳
$ date -d 2008-01-01 +%s
$ date -d 20080101 +%s

# 将时间戳转换为日期字符串
$ date -d '1970-01-01 UTC 1199116800 seconds'
2008年 01月 01日 星期二 00:00:00 CST
$ export LANG=en
$ date -d '1970-01-01 UTC 1199116800 seconds'
Tue Jan  1 00:00:00 CST 2008

# 将时间戳转换为日期格式
$ date -j -f "%s" 160336789645 "+%Y-%m-%d %H:%M:%S"    //Mac下有效
$ date -d @1557025194 "+%Y-%m-%d"    //Mac下无效
```

### 20. AWK抽取文件内容

```sh
# 抽取文件的奇数行
$ awk 'NR%2' file

# 抽取文件的偶数行
$ awk 'NR%2==0' file
```

### 21. 文件的压缩与解压缩
```sh
tar -czf xxx.tar.gz folders
# 压缩并打包目录
```

### 22. 文件系统挂载
```sh
$ mount 11.155.x.x:/vol/xxx /folder
```

参考资料：
1. [使用Diff比较两个文件夹](http://blog.csdn.net/magicpang/article/details/3030089)
2. [Linux Shell For循环总结](http://hi.baidu.com/plp_cm/blog/item/d828d1881b05e2faf11f3607.html)
3. [echo不换行的实现](http://blog.csdn.net/lllxy/article/details/3423580)
4. [Bash for遍历目录](http://hi.baidu.com/cationly/blog/item/398010ef9454f23cacafd5f2.html)
5. [Linux下批量修改文件名](http://www.osmsg.com/2011/03/batch-change-file-names-under-linux/)
6. [Howto：Linux Rename Multiple Files at a shell prompt](http://www.cyberciti.biz/tips/renaming-multiple-files-at-a-shell-prompt.html)
7. [Centos Linux查看系统发行版本](http://lok.me/2011/09/centos-redhat-linux-release/)
8. [Linux下时间戳的转换方法](http://xok.la/2008/06/linux_date_transfer.html)