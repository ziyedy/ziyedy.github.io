---
title: 2、DOM节点操作
date: 2020-06-21 14:38:46
tags:
	- 前端
categories:
	- 前端
	- JS
fileName: javascript2
---

## DOM节点的操作

### 创建节点

```
新的标签(元素节点) = document.createElement("标签名");

文本节点 = document.createTextNode(文本)

文本片段 = document.createDocumentFragment();
```



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

能够创建不支持的标签名





innerHTML

```
<body>
    <div id="content"></div>
    <script>
        // document.write("<h1>创建节点</h1>");
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





