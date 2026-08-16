---
citation_key: "RodriguesEtAl2026"
title: "When Is an LLM Worth It for Hyperparameter Optimization? A Budget-Matched Study on Tabular Data Finds the Warm-Start Is a Default Configuration, Not the Model"
authors: "Carson Rodrigues; Oysturn Vas; Isaiah Abner DCosta; Nithish Kumar Prabhakaran"
year: 2026
doi: "10.48550/arxiv.2606.21641"
source: "arXiv (2606.21641)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "2606.21641"
conversion: "pdftotext -layout (automated)"
language: "bilingual-en-zh"
---

# When Is an LLM Worth It for Hyperparameter Optimization? A Budget-Matched Study on Tabular Data Finds the Warm-Start Is a Default Configuration, Not the Model | LLM 用於超參數最佳化何時划算？表格資料上的預算匹配研究發現：暖啟動來自預設配置，而非模型

> [!abstract] 重點摘要
> - 在 8 個 PMLB 表格分類基準、5 個隨機種子的預算匹配（budget-matched）協定下，比較 LLM 顧問（LLM-OptFlow）與四種經典基線：隨機搜尋、Optuna-TPE、高斯過程貝葉斯最佳化（GP-BO）與逐次減半（successive halving），並以配對檢定與 bootstrap 95% 信賴區間量化差異。
> - 核心發現具警示性：LLM 顧問亮眼的第一個評估點根本不是 LLM 的輸出，而是迴圈在呼叫任何模型前即先評估的固定預設配置（達 88.7% 平均最佳 CV），且在受測的七個模型之間完全一致（差異 < 0.01 個百分點）。
> - LLM 本身的提案在 12 次評估預算內僅比種子多貢獻 +0.40 個百分點的 CV 準確率，而在保留測試集（held-out test）上毫無增益（−0.01 pp，p = 0.92）。
> - 關鍵對照實驗：將同一預設種子給予經典搜尋後，顧問的表面領先隨即瓦解——對播種的隨機搜尋僅在 2 次評估時領先 +0.20 pp，5 次評估後領先消失，12 次評估時反落後 −0.37 pp；即使不播種，經典搜尋也在 12 次評估追平、40 次評估超越（+0.6 至 +0.8 pp，p ≤ 10⁻⁴）。
> - 僅存的兩個 LLM 特有現象：單一任務（vehicle）上的探索失敗（顧問停留於預設值附近，經典方法多獲 6–9 pp），以及一個具操作價值但不改變準確率的規則式信心過濾器（可去除超出搜尋空間的提案並節省約 33% 浪費算力）。
> - 實務建議屬「消風」性質：在表格資料超參數最佳化上，應以合理的預設配置為經典搜尋播種（暖啟動，warm-starting），而非付費將 LLM 置入迴圈；作者釋出可重現所有統計數字的實驗框架與腳本。

---

## Abstract | 摘要

> [!quote] Original
> Large language models (LLMs) have been proposed as hyperparameter-optimization (HPO) advisors that "warm-start" search from prior knowledge, proposing strong configurations in very few evaluations. We test that warm-start claim under a budget-matched, multi-seed protocol on eight PMLB tabular benchmarks, comparing an LLM advisor (LLM-OptFlow) against four classical baselines (random search, a full Optuna-TPE study, Gaussian-process Bayesian optimization [GP-BO], and successive halving) over one shared search space, with paired tests and bootstrap 95% confidence intervals across the 8 × 5 = 40 (task, seed) units. Our central finding is cautionary. The advisor's strong first point is not an LLM output at all: like prior LLM-HPO systems the loop is seeded with a fixed default configuration, evaluated before any model call, which alone reaches 88.7% mean best-CV, identical to within 0.01 pp across all seven advisor models we test. The LLM's own proposals add only +0.40 percentage points (pp) of cross-validation accuracy over that seed across the 12-evaluation budget and nothing on held-out test (LLM−Default = −0.01 pp, p = 0.92). When the same default seed is granted to classical search, the advisor's apparent lead collapses: against seeded random search (an exact control) it leads by only +0.20 pp at a 2-evaluation budget, the lead is gone by 5 evaluations, and it is behind by 12 (−0.37 pp); seeding the model-based optimizers too only widens this. Even without that seed, classical search ties the advisor by 12 evaluations and passes it by 40 (+0.6 to +0.8 pp, p ≤ 10−4). Two LLM-specific behaviors do survive: a single-task exploration failure (vehicle, where the advisor stays at the default while classical search gains 6–9 pp; random +9.06 pp, p < 0.01) and a rule-based confidence filter that is operationally useful (it removes out-of-space proposals and ∼33% of wasted compute) but does not change accuracy. The recommendation is deflationary: on tabular HPO, seed classical search with a sensible default; an LLM advisor adds no measurable generalization benefit and is overtaken within a handful of evaluations. We release the harness and a script that reproduces every statistic.

> [!note] 翻譯
> 大型語言模型（LLMs）被提議作為超參數最佳化（hyperparameter optimization, HPO）的顧問，可憑先驗知識為搜尋「暖啟動」（warm-start），在極少的評估次數內提出強力配置。我們在 8 個 PMLB 表格基準上，以預算匹配（budget-matched）、多隨機種子的協定檢驗此一暖啟動主張：在單一共享搜尋空間上，比較 LLM 顧問（LLM-OptFlow）與四種經典基線（隨機搜尋、完整的 Optuna-TPE 研究、高斯過程貝葉斯最佳化（Gaussian-process Bayesian optimization, GP-BO）與逐次減半（successive halving）），並在 8 × 5 = 40 個（任務、種子）單元上進行配對檢定與 bootstrap 95% 信賴區間估計。我們的核心發現具警示意味。顧問亮眼的第一個點根本不是 LLM 的輸出：如同先前的 LLM-HPO 系統，其迴圈以一個固定的預設配置作為種子（seed），在任何模型呼叫之前即先行評估——單憑此配置便達到 88.7% 的平均最佳 CV，且在我們受測的全部七個顧問模型之間相同至 0.01 個百分點（pp）以內。在 12 次評估的預算內，LLM 本身的提案僅在該種子之上增添 +0.40 個百分點的交叉驗證準確率，而在保留測試集（held-out test）上毫無增益（LLM−Default = −0.01 pp，p = 0.92）。當同一預設種子被授予經典搜尋時，顧問的表面領先隨即崩解：對上播種的隨機搜尋（一個精確對照，exact control），它在 2 次評估的預算下僅領先 +0.20 pp，領先在 5 次評估時消失，12 次評估時反而落後（−0.37 pp）；連基於模型的最佳化器也播種時，差距只會更大。即使沒有該種子，經典搜尋也在 12 次評估時與顧問打平，並在 40 次評估時超越之（+0.6 至 +0.8 pp，p ≤ 10⁻⁴）。有兩種 LLM 特有的行為確實存留：一是單一任務上的探索失敗（vehicle 資料集：顧問停留在預設值，而經典搜尋多獲得 6–9 pp；隨機搜尋 +9.06 pp，p < 0.01）；二是一個規則式的信心過濾器（confidence filter），其具操作上的效用（能移除超出搜尋空間的提案並省下約 33% 的浪費算力），但不改變準確率。我們的建議屬「消風」（deflationary）性質：在表格資料的 HPO 上，請以合理的預設值為經典搜尋播種；LLM 顧問不帶來任何可量測的泛化效益，且在寥寥數次評估內即被超越。我們釋出實驗框架（harness）與可重現每一項統計數字的腳本。

---

## 1 Introduction | 引言

