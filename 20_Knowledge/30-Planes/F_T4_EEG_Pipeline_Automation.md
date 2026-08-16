---
schema_version: "1.0"
id: F_T4_EEG_Pipeline_Automation
type: plane
name: "Automated Optimization of EEG Decoding Pipelines"
description: "Automated search over EEG architectures, hyperparameters, and full pipelines — a field dominated by cold-start evolutionary engines, where Bayesian CASH and warm-starting have not yet arrived."
tags: []
status: active
created: 2026-08-15
updated: 2026-08-15
member_points: [P_Standard_MI_Pipeline]
adjacent_planes: [F_T5_CrossSubject_EEG]
related_body: [B_llm-warmstart-cash-mi-eeg-pipelines]
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-4"
    - "step7_gap_analysis.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# Automated Optimization of EEG Decoding Pipelines / EEG 解碼管線的自動化最佳化

> [!note] 證據層級 / Evidence level
> 本主題 11 篇論文皆僅有摘要層級內容，以下論述僅限摘要中明確報告的數字與主張，權重應相應調降（step6 Theme 4 note）。

## 1. 解決什麼問題？

本平面回答：EEG 解碼管線（前處理＋特徵萃取＋分類，或神經架構本身）能否、以及如何自動化搜尋？共識有四：自動化搜尋以不容忽視的幅度穩定勝過人工配置——MDH-NAS 較基線 +8.70% 準確率並縮短 89% 架構搜尋時間（ZhuEtAl2025）、DE 對 LSTM 的 HPO 帶來 +14 個百分點（NakisaEtAl2018）、GA「在所有受測模型上皆提升準確率」（LeonEtAl2020）；受試者間變異是自動化的核心理由，通用架構被視為不切實際而改為逐受試者搜尋（WangEtAl2026; DuanEtAl2023）；搜尋能跳脫人類先驗——最佳超參數「遠超出批次或手動搜尋的範圍」（BirdLotfi2023）；搜尋成本被視為一級約束。

本平面同時是整份文獻集的「不對稱」所在：最佳化器壓倒性地屬演化式（GA、DE、GP 等，11 篇中 6 篇），貝葉斯最佳化（SMAC 式 CASH）從未作為主要引擎出現、僅以被擊敗的 TPE 基線登場（NakisaEtAl2018）；逐受試者 NAS 皆無暖啟動，完全沒有 LLM 引導或貝葉斯 CASH 的工作——本 session 研究問題所瞄準的交集正是空白。EEG 管線搜尋也沒有自己的 auto-sklearn/VolcanoML（step7 缺口全景）。

## 2. 核心概念有哪些？

- [[P_Standard_MI_Pipeline]]：MI-EEG 的標準管線分層——(i) 神經架構（濾波器組時間單元、DARTS 式單元、脈衝拓撲）、(ii) 固定模型族超參數（LSTM／淺層 CNN／MLP）、(iii) 涵蓋特徵萃取、降維與分類器選擇的完整管線——第三層是最接近 CASH 的類比，但從未以 CASH 框架表述（MirandaEtAl2022; BirdLotfi2023）。
- **（結構註記）BO 缺席、演化式主導**：本平面成員點刻意單薄，反映領域現狀——搜尋引擎由演化式方法主導（6/11），BO 家族只以被差分演化擊敗的 TPE 基線出現；CASH、暖啟動、LLM 引導等概念在本平面內「應存在而不存在」，其缺席本身是知識點。

## 3. 概念之間的關係

- **層級關係**：Standard_MI_Pipeline 的三層搜尋對象（架構／超參數／完整管線）構成本平面內部的粒度階梯；第三層（完整管線）與 T3 的 CASH 形式化同構，卻無任何論文以 CASH 語彙連接兩者。
- **對立關係（複雜度是否划算）**：LeonEtAl2020 的淺層 GA 調校網路（單卷積層 CNN 追平六層 DBN）隱含挑戰 WangEtAl2026/ZhuEtAl2025/DuanEtAl2023 花費大量算力搜尋深層架構的 NAS 路線，並將深度模型困境歸因於小型 EEG 資料集上的過擬合。
- **對立關係（逐受試者 vs. 池化）**：逐受試者 NAS 使算力隨群體規模倍增，跨任務共享搜尋空間與低程式碼固定管線攤提成本；摘要層級無法判定何者更具成本效益。
- **鄰近而未觸及**：最接近暖啟動的先例是權重層級遷移（EMG 預訓練初始化 +29.95 個百分點；BirdEtAl2020）與免訓練代理（LiEtAl2025d）——兩者遷移模型參數或訊號，而非最佳化器先驗或組態；零樣本「組態」遷移在此完全未被檢驗。

