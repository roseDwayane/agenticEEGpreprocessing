---
schema_version: "1.0"
id: P_LLM_Prior_Elicitation
type: point
name: "LLM Prior Elicitation"
description: "Prompting can elicit hyperparameter prior distributions from an LLM, providing the knowledge source for piBO-style prior injection into Bayesian optimization."
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [llm, prior, bayesian-optimization]
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
source: "LiuEtAl2024"
year: 2024
claim_type: conceptual
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-1"
    - "step5_full_text/LiuEtAl2024.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# LLM Prior Elicitation

> **核心主張**：用提示（prompting）從 LLM 引出超參數的先驗分布或初始信念，可作為 πBO 式先驗注入機制的知識來源——無需任何來源任務的觀測資料，只需把領域知識轉譯為 BO 空間上的分布。

## 來源
- 作者：Liu, T., Astorga, N., Seedat, N., & van der Schaar, M.（概念脈絡另含 Topalis et al. 2025 的 LangBO 一系） / 年份：2024 / 出處：*ICLR 2024*（arXiv:2402.03921）；lineage：Topalis, P., Schieseck, M., & Gehlhoff, F. (2025), *IEEE ETFA 2025* / citation key: `LiuEtAl2024`（lineage: `TopalisEtAl2025`）

## 目的
回答「當沒有歷史調參紀錄可供 meta-learning 遷移時，先驗知識從哪裡來」的冷啟動問題——LLM 預訓練吸收的跨任務知識被當作可被誘出（elicit）的先驗分布來源。

## 核心主張（展開）
LLAMBO 的理論框架把 LLM in-context learning 詮釋為隱式貝葉斯推論：p(θ) 代表預訓練吸收的最佳化問題先驗與領域相關性，而多數 BO 實務採用 non-informative priors，錯失了這層知識。LLM Prior Elicitation 即把這層知識顯式化：以 zero-shot prompting 讓 LLM 直接提出有希望的組態或縮小的搜尋空間（LiuEtAl2024; Kannan2023），或更結構化地——LangBO 透過 RAG 把自然語言專家知識轉為 BO 空間上的 Dirichlet 先驗（TopalisEtAl2025）、MetaLLMix 結合歷史實驗與 SHAP meta-features 做零樣本推薦（Bal-GhaouiTiouti2025）、化學領域以調查式提示 + 偏好學習引出 utility prior（PatelEtAl2025）。誘出的分布可經 πBO 式 prior-weighted acquisition（含 β/n 衰減防護）注入任何 BO 引擎，兼得「先驗實用性最高——不需來源觀測，只需信念分布」（HvarfnerEtAl2022）與「LLM 可錯、由衰減機制兜底」兩項性質。

## 方法
一般流程：(1) 把任務詮釋資料（資料集特性、模型類別、搜尋空間語意）組成 prompt；(2) 令 LLM 輸出組態建議、分布參數或空間縮限；(3) 將輸出正規化為 BO 空間上的先驗（如 Dirichlet/KDE/加權 acquisition）；(4) 以 πBO 式衰減（先驗權重隨迭代 β/n 遞減）保證錯誤先驗可被觀測證據覆寫。新興的信任層爭議提示須加防護：誘出的信念對提示措辭與查詢協定高度敏感（LeiCooper2026），促生證據閘控、可否證的先驗加權（ChenYi2026）。

## 發現
- LLM 誘出的效用先驗改善 BO 初始查詢，並在 6 個反應產率資料集中的 4 個提升最佳化表現（PatelEtAl2025，摘要層級）。
- LLM 建議的起始條件與縮小搜尋空間比隨機初始化 BO 更快降低驗證損失（Kannan2023）。
- 注入通道的效力上限由 πBO 證據界定：先驗加權 acquisition 在 ImageNette 上帶來 12.5 倍 time-to-accuracy 加速、U-Net 上 2.5 倍，且可證明從錯誤先驗恢復（HvarfnerEtAl2022）。
- LangBO（TopalisEtAl2025）之具體量化結果 (待補——僅摘要層級)。

## 啟發
- **被啟發**：[[P_PiBO]] — πBO 提供「任意信念分布皆可注入且有衰減保證」的注入機制，先驗誘出負責供給該分布
- **被啟發**：[[P_LLAMBO]] — LLAMBO 的 zero-shot warm-start 證明 LLM 內含可用的組態先驗，誘出即其顯式化
- **啟發了**：本研究 F3 臂 — 以 LLM 誘出的 EEG-MI pipeline 先驗（頻帶、空間濾波、分類器選擇）作為 πBO 式注入來源，替代無跨受試者歷史時的冷啟動
