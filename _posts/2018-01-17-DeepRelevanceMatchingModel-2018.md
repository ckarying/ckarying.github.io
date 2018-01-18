---
layout:     post                    # 使用的布局（不需要改）
title:      A Deep Relevance Matching Model for Ad-hoc Retrieval # 标题 
subtitle:    #副标题
date:       2018-01-17              # 时间
author:     Sylar              # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - Machine Learning
    - IR
    - Deep Learing
---
# Paper
A Deep Relevance Matching Model for Ad-hoc Retrieval
http://www.bigdatalab.ac.cn/~gjf/papers/2016/CIKM2016a_guo.pdf

# Abstract
1.Matching model要解决的问题，是两个文本的匹配问题，对于deep matching model的做法，主要是在上面直接应用nlp的技术，例如Q&A。

2.文章认为，matching model需要的是relevance，而nlp要解决的是semantic，这两者之间是不同的。

# Introduction
1. 文章里面提出了一个观点，认为人工特征，需要消耗时间，并且是不完全、很特殊的。而dnn本身可以做representation，可以自动的从训练数据中学习到抽象结构和特征，按照不同的level，可以对特征进行不同层级的抽象。

2. 目前的deep matching model，按照结构，可以大致分为两类：
+ Representation-focused model，主要用于将两端的文本分别进行抽象表示，然后再对其进行matching，典型的就是DSSM
+ Interaction-focused model，这类算法，先在建立两个文本局部间的interaction（类似Miss match），然后再用dnn，学习到局部关联的信息中的pattern，例如DeepMatch

3. 文章提出了一个观点，认为目前的nlp任务关注两个文本间的sematic relation，但是在matching问题中，我们其实只关注是否相关。

4. 网络的3个key factors：
+ 使用直方图来描述query和term的局部特征
+ Term gating network 网络结构
+ 前向神经网络

5. 文章的创新点
+ 指出了relevance matching 和semantic matching的区别，而这些diff和网络结构相关
+ 提出了一个网络结构，可以很好的描述这3个区别
+ 对比了DRMM和现有的matching 算法

# Problem
1. Matching problem，可以用下式来表示，对于文本T1和T2，通过对文本进行表示，一般的做法是表示为向量，F是scoring function，用于metric两个向量的相似程度。从建模看，matching问题和很多的NLP问题是相似的，因此很多场景下，直接套用了NLP的做法。

2. Representation-focused model，主要工作在于对于文本的表示，因此是相对比较复杂的函数，F会选择比较简单的形式。DSSM中，前者是一个复杂的前向神经网络，而后者只是一个简单的cosine，这种模型一般就是金字塔结构。

3. Interaction-focused model，利用local interaction的基本特征作为dnn的输入，在这种结构中，两个函数的复杂程度会完全相反。



