---
citation_key: "LiEtAl2022a"
title: "Efficient End-to-End AutoML via Scalable Search Space Decomposition"
authors: "Yang Li; Yu Shen; Wentao Zhang; Ce Zhang; Bin Cui"
year: 2022
doi: "10.1007/s00778-022-00752-2"
source: "arXiv (2206.09423)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "2206.09423"
conversion: "pdftotext -layout (automated)"
---

# Efficient End-to-End AutoML via Scalable Search Space Decomposition

Noname manuscript No.
                                         (will be inserted by the editor)

                                         Efficient End-to-End AutoML via Scalable Search Space
                                         Decomposition (Extended Paper)
                                         Yang Li∗ , Yu Shen∗ , Wentao Zhang∗ , Ce Zhang† , Bin Cui

arXiv:2206.09423v2 [cs.LG] 24 Jun 2022
                                         Received: date / Accepted: date

                                         Abstract End-to-end AutoML has attracted intensive            model – akin to the one supported by modern database
                                         interests from both academia and industry which auto-         systems – to execute the plan constructed. Our evalu-
                                         matically searches for ML pipelines in a space induced        ation demonstrates that, not only does VolcanoML
                                         by feature engineering, algorithm/model selection, and        raise the level of expressiveness for search space decom-
                                         hyper-parameter tuning. Existing AutoML systems,              position in AutoML, it also leads to actual findings of
                                         however, suffer from scalability issues when applying         decomposition strategies that are significantly more ef-
                                         to application domains with large, high-dimensional           ficient than the ones employed by state-of-the-art Au-
                                         search spaces. We present VolcanoML, a scalable and           toML systems such as auto-sklearn. This paper is the
                                         extensible framework that facilitates systematic explo-       extended version of the initial VolcanoML paper ap-
                                         ration of large AutoML search spaces. VolcanoML in-           peared in VLDB 2021.
                                         troduces and implements basic building blocks that de-
                                                                                                       Keywords Applied Machine Learning for Data
                                         compose a large search space into smaller ones, and al-
                                                                                                       Management · Scalable Data Science · Automatic
                                         lows users to utilize these building blocks to compose an
                                                                                                       Machine Learning · Data Mining and Analytics
                                         execution plan for the AutoML problem at hand. Vol-
                                         canoML further supports a Volcano-style execution
                                         ∗                                                             1 Introduction
                                          Yang Li
                                         Institute: School of CS & Key Laboratory of High Confidence
                                         Software Technologies (MOE), Peking University                In recent years, researchers in the database community
                                         Institute: Tencent data platform, TEG, Tencent inc.           have been working on raising the level of abstractions
                                         E-mail: liyang.cs@pku.edu.cn                                  of machine learning (ML) and integrating such func-
                                         ∗
                                          Yu Shen                                                      tionality into today’s data management systems [95,
                                         Institute: School of CS & Key Laboratory of High Confidence   96], e.g., SystemML [25], SystemDS [8], Snorkel [71],
                                         Software Technologies (MOE), Peking University
                                         E-mail: shenyu@pku.edu.cn
                                                                                                       ZeroER [91], TFX [5, 9], Query 2.0 [92], Krypton [66],
                                         ∗
                                                                                                       Cerebro [67], ModelDB [86], MLFlow [94], Deep-
                                          Wentao Zhang
                                         Institute: School of CS & Key Laboratory of High Confidence
                                                                                                       Dive [14], HoloClean [72], EaseML [1], ActiveClean [48],
                                         Software Technologies (MOE), Peking University                and NorthStar [47]. End-to-end AutoML systems [93,
                                         Institute: Tencent data platform, TEG, Tencent inc.           97, 33] have been an emerging type of systems that has
                                         E-mail: wentao.zhang@pku.edu.cn                               significantly raised the level of abstractions of build-
                                         †
                                           Ce Zhang                                                    ing ML applications. Given an input dataset and a
                                         Institute: Department of Computer Science, ETH Zürich        user-defined utility metric (e.g., validation accuracy),
                                         E-mail: ce.zhang@inf.ethz.ch
                                                                                                       these systems automate the search of an end-to-end
                                         Corresponding author: Bin Cui                                 ML pipeline, including feature engineering, algorith-
                                         Institute: Department of Computer Science and Technology
                                         & Key Laboratory of High Confidence Software Technologies
                                                                                                       m/model selection, and hyper-parameter tuning. Open-
                                         (MOE), Peking University                                      source examples include auto-sklearn [22], TPOT [69],
                                         Tel: +86-10-62765821                                          and hyperopt-sklearn [46], whereas most cloud ser-
                                         E-mail: bin.cui@pku.edu.cn                                    vice providers, e.g., Google, Microsoft, Amazon, etc., all

2                                                                   Yang Li, Yu Shen, Wentao Zhang, Ce Zhang, Bin Cui

provide their proprietary services on the cloud. As ma-                                     Joint Space
chine learning has become an increasingly indispensable                               (Algorithm, Feature, HP)
functionality integrated in modern data (management)
systems, an efficient and effective end-to-end AutoML                    Strategy 1      Strategy 2   Strategy 3
component also becomes increasingly important.                           Algorithm      Algorithm      Feature
                                                                                                                   ...
    End-to-end AutoML provides a powerful abstrac-
                                                                        Feature, HP       Feature      Alg, HP
tion to automatically navigate and search in a given
complex search space. However, in our experience of ap-                                     HP
plying state-of-the-art end-to-end AutoML systems in a
range of real-world applications [2], we find that such a     Fig. 1 Different decomposition choices.
system running fully automatically is rarely enough —
often, developing a successful ML application involves            Many existing systems such as auto-sklearn [22]
multiple iterations between a user and an AutoML sys-         and TPOT [69] deal with the entire composite search
tem to iteratively improve the resulting ML artifact.         space jointly, which naturally leads to the scalability
                                                              bottleneck. Decomposing a joint space has been ex-
Motivating Practical Challenge. One such type                 plored for some subspaces (e.g., only algorithm and
of interaction, which inspires this work, is the              hyper-parameters as in [63, 53]), however, none of them
enrichment of search space. We observe that the de-           has been applied to a search space as large as that of
fault search space provided by state-of-the-art AutoML        end-to-end AutoML. One challenge is that there exist
systems is often not enough in many applications. This        many different ways to decompose the same space (See
was not obvious to us at all in the beginning and it is not   Figure 1), as shown above, but only some of them can
until we finish building a range of real-world applica-       perform well. Without a structured, high-level abstractions that we realize this via a set of concrete examples.    tion for search space decomposition to explore different
For example, in one of our astronomy applications [75],       strategies, it is very hard to scale up an end-to-end Authe feature normalization function is domain-specific         toML system to accommodate the search space that will
and not supported by most, if not all, AutoML systems.        only get larger in the future.
Similar examples can also be found when searching for
suitable ML models via AutoML. In one of our me-              Summary of Contributions. The initial version of
teorology applications, we need to extend the models          this paper [59] appeared in VLDB 2021, where we fowith meteorology-specific loss functions. We saw simi-        cused on designing the system, VolcanoML, which is
lar problems when we tried to extend existing AutoML          scalable to a large search space. In this paper, we make
systems with pre-trained feature embeddings coming            the following four additional contributions: First, we
from TensorFlow Hub, to include include models that           provide the automatic execution plan generation modhave been newly published on arXiv to enrich the Model        ule (in Section 4.2) to enrich the proposed framework,
Base [52], or to support Cosine annealing as for tuning.      and discuss the advantages and underlying problems
                                                              in this direction. Second, we propose the meta-learning
                                                              based components for the building blocks (in Section 5)
Technical Challenge: Scalability over the Search              to further speed up VolcanoML. Third, we conduct
Space. “Why is it hard to extend the search space, as         a comprehensive set of experiments (in Section 6) to
a user, in an end-to-end AutoML system?” The an-              demonstrate the effectiveness and efficiency of Volswer to this question is a complex one that is not            canoML, and provide the results about automatic plan
completely technical: some aspects are less technical         generation and meta-learning based acceleration. Fisuch as engineering decisions and UX designs, however,        nally, we provide more details about system compothere are also more technically fundamental aspects.          nents, implementations (interfaces) and search spaces
An end-to-end AutoML system contains an optimiza-             in Section A of the appendix. Our technical contribution algorithm that navigates a joint search space in-        tions are as follows.
duced by feature engineering, algorithm selection, and
hyper-parameter tuning. Because of this joint nature,         C1. System Design: A Structured View on Decomposithe search space of end-to-end AutoML is complex and          tion. The main technical contribution of VolcanoML
huge while the enrichment is only going to make it even       is to provide a flexible and principled way of decomlarger. As we will see, handling such a huge space is al-     posing a large search space into multiple smaller ones.
ready challenging for existing systems, and further en-       We propose a novel system abstraction: a set of Volriching it will make it even harder to scale.                 canoML building blocks (Section 3), each of which

Efficient End-to-End AutoML via Scalable Search Space Decomposition (Extended Paper)                                 3

takes charge of a smaller sub-search space whereas a        given a workload, just like what a classic query opti-
VolcanoML execution plan (Section 4) consists of a          mizer would do for relational queries. For constrained
tree of such building blocks — the root node corre-         optimizations, we also imagine techniques similar to
sponds to the original search space and its child nodes     traditional “push-down selection” could be applied in
correspond to different subspaces. Under this abstrac-      a similar spirit. We explore the possibility of automatition, optimizing in the joint space is conducted as op-     cally searching for the best plan in Section 4 and discuss
timization problems over different smaller subspaces.       the limitations of this simple strategy and the exciting
The execution model is similar to the classic Volcano       line of future work that could follow. While the full
query evaluation model in a relational database [24]        treatment of these aspects are beyond the scope of this
(thus the name VolcanoML): the system asks the              paper, we hope the VolcanoML abstraction can serve
root node to take one iteration in the optimization pro-    as a foundation for these future endeavors.
cess, which recursively invokes one of its child nodes
to take one iteration on solving a smaller-scale optimization problem over its own subspace; this recursive      2 Related Work
invocation procedure will continue until a leaf node is
                                                            AutoML is a topic that has been intensively studied
reached. This flexible abstraction allows us to explore
                                                            over the last decade. We briefly summarize related
different ways that the same joint space can be decom-
                                                            work in this section and readers can consult latest surposed. Together with the meta-learning based optimiza-
                                                            veys [33, 93, 97, 29] for more details.
tions (Section 5), VolcanoML can often support more
scalable search process than the existing AutoML sys-
                                                            End-to-End AutoML. End-to-end AutoML, the fotems such as auto-sklearn and TPOT.
                                                            cus of this work, aims to automate the development
                                                            process of the end-to-end ML pipeline, including feature
C2. Large-scale Empirical Evaluations. We conducted         preprocessing, feature engineering, algorithm selection,
intensive empirical evaluations, comparing Vol-             and hyper-parameter tuning. Often, this is modeled
canoML with state-of-the-art systems including              as a black-box optimization problem [34] and solved
auto-sklearn and TPOT. We show that (1) under               jointly [22, 82, 69]. Apart from grid search and random
the same search space as auto-sklearn, VolcanoML            search [6], genetic programming [64, 69] and Bayesian
significantly outperforms auto-sklearn and TPOT —           optimization (BO) [7, 32, 79, 20, 77] has become prevailover 30 classification tasks and 20 regression tasks —      ing frameworks for this problem. One challenge of end-
VolcanoML outperforms the best of auto-sklearn              to-end AutoML is the staggeringly huge search space
and TPOT on a majority of tasks; concretely, Vol-           that one has to support and many of these methods
canoML could achieve a higher balanced accuracy             suffer from scalability issues [57, 55]. In addition, metafor classification tasks and a smaller mean square er-      learning [84, 56, 23] systematically investigates the inror for regression tasks given the same time bud-           teractions that different ML approaches perform on
get; (2) using an enriched search space with addi-          a wide range of learning tasks, and then learns from
tional feature engineering operators, VolcanoML             this experience, to accomplish new tasks much faster.
performs significantly better than auto-sklearn; (3)        Several meta-learning approaches [74, 31, 83, 22, 54] can
using an enriched search space with an additional           guide ML practitioners to design better search spaces
data processing stage and functionalities beyond what       for AutoML tasks.
auto-sklearn and TPOT currently support (i.e., an ad-           Many end-to-end AutoML systems have raised
ditional embedding selection stage using pre-trained        the abstraction level of ML. auto-weka [82],
models on TensorFlow Hub), VolcanoML can deal               hyperopt-sklearn [46], and auto-sklearn [22] are the
with input types such as images efficiently; and (4)        main representatives of BO-based AutoML systems.
VolcanoML is at least comparable with and often             auto-sklearn is one of the most popular open-source
outperforms four industrial AutoML platforms on six         frameworks. TPOT [69] and ML-Plan [64] use genetic
Kaggle competitions.                                        algorithms and hierarchical task networks planning,
                                                            respectively, to optimize over the pipeline space, and
                                                            require discretization of the hyper-parameter space.
Moving Forward. The VolcanoML abstraction en-               AlphaD3M [18] integrates reinforcement learning with
ables a structured view of optimizing a black-box func-     Monte Carlo tree search (MCTS) to solve AutoML
tion via decomposition. This structured view itself         problems but without imposing efficient decomposiopens up interesting future directions. For example, one    tion over hyper-parameters and algorithm selection.
may wish to automatically decompose a search space          AutoStacker [13] focuses on ensembling and cascading

4                                                                         Yang Li, Yu Shen, Wentao Zhang, Ce Zhang, Bin Cui

to generate complex pipelines, and solves the CASH                                                                              Feature Engineering

(Combined Algorithm Selection and Hyperparameters                        preprocessor         scaler     balancer          transformer

optimization) problem [22] via random search. ML                                              … Robust
                                                                              …         Std               …         PCA   SVD   RFE … Percentile
Bazaar [78] is a general-purpose, multi-task, end-to-end
AutoML system, which pair ML pipelines with a hier-                 Fig. 2 The search space of FE pipeline.
archy of AutoML strategies – Bayesian optimization.
Furthermore, a growing number of commercial enterprises also export their AutoML services to their users,
e.g., H2O [49], Microsoft’s Azure Machine Learning [4],             given search space. In this section, we first introduce
Google Cloud’s AI Platform [27], Amazon Machine                     key building blocks in VolcanoML, and in Section 4
Learning [61] and IBM’s Watson Studio AutoAI [35].                  we describe how multiple building blocks are put to-
                                                                    gether to compose a VolcanoML execution plan in
Automating Individual Components. Apart from                        a modular way. Later in Section 5, we introduce addiend-to-end AutoML, many efforts have been de-                       tional optimizations for these building blocks.
voted to studying sub-problems in AutoML: (1) feature engineering [44, 42, 41, 68, 43], (2) algorithm selection [82, 46, 22, 19, 63, 53], and (3) hyper-parameter           3.1 Search Space of End-to-End AutoML
tuning [32, 79, 7, 51, 36, 21, 57, 80, 45,39,70, 30, 76, 90, 37].
Meta-learning methods [89, 26, 23] for hyper-parameter              We describe the search space of end-to-end AutoML foltuning can leverage auxiliary knowledge acquired from               lowing the presentation in auto-sklearn[22]. The inprevious tasks to achieve faster optimization. Several              put to the system is a dataset D, containing a set of
systems offer a subset of functionalities in the end-               training samples. The user also provides a pre-defined
to-end process. Microsoft’s NNI [73] helps users to                 metric, e.g., validation accuracy or cross-validation acautomate feature engineering, hyper-parameter tun-                  curacy, to measure the utility of a given ML pipeline.
ing, and model compression. Recent work [63] lever-                 The output of an end-to-end AutoML system is an ML
ages the ADMM optimization framework to decompose                   pipeline that achieves good utility.
the CASH problem [22], and solves two easier sub-                       To find such an ML pipeline, the system searches
problems. Berkeley’s Ray [65] and OpenBox [58] pro-                 over a large search space of possible pipelines and
vide the tune module [60, 57] to support scalable hyper-            picks one that maximizes the pre-defined utility. This
parameter tuning tasks in a distributed environment.                search space is a composition of (1) feature engineering
Featuretools [40] is a Python library for automatic fea-            operators, (2) ML algorithms/models, and (3) hyperture engineering. Unlike these works, in this paper, we             parameters.
focus on deriving an end-to-end solution to the AutoML
problem, where the sub-problems are solved in a joint               Feature Engineering. The feature engineering promanner.                                                             cess takes as input a dataset D and outputs a
                                                                    new dataset D0 . It achieves this by transforming
