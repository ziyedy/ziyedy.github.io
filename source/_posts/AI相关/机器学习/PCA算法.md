---
title: PCA算法
date: 2020-10-08 16:16:12
tags:
	- 机器学习
	- sklearn
categories:
	- AI相关
	- 机器学习
fileName: pca-basic-use
---

## 算法概述

> PCA算法是一种使用广泛的数据降维算法，主要思想是将n维特征映射到k维上（k < n）。降维的本质也就是*从原始坐标系映射到另一个维数更小的坐标系*
>
> PCA的工作就是从原始的空间中*顺序地*找一组*相互正交*的坐标轴（所以**主成分也可以理解为降维后新坐标系的坐标轴**，比如，第一主成分即为求出来的第一个坐标轴），新的坐标轴的选择与数据本身是密切相关的（原始数据中方差最大的方向）



## 二维数据可视化

### 原始二维数据

```
# 构建原始二维数据
x = np.empty((50, 2))
x[:, 0] = np.random.uniform(0, 50, size=50)
x[:, 1] = 0.8 * x[:, 0] + 2 + np.random.normal(0, 5, size=50)
```

<img src="http://cdn.ziyedy.top/PCA%E7%AE%97%E6%B3%95/%E5%88%9D%E5%A7%8B%E6%95%B0%E6%8D%AE.png" style="zoom:67%;" />



### 对数据样本进行中心化

进行中心化即**将样本的均值归为0**，在计算方差时可以将尾数约掉

```
def demean(x):
    return x - np.mean(x, axis=0)

x_demean = demean(x)
```

<img src="http://cdn.ziyedy.top/PCA%E7%AE%97%E6%B3%95/%E5%BD%920%E5%8C%96%E6%95%B0%E6%8D%AE.png" style="zoom:67%;" />



### 最大化方差

找到**一组权重**，使得样本空间中所有点映射到这个轴后方差最大（有点类似于“梯度上升”）

```
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


initial_w = np.random.random(x.shape[1])
w = gradient_ascent(x_demean, initial_w)

plt.scatter(x_demean[:, 0], x_demean[:, 1])
plt.plot([-w[0]*50, w[0]*50], [-w[1]*50, w[1]*50], c='r')
plt.show()
```

如图所示，该红线即为方差最大时的权重（可以理解为将这些数据点投影上去能够保留最多信息）

<img src="http://cdn.ziyedy.top/PCA%E7%AE%97%E6%B3%95/%E6%A0%87%E5%87%86%E6%AD%A3%E4%BA%A4%E5%9F%BA%E5%90%91%E9%87%8F.png" style="zoom:67%;" />



## PCA的实现

模仿`sklearn`机器学习库的API，实现PCA类

```
class PCA:

    def __init__(self, n_components):
        """ 初始化PCA """
        self.n_components = n_components
        self.components_ = None

    def fit(self, x):
        """ 获取数据集的前n个主成分 """
        def demean(x):
            return x - np.mean(x, axis=0)

        def f(w, x):
            return np.sum((x.dot(w) ** 2)) / len(x)

        def df(w, x):
            return x.T.dot(x.dot(w)) * 2. / len(x)

        def gradient_ascent(x, w):
            """ 梯度上升法获取第1主成分 """
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

        x_pca = demean(x)
        self.components_ = np.empty(shape=(self.n_components, x.shape[1]))
        for i in range(self.n_components):
            initial_w = np.random.random(x_pca.shape[1])
            # 在x_pca上求得第一主成分
            w = gradient_ascent(x_pca, initial_w)
            self.components_[i, :] = w
            # 将x_pca映射到求出来的主成分上，在此基础上再求第一主成分
            x_pca = x_pca - x_pca.dot(w).reshape(-1, 1) * w
        return self

    def transform(self, X):
        """ 将给定的X，映射到各个主成分分量z中 """
        return X.dot(self.components_.T)

    def detransform(self, X):
        """ 将给定的X，反向映射回原来的特征空间 """
        return X.dot(self.components_)
```



## PCA的使用

### 基本用法

`sklearn`库中的PCA的用法与之前实现的基本一致。

```
from sklearn.decomposition import PCA

pca = PCA(n_components=1)
pca.fit(x_data)
pca.components_  # 得到的主成分
x_reduction = pca.transform(x_data)
x_restore = pca.inverse_transform(x_reduction)
```

### PCA效果的查看

```
pca.explained_variance_ratio_
```

> 该方法返回一个表示降维后每个轴能够代表整个数据信息的比例，可以查看降维的效果以及数据损失的程度

```
# 获取PCA所有轴数据损失的程度
pca = PCA(n_components=x_train.shape[1])
pca.fit(x_train)
pca.explained_variance_ratio_
```



