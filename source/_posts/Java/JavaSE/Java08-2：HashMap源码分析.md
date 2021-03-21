---
title: Java08-2：HashMap源码分析
date: 2021-03-04 20:38:50
tags:
	- 源码
	- Java
categories:
	- Java
	- JavaSE
fileName: Java08-hashmap-source-code
---



JDK1.7及其之前，HashMap底层是一个**table数组+链表**实现的哈希表存储结构

JDK1.8及以后，当链表的存储数据个数 **≥8** 的时候，不再采用链表存储，而采用红黑树存储结构。这么做能够降低查询的时间复杂度（链表O(n) -> 红黑树O(logn)）。

JDK1.7中链表的每个节点就是一个`Entry`，1.8之后改为了`Node`，其中包括以下四个部分：

```java
static class Entry<K, V> implements Map.Entry<K, V> {
    final K key; 	//key
    V value;	//value
    Entry<K, V> next; 	//指向下一个节点的指针
    int hash;	//哈希码
}
```

JDK1.7中HashMap的主要成员变量及其含义

```java
public class HashMap<K, V> implements Map<K, V> {
	// 哈希表主数组的默认长度
    static final int DEFAULT_INITIAL_CAPACITY = 16; 
	// 默认的装填因子
    static final float DEFAULT_LOAD_FACTOR = 0.75f; 
	// 主数组的引用
    transient Entry<K, V>[] table; 
    // 阈值
    int threshold;
    // 装填因子
    final float loadFactor;	
    // 默认构造函数使用默认的长度与装填因子
    public HashMap() {
        this(DEFAULT_INITIAL_CAPACITY, DEFAULT_LOAD_FACTOR);
    }
}
```







https://leetcode-cn.com/problems/lru-cache/solution/yuan-yu-linkedhashmapyuan-ma-by-jeromememory/



https://blog.csdn.net/qq_24549805/article/details/103849708?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_baidulandingword-0&spm=1001.2101.3001.4242



https://blog.csdn.net/weixin_39283212/article/details/90711340



HashMap 底层的数据结构主要是：数组 + 链表 + 红黑树。其中当链表的长度大于等于 8 时，链表会转化成红黑树，当红黑树的大小小于等于 6 时，红黑树会转化成链表



## 新增

