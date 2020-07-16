---
title: 2、Python函数相关内容
date: 2020-05-08 15:20:53
tags:
	- python
categories:
	- Python
	- 语言基础
fileName: python2
---

### Python函数的基本特性

#### Python函数为对象

Python中的函数都是对象，这也使得其具备了一些对象的通用特性。

* 可作为**值传递**，赋值给另一个对象；

* 可以作为元素添加到集合对象中；

* 可以作为参数传递给其他函数；

* 可以作为函数的返回值。

1、函数赋值给变量，通过变量可以调用。且传递方法为**值传递**，这也就意味着即使删除函数f1，temp仍然能够正常运行。

```
def f1(str):
  return len(str)
temp=f1	# 函数赋值给对象
del f1	# 删除函数
print temp('hello')	# 仍然正常调用
```

2、作为元素添加进入容器。

```
def f2(str1,str2):
   return str1+str2
a=[]
a.append(f1)
a.append(f2)
print a[0]('hello')
print a[1]('hello','world')
```

3、作为函数参数

```
def func(f,str):
  return f(str)
print func(f1,'hello')
```

4、作为函数返回值

```
def mutiply(x,y):
  reutrn x*y
def sub(x,y):return x-y
def func2(i):if i==1: return mutiplyelse:return sub
temp=func2(2)
print temp(2,4)
```

**Python函数是最高级别的对象，而不仅仅为一小段代码**。一个函数是一个被其自己定义和执行的对象，参数是一种“成员数据”，所以它们的状态和其他对象一样，会随着每一次调用而改变。

#### 嵌套与闭包

1、函数的嵌套

```
def fun1():
    def fun2():
        def fun3():
            print("Hello world!")
        return fun3
    return fun2
fun1()()()
##########################################
Hello world!
```

2、函数的闭包

**闭包函数就是函数可以读取到与自己不是一个作用域的局部变量**

1. 必须有一个内嵌函数
2. 内嵌函数必须引用外部函数中的变量
3. 外部函数的返回值必须是内嵌函数

```
def fun1(x):
    def fun2(y):
        print(x+y)
    return fun2
fun1(2)(3)
#############################
5
```

**装饰器，匿名函数，map函数等都是函数的变体，都是通过这些基本特性来实现的**

**装饰器**：使用函数的嵌套，外层函数传进来的参数为一个函数对象，里层函数对传进来的函数进行加工处理后再返回处理的函数



### 函数的一些传参方法

#### 关键字传参

可以增强代码可读性，每次传参都需要指定形参名，以下就是关键字传参。

```
def func(a,b):
	pass
func(a=1,b=2)
```



**Tip：** * 代表之后所有参数传参时必须使用关键字传参（混合形式传参）

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

将列表传入函数，只要在列表名前面加上*即可传入

```
def func(a, b):
    print(a+b)
l = [1, 2]
func(*l)

# 输出
3
```

#### 字典传参

将字典你传入函数，只需要在字典名前面加上**即可，这种方法可以先将参数写为json格式之后进行传入。

```
def func(a,b,c):
    print(a+b+c)
param = {"a":1,"b":2,"c":3}
func(**param)

# 输出
6
```

#### 默认参数

很好理解，在定义函数时指定该参数的值即可。

```
# 参数a默认为1
def func(a=1):
	pass
```

### 可变参数（重要特性）

在定义函数时，不限定参数的个数，而使用*args和**kwargs表示。*args为元组，**kwargs为字典。

*args将参数打包为tuple给函数体使用，实例如下：

```
def func(a, *args):
    print(a)
    print(type(a))
    print(args)
    print(type(args))
func('gg', 1, 2, 'gg3')

######################################
gg
<class 'str'>
(1, 2, 'gg3')
<class 'tuple'>
```

**kwargs将参数打包为dict给函数使用，调用时实参为key=value的形式。

```
def func(a, **kwargs):
    print(a)
    print(type(a))
    print(kwargs)
    print(type(kwargs))
func('gg', b=1, c=1)

############################################
gg
<class 'str'>
{'b': 1, 'c': 1}
<class 'dict'>
```

#### 参数组合

当多种参数组合使用时，参数定义顺序为必选参数、默认参数、可变参数。



### 高阶函数

可以简单理解为调用函数的函数。

#### `lambda`表达式

lambda是python中的一种表达式，创建内嵌的简单**匿名函数**。可以避免取函数名

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