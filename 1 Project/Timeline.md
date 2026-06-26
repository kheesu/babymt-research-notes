---
tags: [project]
status: draft
created: 2026-06-26
updated: 2026-06-26
---

# Timeline

*Chronological milestones. One line each, dated.*

- **2026-06-25** — pre-trained 11 per-language-subset bases (custom-50m, ~42M) at 100M tokens each.
- **2026-06-26** — re-rooted repo; built the CJK data pipeline (ingest → splits → TM pools → dicts → FAISS → contamination gates) for ko-zh/zh-ko/ko-ja/ja-ko.
- **2026-06-26** — ran the CJK SFT grid (C0/C1/C2 × 4 dirs × 3 budgets × 3 seeds); **paused at 80/108**.
- **2026-06-26** — **pivoted to path-2** (budget must count SFT + datastore; allocation study; bases retired). Grid paused.
- **2026-06-26** — assembled data sources for all 12 pairs; built en-zh (Tatoeba 74.6k) + zh-ja (WikiMatrix 256.6k); added OpenCC t2s zh-normalization; retuned length filter.

> Note: most of the above is a single working day (2026-06-26); exact ordering within the day is approximate.

Related: [[Current State]]
