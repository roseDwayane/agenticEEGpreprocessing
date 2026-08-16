---
citation_key: "LiEtAl2022d"
title: "Transfer Learning based Search Space Design for Hyperparameter Tuning"
authors: "Yang Li; Yu Shen; Huaijun Jiang; Tianyi Bai; Wentao Zhang; Ce Zhang; Bin Cui"
year: 2022
doi: "10.1145/3534678.3539369"
source: "arXiv (2206.02511)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "2206.02511"
conversion: "pdftotext -layout (automated)"
---

# Transfer Learning based Search Space Design for Hyperparameter Tuning

Transfer Learning based Search Space Design for
                                                                          Hyperparameter Tuning

                                                      Yang Li†§ , Yu Shen† , Huaijun Jiang† , Tianyi Bai∗ , Wentao Zhang† , Ce Zhang‡ , Bin Cui†⋄
                                                   † School of CS & Key Laboratory of High Confidence Software Technologies (MOE), Peking University, China
                                                                                                       § Data Platform, TEG, Tencent Inc., China
                                                                            ‡ Department of Computer Science, Systems Group, ETH Zürich, Switzerland
                                                                            ∗ School of Mathematics and Statistics, Beijing Institute of Technology, China
                                                                          ⋄ Institute of Computational Social Science, Peking University (Qingdao), China
                                               † {liyang.cs, shenyu, jianghuaijun, wentao.zhang, bin.cui}@pku.edu.cn ‡ ce.zhang@inf.ethz.ch § baitianyi@bit.edu.cn

arXiv:2206.02511v1 [cs.LG] 6 Jun 2022
                                        ABSTRACT                                                                                      1    INTRODUCTION
                                        The tuning of hyperparameters becomes increasingly important                                  The performance of modern machine learning (ML) and data mining
                                        as machine learning (ML) models have been extensively applied                                 methods highly depends on their hyperparameter configurations [5,
                                        in data mining applications. Among various approaches, Bayesian                               12, 14, 15], e.g., learning rate, the number of hidden layers in a
                                        optimization (BO) is a successful methodology to tune hyperpa-                                deep neural network, etc. As a result, automatically tuning the
                                        rameters automatically. While traditional methods optimize each                               hyperparameters has attracted lots of interest from both academia
                                        tuning task in isolation, there has been recent interest in speeding                          and industry [4, 35]. A large number of approaches have been
                                        up BO by transferring knowledge across previous tasks. In this                                proposed to automate this process, including random search [2],
                                        work, we introduce an automatic method to design the BO search                                Bayesian optimization [3, 16, 41], evolutionary optimization [13, 37]
                                        space with the aid of tuning history from past tasks. This simple                             and bandit-based methods [7, 17, 22, 24, 25].
                                        yet effective approach can be used to endow many existing BO                                     Among various alternatives, Bayesian optimization (BO) is one
                                        methods with transfer learning capabilities. In addition, it enjoys                           of the most prevailing frameworks for automatic hyperparameter
                                        the three advantages: universality, generality, and safeness. The                             optimization (HPO) [3, 16, 40, 41]. The main idea of BO is to use a
                                        extensive experiments show that our approach considerably boosts                              surrogate model, typically a Gaussian Process (GP) [36], to model
                                        BO by designing a promising and compact search space instead of                               the relationship between a hyperparameter configuration and its
                                        using the entire space, and outperforms the state-of-the-arts on a                            performance (e.g., validation error), and then utilize this surrogate to
                                        wide range of benchmarks, including machine learning and deep                                 guide the configuration search over the given hyperparameter space
                                        learning tuning tasks, and neural architecture search.                                        in an iterative manner. However, with the rise of big data and deep
                                                                                                                                      learning (DL) techniques, the following two factors greatly hampers
                                        CCS CONCEPTS                                                                                  the efficiency of BO: (a) large search space and (b) computationally-
                                        • Computing methodologies → Machine learning; Transfer                                        expensive evaluation for each configuration. Given a limited budget,
                                        learning.                                                                                     BO methods can obtain only a few observations over the large
                                                                                                                                      space. In this case, BO fails to converge to the optimal configuration
                                        KEYWORDS                                                                                      quickly, which we refer to as the low-efficiency issue [7, 27].
                                                                                                                                         (Opportunities) To address this issue, researchers in the HPO
                                        hyperparameter optimization, search space design, bayesian opti-
                                                                                                                                      community propose to incorporate the spirit of transfer learning to
                                        mization, transfer learning
                                                                                                                                      accelerate hyperparameter tuning, which could borrow strength
                                        ACM Reference Format:                                                                         from the past tasks (source tasks) to accelerate the current task
                                        Yang Li, Yu Shen, Huaijun Jiang, Tianyi Bai, Wentao Zhang, Ce Zhang,                          (target task). A line of work [9, 32, 47] aims to learn surrogates with
                                        and Bin Cui. 2022. Transfer Learning based Search Space Design for Hy-                        the aid of past tuning history, and the surrogates are utilized to
                                        perparameter Tuning. In Proceedings of the 28th ACM SIGKDD Confer-                            guide the search of configurations. Orthogonal to these approaches,
                                        ence on Knowledge Discovery and Data Mining (KDD ’22), August 14–18,                          we concentrate on how to design a more promising and compact
                                        2022, Washington, DC, USA. ACM, New York, NY, USA, 11 pages. https:
                                                                                                                                      search space with the merit of transfer learning, and further speed
                                        //doi.org/10.1145/3534678.3539369
                                                                                                                                      up the HPO process. The basic idea is that although the optimal
                                                                                                                                      configuration may be different for each HPO task, the region of
                                        Permission to make digital or hard copies of all or part of this work for personal or
                                        classroom use is granted without fee provided that copies are not made or distributed         well-performing hyperparameter configurations for the current
                                        for profit or commercial advantage and that copies bear this notice and the full citation     task may share some similarity with previous HPO tasks due to
                                        on the first page. Copyrights for components of this work owned by others than ACM
                                        must be honored. Abstracting with credit is permitted. To copy otherwise, or republish,
                                                                                                                                      the relevancy among tasks (See the left and middle heatmaps in
                                        to post on servers or to redistribute to lists, requires prior specific permission and/or a   Figure 1). Motivated by the observation, in this paper, we focus on
                                        fee. Request permissions from permissions@acm.org.                                            developing an automatic search space design method for BO, instead
                                        KDD ’22, August 14–18, 2022, Washington, DC, USA
                                                                                                                                      of using the entire and large search space.
                                        © 2022 Association for Computing Machinery.
                                        ACM ISBN 978-1-4503-9385-0/22/08. . . $15.00
                                        https://doi.org/10.1145/3534678.3539369

KDD ’22, August 14–18, 2022, Washington, DC, USA                                                                                                                                                  Li et al.

                       1.0                                                        1.0                                                            1.0
                                                            0.15
                                                                                                                           0.09                                                            0.09
                       0.8                                  0.14                  0.8                                                            0.8
                                                                                                                           0.08

        Max Features                                               Max Features                                                   Max Features
                                                                                                                                                                                           0.08
                                                            0.13
                       0.6                                                        0.6                                      0.07                  0.6
                                                                                                                                                                                           0.07
                                                            0.12                                                           0.06
                       0.4                                                        0.4                                                            0.4
                                                                                                                                                                                           0.06
                                                            0.11                                                           0.05

                       0.2                                                        0.2                                      0.04                  0.2                                       0.05
                                                            0.10
                                                                                                                           0.03                                                            0.04
                       0.0                                                        0.0                                                            0.0
                             2   11   21    31    41   51                               2   11   21    31       41    51                               2   11     21     31      41   51
                                  Min Samples Split                                          Min Samples Split                                              Min Samples Split
                                      satimage                                                   segment                                                        page-blocks(2)
Figure 1: Validation error of 2500 configurations (50 settings of max features and 50 settings of min samples split) when tuning
two hyperparameters of random forest on three OpenML datasets. The red region indicates the promising configurations.

    (Challenges) However, designing the compact search space au-                                            similarity, respectively, and then generate the final search space for
tomatically is non-trivial. To fully unleash the potential of transfer                                      the target task – a sub-region of the complete search space – via
learning for space design, we need to consider the following prob-                                          sampling-based framework along with a voting mechanism. In this
lems: (a) Representation of good/promising region: The region of good                                       way, the proposed method could leverage the promising regions
configurations (promising region) in source tasks has uncertain                                             obtained from similar previous HPO tasks to craft a promising and
shapes (See the deep red regions in Figure 1); in this case, simple                                         compact search space for the current task automatically.
geometrical representations of the search space (e.g., bounding box                                            (Contributions) We summarize our main contributions as folor ellipsoid used in [33]) cannot capture the shape of promising                                            lows: (a) We present a novel transfer learning-based search space
regions well. (b) Relevancy between tasks: While sharing some cor-                                          design method for hyperparameter optimization. Our method learns
relation between tasks, the underlying objective surface may be                                             a suitable search space in an adaptive manner: it can endow BO
quite different (See the middle and right heatmaps in Figure 1). If                                         algorithms with transfer learning capabilities. (b) On an extensive
one ignores this diversity, the performance of transfer learning may                                        set of benchmarks, including tuning ML algorithm on OpenML
be greatly hampered, where a loose search space is obtained, or                                             problems, optimizing ResNet on three vision tasks, and conducting
the optimal configuration is dismissed from the generated search                                            neural architecture search (NAS) on NASBench201, the empirical
space, which further leads to the “negative transfer” issue [31]. Sev-                                      results demonstrate that our approach outperforms the existing
eral HPO methods [10, 47] measure the similarity scores between                                             method and significantly speeds up the HPO process. (c) Despite
HPO tasks by computing the distance of meta-features for data set,                                          the empirical performance, our method enjoys the following three
while the meta-features in practice are often hard to obtain and                                            advantages: universality, practicality, and safeness. With these propneed careful manual design [9, 33]. Therefore, we need to learn this                                        erties, our approach can be seamlessly combined with a wide range
similarity during the HPO process automatically.                                                            of existing HPO techniques. (d) We create and publish large-scale
    In addition, (c) utilization of promising regions is also a chal-                                       benchmarks for transfer learning of HPO, which are significantly
