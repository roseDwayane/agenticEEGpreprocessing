---
schema_version: "1.0"
id: P_draft_LLAMBO
type: point
name: "LLAMBO (Large Language Models to Enhance Bayesian Optimization)"
description: "LLAMBO plugs an LLM into every stage of BO as zero-shot warm-starter, ICL surrogate, and conditional candidate sampler, with warm-starting emerging as the most robust gain."
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [llm, bayesian-optimization, warm-start]
domain: [AI]
field: [AutoML]
status: draft
created: 2026-08-15
updated: 2026-08-15
parent:
parts: []
depends_on: []
caused_by: []
causes: []
related_lines: []
related_planes: []
related_body: [B_draft_llm-warmstart-cash-mi-eeg-pipelines]
source: "LiuEtAl2024"
year: 2024
claim_type: methodological
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-1"
    - "step5_full_text/LiuEtAl2024.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# LLAMBO (Large Language Models to Enhance Bayesian Optimization)

> **核心主張**：把 BO 問題以自然語言表述後，LLM 可作為 zero-shot warm-starter、in-context surrogate 與 conditional candidate sampler 插入 BO 各環節——其中 warm-start 是最穩健、集中於搜尋早期的增益來源。

## 來源
- 作者：Liu, T., Astorga, N., Seedat, N., & van der Schaar, M. / 年份：2024 / 出處：*ICLR 2024*（arXiv:2402.03921） / citation key: `LiuEtAl2024`

## 目的
回答「LLM 的 encoded priors、few-shot ICL 與情境理解能力，能否強化 BO 在觀測稀疏時最弱的環節（surrogate 品質、候選取樣、先驗注入）」的問題。

## 核心主張（展開）
LLAMBO 將 BO 三個組件翻譯為結構化 prompt（含 Data Card 資料集詮釋資料與 Model Card 搜尋空間描述）：(1) zero-shot warm-starting——在沒有任何觀測前，僅憑問題描述提出初始組態，取代 random/Sobol/LHD 初始設計；(2) candidate sampling——受 TPE 啟發，以 ICL 依目標值 s′ 條件化取樣 h̃ ∼ p(h|s′; Dₙ)；(3) surrogate modeling——判別式（迴歸 + 不確定性）與生成式（二元分類評分）兩種 ICL 代理。全程 in-context、無需微調、模組化可單獨嵌入既有 BO 框架。系統性消融顯示增益「前置」（front-loaded）：warm-start 效益在 trial < 5 最顯著；而 LLM 判別式 surrogate 作為純單任務迴歸器弱於 SMAC、校準弱於 GP，其優勢來自跨任務語意先驗而非迴歸精度。

## 方法
以 GPT-3.5 為骨幹，於 Bayesmark + HPOBench 共 74 個超參數調校任務評估。warm-start 實驗比較 no/partial/full context 三種語境設定與 Random/Sobol/LHD 空間填充設計，25 trials、10 個種子。端到端實驗：5 個 Bayesmark 資料集 × 5 個模型類別（RF、AdaBoost、SVM、Logistic Regression、NN），另加 3 個 LLM 預訓練未見過的私有資料集與 2 個合成資料集；每任務 5 個種子 × 25 trials；基線為 GP-DKL、SKOpt (GP)、Optuna (TPE)、SMAC3 (RF)，且為公平起見基線不用 warm-start。

## 發現
- 端到端：在 25 個公開 + 5 個私有/合成 Bayesmark 任務上（5 seeds, 25 trials）取得最佳平均 regret，勝過 GP-DKL、SKOpt、Optuna-TPE、SMAC3（step6 Key Results）。
- warm-start：full/partial context 顯著降低早期 regret 與跨執行變異，增益在 trial < 5 前最顯著；此後初始增益遞減。
- surrogate：GP 校準最佳（coverage ≈ 0.68 目標）；LLAMBO 在 n=5 等低觀測區間展現較佳的取得點品質，n > 5 後 regret 較低（較佳 exploitation）。
- candidate sampling：在候選點的 average/best regret 與多樣性上優於 independent/multivariate TPE 與 random。

## 啟發
- **被啟發**：[[P_draft_PiBO]] — 承接「把使用者/外部先驗注入 BO」的思想，但把先驗來源從人工信念分布換成 LLM 的 encoded priors
- **啟發了**：[[P_draft_Backbone_Capacity_Threshold]] — 其重現研究揭示效果對 LLM 容量的依賴
- **啟發了**：[[P_draft_CoFEH]] — 2026 年的協作式 CASH 系統改為「閘控/條件化 LLM 而非信任 LLM」，直接回應 LLAMBO 的定位
