---
title: PCA算法
date: 2020-10-08 16:16:12
tags:
	- 经典算法
categories:
	- AI
	- 机器学习
fileName: pca-basic-use
---



```
from sklearn.neighbors import KNeighborsClassifier

knn = KNeighborsClassifier(n_neighbors=6)
knn.fit(x_train, y_train)
knn.score(x_test, y_test)
y_predict = knn.predict(x_predict)
```

## 算法概述

> PCA算法是一种使用广泛的数据降维算法，主要思想是将n维特征映射到k维上（k < n）。降维的本质也就是从原始坐标系映射到另一个维数更小的坐标系
>
> PCA的工作就是从原始的空间中*顺序地*找一组*相互正交*的坐标轴（这种正交特征也被称为主成分），新的坐标轴的选择与数据本身是密切相关的（原始数据中方差最大的方向）



## 二维数据可视化

### 原始二维数据



### 对数据样本进行中心化

进行中心化即**将样本的均值归为0**



求一个轴的方向

### 最大化方差

找到一组权重，使得样本空间中所有点映射到这个轴后方差最大



```
import numpy as np
import matplotlib.pyplot as plt


def demean(x):
    return x - np.mean(x, axis=0)


def f(w, x):
    return np.sum((x.dot(w)**2)) / len(x)


def df(w, x):
    return x.T.dot(x.dot(w)) * 2. / len(x)

def gradient_ascent(x, w):
    def direction(w):
        """ 将w转化为单位方向向量 """
        return w / np.linalg.norm(w)
    cur_iter = 0
    w = direction(w)
    while cur_iter < 1e4:
        gradient = df(w, x)
        last_w = w
        w = w + 0.001 * gradient
        w = direction(w)
        if abs(f(w, x) - f(last_w, x) < 1e-8):
            break
        cur_iter += 1
    return w

x = np.empty((50, 2))
x[:, 0] = np.random.uniform(0, 50, size=50)
x[:, 1] = 0.8 * x[:, 0] + 2 + np.random.normal(0, 5, size=50)

x_demean = demean(x)


initial_w = np.random.random(x.shape[1])
w = gradient_ascent(x_demean, initial_w)

plt.scatter(x_demean[:, 0], x_demean[:, 1])
plt.plot([-w[0]*50, w[0]*50], [-w[1]*50, w[1]*50], c='r')
plt.show()
```



## PCA的实现



## PCA的使用

