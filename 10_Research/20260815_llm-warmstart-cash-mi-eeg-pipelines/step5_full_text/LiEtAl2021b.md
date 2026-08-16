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
---

# VolcanoML: Speeding up End-to-End AutoML via Scalable Search Space Decomposition

VolcanoML: Speeding up End-to-End AutoML via
                                                                   Scalable Search Space Decomposition
                                                                                                            [Scalable Data Science]

                                                                Yang Li†‡ , Yu Shen† , Wentao Zhang† , Jiawei Jiang‡ , Bolin Ding§ , Yaliang Li§
                                                                       Jingren Zhou§ , Zhi Yang† , Wentao Wu∗ , Ce Zhang‡ , Bin Cui†
                                                                   † EECS, Peking University ‡ ETH Zurich § Alibaba Group ∗ Microsoft Research, Redmond
                                                        † {liyang.cs, shenyu, wentao.zhang, yangzhi, bin.cui}@pku.edu.cn ‡ {jiawei.jiang, ce.zhang}@inf.ethz.ch
                                                                     § {bolin.ding, yaliang.li, jingren.zhou}@alibaba-inc.com ∗ wentao.wu@microsoft.com

arXiv:2107.08861v2 [cs.LG] 20 Jul 2021
                                         ABSTRACT                                                                                   Snorkel [59], ZeroER [77], TFX [6, 51], “Query 2.0” [78], Kryp-
                                         End-to-end AutoML has attracted intensive interests from both                              ton [54], Cerebro [55], ModelDB [72], MLFlow [80], DeepDive [81],
                                         academia and industry, which automatically searches for ML                                 HoloClean [60], ActiveClean [40], and NorthStar [39]. End-to-end
                                         pipelines in a space induced by feature engineering, algorithm/-                           AutoML systems [26, 79, 82] have been an emerging type of sys-
                                         model selection, and hyper-parameter tuning. Existing AutoML                               tems that has significantly raised the level of abstractions of build-
                                         systems, however, suffer from scalability issues when applying to                          ing ML applications. Given an input dataset and a user-defined
                                         application domains with large, high-dimensional search spaces.                            utility metric (e.g., validation accuracy), these systems automate
                                         We present VolcanoML, a scalable and extensible framework that                             the search of an end-to-end ML pipeline, including feature en-
                                         facilitates systematic exploration of large AutoML search spaces.                          gineering, algorithm/model selection, and hyper-parameter tuning.
                                         VolcanoML introduces and implements basic building blocks that                             Open-source examples include auto-sklearn [15], TPOT [57], and
                                         decompose a large search space into smaller ones, and allows users                         hyperopt-sklearn [38], whereas most cloud service providers,
                                         to utilize these building blocks to compose an execution plan for the                      e.g., Google, Microsoft, Amazon, Alibaba, etc., all provide their pro-
                                         AutoML problem at hand. VolcanoML further supports a Volcano-                              prietary services on the cloud. As machine learning has become
                                         style execution model – akin to the one supported by modern data-                          an increasingly indispensable functionality integrated in modern
                                         base systems – to execute the plan constructed. Our evaluation                             data (management) systems, an efficient and effective end-to-end
                                         demonstrates that, not only does VolcanoML raise the level of                              AutoML component also becomes increasingly important.
                                         expressiveness for search space decomposition in AutoML, it also                              End-to-end AutoML provides a powerful abstraction to automati-
                                         leads to actual findings of decomposition strategies that are signif-                      cally navigate and search in a given complex search space. However,
                                         icantly more efficient than the ones employed by state-of-the-art                          in our experience of applying state-of-the-art end-to-end AutoML
                                         AutoML systems such as auto-sklearn.                                                       systems in a range of real-world applications, we find that such
                                                                                                                                    a system running “fully automatically” is rarely enough — often,
                                         PVLDB Reference Format:                                                                    developing a successful ML application involves multiple iterations
                                         Yang Li, Yu Shen, Wentao Zhang, Jiawei Jiang, Bolin Ding, Yaliang Li,                      between a user and an AutoML system to iteratively improve the
                                         Jingren Zhou, Zhi Yang, Wentao Wu, Ce Zhang, and Bin Cui. VolcanoML:                       resulting ML artifact.
                                         Speeding up End-to-End AutoML via Scalable Search Space Decomposition.
                                                                                                                                       Motivating Practical Challenge. One such type of interac-
                                         PVLDB, 14(11): XXX-XXX, 2021.
                                                                                                                                    tion, which inspires this work, is the enrichment of search space.
                                         doi:10.14778/3476249.3476270
                                                                                                                                    We observe that the default search space provided by state-of-the-
                                         PVLDB Availability Tag:                                                                    art AutoML systems is often not enough in many applications. This
                                         The source code of this research paper has been made publicly available at                 was not obvious to us at all in the beginning and it is not until
                                         https://github.com/PKU-DAIR/mindware.                                                      we finish building a range of real-world applications that we real-
                                                                                                                                    ize this via a set of concrete examples. For example, in one of our
                                         1    INTRODUCTION                                                                          astronomy applications [62], the feature normalization function
                                         In recent years, researchers in the database community have                                is domain-specific and not supported by most, if not all, AutoML
                                         been working on raising the level of abstractions of machine                               systems. Similar examples can also be found when searching for
                                         learning (ML) and integrating such functionality into today’s                              suitable ML models via AutoML. In one of our meteorology applica-
                                         data management systems, e.g., SystemML [18], SystemDS [5],                                tions, we need to extend the models with meteorology-specific loss
                                                                                                                                    functions. We saw similar problems when we tried to extend exist-
                                         This work is licensed under the Creative Commons BY-NC-ND 4.0 International                ing AutoML systems with pre-trained feature embeddings coming
                                         License. Visit https://creativecommons.org/licenses/by-nc-nd/4.0/ to view a copy of        from TensorFlow Hub, to include newly arXiv’ed models to enrich
                                         this license. For any use beyond those covered by this license, obtain permission by
                                         emailing info@vldb.org. Copyright is held by the owner/author(s). Publication rights       the “Model Base” [44], or to support Cosine annealing as for tuning.
                                         licensed to the VLDB Endowment.                                                               Technical Challenge: Scalability over the Search Space.
                                         Proceedings of the VLDB Endowment, Vol. 14, No. 11 ISSN 2150-8097.
                                         doi:10.14778/3476249.3476270                                                               “Why is it hard to extend the search space, as a user, in an end-to-end
                                                                                                                                    AutoML system?” The answer to this question is a complex one
                                                                                                                                1

                                                                                                                                                  Li et al.

that is not completely technical: some aspects are less technical                 best of auto-sklearn and TPOT on a majority of tasks; (2) using an
such as engineering decisions and UX designs, however, there are                  enriched search space with additional feature engineering operators,
also more technically fundamental aspects. An end-to-end AutoML                   VolcanoML performs significantly better than auto-sklearn;
system contains an optimization algorithm that navigates a joint                  and (3) using an enriched search space with an additional data prosearch space induced by feature engineering, algorithm selection,                 cessing stage and functionalities beyond what auto-sklearn and
and hyper-parameter tuning. Because of this joint nature, the search              TPOT currently support (i.e., an additional embedding selection
space of end-to-end AutoML is complex and huge while the enrich-                  stage using pre-trained models on TensorFlow Hub), VolcanoML
ment is only going to make it even larger. As we will see, handling               can deal with input types such as images efficiently.
such a huge space is already challenging for existing systems, and
                                                                                     Moving Forward. The VolcanoML abstraction enables a strucfurther enriching it will make it even harder to scale.
                                                                                  tured view of optimizing a black-box function via decomposition.
   Many existing systems such as
                                                                                  This structured view itself opens up interesting future directions.
auto-sklearn [15] and TPOT [57]                           Joint Space
                                                   (Algorithm, Feature, HP)       For example, one may wish to automatically decompose a search
deal with the entire composite search
                                                                                  space given a workload, just like what a classic query optimizer
space jointly, which naturally leads to Strategy 1 Strategy 2 Strategy 3
                                                                                  would do for relational queries. For constrained optimizations, we
the scalability bottleneck. Decompos- Algorithm Algorithm Feature ...
                                                                                  also imagine techniques similar to traditional “push-down selection”
ing a joint space has been explored for Feature, HP Feature Alg, HP
                                                                                  could be applied in a similar spirit. We explore the possibility of
some subspaces (e.g., only algorithm                      HP
                                                                                  automatically searching for the best plan in Section 4 and discuss
and hyper-parameters as in [45, 50]),
                                                                                  the limitations of this simple strategy and the exciting line of future
however, none of them has been applied to a search space as large
                                                                                  work that could follow. While the full treatment of these aspects
as that of end-to-end AutoML. One challenge is that there exist
                                                                                  are beyond the scope of this paper, we hope the VolcanoML abmany different ways to decompose the same space, as shown above,
                                                                                  straction can serve as a foundation for these future endeavors.
but only some of them can perform well. Without a structured, highlevel abstraction for search space decomposition to explore different             2   RELATED WORK
strategies, it is very hard to scale up an end-to-end AutoML system to
accommodate the search space that will only get larger in the future.             AutoML is a topic that has been intensively studied over the last
                                                                                  decade. We briefly summarize related work in this section and
   Summary of Technical Contributions. In this paper, we fo-                      readers can consult latest surveys [22, 26, 79, 82] for more details.
cus on designing a system, VolcanoML, which is scalable to a large
search space. Our technical contributions are as follows.                            End-to-End AutoML. End-to-end AutoML, the focus of this
                                                                                  work, aims to automate the development process of the end-to-
   C1. System Design: A Structured View on Decomposition. The main                end ML pipeline, including feature preprocessing, feature engitechnical contribution of VolcanoML is to provide a flexible and                  neering, algorithm selection, and hyper-parameter tuning. Often,
