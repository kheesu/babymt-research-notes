# 1. Introduction

> Draft. Citation keys (e.g. `\citep{borgeaud2022retro}`) resolve in `9. References`.

## The claim we want to test

A recurring result in modern NLP is that **retrieval can substitute for parameters**. RETRO matches
GPT-3-class quality on the Pile with roughly 25× fewer parameters by retrieving from a
2-trillion-token database \citep{borgeaud2022retro}; nearest-neighbour LMs and MT improve a frozen
model with no additional training \citep{khandelwal2020knnlm, khandelwal2021knnmt}; and MassiveDS
reports that a small model with a large datastore can beat a larger model, with datastore size
helping *monotonically* and without saturation \citep{shao2024massiveds}. The implicit lesson is
that knowledge can live *outside* the weights, in a cheap, swappable store.

Two assumptions hold across nearly all of this evidence, and both are convenient:

1. **The base model is strong.** Results come from well-trained models, often billions of
   parameters. Whether the effect survives for a *tiny, undertrained* model is largely untested.
2. **The datastore is free.** The retrieval store's tokens are never counted against the model's
   training budget — "weights" and "memory" are drawn from different ledgers, so retrieval looks
   like a pure addition rather than a *reallocation*.

There is, moreover, a sharp counter-signal. In machine translation, translation-memory augmentation
**helps in high-resource settings but actively hurts in low-resource ones**
\citep{hao2023rethinking, cai2021neural}: on JRC-Acquis De→En, a TM-augmented system scores 63.76 vs.
60.83 (vanilla) with abundant data, but **53.92 vs. 54.54** when data is scarce — a loss. The
proposed mechanism is a bias–variance trade-off: retrieval lowers bias but raises variance, and
under scarcity the variance penalty wins. A BabyLM-scale MT model lives squarely in that regime.

## Our question

We collapse both convenient assumptions at once. We ask:

> **Under a single fixed total token budget, is a token better spent on model weights (pretraining)
> or on a nonparametric retrieval datastore — for translation, at small scale, on distant language
> pairs?**

We instantiate this at **BabyLM scale** \citep{warstadt2023findings, choshen2026babylm}, where the
challenge's core premise is exactly a hard data cap. Crucially — and following the challenge's own
accounting rule that *all* data training the model or its auxiliary components counts toward the
budget \citep{warstadt2023findings} — we count **pretraining, supervised fine-tuning, and the
retrieval datastore against one shared 100M-token budget**. This turns "add retrieval" into "move
budget from weights to memory", making a no-retrieval baseline and a retrieval system a *fair,
equal-budget* comparison: same data, same budget, the only difference being whether a token is
baked into weights or held in a lookup table.

## Why CJK + English at BabyLM scale

We study **Chinese, Japanese, Korean, and English** — typologically distant, non-trivially
script-divergent pairs where both halves of retrieval are plausible: a **bilingual dictionary**
(lexical knowledge for rare words) and **parallel-sentence translation memory** (phrasal/structural
knowledge). This is also where low-resource MT pathologies are most visible, so the
bias–variance question above is not hypothetical. The open empirical possibility we are positioned
to settle: a base this small may *underfit* so badly that retrieval's bias-reduction dominates the
usual variance penalty — flipping the standard low-resource story — or it may not, in which case the
cautionary result extends to the fixed-budget regime.

## Contributions

1. **A fixed-budget reframing of the memorize-vs-retrieve question for MT.** We count the retrieval
   datastore against the same hard token budget as the model, so weights and memory compete for one
   resource. To our knowledge this equal-budget framing is new for MT and is untested at BabyLM
   scale (§2 positions us against \citealp{singh2026memorize}, the closest prior work).
2. **A controlled CJK+English translation testbed** spanning a no-retrieval baseline (C0),
   dictionary retrieval (C1), and sentence retrieval (C2), with **random (C3)** and **oracle (C4)**
   retrieval controls that separate the effect of *retrieval quality* from the effect of merely
   adding prompt tokens (§3).
3. ‹PENDING: empirical findings — the direction of the weights-vs-memory trade-off across budgets
   and pairs, and whether dictionary or sentence retrieval helps where (§5–6).›
4. **Honest budget accounting and a data-heterogeneity-aware design** (within-pair deltas with pair
   as a blocking factor) that we argue is necessary for any small-scale retrieval-MT claim (§8).

---
*Sources: `1 Project/{BabyMT Overview, Open Questions}.md`, `6 Decisions/2026-06-26 Path-2...md`, `7 Literature/{Literature Synthesis & Matrix, Rethinking TM-Augmented NMT, To Memorize or to Retrieve}.md`.*
