# Pass 2 — Frontmatter Patches / 前繫資料補丁

These bilateral relations are applied to the matched Point cards at promote time. Endpoints use `P_draft_*` slugs.

- `P_draft_Aperiodic_Signal` ← `depends_on`: `[P_draft_FOOOF]`  (canvas edge "aperiodic = real signal")
- `P_draft_Aperiodic_Signal` ← `depends_on`: `[P_draft_FOOOF]`  (canvas edge "aperiodic validated in aging")
- `P_draft_Delorme_Quality_Metric` ← `depends_on`: `[P_draft_Automagic_QC]`  (canvas edge "shared: need for objective quality")
- `P_draft_Delorme_Quality_Metric` ← `parent`: `P_draft_Preproc_Sensitivity`  (evolution; canvas edge "preprocessing sensitivity → quality metric")
- `P_draft_FOOOF` ← `related_lines`: `[D_draft_FOOOF_vs_IRASA]`  (canvas edges "IRASA compared vs FOOOF", "method scrutinized")
- `P_draft_IRASA` ← `related_lines`: `[D_draft_FOOOF_vs_IRASA]`  (canvas edge "IRASA compared vs FOOOF")
- `P_draft_HAPPE` ← `depends_on`: `[P_draft_Autoreject]`  (canvas edge "autoreject in HAPPE-class pipelines")
- `P_draft_HAPPE` ← `depends_on`: `[P_draft_PREP_Pipeline]`  (canvas edge "robust referencing reused")
- `P_draft_Personalized_Pipeline_Search` ← `parent`: `P_draft_AutoML_HPO`  (evolution; canvas edge "AutoML → preprocessing search")
- `P_draft_Personalized_Pipeline_Search` ← `parent`: `P_draft_BCI_Bayesian_Opt`  (evolution; canvas edge "BO for BCI → pipeline search")
- `P_draft_Greedy_Oracle` ← `depends_on`: `[P_draft_Personalized_Pipeline_Search]`  (canvas edge "manual per-step compare → automated search"; Coelli manual compare → automated oracle)
- `P_draft_Preproc_Sensitivity` ← `depends_on`: `[P_draft_PREP_Pipeline]`  (canvas edge "same group: pipeline sensitivity")
