---
citation_key: "LiEtAl2021b"
title: "VolcanoML: Speeding up End-to-End AutoML via Scalable Search Space Decomposition"
authors: "Yang Li; Yu Shen; Wentao Zhang; Jiawei Jiang; Bolin Ding; Yaliang Li; Jingren Zhou; Zhi Yang; Wentao Wu; Ce Zhang; Bin Cui"
year: 2021
doi: "10.14778/3476249.3476270"
source: "arXiv (2107.08861)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "2107.08861"
conversion: "pdftotext -layout (automated)"
language: "bilingual-en-zh"
---

# VolcanoML: Speeding up End-to-End AutoML via Scalable Search Space Decomposition | VolcanoML：以可擴展的搜尋空間分解加速端到端 AutoML

> [!abstract] 重點摘要
> - 提出 VolcanoML——一個可擴展、可延伸的端到端自動化機器學習（AutoML）框架，核心思想是將由特徵工程、演算法／模型選擇與超參數調校所構成的巨大聯合搜尋空間，系統性地分解為多個較小的子空間。
> - 引入三種基本建構區塊（building blocks）：聯合區塊（joint block，以貝葉斯最佳化直接搜尋）、條件區塊（conditioning block，依類別變數取值切分並以多臂搖臂機制淘汰劣勢分支）與交替區塊（alternating block，將空間一分為二並依期望效用改進量交替最佳化）。
> - 借鑑關聯式資料庫的 Volcano 查詢執行模型：執行計畫是一棵由建構區塊組成的樹，透過遞迴呼叫 do_next! 由根節點傳遞至葉節點逐步迭代，如同關聯式運算子構成的查詢計畫。
> - 在 auto-sklearn 相同的搜尋空間下，VolcanoML 於 30 項分類與 20 項迴歸任務上顯著優於 auto-sklearn 與 TPOT；搜尋空間越大（20 → 29 → 100 個超參數），優勢越明顯。
> - 框架具高度可延伸性：可加入新的特徵工程算子（如 smote_balancer）與嵌入選擇（embedding selection）階段，使其能高效處理影像等 auto-sklearn 與 TPOT 難以支援的輸入型態，並在多個 Kaggle 資料集上與四大商用 AutoML 平台相比至少不落下風。
> - 此結構化分解觀點開啟了「自動計畫生成」（如同查詢最佳化器）等未來研究方向。

---

## Abstract | 摘要

> [!quote] Original
> End-to-end AutoML has attracted intensive interests from both academia and industry, which automatically searches for ML pipelines in a space induced by feature engineering, algorithm/model selection, and hyper-parameter tuning. Existing AutoML systems, however, suffer from scalability issues when applying to application domains with large, high-dimensional search spaces. We present VolcanoML, a scalable and extensible framework that facilitates systematic exploration of large AutoML search spaces. VolcanoML introduces and implements basic building blocks that decompose a large search space into smaller ones, and allows users to utilize these building blocks to compose an execution plan for the AutoML problem at hand. VolcanoML further supports a Volcano-style execution model – akin to the one supported by modern database systems – to execute the plan constructed. Our evaluation demonstrates that, not only does VolcanoML raise the level of expressiveness for search space decomposition in AutoML, it also leads to actual findings of decomposition strategies that are significantly more efficient than the ones employed by state-of-the-art AutoML systems such as auto-sklearn.

> [!note] 翻譯
> 端到端自動化機器學習（AutoML）已引起學術界與產業界的高度關注，其在由特徵工程（feature engineering）、演算法／模型選擇與超參數調校（hyper-parameter tuning）共同構成的空間中自動搜尋 ML 管線。然而，既有 AutoML 系統在應用於具有龐大、高維搜尋空間的領域時，面臨可擴展性（scalability）問題。我們提出 VolcanoML——一個可擴展、可延伸的框架，能促進對大型 AutoML 搜尋空間的系統性探索。VolcanoML 引入並實作了一組基本建構區塊（building blocks），可將大型搜尋空間分解為較小的子空間，並允許使用者利用這些區塊為手邊的 AutoML 問題組合出一份執行計畫（execution plan）。VolcanoML 進一步支援 Volcano 式執行模型——與現代資料庫系統所支援者相仿——以執行所建構的計畫。我們的評估顯示，VolcanoML 不僅提升了 AutoML 搜尋空間分解的表達力，也實際發現了比 auto-sklearn 等最先進 AutoML 系統所採用者顯著更高效的分解策略。

---

## 1 Introduction | 引言

> [!quote] Original
> In recent years, researchers in the database community have been working on raising the level of abstractions of machine learning (ML) and integrating such functionality into today's data management systems, e.g., SystemML [18], SystemDS [5], Snorkel [59], ZeroER [77], TFX [6, 51], "Query 2.0" [78], Krypton [54], Cerebro [55], ModelDB [72], MLFlow [80], DeepDive [81], HoloClean [60], ActiveClean [40], and NorthStar [39]. End-to-end AutoML systems [26, 79, 82] have been an emerging type of systems that has significantly raised the level of abstractions of building ML applications. Given an input dataset and a user-defined utility metric (e.g., validation accuracy), these systems automate the search of an end-to-end ML pipeline, including feature engineering, algorithm/model selection, and hyper-parameter tuning. Open-source examples include auto-sklearn [15], TPOT [57], and hyperopt-sklearn [38], whereas most cloud service providers, e.g., Google, Microsoft, Amazon, Alibaba, etc., all provide their proprietary services on the cloud. As machine learning has become an increasingly indispensable functionality integrated in modern data (management) systems, an efficient and effective end-to-end AutoML component also becomes increasingly important.
>
> End-to-end AutoML provides a powerful abstraction to automatically navigate and search in a given complex search space. However, in our experience of applying state-of-the-art end-to-end AutoML systems in a range of real-world applications, we find that such a system running "fully automatically" is rarely enough — often, developing a successful ML application involves multiple iterations between a user and an AutoML system to iteratively improve the resulting ML artifact.

> [!note] 翻譯
> 近年來，資料庫社群的研究者致力於提升機器學習（ML）的抽象層級，並將此類功能整合進當今的資料管理系統，例如 SystemML [18]、SystemDS [5]、Snorkel [59]、ZeroER [77]、TFX [6, 51]、「Query 2.0」[78]、Krypton [54]、Cerebro [55]、ModelDB [72]、MLFlow [80]、DeepDive [81]、HoloClean [60]、ActiveClean [40] 與 NorthStar [39]。端到端 AutoML 系統 [26, 79, 82] 作為一種新興系統類型，大幅提升了建構 ML 應用的抽象層級。在給定輸入資料集與使用者定義的效用指標（如驗證準確率）後，這些系統自動化搜尋端到端 ML 管線，涵蓋特徵工程、演算法／模型選擇與超參數調校。開源代表包括 auto-sklearn [15]、TPOT [57] 與 hyperopt-sklearn [38]；而大多數雲端服務供應商——如 Google、Microsoft、Amazon、Alibaba 等——皆在雲端提供其專有服務。隨著機器學習日益成為現代資料（管理）系統中不可或缺的功能，一個高效且有效的端到端 AutoML 元件也愈發重要。
>
> 端到端 AutoML 提供了在給定的複雜搜尋空間中自動導航與搜尋的強大抽象。然而，依據我們在一系列真實應用中使用最先進端到端 AutoML 系統的經驗，「完全自動」運行的系統鮮少足夠——開發成功的 ML 應用往往需要使用者與 AutoML 系統之間多次往返互動，以迭代式地改進最終產出的 ML 成品。

> [!quote] Original
> **Motivating Practical Challenge.** One such type of interaction, which inspires this work, is the enrichment of search space. We observe that the default search space provided by state-of-the-art AutoML systems is often not enough in many applications. This was not obvious to us at all in the beginning and it is not until we finish building a range of real-world applications that we realize this via a set of concrete examples. For example, in one of our astronomy applications [62], the feature normalization function is domain-specific and not supported by most, if not all, AutoML systems. Similar examples can also be found when searching for suitable ML models via AutoML. In one of our meteorology applications, we need to extend the models with meteorology-specific loss functions. We saw similar problems when we tried to extend existing AutoML systems with pre-trained feature embeddings coming from TensorFlow Hub, to include newly arXiv'ed models to enrich the "Model Base" [44], or to support Cosine annealing as for tuning.
>
> **Technical Challenge: Scalability over the Search Space.** "Why is it hard to extend the search space, as a user, in an end-to-end AutoML system?" The answer to this question is a complex one that is not completely technical: some aspects are less technical such as engineering decisions and UX designs, however, there are also more technically fundamental aspects. An end-to-end AutoML system contains an optimization algorithm that navigates a joint search space induced by feature engineering, algorithm selection, and hyper-parameter tuning. Because of this joint nature, the search space of end-to-end AutoML is complex and huge while the enrichment is only going to make it even larger. As we will see, handling such a huge space is already challenging for existing systems, and further enriching it will make it even harder to scale.
>
> Many existing systems such as auto-sklearn [15] and TPOT [57] deal with the entire composite search space jointly, which naturally leads to the scalability bottleneck. Decomposing a joint space has been explored for some subspaces (e.g., only algorithm and hyper-parameters as in [45, 50]), however, none of them has been applied to a search space as large as that of end-to-end AutoML. One challenge is that there exist many different ways to decompose the same space, as shown above, but only some of them can perform well. Without a structured, high-level abstraction for search space decomposition to explore different strategies, it is very hard to scale up an end-to-end AutoML system to accommodate the search space that will only get larger in the future.

