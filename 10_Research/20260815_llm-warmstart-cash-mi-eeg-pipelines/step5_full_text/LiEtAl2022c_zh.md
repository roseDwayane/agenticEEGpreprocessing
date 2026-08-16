---
citation_key: "LiEtAl2022c"
title: "TransBO: Hyperparameter Optimization via Two-Phase Transfer Learning"
authors: "Yang Li; Yang Li; Yu Shen; Huaijun Jiang; Wentao Zhang; Zhi Yang; Ce Zhang; Bin Cui"
year: 2022
doi: "10.1145/3534678.3539255"
source: "arXiv (2206.02663)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "2206.02663"
conversion: "pdftotext -layout (automated)"
language: "bilingual-en-zh"
---

# TransBO: Hyperparameter Optimization via Two-Phase Transfer Learning | TransBO：基於兩階段遷移學習的超參數最佳化

> [!abstract] 重點摘要
> - 提出 TransBO：一種新穎的兩階段遷移學習（transfer learning, TL）框架，用於超參數最佳化（hyperparameter optimization, HPO），同時處理兩大挑戰——來源任務間的互補性（complementary nature）與知識彙整過程中的動態性（dynamics，避免負遷移）。
> - 第一階段以權重 w 將多個來源基底代理模型（base surrogates）聯合組合為單一來源代理模型 M^S；第二階段以權重 p 將 M^S 與目標代理模型 M^T 自適應地整合為最終遷移學習代理模型 M^TL。
> - 兩組權重皆以「有原則」的方式學得：以可微分成對排名損失（differentiable pairwise ranking loss）構成約束最佳化問題求解（SQP），並藉交叉驗證機制學習 p 以最大化泛化能力；p^T 施加非遞減先驗，理論上可在足夠試驗次數下避免負遷移。
> - 建立並公開大規模 TL 基準：4 種 ML 演算法（Random Forest、Extra Trees、Adaboost、LightGBM）× 30 個 OpenML 資料集，每組 2 萬個配置，共逾 180 萬次模型評估、耗費逾 20 萬 CPU 小時。
> - 靜態 TL 實驗中 TransBO 在不同基準與不同來源任務數（29 與 5）下均表現穩定且優於 SCoT、SGPR、POGPE、TST(-M)、RGPE 等基線；動態 TL 實驗中在 30 個調參任務裡取得最多的前二名成績。
> - 應用於神經架構搜尋（NAS-Bench-201，由 CIFAR-10/100 遷移至 ImageNet16-120）時，相較最先進 NAS 方法（BO、REA）達成逾 5 倍加速；複雜度 O(kn³) 亦優於單一代理模型合併法的 O(k³n³)。

---

## Abstract | 摘要

> [!quote] Original
> With the extensive applications of machine learning models, automatic hyperparameter optimization (HPO) has become increasingly important. Motivated by the tuning behaviors of human experts, it is intuitive to leverage auxiliary knowledge from past HPO tasks to accelerate the current HPO task. In this paper, we propose TransBO, a novel two-phase transfer learning framework for HPO, which can deal with the complementary nature among source tasks and dynamics during knowledge aggregation issues simultaneously. This framework extracts and aggregates source and target knowledge jointly and adaptively, where the weights can be learned in a principled manner. The extensive experiments, including static and dynamic transfer learning settings and neural architecture search, demonstrate the superiority of TransBO over the state-of-the-arts.

> [!note] 翻譯
> 隨著機器學習模型的廣泛應用，自動超參數最佳化（hyperparameter optimization, HPO）變得日益重要。受人類專家調參行為的啟發，利用過去 HPO 任務的輔助知識來加速當前 HPO 任務是一種直觀的做法。本文提出 TransBO——一種新穎的兩階段遷移學習（transfer learning）框架，能夠同時處理來源任務間的互補性與知識彙整過程中的動態性這兩項議題。此框架以聯合且自適應的方式抽取並彙整來源與目標知識，其中的權重可以有原則的（principled）方式學習得到。大量實驗（涵蓋靜態與動態遷移學習設定以及神經架構搜尋）證明了 TransBO 相對於最先進方法的優越性。

---

## 1 Introduction | 引言

> [!quote] Original
> Machine learning (ML) models have been extensively applied in many fields such as recommendation, computer vision, financial market analysis, etc [6, 14–18]. However, the performance of ML models heavily depends on the choice of hyperparameter configurations (e.g., learning rate or the number of hidden layers in a deep neural network). As a result, automatically tuning the hyperparameters has attracted lots of interest from both academia and industry [59]. Bayesian optimization (BO) is one of the most prevailing frameworks for automatic hyperparameter optimization (HPO) [4, 20, 48]. The main idea of BO is to use a surrogate model, typically a Gaussian Process (GP) [42], to describe the relationship between a hyperparameter configuration and its performance (e.g., validation error), and then utilize this surrogate to determine the next configuration to evaluate by optimizing an acquisition function that balances exploration and exploitation.
>
> Hyperparameter optimization (HPO) is often a computationally-intensive process as one often needs to choose and evaluate hyperparameter configurations by training and validating the corresponding ML models. However, for ML models that are computationally expensive to train (e.g., deep learning models or models trained on large-scale datasets), vanilla Bayesian optimization (BO) suffers from the low-efficiency issue [9, 31, 33] due to insufficient configuration evaluations within a limited budget.

> [!note] 翻譯
> 機器學習（machine learning, ML）模型已廣泛應用於推薦系統、電腦視覺、金融市場分析等諸多領域 [6, 14–18]。然而，ML 模型的效能高度依賴於超參數配置的選擇（例如學習率，或深度神經網路的隱藏層數）。因此，自動調校超參數吸引了學術界與產業界的大量關注 [59]。貝葉斯最佳化（Bayesian optimization, BO）是自動超參數最佳化（HPO）最主流的框架之一 [4, 20, 48]。BO 的主要思想是使用代理模型（surrogate model）——通常為高斯過程（Gaussian Process, GP）[42]——來描述超參數配置與其效能（例如驗證誤差）之間的關係，然後利用此代理模型，透過最佳化在探索（exploration）與利用（exploitation）之間取得平衡的擷取函數（acquisition function），決定下一個要評估的配置。
>
> HPO 通常是計算密集的過程，因為往往需要藉由訓練與驗證相應的 ML 模型來選擇並評估超參數配置。然而，對於訓練成本高昂的 ML 模型（例如深度學習模型，或在大規模資料集上訓練的模型），原生的（vanilla）BO 因在有限預算內無法進行足夠多的配置評估，而受低效率問題所困 [9, 31, 33]。

---

> [!quote] Original
> (Opportunities) Production ML models usually need to be constantly re-tuned as new task / dataset comes or underlying code bases are updated, e.g., in the AutoML applications. The optimal hyperparameters may also change as the data and code change, and so should be frequently re-optimized. Although they may change significantly, the region of good or bad configurations may still share some correlation with those of previous tasks [60], and this provides the opportunities towards a faster hyperparameter search. Therefore, we can leverage the tuning results (i.e., observations) from previous HPO tasks (source tasks) to speed up the current HPO task (target task) via a transfer learning-based framework.

> [!note] 翻譯
> （機會）生產環境中的 ML 模型通常需要隨新任務／新資料集的到來或底層程式碼庫的更新而不斷重新調參，例如在 AutoML 應用中。最佳超參數也可能隨資料與程式碼的變化而改變，因此應當被頻繁地重新最佳化。儘管最佳超參數可能顯著變動，好配置或壞配置所在的區域仍可能與先前任務存在某種相關性 [60]，這便為更快的超參數搜尋提供了機會。因此，我們可以透過基於遷移學習的框架，利用先前 HPO 任務（來源任務，source tasks）的調參結果（即觀測值），來加速當前的 HPO 任務（目標任務，target task）。

---

> [!quote] Original
> (Challenges) The transfer learning for HPO consists of two key operations: extracting source knowledge from previous HPO tasks, and aggregating and transfering these knowledge to a target domain. To fully unleash the potential of TL, we need to address two main challenges when performing the above operations: 1) The Complementary Nature among Source Tasks. Different source tasks are often complementary and thus require us to treat them in a joint and cooperative manner. Ignoring the synergy of multiple source tasks might lead to the loss of auxiliary knowledge. 2) Dynamics during Knowledge Aggregation. At the beginning of HPO, the knowledge from the source tasks could bring benefits due to the scarcity of observations on the target task. However, as the tuning process proceeds, we should shift the focus to the target task. Since the target task gets more observations, transferring from source tasks might not be necessary anymore considering the bias and noises in the source tasks (i.e., negative transfer [36]). Existing methods [11, 46, 58] have been focusing on these two challenges. However, none of them considers both simultaneously. This motivates our work, which aims at developing a transfer learning framework that could 1) extract source knowledge in a cooperative manner, and 2) transfer the auxiliary knowledge in an adaptive way.

> [!note] 翻譯
> （挑戰）HPO 的遷移學習包含兩個關鍵操作：從先前的 HPO 任務中抽取來源知識，以及將這些知識彙整並遷移至目標領域。要充分釋放遷移學習的潛力，我們在執行上述操作時必須解決兩個主要挑戰：1）來源任務間的互補性。不同來源任務往往彼此互補，因此需要以聯合且合作的方式對待它們；忽視多個來源任務之間的協同作用可能導致輔助知識的流失。2）知識彙整過程中的動態性。在 HPO 初期，由於目標任務上的觀測值稀缺，來自來源任務的知識能帶來助益；然而隨著調參過程推進，我們應將焦點轉移至目標任務。當目標任務累積了更多觀測值後，考量來源任務中的偏差與雜訊（即負遷移，negative transfer [36]），從來源任務進行遷移可能已無必要。既有方法 [11, 46, 58] 分別關注這兩項挑戰，但沒有任何一種方法同時考慮兩者。這促成了本研究的動機——發展一個遷移學習框架，能夠：1）以合作的方式抽取來源知識；2）以自適應的方式遷移輔助知識。

---

