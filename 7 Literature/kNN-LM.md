---
tags: [literature]
status: draft
created: 2026-06-26
updated: 2026-06-26
authors: Urvashi Khandelwal, Omer Levy, Dan Jurafsky, Luke Zettlemoyer, Mike Lewis
year: 2020
venue: ICLR 2020
url: https://arxiv.org/abs/1911.00172
arxiv: 1911.00172
---

# Generalization through Memorization: Nearest Neighbor Language Models (kNN-LM)

**TL;DR:** The origin of the nonparametric-memory thesis. Augment a **pretrained, frozen**
LM at inference with a **token-level kNN datastore** over the training set (keys = context
representations, values = next tokens), interpolating the kNN distribution with the base
LM — **no extra training**. The LM precursor that [[kNN-MT]] adapts to translation.
*(bibliography confirmed by direct fetch)*

## Relevance to BabyMT
The conceptual root of [[2026-06-26 Path-2 budget allocation study|path-2]]: its title
literally frames retrieval as *generalization through memorization* — spending capacity on
a lookup store instead of weights. BabyMT inherits the mechanism but (a) targets
**translation** not language modeling, (b) uses **sentence-level TM and dictionary**
retrieval rather than token-level interpolation, and (c) imposes a **fixed total budget**
that kNN-LM never considers.

## Method
Build a datastore of `(context representation → next token)` pairs over the training data.
At inference, query the current context's representation, retrieve k nearest neighbours,
softmax over (negative) distances to form `p_kNN`, and interpolate
`(1−λ)·p_LM + λ·p_kNN`. The datastore requires no model training.

## Key results
- Adding the datastore improves language modeling (lower perplexity) with no retraining;
  scaling the datastore keeps improving results.
- Demonstrates that **retrieving over the training set can beat training on it** —
  memorization via lookup generalizes better than memorization via parameters in places.

## Takeaways / how we differ
Foundational citation for "retrieval as nonparametric memory." BabyMT tests whether the
same substitution holds for **MT at BabyLM scale under a budget** — and at the token vs
sentence granularity that distinguishes C2 from kNN-LM/kNN-MT.

Related: [[kNN-MT]] · [[RETRO]] · [[MassiveDS]] · [[To Memorize or to Retrieve]] · [[Literature Synthesis & Matrix]]
