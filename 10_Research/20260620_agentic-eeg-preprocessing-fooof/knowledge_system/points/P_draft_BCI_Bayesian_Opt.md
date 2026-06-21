---
schema_version: "1.0"
id: P_draft_BCI_Bayesian_Opt
type: point
name: "Bayesian optimization of per-user BCI pipelines"
description: "Applied Bayesian optimization to tune BCI pipelines per user — nearest EEG-specific search precedent"

asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [needs_fulltext]

domain: [AI, Neuroscience]
field: [BCI, AutoML]

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

source: "BashashatiEtAl2016"
year: 2016
claim_type: methodological

provenance:
  session_id: "20260620"
  source_files:
    - "step6_sota_review.md#theme-5"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# Bayesian optimization of per-user BCI pipelines

> **核心主張**：以貝氏最佳化為每位使用者調整 BCI 管線——最接近的 EEG 專屬搜尋前例（前 FOOOF、最佳化任務準確率）。

## 來源
- 作者：Bashashati, Hossein 等
- 年份：2016（Journal of Neural Engineering）
- 出處：step6 theme-5
- citation key: `BashashatiEtAl2016`

## 目的
為個別使用者客製化 BCI 管線以提升表現。

## 核心主張（展開）
貝氏最佳化調 BCI 管線超參數。顯示 EEG 管線「逐使用者最佳化」的前例存在，但比 agent 文獻落後一整代，且以準確率為目標。

## 方法
（待補）貝氏最佳化 over BCI 管線超參數。

## 發現
（待補）客製化優於通用設定。

## 啟發
- **被啟發**：[[P_draft_AutoML_HPO]]
- **啟發了**：[[P_draft_Personalized_Pipeline_Search]]、[[P_draft_LLM_Preproc_Agent]]