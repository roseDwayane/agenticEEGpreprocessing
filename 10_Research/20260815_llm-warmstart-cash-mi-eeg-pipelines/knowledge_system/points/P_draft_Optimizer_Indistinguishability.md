---
schema_version: "1.0"
id: P_draft_Optimizer_Indistinguishability
type: point
name: "CASH Optimizer Indistinguishability"
description: "Across 114 datasets, mature CASH optimizers are statistically indistinguishable (mean differences <1.9%, random search not worse), implying optimizer gains are context-dependent rather than universal."
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [benchmark, cash, evaluation]
domain: [AI]
field: [AutoML]
status: draft
created: 2026-08-15
updated: 2026-08-15
parent:
parts: []
depends_on: []
caused_by: []
causes: []
related_lines: []
related_planes: []
related_body: [B_draft_llm-warmstart-cash-mi-eeg-pipelines]
source: "ZollerHuber2019"
year: 2019
claim_type: empirical
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-3"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# CASH Optimizer Indistinguishability

> **核心主張**：在 114 個資料集的基準上，各成熟 CASH 最佳化器的最終準確率在統計上無法區分（平均差異 <1.9%，random search 不遜色）——最佳化器帶來的增益是情境依賴的，而非普遍成立。

## 來源
- 作者：Zöller, M., & Huber, M. F. / 年份：2019 / 出處：*Journal of Artificial Intelligence Research*（Benchmark and survey of automated machine learning frameworks） / citation key: `ZollerHuber2019`

## 目的
回答「CASH 最佳化器的選擇到底有多重要？各家 AutoML 系統宣稱的優勢在公平比較下還剩多少？」的評估方法學問題。

## 核心主張（展開）
這篇 benchmark + survey 對 CASH 最佳化器（114 個資料集）與完整 AutoML 框架（73 個資料集）做了系統性比較，發現多數資料集上各 CASH 最佳化器統計上無法區分：平均準確率差異小於 1.9%，且 random search 並不遜色；各完整框架之間差異也僅在 2.2% 以內。更引人注目的是，單純的 CASH solver 在 48% 的共同資料集上以約五分之一的時間（12 分鐘 vs. 1 小時）勝過完整 AutoML 框架。這與 GijsbersEtAl2019（無一致最佳系統、部分資料集上無框架勝過調參 random forest）互相印證，共同構成「漸近最佳化器選擇不是決定性槓桿」的證據線；step6 綜述據此推論：對受試者特定 EEG pipeline 而言，決定性的槓桿是「加速抵達良好區域」（先驗/暖啟動作用之處），而非最佳化器本身。

## 方法
以統一協定重跑多個 CASH 最佳化器與 AutoML 框架，控制時間預算與資料集集合後比較最終準確率並做統計檢定；同時對基準效度提出方法學批評——合成函數被認為不適用於 CASH 評測。細部實驗協定 (待補——no-fulltext)。

## 發現
- 114 個資料集上，CASH 最佳化器間平均準確率差異 <1.9%；random search 不遜色。
- 完整 AutoML 框架間差異在 2.2% 以內。
- CASH solver 在 48% 的共同資料集上以 12 分鐘勝過跑 1 小時的完整框架。
- 方法學主張：合成函數不適合作為 CASH 基準。

## 啟發
- **被啟發**：(無上游 Point 卡；屬對 CASH 系統文獻的稽核性回應)
- **啟發了**：[[P_draft_Budget_Matched_Protocol]] — 「無差異」結論確立了任何宣稱增益（含 LLM warm-start）都必須在預算對齊、種子化對照的協定下驗證的必要性論證
