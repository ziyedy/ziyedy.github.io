---
title: SQL语句——查
date: 2020-06-10 17:30:10
tags:
	- 数据库
categories:
	- 计算机基础
	- 数据库
fileName: mysql2
---

### SELECT语句

1、基本使用框架

```
SELECT * FROM 数据表
[WHERE]
[LIMIT]
[OFFSET]
```

2、使用别名

```
SELECT
	字段名 AS 别名
FROM 数据表
```

3、查询语句的执行顺序：

先进行语法分析和优化，找到关键字优先级，先执行FROM，再执行SELECT





### 查询相关功能

1、**ORDER BY**子句功能，对查询的结果进行**排序**，示例如下

```
// 按照相关信息进行排序
SELECT * FROM * ORDER BY 列名 [ASC|DESC]
// 多个排序信息
SELECT * FROM * ORDER BY 列名 [ASC|DESC],列名 [ASC|DESC]
```



2、去除结果中的**重复记录**，在字段名称前加上**DISTINCT**字段，只支持一个字段名。

```
SELECT DISTINCT 字段名 FROM *;
```



3、**条件查询**，使用**WHERE**语句指定查询的条件逻辑，中间使用AND|OR等逻辑连接符连接，同时注意判断相等的条件是“=”而不是“==”。

```
SELECT * FROM * WHERE 条件1 [AND|OR] 条件2 ......;
```



4、使用**聚合函数**。所谓聚合函数，也就是平均值、最大值等函数，以下为求该字段名下的平均值函数（AVG），其他函数使用方法与此类似。该类方法在实际使用时可以将统计结果作为一个子表进行查询。

```
SELECT AVG(字段名) FROM 表名
```

**COUNT(*)**用于获得包含空值的记录数，**COUNT(字段名)**用于获取包含非空值的记录数。



5、**分组查询**，使用**GROUP BY**字句。即通过一定规则将数据集分为若干个部分进行查询处理。以下即为使用字段1进行分组统计每一组中字段2的平均值。

```
SELECT 字段1,AVG(字段2) FROM 表名 GROUP BY 字段1;
```

使用分组查询SELECT语句中只能出现用以分组的字段以及其他字段的聚合函数，否则会报错（由于产生了**一对多**的关系）。为解决这个问题，可以使用`GROUP_CONCAT`函数，这个函数可以将符合条件的值连接起来进行输出，可以理解为将“多”的部分进行了字符串拼接。

```
SELECT 字段1,GROUP_CONCAT(字段2) FROM 表名 GROUP BY 字段1;
```

同时可以加上`WITH ROLLUP`再进行一次汇总运算（即在返回的查询结果之下再加上一条汇总记录）。

```
SELECT 字段1,AVG(字段2) FROM 表名 GROUP BY 字段1 WITH ROLLUP;
```



6、**HAVING**子句，以及为何要使用HAVING子句

由于WHERE子句执行在GROUP BY子句之前，因此如果WHERE子句中出现了如平均值等信息时SQL会不知道在哪个范围内进行平均，因此会出错，如以下代码即为错误的（聚合函数作为判断条件）。所以需要使用`HAVING`子句。

```
# 使用错误
SELECT f1
FROM table
WHERE AVG(f2) > 10
GROUP BY f1;
```

```
# 使用正确
SELECT f1
FROM table
GROUP BY f1 HAVING AVG(f2) > 10;
```





### 表的连接

表的连接很重要，具体有内外两种连接构成。

1、**内连接**：结果集中只保留符合连接条件的记录

```
SELECT * FROM 表1 [INNER] JOIN 表2 ON 连接条件;

SELECT * FROM 表1 [INNER] JOIN 表2 WHERE 连接条件;

SELECT * FROM 表1, 表2 WHERE 连接条件;
```



2、**外连接**：不管是否符合连接条件，记录都要保留在结果集里

```
// 右表出NULL值和左表连接
LEFT JOIN 
```



3、**内外连接的区别**。具体如图所示（图片摘自LeetCode）

1）左联结（left join），联结结果保留左表的全部数据
2）右联结（right join），联结结果保留右表的全部数据
3）内联结（inner join），取两表的公共数据

![](\SQL语句——查\sql连接.png)

应用在[175.组合两个表中](https://leetcode-cn.com/problems/combine-two-tables/)。该题需要满足“无论 person 是否有地址信息，都需要基于上述两表提供 person 的以下信息”，所以当person表在左时采用左连接。



### 一些注意事项

1、**表连接时on和where的区别**





以下不推荐，因为查询速度会很慢，每一次都会执行WHERE语句中的内容。

```
SELECT ename 
FROM t_emp
WHERE deptno = (SELECT deptno FROM t_emp WHERE ename="SCOTT")
```



```
SELECT e2.ename
FROM t_emp e1 JOIN t_emp e2 ON e1.deptno=e2.deptno
WHERE e1.ename = "SCOTT"
```



将AVG的结果视为一个表，与原表进行连接。

错误

```
SELECT e2.ename
FROM t_emp e1 JOIN t_emp e2 ON e2.sal >= AVG(e1.sal)
```

正确

```
SELECT e.ename
FROM t_emp e 
JOIN (SELECT AVG(sal) avg FROM t_emp) t
ON e.sal >= t.avg;
```

