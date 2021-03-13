---
title: Java面向对象
date: 2020-07-04 10:43:51
tags:
	- Java
categories:
	- Java
	- Java基础
fileName: java-orient-object
---

# 面向对象三大特性

## 封装

封装是指把一个对象的状态信息（也就是属性）隐藏在对象内部，不允许外部对象直接访问对象的内部信息。但是可以提供一些可以被外界访问的方法来操作属性。

### 访问权限修饰符

Java 中有三个访问权限修饰符：private、protected 以及 public，如果不加访问修饰符，表示包级可见。**所以Java的访问权限应该有四个**

| 作用范围/关键字 | 当前类 | 同一package | 子孙类 | 其他package |
| :-------------: | :----: | :---------: | :----: | :---------: |
|     public      |   √    |      √      |   √    |      √      |
|    protected    |   √    |      √      |   √    |      ×      |
|  不使用关键字   |   √    |      √      |   ×    |      ×      |
|     private     |   √    |      ×      |   ×    |      ×      |

## 继承

> 继承是使用已存在的类的定义作为基础建立新类的技术，新类的定义可以增加新的数据或新的功能，也可以用父类的功能，但不能选择性地继承父类。

通过使用继承，可以快速地创建新的类，可以提高代码的重用，程序的可维护性，节省大量创建新类的时间 ，提高开发效率。同时，**Java语言中的继承是单继承**。

1、子类拥有父类对象所有的属性和方法（包括私有属性和私有方法），但是父类中的私有属性和方法子类是无法访问，**只是拥有**。

2、子类可以拥有自己属性和方法，即子类可以对父类进行扩展。

3、子类可以用自己的方式实现父类的方法（方法重写）。

### 继承的常用关键字

this：当前类对象的引用

super：父类对象的引用，可用于访问父类成员方法与属性

`super()` 访问父类构造方法，必须位于子类构造方法的**第一行**

子类的构造过程必须调用其父类的构造方法，若子类构造方法中没有显式标注，则系统默认调用父类无参构造方法（若子类构造方法无显式标值且父类中没有无参构造方法，则编译出错），因此Java中每个类中都应该尽量写一个无参构造函数。



## 多态

> 多态通俗意义上来讲就是：不同类的对象对同一消息做出不同响应，

可以分为：

* 编译时多态：即方法重载
* 运行时多态：程序运行时动态决定调用哪个方法

### 向上转型

> 向上转型，即父类（或者父接口）引用指向子类实例，是Java语言中多态的常见表现形式

如下即为向上转型的示例，声明的是父类（父接口），实际指向的是子类的一个对象。这使得对象既可以**使用子类强大的功能**，又可以**抽取父类的共性**。

```
List l1 = new ArrayList();
List l2 = new LinkedList();
```

### 向上转型规则

向上转型**可以调用子类重现父类的方法以及父类派生的方法，无法调用子类独有的方法**，示例如下

```java
/**
 * 父类
 */
public class Father {
    // 父类中的func1方法
    // 由于子类中对该方法只是进行了重载而没有进行重写
    // 因此该方法仍然有效
    public void func1() {
        System.out.println("Father func1");
    }

    // 父类中的func2方法
    // 由于在子类中重写了该方法
    // 所以在“向上转型”中，该方法不再有效
    // 取而代之的是子类中的fun2方法
    public void func2() {
        System.out.println("Father func2");
    }
}

/**
 * 子类
 */
public class Child extends Father {
    // 对func1方法的一个重载
    // 由于在父类中没有定义该方法，所以该方法不能被父类的引用调用
    // 如果进行调用即会报错
    public void func1(int i) {
        System.out.println("Child func1");
    }

    // 对父类方法的重写
    // 能够起作用
    public void func2() {
        System.out.println("Child func2");
    }
}

/**
 * 主函数
 */
public class Main {
    public static void main(String[] args) {
	// write your code here
        Father test = new Child();
        test.func1();   // Father func1
        test.func2();   // Child func2
        test.func1(10); // 报错
    }
}
```

### 向下转型

> 向下转型，子类引用指向父类实例，此处要求必须进行强转

Java中向上转型是安全的，由JVM自动实现，而向下转型是不安全的，需要强制类型转换。

```java
Father father = new Father();
if(father instanceof Child) {	// instanceof 运算符，返回 true false
	Child temp = (Child)father;	// 强制类型转换
}
```



# 嵌套类

> 一个类定义在另一个类（或方法）内部

嵌套类的作用：

1、实现更细粒度的访问权限控制

2、避免过多的类定义

## 静态嵌套类

> 第三方需要通过外部包围类才能够访问

```java
public class Outer {
    // 静态嵌套类
    static class Inner {
        
    }
}
```

## 非静态嵌套类（内部类）

### 普通内部类（成员内部类）

