---
schema_version: "1.0"
id: P_draft_PiBO
type: point
name: "πBO (Prior-injected Bayesian Optimization)"
description: "在 acquisition function 上乘一個隨迭代衰減的使用者先驗項 π(x)^(β/n)；純 default-centered 先驗即可在 ImageNette 帶來 12.5× time-to-accuracy 加速，且錯誤先驗可證明地恢復至標準 EI 收斂速率。"
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [prior-injection, bayesian-optimization, acquisition-function, warm-start]
domain: [AI]
field: [AutoML]
status: draft
created: 2026-08-15
updated: 2026-08-15
parent: P_draft_Warm_Start_Initialization
parts: []
depends_on: []
caused_by: []
causes: []
related_lines: []
related_planes: []
related_body: [B_draft_llm-warmstart-cash-mi-eeg-pipelines]
source: "HvarfnerEtAl2022"
year: 2022
claim_type: methodological
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-2"
    - "step5_full_text/HvarfnerEtAl2022.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# πBO (Prior-injected Bayesian Optimization)

> **核心主張**：把使用者對最優解位置的信念寫成機率分布 π(x)，以一行修改 α(x, D_n)·π(x)^(β/n) 乘進任意 myopic acquisition function，即可讓 BO 早期集中於先驗高機率區、且因先驗隨迭代衰減而在錯誤先驗下可證明地恢復標準收斂速率。

## 來源
- 作者：Hvarfner, C., Stoll, D., Souza, A. L. F., Lindauer, M., Hutter, F., & Nardi, L. / 年份：2022 / 出處：International Conference on Learning Representations（ICLR 2022）/ citation key: `HvarfnerEtAl2022`

## 目的
解決 vanilla BO 無法利用從業者「知道最優解大概在哪」的先驗信念的問題（此缺陷是實務上偏好手動調參的原因之一），並提供一個不需任何來源任務觀測、僅需一個信念分布、可搭配任意 surrogate 與多種 acquisition function 且有理論保證的注入機制。

## 核心主張（展開）
πBO 將先驗視為 acquisition function 上的權重：α_{π,n}(x, D_n) = α(x, D_n)·π(x)^(γ_n)，其中衰減指數 γ_n = β/n 隨迭代 n 趨近零，使先驗影響力由強轉弱、最終趨於均勻分布，點選擇逐漸回歸 vanilla BO；β 由使用者設定以表達對先驗的信心。初始設計亦自 π(x) 取樣（先驗眾數作為第一點）。理論上（Theorem 1）prior-weighted EI 的 loss 被 vanilla EI loss 乘上係數 C_{π,n} = (max π / min π)^(β/n) 所界定，故（Corollary 1）EI_π 與 EI 的 loss 漸近相等，繼承 Bull (2011) 的 EI 收斂速率——即最壞情況（完全錯誤的先驗）下的長期收斂不受影響，短期表現則由 π 與 β 控制。與 BOPrO、BOWS 等先前方法相比，πBO 更簡單、不綁定特定 acquisition function、且有收斂保證。

## 方法
三組實驗均以 Gaussian 先驗、50 次迭代的實務預算（β = N/10）進行：(1) 穩健性：在 Branin（2D）與 Profet surrogate 任務 FC-Net（6D）、XGBoost（8D）上構造 strong / weak / wrong 三種品質的先驗（wrong 為集中於最差區域的窄分布），比較 πBO 與 Spearmint（眾數初始化）、隨機取樣、先驗取樣；(2) 先驗引導方法對比：HPOBench 的 5 超參數 MLP 於 6 個 OpenML 資料集，先驗以 library 預設值為均值、標準差取值域的 25%，在同一 HyperMapper 框架內對比 BOPrO 與 BOWS；(3) 深度學習案例：U-Net Medical（6D）與 ImageNette-128（6D），先驗中心設於公開 repository 的預設值。πBO 已實作進 SMAC 與 HyperMapper。

## 發現
- ImageNette-128：πBO 第 4 次迭代即超越 vanilla BO 第 50 次迭代的表現，即 12.5× time-to-accuracy 加速；U-Net Medical 為 2.5×。
- ImageNette 最終驗證準確率 94.14%，超越該 pipeline 先前最佳的 93.55%，創下新紀錄——而先驗僅是「以公開預設值為中心的 Gaussian」，無任何來源任務觀測。
- 6 個 OpenML MLP 任務中 4 個表現最佳，且跨任務一致性最高（對比 BOPrO、BOWS）。
- 穩健性：informative 先驗下比 Spearmint 領先可達一個數量級；wrong 先驗下能恢復到與 Spearmint 約略相等的 regret，與理論一致；對照組 Spearmint（僅眾數初始化）在 strong/weak 先驗下常無法從初始設計繼續顯著改進——顯示「整程維持先驗影響」優於「只用先驗選初始點」。

## 啟發
- **被啟發**：[[P_draft_MI_SMAC]] — 承接「用外部知識設定 BO 起點」的問題設定，但把知識載體從離散配置清單推廣為任意連續信念分布，並補上收斂理論。
- **啟發了**：[[P_draft_LLM_Prior_Elicitation]] — π(x) 不必來自人類：LLM 依任務描述引出的分布可直接作為 πBO 先驗（本研究 LLM 臂的機制基礎）；其 β/n 衰減也為 [[P_draft_Similarity_Gating]] 的「錯誤先驗安全底線」提供保證。
