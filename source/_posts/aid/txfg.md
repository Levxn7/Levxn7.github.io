---
title: 图像分割的几种模型
date: 2024-02-02 10:31:18
categories: 
- 深度学习
tags:
- 图像分割
---

#### 图像分割的分类：
![eb531a75e29bfed9d71079eec9cfb2c2.png](eb531a75e29bfed9d71079eec9cfb2c2.png)

#### 图像分割的难点
1）数据问题：分割不像检测等任务，只需要标注边框就可以使用，分割需要精确到
像素级标注，包括每一个目标的轮廓等信息；
2）计算资源问题：要想得到较高的精度就需要使用更深的网络、进行更精确的计
算，对计算资源要求较高。目前业界有一些轻量级网络，但总体精度较低；
3）精细分割：目前很多算法对于道路、建筑物等类别分割精度很高，能达到98%，
而对于细小的类别，由于其轮廓太小，而无法精确的定位轮廓；
4）上下文信息：分割中上下文信息很重要，否则会造成一个目标被分成多个部分，
或者不同类别目标分类成相同类别；

#### 图像分割基本原理
1. 整体实现思路
图像分割一般思路如下：
1）输入图像，利用深度卷积神经网络提取特征
2）对特征图进行上采样，输出每个像素的类别
3）利用损失函数，对模型进行优化，将每个像素的分类结果优化到最接近真实值
![d307294eb80b6711f840583c2b7c73fa.png](d307294eb80b6711f840583c2b7c73fa.png)

2. 评价指标
1）像素精度（pixel accuracy ）：每一类像素正确分类的个数/ 每一类像素的实际
个数；
2）平均像素精度（mean pixel accuracy ）：每一类像素的精度的平均值；
3）平均交并比（Mean Intersection over Union）：求出每一类的IOU取平均值

#### 常用模型
##### 1. FCN（2014）Fully Convolutional Networks
FCN(Fully Convolutional Networks)的基本概念和核心思想。FCN是首个使用神经网络实现语义分割任务的方法与传统的特征提取算法相比，FCN具有很多优势，如可以适应不同场景变化、无需大量人时间和精力等。视频中还介绍了FCN的全卷积网络结构、使用现有的分类网络进行调整以及跨层结构等关键点。最后，视频提供了官方代码实现的细老
图像分割需要对图像进行像素级分类，所以在输出层使用全连接模型并不合适。
FCN与CNN的区别在把于CNN最后的全连接层换成卷积层（所以称为“全卷积网
络”）。该网络可以分为两部分，第一部分，通过卷积运算提取图像中的特征，形成
特征图；第二部分，对特征图进行上采样，将特征图数据恢复为原来的大小，并对
每个像素产生一个分类标签，完成像素级分类。

去掉了全链接层，全部使用卷积网络，可以不再要求输入图片的大小
分为32s  16s  8s3中输出
![88a125da67fcc6b88729e56ad01040cd.png](88a125da67fcc6b88729e56ad01040cd.png)
![fe6b68d72aedf7d3c1e6d0e3d2dab7d4.png](fe6b68d72aedf7d3c1e6d0e3d2dab7d4.png)
![cfc88e392fee0a83c0552a29798c16cd.png](cfc88e392fee0a83c0552a29798c16cd.png)
![a2ac30edca9647a1db5e0ecaa76e3be2.png](a2ac30edca9647a1db5e0ecaa76e3be2.png)

![fee378ad378dc22885dafc223ef2ef77.png](fee378ad378dc22885dafc223ef2ef77.png)

##### 2. U-Net（2015）
生物医学分割是图像分割重要的应用领域。U-Net是2015年发表的用于生物医学图
像分割的模型，该模型简单、高效、容易理解、容易定制，能在相对较小的数据集
上实现学习。该模型在透射光显微镜图像（相衬度和DIC）上获得了2015年ISBI细胞
跟踪挑战赛的冠军。该图像分割速度较快，在512x512图像实现分割只需不到一秒
钟的时间。
U-Net基本实现图像分割基本原理与FCN一致，先对原图进行若干层卷积、池化，得
到特征图，再对特征图进行不断上采样，并产生每个像素的类别值。

