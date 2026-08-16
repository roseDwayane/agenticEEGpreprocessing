---
citation_key: "LiuEtAl2024"
title: "Large Language Models to Enhance Bayesian Optimization"
authors: "Tennison Liu; Nicolás Astorga; Nabeel Seedat; Mihaela van der Schaar"
year: 2024
doi: "10.48550/arxiv.2402.03921"
source: "arXiv (2402.03921)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
conversion: "pdftotext -layout (automated)"
language: "bilingual-en-zh"
---

# Large Language Models to Enhance Bayesian Optimization | 以大型語言模型強化貝葉斯最佳化

> [!abstract] 重點摘要
> - 提出 LLAMBO 框架，將大型語言模型（Large Language Model, LLM）的能力整合進貝葉斯最佳化（Bayesian optimization, BO），把 BO 問題以自然語言表述，讓 LLM 依據歷史評估迭代地提出並評估候選解。
> - 針對 BO 管線的三個關鍵元件提出強化方法：零樣本（zero-shot）暖啟動（warm-starting）、透過情境內學習（in-context learning, ICL）建構的判別式與生成式代理模型（surrogate model），以及以目標值為條件的候選點採樣機制。
> - 全程僅使用情境內提示，不需微調 LLM；設計上模組化，各元件可單獨嵌入既有 BO 框架，亦可作為端到端方法整體運作。
> - 在 Bayesmark 與 HPOBench 共 74 項超參數調校（hyperparameter tuning, HPT）任務上系統性驗證：LLAMBO 在觀測樣本稀少的搜尋早期尤其突出，端到端表現優於 SMAC3、Optuna (TPE)、SKOpt (GP) 等成熟基線。
> - 透過私有與合成資料集排除記憶（memorization）疑慮，並以消融實驗證實問題描述（先驗知識）與任務指令對效能的關鍵作用。
> - 主要代價是 LLM 推論的計算成本較高；作者主張以此換取樣本效率，並建議與計算上更輕量的方法混合使用。

---

## Abstract | 摘要

> [!quote] Original
> Bayesian optimization (BO) is a powerful approach for optimizing complex and expensive-to-evaluate black-box functions. Its importance is underscored in many applications, notably including hyperparameter tuning, but its efficacy depends on efficiently balancing exploration and exploitation. While there has been substantial progress in BO methods, striking this balance remains a delicate process. In this light, we present LLAMBO, a novel approach that integrates the capabilities of Large Language Models (LLM) within BO. At a high level, we frame the BO problem in natural language, enabling LLMs to iteratively propose and evaluate promising solutions conditioned on historical evaluations. More specifically, we explore how combining contextual understanding, few-shot learning proficiency, and domain knowledge of LLMs can improve model-based BO. Our findings illustrate that LLAMBO is effective at zero-shot warmstarting, and enhances surrogate modeling and candidate sampling, especially in the early stages of search when observations are sparse. Our approach is performed in context and does not require LLM finetuning. Additionally, it is modular by design, allowing individual components to be integrated into existing BO frameworks, or function cohesively as an end-to-end method. We empirically validate LLAMBO's efficacy on the problem of hyperparameter tuning, highlighting strong empirical performance across a range of diverse benchmarks, proprietary, and synthetic tasks.

> [!note] 翻譯
> 貝葉斯最佳化（Bayesian optimization, BO）是最佳化複雜且評估成本高昂之黑盒函數的強大方法。其重要性在許多應用中得到彰顯，特別是超參數調校（hyperparameter tuning），但其效能取決於能否有效平衡探索（exploration）與利用（exploitation）。儘管 BO 方法已有長足進展，如何拿捏這一平衡仍是一項精細的工作。有鑑於此，我們提出 LLAMBO——一種將大型語言模型（Large Language Model, LLM）能力整合進 BO 的新方法。從宏觀層面看，我們將 BO 問題以自然語言表述，使 LLM 能夠以歷史評估為條件，迭代地提出並評估有潛力的解。更具體而言，我們探討如何結合 LLM 的脈絡理解能力、少樣本（few-shot）學習能力與領域知識，來改進基於模型的 BO。我們的研究結果顯示，LLAMBO 在零樣本（zero-shot）暖啟動（warmstarting）方面十分有效，並能強化代理模型（surrogate model）建構與候選點採樣，尤其是在觀測稀少的搜尋早期階段。我們的方法完全在情境內（in context）執行，無需微調 LLM。此外，其設計具模組化特性，個別元件可整合進既有 BO 框架，亦可作為端到端方法協同運作。我們在超參數調校問題上對 LLAMBO 的效能進行實證驗證，在多樣化的公開基準、專有與合成任務上均展現優異的實證表現。

---

## 1 Introduction | 引言

> [!quote] Original
> **Black-box optimization.** Expensive black-box functions are common in many disciplines and applications including robotics [1, 2], experimental design [3], drug discovery [4], interface design [5] and, in machine learning, hyperparameter tuning [6, 7, 8]. Bayesian optimization (BO) is an efficient model-based approach for globally optimizing these functions [9, 10]. BO's effectiveness lies in its ability to operate based on a limited set of observations without the need for direct access to the objective function or its gradients. It does so by using observed data to learn a surrogate model to approximate the black-box function and a candidate point sampler to iteratively propose potentially good points. In each trial, the acquisition function selects the proposed point with the highest utility, based on surrogate evaluations. This chosen point undergoes evaluation, and the cycle continues.

> [!note] 翻譯
> **黑盒最佳化。** 評估成本高昂的黑盒函數常見於許多學科與應用，包括機器人學 [1, 2]、實驗設計 [3]、藥物發現 [4]、介面設計 [5]，以及機器學習中的超參數調校 [6, 7, 8]。貝葉斯最佳化（BO）是一種對這類函數進行全域最佳化的高效模型式方法 [9, 10]。BO 的效能來自於它能僅憑有限的觀測運作，而無需直接存取目標函數或其梯度。其做法是利用觀測資料學習一個代理模型（surrogate model）以逼近黑盒函數，並以候選點採樣器（candidate point sampler）迭代地提出潛在的優良點。在每一次試驗（trial）中，擷取函數（acquisition function）依據代理模型的評估，從提出的點中選出效用最高者。該點隨後接受實際評估，循環由此持續。

> [!quote] Original
> **Challenges of search efficiency.** For BO, the name of the game is efficient search, but this efficiency largely depends on the quality of the surrogate model and candidate point sampler to quickly identify high-potential regions [11]. Given that BO is designated for scenarios with limited observations, constructing an accurate ▶ surrogate model with sparse observations is inherently challenging. Additionally, the model can be sensitive to misspecification, and even slight misrepresentations of the model can introduce undesired bias, skewing the ▶ sampling of potential solutions [12]. A further challenge arises when considering the integration of ▶ prior knowledge, especially in effectively transferring knowledge about correlations in the optimization space to new tasks.
>
> At the core, these challenges pertain to accurately learning the objective function and effectively generating candidate solutions with limited data. This scenario is typically framed as the few-shot setting, a context that demands swift learning and generalization from very few examples [13]. Interestingly, such challenges of the few-shot paradigm align with the proficiencies of Large Language Models (LLM). Contemporary LLMs, which have been pre-trained on Internet-scale data, showcase an exceptional capacity to generalize from sparse data, enabling them to excel in few-shot prediction, generation [14, 15, 16, 17, 18], and contextual understanding [19, 20]. They achieve this remarkable sample-efficient performance, in part, by exploiting encoded priors [21, 22].

> [!note] 翻譯
> **搜尋效率的挑戰。** 對 BO 而言，關鍵在於高效搜尋，而此效率很大程度上取決於代理模型與候選點採樣器能否快速鎖定高潛力區域 [11]。既然 BO 是為觀測有限的情境而設計，在稀疏觀測下建構準確的 ▶ 代理模型本質上就相當困難。此外，模型對誤設（misspecification）可能十分敏感，即使是輕微的模型失真也會引入不必要的偏差，扭曲 ▶ 潛在解的採樣 [12]。再者，如何整合 ▶ 先驗知識也構成挑戰，特別是如何將最佳化空間中關於相關性的知識有效遷移至新任務。
>
> 這些挑戰的核心在於：如何在資料有限的情況下準確學習目標函數並有效生成候選解。此情境通常被界定為少樣本（few-shot）設定——一種要求從極少範例中快速學習與泛化的情境 [13]。有趣的是，少樣本典範的這些挑戰恰與大型語言模型（LLM）的長處相契合。當代 LLM 經過網際網路規模資料的預訓練，展現出從稀疏資料泛化的卓越能力，使其在少樣本預測、生成 [14, 15, 16, 17, 18] 與脈絡理解 [19, 20] 上表現出色。它們之所以能達成如此優異的樣本效率，部分原因在於善用了編碼於模型中的先驗（priors）[21, 22]。

> [!quote] Original
> **Key considerations.** This study examines the potential of extending the capabilities of LLMs beyond standard natural language tasks to enhance model-based BO. Our approach is grounded in representing BO components using natural language, introducing novel methods to effectively capture LLM's distinct strengths. This exploration gives rise to two key questions: [Q1] Can LLMs, with their encoded knowledge and few-shot learning abilities, enhance key elements of BO, including the surrogate model and candidate point sampler? [Q2] How effectively can LLM-augmented BO components operate as a cohesive, end-to-end pipeline? In answering these questions, we chose hyperparameter tuning (HPT) as our initial area of investigation. This is for two main reasons: firstly, the extensive knowledge potentially acquired by LLMs about HPT during pretraining, coupled with its relatively low-dimensional nature, makes it an ideal test bed to probe the applications of LLMs within BO. Secondly, HPT is practically important and a core enabler in many applications.

