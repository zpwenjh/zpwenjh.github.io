---
layout:     post
title:      LSTM(RNN,GRU)原理
subtitle:   全面解析LSTM(RNN,GRU)及其前向传播计算过程
date:       2021-02-04
author:     wenjh
header-img: img/post-bg-deeplearning-lstm.jpg
catalog:    true
tags:
    - 深度学习
---

# LSTM简介

**LSTM**（Long Short-Term Memory）长短期记忆网络，RNN的升级款，主要解决RNN中长时序列中的梯度消失问题。在长时间序列中，原始的RNN由于梯度的乘性问题，前面的序列的影响近乎为0，LSTM将其修正为加性问题。

# LSTM原理

先上原理图：

![image-20210302114321174](https://zpwenjh.github.io/img-post/image-20210302114321174.png)

**在LSTM中，第一阶段是遗忘门，遗忘层决定哪些信息需要从细胞状态中被遗忘，下一阶段是输入门，输入门确定哪些新信息能够被存放到细胞状态中，最后一个阶段是输出门，输出门确定输出什么值。**下面我们分析这三个门以及中间计算过程。

## 遗忘门

![20180705154117297](https://zpwenjh.github.io/img-post/20180705154117297.png)

遗忘门是将上一时间点输出$h_{t-1}$和当前时间点的数据$x_t$作为输入，通过$sigmoid$激活函数，得到$f_t$的过程。由于$sigmoid$函数的输出在$[0, 1]$区间，即$f_t$在$[0, 1]$区间，因此$f_t$可以表示上一层细胞状态被遗忘的概率，$1$是“完全保留“，$0$是”完全舍弃“。

### 计算公式及解释

遗忘门计算公式如下：

$$f_t = \sigma(W_f\cdot[h_{t-1}, x_t] + b_f)$$

**公式解释：**首先将$h_{t-1}$和$x_t$拼接为一个长的输入向量；然后对输入向量进行全连接计算，权重矩阵为$W_f$；在结果中加入偏置$b_f$得到隐层向量；最后对隐层向量做$sigmoid$激活，得到遗忘概率$f_t$。

在实际编码时，还可以按如下公式描述的进行编码，即不拼接$h_{t-1}$和$x_t$，而是拆开$W_f$。

$$f_t=\sigma(W_{if}\cdot x_t + W_{hf}\cdot h_{t-1}+b_f)$$

## 输入门

![20180705154140100](https://zpwenjh.github.io/img-post/20180705154140100.png)

输入门包含一个和遗忘门相同的结构，输出为$i_t$，表示当前时间点有多+少输入信息被保留，另一部分是对输入信息的处理，输出为$\tilde{C_t}$。

### 计算公式及解释

输入门计算公式有两个，分别如下：

$$i_t=\sigma(W_i\cdot[h_{t-1},x_t]+b_i)$$

$$\tilde{C_t}=\tanh(W_c\cdot[h_{t-1},x_t]+b_c)$$

**公式解释：**该公式格式和遗忘门相同，计算过程也是相同的，解释参考遗忘门。

## 细胞状态更新

计算了输入门和输出门之后，就可以对当前时刻细胞状态进行更新。

![20180705154157781](https://zpwenjh.github.io/img-post/20180705154157781.png)

上图中的`X`和`+`都是`pointwise`操作，逐元素操作。先计算$f_t$和前一时刻细胞状态$C_{t-1}$的外积，遗忘掉部分前一时刻的细胞状态；然后计算$i_t$和$\tilde{C_t}$的外积，保留部分本时刻的输入；最后将两部分向量做加法得到本时刻的细胞状态$C_t$。这一部分计算公式如下：

$$C_t=f_t*C_{t-1}+i_t*\tilde{C_t}$$

## 输出门

![20180705154210768](https://zpwenjh.github.io/img-post/20180705154210768.png)

输出门决定当前时刻细胞状态被过滤程度。首先是和输入门相同的结构，计算得到输出被过滤概率$o_t$，然后将细胞状态$C_t$使用`tanh`函数激活；最后使用$o_t$过滤激活结果得到当前时刻输出$h_t$。其计算公式如下：

$$o_t=\sigma(W_o\cdot[h_{t-1},x_t]+b_o)$$

$$h_t=o_t*\tanh(C_t)$$

---

**LSTM的细胞状态$C_t$变动缓慢，表示长期记忆；$h_t$变动较快，表示短期记忆。另外，每一层的$W_f,b_f$，$W_i,b_i$，$W_c,b_c$以及$W_o,b_o$都是层内共享的**。

# LSTM优缺点

* 优点：有效解决了RNN梯度消失问题。对于长时序列能较好拟合。
* 缺点：LSTM虽然部分解决了rnn梯度消失问题，但是信息在过远的距离传播中损失很厉害；另外，其无法很好的并行（工业上影响很大）。

# 双向LSTM

双向LSTM，由两个普通LSTM组成，能够同时利用过去和未来时刻的信息，推导当前时刻的信息。下图是其结构。

![20180713200802779](https://zpwenjh.github.io/img-post/20180713200802779.png)

# # GRU

GRU是LSTM最流行的一个变体，其结构如下：

![5480235-68c6da22ebd29964](https://zpwenjh.github.io/img-post/5480235-68c6da22ebd29964.png)

GRU取消了LSTM中的cell state，只使用了hidden state，并且使用update gate更新门来替换LSTM中的输入们和遗忘门，取消了LSTM中的输出门，新增了reset gate重置门，图中的$z_t$和$r_t$分别表示更新门和重置门。更新门用于控制前一时刻的状态信息被带入到当前状态中的程度，更新门的值越大说明前一时刻的状态信息带入越多。重置门控制前一状态有多少信息被写入到当前的候选集 $\tilde{h_t}$上，重置门越小，前一状态的信息被写入的越少。这样做的好处是在达到LSTM相近的效果下，GRU参数更少，训练的计算开销更小，训练速度更快。

# 后记

标准RNN结构如下：

![quesbase64155693985278218659](https://zpwenjh.github.io/img-post/quesbase64155693985278218659.png)

该结构和LSTM对比起来非常好理解。但是实际使用场景中，有根据问题域不同，也有不同的使用结构。下图是一些RNN的实际使用场景下的结构。

![RNN-muli-oneo](https://zpwenjh.github.io/img-post/RNN-muli-oneo.jpg)

![2021-03-02-RNN-onei-mulo](https://zpwenjh.github.io/img-post/2021-03-02-RNN-onei-mulo.jpg)

将上面两图结合就是Encoder-Decoder模型，也可以称之为Seq2Seq模型。

![2021-03-02-RNN-seq2seq](https://zpwenjh.github.io/img-post/2021-03-02-RNN-seq2seq.jpg)

LSTM和RNN一样，根据使用场景不同，LSTM可能有单输入多输出，多输入单输出，多输入多输出等输入输出结构。

