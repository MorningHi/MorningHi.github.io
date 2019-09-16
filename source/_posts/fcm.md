---
title: 模糊c均值(Fuzzy C-Means)聚类算法
categories:
  - 机器学习
tags:
  - 聚类
comments: false
mathjax: true
date: 2019-09-12 19:20:12
img:
---

#### 原理简介

&emsp;&emsp;模糊c均值聚类(Fuzzy C-Means)是引入了模糊理论的一种聚类算法，通过**隶属度**来表示样本属于某一类的概率，原因在于在很多情况下多个类别之间的界限并不是绝对的明确。显然，相比于k-means的硬聚类，模糊c均值聚类得到的聚类结果更灵活。

&emsp;&emsp;模糊c均值聚类通过最小化一下目标函数来得到聚类中心：

$$J_{m}=\sum_{i=1}^{N} \sum_{j=1}^{C} u_{i j}^{m}\left\|x_{i}-c_{j}\right\|^{2} \quad, \quad 1 \leq m<\infty \tag{1}$$

其中，$m>1$ 为模糊系数(fuzzy coefficient)，$N$ 为样本数，$C$ 为聚类中心数，$$c_j$$ 表示第 $j$ 个聚类中心，和样本特征维数相同，$$x_i$$ 表示第 $i$ 个样本，$$u_{ij}$$ 表示样本 $$x_i$$ 对聚类中心 $$c_j$$ 的隶属度(通俗的说就是 $$x_i$$ 属于 $$c_j$$ 的概率)，显然满足

$$\sum_{j=1}^{C} u_{i j}=1 \tag{2}$$

$||*||$ 可以是任意度量数据相似性(距离)的范数，最常见的就是欧几里得范数（又称欧氏范数，L2范数，欧氏距离）：

$$d=\|x\|_2=\sqrt{\sum_i {x_i^2}} \tag{3}$$

&emsp;&emsp;模糊c均值聚类通过更新 $u_{ij}$ 和 $c_j$ 来迭代地优化目标函数Eq. (1)：

$$u_{i j}=\frac{1}{\sum_{k=1}^{C}\left(\frac{\left\|x_{i}-c_{j}\right\|}{\left\|x_{i}-c_{k}\right\|}\right)^{\frac{2}{m-1}}} \tag{4}$$

$$c_{j}=\frac{\sum_{i=1}^{N} u_{i j}^{m} \cdot x_{i}}{\sum_{i=1}^{N} u_{i j}^{m}} \tag{5}$$

迭代的终止条件为 $$\max _{ij}\left\{\left|u_{ij}^{(t+1)}-u_{ij}^{(t)}\right|\right\}<\varepsilon$$ ，其中 $t$ 是迭代步数，$$\varepsilon$$ 是一个很小的常数表示误差阈值。也就是说迭代地更新 $$u_{ij}$$ 和 $$c_j$$ 直到前后两次隶属度最大变化值不超过误差阈值。这个过程最终**收敛于 $J_m$ 的局部极小值点或鞍点**。

#### 算法步骤

可以将模糊c均值聚类的过程归纳为以下几步：

1. 初始化隶属度矩阵 $U^{(0)}$，若有 $N$个样本，指定类别数为 $C$，则隶属度矩阵应当是 $N*C$ 的矩阵；
2. 根据式(5)更新聚类中心 $c_j, j=1,...,C$；
3. 根据式(4)更新 $U^{(t)}, U^{(t+1)}$；
4. 若满足终止条件 $$ \max _{ij}\left\{\left|u_{ij}^{(t+1)}-u_{ij}^{(t)}\right|\right\}<\varepsilon$$ 则停止迭代，否则返回步骤2。

#### 程序实现