lenging problem. To prevent negative transfer, the dissimilar tasks                                         larger than the existing ones. We hope this benchmark would help
should hold large enough promising regions, so that the optimal                                             facilitate the research on search space design for HPO.
configuration of the target task is not excluded. On the contrary,
given similar source tasks, a small promising region could greatly                                          2        RELATED WORK
speed up the search of configuration in the target task. As a result,
the size of promising regions should be carefully crafted based on                                          Hyperparameter optimization (HPO) is one of the most fundamental
task relevancy. Finally, we need to leverage these promising regions                                        tasks when developing machine learning (ML) and data mining
to build a compact search space for the target task. Rather than sim-                                       (DM) applications [4, 28]. Many approaches have been proposed
ple intuitions that use a single promising region, a mature design                                          to conduct automatic HPO, such as random search [2], Bayesian
should utilize multiple source tasks. However, how to select and                                            optimization (BO) [3, 16], bandit based method [17, 22, 23], etc. In
leverage those promising regions is still an open question.                                                 addition, many tuning systems [21, 24, 26, 34, 48] in the ML/DM
    In this paper, we propose a novel transfer learning-based search                                        community have successfully integrate the HPO algorithms.
space design method for hyperparameter optimization. Instead of                                                Recently, many researchers have proposed to utilize transfer
restricting the promising region to geometrical shapes, we propose                                          learning within the BO framework to accelerate HPO. The target is
to use a machine learning algorithm to learn the good region au-                                            to leverage auxiliary knowledge acquired from the previous source
tomatically. Concretely, we turn it into a supervised classification                                        tasks to achieve faster optimization on the target task, that is, borproblem. In addition, we develop a ranking-based method to mea-                                             row strength from past HPO tasks. Orthogonal to our contribution,
sure the task correlation between the source and target task on the                                         one common way is to learn surrogate models from past tuning
fly, and the size of promising regions could be adjusted adaptively                                         history and use them to guide the search of hyperparameters. For
based on this correlation. With these basic ingredients, we first ex-                                       instance, several methods learn all available information from both
tract the promising region from each source task based on the task                                          source and target tasks in a single surrogate, and make the data
                                                                                                            comparable through multi-task GPs [44], a ranking algorithm [1], a

Transfer Learning based Search Space Design for Hyperparameter Tuning                                     KDD ’22, August 14–18, 2022, Washington, DC, USA

mixed kernel GP [49], the GP noisy model [19], a multi-layer percep-           Algorithm 1: Pseudo code for Bayesian Optimization
tron with Bayesian linear regression heads [32, 42], a Gaussian cop-              Input: the number of trials 𝑇 , the hyper-parameter space 𝑋 , surrogate
ula process [38] or replace GP with Bayesian neural networks [43].                    model 𝑀, acquisition function 𝛼, and initial hyper-parameter
In addition, several approaches train multiple base surrogates, and                   configurations 𝑋𝑖𝑛𝑖𝑡 .
then combine all base surrogates into a single surrogate with dataset              1: for {𝒙 ∈ 𝑋𝑖𝑛𝑖𝑡 } do
similarities [47], weights adjusted via GP uncertainties [39] or the               2:    evaluate the configuration 𝒙 and obtain its performance 𝑦.
weights estimated by rankings [9]. Similarly, Golovin et al. [11]                  3:    augment 𝐷 = 𝐷 ∪ (𝒙, 𝑦).
build a stack of GPs by iteratively regressing the residuals with the              4: end for
most recent source task. Finally, instead of fitting surrogates on                 5: initialize observations 𝐷 with initial design.
                                                                                   6: for { 𝑖 = |𝑋𝑖𝑛𝑖𝑡 | + 1, ...,𝑇 } do
the past observations, several approaches [20, 29] achieve trans-
                                                                                   7:    fit surrogate 𝑀 based on observations 𝐷.
fer learning in a different way. They warm-start BO by selecting
                                                                                   8:    design the search space: X̂ = design( X).
several initial hyperparameter configurations as the start points of               9:    select the configuration to evaluate: 𝒙𝑖 = argmax𝒙∈X̂ 𝛼 (𝒙, 𝑀).
search procedures to accelerate the searching process.                            10:    evaluate the configuration 𝒙𝑖 and obtain its performance 𝑦𝑖 .
   Recently, transferring search space has become another way for                 11:    augment 𝐷 = 𝐷 ∪ (𝒙𝑖 , 𝑦𝑖 ).
applying transfer learning in HPO. Wistuba et al. [46] prune the bad              12: end for
regions of search space according to the results from previous tasks.             13: return the configuration with the best observed performance.
This method suffers from the complexity of obtaining meta-features
and relies on some other parameters to construct a GP model. On               X̂ such that it contains a proper set of promising configurations,
that basis, Perrone et al. [33] propose to utilize previous tasks to          which is close to the good region in the original space X.
design a sub-region of the entire search space for the new task.
However, this method ignores the similarity between HPO tasks,                3.2     Bayesian Optimization
and applies a simple low-volume geometrical shape (bounding box
                                                                              Bayesian optimization (BO) works as follows. Since evaluating the
or ellipsoid) to obtain the sub-region that contains the optimal
                                                                              objective function 𝑓 for a given configuration 𝒙 is expensive, it
configurations from all past tasks. Therefore, it may design a loose
                                                                              approximates 𝑓 using a surrogate model 𝑀 : 𝑝 (𝑓 |𝐷) fitted on obsearch space or exclude the best configurations and further lead to
                                                                              servations 𝐷, and this surrogate is much cheaper to evaluate. Given
negative transfer [31]. This work is the most closely related to our
                                                                              a configuration 𝒙, the surrogate model 𝑀 outputs the posterior
method. We want to highlight that the other forms of transfer (e.g.,                                                                        2 (𝒙)). BO
                                                                              predictive distribution at 𝒙, that is, 𝑓 (𝒙) ∼ N (𝜇𝑀 (𝒙), 𝜎𝑀
surrogate-based transfer or warm-starting BO) are orthogonal to
                                                                              methods iterate the following three steps: 1) use surrogate 𝑀 to
the search space transfer methods, and thus these approaches can
                                                                              select a promising configuration 𝒙𝑛 that maximizes the acquisition
be seamlessly combined to pursue better performance of HPO.
                                                                              function 𝒙𝑛 = arg max𝒙 ∈X 𝑎(𝒙; 𝑀), where the acquisition function
                                                                              is to balance the exploration and exploitation trade-off; 2) evaluate
3     PRELIMINARY                                                             this point to get its performance 𝑦𝑛 , and add the new observation
In this section, we first introduce the problem definition and then           (𝒙𝑛 , 𝑦𝑛 ) to 𝐷 = {(𝒙 𝑗 , 𝑦 𝑗 ))}𝑛−1
                                                                                                               𝑗=1 ; 3) refit 𝑀 on the augmented 𝐷.
describe the basic framework of Bayesian optimization.                            Expected Improvement (EI) [18] is a common acquisition func-
                                                                              tion, and it is widely used in the HPO community for its excellent
3.1     HPO over a Reduced Search Space                                       empirical performance. EI is defined as follows:
The HPO of ML algorithms can be modeled as a black-box optimization problem. Given a common hyperparameter space X and                                             ∫ ∞
tuning history from previous HPO tasks, we need to optimize the                             𝑎(𝒙; 𝑀) =         max(𝑦 ∗ − 𝑦, 0)𝑝 𝑀 (𝑦|𝒙)𝑑𝑦,             (3)
current tuning task. The goal of this HPO problem is to find the                                         −∞
best hyperparameter configuration that minimizes the objective
function 𝑓 𝑇 on the target task, which is formulated as follows,              where 𝑀 is the surrogate model and 𝑦 ∗ is the best performance
                                                                              observed in 𝐷, i.e., 𝑦 ∗ = min{𝑦1, ..., 𝑦𝑛 }. By maximizing this EI
                              arg min 𝑓 𝑇 (𝒙),                          (1)   function 𝑎(𝒙; 𝑀) over the hyperparameter space X, BO methods
                                𝒙 ∈X
                                                                              can find a configuration with the largest EI value to evaluate for
where 𝑓 𝑇 (𝒙) is the ML model’s performance metric (e.g., validation          each iteration. Algorithm 1 displays the BO framework. While the
error) corresponding to the configuration 𝒙. Due to the intrinsic             design function does nothing in vanilla BO and returns the original
randomness of most ML algorithms, we evaluate a configuration                 space (Line 8), we aim to design a compact and promising search
𝒙 and can only get its noisy observation 𝑦 = 𝑓 (𝒙) + 𝜖 with 𝜖 ∼               space to accelerate HPO as introduced in Section 3.1.
N (0, 𝜎 2 ). In this work, we consider methods that output a compact
search space X̂ ⊆ X with the aid of tuning history from past tasks.
Instead of Equation 1, we solve the following problem:
                                                                              4     THE PROPOSED METHOD
                                                                              In this section, we introduce a novel search space design method for
                              arg min 𝑓 𝑇 (𝒙).                          (2)   hyperparameter tuning. We first present the notations and overview
                                𝒙 ∈ X̂
                                                                              of our method, then introduce the two critical steps: promising
  While X̂ is much smaller than X, optimization methods may find              region extraction and target search space generation. Finally, we
those optimal configurations faster. Therefore, we aim to design              end this section with an algorithm summary and discussion.

KDD ’22, August 14–18, 2022, Washington, DC, USA                                                                                                             Li et al.

4.1     Notations and Overview                                                            where 𝐹 (𝑀 𝑖 ; 𝐷𝑇 ) is the number of order-preserving pairs, |𝐷𝑇 | is
Our method takes the observations from 𝐾 + 1 tasks as input, in                           the number of target observations, 𝑀 𝑖 (𝑥 𝑗 ) is the predictive mean
which 𝐷 1 , ..., 𝐷 𝐾 are tuning history from 𝐾 source tasks and 𝐷𝑇 is                     of the surrogate 𝑀 𝑖 of the 𝑖-th source task given the configuration
the observations in the target task. The 𝑖-th source task contains 𝑛𝑖                     𝑥 𝑗 , and ⊗ is the exclusive-nor operation, in which the statement
                                                𝑖
