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
---

# TransBO: Hyperparameter Optimization via Two-Phase Transfer Learning

TransBO: Hyperparameter Optimization via Two-Phase Transfer
                                                                  Learning
                                                       Yang Li†§ , Yu Shen† , Huaijun Jiang† , Wentao Zhang† , Zhi Yang† , Ce Zhang‡ , Bin Cui†⋄
                                                   † School of CS & Key Laboratory of High Confidence Software Technologies (MOE), Peking University, China
                                                                                                       § Data Platform, TEG, Tencent Inc., China
                                                                            ‡ Department of Computer Science, Systems Group, ETH Zurich, Switzerland
                                                                          ⋄ Institute of Computational Social Science, Peking University (Qingdao), China
                                                         † {liyang.cs, shenyu, wentao.zhang, jianghuaijun, yangzhi, bin.cui}@pku.edu.cn ‡ ce.zhang@inf.ethz.ch

                                        ABSTRACT                                                                                      deep neural network). As a result, automatically tuning the hy-

arXiv:2206.02663v1 [cs.LG] 6 Jun 2022
                                        With the extensive applications of machine learning models, auto-                             perparameters has attracted lots of interest from both academia
                                        matic hyperparameter optimization (HPO) has become increasingly                               and industry [59]. Bayesian optimization (BO) is one of the most
                                        important. Motivated by the tuning behaviors of human experts, it                             prevailing frameworks for automatic hyperparameter optimization
                                        is intuitive to leverage auxiliary knowledge from past HPO tasks to                           (HPO) [4, 20, 48]. The main idea of BO is to use a surrogate model,
                                        accelerate the current HPO task. In this paper, we propose TransBO,                           typically a Gaussian Process (GP) [42], to describe the relation-
                                        a novel two-phase transfer learning framework for HPO, which                                  ship between a hyperparameter configuration and its performance
                                        can deal with the complementary nature among source tasks and                                 (e.g., validation error), and then utilize this surrogate to determine
                                        dynamics during knowledge aggregation issues simultaneously.                                  the next configuration to evaluate by optimizing an acquisition
                                        This framework extracts and aggregates source and target knowl-                               function that balances exploration and exploitation.
                                        edge jointly and adaptively, where the weights can be learned in a                               Hyperparameter optimization (HPO) is often a computationally-
                                        principled manner. The extensive experiments, including static and                            intensive process as one often needs to choose and evaluate hyper-
                                        dynamic transfer learning settings and neural architecture search,                            parameter configurations by training and validating the correspond-
                                        demonstrate the superiority of TransBO over the state-of-the-arts.                            ing ML models. However, for ML models that are computationally
                                                                                                                                      expensive to train (e.g., deep learning models or models trained
                                        CCS CONCEPTS                                                                                  on large-scale datasets), vanilla Bayesian optimization (BO) suf-
                                                                                                                                      fers from the low-efficiency issue [9, 31, 33] due to insufficient
                                        • Computing methodologies → Machine learning; Transfer
                                                                                                                                      configuration evaluations within a limited budget.
                                        learning.
                                                                                                                                         (Opportunities) Production ML models usually need to be
                                        KEYWORDS                                                                                      constantly re-tuned as new task / dataset comes or underlying code
                                                                                                                                      bases are updated, e.g., in the AutoML applications. The optimal
                                        hyperparameter optimization, black-box optimization, bayesian                                 hyperparameters may also change as the data and code change, and
                                        optimization, transfer learning                                                               so should be frequently re-optimized. Although they may change
                                        ACM Reference Format:                                                                         significantly, the region of good or bad configurations may still
                                        Yang Li, Yu Shen, Huaijun Jiang, Wentao Zhang, Zhi Yang, Ce Zhang, Bin                        share some correlation with those of previous tasks [60], and this
                                        Cui. 2022. TransBO: Hyperparameter Optimization via Two-Phase Transfer                        provides the opportunities towards a faster hyperparameter search.
                                        Learning. In Proceedings of the 28th ACM SIGKDD Conference on Knowledge
                                                                                                                                      Therefore, we can leverage the tuning results (i.e., observations) from
                                        Discovery and Data Mining (KDD ’22), August 14–18, 2022, Washington, DC,
                                                                                                                                      previous HPO tasks (source tasks) to speed up the current HPO task
                                        USA. ACM, New York, NY, USA, 11 pages. https://doi.org/10.1145/3534678.
                                        3539255                                                                                       (target task) via a transfer learning-based framework.
                                                                                                                                         (Challenges) The transfer learning for HPO consists of two
                                        1     INTRODUCTION                                                                            key operations: extracting source knowledge from previous HPO
                                                                                                                                      tasks, and aggregating and transfering these knowledge to a target
                                        Machine learning (ML) models have been extensively applied in
                                                                                                                                      domain. To fully unleash the potential of TL, we need to address
                                        many fields such as recommendation, computer vision, financial
                                                                                                                                      two main challenges when performing the above operations: 1)
                                        market analysis, etc [6, 14–18]. However, the performance of ML
                                                                                                                                      The Complementary Nature among Source Tasks. Different source
                                        models heavily depends on the choice of hyperparameter config-
                                                                                                                                      tasks are often complementary and thus require us to treat them
                                        urations (e.g., learning rate or the number of hidden layers in a
                                                                                                                                      in a joint and cooperative manner. Ignoring the synergy of mul-
                                        Permission to make digital or hard copies of all or part of this work for personal or         tiple source tasks might lead to the loss of auxiliary knowledge.
                                        classroom use is granted without fee provided that copies are not made or distributed
                                        for profit or commercial advantage and that copies bear this notice and the full citation
                                                                                                                                      2) Dynamics during Knowledge Aggregation. At the beginning of
                                        on the first page. Copyrights for components of this work owned by others than ACM            HPO, the knowledge from the source tasks could bring benefits
                                        must be honored. Abstracting with credit is permitted. To copy otherwise, or republish,       due to the scarcity of observations on the target task. However, as
                                        to post on servers or to redistribute to lists, requires prior specific permission and/or a
                                        fee. Request permissions from permissions@acm.org.                                            the tuning process proceeds, we should shift the focus to the tar-
                                        KDD ’22, August 14–18, 2022, Washington, DC, USA                                              get task. Since the target task gets more observations, transferring
                                        © 2022 Association for Computing Machinery.                                                   from source tasks might not be necessary anymore considering
                                        ACM ISBN 978-1-4503-9385-0/22/08. . . $15.00
                                        https://doi.org/10.1145/3534678.3539255
                                                                                                                                      the bias and noises in the source tasks (i.e., negative transfer [36]).

KDD ’22, August 14–18, 2022, Washington, DC, USA                                                                                         Li et al.

Existing methods [11, 46, 58] have been focusing on these two chal-      suffer from the low-efficiency issue due to insufficient configuralenges. However, none of them considers both simultaneously. This        tion evaluations within a limited budget. To speed up HPO of ML
motivates our work, which aims at developing a transfer learning         algorithms with limited trials, recent BO methods extend the tradiframework that could 1) extract source knowledge in a cooperative        tional black-box assumption by exploiting cheaper fidelities from
manner, and 2) transfer the auxiliary knowledge in an adaptive way.      the current task [9, 23, 26, 27, 30, 31, 41, 52]. Orthogonal to these
   In this paper, we propose TransBO, a novel two-phase transfer         methods, we focus on borrowing strength from previously finished
learning framework for automatic HPO that tries to address the           tasks to accelerate the HPO of the current task.
above two challenges simultaneously. TransBO works under the                Transfer learning (TL) methods for HPO aim to leverage auxilumbrella of Bayesian optimization and designs a transfer learning        iary knowledge from previous tasks to achieve faster optimization
(TL) surrogate to guide the HPO process. This framework decouples        on the target task. One common way is to learn surrogate modthe process of knowledge transfer into two phases and considers          els from past tuning history and use them to guide the search of
the knowledge extraction and knowledge aggregation separately in         hyperparameters. For instance, several methods learn all available
each phase (See Figure 1). In Phase one, TransBO builds a source sur-    information from both source and target tasks in a single surrorogate that extracts and combines useful knowledge across multiple       gate, and make the data comparable through a transfer stacking
source tasks. In Phase two, TransBO integrates the source surro-         ensemble [38], a ranking algorithm [1], multi-task GPs [51], a mixed
gate (in Phase one) and the target surrogate to construct the final      kernel GP [60], the GP noisy model [22], a multi-layer perceptron
surrogate, which we refer to as the transfer learning surrogate. To      with Bayesian linear regression heads [39, 49] or replace GP with
maximize the generalization of the transfer learning surrogate, we       Bayesian neural networks [50]. SGPR [13] and SMFO [57] utilize
adopt the cross-validation mechanism to learn the transfer learning      the knowledge from all source tasks equally and thus suffer from
surrogate in a principled manner. Moreover, instead of combining         performance deterioration when the knowledge of source tasks is
base surrogates with independent weights, TransBO can learn the          not applicable to the target task. FMLP [45] uses multi-layer peroptimal aggregation weights for base surrogates jointly. To this         ceptrons as the surrogate model that learns the interaction between
end, we propose to learn the weights in each phase by solving a          hyperparameters and datasets. SCoT [1] and MKL-GP [60] fit a
constrained optimization problem with a differentiable ranking loss      GP-based surrogate on merged observations from both source tasks
function.                                                                and target task. To distinguish the varied performance of the same
   The empirical results of static TL scenarios showcase the stability   configuration on different tasks, the two methods use the metaand effectiveness of TransBO compared with state-of-the-art TL           features of datasets to represent the tasks; while the meta-features
