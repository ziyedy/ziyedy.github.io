---
title: 2、Python函数相关内容
date: 2020-05-08 15:20:53
tags:
	- python
categories:
	- Python
	- 基础知识
fileName: python2
---

### 函数相关技巧

#### 关键字传参

可以增强代码可读性，每次传参都需要指定形参名，以下就是关键字传参。

```
def func(a,b):
	pass
func(a=1,b=2)
```



Tip1： `* `代表之后所有参数传参时必须使用关键字传参（混合形式传参）

```
def func(p1,*,p2,p3):
    print(p2+p3)
func(1,p2=2,p3=3)

# 输出
5
```

以下代码中p2、p3不使用关键字传参即会报错。

```
def func(p1,*,p2,p3):
    print(p2+p3)
func(1,2,3)

# 输出（报错）
TypeError                                 Traceback (most recent call last)
<ipython-input-17-83e9b0e5315e> in <module>
      1 def func(p1,*,p2,p3):
      2     print(p2+p3)
----> 3 func(1,2,3)

TypeError: func() takes 1 positional argument but 3 were given
```



#### 列表传参

将列表传入函数，只要在列表名前面加上`*`即可传入

```
def func(a, b):
    print(a+b)
l = [1, 2]
func(*l)

# 输出
3
```

#### 字典传参

将字典你传入函数，只需要在字典名前面加上`**`即可

```
def func(a,b,c):
    print(a+b+c)
param = {"a":1,"b":2,"c":3}
func(**param)

# 输出
6
```



### 高阶函数

可以简单理解为调用函数的函数。

#### `lambda`表达式

lambda是python中的一种表达式，创建内嵌的简单**匿名函数**。

```
f = lambda i:i**2
f(2)
# 4
```



#### filter函数

filter(func, seq) 	返回seq中所有满足func结果为真的对象，可以简单理解为一个过滤器。

```
# 找到所有奇数
l = range(10)
a = filter(lambda n: n%2!=0, l)
print(list(a))
# [1, 3, 5, 7, 9]
```



#### map函数

map (func, seq) 返回对seq执行func后的值，可以简单理解为一个映射。

```
# 找到数的所有平方
l = range(5)
a = map(lambda n: n**2, l)
print(list(a))
# [0, 1, 4, 9, 16]
```