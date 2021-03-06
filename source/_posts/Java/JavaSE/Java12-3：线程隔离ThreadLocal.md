---
title: Java12-3：线程隔离ThreadLocal
date: 2021-03-13 17:05:55
tags:
	- 并发编程
categories:
	- Java
	- JavaSE
fileName: java12-ThreadLocal
---

## ThreadLocal

### 基本思想

利用**线程隔离**的思路解决线程安全问题，`ThreadLocal`变量的作用域是为线程，同一线程内共享，因此也就保证了线程安全

### 使用场景

* 每个线程需要一个独享的对象（比如说工具类或工具方法，该类方法通常不是线程安全的，所以需要将其在**线程间隔离**）

  如使用线程池时，每个任务都需要创建一个对象完成工作，这种情况就可以使用 `ThreadLocal` 为每个线程创建一个目标对象，避免反复创建的同时也避免了“单例”情况下的线程不安全

* 每个线程内需要保存的全局变量，使得相应方法可以直接使用，避免参数的反复传递

经典的使用场景是为每个线程分配一个 JDBC 连接 Connection。这样就可以保证每个线程的都在各自的 Connection 上进行数据库的操作，不会出现 A 线程将 B线程正在使用的 Connection 关闭

### 优势

* 线程安全
* 不需要加锁，提高效率
* 更高效的利用内存、节省开销（不用每次新建对象）
* 避免参数反复传递，使代码耦合度更低

### 基本使用

基本使用就是声明`ThreadLocal`对象，然后使用`set`、`get`方法设置与得到相应的值（与线程绑定）

```java
public class Test {
    // 通常将 ThreadLocal 对象声明为 static final
    static final ThreadLocal<String> threadLocal = new ThreadLocal<>();
    static class Subthread extends Thread {
        @Override
        public void run() {
            for (int i = 0; i < 5; i++) {
                // set 设置线程相关值
                threadLocal.set(Thread.currentThread().getName() + '-' + i);
                // get 取出相关值
                String val = threadLocal.get();
                System.out.println(Thread.currentThread().getName() + ": " + val);
            }
        }
    }

    public static void main(String[] args) {
        Subthread t1 = new Subthread();
        Subthread t2 = new Subthread();
        t1.start();
        t2.start();
    }
}
```

![](http://cdn.ziyedy.top/Java12-3%EF%BC%9A%E7%BA%BF%E7%A8%8B%E9%9A%94%E7%A6%BBThreadLocal/ThreadLocal%E4%BD%BF%E7%94%A8%E7%BB%93%E6%9E%9C.png)



## ThreadLocal 源码解析

### set 方法

多个线程对同一个对象set操作，但只会get出自己set进去的值，原理如下：

```java
// set时，把当前threadLocal对象作为key，想要保存的对象作为value，存入map
public void set(T value) {
    Thread t = Thread.currentThread();
    ThreadLocalMap map = getMap(t);		// 获取的 map 与当前线程绑定的
    if (map != null)
        map.set(this, value);	// 把当前threadLocal对象作为key，将value存入map
    else
        createMap(t, value);
}

ThreadLocalMap getMap(Thread t) {
    return t.threadLocals;	// ThreadLocalMap 就存在 Thread 对象中
}
```

*总结：`ThreadLocal` 看似自己保存了每个线程的变量副本，其实每个线程的变量副本是保存在线程对象中，因此自然就线程隔离了*

![](http://cdn.ziyedy.top/Java12-3%EF%BC%9A%E7%BA%BF%E7%A8%8B%E9%9A%94%E7%A6%BBThreadLocal/ThreadLocal%E5%8E%9F%E7%90%86.jpg)

### ThreadLocalMap 分析

`ThreadLocalMap`是`ThreadLocal`的静态内部类，其虽然作为属性存储在Thread类中，但由于**访问权限问题**只能由`ThreadLocal`进行维护

```java
// 存在于 Thread 类中
// 即ThreadLocal其实是作为 “属性” 保存在Thread对象中的
ThreadLocal.ThreadLocalMap threadLocals = null;
```

### 内存泄漏

>  内存泄漏：为程序在申请内存后，无法释放已申请的内存空间（不会再被使用的对象或者变量占用的内存不能被回收）

`ThreadLocalMap`功能与其他Map类容器作用类似，但其中作为 key 存储的`ThreadLocal`对象为**弱引用**（如下），因此很容易被垃圾回收，但value对象并不会与之一起被回收，这样就会造成**“内存泄漏”**问题

通常线程运行完自动终止不会出现内存泄漏问题，但如果线程不终止（比如线程池中的线程），那么 Key 对应的 value 就不会被回收，因为存在调用链 `Thread -> ThreadLocalMap -> Entry(key == null) -> Value`

```java
// 存在于 ThreadLocalMap 中，用于作为 key
static class Entry extends WeakReference<ThreadLocal<?>> {
    /** The value associated with this ThreadLocal. */
    Object value;

    Entry(ThreadLocal<?> k, Object v) {
        super(k);
        value = v;
    }
}
```

### 如何避免内存泄漏

JDK已经考虑了该问题，因此在 `set`、 `remove`等方法中会扫描key为 null 的Entry并把对应的value置为null，这样就能够对value进行回收

* 每次使用完 `ThreadLocal` 都调用其 `remove()`方法清除数据（阿里规约）
* 将 `ThreadLocal` 变量定义为static，这样就保证了一直存在其强引用

