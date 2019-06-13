---
title: 数据挖掘复习提纲
date: 2019-06-10 19:37:10
mathjax: true
categories:
- ffuck
tags:
- data-mining
---

[课本](http://www.mmds.org): Leskovec, J., Rajaraman, A., & Ullman, J. D. (2014). *Mining of Massive Datasets* (2nd ed.). Cambridge, England: Cambridge University Press.

- - -

#第7章 聚类

##两种聚类方法

**层次(hierarchical)** 或 **凝聚式(agglomerative)** 算法。一开始将每个点都看成一个 **簇(cluster)** 。簇与簇按照 **接近度(closeness)** 来组合。当进一步的组合会导致非期望结果时，组合结束（例如，达到给定的簇的数目，或者簇内点分散的区域过大）。

**点分配(point assignment)** 过程。按照某个顺序依次考虑每个点，并将它分到最适合的簇中。

## 层次聚类

####欧氏空间下的层次聚类

````
WHILE it is not time to stop DO
    pick the best two clusters to merge;
    combine those two clusters into one cluster;
END;
````

#### 效率更高的算法实现方式

1. 第一步，计算所有点对的距离，将其放到一个优先队列中
2. 合并$C$和$D$时，删除队列中包含其中一个簇的所有元素。由于最多删除$2n$次，删除的时间开销为$O(n\log n)$。
3. 计算新簇和所有剩余簇的所有距离。由于最多插入$n$次，计算过程的时间开销同样是$O(n\log n)$。

由于最后两步最多执行n次，故总时间开销为$O(n^2\log n)$。

#### 非欧空间下的层次聚类

处于非欧空间时，我们必须要使用 Jaccard 距离等进行距离计算。

有几种常用的选择中心点的算法，它们分别使得选出该点到簇中其他所有点之距离的如下数值最小：

1. 求和
1. 最大值
1. 平方和

## $k$-means 算法

````
Initially choose k points that are likely to be in different clusters;
Make these points the centroids of their clusters;
FOR each remaining point p DO
    Find the centroid to which p is closest;
    Add p to the cluster of that centroid;
    Adjust the centroid of that cluster to account for p;
END;
````

####簇初始化

簇初始化有两种做法。
1. 选择彼此距离尽可能远的点

    ````
    Pick the first point at random;
    WHILE there are fewer than k points DO
        Add the point whose minimum distance
            from the selected points
            is as large as possible;
    END;
    ````
    
1. 对某个样本数据先进行聚类，因此输出$k$个簇。在每个簇中选择一个点，例如离簇质心最近的那个点。

#第12章 大规模机器学习

##机器学习模型

应用机器学习算法的数据称为训练集。训练集由一系列$(x,y)$对组成，每个$(x,y)$称为一个**训练样例(training example)**，其中：

- $x$是一个**特征向量(feature vector)**
- $y$是一个**标签(label)**

ML过程的目标是寻找一个函数$y=f(x)$来预测对于每个$x$的最佳$y$值。$y$的常见重要类型：

- $y$是实数
- $y$是布尔值，或$+1$和$-1$ **$\Rightarrow$ 二类分类(binary classification)** 
- $y$是有穷集合中的元素 **$\Rightarrow$ 多类分类(multiclass classification)** 

#### 主要的机器学习算法类别：

- 决策树(decision tree)，类似于搜索树
- 感知机(preception)，对向量中各分量加权求和，若和大于给定阈值$\theta$则输出$+1$。适用于二类分类问题
- 神经网络(neural net)，感知机组成的网络。
- 基于实例的学习(instance-based learning)，使用整个训练集作为$f$。

## 感知机

为训练感知机，我们来考察一个训练集并试图寻找一个权重向量$\vec{w}$和阈值$\theta$。一开始假定阈值为0，在此基础上针对未知阈值应用以下算法，使其收敛到一个将训练集中所有正例和负例分开的超平面：

1.  将权重向量$\vec{w}$的所有元素初始化为0；
2.  选择一个很小的正实数$\eta$作为**学习率参数(learning parameter)**。$\eta$的选择会影响感知机的收敛。
3.  依次考虑每个训练样例$t=(\vec{x},y)$:
    - 令$y'=\vec{w}\cdot\vec{x}$;
    - 如果$y$和$y'$的符号相同，则什么都不做，$t$已正确分类；
    - 如果$y$和$y'$的符号不同或$y'=0$，那么将$\vec{w}$替换成$\vec w+\eta y\vec x$。也就是说，沿$\vec x$方向微调$\vec w$。

#####*算法终止的常见方法：

1. 固定次数时终止
2. 分错类的数据点的数目不再变化时终止
3. 抽样，在样本上执行2
4. 降低训练率，例如在第$t$次迭代中将训练率$\eta$变为$\eta_0/(1+ct)$，其中c是很小的常数。

#### Winnow 算法

Winnow假定所有的特征向量都由0和1组成，类别用$+1$和$-1$表示。Winnow算法的权重向量都是正值。

Winnow算法的基本思想是，只降低或增加分错类的数据点中与正确类之符号相同或相反的分量的权重。

例如，以2和1/2作为因子分别代表提高权重和降低权重的情况：

1. 如果$\vec w \cdot \vec x > \theta$且$y=+1$，或者$\vec w \cdot \vec x < \theta$且$y=-1$，那么该样本被正确分类，此时对$\vec w$不做任何更改。
2. 如果$\vec w \cdot \vec x \leq \theta$但$y=+1$，那么$\vec x$中为1的分量所对应的$\vec w$的权重太低。此时，对这些权重进行加倍操作。也就是说，如果$x_i=1$，那么令$w_i:=2w_i$。
3. 如果$\vec w \cdot \vec x \geq \theta$但$y=+1$，那么$\vec x$中为1的分量所对应的$\vec w$的权重太高。此时，对这些权重进行减半操作。也就是说，如果$x_i=1$，那么令$w_i:=w_i/2$。
4. 类比2和3，处理$y=-1$的情况。

#####允许阈值变化的情况：

- 将权重向量$\vec w$定义为 $$\vec w'=[w_1,w_2,\cdots,w_d,\theta]$$
- 将特征向量$\vec x$ 定义为 $$\vec x'=[x_1,x_2,\cdots,x_d,-1]$$

对于新的训练样本和权重向量，可以认为阈值为0并运行基本的感知机算法。

或者可以认为是 Winnow 算法。只不过在其它权重需要翻倍的时候，阈值对应的权重除以2；其他权重需要除以2的时候，阈值对应的权重要翻倍。

## *支持向量机

**支持向量机(Support-Vector Machine, SVM)** 的目标是选择一个超平面$\vec w\cdot\vec x+b=0$，使得该超平面到训练集中任意点距离$\gamma$的最小值最大。

换句话说，就是给定训练集$(\vec{x_1},y_1),(\vec{x_2},y_2),\cdots,(\vec{x_n},y_n)$，对于所有$i=1,2,\cdots,n$，在若狭约束条件下通过变化$\vec w$和$b$最小化$||\vec w||$: $$y_i(\vec w \cdot \vec x+b)\geq1$$

由于$\gamma=1/||\vec w||$，最小化$||\vec w||$就是最大化$\gamma$。