---
title: MYSQL在众多表中查询表名和字段名
date: 2009-12-04 16:59:01
tag: 
---

在Mysql 众多表中查找一个表名或者字段名的 SQL 语句：
SELECT table_name, column_name from information_schema.columns WHERE column_name LIKE 'Name';
下面两种方法也可以查到：
SELECT column_name from information_schema.columns WHERE column_name LIKE ’%searchTerm%’ AND table_schema = ‘yourDB’
SELECT column_name from information_schema.columns WHERE column_name LIKE ’%searchTerm%’ AND table_schema = ‘yourDB’ AND table_name = ‘yourDBTable’
这样，我们在面多突然出现的那么多表时，就不会茫然失措了。












