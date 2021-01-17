---
title: Linux下数据备份的一般方法
date: 2009-10-29 15:50:01
tag: 
keywords: linux, linux数据备份, tar, scp, rsync
description: 本文讨论了Linux下进行备份的几种常用的方法。
---

备份对于任何应用来说都是一个非常重要，并且应当引起足够重视的事情，就算我们平时使用电脑的过程中也要注意，本文就讨论了Linux下进行备份的几种常用的方法，更多的方法，还需要大家来踊跃献计献策。
一个应用中有很多的重要数据，为了在数据灾难发生后我们能够及时、迅速、准确的恢复我们的应用到一个比较合适的位置，我们必须定期的对我们的数据资料进行备份，今天所要讨论的就是Linux下数据资料备份的方法。

## 1、本机数据手工备份

Linux上有功能强大的tar命令，tar最初是为了制作磁带备份而设计的，他的作用是把文件和目录备份到磁带中，然后从磁带中提取或恢复文件。现在我们可以使用tar来备份数据到任何存储介质上。
tar命令备份数据的格式如下：`tar cvf backup.tar /home/www `   这个命令的含义就是将 /home/www/ 打包为 backup.tar 文件，如果我们需要压缩，则需要加上 z 命令。使用这个命令，我们可以手工备份服务器上的数据，但是要做到定时自动备份，我们还需要借助下一个技巧。

## 2、本机数据自动备份

要实现自动备份，我们首先需要将 tar 命令写在脚本里。比如下面这样一个脚本：

```sh
#!/bin/sh

tar zcvf test.tar /home/www/
```

保存为 test.sh 并添加可执行权限，执行后，发现提示：`tar: Removing leading /' from member names`
这个问题是由于脚本执行的时候默认是在用户目录下的。这样就选成了问题的存在,解决办法就是在执行 tar 之前 cd 切换到要打包的目录就可以了。

有了可以执行命令的脚本，还要能够让他定时执行，这个就要借助于计划任务了。

Linux 为我们提供了 cron 这个强大的工具，我们可以用它来实现几乎任何类型的定时触发。每个用户都有自己的 cron 配置文件，一般来说，我们都是通过 root 用户来运行 cron 任务，以保证我们能够获得足够的权限。如果我们希望每天凌晨四点定时运行我们之前写好的脚本，我们可以通过 crontab -e 来编辑配置文件，并加入：

```sh
0 4 * * * /home/test.sh
```

这样，每天凌晨4点系统会自动运行这个备份脚本。
下面是一个自动备份的脚本的例子：
```sh
#!/bin/sh

# aim to backup the resorces files and mysql data that used by shenti system
# crontab run at 4:00AM every day
# procedure: first backup all the data, then check if 3 days ago backup files exist, then delete prior
# created by shiqiang at 2009-10-28

BACKDIR="/home/backup/"
LOGFILE="/home/backup/shenti_backup_log"
MYSQLBIN="/etc/init.d/mysql"

RESOURCEPATH="/home/"    # add the path var to avoid the 'Removing leading / from member names tips'
RESOURCEDIR="www/"

MYSQLDATAPATH="/var/lib/mysql/"
MYSQLDATADIR_1="wys/"
MYSQLDATADIR_2="wys2/"

# cd $BACKDIR

YEAR=`date +%Y`
MONTH=`date +%m`
DAY=`date +%d`

echo "-----------------------------------" >> $LOGFILE
echo $(date +"%Y-%m-%d %H:%M:%S")" Shenti backup begin" >> $LOGFILE

DIRNAME=$BACKDIR$YEAR-$MONTH-$DAY
FILENAME=$DIRNAME/$YEAR-$MONTH-$DAY-shenti-resorces-backup.tar

# change dir to correspond folder
if [ ! -d $DIRNAME ]
then
    mkdir $DIRNAME
fi

# backup the resources folder
cd $RESOURCEPATH
tar zcvf $FILENAME $RESOURCEDIR >> $LOGFILE 2>&1

# backup the mysql data folder, we should be attion about the data consistency

$MYSQLBIN stop >> $LOGFILE 2>&1

cd $MYSQLDATAPATH
FILENAME=$DIRNAME/$YEAR-$MONTH-$DAY-mysqldata-wys-backup.tar
tar zcvf $FILENAME $MYSQLDATADIR_1 >> $LOGFILE 2>&1

FILENAME=$DIRNAME/$YEAR-$MONTH-$DAY-mysqldata-wys2-backup.tar
tar zcvf $FILENAME $MYSQLDATADIR_2 >> $LOGFILE 2>&1

$MYSQLBIN start >> $LOGFILE 2>&1

echo "Resources and mysql data backup finished at"$(date +"%Y-%m-%d %H:%M:%S") >> $LOGFILE

# check the old backup folder
OLDBACKDIR="$BACKDIR"$(date +%Y-%m-%d --date='3 days ago')

if [ -d $OLDBACKDIR ]
then
         `rm -rf $OLDBACKDIR` >> $LOGFILE 2>&1
    echo " [$OLDBACKDIR] Delete Old Backup Folder Success!" >> $LOGFILE
else
         echo "No Old backup folders $OLDBACKDIR  !" >> $LOGFILE
fi

echo "Shenti Backup finished at "$(date +"%Y-%m-%d %H:%M:%S") >> $LOGFILE

cd $BACKDIR
```

## 3、双机数据自动备份

从安全性上考虑来讲，将备份文件存放在宿主机器上本事是不安全的，真的在灾难性情况下，这种备份时很脆弱的，所以我们需要将数据备份到另外的一台服务器上。但是，我们这里实现的双机定时的备份，而不是实时的备份。因为实时备份（热备）对于技术的要求很高，属于商业应用级的。

双机备份，我们可以现在本机上备份文件，在将备份的文件传输到其他机器上。但是这个方法并不好，实际中也不会这么去做。
查看一些资料，有的说可以通过 Mirror 来实现双机的备份[1]，但是这个还需要借助第三方的软件，我觉得并是很好的办法，因为 linux 中同时还有一个强大的工具 scp 。

同时，我们也可以考虑使用 rsync 来同步两个服务器之间的数据，关于 rsync 的使用，在本博中曾经有过一篇做了介绍，这里就不再多说了，希望大家能够找到适合自己的数据备份方案。

参考资料：
1、[Linux下数据的备份方法](http://www.yesky.com/105/197105.shtml)
2、[一个Mysql自动备份脚本](http://linux.chinaunix.net/techdoc/database/2006/12/30/947078.shtml)
3、[验证命令是否成功执行](http://bkeep.blog.163.com/blog/static/1234142902009715589869/)
4、[Mysql备份和恢复策略](http://blog.sina.com.cn/s/blog_4e424e2101000c1x.html)
5、[tar在脚本中使用的错误信息](http://rainbird.blog.51cto.com/211214/41153)
6、[计划任务CRON的配置和说明](http://www.linuxsir.org/main/node/209)











