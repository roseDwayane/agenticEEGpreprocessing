---
schema_version: "1.0"
id: P_draft_TransBO
type: point
name: "TransBO (Two-Phase Transfer HPO)"
description: "兩階段可學權重的遷移 HPO 框架：Phase 1 以約束最佳化聯合聚合 source surrogates，Phase 2 以非遞減 target 權重自適應整合 source 與 target 知識，理論上不劣於無遷移。"
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [transfer-learning, surrogate, hyperparameter-optimization, warm-start]
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
source: "LiEtAl2022c"
year: 2022
claim_type: methodological
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-2"
    - "step5_full_text/LiEtAl2022c.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# TransBO (Two-Phase Transfer HPO)

> **核心主張**：把遷移 HPO 拆成兩個各自以約束最佳化學權重的階段——先聯合聚合多個 source surrogates 為單一 source surrogate，再以非遞減的 target 權重將其與 target surrogate 自適應整合——可同時處理「來源任務互補性」與「聚合動態性」兩大挑戰，且理論上足夠試驗後不劣於無遷移。

## 來源
- 作者：Li, Y., Li, Y., Shen, Y., Jiang, H., Zhang, W., Yang, Z., Zhang, C., & Cui, B. / 年份：2022 / 出處：Proceedings of the 28th ACM SIGKDD Conference on Knowledge Discovery and Data Mining（KDD '22）/ citation key: `LiEtAl2022c`

## 目的
既有 surrogate 層遷移法（POGPE 常數權重、TST 核加權、RGPE 排名機率權重）以獨立的啟發式權重組合 base surrogates，忽略了 (1) 來源任務間的互補綜效與 (2) 搜尋過程中應逐步把重心移回 target 的動態；TransBO 旨在以「有原則地學出來的聯合權重」同時解決這兩者。

## 核心主張（展開）
TransBO 為每個 source task 離線訓練一個 base surrogate M^i，並在 target 觀測上線上訓練 M^T。Phase 1 以 M^S = Σ w_i M^i 聯合組合 K 個 source surrogates；Phase 2 以 M^TL = p_S·M^S + p_T·M^T 整合 source 與 target。兩組權重 w 與 p 皆非啟發式，而是解帶單純形約束（權重和為 1、非負）的最佳化問題：以可微分的 pairwise ranking loss 為目標——HPO 在意的是配置的偏序（最優位置）而非預測絕對值——可用 sequential quadratic programming 求解。p 的學習採 5-fold cross-validation 以估計泛化能力而非 in-sample 誤差；每輪迭代並施加 p_T 的非遞減先驗 p_T^i = max(p_T^i, p_T^{i-1})，確保 target 知識佔比只增不減。基於 cross-validation 與非遞減約束，理論討論給出：試驗足夠時 TransBO 不劣於無遷移方法（負遷移防護）。複雜度 O(k·n³)，遠低於把 k 個來源任務 n 次試驗合進單一 surrogate 的 O(k³n³)，且不需 meta-features。

## 方法
在 BO 迴圈內，每輪解兩個約束最佳化問題得 w 與 p，建 M^TL 後以 EI 選點評估並更新 M^T。實驗分三部分：(1) 靜態遷移場景，對比 Random search、I-GP 兩個非遷移基線與 SCoT、SGPR（Google Vizier 核心演算法）、POGPE、TST、TST-M、RGPE 等遷移基線；(2) 動態遷移場景（貼近生產環境的連續調參）共 30 個 tuning 任務；(3) 普適性測試：以 NAS-Bench-201 將 CIFAR-10 / CIFAR-100 的 NAS 知識遷移至 ImageNet16-120。作者並發布耗費超過 200K CPU 小時、含逾 180 萬次模型評估的大規模遷移 HPO 基準。

## 發現
- 動態遷移場景：30 個 tuning 任務中有 22.25 個進入 top-2（Practicality）。
- NAS 遷移：相對 state-of-the-art NAS 方法達成超過 5× 加速（Universality）。
- 複雜度 O(k·n³) vs 單一合併 surrogate 的 O(k³n³)（Scalability）；消融顯示聯合學權重優於各基線的獨立啟發式權重。
- 理論：cross-validation + 非遞減 p_T 下，給定足夠試驗，效能不劣於無遷移（其他方法無此保證）。

## 啟發
- **被啟發**：[[P_draft_Two_Stage_Transfer_Surrogate]] — 承襲 TST 的「兩段式 base-surrogate 加權組合」骨架，但把 Nadaraya-Watson 核的啟發式相似度權重換成約束最佳化學出的聯合權重，並加入泛化導向的 cross-validation 與非遞減防護。
- **啟發了**：VolcanoML 的 RGPE 擴充路線 — 同一 PKU 系譜將學習式 surrogate 遷移整合進分解式 CASH 系統（VolcanoML 的 RGPE/RankNet meta-learning 遷移），為本研究「分解式 CASH + 跨受試者搜尋歷史遷移」提供系統級先例。
