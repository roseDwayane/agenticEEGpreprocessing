---
schema_version: "1.0"
id: P_draft_LLM_DS_Agent
type: point
name: "LLM data-science / ML-engineering agents"
description: "LLM agents that reason, use tools, and search (tree/MCTS/CBR) to do ML engineering — on tabular/code, never EEG"

asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: []

domain: [AI]
field: [LLMAgents, AutoML]

status: draft
created: 2026-06-20
updated: 2026-06-20

parent:
parts: []
depends_on: []
caused_by: []
causes: []

related_lines: []
related_planes: []
related_body: []

source: "GuoEtAl2024"
year: 2024
claim_type: methodological

provenance:
  session_id: "20260620"
  source_files:
    - "step5_full_text/GuoEtAl2024.md"
    - "step5_full_text/ChiEtAl2024.md"
    - "step5_full_text/TriratEtAl2024.md"
    - "step6_sota_review.md#theme-5"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# LLM data-science / ML-engineering agents

> **核心主張**：會推理中間狀態、呼叫工具、做搜尋（tree/MCTS/case-based）的 LLM agent，已在表格/程式碼 ML 工程匹敵或勝過人工——但從未用於 EEG。

## 來源
- 作者：Guo 等（DS-Agent）、Chi 等（SELA）、Trirat 等（AutoML-Agent）
- 年份：2024
- 出處：step5 GuoEtAl2024/ChiEtAl2024/TriratEtAl2024.md（full-text）
- citation key: `GuoEtAl2024`

## 目的
讓 LLM 自動完成資料科學/ML 工程的選型、調參、迭代。

## 核心主張（展開）
DS-Agent 用 case-based reasoning 迴圈；SELA 用 MCTS 引導；AutoML-Agent 多智能體含驗證；AIDE 程式碼樹搜尋。皆在表格/程式碼基準評估，無一在 EEG 或訊號處理。

## 方法
（full-text）LLM controller + 工具呼叫 + 搜尋（CBR / MCTS / tree）+ 自我驗證。

## 發現
（待補具體數字）在 MLAgentBench 等基準上匹敵或勝過人工 ML 工程。

## 啟發
- **被啟發**：AutoGen、HuggingGPT（substrate）；MLAgentBench（benchmark）
- **啟發了**：[[P_draft_LLM_Preproc_Agent]] — 遷移到 EEG 預處理