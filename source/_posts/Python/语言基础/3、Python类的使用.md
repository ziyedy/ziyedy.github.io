---
title: Python类的使用
date: 2020-05-08 15:40:45
tags:
categories:
	- Python
	- 语言基础
fileName: python3
---

### 一些基本内容

_name：单前置下划线，私有化属性或方法，类对象和子类可以访问，from somemodule import *禁止导入

__name：双前置下划线，私有化属性或方法，无法在外部直接访问（名字重整所以访问不到）

\__name__：系统定义的名字，自己不要创建



Python的析构函数`__del__(self)`。默认会直接进行操作。

```
# 判断是否为子类
issubclass(cat, animal)
```



### 调用父类方法
`super()`方法

若方法重名，先继承的优先调用

```
class A():
    def test(self):
        print("我是爸爸")

class B(A):
    def test(self):
        super().test()
        print("我是儿子")
        
if __name__ == '__main__':
    b = B()
    b.test()
    
#############################################
我是爸爸
我是儿子
```



### @property

@property：将类的方法当做属性来使用（可以简单理解为调用时可以将函数的括号去掉）

一般用来获取某些数据（如获取私有变量），不会向函数中传递参数

```
class pig():
    def __init__(self, name):
        self.__name = name

    @property
    def name(self):
        return self.__name
        
p = pig("猪")
print(p.name) # 等价于不加@property时的，p.name()

# 猪
```



@*.setter 表示可读可写，可以向该函数中传递参数，使用方法为`对象.函数名 = 参数值`。一般用来设置某些数据。

```
class Pig():
    def __init__(self, name):
        self.__name = name

    @property
    def name(self):
        return self.__name

    @name.setter
    def name(self, name):
        # 在这即可对需要设置的值进行类型判断等操作
        self.__name = name
        
if __name__ == '__main__':
    p = Pig('pig1')
    print(p.name)
    p.name = 'pig2' # 可进行更改
    print(p.name)
  
​```
pig1
pig2
​```
```



### \__slots__<限制类中的属性>

为指定的类设置一个静态属性列表，为属性很少的类节约内存空间，同时提供更快的属性访问速度

使用`__slots__`后**不允许给类添加新的属性与方法**（对其子类也适用，子类新加的会拼接到后面）。示例如下：

```
# 不加__slots__
class B():
    pass
    
b = B()
b.gg = 1
print(b.gg)

###############################
1
```

```
# 加上__slots__
class A():
    __slots__ = ('name')
    def __init__(self, name):
        self.name = name
        
if __name__ == '__main__':
    a = A('aa')
    a.gg = 1
    print(a.gg)
    
###############################################
Traceback (most recent call last):
  File "C:/Users/lenovo/Desktop/python学习/decorate.py", line 13, in <module>
    a.gg = 1
AttributeError: 'A' object has no attribute 'gg'
```

可见，使用\__slots__指定了类的属性后就无法对实例添加属性了



### Python类中的方法

Python类中的方法主要可以分为以下三种：

* 实例方法：需要绑定要一个对象上，第一个参数默认使用self，会把对象作为第一个参数传递进来
* 静态方法：使用装饰器@staticmethod进行定义，类和对象都可以调用，不需要默认参数
* 类方法：使用装饰器@classmethod进行定义，类和对象都可以调用，第一个参数默认使用cls，会把类作为第一个参数传递进来



#### @staticmethod，静态方法

静态方法，可以直接通过类名对其进行调用（类的实例也可以调用 ）。相当于变为了"类的方法"。<类和对象都可以调用，不需要默认参数（如self等）>

```
class Pig():
    @staticmethod
    def func():
        print("聪明的猪")
        
Pig()

#############################################
聪明的猪
```



#### @classmethod，类方法

用于表示类的方法，可以**不实例化调用类的属性和方法**。有一个`cls`参数用于表示类本身。<类和对象都可以调用，第一个参数默认使用cls，会把类作为第一个参数传递进来>

```
class A():
    def test(self):
        print("test")

    @classmethod
    def gg(cls):
        cls().test()
        
A.gg()

############################################
test
```



#### 动态添加实例方法

```
class A():
	pass
	
def func(self):
	pass
	
a = A()
a.func = types.MethodType(func, a)	# 给实例添加方法
```

如上，可以使用types.MethodType的方式给类的实例添加其实例方法，但该对象的另一个实例则不具备该方法。

