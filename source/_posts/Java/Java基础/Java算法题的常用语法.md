---
title: Java算法题的常用语法
date: 2020-08-15 14:34:11
tags:
	- Java
categories:
	- Java
	- Java基础
fileName: java-for-algorithm-test
---





## 标准输入与输出



## 基本容器的使用

### 常用容器







## 容器的基本操作

### Arrays

#### Arrays.sort()——排序

对所有数组元素进行排序（默认正序）

```java
int[] a = new int[]{-2,-3,2,1,9};
Arrays.sort(a);	// 默认升序： -3 -2 1 2 9

```

使用**lamda表达式**更改排序方式，以倒序为例，此时需要注意**声明数组必须使用其包装类**，否则会报错

```java
Integer[] a = new Integer[]{-2,-3,2,1,9};
Arrays.sort(a, (a1, a2)->a2-a1);	// 倒序：9 2 1 -2 -3
```

对指定索引范围内元素进行排序

```
int[] b = new int[]{-2,-3,2,1,9};
// 对[0, 2)范围内数组元素进行排序
// 之后也可更改排序方式
Arrays.sort(b, 0, 2);	// -3 -2 2 1 9
```

#### Arrays.fill()——填充

为数组元素填充相同值

```
int[] a = new int[]{-2,-3,2,1,9};
Arrays.fill(a, 10);	// 10 10 10 10 10
```

为指定索引范围内数组填充相同值

```
int[] a = new int[]{-2,-3,2,1,9};
// 在索引[1, 3)范围内填充5
Arrays.fill(a, 1, 3, 5);	// -2 5 5 1 9
```

#### Arrays.equals()——判断两个数组各元素是否都相等

```java
int[] a = new int[]{-2,-3,2,1,9};
int[] b = new int[]{-2,-3,2,1,9};
System.out.println(Arrays.equals(a, b));	// true
```

#### Arrays.binarySerch()——二分查找定位元素下标

注意：数组一定要是**排好序的**，否则会出错，若没有搜索到返回一个负值

```
int[] a = new int[]{-3,-2,2,3,9};
System.out.println(Arrays.binarySearch(a, 2));	// 2
```

#### Arrays.copyOfRange()——截取数组

```
int[] a = new int[]{-2,-3,2,1,9};
// 截取[1, 3)的数组
int[] b = Arrays.copyOfRange(a,1,3);
for (int m : b) System.out.print(m + " ");	// -3 2
```



### Collections



