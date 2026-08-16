---
schema_version: "1.0"
id: F_T2_Warmstart_Transfer_BO
type: plane
name: "Warm-Starting and Transfer Learning for Bayesian Optimization"
description: "Mechanisms that inject prior-task knowledge into BO — initialization, priors, surrogates, search spaces — and the similarity-gating/decay safeguards that bound negative transfer."
tags: []
status: active
created: 2026-08-15
updated: 2026-08-15
member_points: [P_Warm_Start_Initialization, P_MI_SMAC, P_PiBO, P_TransBO, P_Two_Stage_Transfer_Surrogate, P_Zero_Shot_Configuration_Transfer, P_Negative_Transfer, P_Similarity_Gating, P_Meta_Features]
adjacent_planes: [F_T1_LLM_for_AutoML, F_T3_CASH_Systems_Benchmarks, F_T5_CrossSubject_EEG]
related_body: [B_llm-warmstart-cash-mi-eeg-pipelines]
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-2"
    - "step7_gap_analysis.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# Warm-Starting and Transfer Learning for Bayesian Optimization / 貝葉斯最佳化的暖啟動與遷移學習

## 1. 解決什麼問題？

本平面回答：如何把先前任務的調參知識注入貝葉斯最佳化，使其在冷啟動最弱之處——大型、具條件結構搜尋空間中的最初數十次評估——獲得最大收益？十年證據匯聚於此：MI-SMBO 以元特徵相似資料集上的成功組態初始化 SMAC，在 57 個 OpenML 資料集中 35% 上顯著優於冷啟動 SMAC（僅 7% 較差），且此優勢幅度超過 SMAC 對隨機搜尋的優勢（FeurerEtAl2015）；πBO 的先驗加權擷取函數在 ImageNette 上達成 12.5 倍達標時間加速（HvarfnerEtAl2022）；HPO-B 基準上四種遷移方法全部顯著優於四種非遷移基線（ArangoEtAl2021）。

第二個同樣穩固、更具警示性的問題是：遷移何時會傷害？遷移助益與任務相似度成正比，缺乏防護的遷移可能有害——最優解偏移時天真重用來源分布會劇烈退化（NomuraEtAl2021）。因此幾乎所有現代方法都內建衰減或安全機制：πBO 的 β/n 先驗衰減、TransBO 的不劣於無遷移保證、空間遷移的相似度自適應「安全性」設計。對 EEG-MI 而言，這正是「其他受試者的搜尋歷史能否安全加速新受試者管線最佳化」的機制庫。

## 2. 核心概念有哪些？

- [[P_Warm_Start_Initialization]]：以先前任務的優勝組態作為 BO 初始評估點——最大收益出現在前數十次評估，是「初始化貢獻壓過搜尋精煉」的載體。
- [[P_MI_SMAC]]：奠基性實例——meta-feature 距離挑選 t=20 個暖啟動組態初始化 SMAC，50 次評估後仍顯著領先（FeurerEtAl2015）。
- [[P_PiBO]]：把任意使用者信念先驗變成擷取函數的一行修改，β/n 衰減保證從錯誤先驗恢復並保有 vanilla EI 收斂速率（HvarfnerEtAl2022）。
- [[P_TransBO]]：兩階段約束最佳化學習遷移權重，目標權重非遞減、理論上不劣於無遷移（LiEtAl2022c）。
- [[P_Two_Stage_Transfer_Surrogate]]：代理模型層級遷移——加權集成（TST/RGPE）到少樣本深度核 GP（FSBO），FSBO 在 HPO-B 上顯著最佳（WistubaGrabocka2021; ArangoEtAl2021）。
- [[P_Zero_Shot_Configuration_Transfer]]：完全不搜尋、直接套用推薦配置——符號式預設值（GijsbersEtAl2021; PfistererEtAl2018）到 TabPFN 2.8 秒勝過調參 4 小時集成（HollmannEtAl2025），挑戰 BO 迴圈本身的必要性。
- [[P_Negative_Transfer]]：遷移的失敗模式——不只是「任務不相似」，多任務 GP 在仿射相關任務上也錯估跨任務相關性，部分失敗是結構性的（HvarfnerEtAl2026; NomuraEtAl2021）。
- [[P_Similarity_Gating]]：依任務相似度門控／調節遷移強度——γ-相似度相關的 warm-CMA-ES、相似度自適應區域大小（NomuraEtAl2021; LiEtAl2022d）。
- [[P_Meta_Features]]：任務相似度的手工表徵（FeurerEtAl2015 用 46 個），與即時排序式相似度相互競爭——「往往難以取得且需精細人工設計」（LiEtAl2022c; LiEtAl2022d）。

## 3. 概念之間的關係

