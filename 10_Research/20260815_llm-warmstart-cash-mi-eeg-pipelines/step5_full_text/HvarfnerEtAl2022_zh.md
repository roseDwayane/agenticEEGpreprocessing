---
citation_key: "HvarfnerEtAl2022"
title: "πBO: Augmenting Acquisition Functions with User Beliefs for Bayesian Optimization"
authors: "Carl Hvarfner; Daniel Stoll; Artur L. F. Souza; M. Lindauer; Frank Hutter; Luigi Nardi"
year: 2022
doi: "10.48550/arxiv.2204.11051"
source: "arXiv (2204.11051)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "2204.11051"
conversion: "pdftotext -layout (automated)"
language: "bilingual-en-zh"
---

# πBO: Augmenting Acquisition Functions with User Beliefs for Bayesian Optimization | πBO：以使用者信念增強貝葉斯最佳化的擷取函數

> [!abstract] 重點摘要
> - 提出 πBO——一種概念上極為簡潔的擷取函數（acquisition function）廣義化方法，讓使用者以機率分布 π(x) 的形式，把「最佳解可能位於何處」的先驗信念（prior beliefs）注入貝葉斯最佳化（Bayesian optimization, BO）。
> - 核心機制：以衰減的先驗加權擷取函數 α_{π,n}(x, D_n) = α(x, D_n)·π(x)^{β/n}——先驗初期主導點的選擇，隨迭代數 n 增加而衰減至均勻分布，使 πBO 逐漸回歸原味 BO 的行為；在 BO 主迴圈中僅需修改一行程式碼即可實作。
> - 理論保證：當 πBO 搭配期望改進（Expected Improvement, EI）時，證明其損失可被原味 EI 損失乘上因子 C_{π,n} = (max π / min π)^{β/n} 所上界，且漸近收斂率與 EI 相同——無論先驗好壞皆以標準速率收斂。
> - 相較於 BOPrO、BOWS 等既有先驗引導方法，πBO 不受限於特定擷取函數或代理模型（surrogate model），可直接整合進 Spearmint、HyperMapper、SMAC 等既有框架，並支援 EI、PI、UCB、TS。
> - 實驗涵蓋合成函數、Profet 代理基準、OpenML MLP 調校與兩項深度學習任務：πBO 受益於良好先驗、能自誤導性先驗中恢復；在 ImageNette-128 上相對原味 BO 達成 12.5× 的達標時間（time-to-accuracy）加速，並創下該管線新的最先進驗證準確率（94.14%）。
> - 對慣於手動調參的實務工作者而言，πBO 是連接傳統調參直覺與 BO 的直觀橋樑。

---

## Abstract | 摘要

> [!quote] Original
> Bayesian optimization (BO) has become an established framework and popular tool for hyperparameter optimization (HPO) of machine learning (ML) algorithms. While known for its sample-efficiency, vanilla BO can not utilize readily available prior beliefs the practitioner has on the potential location of the optimum. Thus, BO disregards a valuable source of information, reducing its appeal to ML practitioners. To address this issue, we propose πBO, an acquisition function generalization which incorporates prior beliefs about the location of the optimum in the form of a probability distribution, provided by the user. In contrast to previous approaches, πBO is conceptually simple and can easily be integrated with existing libraries and many acquisition functions. We provide regret bounds when πBO is applied to the common Expected Improvement acquisition function and prove convergence at regular rates independently of the prior. Further, our experiments show that πBO outperforms competing approaches across a wide suite of benchmarks and prior characteristics. We also demonstrate that πBO improves on the state-of-the-art performance for a popular deep learning task, with a 12.5× time-to-accuracy speedup over prominent BO approaches.

> [!note] 翻譯
> 貝葉斯最佳化（Bayesian optimization, BO）已成為機器學習（ML）演算法超參數最佳化（hyperparameter optimization, HPO）的成熟框架與流行工具。BO 雖以樣本效率著稱，但原味（vanilla）BO 無法利用實務工作者對最佳解可能位置所持有的、唾手可得的先驗信念。因此，BO 忽視了一項寶貴的資訊來源，降低了它對 ML 從業者的吸引力。為解決此問題，我們提出 πBO——一種擷取函數（acquisition function）的廣義化方法，將使用者以機率分布形式提供之關於最佳解位置的先驗信念納入其中。與先前方法相比，πBO 概念簡潔，可輕鬆整合進既有函式庫與多種擷取函數。我們給出 πBO 應用於常見的期望改進（Expected Improvement, EI）擷取函數時的遺憾值（regret）上界，並證明其以標準速率收斂，且與先驗無關。此外，實驗顯示 πBO 在廣泛的基準與各種先驗特性下皆優於競爭方法。我們亦證明 πBO 在一項流行的深度學習任務上超越最先進表現，相較知名 BO 方法達成 12.5× 的達標時間（time-to-accuracy）加速。

---

## 1 Introduction | 引言

> [!quote] Original
> The optimization of expensive black-box functions is a prominent task, arising across a wide range of applications. Bayesian optimization (BO) is a sample-efficient approach to cope with this task, and has been successfully applied to various problem settings, including hyperparameter optimization (HPO) (Snoek et al., 2012), neural architecture search (NAS) (Ru et al., 2021), joint NAS and HPO (Zimmer et al., 2021), algorithm configuration (Hutter et al., 2011), hardware design (Nardi et al., 2019), robotics (Calandra et al., 2014), and the game of Go (Chen et al., 2018).
>
> Despite the demonstrated effectiveness of BO for HPO (Bergstra et al., 2011; Turner et al., 2021), its adoption among practitioners remains limited. In a survey covering NeurIPS 2019 and ICLR 2020 (Bouthillier & Varoquaux, 2020), manual search was shown to be the most prevalent tuning method, with BO accounting for less than 7% of all tuning efforts. As the understanding of hyperparameter settings in deep learning (DL) models increase (Smith, 2018), so too does the tuning proficiency of practitioners (Anand et al., 2020). As previously displayed (Smith, 2018; Anand et al., 2020; Souza et al., 2021; Wang et al., 2019), this knowledge manifests in choosing single configurations or regions of hyperparameters that presumably yield good results, demonstrating a belief over the location of the optimum. BO's deficit to properly incorporate said beliefs is a reason why practitioners prefer manual search to BO (Wang et al., 2019), despite its documented shortcomings (Bergstra & Bengio, 2012). To improve the usefulness of automated HPO approaches for ML practitioners, the ability to incorporate such knowledge is pivotal.

> [!note] 翻譯
> 最佳化評估成本高昂的黑盒函數是一項重要任務，廣泛出現在各種應用中。貝葉斯最佳化（BO）是應對此任務的高樣本效率方法，已成功應用於多種問題情境，包括超參數最佳化（HPO）(Snoek et al., 2012)、神經架構搜尋（NAS）(Ru et al., 2021)、NAS 與 HPO 聯合最佳化 (Zimmer et al., 2021)、演算法組態 (Hutter et al., 2011)、硬體設計 (Nardi et al., 2019)、機器人學 (Calandra et al., 2014) 以及圍棋 (Chen et al., 2018)。
>
> 儘管 BO 在 HPO 上的有效性已獲驗證 (Bergstra et al., 2011; Turner et al., 2021)，其在實務工作者間的採用率仍然有限。一項涵蓋 NeurIPS 2019 與 ICLR 2020 的調查 (Bouthillier & Varoquaux, 2020) 顯示，手動搜尋是最普遍的調參方法，而 BO 僅占所有調參工作的不到 7%。隨著對深度學習（DL）模型超參數設定的理解加深 (Smith, 2018)，從業者的調參熟練度也隨之提升 (Anand et al., 2020)。如先前研究所示 (Smith, 2018; Anand et al., 2020; Souza et al., 2021; Wang et al., 2019)，這種知識體現在選擇被認為可能產生良好結果的單一組態或超參數區域上——這即是對最佳解位置所持的信念。BO 無法妥善納入此類信念，是從業者寧選手動搜尋而非 BO 的原因之一 (Wang et al., 2019)，儘管手動搜尋的缺陷早有文獻記載 (Bergstra & Bengio, 2012)。要提升自動化 HPO 方法對 ML 從業者的實用性，納入此類知識的能力至關重要。

> [!quote] Original
> Well-established BO frameworks (Snoek et al., 2012; Hutter et al., 2011; The GPyOpt authors, 2016; Kandasamy et al., 2020; Balandat et al., 2020) support user input to a limited extent, such as by biasing the initial design, or by narrowing the search space; however, this type of hard prior can lead to poor performance by missing important regions. BO also supports a prior over functions p(f) via the Gaussian Process kernel. However, this option for injecting knowledge is not aligned with the knowledge that experts possess: they often know which ranges of hyperparameter values tend to work best (Perrone et al., 2019; Smith, 2018; Wang et al., 2019), and are able to specify a probability distribution to quantify these priors. For example, many users of the Adam optimizer (Kingma & Ba, 2015) know that its best learning rate is often in the vicinity of 1 × 10−3. In practice, DL experiments are typically conducted in a low-budget setting of less than 50 full model trainings (Bouthillier & Varoquaux, 2020). As such, practitioners want to exploit their knowledge efficiently without wasting early model trainings on configurations they expect to likely perform poorly. Unfortunately, this suits standard BO poorly, as BO requires a moderate number of function evaluations to learn about the response surface and make informed decisions that outperform random search.
>
> While there is a demand to increase knowledge injection possibilities to further the adoption of BO, the concept of encoding prior beliefs over the location of an optimum is still rather novel: while there are some initial works (Ramachandran et al., 2020; Li et al., 2020; Souza et al., 2021), no approach exists so far that allows the integration of arbitrary priors and offers flexibility in the choice of acquisition function; theory is also lacking. We close this gap by introducing a novel, remarkably simple, approach for injecting arbitrary prior beliefs into BO that is easy to implement, agnostic to the surrogate model used and converges at standard BO rates for any choice of prior.

