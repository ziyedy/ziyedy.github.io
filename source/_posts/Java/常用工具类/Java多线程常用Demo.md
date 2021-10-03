---
title: Java多线程常见问题
date: 2021-04-17 18:27:42
tags:
	- 并发编程
categories:
	- Java
    - 常用工具类
fileName: Java-multithread-demo
---

## 两个线程交替打印奇偶数

### 使用synchronized、wait、notify







## 三个线程交替打印ABC

参考：https://zhuanlan.zhihu.com/p/369505007

### 1、使用 Synchronized

使用同步块与 wait 与 notify 的方法控制三个线程执行次序，核心是确定唤醒、等待的顺序。因此每一个线程需要同时持有两个对象锁：

* 一个是prev，即为前一个线程的对象锁，其主要作用是保证当前线程一定是在前一个线程操作完成后（即前一个线程释放了其对应的对象锁）才开始执行。
* 另一个是自身对象锁

先持有prev锁（也就前一个线程要释放其自身对象锁），然后当前线程再申请自己对象锁，两者兼备时打印。之后首先调用self.notify()唤醒下一个等待线程（注意notify不会立即释放对象锁，只有等到同步块代码执行完毕后才会释放），再调用prev.wait()立即释放prev对象锁，当前线程进入休眠，等待其他线程的notify操作再次唤醒。

```java
package org.example;

public class ABC_Synch {
    public static class ThreadPrinter implements Runnable {
        private String name;
        private Object prev;
        private Object self;

        private ThreadPrinter(String name, Object prev, Object self) {
            this.name = name;
            this.prev = prev;
            this.self = self;
        }

        @Override
        public void run() {
            int count = 10;
            while (count > 0) {// 多线程并发，不能用if，必须使用while循环
                synchronized (prev) { // 先获取 prev 锁
                    synchronized (self) {// 再获取 self 锁
                        System.out.print(name);// 打印
                        count--;

                        self.notifyAll();// 唤醒其他线程竞争self锁，注意此时self锁并未立即释放。
                    }
                    // 此时执行完self的同步块，这时self锁才释放。
                    try {
                        if (count == 0) {// 如果count==0,表示这是最后一次打印操作，通过notifyAll操作释放对象锁。
                            prev.notifyAll();
                        } else {
                            prev.wait(); // 立即释放 prev锁，当前线程休眠，等待唤醒
                        }
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }
    }

    public static void main(String[] args) throws Exception {
        Object a = new Object();
        Object b = new Object();
        Object c = new Object();
        ThreadPrinter pa = new ThreadPrinter("A", c, a);
        ThreadPrinter pb = new ThreadPrinter("B", a, b);
        ThreadPrinter pc = new ThreadPrinter("C", b, c);

        new Thread(pa).start();
        Thread.sleep(10);// 保证初始ABC的启动顺序
        new Thread(pb).start();
        Thread.sleep(10);
        new Thread(pc).start();
        Thread.sleep(10);
    }
}
```



### 2、使用 volatile 实现状态变化

```java
public class ThreadABC extends Thread {
    private volatile static int state = 0;	// 利用state判断当前哪个线程应该打印
    private static char[] list = new char[]{'A', 'B', 'C'};
    private int type;

    public ThreadABC(int type) {
        this.type = type;
    }

    @Override
    public void run() {
        for (int i=0; i<10; ) {
            if (state % 3 == type) {
                System.out.println(list[type]);
                state++;
                i++;
            }
        }
    }

    public static void main(String[] args) {
        new ThreadABC(0).start();
        new ThreadABC(1).start();
        new ThreadABC(2).start();
    }
}
```

### 3、使用Lock

每个线程执行时先获取锁，并且使用state表示打印顺序，执行完之后再释放锁

```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class ThreadABC {
    private static Lock lock = new ReentrantLock();
    private static int state = 0;

    static class ThreadA extends Thread {
        @Override
        public void run() {
            for (int i=0; i<10; ) {
                try {
                    lock.lock();
                    while (state % 3 == 0) {
                        System.out.println("A");
                        state++;
                        i++;
                    }
                } finally {
                    lock.unlock();
                }
            }
        }
    }

    static class ThreadB extends Thread {
        @Override
        public void run() {
            for (int i=0; i<10; ) {
                try {
                    lock.lock();
                    while (state % 3 == 1) {
                        System.out.println("B");
                        state++;
                        i++;
                    }
                } finally {
                    lock.unlock();
                }
            }
        }
    }

    static class ThreadC extends Thread {
        @Override
        public void run() {
            for (int i=0; i<10; ) {
                try {
                    lock.lock();
                    while (state % 3 == 2) {
                        System.out.println("C");
                        state++;
                        i++;
                    }
                } finally {
                    lock.unlock();
                }
            }
        }
    }

    public static void main(String[] args) {
        new ThreadA().start();
        new ThreadB().start();
        new ThreadC().start();
    }
}
```

### 4、ReentrantLock结合Condition





### 5、使用信号量Semaphore

每个线程执行时先获取信号量，后一个线程再释放信号量

```java
package org.example;

import java.util.concurrent.Semaphore;

public class ThreadABC {
    private static Semaphore a = new Semaphore(1);
    private static Semaphore b = new Semaphore(0);
    private static Semaphore c = new Semaphore(0);

    static class ThreadA extends Thread {
        @Override
        public void run() {
            try {
                for (int i=0; i<10; i++) {
                    a.acquire();	// A获取信号执行,A信号量减1,当A为0时将无法继续获得该信号量
                    System.out.println("A");
                    b.release();	// B释放信号，B信号量加1（初始为0），此时可以获取B信号量
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    static class ThreadB extends Thread {
        @Override
        public void run() {
            try {
                for (int i=0; i<10; i++) {
                    b.acquire();
                    System.out.println("B");
                    c.release();
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    static class ThreadC extends Thread {
        @Override
        public void run() {
            try {
                for (int i=0; i<10; i++) {
                    c.acquire();
                    System.out.println("C");
                    a.release();
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args) {
        new ThreadA().start();
        new ThreadB().start();
        new ThreadC().start();
    }
}

```

