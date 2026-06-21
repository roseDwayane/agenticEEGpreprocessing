---
schema_version: "1.0"
id: P_draft_Greedy_Oracle
type: point
name: "Greedy/exhaustive-search ground-truth oracle for EEG preprocessing"
description: "Exhaustively search the preprocessing parameter grid, score each output by FOOOF-SNR, record per-recording best config"

asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: []

domain: [AI, Neuroscience]
field: [EEG, AutoML]

status: draft
created: 2026-06-20
updated: 2026-06-20

parent:
parts: []
depends_on: []
caused_by: []
causes: []

related_lines: []
related_planes: []
related_body: []

source: "MartinezEtAl2023"
year: 2023
claim_type: methodological

provenance:
  session_id: "20260620"
  source_files:
    - "step8_hypothesis_specification.md#GAP_002"
    - "step7_gap_analysis.md#GAP_002"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# Greedy/exhaustive-search ground-truth oracle for EEG preprocessing

> **核心主張**：在公開資料集上窮舉/貪婪搜尋預處理參數格，以 FOOOF-SNR 評分每個輸出，得到每筆記錄的最佳組態——一個目前文獻不存在的 ground-truth 資源。

## 來源
- 作者：本計畫（Pillar 1）；方法範本 MartinezEtAl2023
- 年份：2026（GAP_002）
- 出處：step8 hypothesis spec, GAP_002
- citation key: `MartinezEtAl2023`

## 目的
建立可供監督/模仿學習 agent 使用的 ground truth，並提供衡量 agent 的上界。

## 核心主張（展開）
每筆記錄 × 每個合理管線組態 → FOOOF-SNR → 最佳組態。CoelliEtAl2023 手動做少數方法的逐步比較；本 oracle 將其規模化、自動化，並可作為首個同類基準獨立發表。

## 方法
定義預處理格（濾波截止、line-noise、壞通道準則、ICA+ICLabel 閾值、ASR 截止、重參考）；貪婪/座標搜尋 + 快取中間階段 + 剪枝。

## 發現
（待驗證）H1 預測每筆最佳組態顯著優於最佳固定管線，且最佳組態跨記錄有意義變異。

## 啟發
- **被啟發**：[[P_draft_FOOOF_SNR]]（評分函數）、[[P_draft_AutoML_HPO]]、[[P_draft_Personalized_Pipeline_Search]]（搜尋）
- **啟發了**：[[P_draft_LLM_Preproc_Agent]] — 提供訓練監督與上界