> [!note] 翻譯
> **實務挑戰的動機。** 啟發本研究的其中一類互動，是搜尋空間的擴充（enrichment）。我們觀察到，最先進 AutoML 系統所提供的預設搜尋空間，在許多應用中往往並不足夠。這一點起初對我們而言絲毫不明顯，直到我們完成一系列真實應用的建構，才透過一組具體案例意識到此事。例如，在我們的一項天文學應用 [62] 中，特徵正規化函數是領域特定的，多數（甚至所有）AutoML 系統皆不支援。在透過 AutoML 搜尋合適 ML 模型時亦可見類似案例：在我們的一項氣象學應用中，我們需要以氣象學特定的損失函數擴充模型。當我們嘗試以來自 TensorFlow Hub 的預訓練特徵嵌入（feature embeddings）擴充既有 AutoML 系統、將新近發表於 arXiv 的模型納入「Model Base」[44]、或在調校中支援餘弦退火（cosine annealing）時，也遇到了相似的問題。
>
> **技術挑戰：搜尋空間上的可擴展性。** 「作為使用者，為何在端到端 AutoML 系統中擴充搜尋空間如此困難？」此問題的答案頗為複雜，且並非全然技術性的：某些層面較不技術性，如工程決策與使用者體驗設計；然而也存在技術上更根本的層面。端到端 AutoML 系統包含一個最佳化演算法，在由特徵工程、演算法選擇與超參數調校共同誘導出的聯合搜尋空間中導航。正因這種聯合特性，端到端 AutoML 的搜尋空間既複雜又龐大，而擴充只會使其更加膨脹。如後文所示，處理如此巨大的空間對既有系統已屬艱難，進一步擴充更將使其難以擴展。
>
> 許多既有系統，如 auto-sklearn [15] 與 TPOT [57]，以聯合方式處理整個複合搜尋空間，這自然導致可擴展性瓶頸。分解聯合空間的做法已在某些子空間上被探索（例如 [45, 50] 僅處理演算法與超參數），然而尚無任何方法被應用於如端到端 AutoML 這般龐大的搜尋空間。其中一項挑戰在於：同一空間存在多種不同的分解方式（如上所示），但其中只有部分能有良好表現。若缺乏一個結構化、高層級的搜尋空間分解抽象來探索不同策略，就很難將端到端 AutoML 系統擴展至未來只會愈加龐大的搜尋空間。

> [!quote] Original
> **Summary of Technical Contributions.** In this paper, we focus on designing a system, VolcanoML, which is scalable to a large search space. Our technical contributions are as follows.
>
> *C1. System Design: A Structured View on Decomposition.* The main technical contribution of VolcanoML is to provide a flexible and principled way of decomposing a large search space into multiple smaller ones. We propose a novel system abstraction: a set of VolcanoML building blocks (Section 3), each of which takes charge of a smaller sub-search space whereas a VolcanoML execution plan (Section 4) consists of a tree of such building blocks — the root node corresponds to the original search space and its child nodes correspond to different subspaces. Under this abstraction, optimizing in the joint space is conducted as optimization problems over different smaller subspaces. The execution model is similar to the classic "Volcano" query evaluation model in a relational database [17] (thus the name VolcanoML): The system asks the root node to take one iteration in the optimization process, which recursively invokes one of its child nodes to take one iteration on solving a smaller-scale optimization problem over its own subspace; this recursive invocation procedure will continue until a leaf node is reached. This flexible abstraction allows us to explore different ways that the same joint space can be decomposed. Together with a range of additional optimizations (Section 4), VolcanoML can often support more scalable search process than the existing AutoML systems such as auto-sklearn and TPOT.
>
> *C2. Large-scale Empirical Evaluations.* We conducted intensive empirical evaluations, comparing VolcanoML with state-of-the-art systems including auto-sklearn and TPOT. We show that (1) under the same search space as auto-sklearn, VolcanoML significantly outperforms auto-sklearn and TPOT — over 30 classification tasks and 20 regression tasks — VolcanoML outperforms the best of auto-sklearn and TPOT on a majority of tasks; (2) using an enriched search space with additional feature engineering operators, VolcanoML performs significantly better than auto-sklearn; and (3) using an enriched search space with an additional data processing stage and functionalities beyond what auto-sklearn and TPOT currently support (i.e., an additional embedding selection stage using pre-trained models on TensorFlow Hub), VolcanoML can deal with input types such as images efficiently.
>
> **Moving Forward.** The VolcanoML abstraction enables a structured view of optimizing a black-box function via decomposition. This structured view itself opens up interesting future directions. For example, one may wish to automatically decompose a search space given a workload, just like what a classic query optimizer would do for relational queries. For constrained optimizations, we also imagine techniques similar to traditional "push-down selection" could be applied in a similar spirit. We explore the possibility of automatically searching for the best plan in Section 4 and discuss the limitations of this simple strategy and the exciting line of future work that could follow. While the full treatment of these aspects are beyond the scope of this paper, we hope the VolcanoML abstraction can serve as a foundation for these future endeavors.

> [!note] 翻譯
> **技術貢獻摘要。** 本文聚焦於設計一個能擴展至大型搜尋空間的系統——VolcanoML。我們的技術貢獻如下。
>
> *C1. 系統設計：分解的結構化觀點。* VolcanoML 的主要技術貢獻是提供一種靈活且有原則的方式，將大型搜尋空間分解為多個較小的子空間。我們提出一種新穎的系統抽象：一組 VolcanoML 建構區塊（第 3 節），每個區塊各自負責一個較小的子搜尋空間；而一份 VolcanoML 執行計畫（第 4 節）則由這些建構區塊組成的一棵樹構成——根節點對應原始搜尋空間，其子節點對應不同的子空間。在此抽象下，於聯合空間中的最佳化被轉化為對不同較小子空間的最佳化問題。其執行模型類似關聯式資料庫中經典的「Volcano」查詢求值模型 [17]（VolcanoML 因此得名）：系統要求根節點在最佳化過程中執行一次迭代，根節點遞迴地呼叫其某個子節點，令其在自身子空間上對較小規模的最佳化問題執行一次迭代；此遞迴呼叫程序持續進行，直至抵達葉節點。這一靈活抽象使我們得以探索同一聯合空間的多種分解方式。連同一系列額外最佳化（第 4 節），VolcanoML 通常能支援比 auto-sklearn 與 TPOT 等既有 AutoML 系統更具可擴展性的搜尋過程。
>
> *C2. 大規模實證評估。* 我們進行了密集的實證評估，將 VolcanoML 與包括 auto-sklearn 與 TPOT 在內的最先進系統比較。結果顯示：(1) 在與 auto-sklearn 相同的搜尋空間下，VolcanoML 顯著優於 auto-sklearn 與 TPOT——在 30 項分類任務與 20 項迴歸任務上，VolcanoML 在多數任務中勝過 auto-sklearn 與 TPOT 二者中的較佳者；(2) 使用加入額外特徵工程算子的擴充搜尋空間時，VolcanoML 的表現顯著優於 auto-sklearn；(3) 使用包含額外資料處理階段、且功能超出 auto-sklearn 與 TPOT 現行支援範圍的擴充搜尋空間（即利用 TensorFlow Hub 預訓練模型的額外嵌入選擇階段）時，VolcanoML 能高效處理影像等輸入型態。
>
> **展望。** VolcanoML 抽象提供了「透過分解來最佳化黑盒函數」的結構化觀點。此觀點本身即開啟了有趣的未來方向。例如，人們或許希望在給定工作負載下自動分解搜尋空間，正如經典查詢最佳化器對關聯式查詢所做的那樣。對於帶約束的最佳化，我們也設想類似傳統「選擇下推」（push-down selection）的技術可以相同精神加以應用。我們在第 4 節探索自動搜尋最佳計畫的可能性，並討論此簡單策略的限制及隨之而來的振奮人心的未來工作。雖然對這些面向的完整處理超出本文範圍，我們希望 VolcanoML 抽象能作為這些未來探索的基礎。

---

## 2 Related Work | 相關研究

> [!quote] Original
> AutoML is a topic that has been intensively studied over the last decade. We briefly summarize related work in this section and readers can consult latest surveys [22, 26, 79, 82] for more details.
>
> **End-to-End AutoML.** End-to-end AutoML, the focus of this work, aims to automate the development process of the end-to-end ML pipeline, including feature preprocessing, feature engineering, algorithm selection, and hyper-parameter tuning. Often, this is modeled as a black-box optimization problem [27] and solved jointly [15, 57, 68]. Apart from grid search and random search [3], genetic programming [52, 57] and Bayesian optimization (BO) [4, 13, 25, 64, 65] has become prevailing frameworks for this problem. One challenge of end-to-end AutoML is the staggeringly huge search space that one has to support and many of these methods suffer from scalability issues. In addition, meta-learning [70] systematically investigates the interactions that different ML approaches perform on a wide range of learning tasks, and then learns from this experience, to accomplish new tasks much faster. Several meta-learning approaches [9, 15, 24, 69] can guide ML practitioners to design better search spaces for AutoML tasks.
>
> Many end-to-end AutoML systems have raised the abstraction level of ML. auto-weka [68], hyperopt-sklearn [38] and auto-sklearn [15] are the main representatives of BO-based AutoML systems. auto-sklearn is one of the most popular open-source framework. TPOT [57] and ML-Plan [52] use genetic algorithm and hierarchical task networks planning respectively to optimize over the pipeline space, and require discretization of the hyper-parameter space. AlphaD3M [11] integrates reinforcement learning with Monte Carlo tree search (MCTS) to solve AutoML problems but without imposing efficient decomposition over hyper-parameters and algorithm selection. AutoStacker [8] focuses on ensembling and cascading to generate complex pipelines, and solves the CASH problem [15] via random search. Furthermore, a growing number of commercial enterprises also export their AutoML services to their users, e.g., H2O [41], Microsoft's Azure Machine Learning [2], Google's Prediction API [20], Amazon Machine Learning [49] and IBM's Watson Studio AutoAI [28].

