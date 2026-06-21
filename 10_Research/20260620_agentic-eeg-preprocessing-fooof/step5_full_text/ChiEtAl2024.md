---
citation_key: "ChiEtAl2024"
title: "SELA: Tree-Search Enhanced LLM Agents for Automated Machine Learning"
year: 2024
access_level: "full-text-html"
source_url: "https://arxiv.org/html/2410.17238v1"
---

# SELA: Tree-Search Enhanced LLM Agents for Automated Machine Learning

## 摘要 / Abstract
> [!abstract] Original
> Automated Machine Learning (AutoML) approaches encompass traditional methods that optimize fixed pipelines for model selection and ensembling, as well as newer LLM-based frameworks that autonomously build pipelines. While LLM-based agents have shown promise in automating machine learning tasks, they often generate low-diversity and suboptimal code, even after multiple iterations. To overcome these limitations, we introduce Tree-Search Enhanced LLM Agents (SELA), an innovative agent-based system that leverages Monte Carlo Tree Search (MCTS) to optimize the AutoML process. By representing pipeline configurations as trees, our framework enables agents to conduct experiments intelligently and iteratively refine their strategies, facilitating a more effective exploration of the machine learning solution space. This novel approach allows SELA to discover optimal pathways based on experimental feedback, improving the overall quality of the solutions. In an extensive evaluation across 20 machine learning datasets, we compare the performance of traditional and agent-based AutoML methods, demonstrating that SELA achieves a win rate of 65% to 80% against each baseline across all datasets. These results underscore the significant potential of agent-based strategies in AutoML, offering a fresh perspective on tackling complex machine learning challenges.

> [!abstract] 繁體中文摘要
> 自動化機器學習（AutoML）方法涵蓋為模型選擇與集成最佳化固定管線的傳統方法，以及自主建構管線的新型 LLM 框架。雖然基於 LLM 的代理在自動化機器學習任務上展現潛力，它們即使經過多次迭代，仍常生成低多樣性且次佳的程式碼。為克服這些限制，我們提出 Tree-Search Enhanced LLM Agents（SELA）—一個運用蒙地卡羅樹搜尋（MCTS）來最佳化 AutoML 流程的創新代理系統。藉由將管線設定表示為樹，我們的框架使代理能聰明地進行實驗並迭代精煉策略，促成對機器學習解空間更有效的探索。此新穎方法讓 SELA 能基於實驗回饋發現最佳路徑，提升解的整體品質。在涵蓋 20 個機器學習資料集的廣泛評估中，我們比較傳統與代理型 AutoML 方法，證明 SELA 在所有資料集上對每個基準都達到 65% 至 80% 的勝率。這些結果凸顯代理型策略在 AutoML 中的重大潛力，為應對複雜機器學習挑戰提供了嶄新視角。

## Key Points / 重點筆記

> [!note] What problem it solves / 解決什麼問題
> EN: LLM agents doing AutoML tend to produce low-diversity, suboptimal pipelines and don't improve much even after iterating, because they lack a principled way to explore the large space of pipeline configurations. SELA fixes this by wrapping the LLM agent in Monte Carlo Tree Search, so exploration of alternative pipeline choices is systematic and feedback-driven, mimicking how a human expert tests and refines configurations rather than committing to one attempt.
> 中: 做 AutoML 的 LLM 代理傾向產出低多樣性、次佳的管線，即使迭代也改善有限，因為它們缺乏有原則地探索龐大管線設定空間的方法。SELA 以蒙地卡羅樹搜尋包裹 LLM 代理來解決此問題，使對替代管線選擇的探索系統化且由回饋驅動，模擬人類專家測試與精煉設定的方式，而非只承諾單一嘗試。

