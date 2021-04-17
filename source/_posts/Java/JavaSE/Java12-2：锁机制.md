---
title: Java12-2：锁机制
date: 2021-01-13 16:41:06
tags:
	- 并发编程
categories:
	- Java
	- JavaSE
fileName: Java12-lock
---

# synchronized

使用 `synchronized` 修饰的作用域中的代码为同步执行的，也就是并发的情况下，执行到对**同一个对象加锁**的 synchronized 代码块时，为串行执行的

## synchronized 使用

### 1、同步代码块

小括号里的对象是可以是任意的对象，这个对象负责对想要执行同步代码块的线程进行“调度”，起到对象锁的作用

每个对其 synchronized 的线程，它都会记录下来，然后等到同步代码块没有线程执行的时候，它就会通知其它线程来执行同步代码块

```java
Lock lock = new Lock();	// 此处可以是任意对象

// 对象锁
synchronized (lock) {
}

// 类锁
synchronized (Lock.class) {
}
```

*不同类型的多个 Thread 如果有代码要同步执行，锁对象要使用所有线程共同持有的同一个对象*

### 2、修饰方法

#### 2.1 修饰普通成员方法

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

#### 2.2 修饰静态方法

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

### 1、synchronized 同步语句块原理

```java
public class SynchronizedDemo {
    public void method() {
        synchronized (this) {
            System.out.println("synchronized 代码块");
        }
    }
}
```

使用`javap`命令进行反编译后可以得到：

<img src="http://cdn.ziyedy.top/Java12-2%EF%BC%9A%E9%94%81%E6%9C%BA%E5%88%B6%E7%9A%84%E4%BD%BF%E7%94%A8%E4%B8%8E%E5%AE%9E%E7%8E%B0/%E5%90%8C%E6%AD%A5%E8%AF%AD%E5%8F%A5%E5%9D%97%E5%8E%9F%E7%90%86.png" style="zoom:80%;" />

当执行 `monitorenter` 指令时，线程试图获取锁也就是获取 **对象监视器 `monitor`** 的持有权。

`synchronized` 同步语句块的实现使用的是 `monitorenter` 和 `monitorexit` 指令，其中 `monitorenter` 指令指向同步代码块的开始位置， `monitorexit` 指令则指明同步代码块的结束位置

在执行`monitorenter`时，会尝试获取对象的锁，如果锁的计数器为 0 则表示锁可以被获取，获取后将锁计数器设为 1 也就是加 1。

在执行 `monitorexit` 指令后，将锁计数器设为 0，表明锁被释放。如果获取对象锁失败，那当前线程就要阻塞等待，直到锁被另外一个线程释放为止。

在 Java 虚拟机(HotSpot)中，Monitor 是基于 C++实现的，由ObjectMonitor实现的。每个对象中都内置了一个 `ObjectMonitor`对象（在Java对象头中，维护了所需要的各类信息，如当前持有锁的对象，以及其他等待队列等）。

另外，`wait/notify`等方法也依赖于`monitor`对象，这就是为什么只有在同步的块或者方法中才能调用`wait/notify`等方法，否则会抛出`java.lang.IllegalMonitorStateException`的异常的原因。

### 2、synchronized 修饰方法原理

```java
public class SynchronizedDemo {
    public synchronized void method() {
        System.out.println("synchronized 方法");
    }
}
```

使用`javap`命令进行反编译后可以得到：

<img src="http://cdn.ziyedy.top/Java12-2%EF%BC%9A%E9%94%81%E6%9C%BA%E5%88%B6%E7%9A%84%E4%BD%BF%E7%94%A8%E4%B8%8E%E5%AE%9E%E7%8E%B0/%E5%90%8C%E6%AD%A5%E6%96%B9%E6%B3%95%E5%8E%9F%E7%90%86.png" style="zoom:80%;" />

`synchronized` 修饰的方法并没有 `monitorenter` 指令和 `monitorexit` 指令，取而代之的是 `ACC_SYNCHRONIZED` 标识，该标识指明了该方法是一个同步方法。JVM 通过该 `ACC_SYNCHRONIZED` 访问标志来辨别一个方法是否声明为同步方法，从而执行相应的同步调用。



**总结：两者的本质都是对对象监视器 monitor 的获取。同时 monitor 锁是非公平锁**

## synchronized 注意事项

1、synchronized 使用的为非公平锁，如果需要公平锁，可以使用 ReentrantLock，设置为公平锁

2、锁对象不能为 null。因为锁对象为 null，就不存在对象头，以及与其关联的 monitor 锁

3、由于加锁的开销很大，所以应该把共享资源的操作放在 synchronized 代码块中，而尽量把不需要同步的代码移出 synchronized 代码块



# Lock显式锁

`java.util.concurrent.locks` 包下的 `Lock` 接口和它的一些实现类如 `ReentrantLock`

显式锁：在代码中需要主动的去进行 lock 操作

## ReentrantLock

ReentrantLock 也是互斥锁，有如下三种互斥情况：读/写、写/写、读/读。

相对于`synchronized`，`ReentrantLock` 更为灵活，它能够支持公平和非公平锁两种形式

默认的无参构造方法则会创建非公平锁，要创建公平锁需要在声明的时候传入 true

