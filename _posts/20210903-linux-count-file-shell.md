---
title: Linux 统计文件属性的小脚本
date: 2021-09-04 08:00:45
tags:
keywords: Linux, 统计文件, stat, 使用率
description: 写了一个脚本，支持统计某个文件夹下某一类文件总的大小，或者某一类文件以外其他文件总的大小。
---

## 背景需求

我的[站点](http://www.edulinks.cn)使用 hexo 生成静态文件存放在新浪云服务器上，因为静态文件都做为代码存放上传，新浪云默认提供的代码空间是100M，因此我经常关注 hexo 生成的 public 文件夹的使用情况。

最近发现 public 使用已经到了 91M，感觉很快就会超过 100M 的上限了。因此需要对生成的静态文件做一些分析，看是否还有优化压缩的空间。

hexo 生成的静态文件主要有两类，一类是 markdown 转换后的 html 文件，另一大块主要是 文章中引用的图片。由此产生了一个需求，我需要统计 `pubic` 文件夹下（包括子文件夹）图片占用的空间大小。

## 统计脚本

经过几个小时的编写，终于完成了这个统计脚本 [`count_file.sh`](https://github.com/cocowool/sh-valley/blob/master/shell/count_file.sh) 。支持统计某个文件夹下（包含子文件夹）某一类文件的大小，或者除某一类文件外其他文件的大小。

```sh
#!/bin/bash

# 统计一个目录下指定类型或限制某种类型以外的文件的大小，包括子目录

help() {
  echo "Description: 用于查找某个目录下特定类型文件的数量和总的大小，或者查找某种类型以外的文件数量和总的大小"
  echo "Author: cocowool <cocowool@qq.com>, Blog: http://www.edulinks.cn"
  echo "Usage: sh count_file.sh -p folder_path [ -t jpg ] [ -x html ] "
  echo "       -p : 需要查找的文件路径"
  echo "       -t : 需要查找的文件类型"
  echo "       -x : 需要排除的文件类型"
  exit 0
}

if [[ $# == 0 ]] || [[ "$1" == "-h" ]]; then
  help
  exit 0
fi

# INCLUDE_FILE_TYPE=""
# EXCLUDE_FILE_TYPE=""

# echo $*
while getopts "p:t:x:" opt
do
  case "$opt" in
    p) 
      FOLDER_PATH=$OPTARG ;;
    t) 
      # echo "Found t option"
      INCLUDE_FILE_TYPE=$OPTARG ;;
    x)
      # echo "Found x option"
      EXCLUDE_FILE_TYPE=$OPTARG ;;
    # getopts doesn't support long option, such as --option
    # debug)
    #   echo "Found debug option"
    #   echo $OPTARG ;;
    *) echo "$opt is invalid option" ;;
  esac
done

# FOLDER_PATH="/Users/shiqiang/Projects/edulinks-blog/public"
# 解决文件中含有空格的问题
IFS=$(echo "\n\b")

echo "查找的文件路径为：$FOLDER_PATH"

if [ -n $FOLDER_PATH ]; then
  #list=`find $FOLDER_PATH | grep "jpg"`
  if [ -n "$INCLUDE_FILE_TYPE" ]; then
    echo "查找的文件后缀为：$INCLUDE_FILE_TYPE."
    list=`find $FOLDER_PATH -type f -name "*.$INCLUDE_FILE_TYPE"`
  elif [ -n "$EXCLUDE_FILE_TYPE" ]; then
    echo "查找文件后缀不是：$EXCLUDE_FILE_TYPE 的文件"
    list=`find $FOLDER_PATH -type f ! -name "*.$EXCLUDE_FILE_TYPE"`
  fi

  for i in $list
    do
    # echo $i
    fileSize=$(du -k "${i}" | cut -f1)
    # echo $fileSize
    ((totalSize=fileSize+totalSize))
  done

  echo "文件总大小为：$((totalSize/1024))M"
fi
```

## 基础命令
这个脚本难点在于命令行参数的处理，因为写这个脚本查阅了一些资料，主流的方案是使用下面两个命令：

* getopts 是 Linux 系统中的一个内置变量，一般通过循环的方式使用，每次循环 getopts 都会检查命令行选项，并将命令行选项的值爆存在 OPTARG 变量中
* getopt 是一个外部命令，相较于 `getopts` 命令能够支持长参数，但是使用起来较 getopts 复杂

本文中因为参数比较少且没计划使用长参数，因此采用了 `getopts` 来处理选项和参数问题。如果不熟悉 shell 的使用，可以查看 [Shell 入门详解（一）](http://www.edulinks.cn/2020/07/22/20200722-shell-quick-start/) ，希望能有所帮助。

## 写在最后

最后统计发现，我写了 470 篇博客，引用了 700 多张图片。其中 html 文件大小占用了 11M 的空间，而图片占用了 79M 的空间。如果后续的文章还要放很多图片的话，很快源码空间就会使用完，看来是要考虑准备用图床的方案来存放博客的图片了。

各位自己有独立博客的小伙伴，有没有图床服务提供，欢迎给我些指导。

## 参考资料

1. [查找多个指定文件或者排除某类文件](https://blog.csdn.net/p1279030826/article/details/111868792)
2. [Shell 脚本如何输出帮助信息？](https://www.cnblogs.com/hangj/p/13331581.html)
3. [给你的shell脚本写一个--help文档](https://www.136.la/jingpin/show-115062.html)
4. [编写可接受选项和参数的shell脚本](https://blog.csdn.net/z1037561063/article/details/80410060)
5. [getopts 的使用](https://www.cnblogs.com/klb561/p/8933992.html)