> [!note] 翻譯
> AutoML 是過去十年被深入研究的主題。本節簡要總結相關研究，讀者可參閱最新綜述 [22, 26, 79, 82] 以獲取更多細節。
>
> **端到端 AutoML。** 端到端 AutoML 是本研究的焦點，旨在自動化端到端 ML 管線的開發流程，涵蓋特徵前處理、特徵工程、演算法選擇與超參數調校。此問題通常被建模為黑盒最佳化問題 [27] 並以聯合方式求解 [15, 57, 68]。除網格搜尋與隨機搜尋 [3] 之外，遺傳規劃（genetic programming）[52, 57] 與貝葉斯最佳化（Bayesian optimization, BO）[4, 13, 25, 64, 65] 已成為此問題的主流框架。端到端 AutoML 的一大挑戰在於必須支援極其龐大的搜尋空間，而上述許多方法皆存在可擴展性問題。此外，元學習（meta-learning）[70] 系統性地考察不同 ML 方法在廣泛學習任務上的表現互動，並從此經驗中學習，以更快完成新任務。若干元學習方法 [9, 15, 24, 69] 可指引 ML 從業者為 AutoML 任務設計更佳的搜尋空間。
>
> 許多端到端 AutoML 系統已提升了 ML 的抽象層級。auto-weka [68]、hyperopt-sklearn [38] 與 auto-sklearn [15] 是 BO 式 AutoML 系統的主要代表，其中 auto-sklearn 是最流行的開源框架之一。TPOT [57] 與 ML-Plan [52] 分別採用遺傳演算法與階層任務網路規劃在管線空間上進行最佳化，且需要對超參數空間離散化。AlphaD3M [11] 整合強化學習與蒙地卡羅樹搜尋（MCTS）求解 AutoML 問題，但未對超參數與演算法選擇施加高效的分解。AutoStacker [8] 聚焦於集成（ensembling）與級聯（cascading）以生成複雜管線，並以隨機搜尋求解 CASH 問題 [15]。此外，越來越多商業企業也向使用者輸出其 AutoML 服務，如 H2O [41]、Microsoft 的 Azure Machine Learning [2]、Google 的 Prediction API [20]、Amazon Machine Learning [49] 與 IBM 的 Watson Studio AutoAI [28]。

> [!quote] Original
> **Automating Individual Components.** Apart from end-to-end AutoML, many efforts have been devoted to studying sub-problems in AutoML: (1) feature engineering [33–36, 56], (2) algorithm selection [12, 15, 38, 45, 50, 68], and (3) hyper-parameter tuning [4, 14, 23, 25, 29, 31, 37, 43, 47, 58, 63, 65, 66, 76]. Meta-learning methods [16, 19, 74] for hyper-parameter tuning can leverage auxiliary knowledge acquired from previous tasks to achieve faster optimization. Several systems offer a subset of functionalities in the end-to-end process. Microsoft's NNI [61] helps users to automate feature engineering, hyper-parameter tuning, and model compression. Recent work [50] leverages the ADMM optimization framework to decompose the CASH problem [15], and solves two easier sub-problems. Berkeley's Ray project [53] provides the tune module [48] to support scalable hyper-parameter tuning tasks in a distributed environment. Featuretools [32] is a Python library for automatic feature engineering. Unlike these works, in this paper, we focus on deriving an end-to-end solution to the AutoML problem, where the sub-problems are solved in a joint manner.

> [!note] 翻譯
> **個別元件的自動化。** 除端到端 AutoML 外，許多研究致力於 AutoML 的子問題：(1) 特徵工程 [33–36, 56]；(2) 演算法選擇 [12, 15, 38, 45, 50, 68]；(3) 超參數調校 [4, 14, 23, 25, 29, 31, 37, 43, 47, 58, 63, 65, 66, 76]。用於超參數調校的元學習方法 [16, 19, 74] 可利用自先前任務習得的輔助知識，達成更快的最佳化。若干系統提供端到端流程中的部分功能：Microsoft 的 NNI [61] 協助使用者自動化特徵工程、超參數調校與模型壓縮；近期研究 [50] 利用 ADMM 最佳化框架分解 CASH 問題 [15]，改為求解兩個較易的子問題；Berkeley 的 Ray 專案 [53] 提供 tune 模組 [48]，支援分散式環境下可擴展的超參數調校任務；Featuretools [32] 是用於自動特徵工程的 Python 函式庫。與這些工作不同，本文聚焦於推導 AutoML 問題的端到端解決方案，其中各子問題以聯合方式求解。

---

## 3 VolcanoML and Building Blocks | VolcanoML 與建構區塊

> [!quote] Original
> The goal of VolcanoML is to enable scalability with respect to the underlying AutoML search space. As a result, its design focuses on the decomposition of a given search space. In this section, we first introduce key building blocks in VolcanoML, and in Section 4 we describe how multiple building blocks are put together to compose a VolcanoML execution plan in a modular way. Later in Section 5, we introduce additional optimizations for these building blocks.

> [!note] 翻譯
> VolcanoML 的目標是實現對底層 AutoML 搜尋空間的可擴展性，因此其設計聚焦於給定搜尋空間的分解。本節先介紹 VolcanoML 的關鍵建構區塊；第 4 節描述如何以模組化方式將多個建構區塊組合成 VolcanoML 執行計畫；隨後第 5 節介紹針對這些區塊的額外最佳化。

### 3.1 Search Space of End-to-End AutoML | 端到端 AutoML 的搜尋空間

> [!quote] Original
> We describe the search space of end-to-end AutoML following auto-sklearn. The input to the system is a dataset D, containing a set of training samples. The user also provides a pre-defined metric, e.g., validation accuracy or cross-validation accuracy, to measure the utility of a given ML pipeline. The output of an end-to-end AutoML system is an ML pipeline that achieves good utility. To find such an ML pipeline, the system searches over a large search space of possible pipelines and picks one that maximizes the pre-defined utility. This search space is a composition of (1) feature engineering operators, (2) ML algorithms/models, and (3) hyper-parameters.
>
> **Feature Engineering.** The feature engineering process takes as input a dataset D and outputs a new dataset D′. It achieves this by transforming the input dataset via a set of data transformations. In auto-sklearn, it further defines multiple stages of the feature engineering process: (1) preprocessing, (2) rescaling, (3) balancing, and (4) feature_transforming. For each stage, the system chooses a single transformation to apply. For example, for feature_transforming, the system can choose among no_processing, kernel_pca, polynomial, select_percentile, etc.
>
> **ML Algorithms.** Given a transformed dataset D′, the system then picks an ML algorithm to train. Since different ML algorithms are suitable for different types of tasks, the system needs to consider a diverse range of possible ML algorithms. Taking auto-sklearn as an example, the search space for ML algorithms contains Linear_Model, Support_Vector_Machine, Discriminant_Analysis, Random_Forest, etc.
>
> **Hyper-parameters.** Each ML algorithm has its own sub-search space for hyper-parameter tuning — if we choose to use a certain ML algorithm, we also have to specify the corresponding hyper-parameters. The hyper-parameters fall into three categories: continuous (e.g., sub-sample_rate for Random_Forest), discrete (e.g., maximal_depth for Decision_Tree), and categorical (e.g., kernel_type for Lib_SVM).
>
> If the system makes a concrete pick for each of the above decisions, then it can compose a concrete ML pipeline and evaluate its utility. This is often an expensive process since it involves training an ML model. To find the optimal ML pipeline, the system evaluates the utility of different ML pipelines in an iterative manner following a search strategy, and picks the one that maximizes the utility. For example, auto-sklearn handles the above search space jointly and optimizes it with Bayesian optimization (BO) [64]. Given an initial set of function evaluations, BO proceeds by fitting a surrogate model to those observations, specifically a probabilistic Random Forest in auto-sklearn, and then chooses which ML pipeline to evaluate from the search space by optimizing an acquisition function that balances exploration and exploitation.

> [!note] 翻譯
> 我們依循 auto-sklearn 描述端到端 AutoML 的搜尋空間。系統輸入為包含一組訓練樣本的資料集 D，使用者並提供預先定義的指標（如驗證準確率或交叉驗證準確率）以衡量給定 ML 管線的效用。端到端 AutoML 系統的輸出是一條達到良好效用的 ML 管線。為找到這樣的管線，系統在龐大的候選管線搜尋空間中搜尋，並挑選使預定效用最大化者。此搜尋空間由 (1) 特徵工程算子、(2) ML 演算法／模型、(3) 超參數三者組合而成。
>
> **特徵工程。** 特徵工程流程以資料集 D 為輸入，輸出新資料集 D′，其做法是透過一組資料轉換來變換輸入資料集。在 auto-sklearn 中，特徵工程流程進一步劃分為多個階段：(1) 前處理（preprocessing）、(2) 重縮放（rescaling）、(3) 平衡（balancing）、(4) 特徵轉換（feature_transforming）。系統在每一階段選擇單一轉換施用。例如在 feature_transforming 階段，系統可自 no_processing、kernel_pca、polynomial、select_percentile 等選項中擇一。
>
> **ML 演算法。** 給定轉換後的資料集 D′，系統接著挑選 ML 演算法進行訓練。由於不同 ML 演算法適用於不同類型的任務，系統需考量多樣的候選演算法。以 auto-sklearn 為例，其 ML 演算法搜尋空間包含 Linear_Model、Support_Vector_Machine、Discriminant_Analysis、Random_Forest 等。
>
> **超參數。** 每個 ML 演算法各有其超參數調校的子搜尋空間——若選定某演算法，也必須指定對應的超參數。超參數分三類：連續型（如 Random_Forest 的 sub-sample_rate）、離散型（如 Decision_Tree 的 maximal_depth）與類別型（如 Lib_SVM 的 kernel_type）。
>
> 若系統對上述各項決策皆做出具體選擇，即可組成一條具體的 ML 管線並評估其效用。這通常是昂貴的過程，因為涉及訓練 ML 模型。為找出最佳 ML 管線，系統依循某種搜尋策略以迭代方式評估不同管線的效用，並挑選效用最大者。例如，auto-sklearn 以聯合方式處理上述搜尋空間，並以貝葉斯最佳化（BO）[64] 進行最佳化：給定一組初始函數評估後，BO 對這些觀測擬合代理模型（auto-sklearn 中具體為機率式隨機森林），再透過最佳化一個平衡探索與利用的擷取函數（acquisition function），自搜尋空間中選擇下一條要評估的 ML 管線。

