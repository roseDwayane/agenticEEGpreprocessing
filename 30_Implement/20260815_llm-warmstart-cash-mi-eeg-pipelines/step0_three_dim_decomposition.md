# Step 0 — 三維需求拆解

> 假說來源：`10_Research/20260815_llm-warmstart-cash-mi-eeg-pipelines/step8_hypothesis_specification.md`
> 知識錨定：見 `step0_knowledge_anchors.md`（38 卡，score 3 × 28）
> 一句話需求：建構一套「知識注入暖啟動分解式 CASH」實驗系統，在受試者特定 EEG MI 前處理＋解碼管線空間上，以預算對齊協議檢驗 H1（≥2× 達標加速）、H2（零樣本遷移＋門控防負遷移）、H3（seeded 對照下先驗形狀增益存活）。

---

## 一、功能需求維度（Functional Requirements）

本系統服務面對 [[P_BCI_Calibration_Problem]] 所述「每位新使用者都需重新收集資料與重新調校」瓶頸的 BCI／EEG 研究者。要解的問題：社群慣用的固定標準管線並非逐受試者最優（[[P_Standard_MI_Pipeline]]），而受試者間特徵偏差可達約 30 倍（[[P_Inter_Subject_Variability]]），使「一套配置走天下」不可行、逐受試者冷啟動搜尋又太昂貴。交付價值有三層：(1) 以跨受試者搜尋歷史（[[P_MI_SMAC]] 式）與 LLM 引出先驗（[[P_LLM_Prior_Elicitation]]＋[[P_PiBO]]）暖啟動搜尋，用更少評估次數達到冷啟動最優；(2) 刻畫「不搜尋」到「完整搜尋」的操作曲線，含零樣本配置遷移與相似度門控（[[P_Zero_Shot_Configuration_Transfer]]、[[P_Similarity_Gating]]）；(3) 產出可稽核的預算對齊評估結果與可釋出的搜尋歷史基準資產（[[P_Budget_Matched_Protocol]]、[[P_Anytime_Performance]]）。

## 二、系統架構維度（System Architecture）

核心領域邏輯（純運算，不依賴外部服務）：(a) 分解式 CASH 搜尋迴圈——五階段管線空間的樹狀執行計畫與條件式搜尋空間定義（[[P_Search_Space_Decomposition]]、[[P_CASH]]）；(b) 先驗建構——從搜尋歷史或 LLM 回應建出 per-hyperparameter 先驗與 top-k 初始設計，沿 initial points 與 acquisition 兩管道注入（[[P_Warm_Start_Initialization]]）；(c) 相似度門控 g(s)——以受試者 meta-features 計算並衰減先驗（[[P_Meta_Features]]、[[P_Similarity_Gating]]）；(d) 評估協議——預算對齊、seeded 對照、anytime 曲線與統計檢定（[[P_Seeded_Baseline]]、[[P_Anytime_Performance]]）。外部依賴（邊緣基礎設施）：BO 後端（SMAC3／OpenBox，[[P_SMAC]]）、EEG 資料載入與訊號處理工具鏈、LLM API（僅提示式引出，[[P_LLM_Prior_Elicitation]]）、評估快取與平行執行設施。具體框架選型與 Onion 分層留待 Step 2。

## 三、工程過程維度（Engineering Process）

本需求橫跨完整 SDLC：需求工程（本 pipeline）→ 系統設計（搜尋空間與先驗機制規格）→ 開發實作 → 系統測試（統計驗證即核心測試）→ 部署維運（以「可釋出的搜尋歷史基準資產」形式交付，非線上服務）。特殊風險：(1) 科學效度風險——暖啟動增益可能化約為單一好預設，需 seeded 對照內建於設計（[[P_Seeded_Baseline]]、[[D_Are_LLM_HPO_Gains_Real]]）；(2) 負遷移風險——離群受試者上先驗可能有害（[[P_Negative_Transfer]]）；(3) 外部依賴重現性風險——LLM 先驗品質隨骨幹與提示不穩（[[P_Backbone_Capacity_Threshold]]），需固定提示模板並保留不依賴 LLM 的歷史先驗對照臂；(4) 計算成本風險——109 名受試者搜尋歷史建置昂貴（假說風險表），需評估快取、LOSO 汙染控制與平行執行；(5) 效應過小風險——成熟最佳化器可能統計無差異（[[P_Optimizer_Indistinguishability]]），以 N=109 成對檢定力與預註冊終點緩解。

---

## 2D Intuitive Seeds（2D 直覺種子）

> ⚠️ 本段為 LLM 初步直覺，可能含幻覺；正式版在 Step 3/5/6 完成，屆時會在 `_seed_corrections.md` 記錄差異。

### DDD Seed（需求 × 架構）

直覺切出三個 bounded context：「搜尋 context」（分解式 CASH 迴圈＋管線評估）、「知識注入 context」（歷史／LLM 先驗建構＋相似度門控）、「實驗評估 context」（預算對齊協議＋統計檢定＋基準資產）。（derived from [[P_Search_Space_Decomposition]] + [[P_Warm_Start_Initialization]] + [[P_Budget_Matched_Protocol]]）

→ Refined in `step3_ddd_model.md` §Bounded Contexts

### DevOps Seed（架構 × 過程）

直覺風險點：BO 隨機性使測試需固定 seed；LLM API 為最不穩外部依賴，單元測試全面 mock、先驗解析層設 schema 驗證；管線評估昂貴，以小型合成 EEG fixture 跑快速迴歸、真實資料搜尋放 nightly 長測。CI 為單機輕量拓樸。（derived from [[P_Backbone_Capacity_Threshold]] + [[P_SMAC]]）

→ Refined in `step5_devops_strategy.md`

### Agile Seed（需求 × 過程）

直覺 4 個 sprint：S1 冷啟動分解式 CASH＋評估協議骨架（RQ1）；S2 建置搜尋歷史資產＋歷史先驗暖啟動（H1 臂一）；S3 LLM 先驗引出＋門控＋零樣本遷移（H1 臂二＋H2）；S4 seeded 對照消融＋全量統計（H3）。關鍵依賴：歷史資產先於一切遷移實驗。（derived from [[P_MI_SMAC]] + [[P_Seeded_Baseline]]）

→ Refined in `step6_sprint_plan.md`
