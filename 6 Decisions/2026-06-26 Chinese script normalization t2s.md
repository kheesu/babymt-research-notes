---
tags: [decision]
status: draft
aliases: []
created: 2026-06-26
updated: 2026-06-26
---

# 2026-06-26 Chinese script normalization (OpenCC t2s)

**Decision:** Normalize the zh side of web-sourced pools (Tatoeba, WikiMatrix) to **Simplified** with OpenCC `t2s`, to match the FLORES test set (`zho_Hans`) and the `zh` sacreBLEU tokeniser.

## Context
Tatoeba/WikiMatrix zh is ~37–42% Traditional; FLORES, BabyLM zh, and krdict are 100% Simplified. Training on mixed script vs testing on Simplified is a confound — [[Traditional Chinese only from web sources]].

## Rationale
Standard, defensible preprocessing when reported (tool + % converted). `t2s` is a no-op on already-Simplified text → safe to apply uniformly. Test set left untouched.

## Consequences
- `scripts/normalize_zh.py` (in-place, preserves directed-twin alignment, logs %); `opencc==1.3.1` added to `pyproject.toml`.
- en-zh: 44.5% converted; zh-ja: 68.6% converted → 0.0% Traditional after.
- Run on any future WikiMatrix/Tatoeba zh pool before splitting.

Related: [[Traditional Chinese only from web sources]] · [[Models & Data]]
