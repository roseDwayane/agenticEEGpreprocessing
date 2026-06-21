---
schema_version: "1.0"
id: P_draft_FOOOF
type: point
name: "FOOOF / specparam (parameterizing neural power spectra)"
description: "Models a power spectrum as a 1/f aperiodic background plus Gaussian periodic peaks"

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

source: "DonoghueEtAl2020b"
year: 2020
claim_type: methodological

provenance:
  session_id: "20260620"
  source_files:
    - "step5_full_text/DonoghueEtAl2020b.md"
    - "step6_sota_review.md#theme-2"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# FOOOF / specparam (parameterizing neural power spectra)

> **核心主張**：神經功率譜可被分解為一個 1/f 樣非週期背景（offset + exponent）加上少數高斯形週期峰。

## 來源
- 作者：Donoghue, Thomas 等
- 年份：2020（Nature Neuroscience）
- 出處：step5 DonoghueEtAl2020b.md（abstract-only）
- citation key: `DonoghueEtAl2020b`

## 目的
提供有原則地分離「振盪」與「1/f 背景」的方法，避免傳統 band-power 把兩者混淆。

## 核心主張（展開）
功率譜 = 非週期成分（直線於 log-log，offset 與 exponent 參數化）+ N 個高斯峰（中心頻率、功率、頻寬）。前身為 HallerEtAl2018。是當前主流的頻譜參數化框架。

## 方法
（abstract-only, 待補全文細節）以最小平方迭代擬合非週期背景與週期峰；無需先驗 band 界限。

## 發現
（abstract-only, 待補）被廣泛採用為神經頻譜參數化標準；引用數極高（~2350）。

## 啟發
- **被啟發**：[[P_draft_IRASA]] — 平行的替代分解法
- **啟發了**：[[P_draft_FOOOF_SNR]] — 將分解用作預處理目標；[[P_draft_Aperiodic_Signal]] — 非週期成分作為真實訊號的研究