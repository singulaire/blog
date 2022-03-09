---
title: "Upper and Lower Mutual Information Bounds"
draft: true
toc: false
mathjax: true
image: ""
tags: ["mutual information", "design of experiments"]
---


Recently Foster et al (2020) introduced a couple of tractable bounds that can be used to estimate the expected information gain (EIG) of a *design policy* (a mapping from past designs and observations to the next design). These include the *sequential Prior Contrastive Estimate* (sPCE) lower bound:

$$
\mathcal{L}_T(\pi) = \mathbb{E} \left[ \log \frac{p(h_T | \theta_0, \pi)}{\frac{1}{L+1} \sum_0^L p(h_T | \theta_l, \pi)} \right]
$$

where $h_T = (d_0, y_0,\dots,d_T,y_T)$ is the experimental history at time $T$, $\pi$ is the design policy, $L$ is the number of *contrastiv samples* and $\theta$ parameterises the experimental model $p(y|\theta, d)$ that maps designs to a distribution over outcomes. The expectation in the sPCE is w.r.t. the distributions $\theta_0, h_T \sim p(\theta) \prod_{t=1}^{T}p(y_t | \theta, \pi(h_{t-1})$ and $\theta_{1:L} \sim p(\theta)$ where $p(\theta)$ is some prior.

We also have the corresponding *sequential Nested Monte Carlo* (sNMC) upper bound:

$$
\mathcal{U}_T(\pi) = \mathbb{E} \left[ \log \frac{p(h_T | \theta_0, \pi)}{\frac{1}{L} \sum_1^L p(h_T | \theta_l, \pi)} \right]
$$

where the only difference is that we don't include $l=0$ in the denominator. Since the objective is usually to maximise EIG, the intuitive approach would be to maximise the lower (and hope that it actually drives the EIG up, rather than just tightening the bound). However, as is typical for mutual information lower bounds, the sPCE is upper-bounded by $\text{sPCE} \le \log(L+1)$ which you can verify for yourself by noting that $p(h_T | \theta_0, \pi)$ appears in both the numerator and denominator, and remembering that probabilities are always non-negative. That means if the actual EIG is large, we would need an exponentially large $L$ to estimate it accurately.

This might lead you to wonder if we could use the upper bound instead, which does not suffer the same limitation. Intuitively it doesn't make much sense to maximise a quantity by maximising an upper bound, because we might just be making the bound arbitrarily loose. But it turns out there is a monotonicity property connecting the sPCE and sNMC.

Let $\pi_1, \pi_2$ be two design policies, and for simplicity we will write $\mathcal{L}_1, \mathcal{L}_2$ and $\mathcal{U}_1, \mathcal{U}_2$ to denote the lower and upper bounds of those policies as estimated with arbitrary $L$ and $T$.

**Theorem 1** *the sPCE lower bound and sNMC upper bound induce the same total order on policy space:*
$$\mathcal{U}_2 > \mathcal{U}_1  \iff \mathcal{L}_2 > \mathcal{L}_1 \quad \forall \pi_1,\pi_2 \in \Pi$$
$$\mathcal{U}_2 = \mathcal{U}_1  \iff \mathcal{L}_2 = \mathcal{L}_1 \quad \forall \pi_1,\pi_2 \in \Pi$$

**Proof** to simplify the following analysis, we introduce the notation:
$$a(\pi) = p(h_T | \theta_0, \pi) \quad\quad c(L, \pi) = \sum_1^L p(h_T | \theta_l, \pi)$$
this lets us rewrite the bounds as follows, where we omit the dependency of $a, c$ on $L$ and $\pi$ for brevity:
$$\mathcal{L}(L, \pi) = \log  \frac{(L+1) a}{a+c} \quad\quad \mathcal{U}(L, \pi) =  \log \frac{L \cdot a}{c}$$
Now suppose there exist policies $\pi_1$ and $\pi_2$ s.t.
$$a(\pi_2) = b \cdot a(\pi_1) \quad\quad c(L, \pi_2) = d \cdot c(L, \pi_1)$$
for some constant scalars $b, d$



References:

Foster, A., Ivanova, D. R., Malik, I., & Rainforth, T. "Deep adaptive design: Amortizing sequential bayesian experimental design." International Conference on Machine Learning. PMLR, 2021.