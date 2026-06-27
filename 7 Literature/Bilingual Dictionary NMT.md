---
tags: [literature]
status: draft
created: 2026-06-26
updated: 2026-06-26
authors: Xiangyu Duan, Baijun Ji, Hao Jia, Min Tan, Min Zhang, Boxing Chen, Weihua Luo, Yue Zhang
year: 2020
venue: ACL 2020
url: https://aclanthology.org/2020.acl-main.143/
arxiv: 2007.02671
---

# Bilingual Dictionary Based Neural Machine Translation without Using Parallel Sentences

**TL;DR:** Train MT from **only a bilingual dictionary + monolingual corpora** (no
parallel sentences) via "anchored training"; on **distant pairs it matches/beats
supervised SMT trained on 4M+ parallel sentences**. The strongest precedent for
BabyMT's C1 dictionary arm. *(adversarially verified, 3-0)*

## Relevance to BabyMT
Direct precedent for **[[C1 dict_rag]]**: it shows a bilingual dictionary alone can
anchor translation on exactly our hard pair type (distant, e.g. en-zh). BabyMT differs
by (a) *injecting* dict entries into the prompt of a pretrained-from-scratch BabyLM-scale
LM at SFT+inference (vs their dictionary-anchored *training* objective), (b) operating
under a fixed 100M total-token budget, and (c) gating to rare/bottom-quartile words.

## Method
"Anchored training" (AT) uses the ground-truth bilingual dictionary to establish
**anchoring points** that bridge source and target embedding spaces, learning from
large monolingual corpora with no parallel sentences. Variants include ACP and a
Bi-view AT.

## Key results
- Distant pairs (en-zh, en-ru), where unsupervised NMT collapses (zh→en **1.50** BLEU):
  AT performs **comparably to supervised SMT trained on >4M parallel sentences**.
- Table 2: Supervised SMT zh→en 31.86 / en→zh 16.55 vs **ACP+Bi-view AT zh→en 30.12 /
  en→zh 17.05** (beats SMT on en→zh and en→ru).
- Close pair (en-fr) is the easier control.

## Takeaways / how we differ
Establishes that **dictionary signal substitutes for parallel data on distant pairs** —
the core bet of C1. We test whether this survives at ~42M params with prompt-injected
(not training-anchored) dictionaries, and whether it beats a parameter-only baseline
under an equal token budget ([[2026-06-26 Path-2 budget allocation study]]).

Related: [[C1 dict_rag]] · [[Literature Synthesis & Matrix]]
