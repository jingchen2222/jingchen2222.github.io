---
layout: "post"
title: "neuron_network_machine_learning_lecture4_note"
date: "2017-01-16 12:24"

categories:
- study
- Deep learning
- Neuron network

tags:
- 深度学习
- Neuron network
---
---

## 补充知识点

### Cross-Entropy

#### 信息量


可以理解为，一个事件发生的概率越大，则它所携带的信息量就越小

$$I(x0)=−log(p(x0))$$

#### 什么是熵 - entropy

熵其实是信息量的期望值，它是一个随机变量的确定性的度量。熵越大，变量的取值越不确定，反之就越确定。


熵的定义:

$$
H(X) = E_p log{\frac{1}{p(x)}}
$$


#### 相对熵 - relative entropy

相对熵(relative entropy)又称为KL散度（Kullback-Leibler divergence），KL距离，是两个随机分布间距离的度量。记为DKL(p||q)。它度量当真实分布为p时，假设分布q的无效性。

$$
D_{KL}(p||q) = H_p(q)−H(p)
$$


#### 什么是交叉熵？

CHH(p,q)是p,q分布的交叉熵，反映了分布p，q的相似程度。
$$
CEH(p,q) = −[y log h_θ(x)+(1−y)log(1−h_θ(x))]
$$


>交叉熵与KL距离在行为上是等价的，都反映了分布p，q的相似程度。最小化交叉熵等于最小化KL距离。它们都将在p=q时取得最小值H(p)（p=q时KL距离为0），因此有的工文献中将最小化KL距离的方法称为Principle of Minimum Cross-Entropy (MCE)或Minxent方法。


[交叉熵（Cross-Entropy）from rtygbwwwerr的专栏][aee9fef5]


[A Friendly Introduction to Cross-Entropy Loss][94b12809]



  [94b12809]: https://rdipietro.github.io/friendly-intro-to-cross-entropy-loss/ "A Friendly Introduction to Cross-Entropy Loss"

### N-Grams

#### What is N-Grams

An n-gram is a contiguous sequence of n words, for example, in the sentence "dog that barks does not bite", the n-grams are:


- unigrams (n=1): dog, that, barks, does, not, bite

- bigrams (n=2): dog that, that barks, barks does, does not, not bite

- trigrams (n=3): dog that barks, that barks does, barks does not, does not bite
etc.


#### What are N-grams used for?



**N-Grams of texts** 主要用于数据挖掘和自然语言处理。

> when developing a language model, n-grams are used to develop not just unigram models but also bigram and trigram models.



- a publicly available web scale n-gram model by Microsoft: http://research.microsoft.com/en-us/collaboration/focus/cs/web-ngram.aspx.

> a paper that uses Web N-gram models for text summarization:Micropinion Generation: An Unsupervised Approach to Generating Ultra-Concise Summaries of Opinions



- Another use of n-grams is for developing features for supervised Machine Learning models such as SVMs, MaxEnt models, Naive Bayes, etc.

> The idea is to use tokens such as bigrams in the feature space instead of just unigrams. But please be warned that from my personal experience and various research papers that I have reviewed, the use of bigrams and trigrams in your feature space may not necessarily yield any significant improvement. The only way to know this is to try it!


[What are N-Grams?][496e460e]


[N-gram的原理、用途和研究][019a1c1a]


## Language Model

[漫谈 Language Model (1): 原理篇][94edc82f]
 ### Language Model的是什么?

> Language Model 中文就叫做“语言模型”吧，这实际上是一个概率分布模型 P ，对于语言里的每一个字符串 S 给出一个概率 P(S) 。


> 我使用了“单词”、“字符串”这样的字眼，然而 Language Model 实际上非常通用，任何由一些基础单元组成的序列都可以使用 Language Model 的方法来分析，例如 Speech Recognition 里的音频信号，Bioinformatics 里的基因序列等。

### Language Model 有什么用?

> 文档字词缺失
> 专门建立特定的 Language Model，然后作模型判断；如：判断正常程序和病毒
> 搜索引擎，内容匹配

### Pros and cons

  - Language Model 算是一种 Generative Model
  - Generative Model 则试图寻求数据的本质，一但成功构造出数据的实际模型，就可以用它来做（几乎）任何事情
  - 不过，一切都建立在能够正确够找出 the true model 的前提下