```java
Lock lock = new ReentrantLock(true);	// 创建公平锁
```

### 基本使用

基本使用就是使用`lock`加锁，然后使用`unlock`解锁（建议使用`finally`避免出现异常锁没有释放掉）

```java
Lock lock = new ReentrantLock();
lock.lock();	// 加锁
try {
	// 操作代码
} finally {
	lock.unlock();	// 解锁
}
```

### 使用`tryLock`完成加锁操作

使用`lock.lock()`进行加锁操作是阻塞的，直到获取锁才会继续向下进行

而使用`tryLock`，调用后会立即（也可以通过传入时间和单位，来控制等待获取锁的时长）返回获取锁的情况，获取为 true，未能获取为 false，因此能够*根据结果进行进一步的处理*

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



## `lock` 方法源码解析

如下可见，`lock` 方法通过 `ReentrantLock ` 内置的 `syn` 对象上锁，而 `syn`对象则是在 `ReentrantLock ` 的构造方法中创建

```java
public void lock() {
    sync.lock();
}
```

### ReentrantLock 的构造方法

如下可见 `ReentrantLock` 的构造方法

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

如下：`FairSync` 和 `NonFairSync` 都继承自 `Sync`，而`Sync`继承自`AbstractQueuedSynchronizer`，即所谓的**AQS**

![](http://cdn.ziyedy.top/Java12-2%EF%BC%9A%E9%94%81%E6%9C%BA%E5%88%B6%E7%9A%84%E4%BD%BF%E7%94%A8%E4%B8%8E%E5%AE%9E%E7%8E%B0/Syn%E7%9A%84%E7%BB%A7%E6%89%BF%E5%85%B3%E7%B3%BB.jpg)

### AQS

AQS 的实现思想就是：

* 若资源空闲，则将当前请求的线程设置为有效工作线程，并将共享资源设置为锁定状态
* 若资源被占用，则使用一套线程等待阻塞等待以及锁分配机制，该机制AQS使用一个**虚拟双向队列**实现

通俗而言，就是AQS使用 `volatile ` 去修饰共享变量 state，线程通过CAS去改变状态符，成功则获取共享内容，失败则进入等待队列，等待被唤醒，如下图所示：

<img src="http://cdn.ziyedy.top/Java12-2%EF%BC%9A%E9%94%81%E6%9C%BA%E5%88%B6%E7%9A%84%E4%BD%BF%E7%94%A8%E4%B8%8E%E5%AE%9E%E7%8E%B0/AQS.jpg" style="zoom:80%;" />

AQS 底层使用**模板方法模式**，定义了两种资源共享方式：

* **Exclusive**：独占资源，如ReentrantLock
* **Share**：共享，多个线程可以同时执行，如ReadWriteLock



### 获取锁的流程

获取锁的流程大致如下，公平锁与不公平锁的实现主要体现在``tryAcquire` `中逻辑的差异

1. 直接通过 `tryAcquire` 尝试获取锁，成功直接返回；
2. 如果没能获取成功，那么把自己加入等待队列；
3. **自旋**查看自己的排队情况；
4. 如果排队轮到自己，那么尝试通过 `tryAcquire` 获取锁；
5. 如果没轮到自己，那么回到第三步查看自己的排队情况。





## ReadWriteLock

ReadWriteLock 顾名思义读写锁，也就是说同一个锁对读和写的上锁方式是不一样的，写锁的互斥性更高。

ReadWriteLock 提供了*读写之间不同互斥策略*的锁。因此，当程序有频繁的读操作时，他能够带来更好的性能

由于读写控制的策略不一样，带来了锁内部的复杂度。所以如果程序的读操作并没有达到一定数量，使用读写锁会比互斥锁性能更差

如下为读写锁的继承关系：

![](http://cdn.ziyedy.top/Java12-2%EF%BC%9A%E9%94%81%E6%9C%BA%E5%88%B6%E7%9A%84%E4%BD%BF%E7%94%A8%E4%B8%8E%E5%AE%9E%E7%8E%B0/ReadWriteLock%E5%AE%9E%E7%8E%B0.jpg)

### 锁降级与锁升级

* 锁降级：如果线程持有写锁，如果可以在不释放写锁的情况下，获取读锁，这就是锁降级。

  **ReadWriteLock 支持锁降级**，如下：

```java
ReadWriteLock lock = new ReentrantReadWriteLock();
Lock readLock = lock.readLock();
Lock writeLock = lock.writeLock();

writeLock.lock();
System.out.println("got the write lock");	// 成功输出
readLock.lock();
System.out.println("got the read lock");	// 成功输出
```

* 锁升级：如果线程持有读锁，能够直接获取写锁。这意味着从一个低级别的锁升级到高级别的锁，其实就是变相的插队，无视其它在排队等待写锁的线程。

  **ReadWriteLock 并不支持锁升级**，如下：

```java
ReadWriteLock lock = new ReentrantReadWriteLock();
Lock readLock = lock.readLock();
Lock writeLock = lock.writeLock();

readLock.lock();
System.out.println("got the read lock");	// 成功输出
writeLock.lock();
System.out.println("got the write lock");	// 未输出
```



### 参考

> https://www.cnblogs.com/three-fighter/p/14396208.html