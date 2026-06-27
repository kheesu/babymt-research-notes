# 8. Limitations

> Draft. ACL-mandatory unnumbered section. This is the most developed part of the project notes
> (`limitations_data.md`) — the prose below is close to submission-ready.

## Heterogeneous training data across pairs

Our parallel training data is **not drawn from a common source across language pairs**, and the
domain mix differs even within a pair. Approximate pool sizes and registers:

| Directed pair(s) | Pool | Source(s) / register |
|---|---|---|
| en↔zh | ~1.26M | Tatoeba (everyday) + WikiMatrix (encyclopedic) + MultiUN (legal/diplomatic) |
| en↔ja | ~545k | WikiMatrix (encyclopedic) |
| en↔ko | ~263k | AI-Hub books (literary/academic) + WikiMatrix |
| ja↔zh | ~257k | WikiMatrix + Tatoeba; **no bilingual dictionary** |
| ko↔zh | ~1.0M | AI-Hub books (literary/academic) + captions (broadcast/colloquial) |
| ko↔ja | ~1.04M | AI-Hub books + captions |

**Why this is largely controlled, not fatal.** Our primary contrast is **within-pair** (C0 vs. C1
vs. C2 on the *same* underlying data, with domain held constant inside a pair). Heterogeneous sources
shift all three conditions of a pair together, so they do not confound the *within-pair* delta. We
report per-pair condition deltas and aggregate with **pair as a blocking / random factor** (paired
tests / mixed-effects), which nets out cross-pair domain differences.

**Where the imbalance genuinely bites:**
1. **Cross-pair comparisons are confounded.** "Retrieval helps zh more than ja" cannot be made
   causal — language and domain effects are entangled. We present cross-pair numbers descriptively
   only.
2. **Uneven domain match to the FLORES (news/encyclopedic) test set.** WikiMatrix-heavy pairs are
   better matched than AI-Hub pairs, which inflates their *absolute* scores; the size of a retrieval
   gain partly reflects train–test domain alignment, not just retrieval value.
3. **C1 (dictionary retrieval) is unavailable for ja↔zh** — no direct ja–zh dictionary exists and
   Korean/English pivoting is infeasible on our data — so ja↔zh is reported on **C0 + C2 only**.

**Mitigations we apply / offer:** transparency (per-pair source/register data cards), explicit claim
scoping to within-pair deltas, pair-blocked statistics, and an optional **WikiMatrix-only
domain-matched robustness subset** for the pairs that have WikiMatrix coverage (en↔zh, en↔ja, en↔ko,
ja↔zh) — noting that the Korean-pivot pairs (ko↔zh, ko↔ja) have no WikiMatrix and cannot join, so
this check is only partial.

## Single pretraining seed

The three seeds in our grid vary **SFT only**; all conditions share **one pretraining run** per
configuration. Across-seed confidence intervals therefore capture SFT randomness and **understate
total run-to-run variance**; every result is conditional on that one pretrained base. Bounding
pretraining variance would require multiple pretrain seeds, which the fixed compute budget did not
allow. We flag this rather than over-read tight CIs.

## Budget accounting is an operationalization

We count budget in **unique tokens** across pretraining ∪ SFT ∪ datastore. This is *our* reading of
the BabyLM additive-budget principle; the official rule is stated in "words"/"tokens"
(byte-premium-adjusted) and never in "unique tokens". We adopt unique-token accounting because it is
the only scheme under which an epoch-trained model and a multiply-queried datastore are charged
comparably, but it is an interpretation, not certified compliance — and our pairs (Korean, Japanese)
fall **outside** the official 2026 Multilingual track (English/Dutch/Chinese pretraining), so we
adapt the budget *discipline* rather than compete in that track.

## Absolute quality is low

At ~42M parameters under a hard data budget, absolute translation quality is low (single-digit-to-low
chrF in pilot measurement) with visible capacity-bound artifacts (mild repetition, garbled long
sentences) that decoding constraints do not remove. Our claims are about **relative** effects across
conditions and budgets, not about competitive MT quality; readers should not compare our absolute
scores to production or large-LLM systems.

## Scope of conditions

Retrieval is injected **in-context** (prompt-level), not via token-level distribution interpolation
(kNN-MT-style); a combined dictionary+sentence condition and a legacy FAISS dictionary retriever
exist but are out of the reported grid. Our conclusions are about the conditions we ran.

## Provisional status (remove before camera-ready)

This draft predates the final eval grid. Bases pretrained at the full 100M budget were retired under
the fixed-total accounting and are being re-pretrained at a reduced budget; the pilot ko→zh number is
a sanity check, not a result. All `‹PENDING›` cells must be filled from the completed grid before
submission.

---
*Sources: `limitations_data.md`, `5 Findings/{Traditional Chinese only from web sources, zh-ja Korean-pivot infeasible}.md`, `PHASE4.md`, `7 Literature/Literature Synthesis & Matrix.md`.*
