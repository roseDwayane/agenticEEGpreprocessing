---
schema_version: "1.0"
id: P_draft_PREP_Pipeline
type: point
name: "PREP pipeline (standardized EEG preprocessing)"
description: "Formalized robust referencing and bad-channel detection as a standardized early-stage EEG pipeline"

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

source: "BigdelyShamloEtAl2015"
year: 2015
claim_type: methodological

provenance:
  session_id: "20260620"
  source_files:
    - "step6_sota_review.md#theme-1"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# PREP pipeline (standardized EEG preprocessing)

> **核心主張**：以多階段穩健重參考方案處理 channel-reference 交互作用，建立標準化的早期 EEG 預處理流程。

## 來源
- 作者：Bigdely-Shamlo, Nima 等
- 年份：2015（Frontiers in Neuroinformatics）
- 出處：step6 theme-1
- citation key: `BigdelyShamloEtAl2015`

## 目的
為大規模 EEG 分析提供可重現、自動化的早期預處理標準。

## 核心主張（展開）
不足的早期預處理會降低 SNR、引入 artifact；PREP 提出穩健參考、壞通道偵測與自動報告，成為後續管線（HAPPE 等）的基礎元件。

## 方法
（待補全文）穩健平均參考迭代估計、壞通道偵測、line-noise 移除；自動產生每筆報告。

## 發現
（待補）成為被廣泛重用的標準（引用 ~1408）。

## 啟發
- **被啟發**：（無，奠基性）
- **啟發了**：[[P_draft_HAPPE]] — 重用穩健參考；[[P_draft_Preproc_Sensitivity]] — 同團隊的敏感度研究