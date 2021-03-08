---
title: Java：反射
date: 2021-01-27 17:26:30
tags:
	- Java
categories:
	- Java
	- Java基础
fileName: Java-reflection
---

# 反射的基本概念

## 反射（reflective）

程序可以访问、检测和修改它本身状态和行为的能力（自描述和自控制），称为反射，在`java.lang.reflect`包中。

即在运行时加载、探知和使用编译期间完全未知的类，能够**增加动态语言特性，弥补强动态语言的不足**

注：直观来说就是可以将类名以字符串形式传入，从而达到动态修改程序的目的

## 反射机制的作用

- 在运行时分析类的能力
- 在运行时查看和操作对象（比如编写一个适用于所有类的toString方法）
- 实现泛型数组操作代码
- 利用Method对象（类似于C++中的函数指针）

## 创建对象的方法

### 方法1：静态编译

即传统的使用`new`关键字创建对象

```
A a = new A();
```



### 方法2：克隆

即实现`Cloneable`接口，直接拷贝内存中的相关信息，不再调用构造函数

```
class A implements Cloneable {

}

A a1 = new A();
A a2 = (A) a1.clone();
```



### 方法3：反射

使用反射直接构建类对象

```
A a = (A) Class.forName("A").newInstance();
```

使用构造器构造类对象

```
Constructor<A> constructor = A.class.getConstructor();
A a = constructor.newInstance();
```





# 反射的关键类与方法

## Class类

在Java程序运行时，JVM为每个对象维护一个*运行时类型标识*，该信息追踪每个对象所属的类，而JVM则利用该信息选择要执行的正确的方法。

> 可以使用`Class`类访问这些信息，而Object类中的`getClass()`方法则会返回一个Class类型的实例

## Class类的获取

*Class类实际上是一个泛型类，`A.class`的类型是`Class<A>`*，主要有以下三种获取Class类对象的方法

### 使用`getClass()`方法

```
String s = "123";
Class c = s.getClass();
System.out.println(c.getName()); # java.lang.String
```

### 使用`forName()`方法

若类名保存在一个字符串中，即可以使用该方法。使用该方法时必须提供一个异常处理器（用于应对输入字符串不是一个类的情况）

```
Class c = Class.forName("java.lang.String");
System.out.println(c.getName()); # java.lang.String
```

### 使用`.class`

```
Class c = String.class;
System.out.println(c.getName()); # java.lang.String
```



## Class类中常用方法

```
// 获取Target类的class对象
Class c = Target.class;
```

### 成员变量（Field）

> 在运行时获取到类的所有成员变量，还可以给成员变量赋值和获取成员变量的值。

#### 获取成员变量

```
// 获取所有public修饰的成员变量
Field[] fields1 = c.getFields();

// 获取所有声明的成员变量
Field[] fields2 = c.getDeclaredFields();	

// 获取变量名为value1的变量
Field field3 = c.getField("value1");
Field field4 = c.getDeclaredField("value1");
```

#### 获取变量类型

```
Object fieldType = field3.getType();
```

#### 成员变量赋值与取值

> 使用`Field`中的`set`与`get`方法

```
Target t = new Target();	// 获取target对象
Object value = filed3.get(t);	// 传入对象，获取成员变量值
field3.setAccessible(true);	// 为了对类中的参数进行修改取消安全检查（针对私有变量）
field3.set(t, 1)	// 将该值设置成1
```

### 成员方法（Method）

#### 获取成员方法

> 获取方法数组无法获取到继承父类的方法

```
// 获取所有public修饰的方法
Method[] methods = c.getMethods();	

// 获取所有声明的成员方法
Method[] methods1 = c.getDeclaredMethods();

// 获取无参的method方法
Method method = birdClass.getMethod("method", null);

// 获取参数为指定类型的method方法
Method method1 = birdClass.getDeclaredMethod("method", new Class[]{int.class});
```

#### 获取方法参数与与返回类型

```
// 获取参数列表
Class[] parameterTypes = method.getParameterTypes();

// 获取返回类型
Class returnType = method.getReturnType();
```

#### invoke()方法

> 只要通过反射获取到方法名之后，就可以使用`invoke()`调用对应的方法
>
> 参数1为调用方法的对象，参数2而调用方法的参数
>
> 当调用static方法时，参数1为null

```
Target t = new Target();	// 获取target对象
method.invoke(t, “123”);	// 调用method方法，传入参数“123”
```



### 构造方法（Constructor）

```
Constructor[] constructors = c.getConstructors();
```



其他获取父接口等等暂略



## 应用举例





# 反射的应用举例

暂略





> 参考：
>
> 《Java核心技术卷1》
>
> MOOC华东师范大学Java核心技术课程
>
> https://zhuanlan.zhihu.com/p/80519709