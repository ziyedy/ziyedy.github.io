---
title: Java：字符串
date: 2020-07-27 15:45:43
tags:
	- Java
categories:
	- Java
	- Java基础
fileName: Java-string
---

## String类

### 简介

> 在Java中，所有类似“ABC”的字面值，都是String类的实例；
>
> String类位于java.lang包下，是Java语言的核心类，提供了字符串的比较、查找、截取、大小写转换等操作；
>
> Java语言为`“+”`连接符（字符串连接符）以及对象转换为字符串提供了特殊的支持，字符串对象可以使用`“+”`连接其他对象



### 基本实现

String被声明为`final`，因此不可被继承，同时也说明**String具有不可变特性**。在**Java8**中，String内部使用char数组存储数据。

```
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];
}
```

在**Java9**之后，*String类的实现改用byte数组存储字符串，同时使用coder来标识使用哪种编码*。

```
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final byte[] value;

    /** The identifier of the encoding used to encode the bytes in {@code value}. */
    private final byte coder;
}
```



### 常用方法

```
// 获取字符串长度
int length()

// 获取从begin到end的字符串
String substring(int beginIndex, int endIndex)

// 获取指定位置的字符
char charAt(int index)

// 将字符串分割为子字符串
String[] split(String regex, int limit)	

// 将该字符串转换为byte数组
byte[] getBytes()	
```



## “+”连接符

### 实现原理

> "+"连接符实现字符串连接是通过**StringBuilder（或 StringBuffer）类及其append 方法实现的**
>
> Java在使用“+”连接字符串时，会创建一个`StringBuilder`对象，并调用`append()`方法进行拼接，最后调用`toString()`方法返回拼接好的字符串



### 效率

> 由于使用“+”连接符时，JVM会隐式创建StringBuilder对象
>
> 因此在大规模进行字符串拼接时还是构建一个`StringBuilder`对象手动调用`append`方法比较好
>
> 但同时，当"+"两端均为编译期确定的字符串常量时，编译器会进行相应的**优化**，直接将两个字符串常量拼接好

比如：

```
System.out.println("a" + "b");

System.out.println("ab");	// 反编译后直接拼接好
```





## String、StringBuilder、StringBuffer

### 主要区别

1. String是不可变字符序列，StringBuilder和StringBuffer是可变字符序列
2. 执行速度StringBuilder > StringBuffer > String
3. StringBuilder是非线程安全的，StringBuffer是线程安全的



### StringBuild类

String具有不可变性（变化都是通过赋值完成的，类内方法不能使当前对象中的字符串进行改变），而StringBuild不具备

当频繁操作字符串时，应该使用StringBuild

#### 常用方法

```
// 追加内容到当前StringBuild对象的末尾
StringBuild append(字符串)	

// 将内容插入到指定的位置
StringBuild insert(位置， 字符串)

// 将之转化为String对象
String toString()

// 删除start到end的字符串。[start, end)
String delete(start, end)

// 将start到end之间的字符串替换为str
String replace(start, end, str)

// 获取字符串长度
int length()
```





## 字符串常量池

暂略





> 参考：
>
> https://blog.csdn.net/ifwinds/article/details/80849184