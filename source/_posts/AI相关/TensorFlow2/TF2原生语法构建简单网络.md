---
title: TF2原生语法构建简单网络
date: 2020-08-07 10:22:41
tags:
	- TensorFlow2
categories:
	- AI相关
	- TensorFlow2
fileName: tf2-basic-builder-simple-nn
---

## 基本思路

### 数据集

本实验采用的数据为鸢尾属植物数据集，存储了150个样本的4个特征，分别对应了3类不同的鸢尾属植物。

### 基本流程可视化

每个数据可以视为1×4的向量，且最后对应3类，那么权重w就应该是4×3，偏置量b就应该是1×3。

![](http://cdn.ziyedy.top/image/AI/TF2/%E6%9D%83%E9%87%8D%E7%A4%BA%E6%84%8F%E5%9B%BE.png)

上述过程可以即可以视为一个全连接神经网络，可视化如下。

![](http://cdn.ziyedy.top/image/AI/TF2/%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C%E7%A4%BA%E6%84%8F%E5%9B%BE.png)



## 主要流程

### 一、数据准备

#### 1、数据集读入

```
from sklearn.datasets import datasets
x_data = datasets.load_iris().data
y_data = datasets.load_iris().target
```



#### 2、数据集乱序

```
np.random.seed(116)
np.random.shuffle(x_data)
np.random.seed(116)
np.random.shuffle(y_data)
```



#### 3、生成训练集和测试集，并根据需要转换数据格式

```
# 取后30个为测试集，其余为训练集
x_train = x_data[:-30]
y_train = y_data[:-30]
x_test = x_data[-30:]
y_test = y_data[-30:]

# 转换x的数据类型，避免数据不一致的问题
x_train = tf.cast(x_train, tf.float32)
x_test = tf.cast(x_test, tf.float32)
```



#### 4、将（输入特征，标签）组成对，每次读入一部分（batch）

```
train_db = tf.data.Dataset.from_tensor_slices((x_train, y_train)).batch(32)
test_db = tf.data.Dataset.from_tensor_slices((x_test, y_test)).batch(32)
```



### 二、搭建网络，迭代进行训练

#### 1、定义神经网络中所有可训练参数

```
w1 = tf.Variable(tf.random.truncated_normal([4, 3], stddev=0.1, seed=1))
b1 = tf.Variable(tf.random.truncated_normal([3], stddev=0.1, seed=1))
```



#### 2、定义一些超参数，及后续可能会用到的参数

```
lr = 0.1  # 学习率为0.1
train_loss_results = []  # 将每轮的loss记录在此列表中，为后续画loss曲线提供数据
test_acc = []  # 将每轮的acc记录在此列表中，为后续画acc曲线提供数据
epoch = 500  # 循环500轮
loss_all = 0  # 每轮分4个step，loss_all记录四个step生成的4个loss的和
```



#### 3、嵌套循环迭代，with结构更新参数，显示loss等信息

```
# 数据集级别的循环，每个epoch循环一次数据集
for epoch in range(epoch):  
	# batch级别的循环 ，每个step循环一个batch
    for step, (x_train, y_train) in enumerate(train_db):
    	# with结构记录梯度信息
        with tf.GradientTape() as tape:
        	# 前向传播计算y_
            y = tf.matmul(x_train, w1) + b1
            y = tf.nn.softmax(y)
            y_ = tf.one_hot(y_train, depth=3)
            # 计算总loss
            loss = tf.reduce_mean(tf.square(y_ - y))
            loss_all += loss.numpy()
            
        # 计算loss对各个参数的梯度
        grads = tape.gradient(loss, [w1, b1])

        # 参数自更新
        w1.assign_sub(lr * grads[0])  # 参数w1自更新
        b1.assign_sub(lr * grads[1])  # 参数b自更新

    # 每个epoch，打印loss信息并记录
    print("Epoch {}, loss: {}".format(epoch, loss_all/4))
    train_loss_results.append(loss_all / 4)
    loss_all = 0
```



#### 4、在该轮epoch中，计算当前参数前向传播的准确率并进行记录

```
    # total_correct为预测对的样本个数, total_number为测试的总样本数，将这两个变量都初始化为0
    total_correct, total_number = 0, 0
    for x_test, y_test in test_db:
        # 使用更新后的参数进行预测
        y = tf.matmul(x_test, w1) + b1
        y = tf.nn.softmax(y)
        pred = tf.argmax(y, axis=1)  # 返回y中最大值的索引，即预测的分类
        # 将pred转换为y_test的数据类型
        pred = tf.cast(pred, dtype=y_test.dtype)
        # 若分类正确，则correct=1，否则为0，将bool型的结果转换为int型
        correct = tf.cast(tf.equal(pred, y_test), dtype=tf.int32)
        # 将每个batch的correct数加起来
        correct = tf.reduce_sum(correct)
        # 将所有batch中的correct数加起来
        total_correct += int(correct)
        # total_number为测试的总样本数，也就是x_test的行数，shape[0]返回变量的行数
        total_number += x_test.shape[0]
    # 总的准确率等于total_correct/total_number
    acc = total_correct / total_number
    test_acc.append(acc)
    print("Test_acc:", acc)
    print("--------------------------")
```



### 三、绘制相应训练曲线

```
# 绘制 loss 曲线
plt.title('Loss Function Curve')  
plt.xlabel('Epoch') 
plt.ylabel('Loss') 
plt.plot(train_loss_results, label="$Loss$") 
plt.legend() 
plt.show() 

# 绘制 Accuracy 曲线
plt.title('Acc Curve') 
plt.xlabel('Epoch') 
plt.ylabel('Acc') 
plt.plot(test_acc, label="$Accuracy$") 
plt.legend()
plt.show()
```