evaluated configurations 𝐷 𝑖 = {(𝒙 𝑖𝑗 , 𝑦𝑖𝑗 )}𝑛𝑗=1 . Unlike {𝐷 𝑖 }𝑖=1
                                                                  𝐾 that                  value is true only if the two sub-statements return the same value.
                                                                                               While previous work [33] represents the promising region of a
are obtained in previous tuning procedures, the number of obser-
                                                                                          source task as a single area, we argue that the promising regions
vations in 𝐷𝑇 grows along with the current tuning process. After
                                                                                          in many HPO tasks are non-convex and discontinuous, i.e., there
finishing 𝑡 trials, the target observations are 𝐷𝑇 = {(𝒙𝑇𝑗 , 𝑦𝑇𝑗 )}𝑡𝑗=1 .
                                                                                          may be several local optima, and the final promising region often
   In this work, we consider methods that take the previous obser-
                                                                                          consists of several good areas. In addition, this method restricts the
vations {𝐷 𝑖 }𝑖=1
               𝐾 , and the current observations 𝐷𝑇 as inputs, and out-
                                                                                          region with some geometrical shape, while the shape of promising
put a compact search space X̂ ⊆ X. Our proposed method designs                            regions in most real-world HPO tasks is uncertain and cannot be
the compact search space based on the promising regions obtained                          recognized easily. To represent multiple promising areas along with
from source tasks. The promising region refers to a sub-region of                         uncertain shapes, We turn this problem into a binary classification
the original search space X𝑖 ∈ X where the optimal configurations                         task and employ the Gaussian Process Classifier (GPC) to learn
of the target task 𝒙 ∗ are located with a high probability. Before op-                    the promising region, where the classifier could predict whether
timizing the target task, our approach trains a surrogate model 𝑀 𝑖                       a configuration from the search space belongs to the promising
on observations 𝐷 𝑖 from each source task 𝑖. These surrogate mod-                         region or not. For each source task 𝑖, our method prepares the
els can be fitted in advance. To utilize the promising regions and                                                 |𝐷 𝑖 |
                                                                                          training data {𝒙𝒋𝒊 , 𝑏𝑖𝑗 } 𝑗=1 as follows,
address the challenges in Section 1, our search space design method
includes two critical steps: (1) promising region extraction and
                                                                                                                        (
                                                                                                                   𝑖       1      if 𝑦𝑖𝑗 < 𝑦𝑖+
(2) target search space generation. In short, our approach applies                                               𝑏𝑗 =                          ,                 (5)
                                                                                                                           0      if 𝑦𝑖𝑗 ≥ 𝑦𝑖+
the machine learning algorithm to represent complex promising
regions and adopt a ranking-based method to measure the task cor-                         where 𝑦𝑖+ is determined by some quantile 𝛼 𝑖 of performance values
relation. Then, it generates the target search space via a sampling                       in 𝐷 𝑖 , so that the cumulative distribution function 𝑃 (𝑦 < 𝑦𝑖+ ) = 𝛼 𝑖 .
framework along with the voting mechanism. We will introduce                              Inspired by the aforementioned intuition, we further propose to
the details in Sections 4.2 and 4.3, respectively.                                        control the size of promising regions by adjusting 𝛼 𝑖 based on task
                                                                                          similarity, and the adjustment rule is given by,
4.2     Promising Region Extraction                                                         𝛼 𝑖 = 𝛼𝑚𝑖𝑛 + (1 − 2 ∗ max(𝑆 (𝑀 𝑖 ; 𝐷𝑇 ) − 0.5, 0)) ∗ (𝛼𝑚𝑎𝑥 − 𝛼𝑚𝑖𝑛 ), (6)
To transfer the search spaces, our method first extracts a promising
                                                                                          where 𝛼𝑚𝑎𝑥 and 𝛼𝑚𝑖𝑛 are two parameters set close to 1 and 0
region X𝑖 from each source task 𝑖. A simple intuition is that when
                                                                                          respectively, which control the aggressiveness of the quantile 𝛼 𝑖 .
the target task is quite similar to a source task, the region of the
                                                                                          The two parameters ensure that in the worst case, when the source
best configurations are similar in both two tasks, so we have high
                                                                                          task is adverse to the target task, the size of the promising region
confidence to extract a relatively small region that the optimal con-
                                                                                          will be determined by 𝛼𝑚𝑎𝑥 , i.e., the original complete search space.
figuration of the target task locates in that region. On the contrary,
                                                                                          When a source task is quite similar to the target task, the value
when the target task is quite different from a source task, we are not
                                                                                          of 𝛼 𝑖 approaches 𝛼𝑚𝑖𝑛 so that a compact promising region can be
sure whether the best configurations of the source task work well
                                                                                          extracted. Finally, our method fits a GPC model 𝐺 𝑖 on the training
on the target task, so we need a conservatively large enough space
                                                                                          data in Eq. 5 for each source task and extracts the promising region
to contain a sufficient number of candidate configurations. There-
                                                                                          as X𝑖 = {𝒙 𝑗 |𝒙 𝑗 ∈ X, 𝐺 𝑖 (𝒙 𝑗 ) = 1}. Note that, due to the growing
fore, the size of the promising region should be carefully crafted.
In the following, we will answer two questions: 1) how to define                          observation 𝐷𝑇 during the HPO process, the promising region from
the similarity between the source tasks and the target task, and 2)                       each source task adaptively adjusts based on the task similarity.
how to extract the promising region with a proper size according
to the similarity.                                                                        4.3    Target Search Space Generation
   We measure the task similarity between source tasks and the tar-                       While each source task holds a promising region, the next step is
get task on the target observations 𝐷𝑇 via a ranking-based method.                        to combine those regions and generate the search space for the
In HPO, the ranking is more reasonable than the actual performance                        current iteration. An intuitive way is to select the most similar task
value of each configuration, and we care about the partial orderings                      and directly apply its promising region as the target space, that is:
over configurations, i.e., the region of promising configurations.                                         X̂ = X𝑚 ,        𝑚 = arg max 𝑆 (𝑀 𝑖 ; 𝐷𝑇 ).           (7)
Therefore, we apply the ratio of order-preserving pairs to measure                                                               𝑖=1,...,𝑘
this similarity. The definition of the similarity 𝑆 (𝑀 𝑖 ; 𝐷𝑇 ) between                      However, since the search process could easily be trapped in a
the 𝑖-th source task and the target task is given by,                                     sub-optimal local region provided by the most similar source task,
                      𝐷𝑇   𝐷𝑇                                                             this design may lead to the over-exploitation issue over the whole
           𝑖   𝑇
                      ∑︁ ∑︁                                                          search space. Another alternative is to sample a task according to
      𝐹 (𝑀 ; 𝐷 ) =                  1        𝑀 𝑖 (𝒙 𝑗 ) < 𝑀 𝑖 (𝒙𝑘 ) ⊗ 𝑦 𝑗 < 𝑦𝑘
                                                                                    (4)   the similarity and then apply its promising region, that is:
                      𝑗 =1 𝑘=𝑗 +1

      𝑆 (𝑀 𝑖 ; 𝐷𝑇 ) = 2𝐹 (𝑀 𝑖 ; 𝐷𝑇 )/( |𝐷𝑇 | ∗ ( |𝐷𝑇 | − 1)),                                                       X̂ = X𝑚 ,      𝑚 ∼ P(.),                     (8)

Transfer Learning based Search Space Design for Hyperparameter Tuning                                     KDD ’22, August 14–18, 2022, Washington, DC, USA

 Algorithm 2: Pseudo code for design function.                                  both two types of hyperparameters. In addition, the computational
                                                                                complexity of design function is 𝑂 (𝐾𝑛 3 ), where 𝐾 is the number of
   Input: the surrogates in 𝐾 source tasks: 𝑀 𝑖 with 𝑖 ∈ [1, 𝐾 ], the target
        observation 𝐷𝑇 , the configuration space X.
                                                                                source tasks and 𝑛 is the number of observations in each source task;
      1: compute the similarity 𝑆 (𝑀 𝑖 ; 𝐷𝑇 ) between each source task 𝑖 and    due to the linear growth on 𝐾, the proposed method could scale
        the target task based on Eq. 4.                                         to a large number of source tasks 𝐾 (good scalability). 3. Safeness.
      2: Train a Gaussian Process Classifier 𝐺 𝑖 for each source task 𝑖 based   The design of our method ensures that dissimilar source tasks may
        on training data from Eq. 5.                                            influence little to the final search space. Those tasks have little
      3: Sample 𝑘 source tasks 𝑠 1 , ..., 𝑠𝑘 based on similarity distribution   chance to be sampled due to their low similarity. And once they
        from Eq. 8.                                                             are sampled by coincidence, Eq. 6 returns a large percentile, so that
      4: Generate the target search space X̂ based on the voting results of     their promising regions will be relatively large, and the optimal
        𝐺 𝑠𝑖 with 𝑖 ∈ [1, 𝑘 ].                                                  configuration of the target task is less likely to be excluded.
      5: return the final search space X̂.

where P(.) is the distribution computed based on the similarity,                5     EXPERIMENTS AND RESULTS
                                                                                In this section, we evaluate the superiority of our approach from
                                   Í𝐾
in which 𝑝 (𝑖) = 𝑆 (𝑀 𝑖 ; 𝐷𝑇 )/ 𝑖=1     𝑆 (𝑀 𝑖 ; 𝐷𝑇 ). Though sampling enables exploration on different promising regions, the source infor-             two perspectives: 1) the empirical performance compared with the
mation is not fully utilized in optimization, i.e., the information             existing search space design method on a wide range of benchmarks,
from only one source task is used in this design of space.                      2) its advantages in terms of universality, practicality and safeness.
   To encourage exploration and utilize more source tasks, our
