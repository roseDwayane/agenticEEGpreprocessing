---
schema_version: "1.0"
id: P_draft_HAPPE
type: point
name: "HAPPE (Harvard Automated Processing Pipeline for EEG)"
description: "Standardized automated pipeline for developmental and high-artifact EEG, with quality reports"

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

source: "GabardDurnamEtAl2018"
year: 2018
claim_type: methodological

provenance:
  session_id: "20260620"
  source_files:
    - "step6_sota_review.md#theme-1"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# HAPPE (Harvard Automated Processing Pipeline for EEG)

> **核心主張**：為發展與高 artifact EEG 提供標準化、自動化的預處理管線，並附品質報告，效能勝過七種替代法。

## 來源
- 作者：Gabard-Durnam, Laurel J. 等
- 年份：2018（Frontiers in Neuroscience）
- 出處：step6 theme-1
- citation key: `GabardDurnamEtAl2018`

## 目的
讓高 artifact、短時長的發展族群 EEG 能被一致地自動處理。

## 核心主張（展開）
整合濾波、artifact 移除、重參考；含後處理品質報告。有低密度（HAPPILEE）與 ERP（HAPPE+ER）變體。是計畫 agent 動作空間的代表性固定管線與 baseline。

## 方法
（待補全文）濾波 → line-noise → 壞通道偵測 → wavelet/ICA artifact 校正 → 分段 → 段落拒絕。

## 發現
（待補）在近乎所有情況下比七種替代法移除更多 artifact 並保留更多訊號。

## 啟發
- **被啟發**：[[P_draft_PREP_Pipeline]]、[[P_draft_Autoreject]]、[[P_draft_ICLabel]] — 重用其元件
- **啟發了**：低密度/ERP/新生兒變體；作為計畫的固定管線 baseline