> [!note] 翻譯
> **核心考量。** 本研究檢視將 LLM 的能力延伸至標準自然語言任務之外、用以強化模型式 BO 的潛力。我們的方法立基於以自然語言表徵 BO 的各個元件，並引入新方法以有效發揮 LLM 的獨特優勢。此探索引出兩個關鍵問題：[Q1] LLM 憑藉其編碼的知識與少樣本學習能力，能否強化 BO 的關鍵元素，包括代理模型與候選點採樣器？[Q2] 經 LLM 增強的 BO 元件作為一個連貫的端到端管線，其運作成效如何？為回答這些問題，我們選擇超參數調校（hyperparameter tuning, HPT）作為初始研究領域，主要理由有二：其一，LLM 在預訓練期間可能已習得大量關於 HPT 的知識，加上 HPT 相對低維的特性，使其成為探究 LLM 在 BO 中應用的理想試驗場；其二，HPT 具有實務重要性，是許多應用的核心推手。

> [!quote] Original
> **Contributions.** We present LLAMBO, a novel approach for integrating the capabilities of LLMs into BO. To understand the performance gains from this integration, we execute a systematic investigation, exploring the aforementioned questions. Our primary contributions are:
> - We propose LLAMBO, a novel approach to enhance components of model-based BO with LLMs,
> - We systematically investigate the enhancements of LLAMBO throughout the BO pipeline, showcasing significant improvements to ▶ zero-shot warmstarting, the ▶ efficacy of the surrogate model, and the ▶ efficiency of candidate sampling,
> - We empirically investigated the end-to-end performance of LLAMBO for hyperparameter tuning, demonstrating strong performance on diverse benchmarks.
>
> [Figure 1: Overview of LLAMBO. In order: LLAMBO can initialize BO through zero-shot warmstarting, efficiently sample candidate points from high-potential regions given past observations and problem description, and evaluate these candidate points via a surrogate model.]

> [!note] 翻譯
> **貢獻。** 我們提出 LLAMBO，一種將 LLM 能力整合進 BO 的新方法。為了解此整合帶來的效能增益，我們執行系統性研究，探討前述兩個問題。我們的主要貢獻為：
> - 提出 LLAMBO——以 LLM 強化模型式 BO 各元件的新方法；
> - 系統性地考察 LLAMBO 對整條 BO 管線的強化效果，展示其在 ▶ 零樣本暖啟動、▶ 代理模型效能、以及 ▶ 候選點採樣效率上的顯著改進；
> - 對 LLAMBO 在超參數調校上的端到端表現進行實證研究，在多樣化基準上展現優異效能。
>
> [圖 1：LLAMBO 概覽。依序為：LLAMBO 可透過零樣本暖啟動初始化 BO；在給定過往觀測與問題描述下，高效地自高潛力區域採樣候選點；並經由代理模型評估這些候選點。]

---

## 2 LLAMBO: LLMs to Enhance BO | LLAMBO：以 LLM 強化貝葉斯最佳化

> [!quote] Original
> Figure 1 illustrates the LLAMBO framework. Fundamentally, our methodology translates different components in the BO pipeline into natural language. This allows the LLM to iteratively suggest and evaluate solutions, informed both by the BO problem description and search history.

> [!note] 翻譯
> 圖 1 展示了 LLAMBO 框架。從根本上說，我們的方法論是將 BO 管線中的不同元件轉譯為自然語言，使 LLM 得以在 BO 問題描述與搜尋歷史的雙重資訊下，迭代地建議並評估解。

### 2.1 The Integration of LLMs into BO | LLM 與 BO 的整合

> [!quote] Original
> **Preliminaries.** To aid with exposition, we introduce the following notation. Let us consider an objective function, f : H → S, where h ∈ H ⊆ R^d is the d-dimensional input, and S ∈ R is the output space. We aim to find h* ∈ H that minimizes this objective function:
>
> h* = arg min_{h∈H} f(h)
>
> where f is a costly black-box function without accessible gradient information. To overcome these limitations, BO employs a surrogate model to approximate f and a candidate sampler to generate h ∈ H. In general terms, a surrogate model can be viewed as a machine learning (ML) method producing the predictive distribution of s given h and some observed data Dn = {(hi, si)}_{i=1}^n:
>
> p(s|h; Dn) = ∫_Θ p(s|h, θ; Dn) p(θ|h; Dn) dθ
>
> Here, the marginalization is over θ, a latent variable that captures the underlying structure between s and h. Specifically, p(θ|Dn, h) ∝ p(Dn|θ, h)p(θ) describes the posterior distribution after observing some data, with p(θ) being the prior knowledge of this underlying structure. The candidate point sampler can be viewed similarly, as generating samples from a posterior distribution: p(h|Dn) = ∫_Θ p(h|θ, Dn) p(θ|Dn) dθ. These priors, p(θ), can play a significant role, especially given the typically sparse observations in BO [23]. However, in practice, many BO applications adopt non-informative priors, potentially missing out on valuable domain-specific knowledge. The challenge lies not just in the inclusion of prior knowledge, but also in accurately learning the associated predictive distribution with ML methods, especially in settings with limited observations.

> [!note] 翻譯
> **前置定義。** 為便於闡述，我們引入以下符號。考慮目標函數 f : H → S，其中 h ∈ H ⊆ R^d 為 d 維輸入，S ∈ R 為輸出空間。我們的目標是找到使目標函數最小化的 h* ∈ H：
>
> h* = arg min_{h∈H} f(h)
>
> 其中 f 是評估成本高昂、且無法取得梯度資訊的黑盒函數。為克服這些限制，BO 使用代理模型逼近 f，並以候選採樣器生成 h ∈ H。概括而言，代理模型可視為一種機器學習（ML）方法，在給定 h 與觀測資料 Dn = {(hi, si)}_{i=1}^n 的條件下，產生 s 的預測分布：
>
> p(s|h; Dn) = ∫_Θ p(s|h, θ; Dn) p(θ|h; Dn) dθ
>
> 此處的邊際化是對潛變數 θ 進行的，θ 刻畫了 s 與 h 之間的底層結構。具體而言，p(θ|Dn, h) ∝ p(Dn|θ, h)p(θ) 描述觀測到資料後的後驗分布，其中 p(θ) 即為此底層結構的先驗知識。候選點採樣器亦可類似地視為自後驗分布生成樣本：p(h|Dn) = ∫_Θ p(h|θ, Dn) p(θ|Dn) dθ。這些先驗 p(θ) 可能扮演重要角色，尤其考慮到 BO 中觀測通常十分稀疏 [23]。（註腳 1：值得一提的是編碼先驗的不同途徑：高斯過程（Gaussian Process）[7] 對函數本身嵌入先驗分布 p(f)，而貝葉斯神經網路則對權重使用先驗分布 p(w) [24]。）然而在實務上，許多 BO 應用採用無資訊先驗（non-informative priors），可能因此錯失寶貴的領域知識。挑戰不僅在於納入先驗知識，也在於如何以 ML 方法在觀測有限的情境下準確學習對應的預測分布。

> [!quote] Original
> **Synergy of LLMs and BO.** In this light, LLMs can offer significant enhancements due to the following capabilities: (1) Prior knowledge: Recently, [25] explained LLM in-context learning (ICL) as performing implicit Bayesian inference [14]. This raises the interesting prospect of using ICL to tap into an LLM's encoded knowledge for BO. In this framework, p(θ) represents the priors related to the optimization problem and domain-specific correlations absorbed through pretraining [15, 26]. (2) ICL: Learning generalizable models given only limited observations is highly challenging. LLMs have demonstrated the capacity to generalize from a few in-context examples, an ability that can directly complement BO's needs for sample-efficient exploration [14, 27, 28]. (3) Contextual understanding: LLMs are adept at processing contextual information, especially via natural language [29]. This offers a versatile interface to incorporate meta-features about optimization tasks, search spaces, and auxiliary details that can improve search performance.

> [!note] 翻譯
> **LLM 與 BO 的協同效應。** 由此觀之，LLM 憑藉以下能力可帶來顯著強化：(1) 先驗知識：近期 [25] 將 LLM 的情境內學習（in-context learning, ICL）解釋為隱式貝葉斯推論 [14]。這帶來一個有趣的前景——利用 ICL 汲取 LLM 內部編碼的知識以服務於 BO。在此框架下，p(θ) 代表與最佳化問題相關的先驗，以及預訓練過程中吸收的領域特定相關性 [15, 26]。(2) ICL：僅憑有限觀測學得可泛化的模型極具挑戰性。LLM 已展現出從少數情境內範例泛化的能力，恰可直接補足 BO 對高樣本效率探索的需求 [14, 27, 28]。(3) 脈絡理解：LLM 擅長處理脈絡資訊，尤其是經由自然語言 [29]。這提供了一個靈活的介面，可納入關於最佳化任務、搜尋空間及其他有助於搜尋效能之輔助細節的元特徵（meta-features）。

> [!quote] Original
> **Operationalizing this synergy.** Despite these hypothesized advantages of LLMs, effectively capitalizing on them in an iterative optimization framework like BO is challenging. Recently, [30] explored the use of LLM-based BO for molecules. While this work primarily focused on the surrogate model, we introduce novel methods for LLM enhancement of multiple components of BO and conduct a systematic investigation to understand the performance gains offered by this integration. Specifically, we employ ICL to enhance three key components of BO (Figure 1):
> - **Warmstarting:** Warmstarting initializes the optimization process with a pre-identified set of n points, denoted as {hi}_{i=1}^n, which are evaluated first to build up a meaningful representation of f. We propose a strategy to identify promising initializations through zero-shot prompting.
> - **Sampling candidates:** The sampling process proposes points {h̃k}_{k=1}^K that are considered for future evaluations. Drawing inspiration from TPE [6], we propose a mechanism to conditionally sample candidates based on a target objective value s′: h̃k ∼ p(h|s′; Dn). Here, we employ ICL by providing the optimization history as few-shot examples.
> - **Surrogate modeling:** The surrogate model, denoted as p(s|h; Dn), is an approximation of f and is trained using Dn. Specifically, we introduce two methods, leveraging ICL on the optimization history: a discriminative approach that produces regression estimates with uncertainty, and a generative approach that scores via binary classification.

