---
citation_key: "WistubaGrabocka2021"
title: "Few-Shot Bayesian Optimization with Deep Kernel Surrogates"
authors: "Martin Wistuba; Josif Grabocka"
year: 2021
doi: ""
source: "arXiv (2101.07667)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "2101.07667"
conversion: "pdftotext -layout (automated)"
language: "bilingual-en-zh"
---

# Few-Shot Bayesian Optimization with Deep Kernel Surrogates | 基於深度核代理模型的少樣本貝葉斯最佳化

> [!abstract] 重點摘要
> - 將超參數最佳化（hyperparameter optimization, HPO）中的遷移學習重新定位為少樣本學習（few-shot learning）問題：以跨任務共享的深度核（deep kernel）高斯過程（Gaussian process）代理模型，在少量評估後快速適應新任務的響應函數。
> - 提出 FSBO 方法：以端到端元學習（meta-learning）方式訓練深度核參數，所有任務相關參數被邊際化，模型規模不隨任務數成長，可擴展至任意數量的來源任務。
> - 設計任務增強（task augmentation）策略，對每個批次隨機縮放標籤範圍，使模型對標籤的偏移與尺度不變，從而免除目標任務上難以準確估計的標籤正規化問題。
> - 另提出以演化演算法（evolutionary algorithm）搭配代理模型，為貝葉斯最佳化估計資料驅動的暖啟動（warm-starting）初始化序列。
> - 在 AdaBoost、GLMNet、SVM 三個元資料集上，FSBO 以統計顯著的優勢勝過 RGPE、MetaBO、ABLR 等最先進遷移學習基線；消融實驗顯示暖啟動與微調（fine-tuning）各自帶來正交的貢獻。

---

## Abstract | 摘要

> [!quote] Original
> Hyperparameter optimization (HPO) is a central pillar in the automation of machine learning solutions and is mainly performed via Bayesian optimization, where a parametric surrogate is learned to approximate the black box response function (e.g. validation error). Unfortunately, evaluating the response function is computationally intensive. As a remedy, earlier work emphasizes the need for transfer learning surrogates which learn to optimize hyperparameters for an algorithm from other tasks. In contrast to previous work, we propose to rethink HPO as a few-shot learning problem in which we train a shared deep surrogate model to quickly adapt (with few response evaluations) to the response function of a new task. We propose the use of a deep kernel network for a Gaussian process surrogate that is meta-learned in an end-to-end fashion in order to jointly approximate the response functions of a collection of training data sets. As a result, the novel few-shot optimization of our deep kernel surrogate leads to new state-of-the-art results at HPO compared to several recent methods on diverse metadata sets.

> [!note] 翻譯
> 超參數最佳化（hyperparameter optimization, HPO）是機器學習解決方案自動化的核心支柱，主要透過貝葉斯最佳化（Bayesian optimization）進行：學習一個參數化的代理模型（surrogate）來近似黑盒響應函數（例如驗證誤差）。遺憾的是，評估響應函數的計算成本相當高昂。作為補救，先前的研究強調需要遷移學習代理模型，即從其他任務學習如何為某演算法最佳化超參數。與先前工作不同，我們提出將 HPO 重新思考為一個少樣本學習（few-shot learning）問題：訓練一個共享的深度代理模型，使其能以極少的響應評估快速適應新任務的響應函數。我們提出將深度核網路（deep kernel network）用於高斯過程（Gaussian process）代理模型，並以端到端方式進行元學習（meta-learning），以聯合近似一組訓練資料集的響應函數。因此，我們的深度核代理模型所實現的新穎少樣本最佳化，在多樣的元資料集上相較於多種近期方法，於 HPO 任務取得了新的最先進（state-of-the-art）成果。

---

## 1 Introduction | 引言

> [!quote] Original
> Many machine learning models have very sensitive hyperparameters that must be carefully tuned for efficient use. Unfortunately, finding the right setting is a tedious trial and error process that requires expert knowledge. AutoML methods address this problem by providing tools to automate hyperparameter optimization, where Bayesian optimization has become the standard for this task (Snoek et al., 2012). It treats the problem of hyperparameter optimization as a black box optimization problem. Here the black box function is the hyperparameter response function, which maps a hyperparameter setting to the validation loss. Bayesian optimization consists of two parts. First, a surrogate model, often a Gaussian process, is used to approximate the response function. Second, an acquisition function is used that balances the trade-off between exploration and exploitation. In a sequential process, hyperparameter settings are selected and evaluated, followed by an update of the surrogate model.

> [!note] 翻譯
> 許多機器學習模型都具有非常敏感的超參數，必須仔細調校才能有效使用。遺憾的是，尋找正確的設定是一個冗長的試誤過程，並且需要專家知識。AutoML 方法透過提供自動化超參數最佳化的工具來解決此問題，其中貝葉斯最佳化已成為此任務的標準做法 (Snoek et al., 2012)。它將超參數最佳化視為黑盒最佳化問題，此處的黑盒函數即超參數響應函數（hyperparameter response function），將一組超參數設定映射至驗證損失。貝葉斯最佳化由兩部分組成：其一是代理模型（通常為高斯過程），用以近似響應函數；其二是採集函數（acquisition function），用以權衡探索（exploration）與利用（exploitation）之間的取捨。在一個序貫過程中，超參數設定被選取並評估，隨後更新代理模型。

---

> [!quote] Original
> Recently, several attempts have been made to extend Bayesian optimization to account for a transfer learning setup. It is assumed here that historical information on machine learning algorithms is available with different hyperparameters. This can either be because this information is publicly available (e.g. OpenML) or because the algorithm is repeatedly optimized for different data sets. To this end, several transfer learning surrogates have been proposed that use this additional information to reduce the convergence time of Bayesian optimization.
>
> We propose a new paradigm for accomplishing the knowledge transfer by reconceptualizing the process as a few-shot learning task. Inspiration is drawn from the fact that there are a limited number of black box function evaluations for a new hyperparameter optimization task (i.e. few shots) but there are ample evaluations of related black box objectives (i.e. evaluated hyperparameters on other data sets). This approach has several advantages. First, a single model is learned that is trained to quickly adapt to a new task when few examples are available. This is exactly the challenge we face when optimizing hyperparameters. Second, this method can be scaled very well for any number of considered tasks. This not only enables the learning from large metadata sets but also enables the problem of label normalization to be dealt with in a new way. Finally, we present an evolutionary algorithm that can use surrogate models to get a warm start initialization for Bayesian optimization. All of our contributions are empirically compared with several competitive methods in three different problems. Two ablation studies provide information about the influence of the individual components.

> [!note] 翻譯
> 近來已有多項嘗試將貝葉斯最佳化擴展至遷移學習情境。此處假設可取得機器學習演算法在不同超參數下的歷史資訊，這可能是因為此類資訊為公開資料（如 OpenML），或是因為該演算法會被反覆針對不同資料集進行最佳化。為此，已有多種遷移學習代理模型被提出，利用這些額外資訊以縮短貝葉斯最佳化的收斂時間。
>
> 我們提出一個實現知識遷移的新典範，將此過程重新概念化為少樣本學習任務。其靈感來自以下事實：對於一個新的超參數最佳化任務，黑盒函數評估次數十分有限（即「少樣本」），但相關黑盒目標函數的評估卻相當充足（即已在其他資料集上評估過的超參數）。此方法具有數項優勢。第一，只需學習單一模型，其訓練目標即是在僅有少量樣本時快速適應新任務——這正是最佳化超參數時所面臨的挑戰。第二，此方法可對任意數量的任務良好擴展，不僅使得從大型元資料集學習成為可能，也讓標籤正規化（label normalization）問題得以用新的方式處理。最後，我們提出一個演化演算法（evolutionary algorithm），能利用代理模型為貝葉斯最佳化取得暖啟動（warm-starting）初始化。我們的所有貢獻皆在三個不同問題上與多種具競爭力的方法進行實證比較，並以兩項消融研究（ablation study）說明各個組件的影響。

---

