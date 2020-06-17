---
title: 5、Python装饰器、迭代器、生成器
date: 2020-05-13 14:25:10
tags:
categories:
	- Python
	- 基础知识
fileName: python5
---

### 装饰器

用于拓展原来函数功能的一种函数，返回函数的函数，在不更改原函数代码的前提下给函数增加新的功能（易于代码重用）

```
# 装饰器的常用写法
# 输入函数，在函数内部定义返回函数
def decoratre(func):
    def wrapper():
        print('start')
        func()
        print(('end'))
    return wrapper

@decoratre
def func():
    print("原函数执行")


if __name__ == '__main__':
    func()
    
##########################################
start
原函数执行
end
```



### 带参数的装饰器

#### 装饰器参数的传入

```
def gg(name):
    def decoratre(func):
        def wrapper():
            print('{}:start'.format(name))
            func()
            print('{}:end'.format(name))
        return wrapper
    return decoratre

@gg('func')
def func():
    print("原函数执行")


if __name__ == '__main__':
    func()
    
#########################################
func:start
原函数执行
func:end
```



#### 函数参数的传入

使用`*args, **kwargs`

```
def gg(name):
    def decoratre(func):
        def wrapper(*args, **kwargs):
            print('{}:start'.format(name))
            res = func(*args, **kwargs)
            print(res)
            print('{}:end'.format(name))
            return res
        return wrapper
    return decoratre
    
@gg('sum')
def sum(a, b):
    return a+b
    
if __name__ == '__main__':
    sum(1, 2)
    
#############################################    
sum:start
3
sum:end
```







装饰器对函数进行封装时改变了原函数的名称与说明文档等信息，如下：

```
def gg(name):
    def decoratre(func):
        def wrapper():
            print('{}:start'.format(name))
            func()
            print('{}:end'.format(name))
        return wrapper
    return decoratre

@gg('func')
def func():
    print("原函数执行")

if __name__ == '__main__':
    print(func.__name__)
    
###################################################
wrapper
```



为解决这个问题，可使用`@wraps(func)`方法，具体如下。

```
from functools import wraps

def gg(name):
    def decoratre(func):
        @wraps(func)
        def wrapper():
            print('{}:start'.format(name))
            func()
            print('{}:end'.format(name))
        return wrapper
    return decoratre

@gg('func')
def func():
    print("原函数执行")

if __name__ == '__main__':
    print(func.__name__)
    
####################################################
func
```



### 类的装饰器

对一些类都需要添加一些共有的方法，可以用类的装饰器实现

基本使用方法如下：

```
def decorate(cls):
    cls.func = lambda self: print("新方法")
    return cls

@decorate
class Pig():
    pass

if __name__ == '__main__':
    pig = Pig()
    pig.func()
    
##########################################
新方法
```



### 迭代器

以下代码实现了一个计算平方的迭代器

```
class Square():
    value = 0

    def __iter__(self):
        return self

    def __next__(self):
        self.value += 1
        return self.value ** 2

if __name__ == '__main__':
    test = Square()
    print(test.__next__())
    print(test.__next__())
    print(next(test))
```



### 生成器

```
def square():
    for i in range(5):
        yield i ** 2
```
