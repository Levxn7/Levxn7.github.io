---
title: 批量归一化 Batch Normalization
date: 2023-10-11 10:31:18
categories: 
- 机器学习
---



神经网络中的批量归一化方法,通过对一批样本进行归一化处理,使得每一层网络的输入分布相似,从而避免了分布差异过大导致网络无法收敛的问题。


为了让网络可以正常训练，Batch Normalization 批量归一化方法可以加速神经网络的收敛。使训练过程中对学习率和参数初始化更加鲁棒

但它也有一些缺陷,如对循环神经网络等序列形式数据性能较差,以及在分布式训练中额外的数据同步会影响并行效率等。

神经网络中的批量归一化方法，通过对样本进行归一化处理，使网络能够正常训练。

需要注意批量归一化方法中额外引入的两个参数，需要反向传播更新

仅在Batch中包含样本数量较多时有效。对循环网络(RNN)或序列数据(Sequence)性能较差分布式运算时影响效率。

BN算法仅在BH中包含样本，对于循环神经网络等数据性能较差

后续提出了类似于layer normalization等改进方法

![93968aee8b1f72101a7f5ff44788f110.png](93968aee8b1f72101a7f5ff44788f110.png)
