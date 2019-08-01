---
title: 'The coding period is coming to an end'
date: 2019-08-01
permalink: /posts/2019/08/almost-end-coding-period
excerpt: 'In this post we will reflect on what we been doing for GSoC and some of the difficulties that arose.'
tags: GSoC
---

We already passed two thirds of the coding period for GSoC, with only two weeks left.
It is been a tremendous experience and I have learned a lot so far, both in programming and in statistics.
Nevertheless, I still consider myself a newcomer when it comes to Bayesian statistics.
There is still a lot to learn.
A big part of what I learned is thanks to the problems we encountered.

In this post we will reflect on what we been doing for GSoC and some of the difficulties that arose.

## What have we done until now?

During the coding period, I would say that the time I spend was equally distributed between understanding the model and implementing it.
With respect to the implementation, I tried to always think ahead of time for possible errors or user frictions with the API.

I also paid attention to the performance of the implementation, so that this model is competitive with others already existing in PyMC
For example, while implementing the function `get_available_predictors()`, two different approaches came to my head.
One that uses `numpy.unique()` to get the unique values of a predictor and if that value is greater than two add it to the list of available predictors.
And another that loops through the dataset only looking at one particular variate at a time, when two different values are observed, the predictor is added to list and the iteration breaks.
This last one was based on the implementation from [bartMachine](https://github.com/kapelner/bartMachine).
In these cases, I tested both and only kept the fastest one, which for the `get_available_predictors()` function was the second one.

Nevertheless, I didn't thought much about the parallelization of the implementation.
For this, we could take advantage of the GPU or multithreading architectures like Patrola et al. (2014) did.
This could be implemented once the model is stable.
It might be a good project for another Google Summer of Code.

Next is a brief summary of tasks done so far:

- Understand the Bayesian additive regression trees model. This involved reading many research papers and different implementations.
- Implement the tree structure.
- Implement the visualization of a tree.
- Add tests for the nodes and the tree structure.
- Define the API for BART.
- Implement the setting of the priors.
- Derive and implement the posterior distributions for $\mu_{ij}$ and $\sigma^2$ in BART.
- Implement the Bayesian backfitting MCMC for posterior inference in BART.
- Update PyMC documentation. While I was reading the documentation of PyMC, I encountered some minor errors. Thus, I made some pull requests to fix them: [#3533](https://github.com/pymc-devs/pymc3/pull/3533) and [#3537](https://github.com/pymc-devs/pymc3/pull/3537).

## Difficulties

There were two issues that were relatively difficult for me, one related to the theory of BART and the other related to the implementation:

- Posterior distributions for $\mu_{ij}$ and $\sigma^2$ in BART.
- PyMC API for BART.

### Posterior distributions for $\mu_{ij}$ and $\sigma^2$ in BART

This project made me realize that to fully understand a model, the ins and outs of it, one should read the literature, the implementation and try to implement it oneself.
While trying to implement something, one realizes that there are details that are not explained in the papers.
In this case, this may be due to my ignorance.
Thus, when I started implementing the Bayesian backfitting MCMC algorithm for posterior inference in BART, I figured out that what seemed simple while reading Chipman et al. (2010) was not so clear when implementing it.

First, in the paper, the authors comment that the draws of $M_j$ for the posterior distribution is *simply* (:neutral_face: -something similar happened to me with some proofs that were skipped in Bishop's book for being *straightforward*-) a set of independent draws of the terminal node $\mu_{ij}$'s from a normal distribution. But, __what parameters should this normal distribution have?__
Something similar happened with the posterior distribution for the $\sigma^2$ parameter.

I was clueless. To find the answer, I studied the code of four BART implementations: [bartpy](https://github.com/JakeColtman/bartpy), [pgbart](https://github.com/balajiln/pgbart), [bartMachine](https://github.com/kapelner/bartMachine) and [BayesTree](https://github.com/cran/BayesTree).
This wasn't easy either because everyone used a different parametrization of the distributions which ended in a different expression for the posteriors.

We ended up choosing one implementation and following it.
Since *bartpy* was the clearest implementation we picked it.
But, I wasn't sure the implementation was correct.
Later, I found a paper from Tan et al. (2019) where they derived these posteriors, confirming the validity of the implementation.

If we use the conjugate priors for $\mu_{ij}\mid T_j$ and $\sigma^2$ as in Chipman et al. (2010), the posterior distributions can be obtain analytically. Tan et al. (2019) derived these two expressions in the following manner.

Let $R_{ij}=(R_{ij1}, \ldots, R_{ij\eta_i})^T$ be a subset from $R_j$ where $\eta_i$ is the number of $R_{ijh}$s allocated to the terminal node with parameter $\mu_{ij}$ and $h$ indexes the subjects allocated to the terminal node with parameter $\mu_{ij}$. Note that $R_{ijh} \mid g(X_{ijh}, T_j, M_j),\sigma \sim \mathcal{N}(\mu_{ij}, \sigma^2)$ and $\mu_{ij} \mid T_j \sim \mathcal{N}(\mu_{\mu}, \sigma_{\mu}^2)$. Then the posterior distribution of $\mu_{ij}$ is given by

\begin{equation}
\begin{split}
p(\mu_{ij} \mid T_j, \sigma, R_j) &\propto p(R_{ij} \mid T_j, \mu_{ij}, \sigma) \, p(\mu_{ij} \mid T_j)\\\\ &\propto \exp \left[ - \frac{\left( \mu_{ij}-\frac{\sigma_{\mu}^2 \sum_{h} R_{ijh} + \sigma^2 \mu_{\mu}} {\eta_i \sigma_{\mu}^2 + \sigma^2} \right)^2 } {2 \frac{\sigma^2 \sigma_{\mu}^2}{\eta_i \sigma_{\mu}^2 + \sigma^2} } \right]
\end{split}
\end{equation}

Finally, let $Y = (Y_1, \ldots, Y_n)^T$ and $k$ index the subjects $k=1,\ldots,n$. With $\sigma^2 \sim \frac{\nu\lambda}{\chi_{\nu}^2}$, or using the Inverse Gamma reparametrization, $\sigma^2 \sim IG(\frac{\nu}{2},\frac{\nu \lambda}{2})$, we obtain the posterior draw of $\sigma$ as follows

\begin{equation}
\begin{split}
p(\sigma^2 \mid \\{T_j, M_j\\}\_{j=1}^m, Y) \propto& \, p(Y \mid \\{T_j, M_j\\}\_{j=1}^m, \sigma) \, p(\sigma^2)\\\\ =& \, (\sigma^2)^{-(\frac{\nu + n} {2} +1)}\\\\ & \,\exp \left[ - \frac{\nu \lambda + \sum_{i=1}^n (Y_i - \sum_{j=1}^m g(X_i,T_j,M_j))^2} {2 \sigma^2} \right]
\end{split}
\end{equation}

### PyMC API for BART

Or, in other words, how we fit the code for BART in the PyMC code-base.
For this, I examined PyMC's code-base in order to deepen my knowledge about its data flow, architecture and API.
The API design was the most discussed topic with my mentors, since we wanted little friction for the user.
Because they use PyMC frequently and have the ability to spot weirdly written functions, they provided valuable feedback.

We brainstormed and arrived to a beta version of the API for the model presented in Chipman et al. (2010).
This implementation only allowed for a normal likelihood and conjugate priors, and calculated the posterior for the parameters using analytical derivations.

But, this is not how PyMC is.
The API should be flexible for the user.
If she wants to use some other likelihood or others priors, she should be able to.
PyMC should take care of everything and arrive to the posterior of the parameters given the data.
With this part I am still struggling, since it requires two things I still can't figure out how to do:

- Deduce the likelihood of the model when it is not explicitly given.
- Calculate the posterior distribution of the parameters with not conjugate priors.

We have draft implementation, but there is still a lot of work to do.

## References
1. Chipman, H. A., George, E. I., & McCulloch, R. E. (2010). BART: Bayesian additive regression trees. *The Annals of Applied Statistics*, *4*(1), 266-298.
2. Tan, Y. V., & Roy, J. (2019). Bayesian additive regression trees and the General BART model. *arXiv preprint arXiv:1901.07504*.
3. Pratola, M. T., Chipman, H. A., Gattiker, J. R., Higdon, D. M., McCulloch, R., & Rust, W. N. (2014). Parallel Bayesian additive regression trees. *Journal of Computational and Graphical Statistics*, *23*(3), 830-852.