> [!quote] Original
> In this paper, we propose TransBO, a novel two-phase transfer learning framework for automatic HPO that tries to address the above two challenges simultaneously. TransBO works under the umbrella of Bayesian optimization and designs a transfer learning (TL) surrogate to guide the HPO process. This framework decouples the process of knowledge transfer into two phases and considers the knowledge extraction and knowledge aggregation separately in each phase (See Figure 1). In Phase one, TransBO builds a source surrogate that extracts and combines useful knowledge across multiple source tasks. In Phase two, TransBO integrates the source surrogate (in Phase one) and the target surrogate to construct the final surrogate, which we refer to as the transfer learning surrogate. To maximize the generalization of the transfer learning surrogate, we adopt the cross-validation mechanism to learn the transfer learning surrogate in a principled manner. Moreover, instead of combining base surrogates with independent weights, TransBO can learn the optimal aggregation weights for base surrogates jointly. To this end, we propose to learn the weights in each phase by solving a constrained optimization problem with a differentiable ranking loss function.
>
> The empirical results of static TL scenarios showcase the stability and effectiveness of TransBO compared with state-of-the-art TL methods for HPO. In dynamic TL scenarios that are close to real-world applications, TransBO obtains strong performance – the top-2 results on 22.25 out of 30 tuning tasks (Practicality). In addition, when applying TransBO to neural architecture search (NAS), it achieves more than 5× speedups than the state-of-the-art NAS approaches (Universality).

> [!note] 翻譯
> 本文提出 TransBO——一種用於自動 HPO 的新穎兩階段遷移學習框架，試圖同時解決上述兩項挑戰。TransBO 在貝葉斯最佳化的框架之下運作，並設計一個遷移學習（TL）代理模型來引導 HPO 過程。此框架將知識遷移的過程解耦為兩個階段，在各階段分別考慮知識抽取與知識彙整（見 Figure 1）。在第一階段，TransBO 建立一個來源代理模型（source surrogate），跨多個來源任務抽取並組合有用的知識。在第二階段，TransBO 將（第一階段的）來源代理模型與目標代理模型（target surrogate）整合，構建最終的代理模型，我們稱之為遷移學習代理模型（transfer learning surrogate）。為了最大化遷移學習代理模型的泛化能力，我們採用交叉驗證機制，以有原則的方式學習該代理模型。此外，TransBO 不採用彼此獨立的權重來組合基底代理模型（base surrogates），而是能聯合學習基底代理模型的最佳彙整權重。為此，我們提出在每個階段透過求解一個帶可微分排名損失函數（differentiable ranking loss function）的約束最佳化問題來學習權重。
>
> 靜態 TL 情境的實證結果展示了 TransBO 相較於最先進 HPO 遷移學習方法的穩定性與有效性。在貼近真實應用的動態 TL 情境中，TransBO 取得強勁表現——在 30 個調參任務中有 22.25 個取得前二名的成績（實用性）。此外，將 TransBO 應用於神經架構搜尋（neural architecture search, NAS）時，其相較最先進 NAS 方法達成逾 5 倍加速（通用性）。

---

> [!quote] Original
> (Contributions) In this work, our main contributions are summarized as follows:
> - We present a novel two-phase transfer learning framework for HPO — TransBO, which could address the aforementioned challenges simultaneously.
> - We formulate the learning of this two-phase framework into constrained optimization problems. By solving these problems, TransBO could extract and aggregate the source and target knowledge in a joint and adaptive manner.
> - To facilitate transfer learning research for HPO, we create and publish a large-scale benchmark, which takes more than 200K CPU hours and involves more than 1.8 million model evaluations.
> - The extensive experiments, including static and dynamic TL settings and neural architecture search, demonstrate the superiority of TransBO over state-of-the-art methods.

> [!note] 翻譯
> （貢獻）本研究的主要貢獻總結如下：
> - 我們提出一種新穎的 HPO 兩階段遷移學習框架——TransBO，能夠同時解決前述挑戰。
> - 我們將此兩階段框架的學習過程形式化為約束最佳化問題。透過求解這些問題，TransBO 得以聯合且自適應地抽取並彙整來源與目標知識。
> - 為促進 HPO 遷移學習研究，我們建立並公開一個大規模基準，其建置耗費逾 20 萬 CPU 小時，涉及逾 180 萬次模型評估。
> - 涵蓋靜態與動態 TL 設定以及神經架構搜尋的大量實驗，證明了 TransBO 相對於最先進方法的優越性。

---

## 2 Related Work | 相關工作

> [!quote] Original
> Bayesian optimization (BO) has been successfully applied to hyperparameter optimization (HPO) [5, 29, 30, 32]. For ML models that are computationally expensive to train (e.g., deep learning models or models trained on large datasets), BO methods [4, 20, 48] suffer from the low-efficiency issue due to insufficient configuration evaluations within a limited budget. To speed up HPO of ML algorithms with limited trials, recent BO methods extend the traditional black-box assumption by exploiting cheaper fidelities from the current task [9, 23, 26, 27, 30, 31, 41, 52]. Orthogonal to these methods, we focus on borrowing strength from previously finished tasks to accelerate the HPO of the current task.

> [!note] 翻譯
> 貝葉斯最佳化已成功應用於超參數最佳化 [5, 29, 30, 32]。對於訓練成本高昂的 ML 模型（例如深度學習模型，或在大型資料集上訓練的模型），BO 方法 [4, 20, 48] 因在有限預算內配置評估次數不足而效率低落。為了在有限試驗次數下加速 ML 演算法的 HPO，近期的 BO 方法透過利用當前任務中較廉價的保真度（fidelities），擴展了傳統的黑盒假設 [9, 23, 26, 27, 30, 31, 41, 52]。與這些方法正交（orthogonal），我們專注於借力已完成的先前任務，以加速當前任務的 HPO。

---

> [!quote] Original
> Transfer learning (TL) methods for HPO aim to leverage auxiliary knowledge from previous tasks to achieve faster optimization on the target task. One common way is to learn surrogate models from past tuning history and use them to guide the search of hyperparameters. For instance, several methods learn all available information from both source and target tasks in a single surrogate, and make the data comparable through a transfer stacking ensemble [38], a ranking algorithm [1], multi-task GPs [51], a mixed kernel GP [60], the GP noisy model [22], a multi-layer perceptron with Bayesian linear regression heads [39, 49] or replace GP with Bayesian neural networks [50]. SGPR [13] and SMFO [57] utilize the knowledge from all source tasks equally and thus suffer from performance deterioration when the knowledge of source tasks is not applicable to the target task. FMLP [45] uses multi-layer perceptrons as the surrogate model that learns the interaction between hyperparameters and datasets. SCoT [1] and MKL-GP [60] fit a GP-based surrogate on merged observations from both source tasks and target task. To distinguish the varied performance of the same configuration on different tasks, the two methods use the meta-features of datasets to represent the tasks; while the meta-features are often unavailable for broad classes of HPO problems [11]. Due to the high computational complexity of GP (O(n³)), it is difficult for these methods to scale to a large number of source tasks and trials (scalability bottleneck).

> [!note] 翻譯
> HPO 的遷移學習方法旨在利用先前任務的輔助知識，在目標任務上實現更快的最佳化。常見做法之一是從過去的調參歷史中學習代理模型，並以之引導超參數的搜尋。例如，有數種方法將來源任務與目標任務的所有可用資訊學習於單一代理模型之中，並透過遷移堆疊集成（transfer stacking ensemble）[38]、排名演算法 [1]、多任務高斯過程（multi-task GPs）[51]、混合核 GP [60]、GP 雜訊模型 [22]、帶貝葉斯線性迴歸頭的多層感知機 [39, 49]，或以貝葉斯神經網路取代 GP [50] 等方式使資料可比較。SGPR [13] 與 SMFO [57] 均等地利用所有來源任務的知識，因此當來源任務的知識不適用於目標任務時，會出現效能劣化。FMLP [45] 以多層感知機作為代理模型，學習超參數與資料集之間的交互作用。SCoT [1] 與 MKL-GP [60] 則在來源任務與目標任務合併後的觀測值上擬合基於 GP 的代理模型。為了區分同一配置在不同任務上的差異表現，這兩種方法以資料集的元特徵（meta-features）來表徵任務；然而對許多類型的 HPO 問題而言，元特徵往往無法取得 [11]。由於 GP 的高計算複雜度（O(n³)），這些方法難以擴展至大量的來源任務與試驗次數（可擴展性瓶頸）。

---

> [!quote] Original
> To improve scalability, recent methods adopt the ensemble framework to conduct TL for HPO, where they train a base surrogate on each source task and the target task respectively and then combine all base surrogates into an ensemble surrogate with different weights. This framework ignores the two aforementioned issues and uses the independent weights. POGPE [46] sets the weights of base surrogates to constants. TST [58] linearly combines the base surrogates with a Nadaraya-Watson kernel weighting by defining a distance metric across tasks; the weights are calculated by using either meta-features (TST-M) or pairwise hyperparameter configuration rankings (TST-R). RGPE [11] uses the probability that the base surrogate has the lowest ranking loss on the target task to estimate the weights. Instead of resorting to heuristics, TransBO propose to learn the joint weights in a principled way.

> [!note] 翻譯
> 為改善可擴展性，近期方法採用集成（ensemble）框架來進行 HPO 的遷移學習：分別在每個來源任務與目標任務上訓練一個基底代理模型，再以不同權重將所有基底代理模型組合為一個集成代理模型。此框架忽略了前述兩項議題，並採用彼此獨立的權重。POGPE [46] 將基底代理模型的權重設為常數。TST [58] 透過定義跨任務的距離度量，以 Nadaraya-Watson 核加權線性組合基底代理模型；權重的計算可基於元特徵（TST-M）或成對超參數配置排名（TST-R）。RGPE [11] 以「基底代理模型在目標任務上具有最低排名損失」的機率來估計權重。TransBO 則不訴諸啟發式方法，而是提出以有原則的方式學習聯合權重。

---

> [!quote] Original
> Warm-starting methods [25, 34] select several initial hyperparameter configurations as the start points of search procedures. Salinas et al. [44] deal with the heterogeneous scale between tasks with the Gaussian Copula Process. ABRAC [19] proposes a multi-task BO method with adaptive complexity to prevent over-fitting on scarce target observations. TNP [55] applies the neural process to jointly transfer surrogates, parameters, and initial configurations. Recently, transferring search space has become another way for applying transfer learning in HPO. Wistuba et al. [56] prune the bad regions of search space according to the results from previous tasks. This method suffers from the complexity of obtaining meta-features and relies on some other parameters to construct a GP model. On that basis, Perrone et al. [40] propose to utilize previous tasks to design a sub-region of the entire search space for the new task. While sharing some common spirits, these methods are orthogonal and complementary to our surrogate transfer method introduced in this paper.
>
> In addition, our proposed two-phase framework inherits the advantages of the bi-level optimization [2]. While previous methods in the literature focus on different tasks (e.g., evolutionary computation [47]), to the best of our knowledge, TransBO is the first method that adopts the concept of bi-level optimization into hyperparameter transfer learning.

