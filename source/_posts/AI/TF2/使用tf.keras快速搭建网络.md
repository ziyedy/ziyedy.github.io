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

## 逐层搭建网络结构

### tf.keras.models.Sequential()

Sequential 函数是一个容器，封装了神经网络结构，主要可以进行以下输入：

```
# 直接在列表中加入各层
model = tf.keras.models.Sequential([layer1, layer2, ...])

# 使用add加入各层
model = tf.keras.models.Sequential()
model.add(layer1)
model.add(layer2)
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

损失函数常需要经过 softmax 等函数将输出转化为概率分布的形式。**from_logits 则用来标注该损失函数是否需要转换为概率的形式，取 False 时表示转化为概率分布，取 True 时表示没有转化为概率分布，直接输出**。

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

将上述流程封装到一个类中，即为：（**只需要重写构造函数和call函数即可**）

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



## 实例（以Fashion MNIST数据集）为例

### 载入并可视化数据

```
import tensorflow as tf
from tensorflow import keras
import matplotlib.pyplot as plt

fashion_mnist = keras.datasets.fashion_mnist
(train_images, train_labels), (test_images, test_labels) = fashion_mnist.load_data()

class_names = ['T-shirt/top', 'Trouser', 'Pullover', 'Dress', 'Coat',
               'Sandal', 'Shirt', 'Sneaker', 'Bag', 'Ankle boot']

plt.figure(figsize=(10, 10))
for i in range(100, 125):
    plt.subplot(5, 5, i-99)
    plt.imshow(train_images[i])
    plt.title(class_names[train_labels[i]])
plt.show()
```

<img src="http://cdn.ziyedy.top/image/%E4%BD%BF%E7%94%A8tf.keras%E5%BF%AB%E9%80%9F%E6%90%AD%E5%BB%BA%E7%BD%91%E7%BB%9C/%E6%95%B0%E6%8D%AE%E5%8F%AF%E8%A7%86%E5%8C%96.png" style="zoom: 80%;" />

### 进行数据预处理，将值置于0-1范围内

```
train_images = train_images / 255.0
test_images = test_images / 255.0
```

### 构建网络并进行训练

```
model = keras.Sequential([
    keras.layers.Flatten(input_shape=(28, 28)),
    keras.layers.Dense(128, activation='relu'),
    keras.layers.Dense(10)
])

model.compile(optimizer='adam',
              loss=keras.losses.SparseCategoricalCrossentropy(from_logits=True),
              metrics=['accuracy'])

history = model.fit(train_images, train_labels, epochs=10)
```

### 进行评估并绘制训练曲线

```
test_loss, test_acc = model.evaluate(test_images, test_labels)

plt.plot(history.history['accuracy'], label='accuracy')
plt.plot(history.history['loss'], label='loss')
plt.xlabel('Epoch')
plt.ylabel('Accuracy and Loss')
plt.legend(loc='lower left')
plt.show()
```

<img src="http://cdn.ziyedy.top/%E4%BD%BF%E7%94%A8tf.keras%E5%BF%AB%E9%80%9F%E6%90%AD%E5%BB%BA%E7%BD%91%E7%BB%9C/%E8%AE%AD%E7%BB%83%E6%9B%B2%E7%BA%BF.png" style="zoom:67%;" />

### 使用模型进行预测

```
predict_model = keras.Sequential([
    model,
    tf.keras.layers.Softmax()
])
prediction = predict_model.predict(test_images)
print(prediction[0])
# [7.7702840e-05 5.1073805e-08 1.0888911e-05 1.1339294e-07 5.7997750e-06
#  1.0903468e-03 8.1863102e-05 2.4046257e-02 5.1835599e-07 9.7468650e-01]

plt.subplot(1, 2, 1)
plt.imshow(test_images[0])
plt.title(class_names[tf.argmax(prediction[0])])
plt.subplot(1, 2, 2)
plt.bar(class_names, prediction[0])
plt.show()
```

<img src="http://cdn.ziyedy.top/image/%E4%BD%BF%E7%94%A8tf.keras%E5%BF%AB%E9%80%9F%E6%90%AD%E5%BB%BA%E7%BD%91%E7%BB%9C/%E9%A2%84%E6%B5%8B%E7%BB%93%E6%9E%9C.png" style="zoom:80%;" />



### 参考链接

https://www.tensorflow.org/tutorials/