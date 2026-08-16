---
schema_version: "1.0"
id: D_Does_The_Optimizer_Matter
type: line
relation_type: debate
name: "Debate: Does the CASH Optimizer Matter?"
description: "分解式搜尋隨空間增大優勢擴大 vs. 嚴謹協議下最佳化器統計無差異——增益是情境依賴的"
endpoints: [P_Search_Space_Decomposition, P_Rising_Bandits, P_Optimizer_Indistinguishability]
tags: [cash, benchmarking, debate]
status: active
created: 2026-08-15
updated: 2026-08-15
related_planes: [F_T3_CASH_Systems_Benchmarks]
related_body: [B_llm-warmstart-cash-mi-eeg-pipelines]
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-3"
    - "step6_sota_review.md#cross-theme-analysis"
    - "step7_gap_analysis.md#gap_003"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# Debate: CASH 最佳化器究竟重不重要？

## 爭論軸

同樣的評估預算下，「用哪個最佳化器搜」對最終結果的影響，究竟是決定性的還是統計噪音？

## 正方：結構化搜尋有真實增益（規模情境）

- [[P_Rising_Bandits]] (LiEtAl2020a)：候選演算法 1→16 擴增時保持 95.02% 準確率，SMAC 退化到 93.63%；平均省 ~12.6× 試驗。
- [[P_Search_Space_Decomposition]] (LiEtAl2021b)：VolcanoML 對 auto-sklearn 的排名差距隨超參數 20→100 從 ~0.1 擴大到 ~1.9。
- 共同主張：**空間越大、條件結構越深，結構化搜尋越重要**。

## 反方：嚴謹協議下差異消失（一般情境）

- [[P_Optimizer_Indistinguishability]] (ZollerHuber2019)：114 資料集上各 CASH 最佳化器統計上無法區分（平均差 <1.9%），random search 不劣；12 分鐘 CASH 在 48% 共同資料集上勝過 1 小時 AutoML 框架。
- EricksonEtAl2020 (AutoGluon)：完全不做 CASH、只靠 stacking，平均排名 1.84 對 auto-sklearn 的 3.81。

## 解套提案（本 session 的立場）

增益是**情境依賴**的：大型條件式空間 × 緊預算 × 昂貴評估 = 結構化搜尋的主場——而受試者特定 EEG 管線正是這個情境。裁決武器是 [[P_Anytime_Performance]] 與 [[P_Budget_Matched_Protocol]]：不在這種協議下報告的最佳化器比較不可信。

## 對 Cary 的意義

這條對立線決定了論文的自我防衛結構：H1 的宣稱必須附帶 anytime 曲線與 random-search 下限，否則反方文獻就是現成的審稿人攻擊稿。同時它也是機會——若在 EEG 情境證明分解式＋暖啟動的增益顯著，就是對這場 debate 的一個新資料點。