> [!quote] Original
> Concluding, our contributions in this work are:
> - This is the first work that, in the context of hyperparameter optimization (HPO), trains the initialization of the parameters of a probabilistic surrogate model from a collection of meta-tasks by few-shot learning and then transfers it by fine-tuning the initialized kernel parameters to a target task.
> - We are the first to consider transfer learning in HPO as a few-shot learning task.
> - We set the new state of the art in transfer learning for the HPO and provide ample evidence that we outperform strong baselines published at ICLR and NeurIPS with a statistically significant margin.
> - We present an evolutionary algorithm that can use surrogate models to get a warm start initialization for Bayesian optimization.

> [!note] 翻譯
> 總結而言，本文的貢獻如下：
> - 這是在超參數最佳化（HPO）脈絡下，首個透過少樣本學習從一組元任務（meta-tasks）訓練機率式代理模型參數初始化，並藉由微調（fine-tuning）已初始化的核參數將其遷移至目標任務的工作。
> - 我們是首個將 HPO 中的遷移學習視為少樣本學習任務的研究。
> - 我們在 HPO 的遷移學習領域樹立了新的最先進水準，並提供充分證據顯示，我們以統計顯著的幅度勝過發表於 ICLR 與 NeurIPS 的強力基線方法。
> - 我們提出一個演化演算法，能利用代理模型為貝葉斯最佳化取得暖啟動初始化。

---

## 2 Related Work | 相關研究

> [!quote] Original
> The idea of using transfer learning to improve Bayesian optimization is being investigated in several papers. The early work suggests learning a single Gaussian process for the entire data (Bardenet et al., 2013; Swersky et al., 2013; Yogatama & Mann, 2014). Since the training of a Gaussian process is cubic in the number of training points, this idea does not scale well. This problem has recently been addressed by several proposals to use ensembles of Gaussian processes where a Gaussian process is learned for each task (Wistuba et al., 2018; Feurer et al., 2018). This idea scales linearly in the number of tasks but still cubically in the number of training points per task. Thus, the problem persists in scenarios where there is a lot of data available for each task.

> [!note] 翻譯
> 利用遷移學習改進貝葉斯最佳化的想法已在多篇論文中被探討。早期工作建議對全部資料學習單一高斯過程 (Bardenet et al., 2013; Swersky et al., 2013; Yogatama & Mann, 2014)。由於高斯過程的訓練成本與訓練點數量呈三次方關係，此想法的可擴展性不佳。近來已有多項提案透過高斯過程集成（ensemble）解決此問題，即為每個任務各學習一個高斯過程 (Wistuba et al., 2018; Feurer et al., 2018)。此想法對任務數量呈線性擴展，但對每個任務的訓練點數量仍為三次方，因此在每個任務皆有大量資料的情境下，問題依然存在。

---

> [!quote] Original
> Bayesian neural networks are a possible more scalable way of learning with large amounts of data. For example, Schilling et al. (2015) propose to use a neural network with task embedding and variable interactions. To obtain mean and variance predictions, the authors propose using an ensemble of models. In contrast, Springenberg et al. (2016) use a Bayesian multi-task neural network. However, since training Bayesian neural networks is computationally intensive, Perrone et al. (2018) propose a more scalable approach. They suggest using a neural network that is shared by all tasks and using Bayesian linear regression for each task. The parameters are trained jointly on the entire data. While our work shares some similarities with the previous work, our algorithm has unique properties. First of all, a meta-learning algorithm is used, which is motivated by recent work on model-agnostic meta-learning for few-shot learning (Finn et al., 2017). This will allow us to integrate all task-specific parameters out such that the model does not grow with the number of tasks. As a result, our algorithm scales very well with the number of tasks. Second, while we are also using a neural network, we combine it with a Gaussian process with a nonlinear kernel in order to obtain uncertainty predictions.

> [!note] 翻譯
> 貝葉斯神經網路（Bayesian neural network）是一種在大量資料下可能更具擴展性的學習方式。例如，Schilling et al. (2015) 提出使用帶有任務嵌入（task embedding）與變數交互作用的神經網路；為取得均值與變異數預測，作者提出使用模型集成。相對地，Springenberg et al. (2016) 使用貝葉斯多任務神經網路。然而，由於訓練貝葉斯神經網路的計算成本高昂，Perrone et al. (2018) 提出更具擴展性的做法：使用一個由所有任務共享的神經網路，並對每個任務使用貝葉斯線性迴歸，其參數在全部資料上聯合訓練。雖然我們的工作與先前研究有一些相似之處，但我們的演算法具有獨特性質。首先，我們採用元學習演算法，其動機源自近期關於少樣本學習的模型無關元學習（model-agnostic meta-learning）研究 (Finn et al., 2017)。這使我們能將所有任務特定參數積分消去（integrate out），使模型不隨任務數量成長，因此我們的演算法對任務數量具有極佳的擴展性。其次，雖然我們同樣使用神經網路，但我們將其與具非線性核的高斯過程結合，以取得不確定性預測。

---

> [!quote] Original
> A simpler solution for using the transfer learning idea in Bayesian optimization is initialization (Feurer et al., 2015; Wistuba et al., 2015a). The standard Bayesian optimization routine with a simple Gaussian process is used in this case but it is warm-started by a number of hyperparameter settings that work well for related tasks. We also explore this idea in the context of this paper by proposing a simple evolutionary algorithm that can use a surrogate model to estimate a data-driven warm start initialization sequence. The use of an evolutionary algorithm is motivated by its ease of implementation and natural capability to deal with continuous and discrete hyperparameters.

> [!note] 翻譯
> 在貝葉斯最佳化中運用遷移學習理念的一種更簡單方案是初始化（initialization）(Feurer et al., 2015; Wistuba et al., 2015a)。此情形下仍採用具有簡單高斯過程的標準貝葉斯最佳化流程，但以若干在相關任務上表現良好的超參數設定進行暖啟動。本文亦探討此想法，提出一個簡單的演化演算法，能利用代理模型估計資料驅動的暖啟動初始化序列。採用演化演算法的動機在於其實作簡便，且天然具備處理連續與離散超參數的能力。

---

## 3 Preliminaries | 預備知識

### 3.1 Bayesian Optimization | 貝葉斯最佳化

> [!quote] Original
> Bayesian optimization is an optimization method for computationally intensive black box functions that consists of two main components, the surrogate model and the acquisition function. The surrogate model is a probabilistic model with mean µ and variance σ², which tries to approximate the unknown black box function f, in the following also response function. The acquisition function can provide a score for each feasible solution based on the prediction of the surrogate model. This score balances between exploration and exploitation. The following steps are carried out sequentially. First, the feasible solution that maximizes the acquisition function is evaluated. In this way a new observation (x, f(x)) is obtained. Then, the surrogate model is updated based on the entirety of all observations D. This sequence of steps is carried out until a previously defined convergence criterion occurs (e.g. exhaustion of the time budget). In Figure 1 we provide an example how Bayesian optimization is used to maximize a sine wave.
>
> Expected Improvement (Jones et al., 1998) is one of the most commonly used acquisition functions and will also be used in all our experiments. It is defined as
>
> a(x|D) = E [max {f(x) − y_max, 0}] ,   (1)
>
> where y_max is the largest observed value of f.

> [!note] 翻譯
> 貝葉斯最佳化是一種針對計算成本高昂之黑盒函數的最佳化方法，由兩個主要組件構成：代理模型與採集函數。代理模型是一個具有均值 µ 與變異數 σ² 的機率模型，試圖近似未知的黑盒函數 f（下文亦稱響應函數）。採集函數則能根據代理模型的預測，為每個可行解提供一個分數，此分數在探索與利用之間取得平衡。以下步驟依序執行：首先，評估使採集函數最大化的可行解，藉此獲得新觀測 (x, f(x))；接著，基於所有觀測的全體 D 更新代理模型。此步驟序列持續執行，直至達到預先定義的收斂準則（例如時間預算耗盡）。圖 1 提供了以貝葉斯最佳化求正弦波最大值的範例。
>
> 期望改進量（Expected Improvement）(Jones et al., 1998) 是最常用的採集函數之一，亦將用於我們所有的實驗，其定義為
>
> a(x|D) = E [max {f(x) − y_max, 0}] ,   (1)
>
> 其中 y_max 為 f 的最大觀測值。

---

### 3.2 Gaussian Processes | 高斯過程

