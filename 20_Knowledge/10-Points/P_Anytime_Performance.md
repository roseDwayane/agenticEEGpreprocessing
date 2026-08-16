---
schema_version: "1.0"
id: P_Anytime_Performance
type: point
name: "Anytime Performance"
description: "以 incumbent（running-best）曲線及其下面積評估搜尋演算法，而非僅看最終值；anytime 曲線的缺席被 AutoML 基準文獻明列為開放問題。"
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [automl, benchmark, anytime]
domain: [AI, Neuroscience]
field: [BCI]
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
source: "GijsbersEtAl2019 + EggenspergerEtAl2021"
year: 2019
claim_type: methodological
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-3"
    - "step5_full_text/GijsbersEtAl2019.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# Anytime Performance

> **核心主張**：搜尋演算法應以 anytime performance——每次評估後的 incumbent（running-best）曲線及其彙總量（如曲線下面積）——來評估，而非只看固定大預算後的最終值；因為方法排名強烈依賴預算階段（多保真優勢在 1% 預算顯著、100% 時消失），而 anytime 曲線至今在多數 AutoML 比較中缺席，被基準文獻明列為開放問題。

## 來源
- 作者：Gijsbers, P., LeDell, E., Thomas, J., Poirier, S., Bischl, B., & Vanschoren, J. / 年份：2019 / 出處：6th ICML Workshop on Automated Machine Learning（arXiv:1907.00909，Open AutoML Benchmark）/ citation key: `GijsbersEtAl2019`
- 佐證：Eggensperger, K., et al. / 2021 / NeurIPS Datasets and Benchmarks（HPOBench）/ citation key: `EggenspergerEtAl2021`

## 目的
確立「評估的時間軸維度」為搜尋方法比較的一級公民：暖啟動類方法的價值本質上是早期預算的加速，只有 anytime 量測才能看見它；僅報告最終準確率會系統性抹除初始化的貢獻。

## 核心主張（展開）
GijsbersEtAl2019 建立了首個開放、可擴充、持續更新的 AutoML 基準（39 個 OpenML 資料集、4 個系統、1h/4h 預算、10-fold CV），其結論處明白承認侷限：「我們希望未來加入 anytime performance evaluation curves，但多數 AutoML 工具尚不支援」——說明缺的不是概念而是基礎設施。同一研究也顯示為何需要它：無任何系統一致最優、Auto-WEKA 在 4h 預算下出現過擬合跡象、1h 與 4h 結果差異僅輕微——固定預算的單點快照對「何時該停」與「誰先到好區域」完全沉默。EggenspergerEtAl2021（HPOBench，經 step6 Theme 3）給出決定性的預算依賴證據：多保真方法的優勢在 1% 預算時統計顯著、在 100% 預算時消失——同一組方法在不同預算點的排名可以反轉。step6 Theme 3 的爭議段落據此把「多數比較缺乏 anytime 性能曲線」列為基準效度的未解問題。對本研究而言，這正是量測暖啟動增益的正確語言：知識注入的假說本質是「更快抵達」，不是「最終更高」。

## 方法
GijsbersEtAl2019：39 個分類資料集（依預定準則自先前 AutoML 論文、競賽與基準選出，排除過易與人工資料集），4 個 AutoML 系統（Auto-WEKA, auto-sklearn, TPOT, H2O AutoML）加三個基線（constant predictor、untuned RF、tuned RF），AWS m5.2xlarge 統一硬體，AUROC（二類）/ log loss（多類），10-fold CV，1h 與 4h 兩種預算，總計約 8,000 小時計算；分數正規化至 constant predictor = 0、tuned RF = 1。EggenspergerEtAl2021：12 個基準家族、100+ 問題、13 個最佳化器、32 個種子的容器化多保真基準。

## 發現
- GijsbersEtAl2019 明文：「We hope to add anytime performance evaluation curves in the future, but this is not yet supported by many of the AutoML tools.」——anytime 評估列為開放的未來工作。
- 預算依賴的排名反轉（EggenspergerEtAl2021，經 step6 Theme 3）：多保真方法優勢在 1% 預算顯著、100% 時消失；黑箱方法在大預算下趕上。
- 固定預算快照的盲點例證（GijsbersEtAl2019）：無系統一致最優；dionis/helena 上所有框架皆遜於 tuned RF；Auto-WEKA 於 4h 相對 1h 出現過擬合；meta-learning 對測試資料集的污染被列為未解公平性問題。
- 本卡所倡議的具體 endpoint 形式（incumbent 曲線下面積）在來源文獻中未給出標準化定義——(待補)。

## 啟發
- **被啟發**：ChaLearn AutoML 挑戰賽的「any-time any-dataset learning」框架（Guyon et al.，GijsbersEtAl2019 文中引述）——anytime 概念的競賽源頭。
- **啟發了**：[[B_llm-warmstart-cash-mi-eeg-pipelines]] — 本研究主要 endpoint 直接採 anytime AUC（incumbent balanced-accuracy 曲線下面積）與 evaluations-to-target，於 B ∈ {25, 50, 100} 三個預算點報告（H1 的兩個共同主端點）；[[P_Budget_Matched_Protocol]] — anytime 曲線是預算對齊協議得以逐評估比較的量測基礎，兩者互為表裡。