> [!quote] Original
> Hyperparameter optimization (HPO) is a core, expensive step in applied machine learning. Classical methods are well understood but treat the search as black-box: random search (Bergstra & Bengio, 2012), Bayesian optimization (Snoek et al., 2012), Tree-structured Parzen Estimators (TPE) as in Optuna (Akiba et al., 2019), and multi-fidelity methods such as Hyperband (Li et al., 2018). A recent line of work instead prompts an LLM to propose configurations from a natural-language description of the task and the history of attempts (Yang et al., 2024; Liu et al., 2024a; Mahammadli & Ertekin, 2024; Liu et al., 2024b; Zhang et al., 2023). The appeal is prior knowledge: an LLM "knows" that gradient boosting often beats logistic regression on tabular data, and is claimed to propose a strong starting configuration in very few evaluations, in effect a learned warm-start. In practice these advisor loops are also seeded with a fixed default configuration to begin the search, a detail that turns out to be decisive.
>
> The question for a practitioner is not only whether LLM advising ever helps, but what provides the help. Prior empirical claims of LLM superiority were undermined by under-powered baselines (e.g., three-trial proxies for tuned search), single runs, and no statistics; they also credited the model with a warm-start without separating its proposals from the default configuration the loop is initialized with. We pull these apart under matched budgets with proper variance estimates, and find that on tabular HPO the warm-start is the default seed, not the model: the LLM's own proposals add at most 0.4 percentage points on the cross-validation objective and nothing on held-out accuracy, and once classical search is given the same default it matches the advisor within a few evaluations and surpasses it thereafter.

> [!note] 翻譯
> 超參數最佳化（HPO）是應用機器學習中核心且昂貴的一環。經典方法已被充分理解，但將搜尋視為黑箱：隨機搜尋（Bergstra & Bengio, 2012）、貝葉斯最佳化（Bayesian optimization）（Snoek et al., 2012）、Optuna 中的樹狀結構 Parzen 估計器（Tree-structured Parzen Estimators, TPE）（Akiba et al., 2019），以及 Hyperband 等多保真度（multi-fidelity）方法（Li et al., 2018）。近期的一系列研究改以提示 LLM，令其根據任務的自然語言描述與嘗試歷史提出配置（Yang et al., 2024；Liu et al., 2024a；Mahammadli & Ertekin, 2024；Liu et al., 2024b；Zhang et al., 2023）。其吸引力在於先驗知識：LLM「知道」梯度提升（gradient boosting）在表格資料上常勝過邏輯迴歸，據稱能在極少的評估次數內提出強力的起始配置，實質上是一種習得的暖啟動（learned warm-start）。而在實務中，這些顧問迴圈也會以一個固定的預設配置作為種子來展開搜尋——這個細節最終被證明具有決定性。
>
> 對實務工作者而言，問題不僅是 LLM 顧問是否有幫助，更在於幫助究竟由何而來。先前關於 LLM 優越性的實證主張，因基線檢定力不足（例如以三次試驗代理調校過的搜尋）、僅單次執行、缺乏統計檢定而站不住腳；它們也將暖啟動的功勞歸於模型，卻未將模型的提案與迴圈初始化所用的預設配置區分開來。我們在匹配的預算與適當的變異數估計下將二者拆解，發現在表格資料的 HPO 上，暖啟動來自預設種子而非模型：LLM 本身的提案在交叉驗證目標上至多增添 0.4 個百分點，在保留測試準確率上則毫無增益；而一旦經典搜尋獲得同一預設值，它在數次評估內即與顧問持平，其後更加以超越。

---

> [!quote] Original
> **Contributions.**
>
> - A budget-matched, multi-seed comparison of an LLM advisor against four classical baselines over a single shared search space on eight standard PMLB benchmarks (Olson et al., 2017): random search, a full Optuna-TPE study, Gaussian-process Bayesian optimization (Snoek et al., 2012), and successive halving (Li et al., 2018), with paired tests and bootstrap 95% CIs (Efron & Tibshirani, 1986). The decisive addition is a control that seeds the classical baselines with the same default the advisor is initialized with, which isolates the LLM's marginal contribution.
> - The central, cautionary finding: the warm-start is a default configuration, not the model. The advisor's first point is a fixed default seed (model-independent, 88.7% mean best-CV); the LLM's own proposals add only +0.40 pp of CV accuracy over that seed and −0.01 pp on held-out test (p = 0.92); and once the same default is given to classical search, the advisor's edge is +0.20 pp at 2 evaluations (n.s. vs. TPE), gone by 5, and negative by 12.
> - A negative practical recommendation: on tabular HPO, default-seeded classical search matches the LLM advisor within a few evaluations and surpasses it thereafter; the advisor also ties un-seeded classical search by 12 evaluations and is passed by 40, at a far lower cost per proposal and no API dependency.
> - Two surviving LLM-specific phenomena, each characterized and honestly bounded: a single-task exploration failure (vehicle) and a rule-based confidence filter whose value is operational, not accuracy-improving.
> - A seven-model panel (five providers, nano-to-frontier) confirming the default-driven first point is model-independent, and a released, reproducible harness with a script that regenerates every statistic.

> [!note] 翻譯
> **貢獻。**
>
> - 在 8 個標準 PMLB 基準（Olson et al., 2017）的單一共享搜尋空間上，對 LLM 顧問與四種經典基線進行預算匹配、多種子的比較：隨機搜尋、完整的 Optuna-TPE 研究、高斯過程貝葉斯最佳化（Snoek et al., 2012）與逐次減半（Li et al., 2018），並輔以配對檢定與 bootstrap 95% 信賴區間（Efron & Tibshirani, 1986）。決定性的新增設計是一項對照：以顧問初始化所用的同一預設值為經典基線播種，從而分離出 LLM 的邊際貢獻。
> - 核心的警示性發現：暖啟動來自預設配置，而非模型。顧問的第一個點是固定的預設種子（與模型無關，88.7% 平均最佳 CV）；LLM 本身的提案僅在該種子之上增添 +0.40 pp 的 CV 準確率，在保留測試集上為 −0.01 pp（p = 0.92）；而當同一預設值被給予經典搜尋後，顧問的優勢在 2 次評估時為 +0.20 pp（相對 TPE 不顯著），5 次評估時消失，12 次評估時轉為負值。
> - 一項否定性的實務建議：在表格資料 HPO 上，以預設值播種的經典搜尋在數次評估內即追平 LLM 顧問，其後加以超越；顧問對未播種的經典搜尋亦在 12 次評估時被追平、40 次時被超越——且經典搜尋每次提案的成本遠低於 LLM，亦無 API 依賴。
> - 兩個存留的 LLM 特有現象，均獲刻畫並誠實界定其範圍：單一任務（vehicle）上的探索失敗，以及一個規則式信心過濾器——其價值在於操作面，而非提升準確率。
> - 一個七模型面板（五家供應商，涵蓋 nano 到前沿等級），確認由預設值驅動的第一個點與模型無關；並釋出可重現的實驗框架與能重新生成每一項統計數字的腳本。

---

## 2 Related Work | 相關研究