> 定义后在类中均可以使用，类似于结构体的作用

```java
public class Outer {
    // 成员内部类
    public class Inner {
        
    }
}
```

### 局部内部类

> 定义在代码块（如方法、for/if语句中）中的非静态的类
>
> 只存活于该代码块内部，**代码块结束后外界无法使用该类**

```java
public class Outer {
    public void func() {
        // 局部内部类
        class Inner {
            
        }
    }
}
```

### 匿名内部类

> 没有类名的内部类，必须继承一个父类/实现一个父接口，同时在实例化后迅速转为父类/父接口

匿名内部类通常只使用一次

```java
public class Outer {
    public void func() {
        // 匿名内部类
        class Inner {
            new Thread(new Runnable() {
                public void run() {
                    
                }
            }).start();
        }
    }
}
```

## 四种嵌套类的比较

|            | 访问权限修饰 | 作用范围                       | 可访问外部包围类内容   | 和外部类关系                            |
| ---------- | ------------ | ------------------------------ | ---------------------- | --------------------------------------- |
| 匿名内部类 | 无           | 所在代码块内，外部无法访问     | 访问外部的所有成员     | 在外部类对象内部                        |
| 局部内部类 | 无           | 所在代码块内，外部无法访问     | 访问外部的所有成员     | 在外部类对象内部                        |
| 普通内部类 | 有           | 包围类内可以访问，外部可以访问 | 访问外部的所有成员     | 外界可以new，但必须依附于一个外部类对象 |
| 静态内部类 | 有           | 包围类内可以访问，外部可以访问 | 访问外部的所有静态成员 | 外界可以new，可独立进行工作             |



# 方法

## 参数传递方式

### Java形参的传递方法（值传递）

> 总体而言，Java总是采用按值传递的方式进行传参，即*方法得到的是所有参数值的一个拷贝*，方法不能修改传递给它的任何参数的内容

**1、一个方法不能修改一个基本数据类型的参数**

示例：

```
public static void main(String[] args) {
    int num1 = 10;
    int num2 = 20;

    swap(num1, num2);

    System.out.println("num1 = " + num1);
    System.out.println("num2 = " + num2);
}

public static void swap(int a, int b) {
    int temp = a;
    a = b;
    b = temp;

    System.out.println("a = " + a);
    System.out.println("b = " + b);
}
```

结果：

```
a = 20
b = 10
num1 = 10
num2 = 20
```

说明：

在 swap 方法中，a、b 的值进行交换，并不会影响到 num1、num2。因为，a、b 中的值，只是从 num1、num2 的复制过来的（**值传递**）。也就是说，a、b 相当于 num1、num2 的副本，副本的内容无论怎么修改，都不会影响到原件本身。

**2、一个方法可以改变一个对象参数的状态**

示例：

```
public static void main(String[] args) {
	int[] arr = { 1, 2, 3, 4, 5 };
	System.out.println(arr[0]);
	change(arr);
	System.out.println(arr[0]);
}

public static void change(int[] array) {
	// 将数组的第一个元素变为0
	array[0] = 0;
}
```

结果：

```
1
0
```

说明：

array 被初始化 arr 的拷贝也就是一个对象的引用，也就是说 array 和 arr 指向的是同一个数组对象。 因此，外部对引用对象的改变会反映到所对应的对象上（类似于C++ 的引用传递）。



**3、一个方法不能让对象参数引用一个新的对象**

示例：

```
public class Test {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		Student s1 = new Student("小张");
		Student s2 = new Student("小李");
		Test.swap(s1, s2);
		System.out.println("s1:" + s1.getName());
		System.out.println("s2:" + s2.getName());
	}

	public static void swap(Student x, Student y) {
		Student temp = x;
		x = y;
		y = temp;
		System.out.println("x:" + x.getName());
		System.out.println("y:" + y.getName());
	}
}
```

结果：

```
x:小李
y:小张
s1:小张
s2:小李
```

说明：

方法并没有改变存储在变量 s1 和 s2 中的对象引用。swap 方法的参数 x 和 y 被初始化为两个对象引用的拷贝，这个方法交换的是这两个拷贝



### 深拷贝、浅拷贝

1、**浅拷贝**：对基本数据类型进行值传递，对引用数据类型进行引用传递般的拷贝，此为浅拷贝。

2、**深拷贝**：对基本数据类型进行值传递，对引用数据类型，创建一个新的对象，并复制其内容，此为深拷贝。