> [!note] 翻譯
> **落實此協同效應。** 儘管 LLM 具備上述假設性優勢，要在 BO 這類迭代最佳化框架中有效善用它們仍具挑戰。近期 [30] 探討了以 LLM 為基礎的 BO 於分子領域的應用，但該研究主要聚焦於代理模型；相較之下，我們為 BO 的多個元件引入新的 LLM 強化方法，並進行系統性研究以理解此整合帶來的效能增益。具體而言，我們運用 ICL 強化 BO 的三個關鍵元件（圖 1）：
> - **暖啟動（warmstarting）：** 暖啟動以一組預先選定的 n 個點 {hi}_{i=1}^n 初始化最佳化流程，這些點會被優先評估，以建立對 f 的有意義表徵。我們提出透過零樣本提示（zero-shot prompting）辨識有潛力初始點的策略。
> - **候選點採樣：** 採樣程序提出供未來評估考量的點 {h̃k}_{k=1}^K。受 TPE [6] 啟發，我們提出一種以目標值 s′ 為條件採樣候選點的機制：h̃k ∼ p(h|s′; Dn)。此處我們藉由將最佳化歷史作為少樣本範例來運用 ICL。
> - **代理模型建構：** 代理模型 p(s|h; Dn) 是 f 的近似，以 Dn 訓練。具體而言，我們基於最佳化歷史的 ICL 引入兩種方法：其一為判別式（discriminative）方法，產生附帶不確定性的迴歸估計；其二為生成式（generative）方法，透過二元分類進行評分。

> [!quote] Original
> **Overview of investigation.** Having outlined our approach for leveraging LLMs in BO, we now describe the structure of our investigative study. The framework is presented below, and centres around the two aforementioned questions [Q1-2]. We begin by analyzing each component in isolation while keeping other factors consistent whenever possible. We conclude our study with an assessment of LLAMBO's performance as an end-to-end BO method.
>
> | Section | Method | Goal and Method | Q's |
> |---|---|---|---|
> | Section 4 | Warmstarting | Enhancing optimization with warmstarting from LLM prior | [Q1] |
> | Section 5 | Surrogate model | Improving quality of surrogate model in few-shot settings through ICL | [Q1] |
> | Section 6 | Candidate sampling | Conditional sampling of high-potential points for desired s* via ICL | [Q1] |
> | Section 7 | End-to-end BO | Augmenting end-to-end BO performance | [Q1][Q2] |
>
> **Experimental setup.** We conduct our investigations using 74 tasks extracted from Bayesmark and HPOBench [31, 32] and OpenAI's GPT-3.5 Language Model (see Appendix D for detailed experimental procedures). While it is important to recognize that the choice of LLM can substantially influence the results of optimization, we note that the overarching methodology and fundamental insights covered in this work are broadly applicable beyond the specifics of any single LLM.

> [!note] 翻譯
> **研究架構概覽。** 在概述了於 BO 中運用 LLM 的方法後，我們說明本研究的結構。研究框架如下表所示，圍繞前述兩個問題 [Q1-2] 展開。我們首先在盡可能保持其他因素一致的前提下，逐一分析各元件；最後以 LLAMBO 作為端到端 BO 方法的效能評估作結。
>
> | 章節 | 方法 | 目標與做法 | 問題 |
> |---|---|---|---|
> | 第 4 節 | 暖啟動 | 以 LLM 先驗進行暖啟動以強化最佳化 | [Q1] |
> | 第 5 節 | 代理模型 | 透過 ICL 提升少樣本情境下代理模型的品質 | [Q1] |
> | 第 6 節 | 候選點採樣 | 透過 ICL 針對期望的 s* 條件式採樣高潛力點 | [Q1] |
> | 第 7 節 | 端到端 BO | 增進端到端 BO 效能 | [Q1][Q2] |
>
> **實驗設置。** 我們使用取自 Bayesmark 與 HPOBench [31, 32] 的 74 項任務以及 OpenAI 的 GPT-3.5 語言模型進行研究（詳細實驗流程見附錄 D）。雖然必須承認 LLM 的選擇可能大幅影響最佳化結果，但我們指出，本研究涵蓋的整體方法論與基本洞見，其適用性超越任何單一 LLM 的具體細節。

### 2.2 BO Prompt Design | BO 提示設計

> [!quote] Original
> The proposed integrations are realized through structured natural language queries to the LLM. While the specifics of each query differ (e.g. for surrogate modeling and sampling), they are constructed from three essential elements. For the complete prompts, please refer to Appendix C.1.
> - **Problem description.** This includes information of the input space H, the output space S, and the objective function f. Specifically for HPT, this entails a <MODEL CARD>, describing the ML model being optimized (f), the hyperparameters (H), and the scoring metric (S). We also include a <DATA CARD> containing dataset attributes.
> - **Optimization history.** The history contains the sequence of points and scores observed during the optimization process, captured in Dn. The observed points are provided as few-shot examples for ICL of the surrogate model and candidate point sampler.
> - **Task instructions.** For each component under consideration (e.g. surrogate model), we include task-specific instructions on desired inference and guidelines on the format of the response.

> [!note] 翻譯
> 前述整合是透過對 LLM 發出結構化的自然語言查詢實現的。雖然各查詢的細節不盡相同（例如用於代理模型建構與採樣者各異），它們皆由三個基本要素構成。完整提示請參閱附錄 C.1。
> - **問題描述。** 包含輸入空間 H、輸出空間 S 與目標函數 f 的資訊。就 HPT 而言，這對應一份 <MODEL CARD>（模型卡），描述被最佳化的 ML 模型（f）、超參數（H）與評分指標（S）；我們同時納入包含資料集屬性的 <DATA CARD>（資料卡）。
> - **最佳化歷史。** 歷史包含最佳化過程中觀測到的點與分數序列，即 Dn。觀測點以少樣本範例的形式提供，用於代理模型與候選點採樣器的 ICL。
> - **任務指令。** 針對所考量的每個元件（如代理模型），我們納入關於期望推論內容的任務特定指令，以及回覆格式的規範。

---

## 3 Related Works | 相關研究

> [!quote] Original
> **Bayesian optimization.** At its core, BO relies on probabilistic modeling. One widely adopted technique is the Gaussian Processes (GP) due to their flexibility and analytical tractability [7, 33]. Recent works have sought to enhance their expressiveness through deep kernel GP [34, 35] and manifold GPs [36]. On another front, NN-based and tree-based surrogates have been considered, particularly due to their flexibility in high-dimensional or hierarchical optimization problems [24, 37, 38, 39]. Tree-structured Parzen Estimator (TPE) is an alternate approach based on the generative surrogate model p(h|s) [6, 40, 41]. Recent trends have also leaned towards Transformers as surrogate models [42, 43]. BO is commonly used to optimize expensive, black-box functions, including in robotics and experimental design [1, 2, 3] and most prominently for autoML [40, 44, 45, 46].

> [!note] 翻譯
> **貝葉斯最佳化。** BO 的核心是機率式建模。高斯過程（Gaussian Process, GP）因其彈性與解析上的可處理性而被廣泛採用 [7, 33]。近期研究試圖透過深度核 GP（deep kernel GP）[34, 35] 與流形 GP（manifold GP）[36] 提升其表達能力。另一方面，基於神經網路與樹模型的代理模型也受到關注，特別是因其在高維或階層式最佳化問題中的彈性 [24, 37, 38, 39]。樹狀 Parzen 估計器（Tree-structured Parzen Estimator, TPE）則是基於生成式代理模型 p(h|s) 的另一種途徑 [6, 40, 41]。近期趨勢亦傾向以 Transformer 作為代理模型 [42, 43]。BO 常用於最佳化昂貴的黑盒函數，包括機器人學與實驗設計 [1, 2, 3]，其中最著名的應用是自動化機器學習（autoML）[40, 44, 45, 46]。

> [!quote] Original
> **Transfer learning for BO.** Recent research in BO has explored transfer learning to improve optimization across similar domains. Prominent among these are multitask GPs, designed to optimize several related black-box functions by leveraging common structures or patterns across tasks [47, 48, 49]. Other approaches have sought to transfer learnings from previously optimized functions to new functions [50, 51]. However, these approaches only consider inductive transfer over a fixed search space, i.e. all tasks share the same search space. More recently, [42] introduced a pretrained Transformer for meta-learning across tasks with different search spaces. In our work, we explore a lightweight alternative by harnessing prior knowledge contained in generalist LLMs, which does not require dedicated pretraining and structured results collected from related optimization problems.
>
> **LLMs and optimization.** Recent works have explored the use of LLMs for optimization tasks, notably for prompt optimization [52, 53, 54] and as genetic search operators in evolutionary algorithms [55, 56, 57]. Of particular note is the research that delves into LLM for BO of molecules in [30], which primarily focused on surrogate modeling. In contrast, we introduce novel methods for LLM enhancement of multiple components of model-based BO and conduct a systematic investigation to understand the performance gains offered by this integration.

> [!note] 翻譯
> **BO 的遷移學習。** 近期 BO 研究探索了遷移學習（transfer learning），以改進相似領域間的最佳化。其中最具代表性的是多任務 GP（multitask GP），其藉由利用任務間的共同結構或模式，同時最佳化多個相關的黑盒函數 [47, 48, 49]。其他方法則試圖將先前已最佳化函數的經驗遷移至新函數 [50, 51]。然而，這些方法僅考慮固定搜尋空間上的歸納式遷移，亦即所有任務共享同一搜尋空間。較近期地，[42] 引入預訓練 Transformer，以在不同搜尋空間的任務間進行元學習（meta-learning）。在本研究中，我們探索一種輕量化的替代方案——善用通用型 LLM 所蘊含的先驗知識，既不需要專門的預訓練，也不需要自相關最佳化問題蒐集的結構化結果。
>
> **LLM 與最佳化。** 近期研究探索了 LLM 於最佳化任務的應用，特別是提示最佳化（prompt optimization）[52, 53, 54]，以及作為演化演算法中的遺傳搜尋算子 [55, 56, 57]。尤其值得注意的是 [30] 針對分子 BO 的 LLM 研究，其主要聚焦於代理模型。相較之下，我們為模型式 BO 的多個元件引入新的 LLM 強化方法，並進行系統性研究以理解此整合帶來的效能增益。

