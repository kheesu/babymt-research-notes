# 3. Method

> Draft. Describes the fixed-budget design, the five conditions, the retrievers, and the prompt
> format. Exact split sizes under the path-2 re-pretrain are `‹PENDING›`.

## 3.1 The fixed-budget allocation problem

We treat the BabyLM budget as a single pool of **unique tokens** that *all* model-facing data must
share:

$$ \text{budget} \;=\; \bigl|\,\mathrm{unique}\bigl(\mathcal{D}_{\text{pretrain}} \cup \mathcal{D}_{\text{SFT}} \cup \mathcal{D}_{\text{store}}\bigr)\,\bigr| \;\le\; 100\text{M}. $$

Epochs and repeated retrieval do **not** multiply the count — only *unique* tokens consume budget.
This makes the central comparison an **allocation**: a no-retrieval system (C0) folds what would be
datastore tokens back into pretraining; a retrieval system (C1/C2) holds the same tokens in the
retriever. Same data, same budget — the only difference is whether a token is **baked into weights**
or **held in a lookup table**. The allocation axis (how much of the fixed 100M goes to the
datastore `D` vs. pretraining) is the primary independent variable; budget sizes are `‹PENDING›` the
re-pretrain decision (see `8. Limitations` and the project README).

> **Operationalization note.** "Unique tokens" is *our* reading of BabyLM's additive-budget rule
> (the official rule counts "words"/"tokens"); we adopt it because it is the only accounting under
> which an epoch-trained model and a multiply-queried datastore are charged comparably. We state
> this explicitly rather than claim official compliance.

## 3.2 Conditions

All conditions share one architecture, one tokenizer, and one frozen decoding configuration (§4);
they differ only in **what, if anything, is retrieved and prepended to the source before
translation**. Retrieval context is injected at the prompt level (in-context), not via
distribution interpolation.

| | Condition | Prepended context | Retriever | Role | Trained? |
|---|---|---|---|---|---|
| **C0** | `baseline`     | — (none)                                   | —                            | weights-only control | yes |
| **C1** | `dict_rag`     | dictionary entries (`src → tgt`) for *rare* source words | exact lookup + frequency gate | parametric+lexical memory | yes |
| **C2** | `sent_rag`     | nearest parallel-sentence example(s)       | FAISS over the TM datastore  | parametric+phrasal memory | yes |
| **C3** | `random_rag`   | random dictionary entries (no source-word overlap) | random, count-matched to C1 | **prompt-length control** | inference-only (C1 ckpt) |
| **C4** | `oracle_rag`   | dictionary entries whose target appears in the reference | oracle (eval-only) | **upper bound** on retrieval value | inference-only (C1 ckpt) |

**Why C3 and C4 are load-bearing, not optional.** Because retrieval can backfire in low-resource MT
\citep{hao2023rethinking}, any C1/C2 gain must be attributed to *retrieval quality*, not to the mere
presence of extra prompt tokens. **C3** prepends the *same number* of dictionary entries as C1 but
chosen at random (excluding entries whose source word actually appears in the sentence); if C1 ≈ C3,
the "gain" was just prompt length. **C4** prepends only entries whose target translation is in the
reference, giving an oracle upper bound on how much correct lexical retrieval could possibly help.
C3 and C4 **reuse the C1 checkpoint** and change only the retriever at inference — isolating
retrieval from generation. (A legacy FAISS *dictionary* retriever and a combined `dict_sent_rag`
exist in the codebase but are **out of the experimental grid**.)

## 3.3 Retrievers

**Dictionary retrieval (C1) and its frequency gate.** C1 tokenizes the source sentence
(language-aware: kiwipiepy for Korean lemmas, jieba for Chinese, fugashi for Japanese), looks up
exact (lowercased) dictionary matches, **keeps only rare words**, and caps at `top_k` entries. "Rare"
is defined by a **rank-based bottom-quartile gate**: within the active SFT budget split, unique
source words are ranked by frequency and the rarest 25% are eligible. This same rare-word set drives
both the C1 retrieval gate and the rare-word-accuracy metric in evaluation (§4), so the two are
consistent by construction. The intuition (cf. \citealp{ghazvininejad2023dictionary}) is that
common words are already learned by the weights; retrieval should spend its budget on the long tail.

**Sentence retrieval (C2).** C2 embeds the source with **LaBSE** and retrieves nearest neighbours
from a per-pair **FAISS** index over the translation-memory datastore, prepending the retrieved
source–target example(s). The datastore is capped per pair (target 200k pairs, lower where the pool
is smaller) and its tokens are charged to the budget (§3.1).

**Random (C3) and oracle (C4).** C3 samples `top_k` dictionary entries uniformly at random,
excluding any whose source word occurs in the sentence, with a per-sentence deterministic sub-seed
for reproducibility. C4 returns a dictionary entry only when one of its target translations appears
in the reference. Because C4 requires the reference, it is **eval-only**; the oracle module is
**firewalled from the training code** by module-, constructor-, and AST-level guards so reference
information cannot leak into any trained model.

**Determinism.** Given a fixed seed, the augmenter (`src/rag/augmenter.py`) produces identical
context for a given source sentence across runs; the `mode` argument selects the dictionary
retriever (`exact`/`random`/`oracle`) while all modes share the same FAISS sentence retriever for C2.

## 3.4 Prompt format

Each example is rendered as a single sequence: any retrieved context (dictionary lines or a parallel
example), then the source sentence, then a translation marker, then the target. During SFT the loss
is computed **on the target tokens only** (the context and source are masked), so retrieval shapes
conditioning without being a prediction target. The exact template and `top_k` are in `10. Appendix`.

## 3.5 Model

A single architecture is used for every condition and pair: a **GPT-2-style decoder-only Transformer,
8 layers, 512 hidden, ≈42M parameters** (preset name `custom-50m`; the ~42M figure is the true
count), trained from scratch. The tokenizer is a **corpus-driven BPE with a 32k vocabulary** trained
on the pretraining corpus. We deliberately keep the model fixed across conditions so that any
difference is attributable to the budget allocation, not to architecture.

**Per-pair base selection.** Each translation pair is fine-tuned from a pretrained base whose BPE
covers the pair's scripts (e.g. {ko,zh} → the ko-zh base, {ko,ja} → the ja-ko base). Pretraining on
more languages means fewer tokens per language under the fixed budget (≈50M/lang for a 2-language
base, ≈33M for 3, ≈25M for 4); this gradient supports an optional **base-coverage ablation**
(bilingual vs. trilingual vs. quadrilingual base) on per-pair quality.

> **Path-2 status.** The 11 bases described in the notes were each pretrained at the full 100M
> budget and are **retired** under the fixed-total accounting; the reported study re-pretrains at a
> reduced budget so that pretraining + SFT + datastore fit within 100M. C0 and C1/C2 may therefore
> use *different* bases (C0 gets the datastore's tokens added to pretraining). Final budgets:
> `‹PENDING›`.

---
*Sources: `src/rag/{augmenter,exact_retriever,oracle_retriever,word_freq}.py`, `2 Design/{Models & Data, Conditions/C0–C4}.md`, `6 Decisions/2026-06-26 Path-2...md`, `PHASE3.md`.*