principled way of decomposing a large search space into multiple                  this is modeled as a black-box optimization problem [27] and
smaller ones. We propose a novel system abstraction: a set of Vol-                solved jointly [15, 57, 68]. Apart from grid search and random
canoML building blocks (Section 3), each of which takes charge of                 search [3], genetic programming [52, 57] and Bayesian optimization
a smaller sub-search space whereas a VolcanoML execution plan                     (BO) [4, 13, 25, 64, 65] has become prevailing frameworks for this
(Section 4) consists of a tree of such building blocks — the root node            problem. One challenge of end-to-end AutoML is the staggeringly
corresponds to the original search space and its child nodes corre-               huge search space that one has to support and many of these methspond to different subspaces. Under this abstraction, optimizing in               ods suffer from scalability issues. In addition, meta-learning [70]
the joint space is conducted as optimization problems over different              systematically investigates the interactions that different ML apsmaller subspaces. The execution model is similar to the classic                  proaches perform on a wide range of learning tasks, and then learns
“Volcano” query evaluation model in a relational database [17] (thus              from this experience, to accomplish new tasks much faster. Several
the name VolcanoML): The system asks the root node to take one                    meta-learning approaches [9, 15, 24, 69] can guide ML practitioners
iteration in the optimization process, which recursively invokes                  to design better search spaces for AutoML tasks.
one of its child nodes to take one iteration on solving a smaller-                   Many end-to-end AutoML systems have raised the abstracscale optimization problem over its own subspace; this recursive                  tion level of ML. auto-weka [68], hyperopt-sklearn [38] and
invocation procedure will continue until a leaf node is reached.                  auto-sklearn [15] are the main representatives of BO-based Au-
This flexible abstraction allows us to explore different ways that                toML systems. auto-sklearn is one of the most popular openthe same joint space can be decomposed. Together with a range of                  source framework. TPOT [57] and ML-Plan [52] use genetic algoadditional optimizations (Section 4), VolcanoML can often support                 rithm and hierarchical task networks planning respectively to opmore scalable search process than the existing AutoML systems                     timize over the pipeline space, and require discretization of the
such as auto-sklearn and TPOT.                                                    hyper-parameter space. AlphaD3M [11] integrates reinforcement
   C2. Large-scale Empirical Evaluations. We conducted intensive                  learning with Monte Carlo tree search (MCTS) to solve AutoML
empirical evalutions, comparing VolcanoML with state-of-the-                      problems but without imposing efficient decomposition over hyperart systems including auto-sklearn and TPOT. We show that (1)                     parameters and algorithm selection. AutoStacker [8] focuses on
under the same search space as auto-sklearn, VolcanoML signif-                    ensembling and cascading to generate complex pipelines, and solves
icantly outperforms auto-sklearn and TPOT — over 30 classifica-                   the CASH problem [15] via random search. Furthermore, a growtion tasks and 20 regression tasks — VolcanoML outperforms the                    ing number of commercial enterprises also export their AutoML
                                                                              2

VolcanoML: Speeding up End-to-End AutoML via Scalable Search Space Decomposition

services to their users, e.g., H2O [41], Microsoft’s Azure Machine                 to consider a diverse range of possible ML algorithms. Tak-
Learning [2], Google’s Prediction API [20], Amazon Machine Learn-                  ing auto-sklearn as an example, the search space for ML aling [49] and IBM’s Watson Studio AutoAI [28].                                      gorithms contains Linear_Model, Support_Vector_Machine,
                                                                                   Discriminant_Analysis, Random_Forest, etc.
    Automating Individual Components. Apart from end-to-end AutoML, many efforts have been devoted to studying sub-problems                         Hyper-parameters. Each ML algorithm has its own sub-search
in AutoML: (1) feature engineering [33–36, 56], (2) algorithm                      space for hyper-parameter tuning — if we choose to use a cerselection [12, 15, 38, 45, 50, 68], and (3) hyper-parameter tun-                   tain ML algorithm, we also have to specify the corresponding
ing [4, 14, 23, 25, 29, 31, 37, 43, 47, 58, 63, 65, 66, 76]. Meta-learning         hyper-parameters. The hyper-parameters fall into three categories:
methods [16, 19, 74] for hyper-parameter tuning can leverage aux-                  continuous (e.g., sub-sample_rate for Random_Forest), discrete
iliary knowledge acquired from previous tasks to achieve faster                    (e.g., maximal_depth for Decision_Tree), and categorical (e.g.,
optimization. Several systems offer a subset of functionalities in                 kernel_type for Lib_SVM).
the end-to-end process. Microsoft’s NNI [61] helps users to au-                       If the system makes a concrete pick for each of the above decitomate feature engineering, hyper-parameter tuning, and model                      sions, then it can compose a concrete ML pipeline and evaluate its
compression. Recent work [50] leverages the ADMM optimization                      utility. This is often an expensive process since it involves training
framework to decompose the CASH problem [15], and solves two                       an ML model. To find the optimal ML pipeline, the system evaluates
easier sub-problems. Berkeley’s Ray project [53] provides the tune                 the utility of different ML pipelines in an iterative manner following
module [48] to support scalable hyper-parameter tuning tasks in a                  a search strategy, and picks the one that maximizes the utility.
distributed environment. Featuretools [32] is a Python library for                    For example, auto-sklearn handles the above search space
automatic feature engineering. Unlike these works, in this paper, we               jointly and optimizes it with Bayesian optimization (BO) [64]. Given
focus on deriving an end-to-end solution to the AutoML problem,                    an initial set of function evaluations, BO proceeds by fitting a surrowhere the sub-problems are solved in a joint manner.                               gate model to those observations, specifically a probabilistic Random
                                                                                   Forest in auto-sklearn, and then chooses which ML pipeline to
3     VOLCANOML AND BUILDING BLOCKS                                                evaluate from the search space by optimizing an acquisition func-
                                                                                   tion that balances exploration and exploitation.
The goal of VolcanoML is to enable scalability with respect to the
underlying AutoML search space. As a result, its design focuses on                 3.2    Building Blocks
the decomposition of a given search space. In this section, we first               Unlike auto-sklearn, VolcanoML decomposes the above search
introduce key building blocks in VolcanoML, and in Section 4 we                    space into smaller subspaces. One interesting design decision in
describe how multiple building blocks are put together to compose                  VolcanoML is to introduce a structured abstraction to express difa VolcanoML execution plan in a modular way. Later in Section 5,                   ferent decomposition strategies. A decomposition strategy is akin
we introduce additional optimizations for these building blocks.                   to an execution plan in relational database management systems,
                                                                                   which is composed of building blocks akin to relational operators.
3.1    Search Space of End-to-End AutoML                                           A building block itself can be viewed as an atomic decomposition
We describe the search space of end-to-end AutoML following                        strategy. We next present the details of the building blocks impleauto-sklearn. The input to the system is a dataset 𝐷, containing a                 mented by VolcanoML, and we will introduce how to use these
set of training samples. The user also provides a pre-defined metric,              blocks to compose VolcanoML execution plans in Section 4.
e.g., validation accuracy or cross-validation accuracy, to measure
the utility of a given ML pipeline. The output of an end-to-end                       Goal. The goal of VolcanoML is to solve:
AutoML system is an ML pipeline that achieves good utility.                                                   min 𝑓 (𝑥 1, ..., 𝑥𝑛 ; 𝐷),
                                                                                                            𝑥 1 ,...,𝑥𝑛
   To find such an ML pipeline, the system searches over a large
search space of possible pipelines and picks one that maximizes                    where 𝑥 1, ..., 𝑥𝑛 is a set of 𝑛 variables and each of them has domain
the pre-defined utility. This search space is a composition of (1)                 D𝑥𝑖 for 𝑖 ∈ [𝑛]. Together, these 𝑛 variables define a search space
                                                                                                    Î
feature engineering operators, (2) ML algorithms/models, and (3)                   (𝑥 1, ..., 𝑥𝑛 ) ∈ 𝑖 D𝑥𝑖 . 𝐷 corresponds to the input dataset, which is
hyper-parameters.                                                                  a set of input samples. In our setting, 𝑓 (·) is a black-box function
                                                                                   that we can only evaluate (but not exploiting the derivative). Given
   Feature Engineering. The feature engineering process takes as                                                                Î
                                                                                   constant 𝒄 in the composite domain 𝒄 ∈ 𝑖 D𝑥𝑖 , we use the notation
input a dataset 𝐷 and outputs a new dataset 𝐷 ′ . It achieves this
                                                                                                            𝑓 ({(𝑥 1, ...𝑥𝑛 ) = 𝒄}; 𝐷)
by transforming the input dataset via a set of data transformations. In auto-sklearn, it further defines multiple stages of the                  as the value of evaluating 𝑓 by substituting (𝑥 1, ...𝑥𝑛 ) with 𝒄.
feature engineering process: (1) preprocessing, (2) rescaling, (3) bal-
                                                                                        Subgoal. One key decision of VolcanoML is to solve the optiancing, and (4) feature_transforming. For each stage, the system
                                                                                   mization problem on a search space by decomposing it into multiple
chooses a single transformation to apply. For example, for fea-
                                                                                   smaller subspaces, each of which will be solved by one building
ture_transforming, the system can choose among no_processing,
                                                                                   block. We define optimizing over each of these smaller subspaces as
kernel_pca, polynomial, select_percentile, etc.
                                                                                   a subgoal of the original problem. Formally, a subgoal 𝑔 is defined
   ML Algorithms. Given a transformed dataset 𝐷 ′ , the system                     by two components: 𝒙¯𝑔 ⊆ {𝑥 1, ...𝑥𝑛 } as a subset of variables, and
                                                                                          Î
then picks an ML algorithm to train. Since different ML algo-                      𝒄¯𝑔 ∈ 𝑥𝑖 ∈𝒙¯𝑔 D𝑥𝑖 as an assignment in the domain of all variables in
rithms are suitable for different types of tasks, the system needs                 𝒙¯𝑔 . Let 𝒙¯−𝑔 = {𝑥 1, ..., 𝑥𝑛 } − 𝒙¯𝑔 be all variables that are not in 𝒙¯𝑔 .
                                                                               3

                                                                                                                                                                             Li et al.

  Each subgoal defines a function 𝑓𝑔 over a smaller search space,                 Algorithm 1: The do_next! of conditioning block
