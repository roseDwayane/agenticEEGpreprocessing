---
session_id: "20260620"
topic: "Agentic EEG preprocessing pipeline with FOOOF-based signal quality optimization"
date: "2026-06-20"
---

# Search Queries / 搜尋策略

> Topic / 研究主題: Agentic EEG preprocessing pipeline with FOOOF-based signal quality optimization
> Generated / 產生日期: 2026-06-20

## PICO Framework

| Component | English | 繁體中文 |
|-----------|---------|---------|
| **P** Population | EEG recordings from public datasets (resting-state and task-based); study objects are signals & pipeline configurations, not patients | 公開資料集的 EEG 訊號（靜息態與任務態）；研究對象是訊號與管線組態，而非病患 |
| **I** Intervention | LLM-agent-driven adaptive selection of preprocessing parameters (filtering, ICA, artifact rejection, re-referencing) guided by intermediate signal features | LLM 智能體驅動、依中間訊號特徵自適應挑選預處理參數（濾波、ICA、artifact 移除、重參考） |
| **C** Comparison | Fixed/standard pipelines (PREP, HAPPE, Autoreject), exhaustive greedy search as ground-truth oracle, random/default selection | 固定/標準管線（PREP、HAPPE、Autoreject）、窮舉貪婪搜尋（ground-truth oracle）、隨機/預設參數 |
| **O** Outcome | Signal quality via FOOOF aperiodic(1/f)-periodic decomposition as SNR proxy; agreement with greedy-search ground truth; compute cost | 以 FOOOF 非週期(1/f)–週期分量作為 SNR 代理的訊號品質；與貪婪搜尋 ground truth 的一致性；計算成本 |
| Setting | Offline batch preprocessing, reproducible research pipelines | 離線批次預處理、可重現研究管線 |
| Timeframe | 2019–2026 | 2019–2026 |

## Queries

### Q1: Core Terms + Automated Preprocessing
**Query:** `automated EEG preprocessing pipeline artifact removal parameter optimization`
**Rationale / 策略說明:** Direct hit on automated EEG preprocessing and parameter optimization literature (PREP, HAPPE, Autoreject) / 直接命中自動化 EEG 預處理與參數最佳化的核心文獻

### Q2: Synonyms + Adjacent Terms
**Query:** `("EEG preprocessing" OR "EEG cleaning" OR "artifact correction") AND (reproducib* OR standardiz* OR "quality control")`
**Rationale / 策略說明:** Catches papers using alternative terminology (cleaning, QC, standardization) for the same problem / 抓取用不同術語描述同一問題的論文

### Q3: Mechanism + Quality Metric
**Query:** `FOOOF aperiodic periodic spectral parameterization 1/f EEG signal quality SNR`
**Rationale / 策略說明:** FOOOF/specparam separation of periodic & aperiodic components and its use as a quality/SNR metric / FOOOF 分離週期與非週期成分及其作為品質指標的理論基礎

### Q4: Methodology + Search/Learning
**Query:** `(AutoML OR "reinforcement learning" OR "Bayesian optimization") AND (EEG OR "signal processing") pipeline hyperparameter selection`
**Rationale / 策略說明:** Methods that frame preprocessing as a searchable/learnable problem / 把預處理當成可搜尋或可學習問題的方法論文

### Q5: Cross-disciplinary + LLM Agents
**Query:** `("LLM agent" OR "large language model" OR agentic) AND ("scientific workflow" OR "data preprocessing" OR "tool use") automation`
**Rationale / 策略說明:** LLM agents automating scientific workflows / data pipelines, supporting the Step-3 agent design / LLM 智能體做科學工作流自動化，支撐第 3 步 agent 設計
