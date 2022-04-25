---
title: Mysql 编译安装并使用自定义用户启动
date: 2018-01-23 10:17:11
category: 运维
keywords: mysql, mysql linux, mysql 编译
description: Linux下Mysql 编译安装并使用自定义用户启动。
---

> 本文基于 Redhat Linux 6.7 的环境，Mysql 版本为 5.5.37


### 安装前的检查
必备的组件，如果没有使用 yum 进行安装，可以使用网上的源，也可以使用本地光盘作为 Yum 源。
1、CMake
2、make
3、gcc
4、ncurses
5、Perl

### 默认 Layout 与相关的配置参数。
Mysql默认安装在 /usr/local/mysql 下，可以根据下面几个参数对安装目录做自定义。

标粗的三个是最重要和常用的目录。

### 下载、编译和安装
从[这里](https://downloads.mysql.com/archives/community/)下载源代码，版本选择 5.5.37、Operating System 选择 Source Code，找到 (mysql-5.5.37.tar.gz) 然后下载。下载后解压缩到用户目录下，我的用户为eccs，用户目录为 **/home/ap/eccs。
```bash
tar zxvf mysql-5.5.37.tar.gz .
cd mysql-5.5.37
mkdir bld
cd bld
```
我们所有的编译操作都在**bld**文件夹内进行，编译和安装的步骤是很简单的，使用camke编译，然后生成 make，最后 make install。重要的是在编译这一步指定相关的参数。
```bash
cmake .. -DCMAKE_INSTALL_PREFIX=/home/ap/eccs/mysql -DMYSQL_DATADIR=/home/ap/eccs/mysql_data -DSYSCONFDIR=/home/ap/eccs/mysql_conf -DMYSQL_UNIX_ADDR=/home/ap/eccs/mysql_conf/mysql.sock
make
make install
```

### 安装数据库、启动数据库
编译安装完成后，需要首先安装数据库，执行以下命令。
```bash
chmod +x scripts/mysql_install_db
./scripts/mysql_install_db --basedir=/home/ap/eccs/mysql --datadir=/home/ap/eccs/mysql_data --user=eccs
```
到这一步就可以启动Mysql了，但是还没有指定配置文件的位置，如果没有指定配置文件，mysql按照以下数据自动从路径中查找配置文件，分别是：

* /etc/my.cnf
* /etc/mysql/my.cnf
* /usr/local/mysql/etc/my.cnf
* ~/.my.cnf
如果不知道默认的位置，可以使用下面的命来查找([青云](https://www.cnblogs.com/cyun/p/5553667.html))
```bash
[eccs@bj1eccap02 ~]$ /home/ap/eccs/mysql/bin/mysqld --verbose --help | grep -A 1 'Default options'
Default options are read from the following files in the given order:
/etc/my.cnf /etc/mysql/my.cnf /home/ap/eccs/mysql_conf/my.cnf ~/.my.cnf
```
明确了配置文件的位置，将配置文件按我们的要求修改，然后就可以启动数据库了。
```bash
/home/ap/eccs/mysql/support-files/mysql.server start
Starting MySQL                                             [  OK  ]
[eccs@bj1eccap02 ~]$ ps -ef | grep mysql
eccs       717     1  0 19:23 pts/0    00:00:00 /bin/sh /home/ap/eccs/mysql/bin/mysqld_safe --datadir=/home/ap/eccs/mysql_data --pid-file=/home/ap/eccs/mysql_conf/mysqld.pid
eccs      1081   717  0 19:23 pts/0    00:00:00 /home/ap/eccs/mysql/bin/mysqld --basedir=/home/ap/eccs/mysql --datadir=/home/ap/eccs/mysql_data --plugin-dir=/home/ap/eccs/mysql/lib/plugin --log-error=/home/ap/eccs/mysql_data/mysql_error.log --pid-file=/home/ap/eccs/mysql_conf/mysqld.pid --socket=/home/ap/eccs/mysql_conf/mysql.sock --port=33065
eccs      1498  1801  0 19:26 pts/0    00:00:00 grep mysql
```

### 设置口令
默认安装的mysql口令为空，需要手工设置。
```bash
/home/ap/eccs/mysql/bin/mysqladmin -u root password '123456'
```

### 遇到的问题
前面几次安装的时候，到启动服务器的环节，都会报错，提示找不到pid之类的问题，总结下来发现就是因为默认 my.cnf 的问题。
> To avoid a possible security hole where a user adds a --user=root option to a my.cnf file (thus causing the server to run as root), mysqld uses only the first --user option specified and produces a warning if there are multiple --useroptions. Options in /etc/my.cnf and $MYSQL_HOME/my.cnf are processed before command-line options, so it is recommended that you put a --user option in /etc/my.cnf and specify a value other than root. The option in/etc/my.cnf is found before any other --user options, which ensures that the server runs as a user other than root, and that a warning results if any other --user option is found.

如上所述，默认Mysql启动的时候，会从 /etc/my.cnf 或者安装目录的根目录下寻找my.cnf配置文件，如果发现就会使用配置文件中的参数而忽略命令行中的参数，这个特性是困扰很多人或造成问题的根源。我发现在 Redhat 6.7 中，即使我仅仅安装 Basic Server 选项，在没有安装 Mysql 的情况下，系统中也存在 /etc/my.cnf 文件，此时查询了系统的RPM包，发现存在这样一个包。
```bash
[root@bj1eccap01 ~]# rpm -qa | grep -i mysql
mysql-libs-5.1.73-5.el6_6.x86_64
```
删掉 /etc/my.cnf 之后，就一切正常了。

### 设置随系统自启动
让应用随系统自启动一般有两种方法，一种是放到rc.local下面，一种是放在/etc/init.d下面作为系统服务，两种方法都是 root 用户执行的，这里列出第二种方法。
```bash
[root@bj1eccap02 ~]# cp /home/ap/eccs/mysql/support-files/mysql.server /etc/init.d/mysqld
[root@bj1eccap02 ~]# chkconfig --add mysqld
[root@bj1eccap02 ~]# chkconfig --list mysqld
mysqld             0:off    1:off    2:on    3:on    4:on    5:on    6:off
[eccs@bj1eccap02 ~]$ service mysqld start
Starting MySQL..                                           [  OK  ]
```
以后就可以使用 service 命令起停了。

参考资料：
1、[installing standar source](https://dev.mysql.com/doc/refman/5.5/en/installing-source-distribution.html)
2、[source configuration options](https://dev.mysql.com/doc/refman/5.5/en/source-configuration-options.html)
3、
4、[Ignoring user change](https://dev.mysql.com/doc/refman/5.5/en/server-options.html#option_mysqld_basedir)
5、[脚本中实现切换用户并](http://www.jb51.net/article/59255.htm)
6、[在Linux中，开机自动运行普通用户的脚本程序](http://blog.csdn.net/sinboy/article/details/2466225)