method adopts a sampling framework along with the voting mech-                  5.1    Experimental Settings
anism. It first samples 𝑘 source tasks out of 𝐾 tasks without replace-          Benchmarks. We evaluate the performance of our proposed
ment. Concretely, we scale the sum of similarity to 1 and sample                method on three benchmarks: (1) Random Forest Tuning Benchsource tasks based on this distribution. We denote the sampled                  mark: The benchmark tunes 5 Random Forest hyperparameters
tasks as 𝑠 1, ..., 𝑠𝑘 . Then, for each configuration 𝒙 ∈ X, our method          on 20 OpenML datasets [45]. Each task contains the evaluation
builds a voting ensemble 𝐺ˆ of 𝑘 GPC models as,                                 results of 50k randomly chosen configurations. (2) ResNet Tun-
                          
                            1
                                       Í
                                    if 𝑘𝑖=1 𝐺 𝑠𝑖 (𝒙 𝑗 ) ≥ ⌊ 𝑘2 ⌋                ing Benchmark: The benchmark tunes 5 ResNet hyperparameters
              𝐺ˆ (𝒙 𝑗 ) =                                        .     (9)      on CIFAR-10, SVHN and Tiny-ImageNet. Each task contains the
                            0       else
                                                                                evaluation results of 1500 randomly chosen configurations and 500
   When a configuration is in the majority of promising regions                 configurations selected by running a GP-based BO. (3) NAS-Benchof the sampled tasks, it is regarded as a feasible configuration in             201 [6]: This is a light-weight benchmark with 6 hyperparameters
the compact search space. The final target space is formulated as               for neural architecture search, which includes the statistics of 15,625
X̂ = {𝒙 𝑗 |𝒙 𝑗 ∈ X, 𝐺ˆ (𝒙 𝑗 ) = 1}.                                             CNN models on three datasets – CIFAR-10-valid, CIFAR-100, and
                                                                                ImageNet16-120.
4.4      Algorithm Summary                                                          The hyperparameter space in the Random Forest Tuning Bench-
Algorithm 2 gives the pseudo code of the design function. The func-             mark follows the implementation in Auto-Sklearn [8], while the
tion is called during each iteration as shown in Algorithm 1. We first          space in ResNet Tuning Benchmark follows the previous work [25].
extract a promising region for each source task by computing the                It takes more than 50k CPU hours and 5k GPU hours to collect
similarity based on Equation 4 (Line 1) and training the GPC model              the first two benchmarks. More details about the search space and
based on the generated training data given by Equation 5 (Line 2).              datasets of the benchmarks are provided in Appendix A.1.
Then, we sample source tasks based on the similarity distribution               Search Space Baselines. As a search space design method, we
(Line 3) and generate the target search space by combining those                compare our proposed method with other three search space design:
promising regions via the voting results of GPC models (Line 4).                (1) No design: using the original search space without any space
                                                                                extraction; (2) Box [33]: search space design by representing the
4.5      Discussion                                                             promising region as a low-volume bounding box; (3) Ellipsoid [33]:
To our knowledge, our method is the first method that owns the                  search space design by representing the promising region as a lowfollowing desirable properties simultaneously in the field of search            volume ellipsoid. Based on the search space design methods, we
space design. 1. Universality. Our method focuses on accelerating               further evaluate random search and GP-based BO [41] on the com-
HPO by designing promising and compact search space on the                      pact (or original) search space. In addition, for neural architecture
target task, so it is orthogonal to and compatible with other forms of          search, we also evaluate the regularized evolutionary algorithm
transfer methods and a wide range of BO methods. In other words, it             (REA) [37] and SMAC [16], which are state-of-the-art methods on
can be easily combined into those methods by integrating the design             neural architecture search. Note that, since our method enjoys high
function as in Algorithm 1. 2. Practicality. A practical search space           universality, it can be easily implemented into those methods. We
design method should support different types of hyperparameters,                will further illustrate the performance of the combination of space
including both numerical and categorical ones. While previous                   transfer and surrogate transfer methods in Section 5.5.
work [33] only works on numerical hyperparameters due to the                    Basic Settings. To evaluate the performance of the considered
use of geometrical shapes, our method employs an ML model to                    transfer learning method, the experiments are performed in a leavelearn promising regions with uncertain shapes, so that it supports              one-out fashion, i.e., each method optimizes the hyperparameters of

KDD ’22, August 14–18, 2022, Washington, DC, USA                                                                                                                                                                                                                                                                                                       Li et al.

                                                  Random                        GP                         0.175
                                                                                                                                            Random                        GP                          0.14                      Random                    GP                          0.14                      Random                    GP
                                                  Box + Random                  Box + GP                                                    Box + Random                  Box + GP                                              Box + Random              Box + GP                                              Box + Random              Box + GP
          0.20
                                                  Ellipsoid + Random            Ellipsoid + GP             0.150                            Ellipsoid + Random            Ellipsoid + GP              0.12                      Ellipsoid + Random        Ellipsoid + GP              0.12                      Ellipsoid + Random        Ellipsoid + GP
                                                  Ours + Random                 Ours + GP                                                   Ours + Random                 Ours + GP                   0.10                      Ours + Random             Ours + GP                   0.10                      Ours + Random             Ours + GP
                                                                                                           0.125
          0.15

    NCE                                                                                              NCE                                                                                        NCE                                                                             NCE
                                                                                                           0.100                                                                                      0.08                                                                            0.08

          0.10                                                                                             0.075                                                                                      0.06                                                                            0.06

                                                                                                           0.050                                                                                      0.04                                                                            0.04
          0.05
                                                                                                           0.025                                                                                      0.02                                                                            0.02

          0.00                                                                                             0.000                                                                                      0.00                                                                            0.00
                 0   5        10          15         20       25    30   35     40        45    50                 0        5    10    15        20        25   30   35    40     45       50                0   5   10   15       20    25    30    35    40     45       50                0   5   10   15       20    25    30    35    40     45       50
                                               Number of Trials                                                                             Number of Trials                                                                   Number of Trials                                                                Number of Trials

                         (a) winequality_white                                                                                  (b) page-blocks(2)                                                                              (c) sick                                                                       (d) musk

         0.12                                                                                                                                                                                         0.12                                                                            0.18
                                                  Random                       GP                                                           Random                        GP                                                   Random                     GP                                                   Random                     GP
                                                                                                           0.200
         0.11                                     Box + Random                 Box + GP                                                     Box + Random                  Box + GP                                             Box + Random               Box + GP                    0.16                     Box + Random               Box + GP
                                                  Ellipsoid + Random           Ellipsoid + GP              0.175                            Ellipsoid + Random            Ellipsoid + GP              0.10                     Ellipsoid + Random         Ellipsoid + GP                                       Ellipsoid + Random         Ellipsoid + GP
         0.10                                                                                                                               Ours + Random                 Ours + GP                                                                                                   0.14
                                                  Ours + Random                Ours + GP                                                                                                                                       Ours + Random              Ours + GP                                            Ours + Random              Ours + GP
                                                                                                           0.150
         0.09                                                                                                                                                                                         0.08                                                                            0.12

   NCE                                                                                               NCE                                                                                        NCE                                                                             NCE
                                                                                                           0.125
         0.08                                                                                                                                                                                                                                                                         0.10
                                                                                                           0.100                                                                                      0.06
         0.07                                                                                                                                                                                                                                                                         0.08
                                                                                                           0.075
         0.06                                                                                                                                                                                                                                                                         0.06
                                                                                                                                                                                                      0.04
                                                                                                           0.050
         0.05                                                                                                                                                                                                                                                                         0.04

         0.04                                                                                              0.025                                                                                      0.02                                                                            0.02
                0    5      10           15          20       25   30    35     40        45   50                  0        5    10    15        20    25       30   35    40    45    50                    0   5   10   15      20    25     30    35   40     45    50                    0   5   10   15      20    25     30    35   40     45      50
                                              Number of Trials                                                                          Number of Trials                                                                       Number of Trials                                                                Number of Trials

                                       (e) puma8NH                                                                                    (f) satimage                                                                        (g) segment                                                                     (h) cpu_act

                             Figure 2: Normalized classification error (NCE) when tuning Random Forest on eight OpenML datasets.
                                       0.20
                                                                               Random                                           GP
                                       0.18                                    Box + Random                                     Box + GP
                                                                                                                                                                                                        Implementations & Parameters. Please refer to Appendix A.2
                                                                               Ellipsoid + Random                               Ellipsoid + GP                                                          for more details about implementations and reproductions.

                         Average NCE
                                       0.16
                                                                               Ours + Random                                    Ours + GP
                                       0.14

                                       0.12

                                       0.10
                                                                                                                                                                                                        5.2           Tuning Random Forest on OpenML Tasks
                                       0.08                                                                                                                                                             We first compare our method with other space design methods on
                                       0.06
                                                                                                                                                                                                        the Random Forest Tuning Benchmark. Concretely, each task is se-
                                              0           5        10     15         20
                                                                               Number of Trials
                                                                                               25      30              35       40          45        50
                                                                                                                                                                                                        lected as the target task in turn, and the remaining 19 tasks are the
                                                                                                                                                                                                        source tasks. The search space from each space method is further