which is constructed by substituting all variables in 𝒙¯𝑔 with 𝒄¯𝑔 :               Input: A conditioning block 𝐵𝑔,𝐷 , budget 𝐾 .
                            Ö                                                    1 Let 𝐵 1 , ..., 𝐵𝑚 be all active (have not been eliminated) child blocks;
   𝑓𝑔 = 𝑓 [𝒙¯𝑔 /¯𝒄𝑔 ] : 𝒛 ∈   D𝑥𝑖 ↦→ 𝑓 ({𝒙¯𝑔 = 𝒄¯𝑔 ; 𝒙¯−𝑔 = 𝒛}; 𝐷).              2 for 1 ≤ 𝑖 ≤ 𝐿 do
                                                                                 3       for 1 ≤ 𝑗 ≤ 𝑚 do
                          𝑥𝑖 ∈𝒙¯ −𝑔
                                                                                 4                do_next! (𝐵 𝑗 ) ;
                                                                                 5 for 1 ≤ 𝑗 ≤ 𝑚 do
   Building Block. Each subgoal 𝑔 corresponds to one building block              6       [𝑙 𝑗 , 𝑢 𝑗 ] ← get_eu (𝐵 𝑗 , 𝐾) ;
𝐵𝑔,𝐷 , whose goal is to solve                                                    7 Eliminate child blocks that are dominated by others, using [𝑙 𝑗 , 𝑢 𝑗 ] for 1 ≤ 𝑗 ≤ 𝑚 ;

                             min 𝑓𝑔 ( 𝒙¯−𝑔 ; 𝐷).                                 spirit of dual decomposition [7]). This inspires VolcanoML’s design,
                             𝒙¯ −𝑔
                                                                                 which supports three types of building blocks: (1) joint block that
A building block 𝐵𝑔,𝐷 imposes several assumptions on 𝑔 and 𝐷.                    simply optimizes the input subspace using Bayesian optimization;
First, given an assignment 𝒄¯−𝑔 to 𝒙¯−𝑔 , it is able to evaluate the value       (2) conditioning block that further divides the input subspace into
of the function 𝑓𝑔 (¯𝒄 −𝑔 , 𝐷). Note that such an evaluation can often           smaller ones by conditioning on one particular input variable; and
be expensive and VolcanoML tries to minimize the number of                       (3) alternating block that partitions the input subspace into two and
times that such a function is evaluated. Second, given a dataset                 optimizes each one alternately. Note that both conditioning block
𝐷, a building block has the knowledge about how to subsample                     and alternating block would generate new building blocks with
a smaller dataset 𝐷˜ ⊆ 𝐷 and then conduct evaluations on such a                  smaller subgoals. We next present the implementation details for
                    ˜ Third, we assume that the building block has
subset 𝒙 ↦→ 𝑓𝑔 (𝒙; 𝐷).                                                           each type of building block.
access to a cost model about the cost of an evaluation at 𝒙, 𝐶𝑔,𝐷,𝒙 .
                                                                                 3.3.1 Joint Block. A joint block directly optimizes its subgoal via
   Interfaces. All implementations of a building block follow an                 Bayesian optimization (BO) [64]. Specifically, BO based method interactive optimization process. A building block exposes several               SMAC [25] has been used by many applications where evaluating
interfaces. First, one can initialize a building block via                       the objective function is computationally expensive. It constructs a
                                                                                 probabilistic surrogate model 𝑀 to capture the relationship between
                      𝐵𝑔,𝐷 ← init(𝑓 , 𝒙¯𝑔 , 𝒄¯𝑔 , 𝐷),                            the input variables 𝒙¯ and the objective function value 𝜓 , and refines
which creates a building block. Second, one can query the current                𝑀 iteratively using past observations ( ¯ 𝒙,𝜓 )s.
best solution found in 𝐵𝑔,𝐷 by                                                      The implementation of do_next! for a joint block consists of
                                                                                 the following three steps:
                  𝒙ˆ ← get_current_best(𝐵𝑔,𝐷 ).
                                                                                     (1) Use the surrogate model 𝑀 to select 𝒙¯ that maximizes an
Furthermore, one can ask 𝐵𝑔,𝐷 to iterate once via                                        acquisition function. In our implementation, we use expected
                           do_next!(𝐵𝑔,𝐷 ),                                              improvement (EI) [30] as the acquisition function, which has
                                                                                         been widely used in BO community.
where ‘!’ indicates potential change on the state of the input 𝐵𝑔,𝐷 .
                                                                                     (2) Evaluate the selected 𝒙¯ and obtain its value about the ob-
   Last but not least, one can query a building block about its ex-
                                                                                         jective function (i.e., the subgoal) 𝜓 = 𝑓𝑔 ( ¯  𝒙) + 𝜖 with
pected utility (EU) if given 𝐾 more budget units (e.g., seconds) via
                                                                                         𝜖 ∼ N (0, 𝜎 2 ), where N is the normal distribution.
                      [𝑙, 𝑢] ← get_eu(𝐵𝑔,𝐷 , 𝐾).                                     (3) Refit the surrogate model 𝑀 on the observed ( ¯  𝒙,𝜓 )s.
By adopting a similar design principle used in the existing AutoML                  Early-Stopping based Optimization. For large datasets, earlysystems [15, 50, 57], in VolcanoML we estimate EU by extrapolation               stopping based methods, e.g., Successive Halving [29], Hyperinto the “future” with more available budget. Given the inherent                 band [43], BOHB [14], MFES-HB [47], etc, can terminate the evaluauncertainty in our estimation method, rather than returning a single             tions of poorly-performed configurations in advance, thus speeding
point estimate, we instead return a lower bound 𝑙 and an upper                   up the evaluations. VolcanoML supports MFES-HB [47], which
bound 𝑢. We refer readers to [45] for the details of how the lower and           combines the benefits of Hyperband and Multi-fidelity BO [67, 75],
upper bounds are established. Moreover, one can query a building                 to optimize a joint block, in addition to vanilla BO.
block about its expected utility improvement (EUI) via
                                                                                 3.3.2 Conditioning Block. A conditioning block decomposes its
                         𝛿 ← get_eui(𝐵𝑔,𝐷 ).                                     input 𝒙¯ into 𝒙¯ = {𝑥𝑐 } ∪ ¯
                                                                                                           𝒚, where 𝑥𝑐 is a single variable with domain
Note that, different from EU, EUI is the expected improvement                    D𝑥𝑐 . It then creates one new building block for each possible value
over the current observed utility if given 𝐾 more budget units. In               𝑑 ∈ D𝑥𝑐 of 𝑥𝑐 :
VolcanoML, we estimate EUI by taking the mean of the observed
                                                                                                        min 𝑔𝑑 ( ¯
                                                                                                                𝒚; 𝐷) ≡ 𝑓 ({𝑥𝑐 = 𝑑, ¯
                                                                                                                                   𝒚}; 𝐷).
improvements from history, following Levine et al [42].                                                   𝒚¯

