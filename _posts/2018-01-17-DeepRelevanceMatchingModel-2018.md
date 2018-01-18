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
## Paper
A Deep Relevance Matching Model for Ad-hoc Retrieval

http://www.bigdatalab.ac.cn/~gjf/papers/2016/CIKM2016a_guo.pdf

## Abstract
1.Matching model要解决的问题，是两个文本的匹配问题，对于deep matching model的做法，主要是在上面直接应用nlp的技术，例如Q&A。

2.文章认为，matching model需要的是relevance，而nlp要解决的是semantic，这两者之间是不同的。


## Introduction
1.文章里面提出了一个观点，认为人工特征，需要消耗时间，并且是不完全、很特殊的。而dnn本身可以做representation，可以自动的从训练数据中学习到抽象结构和特征，按照不同的level，可以对特征进行不同层级的抽象。

2.目前的deep matching model，按照结构，可以大致分为两类：
+ Representation-focused model，主要用于将两端的文本分别进行抽象表示，然后再对其进行matching，典型的就是DSSM
+ Interaction-focused model，这类算法，先在建立两个文本局部间的interaction（类似Miss match），然后再用dnn，学习到局部关联的信息中的pattern，例如DeepMatch

3.文章提出了一个观点，认为目前的nlp任务关注两个文本间的sematic relation，但是在matching问题中，我们其实只关注是否相关。

4.网络的3个key factors：
+ 使用直方图来描述query和term的局部特征
+ Term gating network 网络结构
+ 前向神经网络

5.文章的创新点
+ 指出了relevance matching 和semantic matching的区别，而这些diff和网络结构相关
+ 提出了一个网络结构，可以很好的描述这3个区别
+ 对比了DRMM和现有的matching 算法

## Problem
1.Matching problem，可以用下式来表示，对于文本T1和T2，通过对文本进行表示，一般的做法是表示为向量，F是scoring function，用于metric两个向量的相似程度。从建模看，matching问题和很多的NLP问题是相似的，因此很多场景下，直接套用了NLP的做法。

2.Representation-focused model，主要工作在于对于文本的表示，因此是相对比较复杂的函数，F会选择比较简单的形式。DSSM中，前者是一个复杂的前向神经网络，而后者只是一个简单的cosine，这种模型一般就是金字塔结构。

3.Interaction-focused model，利用local interaction的基本特征作为dnn的输入，在这种结构中，两个函数的复杂程度会完全相反。

## Semantic matching VS relevance matching
1.Semantic matching任务中，两个文本同质，并且包含很多口语化的词，key factors：
+ Similarity matching signals： 相比较精确匹配，需要可以对比两个文本的word、phase、sentence的semantic relation。
+ Composition meanings：nlp任务中，文本会比较口语化，利用grammatical结构对文本进行处理，比直接将文本变成COW或者sequence会好很多。
+ Global matching requirements：semantic matching任务中，文本程度一般会有限制，整个问题的topic和语义比较集中，主要是对文本整体的topic进行matching。

2.Relevance matching 任务中，对于文本的semantic考虑的会比较少，更关注两者是否相关，并且一端是比较短的query，另外一端是长度差异会比较大的doc，
+ Exact matching signals：在IR中，基于query的exact matching of terms（类似query rewrite和bmm）还是最主要的触发signal。有文章指出了semantic term matching constraints，doc和原始query有term重叠这一特征，对相关性分数的贡献，不少于semantic terms重叠多次。
+ Query term importance：query一般较短，并且没有通过没什么语法结构，主要是一些简单的布尔结构，区分出query中term的重要性，就很重要了。
+ Diversity matching requirment：doc会有长有短。按照Verbosity Hypothesis，长doc和短doc覆盖的范围一致，那么可以用global matching的方式来做；按照Scope Hypothesis，那么长doc表达的范围，可以切分为多个短doc，那么匹配可以发生在doc的不同部分。

Representaion-focused model，主要关注文章整体的意思，因为他们是分别表示的，直到最后才进行了cross，很多精确匹配的信息已经被丢失了。

## Model
文章中提出了一个DRMM的模型，相比较representatin-focused，新模型更加接近于interaction-focused model，主要是因为representation model不可避免的会丢失掉精确match的部分信息。
模型的构建，大致可以分为4步：
+ 构建query、doc间的local interaction特征，最简单的就是直接用word2vec计算cosine
+ 将变长的local interaction特征，变成定长的直方图特征
+ 利用前向神经网络进行传播
+ 根据term的重要性，对多个子网络算分进行加和
 
文章里面，提出了一个很有意思的观点，这里直接使用word2vec，而没有将这部分加入到网络中，主要是由于：
+ 在大量unlabel的数据上训练word2vec，比在部分有groud truth的数据上，训练得到的representation结果会更可靠
+ 利用先验的特征表示，模型可以更focus在relevace match上，降低模型的复杂程度。

### Matching Histogram Mapping
文章提出了两类matching matrix的计算方式
+ 第一类是和position有关的，但是要做zero-padding，这种做法对短文本会比较的不公平。
+ 第二类是和position无关的，可以将local interaction进行统计表示。文中的做法是利用histogram进行表示。在histogram的计算过程中，做了一个trick，就是对于精确命中的term，单独作为一个bin，因为这种特征的信息量特别大。

### Feed Forward Matching Network
文章中有一次提出了，relevacen matching是位置无关的，而CNN是位置有关的，所以这里没有使用CNN，对于特殊的pooling方式，例如MV-LSTM的K-max pooling也提出了质疑，认为对于长文本会有bias。

### Term Gating Network
term重要性的计算，很简单，就是一个softmax，不过没理解，是怎么把变长的query变长一个定长的向量的。

对于输入的x，文章里面尝试了两种不同的方式：
+ Term Vector：利用term的vector信息作为输入
+ IDF：利用term的idf作为输入，w变化为单一值

## Model Training
模型训练过程中，利用了hinge loss

## Experiment
文章中，对比了传统的QL、BM25，representation的DSSM，interaction的DRMM，从结果看，反而是传统的算法有最好的效果。

另外，调整了DRMM的不同部分，发现DRMM的效果，主要来自于输入的直方图，如果修改为一个dynamic cnn，效果会打折比较多。
