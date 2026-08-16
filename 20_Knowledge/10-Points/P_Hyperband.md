---
schema_version: "1.0"
id: P_Hyperband
type: point
name: "Hyperband"
description: "Hyperband recasts HPO as a pure resource-allocation problem, hedging successive halving across multiple brackets to achieve 5-30x speedups over Bayesian optimization."
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [multi-fidelity, bandits, hpo]
domain: [AI]
field: [AutoML]
status: active
created: 2026-08-15
updated: 2026-08-15
parent:
parts: []
depends_on: []
caused_by: []
causes: []
related_lines: []
related_planes: []
related_body: [B_llm-warmstart-cash-mi-eeg-pipelines]
source: "LiEtAl2016"
year: 2016
claim_type: methodological
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-3"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# Hyperband

> **核心主張**：把 HPO 視為純粹的資源分配問題——以 successive halving 提早終止劣勢組態、並以多個 bracket 對沖「探索廣度 vs. 單點資源深度」的取捨——即可在不建模目標函數的情況下比 BO 快 5–30 倍。

## 來源
- 作者：Li, L., Jamieson, K., DeSalvo, G., Rostamizadeh, A., & Talwalkar, A. / 年份：2016 / 出處：*arXiv (Cornell University)*, arXiv:1603.06560（後刊於 JMLR） / citation key: `LiEtAl2016`

## 目的
回答「當單次組態評估昂貴（如深度學習訓練）時，如何在固定總預算內盡可能多地評估組態」的問題——與其更聰明地挑點（BO 路線），不如更聰明地分配資源。

## 核心主張（展開）
Hyperband 是 bandit-based 的多保真方法：每個組態可用部分預算（epochs、資料子集等）先做廉價的近似評估，successive halving 反覆保留表現前段的組態並倍增其資源。因為「該給每個組態多少起始資源」本身無法先驗決定，Hyperband 以多個 bracket 同時執行不同的「組態數 × 單位資源」配置作為對沖（hedging），使其對問題特性穩健。它完全不擬合 surrogate model，因此無 BO 的建模成本與高維退化問題；其增益集中於低預算階段，大預算下 black-box 方法會趕上（EggenspergerEtAl2021）。此設計成為後續 BOHB（Hyperband + TPE）、MFES-HB、ASHA（Optuna）等混合方法的基座。

## 方法
（依 step6 綜述層級的描述）將 HPO 形式化為 non-stochastic infinite-armed bandit 的資源分配問題；內層為 successive halving 淘汰迴圈，外層以 bracket 網格掃過不同的初始資源配置。評測涵蓋深度學習與 kernel 方法的 HPO 任務。細部演算法參數與理論保證 (待補——no-fulltext)。

## 發現
- 比主流 BO 方法快 5–30 倍；對其競爭方法組合帶來一個數量級的加速（step6 Key Results）。
- 位置界定：HPOBench 大規模評測顯示只有 2/4 的多保真方法能勝過純 Hyperband（EggenspergerEtAl2021），顯示其作為基線之強。
- BOHB 證明在 Hyperband 上疊加 model-based 取樣可以最快 100 倍達到 Hyperband 的最終品質（FalknerEtAl2018）——即 Hyperband 的弱點在後期收斂而非前期。
- 其作者群被 step6 綜述點名（LiEtAl2016, LiEtAl2020a, LiEtAl2022a）一再將 meta-learned warm-starting 列為下一個加速槓桿／未來工作。

## 啟發
- **被啟發**：(無上游 Point 卡；源自 successive halving 與 bandit 文獻)
- **啟發了**：[[P_Rising_Bandits]] — 「bandit 淘汰 + 資源自適應分配」思想被移植到 CASH 的演算法選擇層，並改造為非定常遞增報酬
- **啟發了**：本研究的 warm-start 論證 — 其作者點名 warm-start 為未來工作，正是 LLM/跨受試者先驗注入所要填補的缺口
