---
layout: post
title: "Python 机器学习: 从0到1"
date: 2015-09-12
categories: 机器学习 Python
excerpt: Python 机器学习: 从0到1
---

* content
{:toc}

# Python 机器学习: 从0到1

目标: 记录最近尝试机器学习的经历和关键节点, 让 `六个月前的自己` 可以更顺利的上手机器学习.

提纲:

- 原理
- 流程
- 生态系统
- 第一个版本

## 原理

机器学习 (machine learning)

是什么? 

一段可以从已知数据中"学习", 随后预测未知数据的算法. 首先, 我们把一些数据(训练集, training set) 输入进算法, 算法会从这些数据中学习到一些规律; 随后, 我们把另外的数据(测试集, test set)输入学习后的算法, 算法会预测一些相关的结果.

抽象: 我们有训练集 X_train, y_train, 测试集 X_test. 机器学习, 即用 X_train, y_train 得到二者关系, 再从 X_test 预测 y_test 的过程.

为什么? 

大量数据人类处理起来非常困难, 也不现实. 如果机器学习可以为我们处理这些数据, 就可能发挥出极大的作用. 如大量文本的 OCR, 自动语音识别, 根据购买记录为每个人推荐可能喜欢的商品等等.

以及, 人工智能的梦想

怎么做? 

很多种算法, 很多种程序设计语言都可以进行机器学习工程实践. 在 Python 中, 已有成熟的各类算法包, 其中最著名的是 [scikit-learn](http://scikit-learn.org/stable/index.html)

## 流程

其实, 我们熟知的线性回归, 就是机器学习的一种方法. 在线性拟合之中, 我们先用已知数据作为训练集, 算法学习到的规律即为回归函数; 在此之后, 算法就可以对未知数据进行预测.

- 算法设计
- 训练
- 预测

在真实的问题之中, 数据通常都不是已经整理好的 X, y, 而可能是各种各样的格式. 如 自然语言文本(最近参加的[天池大赛](link here)就是微博原始文本), 图片, 音频, 视频... 在开始设计算法之前, 我们要先整理数据, 甚至要先获得足够多的数据. 机器学习通常由以下几个过程组成:

- 获得数据
- 整理数据
- 算法设计
- 训练与算法优化
- 预测

在我上手的过程中, 发现一开始的困难, 多半是在各个部分的 i/o 接口之中, 而非算法设计. (因为算法使用黑箱模型, 实际上不需要设计和测试算法本身.) 而在后期, 逐渐发现不同算法在数据量足够大时结果相近, 真正的难点往往是寻找合适的特征(feature), 微博的算法实践也初步证实了这一点.

如果想让自己的机器学习 demo 尽快跑起来, 就需要先初步理解这个流程中用到的 Python 科学计算生态系统, 明白每一步的 i/o 接口如何设计.

## 生态系统

Python 的科学计算生态系统主要包括: NumPy, SciPy, matplotlib, scikit-learn, pandas, ...

这里我们的目标不是详尽介绍 Python 科学计算, 而是简单描述相关的数据结构, 从而尽快搭起可用的机器学习算法

- NumPy: 提供多维数组
- SciPy: 稀疏矩阵
- matplotlib: 可视化
- scikit-learn: 机器学习算法包, 提供各种常用算法的黑箱模型. 也就是说, 作为用户, 不必关心实现细节, 只要把数据送进来就好了.

那么, 我们需要把什么数据格式送到 scikit-learn 中呢?

实际上只需要送两个变量 X, y 

X 是 m*n 矩阵(数据结构是二维数组/嵌套列表), 其中m是样本数量, n 是特征数量; 

- 常用格式为 numpy.ndarray
- 通常可用 `type(X)` 输出判断 X 的格式是否正确; 用 `X.shape` (对于 numpy.ndarray 和 稀疏矩阵应都可用) 判断 X 的维度是否正确

调整格式/feature合并/样本合并等常用 data cleanup 操作:

- 先用 type() 确认需要调整的矩阵格式
- 对于 numpy ndarray: np.concatenate(list, axis)
- 对于 scipy.sparse: sp.vstack/sp.hstack
- debug/troubleshoot: 使用 X.shape 即可确认操作是否正确
- scipy.io 提供 loadmat()/savemat() 可用于存储 feature 到文件备用

y 是一维列表(注意, 机器学习中常把 y 用列向量表示, 与一般假定不同)

- 可用 y.ravel() 把 y 转为 行向量/ 列表

## 第一个版本

从线性回归 [Linear Regression Example](http://scikit-learn.org/stable/auto_examples/linear_model/plot_ols.html#example-linear-model-plot-ols-py) 开始吧

```python
>>> from sklearn import linear_model
>>> clf = linear_model.LinearRegression()
>>> clf.fit ([[0, 0], [1, 1], [2, 2]], [0, 1, 2]) # x是 m*n 矩阵(数据结构是二维数组/嵌套列表), m是样本数量 (3) , n 是特征数量 (2), ; y 是一维列表(注意, 机器学习中常把 y 用向量表示)
LinearRegression(copy_X=True, fit_intercept=True, n_jobs=1, normalize=False)
>>> clf.coef_ # 模型中各参数的值
array([ 0.5,  0.5])
```

scikit-learn 中最常用的方法:

- `fit(X, y)`
- `predict(X)`

也就是说, 只要整理好数据, 只需要 `选择模型, fit, predict` 三步就完成了机器学习过程. scikit-learn 就是这么轻松愉快 :)

各种模型基本都通用, 可在 api 中查具体模型的对应方法

[模型保存和读取](http://scikit-learn.org/stable/tutorial/basic/tutorial.html#model-persistence)

```python
from sklearn.externals import joblib
joblib.dump(clf, 'filename.pkl') 

clf = joblib.load('filename.pkl') 
```

注: 如此存储时 .pkl文件还会带有同样名字的几个其它文件 .npy 等, 不要误删.
