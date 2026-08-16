---
citation_key: "BossekEtAl2020"
title: "Initial design strategies and their effects on sequential model-based optimization: an exploratory case study based on BBOB"
authors: "Jakob Bossek; Carola Doerr; P. Kerschke"
year: 2020
doi: "10.1145/3377930.3390155"
source: "arXiv (2003.13826)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "2003.13826"
conversion: "pdftotext -layout (automated)"
language: "bilingual-en-zh"
---

# Initial Design Strategies and their Effects on Sequential Model-Based Optimization | 初始設計策略及其對序貫模型式最佳化的影響：基於 BBOB 的探索性案例研究

> [!abstract] 重點摘要
> - 系統性探討序貫模型式最佳化（sequential model-based optimization, SMBO）中初始設計（initial design）的規模與抽樣分布對 EGO（Efficient Global Optimization）演算法整體表現的影響——此設計選擇在文獻中長期受到忽視且建議分歧。
> - 實驗涵蓋 24 個無雜訊 BBOB 函數、5 種維度（d ∈ {2,3,4,5,10}）、6 種總預算（n = 2⁴–2⁹），共 720 個問題；比較 4 種抽樣設計（均勻、LHS、Halton、Sobol'）× 10 種初始設計比例 k，共 40 種策略、逾 57 萬次實驗。
> - 總體而言，SMBO 表現隨初始設計比例增加而變差，使用 Halton 抽樣的小初始預算（k = 10%）中位數表現最佳，但四種設計之間的實際差異相當小。
> - 表現地景（performance landscape）相當缺乏結構：40 種策略中每一種都至少在某一問題上成為虛擬最佳求解器（virtual best solver, VBS）；在高度多模態函數上，EGO 甚至不優於（準）隨機抽樣。
> - 透過百分位數分析比較「單次長跑」與「多次短跑重啟」，發現多個重啟策略較優的情形；兩類觀察皆指向自適應 SMBO 設計與自動化演算法組態（automated algorithm configuration）的潛在效益。

---

## Abstract | 摘要

> [!quote] Original
> Sequential model-based optimization (SMBO) approaches are algorithms for solving problems that require computationally or otherwise expensive function evaluations. The key design principle of SMBO is a substitution of the true objective function by a surrogate, which is used to propose the point(s) to be evaluated next.
>
> SMBO algorithms are intrinsically modular, leaving the user with many important design choices. Significant research efforts go into understanding which settings perform best for which type of problems. Most works, however, focus on the choice of the model, the acquisition function, and the strategy used to optimize the latter. The choice of the initial sampling strategy, however, receives much less attention. Not surprisingly, quite diverging recommendations can be found in the literature.
>
> We analyze in this work how the size and the distribution of the initial sample influences the overall quality of the efficient global optimization (EGO) algorithm, a well-known SMBO approach. While, overall, small initial budgets using Halton sampling seem preferable, we also observe that the performance landscape is rather unstructured. We furthermore identify several situations in which EGO performs unfavorably against random sampling. Both observations indicate that an adaptive SMBO design could be beneficial, making SMBO an interesting test-bed for automated algorithm design.

> [!note] 翻譯
> 序貫模型式最佳化（sequential model-based optimization, SMBO）方法是一類用於求解「函數評估在計算或其他方面成本高昂」之問題的演算法。SMBO 的核心設計原則是以代理模型（surrogate）替代真實目標函數，並利用代理模型提出下一個（或多個）待評估的點。
>
> SMBO 演算法在本質上是模組化的，留給使用者許多重要的設計選擇。大量研究致力於理解哪種設定對哪類問題表現最佳；然而，多數研究聚焦於模型的選擇、採集函數（acquisition function）的選擇，以及最佳化後者所用的策略。相對地，初始抽樣策略的選擇受到的關注少得多。不出所料，文獻中可以找到相當分歧的建議。
>
> 本文分析初始樣本的規模與分布如何影響知名 SMBO 方法——高效全域最佳化（efficient global optimization, EGO）演算法——的整體品質。雖然總體而言，使用 Halton 抽樣的小初始預算似乎較為可取，但我們也觀察到表現地景（performance landscape）相當缺乏結構。此外，我們指出了數種 EGO 表現不如隨機抽樣的情形。這兩項觀察皆顯示自適應的 SMBO 設計可能有所助益，使 SMBO 成為自動化演算法設計（automated algorithm design）的有趣試驗場。

---

## 1 Introduction | 引言

> [!quote] Original
> Sequential Model-Based Optimization (SMBO) algorithms are techniques for the optimization of problems for which the evaluation of solution candidates is resource-intensive, such as problems requiring real physical experiments or problems that require computationally-expensive simulations. The latter are particularly present in almost any application of Artificial Intelligence, most notably in terms of parameter tuning problems – a problem that is also omnipresent in Evolutionary Computation [37]. SMBO-based techniques are among the most successfully applied hyper-parameter tuning methods [1, 18, 28, 35], so that research on this family of iterative optimization heuristics has gained significant traction in the last decade. SMBO forms today an integral part of the state-of-the-art heuristic solvers. Its probably best-known representatives are Bayesian Optimization (see surveys by [40, 46, 49]) and, in particular, Efficient Global Optimization (EGO, [30]).

> [!note] 翻譯
> 序貫模型式最佳化（SMBO）演算法是一類針對「候選解評估極耗資源」之問題的最佳化技術，例如需要真實物理實驗的問題，或需要高計算成本模擬的問題。後者幾乎存在於人工智慧的所有應用中，尤以參數調校（parameter tuning）問題最為顯著——此問題在演化計算（Evolutionary Computation）中亦無所不在 [37]。基於 SMBO 的技術是應用最成功的超參數調校方法之一 [1, 18, 28, 35]，因此對這一族迭代式最佳化啟發式方法的研究在過去十年間獲得顯著關注。今日，SMBO 已是最先進啟發式求解器不可或缺的一部分，其最著名的代表當屬貝葉斯最佳化（Bayesian Optimization，見綜述 [40, 46, 49]），尤其是高效全域最佳化（EGO, [30]）。

---

> [!quote] Original
> The generic SMBO method works as follows. An initial design of points is sampled and evaluated with the true objective function. The eponymous sequential part iteratively (1) builds a surrogate of the true objective function (on basis of the already evaluated samples), (2) proposes new samples by optimizing a so-called infill-criterion (which is sometimes referred to as acquisition function), (3) evaluates these additional samples, and (4) integrates these samples, together with their quality indicators ("function values", "fitness") into the memory. Each of these steps offers a great variety of design choices, which all may affect the performance of the SMBO procedure. Which surrogate model should be used? Which of the countless infill criteria to use? What method should be used to create the initial sample and what proportion of the overall budget should be spent on the initial design? While a large body of works addresses the first two questions (see surveys mentioned above), the latter two questions are treated rather poorly. In this work we aim to shed light on the relevance of a suitably chosen initial sampling strategy. More precisely, we study how the size of the initial design and the strategy used to generate it affects the performance of SMBO. As a well-established benchmark environment offering a great variety of different numerical optimization problems, we chose the 24 noiseless BBOB functions (in different dimensions) as test-bed for our investigation.

> [!note] 翻譯
> 通用的 SMBO 方法運作如下：先抽樣一組初始設計點，並以真實目標函數加以評估。而其名稱中「序貫」的部分則迭代地 (1) 基於已評估的樣本建立真實目標函數的代理模型；(2) 透過最佳化所謂的填入準則（infill criterion，有時稱為採集函數）提出新樣本；(3) 評估這些額外樣本；(4) 將這些樣本連同其品質指標（「函數值」、「適應度」）整合進記憶中。上述每個步驟都提供了種類繁多的設計選擇，而這些選擇皆可能影響 SMBO 程序的表現：應使用哪種代理模型？在數不清的填入準則中該選用哪一個？應以何種方法產生初始樣本？整體預算中應有多大比例投入初始設計？儘管大量研究處理了前兩個問題（見前述綜述），後兩個問題卻鮮少受到妥善探討。本文旨在闡明妥善選擇初始抽樣策略的重要性。更精確地說，我們研究初始設計的規模及其生成策略如何影響 SMBO 的表現。作為提供多樣數值最佳化問題、廣受認可的基準環境，我們選擇 24 個無雜訊 BBOB 函數（在不同維度下）作為研究的試驗場。

---