methods for HPO. In dynamic TL scenarios that are close to real-         are often unavailable for broad classes of HPO problems [11]. Due
world applications, TransBO obtains strong performance – the top-2       to the high computational complexity of GP (O (𝑛 3 )), it is difficult
results on 22.25 out of 30 tuning tasks (Practicality). In addition,     for these methods to scale to a large number of source tasks and
when applying TransBO to neural architecture search (NAS), it            trials (scalability bottleneck).
achieves more than 5× speedups than the state-of-the-art NAS                To improve scalability, recent methods adopt the ensemble frameapproaches (Universality).                                               work to conduct TL for HPO, where they train a base surrogate
   (Contributions ) In this work, our main contributions are sum-        on each source task and the target task respectively and then commarized as follows:                                                      bine all base surrogates into an ensemble surrogate with different
                                                                         weights. This framework ignores the two aforementioned issues
     • We present a novel two-phase transfer learning framework          and uses the independent weights. POGPE [46] sets the weights of
       for HPO — TransBO, which could address the aforemen-              base surrogates to constants. TST [58] linearly combines the base
       tioned challenges simultaneously.                                 surrogates with a Nadaraya-Watson kernel weighting by defining
     • We formulate the learning of this two-phase framework             a distance metric across tasks; the weights are calculated by using
       into constrained optimization problems. By solving these          either meta-features (TST-M) or pairwise hyperparameter config-
       problems, TransBO could extract and aggregate the source          uration rankings (TST-R). RGPE [11] uses the probability that the
       and target knowledge in a joint and adaptive manner.              base surrogate has the lowest ranking loss on the target task to
     • To facilitate transfer learning research for HPO, we create       estimate the weights. Instead of resorting to heuristics, TransBO
       and publish a large-scale benchmark, which takes more than        propose to learn the joint weights in a principled way.
       200K CPU hours and involves more than 1.8 million model              Warm-starting methods [25, 34] select several initial hyperpa-
       evaluations.                                                      rameter configurations as the start points of search procedures.
     • The extensive experiments, including static and dynamic           Salinas et al. [44] deal with the heterogeneous scale between tasks
       TL settings and neural architecture search, demonstrate the       with the Gaussian Copula Process. ABRAC [19] proposes a multi-
       superiority of TransBO over state-of-the-art methods.             task BO method with adaptive complexity to prevent over-fitting
                                                                         on scarce target observations. TNP [55] applies the neural process
                                                                         to jointly transfer surrogates, parameters, and initial configurations.
2    RELATED WORK                                                        Recently, transferring search space has become another way for
Bayesian optimization (BO) has been successfully applied to hy-          applying transfer learning in HPO. Wistuba et al. [56] prune the bad
perparameter optimization (HPO) [5, 29, 30, 32]. For ML models           regions of search space according to the results from previous tasks.
that are computationally expensive to train (e.g., deep learning         This method suffers from the complexity of obtaining meta-features
models or models trained on large datasets), BO methods [4, 20, 48]      and relies on some other parameters to construct a GP model. On

TransBO: Hyperparameter Optimization via Two-Phase Transfer Learning                                                       KDD ’22, August 14–18, 2022, Washington, DC, USA

                   HPO History in Source Tasks                      Target Observations         4     THE PROPOSED METHOD
     D#               D$             D𝟑              D&
                                                                                                In this section, we present TransBO, a two-phase transfer learning
                                                                           D'
                                                                                                (TL) framework for HPO. Before diving into the proposed frame-
         train                                                                                  work, we first introduce the notations and settings for TL. Then we
                                                                                                describe TransBO in details and end the section with discussions
     M#               M$             M(              M&                                         about its advantages.
    Source base Surrogates                                                                      Basic Notations and Settings. As illustrated in Figure 1, we
      Source Surrogate       𝐌𝐒          1 Source Knowledge                M'                   denote observations from 𝐾 +1 tasks as 𝐷 1 , ..., 𝐷 𝐾 for 𝐾 source tasks
                                          Learning in Phase 1                                   and 𝐷𝑇 for the target task. The 𝑖-th source task has 𝑛𝑖 configuration
                                                                       Target Surrogate
                                                                                                observations: 𝐷 𝑖 = {(𝒙 𝑖𝑗 , 𝑦𝑖𝑗 )}𝑛𝑗=1
                                                                                                                                     𝑖
                                                                                                                                         with 𝑖 = 1, 2, ..., 𝐾, which are
                                         2 Source-Target Aggregation in Phase 2
                                                                                                obtained from previous tuning procedures. For the target task, after
       TL Surrogate          𝐌 𝐓𝐋        3 choose & evaluate a new config for next iteration    completing 𝑡 iterations (trials), the observations in the target task
                                                                                                are: 𝐷𝑇 = {(𝒙𝑇𝑗 , 𝑦𝑇𝑗 )}𝑡𝑗=1 .
     Figure 1: Two-Phase Transfer Learning Framework.                                               Before optimization, we train a base surrogate model for the
                                                                                                𝑖-th source task, denoted by 𝑀 𝑖 . Each base surrogate 𝑀 𝑖 can be
                                                                                                fitted on 𝐷 𝑖 in advance (offline), and the target surrogate 𝑀𝑇 is
                                                                                                trained on 𝐷𝑇 on the fly. Since the configuration performance
that basis, Perrone et al. [40] propose to utilize previous tasks to                            𝑦s in each 𝐷 𝑖 and 𝐷𝑇 may have different numerical ranges, we
design a sub-region of the entire search space for the new task.                                standardize the 𝑦s in each task by removing the mean and scaling
While sharing some common spirits, these methods are orthogonal                                 to unit variance. For a hyperparameter configuration 𝒙 𝑗 , each base
and complementary to our surrogate transfer method introduced
                                                                                                surrogate 𝑀 𝑖 outputs a posterior predictive distribution at 𝒙 𝑗 , that’s,
in this paper.                                                                                                                2 (𝒙 )). For brevity, we denote the mean
                                                                                                𝑀 𝑖 (𝒙 𝑗 ) ∼ N (𝜇𝑀 𝑖 (𝒙 𝑗 ), 𝜎𝑀   𝑗
   In addition, our proposed two-phase framework inherits the                                                                   𝑖

advantages of the bi-level optimization [2]. While previous meth-                               of this prediction at 𝒙 𝑗 as 𝑀 𝑖 (𝒙 𝑗 ) = 𝜇𝑀 𝑖 (𝒙 𝑗 ).
ods in the literature focus on different tasks (e.g., evolutionary
computation [47]), to the best of our knowledge, TransBO is the                                 4.1    Overview
first method that adopts the concept of bi-level optimization into                              TransBO aims to build a transfer learning surrogate model 𝑀𝑇 𝐿 on
hyperparameter transfer learning.                                                               the target task, which outputs a more accurate prediction for each
                                                                                                configuration by borrowing strength from the source tasks. The
3     BAYESIAN HYPERPARAMETER                                                                   cornerstone of TransBO is to decouple the combination of 𝐾 + 1
      OPTIMIZATION                                                                              base surrogates with a novel two-phase framework:
                                                                                                   Phase 1. To leverage the complementary nature among source
The HPO of ML algorithms can be modeled as a black-box optimiza-
                                                                                                tasks, TransBO first linearly combines all source base surrogates
tion problem. The goal is to find 𝑎𝑟𝑔𝑚𝑖𝑛𝒙 ∈X 𝑓 (𝒙) in hyperparam-
                                                                                                into a single source surrogate with the weights w:
eter space X, where 𝑓 (𝒙) is the ML model’s performance metric
(e.g., validation error) corresponding to the configuration 𝒙. Due
to the intrinsic randomness of most ML algorithms, we evaluate                                                       𝑀 𝑆 = agg({𝑀 1, ..., 𝑀 𝐾 }; w).
configuration 𝒙 and can only get its noisy result 𝑦 = 𝑓 (𝒙) + 𝜖 with
𝜖 ∼ N (0, 𝜎 2 ).                                                                                In this phase, the useful source knowledge from each source task is
   Bayesian optimization (BO) is a model-based framework for                                    extracted and integrated into the source surrogate in a joint and
HPO. BO first fits a probabilistic surrogate model 𝑀 : 𝑝 (𝑓 |𝐷) on                              cooperative manner.
the already observed instances 𝐷 = {(𝒙 1, 𝑦1 ), ..., (𝒙𝑛−1, 𝑦𝑛−1 )}. In                            Phase 2. To support dynamics-aware knowledge aggregation,
the 𝑛-th iteration, BO iterates the following steps: 1) use surrogate                           TransBO further combines the aggregated source surrogate with
𝑀 to select a promising configuration 𝒙𝑛 that maximizes the acqui-                              the target surrogate 𝑀𝑇 via weights p in an adaptive manner, where
sition function 𝒙𝑛 = arg max𝒙 ∈X 𝑎(𝒙; 𝑀), where the acquisition                                 𝑀𝑇 is trained on the target observations 𝐷𝑇 :
function is to balance the exploration and exploitation trade-off;
2) evaluate this point to get its performance 𝑦𝑛 , and add the new
observation (𝒙𝑛 , 𝑦𝑛 ) to 𝐷; 3) refit 𝑀 on the augmented 𝐷. Expected                                                  𝑀𝑇 𝐿 = agg({𝑀 𝑆 , 𝑀𝑇 }; p).
Improvement (EI) [21] is a common acquisition function defined as
follows:                                                                                           Such joint and adaptive knowledge transfer in two phases guar-
                                  ∫ ∞                                                           antees the efficiency and effectiveness of the final TL surrogate
                                                                                                𝑀𝑇 𝐿 in extracting and integrating the source and target knowl-
                 𝑎(𝒙; 𝑀) =               max(𝑦 ∗ − 𝑦, 0)𝑝 𝑀 (𝑦|𝒙)𝑑𝑦,                      (1)
                                    −∞                                                          edge. To maximize the generalization ability of 𝑀𝑇 𝐿 , the two-phase
                                                                                                framework further learns the parameters w and p in a principled
where 𝑀 is the surrogate and 𝑦 ∗ = min{𝑦1, ..., 𝑦𝑛 }. By maximizing                             and automatic manner by solving the constrained optimization
this EI function 𝑎(𝒙; 𝑀) over X, BO methods can find a configura-                               problems. In the following, we describe the parameter learning and
tion to evaluate for each iteration.                                                            aggregation method.

KDD ’22, August 14–18, 2022, Washington, DC, USA                                                                                                                   Li et al.

4.2    Parameter Learning in Two-Phase                                                of the objective L as follows:
       Framework                                                                                    𝜕L     ∑︁      𝑛𝑒𝑔_𝑒𝑧
                                                                                                       =                    ∗ (𝐴 [ 𝑗 ] − 𝐴 [𝑘 ] ),
Notice that w and p play different roles — w combines 𝐾 source                                     𝜕w            1 + 𝑛𝑒𝑔_𝑒𝑧
                                                                                                           ( 𝑗,𝑘) ∈P                                                   (5)
