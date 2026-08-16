---
session_id: "20260815"
topic: "LLM-informed warm-starting and prior injection for Bayesian optimization (CASH) of subject-specific EEG motor-imagery preprocessing pipelines, with cross-subject transfer"
date: "2026-08-15"
step: 3
threshold: 3.5
weights: "relevance=0.50, quality=0.30, recency_impact=0.20"
---

# Screening Results / 篩選結果

> Topic / 研究主題: LLM-informed warm-starting and prior injection for BO (CASH) of subject-specific EEG MI pipelines, with cross-subject transfer
> Papers screened / 篩選論文數: 357
> Date / 篩選日期: 2026-08-15
> Threshold / 門檻: composite >= 3.5（composite score，綜合加權分數）
> Screening method / 篩選方式: 6 parallel screening agents with a shared PICO-anchored rubric; composites computed deterministically by script / 6 個平行篩選 agent 使用同一套 PICO 錨定評分規則；composite 由腳本統一計算

## Screening Criteria / 篩選標準

### Inclusion / 納入條件（符合任一支柱即屬相關）
- **Pillar A**: LLMs guiding/initializing/acting as optimizer for HPO/BO/AutoML/pipeline search / LLM 引導、初始化或擔任 HPO／貝葉斯最佳化／AutoML 管線搜尋的最佳化器
- **Pillar B**: Warm-starting, meta-learning, or transfer for BO/HPO — search-history reuse, learned priors, defaults, few-shot BO, zero-shot config recommendation / BO/HPO 的暖啟動、meta-learning 或遷移——搜尋歷史重用、學習先驗、預設值、few-shot BO、零樣本配置推薦
- **Pillar C**: AutoML systems & CASH — auto-sklearn, TPOT, VolcanoML, SMAC, search-space decomposition, benchmarks & evaluation methodology / AutoML 系統與 CASH——搜尋空間分解、基準評測與評估方法學
- **Pillar D**: EEG/BCI decoding pipeline optimization — automated preprocessing, HPO/NAS for EEG, subject-specific config selection, cross-subject config transfer / EEG/BCI 解碼管線最佳化——自動化前處理、EEG 專用 HPO/NAS、受試者特定配置選擇、跨受試者配置遷移
- Empirical study, system paper, benchmark, or systematic review / 實證研究、系統論文、基準評測或系統性綜述

### Exclusion / 排除條件
- Keyword-only match: mentions optimization/EEG but studies a different question (e.g., clinical EEG application with a fixed pipeline and no pipeline search) / 僅關鍵字重疊：如固定管線的臨床 EEG 應用、無管線搜尋
- Domain-specific optimization applications with no methodological contribution to HPO/AutoML transfer (buildings, climate, generic forecasting) / 無方法學貢獻的領域應用（建築、氣候、一般預測）
- No original data or method: editorials, commentaries, proposals without evaluation / 無原始數據或方法：社論、評論、無評估的提案
- Interpretability/XAI, generic deep-learning surveys unrelated to pipeline search / 與管線搜尋無關的可解釋性／通用深度學習綜述

## Summary / 摘要

| Category / 分類 | Count / 數量 | Percentage / 百分比 |
|-----------------|-------------|-------------------|
| **Included / 納入** | **115** | **32%** |
| Borderline / 邊緣 | 61 | 17% |
| Excluded / 排除 | 181 | 51% |

Composite distribution / 分數分佈: min 1.30, max 4.80, mean 3.11 — 排除率高反映 Step 2 刻意寬鬆收集（OpenAlex 全文檢索雜訊在此過濾）。

## Tier 1: Core: Direct Hits on Warm-Started Pipeline Search / 核心：暖啟動管線搜尋直接命中 (Score >= 4.5)

| ID | Title | Authors | Year | Rel | Qual | Rec | **Composite** | Rationale |
|----|-------|---------|------|-----|-----|-----|---------------|-----------|
| paper_162 | Auto-WEKA: Automatic Model Selection and Hyperparameter Optimization in WEKA | Lars Kotthoff et al. | 2019 | 5 | 5 | 4 | **4.80** | Auto-WEKA defined the CASH problem; exact Pillar C anchor |
| paper_163 | Hyperband: A Novel Bandit-Based Approach to Hyperparameter Optimization | Lisha Li et al. | 2016 | 5 | 5 | 4 | **4.80** | Hyperband: seminal bandit HPO method; Pillar C core |
| paper_129 | Hyperparameter optimization: Foundations, algorithms, best practices, and open c | Bernd Bischl et al. | 2023 | 4.5 | 5 | 5 | **4.75** | Authoritative HPO foundations review; core Pillar C background |
| paper_188 | VolcanoML: Speeding up End-to-End AutoML via Scalable Search Space Decomposition | Yang Li et al. | 2021 | 5 | 4.5 | 4 | **4.65** | VolcanoML core system paper (anchor=5), scalable CASH decomposition |
| paper_341 | Benchmark and Survey of Automated Machine Learning Frameworks | M. Zöller et al. | 2019 | 5 | 4.5 | 4 | **4.65** | AutoML framework survey plus benchmark; Pillar C evaluation methodology |
| paper_349 | HPOBench: A Collection of Reproducible Multi-Fidelity Benchmark Problems for HPO | Katharina Eggensperger et al. | 2021 | 5 | 4.5 | 4 | **4.65** | HPOBench reproducible HPO benchmark suite; Pillar C methodology |
| paper_170 | AutoGluon-Tabular: Robust and Accurate AutoML for Structured Data | Nick Erickson et al. | 2020 | 5 | 4.5 | 3.5 | **4.55** | AutoGluon-Tabular AutoML system with extensive benchmark; Pillar C |
| paper_042 | Large Language Models to Enhance Bayesian Optimization | Tennison Liu et al. | 2024 | 5 | 4 | 4 | **4.50** | LLAMBO: LLM-enhanced BO with warm-starting; anchor paper for RQ. |
| paper_130 | Automated Machine Learning | Frank Hutter et al. | 2019 | 5 | 4 | 4 | **4.50** | The AutoML book; canonical Pillar C reference |
| paper_161 | Hyperopt: a Python library for model selection and hyperparameter optimization | James Bergstra et al. | 2015 | 5 | 4 | 4 | **4.50** | Hyperopt: canonical model-selection/HPO library; Pillar C core |
| paper_165 | Initializing Bayesian Hyperparameter Optimization via Meta-Learning | Matthias Feurer et al. | 2015 | 5 | 4 | 4 | **4.50** | Feurer 2015 meta-learning BO initialization; named anchor paper |
| paper_166 | TPOT: A Tree-Based Pipeline Optimization Tool for Automating Machine Learning | Randal S. Olson et al. | 2019 | 5 | 4 | 4 | **4.50** | TPOT pipeline optimization system; Pillar C anchor |
| paper_175 | Evaluation of a Tree-based Pipeline Optimization Tool for Automating Data Scienc | Randal S. Olson et al. | 2016 | 5 | 4 | 4 | **4.50** | TPOT evaluation paper; Pillar C anchor system |
| paper_187 | Efficient End-to-End AutoML via Scalable Search Space Decomposition | Yang Li et al. | 2022 | 5 | 4 | 4 | **4.50** | VolcanoML search-space decomposition; explicit anchor system for CASH |
| paper_347 | Optuna: A Next-generation Hyperparameter Optimization Framework | Takuya Akiba et al. | 2019 | 5 | 4 | 4 | **4.50** | Optuna HPO framework; seminal Pillar C system |
| paper_348 | πBO: Augmenting Acquisition Functions with User Beliefs for Bayesian Optimizatio | Carl Hvarfner et al. | 2022 | 5 | 4 | 4 | **4.50** | piBO prior injection into BO acquisition; explicit anchor paper |

## Tier 2: Strong Supporting: Pillar Methods & Systems / 強支持：支柱方法與系統 (Score 4.0–4.4)

