---
tags: [literature]
status: draft
created: 2026-06-26
updated: 2026-06-26
authors: Urvashi Khandelwal, Angela Fan, Dan Jurafsky, Luke Zettlemoyer, Mike Lewis
year: 2021
venue: ICLR 2021
url: https://arxiv.org/abs/2010.00710
arxiv: 2010.00710
---

# Nearest Neighbor Machine Translation (kNN-MT)

**TL;DR:** Augment a **frozen** pretrained MT model with a **token-level
nearest-neighbor datastore** at inference, **no additional training**; interpolate
`(1−λ)·p_LM + λ·p_kNN`. The canonical nonparametric-retrieval MT method and the cleanest
"trade datastore size for quality" demonstration. *(adversarially verified, 3-0)*

## Relevance to BabyMT
Reference method for the **[[C2 sent_rag]]** / nonparametric arm and a core anchor for
the [[2026-06-26 Path-2 budget allocation study|path-2]] thesis (its subtitle,
"Generalization through Memorization," *is* that thesis). **Two differences to flag:**
(1) kNN-MT is **token-level**; BabyMT's C2 is **sentence-level TM/RAG** (LaBSE+FAISS).
(2) kNN-MT uses a **large, fully-trained SOTA base** and trades datastore for in-domain
*training data* — it does **not** enforce a fixed total budget, so it motivates but does
not pre-empt path-2.

## Method
Datastore = cached `(decoder context representation → ground-truth target token)` pairs
over parallel data. At each decode step, query the current decoder hidden state, retrieve
k nearest tokens, form `p_kNN` by softmax over (negative) distances, and interpolate with
the base model. Build-once, no gradient updates.

## Key results *(verified)*
- **+1.5 BLEU** De-En newstest2019 (37.59 → 39.08) with a **770M-token** datastore.
- One **418M** multilingual model, per-pair specialized datastores: **en-de +3.02,
  zh-en +3.28, en-zh +3.41 BLEU** (datastores 6.50B / 1.19B / 1.13B) — gains hold on
  distant Chinese pairs.
- **Domain adaptation by datastore swap:** a News model reaches Medical 54.35 (vs 39.91
  none; in-domain 56.65) with a 5.7M store, and Legal **61.78** — *exceeding* the
  in-domain Legal model's 59.00 — with an 18.3M store.

## Takeaways / how we differ
Establishes that a datastore can substitute for in-domain training. BabyMT asks the
sharper question — can a datastore substitute for **pretraining parameters under one
fixed total token budget**, at ~42M params, on distant CJK pairs, with sentence-level and
dictionary retrieval rather than token-level interpolation.

Related: [[C2 sent_rag]] · [[To Memorize or to Retrieve]] · [[MassiveDS]] · [[Literature Synthesis & Matrix]]
