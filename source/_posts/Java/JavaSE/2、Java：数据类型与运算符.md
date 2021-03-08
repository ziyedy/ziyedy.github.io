---
title: Java：数据类型与运算符
date: 2021-01-23 17:08:17
tags:
	- Java
categories:
	- Java
	- Java基础
fileName: java-datatype-operator
---

## 数据类型

### 8种基本数据类型

> Java有8中基本数据类型

* 数字类型（byte<1>、short<2>、int<4>、long<8>、float<4>、double<8>）；

* 字符类型（char<2>）；

* 布尔型（boolean<依赖于JVM的具体实现>）

（其中<>内为其所对应的字节数，每个基本数据类型又有对应的默认值，其中**数据类型的基本都是0**）

> 数字类型中前四种使用了*常量池*技术，而后两种没实现，具体的影响见下一节中的案例部分

### 数据类型的转换

#### 转换规律

> 基本按照常识即可判断无精度损失
>
> 需要注意的是int转float、long转float、long转double是有可能出现精度损失的
>
> 当使用*二元运算符*连接两个操作数时，先判断有没有`double`，再判断有没有`float`，再判断有没有`long`，判断有则结果就为对应类型，否则为`int`。

#### 强制类型转换

> 有信息损失的通常需要进行强制转换

```
double x = 3.14;
int x = (int) x;	// double强制转换为int
```

### 常量

> 使用`final`修饰的为常量，使用`static final`修饰的为类常量
>
> 常量名通常全大写



## 装箱与拆箱

参考：https://www.cnblogs.com/dolphin0520/p/3780005.html

> Java为每种基本数据类型都提供了对应的**包装器类型**，将基本数据类型转换成包装类称为**装箱**，反之则称为**拆箱**。

### 装箱

> 装箱：基本数据类型——>包装类
>
> 其中第一种方法不会触发自动装箱的机制，在执行效率和资源占用上，第二种方法通常要优于第一种情况。

```
Integer i = new Integer(10);	// 手动装箱
Integer i = 10;	// 自动装箱
```

### 拆箱

> 拆箱：包装类——>基本数据类型
>
> 直接进行赋值语句即可，同时如“+”、“-”等常规数值运算符会触发自动拆箱

```
int i2 = i;
```

### 装箱与拆箱的实现

**装箱过程是通过调用包装器的`valueOf()`方法实现的**

**拆箱过程是通过调用包装器的 xxxValue方法实现的（xxx代表对应的基本数据类型）**

Java源码：

```
public static void main(String[] args) {
	Integer a = 1;
	int b = a + 1;
}
```

反编译后结果：

![](http://cdn.ziyedy.top/2%E3%80%81Java%EF%BC%9A%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B%E4%B8%8E%E8%BF%90%E7%AE%97%E7%AC%A6/%E8%87%AA%E5%8A%A8%E6%8B%86%E7%AE%B1%E5%8F%8D%E7%BC%96%E8%AF%91.png)

### valueOf方法

#### 源码分析

> 以`Integer`为例，源码如下，可见
>
> 若数值在**[-128,127]**之间，便返回`IntegerCache.cache`中已存在的对象的引用，否则创建一个新的`Integer`对象

```
public static Integer valueOf(int i) {
	if (i >= IntegerCache.low && i <= IntegerCache.high)
		return IntegerCache.cache[i + (-IntegerCache.low)];
	return new Integer(i);
}
```

> 因此：
>
> * `Integer a = 0;`会直接使用常量池中的对象
> * `Integer b = new Integer(0);`会创建新的对象
>
> 所以若输出`a == b`，会输出false

#### 问题案例

1、常量池相关问题

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

> Integer、Short、Byte、Character、Long这几个类的valueOf方法的实现是类似的（**这些方法实现了常量池技术**），而Double、Float的valueOf方法的实现是类似的（**这些方法没有使用常量池技术**）。因此，上述代码若换成Double，会输出“false false”，换成Boolean会输出“true true”。

2、算数运算符相关问题

> 如果这些包装类使用了算数运算符，那么“==”比较的将会是数值（即触发了自动拆箱），如下：

```
Integer a = 1;
Integer b = 2;
Integer c = 3;
System.out.println(c==(a+b));	// 实际比较的是数值 3 == 3 为true

#########################################
true
```

***

> 对于包装器类型，equals方法并不会进行类型转换，如下：

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





## 相关常见问题







> 参考：
>
> https://www.cnblogs.com/dolphin0520/p/3780005.html