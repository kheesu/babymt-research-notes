---
tags: [project]
status: draft
created: 2026-06-26
updated: 2026-06-26
---

# BabyMT — Overview

## Research question
Does **retrieval** (bilingual-dictionary lookups, parallel-sentence examples) help
machine translation for a **BabyLM-scale** model (a single ~42M LM pre-trained from
scratch under the 100M-token budget)?

> [!note] Evolving framing (path-2)
> Reframed to a **fixed-budget allocation** question: counting *all* data (pretrain +
> SFT + datastore) toward the 100M cap, is a token better spent on **model weights**
> (pretraining) or a **retrieval datastore**? Final framing is the user's to lock —
> see [[Open Questions]], [[2026-06-26 Path-2 budget allocation study]].

## Why it matters
RAG-MT is well studied at 7–14B params; this tests whether the benefit survives at
toy scale, and (path-2) makes C0-vs-C1/C2 a *fair, equal-budget* comparison.

## Target / deadline
BabyLM Challenge 2026, **Multilingual Track**, deadline **2026-07-15** (per code
README — verify still current given the pivot).

## Scope
- Pairs: original en-zh/en-ko; extended to **CJK + en, bidirectional** — ko↔zh, ko↔ja,
  en↔zh, zh↔ja, en↔ja, ko↔en (12 directed pairs; exact final set TBD).
- Conditions: **C0/C1/C2 trained**; C3/C4 inference-only controls on the C1 checkpoint.
- Non-goals: not specified — TBD.

Related: [[Current State]] · [[Experimental Design]] · [[Models & Data]]
