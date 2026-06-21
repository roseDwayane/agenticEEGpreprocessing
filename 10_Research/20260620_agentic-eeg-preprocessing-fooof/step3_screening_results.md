---
session_id: "20260620"
topic: "Agentic EEG preprocessing pipeline with FOOOF-based signal quality optimization"
date: "2026-06-20"
step: 3
threshold: 3.5
weights: "relevance=0.50, quality=0.30, recency_impact=0.20"
---

# Screening Results / 篩選結果

> Topic / 研究主題: Agentic EEG preprocessing pipeline with FOOOF-based signal quality optimization
> Papers screened / 篩選論文數: 69
> Date / 篩選日期: 2026-06-20
> Threshold / 門檻: composite >= 3.5

## Screening Criteria / 篩選標準

### Inclusion / 納入條件
- **Preprocessing focus** — paper concerns EEG preprocessing methods, pipelines, or artifact handling (the agent's action space). / 論文涉及 EEG 預處理方法、管線或 artifact 處理（即 agent 的動作空間）。
- **Quality-metric focus** — paper concerns measuring signal quality / SNR / spectral decomposition (FOOOF, IRASA, aperiodic-periodic). / 論文涉及訊號品質、SNR 或頻譜分解的衡量（FOOOF、IRASA、週期/非週期）。
- **Search/learning focus** — paper concerns AutoML, HPO, RL, Bayesian optimization, or LLM agents for selecting/configuring data pipelines. / 論文涉及 AutoML、HPO、RL、貝氏最佳化或 LLM agent 來挑選/設定資料管線。
- Empirical study, methods paper, benchmark, or systematic review. / 實證研究、方法論文、基準測試或系統性回顧。

### Exclusion / 排除條件
- **Downstream-task-only** — paper is about an EEG classification/application task where preprocessing is incidental. / 僅關於下游分類/應用任務，預處理只是附帶步驟。
- **Keyword-only overlap** — shares search/HPO keywords but on an unrelated problem. / 僅關鍵字重疊但問題不相關。
- No original data or methodological contribution. / 無原始資料或方法貢獻。

## Summary / 摘要

| Category / 分類 | Count / 數量 | Percentage / 百分比 |
|-----------------|-------------|-------------------|
| **Included / 納入** | **53** | **77%** |
| Borderline / 邊緣 | 13 | 19% |
| Excluded / 排除 | 3 | 4% |

The collection is strong and on-target — most papers were deliberately gathered to cover the three project pillars, so a high inclusion rate (~77%) is expected. The borderline band is dominated by **aperiodic-as-clinical-biomarker** application papers (relevant evidence that the FOOOF decomposition is meaningful, but not about preprocessing or the metric itself) — exactly the kind of papers to weigh by hand at Checkpoint 2.

## Tier 1: Core Method & Quality-Metric Papers / 核心方法與品質指標論文 (Score >= 4.5)

| ID | Title | Authors | Year | Rel | Qual | Rec | **Composite** | Rationale |
|----|-------|---------|------|-----|------|-----|---------------|-----------|
| paper_004 | ICLabel: An automated electroencephalographic independent comp | Pion-Tonachini et al. | 2019 | 5 | 5 | 5 | **5.00** | Automated IC classifier — central to ICA-based cleaning the agent would invoke; very high impact |
| paper_008 | EEG is better left alone | Delorme | 2023 | 5 | 5 | 5 | **5.00** | Builds an explicit quality metric to compare pipelines & shows automation can hurt — directly motivates quality-over-accuracy framing |
| paper_021 | Parameterizing neural power spectra into periodic and aperiodi | Donoghue et al. | 2020 | 5 | 5 | 5 | **5.00** | THE FOOOF paper — defines aperiodic/periodic split underpinning the SNR metric; field-defining impact |
| paper_024 | Separating Neural Oscillations from Aperiodic 1/f Activity: Ch | Gerster et al. | 2022 | 5 | 5 | 5 | **5.00** | FOOOF-vs-IRASA challenges & recommendations — the methods caveats for using FOOOF as a quality metric |
| paper_001 | The PREP pipeline: standardized preprocessing for large-scale  | Bigdely-Shamlo et al. | 2015 | 5 | 5 | 4 | **4.80** | Canonical standardized preprocessing pipeline; defines the action space the agent/greedy-search operates over; foundational |
| paper_002 | The Harvard Automated Processing Pipeline for Electroencephalo | Gabard-Durnam et al. | 2018 | 5 | 5 | 4 | **4.80** | HAPPE — core automated pipeline & a key baseline; high impact |
| paper_003 | Autoreject: Automated artifact rejection for MEG and EEG data | Jas et al. | 2017 | 5 | 5 | 4 | **4.80** | Automated, cross-validated artifact rejection — directly a candidate preprocessing action & strong baseline |
| paper_006 | Automagic: Standardized preprocessing of big EEG data | Pedroni et al. | 2019 | 5 | 5 | 4 | **4.80** | Explicitly offers OBJECTIVE QUALITY ASSESSMENT of preprocessed EEG — directly parallels the quality-metric goal |
| paper_007 | How Sensitive Are EEG Results to Preprocessing Methods: A Benc | Robbins et al. | 2020 | 5 | 5 | 4 | **4.80** | Quantifies preprocessing-choice sensitivity — the core justification that parameter selection matters; IEEE TNSRE |
| paper_023 | Separating Fractal and Oscillatory Components in the Power Spe | Wen et al. | 2016 | 5 | 5 | 4 | **4.80** | IRASA — the principal alternative decomposition for the signal/noise SNR metric; high impact |
| paper_013 | Selecting methods for a modular EEG pre-processing pipeline: A | Coelli et al. | 2023 | 5 | 4 | 5 | **4.70** | Objectively compares per-step preprocessing choices with quantitative indices — nearly the greedy-search idea, manually done |
| paper_044 | Towards Personalized Preprocessing Pipeline Search | Martinez et al. | 2023 | 5 | 4 | 5 | **4.70** | Per-instance preprocessing-pipeline search — the closest prior art to 'agent picks params per recording' |
| paper_056 | DS-Agent: Automated Data Science by Empowering Large Language  | Guo et al. | 2024 | 5 | 4 | 5 | **4.70** | LLM data-science agent with case-based reasoning — direct template for the Step-3 controller |
| paper_059 | AutoML-Agent: A Multi-Agent LLM Framework for Full-Pipeline Au | Trirat et al. | 2024 | 5 | 4 | 5 | **4.70** | Multi-agent LLM full-pipeline AutoML — closest LLM-agent analog to the proposed system |
| paper_060 | SELA: Tree-Search Enhanced LLM Agents for Automated Machine Le | Chi et al. | 2024 | 5 | 4 | 5 | **4.70** | Tree-search LLM agent for AutoML — directly relevant search+LLM hybrid |
| paper_022 | Parameterizing neural power spectra | Haller et al. | 2018 | 5 | 4 | 4 | **4.50** | FOOOF bioRxiv precursor — method origin |

## Tier 2: Strong Supporting — Pipelines, Search & Agents / 管線、搜尋與智能體 (Score 4.0–4.4)

| ID | Title | Authors | Year | Rel | Qual | Rec | **Composite** | Rationale |
|----|-------|---------|------|-----|------|-----|---------------|-----------|
| paper_027 | Electrophysiological Frequency Band Ratio Measures Conflate Pe | Donoghue et al. | 2020 | 4 | 5 | 4 | **4.30** | Shows band-ratios conflate aperiodic+periodic — argues FOR explicit parameterization the metric relies on |
| paper_039 | Auto-WEKA: Combined Selection and Hyperparameter Optimization  | Thornton et al. | 2012 | 4 | 5 | 4 | **4.30** | Foundational combined algorithm-selection + HPO via Bayesian opt — the search-baseline template for ground-truth construction |
| paper_040 | Efficient and Robust Automated Machine Learning | Feurer et al. | 2015 | 4 | 5 | 4 | **4.30** | Auto-sklearn — canonical AutoML; the meta-learning + search baseline for parameter selection |
| paper_005 | MNE-ICALabel: Automatically annotating ICA components with ICL | Li et al. | 2022 | 4 | 4 | 5 | **4.20** | Python ICLabel port — practical tool the pipeline would call; recent |
| paper_009 | DISCOVER-EEG: an open, fully automated EEG pipeline for biomar | Ávila et al. | 2023 | 4 | 4 | 5 | **4.20** | Fully automated pipeline extracting spectral/connectivity features — close to the agent's target output; recent |
| paper_010 | HAPPILEE: HAPPE In Low Electrode Electroencephalography, a sta | Lopez et al. | 2022 | 4 | 4 | 5 | **4.20** | Low-density HAPPE variant — preprocessing action & baseline; recent |
| paper_011 | The HAPPE plus Event-Related (HAPPE+ER) software: A standardiz | Monachino et al. | 2022 | 4 | 4 | 5 | **4.20** | ERP HAPPE variant — preprocessing baseline; recent |
| paper_012 | NEAR: An artifact removal pipeline for human newborn EEG data | Kumaravel et al. | 2022 | 4 | 4 | 5 | **4.20** | Newborn artifact pipeline using LOF+ASR — concrete preprocessing actions; recent |
| paper_025 | Spectral parameterization for studying neurodevelopment: How a | Ostlund et al. | 2022 | 4 | 4 | 5 | **4.20** | How/why-to-parameterize tutorial — practical guidance for the metric; recent |
| paper_026 | Time-resolved parameterization of aperiodic and periodic brain | Wilson et al. | 2022 | 4 | 4 | 5 | **4.20** | SPRiNT — time-resolved aperiodic/periodic; relevant for windowed quality scoring |
| paper_046 | SAGA: A Scalable Framework for Optimizing Data Cleaning Pipeli | Siddiqi et al. | 2023 | 4 | 4 | 5 | **4.20** | Optimizes data-cleaning pipelines for ML — analogous framework; recent |
| paper_055 | Large Language Models for Automated Data Science: Introducing  | Hollmann et al. | 2023 | 4 | 4 | 5 | **4.20** | LLM does automated feature engineering — archetype for LLM-driven data-prep decisions |
| paper_057 | MLAgentBench: Evaluating Language Agents on Machine Learning E | Huang et al. | 2023 | 4 | 4 | 5 | **4.20** | Benchmark for LLM ML-engineering agents — evaluation template for the agent |
| paper_058 | AIDE: AI-Driven Exploration in the Space of Code | Jiang et al. | 2025 | 4 | 4 | 5 | **4.20** | Tree-search LLM agent over code/ML solutions — directly adaptable to preprocessing-config search |
| paper_061 | Data Interpreter: An LLM Agent For Data Science | Hong et al. | 2024 | 4 | 4 | 5 | **4.20** | LLM agent for end-to-end data science — relevant controller pattern |
| paper_017 | A Riemannian Modification of Artifact Subspace Reconstruction  | Blum et al. | 2019 | 4 | 4 | 4 | **4.00** | rASR — a concrete tunable artifact-handling action with a quality comparison |
| paper_019 | Automatic and robust noise suppression in EEG and MEG: The SOU | Mutanen et al. | 2018 | 4 | 4 | 4 | **4.00** | Data-driven noise suppression with explicit SNR framing — relevant action & metric flavor |
| paper_041 | Auto-Sklearn 2.0: Hands-free AutoML via Meta-Learning | Feurer et al. | 2020 | 4 | 4 | 4 | **4.00** | Hands-free AutoML via meta-learning — directly relevant to learning-to-select-config |
| paper_042 | Hyperparameter optimization: Foundations, algorithms, best pra | Bischl et al. | 2023 | 3 | 5 | 5 | **4.00** | Comprehensive HPO survey — methods reference for the search/optimization baseline |
| paper_045 | Data Cleaning and AutoML: Would an Optimizer Choose to Clean? | Neutatz et al. | 2022 | 4 | 4 | 4 | **4.00** | Asks whether an optimizer chooses to clean data — directly analogous to optimizing preprocessing choices |
| paper_066 | Autonomous chemical research with large language models | Boiko et al. | 2023 | 3 | 5 | 5 | **4.00** | Coscientist — landmark autonomous LLM science agent; template for tool-using scientific agents; Nature |

## Tier 3: Contextual — Applications, Reviews & Adjacent Methods / 應用、回顧與鄰近方法 (Score 3.5–3.9)

| ID | Title | Authors | Year | Rel | Qual | Rec | **Composite** | Rationale |
|----|-------|---------|------|-----|------|-----|---------------|-----------|
| paper_028 | An electrophysiological marker of arousal level in humans | Lendner et al. | 2020 | 3 | 5 | 4 | **3.80** | 1/f slope tracks arousal/state — evidence aperiodic carries real signal, relevant to noise-vs-signal framing |
| paper_048 | User-customized brain computer interfaces using Bayesian optim | Bashashati et al. | 2016 | 4 | 4 | 3 | **3.80** | BO to tune BCI per-user — concrete precedent for optimizing EEG pipeline params per subject |
| paper_030 | Periodic and aperiodic neural activity displays age-dependent  | Hill et al. | 2022 | 3 | 4 | 5 | **3.70** | Aperiodic changes with development — application context for the metric, not method |
| paper_062 | AutoGen: Enabling Next-Gen LLM Applications via Multi-Agent Co | Wu et al. | 2023 | 3 | 4 | 5 | **3.70** | Multi-agent LLM framework — infrastructure the agent could be built on |
| paper_064 | AutoML-GPT: Automatic Machine Learning with GPT | Zhang et al. | 2023 | 4 | 3 | 4 | **3.70** | LLM-driven AutoML — relevant but lighter-weight |
| paper_065 | AutoML-GPT: Large Language Model for AutoML | Tsai et al. | 2023 | 4 | 3 | 4 | **3.70** | LLM for AutoML (variant) — relevant |
| paper_067 | Augmenting large language models with chemistry tools | Bran et al. | 2024 | 3 | 4 | 5 | **3.70** | ChemCrow — LLM+domain tools; archetype for tool-augmented scientific agent |
| paper_068 | The AI Scientist: Towards Fully Automated Open-Ended Scientifi | Lu et al. | 2024 | 3 | 4 | 5 | **3.70** | Fully automated open-ended discovery agent — vision-level precedent |
| paper_069 | A Survey on Large Language Model based Autonomous Agents | Wang et al. | 2024 | 3 | 4 | 5 | **3.70** | Foundational survey framing LLM-agent design — orienting reference; hub |
| paper_014 | Automated pipeline for EEG artifact reduction (APPEAR) recorde | Mayeli et al. | 2021 | 3 | 4 | 4 | **3.50** | Automated EEG-fMRI artifact pipeline — relevant actions but niche (fMRI) population |
| paper_031 | Do age-related differences in aperiodic neural activity explai | Merkin et al. | 2022 | 3 | 4 | 4 | **3.50** | Aperiodic vs alpha in aging — application context |
| paper_036 | Aperiodic and Periodic Components of Ongoing Oscillatory Brain | Thuwal et al. | 2021 | 3 | 4 | 4 | **3.50** | Aperiodic/periodic linked to cognition across lifespan — application context |
| paper_043 | Bayesian Optimization for Adaptive Experimental Design: A Revi | Greenhill et al. | 2020 | 3 | 4 | 4 | **3.50** | BO review — method reference for parameter search |
| paper_047 | AutoML to Date and Beyond: Challenges and Opportunities | Santu et al. | 2020 | 3 | 4 | 4 | **3.50** | AutoML challenges/opportunities survey — context |
| paper_049 | Evaluation of Hyperparameter Optimization in Machine and Deep  | Cooney et al. | 2020 | 3 | 4 | 4 | **3.50** | HPO evaluation/benchmark — method context |
| paper_063 | HuggingGPT: Solving AI Tasks with ChatGPT and its Friends in H | Shen et al. | 2023 | 3 | 4 | 4 | **3.50** | LLM orchestrating tool/model calls — tool-use pattern relevant to invoking preprocessing tools |

---

## Borderline Papers / 邊緣論文 (Score 3.0–3.4)

> **⛳ Checkpoint 2: 邊緣打撈**
> Review the papers below. These scored close to the threshold and may contain relevant work the scoring missed.
> 請審核以下邊緣論文。這些論文分數接近門檻，可能包含評分遺漏的相關研究。
>
> Most are **aperiodic-exponent-as-clinical-biomarker** papers (Parkinson's, schizophrenia, ADHD, Alzheimer's, epilepsy). They are evidence the FOOOF signal/noise split is physiologically meaningful, but they don't study preprocessing or the quality metric directly. Tell me which (if any) to pull in.

| ID | Title | Authors | Year | Rel | Qual | Rec | **Composite** | Rationale | Hub? |
|----|-------|---------|------|-----|------|-----|---------------|-----------|------|
| paper_015 | The BeMoBIL Pipeline for automated analyses of multimodal mobi | Klug et al. | 2022 | 3 | 3 | 5 | **3.40** | Mobile multimodal automated pipeline — relevant automation but niche modality  — |
| paper_053 | Attention model of EEG signals based on reinforcement learning | Zhang et al. | 2024 | 3 | 3 | 5 | **3.40** | RL applied to EEG (attention/feature selection) — relevant RL-for-EEG precedent, modest rigor  — |
| paper_032 | The aperiodic exponent of subthalamic field potentials reflect | Wiest et al. | 2023 | 2 | 5 | 4 | **3.30** | Aperiodic exponent as E/I biomarker in LFP — strong but off-target population (DBS/LFP)  — |
| paper_016 | EPOS: EEG Processing Open-Source Scripts | Rodrigues et al. | 2021 | 3 | 3 | 4 | **3.20** | Semi-automated preprocessing scripts addressing researcher DoF — relevant context, modest rigor  — |
| paper_018 | Identification and Removal of Physiological Artifacts From Ele | Mannan et al. | 2018 | 3 | 3 | 4 | **3.20** | Review of artifact-removal methods — useful context, narrative review  — |
| paper_020 | Stability, change, and reliable individual differences in elec | Lopez et al. | 2023 | 3 | 3 | 4 | **3.20** | Reliability review tying preprocessing software to denoising metrics — supporting context  — |
| paper_054 | The importance of ocular artifact removal in single-trial ERP  | Kotowski et al. | 2022 | 3 | 3 | 4 | **3.20** | Shows artifact-removal choice changes results — supports sensitivity argument  — |
| paper_029 | Separating scale-free and oscillatory components of neural act | Rácz et al. | 2021 | 3 | 3 | 3 | **3.00** | Fractal vs oscillatory in SZ — application showing decomposition matters, but clinical focus  — |
| paper_033 | Resting-state EEG signatures of Alzheimer's disease are driven | Kopčanová et al. | 2023 | 2 | 4 | 4 | **3.00** | AD spectral signatures — clinical application, tangential to preprocessing  — |
| paper_034 | Aperiodic Neural Activity is a Better Predictor of Schizophren | Peterson et al. | 2023 | 2 | 4 | 4 | **3.00** | Aperiodic>oscillation for SZ classification — supports aperiodic-as-signal but clinical  — |
| paper_035 | EEG power spectral slope differs by ADHD status and stimulant  | Robertson et al. | 2019 | 2 | 4 | 4 | **3.00** | Spectral slope as ADHD marker — application, tangential  — |
| paper_037 | Aperiodic neural activity reflects metacontrol | Zhang et al. | 2023 | 2 | 4 | 4 | **3.00** | Aperiodic & cognitive control — application, tangential  — |
| paper_038 | Excitation/Inhibition balance relates to cognitive function an | Duma et al. | 2024 | 2 | 4 | 4 | **3.00** | Aperiodic exponent E/I in epilepsy — clinical application, tangential  — |

---

## Excluded Papers / 排除論文 (Score < 3.0)

| ID | Title | Year | Rel | Qual | Rec | **Composite** | Exclusion Reason / 排除原因 |
|----|-------|------|-----|------|-----|---------------|---------------------------|
| paper_051 | An investigation of multimodal EMG-EEG fusion strategie | 2025 | 2 | 3 | 5 | **2.90** | Downstream task (prosthetic control); not about preprocessing selection or quality metrics |
| paper_052 | Electroencephalography (EEG) Based Neonatal Sleep Stagi | 2023 | 2 | 3 | 4 | **2.70** | Downstream classification task; preprocessing is incidental, not the focus |
| paper_050 | Long Short Term Memory Hyperparameter Optimization for  | 2018 | 2 | 3 | 3 | **2.50** | Generic LSTM HPO for an unrelated task; only keyword overlap with the search idea |

---

## Hub Paper Summary / 核心引用論文摘要

| ID | Title | In-Degree | Cluster | Status | Note |
|----|-------|-----------|---------|--------|------|
| paper_021 | Parameterizing neural power spectra into periodic  | 14 | B fooof spectral | Included | Cited by 14 in-collection |
| paper_069 | A Survey on Large Language Model based Autonomous  | 8 | D llm agents | Included | Cited by 8 in-collection |
| paper_001 | The PREP pipeline: standardized preprocessing for  | 7 | A eeg preprocessing | Included | Cited by 7 in-collection |
| paper_004 | ICLabel: An automated electroencephalographic inde | 6 | A eeg preprocessing | Included | Cited by 6 in-collection |
| paper_003 | Autoreject: Automated artifact rejection for MEG a | 6 | A eeg preprocessing | Included | Cited by 6 in-collection |
| paper_040 | Efficient and Robust Automated Machine Learning | 6 | C automl hpo | Included | Cited by 6 in-collection |
| paper_002 | The Harvard Automated Processing Pipeline for Elec | 4 | A eeg preprocessing | Included | Cited by 4 in-collection |
| paper_039 | Auto-WEKA: Combined Selection and Hyperparameter O | 4 | C automl hpo | Included | Cited by 4 in-collection |
| paper_062 | AutoGen: Enabling Next-Gen LLM Applications via Mu | 4 | D llm agents | Included | Cited by 4 in-collection |
| paper_023 | Separating Fractal and Oscillatory Components in t | 3 | B fooof spectral | Included | Cited by 3 in-collection |

---

Files / 檔案: `step3_screening_results.md`, `step3_shortlist.json`
Next step / 下一步: `/research-export` (after Checkpoint 2)