| ID | Title | Authors | Year | Rel | Qual | Rec | **Composite** | Rationale |
|----|-------|---------|------|-----|-----|-----|---------------|-----------|
| paper_003 | When Is an LLM Worth It for Hyperparameter Optimization? A Budget-Matched Study  | Carson Rodrigues et al. | 2026 | 5 | 4.5 | 3 | **4.45** | Rigorous budget-matched test of LLM warm-start HPO value; cautionary. |
| paper_138 | Transfer Learning for Bayesian Optimization: A Survey | Tianyi Bai et al. | 2023 | 5 | 4 | 3.5 | **4.40** | Survey of transfer learning for BO; exact Pillar B core |
| paper_144 | Towards Learning Universal Hyperparameter Optimizers with Transformers | Yutian Chen et al. | 2022 | 5 | 4 | 3.5 | **4.40** | OptFormer: text-based transformer HPO transfer; Pillar A/B intersection |
| paper_194 | OBOE: Collaborative Filtering for AutoML Model Selection | Chengrun Yang et al. | 2018 | 5 | 4 | 3.5 | **4.40** | Zero-shot model/config recommendation via collaborative-filtering meta-learning |
| paper_211 | DivBO: Diversity-aware CASH for Ensemble Learning | Yu Shen et al. | 2023 | 5 | 4 | 3.5 | **4.40** | Diversity-aware BO for CASH; core pillar C method |
| paper_336 | OpenBox: A Generalized Black-box Optimization Service | Yang Li et al. | 2021 | 5 | 4 | 3.5 | **4.40** | OpenBox BBO service, explicitly named Pillar C system |
| paper_340 | An ADMM Based Framework for AutoML Pipeline Configuration | Sijia Liu et al. | 2019 | 5 | 4 | 3.5 | **4.40** | ADMM pipeline configuration with search-space decomposition; core CASH |
| paper_343 | Hyperparameter Importance Across Datasets | J. V. Rijn et al. | 2017 | 5 | 4 | 3.5 | **4.40** | Meta-learned hyperparameter importance and good values across datasets |
| paper_346 | Few-Shot Bayesian Optimization with Deep Kernel Surrogates | Martin Wistuba et al. | 2021 | 5 | 4 | 3.5 | **4.40** | Few-shot BO with transfer deep-kernel surrogates; core Pillar B |
| paper_350 | HPO-B: A Large-Scale Reproducible Benchmark for Black-Box HPO based on OpenML | Sebastian Pineda Arango et al. | 2021 | 5 | 4 | 3.5 | **4.40** | HPO-B benchmark for transfer HPO evaluation; Pillars B and C |
| paper_217 | Subject-Adaptive EEG Decoding via Filter-Bank Neural Architecture Search for BCI | Chong-Chen Wang et al. | 2026 | 5 | 3.5 | 4 | **4.35** | Subject-specific NAS for EEG decoding; exact pillar D intersection |
| paper_002 | Reproducibility Study of Large Language Model Bayesian Optimization | Adam Rychert et al. | 2025 | 5 | 4 | 3 | **4.30** | Reproduces LLAMBO warm-starting claims; direct Pillar A evidence. |
| paper_134 | Warm Starting CMA-ES for Hyperparameter Optimization | Masahiro Nomura et al. | 2021 | 5 | 4 | 3 | **4.30** | Warm-starting an HPO optimizer; squarely Pillar B |
| paper_136 | Regularized Evolution for Image Classifier Architecture Search | Esteban Real et al. | 2019 | 4 | 5 | 4 | **4.30** | Seminal evolutionary NAS (AmoebaNet); general NAS system |
| paper_178 | An Open Source AutoML Benchmark | Pieter Gijsbers et al. | 2019 | 5 | 4 | 3 | **4.30** | Open AutoML benchmark framework; explicit Pillar C item |
| paper_205 | Put CASH on Bandits: A Max K-Armed Problem for Automated Machine Learning | Amir Rezaei Balef et al. | 2025 | 5 | 4 | 3 | **4.30** | Max k-armed bandit for CASH with benchmark evaluation |
| paper_333 | TransBO: Hyperparameter Optimization via Two-Phase Transfer Learning | Yang Li et al. | 2022 | 5 | 4 | 3 | **4.30** | Two-phase transfer learning for HPO; core Pillar B |
| paper_334 | Transfer Learning based Search Space Design for Hyperparameter Tuning | Yang Li et al. | 2022 | 5 | 4 | 3 | **4.30** | Transfer-learned search space design for BO; core Pillar B |
| paper_338 | Efficient Automatic CASH via Rising Bandits | Yang Li et al. | 2020 | 5 | 4 | 3 | **4.30** | CASH via rising bandits; core CASH decomposition method |
| paper_007 | CoFEH: LLM-driven Feature Engineering Empowered by Collaborative Bayesian Hyperp | Beicheng Xu et al. | 2026 | 4.5 | 4 | 4 | **4.25** | KDD LLM feature engineering coupled with collaborative BO HPO. |
| paper_028 | BOHB: Robust and Efficient Hyperparameter Optimization at Scale | Stefan Falkner et al. | 2018 | 4.5 | 4 | 4 | **4.25** | BOHB: foundational scalable HPO combining BO and Hyperband; Pillar C. |
| paper_131 | Meta-Learning | Joaquin Vanschoren et al. | 2019 | 4.5 | 4 | 4 | **4.25** | Vanschoren meta-learning overview; core Pillar B foundation |
| paper_171 | Auto-Keras: An Efficient Neural Architecture Search System | Haifeng Jin et al. | 2019 | 4.5 | 4 | 4 | **4.25** | Auto-Keras BO-guided NAS system; Pillar C |
| paper_173 | SMAC3: A Versatile Bayesian Optimization Package for Hyperparameter Optimization | Marius Lindauer et al. | 2021 | 5 | 3.5 | 3.5 | **4.25** | SMAC3: the cold-start baseline named in the research question |
| paper_227 | MDH-NAS: Accelerating EEG Signal Classification With Mixed-Level Differentiable  | Lixian Zhu et al. | 2025 | 4.5 | 4 | 4 | **4.25** | Differentiable hardware-aware NAS for EEG classification; pillar D |
| paper_235 | LM-Searcher: Cross-domain Neural Architecture Search with LLMs via Unified Numer | Yuxuan Hu et al. | 2025 | 5 | 3.5 | 3.5 | **4.25** | LLM-driven cross-domain NAS; pillar A core method |
| paper_253 | Evaluation of Hyperparameter Optimization in Machine and Deep Learning Methods f | Ciaran Cooney et al. | 2020 | 5 | 3.5 | 3.5 | **4.25** | HPO evaluation for EEG decoding pipelines; exact pillar D core method |
| paper_057 | Sequential Large Language Model-Based Hyper-parameter Optimization | Kanan Mahammadli et al. | 2024 | 5 | 4 | 2.5 | **4.20** | Sequential LLM-based HPO framework benchmarked against BO; Pillar A. |
| paper_252 | Systematic review on neural architecture search | Sasan Salmani Pour Avval et al. | 2025 | 4 | 4 | 5 | **4.20** | Systematic NAS/AutoML review, squarely pillar C; recent, well cited |
| paper_167 | Towards Automated Machine Learning: Evaluation and Comparison of AutoML Approach | Anh Minh Truong et al. | 2019 | 4.5 | 4 | 3.5 | **4.15** | AutoML tools evaluation and comparison; Pillar C benchmarking |
| paper_191 | In-Context Decision Making for Optimizing Complex AutoML Pipelines | Amir Rezaei Balef et al. | 2025 | 5 | 3.5 | 3 | **4.15** | Extends CASH with in-context PFN-based pipeline selection |
| paper_196 | Cascaded Algorithm-Selection and Hyper-Parameter Optimization with Extreme-Regio | Yi-Qi Hu et al. | 2019 | 5 | 3.5 | 3 | **4.15** | Core CASH method: bandit algorithm selection cascaded with HPO |
| paper_038 | Learning search spaces for Bayesian optimization: Another view of hyperparameter | Valerio Perrone et al. | 2019 | 5 | 3.5 | 2.5 | **4.05** | Learns BO search spaces from prior tasks; Pillar B core. |
| paper_041 | Hyperparameter Optimization: Foundations, Algorithms, Best Practices and Open Ch | Bernd Bischl et al. | 2021 | 4.5 | 4 | 3 | **4.05** | Comprehensive HPO survey covering BO and best practices; Pillars B/C. |
| paper_055 | Evidence-Gated LLM Priors for Multi-Objective Bayesian Optimization | Jiangyu Chen et al. | 2026 | 5 | 3.5 | 2.5 | **4.05** | Calibrated gating of LLM expert priors in BO; prior injection. |
| paper_066 | LLMs for Bayesian Optimization in Scientific Domains: Are We There Yet? | Rushil Gupta et al. | 2025 | 4.5 | 4 | 3 | **4.05** | Rigorous negative evaluation of LLMs as BO/experimental-design agents (Pillar A) |
| paper_149 | Pitfalls and Remedies for Multi-Task Bayesian Optimization | Carl Hvarfner et al. | 2026 | 5 | 3.5 | 2.5 | **4.05** | Analyzes multi-task GP warm-start failures in BO; core Pillar B |
| paper_169 | Efficient Benchmarking of Hyperparameter Optimizers via Surrogates | Katharina Eggensperger et al. | 2015 | 4.5 | 4 | 3 | **4.05** | Surrogate benchmarks for HPO evaluation methodology; Pillar C |
| paper_172 | Analysis of the AutoML Challenge Series 2015–2018 | Isabelle Guyon et al. | 2019 | 4.5 | 4 | 3 | **4.05** | AutoML challenge series analysis; Pillar C evaluation methodology |
| paper_179 | AutoML for Multi-Label Classification: Overview and Empirical Evaluation | Marcel Wever et al. | 2021 | 4 | 4.5 | 3.5 | **4.05** | AutoML search-space/optimizer study for multi-label; Pillar C |
| paper_203 | An experimental survey and Perspective View on Meta-Learning for Automated Algor | Moncef Garouani et al. | 2025 | 4.5 | 4 | 3 | **4.05** | Experimental survey of meta-learning for algorithm selection/parametrization (pillar B) |
| paper_351 | Meta-learning for symbolic hyperparameter defaults | P. Gijsbers et al. | 2021 | 5 | 3.5 | 2.5 | **4.05** | Zero-shot meta-learned symbolic defaults; core Pillar B |
| paper_354 | Meta-Surrogate Benchmarking for Hyperparameter Optimization | Aaron Klein et al. | 2019 | 4.5 | 4 | 3 | **4.05** | Meta-surrogate benchmarking for cheap HPO comparison; Pillar C |
| paper_355 | Learning multiple defaults for machine learning algorithms | Florian Pfisterer et al. | 2018 | 5 | 3.5 | 2.5 | **4.05** | Learned default configuration lists; zero-shot config recommendation |
| paper_357 | Deep learning with convolutional neural networks for EEG decoding and visualizat | R. Schirrmeister et al. | 2017 | 3.5 | 5 | 4 | **4.05** | Seminal EEG ConvNet decoding with systematic design-choice evaluation |
| paper_022 | EA-HAS-Bench and Language-Enhanced Shrinkage Search for Energy-Aware NAS | Cairong Zhao et al. | 2025 | 4 | 4 | 4 | **4.00** | Energy-aware NAS/HPO joint-space benchmark with language-enhanced search; TPAMI. |
| paper_039 | Accurate predictions on small data with a tabular foundation model | Noah Hollmann et al. | 2025 | 3 | 5 | 5 | **4.00** | Meta-learned tabular foundation model; AutoML-adjacent, not config transfer. |
| paper_046 | DARTS: Differentiable Architecture Search | Hanxiao Liu et al. | 2018 | 4 | 4 | 4 | **4.00** | DARTS: seminal differentiable NAS; Pillar C-adjacent search method. |
| paper_135 | Designing Neural Network Architectures using Reinforcement Learning | Bowen Baker et al. | 2016 | 4 | 4 | 4 | **4.00** | MetaQNN RL-based NAS; general architecture search system |
| paper_168 | A survey on multi-objective hyperparameter optimization algorithms for machine l | Alejandro Morales-Hernández et al. | 2022 | 4 | 4 | 4 | **4.00** | Systematic survey of multi-objective HPO; Pillar C |
| paper_177 | Automated machine learning: Review of the state-of-the-art and opportunities for | Jonathan Waring et al. | 2020 | 4 | 4 | 4 | **4.00** | AutoML review for healthcare; Pillar C plus biomedical adjacency |
| paper_183 | Eight years of AutoML: categorisation, review and trends | Rafael Barbudo et al. | 2023 | 4 | 4 | 4 | **4.00** | Comprehensive AutoML review squarely in pillar C |
| paper_222 | Cross task neural architecture search for EEG signal recognition | Yiqun Duan et al. | 2023 | 4.5 | 3.5 | 3.5 | **4.00** | Cross-task NAS framework for EEG decoding; pillar D |
| paper_339 | AutoML: A Survey of the State-of-the-Art | Xin He et al. | 2019 | 4 | 4 | 4 | **4.00** | Highly cited comprehensive AutoML survey; Pillar C |
| paper_345 | Fast Bayesian Optimization of Machine Learning Hyperparameters on Large Datasets | Aaron Klein et al. | 2016 | 4 | 4 | 4 | **4.00** | FABOLAS multi-fidelity BO for HPO; seminal, not transfer |

## Tier 3: Contextual: Adjacent Methods & Reviews / 脈絡：鄰近方法與綜述 (Score 3.5–3.9)

