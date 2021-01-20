---
title: TF2实现经典CNN架构
date: 2020-08-24 17:08:00
tags:
	- TensorFlow2
categories:
	- AI相关
	- TensorFlow2
fileName: tf2-classic-cnn
---

## 卷积神经网络经典操作

### 卷积操作

```
tf.keras.layers.Conv2D (
	filters = 卷积核个数,
	kernel_size = 卷积核尺寸, # 正方形写核长整数，或（核高h，核宽w）
	strides = 滑动步长, # 横纵向相同写步长整数，或(纵向步长h，横向步长w)，默认1
	padding = “same” or “valid”, # same表示全零填充，valid为默认值
	activation = "relu" # 激活函数，如有BN，此处不写
	input_shape = (高, 宽 , 通道数) # 输入特征图维度，可省略
)
```



### 批标准化（Batch Normalization，BN）

```
tf.keras.layers.BatchNormalization()
```



### 激活函数（使用BN的情况下才要）

```
Activation(对应激活函数)
```



### 池化（Pooling）

```
# 最大池化
tf.keras.layers.MaxPool2D(
	pool_size= 池化核尺寸,
	strides= 池化步长, # 默认为pool_size
	padding=‘valid’ or ‘same’
)

# 平均池化
tf.keras.layers.AveragePooling2D(
	pool_size= 池化核尺寸,
	strides= 池化步长, # 默认为pool_size
	padding=‘valid’or‘same’
)
```



### Dropout

```
tf.keras.layers.Dropout(舍弃概率)
```



### VGG