---
layout:     post
title:      前向与反向传播
subtitle:   深度学习前向与反向传播推导
date:       2021-04-29
author:     wenjh
header-img: img/post-bg-os-metro.jpg
catalog:    false
tags:
    - 深度学习
---

本文简单地以下图所示的三层神经网络为例（没有偏置节点），介绍神经网络的前向和反向传播过程：

![image-20210430082958148](https://zpwenjh.github.io/img-post/image-20210430082958148.png)

图中参数含义如下：

$X_1,X_2$：网络输入参数。

$W_{X_11}, W_{X_12}, W_{X_13}, W_{X_21}, W_{X_22}, W_{X_23}$：输入层权重参数。

$H_1, H_2, H_3$：隐含层对输入参数的线性响应。

$Y_1, Y_2, Y_3$：经过非线性激活的隐层输出。

$W_{Y_11}, W_{Y_12}, W_{Y_21}, W_{Y_22}, W_{Y_31}, W_{Y_32}$：输出层权重参数。

$R_1, R_2$：输出层对隐层的线性响应。

$O_1, O_2$：经过非线性激活的网络输出。

# 前向传播

前向传播计算相对简单，按照如下公式逐步计算即可：

![image-20210506120159590](https://zpwenjh.github.io/img-post/image-20210506120159590.png)


注：上式中的$sigmoid$为激活函数，本文假设全部使用$sigmoid$作为激活函数。

为了更加直观，同时方便计算，我们用矩阵表示上述过程：

![image-20210506144826982](https://zpwenjh.github.io/img-post/image-20210506144826982.png)

# 反向传播

## 误差及其反向传播

* 误差的衡量：本文选取$MSE$(均方误差)来衡量网络误差：

  ![image-20210506145157147](https://zpwenjh.github.io/img-post/image-20210506145157147.png)

* 误差的反向传播：误差的反向传播遵循按权重分配原则，因此，$E_{O_1},E_{O_2}$误差全部分别由$R_1, R_2$产生，即$E_{O_1}$也是神经元$F$的误差。各神经元的误差计算如下：

  ![image-20210506145242857](https://zpwenjh.github.io/img-post/image-20210506145242857.png)

  $E_{X_1}$和$E_{X_2}$的计算和上述方法相同，这里不再赘述。
  上述计算过程用矩阵表示为：

![image-20210506145416203](https://zpwenjh.github.io/img-post/image-20210506145416203.png)

  上述计算较为繁琐，可以在不破坏权重比例的情况下，将其简化为如下形式：

![image-20210506145442386](https://zpwenjh.github.io/img-post/image-20210506145442386.png)

  可以发现，上述权重矩阵，就是前向传播时权重矩阵的转置，即反向误差传播形式如下：

$$
E = W^T\times{E}
$$

## 反向权重更新

* 链式求导

  由前向传播的计算式和上面的误差计算式$(1), (2), (3)$可得：
  
  ![image-20210506150847790](https://zpwenjh.github.io/img-post/image-20210506150847790.png)

  依次类推，可以计算$E_O$对所有权重$W$的偏导。

* 权重更新

  从链式求导过程可以看到，求导过程中，大量计算是重复的，因此，在更新$W_{Y_11}$和$W_{X_11}$的过程中，可以将重复计算进行折叠，只计算一次，简化后如下：
  
  ![image-20210506151038412](https://zpwenjh.github.io/img-post/image-20210506151038412.png)

  其中$\sigma{'}$是当前神经元的输出对输入的偏导(梯度)。按照上述过程，可以逐层计算出误差对各个权重$W$的偏导。

  权重更新首先需要选择一个更新算法，这里简单使用随机梯度下降法(SGD)。将上述偏导代入SGD得到：

![image-20210506151207111](https://zpwenjh.github.io/img-post/image-20210506151207111.png)

# 参考
[\[1\] 张小磊啊.《一文搞懂反向传播算法》\[DB/OL\].](https://www.jianshu.com/p/964345dddb70)

[\[2\] aift.《“反向传播算法”过程及公式推导（超直观好懂的Backpropagation）》\[DB/OL\]](https://blog.csdn.net/ft_sunshine/article/details/90221691)