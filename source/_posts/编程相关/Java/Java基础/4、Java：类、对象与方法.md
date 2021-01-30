---
title: Java面向对象
date: 2020-07-04 10:43:51
tags:
	- Java
categories:
	- 编程相关
	- Java
	- Java基础
fileName: java-orient-object
---

## 面向对象三大特征

### 封装

封装是指把一个对象的状态信息（也就是属性）隐藏在对象内部，不允许外部对象直接访问对象的内部信息。但是可以提供一些可以被外界访问的方法来操作属性。

### 继承

继承是使用已存在的类的定义作为基础建立新类的技术，新类的定义可以增加新的数据或新的功能，也可以用父类的功能，但不能选择性地继承父类。通过使用继承，可以快速地创建新的类，可以提高代码的重用，程序的可维护性，节省大量创建新类的时间 ，提高我们的开发效率。同时，**Java语言中的继承是单继承**。

1、子类拥有父类对象所有的属性和方法（包括私有属性和私有方法），但是父类中的私有属性和方法子类是无法访问，**只是拥有**。

2、子类可以拥有自己属性和方法，即子类可以对父类进行扩展。

3、子类可以用自己的方式实现父类的方法（方法重写）。

### 多态



## 方法

#### 参数

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



#### 深拷贝、浅拷贝

1、**浅拷贝**：对基本数据类型进行值传递，对引用数据类型进行引用传递般的拷贝，此为浅拷贝。

2、**深拷贝**：对基本数据类型进行值传递，对引用数据类型，创建一个新的对象，并复制其内容，此为深拷贝。

![](C:\Users\lenovo\Desktop\myblog\ziyedy.github.io\source\_posts\编程相关\Java\Java基础\1、Java基础语法\java-deep-and-shallow-copy.jpg)



### 方法重载（Overload）

同样的方法根据输入数据的不同，做出不同处理（同一个类中多个同名方法根据不同的传参来执行不同的逻辑处理）。满足如下要求：

a、同一个类中

b、方法名相同，参数列表不同，方法返回值相同

c、访问修饰符。



### 方法重写（Override）

重写发生在**运行期**，是子类对父类的允许访问的方法的实现过程进行重新编写（重写就是**子类对父类方法的重新改造**，外部样子不能改变，内部逻辑可以改变）。满足以下条件：

1、发生有继承关系的子类中，且父类方法的**访问修饰符为private/final/static则表示该方法不能重写**，但是使用static修饰的方法能够被再次声明。同时构造方法不能被重写。

2、子类方法的**访问权限必须大于等于**父类方法；

3、子类方法的返回类型必须是父类方法返回类型或为其子类型。

4、子类方法抛出的异常类型必须是父类抛出异常类型或为其子类型。

```
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

如上代码段，子类的访问权限public>父类访问权限protected；子类返回值是父类接口的一个类实现（父类的子类型）；子类抛出的异常为父类抛出异常的子类。

*注：使用 @Override 注解，可以让编译器帮忙检查是否满足上面的限制条件*







## 所有类的父类——Object



**Object类是所有类的父类**，主要提供11个方法。

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

其中native方法即为调用非Java代码的接口，一个native方法是指该方法的实现由非Java语言实现。



### 访问权限修饰符

Java 中有三个访问权限修饰符：private、protected 以及 public，如果不加访问修饰符，表示包级可见。**所以Java的访问权限应该有四个**











主要参考：https://github.com/Snailclimb/JavaGuide