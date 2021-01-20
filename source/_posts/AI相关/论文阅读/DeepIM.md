---
title: DeepIM：Deep Iterative Matching for 6D Pose Estimation
date: 2020-08-08 22:41:52
tags:
	- 6dof姿态估计
categories:
	- AI相关
	- 论文阅读
fileName: deepim-summary
---

### DeepIM: Deep Iterative Matching for 6D Pose Estimation

这篇论文可以理解为6d姿态估计问题中的一个refinement行为，经过测试可以使RGB数据的精度达到RGB-D数据的程度。

具体可见该论文。

### 主要问题

鉴于直接从图像中回归得到的物体姿态精度不高，将输入图像和物体渲染得到的图像进行匹配，能够得到更加精确的结果。

本文提出的DeepIM深度神经网络：给定一个初始位姿，网络能将观测图像和渲染得到的图像匹配，迭代地优化位姿。网络训练后能够预测一个相对的姿态变换，使用了一种分开表示的3D坐标和3D朝向，并且是用了一种迭代的训练过程。



### 算法综述

给定图像中一个物体的初始6D位姿，DeepIM能够将物体渲染得到的图像和当前观测的图像匹配，预测一个相对的SE(3)变换；之后迭代地根据估计的更为精确的位姿重新渲染物体得到渲染图像，并和观测图像相匹配，从而会变得越来越相似，使网络得到越来越精确的位姿。整体框架如下图所示：

![](http://cdn.ziyedy.top/image/DeepIM/%E7%BD%91%E7%BB%9C%E7%A4%BA%E6%84%8F%E5%9B%BE.png)

### 网络结构

**输入：**观测图像、渲染图像以及对应的蒙板区域串联形成一个8通道（观测和渲染图像各自具有3通道，蒙板具有1通道）的张量

**主干网络**同时本文使用预测图像间光流的FlowNetSimple架构作为支撑网络；位姿估计分支将由FlowNetSimple网络的11层卷积层得到的特征图作为输入，之后经过2个256维的全连接层，再分别由两个全连接层得到四元数表示的3D旋转和3D平移。

主干网络如下（关于光流法我也不清楚，算一个坑吧）

![](http://cdn.ziyedy.top/image/DeepIM/FlowNetSimple1.png)

![](http://cdn.ziyedy.top/image/DeepIM/FlowNetSimple2.png)



**网络采用的分支（仅用于训练）：**在训练过程中，增加了两个辅助的分支来归一化网络的特征表示以提高训练的稳定性。一个分支训练后预测渲染图像和观测图像的光流，另一个分支预测观测图像的前景蒙板；

![](http://cdn.ziyedy.top/image/DeepIM/%E7%BD%91%E7%BB%9C%E7%BB%93%E6%9E%84.png)



### 损失函数

以上三个分支分别对应一个损失函数，经过加权后共同构成了本文网络的损失函数，其中a=0.1，b=0.25，c=0.03
$$
L = aL_{pose} + bL_{flow} + cL_{mask}
$$
