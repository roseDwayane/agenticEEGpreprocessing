---
schema_version: "1.0"
id: D_draft_Are_LLM_HPO_Gains_Real
type: line
relation_type: debate
name: "Debate: Are LLM Gains in HPO Real?"
description: "LLM 作為跨任務先驗有效（可重現）vs. 預算對齊後增益化約為一個好預設——兩派各對一半，取決於協議與骨幹規模"
endpoints: [P_draft_LLAMBO, P_draft_Budget_Matched_Protocol, P_draft_Backbone_Capacity_Threshold, P_draft_Seeded_Baseline]
tags: [llm, hpo, debate, evaluation]
status: draft
created: 2026-08-15
updated: 2026-08-15
related_planes: [F_draft_T1_LLM_for_AutoML]
related_body: [B_draft_llm-warmstart-cash-mi-eeg-pipelines]
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-1"
    - "step7_gap_analysis.md#gap_003"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# Debate: LLM 在 HPO 中的增益是真的嗎？

## 爭論軸

LLM 進入 HPO 迴圈（warm-start／surrogate／candidate generation）帶來的加速，是真實的知識注入效果，還是評估協議的假象？

## 正方：LLM 是有效的跨任務先驗

- [[P_draft_LLAMBO]] (LiuEtAl2024)：LLM warm-start 是各注入環節中最穩健的增益來源。
- RychertEtAl2025 用開源 Llama-3.1-70B **獨立重現**了 LLAMBO 的 contextual warm-start 效果——不是單一實驗室的煉金術。
- XuEtAl2026a (CoFEH)：LLM-FE 條件化 SMAC 在 CASH 情境平均排名 1.75、最高 45.1% 誤差下降；FE meta-features 把 surrogate Spearman 相關從 0.587 提升到 0.691。

## 反方：預算對齊後增益蒸發

- [[P_draft_Budget_Matched_Protocol]] (RodriguesEtAl2026)：7 個 LLM 的「warm-start」實為模型呼叫前的固定預設配置（88.7% CV 完全相同）；LLM 本身貢獻 +0.40pp CV、−0.01pp test（p=0.92），12 次評估內被 seeded 傳統搜尋反超。
- [[P_draft_Backbone_Capacity_Threshold]] (RychertEtAl2025 同一論文的另一面)：sub-70B 骨幹輸出畸形或無相關——效果對模型規模高度敏感。
- SrinivasanMenzies2026：LLM 優勢在低維度以外消失。

## 證據對比的關鍵

兩派其實在不同協議下各自正確：正方測的是「先驗品質」（LLM 知道好起點），反方測的是「先驗形狀的邊際貢獻」（扣掉好起點後還剩多少）。[[P_draft_Seeded_Baseline]] 是把兩者分開的手術刀。

## 解套提案

任何 LLM×HPO 宣稱應同時報告：(1) budget-matched anytime 曲線、(2) seeded 傳統對照、(3) 骨幹規模消融。本 session 的 H3 正是把這三者內建進 EEG 情境。

## 對 Cary 的意義

這是全論文最重要的一張防彈衣卡。實驗設計若不內建 seeded 對照，正方結果會被反方文獻一擊即潰；反之，若 warm-start 在 seeded 對照下仍勝出，這將是此 debate 在新任務家族上的有力新證據。
