---
schema_version: "1.0"
id: P_draft_Search_Space_Decomposition
type: point
name: "Search Space Decomposition (VolcanoML)"
description: "VolcanoML decomposes the large end-to-end CASH space into a tree-structured execution plan of sub-space building blocks, with an advantage that grows as the space gets larger."
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [automl, cash, decomposition]
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
source: "LiEtAl2021b"
year: 2021
claim_type: methodological
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-3"
    - "step5_full_text/LiEtAl2021b.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# Search Space Decomposition (VolcanoML)

> **核心主張**：以樹狀 execution plan 把大型端到端 CASH 空間拆解為多個子空間 building blocks，比 joint BO 更能擴展，且此優勢隨搜尋空間增大而擴大。

## 來源
- 作者：Li, Y., Shen, Y., Zhang, W., Jiang, J., Ding, B., Li, Y., Zhou, J., Yang, Z., Wu, W., Zhang, C., & Cui, B. / 年份：2021 / 出處：*Proceedings of the VLDB Endowment*, 14(11)（arXiv:2107.08861；程式碼即 PKU-DAIR/mindware） / citation key: `LiEtAl2021b`

## 目的
回答「當端到端 AutoML 搜尋空間（feature engineering × 演算法選擇 × 超參數）大到 joint BO 無法有效建模時，該如何組織搜尋」的可擴展性問題。

## 核心主張（展開）
既有系統（auto-sklearn、TPOT）將整個複合空間交給單一最佳化器聯合處理，天然形成可擴展性瓶頸。VolcanoML 提出一個結構化抽象：三種 building block——joint block（子空間直接以 BO 求解）、conditioning block（依某 categorical 變數的取值切分為多個子塊，以 multi-armed bandit 分配資源並淘汰劣勢子塊）、alternating block（切成兩個近似獨立的子空間交替最佳化，以 expected utility improvement 決定下一步拉哪個臂）。一個 execution plan 是這些 blocks 組成的樹，根節點對應原始空間；執行模型仿照關聯式資料庫的 Volcano 式查詢求值——do_next! 由根遞迴傳遞到葉。同一空間可有多種分解方式，而作者由 AutoML 空間的性質（最佳特徵隨演算法而異、FE 與 HPO 的改進近似互補）選定「先 conditioning on 演算法、再 alternating FE/HP」的計畫。自動 plan generation 被明確列為開放問題。

## 方法
每個 building block 提供 init / do_next! / get_current_best / get_eu / get_eui 介面；conditioning block 以 round-robin 各拉 L 次（實作取 L=5）後，用 expected utility 上下界 [l, u] 淘汰被支配的子塊；alternating block 以歷史改進均值估計 EUI 決定資源去向。joint block 以 SMAC 式 BO（EI acquisition）求解，並可換用 MFES-HB 等 early-stopping 方法。在 60 個 OpenML 資料集上與 auto-sklearn、TPOT 於相同空間比較，另測試加入 TensorFlow Hub embedding selection 的擴充空間。

## 發現
- 在與 auto-sklearn 相同的搜尋空間下，VolcanoML 在 25/30 分類任務與 17/20 迴歸任務上勝過 auto-sklearn。
- 空間增大時優勢擴大：在 100 個超參數的空間上平均排名 1.65，對 auto-sklearn 的 3.57。
- Higgs 資料集：4 小時達到 27.2% 測試誤差，對比基線的 24 小時（step6 Key Results）。
- VolcanoML 的後續版本（LiEtAl2022a）內建 RGPE/RankNet 式 meta-learned transfer，顯示分解式系統作者亦把 warm-starting 視為下一個加速槓桿。

## 啟發
- **被啟發**：[[P_draft_CASH]] — 針對 joint CASH 形式化的可擴展性瓶頸而生
- **被啟發**：[[P_draft_SMAC]] — joint block 內部仍以 SMAC 式 BO 為子空間求解器
- **啟發了**：本研究的 F1 引擎選擇 — 分解式 CASH（VolcanoML/Rising Bandits 一系）被選為 EEG-MI pipeline 最佳化的搜尋骨幹，LLM/meta-learned 先驗則作用於其子空間暖啟動