> [!note] 翻譯
> 成熟的 BO 框架 (Snoek et al., 2012; Hutter et al., 2011; The GPyOpt authors, 2016; Kandasamy et al., 2020; Balandat et al., 2020) 對使用者輸入的支援有限，例如偏置初始設計，或縮窄搜尋空間；然而，這類「硬性先驗」（hard prior）可能因錯失重要區域而導致糟糕的表現。BO 亦可透過高斯過程（Gaussian Process）核函數支援對函數本身的先驗 p(f)。然而，這種知識注入方式與專家實際擁有的知識並不對齊：專家往往知道哪些超參數取值範圍傾向於表現最佳 (Perrone et al., 2019; Smith, 2018; Wang et al., 2019)，並能以機率分布來量化這些先驗。例如，許多 Adam 最佳化器 (Kingma & Ba, 2015) 的使用者知道，其最佳學習率通常在 1 × 10−3 附近。實務上，DL 實驗通常在少於 50 次完整模型訓練的低預算情境下進行 (Bouthillier & Varoquaux, 2020)。因此，從業者希望高效利用其知識，避免把早期的模型訓練浪費在他們預期表現不佳的組態上。遺憾的是，這與標準 BO 格格不入，因為 BO 需要相當數量的函數評估才能學習響應曲面，並做出優於隨機搜尋的明智決策。
>
> 儘管市場對增加知識注入途徑、進一步推廣 BO 的需求確實存在，「對最佳解位置的先驗信念進行編碼」這一概念仍相當新穎：雖已有若干初步工作 (Ramachandran et al., 2020; Li et al., 2020; Souza et al., 2021)，但迄今尚無方法既允許整合任意先驗、又在擷取函數的選擇上保有彈性；理論亦付之闕如。我們以一種新穎且極為簡潔的方法填補此缺口——將任意先驗信念注入 BO，其易於實作、與所用代理模型無關，且對任何先驗選擇皆以標準 BO 速率收斂。

> [!quote] Original
> **Our contributions** After discussing our problem setting, related work, and background (Section 2), we make the following contributions:
> 1. We introduce πBO, a novel generalization of myopic acquisition functions that accounts for user-specified prior distributions over possible optima, is demonstrably simple-to-implement, and can be easily combined with arbitrary surrogate models (Section 3.1 & 3.2);
> 2. We formally prove that πBO inherits the theoretical properties of the well-established Expected Improvement acquisition function (Section 3.3);
> 3. We demonstrate on a broad range of established benchmarks and in DL case studies that πBO can yield 12.5× time-to-accuracy speedup over vanilla BO (Section 4).

> [!note] 翻譯
> **本文貢獻。** 在討論問題設定、相關研究與背景（第 2 節）之後，我們做出以下貢獻：
> 1. 提出 πBO——短視型（myopic）擷取函數的新穎廣義化，能納入使用者指定之關於可能最佳解的先驗分布，實作顯著簡單，且可輕鬆與任意代理模型結合（第 3.1 與 3.2 節）；
> 2. 形式化證明 πBO 繼承了成熟的期望改進（EI）擷取函數的理論性質（第 3.3 節）；
> 3. 在廣泛的既有基準與 DL 案例研究中證明，πBO 相較原味 BO 可帶來 12.5× 的達標時間加速（第 4 節）。

---

## 2 Background and Related Work | 背景與相關研究

### 2.1 Black-box Optimization | 黑盒最佳化

> [!quote] Original
> We consider the problem of optimizing a black-box function f across a set of feasible inputs X ⊂ R^d:
>
> x* ∈ arg min_{x∈X} f(x).  (1)
>
> We assume that f(x) is expensive to evaluate, and can potentially only be observed through a noisy estimate, y. In this setting, we wish to minimize f in an efficient manner, typically adhering to a budget which sets a cap on the number of points that can be evaluated.
>
> **Black-Box Optimization with Probabilistic User Beliefs** In our work, we consider an augmented version of the optimization problem in Eq. (1), where we have access to user beliefs in the form of a probability distribution on the location of the optimum. Formally, we define the problem of black-box optimization with probabilistic user beliefs as solving Eq. (1), given a user-specified prior probability on the location of the optimum defined as
>
> π(x) = P(f(x) = min_{x′∈X} f(x′)),  (2)
>
> where regions that the user expects to likely to contain an optimum will have a high value. We note that, without loss of generality, we require π to be strictly positive on all of X, i.e., any point in the search space might be an optimum. Since the user belief π(x) can be inaccurate or even misleading, optimizing Eq. (1) given (2) is a challenging problem.

> [!note] 翻譯
> 我們考慮在可行輸入集合 X ⊂ R^d 上最佳化黑盒函數 f 的問題：
>
> x* ∈ arg min_{x∈X} f(x)。  (1)
>
> 我們假設 f(x) 的評估成本高昂，且可能僅能透過帶雜訊的估計 y 觀測。在此情境下，我們希望以高效的方式最小化 f，通常需遵守一個對可評估點數設定上限的預算。
>
> **帶機率式使用者信念的黑盒最佳化。** 在本研究中，我們考慮式 (1) 最佳化問題的擴充版本：我們可取得以機率分布形式表達之關於最佳解位置的使用者信念。形式上，我們將「帶機率式使用者信念的黑盒最佳化」問題定義為：在給定使用者指定之最佳解位置先驗機率
>
> π(x) = P(f(x) = min_{x′∈X} f(x′))  (2)
>
> 的條件下求解式 (1)，其中使用者預期較可能包含最佳解的區域將具有較高的值。我們指出，在不失一般性的前提下，我們要求 π 在整個 X 上嚴格為正，亦即搜尋空間中的任何點都可能是最佳解。由於使用者信念 π(x) 可能不準確、甚至具誤導性，在給定式 (2) 下最佳化式 (1) 是一個具挑戰性的問題。

### 2.2 Bayesian Optimization | 貝葉斯最佳化

> [!quote] Original
> We outline Bayesian optimization (Mockus et al., 1978; Brochu et al., 2010; Shahriari et al., 2016b).
>
> **Model** BO aims to globally minimize f by an initial experimental design D0 = {(xi, yi)}_{i=1}^M and thereafter sequentially deciding on new points xn to form the data Dn = Dn−1 ∪ {(xn, yn)} for the n-th iteration with n ∈ {1 . . . N}. After each new observation, BO constructs a probabilistic surrogate model of f and uses that surrogate to evaluate an acquisition function α(x, Dn). The combination of surrogate model and acquisition function encodes the policy for selecting the next point xn+1. When constructing the surrogate, the most common choice is Gaussian processes (Rasmussen & Williams, 2006), which model f as p(f|Dn) = GP(m, k), with prior mean m (which is typically 0) and positive semi-definite covariance kernel k. The posterior mean mn and the variance s2n are
>
> mn(x) = kn(x)^T (Kn + σn²I)y,  s²n(x) = k(x, x) − kn(x)^T (Kn + σn²I)kn(x),  (3)
>
> where (Kn)ij = k(xi, xj), kn(x) = [k(x, x1), . . . , k(x, xn)]^T and σn² is the estimation of the observation noise variance σ². Alternative surrogate models include Random forests (Hutter et al., 2011) and Bayesian neural networks (Springenberg et al., 2016).

> [!note] 翻譯
> 我們概述貝葉斯最佳化 (Mockus et al., 1978; Brochu et al., 2010; Shahriari et al., 2016b)。
>
> **模型。** BO 旨在全域最小化 f：先建立初始實驗設計 D0 = {(xi, yi)}_{i=1}^M，其後在第 n 次迭代（n ∈ {1 . . . N}）中逐次決定新的點 xn，構成資料 Dn = Dn−1 ∪ {(xn, yn)}。每獲得一筆新觀測後，BO 建構 f 的機率式代理模型，並利用該代理模型評估擷取函數 α(x, Dn)。代理模型與擷取函數的組合編碼了選擇下一個點 xn+1 的策略。建構代理模型時，最常見的選擇是高斯過程 (Rasmussen & Williams, 2006)，其將 f 建模為 p(f|Dn) = GP(m, k)，先驗平均數為 m（通常為 0），共變異數核 k 為半正定。後驗平均數 mn 與變異數 s²n 為
>
> mn(x) = kn(x)^T (Kn + σn²I)y，  s²n(x) = k(x, x) − kn(x)^T (Kn + σn²I)kn(x)，  (3)
>
> 其中 (Kn)ij = k(xi, xj)，kn(x) = [k(x, x1), . . . , k(x, xn)]^T，σn² 為觀測雜訊變異數 σ² 的估計。替代的代理模型包括隨機森林 (Hutter et al., 2011) 與貝葉斯神經網路 (Springenberg et al., 2016)。

> [!quote] Original
> **Acquisition Functions** To obtain new candidates to evaluate, BO employs a criterion, called an acquisition function, that encapsulates an explore-exploit trade-off. By maximizing this criterion at each iteration, one or more candidate point are obtained and added to observed data. Several acquisition functions are used in BO; the most common of these is Expected Improvement (EI) (Jones et al., 1998). For a noiseless function, EI selects the next point xn+1, where fn* is the minimal objective function value observed by iteration n, as
>
> xn+1 ∈ arg max_{x∈X} E[(fn* − f(x))]+ = arg max_{x∈X} Z sn(x)Φ(Z) + sn(x)φ(Z),  (4)
>
> where Z = (fn* − mn(x))/sn(x). Thus, EI provides a myopic strategy for determining promising points; it also comes with convergence guarantees (Bull, 2011). Similar myopic acquisition functions are Upper Confidence Bound (UCB) (Srinivas et al., 2012), Probability of Improvement (PI) (Jones, 2001; Kushner, 1964) and Thompson Sampling (TS) (Thompson, 1933). A different class of acquisition functions is based on non-myopic criteria, such as Entropy Search (Hennig & Schuler, 2012), Predictive Entropy Search (Hernández-Lobato et al., 2014) and Max-value Entropy Search (Wang & Jegelka, 2017), which select points to minimize the uncertainty about the optimum, and the Knowledge Gradient (Frazier et al., 2008), which aims to minimize the posterior mean of the surrogate at the subsequent iteration. Our work applies to all acquisition functions in the first class, and we leave its extension to those in the second class for future work.

