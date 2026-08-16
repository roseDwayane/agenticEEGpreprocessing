---
citation_key: "FeurerEtAl2015"
title: "Initializing Bayesian Hyperparameter Optimization via Meta-Learning"
authors: "Matthias Feurer; Jost Tobias Springenberg; Frank Hutter"
year: 2015
doi: "10.1609/aaai.v29i1.9354"
source: "AAAI OJS"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
conversion: "pdftotext -layout (automated)"
language: "bilingual-en-zh"
---

# Initializing Bayesian Hyperparameter Optimization via Meta-Learning | 以元學習初始化貝葉斯超參數最佳化

> [!abstract] 重點摘要
> - 提出 MI-SMBO：利用元學習（meta-learning）建議的超參數配置來初始化（暖啟動，warm-starting）序列式模型貝葉斯最佳化（Sequential Model-based Bayesian Optimization, SMBO），模仿人類專家「從相似資料集上表現良好的配置開始搜尋」的策略。
> - 方法完全不需修改底層 SMBO 演算法，只要最佳化器接受初始設計（initial design）或效能資料清單即可套用，因此可直接用於 SMAC、Spearmint、TPE 等現成工具。
> - 以 46 個元特徵（metafeatures）定義資料集間距離，比較兩種距離：元特徵差之 p-norm（dp）與以超參數效能排名之 Spearman 相關為基礎、再以隨機森林迴歸近似的學習式距離（dc）。
> - 在 57 個 OpenML 分類資料集上實驗：對 scikit-learn 的 CASH（Combined Algorithm Selection and Hyperparameter optimization）問題（10 個超參數、1623 個配置），MI-SMAC 顯著優於 SMAC、TPE 與隨機搜尋，其改進幅度甚至大於 SMAC 相對隨機搜尋的改進。
> - 對低維 SVM（2 個超參數）問題，MI-Spearmint 在小型評估預算下帶來溫和改進，約 10 次評估後 Spearmint 即趕上。
> - 結論：元學習初始化在大型配置空間中特別有效，且即使經過 50 次函數評估，未使用元學習的 SMBO 仍未完全追上 MI-SMBO。

---

## Abstract | 摘要

> [!quote] Original
> Model selection and hyperparameter optimization is crucial in applying machine learning to a novel dataset. Recently, a sub-community of machine learning has focused on solving this problem with Sequential Model-based Bayesian Optimization (SMBO), demonstrating substantial successes in many applications. However, for computationally expensive algorithms the overhead of hyperparameter optimization can still be prohibitive. In this paper we mimic a strategy human domain experts use: speed up optimization by starting from promising configurations that performed well on similar datasets. The resulting initialization technique integrates naturally into the generic SMBO framework and can be trivially applied to any SMBO method. To validate our approach, we perform extensive experiments with two established SMBO frameworks (Spearmint and SMAC) with complementary strengths; optimizing two machine learning frameworks on 57 datasets. Our initialization procedure yields mild improvements for low-dimensional hyperparameter optimization and substantially improves the state of the art for the more complex combined algorithm selection and hyperparameter optimization problem.

> [!note] 翻譯
> 在將機器學習應用於新資料集時，模型選擇（model selection）與超參數最佳化（hyperparameter optimization）至關重要。近年來，機器學習領域的一個子社群專注於以序列式模型貝葉斯最佳化（Sequential Model-based Bayesian Optimization, SMBO）解決此問題，並在許多應用中取得可觀的成功。然而，對於計算成本高昂的演算法而言，超參數最佳化的額外開銷仍可能令人卻步。本文模仿人類領域專家常用的策略：從曾在相似資料集上表現良好的有潛力配置（configuration）出發，以加速最佳化。由此產生的初始化技術可自然地整合進通用的 SMBO 框架，並可輕易套用於任何 SMBO 方法。為驗證本方法，我們以兩個具互補優勢的成熟 SMBO 框架（Spearmint 與 SMAC）進行了大量實驗，在 57 個資料集上最佳化兩種機器學習框架。我們的初始化程序對低維超參數最佳化帶來溫和的改進，並在更複雜的「演算法選擇與超參數聯合最佳化」（combined algorithm selection and hyperparameter optimization, CASH）問題上大幅超越了現有最先進水準。

---

## Introduction | 引言

> [!quote] Original
> Hyperparameter optimization is a crucial step in the process of applying machine learning algorithms in practice. Finding good hyperparameter settings manually is often a time-consuming, tedious process requiring many ad-hoc choices by the practitioner. As a result, much recent work in machine learning has focused on the development of better hyperparameter optimization methods (Hutter, Hoos, and Leyton-Brown 2011; Bergstra et al. 2011; Snoek, Larochelle, and Adams 2012; Bergstra and Bengio 2012).
>
> Recently, Sequential Model-based Bayesian Optimization (SMBO, see, e.g., Brochu, Cora, and de Freitas (2010) for an overview) has emerged as a successful hyperparameter optimization method in machine learning. SMBO has been conclusively shown to yield better performance than both grid and random search and matched or outperformed the state-of-the-art performance for several challenging machine learning problems (Snoek, Larochelle, and Adams 2012; Bergstra et al. 2011; Bergstra, Yamins, and Cox 2013). It has also enabled AutoWEKA (Thornton et al. 2013), which performs combined algorithm selection and hyperparameter optimization in the space of algorithms defined by the WEKA package (Hall et al. 2009).

> [!note] 翻譯
> 超參數最佳化是機器學習演算法實務應用過程中的關鍵步驟。以人工方式尋找良好的超參數設定往往耗時且繁瑣，並需要實作者做出許多臨時性的抉擇。因此，近年許多機器學習研究致力於發展更好的超參數最佳化方法（Hutter, Hoos, and Leyton-Brown 2011; Bergstra et al. 2011; Snoek, Larochelle, and Adams 2012; Bergstra and Bengio 2012）。
>
> 近來，序列式模型貝葉斯最佳化（SMBO；綜述可參見 Brochu, Cora, and de Freitas (2010)）已成為機器學習中一種成功的超參數最佳化方法。研究已明確顯示，SMBO 的表現優於網格搜尋（grid search）與隨機搜尋（random search），並在數個具挑戰性的機器學習問題上達到或超越了最先進水準（Snoek, Larochelle, and Adams 2012; Bergstra et al. 2011; Bergstra, Yamins, and Cox 2013）。它也催生了 AutoWEKA（Thornton et al. 2013），該系統在 WEKA 套件（Hall et al. 2009）所定義的演算法空間中執行演算法選擇與超參數的聯合最佳化。

---

> [!quote] Original
> However, as a generic function optimization framework, SMBO requires a substantial number of evaluations to detect high-performance regions when started on a new optimization problem. The resulting overhead is computationally infeasible for expensive-to-evaluate machine learning algorithms. A promising approach to combat this problem is to apply meta-learning (Brazdil et al. 2008) to the hyperparameter search problem. The key concept behind meta-learning for hyperparameter search is to suggest good configurations for a novel dataset based on configurations that are known to perform well on similar, previously evaluated, datasets. We follow this strategy to yield a simple and effective initialization procedure that applies generically to all variants of SMBO; we refer to the resulting SMBO approach with meta-learning-based initialization as MI-SMBO. Importantly, MI-SMBO does not require any adaptation of the underlying SMBO procedure. It is hence easy to implement and can be readily applied to off-the-shelf hyperparameter optimizers.

> [!note] 翻譯
> 然而，作為一個通用的函數最佳化框架，SMBO 在面對新的最佳化問題時，需要相當多次的評估才能找出高效能區域，由此產生的開銷對於評估成本高昂的機器學習演算法而言在計算上並不可行。對抗此問題的一個有前景的途徑，是將元學習（meta-learning）（Brazdil et al. 2008）應用於超參數搜尋問題。超參數搜尋之元學習的核心概念是：根據已知在相似且先前已評估過的資料集上表現良好的配置，為新資料集建議好的配置。我們遵循此策略，得出一個簡單而有效、可通用於所有 SMBO 變體的初始化程序；我們將這種帶有元學習初始化的 SMBO 方法稱為 MI-SMBO。重要的是，MI-SMBO 不需要對底層的 SMBO 程序做任何修改，因此易於實作，並可直接套用於現成的超參數最佳化器。

