---
title: Java：基本概念与语言特点
date: 2020-06-27 17:25:12
tags:
	- Java
categories:
	- 编程相关
	- Java
	- Java基础
fileName: java-basic-grammar
---

## 常见名词解释

### JVM（Java Virtual Machine）

Java虚拟机，用于运行Java字节码，其针对不同的操作系统有着特定的实现，使得相同的字节码运行会给出相同的结果。

在Java中，JVM能够理解的代码就叫做**字节码**（.class文件）。其不面向任何特定的处理器，只面向虚拟机。

### JDK（Java Development Kit）

JDK是功能齐全的Java软件开发包，拥有JRE所拥有的一切，有编译器（javac）和一些其他工具，能够创建和编译程序。

### JRE（Java Runtime Environment）

即Java运行时环境。其为运行已编译Java程序所需要的所有内容的集合，包括Java虚拟机、Java类库、Java命令和其他一些基础的构建。但是，其不能用于创建新程序。

### Java SE（Standard Edition）

即Java标准版，包括Java语言规范等等

### Java EE（J2EE）

Java平台企业版，可以理解为为企业级应用开发的一些Java Web框架等内容。



## Java语言特点

### 基本特性

> 1、面向对象（具有封装、继承、多态等特性）
>
> 2、平台无关性（JVM的实现使得Java具有平台无关性）
>
> 3、可靠性、安全性
>
> 4、提供了多线程支持
>
> 5、支持网络编程且十分方便
>
> 6、编译与解释并存：Java程序要经过先编译、后解释两个步骤。（`.java`源文件先经过JDK中的`javac`命令**编译**生成字节码文件`*.class`，再经过JVM**解释**进行执行）

### 与C++的区别

>1、Java不提供指针来直接访问内存，程序内存更安全
>
>2、Java的类是单继承的，而C++支持多继承
>
>3、Java有自动内存管理垃圾回收机制（GC），不需要手动释放无用内存
>
>4、Java的字符串与字符数组最后没有一个额外的字符`\0`来表示结束（Java字符串与数组都可以理解为类，预先知道大小，所以不需要结束字符）



## 常用命令

### 命令行编译

利用`javac`和`java`能够实现对`.java`文件进行编译和运行

`javac`程序是一个Java编译器，将`*.java`编译成`*.class`（需要`.java`后缀）

`java`程序启动Java虚拟机（只需要指定类名）

```
javac Main.java
java Main
```

如下编译运行一个helloworld程序：

![](http://cdn.ziyedy.top/1%E3%80%81Java%EF%BC%9A%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5%E4%B8%8E%E8%AF%AD%E8%A8%80%E7%89%B9%E7%82%B9/javac.png)

### 反编译

使用`javap`能够对`.class`文件进行反编译，通过该命令能够有效了解JVM的内部原理

```
javap	# 对其进行反编译（得到近似的Java源码）
javap -c	# 对其进行反汇编
javap -verbose	# 最常用，显示附加信息
```

如下反编译之前的helloworld程序

![](http://cdn.ziyedy.top/1%E3%80%81Java%EF%BC%9A%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5%E4%B8%8E%E8%AF%AD%E8%A8%80%E7%89%B9%E7%82%B9/javap.png)

而加上`-verbose`则可以显示详细信息，辅助代码分析

![](http://cdn.ziyedy.top/1%E3%80%81Java%EF%BC%9A%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5%E4%B8%8E%E8%AF%AD%E8%A8%80%E7%89%B9%E7%82%B9/javap-v.png)

## 注释

Java编译器在编译代码之前会把代码中的所有注释抹掉，字节码中不保留注释

1. 单行注释：`// `

2. 多行注释：`/* */`
3. 文档注释：`/** */`



## 常用关键字

### final——（最终，不可改变）

#### 修饰数据

> 声明数据为常量（不能再次赋值），可以是编译时常量，也可以是在运行时被初始化后不能被改变的常量。

- 对于基本类型，final 使数值不变；
- 对于引用类型，final 使引用不变，也就不能引用其它对象，但是被引用的对象本身是可以修改的。

#### 修饰方法

> 声明方法不能被子类重写。

其中，**private方法隐式的被指定为final**。如果在子类中定义的方法和父类中的一个private方法相同，则视为子类中定义了一个新方法。

#### 修饰类

> 声明类不能被继承

### static——（静态）

#### 静态变量

> 又称为类变量，类所有的实例都共享静态变量，可以直接通过类名来访问它（*只依赖于类存在，不依赖于对象实例存在*）。
>
> 静态变量在内存中**只存在一份**，所有的对象实例共享。

```
public class A() {
	private static int x;	// 静态变量
}
```

#### 静态方法

> 静态方法在类加载的时候就存在了，它**不依赖于任何实例**。所以静态方法**必须有实现**，也就是说它不能是抽象方法。
>
> 同时，静态方法*只能访问所属类的静态字段和静态方法*。
>
> main方法就是静态方法

**方法中不能有this和super关键字**（这两个关键字与具体对象关联，而静态方法是不依赖于实例的）

#### 静态语句块

> 只在类第一次被加载时调用（程序运行期间，这段代码只运行一次）
>
> 执行顺序：static块 > 普通匿名块 > 构造函数

```
public class test {
    static {
        System.out.println("gg");
    }

    public static void main(String[] args) {
    }
}
/////////////////////////////////////////// 
gg
```

#### 静态内部类（使用的很少）

非静态内部类依赖于外部类的实例，也就是说需要先创建外部类实例，才能用这个实例去创建非静态内部类。而静态内部类不需要。静态内部类不能访问外部类的非静态的变量和方法。



## 包

### package关键字

> package包与C++中的`namespace`类似
>
> Java中常用域名倒序的方法来定义包名，一个Java源文件中只能有一个package语句

### import关键字

> 用于引入Java不同包中的类，放置在`package`之后，类定义之前

### jar文件

> jar文件是一组class文件的压缩包，易于传播（其中可以包含很多Java类），且能够有效保护源码（其中不包含.java文件）
>
> 可以利用IDE打包jar文件，也可以使用`jar.exe`命令进行打包



> 参考：
>
> https://github.com/Snailclimb/JavaGuide
>
> 《Java核心技术卷1》
>
> mooc华东师范大学Java核心技术课程