> [!quote] Original
> Our setup comprises of varying the initial design strategy (classical uniform and Latin-Hypercube-Sampling (LHS) as the most frequently used methods and quasi-random Halton and Sobol' sequences), the total budget, and the fraction of this total budget that is used to build the initial sample. We study a total of 720 problems, which are evaluated against 40 different initial design strategies.
>
> Our general observation is that SMBO performance tends to decrease with increasing initial design ratio, which is in line with the general expectation that adaptive search should outperform non-adaptive sampling. This may justify extreme settings such as the singleton initial design used in the SMAC parameter tuning framework [28]. As always in simulation-based optimization, we are confronted with the important trade-off between the exploitation of already acquired knowledge (through adaptive sampling) and the reduction of uncertainty in regions of the search space that are currently not well covered with already evaluated samples. Sampling in the latter regions of high uncertainty – commonly referred to as exploration – can help to identify other promising regions of the search space. In our experiments, we observe indeed that small initial designs are not always preferable. In fact, we even identify cases in which pure (quasi-)random sampling outperforms any of the tested SMBO-based techniques.

> [!note] 翻譯
> 我們的實驗設置涵蓋了初始設計策略的變化（最常用的古典均勻抽樣與拉丁超立方抽樣（Latin Hypercube Sampling, LHS），以及準隨機（quasi-random）的 Halton 與 Sobol' 序列）、總預算，以及總預算中用於建立初始樣本的比例。我們共研究 720 個問題，並針對 40 種不同的初始設計策略進行評估。
>
> 我們的總體觀察是：SMBO 的表現傾向於隨初始設計比例的增加而下降，這與「自適應搜尋應優於非自適應抽樣」的一般預期相符。這或可為極端設定提供依據，例如 SMAC 參數調校框架 [28] 所採用的單點初始設計。如同所有基於模擬的最佳化，我們面臨一項重要的權衡：一方是利用已獲得的知識（透過自適應抽樣），另一方是降低搜尋空間中目前尚未被已評估樣本妥善覆蓋之區域的不確定性。在後者這類高不確定性區域抽樣——通常稱為探索（exploration）——有助於發現搜尋空間中其他有希望的區域。在我們的實驗中，確實觀察到小初始設計並非總是較優；事實上，我們甚至發現純粹的（準）隨機抽樣勝過所有受測 SMBO 技術的情形。

---

> [!quote] Original
> We use our huge database also to investigate advantages of long runs vs. restarted ones. That is, we address the question whether one should use the full budget for one long run, or whether two shorter runs of smaller budget are preferable. We identify several cases in which restarts seem preferable, giving another indication that an adaptive design of SMBO techniques could be preferable.
>
> The evaluation and analysis of the dataset (which comprises more than 500 000 experiments) has been particularly challenging, as no clear pattern between the performance of the different designs and the parameters of the problem (such as its dimension, its high-level features, or even its function ID) were observable. Our data suggests that machine-trained algorithm configuration techniques should be able to outperform state-of-the-art SMBO designs by large margins. The appropriateness of the BBOB dataset for finding generalizable patterns has been shown in [4, 33].
>
> **Paper Organization.** This work is structured as follows. Below, we continue with an overview of related work and give information about the availability of our data. Section 2 details the SMBO approach. In Section 3 we describe our experimental setup including considered benchmark problems, parameter choices and performance measures. Results are presented in Sections 4 to 6. We conclude with final remarks and visions for incorporating the acquired knowledge into improved SMBO approaches.

> [!note] 翻譯
> 我們也利用這個龐大的資料庫探究「長跑」與「重啟」的相對優勢，亦即回答以下問題：應將全部預算投入單次長跑，還是以較小預算執行兩次較短的執行更為可取？我們找出了數個重啟似乎較優的情形，這再度顯示自適應的 SMBO 設計可能更為可取。
>
> 對此資料集（包含逾 500,000 次實驗）的評估與分析尤具挑戰性，因為在不同設計的表現與問題參數（如維度、高階特徵，甚至函數編號）之間觀察不到清晰的模式。我們的資料顯示，機器訓練的演算法組態（algorithm configuration）技術應能以大幅優勢勝過最先進的 SMBO 設計。BBOB 資料集用於發掘可泛化模式的適切性已在 [4, 33] 中得到證實。
>
> **論文結構。** 本文結構如下：下文接續相關研究的概覽，並說明資料的取得方式。第 2 節詳述 SMBO 方法。第 3 節描述實驗設置，包括所考慮的基準問題、參數選擇與效能量度。結果呈現於第 4 至第 6 節。最後以結語作結，並展望如何將所獲知識融入改良的 SMBO 方法。

---

> [!quote] Original
> **Related Work.** For surveys on Bayesian optimization and, more generally, SMBO approaches we refer the interested reader to the already mentioned surveys [40, 46, 49]. Our work builds on EGO, originally suggested by Jones, Schonlau, and Welch [30]. EGO is characterized by using a flexible Kriging, i.e., a Gaussian process surrogate model which offers a natural uncertainty estimate and the widely used quasi-standard expected improvement (EI) infill criterion which balances exploitation of the model and exploration of uncertain regions of the model [29].
>
> Our key interest is an analysis of the influence of the initial design's size and distribution. We assess four different distributions: uniform sampling, LHS, Halton points, and Sobol' sequences. For each of these designs we test ten different initial sample sizes. Recommendations on which initial design should be favored vary quite significantly within the community, see [2, 41] for a discussion. In terms of design size, SMAC [28] makes an extreme choice in that it uses only one randomly sampled initial design point, whereas other commonly found SMBO implementations typically operate with an initial design of size 10 · d [30, 41], where d denotes the search space dimension (i.e., the optimization problem can be modeled as a function f : S ⊆ R^d → R). In terms of design distribution, LHS and uniform sampling are routinely used in SMBO applications, while quasi-random designs, like Halton and Sobol' designs, are less commonly found – despite several indications that their even distribution may be beneficial for maximizing the initial exploration [48].

> [!note] 翻譯
> **相關研究。** 關於貝葉斯最佳化乃至更廣義 SMBO 方法的綜述，有興趣的讀者可參閱前述綜述 [40, 46, 49]。本研究建立於 EGO 之上，該演算法最初由 Jones、Schonlau 與 Welch [30] 提出。EGO 的特徵在於使用彈性的克利金（Kriging）模型——即高斯過程（Gaussian process）代理模型，可自然提供不確定性估計——以及廣泛使用、近乎標準的期望改進量（expected improvement, EI）填入準則，後者在「利用模型」與「探索模型的不確定區域」之間取得平衡 [29]。
>
> 我們的核心興趣是分析初始設計之規模與分布的影響。我們評估四種不同的分布：均勻抽樣、LHS、Halton 點集與 Sobol' 序列；針對每種設計測試十種不同的初始樣本規模。學界對於應偏好何種初始設計的建議分歧甚大，相關討論見 [2, 41]。就設計規模而言，SMAC [28] 做出極端選擇，僅使用一個隨機抽樣的初始設計點；而其他常見的 SMBO 實作通常採用規模為 10 · d 的初始設計 [30, 41]，其中 d 表示搜尋空間維度（亦即最佳化問題可建模為函數 f : S ⊆ R^d → R）。就設計分布而言，LHS 與均勻抽樣是 SMBO 應用中的常規選擇，而 Halton 與 Sobol' 等準隨機設計則較少見——儘管已有多項跡象顯示其均勻的分布可能有利於最大化初始探索 [48]。

---

> [!quote] Original
> We next summarize the main works which explicitly address the question how to chose the initial design.
>
> Bartz-Beielstein and Preuss study in [2] suitable initial designs for SPOT [1], an SMBO algorithm specifically designed to perform well on parameter tuning challenges. From experiments on hyperparameter tuning of evolutionary computation techniques, they conclude that LHS sampling is, in general, to be preferred over uniform sampling. They thereby disagree with statements previously made in [48], which argues that LHS designs do not gain much over uniform sampling, and that quasi-random sampling strategies should be used instead. The recommendation in [48] is, however, to be understood in terms of general design of experiments setting, and not specifically addressing SMBO initialization.
>
> Brockhoff et al. [11] studied the difference between random sampling and LHS designs for Matlab's MATSuMoTo model-based optimizer [42]. In contrast to our work, they fix the total budget of function evaluations to n = 50 · d (whereas we use n = 2^4, . . . , 2^9) and compared only four initial designs: LHS with 2 · (d + 1) · k for k = 1, 2, 10 and random sampling with 4 · (d + 1) points. Results are compared against SMAC [28] and pure random sampling. Their experiments are also across all 24 BBOB functions in d = 2, 3, 5, 10, 20 dimensions (we study d = 2, 3, 4, 5, 10). Their performance measure is a fixed-target measure, more precisely they study the expected running time (ERT) for target values that are chosen individually for each function and they also compare the anytime performance in terms of ECDF curves. Based on their experiments, Brockhoff et al. conclude that for this setting, no clear advantage of LHS designs can be observed and that large initial samples seem detrimental.

> [!note] 翻譯
> 接下來，我們總結明確處理「如何選擇初始設計」問題的主要研究。
>
> Bartz-Beielstein 與 Preuss 在 [2] 中研究了適合 SPOT [1]（一個專為在參數調校挑戰上表現良好而設計的 SMBO 演算法）的初始設計。根據對演化計算技術超參數調校的實驗，他們的結論是：一般而言，LHS 抽樣應優先於均勻抽樣。此結論與 [48] 先前的主張相左——後者認為 LHS 設計相較均勻抽樣所獲甚微，反而應採用準隨機抽樣策略。不過，[48] 的建議應從一般實驗設計（design of experiments）的脈絡來理解，並非專門針對 SMBO 的初始化。
>
> Brockhoff et al. [11] 針對 Matlab 的 MATSuMoTo 模型式最佳化器 [42]，研究了隨機抽樣與 LHS 設計之間的差異。與本研究不同，他們將函數評估的總預算固定為 n = 50 · d（而我們使用 n = 2⁴, . . . , 2⁹），且僅比較四種初始設計：規模為 2 · (d + 1) · k（k = 1, 2, 10）的 LHS，以及 4 · (d + 1) 個點的隨機抽樣。其結果與 SMAC [28] 及純隨機抽樣進行比較。他們的實驗同樣涵蓋全部 24 個 BBOB 函數，維度為 d = 2, 3, 5, 10, 20（我們研究 d = 2, 3, 4, 5, 10）。其效能量度為固定目標量度（fixed-target measure），更精確地說，他們研究針對每個函數個別選定之目標值的期望執行時間（expected running time, ERT），並以 ECDF 曲線比較任意時間表現（anytime performance）。基於其實驗，Brockhoff et al. 的結論是：在此設定下，觀察不到 LHS 設計的明顯優勢，且大型初始樣本似乎有害。

---

> [!quote] Original
> Morar et al. [41] also compare LHS and uniform sampling, but fix the size of the initial design to 2 · d and rather focus on the interplay between initial design distribution and the infill criteria used in the adaptive steps of the SMBO framework. They compare performances on two variants of the Branin function, a classic benchmark in SMBO research, and on two parameter tuning problems. They conclude that the total budget has an important influence on the ranking of the different SMBO algorithms. In line with our observations and conclusions, they recommend tuning of the SMBO design if one is likely to see similar types of problems several times.
>
> More recently, Lindauer et al. [36] analyze the sensitivity of Bayesian optimization heuristics w.r.t. its own hyper-parameters. This study, however, puts a much stronger emphasis on the various design choices, and details for the initial sampling strategy are not explicitly mentioned, although Table 3 in their work suggests that this has been varied as well.

> [!note] 翻譯
> Morar et al. [41] 同樣比較 LHS 與均勻抽樣，但將初始設計的規模固定為 2 · d，並將重點放在初始設計分布與 SMBO 框架自適應步驟所用填入準則之間的交互作用。他們在 Branin 函數（SMBO 研究中的經典基準）的兩個變體以及兩個參數調校問題上比較效能，結論是總預算對不同 SMBO 演算法的排名有重要影響。與我們的觀察及結論一致，他們建議：若預期會多次遇到類似類型的問題，應對 SMBO 設計進行調校。
>
> 較近期地，Lindauer et al. [36] 分析了貝葉斯最佳化啟發式方法對其自身超參數的敏感度。然而，該研究更著重於各種設計選擇，並未明確提及初始抽樣策略的細節，儘管其論文中的表 3 暗示此因素亦曾被變動。

---

> [!quote] Original
> **Availability of Project Data.** While this report highlights a few of our key findings, and demonstrates which statistics are possible to obtain with the data, the full data base offers much more than we can touch upon in a single conference paper. Not only can our data be used to zoom further into the various settings described below, but it also offers additional information about the function value of the best initial design point and of the first point queried in an adaptive fashion, as well as the distance of these points and of the best solution to the optimal solution (in the decision space [−5, 5]^d, measured in terms of the L2 norm).
>
> Please note that most of the results reported below are based on median values per (dimension, function, total budget, initial budget ratio, design) combination. This is to avoid correcting factors for the comparison between the Halton designs (for which we have 5 runs for each of the 7 200 considered settings) and the other three designs (for which we have 25 independent runs per setting, i.e., 5 SMBO runs for each of the 5 random samples from the design). Detailed results for each experiment are available in the data base, so that one can easily perform statistical tests, or use other aggregation methods. An interactive evaluation of the data is possible with the very recently released tool HiPlot [25], which essentially produces parallel coordinate plots through which one can easily navigate by zooming and/or highlighting different parts of the data.
>
> The interested reader can find all our project data on [10].

> [!note] 翻譯
> **專案資料的取得。** 本文僅著重呈現部分關鍵發現，並展示可從資料中獲得哪些統計量；完整資料庫所能提供的遠超出單篇會議論文所能觸及的範圍。我們的資料不僅可用於進一步深究下文所述的各種設定，還提供額外資訊：最佳初始設計點的函數值、首個以自適應方式查詢之點的函數值，以及這些點與最佳解到最優解的距離（在決策空間 [−5, 5]^d 中，以 L2 範數衡量）。
>
> 請注意，下文報告的多數結果基於每個（維度、函數、總預算、初始預算比例、設計）組合的中位數值。這是為了避免在比較 Halton 設計（在 7,200 個考慮的設定中各有 5 次執行）與其他三種設計（每個設定有 25 次獨立執行，即對設計的 5 個隨機樣本各執行 5 次 SMBO）時引入校正因子。每次實驗的詳細結果皆存於資料庫中，因此可以輕易執行統計檢定或採用其他聚合方法。透過甫發布的工具 HiPlot [25]（其本質上產生平行座標圖，可透過縮放或標示資料的不同部分輕鬆瀏覽），可對資料進行互動式評估。
>
> 有興趣的讀者可在 [10] 找到我們所有的專案資料。

---

## 2 Sequential Model-Based Optimization | 序貫模型式最佳化

> [!quote] Original
> In many real-world applications like production engineering, numerical simulations, or hyper-parameter tuning, the objective function f at hand is often of black-box nature. That is, (a) there is little or no knowledge about the structure of f (in particular, we typically do not have derivatives), and (b) function evaluations are expensive in terms of computational and/or monetary resources (days of computation time or actual physical experiments). As a consequence, in the course of problem solving, one tries to keep the number of true function evaluations low. In such settings, sequential model-based optimization (SMBO, [27]) – also known under the term Bayesian optimization¹ – advanced to the state-of-the-art in recent years and is used extensively in many fields of research, e.g., within versatile tools for automatic algorithm configuration [28].
>
> ¹ Originally, Bayesian Optimization only referred to SMBO approaches with Bayesian priors, but nowadays the term is often used to denote the whole class of SMBO methods.

> [!note] 翻譯
> 在許多真實世界的應用中——如生產工程、數值模擬或超參數調校——手邊的目標函數 f 往往具有黑盒性質。也就是說：(a) 對 f 的結構所知甚少或一無所知（尤其通常無法取得導數）；(b) 函數評估在計算與／或金錢資源上成本高昂（數日的計算時間或實際的物理實驗）。因此，在求解問題的過程中，人們會盡量減少真實函數評估的次數。在此類情境中，序貫模型式最佳化（SMBO, [27]）——亦稱貝葉斯最佳化¹——近年來已發展為最先進技術，並廣泛用於眾多研究領域，例如作為自動演算法組態之多功能工具的一部分 [28]。
>
> ¹ 原本「貝葉斯最佳化」僅指具有貝葉斯先驗的 SMBO 方法，但如今此詞常用於泛指整類 SMBO 方法。

---

> [!quote] Original
> In a nutshell, the key idea of SMBO is as follows: a regression model, i.e., an approximation f̂ to the true optimization problem f, is fitted to the evaluated points of an initial design. Subsequently, the model f̂ serves as a cheap surrogate for the expensive true objective function and is used to determine the next point(s) worth being evaluated through the actual problem f. These points are determined by optimizing a so-called infill criterion (also referred to as acquisition function) which keeps balance between exploiting the model (in the sense of striving to high-quality points) and exploring the search-space regions which lack a good model fit (i.e., regions with a high uncertainty about the quality of approximation f̂). Note that the optimization of the acquisition function itself is an (often highly multimodal) optimization problem, which is typically solved by state-of-the art solvers such as CMA-ES [24], Nelder-Mead [43], or simply by standard Newton methods, if the surrogate model f̂ allows. The key here is that those algorithms now operate on f̂ and not on f, which can be evaluated much more efficiently. The points proposed from the optimization of the acquisition function are then evaluated through f and the surrogate f̂ is updated to account for the new information. The process is repeated until the available budget of time or function evaluations is depleted.
>
> Jones [30] was the first who used this approach in his Efficient Global Optimization (EGO) algorithm. Therein, Gaussian processes serve as the surrogate and expected improvement (EI) is adopted as infill criterion. Following Jones' seminal contribution, a plethora of extensions were proposed by the community including multi-point proposal [7] and multi-objective SMBO (e.g., [34]) making SMBO a highly flexible framework with many interchangeable components and facets. We refer the interested reader to [27] (and references therein) for a comprehensive overview. Our study is based on the classical EGO algorithm by Jones.

> [!note] 翻譯
> 簡而言之，SMBO 的核心思想如下：先將一個迴歸模型——即對真實最佳化問題 f 的近似 f̂——擬合於初始設計中已評估的點。隨後，模型 f̂ 作為昂貴真實目標函數的廉價代理，用於決定下一個（或多個）值得透過實際問題 f 評估的點。這些點透過最佳化所謂的填入準則（亦稱採集函數）決定，該準則在「利用模型」（即力求高品質的點）與「探索缺乏良好模型擬合的搜尋空間區域」（即對近似 f̂ 之品質具高度不確定性的區域）之間維持平衡。值得注意的是，採集函數的最佳化本身即是一個（往往高度多模態的）最佳化問題，通常由最先進的求解器如 CMA-ES [24]、Nelder-Mead [43] 求解，或在代理模型 f̂ 允許時，直接以標準牛頓法求解。此處的關鍵在於，這些演算法如今是在 f̂ 而非 f 上運作，而前者可被高效得多地評估。由採集函數最佳化所提出的點隨後透過 f 評估，並更新代理模型 f̂ 以納入新資訊。此過程反覆進行，直到可用的時間或函數評估預算耗盡。
>
> Jones [30] 是首位在其高效全域最佳化（EGO）演算法中使用此方法的人。其中，高斯過程作為代理模型，並採用期望改進量（EI）作為填入準則。繼 Jones 的開創性貢獻之後，社群提出了大量擴展，包括多點提案（multi-point proposal）[7] 與多目標 SMBO（如 [34]），使 SMBO 成為一個具有眾多可互換組件與面向的高度彈性框架。完整概覽可參閱 [27]（及其中的參考文獻）。本研究基於 Jones 的古典 EGO 演算法。

---

## 3 Experimental Setup | 實驗設置

> [!quote] Original
> Our study investigates the effect of the total budget, the size of the initial design (i.e., the number of evaluations prior to building the first surrogate), and the distribution of this initial design on the quality of the final recommendation made by an off-the-shelf SMBO algorithm. Below, we summarize the benchmark problems and solution strategies (Section 3.1), as well as the performance measures that we used to evaluate the different strategies (Section 3.2).
>
> All our experiments are implemented in the R programming environment [45]. To be more precise: the SMBO framework mlrMBO [6] serves as the working horse for our experimental study, the smoof package [8] is used for an interface to the BBOB functions and the interface package dandy [9] is used to generate the initial designs. The latter delegates to packages qrng [26] and randtoolbox [14], which implement quasi-random sequence generators as well as to package lhs [13] for the LHS designs.

> [!note] 翻譯
> 本研究探討總預算、初始設計的規模（即建立第一個代理模型之前的評估次數）以及此初始設計的分布，對現成 SMBO 演算法最終推薦品質的影響。下文總結基準問題與求解策略（第 3.1 節），以及用於評估不同策略的效能量度（第 3.2 節）。
>
> 我們所有的實驗皆以 R 程式環境 [45] 實作。更精確地說：SMBO 框架 mlrMBO [6] 是實驗研究的主力工具；smoof 套件 [8] 用於介接 BBOB 函數；介面套件 dandy [9] 用於生成初始設計，後者委派給實作準隨機序列產生器的套件 qrng [26] 與 randtoolbox [14]，以及提供 LHS 設計的套件 lhs [13]。

---

### 3.1 Benchmark Problems and Solvers | 基準問題與求解器

> [!quote] Original
> We use the following setup for our experimental analysis:
> - **The objective function f.** As mentioned in the introduction, we focus on the 24 functions from the (noiseless and single-objective) BBOB test suite [22]. An overview of these functions is available in [23]. We consider the first instance of each function, whose d-dimensional variant we denote by f^(d). We let F_d be the collection of these 24 functions. We study minimization as objective.
> - **The problem dimension d.** We consider five different search space dimensions: d ∈ D := {2, 3, 4, 5, 10}.
> - **Total budget n.** The total number of function evaluations. We consider six different budgets: n ∈ N := {2^x | x ∈ {4, . . . , 9}}.
> - **Initial design ratio k:** We consider initial designs of size ⌈k · n⌉ with k ∈ K := {0.1, 0.2, . . . , 1.0}.
> - **Sampling design s.** We study four different distributions from which the d-dimensional initial design of size ⌈k · n⌉ is sampled:
>   – uniform sampling: R's default random number generator (Mersenne-Twister [38]) to generate uniform samples.
>   – Latin Hypercube Sampling (LHS [39]): "improved" LHS design as suggested in [3].
>   – Sobol' sequences [50]: randtoolbox implementation with scrambling as proposed by Owen [44], and Faure & Tezuka [19].
>   – Halton designs [21]: randtoolbox implementation with default parameters.
>   More detailed definitions, motivations, and applications of these distributions can be found, for example, in [15].
> - **Random seed r_i - initial design.** While the Halton point sets are deterministic, the other designs produce random points. To account for this randomness, we sample R_i = 5 instances from each of the three random (i.e., non-Halton) designs.
> - **Random seed r_A - SMBO randomness.** Finally, to compensate for the randomness of the SMBO algorithm (note that the SMBO process is stochastic itself, e.g., by means of a stochastic procedure used to search the infill-criterion), we do R_A = 5 independent runs per each of the settings fixed through the decisions above.
>
> It should be noted that we do not vary the infill criterion (also known as acquisition function), nor any other component of the SMBO, but use the default variant of mlrMBO v1.1.4 with expected improvement as infill criterion and a Kriging surrogate.

> [!note] 翻譯
> 我們的實驗分析採用以下設置：
> - **目標函數 f。** 如引言所述，我們聚焦於（無雜訊、單目標）BBOB 測試套件 [22] 中的 24 個函數，其概覽見 [23]。我們考慮每個函數的第一個實例，其 d 維變體記為 f^(d)，並令 F_d 為這 24 個函數的集合。我們以最小化為目標。
> - **問題維度 d。** 我們考慮五種不同的搜尋空間維度：d ∈ D := {2, 3, 4, 5, 10}。
> - **總預算 n。** 即函數評估的總次數。我們考慮六種不同的預算：n ∈ N := {2^x | x ∈ {4, . . . , 9}}。
> - **初始設計比例 k：** 我們考慮規模為 ⌈k · n⌉ 的初始設計，其中 k ∈ K := {0.1, 0.2, . . . , 1.0}。
> - **抽樣設計 s。** 我們研究四種不同的分布，自其中抽取規模為 ⌈k · n⌉ 的 d 維初始設計：
>   – 均勻抽樣：使用 R 的預設隨機數產生器（Mersenne-Twister [38]）生成均勻樣本。
>   – 拉丁超立方抽樣（LHS [39]）：採用 [3] 所建議的「改良式」LHS 設計。
>   – Sobol' 序列 [50]：randtoolbox 實作，並採用 Owen [44] 及 Faure & Tezuka [19] 提出的擾亂（scrambling）。
>   – Halton 設計 [21]：randtoolbox 實作，使用預設參數。
>   關於這些分布更詳細的定義、動機與應用，可參閱例如 [15]。
> - **隨機種子 r_i——初始設計。** Halton 點集是確定性的，其他設計則產生隨機點。為考量此隨機性，我們對三種隨機（即非 Halton）設計各抽取 R_i = 5 個實例。
> - **隨機種子 r_A——SMBO 隨機性。** 最後，為補償 SMBO 演算法本身的隨機性（注意 SMBO 過程本身即是隨機的，例如搜尋填入準則所用的隨機程序），對上述決策所固定的每一設定，我們執行 R_A = 5 次獨立運行。
>
> 應注意的是，我們並未變動填入準則（即採集函數）或 SMBO 的任何其他組件，而是使用 mlrMBO v1.1.4 的預設變體，以期望改進量為填入準則、克利金模型為代理模型。

---

> [!quote] Original
> With the notation above, we consider a total number of |F| · |D| · |N| = 24 · 5 · 6 = 720 different problems, and for each of these problems we consider |S| · |K| = 4 · 10 = 40 different solution strategies. Here we consider the budget as integral part of a problem, since SMBO algorithms are typically applied when the budget is fixed a priori. We therefore distinguish between the function f^(d) that is to be optimized, and the problem (f, d, n) of minimizing f^(d) with a given budget n.
>
> As mentioned above, on each problem we perform 5 runs of each strategy which is based on Halton designs and we perform 25 runs for all other strategies. Our total number of experiments is thus
>
> |F| · |D| · |N| · |K| · (1 + (|S| − 1) · R_i) · R_A = 24 · 5 · 6 · 10 · (1 + (3 · 5)) · 5 = 576 000.
>
> Not all of these runs terminated successfully, due to problems with the Kriging implementation used by mlrMBO. The problems occur in particular with high total budget and low initial design ratio. Here, the Kriging-routine obviously runs into problems when many points are sampled close to each other as it often is the case when SMBO runs converge into a (local) optimum. While for each n ≤ 128 there are at least 99.8% successful runs this number reduces to 94% for n = 256 and 85.4% for n = 512. In total, we had 555 598 (96.5%) successful runs. In all computations below we only consider (f, d, n, k, s) combinations for which at least three runs terminated successfully, i.e., provided their recommendation.

> [!note] 翻譯
> 依上述符號，我們共考慮 |F| · |D| · |N| = 24 · 5 · 6 = 720 個不同的問題，且對每個問題考慮 |S| · |K| = 4 · 10 = 40 種不同的求解策略。此處我們將預算視為問題不可分割的一部分，因為 SMBO 演算法通常應用於預算事先固定的情境。因此，我們區分「待最佳化的函數 f^(d)」與「以給定預算 n 最小化 f^(d) 的問題 (f, d, n)」。
>
> 如前所述，對每個問題，基於 Halton 設計的策略各執行 5 次，其他所有策略各執行 25 次。因此，實驗總數為
>
> |F| · |D| · |N| · |K| · (1 + (|S| − 1) · R_i) · R_A = 24 · 5 · 6 · 10 · (1 + (3 · 5)) · 5 = 576,000。
>
> 由於 mlrMBO 所用克利金實作的問題，並非所有執行皆成功結束。問題尤其出現在高總預算與低初始設計比例的情形：當許多點彼此靠近地被抽樣時（SMBO 收斂至（局部）最優解時常見此情況），克利金常式顯然會遇到困難。對每個 n ≤ 128，至少有 99.8% 的執行成功；此數字在 n = 256 時降至 94%，在 n = 512 時降至 85.4%。總計有 555,598 次（96.5%）成功執行。下文所有計算中，我們僅考慮至少有三次執行成功結束（即提供其推薦解）的 (f, d, n, k, s) 組合。

---

### 3.2 Performance Measures and VBS | 效能量度與虛擬最佳求解器

> [!quote] Original
> For each of our experiments we record the value of the best solution that has been evaluated during the entire run. We denote this value by f(d, n, k, s, r_i, r_A). Since the BBOB functions have quite diverse ranges of function values, we do not study these function values directly, but rather follow standard practice in BBOB studies and focus on the target precision, i.e., the gap to the global optimum,
>
> p(f, d, n, k, s, r_i, r_A) := f(d, n, k, s, r_i, r_A) − inf f^(d).
>
> As mentioned above, we will restrict most of our analyses to the median performance of each strategy on each problem. Our main performance criteria is therefore
>
> M(f, d, n, k, s) = M({p(f, d, n, k, s, r_i, r_A) | r_i ∈ R_i(s), r_A ∈ [5]}),
>
> where M denotes the median and where we use the convention that R_i(Halton) = {1} and R_i(s) = {1, 2, . . . , 5} =: [5] for the other sampling designs s ∈ S \ {Halton}.

> [!note] 翻譯
> 對每次實驗，我們記錄整個執行期間所評估過之最佳解的數值，記為 f(d, n, k, s, r_i, r_A)。由於各 BBOB 函數的函數值範圍差異甚大，我們不直接研究這些函數值，而是遵循 BBOB 研究的標準做法，聚焦於目標精度（target precision），即與全域最優解之間的差距：
>
> p(f, d, n, k, s, r_i, r_A) := f(d, n, k, s, r_i, r_A) − inf f^(d)。
>
> 如前所述，我們的多數分析將侷限於各策略在各問題上的中位數表現。因此，我們的主要效能準則為
>
> M(f, d, n, k, s) = M({p(f, d, n, k, s, r_i, r_A) | r_i ∈ R_i(s), r_A ∈ [5]})，
>
> 其中 M 表示中位數，且約定 R_i(Halton) = {1}，而對其他抽樣設計 s ∈ S \ {Halton}，R_i(s) = {1, 2, . . . , 5} =: [5]。

---

> [!quote] Original
> **Virtual Best Solver and Relative Target Precision.** An important concept in comparing portfolios of algorithms is the virtual best solver (VBS). This VBS describes a hypothetical algorithm that for each problem (i.e., each (f, d, n) combination in our case) selects an algorithm A from a given portfolio A that achieves the best performance [32]. In our case, the algorithm portfolio is the collection of all 40 (k, s) combinations. As we consider median performance, the VBS is defined by selecting for each problem (f, d, n) the strategy (k, s) that achieved the best median function value. For notational convenience, we omit the explicit mention of the median and set
>
> VBS(f, d, n) := min{M(f, d, n, k, s) | s ∈ S, k ∈ K}.
>
> Fig. 1 shows which strategy defined the VBS for which problem(s). A first visual interpretation suggests that this data is relatively unstructured; we will come back to this point further below.
>
> By design, some of the BBOB functions are much "harder" than others, so that we see substantial differences in the target precision that can be achieved with a fixed budget n. To compensate for that in our aggregations, we will frequently study the relative performance of a strategy (k, s) compared to the VBS. To this end, we set
>
> R(f, d, n, k, s) := M(f, d, n, k, s)/VBS(f, d, n)
>
> and refer to R(f, d, n, k, s) as the relative target precision of strategy (k, s) on problem (f, d, n). Note that these values are at least one, where R(f, d, n, k, s) = 1 implies that strategy (k, s) achieved the best median target precision among all the 40 different strategies.
>
> [Figure 1: Overview of the virtual best solver (VBS), i.e., the strategy (k, s) that achieved the best median performance on the respective problem (f, d, n).]

> [!note] 翻譯
> **虛擬最佳求解器與相對目標精度。** 比較演算法組合（portfolio）時的一個重要概念是虛擬最佳求解器（virtual best solver, VBS）。VBS 描述一個假想的演算法：對每個問題（在本研究中即每個 (f, d, n) 組合），它從給定的演算法組合 A 中選出達到最佳表現的演算法 A [32]。在我們的情形中，演算法組合即是全部 40 個 (k, s) 組合的集合。由於我們考慮中位數表現，VBS 的定義為：對每個問題 (f, d, n)，選出取得最佳中位函數值的策略 (k, s)。為求符號簡潔，我們省略對中位數的明確標記，並設
>
> VBS(f, d, n) := min{M(f, d, n, k, s) | s ∈ S, k ∈ K}。
>
> 圖 1 顯示哪種策略在哪些問題上構成 VBS。初步的視覺判讀顯示這些資料相對缺乏結構；我們稍後將回到這一點。
>
> 依其設計，某些 BBOB 函數遠比其他函數「困難」，因此在固定預算 n 下可達成的目標精度存在顯著差異。為在聚合時補償此差異，我們將經常研究策略 (k, s) 相對於 VBS 的相對表現。為此，我們設
>
> R(f, d, n, k, s) := M(f, d, n, k, s)/VBS(f, d, n)，
>
> 並稱 R(f, d, n, k, s) 為策略 (k, s) 在問題 (f, d, n) 上的相對目標精度（relative target precision）。注意這些值至少為 1，且 R(f, d, n, k, s) = 1 意味著策略 (k, s) 在全部 40 種不同策略中取得了最佳的中位目標精度。
>
> [圖 1：虛擬最佳求解器（VBS）概覽，即在各問題 (f, d, n) 上取得最佳中位數表現的策略 (k, s)。]

---

## 4 Aggregated Results | 聚合結果

> [!quote] Original
> As shown in Fig. 1, it is not possible to derive simple rules that define which strategy achieves the best performance on each of the BBOB functions. In Fig. 2 we therefore count how often each strategy forms the VBS. Therein, we observe a clear advantage for Halton designs (it has the most "hits" for any given initial ratio except for k = 100%), and we further observe a clear tendency towards small initial ratios. However, we also see that each strategy "wins" at least one problem. Neither the simple counting statistics in Fig. 2 nor the more detailed overview in Fig. 1 provide any information about the magnitude of the advantage. We thus plot in Fig. 3 the distribution of the relative target precision R(f, d, n, k, s) of each strategy (k, s), aggregated again over all 720 problems. This plot confirms the tendency that spending a larger ratio of the total budget on generating the initial design results in worse overall performance. We also observe that although the Halton design generated with k = 10% of the total budget has the best median performance, the actual differences between the four designs are rather small.
>
> [Figure 2: Number of problems (f, d, n) for which the respective strategy forms the VBS.]
> [Figure 3: Boxplots of relative performances R(f, d, n, k, s) across all 600 problems (f, d, n) with budget n ≤ 256, shown for all 40 different strategies (k, s). The y-axis is capped at 10.]

> [!note] 翻譯
> 如圖 1 所示，我們無法導出簡單的規則來界定哪種策略在每個 BBOB 函數上表現最佳。因此，我們在圖 2 中統計每種策略成為 VBS 的次數。從中可觀察到 Halton 設計具有明顯優勢（除 k = 100% 外，在任一給定初始比例下其「命中」次數皆最多），並進一步觀察到明顯偏向小初始比例的趨勢。然而，我們也看到每種策略都至少「贏得」一個問題。圖 2 的簡單計數統計與圖 1 的較詳細概覽皆未提供任何關於優勢幅度的資訊。因此，我們在圖 3 中繪製每種策略 (k, s) 之相對目標精度 R(f, d, n, k, s) 的分布，同樣聚合於全部 720 個問題。此圖證實了以下趨勢：將總預算中較大比例花費於生成初始設計，會導致整體表現變差。我們也觀察到，儘管以總預算 k = 10% 生成的 Halton 設計具有最佳的中位數表現，四種設計之間的實際差異其實相當小。
>
> [圖 2：各策略構成 VBS 的問題 (f, d, n) 數量。]
> [圖 3：預算 n ≤ 256 之全部 600 個問題 (f, d, n) 上，40 種策略 (k, s) 之相對表現 R(f, d, n, k, s) 的箱形圖。y 軸截斷於 10。]

---

> [!quote] Original
> A more detailed picture about the relative performances is provided in Fig. 5. Here, we plot the median (over all 24 BBOB functions) relative performance; i.e., the value in each cell represents M({R(f, d, n, k, s) | f ∈ {1, 2, ..., 24}}) for the given dimension, budget, and strategy. We observe that in most cases the performance worsens with increasing initial budget ratio k, and this consistently for each problem dimension d and total budget n. The values in the rows labeled "Total" are the median values over all budgets (last row per dimension) and dimensions (bottom-most rows), respectively. Noticeably, the influence of the sampling design vanishes with increasing dimension – independent from the budget ratio. Aggregated over all dimensions, the differences between the designs are small, as already observed in Fig. 3.
>
> Remember that the values in Fig. 5 are always scaled by the VBS that is specific for problem (f, d, n), but independent from strategy (k, s). This implies that the rows are computed against the same VBS, but different rows compare against different strategies. Values in different rows should therefore only be compared with care.
>
> [Figure 5: Median (over all 24 BBOB functions) relative performance of R(f, d, n, k, s), by dimension and budget (rows) and strategy (k, s) (columns).]

> [!note] 翻譯
> 圖 5 提供了關於相對表現的更詳細圖景。此處我們繪製（對全部 24 個 BBOB 函數取）中位數的相對表現；亦即每個儲存格中的值代表給定維度、預算與策略下的 M({R(f, d, n, k, s) | f ∈ {1, 2, ..., 24}})。我們觀察到，在多數情況下，表現隨初始預算比例 k 的增加而變差，且此現象在每個問題維度 d 與總預算 n 下皆一致成立。標記為「Total」的列分別是對所有預算（每個維度的最後一列）與所有維度（最底部各列）取的中位數值。值得注意的是，抽樣設計的影響隨維度增加而消失——與預算比例無關。聚合所有維度後，各設計之間的差異很小，一如圖 3 所觀察到的。
>
> 請記住，圖 5 中的值始終以針對問題 (f, d, n) 而定、但與策略 (k, s) 無關的 VBS 進行縮放。這意味著同一列的值是以相同的 VBS 計算，但不同列所比較的對象策略不同，因此跨列比較數值時應格外謹慎。
>
> [圖 5：（對全部 24 個 BBOB 函數取）中位數的相對表現 R(f, d, n, k, s)，依維度與預算（列）及策略 (k, s)（欄）呈現。]

---

## 5 Performance by Function | 按函數分析表現

> [!quote] Original
> After having studied values that were aggregated across all 24 BBOB functions (see Section 4), we now take a closer look at the differences between the different strategies on each of the functions.
>
> **Influence of the Total Budget.** Fig. 4 reports the median target precision (shown on a log-scale) achieved by Halton and Sobol' designs with k = 10% initial budget, in dependence of function f and total budget n. The plot reveals the functions that are easy (e.g., functions 1, 21, 22) and difficult (functions 10 and 12) for SMBO. Note that the performance convergence is not always monotonically decreasing with increasing total budget size. This might result from the small number of repetitions (5 for the Halton design, 25 for Sobol'). However, the differences are fairly small. Fig. 6 extends Fig. 4 to all 40 strategies (k, s). That is, for each 5-dimensional problem (f, 5, n) a heatmap of the relative performances R(f, d, n, k, s) is shown for all pairs of sampling design s and initial design ratio k. We observe that, in particular for functions 15, 19, 23 and 24, the differences between the different initial budgets are comparatively small. This likely results from the functions' highly multimodal landscapes, which hinder SMBO from training reasonable surrogates.
>
> [Figure 4: Logarithmic median target precision log10(M(f, d, n, k, s)) depending on the total budget. Results are shown for Halton (left) and Sobol (right) designs with an initial budget of 10% of the total budget and across all 5-dimensional BBOB functions. Gray boxes are due to missing data.]
> [Figure 6: Heatmap visualization of relative performances R(f, d, n, k, s) by function, total budget, and strategy (k, s) for fixed dimension d = 5. Values are capped at 3.]

