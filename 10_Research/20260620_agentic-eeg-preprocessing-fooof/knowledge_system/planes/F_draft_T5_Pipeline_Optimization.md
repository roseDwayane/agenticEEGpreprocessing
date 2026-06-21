---
id: F_draft_T5_Pipeline_Optimization
type: plane
name: "Pipeline Optimization: AutoML/HPO → LLM Agents"
member_points: [P_draft_AutoML_HPO, P_draft_BCI_Bayesian_Opt, P_draft_Personalized_Pipeline_Search, P_draft_LLM_DS_Agent, P_draft_Greedy_Oracle, P_draft_LLM_Preproc_Agent]
adjacent_planes: [F_draft_T1_EEG_Preprocessing_Pipelines]
status: draft
created: 2026-06-20
updated: 2026-06-20
provenance:
  session_id: "20260620"
  source_files:
    - "step6_sota_review.md#theme-5"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# Plane T5: Pipeline Optimization — AutoML/HPO → LLM Agents / 管線最佳化

## Q1 解決什麼問題？
如何自動地選擇/設定資料處理管線的步驟與參數？此面供應計畫的**方法機制**（搜尋 + agent），並含計畫的兩大工程支柱。

## Q2 核心概念有哪些？
- [[P_draft_AutoML_HPO]] — 古典 AutoML/HPO 搜尋
- [[P_draft_BCI_Bayesian_Opt]] — EEG 專屬的貝氏最佳化前例
- [[P_draft_Personalized_Pipeline_Search]] — 逐實例預處理搜尋（最近前作）
- [[P_draft_LLM_DS_Agent]] — LLM ML 工程 agent（DS-Agent/SELA/AutoML-Agent）
- [[P_draft_Greedy_Oracle]] — 計畫支柱 1（ground truth）
- [[P_draft_LLM_Preproc_Agent]] — 計畫支柱 3（agent）

## Q3 概念之間關係？
兩代軌跡：古典 AutoML（2012–2020）→ LLM agents（2023–2025）（見 frontmatter_patches 的 parent 鏈）。Oracle 用搜尋建 ground truth；agent 學會廉價達到之。

## Q4 常用方法？
貝氏最佳化 / SMAC、meta-learning、ensemble（古典）；LLM controller + 工具呼叫 + 搜尋（CBR/MCTS/tree）+ 自我驗證（agent 世代）。

## Q5 常見錯誤？（verbatim from step7 GAP_003）
> 在全部 26 篇方法論文中，無一鎖定 EEG/神經生理預處理，也無一使用頻譜訊號品質作為獎勵。
> 第一代 EEG 工作（BashashatiEtAl2016）早於 FOOOF 且以任務準確率最佳化；第二代智能體 operate 於表格/程式碼/化學，從不在原始電生理上。

## 與其他 Plane 的關係
T5 透過 [[A_draft_Search_x_EEG]] 接到 T1（動作空間），以 T2 的 FOOOF-SNR 為目標——三面交會即計畫本身。
