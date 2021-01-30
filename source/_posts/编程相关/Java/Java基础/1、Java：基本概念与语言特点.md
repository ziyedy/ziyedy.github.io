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

即Java标准版

### Java EE（J2EE）



## Java语言特点

### 与C++的一些区别





### 编译与解释并存

Java程序要经过先编译、后解释两个步骤。（`.java`源文件先经过JDK中的`javac`命令**编译**生成字节码文件`*.class`，再经过JVM**解释**进行执行）



## 常用命令

### 命令行编译



```

```





```
javac Test.java
java Test
```

`javac`程序是一个Java编译器，将`*.java`编译成`*.class`（需要`.java`后缀）

`java`程序启动Java虚拟机（只需要指定类名）

### 反编译

使用`javap`能够对`.class`文件进行反编译，通过该命令能够有效了解JVM的内部原理

```
javap	# 对其进行反编译（得到近似的Java源码）
javap -c	# 对其进行反汇编
javap -verbose	# 最常用，显示附加信息
```







## 注释

Java编译器在编译代码之前会把代码中的所有注释抹掉，字节码中不保留注释

1. 单行注释：`// `

2. 多行注释：`/* */`
3. 文档注释：`/** */`



## 常用关键字

### final——（最终，不可改变）

#### 修饰数据

声明数据为常量，可以是编译时常量，也可以是在运行时被初始化后不能被改变的常量。

- 对于基本类型，final 使数值不变；
- 对于引用类型，final 使引用不变，也就不能引用其它对象，但是被引用的对象本身是可以修改的。

#### 修饰方法

声明方法不能被子类重写。

其中，**private方法隐式的被指定为final**。如果在子类中定义的方法和父类中的一个private方法相同，则视为子类中定义了一个新方法。

#### 修饰类

声明类不能被继承



### static——（静态）

#### 静态变量

又称为类变量，类所有的实例都共享静态变量，可以直接通过类名来访问它。静态变量在内存中只存在一份。

```
public class A() {
	private static int x;	// 静态变量
}
```

#### 静态方法

main方法就是

静态方法在类加载的时候就存在了，它**不依赖于任何实例**。所以静态方法**必须有实现**，也就是说它不能是抽象方法。

同时，静态方法只能访问所属类的静态字段和静态方法。

**方法中不能有this和super关键字**（这两个关键字与具体对象关联，而静态方法是不依赖于实例的）

#### 静态语句块

静态语句块在**类初始化**时运行一次（注意不是实例化运行）

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

#### 静态内部类

非静态内部类依赖于外部类的实例，也就是说需要先创建外部类实例，才能用这个实例去创建非静态内部类。而静态内部类不需要。静态内部类不能访问外部类的非静态的变量和方法。





> 参考：
>
> https://github.com/Snailclimb/JavaGuide
>
> 《Java核心技术卷1》









构造函数需要一个有参构造和一个无参构造



getter/setter





包：一个JAVA源文件中只能有一个package语句

命名方法：域名倒序+模块+功能











 

共有：public：任意位置

私有：private：本类中访问

保护：protected：当前类、同包子类/非子类、跨包子类（跨包子类不允许）

默认：当前类、同包子类（跨包子类不允许调用）



super：父类对象的引用

super.print() 访问父类成员方法

super.name 访问父类属性

super() 访问父类构造方法，必须位于子类构造方法的第一行



子类的构造过程必须调用其父类的构造方法，若子类构造方法中没有显式标注，则系统默认调用父类无参构造方法（若子类构造方法无显式标值且父类中没有无参构造方法，则编译出错）







多态：不同类的对象对同一消息做出不同响应

编译时多态：（方法重载）

运行时多态：程序运行时动态决定调用哪个方法



多态必要条件

1、满足继承关系

2、父类引用指向子类对象



向上转型，父类引用指向子类实例。可以调用子类重现父类的方法以及父类派生的方法，无法调用子类独有的方法

Animal two = new Cat()



向下转型，子类引用指向父类实例，此处要求必须进行强转

instanceof 运算符，返回 true false

```
if(obj instanceof Cat) {
	Cat temp = (Cat)obj
}
```



```
if() {
	return new Dog();
} else {
	return new Cat();
}
```





abstract关键字

抽象类，无法进行实例化的类（可以通过向上转型指向子类实例 ）

抽象方法：子类必须进行实现（或者子类也设置为抽象类），在父类定义时不能有方法体



包含抽象方法的类是抽象类

抽象类中可以没有抽象方法

static/final/private不能与abstract共存



接口：命名通常以“I”打头

接口定义了某一批类所需要遵守的规范

接口不关心这些类的内部数据，也不关心类中方法的实现细节，只规定这些类中必须提供某些方法





接口访问修饰符：public 默认

接口中抽象方法可以不写abstract关键字

作为接口的实现类必须实现接口中定义的所有方法，否则需要将该类设置为抽象类

接口中可以包含常量，默认public static final

```
public interface ITest {
    public void func();
}
```



```
public class A implements ITest {

    @java.lang.Override
    public void func() {

    }
}
```



```
INet net = new A();

```



default 关键字

```
public interface ITest {
	// 默认方法
	default void func1() {
		
	}
	// 静态方法
	static void func2() {
	
	}
}

// 调用接口中的默认方法
ITest.super.func1();
```

接口的继承，可以一对多



在Java中，可以将一个类定义在另一个类中或者一个方法中，这样的类称为内部类



1、成员内部类

获取内部类

```
A.a1 me = new A().new a1();
```



2、静态内部类

3、方法内部类

4、匿名内部类