> [!note] 翻譯
> 暖啟動（warm-starting）方法 [25, 34] 選取數個初始超參數配置作為搜尋程序的起點。Salinas et al. [44] 以高斯 Copula 過程（Gaussian Copula Process）處理任務間的異質尺度問題。ABRAC [19] 提出一種具自適應複雜度的多任務 BO 方法，以防止在稀缺的目標觀測值上過度擬合。TNP [55] 應用神經過程（neural process）聯合遷移代理模型、參數與初始配置。近來，遷移搜尋空間成為在 HPO 中應用遷移學習的另一途徑。Wistuba et al. [56] 依據先前任務的結果修剪搜尋空間中的不良區域；此方法受限於取得元特徵的複雜性，且依賴其他一些參數來建構 GP 模型。在此基礎上，Perrone et al. [40] 提出利用先前任務為新任務設計整體搜尋空間的一個子區域。這些方法雖與本文精神有所相通，但與我們所提出的代理模型遷移方法彼此正交且互補。
>
> 此外，我們提出的兩階段框架繼承了雙層最佳化（bi-level optimization）[2] 的優點。文獻中先前的方法聚焦於不同的課題（例如演化計算 [47]），而據我們所知，TransBO 是首個將雙層最佳化概念引入超參數遷移學習的方法。

---

## 3 Bayesian Hyperparameter Optimization | 貝葉斯超參數最佳化

> [!quote] Original
> The HPO of ML algorithms can be modeled as a black-box optimization problem. The goal is to find argmin_{x∈X} f(x) in hyperparameter space X, where f(x) is the ML model's performance metric (e.g., validation error) corresponding to the configuration x. Due to the intrinsic randomness of most ML algorithms, we evaluate configuration x and can only get its noisy result y = f(x) + ε with ε ∼ N(0, σ²).
>
> Bayesian optimization (BO) is a model-based framework for HPO. BO first fits a probabilistic surrogate model M : p(f|D) on the already observed instances D = {(x₁, y₁), ..., (x_{n−1}, y_{n−1})}. In the n-th iteration, BO iterates the following steps: 1) use surrogate M to select a promising configuration xₙ that maximizes the acquisition function xₙ = arg max_{x∈X} a(x; M), where the acquisition function is to balance the exploration and exploitation trade-off; 2) evaluate this point to get its performance yₙ, and add the new observation (xₙ, yₙ) to D; 3) refit M on the augmented D. Expected Improvement (EI) [21] is a common acquisition function defined as follows:
>
> a(x; M) = ∫₋∞^∞ max(y* − y, 0) p_M(y|x) dy,  (1)
>
> where M is the surrogate and y* = min{y₁, ..., yₙ}. By maximizing this EI function a(x; M) over X, BO methods can find a configuration to evaluate for each iteration.

> [!note] 翻譯
> ML 演算法的 HPO 可建模為黑盒最佳化問題。目標是在超參數空間 X 中尋找 argmin_{x∈X} f(x)，其中 f(x) 是配置 x 對應的 ML 模型效能指標（例如驗證誤差）。由於多數 ML 演算法的內在隨機性，評估配置 x 只能得到帶雜訊的結果 y = f(x) + ε，其中 ε ∼ N(0, σ²)。
>
> 貝葉斯最佳化是一種基於模型的 HPO 框架。BO 首先在已觀測的實例 D = {(x₁, y₁), ..., (x_{n−1}, y_{n−1})} 上擬合機率代理模型 M : p(f|D)。在第 n 次迭代中，BO 反覆執行以下步驟：1）利用代理模型 M 選出使擷取函數最大化的有潛力配置 xₙ = arg max_{x∈X} a(x; M)，其中擷取函數用於平衡探索與利用之間的取捨；2）評估該點以取得其效能 yₙ，並將新觀測 (xₙ, yₙ) 加入 D；3）在擴增後的 D 上重新擬合 M。期望改進量（Expected Improvement, EI）[21] 是常用的擷取函數，定義如下：
>
> a(x; M) = ∫₋∞^∞ max(y* − y, 0) p_M(y|x) dy,  (1)
>
> 其中 M 為代理模型，y* = min{y₁, ..., yₙ}。透過在 X 上最大化此 EI 函數 a(x; M)，BO 方法便能在每次迭代中找到一個要評估的配置。

---

## 4 The Proposed Method | 所提出的方法

> [!quote] Original
> In this section, we present TransBO, a two-phase transfer learning (TL) framework for HPO. Before diving into the proposed framework, we first introduce the notations and settings for TL. Then we describe TransBO in details and end the section with discussions about its advantages.
>
> **Basic Notations and Settings.** As illustrated in Figure 1, we denote observations from K+1 tasks as D¹, ..., D^K for K source tasks and D^T for the target task. The i-th source task has nᵢ configuration observations: Dⁱ = {(xⁱⱼ, yⁱⱼ)} for j = 1..nᵢ with i = 1, 2, ..., K, which are obtained from previous tuning procedures. For the target task, after completing t iterations (trials), the observations in the target task are: D^T = {(x^T_j, y^T_j)} for j = 1..t.
>
> Before optimization, we train a base surrogate model for the i-th source task, denoted by Mⁱ. Each base surrogate Mⁱ can be fitted on Dⁱ in advance (offline), and the target surrogate M^T is trained on D^T on the fly. Since the configuration performance ys in each Dⁱ and D^T may have different numerical ranges, we standardize the ys in each task by removing the mean and scaling to unit variance. For a hyperparameter configuration xⱼ, each base surrogate Mⁱ outputs a posterior predictive distribution at xⱼ, that's, Mⁱ(xⱼ) ∼ N(μ_{Mⁱ}(xⱼ), σ²_{Mⁱ}(xⱼ)). For brevity, we denote the mean of this prediction at xⱼ as Mⁱ(xⱼ) = μ_{Mⁱ}(xⱼ).
>
> [Figure 1: Two-Phase Transfer Learning Framework — 來源任務的 HPO 歷史 D¹...D^K 訓練出來源基底代理模型 M¹...M^K，於第一階段彙整為來源代理模型 M^S；第二階段將 M^S 與目標代理模型 M^T 彙整為 TL 代理模型 M^TL，用以選擇並評估下一輪配置。]

> [!note] 翻譯
> 本節介紹 TransBO——一種 HPO 的兩階段遷移學習框架。在深入所提框架之前，我們先介紹遷移學習的符號與設定，接著詳細描述 TransBO，最後以其優勢的討論作結。
>
> **基本符號與設定。** 如 Figure 1 所示，我們將來自 K+1 個任務的觀測值記為：K 個來源任務的 D¹, ..., D^K，以及目標任務的 D^T。第 i 個來源任務有 nᵢ 筆配置觀測：Dⁱ = {(xⁱⱼ, yⁱⱼ)}（j = 1..nᵢ，i = 1, 2, ..., K），這些觀測來自先前的調參程序。對於目標任務，完成 t 次迭代（試驗）後，目標任務的觀測為 D^T = {(x^T_j, y^T_j)}（j = 1..t）。
>
> 在最佳化開始前，我們為第 i 個來源任務訓練一個基底代理模型，記為 Mⁱ。每個基底代理模型 Mⁱ 皆可事先（離線）在 Dⁱ 上擬合，而目標代理模型 M^T 則在 D^T 上即時訓練。由於各 Dⁱ 與 D^T 中的配置效能 y 可能具有不同的數值範圍，我們對每個任務中的 y 進行標準化——去除均值並縮放至單位變異數。對於超參數配置 xⱼ，每個基底代理模型 Mⁱ 在 xⱼ 處輸出一個後驗預測分布，即 Mⁱ(xⱼ) ∼ N(μ_{Mⁱ}(xⱼ), σ²_{Mⁱ}(xⱼ))。為求簡潔，我們將該預測在 xⱼ 處的均值記為 Mⁱ(xⱼ) = μ_{Mⁱ}(xⱼ)。
>
> [Figure 1：兩階段遷移學習框架——來源任務的 HPO 歷史 D¹...D^K 訓練出來源基底代理模型 M¹...M^K，於第一階段彙整為來源代理模型 M^S；第二階段將 M^S 與目標代理模型 M^T 彙整為 TL 代理模型 M^TL，用以選擇並評估下一輪配置。]

---

### 4.1 Overview | 概覽

> [!quote] Original
> TransBO aims to build a transfer learning surrogate model M^TL on the target task, which outputs a more accurate prediction for each configuration by borrowing strength from the source tasks. The cornerstone of TransBO is to decouple the combination of K + 1 base surrogates with a novel two-phase framework:
>
> Phase 1. To leverage the complementary nature among source tasks, TransBO first linearly combines all source base surrogates into a single source surrogate with the weights w: M^S = agg({M¹, ..., M^K}; w). In this phase, the useful source knowledge from each source task is extracted and integrated into the source surrogate in a joint and cooperative manner.
>
> Phase 2. To support dynamics-aware knowledge aggregation, TransBO further combines the aggregated source surrogate with the target surrogate M^T via weights p in an adaptive manner, where M^T is trained on the target observations D^T: M^TL = agg({M^S, M^T}; p).
>
> Such joint and adaptive knowledge transfer in two phases guarantees the efficiency and effectiveness of the final TL surrogate M^TL in extracting and integrating the source and target knowledge. To maximize the generalization ability of M^TL, the two-phase framework further learns the parameters w and p in a principled and automatic manner by solving the constrained optimization problems. In the following, we describe the parameter learning and aggregation method.

