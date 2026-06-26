---
tags: [decision]
status: draft
aliases: []
created: 2026-06-26
updated: 2026-06-26
---

# 2026-06-26 Length-ratio filter retune for Latin-CJK

**Decision:** In `filter_length_ratio`, measure each side in its own unit — **chars for CJK, words for Latin** — instead of chars-for-both when either side is CJK.

## Context
The old filter measured English in characters against dense, space-free Chinese; English is ~5 chars/word + spaces, so long en↔zh pairs blew the char-ratio/length caps. MultiUN en-zh dropped **~77%**, biasing survivors short.

## Rationale
Per-side units make caps/ratios comparable. **CJK↔CJK and Latin↔Latin are byte-identical** to before; only Latin↔CJK changes.

## Consequences
- MultiUN en-zh drop **77% → 13%**; long sentences retained. 33 preprocessing tests pass.
- Applies to all en↔CJK ingests (en-zh, en-ja, ko-en).
- Committed to code: `src/data/preprocessing.py::filter_length_ratio`.

Related: [[Models & Data]] · [[Phase 1 Data]]
