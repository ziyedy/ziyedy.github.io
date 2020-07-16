---
title: pythonic的编码方式
date: 2020-06-30 10:23:28
tags:
categories:
	- Python
	- 语言基础
fileName: pythonic
---

### 变量的交换

```
a, b = b, a
```

### 利用百分号语法进行字符串格式化



```
a = Curry
b = Player
print("%s is the best %s" % (a,b))
```



enumerate() 提供列表的索引值和数值



将两个字典合并

```
a = {}
b = {}
c = {**a, **b}	# 解包
```

