---
id: A_draft_Caveat_x_Aperiodic
type: line
relation_type: analogy
endpoints: [P_draft_Aperiodic_Artifact_Risk, P_draft_Aperiodic_Signal]
status: draft
created: 2026-06-20
updated: 2026-06-20
provenance:
  session_id: "20260620"
  source_files:
    - "step6_sota_review.md#cross-theme"
    - "step7_gap_analysis.md#GAP_004"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# Bridge: FOOOF metric caveats ↔ aperiodic-as-real-signal

## 結構映射表
| 指標警示這側 (Theme 2, Gerster) | 非週期即訊號這側 (Theme 3) |
|---|---|
| FOOOF 會在某些情境誤估非週期 | 非週期 exponent/offset 是真實神經標記 |
| 「小心非週期估計」 | 「別把非週期當雜訊丟掉」 |
| 方法失效模式 | 生理效度 |

## 為什麼是同構
兩者從不同方向指向同一設計約束：非週期成分既可能被 FOOOF 誤估（方法面），又本身是真實訊號（生理面）。因此天真的「最小化非週期」SNR 在兩個層面都危險。

## 映射的極限
這不是兩個獨立問題的巧合——它們合起來定義了 [[P_draft_Aperiodic_Artifact_Risk]]：獎勵必須同時（a）對 FOOOF 失效穩健、（b）不懲罰生理性非週期。

## 對 Cary 的意義
這是計畫的主要效度風險（GAP_004），且只有把 Theme 2 與 Theme 3 對讀才會浮現——你原始三點構想沒提到。若忽略，FOOOF-SNR 會悄悄獎勵破壞真實訊號的預處理。