---
tags: [literature]
status: draft
created: 2026-06-26
updated: 2026-06-26
authors: Hongkun Hao, Guoping Huang, Lemao Liu, Zhirui Zhang, Shuming Shi, Rui Wang (verify)
year: 2023
venue: Findings of ACL 2023
url: https://arxiv.org/abs/2306.06948
arxiv: 2306.06948
---

# Rethinking Translation Memory Augmented Neural Machine Translation

> [!danger] The cautionary result BabyMT must confront
> TM/retrieval augmentation **helps in high-resource but FAILS (absolutely underperforms
> vanilla) in low-resource MT.** BabyMT lives squarely in the low-resource, distant-pair
> regime where retrieval has been shown to backfire — pre-register this as a risk; the
> random (C3) and oracle (C4) controls are load-bearing, not optional.

**TL;DR:** Identifies a "contradictory phenomenon" on the same task — **TM-augmented NMT
substantially beats vanilla under high resource but loses under low resource** — and
explains it as a **variance–bias tradeoff**: TM gives lower bias (better data fitting) but
higher variance (more sensitive to training-data fluctuations). Originally observed by Cai
et al. (2021); demonstrated and explained here. *(adversarially verified, 3-0)*

## Relevance to BabyMT
The single most important risk for **[[C2 sent_rag]]** (and, by extension, retrieval
generally) at BabyMT scale. If the effect transfers, naive sentence-TM could *hurt*
BabyMT's low-budget cells — making the C0 baseline hard to beat and the controls essential
for attribution. Conversely, it frames BabyMT's sharpest open question: a **tiny,
undertrained** base may *underfit* so badly that TM's bias-reduction dominates its variance
penalty — the opposite of the standard low-resource story. See [[Open Questions]].

## Method
Decompose TM-augmented NMT error into bias and variance across resource levels; compare TM
vs vanilla on the same pairs at high- and low-resource splits; propose a
variance-promoting fix.

## Key results *(verified)*
- JRC-Acquis De→En: TM **63.76** vs vanilla 60.83 (high-resource, gain) **but 53.92 vs
  54.54** (low-resource, **absolute degradation**).
- Mechanism confirmed: lower bias, higher variance under data scarcity.
- Cites **Cai et al. (2021)** for the original low-resource failure observation.

## Takeaways / how we differ
Don't assume retrieval helps. BabyMT must (a) report C0 honestly, (b) lean on C3/C4 to
separate retrieval *quality* from generation, and (c) treat "does retrieval help a tiny
base?" as an empirical contribution rather than an assumption. Verify the full author list
before citing.

Related: [[C2 sent_rag]] · [[kNN-MT]] · [[Open Questions]] · [[Literature Synthesis & Matrix]]
