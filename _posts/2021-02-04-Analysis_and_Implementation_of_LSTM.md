---
layout:     post
title:      LSTM原理
subtitle:   全面解析LSTM
date:       2021-01-19
author:     wenjh
header-img: img/post-bg-deeplearning-lstm.jpg
catalog:    false
tags:
    - 深度学习
---

# LSTM简介

**LSTM**（Long Short-Term Memory）长短期记忆网络，RNN的升级款，主要解决RNN中长时序列中的梯度消失问题。在长时间序列中，原始的RNN由于梯度的乘性问题，前面的序列的影响近乎为0，LSTM将其修正为加性问题。

# LSTM原理

先上原理图

![image-20210302114321174](https://zpwenjh.github.io/img-post/image-20210302114321174.png)

**在LSTM中，第一阶段是遗忘门，遗忘层决定哪些信息需要从细胞状态中被遗忘，下一阶段是输入门，输入门确定哪些新信息能够被存放到细胞状态中，最后一个阶段是输出门，输出门确定输出什么值。**下面我们分析这三个门。

## 遗忘门

