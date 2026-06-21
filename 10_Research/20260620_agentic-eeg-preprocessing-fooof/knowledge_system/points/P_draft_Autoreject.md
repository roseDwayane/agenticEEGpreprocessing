---
schema_version: "1.0"
id: P_draft_Autoreject
type: point
name: "Autoreject (automated artifact rejection)"
description: "Cross-validation estimates per-sensor peak-to-peak thresholds to reject/repair bad trials in M/EEG"

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

source: "JasEtAl2017"
year: 2017
claim_type: methodological

provenance:
  session_id: "20260620"
  source_files:
    - "step6_sota_review.md#theme-1"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# Autoreject (automated artifact rejection)

> **核心主張**：以交叉驗證自動估計逐感測器的 peak-to-peak 閾值，做試次層級的拒絕與修復。

## 來源
- 作者：Jas, Mainak 等（Gramfort 組）
- 年份：2017（NeuroImage）
- 出處：step6 theme-1
- citation key: `JasEtAl2017`

## 目的
以資料驅動方式取代人工設定的固定 artifact 閾值。

## 核心主張（展開）
用交叉驗證 + 穩健評估指標估最佳 peak-to-peak 閾值，延伸到逐感測器；依感測器數做內插或排除。全自動。是計畫動作空間中可調參數（閾值）的代表。

## 方法
（待補）交叉驗證搜尋閾值；逐 trial / 逐 sensor artifact 標記與修復。

## 發現
（待補）跨四個公開資料集 200+ 受試驗證，與 SOTA 相當或更佳。

## 啟發
- **被啟發**：（無）
- **啟發了**：[[P_draft_HAPPE]]-class 管線採用；作為 agent 可調的拒絕步驟