---
title: MySQL常用命令
date: 2021-02-08 14:33:38
tags:
    - MySQL
categories:
    - 数据库
    - MySQL
fileName: mysql-command
---

### 查看数据库版本信息

```sql
# 查看数据库版本
mysql --version
mysql -V
```

### 登录数据库

```sql
# 登录数据库
mysql -u 用户名 -p 密码
```

### 查看、操作数据库

```sql
# 查看现有的数据库
show databases; 

# 创建叫 ziye 的数据库
create database ziye;

# 使用 ziye 数据库
use ziye;

# 删除 ziye 数据库
drop database ziye;

# 运行对应sql语句
source D:\ziye.sql
```

### 使用 select 获取相关信息

```sql
# 查看当前使用的数据库
select database();

# 常看数据库版本
select version();
```

### 查看表

```sql
# 查看当前库中的表
show tables;

# 查看名为 ziye 的数据库中的表
show tables from ziye;
```

### 查看表结构

```sql
# 查看 dept 表的表结构
desc dept;

# 查看 dept 表的创建语句
show create table dept;
```

### 退出 mysql

键入以下命令可退出数据库：`\q`、`quit` 、`exit`

