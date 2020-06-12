title: post
date: 2020-06-10 17:30:10
tags:
categories:
fileName:

12

```
SELECT * FROM 数据表
```

使用别名

```
SELECT
	字段名 AS 别名
FROM 数据表
```



查询语句的执行顺序：

先进行语法分析和优化，找到关键字优先级，先执行FROM，再执行SELECT



```
SELECT * FROM * LIMIT 起始位置, 偏移量;
// 查找t_gg表中gg字段的前20条消息
SELECCT gg FROM t_gg 0, 20;
```



对查询的结果进行排序

```
// 按照相关信息进行排序
SELECT * FROM * ORDER BY 列名 [ASC|DESC]
// 多个排序信息
SELECT * FROM * ORDER BY 列名 [ASC|DESC],列名 [ASC|DESC]
```



去除结果中的重复记录，在字段名称前加上DISTINCT字段，只支持一个字段名。

```
SELECT DISTINCT 字段名 FROM *;
```

条件查询

```
SELECT * FROM * WHERE 条件1 [AND|OR] 条件2 ......;
```



聚合函数

也就是平均值、最大值等函数，以下为求该字段名下的平均值，其他函数使用方法与此类似。

```
SELECT AVG(字段名) FROM 表名
```

COUNT(*)用于获得包含空值的记录数，COUNT(列名)用于获取包含非空值的记录数。



分组查询，使用`GROUP BY`字句。即通过一定规则将数据集分为若干个部分进行查询处理。以下即为使用字段1进行分组统计每一组中字段2的平均值。

```
SELECT 字段1,AVG(字段2) FROM 表名 GROUP BY 字段1;
```

使用分组查询SELECT语句中只能出现用以分组的字段以及其他字段的聚合函数，否则会报错。为解决这个问题，可以使用`GROUP_CONCAT`函数，这个函数可以将符合条件的值连接起来进行输出。

```
SELECT 字段1,GROUP_CONCAT(字段2) FROM 表名 GROUP BY 字段1;
```



同时可以加上`WITH ROLLUP`再进行一次汇总运算。

```
SELECT 字段1,AVG(字段2) FROM 表名 GROUP BY 字段1 WITH ROLLUP;
```



 

由于WHERE子句执行在GROUP BY子句之前，因此如果WHERE子句中出现了如平均值等信息时SQL会不知道在哪个范围内进行平均，因此会出错，如以下代码即为错误的（聚合函数作为判断条件）。所以需要使用`HAVING`子句。

```
SELECT f1
FROM table
WHERE AVG(f2) > 10
GROUP BY f1;
```

```
SELECT f1
FROM table
GROUP BY f1 HAVING AVG(f2) > 10;
```





表的连接

内连接：结果集中只保留符合连接条件的记录

```
SELECT * FROM 表1 [INNER] JOIN 表2 ON 连接条件;

SELECT * FROM 表1 [INNER] JOIN 表2 WHERE 连接条件;

SELECT * FROM 表1, 表2 WHERE 连接条件;
```



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



外连接：不管是否符合连接条件，记录都要保留在结果集里

```
// 右表出NULL值和左表连接
LEFT JOIN 
```

