---
title: 6、Python异常处理
date: 2020-05-15 15:15:49
tags:
categories:
	- Python
	- 基础知识
fileName: python6
---

### 异常基本概述

1. 每个异常都是某个类的实例
2. 发生了异常如果不捕获，程序将终止执行
3. 有一些内置的异常类（Exception等）



### 捕获异常

 `try except`句式

```
def test(a, b):
    return a/b

if __name__ == '__main__':
    try:
        print(test(2, 0))
    except:
        print("出错了")
        
#############################
出错了
```

检测出错类型的方法

```
def test(a, b):
    return a/b

if __name__ == '__main__':
    try:
        print(test(2, 'a'))
    except ZeroDivisionError:
        print("除数为0")
    except TypeError:
        print("类型出错")
        
#######################################
类型出错
```



获取出错信息

```
def test(a, b):
    return a/b

if __name__ == '__main__':
    try:
        print(test(2, 'a'))
    except (ZeroDivisionError, TypeError) as e:
        print(e)
        
############################################
unsupported operand type(s) for /: 'int' and 'str'
```



处理必须处理的语句

`try except finally`语句

```
def test(a, b):
    return a/b

if __name__ == '__main__':
    try:
        print(test(2, 'a'))
    except (ZeroDivisionError, TypeError) as e:
        print(e)
    finally:
        print("我必须处理")
        
#################################################
unsupported operand type(s) for /: 'int' and 'str'
我必须处理
```



### 自定义异常和抛出异常

通过继承Exception类来定义异常，通过使用raise关键字来抛出异常。

```
class test(Exception):
    pass

if __name__ == '__main__':
    if True:
        raise test('出现了自定义的测试错误')
        
######################################################
Traceback (most recent call last):
  File "C:/Users/lenovo/Desktop/python学习/decorate.py", line 7, in <module>
    raise test('出现了自定义的测试错误')
__main__.test: 出现了自定义的测试错误
```



