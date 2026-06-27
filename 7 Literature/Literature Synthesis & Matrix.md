---
tags: [literature, moc]
status: draft
created: 2026-06-26
updated: 2026-06-26
---

# Literature Synthesis & Matrix

Cited related-work map for BabyMT, organized by the seven areas of the research
brief, ending in a paper-by-paper [[#Comparison matrix|comparison matrix]].
Anchored on the **path-2** framing ([[2026-06-26 Path-2 budget allocation study]]):
*under a fixed total data budget, is a token better spent on model weights
(pretraining) or on a nonparametric retrieval datastore?*

> [!info] How this was produced
> Deep-research harness (fan-out web search → fetch → 3-vote adversarial
> verification → synthesis), 2026-06-26. 23 primary sources fetched, 108 claims
> extracted, **25 verified** (24 unanimous 3-0, one 2-1; 0 refuted). Claims tagged
> *verified* below survived that pass; bibliographic details added afterward by
> direct fetch are tagged *(bib unverified)* or *(verify)* where the canonical
> citation is not yet pinned. All sources were primary (official challenge
> pages/CfPs, peer-reviewed ACL/EMNLP/ICLR/ICML papers) — no blog-quality evidence.

---

## TL;DR — what matters for BabyMT

1. **The venue is real, but our framing diverges from the official one.** BabyLM
   2026 (4th iteration, EMNLP 2026 Budapest, direct deadline **2026-07-15**) adds a
   **Multilingual track — but it covers only English/Dutch/Chinese and is
   *pretraining* evaluated by linguistic-competence tasks, not translation**, and
   not ko/ja. BabyMT is a CJK+English *translation* study that *adapts* the budget
   rule outside the official track. Say so explicitly. *(verified)*
2. **"100M unique tokens" is our operationalization, not the rule.** The rule says
   "100M **words**" (Strict) / "10M words" (Strict-Small) / "100M **tokens**"
   (Multilingual, Byte-Premium-adjusted). "Unique tokens" appears nowhere. *(verified)*
3. **The budget *is* additive across the whole pipeline** — any data used to train
   the LM **or auxiliary models** counts. This is exactly the accounting path-2
   needs to count pretrain + SFT + datastore against one cap. Cite the 2023 Findings
   + FAQ for this. *(verified)*
4. **Retrieval-substitutes-for-parameters is well established at large scale** —
   kNN-MT, RETRO, MassiveDS — but **always with a strong/large base model and
   *without* a shared fixed budget.** No one counts datastore tokens against the
   pretraining budget. That gap **is** BabyMT's contribution. *(verified)*
5. **⚠ Closest prior work / partial-scoop risk:** **Singh et al., "To Memorize or
   to Retrieve" (arXiv 2604.00715, Apr 2026)** studies the *pretraining-corpus-size
   vs retrieval-store-size* tradeoff across **30M–3B params** — overlapping our
   scale and our core question. Must read, cite, and differentiate (they: general
   LM, English, scaling laws, no fixed total budget; us: MT, distant CJK pairs,
   BabyLM 100M *total* cap, dict+TM retrieval).
6. **⚠ Biggest empirical risk:** TM/retrieval augmentation **helps in high-resource
   but *fails* (absolutely underperforms vanilla) in low-resource MT** — a
   variance-bias effect (Hao et al. 2023; Cai et al. 2021). BabyMT lives squarely in
   that low-resource, distant-pair regime. Pre-register this risk; the random/oracle
   controls (C3/C4) are essential, not optional.
7. **The dictionary arm has strong precedent on distant pairs:** Duan et al. (ACL
   2020) match supervised SMT (4M+ pairs) on en↔zh / en↔ru using only a bilingual
   dictionary — direct support for C1. *(verified)*

---

## 1 · The BabyLM Challenge (venue, budget rules)

The 2026 BabyLM Workshop is the **4th iteration** of the challenge, co-located with
**EMNLP 2026 (Budapest)**, with a **direct submission deadline of 2026-07-15** (ARR
2026-05-25) — consistent with our target. *(verified, 3-0)* It newly introduces a
**Multilingual track based on BabyBabelLM**, but that track **covers only English,
Dutch, and Chinese**, frames the task as **multilingual *pretraining*** evaluated via
BLiMP-style functional/formal linguistic-competence tasks, and contains **no
translation task**. Korean and Japanese are *not* in the competition track (they
exist in the broader 45-language BabyBabelLM dataset). *(verified, 3-0)* → **BabyMT
does not fit the official multilingual track on either language set or task**; the
related-work section must position BabyMT as adapting the *budget rule* to a
translation setting outside the official track.

**Budget rules** *(all verified, 3-0)*:
- Expressed as "**100M words or less**" (Strict), "**10M words or less**"
  (Strict-Small), "**100M tokens**" for Multilingual (per-language counts adjusted
  by Byte Premium: nl 1.0516, zh 0.9894, en 1.0). **"Unique tokens" never appears**
  — our 100M-unique-token cap is an interpretation, flag it as such.
- The budget is a **TOTAL across the entire pipeline**: any language data used to
  train the LM *or auxiliary models* (taggers, parsers, teacher/reranking/augmentation
  models) counts. The 2023 Findings give the canonical PTB example; the FAQ confirms
  off-the-shelf POS taggers are prohibited. **This is the load-bearing rule for
  path-2.** (Caveat: scoped to *language* data — non-linguistic audio/vision/code
  were encouraged and uncounted.)
- Stable across editions: 2023 (1st, three tracks Strict/Strict-Small/Loose,
  English-only), 2024 (2nd, added a 100M Multimodal+image track, still no
  multilingual), 2026 (4th, adds Multilingual). *(verified; one sub-claim about the
  2023 "Loose=text-only" wording was 2-1, immaterial.)*

Key sources: [[#S1]] BabyLM 2026 CfP, [[#S3]] 2023 Findings, [[#S4]] 2024 Findings,
[[#S?]] BabyBabelLM.

## 2 · Data-efficient / sample-efficient pretraining

> [!warning] Coverage gap
> No claim in the verified set substantiates the general "data-efficient small-model
> pretraining" literature (curriculum learning, distillation, architecture-for-small-
> data, BabyLM winning systems). The challenge's own **Findings papers** (2023/2024)
> are the entry point and are cited above; a dedicated follow-up search is warranted
> if the paper needs a strong "what works under 100M words" subsection.

## 3 · Retrieval-augmented MT (the C2 / nonparametric arm)

**kNN-MT** (Khandelwal et al., **ICLR 2021**, arXiv 2010.00710) is the canonical
token-level nonparametric method: a frozen MT model is augmented with a token-level
nearest-neighbor datastore at inference with **no additional training**, interpolating
`(1−λ)·p_LM + λ·p_kNN`. Its subtitle ("Generalization through Memorization") *is* the
parametric-vs-nonparametric framing. Concrete budget-vs-quality evidence *(verified,
3-0)*: +1.5 BLEU De-En (770M-pair store); for one 418M multilingual model, per-pair
datastores give **en-de +3.02, zh-en +3.28, en-zh +3.41 BLEU** (incl. distant Chinese
pairs); a News model adapts to Medical/Legal *purely by swapping datastores*,
matching or beating in-domain-trained models. **Caveat (verified):** the base is a
large, fully-trained SOTA system, and kNN-MT trades datastore for in-domain *training
data* — it **does not** enforce a fixed total budget, so it motivates but does not
pre-empt path-2. Note also kNN-MT is **token-level** whereas BabyMT's C2 is
**sentence-level TM/RAG** (LaBSE + FAISS).

Efficient/adaptive variants *(verified, 3-0)*: **Adaptive kNN-MT** (Zheng et al.,
**ACL 2021** short, arXiv 2105.13022) adds a light Meta-k Network choosing k per token
to filter retrieval noise, beating vanilla on 4 benchmarks. **Multilingual kNN-MT**
(Stap & Monz, **EMNLP 2023**, arXiv 2310.14644) shares one datastore across languages:
**up to +3.6 BLEU low-resource**, +0.5 high-resource, and can shrink the store to a
quarter (5.3× faster) via linguistic similarity — directly relevant to a
budget-constrained, multi-CJK datastore.

> [!danger] The cautionary result BabyMT must confront
> **TM-augmented NMT helps in high-resource but FAILS in low-resource** (Hao et al.,
> **ACL Findings 2023**, arXiv 2306.06948; orig. Cai et al. 2021). Mechanism: lower
> bias but **higher variance** under data scarcity. Hard numbers (JRC-Acquis De→En):
> TM 63.76 vs vanilla 60.83 high-resource (gain) **but 53.92 vs 54.54 low-resource
> (loss)**. *(verified, 3-0)* BabyMT operates exactly where retrieval has backfired —
> open empirical question whether a *tiny, underfit* base flips this (bias reduction
> could dominate). See [[#Open questions]].

Recent RAG-MT at LLM scale: **DragFT** (Zhang et al., arXiv **2402.15061**, 2024) —
dictionary-enhanced prompting + RAG few-shot selection + fine-tuning for
*domain-specific* MT (combines our C1 + C2 ideas, at LLM scale). **RAG-MT with
Unstructured Knowledge** (Wang, Meng, Zhang & Zhou, arXiv **2412.04342**, Findings
EMNLP 2025) — retrieves from *unstructured multilingual documents* (not paired
corpora/KGs); introduces the **RAGtrans** benchmark (169K samples). Both were the
informally-named README references; canonical citations now confirmed.

## 4 · Dictionary-augmented & lexically-constrained NMT (the C1 arm)

**Duan et al., "Bilingual Dictionary Based NMT without Using Parallel Sentences"**
(**ACL 2020**, arXiv 2007.02671) — this is the README's "Bilingual Dictionary NMT".
Uses *only* a ground-truth bilingual dictionary + monolingual corpora via "anchored
training". On **distant pairs (en-zh, en-ru) where unsupervised MT collapses** (zh→en
1.50 BLEU), it **matches supervised SMT trained on 4M+ parallel sentences** (zh→en
30.12 vs 31.86; en→zh 17.05 vs 16.55 — beating SMT). *(verified, 3-0)* Strongest
single precedent for C1 on our exact pair type.

Prompt-time dictionary injection (the modern C1 analogue, LLM scale, *not* verified
numerically — bib confirmed): **DiPMT** (Ghazvininejad et al., 2023, arXiv 2302.07856)
injects dict entries for a *subset* of rarer words, deliberately skipping the 500
most-frequent — mirrors BabyMT's bottom-quartile frequency gate. **Chain-of-Dictionary
(CoD)** (Lu et al., 2023, arXiv 2305.06575) chains multilingual dictionaries for a
word subset.

Lexically-constrained decoding (force dictionary terms into output at decode time):
**Hokamp & Liu (Grid Beam Search, ACL 2017, arXiv 1704.07138)** and **Post & Vilar
(Dynamic Beam Allocation, NAACL 2018, arXiv 1804.06609 / N18-1119)** — an alternative
to prompt injection BabyMT could contrast against. **Niehues (EACL 2021, arXiv
2102.06558)** uses bilingual dictionaries for *continuous* post-deployment knowledge
integration. *(bib confirmed; results unverified)*

## 5 · In-context / prompting MT (how examples & glossaries are injected)

**Chitale, Gala & Dabre, "An Empirical Study of In-context Learning in LLMs for MT"**
(**Findings of ACL 2024**, 2024.findings-acl.440): ICL for MT is **example-driven, not
instruction-driven** — demonstrations carry the signal even under contrastive/random
instructions; a related task with the same target distribution can suffice;
perturbations sometimes act as regularizers. Informs how BabyMT formats C1/C2 context.
*(bib confirmed; not in verified claim set)*

**Merx et al., "Low-Resource MT through Retrieval-Augmented LLM Prompting: A Study on
Mambai"** (2024, arXiv 2404.04809): few-shot prompting injecting **dictionary entries +
parallel sentences (TF-IDF + semantic retrieval)** — directly mirrors C1+C2, at LLM
scale on a truly low-resource pair. Headline: **stark test-set sensitivity** (BLEU up
to 21.2 on manual-derived test vs ≤4.4 on a native-speaker test) — a warning about
eval-set construction. *(bib confirmed)*

## 6 · Parametric vs nonparametric memory (the path-2 anchor)

**kNN-LM** (Khandelwal et al., **ICLR 2020**, arXiv 1911.00172) — the LM analogue of
kNN-MT; retrieval datastore over training set, no extra training; the origin of
"generalization through memorization". *(bib confirmed)*

**RETRO** (Borgeaud et al., **ICML 2022**): a **7.5B** model + **2-trillion-token**
retrieval DB matches **GPT-3 / Jurassic-1 (~175–178B)** on the Pile with **~25× fewer
parameters** — the cleanest "retrieval substitutes for parameters" demonstration.
*(verified via fetch; central)*

**Shao et al., "Scaling Retrieval-Based LMs with a Trillion-Token Datastore"**
(**arXiv 2407.12854**, MassiveDS, Jul 2024): a **smaller model + large datastore beats
a larger LM-only model** on knowledge-intensive tasks; datastore size improves
performance **monotonically without saturation** → datastore scale belongs in
efficiency/compute-optimal accounting. Strong support for path-2. *(bib confirmed)*

> [!important] ⚠ Closest prior work — read & differentiate
> **Singh et al., "To Memorize or to Retrieve: Scaling Laws for RAG-Considerate
> Pretraining"** (**arXiv 2604.00715**, Apr 2026): *"systematically study the trade-off
> between pretraining corpus size and retrieval store size across a wide range of model
> and data scales"* — **OLMo-2-based 30M–3B params**, up to **100B** pretraining tokens,
> retrieval store 1–20×. Three-dimensional scaling framework; retrieval consistently
> helps, but marginal utility depends on model scale, task type, and pretraining
> saturation.
> **This is the path-2 question at large scale.** BabyMT's defensible niche: (a)
> *machine translation*, not general LM; (b) *distant CJK pairs*, not English;
> (c) a **single fixed 100M *total* budget** (BabyLM-constrained), not open scaling
> curves; (d) *dictionary + sentence-TM* retrieval, not generic chunk retrieval; (e)
> the **30M-scale, undertrained** regime is exactly where their "depends on pretraining
> saturation" caveat is most acute. Cite as the primary point of departure.

## 7 · Low-resource & distant-pair (CJK) MT

- **"Factors Behind the Effectiveness of an Unsupervised NMT System between Korean and
  Japanese"** (Applied Sciences 2021, **11(16):7662**, MDPI; *authors: verify*): MASS
  pretraining → unsupervised NMT. ko↔ja reaches **BLEU 32.76 (ja→ko) / 29.07 (ko→ja)**
  while ko↔en **collapses (en→ko 3.62 / ko→en 1.10)** — even though ko-en shares far more
  vocabulary (30.9% vs 0.089% for ko-ja). Conclusion: **typological similarity matters
  more than shared vocabulary** for unsupervised NMT. *(title/venue verified; numbers from
  fetch)*
- **Low-resource NMT survey, Chinese-centric** (Zhang et al., **ACM TALLIP 2024**, DOI
  10.1145/3665244) — survey entry point for the related-work framing. *(bib confirmed)*
- **Pei, Liu, Lin, Yvon & Schütze, "Understanding In-Context MT for Low-Resource
  Languages: A Case Study on Manchu"** (**ACL 2025**, arXiv **2502.11862**): for GPT-4o on
  337 Manchu-English sentences, **high-quality dictionaries and good parallel examples
  substantially help** in-context MT, while **grammar books hardly help** (verified via
  enciphered-Manchu ablations isolating ICL from pretrained knowledge) — a clean
  C1-vs-C2 comparison on an extremely low-resource pair. *(bib verified)*

---

## Critical framing caveats (carry into the paper)

1. **Budget wording.** Official rule = "words"/"tokens", not "unique tokens" — present
   ours as an operationalization.
2. **Track mismatch.** Official 2026 Multilingual track = En/Nl/Zh *pretraining*; BabyMT
   = ko/ja/zh/en *translation*. Decide which track to actually submit to (likely
   Strict/Strict-Small text track, framed as MT) and state the adaptation. See
   [[Open Questions]].
3. **Novelty boundary.** The retrieval-replaces-parameters results (kNN-MT/RETRO/
   MassiveDS) use large bases and *no shared budget*; Singh et al. 2026 add the
   fixed-budget tradeoff but at general-LM/English scale. BabyMT's wedge = MT + distant
   CJK + a single BabyLM *total* cap. State the gap precisely; don't overclaim.
4. **Low-resource retrieval can backfire** (Hao 2023 / Cai 2021). Treat as a
   pre-registered risk; C3 random and C4 oracle controls are load-bearing.

## Coverage gaps & open questions

- **Coverage gaps** (absent from the verified set, worth a follow-up search): general
  data-efficient/small-model pretraining (§2); BabyLM *winning systems*; LaBSE,
  FLORES-200, chrF++, COMET as eval primitives were used but not independently verified
  here.
- **Open questions** (link [[Open Questions]]):
  1. Canonical bib for the §7 CJK papers (MDPI 7662, arXiv 2502.11862) — titles/authors
     not yet pinned.
  2. Does *any* prior work enforce a **shared parametric-vs-datastore budget** for *MT*?
     (Singh 2026 does for general LM — confirm none does for MT to claim novelty.)
  3. Does retrieval still help when the **base is tiny (~42M) and undertrained** — could
     bias reduction dominate the variance penalty that sinks low-resource TM? BabyMT is
     positioned to answer this.
  4. Which BabyLM track should BabyMT submit to, given ko/ja are out of the official
     Multilingual track?

---

## Comparison matrix

Substantive columns: **scale & budget** and **results & metrics** (per request);
method/retrieval-type and languages kept compact so results stay interpretable.
*V* = adversarially verified in this run; *b* = bibliography confirmed by direct
fetch, result not independently verified; *?* = citation/result still uncertain.

| # | Paper (cite · arXiv/anthology) | Method / retrieval | Scale & budget (params · train data · datastore) | Eval & metrics | Headline result | Relevance to BabyMT | Conf |
|---|---|---|---|---|---|---|---|
| 1 | Choshen et al. 2026, *BabyLM Turns 4 & Goes Multilingual* (CfP) · arXiv 2602.20092 | Shared-task rules | Budget = 100M words / 10M / 100M tokens (ML); **total across pipeline** | — (BLiMP-style competence) | 4th iteration; new ML track **En/Nl/Zh, pretraining only** | Defines venue + the additive budget path-2 needs; ko/ja out of ML track | V |
| 2 | Warstadt et al. 2023, *Findings of the BabyLM Challenge* · 2023.conll-babylm.1 | Shared-task report | 100M / 10M words, English | BLiMP, (Super)GLUE | Budget total incl. auxiliary models (PTB example) | Canonical cite for additive-budget rule | V |
| 3 | *Second BabyLM Challenge* 2024 Findings · arXiv 2412.05149 | Shared-task report | 100M/10M text + 100M multimodal | competence suite | Added image track; no multilingual yet | Edition history; budget stability | V |
| 4 | Khandelwal et al. 2021, *Nearest Neighbor MT (kNN-MT)* · ICLR 2021, arXiv 2010.00710 | Token-level kNN retrieval, frozen MT, no training | 418M base · datastore **0.77B–6.5B** tokens | De-En, multilingual, domains; **BLEU** | +1.5 BLEU De-En; **+3.0–3.4 BLEU** incl. zh-en/en-zh; domain swap = in-domain | Reference nonparametric arm; but large base, **no fixed budget** | V |
| 5 | Zheng et al. 2021, *Adaptive kNN-MT* · ACL 2021, arXiv 2105.13022 | kNN-MT + Meta-k Network (dynamic k) | + light meta-net on kNN-MT | 4 MT benchmarks; **BLEU** | Beats vanilla kNN-MT; filters retrieval noise | Noise-robust retrieval under budget | V |
| 6 | Stap & Monz 2023, *Multilingual kNN-MT* · EMNLP 2023, arXiv 2310.14644 | Shared multilingual kNN datastore | One store across langs; **¼-size** variant, 5.3× faster | multilingual; **BLEU** | **+3.6 low-res / +0.5 high-res**; store shrinkable | Multi-CJK shared, shrinkable datastore | V |
| 7 | Hao et al. 2023, *Rethinking TM-Augmented NMT* · ACL Findings 2023, arXiv 2306.06948 | Sentence-level TM retrieval | high- vs low-resource splits | JRC-Acquis etc.; **BLEU** | **TM helps high-res, FAILS low-res** (variance-bias) | ⚠ Core risk for our regime; motivates C3/C4 | V |
| 8 | Zhang et al. 2024, *DragFT* · arXiv 2402.15061 | Dict prompting + RAG few-shot + fine-tune | LLM scale (~7–13B, *verify*) · self-built domain corpora | domain MT; BLEU/COMET (verify) | Dict+RAG+FT lifts domain-specific MT | Combines C1+C2 at LLM scale | b |
| 9 | Wang et al. 2025, *RAG-MT with Unstructured Knowledge* (RAGtrans) · arXiv 2412.04342, Findings EMNLP 2025 | RAG from unstructured multilingual docs | LLMs · **RAGtrans 169K** samples | RAGtrans; MT metrics | First train/eval benchmark for unstructured-knowledge RAG-MT | README's "RAG-MT" ref; doc-level retrieval contrast | b |
| 10 | Duan et al. 2020, *Bilingual Dict-Based NMT w/o Parallel Sentences* · ACL 2020, arXiv 2007.02671 | Dictionary-anchored training, **no parallel sentences** | dict + monolingual only | en-zh, en-ru, en-fr; **BLEU** | On distant pairs **matches/beats SMT (4M+ pairs)**; UNMT collapses | Strongest C1 precedent on our exact pair type | V |
| 11 | Ghazvininejad et al. 2023, *DiPMT* · arXiv 2302.07856 | Phrase-level dict injection in prompt (rare words) | LLM, few-shot | low-res MT; BLEU/chrF | Skips top-500 freq words; helps rare-word translation | Mirrors C1 + our frequency gate | b |
| 12 | Lu et al. 2023, *Chain-of-Dictionary (CoD)* · arXiv 2305.06575 | Chained multilingual dict prompting | LLM, few-shot | MT; BLEU/chrF | Dict chains elicit translation | C1 prompt-format variant | b |
| 13 | Hokamp & Liu 2017, *Grid Beam Search* · ACL 2017, arXiv 1704.07138 | Lexically-constrained decoding | decode-time, any NMT | constrained MT; BLEU | Forces terms into output at decode | Decode-time C1 alternative | b |
| 14 | Post & Vilar 2018, *Dynamic Beam Allocation* · NAACL 2018, arXiv 1804.06609 | Lexically-constrained decoding, **O(1)** | decode-time, any NMT | constrained MT; BLEU | O(1) in #constraints (vs prior linear/exp) | Efficient decode-time C1 alternative | b |
| 15 | Niehues 2021, *Continuous Learning w/ Bilingual Dicts* · EACL 2021, arXiv 2102.06558 | Dict as post-deploy knowledge source | NMT, online | MT; BLEU | Integrate new phrases without retraining | C1 framing: dict = injectable knowledge | b |
| 16 | Chitale, Gala & Dabre 2024, *Empirical Study of ICL for MT* · Findings ACL 2024, 2024.findings-acl.440 | In-context learning analysis | LLMs, few-shot | multiple pairs; COMET/BLEU | **ICL is example-driven, not instruction-driven** | How to format C1/C2 demonstrations | b |
| 17 | Merx et al. 2024, *Low-Resource MT via RAG Prompting (Mambai)* · arXiv 2404.04809 | Few-shot dict + parallel-sentence retrieval | LLaMA-2 70B / Mixtral / GPT-4 | en→Mambai, 2 test sets; **BLEU** | BLEU 21.2 (manual) vs ≤4.4 (native) — **test-set sensitivity** | C1+C2 at LLM scale; eval-construction warning | b |
| 18 | Khandelwal et al. 2020, *kNN-LM* · ICLR 2020, arXiv 1911.00172 | Token-level kNN retrieval for LMs | LM + datastore over train set | WikiText/Pile; **perplexity** | "Generalization through memorization" | Origin of nonparametric-memory thesis | b |
| 19 | Borgeaud et al. 2022, *RETRO* · ICML 2022 | Chunk retrieval, retrieval-augmented LM | **7.5B** params · **2T-token** DB | the Pile; perplexity/LM | Matches **GPT-3/Jurassic (~175B)** with **~25× fewer params** | Cleanest "retrieval ⇒ fewer params" evidence | b |
| 20 | Shao et al. 2024, *Scaling RAG w/ Trillion-Token Datastore (MassiveDS)* · arXiv 2407.12854 | RAG, datastore scaling | model varied · **1.4T-token** datastore | knowledge-intensive + LM | **Smaller model + big store > larger LM-only**; no saturation | Datastore-as-budget; direct path-2 support | b |
| 21 | **Singh et al. 2026, *To Memorize or to Retrieve* · arXiv 2604.00715** | RAG-considerate pretraining scaling laws | **30M–3B** params · ≤**100B** tokens · store 1–20× | LM + downstream | **Pretrain-size vs retrieval-store-size tradeoff**; utility ∝ scale/saturation | ⚠ **Path-2 at large scale — closest prior work; differentiate** | b |
| 22 | *Factors Behind … Unsupervised NMT between Korean and Japanese* · Applied Sciences 2021, 11(16):7662 (authors verify) | MASS pretrain → unsupervised NMT | no parallel data | ja↔ko, en↔ko; **BLEU** | ja→ko **32.76** / ko→ja 29.07 vs ko→en **1.10** / en→ko 3.62 → typology > shared vocab | Which CJK directions are tractable without parallel data | b |
| 23 | Zhang et al. 2024, *Low-Resource NMT survey (Chinese-centric)* · ACM TALLIP, 10.1145/3665244 | Survey | — | — | Survey of low-resource NMT methods | Related-work framing entry point | b |
| 24 | Pei et al. 2025, *In-Context MT for Low-Resource: Manchu* · ACL 2025, arXiv 2502.11862 | In-context dict + parallel examples (+ grammar ablation) | GPT-4o, 337 sents | Manchu↔en; BLEU/chrF | **Dicts & parallel examples help; grammar books don't** | Clean C1-vs-C2 contrast, ultra-low-resource | b |

---

## Sources (primary, verified run)

<a id="S1"></a>**S1** BabyLM 2026 CfP — https://arxiv.org/abs/2602.20092 ·
<a id="S2"></a>**S2** babylm.github.io — https://babylm.github.io/ ·
<a id="S3"></a>**S3** 2023 Findings — https://aclanthology.org/2023.conll-babylm.1/ ·
**S4** 2024 Findings — https://arxiv.org/abs/2412.05149 ·
BabyBabelLM — https://arxiv.org/abs/2510.10159 ·
kNN-MT — https://arxiv.org/abs/2010.00710 ·
Adaptive kNN-MT — https://aclanthology.org/2021.acl-short.47/ ·
Multilingual kNN-MT — https://arxiv.org/abs/2310.14644 ·
Rethinking TM-NMT — https://arxiv.org/abs/2306.06948 ·
Duan et al. 2020 — https://aclanthology.org/2020.acl-main.143/ ·
DiPMT — https://arxiv.org/abs/2302.07856 ·
Chain-of-Dictionary — https://arxiv.org/abs/2305.06575 ·
Grid Beam Search — https://arxiv.org/abs/1704.07138 ·
Dynamic Beam Allocation — https://aclanthology.org/N18-1119/ ·
Niehues 2021 — https://arxiv.org/abs/2102.06558 ·
ICL-for-MT study — https://aclanthology.org/2024.findings-acl.440/ ·
Mambai RAG-MT — https://arxiv.org/abs/2404.04809 ·
RETRO — https://proceedings.mlr.press/v162/borgeaud22a/borgeaud22a.pdf ·
kNN-LM — https://arxiv.org/abs/1911.00172 ·
MassiveDS — https://arxiv.org/abs/2407.12854 ·
**To Memorize or to Retrieve — https://arxiv.org/abs/2604.00715** ·
Low-resource NMT survey — https://dl.acm.org/doi/10.1145/3665244 ·
ko-ja/ko-en UNMT — https://www.mdpi.com/2076-3417/11/16/7662/htm ·
Manchu-en ICL-MT — https://arxiv.org/abs/2502.11862 ·
DragFT — https://arxiv.org/abs/2402.15061 ·
RAG-MT Unstructured Knowledge — https://arxiv.org/abs/2412.04342

Related: [[Bilingual Dictionary NMT]] · [[DragFT]] · [[RAG-MT with Unstructured Knowledge]] · [[C1 dict_rag]] · [[C2 sent_rag]] · [[2026-06-26 Path-2 budget allocation study]] · [[Open Questions]]