> [!note] 翻譯
> 在研究了跨全部 24 個 BBOB 函數聚合的數值之後（見第 4 節），我們現在更仔細地檢視不同策略在各函數上的差異。
>
> **總預算的影響。** 圖 4 報告了 Halton 與 Sobol' 設計在初始預算 k = 10% 下達成的中位目標精度（以對數尺度顯示），依函數 f 與總預算 n 而變。該圖揭示了對 SMBO 而言容易的函數（如函數 1、21、22）與困難的函數（函數 10 與 12）。注意，表現的收斂並非總是隨總預算規模的增加而單調下降，這可能源於重複次數較少（Halton 設計為 5 次，Sobol' 為 25 次）；不過差異相當小。圖 6 將圖 4 擴展至全部 40 種策略 (k, s)：對每個 5 維問題 (f, 5, n)，以熱圖呈現所有抽樣設計 s 與初始設計比例 k 之組合的相對表現 R(f, d, n, k, s)。我們觀察到，尤其對函數 15、19、23 與 24 而言，不同初始預算之間的差異相對較小。這很可能源於這些函數高度多模態的地景，使 SMBO 難以訓練出合理的代理模型。
>
> [圖 4：中位目標精度的對數 log10(M(f, d, n, k, s)) 隨總預算的變化。結果為初始預算佔總預算 10% 的 Halton（左）與 Sobol（右）設計，涵蓋所有 5 維 BBOB 函數。灰色方格表示缺失資料。]
> [圖 6：固定維度 d = 5 下，依函數、總預算與策略 (k, s) 呈現之相對表現 R(f, d, n, k, s) 熱圖。數值截斷於 3。]