> [!quote] Original
> We have a training set D of n observations, D = {(x_i, y_i)|i = 1, . . . , n}, where y_i are noisy observations of the function values y_i = f(x_i) + ε. We assume that the noise ε is additive independent identically distributed Gaussian with variance σ_n². For Gaussian processes (Rasmussen & Williams, 2006) we consider y_i to be a random variable and the joint distribution of all y_i is assumed to be multivariate Gaussian distributed:
>
> y ∼ N (m(X), k(X, X)) .   (2)
>
> A Gaussian process is completely specified by its mean function m, its covariance function k, and may depend on parameters θ. A common choice is to set m(x_i) = 0. At inference time for instances x*, the assumption is that their ground truth f* is jointly Gaussian with y, where K_n = k(X, X|θ) + σ_n² I, K* = k(X, X*|θ), K** = k(X*, X*|θ) for brevity. Then, the posterior predictive distribution has mean and covariance
>
> E[f*|X, y, X*] = K*ᵀ K_n⁻¹ y,  cov[f*|X, X*] = K** − K*ᵀ K_n⁻¹ K*   (5)
>
> Examples for covariance functions are the linear or squared exponential kernel. However, these kernel functions are designed by hand. The idea of deep kernel learning (Wilson et al., 2016) is to learn the kernel function. They propose to use a neural network ϕ to transform the input x to a latent representation which serves as input for the kernel function.
>
> k_deep(x, x′|θ, w) = k(ϕ(x, w), ϕ(x′, w)|θ)   (6)

> [!note] 翻譯
> 我們有一個包含 n 個觀測的訓練集 D = {(x_i, y_i)|i = 1, . . . , n}，其中 y_i 是函數值的含雜訊觀測，y_i = f(x_i) + ε。我們假設雜訊 ε 為加性、獨立同分布的高斯雜訊，變異數為 σ_n²。對於高斯過程 (Rasmussen & Williams, 2006)，我們將 y_i 視為隨機變數，並假設所有 y_i 的聯合分布為多元高斯分布：
>
> y ∼ N (m(X), k(X, X)) .   (2)
>
> 高斯過程完全由其均值函數 m 與共變異數函數（covariance function）k 所決定，並可能依賴於參數 θ。常見的選擇是令 m(x_i) = 0。在推論階段，對於實例 x*，假設其真值 f* 與 y 服從聯合高斯分布，其中為求簡潔記 K_n = k(X, X|θ) + σ_n² I、K* = k(X, X*|θ)、K** = k(X*, X*|θ)。則後驗預測分布的均值與共變異數為
>
> E[f*|X, y, X*] = K*ᵀ K_n⁻¹ y,  cov[f*|X, X*] = K** − K*ᵀ K_n⁻¹ K*   (5)
>
> 共變異數函數的例子包括線性核或平方指數核（squared exponential kernel）。然而，這些核函數皆為人工設計。深度核學習（deep kernel learning）(Wilson et al., 2016) 的想法是學習核函數本身：他們提出使用神經網路 ϕ 將輸入 x 轉換為潛在表徵，作為核函數的輸入。
>
> k_deep(x, x′|θ, w) = k(ϕ(x, w), ϕ(x′, w)|θ)   (6)

---

## 4 Few-Shot Bayesian Optimization | 少樣本貝葉斯最佳化

> [!quote] Original
> Hyperparameter optimization is traditionally either tackled as an optimization problem without prior knowledge about the response function or alternatively as a multi-task or transfer learning problem. In the former, every search basically starts from scratch. In the latter, one or multiple models are trained that attempt to reuse knowledge of the source tasks for the target task.
>
> In this work we will address the problem as a few-shot problem. Given T related source tasks and very few examples of the target task, we want to make reliable predictions. For each of the source tasks we have observations D^(t) = {(x_i^(t), y_i^(t))}_{i=1...n^(t)}, where x_i^(t) is a hyperparameter setting and y_i^(t) is the noisy observations of f^(t)(x_i^(t)), i.e. a validation score of a machine learning model on data set t. In the following we will denote the set of all data points by D := ∪_{t=1}^{T} D^(t). Model-agnostic meta-learning (Finn et al., 2017) has become a popular choice for few-shot learning and we propose to use an adaptation for Gaussian processes (Patacchiola et al., 2020) as a surrogate model within the Bayesian optimization framework. The idea is simple. A deep kernel ϕ is used to learn parameters across tasks such that all its parameters θ and w are task-independent. All task-dependent parameters are kept separate which allows to marginalize its corresponding variable out when solely optimizing for the task-independent parameters. If we assume that the posteriors over θ and w are dominated by their respective maximum likelihood estimates θ̂ and ŵ, we can approximate the posterior predictive distribution by
>
> p(f*|x*, D) = ∫ p(f*|x*, θ, w) p(θ, w|D) dθ, w ≈ p(f*|x*, D, θ̂, ŵ)   (7)

> [!note] 翻譯
> 超參數最佳化傳統上或被視為對響應函數毫無先驗知識的最佳化問題，或被視為多任務（multi-task）或遷移學習問題。前者中每次搜尋基本上都從零開始；後者則訓練一個或多個模型，試圖將來源任務的知識重複利用於目標任務。
>
> 在本文中，我們將此問題視為少樣本問題來處理。給定 T 個相關的來源任務以及目標任務的極少量樣本，我們希望做出可靠的預測。對每個來源任務，我們擁有觀測 D^(t) = {(x_i^(t), y_i^(t))}_{i=1...n^(t)}，其中 x_i^(t) 是一組超參數設定，y_i^(t) 是 f^(t)(x_i^(t)) 的含雜訊觀測，即機器學習模型在資料集 t 上的驗證分數。下文中，我們以 D := ∪_{t=1}^{T} D^(t) 表示所有資料點的集合。模型無關元學習（model-agnostic meta-learning）(Finn et al., 2017) 已成為少樣本學習的熱門選擇，我們提出在貝葉斯最佳化框架內，採用其針對高斯過程的改編版本 (Patacchiola et al., 2020) 作為代理模型。其想法很簡單：使用深度核 ϕ 跨任務學習參數，使其所有參數 θ 與 w 皆與任務無關；所有任務相關參數則被分離保存，如此在僅針對任務無關參數進行最佳化時，即可將對應變數邊際化消去。若假設 θ 與 w 的後驗分布由其各自的最大概似估計 θ̂ 與 ŵ 所主導，則後驗預測分布可近似為
>
> p(f*|x*, D) = ∫ p(f*|x*, θ, w) p(θ, w|D) dθ, w ≈ p(f*|x*, D, θ̂, ŵ)   (7)

---

> [!quote] Original
> We estimate θ̂ and ŵ by maximizing the log marginal likelihood for all tasks.
>
> log p(y^(1), . . . , y^(T)|X^(1), . . . , X^(T), θ, w) = Σ_{t=1}^{T} log p(y^(t)|X^(t), θ, w)   (8)
> ∝ − Σ_{t=1}^{T} [ y^(t)ᵀ (K_n^(t))⁻¹ y^(t) + log |K_n^(t)| ]   (9)
>
> We maximize the marginal likelihood with stochastic gradient ascent (SGA). Each batch contains data for one task. It is important to note that this batch data does not correspond to the data in D^(t), but rather a subset of it. With small batch sizes, this is an efficient way to train the Gaussian process, regardless of how many tasks knowledge is transferred from or how large D^(t) is. It is important to note that there is a correlation between all data per task and not just one batch. Hence the stochastic gradient is a biased estimator of the full gradient. For a long time we lacked the theoretical understanding of how SGA would behave in this situation. Fortunately, recent work (Chen et al., 2020) proved that SGA still successfully converges and restores model parameters in these cases. The results by Chen et al. (2020) guarantee a O(1/K) optimization margin of error, where K is the number of iterations.
>
> The training works as follows. In each iteration, a task t is sampled uniformly at random from all T tasks. Then we sample a batch of training instances uniformly at random from D^(t). Finally, we calculate the log marginal likelihood for this batch (Equation 8) and update the kernel parameters with one step in the direction of the gradient.

