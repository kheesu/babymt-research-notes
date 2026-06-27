# Limitations draft — training-data heterogeneity & domain imbalance

Paper-ready prose for the Limitations section, plus the supporting reasoning.
Numbers reflect the final carved pools (see `DATA_SOURCES.md` for the per-pair
source table and `data/processed/data_card_*.json` for exact counts).

---

## The situation (factual)

Training parallel data is **not drawn from a common source across language
pairs**, and the within-pair domain mix differs by pair:

| Directed pair(s) | Pool | Source(s) / register |
|---|---|---|
| en↔zh | 1.26 M | Tatoeba (everyday, ~6%) + WikiMatrix (encyclopedic, ~54%) + MultiUN (legal/diplomatic, ~40%) |
| en↔ja | 545 k | WikiMatrix (encyclopedic) |
| en↔ko | 263 k | AI-Hub books (literary/academic) + WikiMatrix (encyclopedic) |
| ja↔zh | 257 k | WikiMatrix (encyclopedic) + Tatoeba (everyday); **no bilingual dictionary** |
| ko↔zh | ~1.0 M | AI-Hub books (literary/academic) + captions (broadcast/colloquial) |
| ko↔ja | ~1.04 M | AI-Hub books + captions |

All pairs are nonetheless carved to an **identical budget** — 100 k SFT-train
(nested 1k⊂10k⊂100k), a 1k dev split, and a 200 k-pair TM datastore (≤ that
where the pool is smaller: en↔ko ~161 k, ja↔zh ~155 k) — so the *quantity* the
model and retriever see is held fixed; only the *provenance/register* varies.

The test set (FLORES-200 devtest) is a single, fixed, encyclopedic/news domain
for all pairs.

---

## Why this is acceptable (the core argument)

**The primary contrast is within-pair, and domain is held constant within a
pair.** The study's claims concern, for each language pair, the *allocation* of
a fixed 100 M-token budget between model weights (pre-training) and a retrieval
datastore — i.e. the C0 (weights) vs C1 (dictionary-RAG) vs C2 (sentence-RAG)
comparison. For a given pair, every condition is trained and evaluated on the
*same* underlying data with the *same* domain composition; the only thing that
changes is whether the datastore content lives in the weights or behind a
retriever. The heterogeneous source mix of, say, en↔zh therefore shifts all
three of its conditions together and **does not confound the within-pair
condition contrast**, which is the quantity we report and aggregate.

Concretely, we report **per-pair condition deltas** (e.g. ΔBLEU = C2 − C0) and
aggregate those deltas across pairs. Because each delta is computed within a
pair, a paired / mixed-effects analysis with **pair as a blocking (random)
factor** mathematically nets out cross-pair domain differences. The main result
("allocating budget to a retrieval datastore changes translation quality by X")
is robust to the imbalance.

---

## Where the imbalance genuinely bites (state plainly)

1. **Cross-pair comparisons are confounded.** Statements of the form "retrieval
   helps zh more than ja" cannot be made causal: en↔zh carries UN + wiki +
   everyday data while en↔ja is purely encyclopedic, so a between-pair
   difference mixes a *language* effect with a *domain* effect. We therefore
   present cross-pair numbers descriptively and do not attribute between-pair
   differences to language.

2. **Domain-match to the test set is uneven.** FLORES is encyclopedic/news.
   Pairs whose training data is wiki-heavy (en↔ja, and the wiki-majority en↔zh)
   are better matched to the test domain than the AI-Hub pairs (ko↔zh, ko↔ja,
   and the books half of en↔ko), inflating their *absolute* scores. Because the
   C2 datastore is drawn from the same source as training, the *size* of the
   retrieval gain partly reflects train↔test domain alignment — an interaction
   we surface rather than average away.

3. **C1 (dictionary-RAG) is unavailable for ja↔zh** (no direct ja–zh
   dictionary; a Korean/English pivot is infeasible — the source-side corpora
   are disjoint, see `DATA_SOURCES.md` §6). ja↔zh is reported on C0 + C2 only.

---

## What we do about it

- **Transparency.** Promote the per-pair source/register table
  (`DATA_SOURCES.md`) to a paper table; release per-ingest data cards.
- **Claim scoping.** Lead with within-pair condition contrasts; treat absolute
  and cross-pair scores as descriptive.
- **Statistics.** Analyze per-pair condition deltas with pair as a blocking
  factor (paired tests / mixed-effects), which controls for cross-pair domain.
- **(Optional) domain-matched robustness subset.** WikiMatrix is available for
  the four English-/Japanese-side pairs that have it (en↔zh, en↔ja, en↔ko,
  ja↔zh). A WikiMatrix-only re-run of those pairs yields a *domain-comparable*
  cross-pair view; the Korean-pivot pairs (ko↔zh, ko↔ja) have no WikiMatrix and
  cannot join, so this check is partial but reviewer-proofing.

**Framing.** Heterogeneous per-pair sources are the norm in multilingual MT
(WMT, OPUS- and FLORES-based studies); perfect cross-pair balance is impossible
here (en↔ja has no UN data; the Korean-pivot pairs have no encyclopedic data).
The contribution is a *within-pair* budget-allocation finding, for which the
imbalance is a documented characteristic rather than a confound.