---

> [!quote] Original
> **Influence of the initial sample size k and design s.** Fig. 7 shows the relative median target precision R(f, d, n, k, s) for all 24 BBOB functions, for a fixed budget of 128 function evaluations and variable dimension (columns) and strategies (rows). We recall that the VBS is defined per column, i.e., each column has at least one strategy with R(f, d, n, k, s) = 1 (see Fig. 1).
>
> We observe that the benefit of small initial budgets is important for functions with at most medium-sized indices. This finding is very plausible, as the first 14 functions mainly are separable and/or unimodal – i.e., functions whose structure can be well exploited by SMBO. However, for the group of multimodal functions (IDs 15 to 24), with the notable exception of functions 21 and 22, the differences between the different initial ratios are rather small, indicating that SMBO does not perform much better than (quasi-)random sampling in the initial phases of the optimization process.
>
> [Figure 7: Heatmap visualization of the relative performance R(f, d, n, k, s) by dimension, function, and design type for a fixed total budget of n = 128 function evaluations. Values are capped at 3.]

> [!note] 翻譯
> **初始樣本規模 k 與設計 s 的影響。** 圖 7 顯示在固定 128 次函數評估預算下，全部 24 個 BBOB 函數的相對中位目標精度 R(f, d, n, k, s)，維度（欄）與策略（列）為變數。我們重申 VBS 是按欄定義的，亦即每一欄至少有一個策略滿足 R(f, d, n, k, s) = 1（見圖 1）。
>
> 我們觀察到，小初始預算的效益對編號至多為中等的函數十分重要。此發現相當合理，因為前 14 個函數主要為可分離且／或單模態函數——即結構能被 SMBO 妥善利用的函數。然而，對於多模態函數群（編號 15 至 24），除函數 21 與 22 這兩個明顯例外之外，不同初始比例之間的差異相當小，顯示在最佳化過程的初始階段，SMBO 的表現並不比（準）隨機抽樣好多少。
>
> [圖 7：固定總預算 n = 128 次函數評估下，依維度、函數與設計類型呈現之相對表現 R(f, d, n, k, s) 熱圖。數值截斷於 3。]

