---
title: Java12-3：Lock
date: 2021-03-13 17:59:41
tags:
	- 并发编程
categories:
	- Java
	- JavaSE
fileName: Java12-
---

#



`java.util.concurrent.locks` 包下的 Lock 接口和它的实现 ReentrantLock

ReentrantLock 也是互斥锁，有如下三种互斥情况：读/写、写/写、读/读。



显式锁：

在代码中需要主动的去进行 lock 操作



```java
Lock lock = new ReentrantLock();
lock.lock();	// 加锁
try {
	// code
} finally {
	lock.unlock();	// 解锁
}
```

相对于`synchronized`，`ReentrantLock` 更为灵活，它能够支持公平和非公平锁两种形式。只需要在声明的时候传入 true。

默认的无参构造方法则会创建非公平锁

```java
Lock lock = new ReentrantLock(true);	// 创建公平锁
```



### 加锁操作

1、使用`lock.lock()`，此时加锁操作是阻塞的，直到获取锁才会继续向下进行

2、使用`tryLock`，调用后会立即（也可以通过传入时间和单位，来控制等待获取锁的时长）返回获取锁的情况，获取为 true，未能获取为 false，因此能够根据结果进行进一步的处理

```java
if(lock.tryLock(1, TimeUnit.SECONDS)){
    try {
        doSomething();
    } catch (InterruptedException e) {
        e.printStackTrace();
    } finally {
        lock.unlock();
    }
}else{
    doSomethingElse();
}
```



## lock 方法源码分析



```java
// 无参构造函数
public ReentrantLock() {
    sync = new NonfairSync();
}

// 有参构造函数
public ReentrantLock(boolean fair) {
    sync = fair ? new FairSync() : new NonfairSync();
}
```

FairSync 和 NonFairSync 都继承自 Sync



其中`AbstractQueuedSynchronizer`就是所谓AQS

AQS 依赖先进先出队列实现了阻塞锁和相关的同步器（信号量、事件等）

AQS 内部有一个 volatile 类型的 state 属性，实际上多线程对锁的竞争体现在对 state 值写入的竞争。一旦 state 从 0 变为 1，代表有线程已经竞争到锁，那么其它线程则进入等待队列。等待队列是一个链表结构的 FIFO 队列，这能够确保公平锁的实现。同一线程多次获取锁时，如果之前该线程已经持有锁，那么对 state 再次加 1。释放锁时，则会对 state-1。直到减为 0，才意味着此线程真正释放了锁。



FairSync 实现的 lock 代码很简单

```java
final void lock() {
    acquire(1);
}
```





## ReadWriteLock 简介

ReadWriteLock 为我们提供了读写之间不同互斥策略的锁。因此，当程序有频繁的读操作时，他能够带来更好的性能

由于读写控制的策略不一样，带来了锁内部的复杂度。所以如果你程序的读操作并没有达到一定数量，反而使用读写锁会比互斥锁性能更差



ReadWriteLock 顾名思义读写锁，也就是说同一个锁对读和写的上锁方式是不一样的写锁的互斥性更高。

锁降级：如果线程持有写锁，如果可以在不释放写锁的情况下，获取读锁，这就是锁降级。ReadWriteLock 是支持锁降级的

锁升级：如果线程持有读锁，那么他是否可以不释放读锁，直接获取写锁。这意味着从一个低级别的锁升级到高级别的锁。其实就是变相的插队，无视其它在排队等待写锁的线程。ReadWriteLock 并不支持锁升级。



```java
ReadWriteLock lock = new ReentrantReadWriteLock();
Lock readLock = lock.readLock();
Lock writeLock = lock.writeLock();

writeLock.lock();
System.out.println("got the write lock");	// 成功输出
readLock.lock();
System.out.println("got the read lock");	// 成功输出
```



```java
ReadWriteLock lock = new ReentrantReadWriteLock();
Lock readLock = lock.readLock();
Lock writeLock = lock.writeLock();

readLock.lock();
System.out.println("got the read lock");	// 成功输出
writeLock.lock();
System.out.println("got the write lock");	// 未输出
```