### Language Model 构建

$$
P(S) = P(w_1w_2... w_n)
= P(w_1)\Pi_{i=2}^{n}P(w_i|w_1...w_{i-1})
$$


#### 引出问题

**<font color='red'>context长度增长，模型计算量爆炸性增长</font>**


> 在构造模型的时候，我们可以通过统计训练数据里各个单词（在各个 context 之下）出现的频率来近似逼近它的概率，问题在这里就会出现了，本身随着 context 长度的增长，可能的情况会爆炸性地增长，训练数据不可能覆盖所有可能的数据，更严重的是，要存储这些频率值所需的空间也是指数增长的。


#### 朴素贝叶斯

>为了避免这种爆炸性的复杂度增长，有人做了一个很大胆的近似方法：抛弃所有的上下文信息。现在一个单词在不同的地方出现，对于这个模型来说都是同等对待了


$$
P(S) = P(w_1w_2... w_n)
= \Pi_{i=1}^{n}P(w_i)
$$


朴素贝叶斯方法，在很多场景应用上，因为模型还是太简单，造成了比较大的 bias ，效果并不好。

折衷的方法就是考虑一定长度以内的上下文。
unigram、bigram、trigram 以及通用的 n-gram。



### Language Model 构建遇到的问题


详情见pluskid博客：[漫谈 Language Model (2): 实践篇][77bd01aa]

#### 浮点精度问题

Language Model 计算的字符串的概率，采用连乘：
$$
P(S) = P(w_1w_2... w_n)
= \Pi_{i=1}^{n}P(w_i)
$$

虽然，用浮点表示每一个 $P(w_i)$ 都没有问题，但是这么多极小的数字乘起来，一不小心就会超出浮点数的精度范围，这个时候计算机就会把它当作 0 了。


> 解决的办法其实很简单，既然问题出在连乘上，那么就拿连乘开刀——在前面加一个 $\log$ ，让它变成连加：


$$
log(P(S)) = \sum_{i=1}^{n}logP(w_i)
$$

$\log$ 函数是单调递增的，因此，对$log(P(S))$和P(S)的求极大值（极小值）效果是一样的。

> $\log$ 函数是一个单调递增的函数，原来的大小比较现在还是照样比较即可。不过有一点要注意的地方就是：$\log$ 函数在 0 处是没有定义的（或者说 -\infty），因此我们需要避免概率值为 0 的情况出现，这也就是我们要说的下一个问题。

#### 0概率问题

- log函数下，要避免出现0概率
- 在引入新词的context下，结构较好（符合语法规则）的句子和结构较差（完全没有语法）的句子P(S)都是0。


##### 解决方案 —— smoothing

$$
P(w_i) = \frac{N_{w_i} + 1}{N+ |W|}
$$
其中，$|W|$ 是总的单词的个数。

smoothing方法不适用N-gram模型。



##### 解决方案 —— back-off

> 回退（back-off）的方法，叫做 Katz Smoothing，主要用在 n-gram 的模型中

以一个 trigram 模型为例，我们通过如下的办法计算条件概率

