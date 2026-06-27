# Figures

Script-generated from `results/master.csv` (Phase 5). None exist yet (eval grid pending).

Planned:
- `learning_curves.*` — chrF/COMET vs. SFT budget (1k/10k/100k), one line per condition, min–max
  band across 3 seeds, log-x. (§5.2)
- `headline_deltas.*` — within-pair Δ(condition − C0) with 95% CIs, faceted by pair. (§5.1)
- `controls.*` — C0 / C3 random / C1 dict / C4 oracle, showing random≈baseline and the oracle
  ceiling. (§5.4)
- `budget_accounting.*` — dual budget bars (weights vs. weights+memory) per condition. (§5 / Phase 5)