> [!note] 翻譯
> 我們透過最大化所有任務的對數邊際概似（log marginal likelihood）來估計 θ̂ 與 ŵ：
>
> log p(y^(1), . . . , y^(T)|X^(1), . . . , X^(T), θ, w) = Σ_{t=1}^{T} log p(y^(t)|X^(t), θ, w)   (8)
> ∝ − Σ_{t=1}^{T} [ y^(t)ᵀ (K_n^(t))⁻¹ y^(t) + log |K_n^(t)| ]   (9)
>
> 我們以隨機梯度上升（stochastic gradient ascent, SGA）最大化邊際概似。每個批次僅包含單一任務的資料。需注意的是，此批次資料並非對應 D^(t) 的全部，而是其子集。在小批次規模下，無論知識遷移來自多少任務、或 D^(t) 有多大，這都是訓練高斯過程的一種高效方式。同時需要注意，每個任務的所有資料之間存在相關性，而非僅限於單一批次之內，因此隨機梯度是完整梯度的有偏估計量。長期以來，我們缺乏對 SGA 在此情況下行為的理論理解。所幸，近期工作 (Chen et al., 2020) 證明了 SGA 在這些情況下仍能成功收斂並復原模型參數。Chen et al. (2020) 的結果保證了 O(1/K) 的最佳化誤差界，其中 K 為迭代次數。
>
> 訓練流程如下：在每次迭代中，自全部 T 個任務中均勻隨機抽取一個任務 t；接著自 D^(t) 均勻隨機抽取一個批次的訓練實例；最後，計算此批次的對數邊際概似（式 8），並沿梯度方向對核參數更新一步。

---

> [!quote] Original
> In hyperparameter optimization, we are only interested in the hyperparameter setting that works best according to a predefined metric of interest. Therefore, only the ranking of the hyperparameter settings is important, not the actual value of the metric. Therefore, we are interested in a surrogate model whose prediction strongly correlate with the response function and the squared error is of little to no interest for us. In practice, however, the minimum / maximum score and the range of values differ significantly between the tasks which makes it challenging to obtain a strongly correlating surrogate model. The most common way to address this problem is to normalize the labels, e.g. by z-normalizing or scaling the labels to [0, 1] per data set. However, this does not fully solve the problem. The main problem is that this label normalization must also be applied to the target task. This is not possible with a satisfactory degree of accuracy, especially when only a few examples are available, since the approximate values for minimum / maximum or mean / standard deviation are insufficiently accurate.

> [!note] 翻譯
> 在超參數最佳化中，我們僅關心依據預先定義之目標指標表現最佳的超參數設定。因此，重要的只是超參數設定之間的排序，而非指標的實際數值。故我們所需的是預測與響應函數強烈相關的代理模型，平方誤差對我們而言幾乎無關緊要。然而在實務上，各任務之間的最小／最大分數與數值範圍差異顯著，使得取得強相關的代理模型頗具挑戰。解決此問題最常見的方式是對標籤進行正規化，例如逐資料集進行 z 正規化或將標籤縮放至 [0, 1]。但這並未完全解決問題：主要困難在於此標籤正規化亦須套用於目標任務，而這無法以令人滿意的精確度達成——尤其在僅有少量樣本時，最小／最大值或均值／標準差的近似值皆不夠準確。

---

> [!quote] Original
> Since our proposed method scales easily to any number of tasks, we can afford to consider another option. We propose a task augmentation strategy that addresses the label normalization issue by randomly scaling the labels for each batch. Since y_min and y_max are the minimum and maximum values for all T tasks, we can generate augmented tasks for each batch B = {x_i, y_i}_{i=1,...,b} ∼ D^(t), where b is the batch size, as follows. A lower and upper limit is sampled for each sample batch,
>
> l ∼ U(y_min, y_max), u ∼ U(y_min, y_max) ,   (10)
>
> such that l < u holds. Then the labels for that batch are scaled to this range,
>
> y ← (y − l) / (u − l) .   (11)
>
> No further changes are required. We summarize this procedure in Algorithm 1. The idea here is to learn a representation that is invariant with respect to various offsets and ranges so that the target task does not require normalization. This strategy is not possible for other hyperparameter optimization methods, since this either increases the training data set size even further and thus becomes computationally impossible (Bardenet et al., 2013; Swersky et al., 2013; Yogatama & Mann, 2014) or thousands of new tasks would have to be generated, which is also is not practical (Springenberg et al., 2016; Perrone et al., 2018; Wistuba et al., 2018; Feurer et al., 2018).
>
> At test time, the posterior predictive distribution or the target task T + 1 is computed according to Equation 5. In practice, the target data set can be very different to the learned prior. For this reason, the deep kernel parameters are fine-tuned on the target task's data for few epochs. Our task augmentation strategy described earlier has resulted in a model that has become invariant for different scales of y. For this reason we do not apply this strategy to the target task T + 1 and only use it for all source tasks 1 to T. We discuss the usefulness of this step in more detail when we discuss the empirical results.

> [!note] 翻譯
> 由於我們提出的方法可輕易擴展至任意數量的任務，我們得以考慮另一種選項。我們提出一種任務增強（task augmentation）策略，透過對每個批次隨機縮放標籤來解決標籤正規化問題。設 y_min 與 y_max 為全部 T 個任務的最小值與最大值，我們可為每個批次 B = {x_i, y_i}_{i=1,...,b} ∼ D^(t)（b 為批次大小）如下生成增強任務。對每個樣本批次抽取上下限：
>
> l ∼ U(y_min, y_max), u ∼ U(y_min, y_max) ,   (10)
>
> 並使 l < u 成立。然後將該批次的標籤縮放至此範圍：
>
> y ← (y − l) / (u − l) .   (11)
>
> 除此之外無需其他改動。我們將此程序總結於 Algorithm 1。其核心思想是學習一個對各種偏移與範圍皆不變的表徵，使目標任務無需正規化。此策略對其他超參數最佳化方法而言並不可行，因為那要麼會進一步擴大訓練資料集規模而導致計算上不可行 (Bardenet et al., 2013; Swersky et al., 2013; Yogatama & Mann, 2014)，要麼必須生成數以千計的新任務，同樣不切實際 (Springenberg et al., 2016; Perrone et al., 2018; Wistuba et al., 2018; Feurer et al., 2018)。
>
> 在測試階段，目標任務 T + 1 的後驗預測分布依式 5 計算。實務上，目標資料集可能與所學先驗差異甚大，因此深度核參數會在目標任務的資料上微調數個訓練週期（epoch）。前述任務增強策略已使模型對 y 的不同尺度具有不變性，因此我們不對目標任務 T + 1 套用該策略，僅將其用於來源任務 1 至 T。此步驟的效用將在討論實證結果時進一步詳述。

---

> [!quote] Original
> Algorithm 1: Few-Shot GP Surrogate
> Input: Learning rates α and β, training data D, kernel k, and neural network ϕ.
> while not done do
>   Sample task t ∼ U({1, . . . , T});
>   Estimate l and u (Equation 10);
>   for bn times do
>     Sample batch B = {(x_i, y_i)}_{i=1,...,b} ∼ D^(t) and scale labels y using l and u;
>     Compute marginal likelihood L on B. (Equation 8);
>     θ ← θ + α∇_θ L;
>     w ← w + β∇_w L;
>   end
> end
>
> [Figure 1: Demonstration of five steps of Bayesian Optimization with FSBO for maximizing a sine wave (blue). One maximum is discovered within only three steps. Expected Improvement has been scaled to improve readability. In black the predictions of the surrogate model. Bottom right are examples of the source tasks. The deep kernel consists of a spectral kernel (Wilson & Adams, 2013) combined with a two-layer neural network (1 → 64 → 64).]

> [!note] 翻譯
> Algorithm 1：少樣本 GP 代理模型
> 輸入：學習率 α 與 β、訓練資料 D、核函數 k、神經網路 ϕ。
> while 未完成 do
>   抽取任務 t ∼ U({1, . . . , T})；
>   估計 l 與 u（式 10）；
>   重複 bn 次：
>     抽取批次 B = {(x_i, y_i)}_{i=1,...,b} ∼ D^(t)，並以 l 與 u 縮放標籤 y；
>     於 B 上計算邊際概似 L（式 8）；
>     θ ← θ + α∇_θ L；
>     w ← w + β∇_w L；
>   end
> end
>
> [圖 1：以 FSBO 進行五步貝葉斯最佳化以求正弦波（藍色）最大值的示範。僅三步內即發現一個最大值。為提升可讀性，期望改進量已經過縮放。黑色為代理模型的預測。右下為來源任務的範例。深度核由頻譜核（spectral kernel）(Wilson & Adams, 2013) 與雙層神經網路（1 → 64 → 64）組合而成。]

