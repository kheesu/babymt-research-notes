---
tags: [meta]
status: stable
aliases: [record-changes-for-paper]
created: 2026-06-26
updated: 2026-06-26
---

# Recording changes for the paper

Working principle (mirrors the Claude `memory/` note of the same slug): when a
change is made or an empirical result is produced, capture it here so it's
retrievable when writing the paper.

**Why:** the paper is written from this project; it needs a durable trail of
decisions, results, and rationale that isn't obvious from code or git alone.

**How:** after any run, config/decoding decision, or notable result, add a
[[5 Findings|finding]] or [[6 Decisions|decision]] note with the numbers, the
setup (checkpoint / test set / n), and the conclusion. Prefer concrete metrics
over prose. Link with `[[ ]]`.
