---
title: Java12：多线程与并发编程
date: 2021-01-03 13:58:43
tags:
	- 并发编程
categories:
	- Java
	- JavaSE
fileName: Java12-multithread-concurrency
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

实现Runnable接口，作为参数传递给Thread构造函数

```java
public class MyRunnable implements Runnable {
    @Override
    public void run() {
        // 线程体，线程要执行的任务
    }
}
```

### 两者比较

使用Runnable接口更好

* Java不支持双继承，如果继承Thread会降低其拓展性
* 从代码架构来说：使用Runnable能够解耦合（线程控制逻辑在Thread类中，业务运行逻辑在Runnable实现类中），并且实现Runnable的实例，可以被多个线程共享并执行
* 新建线程的损耗：使用Runnable只需要传入target，不需要反复创建与销毁线程



## 线程启动

> 调用start方法才是真正开始了一个线程的周期，而调用run方法则只是调用一个普通的方法

```java
MyThread myThread = new MyThread();
myThread.run();		// 未启动线程，只是调用了myThread实例的run方法
myThread.start();	// 启动线程
```

### Thread中start方法源码分析

start方法调用start0（start0是一个native方法，也称为JNI（Java Native Interface）方法），而start0方法在JVM中会调用run方法

```java
public synchronized void start() {
    // 检查线程的状态，判断是否可以启动
    if (threadStatus != 0)
        throw new IllegalThreadStateException();
    
    // 把线程加入到线程group中
    group.add(this);

    // 调用了start0()方法
    boolean started = false;
    try {
        start0();
        started = true;
    } finally {
        try {
            if (!started) {
                group.threadStartFailed(this);
            }
        } catch (Throwable ignore) {
        }
    }
}

private native void start0();
```

而run方法则会先判断target是否为空（target即为传入的Runnable对象），因此当传入Runnable对象后，该Thread类所执行的代码是传入的Runnable对象的run方法

```java
private Runnable target;	// 传入的 Runnable

@Override
public void run() {
    // 判断如果target不为空，则执行传入的target中的逻辑
    if (target != null) {
        target.run();
    }
}
```

如下，`start`方法执行总结如下：

![](http://cdn.ziyedy.top/Java12%EF%BC%9A%E5%A4%9A%E7%BA%BF%E7%A8%8B%E4%B8%8E%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B/start%E6%96%B9%E6%B3%95.jpg)



## 线程生命周期

* **New状态**：当一个 Thread 对象刚刚被创建时，状态为 NEW，此时与其他 Java 对象没有什么不同
* **RUNNABLE状态**：调用 `start` 方法后即进入 RUNNABLE 状态
* **RUNNING状态**：该状态得到CPU分配时即进入RUNNING状态
* **BLOCKED状态**：包括Blocked（被阻塞）、Waiting（等待）、Time_waiting（计时等待）等等
* **TERMINATED状态**：线程的终止状态，意味着线程的生命周期已经走完

### 线程状态的转换

![](http://cdn.ziyedy.top/Java12%EF%BC%9A%E5%A4%9A%E7%BA%BF%E7%A8%8B%E4%B8%8E%E5%B9%B6%E5%8F%91%E7%BC%96%E7%A8%8B/%E7%BA%BF%E7%A8%8B%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.png)



## Thread类常用方法

### currentThread()及线程对象常用方法

该方法返回的是代码实际运行时候的**线程对象实例**

*注：由于同一段代码可能被不同线程执行，因此当前线程是相对的*

```java
Thread.currentThread();					    // 获取当前线程
Thread.currentThread().getName();		    // 获取当前线程名称
Thread.currentThread().setName(线程名称);	 // 设置线程名称
Thread.currentThread().isAlive();			// 判断当前线程是否处于活动状态
Thread.currentThread().getId();				// 获得线程的唯一标识
```

### join()——阻塞线程（插队）

`t.join()`方法是阻塞调用此方法的线程进入`Timed_Waited`状态，直到线程t完成，此线程再继续。因此，**join()方法是阻塞别的线程，让自己先运行**，也就是插队

```java
MyThread myThread = new MyThread();
/**		启动线程	**/
myThread.join();	// 先执行完myThread再执行其他
```

### sleep()——让线程休眠

使当前线程（即调用`Thread.currentThread()`返回的线程）休眠指定的毫秒数

但在这段时间内，该线程持有的 monitor 锁并不会被放弃（所占有的资源并不会交还）

```java
Thread.sleep(10);	// 休眠10毫秒
```

### yield()——礼让线程

让当前正在执行线程暂停（即让出CPU资源），不是阻塞线程，而是将线程转入就绪（RUNNABLE）状态。

如果调用了yield方法之后，没有其他等待执行的线程，这个时候当前线程就会马上恢复执行

```java
Thread.yield();
```

### 设置线程优先级

用于设置/获得线程的优先级（所谓优先级只是获得CPU资源的*概率*），默认为5，最高为10，最低为1

```java
public int getPriority();	// 获取线程优先级
public void setPriority(int newPriority);	// 设置线程优先级
```

### setDaemon()——设置守护线程

守护线程即在后台运行的线程，比如JVM中的垃圾清理就是守护线程执行

当 JVM 中没有任何一个非守护线程时，所有的守护线程都会进入到 TERMINATED 状态，此时JVM 退出

可以将指定的线程设置成后台线程，创建后台线程的线程结束时，后台线程也随之消亡。

*只能在线程启动（即 start 方法调用前）之前把它设为后台线程*

```java
Thread t = new Thread();
t.setDaemon(true);
t.start();
```

### interrupt()——中断

并不是中断线程，而是让可中断方法（如sleep）中断

一旦调用了 interrupt 方法，那么在不进行处理的情况下，线程的 interrupted 状态会一直为 ture



## 其他线程控制方法

### wait/notify

这两类方法不在Thread上，而是在Object类中，所有Java类都会继承这两类方法

* 调用 `wait`方法：原本 RUNNING 的线程，进入 BLOCKING 状态，此线程会放弃原来持有的锁
* 调用 `notify` 方法：唤醒 wait 的线程，让其继续往下执行

方法 `wait()` 的作用是使当前执行代码的线程进行等待， **`wait()` 方法只能在同步方法中或同步块中调用**， `wait()` 方法执行后，当前线程释放锁，线程与其他线程竞争重新获取锁

方法 `notify()` 也要在同步方法或同步块中调用，该方法是用来通知那些可能等待该对象的对象锁的其他线程，对其发出通知notify，并使进入就绪态，等待获取锁。如果有多个线程等待，则有线程规划器随机挑选出一个呈wait状态的线程。在notify()方法后，当前线程不会马上释放该对象锁，要等到执行notify()方法的线程将程序执行完，也就是退出同步代码块中

## 线程基本使用