---

> [!quote] Original
> We empirically studied the impact of our meta-learning-based initialization procedure on two SMBO variants, using a comprehensive suite of 57 classification datasets and 46 metafeatures. First, we applied our method to a combined algorithm selection and hyperparameter (CASH) optimization problem on this benchmark: choosing between three classifiers from the prominent scikit-learn package (Pedregosa et al. 2011) and simultaneously optimizing their hyperparameters. Second, to demonstrate the generality of our approach, we applied MI-SMBO to the lower-dimensional problem of optimizing the 2 hyperparameters of a support vector machine (SVM) on the same datasets. We found that for the lower-dimensional problem our MI-Spearmint variant of Spearmint (Snoek, Larochelle, and Adams 2012) (a state-of-the-art approach for low-dimensional hyperparameter optimization) yielded mild improvements. For the more challenging CASH problem our MI-SMAC variant of the SMBO method SMAC (Hutter, Hoos, and Leyton-Brown 2011) (a state-of-the-art approach for CASH optimization) yielded substantial improvements, significantly outperforming the previous state of the art for this problem. This paper is an improved and extended version of a previous workshop submission (Feurer, Springenberg, and Hutter 2014).

> [!note] 翻譯
> 我們以 57 個分類資料集與 46 個元特徵（metafeatures）組成的完整基準，實證研究了元學習初始化程序對兩種 SMBO 變體的影響。首先，我們將方法應用於此基準上的 CASH 最佳化問題：在著名的 scikit-learn 套件（Pedregosa et al. 2011）的三個分類器之間做選擇，並同時最佳化其超參數。其次，為展示方法的通用性，我們將 MI-SMBO 應用於較低維的問題：在相同資料集上最佳化支持向量機（support vector machine, SVM）的 2 個超參數。我們發現，在低維問題上，基於 Spearmint（Snoek, Larochelle, and Adams 2012；低維超參數最佳化的最先進方法）的 MI-Spearmint 變體帶來溫和的改進；而在更具挑戰性的 CASH 問題上，基於 SMBO 方法 SMAC（Hutter, Hoos, and Leyton-Brown 2011；CASH 最佳化的最先進方法）的 MI-SMAC 變體則帶來大幅改進，顯著超越了此問題先前的最先進水準。本文是先前一篇研討會投稿（Feurer, Springenberg, and Hutter 2014）的改進與擴充版本。

---

## Foundations | 基礎

> [!quote] Original
> Before we describe our MI-SMBO approach in detail we formally describe hyperparameter optimization and SMBO.
>
> **Hyperparameter Optimization.** Let θ₁, . . . , θₙ denote the hyperparameters of a machine learning algorithm, and let Θ₁, . . . , Θₙ denote their respective domains. The algorithm's hyperparameter space is then defined as Θ = Θ₁ × · · · × Θₙ. When trained with θ ∈ Θ on data D_train, we denote the algorithm's validation error on data D_valid as V(θ, D_train, D_valid). Using k-fold cross-validation, the hyperparameter optimization problem for a given dataset D is to minimize:
>
> f^D(θ) = (1/k) Σᵢ₌₁ᵏ V(θ, D_train⁽ⁱ⁾, D_valid⁽ⁱ⁾).  (1)

> [!note] 翻譯
> 在詳述 MI-SMBO 方法之前，我們先形式化地描述超參數最佳化與 SMBO。
>
> **超參數最佳化。** 令 θ₁, . . . , θₙ 表示某機器學習演算法的超參數，Θ₁, . . . , Θₙ 表示其各自的定義域，則該演算法的超參數空間定義為 Θ = Θ₁ × · · · × Θₙ。當以 θ ∈ Θ 在資料 D_train 上訓練時，我們以 V(θ, D_train, D_valid) 表示演算法在資料 D_valid 上的驗證誤差。採用 k 折交叉驗證（k-fold cross-validation）時，給定資料集 D 的超參數最佳化問題即為最小化：
>
> f^D(θ) = (1/k) Σᵢ₌₁ᵏ V(θ, D_train⁽ⁱ⁾, D_valid⁽ⁱ⁾).  (1)

---

> [!quote] Original
> Hyperparameters θᵢ can be numerical (real or integer, as, e.g., the strength of a regularizer) or categorical (unordered, with finite domain, as, e.g., the choice between different kernels). Furthermore, there can be conditional hyperparameters, which are only active if another hyperparameter takes a certain value; for example, setting the "number of principal components" is conditioned on the usage of PCA as a preprocessing method.
>
> The most frequently used hyperparameter optimization method is grid search, which often performs poorly and does not scale to high dimensions. Therefore, a large body of recent work has focused on better-performing methods, in particular SMBO, which we describe in the following section.

> [!note] 翻譯
> 超參數 θᵢ 可以是數值型（實數或整數，例如正則化器的強度），也可以是類別型（無序、定義域有限，例如在不同核函數之間的選擇）。此外，還可能存在條件式超參數（conditional hyperparameters），僅當另一個超參數取特定值時才生效；例如，「主成分數量」的設定即以是否採用 PCA 作為前處理方法為條件。
>
> 最常用的超參數最佳化方法是網格搜尋，但其表現往往不佳，且無法擴展至高維空間。因此，近年大量研究聚焦於表現更好的方法，特別是 SMBO，我們將在下一節描述。

---

### Sequential Model-based Bayesian Optimization | 序列式模型貝葉斯最佳化

> [!quote] Original
> Sequential Model-based Bayesian Optimization (SMBO) (Jones, Schonlau, and Welch 1998; Brochu, Cora, and de Freitas 2010; Hutter, Hoos, and Leyton-Brown 2011) is a powerful method for global optimization of expensive blackbox functions f. As described in Algorithm 1, SMBO starts by querying the function f at the t values in an initial design and recording the resulting ⟨input, output⟩ pairs ⟨θᵢ, f(θᵢ)⟩ᵢ₌₁ᵗ. Afterwards, it iterates the following three phases: (1) fit a probabilistic model M to the ⟨input, output⟩ pairs collected so far; (2) use the probabilistic model M to select a promising input θ to evaluate next by quantifying the desirability of obtaining the function value at arbitrary inputs θ ∈ Θ through a so-called acquisition function a(θ, M); (3) evaluate the function at the new input θ.
>
> [Algorithm 1: Generic Sequential Model-based Optimization. SMBO(f^D, T, Θ, θ₁:ₜ) — 先評估初始設計中的 t 個配置，之後於每次迭代中擬合模型 M、以 argmax a(θ, M) 選取下一個 θⱼ 並評估，最終回傳誤差最小的 θ*。]

> [!note] 翻譯
> 序列式模型貝葉斯最佳化（SMBO）（Jones, Schonlau, and Welch 1998; Brochu, Cora, and de Freitas 2010; Hutter, Hoos, and Leyton-Brown 2011）是一種對昂貴黑盒函數 f 進行全域最佳化的強大方法。如 Algorithm 1 所述，SMBO 首先在初始設計（initial design）的 t 個值處查詢函數 f，並記錄所得的〈輸入, 輸出〉配對 ⟨θᵢ, f(θᵢ)⟩ᵢ₌₁ᵗ。之後，它反覆執行以下三個階段：(1) 對目前為止收集到的〈輸入, 輸出〉配對擬合一個機率模型 M；(2) 利用機率模型 M 選出下一個值得評估的輸入 θ——透過所謂的擷取函數（acquisition function）a(θ, M) 量化在任意輸入 θ ∈ Θ 處取得函數值的可取性；(3) 在新的輸入 θ 處評估函數。
>
> [Algorithm 1：通用序列式模型最佳化 SMBO(f^D, T, Θ, θ₁:ₜ)——先評估初始設計中的 t 個配置，隨後每次迭代擬合模型 M、以 argmax a(θ, M) 選取下一個 θⱼ 並評估，最終回傳誤差最小的 θ*。]