---

> [!quote] Original
> We also see interesting cases in which larger ratios of initial budget result even in better performance than small initial ratios. An extreme case is function 12 in dimension d = 2. Its situation is as follows. The VBS is defined by the (30%, Halton) strategy. The differences between the Halton designs with k > 30% are rather small, whereas for the other strategies smaller initial budgets are preferable. By studying the absolute values in more detail, we find that the Halton strategy identifies a point with absolute target precision 24.9 when k ≥ 30%. SMBO does not manage to find a better point in any of its 128 − ⌈30% · 128⌉ = 89 adaptive evaluations. The best median target precision of any of the other strategies has target precision 58.6 – achieved by the (10%, LHS) strategy. Looking further into the results of the 800 individual runs, we find that 126 of them find a point of target precision smaller than 24.9. The distribution of their initial ratios is not unanimous, as can be seen in the following table, which counts how often each initial ratio k appears among these 126 runs. These results show how difficult it is to give a general advice for the optimization of this function – even when the budget is fixed and the function ID known.
>
> | k | 0.1 | 0.2 | 0.3 | 0.4 | 0.5 | 0.6 | 0.7 | 0.8 | 0.9 | 1 |
> |---|-----|-----|-----|-----|-----|-----|-----|-----|-----|---|
> | # | 14  | 12  | 8   | 13  | 21  | 7   | 12  | 14  | 10  | 15 |

