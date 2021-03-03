---
title: post
date: 2021-01-03 13:58:43
tags:
categories:
fileName:
---

## 线程的创建与启动

### 继承Thread类



### 实现Runnable接口

实现Runnable接口	（最终调用target.run()方法）

继承Thread类	（run()整个被重写）



使用Runnable接口更好

从代码架构来说：解耦合

新建线程的损耗

Java不支持双继承





### 线程的启动

调用start方法才是真正开始了一个线程的周期，而调用run方法则只是调用一个普通的方法





## 停止线程





## 线程的生命周期（6个状态）

New

Runnable

Blocked

Waiting

Timed Waited

Terminated

一般习惯把Blocked（被阻塞）、Waiting（等待）、Time_waiting（计时等待）都称为阻塞状态







```java
package com.wkcto.threadmehtod;
/**
* 测试当前线程
* Author : 蛙课网老崔
*/
public class Test01CurrentThread {
    public static void main(String[] args) {
        System.out.println("main 方 法 中 打 印 当 前 线 程 :" + Thread.currentThread().getName());
        //创建子线程, 调用SubThread1()构造方法, 在main线程中调用构造方法,所以构造方法中的当前线程就是 main 线程
        SubThread1 t1 = new SubThread1();
        // t1.start(); //启动子线程,子线程会调用 run()方法,所以 run()方法中 的当
        前线程就是 Thread-0 子线程
        t1.run(); //在 main 方法中直接调用 run()方法,没有开启新的线程,所以在 run方法
        蛙课网【动力节点旗下品牌】 http://www.wkcto.com
        中的当前线程就是 main 线程
    }
}
package com.wkcto.threadmehtod;
/**
* 当前线程的复杂案例
* Author : 蛙课网老崔
*/
public class SubThread2 extends Thread {
    public SubThread2(){
        System.out.println(" 构 造 方 法 中 ,Thread.currentThread().getName() : " +
        Thread.currentThread().getName() );
        System.out.println("构造方法,this.getName() : " + this.getName());
    }
    @Override
    public void run() {
        System.out.println("run 方 法 中 ,Thread.currentThread().getName() : " +
        Thread.currentThread().getName() );
        System.out.println("run 方法,this.getName() : " + this.getName());
    }
}
```

