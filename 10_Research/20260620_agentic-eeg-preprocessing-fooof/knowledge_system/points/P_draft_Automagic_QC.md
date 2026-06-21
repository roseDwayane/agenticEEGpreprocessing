---
schema_version: "1.0"
id: P_draft_Automagic_QC
type: point
name: "Automagic objective quality-assessment gap"
description: "Named the absence of an objective way to quantify preprocessed-EEG quality"

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

source: "PedroniEtAl2019"
year: 2019
claim_type: conceptual

provenance:
  session_id: "20260620"
  source_files:
    - "step5_full_text/PedroniEtAl2019.md"
    - "step6_sota_review.md#theme-1"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# Automagic objective quality-assessment gap

> **核心主張**：Automagic 明言「沒有方法能客觀量化預處理後 EEG 的品質」，此空白導致主觀剔除受試與 p-hacking。

## 來源
- 作者：Pedroni, Andreas 等
- 年份：2019（NeuroImage）
- 出處：step5 PedroniEtAl2019.md（abstract-only）
- citation key: `PedroniEtAl2019`

## 目的
為大型成長中 EEG 資料提供標準化預處理 + 客觀品質評估包裝。

## 核心主張（展開）
Automagic 是 MATLAB 工具箱，包裝既有預處理法並提供標準化品質評估、BIDS 相容。其論文點名的「無客觀品質指標」正是計畫 FOOOF-SNR 要填的洞之一。

## 方法
（abstract-only, 待補）wrapper 執行多種預處理 + 品質量化 + 資料管理。

## 發現
（待補）品質指標缺失助長 p-hacking、降低可重現性。

## 啟發
- **被啟發**：（無）
- **啟發了**：[[P_draft_FOOOF_SNR]] — 回應此被點名的缺口