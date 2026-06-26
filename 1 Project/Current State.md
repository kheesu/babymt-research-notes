---
tags: [project]
status: draft
created: 2026-06-26
updated: 2026-06-26
---

# Current State

*Live snapshot. Update first when the picture changes.*

> [!important] Major pivot — PATH 2 (fixed-budget allocation study)
> The BabyLM 100M-token budget must count **all** consumed data (pretrain + SFT +
> RAG datastore), not just pretrain. Reframed question: under a fixed 100M total,
> is a token better spent on **weights (pretraining)** or a **retrieval datastore**?
> This **retires the existing 100M-each bases** — they need re-pretraining at a
> reduced budget. See [[2026-06-26 Path-2 budget allocation study]].

## Done
- Repo re-rooted to `/work/heesu/BabyMT` (was a nested-clone mess).
- **CJK data pipeline** for ko-zh / zh-ko / ko-ja / ja-ko: BabyLM (AI-Hub) ingest, FLORES test (pivot), nested 1k/10k/100k splits + dev, 200k TM pools, krdict dicts, FAISS sent indexes, contamination gates (exact + semantic → 0 leakage). See [[Phase 1 Data]], [[Models & Data]].
- **Data sources for all 12 directed pairs** assembled; ingesters built (code `DATA_SOURCES.md` is canonical).
- **en-zh** built (Tatoeba bulk TSV, 74,606) and **zh-ja** built (WikiMatrix, 256,579); both zh-normalized to Simplified. See [[Traditional Chinese only from web sources]].
- Length-ratio filter retuned for Latin↔CJK ([[2026-06-26 Length-ratio filter retune for Latin-CJK]]).

## Running
- Nothing. SFT grid paused.

## Paused / blocked on path-2
- **SFT grid** (CJK, C0/C1/C2): bilingual-base arm reached **80/108, 0 failed**, then paused — [[SFT Grid]]. Likely invalidated by the budget redesign.
- Re-pretraining at reduced budget, budget-aware splits, new grid, eval grid, CJK HP tuning — not started.

## Not built yet (optional)
- en-ja pool (Tanaka ingester ready, ~148k), ko-en pool (BabyLM ~214k, needs ingester).

## Known stale
- Code `README.md` / `PHASE3–5.md` describe the **old** en-zh/en-ko design — not authoritative.

Related: [[Timeline]] · [[Open Questions]] · [[The Grid]]
