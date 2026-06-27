---
tags: [literature]
status: draft
created: 2026-06-26
updated: 2026-06-26
authors: Sebastian Borgeaud, Arthur Mensch, Jordan Hoffmann, et al. (DeepMind)
year: 2022
venue: ICML 2022
url: https://proceedings.mlr.press/v162/borgeaud22a/borgeaud22a.pdf
arxiv: 2112.04426
---

# Improving Language Models by Retrieving from Trillions of Tokens (RETRO)

**TL;DR:** A **7.5B**-param retrieval-augmented LM with a **2-trillion-token** retrieval
database matches **GPT-3 / Jurassic-1 (~175–178B)** on the Pile using **~25× fewer
parameters** — the cleanest demonstration that nonparametric retrieval memory can
substitute for model parameters. *(finding confirmed by direct fetch)*

## Relevance to BabyMT
The headline existence proof behind path-2: *retrieval lets a much smaller model match a
much larger one.* BabyMT scales this intuition **down** to BabyLM size and **constrains
it** — RETRO's 2T-token database is effectively unbounded, whereas BabyMT counts the
datastore against the same 100M budget as the weights. RETRO retrieves **text chunks via
cross-attention**; BabyMT retrieves **dictionary entries (C1) and parallel sentences
(C2)**.

## Method
Chunk the input; for each chunk retrieve nearest neighbours (by a frozen BERT encoder)
from a key–value database of text chunks; integrate them through **Chunked Cross-Attention
(CCA)**. Retrieval is part of pretraining, not only inference; existing LMs can be
"Retrofitted" (RETROfit) to use retrieval with modest extra training.

## Key results
- **7.5B + 2T-token DB ≈ GPT-3/Jurassic-1 (~175B)** on the Pile, ~25× fewer params.
- Performance scales with database size; gains are largely orthogonal to model scale.

## Takeaways / how we differ
RETRO motivates spending capacity on a datastore instead of parameters — but with no
shared budget and at a scale where the base is already strong. BabyMT tests whether the
substitution still holds when the base is **tiny and undertrained** and the datastore is
**budget-limited** — precisely the regime RETRO does not probe.

Related: [[MassiveDS]] · [[To Memorize or to Retrieve]] · [[kNN-MT]] · [[2026-06-26 Path-2 budget allocation study]] · [[Literature Synthesis & Matrix]]
