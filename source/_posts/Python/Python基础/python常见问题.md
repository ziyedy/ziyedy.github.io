---
title: python常见问题
date: 2020-06-26 17:29:13
tags:
	- Python
categories:
	- Python
	- Python基础
fileName: python-common-method
---

## 项目常用

### 生成requirements.txt

使用`pipreqs`生成requirements

```
# 安装
pip install pipreqs
# 在当前目录生成（若存在即覆盖）
pipreqs . --encoding=utf8 --force
```

使用requirement.txt安装依赖：

```\
pip install -r requirements.txt
```



## pythonic的编码方式

### 变量的交换

```
a, b = b, a
```



### 利用百分号语法进行字符串格式化

```
a = 'Curry'
b = 'Player'
print("%s is the best %s" % (a,b))
#################
Curry is the best Player
```



### 将两个字典合并（解包操作）

```
a = {}
b = {}
c = {**a, **b}	# 解包
```

