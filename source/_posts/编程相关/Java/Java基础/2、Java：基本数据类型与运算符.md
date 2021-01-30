---
title: Java：基本数据类型与运算符
date: 2021-01-23 17:08:17
tags:
	- Java
categories:
	- 编程相关
	- Java
	- Java基础
fileName: 
---

## 8种基本数据类型

Java有8中基本数据类型，数字类型（byte<1>、short<2>、int<4>、long<8>、float<4>、double<8>）；字符类型（char<2>）；布尔型（boolean<依赖于JVM的具体实现>），其中<>内为其所对应的字节数，每个基本数据类型又有对应的默认值。



## 装箱与拆箱

参考：https://www.cnblogs.com/dolphin0520/p/3780005.html

Java为每种基本数据类型都提供了对应的包装器类型，将基本数据类型转换成包装类称为**装箱**，反之则称为**拆箱**。

1、装箱：其中第一种方法不会触发自动装箱的机制，在执行效率和资源占用上，第二种方法通常要优于第一种情况。

```
Integer i = new Integer(10);	// 手动装箱
Integer i = 10;	// 自动装箱
```

拆箱（直接进行赋值语句即可）：

```
int i2 = i;
```

**装箱过程是通过调用包装器的valueOf方法实现的，而拆箱过程是通过调用包装器的 xxxValue方法实现的。（xxx代表对应的基本数据类型）。**

***

**注意：**

如下代码的输出不同，原因在于valueOf的实现方法，*可以简单总结为若数值在[-128,127]之间，便返回IntegerCache.cache中已存在的对象的引用，否则创建一个新的Integer对象*

```
Integer i1 = 100;
Integer i2 = 100;

Integer i3 = 200;
Integer i4 = 200;

System.out.println(i1==i2);
System.out.println(i3==i4);

####################################
true
false
```

同时，Integer、Short、Byte、Character、Long这几个类的valueOf方法的实现是类似的（**这些方法实现了常量池技术**），而Double、Float的valueOf方法的实现是类似的（**这些方法没有使用常量池技术**）。因此，上述代码若换成Double，会输出“false false”，换成Boolean会输出“true true”。

除此之外，还应该注意的是，如果这些包装类使用了算数运算符，那么“==”比较的将会是数值（即触发了自动拆箱），如下：

```
Integer a = 1;
Integer b = 2;
Integer c = 3;
System.out.println(c==(a+b));

#########################################
true
```

***

对于包装器类型，equals方法并不会进行类型转换，如下：

```
Integer a = 1;
Integer b = 2;

Long c = 3L;
Long d = 2L;

System.out.println(c==a+b);
System.out.println(c.equals(a+b));
System.out.println(c.equals(a+d));

###########################################
true
false
true
```



## 数组



## 枚举





