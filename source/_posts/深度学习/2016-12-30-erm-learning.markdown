---
layout: "post"
title: "ERM_learning"
date: "2016-12-30 13:14"
categories:
- study
- 深度学习
- learning theory

tags:
- 深度学习
- EMS
- Bias
- variance
---

# Stanford Machine Learning Note - Learning Theory



> 理解了学习理论是对机器学习只懂皮毛的人和真正理解机器学习的人的区别。学习理论的重要性在于通过它能够针对实际问题更好的选择模型，修改模型。
> by Andrew Ng



本文是Andrew NG第九课和第十课的内容笔记。
[Lecture9][1b1b2e0d],[Lecture10][f85d635a]

这两课介绍Learning Theory（学习理论）。由于数据公式推导较多，理论性比较强，看了两遍仍掌握不到位。遂参考和学习网友相关笔记，做一下Review。


本文所参考文献材料包括：
- [《统计学习方法》,李航][7462eecd]
- [斯坦福ML公开课笔记9—偏差/方差、经验风险最小化、联合界、一致收敛][d5c50904]
- [经验风险最小化][792c4e23]
- [svm、经验风险最小化、vc维][30e756b0]


学习理论主要包括:
- 偏差/方差（Bias/variance）
- 经验风险最小化（Empirical Risk Minization，ERM）
- 联合界（Union bound）
- 一致收敛（Uniform Convergence）。


<!-- More -->
## Bias & Variance (偏差和方差)

### 基本概念

- 泛化能力（Generalization ability）
  > 学习方法的泛化能力是指由该方法学习到的模型对未知数据的预测能力，是学习方法本质上最重要的性质。
  >
  > 通过测试误差来评价学习方法的泛化能力，具有局限性。评价结果可能不可靠
  >
  > 统计学习理论试图从理论上对学习方法的泛化能力进行分析。
  >
  > 泛化能力分析往往是通过研究泛化误差的概率上界进行的。简称为泛化误差上界。具体来说，通过比较两种模型的泛化误差上界的大小来比较他们的优劣。




