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
Also, I have written a serie of [blog posts]({{site.url}}{{site.baseurl}}/tags/#gsoc-2019) where I talk about the Bayesian Additive Regression Trees (BART) model and its implementation.

## Project Abstract

Bayesian Additive Regression Trees is a Bayesian nonparametric approach to estimating functions using regression trees. A BART model consist on a sum of regression trees with normal additive noise. Regression trees are defined by recursively partitioning the input space, and defining a local model in each resulting region of input space in order to approximate some unknown function. BARTs are useful and flexible model to capture interactions and non-linearities and have been proved useful tools for variable selection.

Bayesian Additive Regression Trees will allow PyMC3 users to perform regressions with a “canned” non-parametric model. By simple calling a method, users will obtain the mean regressor plus the uncertainty estimation in a fully Bayesian way. This can be used later to predict on hold-out data. Furthermore, the implemented BART model will allow experience users to specify their own priors for the specific problem they are tackling, improving performance substantially.

## Project status

A lot work was put into the Google Summer of Code.
I read some papers about BART and wrote a lot a code, tests and documentation.
But, sadly we didn't meet all the goals we set in the beginning.
There are still a lot more debugging to do to ensure the model is correct.

The main files for the project are:
- [Tree structure]((https://github.com/jmloyola/pymc3/blob/add_bart/pymc3/bart/tree.py)). Defines the classes `Tree`, `BaseNode`, `SplitNode` and `LeafNode`.
- [BART](https://github.com/jmloyola/pymc3/blob/add_bart/pymc3/bart/bart.py).
- [BART exceptions](https://github.com/jmloyola/pymc3/blob/add_bart/pymc3/bart/exceptions.py). Defines the three classes of errors: `TreeStructureError`, `TreeNodeError`, `BARTParamsError`.
- [Tests for nodes](https://github.com/jmloyola/pymc3/blob/add_bart/pymc3/tests/test_tree_nodes.py).
- [Tests for tree structure](https://github.com/jmloyola/pymc3/blob/add_bart/pymc3/tests/test_tree_structure.py).
- [Tests for BART](https://github.com/jmloyola/pymc3/blob/add_bart/pymc3/tests/test_bart.py).

## Things done

- Community bonding period. During this time I learned more about PyMC and the BART model. I also set up this blog. For more details you can check this blog post: ["Coding period begins"]({{ site.baseurl }}{% post_url 2019-06-09-coding-period-begins %}).
- Understand the BART model. This involved reading many research papers and different implementations. The most relevant papers read were _Chipman et al. (2010)_, _Chipman et al. (1998)_, _Lakshminarayanan et al. (2015)_, _Tan et al. (2019)_ and _Kapelner & Bleich (2013)_. I studied the code for some BART implementations to complement the information given in the papers: [bartpy](https://github.com/JakeColtman/bartpy), [pgbart](https://github.com/balajiln/pgbart) and [bartMachine](https://github.com/kapelner/bartMachine). Finally, some notes I wrote about this process can be seen in this blog:
	- ["Introduction to Bayesian Additive Regression Trees"]({{ site.baseurl }}{% post_url 2019-06-23-introduction-to-bart %}).
	- ["Posterior inference in Bayesian Additive Regression Trees"]({{ site.baseurl }}{% post_url 2019-07-21-posterior-inference-in-bart %}).
- [Implement the tree structure](https://github.com/jmloyola/pymc3/commit/0ddb3dc9584f2b6ca5b45d4b6d33d9de317d3e4f). For details on the choosen tree structure implementation, you can check this blog post: ["BART's tree structure implementation"]({{ site.baseurl }}{% post_url 2019-07-05-bart-tree-structure %}).
- [Implement the visualization of a tree](https://github.com/jmloyola/pymc3/commit/473593013f3864c5d15bc011cc611dac1ec90550).
- Add tests for the [nodes](https://github.com/jmloyola/pymc3/commit/0ddb3dc9584f2b6ca5b45d4b6d33d9de317d3e4f) and the [tree structure](https://github.com/jmloyola/pymc3/commit/473593013f3864c5d15bc011cc611dac1ec90550).
- [Define two APIs for BART](https://github.com/jmloyola/pymc3/commit/9b8c7d2cccc904ffa51a973d3482bf735271575b): one for the conjugate model defined in the original paper of _Chipman et al. (2010)_ and a flexible API for user defined priors and likelihoods.
- [Set the priors for BART](https://github.com/jmloyola/pymc3/commit/43e6072deb30225db1d51126b0cff72130934378).
- [Implement the Bayesian backfitting MCMC for posterior inference in BART](https://github.com/jmloyola/pymc3/commit/50fc9440f7a9c62d0f498291d283ccc5f4b6ba1a).
- [Implement the tree samplers GrowPrune and ParticleGibbs](https://github.com/jmloyola/pymc3/commit/5ecec56692fc7ea335260cd4aa55a47204ab996e). Both these approaches are described in _Lakshminarayanan et al. (2015)_.
- [Add `Particle` class to get a clearer code](https://github.com/jmloyola/pymc3/commit/b5cb5e80cc1f118b7aea91f122f1fa0bde2f1c25).
- [Add tests for BART](https://github.com/jmloyola/pymc3/commit/cbcbf9e5b8ddc0578408e2d303e9134bba8dd4c2#diff-be411b5f778aaf7c0eadc4d0456e7752).
- [Optimize space of data structure for trees](https://github.com/jmloyola/pymc3/commit/d9d8e210ddea0102e6359fb0aeb9579fa5157100).
- [Add documentation to tree structure code](https://github.com/jmloyola/pymc3/commit/1360afa371fe2642e074e441bb1850b07a67ffc3).

## Challenges

During GSoC there were two challenges I faced. For one part implementing a model given in a serie of papers was not as simple as I though at first. There are a lot of small details that are not explained in the papers but that appear while implementing the model. This entailed to examine the implementations of the original papers and reading a lot more papers in order to find the answer. Tasks that proved to be hard because I am not an expert in the topic. On the other hand, I faced the challenge of merging the code I wrote for this model inside the PyMC3 code base. For this I had to dive deep into the code base of PyMC3 and adapt the code I had written. I go in a little more detail in this blog post: ["The coding period is coming to an end"]({{ site.baseurl }}{% post_url 2019-08-01-almost-end-coding-period %}).

## Future work
Although GSoC has finished, I plan to keep working in the project. It is not going to be as intense as during GSoC, but I will dedicate a couple of hours a week to have BART merged in PyMC.

To have an initial version of BART in PyMC we should:
- Add documentation to the rest of the code.
- Ensure the model is correct. Compare our model with other implementations.
- Add jupyter notebook showing the effect of the BART priors.
- Add jupyter notebook showing how BART performs.
- Finish merging the BART code to PyMC3.
- Refactor Tree code. Separate the basic tree structure from the things needed for BART. Add a new class `BARTTree` which add the attributes BART needs.

To extend the model we should:
- Implement another way to calculate variable importance following the paper from _Bleich et al. (2014)_.
- Implement another way to choose the path when the variable has `np.NaN` following the paper from _Kapelner & Bleich (2015)_. For example:
	- Always to the left.
	- Always to the right.
	- To the left or right at random.
	- Every data point with `np.NaN` in that particular variate goes to the left and the rest of the data points to the right. The condition the split node will divide the space is `np.isNan(x)`.
- Single program, multiple data (SPMD) parallelization following the paper from _Pratola et al. (2014)_.
- Optimize time for posterior estimation following the paper from _He et al. (2019)_.


## References
1. Chipman, H. A., George, E. I., & McCulloch, R. E. (2010). BART: Bayesian additive regression trees. *The Annals of Applied Statistics*, *4*(1), 266-298.
2. Chipman, H. A., George, E. I., & McCulloch, R. E. (1998). Bayesian CART model search. *Journal of the American Statistical Association*, *93*(443), 935-948.
3. Lakshminarayanan, B., Roy, D., & Teh, Y. W. (2015). Particle Gibbs for Bayesian additive regression trees. In *Artificial Intelligence and Statistics* (pp. 553-561).
4. Kapelner, A., & Bleich, J. (2013). bartMachine: Machine learning with Bayesian additive regression trees. *arXiv preprint arXiv:1312.2171*.
5. Tan, Y. V., & Roy, J. (2019). Bayesian additive regression trees and the General BART model. *arXiv preprint arXiv:1901.07504*.
6. Pratola, M. T., Chipman, H. A., Gattiker, J. R., Higdon, D. M., McCulloch, R., & Rust, W. N. (2014). Parallel Bayesian additive regression trees. *Journal of Computational and Graphical Statistics*, *23*(3), 830-852.
7. Bleich, J., Kapelner, A., George, E. I., & Jensen, S. T. (2014). Variable selection for BART: an application to gene regulation. *The Annals of Applied Statistics*, *8*(3), 1750-1781.
8. Kapelner, A., & Bleich, J. (2015). Prediction with missing data via Bayesian additive regression trees. *Canadian Journal of Statistics*, *43*(2), 224-239.
9. He, J., Yalov, S., & Hahn, P. R. (2019, April). XBART: Accelerated Bayesian Additive Regression Trees. In *The 22nd International Conference on Artificial Intelligence and Statistics* (pp. 1130-1138).

## Acknowledgments
I will like to thanks Google and NumFOCUS for promoting open-source projects and bringing students closer to open-source communities; PyMC for the warm welcome to the community; and Austin Rochford and Osvaldo Martin for their support as mentors during the development of the project.
