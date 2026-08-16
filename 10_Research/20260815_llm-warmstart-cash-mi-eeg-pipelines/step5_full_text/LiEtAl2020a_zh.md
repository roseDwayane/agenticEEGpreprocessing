---
citation_key: "LiEtAl2020a"
title: "Efficient Automatic CASH via Rising Bandits"
authors: "Yang Li; Jiawei Jiang; Jinyang Gao; Yingxia Shao; Ce Zhang; B. Cui"
year: 2020
doi: "10.1609/aaai.v34i04.5910"
source: "arXiv (2012.04371)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "2012.04371"
conversion: "pdftotext -layout (automated)"
language: "bilingual-en-zh"
---

# Efficient Automatic CASH via Rising Bandits | 以上升賭博機實現高效的自動化 CASH

> [!abstract] 重點摘要
> - 針對組合式演算法選擇與超參數最佳化（Combined Algorithm Selection and Hyperparameter optimization, CASH）問題，指出既有貝葉斯最佳化（Bayesian optimization, BO）解法將所有演算法的超參數合併為單一巨大空間，導致 BO 在高維空間中效率低落。
> - 提出交替最佳化框架（alternating optimization framework）：將 CASH 重構為雙層最佳化問題，以 BO 分別解各演算法的超參數最佳化（HPO），並以多臂賭博機（Multi-Armed Bandits, MAB）進行演算法選擇與資源分配。
> - 提出上升賭博機（Rising Bandits）——一種面向 CASH 的非平穩 MAB 變體：各臂的期望報酬隨拉動次數遞增且近似凹性（concave），目標為最大化觀測到的最佳報酬而非累積報酬。
> - 設計基於報酬上下界的淘汰準則之線上演算法，逐步剔除不可能最優的演算法，並提供遺憾上界（regret bound）等理論保證；另提出平滑成長率與成本感知（cost-aware）變體。
> - 在 30 個 OpenML 資料集上，該方法於 26/30 資料集取得最佳驗證準確率，相較 SMAC 平均加速約 12.6 倍（最高逾一個數量級），並在 AutoML 系統比較中勝過 Auto-Sklearn、Hyperopt-Sklearn 與 TPOT。

---

## Abstract | 摘要

> [!quote] Original
> The Combined Algorithm Selection and Hyperparameter optimization (CASH) is one of the most fundamental problems in Automatic Machine Learning (AutoML). The existing Bayesian optimization (BO) based solutions turn the CASH problem into a Hyperparameter Optimization (HPO) problem by combining the hyperparameters of all machine learning (ML) algorithms, and use BO methods to solve it. As a result, these methods suffer from the low-efficiency problem due to the huge hyperparameter space in CASH. To alleviate this issue, we propose the alternating optimization framework, where the HPO problem for each ML algorithm and the algorithm selection problem are optimized alternately. In this framework, the BO methods are used to solve the HPO problem for each ML algorithm separately, incorporating a much smaller hyperparameter space for BO methods. Furthermore, we introduce Rising Bandits, a CASH-oriented Multi-Armed Bandits (MAB) variant, to model the algorithm selection in CASH. This framework can take the advantages of both BO in solving the HPO problem with a relatively small hyperparameter space and the MABs in accelerating the algorithm selection. Moreover, we further develop an efficient online algorithm to solve the Rising Bandits with provably theoretical guarantees. The extensive experiments on 30 OpenML datasets demonstrate the superiority of the proposed approach over the competitive baselines.

> [!note] 翻譯
> 組合式演算法選擇與超參數最佳化（Combined Algorithm Selection and Hyperparameter optimization, CASH）是自動化機器學習（Automatic Machine Learning, AutoML）中最基本的問題之一。既有基於貝葉斯最佳化（Bayesian optimization, BO）的解法，將所有機器學習（ML）演算法的超參數合併，把 CASH 問題轉化為單一的超參數最佳化（Hyperparameter Optimization, HPO）問題，再以 BO 方法求解。其結果是，由於 CASH 的超參數空間極為龐大，這些方法飽受效率低落之苦。為緩解此問題，我們提出交替最佳化框架（alternating optimization framework），其中各 ML 演算法的 HPO 問題與演算法選擇問題被交替地最佳化。在此框架中，BO 方法被用於分別求解各 ML 演算法的 HPO 問題，使 BO 面對的超參數空間大幅縮小。此外，我們引入上升賭博機（Rising Bandits）——一種面向 CASH 的多臂賭博機（Multi-Armed Bandits, MAB）變體——來建模 CASH 中的演算法選擇。此框架能同時發揮兩者之長：BO 擅長在相對較小的超參數空間中求解 HPO 問題，而 MAB 則能加速演算法選擇。再者，我們進一步發展了一個高效的線上演算法來求解上升賭博機，並具有可證明的理論保證。在 30 個 OpenML 資料集上的大量實驗，證明了所提方法優於各競爭基線。

---

## Introduction | 引言

> [!quote] Original
> Machine learning (ML) has made great strides in many application areas, e.g., recommendation, computer vision, financial market analysis, etc (Goodfellow, Bengio, and Courville 2016; He et al. 2017; Ma et al. 2019; Zhao, Shen, and Huang 2019). However, given a practical application, it is usually knowledge-intensive and labor-intensive to develop customized solutions with satisfied learning performance, where the exploration may include but is not limited to selecting ML algorithms, configuring hyperparameters and network architecture searching. To facilitate the deployment of ML applications and democratize the usage of machine learning, it is of vital importance to reduce human efforts during such exploration. Naturally, automatic machine learning (Quanming et al. 2018; Zöller and Huber 2019) has attracted lots of interest from both industry and academia.

> [!note] 翻譯
> 機器學習（ML）已在許多應用領域取得長足進展，例如推薦系統、電腦視覺、金融市場分析等 (Goodfellow, Bengio, and Courville 2016; He et al. 2017; Ma et al. 2019; Zhao, Shen, and Huang 2019)。然而，對於一個實際應用而言，開發具有令人滿意之學習效能的客製化解決方案，通常既需要大量專業知識又耗費人力，其中的探索過程包括但不限於：選擇 ML 演算法、設定超參數以及網路架構搜尋。為促進 ML 應用的部署並使機器學習的使用大眾化，減少此類探索過程中的人力投入至關重要。自然地，自動化機器學習 (Quanming et al. 2018; Zöller and Huber 2019) 已吸引產業界與學術界的廣泛關注。

---

> [!quote] Original
> Given a learning problem, the first thing is to decide which ML algorithm should be applied – from SVM, Adaboost, GBDT (Jiang et al. 2018; Jiang et al. 2017) to deep neural networks. According to the No Free Lunch theorem (Ho and Pepyne 2001), no single ML algorithm can achieve the best performance for all the learning problems; and there is often no golden standard to predict which ML algorithm performs the best. As a result, we typically spend computational resources across all reasonable ML algorithms, and choose the one with the best performance after the optimization of their hyperparameters and network architectures. However, solving the algorithm selection problem after sufficiently optimizing the hyperparameters of each ML algorithm leads to inefficient usage of computational resources. Resources consumed by the poor-performing algorithms are greatly wasted. To this end, the Combined Algorithm Selection and Hyperparameter optimization (CASH) problem (Feurer et al. 2015; Kotthoff et al. 2017) is proposed to jointly optimize the selection of algorithm and its hyperparameters, which is the core focus of this paper.

> [!note] 翻譯
> 給定一個學習問題，首要任務是決定應採用哪一種 ML 演算法——從 SVM、Adaboost、GBDT (Jiang et al. 2018; Jiang et al. 2017) 到深度神經網路皆是候選。根據沒有免費午餐定理（No Free Lunch theorem）(Ho and Pepyne 2001)，沒有任何單一 ML 演算法能在所有學習問題上都達到最佳表現；且往往不存在黃金標準能預測哪一種 ML 演算法表現最佳。因此，我們通常會把計算資源分散於所有合理的 ML 演算法，並在各演算法的超參數與網路架構最佳化之後，選擇表現最佳者。然而，在充分最佳化每個 ML 演算法的超參數之後再解決演算法選擇問題，會導致計算資源使用效率低落——耗費在表現不佳演算法上的資源被大量浪費。為此，組合式演算法選擇與超參數最佳化（CASH）問題 (Feurer et al. 2015; Kotthoff et al. 2017) 被提出，以聯合最佳化演算法的選擇及其超參數，這正是本文的核心焦點。

---

> [!quote] Original
> To solve the CASH problem, a class of methods (Komer, Bergstra, and Eliasmith 2014; Feurer et al. 2015; Kotthoff et al. 2017) transform the CASH problem into a unified hyperparameter optimization (HPO) problem by merging the hyperparameter space for all ML algorithms and treating the selection of algorithm as a new hyperparameter. Then classical Bayesian optimization (BO) methods (Shahriari et al. 2015) are utilized to solve this HPO problem. Consequently, these methods incorporate a huge optimization space with high-dimensional hyperparameters for BO methods. Past works (Eggensperger et al. 2013) show that BO methods perform well for relatively low-dimensional hyperparameters. However, for high-dimensional problems, standard BO methods perform even worse than random search (Wang et al. 2013). Thus, such a huge hyperparameter space greatly hampers the efficiency of Bayesian optimization.

