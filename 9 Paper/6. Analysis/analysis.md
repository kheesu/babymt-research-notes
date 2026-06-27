# 6. Analysis / Discussion

> Skeleton. Depends on §5. Below are the interpretive threads to develop once numbers land, framed
> so that each is falsifiable by the results we will have.

## 6.1 Does retrieval substitute for parameters at this scale?

The headline reading. Place our result on the spectrum between the large-scale "retrieval replaces
parameters" line (\citealp{borgeaud2022retro}; \citealp{shao2024massiveds}; \citealp{singh2026memorize})
and the low-resource cautionary result (\citealp{hao2023rethinking}). Three outcomes to be ready to
argue:
- **Memory wins** — the substitution survives even for a tiny base; the datastore is a better use of
  scarce budget than weights. (Would extend the large-scale story *downward* and against the
  low-resource caution.)
- **Weights win** — at ~42M params the model is too weak to *use* retrieved context well, so the
  budget is better baked in. (Would extend \citealp{hao2023rethinking} into the fixed-budget regime.)
- **It depends on budget** — a crossover along the 1k/10k/100k axis; name where it sits and why.

## 6.2 Why (mechanism)

Tie the direction back to the **bias–variance** framing of \citet{hao2023rethinking}: did the tiny
base underfit enough that retrieval's bias-reduction dominated the variance penalty? Use the
**targeted metrics** (§5.5) and the **retrieval-use breakdown** (§5.6) as the mechanism evidence —
e.g. if C1 helps rare-word accuracy but not corpus chrF, the model is *copying* correct entries
locally without integrating them globally (`copied_correct` high, overall flat).

## 6.3 What the controls tell us

Interpret C3/C4 explicitly: the **C4 oracle gap** is the ceiling on how much *better retrieval* could
help (is the bottleneck retrieval quality or the model's ability to exploit it?); the **C1−C3 gap**
is the part of any C1 effect attributable to retrieval *content* rather than prompt length. If
C4 ≈ C1, better retrieval would not help and the model is the bottleneck; if C4 ≫ C1, retrieval
quality is the lever.

## 6.4 Dictionary vs. sentence memory

Where each form of memory pays (lexical, long-tail vocabulary vs. phrasal/structural), and the
asymmetry that **zh↔ja has no dictionary** so leans entirely on C2. Connect to \citet{pei2025understanding}
(dictionaries + examples help, grammar books don't) and \citet{duan2020bilingual} (dictionaries alone
go a long way on distant pairs).

## 6.5 Distant-pair effects

Whether CJK-internal pairs (ko↔zh, ko↔ja — typologically closer in some respects) behave differently
from en↔CJK pairs, read **descriptively** only (cross-pair confounds; §8). Connect to
\citet{factors2021korean}.

## 6.6 Threats to validity (forward pointer)

Single pretraining seed, heterogeneous per-pair data sources, uneven domain match to FLORES, and the
unique-token operationalization — each is developed in `8. Limitations`; here, note which §5 result
each one could plausibly flip.

---
*Sources: §5 results (pending), `7 Literature/*`, `limitations_data.md`.*
