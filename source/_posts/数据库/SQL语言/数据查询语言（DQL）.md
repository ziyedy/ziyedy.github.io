---
title: 数据查询语言（DQL）
date: 2020-06-10 17:30:10
tags:
	- 数据库
	- SQL
categories:
	- 数据库
	- SQL
fileName: DQL
---

## SELECT——查询

### SELECT-FROM——基本句式（从XX表选择XX字段）

1、基本使用框架

```sql
SELECT * FROM 数据表
[WHERE]
[LIMIT]
[OFFSET]
```

2、使用别名

```sql
SELECT
	字段名 AS 别名
FROM 数据表 AS 别名
```

3、查询语句的执行顺序：

先进行语法分析和优化，找到关键字优先级，先执行FROM，再执行SELECT



### LIMIT-OFFSET——限制查询返回数量，跳过的数据条数

注意：**检索出来的第一行是行0**

同时若指定的行数不够，则返回最多能返回的行。

```sql
# 返回n条数据
LIMIT n
LIMIT 0, n
# 跳过x条数据，之后返回n条数据（两者等价）
LIMIT x, n
LIMIT n OFFSET x
```





### DISTINCT——去重

去除结果中的**重复记录**，在字段名称前加上`DISTINCT`字段，只支持一个字段名。

```sql
SELECT DISTINCT 字段名 FROM *;
```



### ORDER BY——排序

注意：**`ORDER BY`必须位于`WHERE`**之后

```sql
# 按照相关信息进行排序
SELECT * FROM * ORDER BY 列名 [ASC|DESC]
# 多个排序信息
SELECT * FROM * ORDER BY 列名 [ASC|DESC],列名 [ASC|DESC]
```



## GROUP BY——分组查询

### 聚合函数

使用**聚合函数**。所谓聚合函数，也就是平均值、最大值等函数，以下为求该字段名下的平均值函数（AVG），其他函数使用方法与此类似。该类方法在实际使用时可以将统计结果作为一个子表进行查询。

```sql
SELECT AVG(字段名) FROM 表名
```

**COUNT(*)**用于获得包含空值的记录数，**COUNT(字段名)**用于获取包含非空值的记录数。

### 分组查询

**分组查询**，使用`GROUP BY`子句。即通过一定规则将数据集分为若干个部分进行查询处理。以下即为使用字段1进行分组统计每一组中字段2的平均值。

```sql
SELECT 字段1,AVG(字段2) FROM 表名 GROUP BY 字段1;
```

使用分组查询SELECT语句中**只能出现用以分组的字段以及其他字段的聚合函数**，否则会报错（由于产生了**一对多**的关系）。为解决这个问题，可以使用`GROUP_CONCAT`函数，这个函数可以将符合条件的值连接起来进行输出，可以理解为将“多”的部分进行了字符串拼接。

```sql
SELECT 字段1,GROUP_CONCAT(字段2) FROM 表名 GROUP BY 字段1;
```

同时可以加上`WITH ROLLUP`再进行一次汇总运算（即在返回的查询结果之下再加上一条汇总记录）。

```sql
SELECT 字段1,AVG(字段2) FROM 表名 GROUP BY 字段1 WITH ROLLUP;
```



### HAVING——分组查询条件

为何要使用HAVING子句（与WHERE的区别）

**WHERE子句执行在GROUP BY子句之前**，因此如果WHERE子句中出现了如平均值等信息时SQL会不知道在哪个范围内进行平均，即报错。所以需要使用`HAVING`子句。

```sql
# 使用错误，WHERE不知道AVG作用范围
SELECT f1
FROM table
WHERE AVG(f2) > 10
GROUP BY f1;
```

```sql
# 使用正确
SELECT f1
FROM table
GROUP BY f1 HAVING AVG(f2) > 10;
```



## WHERE——条件查询

过滤数据，使用`WHERE`语句指定查询的条件逻辑，中间使用AND|OR等逻辑连接符连接

```sql
SELECT * FROM * WHERE 条件1 [AND|OR] 条件2 ......;
```

同时注意判断相等的条件是`=`而不是`==`；不等于通常写成`<>`。

除此之外还有BETWEEN，LIKE，IN等常用运算符



### 语句基本执行顺序

1、from

2、where

3、group by

4、having

5、select

6、order by

7、limit



## JOIN——表连接

表连接即把多个表通过表之间的共用字段连接起来，可以将其视为一张表进行操作。



### INNER JOIN

如果表中有至少一个匹配，则返回行。结果集中只保留符合连接条件的记录。

**使用WHERE语句连接默认也是INNER JOIN**，如下面第3条语句

```
SELECT * FROM 表1 [INNER] JOIN 表2 ON 连接条件;

SELECT * FROM 表1 [INNER] JOIN 表2 WHERE 连接条件;

SELECT * FROM 表1, 表2 WHERE 连接条件;
```



### LEFT JOIN——左连接

即使右表中没有匹配，联结结果也保留左表的全部数据。同时*右表中对应数据视为NULL*

### RIGHT JOIN——右连接

与左连接相反，联结结果保留右表的全部数据



应用在[175.组合两个表中](https://leetcode-cn.com/problems/combine-two-tables/)。该题需要满足“无论 person 是否有地址信息，都需要基于上述两表提供 person 的以下信息”，所以当person表在左时采用左连接。



### on和where的区别

1. 对`INNER JOIN`，条件放在ON和WHERE中结果集是相同的
2. 对外连接，条件放在ON中，则不管一些条件是否为真，都会返回对应表中的记录

比如在`LEFT JOIN`中写上右表的条件，则不论是否满足有些行都会返回NULL

而

> 具体参考：https://www.runoob.com/w3cnote/sql-different-on-and-where.html



**几种条件查询语句执行顺序：ON > WHERE > 聚合函数 > having**



## 子查询



以下不推荐，因为查询速度会很慢，每一次都会执行WHERE语句中的内容。

```sql
SELECT ename 
FROM t_emp
WHERE deptno = (SELECT deptno FROM t_emp WHERE ename="SCOTT")
```



```sql
SELECT e2.ename
FROM t_emp e1 JOIN t_emp e2 ON e1.deptno=e2.deptno
WHERE e1.ename = "SCOTT"
```



将AVG的结果视为一个表，与原表进行连接。

错误

```sql
SELECT e2.ename
FROM t_emp e1 JOIN t_emp e2 ON e2.sal >= AVG(e1.sal)
```

正确

```sql
SELECT e.ename
FROM t_emp e 
JOIN (SELECT AVG(sal) avg FROM t_emp) t
ON e.sal >= t.avg;
```



## 组合查询