### 3.2 Building Blocks | 建構區塊

> [!quote] Original
> Unlike auto-sklearn, VolcanoML decomposes the above search space into smaller subspaces. One interesting design decision in VolcanoML is to introduce a structured abstraction to express different decomposition strategies. A decomposition strategy is akin to an execution plan in relational database management systems, which is composed of building blocks akin to relational operators. A building block itself can be viewed as an atomic decomposition strategy.
>
> **Goal.** The goal of VolcanoML is to solve:
>
> min_{x1,...,xn} f(x1, ..., xn; D),
>
> where x1, ..., xn is a set of n variables and each of them has domain D_{xi} for i ∈ [n]. Together, these n variables define a search space (x1, ..., xn) ∈ ∏_i D_{xi}. D corresponds to the input dataset, which is a set of input samples. In our setting, f(·) is a black-box function that we can only evaluate (but not exploiting the derivative). Given constant c in the composite domain c ∈ ∏_i D_{xi}, we use the notation f({(x1, ...xn) = c}; D) as the value of evaluating f by substituting (x1, ...xn) with c.
>
> **Subgoal.** One key decision of VolcanoML is to solve the optimization problem on a search space by decomposing it into multiple smaller subspaces, each of which will be solved by one building block. We define optimizing over each of these smaller subspaces as a subgoal of the original problem. Formally, a subgoal g is defined by two components: x̄g ⊆ {x1, ...xn} as a subset of variables, and c̄g ∈ ∏_{xi∈x̄g} D_{xi} as an assignment in the domain of all variables in x̄g. Let x̄−g = {x1, ..., xn} − x̄g be all variables that are not in x̄g. Each subgoal defines a function fg over a smaller search space, which is constructed by substituting all variables in x̄g with c̄g:
>
> fg = f[x̄g/c̄g] : z ∈ ∏_{xi∈x̄−g} D_{xi} ↦ f({x̄g = c̄g; x̄−g = z}; D).

> [!note] 翻譯
> 與 auto-sklearn 不同，VolcanoML 將上述搜尋空間分解為較小的子空間。VolcanoML 的一項有趣設計決策是引入結構化抽象來表達不同的分解策略。一項分解策略類似關聯式資料庫管理系統中的執行計畫，由類似關聯式運算子的建構區塊組成；建構區塊本身可視為一種原子分解策略。
>
> **目標。** VolcanoML 的目標是求解：
>
> min_{x1,...,xn} f(x1, ..., xn; D)，
>
> 其中 x1, ..., xn 為 n 個變數，各變數的定義域為 D_{xi}（i ∈ [n]）。這 n 個變數共同定義搜尋空間 (x1, ..., xn) ∈ ∏_i D_{xi}。D 對應輸入資料集，即一組輸入樣本。在我們的設定中，f(·) 是黑盒函數，只能進行評估（無法利用導數）。給定複合定義域中的常數 c ∈ ∏_i D_{xi}，我們以記號 f({(x1, ...xn) = c}; D) 表示以 c 代入 (x1, ...xn) 評估 f 所得之值。
>
> **子目標。** VolcanoML 的一項關鍵決策是：透過將搜尋空間分解為多個較小的子空間來求解最佳化問題，每個子空間由一個建構區塊負責。我們將對每個較小子空間的最佳化定義為原問題的一個子目標（subgoal）。形式上，子目標 g 由兩部分定義：x̄g ⊆ {x1, ...xn} 為變數子集，c̄g ∈ ∏_{xi∈x̄g} D_{xi} 為 x̄g 中所有變數定義域上的一組賦值。令 x̄−g = {x1, ..., xn} − x̄g 為不在 x̄g 中的所有變數。每個子目標在較小的搜尋空間上定義一個函數 fg，其由將 x̄g 中所有變數以 c̄g 代入而得：
>
> fg = f[x̄g/c̄g] : z ∈ ∏_{xi∈x̄−g} D_{xi} ↦ f({x̄g = c̄g; x̄−g = z}; D)。

> [!quote] Original
> **Building Block.** Each subgoal g corresponds to one building block B_{g,D}, whose goal is to solve
>
> min_{x̄−g} fg(x̄−g; D).
>
> A building block B_{g,D} imposes several assumptions on g and D. First, given an assignment c̄−g to x̄−g, it is able to evaluate the value of the function fg(c̄−g, D). Note that such an evaluation can often be expensive and VolcanoML tries to minimize the number of times that such a function is evaluated. Second, given a dataset D, a building block has the knowledge about how to subsample a smaller dataset D̃ ⊆ D and then conduct evaluations on such a subset x ↦ fg(x; D̃). Third, we assume that the building block has access to a cost model about the cost of an evaluation at x, C_{g,D,x}.
>
> **Interfaces.** All implementations of a building block follow an interactive optimization process. A building block exposes several interfaces. First, one can initialize a building block via B_{g,D} ← init(f, x̄g, c̄g, D), which creates a building block. Second, one can query the current best solution found in B_{g,D} by x̂ ← get_current_best(B_{g,D}). Furthermore, one can ask B_{g,D} to iterate once via do_next!(B_{g,D}), where '!' indicates potential change on the state of the input B_{g,D}. Last but not least, one can query a building block about its expected utility (EU) if given K more budget units (e.g., seconds) via [l, u] ← get_eu(B_{g,D}, K). By adopting a similar design principle used in the existing AutoML systems [15, 50, 57], in VolcanoML we estimate EU by extrapolation into the "future" with more available budget. Given the inherent uncertainty in our estimation method, rather than returning a single point estimate, we instead return a lower bound l and an upper bound u. We refer readers to [45] for the details of how the lower and upper bounds are established. Moreover, one can query a building block about its expected utility improvement (EUI) via δ ← get_eui(B_{g,D}). Note that, different from EU, EUI is the expected improvement over the current observed utility if given K more budget units. In VolcanoML, we estimate EUI by taking the mean of the observed improvements from history, following Levine et al [42].

> [!note] 翻譯
> **建構區塊。** 每個子目標 g 對應一個建構區塊 B_{g,D}，其目標是求解
>
> min_{x̄−g} fg(x̄−g; D)。
>
> 建構區塊 B_{g,D} 對 g 與 D 施加若干假設。第一，給定對 x̄−g 的賦值 c̄−g，它能評估函數值 fg(c̄−g, D)；注意此類評估往往代價高昂，VolcanoML 致力於將此函數的評估次數降至最低。第二，給定資料集 D，建構區塊知道如何子採樣出較小的資料集 D̃ ⊆ D，並在此子集上進行評估 x ↦ fg(x; D̃)。第三，我們假設建構區塊可存取一個成本模型，得知在 x 處進行一次評估的成本 C_{g,D,x}。
>
> **介面。** 建構區塊的所有實作皆遵循互動式最佳化流程。建構區塊對外提供數個介面。第一，可透過 B_{g,D} ← init(f, x̄g, c̄g, D) 初始化並建立一個建構區塊。第二，可透過 x̂ ← get_current_best(B_{g,D}) 查詢 B_{g,D} 目前找到的最佳解。再者，可透過 do_next!(B_{g,D}) 要求 B_{g,D} 迭代一次，其中「!」表示輸入 B_{g,D} 的狀態可能改變。最後（同樣重要），可透過 [l, u] ← get_eu(B_{g,D}, K) 查詢建構區塊在再獲得 K 個預算單位（如秒）時的期望效用（expected utility, EU）。採用與既有 AutoML 系統 [15, 50, 57] 類似的設計原則，VolcanoML 以「向未來外插更多可用預算」的方式估計 EU。鑑於估計方法固有的不確定性，我們不回傳單一點估計，而是回傳下界 l 與上界 u；上下界的建立細節請參閱 [45]。此外，可透過 δ ← get_eui(B_{g,D}) 查詢建構區塊的期望效用改進量（expected utility improvement, EUI）。注意與 EU 不同，EUI 是在再獲得 K 個預算單位時，相對於目前已觀測效用的期望改進量。在 VolcanoML 中，我們依循 Levine 等人 [42] 的做法，以歷史觀測改進量的平均值估計 EUI。

### 3.3 Three Types of Building Blocks | 三種建構區塊

> [!quote] Original
> Decomposition is the cornerstone of VolcanoML's design. Given a search space, apart from exploring it jointly, there are two classical ways of decomposition — to partition the search space via conditioning on different values of a certain variable (in a similar spirit of variable elimination [10]), or to decompose the problem into multiple smaller ones by introducing equality constraints (in a similar spirit of dual decomposition [7]). This inspires VolcanoML's design, which supports three types of building blocks: (1) joint block that simply optimizes the input subspace using Bayesian optimization; (2) conditioning block that further divides the input subspace into smaller ones by conditioning on one particular input variable; and (3) alternating block that partitions the input subspace into two and optimizes each one alternately. Note that both conditioning block and alternating block would generate new building blocks with smaller subgoals. We next present the implementation details for each type of building block.

> [!note] 翻譯
> 分解是 VolcanoML 設計的基石。給定一個搜尋空間，除了聯合探索外，還有兩種經典的分解方式——藉由對某個變數的不同取值進行條件化來切分搜尋空間（精神類似變數消去（variable elimination）[10]），或是引入等式約束將問題分解為多個較小的問題（精神類似對偶分解（dual decomposition）[7]）。這啟發了 VolcanoML 的設計，其支援三種建構區塊：(1) 聯合區塊（joint block）——直接以貝葉斯最佳化來最佳化輸入子空間；(2) 條件區塊（conditioning block）——以某個特定輸入變數為條件，將輸入子空間進一步切分為更小的子空間；(3) 交替區塊（alternating block）——將輸入子空間一分為二，並交替最佳化各半。注意條件區塊與交替區塊皆會生成帶有更小子目標的新建構區塊。以下介紹各類建構區塊的實作細節。