base surrogates to best fit the target observations, while p balances
between two surrogates 𝑀 𝑆 and 𝑀𝑇 . The objective of TransBO is                                    𝑛𝑒𝑔_𝑒𝑧 = 𝑒 (𝐴 [ 𝑗 ] w−𝐴 [𝑘 ] w) ,
to maximize the generalization performance of 𝑀𝑇 𝐿 . To obtain w,                     where P consists of pairs ( 𝑗, 𝑘) satisfying 𝑦 𝑗 < 𝑦𝑘 , 𝐴 is the matrix
we use the target observations 𝐷𝑇 to maximize the performance of                      formed by putting the predictions of 𝑀 1:𝐾 s together where the
source surrogate 𝑀 𝑆 . However, if we learn the parameter p of 𝑀𝑇 𝐿                   element at the 𝑖-th row and 𝑗-th column is 𝑀 𝑖 (𝒙 𝑗 ), and 𝐴 [ 𝑗 ] is the
on 𝐷𝑇 by using the 𝑀 𝑆 and 𝑀𝑇 , where 𝑀 𝑆 and 𝑀𝑇 are trained                          row vector in the 𝑗-th row of matrix 𝐴. Furthermore, this optimizaon 𝐷𝑇 directly, the learning process becomes an estimation of in-                     tion problem can be solved efficiently by applying many existing
sample error and can not reflect the generalization of the final                      sequential quadratic programming (SQP) solvers [28].
surrogate 𝑀𝑇 𝐿 . To address this issue, we adopt the cross-validation                 Learning Parameter w. As stated previously, to maximize the
mechanism to maximize the generalization ability of 𝑀𝑇 𝐿 when                         (generalization) performance of 𝑀 𝑆 , we propose to learn the palearning p. In the following, we first describe the general procedure                 rameter w by fitting 𝑀 𝑆 on the whole observations 𝐷𝑇 . In this
to learn a surrogate 𝑀 𝑆 on given observations 𝐷 (instead of 𝐷𝑇 ),                    way, the useful source knowledge from multiple source tasks can
and then introduce the method to learn the parameters w and p,                        be fully extracted and integrated in a joint manner. Therefore, the
respectively.                                                                         parameters w can be obtained by calling the general procedure, i.e.,
General Procedure: Fitting 𝑀 𝑆 on Given Observations 𝐷. Our                           solving the problem 4, where the available observations 𝐷 are set
strategy is to obtain the source surrogate 𝑀 𝑆 as a weighted combi-                   to 𝐷𝑇 .
nation of the predictions of source base surrogates {𝑀 1, ..., 𝑀 𝐾 }:                 Learning Parameter p. To reflect the generalization in 𝑀𝑇 𝐿 , the
                                        𝐾
                                       ∑︁                                             parameter p is learned with the cross-validation mechanism. We
                         𝑀 𝑆 (𝒙) =            𝑤𝑖 𝑀 𝑖 (𝒙),                       (2)   first split 𝐷𝑇 into 𝑁𝑐𝑣 partitions: 𝐷𝑇1 , ..., 𝐷𝑇𝑁 with 𝑁𝑐𝑣 = 5. For
                                                                                                                                                𝑐𝑣
                                        𝑖=1                                                                                                            𝑆
                                                                                      each partition 𝑖 ∈ [1 : 𝑁𝑐𝑣 ], we first fit a partial surrogate 𝑀−𝑖
        Í                                                                                                    𝑇
                                                                                      on the observations 𝐷 −𝑖 with observations in the 𝑖-th partition
where 𝑖 𝑤𝑖 = 1 and 𝑤𝑖 ∈ [0, 1]. Intuitively, the weight 𝑤𝑖 reflects
the quality of knowledge extracted from the corresponding source                      removed from 𝐷𝑇 , and the surrogate 𝑀−𝑖  𝑆 is learned on 𝐷𝑇 using
                                                                                                                                                  −𝑖
tasks. Instead of calculating weights independently, which may                        the general procedure; in addition, we also fit a partial surrogate
ignore the complementary nature among source tasks, we propose                                𝑇 on 𝐷𝑇 directly. Then we combine the surrogates 𝑀 𝑆
                                                                                      model 𝑀−𝑖        −𝑖                                              −𝑖
to combine source base surrogates 𝑀 𝑖 s in a joint and supervised                           𝑇                        𝑇𝐿:
                                                                                      and 𝑀−𝑖 linearly to obtain a 𝑀−𝑖
manner, which reveals their cooperative contributions to 𝑀 𝑆 .                                                  𝑇𝐿
   To derive 𝑀 𝑆 in a principled way, we use a differentiable pairwise                                         𝑀−𝑖 = 𝑝 𝑆 𝑀−𝑖
                                                                                                                          𝑆
                                                                                                                             + 𝑝𝑇 𝑀−𝑖
                                                                                                                                   𝑇
                                                                                                                                      ,                                (6)
ranking loss function to measure the fitting error between the                        where p = [𝑝 𝑆 , 𝑝𝑇 ]. Therefore, we can obtain 𝑁𝑐𝑣 partial surrogates
prediction of 𝑀 𝑆 and the available observations 𝐷. In HPO, ranking                     𝑆 and 𝑀𝑇 with 𝑖 ∈ [1 : 𝑁 ]. Based on the differentiable pairwise
                                                                                      𝑀−𝑖       −𝑖                  𝑐𝑣
loss is more appropriate than mean square error — the actual values                                                                 𝑇 𝐿 on 𝐷𝑇 is defined as:
                                                                                      ranking loss function in Eq. 3, the loss of 𝑀−𝑖
of predictions are not the most important, and we care more about
                                                                                                                               𝑛            𝑛
the partial orders over the hyperparameter space, e.g., the location                                    𝑇𝐿 𝑇      1 ∑︁                     ∑︁
of the optimal configuration. This ranking loss function is defined                            L𝑐𝑣 (p, 𝑀−𝑖 ;𝐷 ) = 2                                       𝜙 (𝑧),
                                                                                                                 𝑛 𝑗=1
as follows:                                                                                                                        𝑘=1,𝑦𝑇𝑗 <𝑦𝑇𝑘 ,𝑘 ∈𝐷𝑖𝑇                (7)

                      1 ∑︁
                                𝑛       𝑛
                                       ∑︁                                                      𝜙 (𝑧) = 𝑙𝑜𝑔(1 + 𝑒 −𝑧 ), 𝑧 = 𝑀−𝑖
                                                                                                                            𝑇𝐿          𝑇𝐿
                                                                                                                               (𝒙𝑘 ) − 𝑀−𝐻 ( 𝑗) (𝒙 𝑗 )
      L(w, 𝑀 𝑆 ; 𝐷) = 2                           𝜙 (𝑀 𝑆 (𝒙𝑘 ) − 𝑀 𝑆 (𝒙 𝑗 )),
                     𝑛 𝑗=1
                                    𝑘=1,𝑦 𝑗 <𝑦𝑘                                 (3)   where 𝑛 is the number of observations in 𝐷𝑇 , 𝑦𝑇 is the observed
                                                                                      performance of configuration 𝒙𝑇 in 𝐷𝑇 , 𝐻 ( 𝑗) indicates the par-
      𝜙 (𝑧) = 𝑙𝑜𝑔(1 + 𝑒 −𝑧 ),
                                                                                      tition id that configuration 𝒙 𝑗 belongs to, and the prediction of
where 𝑛 is the number of observations in 𝐷, 𝑦 is the observed                         𝑀−𝑖𝑇 𝐿 at configuration 𝒙 is obtained by linearly combining the
                                                                                                                 𝒌
performance of configuration 𝒙 in 𝐷, and the prediction of 𝑀 𝑆 (𝒙 𝑗 )                 predictive mean of 𝑀−𝑖 𝑆 and 𝑀𝑇 with weight p, that’s, 𝑀𝑇 𝐿 (𝒙 ) =
                                                                                                                    −𝑖                        −𝑖    𝑘
at configuration 𝒙 𝑗 is obtained by linearly combining the predictive                 𝑝 𝑆 𝑀−𝑖
                                                                                            𝑆 (𝒙 ) + 𝑝𝑇 𝑀𝑇 (𝒙 ). So the parameter p can be learned by
                                                   Í                                             𝑘        −𝑖   𝑘
mean of 𝑀 𝑖 with a weight 𝑤𝑖 , that’s, 𝑀 𝑆 (𝒙 𝑗 ) = 𝑖 𝑤𝑖 𝑀 𝑖 (𝒙 𝑗 ).                  solving a similar constrained optimization problem on 𝐷𝑇 :
   We further turn the learning of source surrogate 𝑀 𝑆 , i.e., the
                                                                                                                         𝑁𝑐𝑣
learning of w, into the following constrained optimization problem:                                                      ∑︁
                                                                                                                                        𝑇𝐿 𝑇
                                                                                                        minimize               L𝑐𝑣 (p, 𝑀−𝑖 ;𝐷 )
                                                                                                               p                                                       (8)
                     minimize          L(w, 𝑀 𝑆 ; 𝐷)                                                                     𝑖=1
                            w                                                   (4)                                       ⊤
                                                                                                        s.t.            1 p = 1, p ≥ 0.
                     s.t.              1⊤ w = 1, w ≥ 0,
                                                                                      Following the solution introduced in problem 4, the above optimizawhere the objective is the ranking loss of 𝑀 𝑆 on 𝐷. This optimiza-                   tion problem can be solved efficiently.
tion objective is continuously differentiable, and concretely, it is                  Final TL Surrogate. After w and p are obtained, as illustrated in
twice continuously differentiable. So we can have the first derivative                Figure 1, we first combine the source base surrogates into the source

TransBO: Hyperparameter Optimization via Two-Phase Transfer Learning                                 KDD ’22, August 14–18, 2022, Washington, DC, USA

Algorithm 1 The TransBO Framework.                                         meta-features to represent the dataset [11, 45, 58]. The construction
Input: maximum number of trials 𝑁 𝑇 , observations from 𝐾 source tasks:    of TL surrogate in TransBO is insensitive to its hyperparameters
       𝐷 1:𝐾 , and config. space X.                                        and does not require meta-features. 2) Universality. The 1st prop-
 1: for 𝑖 ∈ {1, 2, ..., 𝑁 𝑇 } do                                           erty enable TransBO to be a general transfer learning framework
 2:    Calculate the weight w𝑖 in 𝑀 𝑆 by solving (4).                      for Black-box optimizations, e.g., experimental design [12], neural
 3:    Calculate the weight p𝑖 in 𝑀𝑇 𝐿 by solving (8).                     architecture search [8], etc; we include an experiment to evaluate
 4:    Employ non-decreasing prior on 𝑝𝑇 : 𝑝𝑇𝑖 = max(𝑝𝑇𝑖 , 𝑝𝑇𝑖−1 ).        TransBO on the NAS task in the section of experiment). 3) Scalabil-
 5:    Build 𝑀 𝑆 , 𝑀𝑇 𝐿 with weights w𝑖 and p𝑖 , respectively.             ity. Compared with the methods that combine 𝑘 source tasks with 𝑛
 6:    Sample a large number of configurations randomly from X, com-       trials into a single surrogate (𝑂 (𝑘 3𝑛 3 )), TransBO has a much lower
       pute their acquisition values according to the EI criterion in      complexity 𝑂 (𝑘𝑛 3 ), which means that TransBO could scale to a
       Eq.1, where 𝑀 = 𝑀𝑇 𝐿 , and choose the configuration 𝒙𝑖 =
                                                                           large number of tasks and trials easily. 4) Theoretical Discussion.
       argmax𝑥 ∈X 𝑒𝑖 (𝑥, 𝑀𝑇 𝐿 ).
 7:    Evaluate 𝒙𝑖 and get its performance 𝑦𝑖 , augment observations 𝐷𝑇
                                                                           TransBO also provides theoretical discussions about preventing
       with (𝒙 𝒊 , 𝑦𝑖 ) and refit 𝑀𝑇 on the augmented 𝐷𝑇 .                 the performance deterioration (negative transfer). Base on cross-
 8: end for                                                                validation and the non-decreasing constraint, the performance of
 9: return the best configuration in 𝐷𝑇 .                                  TransBO, given sufficient trials, will be no worse than the method
                                                                           without transfer learning, while the other methods cannot have this
                                                                           (See Appendix A.4 for more details).
