# 9. References

> Draft bibliography. Citation keys match the `\citep{...}` keys used in the section drafts.
> **Verification tags** carried from the literature notes:
> ✅ = claim/citation survived adversarial verification · 📖 = bibliography confirmed, numbers not
> independently checked · ⚠ = citation/result still needs checking — **pin before submission.**
> A `.bib` file should be generated from this list once entries are pinned.

## BabyLM / budget framework
- ✅ **`warstadt2023findings`** — Warstadt, Mueller, Choshen, Wilcox, et al. *Findings of the BabyLM Challenge.* CoNLL/BabyLM 2023 (`2023.conll-babylm.1`). *(Canonical cite for the additive, whole-pipeline budget rule.)*
- ✅ **`choshen2026babylm`** — Choshen et al. *BabyLM Turns 4 & Goes Multilingual* (Call for Papers). arXiv:2602.20092, 2026. *(Venue, deadline, byte-premium budget; En/Nl/Zh multilingual track.)*
- ✅ **`warstadt2024second`** — *Findings of the Second BabyLM Challenge.* arXiv:2412.05149, 2024.
- 📖 **`babybabellm2025`** — *BabyBabelLM.* arXiv:2510.10159, 2025. *(45-language dataset; ko/ja present but outside the competition track.)*

## Parametric vs. nonparametric memory
- 📖 **`khandelwal2020knnlm`** — Khandelwal, Levy, Jurafsky, Zettlemoyer, Lewis. *Generalization through Memorization: Nearest Neighbor Language Models (kNN-LM).* ICLR 2020. arXiv:1911.00172.
- ✅ **`borgeaud2022retro`** — Borgeaud et al. *Improving Language Models by Retrieving from Trillions of Tokens (RETRO).* ICML 2022. arXiv:2112.04426. *(7.5B + 2T-token DB ≈ GPT-3-class, ~25× fewer params.)*
- 📖 **`shao2024massiveds`** — Shao, He, Asai, Shi, Dettmers, Min, Zettlemoyer, Koh. *Scaling Retrieval-Based Language Models with a Trillion-Token Datastore (MassiveDS).* arXiv:2407.12854, 2024 (NeurIPS 2024 — ⚠ verify venue).
- ⚠ **`singh2026memorize`** — Singh, Yu, Gangal, Tao, Kumar, Liu, Feng. *To Memorize or to Retrieve: Scaling Laws for RAG-Considerate Pretraining.* arXiv:2604.00715, 2026. **(Closest prior work / partial-scoop risk — verify author list and claims against the PDF.)**

## Retrieval-augmented / translation-memory MT
- ✅ **`khandelwal2021knnmt`** — Khandelwal, Fan, Jurafsky, Zettlemoyer, Lewis. *Nearest Neighbor Machine Translation (kNN-MT).* ICLR 2021. arXiv:2010.00710.
- ✅ **`zheng2021adaptive`** — Zheng et al. *Adaptive Nearest Neighbor Machine Translation.* ACL 2021 (short). arXiv:2105.13022.
- ✅ **`stap2023multilingual`** — Stap & Monz. *Multilingual kNN-MT.* EMNLP 2023. arXiv:2310.14644.
- ✅ **`hao2023rethinking`** — Hao, Huang, Liu, Zhang, Shi, Wang (⚠ verify full author list). *Rethinking Translation Memory Augmented Neural Machine Translation.* Findings of ACL 2023. arXiv:2306.06948. *(The low-resource TM-failure result.)*
- 📖 **`cai2021neural`** — Cai et al. *Neural Machine Translation with Monolingual Translation Memory.* ACL 2021. *(Original low-resource TM observation, via Hao et al. — ⚠ pin exact cite.)*
- 📖 **`zhang2024dragft`** — Zhang et al. (⚠ author list listed as "Zheng et al.?") *Fine-tuning LLMs for Domain-specific MT (DragFT).* arXiv:2402.15061, 2024. *(⚠ verify authors and scale.)*
- 📖 **`wang2025ragtrans`** — Wang, Meng, Zhang, Zhou. *Retrieval-Augmented MT with Unstructured Knowledge (RAGtrans).* Findings of EMNLP 2025. arXiv:2412.04342.

