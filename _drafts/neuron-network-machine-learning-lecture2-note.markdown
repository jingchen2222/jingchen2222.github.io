---
layout: "post"
title: "neuron_network_machine_learning_lecture2_note"
date: "2017-01-04 22:20"
categories:
- study
- Deep learning
- Neuron network

tags:
- 深度学习
- Neuron network
---

---

## Neuron Network Machine learning

## Problem sets

Suppose we are given three data points:

x →  t

1,0 → 1

1,1 → 1

0,1 → 0

Furthermore, we are given the following weight vector (where the bias is set to 0):

$$w=(0,−3)$$
Let $||w(t)−w(t−1)||_2$
be the distance between the weight vectors at iteration $t$ and iteration $t−1$ of the perceptron learning algorithm. Here, for a given 2D vector $v$, $||v||_2=\sqrt{v_1^2+v_2^2}$ (this is also called the Euclidean norm).

What is the maximum amount by which the weight vectors can change between successive iterations? Note that in this example we are not learning the bias.


Answer:

Let's say that at time $t$ we observe that we have misclassified some point $\hat{x}$ with target $\hat{t}$. Then the learning algorithm will proceed as:



$$ W(t)=\left\{
\begin{aligned}
W^{t-1} + \hat{x} & &      &{\hat{t}=1} \\
W^{t-1} - \hat{x} & &      &{\hat{t}=0} \\
\end{aligned}
\right.
$$

In either case, the distance between $W^{t}$ and $W^{t-1}$ will be $$
||W^{t−1}−W^{t−1}±\hat{x}||_2=||±\hat{x}||_2=||\hat{x}||_2≤\sqrt{2}$$

since this is the length of the largest input vector (in this case, $(1,1)$).