---

> [!quote] Original
> The SMBO framework offers several degrees of freedom to be instantiated, including the procedure's initialization, the acquisition function to use, and the type of probabilistic model. We discuss three prominent hyperparameter optimization methods in terms of these components: SMAC (Hutter, Hoos, and Leyton-Brown 2011), Spearmint (Snoek, Larochelle, and Adams 2012), and TPE (Bergstra et al. 2011).
>
> The role of the acquisition function a(θ, M) is to trade off exploration in hyperparameter regions where the model M is uncertain with exploitation in regions with low predicted validation error. The most commonly acquisition function (used by all three SMBO methods we discuss) is the expected positive improvement (EI) over the best input found so far (Jones, Schonlau, and Welch 1998):
>
> a_EI(θ, M) = ∫₋∞^∞ max(y* − y, 0) p_M(y|θ) dy.  (2)

> [!note] 翻譯
> SMBO 框架有數個可自由實例化的自由度，包括程序的初始化方式、所採用的擷取函數，以及機率模型的類型。我們就這些組件討論三種著名的超參數最佳化方法：SMAC（Hutter, Hoos, and Leyton-Brown 2011）、Spearmint（Snoek, Larochelle, and Adams 2012）與 TPE（Bergstra et al. 2011）。
>
> 擷取函數 a(θ, M) 的作用在於權衡「探索」（exploration，在模型 M 不確定的超參數區域）與「利用」（exploitation，在預測驗證誤差較低的區域）。最常用的擷取函數（本文討論的三種 SMBO 方法皆採用）是相對於目前已找到最佳輸入的期望正改進量（expected positive improvement, EI）（Jones, Schonlau, and Welch 1998）：
>
> a_EI(θ, M) = ∫₋∞^∞ max(y* − y, 0) p_M(y|θ) dy.  (2)

---

> [!quote] Original
> Several different model types can be used inside of SMBO. The most popular choice, used for example by Spearmint, are Gaussian processes (Rasmussen and Williams 2006) because they provide good predictions in low-dimensional numerical input spaces and allow the computation of the posterior Gaussian process model in closed form. The other popular model type are tree-based approaches, which are particularly well suited to handle high-dimensional and partially categorical input spaces. In particular, SMAC uses random forests (Breiman 2001) modified to yield an uncertainty estimate (Hutter et al. 2014). Another tree-based approach is the Tree Parzen Estimator (TPE) (Bergstra et al. 2011), which constructs a density estimate over good and bad instantiations of each hyperparameter.
>
> The final degree of freedom in SMBO is its initialization. A classic approach is to initialize SMBO with a space-filling design (Jones, Schonlau, and Welch 1998). While this can greatly improve the quality of the model, the corresponding function evaluations are also costly and for expensive hyperparameter optimization problems a cheaper solution is needed. To date, this initialization component has not received much attention, and it is typically instantiated in a fairly ad-hoc manner: Spearmint evaluates f at the first two points of a Sobol sequence, SMAC evaluates it at a user-defined 'default' input, and TPE evaluates 20 points selected at random according to a user-defined prior distribution. It is this initialization component that our MI-SMBO approach improves by starting from a list of hyperparameter configurations suggested by meta-learning.

> [!note] 翻譯
> SMBO 內部可採用多種不同類型的模型。最流行的選擇是高斯過程（Gaussian processes）（Rasmussen and Williams 2006），例如 Spearmint 即採用之，因為高斯過程在低維數值輸入空間中能提供良好預測，且其後驗模型可以閉式（closed form）計算。另一類流行的模型是基於樹的方法，特別適合處理高維且部分為類別型的輸入空間。具體而言，SMAC 使用經修改以產生不確定性估計的隨機森林（random forests）（Breiman 2001; Hutter et al. 2014）。另一種樹式方法是樹狀 Parzen 估計器（Tree Parzen Estimator, TPE）（Bergstra et al. 2011），它對每個超參數之「好」與「壞」取值分別建構密度估計。
>
> SMBO 的最後一個自由度是其初始化。經典做法是以空間填充設計（space-filling design）初始化 SMBO（Jones, Schonlau, and Welch 1998）。雖然這能大幅提升模型品質，但相應的函數評估同樣代價高昂，對昂貴的超參數最佳化問題而言需要更廉價的解法。迄今為止，這個初始化組件並未受到太多關注，通常以相當臨時性的方式實例化：Spearmint 在 Sobol 序列的前兩個點評估 f；SMAC 在使用者定義的「預設」輸入處評估；TPE 則依使用者定義的先驗分布隨機選取 20 個點評估。我們的 MI-SMBO 方法所改進的正是這個初始化組件——改以元學習建議的超參數配置清單作為起點。

---

## Initializing SMBO With Configurations Suggested by Meta-Learning | 以元學習建議之配置初始化 SMBO

> [!quote] Original
> Building on the foundations from the previous section we now describe our proposed MI-SMBO method. The core idea behind MI-SMBO is to follow the common practice machine learning experts employ when applying a known machine learning method to a new dataset D_{N+1}: they first study D_{N+1}, relating it to datasets they previously experienced. When manually optimizing hyperparameters for D_{N+1}, they would begin the search with hyperparameter configurations that were optimal for the most similar previous datasets (see, e.g., Dahl, Sainath, and Hinton (2013); Goodfellow et al. (2013)). Our MI-SMBO method automates this approach and uses it to initialize an SMBO method.

> [!note] 翻譯
> 基於上一節的基礎，我們現在描述所提出的 MI-SMBO 方法。MI-SMBO 的核心思想是遵循機器學習專家將已知方法應用於新資料集 D_{N+1} 時的慣常做法：他們會先研究 D_{N+1}，將其與過去經驗中的資料集建立關聯。在為 D_{N+1} 人工最佳化超參數時，他們會從「在最相似的既有資料集上曾是最佳」的超參數配置開始搜尋（參見 Dahl, Sainath, and Hinton (2013)；Goodfellow et al. (2013)）。我們的 MI-SMBO 方法將此做法自動化，並用以初始化 SMBO 方法。

---

> [!quote] Original
> Formally, MI-SMBO can be stated as follows. Let θ̂₁, . . . , θ̂_N denote the best known hyperparameters for the previously encountered datasets D₁, . . . , D_N, respectively. These may originate from an arbitrary source, e.g., a manual search or the application of an SMBO method during an offline training phase. Further, let D_{N+1} denote a new dataset, let d denote a distance metric between datasets, and let π denote a permutation of (1, . . . , N) sorted by increasing distance between D_{N+1} and Dᵢ (i.e., (π(i) ≤ π(j)) ⇔ (d(D_{N+1}, Dᵢ) ≤ d(D_{N+1}, Dⱼ))). Then, MI-SMBO with an initial design of t configurations initializes SMBO with configurations θ̂_{π(1)}, . . . , θ̂_{π(t)}. Algorithm 2 provides pseudocode for the approach.
>
> [Algorithm 2: SMBO with Meta-Learning Initialization. MI-SMBO(D_{N+1}, f^{D_{N+1}}, D_{1:N}, θ̂_{1:N}, d, t, T, Θ) — 依與 D_{N+1} 的距離遞增排序資料集索引，取前 t 個資料集的最佳配置作為初始設計，再呼叫 SMBO。]