surrogate 𝑀 𝑆 with w (the Phase 1), and then integrate 𝑀 𝑆 and 𝑀𝑇
with p to obtain the final TL surrogate 𝑀𝑇 𝐿 (the Phase 2). To ensure      5     EXPERIMENTS AND RESULTS
the surrogate 𝑀𝑇 𝐿 still works in the BO framework, it is required         In this section, we evaluate TransBO from three perspectives: 1)
to be a GP. How to obtain the unified posterior predictive mean            stability and effectiveness on static TL tasks, 2) practicality on
and variance from multiple GPs (base surrogates) is still an open          real-world dynamic TL tasks, and 3) universality when conducting
problem. As suggested by [11], the linear combination of multiple          neural architecture search.
base surrogates works well in practice. Therefore, we aggregate
the base surrogates with linear combination. That’s, suppose there         5.1    Experimental Setup
are 𝑁𝐵 GP-based surrogates, and each base surrogate 𝑀 𝑏 has a              Baselines. We compare TransBO with eight baselines – two nonweight 𝑤𝑏 with 𝑏 = 1, ..., 𝑁𝐵 , the combined prediction under the          transfer methods: (1) Random search [3], (2) I-GP: independent
                                                      Í
linear combination technique is give by: 𝜇𝐶 (𝒙) = 𝑏 𝑤𝑏 𝜇𝑏 (𝒙) and          Gaussian process-based surrogate fitted on the target task without
𝜎𝐶2 (𝒙) = 𝑏 𝑤𝑏2 𝜎𝑏2 (𝒙).
           Í
                                                                           using any source data, (3) SCoT [1]: it models the relationship be-
Algorithm Summary At initialization, we set the weight of each             tween datasets and hyperparamter performance by training a single
source surrogate in w to 1/𝐾, and p = [1, 0] when the number of            surrogate on the scaled and merged observations from both source
trials is insufficient for cross-validation. Algorithm 1 illustrates the   tasks and the target task, (4) SGPR: the core TL algorithm used
pseudo code of TransBO. In the 𝑖-th iteration, we first learn the          in the well-known service — Google Vizier [13], and four ensemweights p𝑖 and w𝑖 by solving two optimization problems (Lines              ble based TL methods: (5) POGPE [46], (6) TST [58], (7) TST-M: a
2-3). Since we have the prior: as the HPO process of the target task       variant of TST using dataset meta-features [58], and (8) RGPE [11].
proceeds, the target surrogate owns more and more knowledge                Benchmark on 30 OpenML Datasets. To evaluate the perforabout the objective function of the target task, therefore the weight      mance of TransBO, we create and publish a large-scale benchmark.
of 𝑀𝑇 should increase gradually. To this end, we employ a max              Four ML algorithms, including Random Forest, Extra Trees, Adoperator, which enforces that the update of 𝑝𝑇 should be non-              aboost and LightGBM [24], are tuned on 30 real-world datasets
decreasing (Line 4). Next, by using linear combination, we build           (tasks) from OpenML repository [53]. The design of hyperparamthe source surrogate 𝑀 𝑆 with weight w𝑖 , and then construct the           eter space and meta-feature for each dataset is adopted from the
final TL surrogate 𝑀𝑇 𝐿 with p𝑖 (Line 5). Finally, TransBO utilizes        implementation in Auto-Sklearn [10]. For each ML algorithm on
𝑀𝑇 𝐿 to choose a promising configuration to evaluate, and refit the        each dataset, we sample 20k configurations from the hyperparametarget surrogate on the augmented observation (the BO framework,           ter space randomly and store the corresponding evaluation results.
Lines 6-7).                                                                It takes more than 200k CPU hours to collect these evaluation re-
                                                                           sults. Note that, for reproducibility, we provide more details about
4.3     Discussion: Advantages of TransBO                                  this benchmark, including the datasets, the hyperparameter space
To our knowledge, TransBO is the first method that conducts trans-         of ML algorithms, etc., in Appendix A.1.
fer learning for HPO in a supervised manner, instead of resorting          AutoML HPO Tasks. To evaluate the performance of each method,
to some heuristics. In addition, this method owns the following            the experiments are performed in a leave-one-out fashion. Each
desirable properties simultaneously. 1) Practicality. A practical          method optimizes the hyperparameters of a specific task over 20k
HPO method should be insensitive to its hyperparameters, and do            configurations while treating the remaining tasks as the source
not depend on meta-features. The goal of HPO is to optimize the ML         tasks. In each source task, only 𝑁𝑆 instances (here 𝑁𝑆 = 50) are
hyperparameters automatically while having extra (or sensitive)            used to extract knowledge from this task in order to test the effihyperparameters itself actually violates its principle. In addition,       ciency of TL [11, 58].
many datasets, including image and text data, lack appropriate                 We include the following three kinds of tasks:

KDD ’22, August 14–18, 2022, Washington, DC, USA                                                                                                                                                                                                                                                  Li et al.

                                        TransBO       SCoT          TST-M                                      TransBO       SCoT          TST-M                                      TransBO       SCoT          TST-M                                          TransBO       SCoT          TST-M
                                                                                                                                                                     6.5                                                                          7
                         7              RS            SGPR          POGPE                     6.0              RS            SGPR          POGPE                                      RS            SGPR          POGPE                                          RS            SGPR          POGPE
                                        I-GP          TST           RGPE                                       I-GP          TST           RGPE                      6.0              I-GP          TST           RGPE                                           I-GP          TST           RGPE

          Average Rank                                                         Average Rank                                                           Average Rank                                                                 Average Rank
                         6                                                                    5.5                                                                                                                                                 6
                                                                                                                                                                     5.5

                                                                                              5.0
                                                                                                                                                                     5.0
                         5                                                                                                                                                                                                                        5

                                                                                              4.5                                                                    4.5
                         4                                                                                                                                                                                                                        4
                                                                                              4.0                                                                    4.0

                         3                                                                                                                                           3.5
                                                                                              3.5                                                                                                                                                 3
                             0   5 10 15 20 25 30 35 40 45 50 55 60 65 70 75                        0   5 10 15 20 25 30 35 40 45 50 55 60 65 70 75                        0   5 10 15 20 25 30 35 40 45 50 55 60 65 70 75                            0   5 10 15 20 25 30 35 40 45 50 55 60 65 70 75
                                          Number of Trials                                                       Number of Trials                                                       Number of Trials                                                           Number of Trials

                                    (a) Random Forest                                                         (b) LightGBM                                                            (c) Adaboost                                                              (d) Extra Trees

                                                                  Figure 2: Static TL results for four algorithms with 𝑁𝑡𝑎𝑠𝑘 = 29 source tasks.

                                        TransBO       SCoT          TST-M                                      TransBO       SCoT          TST-M                     7.0              TransBO       SCoT          TST-M                                          TransBO       SCoT          TST-M
                   7.0                                                                                                                                                                                                                      7.0
                                        RS            SGPR          POGPE                     6.0              RS            SGPR          POGPE                                      RS            SGPR          POGPE                                          RS            SGPR          POGPE
                                                                                                                                                                     6.5
                   6.5                  I-GP          TST           RGPE                                       I-GP          TST           RGPE                                       I-GP          TST           RGPE                      6.5                  I-GP          TST           RGPE

    Average Rank                                                               Average Rank                                                           Average Rank                                                           Average Rank
                   6.0                                                                        5.5                                                                    6.0                                                                    6.0

                   5.5                                                                                                                                               5.5                                                                    5.5
                                                                                              5.0
                   5.0                                                                                                                                                                                                                      5.0
                                                                                                                                                                     5.0
                                                                                              4.5                                                                                                                                           4.5
                   4.5
                                                                                                                                                                     4.5
                                                                                                                                                                                                                                            4.0
                   4.0
                                                                                              4.0                                                                    4.0                                                                    3.5
                   3.5
                                                                                                                                                                     3.5                                                                    3.0
                             0   5 10 15 20 25 30 35 40 45 50 55 60 65 70 75                        0   5 10 15 20 25 30 35 40 45 50 55 60 65 70 75                        0   5 10 15 20 25 30 35 40 45 50 55 60 65 70 75                            0   5 10 15 20 25 30 35 40 45 50 55 60 65 70 75
                                          Number of Trials                                                       Number of Trials                                                       Number of Trials                                                           Number of Trials

                                    (a) Random Forest                                                         (b) LightGBM                                                            (c) Adaboost                                                              (d) Extra Trees

                                                                  Figure 3: Static TL results for four algorithms with 𝑁𝑡𝑎𝑠𝑘 = 5 source tasks

   (a) Static TL Setting. This experiment is performed in a leave-                                                                                                            𝑖
                                                                                                                                                                     where 𝑦𝑚𝑖𝑛         𝑖
                                                                                                                                                                                  and 𝑦𝑚𝑎𝑥   are the best and worst performance value on
one-out fashion, i.e., we optimize the hyperparameters of the target                                                                                                 the 𝑖-th task, 𝐾 is the number of tasks, i.e., 𝐾 = 30, 𝑦𝒙𝑖 corresponds
task while treating the remaining tasks as the source tasks.                                                                                                         to the performance of configuration 𝒙 in the 𝑖-th task, and X𝑡 is
   (b) Dynamic TL Setting. It simulates the real-world HPO sce-                                                                                                      the set of hyperparameter configurations that have been evaluated
narios, in which 30 tasks (datasets) arrive sequentially; when the                                                                                                   in the previous 𝑡 trials. The relative distances over all considered
𝑖-th task appears, the former 𝑖 − 1 tasks are treated as the source                                                                                                  tasks are averaged to obtain the final ADTM value.
tasks.                                                                                                                                                               Implementations & Parameters. TransBO implements the Gauss-
   (c) Neural Architecture Search (NAS). It transfers tuning knowl-                                                                                                  ian process using SMAC31 [20, 35], which can support a complex
