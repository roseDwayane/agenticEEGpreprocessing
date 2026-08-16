---
schema_version: "1.0"
id: P_Two_Stage_Transfer_Surrogate
type: point
name: "Two-Stage Transfer Surrogate (TST)"
description: "早期 surrogate 層遷移法：先為各任務獨立訓練 GP，再以任務相似度（meta-features 或 pairwise ranking）的 Epanechnikov 核權重線性組合 source 與 target surrogates。"
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [transfer-learning, surrogate, gaussian-process, task-similarity]
domain: [AI]
field: [AutoML]
status: active
created: 2026-08-15
updated: 2026-08-15
parent: P_Warm_Start_Initialization
parts: []
depends_on: []
caused_by: []
causes: []
related_lines: []
related_planes: []
related_body: [B_llm-warmstart-cash-mi-eeg-pipelines]
source: "Wistuba2016"
year: 2016
claim_type: methodological
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-2"
    - "step5_full_text/BaiEtAl2023.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# Two-Stage Transfer Surrogate (TST)

> **核心主張**：surrogate 層遷移可以拆成兩個階段——先為每個 source task 與 target task 各自獨立訓練 GP surrogate，再依任務相似度計算權重、將各 surrogate 的預測線性組合——使跨任務知識以「加權集成」而非「合併資料」的方式進入 BO。

## 來源
- 作者：Wistuba, M., Schilling, N., & Schmidt-Thieme, L. / 年份：2016 / 出處：(待補；依 BaiEtAl2023 參考文獻 [124]，題名 "Two-stage transfer surrogate model for automatic hyperparameter optimization") / citation key: `Wistuba2016`
- 備註：本文獻經 snowball 進入本 session 文獻網絡，未列入 step4 的 124 篇正式引用清單（無獨立 citation key）；方法描述取自 `BaiEtAl2023`（全文）與 step6 Theme 2 綜整，未讀原始全文。

## 目的
將多個來源任務的調參歷史帶進 target 任務的 BO，同時避免把所有任務觀測合併進單一 GP 的做法——後者受 GP 的 O(n³) 複雜度限制而難以擴展到多來源、多試驗的場景。

## 核心主張（展開）
TST 分兩階段。第一階段：為每個 source task 與 target task 各自獨立擬合 GP surrogate（source 端可離線預先完成）。第二階段：考慮任務相關性，以 Epanechnikov 核 δ(t) = ¾(1−t²)（t ≤ 1，否則為 0）對任務特徵距離 ‖χ^k − χ^T‖₂/ρ 計算各任務權重 β_k，其中 ρ 為頻寬參數；任務特徵 χ 有兩種取法——dataset meta-features（TST-M），或以第一階段訓練所得均值函數對配置對的 pairwise ranking 二值向量（TST-R，免 meta-features）。最終預測均值為各 surrogate 均值的權重歸一化線性組合，變異數則直接取 target surrogate 的變異數。TST-R 的排名式相似度隨 target 觀測累積而即時更新，是後續「on-the-fly 相似度」路線的起點。

## 方法
（原文全文未讀，實驗設計細節待補。）機制層面：base surrogates 離線訓練 + 線上核加權組合；TST-M 依賴人工設計的 meta-features，TST-R 只需 target 任務上已評估配置的排名一致性，兩變體構成「相似度來源」的對照。此設計後被收入生產級系統與大規模基準作為標準遷移基線。

## 發現
- (原始論文數字待補——未讀全文。)
- 下游基準證據（step6 Theme 2）：在 HPO-B 大規模基準（176 個 meta-datasets）上，TST-R 與 FSBO、RGPE、TAF-R 四種遷移方法全部顯著優於全部四種非遷移 BO 基線（ArangoEtAl2021）。
- 疊加證據：學習式搜尋空間設計可疊在 TST 之上再降低 22.6% 的誤差（RGPE 之上為 10.1%）（LiEtAl2022d，step6 Theme 2）。
- 侷限（TransBO 的批評）：權重為獨立計算的啟發式（Nadaraya-Watson 核），忽略來源任務互補性，且 TST-M 依賴「往往難以取得且需精細人工設計」的 meta-features。

## 啟發
- **被啟發**：product-of-GP 式 ensemble surrogate 想法（Schilling et al. 的 POGPE 一系，見 BaiEtAl2023）— TST 在其等權/常數權重組合上補入任務相似度加權。
- **啟發了**：[[P_TransBO]] — 保留兩段式骨架、以約束最佳化學出的聯合權重取代啟發式核權重；RGPE 亦沿同一 ensemble 路線改以排名損失機率定權重。
