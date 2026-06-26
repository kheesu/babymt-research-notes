---
tags: [moc]
status: stub
created: 2026-06-26
updated: 2026-06-26
---

# 🏠 Home — BabyMT Research Notes

Map of content for the vault. See [[README]] for conventions.

> [!warning] Scaffold
> Pipeline mid-overhaul. Notes are stubs — fill in as the new pipeline settles.

## Start here
- [[BabyMT Overview]] — what the project is and the research question
- [[Current State]] — the live snapshot of what's done / running / planned
- [[Open Questions]]

## Design
- [[Experimental Design]] · [[The Grid]] · [[Frozen Decisions]] · [[Models & Data]]
- Conditions: [[C0 baseline]] · [[C1 dict_rag]] · [[C2 sent_rag]] · [[C3 random_rag]] · [[C4 oracle_rag]]

## Phases
- [[Phase 1 Data]] · [[Phase 2 Retrieval]] · [[Phase 3 Training]] · [[Phase 4 Evaluation]] · [[Phase 5 Analysis]]

## Experiments (as-built)
- [[Pretrain Bases]] · [[SFT Grid]] · [[HP Tuning]] · [[Eval Grid]]

## Findings (paper material)
- [[SFT produces real translations]]
- [[Degenerate loops & no_repeat_ngram]]

<!-- Dataview (if installed): uncomment to auto-list findings
```dataview
TABLE status, updated FROM "5 Findings" WHERE file.name != "Home" SORT updated DESC
```
-->

## Decisions
- [[2026-06-26 Freeze no_repeat_ngram_size]]

## Literature
- [[Bilingual Dictionary NMT]] · [[RAG-MT with Unstructured Knowledge]] · [[DragFT]]

## Journal
- [[2026-06-26]]

## Meta
- [[Tags]] · [[Recording changes for the paper]] · templates in `_meta/Templates/`