> [!note] 翻譯
> 我們也看到一些有趣的情形：較大的初始預算比例甚至帶來比小初始比例更好的表現。一個極端案例是維度 d = 2 的函數 12，其情況如下。VBS 由 (30%, Halton) 策略構成；k > 30% 的各 Halton 設計之間差異相當小，而其他策略則以較小的初始預算為佳。更詳細地研究絕對數值後，我們發現當 k ≥ 30% 時，Halton 策略找到一個絕對目標精度為 24.9 的點；SMBO 在其 128 − ⌈30% · 128⌉ = 89 次自適應評估中皆未能找到更佳的點。其他策略的最佳中位目標精度為 58.6——由 (10%, LHS) 策略達成。進一步檢視 800 次個別執行的結果，我們發現其中 126 次找到了目標精度小於 24.9 的點。這些執行的初始比例分布並不一致，可見於下表，其統計了這 126 次執行中各初始比例 k 出現的次數。這些結果顯示，即使預算固定、函數編號已知，要為此函數的最佳化提供一般性建議仍是何等困難。
>
> | k | 0.1 | 0.2 | 0.3 | 0.4 | 0.5 | 0.6 | 0.7 | 0.8 | 0.9 | 1 |
> |---|-----|-----|-----|-----|-----|-----|-----|-----|-----|---|
> | # | 14  | 12  | 8   | 13  | 21  | 7   | 12  | 14  | 10  | 15 |