---

## 4 Warmstarting the BO Process | 暖啟動 BO 流程

> [!quote] Original
> **Motivation.** We start by analyzing whether LLMs can transfer prior knowledge about an optimization problem through warmstarting. While warmstarting can accelerate convergence by supplying more insightful initial points, conventional approaches require prior results collected from similar optimization problems [58, 59]. This data collection can be resource-intensive and might not be feasible for certain applications. In contrast, we explore the use of LLMs for warmstarting as a more efficient and lightweight alternative, allowing the acquisition of warmstarting points without explicitly requiring data collection from related problems.
>
> **Method.** LLAMBO employs zero-shot prompting to sample points for warmstarting. We explore three distinct settings, each providing different levels of information about the optimization problem. ▶ No context: the LLM is prompted to recommend good initial hyperparameters for a given ML model, but no dataset details are provided; ▶ Partial context: provides meta-features about the dataset through the <DATA CARD>, including the number of samples, features, the type of features (categorical vs continuous), and the learning task (e.g. classification); ▶ Full context: further augments the <DATA CARD> with information on marginal distributions, inter-feature correlations, and feature-label correlations.

> [!note] 翻譯
> **動機。** 我們首先分析 LLM 能否透過暖啟動遷移關於最佳化問題的先驗知識。暖啟動雖能藉由提供更具洞察力的初始點加速收斂，但傳統做法需要事先自相似最佳化問題蒐集結果 [58, 59]，此類資料蒐集可能耗費大量資源，且在某些應用中並不可行。相對地，我們探索以 LLM 進行暖啟動這一更高效、輕量的替代方案，使暖啟動點的取得無需明確依賴相關問題的資料蒐集。
>
> **方法。** LLAMBO 採用零樣本提示來採樣暖啟動點。我們探索三種不同設定，各自提供關於最佳化問題不同程度的資訊。▶ 無脈絡（No context）：提示 LLM 為給定 ML 模型推薦良好的初始超參數，但不提供任何資料集細節；▶ 部分脈絡（Partial context）：透過 <DATA CARD> 提供資料集的元特徵，包括樣本數、特徵數、特徵型態（類別型或連續型）與學習任務（如分類）；▶ 完整脈絡（Full context）：進一步在 <DATA CARD> 中補充邊際分布、特徵間相關性與特徵—標籤相關性等資訊。

> [!quote] Original
> **Experimental setup.** To evaluate the impact of warmstarting, we employ two widely adopted BO methods: Gaussian Processes (GP) [7] and Tree Parzen Estimator (TPE) [6]. We compare these against random initialization techniques, namely Random, Sobol, and Latin Hypercube (HCube) sampling. Each search begins with 5 initialization points and proceeds for 25 trials, and we report average results over ten seeded searches. Our evaluation metrics focus on two aspects: search performance and the diversity of the initialization points. To assess search performance, we adopt the normalized regret metric, defined as min_{h∈Ht}(f(h) − s*_min)/(s*_max − s*_min), where Ht denotes the points chosen up to trial t, and s*_min and s*_max represent the best and worse scores, respectively [60]. To assess diversity, we use the generalized variance: det(Σ), with Σ being the covariance matrix of the hyperparameters.

> [!note] 翻譯
> **實驗設置。** 為評估暖啟動的影響，我們採用兩種廣泛使用的 BO 方法：高斯過程（GP）[7] 與樹狀 Parzen 估計器（TPE）[6]，並與隨機初始化技術比較，即 Random、Sobol 與拉丁超立方（Latin Hypercube, HCube）採樣。每次搜尋以 5 個初始點開始，進行 25 次試驗，我們回報十次不同隨機種子搜尋的平均結果。評估指標聚焦於兩個面向：搜尋效能與初始點的多樣性。搜尋效能採用正規化遺憾值（normalized regret），定義為 min_{h∈Ht}(f(h) − s*_min)/(s*_max − s*_min)，其中 Ht 為截至第 t 次試驗所選的點，s*_min 與 s*_max 分別代表最佳與最差分數 [60]。多樣性則採用廣義變異數（generalized variance）：det(Σ)，其中 Σ 為超參數的共變異數矩陣。

> [!quote] Original
> **Empirical insights.** (1) Performance: Figure 2 (Top) visualizes the average regret across all tasks. We begin our analysis with a sanity check—namely, warmstarting using no context surpasses the performance of random initialization techniques. This verifies that our LLM possesses a basic knowledge of generalizable correlations (independent of specific problems) between hyperparameters. Interestingly, we observe that providing additional information about the dataset improves the search performance when warmstarting for both partial context and full context. This is particularly prominent in the early stages of the search (i.e. trials < 5). However, these initial gains are maintained as the search progresses. (1a) Correlations: To explore deeper, we compute the correlation matrix of sampled warmstarting points depicted in (Middle) (with further analysis in Appendix E). Our findings reveal that the points recommended by the LLM exhibit considerably greater correlations between hyperparameters compared to those from random initialization. More strikingly, the correlation matrices computed for different tasks reveal different correlation structures, suggesting that the LLM is dynamically adjusting its suggestions to different optimization problems. (2) Diversity: A closer look at the diversity of warmstarting points in (Bottom) reveals that their generalized variance is typically lower than that of randomly initialized points. This trend aligns with our expectations: higher correlations often lead to a decreased determinant of the covariance matrix due to 'redundant' information. Since random initialization methods sample each hyperparameter independently, they exhibit lower correlation levels, resulting in higher diversity.
>
> *Warmstart initialization via zero-shot prompting is an efficient strategy to transfer knowledge about correlations in the optimization landscape, enhancing search performance.*

> [!note] 翻譯
> **實證洞見。** (1) 效能：圖 2（上）呈現所有任務的平均遺憾值。我們的分析從一項基本檢驗開始——即使不提供任何脈絡的暖啟動，其表現也優於隨機初始化技術。這驗證了我們的 LLM 具備關於超參數間可泛化相關性（與特定問題無關）的基本知識。有趣的是，提供更多資料集資訊（部分脈絡與完整脈絡）均能提升暖啟動下的搜尋效能，在搜尋早期（試驗數 < 5）尤為顯著；且這些初期優勢在搜尋推進過程中得以維持。(1a) 相關性：為更深入探究，我們計算了採樣暖啟動點的相關矩陣，如圖 2（中）所示（進一步分析見附錄 E）。結果顯示，LLM 推薦的點在超參數之間展現出遠高於隨機初始化的相關性。更引人注目的是，不同任務所計算出的相關矩陣呈現不同的相關結構，顯示 LLM 會針對不同的最佳化問題動態調整其建議。(2) 多樣性：進一步觀察暖啟動點的多樣性（圖 2 下）可發現，其廣義變異數通常低於隨機初始化的點。此趨勢符合我們的預期：較高的相關性往往因「冗餘」資訊而使共變異數矩陣的行列式下降。由於隨機初始化方法對每個超參數獨立採樣，其相關程度較低，因而多樣性較高。
>
> **小結：透過零樣本提示進行暖啟動初始化，是遷移最佳化地景中相關性知識的高效策略，能提升搜尋效能。**

---

## 5 Surrogate Modeling | 代理模型建構

> [!quote] Original
> **Motivation.** Surrogate modeling, a core component of BO, aims to learn accurate representations of complex functions using only a limited set of evaluations. The efficacy of these models depends on their capacity to generalize and make accurate predictions from sparse observations. Recent studies have underscored LLM's remarkable ability to perform few-shot learning [25, 61]. Building on this, we propose two tailored approaches to surrogate modeling via ICL: (1) a discriminative approach to predict the mean and uncertainty of the objective value of a given candidate point, i.e. p(s|h; Dn) in Section 5.1; and (2) a generative approach that scores each point based on the probability that its objective value is better than some performance threshold τ, i.e. p(s ≤ τ|h; Dn) in Appendix B. These represent two distinct approaches, with (1) framing surrogate modeling as a regression problem, while (2) views surrogate modeling as probabilistic binary classification.

> [!note] 翻譯
> **動機。** 代理模型建構是 BO 的核心元件，旨在僅憑有限的評估學得複雜函數的準確表徵。這類模型的效能取決於其自稀疏觀測泛化並做出準確預測的能力。近期研究強調了 LLM 卓越的少樣本學習能力 [25, 61]。基於此，我們提出兩種透過 ICL 量身打造的代理模型建構方法：(1) 判別式方法——預測給定候選點目標值的平均數與不確定性，即第 5.1 節的 p(s|h; Dn)；(2) 生成式方法——依據各點目標值優於某效能門檻 τ 的機率為其評分，即附錄 B 的 p(s ≤ τ|h; Dn)。二者代表兩條截然不同的路徑：(1) 將代理模型建構視為迴歸問題，(2) 則視之為機率式二元分類。

### 5.1 Discriminative Surrogate Model | 判別式代理模型

> [!quote] Original
> One of the main approaches to surrogate modeling involves learning the conditional probability of the output s given the input h using data Dn, expressed as p(s|h; Dn)—a discriminative approach. An effective surrogate model should produce an accurate mean prediction of the objective function's central tendencies, and well-calibrated uncertainty estimates to balance exploration and exploitation.
>
> **Method.** We serialize the observed optimization trajectory into natural text. For example, with hi as an RF's hyperparameters and si the accuracy, the serialization would read: "max depth is 15, min samples split is 0.5, ..., accuracy is 0.9" [62, 63]. These text representations, for all n observed samples, are concatenated into few-shot examples, symbolized as Dn^nl. Here, we use the superscript nl to mean representations of observations in natural text. Together with the problem description and query example hk^nl, they form the input to the LLM. For each query, the LLM outputs a response: (ŝk, p(ŝk)), denoting the predicted score and associated probability, respectively: (ŝk, p(ŝk)) = LLAMBO(hk^nl, Dn^nl). To obtain probabilistic estimates, this prediction step is repeated K times, from which we compute the empirical mean and standard deviation. This Monte Carlo-based approach is termed LLAMBO (MC), and mirrors the method proposed in [30].
>
> Our empirical observations revealed that the MC implementation often achieved suboptimal calibration of uncertainty estimates. After further explorations, we found the sensitivity to the ordering of in-context examples as a likely cause. As LLMs process inputs in a left-to-right manner, the predictions are sensitive to permutations within the prompt [64, 65]. To enhance robustness, we introduce a shuffling mechanism that randomly permutes the few-shot examples within Dn^nl, which is combined with MC sampling. We acknowledge that while this approach is not grounded in principled probabilistic reasoning—similar to the popular SMAC method [8]—it can be an effective technique to obtain probabilistic estimates. This improved method is hereby referred to as LLAMBO.

