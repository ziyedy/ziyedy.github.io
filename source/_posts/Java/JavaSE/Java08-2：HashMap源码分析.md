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

#

https://leetcode-cn.com/problems/lru-cache/solution/yuan-yu-linkedhashmapyuan-ma-by-jeromememory/



https://blog.csdn.net/qq_24549805/article/details/103849708?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_baidulandingword-0&spm=1001.2101.3001.4242



https://blog.csdn.net/weixin_39283212/article/details/90711340



HashMap 底层的数据结构主要是：数组 + 链表 + 红黑树。其中当链表的长度大于等于 8 时，链表会转化成红黑树，当红黑树的大小小于等于 6 时，红黑树会转化成链表



## 新增