3.3    Three Types of Building Blocks                                            As a result, |D𝑥𝑐 | new (child) building blocks are created.
Decomposition is the cornerstone of VolcanoML’s design. Given a                     The conditioning block aims to identify optimal value for 𝑥𝑐 , and
search space, apart from exploring it jointly, there are two classical           many previous AutoML researches have used Bandit algorithms for
ways of decomposition — to partition the search space via condi-                 this purpose [29, 46, 47, 50]. In VolcanoML, we follow these pretioning on different values of a certain variable (in a similar spirit           vious work and model it as a multi-armed bandit (MAB) problem,
of variable elimination [10]), or to decompose the problem into mul-             while our framework is flexible enough to incorporate other algotiple smaller ones by introducing equality constraints (in a similar             rithms when they are available. There are |D𝑥𝑐 | arms, where each
                                                                             4

VolcanoML: Speeding up End-to-End AutoML via Scalable Search Space Decomposition

 Algorithm 2: The init of alternating block                                         Algorithm 3: The do_next! of alternating block
                                                           ¯ ∪ 𝒛¯ .
   Input: An alternating block 𝐵𝑔,𝐷 with search space 𝒙¯ = 𝒚                         Input: An alternating block 𝐵𝑔,𝐷 with budget 𝐾 .
              ¯ and 𝒛¯ with default values 𝒚¯ 0 and 𝒛¯ 0 ;
 1 Initialize 𝒚                                                                    1 𝛿 1 ← get_eui (𝐵 1 ) ;
 2 𝐵 1 ← init ( 𝑓 , 𝒛¯ , 𝒛¯ 0 , 𝐷) ;                                               2 𝛿 2 ← get_eui (𝐵 2 ) ;
 3 𝐵 2 ← init ( 𝑓 , ¯
                    𝒚, 𝒚¯ 0 , 𝐷) ;                                                 3 if 𝛿 1 ≥ 𝛿 2 then
 4 for 1 ≤ 𝑖 ≤ 𝐿 do                                                                4        𝒛¯ best ← get_current_best (𝐵 2 ) ;
 5        do_next (𝐵 1 ) ;                                                         5        set_var (𝐵 1 , 𝒛¯ , 𝒛¯ best ) ;
 6        𝒚¯𝑖 ← get_current_best (𝐵 1 ) ;                                          6        do_next (𝐵 1 ) ;
 7        set_var (𝐵 2 , ¯  𝒚, 𝒚¯𝑖 ) ;                                             7    else
 8        do_next (𝐵 2 ) ;                                                         8           𝒚¯ best ← get_current_best (𝐵 1 ) ;
 9        𝒛¯𝑖 ← get_current_best (𝐵 2 ) ;                                           9          set_var (𝐵 2 , ¯
                                                                                                              𝒚, 𝒚¯ best ) ;
10        set_var (𝐵 1 , 𝒛¯ , 𝒛¯𝑖 );                                               10          do_next (𝐵 2 ) ;

arm corresponds to a child block. Playing an arm means invoking                       Specifically, Algorithm 3 starts by polling the EUI of both child
the do_next! primitive of the corresponding child block.                           blocks (lines 1 and 2). Recall that the EUI is estimated by taking the
    Algorithm 1 illustrates the implementation of do_next! for a                   mean of historic observations. It then compares the EUIs and picks
conditioning block. It starts by playing each arm 𝐿 times in a Round-              the arm/block with larger EUI to play next (lines 3 to 10). Before
Robin fashion (lines 2 to 4). Here, 𝐿 is a user-specified configuration            pulling the winner arm, again it will use the current best settings
parameter of VolcanoML. In our current implementation, we set                      found by the other arm/block (lines 4 to 6, lines 8 to 10).
𝐿 = 5. We then obtain the lower and upper bounds of the expected
utility of each child block by invoking its get_eu primitive (lines 5
to 6), and eliminate child blocks that are dominated by others (line               3.3.4 Discussion: Pros and Cons of Building Blocks. While the joint
7). The elimination works as follows. Consider two blocks 𝐵𝑖 and                   block is the most straightforward way to solve the optimization
𝐵 𝑗 : if the upper bound 𝑢𝑖 of 𝐵𝑖 is less than the lower bound 𝑙 𝑗 of              problem associated, it is difficult to scale Bayesian optimization to
𝐵 𝑗 , then the block 𝐵𝑖 is eliminated. An eliminated arm/block will                a large search space [45, 73]. The alternating block addresses this
not be played in future invocations of do_next!.                                   scalability issue by decomposing the search space into two smaller
Remark: We have simplified the above elimination criterion by                      subspaces, though with the assumption that the improvements
using the lower and upper bounds calculated given 𝐾 budget units                   of the two subspaces are conditionally independent of each other.
for each arm. In fact, these 𝐾 budget units are shared by all the arms,            As a result, the alternating block is a better choice when such an
and as a result, each arm actually has fewer budget units than 𝐾.                  assumption approximately holds. The conditioning block is capable
Our assumption is that, 𝐾 is sufficiently large so that one can play               of pruning the search space as optimization proceeds, when bad
all arms until (the observed distribution of rewards of) each arm                  arms are pulled less often or will not be played anymore, with
converges. Otherwise, the lower and upper bounds obtained may be                   the limitation that it can only work for conditional variables that
over-optimistic, and as a result, may lead to incorrect eliminations.              are categorical. For non-categorical variables, one possible way to
Fortunately, our assumption usually holds in practice, where arms                  use conditioning blocks is to split the value range of variables. For
converge relatively fast.                                                          example, given a numerical variable that ranges from 1 to 3, we
                                                                                   split it into two ranges, which are [1, 2) and [2, 3). During the
                                                                                   optimization iteration, we first choose one sub-range and then
3.3.3 Alternating Block. An alternating block decomposes its input
                                                                                   optimize the splitted space along with its corresponding subspace.
search space into 𝒙¯ = 𝒚¯ ∪ 𝒛¯ , and explores 𝒚¯ and 𝒛¯ in an alternating
                                                                                      In addition, VolcanoML uses bandit-based algorithms from the
way. Similarly, we also model the optimization in alternating block
                                                                                   existing literature [42, 45] as default in both the alternating and
as an MAB problem. Algorithm 2 illustrates how its init primitive
                                                                                   conditioning block, and other bandit-based algorithms, such as
works. It first creates two child blocks 𝐵 1 and 𝐵 2 , which will focus
                                                                                   successive halving [29], Hyperband [43], BOHB [14] and MFESon optimizing for 𝒚¯ and 𝒛¯ respectively (lines 1 to 3). It then (again)
                                                                                   HB [47], can also be used in these blocks.
views 𝐵 1 and 𝐵 2 as two arms and plays them using Round-Robin
(lines 4 to 10). Note that, when 𝐵 1 optimizes 𝒚¯ (resp. when 𝐵 2
optimizes 𝒛¯ ), it uses the current best 𝒛¯ found by 𝐵 2 (resp. the current        3.3.5 Discussion: Comparing Different Building Blocks. Joint blocks
best 𝒚¯ found by 𝐵 1 ). This is done by the set_var primitive (invoked             are the default blocks that can be applied to all problems. When the
at line 7 for 𝐵 2 and line 10 for 𝐵 1 ).                                           search space is rather large, conditioning and alternating blocks
    One problem of our alternating MAB formulation is that the                     can be helpful. If the search space contains a categorical hyperutility improvements of the two building blocks often vary dramat-                 parameter, under which the subspace of each choice is conditionally
ically in practice. For example, some applications are very sensitive              independent with each other, the conditioning block can be used
to the features being used (e.g., normalized vs. non-normalized                    instead of exploring the entire space. If the search space can be
features) while hyper-parameter tuning will offer little or even                   decomposed into two approximately independent subspaces, the
no improvement. In this case, we should spend more resources                       alternating block can be applied to this case. As a result, a scalable
on looking for good features instead of tuning hyper-parameters.                   system needs to be able to decompose the problem in different
Our key observation is that, the expected utility improvement (EUI)                ways and pick the most suitable building blocks. This forms a Voldecays as optimization proceeds. As a result, we propose to use                    canoML execution plan, which we will describe in the next section.
EUI as an indicator that measures the potential of pulling an arm                  In Section 4, we explore the possibility of automatically choosing
further. Algorithm 3 illustrates the details of this idea when used                building blocks to use by maximizing the empirical accuracy of
to implement the do_next! primitive.                                               different execution plans, given a pre-defined set of datasets.
                                                                               5

                                                                                                                                                                                                                Li et al.

                   min 𝑓(𝑥, 𝑦, 𝑧, 𝑤; 𝐷)                                  Plan 2
                (",$,%,&)
                                                                       Cond on x                                                      Search Space: (Embedding, Algorithm, Feature, HP)
                                                                       (x, y, z, w)
                                                                                                                                                                         Plan
                   Plan 1                                                                                                                                           Cond. on Alg
                    Joint                          Joint                  Joint                   Joint                                                           (Alg, Feature, HP)
               (x, y, z, w)                       (y, z, w)             (y, z, w)                (y, z, w)
                                                                                                                                                 Alter.                 Alter.                 Alter.
Figure 1: Two different execution plans for the same opti-                                                                                  (Feature, HP)           (Feature, HP)          (Feature, HP)
mization problem. Each plan corresponds to a different way
to decompose the same search space (𝑥, 𝑦, 𝑧, 𝑤).                                                                                         Joint            Joint                        Joint            Joint
                                                                                                                                                                         ...
                                                                                                                                      (Embedding          (HP)                   (Embedding             (HP)
                     Search Space: (Algorithm, Feature, HP)                           𝐴! : Liblinear SVC
                                                                                                                                       , Feature)                                 , Feature)
                                                                                      𝐴" : Adaboost
                                                           Plan                       …                                  Figure 3: VolcanoML’s execution plan for a larger search
                                                                                      𝐴# : Random Forest
                                                  Cond. on Alg={𝐴! ,…, 𝐴" }
                                                   (Alg, Feature, HP)
                                                                                                                         space enriched by an additional embedding selection stage.
                                                                                                                            Concretely, Figure 2 shows a search space for AutoML with 𝐾
                       Alter. (𝐴! )                     Alter. (𝐴# )                    Alter. (𝐴" )
                     (Feature, HP)                    (Feature, HP)
                                                                              ...
                                                                                      (Feature, HP)
                                                                                                                         choices of ML algorithms. During each iteration, starting from the
                                                                                                                         root node, VolcanoML selects the child node to optimize until it
            Joint (HP fixed)   Joint (FE fixed)                               Joint (HP fixed)    Joint (FE fixed)
                                                             ...                                                         reaches a leaf node, and then optimizes over the subspace in the leaf
            (𝐴! .Feature)         (𝐴! .HP)                                    (𝐴" .Feature)            (𝐴" .HP)
                                                                                                                         node. As shown by the red lines in Figure 2, in this iteration, Vol-
Figure 2: VolcanoML’s execution plan for the same search                                                                 canoML only tunes the feature engineering pipeline of algorithm
space as explored by auto-sklearn. Here ‘Alg’ and ‘HP’ cor-                                                              𝐴1 while fixing its algorithm hyper-parameters.
respond to Algorithm and hyper-parameters respectively.
4   VOLCANOML EXECUTION PLAN                                                                                                Alternative Execution Plans. Note that the execution plan in Fig-
Given a pre-defined search space, the input of VolcanoML is (1) a                                                        ure 2 is not the only possible one. Our flexible and scalable framedataset 𝐷, (2) a utility metric (e.g, cross-validation accuracy) which                                                   work in VolcanoML allows us to explore different execution plans
defines the objective function 𝑓 , and (3) a time budget. VolcanoML                                                      before reaching the proposed one. We enumerate five possible plans
then decomposes a large search space into an execution plan, fol-                                                        in a coarse-grained level, and the results show that the proposed
lowing some specific decomposition strategy.                                                                             plan performs best. The reason why we choose this plan is due to
                                                                                                                         the fundamental property of the AutoML search space — we observe
   VolcanoML Execution Plan. Due to space limitation, we omit                                                            that, the optimal choices of features are different across algorithms,
the formal definition of a VolcanoML execution plan. Intuitively,                                                        which implies that we can first decompose the search space along
a VolcanoML execution plan is a tree of building blocks. The root                                                        ML algorithms. The improvements introduced by feature engineernode corresponds to a building block solving the problem 𝑓 with                                                          ing and hyper-parameter tuning are largely complementary, and
the entire search space, which can be further decomposed into                                                            thus we can optimize them alternately. For feature engineering
multiple building blocks if necessary, as previously described. As                                                       (resp. hyper-parameter tuning), the subspace is small enough to be
an example, Figure 1 illustrates two possible execution plans for                                                        handled by a single joint block efficiently.
𝑓 (𝑥, 𝑦, 𝑧, 𝑤; 𝐷). Plan 1 contains only a single root building block as
a joint block, whereas Plan 2 first introduces a conditioning block                                                         VolcanoML Plan for Enriched Search Space. We can easily exon 𝑥, and then creates one lower level of building blocks for each                                                       tend VolcanoML and enable functionalities that are not supported
possible value of 𝑥 (in Figure 1, we assume that |D𝑥 | = 3).                                                             by most AutoML systems. For example, Figure 3 illustrates an exe-
   VolcanoML Execution Model. To execute a VolcanoML execu-                                                              cution plan for a search space with an additional stage — embedding
tion plan, we follow a Volcano-style execution that is similar to                                                        selection. Given an input, e.g., image or text, we first choose embeda relational database [21] — the system invokes the do_next! of                                                          dings based on a collection of TensorFlow Hub pre-trained models,
the root node, which then invokes the do_next! of one of its child                                                       and then conduct algorithm selection, feature engineering, and
nodes, propagating until the leaf node. At any time, one can in-                                                         hyper-parameter tuning. We use an execution plan as illustrated
voke the get_current_best of the root node, which returns the                                                            in Figure 3, having the embedding selection step jointly optimized
current best solution for the entire search space.                                                                       together with the feature engineering.
   VolcanoML Plan for auto-sklearn. Figure 2 presents a Vol-                                                             Discussion: Automatic Plan Generation. In principle, the decanoML execution plan for the same search space explored by                                                              sign of VolcanoML opens up the opportunity for “automatic plan
auto-sklearn, which consists of the joint search of algorithms,                                                          generation” — given a collection of benchmark datasets, one could
features, and hyper-parameters. Instead of conducting the search                                                         automatically search for the best decomposition strategy of the
process in a single joint block, as was done by auto-sklearn, Vol-                                                       search space and come up with a physical plan automatically. While
canoML first decomposes the search space via a conditioning block                                                        the full treatment of this problem is beyond the scope of this paper,
on algorithms — this introduces a MAB problem in which each arm                                                          we illustrate the possibility with a very simple strategy. We autocorresponds to one particular algorithm. It then further decomposes                                                      matically enumerate all possible execution plans in a coarse-grained
each of the conditioned subspaces via an alternating block between                                                       level, and find that our manually specified execution plan in Figfeature engineering and hyper-parameter tuning. The whole sub-                                                           ure 2 outperforms the alternatives. There is still an open question
space of feature engineering (resp. that of hyper-parameter tuning)                                                      that whether we can support finer-grained partition of the search
is optimized by a joint block.                                                                                           space (e.g., different plans for different subspace of features), and
                                                                                                                     6

VolcanoML: Speeding up End-to-End AutoML via Scalable Search Space Decomposition

moreover, whether we can conduct efficient automatic plan opti-                          Search Space - Task   TPOT    AUSK−   AUSK    VolcanoML−   VolcanoML
                                                                                         Small - CLS            3.09    3.07    3.01       2.94        2.89
mization without enumerating all possible plans. These are exciting                      Medium - CLS            3.2    3.32    3.27       2.78        2.43
future directions and we expect the endeavor to be non-trivial. We                       Large - CLS            3.29    3.77    3.57       2.72        1.65
                                                                                         Small - REG            2.98    3.02    3.0        3.02        2.98
hope that this paper sets the ground for this line of research in the                    Medium - REG           2.95     3.3    3.12       2.75        2.88
                                                                                         Large - REG             3.1    3.85    3.82       2.15
future (e.g., rule-based heuristics or reinforcement learning).                                                                                        2.08

Further Optimization with Meta-learning. VolcanoML sup-                            Table 1: Average ranks on 30 classification (CLS) datasets
ports meta-learning based techniques — given previous runs of                      and 20 regression (REG) datasets with three different search
the system over similar workloads, to transfer the knowledge and                   spaces. (The lower is the better)
better help the workload at hand — to accelerate the optimization
process of building blocks. Appendix contains the details.                            In our evaluation, we repeat each experiment 10 times and report
                                                                                   the average utility metric. In each experiment, we use four fifths of
5     EXPERIMENTAL EVALUATION                                                      the data samples in each dataset to search for the best ML pipeline
                                                                                   and report the utility metric on the remaining fifth.
We compare VolcanoML with state-of-the-art AutoML systems.
In our evaluation, we focus on three perspectives: (1) the perfor-                 Methodology for Comparing AutoML Systems. To compare
mance of VolcanoML given the same search space explored by                         the overall test result of each AutoML system on a wide range of
existing systems, (2) the scalability of VolcanoML given larger                    datasets, we use the average rank as the metric following [1]. For
search spaces, and (3) the extensibity of VolcanoML to integrate                   each dataset, we rank all participant systems based on the result
new components into the search space of AutoML pipelines.                          of the best ML pipeline they have found so far; we then take the
                                                                                   average of their ranks across different datasets.
                                                                                   More Details. We include the details of search space and pro-
5.1    Experimental Setup
                                                                                   gramming API, experiment settings and additional experiments,
AutoML Systems. We evaluate VolcanoML as well as two open                          and reproductions in the Appendix.
source AutoML systems: auto-sklearn [15] and TPOT [57]. In addition, we also compare VolcanoML with four commercial AutoML                      5.2       End-to-End Comparison
platforms from Google, Amazon AWS, Microsoft Azure, and Ora-                       We first evaluate the participant AutoML systems given the search
cle. Both VolcanoML and auto-sklearn support meta-learning,                        space explored by auto-sklearn. Figure 4 presents the results
while TPOT does not. For fair comparison with TPOT, we also use                    of VolcanoML compared to auto-sklearn (AUSK) and TPOT
VolcanoML− and AUSK− to denote the versions of VolcanoML                           on the 30 classification tasks and the 20 regression tasks, respecand auto-sklearn when meta-learning is disabled. Our implemen-                     tively. For classification tasks, we plot the classification accuracy
tation of VolcanoML is available at https://github.com/VolcanoML.                  improvement (%); for regression tasks, we plot the relative MSE
Datasets. To compare VolcanoML with academic baselines, we                                                                                𝑠 (𝑚 2 )−𝑠 (𝑚 1 )
                                                                                   improvement Δ, which is defined as Δ(𝑚 1, 𝑚 2 ) = max(𝑠 (𝑚                   ,
                                                                                                                                                  2 ),𝑠 (𝑚 1 ))
use 60 real-world ML datasets from the OpenML repository [71],                     where 𝑠 (·) is MSE on the test set. Overall, VolcanoML outperforms
including 40 for classification (CLS) tasks and 20 for regression                  auto-sklearn and TPOT on 25 and 23 of the 30 classification tasks,
(REG) tasks. 10 of the 40 classification datasets are relatively large,            and on 17 and 15 of the 20 regression tasks, respectively.
each with 20k to 110k data samples; the other 30 are of medium                        We also conduct experiments to evaluate VolcanoML with difsize, each with 1k to 12k samples. In addition, we also use datasets               ferent time budgets. Figure 5 presents the results on four large
from six Kaggle competitions to compare VolcanoML with four                        classification datasets. We observe that VolcanoML exhibits concommercial platforms.                                                              sistent performance over different time budgets. Notably, on Higgs,
AutoML Tasks. We define three kinds of real-world AutoML tasks,                    VolcanoML achieves 27.2% test error within 4 hours, which is
including (1) a general classification task on 30 medium datasets,                 better than the performance of the other two systems given 24
(2) a general regression task on 20 medium datasets, and (3) a large-              hours.
scale classification task on 10 large datasets.                                       We further study the scalability of the participant systems on
    To test the scalability of the participating systems, we design                the three aforementioned search spaces. Table 1 summarizes the
three search spaces that include 20, 29, and 100 hyper-parameters,                 results in terms of the average ranks. We have two observations:
where the smaller search space is a subset of the larger one. We run               First, without meta-learning, VolcanoML achieves the best average
VolcanoML and the baseline AutoML systems against each of the                      rank for both the classification and regression tasks — on the small
three search spaces. The time budget is 900 seconds for the smallest               search space (with 20 hyper-parameters), VolcanoML performs
search space and 1,800 seconds for the other two, when performing                  slightly better than auto-sklearn and TPOT, and it performs sigthe general classification task (1); the time budget is increased to               nificantly better on the medium (with 29 hyper-parameters) and
5,400 and 86,400 seconds respectively, when performing the general                 large (with 100 hyper-parameters) search spaces. Second, with metaregression task (2) and the large-scale classification task (3).                   learning, the average rank of VolcanoML is dramatically improved
Utility Metrics. Following [15], we adopt the metric balanced                      compared with auto-sklearn. Overall, VolcanoML with metaaccuracy for all classification tasks — compared with standard (clas-              learning achieves the best result over large search space. Furthersification) accuracy, it assigns equal weights to classes and takes                more, we also design additional experiments to evaluate the consisthe average of class-wise accuracy. For regression tasks, we use the               tency of system performance given different (larger) time budgets
mean squared error (MSE) as the metric.                                            and search spaces, and more details can be found in Appendix.
                                                                               7

                                                                                                                                                                                                                                                                                                                                                                                                                                                  Li et al.

                    Accuracy Improvement (%)                                                                                                  Relative MSE Improvement                                                                                    Accuracy Improvement (%)                                                                                            Relative MSE Improvement
                                                                                                                                                                         1.0                                                                                                                                                                                                                             1.0
                                               6
                                                                                                                                                                                                                                                                                     4

                                               4                                                                                                                         0.5                                                                                                                                                                                                                             0.5
                                                                                                                                                                                                                                                                                     2
                                               2

                                                                                                                                                                         0.0                                                                                                         0                                                                                                                   0.0
                                               0
                                                     1489
                                                      737
                                                     1056
                                                      816
                                                      734
                                                       38
                                                      803
                                                       32
                                                                                                                                                                                   541
                                                                                                                                                                                 41021
                                                                                                                                                                                   529
                                                                                                                                                                                   315
                                                                                                                                                                                   225                                                                                                   1489
                                                                                                                                                                                                                                                                                          819
                                                                                                                                                                                                                                                                                          752
                                                                                                                                                                                                                                                                                          183
                                                                                                                                                                                                                                                                                          737
                                                                                                                                                                                                                                                                                          803
                                                                                                                                                                                                                                                                                         1067
                                                                                                                                                                                                                                                                                          728
                                                                                                                                                                                                                                                                                                                                                                                                                 23516
                                                                                                                                                                                                                                                                                                                                                                                                                   223
                                                                                                                                                                                                                                                                                                                                                                                                                   541
                                                                                                                                                                                                                                                                                                                                                                                                                   507
                                                                                                                                                                                                                                                                                                                                                                                                                   225
                                                      847
                                                      819
                                                      728
                                                      183
                                                      735
                                                      761
                                                     1053
                                                       44
                                                      772                                                                                                                          558
                                                                                                                                                                                   503
                                                                                                                                                                                    A1
                                                                                                                                                                                 42369                                                                                                    761
                                                                                                                                                                                                                                                                                           44
                                                                                                                                                                                                                                                                                           28
                                                                                                                                                                                                                                                                                           32
                                                                                                                                                                                                                                                                                          816
                                                                                                                                                                                                                                                                                          182
                                                                                                                                                                                                                                                                                          847
                                                                                                                                                                                                                                                                                          735
                                                                                                                                                                                                                                                                                           36                                                                                                                      572
                                                                                                                                                                                                                                                                                                                                                                                                                   558
                                                                                                                                                                                                                                                                                                                                                                                                                   529
                                                                                                                                                                                                                                                                                                                                                                                                                 41021
                                                     1021
                                                      310
                                                     1067
                                                       36
                                                      979
                                                       28
                                                     1487
                                                      871
                                                      833
                                                      752
                                                                                                                                                                                   668
                                                                                                                                                                                   223
                                                                                                                                                                                   507
                                                                                                                                                                                   572
                                                                                                                                                                                 23516                                                                                                    979
                                                                                                                                                                                                                                                                                         1487
                                                                                                                                                                                                                                                                                           38
                                                                                                                                                                                                                                                                                          734
                                                                                                                                                                                                                                                                                         1056
                                                                                                                                                                                                                                                                                          871
                                                                                                                                                                                                                                                                                          772
                                                                                                                                                                                                                                                                                         1471
                                                                                                                                                                                                                                                                                          807
                                                                                                                                                                                                                                                                                          310
                                                                                                                                                                                                                                                                                                                                                                                                                   503
                                                                                                                                                                                                                                                                                                                                                                                                                   573
                                                                                                                                                                                                                                                                                                                                                                                                                   315
                                                                                                                                                                                                                                                                                                                                                                                                                    A1
                                                                                                                                                                                                                                                                                                                                                                                                                   227
                                                      182
                                                     1471
                                                      807                                                                                                                        41539
                                                                                                                                                                                   227
                                                                                                                                                                                   573                                                                                                    833
                                                                                                                                                                                                                                                                                         1053
                                                                                                                                                                                                                                                                                         1021                                                                                                                    42369
                                                                                                                                                                                                                                                                                                                                                                                                                 41539
                                                                                                                                                                                                                                                                                                                                                                                                                   668
                                                                         Dataset ID                                                                                                                                                                                                                           Dataset ID
                                                                                                                                                                                 23515
                                                                                                                                                                                   189
                                                                                                                                                                                   308                                                                                                                                                                                                                             189
                                                                                                                                                                                                                                                                                                                                                                                                                 23515
                                                                                                                                                                                                                                                                                                                                                                                                                   308
                                                                                                                                                                                                Dataset ID                                                                                                                                                                                                                 Dataset ID

                                                   (a) VolcanoML vs. AUSK on CLS                                                                        (b) VolcanoML vs. AUSK on REG                                                                                                    (c) VolcanoML vs. TPOT on CLS                                                                   (d) VolcanoML vs. TPOT on REG
                                                   Figure 4: End-to-End results on 30 OpenML classification (CLS) datasets and 20 OpenML regression (REG) datasets.
                                       5                                                                                                 30                                                                                                                          12                                                                                                  30

              Average Test Error (%)                                                                            Average Test Error (%)                                                                                                      Average Test Error (%)                                                                              Average Test Error (%)
                                                                                   TPOT                                                                                                                       TPOT                                                                                                       TPOT                                                                                                         TPOT
                                       4
                                                                                   AUSK                                                  25                                                                   AUSK                                                   10                                                  AUSK                                            29
                                                                                                                                                                                                                                                                                                                                                                                                                                      AUSK
                                                                                   VolcanoML                                                                                                                  VolcanoML                                                                                                  VolcanoML                                                                                                    VolcanoML
                                                                                                                                         20                                                                                                                              8
                                       3                                                                                                                                                                                                                                                                                                                                 28

                                                                                                                                         15
                                                                                                                                                                                                                                                                         6
                                       2                                                                                                                                                                                                                                                                                                                                 27
                                                     14400     28800   43200   57600       72000      86400                                                              14400       28800     43200     57600      72000       86400                                                     14400    28800    43200    57600   72000     86400                                                             14400   28800    43200   57600   72000      86400
                                                                 Wall Clock Time (s)                                                                                                   Wall Clock Time (s)                                                                                           Wall Clock Time (s)                                                                                           Wall Clock Time (s)

                                                              (a) Mnist_784                                                                                                             (b) Kropt                                                                                                  (c) Electricity                                                                                                  (d) Higgs
                                                                                           Figure 5: Average test errors on four large datasets with different time budgets.
                                                                                                                                                                                                                                                                                                           Dataset                   AUSK      VolcanoML−                                                          VolcanoML
                                                             VolcanoML                             Platform 1                                                                                Platform 3
                                                                                                                                                                                                                                                                                                           sick                      97.29                               97.31

 Average Test Error (%)
                                                                                                   Platform 2                                                                                Platform 4                                                                                                                                                                                                                  97.34
                                                                                                                                                                                 6                                                                                                                         pc2                       86.70                               86.91                                           90.27
                           20                                                                                                                                                                                                                                                                              abalone                   66.86                               65.97                                           67.32
                                                                                       4                                                                                         4                                                                                                                         page-blocks(2)            94.70                               95.29                                           96.69
                           15                                                                                                                                                                                                                                                                              hypothyroid(2)            99.62                               99.64                                           99.64
                                                                                                                                                                                 2
                           10                                                          2                                                                                                                                                                        Table 2: Test accuracy (%) of VolcanoML with and without
                                                      900     1800 2700 3600                   900      1800    2700                                       3600                               1800        3600          5400
                                                   Wall Clock Time (s)                       Wall Clock Time (s)                                                                         Wall Clock Time (s)                                                    the enrichment of “smote_balancer” operator.
               (a) Influence Network                                                       (b) Virus Prediction                                                                      (c) Employee Access                                                         and we evaluate VolcanoML with the enriched search space on
                                                                                                                                                                                                                                                                 the Kaggle dataset dogs-vs-cats. We observe that VolcanoML

      Average Test Error (%)
                                                                                   40
                                                                                                                                                                                                                                                                 achieves 96.5% test accuracy, which is significantly better than 69.7%
                                                                                                                                                                                 2
                                  4                                                                                                                                                                                                                              obtained by auto-sklearn without considering embeddings.
                                                                                   35                                                                                            0
                                  2
                                                                                                                                                                                                                                                                 5.4                              Comparison with 4 Industrial Platforms
                                                   3600 5400 7200 9000 10800                  9000 18000 27000 36000                                                                         1800      3600      5400    7200
                                                   Wall Clock Time (s)                       Wall Clock Time (s)                                                                         Wall Clock Time (s)                                                   In addition, we run additional experiments on six Kaggle datasets to
 (d) Customer Satisfaction                                                                 (e) Business Value                                                                                (f) Flavours
                                                                                                                                                                                                                                                               compare VolcanoML with four commercial AutoML platforms: 1)
                                                                                                                                                                                                                                                               Google Cloud AutoML, 2) Microsoft Azure Automated ML, 3) Oracle
Figure 6: Test errors on 6 Kaggle competitions compared                                                                                                                                                                                                        data science, and 4) Amazon AWS Sagemaker AutoPilot. Here, we
with four commercial platforms.                                                                                                                                                                                                                                anonymously refer to these platforms as Platform 1-4. Figure 6
5.3                                                  Search Space Enrichment                                                                                                                                                                                   show the results, and the Appendix contains the experiment details.
We now focus on evaluating the extensibility of VolcanoML via                                                                                                                                                                                                  We observe that, given the same time budget (i.e., fix the x-axis to
two experiments with enriched search spaces.                                                                                                                                                                                                                   some time budget), VolcanoML is at least comparable with, often
   Adding Data_Balancing Operator. In the first experiment, we                                                                                                                                                                                                 outperforms, the considered commercial platforms.
