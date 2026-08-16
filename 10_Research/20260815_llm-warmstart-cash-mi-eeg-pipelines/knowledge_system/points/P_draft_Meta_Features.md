---
schema_version: "1.0"
id: P_draft_Meta_Features
type: point
name: "Meta-Features"
description: "以資料集/任務的統計特徵向量作為任務相似度計算與配置推薦的座標系；FeurerEtAl2015 實作 46 個、分五大類，計算成本低於單次配置評估。"
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [meta-features, meta-learning, task-similarity]
domain: [AI]
field: [AutoML]
status: draft
created: 2026-08-15
updated: 2026-08-15
parent: P_draft_Warm_Start_Initialization
parts: []
depends_on: []
caused_by: []
causes: []
related_lines: []
related_planes: []
related_body: [B_draft_llm-warmstart-cash-mi-eeg-pipelines]
source: "FeurerEtAl2015"
year: 2015
claim_type: conceptual
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-2"
    - "step5_full_text/FeurerEtAl2015.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# Meta-Features

> **核心主張**：把每個資料集/任務描述成一個固定維度的統計特徵向量（meta-features），任務間距離便可在此座標系中計算——這是「向誰借配置」「借多少」等一切 meta-learning 式相似度判斷的基礎表徵。

## 來源
- 作者：Feurer, M., Springenberg, J. T., & Hutter, F. / 年份：2015 / 出處：Proceedings of the AAAI Conference on Artificial Intelligence（AAAI-15；作為 MI-SMBO 的相似度基礎設施）/ citation key: `FeurerEtAl2015`

## 目的
讓「資料集相似度」可計算：若無法量化新任務與過往任務的距離，任何「從相似任務借知識」的策略都無從自動化。Meta-features 提供一個廉價（不需在新任務上評估任何配置）的先驗座標系。

## 核心主張（展開）
FeurerEtAl2015 實作了文獻中的 46 個 meta-features，分五大類：simple（樣本數、特徵數、類別數等基本結構）、statistical（kurtosis、skewness、類別分布離散度等描述統計）、PCA-based（主成分統計量）、information-theoretic（class entropy）、landmarking（快速執行 1-NN、LDA、Naive Bayes、decision tree 等簡易演算法的表現，捕捉如線性可分性等性質）。距離有兩種用法：直接取 meta-feature 差向量的 p-norm（d_p），或更進一步以 random forest 迴歸學習「meta-feature 對 → 配置效能排名 Spearman 相關」的映射（d_c），後者以效能相關性校準了座標系。重要的誠實發現：實驗顯示「沒有普遍最優的 meta-feature 子集」；且 KDD 系列後續工作批評 meta-features「往往難以取得且需精細人工設計」（LiEtAl2022c/d），催生了 TST-R、γ-similarity 等免 meta-features 的排序式相似度替代路線——meta-features 是座標系的一種實作，不是唯一實作。

## 方法
每個資料集僅在訓練集上計算 meta-features；實驗中每個資料集的計算時間少於 1 分鐘、低於該資料集單次超參數配置評估的平均時間（成本論證：座標系近乎免費）。以 57 個 OpenML 資料集、leave-one-dataset-out 設定驗證其下游效用：以 d_p 與 d_c 排序資料集、挑選 t 個最相似資料集的最優配置初始化 SMAC/Spearmint；d_c 的迴歸器以所有訓練資料集對的 (meta-features, 排名相關) 為監督訊號離線訓練。

## 發現
- 46 個 meta-features、五大類；計算成本 < 1 分鐘/資料集，低於單次配置評估的平均時間。
- d_p 與 d_c 結果質性相近、d_c 略優；不同 meta-feature 子集間無普遍贏家，最終報告使用全部 46 個。
- 下游效用即 MI-SMAC 的主結果（見 [[P_draft_MI_SMAC]]）：50 次評估後在 35% 資料集上顯著優於冷啟動 SMAC、僅 7% 較差。
- 反面證據（step6 Theme 2 Debates）：RijnHutter2017 提供無需 meta-features 的 fANOVA 先驗；TST-R/γ-similarity 走 on-the-fly 排序相似度——座標系的選擇本身是開放的設計空間。

## 啟發
- **被啟發**：meta-learning 傳統（Brazdil et al. 2008；Soares & Brazdil 2000 的資料集距離度量與 landmarking 文獻）— FeurerEtAl2015 將其彙整為可複用的 46 維實作。
- **啟發了**：[[P_draft_MI_SMAC]] — 相似度排序直接決定初始設計的配置來源；[[P_draft_Similarity_Gating]] — 門控函數 g(s) 需要的相似度訊號即定義於此座標系上；本研究將其 EEG 化：以 band power、CSP eigen-spectrum 等訊號層統計量構造受試者 meta-features，作為跨受試者配置遷移與門控的座標系。
