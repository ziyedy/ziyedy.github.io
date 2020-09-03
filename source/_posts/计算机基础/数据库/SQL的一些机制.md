---
title: SQL的一些机制
date: 2020-06-12 10:39:37
tags:
	- SQL
categories:
	- 计算机基础
	- 数据库
fileName: mysql3
---

123

事务机制

事务是一个或多个SQL语句组成的整体，要么全部执行成功，要么全部执行失败

```
START TRANSACTION
SQL语句
[COMMIT | ROLLBACK]
```





导出SQL文件

```

```



导出



SQL注入攻击：



SQL预编译机制









数据加密

对称加密（DES加密、AES加密）



AES加密函数：`AES_ENCRYPT(原始数据，密钥字符串)`

AES解密函数：`AES_DECRYPT(加密结果，密钥字符串)`



非对称加密（RSA加密、DSA加密、ECC加密）





数据库连接池（Connection Pool）：预先创建一些数据库连接，然后缓存起来，避免了程序语言反复创建和销毁连接的昂贵代价

