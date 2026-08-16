---
schema_version: "1.0"
id: E_draft_Knowledge_Injection_Level_Ascent
type: line
relation_type: evolution
name: "Knowledge Injection Level Ascent (2015-2026)"
description: "BO 知識注入口從初始設計層逐步上移：meta-feature 初始化 → surrogate 遷移 → acquisition 先驗 → 語言層引出"
endpoints: [P_draft_MI_SMAC, P_draft_Two_Stage_Transfer_Surrogate, P_draft_TransBO, P_draft_PiBO, P_draft_LLM_Prior_Elicitation]
tags: [warm-starting, transfer-hpo, evolution]
status: draft
created: 2026-08-15
updated: 2026-08-15
related_planes: [F_draft_T2_Warmstart_Transfer_BO, F_draft_T1_LLM_for_AutoML]
related_body: [B_draft_llm-warmstart-cash-mi-eeg-pipelines]
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-2"
    - "step6_sota_review.md#cross-theme-analysis"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# 知識注入口的層級上移 (2015–2026)

## 完整鏈條

[[P_draft_MI_SMAC]] (2015, 初始設計層) → [[P_draft_Two_Stage_Transfer_Surrogate]] (2016, surrogate 層) → [[P_draft_TransBO]] (2022, 兩階段可學聚合) → [[P_draft_PiBO]] (2022, acquisition 先驗層) → [[P_draft_LLM_Prior_Elicitation]] (2024–, 語言層)

## 每一步的機制

1. **MI-SMAC (2015)**：知識 = 過往資料集的最優配置；注入口 = 初始設計（前幾個評估點）。限制：只影響起點，不影響後續搜尋方向。
2. **TST (2016)**：把知識搬進 surrogate——以任務相似度加權組合 source surrogates。改良：全程引導搜尋。新問題：surrogate 層遷移對任務相似度錯估敏感。
3. **TransBO (2022)**：兩階段可學權重，緩解 source 品質不齊的問題。
4. **πBO (2022)**：退回更輕的注入口——acquisition function 上乘一個「會衰減的先驗」。關鍵洞察：注入口越輕、越晚介入，錯誤知識的傷害越可控（wrong-prior 可證明地恢復）。HvarfnerEtAl2026 進一步顯示 surrogate 層遷移有結構性偏誤，強化了「先驗層 > surrogate 層」的趨勢。
5. **LLM Prior Elicitation (2024–)**：知識來源從「結構化搜尋歷史」擴展到「語言化領域知識」；LLM 引出的分布餵進 πBO 式機制（TopalisEtAl2025 的 LLM+RAG-elicited Dirichlet 即此線最新節點）。

## 反例與例外

- [[P_draft_Budget_Matched_Protocol]] (RodriguesEtAl2026)：預算對齊下，語言層注入的增益可能化約為初始設計層的一個好預設——暗示層級上移未必單調增值，每一步都需 seeded 對照裁決。
- [[P_draft_Backbone_Capacity_Threshold]]：語言層注入依賴 ≥70B 骨幹，注入口越高、對知識源品質越敏感。

## 對 Cary 的意義

這條演化線就是 Related Work 的敘事骨架：本研究站在鏈條末端，把「受試者搜尋歷史」（結構化）與「LLM 先驗」（語言化）兩種知識源餵進同一個 πBO 式注入口，在 EEG subjects-as-tasks 的新任務家族上做首次對決（H1/H3）。
