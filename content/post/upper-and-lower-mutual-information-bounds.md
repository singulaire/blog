---
title: "Upper and Lower Mutual Information Bounds"
draft: false
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
for some constant scalars $b, d$. For convenience, we will denote $a_i = a(\pi_i); c_i = c(L, \pi_i)$. The differences between the bounds for the two policies are:
$$\mathcal{U}_2 - \mathcal{U}_1 = \log \frac{L \cdot ba_1}{dc_1} - \log \frac{L \cdot a_1}{c_1} = \log \frac{Lba_1}{dc_1} \frac{c_1}{La_1} = \log \frac{b}{d}$$
$$\mathcal{L}_2 - \mathcal{L}_1 = \log \frac{(L+1)ba_1}{ba_1 + dc_1} - \log \frac{(L+1)a_1}{a_1 + c_1} =
        \log \frac{(L+1)ba_1}{ba_1 + dc_1} \frac{a_1 + c_1}{(L+1)a_1} = \log\frac{b (a_1 + c_1)}{ba_1 + dc_1}$$
Now note that we can rewrite the expression for $\mathcal{U}_1$ to get an expression for $a_1$ in terms of the upper bound:
$$\mathcal{U}_1 = \log \frac{La_1}{c_1}$$
$$e^{\mathcal{U}_1} = \frac{La_1}{c_1}$$
$$a_1 = \frac{c_1 e^{\mathcal{U}_1}}{L}$$

plugging this back into the difference between lower bounds gives us:
$$\mathcal{L}_2 - \mathcal{L}_1 = \log\frac{b (\frac{1}{L} c_1 e^{\mathcal{U}_1} + c_1)}{b\frac{1}{L}c_1 e^{\mathcal{U}_1} + dc_1} =
        \log \frac{bc_1 (\frac{1}{L}e^{\mathcal{U}_1} + 1)}{c_1(\frac{1}{L}be^{\mathcal{U}_1} + d)} = 
        \log \frac{ \frac{1}{L}be^{\mathcal{U}_1} + b}{\frac{1}{L}be^{\mathcal{U}_1} + d}$$

Now we subtract $\log \frac{d}{d} = 0$ to both sides of the equation:

$$\mathcal{L}_2 - \mathcal{L}_1 - 0 = \log \frac{\frac{1}{L}be^{\mathcal{U}_1} + b}{\frac{1}{L}be^{\mathcal{U}_1} + d} - \log \frac{d}{d}$$
$$\mathcal{L}_2 - \mathcal{L}_1 = \log \frac{\frac{1}{L}be^{\mathcal{U}_1} + b}{\frac{1}{L}be^{\mathcal{U}_1} + d} \frac{\frac{1}{d}}{\frac{1}{d}}$$
$$\mathcal{L}_2 - \mathcal{L}_1 = \log \frac{ \frac{1}{L}\frac{b}{d} e^{\mathcal{U}_1} + \frac{b}{d} }{ \frac{1}{L}\frac{b}{d} e^{\mathcal{U}_1} + \frac{d}{d} }$$

Looking at the difference between upper bounds, we have that $\frac{b}{d} = e^{\mathcal{U}_2 - \mathcal{U}_1}$, therefore

$$\mathcal{L}_2 - \mathcal{L}_1 = \log \frac{ \frac{1}{L} e^{\mathcal{U}_2 - \mathcal{U}_1} e^{\mathcal{U}_1} + e^{\mathcal{U}_2 - \mathcal{U}_1} }{ \frac{1}{L} e^{\mathcal{U}_2 - \mathcal{U}_1} e^{\mathcal{U}_1} + 1 }$$
$$\mathcal{L}_2 - \mathcal{L}_1 = \log \frac{ \frac{1}{L} e^{\mathcal{U}_2} + e^{\mathcal{U}_2 - \mathcal{U}_1} }{ \frac{1}{L} e^{\mathcal{U}_2} + 1 }$$
Which gives us an expression for the difference $\mathcal{L}_2 - \mathcal{L}_1$ purely in terms of the corresponding upper bounds.

Now, we note that if $\mathcal{U}_2 > \mathcal{U}_1$, then $e^{\mathcal{U}_2 - \mathcal{U}_1} > 1$ and therefore

$$\mathcal{L}_2 - \mathcal{L}_1 = \log \frac{ \frac{1}{L} e^{\mathcal{U}_2} + e^{\mathcal{U}_2 - \mathcal{U}_1} }{ \frac{1}{L} e^{\mathcal{U}_2} + 1 } > 0$$
which implies that $\mathcal{L}_2 > \mathcal{L}_1$. The procedure can be reversed to show that $\mathcal{L}_2 > \mathcal{L}_1 \implies \mathcal{U}_2 > \mathcal{U}_1$, thus proving the first claim of the theorem.

Similarly, we can note that if $\mathcal{U}_2 = \mathcal{U}_1$ then $e^{\mathcal{U}_2 - \mathcal{U}_1} = 1$, which by the same procedure as before shows that $\mathcal{U}_2 = \mathcal{U}_1 \implies \mathcal{L}_2 = \mathcal{L}_1$, and again we can reverse the procedure to show that $\mathcal{L}_2 = \mathcal{L}_1 \implies \mathcal{U}_2 = \mathcal{U}_1$, thus proving the second claim of the theorem

Q.E.D.

So what does this all mean? In theory, we can maximise the sNMC upper bound, and this indirectly maximise the sPCE lower bound, which we hope will drive up the actual mutual information. In fact, the two optimisations are in some sense equivalent, as increasing either bound must increase the other by a predictable, deterministic amount. In practice, my experience is that both approaches perform equally well.



References:

Foster, A., Ivanova, D. R., Malik, I., & Rainforth, T. "Deep adaptive design: Amortizing sequential bayesian experimental design." International Conference on Machine Learning. PMLR, 2021.