---
title: MyBatis基本使用方法
date: 2020-12-22 15:59:21
tags:
	- 数据库
categories:
	- Java
	- 框架
fileName: mybatis-basic-summary
---

#

JDBC需要的主要步骤：

```java
// 注册数据库驱动
Class.forName("com.mysql.jdbc.Driver");
// 创建连接对象
Connection conn = DriverManager.getConnection(url, username, password);
// 创建 Statement, 用来执行 sql 语句
Statement stmt = conn.createStatement();

```



