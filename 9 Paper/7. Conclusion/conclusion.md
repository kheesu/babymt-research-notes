# 7. Conclusion

> Draft. Headline claim `‹PENDING›` §5.

We asked a question that the prevailing "retrieval substitutes for parameters" literature has left
largely untested: when a single fixed token budget must be *shared* between a model's weights and its
retrieval datastore — and when the model is small and the language pairs are distant — where is a
token better spent? By charging pretraining, fine-tuning, *and* the retrieval store to one 100M-token
BabyLM budget, we turned "add retrieval" into a fair, equal-budget reallocation, and built a CJK +
English translation testbed with dictionary and sentence retrieval plus random and oracle controls
to attribute any effect to retrieval quality rather than prompt length.

‹PENDING: 2–3 sentences stating the headline finding — the direction and size of the
weights-vs-memory trade-off, whether it shifts with budget, and whether dictionary or sentence
retrieval drives it.›

More broadly, our setting is the regime where the substitution thesis is least examined and most
consequential: small, undertrained models under hard data budgets. ‹PENDING: one sentence on what
this implies for that thesis — does it survive downward, or does the low-resource cautionary result
of \citealp{hao2023rethinking} extend into the fixed-budget regime?›

**Future work.** Bounding pretraining variance with multiple pretrain seeds; a domain-matched
robustness subset (WikiMatrix-only) to disentangle retrieval gain from train–test domain alignment;
extending the allocation axis to finer datastore sizes; and token-level retrieval (kNN-MT-style) as
an alternative to the in-context injection used here.

---
*Sources: synthesis of §1–§6.*