> [!note] 翻譯
> TransBO 旨在於目標任務上建立遷移學習代理模型 M^TL，藉由向來源任務「借力」，對每個配置輸出更準確的預測。TransBO 的基石在於以一個新穎的兩階段框架，將 K + 1 個基底代理模型的組合過程解耦：
>
> 第一階段。為善用來源任務間的互補性，TransBO 首先以權重 w 將所有來源基底代理模型線性組合為單一的來源代理模型：M^S = agg({M¹, ..., M^K}; w)。在此階段，各來源任務中有用的來源知識以聯合且合作的方式被抽取並整合進來源代理模型。
>
> 第二階段。為支援具動態感知的知識彙整，TransBO 進一步以權重 p、以自適應的方式，將彙整後的來源代理模型與目標代理模型 M^T 結合，其中 M^T 是在目標觀測值 D^T 上訓練的：M^TL = agg({M^S, M^T}; p)。
>
> 這種分兩階段、聯合且自適應的知識遷移，保證了最終 TL 代理模型 M^TL 在抽取與整合來源及目標知識上的效率與有效性。為最大化 M^TL 的泛化能力，此兩階段框架進一步透過求解約束最佳化問題，以有原則且自動的方式學習參數 w 與 p。以下描述參數學習與彙整方法。

---

### 4.2 Parameter Learning in Two-Phase Framework | 兩階段框架中的參數學習

> [!quote] Original
> Notice that w and p play different roles — w combines K source base surrogates to best fit the target observations, while p balances between two surrogates M^S and M^T. The objective of TransBO is to maximize the generalization performance of M^TL. To obtain w, we use the target observations D^T to maximize the performance of source surrogate M^S. However, if we learn the parameter p of M^TL on D^T by using the M^S and M^T, where M^S and M^T are trained on D^T directly, the learning process becomes an estimation of in-sample error and can not reflect the generalization of the final surrogate M^TL. To address this issue, we adopt the cross-validation mechanism to maximize the generalization ability of M^TL when learning p. In the following, we first describe the general procedure to learn a surrogate M^S on given observations D (instead of D^T), and then introduce the method to learn the parameters w and p, respectively.

> [!note] 翻譯
> 注意 w 與 p 扮演不同的角色——w 組合 K 個來源基底代理模型以最佳擬合目標觀測值，而 p 在兩個代理模型 M^S 與 M^T 之間取得平衡。TransBO 的目標是最大化 M^TL 的泛化表現。為求得 w，我們利用目標觀測值 D^T 來最大化來源代理模型 M^S 的表現。然而，若在 D^T 上直接以（同樣在 D^T 上訓練的）M^S 與 M^T 來學習 M^TL 的參數 p，則此學習過程將淪為樣本內誤差（in-sample error）的估計，無法反映最終代理模型 M^TL 的泛化能力。為解決此問題，我們在學習 p 時採用交叉驗證機制，以最大化 M^TL 的泛化能力。以下先描述在給定觀測值 D（而非 D^T）上學習代理模型 M^S 的一般程序，再分別介紹學習參數 w 與 p 的方法。

---

> [!quote] Original
> **General Procedure: Fitting M^S on Given Observations D.** Our strategy is to obtain the source surrogate M^S as a weighted combination of the predictions of source base surrogates {M¹, ..., M^K}:
>
> M^S(x) = Σᵢ₌₁^K wᵢ Mⁱ(x),  (2)
>
> where Σᵢ wᵢ = 1 and wᵢ ∈ [0, 1]. Intuitively, the weight wᵢ reflects the quality of knowledge extracted from the corresponding source tasks. Instead of calculating weights independently, which may ignore the complementary nature among source tasks, we propose to combine source base surrogates Mⁱs in a joint and supervised manner, which reveals their cooperative contributions to M^S.
>
> To derive M^S in a principled way, we use a differentiable pairwise ranking loss function to measure the fitting error between the prediction of M^S and the available observations D. In HPO, ranking loss is more appropriate than mean square error — the actual values of predictions are not the most important, and we care more about the partial orders over the hyperparameter space, e.g., the location of the optimal configuration. This ranking loss function is defined as follows:
>
> L(w, M^S; D) = (1/n²) Σⱼ₌₁ⁿ Σ_{k=1, yⱼ<y_k}ⁿ φ(M^S(x_k) − M^S(xⱼ)),  φ(z) = log(1 + e^{−z}),  (3)
>
> where n is the number of observations in D, y is the observed performance of configuration x in D, and the prediction of M^S(xⱼ) at configuration xⱼ is obtained by linearly combining the predictive mean of Mⁱ with a weight wᵢ, that's, M^S(xⱼ) = Σᵢ wᵢ Mⁱ(xⱼ).

> [!note] 翻譯
> **一般程序：在給定觀測值 D 上擬合 M^S。** 我們的策略是將來源代理模型 M^S 取為來源基底代理模型 {M¹, ..., M^K} 預測值的加權組合：
>
> M^S(x) = Σᵢ₌₁^K wᵢ Mⁱ(x),  (2)
>
> 其中 Σᵢ wᵢ = 1 且 wᵢ ∈ [0, 1]。直觀而言，權重 wᵢ 反映了自對應來源任務所抽取知識的品質。我們不採用可能忽略來源任務間互補性的獨立權重計算方式，而是提出以聯合且監督式的方式組合來源基底代理模型 Mⁱ，從而揭示它們對 M^S 的合作性貢獻。
>
> 為以有原則的方式導出 M^S，我們使用可微分的成對排名損失函數（pairwise ranking loss）來衡量 M^S 的預測與可用觀測值 D 之間的擬合誤差。在 HPO 中，排名損失比均方誤差更為合適——預測的實際數值並非最重要，我們更關心超參數空間上的偏序關係（partial orders），例如最佳配置的位置。此排名損失函數定義如下：
>
> L(w, M^S; D) = (1/n²) Σⱼ₌₁ⁿ Σ_{k=1, yⱼ<y_k}ⁿ φ(M^S(x_k) − M^S(xⱼ)),  φ(z) = log(1 + e^{−z}),  (3)
>
> 其中 n 為 D 中的觀測數，y 為配置 x 在 D 中的觀測效能，而 M^S 在配置 xⱼ 處的預測由各 Mⁱ 的預測均值以權重 wᵢ 線性組合而得，即 M^S(xⱼ) = Σᵢ wᵢ Mⁱ(xⱼ)。

---

> [!quote] Original
> We further turn the learning of source surrogate M^S, i.e., the learning of w, into the following constrained optimization problem:
>
> minimize_w L(w, M^S; D)  s.t. 1⊤w = 1, w ≥ 0,  (4)
>
> where the objective is the ranking loss of M^S on D. This optimization objective is continuously differentiable, and concretely, it is twice continuously differentiable. So we can have the first derivative of the objective L as follows:
>
> ∂L/∂w = Σ_{(j,k)∈P} (neg_e^z / (1 + neg_e^z)) · (A[j] − A[k]),  neg_e^z = e^{(A[j]w − A[k]w)},  (5)
>
> where P consists of pairs (j, k) satisfying yⱼ < y_k, A is the matrix formed by putting the predictions of M^{1:K}s together where the element at the i-th row and j-th column is Mⁱ(xⱼ), and A[j] is the row vector in the j-th row of matrix A. Furthermore, this optimization problem can be solved efficiently by applying many existing sequential quadratic programming (SQP) solvers [28].
>
> **Learning Parameter w.** As stated previously, to maximize the (generalization) performance of M^S, we propose to learn the parameter w by fitting M^S on the whole observations D^T. In this way, the useful source knowledge from multiple source tasks can be fully extracted and integrated in a joint manner. Therefore, the parameters w can be obtained by calling the general procedure, i.e., solving the problem 4, where the available observations D are set to D^T.

> [!note] 翻譯
> 我們進一步將來源代理模型 M^S 的學習（即 w 的學習）轉化為以下約束最佳化問題：
>
> minimize_w L(w, M^S; D)  s.t. 1⊤w = 1, w ≥ 0,  (4)
>
> 其中目標函數為 M^S 在 D 上的排名損失。此最佳化目標是連續可微的，具體而言為二次連續可微。因此，目標函數 L 的一階導數如下：
>
> ∂L/∂w = Σ_{(j,k)∈P} (neg_e^z / (1 + neg_e^z)) · (A[j] − A[k]),  neg_e^z = e^{(A[j]w − A[k]w)},  (5)
>
> 其中 P 由滿足 yⱼ < y_k 的配對 (j, k) 構成；A 是將 M^{1:K} 的預測值合併而成的矩陣，其第 i 列第 j 行元素為 Mⁱ(xⱼ)；A[j] 為矩陣 A 第 j 列的列向量。此外，此最佳化問題可透過許多現成的序列二次規劃（sequential quadratic programming, SQP）求解器 [28] 高效求解。
>
> **學習參數 w。** 如前所述，為最大化 M^S 的（泛化）表現，我們提出在全部目標觀測值 D^T 上擬合 M^S 來學習參數 w。如此，多個來源任務中有用的來源知識便能以聯合的方式被充分抽取與整合。因此，參數 w 可透過呼叫一般程序取得——即求解問題 (4)，並將可用觀測值 D 設為 D^T。

---

> [!quote] Original
> **Learning Parameter p.** To reflect the generalization in M^TL, the parameter p is learned with the cross-validation mechanism. We first split D^T into N_cv partitions: D^T_1, ..., D^T_{N_cv} with N_cv = 5. For each partition i ∈ [1 : N_cv], we first fit a partial surrogate M^S_{−i} on the observations D^T_{−i} with observations in the i-th partition removed from D^T, and the surrogate M^S_{−i} is learned on D^T_{−i} using the general procedure; in addition, we also fit a partial surrogate model M^T_{−i} on D^T_{−i} directly. Then we combine the surrogates M^S_{−i} and M^T_{−i} linearly to obtain a M^TL_{−i}:
>
> M^TL_{−i} = p^S M^S_{−i} + p^T M^T_{−i},  (6)
>
> where p = [p^S, p^T]. Therefore, we can obtain N_cv partial surrogates M^S_{−i} and M^T_{−i} with i ∈ [1 : N_cv]. Based on the differentiable pairwise ranking loss function in Eq. 3, the loss of M^TL_{−i} on D^T is defined as:
>
> L_cv(p, M^TL_{−i}; D^T) = (1/n²) Σⱼ₌₁ⁿ Σ_{k=1, y^T_j<y^T_k, k∈D^T_i} φ(z),  φ(z) = log(1 + e^{−z}),  z = M^TL_{−i}(x_k) − M^TL_{−H(j)}(xⱼ),  (7)
>
> where n is the number of observations in D^T, y^T is the observed performance of configuration x^T in D^T, H(j) indicates the partition id that configuration xⱼ belongs to, and the prediction of M^TL_{−i} at configuration x_k is obtained by linearly combining the predictive mean of M^S_{−i} and M^T_{−i} with weight p, that's, M^TL_{−i}(x_k) = p^S M^S_{−i}(x_k) + p^T M^T_{−i}(x_k). So the parameter p can be learned by solving a similar constrained optimization problem on D^T:
>
> minimize_p Σᵢ₌₁^{N_cv} L_cv(p, M^TL_{−i}; D^T)  s.t. 1⊤p = 1, p ≥ 0.  (8)
>
> Following the solution introduced in problem 4, the above optimization problem can be solved efficiently.