edge from conducting NAS on CIFAR-10 and CIFAR-100 to acceler-                                                                                                       hyperparameter space, including numerical, categorical, and conate NAS on ImageNet16-120 based on NAS-Bench201 [7].                                                                                                                 ditional hyperparameters, and the kernel hyperparameters in GP
   In addition, following [11], all the compared methods are ini-                                                                                                    are inferred by maximizing the marginal likelihood. The two optitialized with three randomly selected configurations, after which                                                                                                    mization problems in TransBO are solved by using SQP methods
they proceed sequentially with a total of 𝑁𝑇 evaluations (trials). To                                                                                                provided in SciPy 2 [54]. In the BO module, the popular EI acquisiavoid the effect of randomness, each method is repeated 30 times,                                                                                                    tion function is used. As for the parameters in each baseline, the
and the averaged performance metrics are reported.                                                                                                                   bandwidth 𝜌 in TST [58] is set to 0.3 for all experiments; in RGPE,
Evaluation Metric. Comparing each method in terms of classifica-                                                                                                     we sample 100 times (𝑆 = 100) to calculate the weight for each
tion error is questionable because the classification error is not com-                                                                                              base surrogate; in SGPR [13], the parameter 𝛼, which determines
mensurable across datasets. Following the previous works [1, 11, 58],                                                                                                the relative importance of standard deviations of past tasks and
we adopt the metrics as follows:                                                                                                                                     the current task, is set to 0.95 (Check Appendix B for reproduction
   Average Rank. For each target task, we rank all compared meth-                                                                                                    details).
ods based on the performance of the best configuration they have
found so far. Furthermore, ties are being solved by giving the aver-                                                                                                  5.2           Comprehensive Experiments in Two TL
age rank. For example, if one method observes the lowest validation                                                                                                                 Settings
error of 0.2, another two methods find 0.3, and the last method finds
only 0.45, we would rank the methods with 1, 2+3        2+3                                                                                                          Static TL Setting. To demonstrate the efficiency and effectiveness
                                                    2 , 2 , 4.
   Average Distance to Minimum. The average distance to the global                                                                                                   of transfer learning in the static scenario, we compare TransBO with
minimum after 𝑡 trials is defined as:                                                                                                                                the baselines on four benchmarks (i.e., Random Forest, LightGBM,
                                                                                                                                                                     Adaboost, and Extra Trees). Concretely, each task is selected as the
                                                              1     ∑︁ 𝑚𝑖𝑛𝒙∈X 𝑦𝒙𝑖 − 𝑦𝑖
                                                                             𝑡       𝑚𝑖𝑛                                                                              1 https://github.com/automl/SMAC3
                                     𝐴𝐷𝑇 𝑀 ( X𝑡 ) =                                                                      ,                  (9)
                                                              𝐾
                                                                  𝑖∈ [1:𝐾 ]
                                                                                      𝑖
                                                                                     𝑦𝑚𝑎𝑥 − 𝑦𝑚𝑖𝑛
                                                                                             𝑖                                                                        2 https://docs.scipy.org/doc/scipy/reference/optimize.minimize-slsqp.html

TransBO: Hyperparameter Optimization via Two-Phase Transfer Learning                                                                                                                                                                                                                    KDD ’22, August 14–18, 2022, Washington, DC, USA

                   3.0                                                                                                                                                                                          3.2
                                                            TransBO                  POGPE                 0.19                                       TransBO                       POGPE                                                                         TransBO              POGPE                0.150                           TransBO             POGPE
                                                            TST                      RGPE                                                             TST                           RGPE                                                                          TST                  RGPE                                                 TST                 RGPE
                                                                                                                                                                                                                3.0                                                                                         0.148
                   2.8
                                                                                                           0.18
                                                                                                                                                                                                                                                                                                            0.146

    Average Rank                                                                                                                                                                                 Average Rank
                                                                                                                                                                                                                2.8

                                                                                                    ADTM                                                                                                                                                                                             ADTM
                   2.6                                                                                                                                                                                                                                                                                      0.144
                                                                                                           0.17
                                                                                                                                                                                                                2.6
                                                                                                                                                                                                                                                                                                            0.142
                   2.4                                                                                     0.16                                                                                                 2.4                                                                                         0.140

                                                                                                                                                                                                                                                                                                            0.138
                                                                                                           0.15                                                                                                 2.2
                   2.2
                                                                                                                                                                                                                                                                                                            0.136
                                                                                                                                                                                                                2.0
                                                                                                           0.14                                                                                                                                                                                             0.134
                         0   5   10   15   20    25               30       35   40    45    50                    0    5   10   15   20            25           30       35    40    45    50                         0   5   10   15   20      25                  30       35   40    45     50                   0    5   10   15   20   25   30   35   40    45   50
                                      Number of Trials                                                                          Number of Trials                                                                                   Number of Trials                                                                               Number of Trials

                         (a) Adaboost (Average Rank)                                                                  (b) Adaboost (ADTM)                                                                             (c) LightGBM (Average Rank)                                                                       (d) LightGBM (ADTM)

                                                                                                                  Figure 4: Results on source knowledge learning.

                                                                1.0                                                                                       1.0
                                                                                TransBO          TST                                                                          TransBO           TST                                                                                     TransBO             TST              RGPE
                                                                                RGPE             POGPE                                                                        RGPE              POGPE                                                                                   POGPE               SCoT
                                                                0.8                                                                                       0.8                                                                                                     103

                                                                                                                                                                                                                                             Cumulative Runtime
                                                Target Weight                                                                             Target Weight
                                                                0.6                                                                                       0.6
                                                                                                                                                                                                                                                                  102
                                                                0.4                                                                                       0.4

                                                                0.2                                                                                       0.2                                                                                                     101

                                                                0.0                                                                                       0.0
                                                                       0    5 10 15 20 25 30 35 40 45 50 55 60 65 70                                                 0   5 10 15 20 25 30 35 40 45 50 55 60 65 70                                                        0    5 10 15 20 25 30 35 40 45 50 55 60 65 70 75
                                                                                           Number of Trials                                                                               Number of Trials                                                                                   Number of Trials

                                                                      (a) Random Forest (cpu_small)                                                              (b) Adaboost (hypothyroid(2))                                                                      (c) Scalability on Random Forest

                                                                                                                  Figure 5: Target weight and scalability analysis.

target task in turn, and the remaining tasks are the source tasks;                                                                                                                                              Table 1: Dynamic TL results for Tuning four ML algorithms.
then we can measure the performance of each baseline based on
the results when tuning the hyperparameters of the target task.                                                                                                                                                                          Adaboost                                      Random Forest                         Extra Trees               LightGBM
                                                                                                                                                                                                                      Method
Furthermore, we use 29 and 5 source tasks respectively to evaluate                                                                                                                                                                       1st 2nd                                       1st    2nd                            1st    2nd                1st 2nd
the ability of each method when given a different amount of source                                                                                                                                                    POGPE               0    2                                        0      1                              0       2                 1    2
knowledge in terms of the number of source tasks 𝑁𝑡𝑎𝑠𝑘 . Note that,                                                                                                                                                   TST                 8   12                                        9      9                              7      12                10    9
for each target task, the maximum number of trials is 75. Figure 2                                                                                                                                                    RGPE                8    5                                        6      14                            10       9                 9    10
and Figure 3 show the experiment results on four benchmarks with                                                                                                                                                      TransBO            14   11                                       15      6                             14       7                12    10
29 and 5 source tasks respectively, using average rank; more results
on ADTM can be found in Appendix A.3.
   First, we can observe that the average rank of TransBO in Figure 2                                                                                                                                           The maximum number of trials for each task is 50, and we compare
and Figure 3 decreases sharply in the initial 20 trials. Compared                                                                                                                                               TransBO with TST, RGPE, and POGPE based on the best-observed
with other TL methods, it shows that TransBO can extract and                                                                                                                                                    performance on each task. Table 1 reports the number of tasks on
utilize the auxiliary source knowledge efficiently and effectively.                                                                                                                                             which each TL method gets the highest and second-highest per-
Remarkably, TransBO exhibits a strong stability from two perspec-                                                                                                                                               formance. Note that the sum of each column may be more than 30
tives: 1) TransBO is stable on different benchmarks; and 2) it still                                                                                                                                            since some of the TL methods are tied for first or second place.
performs well when given a different number of source tasks, e.g.,                                                                                                                                                 As shown in Table 1, TransBO achieves the largest number of
in Figure 2 𝑁𝑡𝑎𝑠𝑘 = 29, and 𝑁𝑡𝑎𝑠𝑘 = 5 in Figure 3. RGPE is one of                                                                                                                                               top1 and top2 online performance among the compared methods.
the most competitive baselines, and we take it as an example. RGPE                                                                                                                                              Take Adaboost as an example, TransBO gets 25 top2 results among
achieves comparable or similar performance with TransBO in Fig-                                                                                                                                                 30 tasks, while this number is 13 for RGPE. RGPE gets a similar
ure 2(b) and Figure 2(c) where 𝑁𝑡𝑎𝑠𝑘 = 29. However, in Figure 3(b)                                                                                                                                              performance with TST on Lightgbm and Extra Trees, but its perand Figure 3(c) RGPE exhibits a larger fluctuation over the trials                                                                                                                                              formance decreases on Adaboost. Thus, RGPE is not stable in this
compared with TransBO when 𝑁𝑡𝑎𝑠𝑘 = 5. Unlike the baselines,                                                                                                                                                     scenario. Compared with the baselines, TransBO could achieve more
TransBO extracts the source knowledge in a principled way, and                                                                                                                                                  stable and satisfactory performance in the dynamic setting.
the empirical results show it performs well in most circumstances,
thus demonstrating its superior efficiency and effectiveness.                                                                                                                                                    5.3          Applying TransBO to NAS
Dynamic TL Setting. To simulate the real-world transfer learning                                                                                                                                                To investigate the universality of TransBO in conducting Neural
scenario, we perform the dynamic experiment on different bench-                                                                                                                                                 Architecture Search (NAS), here we use TransBO to extract and
marks. In this experiment, 30 tasks arrive sequentially; when the                                                                                                                                               integrate the optimization knowledge from NAS tasks on CIFAR-10
𝑖-th task arrives, the previous 𝑖-1 tasks are used as the source tasks.                                                                                                                                         and CIFAR-100 (with 50 trials each) to accelerate the NAS task on

KDD ’22, August 14–18, 2022, Washington, DC, USA                                                                                                                       Li et al.

