---
tags: [run]
status: draft
created: 2026-06-26
updated: 2026-06-26
---

# Pretrain Bases

11 per-language-subset bases (custom-50m, ~42M), one per subset of {en, ja, ko, zh},
each pre-trained at the 100M-token budget (split per language). In `checkpoints/pretrain-<subset>/`.

| Base | Langs | Tokens/lang | Status |
|------|-------|-------------|--------|
| pretrain-ko-zh | ko, zh | ~50M | converged (val 7.46→3.98) |
| pretrain-ja-ko | ja, ko | ~50M | converged (val 7.46→3.77) |
| pretrain-ja-zh, pretrain-en-zh, pretrain-en-ko, pretrain-en-ja | 2 langs | ~50M | exists; convergence not individually checked |
| pretrain-ja-ko-zh, pretrain-en-ko-zh, pretrain-en-ja-ko, pretrain-en-ja-zh | 3 langs | ~33M | exists; not checked |
| pretrain-en-ja-ko-zh | 4 langs | ~25M | exists; not checked |

- **Per-pair base selection** (in `run_grid.py`): {ko,zh}→pretrain-ko-zh, {ko,ja}→pretrain-ja-ko (the base's BPE must cover the pair's scripts).
- Budget note: more languages ⇒ less per-language data (50M→33M→25M) — the basis of the [[The Grid|base-coverage ablation]].

> [!warning] Retired under path-2
> These are pre-trained at the full 100M each, which **violates** the path-2 total
> budget once SFT + datastore are counted. They will be **re-pretrained at a reduced
> budget**. See [[2026-06-26 Path-2 budget allocation study]].

Related: [[Phase 3 Training]] · [[SFT Grid]] · [[Models & Data]]