implement “smote_balancer” – a new feature engineering oper-
                                                                                                                                                                                                                                                                 6                        CONCLUSION
ator, and incorporate it into the aforementioned balancing stage
of feature engineering (FE) (Section 3.1). Note that auto-sklearn                                                                                                                                                                                                In this paper, we have presented VolcanoML, a scalable and extencannot support this fine-grained enrichment of the search space. Ta-                                                                                                                                                                                             sible framework that allows users to design decomposition strateble 2 presents the results of auto-sklearn, VolcanoML without                                                                                                                                                                                                    gies for large AutoML search spaces in an expressive and flexible
enrichment, and VolcanoML with enrichment, on five imbalanced                                                                                                                                                                                                    manner. VolcanoML introduces novel building blocks akin to reladatasets. We observe that enriching the search space brings fur-                                                                                                                                                                                                 tional operators in database systems that enable expressing search
ther improvement, e.g., VolcanoML with enrichment outperforms                                                                                                                                                                                                    space decomposition strategies in a structured fashion – similar
auto-sklearn by 3.57% (balanced accuracy) on the dataset pc2.                                                                                                                                                                                                    to relational execution plans. Moreover, VolcanoML introduces
   Supporting Embedding Selection. In the second experiment, we                                                                                                                                                                                                  a Volcano-style execution model, inspired by its classic counteradd a new stage “embedding selection” into the FE pipeline, with                                                                                                                                                                                                 part that has been widely used for relational query evaluation, to
