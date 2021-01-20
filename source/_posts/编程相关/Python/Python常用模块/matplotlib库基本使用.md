---
title: matplotlib库基本使用
date: 2020-08-21 13:58:33
tags:
	- matplotlib
categories:
	- 编程相关
	- Python
	- Python常用模块
fileName: python-matplotlib-use
---

## matplotlib库的基本用法

这是一个图表可视化的工具，和matlab中对应的图表可视化很像

参考文档以及一些可视化效果可见：http://matplotlib.org/gallery.html

导入：`import matplotlib.pyplot as plt`

### 绘图函数plot()

与matlab中对应的绘图函数类似。

```python
# X轴数据，列表或数组，（可选）
# Y轴数据，列表或数组，只写一个自动就是y轴数据
# 控制曲线的格式字符串，（可选）(颜色字符、风格字符、标记字符)
# 第二组或更多(x,y,format_string)
plt.plot(x, y, format_string, **kwargs)
```

### plt.gca()获取修改坐标轴

```

```





### pyplot的文本显示函数

| 函数           | 说明                     |
| -------------- | ------------------------ |
| plt.xlabel()   | 对X轴增加文本标签        |
| plt.ylabel()   | 对Y轴增加文本标签        |
| plt.title()    | 对图形整体增加文本标签   |
| plt.text()     | 在任意位置增加文本       |
| plt.annotate() | 在图形中增加带箭头的注解 |

其中annotate()函数中：xy表示箭头指向的坐标，xytest表示文字坐标，之后的arrowprops传入箭头的粗细颜色等信息，具体如下。

### 示例

```
import numpy as np
import matplotlib.pyplot as plt

a = np.arange(0.0, 5.0, 0.02)
plt.plot(a, np.cos(2*np.pi*a))

plt.xlabel("x-axis")
plt.ylabel("y-axis")
plt.title(r'$y = cos(2 \pi x)$')	# 使用latex
plt.annotate(r'$cos(2 \pi x)$', xy=(2,1), xytext=(3, 1.5),
            arrowprops=dict(facecolor='black', shrink=0.1, width=1))

plt.axis([-1, 6, -2, 2])
plt.grid(True)
plt.show()
```

