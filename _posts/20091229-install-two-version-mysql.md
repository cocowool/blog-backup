---
title: Centos 下同时安装 Mysql4 和 Mysql5，二进制方式
date: 2009-12-29 17:30:01
tag: 
keywords: mysql, centos, linux
description: 系统中原有 Mysql4 ，但是需要使用 Mysql5 的一些新特性，但是 Mysql4 又不能够删除，所以需要同时安装两个版本的 Mysql。
---

系统中原有 Mysql4 ，但是需要使用 Mysql5 的一些新特性，但是 Mysql4 又不能够删除，所以需要同时安装两个版本的 Mysql。

> 如果哪个年代有Docker，就不用这么麻烦了，穿梭于2021-1-18日留下此评论。对于Docker，可以从[Docker入门介绍](http://www.edulinks.cn/2018/06/20/20180620-docker-overview/) 入手

为此，用测试机进行了实验，安装下面两个版本的 Mysql。
* mysql-standard-4.1.15-pc-linux-gnu-i686-glibc23.tar.gz
* mysql-5.0.86-linux-i686.tar.gz

步骤如下：
1、首先安装 Mysql4
> tar –xvzf mysql-standard-4.1.15-pc-linux-gnu-i686-glibc23.tar.gz
cp -rf mysql-standard-4.1.15-pc-linux-gnu-i686-glibc23.tar.gz /usr/local/mysql4
groupadd mysql4
useradd -n mysql4 -c "" -g mysql4 -d /nonexistent -s /usr/sbin/nologin
cd /usr/local/mysql4
chown –R mysql4
chgrp –R mysql4
./scripts/mysql_install_db --user=mysql4


> chown –R root .
chown –R mysql4 data
./bin/mysqld_safe --user=mysql4 &

这样，mysql4 就安装完成了，可以通过 ./bin/mysql –uroot 进入查看，并且通过 status 可以看到数据库的状态：
```sh
> mysql> status
--------------
./bin/mysql  Ver 14.7 Distrib 4.1.15, for pc-linux-gnu (i686) using readline 4.3
Connection id:          2
Current database:
Current user:           root@localhost
SSL:                    Not in use
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         4.1.15-standard
Protocol version:       10
Connection:             Localhost via UNIX socket
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    latin1
Conn.  characterset:    latin1
UNIX socket:            /tmp/mysql.sock
Uptime:                 2 min 32 sec
Threads: 1  Questions: 5  Slow queries: 0  Opens: 11  Flush tables: 1  Open tables: 5  Queries per second avg: 0.033
--------------
```
如果需要加入到系统自启动中，则可以进行如下的操作。

cp support-files/mysql.server /etc/rc.d/init.d/mysql4.server
接下来需要对这个文件中的一些变量进行修改
```mysql
basedir=/usr/local/mysql4
datadir=/usr/local/mysql4/data
pid_file=/var/run/mysql4/mysql4.pid
```
拷贝一个配置文件到 data 目录下```cp /usr/local/mysql4/support-files/my-xxxx.cnf /usr/local/mysql4/data/my.cnf```

修改 my.cnf 文件
```mysql
> [mysqld]
user            = mysql4
port            = 3306
socket          = /tmp/mysql4.sock
```

最后，建立运行时的目录
```sh
mkdir /var/run/mysql4
chown -R mysql4:mysql4 /var/run/mysql4/
ln -s /etc/init.d/mysql4.server /etc/rc3.d/S90mysql4
ln -s /etc/init.d/mysql4.server /etc/rc3.d/K91mysql4
```
这样，系统启动后 mysql4 的服务就会自动起来了。

2、安装 Mysql 5

mysql5 的安装和 mysql4 差不多，只是 mysql4 的部分修改为 mysql5。
```sh
groupadd mysql5
useradd –n mysql5 –c “” –g mysql5 –d /noexistent –s /usr/sbin/nologin
chown –R mysql5 .
chgrp –R mysql5 .
./scripts/mysql_install_db --user=mysql5
chown –R root .
chown –R mysql5 data
cp support-files/mysql.server /etc/rc.d/init.d/mysql5.server
```

修改文件中的变量
```mysql
basedir=/usr/local/mysql5
datadir=/usr/local/mysql5/data
pid_file=/var/run/mysql5/mysql5.pid
server_pid_file=/var/run/mysql5/mysql5.pid
user=mysql5
```

拷贝一个配置文件 cp support-files/my-medium.cnf my.cnf
修改配置文件中的变量
```mysql
user            = mysql5
port            = 3307
socket          = /tmp/mysql5.sock
```

最后建立文件夹
```sh
mkdir /var/run/mysql5
chown -R mysql5:mysql5 /var/run/mysql5/
```

启动服务 /etc/rc.d/init.d/mysql5.server start
查看服务的状态
```sh
[root@fltrpsrv2 mysql5]# /usr/local/mysql5/bin/mysql -uroot -S/tmp/mysql5.sock
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.0.86-log MySQL Community Server (GPL)
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql> status
--------------
/usr/local/mysql5/bin/mysql  Ver 14.12 Distrib 5.0.86, for pc-linux-gnu (i686) using readline 5.1
Connection id:          1
Current database:
Current user:           root@localhost
SSL:                    Not in use
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         5.0.86-log MySQL Community Server (GPL)
Protocol version:       10
Connection:             Localhost via UNIX socket
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    latin1
Conn.  characterset:    latin1
UNIX socket:            /tmp/mysql5.sock
Uptime:                 28 sec
Threads: 1  Questions: 4  Slow queries: 0  Opens: 11  Flush tables: 1  Open tables: 6  Queries per second avg: 0.143
--------------
```

可以看到 mysql5 已经安装成功了，加入系统自启动。
```sh
ln -s /etc/init.d/mysql5.server /etc/rc3.d/S90mysql5
ln -s /etc/init.d/mysql5.server /etc/rc3.d/K91mysql5
```

参考资料：
1、[Install Mysql4 and Mysql5 on a single FreeBSD 6.2 Server](http://blog.opusinteractive.com/managed-services/installing-mysql4-and-mysql5-on-a-single-freebsd-62-server/)
2、[Install Mysql from tar.gz Packages on Other Unix-Like Systems](http://dev.mysql.com/doc/refman/4.1/en/installing-binary.html)












