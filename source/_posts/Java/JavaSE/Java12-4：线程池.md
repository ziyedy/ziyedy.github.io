---
title: Java12-4：线程池
date: 2021-03-08 17:45:15
tags:
	- 并发编程
categories:
	- Java
	- JavaSE
fileName: Java12-thread-pool
---

## 线程池简介

并发的本质其实就是任务的并行处理，所以绝大部分并发程序都是围绕离散任务进行的

1. 线程创建需要消耗资源。通过前面的学习，我们知道线程的创建和启动都需要消耗资源，需要 JVM 和操作系统提供支持。如果线程运行的任务十分轻量级，那么会造成创建线程的时间开销比任务逻辑运行时间还要长；
2. CPU 性能有限。当活跃的线程超过了 CPU 的承载限度，那么会有大量线程参与竞争 CPU，造成系统额外的开销，但是永远都会有很多线程无法竞争到 CPU，造成了资源的浪费；
3. 系统能够支持的线程存在上限。如果超出上限，整个应用就会崩溃。



## 

线程池的作用是维护一定数量的线程，接收任意数量的任务，这些任务被线程池中的线程并发执行

## 线程池设计与实现

### 基本思路

维护一个任务队列 `RunnableTaskQueue ` 用以接收 `Runnable `任务，其中任务一旦被线程池取完，则调用 `wait` 方法进入阻塞状态，而一旦有新的任务被添加进来，则

创建固定数量的线程作为线程池，不断的从 `RunnableTaskQueue ` 中取得 `Runnable` 并调用其 `run`方法



```java
public class RunnableTaskQueue {
    private final LinkedList<Runnable> tasks = new LinkedList<>();

    public Runnable getTask() throws InterruptedException {
        synchronized (tasks) {
            while (tasks.isEmpty()) {
                System.out.println("TaskQueue is empty");
                tasks.wait();
            }
            return tasks.removeFirst();
        }
    }

    public void addTask(Runnable runnable) {
        synchronized (tasks) {
            tasks.add(runnable);
            tasks.notifyAll();
        }
    }
}
```





```java
public class MyExecutor {
    private final int poolSize;
    private final RunnableTaskQueue runnableTaskQueue;
    private final List<Thread> threads = new ArrayList<>();

    public MyExecutor(int poolSize) {
        this.poolSize = poolSize;
        this.runnableTaskQueue = new RunnableTaskQueue();
        Stream.iterate(1, item -> item + 1).limit(poolSize).forEach(item -> {
            initThread();
        });
    }

    private void initThread() {
        if(threads.size() <= poolSize) {
            Thread thread = new Thread(() -> {
                while (true) {
                    try {
                        Runnable task = runnableTaskQueue.getTask();
                        task.run();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            });
            threads.add(thread);
            thread.start();
        }
    }

    public void execute(Runnable runnable) {
        runnableTaskQueue.addTask(runnable);
    }
}
```





### 阻塞队列

阻塞队列是基于普通队列实现的，与多线程联系到一起。主要是对wait（）与notify（）操作的应用

https://blog.csdn.net/weixin_45755718/article/details/106835215





## Executor 线程池框架



### 参数



`coreSize` ：线程池在完成初始化



1：coreSize（核心线程数）：当新任务提交时，发现运行的线程数小于 coreSize，一个新的线程将被创建，即使这时候其它工作线程是空闲的，可以通过 getCorePoolSize 方法获得 coreSize；

2：maxSize（最大线程数）: 当任务提交时，coreSize < 运行线程数 <= maxSize，但队列没有满时，任务提交到队列中，如果队列满了，在 maxSize 允许的范围内新建线程；

coreSize == maxSize：想让线程一下子增加到 maxSize，并且不要回收线程，防止线程回收，避免不断增加回收的损耗





### 向线程池提交任务的过程



### 为什么建议手动创建线程池

可以让我们更加明确线程池的运行规则，避免资源耗尽的风险

正确创建方法：

根据业务场景，自己设置线程池参数（如内存有多大，想给线程取什么名字）



### 种类（5）

Executors#newCachedThreadPool 无界的线程池，并且可以自动回收

Executors#newFixedThreadPool 固定大小线程池；队列没有上限，有可能会导致OOM

Executors#newSingleThreadExecutor 单个线程的线程池；

ScheduledThreadPool	支持定时周期性任务执行的线程池



workStealingPool JDK 1.8 加入的



### 拒绝策略（4）