> [!note] 翻譯
> 代理模型建構的主要途徑之一，是利用資料 Dn 學習在輸入 h 條件下輸出 s 的條件機率，記為 p(s|h; Dn)——即判別式方法。有效的代理模型應能對目標函數的集中趨勢做出準確的平均值預測，並提供校準良好的不確定性估計，以平衡探索與利用。
>
> **方法。** 我們將觀測到的最佳化軌跡序列化為自然文字。舉例而言，若 hi 為隨機森林（RF）的超參數、si 為準確率，序列化結果為：「max depth is 15, min samples split is 0.5, ..., accuracy is 0.9」[62, 63]。將全部 n 個觀測樣本的文字表徵串接為少樣本範例，記為 Dn^nl，其中上標 nl 表示以自然文字表示的觀測。這些範例連同問題描述與查詢範例 hk^nl 一起構成 LLM 的輸入。對每次查詢，LLM 輸出一組回應 (ŝk, p(ŝk))，分別代表預測分數與對應機率：(ŝk, p(ŝk)) = LLAMBO(hk^nl, Dn^nl)。為取得機率式估計，此預測步驟重複 K 次，據以計算經驗平均數與標準差。此基於蒙地卡羅（Monte Carlo）的做法稱為 LLAMBO (MC)，與 [30] 提出的方法相仿。
>
> 我們的實證觀察發現，MC 實作在不確定性估計的校準上經常未臻理想。進一步探究後，我們發現對情境內範例排序的敏感性可能是主因。由於 LLM 以由左至右的方式處理輸入，預測會對提示內的排列變化敏感 [64, 65]。為增強穩健性，我們引入一種洗牌機制，隨機置換 Dn^nl 中的少樣本範例，並與 MC 採樣結合。我們承認此做法並非建立在嚴謹的機率推理之上——這一點與流行的 SMAC 方法 [8] 類似——但它可以是取得機率式估計的有效技術。此改進後的方法以下即稱為 LLAMBO。

> [!quote] Original
> **Experimental setup.** We compare LLAMBO against GP and SMAC, two established surrogate models. We evaluate these probabilistic discriminative models via prediction performance and uncertainty calibration. For performance metrics, we use NRMSE (↓) and R2 (↑). Calibration is assessed using the scoring rule, log predictive density (LPD) (↓), empirical coverage (where the desired coverage for 1 standard deviation, assuming Gaussianity, is ≈ 0.68), and sharpness (↓) [66]. We also include normalized regret of the point acquired using expected improvement (EI) [10]. Our goal is to assess the surrogate model's efficacy when a different number of evaluations are available (n). We evaluate each task when n ∈ [5, 10, 20, 30], and we test predictions against 20 unseen points.

> [!note] 翻譯
> **實驗設置。** 我們將 LLAMBO 與 GP、SMAC 這兩種成熟的代理模型比較。我們從預測效能與不確定性校準兩方面評估這些機率式判別模型。效能指標採用 NRMSE（↓）與 R2（↑）；校準則以評分規則之對數預測密度（log predictive density, LPD）（↓）、經驗覆蓋率（在高斯假設下，1 個標準差的理想覆蓋率約為 0.68）及銳利度（sharpness）（↓）評估 [66]。我們亦納入以期望改進（expected improvement, EI）[10] 擷取之點的正規化遺憾值。我們的目標是評估在可用評估數 n 不同時代理模型的效能。我們在 n ∈ [5, 10, 20, 30] 時評估各任務，並針對 20 個未見過的點檢驗預測。

> [!quote] Original
> **Empirical insights.** (1) Prediction performance: Figure 3 (Top) plots the NRMSE and R2 against the number of observed samples. LLAMBO consistently outperforms in prediction across all sample counts, particularly with fewer observed samples. Moving on, we examine normalized regret: notably, all methods show increased regret at n = 5, this reflects greater uncertainty across unexplored regions, leading to heightened levels of exploration (and higher regret). For n > 5, LLAMBO attains lower regret, demonstrating better exploitation than other methods. (2) Uncertainty quantification: (Bottom) assesses uncertainty quantification. We find that, in this aspect, GPs, with their probabilistic grounding, produce the best uncertainty estimates, particularly in LPD and empirical coverage. GPs maintain good coverage even with a low number of samples, while LLAMBO only approaches similar performances as n increases. In this regard, our approach exhibits performance more similar to SMAC, a frequentist method that also makes use of empirical variance. Interestingly, we note that the sharpness of uncertainty intervals for GPs remains consistently higher, while in LLAMBO, the sharpness decreases as the coverage improves. This is likely due to the better prediction performance, enabling the predictions to be more confident (lower sharpness) while achieving improved empirical coverage. (3) LLAMBO vs LLAMBO (MC): The purely MC-driven approach exhibits subpar uncertainty calibration, evident through worse LPD and coverage metrics. Coupled with low sharpness values, this suggests the predictions are overly confident, tending to underestimate uncertainty. We also observe that LLAMBO consistently achieves better prediction performance. As such, empirical evidence supports that permuting few-shot examples, while straightforward in implementation, improves both uncertainty quantification and prediction performance, both critical aspects of balancing exploration and exploitation. (4) Role of prior knowledge: Lastly, we investigate the importance of prior knowledge to LLAMBO's few-shot performances. To this end, we introduce an ablation setting LLAMBO (UnInf) where the problem description (containing the <DATA CARD> and <MODEL CARD>) are omitted, and the hyperparameter names are substituted with "Xi". Figure 4 reveals better prediction performance and calibration when compared to the uninformative ablation. This reveals the crucial role of prior knowledge in enhancing surrogate modeling, especially in few-shot settings [25, 63].
>
> *Discriminative surrogate models implemented through ICL can produce effective regression estimates with uncertainty, although there is a tradeoff of stronger prediction performance with worse calibration than probabilistic methods. The LLM's encoded prior is crucial to improving the efficacy of such surrogate models.*

> [!note] 翻譯
> **實證洞見。** (1) 預測效能：圖 3（上）繪出 NRMSE 與 R2 隨觀測樣本數的變化。LLAMBO 在所有樣本數下的預測皆持續領先，於觀測樣本較少時尤為突出。接著檢視正規化遺憾值：值得注意的是，所有方法在 n = 5 時遺憾值皆升高，這反映未探索區域的不確定性較大，導致探索程度提高（遺憾值也隨之上升）。在 n > 5 時，LLAMBO 取得較低的遺憾值，顯示其利用（exploitation）能力優於其他方法。(2) 不確定性量化：圖 3（下）評估不確定性量化。我們發現在此面向上，具備機率理論基礎的 GP 產生最佳的不確定性估計，尤其是在 LPD 與經驗覆蓋率上。GP 即使在樣本數很少時仍維持良好覆蓋率，而 LLAMBO 僅在 n 增加後才逐漸接近類似表現。就此而言，我們的方法表現更近似於 SMAC——一種同樣利用經驗變異數的頻率學派方法。有趣的是，GP 不確定性區間的銳利度始終偏高，而 LLAMBO 的銳利度則隨覆蓋率改善而下降。這很可能歸因於較佳的預測效能，使預測得以更有信心（銳利度較低），同時取得更好的經驗覆蓋率。(3) LLAMBO 與 LLAMBO (MC) 之比較：純粹由 MC 驅動的做法呈現欠佳的不確定性校準，反映在較差的 LPD 與覆蓋率指標上；加上偏低的銳利度值，顯示其預測過度自信，傾向低估不確定性。我們亦觀察到 LLAMBO 的預測效能始終較佳。因此，實證證據支持：置換少樣本範例雖然實作簡單，卻能同時改善不確定性量化與預測效能——二者皆是平衡探索與利用的關鍵。(4) 先驗知識的角色：最後，我們考察先驗知識對 LLAMBO 少樣本表現的重要性。為此，我們引入消融設定 LLAMBO (UnInf)：省略問題描述（含 <DATA CARD> 與 <MODEL CARD>），並將超參數名稱替換為「Xi」。圖 4 顯示，相較於此無資訊消融設定，完整版有更佳的預測效能與校準。這揭示了先驗知識在強化代理模型建構中的關鍵作用，尤其是在少樣本情境下 [25, 63]。
>
> **小結：透過 ICL 實作的判別式代理模型能產生附帶不確定性的有效迴歸估計，惟存在一項權衡——預測效能較強，但校準不如機率式方法。LLM 編碼的先驗對提升此類代理模型的效能至關重要。**

---

## 6 Sampling of Candidate Points | 候選點採樣

