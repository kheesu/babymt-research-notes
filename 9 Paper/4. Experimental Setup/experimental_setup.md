# 4. Experimental Setup

> Draft. The grid dimensions below reflect the design; the **exact budget sizes and pair set are
> `‹PENDING›` the path-2 re-pretrain**. Numbers tied to the retired full-budget run are flagged.

## 4.1 Languages and pairs

We study **English (en), Chinese (zh), Japanese (ja), Korean (ko)**, evaluating **directed**
translation. The full design covers the 12 directed CJK+En pairs; the reported grid centres on the
CJK-internal directions (**ko↔zh, ko↔ja**) plus en-side pairs, with the final set `‹PENDING›`
(see README open decisions). Chinese is normalized to **Simplified** throughout training and
evaluation (§4.5).

## 4.2 Data

**Pretraining.** Wikipedia (`wikimedia/wikipedia`, 20231101 snapshot, CC-BY-SA-4.0), restricted to
the base's languages, capped to the per-language budget.

**Parallel data (SFT + datastore).** Sourced per pair from AI-Hub Korean-pivot corpora (ko↔zh
≈1.01M, ko↔ja ≈1.04M, ko↔en ≈214k pairs), WikiMatrix \citep{schwenk2021wikimatrix}, MultiUN
\citep{eisele2010multiun}, Tatoeba, and Tanaka. **Dictionaries (C1)** come from krdict (ko↔zh
108,686; ko↔ja 168,532), CC-CEDICT (en-zh 152,390), JMdict (en-ja, capped 200k), and psyche
(en-ko 125,906). **No direct zh↔ja dictionary exists**, so zh↔ja runs **C0+C2 only**. Full
provenance, licenses, and counts are in `DATA_SOURCES.md` and reproduced in `10. Appendix`.

**Test set.** **FLORES-200 devtest** \citep{nllb2022}, **1,012 sentences per pair**, all directed
pairs built by pivoting on the shared English column. **FLORES is held out and not counted against
the budget.** Contamination gates (exact dedup + semantic LaBSE audit) certify **0 FLORES leakage**
into any training file.

**Budget carving.** Each pair is carved to an identical schedule so cells are comparable: SFT
budgets of **1k ⊂ 10k ⊂ 100k** (nested), a 1k dev split, and a per-pair TM datastore (target 200k
pairs, capped lower where the pool is smaller, e.g. en↔ko ~161k, ja↔zh ~155k).

## 4.3 The grid

The core design crosses:

> **{C0, C1, C2}** conditions × **directions** × **budgets {1k, 10k, 100k}** × **seeds {1, 2, 3}**,
> with **C3, C4** added at evaluation time (inference-only, on the C1 checkpoint).

In the as-built per-base arm this is **108 trained cells** (3 conditions × 4 directions × 3 budgets
× 3 seeds). An optional **base-coverage** arm repeats the grid from bilingual/trilingual/quadrilingual
bases (checkpoints tagged `-tri`/`-quad`; bilingual untagged).

**Seed scope (a reproducibility caveat to state up front).** The 3 seeds vary **SFT only**; all
conditions share **one pretraining run**. Across-seed confidence intervals therefore capture SFT
randomness and **understate total run-to-run variance**; results are conditional on one pretrained
base per configuration. Bounding pretraining variance would require ≥2 pretrain seeds (see
`8. Limitations`).

## 4.4 Training

**Pretraining.** From scratch, bf16, to convergence (monotonically decreasing held-out loss); the
ko-zh and ja-ko bases converged (val loss 7.46→3.98 and 7.46→3.77 respectively).
**SFT.** 10 epochs, batch 32 (16 × 2 grad-accumulation), bf16, dynamic padding, **loss on target
tokens only**. Hyperparameters (LR, epochs) were frozen from a small sweep on a single C0 cell and
held fixed across all cells for comparability (`10. Appendix`). SFT is **compute-bound** (~56 min per
100k cell on an A6000, 93–97% GPU utilization); we deliberately did *not* apply throughput
optimizations (group-by-length, larger batches, fewer epochs) mid-grid because they would break
cross-cell comparability — this is a compute note, not a result.

## 4.5 Decoding

A **single frozen decoding configuration** is used for *every* condition, budget, and seed, so
decoding can never confound a cross-condition comparison: **beam search (beam 4)**, early stopping,
length penalty 1.0, no sampling, `repetition_penalty = 1.0`, **`no_repeat_ngram_size = 3`**, and
`max_new_tokens = 256`.

We added `no_repeat_ngram_size = 3` after observing **degenerate repetition loops** in a small tail
of outputs. On a pilot A/B (ko→zh, C0, 100k, FLORES n=1012, beam 4), it eliminated **100% of
degenerate loops (27/1012 = 2.7% → 0)** and outputs hitting the 256-token cap (20 → 0), with **no
collateral** — clean outputs were essentially byte-identical — and a small quality gain (chrF
13.72 → 14.47; BLEU 14.07 → 14.53). We chose it over a global `repetition_penalty > 1` precisely
because it fires *only* on pathological repeats, preserving cross-condition BLEU comparability. It
was frozen **before the eval grid ran**, so it invalidates no reported number. (Milder,
capacity-bound repetition is *not* removed by this and reflects the BabyLM-scale floor, not
decoding.)

## 4.6 Metrics and significance

**Headline metrics:** BLEU and chrF (with chrF++, TER, and COMET — `Unbabel/wmt22-comet-da` — as
secondary), using **language-aware tokenization** (sacreBLEU `tokenize=zh` for Chinese targets;
character-level for Korean). **Targeted metrics:** **rare-word accuracy** (over the same
bottom-quartile dictionary-covered words used by the C1 gate), terminology accuracy, and entity
accuracy. A **retrieval-use analysis** classifies each C1 dictionary entry as
`copied_correct` / `wrong_morphology` (ko) / `correct_entry_ignored` / `wrong_sense_copied` /
`not_applicable`.

**Significance.** The primary contrast is **within-pair**: for each seed we compute a paired delta
(condition − C0) on the same FLORES sources; the headline test aggregates across the 3 seeds with a
Student-t 95% CI (significant iff the CI excludes 0). We also report per-run paired bootstrap deltas
with 95% CIs, and control the false-discovery rate across the family of per-seed tests
(Benjamini–Hochberg). Cross-pair comparisons are treated as **descriptive only** because data
sources/domains differ across pairs (see `8. Limitations`).

## 4.7 Validation gates

Each phase has a pass/stop gate. Notably, the evaluation phase requires the **monotonicity sanity
checks** that license the controls: **oracle ≥ dict ≥ baseline** on rare-word accuracy at the
smallest budget, and **random ≈ baseline within CI** on chrF (a violation flags a prompt-length
confound and halts the analysis). Budget accounting is script-generated from the data card so the
reported totals are reproducible.

---
*Sources: `PHASE3.md`, `PHASE4.md`, `PHASE5.md`, `DATA_SOURCES.md`, `4 Experiments/{SFT Grid, Pretrain Bases}.md`, `limitations_data.md`, `memory/degenerate-loops-no-repeat-ngram.md`.*
