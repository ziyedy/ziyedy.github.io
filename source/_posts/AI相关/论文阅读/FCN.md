---
title: Fully Convolutional Networks for Semantic Segmentation
date: 2020-03-23 11:50:01
tags:
	- 语义分割
categories:
	- AI相关
	- 论文阅读
fileName: FCN-summary
---

### Fully Convolutional Networks for Semantic Segmentation

### 核心思想

建立全卷积神经网络，将经典的分类网络如AlexNet、VGG、GoogLeNet中网络结构最后的全连接层用上采样替换

pixelwise prediction





### 主要内容

1. **全卷积化**

* 将经典CNN网络结构最后的全连接层替换为卷积层（即对最后一个特征图（feature）进行反卷积操作），使得输出图像与输入图像的尺寸完全一致。
* 经典CNN网络全连接层后对图像进行分类改为了对所有像素都进行softmax分类计算。可以理解为传统分类网络将一幅图像作为一个分类样本，而FCN则将图像中的每一个像素作为分类样本。这样其实就解决了图像语义分割的问题，即进行了密集预测（dense prediction）



2. 进行了对特征图的**上采样**

文中主要提出了三种上采样方法

1. Shift-and-Stitch
2. decreasing subsampling
3. Deconvolution



3. 使用了**跳跃结构**（ skip architecture）

> We address this by **adding skips** that combine the final prediction layer with lower layers with finer strides

使用跳跃结构融合多层输出，使得网络能够预测更多的位置信息。因为在浅层网络位置信息等保留的比较好，将他们加入到深层输出中，就可以预测到更精细的信息。

本文的做法为将对应的两个位置的网络进行逐元素相加的处理。