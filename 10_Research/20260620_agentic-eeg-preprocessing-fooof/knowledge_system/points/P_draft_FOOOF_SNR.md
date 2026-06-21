---
schema_version: "1.0"
id: P_draft_FOOOF_SNR
type: point
name: "FOOOF-SNR (FOOOF-derived signal-to-noise ratio)"
description: "A label-free preprocessing-quality metric: periodic spectral power as signal, aperiodic 1/f + fit residual as noise"

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

source: "DonoghueEtAl2020b"
year: 2020
claim_type: methodological

provenance:
  session_id: "20260620"
  source_files:
    - "step8_hypothesis_specification.md#GAP_001"
    - "step6_sota_review.md#theme-2"
    - "step7_gap_analysis.md#GAP_001"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# FOOOF-SNR (FOOOF-derived signal-to-noise ratio)

> **核心主張**：把 FOOOF 分解出的週期功率當訊號、非週期 1/f 加擬合殘差當雜訊，可得到一個無標籤、任務無關、可在單筆靜息態記錄上計算的預處理品質指標。

## 來源
- 作者：本計畫自創指標，建立在 Donoghue et al. (FOOOF) 之上
- 年份：2026（提出）/ 2020（FOOOF 基礎）
- 出處：step8 hypothesis spec, GAP_001
- citation key: `DonoghueEtAl2020b`

## 目的
解決「EEG 預處理沒有客觀、無標籤的品質指標」這一被 [[P_draft_Automagic_QC]] 與 [[P_draft_Delorme_Quality_Metric]] 反覆點名的缺口；取代以下游任務準確率衡量預處理好壞的傳統做法。

## 核心主張（展開）
FOOOF-SNR = 週期成分功率 / (非週期 1/f 功率 + 擬合殘差)。因週期成分代表神經振盪（訊號），非週期背景與殘差代表 1/f 與未解釋變異（雜訊代理），其比值可作為預處理應最大化的獎勵。指標任務無關、無需標籤、適用單筆靜息態記錄。

## 方法
對預處理後的功率譜跑 FOOOF/specparam，取得週期峰與非週期參數；以帶限週期 SNR 計算，並以擬合優度（R²、error）把關以防 [[P_draft_FOOOF]] 的失效模式。

## 發現
（待驗證）H2 預測 FOOOF-SNR 排序與窮舉搜尋順序及專家評分顯著正相關（Spearman ρ，預註冊門檻）。

## 啟發
- **被啟發**：[[P_draft_FOOOF]] — 提供週期/非週期分解；[[P_draft_Delorme_Quality_Metric]] — 證明客觀預處理指標的需求
- **啟發了**：[[P_draft_Greedy_Oracle]] — 作為 oracle 的評分函數；[[P_draft_LLM_Preproc_Agent]] — 作為 agent 的獎勵