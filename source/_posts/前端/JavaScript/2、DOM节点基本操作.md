---
title: 2、DOM节点基本操作
date: 2020-06-21 14:38:46
tags:
	- JavaScript
categories:
	- 前端
	- JavaScript
fileName: js-dom-basic-handle
---

## 基本概念

### 节点

**节点**（Node）：构成 HTML 网页的最基本单元。网页中的每一个部分都可以称为是一个节点，比如：html标签、属性、文本、注释、整个文档等都是一个节点。

### DOM

**DOM**：Document Object Model，文档对象模型。DOM 为文档提供了结构化表示，并定义了如何通过脚本来访问文档结构。目的其实就是为了能让js操作html元素而制定的一个规范。

**解析过程**：HTML加载完毕，渲染引擎会在内存中把HTML文档，生成一个DOM树。

### 文档的加载

浏览器在加载一个页面时，是按照自上向下的顺序加载的，读取到一行就运行一行。如果将script标签写到页面的上边，在代码执行时，页面及DOM对象还没有加载，会导致无法获取到DOM对象。

onload 事件会在整个页面加载完成之后才触发。为 window 绑定一个onload事件，该事件对应的响应函数将会在页面加载完成之后执行，这样可以确保我们的代码执行时所有的DOM对象已经加载完毕了。

```
window.onload = function() {
}
```





## 获取节点

### 通过元素获取节点

```
// 获取id为“id1”的元素（id唯一）
var id1 = document.getElementById("id1");
```

### 通过标签名获取节点

```
// 获取标签名为div的 元素节点数组（标签名不唯一）
var div1 = document.getElementsByTagName("div");
// 获取该数组中第一个元素
document.getElementsByTagName("div1")[0]; 
```

### 通过类名获取节点

```
// 获取类名为 class1 的元素节点数组
var class1 = document.getElementsByClassName("class1");
document.getElementsByClassName("class1")[0];
```

### querySelector()方法

```
document.querySelector(css选择器);

// 获取文档中有 "target" 属性的第一个 <a> 元素
document.querySelector("a[target]");

// 找到符合条件的所有元素
document.querySelectorAll(css选择器);
```





## 遍历节点

遍历操作如下，用红框圈起来的方法功能是一样的，只是在不同版本的浏览器中兼容不同，建议使用红框中第二种方法

![](http://cdn.ziyedy.top/image/DOM%E8%8A%82%E7%82%B9%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C/%E9%81%8D%E5%8E%86%E8%8A%82%E7%82%B9.png)

### 获取父节点

```
节点.parentNode
```

### 获取兄弟节点

#### 下一个兄弟节点

```
下一个兄弟节点 = 节点.nextElementSibling || 节点.nextSibling
```

#### 上一个兄弟节点

```
上一个兄弟节点 = 节点.previousElementSibling || 节点.previousSibling
```

#### 任意兄弟节点

```
节点.parentNode.children[index];
```

### 获取子节点

#### 第一个/最后一个

```
第一个子元素节点 = 节点.firstElementChild || 节点.firstChild

最后一个子元素节点 = 节点.lastElementChild || 节点.lastChild
```

#### 获取所有子节点

```
子节点数组 = 父节点.childNodes; 

子节点数组 = 父节点.children;
```



## 操作节点

### 创建节点

```
新的标签(元素节点) = document.createElement("标签名");

文本节点 = document.createTextNode(文本)

文本片段 = document.createDocumentFragment();
```

#### 示例

```
<body>
    <ul id="myList"></ul>
    <script>
        var fragment = document.createDocumentFragment();
        var ul = document.getElementById("myList");
        var li = null;
        for(var i=0; i<3; i++) {
            li = document.createElement("li");
            li.appendChild(document.createTextNode("Item" + (i+1)));
            fragment.appendChild(li);
        }
        ul.appendChild(fragment);
    </script>
</body>
```

![](http://cdn.ziyedy.top/image/DOM%E8%8A%82%E7%82%B9%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C/%E5%88%9B%E5%BB%BA%E8%8A%82%E7%82%B9.png)





### 插入节点

#### 在最后插入一个子节点

```
父节点.appendChild(新的子节点);
```

#### 在参考节点前插入一个新的节点

```
父节点.insertBefore(新的子节点,作为参考的子节点)
```



### 删除节点

```
父节点.removeChild(子节点);
```



## 操作节点属性

操作节点属性都有以下两种方法，但一定不能混用，

### 获取节点的属性值

```
元素节点.属性名;
元素节点[属性名];
	
元素节点.getAttribute("属性名称");
```

### 设置节点属性值

```
元素节点.属性名 = 新属性值;
元素节点[属性名] = 新属性值;

元素节点.setAttribute("属性名", "新的属性值");
```

### 删除节点的属性

```
元素节点.removeAttribute(属性名);
```

### 类名的增删操作

```
Node.classList.add("class") // 添加class

Node.classList.remove("class") // 移除class

Node.classList.toggle("class") // 切换class，有则移除，无则添加

Node.classList.contains("class") // 检测是否存在class
```





## DOM的一些常见属性

### nodeType属性

- nodeType == 1  表示的是元素节点（标签） 。记住：在这里，元素就是标签。

- nodeType == 2  表示是属性节点。

- nodeType == 3  是文本节点。

### innerHTML

获取标签本身的html，也可以**通过赋值变相的添加节点**

#### 示例

```
<body>
    <div id="content"></div>
    <script>
        var content = document.getElementById("content");
        var str = "ui * 2 li"
                    + "<ul>"
                    + "<li> Item 1 </li>"
                    + "<li> Item 2 </li>"
                    + "</ul>";
        content.innerHTML = str;
        alert(content.innerHTML);
    </script>
</body>
```

如图可见，div内部已经多出了我们添加的元素

![](http://cdn.ziyedy.top/image/DOM%E8%8A%82%E7%82%B9%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C/innerHTML.png)