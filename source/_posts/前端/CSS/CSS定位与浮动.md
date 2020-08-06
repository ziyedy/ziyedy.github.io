---
title: CSS定位与浮动
date: 2020-08-04 16:07:30
tags:
	- 前端
categories:
	- 前端
	- CSS
fileName: css-float-position
---

## Web标准文档流

### 行内元素与块级元素

#### 行内元素（p、span、a、b、i、u、em等）

- 与其他行内元素并排；
- 不能设置宽、高。默认的宽度，就是文字的宽度。

#### 块级元素（div等）

- 霸占一行，不能与其他任何元素并列；
- 能接受宽、高。如果不设置宽度，那么宽度将默认变为父亲的100%。

#### 块级元素转行内元素

```
display: inline;
```

#### 行内元素转块级元素

```
display: block;
```

### 标准文档流

Web界面可以视为一个流，从上往下，主要有以下特性。

#### 特性

**1、空白折叠**

无论多少个空格、换行、tab，都会折叠为一个空格。

**2、同行的元素底边对齐**

**3、一行空间不够，自动换行**

#### 使元素脱离标准文档流的方法

- 浮动
- 绝对定位
- 固定定位



## 浮动

```
float: left;
```

### 性质

1、浮动元素脱标

2、浮动元素相互贴靠（改变窗口大小即可看到，窗口够大，就会自动贴靠）

3、文字会自动包围浮动元素

### 清除浮动

避免如下情况导致的布局问题，应当进行浮动清除

<img src="http://cdn.ziyedy.top/image/%E5%89%8D%E7%AB%AF/CSS/CSS%E5%AE%9A%E4%BD%8D%E4%B8%8E%E6%B5%AE%E5%8A%A8/%E6%B5%AE%E5%8A%A8%E5%87%BA%E9%94%99.png" style="zoom: 67%;" />

#### 1、给浮动元素的祖先设置合适的高度（大于孩子的高度）

缺点：不能适应页面的快速变化

```
<div>     //设置height
	<p></p>
</div>

<div>    //设置height
	<p></p>
</div>
```

#### 2、clear: both; （即不允许左侧和右侧有浮动对象）

缺点：margin失效，两个div之间没有间隙

```
<div>
	<p></p>
</div>

<div>   //clear:both;
	<p></p>
</div>
```

#### 3、在两个浮动元素之间插入一个div

#### 4、overflow: hidden;

推荐使用，在父元素上添加该元素。



## 定位

### 相对定位

即相对于元素自己**原来的位置**，进行位置调整。

相对定位**不脱标**，即相对定位的真实位置还在元素的原始位置，只不过影子出去了，可以到处飘。

#### 使用方法

```
position: relative;
```

- left：盒子右移

- right：盒子左移

- top：盒子下移

- bottom：盒子上移

负数表示相反的方向

#### 用处

1、微调元素

2、作为绝对定位的参考，子绝父相

#### 示例

HTML元素

```
	<div class="div1">原始状态</div>
    <div class="div2">绝对定位left40px,top40px</div>
    <div class="div3">原始状态</div>
```

CSS样式

```css
div {
    width: 200px;
    height: 200px;
    background-color: red;
}
.div2{
    position: relative;
    left: 40px;
    top: 40px;
}
.div3{
    background-color: aqua;
}
```

表现：

<img src="http://cdn.ziyedy.top/image/%E5%89%8D%E7%AB%AF/CSS/CSS%E5%AE%9A%E4%BD%8D%E4%B8%8E%E6%B5%AE%E5%8A%A8/%E7%BB%9D%E5%AF%B9%E5%AE%9A%E4%BD%8D.png" style="zoom: 67%;" />



### 绝对定位

定义横纵坐标。原点在父容器的左上角或左下角。横坐标用left表示，纵坐标用top或者bottom表示。

```
	position: absolute;  /*绝对定位*/
	left: 10px;  /*横坐标*/
	top/bottom: 20px;  /*纵坐标*/
```

#### 脱标特性

即所有的标准文档流的性质，绝对定位之后都不遵守了，标签也不区分所谓的行内元素、块级元素了

#### 参考点

（1）如果用**top描述**，那么参考点就是**页面的左上角**，而不是浏览器的左上角

<img src="http://cdn.ziyedy.top/image/%E5%89%8D%E7%AB%AF/CSS/CSS%E5%AE%9A%E4%BD%8D%E4%B8%8E%E6%B5%AE%E5%8A%A8/%E5%AE%9A%E4%BD%8D1.png" style="zoom:75%;" />

（2）如果用**bottom描述**，那么参考点就是**浏览器首屏窗口尺寸**（好好理解“首屏”二字），对应的页面的左下角

<img src="http://cdn.ziyedy.top/image/%E5%89%8D%E7%AB%AF/CSS/CSS%E5%AE%9A%E4%BD%8D%E4%B8%8E%E6%B5%AE%E5%8A%A8/%E5%AE%9A%E4%BD%8D2.png" style="zoom:75%;" />

（3）一个绝对定位的元素，如果父辈元素中也出现了已定位（无论是绝对定位、相对定位，还是固定定位）的元素，那么将以父辈这个元素，为参考点。

<img src="http://cdn.ziyedy.top/image/%E5%89%8D%E7%AB%AF/CSS/CSS%E5%AE%9A%E4%BD%8D%E4%B8%8E%E6%B5%AE%E5%8A%A8/%E5%AE%9A%E4%BD%8D3.png" style="zoom:75%;" />

子绝父相：使得整体不脱离文档流

同时，**绝对定位是从父元素的border内侧为参考点，而不是以内容**



### 固定定位

相对浏览器窗口进行定位。**无论页面如何滚动，这个盒子显示的位置不变**。

#### 用途

1、网页右下角的“返回到顶部”

2、顶部导航栏（假设顶部导航条的高度是60px，那么，为了防止其他的内容被导航条覆盖，我们要给body标签设置60px的padding-top）



### z-index属性

表示谁压着谁，数值大的压盖住数值小的。

1、默认的z-index值是0

2、定位了的元素，永远能够压住没有定位的元素

3、从父现象：父亲怂了，儿子再牛逼也没用