> [!quote] Original
> **Motivation.** The sampling of candidate points is another crucial component of BO, as high-potential points can speed up convergence to the optimal solution. In this context, we present a novel mechanism to conditionally generate candidate points based on desired objective values through ICL.
>
> **Method.** Our proposed sampling mechanism draws inspiration from TPE. While TPE focuses on sampling candidate points, denoted as h̃m, from 'good' regions in the search space (i.e. h̃m ∼ l(h) = p(h|s ≤ τ; Dn)), we sample from regions of high potential by directly conditioning on a desired objective value s′: h̃m ∼ p(h|s′; Dn). This distinction is fundamental as it allows us to target specific objective values, something TPE's binary categorization cannot achieve. The few-shot generation capabilities of LLMs are crucial here, as learning such a conditional generator through conventional means poses significant challenges due to the limited number of observations.
>
> We define the desired objective value using the equation: s′ = smin − α × (smax − smin), where smax and smin are the worst and best objective values observed up until that point. Intuitively, s′ is defined relative to the best objective value, with the difference proportional to the observed variability in s. The exact value is controlled by α, the exploration hyperparameter. A positive α sets s′ to improve over smin. Here, we are essentially extrapolating, which cannot be achieved through conventional TPE. Conversely, a negative α (i.e. −1 ≤ α < 0) results in a more conservative target value that is within the observed objective value range. To operationalize this, we implement p(h|s′; Dn) through ICL. We generate M candidate points independently, i.e. h̃k ∼ LLAMBO(s′, Dn^nl), after which, we select the point that maximizes the acquisition function as the point to evaluate next. Thus, our approach, like TPE, uses a sampling-based approximation to optimize the acquisition function.

> [!note] 翻譯
> **動機。** 候選點採樣是 BO 的另一關鍵元件，因為高潛力的點能加速收斂至最佳解。在此脈絡下，我們提出一種透過 ICL、以期望目標值為條件生成候選點的新機制。
>
> **方法。** 我們提出的採樣機制受 TPE 啟發。TPE 著重於自搜尋空間中的「優良」區域採樣候選點 h̃m（即 h̃m ∼ l(h) = p(h|s ≤ τ; Dn)），而我們則直接以期望目標值 s′ 為條件，自高潛力區域採樣：h̃m ∼ p(h|s′; Dn)。此區別是根本性的，因為它讓我們得以鎖定特定的目標值——這是 TPE 的二元分類所無法達成的。LLM 的少樣本生成能力在此至關重要：由於觀測數量有限，以傳統手段學習這樣的條件生成器將面臨重大挑戰。
>
> 我們以下式定義期望目標值：s′ = smin − α × (smax − smin)，其中 smax 與 smin 分別為截至當下觀測到的最差與最佳目標值。直觀而言，s′ 是相對於最佳目標值而定義的，其差距與 s 的觀測變異幅度成正比。確切數值由探索超參數 α 控制。正的 α 使 s′ 優於 smin——此時我們實質上是在外插（extrapolate），這是傳統 TPE 無法達成的。反之，負的 α（即 −1 ≤ α < 0）產生較保守的目標值，落在已觀測目標值範圍之內。為將此付諸實行，我們透過 ICL 實作 p(h|s′; Dn)：獨立生成 M 個候選點，即 h̃k ∼ LLAMBO(s′, Dn^nl)，隨後選擇使擷取函數最大化的點作為下一個評估點。因此，我們的方法與 TPE 一樣，採用基於採樣的近似來最佳化擷取函數。

> [!quote] Original
> **Experimental setup.** We compare our proposed sampler against TPE (Ind), TPE (Multi), and random sampling (Random). As before, we also include ablation of our method LLAMBO (UnInf), which omits problem description and hyperparameter names. Our analysis examines two aspects: candidate point quality and diversity. To evaluate quality, we compute the average regret (↓) and best regret (↓) among the M sampled points [60]. For assessing diversity, we use generalized variance (↑) to evaluate the spread of candidate points and log-likelihood (↑) to assess the probability of candidate points being sampled from observed points. We start by investigating the effect of α on sampling performance. Then, following the experimental procedure outlined previously, we evaluate sampling performance when a different number of observations are available.

> [!note] 翻譯
> **實驗設置。** 我們將所提出的採樣器與 TPE (Ind)、TPE (Multi) 及隨機採樣（Random）比較。與先前相同，我們也納入本方法的消融版本 LLAMBO (UnInf)，其省略了問題描述與超參數名稱。分析涵蓋兩個面向：候選點品質與多樣性。品質方面，我們計算 M 個採樣點的平均遺憾值（↓）與最佳遺憾值（↓）[60]；多樣性方面，我們以廣義變異數（↑）評估候選點的分散程度，並以對數概似（log-likelihood）（↑）評估候選點自觀測點分布中被採樣出的機率。我們先考察 α 對採樣效能的影響，再依循先前概述的實驗流程，評估不同觀測數量下的採樣效能。

> [!quote] Original
> **Empirical insights.** (1) Effect of α: In Figure 5, we observe that as α increases from −0.5 to 0, both average regret and best regret improves. However, as α increases beyond 0, the average regret increases as the candidate points are increasingly sampled from beyond the observed distribution, compromising the reliability of these points. Interestingly, the optimal best regret emerges at α=0.01, hinting at our mechanism's ability to extrapolate from observed distributions. The generalized variance decreases with increasing α, this is reasonable as the candidate points are sampled from smaller regions in the search space. Similarly, the log-likelihood decreases as α increases, as the points are increasingly sampled away from observed points. To confirm that this is indeed the case, we visually examine t-SNE projections of sampled points, localizing them against good (top 20% of samples) and bad points [67]. We note that when α=−0.2, the candidate points cover a similar region as good points, but when α=0.01, the sampled points are observed outside the regions of good points. (2) Quality: Figure 6 compares the quality of our sampled points against baselines, with our method set at α=−0.2. We observe that LLAMBO consistently achieves the lowest average and best regret as n varies, but is especially notable at n = 5. This gain is also present when compared against the ablation, suggesting the crucial role of prior knowledge in proposing high-potential candidates. (3) Diversity: An examination of generalized variance reveals that TPE (Ind) proposes more diverse points. In contrast, the spread of LLAMBO-sampled points is similar to that achieved by TPE (Multi). This is reasonable, as both LLAMBO and TPE (Multi) model correlations, while TPE (Ind) models each dimension independently (and higher correlation decreases generalized variance). Furthermore, the log-likelihood of LLAMBO proposed points are the highest, indicating that they are more plausible given the observed points.
>
> *Sampling candidate points by direct conditioning on desired target value can generate high-quality points, although this can sacrifice diversity among sampled points. The α exploration hyperparameter allows balancing of this trade-off.*

> [!note] 翻譯
> **實證洞見。** (1) α 的影響：在圖 5 中我們觀察到，當 α 自 −0.5 增加至 0 時，平均遺憾值與最佳遺憾值皆改善。然而當 α 增至 0 以上，平均遺憾值反而上升，因為候選點越來越多地自觀測分布之外採樣，損及這些點的可靠性。有趣的是，最佳遺憾值的最優表現出現在 α=0.01，暗示我們的機制具備自觀測分布外插的能力。廣義變異數隨 α 增加而下降，這是合理的，因為候選點是自搜尋空間中更小的區域採樣而得。同樣地，對數概似隨 α 增加而下降，因為採樣點漸漸遠離觀測點。為確認此現象，我們以 t-SNE 投影視覺化檢視採樣點，將其與優良點（前 20% 樣本）及不良點對照定位 [67]。我們注意到當 α=−0.2 時，候選點覆蓋的區域與優良點相近；而當 α=0.01 時，採樣點則落在優良點區域之外。(2) 品質：圖 6 在 α=−0.2 的設定下，比較我們採樣點與各基線的品質。我們觀察到，隨 n 變化，LLAMBO 始終取得最低的平均與最佳遺憾值，在 n = 5 時尤為顯著。與消融版本相比此增益依然存在，顯示先驗知識在提出高潛力候選點上的關鍵作用。(3) 多樣性：檢視廣義變異數可發現 TPE (Ind) 提出的點更為多樣；相對地，LLAMBO 採樣點的分散程度與 TPE (Multi) 相近。這是合理的，因為 LLAMBO 與 TPE (Multi) 皆對相關性建模，而 TPE (Ind) 對每個維度獨立建模（相關性越高，廣義變異數越低）。此外，LLAMBO 提出之點的對數概似最高，顯示在給定觀測點下它們更為合理。
>
> **小結：直接以期望目標值為條件採樣候選點能生成高品質的點，但可能犧牲採樣點間的多樣性；探索超參數 α 可用於平衡此權衡。**

---

## 7 End-to-End Demonstration of LLAMBO | LLAMBO 的端到端驗證

> [!quote] Original
> **Motivation.** Having examined the integration of LLMs into key components of BO, we now holistically evaluate the performance of LLAMBO as an end-to-end BO algorithm. Here, we instantiate LLAMBO with our discriminative surrogate model, as this is the most classic form of surrogate modeling.
>
> **Experimental setup.** We evaluate BO performance on 25 tasks extracted from Bayesmark [31], a continuous HPT benchmark. Here, a task is a dataset-ML model pair, and we consider all 5 included datasets and 5 ML models. Additionally, we introduce 3 proprietary and 2 synthetic datasets into the benchmark—these are datasets for which the LLM would not have seen during pretraining, and thus serve to check for any memorization concerns. This results in a total of 50 HPT tasks, where for each task, we executed 5 seeded searches, each with 25 trials. **Baselines.** We compare LLAMBO against 4 established baselines commonly used in production: GP-DKL [34], SKOpt (GP) [68], Optuna (TPE) [41], and SMAC3 (RF) [8]. To ensure a fair comparison, we do not use warmstarting and initialize all methods with the same set of 5 randomly sampled points in each run. We describe complete experimental details in Appendix D.
>
> **Empirical insights.** (1) Performance: Figure 7 shows the average regrets across all HPT tasks on both public Bayesmark datasets, and private and synthetic datasets. We note that in both settings, LLAMBO achieves the best tuning performance. Additionally, we observe that, consistent with prior findings, LLAMBO excels in earlier stages of the search, when fewer observations are available. (2) Additional results: In the interest of space, we include additional results in Appendix E. Specifically, we ▶ evaluate LLAMBO with our generative surrogate model; ▶ compare against additional baselines on Bayesmark; ▶ evaluate BO performance on 24 additional tasks from HPOBench [32]; ▶ and report individual task search results (by task metric, average regret, and average rank).
>
> *LLAMBO performs effectively as an end-to-end pipeline, exhibiting sample-efficient search. Its modularity further enables individual components to be integrated into existing frameworks.*