> [!note] 翻譯
> 為求解 CASH 問題，一類方法 (Komer, Bergstra, and Eliasmith 2014; Feurer et al. 2015; Kotthoff et al. 2017) 藉由合併所有 ML 演算法的超參數空間，並將演算法的選擇視為一個新的超參數，把 CASH 問題轉化為統一的超參數最佳化（HPO）問題，繼而利用古典貝葉斯最佳化（BO）方法 (Shahriari et al. 2015) 求解。其結果是，這些方法讓 BO 面對一個具有高維超參數的龐大最佳化空間。過往研究 (Eggensperger et al. 2013) 顯示，BO 方法在相對低維的超參數上表現良好；然而對於高維問題，標準 BO 方法甚至比隨機搜尋（random search）還差 (Wang et al. 2013)。因此，如此龐大的超參數空間嚴重阻礙了貝葉斯最佳化的效率。

---

> [!quote] Original
> To alleviate the above issue, it is natural to consider another paradigm where the BO methods are used to solve the HPO problem for each ML algorithm separately, and the algorithm selection is responsible for determining the allocation of resources to each ML algorithm's HPO process. Based on this idea, we propose the alternating optimization framework, where the HPO problem for each ML algorithm and the algorithm selection problem are optimized alternately. Benefiting from solving the HPO problem for each ML algorithm individually, this framework brings a much smaller hyperparameter space for BO methods. Furthermore, within this framework, the resources can be adaptively allocated to the HPO process of each algorithm based on their performance. Intuitively, spending too many resources in tuning the hyperparameters of poor-performing algorithms should be avoided; instead, more resources should be allocated to the more promising ML algorithms that can achieve the best performance. Unfortunately, which algorithm is the best is unknown unless enough resources are allocated to its HPO process. Therefore, solving the CASH problem efficiently requires to trade off the well-celebrated Exploration vs. Exploitation (EvE) dilemma during algorithm selection: should we explore the HPO of different ML algorithms to find the optimal algorithm (Exploration), or give more credit to the best algorithm observed so far to further conduct HPO (Exploitation)?

> [!note] 翻譯
> 為緩解上述問題，一個自然的想法是採用另一種典範：以 BO 方法分別求解各 ML 演算法的 HPO 問題，而演算法選擇則負責決定分配給各 ML 演算法 HPO 過程的資源。基於此想法，我們提出交替最佳化框架，其中各 ML 演算法的 HPO 問題與演算法選擇問題被交替地最佳化。得益於對每個 ML 演算法個別求解 HPO 問題，此框架讓 BO 方法面對的超參數空間大幅縮小。此外，在此框架內，資源可依各演算法的表現自適應地分配至其 HPO 過程。直觀而言，應避免在調校表現不佳演算法的超參數上投入過多資源；相反地，應將更多資源分配給更有希望達到最佳表現的 ML 演算法。遺憾的是，除非為某演算法的 HPO 過程分配了足夠資源，否則無從得知哪個演算法最佳。因此，高效求解 CASH 問題需要在演算法選擇過程中權衡著名的探索與利用（Exploration vs. Exploitation, EvE）兩難：我們應該探索不同 ML 演算法的 HPO 以找出最優演算法（探索），還是給予目前觀測到的最佳演算法更多資源以進一步進行 HPO（利用）？

---

> [!quote] Original
> Since the EvE dilemma has been intensively studied in the context of Multi-Armed Bandits (MAB), here we propose to solve the algorithm selection problem in the framework of MAB. In this setting, each arm is associated with the corresponding HPO process of an ML algorithm. Pulling an arm means that a unit of resource is assigned to the HPO process of the corresponding algorithm, and the reward corresponds to the result from the HPO process. However, the existing MABs cannot be directly applied to model the algorithm selection problem for two reasons: 1) the well-studied objectives of MABs (e.g., accumulated rewards) are not consistent with the target of CASH problem that aims to maximize the observed reward; 2) because the HPO results will be improved with the increase of the HPO resource, the reward distribution of each arm is not stationary over time.

> [!note] 翻譯
> 由於 EvE 兩難已在多臂賭博機（Multi-Armed Bandits, MAB）的脈絡下被深入研究，我們在此提出於 MAB 框架下求解演算法選擇問題。在此設定中，每支臂（arm）對應一個 ML 演算法的 HPO 過程；拉動一支臂意味著將一單位資源分配給對應演算法的 HPO 過程，而報酬（reward）則對應該 HPO 過程的結果。然而，既有的 MAB 無法直接用於建模演算法選擇問題，原因有二：1) MAB 中被深入研究的目標（例如累積報酬）與 CASH 問題旨在最大化觀測到的報酬之目標並不一致；2) 由於 HPO 結果會隨 HPO 資源的增加而改善，每支臂的報酬分布並非隨時間平穩（stationary）。

---

> [!quote] Original
> The main contributions of this paper are the following:
> - We propose the alternating optimization framework to solve the CASH problem efficiently, which optimizes the algorithm selection problem and the HPO problem for each ML algorithm in an alternating manner. It takes the advantages of both BO methods in solving the HPO problem with a relatively small hyperparameter space and MABs in accelerating the algorithm selection.
> - We introduce a novel, CASH-oriented MAB formulation, termed Rising Bandits, where each arm's expected reward increases as a function of the number of times it has been pulled. To the best of our knowledge, this is the first work that models the algorithm selection problem in the framework of non-stationary MABs.
> - We present an easy-to-follow online algorithm for the Rising Bandits, accompanied with provably theoretical guarantees.
> - The empirical studies on 30 OpenML datasets demonstrate the superiority of the proposed method over the state-of-the-art baselines in terms of final accuracy and efficiency. Our method can achieve an order of magnitude speedups compared with BO based solutions.

> [!note] 翻譯
> 本文的主要貢獻如下：
> - 我們提出交替最佳化框架以高效求解 CASH 問題，該框架以交替方式最佳化演算法選擇問題與各 ML 演算法的 HPO 問題，同時發揮 BO 方法在相對較小超參數空間中求解 HPO 問題之長，以及 MAB 在加速演算法選擇上的優勢。
> - 我們引入一種新穎、面向 CASH 的 MAB 形式化，稱為上升賭博機（Rising Bandits），其中每支臂的期望報酬是其被拉動次數的遞增函數。據我們所知，這是首個在非平穩 MAB 框架下建模演算法選擇問題的工作。
> - 我們提出一個簡明易行的上升賭博機線上演算法，並附有可證明的理論保證。
> - 在 30 個 OpenML 資料集上的實證研究顯示，所提方法在最終準確率與效率兩方面均優於最先進的基線；相較於基於 BO 的解法，我們的方法可達到一個數量級的加速。

---

## Preliminaries and Related Works | 預備知識與相關研究

> [!quote] Original
> We first introduce the basic notations for the CASH problem. There are K candidate algorithms A = {A1, ..., AK}. Each algorithm Ai has a corresponding hyperparameter space Λi. The algorithm Ai with a hyperparameter λ is denoted by Ai_λ. Given the dataset D = {D_train, D_valid} of a learning problem, the CASH problem is to find the joint algorithm and hyperparameter configuration A*_λ* that minimizes the loss metric (e.g., the validation error on D_valid):
>
> A*_λ* = argmin_{Ai∈A, λ∈Λi} L(Ai_λ, D).   (1)
>
> Hyperparameter optimization (HPO) is to find the hyperparameter configuration λ* of a given algorithm Ai, which has the best performance on the validation set,
>
> λ* = argmin_{λ∈Λi} L(Ai_λ, D).   (2)
>
> Bayesian optimization (BO) has been successfully applied to solve the HPO problem. Sequential Model-based Algorithm Configuration (SMAC) (Hutter, Hoos, and Leyton-Brown 2011), Tree-structure Parzen Estimator (TPE) (Bergstra et al. 2011), and Spearmint (Snoek, Larochelle, and Adams 2012) are three well-established methods. It is important to note that these approaches can be executed in a sequential manner. That is, the HPO process is iterative. Recently, many approaches develop some elaborated mechanisms to allocate the HPO resources adaptively (Huang et al. 2019; Falkner, Klein, and Hutter 2018; Sabharwal, Samulowitz, and Tesauro 2016). In addition, multi-fidelity optimization has been deeply studied in the framework of BO to accelerate the HPO problem (Swersky, Snoek, and Adams 2013; Klein et al. 2017; Kandasamy et al. 2017; Poloczek, Wang, and Frazier 2017; Hu et al. 2019).

