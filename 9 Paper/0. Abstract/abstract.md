# Abstract

> Draft. ~150–200 words for a short paper. Final numbers `‹PENDING›` the eval grid.

Retrieval augmentation is widely held to "substitute for parameters": a large language model
backed by a big nonparametric datastore can match a much larger parametric model. This evidence,
however, comes almost exclusively from strong, well-trained base models and from settings where the
datastore is *free* — its tokens are never counted against the model's training budget. We ask what
happens when neither assumption holds. Working at **BabyLM scale** (a ~42M-parameter decoder
pretrained from scratch) on **distant CJK + English translation pairs**, we impose a single hard
constraint: a **fixed 100M-token total budget** that every token — pretraining, fine-tuning, *and*
the retrieval datastore — must share. Within this budget we compare allocating tokens to **model
weights** (a no-retrieval baseline) against holding the same tokens in a **dictionary** or
**parallel-sentence** retrieval store, with random- and oracle-retrieval controls to attribute any
effect to retrieval *quality* rather than to extra prompt tokens. ‹PENDING: one-sentence headline
result — direction and magnitude of the weights-vs-memory trade-off, with within-pair deltas.› Our
findings speak to whether the "retrieval-replaces-parameters" story survives in the
small-model, low-resource, fixed-budget regime where it is least tested and most consequential.

---
*Sources: `1 Project/BabyMT Overview.md`, `6 Decisions/2026-06-26 Path-2 budget allocation study.md`, `7 Literature/Literature Synthesis & Matrix.md`.*
