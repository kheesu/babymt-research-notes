---
tags: [literature]
status: draft
created: 2026-06-26
updated: 2026-06-26
authors: Jiaan Wang, Fandong Meng, Yingxue Zhang, Jie Zhou
year: 2025
venue: Findings of EMNLP 2025
url: https://aclanthology.org/2025.findings-emnlp.313/
arxiv: 2412.04342
---

# Retrieval-Augmented Machine Translation with Unstructured Knowledge

**TL;DR:** RAG-style MT that retrieves from **unstructured multilingual documents**
(not paired MT corpora or knowledge graphs); introduces the **RAGtrans** benchmark
(169K MT samples) and a multi-task training method to teach LLMs to use retrieved
document context. Citation now confirmed (Wang, Meng, Zhang & Zhou; arXiv 2412.04342;
Findings of EMNLP 2025).

## Relevance to BabyMT
The README's "RAG-MT" reference and a contrast point for **[[C2 sent_rag]]**: where
BabyMT's C2 retrieves *paired* parallel sentences from a TM pool (LaBSE+FAISS), this
work retrieves from *unstructured, not-fully-paired* documents — a harder retrieval
setting. BabyMT differs by BabyLM scale, distant CJK pairs, and the fixed-budget framing.

## Method
- Argues much world knowledge lives in **unstructured documents** that are not paired
  across languages, unlike standard TM/kNN-MT corpora.
- **RAGtrans** benchmark: 169K MT samples collected via GPT-4o + human translators, to
  *train and evaluate* LLMs' retrieval-augmented MT ability (the first such benchmark).
- **Multi-task training** to teach LLMs to exploit information from multilingual docs.

## Key results
Establishes the benchmark and shows the multi-task method improves LLMs' ability to use
retrieved unstructured knowledge during translation. *(specific metrics not
independently verified — fetch before quoting.)*

## Takeaways / how we differ
Pushes retrieval-MT beyond paired corpora toward open documents — orthogonal to BabyMT's
paired-TM C2, but useful framing for "what counts as a datastore". BabyMT's datastore is
*paired* and *budgeted*; this work's is *unstructured* and unbudgeted, at LLM scale.

Related: [[C2 sent_rag]] · [[Literature Synthesis & Matrix]]