ImageNet with NAS-Bench201 [7]. From Figure 6, we have that                              baselines - POGPE, RGPE, TST, SCoT, and TransBO on Random
TransBO could achieve more than 5x speedups over the state-of-the-                       Forest with 75 trials. To investigate the scalability of TransBO, we
art NAS methods – Bayesian Optimization (BO) and Regularized                             measure the runtime of the competitive TL methods: POGPE, RGPE,
Evolution Algorithm (REA) [43]. Therefore, TransBO can also be                           TST, SCoT, and TransBO. Each method is tested on Random Forest
applied to the NAS tasks.                                                                with 75 trials, and we repeat each method 20 times. Figure 5(c)
                                                                                         shows the experiment results, where the y-axis is the mean cu-
                                                                                         mulative runtime in seconds on a log scale. We do not take the
                                                             TransBO         BO
                                    0.555
                                                             RS              REA
                                                                                         evaluation time of each configuration into account, and only com-

            Best Validation Error
                                                                                         pare the optimization overhead of suggesting a new configuration.
                                    0.550
                                                                                         ScoT’s runtime increases rapidly among the compared methods
                                    0.545                                                as it has the 𝑂 (𝑘 3𝑛 3 ) complexity. Since both the two-phase and
                                                                                         one-phase framework-based methods own the 𝑂 (𝑛 3 ) complexity, it
                                    0.540
                                                                                         takes nearly the same optimization overhead for TST, POGPE, and
                                    0.535
                                                                                         TransBO to suggest a configuration in the first 75 trials. Although
                                                                                         RGPE also has the 𝑂 (𝑛 3 ) complexity, it depends on a sampling
                                    0.530                                                strategy to compute the surrogate weight, which introduces extra
                                        0.0   0.5     1.0    1.5       2.0         2.5   overhead to configuration suggestion. Instead, TransBO exhibits a
                                                Wall Clock Time (106s)                   similar scalability result like POGPE, which incorporates no opti-
                                                                                         mization overhead due to the constant weights. This shows that
   Figure 6: Results on optimizing NAS on NASBench201.                                   TransBO scales well in both the number of trials and tasks.

                                                                                         6    CONCLUSION
5.4    Ablation Studies                                                                  In this paper, we introduced TransBO, a novel two-phase trans-
Source Knowledge Learning. This experiment is designed to                                fer learning (TL) method for hyperparameter optimization (HPO),
evaluate the performance of source surrogate 𝑀 𝑆 learned in Phase                        which can leverage the auxiliary knowledge from previous tasks
1. 𝑀 𝑆 corresponds to the source knowledge extracted from the                            to accelerate the HPO process of the current task effectively. This
source tasks. In this setting, the source surrogate is used to guide                     framework can extract and aggregate the source and target knowlthe optimization of hyperparameters instead of the final TL sur-                         edge jointly and adaptively. In addition, we published a large-scale
rogate 𝑀𝑇 𝐿 . The quality of source knowledge learned by each TL                         TL benchmark for HPO with up to 1.8 million model evaluations;
method thus can be measured by the performance of 𝑀 𝑆 . Figure 4                         the extensive experiments, including static and dynamic transfer
shows the results of TransBO and three one-phase framework based                         learning settings and neural architecture search, demonstrate the
methods: POGPE, TST, and RGPE on two benchmarks — Adaboost                               superiority of TransBO over the state-of-the-art methods.
and LightGBM. We can observe that the proposed TransBO outperforms the other three baselines on both two metrics: average                          ACKNOWLEDGMENTS
rank and ADTM. According to some heuristics, these baselines                             This work was supported by the National Natural Science Founcalculate the weights in 𝑀 𝑆 independently. Instead, by solving the                      dation of China (No.61832001), Beijing Academy of Artificial Inconstrained optimization problem, TransBO can learn the optimal                          telligence (BAAI), PKU-Tencent Joint Research Lab. Bin Cui is the
weights in 𝑀 𝑠 in a joint and principled manner. More results on                         corresponding author.
the other two benchmarks can be found in Appendix A.3.
Target Weight Analysis. Here we compare the target weight                                REFERENCES
obtained in POGPE, RGPE, TST, and TransBO. Figure 5(a) and 5(b)                           [1] Rémi Bardenet, Mátyás Brendel, Balázs Kégl, and Michele Sebag. 2013. Collabo-
                                                                                              rative hyperparameter tuning. In ICML. 199–207.
illustrate the trend of target weight on two benchmarks: Random                           [2] Kristin P Bennett, Gautam Kunapuli, Jing Hu, and Jong-Shi Pang. 2008. Bilevel
Forest and Adaboost. The target weight in POGPE is fixed to a con-                            optimization and machine learning. In IEEE World Congress on Computational
stant - 0.5, regardless of the increasing number of trials; TST’s re-                         Intelligence. Springer, 25–47.
                                                                                          [3] James Bergstra and Yoshua Bengio. 2012. Random search for hyper-parameter
mains low even when the target observations are sufficient; RGPE’s                            optimization. Journal of Machine Learning Research 13, Feb (2012), 281–305.
shows a trend of fluctuation because the sampling-based ranking                           [4] James S Bergstra, Rémi Bardenet, Yoshua Bengio, and Balázs Kégl. 2011. Alloss is not stable. TransBO’s keeps increasing with the number of                             gorithms for hyper-parameter optimization. In Advances in neural information
                                                                                              processing systems. 2546–2554.
trials, which matches the intuition that the importance of the target                     [5] Bernd Bischl, Martin Binder, Michel Lang, Tobias Pielok, Jakob Richter, Stefan
surrogate should be low when target observations are insufficient                             Coors, Janek Thomas, Theresa Ullmann, Marc Becker, Anne-Laure Boulesteix,
                                                                                              et al. 2021. Hyperparameter optimization: Foundations, algorithms, best practices
and gradually increase as target observations grow.                                           and open challenges. arXiv preprint arXiv:2107.05847 (2021).
Scalability Analysis. In the static TL setting, we include different                      [6] Jacob Devlin, Ming-Wei Chang, Kenton Lee, and Kristina Toutanova. 2018. Bert:
                                                                                              Pre-training of deep bidirectional transformers for language understanding. arXiv
number of source tasks when conducting transfer learning (See                                 preprint arXiv:1810.04805 (2018).
Figures 2 and 3, where 𝑁𝑡𝑎𝑠𝑘 = 5 and 𝑁𝑡𝑎𝑠𝑘 = 29); the stable and                          [7] Xuanyi Dong and Yi Yang. 2019. NAS-Bench-201: Extending the Scope of Reeffective results show the scalability in terms of the number of                              producible Neural Architecture Search. In International Conference on Learning
                                                                                              Representations.
source tasks. We further investigate the optimization overhead of                         [8] Lukasz Dudziak, Thomas Chau, Mohamed Abdelfattah, Royson Lee, Hyeji Kim,
suggesting a new configuration, and measure the runtime of the                                and Nicholas Lane. 2020. BRP-NAS: Prediction-based NAS using GCNs. Advances

TransBO: Hyperparameter Optimization via Two-Phase Transfer Learning                                                        KDD ’22, August 14–18, 2022, Washington, DC, USA

     in Neural Information Processing Systems 33 (2020).                                         end-to-end AutoML via scalable search space decomposition. Proceedings of the
 [9] Stefan Falkner, Aaron Klein, and Frank Hutter. 2018. BOHB: Robust and Efficient             VLDB Endowment 14 (2021).
     Hyperparameter Optimization at Scale.. In ICML. 1436–1445.                             [34] Marius Lindauer and Frank Hutter. 2018. Warmstarting of model-based algorithm
[10] Matthias Feurer, Aaron Klein, Katharina Eggensperger, Jost Springenberg, Manuel             configuration. In Thirty-Second AAAI Conference on Artificial Intelligence.
     Blum, and Frank Hutter. 2015. Efficient and robust automated machine learning.         [35] Marius Thomas Lindauer, Katharina Eggensperger, Matthias Feurer, Andr’e
     In Advances in neural information processing systems. 2962–2970.                            Biedenkapp, Difan Deng, Caroline Benjamins, René Sass, and Frank Hutter. 2021.
[11] Matthias Feurer, Benjamin Letham, and Eytan Bakshy. 2018. Scalable meta-                    SMAC3: A Versatile Bayesian Optimization Package for Hyperparameter Opti-
     learning for bayesian optimization using ranking-weighted gaussian process                  mization. ArXiv abs/2109.09831 (2021).
     ensembles. In AutoML Workshop at ICML.                                                 [36] Sinno Jialin Pan and Qiang Yang. 2010. A Survey on Transfer Learning. IEEE
[12] Adam Foster, Martin Jankowiak, Elias Bingham, Paul Horsfall, Yee Whye Teh,                  Transactions on Knowledge and Data Engineering 22, 10 (2010), 1345–1359.
     Thomas Rainforth, and Noah Goodman. 2019. Variational Bayesian optimal                 [37] Sinno Jialin Pan, Qiang Yang, et al. 2010. A survey on transfer learning. IEEE
     experimental design. Advances in Neural Information Processing Systems 32                   Transactions on knowledge and data engineering 22, 10 (2010), 1345–1359.
     (2019).                                                                                [38] David Pardoe and Peter Stone. 2010. Boosting for regression transfer. In ICML.
[13] Daniel Golovin, Benjamin Solnik, Subhodeep Moitra, Greg Kochanski, John                     863–870.
     Karro, and D Sculley. 2017. Google vizier: A service for black-box optimization.       [39] Valerio Perrone, Rodolphe Jenatton, Matthias W Seeger, and Cédric Archambeau.
     In Proceedings of the 23rd ACM SIGKDD International Conference on Knowledge                 2018. Scalable hyperparameter transfer learning. Advances in neural information
     Discovery and Data Mining. ACM, 1487–1495.                                                  processing systems 31 (2018).
[14] Ian Goodfellow, Yoshua Bengio, and Aaron Courville. 2016. Deep learning. MIT           [40] Valerio Perrone, Huibin Shen, Matthias W Seeger, Cedric Archambeau, and
     press.                                                                                      Rodolphe Jenatton. 2019. Learning search spaces for bayesian optimization: An-
[15] Kaiming He, Xiangyu Zhang, Shaoqing Ren, and Jian Sun. 2016. Deep residual                  other view of hyperparameter transfer learning. Advances in Neural Information
     learning for image recognition. In Proceedings of the IEEE conference on computer           Processing Systems 32 (2019).
     vision and pattern recognition. 770–778.                                               [41] Matthias Poloczek, Jialei Wang, and Peter Frazier. 2017. Multi-information source
[16] Xiangnan He, Lizi Liao, Hanwang Zhang, Liqiang Nie, Xia Hu, and Tat-Seng                    optimization. In Advances in Neural Information Processing Systems. 4288–4298.
     Chua. 2017. Neural collaborative filtering. In Proceedings of the 26th international   [42] Carl Edward Rasmussen. 2004. Gaussian processes in machine learning. In
     conference on world wide web. 173–182.                                                      Advanced lectures on machine learning. Springer, 63–71.
