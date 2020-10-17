---
title: Normalized Object Coordinate Space for Category-Level 6D Object Pose and Size Estimation
date: 2020-10-11 12:32:06
tags:
	- 6dof
categories:
	- AI
	- 论文阅读
fileName: nocs
---

> [CVPR 2019]Normalized Object Coordinate Space for Category-Level 6D Object Pose and Size Estimation
>
> 论文地址：https://arxiv.org/abs/1901.02970
>
> GitHub：https://github.com/hughw19/NOCS_CVPR2019

## 文章主要内容

### 问题提出

现有的相关6dof姿态估计方法都是instance-level的（即有目标物体的CAD模型作为先验知识）

相关的三维目标检测方法能够得到目标物体的3D边界框，但缺乏精确方向

### category-level的6dof姿态估计

作为严格意义上6dof的类别估计下的*第一篇文章*，本文对category-level的叙述非常简单易懂，需要解决的核心问题即为**如何对同属一类的不同实例进行统一规范的表示（a shared canonical representation）**

面临的主要挑战

1. 在特定类别中为不同实例寻找一种能够**统一表示**其*6D信息和尺寸信息*的表示方法
2. 现有的**数据集**很难作为训练和测试的基准（相关数据集缺乏相关注释，且很多不包含桌面级物体类别）

### 本文的主要贡献

针对上面两个问题，本文分别提出了相应的解决方案，相应的算法流程如下

![](http://cdn.ziyedy.top/NOCS/%E6%B5%81%E7%A8%8B.png)

#### 1、针对对同类物体的统一表示

> 本文提出了一种NOCS的共享空间关系（Normalized Object Coordinate Spac），用于建立物体像素点与NOCS中物体的信息的对应关系

NOCS是一个三维空间中的单位立方体，对给定的类别将其规范化并“放置”于该立方体当中。

![](http://cdn.ziyedy.top/NOCS/NOCS.png)

立方体中每个点存储的是一个3维的RGB信息，我个人认为这种思路与instance-level中的热力图的方法思路很相近，利用一种可视化的思路表示物体的朝向进而表示物体位姿（比如在NOCS中红色表示模型正前方，那么经过网络前向传播输出的NOCS图中红色最”浓“的地方就表示物体的正前方）

#### 2、针对缺乏数据集的问题

> 本文提出了一种CAMERA（Context-Aware MixEd ReAlity）的数据生成方法用于生成数据
>
> 模型来源于ShapeNet，同时创建时考虑了图像上下文（即在将模型2D投影嵌入背景前在Unity中使用灯光对其进行了渲染），并且加入了一些错误的类用以增加训练的鲁棒性



## 具体方法

### 网络结构

> 使用Mask R-CNN作为骨架网络，并且多了三条分支用于分别估计NOCS中的RGB

![](http://cdn.ziyedy.top/NOCS/%E7%BD%91%E7%BB%9C%E7%BB%93%E6%9E%84.png)

### 训练trick

1. 将回归NOCS变为了一个分类问题（经过文章的实验验证得出改为B=32的分类问题具有最佳的性能）
2. 在训练中没有使用深度图（作者使用COCO数据集来提高网络鲁棒性，而COCO不包含深度图）

### 后序6D位姿与尺寸估计

利用输出的NOCS图和深度图来进行被检测物体的6D姿态估计与尺寸大小检查。

首先将深度图与RGB图对齐，之后利用输出的Mask来获得物体的3D点云Pm，同时使用NOCS图获得预测位姿Pn。之后，**估计将Pn转换为Pm的比例、旋转和平移**。对于这个7维刚性变换估计问题，作者使用Umeyama算法，而对于离群点去除，作者使用RANSAC。

### 实验结果

#### 在合成数据上

对于50%的3D IoU，mAP为83.9%，位姿使用(5cm，5°)度量，mAP为40.9%

#### 在真实数据集上

对于50%的3D IoU， mAP为76.4%，位姿使用(5cm，5°)，mAP为10.2%

#### 与PoseCNN进行比较

使用2D重投影误差，本文的方法误差的mAP为30.2%，PoseCNN为17.2%



## 文章的不足与可改进之处

1. 显然，处理对称物体是6dof问题的一个难点，本文所提出的NOCS类别表示器并没有将**物体的对称性**考虑进去（只设计了处理对称物体的损失函数）
2. 文章的输入数据是RGB-D数据，可以该进的地方在于仅使用较容易获取的RGB数据
3. 文章中构建的NOCS表示方法仅仅利用了物体的朝向信息，而忽略了可能隐藏的刚体之间的几何等信息，处理类别个人认为应当使用网络对这些信息进行归纳与学习（新的问题）。