Figure 3: Average NCE results when tuning random forest                                                                                                                                                 evaluated by random search and GP-based BO. Figure 2 demonacross 20 OpenML tasks.                                                                                                                                                                                 strates the normalized classification error (NCE) on 8 datasets and
a specific task while treating the remaining tasks as the source tasks.                                                                                                                                 Figure 3 shows the aggregated NCE on the entire benchmark. We
We follow the experiment settings as in previous work [9, 47]. Con-                                                                                                                                     observe that the Ellipsoid variants perform better than the Box and
cretely, only 𝑁𝑆 observations in the source tasks (here 𝑁𝑆 = 100)                                                                                                                                       non-transfer counterparts on most tasks in terms of convergence
are used to extract promising region. In addition, following [9], all                                                                                                                                   speed. However, on musk and satimage, GP works slightly better
the compared methods are initialized with three randomly selected                                                                                                                                       than Ellipsoid GP and Box GP. As we have explained in Section 1,
configurations, and then a total of 𝑁𝑇 = 50 configuration evalua-                                                                                                                                       the reason is that the two space design methods ignore the reletions are conducted sequentially to evaluate the effectiveness of the                                                                                                                                   vancy between tasks, which hampers the performance of transfer
transfer learning based space design given limited trials. To avoid                                                                                                                                     learning when using dissimilar source tasks. Generally, the variants
the effect of randomness, each method is repeated 20 times and the                                                                                                                                      of our method consistently outperform their counterparts. The NCE
mean performance metrics are reported along with their variance.                                                                                                                                        of Ours + GP rapidly drops in the first 25 iterations, and achieves
Evaluation Metrics. For each task, the objective of HPO is to find                                                                                                                                      the best final performance on 7 out of 8 tasks. In addition, we find
the configuration with the lowest validation error. However, since                                                                                                                                      that random search equipped with our method outperforms the Box
the classification error is not commensurable across the benchmark                                                                                                                                      and Ellipsoid variants on sick and musk, which further indicates
datasets, we follow the previous work [1, 9, 47] and report the                                                                                                                                         the superiority of our space design method.
Normalized Classification Error (NCE) of all compared baselines
on each dataset. The NCE after 𝑡 trials is defined as:                                                                                                                                                  5.3           Tuning ResNet on Vision Problems
                                                                                                                                                                                                        In this part, we evaluate our method on a deep learning algorithm
                                                                𝑚𝑖𝑛𝒙 ∈X𝑡 𝑦𝒙𝑖 − 𝑦𝑚𝑖𝑛
                                                                                𝑖
                                                   𝑁𝐶𝐸 (X𝑡𝑖 ) =                     ,                                                                                      (10)                         tuning problem. Figure 4 shows the compared results on three
                                                                   𝑖
                                                                  𝑦𝑚𝑎𝑥       𝑖
                                                                         − 𝑦𝑚𝑖𝑛                                                                                                                         vision problems. On CIFAR-10 and SVHN, we observe that GP and
                                                                                                                                                                                                        Ellipsoid + GP show almost the same performance. The reason is
         𝑖
where 𝑦𝑚𝑖𝑛          𝑖
              and 𝑦𝑚𝑎𝑥  are the best and worst ground-truth perfor-                                                                                                                                     that, the optimal configuration of Tiny-ImageNet is quite far away
mance value (i.e., classification error) on the 𝑖-th task, 𝑦𝒙𝑖 corre-                                                                                                                                   from the other two tasks. And then, it will take a large ellipsoid to
sponds to the performance of configuration 𝒙 in the 𝑖-th task, and                                                                                                                                      cover the optimal configurations of all source tasks, which is almost
X𝑡𝑖 is the set of hyperparameter configurations on the 𝑖-th task that                                                                                                                                   the same as the original space. In this case, they achieve almost
have been evaluated in the previous 𝑡 trials. To measure a method                                                                                                                                       the same performance due to the same underlying search space.
on the entire benchmark, we average the NCE over all considered                                                                                                                                         Similar to the Random Forest Tuning Benchmark, the variants of
tasks, that is, 𝐾1 𝑖=1
                   Í𝐾
                        𝑁𝐶𝐸 (X𝑡𝑖 ), where 𝐾 is the number of tasks.                                                                                                                                     our method consistently outperforms the compared counterparts.

Transfer Learning based Search Space Design for Hyperparameter Tuning                                                                                                                                                                                                       KDD ’22, August 14–18, 2022, Washington, DC, USA

                                         0.020
                                                                                  Random                                       GP                             0.006                                    Random                     GP                                                                Random                               GP
                                                                                                                                                                                                                                                             0.07
                                         0.018                                    Box + Random                                 Box + GP                                                                Box + Random               Box + GP                                                          Box + Random                         Box + GP
                                                                                  Ellipsoid + Random                           Ellipsoid + GP                 0.005                                    Ellipsoid + Random         Ellipsoid + GP                                                    Ellipsoid + Random                   Ellipsoid + GP
                                                                                                                                                                                                                                                             0.06
                                         0.016                                                                                                                                                         Ours + Random              Ours + GP                                                         Ours + Random                        Ours + GP
                                                                                  Ours + Random                                Ours + GP
                                         0.014                                                                                                                0.004                                                                                          0.05

                                   NCE   0.012                                                                                                          NCE   0.003
                                                                                                                                                                                                                                                       NCE
                                                                                                                                                                                                                                                             0.04
                                         0.010
                                                                                                                                                              0.002                                                                                          0.03
                                         0.008
                                                                                                                                                              0.001                                                                                          0.02
                                         0.006

                                                 0        5        10        15      20    25            30             35      40       45      50                      0   5         10         15      20    25     30   35     40      45    50                 0   5        10        15              20         25    30    35     40       45        50
                                                                              Number of Trials                                                                                                     Number of Trials                                                                             Number of Trials

                                                                             (a) CIFAR-10                                                                                                          (b) SVHN                                                                           (c) Tiny-ImageNet

                                                     Figure 4: Normalized classification error (NCE) when tuning ResNet on three vision problems.
                                         0.025                                                                                                                0.06                                                                                           0.08
                                                                                           Random                            Ours + Random                                                                      Random           Ours + Random                                                                       Random            Ours + Random
                                                                                           GP                                Ours + GP                                                                          GP               Ours + GP                   0.07                                                    GP                Ours + GP
                                                                                                                                                              0.05
                                         0.020                                             SMAC                              Ours + SMAC                                                                        SMAC             Ours + SMAC                                                                         SMAC              Ours + SMAC
                                                                                                                                                                                                                                                             0.06
                                                                                           REA                                                                                                                  REA                                                                                                  REA
                                                                                                                                                              0.04
                                         0.015                                                                                                                                                                                                               0.05

                                   NCE                                                                                                                  NCE   0.03
                                                                                                                                                                                                                                                       NCE   0.04
                                         0.010                                                                                                                                                                                                               0.03
                                                                                                                                                              0.02
                                                                                                                                                                                                                                                             0.02
                                         0.005
                                                                                                                                                              0.01
                                                                                                                                                                                                                                                             0.01

                                         0.000                                                                                                                0.00                                                                                           0.00
                                                 0        5        10        15      20    25            30             35      40       45      50                  0       5         10     15          20    25    30    35    40       45    50                 0   5        10        15              20         25    30    35     40       45        50
                                                                              Number of Trials                                                                                                     Number of Trials                                                                             Number of Trials

                                                                             (a) CIFAR-10                                                                                                    (b) CIFAR-100                                                                        (c) ImageNet16-120

                                    Figure 5: Normalized classification error (NCE) when conducting architecture search on NASBench201.

                                                                                                              0.12
                                                      Random                 GP                                                                                                  GP
               0.18                                   RGPE                   Ours + RGPE                                                                                         Box + GP
                                                      TST                    Ours + TST                       0.10
               0.16                                                                                                                                                              Ellipsoid + GP

 Average NCE                                                                                    Average NCE
                                                                                                                                                                                 Ours + GP

                                                                                                                                                                                                                                                                                            Max Features                                                                   Max Features
               0.14                                                                                           0.08
                                                                                                                                                                                                                                                                                        1.0                                                                             1.0
                                                                                                                                                                                                                                                                                        0.8                                                                             0.8
               0.12
                                                                                                              0.06                                                                                                                                                                     0.6                                                                             0.6
               0.10                                                                                                                                                                                                                                                                    0.4                                                                             0.4
                                                                                                              0.04                                                                                                                                                                     0.2                                                                             0.2
               0.08
                                                                                                                                                                                                                                                                                       0.0                                                                             0.0
               0.06                                                                                           0.02
                                                                                                                                                                                                                                                                                      20
                                                                                                                                                                                                                                                                                       lit                                                                            20
                                                                                                                                                                                                                                                                                                                                                                       lit
                      0   5   10    15      20       25       30        35    40     45   50                         0 10 20 30 40 50 60 70 80 90 100 110 120 130 140 150 160 170 180 190 200                                                                                         Sp                                                                              Sp
                                         Number of Trials                                                                                     Number of Trials                                                                                                                   15                                                                              15
                                                                                                                                                                                                                                 20                                              es                             20                                               es
                                                                                                                                                                                                                                      Min15                                 10   pl
                                                                                                                                                                                                                                                                                                                     Min15                                 10    pl
                                                                                                                                                                                                                                          Sam 10                             m                                           Sam 10                             m
                               (a) Universality                                                                                         (b) Safeness                                                                                         ples
                                                                                                                                                                                                                                                  Lea5
                                                                                                                                                                                                                                                                        5
                                                                                                                                                                                                                                                                        in
                                                                                                                                                                                                                                                                            Sa                                              ples
                                                                                                                                                                                                                                                                                                                                 Lea5
                                                                                                                                                                                                                                                                                                                                                       5
                                                                                                                                                                                                                                                                                                                                                       in
                                                                                                                                                                                                                                                                                                                                                           Sa
                                                                                                                                                                                                                                                      f         0       M                                                            f        0    M

      Figure 6: Case study on universality and safeness.                                                                                                                                                                                              (a) musk                                                                   (b) cpu_act
Remarkably, both two variants of our method reduces the NCE of
the second-best baseline Box + GP by 23.7% on Tiny-ImageNet.                                                                                                                                                         Figure 7: Candidate points in our compact search space. The
                                                                                                                                                                                                                     blue points refer to candidates and the red points refer to
                                                                                                                                                                                                                     the global optimum.
5.4                       Architecture Search on NASBench201
                                                                                                                                                                                                                     as the optimization algorithms. Figure 6(a) demonstrates the opti-
Different from the above two benchmarks, the search space for
                                                                                                                                                                                                                     mization results with and without the surrogate transfer methods
