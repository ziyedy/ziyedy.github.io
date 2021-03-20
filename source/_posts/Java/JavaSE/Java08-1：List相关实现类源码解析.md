---
title: Java08-1：List相关实现类源码解析
date: 2021-03-14 11:15:25
tags:
	- 源码
	- Java
categories:
	- Java
	- JavaSE
fileName: Java08-List-source-code
---

## 初始化

主要有三种初始化办法：无参初始化、指定初始化数组大小初始化、指定初始数据初始化

```java
private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

// 无参数初始化，数组大小为空
public ArrayList() {
    this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
}

// 指定初始数据初始化
public ArrayList(Collection<? extends E> c) {
    // elementData 是保存数组的容器，默认为 null
    elementData = c.toArray();
    // 如果给定的集合（c）数据有值
    if ((size = elementData.length) != 0) {
        // 如果集合元素类型不是 Object 类型，我们会转成 Object
        if (elementData.getClass() != Object[].class) {
            elementData = Arrays.copyOf(elementData, size, Object[].class);
        }
    } else {
        // 给定集合（c）无值，则默认空数组
        this.elementData = EMPTY_ELEMENTDATA;
    }
}
```

需要注意，JDK 1.8之后：*ArrayList 无参构造器初始化时，默认大小是空数组，当第一次add后才扩容为10*



## 新增与扩容

### 新增

```java
public boolean add(E e) {
      // 确保数组大小足够，不够执行扩容，size 为当前数组的大小
      ensureCapacityInternal(size + 1);  // Increments modCount!!
      // 直接赋值(线程不安全)
      elementData[size++] = e;
      return true;
}
```

### 扩容

```java
private void ensureCapacityInternal(int minCapacity) {
  	//如果初始化数组大小时，有给定初始值，以给定的大小为准，不走 if 逻辑
	if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
		minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    // 确保容积足够
    ensureExplicitCapacity(minCapacity);
}

private void ensureExplicitCapacity(int minCapacity) {
    // 修改记录数组
    modCount++;
    // 如果我们期望的最小容量大于目前数组的长度，那么就扩容
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}

// 扩容(把现有数据拷贝到新的数组)
private void grow(int minCapacity) {
    int oldCapacity = elementData.length;
    // oldCapacity >> 1 是把 oldCapacity 除以 2 的意思
    int newCapacity = oldCapacity + (oldCapacity >> 1);

    // 如果扩容后的值 < 我们的期望值，扩容后的值就等于我们的期望值
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;

    // 如果扩容后的值 > jvm 所能分配的数组的最大值，那么就用 Integer 的最大值
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);

    // 通过复制进行扩容
    elementData = Arrays.copyOf(elementData, newCapacity);
}
```

注意：

* 扩容的规则并不是翻倍，而是扩为之前容量的1.5倍
* ArrayList 中的数组的最大值是 `Integer.MAX_VALUE`，超过这个值，JVM 就不会给数组分配内存空间了
* 新增时，并没有对值进行严格的校验，所以 ArrayList 是允许 null 值的
* 扩容的底层是通过 `Arrays.copyOf(elementData, newCapacity);` 实现的







