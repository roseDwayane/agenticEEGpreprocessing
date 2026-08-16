---
schema_version: "1.0"
id: F_T3_CASH_Systems_Benchmarks
type: plane
name: "CASH, AutoML Systems, and Benchmarking Infrastructure"
description: "The formalization of combined algorithm selection and hyperparameter optimization, the systems that solve it (joint BO, multi-fidelity bandits, decomposition), and the benchmark protocols that judge whether optimizer claims hold."
tags: []
status: active
created: 2026-08-15
updated: 2026-08-15
member_points: [P_CASH, P_SMAC, P_Search_Space_Decomposition, P_Rising_Bandits, P_Hyperband, P_Optimizer_Indistinguishability, P_Anytime_Performance, P_Seeded_Baseline]
adjacent_planes: [F_T1_LLM_for_AutoML, F_T2_Warmstart_Transfer_BO]
related_body: [B_llm-warmstart-cash-mi-eeg-pipelines]
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-3"
    - "step7_gap_analysis.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# CASH, AutoML Systems, and Benchmarking Infrastructure / CASH、AutoML 系統與基準評測基礎設施

## 1. 解決什麼問題？

本平面回答：如何把「同時選擇演算法與其超參數」（CASH）做成可擴展的系統，並如何可信地評測這些系統？CASH 自 Auto-WEKA 以來被視為可由 BO 求解的雙層／黑箱最佳化問題，是管線最佳化的典範形式化（KotthoffEtAl2019; LindauerEtAl2021; HutterEtAl2019）。系統演化的核心發現有三：自適應最佳化器平均而言勝過樸素基線但並非普遍如此（HPOBench 上 4/5 黑箱最佳化器顯著優於隨機搜尋，但僅 2/4 多保真方法勝過純 Hyperband）；多保真／提早停止的增益集中於低預算階段（Hyperband 比 BO 快 5–30 倍，BOHB 最快 100 倍達到 Hyperband 最終品質，大預算下黑箱方法趕上）；對大型端到端空間而言分解優於聯合搜尋（VolcanoML 平均排名 1.65 對 auto-sklearn 3.57；Rising Bandits 比 SMAC 少約 12.6 倍試驗）。

同樣重要的是基準端的冷水：在 114 個資料集上多數 CASH 最佳化器統計上無法區分、隨機搜尋不遜色（ZollerHuber2019），AutoGluon 完全不做 CASH 也能勝出（EricksonEtAl2020）。這意味著最佳化器增益依情境而定——在大型、條件式空間與緊預算下最大——決定性槓桿是「加速抵達良好區域」而非漸近最佳化器選擇；而成熟系統已內建的歷史暖啟動（auto-sklearn 元學習、VolcanoML RGPE/RankNet、OBOE 協同過濾）正是這條加速路線的現行實作。

## 2. 核心概念有哪些？

- [[P_CASH]]：Combined Algorithm Selection and Hyperparameter optimization——管線最佳化的典範形式化，雙層／黑箱最佳化問題（KotthoffEtAl2019; ElshawiEtAl2019）。
- [[P_SMAC]]：隨機森林代理的 BO 骨幹，處理條件層級搜尋空間，驅動 Auto-WEKA／auto-sklearn 一系（LindauerEtAl2021）。
- [[P_Search_Space_Decomposition]]：把大型聯合空間拆解為可分頭征服的子問題——ADMM 算子分裂、串接 bandit、交替式、VolcanoML 可組合執行計畫；排名優勢隨空間增大而擴大（LiuEtAl2019; LiEtAl2021b; LiEtAl2022a）。
- [[P_Rising_Bandits]]：交替式 BO+MAB 分解的代表——26/30 資料集最佳、平均比 SMAC 少 12.6 倍試驗；候選演算法 1→16 時維持 95.02% 而 SMAC 降至 93.63%（LiEtAl2020a）。
- [[P_Hyperband]]：逐次減半的多保真排程——比主流 BO 快 5–30 倍，與模型式取樣混合為 BOHB/MFES-HB，產品化為 Optuna ASHA 剪枝（LiEtAl2016; FalknerEtAl2018; AkibaEtAl2019）。
- [[P_Optimizer_Indistinguishability]]：嚴謹協定下成熟最佳化器差異消失——114 個資料集上平均差異 <1.9%、隨機搜尋不劣（ZollerHuber2019; GijsbersEtAl2019 無一致最佳系統）。
- [[P_Anytime_Performance]]：以「任意時間點的最佳表現曲線」而非單一終點評測——多數比較缺乏此曲線，被點名為開放基準問題（GijsbersEtAl2019; EggenspergerEtAl2021）。
- [[P_Seeded_Baseline]]：給古典基線相同的初始配置種子再比較——RodriguesEtAl2026 揭露混淆的關鍵控制，任何暖啟動宣稱的必要對照。

## 3. 概念之間的關係