> [!note] 翻譯
> **擷取函數。** 為取得下一批要評估的候選點，BO 採用一種稱為擷取函數的準則，其封裝了探索—利用（explore-exploit）的權衡。透過在每次迭代中最大化此準則，可取得一或多個候選點並加入觀測資料。BO 中使用多種擷取函數，其中最常見的是期望改進（EI）(Jones et al., 1998)。對於無雜訊函數，EI 選擇下一個點 xn+1（其中 fn* 為截至第 n 次迭代所觀測到的最小目標函數值）：
>
> xn+1 ∈ arg max_{x∈X} E[(fn* − f(x))]+ = arg max_{x∈X} Z sn(x)Φ(Z) + sn(x)φ(Z)，  (4)
>
> 其中 Z = (fn* − mn(x))/sn(x)。因此，EI 為判定有潛力的點提供了一種短視型策略；它亦具備收斂保證 (Bull, 2011)。類似的短視型擷取函數還有信賴上界（Upper Confidence Bound, UCB）(Srinivas et al., 2012)、改進機率（Probability of Improvement, PI）(Jones, 2001; Kushner, 1964) 與湯普森採樣（Thompson Sampling, TS）(Thompson, 1933)。另一類擷取函數基於非短視型準則，例如熵搜尋（Entropy Search）(Hennig & Schuler, 2012)、預測熵搜尋（Predictive Entropy Search）(Hernández-Lobato et al., 2014) 與最大值熵搜尋（Max-value Entropy Search）(Wang & Jegelka, 2017)——它們選擇能使關於最佳解之不確定性最小化的點——以及知識梯度（Knowledge Gradient）(Frazier et al., 2008)，其目標是最小化下一次迭代時代理模型的後驗平均數。本研究適用於第一類的所有擷取函數；延伸至第二類則留待未來工作。

### 2.3 Related Work | 相關研究

> [!quote] Original
> There are two main categories of approaches that exploit prior knowledge in BO: approaches that use records of previous experiments, and approaches that incorporate assumptions on the black-box function provided either directly or indirectly by the user. As πBO exploits prior knowledge from users, we briefly discuss approaches which utilize previous experiments, and then comprehensively discuss the literature on exploiting expert knowledge.
>
> **Learning from Previous Experiments** Transfer learning for BO aims to automatically extract and use knowledge from prior executions of BO. These executions can come, for example, from learning and optimizing the hyperparameters of a machine learning algorithm on different datasets (van Rijn & Hutter, 2018; Swersky et al., 2013; Wistuba et al., 2015; Perrone et al., 2019; Feurer et al., 2015; 2018), or from optimizing the hyperparameters at different development stages (Stoll et al., 2020). For a comprehensive overview of meta learning for hyperparameter optimization, please see the survey from Vanschoren (2018). In contrast to these transfer learning approaches, πBO and the related work discussed below does not hinge on the existence of previous experiments, and can therefore be applied more generally.

> [!note] 翻譯
> 在 BO 中利用先驗知識的方法主要分為兩大類：使用先前實驗紀錄的方法，以及納入由使用者直接或間接提供之關於黑盒函數假設的方法。由於 πBO 利用的是來自使用者的先驗知識，我們先簡要討論利用先前實驗的方法，再全面討論利用專家知識的文獻。
>
> **從先前實驗中學習。** BO 的遷移學習旨在自動抽取並利用先前 BO 執行中的知識。這些執行可能來自於：在不同資料集上學習並最佳化機器學習演算法的超參數 (van Rijn & Hutter, 2018; Swersky et al., 2013; Wistuba et al., 2015; Perrone et al., 2019; Feurer et al., 2015; 2018)，或在不同開發階段最佳化超參數 (Stoll et al., 2020)。有關超參數最佳化之元學習的全面綜述，請參閱 Vanschoren (2018) 的調查。與這些遷移學習方法不同，πBO 及下文討論的相關工作不依賴於先前實驗的存在，因此適用範圍更廣。

> [!quote] Original
> **Incorporating Expert Priors over Function Structure** BO can leverage structural priors on how the objective function is expected to behave. Traditionally, this is done via the surrogate model's prior over functions, e.g., the kernel of the GP. However, there are lines of work that explore additional structural priors for BO to leverage. For instance, both SMAC (Hutter et al., 2011) and iRace (López-Ibáñez et al., 2016) support structural priors in the form of log-transformations, Li et al. (2018) propose to use knowledge about the monotonicity of the objective function as a prior for BO, and Snoek et al. (2014) model non-stationary covariance between inputs by warping said inputs. Oh et al. (2018) and Siivola et al. (2018) both propose structural priors tailored to high-dimensional problems, addressing the issue of over-exploring the boundary described by Swersky (2017). Oh et al. (2018) propose a cylindrical kernel that expands the center of the search space and shrinks the edges, while Siivola et al. (2018) propose adding derivative signs to the edges of the search space to steer BO towards the center. Lastly, Shahriari et al. (2016a) propose a BO algorithm for unbounded search spaces which uses a regularizer to penalize points based on their distance to the center of the user-defined search space. All of these approaches incorporate prior information on specific properties of the function or search space, and are thus not always applicable. Moreover, they do not generally direct the search to desired regions of the search space, offering the user little control over the selection of points to evaluate.

> [!note] 翻譯
> **納入關於函數結構的專家先驗。** BO 可利用關於目標函數預期行為的結構性先驗。傳統上，這是透過代理模型對函數的先驗來達成，例如 GP 的核函數。然而，另有多條研究路線探索了 BO 可利用的額外結構性先驗。例如，SMAC (Hutter et al., 2011) 與 iRace (López-Ibáñez et al., 2016) 均支援以對數轉換形式表達的結構性先驗；Li et al. (2018) 提出將目標函數單調性的知識作為 BO 的先驗；Snoek et al. (2014) 則透過對輸入進行翹曲（warping）來建模輸入間的非平穩共變異數。Oh et al. (2018) 與 Siivola et al. (2018) 皆提出針對高維問題量身打造的結構性先驗，以解決 Swersky (2017) 所描述的邊界過度探索問題：Oh et al. (2018) 提出一種圓柱形核，放大搜尋空間中心並收縮邊緣；Siivola et al. (2018) 則提出在搜尋空間邊緣加入導數符號，引導 BO 朝中心搜尋。最後，Shahriari et al. (2016a) 針對無界搜尋空間提出一種 BO 演算法，利用正則化項依據點與使用者定義搜尋空間中心的距離施以懲罰。所有這些方法納入的都是關於函數或搜尋空間特定性質的先驗資訊，因此並非總是適用；此外，它們通常無法將搜尋導向搜尋空間中期望的區域，使用者對評估點的選擇幾乎沒有掌控權。

> [!quote] Original
> **Incorporating Expert Priors over Function Optimum** Few previous works have proposed to inject explicit prior distributions over the location of an optimum into BO. In these cases, users explicitly define a prior that encodes their beliefs on where the optimum is more likely to be located. Bergstra et al. (2011) suggest an approach that supports prior beliefs from a fixed set of distributions. However, this approach cannot be combined with standard acquisition functions. BOPrO (Souza et al., 2021) employs a similar structure that combines the user-provided prior distribution with a data-driven model into a pseudo-posterior. From the pseudo-posterior, configurations are selected using the EI acquisition function, using the formulation in Bergstra et al. (2011). While BOPrO is able to recover from misleading priors, its design restricts it to only use EI. Moreover, it does not provide the convergence guarantees of πBO. Li et al. (2020) propose to infer a posterior conditioned on both the observed data and the user prior through repeated Thompson sampling and maximization under the prior. This method displays robustness against misleading priors but lacks in empirical performance. Additionally, it is restricted to only one specific acquisition function. Ramachandran et al. (2020) use the probability integral transform to warp the search space, stretching high-probability regions and shrinking others. While the approach is model- and acquisition function agnostic, it requires invertible priors, and does not empirically display the ability to recover from misleading priors. In Section 4, we demonstrate that πBO compares favorably for priors over the function optimum, and shows improved empirical performance. Additionally, we do a complete comparison of all approaches in Appendix C.
>
> In summary, πBO sets itself apart from the methods above by being simpler (and thus easier to implement in different frameworks), flexible with regard to different acquisition functions and different surrogate models, the availability of theoretical guarantees, and, as we demonstrate in Section 4, better empirical results.

> [!note] 翻譯
> **納入關於函數最佳解的專家先驗。** 先前僅有少數研究提出將關於最佳解位置的顯式先驗分布注入 BO。在這些工作中，使用者明確定義一個先驗，編碼其對最佳解較可能位於何處的信念。Bergstra et al. (2011) 提出一種支援來自固定分布集合之先驗信念的方法，但該方法無法與標準擷取函數結合。BOPrO (Souza et al., 2021) 採用類似結構，將使用者提供的先驗分布與資料驅動模型結合為一個偽後驗（pseudo-posterior）；再依 Bergstra et al. (2011) 的公式化，以 EI 擷取函數自偽後驗中選擇組態。BOPrO 雖能自誤導性先驗中恢復，其設計卻限制它只能使用 EI，且不具備 πBO 的收斂保證。Li et al. (2020) 提出透過在先驗下反覆進行湯普森採樣與最大化，推斷同時以觀測資料與使用者先驗為條件的後驗。此方法對誤導性先驗展現穩健性，但實證表現欠佳，且僅限於一種特定擷取函數。Ramachandran et al. (2020) 利用機率積分轉換（probability integral transform）對搜尋空間進行翹曲，拉伸高機率區域並收縮其他區域。該方法雖與模型及擷取函數無關，卻要求先驗可逆，且在實證上未展現自誤導性先驗恢復的能力。在第 4 節中，我們證明 πBO 在最佳解先驗的情境下表現更勝一籌，並展現更佳的實證效能。此外，我們在附錄 C 對所有方法進行完整比較。
>
> 總結而言，πBO 與上述方法的區別在於：更簡潔（因此更易於在不同框架中實作）、對不同擷取函數與不同代理模型皆具彈性、具備理論保證，且如第 4 節所示，擁有更佳的實證結果。

---

## 3 Methodology | 方法論

