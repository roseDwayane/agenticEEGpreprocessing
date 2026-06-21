---
id: F_draft_T4_Preproc_Quality
type: plane
name: "Preprocessing Sensitivity & Objective Quality Assessment"
member_points: [P_draft_Preproc_Sensitivity, P_draft_Delorme_Quality_Metric]
adjacent_planes: [F_draft_T1_EEG_Preprocessing_Pipelines, F_draft_T2_FOOOF_Spectral]
status: draft
created: 2026-06-20
updated: 2026-06-20
provenance:
  session_id: "20260620"
  source_files:
    - "step6_sota_review.md#theme-4"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# Plane T4: Preprocessing Sensitivity & Objective Quality Assessment / 預處理敏感度與客觀品質評估

## Q1 解決什麼問題？
不靠下游任務準確率，如何客觀判斷預處理好壞？此面是整個計畫的**直接動機**與最接近前作。

## Q2 核心概念有哪些？
- [[P_draft_Preproc_Sensitivity]] — 預處理選擇大幅且不一致地改變結果
- [[P_draft_Delorme_Quality_Metric]] — 客觀（但需條件）品質指標；多數自動步驟無益或有害

## Q3 概念之間關係？
敏感度（為何選擇重要）→ 客觀指標（如何判斷）→ FOOOF-SNR（無標籤的下一步）。橋接於 [[A_draft_Quality_x_Spectral]]。

## Q4 常用方法？
跨管線比較下游指標（RobbinsEtAl2020）；條件對比的顯著通道百分比（Delorme2023）；逐步手動比較與量化指標（CoelliEtAl2023）。

## Q5 常見錯誤？（verbatim from step7 GAP_001）
> 文獻評估預處理不是靠下游任務準確率，就是靠需標註事件的條件對比統計（Delorme2023），留下無標籤情形未解。
> 「更多校正」並非單調更好——多數自動校正步驟不是沒幫助就是有害。

## 與其他 Plane 的關係
T4 橋接 T1（管線）與 T2（指標）：已在問「如何客觀評判預處理」，但無一用 FOOOF、無一搜尋或學習參數。