two candidate embedding-extraction operators (i.e., two pre-trained                                                                                                                                                                                              execute the decomposition strategies it yields. Experimental evalumodels). This allows VolcanoML to deal with images, which are                                                                                                                                                                                                    ation demonstrates that VolcanoML can generate more efficient
not easily supported by both auto-sklearn and TPOT. We imple-                                                                                                                                                                                                    decomposition strategies that also lead to performance-wise better
ment two pre-trained models to generate embeddings for images,                                                                                                                                                                                                   ML pipelines, compared to state-of-the-art AutoML systems.

                                                                                                                                                                                                                                        8

VolcanoML: Speeding up End-to-End AutoML via Scalable Search Space Decomposition

REFERENCES                                                                                       [27] Frank Hutter, Jörg Lücke, and Lars Schmidt-Thieme. 2015. Beyond Manual Tuning
 [1] Rémi Bardenet, Mátyás Brendel, Balázs Kégl, and Michele Sebag. 2013. Collabo-                    of Hyperparameters. KI - Kunstliche Intelligenz (2015). https://doi.org/10.1007/
     rative hyperparameter tuning. In International Conference on Machine Learning.                   s13218-015-0381-0
     199–207.                                                                                    [28] IBM. 2020. IBMWatson Studio AutoAI. https://www.ibm.com/cloud/watson-
 [2] Jeff Barnes. 2015. Azure machine learning. Microsoft Azure Essentials. 1st ed,                   studio/autoai.
     Microsoft (2015).                                                                           [29] Kevin Jamieson and Ameet Talwalkar. 2016. Non-stochastic best arm identifi-
 [3] James Bergstra and Yoshua Bengio. 2012. Random search for hyper-parameter                        cation and hyperparameter optimization. In Artificial Intelligence and Statistics.
     optimization. Journal of Machine Learning Research 13, Feb (2012), 281–305.                      240–248.
 [4] James S Bergstra, Rémi Bardenet, Yoshua Bengio, and Balázs Kégl. 2011. Al-                  [30] Donald R Jones, Matthias Schonlau, and William J Welch. 1998. Efficient global
     gorithms for hyper-parameter optimization. In Advances in neural information                     optimization of expensive black-box functions. Journal of Global optimization 13,
     processing systems. 2546–2554.                                                                   4 (1998), 455–492.
 [5] Matthias Boehm, Iulian Antonov, Mark Dokter, Robert Ginthör, Kevin Innerebner,              [31] Kirthevasan Kandasamy, Gautam Dasarathy, Jeff Schneider, and Barnabas Poczos.
     Florijan Klezin, Stefanie Lindstaedt, Arnab Phani, and Benjamin Rath. 2019.                      2017. Multi-fidelity bayesian optimisation with continuous approximations. arXiv
     SystemDS: A declarative machine learning system for the end-to-end data science                  preprint arXiv:1703.06240 (2017).
     lifecycle. arXiv:1909.02976                                                                 [32] James Max Kanter and Kalyan Veeramachaneni. 2015. Deep feature synthesis:
 [6] Eric Breck et al. 2019. Data Validation for Machine Learning. In SysML.                          Towards automating data science endeavors. In 2015 IEEE International Conference
 [7] Claus C CarøE and Rüdiger Schultz. 1999. Dual decomposition in stochastic                        on Data Science and Advanced Analytics, DSAA 2015, Paris, France, October 19-21,
     integer programming. Operations Research Letters 24, 1-2 (1999), 37–45.                          2015. IEEE, 1–10.
 [8] Boyuan Chen, Harvey Wu, Warren Mo, and Ishanu Chattopadhyay. 2018. Au-                      [33] Gilad Katz, Eui Chul Richard Shin, and Dawn Song. 2017. ExploreKit: Automatic
     tostacker: A compositional evolutionary learning system. In GECCO 2018 - Pro-                    feature generation and selection. In Proc. - IEEE Int. Conf. Data Mining, ICDM.
     ceedings of the 2018 Genetic and Evolutionary Computation Conference. https:                     https://doi.org/10.1109/ICDM.2016.176
     //doi.org/10.1145/3205455.3205586 arXiv:1803.00684                                          [34] Ambika Kaul, Saket Maheshwary, and Vikram Pudi. 2017. Autolearn - automated
 [9] Alex G.C. de Sá, Walter José G.S. Pinto, Luiz Otavio V.B. Oliveira, and Gisele L.                feature generation and selection. In Proc. - IEEE Int. Conf. Data Mining, ICDM,
     Pappa. 2017. RECIPE: A grammar-based framework for automatically evolving                        Vol. 2017-Novem. https://doi.org/10.1109/ICDM.2017.31
     classification pipelines. In Lecture Notes in Computer Science (including subseries         [35] Udayan Khurana, Horst Samulowitz, and Deepak Turaga. 2018. Feature engineer-
     Lecture Notes in Artificial Intelligence and Lecture Notes in Bioinformatics). https:            ing for predictive modeling using reinforcement learning. In 32nd AAAI Conf.
     //doi.org/10.1007/978-3-319-55696-3_16                                                           Artif. Intell. AAAI 2018.