> [!note] 翻譯
> 我們首先介紹 CASH 問題的基本符號。共有 K 個候選演算法 A = {A1, ..., AK}，每個演算法 Ai 有其對應的超參數空間 Λi；帶有超參數 λ 的演算法 Ai 記為 Ai_λ。給定學習問題的資料集 D = {D_train, D_valid}，CASH 問題即是找出使損失指標（例如在 D_valid 上的驗證誤差）最小化的演算法與超參數聯合配置 A*_λ*：
>
> A*_λ* = argmin_{Ai∈A, λ∈Λi} L(Ai_λ, D).   (1)
>
> 超參數最佳化（HPO）則是為給定演算法 Ai 找出在驗證集上表現最佳的超參數配置 λ*：
>
> λ* = argmin_{λ∈Λi} L(Ai_λ, D).   (2)
>
> 貝葉斯最佳化（BO）已被成功應用於求解 HPO 問題。序貫模型式演算法組態（Sequential Model-based Algorithm Configuration, SMAC）(Hutter, Hoos, and Leyton-Brown 2011)、樹狀結構 Parzen 估計器（Tree-structure Parzen Estimator, TPE）(Bergstra et al. 2011) 與 Spearmint (Snoek, Larochelle, and Adams 2012) 是三種成熟的方法。值得注意的是，這些方法可以序貫方式執行，亦即 HPO 過程是迭代性的。近來，許多方法發展了精巧的機制以自適應分配 HPO 資源 (Huang et al. 2019; Falkner, Klein, and Hutter 2018; Sabharwal, Samulowitz, and Tesauro 2016)。此外，多保真度最佳化（multi-fidelity optimization）亦已在 BO 框架下被深入研究，以加速 HPO 問題 (Swersky, Snoek, and Adams 2013; Klein et al. 2017; Kandasamy et al. 2017; Poloczek, Wang, and Frazier 2017; Hu et al. 2019)。

---

> [!quote] Original
> In the algorithm selection problem, the objective is to choose a parameterized algorithm A*_λ*, which is the most effective with respect to a specified quality metric Q(.). This sub-problem can be stated as a minimization problem:
>
> A*_λ* = argmin_{i∈[1,...,K]} Q(Ai_λ*, D).   (3)
>
> In practice, all candidate algorithms with some fixed hyperparameters are evaluated on the validation dataset, and the algorithm with the best performance is chosen. However, this method suffers from the "low accuracy" issue due to the lack of the HPO: the fixed hyperparameters cannot accurately reflect the performance of the algorithm across different problems. Moreover, many methods select algorithms according to some theoretical decision rules, meta-learning methods (Abdulrhaman et al. 2015) and supervised learning techniques (Sun and Pfahringer 2013).
>
> To solve the CASH problem effectively in the ML applications, it is necessary to select the algorithm and its hyperparameters simultaneously. Auto-Weka is the first work devoted to the CASH problem, which takes the BO based solutions. Then Auto-Sklearn and Hyperopt-Sklearn also adopt the same BO based framework. In addition, tree-based pipeline optimization tool (TPOT) (Olson and Moore 2019) uses genetic programming to address the CASH problem. Recently, Reinforcement learning method (Efimova, Filchenkov, and Shalamov 2017) and MAB based methods (Liu et al. 2019) have been studied to solve the CASH problem. They model the rewards in the stationary environment and ignore the objective's difference between MABs and CASH. In the community of MAB, several works (Besbes, Gur, and Zeevi 2014; Jamieson and Talwalkar 2016; Heidari, Kearns, and Roth 2016; Levine, Crammer, and Mannor 2017) focus on the non-stationary bandits, but none of them match the settings in CASH.

> [!note] 翻譯
> 在演算法選擇問題中，目標是就特定品質指標 Q(.) 選出最有效的參數化演算法 A*_λ*。此子問題可表述為一個最小化問題：
>
> A*_λ* = argmin_{i∈[1,...,K]} Q(Ai_λ*, D).   (3)
>
> 實務上，所有候選演算法會以某些固定的超參數在驗證資料集上評估，並選出表現最佳的演算法。然而，由於缺乏 HPO，此方法存在「低準確率」的問題：固定的超參數無法準確反映演算法在不同問題上的表現。此外，許多方法依據某些理論決策規則、元學習方法 (Abdulrhaman et al. 2015) 或監督式學習技術 (Sun and Pfahringer 2013) 來選擇演算法。
>
> 為在 ML 應用中有效求解 CASH 問題，必須同時選擇演算法及其超參數。Auto-Weka 是首個致力於 CASH 問題的工作，採用基於 BO 的解法；隨後 Auto-Sklearn 與 Hyperopt-Sklearn 亦採用相同的 BO 框架。此外，基於樹的管線最佳化工具（tree-based pipeline optimization tool, TPOT）(Olson and Moore 2019) 使用遺傳規劃（genetic programming）處理 CASH 問題。近來，強化學習方法 (Efimova, Filchenkov, and Shalamov 2017) 與基於 MAB 的方法 (Liu et al. 2019) 也被用於研究 CASH 問題，但它們在平穩環境下建模報酬，並忽略了 MAB 與 CASH 之間目標的差異。在 MAB 社群中，多篇論文 (Besbes, Gur, and Zeevi 2014; Jamieson and Talwalkar 2016; Heidari, Kearns, and Roth 2016; Levine, Crammer, and Mannor 2017) 聚焦於非平穩賭博機，但皆與 CASH 的設定不相吻合。

---

## The Proposed Method | 所提方法

### The Alternating Optimization Framework | 交替最佳化框架

> [!quote] Original
> In this section, we introduce the alternating optimization framework, give the formulation of Rising Bandits, and describe the online algorithm to solve this bandit problem.
>
> We reformulate the CASH problem into the following bilevel optimization problem:
>
> min_{i∈[1,...,K]} Q(Ai_λ*, D)   s.t.   λ* = argmin_{λ∈Λi} L(Ai_λ, D).   (4)
>
> Here the CASH problem is decomposed into two kinds of sub-problems: algorithm selection problem (the upper-level sub-problem) and the HPO problem for each ML algorithm (the lower-level sub-problem). We propose to solve this bilevel optimization problem by optimizing the upper-level and lower-level sub-problems alternately. We name it the alternating optimization framework. In this framework, Bayesian Optimization (BO) methods are used to conduct HPO for each ML algorithm individually; MAB based method is utilized to solve the algorithm selection problem. This framework brings two benefits:
> - Since the hyperparameter space for each ML algorithm is relatively small, BO methods can solve the corresponding HPO problem efficiently.
> - The resources can be adaptively allocated to the HPO of each ML algorithm according to its HPO performance in the MAB framework.
>
> As a result, the poor-performing ML algorithms will be equipped with few HPO resources (e.g., the number of trials), and more resources are allocated to the promising algorithms that can achieve better learning performance.

> [!note] 翻譯
> 本節介紹交替最佳化框架，給出上升賭博機的形式化定義，並描述求解此賭博機問題的線上演算法。
>
> 我們將 CASH 問題重構為如下的雙層最佳化問題（bilevel optimization problem）：
>
> min_{i∈[1,...,K]} Q(Ai_λ*, D)   s.t.   λ* = argmin_{λ∈Λi} L(Ai_λ, D).   (4)
>
> 此處 CASH 問題被分解為兩類子問題：演算法選擇問題（上層子問題）與各 ML 演算法的 HPO 問題（下層子問題）。我們提出以交替最佳化上層與下層子問題的方式求解此雙層最佳化問題，並將其命名為交替最佳化框架。在此框架中，貝葉斯最佳化方法被用於對每個 ML 演算法個別進行 HPO；基於 MAB 的方法則用於求解演算法選擇問題。此框架帶來兩項益處：
> - 由於每個 ML 演算法的超參數空間相對較小，BO 方法能高效求解對應的 HPO 問題。
> - 在 MAB 框架下，資源可依各 ML 演算法的 HPO 表現自適應地分配至其 HPO 過程。
>
> 其結果是，表現不佳的 ML 演算法僅會獲得少量 HPO 資源（例如試驗次數），而更多資源被分配給能取得較佳學習表現、更具潛力的演算法。

---

### Non-stationary Rewards from Bayesian Optimization | 貝葉斯最佳化的非平穩報酬

> [!quote] Original
> Before introducing the Rising Bandits, we first investigate the rewards (HPO results) from BO methods. Given more HPO resources, the expected rewards (i.e., the best-observed validation accuracy) will increase. Figure 1 provides an intuitive example. Six ML algorithms are equipped with 200 trials to conduct HPO. The rewards r(.) correspond to the best-observed validation accuracy in each trial. As the number of HPO trial increases, this validation accuracy improves gradually, and then gets saturated. Further, we can summarize the following observations about the rewards from BO:
> - For each ML algorithm Ak, the reward sequence rk(1), ..., rk(n) is increasing and bounded, and the limit lim_{n→∞} rk(n) exists.
> - The reward sequence satisfies the decreasing marginal returns approximately. Here we abuse the terminology and refer to this as "concavity".
>
> Since the rewards increase monotonically across trials, it is evident that the rewards are not identically distributed, but are generated by a non-stationary stochastic process.
>
> [Figure 1: The HPO results of 6 ML algorithms. BO method – SMAC is used to tune the hyperparameters of each algorithm 50 times, and the average validation accuracy across trials is reported.]