> [!quote] Original
> **Classical HPO.** Random search is a strong, embarrassingly-parallel baseline (Bergstra & Bengio, 2012); model-based methods (GP-BO (Snoek et al., 2012), SMAC (Hutter et al., 2011), TPE (Akiba et al., 2019)) improve sample efficiency, and multi-fidelity methods (Li et al., 2018) improve anytime performance. Toolkits such as Optuna (Akiba et al., 2019) and KerasTuner (O'Malley et al., 2019) make these routine.
>
> **Warm-starting and meta-learning for HPO.** Initializing search from configurations that performed well on related tasks is a long-studied route to strong early evaluations: meta-learning the initial design for Bayesian optimization (Feurer et al., 2015b) and the warm-start in auto-sklearn (Feurer et al., 2015a) are direct precedents. An LLM advisor's prior knowledge is, in this framing, a learned warm-start, so a fair evaluation must separate it from the trivial warm-start of a hand-chosen default configuration. Supplying that control is central to our study.

> [!note] 翻譯
> **經典 HPO。**隨機搜尋是一個強力且「極易平行化」（embarrassingly-parallel）的基線（Bergstra & Bengio, 2012）；基於模型的方法（GP-BO（Snoek et al., 2012）、SMAC（Hutter et al., 2011）、TPE（Akiba et al., 2019））改善樣本效率，多保真度方法（Li et al., 2018）則改善任意時間（anytime）效能。Optuna（Akiba et al., 2019）與 KerasTuner（O'Malley et al., 2019）等工具套件使這些方法成為例行手段。
>
> **HPO 的暖啟動與元學習。**以在相關任務上表現良好的配置來初始化搜尋，是通往強力早期評估的一條長期受研究的路徑：為貝葉斯最佳化以元學習（meta-learning）決定初始設計（Feurer et al., 2015b），以及 auto-sklearn 中的暖啟動（Feurer et al., 2015a），皆是直接的先例。在此框架下，LLM 顧問的先驗知識即是一種習得的暖啟動，因此公平的評估必須將其與「人工挑選之預設配置」這種平凡暖啟動區分開來。提供此一對照正是本研究的核心。

---

> [!quote] Original
> **LLMs for optimization and HPO.** OPRO frames the LLM itself as the optimizer over a prompt of prior (solution, score) pairs (Yang et al., 2024). A complementary thread optimizes the textual components of a system rather than its numeric configuration: GEPA evolves prompts, code, and control flow by having an LLM reflect in natural language on full execution traces and mutate candidates on a Pareto front, reportedly beating reinforcement learning (GRPO) while using up to 35× fewer rollouts (Agrawal et al., 2026). Our object of optimization differs (numeric model and hyperparameter configurations under matched evaluation budgets), but the same question drives both lines of work: when does reflective LLM proposal beat brute-force search? AgentHPO (Liu et al., 2024a) and SLLMBO (Mahammadli & Ertekin, 2024) build iterative LLM HPO loops, the latter hybridizing with TPE; LLAMBO (Liu et al., 2024b) uses LLMs inside Bayesian optimization, including for warm-starting. Recent evidence is converging on the limits of LLM proposers: a reproducibility study of LLM Bayesian optimization finds the LLM surrogate weaker than Gaussian-process and SMAC search (Rychert et al., 2025), a high-dimensional study reports LLMs competitive only below roughly a dozen features and overtaken by Bayesian search beyond that (Srinivasan & Menzies, 2026), and a re-examination of OPRO documents small-scale LLMs failing as optimizers (Zhang et al., 2024); a parallel proposal drops the classical baseline altogether (Naphade et al., 2025), the very comparison our default-seed control restores. Agentic ML-engineering benchmarks (MLAgentBench (Huang et al., 2024), MLE-bench (Chan et al., 2025)) evaluate broader pipelines. Our contribution is not a new method but a rigorous characterization of what, if anything, the LLM-advisor pattern buys, which prior work asserts more than measures. In contrast to claims of LLM sample-efficiency, we find that on tabular HPO the early advantage is attributable to the default seed rather than the model's proposals, a control these papers do not isolate.

> [!note] 翻譯
> **用於最佳化與 HPO 的 LLM。**OPRO 將 LLM 本身框定為最佳化器，在由先前（解、分數）對組成的提示上運作（Yang et al., 2024）。一條互補的研究脈絡最佳化的是系統的文字組件而非其數值配置：GEPA 讓 LLM 以自然語言反思完整執行軌跡，並在 Pareto 前緣上突變候選者，藉此演化提示、程式碼與控制流程，據報告以最多少 35 倍的 rollouts 勝過強化學習（GRPO）（Agrawal et al., 2026）。我們的最佳化對象不同（在匹配評估預算下的數值模型與超參數配置），但驅動兩條研究線的問題相同：反思式的 LLM 提案何時勝過暴力搜尋？AgentHPO（Liu et al., 2024a）與 SLLMBO（Mahammadli & Ertekin, 2024）建構迭代式 LLM HPO 迴圈，後者與 TPE 混合；LLAMBO（Liu et al., 2024b）將 LLM 用於貝葉斯最佳化內部，包括用於暖啟動。近期證據正逐漸收斂於 LLM 提案者的侷限：一項 LLM 貝葉斯最佳化的可重現性研究發現 LLM 代理模型（surrogate）弱於高斯過程與 SMAC 搜尋（Rychert et al., 2025）；一項高維度研究報告 LLM 僅在特徵數大約低於十來個時具競爭力，超過即被貝葉斯搜尋超越（Srinivasan & Menzies, 2026）；對 OPRO 的重新檢視則記錄了小規模 LLM 作為最佳化器的失敗（Zhang et al., 2024）；另一項平行提案乾脆捨棄了經典基線（Naphade et al., 2025）——而這正是我們的預設種子對照所恢復的比較。代理式機器學習工程基準（MLAgentBench（Huang et al., 2024）、MLE-bench（Chan et al., 2025））評估的則是更廣泛的管線。我們的貢獻不是新方法，而是對「LLM 顧問模式究竟換得了什麼（如果有的話）」的嚴謹刻畫——先前研究對此多為斷言而少有量測。與 LLM 樣本效率的主張相反，我們發現在表格資料 HPO 上，早期優勢應歸因於預設種子而非模型的提案——這正是那些論文未曾分離的對照。

---

## 3 Method: LLM-OptFlow and the Confidence Filter | 方法：LLM-OptFlow 與信心過濾器

> [!quote] Original
> **Search space S.** All methods explore one space over four model families (logistic regression, random forest, gradient boosting, and SVC) with standard hyperparameter ranges (e.g., RF n_estimators ∈ [50, 500], GB learning_rate ∈ [10−3, 0.3]). Random search samples S uniformly; TPE explores the identical space; the LLM is prompted with the ranges and asked to stay within them. All methods thus explore the same space and objective; §5 also controls for the default configuration the advisor is seeded with, by granting the same seed to classical search.
>
> **LLM-OptFlow advisor.** The loop is seeded with a fixed default configuration (RandomForest with n_estimators = 100, max_depth = 16, min_samples_leaf = 1; the same configuration as the Default baseline), scored once before any LLM call; this seed is the first point on the advisor's budget curve. Thereafter, at each iteration the LLM (Claude Haiku) receives the dataset description, the incumbent configuration and its 3-fold CV score, and the history of attempts, and proposes one configuration with a short rationale. The objective is 3-fold CV accuracy on the training split; the reported number is held-out test accuracy of the selected configuration. We count the default seed as the advisor's first evaluation, so its k-th budget point reflects the seed plus k − 1 LLM proposals.
>
> **Confidence filter.** Each proposal is validated against S before evaluation: unknown models, out-of-range numeric values, bad enums, or wrong types are rejected and the incumbent is kept (no evaluation spent). A naive validator that checks only trivial bounds never fires in practice; ours enforces the full space S, so it triggers exactly when a proposal drifts outside S.

> [!note] 翻譯
> **搜尋空間 S。**所有方法皆探索同一空間，涵蓋四個模型家族（邏輯迴歸、隨機森林、梯度提升與 SVC）及標準超參數範圍（例如 RF 的 n_estimators ∈ [50, 500]、GB 的 learning_rate ∈ [10⁻³, 0.3]）。隨機搜尋對 S 均勻取樣；TPE 探索完全相同的空間；LLM 則在提示中被告知各範圍並被要求不得逾越。因此所有方法探索的是同一空間與同一目標；第 5 節並藉由將同一種子授予經典搜尋，對顧問所播種的預設配置加以控制。
>
> **LLM-OptFlow 顧問。**迴圈以一個固定的預設配置作為種子（RandomForest，n_estimators = 100、max_depth = 16、min_samples_leaf = 1；與 Default 基線為同一配置），在任何 LLM 呼叫之前先評分一次；此種子即為顧問預算曲線上的第一個點。此後，每次迭代時 LLM（Claude Haiku）接收資料集描述、現任（incumbent）配置及其 3 折 CV 分數，以及嘗試歷史，並附帶簡短理由提出一個配置。目標函數為訓練切分上的 3 折 CV 準確率；報告的數字則是所選配置在保留測試集上的準確率。我們將預設種子計為顧問的第一次評估，故其第 k 個預算點反映的是種子加上 k − 1 個 LLM 提案。
>
> **信心過濾器。**每個提案在評估前先對照 S 進行驗證：未知模型、超出範圍的數值、無效的列舉值或型別錯誤者一律拒絕，並保留現任配置（不耗費評估次數）。只檢查平凡邊界的天真驗證器在實務中從不觸發；我們的驗證器強制執行完整的空間 S，因此恰在提案漂移至 S 之外時觸發。

---

## 4 Experimental Setup | 實驗設置

> [!quote] Original
> **Benchmarks.** Eight PMLB (Olson et al., 2017) classification datasets spanning sizes and class counts: credit-g, spambase, phoneme, churn, satimage (6-class), vehicle (4-class), ionosphere, hypothyroid. (OpenML's live API was unavailable during our study; PMLB provides the same canonical datasets from a reliable mirror.)
>
> **Protocol.** For each dataset and each of 5 seeds we take a stratified 70/30 train/test split. Random search, TPE, and GP-BO run 40 trials; LLM-OptFlow runs 12 iterations. We log the running-best CV score after each evaluation (the budget curve) and report test accuracy of the final selected configuration. Every number is reported as a mean±std over the five seeds, and the budget curves show standard-error bands. Optuna's TPE and GP-BO samplers use the default 10 random startup trials before their model-based phase, so their surrogate machinery does not engage until trial 11 and the single-digit-budget regime reflects each sampler's startup behavior rather than its surrogate.
>
> **Statistical analysis.** All cross-method comparisons are paired across the 8 × 5 = 40 (task, seed) units. We report mean differences in percentage points (pp) with bootstrap 95% confidence intervals (20,000 resamples) (Efron & Tibshirani, 1986) and paired two-sided tests (Student's t, with the Wilcoxon signed-rank test as a distribution-free check). A released script (significance.py) regenerates every statistic reported below.
>
> **Strong classical baselines.** Beyond random search and TPE we add two stronger classical optimizers, all exploring the identical space S and objective. GP-BO is Gaussian-process Bayesian optimization (Snoek et al., 2012) via Optuna's GPSampler; because GP-BO assumes a fixed-dimensional space, we give it the standard flat encoding of S (all hyperparameters present every trial, those irrelevant to the sampled family masked at evaluation), so it is full-fidelity and directly comparable on the per-evaluation budget curve. Successive halving (SH) is a Hyperband-family multi-fidelity method (Li et al., 2018) (scikit-learn's HalvingRandomSearchCV) using training-set size as the fidelity; being multi-fidelity it spends partial-fidelity evaluations, so we do not place it on the per-evaluation budget curve (that accounting would be unfair) and instead report it in the final-budget table with its total resource noted. SMAC's random-forest surrogate would be a natural further baseline; its native build was unavailable on our platform, and GP-BO and TPE together already span the GP- and tree-based model-based-BO families.

> [!note] 翻譯
> **基準資料集。**8 個 PMLB（Olson et al., 2017）分類資料集，橫跨不同規模與類別數：credit-g、spambase、phoneme、churn、satimage（6 類）、vehicle（4 類）、ionosphere、hypothyroid。（本研究進行期間 OpenML 的線上 API 無法使用；PMLB 從可靠的鏡像站提供同樣的標準資料集。）
>
> **實驗協定。**對每個資料集與 5 個隨機種子中的每一個，我們採分層（stratified）70/30 訓練／測試切分。隨機搜尋、TPE 與 GP-BO 執行 40 次試驗；LLM-OptFlow 執行 12 次迭代。我們在每次評估後記錄迄今最佳 CV 分數（即預算曲線），並報告最終所選配置的測試準確率。所有數字均以五個種子的平均值±標準差呈現，預算曲線則附標準誤帶。Optuna 的 TPE 與 GP-BO 取樣器在其基於模型的階段之前，預設先進行 10 次隨機起始試驗，因此其代理模型機制要到第 11 次試驗才啟動；個位數預算範圍反映的是各取樣器的起始行為，而非其代理模型。
>
> **統計分析。**所有跨方法比較均在 8 × 5 = 40 個（任務、種子）單元上配對進行。我們以百分點（pp）報告平均差異，附 bootstrap 95% 信賴區間（20,000 次重抽樣）（Efron & Tibshirani, 1986）與配對雙尾檢定（Student's t 檢定，並以 Wilcoxon 符號等級檢定作為無分布假設的核驗）。釋出的腳本（significance.py）可重新生成下文報告的每一項統計數字。
>
> **強力經典基線。**除隨機搜尋與 TPE 之外，我們再加入兩個更強的經典最佳化器，皆探索同一空間 S 與同一目標。GP-BO 為高斯過程貝葉斯最佳化（Snoek et al., 2012），透過 Optuna 的 GPSampler 實作；由於 GP-BO 假設固定維度的空間，我們給予其 S 的標準平坦編碼（每次試驗所有超參數皆存在，與被取樣家族無關者在評估時遮罩），故其為全保真度、可直接在逐次評估的預算曲線上比較。逐次減半（successive halving, SH）為 Hyperband 家族的多保真度方法（Li et al., 2018）（scikit-learn 的 HalvingRandomSearchCV），以訓練集大小作為保真度；因其為多保真度方法、會花費部分保真度的評估，我們不將其置於逐次評估的預算曲線上（那樣的計帳方式並不公平），而是在最終預算表中報告並註明其總資源。SMAC 的隨機森林代理模型本可作為自然的進一步基線；其原生建置在我們的平台上無法使用，且 GP-BO 與 TPE 二者已涵蓋基於 GP 與基於樹的模型式貝葉斯最佳化家族。

---

## 5 Results | 結果

### 5.1 The warm-start is a default configuration, not the model | 暖啟動來自預設配置，而非模型

> [!quote] Original
> Table 1 and Figure 1 report mean best-CV accuracy at matched budgets. The advisor's first point is striking: 88.7% at one evaluation, far above random search (83.7%), TPE (86.7%), and GP-BO (86.3%). But it is not an LLM proposal. It is the fixed default configuration the loop is seeded with, scored before any model is queried (§3); across all seven advisors we test it is identical to within 0.01 pp (Table 3; the residual reflects cross-validation nondeterminism, not the model), as it essentially must be, since the model has not yet been called. The Default (fixed) row in Table 1 is this same configuration, flat across budget. The classical baselines receive no such seed, so the apparent "+2 to +5 pp warm-start" at one evaluation is a hand-chosen default beating a single cold draw, not the LLM's prior knowledge.
>
> **What the LLM actually adds.** Measured over its own seed, the LLM's first proposal improves best-CV by only +0.22 pp (95% CI [0.09, 0.42], p = 0.02) and its proposals over the full 12-evaluation budget by +0.40 pp [0.22, 0.62] (p < 0.001): a real but small effect on the CV objective. On held-out test accuracy it adds nothing: LLM-OptFlow is statistically indistinguishable from simply using the default (−0.01 pp, 95% CI [−0.22, 0.19], p = 0.92).

> [!note] 翻譯
> 表 1 與圖 1 報告匹配預算下的平均最佳 CV 準確率。顧問的第一個點十分醒目：單次評估即達 88.7%，遠高於隨機搜尋（83.7%）、TPE（86.7%）與 GP-BO（86.3%）。但那並不是 LLM 的提案。它是迴圈播種所用的固定預設配置，在查詢任何模型之前即先評分（第 3 節）；在我們受測的全部七個顧問模型之間，該值相同至 0.01 pp 以內（表 3；殘餘差異反映交叉驗證的非決定性，而非模型），這在本質上也必然如此——因為模型此時尚未被呼叫。表 1 中的 Default (fixed) 列正是這同一配置，在各預算下持平。經典基線並未獲得這樣的種子，因此單次評估時表面上「+2 到 +5 pp 的暖啟動」，其實是人工挑選的預設值勝過單次冷抽樣（cold draw），而非 LLM 的先驗知識。
>
> **LLM 實際增添了什麼。**以其自身種子為基準量測，LLM 的第一個提案僅使最佳 CV 提升 +0.22 pp（95% CI [0.09, 0.42]，p = 0.02），其提案在完整 12 次評估預算內共提升 +0.40 pp [0.22, 0.62]（p < 0.001）：對 CV 目標而言是真實但微小的效果。在保留測試準確率上，它毫無增益：LLM-OptFlow 與單純使用預設值在統計上無法區分（−0.01 pp，95% CI [−0.22, 0.19]，p = 0.92）。

---

> [!quote] Original
> **The decisive control: give classical search the same seed.** Seeding random search with the same default (an exact control, since random draws are independent of the seed), the advisor leads by only +0.20 pp at a 2-evaluation budget (p = 0.03), the lead is gone by 5 evaluations (−0.09 pp, n.s.), and the advisor is behind by 12 (−0.37 pp, 95% CI [−0.82, −0.02]). An approximate control that also seeds the model-based optimizers (treating the default as a performance floor; see Limitations) tells the same story: a non-significant +0.14 pp against TPE at 2 evaluations, and −0.60/−0.48 pp behind TPE/GP-BO by 12. Once the free default is available to both, the LLM's proposals buy no sample efficiency.
>
> **Even un-seeded, classical search catches up.** By 12 evaluations the advisor is statistically tied with both Bayesian optimizers without any seed of their own (TPE−LLM = +0.45 pp, 95% CI [−0.03, +1.01], paired-t p = 0.10, Wilcoxon p = 0.81; GP-BO−LLM = +0.28 pp [−0.22, +0.85], p = 0.31). It then falls behind because classical search keeps improving (TPE +0.62 pp, random +0.76 pp, GP-BO +0.61 pp from 12 to 40 evals, all p ≤ 10−4) while the advisor has converged: its 5-to-12 gain is 0.03 pp and extending it to the full 40-evaluation budget on four datasets adds at most 0.24 pp (credit-g +0.24, spambase +0.21, satimage +0.00, vehicle +0.00). The advisor's ceiling is set early and low, and that both a tree-based (TPE) and a GP-based (GP-BO) optimizer overtake it indicates the effect is a property of classical search in general, not of one sampler.

> [!note] 翻譯
> **決定性的對照：給經典搜尋同一種子。**以同一預設值為隨機搜尋播種（此為精確對照，因隨機抽樣與種子無關）後，顧問在 2 次評估的預算下僅領先 +0.20 pp（p = 0.03），領先在 5 次評估時消失（−0.09 pp，不顯著），12 次評估時顧問反而落後（−0.37 pp，95% CI [−0.82, −0.02]）。連基於模型的最佳化器也一併播種的近似對照（將預設值視為效能下限；見「限制」一節）呈現同樣的故事：2 次評估時對 TPE 為不顯著的 +0.14 pp，12 次評估時落後 TPE／GP-BO 達 −0.60／−0.48 pp。一旦這個免費的預設值對雙方皆可用，LLM 的提案便買不到任何樣本效率。
>
> **即使不播種，經典搜尋也會追上。**到 12 次評估時，顧問已與兩個未獲任何種子的貝葉斯最佳化器在統計上打平（TPE−LLM = +0.45 pp，95% CI [−0.03, +1.01]，配對 t 檢定 p = 0.10，Wilcoxon p = 0.81；GP-BO−LLM = +0.28 pp [−0.22, +0.85]，p = 0.31）。其後顧問逐漸落後，因為經典搜尋持續進步（自 12 至 40 次評估：TPE +0.62 pp、隨機搜尋 +0.76 pp、GP-BO +0.61 pp，均 p ≤ 10⁻⁴），而顧問已然收斂：其第 5 至第 12 次評估的增益僅 0.03 pp；在四個資料集上將其延伸至完整的 40 次評估預算，至多再增 0.24 pp（credit-g +0.24、spambase +0.21、satimage +0.00、vehicle +0.00）。顧問的天花板既早又低；而基於樹（TPE）與基於 GP（GP-BO）的最佳化器雙雙超越它，顯示此效應是經典搜尋整體的性質，而非單一取樣器所致。

---

> [!quote] Original
> Table 1: Mean best-CV accuracy (%) at matched evaluation budgets (8 tasks × 5 seeds). Default (fixed) is a single hand-chosen configuration, constant across budget. LLM-OptFlow is seeded with it, so its 1-evaluation entry (†) is the default (88.7), not an LLM proposal; the LLM's proposals add ≤ 0.4 pp over this seed (§5.1). Once the same seed is given to classical search the advisor's early lead disappears; even un-seeded, classical search ties the advisor by 12 evaluations and passes it by 40. Bold marks the best tuned, non-seeded optimizer at the 12- and 40-evaluation budgets; the advisor's seed-driven early lead is not bolded.
>
> | Budget (evals) | 1 | 3 | 5 | 12 | 40 |
> |---|---|---|---|---|---|
> | Default (fixed) | 88.7 | 88.7 | 88.7 | 88.7 | 88.7 |
> | Random search | 83.7 | 87.1 | 88.2 | 89.2 | 90.0 |
> | Optuna-TPE | 86.7 | 87.7 | 88.7 | **89.5** | **90.1** |
> | GP-BO | 86.3 | 88.0 | 88.3 | 89.3 | 89.9 |
> | LLM-OptFlow | 88.7† | 89.0 | 89.0 | 89.1 | — |
>
> [Figure 1: Running-best CV accuracy vs. evaluation budget (mean ± s.e. over tasks×seeds). LLM-OptFlow is seeded with a fixed default (its budget-1 point), so its early lead reflects that seed; once classical search is given the same seed the advantage disappears, and even un-seeded, random, TPE, and GP-BO (40 evals) tie the advisor by 12 evaluations and pass it by 40.]

> [!note] 翻譯
> 表 1：匹配評估預算下的平均最佳 CV 準確率（%）（8 個任務 × 5 個種子）。Default (fixed) 為單一人工挑選的配置，在各預算下恆定。LLM-OptFlow 以其播種，故其 1 次評估的數值（†）即是該預設值（88.7），並非 LLM 提案；LLM 的提案在此種子之上增添 ≤ 0.4 pp（§5.1）。當同一種子被給予經典搜尋後，顧問的早期領先隨即消失；即使不播種，經典搜尋亦於 12 次評估追平顧問、40 次時超越。粗體標示 12 與 40 次評估預算下最佳的「經調校、未播種」最佳化器；顧問由種子驅動的早期領先不予粗體。
>
> | 預算（評估次數） | 1 | 3 | 5 | 12 | 40 |
> |---|---|---|---|---|---|
> | Default（固定） | 88.7 | 88.7 | 88.7 | 88.7 | 88.7 |
> | 隨機搜尋 | 83.7 | 87.1 | 88.2 | 89.2 | 90.0 |
> | Optuna-TPE | 86.7 | 87.7 | 88.7 | **89.5** | **90.1** |
> | GP-BO | 86.3 | 88.0 | 88.3 | 89.3 | 89.9 |
> | LLM-OptFlow | 88.7† | 89.0 | 89.0 | 89.1 | — |
>
> [圖 1：迄今最佳 CV 準確率對評估預算的曲線（任務×種子之平均 ± 標準誤）。LLM-OptFlow 以固定預設值播種（即其預算 1 的點），故其早期領先反映的是該種子；當經典搜尋獲得同一種子後優勢即消失，且即使不播種，隨機搜尋、TPE 與 GP-BO（40 次評估）也在 12 次評估時追平顧問、40 次時超越。]

---

### 5.2 Final-budget accuracy and a failure mode | 最終預算準確率與一種失敗模式

> [!quote] Original
> Table 2 gives held-out test accuracy at full budget. On most datasets all methods, including a sensible default, agree within ∼1 point: on the seven non-vehicle tasks the advisor and TPE are statistically indistinguishable (TPE−LLM = +0.20 pp, 95% CI [−0.01, +0.43], paired-t p = 0.10, n = 35), confirming that with adequate trials these tabular problems are not separated by the optimizer. The informative exception is vehicle: random search, TPE, and GP-BO all find a substantially better configuration (82.4/81.4/79.8%), and even successive halving reaches 79.3%, while LLM-OptFlow remains essentially at the default (73.3% vs. default 73.5%; per method, random−LLM = +9.06, TPE +8.11, GP-BO +6.54, SH +5.98 pp, with p < 0.01 for random/TPE/GP-BO and p = 0.02 for SH; random−LLM 95% CI [5.59, 12.13]). This one task in fact accounts for the entire pooled test-accuracy gap between classical search and the advisor (TPE−LLM over all eight datasets = +1.18 pp, p = 0.01, but a non-significant +0.20 pp once vehicle is removed); on every other task the advisor, the default, and tuned classical search are mutually indistinguishable. That every classical optimizer, including model-based Bayesian optimization, escapes this basin while the LLM does not isolates the failure as one of exploration, not of the search budget or the sampler: the LLM repeatedly proposes "reasonable" configurations near its prior, and even given the full 40-evaluation budget it never escapes (76.1% best-CV at both 12 and 40 evaluations).

> [!note] 翻譯
> 表 2 給出完整預算下的保留測試準確率。在多數資料集上，所有方法——包括合理的預設值——彼此相差約 1 個百分點以內：在七個非 vehicle 任務上，顧問與 TPE 在統計上無法區分（TPE−LLM = +0.20 pp，95% CI [−0.01, +0.43]，配對 t 檢定 p = 0.10，n = 35），確認在足夠的試驗次數下，這些表格問題並不因最佳化器不同而分出高下。具啟發性的例外是 vehicle：隨機搜尋、TPE 與 GP-BO 均找到明顯更佳的配置（82.4／81.4／79.8%），連逐次減半也達到 79.3%，而 LLM-OptFlow 基本上停留在預設值（73.3%，相對預設值 73.5%；逐一方法而言，random−LLM = +9.06、TPE +8.11、GP-BO +6.54、SH +5.98 pp，其中 random／TPE／GP-BO 之 p < 0.01，SH 之 p = 0.02；random−LLM 的 95% CI 為 [5.59, 12.13]）。事實上，僅此一個任務便解釋了經典搜尋與顧問之間全部的合併測試準確率差距（在全部八個資料集上 TPE−LLM = +1.18 pp，p = 0.01，但一旦移除 vehicle 即為不顯著的 +0.20 pp）；在其他每個任務上，顧問、預設值與調校過的經典搜尋彼此皆無法區分。每一個經典最佳化器——包括基於模型的貝葉斯最佳化——都能逃離此盆地（basin）而 LLM 不能，這將失敗定位為「探索」（exploration）的失敗，而非搜尋預算或取樣器的問題：LLM 反覆提出貼近其先驗的「合理」配置，即使給予完整的 40 次評估預算也始終未能逃離（12 與 40 次評估時的最佳 CV 皆為 76.1%）。

---

> [!quote] Original
> Table 2: Held-out test accuracy (%, mean±std over 5 seeds) at full budget. Random/TPE/GP-BO: 40 evals; LLM-OptFlow: 12; successive halving (SH) is multi-fidelity, consuming ∼6 full-fidelity-equivalent evals. On vehicle, the one task the optimizer separates, every classical method beats both the default and the LLM (bold marks the best classical optimizer there).
>
> | Dataset | Default | Random | Optuna-TPE | GP-BO | SH | LLM-OptFlow |
> |---|---|---|---|---|---|---|
> | churn | 95.4±0.5 | 95.5±0.5 | 95.5±0.5 | 95.6±0.5 | 95.3±0.4 | 95.3±0.4 |
> | credit-g | 76.4±0.6 | 75.5±1.6 | 77.1±0.9 | 76.8±1.0 | 74.7±2.9 | 76.6±0.9 |
> | hypothyroid | 98.1±0.1 | 98.1±0.2 | 98.1±0.3 | 98.0±0.5 | 97.8±0.3 | 98.0±0.4 |
> | ionosphere | 93.6±2.2 | 93.6±2.8 | 93.6±2.2 | 93.8±2.2 | 89.4±3.2 | 93.4±2.7 |
> | phoneme | 90.2±0.5 | 90.2±0.8 | 90.4±0.6 | 90.3±0.7 | 89.8±0.9 | 90.2±0.5 |
> | satimage | 91.2±0.6 | 91.4±0.5 | 91.3±0.6 | 91.5±0.6 | 89.3±1.9 | 91.3±0.5 |
> | spambase | 95.0±0.3 | 95.3±0.7 | 95.5±0.6 | 95.4±0.5 | 93.9±1.3 | 95.1±0.5 |
> | vehicle | 73.5±0.9 | **82.4±2.7** | 81.4±2.0 | 79.8±1.3 | 79.3±2.5 | 73.3±1.5 |

> [!note] 翻譯
> 表 2：完整預算下的保留測試準確率（%，5 個種子的平均±標準差）。隨機搜尋／TPE／GP-BO：40 次評估；LLM-OptFlow：12 次；逐次減半（SH）為多保真度方法，消耗約 6 次全保真度等效評估。在 vehicle——唯一由最佳化器分出高下的任務——上，每一個經典方法皆勝過預設值與 LLM（粗體標示該處最佳的經典最佳化器）。
>
> | 資料集 | Default | 隨機搜尋 | Optuna-TPE | GP-BO | SH | LLM-OptFlow |
> |---|---|---|---|---|---|---|
> | churn | 95.4±0.5 | 95.5±0.5 | 95.5±0.5 | 95.6±0.5 | 95.3±0.4 | 95.3±0.4 |
> | credit-g | 76.4±0.6 | 75.5±1.6 | 77.1±0.9 | 76.8±1.0 | 74.7±2.9 | 76.6±0.9 |
> | hypothyroid | 98.1±0.1 | 98.1±0.2 | 98.1±0.3 | 98.0±0.5 | 97.8±0.3 | 98.0±0.4 |
> | ionosphere | 93.6±2.2 | 93.6±2.8 | 93.6±2.2 | 93.8±2.2 | 89.4±3.2 | 93.4±2.7 |
> | phoneme | 90.2±0.5 | 90.2±0.8 | 90.4±0.6 | 90.3±0.7 | 89.8±0.9 | 90.2±0.5 |
> | satimage | 91.2±0.6 | 91.4±0.5 | 91.3±0.6 | 91.5±0.6 | 89.3±1.9 | 91.3±0.5 |
> | spambase | 95.0±0.3 | 95.3±0.7 | 95.5±0.6 | 95.4±0.5 | 93.9±1.3 | 95.1±0.5 |
> | vehicle | 73.5±0.9 | **82.4±2.7** | 81.4±2.0 | 79.8±1.3 | 79.3±2.5 | 73.3±1.5 |

---

### 5.3 The confidence filter, finally exercised | 信心過濾器的實際考驗

> [!quote] Original
> On well-formed proposals the filter never fires (rejection rate 0.00). To test its value, on three representative datasets (credit-g, spambase, churn) we inject an adversarial proposal channel that corrupts a fraction of proposals into configurations outside S (unknown model, out-of-range value, wrong type), the drift LLMs actually produce. Under a 0.40 corruption rate the filter rejects at 0.38 (near-perfect precision/recall, since it is rule-based), eliminating failed evaluations (1.5 → 0 per run) and cutting wasted evaluation time by ∼33% (13.2 → 8.8 s), while final accuracy is unchanged (0.889 vs. 0.890); see Figure 2. In a keep-best loop the filter does not improve accuracy, since bad configurations simply score poorly and are never selected, so its value is operational: it prevents wasted compute and runtime failures in an LLM-in-the-loop system.
>
> [Figure 2: Confidence filter under adversarial proposals: failed evaluations and wasted evaluation time per run, filter ON vs. OFF.]

> [!note] 翻譯
> 對格式良好的提案，過濾器從不觸發（拒絕率 0.00）。為檢驗其價值，我們在三個具代表性的資料集（credit-g、spambase、churn）上注入一個對抗性提案通道，將一部分提案破壞為 S 之外的配置（未知模型、超出範圍的值、錯誤型別）——這正是 LLM 實際會產生的漂移。在 0.40 的破壞率下，過濾器以 0.38 的比率拒絕（因其為規則式，精確率／召回率近乎完美），消除了失敗的評估（每次執行 1.5 → 0），並將浪費的評估時間削減約 33%（13.2 → 8.8 秒），而最終準確率不變（0.889 對 0.890）；見圖 2。在「保留最佳」（keep-best）迴圈中，過濾器不會提升準確率——因為糟糕的配置本來就得分低、永遠不會被選中——故其價值在於操作面：在 LLM 在迴圈中（LLM-in-the-loop）的系統裡，它防止算力浪費與執行期失敗。
>
> [圖 2：對抗性提案下的信心過濾器：每次執行的失敗評估數與浪費的評估時間，過濾器開啟與關閉之比較。]

---

### 5.4 Sensitivity to the LLM: a seven-model panel | 對 LLM 的敏感度：七模型面板

> [!quote] Original
> To test whether our findings depend on the specific advisor, we re-ran the identical advisor, confidence filter, and search space S across seven models spanning five providers and the nano-to-frontier range (Claude Haiku 4.5 and Sonnet 4.6, GPT-5-chat and GPT-4o-mini, Gemini 2.5 Flash, DeepSeek-V3, and Qwen3.7-max), through one OpenRouter pipeline, on the same 8 datasets / 5 seeds / 12-iteration budget (Table 3, Figure 3). Three findings emerge.
>
> **First, the first point is the seed, identical across models.** Every advisor's budget-1 value is the same 88.7% default (Table 1), confirming it is not an LLM output. The first genuine LLM proposal (Table 3, CV@1) lands at 88.8–89.6%; but at the matched 2-evaluation budget classical search has already reached random 85.9%, TPE 87.2%, GP-BO 87.5%, so after one real proposal the advisor's edge ranges from a fraction of a point (cheaper models) to ∼2 pp (frontier), consistent with §5.1.
>
> **Second, final accuracy varies across models, but only one task separates them at all.** Just one dataset (vehicle) separates the optimizers, so this observation rests on that task. There, the two strongest advisors, Sonnet 4.6 and Qwen3.7-max, reach the classical 82% and lead the panel (90.3% final test accuracy vs. 89.2–89.5% for the rest), while the others stay near the default. The relation is non-monotone, however: GPT-5-chat, also a frontier model, does not escape (75.1%), and once vehicle is removed all seven models tie (final ∼91.5%). We therefore report this as a single-task observation, not a scaling law.
>
> **Third, the vehicle exploration outcome is model-dependent on this one task.** Sonnet 4.6 and Qwen3.7-max escape the default basin (82.0 ± 2.0%, above the default on all five seeds, matching classical search's 82.4%), while the other five, including GPT-5-chat (75.1%), remain near the default (73–75%). Across all seven models the confidence filter fired at essentially zero on well-formed proposals (≤ 0.002), and the panel's Haiku 4.5 run reproduces the canonical Haiku result per seed to within run-to-run noise, confirming the two pipelines are consistent.

> [!note] 翻譯
> 為檢驗我們的發現是否依賴特定的顧問模型，我們在相同的顧問、信心過濾器與搜尋空間 S 下，透過單一 OpenRouter 管線，於同樣的 8 個資料集／5 個種子／12 次迭代預算上，重跑了橫跨五家供應商、涵蓋 nano 至前沿（frontier）等級的七個模型（Claude Haiku 4.5 與 Sonnet 4.6、GPT-5-chat 與 GPT-4o-mini、Gemini 2.5 Flash、DeepSeek-V3、Qwen3.7-max）（表 3、圖 3）。得出三項發現。
>
> **第一，第一個點是種子，且在各模型間完全相同。**每個顧問在預算 1 的數值都是同一個 88.7% 的預設值（表 1），證實其並非 LLM 輸出。第一個真正的 LLM 提案（表 3 的 CV@1）落在 88.8–89.6%；但在匹配的 2 次評估預算下，經典搜尋已達到：隨機搜尋 85.9%、TPE 87.2%、GP-BO 87.5%。因此在一個真實提案之後，顧問的優勢從不到一個百分點（較廉價的模型）到約 2 pp（前沿模型）不等，與 §5.1 一致。
>
> **第二，最終準確率因模型而異，但只有一個任務能區分它們。**僅有一個資料集（vehicle）能區分各最佳化器，故此觀察僅立足於該任務。在該任務上，兩個最強的顧問——Sonnet 4.6 與 Qwen3.7-max——達到了經典方法的 82% 並領先整個面板（最終測試準確率 90.3%，其餘為 89.2–89.5%），其他模型則停留在預設值附近。然而此關係並非單調：同為前沿模型的 GPT-5-chat 未能逃離（75.1%），且一旦移除 vehicle，七個模型全數打平（最終約 91.5%）。因此我們將此報告為單一任務的觀察，而非規模定律（scaling law）。
>
> **第三，vehicle 的探索結果在此單一任務上與模型相關。**Sonnet 4.6 與 Qwen3.7-max 逃離了預設值盆地（82.0 ± 2.0%，五個種子皆高於預設值，與經典搜尋的 82.4% 相當），而其餘五個模型——包括 GPT-5-chat（75.1%）——停留在預設值附近（73–75%）。在全部七個模型中，信心過濾器對格式良好的提案幾乎從不觸發（≤ 0.002），且面板中的 Haiku 4.5 執行在逐種子層級上於執行間雜訊範圍內重現了標準 Haiku 結果，證實兩條管線相互一致。

---

> [!quote] Original
> Table 3: Seven-model panel (8 datasets × 5 seeds, 12-iteration budget, one OpenRouter pipeline). CV@k is mean best-CV (%) after k LLM proposals; the fixed default seed is proposal 0, a uniform 88.7% across all models (Table 1). This proposal index is offset by one from Table 1's per-evaluation axis, which counts the seed as evaluation 1. Final and vehicle are mean held-out test accuracy (%); reject is the confidence-filter rate. The seed-driven start is model-independent; final accuracy and vehicle escape vary with model, but only vehicle separates the optimizers (rows sorted by final accuracy).
>
> | Model | CV@1 | CV@6 | CV@12 | Final | vehicle | reject |
> |---|---|---|---|---|---|---|
> | Claude Sonnet 4.6 | 89.6 | 89.9 | 89.9 | 90.3 | 82.0 | 0.00 |
> | Qwen3.7-max | 88.9 | 89.8 | 89.9 | 90.3 | 82.0 | 0.00 |
> | GPT-5-chat | 88.9 | 89.1 | 89.4 | 89.5 | 75.1 | 0.00 |
> | Claude Haiku 4.5 | 88.9 | 89.0 | 89.0 | 89.2 | 73.4 | 0.00 |
> | Gemini 2.5 Flash | 88.8 | 89.0 | 89.0 | 89.3 | 73.7 | 0.00 |
> | DeepSeek-V3 | 88.8 | 88.9 | 89.1 | 89.3 | 73.2 | 0.00 |
> | GPT-4o-mini | 88.8 | 88.9 | 88.9 | 89.3 | 73.4 | 0.00 |
>
> [Figure 3: Running-best CV accuracy vs. budget for all seven advisor models (mean over tasks×seeds). The budget-1 point is the shared default (identical across models); the LLM proposals plateau quickly across every model and provider, with frontier models plateauing slightly higher.]

> [!note] 翻譯
> 表 3：七模型面板（8 個資料集 × 5 個種子、12 次迭代預算、單一 OpenRouter 管線）。CV@k 為 k 個 LLM 提案後的平均最佳 CV（%）；固定的預設種子是第 0 號提案，在所有模型間一律為 88.7%（表 1）。此提案索引與表 1 的逐次評估軸相差一位——後者將種子計為第 1 次評估。Final 與 vehicle 為平均保留測試準確率（%）；reject 為信心過濾器的拒絕率。種子驅動的起點與模型無關；最終準確率與 vehicle 逃離與否隨模型而異，但只有 vehicle 能區分各最佳化器（各列依最終準確率排序）。
>
> | 模型 | CV@1 | CV@6 | CV@12 | Final | vehicle | 拒絕率 |
> |---|---|---|---|---|---|---|
> | Claude Sonnet 4.6 | 89.6 | 89.9 | 89.9 | 90.3 | 82.0 | 0.00 |
> | Qwen3.7-max | 88.9 | 89.8 | 89.9 | 90.3 | 82.0 | 0.00 |
> | GPT-5-chat | 88.9 | 89.1 | 89.4 | 89.5 | 75.1 | 0.00 |
> | Claude Haiku 4.5 | 88.9 | 89.0 | 89.0 | 89.2 | 73.4 | 0.00 |
> | Gemini 2.5 Flash | 88.8 | 89.0 | 89.0 | 89.3 | 73.7 | 0.00 |
> | DeepSeek-V3 | 88.8 | 88.9 | 89.1 | 89.3 | 73.2 | 0.00 |
> | GPT-4o-mini | 88.8 | 88.9 | 88.9 | 89.3 | 73.4 | 0.00 |
>
> [圖 3：全部七個顧問模型的迄今最佳 CV 準確率對預算曲線（任務×種子之平均）。預算 1 的點是共享的預設值（各模型完全相同）；每個模型與供應商的 LLM 提案都快速進入高原期，前沿模型的高原略高。]

---

## 6 Discussion | 討論

> [!quote] Original
> The evidence supports a cautionary, practical claim: on tabular HPO, the warm-start attributed to LLM advisors is a default configuration, not the model. A single hand-chosen default reaches the advisor's headline first-evaluation accuracy; the LLM's proposals add ≤ 0.4 pp on the CV objective and nothing on held-out test (p = 0.92); and once the same default is granted to classical search, the advisor's early lead is gone within five evaluations and reversed by twelve. Even without the seed, tuned classical search ties the advisor by twelve evaluations and passes it by forty, at a far lower cost per proposal and with no API dependency. The actionable recommendation is therefore the opposite of the usual pitch: seed classical search with a sensible default, or a cheap meta-learned initialization (Feurer et al., 2015b), rather than pay for an LLM in the loop. The two LLM-specific behaviors we did find are a caution and a convenience, not a case for the advisor: the vehicle exploration failure shows the advisor can lock onto its prior and miss a basin every classical method finds, and the one component that helped, the confidence filter, is a rule-based validator that needs no LLM at all.

> [!note] 翻譯
> 證據支持一項具警示性且實用的主張：在表格資料 HPO 上，被歸功於 LLM 顧問的暖啟動其實是一個預設配置，而非模型。單一人工挑選的預設值即可達到顧問標榜的首次評估準確率；LLM 的提案在 CV 目標上增添 ≤ 0.4 pp，在保留測試集上毫無增益（p = 0.92）；而當同一預設值被授予經典搜尋後，顧問的早期領先在五次評估內消失、十二次評估時反轉。即使沒有種子，調校過的經典搜尋也在十二次評估時追平顧問、四十次時超越——且每次提案的成本遠低於 LLM，亦無 API 依賴。因此，可付諸行動的建議與慣常的推銷說詞恰恰相反：以合理的預設值、或低成本的元學習初始化（Feurer et al., 2015b）為經典搜尋播種，而非付費將 LLM 置入迴圈。我們確實發現的兩個 LLM 特有行為，一是警訊、一是便利，皆不足以構成支持顧問的理由：vehicle 的探索失敗顯示顧問可能鎖死於其先驗、錯過每個經典方法都能找到的盆地；而唯一有幫助的組件——信心過濾器——是一個完全不需要 LLM 的規則式驗證器。

---

## 7 Limitations | 限制

> [!quote] Original
> Our seeded-baseline control is exact for random search (random draws are independent of the seed) but approximate for the model-based optimizers: enqueuing the default would also shift TPE's and GP-BO's subsequent draws, so we report the exact random-search control and note that un-seeded TPE and GP-BO already tie and then pass the advisor (§5.1), so seeding them could only strengthen the conclusion; a fully re-seeded model-based study is straightforward future work. The capability-related observations rest on a single exploration-separating task (vehicle); confirming any scaling relationship needs more exploration-hard datasets. We study tabular classification with sklearn model families; deep-learning HPO (where tiny budgets, and thus a good initialization, matter most) is the natural extension, but its per-trial cost made a budget-matched, multi-seed study impractical on our hardware. Our seven-model panel covers five providers and the nano-to-frontier range, though all are general-purpose chat models rather than HPO-specialized agents. We run many paired tests without a multiple-comparison correction; the load-bearing results survive a Bonferroni adjustment (p ≤ 10−4 throughout), and the one borderline result (pooled TPE−LLM, p = 0.015) is used only to deflate the classical advantage, so correction would only reinforce our conclusions. The adversarial channel is synthetic but mimics observed LLM drift.

> [!note] 翻譯
> 我們的播種基線對照對隨機搜尋是精確的（隨機抽樣與種子無關），但對基於模型的最佳化器僅為近似：將預設值排入佇列也會改變 TPE 與 GP-BO 的後續抽樣，因此我們報告精確的隨機搜尋對照，並指出未播種的 TPE 與 GP-BO 本已追平並隨後超越顧問（§5.1），故對其播種只會強化結論；完整重新播種的模型式研究是直接了當的未來工作。與能力相關的觀察僅立足於單一能區分探索能力的任務（vehicle）；要確認任何規模關係，需要更多探索困難（exploration-hard）的資料集。我們研究的是使用 sklearn 模型家族的表格分類；深度學習 HPO（其中極小的預算——因而良好的初始化——最為關鍵）是自然的延伸，但其單次試驗成本使預算匹配、多種子的研究在我們的硬體上不切實際。我們的七模型面板涵蓋五家供應商與 nano 至前沿等級，惟其皆為通用聊天模型，而非 HPO 專用代理。我們進行了許多配對檢定而未做多重比較校正；承重的（load-bearing）結果在 Bonferroni 調整後依然成立（全程 p ≤ 10⁻⁴），而唯一處於邊緣的結果（合併的 TPE−LLM，p = 0.015）僅用於「消風」經典方法的優勢，故校正只會強化我們的結論。對抗性通道雖為合成，但模仿了實際觀察到的 LLM 漂移。

---

## 8 Conclusion | 結論

> [!quote] Original
> Under a rigorous, budget-matched, multi-seed protocol, the apparent sample-efficiency of an LLM hyperparameter advisor on tabular data is an artifact of the default configuration it is seeded with: the model's own proposals add at most 0.4 pp on the cross-validation objective and no measurable held-out improvement, and default-seeded classical search matches the advisor within a few evaluations and surpasses it thereafter. The honest recommendation is to seed classical search with a good default rather than pay for an LLM in the loop. We release the harness and a script that reproduces every statistic, so both the claim and its limits can be checked directly.

> [!note] 翻譯
> 在嚴謹、預算匹配、多種子的協定下，LLM 超參數顧問在表格資料上表面的樣本效率，實為其播種所用預設配置造成的假象（artifact）：模型本身的提案在交叉驗證目標上至多增添 0.4 pp，在保留測試集上無可量測的改善；而以預設值播種的經典搜尋在數次評估內即追平顧問，其後加以超越。誠實的建議是：以良好的預設值為經典搜尋播種，而非付費將 LLM 置入迴圈。我們釋出實驗框架與可重現每一項統計數字的腳本，使此主張及其界限皆可被直接檢驗。

---

## References | 參考文獻

> [!info] References omitted / 參考文獻略
