---
title: 工作中常用到的SQL语句
date: 2021-05-06 08:40:14
tags:
keywords: sql, 常用sql
description: 总结了几条工作中经常用到的SQL，欢迎大家补充。
---

1、修改已有的列名。
```sql
ALTER TABLE s_project CHANGE p_owner p_name varchar(250) default '';

-- 修改已有列的顺序也可以用这个方法
alter table s_domain modify log_path varchar(250) default '' after status;
```

2、修改字符集。Mysql的数据库、表、列都可以单独定义字符集，所以如果字符集定义不一致，会给程序带来很多问题。
```sql
-- 修改数据库的字符集 
alter database mydb character set utf-8;
-- 修改数据表的字符集 
alter table mytable character set utf-8;
```

3、增加主键索引

```sql
Alter table table_name add primary key (`column`);
```

4、增加唯一索引

```sql
Alter table table_name add uniuqe(`column`);
```

5、增加普通索引

```sql
Alter table table_name add index index_name(`column`);
```

6、增加全文索引
```sql
Alter table table_name add fulltext (`column`);
-- 注意应当使用反引号，而不是单引号
```

7、修改列为自增长
```sql
alter table think_score modify id int(11) auto_increment;
```

8、删除列

```sql
alter table table_name drop column columnname;
```

9、清理binlog日志
```sql
purge master logs  before '2010-12-31';
```

10、修改表的编码方式
```sql
alter table type character set utf8;
```

11、批量清空多个表的内容

这个问题的解决思路在于从数据库的information_schema中找到所有表的名字，然后构造清空操作进行清空
```sql
select concat('truncate table ', table_name, ';') from information_schema.tables where table_schema='Database name';
```

## 参考资料：
1、修改及查看Mysql数据库的字符集
2、Mysql批量删除多个表