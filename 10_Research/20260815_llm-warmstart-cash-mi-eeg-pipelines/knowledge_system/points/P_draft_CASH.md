---
schema_version: "1.0"
id: P_draft_CASH
type: point
name: "Combined Algorithm Selection and Hyperparameter Optimization (CASH)"
description: "CASH formalizes algorithm selection and hyperparameter tuning as a single hierarchical (bilevel/black-box) optimization problem solvable by Bayesian optimization."
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [cash, automl, optimization]
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
source: "KotthoffEtAl2019"
year: 2019
claim_type: conceptual
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-3"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# Combined Algorithm Selection and Hyperparameter Optimization (CASH)

> **核心主張**：把演算法選擇與超參數調校形式化為單一階層式最佳化問題，使整條 ML pipeline 的組態搜尋可以被視為一個 black-box 最佳化問題並交由 Bayesian optimization 求解。

## 來源
- 作者：Kotthoff, L., Thornton, C., Hoos, H. H., Hutter, F., & Leyton-Brown, K. / 年份：2019 / 出處：*The Springer Series on Challenges in Machine Learning*（Auto-WEKA 章節；CASH 概念源自 Thornton et al. 2013 的 Auto-WEKA） / citation key: `KotthoffEtAl2019`

## 目的
回答「面對一個新資料集，該用哪個演算法、又該如何設定它的超參數」這個雙重問題——傳統上兩者被分開處理，導致資源浪費與次佳解。

## 核心主張（展開）
CASH 將演算法選擇（algorithm selection）與超參數最佳化（hyperparameter optimization, HPO）合併為單一問題：在演算法集合 A 與各自的超參數空間 Λᵢ 上，聯合搜尋 (A*, λ*) 使驗證損失最小。關鍵形式化技巧是把「選哪個演算法」當作一個 top-level 的 categorical 超參數，其取值決定哪一個子空間 Λᵢ 被啟用，形成 conditional hierarchy 結構。如此一來，整個問題成為一個可由 Bayesian optimization 求解的 bilevel/black-box 最佳化問題。自 Auto-WEKA 以來，這已成為 pipeline 最佳化的典範形式化（canonical formalization），被 auto-sklearn、Hyperopt-sklearn、SMAC3 等系統採納。

## 方法
Auto-WEKA 一系的做法是把所有候選演算法的超參數空間合併為單一巨大的 conditional 搜尋空間，再以 SMAC 式的 random forest surrogate BO 進行聯合搜尋——random forest 天然能處理條件層級與 categorical 變數。給定初始評估後，BO 迭代地擬合 surrogate model、以 acquisition function（如 EI）平衡探索與利用來挑選下一個要評估的 (演算法, 超參數) 組態。此「joint space」路線後來也成為分解式方法（decomposition）批評與改良的對象。

## 發現
- step6 綜述未報告 Auto-WEKA 原始論文的具體效能數字 (待補)。
- 後續證據顯示 joint CASH 形式化在空間增大時退化：當候選演算法從 1 增至 16，SMAC 在 PC4 上的準確率由 95.02% 降至 93.63%（LiEtAl2020a），這成為分解式 CASH 方法的直接動機。
- Zöller & Huber（2019）顯示 CASH solver 在 48% 的共同資料集上以約五分之一時間勝過完整 AutoML 框架。

## 啟發
- **被啟發**：(無上游 Point 卡；概念源自 model selection 與 HPO 兩傳統的合流)
- **啟發了**：[[P_draft_SMAC]] — CASH 的 conditional hierarchy 結構直接推動 random forest surrogate BO 成為主流求解引擎
- **啟發了**：[[P_draft_Search_Space_Decomposition]] — joint CASH 空間的可擴展性瓶頸催生了樹狀分解式搜尋
