# Knowledge Anchors — 知識注入暖啟動分解式 CASH 之受試者特定 EEG MI 管線最佳化

> 來源範圍：`20_Knowledge/`（全庫）
> 掃描卡片數：38（過濾後保留：38）
> 假說來源：`10_Research/20260815_llm-warmstart-cash-mi-eeg-pipelines/step8_hypothesis_specification.md`

## 高度相關（score 3）

### Body / Planes（系統與理論面）
- `[[B_llm-warmstart-cash-mi-eeg-pipelines]]` — 本假說對應的 prescriptive 研究計畫系統卡，即為本次工程化的直接對象（path: `20_Knowledge/40-Body/B_llm-warmstart-cash-mi-eeg-pipelines.md`）
- `[[F_T2_Warmstart_Transfer_BO]]` — 知識注入 BO 的四管道機制與門控／衰減防護，本研究的方法脊柱（path: `20_Knowledge/30-Planes/F_T2_Warmstart_Transfer_BO.md`）
- `[[F_T1_LLM_for_AutoML]]` — LLM 作為 warm-starter／prior injector 的角色定位（永不獨立作 surrogate）（path: `20_Knowledge/30-Planes/F_T1_LLM_for_AutoML.md`）
- `[[F_T3_CASH_Systems_Benchmarks]]` — CASH 形式化、求解系統與基準協議（path: `20_Knowledge/30-Planes/F_T3_CASH_Systems_Benchmarks.md`）
- `[[F_T4_EEG_Pipeline_Automation]]` — EEG 管線自動化現況：冷啟動演化式引擎主導、BO-CASH 缺席（本缺口所在）（path: `20_Knowledge/30-Planes/F_T4_EEG_Pipeline_Automation.md`）
- `[[F_T5_CrossSubject_EEG]]` — 跨受試者只遷移權重、不遷移配置的現況（path: `20_Knowledge/30-Planes/F_T5_CrossSubject_EEG.md`）

### Points（核心機制）
- `[[P_CASH]]` — CASH 形式化：演算法選擇＋HPO 合併為單一階層式黑箱最佳化（path: `20_Knowledge/10-Points/P_CASH.md`）
- `[[P_Search_Space_Decomposition]]` — VolcanoML 樹狀分解執行計畫，本系統的搜尋框架基礎（path: `20_Knowledge/10-Points/P_Search_Space_Decomposition.md`）
- `[[P_SMAC]]` — random forest surrogate BO，處理條件式 CASH 空間的主流引擎（後端）（path: `20_Knowledge/10-Points/P_SMAC.md`）
- `[[P_MI_SMAC]]` — meta-features 挑過往最優配置作初始設計：知識注入機制 (a) 的原型（path: `20_Knowledge/10-Points/P_MI_SMAC.md`）
- `[[P_PiBO]]` — acquisition 上乘衰減先驗項：知識注入機制 (b) 的載體，錯誤先驗可恢復（path: `20_Knowledge/10-Points/P_PiBO.md`）
- `[[P_LLM_Prior_Elicitation]]` — 以提示從 LLM 引出超參數先驗分布：πBO 先驗的知識來源（path: `20_Knowledge/10-Points/P_LLM_Prior_Elicitation.md`）
- `[[P_LLAMBO]]` — LLM 插入 BO 各階段，warm-starting 為最穩健增益（path: `20_Knowledge/10-Points/P_LLAMBO.md`）
- `[[P_Warm_Start_Initialization]]` — 四個正交注入管道的傘狀分類，本研究結合 initial points＋acquisition 兩管道（path: `20_Knowledge/10-Points/P_Warm_Start_Initialization.md`）
- `[[P_Meta_Features]]` — 任務相似度座標系，相似度門控 g(s) 的輸入（path: `20_Knowledge/10-Points/P_Meta_Features.md`）
- `[[P_Similarity_Gating]]` — 依相似度調節暖啟動強度、低相似度退回冷啟動（path: `20_Knowledge/10-Points/P_Similarity_Gating.md`）
- `[[P_Negative_Transfer]]` — 負遷移風險：部分遷移失敗非任務不相似所致（H2 門控終點的動機）（path: `20_Knowledge/10-Points/P_Negative_Transfer.md`）
- `[[P_Zero_Shot_Configuration_Transfer]]` — 零樣本配置遷移與其未測保留率（GAP_002／H2 主體）（path: `20_Knowledge/10-Points/P_Zero_Shot_Configuration_Transfer.md`）
- `[[P_Budget_Matched_Protocol]]` — 預算對齊＋seeded 對照協議（GAP_003／評估方法學骨架）（path: `20_Knowledge/10-Points/P_Budget_Matched_Protocol.md`）
- `[[P_Seeded_Baseline]]` — 裁決「好預設 vs 先驗形狀」的對照工具（H3 核心對照臂）（path: `20_Knowledge/10-Points/P_Seeded_Baseline.md`）
- `[[P_Anytime_Performance]]` — incumbent 曲線與 AUC 作主要結果指標（path: `20_Knowledge/10-Points/P_Anytime_Performance.md`）
- `[[P_Subjects_as_Tasks]]` — 受試者即任務的橋接概念，本研究的核心框架主張（path: `20_Knowledge/10-Points/P_Subjects_as_Tasks.md`）
- `[[P_Inter_Subject_Variability]]` — 受試者間約 30 倍特徵偏差：單一配置不可通用的動機證據（path: `20_Knowledge/10-Points/P_Inter_Subject_Variability.md`）
- `[[P_Standard_MI_Pipeline]]` — 8–30 Hz FIR＋CSP＋LDA 標準管線：固定 baseline 且「標準≠最優」動機（path: `20_Knowledge/10-Points/P_Standard_MI_Pipeline.md`）
- `[[P_BCI_Calibration_Problem]]` — 每受試者重新調校的部署瓶頸：本系統的使用者價值所在（path: `20_Knowledge/10-Points/P_BCI_Calibration_Problem.md`）