Volcano Model. The Volcano model [28] (originally
                                                                    the input dataset via a set of data transformaknown as the Iterator Model) is the classical evaluation
                                                                    tions. The pipeline for feature engineering is shown
strategy of an analytical DBMS query: Each relational-
                                                                    in Figure 2. It comprises four sequential stages: prealgebraic operator produces a tuple stream, and a con-
                                                                    processors (compulsory), scalers (5 possible operasumer can iterate over its input streams. The tuple
                                                                    tors), balancers (1 possible operators) and feature
stream has three interfaces: open, next and close; all
                                                                    transformers (13 possible operstors). For each stage,
operators own the same interface, and the implementa-
                                                                    the system chooses a single transformation to aption is opaque. It is a chain of iterators and data flows
                                                                    ply. For example, for feature transforming, the systhrough them when the topmost iterator calls next()
                                                                    tem can choose among no processing, kernel pca,
on the iterator below it. This results in propagation of
                                                                    polynomial, select percentile, etc.
next() calls till the bottom-most iterator is called.
                                                                    ML Algorithms. Given a transformed dataset D0 ,
3 VolcanoML and Building Blocks                                     the system then picks an ML algorithm to train. Since
                                                                    different ML algorithms are suitable for different types
The goal of VolcanoML is to enable scalability with                 of tasks, the system needs to consider a diverse range
respect to the underlying AutoML search space. As                   of possible ML algorithms. Taking auto-sklearn
a result, its design focuses on the decomposition of a              as an example, the search space for ML algorithms

Efficient End-to-End AutoML via Scalable Search Space Decomposition (Extended Paper)                                     5

contains Linear Model, Support Vector Machine,              composition strategy is akin to an execution plan in re-
Discriminant Analysis, Random Forest, etc.                  lational database management systems, which is com-
                                                            posed of building blocks akin to relational operators.
Hyper-parameters. Each ML algorithm has its own             A building block itself can be viewed as an atomic desub-search space for hyper-parameter tuning — if we         composition strategy. We next present the details of the
choose to use a certain ML algorithm, we also have          building blocks implemented by VolcanoML, and we
to specify the corresponding hyper-parameters. The          will introduce how to use these blocks to compose Volhyper-parameters fall into three categories: continuous     canoML execution plans in Section 4.
(e.g., sub-sample rate for Random Forest), discrete
(e.g., maximal depth for Decision Tree), and categor-       Goal. The goal of VolcanoML is to solve:
ical (e.g., kernel type for Lib SVM).
    (AutoML optimization.) If the system makes a              min f (x1 , ..., xn ; D),                                (1)
                                                            x1 ,...,xn
concrete pick for each of the above decisions, then it
can compose a concrete ML pipeline and evaluate its         where x1 , ..., xn is a set of n variables and each of them
utility. Concretely, given a pipeline configuration that    has domain Dxi for i ∈ [n]. Together, Q    these n variables
determines the details of feature engineering, algorithm    define a search space (x1 , ..., xn ) ∈ i Dxi . D correand hyperparameters, we could construct a specific ML       sponds to the input dataset, which is a set of input
pipeline. Then we need to train a corresponding ML          samples. In AutoML, the variables x1 , ..., xn are actumodel within this pipeline, and evaluate its perfor-        ally the pipeline hyperparameters, and the search space
mance on the validation set to obtain the utility of this   is the complete pipeline search space, which is a compopipeline configuration. This is often an expensive pro-     sition of feature engineering operators, ML algorithmcess since it involves training an ML model. To find the    s/models, and hyper- parameters. The optimization oboptimal ML pipeline, the system evaluates the utility of    jective f is to minimize the validation loss (e.g., classidifferent ML pipelines in an iterative manner following     fication error), i.e., the objective function f (·) in Fora search strategy, and picks the one that maximizes the     mula 1. In our setting, f (·) is a black-box function that
utility. The candidates for search strategy can be ran-     we can only evaluate (but not exploiting the derivadom search [6], grid search, genetic algorithms [64, 69],   tive), and the objective is to solve min f (·) as quickly as
Bayesian optimization [7, 32, 79], bandits based meth-      possible. Given a fixed c (i.e.,Q  a concrete ML pipeline)
ods [36, 51], etc.                                          in the composite domain c ∈ i Dxi , we use the nota-
    For example, auto-sklearn handles the above             tion f (c; D) as the value of evaluating f by substituting
search space jointly and optimizes it with Bayesian op-     (x1 , ...xn ) with c.
timization (BO) [77]. Given an initial set of function
evaluations, BO proceeds by fitting a surrogate model
to those observations, specifically a probabilistic Ran-    Subgoal. One key decision of VolcanoML is to solve
dom Forest in auto-sklearn, and then chooses which          the optimization problem on a search space by decom-
ML pipeline to evaluate from the search space by op-        posing it into multiple smaller subspaces, each of which
timizing an acquisition function that balances explo-       will be solved by one building block. We define optimizration and exploitation.                                    ing over each of these smaller subspaces as a subgoal of
                                                            the original problem. Formally, a subgoal g is defined
                                                            by two components: x̄g ⊆ {x1 , ...xn } as a subset of vari-
                                                                             Q
3.2 Building Blocks                                         ables, and c̄g ∈ xi ∈x̄g Dxi as an assignment in the do-
                                                            main of all variables in x̄g . Let x̄−g = {x1 , ..., xn } − x̄g
Unlike auto-sklearn, VolcanoML decomposes the               be all variables that are not in x̄g .
above search space into smaller subspaces. (Key idea.)          Each subgoal defines a function fg over a smaller
Instead of searching over a huge pipeline space, it could   search space, which is constructed by substituting all
be easier for an algorithm to optimize over its sub-        variables in x̄g with c̄g :
spaces. Decomposing a joint space has been explored
in many domains [63, 53]. The way how to decompose          fg =f [x̄g /c̄g ] :
the pipeline space into subspaces in field of AutoML            z∈
                                                                        Y
                                                                                Dxi 7→ f ({c̄g ; z}; D).               (2)
is still remains open. Next, we propose a structured                     xi ∈x̄−g
and high-level abstraction to support scalable search
space decomposition. One interesting design decision        Each subgoal is a sub-problem in the ML pipeline search
in VolcanoML is to introduce a structured abstrac-          of AutoML such as feature engineering, algorithm setion to express different decomposition strategies. A de-   lection, etc.

6                                                                   Yang Li, Yu Shen, Wentao Zhang, Ce Zhang, Bin Cui

Building Block. Each subgoal g corresponds to one             3.3 Three Types of Building Blocks
building block Bg,D , whose goal is to solve
min fg (x̄−g ; D).                                     (3)    Decomposition is the cornerstone of VolcanoML’s
x̄−g                                                          design. Given a search space, apart from exploring it
A building block Bg,D imposes several assumptions on          jointly, there are two classical ways of decomposition
g and D. First, given an assignment c̄−g to x̄−g , it is      — to partition the search space via conditioning on difable to evaluate the value of the function fg (c̄−g , D).     ferent values of a certain variable (in a similar spirit of
Second, given a dataset D, a building block has the           variable elimination [15]), or to decompose the problem
knowledge about how to subsample a smaller dataset            into multiple smaller ones by introducing equality con-
D̃ ⊆ D and then conduct evaluations on such a sub-            straints (in a similar spirit of dual decomposition [11]).
set x 7→ fg (x; D̃). Third, we assume that the building       This inspires VolcanoML’s design, which supports
block has access to a cost model about the cost of an         three types of building blocks: (1) joint block that simevaluation at x, Cg,D,x .                                     ply optimizes the input subspace using Bayesian op-
                                                              timization; (2) conditioning block that further divides
Interfaces. All implementations of a building block fol-      the input subspace into smaller ones by conditioning on
low an interactive optimization process. A building           one particular input variable; and (3) alternating block
block exposes several interfaces. First, one can initialize   that partitions the input subspace into two and optia building block via                                          mizes each one alternately. Note that both conditioning
Bg,D ← init(f, x̄g , c̄g , D),                         (4)    block and alternating block would generate new build-
                                                              ing blocks with smaller subgoals. We next present the
which creates a building block (i.e., a new sub-
                                                              implementation details for each type of building block.
problem). Second, one can query the current best solution found in Bg,D by
x̂ ← get current best(Bg,D ).                          (5)    3.3.1 Joint Block
Furthermore, one can ask Bg,D to iterate once via
                                                              A joint block directly optimizes its subgoal via Bayesian
do next!(Bg,D ),                                       (6)    optimization (BO) [77]. Specifically, BO based method
where ‘!’ indicates potential change on the state of the      - SMAC [32] has been used by many applications where
input Bg,D .                                                  evaluating the objective function is computationally ex-
   Last but not least, one can query a building block         pensive. It constructs a probabilistic surrogate model
about its expected utility (EU) if given K more budget        M to capture the relationship between the input variunits (e.g., seconds) via                                     ables x̄ (i.e., hyperparamters in AutoML) and the ob-
                                                              jective function value ψ (e.g., the validation loss), and
[l, u] ← get eu(Bg,D , K).                             (7)    this surrogate model is utilized to suggest a new promis-
By adopting a similar design principle used in the ex-        ing configuration to evaluate for each iteration. It then
isting AutoML systems [22, 69, 63], in VolcanoML we           refines M iteratively using past evaluation observations
estimate EU by extrapolation into the future with more        (x̄, ψ).
available budget. Given the inherent uncertainty in our            Based on the BO framework, the implementation
estimation method, rather than returning a single point       of do next! for a joint block consists of the following
estimate, we instead return a lower bound l and an up-        three steps:
per bound u. We refer readers to [53] for the details of
how the lower and upper bounds are established. More-         1. Use the surrogate model M to select a configuraover, one can query a building block about its expected          tion x̄ that maximizes an acquisition function. In
utility improvement (EUI) via                                    our implementation, we use expected improvement
                                                                 (EI) [38] as the acquisition function, which has been
δ ← get eui(Bg,D ).                                    (8)       widely used in BO community.
Note that, different from EU, EUI is the expected im-         2. Evaluate the selected configuration x̄ and obtain
provement over the current observed utility if given K           its result about the objective function fg (x̄) (i.e.,
more budget units. While sharing some similarity with            the subgoal). Due to the randomness of most ML
EI in BO, EUI works on the level of optimization pro-            algorithms, we assume that f (x) cannot be obcess (building blocks), while EI in BO is implemented            served directly but rather through noisy observation
for one single iteration in BO. In VolcanoML, we esti-           ψ = fg (x̄) + , with  ∼ N (0, σ 2 ), where N is the
mate EUI by taking the mean of the observed improve-             normal distribution.
ments from history, following Levine et al [50].              3. Refit the surrogate model M on the observed (x̄, ψ).

Efficient End-to-End AutoML via Scalable Search Space Decomposition (Extended Paper)                                        7

 Algorithm 1: The do next! of conditioning                       Algorithm 2: The init of alternating block
 block                                                             Input: An alternating block Bg,D with search space
   Input: A conditioning block Bg,D , times to play                         x̄ = ȳ ∪ z̄.
             each arm L, total budget K.                         1 Initialize ȳ and z̄ with default values ȳ0 and z̄0 ;
 1 Let B1 , ..., Bm be all active (have not been                 2 B1 ← init(f, z̄, z̄0 , D);
    eliminated) child blocks;                                    3 B2 ← init(f, ȳ, ȳ0 , D);
 2 for 1 ≤ i ≤ L do                                              4 for 1 ≤ i ≤ L do
 3     for 1 ≤ j ≤ m do                                          5     do next(B1 );
 4           do next!(Bj );                                      6     ȳi ← get current best(B1 );
 5 for 1 ≤ j ≤ m do                                              7     set var(B2 , ȳ, ȳi );
 6     [lj , uj ] ← get eu(Bj , K);                              8     do next(B2 );
 7 Eliminate child blocks that are dominated by others,          9     z̄i ← get current best(B2 );
    using [lj , uj ] for 1 ≤ j ≤ m;                             10     set var(B1 , z̄, z̄i );

    Early-Stopping based Optimization. For large                The elimination works as follows. Consider two blocks
datasets, early-stopping based methods, e.g., Succes-           Bi and Bj : if the upper bound ui of Bi is less than the
sive Halving [36], Hyperband [51], BOHB [21], MFES-             lower bound lj of Bj , then the block Bi is eliminated.
HB [57], etc, can terminate the evaluations of poorly-          An eliminated arm/block will not be played in future
performing configurations in advance, thus speeding             invocations of do next!.
up the evaluations. VolcanoML supports MFES-                    Remark: We have simplified the above elimination cri-
HB [57], which combines the benefits of Hyperband and           terion by using the lower and upper bounds calculated
Multi-fidelity BO [90, 81], to optimize a joint block, in       given K budget units for each arm. In fact, these K
addition to vanilla BO.                                         budget units are shared by all the arms, and as a re-
                                                                sult, each arm actually has fewer budget units than K.
3.3.2 Conditioning Block                                        Our assumption is that, K is sufficiently large so that
                                                                one can play all arms until (the observed distribution of
A conditioning block decomposes its input x̄ into x̄ =          rewards of) each arm converges. Otherwise, the lower
{xc } ∪ ȳ, where xc is a single variable with domain Dxc .     and upper bounds obtained may be over-optimistic, and
It then creates one new building block for each possible        as a result, may lead to incorrect eliminations. Fortuvalue d ∈ Dxc of xc :                                           nately, our assumption usually holds in practice, where
                                                                arms converge relatively fast.
min gd (ȳ; D) ≡ f ({xc = d, ȳ}; D).                     (9)
 ȳ
                                                                3.3.3 Alternating Block
As a result, |Dxc | new (child) building blocks are created.                                                           An alternating block decomposes its input search space
    The conditioning block aims to identify optimal             into x̄ = ȳ ∪ z̄, and explores ȳ and z̄ in an alternatvalue for xc , and many previous AutoML researchers             ing way. Similarly, we also model the optimization in
have used Bandit algorithms for this purpose [63, 36,           alternating block as an MAB problem. Algorithm 2 il-
53,57]. In VolcanoML, we follow these previous work             lustrates how its init primitive works. It first creates
and model it as a multi-armed bandit (MAB) prob-                two child blocks B1 and B2 , which will focus on optilem, while our framework is flexible enough to incor-           mizing for ȳ and z̄ respectively (lines 1 to 3). It then
porate other algorithms when they are available. There          (again) views B1 and B2 as two arms and plays them
are |Dxc | arms, where each arm corresponds to a child          using Round-Robin (lines 4 to 10). Note that, when B1
block. Playing an arm means invoking the do next!               optimizes ȳ (resp. when B2 optimizes z̄), it uses the
primitive of the corresponding child block.                     current best z̄ found by B2 (resp. the current best ȳ
    Algorithm 1 illustrates the implementation of               found by B1 ). This is done by the set var primitive
do next! for a conditioning block. It starts by play-           (invoked at line 7 for B2 and line 10 for B1 ).
ing each arm L times in a Round-Robin fashion (lines                One problem of our alternating MAB formulation
2 to 4). Here, L is a user-specified configuration param-       is that the utility improvements of the two building
eter of VolcanoML. In our current implementation,               blocks often vary dramatically in practice. For example,
we set L = 5. We then obtain the lower and upper                some applications are very sensitive to the features bebounds of the expected utility of each child block by           ing used (e.g., normalized vs. non-normalized features)
invoking its get eu primitive (lines 5 to 6), and elimi-        while hyper-parameter tuning will offer little or even
nate child blocks that are dominated by others (line 7).        no improvement. In this case, we should spend more