> [!note] 翻譯
> 形式上，MI-SMBO 可陳述如下。令 θ̂₁, . . . , θ̂_N 分別表示先前遇過的資料集 D₁, . . . , D_N 各自已知的最佳超參數。這些配置可來自任意來源，例如人工搜尋，或離線訓練階段中 SMBO 方法的執行結果。進一步令 D_{N+1} 表示新資料集，d 表示資料集間的距離度量，π 表示按 D_{N+1} 與 Dᵢ 距離遞增排序的 (1, . . . , N) 排列（即 (π(i) ≤ π(j)) ⇔ (d(D_{N+1}, Dᵢ) ≤ d(D_{N+1}, Dⱼ))）。那麼，初始設計含 t 個配置的 MI-SMBO 即以配置 θ̂_{π(1)}, . . . , θ̂_{π(t)} 初始化 SMBO。Algorithm 2 給出了此方法的偽代碼。
>
> [Algorithm 2：帶元學習初始化的 SMBO。MI-SMBO(D_{N+1}, f^{D_{N+1}}, D_{1:N}, θ̂_{1:N}, d, t, T, Θ)——依與 D_{N+1} 的距離遞增排序資料集索引，取前 t 個資料集的最佳配置作為初始設計，再呼叫 SMBO。]

---

> [!quote] Original
> We would like to highlight the fact that MI-SMBO is agnostic of the SMBO algorithm used, as long as the algorithm's implementation accepts an initial design as input or can be warmstarted with a given list of performance data ⟨θᵢ, yᵢ⟩ᵢ₌₁ᵗ. All of SMAC, TPE, and Spearmint fulfill these criteria. Furthermore, in contrast to existing approaches that initialize direct search algorithms via meta-learning (Gomes et al. 2012; Reif, Shafait, and Dengel 2012), SMBO is a particularly good match for initialization with meta-learning as it can make effective use of all performance data it receives as input. In practice, this procedure replaces SMACs and Spearmints initialization and delays the start of the actual SMBO procedure until all configurations θ̂_{π(1)}, . . . , θ̂_{π(t)} are evaluated.

> [!note] 翻譯
> 我們要強調，MI-SMBO 對所使用的 SMBO 演算法是不可知的（agnostic），只要該演算法的實作能接受初始設計作為輸入，或能以給定的效能資料清單 ⟨θᵢ, yᵢ⟩ᵢ₌₁ᵗ 進行暖啟動（warm-starting）即可。SMAC、TPE 與 Spearmint 皆滿足這些條件。此外，與既有透過元學習初始化直接搜尋演算法的方法（Gomes et al. 2012; Reif, Shafait, and Dengel 2012）不同，SMBO 特別適合搭配元學習初始化，因為它能有效利用其接收到的所有效能資料。實務上，此程序取代了 SMAC 與 Spearmint 的原始初始化，並將真正 SMBO 程序的啟動延後至所有配置 θ̂_{π(1)}, . . . , θ̂_{π(t)} 都評估完成之後。

---

> [!quote] Original
> The last component needed to implement MI-SMBO is the definition of a distance metric between datasets. This problem was, to our knowledge, first discussed by Soares and Brazdil (2000). For the purpose of this work we assume that each dataset Dᵢ can be described by a set of F metafeatures mᵢ = (mᵢ₁, . . . , mᵢ_F). We discuss the metafeatures we used in the next section. In practice, we precompute the metafeatures for all training datasets D₁, . . . , D_N, along with the best configurations (θ̂₁, . . . , θ̂_N). Given a new dataset D_{N+1}, we then measure its distances to all previous datasets Dᵢ using a distance measure d : D × D → R.
>
> We experimented with two different instantiations of this distance measure d(·, ·). The first measure (denoted as d_p) we used is the commonly-used p-norm of the difference between the datasets' metafeatures:
>
> d_p(Dᵢ, Dⱼ) = ‖mᵢ − mⱼ‖_p.  (3)
>
> Next to this standard metric we aimed for a metric that reflects how similar the datasets are with respect to the performance of different hyperparameter settings. The measure we use (in the following denoted as d_c) is the negative Spearman correlation coefficient between the ranked results of a fixed set of n hyperparameter configurations on both datasets:
>
> d_c(Dᵢ, Dⱼ) = 1 − Corr([f^{Dᵢ}(θ₁), . . . , f^{Dᵢ}(θₙ)], [f^{Dⱼ}(θ₁), . . . , f^{Dⱼ}(θₙ)]).  (4)

> [!note] 翻譯
> 實作 MI-SMBO 所需的最後一個組件，是資料集間距離度量的定義。據我們所知，此問題最早由 Soares and Brazdil (2000) 討論。就本研究而言，我們假設每個資料集 Dᵢ 可由一組 F 個元特徵 mᵢ = (mᵢ₁, . . . , mᵢ_F) 描述；所用元特徵將於下一節討論。實務上，我們預先計算所有訓練資料集 D₁, . . . , D_N 的元特徵，以及各自的最佳配置 (θ̂₁, . . . , θ̂_N)。給定新資料集 D_{N+1} 後，我們以距離度量 d : D × D → R 衡量其與所有既有資料集 Dᵢ 的距離。
>
> 我們實驗了此距離度量 d(·, ·) 的兩種不同實例。第一種（記為 d_p）是常用的、資料集元特徵之差的 p-norm：
>
> d_p(Dᵢ, Dⱼ) = ‖mᵢ − mⱼ‖_p.  (3)
>
> 除此標準度量外，我們還希望有一種能反映「兩個資料集在不同超參數設定之效能表現上有多相似」的度量。我們採用的度量（以下記為 d_c）是固定 n 個超參數配置在兩個資料集上排名結果之間的負 Spearman 相關係數：
>
> d_c(Dᵢ, Dⱼ) = 1 − Corr([f^{Dᵢ}(θ₁), . . . , f^{Dᵢ}(θₙ)], [f^{Dⱼ}(θ₁), . . . , f^{Dⱼ}(θₙ)]).  (4)

---

> [!quote] Original
> Of course, this distance measure cannot be computed directly for the new dataset D_{N+1} since we have not yet evaluated f^{D_{N+1}}(θ₁), . . . , f^{D_{N+1}}(θₙ). However, we can compute d_c(Dᵢ, Dⱼ) for all 1 ≤ i, j ≤ N and use regression to learn a function R : R^F × R^F → R, mapping from pairs of meta-features ⟨mᵢ, mⱼ⟩ to d_c(Dᵢ, Dⱼ). Using this pre-trained regressor the distance metric can then be approximated as
>
> d_c(D_{N+1}, Dⱼ) ≈ R(m_{N+1}, mⱼ).  (5)
>
> In our experiments, we implemented R using a random forest because of its robustness and speed.

> [!note] 翻譯
> 當然，此距離度量無法對新資料集 D_{N+1} 直接計算，因為我們尚未評估 f^{D_{N+1}}(θ₁), . . . , f^{D_{N+1}}(θₙ)。然而，我們可以計算所有 1 ≤ i, j ≤ N 的 d_c(Dᵢ, Dⱼ)，並以迴歸學習一個函數 R : R^F × R^F → R，將元特徵配對 ⟨mᵢ, mⱼ⟩ 映射至 d_c(Dᵢ, Dⱼ)。利用此預先訓練好的迴歸器，距離度量即可近似為
>
> d_c(D_{N+1}, Dⱼ) ≈ R(m_{N+1}, mⱼ).  (5)
>
> 在實驗中，考量其穩健性與速度，我們以隨機森林實作 R。

---

### Implemented Metafeatures | 實作之元特徵