![](http://ogqir9ige.bkt.clouddn.com/4f3544693456382cb8c2ee5d066e33b1.png)


>下标 $\text{ML}$ 表示这是通过最大似然的方法估计出来的概率，如前面所说，如果没有 $w_1w_2w_3$ 出现在训练数据中的话，这个式子会得零，这不是我们想看到的，我们用回退的办法来计算它的概率，减小上下文的长度，因此重新定义一个概率计算公式如下:
因此重新定义一个概率计算公式如下：


![Katz](http://ogqir9ige.bkt.clouddn.com/c0371df3d09f3bba0829850932d0016c.png)



$其中 \alpha(w_1w_2) 是回退系数，d_r 是打折的折扣系数，下标 r = C(w_1w_2w_3)$

这部分推导并没有理解很清楚。大体上理解下来：
- 遇到不存在的词，就删减词——回退，去计算更小长度的字符串概率，如Trigram回退到Bigram,如果仍存在0概率，继续回退到 unigram

## A Neural Probabilistic Language Model

[A Neural Probabilistic Language Model, Journal of Machine Learning Research 3 (2003) 1137–1155, by Yoshua Bengio ... ](http://www.jmlr.org/papers/volume3/bengio03a/bengio03a.pdf)



[ Deep Learning 读书笔记（十二）：A Neural Probabilistic Language Model][d063a95f]

[類神經網路 -- Neural Probabilistic Language Model][5a7b18c9]
 这篇中文博客简明扼要。感谢博主无私分享新的，并且用这么干练的语言表达准确，


### 1.1 Fighting the Curse of Dimensionality with Distributed Representations


#### 方法总结如下：

1. 每一个word关联一个feature vector

2. express the joint probability function

3. learn feature vector and probability function


>
1. associate with each word in the vocabulary a distributed word feature vector (a real- valued vector in $R^m$),
>
2. express the joint probability function of word sequences in terms of the feature vectors of these words in the sequence, and
>
3. learn simultaneously the word feature vectors and the parameters of that probability function.


#### feature vector

- represent different aspects of the word
- number of features is much smaller than the size of the vocabulary.
- the feature vector are learned
- the feature vector could be initialized using prior knowledge of semantic feature

#### probability function
- probability function expressed as: a product of conditional probabilities of the next word given the previous ones.
- This function has parameters can be tuned to maximize the log-likelihood of the training data.





### A Neural Model


#### Neural architecture

![Neural architecture](http://ogqir9ige.bkt.clouddn.com/598bb8720388e105d248f1a61a61c482.png)


$$
L = 1/T ∑log f(w_t,w_{t−1},··· ,w_{t−n+1};θ)+R(θ),
$$


#### parameters

- 自由参个数正比于 vocabulary规模，V
- 基于sharing structure，自由参个数将sub-linear with V


> In the above model, the number of free parameters only scales linearly with V , the number of words in the vocabulary. It also only scales linearly with the order n : the scaling factor could be reduced to sub-linear if more sharing structure were introduced, e.g. using a time-delay neural network or a recurrent neural network (or a combination of both).



## Hierarchical probabilistic neural network language model

[類神經網路 -- Hierarchical Probabilistic Neural Network Language Model (Hierarchical Softmax)
][484ef271]


## Three New Graphical Models for Statistical Language Modellin

[Three New Graphical Models for Statistical Language Modellin][e613915d]
https://www.cs.toronto.edu/~amnih/papers/threenew.pdf


### Abstract

> We propose three new probabilistic language models that define the distribution of the next word in a sequence given several preceding words by using distributed representations of those words.



> how real-valued distributed representations for words can be learned at the same time as learning a large set of stochastic binary hidden features that are used to predict the distributed representation of the next word from previous distributed representations.


> Adding connections from the previous states of the binary hidden features improves performance as does adding direct connections between the real-valued distributed representations.


### Introduction

#### density estimation for discrete distributions difficult in smoothing
> Density estimation for discrete distributions is inherently difficult because there is no simple way to do smoothing based on input similarity.


> Since all discrete values are equally similar (or dissimilar) assigning similar probabilities to similar inputs, which is typically done for continuous inputs, does not work in the discrete case.
离散数据，要么相似，要么不相似。无法根据相似度的不同来分配概率。


> Representing discrete structures such as words using continuous-valued distributed representations and then assigning probability to these structures based on their representations automatically introduces smoothing into the density estimation problem making the data sparsity problem less severe.

#### continuous-valued distribution representation was introdeced
<font color='blue'>于是，引入Representing discrete structures，（如：word's distributed feature vectors）来表征word。用feature vector代表词，words这种离散结构被一个continuous-valued distribution representations代替了，因此automatically introduces smoothing into the density estimation problem，从而缓解了data sparsity problem。</font>


#### drawback: long training times
> A number of techniques have been proposed to address the main drawback of these models – their long training times


#### Recently Approach

- Hierarchical alternatives to feed-forward networks which are faster to train and use, do not perform quite as well.

(Morin & Bengio： Hierarchical prob- abilistic neural network language model, 2005; Blitzer et al., 2005b Hierarchical distributed representations for statistical language modeling.)



- Recently, a stochastic model with hidden variables has been proposed for language modelling . It uses distributed representations that consist of stochastic binary variables as opposed to real numbers.

(Blitzer et al., 2005a)：Distributed latent variable models of lexical co- occurrences.

> 但是由于模型训练难度，这种模型不能scale well to large vocabulary sizes。因为 inference to model太难了——你想也知道，这种带大量binary variables的model训练难度一般来说和2的指数次都有点啥关系，这里的m不一定是什么。在这篇文章里也是一样，刚开始Hinton提出的模型原型RBM机训练时间也是级别的。


> 为了方便起见，这篇论文里面涉及到的RBM原型和三个基于Restricted Boltzmann Machine的Models将被命名为：
>
> - 零号机——RBM Model
> - 初号机——Factored RBM
> - 二号机——The Temporal Factored RBM
> - 三号机——Log-Bilinear Language Model
>
> from [许九祀-知乎][75d8a4ac]


#### Out Approach
We start with an undirected graphical model that uses a large number of hidden binary variables to capture the desired conditional distribution.

Then we augment it with temporal connections between hidden units to increase the number of preceding words taken into account without significantly increasing the number of model parameters.

Finally, we investigate a model that predicts the distributed representation for the next word using a linear function of the distributed representations of the preceding words, without using any additional latent variables.



### The Factored Restricted Boltzmann Machine Language Model : 零号机和初号机


#### Goal
> Our goal is to design a probabilistic model for word sequences that uses distributed representations for words and captures the dependencies between words in a se- quence using stochastic hidden variables.

- use distributed representations for words
- captures the dependencies between words using stochastic hidden variables.


#### 零号机

Energy function:

![](http://ogqir9ige.bkt.clouddn.com/98fff360a8c63be13fa51c708ef24cc4.png)

Drawback:

- parameters : N * N (N is vocabulary length)
- parameters depend on position


Problems Addressed by :



#### 1号机:  2. RBM with distributed representations for words


- introducing distributed representations
- sharing word feature vectors
- related work:
    - > This type of parameterization has been used in feed-forward neural networks for mod- elling symbolic relations (Hinton, 1986) and for statis- tical language modelling (Bengio et al., 2003).


##### Feature vectors

a real-valued feature vector of length Nf

##### Energy function
![energy_function_no1_machine](http://ogqir9ige.bkt.clouddn.com/a830c0e317646f76edd2909704ec8199.png)

##### Advantage : reduced parameters (dimension)
- $N_f << N_m$
- sharing feature vectors force to capture **position- invariant** information


> As can be seen from Eqs. 1 and 2, the feature-based parameteriza- tion constrains each of the visible-hidden interaction matrices Gi to be a product of two low-rank matrices R and Wi, while the original parameterization does not constrain Gi in any way.

##### The joint conditional distribution of the next word
![](http://ogqir9ige.bkt.clouddn.com/b12c2e869cb15fbd5e8a3c26696a6654.png)


##### conditional distribution of the next word
因此，w_n在给定w_{1:n-1}的情况下的条件概率就是公式（3）的边缘概率，可以计算为

![](http://ogqir9ige.bkt.clouddn.com/7d51d81daf6ec5262686fea143028569.png)


> 尽管同一个词对应的还是在变（比如说第三个词是dog和第一个词是dog时对应的边权值矩阵分别为和）——根据前文（特别是那句 Using the same feature matrix  ... capture position-invariant information about words）暗示了这个矩阵是为了抓position-variant information about words。
<font color='red'>但是由于输入变量（输入变量对应的是0号机的和初号机中的），在这里是，这个输入变量固定了，因此还能抓position-invariant information。</font>

##### 2.1 Making Predictions

<font color='red'>从这之后，看不懂了 --------- 2017-01-21------------------------------------未完待续</font>



### 相关知识点：

- Restricted Boltzmann Machines (RBM)

### 难点理解：

- Density estimation for discrete distributions is inherently difficult because there is no simple way to do smoothing based on input similarity.

    - <font color='red'> **why smoothing is necessary?**</font>
    - conception: density estimation




#### Data sparsity

> One of the biggest problems is that mostly the cube is very sparsely populated.
Many of the cell combinations might not make sense or the data for them might be missing. In the relational world storage of such data is not a problem: we only keep whatever there is. If we want to keep closer to our multidimensional view of the world, we face a dilemma: either store empty space or create an index to keep track of the nonempty cells. Or - search for an alternative solution.

[What is a clear explanation of data sparsity?][4f6d61b0]




## Restricted Boltzmann Machines (RBM)


[An Introduction to Restricted Boltzmann Machines][309654cd]

[Restricted Boltzmann Machines (RBM)][b67af3e1]

[【Paper知识点】Restricted Boltzmann Machines(RBM)][99894bfa]

### Energy-Based Models (EBM)

  [99894bfa]: https://zhuanlan.zhihu.com/p/20396389?columnSlug=system "【Paper知识点】Restricted Boltzmann Machines(RBM)"

EBM用energy function来表征概率密度函数：

> Energy-based probabilistic models define a probability distribution through an energy function, as follows:

$$p(x) = \frac {e^{-E(x)}} {Z}.$$

Z用于归一化，称作partition function：

> The normalizing factor Z is called the partition function by analogy with physical systems.

$$Z = \sum_x e^{-E(x)}$$

log-likelihood function:
$$
\mathcal{L}(\theta, \mathcal{D}) = \frac{1}{N} \sum_{x^{(i)} \in
\mathcal{D}} \log\ p(x^{(i)})\\
$$

loss function:

$$
\ell (\theta, \mathcal{D}) = - \mathcal{L} (\theta, \mathcal{D})
$$

利用SGD（随机梯度下降）求解：

> using the stochastic gradient $-\frac{\partial  \log p(x^{(i)})}{\partial
\theta}$, where $\theta$ are the parameters of the model.


#### EBMs with Hidden Units

hidden Units引入原因：
- 无法观测全样本
- 想引入一些未观测的量来提升模型表现力

> In many cases of interest, we do not observe the example x fully, or we want to introduce some non-observed variables to increase the expressive power of the model. So we consider an observed part (still denoted x here) and a hidden part h. We can then write:

$$P(x) = \sum_h P(x,h) = \sum_h \frac{e^{-E(x,h)}}{Z}.$$

我们引入一个free energy的概念
> In such cases, to map this formulation to one similar to Eq. (1), we introduce the notation (inspired from physics) of free energy, defined as follows:

$$
\mathcal{F}(x) = - \log \sum_h e^{-E(x,h)}
$$


which allows us to write,

$$
P(x) = \frac{e^{-\mathcal{F}(x)}}{Z} \text{ with } Z=\sum_x e^{-\mathcal{F}(x)}.
$$

The data negative log-likelihood gradient then has a particularly interesting form.

$$
-\frac{\partial  \log p(x)}{\partial \theta} = \frac{\partial \mathcal{F}(x)}{\partial \theta} -
       \sum_{\tilde{x}} p(\tilde{x}) \
           \frac{\partial \mathcal{F}(\tilde{x})}{\partial\theta}
$$

卡在公式推导上，幸得一篇笔记，作者详细给出了推导过程。现直接从[【Paper知识点】Restricted Boltzmann Machines(RBM)][99894bfa]扒过来：


$$
log(P(x))=-F(x)-log(Z) \\
\frac{\partial{log(P(x))}}{\partial\theta}=\frac{-F(x)}{\partial\;\theta}-\frac{log(Z)}{\partial \;\theta} \\
\frac{\partial log(P(x))}{\partial \theta} = \frac{-F(x)}{\partial\;\theta}-\frac{1}{Z}\frac{\partial Z}{\partial \theta}\\
\frac{\partial Z}{\partial \theta}=\sum_x \frac{e^{-F(x)}}{\partial \theta} = \sum_x e^{-F(x) } \cdot \frac{-F(x)}{\partial \theta}（注意\frac{\partial e^{y(x)}}{\partial x}=e^{y(x)}\cdot \frac{\partial y(x)}{\partial x}）\\
\frac{\partial log(P(x))}{\partial \theta} = \frac{-F(x)}{\partial\;\theta}-\frac{1}{Z} \sum_x e^{-F(x) } \cdot \frac{-F(x)}{\partial \theta}\\
\frac{\partial log(P(x))}{\partial \theta} = \frac{-F(x)}{\partial\;\theta}-\sum_{\tilde{x}} P(\tilde{x}) \cdot \frac{-F(\tilde{x})}{\partial \theta}\\
-\frac{\partial log\;p(x)}{\partial\theta}=\frac{\partial \mathcal{F}(x)}{\partial \theta} - \sum_{\tilde{x}}P(\tilde{x})\frac{\partial \mathcal{F}(\tilde{x})}{\partial \theta}
$$


这个式子前面部分叫positive phase，后面的叫negative phase。

> positive phase: increases the probability of training data, by reducing the corresponding free energy.

> negative phase: decreases the probability of samples generated by the model.


Negative部分实际上是$\frac{\partial \mathcal{F}(\tilde{x})}{\partial\theta}$在P分布下的期望：

$$ E_P [ \frac{\partial \mathcal{F}(x)} {\partial \theta} ] = \sum_{\tilde{x}} p(\tilde{x}) \
     \frac{\partial \mathcal{F}(\tilde{x})}{\partial\theta}$$

> It is usually difficult to determine this gradient analytically, as it involves the computation of E_P [ \frac{\partial \mathcal{F}(x)} {\partial \theta} ]. This is nothing less than an expectation over all possible configurations of the input x (under the distribution P formed by the model) !

于是，目标问题可以近似转化为:**
用fixed number of model samples来estimate the expectation了**。

$$
- \frac{\partial \log p(x)}{\partial \theta}
 \approx
  \frac{\partial \mathcal{F}(x)}{\partial \theta} -
   \frac{1}{|\mathcal{N}|}\sum_{\tilde{x} \in \mathcal{N}} \
   \frac{\partial \mathcal{F}(\tilde{x})}{\partial \theta}.
$$
> where we would ideally like elements $\tilde{x}$ of $\mathcal{N}$ to be sampled according to P (i.e. we are doing Monte-Carlo). With the above formula, we almost have a pratical, stochastic algorithm for learning an EBM. The only missing ingredient is how to extract these negative particles $\mathcal{N}$.


### Boltzmann Machines(BM)




[An Introduction to Restricted Boltzmann Machines][309654cd]
### Restricted Boltzmann Machines (RBM)



> RBM通过引入hidden variables让模型的效果增强，RBM所对的图必须是二分图（不能有visible-visible 或者 hidden-hidden这样的连接）。 from [知乎笔记][b67af3e1]

#### Boltzmann Machines

#### MRF

[PGM学习之七 MRF，马尔科夫随机场][aae563ca]



### Sampling in an RBM




---
[aee9fef5]: http://blog.csdn.net/rtygbwwwerr/article/details/50778098 "交叉熵（Cross-Entropy）from rtygbwwwerr的专栏"

[019a1c1a]: http://blog.sciencenet.cn/blog-713101-797384.html "N-gram的原理、用途和研究"


[496e460e]: http://text-analytics101.rxnlp.com/2014/11/what-are-n-grams.html "What are N-Grams?"



[94edc82f]: http://blog.pluskid.org/?p=352 "漫谈 Language Model (1): 原理篇"



[77bd01aa]: http://blog.pluskid.org/?p=361 "漫谈 Language Model (2): 实践篇"


[d063a95f]: http://blog.csdn.net/tuqinag/article/details/43798033 "Deep Learning 读书笔记（十二）：A Neural Probabilistic Language Model"



[484ef271]: http://cpmarkchang.logdown.com/posts/276263--hierarchical-probabilistic-neural-networks-neural-network-language-model "類神經網路 -- Hierarchical Probabilistic Neural Network Language Model (Hierarchical Softmax)
"

[5a7b18c9]: http://cpmarkchang.logdown.com/posts/255785-neural-network-neural-probabilistic-language-model "類神經網路 -- Neural Probabilistic Language Model"



[e613915d]: https://www.cs.toronto.edu/~amnih/papers/threenew.pdf "Three New Graphical Models for Statistical Language Modellin"


[4f6d61b0]: https://www.quora.com/What-is-a-clear-explanation-of-data-sparsity "What is a clear explanation of data sparsity?"


[75d8a4ac]: https://zhuanlan.zhihu.com/p/20434661 "【Paper】ICML 2007 - Three New Graphical Models for Statistical Language Modeling"



[b67af3e1]: http://deeplearning.net/tutorial/rbm.html "Restricted Boltzmann Machines (RBM)"


[309654cd]: http://image.diku.dk/igel/paper/AItRBM-proof.pdf "An Introduction to Restricted Boltzmann Machines"

[aae563ca]: http://blog.csdn.net/polly_yang/article/details/9716591 "PGM学习之七 MRF，马尔科夫随机场"
