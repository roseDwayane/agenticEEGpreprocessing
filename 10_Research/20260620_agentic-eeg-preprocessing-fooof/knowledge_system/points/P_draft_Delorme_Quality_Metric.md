---
schema_version: "1.0"
id: P_draft_Delorme_Quality_Metric
type: point
name: "Delorme objective preprocessing-quality metric (EEG is better left alone)"
description: "A label-light metric (% significant channels post-stimulus) showing many automated steps don't help or hurt"

asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: []

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

source: "Delorme2023"
year: 2023
claim_type: empirical

provenance:
  session_id: "20260620"
  source_files:
    - "step5_full_text/Delorme2023.md"
    - "step6_sota_review.md#theme-4"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# Delorme objective preprocessing-quality metric (EEG is better left alone)

> **核心主張**：以「刺激後窗內顯著通道百分比」為客觀品質指標比較各軟體最佳化管線，發現多數自動校正步驟不是沒幫助就是有害，僅高通濾波與壞通道內插可靠有益。

## 來源
- 作者：Delorme, Arnaud
- 年份：2023（Scientific Reports）
- 出處：step5 Delorme2023.md（full-text）
- citation key: `Delorme2023`

## 目的
建立客觀指標以比較自動預處理管線，並檢驗「更多校正是否更好」。

## 核心主張（展開）
因 volume conduction，無雜訊時多數 ERP 應出現在多數通道；故顯著通道比例可當品質代理。比較 EEGLAB/FieldTrip/MNE/Brainstorm 最佳化管線，僅一條顯著優於單純高通。

## 方法
跨三個公開資料集，計算兩條件刺激後 100ms 窗內顯著通道百分比。

## 發現
重參考與進階基線移除有害；ICA 拒絕眼動/肌肉 artifact 未可靠提升；多數自動步驟無益或有害。

## 啟發
- **被啟發**：[[P_draft_Preproc_Sensitivity]] — 預處理敏感度
- **啟發了**：[[P_draft_FOOOF_SNR]] — 證明客觀指標需求，但其指標需標籤/條件，留下無標籤情形給 FOOOF-SNR