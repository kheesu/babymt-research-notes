---
tags: [literature]
status: draft
created: 2026-06-26
updated: 2026-06-26
authors: Rulin Shao, Jacqueline He, Akari Asai, Weijia Shi, Tim Dettmers, Sewon Min, Luke Zettlemoyer, Pang Wei Koh
year: 2024
venue: arXiv 2407.12854 (NeurIPS 2024 — verify)
url: https://arxiv.org/abs/2407.12854
arxiv: 2407.12854
---

# Scaling Retrieval-Based Language Models with a Trillion-Token Datastore (MassiveDS)

**TL;DR:** With **MassiveDS** (a **1.4-trillion-token** open datastore), **a smaller model
+ a large datastore outperforms a larger LM-only model** on knowledge-intensive tasks, and
datastore size improves performance **monotonically without saturation** → datastore scale
belongs alongside parameters in compute-optimal accounting. *(finding confirmed by direct
fetch)*

## Relevance to BabyMT
The most direct large-scale support for path-2's central claim: **datastore tokens are a
budget axis you can trade against parameters.** BabyMT operationalizes exactly this trade,
but (a) at BabyLM scale, (b) under a *single fixed total* budget rather than open scaling,
(c) for **translation** with dictionary/sentence retrieval rather than open-domain QA-style
retrieval.

## Method
Built **MassiveDS**, the largest/most-diverse open datastore for retrieval-based LMs to
date, plus an efficient pipeline to study datastore scaling without retraining models.
Produces **compute-optimal scaling curves** over datastore size × model size × pretraining
data size. Open-sourced code + datastore.

## Key results
- **Smaller model + large datastore > larger LM-only** on knowledge-intensive tasks.
- Increasing datastore size yields **continuous, non-saturating** gains.
- Argues efficiency analyses should treat datastore size as a first-class scaling variable.

## Takeaways / how we differ
Validates the "spend tokens on the datastore" half of path-2 at trillion scale. BabyMT
supplies the missing constraint — a **fixed total budget** that forces weights *and*
datastore to compete — and tests it where the base model is far too small to be saturated.

Related: [[RETRO]] · [[To Memorize or to Retrieve]] · [[kNN-MT]] · [[2026-06-26 Path-2 budget allocation study]] · [[Literature Synthesis & Matrix]]
