---
title: Building Machine Learning Systems with Python
tags: 学习笔记
grammar_cjkRuby: true
---
[TOC]

### 1. Getting Started
 
#### 1.1 introduce to numpy

A big advantage of NumPy arrays is that the operations are propagated to the individual elements. For example, multiplying a NumPy array will result in an array of the same size with all of its elements being multiplied:
```
>>> d = np.array([1,2,3,4,5])
>>> d*2
array([ 2,  4,  6,  8, 10])
```
Contrast that to ordinary Python lists:
```
>>> [1,2,3,4,5]*2
[1, 2, 3, 4, 5, 1, 2, 3, 4, 5]
>>> [1,2,3,4,5]**2
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unsupported operand type(s) for ** or pow(): 'list' and 'int'
```
> 参考page32页的栗子，可知在python中循环取一个元素是非常耗时的，所以我们应该尽可能把==循环==这件事交给numpy、scipy中已经优化好的函数去实行。In every algorithm we are about to implement, we should always look how we can move loops over individual elements from Python to some of the highly optimized NumPy or SciPy extension functions

#### 1.2 introduce to scipy
```
>>> import scipy as sp
>>> data = sp.genfromtxt("web_traffic.tsv", delimiter="\t",skip_header=1)
>>> x = data[:,0]
>>> y = data[:,1]
>>> x = x[~sp.isnan(y)]  #rm the null value
>>> y = y[~sp.isnan(y)] 
```
数据读取、预处理之后，可以先进行初步的可视化观察数据，选择更适合的模型。先假设这个例子里面是个线性模型。 (a straight line has order 1)
```
>>> fp1, residuals, rank, sv, rcond = sp.polyfit(x, y, 1, full=True)
>>> f1 = sp.poly1d(fp1) # use polyld to create a model function
>>> print(error(f1, x, y))
317389767.34
```
> we can see that a linear model may not be the best model for the dataset, so order was increased to increase model complexity. However, there is a problem of ==overfitting==

> On the other hand, the lower degree models seem not to be capable of capturing the data good enough. This is called ==underfitting==.

#### 1.3 modeling
   * **Training and testing**
   如果我们有来自未来的数据，那么我们就可以用未来数据和模型之间的近似误差来评判模型的好坏。遗憾的是，我们并不能触碰未来。但是我们可以通过分离一部分实际数据来模拟reality。举个例子，我们移除相应百分比的数据，对剩下的数据进行训练，然后用这些holdout数据来计算approximation error. As the model has been trained not knowing the held-out data, we should get a more realistic picture of how the model will behave in the future.

### 2.classification
#### 2.1 第一个简单分类模型
最简单的例子，反复迭代数据，找出accuracy最高的分界线或分界面（预先知道true or false）
然而，存在的问题是，我们用全部数据本身去确定最佳的threshold，这样得到的accuracy自然是最高的。但是我们想要估计的是模型处理new instance的performance，所以与上一章讲的类似，先训练再测试。
这样，新的问题又出现了，如果training datasets中靠近真实decision boundary的数据点丢失了，这样训练之后确定的threshold就会左右（或者上下）移动，进而影响test datasets的准确性。这个问题会随着模型的复杂化变得愈加严重。
* **cross-validation**
>比较理想的但是实际上不可能的一个解决办法就是cross-validation。简单来讲，就是拿一部分做test，剩下的为training data，反复循环，得到最佳模型。为了减轻计算量，可以用x-fold cross-validation。
There is a trade-off between computational ef ciency (the more folds, the more computation is necessary) and accurate results (the more folds, the closer you are to using the whole of the data for training). **When generating the folds, you need to be careful to keep them balanced. For example, if all of the examples in one fold come from the same class, then the results will not be representative. We will not go into the details of how to do this, because the machine learning package scikit-learn will handle them for you.**

#### 2.2 一个更复杂的例子
* **What makes up a classi cation model?**
>1、The structure of the model;
2、The search procedure；
3、The gain or loss function


