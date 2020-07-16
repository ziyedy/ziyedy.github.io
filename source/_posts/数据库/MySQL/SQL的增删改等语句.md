---
title: SQL的增删改等语句
date: 2020-06-07 09:44:15
tags:
	- 数据库
categories:
	- 数据库
	- MySQL
fileName: mysql1
---



逻辑库

```
CREATE DATABASE; 逻辑库名称
SHOW DATABASES;
DROP DATABASE 逻辑库名称;
USE 逻辑库名称;
```

数据表

```
CREATE TABLE 数据表(
			列名 数据类型 [约束] [COMMENT 注释],
			列名 数据类型 [约束] [COMMENT 注释],
			)[COMMENT 注释];
```

```
SHOW 表名;
DESC 表名; // 查看表的情况
SHOW CREATE TABLE 表名;	// 查看创建该表时的SQL语句
DROP TABLEE 表名;	// 删除数据表
```



添加字段

```
ALTER TABLE 表名
ADD 列名 数据类型 [约束] [COMMENT 注释],
...
ADD 列名 数据类型 [约束] [COMMENT 注释];
```

修改字段数据类型等信息

```
ALTER TABLE 表名
MODIFY 列名 数据类型 [约束] [COMMENT 注释],
...
MODIFY 列名 数据类型 [约束] [COMMENT 注释];
```

修改字段名称

```
ALTER TABLE 表名
CHANGE 列名 新列名 数据类型 [约束] [COMMENT 注释],
...
CHANGE 列名 新列名 数据类型 [约束] [COMMENT 注释];
```

删除字段

```
ALTER TABLE 表名
DROP 字段名称,
...
DROP 字段名称;
```



添加索引

```
CREATE TABLE *(
		INDEX 索引名称 (字段名称),
		);
```

添加、显示、删除索引

```
CREATE INDEX 索引名称 ON 表名(字段);
ALTER TABLE 表名 ADD INDEX [索引名](字段);

SHOW INDEX FROM 表名;
DROP INDEX 索引名称 ON 表名;
```





向数据表中写入记录

```
INSERT INTO 表名(字段1,字段2......)
VALUES(值1,值2......);
```



```
// 当插入数据有问题时直接忽略
NSERT IGNORE INTO
```



修改表的记录

```
UPDATE [IGNORE] 表名
SET 字段1=值1, 字段2=值2 ......
WHERE
ORDER BY
LIMIT
```



删除数据

```
DELETE [IGNORE] FROM 表名
[WHERE]
[ORDER BY]
[LIMIT]
```



