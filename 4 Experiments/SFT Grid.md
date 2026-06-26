---
tags: [run]
status: draft
created: 2026-06-26
updated: 2026-06-26
---

# SFT Grid

CJK SFT grid (`scripts/run_grid.py`). **Paused** at 80/108 after the path-2 pivot.

## Cell matrix
- 3 conditions (C0 baseline, C1 dict_rag, C2 sent_rag) × 4 directions (ko-zh, zh-ko, ko-ja, ja-ko) × 3 budgets (1k/10k/100k) × 3 seeds = **108 cells per base arm**.
- C3/C4 are inference-only (eval), not trained.

## Naming / launcher
- `run_grid.py --pairs ko-zh zh-ko ko-ja ja-ko --wandb-project babymt-sft --gpus 4,5,6,7`
- Output: `checkpoints/sft-{cond}-{pair}-{budget}-s{seed}` (base-coverage arms add a `-tri`/`-quad` suffix; bi = untagged).
- Per-pair base via `run_grid.base_ckpt_for`. W&B project `babymt-sft`.

## Status (2026-06-26)
- Bilingual-base arm: **80/108 done, 0 failed**, then paused. ~56 min per 100k cell.
- **Likely invalidated** by path-2 (bases retire → re-pretrain → re-run). See [[Current State]].

## Config notes
- 10 epochs, batch 32 (16×2 accum), bf16, dynamic padding, loss on target only.
- `--no-wandb` fixed to use `report_to=none` (was setting deprecated `WANDB_DISABLED`).
- Why it's not faster: [[SFT speed is compute-bound not a bug]].

Related: [[The Grid]] · [[Pretrain Bases]] · [[Eval Grid]]