> [!quote] Original
> To evaluate our approach in a realistic setting we implemented 46 metafeatures from the literature; they are listed in Table 1. Based on their types and underlying assumptions, these metafeatures can be divided into at least five groups:
> - Simple metafeatures, such as the number of features, patterns or classes, describe the basic dataset structure (Michie et al. 1994; Kalousis 2002)
> - PCA metafeatures (Bardenet et al. 2013) compute various statistics of the datasets principal components.
> - The information-theoretic metafeature measures the class entropy in the data (Michie et al. 1994).
> - Statistical metafeatures (Michie et al. 1994) characterize the data via descriptive statistics such as the kurtosis or the dispersion of the label distribution.
> - Landmarking metafeatures (Pfahringer, Bensusan, and Giraud-Carrier 2000) are computed by running several fast machine learning algorithms on the dataset. Based on their learning scheme they can capture different properties of the dataset, like e.g. linear separability.
>
> For each dataset, metafeatures are only computed on the training set. In our experiments, for each dataset this required less than one minute and less than the average time it took to evaluate one hyperparameter configuration on that dataset.
>
> [Table 1: List of implemented metafeatures — 涵蓋簡單元特徵（樣本數、特徵數、類別數、缺失值統計、維度比等）、統計元特徵（峰度、偏度、類別型特徵統計等）、PCA 元特徵、資訊理論元特徵（類別熵）與地標式元特徵（1-NN、LDA、Naive Bayes、決策樹等）。]

> [!note] 翻譯
> 為了在貼近實際的情境下評估方法，我們實作了文獻中的 46 個元特徵，列於 Table 1。依其類型與背後假設，這些元特徵至少可分為五組：
> - 簡單元特徵（simple metafeatures）：如特徵數、樣本數（patterns）或類別數，描述資料集的基本結構（Michie et al. 1994; Kalousis 2002）。
> - PCA 元特徵（Bardenet et al. 2013）：計算資料集主成分的各種統計量。
> - 資訊理論元特徵（information-theoretic metafeature）：衡量資料中的類別熵（class entropy）（Michie et al. 1994）。
> - 統計元特徵（statistical metafeatures）（Michie et al. 1994）：以描述統計量刻畫資料，例如峰度（kurtosis）或標籤分布的離散程度。
> - 地標式元特徵（landmarking metafeatures）（Pfahringer, Bensusan, and Giraud-Carrier 2000）：透過在資料集上執行數個快速的機器學習演算法計算而得；依其學習機制，可捕捉資料集的不同性質，例如線性可分性。
>
> 對每個資料集，元特徵僅在訓練集上計算。在我們的實驗中，每個資料集的元特徵計算耗時不到一分鐘，且少於在該資料集上評估一個超參數配置的平均時間。
>
> [Table 1：實作之元特徵清單——涵蓋簡單元特徵（樣本數、特徵數、類別數、缺失值統計、維度比等）、統計元特徵（峰度、偏度、類別型特徵統計等）、PCA 元特徵、資訊理論元特徵（類別熵）與地標式元特徵（1-NN、LDA、Naive Bayes、決策樹等）。]

---

## Application to Machine Learning Algorithms | 應用於機器學習演算法

> [!quote] Original
> We now discuss the machine learning algorithms and their hyperparameters we optimized, as well as the datasets we used in our experiments.
>
> **ML Algorithms and Hyperparameters.** We empirically evaluated our MI-SMBO approach to optimize two practically relevant machine learning frameworks. We focused on supervised classification because it is the most widely studied problem in metalearning, with a large body of literature and readily available metafeatures and datasets.

> [!note] 翻譯
> 接下來討論我們所最佳化的機器學習演算法及其超參數，以及實驗所用的資料集。
>
> **機器學習演算法與超參數。** 我們實證評估了 MI-SMBO 方法在兩種具實務意義的機器學習框架上的最佳化表現。我們聚焦於監督式分類，因為它是元學習領域研究最廣泛的問題，擁有大量文獻以及現成可用的元特徵與資料集。

---

> [!quote] Original
> The large configuration space for our main experiment is spanned by a range of machine learning algorithms from scikit-learn (Pedregosa et al. 2011). We combined all algorithms into a single hierarchical optimization problem using the Combined Algorithm Selection and Hyperparameter optimization (CASH) setting by Thornton et al. (2013): we used one top-level hyperparameter θ_classifier for choosing between classification algorithms, and set all hyperparameters of classification algorithm Aᵢ as conditional on θ_classifier being set to Aᵢ. This CASH problem is of high practical relevance since it describes precisely the problem an end user faces when given a new dataset. To keep the computation bearable and the results interpretable, we only included three classification algorithms: an SVM with an RBF kernel, a linear SVM, and random forests. Since we expected noise and redundancies in the training data, we also allowed the optimization procedure to use Principal Component Analysis (PCA) for preprocessing; with the number of PCA components being conditional on PCA being applied. In total this lead to 10 hyperparameters, as detailed in Table 2. We discretized these 10 hyperparameters to obtain a manageable number of 1 623 hyperparameter configurations that allowed the exhaustive precomputation of classification errors for the entire grid.
>
> [Table 2: Hyperparameters for the CASH problem in scikit-learn — θ_classifier ∈ {RF, SVM, LinearSVM}、preprocessing ∈ {PCA, None}，以及 SVM 的 log₂(C)、log₂(γ)、LinearSVM 的 log₂(C) 與 penalty、RF 的 min splits、max features、criterion、PCA 的保留變異量；除 θ_classifier 與 preprocessing 外皆為條件式超參數。]

> [!note] 翻譯
> 主實驗的大型配置空間由 scikit-learn（Pedregosa et al. 2011）中的一系列機器學習演算法張成。我們採用 Thornton et al. (2013) 的 CASH（演算法選擇與超參數聯合最佳化）設定，將所有演算法合併為單一的階層式最佳化問題：以一個頂層超參數 θ_classifier 在分類演算法之間做選擇，並將分類演算法 Aᵢ 的所有超參數設為以「θ_classifier 被設為 Aᵢ」為條件。此 CASH 問題具有高度實務意義，因為它恰好描述了終端使用者面對新資料集時所遇到的問題。為使計算可承受且結果可解釋，我們僅納入三種分類演算法：RBF 核 SVM、線性 SVM 與隨機森林。由於預期訓練資料中存在雜訊與冗餘，我們也允許最佳化程序使用主成分分析（Principal Component Analysis, PCA）作為前處理，其中 PCA 成分數以「是否採用 PCA」為條件。合計共有 10 個超參數，詳見 Table 2。我們將這 10 個超參數離散化，得到 1623 個可管理的超參數配置，從而能對整個網格窮舉預先計算分類誤差。
>
> [Table 2：scikit-learn CASH 問題之超參數——θ_classifier ∈ {RF, SVM, LinearSVM}、preprocessing ∈ {PCA, None}，以及 SVM 的 log₂(C)、log₂(γ)、LinearSVM 的 log₂(C) 與 penalty、RF 的 min splits、max features、criterion、PCA 的保留變異量；除 θ_classifier 與 preprocessing 外皆為條件式超參數。]

---

> [!quote] Original
> We performed a second experiment to test the suitability of our method for a low-dimensional hyperparameter optimization problem: optimizing the complexity penalty C and the kernel width γ of an SVM using an RBF kernel. As above, we discretized these two hyperparameters to a grid of 19 · 21 = 399 combinations; these constitute a subset of the configurations considered in the CASH problem above.

> [!note] 翻譯
> 我們進行了第二個實驗，以檢驗本方法在低維超參數最佳化問題上的適用性：最佳化 RBF 核 SVM 的複雜度懲罰 C 與核寬度 γ。與前述做法相同，我們將這兩個超參數離散化為 19 × 21 = 399 個組合的網格；這些組合構成上述 CASH 問題所考慮配置的一個子集。

---

### Datasets and Preprocessing | 資料集與前處理

> [!quote] Original
> For our experiments, we aimed for a large number of high-quality classification datasets. We found the OpenML project (Vanschoren et al. 2013) to be the best source of datasets and used the 60 classification datasets it contained in April 2014. For computational reasons we had to exclude three datasets, leaving us with a total of 57. We first shuffled each dataset and then split it in stratified fashion into 2/3 training and 1/3 test data. Then, we computed the validation performance for Bayesian optimization by ten-fold crossvalidation on the training dataset.
>
> To use the same dataset for each classification algorithm, we coded categorical features using a one-hot (aka 1-in-k) encoding, replacing each categorical feature f with domain {v₁, . . . , vₖ} by k binary variables, only the i-th of which is set to true for data points where f is set to vᵢ. To retain sparsity, we replaced any missing values with zero. Finally, we scaled numerical features linearly to the range [0, 1].

