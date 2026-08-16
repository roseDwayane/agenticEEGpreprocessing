---
schema_version: "1.0"
id: P_draft_Rising_Bandits
type: point
name: "Rising Bandits"
description: "Rising Bandits models CASH algorithm selection as a non-stationary bandit whose rising, concave arm rewards allow provable elimination of inferior algorithms, holding 95% accuracy as algorithms grow 1 to 16 while saving ~12.6x trials versus SMAC."
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [bandits, cash, automl]
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
source: "LiEtAl2020a"
year: 2020
claim_type: methodological
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-3"
    - "step5_full_text/LiEtAl2020a.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# Rising Bandits

> **核心主張**：以非定常（non-stationary）multi-armed bandit 建模 CASH 的演算法選擇——每支臂的報酬隨 HPO 資源遞增且近似凹性——即可用上下界淘汰劣勢演算法臂，在演算法數 1→16 時仍保持 95.02% 準確率，並平均比 SMAC 省約 12.6 倍試驗。

## 來源
- 作者：Li, Y., Jiang, J., Gao, J., Shao, Y., Zhang, C., & Cui, B. / 年份：2020 / 出處：*AAAI Conference on Artificial Intelligence*（arXiv:2012.04371） / citation key: `LiEtAl2020a`

## 目的
回答「如何避免 joint-space BO 在巨大 CASH 超參數空間中的低效率，把資源自適應地集中到有希望的演算法上」的資源分配問題。

## 核心主張（展開）
論文將 CASH 重寫為 bilevel 最佳化：上層為演算法選擇、下層為各演算法獨立的 HPO，並提出 alternating optimization framework 交替求解——BO（SMAC）負責每個演算法自己的小超參數空間，MAB 負責演算法間的資源分配。關鍵觀察是：BO 的最佳觀測驗證準確率序列 r(1), r(2), … 是遞增、有界、且近似凹（邊際報酬遞減）的非定常過程，既有的定常 MAB 假設不適用。因此提出 Rising Bandits：目標不是最大化累積報酬，而是最大化 T 步內的最佳觀測報酬（等價於 best-arm identification）。利用凹性可外推每支臂最終報酬的上界 u(T)，若某臂上界低於另一臂的下界（目前最佳值）即可安全淘汰；此淘汰準則具有可證明的 regret 上界（Theorem 1），並有處理「loose concavity」的平滑成長率變體與 cost-aware 變體。

## 方法
Algorithm 1：維持候選臂集合，每輪對每支存活臂各給一單位 HPO 資源（一次 SMAC 試驗），以成長率 ω_k(t) 更新上界 u_k(T) = min(y_k(t) + ω_k(t)(T−t), 1) 與下界 l_k(T) = y_k(t)，淘汰被支配的臂，直到預算 T 用盡。平滑成長率取最近 C 步平均（實驗 C=7，不敏感）。實驗設定：30 個 OpenML 分類資料集、與 auto-sklearn 相同的空間（16 個分類演算法、78 個超參數）、資料切分 64%/16%/20%、每法重複 10 次；基線含 AVG、SMAC、TPE、CMAB、UCB、Softmax、BOHB 及 Auto-Sklearn、Hyperopt-Sklearn、TPOT。

## 發現
- 高維穩健性（PC4，500 trials）：候選演算法數 K 由 1 增至 16 時，本法維持 95.02%（K=16 時 95.02%），SMAC 由 95.02% 降至 93.63%，AVG 降至 93.39%。
- 效率：平均比 SMAC 少約 12.6 倍試驗（step6 Key Results）；對 BOHB 最高 15.7 倍加速（step6 Key Results）。
- 最終品質：在 30 個資料集中的 26 個取得最佳驗證準確率（step6 Key Results）。
- 機制：前約 100 次試驗與 SMAC 行為相似（探索期），之後開始淘汰劣勢演算法、將資源集中於有望的臂，帶來顯著改善。

## 啟發
- **被啟發**：[[P_draft_Hyperband]] — 承接「以 bandit 做資源分配、以淘汰加速搜尋」的思想，但把定常報酬改為遞增凹性報酬以貼合 CASH
- **啟發了**：[[P_draft_Search_Space_Decomposition]] — VolcanoML 的 conditioning block 沿用同一「上下界淘汰子空間」機制，將其推廣為可組合的執行計畫節點
