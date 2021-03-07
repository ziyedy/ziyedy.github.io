---
title: Java线程概述
date: 2021-01-03 13:58:43
tags:
categories:
fileName:
---

## 线程创建

### 继承Thread类

Thread类是Java提供的线程顶级类，继承Thread类可快速定义线程。

```java
public class MyThread extends Thread {
    @Override
    public void run() {
		// 线程体，线程要完成的任务，要执行的代码
    }
}
```

### 实现Runnable接口

```java
public class MyRunnable implements Runnable {
    @Override
    public void run() {
        // 线程体，线程要执行的任务
    }
}
```

## 线程启动

实现Runnable接口	（最终调用target.run()方法）

继承Thread类	（run()整个被重写）

使用Runnable接口更好

从代码架构来说：解耦合

新建线程的损耗

Java不支持双继承

调用start方法才是真正开始了一个线程的周期，而调用run方法则只是调用一个普通的方法



## 线程生命周期

New

Runnable

Blocked

Waiting

Timed Waited

Terminated

一般习惯把Blocked（被阻塞）、Waiting（等待）、Time_waiting（计时等待）都称为阻塞状态





## 线程常用方法

### currentThread()及线程对象常用方法

该方法返回的是代码实际运行时候的**线程对象**

由于同一段代码可能被不同线程执行，因此当前线程是**相对的**

```java
Thread.currentThread();					    // 获取当前线程
Thread.currentThread().getName();		    // 获取当前线程名称
Thread.currentThread().setName(线程名称);	 // 设置线程名称
Thread.currentThread().isAlive();			// 判断当前线程是否处于活动状态
Thread.currentThread().getId();				// 获得线程的唯一标识
```



## 线程控制方法

### join()——阻塞线程

`t.join()`方法是阻塞调用此方法的线程进入`Timed_Waited`状态，直到线程t完成，此线程再继续

因此，**join()方法是阻塞别的线程，让自己先运行**

### sleep()——让线程休眠

使当前线程（即调用`Thread.currentThread()`返回的线程）休眠指定的毫秒数，让出CPU

```java
Thread.sleep(10);	// 休眠10毫秒
```



### yield()——礼让线程

让当前正在执行线程暂停，不是阻塞线程，而是将线程转入就绪状态。

如果调用了yield方法之后，没有其他等待执行的线程，这个时候当前线程就会马上恢复执行

### setDaemon()

可以将指定的线程设置成后台线程，创建后台线程的线程结束时，后台线程也随之消亡。

只能在*线程启动之前*把它设为后台线程 



### interrupt()



