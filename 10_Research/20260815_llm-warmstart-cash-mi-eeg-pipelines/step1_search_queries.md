---
session_id: "20260815"
topic: "LLM-informed warm-starting and prior injection for Bayesian optimization (CASH) of subject-specific EEG motor-imagery preprocessing pipelines, with cross-subject transfer"
date: "2026-08-15"
---

# Search Queries / 搜尋策略

> Topic / 研究主題: LLM-informed warm-starting and prior injection for Bayesian optimization (CASH) of subject-specific EEG motor-imagery preprocessing pipelines, with cross-subject transfer
> 原始主題（使用者輸入）: 在受試者特定的 MI-BCI 前處理管線 CASH 最佳化中，讓 LLM 扮演 prior injector / cold-start initializer（或結合其他受試者的歷史搜尋結果做遷移），相較於純 cold-start 的 SMAC/VolcanoML 搜尋，能否在相同評估預算內顯著加速收斂並提升最終解碼準確率？零樣本直接遷移（zero-shot transfer）又能達到多少水準？
> Generated / 產生日期: 2026-08-15

## PICO Framework

| Component | English | 繁體中文 |
|-----------|---------|---------|
| **P** Population | Subject-specific EEG motor-imagery (MI) BCI decoding tasks on public benchmarks (PhysioNet EEGMMI, BCI Competition IV-2a/2b), with the full preprocessing + feature extraction + classification pipeline framed as a CASH search space | 以公開基準資料集（PhysioNet EEGMMI、BCI Competition IV-2a/2b）上的受試者特定 EEG 運動想像（MI）解碼任務為對象，將「前處理＋特徵萃取＋分類」整條管線形式化為 CASH（演算法選擇＋超參數最佳化）搜尋空間 |
| **I** Intervention | LLM as prior injector / cold-start initializer for Bayesian optimization (SMAC / OpenBox / VolcanoML-style decomposed search); meta-learned warm-starting that transfers other subjects' search histories | 讓 LLM（大型語言模型）扮演先驗注入者／冷啟動初始化者，為貝葉斯最佳化（SMAC / OpenBox / VolcanoML 式分解搜尋）提供暖啟動；並以其他受試者的歷史搜尋結果做 meta-learning 遷移 |
| **C** Comparison | Pure cold-start BO (SMAC, auto-sklearn, VolcanoML), random search, fixed manually-designed "standard" pipelines; zero-shot transferred configurations without further search | 純冷啟動貝葉斯最佳化（SMAC、auto-sklearn、VolcanoML）、隨機搜尋、人工固定的「標準」前處理管線；以及不再搜尋、直接套用的零樣本遷移（zero-shot transfer）配置 |
| **O** Outcome | Convergence speed under a fixed evaluation budget (anytime performance, evaluations-to-target), final decoding performance (balanced accuracy, kappa), zero-shot transfer accuracy | 固定評估預算下的收斂速度（anytime performance、達標所需評估次數）、最終解碼表現（balanced accuracy、kappa）、零樣本遷移的準確率水準 |
| Setting | Computational/offline benchmark experiments; AutoML / HPO systems research | 計算型／離線基準實驗；AutoML／超參數最佳化（HPO）系統研究情境 |
| Timeframe | 2015–2026 (meta-learning warm-start BO from ~2015; LLM-for-AutoML concentrated in 2023–2026) | 2015–2026（meta-learning 暖啟動 BO 約自 2015 起；LLM×AutoML 集中於 2023–2026） |

## Queries

### Q1: Core Terms + Population
**Query:** `"large language model" AND ("Bayesian optimization" OR "hyperparameter optimization" OR AutoML) AND (warm-start OR "cold start" OR prior OR initialization)`
**Rationale / 策略說明:** Direct hit on the LLM-as-prior-injector/initializer literature for BO/HPO/AutoML (LLAMBO, BORA, LB-MCTS lineage). / 直接命中「LLM 作為 BO/HPO/AutoML 先驗注入者／初始化者」的文獻脈絡（LLAMBO、BORA、LB-MCTS 一系）。

### Q2: Synonyms + Alternative Terminology
**Query:** `(EEG OR "brain-computer interface" OR BCI) AND ("motor imagery") AND ("pipeline optimization" OR AutoML OR "combined algorithm selection" OR CASH OR "automated preprocessing")`
**Rationale / 策略說明:** Catches EEG/BCI-side work that automates preprocessing or full-pipeline selection, with or without LLMs (NeuroWeaver, EEG AutoML systems). / 撈出 EEG/BCI 端自動化前處理或整條管線選擇的研究，不論是否用到 LLM（NeuroWeaver、EEG AutoML 系統）。

### Q3: Mechanism + Theoretical Basis
**Query:** `("meta-learning" OR "transfer learning") AND ("Bayesian optimization" OR "hyperparameter optimization") AND (warm-start OR "warm starting" OR "search history" OR "transfer of surrogates")`
**Rationale / 策略說明:** Finds the mechanistic foundation — how prior task/subject search histories are transferred into BO surrogates and initial designs. / 找出機制基礎——歷史任務／受試者搜尋紀錄如何被遷移進 BO 的代理模型與初始設計。

### Q4: Methodology + Study Design
**Query:** `(AutoML OR "hyperparameter optimization") AND (benchmark OR "anytime performance" OR "evaluation budget" OR ablation) AND ("search space decomposition" OR SMAC OR VolcanoML OR auto-sklearn)`
**Rationale / 策略說明:** Targets rigorous system evaluations and budget-controlled comparisons that define how to measure convergence speedup fairly. / 鎖定嚴謹的系統評估與預算受控比較，界定如何公平量測收斂加速。

### Q5: Cross-Disciplinary
**Query:** `("cross-subject" OR "inter-subject" OR "subject transfer" OR "subject-to-subject") AND (EEG OR BCI) AND (hyperparameter OR configuration OR pipeline OR "neural architecture search")`
**Rationale / 策略說明:** Broadens to cross-subject variability/transfer work in EEG — including NAS-for-EEG and subject-adaptive decoding — whose transfer methods may apply to pipeline configuration. / 擴展到 EEG 跨受試者變異與遷移研究——含 NAS-for-EEG 與受試者自適應解碼——其遷移方法可能可套用到管線配置。

---

> **Checkpoint 1: 初始定向核准**
> Please review the PICO framework and search queries above.
> - Are the PICO components accurate? / PICO 各元素是否正確？
> - Any missing keywords or synonyms? / 有遺漏的關鍵字或同義詞嗎？
> - Any off-target dimensions to remove? / 有需要移除的偏離維度嗎？
>
> When ready, greenlight to proceed to `/research-search`.
