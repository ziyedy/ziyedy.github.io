---
title: 数据操纵语言（DML）
date: 2020-06-07 09:44:15
tags:
	- SQL语言
categories:
	- 数据库
	- SQL
fileName: DML
---



逻辑库

```
CREATE DATABASE; 逻辑库名称
SHOW DATABASES;
DROP DATABASE 逻辑库名称;
USE 逻辑库名称;
```

数据表

```sql
CREATE TABLE [IF NOT EXISTS] 数据表(
			列名 数据类型 [约束] [COMMENT 注释],
			列名 数据类型 [约束] [COMMENT 注释],
			)[COMMENT 注释];
```



```sql
SHOW 表名;
DESC 表名; // 查看表的情况
SHOW CREATE TABLE 表名;	// 查看创建该表时的SQL语句
DROP TABLEE 表名;	// 删除数据表
```



添加字段

```sql
ALTER TABLE 表名
ADD 列名 数据类型 [约束] [COMMENT 注释],
...
ADD 列名 数据类型 [约束] [COMMENT 注释];
```

修改字段数据类型等信息

```sql
ALTER TABLE 表名
MODIFY 列名 数据类型 [约束] [COMMENT 注释],
...
MODIFY 列名 数据类型 [约束] [COMMENT 注释];
```

修改字段名称

```sql
ALTER TABLE 表名
CHANGE 列名 新列名 数据类型 [约束] [COMMENT 注释],
...
CHANGE 列名 新列名 数据类型 [约束] [COMMENT 注释];
```

删除字段

```sql
ALTER TABLE 表名
DROP 字段名称,
...
DROP 字段名称;
```



添加索引

```sql
CREATE TABLE *(
		INDEX 索引名称 (字段名称),
		);

CREATE INDEX 索引名称 ON 表名(字段);
ALTER TABLE 表名 ADD INDEX [索引名](字段);
```

显示、删除索引

```sql
SHOW INDEX FROM 表名;

DROP INDEX 索引名称 ON 表名;
ALTER TABLE tbl_name DROP INDEX index_name；
```



1. 添加**主键**

   `ALTER TABLE tbl_name ADD PRIMARY KEY (col_list);``// 该语句添加一个主键，这意味着索引值必须是唯一的，且不能为NULL。`

2. 添加**唯一索引**

   `ALTER TABLE tbl_name ADD UNIQUE index_name (col_list);``// 这条语句创建索引的值必须是唯一的。`

3. 添加**普通索引**

   `ALTER TABLE tbl_name ADD INDEX index_name (col_list);``// 添加普通索引，索引值可出现多次。`

4. 添加**全文索引**

   `ALTER TABLE tbl_name ADD FULLTEXT index_name (col_list);``// 该语句指定了索引为 FULLTEXT ，用于全文索引。`



## 添加数据 INSERT

向数据表中写入记录

```sql
INSERT INTO 表名(字段1,字段2......)
VALUES(值1,值2......);
```



```sql
// 当插入数据有问题时直接忽略
INSERT IGNORE INTO
```



## 修改数据 UPDATE

修改表的记录

```sql
UPDATE [IGNORE] 表名
SET 字段1=值1, 字段2=值2 ......
WHERE
ORDER BY
LIMIT
```



## 删除数据 DELETE

DELETE不能与GROUP BY一起使用

```sql
DELETE [IGNORE] FROM 表名
[WHERE]
[ORDER BY]
[LIMIT]
```

### DELETE经典问题

1、删除重复数据，只保留id最小的数据

*需要将查询结果作为一个第三方表，避免同时将查询同一张表的结果作为删除的条件（不能对）*

https://blog.csdn.net/NoviceZ/article/details/119514532