下面代码以[Iris](http://archive.ics.uci.edu/ml/datasets/Iris)数据集聚类为例实现了fuzzy c-means。

```python
#!/usr/bin/python3
# -*- coding: utf-8 -*-

'''
@Date    : 2019/9/11
@Author  : Rezero
'''

import numpy as np
import pandas as pd

def loadData(datapath):
    data = pd.read_csv(datapath, sep=',', header=None)
    data = data.sample(frac=1.0)   # 打乱数据顺序
    dataX = data.iloc[:, :-1].values # 特征
    labels = data.iloc[:, -1].values # 标签
    # 将标签类别用 0, 1, 2表示
    labels[np.where(labels == "Iris-setosa")] = 0
    labels[np.where(labels == "Iris-versicolor")] = 1
    labels[np.where(labels == "Iris-virginica")] = 2

    return dataX, labels


def initialize_U(samples, classes):
    U = np.random.rand(samples, classes)  # 先生成随机矩阵
    sumU = 1 / np.sum(U, axis=1)   # 求每行的和
    U = np.multiply(U.T, sumU)   # 使隶属度矩阵每一行和为1

    return U.T

# 计算样本和簇中心的距离，这里使用欧氏距离
def distance(X, centroid):
    return np.sqrt(np.sum((X-centroid)**2, axis=1))


def computeU(X, centroids, m=2):
    sampleNumber = X.shape[0]  # 样本数
    classes = len(centroids)
    U = np.zeros((sampleNumber, classes))
    # 更新隶属度矩阵
    for i in range(classes):
        for k in range(classes):
            U[:, i] += (distance(X, centroids[i]) / distance(X, centroids[k])) ** (2 / (m - 1))
    U = 1 / U

    return U


def ajustCentroid(centroids, U, labels):
    newCentroids = [[], [], []]
    curr = np.argmax(U, axis=1)  # 当前中心顺序得到的标签
    for i in range(len(centroids)):
        index = np.where(curr == i)   # 建立中心和类别的映射
        trueLabel = list(labels[index])  # 获取labels[index]出现次数最多的元素，就是真实类别
        trueLabel = max(set(trueLabel), key=trueLabel.count)
        newCentroids[trueLabel] = centroids[i]
    return newCentroids

def cluster(data, labels, m, classes, EPS):
    """
    :param data: 数据集
    :param m: 模糊系数(fuzziness coefficient)
    :param classes: 类别数
    :return: 聚类中心
    """
    sampleNumber = data.shape[0]  # 样本数
    cNumber = data.shape[1]       # 特征数
    U = initialize_U(sampleNumber, classes)   # 初始化隶属度矩阵
    U_old = np.zeros((sampleNumber, classes))

    while True:
        centroids = []
        # 更新簇中心
        for i in range(classes):
            centroid = np.dot(U[:, i]**m, data) / (np.sum(U[:, i]**m))
            centroids.append(centroid)

        U_old = U.copy()
        U = computeU(data, centroids, m)  # 计算新的隶属度矩阵

        if np.max(np.abs(U - U_old)) < EPS:
            # 这里的类别和数据标签并不是一一对应的, 调整使得第i个中心表示第i类
            centroids = ajustCentroid(centroids, U, labels)
            return centroids, U


# 预测所属的类别
def predict(X, centroids):
    labels = np.zeros(X.shape[0])
    U = computeU(X, centroids)  # 计算隶属度矩阵
    labels = np.argmax(U, axis=1)  # 找到隶属度矩阵中每行的最大值，即该样本最大可能所属类别

    return labels


def main():
    datapath = "iris.data"
    dataX, labels = loadData(datapath)  # 读取数据

    # 划分训练集和测试集
    ratio = 0.6  # 训练集的比例
    trainLength = int(dataX.shape[0] * ratio)  # 训练集长度
    trainX = dataX[:trainLength, :]
    trainLabels = labels[:trainLength]
    testX = dataX[trainLength:, :]
    testLabels = labels[trainLength:]

    EPS = 1e-6   # 停止误差条件
    m = 2        # 模糊因子
    classes = 3  # 类别数
    # 得到各类别的中心
    centroids, U = cluster(trainX, trainLabels, m, classes, EPS)

    trainLabels_prediction = predict(trainX, centroids)
    testLabels_prediction = predict(testX, centroids)


    train_error = len(np.where((trainLabels_prediction - trainLabels) != 0)[0]) / trainLength
    test_error = len(np.where((testLabels_prediction - testLabels) != 0)[0]) / (len(dataX) -  trainLength)
    print("Clustering on traintset is %.2f%%" % (train_error*100))
    print("Clustering on testset is %.2f%%" % (test_error*100))



if __name__ == "__main__":
    main()
```

#### 参考资料

> [**A Tutorial on Clustering Algorithms**——Fuzzy C-Means Clustering](http://home.deib.polimi.it/matteucc/Clustering/tutorial_html/cmeans.html)
> [Fuzzy C-Means（模糊C均值聚类）算法原理详解与python实现](https://blog.csdn.net/lyxleft/article/details/88964494)