> [!note] 翻譯
> 我們的實驗需要大量高品質的分類資料集。我們發現 OpenML 專案（Vanschoren et al. 2013）是最佳的資料集來源，並使用其在 2014 年 4 月所收錄的 60 個分類資料集。基於計算上的考量，我們必須排除三個資料集，最終共計 57 個。我們先將每個資料集洗牌，再以分層（stratified）方式切分為 2/3 訓練資料與 1/3 測試資料，隨後在訓練資料上以十折交叉驗證計算貝葉斯最佳化所用的驗證效能。
>
> 為使每個分類演算法都能使用相同的資料集，我們以 one-hot（又稱 1-in-k）編碼處理類別型特徵：將定義域為 {v₁, . . . , vₖ} 的每個類別特徵 f 替換為 k 個二元變數，且僅當資料點的 f 取值為 vᵢ 時，第 i 個變數才設為真。為保持稀疏性，所有缺失值以零取代。最後，我們將數值特徵線性縮放至 [0, 1] 區間。

---

## Experiments | 實驗

### Experimental Setup | 實驗設定

> [!quote] Original
> We precomputed the 10-fold crossvalidation error on all 57 datasets for each of the 1 623 hyperparameter configurations in our CASH problem. Because the configurations for the SVM benchmark form a subset of these configurations, the corresponding results were reused for the second experiment. Although the classification datasets were no larger than medium-sized (< 20 000 data points), calculating the grid took up to three days per dataset on a modern CPU. This extensive precomputation allowed us to run all our experiments in simulation, by using a lookup table in lieu of running an actual algorithm.
>
> We evaluated our MI-SMBO approach in a leave-one-dataset-out fashion: to evaluate it on one dataset, we assumed knowledge of the other 56 datasets and their best hyperparameter settings. Because Bayesian optimization contains random factors, we repeated each optimization run ten times on each dataset. In total, we thus executed each optimization procedure 570 times.

> [!note] 翻譯
> 我們對 CASH 問題中的 1623 個超參數配置，在全部 57 個資料集上預先計算了十折交叉驗證誤差。由於 SVM 基準的配置是這些配置的子集，其結果可重複用於第二個實驗。儘管這些分類資料集規模不超過中等（少於 20,000 筆資料點），在現代 CPU 上計算整個網格每個資料集仍需耗時多達三天。這種大規模預先計算使我們能以查表（lookup table）代替實際執行演算法，從而以模擬方式進行所有實驗。
>
> 我們以留一資料集（leave-one-dataset-out）方式評估 MI-SMBO：在某一資料集上評估時，假設已知其他 56 個資料集及其最佳超參數設定。由於貝葉斯最佳化含有隨機因素，我們在每個資料集上將每次最佳化執行重複十次。因此，每種最佳化程序總計執行了 570 次。

---

> [!quote] Original
> Our meta-learning initialization approach has several free design choices we had to instantiate for our experiments. These are: the distance metric d, the used metafeatures (we experimented with several subsets suggested in the literature (Pfahringer, Bensusan, and Giraud-Carrier 2000; Bardenet et al. 2013; Yogatama and Mann 2014)) and the number t ∈ {5, 10, 20, 25} of configurations used for initializing SMBO. In total, we evaluated 40 different instantiations of our meta-learning procedure. Due to space restrictions, we only report results for the best of these instantiations; for more results, please see the supplementary material: www.automl.org/aaai2015-mi-smbo-supplementary.pdf
>
> Concerning distance measures, we found the results with d_p and d_c distance to be qualitatively similar, with slightly better results for the d_c measure. We thus restrict the plots to d_c in several experiments to avoid clutter in the plots. Our experiments with different metafeatures showed that there is no general best set of metafeatures; thus, we only report results using all metafeatures.

> [!note] 翻譯
> 我們的元學習初始化方法有數個須在實驗中實例化的自由設計選項，包括：距離度量 d、所用的元特徵（我們實驗了文獻中建議的數個子集（Pfahringer, Bensusan, and Giraud-Carrier 2000; Bardenet et al. 2013; Yogatama and Mann 2014）），以及用於初始化 SMBO 的配置數量 t ∈ {5, 10, 20, 25}。我們總共評估了元學習程序的 40 種不同實例。囿於篇幅，我們僅報告其中最佳實例的結果；更多結果請參見補充材料：www.automl.org/aaai2015-mi-smbo-supplementary.pdf。
>
> 就距離度量而言，我們發現 d_p 與 d_c 的結果在性質上相似，其中 d_c 略優。因此，為避免圖表雜亂，若干實驗僅繪出 d_c 的結果。不同元特徵組合的實驗顯示，並不存在普遍最佳的元特徵集合；因此我們僅報告使用全部元特徵的結果。

---

### Warmstarting SMAC for Optimizing scikit-learn | 暖啟動 SMAC 以最佳化 scikit-learn

> [!quote] Original
> We now report our results for solving the CASH problem in scikit-learn. First, we evaluated the base performance of the hyperparameter optimization procedures random search, TPE, and SMAC (note that for TPE the prior distributions were uniform) on all 57 datasets and then added meta-learning-initialization to the best of these. Due to the conditional hyperparameters in the scikit-learn space we excluded Spearmint, which – without modification – is known to perform poorly in their presence (Eggensperger et al. 2013).
>
> Figure 1 presents the qualitative performance of all optimizers on three representative datasets. The plots show the mean of the best function values for one optimizer obtained up to a given number of function evaluations. Overall, we found SMAC to outperform both TPE and random search for this large hyperparameter space, confirming the results of Eggensperger et al. (2013). We thus applied our meta-learning initialization to SMAC, but would also expect TPE to benefit from it.

> [!note] 翻譯
> 我們現在報告在 scikit-learn 中求解 CASH 問題的結果。首先，我們在全部 57 個資料集上評估了隨機搜尋、TPE 與 SMAC 三種超參數最佳化程序的基準效能（注意 TPE 的先驗分布設為均勻分布），然後為其中表現最好者加上元學習初始化。由於 scikit-learn 空間中存在條件式超參數，我們排除了 Spearmint——已知未經修改的 Spearmint 在條件式超參數存在時表現不佳（Eggensperger et al. 2013）。
>
> Figure 1 呈現了所有最佳化器在三個代表性資料集上的定性表現。圖中顯示各最佳化器在給定函數評估次數內所得最佳函數值的平均。整體而言，在這個大型超參數空間中，SMAC 優於 TPE 與隨機搜尋，印證了 Eggensperger et al. (2013) 的結果。因此我們將元學習初始化應用於 SMAC，但預期 TPE 同樣能從中受益。

---

> [!quote] Original
> Figure 1 also compares qualitative results of MI-SMAC to the three baselines. In the left plot, the meta-learning suggestions were reasonable and thus lead to MI-SMAC successively improving them over time. In the middle plot the second configuration suggested by meta-learning was already the best, leaving no room for improvement by SMAC. The right plot highlights the fact that meta-learning can also fail and decrease SMAC's performance.
>
> [Figure 1: Difference in validation error between hyperparameters found by SMBO and the best value obtained via full grid search for three datasets (liver-disorders, heart-h, hepatitis) with scikit-learn. (20,d,X) stands for MI-SMAC with an initial design of t = 20 configurations suggested by meta-learning with distance measure d using metafeatures X.]

