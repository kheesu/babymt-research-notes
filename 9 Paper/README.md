# BabyMT — Paper Drafts

Working title (provisional):
**Weights or Memory? Allocating a Fixed Token Budget Between Parametric and Retrieval-Based Translation at BabyLM Scale**

Target venue: **BabyLM Challenge 2026** (4th iteration, co-located with EMNLP 2026, Budapest).
Direct submission deadline: **2026-07-15** · ARR commitment: 2026-05-25.

---

## How this directory is organized

One subdirectory per paper section, in reading order. Each holds a single draft markdown
file. `_figures/` holds figure stubs / generated plots.

| Dir | File | Status |
|---|---|---|
| `0. Abstract`        | `abstract.md`           | draft |
| `1. Introduction`    | `introduction.md`       | draft |
| `2. Related Works`   | `related_works.md`      | draft |
| `3. Method`          | `method.md`             | draft |
| `4. Experimental Setup` | `experimental_setup.md` | draft |
| `5. Results`         | `results.md`            | **skeleton — final numbers pending eval grid** |
| `6. Analysis`        | `analysis.md`           | **skeleton — depends on Results** |
| `7. Conclusion`      | `conclusion.md`         | draft |
| `8. Limitations`     | `limitations.md`        | draft (ACL-mandatory section) |
| `9. References`      | `references.md`         | draft bibliography (verify flagged entries) |
| `10. Appendix`       | `appendix.md`           | draft (data card, hyperparams, ethics) |

---

## ⚠ Read before editing — project status

This paper is being drafted while the experiments are **mid-pivot**. Two facts shape every draft:

1. **The framing pivoted to "path-2" on 2026-06-26** (`babymt-research-notes/6 Decisions/2026-06-26 Path-2 budget allocation study.md`).
   The drafts here adopt **path-2** as the spine: *under a fixed 100M-token total budget, is a token
   better spent on model weights (pretraining) or on a retrieval datastore?* The earlier "path-1"
   framing (*does retrieval help a BabyLM-scale MT model?*) is the fallback. **This framing choice is
   flagged in the notes as the user's final call** — if you prefer path-1, the Introduction and
   Method need re-pointing (the rest largely survives).

2. **Most quantitative results are provisional.** Adopting path-2 retired the 11 fully-pretrained
   bases (each used the full 100M budget) and paused the SFT grid at 80/108 cells; bases will be
   re-pretrained at a reduced budget and the eval grid has not yet run. Throughout the drafts:
   - `‹PENDING›` marks a number/table that must come from the final eval grid.
   - The single real end-to-end MT number that exists today (ko→zh, C0, 100k) is used **only** as a
     pilot/sanity result and is labelled as such.
   - **Do not present any provisional number as final.**

## Open decisions to resolve before submission
- [ ] **Final research-question framing** (path-2 allocation study vs. path-1 retrieval-helps). *(user's call)*
- [ ] **Track / compliance**: the official 2026 Multilingual track is En/Nl/Zh *pretraining* (BLiMP-style), not CJK MT, and the official rule says "100M words/tokens", never "unique tokens". We adapt the budget rule *outside* that track — state this as our operationalization.
- [ ] **Re-pretrain budget** and final datastore size `D` (the allocation axis).
- [ ] **Final pair set** (12 directed CJK+En pairs vs. a reported subset).
- [ ] Pin all `(verify)` citations in `9. References`.
- [ ] Resolve stale repo docs (`README.md`, `PHASE3–5.md` describe the older en-zh/en-ko design).

## Provenance
Drafts synthesized from `babymt-research-notes/` (the Obsidian vault), `DATA_SOURCES.md`,
`PHASE3–5.md`, and the Claude `memory/` notes (which hold the only recorded A/B numbers).
See each section file's footer for its specific sources.
