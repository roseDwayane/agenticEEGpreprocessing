---
schema_version: "1.0"
id: P_ChatBCI
type: point
name: "ChatBCI (Human-AI Teaming for BCI)"
description: "LLM agent 操作 BCI 工具鏈的首個示範：GPT-4o 在 <10 個 prompt 內產生可運作的 CNN 解碼器；其未來工作明確呼籲 LLM-driven HPO/NAS——LLM 先驗進入 EEG 管線的介面。"
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [llm, bci, automl]
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
source: "KapitonovaBall2024"
year: 2024
claim_type: methodological
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-5"
    - "step6_sota_review.md#cross-theme-analysis"
    - "step5_full_text/KapitonovaBall2024.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# ChatBCI (Human-AI Teaming for BCI)

> **核心主張**：ChatBCI 證明 LLM agent 已能實際操作 EEG/BCI 工具鏈（資料探索、前處理、解碼器生成、訓練迴圈），其成果「高於 chance 但低於 SoA、且需專家引導」，而其未來工作章節明確呼籲在工具箱中實作 LLM-driven HPO/NAS——這正是 LLM 先驗進入 EEG 管線最佳化的介面。

## 來源
- 作者：Kapitonova, M., & Ball, T. / 年份：2024 / 出處：arXiv preprint（arXiv:2501.01451; University Hospital Freiburg / NeuroMentum AI）/ citation key: `KapitonovaBall2024`

## 目的
把「AI scientist」系統改造為適合 BCI 領域的 human-AI teaming 框架：以七項 Janusian design principles 為基礎建立 Python 工具箱 ChatBCI，驗證 LLM 能否在真實 BCI 專案中承擔從想法生成到實驗迭代的工作。

## 核心主張（展開）
ChatBCI 以 GPT-4o 為底層 LLM，內含公開 EEG 資料集、EEG/BCI 知識庫、前處理／解碼／訓練／視覺化核心功能與 LLM 溝通工具。在 BCI Competition IV 2a 上的示範專案顯示三件事。第一，可行性：epoching、re-referencing、濾波等步驟通常僅需數個 prompt；LLM 生成的 CNN 解碼器與訓練迴圈（含資料增強）在 10 個 prompt 內完成，validation 準確率明顯高於 chance。第二，極限：未經領域微調的 LLM 生成的研究想法全數已被文獻處理過、且無法正確檢索該資料集的 SoA 準確率；解碼結果「大幅低於先前報告」，ERP 解讀混雜正確與錯誤假設（如誤引 readiness potential），凸顯專家知識轉移之必要——step6 Theme 5 據此將其定位為「有潛力，但缺乏專家引導時明顯低於最先進水準」。第三，介面地位：作者將 LLM 生成解碼器稱為「a novel class of AutoML for brain signal analysis」，並把「在 ChatBCI 中實作 hyperparameter optimization 與 neural architecture search 功能」列為下一步，指出這可實現「無需 AutoML 庫專家知識的 LLM 式 AutoML」——step6 跨主題分析視此為 LLM 先驗到達 EEG 管線的通道。

## 方法
以七項 Janusian design principles（共同語言、雙向透明、共享知識庫 Jiki、優先序整合、adaptive autonomy、novice-to-expert 可及性、持續共演化）設計 human-AI 工作空間；在 BCI IV 2a 上執行兩個目標：(1) 探索性資料分析與資料驗證（基本統計、ERP、頻域 PSD；發現 cue 誘發的 blink-saccade 眼動 artifact 在 4-Hz high-pass 後仍殘留快速瞬變）；(2) 協作設計一個空間＋時間卷積的簡單 CNN（batch normalization、dropout、SWISH activation），以原始競賽資料切分做 within-subject 訓練。

## 發現
- 解碼器與訓練迴圈的建置總計少於 10 個 prompt；validation 準確率在所有受試者上明顯高於 chance，但「大幅低於先前報告」。
- SoA 對照（文中引述）：原競賽冠軍 FBCSP 重現為四類 67.8%，ConvNets 再增約 4%（SchirrmeisterEtAl2017）；文獻中甚至有高達 97.61% 的宣稱（Xie & Oniga 2023）——ChatBCI 的簡單模型距此甚遠。
- 想法生成：連續生成的研究問題（12 例列表）經專家一眼即可確認皆已被社群處理，顯示需要 novelty 驗證工具（Semantic Scholar API）與迭代精煉。
- 本文未報告 ChatBCI 解碼器的具體準確率數字（僅圖示與定性描述）——(待補)。

## 啟發
- **被啟發**：LLM agent／AI-scientist 一系（Lu et al. 2024 的全自動 AI Scientist、Swanson et al. 2024 的 Virtual Lab）——ChatBCI 把該線改造為強調 human-AI teaming 的 BCI 版本。
- **啟發了**：[[B_llm-warmstart-cash-mi-eeg-pipelines]] — 本研究 F3（LLM 先驗引出介面：由 LLM 根據資料集／受試者描述產生 πBO 先驗）直接承接 ChatBCI 未來工作所呼籲的 LLM-driven HPO/NAS，但以「LLM 只做先驗注入、搜尋仍由古典 BO 執行」的閘控式設計回應其「低於 SoA、需引導」的教訓；[[P_Subjects_as_Tasks]] — 提供了 LLM 知識與 EEG 管線之間缺失介面存在的證據。
