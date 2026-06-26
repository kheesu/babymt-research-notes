---
tags: [decision]
status: draft
aliases: []
created: 2026-06-26
updated: 2026-06-26
supersedes:
superseded_by:
---

# 2026-06-26 Path-2 budget allocation study

**Decision:** Count *all* consumed data (pretrain + SFT + RAG datastore) toward the 100M-token budget, and study the **allocation** — weights (pretraining) vs a retrieval datastore — at a fixed 100M total.

## Context
Capping only pretraining at 100M is not BabyLM-compliant: SFT data + the TM/dict datastore are additional unique tokens (a single C2 model ≈ 115–130M). Budget unit = **unique tokens** (epochs / repeated retrieval don't multiply), so it reduces to `|unique(pretrain ∪ SFT ∪ datastore)|`.

## Options considered
1. Datastore ⊆ counted data (compliance only).
2. **Allocation study** (chosen): fix 100M total, vary the pretrain-vs-datastore split. Fair C0-vs-C1/C2 (C0 folds the datastore tokens into pretraining; C1/C2 hold them in the retriever — same data, same budget, weights vs lookup).
3. Report honestly out-of-budget.

## Rationale
Path-2 subsumes path-1's compliance and is the sharper, more publishable framing.

## Consequences
- **Retires the 100M-each bases** → re-pretrain at a reduced budget (the expensive part). C0 vs C1/C2 likely need different bases.
- SFT grid **paused** (80/108) — [[SFT Grid]].
- Open: datastore `D`, SFT budgets, fixed-split vs axis, re-pretrain scope — [[Open Questions]].
- Committed to code: not yet (design only).

Related: [[Current State]] · [[BabyMT Overview]]