[10] Rina Dechter. 1998. Bucket elimination: A unifying framework for probabilistic              [36] Udayan Khurana, Deepak Turaga, Horst Samulowitz, and Srinivasan Parthasrathy.
     inference. In Learning in graphical models. Springer, 75–104.                                    2016. Cognito: Automated Feature Engineering for Supervised Learning. In IEEE
[11] Iddo Drori, Yamuna Krishnamurthy, Remi Rampin, Raoni De, Paula Lourenco,                         Int. Conf. Data Min. Work. ICDMW, Vol. 0. https://doi.org/10.1109/ICDMW.2016.
     Jorge Piazentin Ono, Kyunghyun Cho, Claudio Silva, and Juliana Freire. 2018.                     0190
     AlphaD3M: Machine Learning Pipeline Synthesis. AutoML Workshop at ICML                      [37] Aaron Klein, Stefan Falkner, Simon Bartels, Philipp Hennig, and Frank Hutter.
     (2018).                                                                                          2017. Fast Bayesian Optimization of Machine Learning Hyperparameters on
[12] Valeria Efimova, Andrey Filchenkov, and Viacheslav Shalamov. 2017. Fast Au-                      Large Datasets. In Proceedings of the 20th International Conference on Artificial
     tomated Selection of Learning Algorithm And its Hyperparameters by Rein-                         Intelligence and Statistics. 528–536.
     forcement Learning. In International Conference on Machine Learning AutoML                  [38] Brent Komer, James Bergstra, and Chris Eliasmith. 2014. Hyperopt-sklearn:
     Workshop.                                                                                        automatic hyperparameter configuration for scikit-learn. In ICML workshop on
[13] Katharina Eggensperger, Matthias Feurer, Frank Hutter, James Bergstra, Jasper                    AutoML, Vol. 9. Citeseer.
     Snoek, Holger Hoos, and Kevin Leyton-Brown. 2013. Towards an empirical                      [39] Tim Kraska. 2018. Northstar: An Interactive Data Science System. PVLDB (2018).
     foundation for assessing bayesian optimization of hyperparameters. In NIPS                  [40] Sanjay Krishnan et al. 2016. ActiveClean: Interactive Data Cleaning for Statistical
     workshop on Bayesian Optimization in Theory and Practice, Vol. 10. 3.                            Modeling. PVLDB (2016).
[14] Stefan Falkner, Aaron Klein, and Frank Hutter. 2018. BOHB: Robust and efficient             [41] Erin LeDell and S Poirier. 2020. H2o automl: Scalable automatic machine learning.
     hyperparameter optimization at scale. arXiv preprint arXiv:1807.01774 (2018).                    In Proceedings of the AutoML Workshop at ICML, Vol. 2020.
[15] Matthias Feurer, Aaron Klein, Katharina Eggensperger, Jost Springenberg, Manuel             [42] Nir Levine, Koby Crammer, and Shie Mannor. 2017. Rotting bandits. In Advances
     Blum, and Frank Hutter. 2015. Efficient and robust automated machine learning.                   in NIPS. 3074–3083.
     In Advances in neural information processing systems. 2962–2970.                            [43] Lisha Li, Kevin Jamieson, Giulia DeSalvo, Afshin Rostamizadeh, and Ameet Tal-
[16] Matthias Feurer, Benjamin Letham, and Eytan Bakshy. 2018. Scalable meta-                         walkar. 2018. Hyperband: A novel bandit-based approach to hyperparameter
     learning for bayesian optimization using ranking-weighted gaussian process                       optimization. Proceedings of the International Conference on Learning Representa-
     ensembles. In AutoML Workshop at ICML.                                                           tions (2018), 1–48.
[17] Hector Garcia-Molina, Jeffrey D. Ullman, and Jennifer Widom. 2008. Database                 [44] Tian Li et al. 2018. Ease.ml: Towards Multi-tenant Resource Sharing for Machine
     Systems: The Complete Book (2 ed.). Prentice Hall Press, USA.                                    Learning Workloads. In PVLDB.
[18] Amol Ghoting, Rajasekar Krishnamurthy, Edwin Pednault, Berthold Rein-                       [45] Yang Li, Jiawei Jiang, Jinyang Gao, Yingxia Shao, Ce Zhang, and Bin Cui. 2020.
     wald, Vikas Sindhwani, Shirish Tatikonda, Yuanyuan Tian, and Shivakumar                          Efficient Automatic CASH via Rising Bandits.. In AAAI. 4763–4771.
     Vaithyanathan. 2011. SystemML: Declarative machine learning on MapRe-                       [46] Yang Li, Jiawei Jiang, Jinyang Gao, Yingxia Shao, Ce Zhang, and Bin Cui. 2020. Ef-
     duce. In Proceedings - International Conference on Data Engineering. https:                      ficient Automatic CASH via Rising Bandits. In The Thirty-Fourth AAAI Conference
     //doi.org/10.1109/ICDE.2011.5767930                                                              on Artificial Intelligence, AAAI 2020, The Thirty-Second Innovative Applications