> [!note] 翻譯
> **動機。** 在檢視了 LLM 與 BO 關鍵元件的整合後，我們現在整體評估 LLAMBO 作為端到端 BO 演算法的效能。此處我們以判別式代理模型實例化 LLAMBO，因為這是代理模型建構最經典的形式。
>
> **實驗設置。** 我們在取自 Bayesmark [31]（一項連續型 HPT 基準）的 25 項任務上評估 BO 效能。此處一項任務即為一組「資料集—ML 模型」配對，我們考慮基準內含的全部 5 個資料集與 5 種 ML 模型。此外，我們在基準中引入 3 個專有與 2 個合成資料集——這些是 LLM 在預訓練期間不可能見過的資料集，用以檢驗是否存在記憶（memorization）疑慮。合計共 50 項 HPT 任務；每項任務執行 5 次不同種子的搜尋，每次 25 次試驗。**基線。** 我們將 LLAMBO 與 4 個生產環境常用的成熟基線比較：GP-DKL [34]、SKOpt (GP) [68]、Optuna (TPE) [41] 與 SMAC3 (RF) [8]。為確保比較公平，我們不使用暖啟動，且每次執行中所有方法皆以同一組 5 個隨機採樣點初始化。完整實驗細節見附錄 D。
>
> **實證洞見。** (1) 效能：圖 7 顯示所有 HPT 任務在公開 Bayesmark 資料集，以及私有與合成資料集上的平均遺憾值。我們注意到在兩種情境下，LLAMBO 皆取得最佳的調校表現。此外，與先前發現一致，LLAMBO 在搜尋早期、觀測較少時表現尤佳。(2) 其他結果：限於篇幅，其餘結果收錄於附錄 E，具體包括：▶ 以生成式代理模型評估 LLAMBO；▶ 在 Bayesmark 上與更多基線比較；▶ 在 HPOBench [32] 的 24 項額外任務上評估 BO 效能；▶ 回報個別任務的搜尋結果（依任務指標、平均遺憾值與平均排名）。
>
> **小結：LLAMBO 作為端到端管線運作良好，展現高樣本效率的搜尋能力；其模組化設計更使個別元件得以整合進既有框架。**

---

## 8 Discussions | 討論

> [!quote] Original
> In summary, we introduced LLAMBO, a novel framework that integrated LLM capabilities to enhance model-based BO. Our approach introduced three specific enhancements: ▶ zero-shot warmstarting to initialize search, generative and discriminative ▶ surrogate models of the objective function via ICL, and a ▶ candidate point sampler that can conditionally generate for specific target values. Our investigative study on the problem of HPT uncovered performance improvements across all three integrations, which was especially notable when fewer samples were available. Additionally, we found that LLAMBO to be an effective stand-alone BO method, exemplified through superior performance on diverse benchmarks.
>
> **Limitations & future works.** While LLAMBO does not perform any finetuning, performing inference through LLMs incurs a much larger computational footprint than traditional BO algorithms. Our findings indicated that LLAMBO trades off this computational complexity for improved sample efficiency, an especially desirable property in black-box optimization tasks. This suggests the potential fusion of LLAMBO with more computationally efficient methods. For instance, deploying LLAMBO in earlier stages of the search, or only leveraging an individual component to complement existing BO frameworks. Additionally, while we have demonstrated the potential for integrating LLM in BO with GPT-3.5, it is important to recognize the choice of LLMs can significantly influence optimization results. A promising future direction involves benchmarking various LLMs, to understand their strengths and limitations in different BO problem settings. Our study has primarily focused on HPT tasks, which are relatively low-dimensional. However, a notable area for future research is the expansion LLAMBO's application to higher-dimensional BO tasks with more complex search spaces, such as neural architecture search and robotic control [39, 69].

> [!note] 翻譯
> 總結而言，我們提出了 LLAMBO——一個整合 LLM 能力以強化模型式 BO 的新框架。我們的方法引入三項具體強化：▶ 用於初始化搜尋的零樣本暖啟動；▶ 透過 ICL 建構之目標函數的生成式與判別式代理模型；以及 ▶ 能以特定目標值為條件進行生成的候選點採樣器。我們在 HPT 問題上的研究揭示，三項整合均帶來效能提升，且在樣本較少時尤為顯著。此外，我們發現 LLAMBO 亦是有效的獨立 BO 方法，其在多樣化基準上的卓越表現即為例證。
>
> **限制與未來工作。** 雖然 LLAMBO 不進行任何微調，但透過 LLM 執行推論所需的計算量遠大於傳統 BO 演算法。我們的研究結果顯示，LLAMBO 是以此計算複雜度換取更高的樣本效率——這在黑盒最佳化任務中是特別可貴的性質。這也暗示了 LLAMBO 與計算上更高效方法融合的潛力：例如僅在搜尋的早期階段部署 LLAMBO，或僅取用單一元件來補強既有 BO 框架。此外，儘管我們以 GPT-3.5 展示了在 BO 中整合 LLM 的潛力，必須認識到 LLM 的選擇可能顯著影響最佳化結果。一個有前景的未來方向是對各種 LLM 進行基準測試，以理解它們在不同 BO 問題情境下的優勢與限制。本研究主要聚焦於相對低維的 HPT 任務；然而，將 LLAMBO 的應用擴展至搜尋空間更複雜的高維 BO 任務——如神經架構搜尋（neural architecture search）與機器人控制 [39, 69]——是值得關注的未來研究領域。

---

## Ethics and Reproducibility Statements | 倫理與可重現性聲明

> [!quote] Original
> **Ethics.** In this work, we evaluate both public benchmarks and private datasets. The private datasets are de-identified and used following the guidance of the respective data providers. We follow recommendations to use the Azure OpenAI service when using GPT models, where via the agreement we ensure the medical data is not sent for human review or stored, hence respecting the guidelines given by the dataset providers.
>
> **Reproducibility.** Experimental investigations are described in Sections 3-6 with further details of the method, experimental setup, and datasets included in Appendix D. We provide the code to reproduce our results at https://github.com/tennisonliu/LLAMBO and the wider lab repository https://github.com/vanderschaarlab/LLAMBO.

> [!note] 翻譯
> **倫理。** 本研究同時評估公開基準與私有資料集。私有資料集均已去識別化，並遵循各資料提供者的指引使用。我們依循建議，在使用 GPT 模型時採用 Azure OpenAI 服務，透過協議確保醫療資料不會被送交人工審閱或儲存，從而遵守資料集提供者所訂的準則。
>
> **可重現性。** 實驗研究於第 3–6 節描述，方法、實驗設置與資料集的進一步細節收錄於附錄 D。重現結果的程式碼公開於 https://github.com/tennisonliu/LLAMBO 及實驗室整體儲存庫 https://github.com/vanderschaarlab/LLAMBO。

---

## References | 參考文獻

> [!note] 翻譯
> References omitted / 參考文獻略。

---

## Appendices | 附錄（附錄僅節譯）

### A Bayesian Optimization Background | 貝葉斯最佳化背景

> [!quote] Original
> Consider an objective function: f : H → S. BO constructs a surrogate model to approximate f and iteratively proposes potential points. In more detail, the core components are: (1) Surrogate model: BO methods typically construct a surrogate approximation of f using available samples, denoted p(s|h; Dn). Commonly used models include the Gaussian Process (GP) [7], and random forests (SMAC) [8]. An alternative approach is the Tree Parzen Estimator (TPE) [6] which uses two hierarchical processes l(x) and g(x) to model input distributions when the objective function is above or below a specified quantile τ. (2) Candidate point sampler: The sampler proposes a set of candidate points to query next. For GPs, the candidate points are typically randomly sampled but then further optimized directly using the acquisition function. In SMAC, candidate points are sampled using a combination of random search and local search in the good regions found by the random forest. For TPE, the candidate points are sampled directly from the density of "good" points, g(h). (3) Acquisition function: The acquisition function a : H → R scores and selects the candidate points using the surrogate model. One of the most popular acquisition functions is expected improvement (EI). The BO process thus operates by first updating the surrogate model with existing data, and then sampling a set of promising candidate points. Using the surrogate model, the acquisition function scores each candidate point and selects the best point for evaluation. The new point and observed value are appended to available observations, and the cycle continues.

> [!note] 翻譯
> 考慮目標函數 f : H → S。BO 建構代理模型逼近 f 並迭代地提出潛在的點。更具體地，其核心元件為：(1) 代理模型：BO 方法通常利用可得樣本建構 f 的代理近似，記為 p(s|h; Dn)；常用模型包括高斯過程（GP）[7] 與隨機森林（SMAC）[8]。另一種途徑是樹狀 Parzen 估計器（TPE）[6]，它以兩個階層式過程 l(x) 與 g(x)，分別對目標函數高於或低於指定分位數 τ 時的輸入分布建模。(2) 候選點採樣器：採樣器提出下一步查詢的候選點集合。對 GP 而言，候選點通常先隨機採樣，再直接以擷取函數進一步最佳化；SMAC 則結合隨機搜尋與在隨機森林找到之優良區域內的局部搜尋來採樣候選點；TPE 則直接自「優良」點的密度 g(h) 採樣候選點。(3) 擷取函數：擷取函數 a : H → R 利用代理模型為候選點評分並加以挑選，最流行的擷取函數之一是期望改進（EI）。整體而言，BO 流程先以既有資料更新代理模型，再採樣一組有潛力的候選點；擷取函數藉由代理模型為每個候選點評分並選出最佳點進行評估；新的點與其觀測值被加入既有觀測，循環由此持續。

### B Generative Surrogate Model | 生成式代理模型

