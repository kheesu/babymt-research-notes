---
tags: [finding]
status: draft
aliases: []
created: 2026-06-26
updated: 2026-06-26
pair: "*-zh"
confidence: high
---

# Traditional Chinese only from web sources

**Claim:** Traditional Chinese enters the data *only* via the community/web sources (Tatoeba, WikiMatrix); the core ko-pivot data, dictionaries, and test set are 100% Simplified. Fixed with OpenCC `t2s`.

## Setup
- Classify zh segments by simplified-only vs traditional-only characters; sample 20k/source.

## Result
| Source | Traditional % |
|---|---|
| Tatoeba en-zh | 37.3% |
| WikiMatrix ja-zh | 42.4% |
| BabyLM ko-zh (books/captions) | 0.0% |
| krdict ko-zh | 0.0% |
| FLORES ko-zh (test, zho_Hans) | 0.0% |

- Normalized with OpenCC `t2s` (`scripts/normalize_zh.py`): en-zh **44.5%** converted, zh-ja **68.6%** converted → 0.0% Traditional after.

## Interpretation
Train/test script mismatch (Simplified FLORES) would depress zh scores. `t2s` is a
no-op on already-Simplified text, so safe to apply uniformly to web-source zh pools.
Standard, defensible preprocessing when reported with tool + %.

## Paper
- [ ] Goes in as: data-prep sentence ("…OpenCC t2s; N% converted").

Related: [[2026-06-26 Chinese script normalization t2s]] · [[Models & Data]]
