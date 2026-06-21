---
schema_version: "1.0"
id: P_draft_AutoML_HPO
type: point
name: "AutoML / hyperparameter optimization as pipeline search"
description: "Frames algorithm + hyperparameter selection as a single (often Bayesian) search problem"

asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [needs_fulltext]

domain: [AI]
field: [AutoML]

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

source: "FeurerEtAl2015"
year: 2015
claim_type: methodological

provenance:
  session_id: "20260620"
  source_files:
    - "step6_sota_review.md#theme-5"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# AutoML / hyperparameter optimization as pipeline search

> **核心主張**：把模型與超參數選擇框定為單一搜尋問題（Auto-WEKA、Auto-sklearn），是「把預處理當可搜尋問題」的範本。

## 來源
- 作者：Feurer, Matthias 等（及 Thornton 2012）
- 年份：2015（NeurIPS, Auto-sklearn）
- 出處：step6 theme-5
- citation key: `FeurerEtAl2015`

## 目的
自動化 ML 管線建構，免除人工試錯。

## 核心主張（展開）
以貝氏最佳化在巨大組合空間中聯合選演算法與超參數；Auto-sklearn 加上 meta-learning。NeutatzEtAl2022 / SiddiqiEtAl2023 延伸到資料清理決策——最接近「最佳化預處理」的類比。

## 方法
（待補）貝氏最佳化 / SMAC + meta-learning warm-start + ensemble。

## 發現
（待補）在多資料集上常優於標準選擇/調參。

## 啟發
- **被啟發**：（Auto-WEKA）
- **啟發了**：[[P_draft_Personalized_Pipeline_Search]] — 把搜尋用於預處理；[[P_draft_Greedy_Oracle]] — 古典搜尋 baseline