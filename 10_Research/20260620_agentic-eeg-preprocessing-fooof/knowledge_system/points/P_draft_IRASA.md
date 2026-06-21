---
schema_version: "1.0"
id: P_draft_IRASA
type: point
name: "IRASA (Irregular-Resampling Auto-Spectral Analysis)"
description: "Separates fractal (aperiodic) from oscillatory power by resampling — the principal alternative to FOOOF"

asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [needs_fulltext]

domain: [Neuroscience]
field: [EEG, SpectralAnalysis]

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

source: "WenLiu2016"
year: 2016
claim_type: methodological

provenance:
  session_id: "20260620"
  source_files:
    - "step6_sota_review.md#theme-2"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# IRASA (Irregular-Resampling Auto-Spectral Analysis)

> **核心主張**：透過不規則重採樣分離功率譜中的碎形（非週期）與振盪成分。

## 來源
- 作者：Wen, Haiguang; Liu, Zhongming
- 年份：2016（Brain Topography）
- 出處：step6 theme-2
- citation key: `WenLiu2016`

## 目的
在 FOOOF 之外提供另一條分離週期/非週期的路徑，作為品質指標可選的分解後端。

## 核心主張（展開）
利用重採樣會平移振盪峰但不改變碎形成分的性質，以中位數消去振盪、留下 1/f 碎形。是 FOOOF 的主要替代法。

## 方法
（待補全文）對訊號做多組非整數因子重採樣、求功率譜中位數得碎形成分，原譜減碎形得振盪成分。

## 發現
（待補）被 GersterEtAl2022 與 FOOOF 系統性比較；各有失效情境。

## 啟發
- **被啟發**：（無，方法原創）
- **啟發了**：[[D_draft_FOOOF_vs_IRASA]] — 與 FOOOF 的比較與失效模式