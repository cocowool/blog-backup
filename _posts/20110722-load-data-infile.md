---
title: Load Data Infile批量导入数据
date: 2011-07-22 10:31:01
tag: 
---

mysql提供了一个  load data infile xxx into table table_name  的方法来帮助批量的导入数据。这个操作是 select * out to file 操作的逆操作。


大体的使用方式是：
shell>mysql -hxx -uxx -pxx database
mysql>set names utf8;
mysql>load data infile '/path_to_file/xx.csv' into table xx ;


但是在遇到文件不在mysql目录中时，会发生 参考资料1 中的错误，修正的办法是使用 local 参数。具体问题是：


出于安全原因，当读取位于服务器中的文本文件时，文件必须位于数据库目录中，或者是全体可读的。另外，要对服务器文件使用LOAD DATA INFILE，您必须拥有FILE权限。
如果指定了LOCAL，则文件会被客户主机上的客户端读取，并被发送到服务器。文件会被给予一个完整的路径名称，以指定确切的位置。


例如：
load data local infile '/Users/shiqiang/Desktop/school.csv' into table i_school FIELDS TERMINATED BY ',' LINES TERMINATED BY '\r';


参考资料：
1、[Mysql Error 13](http://www.cublog.cn/u/7040/showart_2062246.html)
2、[Mysql手册 Load Data Infile](http://dev.mysql.com/doc/refman/5.1/zh/sql-syntax.html#load-data)













