---
title: 'Coding period begins'
date: 2019-06-09
permalink: /posts/2019/06/coding-period-begins
tags: GSoC
---

At this point the bonding period already ended and we are in the middle of the coding period. In this post I will tell you how things are going.

## Community bonding period

The community bonding period is a time for students to learn about the organization's processes, developer interactions, codes of conduct, set up environment, etc. Since I already contributed to PyMC3, I had my working environment set up by now. Thus, I used this time to organize the project, talk to my mentors and learn more about the community.

In this period, Austin, Osvaldo and me had an online meeting to introduce ourselves. We talked about the project and coordinated future meetings. Since Osvaldo and me live in the same city, we arranged to meet in person every week. Then, every three weeks or so, Austin,  Osvaldo and me will meet online to discuss questions or problems about the project. I shared a Trello board with my mentors to show them what task I'm working on.

Furthermore, I was added to the PyMC's Slack group. Here, I introduced myself to the rest of the developers and receive a warm welcome from them. Casually, that week they had organized a journal club to discuss a paper, so they invited me to join in. Colin Carroll commented the paper [NeuTra-lizing Bad Geometry in Hamiltonian Monte Carlo Using Neural Transport](https://arxiv.org/abs/1903.03704). Since I didn't had time to read the paper beforehand and my knowledge of Bayesian statistics is not that great, I didn't understand everything :sweat_smile:. Nonetheless, it was a great experience to hear everyone discuss the topic.

In these weeks, I also set up this blog using a GitHub page template called [academic pages](https://academicpages.github.io/), a fork from [minimal mistakes](https://mmistakes.github.io/minimal-mistakes/).

Finally, I started reading about the main topic of the project, Bayesian Additive Regression Trees (BART).

## Coding period

In May 27th the coding period started. The primary focus for the beginning of this period was to fully understand the Bayesian Additive Regression Trees model. Thus, I read in depth these papers:

- Chipman, H. A., George, E. I., & McCulloch, R. E. (1998). [Bayesian CART model search](https://www.tandfonline.com/doi/abs/10.1080/01621459.1998.10473750). Journal of the American Statistical Association, 93(443), 935-948.
- Chipman, H. A., George, E. I., & McCulloch, R. E. (2010). [BART: Bayesian additive regression trees](https://projecteuclid.org/euclid.aoas/1273584455). The Annals of Applied Statistics, 4(1), 266-298.
- Lakshminarayanan, B., Roy, D., & Teh, Y. W. (2015, February). [Particle Gibbs for Bayesian additive regression trees](http://proceedings.mlr.press/v38/lakshminarayanan15.pdf). In Artificial Intelligence and Statistics (pp. 553-561).
- Tan, Y. V., & Roy, J. (2019). [Bayesian additive regression trees and the General BART model](https://arxiv.org/abs/1901.07504). arXiv preprint arXiv:1901.07504.

I also skimmed through papers that went more in-depth with the theoretical analysis of BART and its implementation:

- Rockova, V., & Saha, E. (2018). [On theory for BART](https://arxiv.org/abs/1810.00787). arXiv preprint arXiv:1810.00787.
- Kapelner, A., & Bleich, J. (2013). [bartMachine: Machine learning with Bayesian additive regression trees](https://arxiv.org/abs/1312.2171). arXiv preprint arXiv:1312.2171.

After reading these papers, I now have deeper understanding of BART but I still lack from a clear vision of the inference process. Surely, these will fade as I start implementing the model.

While working on these, a new stable version of PyMC was released, [PyMC 3.7](https://github.com/pymc-devs/pymc3/releases/tag/v3.7). The new release featured code I contributed with in past PRs ([#3389](https://github.com/pymc-devs/pymc3/pull/3389), [#3427](https://github.com/pymc-devs/pymc3/pull/3427)), namely the `Data` class. This new feature was highlighted in a [blog post](https://medium.com/@pymc_devs/pymc-3-7-making-data-a-first-class-citizen-7ed87fe4bcc5?sk=2e984396bd3c540bfdafdc8842becf38) written by Chris Fonnesbeck where I am directly mentioned :flushed:. I felt super happy because the work I had done was distinguished, but at the same time I felt an enormous weight over my shoulders. The new class was going to be used by more people than me and in many different ways I didn't plan to, possibly finding new issues. So it happened. I felt I needed to solve all these problems but I didn't have time for it right now. Osvaldo calmed me down and reminded me that this is an open source project and that although I may not have time right now, another person can step in and fix those problems. This reassured me and helped me focus on GSoC.

Finally, I dug into other already existing BART implementations in Python like [bartpy](https://github.com/JakeColtman/bartpy) and [pgbart](https://github.com/balajiln/pgbart) to organize the project and better understand the model. I also checked the [scikit-learn's tree implementation](https://github.com/scikit-learn/scikit-learn/blob/7b136e92acf49d46251479b75c88cba632de1937/sklearn/tree/_tree.pyx#L504) for ideas on the tree implementation.

In the next blog post I will introduce the Bayesian Additive Regression Trees model. Till next time...