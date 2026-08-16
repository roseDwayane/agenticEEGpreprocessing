---
schema_version: "1.0"
id: P_draft_MI_SMAC
type: point
name: "MI-SMAC (Meta-learning Initialization for SMAC)"
description: "以 dataset meta-features 挑選過往任務最優配置作為 SMAC 的初始設計；在 CASH 空間上 warm-start 帶來的增益超過 SMAC 本身相對 random search 的增益。"
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [warm-start, meta-learning, smac, cash]
domain: [AI]
field: [AutoML]
status: draft
created: 2026-08-15
updated: 2026-08-15
parent: P_draft_Warm_Start_Initialization
parts: []
depends_on: [P_draft_Meta_Features]
caused_by: []
causes: []
related_lines: []
related_planes: []
related_body: [B_draft_llm-warmstart-cash-mi-eeg-pipelines]
source: "FeurerEtAl2015"
year: 2015
claim_type: methodological
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-2"
    - "step5_full_text/FeurerEtAl2015.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# MI-SMAC (Meta-learning Initialization for SMAC)

> **核心主張**：把「在 meta-features 相似資料集上表現最優的配置」直接當作 SMAC 的初始設計（initial design），即可在大型 CASH 空間顯著加速搜尋——此 warm-start 增益甚至超過 SMAC 相對 random search 的增益。

## 來源
- 作者：Feurer, M., Springenberg, J. T., & Hutter, F. / 年份：2015 / 出處：Proceedings of the AAAI Conference on Artificial Intelligence（AAAI-15）/ citation key: `FeurerEtAl2015`

## 目的
模仿人類專家「從相似資料集上曾經有效的配置開始調參」的策略，解決 SMBO 在新任務上冷啟動需大量評估才能找到高效能區域的問題，且不修改底層 SMBO 演算法本身。

## 核心主張（展開）
MI-SMBO 將 SMBO 的初始化元件替換為 meta-learning 建議的配置序列：離線階段先為 N 個訓練資料集各記錄最優配置 θ̂ 與 meta-features 向量；新資料集到來時，依 meta-feature 距離將舊資料集排序，取距離最近的前 t 個資料集的最優配置作為初始設計，之後照常執行 SMBO。方法對 SMBO 變體完全 agnostic——只要最佳化器接受 initial design 或效能資料即可套用（SMAC、TPE、Spearmint 皆符合）。距離度量有兩種：meta-features 差向量的 p-norm（d_p），以及以 random forest 迴歸從 meta-features 對預測「配置效能排名的 Spearman 相關」的學習式距離（d_c），實驗顯示 d_c 略優。關鍵結論：搜尋空間越大、預算越小，初始化的貢獻越大——在 2 個超參數的 SVM 問題上 Spearmint 約 10 次評估即追平，但在 10 個超參數的 CASH 空間上 50 次評估後 SMAC 仍未追上。

## 方法
在 57 個 OpenML 分類資料集上，以 leave-one-dataset-out 方式評估：CASH 空間為 scikit-learn 的 3 個分類器（RBF-SVM、linear SVM、random forest）+ PCA 前處理，共 10 個超參數、離散化為 1,623 個配置並預先算好 10-fold CV 誤差（查表模擬）；另設 2 超參數 SVM（399 個配置）的低維對照。實作 46 個 meta-features（simple、statistical、PCA、information-theoretic、landmarking 五大類），每個資料集計算耗時不到 1 分鐘、低於單次配置評估的平均時間。初始配置數 t ∈ {5, 10, 20, 25}，每方法每資料集重複 10 次（每個最佳化程序共 570 次執行），以 bootstrap 平均排名與 two-sided t-test 勝負比例彙整。

## 發現
- CASH 空間、50 次評估後：MI-SMAC（t=20, 學習式距離）顯著優於冷啟動 SMAC 的資料集佔 35%（劣於僅 7%）、優於 TPE 佔 28%（劣 11%）、優於 random search 佔 43%（劣 9%）。
- 對照組：SMAC 顯著優於 random search 的資料集僅佔 20%——即「換更好的初始化」比「從 random search 換成 SMAC」增益更大。
- 50 次評估後仍無任何冷啟動 SMBO 方法完全追上 MI-SMBO，顯示 meta-learning 初始化亦提供了後續改進的良好基礎。
- 低維 SVM 問題：MI-Spearmint 僅在小預算時領先，約 10 次評估後 Spearmint 追平；且此問題上 t 取小值較好（與 CASH 空間相反，CASH 上 t 越大越好）。
- 失敗案例確實存在：論文圖 1 右圖顯示 meta-learning 建議亦可能降低 SMAC 表現（負遷移的早期證據）。

## 啟發
- **被啟發**：[[P_draft_SMAC]] — 直接建立在 SMAC 的 SMBO 框架與其可接受 initial design 的介面之上；[[P_draft_Meta_Features]] — 46 個 meta-features 是配置推薦的座標系，整套方法的相似度計算依賴之。
- **啟發了**：[[P_draft_PiBO]] — 從「離散配置清單」進化為「連續先驗分布」的知識注入；auto-sklearn 將本方法的 meta-learning 初始化內建為系統元件；[[P_draft_Zero_Shot_Configuration_Transfer]] — 其 t 個 meta-建議配置在 SMBO 開始前先被評估，本身即是零樣本配置遷移的原型。