- 泛化误差 Generalization error:
[wiki_Generalization error][80fc3ae3]

  ![generalization_error_definition](http://ogqir9ige.bkt.clouddn.com/0c8019e8f3b944d9955f645fa7e3a9bb.png)

  > 事实上，泛化误差就是所学习到的模型的期望风险
- 欠拟合（Underfit）和过拟合（Overfit）

  [80fc3ae3]: https://en.wikipedia.org/wiki/Generalization_error "wiki_Generalization error"


- 偏差（bias）和方差（Variance）


- 泛化误差上界

> 它是样本容量的函数，当样本容量增加时，泛化上界就趋于0；
>
> 它是假设空间容量（|H|=k）的函数,假设空间容量k越大，模型越复杂，泛化误差上界就越大。


### 概念解释


为了直观，我们用三幅图来解释偏差和方差。
![GeneralizationError](http://ogqir9ige.bkt.clouddn.com/caf73674fdb745c7fc67b0cf30e99157.png)
图左, 使用二元线性模型拟合数据，即使给再大的train data，仍避免不了较大的泛化误差。这种情况称为欠拟合，对应高偏差（high bias）

> we define the bias of a model to be the expected generalization error even if we were to fit it to a very (say, infinitely) large training set.


图右,使用高阶函数拟合数据，对当前训练数据拟合很好，但也因此拟合出很多训练集特有结构特性。当在其他测试集上，又出现较大泛化误差。这种情况成为过拟合（Overfit），对应高方差。


## 经验风险最小化（empirical risk minimization, ERM）

### 经验风险最小化表示方式:

![ERM1](http://ogqir9ige.bkt.clouddn.com/d72dc30e92cfa2a0086bf7b0876fd03e.png)
即，选择训练误差最小的参数

另一种等价的表示方式：
![ERM2](http://ogqir9ige.bkt.clouddn.com/c7a305e6dbe360cdf84a7abe6427c783.png)

泛化能力可以表示为：
![generalizationAbility](http://ogqir9ige.bkt.clouddn.com/8d3154360facd49b06fe6c2601dae25e.png)

### 联合界（Union bound）、一致收敛（Uniform Convergence）

Union bound
![UnionBound](http://ogqir9ige.bkt.clouddn.com/e6255ad366e95512fdfb48099d2d1dad.png)

Hoeffding inequality
![HoeffdingInequality](http://ogqir9ige.bkt.clouddn.com/9be870ee62d4e675a2d7ce81cbc381a3.png)

> This lemma (which in learning theory is also called the Chernoff bound) says that if we take φˆ—the average of m Bernoulli(φ) random variables—to be our estimate of φ, then the probability of our being far from the true value is small, so long as m is large

Hoeffiding ineuality 定理的意义在于，当m不断增大，对参数的经验估计将逼急真实值。

### 一致收敛（Union Convergence）
我们使用ERM的第二种定义来证明该定理。
首先，推导当模型集合H是有限集合时，定理成立。The case of finite H：

![Finite_Convergence](http://ogqir9ige.bkt.clouddn.com/6e7870516572e9422f442fc402e831d1.png)

又根据Union bound定理，可以推出
![Finite_Convergence_2](http://ogqir9ige.bkt.clouddn.com/f69f8ae01d90b8b50de86b6e49f68902.png)

其中，Ai表示：![Ai](http://ogqir9ige.bkt.clouddn.com/686fa25626ddac1a11d3a0d5825e1683.png)

求反：
![Finite_Convergence_3](http://ogqir9ige.bkt.clouddn.com/2c1a879acec2c9aada95a3711d8c1e69.png)
![Finite_Convergence_result](http://ogqir9ige.bkt.clouddn.com/028b189a2c631d74a437ffd6a6e39861.png)

以上即是**一致收敛定理**，它的意义在于：
> 至少有一定的概率`(1−2kexp(−2γ^2m))`，使得模型集合H中的所有假设，其泛化误差都在训练误差的γ范围内。

#### 一致收敛推论
在一致收敛定理中，有参数γ，m，P。根据一致收敛定理不等式，通过固定其中两个，就可以推导出其他第三个的边界。


##### 推论1:

 问题：给定γ和δ，需要多少样本量（m）可以保证至少有1-δ的概率，使得泛化错误（Generalization error）在训练错误的γ范围内。

![sample_complexity](http://ogqir9ige.bkt.clouddn.com/9d20755e89b4c4f02ebb7b2d0f7747d0.png)

##### 推论2:
问题：给定m和δ，泛化错误落在训练错误的多少范围为：

![uniform_convergence_3](http://ogqir9ige.bkt.clouddn.com/1da2f12b66049a1be467631aff7c6a46.png)

#### 泛化能力的估计
在一致收敛定理成立的情况下，通过ERM算法得到的假设h^的泛化能力如何评估？

##### Notation
- 泛化误差最小的假设（最优假设）h∗
> h∗ = arg minh∈H ε(h) to be the best possible hypothesis in H

- 训练误差最小的假设（经验最优假设）hˆ
> hˆ = arg min εˆ(h)

- 推导过程:
  - step1: 根据Union convegence,泛化误差<经验误差+γ
  - step2: 因为hˆ是经验最优假设，经验最优误差<=h∗的经验误差
  - step3: 根据Union convegence, h∗的经验误差<= h∗的泛化误差+γ，最终：

  ![generalization_ability_cal1](http://ogqir9ige.bkt.clouddn.com/7576321020a3901dcdf5f9d576897942.png)

最终结论：

![generalizaiton_ability_value_final](http://ogqir9ige.bkt.clouddn.com/01731538826a6bc45c609e9d387aa339.png)

这个定理反应了偏差和误差的权衡。一般而言：
- 当选择复杂模型时：k将变得很大。不等式的第二项变大。同时，由于k变大，H集合变大，有可能找到更小的h。


这个推论还有另外一种形式：

![generalization_ability_value_final_2](http://ogqir9ige.bkt.clouddn.com/f0f62162a46f0538c32b126ab3246612.png)

## 无限集合证明
然后，推导当模型集合H是无限集合时，定理成立。


### 几个概念

#### VC维（Vapnik-Chervonenkis dimension）

![VC_demension](http://ogqir9ige.bkt.clouddn.com/ed21f842d89f310da998bf4208e626e3.png)

### 无限集合下的，一致收敛定理
![infinite_union_convengen](http://ogqir9ige.bkt.clouddn.com/45aa40716e3822e50202568875a85c0d.png)

![Corollary_inifite_convergence](http://ogqir9ige.bkt.clouddn.com/5b52eae8fa6f9fe2a23d6b650f6e7cd9.png)

#### SVM
SVM就是一种降低VC demension的方法来提高泛化能力。


----






  [792c4e23]: https://www.zybuluo.com/a335031/note/35923 "经验风险最小化"
  [30e756b0]: http://blog.csdn.net/keith0812/article/details/8901113 "svm、经验风险最小化、vc维"


  [d5c50904]: http://blog.csdn.net/stdcoutzyx/article/details/12110337 "参考笔记1："




  [7462eecd]: https://www.gitbook.com/book/bj6221/-/details "统计学习方法，李航"




  [1b1b2e0d]: http://open.163.com/movie/2008/1/F/H/M6SGF6VB4_M6SGJV3FH.html "Lecture9"
  [f85d635a]: http://open.163.com/movie/2008/1/U/O/M6SGF6VB4_M6SGJURUO.html "Lecture10"