neural architecture search (NAS) contains only categorical hyper-
                                                                                                                                                                                                                     on Random Forest Tuning Benchmark. We observe that the surparameters. In this case, the Box and Ellipsoid variants fail to find
                                                                                                                                                                                                                     rogate transfer methods alone (RGPE and TST) indeed accelerate
a compact space and revert to standard search space since there
                                                                                                                                                                                                                     hyperparameter optimization. When our method is implemented,
is no partial order between different values of categorical hyper-
                                                                                                                                                                                                                     the performance is further improved. Concretely, our method reparameters. Therefore, we compare our method with non-transfer
                                                                                                                                                                                                                     duces the aggregated NCE of RGPE and TST by 10.1% and 22.6% on
counterparts. To further demonstrate we extend the optimization
                                                                                                                                                                                                                     the entire benchmark, respectively.
algorithms with state-of-the-art SMAC and REA and the results
                                                                                                                                                                                                                        In addition, we set the number of trials to 200 showcase the
are shown in Figure 5. On all the three datasets, we observe that
                                                                                                                                                                                                                     convergence results of compared methods given a larger budget
the variants of our method consistently performs better than non-
                                                                                                                                                                                                                     (Safeness). Figure 6(b) shows that our method still outperforms the
transfer counterparts. When we equip the state-of-the-art method
                                                                                                                                                                                                                     compared GP variants. Concretely, our method with GP reduces the
SMAC with our method, we observe a significant reduction on NCE,
                                                                                                                                                                                                                     aggregated NCE by 36.0% compared with the second-best baseline
which indicates that our method enjoys practicality on different
                                                                                                                                                                                                                     Ellipsoid + GP on the Random Forest Tuning Benchmark.
types of HPO tasks.

                                                                                                                                                                                                                     5.6         Case Study on Search Space Design
5.5                       Case Study on Universality and Safeness                                                                                                                                                    In this subsection, we visualize the compact search space given by
In this subsection, we investigate the advantages of our proposed                                                                                                                                                    our method on two datasets in Random Forest Tuning Benchmark.
method. We first study how our method works with existing sur-                                                                                                                                                       Figure 7 plots the candidate points in our compact search space
rogate transfer methods (Universality) on Random Forest Tuning                                                                                                                                                       during the 50-th iteration on two datasets. Rather than optimizing
Benchmark. We choose the state-of-the-art RGPE [9] and TST [47]                                                                                                                                                      over the entire cube, our method generates a significantly smaller

KDD ’22, August 14–18, 2022, Washington, DC, USA                                                                                                                                                                                                                                                                                         Li et al.

                                           Source 1: satimage                              Source 2: page-blocks(2)                                Target: segment
                                 1.0                                             1.0                                                  1.0                                                    0.20                                                                                 0.20
                                                                                                                                                                                                                                              Ours(KNN) + GP                                                Random                  GP
                                                                                                                                                                                             0.18                                             Ours(SVM) + GP                      0.18                      Ours-v1 + Random        Ours-v1 + GP
                                                                                                                                                                                                                                                                                                            Ours-v2 + Random        Ours-v2 + GP
                                 0.8                                             0.8                                                  0.8                                                                                                     Ours(RF) + GP

                                                                                                                                                                               Average NCE                                                                          Average NCE
                                                                                                                                                                                             0.16                                                                                 0.16
                                                                                                                                                                                                                                              Ours(GPC) + GP                                                Ours-v3 + Random        Ours-v3 + GP

5-th Iteration    Max Features                                    Max Features                                         Max Features
                                                                                                                                                                                             0.14                                                                                 0.14
                                 0.6                                             0.6                                                  0.6                                                    0.12                                                                                 0.12

                                                                                                                                                                                             0.10                                                                                 0.10
                                 0.4                                             0.4                                                  0.4                                                    0.08                                                                                 0.08

                                                                                                                                                                                             0.06                                                                                 0.06
                                 0.2                                             0.2                                                  0.2
                                                                                                                                                                                                    0   5   10   15      20   25   30    35      40    45      50                        0   5   10   15      20   25    30    35   40     45   50
                                                                                                                                                                                                                      Number of Trials                                                                     Number of Trials
                                 0.0                                             0.0                                                  0.0
                                       2   11   21    31    41   51                    2     11    21   31    41      51                    2    11    21   31    41     51
                                            Min Samples Split                                 Min Samples Split                                   Min Samples Split                             (a) Promising Region Extraction                                               (b) Target Search Space Generation
                                 1.0                                             1.0                                                  1.0

                                 0.8                                             0.8                                                  0.8

40-th Iteration
                                                                                                                                                                              Figure 10: Ablation study on promising region extraction

                  Max Features                                    Max Features                                         Max Features
                                 0.6                                             0.6                                                  0.6
                                                                                                                                                                              and target search space generation.
                                 0.4                                             0.4                                                  0.4
                                                                                                                                                                              iteration. However, as shown in Figure 9 where the target task is
                                 0.2                                             0.2                                                  0.2
                                                                                                                                                                              quite dissimilar to both source tasks, the compact search space
                                 0.0
                                       2   11   21    31    41   51
                                                                                 0.0
                                                                                       2     11    21   31    41      51
                                                                                                                                      0.0
                                                                                                                                            2    11    21   31    41     51   does not include the global optimum in the very beginning. In this
                                            Min Samples Split                                 Min Samples Split                                   Min Samples Split
                                                                                                                                                                              case, the promising region of source tasks gradually expand due to
                                                                                                                                                                              the dynamic quantile introduced in Section 4.2, and it eventually
Figure 8: Promising regions for source tasks and target
                                                                                                                                                                              covers the global optimum in the 40-th iteration while still excludsearch space when tuning on segment. The blue area refers
                                                                                                                                                                              ing the bad regions as shown in Figure 1. This demonstrates the
to the promising regions of the source tasks and the search
                                                                                                                                                                              effectiveness and safeness of our approach.
space of the target task from our method. The red point is
the ground-truth optimum on that task, and the red box is
the search space generated by the baseline Box.                                                                                                                               5.7                       Ablation Study
                                                                                                                                                                              Finally, we provide ablation study on the machine learning clas-
                                           Source 1: satimage                                 Source 2: segment                                 Target: page-blocks(2)        sifiers used in promising region extraction and the target search
                                 1.0                                             1.0                                                  1.0
                                                                                                                                                                              space generation strategy on Random Forset Tuning Benchmark.
                                 0.8                                             0.8                                                  0.8
                                                                                                                                                                              For classifiers, we compare the influence of different machine learn-

5-th Iteration    Max Features                                    Max Features                                         Max Features
                                 0.6                                             0.6                                                  0.6                                     ing algorithms used in promising region extraction. Figure 10(a)
                                 0.4                                             0.4                                                  0.4                                     shows the results of four algorithm choices: KNN, LibSVM, Ran-
                                 0.2                                             0.2                                                  0.2                                     dom Forest (RF), and Gaussian Process Classifier (GPC) used in
                                 0.0                                             0.0                                                  0.0
                                                                                                                                                                              our method. Among the choices, the GPC shows the most stable
                                       2   11   21    31    41   51                    2     11    21   31    41      51                    2    11    21   31    41     51
                                            Min Samples Split                                 Min Samples Split                                   Min Samples Split           performance across all tasks. Moreover, GPC is a the only choice
                                 1.0                                             1.0                                                  1.0
                                                                                                                                                                              without algorithm hyperparameters, and thus we employ it as the
                                 0.8                                             0.8                                                  0.8
                                                                                                                                                                              classifier for promising region extraction.

40-th Iteration   Max Features                                    Max Features                                         Max Features
                                 0.6                                             0.6                                                  0.6                                         In addition, we denote 1) OURS-v1 as using the promising region
                                 0.4                                             0.4                                                  0.4                                     of the most similar source task; 2) OURS-v2 as using the promising
                                 0.2                                             0.2                                                  0.2
                                                                                                                                                                              region of a sampled source task; 3) OURS-v3 as our method. Fig-
                                                                                                                                                                              ure 10(b) demonstrates the results of those three variants. Among
                                 0.0                                             0.0                                                  0.0
                                       2   11   21    31    41
                                            Min Samples Split
                                                                 51                    2     11    21   31    41
                                                                                              Min Samples Split
                                                                                                                      51                    2    11    21   31    41
                                                                                                                                                  Min Samples Split
                                                                                                                                                                         51
                                                                                                                                                                              the three methods, OURS-v3 performs the best while OURS-v1 and
                                                                                                                                                                              OURS-v2 perform worse that it. As we have explained in Section 4.3,
Figure 9: Promising regions for source tasks and target                                                                                                                       the reason is that OURS-v1 may be trapped in a sub-optimal region
search space when tuning on page-blocks(2).                                                                                                                                   provided by the most similar task, and OURS-v2 can not leverage
search space, which still contains the global optimum (the red                                                                                                                the information from various source tasks.
point in Figure 7). Remarkably, the size of search space designed
by our method is 375 and 1904 on musk and cpu_act, respectively.                                                                                                              6                     CONCLUSION
Compared with the original search space with 50000 candidates,                                                                                                                We presented a novel approach to incorporate transfer learning in
the size of space shrinks to 0.75% and 3.81% of the original search                                                                                                           BO. Rather than designing a specialized multi-task surrogate model,
space on the two tasks.                                                                                                                                                       our method automatically crafts promising search spaces based on
   In addition, we also plot the promising regions (source tasks)                                                                                                             previously tuning tasks. The extensive experiments on a wide range
and the compact search space (target task) in Figures 8 and 9 us-                                                                                                             of tuning tasks demonstrate that our approach could significantly
ing three datasets from Figure 1. Recall that satimage is similar to                                                                                                          speed up the HPO process and enjoy desirable properties.
segment, but different from page-blocks(2). The red bounding box
refers to the search space from the baseline Box. As it only depends
on the best configurations found in the source tasks, we observe                                                                                                              ACKNOWLEDGMENTS
that neither of the two search spaces from Box covers the global                                                                                                              This work was supported by the National Natural Science Founoptimum (the red point in Figures 8 and 9) on the target task. In                                                                                                             dation of China (No.61832001), Beijing Academy of Artificial In-
Figure 8 where the first source task is similar to target task, our                                                                                                           telligence (BAAI), PKU-Tencent Joint Research Lab. Bin Cui is the
compact search space covers the global optimum since the 5-th                                                                                                                 corresponding author.

