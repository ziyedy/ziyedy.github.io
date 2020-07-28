---
title: 5、Java异常
date: 2020-07-28 11:38:50
tags:
	- Java
categories:
	- Java
	- Java基础
fileName: Java-exception
---

### 异常层次结构

<img src="5、Java异常/exception-chart.png" style="zoom:67%;" />

 java.lang 包中的**Throwable类**是所有异常的根类。它有两个重要的子类

1、**Error。程序无法处理的错误**。表示运行程序中较为严重的错误，通常是代码运行时JVM出现的问题

2、**Exception。程序本身可以处理的异常**。



### Throwable常用方法

```
public string getMessage():返回异常发生时的简要描述

public string toString():返回异常发生时的详细信息

public void printStackTrace():在控制台上打印 Throwable 对象封装的异常信息
```





### try-catch-finally

#### try

用于捕获异常。其后可接零个或多个 catch 块，如果没有 catch 块，则必须跟一个 finally 块。

```
try {

} catch(Exception e) {
	e.printStackTrace();
} finally {

}
```

#### catch

用于处理 try 捕获到的异常。

#### finally

无论是否捕获或处理异常，finally 块里的语句都会被执行。当在 try 块或 catch 块中遇到 return 语句时，finally 语句块将在方法返回之前被执行。



#### throw与throws

throws声明方法将要抛出何种异常的声明，通过throw将产生的异常抛出

若一个方法可能出现异常，但没有能力处理异常，可以在方法声明处用throws子句来声明抛出异常

```
public void method() throws Exception1, Exception2 {
	//可能产生异常的代码
}
```





本文参考：

[异常处理参考](https://gitee.com/SnailClimb/JavaGuide/blob/master/docs/java/Java基础知识.md#14-%E6%96%B9%E6%B3%95%E5%87%BD%E6%95%B0)