> [!note] 翻譯
> 在介紹上升賭博機之前，我們先探究 BO 方法所產生的報酬（即 HPO 結果）。給予更多 HPO 資源時，期望報酬（即觀測到的最佳驗證準確率）將會提高。圖 1 提供了一個直觀的例子：六個 ML 演算法各獲得 200 次試驗以進行 HPO。報酬 r(.) 對應每次試驗中觀測到的最佳驗證準確率。隨著 HPO 試驗次數增加，此驗證準確率逐步改善，隨後趨於飽和。進一步地，我們可歸納出關於 BO 報酬的以下觀察：
> - 對每個 ML 演算法 Ak，報酬序列 rk(1), ..., rk(n) 遞增且有界，且極限 lim_{n→∞} rk(n) 存在。
> - 報酬序列近似滿足邊際報酬遞減。此處我們借用術語，稱之為「凹性」（concavity）。
>
> 由於報酬在試驗之間單調遞增，顯然這些報酬並非同分布，而是由一個非平穩隨機過程所產生。
>
> [圖 1：6 個 ML 演算法的 HPO 結果。使用 BO 方法 SMAC 對各演算法的超參數調校 50 次，並報告各試驗的平均驗證準確率。]

---

### The Definition of Rising Bandits | 上升賭博機的定義

> [!quote] Original
> Based on the observations about the HPO results, we give the formulation of Rising Bandits to model the algorithm selection problem with non-stationary rewards. In this bandit variant, the agent is given K arms, and at each time step t = 1, 2, ..., T one of the arm must be pulled. Each arm k is associated with the HPO process of an ML algorithm Ak. Pulling an arm means that a unit of resource (e.g., an HPO trial) is assigned to the HPO process of an algorithm, and the reward corresponds to the non-stationary HPO results.
>
> In Rising Bandits, we model the non-stationary reward sequences of the arms as follows: each arm k has a fixed underlying reward function denoted by rk(.). All the reward functions are bounded within [0, 1]. When the agent pulls arm k for the nth time, he receives an instantaneous reward rk(n). We denote the arm that is pulled at time step t as i(t) ∈ [K] = [1, ..., K]. Let Nk(t) be the number of pulls of arm k at time step t, not including this round's choice, that's, Nk(1) = 0, and Π the set of all sequences i(1), i(2), ..., where i(t) ∈ [K], ∀t ∈ N, i.e., π ∈ Π is a sequence of actions (arms), also referred to as a policy. We denote the arm that is chosen by policy π at time step t as π(t).

> [!note] 翻譯
> 基於對 HPO 結果的觀察，我們給出上升賭博機的形式化定義，以建模具有非平穩報酬的演算法選擇問題。在此賭博機變體中，代理人（agent）擁有 K 支臂，且在每個時間步 t = 1, 2, ..., T 必須拉動其中一支臂。每支臂 k 對應一個 ML 演算法 Ak 的 HPO 過程；拉動一支臂意味著將一單位資源（例如一次 HPO 試驗）分配給某演算法的 HPO 過程，而報酬對應非平穩的 HPO 結果。
>
> 在上升賭博機中，我們將各臂的非平穩報酬序列建模如下：每支臂 k 有一個固定的底層報酬函數，記為 rk(.)，所有報酬函數皆有界於 [0, 1] 之內。當代理人第 n 次拉動臂 k 時，會收到即時報酬 rk(n)。我們將時間步 t 所拉動的臂記為 i(t) ∈ [K] = [1, ..., K]。令 Nk(t) 為時間步 t 時臂 k 已被拉動的次數（不含本回合的選擇），即 Nk(1) = 0；並令 Π 為所有序列 i(1), i(2), ...（其中 i(t) ∈ [K]，∀t ∈ N）的集合，亦即 π ∈ Π 是一個行動（臂）序列，也稱為策略（policy）。我們將策略 π 在時間步 t 所選擇的臂記為 π(t)。

---

> [!quote] Original
> Instead of maximizing the accumulated rewards Σ_{t=1}^{T} r_{π(t)}(N_{π(t)}(t) + 1), the objective of the agent in CASH is to maximize the observed reward within T, defined for policy π ∈ Π by,
>
> J(T; π) = max_{t=1:T} r_{π(t)}(N_{π(t)}(t) + 1).   (5)
>
> We consider the equivalent objective of minimizing the regret within T defined by,
>
> R(T; π) = max_{π̃∈Π}{J(T; π̃)} − J(T; π).   (6)
>
> Based on the observations about the non-stationary rewards, we introduce the following assumption:
>
> **Assumption 1.** (Rising) ∀k ∈ [K], rk(n) is bounded, increasing, and concave in n.
>
> According to this assumption, the original objective in (5) is equivalent to,
>
> J(T; π) = max_k rk(Nk(T + 1)).   (7)
>
> In the CASH problem, it is clear that the reward function r(n) is bounded and increasing; but the concavity assumption may not always hold. We will discuss the two situations in the following sections. Then we investigate an offline solution for the Rising Bandits. The offline setting means that the optimal arm is known to the agent before the game. Let π_max be a policy defined by,
>
> π_max(t) ∈ argmax_{k∈[K]} rk(T).   (8)
>
> **Lemma 1.** π_max is the optimal policy for the Rising Bandits problem in the offline setting.
> Proof: See Appendix A.1 of the supplementary material.
>
> If the best arm is known to the agent, the optimal policy must pull the best arm repeatedly.

> [!note] 翻譯
> 與最大化累積報酬 Σ_{t=1}^{T} r_{π(t)}(N_{π(t)}(t) + 1) 不同，CASH 中代理人的目標是最大化 T 之內觀測到的報酬，對策略 π ∈ Π 定義為
>
> J(T; π) = max_{t=1:T} r_{π(t)}(N_{π(t)}(t) + 1).   (5)
>
> 我們考慮其等價目標，即最小化 T 之內的遺憾（regret），定義為
>
> R(T; π) = max_{π̃∈Π}{J(T; π̃)} − J(T; π).   (6)
>
> 基於對非平穩報酬的觀察，我們引入以下假設：
>
> **假設 1.**（上升性）∀k ∈ [K]，rk(n) 對 n 有界、遞增且為凹函數。
>
> 依此假設，式 (5) 中的原始目標等價於
>
> J(T; π) = max_k rk(Nk(T + 1)).   (7)
>
> 在 CASH 問題中，報酬函數 r(n) 顯然有界且遞增，但凹性假設未必總是成立；我們將在後續章節討論這兩種情況。接著我們探討上升賭博機的離線解法。離線設定意指代理人在遊戲開始前即已知最優臂。令 π_max 為如下定義的策略：
>
> π_max(t) ∈ argmax_{k∈[K]} rk(T).   (8)
>
> **引理 1.** 在離線設定下，π_max 是上升賭博機問題的最優策略。
> 證明：見補充材料附錄 A.1。
>
> 若代理人已知最佳臂，最優策略必然是反覆拉動該最佳臂。

---

### Online Solution for Rising Bandits | 上升賭博機的線上解法

> [!quote] Original
> The CASH problem falls into the online setting, where the best arm is unknown to the agent. In this circumstance, the above Lemma 1 fails. However, it guides us to derive an efficient policy in the online setting: 1) first identify the best arm by using as few time steps as possible, and then 2) pull the best arm until the time step T meets. That is, solving the best arm identification problem first and then fully exploiting the best arm can efficiently optimize the objective in (7).
>
> Based on the Assumption 1, we can obtain an interval that bounds the final reward of an arm. The reward function is concave, that's, for any n > 2, we have r(n) − r(n − 1) ≥ r(n + 1) − r(n). Suppose the arm k has been pulled n times, and n rewards rk(1), ..., rk(n) are observed. Given that rk(.) is increasing, bounded and concave, we have for any t > n,
>
> rk(t) ≤ min(rk(n) + (t − n)ωk(n), 1),   (9)
>
> where ωk(n) equals rk(n) − rk(n − 1), and we name ω(n) as the growth rate at the nth step. We refer to the right-hand side of Inequality 9 as the upper bound uk(t) of rk(t). Naturally, the lower bound lk(t) of rk(t) is rk(n). If the arm i has the lower bound li(t) that is no less than the upper bound uk(s) of the arm k, the arm k cannot be the optimal arm. By using this elimination criterion, we can gradually dismiss the arm that cannot be the optimal arm. After finding the best arm, this arm will be pulled repeatedly until the game ends.

> [!note] 翻譯
> CASH 問題屬於線上設定，即代理人並不知道最佳臂為何。在此情況下，上述引理 1 不再適用；但它引導我們在線上設定中導出一個高效策略：1) 先以盡可能少的時間步識別出最佳臂，接著 2) 反覆拉動最佳臂直到時間步 T 為止。也就是說，先解決最佳臂識別（best arm identification）問題，再充分利用最佳臂，便能高效最佳化式 (7) 的目標。
>
> 基於假設 1，我們可以求得一個界定某臂最終報酬的區間。報酬函數為凹，亦即對任意 n > 2，有 r(n) − r(n − 1) ≥ r(n + 1) − r(n)。假設臂 k 已被拉動 n 次，並觀測到 n 個報酬 rk(1), ..., rk(n)。由於 rk(.) 遞增、有界且為凹，對任意 t > n 有
>
> rk(t) ≤ min(rk(n) + (t − n)ωk(n), 1),   (9)
>
> 其中 ωk(n) 等於 rk(n) − rk(n − 1)，我們稱 ω(n) 為第 n 步的成長率（growth rate）。不等式 9 的右側即為 rk(t) 的上界 uk(t)；自然地，rk(t) 的下界 lk(t) 為 rk(n)。若臂 i 的下界 li(t) 不小於臂 k 的上界 uk(s)，則臂 k 不可能是最優臂。利用此淘汰準則（elimination criterion），我們可以逐步剔除不可能成為最優臂的臂。找到最佳臂之後，便反覆拉動該臂直到遊戲結束。