> [!quote] Original
> **3.3.1 Joint Block.** A joint block directly optimizes its subgoal via Bayesian optimization (BO) [64]. Specifically, BO based method SMAC [25] has been used by many applications where evaluating the objective function is computationally expensive. It constructs a probabilistic surrogate model M to capture the relationship between the input variables x̄ and the objective function value ψ, and refines M iteratively using past observations (x̄, ψ)s. The implementation of do_next! for a joint block consists of the following three steps: (1) Use the surrogate model M to select x̄ that maximizes an acquisition function. In our implementation, we use expected improvement (EI) [30] as the acquisition function, which has been widely used in BO community. (2) Evaluate the selected x̄ and obtain its value about the objective function (i.e., the subgoal) ψ = fg(x̄) + ε with ε ∼ N(0, σ²), where N is the normal distribution. (3) Refit the surrogate model M on the observed (x̄, ψ)s.
>
> **Early-Stopping based Optimization.** For large datasets, early-stopping based methods, e.g., Successive Halving [29], Hyperband [43], BOHB [14], MFES-HB [47], etc, can terminate the evaluations of poorly-performed configurations in advance, thus speeding up the evaluations. VolcanoML supports MFES-HB [47], which combines the benefits of Hyperband and Multi-fidelity BO [67, 75], to optimize a joint block, in addition to vanilla BO.

> [!note] 翻譯
> **3.3.1 聯合區塊。** 聯合區塊直接以貝葉斯最佳化（BO）[64] 來最佳化其子目標。具體而言，基於 BO 的方法 SMAC [25] 已被許多目標函數評估計算成本高昂的應用所採用。它建構一個機率式代理模型 M 以刻畫輸入變數 x̄ 與目標函數值 ψ 之間的關係，並利用過往觀測 (x̄, ψ) 迭代地精煉 M。聯合區塊的 do_next! 實作包含以下三步：(1) 利用代理模型 M 選出使擷取函數最大化的 x̄；在我們的實作中採用 BO 社群廣泛使用的期望改進（expected improvement, EI）[30] 作為擷取函數。(2) 評估選定的 x̄，取得其目標函數（即子目標）之值 ψ = fg(x̄) + ε，其中 ε ∼ N(0, σ²)，N 為常態分布。(3) 以已觀測的 (x̄, ψ) 重新擬合代理模型 M。
>
> **基於提前停止的最佳化。** 對於大型資料集，基於提前停止（early-stopping）的方法——如逐次減半（Successive Halving）[29]、Hyperband [43]、BOHB [14]、MFES-HB [47] 等——能提前終止表現不佳組態的評估，從而加速整體評估。除原味 BO 外，VolcanoML 亦支援結合 Hyperband 與多保真度 BO（multi-fidelity BO）[67, 75] 之優點的 MFES-HB [47] 來最佳化聯合區塊。

> [!quote] Original
> **3.3.2 Conditioning Block.** A conditioning block decomposes its input x̄ into x̄ = {xc} ∪ ȳ, where xc is a single variable with domain D_{xc}. It then creates one new building block for each possible value d ∈ D_{xc} of xc: min_ȳ gd(ȳ; D) ≡ f({xc = d, ȳ}; D). As a result, |D_{xc}| new (child) building blocks are created.
>
> The conditioning block aims to identify optimal value for xc, and many previous AutoML researches have used Bandit algorithms for this purpose [29, 46, 47, 50]. In VolcanoML, we follow these previous work and model it as a multi-armed bandit (MAB) problem, while our framework is flexible enough to incorporate other algorithms when they are available. There are |D_{xc}| arms, where each arm corresponds to a child block. Playing an arm means invoking the do_next! primitive of the corresponding child block.
>
> Algorithm 1 illustrates the implementation of do_next! for a conditioning block. It starts by playing each arm L times in a Round-Robin fashion (lines 2 to 4). Here, L is a user-specified configuration parameter of VolcanoML. In our current implementation, we set L = 5. We then obtain the lower and upper bounds of the expected utility of each child block by invoking its get_eu primitive (lines 5 to 6), and eliminate child blocks that are dominated by others (line 7). The elimination works as follows. Consider two blocks Bi and Bj: if the upper bound ui of Bi is less than the lower bound lj of Bj, then the block Bi is eliminated. An eliminated arm/block will not be played in future invocations of do_next!.
>
> *Remark:* We have simplified the above elimination criterion by using the lower and upper bounds calculated given K budget units for each arm. In fact, these K budget units are shared by all the arms, and as a result, each arm actually has fewer budget units than K. Our assumption is that, K is sufficiently large so that one can play all arms until (the observed distribution of rewards of) each arm converges. Otherwise, the lower and upper bounds obtained may be over-optimistic, and as a result, may lead to incorrect eliminations. Fortunately, our assumption usually holds in practice, where arms converge relatively fast.

> [!note] 翻譯
> **3.3.2 條件區塊。** 條件區塊將其輸入 x̄ 分解為 x̄ = {xc} ∪ ȳ，其中 xc 為單一變數、定義域為 D_{xc}。接著為 xc 的每個可能取值 d ∈ D_{xc} 建立一個新的建構區塊：min_ȳ gd(ȳ; D) ≡ f({xc = d, ȳ}; D)。因此共建立 |D_{xc}| 個新的（子）建構區塊。
>
> 條件區塊旨在找出 xc 的最佳取值，許多先前的 AutoML 研究已為此目的使用搖臂（bandit）演算法 [29, 46, 47, 50]。在 VolcanoML 中，我們依循這些先前工作，將其建模為多臂搖臂（multi-armed bandit, MAB）問題，且我們的框架具備足夠彈性，可在其他演算法可用時加以納入。此處共有 |D_{xc}| 支臂，每支臂對應一個子區塊；拉動一支臂即呼叫對應子區塊的 do_next! 原語。
>
> 演算法 1 展示條件區塊 do_next! 的實作。它首先以輪詢（Round-Robin）方式將每支臂各拉動 L 次（第 2–4 行）。此處 L 為 VolcanoML 的使用者指定組態參數，目前實作中設 L = 5。接著透過呼叫各子區塊的 get_eu 原語，取得其期望效用的上下界（第 5–6 行），並淘汰被其他區塊支配（dominated）的子區塊（第 7 行）。淘汰機制如下：考慮兩個區塊 Bi 與 Bj，若 Bi 的上界 ui 小於 Bj 的下界 lj，則 Bi 被淘汰。被淘汰的臂／區塊在後續的 do_next! 呼叫中不再被拉動。
>
> *附註：* 上述淘汰準則經過簡化——我們使用的是「每支臂各獲 K 個預算單位」下計算的上下界。實際上這 K 個預算單位由所有臂共享，因此每支臂實際可用的預算少於 K。我們的假設是 K 足夠大，使所有臂都能被拉動至各臂（觀測到的報酬分布）收斂為止；否則所得上下界可能過度樂觀，進而導致錯誤的淘汰。所幸此假設在實務中通常成立，各臂收斂相對較快。

> [!quote] Original
> **3.3.3 Alternating Block.** An alternating block decomposes its input search space into x̄ = ȳ ∪ z̄, and explores ȳ and z̄ in an alternating way. Similarly, we also model the optimization in alternating block as an MAB problem. Algorithm 2 illustrates how its init primitive works. It first creates two child blocks B1 and B2, which will focus on optimizing for ȳ and z̄ respectively (lines 1 to 3). It then (again) views B1 and B2 as two arms and plays them using Round-Robin (lines 4 to 10). Note that, when B1 optimizes ȳ (resp. when B2 optimizes z̄), it uses the current best z̄ found by B2 (resp. the current best ȳ found by B1). This is done by the set_var primitive (invoked at line 7 for B2 and line 10 for B1).
>
> One problem of our alternating MAB formulation is that the utility improvements of the two building blocks often vary dramatically in practice. For example, some applications are very sensitive to the features being used (e.g., normalized vs. non-normalized features) while hyper-parameter tuning will offer little or even no improvement. In this case, we should spend more resources on looking for good features instead of tuning hyper-parameters. Our key observation is that, the expected utility improvement (EUI) decays as optimization proceeds. As a result, we propose to use EUI as an indicator that measures the potential of pulling an arm further. Algorithm 3 illustrates the details of this idea when used to implement the do_next! primitive. Specifically, Algorithm 3 starts by polling the EUI of both child blocks (lines 1 and 2). Recall that the EUI is estimated by taking the mean of historic observations. It then compares the EUIs and picks the arm/block with larger EUI to play next (lines 3 to 10). Before pulling the winner arm, again it will use the current best settings found by the other arm/block (lines 4 to 6, lines 8 to 10).

> [!note] 翻譯
> **3.3.3 交替區塊。** 交替區塊將其輸入搜尋空間分解為 x̄ = ȳ ∪ z̄，並以交替方式探索 ȳ 與 z̄。同樣地，我們也把交替區塊中的最佳化建模為 MAB 問題。演算法 2 展示其 init 原語的運作：首先建立兩個子區塊 B1 與 B2，分別聚焦於最佳化 ȳ 與 z̄（第 1–3 行）；接著（再度）將 B1 與 B2 視為兩支臂，以輪詢方式拉動（第 4–10 行）。注意當 B1 最佳化 ȳ 時（相應地，當 B2 最佳化 z̄ 時），它會使用 B2 目前找到的最佳 z̄（相應地，B1 目前找到的最佳 ȳ）；此事由 set_var 原語完成（第 7 行針對 B2、第 10 行針對 B1 呼叫）。
>
> 交替式 MAB 形式化的一個問題是：實務上兩個建構區塊的效用改進幅度往往差異劇烈。例如，某些應用對所使用的特徵非常敏感（如正規化與未正規化特徵之別），而超參數調校則幾乎或完全帶不來改進。在此情況下，我們應把更多資源投注於尋找好的特徵，而非調校超參數。我們的關鍵觀察是：期望效用改進量（EUI）會隨最佳化的推進而衰減。因此，我們提出以 EUI 作為衡量「繼續拉動某支臂之潛力」的指標。演算法 3 展示此想法用於實作 do_next! 原語的細節：首先輪詢兩個子區塊的 EUI（第 1、2 行）——回顧 EUI 是以歷史觀測的平均值估計的——接著比較 EUI，挑選 EUI 較大的臂／區塊作為下一個拉動對象（第 3–10 行）。在拉動勝出的臂之前，同樣會先套用另一臂／區塊目前找到的最佳設定（第 4–6 行、第 8–10 行）。

