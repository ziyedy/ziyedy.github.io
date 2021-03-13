---
title: Java12-1：线程安全问题与解决方案
date: 2021-03-05 10:01:09
tags:
	- 并发编程
categories:
	- Java
	- JavaSE
fileName: Java12-thread-safe
---

# 线程安全问题

线程安全问题主要来源于并发时对共享资源的操作（如出现值更改与值不同步等问题）

线程安全主要表现在：**原子性、可见性、有序性**  三个方面

## 原子性

> 所有操作要么全部成功，要么全部失败

即不可分割性，做为一个整体的N次操作，要么已经执行完毕，要么尚未发生。

### 竞态条件

> 在多线程的情况下，由于多个线程执行的时序不同，而出现不正确的结果

即所做的“观察”得出的结论在进行执行时已经失效

例如：有一个a变量，查看时a==5，而在查看与执行中间的时间里a被修改使得a==10，因此使用 a==5 作为计算就会出现错误





Java实现原子性的方法：锁、利用处理器的CAS（Compare and Swap）指令

锁具有排它性，保证共享变量在某一时刻只能被一个线程访问

CAS 指令直接在硬件（处理器和内存）层次上实现，看作是硬件锁



## 可见性

> 一个线程对变量进行了修改，另外一个线程能够立刻读取到此变量的最新值

### 缓存一致性（可见性失败案例）

失败案例代码：

```java
public class volatile1 {

    private static class Test implements Runnable {
        public static Object o = new Object();
        private Boolean flag = false;

        @Override
        public void run() {
            while (true) {
                if (flag) {
                    // 该行未打印
                    System.out.println(Thread.currentThread().getName() + ":" + flag);
                }
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Test test = new Test();
        Thread thread = new Thread(test);
        thread.start();
        Thread.sleep(100);
        test.flag = true;	// 主函数中对flag进行修改并未被thread线程发现
        Thread.sleep(100);
        System.out.println("Main thread is over");	// 直接运行完该行
    }
}
```

出错原因：

为了提高计算效率，CPU 会从缓存中取得 flag 值。但是主存中 flag 值的变化，thread线程并不知道，导致其缓存和主存不一致，获取到的是失效的 flag 值（多个线程缓存不一致）





代码中使用 volatile 修饰 flag 变量。这确保在多个线程并发时，任何一个线程改变了 flag 的值都会立即被其它线程所看到



对读和写都进行同步

使用 `volatile` 关键字







## 有序性

> 代码在运行期间保证按照编写的顺序

### 指令重排序

代码在执行阶段，CPU会根据指令间的依赖关系进行优化以提高运行效率





确保并发三大特性的方法：使用 synchronized 关键字或者其它加锁，这样很简单，但会让多线程程序串行化，因此不能滥用同步





# Atomic与CAS——原子性实现

Atomic 相关类在 java.util.concurrent.atomic 包中

使用Synchronized ：悲观锁：每次都认为有其它线程在和它并发操作，所以每次都要对资源进行锁定



Atomic 变量的操作是如何保证原子性的呢？其实是使用了 CAS 算法



## CAS算法

CAS 即比较替换（Compare and swap） ：乐观锁

**悲观锁**–认为每一次自己的操作大概率会有其它线程在并发，所以自己在操作前都要对资源进行锁定，这种锁定是排他的。悲观锁的缺点是不但把多线程并行转化为了串行，而且加锁和释放锁都会有额外的开支。

**乐观锁**–认为每一次操作时大概率不会有其它线程并发，所以操作时并不加锁，而是在对数据操作时比较数据的版本，和自己更新前取得的版本一致才进行更新。乐观锁省掉了加锁、释放锁的资源消耗，而且在并发量并不是很大的时候，很少会发生版本不一致的情况，此时乐观锁效率会更高。



