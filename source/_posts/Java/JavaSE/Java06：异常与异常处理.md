---
title: Java06：异常与异常处理
date: 2020-07-28 11:38:50
tags:
	- Java
categories:
	- Java
	- JavaSE
fileName: Java-exception-handle
---

## 异常分类

### 异常层次结构

<img src="http://cdn.ziyedy.top/6%E3%80%81Java%EF%BC%9A%E5%BC%82%E5%B8%B8%E4%B8%8E%E5%BC%82%E5%B8%B8%E5%A4%84%E7%90%86/exception-chart.png" style="zoom:67%;" />

 `java.lang`包中的`Throwable`类是所有异常的根类。它有两个重要的子类

1、`Error`。程序无法处理的错误。表示运行程序中较为严重的错误，通常是代码运行时JVM出现的问题

2、`Exception`。程序本身可以处理的异常。比如`RuntimeException`等等

除此之外，还可以分为*检查异常*与*非检查异常*。非检查异常包括Error与RuntimeException，而检查异常则包括其他相关的异常

**编译器会为检查异常配置相关处理，若没有处理会报错**



### Throwable 常用方法

```java
public string getMessage()	// 返回异常发生时的简要描述

public string toString()	// 返回异常发生时的详细信息

public void printStackTrace()	// 在控制台上打印 Throwable 对象封装的异常信息
```



## 异常处理

### try-catch-finally结构

* `try`：正常业务逻辑代码
* `catch`：当`try`发生异常时执行的代码，可以有多个，从上到下进行匹配，一个异常只能进入一个`catch`
* `finally`：当`try`或`catch`执行结束后必须要执行的代码块

```
try {

} catch(Exception e) {
	e.printStackTrace();
} finally {

}
```

### try-with-Resources语句

> 定义于JDK 7，对**实现了`AutoCloseable`接口及其子接口的类（实现了其中的`close`方法）**，在try之后会自动调用关闭方法
>
> 类似于Python中的`with`语句



### throws关键字

> 应用于方法存在可能异常的语句，但不想在此处进行处理，即可使用`throws`
>
> 若一个方法可能出现异常，但没有能力处理异常，可以在方法声明处用throws子句来声明抛出异常

```java
public void method() throws Exception1, Exception2 {
	// 可能产生异常的代码
}
```



### throw关键字

> 将产生的异常抛出



### 异常抛出的规律

> 1、一个方法被覆盖，则覆盖它的方法必须抛出相同的异常

如下，method2抛出了Exception，那么调用它的method1也必须抛出相应的方法

```java
public void method1() throws Exception {
    method2();
}

public void method2() throws Exception {
    
}
```

> 如果父类的方法抛出了异常，那么重写的子类方法也**必须抛出那些异常的子集，不能抛出新的异常**





## 自定义异常

### Exception类

> Exception类是所有待处理异常的父类
>
> 自定义异常需要继承`Exception`类或其子类
>
> 同时实现的关键在于其构造函数

### 示例

```java
public class MyException extends Exception {
    private String returnCode;  // 异常返回码
    private String returnMsg;   // 异常描述信息

    public MyException() {
        super();
    }

    public MyException(String returnMsg) {
        super(returnMsg);
        this.returnMsg = returnMsg;
    }
}
```







本文参考：

