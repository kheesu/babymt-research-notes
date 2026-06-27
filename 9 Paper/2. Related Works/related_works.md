# 2. Related Work

> Draft. Every citation here has a matching entry in `9. References`. Entries flagged `(verify)`
> there still need a final bibliographic / numerical check before submission.

We organize prior work along the two axes our study sits between: **retrieval as a substitute for
parameters** (the scaling-laws / nonparametric-memory line) and **retrieval for machine
translation** (sentence- and dictionary-augmented MT), and then situate the **BabyLM** budget
framework that makes the trade-off concrete.

## 2.1 Parametric vs. nonparametric memory

The thesis that knowledge can be offloaded from weights to an external store begins with **kNN-LM**
\citep{khandelwal2020knnlm} and is pushed to scale by **RETRO**, whose 7.5B-parameter model
retrieving from a 2T-token database matches GPT-3-class models with ~25× fewer parameters on the
Pile \citep{borgeaud2022retro}. **MassiveDS** \citep{shao2024massiveds} makes the budget intuition
explicit: a smaller model with a 1.4T-token datastore can beat a larger LM-only model, and datastore
size helps monotonically without saturation — suggesting the datastore is itself a scaling axis.

The work closest to ours is **Singh et al. \citeyearpar{singh2026memorize}, "To Memorize or to
Retrieve: Scaling Laws for RAG-Considerate Pretraining"**, which studies exactly the
pretrain-corpus-vs-retrieval-store trade-off across 30M–3B parameters and up to 100B tokens, varying
the store 1–20×. Their finding — retrieval helps, but its marginal utility depends on model scale,
task type, and *degree of pretraining saturation* — is the large-scale, general-LM counterpart to
our question. We differ on five axes that we argue make our regime non-redundant and, if anything,
the harder test of their own caveat: **(a)** we study **machine translation**, not general LM;
**(b)** **distant CJK + English** pairs, not English; **(c)** a **single fixed 100M *total* budget**
(the BabyLM cap), not open scaling curves with separate ledgers; **(d)** **dictionary + sentence-TM**
retrieval, not generic chunk retrieval; and **(e)** the **~42M, undertrained** regime — precisely
where their "depends on saturation" caveat should bite hardest, and where the substitution may fail.

## 2.2 Retrieval-augmented and translation-memory MT (our C2)

**kNN-MT** \citep{khandelwal2021knnmt} attaches a token-level datastore to a frozen MT model and
interpolates its predictions, with no training; it reports gains including en↔zh and zh→en, and
enables domain adaptation purely by swapping datastores. **Adaptive kNN-MT**
\citep{zheng2021adaptive} learns to weight retrieval per token to suppress noise. **Multilingual
kNN-MT** \citep{stap2023multilingual} shares one datastore across languages, helping low-resource
pairs more than high-resource ones (+3.6 vs. +0.5 BLEU) and shrinking the store by linguistic
similarity — directly relevant to a budget-constrained multi-CJK store. Our C2 differs in being
**sentence-level** translation-memory retrieval injected into the prompt, rather than token-level
distribution interpolation.

The central cautionary result is **Hao et al. \citeyearpar{hao2023rethinking}** (building on
\citealp{cai2021neural}): TM-augmented NMT helps with abundant data but **underperforms vanilla NMT
in low-resource conditions**, which they attribute to a bias–variance trade-off. Our entire study is
designed around the possibility that this result does or does not extend to the tiny-base,
fixed-budget setting — hence our oracle/random controls.

## 2.3 Dictionary- and lexicon-augmented MT (our C1)

The strongest single precedent for dictionary retrieval is **Duan et al.
\citeyearpar{duan2020bilingual}**: using only a bilingual dictionary plus monolingual data, their
"anchored training" matches or beats supervised SMT trained on >4M parallel sentences on distant
en↔zh / en↔ru — exactly the distant-pair, scarce-parallel-data regime we operate in. **DiPMT**
\citep{ghazvininejad2023dictionary} injects phrase-level dictionary entries into the prompt and
deliberately skips the most frequent words, mirroring our **bottom-quartile rare-word frequency
gate**. **Chain-of-Dictionary** \citep{lu2023chain} chains multilingual dictionaries; **DragFT**
\citep{zhang2024dragft} combines dictionary prompting, RAG few-shot selection, and fine-tuning — the
one prior work bundling both of our retrieval arms, but at LLM scale and with no shared budget.
Lexically-constrained *decoding* (\citealp{hokamp2017lexically}; \citealp{post2018fast}) and
continuous learning from dictionaries \citep{niehues2021continuous} are decode-time alternatives to
our train-time C1.

## 2.4 In-context and low-resource / distant-pair MT

For very low-resource pairs, retrieval-augmented prompting of LLMs is the dominant recipe:
\citet{merx2024low} combine dictionary and parallel-sentence retrieval for Mambai, and
\citet{pei2025understanding} show on Manchu that **good dictionaries and parallel examples help
substantially while grammar books hardly do** — a clean C1-vs-C2-style contrast. \citet{chitale2024empirical}
find ICL for MT is example-driven rather than instruction-driven, informing how we format
demonstrations. On CJK specifically, \citet{factors2021korean} show that for unsupervised ko↔ja NMT,
**typological similarity matters more than shared vocabulary** (ja↔ko succeeds where ko↔en collapses,
despite far less shared vocabulary), and \citet{zhang2024lowresource} survey Chinese-centric
low-resource MT.

## 2.5 The BabyLM Challenge and our budget framing

The **BabyLM Challenge** \citep{warstadt2023findings, choshen2026babylm} caps the training budget to
a developmentally plausible scale and — critically for us — stipulates that the budget is a
**total across the whole pipeline**: any data used to train the LM *or auxiliary models* counts
\citep{warstadt2023findings}. This is the rule that lets us count a retrieval datastore against the
same budget as the model.

Two honest caveats frame our positioning. First, the **2026 Multilingual track is English/Dutch/
Chinese *pretraining* evaluated by competence (BLiMP-style) tasks** \citep{choshen2026babylm} — not
translation, and it does not include Korean or Japanese; we therefore *adapt the budget rule outside
the official track* rather than competing within it. Second, the official rule is stated in
"words"/"tokens" (byte-premium-adjusted), never in "unique tokens"; we present our **unique-token
accounting as an operationalization** of the additive-budget principle, and say so explicitly. We
view BabyMT as a study that *uses* the BabyLM budget discipline to make the memorize-vs-retrieve
trade-off concrete, not as a submission to the multilingual competition track.

## 2.6 What is new here

To our knowledge, no prior work asks the memorize-vs-retrieve question for **MT** while (i) charging
the retrieval datastore to the **same fixed total budget** as the model and (ii) doing so at
**BabyLM scale** on **distant CJK pairs**, with (iii) **random and oracle retrieval controls** that
isolate retrieval quality from prompt-length effects. \citet{singh2026memorize} is the nearest
neighbour in spirit; we are its small-model, translation-specific, fixed-budget complement, and a
direct test of the low-resource cautionary result of \citet{hao2023rethinking}.

---
*Sources: `7 Literature/Literature Synthesis & Matrix.md` (24-row matrix), the per-paper notes in `7 Literature/`, and `memory/literature-deep-research-2026-06.md`.*
