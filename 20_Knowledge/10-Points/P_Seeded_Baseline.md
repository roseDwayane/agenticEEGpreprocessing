---
schema_version: "1.0"
id: P_Seeded_Baseline
type: point
name: "Seeded Baseline (Seeded Cold-Start)"
description: "把先驗的單一最佳配置作為冷啟動搜尋的首次評估——區分「一個好預設」與「先驗形狀引導搜尋」兩種增益來源的裁決性對照工具。"
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [hpo, control, warm-start]
domain: [AI, Neuroscience]
field: [BCI]
status: active
created: 2026-08-15
updated: 2026-08-15
parent:
parts: []
depends_on: [P_Budget_Matched_Protocol]
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

# Seeded Baseline (Seeded Cold-Start)

> **核心主張**：把先驗（LLM 或搜尋歷史）所指的單一最佳配置授予冷啟動搜尋、作為其第一次評估（seeded cold-start），是分離兩種增益來源的裁決性對照——若暖啟動優勢在此對照下消失，增益只是「一個好預設」；若仍存活，才證明先驗的「形狀」在首配置之外持續引導搜尋。

## 來源
- 作者：Rodrigues, C., Vas, O., DCosta, I. A., & Prabhakaran, N. K. / 年份：2026 / 出處：arXiv preprint（arXiv:2606.21641；文中稱之為 "the decisive control"）/ citation key: `RodriguesEtAl2026`

## 目的
提供一個最小而致命的消融：任何暖啟動系統的宣稱增益，都必須先扣除「其迴圈第一個點本來就是免費好配置」這一平凡解釋，剩餘的才可歸因於先驗對後續搜尋軌跡的引導。

## 核心主張（展開）
RodriguesEtAl2026 觀察到：LLM 顧問迴圈（如同先前的 LLM-HPO 系統）在任何模型呼叫之前，就先評估一個固定預設配置（RandomForest, n_estimators=100, max_depth=16, min_samples_leaf=1）作為首點——因此顧問的「+2 到 +5 pp 暖啟動」其實是「手選預設打敗一次冷抽樣」。對照的構造是把同一預設配置也給古典基線：對 random search 是精確對照（隨機抽樣獨立於種子）；對 model-based 最佳化器（TPE/GP-BO）是近似對照（enqueue 預設會改變後續抽樣，文中列為侷限）。在表格 HPO 上結果一面倒：seeded random search 讓顧問領先在 5 次評估內消失、12 次內反轉。方法學上的普遍意義是：seeded baseline 把「先驗品質」分解為「先驗均值（最佳單點）的品質」與「先驗形狀（分布）對探索的引導」兩個可分別檢定的成分——這正是 step8 RQ4 所要的消融。而 EEG 的大型條件式搜尋空間是否會像表格任務一樣被單點種子飽和，是未決的經驗問題，也因此成為本研究 H3 刻意存疑的檢定目標。

## 方法
在共享空間 S 與預算對齊協議（8 PMLB × 5 seeds、成對檢定、bootstrap CI）下：(1) 以 Default (fixed) 行單獨追蹤預設配置的表現；(2) seeded random search 以預設為第一次評估後照常均勻抽樣（精確對照）；(3) seeded TPE/GP-BO 以預設為 performance floor（近似對照）；(4) 比較顧問與各 seeded/未 seeded 基線在 2、5、12、40 次評估點的 running-best CV 差異。

## 發現
- 精確對照（seeded random search）：顧問僅 +0.20 pp @2 evals（p = 0.03）→ 5 次評估內歸零（−0.09 pp, n.s.）→ 12 次評估時 −0.37 pp（95% CI [−0.82, −0.02]）。
- 近似對照（seeded model-based）：對 TPE +0.14 pp @2（n.s.）→ 12 次評估時落後 TPE −0.60 pp、GP-BO −0.48 pp。
- 預設種子本身即 88.7% 平均 best-CV，跨 7 個 LLM 相同至 0.01 pp——首點增益與模型無關。
- 實務建議（文中原話方向）：「seed classical search with a sensible default」勝過付費把 LLM 放進迴圈——在表格 HPO 上，先驗形狀的引導未能存活此對照。

## 啟發
- **被啟發**：[[P_Budget_Matched_Protocol]] — seeded baseline 是該協議中決定性的對照臂，脫離預算對齊與成對統計則無從解讀；概念上亦上承 meta-learned 初始化傳統（FeurerEtAl2015，文中列為直接先例）。
- **啟發了**：[[B_llm-warmstart-cash-mi-eeg-pipelines]] — 本研究 H3 把 seeded cold-start 列為內建對照臂（H3-H0：seeded 後暖啟動優勢消失；H3-H1：anytime-AUC 優勢存活，證明先驗形狀在 EEG 條件式空間中有超越首配置的引導價值），並據此把「增益化約為強預設」列為風險評估中的已知混淆。
