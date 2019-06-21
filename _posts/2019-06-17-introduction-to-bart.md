---
title: 'Introduction to Bayesian Additive Regression Trees'
date: 2019-06-25
permalink: /posts/2019/06/introduction-to-bart
header:
  overlay_image: /posts/san_luis.jpg
  caption: "San Luis, Argentina"
excerpt: 'In this post we will delve into the theory of Bayesian Additive Regression Trees (BART).'
tags:
  - GSoC
  - BART
---


## Introduction

> Conceptually, BART can be viewed as a Bayesian nonparametric approach that fits a parameter rich model using a strongly influential prior distribution.

We want to perform inference about an unknown function $f$ that predicts an output $Y$ of the form:

$$Y = f(x) + \epsilon$$

where:

* $x$ is a $p$ dimensional vector of inputs $x = (x_1, \dots, x_p)$
* $\epsilon \sim \mathcal{N}(0, \sigma^2)$

In order to do this we approximate $f(x)$ as a sum of $m$ trees

$f(x) \approx \sum_{j=1}^m g_j(x)$

Thus we approximate this equation as a sum-of-trees model.


$$Y = \sum_{j=1}^m g_j(x) + \epsilon$$


Where each $g_j$ is of the form

$$g(x; T_j, M_j)$$ 

* $T$ is a binary tree consisting of a set of interior node decision rules and a set of terminal nodes
* $M = \\{\mu_1, \mu_1, \cdots, \mu_b \\}$ denote a set of parameter values associated with each of the $b$ terminal nodes of $T$.

The decision rules are binary splits of the predictor space of the form $\\{x \in A\\}$ vs $\\{x \notin A\\}$ where $A$ is a subset of the range of $x$. These are typically based on the single components of $x = (x_1, \dots , x_p)$ and
are of the form $\\{x_i \leq c\\}$ vs $\\{x_i > c\\} $ for continuous $x_i$.

According to the second equation $\mathbb{E}(Y \mid x)$ equals the sum of all the terminal node parameter $\mu_{ij}$

* When $g(x; T_j, M_j)$ depends on a single variable (only one component of $x$) each such $\mu_{ij}$ represents a main effect
* When $g(x; T_j, M_j)$ depends on more than one variable (more than one component of $x$) each such $\mu_{ij}$ represents an interaction effect.
* Because the trees are allow to have different sizes, the interaction effects may be of varying orders.
* In the special case where every terminal node assignment de-
pends on just a single component of x, the sum-of-trees model reduces to a simple
additive function, a sum of step functions of the individual components of x.


## Priors
There are many ways to generate an ensemble of trees the Bayesian Additive Regression Trees (BART) method makes use of a regularizing prior that induce $g_j$ trees that only are able to explains a small and different portion of $f$. Notice that is different from Bayesian averaging of single trees fitting the entire $f$ function.

Chipman et al. recommend automatic default specifications and proposed a prior formulation in term of just a few interpretable hyperparameters which govern priors on $T_j$, $M_j$ and $\sigma$. When domain information is not available the authors recomend using an _empirical Bayes_ approach and calibrate the prior using the observed variation in $y$. Or at least to obtaing a range of plauisble values and the perform cross-validation to select from these values.

### Prior independence and symmetry

In order to simplyfy the specification of the regularization prior we restrict our attention to priors for which the tree components ($T_j$, $M_j$) are independent of each other and also independent of $\sigma$, and the terminal node parameters of every tree are independent. 

$$p((T_1 , M_1), \dots , (T_m , M_m ), \sigma ) = \left [\prod_j p(T_j , M_j) \right ] p(\sigma) = \left [\prod_j p(M_j \mid T_j) , T_j) \right ] p(\sigma)$$

and

$$p(M_j \mid T_j) = \prod_j p(\mu_{ij} \mid T_j)$$

where $\mu_{ij} \in M_j$$

Under the independence assumption we only need to specify $p(T_j)$, $p(\mu_{ij} \mid T_j)$ and $p(\sigma)$. We can use the same prior forms proposed by Chipman et al. for Bayesian CART.


### Prior for $p(T_j)$

It is specified by three aspects: 


* the probability that a node at depth $d=(0, 1, 2, \dots)$ is nonterminal, given by:
 $$ \frac{\alpha}{(1 + d)^{\beta}} \alpha \in (0, 1), \beta \in [0, \infty)$$
