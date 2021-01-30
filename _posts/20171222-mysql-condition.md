---
title: Mysql中的条件语句if、case
date: 2017-12-22 11:17:01
tag: 
keywords: mysql, mysql if case
description: Mysql中的条件语句在我们对数据进行转换的时候比较有用，这样就不需要创建中转表。
---

Mysql中的条件语句在我们对数据进行转换的时候比较有用，这样就不需要创建中转表。

### IF 函数
```sql
IF(expr1,expr2,expr3)
```
如果 expr1 是TRUE (expr1 <> 0 and expr1 <> NULL)，则 IF()的返回值为expr2; 否则返回值则为 expr3。IF() 的返回值为数字值或字符串值。
```sql
select *,if(sva=1,"男","女") as ssva from taname where sva != ""
```

### IF ELSE 流程控制语句
在mysql存储过程中的用法：
```sql
IF search_condition THEN statement_list 
    [ELSEIF search_condition THEN statement_list] ... 
    [ELSE statement_list]
END IF
```
IF作为一条语句，在END IF后需要加上分号“;”以表示语句结束，其他语句如CASE、LOOP等也是相同的。

### CASE 表达式
```sql
select CASE sva WHEN 1 THEN '男' ELSE '女' END as ssva from taname where sva != ''
```

### Swap Salary
Leetcode中有一道题目就是根据条件来转换数据的，就需要用条件控制语句来实现。
```sql
UPDATE salary SET sex = IF(sex = 'm', 'f', 'm')
```
也可以利用条件语句，在搜索的时候，直接进行数据转换
```sql
select *,(CASE WHEN sex='1' THEN '男' WHEN sex='0' THEN '女' ELSE '保密' END) as sex_text from user
```

参考资料：
1、[Mysql if case总结](http://outofmemory.cn/code-snippet/1149/MySQL-if-case-statement-usage-summary)
2、[Leetcode swap salary](https://leetcode.com/problems/swap-salary/description/)
3、[select case when if 的一些用法](https://www.cnblogs.com/martinzhang/p/3220595.html)
4、[IF Syntax](https://dev.mysql.com/doc/refman/5.7/en/if.html)/