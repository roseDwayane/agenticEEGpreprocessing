---
session_id: "20260620"
topic: "Agentic EEG preprocessing pipeline with FOOOF-based signal quality optimization"
date: "2026-06-20"
step: 2
---

# Search Summary / 搜尋總結

> Topic / 研究主題: Agentic EEG preprocessing pipeline with FOOOF-based signal quality optimization
> Search date / 搜尋日期: 2026-06-20

## Yield / 收穫總覽

| Metric | Value |
|--------|-------|
| Total records retrieved (before dedup) | 70 |
| **Unique papers (after dedup)** | **69** |
| DOI coverage | 98.6% |
| Sources used | OpenAlex, arXiv, WebSearch |
| Year range | 2012–2025 |
| Seed papers (snowball anchors) | 5 (top-cited) |

**Note on sources / 來源備註:** The Semantic Scholar API was rate-limited (HTTP 429) throughout this session, so the search relied on **OpenAlex** (primary, reliable), **arXiv** (for AutoML/LLM-agent preprints), and **targeted WebSearch** for DOI confirmation. Every record was verified against an actual API response or WebSearch hit — no fabricated papers. Citation counts are point-in-time snapshots.

## Thematic Clusters / 主題群集

The 69 papers fall into 4 citation clusters, which map directly onto the 3 pillars of the project plus its methodological backbone:

| Cluster | Theme | n | Maps to project pillar |
|---------|-------|---|------------------------|
| **A** | Standard & automated EEG preprocessing pipelines / artifact removal | 20 | **Pillar 1** — the "preprocessing actions" the greedy search & agent operate over (PREP, HAPPE, Autoreject, ICLabel, Automagic, ASR) + benchmarking of preprocessing sensitivity |
| **B** | FOOOF / specparam aperiodic–periodic spectral parameterization | 18 | **Pillar 2** — the FOOOF-based signal/noise decomposition for the SNR quality metric (Donoghue 2020, IRASA, FOOOF-vs-IRASA, aperiodic-as-biomarker apps) |
| **C** | Classical AutoML / HPO / Bayesian-opt / pipeline search | 16 | **Pillar 3 (baseline)** — framing preprocessing as a searchable problem; the "greedy/exhaustive search" ground-truth idea (Auto-sklearn, Auto-WEKA, personalized preprocessing pipeline search, data-cleaning + AutoML) |
| **D** | LLM agents for data science / ML engineering / scientific discovery | 15 | **Pillar 3 (target)** — the LLM-agent-with-tools controller (DS-Agent, AIDE, AutoML-Agent, SELA, Data Interpreter, AI Scientist, ChemCrow/Coscientist) |

## Hub Papers / 樞紐論文 (in-degree ≥ 3 within the collection)

These are the most-cited-within-collection works — the conceptual anchors for the SOTA review:

| In-deg | Paper | Cluster |
|-------:|-------|---------|
| 14 | Donoghue et al. 2020 — Parameterizing neural power spectra (FOOOF) | B |
| 8 | Wang et al. 2023 — A Survey on LLM-based Autonomous Agents | D |
| 7 | Bigdely-Shamlo et al. 2015 — The PREP pipeline | A |
| 6 | Pion-Tonachini et al. 2019 — ICLabel | A |
| 6 | Feurer et al. 2015 — Efficient and Robust Automated ML (Auto-sklearn) | C |
| 6 | Jas et al. 2017 — Autoreject | A |
| 4 | Gabard-Durnam et al. 2018 — HAPPE | A |
| 4 | Wu et al. 2023 — AutoGen | D |
| 4 | Thornton et al. 2013 — Auto-WEKA | C |
| 3 | Wen & Liu 2016 — IRASA | B |

## Seed Papers / 種子論文 (top-5 by citation — snowball anchors)

1. Donoghue et al. 2020 — Parameterizing neural power spectra (FOOOF) — Nature Neuroscience [~2350]
2. Pion-Tonachini et al. 2019 — ICLabel — NeuroImage [~2298]
3. Bigdely-Shamlo et al. 2015 — The PREP pipeline — Front. Neuroinform. [~1408]
4. Feurer et al. 2015 — Auto-sklearn — NeurIPS [~1259]
5. Wang et al. 2023 — Survey on LLM-based Autonomous Agents [~1151]

## Most-On-Target Papers for the Core Hypothesis / 最切題論文

These directly touch the project's central claims and will be heavily weighted in screening:

- **Delorme 2023, "EEG is better left alone"** (Sci. Rep.) — builds an explicit *data-quality metric* to compare preprocessing pipelines and finds many automated steps *hurt* — directly motivates the "quality metric instead of accuracy" framing AND is a cautionary baseline.
- **"Towards Personalized Preprocessing Pipeline Search" (2023)** — frames preprocessing-pipeline selection as a per-instance search problem — the closest prior art to the "agent picks parameters per recording" idea.
- **Robbins et al. 2020, "How Sensitive Are EEG Results to Preprocessing"** (IEEE TNSRE) — quantifies preprocessing-choice sensitivity — the justification for why parameter selection matters at all.
- **Gerster et al. 2022, FOOOF vs IRASA** — the methods caveats for using FOOOF as a quality metric (what can go wrong in the aperiodic/periodic split).
- **DS-Agent / AIDE / AutoML-Agent / SELA (2024)** — the LLM-agent-for-ML-engineering archetypes the Step-3 controller would adapt.

## Coverage Gaps Noted at Search Time / 搜尋階段已知缺口

- Very little work *directly combines* (FOOOF-SNR as reward) × (agent selecting EEG preprocessing) — this is the apparent novelty seam (to be confirmed in Step 7 gap analysis).
- arXiv was intermittently unavailable, so some very recent (2025) LLM-agent preprints may be under-sampled; snowball + Step 5 full-text can backfill.

## Next Step / 下一步

**Step 3 — research-screen**: score all 69 papers on relevance + methodological rigor against the PICO, produce a quality-filtered shortlist, and surface borderline papers for Checkpoint 2.
