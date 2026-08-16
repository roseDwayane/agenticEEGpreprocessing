---
schema_version: "1.0"
id: P_Similarity_Gating
type: point
name: "Similarity Gating"
description: "依任務相似度調節暖啟動強度：WS-CMA-ES 的增益與 γ-similarity 單調相關、天真重用在低相似度下劇烈退化，故低相似度時應安全退回冷啟動。"
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [task-similarity, gating, negative-transfer, warm-start]
domain: [AI]
field: [AutoML]
status: active
created: 2026-08-15
updated: 2026-08-15
parent: P_Warm_Start_Initialization
parts: []
depends_on: [P_Meta_Features]
caused_by: [P_Negative_Transfer]
causes: []
related_lines: []
related_planes: []
related_body: [B_llm-warmstart-cash-mi-eeg-pipelines]
source: "NomuraEtAl2021"
year: 2021
claim_type: methodological
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-2"
    - "step5_full_text/NomuraEtAl2021.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# Similarity Gating

> **核心主張**：暖啟動的效益與可形式化的任務相似度單調相關，而天真重用來源分布在低相似度下劇烈退化——因此暖啟動強度應依相似度門控，並在偵測到低相似度時安全退回冷啟動（Nomura 等人明示「自動偵測任務不相似並切回原始 CMA-ES」是此類方法可靠化的必要機制）。

## 來源
- 作者：Nomura, M., Watanabe, S., Akimoto, Y., Ozaki, Y., & Onishi, M. / 年份：2021 / 出處：Proceedings of the AAAI Conference on Artificial Intelligence（AAAI-21；Warm starting CMA-ES for hyperparameter optimization）/ citation key: `NomuraEtAl2021`

## 目的
CMA-ES 在嚴格受限的評估預算下因 covariance matrix 適應期過長（O(d²) 迭代）而輸給 BO；WS-CMA-ES 用來源任務知識初始化其多變量高斯分布以縮短適應期，並首次給出「任務相似度」的形式化定義，使暖啟動效益可以被相似度預測與診斷。

## 核心主張（展開）
方法先定義 γ-promising distribution：目標函數最好的 γ×100% 解所在區域經高斯平滑後的分布（Definition 3.1）；再定義 γ-similarity s(γ₁,γ₂) = D_KL(P*‖P₂) − D_KL(P₁‖P₂)，即「來源任務的 promising 分布比非資訊先驗 P* 更接近目標任務 promising 分布的程度」（Definition 3.2）。暖啟動作法：從來源任務觀測取 top-γ 解建 Gaussian mixture（各成分 α²I 共變異），再以最小化 KL divergence 的閉式解（m* 為 top-γ 解均值、Σ* = α²I + 經驗共變異）初始化 CMA-ES 的均值與共變異矩陣。合成實驗證實 WS-CMA-ES 的增益隨 γ-similarity 變化幾乎同步——相似度高則大幅加速、相似度低則不佳；而兩種天真遷移（ReuseNormal：直接沿用來源最終分布；ReuseGMM：整程從來源 GMM 取樣）在來源—目標偏移時劇烈退化，WS-CMA-ES 因保留 α²I 正則化與後續適應能力而退化較緩。結論明言：自動偵測任務不相似並切回原始 CMA-ES，是讓此方法更可信可靠的關鍵——即相似度門控。

## 方法
合成實驗：sphere 與 rotated ellipsoid（非可分、病態條件）函數，目標 b=0.6、來源 b=0.4–0.8（各 20 次執行；α = γ = 0.1），同時量測 γ-similarity 與 f̄_cma − f̄_ws 的對應。HPO 實驗兩種實務情境：(1) 以 10% 子集的 HPO 結果暖啟動全資料集（LightGBM 6 超參數於 Toxic Comment、MLP 8 超參數於 MNIST/Fashion-MNIST、8 層 CNN 10 超參數於 CIFAR-100）；(2) 跨資料集遷移（MNIST→Fashion-MNIST、SVHN→CIFAR-10）。來源知識統一為 100 次 random search 評估、每設定 12 次執行；基線含 CMA-ES、RS、WS-only、GP-EI、TPE、MTBO、MT-BOHAMIANN。

## 發現
- 合成實驗：γ-similarity 的變化與 WS-CMA-ES 相對 CMA-ES 的改善量幾乎同步（兩函數皆然）——相似度即暖啟動效益的預測子。
- 天真遷移的教訓：ReuseNormal 在 b=0.6（來源=目標）時最佳，但偏移後劇烈退化；ReuseGMM 因整程不適應同樣受害；WS-CMA-ES / WS-sep-CMA-ES 退化明顯較小。
- HPO 實驗：WS-CMA-ES 在約 25 次（子集遷移）與約 30 次（跨資料集遷移）評估內即找到優於冷啟動 CMA-ES 的配置，收斂速度在各實驗中最突出；MTBO 在跨資料集情境需約 40 次評估才勝過 GP-EI（子集情境約 25 次）。
- 計算性質：知識僅在初始化時使用一次，複雜度不隨來源觀測數成長（對比逐迭代重建模型的 transfer-BO）；不需 meta-features。
- 侷限（作者自述）：相似度低時 WS-CMA-ES 可能劣於 CMA-ES；「自動偵測不相似 + 切回冷啟動」被列為必要的未來工作——本卡命名的 gating 機制本身在該文中尚未實作 (待補)。

## 啟發
- **被啟發**：[[P_Negative_Transfer]] — 天真重用的劇烈退化與 surrogate 層的結構性錯估風險，是門控機制存在的理由（caused_by 關係）；[[P_Meta_Features]] — 門控需要任務相似度的座標系，γ-similarity 提供了免 meta-features 的替代方案。
- **啟發了**：本研究門控臂 — 以 EEG meta-features 距離實作 g(s) 門控：低相似度時先驗衰減向均勻分布（以 πBO 的錯誤先驗恢復保證為安全底線），並以 gated vs ungated 的負遷移率（McNemar's test）作為正式終點。
