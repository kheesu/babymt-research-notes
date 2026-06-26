---
tags: [finding]
status: draft
aliases: []
created: 2026-06-26
updated: 2026-06-26
confidence: high
---

# SFT speed is compute-bound, not a bug

**Claim:** The "slow" SFT (~56 min per 100k cell on an A6000) is genuine configured work, not a pipeline fault — the GPU is compute-bound.

## Setup
- Profiled a finished 100k cell + live `nvidia-smi`; measured token lengths and throughput.

## Result
- GPU util **93–97%**; bf16 on; labels masked to target only; collator pads **dynamically** (not 512); eval only ~154s total.
- Driver of runtime: **10 epochs** (best eval_loss at ~86% of steps), short sequences (baseline ~62 tok; sent_rag ~198 tok, 3×) × small batch (32) → ~19k real / 30k padded tok/s, ~38% pad waste.

## Interpretation
No bug. Speedups exist (group_by_length, larger batch, fewer epochs) but **break cross-cell comparability mid-grid** → user chose to keep config as-is.

## Paper
- [ ] Goes in as: footnote/compute-budget note (not a result).

Related: [[SFT Grid]]