---

> [!quote] Original
> Algorithm 1 illustrates both the pseudo-code of the proposed online algorithm and its usage in the alternating optimization framework. It operates as follows: it maintains a set of candidate arms (ML algorithms) in which the best arm is guaranteed to lie (Line 1). At each round, it pulls all the arms in the candidate set once, and it means that each corresponding algorithm in the candidate set is given one unit of resource to tune its hyperparameters with BO methods. Then both the upper bound and lower bound of the final reward at time step T are updated (Line 5-10). If the upper bound of the final reward of an arm k (algorithm Ak) is less than or equal to the lower bound of another arm's in the candidate set, then the arm k will be eliminated from the candidate set (Line 11-15). The above process iterates until the time step T meets. Finally, the best algorithm along with the corresponding hyperparameter configuration is returned.
>
> Algorithm 1 Online algorithm for Rising Bandit
> Input: ML algorithm candidates A = {A1, ..., AK}, the total time steps T, and one unit of HPO resource b̂.
> 1: Initialize S_cand = {1, 2, ..., K}, t = 0.
> 2: while t < T do
> 3:   for each k ∈ S_cand do
> 4:     t = t + 1.
> 5:     Pull arm k once: Hk ← Iterate_HPO(Ak, b̂).
> 6:     Calculate ωk(t) according to Hk.
> 7:     Update u_k^t(T) = min(yk(t) + ωk(t)(T − t), 1).
> 8:     Update l_k^t(T) = yk(t).
> 9:   end for
> 10:  for i ≠ j ∈ S_cand do
> 11:    if l_i^t(T) ≥ u_j^t(T) then
> 12:      S_cand = S_cand \ {j}
> 13:    end if
> 14:  end for
> 15: end while
> 16: return the corresponding ML algorithm A* and its best hyperparameter configuration.

> [!note] 翻譯
> Algorithm 1 同時展示了所提線上演算法的虛擬碼及其在交替最佳化框架中的用法。其運作方式如下：演算法維護一個候選臂（ML 演算法）集合，保證最佳臂位於其中（第 1 行）。每一輪中，它將候選集合內的所有臂各拉動一次，意即候選集合中每個對應的演算法皆獲得一單位資源，以 BO 方法調校其超參數。接著更新時間步 T 時最終報酬的上界與下界（第 5–10 行）。若某臂 k（演算法 Ak）最終報酬的上界小於或等於候選集合中另一支臂的下界，則臂 k 將自候選集合中被淘汰（第 11–15 行）。上述過程迭代進行，直到時間步達到 T 為止。最終，回傳最佳演算法及其對應的超參數配置。
>
> Algorithm 1：上升賭博機的線上演算法
> 輸入：ML 演算法候選集 A = {A1, ..., AK}、總時間步數 T、一單位 HPO 資源 b̂。
> 1: 初始化 S_cand = {1, 2, ..., K}，t = 0。
> 2: while t < T do
> 3:   for 每個 k ∈ S_cand do
> 4:     t = t + 1。
> 5:     拉動臂 k 一次：Hk ← Iterate_HPO(Ak, b̂)。
> 6:     依據 Hk 計算 ωk(t)。
> 7:     更新 u_k^t(T) = min(yk(t) + ωk(t)(T − t), 1)。
> 8:     更新 l_k^t(T) = yk(t)。
> 9:   end for
> 10:  for i ≠ j ∈ S_cand do
> 11:    if l_i^t(T) ≥ u_j^t(T) then
> 12:      S_cand = S_cand \ {j}
> 13:    end if
> 14:  end for
> 15: end while
> 16: return 對應的 ML 演算法 A* 及其最佳超參數配置。

---

### Rising Bandits with "Loose" Concavity | 具「鬆弛」凹性的上升賭博機

> [!quote] Original
> As stated previously, the concavity in Assumption 1 may not always hold in the CASH problem. In this case, the problematic growth rate ωk(t) = rk(t) − rk(t − 1) may lead to a wrong upper bound. To alleviate this issue, we propose to use the following growth rate calculated by,
>
> ωk(t) = (yk(t) − yk(t − C)) / C ,   (10)
>
> where C is a constant. Intuitively, by averaging the latest C growth rates, this smooth growth rate is more robust to the case with "loose" concavity. In the next section, we provide theoretical guarantees for the proposed methods.

> [!note] 翻譯
> 如前所述，假設 1 中的凹性在 CASH 問題中未必總是成立。此時，有問題的成長率 ωk(t) = rk(t) − rk(t − 1) 可能導致錯誤的上界。為緩解此問題，我們提出使用如下計算的成長率：
>
> ωk(t) = (yk(t) − yk(t − C)) / C ,   (10)
>
> 其中 C 為常數。直觀而言，透過對最近 C 個成長率取平均，此平滑成長率（smooth growth rate）對「鬆弛」凹性的情形更為穩健。下一節中，我們為所提方法提供理論保證。

---

## Theoretical Analysis and Discussions | 理論分析與討論

> [!quote] Original
> For the coming theorem, we define a quantity that captures the time steps required to distinguish the optimal arm from the others. More precisely, we define γ(T) = max_k γk(T), where
>
> γk(T) = arg min_t {u_k^t(T) ≤ l_{k*_T}^t(T)}   (11)
>
> and k*_T is the optimal arm in the given T. Thus γk(T) specifies the smallest number of time steps needed to pull both arm k and the optimal arm so that the sub-optimal arms can be distinguished from the optimal arm.
>
> We prove that the upper bound of the policy regret of the proposed algorithm exists.
>
> **Theorem 1.** Suppose Assumption 1 holds. The proposed algorithm achieves regret bounded by,
>
> R(T; π̄) ≤ r_{k*_T}(T) − r_{k*_T}(T − (n − 1)γ(T)).   (12)
>
> Proof: See Appendix A.2 of the supplementary material.
>
> This bound contains a problem-dependent term γ(T). If identifying the optimal arm is easier, γ(T) will be smaller.

> [!note] 翻譯
> 為敘述接下來的定理，我們定義一個量化「區分最優臂與其他臂所需時間步數」的量。更精確地，我們定義 γ(T) = max_k γk(T)，其中
>
> γk(T) = arg min_t {u_k^t(T) ≤ l_{k*_T}^t(T)}   (11)
>
> 且 k*_T 為給定 T 下的最優臂。因此，γk(T) 給出了同時拉動臂 k 與最優臂、使次優臂得以與最優臂區分開來所需的最少時間步數。
>
> 我們證明所提演算法的策略遺憾（policy regret）存在上界。
>
> **定理 1.** 假設假設 1 成立，則所提演算法的遺憾有如下上界：
>
> R(T; π̄) ≤ r_{k*_T}(T) − r_{k*_T}(T − (n − 1)γ(T)).   (12)
>
> 證明：見補充材料附錄 A.2。
>
> 此上界包含一個依問題而定的項 γ(T)：若識別最優臂較為容易，γ(T) 將較小。

---

### Compare with Average Policy | 與平均策略之比較

> [!quote] Original
> An intuitive policy π_uni is to pull each arm T/K times. The regret of this policy is,
>
> R(T; π_uni) = r_{k*_T}(T) − max_k rk(T/K).   (13)
>
> We now establish the regret connection between the proposed algorithm and the average policy.
>
> **Corollary 1.** When the problem-dependent term γ(T) satisfies: γ(T) ≤ (KT − T)/(K(K − 1)), the regret of the proposed algorithm will not be worse than the average strategy's.
>
> R(T; π̄) ≤ R(T; π_uni).   (14)
>
> Proof: See Appendix A.3 of the supplementary material.

> [!note] 翻譯
> 一個直觀的策略 π_uni 是將每支臂各拉動 T/K 次。此策略的遺憾為
>
> R(T; π_uni) = r_{k*_T}(T) − max_k rk(T/K).   (13)
>
> 我們現在建立所提演算法與平均策略之間的遺憾關係。
>
> **推論 1.** 當依問題而定的項 γ(T) 滿足 γ(T) ≤ (KT − T)/(K(K − 1)) 時，所提演算法的遺憾不會劣於平均策略：
>
> R(T; π̄) ≤ R(T; π_uni).   (14)
>
> 證明：見補充材料附錄 A.3。

---

### Theoretical Guarantee for "Loose" Concavity | 「鬆弛」凹性的理論保證

