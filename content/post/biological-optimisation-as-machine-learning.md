---
title: "Biological Optimisation as Machine Learning"
draft: false
toc: false
mathjax: true
image: ""
tags: ["biology"]
---


The biotech field has long been in the business of trying to find new molecules or mechanisms that achieve improved function compared with what is readily available in nature. Fundamentally, this can be thought of as an optimisation problem in a very large search space. For machine learning scientists, as soon as we hear "optimisation problem" we immediately think "how can we do it better than anyone else with machine learning?". While ML for biology goes back a long way, 2022 feels like a phase shift in terms of attention. Half the invited talks in ICML were on this subject, and the conference also hosted a workshop on computational biology. Biotech companies ranging from start-ups to industry titans are now signing up as sponsors at the top ML conferences. Suffice to say that it's time for a primer on the subject.

# Laying the Foundation

First up is problem formulation. Since this is about machine learning, it means the problem will be one of optimisation, and hence requires defining an objective and a solution space. This varies considerably depending on the exact subfield in question. For example, we might want to find a gene promoter that maximizes the expression strength of its associated gene, in which case we're looking at the alphabet of DNA base pairs (the 4 infamous nucleotides: Adenine, Guanine, Cytosine, Thymine) and the solution space is all strings of some fixed length or range of lengths (e.g. n=300). Or we might be looking at engineering an enzyme that catalyses a specific chemical reaction at a high rate, meaning that the alphabet is the set of amino acids (d=20). Yet another possibility is that we're designing a drug and seeking to minimise its toxicity, in which case we have an alphabet of common chemical elements (e.g. carbon, hydrogen, nitrogen and oxygen) and structures (e.g. rings, methyl groups). Unlike proteins and DNA, however, specifying general molecules with a string is complicated, since most molecules have a nonlinear graph structure. There are numerous standards for representing molecules as strings (e.g. [SMILES](https://en.wikipedia.org/wiki/Simplified_molecular-input_line-entry_system)), but coding/decoding is rather involved, so this mostly handled by code libraries that practitioners treat as black boxes.

The above examples should demonstrate that the variation is significant and the engineering effort required to formulate the problem is not trivial (although often we can rely on previous art that did not involve machine learning). However, note the commonalities: we have some alphabet of building blocks (nucleotides, amino acids, elements), a solution space defined by all possible strings of the alphabet (possibly with some constraint on string length), and the goal is to find the element of this discrete-yet-large set that maximises some utility.

# Jagged Utility Surface

One of the biggest challenges in biological optimisation arises from the geometry of the utility function. Suppose we have some problem where the solution space has $d$ dimensions. The utility function maps this (discrete) space to a (discrete subgroup of) a $d+1$-dimensional surface. The study of utility surfaces (also called "loss surfaces") is a time-honoured tool in machine learning. Many guarantees about the performance of various ML algorithms rely on certain assumptions about the geometry of the utility surface, such as being lipschitz continuous or having a low density of local optima. Generally speaking, we don't get nice utility surfaces when working in the biological domain.

Since the solution space is discrete, it is by definition not smooth. The smallest step we can take in the space is to change a single character of the string. Even then, the notion of distance is muddy, since there's no notion of distance between different characters in the alphabet. Suppose we want to change the nucleotide in the $212^{th}$ position of a DNA sequence from cytosine to something else. Which of the other three nucleotides is closest? If we think in terms of Hamming distance, they're all equally far. But the Hamming distance, although popular, is by no means a canonical measure of similarity between genes. In fact, it is a well-known phenomenon in genetics that a difference of a single nucleotide (aptly called a "single-nucleotide polymorphism") can result in radically different gene function. At the same time, genes that differ in the majority of their base pairs can have similar functionality [1]. The combination of these two properties means that the utility surface can be extremely misshapen, with long, wide plateaus and sudden dropoffs.

# Representation of Molecules






# References

[1] Wagner A. *Arrival of the Fittest*, 2014, Penguin Random House