---
tags: [finding]
status: draft
aliases: []
created: 2026-06-26
updated: 2026-06-26
pair: zh-ja
confidence: high
---

# zh-ja Korean-pivot infeasible

**Claim:** zh↔ja cannot be built by pivoting through Korean (or English) on our data — the source sets are disjoint — so zh-ja must use direct sources.

## Setup
- AI-Hub (BabyLM) ko-zh vs ko-ja label files; join on Korean text and on `src_paragraphs_id`.

## Result
- Unique Korean sentences: **165,310** (ko-zh) vs **163,040** (ko-ja).
- Join on ko-text → **4** shared. Join on sentence-id → **0** shared.
- English pivot also fails: MultiUN (UN) vs Tanaka (everyday) English are disjoint.

## Interpretation
AI-Hub assigned each Korean document to *one* target language, not all three → no
multi-parallel anchor. (FLORES is multi-parallel but is the held-out test set.) zh-ja
therefore uses **direct** sources: WikiMatrix (256,579) + Tatoeba (~14k).

## Paper
- [ ] Goes in as: methods/limitation (why pivoting wasn't used for zh-ja).

Related: [[Models & Data]] · [[Open Questions]]