> [!quote] Original
> Here we provide a theoretical guarantee for the smooth growth rate. For any reward sequence yk(1), ..., we can find a reward function rk(.) that satisfies the Assumption 1. At each time step t, rk(t) ≥ yk(t), and they have the same limit. We denote the bias between rk(.) and yk(.) by ∆k(t) = rk(t) − yk(t).
>
> **Theorem 2.** If the following condition holds, the proposed algorithm with smooth growth rate can be used to identify the optimal arm without any loss,
>
> ∆k(t) / ∆k(t − C) ≤ (T − t) / (T − t + C).   (15)
>
> Proof: See Appendix A.4 of the supplementary material.

> [!note] 翻譯
> 此處我們為平滑成長率提供理論保證。對於任意報酬序列 yk(1), ...，我們都可以找到一個滿足假設 1 的報酬函數 rk(.)，使得在每個時間步 t 皆有 rk(t) ≥ yk(t)，且兩者具有相同的極限。我們以 ∆k(t) = rk(t) − yk(t) 表示 rk(.) 與 yk(.) 之間的偏差。
>
> **定理 2.** 若以下條件成立，則採用平滑成長率的所提演算法可在無任何損失的情況下識別最優臂：
>
> ∆k(t) / ∆k(t − C) ≤ (T − t) / (T − t + C).   (15)
>
> 證明：見補充材料附錄 A.4。

---

### Towards Cost-Aware CASH | 邁向成本感知的 CASH

> [!quote] Original
> In the previous sections, the limited resource is the number of HPO trials, and here we consider the time B as the limited resource. Both the algorithm's performance and its HPO evaluation cost in runtime should be taken into consideration. In CASH, conducting an HPO trial for different ML algorithms usually takes a different time cost. For example, for large datasets, training linear models is much faster than the tree-based model such as gradient boosting. To solve the cost-aware CASH, we develop a variant of Algorithm 1. For each ML algorithm, we first compute its average time overhead ck in each HPO trial; then we predict the upper bound of the final reward within the given time B by,
>
> u_k^t(B) = rk(t) + ωk · B′/ck ,   (16)
>
> where B′ is the time left, and ωk is the growth rate at time t.

> [!note] 翻譯
> 前述各節中，受限的資源是 HPO 試驗次數；此處我們改以時間 B 作為受限資源。演算法的效能與其 HPO 評估的執行時間成本皆應納入考量。在 CASH 中，對不同 ML 演算法進行一次 HPO 試驗所需的時間成本通常不同：例如在大型資料集上，訓練線性模型遠比梯度提升（gradient boosting）等基於樹的模型快得多。為求解成本感知（cost-aware）的 CASH，我們發展了 Algorithm 1 的一個變體。對每個 ML 演算法，我們先計算其每次 HPO 試驗的平均時間開銷 ck；接著以下式預測給定時間 B 內最終報酬的上界：
>
> u_k^t(B) = rk(t) + ωk · B′/ck ,   (16)
>
> 其中 B′ 為剩餘時間，ωk 為時間 t 時的成長率。

---

### Relationship and Comparison with Previous Works | 與先前研究之關聯與比較

> [!quote] Original
> Our approach takes an adaptive resource allocation scheme. From a theoretical perspective, our method is similar, in spirit, to some previous works (Huang et al. 2019; Falkner, Klein, and Hutter 2018; Sabharwal, Samulowitz, and Tesauro 2016). In addition, one work (Heidari, Kearns, and Roth 2016) also supports concave reward functions. Compared with these works, our main contribution is to apply a similar methodology to a new application, i.e., CASH. In the CASH problem, we find some additional structures that we can use, e.g., CASH has the concave structure in the reward function. Furthermore, instead of optimizing the accumulated regrets in Heidari, Kearns, and Roth, CASH focuses more on identifying the best arm. These additional structures allow us to perform significantly better over simply applying these previous approaches to CASH.
>
> Compared with BO based solutions, our method explicitly reduces the hyperparameter space in the CASH problem by dismissing the poor algorithms successively. Without any modification, this method can also be used to solve the regression tasks by mapping the loss into [0, 1]. In addition, the proposed approach can handle the cost-aware CASH; however, the existing solutions for the CASH problem do not take the evaluation cost into consideration.

> [!note] 翻譯
> 我們的方法採取自適應的資源分配方案。從理論觀點看，我們的方法在精神上與若干先前工作相似 (Huang et al. 2019; Falkner, Klein, and Hutter 2018; Sabharwal, Samulowitz, and Tesauro 2016)；此外，另一項工作 (Heidari, Kearns, and Roth 2016) 亦支援凹報酬函數。與這些工作相比，我們的主要貢獻在於將類似的方法論應用於一個新的應用場景，即 CASH。在 CASH 問題中，我們發現了一些可資利用的額外結構，例如 CASH 的報酬函數具有凹性結構。再者，不同於 Heidari, Kearns, and Roth 最佳化累積遺憾，CASH 更著重於識別最佳臂。這些額外結構使我們的表現顯著優於將先前方法直接套用於 CASH。
>
> 與基於 BO 的解法相比，我們的方法藉由逐步剔除表現不佳的演算法，顯式地縮減了 CASH 問題中的超參數空間。無需任何修改，只要將損失映射至 [0, 1]，此方法亦可用於求解迴歸任務。此外，所提方法能處理成本感知的 CASH，而既有的 CASH 解法皆未將評估成本納入考量。

---

## Experiments and Results | 實驗與結果

> [!quote] Original
> In the evaluation of the proposed method, we demonstrate its superiority from the following three perspectives: 1) the efficiency compared with the state-of-the-art BO based solutions, 2) the empirical performance compared with all considered baselines in terms of final accuracy and efficiency, and 3) practicability and effectiveness in the AutoML systems.
>
> We compared our method with the following baselines, including the BO based methods and the traditional bandit based methods in the MAB community:
> - **AVG** The average policy that allocates the same HPO resources to each ML algorithm.
> - **SMAC** BO based method that uses a modified random forest as the surrogate model to conduct BO.
> - **TPE** BO based method that utilizes the tree-structured Parzen density estimators as the surrogate model.
> - **CMAB** Bandit based method that models the stationary reward and maximizes the accumulated rewards with Thompson sampling (Russo et al. 2018; Liu et al. 2019).
> - **UCB** UCB policy is used to solve the traditional MAB.
> - **Softmax** Softmax policy (Sutton and Barto 2018) is leveraged to solve the traditional MAB.
> - **BOHB** This method takes an adaptive strategy to conduct HPO (Falkner, Klein, and Hutter 2018).
>
> In addition, to investigate its practicability and the empirical performance in the AutoML systems, we also take the following AutoML systems into account:
> - **Auto-Sklearn (ASK)** The state-of-the-art AutoML system. It utilizes the BO based solution – SMAC to solve the CASH problem.
> - **Hyperopt-Sklearn (HPSK)** Similar to Auto-Sklearn, it also adopts the BO based solution, and it uses TPE to conduct HPO instead.
> - **TPOT** It leverages the genetic algorithm to solve CASH.

> [!note] 翻譯
> 在對所提方法的評估中，我們從以下三個面向展示其優越性：1) 相較於最先進基於 BO 之解法的效率；2) 就最終準確率與效率而言，相較於所有納入考量之基線的實證表現；3) 在 AutoML 系統中的實用性與有效性。
>
> 我們將所提方法與下列基線進行比較，包括基於 BO 的方法與 MAB 社群中傳統的賭博機方法：
> - **AVG** 平均策略，對每個 ML 演算法分配相同的 HPO 資源。
> - **SMAC** 基於 BO 的方法，使用改良的隨機森林作為代理模型進行 BO。
> - **TPE** 基於 BO 的方法，利用樹狀結構 Parzen 密度估計器作為代理模型。
> - **CMAB** 基於賭博機的方法，於平穩環境建模報酬並以 Thompson 抽樣最大化累積報酬 (Russo et al. 2018; Liu et al. 2019)。
> - **UCB** 使用 UCB 策略求解傳統 MAB。
> - **Softmax** 使用 Softmax 策略 (Sutton and Barto 2018) 求解傳統 MAB。
> - **BOHB** 採取自適應策略進行 HPO 的方法 (Falkner, Klein, and Hutter 2018)。
>
> 此外，為探究其在 AutoML 系統中的實用性與實證表現，我們亦納入以下 AutoML 系統：
> - **Auto-Sklearn (ASK)** 最先進的 AutoML 系統，利用基於 BO 的解法 SMAC 求解 CASH 問題。
> - **Hyperopt-Sklearn (HPSK)** 與 Auto-Sklearn 類似，亦採用基於 BO 的解法，但改以 TPE 進行 HPO。
> - **TPOT** 利用遺傳演算法求解 CASH。

---

### CASH Space, Datasets and Basic Settings | CASH 空間、資料集與基本設定

