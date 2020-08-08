---
title: 使用tf.keras快速搭建网络
date: 2020-08-07 20:22:41
tags:
	- AI
categories:
	- AI
	- TF2
fileName: tf2-keras-build-nn
---

主要在于与《TF2原生语法构建简单网络》的比较

### 逐层搭建网络结构

#### tf.keras.models.Sequential()

Sequential 函数是一个容器，封装了神经网络结构，主要可以进行以下输入：

```
model = tf.keras.models.Sequential()
```

#### 压平层

将输入特征拉直为一维向量

```
tf.keras.layers.Flatten()
```

#### 全连接层

```
tf.keras.layers.Dense(输出神经元个数,
					  activation="激活函数",
					  kernel_regularizer="正则化方式"
)
```

**activation可选**：relu、softmax、sigmoid、tanh等

**kernel_regularizer可选**：tf.keras.regularizers.l1()、tf.keras.regularizers.l2()

#### 卷积层

```
tf.keras.layers.Conv2D( filter = 卷积核个数,
						kernel_size = 卷积核尺寸,
						strides = 卷积步长,
						padding = “valid” or “same”)
```

#### LSTM层

```
tf.keras.layers.LSTM()
```

#### 实践

在TF2原生语法构建简单网络的例子中，改用该api可以简化为：

```
model = tf.keras.models.Sequential([
    tf.keras.layers.Dense(3, activation='softmax', kernel_regularizer=tf.keras.regularizers.l2())
])
```





### 训练网络（在 model.compile()中配置训练方法）

```
model.compile(optimizer=优化器,
			  loss=损失函数,
			  metrics=["准确率"]
)
```

#### optimizer

**optimizer 可以是字符串形式给出的优化器名字，也可以是函数形式 ，使用函数形式可以设置学习率、动量和超参数。**

可选项：

```
‘sgd’or tf.optimizers.SGD( lr=学习率, decay=学习率衰减率, momentum=动量参数)

‘adagrad’or tf.keras.optimizers.Adagrad(lr=学习率, decay=学习率衰减率)

‘adadelta’or tf.keras.optimizers.Adadelta(lr=学习率, decay=学习率衰减率)

‘adam’or tf.keras.optimizers.Adam (lr=学习率, decay=学习率衰减率)
```

#### loss

**Loss 可以是字符串形式给出的损失函数的名字，也可以是函数形式。**

```
‘mse’ or tf.keras.losses.MeanSquaredError()

‘sparse_categorical_crossentropy' or tf.keras.losses.SparseCategoricalCrossentropy(from_logits=False)
```

损失函数常需要经过 softmax 等函数将输出转化为概率分布的形式。from_logits 则用来标注该损失函数是否需要转换为概率的形式，取 False 时表示转化为概率分布，取 True 时表示没有转化为概率分布，直接输出。

#### metrics

> ‘accuracy’：y\_和 y 都是数值，如 y\_=[1] y=[1]。
> ‘categorical_accuracy’：y\_和 y 都是以独热码和概率分布表示。如 y_=[0, 1, 0], y=[0.256, 0.695, 0.048]。
> ‘sparse_ categorical_accuracy’：y\_是以数值形式给出，y 是以独热码形式给出。如 y_=[1],y=[0.256, 0.695, 0.048]。

#### 实践

```
model.compile(optimizer=tf.keras.optimizers.SGD(lr=0.1),
              loss=tf.keras.losses.SparseCategoricalCrossentropy(from_logits=False),
              metrics=['sparse_categorical_accuracy'])
```



### 在model.fit()中执行训练过程

```
model.fit( 训练集的输入特征， 训练集的标签， batch _size, epochs,
		   validation_data = ( 测试集的输入特征，测试集的标签 ) ，
		   validataion_split = 从测试集划分多少比例给训练集，
		   validation_freq = 测试的 epoch 间隔次数
           )
```

#### 实践

```
model.fit(x_train, y_train, batch_size=32, epochs=500, validation_split=0.2, validation_freq=20)
```

同时，在最后可以使用`model.summary()`查看网络参数量等基本信息。



### 使用class封装上述过程

将上述流程封装到一个类中，即为：

```
import tensorflow as tf
from tensorflow.keras.layers import Dense
from tensorflow.keras import Model

class IrisModel(Model):
    def __init__(self):
        super(IrisModel, self).__init__()
        self.d1 = Dense(3, activation='softmax', kernel_regularizer=tf.keras.regularizers.l2())

    def call(self, x):
        y = self.d1(x)
        return y

model = IrisModel()
model.compile(optimizer=tf.keras.optimizers.SGD(lr=0.1),
              loss=tf.keras.losses.SparseCategoricalCrossentropy(from_logits=False),
              metrics=['sparse_categorical_accuracy'])

model.fit(x_train, y_train, batch_size=32, epochs=500, validation_split=0.2, validation_freq=20)
model.summary()
```