> [!quote] Original
> **Method.** An alternative approach to surrogate modelling is to learn the generative process of inputs given the output, represented as p(h|s; Dn). This approach is exemplified in TPE-based methods [6], which constructs two hierarchical processes: l(h) is the generative model of good points (score s ≤ τ, the top γ quantile), and g(h) is the model for bad points. TPE evaluates each point using the acquisition function a(h) ∝ l(h)/g(h). **Density ratio estimation.** To address the difficulty of learning the two densities directly with an LLM, we employ Bayes' rule, transforming the density ratio estimation into probabilistic binary classification [71]. The EI expression can be equivalently rewritten as γ^{-1} p(s ≤ τ|h), i.e. the probability of h belonging to the good points. Using this reformulation, we can estimate the score a(h) through ICL, by obtaining the probabilistic classification p(s ≤ τ|h). We recategorize the observed samples such that zi = 1(si ≤ τ), transform the observed samples to text, obtain K predictions from the LLM, and compute the empirical average to estimate a(h).
>
> **Empirical insights.** (1) Scoring performance: LLAMBO achieves notably higher correlations between predicted scores and ground truth, especially at n=5, where the TPE variants generated scores that are only weakly correlated with the ground truth performance. (2) Regret: While all methods show higher regret at low sample sizes, LLAMBO quickly identifies good regions in the search space, leading to lower regret as n increases. *Generative surrogate modeling via ICL predicts scores that are more highly correlated with ground-truth scores, leading to better identification of high-potential points.*

> [!note] 翻譯
> **方法。** 代理模型建構的另一途徑是學習在給定輸出下輸入的生成過程，記為 p(h|s; Dn)。此途徑以 TPE 系列方法 [6] 為代表：其建構兩個階層式過程——l(h) 為優良點（分數 s ≤ τ，即前 γ 分位數）的生成模型，g(h) 為不良點的模型；TPE 以擷取函數 a(h) ∝ l(h)/g(h) 評估每個點。**密度比估計。** 直接以 LLM 學習這兩個密度並不容易；為此，我們運用貝氏定理，將密度比估計轉化為機率式二元分類 [71]。EI 的表達式可等價地改寫為 γ^{-1} p(s ≤ τ|h)，即 h 屬於優良點的機率。藉由此重構，我們可透過 ICL 取得機率式分類 p(s ≤ τ|h)，據以估計分數 a(h)。我們將觀測樣本重新分類為 zi = 1(si ≤ τ)，將其轉換為文字，自 LLM 取得 K 次預測，並計算經驗平均以估計 a(h)。
>
> **實證洞見。** (1) 評分效能：LLAMBO 的預測分數與真值之間的相關性顯著較高，尤其在 n=5 時，TPE 各變體產生的分數與真值效能僅呈弱相關。(2) 遺憾值：雖然所有方法在樣本數少時遺憾值都較高，LLAMBO 能快速鎖定搜尋空間中的優良區域，使遺憾值隨 n 增加而降低。**小結：透過 ICL 的生成式代理模型所預測的分數與真值分數相關性更高，從而更能辨識高潛力的點。**

### C Prompt Designs | 提示設計

> [!quote] Original
> This section supplies the complete prompts used in LLAMBO (Appendix C.1) and performs an ablation study (Appendix C.2). Each prompt is constructed with four key components: <Model Card> describing the ML model being optimized; <Data Card> providing information about the dataset; Instructions (task-specific guidelines on the format and requirements of the response); and Observations of the current optimization trajectory. The ablation study compares: LLAMBO (standard), LLAMBO [No context] (omitting dataset metadata), and LLAMBO [No instructions] (excluding non-formatting-related instructions). Findings: the standard LLAMBO configuration outperforms other variants, underscoring the significance of each prompt component. Notably, LLAMBO [No context] demonstrated similar optimization behavior without any meta-data about the underlying task—signifying the model's effectiveness beyond mere reliance on data memorization or leakage. LLAMBO [No instructions] recorded an acceptance rate of proposed points of 69.26% ± 0.79%, significantly lower than LLAMBO (91.60% ± 0.45%) and LLAMBO [No context] (88.8% ± 0.39%), underscoring the importance of detailed task instructions in enhancing the quality and efficiency of the candidate sampling process.

> [!note] 翻譯
> 本節提供 LLAMBO 使用的完整提示（附錄 C.1），並進行消融研究（附錄 C.2）。每個提示由四個關鍵部分構成：描述被最佳化 ML 模型的 <Model Card>；提供資料集資訊的 <Data Card>；指令（關於回覆格式與要求的任務特定準則）；以及當前最佳化軌跡的觀測。消融研究比較：LLAMBO（標準版）、LLAMBO [No context]（省略資料集元資料）與 LLAMBO [No instructions]（排除與格式無關的額外指令）。結果顯示：標準 LLAMBO 配置優於其他變體，凸顯每一提示元件的重要性。值得注意的是，LLAMBO [No context] 在完全沒有任務元資料的情況下仍展現相近的最佳化行為——這表明模型的效能並非僅仰賴資料記憶或洩漏。LLAMBO [No instructions] 的候選點接受率為 69.26% ± 0.79%，顯著低於 LLAMBO（91.60% ± 0.45%）與 LLAMBO [No context]（88.8% ± 0.39%），凸顯詳盡任務指令對提升候選點採樣品質與效率的重要性。（附錄 C 之完整提示原文（圖 9–18）為提示模板，此處僅節譯。）

### D Detail of Experimental Procedures | 實驗流程細節

> [!quote] Original
> To evaluate the performance of LLAMBO on HPT, we considered 25 built-in tasks from Bayesmark (5 public datasets × 5 ML models: RandomForest, SVM, DecisionTree, MLP, AdaBoost) and 24 built-in tasks from HPOBench (8 OpenML datasets × 3 ML models: XGBoost, RandomForest, MLP). Additionally, we included three synthetic (Rosenbrock, Griewank, KTablet) and three private datasets (SEER, MAGGIC, CUTRACT) into Bayesmark, resulting in additional 30 tasks. All tasks were executed using five different seeds for 25 trials, with all models sharing the same initialization for each seed. For our instantiation of LLAMBO, we sample M = 20 candidate points, set the exploration hyperparameter to α = −0.1, and sample K = 10 MC predictions for the surrogate model. We used gpt-3.5-turbo, version 0301 with default hyperparameters temperature = 0.7 and top_p = 0.95. Baselines include SKOpt (GP-based), GP (Deep Kernel Learning), DNGO, SMAC3, Turbo, HEBO, Optuna, an optimized TPE variant, and STO.

> [!note] 翻譯
> 為評估 LLAMBO 於 HPT 上的效能，我們考慮 Bayesmark 的 25 項內建任務（5 個公開資料集 × 5 種 ML 模型：RandomForest、SVM、DecisionTree、MLP、AdaBoost），以及 HPOBench 的 24 項內建任務（8 個 OpenML 資料集 × 3 種 ML 模型：XGBoost、RandomForest、MLP）。此外，我們在 Bayesmark 中加入三個合成資料集（Rosenbrock、Griewank、KTablet）與三個私有資料集（SEER、MAGGIC、CUTRACT），新增 30 項任務。所有任務均以五個不同隨機種子執行 25 次試驗，且每個種子下所有模型共享相同的初始化。在 LLAMBO 的實例化中，我們採樣 M = 20 個候選點，將探索超參數設為 α = −0.1，並為代理模型採樣 K = 10 次 MC 預測。實驗使用 gpt-3.5-turbo（0301 版），採用預設超參數 temperature = 0.7 與 top_p = 0.95。基線包括 SKOpt（GP 式）、GP（深度核學習）、DNGO、SMAC3、Turbo、HEBO、Optuna、一個高度最佳化的 TPE 變體，以及 STO。（各模型之超參數搜尋空間細節見原文附錄 D.1–D.3，此處僅節譯。）

### E Additional Results | 額外結果

> [!quote] Original
> **E.1 Additional warmstarting results.** Increasing the informativeness of the prompts led to improved search performance for both GP and TPE under different initialization; correlation matrices show higher correlations between points recommended by an LLM. **E.2 LLAMBO with generative surrogate model.** In a constrained single-run evaluation over all 25 public Bayesmark tasks, the discriminative surrogate model outperformed the generative counterpart, which was nonetheless competitive with baselines; the generative version might exhibit sensitivity to the τ hyperparameter, potentially tied to the majority label bias in ICL [64]. **E.3 Additional results on Bayesmark.** Against a wider array of baselines (optimized TPE, DNGO, STO, Turbo, HEBO), LLAMBO consistently demonstrates the best overall performance, excelling in tuning DecisionTree and RandomForest but doing less well on SVMs—a sensitivity common to all BO techniques. **E.4 Additional results on HPOBench.** Across 24 tasks, LLAMBO achieves the best tuning performance, excelling in earlier stages of the search. **E.5 Clock time.** LLAMBO incurs a higher average clock time per iteration (predominantly influenced by internet connectivity and API latency); however, in black-box optimization, querying the black-box function is typically the primary computational expense, making sample efficiency arguably more significant than per-iteration time.

> [!note] 翻譯
> **E.1 額外暖啟動結果。** 提高提示的資訊量能改善 GP 與 TPE 在不同初始化下的搜尋效能；相關矩陣顯示 LLM 推薦之點間的相關性較高。**E.2 採用生成式代理模型的 LLAMBO。** 在涵蓋全部 25 項公開 Bayesmark 任務的受限單次執行評估中，判別式代理模型優於生成式版本，但後者相較各基線仍具競爭力；生成式版本可能對 τ 超參數敏感，或與 ICL 中的多數標籤偏差（majority label bias）[64] 有關。**E.3 Bayesmark 額外結果。** 面對更廣泛的基線（最佳化 TPE、DNGO、STO、Turbo、HEBO），LLAMBO 始終展現最佳整體表現，在調校 DecisionTree 與 RandomForest 上尤為出色，但在 SVM 上表現較弱——此類敏感性為所有 BO 技術所共有。**E.4 HPOBench 額外結果。** 在 24 項任務上，LLAMBO 取得最佳調校表現，且在搜尋早期階段尤為突出。**E.5 執行時間。** LLAMBO 每次迭代的平均時鐘時間較高（主要受網路連線與 API 延遲影響）；然而在黑盒最佳化中，查詢黑盒函數通常才是主要計算開銷，因此樣本效率可謂比每次迭代的耗時更為重要。（附錄僅節譯。）