> [!note] 翻譯
> **學習參數 p。** 為了在 M^TL 中反映泛化能力，參數 p 以交叉驗證機制學習。我們先將 D^T 切分為 N_cv 個分割：D^T_1, ..., D^T_{N_cv}，其中 N_cv = 5。對每個分割 i ∈ [1 : N_cv]，我們先在 D^T_{−i}（即自 D^T 中移除第 i 個分割之觀測後的觀測值）上擬合部分代理模型 M^S_{−i}，其中 M^S_{−i} 依一般程序在 D^T_{−i} 上學習；此外，我們也直接在 D^T_{−i} 上擬合部分代理模型 M^T_{−i}。接著將 M^S_{−i} 與 M^T_{−i} 線性組合，得到 M^TL_{−i}：
>
> M^TL_{−i} = p^S M^S_{−i} + p^T M^T_{−i},  (6)
>
> 其中 p = [p^S, p^T]。如此可得 N_cv 組部分代理模型 M^S_{−i} 與 M^T_{−i}（i ∈ [1 : N_cv]）。基於式 (3) 的可微分成對排名損失函數，M^TL_{−i} 在 D^T 上的損失定義為：
>
> L_cv(p, M^TL_{−i}; D^T) = (1/n²) Σⱼ₌₁ⁿ Σ_{k=1, y^T_j<y^T_k, k∈D^T_i} φ(z),  φ(z) = log(1 + e^{−z}),  z = M^TL_{−i}(x_k) − M^TL_{−H(j)}(xⱼ),  (7)
>
> 其中 n 為 D^T 中的觀測數，y^T 為配置 x^T 在 D^T 中的觀測效能，H(j) 表示配置 xⱼ 所屬的分割編號，而 M^TL_{−i} 在配置 x_k 處的預測由 M^S_{−i} 與 M^T_{−i} 的預測均值以權重 p 線性組合而得，即 M^TL_{−i}(x_k) = p^S M^S_{−i}(x_k) + p^T M^T_{−i}(x_k)。於是，參數 p 便可透過在 D^T 上求解一個類似的約束最佳化問題來學習：
>
> minimize_p Σᵢ₌₁^{N_cv} L_cv(p, M^TL_{−i}; D^T)  s.t. 1⊤p = 1, p ≥ 0.  (8)
>
> 沿用問題 (4) 所介紹的求解方式，上述最佳化問題可被高效求解。

---

> [!quote] Original
> **Final TL Surrogate.** After w and p are obtained, as illustrated in Figure 1, we first combine the source base surrogates into the source surrogate M^S with w (the Phase 1), and then integrate M^S and M^T with p to obtain the final TL surrogate M^TL (the Phase 2). To ensure the surrogate M^TL still works in the BO framework, it is required to be a GP. How to obtain the unified posterior predictive mean and variance from multiple GPs (base surrogates) is still an open problem. As suggested by [11], the linear combination of multiple base surrogates works well in practice. Therefore, we aggregate the base surrogates with linear combination. That's, suppose there are N_B GP-based surrogates, and each base surrogate M^b has a weight w_b with b = 1, ..., N_B, the combined prediction under the linear combination technique is give by: μ_C(x) = Σ_b w_b μ_b(x) and σ²_C(x) = Σ_b w_b² σ_b²(x).

> [!note] 翻譯
> **最終 TL 代理模型。** 取得 w 與 p 後，如 Figure 1 所示，我們先以 w 將來源基底代理模型組合為來源代理模型 M^S（第一階段），再以 p 整合 M^S 與 M^T，得到最終的 TL 代理模型 M^TL（第二階段）。為確保代理模型 M^TL 仍能在 BO 框架中運作，其須為一個 GP。如何從多個 GP（基底代理模型）得到統一的後驗預測均值與變異數仍是一個開放問題。如 [11] 所建議，多個基底代理模型的線性組合在實務中表現良好，因此我們以線性組合彙整基底代理模型。亦即，假設有 N_B 個基於 GP 的代理模型，且每個基底代理模型 M^b 具有權重 w_b（b = 1, ..., N_B），則線性組合技術下的合併預測為：μ_C(x) = Σ_b w_b μ_b(x)，σ²_C(x) = Σ_b w_b² σ_b²(x)。

---

> [!quote] Original
> **Algorithm Summary.** At initialization, we set the weight of each source surrogate in w to 1/K, and p = [1, 0] when the number of trials is insufficient for cross-validation. Algorithm 1 illustrates the pseudo code of TransBO. In the i-th iteration, we first learn the weights pᵢ and wᵢ by solving two optimization problems (Lines 2-3). Since we have the prior: as the HPO process of the target task proceeds, the target surrogate owns more and more knowledge about the objective function of the target task, therefore the weight of M^T should increase gradually. To this end, we employ a max operator, which enforces that the update of p^T should be non-decreasing (Line 4). Next, by using linear combination, we build the source surrogate M^S with weight wᵢ, and then construct the final TL surrogate M^TL with pᵢ (Line 5). Finally, TransBO utilizes M^TL to choose a promising configuration to evaluate, and refit the target surrogate on the augmented observation (the BO framework, Lines 6-7).
>
> [Algorithm 1: The TransBO Framework — 輸入最大試驗數 N_T、K 個來源任務之觀測 D^{1:K} 與配置空間 X；每次迭代：解 (4) 得 wᵢ、解 (8) 得 pᵢ、對 p^T 施加非遞減先驗 p^T_i = max(p^T_i, p^T_{i−1})、建構 M^S 與 M^TL、依 EI 準則選取並評估配置、擴增 D^T 並重擬合 M^T；最終回傳 D^T 中最佳配置。]

> [!note] 翻譯
> **演算法摘要。** 初始化時，我們將 w 中每個來源代理模型的權重設為 1/K；當試驗次數不足以進行交叉驗證時，設 p = [1, 0]。Algorithm 1 展示了 TransBO 的偽代碼。在第 i 次迭代中，我們先透過求解兩個最佳化問題學習權重 pᵢ 與 wᵢ（第 2–3 行）。由於我們持有以下先驗：隨著目標任務 HPO 過程的推進，目標代理模型對目標任務之目標函數的知識愈來愈多，因此 M^T 的權重應逐漸增加。為此，我們採用 max 運算子，強制 p^T 的更新為非遞減（第 4 行）。接著，利用線性組合，以權重 wᵢ 建構來源代理模型 M^S，再以 pᵢ 建構最終 TL 代理模型 M^TL（第 5 行）。最後，TransBO 利用 M^TL 選出有潛力的配置進行評估，並在擴增後的觀測值上重新擬合目標代理模型（BO 框架，第 6–7 行）。
>
> [Algorithm 1：TransBO 框架——輸入最大試驗數 N_T、K 個來源任務之觀測 D^{1:K} 與配置空間 X；每次迭代：解 (4) 得 wᵢ、解 (8) 得 pᵢ、對 p^T 施加非遞減先驗 p^T_i = max(p^T_i, p^T_{i−1})、建構 M^S 與 M^TL、依 EI 準則選取並評估配置、擴增 D^T 並重擬合 M^T；最終回傳 D^T 中最佳配置。]

---

### 4.3 Discussion: Advantages of TransBO | 討論：TransBO 的優勢

> [!quote] Original
> To our knowledge, TransBO is the first method that conducts transfer learning for HPO in a supervised manner, instead of resorting to some heuristics. In addition, this method owns the following desirable properties simultaneously. 1) Practicality. A practical HPO method should be insensitive to its hyperparameters, and do not depend on meta-features. The goal of HPO is to optimize the ML hyperparameters automatically while having extra (or sensitive) hyperparameters itself actually violates its principle. In addition, many datasets, including image and text data, lack appropriate meta-features to represent the dataset [11, 45, 58]. The construction of TL surrogate in TransBO is insensitive to its hyperparameters and does not require meta-features. 2) Universality. The 1st property enable TransBO to be a general transfer learning framework for Black-box optimizations, e.g., experimental design [12], neural architecture search [8], etc; we include an experiment to evaluate TransBO on the NAS task in the section of experiment. 3) Scalability. Compared with the methods that combine k source tasks with n trials into a single surrogate (O(k³n³)), TransBO has a much lower complexity O(kn³), which means that TransBO could scale to a large number of tasks and trials easily. 4) Theoretical Discussion. TransBO also provides theoretical discussions about preventing the performance deterioration (negative transfer). Base on cross-validation and the non-decreasing constraint, the performance of TransBO, given sufficient trials, will be no worse than the method without transfer learning, while the other methods cannot have this (See Appendix A.4 for more details).

> [!note] 翻譯
> 據我們所知，TransBO 是第一個以監督式方式（而非訴諸啟發式方法）為 HPO 進行遷移學習的方法。此外，本方法同時具備以下理想性質。1）實用性（Practicality）。實用的 HPO 方法應對自身的超參數不敏感，且不依賴元特徵。HPO 的目標是自動最佳化 ML 超參數，若方法本身還帶有額外（或敏感）的超參數，實際上違背了其初衷。此外，許多資料集（包括影像與文字資料）缺乏能恰當表徵資料集的元特徵 [11, 45, 58]。TransBO 中 TL 代理模型的建構對其超參數不敏感，且不需要元特徵。2）通用性（Universality）。第一項性質使 TransBO 得以成為黑盒最佳化的通用遷移學習框架，例如實驗設計 [12]、神經架構搜尋 [8] 等；我們在實驗章節中納入了在 NAS 任務上評估 TransBO 的實驗。3）可擴展性（Scalability）。相較於將 k 個來源任務、n 次試驗合併進單一代理模型的方法（O(k³n³)），TransBO 的複雜度低得多，為 O(kn³)，這意味著 TransBO 能輕鬆擴展至大量任務與試驗。4）理論探討。TransBO 也就防止效能劣化（負遷移）提供了理論討論：基於交叉驗證與非遞減約束，在足夠的試驗次數下，TransBO 的表現將不劣於未使用遷移學習的方法，而其他方法則不具備此性質（詳見附錄 A.4）。

