# Knowledge System Bridge — Manifest / 知識系統橋接清單

> Source session / 來源 session: `20260620_agentic-eeg-preprocessing-fooof`
> Topic: Agentic EEG preprocessing pipeline with FOOOF-based signal quality optimization
> Generated: 2026-06-21 by research-to-knowledge bridge skill
> Mode: **full** (6/9 fulltext; no 20_Knowledge tree → 0 dedup, all NEW)

## Source session summary / 來源摘要
| Field | Value |
|-------|-------|
| current_step | 9 (pipeline complete) |
| Shortlisted papers | 53 |
| Fulltext notes | 9 (6 full-text + 3 abstract-only) |
| Canvas edges (labeled) | 32 |
| SOTA themes | 5 |
| Gaps | 4 (GAP_001 locked + GAP_002 + GAP_003; GAP_004 as constraint) |
| Hypotheses | 4 (H1 primary + H2/H3/H4) |

## Folder skeleton / 資料夾結構
```
knowledge_system/
├── _MANIFEST.md
├── _DEDUP_REPORT.md
├── points/        (18 P_draft cards + _skipped.md)
├── lines/         (4 standalone + frontmatter_patches.md)
├── planes/        (5 F_draft cards)
└── body/          (1 B_draft card)
```

## Pass statistics / 各 Pass 統計

### Pass 1 — Points (18 cards)
| Card | Cluster | Source | needs_fulltext |
|------|---------|--------|:---:|
| P_draft_FOOOF_SNR | B/pillar2 | DonoghueEtAl2020b | — |
| P_draft_FOOOF | B | DonoghueEtAl2020b | ✓ |
| P_draft_IRASA | B | WenLiu2016 | ✓ |
| P_draft_Aperiodic_Signal | B/T3 | LendnerEtAl2020 | ✓ |
| P_draft_Aperiodic_Artifact_Risk | T3/risk | GersterEtAl2022 | — |
| P_draft_PREP_Pipeline | A | BigdelyShamloEtAl2015 | ✓ |
| P_draft_HAPPE | A | GabardDurnamEtAl2018 | ✓ |
| P_draft_ICLabel | A | PionTonachiniEtAl2019 | ✓ |
| P_draft_Autoreject | A | JasEtAl2017 | ✓ |
| P_draft_Automagic_QC | A | PedroniEtAl2019 | ✓ |
| P_draft_Delorme_Quality_Metric | T4 | Delorme2023 | — |
| P_draft_Preproc_Sensitivity | T4 | RobbinsEtAl2020 | ✓ |
| P_draft_AutoML_HPO | C | FeurerEtAl2015 | ✓ |
| P_draft_Personalized_Pipeline_Search | C | MartinezEtAl2023 | — |
| P_draft_BCI_Bayesian_Opt | C | BashashatiEtAl2016 | ✓ |
| P_draft_LLM_DS_Agent | D | GuoEtAl2024 | — |
| P_draft_Greedy_Oracle | pillar1 | MartinezEtAl2023 | — |
| P_draft_LLM_Preproc_Agent | pillar3 | GuoEtAl2024 | — |

All NEW (0 dedup hits). 11/18 tagged `needs_fulltext` (abstract-only sources). 8 candidates skipped — see `points/_skipped.md`.

### Pass 2 — Lines (4 standalone + 12 patches)
| Card | Type | Endpoints |
|------|------|-----------|
| D_draft_FOOOF_vs_IRASA | debate | FOOOF ↔ IRASA |
| A_draft_Quality_x_Spectral | analogy/bridge | Delorme_Quality_Metric ↔ FOOOF_SNR |
| A_draft_Search_x_EEG | analogy/bridge | Personalized_Pipeline_Search ↔ PREP_Pipeline |
| A_draft_Caveat_x_Aperiodic | analogy/bridge | Aperiodic_Artifact_Risk ↔ Aperiodic_Signal |

12 in-theme relations (evolution/dependency) → `lines/frontmatter_patches.md`. 0 unclassified.

### Pass 3 — Planes (5 cards, 5/5 Q each)
T1 Preprocessing Pipelines · T2 FOOOF Spectral · T3 Aperiodic Signal · T4 Preproc Quality · T5 Pipeline Optimization. Q5 quoted verbatim from step7 GAPs (no invention).

### Pass 4 — Body (1 card, 7/7 Q)
B_draft_agentic_eeg_preprocessing_fooof (prescriptive) — modules = 5 Planes; 3 feedback loops.

## Known limitations / 已知限制
- **3 abstract-only sources** (DonoghueEtAl2020b FOOOF paper, PedroniEtAl2019, RobbinsEtAl2020): their Method/Findings fields carry `(abstract-only, 待補)` and `needs_fulltext` tag. The FOOOF paper being paywalled is notable — its full method would enrich [[P_draft_FOOOF]].
- **No 20_Knowledge tree** → no dedup, no cross-link to existing cards. First promote seeds the library.
- Borderline folds (ASR, SOUND, SPRiNT) flagged in dedup report for promote-time decision.

## Next step / 下一步
Review the drafts — suggested order: **body/ first** (validates the spine), then **planes/F_draft_T2** and **T5** (the metric + agent cores), then Points. When ready, run the promote flow (manual or future skill) to assign P{NN}/F{NN} numbers, apply `frontmatter_patches.md`, and move into `20_Knowledge/`.

## Bridge design feedback / 橋接設計回饋
- Edge-intent classification needed an intent-over-keyword pass: many evolution labels contain "→" which a naive causal-keyword rule catches first. The skill's keyword table could note that `bridge:`-prefixed and `→`-containing labels should be tested before the causal keywords.
- This session is unusually self-referential (the project's 3 pillars ARE 3 of the gaps), so several Points are prescriptive-flavored (Greedy_Oracle, LLM_Preproc_Agent, FOOOF_SNR) rather than describing existing literature — flagged as such via their `provenance` pointing to step8 not step5.
