---
title: DPOD: 6D Pose Object Detector and Refiner
date: 2020-08-09 09:30:37
tags:
	- 6dof
categories:
	- AI
	- 论文阅读
fileName: DPOD-summary
---

### DPOD: 6D Pose Object Detector and Refiner

提出了一种从RGB图像进行3D目标检测和6D位姿估计的深度学习方法（DPOD，Dense Pose Object Detector），即密集姿势目标检测器。

用于估计输入图像和可用3D模型之间的密集2D-3D对应图，使用RANSAC与PnP解算6dof姿态，同时基于深度学习的定制细化方案对初始姿态估计值进行细化

简单来讲两个贡献

1、密集的2D-3D点对，比传统8个框点的对应具有更好的鲁棒性

2、一个效果不错的优化器



### 关键要素

1、多类对象对应掩膜ID的预测（the pixel-wise prediction of the multi-class object ID masks）

2、直接提供图像像素和3D模型顶点之间关系的对应映射的分类（classification of correspondence maps that directly provide a relation between image pixels and 3D model vertices）

**其中第2点保证了本方法的大量的像素对应（相对于那些使用8个框点进行pnp求解的方法来讲），也是本文dense的由来吧**

其中应该使用了UV的贴图技术将图像点与3D点进行对应（**UV map的使用应该是本文最大的亮点了**），但UV我只在3dmax等软件中看到过，对其原理并不理解，因此对该部分没有完全看懂。

![](http://cdn.ziyedy.top/image/DPOD/%E5%AF%B9%E5%BA%94%E6%A8%A1%E5%9E%8B.png)



### 整体架构

![](http://cdn.ziyedy.top/image/DPOD/%E6%95%B4%E4%BD%93%E6%9E%B6%E6%9E%84.png)

输入：RGB图像（320×240×3）

**对应模块**：编码器-解码器架构，编码器采用类似ResNet的12层架构（具有残差层，可加快收敛速度），解码器使用双线性内插和卷积层的堆叠进行上采样到原始大小

输出：目标ID掩码（标识是哪个物体）与其对应2D位置；UV对应图（提供了明确的2D-3D对应关系）

**姿态模块**：基于PnP+RANSAC有效计算各个6D姿势



### Refinement

图中绿色是已知的

![](http://cdn.ziyedy.top/image/DPOD/refinement.png)

结构：

使用ResNet做backbone，用ImageNet权重初始化，两个分支提取完特征后相减，再送到 E2 中，然后三个分支分别回归旋转R、XY、Z，每个头都是两个FC层。





### 损失函数

损失函数依然使用的加权的思想，充分考虑到了三个分支的状态



![](http://cdn.ziyedy.top/image/DPOD/%E6%8D%9F%E5%A4%B1%E5%87%BD%E6%95%B0.png)