> [!note] Method / 方法
> EN: SELA conceptualizes the ML solution space as a **tree**, where each branch is a potential decision in stage-wise pipeline construction (Exploratory Data Analysis → Data Preprocessing → Feature Engineering → Model Training). An LLM first proposes *insights* to populate the search space; then **MCTS** (selection / expansion / simulation / backpropagation, with UCT-style exploration–exploitation balancing) navigates the tree. Each **experiment node** corresponds to a concrete pipeline configuration that an LLM agent turns into runnable code; the node is executed, the resulting score is backpropagated to update value estimates, and the next promising configuration is selected. Experiment state is saved/loaded so rollouts build on prior results, incrementally refining solutions.
> 中: SELA 將 ML 解空間概念化為一棵**樹**，每條分支是分階段管線建構中的一個潛在決策（探索式資料分析 → 資料前處理 → 特徵工程 → 模型訓練）。LLM 先提出*洞見*來填充搜尋空間；接著以**MCTS**（選擇/擴展/模擬/回傳，採 UCT 式探索–利用平衡）在樹中導航。每個**實驗節點**對應一個具體管線設定，由 LLM 代理轉為可執行程式碼；該節點被執行，所得分數回傳以更新價值估計，再選出下一個有前景的設定。實驗狀態被儲存/載入，使各 rollout 建立於先前結果之上，逐步精煉解。

> [!note] Key findings / 主要發現
> EN: Across 20 ML datasets, SELA achieves a 65%–80% win rate against each baseline (traditional AutoML such as AutoGluon/Auto-Sklearn and other LLM-agent methods). Ablations show that the tree search itself is the key driver (vs. linear iteration), that performance improves with more rollouts, and that SELA is adaptable across different backbone LLMs. The MCTS exploration–exploitation balance produces more diverse, higher-quality pipelines than single-pass or naively-iterated LLM agents.
> 中: 在 20 個 ML 資料集上，SELA 對每個基準（傳統 AutoML 如 AutoGluon/Auto-Sklearn 及其他 LLM 代理方法）達到 65%–80% 勝率。消融顯示樹搜尋本身是關鍵驅動（相對於線性迭代）、表現隨 rollout 增多而提升、且 SELA 可跨不同骨幹 LLM 適應。MCTS 的探索–利用平衡產出比單次或樸素迭代 LLM 代理更多樣、更高品質的管線。

