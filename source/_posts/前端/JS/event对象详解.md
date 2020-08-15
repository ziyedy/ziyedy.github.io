---
title: 2.1、JS事件处理
date: 2020-08-15 22:51:57
tags:
	- 前端
categories:
	- 前端
	- JS
fileName: event-obj
---

## 事件

事件指的是一些特定的交互瞬间，如点击某个元素等等

JavaScript 是以**事件驱动为核心**的一门语言。JavaScript 与 HTML 之间的交互是通过事件实现的。

### 事件的基本要素

- 事件源：引发后续事件的html标签。

- 事件：js中已经定义好了（见下图）。

- 事件驱动程序：对样式和html的操作。也就是DOM。

![](http://cdn.ziyedy.top/image/JS%E4%BA%8B%E4%BB%B6%E5%A4%84%E7%90%86/js%E4%BA%8B%E4%BB%B6.png)

### 事件程序的基本书写

#### 1、获取事件源

详见DOM操作中的相关操作，主要有`document.getElementById()`等方法

#### 2、绑定事件

以onclick事件为例：

**方法1（绑定匿名函数）：**

```
element.onclick = function () {

}
```

**方法2（addEventListener）：**

```
element.addEventListener('click', function () {

}, false);
```

- 参数1：事件名的字符串(注意，没有on)

- 参数2：回调函数：当事件触发时，该函数会被执行

- 参数3：**true表示捕获阶段触发，false表示冒泡阶段触发（默认）**。如果不写，则默认为false。【重要】

**方法3（行内绑定）：**

```
<!--行内绑定-->
<div id="box1" onclick="fn()"></div>

<script type="text/javascript">
    function fn() {
        alert("我是弹出的内容");
    }
</script>
```





## 事件对象

当事件的响应函数被触发时，会产生一个事件对象`event`。浏览器每次都会将这个事件`event`作为实参传进之前的响应函数。

这个对象中包含了与当前事件相关的一切信息。比如鼠标的坐标、键盘的哪个按键被按下、鼠标滚轮滚动的方向等。

![](http://cdn.ziyedy.top/image/JS%E4%BA%8B%E4%BB%B6%E5%A4%84%E7%90%86/event%E4%BF%A1%E6%81%AF.png)

### 获取event对象

除去以上直接使用event外，标准兼容写法如下

```
<script>
    //点击页面的任何部分
    document.onclick = function (event) {
        event = event || window.event; //兼容性写法
    }
</script>
```



### event属性

![](http://cdn.ziyedy.top/image/JS%E4%BA%8B%E4%BB%B6%E5%A4%84%E7%90%86/event%E5%B1%9E%E6%80%A7.png)

除此之外，还有以下等信息：

```
keyCode	// 返回响应的键盘信息
```





## DOM事件流

事件传播的三个阶段是：事件捕获、事件冒泡和目标。

- 事件捕获阶段：事件从祖先元素往子元素查找（DOM树结构），直到捕获到事件目标 target。在这个过程中，默认情况下，事件相应的监听函数是不会被触发的。
- 事件目标：当到达目标元素之后，执行目标元素该事件相应的处理函数。如果没有绑定监听函数，那就不执行。
- 事件冒泡阶段：事件从事件目标 target 开始，从子元素往冒泡祖先元素冒泡，直到页面的最上一级标签。

![](http://cdn.ziyedy.top/image/JS%E4%BA%8B%E4%BB%B6%E5%A4%84%E7%90%86/%E6%8D%95%E8%8E%B7%E4%B8%8E%E5%86%92%E6%B3%A1.png)



### 捕获与冒泡实验

addEventListener函数最后一个参数为true代表事件在捕获阶段执行，为false代表在冒泡阶段进行

```
<body>
    <div><p>click here</p></div>
    <script>
        var div = document.getElementsByTagName("div")[0];
        var p = document.getElementsByTagName("p")[0];
        div.addEventListener("click", function() {
            console.log("div");
        }, true);
        p.addEventListener("click", function() {
            console.log("p");
        }, true);
    </script>
</body>
```

**实验结果**

![](http://cdn.ziyedy.top/image/JS%E4%BA%8B%E4%BB%B6%E5%A4%84%E7%90%86/%E5%86%92%E6%B3%A1%E5%AE%9E%E9%AA%8C%E7%BB%93%E6%9E%9C.png)