---
tags: [literature]
status: draft
created: 2026-06-26
updated: 2026-06-26
authors: Zheng et al. (verify full author list)
year: 2024
venue: arXiv (preprint; OpenReview DVtTMQ7CJj)
url: https://arxiv.org/abs/2402.15061
arxiv: 2402.15061
---

# DragFT — Dictionary & Retrieval-Augmented Fine-Tuning for Domain-specific MT

**TL;DR:** Combines **dictionary-enhanced prompting + RAG few-shot example selection +
fine-tuning** to adapt LLMs to domain-specific MT. The README's informally-named
"DragFT" reference; canonical paper = *"Fine-tuning Large Language Models for
Domain-specific Machine Translation"* (arXiv 2402.15061, 2024).

> [!note] Citation resolved
> "DragFT" is the **framework name**; the arXiv title is *Fine-tuning Large Language
> Models for Domain-specific Machine Translation*. Author list and exact model scale
> (README said "~13B") are **not yet verified** — confirm before citing in the paper.

## Relevance to BabyMT
The one prior work that bundles **both** of BabyMT's retrieval arms — dictionary
injection ([[C1 dict_rag]]) and retrieval of parallel examples ([[C2 sent_rag]]) — plus
fine-tuning, the same SFT+inference locus BabyMT uses. Key contrast: DragFT runs at
**LLM scale on a strong base** with no token budget; BabyMT runs at **BabyLM scale from
scratch under a fixed 100M total budget**.

## Method
- **Dict-enhanced prompting / Dict-rephrasing:** rephrases the source by replacing
  terminology with target-language domain terms; integrates dict info into the prompt.
- **RAG-based few-shot selection:** builds vector DBs over self-constructed
  domain corpora and retrieves relevant translation pairs as few-shot examples.
- **Fine-tuning** with the few-shot in-domain examples.

## Key results
Reports that the dictionary + RAG + fine-tuning combination improves domain-specific
terminology translation over plain LLM baselines. *(specific BLEU/COMET numbers not
verified — fetch the paper before quoting.)*

## Takeaways / how we differ
DragFT shows dict+RAG+FT compose at LLM scale; BabyMT asks the **scale-down** question
and adds the **fixed-budget allocation** framing (weights vs datastore) that DragFT does
not study. DragFT mixes both retrieval modes — BabyMT deliberately isolates C1 vs C2 as
separate trained conditions plus random/oracle controls.

Related: [[C1 dict_rag]] · [[C2 sent_rag]] · [[Literature Synthesis & Matrix]]
