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

### 面向对象三大特征

#### 封装

封装是指把一个对象的状态信息（也就是属性）隐藏在对象内部，不允许外部对象直接访问对象的内部信息。但是可以提供一些可以被外界访问的方法来操作属性。

#### 继承

继承是使用已存在的类的定义作为基础建立新类的技术，新类的定义可以增加新的数据或新的功能，也可以用父类的功能，但不能选择性地继承父类。通过使用继承，可以快速地创建新的类，可以提高代码的重用，程序的可维护性，节省大量创建新类的时间 ，提高我们的开发效率。同时，**Java语言中的继承是单继承**。

1、子类拥有父类对象所有的属性和方法（包括私有属性和私有方法），但是父类中的私有属性和方法子类是无法访问，**只是拥有**。

2、子类可以拥有自己属性和方法，即子类可以对父类进行扩展。

3、子类可以用自己的方式实现父类的方法（方法重写）。

#### 多态





### Object类的常见方法总结

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