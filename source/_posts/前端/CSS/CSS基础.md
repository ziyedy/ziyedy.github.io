---
title: CSS盒子模型
date: 2020-08-04 16:07:30
tags:
	- 前端
categories:
	- 前端
	- CSS
fileName: css-basic-knowledge
---



## 盒子模型

盒子模型如下，即包含外边距（margin）、边界（border）、内边距（padding）和内容的一种模型。

![](CSS基础\盒子模型.png)

### padding

padding为内边距，其区域有背景颜色（与内容区域相同），background-color将填充所有border以内的区域

#### 写法

四个值：上、右、下、左

```
padding:30px 40px 30px 40px;
```

三个值：上、右、下、（左与右一样）

上下、左右

```
padding:30px 40px;
```



### margin

注意：**`<body>`标签默认有margin，为8px，因此很多情况下都需要加上以下语句清除margin。**

```
* {
    margin: 0;
    padding: 0;
}
```

其余操作与padding中差不多



### border

边框主要有三个要素：像素（粗细）、线型、颜色

```
border: 2px solid red;
```

