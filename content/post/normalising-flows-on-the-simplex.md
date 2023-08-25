---
title: "Normalising Flows on the Simplex"
draft: false
toc: false
mathjax: true
image: ""
tags: ["normalising flows", "inference"]
---

Recently I've run into an interesting phenomenon when trying to use conditional normalising flows: sometimes they predict the posterior more accurately than the true posterior! Specifically, if we consider $q(\theta | y)$ to be the posterior over $\theta$ predicted by a normalising flow in response to input $y$, where the true posterior is $p(\theta | y)$, then we sometimes get the result that:

$$
\mathbb{E}_{p(\theta)p(y | \theta)} \left[ q(\theta | y) \right] > \mathbb{E}_{p(\theta)p(y | \theta)} \left[ p(\theta | y) \right].
$$

In other words, the true value of the RV $\theta$ has higher likelihood under the NF posterior distribution then under the true posterior (in expectation), even though $\theta$ is sampled from the prior.

Mathematically, this is impossible: the relative information between two RVs (left-hand side) can't be greater than the self information (right-hand side). Intuitively, it makes no sense: how can the NF consistently assign higher likelihood to the true value of $\theta$ than the actual posterior? Surely the equations were implemented incorrectly, or the sampling is being done wrong, or I'm just missing some correction term.

The issue, as it turns out, is quite subtle. Some additional investigation shows that the same error does not occur in the case where $p(\theta)$ is a conjugate Gaussian prior (i.e. $p(y | \theta)$ is Gaussian and consequently $p\theta | y)$ is also Gaussian and has a closed-form expression), but *does* occur if $p(\theta)$ is a conjugate Dirichlet prior (i.e. $p(y | \theta)$ is categorical and consequently $p\theta | y)$ is a Dirichlet and has a closed-form expression). This suggests that the issue isn't a general bug in the sampling logic or the change-of-variable equations, but rather there's an error term that is zero for the Gaussian case and non-zero for the Dirichlet case.

The issue seems to arise from the fact that a Dirichlet RV is distributed on the $k-1$ probability simplex, which is a $k-1$-dimensional manifold embedded in a $k$-dimensional space. That's a fancy way of saying that, since the $k$ elements of a Dirichlet RV add up to $1$, each element is fully determined by the remaining $k-1$ elements. A standard NF or conditional NF doesn't capture this fact. The support of a NF is typically the $\mathcal{R}^k$ space where $k$ is the number of dimensions, and so it has an extra degree of freedom that contributes to the final likelihood calculation (usually in an additive fashion). What's worse is that, since the final element of the RV is fully determined by the others, we can have very high confidence about it, so the contribution to the likelihood will be particularly outsized.

The most obvious way to fix this is to have the NF predict only the first $k-1$ dimensions of any RV distributed on the probability simplex. Indeed, when I plug this back into the conjugate prior example mentioned above, the NF converges to predicting a cross-entropy (LHS of the equation at the start of this text) that is just slightly below the posterior entropy (RHS), which is what we would expect from a well-fitted model.

An alternative, and perhaps more principled approach, would be to add a final transformation on top of the NF that projects the output from $\mathcal{R}^k$ to $\delta_k$, but I haven't tried this and haven't yet developed the mathematical derivation to show why it should work.