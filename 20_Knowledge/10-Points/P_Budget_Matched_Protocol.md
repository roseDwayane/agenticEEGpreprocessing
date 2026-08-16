---
schema_version: "1.0"
id: P_Budget_Matched_Protocol
type: point
name: "Budget-Matched Evaluation Protocol"
description: "對齊評估預算並加入 seeded 對照後，LLM 顧問的表面增益化約為一個固定首配置：LLM 提案僅 +0.40pp CV、held-out test −0.01pp（p=0.92），且在 12 次評估內被 seeded 古典搜尋反超。"
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [llm, hpo, evaluation]
domain: [AI, Neuroscience]
field: [BCI]
status: active
created: 2026-08-15
updated: 2026-08-15
parent:
parts: []
depends_on: [P_MI_SMAC]
caused_by: []
causes: []
related_lines: []
related_planes: []
related_body: [B_llm-warmstart-cash-mi-eeg-pipelines]
source: "RodriguesEtAl2026"
year: 2026
claim_type: methodological
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-1"
    - "step5_full_text/RodriguesEtAl2026.md"
    - "step8_hypothesis_specification.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# Budget-Matched Evaluation Protocol

> **核心主張**：只有在「所有方法消耗相同評估次數＋多種子成對統計＋seeded 對照」的預算對齊協議下，LLM 顧問的宣稱增益才能被正確歸因——在此協議下，其著名的暖啟動首點其實是模型呼叫前就評估的固定預設配置，LLM 自身提案僅 +0.40 pp CV、held-out test −0.01 pp（p = 0.92），且 seeded 古典搜尋在 12 次評估內反超。

## 來源
- 作者：Rodrigues, C., Vas, O., DCosta, I. A., & Prabhakaran, N. K. / 年份：2026 / 出處：arXiv preprint（arXiv:2606.21641）/ citation key: `RodriguesEtAl2026`

## 目的
建立一個能拆解「LLM 顧問到底貢獻了什麼」的稽核協議：預算對齊、多種子成對檢定、bootstrap 信賴區間，以及決定性的 seeded 對照——把先前文獻「宣稱多於量測」的暖啟動敘事變成可否證的量化問題。

## 核心主張（展開）
協議包含四項要素：(1) 單一共享搜尋空間 S（四個模型家族），所有方法（random search、Optuna-TPE、GP-BO、successive halving、LLM 顧問）在其中以對齊的評估次數比較；(2) 8 個 PMLB 資料集 × 5 個種子 = 40 個成對單位，配 paired t / Wilcoxon 檢定與 20,000 次 bootstrap 95% CI；(3) 把顧問迴圈中「模型呼叫前就評估的固定預設配置」單獨列為 Default (fixed) 基線；(4) 把同一預設配置授予古典搜尋（seeded control），隔離 LLM 的邊際貢獻。結果是通縮性的：預設種子單獨即達 88.7% 平均 best-CV，且在 7 個 LLM 顧問（5 家供應商、nano-to-frontier）間相同至 0.01 pp 內——首點根本不是 LLM 輸出。此協議同時保留了真實的 LLM 特有現象（vehicle 任務的探索失敗；rule-based confidence filter 的操作性價值），示範「嚴格協議不等於全盤否定」。step6 Theme 1 將其定位為 2023–2026「從熱情到冷靜稽核」轉折的代表；step8 則把整套協議內建為本研究的評估方法學（GAP_003）。

## 方法
每資料集每種子做 70/30 分層切分；目標函數為訓練集 3-fold CV accuracy，報告值為 held-out test accuracy；random/TPE/GP-BO 跑 40 次試驗、LLM-OptFlow 跑 12 次迭代（預設種子計為第 1 次評估）；記錄每次評估後的 running-best CV（budget curve）；跨方法比較全部在 40 個 (task, seed) 成對單位上進行；released harness 附可重現全部統計量的 script（significance.py）。

## 發現
- 預設種子單獨：88.7% 平均 best-CV（budget=1），高於 random 83.7%、TPE 86.7%、GP-BO 86.3%；7 個 LLM 間相同至 0.01 pp。
- LLM 邊際貢獻：首個真提案 +0.22 pp（95% CI [0.09, 0.42], p = 0.02）；12 次評估全程 +0.40 pp CV（[0.22, 0.62], p < 0.001）；held-out test −0.01 pp（[−0.22, 0.19], p = 0.92）。
- Seeded 對照（random search，精確對照）：顧問領先僅 +0.20 pp @2 evals（p = 0.03），5 次評估內消失（−0.09, n.s.），12 次評估時落後 −0.37 pp（[−0.82, −0.02]）；seeded TPE/GP-BO 下落後 −0.60/−0.48 pp。
- 未 seeded 的古典搜尋也在 12 次評估追平、40 次反超（TPE +0.62、random +0.76、GP-BO +0.61 pp，皆 p ≤ 10⁻⁴）。
- vehicle 探索失敗：古典方法 +6.5 至 +9.1 pp（random +9.06, p < 0.01），顧問停在預設（73.3% vs 預設 73.5%）；7 模型中僅 2 個（Sonnet 4.6, Qwen3.7-max）逃離。
- Confidence filter：對抗性提案下 reject rate 0.38（corruption 0.40），省約 33% 浪費計算時間（13.2 → 8.8 s），準確率不變。

## 啟發
- **被啟發**：[[P_Optimizer_Indistinguishability]] — 「成熟最佳化器最終差異極小」的虛無結果使歸因問題（增益來自誰）成為關鍵，本協議即為此打造；亦回應 LLAMBO 一系（LiuEtAl2024）未隔離預設種子的暖啟動敘事。
- **啟發了**：[[P_Seeded_Baseline]] — 協議中決定性的對照臂被抽出為獨立方法學工具；[[B_llm-warmstart-cash-mi-eeg-pipelines]] — 本研究 H3 直接把「seeded 冷啟動 12 次內反超」設為待檢驗的虛無假說，F6（預算對齊評估框架）全面採用本協議（所有條件消耗相同評估次數、anytime 曲線、成對 Wilcoxon）。