> [!tip] Relevance to "Agentic EEG preprocessing + FOOOF-SNR" project / 與本計畫的關聯
> EN: SELA is the search-algorithm backbone that upgrades our greedy ground-truth search and the LLM agent. (c) Greedy-search ground truth: our greedy search is the *baseline* search strategy; SELA shows that replacing greedy/linear iteration with **MCTS over a pipeline tree** finds better configurations — so our ground-truth generator can be strengthened from greedy to tree search, and the project can position greedy search as the cheap ground truth and MCTS as the strong upper bound. (a) Action space: SELA's stage-wise tree (EDA → preprocessing → feature engineering → model) is structurally identical to an ordered EEG preprocessing tree (filter → bad-channel → re-reference → ASR → ICA → …); each tree branch = a preprocessing action/parameter choice, making EEG preprocessing a natural MCTS search problem. (b) FOOOF-SNR as the node reward: SELA's experiment node executes a pipeline and backpropagates a *score*; in our setting the FOOOF-SNR (gated by Gerster's fit-validity checks) is that score — a label-free, per-recording reward that MCTS maximizes, so we get an unsupervised tree search for EEG preprocessing. The "low-diversity, suboptimal code" problem SELA solves is exactly the failure mode an LLM-only EEG-preprocessing agent would have; SELA's rollout-count scaling tells us how to trade compute for pipeline quality during ground-truth generation.
> 中: SELA 是升級我們貪婪真值搜尋與 LLM 代理的搜尋演算法骨幹。(c) 貪婪搜尋真值：我們的貪婪搜尋是*基準*搜尋策略；SELA 證明以**管線樹上的 MCTS** 取代貪婪/線性迭代能找到更好的設定—因此我們的真值產生器可從貪婪強化為樹搜尋，計畫可將貪婪搜尋定位為便宜的真值、MCTS 為強上界。(a) 動作空間：SELA 的分階段樹（EDA → 前處理 → 特徵工程 → 模型）在結構上與有序的 EEG 前處理樹（濾波 → 壞通道 → 重新參考 → ASR → ICA → …）完全相同；每條樹分支＝一個前處理動作/參數選擇，使 EEG 前處理成為自然的 MCTS 搜尋問題。(b) FOOOF-SNR 作為節點獎勵：SELA 的實驗節點執行管線並回傳一個*分數*；在我們的設定中，FOOOF-SNR（由 Gerster 的擬合有效性檢查把關）正是該分數—一個無標籤、逐紀錄的獎勵，供 MCTS 最大化，於是我們得到一個無監督的 EEG 前處理樹搜尋。SELA 所解決的「低多樣性、次佳程式碼」問題，正是純 LLM 的 EEG 前處理代理會有的失效模式；SELA 的 rollout 數量縮放則告訴我們在真值產生時如何以計算量換取管線品質。

## Full Text / 全文

### Abstract
AutoML spans traditional fixed-pipeline optimization and newer LLM-based pipeline builders, but LLM agents generate low-diversity, suboptimal code even after iterating. **SELA (Tree-Search Enhanced LLM Agents)** uses **Monte Carlo Tree Search (MCTS)** to optimize AutoML: pipeline configurations are represented as trees, letting agents experiment intelligently and refine strategies, exploring the ML solution space more effectively and discovering optimal pathways from experimental feedback. Across 20 datasets, SELA wins 65%–80% against each baseline. Code: github.com/geekan/MetaGPT.

### 1 Introduction
Traditional AutoML (Auto-WEKA, Auto-Sklearn, AutoGluon, H2O) relies on predefined search spaces and mainly optimizes hyperparameters and ensembling, lacking adaptability and underexploring data preprocessing and feature engineering. LLM-based agents are more dynamic but produce low-diversity, suboptimal solutions. Human experts instead explore configurations, run experiments, analyze results, and iteratively refine. Inspired by this, SELA combines LLM agents with structured search: **stage-wise planning** (EDA, Data Preprocessing, Feature Engineering, Model Training) plus an **iterative refinement mechanism**. The search space is a tree; **MCTS** is the decision engine, balancing exploration (new strategies) and exploitation (improving known-good strategies), efficiently navigating large decision spaces and selecting the next promising configuration.

### 2 Related Works
- **Tree search:** MCTS has advanced problem-solving in robotics, chemistry, and gaming, and is increasingly combined with LLMs for reasoning/decision-making (efficient exploration, exploiting learned knowledge, AlphaZero-style search, planning with external/self-evaluated feedback).
- **AutoML:** early frameworks automated HPO, model selection, stacking, ensembling, with meta-learning; extensions cover multi-modal data.
- **LLM-based agents:** hierarchical-graph agents with programmable node generation, agents applying past experience, case-based reasoning (DS-Agent, which struggles generating from scratch due to reliance on existing codebases), and one-step-then-iteratively-refine approaches.

### 3 Method
**3.1 Insight Proposal and Search Space Creation:** an LLM proposes insights that define/populate the tree-structured search space over pipeline stages.
**3.2 Pipeline Execution and Code Generation:** an LLM agent turns a selected configuration (a path in the tree) into runnable code and executes it.
**3.3 Tree Search in ML Experiments:**
- **3.3.1 Experiment Node:** each node is a concrete pipeline configuration with an associated experimental result/score.
- **3.3.2 Tree Search for ML Experiments:** MCTS (selection, expansion, simulation, backpropagation; UCT-style exploration–exploitation) navigates the tree, adaptively identifying high-performance pipelines from feedback.
- **3.3.3 Experiment State Saving and Loading:** state is persisted so rollouts build on prior results.

### 4 Experiments
**4.1 Setup:** 20 datasets; evaluation metrics; baselines include traditional AutoML and LLM-agent methods.
**4.2 Results:** SELA achieves a 65%–80% win rate against each baseline across all datasets.
**4.3 Ablation:** (i) *Effectiveness of search* — tree search drives the gains; (ii) *Number of rollouts* — more rollouts improve performance; (iii) *LLM adaptability* — SELA works across different backbone LLMs.

### 5 Conclusion
SELA integrates MCTS with LLM agents to explore and refine ML pipelines as a tree, achieving strong win rates and demonstrating the potential of agent-based, tree-search strategies for AutoML.