8                                                                  Yang Li, Yu Shen, Wentao Zhang, Ce Zhang, Bin Cui

    Algorithm 3: The do next! of alternating                 not be played anymore, with the limitation that it can
    block                                                    only work for conditional variables that are categori-
   Input: An alternating block Bg,D with budget K.           cal. For non-categorical variables, one possible way to
   δ1 ← get eui(B1 );
    1                                                        use conditioning blocks is to split the value range of
 2 δ2 ← get eui(B2 );
                                                             variables. For example, given a numerical variable that
 3 if δ1 ≥ δ2 then
 4     z̄best ← get current best(B2 );                       ranges from 1 to 3, we split it into two ranges, which
 5     set var(B1 , z̄, z̄best );                            are [1, 2) and [2, 3). During the optimization iteration,
 6     do next(B1 );                                         we first choose one sub-range and then optimize the
 7 else
                                                             splitted space along with its corresponding subspace.
 8     ȳbest ← get current best(B1 );
 9     set var(B2 , ȳ, ȳbest );
                                                                 In addition, VolcanoML uses bandit-based al-
10     do next(B2 );                                         gorithms from the existing literature [50, 53] as de-
                                                             fault in both the alternating and conditioning block,
                                                             and other bandit-based algorithms, such as successive
resources on looking for good features instead of tun-       halving [36], Hyperband [51], BOHB [21] and MFESing hyper-parameters. Our key observation is that, the       HB [57], can also be used in these blocks.
expected utility improvement (EUI) decays as optimiza-
                                                             3.3.5 Discussion: Comparing Different Building Blocks
tion proceeds. As a result, we propose to use EUI as an
indicator that measures the potential of pulling an arm
                                                             Joint blocks are the default blocks that can be apfurther. Algorithm 3 illustrates the details of this idea
                                                             plied to all problems. When the search space is rather
when used to implement the do next! primitive.
                                                             large, conditioning and alternating blocks can be help-
    Specifically, Algorithm 3 starts by polling the EUI of
                                                             ful. If the search space contains a categorical hyperboth child blocks (lines 1 and 2). Recall that the EUI is
                                                             parameter, under which the subspace of each choice is
estimated by taking the mean of historic observations.
                                                             conditionally independent with each other, the condi-
It then compares the EUIs and picks the arm/block
                                                             tioning block can be used instead of exploring the enwith larger EUI to play next (lines 3 to 10). Before
                                                             tire space. If the search space can be decomposed into
pulling the winner arm, again it will use the current
                                                             two approximately independent subspaces, the alterbest settings found by the other arm/block (lines 4 to
                                                             nating block can be applied to this case. As a result,
6, lines 8 to 10).
                                                             a scalable system needs to be able to decompose the
                                                             problem in different ways and pick the most suitable
3.3.4 Discussion: Pros and Cons of Building Blocks           building blocks. This forms a VolcanoML execution
                                                             plan, which we will describe in the next section. In
While the joint block is the most straightforward way        Section 4, we explore the possibility of automatically
to solve the optimization problem associated, it is dif-     choosing building blocks to use by maximizing the emficult to scale Bayesian optimization to a large search      pirical accuracy of different execution plans, given a
space [88, 53]. The alternating block addresses this scal-   pre-defined set of datasets.
ability issue by decomposing the search space into two
smaller subspaces, though with the assumption that           3.3.6 Discussion: Continue Tuning in Conditional
the improvements of the two subspaces are condition-         Block
ally independent of each other. As a result, the alternating block is a better choice when such an assump-         As introduced in Section 3.3.2, in the conditional
tion approximately holds. The advantage of alternating       block of VolcanoML, we store the lower and upper
block with this assumption can solve the optimization        bounds of the expected utility of each child block. Volproblem efficiently by decomposing the huge and joint        canoML eliminates those potentially bad blocks based
search space into two smaller subspaces (efficiency).        on the two bounds. When new algorithms are added
While the issue behind this lies in that the alternat-       into the search space, we extend the previously suring block cannot converge to the optimal solution (ef-       vived candidate algorithm set in the conditional block
fectiveness) when the two subspaces are highly depen-        with those new algorithms and play each candidate in a
dent. We expect this assumption approximately hold;          round-robin fashion as described in Section 3.3.2. After
if not, the alternating block still has its position when    VolcanoML evaluates those new algorithms with sufdealing with the “efficiency vs. effectiveness” trade-off    ficient budget, the conditional block follows the bandit
when the search space is large. The conditioning block       algorithm and eliminates bad candidates with low upis capable of pruning the search space as optimization       per bounds from the candidate set. This process still folproceeds, when bad arms are pulled less often or will        lows Algorithm 1, only with a difference that the child

