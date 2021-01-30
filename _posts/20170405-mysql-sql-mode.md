---
title: Mysql运行模式及1690错误处理
date: 2017-04-05 16:28:01
tag: 
keywords: mysql, mysql运行模式, mysql 1690错误
description: 经过查询，发现这个错误的原因是两个时间字段进行**减法**运算时，如果有一个时间为**0000-00-00**时造成的，根本原因是因为这样减法的结果会超过Mysql数值字段的范围，从而触发1690报错。
---

最近一段运行良好的代码突然无法运行，报错：

```sh
MySQL said: Documentation

# 1690 - BIGINT UNSIGNED value is out of range in
```

经过查询，发现这个错误的原因是两个时间字段进行**减法**运算时，如果有一个时间为**0000-00-00**时造成的，根本原因是因为这样减法的结果会超过Mysql数值字段的范围，从而触发1690报错。

## ERROR 1690 Out-of-Range
当Mysql中的数字字段存储了一个超过允许范围的数字时，会触发1690 Out of Range错误，是否触发错误取决于SQL运行时的模式：
> 当标准 Standar Mode 或 Strict Mode 运行时，数据插入会失败
当非限制模式 No Restrictive 运行时，Mysql将数值转化为范围允许内的最大或最小值进行存储


## 解决方法
```sql
SET sql_mode = 'NO_UNSIGNED_SUBTRACTION';
```
在进行计算时，首先执行上面的语句，可以避免减法运算过程中的错误。在Mysql文档中，明确指出两个整数进行相减运算的结果是一个无符号数，在Mysql 5.5.5 之前，如果产生一个负数，mysql会将这个数转换为一个最大的数值。
```mysql
mysql> SET sql_mode = '';
mysql> SELECT CAST(0 AS UNSIGNED) - 1;
+-------------------------+
| CAST(0 AS UNSIGNED) - 1 |
+-------------------------+
|    18446744073709551615 |
+-------------------------+
```
自Mysql 5.5.5 之后，如果产生一个负数，则会产生一个错误 ERROR 1690。
```mysql
mysql> SET sql_mode = '';
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT CAST(0 AS UNSIGNED) - 1;
ERROR 1690 (22003): BIGINT UNSIGNED value is out of range in '(cast(0 as unsigned) - 1)'
```

## 关于SQL_MODE
SQL_MODE默认为空，有很多的选项，建议在生产环境中设置成严格的MODE，这样可以在运维期间避免很多麻烦。

参考资料
1、[Server SQL Modes](https://dev.mysql.com/doc/refman/5.5/en/sql-mode.html)
2、[Out of Range and Overflow Handling](https://dev.mysql.com/doc/refman/5.5/en/out-of-range-and-overflow.html)
3、[Mysql SQL_MODE](http://www.cnblogs.com/zengkefu/p/5636614.html)