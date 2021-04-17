---
title: Java12-5：控制并发流程
date: 2021-04-17 15:07:47
tags:
	- 并发编程
categories:
	- Java
	- JavaSE
fileName: Java12-concurrent-process
---

## CountDownLatch

传入一个数值，倒数该数直到为0之前，线程一直处于等待状态，直到倒计时结束了，此线程才继续工作

### 主要函数

* `CountDownLatch(int count)`，构造函数，参数 `count` 为需要倒数的值
* `await()`，调用该方法的线程会被挂起，直到count值为0时才继续执行
* `countDown()`，将`count` 的值减1，直到0为止，等待的线程会被唤醒

### 示例

使用 `CountDownLatch` 实现控制线程的开始与结束。

```java
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class Demo {
    public static void main(String[] args) throws InterruptedException {
        CountDownLatch begin = new CountDownLatch(1);   // 用于控制所有线程同时开始
        CountDownLatch end = new CountDownLatch(5);     // 用于确保所有线程都运行完成了

        ExecutorService service = Executors.newFixedThreadPool(5);
        for (int i=0; i<5; i++) {
            int finalI = i;
            Runnable runnable = new Runnable() {
                @Override
                public void run() {
                    try {
                        begin.await();	// 使用begin保证所有线程同时开始
                        System.out.println(finalI + "-开始工作");
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    } finally {
                        end.countDown();	
                    }
                }
            };
            service.submit(runnable);
        }
        Thread.sleep(5000);
        System.out.println("全部开始工作");
        begin.countDown();
        end.await();	// 使用end保证所有线程都结束才继续
        System.out.println("全部完成工作");
    }
}
```



## Semaphore信号量

与操作系统中利用信号量控制并发流程的思路一致，信号量可用来限制或管理数量有限的资源的使用情况

### 使用流程

1. 初始化 `Semaphore` 并指定“许可证”的数量。`new Semaphore(int permits, boolean fair)`，可以设置公平策略，为true时为先来先服务的模式
2. 执行任务前，调用 `acquire()` 方法进行获取（获取不到则响应中断），也可以不响应中断。可以使用 `tryAcquire()` 进行判断
3. 任务结束后，调用 `release()` 方法进行释放

### 示例

如下示例的运行结果即为 3行 一打印，表示一次只能获取 3 个许可。

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Semaphore;

public class Demo {
    static Semaphore semaphore = new Semaphore(3, true);	// 只有3个许可

    public static void main(String[] args) {
        ExecutorService service = Executors.newFixedThreadPool(30);
        for (int i=0; i<60; i++) {
            service.submit(new Runnable() {
                @Override
                public void run() {
                    try {
                        semaphore.acquire();    // 获取许可
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(Thread.currentThread().getName() + "拿到许可");
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    semaphore.release();    // 释放许可
                }
            });
        }
        service.shutdown();
    }
}
```