---
schema_version: "1.0"
id: F_draft_T1_LLM_for_AutoML
type: plane
name: "LLMs as Optimizers and Prior Injectors for AutoML/HPO"
description: "How large language models act as warm-starters, prior injectors, and gated collaborators (never standalone surrogates) inside classical BO/CASH optimization loops."
tags: []
status: draft
created: 2026-08-15
updated: 2026-08-15
member_points: [P_draft_LLAMBO, P_draft_LLM_Prior_Elicitation, P_draft_CoFEH, P_draft_Backbone_Capacity_Threshold, P_draft_Budget_Matched_Protocol]
adjacent_planes: [F_draft_T2_Warmstart_Transfer_BO, F_draft_T3_CASH_Systems_Benchmarks, F_draft_T5_CrossSubject_EEG]
related_body: [B_draft_llm-warmstart-cash-mi-eeg-pipelines]
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-1"
    - "step7_gap_analysis.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# LLMs as Optimizers and Prior Injectors for AutoML/HPO / LLM 作為 AutoML/HPO 的最佳化器與先驗注入者

## 1. 解決什麼問題？

本平面回答的核心問題是：LLM 在超參數最佳化（HPO）與 CASH 中到底能扮演什麼角色？文獻已收斂到一個細緻的立場——LLM 對搜尋的價值集中在「起始階段」：LLAMBO 的零樣本暖啟動在 74 個 Bayesmark/HPOBench 任務上降低早期遺憾與跨執行變異，增益在第 5 次試驗前最為顯著（LiuEtAl2024），且開放權重 Llama 3.1 70B 的獨立重現研究證實情境式暖啟動確實改善早期行為（RychertEtAl2025）。但 LLM 作為「獨立」代理模型表現不佳：校準弱於 GP、單任務迴歸弱於 SMAC，其優勢來自跨任務語意先驗而非迴歸精度；CASH 相關研究直言「BO 仍是 HPO 的黃金標準」（XuEtAl2026a; GuptaEtAl2025）。

因此本平面同時處理兩個對立張力：一方面 2026 年最強系統（CoFEH、LB-MCTS）採取混合式設計——LLM 作為先驗注入者、候選生成者或回饋轉譯者，包覆於古典 BO/TPE/MCTS 引擎之外，並在設計上即假定 LLM 可能出錯（閘控、條件化）；另一方面預算對齊稽核（RodriguesEtAl2026）警告：表面上的 LLM 暖啟動效益有相當部分可化約為一個好的預設配置。這個「效益真實但前置、且需嚴格對照才能證明」的立場，正是把 LLM 先驗帶入 EEG 管線最佳化前必須內化的方法學基礎。

## 2. 核心概念有哪些？

- [[P_draft_LLAMBO]]：LLM 全面嵌入 BO 三元件（暖啟動、ICL 代理模型、條件取樣器）的代表系統；增益集中於第 5 次試驗前，GP 仍是校準最佳者（LiuEtAl2024; RychertEtAl2025）。
- [[P_draft_LLM_Prior_Elicitation]]：從自然語言誘出結構化先驗——RAG 轉 Dirichlet 先驗（TopalisEtAl2025）、偏好學習效用先驗（PatelEtAl2025）、SHAP 後設特徵零樣本推薦（Bal-GhaouiTiouti2025）；誘出的信念對提示措辭高度敏感（LeiCooper2026）。
- [[P_draft_CoFEH]]：2026 年協作式 CASH 趨勢的代表——LLM 思維樹特徵工程與 SMAC 式 BO 相互條件化，28 個資料集平均排名 1.75、CASH 情境最高 +45.1% 誤差降低（XuEtAl2026a）。
- [[P_draft_Backbone_Capacity_Threshold]]：效果強烈依賴模型容量——Gemma 27B 與 Llama 3.1 8B 產生不穩定、格式錯誤的代理行為，7 個顧問模型僅 2 個逃離探索陷阱（RychertEtAl2025; RodriguesEtAl2026）。
- [[P_draft_Budget_Matched_Protocol]]：預算對齊、多種子、種子化對照的稽核協定——揭露 LLM 顧問的首個點是固定預設配置，LLM 自身提案僅 +0.40 pp CV、測試集 −0.01 pp（RodriguesEtAl2026）。

## 3. 概念之間的關係

