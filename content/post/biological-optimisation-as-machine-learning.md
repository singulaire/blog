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

Since the solution space is discrete, it is by definition not smooth. The smallest step we can take in the space is to change a single character of the string. Even then, the notion of distance is muddy, since there's no notion of distance between different characters in the alphabet. Suppose we want to change the nucleotide in the $212^{th}$ position of a DNA sequence from cytosine to something else. Which of the other three nucleotides is closest? If we think in terms of Hamming distance, they're all equally far. But the Hamming distance, although popular, is by no means a canonical measure of similarity between genes. In fact, it is a well-known phenomenon in genetics that a difference of a single nucleotide (aptly called a "single-nucleotide polymorphism" or "single-nucleotide variant") can result in radically different gene function. At the same time, genes that differ in the majority of their base pairs can have similar functionality [1]. The combination of these two properties means that the utility surface can be extremely misshapen, with long, wide plateaus and sudden dropoffs.

# Representation of Molecules

Although molecules *can* be uniquely and precisely represented as a string, it is generally challenging to learn a fully-connected neural network that maps directly from string representations to molecular properties. These days it is common to use specific NN architectures that embed knowledge from physics and chemistry into the model. For example, it is common in chemistry to represent molecules as graphs, which explicitly encode information about the topology and geometry of the molecule, the relative positions of the constituent atoms, and the types of connections between them. This kind of graph structure is well represented by graph neural networks (GNN), which have been used extensively in this domain [2]. Another important physical insight about molecules is that their properties are invariant to certain [Euclidean group](https://en.wikipedia.org/wiki/Euclidean_group) isometries such as rotation and translation in three-dimensional space. This leads to the use of equivariant graph neural networks [3]. 

*Note: I'm not entirely sold on the logic of using EGNNs for molecules. For one, equivariance and invariance are obviously different in important ways. Moreover, molecules are only invariant to some E(3) isometries, whereas other isometries such as reflection lead to important differences such as chirality. Maybe the idea is that the equivariance only applies to the three-dimensional coordinates of the atoms, but it's unclear whether the model properly separates representation of positional data and representation of other atomic properties.*

# Generative Models

Earlier in this post I talked about optimising molecules by searching through the solution space. While this approach has a long history and can be effective, the existence of learned representations affords us another valuable opportunity: to optimise in the latent space. This puts us squarely in the realm of generative models, i.e. models that can generate data points as opposed to merely predicting the likelihood of one.

A (comparatively) simple example is a variational autoencoder [4]. VAEs have an *encoder* component that maps the input to a latent space and *decoder* that maps from the latent space back to the input space. The latent space is continuous, so we can search through it in a gradient-based way, and any point we pick will map back to the (discrete) input space. Suppose we also had a predictor network that was trained to map the latent space to the value of some property of the corresponding input-space molecule. We could perform SGD with respect to the latent space (rather than with respect to the network weights, as done in training) in order to find the point that maximises the property of interest, and then recover the molecule with the decoder. There are a few problems with this approach (the decoder only approximately inverts the encoder, not every member of the input space may be a valid molecule, the predictor won't be accurate for every point in latent space, etc.), but empirically it has been shown to work rather well [5].

Of course, VAE is only one of many popular generative models in the ML universe. The same principle has also been successfully applied to autoregressive recurrent neural network models [6], transformers [7], flows [3], generative adversarial networks[8] and diffusion models[9].

# Metrics

Thus far I've referred in the abstract to utility functions and properties of molecules that we might seek to optimise.

*Note: this section is incomplete*


# References

[1] Wagner A. *Arrival of the Fittest*, 2014, Penguin Random House.\
[2] Yang, K. et al. *Analyzing learned molecular representations for property prediction*, Journal of Chemical Information and Modeling, 2019.\
[3] Verma, Y. et al. *Modular Flows: Differential Molecular Generation*, Advances in Neural Information Processing Systems, 2022\
[4] Kingma, D. P., & Welling, M. *Auto-encoding Variational Bayes*. arXiv preprint arXiv:1312.6114, 2013.\
[5] Gómez-Bombarelli, R. et al. *Automatic hemical design using a data-driven continuous representation of molecules*, ACS Central Science, 2018.\
[6] Segler, M.H.S. et al. *Generating focused molecule libraries for drug discovery with recurrent neural networks*, ACS Central Science, 2018.\
[7] Bagal, V. et al. *MolGPT: molecular generation using a transformer-decoder model*, Journal of Chemical Information and Modeling, 2021.\
[8] Prykhodko, O. et al, *A de novo molecular generation method using latent vector based generative adversarial network*, Journal of Cheminformatics, 2019.\
[9] Hoogeboom, E. et al. *Equivariant diffusion for molecule generation in 3d*, International Conference on Machine Learning, 2022.\
