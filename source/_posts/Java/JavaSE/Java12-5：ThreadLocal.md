---
title: ThreadLocal
date: 2021-03-13 17:05:55
tags:
	- 并发编程
categories:
	- Java
	- JavaSE
fileName: java12-ThreadLocal
---

#

利用“线程封闭”的思路解决线程安全问题，ThreadLocal变量的作用域是为线程，同一线程内共享





ThreadLocal的特性决定了它的使用场景，由于ThreadLocal中存储的变量是线程隔离的

1、存储需要在线程隔离的数据。比如线程执行的上下文信息，每个线程是不同的，但是对于同一个线程来说会共享同一份数据

2、



## ThreadLocal 的使用



## ThreadLocal源码解析

多个线程对同一个对象set操作，但只会get出自己set进去的值，原理如下：

```java
// set时，把当前threadLocal对象作为key，想要保存的对象作为value，存入map
public void set(T value) {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);		// 获取的 map 其实是与线程绑定的
    if (map != null)
        map.set(this, value);
    else
        createMap(t, value);
}

ThreadLocalMap getMap(Thread t) {
    return t.threadLocals;	// ThreadLocalMap 就存在Thread对象中
}
```



ThreadLocalMap是ThreadLocal的静态内部类

在ThreadLocalMap中使用WeakReference包装后的ThreadLocal对象作为key，也就是说这里对ThreadLocal对象为弱引用。当ThreadLocal对象在ThreadLocalMap引用之外，再无其他引用的时候能够被垃圾回收