| ID | Title | Authors | Year | Rel | Qual | Rec | **Composite** | Rationale |
|----|-------|---------|------|-----|-----|-----|---------------|-----------|
| paper_005 | Tree-Structured Synergy of Large Language Models and Bayesian Optimization for E | Beicheng Xu et al. | 2026 | 5 | 3 | 2.5 | **3.90** | LLM+BO hybrid for CASH cold-start; exact RQ intersection. |
| paper_008 | LangBO: A Framework for Language-Guided Prior Integration in Bayesian Optimizati | Philip Topalis et al. | 2025 | 5 | 3 | 2.5 | **3.90** | LLM/RAG converts expert knowledge into BO priors; prior injection. |
| paper_050 | Towards Automated Deep Learning: Efficient Joint Neural Architecture and Hyperpa | Arber Zela et al. | 2018 | 4.5 | 3.5 | 3 | **3.90** | Joint NAS+HPO with BOHB; early AutoDL, Pillar C. |
| paper_071 | MetaLLMix : An XAI Aided LLM-Meta-learning Based Approach for Hyper-parameters O | Mohamed Bal-Ghaoui et al. | 2025 | 5 | 3 | 2.5 | **3.90** | LLM plus meta-learning zero-shot HPO; exact Pillar A/B intersection |
| paper_011 | Unleashing LLMs in Bayesian Optimization: Preference-Guided Framework for Scient | Xinzhe Yuan et al. | 2026 | 4.5 | 3 | 3.5 | **3.85** | LLM preference-guided BO tackling cold-start; scientific discovery domain. |
| paper_164 | An improved hyperparameter optimization framework for AutoML systems using evolu | Amala Mary Vincent et al. | 2023 | 4 | 3.5 | 4 | **3.85** | Evolutionary HPO framework for AutoML systems; Pillar C |
| paper_182 | Multi-Objective Hyperparameter Optimization in Machine Learning—An Overview | Florian Karl et al. | 2023 | 3.5 | 4 | 4.5 | **3.85** | Solid HPO survey, but multi-objective focus differs from warm-start/CASH question |
| paper_228 | Spiking Spatiotemporal Neural Architecture Search for EEG-Based Emotion Recognit | Wei Li et al. | 2025 | 4 | 3.5 | 4 | **3.85** | Spiking NAS for EEG emotion recognition; pillar D NAS |
| paper_001 | HALO: Hyperparameter Auto-tuning via LLM Optimizer for Rapid, Interpretable Auto | Zhaoyu Chen et al. | 2025 | 5 | 3 | 2 | **3.80** | LLM optimizer for AutoML HPO addressing BO cold-start; Pillar A core. |
| paper_056 | LLaMEA-BO: A Large Language Model Evolutionary Algorithm for Automatically Gener | Wenhu Li et al. | 2025 | 4.5 | 3.5 | 2.5 | **3.80** | LLM evolutionary generation of BO algorithm code; Pillar A. |
| paper_058 | Distilling and exploiting quantitative insights from Large Language Models for e | Roshan Patel et al. | 2025 | 4.5 | 3.5 | 2.5 | **3.80** | LLM knowledge distilled into BO priors for chemistry; transfer-flavored. |
| paper_061 | A Sober Look at LLMs for Material Discovery: Are They Actually Good for Bayesian | Agustinus Kristiadi et al. | 2024 | 4 | 4 | 3 | **3.80** | Critical benchmark of LLM-informed BO (Pillar A), molecules not HPO |
| paper_088 | Genetic Programming-based AutoML for EEG Signal Classification - A Comparative S | Isabel Miranda et al. | 2022 | 5 | 3 | 2 | **3.80** | AutoML (genetic programming) for EEG classification; Pillar C/D intersection |
| paper_335 | Hyper-Tune: Towards Efficient Hyper-parameter Tuning at Scale | Yang Li et al. | 2022 | 4 | 4 | 3 | **3.80** | Scalable distributed HPO system; Pillar C system paper |
| paper_337 | MFES-HB: Efficient Hyperband with Multi-Fidelity Quality Measurements | Yang Li et al. | 2020 | 4 | 4 | 3 | **3.80** | Multi-fidelity Hyperband HPO method; Pillar C |
| paper_213 | Automated Machine Learning: State-of-The-Art and Open Challenges | Radwa Elshawi et al. | 2019 | 4 | 3.5 | 3.5 | **3.75** | Well-known narrative survey of CASH/AutoML state of the art |
| paper_220 | EEG Foundation Challenge: From Cross-Task to Cross-Subject EEG Decoding | Bruno Aristimunha et al. | 2025 | 3.5 | 4 | 4 | **3.75** | Zero-shot cross-subject EEG decoding benchmark; models, not configurations |
| paper_236 | Causal-aware Graph Neural Architecture Search under Distribution Shifts | Peiwen Li et al. | 2025 | 3.5 | 4 | 4 | **3.75** | Graph NAS under distribution shift; methodologically relevant, domain mismatch |
| paper_204 | An Extensive Experimental Evaluation of Automated Machine Learning Methods for R | Márcio P. Basgalupp et al. | 2020 | 4 | 4 | 2.5 | **3.70** | Extensive experimental comparison of CASH/AutoML recommendation methods |
| paper_219 | EmT: A Novel Transformer for Generalized Cross-Subject EEG Emotion Recognition | Yi Ding et al. | 2024 | 3 | 4 | 5 | **3.70** | Cross-subject EEG emotion model; no pipeline/config search |
| paper_243 | NEED: Cross-Subject and Cross-Task Generalization for Video and Image Reconstruc | Shuai Huang et al. | 2025 | 3 | 4 | 5 | **3.70** | Zero-shot cross-subject EEG generalization of models, not configs; NeurIPS |
| paper_009 | A Meta-Knowledge-Augmented LLM Framework for Hyperparameter Optimization in Time | Ons Saadallah et al. | 2026 | 4.5 | 3 | 2.5 | **3.65** | Meta-knowledge LLM plus BO hybrid HPO for time-series. |
| paper_084 | Personalized Classifier Selection for EEG-Based BCIs | Javad Rahimipour Anaraki et al. | 2024 | 4.5 | 3 | 2.5 | **3.65** | Automated personalized per-subject classifier selection for BCI; Pillar D core |
| paper_152 | Efficient Automatic Tuning for Data-driven Model Predictive Control via Meta-Lea | Baoyu Li et al. | 2024 | 4.5 | 3 | 2.5 | **3.65** | Portfolio meta-learning warm-starts BO tuning; squarely Pillar B |
| paper_158 | Monte Carlo Tree Search based Space Transfer for Black-box Optimization | Shukuan Wang et al. | 2024 | 4.5 | 3 | 2.5 | **3.65** | Search-space transfer to accelerate BO; Pillar B core |
| paper_210 | PSEO: Optimizing Post-hoc Stacking Ensemble Through Hyperparameter Tuning | Beicheng Xu et al. | 2025 | 4 | 3.5 | 3 | **3.65** | CASH post-hoc stacking-ensemble hyperparameter tuning |
| paper_082 | BrainFusion: a Low‐Code, Reproducible, and Deployable Software Framework for Mul | Wenhao Li et al. | 2025 | 3.5 | 3.5 | 4 | **3.60** | Automated standardized EEG preprocessing pipeline framework; no HPO search |
| paper_006 | EvoContext: Evolving Contextual Examples by Genetic Algorithm for Enhanced Hyper | Yutian Xu et al. | 2025 | 4.5 | 3 | 2 | **3.55** | LLM-based HPO with genetic context-example evolution; Pillar A method. |
| paper_020 | Beyond the Prompt: Assessing Domain Knowledge Strategies for High-Dimensional LL | Srinath Srinivasan et al. | 2026 | 4 | 3.5 | 2.5 | **3.55** | Compares knowledge-injection strategies for LLM-driven optimization; SE domain. |
| paper_059 | LILO: Bayesian Optimization with Natural Language Feedback | Katarzyna Kobalczyk et al. | 2025 | 4 | 3.5 | 2.5 | **3.55** | LLM translates language feedback into BO preference signals; Pillar A. |
| paper_068 | Thompson Sampling via Fine-Tuning of LLMs | Nicolas Menet et al. | 2025 | 4 | 3.5 | 2.5 | **3.55** | LLM priors injected into Thompson-sampling BO; Pillar A core method |
| paper_070 | Elicitation Matters: How Prompts and Query Protocols Shape LLM Surrogates under  | Ge Lei et al. | 2026 | 4.5 | 3 | 2 | **3.55** | LLM-as-surrogate for BO, prompt-elicited priors; squarely Pillar A |
| paper_087 | Fall compensation detection from EEG using neuroevolution and genetic hyperparam | Jordan J. Bird et al. | 2023 | 4.5 | 3 | 2 | **3.55** | Evolutionary HPO and GP pipeline search for EEG decoding; Pillar D |
| paper_114 | Transfer learning for motor imagery based brain-computer interfaces: A tutorial. | Dongrui Wu et al. | 2022 | 3.5 | 4 | 3 | **3.55** | Comprehensive tutorial on cross-subject MI transfer across full pipeline stages |
| paper_154 | Optimizing Closed-Loop Performance with Data from Similar Systems: A Bayesian Me | Ankush Chakrabarty et al. | 2022 | 4.5 | 3 | 2 | **3.55** | Bayesian meta-learned surrogate priors from similar systems; Pillar B core |
| paper_074 | Informing Acquisition Functions via Foundation Models for Molecular Discovery | Qi Chen et al. | 2025 | 4 | 3 | 3 | **3.50** | Foundation-model priors informing BO acquisition; Pillar A, molecular domain |
| paper_132 | Informed Machine Learning - A Taxonomy and Survey of Integrating Prior Knowledge | Laura von Rueden et al. | 2021 | 3 | 4 | 4 | **3.50** | Prior-knowledge injection taxonomy for training, not for BO/HPO |
| paper_176 | Symbolic Discovery of Optimization Algorithms | Xiangning Chen et al. | 2023 | 3 | 4 | 4 | **3.50** | Program search discovers training optimizers; automated search, different target |
| paper_200 | Automatic Gradient Boosting | Janek Thomas et al. | 2018 | 4 | 3 | 3 | **3.50** | AutoML system restricting CASH to gradient boosting |
| paper_202 | Meta-Learning from Learning Curves for Budget-Limited Algorithm Selection | Manh Hung Nguyen et al. | 2024 | 4 | 3 | 3 | **3.50** | Meta-learning from learning curves for budget-limited algorithm selection |
| paper_239 | Dual-Level Cross-Modality Neural Architecture Search for Guided Image Super-Reso | Zhiwei Zhong et al. | 2025 | 3 | 4 | 4 | **3.50** | NAS applied to image super-resolution; different domain |
| paper_245 | Deep learning for electroencephalogram (EEG) classification tasks: a review | Alexander Craik et al. | 2019 | 3 | 4 | 4 | **3.50** | Seminal DL-for-EEG review covering decoding design space; no automated search |
| paper_251 | Cross-Domain MLP and CNN Transfer Learning for Biological Signal Processing: EEG | Jordan J. Bird et al. | 2020 | 4 | 3 | 3 | **3.50** | Evolutionary hyperparameter search plus transfer of optimized topology across EEG/EMG domains |
| paper_259 | Long Short Term Memory Hyperparameter Optimization for a Neural Network Based Em | Bahareh Nakisa et al. | 2018 | 4 | 3 | 3 | **3.50** | Automated differential-evolution HPO for EEG-based emotion classifier; pillar D method |

---

## Borderline Papers / 邊緣論文 (Score 3.0–3.4)

