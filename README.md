# BabyMT — Research Notes

An [Obsidian](https://obsidian.md)-compatible vault for the **BabyMT** project
(retrieval-augmented MT at BabyLM scale). It is the durable, paper-facing
companion to the code repo — decisions, findings, run logs, and literature that
won't be obvious from code or git history alone.

> **Status: scaffold.** The pipeline is mid-overhaul; most notes are intentionally
> empty stubs. Fill them in as the new pipeline settles. Do not treat any
> experiment detail here as authoritative until its note loses `status: stub`.

## How to use

- Open the folder as an Obsidian vault (`Open folder as vault`).
- Start at [[Home]] — the map of content.
- Notes are atomic and link-heavy. Prefer `[[wikilinks]]` over folders for
  relationships.

## Layout

| Folder | What lives here |
|---|---|
| `1 Project/` | Overview, the live status snapshot, timeline, open questions |
| `2 Design/` | Experimental design, the grid, frozen decisions, conditions C0–C4 |
| `3 Phases/` | One note per pipeline phase (data → analysis) |
| `4 Experiments/` | The as-built runs: pretrain bases, SFT grid, HP tuning, eval |
| `5 Findings/` | Empirical results — one claim per note (paper material) |
| `6 Decisions/` | Dated, ADR-style design decisions |
| `7 Literature/` | Papers and prior work |
| `8 Journal/` | Dated session logs (append-only) |
| `_meta/` | Templates and the tag taxonomy |
| `_attachments/` | Figures, slides, screenshots |

## Conventions

- **Frontmatter** on every note: `tags`, `status`, `created`, `updated`, plus
  type-specific keys. Dataview-friendly (and harmless without Dataview).
- **`status`** values: `stub` → `draft` → `stable` (or `superseded`).
- **Tags**: see [[Tags]] for the taxonomy.
- **Templates**: see `_meta/Templates/`.
- This vault mirrors the Claude `memory/` link graph; findings/decisions carry
  the memory slug as a frontmatter `alias` so links resolve both ways.
