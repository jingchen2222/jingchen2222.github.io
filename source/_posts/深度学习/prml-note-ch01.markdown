---
layout: "post"
title: "PRML_note_Ch01"
date: "2017-01-02 19:46"

categories:
- study
- 读书笔记

tags:
- 深度学习
- 读书笔记
- Patten recognition
-
---

今天开始学习读机器学习的经典大作《Pattern recognition and machine learning》。计划1个月，读完前四章。

## 1.2 Probalility theory

#### A key Concept: 'uncertainty':

- it arised through noise on measurements
- it arised through the finite size of data sets


#### Example:

$B$: the identify of the box that will be chosen, can taken from $r$ (corresponding to the red box) or $b$ (coresponding to the blue);

define the probability of an event to be the fraction of times that events occurs out of the total number of trials


$p(B==r) = 4/10$

$p(B==b) = 6/10$


#### rules:

> the two el- ementary rules of probability, known as the sum rule and the product rule

joint probability
marginal probability
conditional probability

- sum rule

$$
p(X=x_i) = \sum_{j=1}^L{p(X=x_i, Y=y_j)}
$$


$$
p(X) = \sum_Y{p(X,Y)}
$$


> 'marginal probability' = sum of joint probability


- product rule

$$
p(X=x_i,Y=y_j) = \frac{n_{ij}}{N}=\frac {n_{ij} }{c_i} * \frac{c_i}{N}
$$
$$
= p(Y = y_j|X = x_i) * p(X = x_i)
$$


$$
p(X,Y) = p(X|Y)p(Y)
$$

> 'joint probability' = 'conditional probability' * 'marginal probability'


> $p(X, Y )$ is a joint probability and is verbalized as “the probability of X and Y ”.
>
> the quantity $p(Y |X)$ is a conditional probability and is verbalized as “the probability of Y given X
>
> $p(X)$ is a marginal probability  and is simply “the probability of X”.


#### Bayes’ theorem

$$p(Y|X) = \frac{p(X|Y){p(Y)}}{p(X)}$$

> play a central role in patten regnition and machine learning


Give 'sum rule', the we have:


$$
p(X) = \sum_{Y}^{}{p(X,Y)} = \sum_{Y}^{}{p(X|Y)*p(Y)}
$$

##### prior probability

> ... it is the probability available before we observe some conditional event

##### posterior probability

> it is the probability obtained after we have observed the conditional event( the identify of the fruit)


### Probability densities

##### Definition
If the probability of a real-valued variable x falling in the interval $(x, x + \delta{x})$ is given by $p(x)\delta{x}$ for $\delta{x} → 0$, then $p(x)$ is called the probability density over $x$.


##### Rules
> For instance, if x and y are two real variables, then the sum and product rules take the form
$$
p(x) = \int p(x, y)dy \\
p(x, y) =  p(y|x)p(x)
$$

### Expectations and covariances


$$
E[f] = \int_x p(x)f(x)dx
$$
We can also consider a conditional expectation with respect to a conditional
distribution, so that

$$
Ex[f|y] =\sum_x p(x|y)f(x)
$$

- The variance of f(x) is defined by

$$
var[f] = E[( f(x) - E[f(x)] )^2] \\
var[f] = E[( f(x)^2] - E[f(x)] ^2
$$



## Bayesian probabilities

> Now we turn to the more general Bayesian view, in which probabilities provide a quantification of uncertainty

- quantify our expression of uncertainty
- make precise revisions of uncertainty in the light of new evidence
- take optimal actions or decisions as a consequence.


### Bayes’ theorem example: polynomial curve fitting

- a prior probability distribution

> capture our assumptions about $w$, before observing the data, in the form of a prior probability distribution $p(w)$.

- the conditional probability

> The effect of the observed data $D = {t_1, . . . , t_N }$ is expressed through the conditional probability $p(D|w)$

- likelihood function

>The quantity $p(D|w)$ on the right-hand side of Bayes’ theorem is evaluated for the observed data set $D$ and can be viewed as a function of the parameter vector $w$,

- Bayes’ theorem:  

> evaluate the uncertainty in $w$ after we have observed D in the form of the posterior probability $p(w|D)$.

$$
p(w|D) = \frac {p(D|w)p(w)}{p(D)}
$$

$$
posterior ∝ likelihood × prior
$$

￼$$
p(D) = \int p(D|w)p(w) dw
$$


### Use
- small-scale applications
  -
- large-scale applications
  -

### Stuck points:

> When combined with decision theory, discussed in Section 1.5, it allows us to make optimal predictions given all the information available to us, even though that infor- mation may be incomplete or ambiguous.


>  Let us suppose that in so doing we pick the red box 40% of the time and we pick the blue box 60% of the time, and that when we remove an item of fruit from a box we are equally likely to select any of the pieces of fruit in the box.


>Under a nonlinear change of variable, a probability density transforms differently from a simple function, due to the Jacobian factor.

> For instance, Cox (1946) showed that if numerical values are used to represent degrees of belief, then a simple set of axioms encoding common sense properties of such beliefs leads uniquely to a set of rules for manipulating degrees of belief that are equivalent to the sum and product rules of probability.