---

## 5 A Motivating Example | 動機範例

> [!quote] Original
> We would like to first motivate our method, FSBO, with an example, similar to the one described in Nichol et al. (2018). The aim is to find the argument x that maximizes a one-dimensional sine wave of the form f^(t)(x) = a^(t) sin(x + b^(t)). It is not known that the function is a sine wave and that one only has to determine the amplitude a and phase b. However, information about other sine waves is available to the optimizer which are considered to be related. These source tasks t = 1, . . . , T are generated randomly with a ∼ U(0.1, 5) and b ∼ U(0, 2π). 50 examples (x_i^(t), f^(t)(x_i^(t))) are available for each task t, whereby the points x_i^(t) ∈ [−5, 5] are evenly spaced.
>
> It should be noted at this point that the expected value for each x_i is 0. Thus, training on the data described above leads to a model predicting 0 for every x_i. However, the used meta-learning procedure allows for accurately reconstructing the underlying sine wave after only a few examples (see Figure 1). Provided that the response function of the original task is similar to the target task, this is a very promising technique for accelerating the search for good hyperparameter settings.

> [!note] 翻譯
> 我們首先以一個範例來闡明我們的方法 FSBO 的動機，此範例類似於 Nichol et al. (2018) 所描述者。目標是找出使一維正弦波 f^(t)(x) = a^(t) sin(x + b^(t)) 最大化的引數 x。最佳化器並不知道該函數是正弦波、也不知道只需確定振幅 a 與相位 b；但它可取得其他被視為相關的正弦波資訊。這些來源任務 t = 1, . . . , T 以 a ∼ U(0.1, 5) 與 b ∼ U(0, 2π) 隨機生成。每個任務 t 有 50 個樣本 (x_i^(t), f^(t)(x_i^(t)))，其中資料點 x_i^(t) ∈ [−5, 5] 為均勻間隔。
>
> 此處應注意，每個 x_i 的期望值皆為 0。因此，在上述資料上訓練會得到一個對每個 x_i 都預測 0 的模型。然而，所採用的元學習程序使模型僅需少量樣本即可準確重建底層的正弦波（見圖 1）。只要來源任務的響應函數與目標任務相似，這便是加速搜尋優良超參數設定的一項極具前景的技術。

---

## 6 A Data-Driven Initialization | 資料驅動的初始化

> [!quote] Original
> The proposed few-shot surrogate model requires only a few examples before reliable predictions can be made for the target data set. How do you choose the initial hyperparameter settings? The simplest idea would be to pick them at random or use Latin Hypercube Sampling (LHS) (McKay et al., 1979). Since we already have data from various tasks and a surrogate model that can impute missing values, we propose a data-driven warm start approach. If we evaluate the performance of a hyperparameter optimization algorithm with a loss function L, we use an evolutionary algorithm to find a set with I hyperparameter settings which minimizes this loss on the source tasks.
>
> X^(init) = arg min_{X∈X^I} Σ_{t=1}^{T} L(f^(t), X) = arg min_{X∈X^I} Σ_{t=1}^{T} min_{x∈X} f̃^(t)(x)   (12)
>
> The loss of a set of hyperparameters depends on the response function values for each of the elements. Since these are not available for every x, we approximate f^(t)(x) with the prediction of the surrogate model described in the last section whenever this is necessary.
>
> Arbitrary loss function can be considered. The specific loss function used in our experiments is the normalized regret and is defined on the right side of Equation 12, where f̃^(t) is derived from f^(t) by scaling it to [0, 1] range:
>
> f̃^(t)(x) = (f^(t)(x) − f_min^(t)) / (f_max^(t) − f_min^(t)) ,   (13)
>
> where f_max^(t) and f_min^(t) are the maximum and minimum of f^(t), respectively.

> [!note] 翻譯
> 我們提出的少樣本代理模型只需少量樣本，即可對目標資料集做出可靠預測。那麼，初始的超參數設定該如何選擇？最簡單的想法是隨機挑選，或使用拉丁超立方抽樣（Latin Hypercube Sampling, LHS）(McKay et al., 1979)。由於我們已擁有來自多個任務的資料，以及一個能補插（impute）缺失值的代理模型，我們提出一種資料驅動的暖啟動做法。若以損失函數 L 評估超參數最佳化演算法的表現，我們便使用演化演算法尋找一個包含 I 組超參數設定的集合，使其在來源任務上的損失最小化：
>
> X^(init) = arg min_{X∈X^I} Σ_{t=1}^{T} L(f^(t), X) = arg min_{X∈X^I} Σ_{t=1}^{T} min_{x∈X} f̃^(t)(x)   (12)
>
> 一組超參數集合的損失取決於其中每個元素的響應函數值。由於並非每個 x 都有可用的響應值，必要時我們以上一節所述代理模型的預測來近似 f^(t)(x)。
>
> 任意損失函數皆可考慮。我們實驗中所使用的具體損失函數為正規化遺憾（normalized regret），定義於式 12 的右側，其中 f̃^(t) 由 f^(t) 縮放至 [0, 1] 範圍而得：
>
> f̃^(t)(x) = (f^(t)(x) − f_min^(t)) / (f_max^(t) − f_min^(t)) ,   (13)
>
> 其中 f_max^(t) 與 f_min^(t) 分別為 f^(t) 的最大值與最小值。

---

> [!quote] Original
> The evolutionary algorithm works as follows. We initialize the population with sets containing I random settings, the settings being sampled in proportion to their performance according to the predictions. Precisely, a setting x is sampled proportional to
>
> exp(− min_{t∈{1,...,T}} f̃^(t)(x)) .   (14)
>
> The best sets are then selected to be refined using an evolutionary algorithm. The algorithm randomly chooses either to mutate a set or to perform a crossover operation between two sets. When mutating a set, a setting is removed uniformly at random and a new setting is added proportional to its predicted performance (Figure 2, left). The crossover operation creates a new set and adds elements from the union of the parent sets until the new set has I settings (Figure 2, right). The new set is added to the population. After 100,000 steps, the algorithm is stopped and the best set is used as the set of initial hyperparameter settings. In Figure 4 we compare this warm start initialization with the simple random or LHS initialization.
>
> [Figure 2: Examples for the mutation and crossover operation with I = 3.]

> [!note] 翻譯
> 演化演算法的運作方式如下。我們以包含 I 組隨機設定的集合初始化族群（population），這些設定依其預測表現按比例抽樣。準確而言，設定 x 的抽樣機率正比於
>
> exp(− min_{t∈{1,...,T}} f̃^(t)(x)) .   (14)
>
> 接著選出最佳的集合，以演化演算法加以精煉。演算法隨機選擇對一個集合進行突變（mutation），或在兩個集合之間執行交配（crossover）操作。突變時，均勻隨機移除一組設定，並依預測表現按比例新增一組設定（圖 2 左）。交配操作則建立一個新集合，從父代集合的聯集中加入元素，直到新集合含有 I 組設定為止（圖 2 右）。新集合隨後被加入族群。經過 100,000 步後演算法停止，最佳集合即作為初始超參數設定集。在圖 4 中，我們將此暖啟動初始化與簡單的隨機初始化或 LHS 初始化進行比較。
>
> [圖 2：I = 3 時突變與交配操作的示例。]

---

## 7 Experiments | 實驗

