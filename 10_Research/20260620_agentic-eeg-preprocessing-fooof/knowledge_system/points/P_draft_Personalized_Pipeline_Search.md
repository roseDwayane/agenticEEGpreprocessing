---
schema_version: "1.0"
id: P_draft_Personalized_Pipeline_Search
type: point
name: "Personalized (per-instance) preprocessing pipeline search"
description: "Searches a per-instance preprocessing pipeline — closest prior art to per-recording adaptive preprocessing"

asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: []

domain: [AI]
field: [AutoML, Preprocessing]

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

source: "MartinezEtAl2023"
year: 2023
claim_type: methodological

provenance:
  session_id: "20260620"
  source_files:
    - "step5_full_text/MartinezEtAl2023.md"
    - "step6_sota_review.md#theme-5"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# Personalized (per-instance) preprocessing pipeline search

> **核心主張**：為每個實例搜尋專屬的預處理管線——最接近「agent 為每筆記錄挑參數」的前作，但針對通用 ML 資料且最佳化任務損失。

## 來源
- 作者：Martinez, Diego 等
- 年份：2023（arXiv）
- 出處：step5 MartinezEtAl2023.md（full-text）
- citation key: `MartinezEtAl2023`

## 目的
證明「逐實例預處理選擇」可行——而非全資料集套同一管線。

## 核心主張（展開）
以雙層搜尋為每個實例找最佳預處理管線。是計畫 GAP_003 的最近前作；缺的是 EEG 領域與 FOOOF 式品質目標。

## 方法
（full-text）雙層/巢狀搜尋；外層選管線、內層評估下游任務損失。

## 發現
（待補具體數字）per-instance 搜尋優於固定管線。

## 啟發
- **被啟發**：[[P_draft_AutoML_HPO]]、[[P_draft_BCI_Bayesian_Opt]] — 搜尋方法
- **啟發了**：[[P_draft_LLM_Preproc_Agent]] — 把搜尋換成 EEG + FOOOF + LLM agent