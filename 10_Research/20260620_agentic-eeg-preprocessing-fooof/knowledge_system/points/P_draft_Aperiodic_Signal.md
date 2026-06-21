---
schema_version: "1.0"
id: P_draft_Aperiodic_Signal
type: point
name: "Aperiodic component as validated neural signal"
description: "The 1/f aperiodic exponent/offset is a real, behaviorally and clinically meaningful neural marker, not mere noise"

asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [needs_fulltext]

domain: [Neuroscience]
field: [EEG]

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

source: "LendnerEtAl2020"
year: 2020
claim_type: empirical

provenance:
  session_id: "20260620"
  source_files:
    - "step6_sota_review.md#theme-3"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# Aperiodic component as validated neural signal

> **核心主張**：非週期 1/f 成分（exponent/offset）攜帶真實、與行為及臨床相關的神經資訊，不能僅當雜訊濾除。

## 來源
- 作者：Lendner 等（及 Hill, Merkin, Thuwal 等多篇）
- 年份：2020–2022
- 出處：step6 theme-3
- citation key: `LendnerEtAl2020`

## 目的
回答「非週期成分是雜訊還是訊號？」——為 FOOOF-SNR 設計提供關鍵約束。

## 核心主張（展開）
1/f 斜率區分清醒/麻醉/睡眠（LendnerEtAl2020）；exponent/offset 隨發展與老化系統性變化（HillEtAl2022, MerkinEtAl2022）；與認知不同面向連結（ThuwalEtAl2021）。

## 方法
（待補）多資料集靜息態 EEG，以 FOOOF 取 exponent/offset，與狀態/年齡/認知做迴歸。

## 發現
（待補）非週期成分跨實驗室與模態可重現地連結到喚醒、年齡、E/I 平衡。

## 啟發
- **被啟發**：[[P_draft_FOOOF]] — 提供量化非週期的工具
- **啟發了**：[[P_draft_Aperiodic_Artifact_Risk]] — 因其為真實訊號，天真 SNR 有破壞它的風險