![](http://cdn.ziyedy.top/4%E3%80%81Java%EF%BC%9A%E7%B1%BB%E3%80%81%E5%AF%B9%E8%B1%A1%E4%B8%8E%E6%96%B9%E6%B3%95/java-deep-and-shallow-copy.jpg)



## 方法重载（Overload）

> 同样的方法根据输入数据的不同，做出不同处理（同一个类中多个同名方法根据不同的传参来执行不同的逻辑处理）
>
> **每个重载的方法都有独一无二的参数列表**，包括参数的类型、顺序、参数数量等等
>
> 重载是发生在**编译时**的，由编译器根据参数的类型来选择使用哪个方法

### 重载的条件

1. 位于同一个类中，且方法名必须相同
2. 参数列表必须不同（个数、类型、参数类型排列顺序不同）
3. 返回值可以不同，仅返回值不同不能构成重载

4. 可以有不同的访问修饰符，可以抛出不同的异常



## 方法重写（Override）

> 重写发生在**运行期**，是子类对父类的允许访问的方法的实现过程进行重新编写（重写就是**子类对父类方法的重新改造**，外部样子不能改变，内部逻辑可以改变）

### 重写的原则

1. 发生有继承关系的*父类与子类*中，且父类方法的*访问修饰符为private/final/static则表示该方法不能重写*，但是使用static修饰的方法能够被再次声明，同时构造方法不能被重写。
2. 重写的方法必须要和父类保持一致

3. 子类方法的*访问权限必须大于等于*父类方法；

4. 子类方法的返回类型必须是父类方法返回类型或为其子类型。

5. 子类方法抛出的异常类型必须是父类抛出异常类型或为其子类型。

### 示例

```java
/**
 * 1、子类的访问权限public > 父类访问权限protected
 * 2、子类返回值是父类接口的一个类实现（父类的子类型）
 * 3、子类抛出的异常为父类抛出异常的子类
 * 使用 @Override 注解，可以让编译器帮忙检查是否满足上面的限制条件*
 */
class SuperClass {
    protected List<Integer> func() throws Throwable {
        return new ArrayList<>();
    }
}

class SubClass extends SuperClass {
    @Override
    public ArrayList<Integer> func() throws Exception {
        return new ArrayList<>();
    }
}
```



# 所有类的父类——Object

## 主要方法

**Object类是所有类的父类**，主要提供11个方法。

其中native方法即为调用非Java代码的接口，一个native方法是指该方法的实现由非Java语言实现。

```
// 用于返回当前运行时对象的Class对象。
public final native Class<?> getClass()

// 用于返回对象的哈希码，主要使用在哈希表中，比如JDK中的HashMap
public native int hashCode()

// 用于比较2个对象的内存地址是否相等，String类对该方法进行了重写用户比较字符串的值是否相等
public boolean equals(Object obj)

// 用于创建并返回当前对象的一份拷贝。
// 一般情况下，对于任何对象 x
// 表达式 x.clone() != x 为true
// x.clone().getClass() == x.getClass() 为true
// Object本身没有实现Cloneable接口，所以不重写clone方法并且进行调用的话会产生异常。
protected native Object clone() throws CloneNotSupportedException

// 返回类的名字@实例的哈希码的16进制的字符串。建议Object所有的子类都重写这个方法。
public String toString()

// 不能重写。唤醒一个在此对象监视器上等待的线程(监视器相当于就是锁的概念)。如果有多个线程在等待只会任意唤醒一个。
public final native void notify()

// 不能重写。跟notify一样，唯一的区别就是会唤醒在此对象监视器上等待的所有线程，而不是一个线程。
public final native void notifyAll()

// 不能重写。暂停线程的执行。注意：sleep方法没有释放锁，而wait方法释放了锁 。timeout是等待时间。
public final native void wait(long timeout) throws InterruptedException

// 多了nanos参数，这个参数表示额外时间（以毫微秒为单位，范围是 0-999999）。 所以超时的时间还需要加上nanos毫秒。
public final void wait(long timeout, int nanos) throws InterruptedException

// 跟之前的2个wait方法一样，只不过该方法一直等待，没有超时时间这个概念
public final void wait() throws InterruptedException

// 实例被垃圾回收器回收的时候触发的操作
protected void finalize() throws Throwable

```

## 常见问题

### ==与equals

* **==**：判断两个对象在内存中的地址是不是相等（基本数据类型==比较的是值，引用数据类型==比较的是内存地址）
* **equals()**：通常用于判断两个对象的“值”是否相等（若类没有重写该方法，则等价于使用“==”，但通常都会对equals进行重写）

### hashCode与equals

* 使用`equals()`进行对比是绝对可靠的：equals()相等的两个对象它们的hashCode()也一定相等

* 使用`hashCode()`进行对比不是绝对可靠的：hashCode()相等的两个对象equals()不一定相等（有时不同对象hashcode会一样）

在使用`Set`等hash结构的类中存储该类时，**equals 方法被重写，则 hashCode 方法也必须被重写**

如`String`重写了这两个方法，则可以在`HashMap`等类似结构中使用`String`对象。



参考：

> https://github.com/Snailclimb/JavaGuide