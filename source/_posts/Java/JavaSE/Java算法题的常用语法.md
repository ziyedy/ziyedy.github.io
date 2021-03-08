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

## 控制台输入与输出

### 控制台输入

```java
Scanner sc = new Scanner(System.in);
```

#### 输入一整行数据

以换行为终止，返回一个String类型变量.

```
String s = sc.nextLine();
```

#### 输入单个数据（方法名为next后面跟基本数据类型名）

如下为输入一个整型

```
int a = sc.nextInt();
```

#### 输入一个字符串

```
String str = sc.next();
```



### 控制台输出

#### 基本语法

输出内容：`System.out.print(""); `

输出内容并换行：`System.out.println(""); `

格式化输出1： `System.out.format(String format, Object ... args); `

格式化输出2：`System.out.printf((String format, Object ... args);`

#### 格式化输出常用转换符

| 转 换 符 | 说  明             |
| -------- | ------------------ |
| %s       | 字符串类型         |
| %c       | 字符类型           |
| %d       | 整数类型（十进制） |
| %f       | 浮点类型           |
| %n 或 \n | 换行符             |

#### 搭配转换符的常用标志

| 标  志 | 说  明                 | 示  例               | 结  果  |
| ------ | ---------------------- | -------------------- | ------- |
| +      | 为正数或者负数添加符号 | ("%+d %+d", -10, 10) | -10 +10 |
| 0      | 数字前面补0            | ("%05d", 10)         | 00010   |

#### 常用示例

```
// 输出带有五位小数的浮点型
System.out.printf("%.5f", 99.89);	// 99.89000
// 输出在前面补齐5位数
System.out.printf("%05d", 99);	// 00099
```



## 基本容器的使用

### 常用容器的基本使用

![](http://cdn.ziyedy.top/image/Java%E7%AE%97%E6%B3%95%E9%A2%98%E7%9A%84%E5%B8%B8%E7%94%A8%E8%AF%AD%E6%B3%95/Collection.png)





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

示例列表如下：

```
ArrayList<Integer> list = new ArrayList<>();
list.add(2);
list.add(-2);
list.add(5);
list.add(99);
list.add(-10);
for (int v : list) System.out.print(v + " ");	// 2 -2 5 99 -10
```

#### void reverse(List list)：反转

```
// 反转整个列表
Collections.reverse(list);	// -10 99 5 -2 2

// 反转[1, 3)的列表
Collections.reverse(list.subList(1, 3));	// 2 5 -2 99 -10
```

#### void sort(List list)：排序

```
// 自然升序
Collections.sort(list);	// -10 -2 2 5 99

// 自定义排序方法（此处为降序）
Collections.sort(list, (a, b)->b-a);	// 99 5 2 -2 -10
```

#### void swap(List list, int i , int j)：交换两个索引位置的元素

```
// 交换索引为0 和 4的元素
Collections.swap(list, 0, 4);	// -10 -2 5 99 2
```