> [!quote] Original
> **3.3.4 Discussion: Pros and Cons of Building Blocks.** While the joint block is the most straightforward way to solve the optimization problem associated, it is difficult to scale Bayesian optimization to a large search space [45, 73]. The alternating block addresses this scalability issue by decomposing the search space into two smaller subspaces, though with the assumption that the improvements of the two subspaces are conditionally independent of each other. As a result, the alternating block is a better choice when such an assumption approximately holds. The conditioning block is capable of pruning the search space as optimization proceeds, when bad arms are pulled less often or will not be played anymore, with the limitation that it can only work for conditional variables that are categorical. For non-categorical variables, one possible way to use conditioning blocks is to split the value range of variables. For example, given a numerical variable that ranges from 1 to 3, we split it into two ranges, which are [1, 2) and [2, 3). During the optimization iteration, we first choose one sub-range and then optimize the splitted space along with its corresponding subspace.
>
> In addition, VolcanoML uses bandit-based algorithms from the existing literature [42, 45] as default in both the alternating and conditioning block, and other bandit-based algorithms, such as successive halving [29], Hyperband [43], BOHB [14] and MFES-HB [47], can also be used in these blocks.
>
> **3.3.5 Discussion: Comparing Different Building Blocks.** Joint blocks are the default blocks that can be applied to all problems. When the search space is rather large, conditioning and alternating blocks can be helpful. If the search space contains a categorical hyper-parameter, under which the subspace of each choice is conditionally independent with each other, the conditioning block can be used instead of exploring the entire space. If the search space can be decomposed into two approximately independent subspaces, the alternating block can be applied to this case. As a result, a scalable system needs to be able to decompose the problem in different ways and pick the most suitable building blocks. This forms a VolcanoML execution plan, which we will describe in the next section. In Section 4, we explore the possibility of automatically choosing building blocks to use by maximizing the empirical accuracy of different execution plans, given a pre-defined set of datasets.

> [!note] 翻譯
> **3.3.4 討論：各建構區塊的優缺點。** 聯合區塊雖是求解對應最佳化問題最直接的方式，但貝葉斯最佳化難以擴展至大型搜尋空間 [45, 73]。交替區塊藉由將搜尋空間分解為兩個較小的子空間來解決此可擴展性問題，惟其假設兩個子空間的改進彼此條件獨立；因此當此假設近似成立時，交替區塊是較佳選擇。條件區塊能在最佳化推進的同時修剪搜尋空間——不佳的臂被拉動的頻率降低，甚至不再被拉動——但其限制是僅適用於類別型的條件變數。對於非類別型變數，一種可行的做法是切分變數的取值範圍：例如給定取值範圍為 1 到 3 的數值變數，可將其切分為 [1, 2) 與 [2, 3) 兩個區間；在最佳化迭代中，先選擇一個子區間，再連同其對應子空間一起最佳化切分後的空間。
>
> 此外，VolcanoML 在交替區塊與條件區塊中預設採用既有文獻的搖臂式演算法 [42, 45]；其他搖臂式演算法——如逐次減半 [29]、Hyperband [43]、BOHB [14] 與 MFES-HB [47]——亦可用於這些區塊。
>
> **3.3.5 討論：不同建構區塊的比較。** 聯合區塊是可套用於所有問題的預設區塊。當搜尋空間相當龐大時，條件區塊與交替區塊會有所幫助。若搜尋空間包含一個類別型超參數，且其各選項之下的子空間彼此條件獨立，則可使用條件區塊，而非探索整個空間。若搜尋空間可分解為兩個近似獨立的子空間，則可套用交替區塊。因此，一個可擴展的系統必須能以不同方式分解問題，並挑選最合適的建構區塊。這便構成一份 VolcanoML 執行計畫，我們將在下一節描述。在第 4 節中，我們也探索在給定一組預先定義的資料集下，透過最大化不同執行計畫的經驗準確率來自動選擇建構區塊的可能性。

---

## 4 VolcanoML Execution Plan | VolcanoML 執行計畫

> [!quote] Original
> Given a pre-defined search space, the input of VolcanoML is (1) a dataset D, (2) a utility metric (e.g, cross-validation accuracy) which defines the objective function f, and (3) a time budget. VolcanoML then decomposes a large search space into an execution plan, following some specific decomposition strategy.
>
> **VolcanoML Execution Plan.** Due to space limitation, we omit the formal definition of a VolcanoML execution plan. Intuitively, a VolcanoML execution plan is a tree of building blocks. The root node corresponds to a building block solving the problem f with the entire search space, which can be further decomposed into multiple building blocks if necessary, as previously described. As an example, Figure 1 illustrates two possible execution plans for f(x, y, z, w; D). Plan 1 contains only a single root building block as a joint block, whereas Plan 2 first introduces a conditioning block on x, and then creates one lower level of building blocks for each possible value of x (in Figure 1, we assume that |D_x| = 3).
>
> **VolcanoML Execution Model.** To execute a VolcanoML execution plan, we follow a Volcano-style execution that is similar to a relational database [21] — the system invokes the do_next! of the root node, which then invokes the do_next! of one of its child nodes, propagating until the leaf node. At any time, one can invoke the get_current_best of the root node, which returns the current best solution for the entire search space.

> [!note] 翻譯
> 在預先定義的搜尋空間下，VolcanoML 的輸入為：(1) 資料集 D；(2) 定義目標函數 f 的效用指標（如交叉驗證準確率）；(3) 時間預算。VolcanoML 隨後依循某種特定的分解策略，將大型搜尋空間分解為一份執行計畫。
>
> **VolcanoML 執行計畫。** 限於篇幅，我們省略 VolcanoML 執行計畫的形式化定義。直觀而言，VolcanoML 執行計畫是一棵由建構區塊組成的樹：根節點對應一個以整個搜尋空間求解問題 f 的建構區塊，必要時可如前述進一步分解為多個建構區塊。舉例來說，圖 1 展示了 f(x, y, z, w; D) 的兩種可能執行計畫：計畫 1 僅包含作為聯合區塊的單一根建構區塊；計畫 2 則先對 x 引入條件區塊，再為 x 的每個可能取值建立下一層的建構區塊（圖 1 中假設 |D_x| = 3）。
>
> **VolcanoML 執行模型。** 執行 VolcanoML 執行計畫時，我們遵循與關聯式資料庫 [21] 相似的 Volcano 式執行——系統呼叫根節點的 do_next!，根節點再呼叫其某個子節點的 do_next!，如此傳遞直至葉節點。在任何時刻皆可呼叫根節點的 get_current_best，其回傳整個搜尋空間目前的最佳解。

> [!quote] Original
> **VolcanoML Plan for auto-sklearn.** Figure 2 presents a VolcanoML execution plan for the same search space explored by auto-sklearn, which consists of the joint search of algorithms, features, and hyper-parameters. Instead of conducting the search process in a single joint block, as was done by auto-sklearn, VolcanoML first decomposes the search space via a conditioning block on algorithms — this introduces a MAB problem in which each arm corresponds to one particular algorithm. It then further decomposes each of the conditioned subspaces via an alternating block between feature engineering and hyper-parameter tuning. The whole subspace of feature engineering (resp. that of hyper-parameter tuning) is optimized by a joint block. Concretely, Figure 2 shows a search space for AutoML with K choices of ML algorithms. During each iteration, starting from the root node, VolcanoML selects the child node to optimize until it reaches a leaf node, and then optimizes over the subspace in the leaf node. As shown by the red lines in Figure 2, in this iteration, VolcanoML only tunes the feature engineering pipeline of algorithm A1 while fixing its algorithm hyper-parameters.
>
> **Alternative Execution Plans.** Note that the execution plan in Figure 2 is not the only possible one. Our flexible and scalable framework in VolcanoML allows us to explore different execution plans before reaching the proposed one. We enumerate five possible plans in a coarse-grained level, and the results show that the proposed plan performs best. The reason why we choose this plan is due to the fundamental property of the AutoML search space — we observe that, the optimal choices of features are different across algorithms, which implies that we can first decompose the search space along ML algorithms. The improvements introduced by feature engineering and hyper-parameter tuning are largely complementary, and thus we can optimize them alternately. For feature engineering (resp. hyper-parameter tuning), the subspace is small enough to be handled by a single joint block efficiently.

> [!note] 翻譯
> **針對 auto-sklearn 搜尋空間的 VolcanoML 計畫。** 圖 2 呈現了針對 auto-sklearn 所探索之相同搜尋空間（由演算法、特徵與超參數的聯合搜尋構成）的 VolcanoML 執行計畫。VolcanoML 不像 auto-sklearn 那樣在單一聯合區塊中進行搜尋，而是先透過以演算法為條件的條件區塊分解搜尋空間——這引入一個 MAB 問題，其中每支臂對應一個特定演算法。接著再透過特徵工程與超參數調校之間的交替區塊，進一步分解每個條件化後的子空間。特徵工程的整個子空間（相應地，超參數調校的子空間）由一個聯合區塊負責最佳化。具體而言，圖 2 展示了含 K 種 ML 演算法選擇的 AutoML 搜尋空間。在每次迭代中，VolcanoML 自根節點出發選擇要最佳化的子節點，直至抵達葉節點，再對葉節點中的子空間進行最佳化。如圖 2 中紅線所示，在該次迭代中，VolcanoML 僅調校演算法 A1 的特徵工程管線，同時固定其演算法超參數。
>
> **替代執行計畫。** 注意圖 2 中的執行計畫並非唯一可能。VolcanoML 靈活且可擴展的框架使我們得以在確定所提計畫之前探索不同的執行計畫。我們在粗粒度層級枚舉了五種可能的計畫，結果顯示所提計畫表現最佳。我們選擇此計畫的原因源於 AutoML 搜尋空間的基本性質——我們觀察到，最佳特徵選擇因演算法而異，這意味著可先沿 ML 演算法分解搜尋空間；特徵工程與超參數調校所帶來的改進大體上互補，因此可交替最佳化二者；而特徵工程（相應地，超參數調校）的子空間已小到足以由單一聯合區塊高效處理。

