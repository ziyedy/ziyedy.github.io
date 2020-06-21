---
title: 1、JS内置对象与基本语法
date: 2020-06-05 20:18:08
tags:
	- 前端
categories:
	- 前端
	- JS
fileName: javascript1
---

### 数组：

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



### 字符串

```
// 返回子字符串的位置，若没有，返回-1
str.indexOf("char")
// 从后向前检索
str.lastIndexOf("char")
```



```
str.slice(start, end) // 截取字符串（end索引不在截取范围之内）

str.substring(start, end) // 功能与slice一致

str.substr(start, len) // 从start处截取长为len的字符串
```



```
str.split(separate) // 把字符串分割为字符串数组，参数为分隔符

// 替换字符串，将a替换为b
str.replace(a,b)
```



```
str.toUpperCase()

str.toLowerCase()
```





### debug方法

JavaScript程序中添加断点，使用debugger。示例如下：

```
<script>
    var a = [1,2,3,4,5];
    for(i=0;i<5;i++){
        console.log(a[i]);
        debugger;
    }
</script>
```

刷新Google页面即可进行debug



