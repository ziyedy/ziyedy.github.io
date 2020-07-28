---
title: 4、Java多线程
date: 2020-07-12 18:47:21
tags:
	- Java
categories:
	- Java
	- Java基础
fileName: java4
---

### 线程的创建

1、创建一个Thread类，或者一个Thread子类的对象

2、创建一个实现Runnable接口的类的对象（解决Java只能单继承的限制，同时可以避免重写Thread类中的其他方法）

```
class Test implements Runnable {
	@Override
	public void run() {
		// 添加你的代码
	}
}
```

之后在主函数中输入

```
Test t = new Test();
Thread t1 = new Thread(pr);	// 将创建好的通过参数传入Thread
t1.start();
```





Thread类的常用方法

```
# 线程相关的代码写入该方法，一般需要重写
public void run()

# 启动线程
public void start()

# 线程休眠m毫秒的方法
public static void sleep(long m)

# 优先执行调用join()方法的线程
public void join()
# 等待该线程终止的最长时间为millis毫秒
public final void join(long millis)
```



Runnable接口

Runnable是Java中用以实现线程的接口，只有一个方法run()

任何实现线程功能的类都必须实现该接口



### 线程优先级

Java为线程类提供了10个优先级，优先级可以用整数1-10表示，超过范围会抛出异常，主线程默认优先级为5

优先级常量

```
MAX_PRIORITY：线程的最高优先级10
MIN_PRIORITY：线程的最低优先级1
NORM_PRIORITY：线程的默认优先级5
```

优先级相关方法：

```;
// 获取线程优先级
public int getPriority()

// 设置线程优先级
public void setPriority(int newPriority)

// 获取主线程优先级
int mainpriority = Thread.currentThread().getPriority()
```



### 多线程

各线程通过竞争CPU时间而获得运行机会，各线程什么时候得到CPU时间，占用多久，是不可预测的，一个正在运行着的线程在什么地方被暂停是不确定的



synchronized

成员方法，静态方法、语句块

```
public synchronized void func() {}
public static synchronized void func() {}
synchronized(obj) {}
```



线程间通信



