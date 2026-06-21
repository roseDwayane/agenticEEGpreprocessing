---
schema_version: "1.0"
id: P_draft_LLM_Preproc_Agent
type: point
name: "LLM-agent EEG preprocessing controller"
description: "An LLM agent that reads intermediate signal features and selects the next preprocessing operation + parameters"

asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: []

domain: [AI, Neuroscience]
field: [EEG, LLMAgents]

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

source: "GuoEtAl2024"
year: 2024
claim_type: methodological

provenance:
  session_id: "20260620"
  source_files:
    - "step8_hypothesis_specification.md#GAP_003"
    - "step7_gap_analysis.md#GAP_003"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# LLM-agent EEG preprocessing controller

> **核心主張**：一個讀取中間 EEG 訊號特徵、選擇下一個預處理操作與其參數的 LLM agent——首次將工具使用型 LLM agent 用於電生理預處理。

## 來源
- 作者：本計畫（Pillar 3）；範型 GuoEtAl2024 等
- 年份：2026（GAP_003）
- 出處：step8 hypothesis spec, GAP_003
- citation key: `GuoEtAl2024`

## 目的
以可解釋方式廉價地達到 oracle 的近最佳預處理組態。

## 核心主張（展開）
LLM controller 讀頻譜、通道統計、ICA 成分標籤、中間 FOOOF 估計，呼叫工具選下一步。由 [[P_draft_Greedy_Oracle]] 提供監督與上界。成功將是 LLM agent 遷移到電生理訊號處理的首次展示。

## 方法
工具增強 LLM agent（可選搜尋增強）over 預處理格；以 oracle regret、與固定管線/古典搜尋比較、留出泛化、可解釋性評估。

## 發現
（待驗證）H3 預測 agent 在留出資料上顯著比固定管線更接近 oracle，且以更低查詢成本與古典搜尋相當。

## 啟發
- **被啟發**：[[P_draft_LLM_DS_Agent]]（agent 範型）、[[P_draft_Greedy_Oracle]]（監督）、[[P_draft_Personalized_Pipeline_Search]]（搜尋前作）
- **啟發了**：（下游：未來 RL 變體、線上版本）