Transfer Learning based Search Space Design for Hyperparameter Tuning                                                     KDD ’22, August 14–18, 2022, Washington, DC, USA

REFERENCES                                                                                [26] Yang Li, Yu Shen, Wentao Zhang, Yuanwei Chen, Huaijun Jiang, Mingchao
 [1] Rémi Bardenet, Mátyás Brendel, Balázs Kégl, and Michele Sebag. 2013. Collabo-             Liu, Jiawei Jiang, Jinyang Gao, Wentao Wu, Zhi Yang, et al. 2021. Openbox:
     rative hyperparameter tuning. In International Conference on Machine Learning.            A generalized black-box optimization service. In Proceedings of the 27th ACM
     199–207.                                                                                  SIGKDD Conference on Knowledge Discovery & Data Mining. 3209–3219.
 [2] James Bergstra and Yoshua Bengio. 2012. Random search for hyper-parameter            [27] Yang Li, Yu Shen, Wentao Zhang, Jiawei Jiang, Bolin Ding, Yaliang Li, Jingren
     optimization. Journal of Machine Learning Research 13, Feb (2012), 281–305.               Zhou, Zhi Yang, Wentao Wu, Ce Zhang, et al. 2021. VolcanoML: speeding up
 [3] James S Bergstra, Rémi Bardenet, Yoshua Bengio, and Balázs Kégl. 2011. Al-                end-to-end AutoML via scalable search space decomposition. Proceedings of the
     gorithms for hyper-parameter optimization. In Advances in neural information              VLDB Endowment 14 (2021).
     processing systems. 2546–2554.                                                       [28] Yaliang Li, Zhen Wang, Bolin Ding, and Ce Zhang. 2021. AutoML: A Perspec-
 [4] Bernd Bischl, Martin Binder, Michel Lang, Tobias Pielok, Jakob Richter, Stefan            tive where Industry Meets Academy. In Proceedings of the 27th ACM SIGKDD
     Coors, Janek Thomas, Theresa Ullmann, Marc Becker, Anne-Laure Boulesteix,                 Conference on Knowledge Discovery & Data Mining. 4048–4049.
     et al. 2021. Hyperparameter optimization: Foundations, algorithms, best practices    [29] Marius Lindauer and Frank Hutter. 2018. Warmstarting of model-based algorithm
     and open challenges. arXiv preprint arXiv:2107.05847 (2021).                              configuration. In Proceedings of the AAAI Conference on Artificial Intelligence,
 [5] Jacob Devlin, Ming-Wei Chang, Kenton Lee, and Kristina Toutanova. 2018. Bert:             Vol. 32.
     Pre-training of deep bidirectional transformers for language understanding. arXiv    [30] Marius Thomas Lindauer, Katharina Eggensperger, Matthias Feurer, Andr’e
     preprint arXiv:1810.04805 (2018).                                                         Biedenkapp, Difan Deng, Caroline Benjamins, René Sass, and Frank Hutter. 2021.
 [6] Xuanyi Dong and Yi Yang. 2020. NAS-Bench-201: Extending the Scope of Re-                  SMAC3: A Versatile Bayesian Optimization Package for Hyperparameter Opti-
     producible Neural Architecture Search. In International Conference on Learning            mization. ArXiv abs/2109.09831 (2021).
     Representations.                                                                     [31] Sinno Jialin Pan and Qiang Yang. 2009. A survey on transfer learning. IEEE
 [7] Stefan Falkner, Aaron Klein, and Frank Hutter. 2018. BOHB: Robust and efficient           Transactions on knowledge and data engineering 22, 10 (2009), 1345–1359.
     hyperparameter optimization at scale. arXiv preprint arXiv:1807.01774 (2018).        [32] Valerio Perrone, Rodolphe Jenatton, Matthias W Seeger, and Cédric Archambeau.
 [8] Matthias Feurer, Aaron Klein, Katharina Eggensperger, Jost Springenberg, Manuel           2018. Scalable hyperparameter transfer learning. Advances in neural information
     Blum, and Frank Hutter. 2015. Efficient and robust automated machine learning.            processing systems 31 (2018).
     In Advances in neural information processing systems. 2962–2970.                     [33] Valerio Perrone, Huibin Shen, Matthias W Seeger, Cedric Archambeau, and
 [9] Matthias Feurer, Benjamin Letham, and Eytan Bakshy. 2018. Scalable meta-                  Rodolphe Jenatton. 2019. Learning search spaces for bayesian optimization: An-
     learning for Bayesian optimization. stat 1050 (2018), 6.                                  other view of hyperparameter transfer learning. Advances in Neural Information
[10] Matthias Feurer, Jost Tobias Springenberg, and Frank Hutter. 2015. Initializing           Processing Systems 32 (2019).
     Bayesian Hyperparameter Optimization via Meta-Learning.. In AAAI. 1128–1135.         [34] Valerio Perrone, Huibin Shen, Aida Zolic, Iaroslav Shcherbatyi, Amr Ahmed,
[11] Daniel Golovin, Benjamin Solnik, Subhodeep Moitra, Greg Kochanski, John                   Tanya Bansal, Michele Donini, Fela Winkelmolen, Rodolphe Jenatton, Jean Bap-
     Karro, and D Sculley. 2017. Google vizier: A service for black-box optimization.          tiste Faddoul, et al. 2021. Amazon sagemaker automatic model tuning: Scalable
     In Proceedings of the 23rd ACM SIGKDD International Conference on Knowledge               gradient-free optimization. In Proceedings of the 27th ACM SIGKDD Conference
     Discovery and Data Mining. ACM, 1487–1495.                                                on Knowledge Discovery & Data Mining. 3463–3471.
[12] Ian Goodfellow, Yoshua Bengio, and Aaron Courville. 2016. Deep learning. MIT         [35] Yao Quanming, Wang Mengshuo, Jair Escalante Hugo, Guyon Isabelle, Hu Yi-Qi,
     press.                                                                                    Li Yu-Feng, Tu Wei-Wei, Yang Qiang, and Yu Yang. 2018. Taking human out of
[13] Nikolaus Hansen. 2016. The CMA evolution strategy: A tutorial. arXiv preprint             learning applications: A survey on automated machine learning. arXiv preprint
     arXiv:1604.00772 (2016).                                                                  arXiv:1810.13306 (2018).
[14] Kaiming He, Xiangyu Zhang, Shaoqing Ren, and Jian Sun. 2016. Deep residual           [36] Carl Edward Rasmussen. 2004. Gaussian processes in machine learning. In
     learning for image recognition. In Proceedings of the IEEE conference on computer         Advanced lectures on machine learning. Springer, 63–71.
     vision and pattern recognition. 770–778.                                             [37] Esteban Real, Alok Aggarwal, Yanping Huang, and Quoc V Le. 2019. Regularized
[15] Geoffrey Hinton, Li Deng, Dong Yu, George E Dahl, Abdel-rahman Mohamed,                   evolution for image classifier architecture search. In Proceedings of the aaai
     Navdeep Jaitly, Andrew Senior, Vincent Vanhoucke, Patrick Nguyen, Tara N                  conference on artificial intelligence, Vol. 33. 4780–4789.
     Sainath, et al. 2012. Deep neural networks for acoustic modeling in speech           [38] David Salinas, Huibin Shen, and Valerio Perrone. 2020. A quantile-based approach
     recognition: The shared views of four research groups. IEEE Signal processing             for hyperparameter transfer learning. In International Conference on Machine
     magazine 29, 6 (2012), 82–97.                                                             Learning. PMLR, 8438–8448.
[16] Frank Hutter, Holger H Hoos, and Kevin Leyton-Brown. 2011. Sequential model-         [39] Nicolas Schilling, Martin Wistuba, and Lars Schmidt-Thieme. 2016. Scalable
     based optimization for general algorithm configuration. In International Confer-          hyperparameter optimization with products of gaussian process experts. In ECML
     ence on Learning and Intelligent Optimization. Springer, 507–523.                         PKDD. Springer, 33–48.
[17] Kevin Jamieson and Ameet Talwalkar. 2016. Non-stochastic best arm identifi-          [40] Bobak Shahriari, Kevin Swersky, Ziyu Wang, Ryan P. Adams, and Nando de Fre-
     cation and hyperparameter optimization. In Artificial intelligence and statistics.        itas. 2016. Taking the Human Out of the Loop: A Review of Bayesian Optimization.
     PMLR, 240–248.                                                                            Proc. IEEE 104, 1 (2016), 148–175.
[18] Donald R Jones, Matthias Schonlau, and William J Welch. 1998. Efficient global       [41] Jasper Snoek, Hugo Larochelle, and Ryan P Adams. 2012. Practical bayesian
     optimization of expensive black-box functions. Journal of Global optimization 13,         optimization of machine learning algorithms. In Advances in neural information
     4 (1998), 455–492.                                                                        processing systems. 2951–2959.
[19] Tinu Theckel Joy, Santu Rana, Sunil Kumar Gupta, and Svetha Venkatesh. 2016.         [42] Jasper Snoek, Oren Rippel, Kevin Swersky, Ryan Kiros, Nadathur Satish,
     Flexible transfer learning framework for Bayesian optimisation. In Pacific-Asia           Narayanan Sundaram, Mostofa Patwary, Mr Prabhat, and Ryan Adams. 2015.
     Conference on Knowledge Discovery and Data Mining. Springer, 102–114.                     Scalable bayesian optimization using deep neural networks. In International