> [!quote] Original
> We used three different optimization problems to compare the different hyperparameter optimization methods: AdaBoost, GLMNet, and SVM. We created the GLMNet and SVM metadata set by downloading the 30 data sets with the most reported hyperparameter settings from OpenML for each problem. The AdaBoost data set is publicly available (Wistuba et al., 2018). The number of settings per data set varies, the number of settings across all tasks for the GLMNet problem is approximately 800,000. See the appendix for more details about the metadata sets. We compare the following list of hyperparameter optimization methods.
>
> **Random Search** This is a simple but strong baseline for hyperparameter optimization (Bergstra & Bengio, 2012). Hyperparameters settings are selected uniformly at random from the search space.
>
> **Gaussian Process (GP)** Bayesian optimization with a Gaussian process as a surrogate model is a standard and strong baseline (Snoek et al., 2012). We use a Matérn 5/2 kernel with ARD and rely on the scikit-optimize implementation. We compare with two variations. The first is the vanilla method, which uses Latin Hypercube Sampling (LHS) with design size of 10 to initialize. The second method uses the warm start (WS) method described in Section 6 to find an initial set of 5 settings to use the knowledge from other data sets.
>
> **RGPE** RGPE (Feurer et al., 2018) is one of the latest examples of methods that use GP ensembles to transfer knowledge across task (Wistuba et al., 2016; Schilling et al., 2016; Wistuba et al., 2018). In this case a GP is trained for each individual task and in a final step the predictions of the GPs are aggregated. These ensembles scale linearly in the number of tasks, but the computation time is still cubic and the space requirement is still quadratic in the number of data points per task. For this reason we can only present results for AdaBoost and not for the other optimization problems, which have significantly more data points.

> [!note] 翻譯
> 我們使用三個不同的最佳化問題來比較各種超參數最佳化方法：AdaBoost、GLMNet 與 SVM。GLMNet 與 SVM 的元資料集（metadata set）由我們自行建立，即針對每個問題自 OpenML 下載回報超參數設定最多的 30 個資料集；AdaBoost 資料集則為公開資料 (Wistuba et al., 2018)。每個資料集的設定數量不一，GLMNet 問題所有任務的設定總數約為 800,000。有關元資料集的更多細節請見附錄。我們比較下列超參數最佳化方法。
>
> **隨機搜尋（Random Search）** 這是超參數最佳化的一個簡單但強力的基線 (Bergstra & Bengio, 2012)：自搜尋空間中均勻隨機選取超參數設定。
>
> **高斯過程（GP）** 以高斯過程為代理模型的貝葉斯最佳化是標準且強力的基線 (Snoek et al., 2012)。我們使用帶 ARD 的 Matérn 5/2 核，並採用 scikit-optimize 的實作。我們比較兩種變體：其一為原始方法，以設計規模為 10 的拉丁超立方抽樣（LHS）初始化；其二使用第 6 節所述之暖啟動（WS）方法，找出 5 組初始設定，以利用來自其他資料集的知識。
>
> **RGPE** RGPE (Feurer et al., 2018) 是使用 GP 集成進行跨任務知識遷移的最新方法之一 (Wistuba et al., 2016; Schilling et al., 2016; Wistuba et al., 2018)。此方法為每個任務各訓練一個 GP，並在最後一步聚合各 GP 的預測。這類集成對任務數量呈線性擴展，但計算時間對每任務資料點數量仍為三次方、空間需求仍為二次方。因此我們僅能呈現 AdaBoost 的結果，無法涵蓋資料點顯著更多的其他最佳化問題。

---

> [!quote] Original
> **MetaBO** Using the acquisition function instead of the surrogate model for transfer learning is another option. MetaBO is the state-of-the-art transfer acquisition function and was presented last year at ICLR (Volpp et al., 2020). We use the implementation provided by the authors.
>
> **ABLR** The state-of-the-art surrogate model for scalable hyperparameter transfer learning (Perrone et al., 2018). This method uses a multi-task GP which combines a linear kernel with a neural network and scales to very large data sets. Transfer learning is achieved by sharing the neural network parameters across different tasks. By ABLR (WS) we are referring to a version of ABLR that uses the same warm start as FSBO.
>
> **Multi-Head GPs** This closely resembles ABLR but uses the same deep kernel as our proposed method. The main difference from our proposed method is that it is using a GP for every task, only shares the neural network across tasks, and uses standard stochastic gradient ascent.
>
> **Few-Shot Bayesian Optimization (FSBO)** Our proposed method described in Section 4. The deep kernel is composed of a two-layer neural network (128 → 128) with ReLU activations and a squared-exponential kernel. We use the Adam optimizer with learning rate 10⁻³ and a batch size of fifty. The warm start length is five.
>
> The experiments are repeated ten times and evaluated in a leave-one-task-out cross-validation. This means that all transfer learning methods use one task as the target task and all other tasks as source tasks. For AdaBoost we use the same train/test split as used by Volpp et al. (2020) instead. We report the aggregated results for all tasks within one problem class with respect to the mean of normalized regrets. The normalized regret for a task is obtained by first scaling the response function values between 0 and 1 before calculating the regret (Wistuba et al., 2018), i.e.
>
> r̃^(t) = f̃^(t)(x_min) − f̃_min^(t) ,   (15)
>
> where f̃^(t) is the normalized response function (Equation 13), f̃_min^(t) is the global minimum for task t, and x_min is the best discovered hyperparameter setting by the optimizer.

> [!note] 翻譯
> **MetaBO** 以採集函數而非代理模型進行遷移學習是另一種選項。MetaBO 是最先進的遷移採集函數，於去年在 ICLR 發表 (Volpp et al., 2020)。我們使用作者提供的實作。
>
> **ABLR** 可擴展超參數遷移學習領域最先進的代理模型 (Perrone et al., 2018)。此方法使用結合線性核與神經網路的多任務 GP，可擴展至非常大的資料集；遷移學習透過在不同任務間共享神經網路參數來達成。ABLR (WS) 指的是使用與 FSBO 相同暖啟動的 ABLR 版本。
>
> **Multi-Head GPs** 此方法與 ABLR 極為相似，但使用與我們所提方法相同的深度核。與我們方法的主要差異在於：它為每個任務各使用一個 GP，僅在任務之間共享神經網路，並使用標準的隨機梯度上升。
>
> **少樣本貝葉斯最佳化（FSBO）** 即第 4 節所述我們提出的方法。深度核由帶 ReLU 激活的雙層神經網路（128 → 128）與平方指數核組成。我們使用 Adam 最佳化器，學習率為 10⁻³，批次大小為 50，暖啟動長度為 5。
>
> 實驗重複十次，並以留一任務交叉驗證（leave-one-task-out cross-validation）評估，亦即所有遷移學習方法皆以某一任務為目標任務、其餘任務為來源任務。AdaBoost 則改用 Volpp et al. (2020) 所採用的相同訓練／測試切分。我們以正規化遺憾之均值，報告同一問題類別內所有任務的彙總結果。任務的正規化遺憾之計算方式為：先將響應函數值縮放至 0 與 1 之間，再計算遺憾 (Wistuba et al., 2018)，即
>
> r̃^(t) = f̃^(t)(x_min) − f̃_min^(t) ,   (15)
>
> 其中 f̃^(t) 為正規化響應函數（式 13），f̃_min^(t) 為任務 t 的全域最小值，x_min 為最佳化器所發現的最佳超參數設定。

---

### 7.1 Hyperparameter Optimization | 超參數最佳化

> [!quote] Original
> We conduct experiments on three different metadata sets and report the aggregated mean of normalized regrets in Table 1. The best results are in bold. Results that are not significantly worse than the best are in italics. We determined the significance using the Wilcoxon signed rank test with a confidence level of 95%. Results are reported after every 33 trials of Bayesian optimization for GLMNet and SVM. Since AdaBoost has fewer test examples, we report its results in shorter intervals.
>
> Our proposed FSBO method outperforms all other transfer methods in all three tasks. Results are significantly better but in the case of AdaBoost where GP (WS) achieves similar but on average worse results. The results obtained for MetaBO are the worst. We contacted Volpp et al. (2020) and they confirmed that our results are obtained correctly. The problem is apparently that the Reinforcement Learning method does not work well for larger number of trials. We provide a longer discussion in the appendix. Also ABLR and the very related Multi-Head GP are not performing very well. As we show in the next section, one possible reason for this might be because the neural network parameters are fixed which will prohibit a possibly required adaptation to the target task.
>
> The vanilla GP and its transfer variant that uses a warm start turn out to be among the strongest baselines. This is in particular true for the warm start version which is the second best method. This simple baseline of knowledge transfer is often not taken into account when comparing transfer surrogates, although it is easy to implement. Due to the very competitive results, we recommend using it as a standard baseline to assess the usefulness of new transfer surrogates.
>
> [Table 1: FSBO obtains better results for all hyperparameter optimization problems. The best results are in bold. Results that are not significantly worse than the best are in italics. Used initialization in parentheses, (LHS) - Latin Hypercube Sampling, (WS) - Warm Start.]

