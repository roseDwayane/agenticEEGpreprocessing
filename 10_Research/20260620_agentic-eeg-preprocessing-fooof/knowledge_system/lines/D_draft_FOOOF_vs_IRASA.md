---
id: D_draft_FOOOF_vs_IRASA
type: line
relation_type: debate
endpoints: [P_draft_FOOOF, P_draft_IRASA]
status: draft
created: 2026-06-20
updated: 2026-06-20
provenance:
  session_id: "20260620"
  source_files:
    - "step6_sota_review.md#theme-2"
    - "step5_full_text/GersterEtAl2022.md"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# Debate: FOOOF vs IRASA for periodic/aperiodic separation

## 爭論軸
用哪種方法把功率譜分離為週期（振盪）與非週期（1/f）成分，且哪一種對預處理與擬合選擇更穩健——直接影響 FOOOF-SNR 指標的可靠度。

## 兩派主張
- **FOOOF / specparam**（[[P_draft_FOOOF]]）：以 log-log 直線 + 高斯峰參數化；直觀、被廣泛採用，但對擬合範圍、knee、重疊峰敏感。
- **IRASA**（[[P_draft_IRASA]]）：以不規則重採樣分離碎形與振盪；不需先驗峰模型，但對寬頻振盪與取樣設定敏感。

## 證據對比
GersterEtAl2022 在 EEG/MEG/LFP 三資料集系統比較兩法，以模擬參數量化各自的參數化誤差，指出特定頻譜特徵（過窄擬合範圍、重疊峰、knee/bend）會妨礙分離，並評估計算成本。結論是兩法各有失效情境，需依資料選擇與把關。

## 解套提案
對本計畫：固定擬合範圍、明確 knee 處理、以擬合優度（R²/error）把關；必要時兩法交叉檢驗。此即 [[P_draft_Aperiodic_Artifact_Risk]] 的一部分。

## 對 Cary 的意義
FOOOF-SNR 指標的效度取決於分解的正確性。這場辯論不是學術細節，而是 GAP_001 能否成立的前置條件——指標與被評估對象（預處理）耦合，分解一旦失真，整個 reward 失效。