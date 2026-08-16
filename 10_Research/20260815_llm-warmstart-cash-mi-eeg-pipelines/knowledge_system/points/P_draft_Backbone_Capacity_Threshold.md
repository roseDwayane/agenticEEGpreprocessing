---
schema_version: "1.0"
id: P_draft_Backbone_Capacity_Threshold
type: point
name: "LLM Backbone Capacity Threshold"
description: "Under current prompting schemes, sub-70B backbones (Gemma 27B, Llama 3.1 8B) produce malformed or uncorrelated surrogate outputs, so LLAMBO-style gains depend on large-capacity models."
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [llm, capacity, reproducibility]
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
source: "RychertEtAl2025"
year: 2025
claim_type: empirical
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-1"
    - "step5_full_text/RychertEtAl2025.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# LLM Backbone Capacity Threshold

> **核心主張**：在目前的提示法下，sub-70B 骨幹（Gemma 27B、Llama 3.1 8B）會產生畸形（invalid JSON、缺漏超參數）或與觀測表現無相關的 surrogate 輸出——LLAMBO 式增益依賴大容量模型，對模型容量的縮減並不穩健。

## 來源
- 作者：Rychert, A., Spagnolo, G., & Posashkov, E. / 年份：2025 / 出處：*arXiv preprint*, arXiv:2511.18891（UL FRI reproducibility study） / citation key: `RychertEtAl2025`

## 目的
回答兩個問題：LLAMBO 的核心主張換成開放權重模型後是否成立？其行為對底層 LLM 的選擇與容量有多敏感？

## 核心主張（展開）
此重現研究以開放權重 Llama 3.1 70B 取代 GPT-3.5 重建 LLAMBO 的完整 prompting pipeline（所有 OpenAI API 呼叫改為本地 ollama），在原始評估協定下重跑 Bayesmark 與 HPOBench 實驗。主結論有二：其一，核心主張成立——情境式 warm-start 大幅改善早期 regret 並降低跨執行變異；移除文字脈絡（問題描述與超參數名稱嵌入）會顯著劣化預測精度與校準，證實 LLAMBO 架構對更換骨幹是穩健的。其二，此穩健性有容量門檻：以相同 prompts 與解析邏輯執行 Gemma 27B 與 Llama 3.1 8B 時，模型頻繁回傳畸形輸出（invalid JSON、缺漏超參數），surrogate 分數與觀測表現無相關，導致最佳化迴圈不穩定、約束違反、對近乎相同的候選給出高度不一致的排序。可靠的 surrogate 行為要求強 instruction-following、基本數值推理與合理校準的評分——這些性質在該實驗設定中只在 70B 模型上穩健浮現。

## 方法
重現範圍涵蓋四個面向：warmstarting 效率（對比 Random/Sobol/LHD 與 No/Partial/Full Context）、surrogate 行為與校準（NRMSE、R²、regret；LPD、coverage、sharpness）、文字脈絡消融、候選生成品質（對比 independent/multivariate TPE 與 random；以 regret 與 generalized variance/log-likelihood 評估）。設定：30 個任務（25 個 Bayesmark 任務 = 5 資料集 × 5 模型類別，加 3 個 LLM 預訓練未見的私有資料集與 2 個合成資料集）、每任務 5 個初始點 + 25 trials × 5 次獨立執行；自行重建統一的基線（GP-DKL、SKOpt-GP、Optuna-TPE、SMAC3）與評估/繪圖工具，並補充 per-task min-max 正規化分析（不改變定性排序）。

## 發現
- 核心主張確認：情境式 warm-start 一致降低早期 regret 與變異；Full Context 表現最佳；contextual warm-start 的初始設計多樣性與 LHCube 相當、遠高於 Random/Sobol。
- surrogate 分工確認：SMAC 純迴歸最強（最低 NRMSE、最高 R²），GP 校準最佳（近乎理想 coverage）；LLAMBO 低資料區間迴歸較弱、不確定性系統性低估，優勢來自跨任務語意先驗。
- 消融：移除文字脈絡使 NRMSE 一致升高（觀測數少時差距最大）、LPD 劣化、預測過度自信。
- 容量門檻：Gemma 27B 與 Llama 3.1 8B 在相同 prompts 下失敗（invalid JSON、缺漏超參數、分數與表現無相關、排序不一致）；僅 70B 骨幹穩健。

## 啟發
- **被啟發**：[[P_draft_LLAMBO]] — 直接以原協定重現並確認其核心主張，同時劃出其成立的容量邊界
- **啟發了**：本研究 OUT-scope 排除 sub-70B — 依據容量門檻證據，本研究的 LLM warm-start 臂將 sub-70B 骨幹列為範圍外（OUT-scope），避免把骨幹容量不足與方法失效混淆
