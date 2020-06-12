---
title: JavaScript基础知识（一）
date: 2020-06-05 20:18:08
tags:
categories:
fileName: javascript1
---

数组：

基本操作

```
array.reverse()	// 颠倒数组
array.sort()	// 对数组进行排序（默认升序）
array.sort(function(a, b) {return b-a})	// 传入比较函数，变为降序
array1.concat(array2)	// 连接array1和array2
array.slice(start, end)	// 从start到end的切片
```



转化为字符串，默认无参数则用逗号“,”连接。

```
str = array.join()
str = array.join("")	// 无连接符号直接连接
```



```
// 删除从index处开始的零个或多个元素
array.splice(index, count)
// 在index后插入值
array.splice(index, 0, item1.....)
// 替换
array.splice(index, count, item1.....)
```



```
array.indexOf(value)	// 查找值为value的索引，没有则返回-1
```



字符串

```
str.charAt(index)
```

