---
id: A_draft_Search_x_EEG
type: line
relation_type: analogy
endpoints: [P_draft_Personalized_Pipeline_Search, P_draft_PREP_Pipeline]
status: draft
created: 2026-06-20
updated: 2026-06-20
provenance:
  session_id: "20260620"
  source_files:
    - "step6_sota_review.md#theme-5"
    - "step7_gap_analysis.md#GAP_002"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# Bridge: pipeline search ↔ EEG preprocessing action space

## 結構映射表
| 搜尋這側 (Theme 5) | EEG 預處理這側 (Theme 1) |
|---|---|
| AutoML / MartinezEtAl2023 的管線搜尋 | PREP/HAPPE/Autoreject/ICLabel 的可調參數 |
| 目標：任務損失 | 目標：FOOOF-SNR（本計畫換掉） |
| 通用表格資料 | 原始 EEG 訊號 |

## 為什麼是同構
預處理管線 = 一串帶參數的操作；選參數 = 在組合空間中搜尋。MartinezEtAl2023 已證明逐實例搜尋可行；EEG 管線（[[P_draft_PREP_Pipeline]] 等）暴露的參數正是可搜尋對象。

## 映射的極限
EEG 有特殊性：montage 異質、artifact 結構特殊、品質目標需用頻譜（非任務損失）。直接套 AutoML 不夠——這正是 [[P_draft_Greedy_Oracle]] 與 [[P_draft_LLM_Preproc_Agent]] 要補的。

## 對 Cary 的意義
這條 bridge 是 GAP_002/003 的基礎：把成熟的「搜尋/agent」機制接到「EEG 預處理動作空間」上，以 FOOOF-SNR 為目標。MartinezEtAl2023 證明搜尋半邊可行，EEG + 頻譜目標是空白。