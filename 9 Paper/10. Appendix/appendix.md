# Appendix

> Draft. Reference material that supports but does not belong in the main body.

## A. Data card (provenance, licenses, counts)

Canonical source: `DATA_SOURCES.md`. **FLORES (test) is held out and not counted against the budget.**

### A.1 Pretraining corpus
| Source | Access | License | Langs |
|---|---|---|---|
| Wikipedia | `wikimedia/wikipedia` (20231101) | CC-BY-SA-4.0 | en/zh/ko/ja |

### A.2 Parallel data (SFT + datastore)
| Source | Access | License | Domain | Volume (verbatim) |
|---|---|---|---|---|
| AI-Hub MT corpora (humbook/scibook/captions) | AI-Hub (NIA) bulk | AI-Hub research terms — ⚠ verify redistribution | literary/academic/broadcast | ko-zh ≈1,007,779; ko-ja ≈1,042,839; ko-en ≈213,688 |
| MultiUN | `Helsinki-NLP/multiun` | UN public (cite Eisele & Chen 2010) | legal/diplomatic | en↔zh ≈9–10M available |
| Tanaka | `hpprc/tanaka-corpus` | CC-BY (Tatoeba lineage) | everyday | en↔ja ≈148k |
| Tatoeba | API / bulk TSV | CC-BY 2.0 FR / CC0 (attribution) | everyday | zh-ja 14,259; en-zh 74,606 |
| WikiMatrix v1 | OPUS | CC-BY-SA (cite Schwenk et al. 2021) | Wikipedia (≈FLORES) | LASER margin ≥1.04; zh-ja built 256,579 |

### A.3 Dictionaries (C1)
| Source | License | Pair | Entries |
|---|---|---|---|
| krdict (NIKL) | CC-BY-SA 2.0 KR — ⚠ verify | ko↔zh / ko↔ja | 108,686 / 168,532 (+reverse) |
| CC-CEDICT | CC-BY-SA-3.0 | en-zh | 152,390 |
| psyche/dictionary_koen | CC-BY-SA-4.0 | en-ko | 125,906 |
| JMdict_e (EDRDG) | CC-BY-SA-4.0 (cite Breen) | en-ja | 200,000 (cap) |

*No direct zh↔ja dictionary exists → zh↔ja is C0/C2 only.*

### A.4 Test set
| Source | License | Size |
|---|---|---|
| FLORES-200 devtest (`haoranxu/FLORES-200` mirror) | CC-BY-SA-4.0 (cite NLLB) | 1,012 sentences/pair, all 12 directed pairs |

## B. Data preprocessing decisions

- **Chinese script normalization (OpenCC `t2s`, opencc 1.3.1).** Web sources carry Traditional
  Chinese (Tatoeba en-zh 37.3%, WikiMatrix ja-zh 42.4%) while AI-Hub, krdict, and FLORES are 100%
  Simplified. We normalize all training zh to Simplified to match the test set and the `zh` sacreBLEU
  tokenizer (en-zh: 44.5% of segments converted; zh-ja: 68.6% → 0.0% Traditional after); the test set
  is left untouched. `t2s` is a no-op on already-Simplified text.
- **Length-ratio filter (per-side units).** CJK measured in characters, Latin in words. Fixed an
  over-aggressive filter that dropped ~77% of MultiUN en-zh down to ~13%; CJK↔CJK and Latin↔Latin
  outputs are byte-identical to the prior filter (33 preprocessing tests pass).
- **Cleaning / validity.** NFKC, HTML/control/zero-width strip; target-script ratio ≥0.30 for
  ko/zh; copy-through rejection; exact dedup.
- **Contamination gate.** Exact dedup + semantic (LaBSE) leakage audit certify **0 FLORES leakage**
  into any training file.
- **zh↔ja pivot infeasibility.** AI-Hub assigned each Korean document to a single target language;
  ko-zh and ko-ja share 4 sentences by text and 0 by id, so Korean-pivoting zh↔ja is infeasible
  (English-pivot also fails) — zh↔ja uses direct WikiMatrix + Tatoeba sources.

## C. Model & training hyperparameters

| | |
|---|---|
| Architecture | GPT-2-style decoder, 8 layers, 512 hidden, ≈42M params (preset `custom-50m`) |
| Tokenizer | corpus-driven BPE, 32k vocab |
| Pretraining | from scratch, bf16, to convergence (ko-zh val 7.46→3.98; ja-ko 7.46→3.77) |
| SFT | 10 epochs, batch 32 (16×2 grad-accum), bf16, dynamic padding, loss on target only |
| SFT hyperparameters | frozen from a small LR×epochs sweep on one C0 cell; held fixed across all cells |
| Per-pair base | base whose BPE covers the pair's scripts (e.g. {ko,zh}→ko-zh base) |
| Compute | ~56 min / 100k cell on an A6000, 93–97% GPU util (compute-bound) |

## D. Decoding configuration (frozen across all conditions/budgets/seeds)

`beam=4`, `early_stopping=true`, `length_penalty=1.0`, no sampling, `repetition_penalty=1.0`,
`no_repeat_ngram_size=3`, `max_new_tokens=256`.

**`no_repeat_ngram_size=3` pilot A/B** (ko→zh, C0, 100k, FLORES n=1012, beam 4):

| | chrF | BLEU | degenerate loops | near 256-tok cap |
|---|---|---|---|---|
| without | 13.72 | 14.07 | 27 (2.7%) | 20 |
| with | 14.47 | 14.53 | 0 | 0 |

## E. Prompt format

`‹retrieved context: dict lines `src → tgt`, or a parallel example src/tgt›` + `‹source sentence›` +
`‹translation marker›` + `‹target›`. SFT loss masks everything but the target tokens. `top_k` and the
exact template string: `‹PENDING — pull verbatim from configs/rag.yaml and src/rag/augmenter.py›`.

## F. Metric & significance details

- BLEU/chrF via sacreBLEU; `tokenize=zh` for Chinese targets; character-level for Korean. Secondary:
  chrF++, TER, COMET (`Unbabel/wmt22-comet-da`).
- Targeted: rare-word accuracy (bottom-quartile dict-covered words), terminology accuracy, entity
  accuracy (gold `entities_{pair}.tsv`).
- Headline test: per-seed paired delta vs. C0 on shared FLORES sources; aggregate across 3 seeds with
  Student-t 95% CI (df=2, t=4.303); significant iff CI excludes 0. Per-run paired bootstrap
  (sacreBLEU `paired_bs`) with 95% CI; Benjamini–Hochberg FDR over per-seed p-values.

## G. Ethics statement (draft)

All datasets are used under their stated licenses for research; **AI-Hub redistribution terms must be
verified before any data release** (see A.2). Training data may contain biases inherent to Wikipedia,
UN documents, books, and web-mined corpora; our models are small research artifacts not intended for
deployment, and absolute translation quality is low. No human subjects or private data are involved.
Compute and its energy footprint are reported (GPU-hours summed from per-run timing logs).

## H. Reproducibility

Per-run `*_timing.json` and an `environment.json` (Python, platform, git commit, GPU, `pip freeze`)
accompany the artifacts; all paper figures/tables are script-generated from `results/master.csv` plus
the data card so reported totals are reproducible. Code: `src/{data,rag,training,inference}`, configs
in `configs/`, pipeline driver scripts in `scripts/`.

---
*Sources: `DATA_SOURCES.md`, `PHASE3–5.md`, `4 Experiments/*`, `5 Findings/*`, `6 Decisions/*`, `memory/*`.*
