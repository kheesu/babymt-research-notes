---
tags: [project]
status: draft
created: 2026-06-26
updated: 2026-06-26
---

# Open Questions

*Unresolved; move resolved ones to [[6 Decisions|Decisions]] or a [[5 Findings|Finding]].*

- [ ] **Path-2 allocation** (the main gate): datastore size `D`, SFT budget set (incl. 500k?), fixed split vs an allocation **axis**, and which bases to **re-pretrain** at the reduced budget. See [[2026-06-26 Path-2 budget allocation study]].
- [ ] **Final research-question framing** for the paper (retrieval benefit at scale vs the weights-vs-datastore allocation framing) — user's call.
- [ ] **en-zh source mix** + final size (Tatoeba 74.6k now; augment with WikiMatrix / MultiUN?).
- [ ] **zh-ja**: WikiMatrix (256k) only, or `--append` Tatoeba (14k)? No bilingual dict exists → **C1 (dict_rag) not possible for zh-ja** (and en-ja/zh-ja generally). Run those pairs C0+C2 only?
- [ ] **Pairs in scope**: confirm the 12 directed pairs, or a subset. Build en-ja / ko-en pools?
- [ ] **Base-coverage ablation** (bi / tri / quad pretraining) — keep as an axis under path-2, given re-pretraining cost?

Related: [[Current State]]