## Dictionary- / lexicon-augmented MT
- ✅ **`duan2020bilingual`** — Duan, Ji, Jia, Tan, Zhang, Chen, Luo, Zhang. *Bilingual Dictionary Based NMT without Using Parallel Sentences.* ACL 2020. arXiv:2007.02671. *(Strongest C1 precedent.)*
- 📖 **`ghazvininejad2023dictionary`** — Ghazvininejad et al. *Dictionary-based Phrase-level Prompting of LLMs for MT (DiPMT).* arXiv:2302.07856, 2023.
- 📖 **`lu2023chain`** — Lu et al. *Chain-of-Dictionary Prompting (CoD).* arXiv:2305.06575, 2023.
- 📖 **`hokamp2017lexically`** — Hokamp & Liu. *Lexically Constrained Decoding ... (Grid Beam Search).* ACL 2017. arXiv:1704.07138.
- 📖 **`post2018fast`** — Post & Vilar. *Fast Lexically Constrained Decoding (Dynamic Beam Allocation).* NAACL 2018. arXiv:1804.06609.
- 📖 **`niehues2021continuous`** — Niehues. *Continuous Learning in NMT using Bilingual Dictionaries.* EACL 2021. arXiv:2102.06558.

## In-context / low-resource / distant-pair MT
- 📖 **`chitale2024empirical`** — Chitale, Gala, Dabre. *An Empirical Study of In-context Learning in LLMs for MT.* Findings of ACL 2024 (`2024.findings-acl.440`).
- 📖 **`merx2024low`** — Merx et al. *Low-Resource MT through Retrieval-Augmented LLM Prompting: A Study on Mambai.* arXiv:2404.04809, 2024.
- 📖 **`pei2025understanding`** — Pei, Liu, Lin, Yvon, Schütze. *Understanding In-Context MT for Low-Resource Languages: A Case Study on Manchu.* ACL 2025. arXiv:2502.11862.
- ⚠ **`factors2021korean`** — *Factors Behind the Effectiveness of an Unsupervised NMT System between Korean and Japanese.* Applied Sciences 2021, 11(16):7662. *(⚠ verify authors.)*
- 📖 **`zhang2024lowresource`** — Zhang et al. *Low-Resource NMT survey (Chinese-centric).* ACM TALLIP 2024. DOI:10.1145/3665244.

## Data / evaluation resources (cited in §4 / appendix)
- ⚠ **`nllb2022`** — NLLB Team. *No Language Left Behind / FLORES-200.* 2022. *(Test set; pin exact cite.)*
- 📖 **`schwenk2021wikimatrix`** — Schwenk et al. *WikiMatrix.* EACL 2021. *(LASER-mined parallel data.)*
- 📖 **`eisele2010multiun`** — Eisele & Chen. *MultiUN.* LREC 2010. *(⚠ verify.)*
- ⚠ **`feng2022labse`** — Feng et al. *Language-agnostic BERT Sentence Embedding (LaBSE).* ACL 2022. *(Sentence retriever / leakage audit; pin cite.)*
- ⚠ Tooling to cite where used: **sacreBLEU** (Post 2018), **chrF/chrF++** (Popović 2015/2017), **COMET** (Rei et al. 2020/2022, `Unbabel/wmt22-comet-da`), **JMdict/EDICT** (Breen), **CC-CEDICT**, **krdict** (NIKL), **OpenCC**. Add formal entries before submission.

---
*Sources: `7 Literature/Literature Synthesis & Matrix.md` (24-row matrix with verification tags), per-paper notes in `7 Literature/`, `memory/literature-deep-research-2026-06.md`.*
