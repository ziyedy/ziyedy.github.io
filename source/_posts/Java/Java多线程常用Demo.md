---
title: Java多线程常用Demo
date: 2021-04-17 18:27:42
tags:
	- 并发编程
categories:
	- Java
fileName: Java-multithread-demo
---

## 两个线程交替打印奇偶数

### 使用synchronized、wait、notify







## 三个线程交替打印ABC

### 使用`volatile`实现状态变化

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

### 使用Lock与state状态