| ID | Title | Authors | Year | Rel | Qual | Rec | **Composite** | Rationale | Hub? |
|----|-------|---------|------|-----|-----|-----|---------------|-----------|------|
| paper_258 | IFNet: An Interactive Frequency Convolutional Neural Network for Enhancing Motor | Jiaheng Wang et al. | 2023 | 3 | 3.5 | 4.5 | **3.45** | MI EEG decoding population match, but fixed architecture, no search | — |
| paper_260 | CTNet: a convolutional transformer network for EEG-based motor imagery classific | Wei Zhao et al. | 2024 | 3 | 3.5 | 4.5 | **3.45** | MI decoding population match; fixed transformer pipeline, no HPO | — |
| paper_021 | AutoSurrogate: An LLM-Driven Multi-Agent Framework for Autonomous Construction o | Jiale Liu et al. | 2026 | 4 | 3 | 2.5 | **3.40** | LLM multi-agent AutoML builds DL surrogates; Pillar A, subsurface domain. | — |
| paper_023 | GRIMIP: A General Framework for Instance-Specific Configuration of MIP Solvers U | Yidong Luo et al. | 2026 | 4 | 3 | 2.5 | **3.40** | LLM instance-specific solver configuration addressing cold-start; algorithm configuration. | — |
| paper_024 | Can LLMs Configure Software Tools | Jai Kannan et al. | 2023 | 4 | 3 | 2.5 | **3.40** | Early exploratory study of LLMs proposing tool configurations; Pillar A. | — |
| paper_078 | Machine-learning-based diagnostics of EEG pathology | Lukas Gemein et al. | 2020 | 3 | 4 | 3.5 | **3.40** | Large systematic comparison of EEG feature/decoder pipelines; no automated search | — |
| paper_155 | Hyper-parameter Optimization for Wireless Network Traffic Prediction Models with | Liangzhi Wang et al. | 2024 | 4 | 3 | 2.5 | **3.40** | Meta-learned HPO experience reuse for new tasks; Pillar B | — |
| paper_157 | System-Aware Neural ODE Processes for Few-Shot Bayesian Optimization | Jixiang Qing et al. | 2024 | 4 | 3 | 2.5 | **3.40** | Few-shot BO with learned priors; dynamical-systems domain mismatch | — |
| paper_233 | Cross-subject EEG-based emotion recognition through dynamic optimization of rand | Xiaodan Zhang et al. | 2024 | 4 | 3 | 2.5 | **3.40** | Metaheuristic classifier hyperparameter optimization for cross-subject EEG decoding | — |
| paper_277 | EEG-Reptile: An Automatized Reptile-Based Meta-Learning Library for BCIs | Daniil A. Berdyshev et al. | 2024 | 4 | 3 | 2.5 | **3.40** | Automated meta-learning library for cross-subject BCI adaptation; near pillar B-D intersection | — |
| paper_324 | RUNet: A Zero-Calibration Framework for Cross-Domain EEG Decoding via Riemannian | Jing Jin et al. | 2026 | 3.5 | 3.5 | 3 | **3.40** | Zero-calibration MI decoding framework; zero-shot for models not configs | — |
| paper_353 | Initial design strategies and their effects on sequential model-based optimizati | Jakob Bossek et al. | 2020 | 4 | 3 | 2.5 | **3.40** | Initial design strategies for SMBO; initialization-focused case study | — |
| paper_018 | LLM Agent for Hyper-Parameter Optimization | Wanzhe Wang et al. | 2025 | 4 | 2.5 | 3 | **3.35** | LLM agent auto-tunes algorithm hyperparameters; Pillar A, UAV domain. | — |
| paper_027 | Advanced hyperparameter optimization of deep learning models for wind power pred | Shahram Hanifi et al. | 2023 | 3 | 3.5 | 4 | **3.35** | HPO methods compared for wind forecasting; intervention overlap only. | — |
| paper_032 | Bayesian optimization algorithms for accelerator physics | Ryan Roussel et al. | 2024 | 3 | 3.5 | 4 | **3.35** | Domain review of BO algorithms for accelerators; intervention overlap only. | — |
| paper_225 | Sera: Separated Coarse-to-fine Representation Alignment for Cross-subject EEG-ba | Zhihao Jia et al. | 2025 | 3 | 3.5 | 4 | **3.35** | Cross-subject EEG emotion alignment model; no pipeline optimization | — |
| paper_232 | ST-SCGNN: A Spatio-Temporal Self-Constructing Graph Neural Network for Cross-Sub | Jiahui Pan et al. | 2023 | 3 | 3.5 | 4 | **3.35** | Cross-subject EEG graph model; no pipeline/config optimization | — |
| paper_238 | Cross-Subject EEG Channel Selection Method for Lower Limb Brain-Computer Interfa | Mingnan Wei et al. | 2023 | 4 | 2.5 | 3 | **3.35** | Cross-subject channel-selection (config choice) for MI BCI | — |
| paper_125 | Deep learning for EEG-based Motor Imagery classification: Accuracy-cost trade-of | Javier León et al. | 2020 | 4 | 3 | 2 | **3.30** | Pillar D: HPO accuracy-cost trade-off for EEG MI deep learning | — |
| paper_151 | Towards Automated Design of Bayesian Optimization via Exploratory Landscape Anal | Carolin Benjamins et al. | 2022 | 4 | 3 | 2 | **3.30** | Automated BO component selection via landscape analysis; Pillar C adjacent | — |
| paper_153 | Solving Expensive Optimization Problems in Dynamic Environments with Meta-learni | Huan Zhang et al. | 2023 | 4 | 3 | 2 | **3.30** | Meta-learning accelerates expensive BO in dynamic settings; Pillar B | — |
| paper_206 | Incremental Search Space Construction for Machine Learning Pipeline Synthesis | Marc-André Zöller et al. | 2021 | 4 | 3 | 2 | **3.30** | Meta-feature-driven incremental pipeline search-space construction | — |
| paper_214 | Using Known Information to Accelerate HyperParameters Optimization Based on SMBO | Cheng Daning et al. | 2018 | 4.5 | 2.5 | 1.5 | **3.30** | Prior-information injection to accelerate SMBO/SMAC; weakly evaluated | — |
| paper_308 | Transfer learning for EEG-based BCIs: a comparative evaluation and optimization  | Soha Galalaldin Ahmed et al. | 2026 | 3.5 | 3.5 | 2.5 | **3.30** | Comparative optimization of alignment preprocessing for cross-subject BCI | — |
| paper_321 | Cross-subject motor imagery EEG signal classification based on meta-transfer lea | Hongli Li et al. | 2026 | 3.5 | 3.5 | 2.5 | **3.30** | Meta-transfer learning for cross-subject MI; two datasets, model-level | — |
| paper_089 | Human-AI Teaming Using Large Language Models: Boosting Brain-Computer Interfacin | Maryna Kapitonova et al. | 2024 | 4 | 2.5 | 2.5 | **3.25** | LLM agents automating BCI research/analysis; Pillar A/D intersection, position paper | — |
| paper_244 | Cross-subject EEG emotion recognition combined with connectivity features and me | Jinyu Li et al. | 2022 | 3 | 3.5 | 3.5 | **3.25** | Meta-transfer learning across subjects, but model weights not search configs; emotion | — |
| paper_263 | Geometric Deep Learning for Subject Independent Epileptic Seizure Prediction Usi | Theekshana Dissanayake et al. | 2021 | 3 | 3.5 | 3.5 | **3.25** | Subject-independent seizure prediction; subject variability central, no pipeline search | — |
| paper_356 | Inter-subject transfer learning with an end-to-end deep convolutional neural net | Fatemeh Fahimi et al. | 2019 | 3 | 3.5 | 3.5 | **3.25** | Inter-subject CNN transfer for BCI; model weights not configs | — |
| paper_249 | Exploring Convolutional Neural Network Architectures for EEG Feature Extraction | Ildar Rakhmatulin et al. | 2024 | 3 | 3 | 4 | **3.20** | Surveys CNN architecture and preprocessing choices for EEG, but manual not automated | — |
| paper_081 | An investigation of multimodal EMG-EEG fusion strategies for upper-limb gesture  | Michael Pritchard et al. | 2025 | 3 | 3.5 | 3 | **3.15** | Subject-specific vs subject-independent decoding central; fusion, no config search | — |
| paper_108 | Channel reflection: Knowledge-driven data augmentation for EEG-based brain-compu | Ziwei Wang et al. | 2024 | 3 | 3.5 | 3 | **3.15** | Knowledge-driven augmentation addressing subject calibration shortage; no config transfer | — |
| paper_111 | Transfer learning with data alignment and optimal transport for EEG based motor  | Chao Chu et al. | 2024 | 3 | 3.5 | 3 | **3.15** | Cross-subject calibration reduction via alignment/optimal transport; model transfer | — |
| paper_150 | Developmental Bayesian Optimization of Black-Box with Visual Similarity-Based Tr | Maxime Petit et al. | 2018 | 4 | 2.5 | 2 | **3.15** | Similarity-based warm-start of BO from past experience; robotics domain | — |
| paper_199 | Explaining AutoClustering: Uncovering Meta-Feature Contribution in AutoML for Cl | Matheus Camilo da Silva et al. | 2026 | 3.5 | 3 | 2.5 | **3.15** | Meta-feature meta-learning, but clustering AutoML explainability focus | — |
| paper_209 | Revisiting Learning Rate Control | Micha Henheik et al. | 2025 | 3 | 3.5 | 3 | **3.15** | Learning-rate control comparison; HPO-adjacent, not CASH/warm-start | — |
| paper_218 | EEG-TriNet++: A Transformer-Guided Meta-Learning Framework for Robust and Genera | A. Tibermacine et al. | 2026 | 3.5 | 3 | 2.5 | **3.15** | MI EEG cross-subject meta-learning of models, not configs | — |
| paper_234 | Behavior Importance-Aware Graph Neural Architecture Search for Cross-Domain Reco | Chendi Ge et al. | 2025 | 3 | 3.5 | 3 | **3.15** | Graph NAS for cross-domain recommendation; different domain | — |
| paper_267 | Decoding Covert Speech From EEG-A Comprehensive Review | Jerrin Thomas Panachakel et al. | 2021 | 3 | 3.5 | 3 | **3.15** | Review comparing preprocessing/ML choices across imagined-speech pipelines; no automation | — |
| paper_317 | Meta-Learning Enhanced Multi-Source Domain Adaptation for zero-calibration motor | Minmin Miao et al. | 2026 | 3.5 | 3 | 2.5 | **3.15** | Meta-learning zero-calibration MI decoding; model weights not configs | — |
| paper_034 | A tutorial on automatic hyperparameter tuning of deep spectral modelling for reg | Dário Passos et al. | 2022 | 3 | 3 | 3.5 | **3.10** | Hands-on HPO tutorial for chemometrics; intervention overlap, other domain. | — |
| paper_060 | Bayesian Optimization with LLM-Based Acquisition Functions for Natural Language  | David Eric Austin et al. | 2024 | 3 | 3.5 | 2.5 | **3.05** | LLM acquisition functions for BO preference elicitation; recommendation domain. | — |
| paper_314 | Adapting frozen foundation models for montage-agnostic high-resolution EEG event | Jun Ma et al. | 2026 | 3 | 3.5 | 2.5 | **3.05** | Preprocessing-layer adaptation of EEG foundation models; no HPO | — |
| paper_049 | Model soups: averaging weights of multiple fine-tuned models improves accuracy w | Mitchell Wortsman et al. | 2022 | 2 | 4 | 4 | **3.00** | Weight averaging over hyperparameter sweeps; not search methodology. | — |
| paper_077 | EEG Based Emotion Recognition: A Tutorial and Review | Xiang Li et al. | 2022 | 2 | 4 | 4 | **3.00** | EEG emotion recognition review; no pipeline search or MI focus | — |
| paper_092 | From Auto ML to Stacking Ensembles Advancing EEG Cognitive State Prediction Tech | Murad Ali Khan et al. | 2024 | 4 | 2 | 2 | **3.00** | AutoML stacking applied to EEG prediction; weak venue and methods | — |
| paper_109 | Multi-Task Heterogeneous Ensemble Learning-Based Cross-Subject EEG Classificatio | Minji Lee et al. | 2024 | 3 | 3 | 3 | **3.00** | Cross-subject ensemble training for stroke MI; models not configs | — |
| paper_180 | Echo State Networks: Novel reservoir selection and hyperparameter optimization m | César Hernando Valencia Niño et al. | 2023 | 3 | 3 | 3 | **3.00** | HPO/AutoML applied to reservoir computing forecasting; other domain | — |
| paper_181 | Effect of the Sampling of a Dataset in the Hyperparameter Optimization Phase ove | Noemí DeCastro‐García et al. | 2019 | 3 | 3 | 3 | **3.00** | Generic HPO efficiency study via dataset sampling; no warm-start or CASH | — |
| paper_184 | A Benchmark for Data Imputation Methods | Sebastian Jäger et al. | 2021 | 2 | 4 | 4 | **3.00** | Data imputation benchmark, not pipeline search or HPO | — |
| paper_193 | Siamese Meta-Learning and Algorithm Selection with 'Algorithm-Performance Person | Joeran Beel et al. | 2020 | 4 | 2 | 2 | **3.00** | Meta-learning for algorithm selection, but only a proposal | — |
| paper_201 | PS-AAS: Portfolio Selection for Automated Algorithm Selection in Black-Box Optim | Ana Kostovska et al. | 2023 | 3 | 3 | 3 | **3.00** | Portfolio selection for black-box optimizer selection; adjacent to CASH | — |
| paper_223 | mixEEG: Enhancing EEG Federated Learning for Cross-subject EEG Classification wi | Xuan-Hao Liu et al. | 2025 | 3 | 3 | 3 | **3.00** | Federated cross-subject EEG classification; models not configs | — |
| paper_230 | Domain adaptation spatial feature perception neural network for cross-subject EE | Wei Lu et al. | 2024 | 3 | 3 | 3 | **3.00** | Domain-adaptation model for cross-subject EEG emotion; no config search | — |
| paper_231 | Global-regional anti-noise network for cross-subject EEG emotion recognition. | Yilin Wang et al. | 2026 | 3 | 3 | 3 | **3.00** | Denoising-integrated cross-subject EEG emotion model; no pipeline search | — |
| paper_237 | Two-Phase Prototypical Contrastive Domain Generalization for Cross-Subject EEG-B | Honghua Cai et al. | 2023 | 3 | 3 | 3 | **3.00** | Domain generalization for cross-subject EEG emotion; models not configs | — |
| paper_241 | Cross-subject EEG linear domain adaption based on batch normalization and depthw | Guofa Li et al. | 2023 | 3 | 3 | 3 | **3.00** | Cross-subject EEG model adaptation, emotion task; no config/pipeline search | — |
| paper_255 | SeqSleepNet: End-to-End Hierarchical Recurrent Neural Network for Sequence-to-Se | Huy Phan et al. | 2019 | 2 | 4 | 4 | **3.00** | Sleep staging architecture; no HPO, transfer of configs, or CASH | — |
| paper_257 | Deep Learning for Electromyographic Hand Gesture Signal Classification Using Tra | Ulysse Côté‐Allard et al. | 2019 | 2 | 4 | 4 | **3.00** | EMG not EEG; model-weight transfer learning only | — |
| paper_261 | An adversarial discriminative temporal convolutional network for EEG-based cross | Zhipeng He et al. | 2021 | 3 | 3 | 3 | **3.00** | Cross-subject/cross-dataset EEG domain adaptation of models, emotion task | — |
| paper_268 | Multi-Source Transfer Learning for EEG Classification Based on Domain Adversaria | Dezheng Liu et al. | 2022 | 3 | 3 | 3 | **3.00** | Multi-source cross-subject adversarial transfer of models, not configurations | — |