> [!quote] Original
> **VolcanoML Plan for Enriched Search Space.** We can easily extend VolcanoML and enable functionalities that are not supported by most AutoML systems. For example, Figure 3 illustrates an execution plan for a search space with an additional stage — embedding selection. Given an input, e.g., image or text, we first choose embeddings based on a collection of TensorFlow Hub pre-trained models, and then conduct algorithm selection, feature engineering, and hyper-parameter tuning. We use an execution plan as illustrated in Figure 3, having the embedding selection step jointly optimized together with the feature engineering.
>
> **Discussion: Automatic Plan Generation.** In principle, the design of VolcanoML opens up the opportunity for "automatic plan generation" — given a collection of benchmark datasets, one could automatically search for the best decomposition strategy of the search space and come up with a physical plan automatically. While the full treatment of this problem is beyond the scope of this paper, we illustrate the possibility with a very simple strategy. We automatically enumerate all possible execution plans in a coarse-grained level, and find that our manually specified execution plan in Figure 2 outperforms the alternatives. There is still an open question that whether we can support finer-grained partition of the search space (e.g., different plans for different subspace of features), and moreover, whether we can conduct efficient automatic plan optimization without enumerating all possible plans. These are exciting future directions and we expect the endeavor to be non-trivial. We hope that this paper sets the ground for this line of research in the future (e.g., rule-based heuristics or reinforcement learning).
>
> **Further Optimization with Meta-learning.** VolcanoML supports meta-learning based techniques — given previous runs of the system over similar workloads, to transfer the knowledge and better help the workload at hand — to accelerate the optimization process of building blocks. Appendix contains the details.

> [!note] 翻譯
> **針對擴充搜尋空間的 VolcanoML 計畫。** 我們可以輕易延伸 VolcanoML，啟用多數 AutoML 系統不支援的功能。例如，圖 3 展示了一份針對含額外階段——嵌入選擇（embedding selection）——之搜尋空間的執行計畫。給定輸入（如影像或文字），我們先基於一組 TensorFlow Hub 預訓練模型選擇嵌入，再進行演算法選擇、特徵工程與超參數調校。我們採用如圖 3 所示的執行計畫，將嵌入選擇步驟與特徵工程一起聯合最佳化。
>
> **討論：自動計畫生成。** 原則上，VolcanoML 的設計開啟了「自動計畫生成」的機會——給定一組基準資料集，可自動搜尋搜尋空間的最佳分解策略，並自動產出實體計畫（physical plan）。雖然對此問題的完整處理超出本文範圍，我們以一種非常簡單的策略展示其可能性：在粗粒度層級自動枚舉所有可能的執行計畫，發現我們在圖 2 中手動指定的執行計畫優於其他替代方案。仍然開放的問題包括：能否支援更細粒度的搜尋空間切分（例如對不同的特徵子空間採用不同計畫），以及能否在不枚舉所有可能計畫的前提下進行高效的自動計畫最佳化。這些都是令人振奮的未來方向，我們預期這些努力並非易事。我們希望本文為此研究路線奠定基礎（例如基於規則的啟發式方法或強化學習）。
>
> **以元學習進一步最佳化。** VolcanoML 支援基於元學習的技術——利用系統先前在相似工作負載上的執行紀錄來遷移知識，以更好地協助手邊的工作負載——從而加速建構區塊的最佳化過程。細節見附錄。

---

## 5 Experimental Evaluation | 實驗評估

> [!quote] Original
> We compare VolcanoML with state-of-the-art AutoML systems. In our evaluation, we focus on three perspectives: (1) the performance of VolcanoML given the same search space explored by existing systems, (2) the scalability of VolcanoML given larger search spaces, and (3) the extensibility of VolcanoML to integrate new components into the search space of AutoML pipelines.

> [!note] 翻譯
> 我們將 VolcanoML 與最先進的 AutoML 系統比較。評估聚焦三個面向：(1) 在與既有系統相同的搜尋空間下 VolcanoML 的效能；(2) 在更大搜尋空間下 VolcanoML 的可擴展性；(3) VolcanoML 將新元件整合進 AutoML 管線搜尋空間的可延伸性。

### 5.1 Experimental Setup | 實驗設置

> [!quote] Original
> **AutoML Systems.** We evaluate VolcanoML as well as two open source AutoML systems: auto-sklearn [15] and TPOT [57]. In addition, we also compare VolcanoML with four commercial AutoML platforms from Google, Amazon AWS, Microsoft Azure, and Oracle. Both VolcanoML and auto-sklearn support meta-learning, while TPOT does not. For fair comparison with TPOT, we also use VolcanoML− and AUSK− to denote the versions of VolcanoML and auto-sklearn when meta-learning is disabled. Our implementation of VolcanoML is available at https://github.com/VolcanoML.
>
> **Datasets.** To compare VolcanoML with academic baselines, we use 60 real-world ML datasets from the OpenML repository [71], including 40 for classification (CLS) tasks and 20 for regression (REG) tasks. 10 of the 40 classification datasets are relatively large, each with 20k to 110k data samples; the other 30 are of medium size, each with 1k to 12k samples. In addition, we also use datasets from six Kaggle competitions to compare VolcanoML with four commercial platforms.
>
> **AutoML Tasks.** We define three kinds of real-world AutoML tasks, including (1) a general classification task on 30 medium datasets, (2) a general regression task on 20 medium datasets, and (3) a large-scale classification task on 10 large datasets. To test the scalability of the participating systems, we design three search spaces that include 20, 29, and 100 hyper-parameters, where the smaller search space is a subset of the larger one. We run VolcanoML and the baseline AutoML systems against each of the three search spaces. The time budget is 900 seconds for the smallest search space and 1,800 seconds for the other two, when performing the general classification task (1); the time budget is increased to 5,400 and 86,400 seconds respectively, when performing the general regression task (2) and the large-scale classification task (3).
>
> **Utility Metrics.** Following [15], we adopt the metric balanced accuracy for all classification tasks — compared with standard (classification) accuracy, it assigns equal weights to classes and takes the average of class-wise accuracy. For regression tasks, we use the mean squared error (MSE) as the metric. In our evaluation, we repeat each experiment 10 times and report the average utility metric. In each experiment, we use four fifths of the data samples in each dataset to search for the best ML pipeline and report the utility metric on the remaining fifth.
>
> **Methodology for Comparing AutoML Systems.** To compare the overall test result of each AutoML system on a wide range of datasets, we use the average rank as the metric following [1]. For each dataset, we rank all participant systems based on the result of the best ML pipeline they have found so far; we then take the average of their ranks across different datasets. **More Details.** We include the details of search space and programming API, experiment settings and additional experiments, and reproductions in the Appendix.

> [!note] 翻譯
> **AutoML 系統。** 我們評估 VolcanoML 以及兩個開源 AutoML 系統：auto-sklearn [15] 與 TPOT [57]。此外，我們也將 VolcanoML 與來自 Google、Amazon AWS、Microsoft Azure 及 Oracle 的四個商用 AutoML 平台比較。VolcanoML 與 auto-sklearn 皆支援元學習，TPOT 則否。為與 TPOT 公平比較，我們亦以 VolcanoML− 與 AUSK− 分別表示停用元學習的 VolcanoML 與 auto-sklearn 版本。VolcanoML 的實作公開於 https://github.com/VolcanoML。
>
> **資料集。** 為與學術基線比較，我們使用 OpenML 儲存庫 [71] 的 60 個真實 ML 資料集，其中 40 個用於分類（CLS）任務、20 個用於迴歸（REG）任務。40 個分類資料集中有 10 個相對較大，各含 2 萬至 11 萬筆樣本；其餘 30 個為中型，各含 1 千至 1.2 萬筆樣本。此外，我們也使用六場 Kaggle 競賽的資料集，將 VolcanoML 與四個商用平台比較。
>
> **AutoML 任務。** 我們定義三類真實 AutoML 任務：(1) 於 30 個中型資料集上的一般分類任務；(2) 於 20 個中型資料集上的一般迴歸任務；(3) 於 10 個大型資料集上的大規模分類任務。為測試各參與系統的可擴展性，我們設計了分別包含 20、29 與 100 個超參數的三種搜尋空間，其中較小的搜尋空間是較大者的子集。我們在三種搜尋空間上分別運行 VolcanoML 與各基線 AutoML 系統。執行一般分類任務 (1) 時，最小搜尋空間的時間預算為 900 秒，其餘兩種為 1,800 秒；執行一般迴歸任務 (2) 與大規模分類任務 (3) 時，時間預算分別增至 5,400 秒與 86,400 秒。
>
> **效用指標。** 依循 [15]，所有分類任務採用平衡準確率（balanced accuracy）——與標準（分類）準確率相比，它對各類別賦予相同權重，並取各類別準確率的平均。迴歸任務則採用均方誤差（mean squared error, MSE）。評估中每項實驗重複 10 次，回報平均效用指標。每次實驗中，我們使用各資料集五分之四的樣本搜尋最佳 ML 管線，並在剩餘五分之一上回報效用指標。
>
> **AutoML 系統比較方法。** 為比較各 AutoML 系統在廣泛資料集上的整體測試結果，我們依循 [1] 採用平均排名（average rank）作為指標：對每個資料集，依各參與系統迄今找到的最佳 ML 管線之結果對其排名，再對不同資料集上的排名取平均。**更多細節。** 搜尋空間與程式介面（API）細節、實驗設定與額外實驗、以及重現方式收錄於附錄。

