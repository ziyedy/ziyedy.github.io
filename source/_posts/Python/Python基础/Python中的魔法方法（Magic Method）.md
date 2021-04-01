---
title: Python中的魔法方法（Magic Method）
date: 2020-08-26 09:19:41
tags:
	- Python
categories:
	- Python
	- Python基础
fileName: python-magic-method
---

### 什么是魔法函数

所谓魔法函数（Magic Methods），是Python的一种高级语法，允许你在类中自定义函数（函数名格式一般为\_\_xx\_\_），并绑定到类的特殊方法中。比如在类A中自定义`__str__()`函数，则在调用`str(A())`时，会自动调用`__str__()`函数，并返回相应的结果。在我们平时的使用中，可能经常使用`__init__`函数和`__del__`函数，其实这也是魔法函数的一种。

Python中每一个魔法函数都对应了一个Python内置函数或操作。比如`__str__`对应str函数。

### 构造与析构

当创建一个对象时，`__new__`被调用创建类并返回这个类的实例，`__init__`将传入的参数来初始化该实例。

> 1. `__new__`是一个静态方法，而`__init__`是一个实例方法.
> 2. `__new__`方法会返回一个创建的实例，而`__init__`什么都不返回.
> 3. 只有在`__new__`返回一个cls的实例时后面的`__init__`才能被调用.
> 4. 当创建一个新实例时调用`__new__`，初始化一个实例时用`__init__`.

当对象生命周期结束时，`__del__`会被调用（作为析构函数）



### 二元操作符

```
+	object.__add__(self, other)
-	object.__sub__(self, other)
*	object.__mul__(self, other)
//	object.__floordiv__(self, other)
/	object.__div__(self, other)
%	object.__mod__(self, other)
**	object.__pow__(self, other[, modulo])
<<	object.__lshift__(self, other)
>>	object.__rshift__(self, other)
&	object.__and__(self, other)
^	object.__xor__(self, other)
|	object.__or__(self, other)
```



### 扩展二元操作符

```
+=	object.__iadd__(self, other)
-=	object.__isub__(self, other)
*=	object.__imul__(self, other)
/=	object.__idiv__(self, other)
//=	object.__ifloordiv__(self, other)
%=	object.__imod__(self, other)
**=	object.__ipow__(self, other[, modulo])
<<=	object.__ilshift__(self, other)
>>=	object.__irshift__(self, other)
&=	object.__iand__(self, other)
^=	object.__ixor__(self, other)
|=	object.__ior__(self, other)
```



### 比较方法

```
<	object.__lt__(self, other)
<=	object.__le__(self, other)
==	object.__eq__(self, other)
!=	object.__ne__(self, other)
>=	object.__ge__(self, other)
>	object.__gt__(self, other)
```



### 类的表示、输出

```
str()	object.__str__(self) 
repr()	object.__repr__(self)
len()	object.__len__(self)
hash()	object.__hash__(self) 
bool()	object.__nonzero__(self) 
dir()	object.__dir__(self)
sys.getsizeof()	object.__sizeof__(self)
```



### 类容器

```
len()	object.__len__(self)
self[key]	object.__getitem__(self, key)
self[key] = value	object.__setitem__(self, key, value)
del[key] object.__delitem__(self, key)
iter()	object.__iter__(self)
reversed()	object.__reversed__(self)
in操作	object.__contains__(self, item)
字典key不存在时	object.__missing__(self, key)
```





### 参考

https://zhuanlan.zhihu.com/p/24567545

https://www.cnblogs.com/jhao/p/6022928.html