[19] Daniel Golovin, Benjamin Solnik, Subhodeep Moitra, Greg Kochanski, John                          of Artificial Intelligence Conference, IAAI 2020, The Tenth AAAI Symposium on
     Karro, and D Sculley. 2017. Google vizier: A service for black-box optimization.                 Educational Advances in Artificial Intelligence, EAAI 2020, New York, NY, USA,
     In Proceedings of the 23rd ACM SIGKDD International Conference on Knowledge                      February 7-12, 2020. AAAI Press, 4763–4771. https://aaai.org/ojs/index.php/
     Discovery and Data Mining. ACM, 1487–1495.                                                       AAAI/article/view/5910
[20] Google. 2020. Google Prediction API,. https://developers.google.com/prediction.             [47] Yang Li, Yu Shen, Jiawei Jiang, Jinyang Gao, Ce Zhang, and Bin Cui. 2020.
[21] Goetz Graefe. 1994. Volcano—An Extensible and Parallel Query Evaluation                          MFES-HB: Efficient Hyperband with Multi-Fidelity Quality Measurements.
     System. IEEE Transactions on Knowledge and Data Engineering (1994). https:                       arXiv:2012.03011 [cs.LG]
     //doi.org/10.1109/69.273032                                                                 [48] Richard Liaw, Eric Liang, Robert Nishihara, Philipp Moritz, Joseph E Gonzalez,
[22] Xin He, Kaiyong Zhao, and Xiaowen Chu. 2020. AutoML: A Survey of the                             and Ion Stoica. 2018. Tune: A Research Platform for Distributed Model Selection
     State-of-the-Art. arXiv:1908.00709 [cs.LG]                                                       and Training. arXiv preprint arXiv:1807.05118 (2018).
[23] Yi-Qi Hu, Yang Yu, Wei-Wei Tu, Qiang Yang, Yuqiang Chen, and Wenyuan Dai.                   [49] Edo Liberty, Zohar Karnin, Bing Xiang, Laurence Rouesnel, Baris Coskun, Ramesh
     2019. Multi-Fidelity Automatic Hyper-Parameter Tuning via Transfer Series                        Nallapati, Julio Delgado, Amir Sadoughi, Yury Astashonok, Piali Das, et al. 2020.
     Expansion. AAAI (2019).                                                                          Elastic Machine Learning Algorithms in Amazon SageMaker. In Proceedings of the
[24] Frank Hutter, Hoiger Hoos, and Kevin Leyton-Brown. 2014. An efficient approach                   2020 ACM SIGMOD International Conference on Management of Data. 731–737.
     for assessing hyperparameter importance. In 31st International Conference on                [50] Sijia Liu, Parikshit Ram, Djallel Bouneffouf, Gregory Bramble, Andrew R Conn,
     Machine Learning, ICML 2014.                                                                     Horst Samulowitz, and Alexander G Gray. 2020. An ADMM Based Framework
[25] Frank Hutter, Holger H Hoos, and Kevin Leyton-Brown. 2011. Sequential model-                     for AutoML Pipeline Configuration. (2020), 4892–4899.
     based optimization for general algorithm configuration. In International Confer-            [51] Akshay Naresh Modi et al. 2017. TFX: A TensorFlow-Based Production-Scale
     ence on Learning and Intelligent Optimization. Springer, 507–523.                                Machine Learning Platform. In KDD 2017.
[26] Frank Hutter, Lars Kotthoff, and Joaquin Vanschoren (Eds.). 2018. Automated                 [52] Felix Mohr, Marcel Wever, and Eyke Hüllermeier. 2018. ML-Plan: Automated
     Machine Learning: Methods, Systems, Challenges. Springer. In press, available at                 machine learning via hierarchical planning. Machine Learning (2018). https:
     http://automl.org/book.                                                                          //doi.org/10.1007/s10994-018-5735-z
                                                                                             9

                                                                                                                                                                           Li et al.

[53] Philipp Moritz, Robert Nishihara, Stephanie Wang, Alexey Tumanov, Richard                [68] C. Thornton, F. Hutter, H. H. Hoos, and K. Leyton-Brown. 2013. Auto-WEKA:
     Liaw, Eric Liang, Melih Elibol, Zongheng Yang, William Paul, Michael I. Jordan,               Combined Selection and Hyperparameter Optimization of Classification Algo-
     and Ion Stoica. 2007. Ray: A distributed framework for emerging AI applications.              rithms. In Proc. of KDD-2013. 847–855.
     In Proceedings of the 13th USENIX Symposium on Operating Systems Design and              [69] Jan N. Van Rijn and Frank Hutter. 2018. Hyperparameter importance across
     Implementation, OSDI 2018. arXiv:1712.05889                                                   datasets. In Proceedings of the ACM SIGKDD International Conference on Knowl-
[54] Supun Nakandala et al. 2019. Incremental and Approximate Inference for Faster                 edge Discovery and Data Mining. https://doi.org/10.1145/3219819.3220058
     Occlusion-Based Deep CNN Explanations. In SIGMOD.                                             arXiv:1710.04725
[55] Supun Nakandala et al. 2020. Cerebro: A Data System for Optimized Deep                   [70] Joaquin Vanschoren. 2018. Meta-Learning: A Survey. CoRR abs/1810.03548 (2018).
     Learning Model Selection. PVLDB (2020).                                                       arXiv:1810.03548 http://arxiv.org/abs/1810.03548
[56] Fatemeh Nargesian, Horst Samulowitz, Udayan Khurana, Elias B. Khalil, and                [71] Joaquin Vanschoren, Jan N. van Rijn, Bernd Bischl, and Luis Torgo. 2014. OpenML:
     Deepak Turaga. 2017. Learning feature engineering for classification. In IJCAI                Networked Science in Machine Learning. SIGKDD Explor. Newsl. 15, 2 (June 2014),
     Int. Jt. Conf. Artif. Intell., Vol. 0. https://doi.org/10.24963/ijcai.2017/352                49–60. https://doi.org/10.1145/2641190.2641198
[57] Randal S Olson and Jason H Moore. 2019. TPOT: A tree-based pipeline opti-                [72] Manasi Vartak et al. 2016. ModelDB: A System for Machine Learning Model
     mization tool for automating machine learning. In Automated Machine Learning.                 Management. In HILDA.
     Springer, 151–160.                                                                       [73] Ziyu Wang, Masrour Zoghi, Frank Hutter, David Matheson, and Nando De Freitas.
[58] Matthias Poloczek, Jialei Wang, and Peter Frazier. 2017. Multi-information source             2013. Bayesian optimization in high dimensions via random embeddings. In
     optimization. In Advances in Neural Information Processing Systems. 4288–4298.                Twenty-Third International Joint Conference on Artificial Intelligence.
[59] Alexander Ratner et al. 2017. Snorkel: Rapid Training Data Creation with Weak            [74] Martin Wistuba, Nicolas Schilling, and Lars Schmidt-Thieme. 2016. Two-stage
     Supervision. PVLDB (2017).                                                                    transfer surrogate model for automatic hyperparameter optimization. In Joint
[60] Theodoros Rekatsinas et al. 2017. HoloClean: Holistic Data Repairs with Proba-                European conference on machine learning and knowledge discovery in databases.
     bilistic Inference. PVLDB (2017).                                                             Springer, 199–214.
[61] Microsoft Research. 2020. Microsoft NNI. https://github.com/Microsoft/nni.               [75] Jian Wu, Saul Toscano-Palmerin, Peter I. Frazier, and Andrew Gordon Wilson.
[62] Kevin Schawinski et al. 2017. Generative Adversarial Networks recover features                2019. Practical multi-fidelity Bayesian optimization for hyperparameter tuning.
     in astrophysical images of galaxies beyond the deconvolution limit. MNRAS                     arXiv:1903.04703
     Letters (2017).                                                                          [76] Jian Wu, Saul Toscanopalmerin, Peter I Frazier, and Andrew Gordon Wilson.
[63] Rajat Sen, Kirthevasan Kandasamy, and Sanjay Shakkottai. 2018. Noisy Black-                   2019. Practical multi-fidelity Bayesian optimization for hyperparameter tuning.
     box Optimization with Multi-Fidelity Queries: A Tree Search Approach. arXiv:                  (2019), 284.
     Machine Learning (2018).                                                                 [77] Renzhi Wu et al. 2020. ZeroER: Entity Resolution Using Zero Labeled Examples.
[64] Bobak Shahriari, Kevin Swersky, Ziyu Wang, Ryan P. Adams, and Nando De Fre-                   In SIGMOD.
     itas. 2016. Taking the human out of the loop: A review of Bayesian optimization.         [78] Weiyuan Wu et al. 2020. Complaint-Driven Training Data Debugging for Query
     https://doi.org/10.1109/JPROC.2015.2494218                                                    2.0. In SIGMOD.
[65] Jasper Snoek, Hugo Larochelle, and Ryan P Adams. 2012. Practical bayesian                [79] Quanming Yao, Mengshuo Wang, Hugo Jair Escalante, Isabelle Guyon, Yi-Qi Hu,
     optimization of machine learning algorithms. In Advances in neural information                Yu-Feng Li, Wei-Wei Tu, Qiang Yang, and Yang Yu. 2018. Taking Human out of
     processing systems. 2951–2959.                                                                Learning Applications: A Survey on Automated Machine Learning. CoRR (2018).
[66] Kevin Swersky, Jasper Snoek, and Ryan P Adams. 2013. Multi-task bayesian                 [80] M. Zaharia et al. 2018. Accelerating the Machine Learning Lifecycle with MLflow.
     optimization. In Advances in neural information processing systems. 2004–2012.                IEEE Data Eng. Bull. (2018).
[67] Shion Takeno, Hitoshi Fukuoka, Yuhki Tsukada, Toshiyuki Koyama, Mo-                      [81] Ce Zhang et al. 2017. DeepDive: Declarative Knowledge Base Construction.
     toki Shiga, Ichiro Takeuchi, and Masayuki Karasuyama. 2020. Multi-fidelity                    Commun. ACM (2017).
     Bayesian Optimization with Max-value Entropy Search and its parallelization.             [82] Marc-André Zöller and Marco F. Huber. 2019. Survey on Automated Machine
     arXiv:1901.08275 [stat.ML]                                                                    Learning. CoRR abs/1904.12054 (2019).

                                                                                         10