---

## Excluded Papers / 排除論文 (Score < 3.0)

| ID | Title | Year | Rel | Qual | Rec | **Composite** | Exclusion Reason / 排除原因 |
|----|-------|------|-----|-----|-----|---------------|---------------------------|
| paper_013 | Reducing Hyperparameter Tuning Costs in ML, Vision and Language Model  | 2024 | 3 | 3.5 | 2 | **2.95** | Pipeline-aware memoization BO cuts HPO cost; efficiency, not warm-start. |
| paper_029 | Can Large Language Models Transform Computational Social Science? | 2023 | 1.5 | 4 | 5 | **2.95** | LLM application to computational social science, no optimization or pipelines |
| paper_275 | Toward cross-subject and cross-session generalization in EEG-based emo | 2022 | 3 | 3.5 | 2 | **2.95** | Systematic review of cross-subject EEG generalization methods; model-level, emotion |
| paper_284 | Revisiting Euclidean Alignment for Transfer Learning in EEG-Based Brai | 2025 | 3 | 3.5 | 2 | **2.95** | Benchmarks Euclidean alignment preprocessing for cross-subject BCI transfer; data-level not configs |
| paper_291 | Transfer Learning for EEG-Based Brain-Computer Interfaces: A Review of | 2020 | 3 | 3.5 | 2 | **2.95** | Comprehensive review of transfer learning for BCI calibration reduction; model-level |
| paper_015 | DASH: Decoupled Adaptive Surrogate - Acquisition Harness for Automated | 2026 | 3 | 3 | 2.5 | **2.90** | Online surrogate/acquisition selection for BO; tangential to warm-starting. |
| paper_054 | Optimizing Large Language Models for Causality Assessment in Pharmacov | 2026 | 3 | 3 | 2.5 | **2.90** | GP-based tuning of LLM inference temperature; BO intervention, other domain. |
| paper_105 | Partial prior transfer learning based on self-attention CNN for EEG de | 2024 | 3 | 3 | 2.5 | **2.90** | Partial prior transfer of model parameters for stroke MI decoding |
| paper_110 | Three-stage transfer learning for motor imagery EEG recognition. | 2024 | 3 | 3 | 2.5 | **2.90** | Cross-subject three-stage transfer learning for MI models |
| paper_112 | Discrepancy between inter- and intra-subject variability in EEG-based  | 2023 | 3 | 3 | 2.5 | **2.90** | Inter/intra-subject variability analysis motivating subject-specific configuration |
| paper_174 | Review of ML and AutoML Solutions to Forecast Time-Series Data | 2022 | 3 | 2 | 4 | **2.90** | AutoML time-series review; no abstract, title-only score |
| paper_186 | Weighted Random Search for CNN Hyperparameter Optimization | 2020 | 3 | 3 | 2.5 | **2.90** | Random-search HPO variant; no warm-start, CASH, or EEG |
| paper_189 | Toward Autonomous and Efficient Cybersecurity: A Multi-Objective AutoM | 2025 | 3 | 3 | 2.5 | **2.90** | AutoML applied to intrusion detection; different domain |
| paper_190 | Interpretable by Design: MH-AutoML for Transparent and Efficient Andro | 2025 | 3 | 3 | 2.5 | **2.90** | Domain-specific AutoML for malware detection; transparency focus |
| paper_198 | Explainable AutoML (xAutoML) with adaptive modeling for yield enhancem | 2024 | 3 | 3 | 2.5 | **2.90** | Domain xAutoML for semiconductor yield prediction |
| paper_212 | Soil Compaction Parameters Prediction Based on Automated Machine Learn | 2025 | 3 | 3 | 2.5 | **2.90** | Generic AutoML application to soil-compaction engineering |
| paper_221 | Domain Adversarial Neural Network with Reliable Pseudo-labels Iteratio | 2025 | 3 | 2 | 4 | **2.90** | No abstract; title-only, cross-subject EEG domain adaptation |
| paper_240 | CrossNAS: A Cross-Layer Neural Architecture Search Framework for PIM S | 2025 | 3 | 3 | 2.5 | **2.90** | NAS for PIM hardware mapping; NAS intervention, distant domain |
| paper_262 | Conditional Adversarial Domain Adaptation Neural Network for Motor Ima | 2020 | 3 | 3 | 2.5 | **2.90** | MI EEG cross-subject adversarial adaptation; model-level, not config transfer |
| paper_312 | Comprehensive benchmarking and explainable machine learning analysis o | 2026 | 3 | 3 | 2.5 | **2.90** | MI benchmarking with XAI; single dataset, no pipeline search |
| paper_313 | BR-SFDA: A Source-Target Bidirectional Refined SFDA for Privacy Preser | 2026 | 3 | 3 | 2.5 | **2.90** | Cross-subject source-free DA for EEG; model transfer not configs |
| paper_322 | RE-HPBS-IPIC: A Resting EEG- and High-Activation Pain Brain Source-Dri | 2026 | 3 | 3 | 2.5 | **2.90** | Subject-similarity source selection for pain transfer; conceptually adjacent |
| paper_012 | BOAT: A Bayesian Optimization AutoML Time-series Framework for Industr | 2021 | 3 | 3 | 2 | **2.80** | AutoML BO framework applied to industrial time-series; domain mismatch. |
| paper_017 | Accelerating Hyperparameter Optimization of Deep Neural Network via Pr | 2020 | 3 | 3 | 2 | **2.80** | Multi-fidelity successive-halving BO speedup; HPO acceleration without transfer. |
| paper_019 | Scalable Hyperparameter Transfer Learning | 2018 | 3 | 2 | 3.5 | **2.80** | Hyperparameter transfer learning; Pillar B seminal but no abstract. |
| paper_044 | Protein engineering via Bayesian optimization-guided evolutionary algo | 2022 | 2 | 4 | 3 | **2.80** | BO wet-lab application, no HPO warm-start or pipeline search |
| paper_076 | Principle-Evolvable Scientific Discovery via Uncertainty Minimization | 2026 | 3 | 3 | 2 | **2.80** | LLM agent with evolving priors framed as BO; scientific discovery, not HPO |
| paper_126 | EEG classification across sessions and across subjects through transfe | 2020 | 3 | 3 | 2 | **2.80** | Cross-subject/session MI transfer of model parameters, not configs |
| paper_148 | Meta-Learning Initializations for Image Segmentation | 2019 | 3 | 3 | 2 | **2.80** | Meta-learned model initialization plus BO tuning; models not configs |
| paper_156 | Cautious Bayesian Optimization for Efficient and Scalable Policy Searc | 2020 | 3 | 3 | 2 | **2.80** | BO constrained around prior policies; policy search not HPO |
| paper_192 | An Automated Machine Learning (AutoML) Method for Driving Distraction  | 2021 | 3 | 3 | 2 | **2.80** | Domain AutoML application to driving distraction detection |
| paper_195 | Leveraging Benchmarking Data for Informed One-Shot Dynamic Algorithm S | 2021 | 3 | 3 | 2 | **2.80** | Benchmark-informed algorithm selection, but evolutionary black-box not ML pipelines |
| paper_208 | Automated Machine Learning Techniques for Data Streams | 2021 | 3 | 3 | 2 | **2.80** | AutoML for data streams; concept-drift focus differs from question |
| paper_226 | An Effective Deep Neural Network Architecture for Cross-Subject Epilep | 2021 | 3 | 3 | 2 | **2.80** | Cross-subject seizure CNN; subject variability central, no search |
| paper_254 | EEG-based detection of the locus of auditory attention with convolutio | 2021 | 2 | 4 | 3 | **2.80** | EEG application with fixed pipeline, no search or subject-config focus |
| paper_274 | Subject-Adaptive Transfer Learning Using Resting State EEG Signals for | 2024 | 3 | 3 | 2 | **2.80** | Subject-adaptive MI transfer via resting-state EEG; models, not search configs |
| paper_279 | M3D: Manifold-based Domain Adaptation with Dynamic Distribution for No | 2024 | 3 | 3 | 2 | **2.80** | Cross-subject/session manifold domain adaptation, emotion; model-level transfer |
| paper_280 | Inter-subject Deep Transfer Learning for Motor Imagery EEG Decoding | 2021 | 3 | 3 | 2 | **2.80** | Inter-subject deep transfer for MI decoding; model weights not configs |
| paper_281 | Physiological Prior-Driven Label Enhancement for Cross-Subject EEG Emo | 2026 | 3 | 3 | 2 | **2.80** | Physiological prior injection for labels, cross-subject emotion; not BO/HPO priors |
| paper_288 | PR-PL: A Novel Transfer Learning Framework with Prototypical Represent | 2022 | 3 | 3 | 2 | **2.80** | Prototypical cross-subject transfer for emotion; model representations not configs |
| paper_295 | Learning Domain- and Class-Disentangled Prototypes for Domain-Generali | 2025 | 3 | 3 | 2 | **2.80** | Domain-generalized cross-subject emotion prototypes; model-level transfer only |
| paper_296 | iFuzzyTL: Interpretable Fuzzy Transfer Learning for SSVEP BCI System | 2024 | 3 | 3 | 2 | **2.80** | Fuzzy transfer learning reducing SSVEP calibration; cross-subject central, no configs |
| paper_301 | Mutual Information-driven Subject-invariant and Class-relevant Deep Re | 2019 | 3 | 3 | 2 | **2.80** | Cross-subject adversarial DA for BCI; models not configs |
| paper_302 | Enabling Rapid Calibration of BCI Systems that Detect Movement-Related | 2026 | 3 | 3 | 2 | **2.80** | Calibration reduction for subject-specific BCI, model-level; 4-subject pilot |
| paper_309 | Personalized Adaptive Gabor Filtering with Three-Stage Semi-Supervised | 2026 | 3 | 3 | 2 | **2.80** | Subject-personalized adaptive filtering, SSVEP; model-embedded not config search |
| paper_310 | Unified Temporal-Spectral-Spatial Modeling for Robust and Generalizabl | 2026 | 3 | 3 | 2 | **2.80** | MI decoding with subject variability central; no pipeline search |
| paper_315 | Dynamic source domain selection: An adaptive EEG transfer learning fra | 2026 | 3 | 3 | 2 | **2.80** | Source-subject selection for MI transfer; model-level analogue of history reuse |
| paper_319 | Multi-branch Domain Adversarial Neural Network with dynamic weight all | 2026 | 3 | 3 | 2 | **2.80** | Multi-source adversarial EEG classification; subject-shift central, no config search |
| paper_320 | Leveraging Cross-Subject Transfer Learning and Signal Augmentation for | 2026 | 3 | 3 | 2 | **2.80** | Cross-subject transfer+augmentation, niche RGB decoding task |
| paper_323 | Transfer learning for subject-independent motor imagery EEG classifica | 2026 | 3 | 3 | 2 | **2.80** | Subject-independent MI transfer network; models not configs |
| paper_326 | Dual-channel TRCA-net based on cross-subject positive transfer for SSV | 2025 | 3 | 3 | 2 | **2.80** | SSVEP cross-subject transfer with subject selection strategy |
| paper_330 | Cross-domain correlation analysis to improve SSVEP signals recognition | 2025 | 3 | 3 | 2 | **2.80** | Calibration-free SSVEP pipeline with subject selection and alignment |
| paper_352 | Auto-Pytorch: Multi-Fidelity MetaLearning for Efficient and Robust Aut | 2021 | 3 | 2 | 3.5 | **2.80** | Auto-PyTorch multi-fidelity meta-learning; no abstract, title-only cap |
| paper_037 | Fast online deconvolution of calcium imaging data | 2017 | 1.5 | 4 | 4 | **2.75** | Neural signal deconvolution, no pipeline search or EEG decoding |
| paper_043 | MEDITRON-70B: Scaling Medical Pretraining for Large Language Models | 2023 | 1.5 | 4 | 4 | **2.75** | Medical LLM pretraining, no HPO/AutoML/EEG content |
| paper_014 | Scalable Meta-Learning for Bayesian Optimization | 2018 | 3 | 2 | 3 | **2.70** | Meta-learning for BO; Pillar B topic but no abstract. |
| paper_025 | GizaML: A Collaborative Meta-learning Based Framework Using LLM For Au | 2024 | 3 | 2 | 3 | **2.70** | Meta-learning plus LLM AutoML forecasting; no abstract. |
| paper_216 | HNAS-EEG: Hierarchical neural architecture search for robust multi-sou | 2026 | 3 | 2 | 3 | **2.70** | No abstract; title-only cap, NAS for cross-subject EEG |
| paper_250 | DICE-Net: A Novel Convolution-Transformer Architecture for Alzheimer D | 2023 | 2 | 3 | 4 | **2.70** | Disease-detection architecture paper, no HPO, transfer of configs, or AutoML |
| paper_265 | EEG-based measurement system for monitoring student engagement in lear | 2022 | 2 | 3 | 4 | **2.70** | Wearable EEG engagement application, no HPO/AutoML/config transfer |
| paper_271 | Generative adversarial networks in EEG analysis: an overview | 2023 | 2 | 3 | 4 | **2.70** | EEG GAN augmentation review, unrelated to CASH/HPO/warm-starting |
| paper_287 | Deep Transfer Learning for Error Decoding from Non-Invasive EEG | 2017 | 3 | 3 | 1.5 | **2.70** | Inter-subject ConvNet transfer for error decoding; model-level, dated |
| paper_299 | Manifold Embedded Knowledge Transfer for Brain-Computer Interfaces | 2019 | 3 | 3 | 1.5 | **2.70** | Manifold knowledge transfer for cross-subject BCI; model/feature-level, dated preprint |
| paper_342 | ML-Plan: Automated machine learning via hierarchical planning | 2018 | 3 | 2 | 3 | **2.70** | AutoML via hierarchical planning; no abstract, title-only cap |
| paper_344 | Two-Stage Transfer Surrogate Model for Automatic Hyperparameter Optimi | 2016 | 3 | 2 | 3 | **2.70** | Transfer surrogate for HPO; no abstract, title-only cap |
| paper_080 | Empowering <scp>EEG</scp> motor imagery classification with deep trans | 2024 | 3 | 2.5 | 2 | **2.65** | Cross-subject MI transfer learning of models, not configurations |
| paper_083 | Machine learning algorithm for predicting seizure control after tempor | 2024 | 2 | 3.5 | 3 | **2.65** | Epilepsy surgery outcome prediction; no pipeline/HPO relevance |
| paper_085 | Deep learning for biosignal control: insights from basic to real-time  | 2022 | 2 | 3.5 | 3 | **2.65** | General biosignal-control DL review; no HPO or config-transfer content |
| paper_121 | A magnetoencephalography dataset for motor and cognitive imagery-based | 2021 | 2 | 3.5 | 3 | **2.65** | Dataset release paper; no HPO, AutoML, or config optimization |
| paper_127 | Classification of left and right foot kinaesthetic motor imagery using | 2019 | 3 | 2.5 | 2 | **2.65** | Subject-specific FBCSP band selection; light config tuning only |
| paper_207 | Automatic deep learning for trend prediction in time series data | 2020 | 3 | 2.5 | 2 | **2.65** | AutoML (HpBandSter) applied to time-series trend prediction |
| paper_229 | Towards Robust Cross-Subject EEG-fNIRS Classification: A Hybrid Deep L | 2024 | 3 | 2.5 | 2 | **2.65** | Cross-subject EEG-fNIRS model with feature selection; weak venue |
| paper_242 | Cross-Subject Driver Fatigue Detection Based on Attention-Guided Multi | 2024 | 3 | 2.5 | 2 | **2.65** | Cross-subject fatigue EEG CNN; subject variability central but no pipeline optimization |
| paper_276 | EEGReXferNet: A Lightweight Gen-AI Framework for EEG Subspace Reconstr | 2025 | 3 | 2.5 | 2 | **2.65** | Automated EEG artifact reconstruction with cross-subject transfer; no config search |
| paper_278 | Common Spatial Generative Adversarial Networks based EEG Data Augmenta | 2021 | 3 | 2.5 | 2 | **2.65** | GAN augmentation to reduce cross-subject BCI calibration; not config transfer |
| paper_283 | Cross-Subject and Cross-Montage EEG Transfer Learning via Individual T | 2025 | 3 | 2.5 | 2 | **2.65** | Cross-subject/montage Riemannian alignment reducing calibration; not config transfer |
| paper_285 | Using i-vectors for subject-independent cross-session EEG transfer lea | 2024 | 3 | 2.5 | 2 | **2.65** | Cross-session workload transfer with i-vectors; subject variability central, no search |
| paper_286 | Cross-Subject Intracranial EEG Reconstruction from Scalp Recordings Us | 2026 | 3 | 2.5 | 2 | **2.65** | Cross-subject iEEG reconstruction for unseen patients; not decoding pipeline optimization |
| paper_289 | EEG-NeXt: A Modernized ConvNet for The Classification of Cognitive Act | 2022 | 3 | 2.5 | 2 | **2.65** | End-to-end EEG pipeline with alignment and transfer; hand-designed, no search |
| paper_292 | Exploiting Multiple EEG Data Domains with Adversarial Learning | 2022 | 3 | 2.5 | 2 | **2.65** | Adversarial multi-domain EEG generalization; cross-subject central, no config search |
| paper_297 | SDA-DDA Semi-supervised Domain Adaptation with Dynamic Distribution Al | 2025 | 3 | 2.5 | 2 | **2.65** | Semi-supervised domain adaptation for cross-subject emotion; model alignment only |
| paper_298 | Leveraging Transfer Learning and User-Specific Updates for Rapid Train | 2025 | 3 | 2.5 | 2 | **2.65** | Cross-subject pretraining plus rapid user-specific updates; model weights not configs |
| paper_300 | Cross-Subject Deep Transfer Models for Evoked Potentials in Brain-Comp | 2023 | 3 | 2.5 | 2 | **2.65** | Cross-subject deep transfer for evoked potentials; reduces calibration, model-level |
| paper_010 | Dynamic meta-learning acquisition function method for Bayesian optimiz | 2026 | 3 | 2 | 2.5 | **2.60** | Meta-learning acquisition function for BO HPO; no abstract. |
| paper_031 | Hands-On Bayesian Neural Networks—A Tutorial for Deep Learning Users | 2022 | 1.5 | 3.5 | 4 | **2.60** | Bayesian deep learning tutorial, no HPO or EEG relevance |
| paper_051 | Optimizing Battery RUL Prediction of Lithium-Ion Batteries Based on Ha | 2023 | 2 | 3 | 3.5 | **2.60** | HPO applied to battery prognostics, unrelated domain |
| paper_052 | Contrastive Learning for Cold-Start Recommendation | 2021 | 1.5 | 3.5 | 4 | **2.60** | Recommender-system cold-start, unrelated to BO/HPO/EEG |
| paper_053 | <i>SDMtune</i>: An R package to tune and evaluate species distribution | 2020 | 2 | 3 | 3.5 | **2.60** | Species distribution model tuning package, unrelated domain |
| paper_248 | Epileptic Seizure Detection Based on EEG Signals and CNN | 2018 | 2 | 3 | 3.5 | **2.60** | Seizure detection application with fixed pipeline, no optimization or config transfer |
| paper_073 | The Kernel Manifold: A Geometric Approach to Gaussian Process Model Se | 2026 | 2.5 | 3 | 2 | **2.55** | BO over GP kernel space; model selection but no transfer/warm-start |
| paper_099 | Exploring Agentic Multimodal Large Language Models: A Survey for AISci | 2025 | 2.5 | 3 | 2 | **2.55** | Agentic MLLM survey for science; touches LLM-agent automation broadly |
| paper_116 | Computerized Multidomain EEG Classification System: A New Paradigm. | 2022 | 2 | 3.5 | 2.5 | **2.55** | Fixed multidomain classification system; no CASH/HPO or subject-specific config search |
| paper_282 | Ultra Efficient Transfer Learning with Meta Update for Cross Subject E | 2020 | 3 | 2.5 | 1.5 | **2.55** | Meta-update transfer for cross-subject EEG adaptation; model-level, older preprint |
| paper_290 | A Many Objective Optimization Approach for Transfer Learning in EEG Cl | 2019 | 3 | 2.5 | 1.5 | **2.55** | Many-objective optimization for EEG transfer learning; optimizes classifier not pipeline configs |
| paper_332 | Cross-subject emotion recognition with loop adaptive adversarial trans | 2025 | 2 | 3.5 | 2.5 | **2.55** | Cross-subject emotion model transfer; no config/pipeline search |
| paper_004 | SEMBO: Semantic Memory-Based Warm-Starting for Hyperparameter Optimiza | 2026 | 3 | 2 | 2 | **2.50** | LLM semantic-memory warm-start HPO; no abstract, title-only score. |
| paper_016 | Designing an Interpretable and Efficient AutoML Pipeline for Enhanced  | 2025 | 3 | 2 | 2 | **2.50** | Generic AutoML pipeline with LLM features and BO; vague evaluation. |
| paper_036 | All you need to know about model predictive control for buildings | 2020 | 1 | 4 | 4 | **2.50** | Keyword-only: building control review, no HPO/EEG |
| paper_090 | Few-shot meta-learning applied to whole brain activity maps improves s | 2024 | 2 | 3 | 3 | **2.50** | Meta-learning applied to neuropharmacology, not search-history/config transfer |
| paper_102 | Trust and explainability in robotic hand control via adversarial multi | 2025 | 2 | 3 | 3 | **2.50** | Adversarial robustness/trust study of fixed MI classifiers; no pipeline search |
| paper_139 | Groundwater level prediction using machine learning models: A comprehe | 2022 | 1 | 4 | 4 | **2.50** | Hydrology forecasting review; unrelated to pipeline search or EEG |
| paper_142 | A Machine Learning-Oriented Survey on Tiny Machine Learning | 2024 | 1 | 4 | 4 | **2.50** | Edge-device ML survey; unrelated to HPO or EEG |
| paper_185 | A survey on semi-supervised learning | 2019 | 1 | 4 | 4 | **2.50** | Semi-supervised learning survey unrelated to HPO/BO/AutoML or EEG pipelines |
| paper_197 | MA-BBOB: Many-Affine Combinations of BBOB Functions for Evaluating Aut | 2023 | 2 | 3 | 3 | **2.50** | Numerical BBOB benchmark generation, not ML-pipeline CASH/HPO or EEG |
| paper_246 | Methods for interpreting and understanding deep neural networks | 2017 | 1 | 4 | 4 | **2.50** | XAI/interpretability tutorial with no HPO, AutoML, or EEG pipeline content |
| paper_247 | Explaining Deep Neural Networks and Beyond: A Review of Methods and Ap | 2021 | 1 | 4 | 4 | **2.50** | Explainable AI review, keyword-only match to research question |
| paper_256 | Emotion Recognition from Multiband EEG Signals Using CapsNet | 2019 | 2 | 3 | 3 | **2.50** | EEG emotion application with fixed pipeline, no HPO or config transfer |
| paper_266 | Convolutional neural networks for decoding of covert attention focus a | 2019 | 2 | 3 | 3 | **2.50** | Fixed CNN decoding plus interpretability, unrelated to pipeline optimization |
| paper_270 | A Bimodal Deep Learning Architecture for EEG-fNIRS Decoding of Overt a | 2021 | 2 | 3 | 3 | **2.50** | Fixed bimodal decoding architecture, no pipeline search or subject-config focus |
| paper_048 | Bayesian learning for neural networks: an algorithmic survey | 2023 | 1.5 | 3 | 4 | **2.45** | Bayesian neural network survey, no pipeline optimization |
| paper_123 | Identification of Motor and Mental Imagery EEG in Two and Multiclass S | 2020 | 2 | 3.5 | 2 | **2.45** | Hand-crafted feature method; no HPO or config transfer |
| paper_035 | Bayesian Regression Tree Models for Causal Inference: Regularization,  | 2020 | 1 | 4 | 3.5 | **2.40** | Causal inference method, no HPO/pipeline search |
| paper_040 | Cold-start Active Learning through Self-supervised Language Modeling | 2020 | 1.5 | 3.5 | 3 | **2.40** | Cold-start active learning for NLP annotation, not BO/HPO |
| paper_093 | Hybrid CNN transformer framework for EEG-based epileptic seizure detec | 2026 | 2 | 3 | 2.5 | **2.40** | Seizure-detection architecture paper; no pipeline/HPO search |
| paper_107 | Self-supervised motor imagery EEG recognition model based on 1-D MTCNN | 2024 | 2 | 3 | 2.5 | **2.40** | Self-supervised MI recognition model; no pipeline/HPO relevance |
| paper_141 | Federated Learning in a Medical Context: A Systematic Literature Revie | 2021 | 1 | 4 | 3.5 | **2.40** | Federated learning privacy review; not pipeline/config optimization |
| paper_146 | Sparsity in Deep Learning: Pruning and growth for efficient inference\ | 2021 | 1 | 4 | 3.5 | **2.40** | Network pruning survey; unrelated to pipeline optimization |
| paper_224 | Cross-Subject EEG Emotion Recognition Using SSA-EMS Algorithm for Feat | 2025 | 2 | 3 | 2.5 | **2.40** | Fixed SSA-EMS feature pipeline; no pipeline/config optimization or transfer |
| paper_264 | RETRACTED: A 1D CNN for high accuracy classification and transfer lear | 2021 | 3 | 1 | 3 | **2.40** | MI transfer learning CNN but RETRACTED; unreliable evidence |
| paper_269 | LCADNet: a novel light CNN architecture for EEG-based Alzheimer diseas | 2024 | 2 | 2 | 4 | **2.40** | No abstract; title indicates fixed disease-detection architecture, off-topic |
| paper_311 | DuA: Dual Attentive Transformer in Long-Term Continuous EEG Emotion An | 2026 | 2 | 3 | 2.5 | **2.40** | Emotion analysis architecture; no config/pipeline optimization |
| paper_318 | EEG-Based Emotion Recognition Using Multi-Axis Adapter Transformer. | 2026 | 2 | 3 | 2.5 | **2.40** | Emotion recognition architecture; no pipeline/HPO relevance |
| paper_327 | Deep Transfer Learning in Intra-Subject and Inter-Subjects for Intraco | 2026 | 2 | 3 | 2.5 | **2.40** | Intracortical BMI model transfer; not EEG pipelines or config search |
| paper_062 | Neural Nonmyopic Bayesian Optimization in Dynamic Cost Settings | 2026 | 2 | 3 | 2 | **2.30** | Generic BO acquisition methodology; no warm-starting, meta-learning, AutoML, or EEG |
| paper_065 | An Exploratory Study of Bayesian Prompt Optimization for Test-Driven C | 2025 | 2 | 3 | 2 | **2.30** | BO for prompt search in code generation, not LLM-guided pipeline/HPO search |
| paper_072 | TL-GRPO: Turn-Level RL for Reasoning-Guided Iterative Optimization | 2026 | 2 | 3 | 2 | **2.30** | Turn-level GRPO for LLM agents; no HPO/BO/pipeline-search contribution |
| paper_079 | Deep learning in fNIRS: a review | 2022 | 2 | 3 | 2 | **2.30** | Narrative fNIRS DL review; not EEG-MI pipeline search |
| paper_104 | 3D convolutional neural network based on spatial-spectral feature pict | 2024 | 2 | 3 | 2 | **2.30** | Novel fixed architecture; no pipeline/hyperparameter search |
| paper_118 | A deep neural network with subdomain adaptation for motor imagery brai | 2021 | 2.5 | 2.5 | 1.5 | **2.30** | Cross-time subdomain adaptation for MI; nonstationarity central, model transfer |
| paper_120 | Probabilistic learning vector quantization on manifold of symmetric po | 2021 | 2 | 3 | 2 | **2.30** | General SPD-manifold classifier; no HPO/config-transfer content |
| paper_122 | Bilinear neural network with 3-D attention for brain decoding of motor | 2020 | 2 | 3 | 2 | **2.30** | Fixed DL architecture for EEG MI; no pipeline/HPO search |
| paper_124 | Electroencephalogram classification in motor-imagery brain-computer in | 2020 | 2 | 3 | 2 | **2.30** | Fixed NMF classification method; no pipeline optimization |
| paper_128 | A hierarchical architecture for recognising intentionality in mental t | 2019 | 2 | 3 | 2 | **2.30** | Mental-task recognition system; no HPO or AutoML component |
| paper_143 | Decentralized learning works: An empirical comparison of gossip learni | 2020 | 1 | 4 | 3 | **2.30** | Decentralized learning comparison; unrelated to pipeline search |
| paper_304 | Prototypical graph based deep label propagation with semantic augmenta | 2026 | 2 | 3 | 2 | **2.30** | Emotion recognition domain adaptation; no pipeline/config optimization, not MI |
| paper_305 | Cross-subject generalization for EEG emotion recognition: a review of  | 2026 | 2 | 3 | 2 | **2.30** | EEG emotion review; outside MI pipeline optimization scope |
| paper_306 | Dynamic bi-domain discriminator adversarial network for EEG emotion re | 2026 | 2 | 3 | 2 | **2.30** | Emotion DA at model level; no config/pipeline search |
| paper_316 | Depression Detection from Three-Channel Resting-State EEG Using a Hybr | 2026 | 2 | 3 | 2 | **2.30** | Fixed-pipeline depression detection; no search or config transfer |
| paper_325 | Multi-source self-guided domain adaptation framework for EEG-based emo | 2025 | 2 | 3 | 2 | **2.30** | Emotion domain adaptation; no pipeline/config optimization |
| paper_033 | From predictive methods to missing data imputation: an optimization ap | 2017 | 1.5 | 3 | 3 | **2.25** | Imputation method paper, no HPO/AutoML/EEG relevance |
| paper_045 | Optimization for deep learning: theory and algorithms | 2019 | 1.5 | 3 | 3 | **2.25** | DL training optimization theory, not hyperparameter/pipeline search |
| paper_307 | Biologically inspired mechanisms for enhancing robustness in EEG signa | 2026 | 2 | 2.5 | 2.5 | **2.25** | Narrative perspective on robustness; no pipeline search or HPO |
| paper_133 | Tackling Climate Change with Machine Learning | 2022 | 1 | 3 | 4 | **2.20** | Climate-change ML applications; no HPO/EEG relevance |
| paper_047 | Ensemble deep learning: A review | 2022 | 1.5 | 2 | 4 | **2.15** | Ensemble learning review, no HPO/AutoML/EEG focus |
| paper_086 | Brain-Computer Interfaces and AI Segmentation in Neurosurgery: A Syste | 2025 | 1.5 | 3 | 2.5 | **2.15** | AI segmentation in neurosurgery review; no HPO/AutoML/MI-pipeline relevance |
| paper_091 | Outlier Detection in EEG Signals Using Ensemble Classifiers | 2025 | 2 | 2.5 | 2 | **2.15** | EEG outlier/seizure ensemble classifier; no pipeline search or MI |
| paper_100 | Lower-Limb Motor Imagery Recognition Prototype Based on EEG Acquisitio | 2025 | 2 | 2.5 | 2 | **2.15** | Proof-of-concept acquisition prototype; no pipeline/config optimization |
| paper_101 | Home Robot Interaction Based on EEG Motor Imagery and Visual Perceptio | 2025 | 2 | 2.5 | 2 | **2.15** | Robot interaction application; no automated pipeline search |
| paper_103 | EEG Acquisition and Motor Imagery Classification for Robotic Control. | 2024 | 2 | 2.5 | 2 | **2.15** | Robotic vehicle BCI demo; no HPO or config optimization |
| paper_106 | Transforming Motor Imagery Analysis: A Novel EEG Classification Framew | 2024 | 2 | 2.5 | 2 | **2.15** | Fixed feature-extraction method; no automated pipeline search |
| paper_215 | AutoAIViz: Opening the Blackbox of Automated Artificial Intelligence w | 2019 | 2 | 2.5 | 2 | **2.15** | HCI visualization of AutoAI pipelines; no optimization method contribution |
| paper_273 | Multi-Channel Convolutional Neural Networks Architecture Feeding for E | 2018 | 2 | 2.5 | 2 | **2.15** | Fixed CNN application, no systematic hyperparameter or pipeline search |
| paper_293 | DuA: Dual Attentive Transformer in Long-Term Continuous EEG Emotion An | 2024 | 2 | 2.5 | 2 | **2.15** | Continuous emotion architecture paper, unrelated to HPO/warm-starting/config transfer |
| paper_328 | Online Sequential EEG Emotion Recognition with Prototypical Alignment  | 2025 | 2 | 2.5 | 2 | **2.15** | Online emotion recognition; no pipeline/HPO relevance |
| paper_331 | A Novel Brain-Computer Interface Application: Precise Decoding of Urin | 2026 | 1.5 | 3 | 2.5 | **2.15** | Application-specific BCI decoding; no search, transfer, or config relevance |
| paper_095 | fNIRS-based early identification of mild cognitive impairment: a large | 2026 | 1.5 | 3 | 2 | **2.05** | fNIRS clinical screening; no EEG-MI or pipeline-search relevance |
| paper_113 | Machine Learning for Motor Imagery Wrist Dorsiflexion Prediction in Br | 2022 | 2 | 2.5 | 1.5 | **2.05** | Rehabilitation application study; no pipeline/config search |
| paper_115 | Hand Motor Imagery Classification Using Effective Connectivity and Hie | 2022 | 2 | 2.5 | 1.5 | **2.05** | Fixed effective-connectivity pipeline; no automated search |
| paper_117 | The Ensemble Machine Learning-Based Classification of Motor Imagery Ta | 2021 | 2 | 2.5 | 1.5 | **2.05** | Fixed MSPCA-wavelet ensemble pipeline; no pipeline optimization |
| paper_119 | Automatic feature extraction and fusion recognition of motor imagery E | 2021 | 2 | 2.5 | 1.5 | **2.05** | Fixed CNN feature-fusion architecture; no pipeline/HPO relevance |
| paper_294 | EEG-based Classification of Drivers Attention using Convolutional Neur | 2021 | 2 | 2.5 | 1.5 | **2.05** | Fixed-pipeline attention classification study, no pipeline optimization content |
| paper_303 | MAD: A Multimodal and Multi-perspective Affective Dataset with Hierarc | 2026 | 1.5 | 3 | 2 | **2.05** | Emotion dataset release; no pipeline search, HPO, or config transfer |
| paper_140 | Regularization for Deep Learning: A Taxonomy | 2017 | 1 | 3 | 3 | **2.00** | Regularization methods taxonomy; no HPO/AutoML/EEG relevance |
| paper_026 | Bayesian statistics and modelling | 2021 | 1.5 | 2 | 3 | **1.95** | Keyword-only: Bayesian statistics primer, no HPO/AutoML/EEG content |
| paper_097 | Explainable AI for HCI: a systematic review of a decade of studies on  | 2026 | 1 | 3.5 | 2 | **1.95** | XAI tools survey; no HPO/BO/EEG relevance |
| paper_030 | Explainable Artificial Intelligence (XAI): Concepts, taxonomies, oppor | 2019 | 1 | 2 | 4 | **1.90** | Keyword-only: XAI survey, no pipeline optimization |
| paper_137 | Deep learning for water quality | 2024 | 1 | 2 | 4 | **1.90** | Unrelated environmental domain; no HPO/EEG connection |
| paper_329 | Cross-Stimulus Transfer Learning: Enhancing Emotion Recognition from V | 2025 | 1.5 | 2.5 | 2 | **1.90** | Olfactory emotion transfer; unrelated to pipeline/config optimization |
| paper_063 | ConsRoute:Consistency-Aware Adaptive Query Routing for Cloud-Edge-Devi | 2026 | 1 | 3 | 2 | **1.80** | Cloud-edge LLM query routing; no HPO/BO/AutoML/EEG content |
| paper_064 | Addressing Out-of-Distribution Challenges in Image Semantic Communicat | 2024 | 1 | 3 | 2 | **1.80** | Image semantic communication; unrelated to pipeline search or EEG |
| paper_069 | Mechanistic Behavior Editing of Language Models | 2024 | 1 | 3 | 2 | **1.80** | Mechanistic LLM interpretability; unrelated to HPO/BO/EEG |
| paper_075 | Trident: Adaptive Scheduling for Heterogeneous Multimodal Data Pipelin | 2026 | 1 | 3 | 2 | **1.80** | Infrastructure scheduler; no HPO/BO/AutoML/EEG relevance |
| paper_159 | Deep Reinforcement Learning for Day-to-day Dynamic Tolling in Tradable | 2025 | 1 | 3 | 2 | **1.80** | Transportation credit-scheme RL; wholly unrelated |
| paper_145 | Workshop Report on Basic Research Needs for Scientific Machine Learnin | 2019 | 1 | 2 | 3 | **1.70** | Research-needs workshop report; no HPO/EEG methods |
| paper_147 | Deep learning for electronic health records: A comparative review of m | 2020 | 1 | 2 | 3 | **1.70** | Health-records DL architectures; not HPO/EEG pipeline search |
| paper_098 | Explainable AI for HCI : A Systematic Review of a Decade of Studies on | 2026 | 1 | 3 | 1 | **1.60** | Duplicate repository copy of XAI/HCI survey; keyword-only match |
| paper_272 | Driver Emotions Recognition Based on Improved Faster R-CNN and Neural  | 2022 | 1 | 2 | 2.5 | **1.60** | Image-based emotion recognition, no EEG and no actual NAS/HPO method |
| paper_094 | Exploring The people Cases of Epileptic Patients and Healthy by the CN | 2025 | 1.5 | 2 | 1 | **1.55** | Basic CNN epilepsy classification; no relevance to pipeline optimization |
| paper_067 | Trading Devil RL: Backdoor attack via Stock market, Bayesian Optimizat | 2024 | 1 | 2 | 2 | **1.50** | Security/backdoor attack study; unrelated to HPO, AutoML, or EEG |
| paper_160 | Convolutional neural network for Lyman break galaxies classification a | 2024 | 1 | 2 | 2 | **1.50** | Astronomy application; no HPO/EEG relevance |
| paper_096 | Predictive AI–Integrated Biosensing Model for Rapid Detection of Subst | 2025 | 1 | 2 | 1 | **1.30** | AI biosensor for substance abuse; unrelated field |

