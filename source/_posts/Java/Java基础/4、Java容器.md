---
title: 3、Java容器
date: 2020-07-10 14:34:11
tags:
	- Java
categories:
	- Java
	- Java基础
fileName: java-collection
---



equal方法指内容是否相同

==指地址是否相同



String的对象一旦被创建，则不能修改，是不可变的（即使用substr等函数时其本身是不会变的）



当频繁操作字符串时，使用StringBuilder

常用方法

```
StringBuilder str = new StringBuilder("hello");
str.append(" world");	// 向后添加字符串
str.delete(0,5);	// 删除第0-4个字符
str.insert(4,"gg");	// 在索引4向后插入gg
str.replace(4,6,"aa");	// 在缩影4到5处替换为aa

```



### 集合

Collection：List，Queue，Set

List是元素有序并且可以重复的集合，称为序列

两个主要实现类：ArrayList，LinkedList



ArrayList底层由数组实现，动态增长，其中元素可以为null

```
ArrayList list = new ArrayList();
list.add(1);	// 向后添加
list.size();	// 获取元素数量
list.get(1);	// 取出索引值为1的元素
list.remove(2);	// 移除索引为2的元素

```



Set

元素无序且不可以重复的集合。

HashSet是Set的一个重要元素，称为哈希集，其中元素无序且不可以重复，只允许一个null元素。



### 迭代器

Iterator接口可以以统一的方式对各种集合元素进行遍历，调用各种集合元素的iterator()方法得到迭代器。

hasNext()方法检测集合中是否还有下一个元素

next()方法返回集合中的下一个元素

```
Iterator it = set.iterator();
while (it.hasNext()) {
	System.out.println(it.next());
}
```



hashCode()和equals()，用于判断是否相同，选哟进行重写  



Map。以键值对的形式存储

HashMap：基于哈希表的Map接口的实现，允许使用null值和null键，无序排列。



排序

```
Collections.sort(list);
```

使用Comparable或Comparator接口对自定义类进行排序









