---
schema_version: "1.0"
id: P_SMAC
type: point
name: "SMAC (Sequential Model-based Algorithm Configuration)"
description: "SMAC is a random-forest-surrogate Bayesian optimization framework that handles conditional CASH spaces and serves as the mainstream engine of AutoML systems."
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [bayesian-optimization, surrogate, automl]
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
source: "LindauerEtAl2021"
year: 2021
claim_type: methodological
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-3"
    - "step5_full_text/LindauerEtAl2021.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# SMAC (Sequential Model-based Algorithm Configuration)

> **核心主張**：以 random forest 為 surrogate 的 Bayesian optimization 能穩健處理高維、具條件層級的 CASH 搜尋空間，是 AutoML/CASH 系統的主流最佳化引擎。

## 來源
- 作者：Lindauer, M., Eggensperger, K., Feurer, M., Biedenkapp, A., Deng, D., Benjamins, C., Ruhkopf, T., Sass, R., & Hutter, F. / 年份：2021 / 出處：*Journal of Machine Learning Research*, 22(1–9)（arXiv:2109.09831） / citation key: `LindauerEtAl2021`

## 目的
回答「如何用一個穩健、可重用的 BO 框架，在少量評估內為各種 HPO/CASH/演算法組態任務找到好組態」的工程問題。

## 核心主張（展開）
SMAC3 是 BSD 授權的開源 BO 套件，其核心設計是以 random forest（RF）作為 surrogate model——SMAC 是第一個採用此類模型的 BO 方法（Hutter et al. 2011）。RF surrogate 天然支援 categorical 變數與多層 conditional hierarchy（例如 top-level 超參數選分類演算法、sub-level 超參數選 optimizer），因此特別適合 CASH 這種結構化空間。套件透過四種 facade 封裝不同使用情境：SMAC4BB（低維連續 black-box，GP + EI）、SMAC4HPO（CASH/結構化 HPO，Sobol 初始設計 + RF + logEI）、SMAC4MF（多保真，依 BOHB 原則結合 Hyperband 但以 RF 取代 TPE）、SMAC4AC（演算法組態，aggressive racing）。其在 auto-sklearn、Auto-PyTorch 與 NeurIPS 2020 BBO challenge 冠軍方案中的採用，確立了它作為 CASH 生態系預設引擎的地位。

## 方法
標準 SMBO 迴圈：以 surrogate model M 選出最大化 acquisition function 的組態、評估目標函數、將觀測 (λ, ψ) 回饋重擬合 M。針對不同任務預設不同組件組合（initial design：Default/Random/LHD/Sobol；acquisition：PI/EI/logEI/EIperSec/LCB；intensification：racing/successive halving/Hyperband）。支援 DASK 平行化與 CLI 跨語言呼叫。多保真模式在最高 budget 層級擬合 surrogate；演算法組態模式支援 right-censored 觀測插補與 heavy-tailed 成本的 logEI。

## 發現
- 在 letter（6D）、Naval-Propulsion（9D）、NAS（9D）三個 HPOBench surrogate 基準上，SMAC3 的多保真模式在早期與 Hyperband 相當、中期最佳，後期由純 BO+RF 趕上；全程穩定優於 Dragonfly，後期亦優於 BOHB。
- SMAC 曾為 SAT solver 最佳化超過 300 個超參數的組態空間，帶來高達數個數量級的加速。
- 外部證據：在 LLAMBO 重現研究中，SMAC 是純單任務迴歸表現最強的 surrogate（最低 NRMSE）（RychertEtAl2025）；joint-space SMAC 在演算法數 1→16 時準確率由 95.02% 降至 93.63%（LiEtAl2020a），顯示其高維退化弱點。

## 啟發
- **被啟發**：[[P_CASH]] — CASH 的 conditional 空間結構是 RF surrogate 設計的直接動因
- **啟發了**：[[P_MI_SMAC]] — meta-learning 暖啟動初始化 SMAC（MI-SMBO）以其為基底引擎
- **啟發了**：[[P_CoFEH]] — CoFEH 的 HPO 模組即源自 SMAC 的 BO，並以 FE meta-features 條件化其 surrogate
