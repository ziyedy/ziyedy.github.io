---
title: Java08-2：HashMap实现方法总结
date: 2021-03-04 20:38:50
tags:
	- 源码
	- Java
categories:
	- Java
	- JavaSE
fileName: Java08-hashmap-source-code
---

## 底层实现

JDK1.7及其之前，HashMap底层是一个**table数组 + 链表**实现的哈希表存储结构

JDK1.8以后，HashMap 底层的数据结构主要是：数组 + 链表 + 红黑树。其中当链表的长度大于等于 8 时，链表会转化成红黑树（能够降低查询的时间复杂度），当红黑树的大小小于等于 6 时，红黑树会转化成链表



### 基本属性

```java
// 初始容量为 16
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;

// 最大容量
static final int MAXIMUM_CAPACITY = 1 << 30;

// 负载因子默认值
static final float DEFAULT_LOAD_FACTOR = 0.75f;

// 桶上的链表长度大于等于8时，链表转化成红黑树
static final int TREEIFY_THRESHOLD = 8;

// 桶上的红黑树大小小于等于6时，红黑树转化成链表
static final int UNTREEIFY_THRESHOLD = 6;

// 当数组容量大于 64 时，链表才会转化成红黑树
static final int MIN_TREEIFY_CAPACITY = 64;

// 记录迭代过程中 HashMap 结构是否发生变化，如果有变化，迭代时会 fail-fast
transient int modCount;

// HashMap 的实际大小
transient int size;

// 存放数据的数组
transient Node<K,V>[] table;

// 触发扩容的阈值，有两种情况：
// 1、初始化时，给定数组大小，通过 tableSizeFor 方法计算
//	  数组大小为比给定数值大的2的整数次幂，如给定初始化大小19，实际上初始化大小即为为32
// 2、通过 resize 方法进行扩容，大小 = 数组容量 * 0.75
int threshold;

//链表的节点
static class Node<K,V> implements Map.Entry<K,V> {

//红黑树的节点
static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V>
```



### 为什么是 >= 8 时扩容

红黑树需要占用的空间为链表的2倍，而链表数据不多时两者的查询速度差距没有那么明显，因此考虑到转化时间和空间损耗，必须定义出转化的边界值

因此使用**泊松分布**计算链表长度为8的概率，几乎接近于0，而一旦到8，说明哈希算法出现了问题，为提高性能则让链表转化为红黑树。



### HashMap 中数组大小为何一定为2的n次方

HashMap在取模和扩容时进行了优化（加入了高位参与运算的过程），主要有如下三个步骤：

```java
static final int hash(Object key) {
    int h;
    // 1、取hashCode值
    // 2、高位参与运算
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}

//jdk1.7的源码，jdk1.8与之实现原理理一样
static int indexFor(int h, int length) { 
	return h & (length-1); // 3、取模运算
}
```

该方法非常巧妙，通过 `h & (length-1)` 来获取目标对象的位置（等价于h % length），而length保持了2的n次方能够大大加快运算效率，避免直接取模的效率损失。



## put 方法

put方法的基本过程：

1. 空数组有无初始化，没有的话初始化；
2. 如果通过 key 的 hash 能够直接找到值，跳转到 6，否则到 3；
3. 如果 hash 冲突，两种解决方案：链表 or 红黑树；
4. 如果是链表，递归循环，把新元素追加到队尾；
5. 如果是红黑树，调用红黑树新增的方法；
6. 通过 2、4、5 将新元素追加成功，再根据 onlyIfAbsent 判断是否需要覆盖；
7. 判断是否需要扩容，需要扩容进行扩容，结束。



## 扩容机制

* JDK 1.7链表采用头插法
* JDK 1.8链表采用尾插法

由JDK1.7源码如下：

```java
void resize(int newCapacity) { 	// 传入新的容量
	Entry[] oldTable = table; 	// 引⽤扩容前的Entry数组
	int oldCapacity = oldTable.length;
	if (oldCapacity == MAXIMUM_CAPACITY) { 	// 扩容前的数组大⼩如果已经达到最⼤(2^30)了
		threshold = Integer.MAX_VALUE; 		// 修改阈值为int的最⼤大值(2^31-1)
		return;
	}
    
	Entry[] newTable = new Entry[newCapacity]; // 初始化一个新的Entry数组
	transfer(newTable); 	// 将数据转移到新的Entry数组里
	table = newTable; 		// HashMap的table属性引⽤新的Entry数组
	threshold = (int)(newCapacity * loadFactor);	// 修改阈值
}
```

其中 `transfer()` 方法将原有的数组中的元素拷贝到新的数组中去，代码如下：

```java
void transfer(Entry[] newTable) {
	Entry[] src = table; 	// src引⽤了旧的Entry数组
	int newCapacity = newTable.length;
	for (int j = 0; j < src.length; j++) { 	// 遍历旧的Entry数组
		Entry<K,V> e = src[j]; 		// 取得旧Entry数组的每个元素
		if (e != null) {
			src[j] = null;	// 释放旧Entry数组的对象引⽤用（for循环后，旧的Entry数组不再引⽤用任何对象）
            do {
                Entry<K,V> next = e.next;
                int i = indexFor(e.hash, newCapacity); // 重新计算每个元素在数组中的位置
                e.next = newTable[i]; //标记[1]
                newTable[i] = e; 	//将元素放在数组上
                e = next;	 // 访问下⼀个Entry链上的元素
            } while (e != null);
		}
	}
}
```



## HashMap 并发安全问题

并发的多线程环境使用HashMap可能会造成死循环（并发插入元素的时候，有可能出现带环链表，让下一次读操作死循环）









## ConcurrentHashMap

锁分段技术，每一个Segment之间互不影响

ConcurrentHashMap当中每个Segment各自持有一把锁。在保证线程安全的同时降低了锁的粒度，让并发操作效率更高



https://zhuanlan.zhihu.com/p/31614308