## 4. 常用方法

搜尋對象分三層：(i) 神經架構——含膨脹卷積的濾波器組時間單元（WangEtAl2026）、顯式尺寸約束下的 DARTS 式混合層級單元（ZhuEtAl2025）、跨任務單元空間（DuanEtAl2023）、脈衝 CNN/LSTM 拓撲（LiEtAl2025d）；(ii) 固定模型族超參數——LSTM（NakisaEtAl2018）、淺層 CNN/FFNN/RNN（LeonEtAl2020）；(iii) 完整管線——特徵萃取＋降維＋分類器（MirandaEtAl2022; BirdLotfi2023）。最佳化器：演化式壓倒性主導（GA、DE、GP、超啟發式多目標、免訓練基因搜尋），梯度式為少數（可微分 NAS、多路徑 NAS），BO 僅以 TPE 基線出現。基準集中於 BCI Competition IV-2a，另有 OpenBMI/SEED、PhysioNet EEGMMI（裝置端 85.37%）、情緒語料 FACED/DEAP/DREAMER。評估協定各異（跨會期、受試者內、嵌入式部署），阻礙直接比較；CraikEtAl2019 對 90 篇研究的回顧提供人工設計基線。

## 5. 常見錯誤

- **每個資料集／受試者都冷啟動重跑、丟棄搜尋知識**（step7 GAP_001 證據，逐字引用）：「**ZhangEtAl2024**: Cold-restarts its metaheuristic HPO for every dataset — search knowledge is discarded between runs. / 每個資料集都重新冷啟動其 metaheuristic HPO——搜尋知識在批次之間被丟棄。」
- **低估冷啟動 HPO 的實際成本**（step7 GAP_001 證據，逐字引用）：「**BerdyshevEtAl2024**: Reports ~24 hours per automated HPO setting for BCI meta-learning, and names cutting this cost as future work. / 回報 BCI meta-learning 的自動化 HPO 每一設定約需 24 小時，並將降低此成本列為未來工作。」
- **假定主流 AutoML 引擎已在 EEG 驗證**（step7 GAP_001 證據，逐字引用）：「**Theme 4 collectively (11 papers)**: Zero papers combine LLM guidance or warm-started search with EEG pipeline optimization; NakisaEtAl2018 includes TPE only as a baseline that differential evolution beats. / 主題四全體：無任何論文結合 LLM 引導或暖啟動搜尋與 EEG 管線最佳化；NakisaEtAl2018 的 TPE 僅是被差分演化擊敗的 baseline。」
- **不報達標評估次數或預算對齊比較**（step7 GAP_003 證據，逐字引用）：「**Theme 4 collectively**: None of the 11 EEG automation papers reports evaluations-to-target or budget-matched comparisons. / 主題四 11 篇 EEG 自動化論文皆未回報達標評估次數或預算對齊比較。」
- **在小型 EEG 資料集上搜尋深層複雜模型而不防過擬合**（step6 Theme 4 Debates）：LeonEtAl2020 顯示刻意淺層、GA 調校的網路可比肩更深的模型，「並將深度模型的困境歸因於小型 EEG 資料集上的過擬合」；CooneyEtAl2020 亦「明確指出 DL-EEG 中 HPO 效果的統計顯著性不確定且未被充分檢驗」（step7 GAP_003 證據）。

## 與其他 Plane 的關係

- [[F_T5_CrossSubject_EEG]]：T5 的受試者間變異是本平面「逐受試者搜尋」路線的科學理由；本平面的冷啟動搜尋成本則是 T5 校正問題的計算面——兩者合起來構成「受試者特定管線值得做、但目前貴到不可部署」的完整敘事。