> [!quote] Original
> We now present πBO, which allows users to specify their belief about the location of the optimum through any probability distribution. A conceptually simple approach, πBO can be easily implemented in existing BO frameworks and can be combined directly with the myopic acquisition functions listed above. πBO augments an acquisition function to emphasize promising regions under the prior, ensuring such regions are to be explored frequently. As optimization progresses, the πBO strategy increasingly resembles that of vanilla BO, retaining its standard convergence rates (see Section 3.3). πBO is publicly available as part of the SMAC (https://github.com/automl/SMAC3) and HyperMapper (https://github.com/luinardi/hypermapper) HPO frameworks.

> [!note] 翻譯
> 我們現在介紹 πBO，它允許使用者透過任意機率分布來表述其關於最佳解位置的信念。作為一種概念上簡潔的方法，πBO 可輕鬆實作於既有 BO 框架中，並可直接與前述各短視型擷取函數結合。πBO 對擷取函數進行增強，以強調先驗下有潛力的區域，確保這些區域被頻繁探索。隨著最佳化的推進，πBO 的策略愈發趨近原味 BO，保有其標準收斂速率（見第 3.3 節）。πBO 已作為 SMAC（https://github.com/automl/SMAC3）與 HyperMapper（https://github.com/luinardi/hypermapper）HPO 框架的一部分公開發布。

### 3.1 Prior-weighted Acquisition Function | 先驗加權擷取函數

> [!quote] Original
> In πBO, we consider π(x) in Eq. (2) to be a weighting scheme on points in X. The heuristic provided by an acquisition function α(x, Dn), such as EI in Eq. (4), can then be combined with said weighting scheme to form a prior-weighted version of the acquisition function. The resulting strategy then becomes:
>
> xn ∈ arg max_{x∈X} α(x, Dn)π(x).  (5)
>
> This emphasizes good points under π(x) throughout the optimization. While this property is suitable for well-located priors π, it risks incurring a substantial slowdown for poorly-chosen priors; we will now show how to counter this by decaying the prior over time.

> [!note] 翻譯
> 在 πBO 中，我們將式 (2) 中的 π(x) 視為對 X 中各點的加權方案。擷取函數 α(x, Dn) 所提供的啟發式準則（如式 (4) 的 EI）便可與此加權方案結合，構成擷取函數的先驗加權版本。所得策略為：
>
> xn ∈ arg max_{x∈X} α(x, Dn)π(x)。  (5)
>
> 此式在整個最佳化過程中持續強調 π(x) 下的優良點。這一性質雖適合位置準確的先驗 π，但對於選擇不當的先驗，卻有導致大幅減速的風險；接下來我們展示如何透過讓先驗隨時間衰減來化解此問題。

### 3.2 Decaying Prior-weighted Acquisition Function | 衰減式先驗加權擷取函數

> [!quote] Original
> As the optimization progresses, we should increasingly trust the surrogate model over the prior; the model improves with data while the user prior remains fixed. This cannot be achieved with the formulation in Eq. (5), as poorly-chosen priors would permanently slow down the optimization. Rather, to accomplish this desired behaviour, the influence of the prior needs to decay over time. Building on the approaches of Lee et al. (2020) and Souza et al. (2021), we accomplish this by raising the prior to a power γn ∈ R+, which decays towards zero with growing n. Thus, the resulting prior πn(x) = π(x)^{γn} reflects a belief on the location of an optimum that gets weaker with time, converging towards a uniform distribution. We set γn = β/n, where β ∈ R+ is a hyperparameter set by the user, reflecting their confidence in π(x). We provide a sensitivity study on β in Appendix A.
>
> For a given acquisition function α(x, Dn) and user-specified prior π(x), we define the decaying prior-weighted acquisition function at iteration n as
>
> απ,n(x, Dn) ≜ α(x, Dn)πn(x) ≜ α(x, Dn)π(x)^{β/n}  (6)
>
> and its accompanying strategy as the maximizer of απ,n. With the acquisition function in Eq. (6), the prior will assume large importance initially, promoting the selection of points close to the prior mode. With time, the exponent on the prior will tend to zero, making the prior tend to uniform. Thus, with increasing n, the point selection of απ,n becomes increasingly similar to that of α. Algorithm 1 displays the simplicity of the new strategy, highlighting the required one-line change (Line 6) in the main BO loop. In Line 3, the mode of the prior is used as a first initial sample if available. Otherwise, only sampling is used for initialization.
>
> **Algorithm 1 πBO Algorithm**
> 1: Input: Input space X, prior distribution over optimum π(x), prior confidence parameter β, size M of the initial design, max number of optimization iterations N.
> 2: Output: Optimized design x*.
> 3: {xi}_{i=1}^M ∼ π(x), {yi ← f(xi) + εi}_{i=1}^M, εi ∼ N(0, σ²)
> 4: D0 ← {(xi, yi)}_{i=1}^M
> 5: for {n = 1, 2, . . . , N} do
> 6:   xnew ← arg max_{x∈X} α(x, Dn−1)π(x)^{β/n}
> 7:   ynew ← f(xnew) + εi
> 8:   Dn ← Dn−1 ∪ {(xnew, ynew)}
> 9: end for
> 10: return x* ← arg min_{(xi,yi)∈DN} yi

> [!note] 翻譯
> 隨著最佳化的推進，我們應當愈來愈信任代理模型而非先驗；模型隨資料而改進，使用者先驗則保持固定。式 (5) 的形式無法達成此點，因為選擇不當的先驗會永久性地拖慢最佳化。要達成所期望的行為，先驗的影響力必須隨時間衰減。基於 Lee et al. (2020) 與 Souza et al. (2021) 的方法，我們將先驗提升至冪次 γn ∈ R+，其隨 n 增大而朝零衰減。如此，所得先驗 πn(x) = π(x)^{γn} 反映的是一個隨時間減弱、收斂至均勻分布的最佳解位置信念。我們設 γn = β/n，其中 β ∈ R+ 為使用者設定的超參數，反映其對 π(x) 的信心。附錄 A 提供對 β 的敏感度研究。
>
> 給定擷取函數 α(x, Dn) 與使用者指定的先驗 π(x)，我們定義第 n 次迭代的衰減式先驗加權擷取函數為
>
> απ,n(x, Dn) ≜ α(x, Dn)πn(x) ≜ α(x, Dn)π(x)^{β/n}  (6)
>
> 其對應策略即為 απ,n 的最大化者。在式 (6) 的擷取函數下，先驗在初期占據重要地位，促使選擇靠近先驗眾數（mode）的點；隨時間推移，先驗的指數趨近於零，使先驗趨於均勻。因此，隨 n 增加，απ,n 的點選擇愈發近似 α 的點選擇。演算法 1 展示了新策略的簡潔性，凸顯 BO 主迴圈中僅需修改的一行程式碼（第 6 行）。第 3 行中，若先驗眾數可得，則以之作為第一個初始樣本；否則僅以採樣進行初始化。
>
> **演算法 1：πBO 演算法**
> 1: 輸入：輸入空間 X、最佳解先驗分布 π(x)、先驗信心參數 β、初始設計規模 M、最大最佳化迭代次數 N。
> 2: 輸出：最佳化後的設計 x*。
> 3: {xi}_{i=1}^M ∼ π(x)，{yi ← f(xi) + εi}_{i=1}^M，εi ∼ N(0, σ²)
> 4: D0 ← {(xi, yi)}_{i=1}^M
> 5: for {n = 1, 2, . . . , N} do
> 6:   xnew ← arg max_{x∈X} α(x, Dn−1)π(x)^{β/n}
> 7:   ynew ← f(xnew) + εi
> 8:   Dn ← Dn−1 ∪ {(xnew, ynew)}
> 9: end for
> 10: return x* ← arg min_{(xi,yi)∈DN} yi

> [!quote] Original
> To illustrate the behaviour of πBO, we consider a toy problem with Gaussian priors on three different locations of the 1D space (center, left and right) as displayed in Figure 1. We define a 1D-Log-Branin toy problem by setting the second dimension of the 2D Branin function to the global optimum x2 = 2.275 and optimizing for the first dimension. Initially (iteration 4 in the top row), πBO amplifies the acquisition function α in high-probability regions, putting a lot of trust in the prior. As the prior decays (iteration 6 and 8 in the middle and bottom rows, respectively), the influence of the prior on the point selection decreases. By later iterations, πBO has searched substantially around the prior mode, and moves gradually towards other parts of the search space. This is of particular importance for the scenarios in the right column, where πBO recovers from a misleading prior. In Appendix B, we show that πBO is applicable to different surrogate models and acquisition functions.
>
> [Figure 1: Rescaled values of prior-weighted EI (purple), EI (blue) and πn (red) on a 1D-Branin in logscale (grey) with global optimum in the center of the search space. Runs with two different prior locations ("Well-located" slightly right of optimum, "Off-center" significantly left of optimum) are shown in the two columns. Each row represents an iteration (iteration 4, 6 and 8), for an optimization run with β = 2.]

> [!note] 翻譯
> 為說明 πBO 的行為，我們考慮一個玩具問題：在一維空間的三個不同位置（中央、左側與右側）放置高斯先驗，如圖 1 所示。我們透過將二維 Branin 函數的第二維固定於全域最佳解 x2 = 2.275、僅對第一維最佳化，定義一個 1D-Log-Branin 玩具問題。初期（上排的第 4 次迭代），πBO 在高機率區域放大擷取函數 α，對先驗給予高度信任。隨著先驗衰減（中排與下排分別為第 6 與第 8 次迭代），先驗對點選擇的影響降低。到了較後期的迭代，πBO 已在先驗眾數周圍進行了充分搜尋，並逐漸移向搜尋空間的其他部分。這對右欄的情境尤為重要——πBO 在該情境中自誤導性先驗中恢復。附錄 B 顯示 πBO 適用於不同的代理模型與擷取函數。
>
> ［圖 1：對數尺度 1D-Branin（灰）上先驗加權 EI（紫）、EI（藍）與 πn（紅）的重縮放值，全域最佳解位於搜尋空間中央。兩欄分別展示兩種先驗位置（「位置良好」：略偏最佳解右側；「偏離中心」：顯著偏最佳解左側）的執行。各列代表一個迭代（第 4、6、8 次），β = 2。］

### 3.3 Theoretical Analysis | 理論分析

> [!quote] Original
> We now study the πBO method from a theoretical standpoint when paired with the EI acquisition function. For the full proof, we refer the reader to Appendix E. To provide convergence rates, we rely on the set of assumptions introduced by Bull (2011). These assumptions are satisfied for popular kernels like the Matérn (1960) class and the Gaussian kernel, which is obtained in the limit ν → ∞, where the rate ν controls the smoothness of functions from the GP prior. Our theoretical results apply when both length scales ℓ and the global scale of variation σ are fixed; these results can then be extended to the case where the kernel hyperparameters are learned using Maximum Likelihood Estimation (MLE) following the same procedure as in Bull (2011) (Theorem 5). We define the loss over the ball BR for a function f of norm ||f||_{Hℓ(X)} ≤ R in the reproducing kernel Hilbert space (RKHS) Hℓ(X) given a symmetric positive-definite kernel Kℓ as
>
> Ln(u, Dn, Hℓ(X), R) ≜ sup_{||f||_{Hℓ(X)}≤R} E_u^f [f(x*_n) − min f],  (7)
>
> where n is the optimization iteration and u a strategy. We focus on the strategy that maximizes EIπ, the prior-weighted EI, and show that the loss in Equation (7) can, at any iteration n, be bounded by the vanilla EI loss function. We refer to EIπ,n and EIn when we want to emphasize the iteration n for the acquisition functions EIπ and EI, respectively.
>
> **Theorem 1.** Given Dn, Kℓ, π, β, σ, ℓ, R and the compact set X ⊂ R^d as defined above, the loss Ln incurred at iteration n by EIπ,n can be bounded from above as
>
> Ln(EIπ,n, Dn, Hℓ(X), R) ≤ Cπ,n Ln(EIn, Dn, Hℓ(X), R),  Cπ,n = (max_{x∈X} π(x) / min_{x∈X} π(x))^{β/n}.  (8)
>
> Using Theorem 1, we obtain the convergence rate of EIπ. This trivially follows when considering the fraction of the losses in the limit and inserting the original convergence rate on EI as in Bull (2011):
>
> **Corollary 1.** The loss of a decaying prior-weighted Expected Improvement strategy, EIπ, is asymptotically equal to the loss of an Expected Improvement strategy, EI:
>
> Ln(EIπ,n, Dn, Hℓ(X), R) ∼ Ln(EIn, Dn, Hℓ(X), R),  (9)
>
> so we obtain a convergence rate for EIπ of Ln(EIπ,n, Dn, Hℓ(X), R) = O(n^{−(ν∧1)/d}(log n)^γ).
>
> Thus, we determine that the weighting introduced by EIπ does not negatively impact the worst-case convergence rate. The short-term performance is controlled by the user in their choice of π(x) and β. This result is coherent with intuition, as a weaker prior or quicker decay will yield a short-term performance closer to that of EI. In contrast, a stronger prior or slower decay does not guarantee the same short-term performance, but can produce better empirical results, as shown in Section 4.

> [!note] 翻譯
> 我們現在從理論角度研究搭配 EI 擷取函數的 πBO 方法。完整證明請參閱附錄 E。為給出收斂速率，我們依賴 Bull (2011) 引入的假設集合。這些假設對流行的核函數皆成立，例如 Matérn (1960) 核族，以及在極限 ν → ∞ 下所得的高斯核，其中參數 ν 控制 GP 先驗所生成函數的平滑度。我們的理論結果適用於長度尺度 ℓ 與整體變異尺度 σ 皆固定的情形；依循 Bull (2011)（定理 5）的相同程序，這些結果可延伸至以最大概似估計（Maximum Likelihood Estimation, MLE）學習核超參數的情形。給定對稱正定核 Kℓ，對於再生核希爾伯特空間（reproducing kernel Hilbert space, RKHS）Hℓ(X) 中範數 ||f||_{Hℓ(X)} ≤ R 的函數 f，我們定義球 BR 上的損失為
>
> Ln(u, Dn, Hℓ(X), R) ≜ sup_{||f||_{Hℓ(X)}≤R} E_u^f [f(x*_n) − min f]，  (7)
>
> 其中 n 為最佳化迭代次數，u 為策略。我們聚焦於最大化 EIπ（先驗加權 EI）的策略，並證明式 (7) 的損失在任何迭代 n 皆可被原味 EI 的損失函數所上界。當需強調迭代次數 n 時，我們分別以 EIπ,n 與 EIn 指稱擷取函數 EIπ 與 EI。
>
> **定理 1。** 給定如上定義的 Dn、Kℓ、π、β、σ、ℓ、R 與緊緻集 X ⊂ R^d，EIπ,n 在第 n 次迭代所產生的損失 Ln 可被上界為
>
> Ln(EIπ,n, Dn, Hℓ(X), R) ≤ Cπ,n Ln(EIn, Dn, Hℓ(X), R)，  Cπ,n = (max_{x∈X} π(x) / min_{x∈X} π(x))^{β/n}。  (8)
>
> 利用定理 1，我們可得 EIπ 的收斂速率。考慮兩損失之比值在極限下的行為，並代入 Bull (2011) 中 EI 的原始收斂速率，即可平凡地推得：
>
> **系理 1。** 衰減式先驗加權期望改進策略 EIπ 的損失，漸近上等於期望改進策略 EI 的損失：
>
> Ln(EIπ,n, Dn, Hℓ(X), R) ∼ Ln(EIn, Dn, Hℓ(X), R)，  (9)
>
> 因此 EIπ 的收斂速率為 Ln(EIπ,n, Dn, Hℓ(X), R) = O(n^{−(ν∧1)/d}(log n)^γ)。
>
> 由此可知，EIπ 引入的加權不會對最壞情況的收斂速率造成負面影響。短期表現則由使用者透過 π(x) 與 β 的選擇來控制。此結果與直覺一致：較弱的先驗或較快的衰減會使短期表現更接近 EI；反之，較強的先驗或較慢的衰減雖不保證相同的短期表現，卻可能產生更佳的實證結果，如第 4 節所示。

---

## 4 Results | 實驗結果

> [!quote] Original
> We empirically demonstrate the efficiency of πBO in three different settings. As πBO is a general method to augment acquisition functions, it can be implemented in different parent BO packages, and the implementation in any given package inherits the pros and cons of that package. To minimize confounding factors concerning this choice of parent package, we keep comparisons within the methods in one package where possible and provide results in the other packages in Appendix C. In Sec. 4.2, using Spearmint as a parent package, we evaluate πBO against three intuitive baselines to assess its performance and robustness on priors with different qualities, ranging from very accurate to purposefully detrimental. To this end, we use toy functions and cheap surrogates, where priors of known quality can be obtained. Next, in Sec. 4.3, we compare πBO against two competitive approaches (BOPrO and BOWS) that integrate priors over the optimum similarly to πBO, using HyperMapper (Nardi et al., 2019) as a parent framework, in which the most competitive baseline BOPrO is implemented. For these experiments we adopt a Multilayer Perceptron (MLP) benchmark on various datasets, using the interface provided by HPOBench (Eggensperger et al., 2021), with priors constructed around the defaults provided by the library. Lastly, in Sec. 4.4, we apply πBO and other approaches to two deep learning tasks, also using priors derived from publicly available defaults. Further, we demonstrate the flexibility of πBO in Appendix B, where we evaluate πBO in SMAC (Hutter et al., 2011; Lindauer et al., 2021) with random forests, as another framework with another surrogate model, and adapt it to use the UCB, TS and PI acquisition functions instead of EI.

> [!note] 翻譯
> 我們在三種不同情境下實證展示 πBO 的效率。由於 πBO 是增強擷取函數的通用方法，可實作於不同的母 BO 套件之中，而在任一套件中的實作會繼承該套件的優缺點。為將母套件選擇帶來的混淆因素降至最低，我們盡可能在同一套件內的方法間進行比較，並在附錄 C 提供其他套件的結果。第 4.2 節以 Spearmint 為母套件，將 πBO 與三個直觀的基線比較，以評估其在不同品質先驗（從非常準確到刻意有害）下的效能與穩健性；為此我們使用玩具函數與低成本代理基準，其先驗品質可事先得知。接著，第 4.3 節將 πBO 與兩個具競爭力、同樣整合最佳解先驗的方法（BOPrO 與 BOWS）比較，母框架採用 HyperMapper (Nardi et al., 2019)——最具競爭力的基線 BOPrO 即實作於其中。這些實驗採用多層感知器（Multilayer Perceptron, MLP）基準，於多個資料集上進行，介面由 HPOBench (Eggensperger et al., 2021) 提供，先驗則圍繞函式庫提供的預設值建構。最後，第 4.4 節將 πBO 與其他方法應用於兩項深度學習任務，先驗同樣源自公開可得的預設值。此外，我們在附錄 B 展示 πBO 的彈性：在 SMAC (Hutter et al., 2011; Lindauer et al., 2021) 中以隨機森林作為另一種框架與另一種代理模型評估 πBO，並將其改用 UCB、TS 與 PI 擷取函數取代 EI。

### 4.1 Experimental Setup | 實驗設置

> [!quote] Original
> **Priors** For our surrogate and toy function tasks, we follow the prior construction methodology in BOPrO (Souza et al., 2021) and create three main types of prior qualities, all Gaussian: strong, weak and wrong. The strong and weak priors are located to have a high and moderate density on the optimum, respectively. The wrong prior is a narrow distribution located in the worst region of the search space. For the OpenML MLP tuning benchmark, we utilize the defaults and search spaces provided in HPOBench (Eggensperger et al., 2021), and construct Gaussian priors for each hyperparameter with their mean on the default value, and a standard deviation of 25% of the hyperparameter's domain. For the DL case studies, we utilize defaults from each task's repository and, for numerical hyperparameters, once again set the standard deviation to 25% of the hyperparameter's domain. For categorical hyperparameters, we place a higher probability on the default. As such, the quality of the prior is ultimately unknown, but serves as a proxy for what a practitioner may choose and has shown to be a reasonable choice (Anastacio & Hoos, 2020). For all experiments, we run πBO with β = N/10, where N is the total number of iterations, in order to make the prior influence approximately equal in all experiments, regardless of the number of allowed BO iterations. We investigate the sensitivity to β in Appendix A, and the sensitivity to prior quality in Appendix G.
>
> **Baselines** We empirically evaluate πBO against the most competitive approaches for priors over the optimum described in Section 2.3: BOPrO (Souza et al., 2021) and BO in Warped Space (BOWS) (Ramachandran et al., 2020). To contextualize the performance of πBO, we provide additional, simpler baselines: random sampling, sampling from the prior and BO with prior-based initial design. The latter is initialized with the mode of the prior in addition to its regular initial design. In our main results, we choose Spearmint (with EI) (Snoek et al., 2012) for this mode-initialized baseline, simply referring to it as Spearmint. See Appendix F for complete details on the experiments.

> [!note] 翻譯
> **先驗。** 對於代理基準與玩具函數任務，我們依循 BOPrO (Souza et al., 2021) 的先驗建構方法，建立三種主要品質的先驗，皆為高斯分布：強（strong）、弱（weak）與錯誤（wrong）。強與弱先驗的位置分別使最佳解處具有高與中等的機率密度；錯誤先驗則是位於搜尋空間最差區域的狹窄分布。對於 OpenML MLP 調校基準，我們採用 HPOBench (Eggensperger et al., 2021) 提供的預設值與搜尋空間，為每個超參數建構高斯先驗——平均數置於預設值，標準差為該超參數定義域的 25%。對於 DL 案例研究，我們採用各任務儲存庫的預設值；數值型超參數同樣將標準差設為定義域的 25%，類別型超參數則在預設值上放置較高機率。因此，先驗的品質實際上未知，但可作為從業者可能做出之選擇的代理，且已被證明是合理的選擇 (Anastacio & Hoos, 2020)。所有實驗中，我們以 β = N/10 執行 πBO（N 為總迭代數），使先驗影響力在所有實驗中大致相同，而與允許的 BO 迭代數無關。附錄 A 探討對 β 的敏感度，附錄 G 探討對先驗品質的敏感度。
>
> **基線。** 我們將 πBO 與第 2.3 節所述最具競爭力的最佳解先驗方法進行實證比較：BOPrO (Souza et al., 2021) 與翹曲空間 BO（BO in Warped Space, BOWS）(Ramachandran et al., 2020)。為使 πBO 的表現更具參照性，我們另提供更簡單的基線：隨機採樣、自先驗採樣，以及採用先驗式初始設計的 BO——後者除常規初始設計外，並以先驗眾數作為初始化的一部分。在主要結果中，我們選擇 Spearmint（搭配 EI）(Snoek et al., 2012) 作為此眾數初始化基線，簡稱為 Spearmint。實驗完整細節見附錄 F。

### 4.2 Robustness of πBO | πBO 的穩健性

> [!quote] Original
> First, we study the robustness of πBO. To this end, we show that πBO benefits from informative priors and can recover from wrong priors, being consistent with our theoretical results in Section 3.3. To this end, we consider a well-known black-box optimization function, Branin (2D), as well as two surrogate HPO tasks from the Profet suite (Klein et al., 2019): FC-Net (6D) and XGBoost (8D). For these tasks, we exemplarily show results for πBO implemented in the Spearmint framework. As Figure 2 shows, πBO is able to quickly improve over sampling from the prior. Moreover, it improves substantially over Spearmint (with mode initialization) for all informative priors, staying up to an order of magnitude ahead throughout the optimization for both strong and weak priors. For wrong priors, πBO displays desired robustness by recovering to approximately equal regret as Spearmint. In contrast, Spearmint frequently fails to substantially improve from its initial design on the strong and weak prior, which demonstrates the importance of considering the prior throughout the optimization procedure. This effect is even more pronounced on the higher-dimensional tasks FCNet and XGBoost, where BO typically spends many iterations at the boundary (Swersky, 2017). Here, πBO rapidly improves multiple orders of magnitude over the initial design, displaying its ability to efficiently exploit the information provided by the prior.

> [!note] 翻譯
> 首先，我們研究 πBO 的穩健性。為此，我們證明 πBO 能受益於具資訊量的先驗、並能自錯誤先驗中恢復，這與第 3.3 節的理論結果一致。我們考慮著名的黑盒最佳化函數 Branin（2 維），以及 Profet 套件 (Klein et al., 2019) 中的兩項代理 HPO 任務：FC-Net（6 維）與 XGBoost（8 維）。對於這些任務，我們以實作於 Spearmint 框架中的 πBO 為例展示結果。如圖 2 所示，πBO 能迅速超越「自先驗採樣」的表現。此外，對所有具資訊量的先驗，πBO 均大幅優於（眾數初始化的）Spearmint——無論強或弱先驗，在整個最佳化過程中領先幅度最高可達一個數量級。對於錯誤先驗，πBO 展現了所期望的穩健性，恢復至與 Spearmint 大致相同的遺憾值水準。相對地，Spearmint 在強與弱先驗下往往無法自其初始設計顯著改進，這說明了在整個最佳化過程中持續考量先驗的重要性。此效應在較高維的 FCNet 與 XGBoost 任務上更為顯著——BO 在這類任務中通常會在邊界上耗費許多迭代 (Swersky, 2017)。在此，πBO 相對初始設計迅速改進了多個數量級，展現其高效利用先驗所提供資訊的能力。
>
> ［圖 2：πBO、Spearmint 與兩種採樣方法在 Branin、FCNet 與 XGBoost 上於各種先驗強度下的比較。顯示 100 次迭代之對數簡單遺憾值（log simple regret）的平均與標準誤，20 次執行平均。垂直線代表初始設計階段的結束。］

### 4.3 Comparison of πBO against Other Prior-Guided Approaches | πBO 與其他先驗引導方法的比較

> [!quote] Original
> Next, we study the performance of πBO against other state-of-the-art prior-guided approaches. To this end, we consider optimizing 5 hyperparameters of an MLP for classification (Eggensperger et al., 2021) on 6 different OpenML datasets (Vanschoren et al., 2014) and compare against BOPrO (Souza et al., 2021) and BOWS (Ramachandran et al., 2020). For minimizing confounding factors, we implement πBO and BOWS in HyperMapper (Nardi et al., 2019), the same framework that BOPrO runs on. Moreover, we let all approaches share πBO's initialization procedure. We consider a budget of 50 iterations as it is common with ML practitioners (Bouthillier & Varoquaux, 2020). In Figure 3, we see that πBO offers the best performance on four out of six tasks, and displays the most consistent performance across tasks. In contrast to them BOWS and BOPrO, πBO also comes with theoretical guarantees and is flexible in the choice of framework and acquisition function.

> [!note] 翻譯
> 接著，我們研究 πBO 相對其他最先進先驗引導方法的表現。為此，我們考慮在 6 個不同的 OpenML 資料集 (Vanschoren et al., 2014) 上最佳化分類用 MLP 的 5 個超參數 (Eggensperger et al., 2021)，並與 BOPrO (Souza et al., 2021) 及 BOWS (Ramachandran et al., 2020) 比較。為將混淆因素降至最低，我們將 πBO 與 BOWS 實作於 HyperMapper (Nardi et al., 2019)——即 BOPrO 所運行的同一框架——並讓所有方法共享 πBO 的初始化程序。我們採用 50 次迭代的預算，因為這是 ML 從業者常見的設定 (Bouthillier & Varoquaux, 2020)。由圖 3 可見，πBO 在六項任務中的四項上提供最佳表現，且在各任務間展現最一致的表現。與 BOWS 及 BOPrO 相比，πBO 更兼具理論保證，且在框架與擷取函數的選擇上具備彈性。
>
> ［圖 3：πBO、BOPrO、BOWS 與先驗採樣在各 OpenML 資料集上進行 5 維 MLP 調校的比較，先驗以預設值為中心。顯示 20 次執行之準確率的平均與標準誤。垂直線代表初始設計階段的結束。］

### 4.4 Case Studies on Deep Learning Pipelines | 深度學習管線案例研究

> [!quote] Original
> Last, we study the impact of πBO on deep learning applications, which are often fairly expensive, making efficiency even more important than in HPO for traditional machine learning. To this end, we consider two deep learning case studies: segmentation of neuronal processes in electron microscopy images with a U-Net (6D) (Ronneberger et al., 2015), with code provided from the NVIDIA deep learning examples repository (Przemek et al.), and image classification on ImageNette-128 (6D) (Howard, 2019), a light-weight adaptation of ImageNet (Deng et al., 2009), with code from the repository of the popular FastAI library (Howard et al., 2018). We mimic the setup from Section 4.3 by using the HyperMapper framework and identical initialization procedures across approaches. Gaussian priors are set on publicly available default values, which are results of previous tuning efforts of the original authors. We again optimize for a practical budget of 50 iterations (Bouthillier & Varoquaux, 2020). As test splits for both tasks were not available to us, we report validation scores. As shown in Figure 4, πBO achieves a 2.5× time-to-accuracy speedup over Vanilla BO. For ImageNette, the performance of πBO at iteration 4 already surpasses the performance of Vanilla BO at Iteration 50, demonstrating a 12.5× time-to-accuracy speedup. Ultimately, πBO's final performance establishes a new state-of-the-art validation performance on ImageNette with the provided pipeline, with a final accuracy of 94.14% (vs. the previous state of the art with 93.55%).

> [!note] 翻譯
> 最後，我們研究 πBO 對深度學習應用的影響——這類應用往往成本相當高昂，使效率比傳統機器學習的 HPO 更加重要。為此，我們考慮兩項深度學習案例研究：其一，以 U-Net（6 維）(Ronneberger et al., 2015) 對電子顯微鏡影像中的神經元突起進行分割，程式碼取自 NVIDIA 深度學習範例儲存庫 (Przemek et al.)；其二，於 ImageNette-128（6 維）(Howard, 2019)——ImageNet (Deng et al., 2009) 的輕量化改編版——進行影像分類，程式碼取自流行的 FastAI 函式庫儲存庫 (Howard et al., 2018)。我們仿照第 4.3 節的設置，使用 HyperMapper 框架，且各方法採用完全相同的初始化程序。高斯先驗設於公開可得的預設值上——這些預設值是原作者先前調參努力的成果。我們同樣以 50 次迭代的實務預算進行最佳化 (Bouthillier & Varoquaux, 2020)。由於我們無法取得兩項任務的測試切分，故回報驗證分數。如圖 4 所示，πBO 相較原味 BO 達成 2.5× 的達標時間加速。就 ImageNette 而言，πBO 在第 4 次迭代的表現已超越原味 BO 在第 50 次迭代的表現，展現 12.5× 的達標時間加速。最終，πBO 的最終表現以所提供的管線在 ImageNette 上創下新的最先進驗證表現，最終準確率達 94.14%（先前最先進為 93.55%）。
>
> ［圖 4：U-Net Medical 與 ImageNette-128 上各方法的比較，先驗以預設值為中心。U-Net Medical 顯示 20 次執行、ImageNette-128 顯示 10 次執行之準確率的平均與標準誤。垂直線代表初始設計階段的結束。］

---

## 5 Conclusion and Future Work | 結論與未來工作

> [!quote] Original
> We presented πBO, a conceptually very simple Bayesian optimization approach for leveraging user beliefs about the location of an optimum, which relies on a generalization of myopic acquisition functions. πBO modifies the selection of design points through a decaying weighting scheme, promoting high-probability regions under the prior. Contrary to previous approaches, πBO imposes only minor restrictions on the type of priors, surrogates or frameworks that can be used. πBO provably converges at regular rates, displays state-of-the art performance across tasks, and effectively recovers from poorly specified priors. Moreover, we have demonstrated that πBO can yield substantial performance gains for practical low-budget settings, improving on the state-of-the-art for a real-world CNN tuning tasks even with trivial choices for the prior. For practitioners who have historically relied on manual or grid search for HPO, we hope that πBO will serve as an intuitive and effective tool for bridging the gap between traditional tuning methods and BO.
>
> πBO sets the stage for several follow-up studies. Amongst others, we will examine the extension of πBO to non-myopic acquisition functions, such as entropy-based methods. Non-myopic acquisition functions do not fit well in the current πBO framework, as they do not necessarily benefit from evaluating inputs expected to perform well. We will also combine πBO with multi-fidelity optimization methods to yield even higher speedups, and with multi-objective optimization to jointly optimize performance and secondary objective functions, such as interpretability or fairness of models.

> [!note] 翻譯
> 我們提出了 πBO——一種概念上極為簡潔、用於利用使用者對最佳解位置信念的貝葉斯最佳化方法，其立基於短視型擷取函數的廣義化。πBO 透過衰減式加權方案改變設計點的選擇，促進先驗下高機率區域的探索。與先前方法相反，πBO 對可使用的先驗類型、代理模型或框架僅施加極少限制。πBO 可證明地以標準速率收斂，在各項任務上展現最先進的表現，並能有效自指定不當的先驗中恢復。此外，我們已證明 πBO 在實務的低預算情境下可帶來可觀的效能增益——即使先驗的選擇十分平凡，仍在真實世界的 CNN 調校任務上超越了最先進水準。對於長期依賴手動或網格搜尋進行 HPO 的實務工作者，我們希望 πBO 能成為一項直觀且有效的工具，弭平傳統調參方法與 BO 之間的鴻溝。
>
> πBO 為多項後續研究奠定了基礎。其中，我們將檢視 πBO 向非短視型擷取函數（如基於熵的方法）的延伸——非短視型擷取函數與當前的 πBO 框架並不十分契合，因為它們未必受益於評估「預期表現良好」的輸入。我們也將把 πBO 與多保真度（multi-fidelity）最佳化方法結合以獲得更高的加速，並與多目標最佳化結合，以聯合最佳化效能與次要目標函數，例如模型的可解釋性或公平性。

---

## 6 Ethics Statement | 倫理聲明

> [!quote] Original
> Our work proposes an acquisition function generalization which incorporates prior beliefs about the location of the optimum into optimization. The approach is foundational and thus will not bring direct societal or ethical consequences. However, πBO will likely be used in the development of applications for a wide range of areas and thus indirectly contribute to their impacts on society. In particular, we envision that πBO will impact a multitude of fields by allowing ML experts to inject their knowledge about the location of the optimum into Bayesian Optimization.
>
> We also note that we intend for πBO to be a tool that allows users to assist Bayesian Optimization by providing reasonable prior knowledge and beliefs. This process induces user bias into the optimization, as πBO will inevitably start by optimizing around this prior. As some users may only be interested in optimizing in the direct neighborhood of their prior, πBO could allow them to do so if provided with a high β value in relation to the number of iterations. Thus, if improperly specified, πBO could serve to reinforce user's beliefs by providing improved solutions only for the user's region of interest. However, if used properly, πBO will reduce the computational resources required to find strong hyperparameter settings, contributing to the sustainability of machine learning.

> [!note] 翻譯
> 本研究提出一種擷取函數的廣義化方法，將關於最佳解位置的先驗信念納入最佳化。此方法屬基礎性研究，因此不會帶來直接的社會或倫理後果。然而，πBO 很可能被用於廣泛領域之應用的開發，從而間接影響這些應用對社會的衝擊。特別地，我們預見 πBO 將透過讓 ML 專家把其關於最佳解位置的知識注入貝葉斯最佳化，對眾多領域產生影響。
>
> 我們亦指出，我們期望 πBO 是一項讓使用者藉由提供合理的先驗知識與信念來輔助貝葉斯最佳化的工具。此過程會將使用者偏差引入最佳化，因為 πBO 必然會從此先驗附近開始最佳化。由於某些使用者可能只對其先驗的直接鄰域內的最佳化感興趣，若相對於迭代數提供較高的 β 值，πBO 便能讓其如願。因此，若指定不當，πBO 可能僅為使用者感興趣的區域提供改進解，從而強化使用者的既有信念。然而，若使用得當，πBO 將降低尋找優良超參數設定所需的計算資源，為機器學習的永續性做出貢獻。

---

## 7 Reproducibility | 可重現性

> [!quote] Original
> In order to make the experiments run in πBO as reproducible as possible, we have included links to repositories of our implementations in both Spearmint and HyperMapper, with instructions on how to run our experiments. Moreover, we have included in said repositories all of the exact priors that we have used for our runs, which run out of the box. The priors we used were, in our opinion, well motivated as to avoid subjectivity, which we hope serves as a good frame of reference for similar works in the future. Specifically, Appendix 4.4 describes how we ran our DL experiments, Appendix F.1 goes into the implementation in further detail, and Appendix D displays the exact priors for all our experiments and prior strengths. Our Spearmint implementation of both πBO and BOWS is available at https://github.com/piboauthors/PiBO-Spearmint, and our HyperMapper implementation is available at https://github.com/piboauthors/PiBO-Hypermapper. For our results on the convergence of πBO, we have provided a complete proof in Appendix E.

> [!note] 翻譯
> 為使 πBO 的實驗盡可能可重現，我們附上了 Spearmint 與 HyperMapper 兩種實作的儲存庫連結，並附有執行實驗的說明。此外，這些儲存庫中包含了我們執行時所使用的全部確切先驗，可直接運行。我們所使用的先驗在我們看來動機充分、足以避免主觀性，希望能為未來類似工作提供良好的參照框架。具體而言，附錄 4.4 描述我們如何執行 DL 實驗，附錄 F.1 進一步詳述實作細節，附錄 D 展示所有實驗與先驗強度的確切先驗。πBO 與 BOWS 的 Spearmint 實作公開於 https://github.com/piboauthors/PiBO-Spearmint，HyperMapper 實作公開於 https://github.com/piboauthors/PiBO-Hypermapper。關於 πBO 收斂性的結果，完整證明見附錄 E。

---

## References | 參考文獻

> [!note] 翻譯
> References omitted / 參考文獻略。（第 8 節致謝亦略。）

---

## Appendices | 附錄（附錄僅節譯）

### A Beta Ablation Study | β 消融研究

> [!quote] Original
> We consider the effect of the β hyperparameter of πBO introduced in Section 3.2, controlling the speed of the prior decay. In general, a higher value of β yields better performance for good priors, but makes πBO slower to recover from bad priors. Following the prior decay parameter baseline by Souza et al. (2021), we show that the choice of β = 10 consistently gives one of the best performances for strong priors, while retaining good overall robustness. Nearly all choices of β give a final performance better than that of Spearmint for good priors. Overall, πBO is competitive for a wide range of β, but suffers slightly worse final performance on good priors for low values of β.

> [!note] 翻譯
> 我們考察第 3.2 節引入之 πBO 超參數 β 的影響，其控制先驗衰減的速度。整體而言，較高的 β 值對良好先驗帶來較佳表現，但使 πBO 從不良先驗中恢復得較慢。依循 Souza et al. (2021) 的先驗衰減參數基線，我們顯示 β = 10 的選擇在強先驗下持續給出最佳表現之一，同時保有良好的整體穩健性。對良好先驗而言，幾乎所有 β 的選擇之最終表現皆優於 Spearmint。總體來看，πBO 在廣泛的 β 取值範圍內皆具競爭力，僅在 β 值偏低時於良好先驗上的最終表現略遜。（附錄僅節譯。）

### B πBO Versatility | πBO 的多樣適用性

> [!quote] Original
> We show the versatility of πBO by implementing it in numerous variants of SMAC, a well-established HPO framework which supports both GP and RF surrogates, and a majority of the myopic acquisition functions. We showcase the performance of πBO-EI, πBO-PI, πBO-UCB and πBO-TS with a GP surrogate, as well as πBO-EI with an RF surrogate. **General formulation:** as UCB and TS typically output values in the same order of magnitude and sign as the objective function, we add a simple affine transformation to the observations—subtracting the incumbent y*_n—yielding scale- and sign-invariance in UCB and TS while leaving prior-weighted EI and PI unaffected. **Random forest surrogate:** the RF surrogate in SMAC forms piece-wise constant mean and covariance functions, so the acquisition surface is piece-wise constant and the next design point is chosen uniformly at random among candidate optima. To retain this randomness, the prior must be piece-wise constant too; we employ a binning approach that linearly rounds prior values after applying the decay term, with binning granularity decreasing at the same rate as the prior. The binned πBO with an RF surrogate is competitive: it provides substantial improvement over SMAC, improves over sampling from the prior, and quickly recovers from misleading priors. Binning is not required for discrete parameters.

> [!note] 翻譯
> 我們透過在 SMAC——一個同時支援 GP 與隨機森林（RF）代理模型、且涵蓋多數短視型擷取函數的成熟 HPO 框架——的多種變體中實作 πBO，展示其多樣適用性。我們展示了以 GP 代理模型運行的 πBO-EI、πBO-PI、πBO-UCB 與 πBO-TS，以及以 RF 代理模型運行的 πBO-EI。**一般化形式：** 由於 UCB 與 TS 的輸出通常與目標函數同量級同符號，我們對觀測施加一個簡單的仿射轉換——減去現任最佳值（incumbent）y*_n——使 UCB 與 TS 具備尺度與符號不變性，而先驗加權的 EI 與 PI 不受影響。**隨機森林代理模型：** SMAC 中的 RF 代理模型形成逐段常數的平均與共變異數函數，因此擷取函數曲面亦為逐段常數，下一個設計點是自候選最佳解中均勻隨機選出的。為保留此隨機性，先驗也必須是逐段常數；我們採用分箱（binning）方法，在施加衰減項後對先驗值進行線性捨入，分箱粒度以與先驗相同的速率遞減。搭配 RF 代理模型的分箱版 πBO 具競爭力：相較 SMAC 有可觀改進、優於自先驗採樣，且能迅速自誤導性先驗中恢復。離散參數不需分箱。（附錄僅節譯。）

### C Other Prior-based Approaches | 其他先驗式方法

> [!quote] Original
> We demonstrate the performance of πBO for five different functions and HPO surrogates: Branin, Hartmann-6, and three tasks from the Profet suite (SVM, FCNet, XGBoost). We compare all frameworks for priors over the optimum—BOPrO, BOWS, TPE, PS-G. The performance of πBO is shown on two different frameworks—Spearmint and HyperMapper—to allow for fair comparison and display cross-framework consistency.

> [!note] 翻譯
> 我們在五個不同的函數與 HPO 代理基準上展示 πBO 的表現：Branin、Hartmann-6，以及 Profet 套件的三項任務（SVM、FCNet、XGBoost）。我們比較所有最佳解先驗框架——BOPrO、BOWS、TPE、PS-G。πBO 的表現在 Spearmint 與 HyperMapper 兩個不同框架上呈現，以確保公平比較並展示跨框架的一致性。（附錄僅節譯。）

### D Prior Construction | 先驗建構

> [!quote] Original
> For the synthetic benchmarks, we mimic Souza et al. (2021) by offsetting a Gaussian distribution from the optima. For a function of dimensionality d with optimum at x*, the strong prior πs(x) ∼ N(x* + ε, σs) uses a small standard deviation σs = 1% with noise εi ∼ N(0, σs); weak priors are constructed analogously with σw = 10%. The wrong prior πm ∼ N(x̄*, σs) is constructed around the empirical maximum of the objective function without additional noise, and is identical across runs. For the case studies, we choose Gaussian priors with zero correlation between dimensions, constructed once before the experiments and kept fixed throughout.

> [!note] 翻譯
> 對於合成基準，我們仿照 Souza et al. (2021)，將高斯分布自最佳解偏移。對維度為 d、最佳解位於 x* 的函數，強先驗 πs(x) ∼ N(x* + ε, σs) 使用小標準差 σs = 1%，並帶雜訊 εi ∼ N(0, σs)；弱先驗以 σw = 10% 類比建構。錯誤先驗 πm ∼ N(x̄*, σs) 則圍繞目標函數的經驗最大值建構、不加額外雜訊，且在各次執行間相同。對於案例研究，我們選擇維度間零相關的高斯先驗，於實驗前建構一次並全程固定。（附錄僅節譯。）

### E Proofs | 證明

> [!quote] Original
> We provide the complete proofs for Theorem 1 and Corollary 1. The proof of Theorem 1 builds on Lemma 7 and Lemma 8 of Bull (2011): Lemma 7 bounds the posterior variance sn, and Lemma 8 bounds EI by the actual improvement In. Since πBO re-weights EIn by πn, these bounds are adjusted using max_{x∈X} πn(x) for the upper bound and min_{x∈X} πn(x) for the lower bound, yielding min_x πn(x)·max(In − Rs, (τ(−R/σ)/τ(R/σ))In) ≤ EIπ,n(x) ≤ max_x πn(x)·(In + (R + σ)s). Following the setting of Theorem 2 in Bull (2011), the chain of inequalities yields a bound on the EIπ loss that is a factor Cπ,n = (max π / min π)^{β/n} larger than the bound on the EI loss. Corollary 1 follows by computing the fraction of the losses in the limit, which converges to 1. **Sensitivity analysis on Cπ,n (E.1):** displaying Cπ,n at iteration 50 for a centered 1D Gaussian prior while varying σ and β shows that for approximately half of the (σ, β) space the upper bound on the loss is at least 80% of the EI bound, and only a small region of very narrow priors gives a low guaranteed convergence rate.

> [!note] 翻譯
> 我們提供定理 1 與系理 1 的完整證明。定理 1 的證明建立在 Bull (2011) 的引理 7 與引理 8 之上：引理 7 給出後驗變異數 sn 的界，引理 8 以實際改進量 In 為 EI 定界。由於 πBO 以 πn 對 EIn 重新加權，這些界需相應調整——上界使用 max_{x∈X} πn(x)、下界使用 min_{x∈X} πn(x)，得到 min_x πn(x)·max(In − Rs, (τ(−R/σ)/τ(R/σ))In) ≤ EIπ,n(x) ≤ max_x πn(x)·(In + (R + σ)s)。依循 Bull (2011) 定理 2 的設定，一連串不等式推得 EIπ 損失的上界，其比 EI 損失的上界大一個因子 Cπ,n = (max π / min π)^{β/n}。系理 1 則由計算兩損失比值在極限下收斂至 1 而得。**Cπ,n 的敏感度分析（E.1）：** 針對一維中心高斯先驗，在第 50 次迭代時變動 σ 與 β 並繪出 Cπ,n 的值，顯示在約一半的 (σ, β) 空間中，損失上界至少達 EI 上界的 80%，僅有極窄先驗構成的一小塊區域給出較低的保證收斂速率。（附錄僅節譯，完整不等式推導請見原文附錄 E。）

### F Experiment Details | 實驗細節

> [!quote] Original
> Our implementations of πBO require little change in the supporting frameworks (Spearmint and HyperMapper), using a Matérn 5/2 kernel and default settings where possible. To ensure a strictly positive prior, a small constant ε = 10^−12 is added throughout the search space. Initial sampling from the prior is truncated to the search space by re-sampling out-of-bounds points. As no public implementation of BOWS was available, it was reimplemented in Spearmint with warped versions of each benchmark. Benchmarks: Branin (2D synthetic), Hartmann-6 (6D synthetic), Profet SVM (2D), FCNet (6D) and XGBoost (8D) surrogates, OpenML MLP tuning via HPOBench (5D), U-Net Medical (6D, EM segmentation) and ImageNette-128 (6D, FastAI pipeline). Search spaces and priors are summarized in Tables 1–3; the DL case studies used one or two GeForce RTX 2080 Ti GPUs, totalling 180 GPUh (U-Net) and 400 GPUh (ImageNette).

> [!note] 翻譯
> πBO 的實作對支援框架（Spearmint 與 HyperMapper）所需的更動極少，採用 Matérn 5/2 核並盡可能沿用預設設定。為確保先驗嚴格為正，在整個搜尋空間加上一個小常數 ε = 10^−12。自先驗的初始採樣以重新採樣的方式截斷至搜尋空間之內。由於 BOWS 沒有公開實作，我們在 Spearmint 中重新實作之，並為每個基準提供翹曲版本。基準包括：Branin（2 維合成函數）、Hartmann-6（6 維合成函數）、Profet 的 SVM（2 維）、FCNet（6 維）與 XGBoost（8 維）代理基準、經 HPOBench 的 OpenML MLP 調校（5 維）、U-Net Medical（6 維，電子顯微鏡影像分割）與 ImageNette-128（6 維，FastAI 管線）。各基準的搜尋空間與先驗彙整於表 1–3；DL 案例研究使用一至兩張 GeForce RTX 2080 Ti GPU，共計 180 GPU 小時（U-Net）與 400 GPU 小時（ImageNette）。（附錄僅節譯。）

### G Sensitivity to Prior Strength | 對先驗強度的敏感度

> [!quote] Original
> We investigate the performance of πBO when providing priors over the optimum of various qualities, using a grid of prior qualities with varying widths and offsets from the optimum. From Figures 14–18, it is shown that πBO provides substantial performance across most prior qualities for all benchmarks but Branin, and recoups its early losses on the worst priors in the bottom left corner. πBO demonstrates sensitivity to the width of the prior, as the optimization does not progress as quickly for well-located priors with a larger width. Additionally, πBO's improvement over the Spearmint + Mode baseline is further emphasized, as this baseline often fails to meaningfully improve over the mode in early iterations.

> [!note] 翻譯
> 我們以一個由不同寬度與相對最佳解不同偏移量構成的先驗品質網格，考察 πBO 在各種品質之最佳解先驗下的表現。圖 14–18 顯示，除 Branin 外，πBO 在所有基準的多數先驗品質下皆有出色表現，並能在（左下角）最差的先驗上補回早期損失。πBO 對先驗寬度展現敏感性——對位置良好但寬度較大的先驗，最佳化的推進速度較慢。此外，πBO 相對於「Spearmint + 眾數」基線的改進更被凸顯，因為該基線在早期迭代中往往無法有意義地超越眾數本身。（附錄僅節譯。）