---

## 6 Restarts vs. Long Runs | 重啟與長跑之比較

> [!quote] Original
> In the previous paragraph, we have started to look into the distribution of the target precisions. We now demonstrate how such information can be used to study whether it is beneficial to use the total budget of n function evaluations for a single long run, or whether one should rather start two shorter runs of budget n/2 each, or four runs of budget n/4, etc.
>
> **Distribution of the Target Precisions.** Crucial for the consideration of restarts are the distributions of the function values (or, equivalently, the distributions of the target precisions) achieved by the different strategies (k, s). For reasons of space, we cannot go in much detail here, but Fig. 8 demonstrates how these boxplots look like. Note that this figure is for one specific combination of function (f = 17), dimension (d = 5) and budget (n = 128). It aggregates the target precision of all 40 strategies, i.e., of 800 runs in total. Our data base contains one such plot for each of the 720 (f, d, n) problems.
>
> Note that the dispersion of Halton designs are smaller, but this is due to the fact that we do not perform resampling for this sequence. For all pairs of (k, Halton) strategies with k ≥ 50% the target precision of the best initial design point is slightly above 3. For k ≥ 80% none of the SMBO runs starting in this best initial design point finds a solution of better target precision. For k ∈ {60%, 70%}, only one of the five runs each finds a better solution. Note that the length of each of these SMBO runs is n − ⌈k · n⌉, which for k = 0.6 corresponds to 51 adaptive SMBO steps. Such detailed information could be very useful to identify weaknesses of the EGO approach, and, hopefully, contribute towards better SMBO designs.
>
> [Figure 8: Boxplots of the target precisions p(17, 5, 128, k, s, r_i, r_A)-values for function f = 17, dimension 5, and total budget n = 128, grouped by initial budget ratio k and design s.]

> [!note] 翻譯
> 在前一段中，我們已開始檢視目標精度的分布。現在我們展示如何利用此類資訊來研究：將 n 次函數評估的總預算用於單次長跑是否有利，抑或應改為啟動兩次各具預算 n/2 的較短執行、或四次預算 n/4 的執行，依此類推。
>
> **目標精度的分布。** 考量重啟策略時，關鍵在於不同策略 (k, s) 所達成之函數值的分布（或等價地，目標精度的分布）。限於篇幅，此處無法深入細節，但圖 8 展示了這些箱形圖的樣貌。注意此圖對應函數（f = 17）、維度（d = 5）與預算（n = 128）的一個特定組合，聚合了全部 40 種策略的目標精度，即總計 800 次執行。我們的資料庫為 720 個 (f, d, n) 問題中的每一個都包含一張此類圖。
>
> 注意 Halton 設計的離散度較小，但這是因為我們未對此序列進行重抽樣。對於所有 k ≥ 50% 的 (k, Halton) 策略組合，最佳初始設計點的目標精度略高於 3。當 k ≥ 80% 時，自此最佳初始設計點出發的 SMBO 執行中，沒有任何一次找到目標精度更佳的解；當 k ∈ {60%, 70%} 時，各自的五次執行中僅有一次找到更佳的解。注意這些 SMBO 執行各自的長度為 n − ⌈k · n⌉，在 k = 0.6 時對應 51 個自適應 SMBO 步驟。此類詳細資訊對於識別 EGO 方法的弱點可能極為有用，並有望有助於發展更佳的 SMBO 設計。
>
> [圖 8：函數 f = 17、維度 5、總預算 n = 128 之目標精度 p(17, 5, 128, k, s, r_i, r_A) 的箱形圖，依初始預算比例 k 與設計 s 分組。]

---

