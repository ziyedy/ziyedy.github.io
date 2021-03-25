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

## 解决方案——使用volatile关键字

上述代码中**使用 volatile 修饰 flag 变量**，会发现运行得到了想要的结果。`volatile` 关键字确保在多个线程并发时，任何一个线程改变了 flag 的值都会立即被其它线程所看到

### volatile的用处

如之前在可见性中“缓存失败案例”中所述，使用`volatile`关键字可以解决可见性的问题

volatile 关键字可以用来修饰实例变量和类变量，被 volatile 修饰后，该变量可以获得可见性与有序性

* **可见性**：任何线程对其修改，其它线程马上就能读到最新值；

  volatile  编译为指令后有lock关键字，会锁住相关区域，即独占变量缓存而使其他缓存全部失效（缓存一致性协议）

  1、执行写操作后，JMM会把工作内存中的最新变量值强制刷新到主内存

  2、写操作会导致其他线程中的缓存无效

* **有序性**：禁止指令重排序。

  通过**内存屏障**：即在屏障前所有指令可以重排序，在屏障后也可以重排序，但重排序不能够越过内存屏障

**注：volatile 无法保证原子性**

参考：https://zhuanlan.zhihu.com/p/133851347

### 局限性

1. volatile 的可见性和有序性只能作用于单一变量，且无法修饰方法
2. volatile 不能确保原子性

### 使用场景

volatile 最常用的用法，是**使用 volatile 修饰作为标识判断的变量**，如之前示例中用于进行判断的flag变量

## 有序性

> 代码在运行期间保证按照编写的顺序

### 指令重排序

代码在执行阶段，CPU会根据指令间的依赖关系进行优化以提高运行效率



# Atomic与CAS——原子性实现

Atomic 相关类在 `java.util.concurrent.atomic` 包中，针对不同类型提供了很多类，**它们都是使用  CAS 来保证原子性**

```java
AtomicInteger count = new AtomicInteger(0);		// 利用Atomic声明当前变量
count.incrementAndGet();	// 获取“最新”变量然后+1
```

## CAS 算法

CAS 即比较替换（Compare and swap） ，是一种乐观锁

它通过 CPU 指令实现，把无谓的同步消耗降到最低

### 乐观锁与悲观锁

* **悲观锁**：认为每一次自己的操作大概率会有其它线程在并发，所以自己在操作前都要对资源进行锁定，这种锁定是排他的。

  悲观锁的缺点是不但把多线程并行转化为了串行，而且加锁和释放锁都会有额外的开支。

* **乐观锁**：认为每一次操作时大概率不会有其它线程并发，所以操作时并不加锁，而是在对数据操作时比较数据的版本，和自己更新前取得的版本一致才进行更新。

  乐观锁省掉了加锁、释放锁的资源消耗，而且在并发量并不是很大的时候，很少会发生版本不一致的情况，此时乐观锁效率会更高。

### 算法过程

首先从内存中取得要被更新的变量值，并且与期望的值进行比较（即需要更新操作的值）

* 如果两个值**相等**，那么说明没有其它线程对其更新，本线程可以继续执行。

* 如果**不等**，说明有线程已经先于此线程进行了更新操作。那么则继续取得该变量的最新值，**重复之前的逻辑**，直至操作成功。

### 举例

其实Git的pull与push等操作就采用了CAS的思想，只是项目版本是否改变是由Git帮助我们进行判断

## Atomic源码分析

### 三个重要成员变量

`AtomicInteger` 中有 3 个重要的成员变量：

```java
// Atomic 中的原子操作都是借助 unsafe 对象所实现的
private static final Unsafe unsafe = Unsafe.getUnsafe();

// AtomicInteger 包装的变量在内存中的地址，即 value 的地址
private static final long valueOffset;

// AtomicInteger 包装的变量值，并且用 volatile 修饰，以确保变量的变化能被其它线程看到
private volatile int value;
```

### incrementAndGet方法

首先调用`getAndAddInt`方法得到最新值，然后+1返回

```java
public final int incrementAndGet() {
    return unsafe.getAndAddInt(this, valueOffset, 1) + 1;
}
```

`getAndAddInt`基本就是CAS的实现，源码如下：

```java
public final int getAndAddInt(Object obj, long valueOffset, int var) {
    int expect;
    // 利用循环
    do {
        // 获取value的最新值
        expect = this.getIntVolatile(obj, valueOffset);
        
    } while(!this.compareAndSwapInt(obj, valueOffset, expect, expect + var));

    // 返回value的原先值
    return expect;
}
```

## CAS 的缺点

1. 比较替换如果失败，则会一直循环，直至成功。这在并发量很大的情况下对 CPU 的消耗将会非常大
2. 只能保证一个变量自身操作的原子性，但多个变量操作要实现原子性，是无法实现的
3. 会导致 ABA 问题

### ABA 问题

问题描述：

假如本线程更新前取得期望值为 A，在更新操作发生前的这段时间内，其它线程可能把 value 改为了 B 又改回了 A。 而本线程更新时发现 value 和期望值一样还是 A，认为其没有变化，但其实此时的 A 已经不是彼时的 A 了。

解决方案：可以为使用`Atomic`包装的变量**增加版本号**，通过同时比较版本号来确定变脸是否发生变化

