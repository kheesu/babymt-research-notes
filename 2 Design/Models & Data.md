---
tags: [project]
status: draft
created: 2026-06-26
updated: 2026-06-26
---

# Models & Data

> Canonical, exact provenance + licenses live in the code repo's **`DATA_SOURCES.md`**.
> This is the brief mirror.

## Model
- `custom-50m` — GPT-2-style, 8 layers / 512 hidden, ~42M params, trained from scratch.
- Corpus-driven BPE (32k). Per-language-subset bases — see [[Pretrain Bases]].

## Corpus (pretraining)
- Wikipedia (`wikimedia/wikipedia` 20231101), en/zh/ko/ja. 100M-token cap, split per language.

## Parallel data (SFT + retrieval) — by pair
| Pairs | Source | Notes |
|---|---|---|
| ko↔zh, ko↔ja | AI-Hub book/caption (ko-pivot) | ~1.0M each; books→train, captions→TM |
| en↔zh | Tatoeba bulk TSV (74.6k) | Simplified after t2s; augmentable w/ MultiUN/WikiMatrix |
| zh↔ja | WikiMatrix (256.6k) | Simplified after t2s; the hard pair, solved |
| en↔ja | Tanaka (~148k) | ingester ready, not built |
| ko↔en | AI-Hub TL_en (~214k) | not built |

All zh from web sources (Tatoeba/WikiMatrix) is **OpenCC t2s**-normalized to Simplified to match FLORES — [[Traditional Chinese only from web sources]].

## Dictionaries (C1)
- krdict (NIKL) → ko↔zh (108,686), ko↔ja (168,532). en-X: CC-CEDICT / psyche / kaikki. **No zh↔ja or en↔ja dict** → those pairs C0+C2 only.

## TM pool / test
- TM pool: 200k/pair (sentence FAISS, LaBSE). Test: **FLORES-200** devtest, 1012/pair, all pairs built by pivoting the shared English column.

> [!warning] path-2 will re-cut sizes
> Splits/TM sizes above are pre-path-2; budget-aware re-slicing pending the allocation decision.

Related: [[Phase 1 Data]] · [[Pretrain Bases]]
