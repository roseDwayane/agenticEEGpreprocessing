---
schema_version: "1.0"
id: P_draft_ICLabel
type: point
name: "ICLabel (automated IC classifier)"
description: "Automated classification of ICA components into brain/muscle/eye/heart/line-noise/channel-noise/other"

asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [needs_fulltext]

domain: [Neuroscience, AI]
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

source: "PionTonachiniEtAl2019"
year: 2019
claim_type: methodological

provenance:
  session_id: "20260620"
  source_files:
    - "step6_sota_review.md#theme-1"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# ICLabel (automated IC classifier)

> **核心主張**：用在 20 萬+ IC、6000+ 記錄上訓練的分類器，自動把 ICA 成分分到七類（腦、肌肉、眼、心、線雜訊、通道雜訊、其他）。

## 來源
- 作者：Pion-Tonachini, Luca 等
- 年份：2019（NeuroImage）
- 出處：step6 theme-1
- citation key: `PionTonachiniEtAl2019`

## 目的
讓 ICA-based 清理可自動化、可規模化，不需人工逐成分標註。

## 核心主張（展開）
以頭皮地形、PSD、自相關、dipole 擬合等特徵分類 IC。是 ICA 清理流程的標準工具；有 Python 版 MNE-ICALabel。引用極高（~2298）。

## 方法
（待補）多特徵集 → 神經網路分類器 → 七類機率。

## 發現
（待補）成為自動 ICA 清理的事實標準。

## 啟發
- **被啟發**：（無）
- **啟發了**：MNE-ICALabel（Python port）；作為 agent 可呼叫的清理工具