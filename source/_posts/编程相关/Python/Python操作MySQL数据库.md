---
title: Python操作MySQL数据库
date: 2020-06-15 15:37:28
tags:
	- Python
	- 数据库
categories:
	- 编程相关
	- Python
fileName: python-mysql-handle
---

**使用`mysql.connector`模块实现MySQL与Python的交互**

### 创建连接

代码示例如下

```
import mysql.connector

config = {
    "host": "localhost",
    "port": "3306",
    "user": "root",
    "password": "password",
    "database": "demo"
}

con = mysql.connector.connect(**config)

con.close()
```





### 游标（Cursor）

`mysql.connector`中使用游标来执行SQL语句，并且查询结果也会保存在游标中

#### 执行SQL语句

```
curser = con.cursor()
curser.execute()
```

示例

```
curser = con.cursor()
sql = "SELECT ename FROM t_emp;"
curser.execute(sql)
for one in curser:
    print(one[0])
```



事务控制

```
con.start_transaction()
con.commit()
con.rollback()
```

异常处理

```
try:
    con = mysql.connector.connect(**config)
    con.start_transaction()
    con.commit()
except Exception as e:
    con.rollback()
finally:
    if "con" in dir():
        con.close()
```



数据库连接池

```
pool = mysql.connector.pooling.MySQLConnectionPool(
    **config,
    pool_size=10
)
con = pool.get_connection()
```



### SQL注入攻击