> [!note] 翻譯
> Figure 1 亦將 MI-SMAC 的定性結果與三個基線比較。左圖中，元學習的建議合理，因此 MI-SMAC 得以隨時間逐步改進它們。中圖中，元學習建議的第二個配置已是最佳，SMAC 已無改進空間。右圖則突顯元學習也可能失敗，並降低 SMAC 的表現。
>
> [Figure 1：在三個資料集（liver-disorders、heart-h、hepatitis）上，SMBO 找到的超參數之驗證誤差與完整網格搜尋所得最佳值之差。(20,d,X) 表示以距離度量 d、元特徵 X 的元學習建議 t = 20 個配置作為初始設計的 MI-SMAC。]

---

> [!quote] Original
> Next, we analyzed MI-SMAC's performance using the same ranking-based evaluation as Bardenet et al. (2013) to aggregate over datasets. For each dataset and each function evaluation, we computed the ranks of the three baselines and the two MI-SMAC variants. More precisely, since we had 10 runs of each of the five methods available for each dataset (which give rise to 10⁵ possible combinations), we drew a bootstrap sample of 1000 joint runs of the five optimizers and computed the average ranks across these runs. We then further averaged these average ranks across the 57 datasets and show the results in Figure 2. We remind the reader that the rank is a measure of performance relative to the performance of the other optimizers; thus, a method's rank can increase over time (with larger function evaluation budgets), even though its error decreases, if the other methods achieve greater error reductions. Furthermore, we note that the ranks do not reflect the magnitude of the difference between raw function values.

> [!note] 翻譯
> 接著，我們採用與 Bardenet et al. (2013) 相同的基於排名的評估方式，跨資料集彙整分析 MI-SMAC 的表現。對每個資料集與每個函數評估次數，我們計算三個基線與兩個 MI-SMAC 變體的排名。更精確地說，由於每個資料集上五種方法各有 10 次執行（可產生 10⁵ 種可能組合），我們對五個最佳化器的聯合執行抽取 1000 組自助（bootstrap）樣本，並計算這些執行的平均排名；再將這些平均排名跨 57 個資料集進一步平均，結果示於 Figure 2。我們提醒讀者：排名是相對於其他最佳化器表現的度量；因此，若其他方法達成更大的誤差降低，某方法的排名可能隨時間（更大的函數評估預算）上升，即使其自身誤差在下降。此外，排名並不反映原始函數值差異的幅度。

---

> [!quote] Original
> As Figure 2 shows, the two variants of MI-SMAC performed best, converging to similar ranks with larger function evaluation budgets; and meta-learning yielded dramatically better results for very small function evaluation budgets. We also note that even after 50 function evaluations no SMBO method had fully caught up to the MI-SMBO results. This indicates that meta-learning initialization provided not only good performance with few function evaluations but also a good basis for SMAC to improve upon further.
>
> To demonstrate the effect of varying the number of initial configurations t selected by meta-learning, we plotted the ranks of different instantiations of MI-SMAC in Figure 3. We observe that within the range of t we studied MI-SMAC performs better with more initial configurations.
>
> [Figure 2: Ranks of various optimizers averaged over all datasets for the CASH problem in scikit-learn. / Figure 3: Ranks of SMAC and various MI-SMAC variants averaged over all datasets for the CASH problem in scikit-learn.]

> [!note] 翻譯
> 如 Figure 2 所示，MI-SMAC 的兩個變體表現最佳，並在較大的函數評估預算下收斂至相近的排名；而在極小的函數評估預算下，元學習帶來顯著更好的結果。我們亦注意到，即使經過 50 次函數評估，仍沒有任何 SMBO 方法完全追上 MI-SMBO 的結果。這顯示元學習初始化不僅在少量函數評估下提供了良好表現，也為 SMAC 的進一步改進提供了良好基礎。
>
> 為展示改變元學習所選初始配置數量 t 的效果，我們在 Figure 3 中繪出 MI-SMAC 不同實例的排名。我們觀察到，在所研究的 t 範圍內，初始配置越多，MI-SMAC 表現越好。
>
> [Figure 2：scikit-learn CASH 問題中各最佳化器跨所有資料集的平均排名。／Figure 3：SMAC 與各 MI-SMAC 變體跨所有資料集的平均排名。]

---

> [!quote] Original
> To complement the above ranking analysis, Figure 4 (top) quantifies on how many datasets MI-SMAC with a learned distance performed significantly better than the other methods according to a two-sided t-test, while Figure 4 (bottom) shows the statistically significant losses. Both of these quantities are plotted over time, as the function evaluation budget increases.
>
> Compared to the optimizers without meta-learning, MI-SMAC performed much better from the start. Even after 50 iterations, it performed significantly better than TPE on 28% of the datasets (in 11% worse), better than SMAC on 35% of the datasets (in 7% worse), and better than random search on 43% of the datasets (in 9% worse). We would like to point out that the improvement MI-SMAC yielded over SMAC is larger than the improvement that SMAC yielded over random search (in 20% better). We attribute this success to the large search space for this problem, which not even SMAC can effectively search in as little as 50 function evaluations. Leveraging successful optimizations from previous datasets clearly helped SMAC in this complex search space.
>
> [Figure 4: Percentage of wins of MI-SMAC with an initial design of t = 20 configurations suggested by meta-learning using the learned distance on all metafeatures; top: significant wins, bottom: significant losses (two-sided t-test).]

> [!note] 翻譯
> 為補充上述排名分析，Figure 4（上）量化了採用學習式距離的 MI-SMAC 依雙尾 t 檢定（two-sided t-test）在多少個資料集上顯著優於其他方法，Figure 4（下）則顯示統計上顯著的落敗情形。兩者皆隨函數評估預算增加而繪製其隨時間的變化。
>
> 與未使用元學習的最佳化器相比，MI-SMAC 從一開始就表現得好得多。即使經過 50 次迭代，它仍在 28% 的資料集上顯著優於 TPE（於 11% 較差）、在 35% 的資料集上優於 SMAC（於 7% 較差）、在 43% 的資料集上優於隨機搜尋（於 9% 較差）。我們要指出，MI-SMAC 相對 SMAC 的改進幅度，大於 SMAC 相對隨機搜尋的改進（後者為 20% 較優）。我們將此成功歸因於該問題的大型搜尋空間——即使是 SMAC，也無法在僅僅 50 次函數評估內有效搜尋。善用先前資料集上成功的最佳化結果，顯然幫助了 SMAC 應對這個複雜的搜尋空間。
>
> [Figure 4：以學習式距離、全部元特徵、t = 20 個元學習建議配置作為初始設計的 MI-SMAC 之勝率；上：顯著勝出，下：顯著落敗（雙尾 t 檢定）。]

---

### Warmstarting Spearmint for Optimizing SVMs | 暖啟動 Spearmint 以最佳化 SVM

> [!quote] Original
> To test the generality of our approach we performed an additional experiment on a lower dimensional problem; optimizing the hyperparameters of an SVM on all 57 datasets using Spearmint. We expected Spearmint to yield the best results for this problem as it is known to perform well in cases where the hyperparameters are few and real-valued (Eggensperger et al. 2013). A statistical analysis using a two-sided t-test on the performances for each of the 57 datasets confirms this hypothesis, as Spearmint indeed significantly outperformed TPE, SMAC, and random search in 32%, 44%, and 52% of the datasets, respectively, and only lost in 7%, 8%, and 9% of the cases, respectively.
>
> The ranking plot in Figure 5 shows the performance of Spearmint and two MI-Spearmint variants compared to SMAC, TPE and random search. As this plot shows, the three variants of Spearmint performed best, converging to a similar rank with larger function evaluation budgets. While meta-learning yielded considerably better results for small function evaluation budgets, after about 10 evaluations Spearmint caught up.
>
> As for the scikit-learn benchmark, we also evaluated the effect of using different values of t and plotted these in Figure 6. In contrast to the results for scikit-learn, for this benchmark it was better to use less configurations suggested by meta-learning. In both benchmarks, however, MI-SMBO yielded substantial performance gains over SMBO during the first function evaluations.
>
> [Figure 5: Ranks of various optimizers averaged over all datasets for optimizing the SVM. / Figure 6: Ranks of Spearmint and various MI-Spearmint variants averaged over all datasets for optimizing the SVM.]

