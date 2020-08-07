---
title: TF2的基础函数
date: 2020-08-07 10:22:41
tags:
	- AI
categories:
	- AI
	- TF2
fileName: TF2-basic-function
---



### 张量生成

#### 直接创建

```
tf.constant(张量内容, dtype=数据类型<可选>)
```

#### 创建全为0的张量

```
tf.zeros(维度)
```



#### 创建全为1的张量

```
tf.ones(维度)
```

#### 创建全为指定值的张量

```
tf.fill(维度, 指定值)
```



#### 将numpy数据转化为Tensor数据

```
tf.convert_to_tensor(数据名, dtype=数据类型<可选>)
```



#### 生成正态分布的随机数，默认均值为0，标准差为1

```
tf.random.normal(维度, mean=均值, stddev=标准差)
```



#### 生成截断式正态分布随机数

```
tf.random.truncated_normal(维度, mean=均值, stddev=标准差)
```

#### 生成均匀分布随机数（[minval, maxval)）

```
tf.random.uniform(维度, minval=0, maxval=1)
```



### 常用函数

#### 将输入特征与标签配对

```
tf.data.Dataset.from_tensor_slices( (输入特征, 标签) )
```



#### 强制tensor转换为该数据类型

```
tf.cast(张量名, dtype=数据类型)
```



#### 将变量标记为“可训练”

使用该方法标记的变量会在反向传播中记录梯度信息

```
tf.Variable(初始值)
```





#### 某个函数对指定参数的求导运算

with 结构记录计算过程，gradient求出张量的梯度

```
with tf.GradientTape() as tape:
    若干个计算过程
grad = tape.gradient(函数, 对谁求导)
```



#### 自减函数（常用于更新参数的值）

需要先用`tf.Variable(w) `定义w为可训练

```
w.assign_sub(w要自减的内容)
```



#### 独热编码

分类问题中，常用独热码做标签标记类别，1表示是，2表示非

```
tf.one_hot(带转换数据, depth=几分类)
```



#### softmax：使数值变为0-1之间的概率分布

```
tf.nn.softmax(张量名)
```



#### argmax：返回张量沿指定维度最大值的索引

```
tf.argmax(张量名, axis=操作轴)
```







### 张量数值统计

#### 计算张量元素最小值/最大值

```
tf.reduce_min()
tf.reduce_max()
```

#### 计算张量沿着指定维度的平均值（不指定维度对所有操作）

```
tf.reduce_mean(张量名, axis=操作轴)
```

#### 计算张量沿着指定维度的和

```
tf.reduce_sum(张量名, axis=操作轴)
```



### 数学运算

#### 两个张量对应元素相加

```
tf.add(张量1, 张量2)
```

#### 相减

```
tf.subtract()
```

#### 相乘

```
tf.multiply
```



#### 相除

```
tf.divide
```



#### 计算张量的平方

```
tf.square(张量名)
```

#### n次方

```
tf.pow(张量名, n次方)
```

#### 开方

```
tf.sqrt(张量名)
```

#### 两个矩阵相乘

```
tf.matmul(矩阵1, 矩阵2)
```
