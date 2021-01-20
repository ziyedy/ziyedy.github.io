---
title: 1、JS基础
date: 2020-07-29 20:14:04
tags:
	- JavaScript
categories:
	- 编程相关
	- JavaScript
fileName: js-basic-grammer
---

## 基本概念

### 语言特点

1、解释性语言

2、单线程

3、遵从ECMAScript标准

对换行、缩进、空格不敏感。每一条语句以分号结尾。（分号不是必须加，如果不写分号，浏览器会自动添加，但是会消耗一些系统资源）



### JavaScript组成

JavaScript基础分为三个部分：

- **ECMAScript**：JavaScript的语法标准。包括变量、表达式、运算符、函数、if语句、for语句等。

- **DOM**：文档对象模型（Document object Model），操作**网页上的元素**的API。比如让盒子移动、变色、轮播图等。

- **BOM**：浏览器对象模型（Browser Object Model），操作**浏览器部分功能**的API。比如让浏览器自动滚动。



### 输入输出

JavaScript中的输入输出主要用于调试

1、弹出警告框：**alert("str")**

2、控制台输出：**console.log("str")**

3、弹出输入框：**var a = prompt("str")**



## 基本语法

### 变量基本类型

ES6前，使用`var`关键字来声明一个变量

**JS中一共有六种数据类型：**

- **基本数据类型（值类型，传值）**：String 字符串、Number 数值、Boolean 布尔值、Null 空值、Undefined 未定义。
- **引用数据类型（引用类型，传址）**：Object 对象（除去五种基本数据类型之外，其他的，都称之为 Object类型）。

### 运算符

JS中运算符比较特殊的是`==`与`===`。

其中，`==`这个符号会自动进行隐式转换，将不同的数据类型，转为相同类型进行比较（大部分情况下，都是转换为数字）。而`===`这个符号在进行比较时不会进行类型转换，是绝对等于。





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