> [!note] 翻譯
> 為檢驗方法的通用性，我們在一個較低維的問題上進行了額外實驗：使用 Spearmint 在全部 57 個資料集上最佳化 SVM 的超參數。我們預期 Spearmint 在此問題上會取得最佳結果，因為已知其在超參數少且為實值的情況下表現良好（Eggensperger et al. 2013）。對 57 個資料集各自效能進行的雙尾 t 檢定統計分析證實了此假設：Spearmint 分別在 32%、44% 與 52% 的資料集上顯著優於 TPE、SMAC 與隨機搜尋，且分別僅在 7%、8% 與 9% 的情況下落敗。
>
> Figure 5 的排名圖顯示了 Spearmint 與兩個 MI-Spearmint 變體相對於 SMAC、TPE 與隨機搜尋的表現。如圖所示，Spearmint 的三個變體表現最佳，並在較大的函數評估預算下收斂至相近排名。雖然元學習在小型函數評估預算下帶來明顯更好的結果，但大約 10 次評估之後 Spearmint 便迎頭趕上。
>
> 與 scikit-learn 基準相同，我們也評估了不同 t 值的效果，並繪於 Figure 6。與 scikit-learn 的結果相反，在此基準上採用較少的元學習建議配置反而較好。不過，在兩個基準中，MI-SMBO 都在最初若干次函數評估期間相對 SMBO 帶來可觀的效能增益。
>
> [Figure 5：最佳化 SVM 時各最佳化器跨所有資料集的平均排名。／Figure 6：最佳化 SVM 時 Spearmint 與各 MI-Spearmint 變體跨所有資料集的平均排名。]

---

## Related Work and Possible Extensions | 相關工作與可能的延伸

> [!quote] Original
> Existing work on using meta-learning for hyperparameter optimization roughly follows two different directions of research. Firstly, Leite, Brazdil, and Vanschoren (2012) developed Active Testing, a method similar to SMBO that reasons across datasets. In contrast to SMBO, Active Testing is a pure algorithm selection method which does not model the effect of hyperparameters (and algorithms) on the results and is limited to a finite number of algorithms. Secondly, meta-learning was used to initialize model-free hyperparameter optimization methods with configurations that previously yielded good performance on similar datasets (Reif, Shafait, and Dengel 2012; Gomes et al. 2012). While similar to our work, these methods were limited by their search mechanism and did not improve the state of the art in hyperparameter optimization.
>
> There also exist first attempts to formalize SMBO across several datasets. These collaborative SMBO methods (Bardenet et al. 2013; Swersky, Snoek, and Adams 2013; Yogatama and Mann 2014) address the knowledge transfer directly in the SMBO procedure. However, to date they are limited to small-scale problems with few continuous hyperparameters and a handful of meta-features. In contrast to MI-SMBO they are dependent on the specific SMBO implementation and cannot be readily applied to off-the-shelf hyperparameter optimizers.

> [!note] 翻譯
> 既有將元學習用於超參數最佳化的研究大致沿兩個方向發展。其一，Leite, Brazdil, and Vanschoren (2012) 提出了主動測試（Active Testing），這是一種類似 SMBO、能跨資料集推理的方法。與 SMBO 不同，主動測試是純粹的演算法選擇方法，不對超參數（與演算法）對結果的影響建模，且僅限於有限個演算法。其二，元學習曾被用於以「先前在相似資料集上表現良好的配置」初始化無模型（model-free）超參數最佳化方法（Reif, Shafait, and Dengel 2012; Gomes et al. 2012）。這些方法雖與本研究相似，但受限於其搜尋機制，並未提升超參數最佳化的最先進水準。
>
> 此外，亦有將 SMBO 形式化為跨多資料集的初步嘗試。這些協作式 SMBO（collaborative SMBO）方法（Bardenet et al. 2013; Swersky, Snoek, and Adams 2013; Yogatama and Mann 2014）直接在 SMBO 程序內處理知識遷移。然而，迄今它們仍侷限於僅含少數連續超參數與少量元特徵的小規模問題。與 MI-SMBO 相比，它們依賴特定的 SMBO 實作，無法直接套用於現成的超參數最佳化器。

---

> [!quote] Original
> Our method's generality opens several avenues for future work. Here, we evaluated MI-SMBO on small and medium-sized hyperparameter optimiziation problems, and an important open research question is to extend it to even larger configuration spaces, such as those of Auto-WEKA (Thornton et al. 2013) and Hyperopt-Sklearn (Komer, Bergstra, and Eliasmith 2014). We also plan to extend collaborative SMBO methods to overcome their limitation to small-scale problems. Finally, it would be interesting to extend our work to general algorithm configuration (Hutter, Hoos, and Leyton-Brown 2011) and to the life-long learning setting (Gagliolo and Schmidhuber 2005; Hutter and Hamadi 2005; Arbelaez, Hamadi, and Sebag 2010).

> [!note] 翻譯
> 本方法的通用性為未來工作開啟了多條途徑。本文在中小型超參數最佳化問題上評估了 MI-SMBO，一個重要的開放研究問題是將其延伸至更大的配置空間，例如 Auto-WEKA（Thornton et al. 2013）與 Hyperopt-Sklearn（Komer, Bergstra, and Eliasmith 2014）的配置空間。我們也計畫擴展協作式 SMBO 方法，以克服其僅適用於小規模問題的限制。最後，將本研究延伸至一般演算法組態（general algorithm configuration）（Hutter, Hoos, and Leyton-Brown 2011）以及終身學習（life-long learning）情境（Gagliolo and Schmidhuber 2005; Hutter and Hamadi 2005; Arbelaez, Hamadi, and Sebag 2010），亦將是饒富興味的方向。

---

## Conclusion | 結論

> [!quote] Original
> We have presented a simple, yet effective, method for improving Sequential Model-based Bayesian Optimization (SMBO) by leveraging knowledge from previous optimization runs. Our method combines SMBO with configurations suggested by a meta-learning procedure. It is agnostic of the actual SMBO method used and can thus be applied to the method best suited for a particular problem.
>
> We demonstrated MI-SMBO's efficacy by improving the initialization of two SMBO methods on a collection of 57 datasets. For a low-dimensional hyperparameter optimization problem, for small optimization budgets MI-Spearmint improved upon the current state of the art algorithm Spearmint. For a large configuration space describing a CASH problem in scikit-learn, MI-SMAC substantially improved over the current state of the art CASH algorithm SMAC (and all other tested optimizers), showing the potential of our approach especially for large-scale hyperparameter optimization.

> [!note] 翻譯
> 我們提出了一種簡單而有效的方法，透過善用先前最佳化執行所累積的知識來改進序列式模型貝葉斯最佳化（SMBO）。本方法將 SMBO 與元學習程序所建議的配置相結合；它對實際採用的 SMBO 方法是不可知的，因此可套用於最適合特定問題的方法。
>
> 我們在 57 個資料集的集合上，藉由改進兩種 SMBO 方法的初始化，展示了 MI-SMBO 的效用。對低維超參數最佳化問題，在小型最佳化預算下，MI-Spearmint 優於當前最先進的演算法 Spearmint。對描述 scikit-learn CASH 問題的大型配置空間，MI-SMAC 大幅超越了當前最先進的 CASH 演算法 SMAC（以及所有其他受測最佳化器），顯示本方法的潛力，尤其是在大規模超參數最佳化方面。

---

## Acknowledgments | 致謝

> [!quote] Original
> This work was supported by the German Research Foundation (DFG) under grant HU 1900/3-1.

> [!note] 翻譯
> 本研究由德國研究基金會（DFG）資助，計畫編號 HU 1900/3-1。

---

## References | 參考文獻

> References omitted / 參考文獻略
