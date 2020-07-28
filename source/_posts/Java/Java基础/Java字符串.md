---
title: Java字符串
date: 2020-07-27 15:45:43
tags:
	- Java
categories:
	- Java
	- Java基础
fileName: Java-string
---

### String类

#### 基本实现

String被声明为final，因此不可被继承。在**Java8**中，String内部使用char数组存储数据。

```
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];
}
```

在**Java9**之后，String类的实现改用byte数组存储字符串，同时使用coder来标识使用哪种编码。

```
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final byte[] value;

    /** The identifier of the encoding used to encode the bytes in {@code value}. */
    private final byte coder;
}
```

#### 不可变特性

上述中*value*使用了final关键字修饰，这说明value数组初始化后就不能再引用其他数组，因此String是不可变的。



#### 常用方法

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



### StringBuild类

String具有不可变性（变化都是通过赋值完成的，类内方法不能使当前对象中的字符串进行改变），而StringBuild不具备

当频繁操作字符串时，应该使用StringBuild

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