1）网络结构
U-Net网络体系结构如下图所示，它由收缩路径（左侧）和扩展路径（右侧）组成，
共包含23个卷积层。
收缩路径遵循卷积网络的典型结构，它包括重复应用两个3x3卷积（未相加的卷
积），每个卷积后面都有一个ReLU和一个2x2最大合并操作，步长为2，用于下
采样。在每个下采样步骤中，特征通道的数量加倍。
扩展路径中的每一步都包括对特征映射进行上采样，然后进行2x2向上卷积
（up-convolution ），将特征通道数量减半，与收缩路径中相应裁剪的特征映
射进行串联，以及两个3x3卷积，每个卷积后面都有一个ReLU。在最后一层，
使用1x1卷积将每个64分量特征向量映射到所需数量的类
![4b62c535ae33035d523e1361d2ccaef7.png](4b62c535ae33035d523e1361d2ccaef7.png)
但是现在很多人使用unet模型会加padding，使得输出图像与输入图像尺寸相等


##### 3. Mask R-CNN（2017）
Mask R-CNN是一个小巧灵活的通用实例级分割框架，它不仅可对图像中的目标进行
检测，还可以对每一个目标给出一个高质量的分割结果。它在Faster R-CNN基础之
上进行扩展，并行地在bounding box recognition分支上添加一个用于预测目标掩
模（object mask）的新分支。该网络具有良好的扩展性，很容易扩展到其它任务中，比如估计人的姿势。Mask R-CNN结构简单、准确度高、容易理解，是图像实例
级分割的优秀模型

1）主要思想
（1）分割原理。Mask R-CNN是在Faster R-CNN基础之上进行了扩展。Faster R
CNN是一个优秀的目标检测模型，能较准确地检测图像中的目标物体（检测到实
例），其输出数据主要包含两组：一组是图像分类预测，一组是图像边框回归。
Mask R-CNN在此基础上增加了FCN来产生对应的像素分类信息（称为Mask），用
来描述检测出的目标物体的范围，所以Mask R-CNN可以理解为Faster R-CNN +
FCN。整体结构如下图所示
![3085fc1be2d57247362fb3249f0c909a.png](3085fc1be2d57247362fb3249f0c909a.png)
![de7af8ee8168caf2deccc5e47d66da5f.png](de7af8ee8168caf2deccc5e47d66da5f.png)


##### 4. deeplab

**空洞卷积dilated convolution**： 卷积数量不变，参数个数不变，但感受野变大
![f76094366cdf7fcea2f936f393c3eef0.png](f76094366cdf7fcea2f936f393c3eef0.png)
空洞卷积的优势：
图像分割任务中(其他场景也适用)需要较大感受野来更好完成任务
通过设置dilation rate参数来完成空洞卷积，并没有额外计算
可以按照参数扩大任意倍数的感受野，而且没有引入额外的参数
应用简单，就是卷积层中多设置一个参数就可以了

空洞卷积的优势,包括全局感受、可控参数和应用简单等作者解释了为什么在很多网络结构中都使用堆叠3x3的卷积核,而不是直接使用大的卷积核,通过算笔账,说明堆叠小的卷积核可以节省参数,引入更多的非线性函数,从而提高模型效果。视频还介绍了感受野的概念,以及为什么感受野大小在分割任务中很重要。

v2：
spp layer
![176f7e07358f2d61ef3a2383edb1cde6.png](176f7e07358f2d61ef3a2383edb1cde6.png)
![31416fc90b45bd6eb6608f9d75809426.png](31416fc90b45bd6eb6608f9d75809426.png)

aspp 加了空洞卷积的spp 把不同倍率的空洞卷积融合在一起
![9e5032731b3848d7677bd0087d06407b.png](9e5032731b3848d7677bd0087d06407b.png)


v3
padding=空洞数，就可以保证得到的特征图宽高相等
特征融合
![c4482417331e3eed26d87569cc30d29c.png](c4482417331e3eed26d87569cc30d29c.png)
