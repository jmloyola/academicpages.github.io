---
title: 'Accepted to the Google Summer of Code 2019'
date: 2019-05-15
permalink: /posts/2019/05/gsoc-acceptance
tags: GSoC
---

```python
print('Hello World!')
```

*(As a computer scientist I had to do that :grin:)*

Last week Google announced the accepted students for the Google Summer of Code (GSoC) for this year. Luckily I'm one of those. I will work on PyMC3 under the NumFOCUS umbrella organization. Austin Rochford and Osvaldo Martin, core developers of PyMC3, will be my mentors. The main objective of the project is to [implement Bayesian Additive Regression Trees in PyMC3](https://summerofcode.withgoogle.com/projects/#4666396833742848).

Bayesian Additive Regression Trees (BART) is a Bayesian nonparametric approach to estimating functions using regression trees. A BART model consist of a sum of regression trees with (homoskedastic) normal additive noise. Regression trees are defined by recursively partitioning the input space, and defining a local model in each resulting region of input space in order to approximate some unknown function. BARTs are useful and flexible models to capture interactions and non-linearities and have been proved useful tools for variable selection.

Bayesian Additive Regression Trees will allow PyMC3 users to perform regressions with a “canned” non-parametric model. By simple calling a method, users will obtain the mean regressor plus the uncertainty estimation in a fully Bayesian way. This can be used later to predict on hold-out data. Furthermore, the implemented BART model will allow experience users to specify their own priors for the specific problem they are tackling, improving performance substantially.

In the next few weeks, Community Bonding Period, I will:

- deepen my knowledge about BART;
- examine PyMC3 code-base in order to deepen my knowledge about its data flow, architecture and application programming interface (API);
- communicate with mentors and PyMC3 core developers to clarify doubts about possible API for BART.