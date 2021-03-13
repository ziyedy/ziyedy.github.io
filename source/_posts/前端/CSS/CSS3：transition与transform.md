---
title: CSS3：transition与transform
date: 2020-08-15 15:08:01
tags:
	- 前端样式
categories:
	- 前端
	- CSS
fileName: css3-transition-transform
---

## 过渡

### transition

**transition实现元素不同状态间的平滑过渡**，可以用来制作动画效果

#### 属性

```css
/* 让哪些属性发生过渡，all表示所有属性 */
transition-property: all;

/*过渡的持续时间*/
transition-duration: 1s;

/*过渡的运动曲线
linear表示线性，ease表示减速，ease-in表示加速
也可以使用cubic-bezier，自定义曲线*/
transition-timing-function: linear;

/*多长时间后执行过渡*/
transition-delay: 1s;

/*综合写法*/
transition: 让哪些属性进行过度 过渡的持续时间 运动曲线 延迟时间;
```

#### 示例

给`<div class="box"></div>`添加如下css代码。

```
<style>
    .box {
        width: 200px;
        height: 200px;
        background-color: greenyellow;
        margin: 100px auto;
        /* 只渐变宽度，不渐变颜色 */
        transition: width 2s linear 0s;
    }
    .box:hover {
        width: 500px;
        background-color: blue;
    }
</style>
```



### transitionend（过渡完成事件）

transitionend 事件在 CSS **完成过渡后触发**。

#### 示例

在过渡事件的代码中添加如下代码

```
<script>
    const div = document.querySelector(".box");
    div.addEventListener("transitionend", function() {
        alert("过渡已完成！");
    })
</script>
```



## 转换（transform）

### 缩放：scale

参数解释：

 x：表示水平方向的缩放倍数；y：表示垂直方向的缩放倍数。大于1表示放大，小于1表示缩小

```
transform: scale(x, y);
transform: scale(0.8, 1.2)
```



### 位移：translate

参数为百分比，相对于自身移动。正值为向右和向下，只写一个值代表水平移动

```
transform: translate(水平位移, 垂直位移);
transform: translate(-30%, 30%);
```



### 旋转：rotate

正值代表顺时针。

```
transform: rotate(角度);
transform: rotate(45deg);
```

#### 旋转的坐标原点

参数可为百分比、长度以及定位位置的词（如left，center等）

```
transform-origin: x-axis y-axis z-axis;
```



### 3D转换

### 使用rotateX、rotateY、rotateZ进行旋转

```
transform: rotateX(360deg);
```