- **演化關係**：Meta_Features 查表（MI_SMAC, 2015–2018）→ 遷移進入 BO 結構（Two_Stage_Transfer_Surrogate、學習式搜尋空間, 2019–2021）→ 有原則的聚合與注入（TransBO、PiBO, 2022–2023）→ 預訓練情境內推論（OptFormer、TabPFN 式 Zero_Shot_Configuration_Transfer, 2024–2026）。
- **爭議關係（注入位置）**：BaiEtAl2023 形式化四個正交管道——初始點（Warm_Start_Initialization）、搜尋空間、代理模型（Two_Stage_Transfer_Surrogate）、擷取函數（PiBO）——幾乎無工作將其結合，「綜合框架」仍是開放問題；代理遷移派以 FSBO 為據，空間剪枝派主張普適可疊加（RGPE/TST 之上再降 10.1%/22.6% 誤差），先驗注入派主張完全不需來源觀測、實用性最高。
- **依賴／防護關係**：Warm_Start_Initialization、PiBO、TransBO 的可靠性皆依賴對 Negative_Transfer 的防護——Similarity_Gating 與衰減機制是前提而非選配。
- **對立關係**：Zero_Shot_Configuration_Transfer 質疑序列式暖啟動搜尋本身的必要性——是「不搜尋」端點，與 Warm_Start_Initialization 的「加速搜尋」構成部署光譜的兩極。
- **競爭關係**：Meta_Features 與排序式即時相似度是 Similarity_Gating 的兩種輸入來源，KDD 系列與 RijnHutter2017（無需元特徵的先驗）各執一端。

## 4. 常用方法

方法軌跡：手工元知識 → 可學習 → 預訓練遷移。**2015–2018**：meta-feature 距離挑選暖啟動組態（MI-SMBO）、互補式預設值組合（PfistererEtAl2018）、fANOVA 重要性排序與取值先驗（RijnHutter2017）。**2019–2021**：學習式包圍盒／橢球搜尋空間（PerroneEtAl2019）、KL 匹配初始化 CMA-ES（NomuraEtAl2021）、TST/RGPE 加權代理集成部署於 OpenBox（LiEtAl2021a）、FSBO 少樣本深度核 GP，HPO-B 提供首個大規模共同基準。**2022–2023**：TransBO 兩階段學習遷移權重、GPC 自適應空間設計、πBO 一行先驗注入、OptFormer 單一 Transformer 模仿 7 種 HPO 演算法。**2024–2026**：MCTS 自適應空間遷移、MaxUCB 極值賭博機、PFN 管線後驗取樣、表格基礎模型（HollmannEtAl2025），並出現檢視多任務 GP 根基的修正轉向（HvarfnerEtAl2026）。對 EEG-MI 的可移植啟示：來源任務（其他受試者）充足、目標預算僅數十次評估時，πBO 式先驗注入與 FSBO 式少樣本代理是兩種已被證明有效的機制。

## 5. 常見錯誤

- **無門控的天真遷移**（step6 Theme 2 Consensus/Key Results）：「當最優解偏移時，天真地重用來源任務分布會造成劇烈退化」（NomuraEtAl2021，ReuseNormal 在相似度外劇烈退化）；「Feurer 等人自己的圖表也顯示元學習在某些資料集上反而降低了 SMAC 的表現」（FeurerEtAl2015）。
- **把負遷移全歸因於任務不相似**（step7 GAP_002 證據，逐字引用）：「**HvarfnerEtAl2026**: Shows multi-task GP surrogates misestimate cross-task correlation even for affinely related tasks — negative transfer is partly structural, favoring prior/space-level over surrogate-level transfer in high-variability settings like EEG. / 顯示多任務 GP 即使在仿射相關任務上也會錯估跨任務相關——負遷移部分是結構性的，在 EEG 這類高變異情境更支持先驗／空間層級而非代理模型層級的遷移。」
- **忽視離群任務即直接遷移**（step7 GAP_002 證據，逐字引用）：「**ZhangEtAl2024 + BerdyshevEtAl2024**: Both document extreme outlier subjects (feature deviations up to ~30-fold), directly motivating similarity gating before transfer. / 兩者都記錄極端離群受試者，直接支持遷移前的相似度門控。」
- **元層級程序本身過擬合**（step6 Theme 2 Debates）：「BasgaluppEtAl2020 並警示元層級程序本身也可能過擬合（Auto-WEKA 的『元過擬合』）。」
- **迷信單一通用初始設計**（step6 Theme 2 Key Results）：BossekEtAl2020 在 720 個 BBOB 問題 × 40 種初始設計上顯示「整體而言小初始設計較佳，但 40 種設計中每一種都在至少 1 個問題上最佳——不存在通用初始化器」。

## 與其他 Plane 的關係

- [[F_T1_LLM_for_AutoML]]：LLM 是本平面先驗注入管道的新來源——不需歷史觀測、只需信念分布的 πBO 式機制，恰好是 LLM 語意先驗最自然的進入點；本平面的衰減／門控設計則是防範 LLM 先驗未校準的既有解法。
- [[F_T3_CASH_Systems_Benchmarks]]：本平面機制的施用對象——成熟 CASH 系統（auto-sklearn 元學習、VolcanoML RGPE/RankNet）已內建暖啟動並一致報告有益，且系統端明確把 meta-learning 暖啟動列為下一個加速槓桿。
- [[F_T5_CrossSubject_EEG]]：遷移機制 × 受試者變異的交會——EEG 受試者構成天然的「相關但偏移」任務家族，是本平面門控機制最困難的現實測試場；而 T5 記錄的 ~30 倍離群受試者正是 Negative_Transfer 風險的具體化。
