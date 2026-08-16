---
schema_version: "1.0"
id: P_Subjects_as_Tasks
type: point
name: "Subjects-as-Tasks"
description: "把 EEG 受試者視為 transfer-HPO 意義下的任務家族——AutoML 端的 datasets-as-tasks 與 EEG 端的 subjects-as-tasks（權重層級）雙邊皆成熟，卻無人在配置／搜尋層級搭起這座橋。"
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [transfer-hpo, eeg, concept]
domain: [AI, Neuroscience]
field: [BCI]
status: active
created: 2026-08-15
updated: 2026-08-15
parent:
parts: []
depends_on: [P_Inter_Subject_Variability, P_BCI_Calibration_Problem]
caused_by: []
causes: []
related_lines: []
related_planes: []
related_body: [B_llm-warmstart-cash-mi-eeg-pipelines]
source: "本人合成 (anchors: GAP_001 / step7, step8)"
year: 2026
claim_type: conceptual
provenance:
  session_id: "20260815"
  source_files:
    - "step8_hypothesis_specification.md"
    - "step7_gap_analysis.md"
    - "step6_sota_review.md#cross-theme-analysis"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# Subjects-as-Tasks

> **核心主張**：把每位 EEG 受試者視為 transfer-HPO 意義下的一個任務（task），受試者群體即任務家族——這使 AutoML 文獻中整套跨任務暖啟動機制（meta-feature 初始化、πBO 先驗注入、搜尋歷史遷移）可直接遷移到逐受試者管線搜尋上；此概念橋的兩端各自成熟，卻無任何現有工作佔據橋本身。

## 來源
- 作者：本人合成（概念卡）/ 年份：2026 / 出處：本 session 之 GAP_001（step7 gap analysis, composite 4.60）與 step8 hypothesis specification；概念錨點為 FeurerEtAl2015（datasets-as-tasks）與 BerdyshevEtAl2024（subjects-as-tasks，權重層級）/ citation key: `本人合成`（anchors: `FeurerEtAl2015`, `BerdyshevEtAl2024`）

## 目的
為本研究提供單一的概念樞紐：一旦受試者被形式化為任務，「跨受試者配置遷移」「LLM 先驗注入」「相似度門控」就分別對應 transfer-HPO 已驗證的 warm-start、prior injection 與 similarity gating 機制，全部設計可從文獻直接繼承。

## 核心主張（展開）
類比的兩個半邊各自有堅實先例。AutoML 端：MI-SMBO（FeurerEtAl2015）把「資料集」視為任務，以 meta-feature 相似度挑選在相似資料集上表現良好的配置來初始化 SMAC——datasets-as-tasks 是 transfer-HPO 的奠基框架。EEG 端：EEG-Reptile（BerdyshevEtAl2024）明確把「個別 BCI 使用者的分類問題視為獨立任務」，但遷移的是神經網路權重初始化，不是配置或搜尋知識。合成主張是：這兩個類比在結構上同構——受試者之於 EEG 管線搜尋，正如資料集之於 CASH——因此 Theme 2 的全部機制（初始點設計、先驗注入、空間設計、代理模型遷移）在受試者維度上原則上皆適用。而 step7/step8 的缺口分析證實此橋無人佔據：124 篇篩選文獻中，零篇將暖啟動搜尋與 EEG 管線最佳化結合；Theme 4 的 11 篇 EEG 自動化論文中 BO-CASH 為 0 篇；Theme 5 只遷移權重、從不遷移配置。受試者間變異（含約 30 倍偏差的離群個體）同時警示：此任務家族的異質性高於典型 OpenML 資料集家族，相似度門控與先驗衰減（πBO 式安全底線）是類比成立的必要條件，而非可選項。

## 方法
概念合成程序：(1) 從 step6 跨主題分析抽出兩端最接近的先例（BerdyshevEtAl2024 的權重層級 subjects-as-tasks 與隱性的微調超參數重用；AnarakiEtAl2024 由資料集特徵預測個人化分類器）；(2) 經 step7 以 124 篇文獻驗證交集為空（GAP_001, composite 4.60）；(3) 在 step8 操作化為概念框架——每受試者一任務、LOSO 搜尋歷史與 LLM 引出先驗為兩種知識來源、以受試者 meta-features（band power、CSP eigen-spectrum、baseline accuracy）計算相似度門控 g(s)，先驗在目標受試者遠離來源池時衰減向均勻分布。

## 發現
概念卡，無自有實驗數字；支撐數據皆來自錨點文獻：
- 類比可行性的 AutoML 端證據：MI-SMAC 在 57 個 OpenML 資料集的 35% 上顯著優於冷啟動 SMAC（僅 7% 較差），其暖啟動增益超過 SMAC 對隨機搜尋的增益（FeurerEtAl2015，經 step6 Theme 2）。
- 橋空缺的量化：124 篇中 0 篇結合暖啟動搜尋與 EEG 管線最佳化；Theme 4 中 BO 家族 CASH 0/11；Theme 5 中配置層級遷移 0 篇（step7 GAP_001）。
- 初步橋接證據（實驗室 pilot, step8）：EEGMMI S002 上 warm 0.950 vs cold 0.915（預算約 30 次評估）；零樣本配置遷移 0.765，落在 baseline 0.635 與搜尋最優 0.950 之間（保留率約 41%）。

## 啟發
- **被啟發**：[[P_MI_SMAC]] — datasets-as-tasks 的原型（meta-feature 相似度暖啟動）提供類比的 AutoML 半邊；[[P_EEG_Reptile]] — 權重層級的 subjects-as-tasks 提供 EEG 半邊，其 few-shot 低天花板（43%→46%）並論證了配置層級遷移的必要性；[[P_Inter_Subject_Variability]] 與 [[P_BCI_Calibration_Problem]] — 供給任務異質性與成本動機。
- **啟發了**：[[B_llm-warmstart-cash-mi-eeg-pipelines]] — 本研究全部設計（RQ1–RQ4、H1–H3、LOSO 先驗學習、相似度門控、零樣本遷移臂）皆由此概念展開；它是把 [[P_Budget_Matched_Protocol]] 與 [[P_Seeded_Baseline]] 的方法學防護接到 EEG 場域的載體。