---

## 5 Experiments and Results | 實驗與結果

### 5.1 Experimental Setup | 實驗設定

> [!quote] Original
> In this section, we evaluate TransBO from three perspectives: 1) stability and effectiveness on static TL tasks, 2) practicality on real-world dynamic TL tasks, and 3) universality when conducting neural architecture search.
>
> **Baselines.** We compare TransBO with eight baselines – two non-transfer methods: (1) Random search [3], (2) I-GP: independent Gaussian process-based surrogate fitted on the target task without using any source data, (3) SCoT [1]: it models the relationship between datasets and hyperparamter performance by training a single surrogate on the scaled and merged observations from both source tasks and the target task, (4) SGPR: the core TL algorithm used in the well-known service — Google Vizier [13], and four ensemble based TL methods: (5) POGPE [46], (6) TST [58], (7) TST-M: a variant of TST using dataset meta-features [58], and (8) RGPE [11].

> [!note] 翻譯
> 本節從三個面向評估 TransBO：1）在靜態 TL 任務上的穩定性與有效性；2）在貼近真實世界的動態 TL 任務上的實用性；3）進行神經架構搜尋時的通用性。
>
> **基線方法。** 我們將 TransBO 與八個基線比較——兩個非遷移方法：(1) 隨機搜尋（Random search）[3]；(2) I-GP：僅在目標任務上擬合、完全不使用來源資料的獨立高斯過程代理模型；(3) SCoT [1]：藉由在來源任務與目標任務經縮放並合併的觀測值上訓練單一代理模型，來建模資料集與超參數效能之間的關係；(4) SGPR：知名服務 Google Vizier [13] 所採用的核心遷移學習演算法；以及四個基於集成的遷移學習方法：(5) POGPE [46]、(6) TST [58]、(7) TST-M：使用資料集元特徵的 TST 變體 [58]、(8) RGPE [11]。

---

> [!quote] Original
> **Benchmark on 30 OpenML Datasets.** To evaluate the performance of TransBO, we create and publish a large-scale benchmark. Four ML algorithms, including Random Forest, Extra Trees, Adaboost and LightGBM [24], are tuned on 30 real-world datasets (tasks) from OpenML repository [53]. The design of hyperparameter space and meta-feature for each dataset is adopted from the implementation in Auto-Sklearn [10]. For each ML algorithm on each dataset, we sample 20k configurations from the hyperparameter space randomly and store the corresponding evaluation results. It takes more than 200k CPU hours to collect these evaluation results. Note that, for reproducibility, we provide more details about this benchmark, including the datasets, the hyperparameter space of ML algorithms, etc., in Appendix A.1.
>
> **AutoML HPO Tasks.** To evaluate the performance of each method, the experiments are performed in a leave-one-out fashion. Each method optimizes the hyperparameters of a specific task over 20k configurations while treating the remaining tasks as the source tasks. In each source task, only N_S instances (here N_S = 50) are used to extract knowledge from this task in order to test the efficiency of TL [11, 58].

> [!note] 翻譯
> **基於 30 個 OpenML 資料集的基準。** 為評估 TransBO 的表現，我們建立並公開一個大規模基準。四種 ML 演算法——Random Forest、Extra Trees、Adaboost 與 LightGBM [24]——在來自 OpenML 資料庫 [53] 的 30 個真實世界資料集（任務）上進行調參。每個資料集的超參數空間與元特徵設計沿用 Auto-Sklearn [10] 的實作。對每個資料集上的每種 ML 演算法，我們自超參數空間隨機抽取 2 萬個配置並儲存相應的評估結果；收集這些評估結果耗費逾 20 萬 CPU 小時。為利重現，本基準的更多細節（包括資料集、ML 演算法的超參數空間等）見附錄 A.1。
>
> **AutoML HPO 任務。** 為評估各方法的表現，實驗以留一法（leave-one-out）進行：各方法在 2 萬個配置上最佳化特定任務的超參數，並將其餘任務視為來源任務。在每個來源任務中，僅使用 N_S 筆實例（此處 N_S = 50）來抽取該任務的知識，以檢驗遷移學習的效率 [11, 58]。

---

> [!quote] Original
> We include the following three kinds of tasks: (a) Static TL Setting. This experiment is performed in a leave-one-out fashion, i.e., we optimize the hyperparameters of the target task while treating the remaining tasks as the source tasks. (b) Dynamic TL Setting. It simulates the real-world HPO scenarios, in which 30 tasks (datasets) arrive sequentially; when the i-th task appears, the former i − 1 tasks are treated as the source tasks. (c) Neural Architecture Search (NAS). It transfers tuning knowledge from conducting NAS on CIFAR-10 and CIFAR-100 to accelerate NAS on ImageNet16-120 based on NAS-Bench201 [7].
>
> In addition, following [11], all the compared methods are initialized with three randomly selected configurations, after which they proceed sequentially with a total of N_T evaluations (trials). To avoid the effect of randomness, each method is repeated 30 times, and the averaged performance metrics are reported.

> [!note] 翻譯
> 我們納入以下三類任務：(a) 靜態 TL 設定。此實驗以留一法進行，即最佳化目標任務的超參數，並將其餘任務視為來源任務。(b) 動態 TL 設定。模擬真實世界的 HPO 情境：30 個任務（資料集）依序到達；當第 i 個任務出現時，先前的 i − 1 個任務被視為來源任務。(c) 神經架構搜尋（NAS）。基於 NAS-Bench201 [7]，將在 CIFAR-10 與 CIFAR-100 上進行 NAS 所得的調參知識遷移，以加速 ImageNet16-120 上的 NAS 任務。
>
> 此外，遵循 [11]，所有受比較的方法均以三個隨機選取的配置初始化，之後依序進行總計 N_T 次評估（試驗）。為避免隨機性的影響，每種方法重複 30 次，並報告平均後的效能指標。

---

> [!quote] Original
> **Evaluation Metric.** Comparing each method in terms of classification error is questionable because the classification error is not commensurable across datasets. Following the previous works [1, 11, 58], we adopt the metrics as follows:
>
> Average Rank. For each target task, we rank all compared methods based on the performance of the best configuration they have found so far. Furthermore, ties are being solved by giving the average rank. For example, if one method observes the lowest validation error of 0.2, another two methods find 0.3, and the last method finds only 0.45, we would rank the methods with 1, (2+3)/2, (2+3)/2, 4.
>
> Average Distance to Minimum. The average distance to the global minimum after t trials is defined as:
>
> ADTM(X_t) = (1/K) Σ_{i∈[1:K]} (min_{x∈X_t} y^i_x − y^i_min) / (y^i_max − y^i_min),  (9)
>
> where y^i_min and y^i_max are the best and worst performance value on the i-th task, K is the number of tasks, i.e., K = 30, y^i_x corresponds to the performance of configuration x in the i-th task, and X_t is the set of hyperparameter configurations that have been evaluated in the previous t trials. The relative distances over all considered tasks are averaged to obtain the final ADTM value.

> [!note] 翻譯
> **評估指標。** 直接以分類誤差比較各方法並不妥當，因為分類誤差在不同資料集之間不可通約（not commensurable）。遵循先前研究 [1, 11, 58]，我們採用以下指標：
>
> 平均排名（Average Rank）。對每個目標任務，我們依各方法迄今所找到最佳配置的效能，對所有受比較方法進行排名；並以平均排名處理並列情形。例如，若某方法觀測到最低驗證誤差 0.2，另兩個方法找到 0.3，最後一個方法僅找到 0.45，則各方法的排名為 1、(2+3)/2、(2+3)/2、4。
>
> 平均至最小值距離（Average Distance to Minimum, ADTM）。經 t 次試驗後至全域最小值的平均距離定義為：
>
> ADTM(X_t) = (1/K) Σ_{i∈[1:K]} (min_{x∈X_t} y^i_x − y^i_min) / (y^i_max − y^i_min),  (9)
>
> 其中 y^i_min 與 y^i_max 分別為第 i 個任務上的最佳與最差效能值，K 為任務數（即 K = 30），y^i_x 為配置 x 在第 i 個任務上的效能，X_t 為前 t 次試驗中已被評估的超參數配置集合。將所有考慮任務上的相對距離取平均，即得最終的 ADTM 值。

---

> [!quote] Original
> **Implementations & Parameters.** TransBO implements the Gaussian process using SMAC3 [20, 35], which can support a complex hyperparameter space, including numerical, categorical, and conditional hyperparameters, and the kernel hyperparameters in GP are inferred by maximizing the marginal likelihood. The two optimization problems in TransBO are solved by using SQP methods provided in SciPy [54]. In the BO module, the popular EI acquisition function is used. As for the parameters in each baseline, the bandwidth ρ in TST [58] is set to 0.3 for all experiments; in RGPE, we sample 100 times (S = 100) to calculate the weight for each base surrogate; in SGPR [13], the parameter α, which determines the relative importance of standard deviations of past tasks and the current task, is set to 0.95 (Check Appendix B for reproduction details).

> [!note] 翻譯
> **實作與參數。** TransBO 使用 SMAC3 [20, 35] 實作高斯過程，其可支援複雜的超參數空間，包括數值型、類別型與條件式超參數；GP 中的核超參數則透過最大化邊際概似（marginal likelihood）推得。TransBO 中的兩個最佳化問題以 SciPy [54] 提供的 SQP 方法求解。在 BO 模組中，使用流行的 EI 擷取函數。至於各基線的參數：TST [58] 的帶寬 ρ 在所有實驗中設為 0.3；RGPE 中，我們抽樣 100 次（S = 100）以計算各基底代理模型的權重；SGPR [13] 中決定過去任務與當前任務標準差相對重要性的參數 α 設為 0.95（重現細節見附錄 B）。

---

### 5.2 Comprehensive Experiments in Two TL Settings | 兩種遷移學習設定下的完整實驗

