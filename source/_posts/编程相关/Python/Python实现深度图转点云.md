---
title: Python实现深度图转点云
date: 2020-10-04 10:44:17
tags:
	- 3D视觉
	- 点云
categories:
	- 编程相关
	- Python
fileName: python-depth-points
---

## 理论基础

深度图转点云本质上我认为就算利用深度信息**将图像从像素坐标系转化到相机坐标系的过程**（很多资料说是转到世界坐标系，但我认为这是一个如何定义世界坐标系的问题）

![](http://cdn.ziyedy.top/Python%E5%AE%9E%E7%8E%B0%E6%B7%B1%E5%BA%A6%E5%9B%BE%E8%BD%AC%E7%82%B9%E4%BA%91/%E5%9D%90%E6%A0%87%E7%B3%BB.jpg)

忽略推导过程，其实可以归纳为从图像点`[u, v]`到相机坐标点`[x_c, y_c, z_c]`的过程，具体公式如下（其中depth(u, v)表示深度图(u, v)位置的像素值）
$$
x_c = z_c (u - u_0) / f_x \\
y_c = z_c (v - v_0) / f_y \\
z_c = depth(u, v)
$$



## Python实现

### 深度图

![深度图](http://cdn.ziyedy.top/Python%E5%AE%9E%E7%8E%B0%E6%B7%B1%E5%BA%A6%E5%9B%BE%E8%BD%AC%E7%82%B9%E4%BA%91/%E6%B7%B1%E5%BA%A6%E5%9B%BE.png)

### 转换代码

```
def depth2pc(depth_img):
    """
    深度图转点云数据
    图像坐标系 -> 世界坐标系 
    :param depth_img: 深度图
    :return: 点云数据 N*3
    """
    
    # 相机内参
    cam_fx = 1120.12
    cam_fy = 1120.12
    cam_cx = 640.5
    cam_cy = 360.5
    factor = 1

	# 逐点处理，此过程可以使用numpy优化
    m, n = depth_img.shape
    point_cloud = []
    for v in range(m):
        for u in range(n):
            if depth_img[v, u] == 0:
                continue
            depth = depth_img[v, u]
            p_z = depth / factor
            p_x = (u - cam_cx) * p_z / cam_fx
            p_y = (v - cam_cy) * p_z / cam_fy
            point_cloud.append([p_x, p_y, p_z])

    point_cloud = np.array(point_cloud)

    return point_cloud
```

### 转换后的点云

*略去了将桌面等背景略去的相关代码*，转换后的点云数据如下

![](http://cdn.ziyedy.top/Python%E5%AE%9E%E7%8E%B0%E6%B7%B1%E5%BA%A6%E5%9B%BE%E8%BD%AC%E7%82%B9%E4%BA%91/%E7%82%B9%E4%BA%91%E6%95%B0%E6%8D%AE.png)

### 参考

由于本人是初次接触点云相关数据，而任务又有时间要求，所以没有使用较为主流的C++下的pcl库，未来两个月打算学习以下点云相关的操作与理论，届时再进行更新

https://blog.csdn.net/weixin_30284355/article/details/98320637