[17] Bruno Miranda Henrique, Vinicius Amorim Sobreiro, and Herbert Kimura. 2019.            [43] Esteban Real, Alok Aggarwal, Yanping Huang, and Quoc V Le. 2019. Regularized
     Literature review: Machine learning techniques applied to financial market pre-             evolution for image classifier architecture search. In Proceedings of the AAAI
     diction. Expert Systems with Applications 124 (2019), 226–251.                              conference on artificial intelligence, Vol. 33. 4780–4789.
[18] Geoffrey Hinton, Li Deng, Dong Yu, George E Dahl, Abdel-rahman Mohamed,                [44] David Salinas, Huibin Shen, and Valerio Perrone. 2020. A quantile-based approach
     Navdeep Jaitly, Andrew Senior, Vincent Vanhoucke, Patrick Nguyen, Tara N                    for hyperparameter transfer learning. In ICML. PMLR, 8438–8448.
     Sainath, et al. 2012. Deep neural networks for acoustic modeling in speech             [45] Nicolas Schilling, Martin Wistuba, Lucas Drumond, and Lars Schmidt-Thieme.
     recognition: The shared views of four research groups. IEEE Signal processing               2015. Hyperparameter optimization with factorized multilayer perceptrons. In
     magazine 29, 6 (2012), 82–97.                                                               ECML PKDD. 87–103.
[19] Samuel Horváth, Aaron Klein, Peter Richtárik, and Cédric Archambeau. 2021.             [46] Nicolas Schilling, Martin Wistuba, and Lars Schmidt-Thieme. 2016. Scalable
     Hyperparameter transfer learning with adaptive complexity. In International                 hyperparameter optimization with products of gaussian process experts. In ECML
     Conference on Artificial Intelligence and Statistics. PMLR, 1378–1386.                      PKDD. Springer, 33–48.
[20] Frank Hutter, Holger H Hoos, and Kevin Leyton-Brown. 2011. Sequential model-           [47] Ankur Sinha, Pekka Malo, and Kalyanmoy Deb. 2017. A review on bilevel
     based optimization for general algorithm configuration. In International Confer-            optimization: from classical to evolutionary approaches and applications. IEEE
     ence on Learning and Intelligent Optimization. Springer, 507–523.                           Transactions on Evolutionary Computation 22, 2 (2017), 276–295.
[21] Donald R Jones, Matthias Schonlau, and William J Welch. 1998. Efficient global         [48] Jasper Snoek, Hugo Larochelle, and Ryan P Adams. 2012. Practical bayesian
     optimization of expensive black-box functions. Journal of Global optimization 13,           optimization of machine learning algorithms. In Advances in neural information
     4 (1998), 455–492.                                                                          processing systems. 2951–2959.
[22] Tinu Theckel Joy, Santu Rana, Sunil Kumar Gupta, and Svetha Venkatesh. 2016.           [49] Jasper Snoek, Oren Rippel, Kevin Swersky, Ryan Kiros, Nadathur Satish,
     Flexible transfer learning framework for Bayesian optimisation. In Pacific-Asia             Narayanan Sundaram, Mostofa Patwary, Mr Prabhat, and Ryan Adams. 2015.
     Conference on Knowledge Discovery and Data Mining. Springer, 102–114.                       Scalable bayesian optimization using deep neural networks. In ICML. PMLR,
[23] Kirthevasan Kandasamy, Gautam Dasarathy, Jeff Schneider, and Barnabás Póczos.               2171–2180.
     2017. Multi-fidelity bayesian optimisation with continuous approximations. In          [50] Jost Tobias Springenberg, Aaron Klein, Stefan Falkner, and Frank Hutter. 2016.
     ICML. PMLR, 1799–1808.                                                                      Bayesian optimization with robust Bayesian neural networks. Advances in neural
[24] Guolin Ke, Qi Meng, Thomas Finley, Taifeng Wang, Wei Chen, Weidong Ma,                      information processing systems 29 (2016).
     Qiwei Ye, and Tie-Yan Liu. 2017. Lightgbm: A highly efficient gradient boosting        [51] Kevin Swersky, Jasper Snoek, and Ryan P Adams. 2013. Multi-task bayesian
     decision tree. Advances in neural information processing systems 30 (2017).                 optimization. Advances in neural information processing systems 26 (2013).
[25] Jungtaek Kim, Saehoon Kim, and Seungjin Choi. 2017. Learning to Transfer               [52] Kevin Swersky, Jasper Snoek, and Ryan Prescott Adams. 2014. Freeze-thaw
     Initializations for Bayesian Hyperparameter Optimization. ArXiv abs/1710.06219              Bayesian optimization. arXiv preprint arXiv:1406.3896 (2014).
     (2017).                                                                                [53] Joaquin Vanschoren, Jan N Van Rijn, Bernd Bischl, and Luis Torgo. 2014. OpenML:
[26] Aaron Klein, Stefan Falkner, Simon Bartels, Philipp Hennig, Frank Hutter, et al.            networked science in machine learning. ACM SIGKDD Explorations Newsletter
     2017. Fast Bayesian hyperparameter optimization on large datasets. Electronic               15, 2 (2014), 49–60.
     Journal of Statistics 11, 2 (2017), 4945–4968.                                         [54] Pauli Virtanen, Ralf Gommers, Travis E Oliphant, Matt Haberland, Tyler
[27] Aaron Klein, S. Falkner, Jost Tobias Springenberg, and F. Hutter. 2017. Learning            Reddy, David Cournapeau, Evgeni Burovski, Pearu Peterson, Warren Weckesser,
     Curve Prediction with Bayesian Neural Networks. In ICLR.                                    Jonathan Bright, et al. 2020. SciPy 1.0: fundamental algorithms for scientific
[28] Dieter Kraft. 1994. Algorithm 733: TOMP–Fortran modules for optimal control                 computing in Python. Nature methods 17, 3 (2020), 261–272.
     calculations. ACM Transactions on Mathematical Software (TOMS) 20, 3 (1994),           [55] Ying Wei, Peilin Zhao, and Junzhou Huang. 2021. Meta-learning Hyperparameter
     262–281.                                                                                    Performance Prediction with Neural Processes. In ICML. PMLR, 11058–11067.
[29] Yang Li, Jiawei Jiang, Jinyang Gao, Yingxia Shao, Ce Zhang, and Bin Cui. 2020.         [56] Martin Wistuba, Nicolas Schilling, and Lars Schmidt-Thieme. 2015. Hyperpa-
     Efficient automatic CASH via rising bandits. In Proceedings of the AAAI Conference          rameter search space pruning–a new component for sequential model-based
     on Artificial Intelligence, Vol. 34. 4763–4771.                                             hyperparameter optimization. In ECML PKDD. Springer, 104–119.
[30] Yang Li, Yu Shen, Huaijun Jiang, Wentao Zhang, Jixiang Li, Ji Liu, Ce Zhang, and       [57] Martin Wistuba, Nicolas Schilling, and Lars Schmidt-Thieme. 2015. Sequential
     Bin Cui. 2022. Hyper-Tune: Towards Efficient Hyper-parameter Tuning at Scale.               model-free hyperparameter tuning. In Data Mining (ICDM), 2015 IEEE Interna-
     Proceedings of the VLDB Endowment 15 (2022).                                                tional Conference on. IEEE, 1033–1038.
[31] Yang Li, Yu Shen, Jiawei Jiang, Jinyang Gao, Ce Zhang, and Bin Cui. 2021. MFES-        [58] Martin Wistuba, Nicolas Schilling, and Lars Schmidt-Thieme. 2016. Two-stage
     HB: Efficient Hyperband with Multi-Fidelity Quality Measurements. In Proceed-               transfer surrogate model for automatic hyperparameter optimization. In ECML
     ings of the AAAI Conference on Artificial Intelligence, Vol. 35. AAAI Press, 8491–          PKDD. 199–214.
     8500.                                                                                  [59] Quanming Yao, Mengshuo Wang, H. Escalante, I. Guyon, Yi-Qi Hu, Yu-Feng Li,
[32] Yang Li, Yu Shen, Wentao Zhang, Yuanwei Chen, Huaijun Jiang, Mingchao                       Wei-Wei Tu, Qiang Yang, and Yang Yu. 2018. Taking Human out of Learning
     Liu, Jiawei Jiang, Jinyang Gao, Wentao Wu, Zhi Yang, et al. 2021. Openbox:                  Applications: A Survey on Automated Machine Learning. ArXiv abs/1810.13306
     A generalized black-box optimization service. In Proceedings of the 27th ACM                (2018).
     SIGKDD Conference on Knowledge Discovery & Data Mining. 3209–3219.                     [60] Dani Yogatama and Gideon Mann. 2014. Efficient transfer learning method for
[33] Yang Li, Yu Shen, Wentao Zhang, Jiawei Jiang, Bolin Ding, Yaliang Li, Jingren               automatic hyperparameter tuning. In Artificial Intelligence and Statistics. 1077–
     Zhou, Zhi Yang, Wentao Wu, Ce Zhang, et al. 2021. VolcanoML: speeding up                    1085.

KDD ’22, August 14–18, 2022, Washington, DC, USA                                                                                          Li et al.

A APPENDIX                                                                               Name           #Rows   #Columns    #Categories

A.1 The Details of Benchmark                                                             balloon         2001       1             2
                                                                                           kc1           2109       21            2
As described in Section 5, we create a benchmark to evaluate the                          quake          2178        3            2
performance of TL methods. We choose four ML algorithms that are                        segment          2310      19             7
widely used in data analysis, including Random Forest, Extra Trees,                     madelon          2600      500            2
Adaboost and Lightgbm. The implementation of each algorithm and                         space_ga         3107       6             2
                                                                                          splice         3190       60            3
the design of their hyperparameter space follows Auto-sklearn. For
                                                                                        kr-vs-kp         3196       36            2
each algorithm, the range and default value of each hyperparameter                         sick          3772       29            2
are illustrated in Tables 2, 3 and 4. To collect sufficient source HPO               hypothyroid(1)      3772       29            4
data for transfer learning, we select 30 real-world datasets from                    hypothyroid(2)      3772       29            2
OpenML repository, and evaluate the validation performance (i.e.,                         pollen         3848        5            2
the balanced accuracy) of 20k configurations for each benchmark,                  analcatdata_supreme    4052        7            2
                                                                                         abalone         4177        8           26
which are randomly sampled from the hyperparameter space. The
                                                                                       spambase          4600       57            2
datasets used in our benchmarks are of medium size, whose number                   winequality_white     4898       11            7
of rows ranges from 2000 to 8192. For more details, see Table 5.                   waveform-5000(1)      5000       40            3
The total number of model evaluations (observations) in our bench-                 waveform-5000(2)      5000       40            2
marks reaches 1.8 million and it takes more than 100k CPU hours                      page-blocks(1)      5473      10             5
to evaluate all the configurations. For reproduction purposes, we                    page-blocks(2)      5473      10             2
                                                                                        optdigits        5610       64           10