Atomic 变量在做原子性操作时，会从内存中取得要被更新的变量值，并且和你期望的值进行比较，期望的值则是你要更新操作的值。如果两个值相等，那么说明没有其它线程对其更新，本线程可以继续执行。如果不等，说明有线程已经先于此线程进行了更新操作。那么则继续取得该变量的最新值，重复之前的逻辑，直至操作成功。这保证了每个线程对 Atomic 变量操作是线程安全的。

（Git的pull与push）



## Atomic源码分析

AtomicInteger 中有 3 个重要的成员变量：

```java
// Atomic 中的原子操作都是借助 unsafe 对象所实现的
private static final Unsafe unsafe = Unsafe.getUnsafe();

// AtomicInteger 包装的变量在内存中的地址
// value的地址
private static final long valueOffset;

// AtomicInteger 包装的变量值，并且用 volatile 修饰，以确保变量的变化能被其它线程看到
private volatile int value;
```

1. AtomicInteger 对象包装了通过构造函数传入的一个初始 int 值；
2. AtomicInteger 持有这个 int 变量的内存地址；
3. AtomicInteger 还有一个用来做原子性操作的 unsafe 对象。



`incrementAndGet `方法：

```java
public final int incrementAndGet() {
    return unsafe.getAndAddInt(this, valueOffset, 1) + 1;
}
```



```java
public final int getAndAddInt(Object obj, long valueOffset, int var) {
    int expect;
    // 利用循环，直到更新成功才跳出循环。
    do {
        // 获取value的最新值
        expect = this.getIntVolatile(obj, valueOffset);
        // expect + var表示需要更新的值，如果compareAndSwapInt返回false，说明value值被其他线程更改了。
        // 那么就循环重试，再次获取value最新值expect，然后再计算需要更新的值expect + var。直到更新成功
    } while(!this.compareAndSwapInt(obj, valueOffset, expect, expect + var));

    // 返回当前线程在更改value成功后的，value变量原先值。并不是更改后的值
    return expect;
}
```



## CAS 的缺点

CAS 最终通过 CPU 指令实现，把无谓的同步消耗降到最低

缺点：

1. 比较替换如果失败，则会一直循环，直至成功。这在并发量很大的情况下对 CPU 的消耗将会非常大；
2. 只能保证一个变量自身操作的原子性，但多个变量操作要实现原子性，是无法实现的；
3. ABA 问题。

### ABA问题

假如本线程更新前取得期望值为 A，和更新操作之间的这段时间内，其它线程可能把 value 改为了 B 又改回了 A。 而本线程更新时发现 value 和期望值一样还是 A，认为其没有变化，则执行了更新操作。但其实此时的 A 已经不是彼时的 A 了。

大多数情况下 ABA 不会造成业务上的问题，JDK 提供了 AtomicStampedReference 类，通过对 Atomic 包装的变量**增加版本号**，来解决 ABA 问题，即使 value 还是 A，但如果版本变化了，也认为比较失败



# volatile关键字

被 volatile 修饰后，该变量或获得以下特性：

1. 可见性。任何线程对其修改，其它线程马上就能读到最新值；
2. 有序性。禁止指令重排序。

为了解决缓存一致性，我们需要了解缓存一致性协议。MESI 协议是目前主流的缓存一致性协议。此协议会保证，写操作发生时，线程独占该变量的缓存，CPU 并且会通知其它线程对于该变量所在的缓存段失效。只有在独占操纵完成之后，该线程才能修改此变量。而此时由于其它缓存全部失效，所以就不存在缓存一致性问题。而其它线程的读取操作，需要等写入操作完成，恢复到共享状态。



volatile实现的机制：编译为指令后 有lock关键字，即锁住相关区域

volatile 的有序性则是通过内存屏障，即在屏障前所有指令可以重排序，在屏障后也可以重排序，但重排序不能够越过内存屏障

volatile 无法保证原子性



volatile 的局限性：

1. volatile 的可见性和有序性只能作用于单一变量；
2. volatile 不能确保原子性；
3. volatile 不能作用于方法，只能修饰实例或者类变量。



volatile 最常用的用法，是使用 volatile 修饰作为标识判断的变量