### Lines（辯論／類比／演化）
- `[[D_Are_LLM_HPO_Gains_Real]]` — LLM HPO 增益真偽之辯：H3 直接檢驗此辯論（path: `20_Knowledge/20-Lines/D_Are_LLM_HPO_Gains_Real.md`）
- `[[A_Weight_Init_vs_Config_Init]]` — 權重初始化 ≅ 配置初始化類比：EEG 端配置初始化空位即本缺口（path: `20_Knowledge/20-Lines/A_Weight_Init_vs_Config_Init.md`）
- `[[E_Knowledge_Injection_Level_Ascent]]` — 知識注入口 2015–2026 演化脈絡：本研究同時佔據初始設計層與 acquisition 層（path: `20_Knowledge/20-Lines/E_Knowledge_Injection_Level_Ascent.md`）

## 中度相關（score 2）

- `[[P_Backbone_Capacity_Threshold]]` — 70B 以下骨幹輸出畸形：OUT-scope（不用小模型）的證據基礎（path: `20_Knowledge/10-Points/P_Backbone_Capacity_Threshold.md`）
- `[[P_Optimizer_Indistinguishability]]` — 成熟 CASH 最佳化器統計無差異：效應過小風險的來源（path: `20_Knowledge/10-Points/P_Optimizer_Indistinguishability.md`）
- `[[P_TransBO]]` — 兩階段可學權重遷移 HPO：surrogate 層遷移的替代路線（本研究未採，供對照）（path: `20_Knowledge/10-Points/P_TransBO.md`）
- `[[P_Two_Stage_Transfer_Surrogate]]` — 早期 surrogate 遷移法：演化脈絡中的中間層（path: `20_Knowledge/10-Points/P_Two_Stage_Transfer_Surrogate.md`）
- `[[P_EEG_Reptile]]` — 權重層級跨受試者遷移的天花板證據：配置層級路線的對照組（path: `20_Knowledge/10-Points/P_EEG_Reptile.md`）
- `[[P_Rising_Bandits]]` — 非平穩 bandit 演算法淘汰：分解式 CASH 的相鄰求解策略（path: `20_Knowledge/10-Points/P_Rising_Bandits.md`）
- `[[P_Hyperband]]` — 多保真資源分配：未採用但為預算概念的相鄰機制（path: `20_Knowledge/10-Points/P_Hyperband.md`）
- `[[P_CoFEH]]` — LLM 特徵工程與 SMAC-BO 交錯：LLM×CASH 的相鄰整合路線（path: `20_Knowledge/10-Points/P_CoFEH.md`）
- `[[P_ChatBCI]]` — LLM agent 操作 BCI 工具鏈：LLM 進入 EEG 管線的介面先例（path: `20_Knowledge/10-Points/P_ChatBCI.md`）
- `[[D_Does_The_Optimizer_Matter]]` — 最佳化器是否重要之辯：情境依賴增益的風險框架（path: `20_Knowledge/20-Lines/D_Does_The_Optimizer_Matter.md`）

## 低度相關（score 1）

（無——本知識庫由同一研究 session 橋接產生，所有卡片均達 score ≥ 2。）
