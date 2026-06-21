---
id: A_draft_Quality_x_Spectral
type: line
relation_type: analogy
endpoints: [P_draft_Delorme_Quality_Metric, P_draft_FOOOF_SNR]
status: draft
created: 2026-06-20
updated: 2026-06-20
provenance:
  session_id: "20260620"
  source_files:
    - "step6_sota_review.md#theme-4"
    - "step7_gap_analysis.md#GAP_001"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# Bridge: objective preprocessing quality ↔ spectral signal/noise metric

## 結構映射表
| 客觀品質這側 (Theme 4) | 頻譜指標這側 (Theme 2) |
|---|---|
| Delorme：% 顯著通道（需條件/標籤） | FOOOF-SNR：週期/非週期比（無標籤） |
| 證明「需要客觀指標」 | 提供「可作為目標函數的指標」 |
| 任務對比 → 需 ≥2 條件 | 單筆靜息態即可計算 |

## 為什麼是同構
兩者都在回答同一問題：「不靠下游準確率，如何判斷預處理好壞？」Delorme 用條件對比、本計畫用頻譜分解；但目標、用途（評分預處理）、評估方式（與專家/oracle 一致性）結構相同。

## 映射的極限
Delorme 指標需標籤/事件，不適用無標籤靜息態；FOOOF-SNR 補上這一格，但代價是依賴頻譜分解的正確性（見 [[D_draft_FOOOF_vs_IRASA]]）。

## 對 Cary 的意義
這條 bridge 定義了 GAP_001 的座標：站在 Delorme 已證明的「需求」上，用 FOOOF 填補其「無標籤」空缺。是計畫最強的單一正當理由（兩個獨立文獻收斂於同一抱怨）。