> [!quote] Original
> In all experiments, the optimization space of the CASH problem is the same as the one in Auto-Sklearn. It comprises 16 ML classification algorithms with 78 hyperparameters. More details about the space can be found in Appendix B of the supplementary materials. We considered 30 classification datasets from the OpenML repositories. These datasets are widely used in the related works (Feurer et al. 2015; Efimova, Filchenkov, and Shalamov 2017; Olson and Moore 2019; Liu et al. 2019), and the details are listed in Appendix C. For each run, the original dataset will be partitioned into three subsets: training set, validation set and test set, in the proportion of 64%, 16%, 20%. Accuracy is used as the metric of the objective. We repeated each method 10 times on each dataset and reported the average accuracy. For the sake of fairness, we assured that all compared methods use the data with the same preprocessing operations. That is, we processed the raw datasets with the necessary operations only (e.g., label encoder, one-hot encoding); and we disabled the original preprocessing module in Auto-Sklearn and TPOT. Like Auto-Sklearn and Auto-Weka, the proposed method leverages SMAC to solve the HPO problem for each ML algorithm individually. In the following experiments, we used the initial version of our method (in Algorithm 1) by default (except when specified the concrete version). The parameter C for computing the smooth growth rate is set to 7. Our method is not sensitive to the choice of C, and the detailed sensitivity analysis can be found in Appendix D.
>
> **More Results about the Concave Rewards** We ran experiments on 5 datasets, and analyzed the reward functions for different ML algorithms. Ten figures in the supplementary materials illustrate the rewards functions for each algorithm in details. We found that the concave behavior about the reward function is largely consistent with the result we showed in Figure 1.

> [!note] 翻譯
> 所有實驗中，CASH 問題的最佳化空間與 Auto-Sklearn 相同，包含 16 個 ML 分類演算法與 78 個超參數；關於此空間的更多細節見補充材料附錄 B。我們選用 OpenML 資料庫中的 30 個分類資料集，這些資料集在相關研究中被廣泛使用 (Feurer et al. 2015; Efimova, Filchenkov, and Shalamov 2017; Olson and Moore 2019; Liu et al. 2019)，細節列於附錄 C。每次執行時，原始資料集會被切分為三個子集：訓練集、驗證集與測試集，比例為 64%、16%、20%。以準確率作為目標指標。我們對每個資料集將每個方法重複執行 10 次並報告平均準確率。為求公平，我們確保所有比較方法皆使用經過相同前處理操作的資料：亦即僅以必要操作處理原始資料集（例如標籤編碼、one-hot 編碼），並停用 Auto-Sklearn 與 TPOT 內建的前處理模組。與 Auto-Sklearn 及 Auto-Weka 相同，所提方法利用 SMAC 對每個 ML 演算法個別求解 HPO 問題。以下實驗中，除非特別指明具體版本，我們預設使用方法的初始版本（Algorithm 1）。計算平滑成長率的參數 C 設為 7；我們的方法對 C 的選擇並不敏感，詳細的敏感度分析見附錄 D。
>
> **關於凹性報酬的更多結果** 我們在 5 個資料集上進行實驗，並分析了不同 ML 演算法的報酬函數。補充材料中的十張圖詳細展示了各演算法的報酬函數。我們發現報酬函數的凹性行為與圖 1 所示結果大體一致。

---

### Comparison with BO based Methods | 與基於 BO 方法之比較

> [!quote] Original
> The empirical evaluation of BO methods shows that SMAC performs best on the benchmarks with the high-dimensional hyperparameter space, closely followed by TPE. In this experiment, we evaluated the performance of both the proposed method and SMAC on the CASH problem.
>
> **High-dimensional Hyperparameter Space.** Here we demonstrated that the proposed method still works well when the hyperparameter space becomes large. We evaluated the following three methods on OpenML PC4 dataset with 500 trials: average policy (AVG), SMAC and our approach (OURS). The hyperparameter space of CASH problem is gradually augmented by adding more and more ML algorithms into the algorithm candidate A with |A| = K. The performance of each method is tested with different Ks: K = [1, 2, 4, 8, 12, 16]. When K equals to 1, the hyperparameter space only includes the hyperparameters of the optimal algorithm; if K is set to 16, the hyperparameter space contains the hyperparameters of all ML algorithms and the algorithm selection hyperparameter. As illustrated in Table 1, SMAC suffers from the low-efficiency issue. With the increase of K, it is infeasible for BO methods to learn a surrogate model that models this huge optimization space accurately within 500 trials. Consequently, the validation accuracy drops from 95.02% to 93.63%. In contrast, the proposed method utilizes the elimination criterion to dismiss the poor-performing algorithms from the candidate set, thus decreasing the dimension of hyperparameter space automatically. Hence our method still can achieve the best accuracy - 95.02% when K equals to 16.
>
> [Table 1: The validation accuracy (%) with different Ks in the CASH problem.]

> [!note] 翻譯
> 對 BO 方法的實證評估顯示，SMAC 在具有高維超參數空間的基準測試上表現最佳，TPE 緊隨其後。本實驗中，我們評估了所提方法與 SMAC 在 CASH 問題上的表現。
>
> **高維超參數空間。** 此處我們展示當超參數空間變大時，所提方法仍能良好運作。我們在 OpenML PC4 資料集上以 500 次試驗評估以下三種方法：平均策略（AVG）、SMAC 與我們的方法（OURS）。CASH 問題的超參數空間透過向候選演算法集 A（|A| = K）逐步加入更多 ML 演算法而漸次擴大；各方法在不同的 K 值下測試：K = [1, 2, 4, 8, 12, 16]。當 K 等於 1 時，超參數空間僅包含最優演算法的超參數；當 K 設為 16 時，超參數空間包含所有 ML 演算法的超參數以及演算法選擇超參數。如表 1 所示，SMAC 存在效率低落的問題：隨著 K 增加，BO 方法無法在 500 次試驗內學得能準確建模如此龐大最佳化空間的代理模型，其驗證準確率遂從 95.02% 降至 93.63%。相對地，所提方法利用淘汰準則自候選集中剔除表現不佳的演算法，從而自動降低超參數空間的維度，因此即使 K 等於 16，我們的方法仍能達到最佳準確率 95.02%。
>
> [表 1：CASH 問題在不同 K 值下的驗證準確率（%）。]

---

> [!quote] Original
> **Resource Allocation** Figure 2 (a) depicts the validation accuracy of three methods across trials, where 500 trials are used to solve the CASH problem with K = 16. In the first 100 trials, SMAC and the proposed method behave similarly, and both of them explore the performance distribution over the optimization space. Then our method starts to identify and dismiss the poor-performing algorithms by leveraging the known HPO results. More resources (trials) are allocated to the more promising algorithms, and this procedure brings significant performance improvement. Due to the huge hyperparameter space, SMAC cannot model the performance for each ML algorithm effectively. Therefore, its performance improves very slowly, and it is even worse than the average policy. To further compare our method with SMAC, Figure 2 (b) illustrates their percentages of the HPO trials for each ML algorithm respectively. In this problem (dataset), Adaboost is the optimal algorithm. As can be seen, our method identifies and terminates 13 unpromising ML algorithms by using 20% trials. Another 30% of trials are used to dismiss the left two algorithms that have a near-optimal performance. Almost 50% of trials are spent on tuning the optimal algorithm – Adaboost. In contrast, most of the trials in SMAC are used to tune the poor-performing algorithms.
>
> [Figure 2: Performance comparison between BO based solutions and the proposed method on PC4 dataset.]

> [!note] 翻譯
> **資源分配** 圖 2 (a) 描繪了三種方法在各試驗中的驗證準確率，其中以 500 次試驗求解 K = 16 的 CASH 問題。在前 100 次試驗中，SMAC 與所提方法表現相似，兩者皆在最佳化空間上探索效能分布；隨後我們的方法開始利用已知的 HPO 結果識別並剔除表現不佳的演算法，將更多資源（試驗）分配給更具潛力的演算法，此程序帶來顯著的效能提升。由於超參數空間過於龐大，SMAC 無法有效建模每個 ML 演算法的效能，因此其效能提升極為緩慢，甚至劣於平均策略。為進一步比較我們的方法與 SMAC，圖 2 (b) 分別展示了兩者對各 ML 演算法的 HPO 試驗百分比。在此問題（資料集）中，Adaboost 是最優演算法。可以看到，我們的方法僅用 20% 的試驗即識別並終止了 13 個無望的 ML 演算法；另有 30% 的試驗用於剔除剩餘兩個具有近乎最優表現的演算法；將近 50% 的試驗花費在調校最優演算法 Adaboost 上。相對地，SMAC 的大多數試驗被用於調校表現不佳的演算法。
>
> [圖 2：基於 BO 之解法與所提方法在 PC4 資料集上的效能比較。]

---