Efficient End-to-End AutoML via Scalable Search Space Decomposition (Extended Paper)                                                                                        9

           min 𝑓(𝑥, 𝑦, 𝑧, 𝑤; 𝐷)         Plan 2                               Search Space: (Algorithm, Feature, HP)                           𝐴! : Liblinear SVC
         (",$,%,&)                                                                                                                            𝐴" : Adaboost
                                      Cond on x
                                                                                                                   Plan                       …
                                      (x, y, z, w)                                                                                            𝐴# : Random Forest
                                                                                                          Cond. on Alg={𝐴! ,…, 𝐴" }
           Plan 1
                                                                                                           (Alg, Feature, HP)
            Joint          Joint         Joint        Joint
         (x, y, z, w)     (y, z, w)    (y, z, w)     (y, z, w)
                                                                               Alter. (𝐴! )                     Alter. (𝐴# )                    Alter. (𝐴" )
                                                                                                                                      ...
Fig. 3 Two different execution plans for the same optimiza-                  (Feature, HP)                    (Feature, HP)                   (Feature, HP)
tion problem. Each plan corresponds to a different way to
decompose the same search space (x, y, z, w).                       Joint (HP fixed)   Joint (FE fixed)                               Joint (HP fixed)   Joint (FE fixed)
                                                                                                                     ...
                                                                    (𝐴! .Feature)         (𝐴! .HP)                                    (𝐴" .Feature)            (𝐴" .HP)

blocks are extended with new algorithms. Therefore,              Fig. 4 VolcanoML’s execution plan for the same search
                                                                 space as explored by auto-sklearn. Here ‘Alg’ and ‘HP’ corit’s quite natural and easy to support continue tuning           respond to Algorithm and hyper-parameters respectively.
in VolcanoML.

                                                                 VolcanoML Plan for auto-sklearn. Figure                  4
4 VolcanoML Execution Plan                                       presents a VolcanoML execution plan for the same
                                                                 search space explored by auto-sklearn, which consists
Given a pre-defined search space, the input of Vol-              of the joint search of algorithms, features transforcanoML is (1) a dataset D, (2) a utility metric (e.g.,           mations operators, and hyper-parameters. Instead of
cross-validation accuracy) which defines the objective           conducting the search process in a single joint block,
function f , and (3) a time budget. VolcanoML then               as was done by auto-sklearn, VolcanoML first dedecomposes a large search space into an execution plan,          composes the search space via a conditioning block on
following some specific decomposition strategy.                  algorithms — this introduces a MAB problem in which
                                                                 each arm corresponds to one particular algorithm. It
4.1 Execution Plan                                               then decomposes each of the conditioned subspaces via
                                                                 an alternating block between feature engineering and
VolcanoML Execution Plan. Due to the space limi-                 hyper-parameter tuning. The whole subspace of feature
tation, we omit the formal definition of a VolcanoML             engineering (resp. that of hyper-parameter tuning) is
execution plan. A VolcanoML execution plan is a                  optimized by a joint block. Note that this execution
tree of building blocks. The root node corresponds to            plan is similar to the regular plan of human experts,
a building block solving the problem f with the en-              in which experts usually try different algorithms and
tire search space, which can be further decomposed               optimize the feature engineering operations and hyperinto multiple sub-problems. For each generated sub-              parameters alternatively for specific well-performing
problem, a building block (from the three candidates)            algorithms.
is applied to solve the corresponding problem. In addi-              Concretely, Figure 4 shows a search space for Aution, all the leaf nodes must be the joint blocks. Since         toML with K choices of ML algorithms. During each
joint block does not decompose the search space, it can          iteration, starting from the root node, VolcanoML
not be in any paths from the root node to leaf node. As          selects the child node to optimize until it reaches a leaf
an example, Figure 3 illustrates two possible execution          node and then optimizes over the subspace in the leaf
plans for f (x, y, z, w; D). Plan 1 contains only a sin-         node. As shown by the red lines in Figure 4, in this itgle root building block as a joint block, whereas Plan 2         eration, VolcanoML only tunes the feature engineerfirst introduces a conditioning block on x, and then cre-        ing pipeline of algorithm A1 while fixing its algorithm
ates one lower level of building blocks for each possible        hyper-parameters.
value of x (in Figure 3, we assume that |Dx | = 3).
                                                                 Alternative Execution Plans. Note that the execution
VolcanoML Execution Model. To execute a Vol-                     plan in Figure 4 is not the only possible one. Our flexcanoML execution plan, we follow a Volcano-style exe-            ible and scalable framework in VolcanoML allows us
cution that is similar to a relational database [28] — the       to explore different execution plans before reaching the
system invokes the do next! of the root node, which              proposed one, and in the next section 4.2 we introduce
then invokes the do next! of one of its child nodes,             the way of automatic plan generation. The reason why
propagating until the leaf node. At any time, one can            we choose this plan is due to the fundamental propinvoke the get current best of the root node, which              erty of the AutoML search space — we observe that,
returns the current best solution for the entire search          the optimal choices of features are different across algospace.                                                           rithms, which implies that we can first decompose the

10                                                                                   Yang Li, Yu Shen, Wentao Zhang, Ce Zhang, Bin Cui

     Search Space: (Embedding, Algorithm, Feature, HP)                          – Plan 1 - J(Joint). Optimize over the entire space
                                                                                  using a joint block.
                                        Plan
                                                                                – Plan 2 - C(Conditioning). Use a conditioning block
                                   Cond. on Alg
                                                                                  on the choice of machine learning algorithms, and
                                 (Alg, Feature, HP)
                                                                                  then optimize each subspace using joint blocks.
                Alter.                 Alter.                 Alter.
                                                                                – Plan 3 - A(Alternating). Use an alternating block
           (Feature, HP)           (Feature, HP)          (Feature, HP)
                                                                                  to separate the entire space into feature engineering
                                                                                  space and combined algorithm selection and hyper-
                                                                                  parameter tuning (CASH) space.
        Joint            Joint                        Joint            Joint
                                        ...                                     – Plan 4 - AC(Alternating then Conditioning). Use an
     (Embedding          (HP)                   (Embedding             (HP)
      , Feature)                                 , Feature)                       alternating block to separate the entire space into
                                                                                  feature engineering and CASH space, and then use
Fig. 5 VolcanoML’s execution plan for a larger search                             a conditioning block on the choice of algorithm.
space enriched by an additional embedding selection stage.
                                                                                – Plan 5 - CA(Conditioning then Alternating). Use a
                                                                                  conditioning block on the choice of machine learnsearch space along ML algorithms. The improvements                                ing algorithms, and then optimize the subspace of
introduced by feature engineering and hyper-parameter                             feature engineering and algorithm hyperparameters
tuning are largely complementary (See A.1.2 for more                              alternately. See Plan 5 in Figure 6 for more details.
details), and thus we can optimize them alternately. For                        – TPOT - TPOT. In essence, the execution plan of TPOT
feature engineering (resp. hyper-parameter tuning), the                           also uses a single joint block. The difference between
subspace is small enough to be handled by a single joint                          TPOT and Plan 1 is that TPOT uses the evolutionary
block efficiently. In Section 4.2, we will list the possible                      algorithm while Plan 1 uses the Bayesian optimizaplans of the coarse-grained level and further discuss the                         tion.
opportunity of automatic plan generation.                                       – AUSK - autosklearn. The execution plan of
                                                                                  autosklearn also uses a single joint block. The dif-
                                                                                  ference between autosklearn and Plan 1 is their
VolcanoML Plan for Enriched Search Space. We
                                                                                  ensemble strategy. Concretely, autosklearn build
can easily extend VolcanoML and enable functional-
                                                                                  the ensemble model over all the evaluated models
ities that are not supported by most AutoML systems.
                                                                                  while Plan 1 builds it over a fixed number of well-
For example, Figure 5 illustrates an execution plan for
                                                                                  performed models as VolcanoML does.
a search space with an additional stage — embedding
selection. Given an input, e.g., image or text, we first                           Indeed, automatic plan generation can find the optichoose embeddings based on a collection of TensorFlow                          mal solutions with techniques like reinforcement learn-
Hub pre-trained models and then conduct algorithm                              ing. However, one critical problem behind the autoselection, feature engineering, and hyper-parameter                            matic plan generation is the overhead introduced by
tuning. We use an execution plan as illustrated in                             constructing and searching for a new execution plan.
Figure 5, having the embedding selection step jointly                          Here, generating execution plans may take a massive
optimized together with the feature engineering.                               amount of training cost, and automatic plan genera-
                                                                               tion may involve building and evaluating an extremely
                                                                               large volume of plans. Moreover, if the user only has
4.2 Automatic Plan Generation                                                  a limited budget, automated plan generation can eas-
                                                                               ily run out of the budget while not providing a decent
In principle, the design of VolcanoML opens up the                             execution plan.
opportunity for automatic plan generation — given a                                It is still an open question of whether we can support
collection of benchmark datasets, one could automati-                          finer-grained partition of the search space (e.g., differcally search for the best decomposition strategy of the                        ent plans for different subspace of features), and moresearch space and come up with a physical plan auto-                            over, whether we can conduct efficient automatic plan
matically. While the complete treatment of this prob-                          optimization without enumerating all possible plans.
lem is beyond the scope of this paper, we illustrate the                       These are exciting future directions, and we expect the
possibility with a straightforward strategy. We auto-                          endeavor to be non-trivial. We hope that this paper sets
matically enumerate all possible execution plans in a                          the ground for this line of research in the future (e.g.,
coarse-grained level and find that our manually speci-                         rule-based heuristics or reinforcement learning).
fied execution plan in Figure 4 outperforms the alter-                         Further Discussion. We abstract a VolcanoML exnatives. The five execution plans are as follows:                              ecution plan as a tree of building blocks. The root

Efficient End-to-End AutoML via Scalable Search Space Decomposition (Extended Paper)                                                                                  11

                                                                              Cond. on Alg                                                   Alter.
                                                                          (Feature, Alg, HP)                                         (Feature, Alg, HP)

                                                                         𝐴!                   𝐴"

                                     Joint                            Joint                     Joint                             Joint                    Joint
                           (Feature, Alg, HP)                     (Feature, HP)          (Feature, HP)                       (Feature)                   (Alg, HP)
                                    Plan 1: J                                     Plan 2: C                                                  Plan 3: A

                                     Alter.                                                                           Cond. on Alg
                               (Feature, Alg, HP)                                                                 (Feature, Alg, HP)
                                                                                                            𝐴!                              𝐴"

                       Joint                  Cond. on Alg                                         Alter.                Alter.                   Alter.
                     (Feature)                  (Alg, HP)                                     (Feature, HP)           (Feature, HP)           (Feature, HP)

                                        𝐴!                   𝐴"

                                    Joint                    Joint                   Joint                    Joint                       Joint               Joint
                                     (HP)                    (HP)                  (Feature)                  (HP)                   (Feature)                 (HP)
                                       Plan 4: AC                                                                       Plan 5: CA

Fig. 6 Five execution plans in the task granularity.

node corresponds to a building block solving the prob-                                         found feature engineering operators, it optimizes the
lem with the entire search space, which can be further                                         algorithm hyperparameters and obtains the final condecomposed into multiple building blocks if necessary.                                         figuration. The main advantage of progressive methods
Three kinds of building blocks can be used to build the                                        is that, they enjoy high efficiency in exploring the space
tree-structured execution plan. Reinforcement Learn-                                           because they only need to optimize the blocks following
ing (RL) could be a straightforward solution to gen-                                           a path from the root to the leaves. However, they also
erate execution plans automatically. The key decisions                                         have two weak points: (1) While the best algorithm is
involve how to define the states, rewards, and actions.                                        chosen by keeping other hyperparameters by default,
We can define the current state by encoding the cur-                                           there is a risk that the algorithm found progressively
rent structure of the tree and the optimization prob-                                          may not be the optimal one; (2) Only one algorithm is
lem to decompose. When all leaf nodes in the tree are                                          explored in the optimization process, and it leads to a
joint blocks, we can execute the current decomposition                                         lack of diversity in the model pool for the final ensemplan. And we can take the validation accuracy as the                                           ble. The original optimization strategy deals with the
reward. Each action corresponds to apply a decompo-                                            weak points by applying the bandit-based algorithm. It
sition strategy to an optimization problem by adding                                           evaluates each algorithm by trying different combinaa building block to the tree’s some leaf node. The RL                                          tions of other hyperparameters so that it can further
agent builds and evaluates each execution plan itera-                                          compute the expected utility of each algorithm. Meantively by trying different actions. The goal of the agent                                      while, since all algorithms are evaluated for some given
is to find the plan that achieves the optimal evaluation                                       budget, the evaluation history is diverse, which helps
result.                                                                                        generate a better model ensemble.

4.3 Progressive Optimization Methods

Unlike the optimization strategy used in Figure 4, the
progressive methods [62] can optimize the search space
in a top-down manner. Take the default tree-structured
space (Plan 5) shown in Figure 6 as an example, a pro-                                         5 Further Optimization with Meta-learning
gressive method first tries different algorithms in the
conditional block while keeping all other hyperparam-                                          One class of optimizations that we support is metaeters by default. After evaluating all algorithm candi-                                        learning [84, 87] — given previous runs of the system
dates, it fixes the best algorithm and enters the search                                       over similar workloads, to transfer the knowledge and
space under this algorithm. Then, it optimizes the space                                       better help the workload at hand. Depending on the
of feature engineering while keeping the algorithm hy-                                         type of different building blocks, we support different
perparameters by default. Finally, by fixing the best                                          meta-learning strategies.

12                                                                     Yang Li, Yu Shen, Wentao Zhang, Ce Zhang, Bin Cui

5.1 Meta-learning for Conditioning Blocks                        previous datasets on the same search space, and the ob-
                                                                 servations in the current task is HT . We use a scalable
For conditioning block on variable x, it introduces a            meta-learning method, RGPE [23], to accelerate BO.
multi-armed bandit problem with |Dx | arms, and its              First, for each previous dataset Di , we train a Gausobjective is to identify the optimal arm. One natural            sian process model Mi on the corresponding observameta-learning strategy is to learn, given the dataset D,         tions from Hi . Then we build a surrogate model Mmeta
a much smaller subset of arms A ⊆ Dx that includes               to guide the search in this joint block, instead of the
the optimal arm. This could explicitly reduce the search         original surrogate MT fitted on HT only. The predicspace in the conditioning block from Dx to A. We use             tion of Mmeta at point x is given by
a meta-learning strategy based on RankNet [10].                          X              X
   During the training process, we are given a train-            y ∼ N(      wi µi (x),   wi σi2 (x)),               (12)
ing history over multiple previous datasets D1 , ..., Dn .                i                i

We are given the relative relationships between different        where wi is the weight of base surrogate Mi , and µi and
arms on different datasets                                       σi2 are the predictive mean and variance from base sur-
                                                                 rogate Mi . The weight wi reflects the similarity between
T = {(Aj , Ak , Di ) : Aj , Ak ∈ Dx },                   (10)
                                                                 the previous task and current task. Therefore, Mmeta
where (Aj , Ak , Di ) ∈ T means that Aj performs bet-            carries the knowledge of search on previous tasks, which
ter than Ak on dataset Di . We are also given a meta-            can greatly accelerate the convergence of the search in
feature extractor hD for dataset and a meta-feature ex-          current joint block. We then use the following ranking
tractor hA for arms. Both types of extractors will map a         loss function L, i.e., the number of misranked pairs, to
dataset (resp. an arm) to an m-dimensional real-valued           measure the similarity between previous tasks and curvector. The model that we are trying to learn is a multi-        rent task:
layer perceptron (MLP) model taking as input a dataset                            T
                                                                                 n X
                                                                                   n  T

embedding and an arm embedding, with the following                                         1((Mi (xj ) <i (xk ) ⊕ (yj < yk )),
                                                                                 X
                                                                 L(Mi , HT ) =
learning objective:                                                              j=1 k=1
                                                                                                                       (13)
                              (i)  (i)         (i)  (i)
           X
min                     l+ σ(rj − rk ) + l− σ(rk − rj )
 Θ
      (Di ,Aj ,Ak )∈T                                            where ⊕ is the exclusive-or operator, nT = |HT |, xj and
                             (i)                                 yj are the sample point and its performance in H T , and
                  where     rj = M LP (hD (Di ), hA (Aj ); Θ),
                                                                 Mi (xj ) means the prediction of Mi on point xj . Based
                             (i)
                            rk = M LP (hD (Di ), hA (Ak ); Θ),   on the ranking loss function, the weight wi is set to
                                                         (11)    the probability that Mi has the smallest ranking loss
                                                                 on HT , that is, wi = P(i = argminj L(Mj , HT )). This
where σ is the sigmoid function, l+ is the hinge loss with       probability can be estimated using MCMC sampling.
positive label, and l− is the hinge loss with negative
label.                                                           Example. When applied to end-to-end AutoML, the
    During inference, the MLP with parameter Θ takes             joint block is used to select configurations from a joint
the vector that consists of hD and hA as input, and              search space, e.g., the search of hyper-parameter conoutputs a score. The best subset of arms can then be             figurations or features given a specific ML algorithm.
selected based on these scores.                                  Although the optimal configuration may be different
                                                                 across tasks, the performance surface of configurations
                                                                 in current task may be similar to some in previous tasks
5.2 Meta-learning for Joint Blocks                               due to the relevancy between tasks. In this case, BO his-
                                                                 tory on previous datasets can be utilized to guide the
A joint block uses BO method that can be slow                    configuration search via the above meta-learning based
when the underlying search space is large. An                    BO method.
intuitive optimization is to leverage BO history
                     1
H1 = {(x1j , yj1 )}nj=1 , ..., Hn from n previous datasets
D1 , ..., Dn . This motivates the meta-learning based BO         6 Experimental Evaluation
that can speed up the convergence of search in the current joint block.                                                We compare VolcanoML with state-of-the-art Au-
    When executing joint block on a new dataset, we              toML systems. In our evaluation, we focus on three perare given the historical observations H1 , ..., Hn from n        spectives: (1) the performance of VolcanoML given

Efficient End-to-End AutoML via Scalable Search Space Decomposition (Extended Paper)                                   13

the same search space explored by existing systems,              In our evaluation, we repeat each experiment 10
(2) the scalability of VolcanoML given larger search         times and report the average utility metric. In each exspaces, and (3) the extensibity of VolcanoML to inte-        periment, we use four fifths of the data samples in each
grate new components into the search space of AutoML         dataset to search for the best ML pipeline and report
pipelines.                                                   the utility metric on the remaining fifth.
                                                             Methodology for Comparing AutoML Systems.
                                                             To compare the overall test result of each AutoML sys-
6.1 Experimental Setup                                       tem on a wide range of datasets, we use the average
                                                             rank as the metric following [3]. For each dataset, we
AutoML Systems.              We evaluate VolcanoML           rank all participant systems based on the result of the
as well as two open-source AutoML systems:                   best ML pipeline they have found so far; we then take
auto-sklearn [22] and TPOT [69]. In addition, we also        the average of their ranks across different datasets. In
compare VolcanoML with four commercial AutoML                addition, we use statistical testing to determine ties and
platforms from Google, Amazon AWS, Microsoft Azure,          adjust the rankings [16].
and Oracle. Both VolcanoML and auto-sklearn
                                                             Training Data for Meta-learning. The results for
support meta-learning, while TPOT does not. For fair
                                                             meta-learning are obtained from running Bayesian opticomparison with TPOT, we also use VolcanoML− and
                                                             mization on 90 classification datasets and 50 regression
AUSK− to denote the versions of VolcanoML and
                                                             datasets collected from OpenML. For classification, we
auto-sklearn when meta-learning is disabled. Our im-
                                                             collect the results by optimizing the balanced accuracy,
plementation of VolcanoML is available at https:
                                                             accuracy, f1 score and AUC. For regression, we collect
//github.com/PKU-DAIR/mindware.
                                                             the results by optimizing the mean squared error, mean
Datasets. To compare VolcanoML with academic                 absolute error and r2 value. When VolcanoML rebaselines, we use 60 real-world ML datasets from the         ceives a new task and the optimization target is one of
OpenML repository [85], including 40 for classification      the above metrics, VolcanoML will use all the evalua-
(CLS) tasks and 20 for regression (REG) tasks. 10 of the     tion results with this metric to train the RankNet in the
40 classification datasets are relatively large, each with   conditional block and RGPE in the joint block. In our
20k to 110k data samples; the other 30 are of medium         experiments, to ensure the current task does not occur
size, each with 1k to 12k samples. In addition, we also      in the results for meta-learning, we apply the leave-oneuse datasets from six Kaggle competitions (See Table 3       out strategy. For example, when we optimize Dataset A,
for details) to compare VolcanoML with four com-             we will use all other results except A for meta-learning.
mercial platforms.
                                                             More Details. We include the details of search space
AutoML Tasks. We define three kinds of real-world            and programming API in Appendix A.2, experiment
AutoML tasks, including (1) a general classification         datasets in Appendix A.3.
task on 30 medium datasets, (2) a general regression
task on 20 medium datasets, and (3) a large-scale classification task on 10 large datasets.                        6.2 End-to-End Comparison
     To test the scalability of the participating systems,
we design three search spaces that include 20, 29, and       We first evaluate the participant AutoML systems
100 hyper-parameters, where the smaller search space         given the search space explored by auto-sklearn. Figis a subset of the larger one. We run VolcanoML and          ure 7 presents the results of VolcanoML compared
the baseline AutoML systems against each of the three        to auto-sklearn (AUSK) and TPOT on the 30 classearch spaces. The time budget is 900 seconds for the        sification tasks and the 20 regression tasks, respecsmallest search space and 1,800 seconds for the other        tively. For classification tasks, we plot the classificatwo, when performing the general classification task (1);    tion accuracy improvement (%); for regression tasks,
the time budget is increased to 5,400 and 86,400 sec-        we plot the relative MSE improvement ∆, which is deonds respectively, when performing the general regres-                                   s(m2 )−s(m1 )
                                                             fined as ∆(m1 , m2 ) = max(s(m    2 ),s(m1 ))
                                                                                                           , where s(·) is
sion task (2) and the large-scale classification task (3).   MSE on the test set. Overall, VolcanoML outper-
Utility Metrics. Following [22], we adopt the metric         forms auto-sklearn and TPOT on 25 and 23 of the 30
balanced accuracy for all classification tasks — com-        classification tasks, and on 17 and 15 of the 20 regrespared with standard (classification) accuracy, it assigns    sion tasks, respectively.
equal weights to classes and takes the average of class-          We also conduct experiments to evaluate Volwise accuracy. For regression tasks, we use the mean         canoML with different time budgets. Figure 8 presents
squared error (MSE) as the metric.                           the results on four large classification datasets. We

14                                                                                                                                                                   Yang Li, Yu Shen, Wentao Zhang, Ce Zhang, Bin Cui

                                              Accuracy Improvement (%)                                                                                 Relative MSE Improvement
                                                                                                                                                                                  1.0
                                                                         6

                                                                         4                                                                                                        0.5

                                                                         2

                                                                                                                                                                                  0.0
                                                                         0
                                                                             1489
                                                                              737
                                                                             1056
                                                                              816
                                                                              734
                                                                               38
                                                                              803
                                                                               32                                                                                                         541
                                                                                                                                                                                        41021
                                                                                                                                                                                          529
                                                                                                                                                                                          315
                                                                                                                                                                                          225
                                                                              847
                                                                              819
                                                                              728
                                                                              183
                                                                              735
                                                                              761
                                                                             1053
                                                                               44
                                                                              772                                                                                                         558
                                                                                                                                                                                          503
                                                                                                                                                                                           A1
                                                                                                                                                                                        42369
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
                                                                                                                                                                                        23516
                                                                              182
                                                                             1471
                                                                              807                                                                                                       41539
                                                                                                                                                                                          227
                                                                                                                                                                                          573
                                                                                               Dataset ID
                                                                                                                                                                                        23515
                                                                                                                                                                                          189
                                                                                                                                                                                          308
                                                                                                                                                                                                    Dataset ID
                                                                             (a) VolcanoML vs. AUSK on CLS                                                              (b) VolcanoML vs. AUSK on REG

                                              Accuracy Improvement (%)                                                                                 Relative MSE Improvement
                                                                                                                                                                                  1.0
                                                                         4

                                                                                                                                                                                  0.5
                                                                         2

                                                                         0                                                                                                        0.0

                                                                             1489
                                                                              819
                                                                              752
                                                                              183
                                                                              737
                                                                              803
                                                                             1067
                                                                              728                                                                                                       23516
                                                                                                                                                                                          223
                                                                                                                                                                                          541
                                                                                                                                                                                          507
                                                                                                                                                                                          225
                                                                              761
                                                                               44
                                                                               28
                                                                               32
                                                                              816
                                                                              182
                                                                              847
                                                                              735
                                                                               36                                                                                                         572
                                                                                                                                                                                          558
                                                                                                                                                                                          529
                                                                                                                                                                                        41021
                                                                              979
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
                                                                              833
                                                                             1053
                                                                             1021                                                                                                       42369
                                                                                                                                                                                        41539
                                                                                                                                                                                          668
                                                                                               Dataset ID
                                                                                                                                                                                          189
                                                                                                                                                                                        23515
                                                                                                                                                                                          308
                                                                                                                                                                                                    Dataset ID
                                                                             (c) VolcanoML vs. TPOT on CLS                                                             (d) VolcanoML vs. TPOT on REG

Fig. 7 End-to-End results on 30 OpenML classification (CLS) datasets and 20 OpenML regression (REG) datasets.

                                          5                                                                                                       30

                 Average Test Error (%)                                                                                  Average Test Error (%)
                                                                                                     TPOT                                                                                                        TPOT
                                          4
                                                                                                     AUSK                                         25                                                             AUSK
                                                                                                     VolcanoML                                                                                                   VolcanoML
                                                                                                                                                  20
                                          3

                                                                                                                                                  15
                                          2

                                                             14400             28800   43200     57600   72000   86400                                                              14400   28800    43200   57600   72000   86400
                                                                                 Wall Clock Time (s)                                                                                          Wall Clock Time (s)
                                                                                 (a) Mnist 784                                                                                                  (b) Kropt

                                   12                                                                                                             30

          Average Test Error (%)                                                                                         Average Test Error (%)
                                                                                                     TPOT                                                                                                        TPOT
                                   10                                                                AUSK                                         29
                                                                                                                                                                                                                 AUSK
                                                                                                     VolcanoML                                                                                                   VolcanoML
                                          8
                                                                                                                                                  28

                                          6
                                                                                                                                                  27
                                                             14400             28800   43200     57600   72000   86400                                                              14400   28800    43200   57600   72000   86400
                                                                                 Wall Clock Time (s)                                                                                          Wall Clock Time (s)
                                                                                (c) Electricity                                                                                                 (d) Higgs

Fig. 8 Average test errors on four large datasets with different time budgets.

Efficient End-to-End AutoML via Scalable Search Space Decomposition (Extended Paper)                                15

Table 1 Average ranks on 30 classification (CLS) datasets and 20 regression (REG) datasets with three different search
spaces. (The lower is the better)

                  Search Space - Task   TPOT     AUSK−      AUSK     VolcanoML−         VolcanoML
                  Small - CLS            3.09     3.07       3.01        2.94              2.89
                  Medium - CLS            3.2     3.32       3.27        2.78              2.43
                  Large - CLS            3.29     3.77       3.57        2.72              1.65
                  Small - REG            2.98     3.02        3.0        3.02              2.98
                  Medium - REG           2.95      3.3       3.12        2.75              2.88
                  Large - REG             3.1     3.85       3.82        2.15              2.08

observe that VolcanoML exhibits consistent perfor-           allows VolcanoML to deal with images, which is difmance over different time budgets. Notably, on Higgs,        ficult for auto-sklearn and TPOT to support using ex-
VolcanoML achieves 27.2% test error within 4 hours,          isting code. We implement two pre-trained models to
which is better than the performance of the other two        generate embeddings for images, and we evaluate Volsystems given 24 hours. Furthermore, we also design          canoML with the enriched search space on the Kaggle
additional experiments to evaluate the consistency of        dataset dogs-vs-cats. We observe that VolcanoML
system performance given different (larger) time bud-        achieves 96.5% test accuracy, which is significantly betgets and search spaces, and more details can be found        ter than 70.4% obtained by VolcanoML without conin the following sections.                                   sidering embeddings.
    We further study the scalability of the participant systems on the three aforementioned search
spaces. Without meta-learning, VolcanoML achieves            Table 2 Test accuracy (%) of VolcanoML with and with-
                                                             out the enrichment of “smote balancer” operator.
the best average rank for both the classification and
regression tasks — on the small search space (with            Dataset          AUSK      VolcanoML−      VolcanoML
20 hyper-parameters), VolcanoML performs slightly             sick              97.29        97.31          97.34
better than auto-sklearn and TPOT, and it performs            pc2               86.70        86.91          90.27
significantly better on the medium (with 29 hyper-            abalone           66.86        65.97          67.32
                                                              page-blocks(2)    94.70        95.29          96.69
parameters) and large (with 100 hyper-parameters)             hypothyroid(2)    99.62        99.64          99.64
search spaces. For meta-learning, we present more empirical results and discussions in Section 6.6.

6.3 Search Space Enrichment
                                                             6.4 Comparison with 4 Industrial Platforms
We now evaluate the extensibility of VolcanoML via
two experiments with enriched search spaces.                 We run additional experiments on six Kaggle competi-
    Adding Data Balancing Operator. In the first ex-         tions (See Table 3 for dataset statistics) over four comperiment, we implement the feature engineering oper-         mercial baselines (AutoML services from Google, AWS,
ator “smote balancer” – a popular over-sampling te-          Azure and Oracle) as follows:
chinique proposed for the overfitting problem, and incorporate it into the aforementioned balancing stage          – Google Cloud AutoML on unknown running enviof feature engineering (FE) (Section 3.1). Note that            ronment (not transparent to users).
auto-sklearn cannot support this fine-grained enrich-         – AWS Sagemaker AutoPilot on an instance
ment of the search space. Table 2 presents the results          ‘ml.m5.4xlarge’ with 16 Intel Xeon® Platinum
of auto-sklearn, VolcanoML without enrichment,                  8175M processors and 64G memory.
and VolcanoML with enrichment, on five imbalanced             – Azure Automated ML on two instances ‘STANdatasets. We observe that enriching the search space            DARD D12’ with totally 8 unknown processors and
brings further improvement, e.g., VolcanoML with                56G memory.
enrichment outperforms auto-sklearn by 3.57% (bal-            – Oracle     Data     Science    on     an instance
anced accuracy) on the dataset pc2.                             ‘VM.Standard2.2’ with 2 2.0 GHz Intel® Xeon®
    Supporting Embedding Selection. In the second ex-           Platinum 8167M processors and 30G memory.
periment, we add a new stage “embedding selection”            – VolcanoML          on    an    Ali-cloud instance
into the FE pipeline, with two candidate embedding-             ‘ecs.hfc6.2xlarge’ with 8 3.10 GHz Intel® Xeon®
extraction operators (i.e., two pre-trained models). This       Platinum 8269CY processors and 30G memory.

16                                                                                                                                                Yang Li, Yu Shen, Wentao Zhang, Ce Zhang, Bin Cui

                                                                                                                                            6
                                     22.5                VolcanoML             Platform 1           Platform 4                                                     VolcanoML                Platform 1        Platform 4

            Average Test Error (%)                                                                               Average Test Error (%)
                                                         VolcanoML-            Platform 2           Platform 3                                                     VolcanoML-               Platform 2        Platform 3
                                     20.0                                                                                                   5

                                     17.5
                                                                                                                                            4
                                     15.0

                                     12.5                                                                                                   3

                                     10.0
                                                                                                                                            2
                                                    900               1800             2700              3600                                              900               1800                  2700             3600
                                                             Wall Clock Time (s)                                                                                     Wall Clock Time (s)
                                                    (a) Influence Network                                                                                        (b) Virus Prediction

                               7                                                                                                            5
                                                         VolcanoML             Platform 1           Platform 4                                                     VolcanoML                Platform 1        Platform 4

     Average Test Error (%)                                                                                      Average Test Error (%)
                               6                         VolcanoML-            Platform 2           Platform 3                                                     VolcanoML-               Platform 2        Platform 3

                                                                                                                                            4
                               5

                               4                                                                                                            3

                               3
                                                                                                                                            2
                               2

                                            900      1800        2700        3600           4500     5400                                           3600            5400            7200             9000          10800
                                                            Wall Clock Time (s)                                                                                      Wall Clock Time (s)
                                                     (c) Employee Access                                                                                   (d) Customer Satisfaction

                                                                                                                                            0.5
                                                         VolcanoML             Platform 1           Platform 4                                                     VolcanoML                Platform 1        Platform 4

      Average Test Error (%)                                                                                       Average Test Error (%)
                               42
                                                         VolcanoML-            Platform 2           Platform 3                                                     VolcanoML-               Platform 2        Platform 3

                               40

                               38
                                                                                                                                            0.0
                               36

                               34
                                                  9000            18000              27000               36000                                       900    1800      2700    3600         4500     5400    6300    7200
                                                            Wall Clock Time (s)                                                                                       Wall Clock Time (s)
                                                         (e) Business Value                                                                                           (f) Flavours

Fig. 9 Test error on Kaggle competitions compared with commericial baselines.

Table 3 Kaggle dataset information.                                                                               pared with those cloud solutions on the six tasks. Due to
 Datasets                                                               Classes     Samples        Features
                                                                                                                  the large initial search space, VolcanoML- performs
 Influencers in Social Networks                                            2          5500            22          slightly worse than VolcanoML (with meta-learning)
 West-Nile Virus Prediction                                                2         10506            11          in the beginning on Influence Network, Virus Predic-
 Employee Access Challenge                                                 2         32769             9
 Santander Customer Satisfaction                                           2         76020           369          tion, and Business Value. Given more time budget (i.e.,
 Predicting Red Hat Business Value                                         2        2197291           12          fix the x-axis to some time budget), VolcanoML and
 Flavors of Physics                                                        2         38012            49
                                                                                                                  VolcanoML- show similar results and often outper-
                                                                                                                  form the considered commercial platforms. This demon-
                                                                                                                  strates VolcanoML’s effectiveness against the com-
    Due to the different design principles (different                                                             mercial AutoML baselines.
hardware and parallelism) of commercial manufacturers, it is very hard to set up exactly the same environment settings. We set the the maximal time budget as                                                              6.5 Scalability on Different Search Space
10 hours and use cost as an additional metric. Here,
we anonymously refer to these platforms as Platform                                                               To evaluate the scalability of each system, we design
1-4. Figure 9 show the results of VolcanoML and                                                                   three search spaces of different sizes. The small search
the platforms. We observe that VolcanoML- (with-                                                                  space only contains four feature selectors (select perout meta-learning) achieves satisfactory results com-                                                             centile, select generic univariate, extra trees preprocess-

Efficient End-to-End AutoML via Scalable Search Space Decomposition (Extended Paper)                                                                          17

                                                                            Average Validation Error (%)
Table 4 Average ranks on 30 classification (CLS) datasets                                                  50
                                                                                                                                            VolcanoML−
and 20 regression (REG) datasets with three different search                                               49
                                                                                                                                            VolcanoML
spaces (The lower is the better). The budget is 1800 seconds                                               48
for classification and 5400 seconds for regression.                                                        47

                                                                                                           46
  Search Space - Task    TPOT      AUSK     VolcanoML
  Small - CLS             2.03      1.98       1.98                                                        45

  Medium - CLS            1.95      2.21       1.83                                                        44
  Large - CLS             1.97      2.43       1.60                                                             0   10        20       30        40      50
  Small - REG             2.00      2.00       2.00                                                                      Number of Evaluations
  Medium - REG            2.05      2.30       1.65                                                                        (a) Quake
  Large - REG             2.10      2.20       1.70

                                                                      Average Validation Error (%)
Table 5 Average ranks on 30 classification (CLS) datasets                                                                                   VolcanoML−
                                                                                                      50
and 20 regression (REG) datasets with three different search                                                                                VolcanoML
spaces (The lower is the better). The budget is 3600 seconds
                                                                                                      48
for classification and 10800 seconds for regression.
                                                                                                      46
  Search Space - Task    TPOT      AUSK     VolcanoML
  Small - CLS             2.03      1.97        2.0
                                                                                                      44
  Medium - CLS            1.90      2.27       1.83
  Large - CLS             2.03      2.53       1.43                                                         0       10        20       30        40      50

  Small - REG             1.95      2.00       2.05                                                                      Number of Evaluations
  Medium - REG            1.95      2.30       1.75                                                                       (b) Space ga
  Large - REG             1.98      2.28       1.75
                                                               Fig. 10 The result of the first 50 evaluations on ‘quake’
Table 6 Average ranks on 30 classification (CLS) datasets      and ‘space ga’ using LibSVM. VolcanoML− refers to Voland 20 regression (REG) datasets with three different search   canoML with meta-learning based BO disabled.
spaces (The lower is the better). The budget is 7200 seconds
for classification and 21600 seconds for regression.
                                                               canoML still achieves the best average rank, and per-
  Search Space - Task    TPOT      AUSK     VolcanoML
  Small - CLS             2.00      2.00       2.00
                                                               forms better compared with auto-sklearn and TPOT.
  Medium - CLS            1.97      2.23       1.80
  Large - CLS             1.92      2.40       1.68
  Small - REG             2.00      2.00       2.00
  Medium - REG            1.95      2.23       1.83
                                                               6.6 Results about Meta-Learning based Optimization
  Large - REG             2.00      2.10       1.90
                                                               Meta-learning in Joint Block. Figure 10 shows
ing, and liblinear SVM preprocessing) and uses random          the improvement of meta-learning in a joint block.
forest as the ML algorithm. The medium search space            Compared with VolcanoML− , the validation error
contains the same four feature selectors as the small one      drops significantly in the first 10 evaluations on Voland uses linear svc(r), random forest, and AdaBoost as         canoML, which indicates that meta-learning captures
the ML algorithms. The large search space is the entire        the information of the historical tasks and performs an
search space described in Section 3.1. The three spaces        effective warm-start. When achieving the same validainclude 20, 29, and 100 hyper-parameters, respectively,        tion error as vanilla VolcanoML− , VolcanoML reand the smaller space is a subset of the larger one.           duces the number of evaluations by eight-fold on quake
    To further investigate the result of VolcanoML             and two-fold on space ga.
over 1) different time budgets and 2) different search         Meta-learning in Conditioning Block. To comspaces, we conducted additional experiments to run             pare the performance of RankNet, we also used Lighteach system given 1800 / 5400 seconds, 3600 / 10800            GBM to model the relationship between algorithm perseconds and 7200 / 21600 seconds for classification / re-      formance and tasks by transforming the ranking probgression tasks over the small, medium and large search         lem into a binary classification problem. The input to
spaces respectively. These numbers are chosen by fol-          both LightGBM and RankNet is the same. We adopted
lowing the settings in papers of auto-sklearn and              10-fold validation mechanism to evaluate each method;
TPOT. The experiments include 50 AutoML tasks (30 for          the meta-learner is learned on the training set, and
classification and 20 for regression), and we use the met-     validated on the validation set. In addition, we mearic — average rank to measure each system. Tables 4, 5         sured the performance of each method using the metand 6 show the results over three different search spaces      ric ‘mAP@5 ’, which is the mean Average Precision
given different time budgets. We can observe that, with        to predict the top-5 algorithms. RankNet and Lightthe increase of time budget and search space, Vol-             GBM gets 0.87 and 0.62 mAP@5 score respectively.

18                                                                  Yang Li, Yu Shen, Wentao Zhang, Ce Zhang, Bin Cui

This demonstrates the more powerful expressiveness of         execution plan (Plan 5) in VolcanoML sets up a very
neural networks (RankNet) than traditional ML algo-           competitive AutoML baseline for our further research
rithms (LightGBM ).                                           on VolcanoML, e.g., automatic plan generation.
    Table 1 also summarizes the performance of metalearning in terms of the average ranks. With metalearning, the average rank of VolcanoML is dramati-           6.8 Additional Experiment Results
cally improved compared with auto-sklearn. Overall,
VolcanoML with meta-learning achieves the best re-            Comparison with early-stopping methods. Tasult over large search space.                                 ble 9 show the results for VolcanoML compared
                                                              with early-stopping based methods on five classifica-
                                                              tion datasets and five regression datasets in Table 9.
6.7 Evaluations on Different Execution Plans                  The datasets are of medium size, each of which con-
                                                              tains 8192 samples. The settings follow Section 6.1, and
To address the inefficiency of automated plan genera-
                                                              the compared baselines are Hyperband [51], BOHB [21],
tion, we apply the execution plan that performs well
                                                              and MFES-HB [57]. Remind that VolcanoML uses
on most tasks. Since there are lots of ways to decom-
                                                              SMAC [32] in the joint block by default. While the expose AutoML space, enumerating and evaluating each
                                                              ecution plan in VolcanoML is independent of optiexecution plan is impossible. To avoid enumerating the
                                                              mization algorithms, we also implement VolcanoML
entire search space, we list all the execution plans (a
                                                              +, which applies the MFES-HB algorithm in the joint
small plan set) in the coarse-grained level — sub-task,
                                                              block. From Table 9, we observe that VolcanoML
and this small plan set covers both the frameworks in
                                                              with SMAC outperforms the three early-stopping methexisting AutoML systems and the strategies used by
                                                              ods, and the performance is further improved when we
human experts. Concretely, we can obtain this small
                                                              combine the benefits of both VolcanoML execution
plan set by decomposing the search space according to
                                                              plans and early-stopping optimization methods.
the sub-tasks in AutoML: feature engineering, hyperparameter tuning and algorithm selection. There are in        Results on large datasets. Table 10 shows the retotal five execution plans (See Figure 6) — J, C, A, AC,      sults on ten large datasets with a budget of 18,000
and CA. If we decompose the search space in a more            seconds. VolcanoML is the best on eight of them.
fine-grained level, the plan set will be larger. Note that,   Figure 11 shows the validation errors on four of those
most of the existing open-source AutoML systems, e.g.,        datasets. When achieving the same validation error
auto-sklearn and TPOT correspond to the execution             compared with TPOT and auto-sklearn, VolcanoML
plan — plan 1 (J ). We evaluate each of them on 20            obtains a speed-up of 4.3-10.5× and 4.8-11×, respecclassification tasks and 10 regression tasks, and the re-     tively.
sults can be found in Tables 7 and 8. We can have that        Continue tuning in conditional block. To show
the proposed execution plan used in VolcanoML (i.e.,          the process of continue tuning, we present a case study
plan 5 - CA) outperforms the other alternatives on most       on the dataset pc4. We add three algorithms (Lighttasks (a smaller average rank). This demonstrates the         GBM, Extra Trees, and Liblinear SVC) after tuning
effectiveness of the proposed execution plan over the         7 other algorithms in VolcanoML for 1200 seconds.
potential competitors.                                        The total budget is 1800 seconds. The trend of the
    Furthermore, if we look at the performance                number of active blocks is plotted in Figure 12. When
of the existing open-source AutoML systems, i.e.,             new algorithms come, VolcanoML with restarting reauto-sklearn and TPOT, which fall into the execution          optimizes the extended search space, and it takes anplan 1 - J. As shown in Tables 7 and 8, we find that the      other 540s to reduce the number of active algorithms to
execution plan used in VolcanoML (i.e., CA) outper-           6. For VolcanoML with continue tuning, the number
forms the two AutoML systems on most tasks (a smaller         of active algorithms is 4 (1 survived + 3 added) when
average rank).                                                new algorithms are added, and it takes another 220 sec-
    The test results are shown in Tables 7 and 8. We can      onds to reduce the number to 1, which is LightGBM
have that the best execution plan varies over datasets.       in the added algorithms. As continue tuning avoids
Surprisingly, we find that Plan 5, which is the execu-        exploring the search space of those eliminated algotion plan introduced in VolcanoML, achieves the first         rithms, VolcanoML with continue tuning improves
place on 16 of the total 30 tasks with an average rank        the test accuracy on pc4 to 86.44% compared with
of 2.45 (the lower, the better). TPOT and autosklearn         84.74% achieved by VolcanoML with restarting.
that use a single joint block achieves an average rank        Comparison with progressive methods. We also
of 3.83 and 4.98 respectively. Therefore, the proposed        compare the progressive strategies with original ones on

Efficient End-to-End AutoML via Scalable Search Space Decomposition (Extended Paper)                                                             19

Table 7 Test accuracy with different execution plans for classification.

                        Dataset                      Plan 1       Plan 2        Plan 3     Plan 4    Plan 5       TPOT      AUSK
                        puma8NH                      0.8275       0.8312        0.8271     0.8280    0.8303       0.8306    0.8325
                        kin8nm                       0.8808       0.8886        0.8886     0.8654    0.8910       0.8706    0.8834
                        cpu cmall                    0.9122       0.9126        0.9126     0.9027    0.9127       0.9121    0.9106
                        puma32H                      0.8849       0.8864        0.8848     0.8835    0.8894       0.8955    0.8830
                        cpu act                      0.9303       0.9315        0.9305     0.9302    0.9309       0.9312    0.9298
                        bank32nh                     0.7896       0.7889        0.7838     0.7891    0.7957       0.7593    0.7957
                        mc1                          0.8796       0.8904        0.8722     0.8721    0.8975       0.8835    0.8896
                        delta elevators              0.8763       0.8760        0.8779     0.8766    0.8790       0.8835    0.8743
                        jm1                          0.6718       0.6721        0.6581     0.6473    0.6692       0.6415    0.6772
                        pendigits                    0.9932       0.9936        0.9929     0.9945    0.9937       0.9931    0.9944
                        delta ailerons               0.9235       0.9240        0.9242     0.9225    0.9259       0.9278    0.9259
                        wind                         0.8587       0.8589        0.8566     0.8583    0.8593       0.8542    0.8494
                        satimage                     0.8961       0.8954        0.8965     0.8946    0.8981       0.8961    0.8793
                        optdigits                    0.9889       0.9889        0.9883     0.9889    0.9889       0.9902    0.9818
                        phoneme                      0.8799       0.8832        0.8808     0.8791    0.8866       0.8812    0.8770
                        spambase                     0.9401       0.9406        0.9379     0.9387    0.9386       0.9385    0.9358
                        abalone                      0.6688       0.6679        0.6618     0.6614    0.6680       0.6748    0.6751
                        mammography                  0.8740       0.8783        0.8577     0.8755    0.8787       0.8568    0.8762
                        waveform                     0.8948       0.8961        0.8900     0.8835    0.8952       0.8955    0.9040
                        pollen                       0.4934       0.5013        0.5012     0.5013    0.5013       0.4961    0.4896
                        Average Rank                  4.30         2.98          4.80       5.13      2.58         3.83      4.40

Table 8 Test mean square error with different execution plans for regression.

                        Dataset                     Plan 1        Plan 2        Plan 3     Plan 4     Plan 5      TPOT      AUSK
                        bank8FM                     0.0008       0.0008         0.0008     0.0008     0.0008      0.0008     0.0009
                        bank32nh                     0.0071      0.0069         0.0070      0.0071    0.0069      0.0069     0.0070
                        kin8nm                       0.0067       0.0068        0.0073     0.0076     0.0066      0.0092     0.0148
                        puma8NH                     10.3020      10.0822        10.1293    10.1091    10.1698     10.1043   10.2109
                        cpu small                    7.3994       7.0854        7.0069     7.1741     7.0051      7.4058     8.7286
                        wind                         8.9650       8.9636        8.8993     9.2930     8.6976      8.8618     9.2261
                        cpu act                      5.0067       4.8762        4.7950     4.7983     4.7790      4.8373     6.4232
                        puma32H                      0.0001       0.0001        0.0001     0.0001     0.0000      0.0001     0.0001
                        sulfur                      0.0002       0.0002         0.0002     0.0002     0.0002      0.0003     0.0003
                        space ga                     0.0115      0.0093         0.0098      0.0099     0.0098      0.0098    0.0108
                        Average Rank                  4.95         3.00           3.40       4.30       2.20        4.00      6.15

                               (a) Classification
                                                                                         Table 10 Test balanced accuracy on 10 large datasets.
 Dataset (ID)      VolcanoML   VolcanoML +          HyperBand   BOHB      MFES-HB
 puma8NH (816)        83.03       83.12               83.01     82.91      82.96
 kin8nm (807)         89.10       89.14               88.28     88.70      89.12
                                                                                                 Datasets       TPOT     AUSK     VolcanoML
 cpu small (735)      91.27       91.33               90.97     91.08      91.14
 puma32H (752)        89.55        89.61              89.34     89.43      89.73
                                                                                                 mnist 784      0.9724   0.9701       0.9795
 cpu act (761)       93.12         93.01              92.88     92.96      92.97                 letter(2)      0.9969   0.9939       0.9969
 Average Rank          2.2          1.4                4.6       4.2        2.6                  kropt          0.8656   0.8267       0.8669
                                                                                                 mv             0.9997   0.9994       0.9997
                                (b) Regression
                                                                                                 a9a            0.8129   0.8250       0.8215
 Dataset (ID)      VolcanoML   VolcanoML +          HyperBand   BOHB      MFES-HB
 puma8NH (225)       10.1698     10.1642             10.1843    10.1654    10.2619
                                                                                                 covertype      0.7124   0.7098       0.7152
 kin8nm (189)        0.0066       0.0069              0.0081    0.0073      0.0072               2dplanes       0.9291   0.9297       0.9293
 cpu small (227)     7.0051       7.1341              7.4657    7.5272      7.5363               higgs          0.7235   0.7258       0.7279
 puma32H (308)       0.0000       0.0000             0.0000     0.0000     0.0000
 cpu act (573)        4.7790      4.7524              4.8778    5.2856      5.1506               electricity    0.9327   0.9226       0.9329
 Average Rank           2.0         1.8                 3.6       3.6         4.0                fried          0.9296   0.9280       0.9300

Table 9 Test accuracy (%) and test mean squared error of
VolcanoML compared with early-stopping methods. VolcanoML + refers to the combination of VolcanoML with                                     result, we apply it as the original strategy by default
MFES-HB.                                                                                 for VolcanoML.

five classification tasks and five regression tasks. The                                 7 Conclusion
settings follow Section 6.1 and the results are shown in
Table 11. We observe that the original strategy outper-                                  In this paper, we have presented VolcanoML, a
forms the progressive one on 8 of the 10 tasks. As a                                     scalable and extensible framework that allows users

20                                                                                                                                                                                                 Yang Li, Yu Shen, Wentao Zhang, Ce Zhang, Bin Cui

                                                                               14.6

                                                      Average Test Error (%)                                                                                     Average Test Error (%)
                                                                                                                                      TPOT                                                13                                              TPOT
                                                                                                                                      AUSK                                                                                                AUSK
                                                                               14.4
                                                                                                                                      VolcanoML                                           12                                              VolcanoML

                                                                               14.2
                                                                                                                                                                                          11

                                                                               14.0
                                                                                                                                                                                          10

                                                                                           0   1800 3600 5400 7200 9000 10800 12600 14400 16200 18000 19800                                    0   1800 3600 5400 7200 9000 10800 12600 14400 16200 18000 19800
                                                                                                             Wall Clock Time (s)                                                                                Wall Clock Time (s)
                                                                                                              (a) Wind                                                                                          (b) Kin8nm

                                                                                     7.6

                                                            Average Test Error (%)                                                                            Average Test Error (%)
                                                                                                                                      TPOT                                             6.5                                                TPOT
                                                                                     7.4                                              AUSK                                             6.4                                                AUSK
                                                                                     7.2                                              VolcanoML                                        6.3                                                VolcanoML
                                                                                     7.0                                                                                               6.2
                                                                                     6.8                                                                                               6.1

                                                                                     6.6                                                                                               6.0

                                                                                     6.4                                                                                               5.9

                                                                                     6.2
                                                                                           0   1800 3600 5400 7200 9000 10800 12600 14400 16200 18000 19800                                    0   1800 3600 5400 7200 9000 10800 12600 14400 16200 18000 19800
                                                                                                             Wall Clock Time (s)                                                                                 Wall Clock Time (s)
                                                                                                             (c) Cpu act                                                                                       (d) Spambase

Fig. 11 Test errors on four medium datasets given different time budgets.

               Number of Active Algorithms
                                                                                                                                                              tion strategies that also lead to performance-wise bet-
                                                                                     Restart
                                             10
                                                                                     Continue
                                                                                                                                                              ter ML pipelines, compared to state-of-the-art AutoML
                                              9
                                              8
                                              7
                                                                                                                                                              systems.
                                              6
                                              5
                                              4
                                              3
                                              2
                                              1
                                                                                                                                                              Acknowledgements This work is supported by the
                                                  0                             300             600    900     1200    1500    1800                           National Natural Science Foundation of China (NSFC
                                                                                               Wall Clock Time (s)                                            No.61832001, U1936104), Beijing Academy of Artificial In-
                                                                                                                                                              telligence (BAAI) and PKU-Tencent Joint Research Lab. Bin
Fig. 12 The trend of the number of active algorithms in the
                                                                                                                                                              Cui is the corresponding authors.
conditional block on pc4.
                                                                                                                                                                   Ce Zhang and the DS3Lab gratefully acknowledge the
                                                                                                                                                              support from the Swiss National Science Foundation (Project
            (a) Classification                                                                                         (b) Regression                         Number 200021 184628), Innosuisse/SNF BRIDGE Discov-
 Dataset (ID)                                Original                                Progressive         Dataset (ID)         Original   Progressive          ery (Project Number 40B2-0 187132), European Union Hori-
 puma8NH (816)                                83.03                                    82.99             puma8NH (225)        10.1698     10.2437
 kin8nm (807)                                 89.10                                    88.72             kin8nm (189)          0.0066     0.0065
                                                                                                                                                              zon 2020 Research and Innovation Programme (DAPHNE,
 cpu small (735)                              91.27                                    91.27             cpu small (227)       7.0051      7.2181             957407), Botnar Research Centre for Child Health, Swiss
 puma32H (752)                                89.55                                    88.97             puma32H (308)         0.0000      0.0001
 cpu act (761)                                93.12                                    93.09             cpu act (573)         4.7790      4.8321
                                                                                                                                                              Data Science Center, Alibaba, Cisco, eBay, Google Focused
                                                                                                                                                              Research Awards, Oracle Labs.
Table 11 Test accuracy (%) and test mean squared error for
two optimization strategies on classification and regression
tasks.                                                                                                                                                        A Appendix

                                                                                                                                                              In this section, we describe more details about the back-
                                                                                                                                                              ground, system design and implementations.
to design decomposition strategies for large AutoML
search spaces in an expressive and flexible manner.
VolcanoML introduces novel building blocks akin                                                                                                               A.1 AutoML Formulations and Motivations
to relational operators in database systems that enable expressing search space decomposition strategies                                                                                                         A.1.1 Formulations
in a structured fashion – similar to relational execution
plans. Moreover, VolcanoML introduces a Volcano-                                                                                                              Definition and Notation. There are K candidate algostyle execution model, inspired by its classic counter-                                                                                                       rithms A = {A1 , ..., AK }. Each algorithm Ai has a correpart that has been widely used for relational query                                                                                                           sponding hyper-parameter space Λi . The algorithm Ai with
                                                                                                                                                              hyper-parameter configuration λ and new feature set F is deevaluation, to execute the decomposition strategies                                                                                                           noted by Ai(λ,F ) . Given the dataset D = {Dtrain , Dvalid }
it yields. Experimental evaluation demonstrates that                                                                                                          of a learning problem, the AutoML problem is to find the
VolcanoML can generate more efficient decomposi-                                                                                                              joint algorithm, feature, and hyper-parameter configuration

Efficient End-to-End AutoML via Scalable Search Space Decomposition (Extended Paper)                                                                                      21

                                                                                             AUSK             A.1.2 Observations and Motivations about AutoML
                                                    16
                                                                                             Ideal
                                                                                                              We now present several important observations that inspired

                             Validation error (%)
                                                    14                                                        the design of VolcanoML.
                                                                                                              Observation 1. The search space can be partitioned ac-
                                                    12                                                        cording to ML algorithms. The entire search space is the
                                                                                                              union of the search spaces of individual algorithms, i.e.,
                                                    10                                                        Ω = {S 1 , ..., S K }, where S i is the joint space of features
                                                                                                              and hyper-parameters, i.e., S i = (Λi × F i ).
                                                     8                                                        Observation 2. The sub-space of algorithm Ai can be very
                                                         50     60    70       80     90       100            large, e.g., in auto-sklearn, S i usually includes more than
                                                                 Number of Hyperparameters                    50 hyper-parameters. When exploring the search spaces via
Fig. 13 Validation error on pc4 when increasing the number                                                    extensive experiments, we observe the following:
of hyper-parameters in auto-sklearn given the same time                                                        – If hyper-parameter configuration λ1 performs better than
budget.                                                                                                          λ2 , i.e., λ1 ≤ λ2 , then it often holds that (λ1 , F ) ≤
                                                                                                                 (λ2 , F ) for the joint configuration (λ, F ) with F fixed;
                                                                                                               – If FE pipeline configuration F1 performs better than F2 ,
                                                                                                                 i.e., F1 ≤ F2 , then it often holds that (λ, F1 ) ≤ (λ, F2 )
                                                                                                                 for the joint configuration (λ, F ) with λ fixed.
                                                                                                              Figure 14 presents an example for these observations. This
                                                                                                0.96

           FE Pipeline Configurations
                                                                                                              motivates us to solve the joint FE and HPO problem via
                                                                                                0.88
                                                                                                              alternating optimization. That is, we can alternate between
                                                                                                              optimizing FE and HPO, and we can fix the FE configuration
                                                                                                0.80          (resp. HPO configuration) when optimizing for HPO (resp.
                                                                                                              FE). This alternating manner is indeed similar to how human
                                                                                                0.72          experts solve the joint optimization problem manually. One
                                                                                                              obvious advantage of alternating optimization is that each
                                                                                                0.64          time only a much smaller subspace (Λi or F i ) needs to be
                                                                                                              optimized, instead of the joint space S i = (Λi × F i ).
                                                                                                0.56          Observation 3. The sensitivity of ML algorithms to FE and
                                                         Hyperparameter Configurations
                                                                                                              HPO is often different. Taking Figure 14 for example, com-
Fig. 14 The performance distribution of ML pipelines con-                                                     pared to HPO, FE has a larger influence on the performance
structed by 30 FE and HPO configurations on fri c1 using                                                      of ‘Random Forest’ on ‘fri c1’; in this case, optimizing FE
Random Forest. For FE configurations, the performance in-                                                     more frequently can bring more performance improvement.
creases from top to down; for HPO configurations, the perfor-                                                 Observation 4. The above observations motivate the use of
mance increases from left to right (The deeper, the better).                                                  meta-learning. We can learn (1) the algorithm performance
                                                                                                              across ML tasks and (2) the configuration selection of each
                                                                                                              ML algorithm across tasks. Such meta-knowledge obtained
                                                                                                              from historical tasks can greatly improve the efficiency of ML
                                                                                                              pipeline search.
                                                                                                                  Therefore, a scalable AutoML system should include two
A∗(λ∗ ,F ∗ ) that minimizes the loss metric (e.g., the validation                                             basic components: (1) an efficient framework that can navierror on Dvalid ):                                                                                            gate in a huge search space, and (2) a meta-learning module
                                                                                                              that can extract knowledge from previously ML tasks and
                                                                                                              apply it to new tasks.
A∗
 (λ∗ ,F ∗ ) =                                             argmin         L(Ai(λ,F ) ; D),              (14)
                               Ai ∈A,λ∈Λi ,F ∈F i

                                                                                                              A.2 VolcanoML Components and Implementations
where F i = Gen(Ai , D, op) is the feature space of Ai that
can be generated from the raw feature (data) set D, and op                                                    A.2.1 Compenents and Search Space
is the set of available FE operators.
Challenge: Ever-growing Search Space. Enriching the                                                           Feature Engineering. The feature engineering pipeline is
search space can lead to performance improvement since the                                                    shown in Figure 2. It comprises four sequential stages: preenriched search space may bring better configurations. How-                                                   processors (compulsory), scalers (5 possible operators), balever, an ever-growing search space can significantly increase                                                 ancers (1 possible operators) and feature transformers (13
the complexity of searching for ML pipelines. Existing Au-                                                    possible operstors). For each of the latter three stages, VoltoML systems usually can only explore very limited config-                                                    canoML picks one operator and then execute the entire
urations in a huge search space, and thus suffer from the                                                     pipeline. Table 13 presents the details of each operator. The
low-efficiency issue [53] that hampers the effectiveness of Au-                                               total number of hyper-parameters for FE is 52.
toML systems. In Figure 13, we provide a brief example of                                                         We follow the design of the search space for feature enauto-sklearn, one state-of-the-art system AutoML system.                                                      gineering in the existing AutoML systems, e.g., autosklearn
Its search algorithm cannot scale to a high-dimensional search                                                and TPOT. It limits the search space for feature engineering
space [53]. To alleviate this issue, in this paper we focus on                                                by adopting a fixed pipeline including different stages, and
developing a scalable AutoML system.                                                                          each stage is equipped with an operation (featurizer) that

22                                                                       Yang Li, Yu Shen, Wentao Zhang, Ce Zhang, Bin Cui

is selected from a pool of featurizers. The pool of featur-        Table 13 Hyper-parameters of FE operators in Volizers at each stage is relatively small, and Bayesian opti-        canoML.
mization can be used to choose the proper featurizers for
                                                                        Type of Operator            #λ   cat (cond)   cont (cond)
each stage. When the pool of featurizers is very large, highdimensional Bayesian optimization algorithms could work                 One-hot encoder             0        -             better. In many real-world cases, though this architecture is           Imputer                     1        -           1 (-)
not good enough for feature engineering (effectiveness), there          Minmax                      0        -             still remains space to explore to conduct feature engineer-             Normalizer                  0        -             ing effectively and efficiently. To support real scenarios, Vol-        Quantile                    2      1 (-)         1 (-)
canoML provides API for user-defined feature engineering                Robust                      2        -           2 (-)
                                                                        Standard                    0        -             operators and we recommend users to add domain-specific
feature engineering operators to the search space for better            Weight Balancer             0        -             search performance. In addition, users can replace the original         Cross features              1        -           1 (-)
feature engineering part in VolcanoML with other iterative              Fast ICA                    4      3 (1)         1 (1)
feature engineering methods easily.                                     Feature agglomeration       4      3 (2)         1 (-)
                                                                        Kernel PCA                  5      1 (1)         4 (3)
ML Algorithms. VolcanoML implements 11 algorithms                       Rand. kitchen sinks         2        -           2 (-)
                                                                        LDA decomposer              1      1 (-)           for classification and 10 algorithms for regression, with a to-
                                                                        Nystroem sampler            5      1 (1)         4 (3)
tal of 50 and 49 hyper-parameters respectively. The built-              PCA                         2      1 (-)         1 (-)
in algorithms include linear models, support vector machine,            Polynomial                  2      1 (-)         1 (-)
discriminant analysis, nearest neighbors, and ensembles. Ta-            Random trees embed.         5      1 (-)         4 (-)
ble 12 presents the details.                                            SVD                         1        -           1 (-)
                                                                        Select percentile           2      1 (-)         1 (-)
Ensemble Methods. Ensembles that combine predictions                    Select generic univariate   3      2 (-)         1 (-)
from multiple base models have been known to outperform                 Extra trees preprocess-     5      2 (-)         3 (-)
individual models, often drastically reducing the variance of           ing
the final predictions [17]. VolcanoML provides four ensem-              Linear SVM preprocess-      5      3 (3)         2 (-)
ble methods: bagging, blending, stacking, and ensemble se-              ing
lection [12]. During the search process, the top Ntop configurations for each algorithm are recorded and the corresponding models are stored. After the optimization budget          A.2.2 Programming Interface
exhausts, the saved models are treated as the base models
for the ensemble method. We use ensemble selection as the          Consider a tabular dataset of raw values in a CSV file, named
default method and build an ensemble of size 50.                   train.csv, where the last column represents the label. We
                                                                   take a classification task as an example. With VolcanoML,
                                                                   only six lines of code are needed for searching and model
                                                                   evaluation.
Table 12 Hyper-parameters of ML algorithms in VolcanoML. We distinguish categorical (cat) hyper-parameters          from ... import DataManager , Classifier
from numerical (cont) ones. The numbers in the brackets are        dm = DataManager ()
conditional hyper-parameters.                                      train_node = dm . load_train ( ’ train . csv ’)
                                                                   test_node = dm . load_test ( ’ test . csv ’)
     Type of Classifier       #λ   cat (cond)   cont (cond)        clf = Classifier (** params ). fit ( train_node )
     AdaBoost                  4      1 (-)        3 (-)
                                                                   predictions = clf . predict ( test_node )
     Random forest             5      2 (-)        3 (-)
                                                                       By calling load train and load test, the data manager
     Extra trees               5      2 (-)        3 (-)
     Gradient boosting         7      1 (-)        6 (-)           automatically identifies the type of each feature (continuous,
     KNN                       2      1 (-)        1 (-)           discrete, or categorical), imputes missing values, and converts
     LDA                       4      1 (-)        3 (1)           string-like features to one-hot vectors. By calling fit, Vol-
     QDA                       1        -          1 (-)           canoML splits the dataset into folds for training and val-
     Logistic regression       4      2 (-)        2 (-)           idation, evaluates various configurations, and generates an
     Liblinear SVC             5      2 (2)        3 (-)           ensemble from each individual configuration. For users who
     LibSVM SVC                7      2 (2)        5 (-)
                                                                   need to customize the search process, Classifier provides
     LightGBM                  6        -          6 (-)
                                                                   additional parameters to specify:
     Type of Regressor        #λ   cat (cond)   cont (cond)
                                                                    – time limit controls the total runtime of the search pro-
     AdaBoost                  4      1 (-)        3 (-)              cess;
     Random forest             5      2 (-)        3 (-)
                                                                    – include algorithms specifies which algorithms are in-
     Extra trees               5      2 (-)        3 (-)
     Gradient boosting         7      1 (-)        6 (-)              cluded (if not specified, all built-in algorithms are in-
     KNN                       2      1 (-)        1 (-)              cluded);
     Lasso                     3        -          3 (-)            – ensemble method chooses which ensemble strategy to use;
     Ridge                     4      1 (-)        3 (-)            – enable meta determines whether to use meta-learning to
     Liblinear SVC             5      2 (2)        3 (-)              accelerate the search process;
     LibSVM SVC                8      3 (3)        5 (-)            – metric specifies the metric used to evaluate the perfor-
     LightGBM                  6        -          6 (-)
                                                                      mance of each configuration.
                                                                   Customized Components. VolcanoML provides APIs
                                                                   to easily enrich the search space, such as the stage in FE

Efficient End-to-End AutoML via Scalable Search Space Decomposition (Extended Paper)                                             23

pipeline, FE operators, and ML algorithms. The following is             machine learning. In: Proceedings of the Annual Conthe syntax of defining customized components:                           ference on Innovative Data Systems Research (CIDR),
                                                                        2021. CIDR (2021)
from ... import add_clas sifier
                                                                     2. Bai, Y., Li, Y., Shen, Y., Yang, M., Zhang, W., Cui, B.:
from ... import u p d a t e _ F E P i p e l i n e
                                                                        Autodc: an automatic machine learning framework for
from ... import BaseModel , BaseOperator
                                                                        disease classification. Bioinformatics (2022)
                                                                     3. Bardenet, R., Brendel, M., Kégl, B., Sebag, M.: Collab-
# Add new ML algorithm .
                                                                        orative hyperparameter tuning. In: International conferclass CustomizedM o de l ( BaseModel ):
                                                                        ence on machine learning, pp. 199–207. PMLR (2013)
    def fit (x , y ): ...
    def predict ( x ): ...                                           4. Barnes, J.: Azure machine learning. Microsoft Azure Es-
    def get_sear c h _ s p a c e (): ...                                sentials. 1st ed, Microsoft (2015)
                                                                     5. Baylor, D., Breck, E., Cheng, H.T., Fiedel, N., Foo, C.Y.,
add_classifier ( Cu st o mi ze dM o de l )                              Haque, Z., Haykal, S., Ispir, M., Jain, V., Koc, L., et al.:
                                                                        Tfx: A tensorflow-based production-scale machine learn-
# Add new FE operator .                                                 ing platform. In: Proceedings of the 23rd ACM SIGKDD
class CustomizedOP ( BaseOperator ):                                    International Conference on Knowledge Discovery and
    def operate ( x ): ...                                              Data Mining, pp. 1387–1395 (2017)
    def get_sear c h _ s p a c e (): ...                             6. Bergstra, J., Bengio, Y.: Random search for hyper-
                                                                        parameter optimization. Journal of Machine Learning
# Customize FE pipeline .                                               Research 13, 281–305 (2012)
u p d at e_F EPip eli n e ([ ‘ new_stage ’ , ...] ,                  7. Bergstra, J.S., Bardenet, R., Bengio, Y., Kégl, B.: Algo-
       { ‘ new_stage ’: [ CustomizedOP ] ,                              rithms for hyper-parameter optimization. In: Advances
       ...})                                                            in neural information processing systems, pp. 2546–2554
                                                                        (2011)
    It is important to note that, auto-sklearn does not sup-         8. Boehm, M., Antonov, I., Baunsgaard, S., Dokter, M.,
port adding a new stage or updating the existing stages in              Ginthör, R., Innerebner, K., Klezin, F., Lindstaedt, S.,
the FE pipeline. In addition, auto-sklearn cannot add an                Phani, A., Rath, B., et al.: Systemds: A declarative maoperator for any stage (e.g., adding smote balancer to the              chine learning system for the end-to-end data science lifestage balancer), while VolcanoML supports this.                         cycle. arXiv preprint arXiv:1909.02976 (2019)
                                                                     9. Breck, E., Polyzotis, N., Roy, S., Whang, S., Zinkevich,
                                                                        M.: Data validation for machine learning. In: MLSys
A.3 Experiment Datasets                                                 (2019)
                                                                    10. Burges, C.: From ranknet to lambdarank to lambdamart:
In our experiments, we splitted each dataset into five folds.           An overview. Learning 11 (2010)
Four are used for training and the remaining one is used for        11. CarøE, C.C., Schultz, R.: Dual decomposition in stochastesting. The 60 OpenML datasets used are presented as fol-              tic integer programming. Operations Research Letters
lows (in the form of “dataset name (OpenML id)”):                       24(1-2), 37–45 (1999)
Classification Datasets. kc1 (1067), quake (772), seg-              12. Caruana, R., Niculescu-Mizil, A., Crew, G., Ksikes, A.:
ment (36), ozone-level-8hr (1487), space ga (737), sick                 Ensemble selection from libraries of models. In: Proceed-
(38), pollen (871), analcatdata supreme (728), abalone                  ings, Twenty-First International Conference on Machine
(183), spambase (44), waveform(2) (979), phoneme (1489),                Learning, ICML 2004 (2004). DOI 10.1145/1015330.
page-blocks(2) (1021), optdigits(28), satimage (182), wind              1015432
(847), delta ailerons (803), puma8NH (816), kin8nm (807),           13. Chen, B., Wu, H., Mo, W., Chattopadhyay, I., Lipson,
puma32H (752), cpu act (761), bank32nh (833), mc1 (1056),               H.: Autostacker: A compositional evolutionary learning
delta elevators (819), jm1 (1053), pendigits (32), mammog-              system. In: Proceedings of the Genetic and Evolutionary
raphy (310), ailerons (734), eeg (1471), letter(2) (977), kropt         Computation Conference, pp. 402–409 (2018)
(184), mv (881), fried (901), 2dplanes (727), electricity (151),    14. De Sa, C., Ratner, A., Ré, C., Shin, J., Wang, F., Wu,
a9a (A2), mnist 784 (554), higgs (23512), covertype (180).              S., Zhang, C.: Deepdive: Declarative knowledge base con-
                                                                        struction. ACM SIGMOD Record 45(1), 60–67 (2016)
Regression Datasets. stock (223), socmob (541), Moneyball (41021), insurance (A1), weather izmir (42369), us crime       15. Dechter, R.: Bucket elimination: A unifying framework
(315), debutanizer (23516), space ga (507), pollen (529),               for probabilistic inference. In: Learning in graphical modwind (503), bank8FM (572), bank32nh (558), kin8nm                       els, pp. 75–104. Springer (1998)
(189), puma8NH (225), cpu act (573), puma32H (308),                 16. Dewancker, I., McCourt, M., Clark, S., Hayes, P., Johncpu small (227), visualizing soil (668), sulfur (23515), rain-          son, A., Ke, G.: A strategy for ranking optimization
fall bangladesh (41539).                                                methods using multiple criteria. In: Workshop on Au-
     Since the datasets insurance and a9a are not collected in          tomatic Machine Learning, pp. 11–20. PMLR (2016)
OpenML, we use A1 and A2 as their OpenML ID instead.                17. Dietterich, T.G.: Ensemble methods in machine learn-
                                                                        ing. In: Lecture Notes in Computer Science (includ-
                                                                        ing subseries Lecture Notes in Artificial Intelligence and
                                                                        Lecture Notes in Bioinformatics) (2000). DOI 10.1007/
References                                                              3-540-45014-9 1
                                                                    18. Drori, I., Krishnamurthy, Y., Rampin, R., De, R.,
 1. Aguilar Melgar, L., Dao, D., Gan, S., Gürel, N.M., Hol-            Lourenco, P., Ono, J.P., Cho, K., Silva, C., Freire, J.:
    lenstein, N., Jiang, J., Karlaš, B., Lemmin, T., Li, T., Li,       AlphaD3M: Machine Learning Pipeline Synthesis. Au-
    Y., et al.: Ease. ml: A lifecycle management system for             toML Workshop at ICML (2018)

24                                                                      Yang Li, Yu Shen, Wentao Zhang, Ce Zhang, Bin Cui

19. Efimova, V., Filchenkov, A., Shalamov, V.: Fast auto-         38. Jones, D.R., Schonlau, M., Welch, W.J.: Efficient global
    mated selection of learning algorithm and its hyperpa-            optimization of expensive black-box functions. Journal
    rameters by reinforcement learning. In: International             of Global optimization 13(4), 455–492 (1998)
    Conference on Machine Learning AutoML Workshop                39. Kandasamy, K., Dasarathy, G., Schneider, J., Póczos, B.:
    (2017)                                                            Multi-fidelity bayesian optimisation with continuous ap-
20. Eggensperger, K., Feurer, M., Hutter, F., Bergstra, J.,           proximations. In: International Conference on Machine
    Snoek, J., Hoos, H., Leyton-Brown, K.: Towards an em-             Learning, pp. 1799–1808. PMLR (2017)
    pirical foundation for assessing bayesian optimization of     40. Kanter, J.M., Veeramachaneni, K.: Deep feature synthe-
    hyperparameters. In: NIPS workshop on Bayesian Opti-              sis: Towards automating data science endeavors. In: 2015
    mization in Theory and Practice, vol. 10, p. 3 (2013)             IEEE International Conference on Data Science and Ad-
21. Falkner, S., Klein, A., Hutter, F.: Bohb: Robust and effi-        vanced Analytics, DSAA 2015, Paris, France, October
    cient hyperparameter optimization at scale. In: Interna-          19-21, 2015, pp. 1–10. IEEE (2015)
    tional Conference on Machine Learning, pp. 1437–1446.         41. Katz, G., Shin, E.C.R., Song, D.: Explorekit: Automatic
    PMLR (2018)                                                       feature generation and selection. In: 2016 IEEE 16th
22. Feurer, M., Klein, A., Eggensperger, K., Springenberg,            International Conference on Data Mining (ICDM), pp.
    J., Blum, M., Hutter, F.: Efficient and robust automated          979–984. IEEE (2016)
    machine learning. In: Advances in neural information          42. Kaul,     A.,   Maheshwary,       S.,   Pudi,    V.:   Au-
    processing systems, pp. 2962–2970 (2015)                          tolearn—automated feature generation and selection.
23. Feurer, M., Letham, B., Bakshy, E.: Scalable meta-
                                                                      In: 2017 IEEE International Conference on data mining
    learning for bayesian optimization using ranking-
                                                                      (ICDM), pp. 217–226. IEEE (2017)
    weighted gaussian process ensembles. In: AutoML Work-
                                                                  43. Khurana, U., Samulowitz, H., Turaga, D.: Feature en-
    shop at ICML (2018)
24. Garcia-Molina, H., Ullman, J.D., Widom, J.: Database              gineering for predictive modeling using reinforcement
    Systems: The Complete Book, 2 edn. Prentice Hall Press,           learning. In: 32nd AAAI Conf. Artif. Intell. AAAI 2018
    USA (2008)                                                        (2018)
25. Ghoting, A., Krishnamurthy, R., Pednault, E., Rein-           44. Khurana, U., Turaga, D., Samulowitz, H., Parthasrathy,
    wald, B., Sindhwani, V., Tatikonda, S., Tian, Y.,                 S.: Cognito: Automated feature engineering for super-
    Vaithyanathan, S.: Systemml: Declarative machine learn-           vised learning. In: 2016 IEEE 16th International Confer-
    ing on mapreduce. In: 2011 IEEE 27th International Con-           ence on Data Mining Workshops (ICDMW), pp. 1304–
    ference on Data Engineering, pp. 231–242. IEEE (2011)             1307. IEEE (2016)
26. Golovin, D., Solnik, B., Moitra, S., Kochanski, G., Karro,    45. Klein, A., Falkner, S., Bartels, S., Hennig, P., Hutter,
    J., Sculley, D.: Google vizier: A service for black-box op-       F.: Fast bayesian optimization of machine learning hy-
    timization. In: Proceedings of the 23rd ACM SIGKDD                perparameters on large datasets. In: Proceedings of the
    International Conference on Knowledge Discovery and               20th International Conference on Artificial Intelligence
    Data Mining, pp. 1487–1495. ACM (2017)                            and Statistics, pp. 528–536 (2017)
27. Google: Google prediction api,. https://developers.           46. Komer, B., Bergstra, J., Eliasmith, C.: Hyperopt-sklearn:
    google.com/prediction (2020)                                      automatic hyperparameter configuration for scikit-learn.
28. Graefe, G.: Volcano—An Extensible and Parallel Query              In: ICML workshop on AutoML, vol. 9. Citeseer (2014)
    Evaluation System. IEEE Transactions on Knowledge             47. Kraska, T.: Northstar: An interactive data science sys-
    and Data Engineering (1994)                                       tem. Proceedings of the VLDB Endowment 11(12),
29. He, X., Zhao, K., Chu, X.: Automl: A survey of the state-         2150–2164 (2018)
    of-the-art. Knowledge-Based Systems 212, 106622 (2021)        48. Krishnan, S., Wang, J., Wu, E., Franklin, M.J., Goldberg,
30. Hu, Y.Q., Yu, Y., Tu, W.W., Yang, Q., Chen, Y., Dai,              K.: Activeclean: Interactive data cleaning for statistical
    W.: Multi-fidelity automatic hyper-parameter tuning via           modeling. Proceedings of the VLDB Endowment 9(12),
    transfer series expansion. AAAI (2019)                            948–959 (2016)
31. Hutter, F., Hoos, H., Leyton-Brown, K.: An efficient ap-      49. LeDell, E., Poirier, S.: H2o automl: Scalable automatic
    proach for assessing hyperparameter importance. In: 31st          machine learning. In: Proceedings of the AutoML Work-
    International Conference on Machine Learning, ICML                shop at ICML, vol. 2020 (2020)
    2014 (2014)                                                   50. Levine, N., Crammer, K., Mannor, S.: Rotting bandits.
32. Hutter, F., Hoos, H.H., Leyton-Brown, K.: Sequential
                                                                      In: Advances in NIPS, pp. 3074–3083 (2017)
    model-based optimization for general algorithm config-
                                                                  51. Li, L., Jamieson, K., DeSalvo, G., Rostamizadeh, A., Tal-
    uration. In: International Conference on Learning and
                                                                      walkar, A.: Hyperband: A novel bandit-based approach
    Intelligent Optimization, pp. 507–523. Springer (2011)
33. Hutter, F., Kotthoff, L., Vanschoren, J. (eds.): Au-              to hyperparameter optimization. Proceedings of the In-
    tomated Machine Learning: Methods, Systems, Chal-                 ternational Conference on Learning Representations pp.
    lenges.      Springer (2018).       In press, available at        1–48 (2018)
    http://automl.org/book.                                       52. Li, T., Zhong, J., Liu, J., Wu, W., Zhang, C.: Ease. ml:
34. Hutter, F., Lücke, J., Schmidt-Thieme, L.: Beyond man-           Towards multi-tenant resource sharing for machine learn-
    ual tuning of hyperparameters. KI-Künstliche Intelligenz         ing workloads. Proceedings of the VLDB Endowment
    29(4), 329–337 (2015)                                             11(5), 607–620 (2018)
35. IBM: Ibmwatson studio autoai. https://www.ibm.com/            53. Li, Y., Jiang, J., Gao, J., Shao, Y., Zhang, C., Cui, B.:
    cloud/watson-studio/autoai (2020)                                 Efficient automatic cash via rising bandits. In: AAAI,
36. Jamieson, K., Talwalkar, A.: Non-stochastic best arm              pp. 4763–4771 (2020)
    identification and hyperparameter optimization. In: Ar-       54. Li, Y., Shen, Y., Jiang, H., Bai, T., Zhang, W., Zhang, C.,
    tificial Intelligence and Statistics, pp. 240–248 (2016)          Cui, B.: Transfer learning based search space design for
37. Jiang, H., Shen, Y., Li, Y.: Automated hyperparameter             hyperparameter tuning. Proceedings of the 28th ACM
    optimization challenge at cikm 2021 analyticcup. arXiv            SIGKDD Conference on Knowledge Discovery & Data
    preprint arXiv:2111.00513 (2021)                                  Mining (2022)

Efficient End-to-End AutoML via Scalable Search Space Decomposition (Extended Paper)                                          25

55. Li, Y., Shen, Y., Jiang, H., Zhang, W., Li, J., Liu, J.,      70. Poloczek, M., Wang, J., Frazier, P.: Multi-information
    Zhang, C., Cui, B.: Hyper-tune: Towards efficient hyper-          source optimization. In: Advances in Neural Information
    parameter tuning at scale. Proceedings of the VLDB                Processing Systems, pp. 4288–4298 (2017)
    Endowment 15 (2022)                                           71. Ratner, A., et al.: Snorkel: Rapid training data creation
56. Li, Y., Shen, Y., Jiang, H., Zhang, W., Yang, Z., Zhang,          with weak supervision. PVLDB (2017)
    C., Cui, B.: Transbo: Hyperparameter optimization via         72. Rekatsinas, T., Chu, X., Ilyas, I.F., Ré, C.: Holoclean:
    two-phase transfer learning. Proceedings of the 28th              Holistic data repairs with probabilistic inference. Pro-
    ACM SIGKDD Conference on Knowledge Discovery &                    ceedings of the VLDB Endowment 10(11) (2017)
    Data Mining (2022)                                            73. Research, M.: Microsoft nni.        https://github.com/
57. Li, Y., Shen, Y., Jiang, J., Gao, J., Zhang, C., Cui, B.:         Microsoft/nni (2020)
    Mfes-hb: Efficient hyperband with multi-fidelity quality      74. de Sá, A.G., Pinto, W.J.G., Oliveira, L.O.V., Pappa,
    measurements. In: Proceedings of the AAAI Conference              G.L.: RECIPE: A grammar-based framework for auto-
    on Artificial Intelligence, vol. 35, pp. 8491–8500 (2021)         matically evolving classification pipelines. In: Lecture
                                                                      Notes in Computer Science (including subseries Lec-
58. Li, Y., Shen, Y., Zhang, W., Chen, Y., Jiang, H., Liu, M.,
                                                                      ture Notes in Artificial Intelligence and Lecture Notes
    Jiang, J., Gao, J., Wu, W., Yang, Z., et al.: Openbox: A
                                                                      in Bioinformatics) (2017)
    generalized black-box optimization service. In: Proceed-
                                                                  75. Schawinski, K., et al.: Generative adversarial networks re-
    ings of the 27th ACM SIGKDD Conference on Knowledge
                                                                      cover features in astrophysical images of galaxies beyond
    Discovery & Data Mining, pp. 3209–3219 (2021)
                                                                      the deconvolution limit. MNRAS Letters (2017)
59. Li, Y., Shen, Y., Zhang, W., Jiang, J., Ding, B., Li, Y.,     76. Sen, R., Kandasamy, K., Shakkottai, S.: Noisy blackbox
    Zhou, J., Yang, Z., Wu, W., Zhang, C., et al.: Volcanoml:         optimization with multi-fidelity queries: A tree search ap-
    Speeding up end-to-end automl via scalable search space           proach. arXiv preprint arXiv:1810.10482 (2018)
    decomposition. Proceedings of the VLDB Endowment              77. Shahriari, B., Swersky, K., Wang, Z., Adams, R.P.,
    (2021)                                                            De Freitas, N.: Taking the human out of the loop: A re-
60. Liaw, R., Liang, E., Nishihara, R., Moritz, P., Gonza-            view of bayesian optimization. Proceedings of the IEEE
    lez, J.E., Stoica, I.: Tune: A research platform for dis-         104(1), 148–175 (2015)
    tributed model selection and training. arXiv preprint         78. Smith, M.J., Sala, C., Kanter, J.M., Veeramachaneni, K.:
    arXiv:1807.05118 (2018)                                           The machine learning bazaar: Harnessing the ml ecosys-
61. Liberty, E., Karnin, Z., Xiang, B., Rouesnel, L., Coskun,         tem for effective system development. In: Proceedings
    B., Nallapati, R., Delgado, J., Sadoughi, A., Astashonok,         of the 2020 ACM SIGMOD International Conference on
    Y., Das, P., et al.: Elastic machine learning algorithms in       Management of Data, pp. 785–800 (2020)
    amazon sagemaker. In: Proceedings of the 2020 ACM             79. Snoek, J., Larochelle, H., Adams, R.P.: Practical
    SIGMOD International Conference on Management of                  bayesian optimization of machine learning algorithms. In:
    Data, pp. 731–737 (2020)                                          Advances in neural information processing systems, pp.
62. Liu, C., Zoph, B., Neumann, M., Shlens, J., Hua, W.,              2951–2959 (2012)
    Li, L.J., Fei-Fei, L., Yuille, A., Huang, J., Murphy, K.:     80. Swersky, K., Snoek, J., Adams, R.P.: Multi-task bayesian
    Progressive neural architecture search. In: Proceedings of        optimization. In: Advances in neural information process-
    the European Conference on Computer Vision (ECCV),                ing systems, pp. 2004–2012 (2013)
    pp. 19–34 (2018)                                              81. Takeno, S., Fukuoka, H., Tsukada, Y., Koyama, T.,
63. Liu, S., Ram, P., Bouneffouf, D., Bramble, G., Conn,              Shiga, M., Takeuchi, I., Karasuyama, M.: Multi-fidelity
    A.R., Samulowitz, H., Gray, A.G.: An admm based                   bayesian optimization with max-value entropy search and
    framework for automl pipeline configuration pp. 4892–             its parallelization. In: International Conference on Ma-
    4899 (2020)                                                       chine Learning, pp. 9334–9345. PMLR (2020)
64. Mohr, F., Wever, M., Hüllermeier, E.: Ml-plan: Auto-         82. Thornton, C., Hutter, F., Hoos, H.H., Leyton-Brown,
    mated machine learning via hierarchical planning. Ma-             K.: Auto-weka: Combined selection and hyperparameter
    chine Learning 107(8), 1495–1515 (2018)                           optimization of classification algorithms. In: Proceed-
65. Moritz, P., Nishihara, R., Wang, S., Tumanov, A., Liaw,           ings of the 19th ACM SIGKDD international conference
    R., Liang, E., Elibol, M., Yang, Z., Paul, W., Jordan,            on Knowledge discovery and data mining, pp. 847–855
    M.I., et al.: Ray: A distributed framework for emerging           (2013)
                                                                  83. Van Rijn, J.N., Hutter, F.: Hyperparameter importance
    {AI} applications. In: 13th {USENIX} Symposium on
                                                                      across datasets. In: Proceedings of the 24th ACM
    Operating Systems Design and Implementation ({OSDI}
                                                                      SIGKDD International Conference on Knowledge Discov-
    18), pp. 561–577 (2018)
                                                                      ery & Data Mining, pp. 2367–2376 (2018)
66. Nakandala, S., Kumar, A., Papakonstantinou, Y.: Incre-
                                                                  84. Vanschoren, J.: Meta-learning: A survey.             CoRR
    mental and approximate inference for faster occlusion-
                                                                      abs/1810.03548 (2018). URL http://arxiv.org/abs/
    based deep cnn explanations. In: Proceedings of the 2019
                                                                      1810.03548
    International Conference on Management of Data, pp.           85. Vanschoren, J., Van Rijn, J.N., Bischl, B., Torgo, L.:
    1589–1606 (2019)                                                  Openml: networked science in machine learning. ACM
67. Nakandala, S., Zhang, Y., Kumar, A.: Cerebro: A data              SIGKDD Explorations Newsletter 15(2), 49–60 (2014)
    system for optimized deep learning model selection. Pro-      86. Vartak, M., et al.: Modeldb: A system for machine learn-
    ceedings of the VLDB Endowment 13(12), 2159–2173                  ing model management. In: HILDA (2016)
    (2020)                                                        87. Vilalta, R., Drissi, Y.: A perspective view and survey
68. Nargesian, F., Samulowitz, H., Khurana, U., Khalil, E.B.,         of meta-learning. Artificial Intelligence Review (2002).
    Turaga, D.S.: Learning feature engineering for classifica-        DOI 10.1023/A:1019956318069
    tion. In: Ijcai, pp. 2529–2535 (2017)                         88. Wang, Z., Zoghi, M., Hutter, F., Matheson, D., De Fre-
69. Olson, R.S., Moore, J.H.: Tpot: A tree-based pipeline op-         itas, N.: Bayesian optimization in high dimensions via
    timization tool for automating machine learning. In: Au-          random embeddings. In: Twenty-Third International
    tomated Machine Learning, pp. 151–160. Springer (2019)            Joint Conference on Artificial Intelligence (2013)

26                                                                Yang Li, Yu Shen, Wentao Zhang, Ce Zhang, Bin Cui

89. Wistuba, M., Schilling, N., Schmidt-Thieme, L.: Two-
    stage transfer surrogate model for automatic hyperpa-
    rameter optimization. In: Joint European conference on
    machine learning and knowledge discovery in databases,
    pp. 199–214. Springer (2016)
90. Wu, J., Toscano-Palmerin, S., Frazier, P.I., Wilson, A.G.:
    Practical multi-fidelity bayesian optimization for hyper-
    parameter tuning. In: Uncertainty in Artificial Intelli-
    gence, pp. 788–798. PMLR (2020)
91. Wu, R., Chaba, S., Sawlani, S., Chu, X., Thirumuru-
    ganathan, S.: Zeroer: Entity resolution using zero labeled
    examples. In: Proceedings of the 2020 ACM SIGMOD
    International Conference on Management of Data, pp.
    1149–1164 (2020)
92. Wu, W., Flokas, L., Wu, E., Wang, J.: Complaint-driven
    training data debugging for query 2.0. In: Proceedings
    of the 2020 ACM SIGMOD International Conference on
    Management of Data, pp. 1317–1334 (2020)
93. Yao, Q., Wang, M., Chen, Y., Dai, W., Li, Y.F., Tu,
    W.W., Yang, Q., Yu, Y.: Taking human out of learning
    applications: A survey on automated machine learning.
    arXiv preprint arXiv:1810.13306 (2018)
94. Zaharia, M., et al.: Accelerating the machine learning
    lifecycle with mlflow. IEEE Data Eng. Bull. (2018)
95. Zhang, X., Chang, Z., Li, Y., Wu, H., Tan, J., Li, F., Cui,
    B.: Facilitating database tuning with hyper-parameter
    optimization: A comprehensive experimental evaluation.
    Proceedings of the VLDB Endowment (2021)
96. Zhang, X., Wu, H., Li, Y., Tan, J., Li, F., Cui, B.: To-
    wards dynamic and safe configuration tuning for cloud
    databases. Proceedings of the 2022 ACM SIGMOD In-
    ternational Conference on Management of Data (2022)
97. Zöller, M.A., Huber, M.F.: Benchmark and survey of au-
    tomated machine learning frameworks. Journal of artifi-
    cial intelligence research 70, 409–472 (2021)