> [!quote] Original
> **Static TL Setting.** To demonstrate the efficiency and effectiveness of transfer learning in the static scenario, we compare TransBO with the baselines on four benchmarks (i.e., Random Forest, LightGBM, Adaboost, and Extra Trees). Concretely, each task is selected as the target task in turn, and the remaining tasks are the source tasks; then we can measure the performance of each baseline based on the results when tuning the hyperparameters of the target task. Furthermore, we use 29 and 5 source tasks respectively to evaluate the ability of each method when given a different amount of source knowledge in terms of the number of source tasks N_task. Note that, for each target task, the maximum number of trials is 75. Figure 2 and Figure 3 show the experiment results on four benchmarks with 29 and 5 source tasks respectively, using average rank; more results on ADTM can be found in Appendix A.3.
>
> [Figure 2: Static TL results for four algorithms with N_task = 29 source tasks. / Figure 3: Static TL results for four algorithms with N_task = 5 source tasks.]

> [!note] 翻譯
> **靜態 TL 設定。** 為展示遷移學習在靜態情境下的效率與有效性，我們在四個基準（Random Forest、LightGBM、Adaboost 與 Extra Trees）上將 TransBO 與各基線比較。具體而言，依次選取每個任務作為目標任務，其餘任務作為來源任務；如此便能依據目標任務調參的結果衡量各基線的表現。此外，我們分別使用 29 個與 5 個來源任務，以評估各方法在來源任務數 N_task 不同（即所給來源知識量不同）時的能力。注意，每個目標任務的最大試驗次數為 75。Figure 2 與 Figure 3 分別展示了四個基準在 29 個與 5 個來源任務下、以平均排名衡量的實驗結果；更多 ADTM 結果見附錄 A.3。
>
> [Figure 2：N_task = 29 個來源任務下四種演算法的靜態 TL 結果。／Figure 3：N_task = 5 個來源任務下四種演算法的靜態 TL 結果。]

---

> [!quote] Original
> First, we can observe that the average rank of TransBO in Figure 2 and Figure 3 decreases sharply in the initial 20 trials. Compared with other TL methods, it shows that TransBO can extract and utilize the auxiliary source knowledge efficiently and effectively. Remarkably, TransBO exhibits a strong stability from two perspectives: 1) TransBO is stable on different benchmarks; and 2) it still performs well when given a different number of source tasks, e.g., in Figure 2 N_task = 29, and N_task = 5 in Figure 3. RGPE is one of the most competitive baselines, and we take it as an example. RGPE achieves comparable or similar performance with TransBO in Figure 2(b) and Figure 2(c) where N_task = 29. However, in Figure 3(b) and Figure 3(c) RGPE exhibits a larger fluctuation over the trials compared with TransBO when N_task = 5. Unlike the baselines, TransBO extracts the source knowledge in a principled way, and the empirical results show it performs well in most circumstances, thus demonstrating its superior efficiency and effectiveness.

> [!note] 翻譯
> 首先可以觀察到，TransBO 在 Figure 2 與 Figure 3 中的平均排名於最初 20 次試驗內急劇下降。相較其他遷移學習方法，這顯示 TransBO 能高效且有效地抽取並利用輔助的來源知識。值得注意的是，TransBO 從兩個角度展現了強大的穩定性：1）TransBO 在不同基準上均穩定；2）在來源任務數不同時（如 Figure 2 之 N_task = 29 與 Figure 3 之 N_task = 5）仍表現良好。RGPE 是最具競爭力的基線之一，我們以之為例：在 N_task = 29 的 Figure 2(b) 與 Figure 2(c) 中，RGPE 達到與 TransBO 相當或相近的表現；然而在 N_task = 5 的 Figure 3(b) 與 Figure 3(c) 中，RGPE 隨試驗次數呈現比 TransBO 更大的波動。與各基線不同，TransBO 以有原則的方式抽取來源知識，實證結果顯示其在多數情況下表現良好，從而證明其優越的效率與有效性。

---

> [!quote] Original
> **Dynamic TL Setting.** To simulate the real-world transfer learning scenario, we perform the dynamic experiment on different benchmarks. In this experiment, 30 tasks arrive sequentially; when the i-th task arrives, the previous i-1 tasks are used as the source tasks. The maximum number of trials for each task is 50, and we compare TransBO with TST, RGPE, and POGPE based on the best-observed performance on each task. Table 1 reports the number of tasks on which each TL method gets the highest and second-highest performance. Note that the sum of each column may be more than 30 since some of the TL methods are tied for first or second place.
>
> As shown in Table 1, TransBO achieves the largest number of top1 and top2 online performance among the compared methods. Take Adaboost as an example, TransBO gets 25 top2 results among 30 tasks, while this number is 13 for RGPE. RGPE gets a similar performance with TST on Lightgbm and Extra Trees, but its performance decreases on Adaboost. Thus, RGPE is not stable in this scenario. Compared with the baselines, TransBO could achieve more stable and satisfactory performance in the dynamic setting.
>
> [Table 1: Dynamic TL results for tuning four ML algorithms — TransBO 在 Adaboost/Random Forest/Extra Trees/LightGBM 的第一名（1st）任務數分別為 14/15/14/12，均高於 POGPE、TST 與 RGPE。]

> [!note] 翻譯
> **動態 TL 設定。** 為模擬真實世界的遷移學習情境，我們在不同基準上進行動態實驗。實驗中 30 個任務依序到達；當第 i 個任務到達時，先前的 i−1 個任務被用作來源任務。每個任務的最大試驗次數為 50，我們依各任務上觀測到的最佳表現，將 TransBO 與 TST、RGPE、POGPE 比較。Table 1 報告了各遷移學習方法取得最高與次高表現的任務數。注意，各欄總和可能超過 30，因為部分方法會並列第一或第二。
>
> 如 Table 1 所示，TransBO 在受比較方法中取得最多的線上前一、前二名表現。以 Adaboost 為例，TransBO 在 30 個任務中取得 25 個前二名成績，而 RGPE 僅為 13 個。RGPE 在 LightGBM 與 Extra Trees 上與 TST 表現相近，但在 Adaboost 上表現下滑，可見 RGPE 在此情境中並不穩定。與基線相比，TransBO 在動態設定下能達成更穩定且令人滿意的表現。
>
> [Table 1：調校四種 ML 演算法的動態 TL 結果——TransBO 在 Adaboost／Random Forest／Extra Trees／LightGBM 的第一名（1st）任務數分別為 14／15／14／12，均高於 POGPE、TST 與 RGPE。]

---

### 5.3 Applying TransBO to NAS | 將 TransBO 應用於神經架構搜尋

> [!quote] Original
> To investigate the universality of TransBO in conducting Neural Architecture Search (NAS), here we use TransBO to extract and integrate the optimization knowledge from NAS tasks on CIFAR-10 and CIFAR-100 (with 50 trials each) to accelerate the NAS task on ImageNet with NAS-Bench201 [7]. From Figure 6, we have that TransBO could achieve more than 5x speedups over the state-of-the-art NAS methods – Bayesian Optimization (BO) and Regularized Evolution Algorithm (REA) [43]. Therefore, TransBO can also be applied to the NAS tasks.
>
> [Figure 6: Results on optimizing NAS on NASBench201 — 縱軸為最佳驗證誤差，橫軸為牆鐘時間（10⁶ 秒）；TransBO 明顯快於 RS、BO 與 REA。]

> [!note] 翻譯
> 為考察 TransBO 在神經架構搜尋（NAS）上的通用性，我們使用 TransBO 抽取並整合 CIFAR-10 與 CIFAR-100 上 NAS 任務（各 50 次試驗）的最佳化知識，以加速基於 NAS-Bench201 [7] 的 ImageNet NAS 任務。由 Figure 6 可知，TransBO 相較最先進的 NAS 方法——貝葉斯最佳化（BO）與正則化演化演算法（Regularized Evolution Algorithm, REA）[43]——可達成逾 5 倍的加速。因此，TransBO 也能應用於 NAS 任務。
>
> [Figure 6：NAS-Bench201 上 NAS 最佳化的結果——縱軸為最佳驗證誤差，橫軸為牆鐘時間（10⁶ 秒）；TransBO 明顯快於 RS、BO 與 REA。]

---

### 5.4 Ablation Studies | 消融實驗

> [!quote] Original
> **Source Knowledge Learning.** This experiment is designed to evaluate the performance of source surrogate M^S learned in Phase 1. M^S corresponds to the source knowledge extracted from the source tasks. In this setting, the source surrogate is used to guide the optimization of hyperparameters instead of the final TL surrogate M^TL. The quality of source knowledge learned by each TL method thus can be measured by the performance of M^S. Figure 4 shows the results of TransBO and three one-phase framework based methods: POGPE, TST, and RGPE on two benchmarks — Adaboost and LightGBM. We can observe that the proposed TransBO outperforms the other three baselines on both two metrics: average rank and ADTM. According to some heuristics, these baselines calculate the weights in M^S independently. Instead, by solving the constrained optimization problem, TransBO can learn the optimal weights in M^S in a joint and principled manner. More results on the other two benchmarks can be found in Appendix A.3.

> [!note] 翻譯
> **來源知識學習。** 此實驗旨在評估第一階段所學來源代理模型 M^S 的表現。M^S 對應於自來源任務抽取的來源知識。在此設定中，改以來源代理模型（而非最終 TL 代理模型 M^TL）引導超參數的最佳化，如此各遷移學習方法所學來源知識的品質即可由 M^S 的表現衡量。Figure 4 顯示 TransBO 與三種基於單階段框架的方法（POGPE、TST、RGPE）在 Adaboost 與 LightGBM 兩個基準上的結果。可以觀察到，所提出的 TransBO 在平均排名與 ADTM 兩項指標上均優於其他三個基線。這些基線依某些啟發式方法獨立計算 M^S 中的權重；相反地，TransBO 透過求解約束最佳化問題，以聯合且有原則的方式學得 M^S 的最佳權重。其餘兩個基準的更多結果見附錄 A.3。

---

> [!quote] Original
> **Target Weight Analysis.** Here we compare the target weight obtained in POGPE, RGPE, TST, and TransBO. Figure 5(a) and 5(b) illustrate the trend of target weight on two benchmarks: Random Forest and Adaboost. The target weight in POGPE is fixed to a constant - 0.5, regardless of the increasing number of trials; TST's remains low even when the target observations are sufficient; RGPE's shows a trend of fluctuation because the sampling-based ranking loss is not stable. TransBO's keeps increasing with the number of trials, which matches the intuition that the importance of the target surrogate should be low when target observations are insufficient and gradually increase as target observations grow.