> [!quote] Original
> **Speedups** We evaluated the achievable speedups of our method against the baseline - SMAC on 10 OpenML datasets. Continued with the previous settings, 5000 trials in total are given to SMAC. The speedup is measured with the number of trials (#) that each method needs to reach the same validation accuracy (%). Table 3 depicts the speedup results. As can be seen, our method is more efficient than SMAC in terms of the number of trials one needs to reach the same validation accuracy. To derive a more clear illustration about this, we plotted the validation accuracy curve of these two methods across trials on the PC4 dataset. As shown in Figure 2 (c), the final validation accuracy of SMAC is still worse than the one that our approach achieves within 250 trials. The empirical results demonstrate that the proposed method can outperform the existing CASH algorithm - SMAC by over an order of magnitude speedups.
>
> [Table 3: Speedup results on 10 OpenML datasets. Average speedup: 12.6x.]

> [!note] 翻譯
> **加速比** 我們在 10 個 OpenML 資料集上評估了所提方法相對基線 SMAC 可達成的加速比。延續先前的設定，SMAC 共獲得 5000 次試驗。加速比以各方法達到相同驗證準確率（%）所需的試驗次數（#）來衡量。表 3 展示了加速結果。可以看到，就達到相同驗證準確率所需的試驗次數而言，我們的方法比 SMAC 更有效率。為更清楚地說明這一點，我們繪製了兩種方法在 PC4 資料集上跨試驗的驗證準確率曲線。如圖 2 (c) 所示，SMAC 的最終驗證準確率仍不及我們的方法在 250 次試驗內所達到的水準。實證結果顯示，所提方法能以超過一個數量級的加速勝過既有的 CASH 演算法 SMAC。
>
> [表 3：10 個 OpenML 資料集上的加速結果。平均加速比：12.6 倍。]

---

### Comparison with All Considered Methods | 與所有納入考量方法之比較

> [!quote] Original
> In this experiment, we compared the proposed method with all considered baselines in terms of two perspectives: 1) final accuracy, and 2) the efficiency, i.e., the number of trials one needs to reach the same validation accuracy. In the first part, each method is given 500 trials, and the average accuracy across 10 runs is reported. Table 2 lists both the average validation accuracy and the average test accuracy on 30 OpenML datasets. In order to evaluate the generalization of the corresponding model, we also compared the accuracy on the test set. As can be seen, the proposed method achieves the best validation accuracy on 26 out of 30 datasets, and it also reaches the highest test accuracy on 20 out of 30 datasets. This gives that the ML models obtained by our method generalize well. Although our method does not get the highest accuracy on a few datasets, its result is very close to the best one. It is worth noting that, on most datasets, our method outperforms both the existing bandit-based methods (CMAB, UCB, and Softmax) and BO-based methods in terms of the final accuracy in solving the CASH problem.
>
> [Table 2: Average validation accuracy and test accuracy for all considered methods on 30 OpenML datasets.]

> [!note] 翻譯
> 本實驗中，我們從兩個面向將所提方法與所有納入考量的基線進行比較：1) 最終準確率；2) 效率，即達到相同驗證準確率所需的試驗次數。第一部分中，每個方法獲得 500 次試驗，並報告 10 次執行的平均準確率。表 2 列出了 30 個 OpenML 資料集上的平均驗證準確率與平均測試準確率。為評估對應模型的泛化能力，我們亦比較了測試集上的準確率。可以看到，所提方法在 30 個資料集中的 26 個上取得最佳驗證準確率，並在 30 個中的 20 個上達到最高測試準確率，顯示以我們的方法取得的 ML 模型具有良好的泛化能力。儘管我們的方法在少數資料集上未能取得最高準確率，其結果與最佳者非常接近。值得注意的是，在大多數資料集上，就求解 CASH 問題的最終準確率而言，我們的方法同時勝過既有的賭博機方法（CMAB、UCB、Softmax）與基於 BO 的方法。
>
> [表 2：所有納入考量方法在 30 個 OpenML 資料集上的平均驗證準確率與測試準確率。]

---

> [!quote] Original
> In the second part, we took another two related works into consideration: Heidari et al. (Heidari, Kearns, and Roth 2016) and BOHB (Falkner, Klein, and Hutter 2018). First we ran these two methods on 10 datasets with 500 trials, and the result is reported in Table 4. Although Heidari et al. (2016) leverage the concave reward function, this method cannot outperform the solution found by our approach because it tries to maximize the accumulated rewards. As mentioned previously, the objective in CASH focuses more on identifying the optimal arm, instead of optimizing the accumulated rewards. Similar to our approach, BOHB adopts an adaptive mechanism to conduct hyperparameter optimization. The reason why this method cannot beat our method is that it does not use the structure information about the concave rewards in CASH. By contrast, our method, with the Rising Bandits, absorbs the advantages of these two kinds of methods, and avoids their drawbacks successfully. Furthermore, similar to the last section about speedups, we gave the baseline - BOHB enough trials, enabling it to reach the same validation accuracy that our method gets within 500 trials (that is, the fourth column in Table 4). Finally, we obtained the speedups against BOHB, and illustrated the result in Table 4. It exhibits that the CASH-oriented Rising Bandits are more efficient than the existing adaptive method in solving the CASH problem.
>
> [Table 4: Average validation accuracy (%) and speedups compared with the considered methods.]

> [!note] 翻譯
> 第二部分中，我們納入另外兩項相關工作：Heidari et al. (Heidari, Kearns, and Roth 2016) 與 BOHB (Falkner, Klein, and Hutter 2018)。我們先在 10 個資料集上以 500 次試驗執行這兩種方法，結果報告於表 4。儘管 Heidari et al. (2016) 利用了凹報酬函數，此方法仍無法勝過我們方法所找到的解，因為它試圖最大化累積報酬；如前所述，CASH 的目標更著重於識別最優臂，而非最佳化累積報酬。與我們的方法類似，BOHB 採用自適應機制進行超參數最佳化，其無法擊敗我們方法的原因在於它未利用 CASH 中凹性報酬的結構資訊。相對地，我們的方法藉由上升賭博機吸收了這兩類方法的優點，並成功避免其缺點。此外，與前一節的加速比實驗類似，我們給予基線 BOHB 足夠的試驗次數，使其達到我們的方法在 500 次試驗內取得的相同驗證準確率（即表 4 第四欄），最終得到相對 BOHB 的加速比，結果展示於表 4。這顯示面向 CASH 的上升賭博機在求解 CASH 問題上比既有的自適應方法更有效率。
>
> [表 4：與納入考量方法比較的平均驗證準確率（%）與加速比。]

---

### Comparison with AutoML Systems | 與 AutoML 系統之比較

> [!quote] Original
> To investigate the practicality and effectiveness of our method in the AutoML systems, we implemented the proposed method based on the components of Auto-Sklearn and compared it with three popular AutoML systems. Each system is given 2 hours, and the average test accuracy across 10 runs is reported. The cost-aware variant of our method is used to solve the CASH problems. Because the three AutoML systems do not take the evaluation cost into account, they only optimize the performance, instead of optimizing both efficiency and performance together. As a result, given a limited time, these AutoML systems suffer from the low-efficiency problem. The empirical results in Table 5 demonstrate that the proposed method is more efficient than the existing AutoML systems on the 12 OpenML datasets.
>
> [Table 5: Average test accuracy (%) of compared AutoML systems on 12 OpenML datasets.]

> [!note] 翻譯
> 為探究我們的方法在 AutoML 系統中的實用性與有效性，我們基於 Auto-Sklearn 的組件實作了所提方法，並與三個流行的 AutoML 系統進行比較。每個系統獲得 2 小時的執行時間，並報告 10 次執行的平均測試準確率。此處使用我們方法的成本感知變體來求解 CASH 問題。由於這三個 AutoML 系統皆未將評估成本納入考量，它們僅最佳化效能，而非同時最佳化效率與效能；因此在有限時間下，這些 AutoML 系統存在效率低落的問題。表 5 的實證結果顯示，在 12 個 OpenML 資料集上，所提方法比既有的 AutoML 系統更有效率。
>
> [表 5：各 AutoML 系統在 12 個 OpenML 資料集上的平均測試準確率（%）。]

---

## Conclusion | 結論

> [!quote] Original
> In this paper, we proposed an alternating optimization framework to accelerate the CASH problem, where a novel MAB variant is introduced to conduct algorithm selection and the Bayesian optimization methods are used to conduct HPO for each ML algorithm individually. Moreover, we presented an online algorithm to solve the Rising Bandits problem with provably theoretical guarantees. We evaluated the performance of the proposed method on a number of AutoML tasks and demonstrated its superiority over the competitive baselines. In the future work, we plan to leverage the meta-learning techniques to speed up the CASH problem.

> [!note] 翻譯
> 本文提出一個交替最佳化框架以加速 CASH 問題的求解，其中引入一種新穎的 MAB 變體進行演算法選擇，並以貝葉斯最佳化方法對每個 ML 演算法個別進行 HPO。此外，我們提出一個求解上升賭博機問題的線上演算法，並具有可證明的理論保證。我們在多項 AutoML 任務上評估了所提方法的效能，證明其優於各競爭基線。在未來的工作中，我們計畫利用元學習（meta-learning）技術進一步加速 CASH 問題。

---

## Acknowledgments | 致謝

> [!quote] Original
> This work is supported by the National Key Research and Development Program of China (No.2018YFB1004403), NSFC (No.61832001, 61702015, 61702016, 61572039), Beijing Academy of Artificial Intelligence (BAAI), and Alibaba-PKU joint program. Jiawei Jiang is the corresponding author.

> [!note] 翻譯
> 本研究由中國國家重點研發計畫（No.2018YFB1004403）、國家自然科學基金（NSFC，No.61832001、61702015、61702016、61572039）、北京智源人工智能研究院（BAAI）以及阿里巴巴—北京大學聯合計畫支持。Jiawei Jiang 為通訊作者。

---

## References | 參考文獻

> [!note] 翻譯
> References omitted / 參考文獻略。