![](http://cdn.ziyedy.top/image/matplotlib%E5%BA%93%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8/plot%E7%A4%BA%E4%BE%8B.png)



### plt.figure()

#### 基本理解

<img src="http://cdn.ziyedy.top/image/matplotlib%E5%BA%93%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8/fig%E4%B8%8Eaxis.png" style="zoom:67%;" />

#### 语法说明

```
figure(num=None, figsize=None, dpi=None, facecolor=None, edgecolor=None, frameon=True)

"""
      num : 图像编号或名称，数字为编号，字符串为名称
  figsize : 指定figure的宽和高，单位为英寸
      dpi : 指定绘图对象的分辨率，即每英寸多少个像素，缺省值为80
facecolor : 背景的颜色
edgecolor : 边框颜色
  frameon : 是否显示边框
"""

# 示例
import matplotlib.pyplot as plt

fig = plt.figure(figsize=(4, 3), facecolor='blue')
plt.plot([1 ,2, 3, 4])
plt.show()
```

![](http://cdn.ziyedy.top/image/matplotlib%E5%BA%93%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8/figure%E7%A4%BA%E4%BE%8B.png)



### plt.subplot()创建单个子图

即将绘图区域分为nrows行，ncols列，然后在从上向下从左到右第plot_number处进行绘图。使用与matlab中的函数基本一致，还有一些比较复杂的绘图函数，我个人认为没有什么必要（要是用在文章中放进ai再处理好了，没必要直接输出成图吧）

```
plt.subplot(nrows, ncols, plot_number)
```



### plt.subplots()创建多个子图

```
import numpy as np
import matplotlib.pyplot as plt

x = np.arange(0, 100)
#划分子图
fig, axes = plt.subplots(2, 2)
ax1 = axes[0, 0]
ax2 = axes[0, 1]
ax3 = axes[1, 0]
ax4 = axes[1, 1]

#作图1
ax1.plot(x, x)
#作图2
ax2.plot(x, x)
#作图3
ax3.plot(x, x ** 2)
#作图4
ax4.plot(x, np.log(x))
plt.show()
```

![](http://cdn.ziyedy.top/image/matplotlib%E5%BA%93%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8/subplots%E7%A4%BA%E4%BE%8B.png)





### 面向对象API

#### add_subplot新增子图

该方法与subplot方法类似，如下代码即与plt.subplots()中绘制第一幅图和第三幅图等价

```
import numpy as np
import matplotlib.pyplot as plt

x = np.arange(0, 100)
#新建figure对象
fig = plt.figure()
#新建子图1
ax1 = fig.add_subplot(2, 2, 1)
ax1.plot(x, x)
#新建子图3
ax3 = fig.add_subplot(2, 2, 3)
ax3.plot(x, x ** 2)
```

#### add_axes新增子区域（图中图）

```
import numpy as np
import matplotlib.pyplot as plt

#新建figure对象
fig = plt.figure()

#定义数据
x = [1, 2, 3, 4, 5, 6, 7]
y = [1, 3, 4, 2, 5, 8, 6]

#新建区域ax1
#figure的百分比，从figure 10%的位置开始绘制，宽高是figure的80%
left, bottom, width, height = 0.1, 0.1, 0.8, 0.8
#获得绘制的句柄
ax1 = fig.add_axes([left, bottom, width, height])
ax1.plot(x, y, 'r')
ax1.set_title('area1')


#新增区域ax2，嵌套在ax1内
left, bottom, width, height = 0.2, 0.6, 0.25, 0.25
#获得绘制的句柄
ax2 = fig.add_axes([left, bottom, width, height])
ax2.plot(x, y, 'b')
ax2.set_title('area2')
plt.show()
```

![](http://cdn.ziyedy.top/image/matplotlib%E5%BA%93%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8/%E5%9B%BE%E4%B8%AD%E5%9B%BE.png)



### 保存图像

使用plt.savefig()能很容易的实现，但应当注意，**必须写在plt.show()之前**，否则保存的图像会是空白的

```
plt.savefig('test.png')
```





## 常用可视化图表绘制

常见可视化图表如下

| 函数                              | 说明                                                |
| --------------------------------- | --------------------------------------------------- |
| plt.plot(x,y,fmt,…)               | 绘制一个坐标图                                      |
| plt.scatter(x,y)                  | 绘制散点图，其中，x和y长度相同                      |
| plt.bar(left,height,width,bottom) | 绘制一个条形图                                      |
| plt.contour(X,Y,Z,N)              | 绘制等值图                                          |
| plt.pie(data, explode)            | 绘制饼图（explode传入一个元组，表示一块出来的比例） |
| plt.hist(x,bins)                  | 绘制直方图（bins代表直方图个数）                    |
| plt.polar(theta, r)               | 绘制极坐标图                                        |
| ...                               | ...                                                 |

### 饼图（pie）

```
import numpy as np
import matplotlib.pyplot as plt

labels = 'A', 'B', 'C', 'D'
sizes = [10, 20 ,30, 40]
explode = (0, 0, 0, 0.15)
plt.pie(sizes, explode=explode, labels=labels)
plt.show()
```

![](http://cdn.ziyedy.top/image/matplotlib%E5%BA%93%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8/%E9%A5%BC%E5%9B%BE.png)



### 直方图（hist）

```
import numpy as np
import matplotlib.pyplot as plt

np.random.seed(0)
mu, sigma = 100, 20
a = np.random.normal(mu, sigma, size=100)
plt.hist(a, bins=40, facecolor='r')
plt.show()
```

![](http://cdn.ziyedy.top/image/matplotlib%E5%BA%93%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8/%E7%9B%B4%E6%96%B9%E5%9B%BE.png)



### 等值图（contour）

```
import numpy as np
import matplotlib.pyplot as plt

delta = 0.025
x = np.arange(-3.0, 3.0, delta)
y = np.arange(-3.0, 3.0, delta)
X, Y = np.meshgrid(x, y)
Z = np.exp(-X**2/12 - Y**2/2)
plt.contour(X, Y, Z)
plt.show()
```

![](http://cdn.ziyedy.top/image/matplotlib%E5%BA%93%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8/%E7%AD%89%E5%80%BC%E5%9B%BE.png)





## matplotlib总结图（源于知乎）

![图源自知乎](http://cdn.ziyedy.top/image/matplotlib%E5%BA%93%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8/matplotlib%E6%80%BB%E7%BB%93.jpg)



### 参考

https://www.dazhuanlan.com/2020/01/19/5e24219f9ae61/

mooc北理工相关课程

http://matplotlib.org/gallery.html

https://www.zhihu.com/question/51745620