* Node depth is defined as distance from the root. Thus, the root itself has depth 0, its first child node has depth 1, etc.

* the distribution on the splitting variable assignments at each interior node. (uniform on available variables) 
* the distribution on the splitting rule assignment in each interior node, conditional on the splitting variable (uniform on the discrete set of available splitting values).

Chipman et al. recommend using $\alpha = 0.95$ and $\beta = 2$ trees with 1, 2, 3, 4 and $\geq$ 5 terminal nodes receive prior probability of 0.05, 0.55, 0.28, 0.09 and 0.03, respectively. Está bien esto? por que sube y luego baja?

### Prior for $p(\mu_{ij}, T_j)$
For convenience, we implement our specification strategy by first shifting and
rescaling $Y$ so that the observed transformed $y$ values range from $y_{min} = -0.5$ to $y_max = 0.5$, then the prior is 

$$\mu_{ij} \sim N(0, \sigma_{\mu}^2)$$

where $\sigma_{\mu} = \frac{0.5}{k\sqrt{m}}$


This prior has the effect of shrinking the tree parameters $\mu_{ij}$ toward zero, limiting the effect of the individual tree components by keeping them small. Note that as $k$ and/or $m$ is increased, this prior will become tighter and apply greater shrinkage to the $\mu_{ij}$. A value of $k$ between 1 and 3 yield good results.

### The $\sigma$ prior

We used the inverse chi-square distribution

$$\sigma^2 \sim \frac{\nu\lambda}{X_{\nu}^2}$$

Essentially, we calibrate the prior df $\hat \sigma$ and scale $\lambda$ for this purpose using a _rough data-based overestimate_ $\hat \sigma$ $\sigma$. Two natural choices for $\hat \sigma$ are:

* the _naive_ specification, in which we take $\hat \sigma$ to be the sample standard deviation of $Y$
* the _linear model_ specification, in which we take $\hat \sigma$ as the residual standard deviation from a least squares linear regression of $Y$ on the original $X$.

We then pick a value of $\hat \sigma$ between 3 and 10 to get an appropriate shape, and a value of $\lambda$ so that the qth quantile of the prior on $\sigma$ is located at $\hat \sigma$, that is, $p(\sigma < \hat \sigma) = q$. We consider values of $q$ such as 0.75, 0.90 or 0.99 to center the distribution below $\hat \sigma$.


For automatic use, we recommend the default setting ($\nu$, $q$) = (3, 0.90). We recommend against choosing $\nu ν < 3$ because it seems to concentrate too much mass on very small $\sigma$ values, which leads to overfitting.

### The choice of $m$

Instead of being fully Bayesian and estimate the value of $m$ a fast and robust option is to choose $m=200$ and then maybe check a couple of other values ofr differences. As $m$ is increased, starting with $m$ = 1, the predictive performance of BART improves dramatically until at some point it levels off and then begins to very slowly degrade for large values of $m$. Thus, for prediction, it seems
only important to avoid choosing $m$ too small.

For variable selection...





## Sampler
In Chipman et al. they use a a tailored version of Bayesian back-fitting MCMC [@HastieBayesianbackfittingcomments2000] that iteratively constructs and fits successive residuals. It seems that a better approach is using Particle Gibbs for Bayesian Additive Regression Trees [LakshminarayananParticleGibbsBayesian]


## Results/output
The ouput of a BART model is:
* A posterior mean estimate of $f(x) = \mathbb{E}(Y \mid x)$ at any input value $x$ 
* Pointwise uncertainty intervals for $f(x)$
* Point and interval estimates can also be obtained for functionals of $f$, such as partial dependence functions.
* Variable importance meassures. This is done by keeping track of the relative frequency with which $x$ components appear in the sum-of-trees model iterations. the variation of $Y$.





## Comments
A sum-of-trees model is fundamentally an additive model with multivariate
components. Compared to [generalized additive models](https://en.wikipedia.org/wiki/Generalized_additive_model) based on sums of low dimensional smoothers, these multivariate components can more naturally incorporate interaction effects (por que?). And compared to a single tree model, the sum-of-trees can more easily incorporate additive effects [@ChipmanBARTBayesianadditive2010]
