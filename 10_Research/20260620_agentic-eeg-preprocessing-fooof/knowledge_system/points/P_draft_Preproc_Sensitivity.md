---
schema_version: "1.0"
id: P_draft_Preproc_Sensitivity
type: point
name: "EEG preprocessing sensitivity"
description: "Downstream EEG measures shift substantially and non-uniformly with preprocessing choices"

asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [needs_fulltext]

domain: [Neuroscience]
field: [EEG, Preprocessing]

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

source: "RobbinsEtAl2020"
year: 2020
claim_type: empirical

provenance:
  session_id: "20260620"
  source_files:
    - "step5_full_text/RobbinsEtAl2020.md"
    - "step6_sota_review.md#theme-4"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# EEG preprocessing sensitivity

> **核心主張**：即使遵循最佳實務，預處理方法與參數的變異仍使下游 EEG 結果大幅且不一致地改變。

## 來源
- 作者：Robbins, Kay A. 等
- 年份：2020（IEEE TNSRE）
- 出處：step5 RobbinsEtAl2020.md（abstract-only）
- citation key: `RobbinsEtAl2020`

## 目的
量化 EEG 分析結果對預處理選擇的敏感度。

## 核心主張（展開）
在模擬與真實資料上評估多條管線（LARG, MARA, ASR），發現低頻特徵與 blink 殘留差異顯著——說明預處理「選擇」確實重要，且目前是 ad hoc 決定的。

## 方法
（abstract-only, 待補）以簡單訊號與 ERP 指標評估多條預處理管線在時域與頻域的殘留差異。

## 發現
（待補）跨方法差異顯著，需詳細記錄與標準化比較工具。

## 啟發
- **被啟發**：[[P_draft_PREP_Pipeline]] — 同團隊
- **啟發了**：[[P_draft_Delorme_Quality_Metric]]、[[P_draft_FOOOF_SNR]] — motivate 需要可最佳化的客觀目標