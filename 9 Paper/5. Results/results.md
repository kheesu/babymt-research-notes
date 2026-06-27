# 5. Results

> **Skeleton.** The final eval grid has **not yet run** under the path-2 design (bases retired and
> being re-pretrained; SFT grid paused at 80/108 cells). Every cell below marked `‹PENDING›` is to
> be filled from `results/master.csv`. The one real end-to-end number that exists is a **pilot /
> sanity** result and is labelled as such — **do not** present it as a final result.

## 5.0 Pilot sanity check (not a final result)

Before the path-2 re-cut, a single cell — **ko→zh, C0 (baseline), 100k budget, seed 1** — was
evaluated on FLORES devtest (n=1012, beam 4, frozen decoding) to confirm the pipeline produces real
translations rather than noise:

| Setting | chrF | BLEU |
|---|---|---|
| ko→zh, C0, 100k (with frozen `no_repeat_ngram_size=3`) | **14.47** | **14.53** |

SFT eval loss fell **4.67 → 2.48 monotonically**; outputs are fluent, on-target Chinese
(mediocre but valid, as expected at ~42M params under a BabyLM budget). **Takeaway:** absolute
quality is low but the system is valid for measuring *relative* effects across the grid — which is
all our within-pair design requires. This number is a sanity check on one C0 cell; it is **not** a
condition comparison and **not** a final result.

## 5.1 Headline: weights vs. memory (main result)

Per-pair, per-budget chrF / BLEU for C0 / C1 / C2, with within-pair deltas vs. C0 and 95% CIs.

| Pair | Budget | C0 | C1 (Δ vs C0) | C2 (Δ vs C0) |
|---|---|---|---|---|
| ko↔zh | 1k / 10k / 100k | ‹PENDING› | ‹PENDING› | ‹PENDING› |
| ko↔ja | 1k / 10k / 100k | ‹PENDING› | ‹PENDING› | ‹PENDING› |
| en↔zh | … | ‹PENDING› | ‹PENDING› | ‹PENDING› |
| … | … | … | … | … |

> **Headline sentence to write once numbers land:** under the fixed 100M-token budget, allocating
> tokens to {weights | the datastore} wins by ‹Δ› chrF on average (pair as blocking factor),
> {with / against} the low-resource cautionary expectation of \citet{hao2023rethinking}.

## 5.2 Where on the budget curve retrieval pays off

chrF vs. SFT budget (1k → 10k → 100k), one line per condition, with the min–max band across 3 seeds
(log-x). Expected story to confirm: ‹does the weights-vs-memory crossover move with budget?›
Figure: `_figures/learning_curves.*` (`‹PENDING›`).

## 5.3 Dictionary vs. sentence retrieval (C1 vs. C2)

Which form of retrieval helps, and where (lexical vs. phrasal regimes; pairs with vs. without
dictionary coverage). ‹PENDING›. Recall **zh↔ja has no dictionary**, so it appears in C0/C2 only.

## 5.4 Attribution controls (C3 random, C4 oracle)

The controls that license a causal reading of any C1 effect:

| Pair | Budget | C0 | C3 random (Δ) | C1 dict (Δ) | C4 oracle (Δ) |
|---|---|---|---|---|---|
| … | 1k | ‹PENDING› | ‹PENDING› | ‹PENDING› | ‹PENDING› |

**Gate checks to report:** (i) **random ≈ baseline within CI** on chrF (else the C1 "gain" is a
prompt-length artifact); (ii) **oracle ≥ dict ≥ baseline** on rare-word accuracy at 1k. ‹PENDING›.

## 5.5 Targeted metrics

Rare-word accuracy, terminology accuracy, entity accuracy by condition — these are where dictionary
retrieval should show its clearest signal even if corpus-level chrF moves little. ‹PENDING›.

## 5.6 Retrieval-use breakdown

Distribution of C1 entries over `copied_correct` / `wrong_morphology` / `correct_entry_ignored` /
`wrong_sense_copied` / `not_applicable`, by pair and budget. ‹PENDING›.

## 5.7 Optional: base-coverage ablation

Per-pair quality from bilingual vs. trilingual vs. quadrilingual bases (more languages ⇒ fewer
tokens/language under the fixed budget). ‹PENDING — run only if budget/time allow›.

---
*Sources: `4 Experiments/SFT Grid.md`, `PHASE4.md`, `PHASE5.md`, `memory/{sft-produces-real-translations, degenerate-loops-no-repeat-ngram}.md`.*