- **演化關係**：SMAC 式聯合 BO 骨幹（2013–2016）→ Hyperband／多保真 bandit（2016–2020）→ Search_Space_Decomposition 大規模化（Rising_Bandits、VolcanoML, 2019–2022）→ 集成中心與基準成熟期（2020–2026）。
- **爭議關係（聯合 vs. 分解）**：SMAC 陣營主張結構感知代理即可處理條件層級；分解陣營以維度退化證據反駁（SMAC 在 PC4 上 95.02%→93.63% 而 Rising_Bandits 持平）；哪種分解最佳（條件化、交替、ADMM、串接 bandit）仍無定論，自動執行計畫生成是開放問題。
- **爭議關係（CASH 是否必要）**：AutoGluon 無 CASH 靠堆疊達排名 1.84 對 auto-sklearn 3.81；DivBO/PSEO 反主張搜尋應具集成／多樣性感知。
- **裁判關係**：Optimizer_Indistinguishability、Anytime_Performance、Seeded_Baseline 三者構成評測層，裁定 CASH、Rising_Bandits、Hyperband 等系統層宣稱是否成立——基準效度本身亦有爭議（合成函數不適用於 CASH、代理式基準以真實性換統計檢定力、元學習污染測試集）。
- **互補關係**：Hyperband 的多保真排程與 Search_Space_Decomposition 正交，可疊加（Hyper-Tune、MFES-HB）；兩者各自帶來約一個數量級的試驗節省。

## 4. 常用方法

四波浪潮。(1) **聯合 BO 骨幹（2013–2016）**：SMAC 式隨機森林 BO 與 TPE 驅動 Auto-WEKA、Hyperopt-sklearn、auto-sklearn；演化式管線搜尋（TPOT）為主要替代；NAS 經 RL、可微鬆弛、正則化演化分支發展。(2) **Bandit 與多保真（2016–2020）**：Hyperband／逐次減半，混合為 BOHB、MFES-HB、Fabolas、Hyper-Tune，Optuna 以 ASHA 剪枝產品化（等時試驗數 35.8→1,278.6）。(3) **大規模 CASH 分解（2019–2022）**：ADMM 算子分裂、串接 ER-UCB bandit、交替式 Rising Bandits、VolcanoML 可組合 joint/conditioning/alternating 區塊佐以 RGPE/RankNet 元學習遷移。(4) **集成中心與基準成熟期（2020–2026）**：無 CASH 堆疊集成（AutoGluon）、多樣性感知 CASH（DivBO/PSEO）、整合基礎設施——OpenML AutoML Benchmark、HPOBench 容器（12 家族、100+ 問題、13 最佳化器、32 種子）、代理／元代理基準、ChaLearn 挑戰賽。

## 5. 常見錯誤

- **在未控制協定下宣稱最佳化器優勢**（step7 GAP_003 證據，逐字引用）：「**ZollerHuber2019**: Across 114 datasets, CASH optimizers were statistically indistinguishable and random search not worse — optimizer claims need careful protocols to be meaningful. / 114 個資料集上 CASH 最佳化器統計上無法區分、隨機搜尋不劣——最佳化器宣稱需要嚴謹協議才有意義。」
- **不報 anytime 曲線、放任元學習污染**（step7 GAP_003 證據，逐字引用）：「**GijsbersEtAl2019 + EggenspergerEtAl2021**: Name anytime-performance reporting and meta-learning train/test contamination as open benchmark problems — contamination is acute when warm-start priors are learned from subjects in the same dataset. / 點名 anytime performance 回報與 meta-learning 訓練／測試污染為開放基準問題——當暖啟動先驗學自同一資料集的受試者時，污染問題尤其尖銳。」
- **缺少種子化對照即歸功於新方法**（step7 GAP_003 證據，逐字引用）：「**RodriguesEtAl2026**: Budget-matched protocol shows LLM advisor gains reduce to the fixed first configuration (+0.40 pp CV, −0.01 pp test, p=0.92); seeded classical search overtakes within 12 evaluations — the exact confound an EEG study must control. / 預算對齊協議顯示 LLM 顧問增益化約為固定首配置；有 seed 的傳統搜尋 12 次評估內反超——EEG 研究必須控制的混淆正是這個。」
- **以合成函數或不當基準評測 CASH**（step6 Theme 3 Debates）：「合成函數被認為不適用於 CASH（ZollerHuber2019），代理／表格式基準以真實性換取統計檢定力（EggenspergerEtAl2015; KleinEtAl2019; EggenspergerEtAl2021）」；另有長時預算下的過擬合案例——「Auto-WEKA overfits at 4h」，且 dionis/helena 上「所有框架都不如調參隨機森林」（GijsbersEtAl2019）。

## 與其他 Plane 的關係

- [[F_T1_LLM_for_AutoML]]：T1 的混合系統（CoFEH 的 SMAC 條件化、LB-MCTS）直接建立在本平面的 CASH 機制之上；反過來，本平面的 Seeded_Baseline 與 Anytime_Performance 是審判 LLM 增益是否真實的方法學工具。
- [[F_T2_Warmstart_Transfer_BO]]：本平面的系統是 T2 遷移機制的宿主——auto-sklearn/VolcanoML/OBOE 已內建歷史暖啟動，且 Hyperband、Rising Bandits、VolcanoML 的結論各自獨立點名 meta-learning 暖啟動為下一個加速槓桿。
