---
title: BOM基本内容
date: 2020-06-21 14:38:46
tags:
	- JavaScript
categories:
	- 编程相关
	- JavaScript
fileName: js-bom-basic-handle
---

## 常见的BOM对象

BOM可以让我们通过JS来操作浏览器。BOM中为我们提供了一些对象，来完成对浏览器相关的操作。

常见的 BOM对象有：

- Window：代表整个浏览器的窗口，同时 window 也是**网页中的全局对象**。

- Navigator：代表当前浏览器的信息，通过该对象可以识别不同的浏览器。

- Location：代表当前浏览器的地址栏信息，通过 Location 可以获取地址栏信息，或者操作浏览器跳转页面。

- History：代表浏览器的历史记录，通过该对象可以操作浏览器的历史记录。由于隐私原因，该对象不能获取到具体的历史记录，只能操作浏览器向前或向后翻页，而且该操作只在当次访问时有效。

- Screen：代表用户的屏幕信息，通过该对象可以获取用户的显示器的相关信息。

备注：这些 BOM 对象都是**作为 window 对象的属性**保存的，可以通过window对象来使用，也可以直接使用。比如说，我可以使用 `window.location.href`，也可以直接使用 `location.href`，二者是等价的。

备注2：不要忘了，之前学习过的`document`也是在`window`中保存的。



## Navigator

Navigator代表当前浏览器的信息，通过该对象可以识别不同的浏览器。

通常使用`navigator.userAgent`来获取浏览器信息



## History

History对象：可以用来操作浏览器的向前或向后翻页。

### 属性

获取浏览器历史列表中的 url 数量。注意，只是统计当次的数量，如果浏览器关了，数量会重置为1。

```
history.length
```

### 方法

回退到上一个页面，作用和浏览器的「回退按钮」一样。

```
history.back();
```

跳转下一个页面，作用和浏览器的「前进按钮」一样。

```
history.forward();
```

**go方法**

```
// 向前跳转2个页面
history.go( 2 );

// 向后跳转2个页面
history.go( -2 );

// 刷新当前页面
history.go( 0 );
```



## Location 

Location 对象：封装了浏览器地址栏的 URL 信息。

### 属性

获取当前页面的 url 路径

```
location.href
```

也可以通过设置该属性的方法使页面跳转

```
location.href = 'ziyedy.top';
```

### 方法

用来跳转到其他的页面，作用和直接修改`location.href`一样。

```
location.assign(str);
```

用于重新加载当前页面，作用和刷新按钮一样。在方法的参数中传递一个true，则会强制清空缓存刷新页面.

```
location.reload();
location.reload(true);
```

使用一个新的页面替换当前页面，调用完毕也会跳转页面。但不会生成历史记录，不能使用「后退按钮」后退。

```
location.replace();
```





