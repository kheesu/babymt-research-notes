---
tags: [literature]
status: draft
created: 2026-06-26
updated: 2026-06-26
authors: Karan Singh, Michael Yu, Varun Gangal, Zhuofu Tao, Sachin Kumar, Emmy Liu, Steven Y. Feng
year: 2026
venue: arXiv (cs.CL), Apr 2026
url: https://arxiv.org/abs/2604.00715
arxiv: 2604.00715
---

# To Memorize or to Retrieve: Scaling Laws for RAG-Considerate Pretraining

> [!important] Closest prior work to BabyMT's path-2 — read first, differentiate explicitly
> This paper studies **the same tradeoff BabyMT studies** (pretraining-corpus-size vs
> retrieval-store-size under constrained resources), at **overlapping model scale
> (30M–3B)**. It is the primary point of departure for the related-work section.

**TL;DR:** *"Systematically study the trade-off between pretraining corpus size and
retrieval store size across a wide range of model and data scales."* Builds a
**three-dimensional scaling framework**; retrieval consistently helps, but marginal utility
**depends on model scale, task type, and degree of pretraining saturation.** *(finding
confirmed by direct fetch; numbers not independently re-verified)*

## Relevance to BabyMT
This **is** the path-2 question at large scale. BabyMT's defensible niche vs. this work:

| Axis | Singh et al. 2026 | BabyMT |
|---|---|---|
| Task | General LM (DCLM) | **Machine translation** |
| Languages | English | **Distant CJK + English** (ko/ja/zh/en) |
| Budget | Open scaling curves (1–150× params; store 1–20×) | **Single fixed 100M *total* (BabyLM) cap** |
| Retrieval | Generic chunk retrieval | **Dictionary (C1) + sentence-TM (C2)** |
| Scale | 30M–3B (OLMo-2) | ~42M from scratch |

Their finding that utility **depends on pretraining saturation** is *most* relevant exactly
where BabyMT lives — the small, **undertrained** regime — which doubles as the open
empirical question BabyMT can answer ([[Open Questions]]).

## Method
OLMo-2-based models from **30M to 3B** params; pretraining up to **100B tokens** (DCLM);
retrieval store varied **1–20×**, pretraining data **1–150×** the parameter count. Fits a
joint scaling framework over (model size, pretraining tokens, datastore size) to give
allocation guidance.

## Key results
- Retrieval **consistently improves** performance across the grid.
- **Marginal value of retrieval rises** with under-saturated pretraining and varies by
  task type and model scale — i.e., retrieval helps most when the model is small/undertrained
  relative to its data.
- Yields practical guidance for splitting budget between parametric memorization and retrieval.

## Takeaways / how we differ
Cite as the headline related work and state the gap precisely: BabyMT transfers the
weights-vs-datastore allocation question to **constrained-budget, distant-pair MT** and
tests it under the **BabyLM total-token rule** — neither of which this paper does. Do **not**
overclaim novelty of the tradeoff itself; claim the *MT + fixed-budget + distant-CJK*
instantiation. Verify the full result set against the PDF before quoting numbers.

Related: [[MassiveDS]] · [[RETRO]] · [[kNN-MT]] · [[2026-06-26 Path-2 budget allocation study]] · [[Open Questions]] · [[Literature Synthesis & Matrix]]
