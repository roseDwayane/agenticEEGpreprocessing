---
schema_version: "1.0"
id: P_draft_Aperiodic_Artifact_Risk
type: point
name: "Aperiodic-as-signal vs aperiodic-as-artifact (reward design risk)"
description: "A naive 'minimize aperiodic' SNR risks rewarding preprocessing that destroys real aperiodic physiology"

asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: []

domain: [Neuroscience, AI]
field: [EEG, SignalProcessing]

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

source: "GersterEtAl2022"
year: 2022
claim_type: conceptual

provenance:
  session_id: "20260620"
  source_files:
    - "step7_gap_analysis.md#GAP_004"
    - "step6_sota_review.md#cross-theme"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# Aperiodic-as-signal vs aperiodic-as-artifact (reward design risk)

> **核心主張**：因非週期成分是真實神經訊號，天真的「最大化週期、最小化非週期」SNR 可能錯誤獎勵破壞正當非週期生理的預處理；獎勵須區分非週期生理與非週期污染。

## 來源
- 作者：本計畫綜整（GersterEtAl2022 + Theme 3）
- 年份：2026（GAP_004）
- 出處：step7 GAP_004
- citation key: `GersterEtAl2022`

## 目的
界定 FOOOF-SNR 的主要效度風險並提出設計約束。

## 核心主張（展開）
EMG 抬升高頻非週期功率、線雜訊加入週期峰、漂移改變低頻——這些是污染；而 E/I、喚醒相關的非週期變化是生理。獎勵須以帶限週期 SNR、artifact 頻段感知的雜訊項、擬合優度把關來區分。

## 方法
以已知非週期 ground truth 模擬 artifact，檢驗獎勵是否只懲罰污染而不懲罰生理。

## 發現
（待驗證）為 GAP_001 指標效度的必要子貢獻。

## 啟發
- **被啟發**：[[P_draft_Aperiodic_Signal]] — 非週期是真實訊號；[[D_draft_FOOOF_vs_IRASA]] — 分解的失效模式
- **啟發了**：[[P_draft_FOOOF_SNR]] — 作為其內建設計約束