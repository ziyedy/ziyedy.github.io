---
title: Pandas库基本用法
date: 2020-08-22 11:38:29
tags:
categories:
	- Python
	- 常用模块
fileName: pandas-basic-use
---

## Pandas基础

Pandas是Python第三方库，提供高性能易用数据类型和分析工具，其基于NumPy实现，常与NumPy和Matplotlib一同使用.

器有两个主要数据类型：**Series, DataFrame**

基于上述数据类型的各类操作
基本操作、运算操作、特征类操作、关联类操作

| NumPy              | Pandas             |
| ------------------ | ------------------ |
| 基础数据类型       | 扩展数据类型       |
| 关注数据的结构表达 | 关注数据的应用表达 |
| 维度：数据间关系   | 数据与索引间关系   |



## Series类型

Series类型由一组数据及与之相关的数据索引组成（类似python的enumerate）

如下所示，Series为列表**自动创建了索引**

```
>>> import pandas as pd
>>> p = pd.Series([4,3,2,1])
>>> p
0    4
1    3
2    2
3    1
dtype: int64
```

也可以使用index参数**手动指定索引**（index可省略）

```
>>> import pandas as pd
>>> a = pd.Series([1,2,3],['a','b','c'])
>>> a
a    1
b    2
c    3
dtype: int64
```

### Series类型的创建

> * Python列表，index与列表元素个数一致
>
> * 标量值，index表达Series类型的尺寸
> * Python字典，键值对中的“键”是索引，index从字典中进行选择操作
> * ndarray，索引和数据都可以通过ndarray类型创建
>
> * 其他函数，range()函数等

### Series类型的操作

#### 获得索引与数据

```
>>> a.index
Index(['a', 'b', 'c'], dtype='object')
>>> a.values
array([1, 2, 3], dtype=int64)
```

#### 其他操作

**其他操作基本与numpy和Python中自带的字典一致，包括切片等功能，其中自定义索引与自动索引并存，但不能混用**



## DataFrame类型

**DataFrame类型由共用相同索引的一组列组成（一种表格型的数据类型）**





### 参考

MOOC上北理工相关课程PPT