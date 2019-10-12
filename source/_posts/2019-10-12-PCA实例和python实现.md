---
title: PCA实例和python实现
date: 2019-10-12 21:04:46
tags: [模式识别,数据降维]
categories: 模式识别
---

本文给出一个PCA的具体实例和源码：

## PCA算法

算法步骤：

设有m条n维数据。

1）将原始数据按列组成n行m列矩阵X

2）将X的每一行（代表一个属性字段）进行零均值化，即减去这一行的均值

3）求出协方差矩阵C=1/m*XXT

4）求出协方差矩阵的特征值及对应的特征向量

5）将特征向量按对应特征值大小从上到下按行排列成矩阵，取前k行组成矩阵P

6）Y=PX即为降维到k维后的数据



## python实现

```python
import numpy as np
import math
from matplotlib import pyplot as plt

def setK(featureValue, rate):
    eigValInd = np.argsort(-featureValue) #返回特征值由大到小排序的下标值
    for i in range(1, featureValue.size+1):
        topK = eigValInd[i-1]
        Val = featureValue[topK]
        a = Val.sum()
        b = featureValue.sum()
        if a/b >= rate:
            break
    return i

#dataMat 为原始的输入样本矩阵，每一行为一个样本，k为保留的主成分个数,rate是保留的主成分占原有的百分比
def PCA(dataMat,k=0, rate=0.5):
    # 按列求均值,
    average = np.mean(dataMat, axis=0)
    print("means\n",average)
    m, n = np.shape(dataMat)
    # 减去均值
    meanRemoved = dataMat - np.tile(average, (m,1))
    print("meanRemoved\n", meanRemoved)
    # 标准差归一化
    normData = meanRemoved / np.std(dataMat)
    print("normal_data\n",normData)
    # 求协方差矩阵
    covMat = np.cov(meanRemoved.T)
    # 求协方差矩阵的特征值和特征向量
    featureValue, featureVec = np.linalg.eig(covMat)
    print("featureValue\n",featureValue)
    print("featureVec\n", featureVec)
    # 返回特征值由大到小排序的下标
    featureValInd = np.argsort(-featureValue)
    #按照特征值大小选择前K个数据
    if k==0:
        princpleNum = setK(featureValue, rate)
    else:
        princpleNum = k
    # [:princpleNum]表示前princpleNum行，因此之前需要转置处理（选择前princpleNum个大的特征值）
    selectVec = np.matrix(featureVec.T[:princpleNum])
    pcaData = meanRemoved * selectVec.T
    return pcaData

#从文件中读取样本矩阵
filePath = "./mat.txt"
dataMat = np.loadtxt(filePath,delimiter=' ')
dataMat = np.mat(dataMat)
pcaData = PCA(dataMat,1)
print("pcaData\n",pcaData)
```



## 测试数据

输入样本矩阵：

1 1
1 3
2 3
4 4
2 4

输出：

计算每一维的均值
 [ 2.  3.]

每一个样本减去对应维度均值
 [-1. -2.]
 [-1.  0.]
 [ 0.  0.]
 [ 2.  1.]
 [ 0.  1.]

计算协方差的特征值
 [ 2.5  0.5]

计算协方差的特征向量
 [ 0.70710678  -0.70710678]
 [ 0.70710678   0.70710678]

因为只保留一个维度，所以最后经过PCA处理的数据结果为
 [-2.12132034]
 [-0.70710678]
 [ 0.        ]
 [ 2.12132034]
 [ 0.70710678]

参考资料： [PCA的数学原理](http://blog.codinglabs.org/articles/pca-tutorial.html) 