> [!quote] Original
> **Computing median target precision of restarting SMBO.** To investigate if, for a given problem (f, d, n), a restart strategy is beneficial over a single long run, we need to extend our previous focus on median target precision to different percentiles. To this end, let
>
> Pq(f, d, n, k, s) := Pq({p(f, d, n, k, s, r_i, r_A) | r_i ∈ R_i(s), r_A ∈ [5]}),
>
> the q-th percentile of the target precisions achieved by strategy (k, s) on problem (f, d, n) across all 5 (Halton) or 25 (Sobol', LHS, uniform designs) runs, respectively. For a fair comparison of one run of the full budget n with two runs of budget n/2 (of the same strategy), we compare the median M(f, d, n, k, s) (i.e., the 50-th percentile) with the q := 1 − √(1/2)-th percentile Pq(f, d, n/2, k, s). With this value of q, the probability that (at least) one of the two runs achieves a target precision that is at least as good as Pq(f, d, n/2, k, s) equals 1 − (1 − q)² = 1/2. This is identical to the probability that one long run achieves a target precision that is at least as good as M(f, d, n, k, s). Note that we disregard a small bias in our data, which results from the fact that we do not have 25 completely independent runs. Instead, we use the same initial design sample for five independent SMBO runs each – but, we ignore this effect in the following computations. Also, given the small number of runs, all numbers should be taken with care – the smaller the percentile, the larger the uncertainty around the values. We nevertheless show this example to demonstrate how one could systematically address the question how to split a given budget into possibly parallel runs.

> [!note] 翻譯
> **計算重啟式 SMBO 的中位目標精度。** 為探究對給定問題 (f, d, n) 而言，重啟策略是否優於單次長跑，我們需將先前對中位目標精度的關注擴展至不同的百分位數。為此，令
>
> Pq(f, d, n, k, s) := Pq({p(f, d, n, k, s, r_i, r_A) | r_i ∈ R_i(s), r_A ∈ [5]})，
>
> 即策略 (k, s) 在問題 (f, d, n) 上，跨全部 5 次（Halton）或 25 次（Sobol'、LHS、均勻設計）執行所達成之目標精度的第 q 百分位數。為公平比較「一次使用完整預算 n 的執行」與「兩次各具預算 n/2 的執行」（同一策略），我們將中位數 M(f, d, n, k, s)（即第 50 百分位數）與第 q := 1 − √(1/2) 百分位數 Pq(f, d, n/2, k, s) 進行比較。在此 q 值下，兩次執行中（至少）一次達成不劣於 Pq(f, d, n/2, k, s) 之目標精度的機率等於 1 − (1 − q)² = 1/2，這與單次長跑達成不劣於 M(f, d, n, k, s) 之目標精度的機率相同。注意，我們忽略了資料中的一個小偏差：我們並沒有 25 次完全獨立的執行，而是對每個初始設計樣本各執行五次獨立的 SMBO——但在以下計算中我們忽略此效應。此外，鑑於執行次數不多，所有數字皆應審慎看待——百分位數越小，數值周圍的不確定性越大。儘管如此，我們仍展示此例，以說明如何系統性地處理「如何將給定預算切分為可能平行之多次執行」的問題。

---

> [!quote] Original
> Fig. 9 illustrates an example for the relevant percentiles when comparing one long run of budget n with two short ones of budget n/2, and four even shorter ones of budget n/4. More precisely, we fix in this figure the strategy to (10%, LHS) and the dimension to d = 5, and we show log-scaled relative data. Each 3 × 6 box corresponds to one of the 24 BBOB functions. As we scale the values within a box by its VBS, and afterwards show the percentile ratios on a log-scale, the field with value 0.0 represents the combination achieving the best target precision (i.e., the VBS) among the displayed combinations. Not surprisingly, for most functions this is the (1 − ⁴√(1/2))-th percentile of the full budget n = 512. Let P*_f be the target precision of this (percentile, budget) combination for a given function f. A value φ in field (q, n) is then to be read as follows: the target precision Pq(n) := Pq(f, d = 5, n, 10%, LHS) satisfies Pq(n) = 10^φ · P*_f. Smaller values are therefore better. We see that for f = 5, for example, our data suggests that a total budget of 512 evaluations (value 0.4 when used as single run) is better used for four runs of budget 128 each (value 0.1). We have marked in this matrix all fields for which the long run compares unfavorably with a restart strategy – the one corresponding to the neighboring field on the lower left diagonal. Overall, we see that several such cases exist, which confirms our previous finding that EGO does not always compare favorably against quasi-random sampling.
>
> [Figure 9: Percentiles Pq(f, d, n, k, s) of target precisions across the 25 SMBO runs per function and dimension using an LHS design with 10% initial budget. The percentiles are scaled by the respective function's best percentile, and the resulting ratios are shown on a capped log10-scale. Red boxes indicate that the corresponding strategy performs unfavorably against a restart strategy (the one to the lower left).]

> [!note] 翻譯
> 圖 9 舉例說明了比較「一次預算 n 的長跑」、「兩次預算 n/2 的短跑」與「四次預算 n/4 的更短執行」時的相關百分位數。更精確地說，此圖中我們將策略固定為 (10%, LHS)、維度固定為 d = 5，並顯示對數尺度的相對數據。每個 3 × 6 的方塊對應 24 個 BBOB 函數之一。由於方塊內的數值以其 VBS 縮放、再以對數尺度顯示百分位數比值，數值為 0.0 的欄位即代表在所顯示的組合中達到最佳目標精度（即 VBS）的組合。不出所料，對多數函數而言，這是完整預算 n = 512 的第 (1 − ⁴√(1/2)) 百分位數。令 P*_f 為給定函數 f 下此（百分位數, 預算）組合的目標精度。欄位 (q, n) 中的值 φ 應如下解讀：目標精度 Pq(n) := Pq(f, d = 5, n, 10%, LHS) 滿足 Pq(n) = 10^φ · P*_f。因此數值越小越好。例如對 f = 5，我們的資料顯示：512 次評估的總預算（作為單次執行時數值為 0.4）更適合用於四次各 128 預算的執行（數值 0.1）。我們在此矩陣中標記了所有「長跑相較於重啟策略（即左下對角相鄰欄位對應者）表現不利」的欄位。整體而言，我們看到存在多個此類情形，這證實了我們先前的發現：EGO 並非總是優於準隨機抽樣。
>
> [圖 9：使用初始預算 10% 之 LHS 設計時，每個函數與維度下 25 次 SMBO 執行之目標精度的百分位數 Pq(f, d, n, k, s)。百分位數以各函數的最佳百分位數縮放，所得比值以截斷的 log10 尺度顯示。紅框表示對應策略相較於重啟策略（左下方欄位對應者）表現不利。]

---

## 7 Conclusions | 結論

> [!quote] Original
> In this paper we have presented a database for data-driven investigations of the sequential model-based optimization (SMBO) strategy EGO [30]. The focus of our work is on analyzing the influence of the (size and type of) initial design on the overall performance of EGO. Our data base contains data for 720 different problems, which are evaluated against a total of 40 different initial design strategies.
>
> While we clearly observed that small initial designs are preferable at a high-level view, we also found that each of the 40 considered combinations of design type and size achieved best performance on at least one of the 720 problems. Our findings thus confirm that an automated strategy selection method – like the proof-of-concept approach presented in [47] – might indeed be profitable. Moreover, we even identified cases in which the usage of EGO does not provide any benefits over the initial (quasi-)random sample – especially in case of highly multimodal problems.

> [!note] 翻譯
> 本文提出了一個資料庫，用於對序貫模型式最佳化（SMBO）策略 EGO [30] 進行資料驅動的研究。本研究的焦點在於分析初始設計（的規模與類型）對 EGO 整體表現的影響。我們的資料庫包含 720 個不同問題的資料，並針對總計 40 種不同的初始設計策略進行評估。
>
> 雖然我們在宏觀層面清楚觀察到小初始設計較為可取，但我們也發現，所考慮的 40 種設計類型與規模組合中，每一種都至少在 720 個問題之一上取得最佳表現。我們的發現因此證實：自動化的策略選擇方法——例如 [47] 所提出的概念驗證做法——可能確實有利可圖。此外，我們甚至找出了 EGO 的使用相較初始（準）隨機樣本毫無益處的情形——尤其是在高度多模態的問題上。

---

> [!quote] Original
> Our long-term vision are SMBO approaches that dynamically decide whether to take the next sample from a (quasi-)random distribution or whether to derive it from the surrogate model. Going one step further, we believe that an adaptive choice of the acquisition function, and possibly even of the solver used to optimize the latter, should bring substantial performance gains – in particular in the case in which the total budget is known in advance. Hence, we need to "train" a final recommendation (last evaluation) instead of achieving good anytime performance. These two mentioned questions fall under the umbrella of dynamic algorithm configuration, which has been an important driver for the field of evolutionary computation for the last decades [12, 16, 17, 31], and which has recently also gained interest in machine learning communities [5].
>
> Typically, the budget of common SMBO applications is too small for a classical a priori (i.e., offline) landscape-aware selection of the optimizer design based on supervised learning approaches (see [32] for a survey). However, in case high-level properties – such as the degree of (multi-)modality or the sizes of the problem's attraction basins – are known for the problem at hand, or can be guessed by an expert, selecting a suitable initial design strategy is feasible.
>
> Finally, we have seen that the performance of the different designs was often quite comparable. To investigate the differences in more detail, we suggest to consider the different strategies as a portfolio of different algorithms. With this viewpoint, one could analyze the marginal contributions [51] or Shapley values [20] of the different designs, and leverage the information contained therein.

> [!note] 翻譯
> 我們的長期願景是這樣的 SMBO 方法：動態決定下一個樣本應取自（準）隨機分布，還是應由代理模型導出。更進一步，我們相信對採集函數——甚至可能包括用於最佳化採集函數的求解器——進行自適應選擇，應能帶來可觀的效能增益，尤其是在總預算事先已知的情況下。因此，我們需要「訓練」的是最終推薦（最後一次評估），而非追求良好的任意時間表現。上述兩個問題皆屬動態演算法組態（dynamic algorithm configuration）的範疇，該領域在過去數十年間一直是演化計算領域的重要驅動力 [12, 16, 17, 31]，近來亦引起機器學習社群的興趣 [5]。
>
> 通常，常見 SMBO 應用的預算太小，不足以支持基於監督式學習方法、事前（即離線）進行的地景感知（landscape-aware）最佳化器設計選擇（相關綜述見 [32]）。然而，若手邊問題的高階性質——如（多）模態程度或問題吸引域（attraction basins）的大小——為已知，或可由專家推測，則選擇合適的初始設計策略是可行的。
>
> 最後，我們已看到不同設計的表現往往相當接近。為更詳細地探究其差異，我們建議將不同策略視為由不同演算法組成的組合（portfolio）。以此觀點，便可分析各設計的邊際貢獻 [51] 或 Shapley 值 [20]，並善用其中蘊含的資訊。

---

## Acknowledgments | 致謝

> [!quote] Original
> This work was supported by the Paris Ile-de-France Region and the European Research Center for Information Systems (ERCIS).

> [!note] 翻譯
> 本研究由巴黎法蘭西島大區（Paris Ile-de-France Region）與歐洲資訊系統研究中心（ERCIS）支持。

---

## References | 參考文獻

> [!note] 翻譯
> References omitted / 參考文獻略。
