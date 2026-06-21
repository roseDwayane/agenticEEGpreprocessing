---
id: F_draft_T3_Aperiodic_Signal
type: plane
name: "Aperiodic Activity as a Validated Neural Signal"
member_points: [P_draft_Aperiodic_Signal, P_draft_Aperiodic_Artifact_Risk]
adjacent_planes: [F_draft_T2_FOOOF_Spectral]
status: draft
created: 2026-06-20
updated: 2026-06-20
provenance:
  session_id: "20260620"
  source_files:
    - "step6_sota_review.md#theme-3"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# Plane T3: Aperiodic Activity as a Validated Neural Signal / 非週期活動作為已驗證的神經訊號

## Q1 解決什麼問題？
非週期 1/f 成分是雜訊還是訊號？此面提供 FOOOF-SNR 的**外部效度論證**與**關鍵設計約束**。

## Q2 核心概念有哪些？
- [[P_draft_Aperiodic_Signal]] — 非週期是真實、有行為/臨床意義的神經訊號
- [[P_draft_Aperiodic_Artifact_Risk]] — 因其為真實訊號，天真 SNR 有破壞它的風險

## Q3 概念之間關係？
非週期效度（Aperiodic_Signal）直接導出獎勵設計風險（Aperiodic_Artifact_Risk）；後者與 T2 的方法失效模式合流於 [[A_draft_Caveat_x_Aperiodic]]。

## Q4 常用方法？
多資料集靜息態 EEG → FOOOF 取 exponent/offset → 與狀態（喚醒/麻醉/睡眠）、年齡、E/I、認知做迴歸/分類。

## Q5 常見錯誤？（verbatim from step7 GAP_004）
> 天真的 FOOOF-SNR（「最大化週期、最小化非週期」）有獎勵破壞正當非週期神經訊號的風險，因非週期成分本身具生理意義。
> 無工作定義預處理品質獎勵該如何區分真實非週期生理（E/I、喚醒）與非週期污染（寬頻 EMG、漂移、通道雜訊）。

## 與其他 Plane 的關係
T3 是 T2 的「煞車」：T2 想用非週期當雜訊，T3 提醒它是訊號。兩者張力是計畫核心科學設計問題（GAP_004）。
