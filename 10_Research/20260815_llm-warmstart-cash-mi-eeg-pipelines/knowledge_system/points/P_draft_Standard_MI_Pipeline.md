---
schema_version: "1.0"
id: P_draft_Standard_MI_Pipeline
type: point
name: "Standard MI Pipeline (8-30 Hz FIR + CSP + LDA)"
description: "「標準」MI 前處理管線（8–30 Hz FIR band-pass + CSP + LDA）是社群慣例而非逐受試者最優配置，標準≠最優正是管線搜尋的動機。"
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [eeg, motor-imagery, pipeline, baseline]
domain: [AI, Neuroscience]
field: [BCI]
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
source: "CraikEtAl2019"
year: 2019
claim_type: conceptual
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-4"
    - "step6_sota_review.md#theme-5"
    - "step8_hypothesis_specification.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# Standard MI Pipeline (8-30 Hz FIR + CSP + LDA)

> **核心主張**：MI-BCI 社群沿用的「標準」前處理＋解碼管線（8–30 Hz FIR band-pass → CSP 空間濾波 → LDA 分類器）是一種人工設計慣例，而非任何單一受試者的最優配置——「標準 ≠ 最優」正是逐受試者管線搜尋（pipeline search）的根本動機。

## 來源
- 作者：Craik, A., He, Y., & Contreras-Vidal, J. L. / 年份：2019 / 出處：*Journal of Neural Engineering*（90 篇 EEG 深度學習研究之系統性回顧；本卡為概念卡，錨定於 step6 Theme 4 與 CraikEtAl2019，該文獻在本 session 僅摘要層級可得）/ citation key: `CraikEtAl2019`

## 目的
把「固定標準管線只是社群慣例」這一常被默認的前提明確化為可引用的知識點，作為本研究 baseline 對照臂與 H2（零樣本遷移 vs 標準管線）的概念基礎。

## 核心主張（展開）
MI 解碼的人工設計傳統由回顧文獻典律化：CraikEtAl2019 對 90 篇研究的回顧提供了「人工設計基線」的全景，並給出逐任務（per-task）的 hyperparameter 指引——這正說明最佳設定依任務而異，固定配置只是折衷。step6 Theme 4 指出，自動化搜尋能以不容忽視的幅度穩定勝過人工配置的 EEG 模型，且最佳 hyperparameters「遠超出批次或手動搜尋的範圍」（BirdLotfi2023）。Theme 5 的證據同方向：最佳組態依受試者與資料集而異，最佳分類器因人而異（AnarakiEtAl2024）、SSA 優化出的 RF 參數系統性偏離經驗預設值（ZhangEtAl2024）。因此 8–30 Hz FIR + CSP + LDA 應被理解為「合理的群體折衷點」，而非逐受試者最優；它的角色是對照臂，不是天花板。

## 方法
本卡為概念綜整，非單一實驗：(1) 以 CraikEtAl2019 的 90 篇研究回顧確立人工設計慣例的存在與其 per-task 變異；(2) 以 step6 Theme 4（11 篇 EEG 管線自動化文獻）中「自動搜尋穩定勝過人工配置」的收斂證據反證固定管線的次優性；(3) 以 step8 的 IN-scope 規格（固定標準管線 = 8–30 Hz FIR + CSP + LDA，作為四個對照條件之一）將此概念操作化為實驗設計中的 baseline 定義。

## 發現
- 標準管線的操作型定義（step8）：8–30 Hz FIR band-pass + CSP + LDA，列為本研究固定對照臂。
- 實驗室 pilot（step8 RQ1）：EEGMMI S001/S002 上，固定標準管線 balanced accuracy 為 0.555/0.635，而冷啟動管線搜尋達 0.775/0.915——同一受試者上「標準」與「搜尋所得」的差距具體可見。
- 相關佐證（step6 Theme 5）：within-subject 訓練下 FBCSP（CSP 家族的強化版）平均解碼準確率 82.1%，deep ConvNets 84.0%（SchirrmeisterEtAl2017）；SSA 優化的 RF 最優 DTN 為 24–50，偏離經驗預設值 30（ZhangEtAl2024）。
- CraikEtAl2019（摘要層級）：回顧 90 篇研究，CNN/RNN/DBN 優於 SAE/MLP，並提供逐任務 hyperparameter 指引。

## 啟發
- **被啟發**：CraikEtAl2019 的人工設計回顧傳統——它把「標準管線」典律化，同時（藉 per-task 指引）暴露了固定配置的內在張力。
- **啟發了**：[[P_draft_Subjects_as_Tasks]] — 若標準≠最優且最優因人而異，受試者就自然成為各自需要搜尋的任務；[[B_draft_llm-warmstart-cash-mi-eeg-pipelines]] — 本研究把標準管線設為固定 baseline 對照臂（H2 的比較對象），「標準≠最優」即管線搜尋需求的來源。
