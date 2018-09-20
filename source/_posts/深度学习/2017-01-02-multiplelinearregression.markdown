---
layout: "post"
title: "MultipleLinearRegression"
date: "2017-01-02 11:57"

categories:
- study
- 深度学习
- linear regression

tags:
- 深度学习
- regression
---

# Linear Regression with multiple variables

## Multiple Feature

### Feature scaling

#### Mean Normalization

$x = (x-\mu)/S$

其中，$\mu$是样本的均值。S是样本的range:(Max-Min)


## Gradient descent

Debugging： How to make sure gradient descent is working corrctly:

- Gradient($J(\theta)$) should decrease after evert iteration
- Use small learning rate($\alpha$)
- But if use too small alpha, gradient will be slow to converge