also upload the benchmark data (e.g., evaluation results and the                        satimage         6430      36             6
corresponding scripts) along with this submission. The benchmark                          wind           6574       14            2
data (with size – 477.7Mb); due to the space limit (maximum 20Mb)                         musk           6598      167            2
on CMT3, we only upload a small subset of benchmark on one                           delta_ailerons      7129       5             2
algorithm — LightGBM. After the review process, we will make the                       mushroom          8124       22            2
                                                                                       puma8NH           8192       8             2
complete benchmark publicly available (e.g., on Google Drive).
                                                                                       cpu_small         8192       12            2
                                                                                         cpu_act         8192       21            2
         Hyperparameter                 Range              Default                     bank32nh          8192       32            2
         n_estimators              [50, 500]                 50            Table 5: Details of 30 datasets used in the benchmarks.
         learning_rate (log)       [0.01, 2]                0.1
         algorithm             {SAMME.R, SAMME}           SAMME.R
         max_depth                   [2, 8]                   3
             Table 2: Hyperparameters of Adaboost.                       A.2     Feasibility of Transfer Learning
                                                                         To verify the feasibility of transfer learning in the setting of HPO, we
                                                                         conduct an HPO experiment on two datasets — quake and hypothy-
                                                                         roid(2). We tune the learning rate and n_estimators of Adaboost
              Hyperparameter             Range         Default           while fixing the other hyperparameters, and then evaluate the vali-
              criterion              {gini, entropy}     gini            dation performance (the balanced accuracy) of each configuration.
              max_features                [0, 1]          0.5            Figure 9 shows the performance on 2500 Adaboost configurations,
              min_sample_split           [2, 20]           2             where deeper color means better performance.
              min_sample_leaf            [1, 20]           1                It is quite clear that the optimal configuration differs on the two
              bootstrap               {True, False}      True            datasets (tasks), which means re-optimization is essential for HPO.
Table 3: Hyperparameters of Random Forest and Extra                      However, the performance distribution is somehow similar on the
Trees.                                                                   two datasets. For example, they both perform badly in the lower-
                                                                         right region and perform well in the upper region. Based on this
                                                                         observation, it is natural to accelerate the re-optimization process
                                                                         with the auxiliary knowledge acquired from the previous tasks.
               Hyperparameter             Range        Default
               n_estimators            [100, 1000]      500
                                                                         A.3     More Experiment Results
               num_leaves               [31, 2047]      127              In this section, we provide more experiment results besides those
               learning_rate (log)     [0.001, 0.3]     0.1              in Section 5.
               min_child_samples          [5, 30]        20              Static Experiments Figure 7 shows the results of all considered
               subsample                 [0.7, 1]        1               methods on the four benchmarks, where the metric is ADTM. We
               colsample_bytree          [0.7, 1]        1
                                                                         can observe that the proposed TransBO exhibits strong stability,
            Table 4: Hyperparameters of LightGBM.                        and performs well across benchmarks.
                                                                         Source Knowledge Learning The additional results on Random
                                                                         Forest and Extra Trees are illustrated in Figure 8. Similar to the

TransBO: Hyperparameter Optimization via Two-Phase Transfer Learning                                                                                                                                                                                                                        KDD ’22, August 14–18, 2022, Washington, DC, USA

                                0.160                  TransBO              SCoT              TST-M                                    0.1500                   TransBO             SCoT                TST-M                      0.19                TransBO            SCoT             TST-M                               TransBO         SCoT               TST-M
                                                       RS                   SGPR              POGPE                                                             RS                  SGPR                POGPE                                          RS                 SGPR             POGPE             0.170             RS              SGPR               POGPE
                                                       I-GP                 TST               RGPE                                     0.1475                   I-GP                TST                 RGPE                                           I-GP               TST              RGPE                                I-GP            TST                RGPE
                                0.155                                                                                                                                                                                              0.18
                                                                                                                                       0.1450                                                                                                                                                                0.165

                   ADTM                                                                                                         ADTM                                                                                     ADTM                                                                         ADTM
                                                                                                                                       0.1425                                                                                      0.17                                                                      0.160
                                0.150
                                                                                                                                       0.1400
                                                                                                                                                                                                                                                                                                             0.155
                                                                                                                                                                                                                                   0.16
                                0.145                                                                                                  0.1375
                                                                                                                                                                                                                                                                                                             0.150
                                                                                                                                       0.1350                                                                                      0.15
                                0.140                                                                                                                                                                                                                                                                        0.145
                                                                                                                                       0.1325
                                                                                                                                                                                                                                   0.14
                                                                                                                                       0.1300                                                                                                                                                                0.140
                                            0    5 10 15 20 25 30 35 40 45 50 55 60 65 70 75                                                       0    5 10 15 20 25 30 35 40 45 50 55 60 65 70 75                                           0   5 10 15 20 25 30 35 40 45 50 55 60 65 70 75                        0   5 10 15 20 25 30 35 40 45 50 55 60 65 70 75
                                                              Number of Trials                                                                                         Number of Trials                                                                     Number of Trials                                                        Number of Trials

                                        (a) Random Forest (ADTM)                                                                                       (b) LightGBM (ADTM)                                                                        (c) Adaboost (ADTM)                                                (d) Extra Trees (ADTM)

                                                                                                                                Figure 7: Static results on four benchmarks with 75 trials.

                                                                            TransBO           POGPE                                                                                 TransBO             POGPE                     3.2                                     TransBO          POGPE                                              TransBO             POGPE
                                                                                                                                         0.19
                                3.2                                         TST               RGPE                                                                                  TST                 RGPE                                                              TST              RGPE              0.170                            TST                 RGPE
                                                                                                                                                                                                                                  3.0
                                3.0                                                                                                      0.18
                                                                                                                                                                                                                                                                                                             0.165

                 Average Rank                                                                                                                                                                                      Average Rank
                                2.8                                                                                                                                                                                               2.8

                                                                                                                                  ADTM                                                                                                                                                                ADTM
                                                                                                                                         0.17                                                                                                                                                                0.160
                                2.6                                                                                                                                                                                               2.6

                                2.4                                                                                                      0.16                                                                                                                                                                0.155
                                                                                                                                                                                                                                  2.4
                                2.2                                                                                                                                                                                                                                                                          0.150
                                                                                                                                         0.15                                                                                     2.2
                                2.0
                                                                                                                                                                                                                                                                                                             0.145
                                                                                                                                                                                                                                  2.0
                                1.8                                                                                                      0.14
                                        0       5    10      15   20   25     30    35   40      45   50                                           0     5      10     15     20   25   30    35   40    45   50                          0       5   10   15   20   25     30   35   40    45   50                  0     5   10   15   20   25   30   35   40    45   50
                                                             Number of Trials                                                                                          Number of Trials                                                                    Number of Trials                                                         Number of Trials

                                (a) Random Forest (Average Rank)                                                                                (b) Random Forest (ADTM)                                                                (c) Extra Trees (Average Rank)                                               (d) Extra Trees (ADTM)

                                                                                                                                                 Figure 8: Results on source knowledge learning.

                 50                                                                                                       50                                                                                                       B              REPRODUCTION DETAILS
                 141                                                                     0.510                            141                                                                  0.990
                                                                                                                                                                                                                                  The source code and the benchmark data are available in the com-
                                                                                                                                                                                                                                  pressed file “benchmark_data_and_source_code.zip” on CMT3. The
  n estimators                                                                                             n estimators
                                                                                         0.504                                                                                                 0.985
                 233                                                                                                      233
                                                                                                                                                                                                                                  source code is also available in the anonymous repository 3 now. All
                                                                                         0.498                                                                                                 0.980
                                                                                         0.492                                                                                                 0.975
                 325                                                                                                      325
                                                                                         0.486
                                                                                         0.480
                                                                                                                                                                                               0.970
                                                                                                                                                                                               0.965
                                                                                                                                                                                                                                  files in the benchmark should be placed under the folder ‘data/hpo_data’
                 417                                                                                                      417                                                                                                     of the project root directory. To reproduce the experimental results
                 500
                       0.01             0.0295      0.0869    0.2563   0.7558      0.2
                                                                                                                          500
                                                                                                                            0.01          0.0295       0.0869        0.2563    0.7558   0.2
                                                                                                                                                                                                                                  in this paper, an environment of Python 3.6+ is required. We in-
                                                    learning rate                                                                                      learning rate
                                                      (a) quake                                                                                 (b) hypothyroid(2)
                                                                                                                                                                                                                                  troduce the experiment scripts and installation of required tools
                                                                                                                                                                                                                                  in README.md and list the required Python packages in require-
Figure 9: Performance of 2500 Adaboost configurations on                                                                                                                                                                          ments.txt under the root directory. Take one experiment as an
two tasks, in which each hyperparameter has 50 settings.                                                                                                                                                                          example, to evaluate the static TL performance of TransBO and
                                                                                                                                                                                                                                  other baselines on Random Forest using 29 source tasks with 75
                                                                                                                                                                                                                                  trials, you need to execute the following script:
                                                                                                                                                                                                                                  python tools/static_benchmark.py –trial_num 75 –algo_id random_forest
findings in Section 5.4, our method - TransBO shows excellent                                                                                                                                                                     –methods rgpe,pogpe,tst,transbo –num_source_problem 29
ability in extracting and integrating the source knowledge from                                                                                                                                                                       Please check the document README.md in this repository for
previous tasks.                                                                                                                                                                                                                   more details, e.g., how to use the benchmark, and how to run the
                                                                                                                                                                                                                                  other experiments.

A.4                                   Convergence Discussion about TransBO
In TransBO, when sufficient trials on the target task are obtained,
the weight of target surrogate 𝑝𝑇 will approach 1 as the HPO
proceeds. Based on the mechanism we adopted in TransBO— crossvalidation, we can observe that 𝑝𝑇𝑖 in the 𝑖-th trial will approach 1
as 𝑖 increases. Therefore, the final TL surrogate 𝑀𝑇 𝐿 will be set to
the target surrogate 𝑀𝑇 . So we can have that,
With sufficient trials, the final TL surrogate will find the same optimum as the target surrogate does; that’s, the final solution of surrogate
𝑀𝑇 𝐿 will be no worse than the one in 𝑀𝑇 given sufficient trials.
The above finding demonstrates that TransBO can alleviate negative
transfer [37]. In other words, it can avoid performance degradation
compared with non-transfer methods – the traditional BO methods.                                                                                                                                                                   3 https://anonymous.4open.science/r/TransBO-EE01/