---

## Hub Paper Summary / 核心引用論文摘要

| ID | Title | In-Degree | Cluster | Status | Note |
|----|-------|-----------|---------|--------|------|
| paper_162 | Auto-WEKA: Automatic Model Selection and Hyperparameter Optimization i | 10 | automl_systems_cash | included | included |
| paper_163 | Hyperband: A Novel Bandit-Based Approach to Hyperparameter Optimizatio | 5 | automl_systems_cash | included | included |
| paper_130 | Automated Machine Learning | 5 | automl_systems_cash | included | included |
| paper_161 | Hyperopt: a Python library for model selection and hyperparameter opti | 5 | automl_systems_cash | included | included |
| paper_165 | Initializing Bayesian Hyperparameter Optimization via Meta-Learning | 6 | hpo_warmstart_transfer | included | included |
| paper_166 | TPOT: A Tree-Based Pipeline Optimization Tool for Automating Machine L | 3 | automl_systems_cash | included | included |
| paper_175 | Evaluation of a Tree-based Pipeline Optimization Tool for Automating D | 11 | hpo_warmstart_transfer | included | included |
| paper_136 | Regularized Evolution for Image Classifier Architecture Search | 4 | None | included | included |
| paper_171 | Auto-Keras: An Efficient Neural Architecture Search System | 7 | automl_systems_cash | included | included |
| paper_253 | Evaluation of Hyperparameter Optimization in Machine and Deep Learning | 4 | eeg_pipeline_optimization | included | included |
| paper_245 | Deep learning for electroencephalogram (EEG) classification tasks: a r | 7 | eeg_pipeline_optimization | included | included |
| paper_246 | Methods for interpreting and understanding deep neural networks | 3 | None | excluded | XAI/interpretability tutorial with no HPO, AutoML, or EEG pipeline content |
| paper_030 | Explainable Artificial Intelligence (XAI): Concepts, taxonomies, oppor | 5 | None | excluded | Keyword-only: XAI survey, no pipeline optimization |
---

> **⛳ Checkpoint 2: 邊緣打撈**
> Review the borderline papers above (score 3.0–3.4). These scored close to the threshold and may contain relevant work the scoring missed — especially cross-disciplinary papers using non-standard terminology.
> 請審核上方邊緣論文（3.0–3.4 分）。分數接近門檻，可能包含評分遺漏的相關研究——特別是使用非標準術語的跨領域論文。
>
> Mark any paper to include with `include {paper_id}` / 以 `include {paper_id}` 標記要納入的論文。

Files / 檔案: `step3_screening_results.md`, `step3_shortlist.json`
Next step / 下一步: `/research-export`
