---
title: 'GSoC 2019 Final Evaluation'
date: 2019-08-22
permalink: /posts/2019/08/gsoc-2019-final-evaluation
excerpt: 'Review of the things done during GSoC 2019.'
tags: GSoC-2019
---
The Google Summer of Code 2019 has come to an end.
It has been an amazing experience!
I would recommend it to anyone that is interested in participating in an open source project.

In what follows, I will present a summary of the work done for the Google Summer of Code project ["Bayesian Additive Regression Trees in PyMC3"](https://summerofcode.withgoogle.com/projects/#4666396833742848). I will go over key points of the project and provide links to commits that show the current state of the implementation. Along the way, I will point out different challenges encountered and future work to be done.

All the code produced during GSoC 2019 is available in [this repository of GitHub](https://github.com/jmloyola/pymc3/tree/add_bart).
Also, I have written series of [blog posts]({{site.url}}{{site.baseurl}}/tags/#gsoc-2019) where I talk about the Bayesian Additive Regression Trees (BART) model and its implementation.

## Project Abstract

Bayesian Additive Regression Trees (BART) is a Bayesian nonparametric approach to estimating functions using regression trees. A BART model consist on a sum of regression trees with normal additive noise. Regression trees are defined by recursively partitioning the input space, and defining a local model in each resulting region of input space in order to approximate some unknown function. BARTs are useful and flexible model to capture interactions and non-linearities and have been proved useful tools for variable selection.

Bayesian Additive Regression Trees will allow PyMC3 users to perform regressions with a “canned” non-parametric model. By simple calling a method, users will obtain the mean regressor plus the uncertainty estimation in a fully Bayesian way. This can be used later to predict on hold-out data. Furthermore, the implemented BART model will allow experience users to specify their own priors for the specific problem they are tackling, improving performance substantially.

## Project status

Sadly we didn't meet all the goals we set in the beginning.


## Things done
- Understand the Bayesian additive regression trees model. This involved reading many research papers and different implementations.
- Implement the tree structure.
- Implement the visualization of a tree.
- Add tests for the nodes and the tree structure.
- Define the API for BART.
- Implement the setting of the priors.
- Derive and implement the posterior distributions for $\mu_{ij}$ and $\sigma^2$ in BART.
- Implement the Bayesian backfitting MCMC for posterior inference in BART.
- Update PyMC documentation. While I was reading the documentation of PyMC, I encountered some minor errors. Thus, I made some pull 

## Challenges

## Future work

- Refactor Tree code. Separate the basic tree structure from the things needed for BART. Add a new class `BARTTree`.
- Implement another way to calculate variable importance.
- Implement another way to choose the path when the variable has `np.NaN`. For example:
	- Always to the left.
	- Always to the right.
	- To the left or right at random.
	- Every data point with `np.NaN` in that particular variate goes to the left and the rest of the data points to the right. The condition the split node will divide the space is `np.isNan(x)`.
- Allow for user defined `TreeSampler`.
- Single program, multiple data (SPMD) parallelization.


## References
1. Chipman, H. A., George, E. I., & McCulloch, R. E. (2010). BART: Bayesian additive regression trees. *The Annals of Applied Statistics*, *4*(1), 266-298.
2. Tan, Y. V., & Roy, J. (2019). Bayesian additive regression trees and the General BART model. *arXiv preprint arXiv:1901.07504*.
3. Pratola, M. T., Chipman, H. A., Gattiker, J. R., Higdon, D. M., McCulloch, R., & Rust, W. N. (2014). Parallel Bayesian additive regression trees. *Journal of Computational and Graphical Statistics*, *23*(3), 830-852.

## Acknowledgments
I will like to thanks Google and NumFOCUS for promoting open-source projects and bringing students closer to open-source communities; PyMC for the warm welcome to the community; and Austin Rochford and Osvaldo Martin for their support as mentors during the development of the project.

