---
title: Java12-2：synchronized关键字总结
date: 2021-01-13 16:41:06
tags:
	- 并发编程
categories:
	- Java
	- JavaSE
fileName: Java12-synchronized
---

使用 `synchronized` 修饰的作用域中的代码为同步执行的，也就是并发的情况下，执行到对**同一个对象加锁的 synchronized 代码块**时，为串行执行的

## synchronized 使用

### 1、同步代码块

小括号里的对象是可以是任意的对象，这个对象负责对想要执行同步代码块的线程进行“调度”

每个对其 synchronized 的线程，它都会记录下来，然后等到同步代码块没有线程执行的时候，它就会通知其它线程来执行同步代码块

```java
synchronized (lock) {
	// 同步代码
}
```

*不同类型的多个 Thread 如果有代码要同步执行，锁对象要使用所有线程共同持有的同一个对象*

### 2、修饰方法

2.1 修饰普通成员方法

此时锁对象就是`this`，所以以下两种写法等价

```java
public synchronized void eat(){
	.......
  .......
}

public void func(){
	synchronized(this){
		.......
  	.......
	}
}
```

2.2 修饰静态方法

此时锁对象为类的 Class 对象。如果静态方法所在的类为 Test。那么锁对象就是 Test.class

```java
class Test {
    public static synchronized void func(){
        // 锁对象为 Test.class
        // 同步方法体
    }
}
```

*注意：构造方法是不能使用 synchronized 关键字修饰的*



## synchronized 原理

普通的java对象能够完成同步，是因为每个对象都关联了一个 monitor lock（内置锁）。

当一个线程获取了 monitor lock 后，其它线程如果运行到获取同一个 monitor 的时候就会被 block 住。当这个线程执行完同步代码，则会释放 monitor lock。

每个 Java 对象在 JVM 的对等对象的头中保存锁状态，指向 ObjectMonitor。ObjectMonitor 保存了当前持有锁的线程引用，EntryList 中保存目前等待获取锁的线程，WaitSet 保存 wait 的线程。此外还有一个计数器，每当线程获得 monitor 锁，计数器 +1，当线程重入此锁时，计数器还会 +1。当计数器不为0时，其它尝试获取 monitor 锁的线程将会被保存到EntryList中，并被阻塞。当持有锁的线程释放了monitor 锁后，计数器 -1。当计数器归位为 0 时，所有 EntryList 中的线程会尝试去获取锁，但只会有一个线程会成功，没有成功的线程仍旧保存在 EntryList 中。**由此可以看出 monitor 锁是非公平锁**



使用`javap`命令进行反编译后，这两条指令就是上面所讲述的获取锁和释放锁的关键指令

```
15: monitorenter
......
128: monitorexit
```



## synchronized 使用注意



正确的编写方式应该把共享资源的操作放在 synchronized 代码块中

尽量把不需要同步的代码移出 synchronized 代码块

synchronized 关键字需要配合一个对象使用，这个对象可以是任何对象





1、synchronized 使用的为非公平锁，如果你需要公平锁，那么不要使用 synchronized。可以使用 ReentrantLock，设置为公平锁。关于 ReentrantLock，会在后面章节进行讲解；

2、锁对象不能为 null。如果锁对象为 null，何谈对象头，以及保存与其关联的 monitor 锁呢？所以代码中要确保synchronized使用的锁对象不为 null；

3、只把需要同步的代码放入 synchronized 代码块。如果不思考，为了线程安全把方法中全部代码都放入同步代码块，那么将会丧失多线程的优势。再多的线程也只能串行执行，这完全违背了并发的初衷；

4、只有使用同一个对象作为锁对象，才能同步。记住是同一个对象，而不是同一个类。有一种常犯的错误是，不同线程持有的是同一个类的不同实例。那么该对象实例用作锁对象的话，多个线程并不会同步。还一种错误是使用不同类的实例作为锁对象，但是期望不同位置的同步代码块能够同步执行。这是不可能达到你想要的效果的。