- **挑戰／修正關係**：Budget_Matched_Protocol 直接挑戰 LLAMBO 一系的暖啟動敘事——一旦古典搜尋獲得相同預設種子，顧問系統 12 次評估後即落後（RodriguesEtAl2026 vs LiuEtAl2024）；但在 LLAMBO 原始協定下的重現消融仍發現真實脈絡效應（RychertEtAl2025），顯示分歧部分來自協定設計而非原始結果。
- **依賴關係**：LLAMBO 與 LLM_Prior_Elicitation 的效果皆以 Backbone_Capacity_Threshold 為前提——骨幹容量不足時，暖啟動與先驗誘出直接失效。
- **演化關係**：時間軌跡從 LLM-as-optimizer 的熱情（Kannan2023; LiuEtAl2024, 2023–2024）→ 冷靜稽核與預算對齊否定結果（KristiadiEtAl2024; RodriguesEtAl2026, 2024–2026）→ 閘控式結構化混合（ChenYi2026; XuEtAl2026b），CoFEH 即此第三階段在 CASH 規模的具體化。
- **層級關係**：LLM_Prior_Elicitation 是機制層（如何取得先驗），LLAMBO 與 CoFEH 是系統層（先驗如何進入迴圈），Budget_Matched_Protocol 是評估層（增益是否成立）。

## 4. 常用方法

文獻依五種 LLM 角色歸類：(1) 暖啟動者／先驗注入者——零樣本初始組態提案、搜尋空間縮小、RAG→Dirichlet 結構化先驗（LiuEtAl2024; Kannan2023; TopalisEtAl2025）；(2) 代理模型／擷取函數元件——ICL 判別式代理、固定特徵擷取器餵入貝葉斯代理、微調 Thompson 抽樣（LiuEtAl2024; KristiadiEtAl2024; MenetEtAl2025）；(3) 古典迴圈內的候選生成者——LLM-TPE 混合取樣器、GA 介入打破 LLM 重複迴圈（MahammadliErtekin2024; XuEtAl2025a）；(4) 代理人／協調者——MCP agent 呼叫外部最佳化器、雙代理暖啟動加精煉（WangEtAl2025; ChenEtAl2025a）；(5) 協作式管線／CASH 最佳化器——CoFEH 交錯條件化、LB-MCTS 共享 MCTS 狀態（XuEtAl2026a; XuEtAl2026b）。主流基準：Bayesmark/HPOBench、PMLB、OpenML 衍生表格資料集、BBOB。

## 5. 常見錯誤

- **把固定預設配置的功勞歸給 LLM**（RodriguesEtAl2026, step7 GAP_003 證據，逐字引用）：「**RodriguesEtAl2026**: Budget-matched protocol shows LLM advisor gains reduce to the fixed first configuration (+0.40 pp CV, −0.01 pp test, p=0.92); seeded classical search overtakes within 12 evaluations — the exact confound an EEG study must control. / 預算對齊協議顯示 LLM 顧問增益化約為固定首配置；有 seed 的傳統搜尋 12 次評估內反超——EEG 研究必須控制的混淆正是這個。」
- **誤信 LLM 在情境中真的在「最佳化」**（step6 Theme 1 Debates）：在科學發現任務中「以隨機置換的標籤取代真實實驗結果對 LLM 代理的表現毫無影響，而線性 bandit 與 GP 最佳化持續勝出」（GuptaEtAl2025）；LLM 僅在約十餘個維度以下具競爭力（SrinivasanMenzies2026）；分子 BO 中 LLM 僅在經領域資料預訓練或微調後才有幫助（KristiadiEtAl2024）。
- **未經校準即信任 LLM 先驗與自述信心**（step6 Theme 1 Debates）：「LLM 先驗及其自述信心可能未經校準」，促生證據閘控、可否證的先驗加權（ChenYi2026），且「誘出的代理信念對提示措辭與查詢協定高度敏感」（LeiCooper2026）——與每次迭代直接注入 LLM 偏好的框架（YuanEtAl2026）存在張力。
- **忽略骨幹容量門檻**（step6 Theme 1 Consensus/Debates）：「Gemma 27B 與 Llama 3.1 8B 產生不穩定、格式錯誤的代理行為」（RychertEtAl2025），「7 個顧問模型中僅 2 個逃離已知的探索陷阱」（RodriguesEtAl2026）。

## 與其他 Plane 的關係

- [[F_draft_T2_Warmstart_Transfer_BO]]：本平面的 LLM 先驗正是 T2 四個遷移管道中「初始點設計／先驗注入」管道的新知識來源——LLM 先驗可直接走 πBO 式加權擷取函數進入 BO，等於以語意知識替代歷史任務資料。
- [[F_draft_T3_CASH_Systems_Benchmarks]]：CoFEH 與 LB-MCTS 把 LLM 接到 T3 的 SMAC/CASH 機制上；同時 T3 的基準方法學（種子化對照、anytime 曲線）是檢驗本平面任何宣稱的裁判。
- [[F_draft_T5_CrossSubject_EEG]]：ChatBCI（KapitonovaBall2024）提供 LLM 先驗進入 EEG 管線的介面，其未來工作明確呼籲 LLM 驅動的 HPO/NAS——本平面的機制經此介面落地到跨受試者 EEG 情境。