### 5.2 End-to-End Comparison | 端到端比較

> [!quote] Original
> We first evaluate the participant AutoML systems given the search space explored by auto-sklearn. Figure 4 presents the results of VolcanoML compared to auto-sklearn (AUSK) and TPOT on the 30 classification tasks and the 20 regression tasks, respectively. For classification tasks, we plot the classification accuracy improvement (%); for regression tasks, we plot the relative MSE improvement Δ, which is defined as Δ(m1, m2) = (s(m2) − s(m1)) / max(s(m2), s(m1)), where s(·) is MSE on the test set. Overall, VolcanoML outperforms auto-sklearn and TPOT on 25 and 23 of the 30 classification tasks, and on 17 and 15 of the 20 regression tasks, respectively.
>
> We also conduct experiments to evaluate VolcanoML with different time budgets. Figure 5 presents the results on four large classification datasets. We observe that VolcanoML exhibits consistent performance over different time budgets. Notably, on Higgs, VolcanoML achieves 27.2% test error within 4 hours, which is better than the performance of the other two systems given 24 hours.
>
> We further study the scalability of the participant systems on the three aforementioned search spaces. Table 1 summarizes the results in terms of the average ranks. We have two observations: First, without meta-learning, VolcanoML achieves the best average rank for both the classification and regression tasks — on the small search space (with 20 hyper-parameters), VolcanoML performs slightly better than auto-sklearn and TPOT, and it performs significantly better on the medium (with 29 hyper-parameters) and large (with 100 hyper-parameters) search spaces. Second, with meta-learning, the average rank of VolcanoML is dramatically improved compared with auto-sklearn. Overall, VolcanoML with meta-learning achieves the best result over large search space. Furthermore, we also design additional experiments to evaluate the consistency of system performance given different (larger) time budgets and search spaces, and more details can be found in Appendix.

> [!note] 翻譯
> 我們首先在 auto-sklearn 所探索的搜尋空間下評估各參與 AutoML 系統。圖 4 分別呈現 VolcanoML 與 auto-sklearn（AUSK）及 TPOT 在 30 項分類任務與 20 項迴歸任務上的比較結果。分類任務繪製分類準確率改進幅度（%）；迴歸任務繪製相對 MSE 改進量 Δ，定義為 Δ(m1, m2) = (s(m2) − s(m1)) / max(s(m2), s(m1))，其中 s(·) 為測試集上的 MSE。整體而言，VolcanoML 在 30 項分類任務中分別於 25 項與 23 項上優於 auto-sklearn 與 TPOT；在 20 項迴歸任務中分別於 17 項與 15 項上勝出。
>
> 我們亦進行實驗，以不同時間預算評估 VolcanoML。圖 5 呈現四個大型分類資料集上的結果。我們觀察到 VolcanoML 在不同時間預算下均表現一致。值得注意的是，在 Higgs 資料集上，VolcanoML 在 4 小時內達到 27.2% 的測試誤差，優於另外兩個系統在 24 小時內的表現。
>
> 我們進一步在前述三種搜尋空間上研究各參與系統的可擴展性。表 1 以平均排名彙整結果。我們有兩項觀察：第一，在不使用元學習的情況下，VolcanoML 在分類與迴歸任務上均取得最佳平均排名——在小型搜尋空間（20 個超參數）上，VolcanoML 略優於 auto-sklearn 與 TPOT；在中型（29 個超參數）與大型（100 個超參數）搜尋空間上則顯著勝出。第二，在使用元學習時，VolcanoML 的平均排名相較 auto-sklearn 大幅提升。整體而言，帶元學習的 VolcanoML 在大型搜尋空間上取得最佳結果。此外，我們也設計了額外實驗，評估系統在不同（更大）時間預算與搜尋空間下表現的一致性，更多細節見附錄。
>
> ［表 1：三種搜尋空間下，30 個分類（CLS）與 20 個迴歸（REG）資料集上的平均排名（越低越好）。大型分類空間：TPOT 3.29、AUSK− 3.77、AUSK 3.57、VolcanoML− 2.72、VolcanoML 1.65；大型迴歸空間：TPOT 3.1、AUSK− 3.85、AUSK 3.82、VolcanoML− 2.15、VolcanoML 2.08。］

### 5.3 Search Space Enrichment | 搜尋空間擴充

> [!quote] Original
> We now focus on evaluating the extensibility of VolcanoML via two experiments with enriched search spaces.
>
> **Adding Data_Balancing Operator.** In the first experiment, we implement "smote_balancer" – a new feature engineering operator, and incorporate it into the aforementioned balancing stage of feature engineering (FE) (Section 3.1). Note that auto-sklearn cannot support this fine-grained enrichment of the search space. Table 2 presents the results of auto-sklearn, VolcanoML without enrichment, and VolcanoML with enrichment, on five imbalanced datasets. We observe that enriching the search space brings further improvement, e.g., VolcanoML with enrichment outperforms auto-sklearn by 3.57% (balanced accuracy) on the dataset pc2.
>
> **Supporting Embedding Selection.** In the second experiment, we add a new stage "embedding selection" into the FE pipeline, with two candidate embedding-extraction operators (i.e., two pre-trained models). This allows VolcanoML to deal with images, which are not easily supported by both auto-sklearn and TPOT. We implement two pre-trained models to generate embeddings for images, and we evaluate VolcanoML with the enriched search space on the Kaggle dataset dogs-vs-cats. We observe that VolcanoML achieves 96.5% test accuracy, which is significantly better than 69.7% obtained by auto-sklearn without considering embeddings.

> [!note] 翻譯
> 我們接著透過兩項擴充搜尋空間的實驗，評估 VolcanoML 的可延伸性。
>
> **加入資料平衡算子。** 第一項實驗中，我們實作了新的特徵工程算子「smote_balancer」，並將其納入前述特徵工程（FE）的平衡階段（第 3.1 節）。注意 auto-sklearn 無法支援此種細粒度的搜尋空間擴充。表 2 呈現 auto-sklearn、未擴充的 VolcanoML 與擴充後的 VolcanoML 在五個類別不平衡資料集上的結果。我們觀察到擴充搜尋空間帶來進一步的改進，例如在資料集 pc2 上，擴充後的 VolcanoML 以平衡準確率計優於 auto-sklearn 達 3.57%。
>
> **支援嵌入選擇。** 第二項實驗中，我們在 FE 管線中加入新的「嵌入選擇」階段，包含兩個候選嵌入抽取算子（即兩個預訓練模型）。這使 VolcanoML 得以處理影像——auto-sklearn 與 TPOT 皆不易支援的輸入型態。我們實作兩個預訓練模型為影像生成嵌入，並在 Kaggle 資料集 dogs-vs-cats 上以擴充後的搜尋空間評估 VolcanoML。我們觀察到 VolcanoML 達到 96.5% 的測試準確率，顯著優於未考慮嵌入的 auto-sklearn 所得的 69.7%。

### 5.4 Comparison with 4 Industrial Platforms | 與四個工業平台的比較

> [!quote] Original
> In addition, we run additional experiments on six Kaggle datasets to compare VolcanoML with four commercial AutoML platforms: 1) Google Cloud AutoML, 2) Microsoft Azure Automated ML, 3) Oracle data science, and 4) Amazon AWS Sagemaker AutoPilot. Here, we anonymously refer to these platforms as Platform 1-4. Figure 6 show the results, and the Appendix contains the experiment details. We observe that, given the same time budget (i.e., fix the x-axis to some time budget), VolcanoML is at least comparable with, often outperforms, the considered commercial platforms.

> [!note] 翻譯
> 此外，我們在六個 Kaggle 資料集上進行額外實驗，將 VolcanoML 與四個商用 AutoML 平台比較：1) Google Cloud AutoML；2) Microsoft Azure Automated ML；3) Oracle Data Science；4) Amazon AWS SageMaker AutoPilot。此處我們匿名地將其稱為平台 1–4。圖 6 呈現結果，附錄包含實驗細節。我們觀察到，在相同時間預算下（即將橫軸固定於某一時間預算），VolcanoML 至少可與所考量的商用平台相提並論，且經常勝出。

---

## 6 Conclusion | 結論

> [!quote] Original
> In this paper, we have presented VolcanoML, a scalable and extensible framework that allows users to design decomposition strategies for large AutoML search spaces in an expressive and flexible manner. VolcanoML introduces novel building blocks akin to relational operators in database systems that enable expressing search space decomposition strategies in a structured fashion – similar to relational execution plans. Moreover, VolcanoML introduces a Volcano-style execution model, inspired by its classic counterpart that has been widely used for relational query evaluation, to execute the decomposition strategies it yields. Experimental evaluation demonstrates that VolcanoML can generate more efficient decomposition strategies that also lead to performance-wise better ML pipelines, compared to state-of-the-art AutoML systems.

> [!note] 翻譯
> 本文提出了 VolcanoML——一個可擴展、可延伸的框架，讓使用者能以富表達力且靈活的方式，為大型 AutoML 搜尋空間設計分解策略。VolcanoML 引入類似資料庫系統中關聯式運算子的新穎建構區塊，使搜尋空間分解策略得以結構化的方式表達——如同關聯式執行計畫。此外，VolcanoML 引入受經典關聯式查詢求值模型啟發的 Volcano 式執行模型，用以執行其產出的分解策略。實驗評估顯示，相較於最先進的 AutoML 系統，VolcanoML 能生成更高效的分解策略，並帶來效能上更佳的 ML 管線。

---

## References | 參考文獻

> [!note] 翻譯
> References omitted / 參考文獻略。（本轉檔未含附錄內文；原文提及之附錄細節——搜尋空間與 API、實驗設定、額外實驗與重現方式——請參閱原始論文。）
