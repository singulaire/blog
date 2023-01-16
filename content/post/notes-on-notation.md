---
title: "Notes on Notation"
draft: false
toc: false
mathjax: true
image: ""
tags: ["notation"]
---


This post is a living document of various ideas on mathematical notation that I think are interesting.

## Tuple or Set?

I often encounter the situation where I have a collection of mathematical objects whose ordering doesn't matter. The default is usually to pronounce such a collection to be a *tuple*, and it's common to hear people colloquially refer to any collection as a (n-)tuple even if ordering is irrelevant. For example, if you're computing a Monte Carlo estimate of an expectation, you typically have a collection of samples and calculate their average, but how should you write this?
$$ \frac{1}{N} \sum_{i=1}^N x_i \quad \text{or} \quad \frac{1}{|S|} \sum_{s \in S} s \text{ ?}$$
The left form treats the samples as a tuple or vector, possessed of an order. But since addition is associative and commutative, we can permute the order of the samples however we like without changing the sum. Hence the form on the right, which treats the samples as a set is also "correct"

Tuples are fundamentally ordered, and sets conjure the idea of functions like partition, Cartesian product, and the power set. I still haven't found a satisfactory notation for "collection of items that exist to serve as input for a scalarisation function".


## Products in Expectations

It's very common that I have to write an expectation over an indexed collection of variables. For example, in reinforcement learning you typically have a sequence of actions $a_t$ and states $s_t$ indexed by time. Simply writing out the expectation with generic indexes, i.e. $\mathbb{E}_{a_t} $ isn't quite correct since the distribution of variables with a higher index may depend on the realisation of variables with a lower index.

The most absolutely correct way to write out these expectations is to enumerate all the random variables, which is impossible if the maximum value of the index is not known or meant to be generic. We can use ellipsis notation, e.g. $\mathbb{E}_{a_0, a_1,\dots,a_t}$ but that gets very cumbersome.

Recently I've started using product notation inside the expectation, e.g. $\mathbb{E}_{\prod_{t=0}^H a_t}$ which is both correct and compact and doesn't elide any important details like dependencies between RVs with subsequent indices.

## Canceling Out

When writing out mathematical derivations by hand, I follow the common practice of denoting terms that cancel out by crossing them with a diagonal line. This makes the notation easier to follow, even if I'm reading my own work from just a couple of days ago. However, in ML papers I've almost never seen this done, and sometimes its hard to figure out how the authors got from one equation to the next because of non-obvious cancellations mixed in with other operations.

The [cancel package](https://ctan.org/pkg/cancel?lang=en) makes it easy to use the cross-out notation by simply wrapping the expression to be crossed out with a `\cancel{}` statement. If you have multiple cancellations in a single step, and you want to make it very obvious which term is cancelled out by which other term, you can highlight paired terms by inserting a `\color{}` statement *inside* the appropriate `\cancel{}` statement. Alternatively you can highlight a term with background colours by using the `\colorbox` command from the [xcolor](https://ctan.org/pkg/xcolor?lang=en) package.