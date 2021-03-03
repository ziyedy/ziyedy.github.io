---
title: Java：注解
date: 2021-01-30 23:38:43
tags:
	- Java
categories:
	- 编程相关
	- Java
	- Java基础
fileName: Java-annotation
---

## 注解基础

### 概念

> 注解（Annotation），JDK1.5引入
>
> 位于源码中（可以理解Java源码由*代码/注释/注解* 构成）
>
> 注解用于修饰程序的元素，而不会对被修饰的对象有直接影响，只有通过**某种配套的工具**才会对注解信息进行访问和处理（比如一些框架中就利用反射技术对注解中内容进行处理）

### 作用

* 提供信息给编译器/IDE（如`@SuppressWarnings`即让IDE压制相应警告信息）

* 可用于其他工具来产生额外的代码/配置文件（比如Java web中即可使用注解生成配置文件）
* 有一些注解可在程序运行时访问，增加程序的动态性

### 普通注解

即Java预定义的普通注解，主要有以下一些

* `@Override`：修饰方法，强制该方法代码必须符合父类中该方法的定义，避免代码错误
* `@Deprecated`：修饰类/类元素/包，表明该部分废除，建议不再使用（IDE会显示为被横线划掉）
* ` @SuppressWarnings`：压制不同类型的警告信息，使得编译器不再显示警告（警告类型由IDE自定）。同时该方法可以传入参数，用于标识忽略哪些信息，示例如下

```
@SuppressWarnings("all")	// 忽略所有警告
```

### 元注解

> 元注解即修饰注解的注解，在自定义注解时比较常用

#### @Retention

> 该注解用于修饰其他注解的存在范围
>
> `RetentionPolicy.SOURCE`：注解仅存在于源码，不在class文件
>
> `RetentionPolicy.CLASS`：*默认参数*，注解存在于.class文件，但不能被JVM加载
>
> `RetentionPolicy.RUNTIME`：表示注解可以被JVM运行时访问到，通常情况下可以结合反射进行一些工作

```
@Retention(RetentionPolicy.RUNTIME)	// 修饰的注解存在于JVM中
```

#### @Target

> 用于限定目标注解能够作用在什么地方
>
> 比如有些注解只能修饰方法，就是使用这个实现的

```
@Target({ElementType.METHOD, ElementType.FIELD})	// 限定修饰方法与成员变量
```

#### @Inherited

> 使得修饰的注解修饰的类与它的子类都能够包含某个注解
>
> 普通的注解没有继承功能

#### @Repeatable

> 修饰的注解可以重复应用标注，常用于测试
>
> 需要定义注解和注解容器（即原注解的数组）

#### @Documented

> 指明该注解可以被Javadoc工具解析，形成帮助文档



## 自定义注解

> 注解的定义即拓展`java.lang.annotation.Annotation`注解接口
>
> 其中绝大部分类型都可以作为其参数，但属性定义成方法 的样子

定义形式如下

```
public @interface MyAnnotation {
    int a() default 0;
    int b() default 0;
}
```



## 注解的解析

暂略





## 注解的应用

暂略









> 参考：
>
> mooc华东师范大学Java核心技术