> [!note] 翻譯
> **目標權重分析。** 此處比較 POGPE、RGPE、TST 與 TransBO 所得的目標權重。Figure 5(a) 與 5(b) 展示了 Random Forest 與 Adaboost 兩個基準上目標權重的變化趨勢。POGPE 的目標權重固定為常數 0.5，不隨試驗次數增加而變化；TST 的目標權重即使在目標觀測值充足時仍維持在低檔；RGPE 的目標權重呈現波動趨勢，因為基於抽樣的排名損失並不穩定。TransBO 的目標權重則隨試驗次數持續上升，符合直覺——當目標觀測值不足時，目標代理模型的重要性應較低，並隨目標觀測值增長而逐漸提高。

---

> [!quote] Original
> **Scalability Analysis.** In the static TL setting, we include different number of source tasks when conducting transfer learning (See Figures 2 and 3, where N_task = 5 and N_task = 29); the stable and effective results show the scalability in terms of the number of source tasks. We further investigate the optimization overhead of suggesting a new configuration, and measure the runtime of the competitive TL methods: POGPE, RGPE, TST, SCoT, and TransBO. Each method is tested on Random Forest with 75 trials, and we repeat each method 20 times. Figure 5(c) shows the experiment results, where the y-axis is the mean cumulative runtime in seconds on a log scale. We do not take the evaluation time of each configuration into account, and only compare the optimization overhead of suggesting a new configuration. ScoT's runtime increases rapidly among the compared methods as it has the O(k³n³) complexity. Since both the two-phase and one-phase framework-based methods own the O(n³) complexity, it takes nearly the same optimization overhead for TST, POGPE, and TransBO to suggest a configuration in the first 75 trials. Although RGPE also has the O(n³) complexity, it depends on a sampling strategy to compute the surrogate weight, which introduces extra overhead to configuration suggestion. Instead, TransBO exhibits a similar scalability result like POGPE, which incorporates no optimization overhead due to the constant weights. This shows that TransBO scales well in both the number of trials and tasks.

> [!note] 翻譯
> **可擴展性分析。** 在靜態 TL 設定中，我們在進行遷移學習時納入了不同數量的來源任務（見 Figure 2 與 Figure 3，分別為 N_task = 5 與 N_task = 29）；穩定且有效的結果展現了在來源任務數方面的可擴展性。我們進一步考察建議新配置的最佳化開銷，量測具競爭力的遷移學習方法——POGPE、RGPE、TST、SCoT 與 TransBO——的執行時間。每種方法在 Random Forest 上以 75 次試驗測試，並重複 20 次。Figure 5(c) 顯示實驗結果，其中縱軸為對數尺度下的平均累積執行時間（秒）。我們不計入各配置的評估時間，僅比較建議新配置的最佳化開銷。SCoT 因具有 O(k³n³) 的複雜度，其執行時間在受比較方法中增長最快。由於兩階段與單階段框架方法皆為 O(n³) 複雜度，TST、POGPE 與 TransBO 在前 75 次試驗中建議配置的最佳化開銷幾乎相同。RGPE 雖同為 O(n³) 複雜度，但其依賴抽樣策略計算代理模型權重，為配置建議引入了額外開銷。相對地，TransBO 展現出與 POGPE（因權重為常數而不含最佳化開銷）相似的可擴展性結果。這顯示 TransBO 在試驗數與任務數兩方面均具良好的擴展性。

---

## 6 Conclusion | 結論

> [!quote] Original
> In this paper, we introduced TransBO, a novel two-phase transfer learning (TL) method for hyperparameter optimization (HPO), which can leverage the auxiliary knowledge from previous tasks to accelerate the HPO process of the current task effectively. This framework can extract and aggregate the source and target knowledge jointly and adaptively. In addition, we published a large-scale TL benchmark for HPO with up to 1.8 million model evaluations; the extensive experiments, including static and dynamic transfer learning settings and neural architecture search, demonstrate the superiority of TransBO over the state-of-the-art methods.

> [!note] 翻譯
> 本文介紹了 TransBO——一種新穎的兩階段遷移學習方法，用於超參數最佳化，能有效利用先前任務的輔助知識來加速當前任務的 HPO 過程。此框架能以聯合且自適應的方式抽取並彙整來源與目標知識。此外，我們公開了一個包含多達 180 萬次模型評估的大規模 HPO 遷移學習基準；涵蓋靜態與動態遷移學習設定以及神經架構搜尋的大量實驗，證明了 TransBO 相對於最先進方法的優越性。

---

## Acknowledgments | 致謝

> [!quote] Original
> This work was supported by the National Natural Science Foundation of China (No.61832001), Beijing Academy of Artificial Intelligence (BAAI), PKU-Tencent Joint Research Lab. Bin Cui is the corresponding author.

> [!note] 翻譯
> 本研究由中國國家自然科學基金（No.61832001）、北京智源人工智能研究院（BAAI）與北大—騰訊聯合實驗室資助。Bin Cui 為通訊作者。

---

## References | 參考文獻

> References omitted / 參考文獻略

---

## Appendix | 附錄（附錄僅節譯）

> [!quote] Original
> **A.1 The Details of Benchmark.** As described in Section 5, we create a benchmark to evaluate the performance of TL methods. We choose four ML algorithms that are widely used in data analysis, including Random Forest, Extra Trees, Adaboost and Lightgbm. The implementation of each algorithm and the design of their hyperparameter space follows Auto-sklearn. For each algorithm, the range and default value of each hyperparameter are illustrated in Tables 2, 3 and 4. To collect sufficient source HPO data for transfer learning, we select 30 real-world datasets from OpenML repository, and evaluate the validation performance (i.e., the balanced accuracy) of 20k configurations for each benchmark, which are randomly sampled from the hyperparameter space. The datasets used in our benchmarks are of medium size, whose number of rows ranges from 2000 to 8192. For more details, see Table 5.

> [!note] 翻譯
> **A.1 基準細節。** 如第 5 節所述，我們建立了一個評估遷移學習方法表現的基準。我們選擇了資料分析中廣泛使用的四種 ML 演算法：Random Forest、Extra Trees、Adaboost 與 LightGBM。各演算法的實作及其超參數空間設計沿用 Auto-sklearn；各演算法每個超參數的範圍與預設值列於 Tables 2、3、4。為收集足夠的來源 HPO 資料以進行遷移學習，我們自 OpenML 資料庫選取 30 個真實世界資料集，並對每個基準評估自超參數空間隨機抽取之 2 萬個配置的驗證表現（即平衡準確率，balanced accuracy）。基準所用資料集為中等規模，列數介於 2000 至 8192 之間；詳見 Table 5。
>
> [Tables 2–4：Adaboost、Random Forest／Extra Trees、LightGBM 的超參數範圍與預設值。Table 5：30 個基準資料集之列數、欄數與類別數。]

---

> [!quote] Original
> **A.2 Feasibility of Transfer Learning.** To verify the feasibility of transfer learning in the setting of HPO, we conduct an HPO experiment on two datasets — quake and hypothyroid(2). We tune the learning rate and n_estimators of Adaboost while fixing the other hyperparameters, and then evaluate the validation performance (the balanced accuracy) of each configuration. Figure 9 shows the performance on 2500 Adaboost configurations, where deeper color means better performance. It is quite clear that the optimal configuration differs on the two datasets (tasks), which means re-optimization is essential for HPO. However, the performance distribution is somehow similar on the two datasets. For example, they both perform badly in the lower-right region and perform well in the upper region. Based on this observation, it is natural to accelerate the re-optimization process with the auxiliary knowledge acquired from the previous tasks.

> [!note] 翻譯
> **A.2 遷移學習的可行性。** 為驗證遷移學習在 HPO 情境下的可行性，我們在 quake 與 hypothyroid(2) 兩個資料集上進行 HPO 實驗：固定其他超參數，調校 Adaboost 的 learning rate 與 n_estimators，並評估各配置的驗證表現（平衡準確率）。Figure 9 顯示 2500 個 Adaboost 配置的表現，顏色越深代表表現越好。顯而易見，最佳配置在兩個資料集（任務）上並不相同，這意味著重新最佳化對 HPO 而言是必要的。然而，兩個資料集上的表現分布卻有某種相似性——例如兩者在右下區域均表現不佳，而在上方區域均表現良好。基於此觀察，以先前任務所獲得的輔助知識來加速重新最佳化的過程，是相當自然的做法。

---

> [!quote] Original
> **A.4 Convergence Discussion about TransBO.** In TransBO, when sufficient trials on the target task are obtained, the weight of target surrogate p^T will approach 1 as the HPO proceeds. Based on the mechanism we adopted in TransBO — cross-validation, we can observe that p^T_i in the i-th trial will approach 1 as i increases. Therefore, the final TL surrogate M^TL will be set to the target surrogate M^T. So we can have that, with sufficient trials, the final TL surrogate will find the same optimum as the target surrogate does; that's, the final solution of surrogate M^TL will be no worse than the one in M^T given sufficient trials. The above finding demonstrates that TransBO can alleviate negative transfer [37]. In other words, it can avoid performance degradation compared with non-transfer methods – the traditional BO methods.

> [!note] 翻譯
> **A.4 TransBO 的收斂性討論。** 在 TransBO 中，當目標任務累積了足夠的試驗後，目標代理模型的權重 p^T 會隨 HPO 的推進而趨近於 1。基於 TransBO 所採用的機制——交叉驗證，可以觀察到第 i 次試驗中的 p^T_i 會隨 i 增加而趨近於 1。因此，最終的 TL 代理模型 M^TL 將等同於目標代理模型 M^T。由此可得：在足夠的試驗次數下，最終 TL 代理模型將找到與目標代理模型相同的最佳解；亦即，在足夠試驗下，代理模型 M^TL 的最終解將不劣於 M^T 的解。上述發現說明 TransBO 能緩解負遷移 [37]；換言之，相較於非遷移方法（傳統 BO 方法），它能避免效能退化。
>
> ※ 附錄 A.3（更多實驗結果：四基準之 ADTM 靜態結果與 Random Forest／Extra Trees 之來源知識學習結果）與附錄 B（重現細節：程式碼、基準資料與執行腳本說明）僅節譯，內容為補充圖表與重現指引。
