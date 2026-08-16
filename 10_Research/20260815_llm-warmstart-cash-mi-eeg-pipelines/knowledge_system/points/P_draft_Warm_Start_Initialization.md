---
schema_version: "1.0"
id: P_draft_Warm_Start_Initialization
type: point
name: "Warm-Start Initialization"
description: "Warm-start 是以外部知識設定 BO 起點的傘狀機制族，可沿 initial points、search space、surrogate、acquisition function 四個正交管道注入，且四管道幾乎無人結合。"
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [warm-start, transfer-learning, bayesian-optimization, taxonomy]
domain: [AI]
field: [AutoML]
status: draft
created: 2026-08-15
updated: 2026-08-15
parent:
parts: [P_draft_MI_SMAC, P_draft_PiBO, P_draft_TransBO, P_draft_Two_Stage_Transfer_Surrogate, P_draft_Zero_Shot_Configuration_Transfer, P_draft_Similarity_Gating, P_draft_Meta_Features]
depends_on: []
caused_by: []
causes: []
related_lines: []
related_planes: []
related_body: [B_draft_llm-warmstart-cash-mi-eeg-pipelines]
source: "BaiEtAl2023"
year: 2023
claim_type: conceptual
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-2"
    - "step5_full_text/BaiEtAl2023.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# Warm-Start Initialization

> **核心主張**：Warm-start 不是單一技巧，而是「用外部知識設定 Bayesian optimization 起點」的傘狀機制族，可沿 initial points、search space、surrogate model、acquisition function 四個正交管道注入，而幾乎沒有工作同時結合多個管道。

## 來源
- 作者：Bai, T., Li, Y., Shen, Y., Zhang, X., Zhang, W., & Cui, B. / 年份：2023 / 出處：arXiv preprint（Transfer learning for Bayesian optimization: A survey）/ citation key: `BaiEtAl2023`

## 目的
系統性回答「遷移學習應注入 BO 的哪個元件」：將分散的 transfer-learning-for-BO 文獻依 "what to transfer"（BO 元件）與 "how to transfer"（技術）雙軸統整，為冷啟動慢收斂（slow convergence）問題提供機制地圖。

## 核心主張（展開）
Vanilla BO 在最初數十次評估因觀測稀少而 surrogate 不準，導致慢收斂；來源任務（source tasks）的知識可以縮短這段冷啟動期。BO 有四個可客製的元件——initial points、search space、surrogate model、acquisition function——每一個都是一條正交的知識注入管道：初始點設計（meta-features 選點、gradient-based 學習、演化式生成）、搜尋空間設計（空間剪枝、promising region 設計）、surrogate 遷移（GP kernel/prior/data-scale/ensemble 設計、Bayesian neural network、neural process）、與 acquisition 遷移（multi-task acquisition、ensemble-GP acquisition、RL-based）。四管道彼此正交、原則上可組合，但現有工作幾乎都只佔用單一管道，「comprehensive TLBO framework」被明列為開放問題（survey Sec. 9.2）。

## 方法
文獻綜述：以 BO 的四個元件為第一層分類、具體技術為第二層分類建立 taxonomy（survey Table 1）；提出一般化的 TLBO 框架圖描述 initial points generator、evaluator、surrogate、acquisition、search space 五個角色的資訊流；盤點應用場景（HPO、A/B testing、DBMS knob tuning、Spark 調參、EDA 設計）；最後列出未來方向：統一評測基準、綜合框架、廣義可遷移資訊（如 low-fidelity 結果）、trainable parameters + hyperparameters 的聯合遷移。

## 發現
- 本卡來源為 survey，無單一 headline 數字；結構性發現有二：(1) 四遷移管道正交、可組合，但幾乎無工作組合之，「comprehensive framework」為明列的開放問題（Sec. 9.2）；(2) 各方法在不同實驗環境下自行比較，缺乏公平比較的統一基準（Sec. 9.1）。
- 跨論文綜整證據（step6 Theme 2 takeaway）：只要有 decay 或相似度自適應防護抑制負遷移，warm-starting 可將其他任務的調參歷史轉化為 CASH 規模空間上 BO 的 2–12 倍早期預算加速。
- 各管道的代表性單點證據見 parts 各卡（MI-SMAC、πBO、TransBO、TST 等）。

## 啟發
- **被啟發**：[[P_draft_MI_SMAC]] — 初始點遷移是最早成熟的管道，survey 的 Initialization Design 分類即以 meta-features 選點（MI-SMBO 一系）為原型；surrogate 層的 [[P_draft_Two_Stage_Transfer_Surrogate]] 與 [[P_draft_TransBO]]、先驗注入的 [[P_draft_PiBO]] 各自填入其餘管道。
- **啟發了**：本研究 F2 知識注入模組 — 本研究同時佔用「初始設計遷移」（跨受試者搜尋歷史）與「acquisition 先驗注入」（LLM 引出的 πBO 先驗）兩個管道並加上相似度門控，直接回應 survey 的 comprehensive-framework 開放問題。