> [!note] 翻譯
> 我們在三個不同的元資料集上進行實驗，並在表 1 中報告正規化遺憾的彙總均值。最佳結果以粗體標示；與最佳結果無顯著差距者以斜體標示。顯著性以 Wilcoxon 符號秩檢定（Wilcoxon signed rank test）在 95% 信賴水準下判定。GLMNet 與 SVM 的結果於貝葉斯最佳化每 33 次試驗後報告；由於 AdaBoost 的測試樣本較少，其結果以較短的間隔報告。
>
> 我們提出的 FSBO 方法在全部三個任務上皆勝過所有其他遷移方法。結果均為顯著更佳，唯一例外是 AdaBoost，其中 GP (WS) 取得相近但平均而言較差的結果。MetaBO 的結果最差。我們聯繫了 Volpp et al. (2020)，他們確認我們的結果取得無誤。問題顯然在於該強化學習（Reinforcement Learning）方法在試驗次數較多時表現不佳；我們在附錄中提供更長的討論。ABLR 及與之極為相關的 Multi-Head GP 表現亦不甚理想。如下一節所示，一個可能的原因是其神經網路參數固定不變，因而妨礙了對目標任務可能必需的適應。
>
> 原始 GP 及其使用暖啟動的遷移變體被證明是最強的基線之一，暖啟動版本尤其如此，是整體第二佳的方法。這個簡單的知識遷移基線雖然容易實作，卻常在比較遷移代理模型時被忽略。鑑於其極具競爭力的結果，我們建議將其作為評估新遷移代理模型效用的標準基線。
>
> [表 1：FSBO 在所有超參數最佳化問題上均取得更佳結果。最佳結果以粗體標示；與最佳結果無顯著差距者以斜體標示。括號內為所用初始化方式：(LHS) 拉丁超立方抽樣、(WS) 暖啟動。]

---

### 7.2 Component Contributions | 組件貢獻

> [!quote] Original
> In this section we highlight the various components that make a significant difference to ABLR. The results for each setup are reported in Figure 3. The Multi-Head GP is a surrogate model that shares the neural network between tasks but uses a separate Gaussian process for each task. It closely resembles the idea of ABLR but is closer to our proposed implementation of FSBO. Starting from this configuration, we add various components that will eventually lead to our proposed model FSBO. We consider Multi-Head GP (WS), a version that additionally uses the warm start method described in Section 6 instead of a random initialization. Multi-Head GP (fine-tune) not only updates the kernel parameters when a new observation is received but also fine-tunes the parameters of the neural network. Finally, FSBO is our proposed method, which uses only one Gaussian process for all tasks. We see that all Multi-Head GP versions have a problem adapting to the target tasks efficiently. Fine-tuning the deep kernel is an important part of learning FSBO. Although FSBO outperforms all Multi-Head GP versions without fine-tuning, the additional use of it makes for a significant improvement. We analyzed the reason for this in more detail. We observed that the learned neural network provides a strong prior that leads to strong exploitation behavior. Fine-tuning prevents this and thus ensures that the method does not get stuck in local optima.
>
> [Figure 3: Comparison of the contribution of the various FSBO components to the final solution. Each component makes its own orthogonal contribution.]

> [!note] 翻譯
> 本節著重說明相對於 ABLR 產生顯著差異的各項組件。各設定的結果報告於圖 3。Multi-Head GP 是一種在任務之間共享神經網路、但為每個任務使用獨立高斯過程的代理模型；它與 ABLR 的想法極為相似，但更接近我們所提出的 FSBO 實作。從此配置出發，我們逐步加入各項組件，最終形成我們提出的模型 FSBO。我們考慮 Multi-Head GP (WS)，即額外使用第 6 節所述暖啟動方法（而非隨機初始化）的版本；Multi-Head GP (fine-tune) 則在收到新觀測時不僅更新核參數，亦微調神經網路的參數。最後，FSBO 是我們提出的方法，對所有任務僅使用一個高斯過程。我們觀察到，所有 Multi-Head GP 版本在高效適應目標任務方面皆有困難。微調深度核是 FSBO 學習過程的重要一環：儘管未經微調的 FSBO 已勝過所有 Multi-Head GP 版本，額外加入微調仍帶來顯著改進。我們更深入分析了其原因：所學得的神經網路提供了強烈的先驗，導致過強的利用（exploitation）行為；微調可防止此現象，從而確保方法不會陷入局部最優。
>
> [圖 3：FSBO 各組件對最終解法貢獻的比較。每個組件各自做出正交的貢獻。]

---

### 7.3 Data-Driven Warm Start | 資料驅動的暖啟動

> [!quote] Original
> The use of a warm start is not the main contribution of this paper but it is interesting to explore further nonetheless. First of all, for some methods this is the only differentiating factor. We compare our suggested warm start initialization with a random and a Latin hypercube sampling (LHS) initialization in Figure 4. Considering that GP (WS) always performed better than GP (LHS) in Table 1, one would expect that the warm start itself also performs better than LHS. While this is the case for GLMNet and SVM, there are no significant differences for the AdaBoost problem. Our assumption is that in this case the warm start might not have always found good settings as part of the initialization, it still explored areas in the search space close to the optimum. This would facilitate finding a better solution in one of the subsequent steps of Bayesian optimization.
>
> [Figure 4: The warm start initialization yields the best results on GLMNet and SVM for all initialization lengths. For AdaBoost it is comparable to LHS.]

> [!note] 翻譯
> 暖啟動的使用並非本文的主要貢獻，但仍值得進一步探究。首先，對某些方法而言，這是唯一的差異因素。我們在圖 4 中將所建議的暖啟動初始化與隨機初始化及拉丁超立方抽樣（LHS）初始化進行比較。考量到表 1 中 GP (WS) 始終優於 GP (LHS)，可以預期暖啟動本身也應優於 LHS。GLMNet 與 SVM 的確如此，但在 AdaBoost 問題上並無顯著差異。我們推測，此情形下暖啟動或許未必總能在初始化階段找到優良設定，但它仍探索了搜尋空間中鄰近最優解的區域，這有助於在後續貝葉斯最佳化的某一步驟中找到更佳的解。
>
> [圖 4：在所有初始化長度下，暖啟動初始化於 GLMNet 與 SVM 上均取得最佳結果；於 AdaBoost 上則與 LHS 相當。]

---

## 8 Conclusions | 結論

> [!quote] Original
> In this work, we propose to rethink hyperparameter optimization as a few-shot learning problem in which we train a shared deep surrogate model to quickly adapt (with few response evaluations) to the response function of a new task. We propose the use of a deep kernel network for a Gaussian process surrogate that is meta-learned in an end-to-end fashion in order to jointly approximate the response functions of a collection of training data sets. This few-shot surrogate model is used for two different purposes. First, we use it in combination with an evolutionary algorithm in order to estimate a data-driven warm start initialization for Bayesian optimization. Second, we use it directly for Bayesian optimization. In our empirical evaluation on three hyperparameter optimization problems, we observe significantly better results than with state-of-the-art methods that use transfer learning.

> [!note] 翻譯
> 在本文中，我們提出將超參數最佳化重新思考為一個少樣本學習問題：訓練一個共享的深度代理模型，使其能以極少的響應評估快速適應新任務的響應函數。我們提出將深度核網路用於高斯過程代理模型，並以端到端方式進行元學習，以聯合近似一組訓練資料集的響應函數。此少樣本代理模型被用於兩種不同目的：其一，與演化演算法結合，為貝葉斯最佳化估計資料驅動的暖啟動初始化；其二，直接用於貝葉斯最佳化。在三個超參數最佳化問題上的實證評估中，我們觀察到其結果顯著優於採用遷移學習的最先進方法。

---

## Acknowledgments | 致謝

> [!quote] Original
> This work has been supported by European Union's Horizon 2020 research and innovation programme under grant number 951911 - AI4Media. Prof. Grabocka is thankful to the Eva Mayr-Stihl Foundation for their generous research grant.