[20] Jungtaek Kim, Saehoon Kim, and Seungjin Choi. 2017. Learning to Trans-                    conference on machine learning. PMLR, 2171–2180.
     fer Initializations for Bayesian Hyperparameter Optimization. arXiv preprint         [43] Jost Tobias Springenberg, Aaron Klein, Stefan Falkner, and Frank Hutter. 2016.
     arXiv:1710.06219 (2017).                                                                  Bayesian optimization with robust Bayesian neural networks. Advances in neural
[21] Patrick Koch, Oleg Golovidov, Steven Gardner, Brett Wujek, Joshua Griffin, and            information processing systems 29 (2016).
     Yan Xu. 2018. Autotune: A derivative-free optimization framework for hyperpa-        [44] Kevin Swersky, Jasper Snoek, and Ryan P Adams. 2013. Multi-task bayesian
     rameter tuning. In Proceedings of the 24th ACM SIGKDD International Conference            optimization. Advances in neural information processing systems 26 (2013).
     on Knowledge Discovery & Data Mining. 443–452.                                       [45] Joaquin Vanschoren, Jan N Van Rijn, Bernd Bischl, and Luis Torgo. 2014. OpenML:
[22] Lisha Li, Kevin Jamieson, Giulia DeSalvo, Afshin Rostamizadeh, and Ameet Tal-             networked science in machine learning. ACM SIGKDD Explorations Newsletter
     walkar. 2018. Hyperband: A novel bandit-based approach to hyperparameter                  15, 2 (2014), 49–60.
     optimization. Proceedings of the International Conference on Learning Representa-    [46] Martin Wistuba, Nicolas Schilling, and Lars Schmidt-Thieme. 2015. Hyperpa-
     tions (2018), 1–48.                                                                       rameter search space pruning–a new component for sequential model-based
[23] Yang Li, Jiawei Jiang, Jinyang Gao, Yingxia Shao, Ce Zhang, and Bin Cui. 2020.            hyperparameter optimization. In ECML PKDD. Springer, 104–119.
     Efficient automatic CASH via rising bandits. In Proceedings of the AAAI Conference   [47] Martin Wistuba, Nicolas Schilling, and Lars Schmidt-Thieme. 2016. Two-stage
     on Artificial Intelligence, Vol. 34. 4763–4771.                                           transfer surrogate model for automatic hyperparameter optimization. In ECML
[24] Yang Li, Yu Shen, Huaijun Jiang, Wentao Zhang, Jixiang Li, Ji Liu, Ce Zhang, and          PKDD. Springer, 199–214.
     Bin Cui. 2022. Hyper-Tune: Towards Efficient Hyper-parameter Tuning at Scale.        [48] Chengrun Yang, Yuji Akimoto, Dae Won Kim, and Madeleine Udell. 2019. OBOE:
     Proceedings of the VLDB Endowment 15 (2022).                                              Collaborative filtering for AutoML model selection. In Proceedings of the 25th
[25] Yang Li, Yu Shen, Jiawei Jiang, Jinyang Gao, Ce Zhang, and Bin Cui. 2021. MFES-           ACM SIGKDD International Conference on Knowledge Discovery & Data Mining.
     HB: Efficient Hyperband with Multi-Fidelity Quality Measurements. In Proceed-             1173–1183.
     ings of the AAAI Conference on Artificial Intelligence, Vol. 35. AAAI Press, 8491–   [49] Dani Yogatama and Gideon Mann. 2014. Efficient transfer learning method for
     8500.                                                                                     automatic hyperparameter tuning. In Artificial Intelligence and Statistics. 1077–
                                                                                               1085.

KDD ’22, August 14–18, 2022, Washington, DC, USA                                                                                               Li et al.

A APPENDIX                                                               Datasets            OpenML ID     Classes   Samples    Continuous   Nominal
                                                                         kc1                    1067          2        2109         21         0
A.1 The Details of Benchmark                                             quake                   772          2        2178         3          0
As described in Section 5, we create two benchmarks to evaluate          segment                 36           7        2310         19         0
                                                                         madelon                1485          2        2600        500         0
the performance of search space design methods, namely the Ran-
                                                                         space_ga                737          2        3107         6          0
dom Forest Tuning Benchmark and ResNet Tuning Benchmark. In              sick                    38           2        3772         7          22
addition, we apply NASBench-201 [6] to test the practicality of our      pollen                  871          2        3848         5          0
method on different types of hyperparameters.                            abalone                 183         26        4177         7          1
                                                                         winequality_white        -           7        4898         11          0
    Random Forest Tuning Benchmark. The random forest clas-              waveform(1)             979          3        5000         40          0
sifier is a widely used tree-based ensemble model in data analysis.      waveform(2)             979          2        5000         40          0
The implementation of random forest and the design of their hyper-       page-blocks(2)         1021          2        5473         10          0
                                                                         optdigits               28          10        5610         64         0
parameter space in the benchmark follows the widely-used AutoML
                                                                         satimage                182          6        6430         36         0
system — Auto-sklearn [8]. The range and the default value of each       wind                    847          2        6574         14         0
hyperparameter are illustrated in Tables 1. To collect sufficient        musk                   1116          2        6598        167         0
source HPO data for transfer learning, we select 20 real-world           delta_ailerons          803          2        7129         5           0
                                                                         puma8NH                 816          2        8192         8          0
datasets from OpenML repository [45], and evaluate the validation        cpu_act                 761          2        8192         21         0
and test performance (i.e., the balanced accuracy) of 50k config-        cpu_small               735          2        8192         12         0
urations for each dataset, which are randomly sampled from the          Table 2: Datasets used in Random Forest Tuning Benchhyperparameter space. The datasets used in our benchmarks are           mark.
of medium size, whose number of rows ranges from 2000 to 8192.
For more details, see Table 2. The total number of model evaluations (observations) in our benchmarks reaches 10 million, and it                        Hyperparameter           Range        Default
takes more than 50k CPU hours to obtain the evaluation results. For
                                                                                         batch size             [32, 256]        64
reproduction purposes, we also upload the benchmark data (e.g.,
                                                                                         learning rate         [1e-3, 0.3]       0.1
evaluation results and the corresponding scripts) along with this
                                                                                         weight decay         [1e-5, 1e-2]      2e-4
submission. The benchmark data (with size – 355.4Mb); due to the                         momentum              [0.5, 0.99]       0.9
space limit (maximum 20Mb) on CMT3, we only upload a small                               nesterov            {True, False}      True
subset of this benchmark. After the review process, we will make
                                                                                        Table 3: Hyperparameters of ResNet.
the complete benchmark publicly available (e.g., on Google Drive).

                                                                          Hyperparameter                          Choice                     Default
              Hyperparameter            Range         Default
                                                                          Operation 1        {None, Skip, 1*1 Conv, 3*3 Conv, 3*3 Pooling}    Skip
              criterion             {gini, entropy}    gini               Operation 2        {None, Skip, 1*1 Conv, 3*3 Conv, 3*3 Pooling}    Skip
              max_features               [0, 1]         0.5               Operation 3        {None, Skip, 1*1 Conv, 3*3 Conv, 3*3 Pooling}    Skip
              min_sample_split          [2, 20]          2                Operation 4        {None, Skip, 1*1 Conv, 3*3 Conv, 3*3 Pooling}    Skip
              min_sample_leaf           [1, 20]          1                Operation 5        {None, Skip, 1*1 Conv, 3*3 Conv, 3*3 Pooling}    Skip
              bootstrap              {True, False}     True               Operation 6        {None, Skip, 1*1 Conv, 3*3 Conv, 3*3 Pooling}    Skip

         Table 1: Hyperparameters of Random Forest.                              Table 4: Hyperparameters of NASBench-201.

                                                                        A.2     Implementation and Reproduction Details
                                                                        We use the implementation of Bayesian optimization in SMAC [16,
    ResNet Tuning Benchmark. While deep learning has attracted          30], a toolkit for black-box optimization that support a complex
great attention in recent years, we also create a benchmark for DL      hyperparameter space, including numerical, categorical, and condialgorithm tuning. Concretely, we tune the ResNet with five hyper-       tional hyperparameters. The kernel hyperparameters in Gaussian
parameters as shown in Table 3. The benchmark contains three            Process are inferred by maximizing the marginal likelihood. In the
vision tasks, including CIFAR-10, SVHN, and Tiny-ImageNet. Each         BO module, the popular EI acquisition function is used. The other
task contains the evaluation results of 1500 randomly chosen config-    baselines are implemented following their original papers. In all
urations and 500 configurations selected by running a GP-based BO.      experiments, the 𝛼𝑚𝑎𝑥 and 𝛼𝑚𝑖𝑛 in our approach are set to 0.95 and
It takes more than 5k GPU hours to obtain the evaluation results.       0.05, respectively. The sampling framework samples 𝑘 = 5 tasks
    NASBench-201 NASBench-201 [6] is a benchmark for neural             during each iteration on the benchmarks.
architecture search (NAS). The architecture space includes six op-         The source code and benchmark data are uploaded along with
erations, and the choices for each operation are provided in Table 4.   this submission on CMT3, and the source code is also available in
To help compare different NAS algorithms, NASBench-201 exhaus-          the anonymous repository 1 now. To reproduce the experimental
tively evaluates all the configurations from its search space on        results in this paper, an environment of Python 3.7+ is required.
three datasets (CIFAR-10, CIFAR-100, ImageNet16-120). Each task         We introduce the experiment scripts and installation of required
includes the results of 15625 evaluations, including the validation
accuracy, test accuracy, and training time.                             1 https://anonymous.4open.science/r/2022-5263-XXXXX

Transfer Learning based Search Space Design for Hyperparameter Tuning                            KDD ’22, August 14–18, 2022, Washington, DC, USA

tools in README.md and list the required Python packages in re-         python tools/offline_benchmark.py –algo_id random_forest –trial_num
quirements.txt under the root directory. Take one experiment as         50 –methods rs,box-rs,ellipsoid-rs,ours-rs –rep 20
an example, to evaluate the offline performance of our method              Please check the document README.md in this repository for
with random search and other baselines on Random Forest Tuning          more details, e.g., how to use the benchmark data in this benchmark,
Benchmark with 50 trials, just execute the following script:            and how to run the experiments.