> [!note] 翻譯
> 本研究由歐盟 Horizon 2020 研究與創新計畫（補助編號 951911 - AI4Media）支持。Grabocka 教授感謝 Eva Mayr-Stihl 基金會慷慨的研究補助。

---

## References | 參考文獻

> [!note] 翻譯
> References omitted / 參考文獻略。

---

## Appendix | 附錄（附錄僅節譯）

### A Hyperparameter Optimization | 超參數最佳化

> [!quote] Original
> In the main paper we report the normalized regret after every 33 trials of Bayesian optimization in a table. This allows us to also report statistical significance. In Figure 5 we show results for all 100 trials. The conclusions remain unchanged. FSBO provides consistently the best results.

> [!note] 翻譯
> 正文中我們以表格報告貝葉斯最佳化每 33 次試驗後的正規化遺憾，這使我們也能報告統計顯著性。圖 5 展示了全部 100 次試驗的結果，結論維持不變：FSBO 始終提供最佳結果。

---

### B Challenges with MetaBO | MetaBO 的挑戰

> [!quote] Original
> The results reported in the main paper for MetaBO seem not to align with the numbers reported by Volpp et al. (2020), in particular for AdaBoost. In order to understand this behavior, we conducted a deeper analysis for the AdaBoost metadata set which was used in the evaluation of Volpp et al. (2020) as well. Using the authors' code, we executed the same scripts used by the authors to report their results for T = 15 trials. Furthermore, we changed two lines in the scripts to train MetaBO for T = 50, the number of trials considered for AdaBoost in our experiment. [...] We observe that the policy learned for T = 15 has a lower regret than the one for T = 50 even though it is only trying 15 compared to 50 trials. In the right plot we show the results compared to a simple random search baseline. The results for T = 15 are in line with those reported by Volpp et al. (2020). We contacted the authors and they confirmed that we use their code correctly. Their explanation is that the increase of episode length makes this a harder problem for Reinforcement Learning. This results in worse results for T = 50 and even worse for T = 100 in case of the GLMNet and SVM problem. They proposed to randomly vary T between 5 and 50 during the training phase. We also considered this training protocol and report the results in Figure 6. This improves over training only with T = 50 but does not improve over a simple random search.

> [!note] 翻譯
> 正文中報告的 MetaBO 結果似乎與 Volpp et al. (2020) 所報告的數字不符，尤以 AdaBoost 為甚。為理解此現象，我們對 AdaBoost 元資料集（Volpp et al. (2020) 的評估亦使用此資料集）進行了更深入的分析。我們使用作者的程式碼，執行作者用於報告 T = 15 次試驗結果的相同腳本；此外，我們修改腳本中的兩行，以 T = 50（即本實驗中 AdaBoost 所採用的試驗次數）訓練 MetaBO。（實驗細節略）我們觀察到，T = 15 學得的策略之遺憾低於 T = 50 者，儘管前者僅嘗試 15 次而非 50 次試驗。右圖顯示與簡單隨機搜尋基線的比較結果；T = 15 的結果與 Volpp et al. (2020) 的報告一致。我們聯繫了作者，他們確認我們正確使用了其程式碼。他們的解釋是：回合長度（episode length）的增加使此問題對強化學習而言更加困難，導致 T = 50 的結果較差，而在 GLMNet 與 SVM 問題上 T = 100 的結果更差。他們建議在訓練階段將 T 於 5 至 50 之間隨機變動。我們亦考慮了此訓練協定，並將結果報告於圖 6：這比僅以 T = 50 訓練有所改善，但仍未優於簡單的隨機搜尋。

---

### C Metadata | 元資料

> [!quote] Original
> We created the GLMNet and SVM metadata using OpenML. We chose the 30 OpenML flows with the most observations. We also limited ourselves to the uploaded results from user with ID 2702 (OpenML Bot R), who uploaded the majority of all runs, to ensure that the metadata was generated under similar circumstances. In some cases the choice of a single hyperparameter is not indicated. In such a case, we assume that the default value was used. The GLMNet metadata has two continuous hyperparameters: the elastic-net mixing parameter α ∈ [0, 1] and the regularization parameter λ ∈ [2⁻¹⁰, 2¹⁰]. The SVM metadata has two mandatory hyperparameters and two conditional hyperparameters. The kernel (linear, polynomial or RBF) and the continuous trade-off parameter C ∈ [2⁻¹⁰, 2¹⁰] must always be selected. The degree d ∈ {2, 3, 4, 5} is only considered for the polynomial kernel and the bandwidth γ ∈ [2⁻¹⁰, 2¹⁰] is only considered for the RBF kernel.
>
> We further use the AdaBoost metadata which has been used to evaluate MetaBO (Volpp et al., 2020). According to Wistuba et al. (2015b), this metadata was created using AdaBoost (Kégl & Busa-Fekete, 2009) with decision products as weak learners. The authors designed a grid over the two continuous hyperparameters: the number of iterations and the number of product terms. [...] The metadata was created by running a grid search on 50 different classification data sets, using classification accuracy as an objective. Compared to the other two metadata sets, this one is significantly smaller. Its use is mainly motivated by comparing to MetaBO under the same circumstances as used to evaluate MetaBO. For that reason, we do not use the leave-one-data-set-out cross-validation protocol but instead use the same fixed split created by Volpp et al. (2020).

> [!note] 翻譯
> 我們使用 OpenML 建立 GLMNet 與 SVM 的元資料，選取觀測數最多的 30 個 OpenML flow，並將範圍限定於 ID 為 2702 的使用者（OpenML Bot R，其上傳了絕大多數的執行結果）所上傳之結果，以確保元資料是在相似條件下產生。某些情況下單一超參數的取值未被標明，此時我們假設使用了預設值。GLMNet 元資料有兩個連續超參數：elastic-net 混合參數 α ∈ [0, 1] 與正則化參數 λ ∈ [2⁻¹⁰, 2¹⁰]。SVM 元資料有兩個必要超參數與兩個條件超參數：核函數（線性、多項式或 RBF）與連續權衡參數 C ∈ [2⁻¹⁰, 2¹⁰] 必須選定；次數 d ∈ {2, 3, 4, 5} 僅在多項式核時考慮，頻寬 γ ∈ [2⁻¹⁰, 2¹⁰] 僅在 RBF 核時考慮。
>
> 我們進一步使用曾用於評估 MetaBO 的 AdaBoost 元資料 (Volpp et al., 2020)。根據 Wistuba et al. (2015b)，該元資料是以決策乘積（decision products）為弱學習器的 AdaBoost (Kégl & Busa-Fekete, 2009) 所建立。作者針對兩個連續超參數（迭代次數與乘積項數）設計了網格。（網格取值細節略）此元資料透過在 50 個不同分類資料集上執行網格搜尋、以分類準確率為目標而建立。與其他兩個元資料集相比，此資料集顯著較小；使用它的主要動機是在與評估 MetaBO 時相同的條件下進行比較。因此，我們不採用留一資料集交叉驗證協定，而是使用 Volpp et al. (2020) 所建立的相同固定切分。（表 3 元資料集統計數據略）

---

### D Meta-Learning with Reptile | 以 Reptile 進行元學習

> [!quote] Original
> In principle our few-shot surrogate can be combined with any model-agnostic meta-learning approach. In this section we compare our proposed meta-learning approach against Reptile (Nichol et al., 2018), a first-order approximation of MAML. We use the same hyperparameters and data augmentation as described in Algorithm 1. Reptile introduces a new hyperparameter, an outer learning rate. We set it to 0.1 and decay it linearly to 0. As summarized in Table 2, FSBO with the meta-learning approach described in Algorithm 1 is either better or not significantly worse than its variation with Reptile.

> [!note] 翻譯
> 原則上，我們的少樣本代理模型可與任何模型無關元學習方法結合。本節將我們提出的元學習方法與 Reptile (Nichol et al., 2018)（MAML 的一階近似）進行比較。我們使用與 Algorithm 1 所述相同的超參數與資料增強。Reptile 引入了一個新的超參數——外層學習率；我們將其設為 0.1 並線性衰減至 0。如表 2 所總結，採用 Algorithm 1 所述元學習方法的 FSBO，相較其 Reptile 變體，或表現更佳，或無顯著劣勢。
