---
citation_key: "BaiEtAl2023"
title: "Transfer Learning for Bayesian Optimization: A Survey"
authors: "Tianyi Bai; Yang Li; Yu Shen; Xinyi Zhang; Wentao Zhang; Bin Cui"
year: 2023
doi: "10.48550/arxiv.2302.05927"
source: "arXiv (2302.05927)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "2302.05927"
conversion: "pdftotext -layout (automated)"
---

# Transfer Learning for Bayesian Optimization: A Survey

Transfer Learning for Bayesian Optimization: A Survey
                                         TIANYI BAI, Beijing Institute of Technology, China
                                         YANG LI∗ , Tencent Inc., China
                                         YU SHEN, XINYI ZHANG, Peking University, China

arXiv:2302.05927v1 [cs.LG] 12 Feb 2023
                                         WENTAO ZHANG, Mila - Québec AI Institute, Canada
                                         BIN CUI∗ , Peking University, China
                                         A wide spectrum of design and decision problems, including parameter tuning, A/B testing and drug design,
                                         intrinsically are instances of black-box optimization. Bayesian optimization (BO) is a powerful tool that
                                         models and optimizes such expensive “black-box” functions. However, at the beginning of optimization, vanilla
                                         Bayesian optimization methods often suffer from slow convergence issue due to inaccurate modeling based
                                         on few trials. To address this issue, researchers in the BO community propose to incorporate the spirit of
                                         transfer learning to accelerate optimization process, which could borrow strength from the past tasks (source
                                         tasks) to accelerate the current optimization problem (target task). This survey paper first summarizes transfer
                                         learning methods for Bayesian optimization from four perspectives: initial points design, search space design,
                                         surrogate model, and acquisition function. Then it highlights its methodological aspects and technical details
                                         for each approach. Finally, it showcases a wide range of applications and proposes promising future directions.
                                         CCS Concepts: • Computing methodologies → Transfer learning; Machine learning approaches; Search
                                         methodologies.
                                         Additional Key Words and Phrases: Bayesian Optimization; Transfer Learning; Black-box Optimization
                                         ACM Reference Format:
                                         Tianyi Bai, Yang Li, Yu Shen, Xinyi Zhang, Wentao Zhang, and Bin Cui. 2023. Transfer Learning for Bayesian
                                         Optimization: A Survey. J. ACM 1, 1 (February 2023), 35 pages. https://doi.org/XXXXXXX.XXXXXXX

                                         1    INTRODUCTION
                                         Black–box optimization (BBO) is the task of optimizing an objective function within a limited
                                         budget for function evaluations. “Black-box” means that the objective function has no analytical
                                         form. In this way, we cannot access but we can only observe its outputs (i.e., objective values)
                                         based on the given inputs, without any knowledge of its internal workings. Since the evaluation
                                         of objective functions is often expensive, the goal of black-box optimization is to find the global
                                         optimum as rapidly as possible [67].
                                            Black-box optimization problems appear everywhere. Design problems and many decision
                                         problems, which are pervasive in scientific and industrial endeavours, fall into the domain of
                                         ∗ Yang Li and Bin Cui are the corresponding authors.

                                         Authors’ addresses: Tianyi Bai, School of Mathematics and Statistics, Beijing Institute of Technology, Beijing, China,
                                         baitianyi@bit.edu.cn; Yang Li, Data Platform, TEG, Tencent Inc., Beijing, China, thomasyngli@tencent.com; Yu Shen, Xinyi
                                         Zhang, Key Lab of High Confidence Software Technologies, Peking University, Beijing, China, {shenyu,zhang_xinyi}@
                                         pku.edu.cn; Wentao Zhang, Mila - Québec AI Institute, Montréal, Canada, wentao.zhang@mila.quebec; Bin Cui, Key Lab
                                         of High Confidence Software Technologies, Peking University, Beijing, Institute of Computational Social Science, Peking
                                         University, Qingdao, China, bin.cui@pku.edu.cn.

                                         Permission to make digital or hard copies of all or part of this work for personal or classroom use is granted without fee
                                         provided that copies are not made or distributed for profit or commercial advantage and that copies bear this notice and
                                         the full citation on the first page. Copyrights for components of this work owned by others than ACM must be honored.
                                         Abstracting with credit is permitted. To copy otherwise, or republish, to post on servers or to redistribute to lists, requires
                                         prior specific permission and/or a fee. Request permissions from permissions@acm.org.
                                         © 2023 Association for Computing Machinery.
                                         0004-5411/2023/2-ART $15.00
                                         https://doi.org/XXXXXXX.XXXXXXX

                                                                                                      J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

2                                                                                                 Bai et al.

black-box optimization, including experiment design [31, 35, 108], machine design [55, 93], drug
design [43, 84], robotics [70, 73], environmental monitoring [5, 72], combinatorial optimization [42,
59], and automatic machine learning [6, 22, 25, 45, 62, 68], etc.
  Example 1: Hyperparameter tuning. The performance of machine learning (ML) models heavily
depends on the choice of hyperparameter configurations (e.g., regularization parameter in support
vector machine or learning rate in a deep neural network). As a result, automatically tuning the
hyperparameters has attracted lots of interest in machine learning community.
   Example 2: A/B Testing. A/B testing is useful for understanding user engagement and satisfaction
of online features like a new feature or product. Large social media sites like LinkedIn, Facebook,
and Instagram use A/B testing to make user experiences more successful and as a way to streamline
their services [127]. A/B testing is widely used by data engineers, designers, software engineers,
and entrepreneurs, among others. For instance, A/B testing can be utilized to determine the most
suitable price for the product, where it aims to find out which price-point maximizes the total
revenue.
  Example 3: Knobs tuning. Modern database management systems (DBMS) contain tens to hundreds of critical performance tuning knobs that determine the system runtime behaviors. Different
knobs directly affect the running database performance in terms of latency and throughput. Recently, many methods are proposed to utilize ML based techniques to optimize the performance of
DBMSs automatically.
  Example 4: Big data platforms tuning. Spark has emerged as one of the most widely used frameworks for massively parallel data analytics. Spark task is controlled by up to 160 configuration
parameters, which determine many aspects including dynamic allocation, scheduling, memory
management, execution behavior, etc. Tuning arbitrary Spark applications by efficiently and automatically navigating over the huge search space is a challenging task.
   Example 5: Electronic design automation. Electronic design automation (EDA) tools play a vital role
in pushing forward the VLSI industry. The design complexity keeps increasing in order to ensure
timing, reliability, manufacturability, etc. This trend brings the increasing amount of parameters
involved in EDA tools, thus incurring a huge design search space. The aim is to find the most
suitable parameters in EDA tools to achieve desired quality [71].
   Recently, Bayesian Optimization (BO) methods have become one of the most prevailing frameworks in solving black-box optimization problems [91]. BO-based solutions have been extensively
investigated and deployed to solve the BBO problems efficiently and effectively, including the
aforementioned examples [2, 6, 42, 64, 66, 71, 96, 130, 132].
   Challenge. Although Bayesian optimization (BO) methods have achieved a great stride of success
in a wide range of fields, there still remain issues that need to be addressed. One of them is about the
slow convergence issue, which greatly hampers the efficiency and practicality of BO. The main idea
of BO is to use a surrogate model, typically a Gaussian Process (GP), to describe the relationship
between a configuration and its performance, and then utilize this surrogate to determine the
next configuration to evaluate by optimizing an acquisition function that balances exploration and
exploitation. However, evaluating the objective functions is usually computationally expensive.
Given a limited budget, few observations about the function evaluations are obtained, and these
observations cannot be used to learn an accurate surrogate model that represents the objective
function well. Further, the surrogate model cannot guide the search of configuration effectively and
efficiently, thus leading to the “slow convergence” problem. In many real scenarios, users cannot
bear the additional cost for initial trials during the cold start period. For each trial, the cost in terms

J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

Transfer Learning for Bayesian Optimization: A Survey                                                                  3

of device, expense or time can be very expensive. In addition, along with the growing search space,
the number of trials increases for building accurate surrogates. Therefore, it is essential to improve
Bayesian optimization method with faster convergence.
   Opportunity. To address this issue, researchers in the BO community propose to incorporate
the spirit of transfer learning to accelerate Black-box optimization, which could borrow strength
from past tasks (source tasks) to accelerate the current optimization task (target task). Many realworld black-box problems usually need to be constantly re-optimized as task/environment changes,
e.g., the update of model/code in the AutoML applications. The optimal configuration (i.e., some
design or decision) may also change as the task/ environment varies, and so should be frequently
re-optimized. Although they may change significantly, the region of good or bad configurations
may still share some correlation with those of previous tasks, and this provides the opportunity for
faster Bayesian optimization.
  Method categorization. In this paper, we review the transfer learning methods for Bayesian
optimization in depth. The overview of the categorization is summarized in Table 1. As far as we
know, Bayesian Optimization consists of four main components that can be customized manually,
which are the initial points, the search space, the surrogate model, and the acquisition function.
Based on this perspective, we divide existing transfer learning methods into four main categories.
For each main category, we further divide each category based on specific techniques.

                       Table 1. Transfer learning for Bayesian Optimization: Overview

             BO components                         Specific categories
                                   Gaussian Process as surrogate model (Kernel Design,
                Surrogate           Prior Design, Data Scale Design, Ensemble Design)
                  Design               Bayesian Neural Network as surrogate model
                                            Neural Process as surrogate model
                                            Multi-task BO acquisition function
           Acquisition function
                                    Ensemble GPs-based acquisition function transfer
                  Design
                                Reinforcement learning-based acquisition function transfer
                                            Meta-features based initialization
              Initialization
                                           Gradient-based learning initialization
                  Design
                                        Evolutionary algorithm based initialization
              Search space                    Search space pruning method
                  Design                      Promising search space design

  Contribution and Overview. In this survey, the main contributions can be summarized as follows:
  (1) We systematically categorize existing transfer learning works of Bayesian optimization based
      on “what to transfer” and “how to transfer”. Problem setups are from the “what” perspective,
      indicating which learning process we want to make transfer. Techniques are from the “how”
      perspective, introducing the methods proposed to solve BO problems. For each category, we
      present detailed method descriptions for reference.
  (2) We propose and discuss a general transfer learning framework for Bayesian optimization.
      Such a framework can act as a guidance for developing new approaches.
  (3) In addition, we also present the potential application scenarios, where the transfer learning
      approaches for Bayesian optimization could work well.
  We begin in Section 2, with an introduction to black-box optimization and Bayesian optimization.
In Sections 3-7, we introduce existing transfer learning methods from four aspects, Surrogate

                                                        J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

4                                                                                            Bai et al.

Design in Section 4, Acquisition function Design in section 5, Warm-start Method in Section 6, and
Search space Design in Section 7. We provide the description for potential application scenarios in
Section 8, and end this survey with a conclusion in Section 10.

2   BACKGROUND AND FORMULATION
2.1 Black-box Optimization
Black-box Optimization (BBO) is a kind of optimization problem when the objective function is
a black-box function. On the contrary to white-box function, black-box function has no exact
form and is not access to any other information like gradients or the Hessian. The mathematical
expression of black-box function is 𝑓 : X → R, where X is the search space for a certain problem.
For a given point 𝑥 ∈ X, we can evaluate the function value 𝑓 (𝑥) of a black-box function. When
the evaluation cost is very high, selecting which point to evaluate next becomes a vital problem to
consider. Therefore, the problem of BBO can be interpreted as to approach the global optimum
as rapidly as possible through selecting a sequence of search points {𝑥𝑡 }𝑛𝑡=1 and evaluating their
function value.
   Black-box Optimization has a wide application in many areas where the relationship of inputs and
outputs is complex or unknown, such as automated hyperparameters tuning of automated machine
learning system, optimization of chemical compounds or materials [104], reference learning and
interactive interfaces [13], resource allocation and so on.
   Due to the lack of information of the target function, in order to solve a BBO problem, we
have to utilize some navigation algorithms to guide our searching process. There exists two main
taxonomies for those BBO algorithms, which could be summarized as non-adaptive or self-adaptive
algorithms, local optimization or global optimization algorithms. The simplest one is algorithms
with no adaptive capacity, including Grid Search that selects 𝑥𝑡 along a grid made of Cartesian
product of all candidates values, and Random Search that selects 𝑥𝑡 uniformly at random from
X at each steps. The self-adaptive algorithms consists of classic algorithms (such as Simulated
Annealing), population-based optimization algorithms [126] (such as Genetic Algorithms [19], Ant
Colony Optimization [21]) and so on. As for local or global optimization algorithms, the main
difference between them is that local optimization algorithms can only get a local optimum, but
global optimization algorithms try their best to get a global optimum. Many local optimization
algorithms try to maintain simple models of the objective function 𝑓 within a subset of the feasible
regions (known as trust region), including derivative-free optimization [18] (such as Nelder-Mead
simplex reflection [78]). While the global optimization algorithms try to optimize the function in
the overall searching spaces to obtain a global optimum.
   More recently, Bayesian Optimization has been developed to solve BBO problem [75] and is
been shown to outperform other global optimization algorithms on a number of challenging
optimization benchmark functions[48]. Bayesian optimization utilizes the idea from multi-armed
bandit problems to manage exploration and exploitation trade-offs. This optimization technique
goes under a Bayesian pattern, which learns a posterior from a given prior and the observed
information of sequential evaluation.
   For a certain BBO problem, there are three main questions to consider, the design of search space,
the selection of navigation algorithm and initialization. Due to the high computational complexity
of searching the whole flexible region in large data-sets, many researchers have come up with an
idea of removing unpromising region to accelerate searching process [82, 121]. Besides, sometimes
we can acquire little information from the certain problem, thus designing a bounded search space
may be hard to accomplish. Therefore, some works propose to incrementally expand the search
space with unbounded form [79, 90]. It should be noted that there is a difference between traditional

J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

Transfer Learning for Bayesian Optimization: A Survey                                                                  5

local optimization algorithms and the algorithms with search space design, as the latter ones still
belong to the global optimization category that hopes to find a global optimum. Meanwhile, as
many navigation algorithms are self-adaptive algorithms, choosing a promising initial point is also
beneficial for further searching process [28, 53].

2.2 Bayesian Optimization
Bayesian Optimization (BO) [75] is one of the state-of-the-art algorithms for black-box optimization. It performs well when expensive function is needed to be evaluated and it has applied in
many areas [91], including robotics [7], automatic machine learning [58, 66, 96], environmental
monitoring [72], reinforcement learning [14], neural architecture search [52]and so on.
   The main problem for Bayesian optimization to solve is mathematically as follow. We consider
the problem as an optimization problem for an unknown objective function 𝑓 , and we hope to find
a global minimizer (or maximizer) of that function:
                                              𝒙 ∗ = arg min 𝑓 (𝒙),                                                   (1)
                                                        𝒙 ∈X
where X is a designed search space. Bayesian optimization can not only deal with the traditional
problem that the search space is numerical and in a form of R𝑑 , but it can also applied to problems
with unusual search spaces, including categorical or conditional inputs, or even combinatorial
search spaces with multiple categorical inputs. Moreover, the black-box function 𝑓 is assumed with
no simple closed form, but it can be evaluated at any point in the search space. For any given point
𝑥𝑖 , we can get an noisy observation of function 𝑓 , noted as 𝑦𝑖 = 𝑓 (𝑥𝑖 ) + 𝜀 in which 𝜀 ∼ N (0, 𝜎 2 )
and E[𝑦 | 𝒙] = 𝑓 (𝒙).
    In this setting, we consider Bayesian optimization as a sequential search algorithm. At iteration
n, BO uses the evaluated information to guide the search of new location 𝒙𝑛+1 and get the noisy
evaluation 𝒚𝑛+1 of the black-box function 𝑓 . And after N rounds of iterations, BO makes a final
decision of the optimization solution and provide a solution noted as 𝒙ˆ ∗ .
    Bayesian optimization mainly contains two key ingredients, a probabilistic surrogate model and
an acquisition function. We assume that the black-box function 𝑓 is sampled from a probabilistic
distribution, known as probabilistic surrogate model, which contains our beliefs on the unknown
black-box function and captures the new observation information to update our knowledge of
the current function. The acquisition function is used to balance the exploration and exploitation
trade-off to make decision of next searching point in the domain. We will introduce common
surrogate models and acquisition function in the following parts.
2.2.1 Surrogate model. There are many available surrogate models for Bayesian Optimization. Most
of the researches use Gaussian Processes [96], Bayesian neural networks [81, 97, 100], tree parzen
estimators [6], or random forest [11, 42] as surrogate models. In this section, we will introduce
some of them, and the detailed settings will be introduced in following sections.
• Gaussian Processes as surrogate models. Gaussian Processes (GPs) [116] has been widely used
as surrogate models in Bayesian Optimization [42, 75, 96]. The Gaussian process has a convenient
property that, if we assume prior as a Gaussian distribution, we can get the posterior by computing
the mean and covariance function, which still follows a Gaussian distribution.
   Usually we assume the objective black-box function follows a Gaussian distribution prior. Besides,
we assume that each variable 𝑓𝑖 = 𝑓 (𝒙𝑖 ) is independent and identically distributed to others, thus
the joint distribution of 𝒇 := 𝑓1:𝑛 is a joint Gaussian prior, i.e. 𝒇 | 𝑿 ∼ N (𝒎, 𝑲 ), where 𝑿 is a vector
consists of {𝒙𝑖 }𝑛𝑖=1 , 𝒎 is a mean vector consists of mean values {𝑚𝑖 = 𝜇0 (𝒙𝑖 )}𝑛𝑖=1 generated from
mean function 𝜇0 : X → R, and 𝑲 is a covariance matrix consists of positive–definite kernels
{𝐾𝑖,𝑗 = 𝑘 (𝒙𝑖 , 𝒙 𝑗 )}𝑛𝑖,𝑗=1 generated from convariance function 𝑘 : X × X → R. The noisy observations

                                                        J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

6                                                                                                Bai et al.

Algorithm 1 Pseudo code for Bayesian Optimization
Require: the number of trials 𝑇 , the hyper-parameter space X, surrogate model 𝑀, acquisition function 𝛼,
    and initial hyper-parameter configurations 𝑋𝑖𝑛𝑖𝑡 .
 1: for {𝒙 ∈ 𝑋𝑖𝑛𝑖𝑡 } do
 2:    evaluate the configuration 𝒙 and obtain its performance 𝑦.
 3:    augment 𝐷 = 𝐷 ∪ (𝒙, 𝑦).
 4: end for
 5: initialize observations 𝐷 with initial design.
 6: for { 𝑖 = |𝑋𝑖𝑛𝑖𝑡 | + 1, ...,𝑇 } do
 7:    fit surrogate 𝑀 based on observations 𝐷.
 8:    select the configuration to evaluate: 𝒙𝑖 = argmax𝒙 ∈ X̂ 𝛼 (𝒙, 𝑀).
 9:    evaluate the configuration 𝒙𝑖 and obtain its performance 𝑦𝑖 .
10:    augment 𝐷 = 𝐷 ∪ (𝒙𝑖 , 𝑦𝑖 ).
11: end for
12: return the configuration with the best observed performance.

𝒚 := 𝑦1:𝑛 naturally follow a normal distribution given as,
                                                𝒚 | 𝒇, 𝜎 2 ∼ N (𝒇, 𝜎 2 𝑰 ).                            (2)
  Given a set of observation D              = {𝒙𝑖 , 𝑦𝑖 }𝑛𝑖=1 , also noted as D
                                                                        = (𝑿, 𝒚), we can compute the
posterior distribution of 𝑓 at an arbitrary test point 𝒙 by computing its posterior mean and variance
function:
                                 𝑓 | 𝒙, 𝑿, 𝒚 ∼ N (𝜇𝑛 (𝒙), 𝜎𝑛2 (𝒙)), 𝑤ℎ𝑒𝑟𝑒
                                𝜇𝑛 (𝒙) = 𝜇0 (𝒙) + 𝑘 (𝑿, 𝒙)𝑇 (𝑲 + 𝜎 2 𝑰 ) −1 (𝒚 − 𝒎),                   (3)
                                𝜎𝑛2 (𝒙) = 𝑘 (𝒙, 𝒙) − 𝑘 (𝑿, 𝒙)𝑇 (𝑲 + 𝜎 2 𝑰 ) −1𝑘 (𝑿, 𝒙),
where 𝑘 (𝑿, 𝒙) is a vector that shows the result of computing covariance function between 𝒙 and 𝑿 .
   Usually we require a predetermined form of the mean function 𝜇0 and covariance function 𝑘.
Previous works usually set mean functions to be zero or linear, and the popular kernel functions
include Matěrn kernels, Squared Exponential kernel and RBF kernel [77, 86]. The hyper-parameters
in these functions are usually trained by maximizing data-likelihood of the current observations,
or by putting a prior on the mean/kernel hyper-parameters and obtaining a distribution of such
hyper-parameters to adapt the model given observations [86].
• Random Forests as surrogate models. Random Forests are an ensemble of regression trees
that are used to handle the problems with many input variables and hard to be dealt with a single
regression tree. Regression trees [12] utilize tree structures to model classification or regression
problems in machine learning. Different from typical decision trees that also leverage tree structures,
regression trees have real values rather than classifying labels at their leaves, thus they can give
out a predictive value for every input point. It is proved that the random forest method always
converges to the optimal solutions [11] and empirically performs well especially on problems with
categorical inputs.
   Previous works have utilized random forests in Bayesian optimization (such as Sequential Modelbased Algorithm Configuration (SMAC) in [42]), due to their efficiency when dealing with categorical
inputs, and their advantage that can give out both predictive value and uncertainty of the prediction
for any given input. To construct a random forest, independent regression trees are built by randomly
sampling 𝑛 ′ points from a given dataset D = {𝒙𝑖 , 𝑦𝑖 }𝑛𝑖=1 , and then randomly selecting features to
split the points in every node. Assuming a random forest has 𝑚 regression trees in it, we note 𝑇𝑖 as
the predictive function of the 𝑖-th regression tree. The total predictive mean is given as the average

J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

Transfer Learning for Bayesian Optimization: A Survey                                                                   7

of predictive values from each regression tree in the random forest, as 𝜇 (𝒙) = 𝑚1 𝑚
                                                                                    Í
                                                                                      𝑖=1 𝑇𝑖 (𝒙), and
                                    1 Í𝑚
the variance is given as 𝜎 2 (𝒙) = 𝑚−1       (𝑇
                                         𝑖=1 𝑖  (𝒙)  − 𝜇 (𝒙)) 2.

• Tree Parzen Estimators as surrogate models. While GPs model 𝑝 (𝑦 | 𝒙) directly, Tree Parzen
Estimators [6] model 𝑝 (𝒙 | 𝑦) and 𝑝 (𝑦) separately. Specifically, to model 𝑝 (𝒙 | 𝑦), a parameter
𝛾 that tells the selected quantile should be given, thus for a given dataset D = {𝒙𝑖 , 𝑦𝑖 }𝑛𝑖=1 , 𝛾 can
be used to choose the observation value 𝑦 ′ that satisfies 𝑝 (𝑦 < 𝑦 ′) = 𝛾. Leveraging this chosen
observation value 𝑦 ′, the likelihood 𝑝 (𝒙 | 𝑦) can be defined as
                                                 (
                                                  𝑙 (𝒙) if 𝑦 < 𝑦 ′
                                    𝑝 (𝒙 | 𝑦) =                                                      (4)
                                                  𝑔(𝒙) if 𝑦 ≥ 𝑦 ′
where 𝑙 (𝒙) is the probability computed by using the points in dataset D that satisfies 𝑦 (𝒙𝑖 ) < 𝑦 ′,
and 𝑔(𝒙) is the probability computed by using the rest of the points in dataset D. Therefore, using
Bayes rule, the posterior 𝑝 (𝑦 | 𝒙) can be given as
                                                         𝑝 (𝒙 | 𝑦)𝑝 (𝑦)
                                           𝑝 (𝑦 | 𝒙) =                                                                (5)
                                                             𝑝 (𝒙)
                ∫
where 𝑝 (𝒙) = R 𝑝 (𝒙 | 𝑦)𝑝 (𝑦)𝑑𝑦 = 𝛾𝑙 (𝒙) + (1 − 𝛾)𝑔(𝒙). To utilizing this method in Bayesian
optimization, previous works [6, 135] consider to combine it with the acquisition function Expected
Improvement (general form of EI will be introduced in Sec.2.2.2) as
                               ∫ 𝑦′                         ∫ 𝑦′
                                                                            𝑝 (𝒙 | 𝑦)𝑝 (𝑦)
                 𝛼 𝐸𝐼 𝑦′ (𝒙) =       (𝑦 ′ − 𝑦)𝑝 (𝑦 | 𝒙)𝑑𝑦 =      (𝑦 ′ − 𝑦)                 𝑑𝑦
                                −∞                           −∞                 𝑝 (𝒙)
                                                 ∫ 𝑦′                                            (6)
                               𝛾𝑦 ′𝑙 (𝒙) − 𝑙 (𝒙) −∞ 𝑝 (𝑦)𝑑𝑦              𝑙 (𝒙)
                             =                               ∝
                                   𝛾𝑙 (𝒙) + (1 − 𝛾)𝑔(𝒙)        𝛾𝑙 (𝒙) + (1 − 𝛾)𝑔(𝒙)
• Bayesian Neural Networks as surrogate models. From the computational formula of GP
posterior in Eq.3 we can know that the inference time of a GP scales cubically with the number
of observations, as it has to compute a dense covariance matrix and its inversion. For this reason,
GP-based BO can hardly leverage large numbers of past function evaluations. Thus, for optimization
problem that requires many evaluations, GP-based BO shows its weakness.
   Therefore, some researchers have proposed to use Bayesian Neural Network as an alternative to
GP to be the surrogate model of BO [81, 97, 100]. Those works utilize the flexibility and scalability
of neural networks while keep the well-calibrated uncertainty estimates of GPs. Those works use
different methods to compute prior and posterior distribution, which we will introduce in detail in
section 4.
2.2.2 Acquisition function. In Bayesian Optimization, acquisition functions are used to choose
which point to evaluate next from the search space, i.e., based on the posterior model generated
from the evaluated sets D𝑛 to select the next querying point 𝒙𝑛+1 in the search space X. The main
question an acquisition function has to handle is how to leverage the posterior model to manage the
exploration and exploitation trade-offs. Usually, given an acquisition function 𝛼, the next querying
point 𝒙𝑛+1 is computed by calculating the acquisition function for every point in a given search
space and find the maximizer of it, i.e.
                                             𝒙𝑛+1 = arg max 𝛼𝑛 (𝒙)                                                    (7)
                                                         𝒙 ∈X
   In general, acquisition functions can be divided into four categories (proposed by Shahriari
et al. [91]), improvement-based policies (such as Probability of Improvement [60], Expected Improvement [49, 74] and Knowledge Gradient [30]), optimistic policies (such as Gaussian Process Upper

                                                         J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

8                                                                                                 Bai et al.

Confidence Bound [101]), information-based policies (such as Thompson Sampling [105] and Entropy
Search [37]) and some portfolios containing multiple acquisition functions (such as Entropy Search
Portfolio [92]). We will introduce some of them in the following.
   In this section, we note D𝑛 = {𝒙𝑖 , 𝑦𝑖 }𝑖=1
                                             𝑡   as the dataset observed at 𝑛-th iteration, 𝑓𝑛∗ as the
optimal evaluations at 𝑛-th iteration, i.e. 𝑓𝑛 = max𝒙 ∈𝐷𝑛 𝑓 (𝒙), and 𝒙 ∗ = arg max𝒙 ∈𝐷𝑛 𝑓 (𝒙).
                                               ∗

• Probability of Improvement Probability of Improvement (PI) is an early method proposed
by Kushner [60] to manage the exploration-exploitation trade-offs. It simply utilizes the mean and
variance function given by the probability model, and is defined as
                                                              𝜇𝑛 (𝒙) − 𝑓𝑛∗
                                                                          
                             𝛼 𝑃𝐼𝑛 (𝒙) = P(𝑓 (𝒙) > 𝑓𝑛 ) = Φ
                                                     ∗
                                                                             ,                      (8)
                                                                 𝜎𝑛 (𝒙)
where the standard normal cumulative distribution function Φ is utilized to compute the cumulative
probability.
• Expected Improvement Expected Improvement (EI) is proposed by Močkus [74] and popularized
by Jones et al. [49]. It improves the PI as it considers the amount of improvement, thus not simply
rely on the probability. The simplest form of EI can be note as
                      𝛼 𝐸𝐼𝑛 (𝒙) = E𝑛 [(𝑓 (𝒙) − 𝑓𝑛∗ )P(𝑓 (𝒙) > 𝑓𝑛∗ )]
                                                     𝜇𝑛 (𝒙) − 𝑓𝑛∗               𝜇𝑛 (𝒙) − 𝑓𝑛∗
                                                                                          
                                = (𝜇𝑛 (𝒙) − 𝑓𝑛∗ )Φ                  + 𝜎𝑛 (𝒙)𝜙                           (9)
                                                        𝜎𝑛 (𝒙)                     𝜎𝑛 (𝒙)
                                = 𝜎𝑛 (𝒙) [𝛾 (𝒙)Φ(𝛾 (𝒙)) + 𝜙 (𝛾 (𝒙))],
                                                                                                 𝜇 (𝒙)−𝑓 ∗
where 𝜙 is the probability density function of the standard normal distribution, and 𝛾 (𝒙) = 𝑛𝜎𝑛 (𝒙) 𝑛 .
• Gaussian Process Upper Confidence Bound Gaussian Process Upper Confidence Bound (GP-
UCB) is proposed by Srinivas et al. [101]. It is a method generated from the idea of using Gaussian
Processes as surrogate models, and the GP-UCB is simply defined by utilizing the mean and variance
function computed from the probability model, as
                                         𝛼𝐺𝑃 −𝑈 𝐶𝐵𝑛 (𝒙) = 𝜇𝑛 (𝒙) + 𝛽𝑛 𝜎𝑛 (𝒙),                         (10)
where 𝛽𝑛 is a given parameter that control the degree of exploration and exploitation. Additionally,
the Gaussian Process Lower Confidence Bound (GP-LCB) can be defined accordingly,
                                             𝑙𝑐𝑏𝑛 (𝒙) = 𝜇𝑛 (𝒙) − 𝛽𝑛 𝜎𝑛 (𝒙),                           (11)
which is also useful in some literature.
• Entropy Search Entropy search (ES) is proposed by Hennig and Schuler [37] and it leverages
the idea from information theory. Specifically, ES measures how promising a given point 𝒙 is by
computing the information gain of selecting it as next point to explore:
                         𝛼 𝐸𝑆𝑛 (𝒙) = 𝐻 (𝒙 ∗ | 𝐷𝑛 ) − E 𝑓 (𝒙) [𝐻 (𝒙 ∗ | 𝐷𝑛 ∪ {(𝒙, 𝜇𝑛 (𝒙))})]           (12)
where 𝐻 (𝒙 ∗ | 𝐷𝑛 ) represents the entropy of the posterior distribution 𝑝 (𝒙 ∗ | 𝐷𝑛 ) at 𝑛-th iteration.
The evaluation at point 𝒙 is approximated by utilizing the mean function 𝜇𝑛 computed from the
probability model. And the expectation is taken over the posterior 𝑓 (𝒙), also given by the probability
model. An alternative form is given as
                     ∫ ∫
         𝛼 𝐸𝑆𝑛 (𝒙) =      [𝐻 (𝒙 ∗ | 𝐷𝑛 ) − 𝐻 (𝒙 ∗ | 𝐷𝑛 ∪ {(𝒙, 𝜇𝑛 (𝒙))})]𝑝 (𝑦 | 𝑓 )𝑝 (𝑓 | 𝒙)𝑑𝑦𝑑 𝑓  (13)

where it is usually approximated through sampling 𝑓 using Monte Carlo method due to the fact
that it has no simple form. This form of ES is also applied in many works [103].
  Some variant of ES has been proposed, such as Predictive Entropy Search (PES) [38] and Max-value
Entropy Search (MES) [112].

J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

Transfer Learning for Bayesian Optimization: A Survey                                                                  9

2.3     Transfer Learning Scenarios
We will give out a unified setting and notation for this problem.
2.3.1    Settings and Notations. The primary notations used in this paper are listed in Table 2.

                                               Table 2. Notations

                    Symbol                                        Definition
                 𝑡 1, 𝑡 2, ..., 𝑡𝑘    the source tasks
                         𝑡𝑇           the target task
                𝐷𝑛1 , ..., 𝐷𝑛𝑡𝐾𝐾
                   𝑡1
                                      the training history from 𝐾 source tasks
                       𝐷𝑡𝑡𝑇           the observations in the target task at 𝑡-th iteration
                         𝑓𝑖           the black-box function of task 𝑡𝑖
                        X𝑖            the search space of task 𝑡𝑖
                        𝒚 𝑡𝑖          the noisy evaluations of task 𝑡𝑖
                         𝜇𝑖           the mean function of task 𝑡𝑖
                         𝜆𝑖           the variance function of task 𝑡𝑖 , same as 𝜎 2
                        𝜎𝑖            the standard deviation function of task 𝑡𝑖
                         𝑘𝑖           the co-variance function of task 𝑡𝑖
                        𝒎𝑖            the meta-features that extract the feature of datasets for
                                      task 𝑡𝑖
                       𝒙 𝑡𝑖           a given point of task 𝑡𝑖

    To avoid confusion of transfer learning problem and its notations, we propose a unified setting
and notation for it.
    We consider 𝐾 source tasks, noted as 𝑡 1, 𝑡 2, ..., 𝑡𝐾 , and one target task, noted as 𝑡𝑇 . Our observations
are taken from these 𝐾 + 1 tasks as input, in which 𝐷𝑛𝑡11 , ..., 𝐷𝑛𝑡𝐾𝐾 are training history from 𝐾 source
tasks and 𝐷𝑡𝑡𝑇 is the observations in the target task at 𝑡-th iteration. The source task 𝑡𝑖 contains
                                                           𝑖
𝑛𝑖 evaluated points 𝐷𝑛𝑡𝑖𝑖 = {(𝒙 𝑡𝑗𝑖 , 𝑦𝑡𝑗𝑖 )}𝑛𝑗=1 . Unlike {𝐷𝑛𝑡𝑖𝑖 }𝑖=1     𝐾 that are obtained in previous tuning

procedures, the number of observations 𝑡 in 𝐷𝑡𝑡𝑇 grows along with the current training process.
    Given a task 𝑡𝑖 , we note its black-box function as 𝑓 𝑖 (𝑥) and its search space as X𝑖 . The noisy
evaluations 𝒚𝑡𝑖 of task 𝑡𝑖 follow a distribution as Eq.2 shows, which we note as 𝒚𝑡𝑖 ∼ N (𝒇 𝑡𝑖 , 𝜆𝑡𝑖 𝑰 ) in
transfer learning scenarios. We note 𝜇 1, ..., 𝜇 𝐾 , 𝜇𝑇 as mean functions, 𝑘 1, ..., 𝑘 𝐾 , 𝑘𝑇 as co-variance
functions, and 𝜆 1, ..., 𝜆𝐾 , 𝜆𝑇 as the posterior variance functions of each task. Given a data-set on
                                                     𝑖
task 𝑡 𝑖 , noted as 𝐷𝑛𝑡𝑖𝑖 = {(𝒙 𝑡𝑗𝑖 , 𝑦𝑡𝑗𝑖 )}𝑛𝑗=1 , the posterior of task on a given point 𝒙 𝑡𝑖 can be noted as
𝑓 𝑖 | 𝒙 𝑡𝑖 , 𝑿 𝑡𝑖 , 𝒚𝑡𝑖 ∼ N (𝜇𝑛𝑖 𝑖 (𝒙 𝑡𝑖 ), 𝜆𝑛𝑖 𝑖 (𝒙 𝑡𝑖 )), as Eq.3 show. Meanwhile, we note the meta-features that
extract the feature of datasets as 𝒎𝑖 = (𝑚𝑖1, ..., 𝑚𝑖𝐹 ) for task 𝑡𝑖 , assuming that we only consider 𝐹
meta-features for each tasks.
    Note that in our setting, the superscripts are always used to distinguish between different tasks.
Subscripts of points are used to distinguish between different points in a same task. Subscripts
of data-sets, mean functions, co-variance functions and variance functions are used to show the
number of input-output pairs when considering them.
    In this setting, the overall goal of transfer learning for Bayesian Optimization is to to find a global
minimizer (or maximizer) of an unknown function on the target task, based on the information we

                                                        J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

10                                                                                                                        Bai et al.

                                                                   Next point to evaluate

     Initial points   Initial points               Observations                             Mean and variance of   Acquisition
                                       Evaluator                    Surrogate model
       generator                                                                               given points         function

                                                                      Search space

                                           Fig. 1. Bayesian Optimization Framework.

know from previous estimates on 𝐾 tasks and current observation sets 𝐷 𝑡𝑇 :
                                                         ∗
                                                   𝒙 𝒕𝑻 = arg min 𝑓 𝑇 (𝒙),                                                       (14)
                                                                  𝒙 ∈X𝑇

3 IMPLEMENTATION OF TRANSFER LEARNING: OVERVIEW
3.1 Transfer Learning: Opportunity and Challenge
Traditional Bayesian optimization usually considers only one task, and requires sufficient evaluations of configurations to converge to a good result. Given a new task, traditional Bayesian
optimization re-optimize the task from scratch. This process may cost a lot of time and computational resources.
   In practice, researchers observed that similar tasks are likely to have similar response surface.
Therefore, leveraging information from source tasks provides an opportunity to accelerate the
searching process of the target task, and therefore reduce the time and computational resources.
However, leveraging information from source tasks to the target task is not simple. Challenges
mainly lie in:
     (1) How to properly use history tasks (source tasks): Before leveraging the information from
         history tasks to target task, it is necessary to carefully consider what to use and how to
         use. The first challenge is the heterogeneous scales and noise levels between different tasks.
         Besides, not all history tasks is helpful to the target task, it is also necessary to exclude those
         dissimilar tasks and only utilize those similar and helpful history tasks.
     (2) How to leverage information into the target task: As Fig. 1 shows, there are five parts in BO
         framework. In order to utilize the information from history tasks, one have to choose add the
         information to which part in BO and make sure that this action would not lead to too much
         additional time and computational resource.

3.2      Categories of Transfer Learning-Based Bayesian Optimization
In this survey, we propose a taxonomy to classify the existing transfer learning-based BO models
for the first time. As Fig.1 shows, we divide the BO framework into the following five parts:
     (1) Initial points generator: The initial points generator generates initial hyper-paramater
         configurations for Bayesian optimization process. Previous work focuses on finding out
         promising initial points to accelerate the searching process, as Sec.6 introduces.
     (2) Evaluator: The evaluator can be anything Bayesian optimization can be applied for. As
         we will introduce in Sec.8, the evaluator can be machine learning models with certain
         hyperparameters, databases with various knobs to tune, etc.

J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

Transfer Learning for Bayesian Optimization: A Survey                                                                 11

    (3) Surrogate model: As we introduced in Sec.2.2.1, the surrogate model is used to give out the
        marginal distribution of unknown points by fitting the observations we gained before. Previ-
        ous work focuses on leverage information from source tasks to help building the probabilistic
        model in this part. For details see Sec.4.
    (4) Acquisition function: The acquisition function aims to find out next promising points
        to evaluate, as we introduced in Sec.2.2.2. Similar to work that focuses on the Surrogate
        model, previous work also consider to leverage information of source tasks in the acquisition
        function to help searching, details are in Sec.5.
    (5) Search space: The search space is an finite scope designed for the acquisition function to
        search for the next evaluating points. Previous work focuses on finding out a more tightened
        and promising search space based on the information from source tasks to help accelerating
        the searching process, as Sec.7 introduces.

4     TRANSFER LEARNING FROM THE VIEW OF SURROGATE DESIGN
Among existing literature, most previous work focus on transfer learning techniques with specific
surrogate model designs. As introduced in Sec.2.2.1, those surrogate transfer methods can be
categorized based on their inner surrogate design. While the Gaussian Process (GP) is the most
common surrogate model used in BO, most of the previous surrogate transfer work depend on
GP as the surrogate model [4, 26, 34, 44, 47, 50, 61, 83, 85, 88, 89, 94, 103, 111, 113, 118, 120, 120,
124, 125, 128]. In addition to GP, there are also other surrogate designs, e,g, Bayesian Neural
Network[40, 81, 97, 100], Neural Processes[114], and Tree Parzen Estimators [99].

4.1    Gaussian Process as surrogate model
Gaussian Process is the most common surrogate model for Bayesian Optimization. When considering transfer learning from the source tasks to the target task through Gaussian Processes, there
exist usually three main problems to solve: (1) how to construct the kernel function between points
from different tasks; (2) how to set the GP prior; (3) how to deal with heterogeneous scales and
noise levels between different tasks.
   The most intuitive idea for transfer learning through GP is to put the datasets from source tasks
and the target task into a single GP model. Previous works consider kernel design, GP prior design
and response surface design to solve the three main problems mentioned above respectively. We
will introduce these methods in Sec.4.1.1, 4.1.2, 4.1.3 , respectively.
   Meanwhile, some methods consider learning the individual GP models of each source task, and
then learn an ensemble model based on those GP models for the target task. We will introduce
these methods in Sec.4.1.4.

4.1.1 Kernel Design. Kernel function is a vital part in GP model. As Sec.2.2.1 introduced, for the
traditional single-task BO model, the kernel function is usually pre-defined as Matěrn kernels,
Squared Exponential kernel and RBF kernel. However, in transfer learning problem, it is important
to make a difference between points from source tasks and the target task. Therefore, some kernel
design methods are proposed to additionally compute the difference between tasks.
   Multi-task kernel design is based on the setting that considers previous observations from source
tasks and target task together, and train the GP surrogate model with those observations and a
designed kernel function to compute the covariance between points from different tasks.
A. Multi-task Kernel Design. Several work put source tasks and target task together into one
GP model, and consider the difference between tasks to compute the kernel of this GP model. This
work can be summarize as Multi-task Kernel Design.

                                                        J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

12                                                                                                                                Bai et al.

                                                                                               Multi-task Kernel Design:
                                                                                               [103], [83], [128], [106], [61]

                                                  Kernel Design                                Noisy Biased Kernel design:
                                                                                               Env-GP [50], Diff-GP [94],
                                                                                               Task Selection [85]

                                                                                               Prior GP Mean and Kernel Design:
                                                                                               MetaBO [113], HyperBO [111]

       GP as surrogate model                       Prior Design
                                                                                               Deep Kernel Prior:
                                                                                               DKPD [120], [47], DKAF[44]

                                                 Data Scale Design                             [128], [120], [4], [88]

                                                                                               TST [89, 124, 125], RGPE [26],
                                                 Ensemble Design
                                                                                               TransBO [65], Google Vizier [34]

                                       Fig. 2. Summary of GP as Surrogate Model

  Swersky et al. [103] first propose a method called Multi-Task Gaussian Processes, in which they
define the multi-tasks kernel between different tasks by considering the correlation of tasks. The
multi-tasks kernel in this work is called intrinsic model of coregionalization :
                               𝐾𝑚𝑢𝑙𝑡𝑖 ((𝒙 𝑡𝑖 , 𝑡𝑖 ), (𝒙 𝑡 𝑗 , 𝑡 𝑗 )) = 𝐾𝑡 (𝑡𝑖 , 𝑡 𝑗 ) ⊗ 𝐾𝑥 (𝒙 𝑡𝑖 , 𝒙 𝑡 𝑗 ),                           (15)
where ⊗ means the Kronecker product, 𝐾𝑥 means the kernel between different input points, same
as the kernel in traditional GPs model, and 𝐾𝑡 measures the difference between tasks. In this work,
the parameters of 𝐾𝑡 was inferred using slicing sampling, specifically, 𝐾𝑡 is represented by Cholesky
factor and samples in the space. To leverage this multi-task kernel, this work assume that all tasks
are positively correlated.
   Poloczek et al. [83] also consider different tasks into single Gaussian Processes based on multitask kernel design. They rethink about the property of covariance and deduce that for points 𝒙 𝑡𝑖 in
task 𝑡𝑖 and 𝒙 𝑡 𝑗 in task 𝑡 𝑗 , the covariance function between them can be computed as follows:

                          𝑘 (𝒙 𝑡𝑖 , 𝒙 𝑡 𝑗 ) = 𝐶𝑜𝑣 (𝑓 𝑖 (𝒙 𝑡𝑖 ), 𝑓 𝑗 (𝒙 𝑡 𝑗 ))
                                          = 𝐶𝑜𝑣 (𝑓 𝑡 (𝒙 𝑡𝑖 ) + 𝛿 𝑖 (𝒙 𝑡𝑖 ), 𝑓 𝑡 (𝒙 𝑡 𝑗 ) + 𝛿 𝑗 (𝒙 𝑡 𝑗 ))
                                                                                                                                      (16)
                                          = 𝐶𝑜𝑣 (𝑓 𝑡 (𝒙 𝑡𝑖 ), 𝑓 𝑡 (𝒙 𝑡 𝑗 )) + 𝐶𝑜𝑣 (𝛿 𝑖 (𝒙 𝑡𝑖 ), 𝛿 𝑗 (𝒙 𝑡 𝑗 ))
                                          = 𝑘 𝑡 (𝒙 𝑡𝑖 , 𝒙 𝑡 𝑗 ) + 1𝑡𝑖 ,𝑡 𝑗  𝑘 𝑖 (𝒙 𝑡𝑖 , 𝒙 𝑡 𝑗 ),

where they assume 𝒇 in a joint Gaussian distribution and 𝛿 𝑖 (𝒙) = 𝑓 𝑖 (𝒙) − 𝑓 𝑇 (𝒙) is a bias with zero
expectation for task 𝑡𝑖 . Since they assume that 𝛿 𝑖 and 𝛿 𝑗 are independent iff 𝑖 ≠ 𝑗, the indicator
variable 1𝑡𝑖 ′ ,𝑡 𝑗 ′ is one if 𝑡𝑖 ′ = 𝑡 𝑗 ′ and 𝑡𝑖 ′ ≠ 𝑡𝑇 , and zero otherwise. Note that this property can be
deduced only if the predetermined kernel functions are linear.
  Yogatama and Mann [128] consider using multiple kernel to deal with points in the same task
and in different tasks. They use the Squared Exponential kernel for points in the same task and a
Nearest Neighbor kernel that consider points from the 𝑛 nearest neighbor tasks, which they find by
using Euclidean distance in the dataset feature space R𝑑 , and the dataset features are computed by
using the previous observations of each task. Specifically, the Nearest Neighbor kernel is defined as

J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

Transfer Learning for Bayesian Optimization: A Survey                                                                                   13

follows:                                  (                   𝑡𝑗
                                              1 − ∥𝒙 −𝒙            ∥2
                                                     𝑡𝑖
                            𝑡𝑖    𝑡𝑗                                    if 𝑡 𝑗 ∈ nearest neighbor task of 𝑡𝑖 ,
                       𝑘 (𝒙 , 𝒙 ) =                  𝐵                                                                                (17)
                                              0                         otherwise,
where 𝐵 is a bound that ∥𝒙 ∥ 2 ≤ 𝐵.
    Tighineanu et al. [106] propose a method that leverage the idea of boosting in machine learning.
Their method is called Boosted Hierarchical GP (BHGP), where they only consider one source
task, noted as 𝑡𝑠 . They add an additional term for the kernel of the query points, 𝑘 ∗ = 𝑘 𝑡 + Σ𝑏𝑜𝑜𝑠𝑡                               ∗     ,
while the additive term is computed as, Σ𝑏𝑜𝑜𝑠𝑡              ∗          =   Σ  𝑡𝑠
                                                                              ∗,∗  +  𝛼    Σ  𝑡𝑠 𝑇
                                                                                                  𝛼
                                                                                        ∗,𝑡 𝑡,𝑡 ∗,𝑡       − 𝛼    Σ𝑡𝑠
                                                                                                              ∗,𝑡 𝑡,∗ − Σ𝑡𝑠 𝑇
                                                                                                                             𝛼
                                                                                                                          ∗,𝑡 ∗,𝑡 , where
𝛼 ∗,𝑡 = 𝑘 𝑡 (𝒙 ∗, 𝑿 𝑡 ) (𝑘 𝑡 (𝑿 𝑡 , 𝑿 𝑡 ) + 𝜎𝑡2 1), and Σ𝑡𝑠 denotes the posterior covariance matrix of the source
task, which is evaluated on the points of the target task and the query points. Therefore, the kernel
can be computed as follows:
                                                       ∑︁
                                      𝑘 (𝒙 𝑖 , 𝒙 𝑗 ) =    [𝑾 𝑟 ] 𝑖,𝑗 𝑘 𝑟 (𝒙 𝑖 , 𝒙 𝑗 ) + 𝛿 𝒙 𝑖 𝒙 𝑗 𝛿𝑖 𝑗 𝜎𝑖2,                            (18)
                                                          𝑟

where 𝑖, 𝑗, 𝑟 ∈ {𝑡𝑠 , 𝑡, ∗}, [𝑾 𝑟 ] 𝑖,𝑗 = 𝛿𝑖𝑟 𝛿 𝑗𝑟 . Note that 𝛿𝑖 𝑗 = 1 if 𝑖 = 𝑗 and 0 otherwise for any 𝑖 and 𝑗.
Through adding this boosting term, their method reduces the computational complexity comparing
to the original multi-task BO.
   Also aim to design a multi-task kernel function, Law et al. [61] consider a specific condition
when utilizing BO to tune the hyperparameters of machine learning models. In this condition, each
                                                        𝑘
source task has two datasets, 𝐷 𝑡𝑘 = {(𝒙 𝑡𝑗𝑘 , 𝑦𝑡𝑗𝑘 )}𝑛𝑗=1 as the training data and training results of the
                                                                    𝑘
machine learning models, and {(𝜽 𝑡𝑗 𝑘 , 𝑧𝑡𝑗𝑘 )}𝑁𝑗=1 as the hyperparameters configurations and the noisy
evaluations of the black-box function 𝑓 𝑘 . Note that the notations here is little different from other
methods, where their goal is to find the best hyperparameters configuration for the target task, as
𝜽 𝑡𝑇 ∗ = arg min𝜽 ∈Θ𝑇 𝑓 𝑇 (𝜽 ).
    They also assume that all the source tasks and target task follow a same class of supervised
machine learning model, such that what makes the black-box function 𝑓 different from task to task
is only relied on the structure of inputs for the machine learning model, i.e. the input datasets 𝐷 𝑡𝑘 .
Therefore, they learn the representation of each dataset, where they decompose the dataset 𝐷 𝑡𝑘
                                                                             𝑡𝑘
into two parts, a joint distribution of the training data, noted as 𝐷 𝑡𝑘 ∼ P𝑋𝑌  , and the sample size 𝑛𝑘
for task 𝑡𝑘 . Specifically, they construct a feature map 𝜑 (𝐷 𝑡𝑘 ) on joint distributions for each task,
where they consider three distributions for different kinds of datasets, the marginal distribution
of 𝑋 as 𝑃𝑋 , conditional distribution 𝑃𝑌 |𝑋 , and the joint distribution 𝑃𝑋𝑌 . They first compute the
kernel mean embedding [77] as follows:
                                                                                𝑛
                                                                            1 ∑︁
                                                                               𝑘

                                                𝜑 (𝐷 𝑡𝑘 ) = 𝜇ˆ𝑃𝑋 =               𝜙𝑥 (𝒙𝑙 ),                                            (19)
                                                                           𝑛𝑘
                                                                                𝑙=1

and they compute the kernel conditional mean operator [98],
                   Ĉ𝑌 |𝑋 = Φ𝑇𝑦 (Φ𝑥 Φ𝑇𝑥 + 𝜆𝐼 ) −1 Φ𝑥 = 𝜆 −1 Φ𝑇𝑦 (𝐼 − Φ𝑥 (𝜆𝐼 + Φ𝑥 Φ𝑥 Φ𝑇𝑥 Φ𝑥 ) −1 Φ𝑇𝑥 )Φ𝑥 ,                             (20)
Meanwhile, they also compute the cross covariance operator [36],
                                                          𝑛
                                                   1 ∑︁
                                                      𝑘
                                                                               1
                                       Ĉ𝑋𝑌 =           𝜙𝑥 (𝒙𝑙 ) ⊗ 𝜙 𝑦 (𝑦𝑙 ) = Φ𝑇𝑥 Φ𝑦 ,                                               (21)
                                                  𝑛𝑘                          𝑛𝑘
                                                          𝑙=1

where 𝜙𝑥 , 𝜙 𝑦 are feature maps learned by neural networks, which is similar to the latent representation 𝜙 in deep kernel learning [118] (see Eq.32). They then use the kernel mean embedding, the
kernel conditional mean operator, and the cross covariance operator to estimate the dataset 𝐷 𝑡𝑘 .

                                                                        J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

14                                                                                                                          Bai et al.

While Φ𝑥 = [𝜙𝑥 (𝒙 1 ), ..., 𝜙𝑥 (𝒙𝑛𝑘 )], Φ𝑦 = [𝜙 𝑦 (𝑦1 ), ..., 𝜙 𝑦 (𝑦𝑛𝑘 )], and 𝜆 is a regularization parameter
they learned. They then flatten Ĉ𝑌 |𝑋 and Ĉ𝑋𝑌 to obtain the feature map 𝜑 (𝐷 𝑡𝑘 ) in each condition.
   Having the feature map 𝜑 (𝐷 𝑡𝑘 ) that represent the distribution of 𝐷 𝑡𝑘 , they then use Gaussian
process or Bayesian neural network (which we will discuss in details in Sec.4.2) to model the
objective function 𝑓 . For the GP model, they assume that 𝑓 ∼ N (𝜇, C), and the noisy evaluation
𝑧 | 𝜽 ∼ N (𝑓 (𝜽 ), 𝜎 2 ). Specifically, 𝜇 is a constant, and C is the kernel function corresponding to
(𝜽, P𝑋𝑌 , 𝑛) for each task, which is computed as
                         𝑡𝑖                    𝑡
                C({𝜽𝑖 , P𝑋𝑌 , 𝑛𝑡𝑖 }, {𝜽 𝑗 , P𝑋𝑌
                                              𝑗
                                                , 𝑛𝑡 𝑗 }) = 𝜐𝑘𝜽 (𝜽𝑖 , 𝜽 𝑗 )𝑘𝑝 ( [𝜑 (𝐷 𝑡𝑖 ), 𝑛𝑖 ], [𝜑 (𝐷 𝑡 𝑗 ), 𝑛 𝑗 ]),          (22)
where 𝜐 is a constant, and they use Matěrn 3/2-kernel for 𝑘𝑝 and 𝑘𝜽 , and the parameters are
optimized by using the marginal likelihood of GP.
   Following multi-task ABLR (which we will introduce in Sec.4.2), Law et al. [61] propose to
combine their method with multi-task ABLR. The only change they made is they replace 𝚽𝑘 in
Eq.45 with the vector 𝜸 = [𝜐 ( [𝜽 1𝑡1 , Ψ𝑡1 ]), ..., 𝜐 ( [𝜽 𝑁𝑡11 , Ψ𝑡1 ]), ..., 𝜐 ( [𝜽 1𝑡𝐾 , Ψ𝑡𝐾 ]), ..., 𝜐 ( [𝜽 𝑁𝑡𝐾𝐾 , Ψ𝑡𝐾 ])], where
𝜐 is a feature map, and Ψ𝑡𝑖 = [𝜑 (𝐷 𝑡𝑖 ), 𝑛𝑖 ].
B. Noisy Biased Kernel Design
   Also focused on the kernel construction, some works consider to view source tasks as noisy
observations of target task and develop Noisy biased kernel design. Joy et al. [50] first proposed a
method called Envelope-BO (also known as Env-GP in related works), where they view target task
in a noisy envelope of source task, and the size of the envelope depends on the correlation between
source task and target task. This work assumes source task and target task have same covariance
function 𝑘. And the covariance matrix between one source task 𝑡𝑠 and the target task is given as
follows follows:
                             𝐾 (𝑿 𝑡𝑠 , 𝑿 𝑡𝑠 ) + 𝜎𝑠2 𝑰𝑛𝑠 ×𝑛𝑠
                                                                                                         
                                                                              𝐾 (𝑿 𝑡𝑠 , 𝑿 𝑡𝑇 )
                     𝑲∗ =                                                                                   ,                     (23)
                                     𝐾 (𝑿 𝑡𝑇 , 𝑿 𝑡𝑠 )               𝐾 (𝑿 𝑡𝑇 , 𝑿 𝑡𝑇 ) + 𝜆𝑇 𝑰𝑛𝑇 ×𝑛𝑇

where 𝐾 (𝑿 𝑡𝑠 , 𝑿 𝑡𝑠 ) is a 𝑛𝑠 × 𝑛𝑠 matrix with 𝐾 (𝑿 𝑡𝑠 , 𝑿 𝑡𝑠 )𝑖,𝑗 = 𝑘 (𝒙𝑖𝑡𝑠 , 𝒙 𝑡𝑗𝑠 ) and the same goes for the
rest of three matrices. The key is to properly design the source noise variance 𝜎𝑠2 , which has to
increase when the similarity between source and target task decreases. This work considers an
adaptive form of 𝜎𝑠2 due to the fact that the correlation between source and target task can vary as
more evaluations are observed. They place an inverse gamma distribution with parameters 𝜏0 and
𝜐 0 as a prior distribution of 𝜎𝑠2 , and update 𝜎𝑠2 at each iteration using the observations of the target
task and the approximations of the source task on the selected points as follows:

                                                   𝜎𝑠2 ∼ InvGamma(𝜏0, 𝜐 0 ),
                                  𝑝 (𝜎𝑠2 | {𝑦𝑖𝑡𝑇 − 𝜇𝑛𝑠 𝑠 (𝒙𝑖𝑡𝑇 }𝑖=1
                                                                𝑡
                                                                    ) ∼ InvGamma(𝜏𝑡 , 𝜐𝑡 ).                                     (24)

They use the mode of the posterior distribution as the value of source noise variance, as 𝜎𝑠2 = 𝜏𝑡𝜐+1
                                                                                                   𝑡
                                                                                                      .
   Shilton et al. [94] proposed a similar algorithm called Diff-GP and proved it outperform the
Env-GP. The main difference between Env-GP and Diff-GP is that the former consider to update 𝜎𝑠2
to measure the correlation between tasks, while the latter consider to compute a distribution of a
new function to achieve this goal. They define a new function 𝑔(𝒙) = 𝑓 𝑇 (𝒙) − 𝑓 𝑠 (𝒙) to measure the
difference between source task and target task, and assume that this function follows a GP model,

                                        𝑔 | 𝒙, 𝑿 𝒕𝑻 , Δ𝑦 (𝑿 𝑡𝑇 ) ∼ N (𝜇𝑡 (𝒙), 𝜆𝑡 (𝒙)).
                                                                            𝑔        𝑔
                                                                                                                                (25)

J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

Transfer Learning for Bayesian Optimization: A Survey                                                                 15

   The noisy observation of function 𝑔(𝒙) is computed as Δ𝑦 (𝒙) = 𝑦𝑡𝑇 − 𝜇𝑛𝑠 𝑠 (𝒙), noted as Δ𝑦 (𝒙) =
𝑔(𝒙) + 𝜖𝑔 (𝒙), where 𝜖𝑔 (𝒙) ∼ N (0, 𝜆𝑇 + 𝜆𝑠 (𝒙)). Given a data-set Δ𝑦 (𝑿 𝑡𝑇 ) = {Δ𝑦 (𝒙𝑖𝑡𝑇 )}𝑖=1     𝑡 , the

posterior of 𝑔 on a given point 𝒙 can be computed as shown in Eq.3, where the prior mean here is
zero.
                                                            𝑔
   They then use the posterior mean function 𝜇𝑡 to correct the observation in source task, thus
transfer the observations from source task to target task. We note the predictive mean on the target
                                      𝑔
task as 𝝁 𝑡𝑇 = {𝜇𝑖𝑡𝑇 }𝑛𝑖=1
                        𝑠
                           = {𝑦𝑖𝑡𝑠 + 𝜇𝑡 (𝒙𝑖𝑡𝑠 )}𝑛𝑖=1
                                                  𝑠
                                                     . And the covariance matrix can be computed as follows:
                                                                                      
                                      𝐾 (𝑿 𝑡𝑠 , 𝑿 𝑡𝑠 ) + 𝚲𝑔       𝐾 (𝑿 𝑡𝑠 , 𝑿 𝑡𝑇 )
                              𝑲∗ =                                                       ,              (26)
                                         𝐾 (𝑿 𝑡𝑇 , 𝑿 𝑡𝑠 )      𝐾 (𝑿 𝑡𝑇 , 𝑿 𝑡𝑇 ) + 𝜆𝑇 𝑰
                                          𝑔                   𝑔
where 𝚲𝑔 is a diagonal matrix with 𝚲𝑖,𝑖 = 𝜆𝑛𝑠 𝑠 (𝒙𝑖𝑡𝑠 ) + 𝜆𝑡 (𝒙𝑖𝑡𝑠 ). Therefore, they can use this covariance
matrix and corrected observations to compute the posterior of GP model of the target task, as Eq.3
show.
     As these transfer learning methods can only transfer knowledge from one source task to the
target task at each iteration, it is important to choose the right task in each iteration to ensure the
efficiency. Due to this motivation, Ramachandran et al. [85] propose an additional mechanism to
actively select the optimal source for transfer learning based on Multi-arm bandit (MAB), and then
couple it with transfer learning methods (in this work they use Env-GP[50]).
     Specifically, they treat every source task as an arm (or a bandit), and they define a reward function
that measures the benefit gained from utilizing a certain source task to the transfer learning scenario.
Specifically, they assume 𝐾 source tasks with indexes 𝑘 = 1, ...𝐾, and they define a random variable
𝑟𝑡𝑘 , which means the reward when the 𝑘-th source task is selected at iteration 𝑡. In this work,
they set 𝑟𝑡𝑘 = −(𝑦𝑡𝑡𝑇 − 𝜇𝑘 (𝒙𝑡𝑡𝑇 )) 2 , where 𝒙𝑡𝑡𝑇 is the point selected in iteration t and 𝑦𝑡𝑡𝑇 is the noisy
observation on target task, 𝜇𝑘 is the mean function of the source task 𝑡𝑘 .
     Before starting the training process of the target task, they firstly train the GPs models of all the
source tasks as Eq.3 show. Then they train the target task using normal BO method (in this work they
use Env-GP) with mechanism of selecting the optimal source task in each iteration. Specifically, they
select the optimal source task 𝑠𝑡 at 𝑡-th iteration by finding the solution of 𝑠𝑡 = arg max𝑘=1,...,𝐾 𝑝𝑡𝑘 ,
where they use a weight strategy to compare the relatedness between different tasks,
                                                        𝜔 𝑘 (𝑡)    𝛾
                                         𝑝𝑡𝑘 = (1 − 𝛾) Í𝐾         + ,                                               (27)
                                                        𝑖=1 𝜔 (𝑡)
                                                              𝑖    𝐾

in which 𝛾 is a hyper-parameter chosen from (0, 1]. And 𝜔 𝑘 (𝑡) is a weight variable that update in
each iteration with 𝜔 𝑘 (1) = 1,
                                        (
                                    𝑘     𝑟𝑡𝑘 /𝑝𝑡𝑘 if 𝑘 = 𝑠𝑡 ,
                                  𝑟ˆ𝑡 =                                                        (28)
                                          0        otherwise,

                                                            𝛾 · 𝑟ˆ𝑡𝑘
                                        𝜔 𝑘 (𝑡 + 1) = 𝜔 𝑘 (𝑡) exp(   ),                             (29)
                                                              𝑀
the correlation between the 𝑘-th source task and the target task. They only update the weight of
selected source task in each iteration and remain the weight of other source tasks unchanged until
they are selected. Finally, they prove that using this reward function, their source selection strategy
based on MAB converges to the optimal source for transfer learning.
4.1.2 Prior Design. Above methods put evaluations from source tasks and target task together to
consider problem as multi-tasks or dual-tasks problem, and design the kernel that can be properly
used to compute the covariance of points from different tasks. While another line consider to use

                                                        J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

16                                                                                                           Bai et al.

the source tasks to learn the prior mean and(or) kernel function, thus implement transfer learning
for BO.
A. GP Mean and Kernel Prior Design
   Wang et al. [113] propose a method called MetaBO to leverage observations from source tasks
to train a prior mean and covariance function before the online training process of target task
begins. They assume that all the objective functions of different tasks are sampled from the same
GP prior distribution and are conditionally independent, and consider two conditions where the
search space X is either a finite set or a compact subset of R𝑑 . For both conditions, they first give
out an estimator of the prior mean and kernel based on the observations from source tasks, and
then give out an estimator to compute the posterior on the target task.
   When the search space is a finite set X = [𝒙 𝑗 ] 𝑀      𝑗=1 , the dataset from all source tasks can be noted
             𝑡𝑘 𝑡𝑘 𝑡𝑘 𝑀 𝐾                     𝑡𝑘
as 𝐷 = {[(𝒙 𝑗 , 𝛿 𝑗 𝑦 𝑗 )] 𝑗=1 }𝑘=1 , where 𝑦 𝑗 is computed by using the mean function from the GP
model N (𝑓 𝑘 (𝒙), 𝜎 2 ) trained on task 𝑡𝑘 , in which they assume the GP model for all tasks have a same
variance 𝜎. 𝛿 𝑡𝑗𝑘 ∈ {0, 1} denotes whether the experiment failed to compute function value, for this
value missing problem, they use the matrix completion technique in [15] to fill in the missing value
in the observation matrix 𝒀 = {𝑦𝑡𝑗𝑘 } 𝑗 ∈ [𝑀 ],𝑘 ∈ [𝐾 ] . Then they use the unbiased estimator for the prior
mean and kernel function, as 𝜇ˆ (X) = 𝐾1 𝒀 𝑇 1𝐾 and 𝑘ˆ (X) = 𝐾−1           1
                                                                              (𝒀 − 1𝐾 𝜇ˆ (X)𝑇 )𝑇 (𝒀 − 1𝐾 𝜇ˆ (X)𝑇 ),
where 𝜇ˆ (X) ∼ N (𝜇 (X), 𝐾1 (𝑘 (X) + 𝜎 2 )) and 𝑘ˆ (X) ∼ W ( 𝐾−1       1
                                                                          (𝑘 (X) + 𝜎 2 𝑰 ), 𝐾 − 1). Thus they can
leverage this prior mean and kernel function trained on the source tasks to construct the GP
posterior for the target task as Eq.3. They give out an unbiased estimator using 𝜇ˆ and 𝑘ˆ for the GP
posterior at 𝑡-th iteration on the target task as follows:

                          𝜇ˆ𝑡 (𝒙) = 𝜇ˆ (𝒙) + 𝑘ˆ (𝒙, 𝑿𝑡 )𝑘ˆ (𝑿𝑡 , 𝑿𝑡 ) −1 (𝒚 − 𝜇ˆ𝑡 (𝑿𝑡 )),
                                        𝐾 − 1 ˆ                                                                (30)
                     𝑘ˆ𝑡 (𝒙, 𝒙 ′) =                𝑘 (𝒙, 𝒙 ′) − 𝑘ˆ (𝒙, 𝑿𝑡 )𝑘ˆ (𝑿𝑡 , 𝑿𝑡 ) −1𝑘ˆ (𝑿𝑡 , 𝒙 ′) ,
                                     𝐾 −𝑡 −1
where 𝑿𝑡 = {𝒙 𝑗 }𝑡𝑗=1 .
   When the search space is a compact subset of R𝑑 , i.e. X ⊂ R𝑑 , they assume that there exists a
given basis function Φ(𝒙) = [𝜙𝑠 (𝒙)] 𝑠=1 : X → R𝑝 , a mean parameter 𝒖 ∈ R𝑝 , and a covariance
                                        𝑝

parameter Σ ∈ R𝑝×𝑝 , such that 𝜇 (𝒙) = Φ(𝒙)𝑇 𝒖 and 𝑘 (𝒙, 𝒙 ′) = Φ(𝒙)𝑇 ΣΦ(𝒙 ′). They also assume
that the observations 𝒚𝑡𝑘 = Φ(𝑿 )𝑇 𝑾 𝑡𝑘 + 𝝐 𝑡𝑘 ∼ N (Φ(𝑿 )𝑇 𝒖, Φ(𝑿 )𝑇 ΣΦ(𝑿 ) + 𝜎 2 𝑰 ), where 𝑾 𝑡𝑘 is the
linear operator of task 𝑡𝑘 , 𝑾 ∼ N (𝒖, Σ). Then if the matrix Φ(𝑿 ) ∈ R𝑝×𝑀 is reversible, the unbiased
estimator of 𝑾 𝑡𝑘 can be given as 𝑾 ˆ 𝑡𝑘 = (Φ(𝑿 )Φ(𝑿 )𝑇 ) −1 Φ(𝑿 )𝒚𝑡𝑘 ∼ N (𝒖, Σ + 𝜎 2 (Φ(𝑿 )Φ(𝑿 )𝑇 ) −1 ),
note W = [𝑾 ] 𝑖=1 . Therefore, as the basis function Φ(𝒙) is given, learning the mean and kernel
            ˆ 𝑡 𝑖 𝐾

function 𝜇 and 𝑘 is equivalent to learning the mean and covariance parameter 𝒖 and Σ. They
use the estimator 𝒖ˆ = 𝐾1 W𝑇 1𝐾 and Σ̂ = 𝐾−1    1
                                                    (W − 1𝐾 𝒖ˆ )𝑇 (W − 1𝐾 𝒖ˆ ) for 𝒖 and Σ, where 𝒖ˆ ∼
N (𝒖, 𝐾 (Σ +𝜎 (Φ(𝑿 )Φ(𝑿 ) ) )) and Σ̂ ∼ W ( 𝐾−1
       1      2               𝑇 −1                  1
                                                      (Σ +𝜎 2 (Φ(𝑿 )Φ(𝑿 )𝑇 ) −1 ), 𝐾 − 1). Similar to Eq.30,
they give out an estimator of the posterior of the linear operator 𝑾 ∼ N (𝒖𝑡 , Σ𝑡 ) at 𝑡-th iteration,

                        𝒖ˆ 𝑡 = 𝒖ˆ + Σ̂Φ(𝑿𝑡𝑡𝑇 ) (Φ(𝑿𝑡𝑡𝑇 )𝑇 Σ̂Φ(𝑿𝑡𝑡𝑇 )) −1 (𝒚𝑡𝑡𝑇 − Φ(𝑿𝑡𝑡𝑇 )𝑇 𝒖),
                                  𝐾 −1                                                                           (31)
                        Σ̂𝑡 =             ( Σ̂ − Σ̂Φ(𝑿 ) (Φ(𝑿𝑡𝑡𝑇 )𝑇 Σ̂Φ(𝑿𝑡𝑡𝑇 )) −1 Φ(𝑿𝑡𝑡𝑇 )𝑇 Σ̂),
                                𝐾 −𝑡 −1
where 𝑿𝑡𝑡𝑇 denotes the queried points at 𝑡-th iteration on the target task. Then the posterior mean
and variance on a given point 𝒙 can be given as 𝜇ˆ𝑡 (𝒙) = Φ(𝒙)𝑇 𝒖ˆ 𝑡 and 𝑘ˆ𝑡 (𝒙) = Φ(𝒙)𝑇 Σ̂𝑡 Φ(𝒙).
Moreover, for both conditions they shows that the regret bounds hold for BO.

J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

Transfer Learning for Bayesian Optimization: A Survey                                                                 17

   Building upon the method proposed by Wang et al. [113], Wang et al. [111] propose HyperBO,
while the former work requires that for all tasks the input points are same, which is not required
for the latter work. Their method views Bayesian optimization as a parameter-led process, where
they assume the task is defined by a parameter 𝜃 ∼ 𝑝 (𝜃 | 𝛼) and the variance 𝜎 ∼ 𝑝 (𝛼 | 𝜃 ), and the
GP model 𝜇, 𝑘 ∼ 𝑝 (𝜇, 𝑘 | 𝜃 ). Then as they make similar assumption as [113], the objective functions
for all tasks are viewed sampled independently from the GP model with 𝜇 and 𝑘. Therefore, in their
view, their transfer method is to leverage the previous source tasks to determine the parameters
in 𝜇, 𝑘, 𝜎, and set them as the GP prior for the target task. Specifically, they use two method to
determine the parameters, which is either to marginal the log likelihood, or considering an empirical
divergence between their defined multivariate Gaussian estimators and the true model predictions.
B. Deep Kernel Prior
   To learn a proper kernel function, some researchers consider the idea of deep kernel learning [118].
The main difference between deep kernels and the traditional kernels is that traditional kernels
give out the form of well-defined kernels and the hyper-parameters are learnt through training
process, while deep kernels use a neural network 𝜙 to learn a latent representation of 𝒙, and then
use this latent representation to define a kernel function as follows:

                               𝑘𝑑𝑒𝑒𝑝 (𝒙, 𝒙 ′ | 𝜽, 𝝎) = 𝑘 (𝜙 (𝒙, 𝝎), 𝜙 (𝒙 ′, 𝝎) | 𝜽 ).                               (32)

   Based on this idea, Wistuba and Grabocka [120] and Jomaa et al. [47] develop the Deep kernel
prior design. They consider to leverage a collection of source tasks using the few-shot learning
technique to learn the hyper-parameters of the deep kernel (Eq.32), thus transfer the parameterized
deep kernel to the target task. Specifically, they use the estimates 𝜽ˆ and 𝝎ˆ to approximate the
conditional distribution of 𝑓 ,
                                ∫
                𝑝 (𝑓 | 𝒙, 𝐷) =    𝑝 (𝑓 | 𝒙, 𝜽, 𝝎)𝑝 (𝜽, 𝝎 | 𝐷)𝑑𝜽, 𝝎 ≈ 𝑝 (𝑓 | 𝒙, 𝐷, ˆ
                                                                                 𝜽, ˆ
                                                                                    𝝎).        (33)

They use stochastic gradient ascent (SGA) to maximize the marginal likelihood of this distribution
at each iterations. Specifically, they use a batch of observations from one sampled source task at
each , to update the hyper-parameters, and get the final estimates after a given iteration time T.
   Iwata [44] also develop a deep kernel by combining RBF kernel with neural network as Eq.34
shows. Their proposed method is called Deep Kernel Acquisition Function (DKAF). Their model
contains three components, a neural network-based kernel, a Gaussian process, and a mutual
information based acquisition function. They define the RBF deep kernel as follows:
                                                                       
                                              1
                𝑘 (𝒙, 𝒙 ′ | 𝜽, 𝝎) = 𝛼 · 𝑒𝑥𝑝 − ∥𝜙 (𝒙, 𝝎) − 𝜙 (𝒙 ′, 𝝎) ∥ 2 + 𝛽 · 𝛿 (𝒙, 𝒙 ′),      (34)
                                             2𝜂
where 𝜽 = {𝛼, 𝛽, 𝜂} are parameters of the deep kernel, while 𝜙 (·, 𝝎) is the neural network with
parameter 𝝎. Different from the method proposed above by Wistuba and Grabocka [120], they
learn the parameters by treating BO process as Reinforcement Learning (RL) process and train the
parameters in neural networks and kernel using the source tasks. Specifically, for each iteration,
they first randomly sample a source task, and they run Bayesian optimization using their designed
Gaussian process with RBF deep kernel (as Eq.34 shows) to get the mean and variance function.
They convert the BO problem to RL setting, where evaluated data points is set as the state, point to
be evaluated next is the action, and a gap between true maximum value and the maximum value at
currently evaluated point is set as the negative reward. In this setting, they train the parameters by
using RL algorithm, i.e. leveraging the Policy Gradient method [102] and updating the parameters
by minimizing loss using a stochastic gradient method.

                                                        J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

18                                                                                                 Bai et al.

4.1.3 Data Scale Design. To deal with the problem of heterogeneous scale and noise levels between
different tasks, previous works consider to reconstruct the response surface. The most intuitive idea
to solve the scaling problem is simply standardize the function surface to a same scale. Yogatama
                                                                                  𝑓 𝑖 (𝒙 𝑗 )−𝜇𝑖
and Mann [128] simply standardize the response surface for task 𝑡𝑖 by 𝑦𝑡𝑗𝑖 =            𝜎𝑖
                                                                                                , where they
                                             √︃ Í
                     1 Í𝑛𝑖                      1
use estimates 𝜇ˆ = 𝑛𝑖 𝑗=1 𝑓 (𝒙𝑖 ) and 𝜎ˆ = 𝑛𝑖 𝑗=1 (𝑓 𝑖 (𝒙𝑖 ) − 𝜇ˆ𝑖 ) 2 to approximate 𝜇 and 𝜎 𝑖 . This
                 𝑖            𝑖          𝑖           𝑛𝑖                                       𝑖

technique is also applied in other work [89].
   Wistuba and Grabocka [120] consider another method to standardize the observations. They
firstly compute the maximum and minimum of observations for all tasks, noted as 𝑦𝑚𝑖𝑛 and 𝑦𝑚𝑎𝑥 .
For any given task 𝑡𝑖 , they sample a lower limit 𝑙 𝑖 and a upper limit 𝑢 𝑖 from uniform distribution,

                                      𝑙 ∼ U (𝑦𝑚𝑖𝑛 , 𝑦𝑚𝑎𝑥 ), 𝑢 ∼ U (𝑦𝑚𝑖𝑛 , 𝑦𝑚𝑎𝑥 )                       (35)

Then they use this lower limit and upper limit to standardize the observations of task 𝑡𝑖 , as
        𝑡
       𝑦 𝑖 −𝑙 𝑖
𝑦ˆ𝑡𝑗𝑖 = 𝑢𝑗𝑖 −𝑙 𝑖 .
     While another line take task differences into consideration, propose ranking-based response
surface reconstruction methods. Bardenet et al. [4] propose Scot algorithm to deal with this problem.
                                                                             𝑖
They firstly define a partial order for points in 𝐷𝑛𝑡𝑖𝑖 = {(𝒙 𝑡𝑗𝑖 , 𝑦𝑡𝑗𝑖 )}𝑛𝑗=1 :

                                              𝒙𝑖𝑡𝑘 ≺ 𝒙 𝑡𝑗𝑘 ⇐⇒ 𝑦𝑖𝑡𝑘 ≤ 𝑦𝑡𝑗𝑘                              (36)

   In their algorithm, they compute the partial order between one point and all the other points in
the same task. Then they use the Gaussian process-based ranking algorithm proposed by Chu and
Ghahramani [17] or 𝑆𝑉 𝑀 𝑅𝐴𝑁 𝐾 proposed by Joachims [46] to compute a reconstructed response
surface 𝑓ˆ𝑖 to estimate function value 𝑓 𝑖 . Different from evaluation 𝑦, for all tasks the estimates 𝑓ˆ
are in a same scale, thus the heterogeneous scaling problem is solved.
   Salinas et al. [88] propose a reconstructing method based on semi-parametric Gaussian Copulas [3,
117], where they use Gaussian Copulas to map the observations from different tasks to comparable
estimates. Specifically, they build a CDF 𝐹 (𝑦) with Winsorized cut-off estimator as
                                            𝛿             𝑖 𝑓 𝐹˜ (𝑡) < 𝛿 𝑁
                                            𝑁
                                           
                                           
                                           
                                    𝐹 (𝑡) ≈ 𝐹˜ (𝑡)         𝑖 𝑓 𝛿 𝑁 ≤ 𝐹˜ (𝑡) ≤ 1 − 𝛿 𝑁                  (37)
                                                           𝑖 𝑓 𝐹˜ (𝑡) > 1 − 𝛿 𝑁
                                           
                                           1 − 𝛿𝑁
                                           
                                           

where 𝐹˜ (𝑡) = 𝑁1 𝑖=1 1𝑦𝑖 ≤𝑡 , N is the number of observations 𝑦. Intuitively, this CDF is to replace
                  Í𝑁
observation 𝑦 by rank and then normalize it within a given task. With this CDF and the standard
normal CDF Φ−1 , they can obtain a new variable by mapping the observations through a bijection
𝑧 = 𝜙 (𝑦) = Φ−1 ◦ 𝐹 (𝑦), and 𝑧 follows a normal distribution. Then they compute the conditional
distribution of 𝑧,
                                            𝑃 (𝑧 | 𝒙) ∼ N (𝜇𝜃 (𝒙), 𝜎𝜃2 (𝒙)).                           (38)

Specifically, they minimize the Gaussian negative log-likelihood using the observations from 𝐾
source tasks with stochastic gradient descent method. Therefore, they can standardize variable 𝑧
                                            𝜙 (𝑦)−𝜇 (𝒙)
with deterministic functions 𝜇𝜃 and 𝜎𝜃2 , as 𝜎𝜃 (𝒙)𝜃 .

4.1.4 Ensemble Design. To deal with problems mentioned at the beginning of this section, another
line considers to build different tasks in separative GP models.

J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

Transfer Learning for Bayesian Optimization: A Survey                                                                   19

  Schilling et al. [89] propose POGPE to compute 𝐾 different source tasks as individual GP model
and then compute the weighted product of all individual likelihoods:
                                                        𝐾
                                                        Ö
                                                                𝛽
                                     𝑝 (𝒚 | 𝑿, 𝜃 ) =          𝑝𝑖 𝑖 (𝒚𝑡𝑖 | 𝑿 𝑡𝑖 , 𝜃 𝑡𝑖 ),                              (39)
                                                        𝑖=1
where they take 𝛽𝑖 = 1/𝐾 for every 𝑖. Therefore, the mean and variance of the objective function
on a given point can be computed as 𝜇 ∗ (𝒙) = 𝜆 ∗ (𝒙) 𝑖=1       𝛽𝑖 · (𝜆𝑖 ) −1 (𝒙) · 𝜇𝑖 (𝒙) and (𝜆 ∗ ) −1 (𝒙) =
                                                          Í𝐾
Í𝐾              −1
   𝑖=1 𝛽𝑖 · (𝜆 ) (𝒙). For the target task, they consider two methods, either considering target task
              𝑖

equal to the source tasks and take 𝛽𝑇 = 1/(𝐾 + 1) = 𝛽𝑖 or giving a higher weight to target task as
𝛽𝑇 = 1/2 and source tasks as 𝛽𝑖 = 1/𝐾.
    Wistuba et al. [124] consider a similar idea and build Two-Stage Surrogate model (TST). At the
first stage, they compute the individual GP models for both source tasks and target task similar to
the above method. At the second stage, they take task correlation into consideration and compute
the weight of each tasks as follows:
                                                     𝑘           
                                                     ∥𝝌 − 𝝌 𝑇 ∥ 2
                                       𝛽𝑘 = 𝛿 (𝑛𝑘 )                 ,                                      (40)
                                                         𝜌
where the Epanechnikov kernel 𝛿 (𝑡) is 34 (1 − 𝑡 2 ) if 𝑡 ≤ 1 and 0 otherwise, and 𝜌 > 0 is a given
parameter that shows the bandwidth. For task 𝑡𝑘 , 𝝌 𝑘 shows the feature of the task, which they use
either meta-features or pairwise ranking. The pairwise ranking for both target task and source
tasks is define as 𝝌 𝑘 with
                                             (
                               𝑘               1 if 𝜇𝑘 (𝒙𝑖 ) > 𝜇𝑘 (𝒙 𝑗 )
                             (𝝌 ) 𝑗+(𝑖−1)𝑡 =                                                    (41)
                                               0 otherwise
where 𝜇𝑘 is the mean Ífunction trained at the first stage. Therefore, the final mean function can be
                           𝐾
                           𝑖=1 𝛽𝑖 𝜇 (𝒙)+𝛽𝑇 𝜇 (𝒙)
                                   𝑖        𝑇
computed as 𝜇 ∗ (𝒙) =          Í𝐾 +1
                                      𝛽 +𝛽
                                                   and the variance function is simply defined as the variance
                                  𝑖=1 𝑖   𝑇
of the target task 𝜆𝑇 .
  In [125], they summarize their previous works product of GPs [89] and TST [124], and give out
some variant of above functions.

   Feurer et al. [26] propose Ranking-Weighted Gaussian Process Ensembles (RGPE) also based on the
idea of ensemble GPs. They first train the GP posterior for every source task, and use the posterior
mean function to compute the the number of misranked pairs between one source task and the
target task as follows:
                                    𝑛𝑇 ∑︁
                                       𝑛𝑇
                                          1 (𝜇𝑘 (𝒙𝑖𝑡𝑇 ) < 𝜇𝑘 (𝒙 𝑡𝑗𝑇 ) ⊕ 𝑦𝑖𝑡𝑇 < 𝑦𝑡𝑗𝑇 ),
                                    ∑︁
                         L (𝑓 𝑘 ) =                                                             (42)
                                       𝑖=1 𝑗=1

where ⊕ is the exclusive-or operator, and 1 is 1 if the logical expression is true and 0 otherwise.
Then they use this loss to compute the rank for each source task. Specifically, they draw 𝑆 samples
from the loss of all tasks, as 𝑙𝑠𝑘 ∼ L (𝑓 𝑘 ), and the weight of source task 𝑡𝑘 is computed as
                                                𝑆                   
                                            1 ∑︁
                                                  1 𝑘 = arg min 𝑙𝑠𝑘 ,
                                                                   ′
                                     𝛽𝑘 =                                                      (43)
                                            𝑆 𝑠=1           𝑘′

where they use this sample technique to consider the overall uncertainty of the source and target
models. Therefore, they can compute the mean and variance function at a given point 𝒙 of the
objective function as 𝜇 ∗ (𝒙) = 𝑘=1 𝛽𝑘 𝜇𝑘 (𝒙) and 𝜆 ∗ (𝒙) = 𝑘=1 (𝛽𝑘 ) 2 𝜆𝑘 (𝒙).
                               Í𝐾                          Í𝐾

                                                          J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

20                                                                                                Bai et al.

  They further propose a weight dilution technique to remove useless source tasks. They set the
weight as zero of given source task 𝑡𝑘 if the median of its loss samples 𝑙𝑠𝑘 is greater than 95𝑡ℎ
percentile of the loss samples of the target task 𝑙𝑠𝑇 . Furthermore, in [27] they give out a more
detailed version of RGPE and some variants of the above method.

   Li et al. [65] propose TransBO, which considers transfer learning for BO as a two-phase framework. Specifically, their two-phase framework learns the knowledge from source tasks in the first
phase, and aggregates the knowledge from source tasks with the observations in the target task
together in the second phase. Based on this two-phase framework, they can generate a combined
transfer learning surrogate model that leverages both information from source tasks and the target
task. And this model can help to choose the next promising configuration to evaluate in the next
iteration. They formulate their whole learning process into a constraint optimization problem,
which provides their algorithm theoretical guarantee.

   Golovin et al. [34] propose a multi-GPs model that is quite similar to the idea of Noisy biased
kernel design introduced in Sec.4.1.1-B, where they also consider to train a distribution on the
residual between the observed function value and the predicted value gained from the GP model
trained at previous recursion. Specifically, at 𝑘-th iteration, they utilize the mean function trained
at 𝑘 − 1-th iteration, noted as 𝜇𝑝𝑟𝑖𝑜𝑟 to train the GP model of the residual with dataset 𝐷𝑟𝑒𝑠𝑖𝑑𝑢𝑎𝑙𝑠
                                                                                             𝑘        =
    𝑡𝑘 𝑡𝑘             𝑡𝑘 𝑛𝑘
{(𝒙𝑖 , 𝑦𝑖 − 𝜇𝑝𝑟𝑖𝑜𝑟 (𝒙𝑖 )}𝑖=1 , and the mean and variance function of this residual GP model are noted
as 𝜇𝑡𝑜𝑝 and 𝜎𝑡𝑜𝑝 . And they use this residual GP model to correct the GP model trained at 𝑘 − 1-th
iteration to gain the predicted mean and variance of a given new point, as 𝜇 (𝒙) = 𝜇𝑝𝑟𝑖𝑜𝑟 (𝒙) +𝜇𝑡𝑜𝑝 (𝒙),
                                               𝛽      1−𝛽
𝛽 = 𝛼 ∥𝐷𝑘 ∥/(𝛼 ∥𝐷𝑘 + 𝐷𝑘−1 ) and 𝜎 (𝒙) = 𝜎𝑡𝑜𝑝 (𝒙)𝜎𝑝𝑟𝑖𝑜𝑟 (𝒙), where 𝜎𝑝𝑟𝑖𝑜𝑟 is the variance function
gained at 𝑘 − 1-th iteration. Note that here 𝛽 is a weight in which 𝛼 ≈ 1 measures the the relative
importance of the prior and top variance function.

4.2 Bayesian Neural Network as Surrogate Model
Due to the cubically-increasing computational complexity of GP mentioned in Sec.2.2.1, some work
consider to use Bayesian neural networks as an alternative to GP surrogate model, thus develop an
scalable form of BO.
   Snoek et al. [97] propose a scalable BO method for single task BO, which is called Deep Networks
for Global Optimization (DNGO), where they use neural networks to learn adaptive basis functions
for Bayesian linear regression. This work is based on adaptive Bayesian linear regression (ABLR) [9],
given a dataset D = {𝒙𝑖 , 𝑦𝑖 }𝑛𝑖=1 , the predictive mean and variance of a given point 𝜇𝑛 (𝒙) can be
computed as

                                 𝜇𝑛 (𝒙) = [𝛽𝑲 −1 𝚽𝑇 (𝒚 − 𝜇0 (𝒙))]𝑇 𝝓 (𝒙) + 𝜇0 (𝒙),
                                                             1                                        (44)
                                  𝜆(𝒙) = 𝝓 (𝒙)𝑇 𝑲 −1 𝝓 (𝒙) + ,
                                                             𝛽

where 𝑲 = 𝛽𝝓𝑇 𝝓 + 𝛼 𝑰 , 𝝓 (·) = [𝜙 1 (·), ..., 𝜙 𝐷 (·)]𝑇 is the outputs function from the last hidden layer
of the neural network, and 𝚽 is the matrix with 𝚽𝑛,𝑑 = 𝜙𝑑 (𝒙𝑛 ). The prior mean function 𝜇0 (𝒙) is
pre-designed containing our belief of the objective function.
   Perrone et al. [81] (also in [80]) propose a method for multiple tasks BO as an extension of DNGO.
Specifically, they model the surrogate of the black box function using Bayesian linear regression

J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

Transfer Learning for Bayesian Optimization: A Survey                                                                      21

for each task, as the following equations:
                                    𝒚𝑡𝑘 | 𝒘 𝑡𝑘 , 𝜽, 𝛽𝑘 ∼ N (𝚽𝑘 𝒘 𝑡𝑘 , 𝛽𝑘−1 𝑰𝑛𝑘 ),
                                                                                                                         (45)
                                            𝒘 𝑡𝑘 | 𝛼𝑘 ∼ N (0, 𝛼𝑘−1 𝑰𝐷 ),
where the linear regression weight 𝒘 𝑡𝑘 is treated as latent variable and integrated out, and 𝛼𝑘 , 𝛽𝑘 , 𝜽
are learned or given during the process. The matrix 𝚽𝑘 is similar to matrix in Eq.44 with 𝚽𝑘𝑛,𝑑 =
𝜙𝑑 (𝒙𝑛𝑡𝑘 ). Then they give out a multi-task ABLR posterior (similar to Eq.44) as follows:
                                             𝛽𝑘
                                   𝜇𝑛𝑘 (𝒙) =    (𝝓 (𝒙))𝑇 𝑲𝑘−1 (𝚽𝑘 )𝑇 ,
                                             𝛼𝑘
                                                                                                    (46)
                                              1
                                   𝜆𝑛𝑘 (𝒙) =    (𝝓 (𝒙))𝑇 𝑲𝑘−1 𝝓 (𝒙),
                                             𝛼𝑘
where similar to single-task ABLR, 𝝓 (·) = [𝜙 1 (·), ..., 𝜙 𝐷 (·)]𝑇 . They learn the parameters 𝜽 of the
neural network 𝝓 (𝒙) by minimizing the negative log marginal likelihood of multi-task ABLR as
follows:
                                                  𝐾
                                                 ∑︁
                                         𝐾
                         L (𝜽, {𝛼𝑘 , 𝛽𝑘 }𝑘=1 )=−       log 𝑃 (𝒚𝑡𝑘 | 𝜽, 𝛼𝑘 , 𝛽𝑘 ),                   (47)
                                                              𝑘=1
where 𝒚𝑡𝑘 | 𝜽, 𝛼𝑘 , 𝛽𝑘 ∼ N (𝒚𝑡𝑘 | 0, 𝛽𝑘−1 𝑰𝑛𝑘 + 𝛼𝑘−1 𝚽𝑘 (𝚽𝑘 )𝑇 ).
   Based on multi-task ABLR, Horváth et al. [40] apply nested dropout [87] to ABLR, and develop
multi-task ABLR with Adaptive Complexity (ABRAC). They propose a two-step procedure, where
they use the source tasks combing nested dropout to learn prior parameters of ABLR in offline
procedure, and then use normal multi-task ABLR method same as Eq.46 and 47 to deal with
target task in online procedure. Their surrogate model is similar to Eq.45, but they allow 𝛼𝑘 to
be different for different 𝑤 𝑡𝑘 , as 𝑝 (𝒘 𝑡𝑘 | 𝜶𝑘 ) = N (0, 𝑑𝑖𝑎𝑔(𝜶𝑘−1 )), which intuitively means that the
linear regression weights can have different precision. Specifically, for offline process, they apply
nested dropout to the ABLR as follows:
                                                   𝑑
                                                   Ö                                             𝛿
                                                         N ( [(𝝓𝑘 )𝑖↓ (𝑿 𝑡𝑘 )]𝑇 𝒘 𝑡𝑘 , 𝛽𝑘−1 𝑰𝑛𝑘 ) 𝑖𝑏𝑡
                                                                                                     𝑘
                       𝑝 (𝒚𝑡𝑘 | 𝒘 𝑡𝑘 , 𝜽, 𝛽𝑘 ) =                                                                         (48)
                                                   𝑖=1

where 𝛿𝑖𝑏𝑡𝑘 is the Kronecker delta, and 𝑏𝑡𝑘 ∼ 𝑈 (1, ..., 𝑑) is the truncation at 𝑡-th iteration. (𝝓𝑘 )𝑖↓ (·)
means vector function (𝝓𝑘 )𝑖↓ (·) = [𝜙 1 (·), ..., 𝜙𝑖 (·), 0, ...0]𝑇 . For online process, they also apply
Automatic Relevance Determination (ARD) to adjust the level of sparsity of the Bayesian linear
regression [107, 119].
  Also following multi-task ABLR, Law et al. [61] propose to combine TNP with multi-task ABLR,
which we have introduced in Sec.4.1.2.C.

   Springenberg et al. [100] propose a method named Bayesian Optimization with Hamiltonian
Monte Carlo Artificial Neural Networks (BOHAMIANN), which is based on stochastic Markov chain
Monte Carlo (MCMC) method from [16]. Specifically, they define the Bayesian neural networks
of multi-task model as 𝑓ˆ𝑘 (𝒙; 𝜃 𝜇 ) = ℎ [𝒙;𝜓𝑘 ]𝑇 , 𝜃ℎ for task 𝑡𝑘 , where ℎ(·) is the output of the
                                                      

neural network parameterized by 𝜃ℎ , and 𝜓𝑘 is the 𝑘-th row of an embedding matrix 𝜓 ∈ R𝐾×𝐿 .
The overall mean prior parameters of this neural networks can be noted as 𝜃 𝜇 = [𝜃ℎ , 𝑣𝑒𝑐 (𝜓 )],
where 𝑣𝑒𝑐 () means vectorization, and the variance prior parameters can be noted as 𝜃 𝜆𝑖 . In order to
compute the predictive posterior 𝑝 (𝑓 𝑘 (𝒙) | 𝒙, 𝐷), which is hard to evaluate with the choice of using
neural networks, they propose to use Stochastic Gradient Hamiltonian Monte Carlo (SGHMC) to
sample 𝜃 𝑖 ∼ 𝑝 (𝜃 | 𝐷) [16], and approximate the posterior with a summation as 𝑝 (𝑓 𝑘 (𝒙) | 𝒙, 𝐷) =

                                                             J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

22                                                                                                              Bai et al.

∫
  𝑝 (𝑓 𝑘 (𝒙) | 𝒙, 𝜃 )𝑝 (𝜃 | 𝐷)𝑑𝜃 ≈ 𝑀1
                                             Í𝑀
                                                𝑖=1 𝑝 (𝑓
                                                           𝑘 (𝒙) | 𝒙, 𝜃 𝑖 ). The posterior mean and variance can be
 𝜃
given as
                                                     𝑀
                                                1 ∑︁ ˆ𝑘
                                    𝜇𝑛𝑘 (𝒙) =         𝑓 (𝒙; 𝜃 𝜇𝑖 ),
                                                𝑀 𝑖=1
                                                                                                                    (49)
                                                     𝑀                       2
                                                1 ∑︁  ˆ𝑘
                                    𝜆𝑛𝑘 (𝒙) =         𝑓 (𝒙; 𝜃 𝜇𝑖 ) − 𝜇𝑛𝑘 (𝒙)) + 𝜃 𝜆𝑖 .
                                                𝑀 𝑖=1

4.3     Neural Process as surrogate models
Recent works also consider to leverage Neural Processes (NPs) [32, 33, 56] as a replacement of GPs
model to deal with the inefficiency problem of GPs [114]. NPs combine the best of both neural
networks and GPs to simultaneously trained with backpropagation and in a distribution, and have
been proved successful in recent studies [32, 114].
   Wei et al. [114] propose to use NPs as surrogate models and develop the transfer learning scenario.
Their method is called Transfer Neural Processes (TNP). The Neural Process usually contains three
components, an encoder E𝜽𝑒 that learns an embedding for every observation, a data-aware attention
unit A𝜽𝑎 that considers all the previous observations and gives a representation of them that has
invariant order, and a decoder D𝜽𝑑 that compute the predicted mean and variance for a given point.
The encoder and the decoder are both parameterized by neural networks.
   Formally, the neural process model can be noted as 𝑇 𝑁 𝑃𝜽 = E𝜽𝑒 ◦ A𝜽𝑎 ◦ D𝜽𝑑 . The overall
parameters are noted as 𝜽 = 𝜽𝑒 ∪ 𝜽𝑎 ∪ 𝜽𝑑 . Take the target task as an example, at 𝑡-th iteration the
target task contains 𝑛𝑇 + 𝑡 points that the first 𝑛𝑇 points are initialized points (we will discuss the
initialization technique later) and the last 𝑡 points are points queried at each iteration. Following
                                                                                                  𝑡𝑇
the idea from [29], this work randomly shuffles the observations into two parts, noted as 𝐷𝑡,ℎ        =
    𝑡𝑇 𝑡𝑇 ℎ            𝑡𝑇       𝑡𝑇 𝑡𝑇 𝑛𝑇 +𝑡
{(𝒙𝑖 , 𝑦𝑖 )}𝑖=1 and 𝐷𝑡,ℎ¯ = {(𝒙𝑖 , 𝑦𝑖 )}𝑖=ℎ+1 , and then the conditional log likelihood is
                          𝑡𝑇
                      L (𝐷𝑡,ℎ | 𝐷𝑡,𝑡𝑇ℎ¯ , 𝜽 ) = E 𝑓 ∼𝑃 [Eℎ [log 𝑝 𝜽 ({𝑦𝑖𝑡𝑇 }ℎ𝑖=1 | 𝐷𝑡,𝑡𝑇ℎ¯ , {𝒙𝑖𝑡𝑇 }ℎ𝑖=1 )]],       (50)
where the gradient of L is empirical estimated by sampling 𝑓 and different values of ℎ.
   The specific forms of three components are given as following. Note the learned embedding of
the encoder at point 𝒙𝑖𝑡𝑘 as 𝒓𝑖𝑡𝑘 = E𝜽𝑒 (𝒙𝑖𝑡𝑘 , 𝑦𝑖𝑡𝑘 ), and the data-aware attention unit is computed in
this work for a given point 𝒙 as
                                     𝒓 ∗ (𝒙) = A𝜃𝑎 (𝑹𝑡𝑇 , {𝒓𝑖𝑡1 }𝑛𝑖=1
                                                                   1
                                                                      , ..., {𝒓𝑖𝑡𝐾 }𝑛𝑖=1
                                                                                      𝐾
                                                                                         ; 𝒙)
                                             = MultiHead(𝑔(𝒙), 𝑔(𝑿 ), 𝑹, 𝒔)                                         (51)
                                                                               ⌊𝑟 /𝑑 ⌋
                                            = [head𝑑 (𝑔(𝒙), 𝑔(𝑿 ), 𝑹, 𝒔)]𝑑=1           ,
where 𝑹 = [𝑹𝑡𝑇 ; {𝒓𝑖𝑡1 }𝑛𝑖=1
                           1
                              , ..., {𝒓𝑖𝑡𝐾 }𝑛𝑖=1
                                              𝐾
                                                 ], and 𝑿 = [𝑿 𝑡𝑇 ; 𝑿 𝑡1 , ..., 𝑿 𝑡𝐾 ]. As previously discussed in
                                                                            𝑡𝑇                                    𝑇 +𝑡
Eq.50, when training the parameters 𝜃 by conditioning on 𝐷𝑡,ℎ                   , the datasets are 𝑹𝑡𝑇 = {𝒓𝑖𝑡𝑇 }𝑛𝑖=ℎ+1 ,
          𝑡𝑇 𝑛𝑇 +𝑡                   𝑡𝑇
𝑿 = {𝒙𝑖 }𝑖=ℎ+1 and 𝒙 ∈ 𝐷𝑡,ℎ , while when making prediction for a given target points 𝒙, the
  𝑡𝑇

                             𝑇 +𝑡                        𝑇 +𝑡
datasets are 𝑹𝑡𝑇 = {𝒓𝑖𝑡𝑇 }𝑛𝑖=1      and 𝑿 𝑡𝑇 = {𝒙𝑖𝑡𝑇 }𝑛𝑖=1    .
     In Eq.51, each head of the multi-head attention is computed as
                                                                             √
                               head𝑑 = softmax(𝒔 ◦ [𝑔(𝒙)]𝑇 𝑾𝑑1 [𝑾𝑑2 ]𝑇 𝑔(𝑿 )/ 𝑟 ),                                  (52)
where 𝑾𝑑1, 𝑾𝑑2 ∈ R𝑟 ×𝑑 are parameters. Different from previous works that considers to measure
the similarity between source tasks and target task through meta-features [28, 57] or partial
relationship [4], this work computes similarity through cosine similarity. Specifically, similarity

J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

Transfer Learning for Bayesian Optimization: A Survey                                                                       23

𝒔 = softmax( [11×(𝑛𝑇 +𝑡 −ℎ) , 𝑠 𝑡1 11×𝑛1 , ..., 𝑠 𝑡𝐾 11×𝑛𝐾 ]), where 11×𝑛𝑘 is the one vector with 𝑛𝑘 elements,
and the similarity between the target task and the task 𝑡𝑘 is computed through cosine similarity
               Í𝑛𝑇 +𝑡
as 𝑠 𝑡𝑘 = 𝑛𝑇1+𝑡 𝑖=1   cos(𝒓𝑖𝑡𝑇 , 𝑄1 𝑗 𝒓 𝑡𝑗𝑘 ). Thus given the representation 𝑟 ∗ and a given point 𝒙ˆ 𝑗 , the
                                    Í𝑄

decoder output the predicted mean and variance of the function value 𝑦 𝑗 at point 𝒙, as 𝜇 (𝒙), 𝜎 (𝒙) =
D𝜽𝑑 (𝒓 ∗, 𝒙).
   Before training on the target task, in order to achieve transfer from source tasks to target task,
the parameters 𝜽 are updated through pre-training on 𝐾 source tasks. Specifically, the parameters
𝜽 are randomly initialized as 𝜽˜ and updated at 𝑘-th iteration (which means that the source task 𝑡𝑘
is taken into consideration, and the following text has same meaning) as

                          𝜽𝑝𝑘 = 𝜽˜ − 𝛼 ▽𝜽 L (𝐷𝑛𝑡𝑘 ,ℎ | 𝐷𝑛𝑡𝑘 ,ℎ¯ , 𝜃 ), 𝜽˜ = 𝜽˜ + 𝜖 (𝜽𝑝𝑘 − 𝜽˜ )
                                        𝑝
                                                                                                                          (53)
                                                        𝑘         𝑘

where 𝑘 = 1, ..., 𝐾, 𝑝 denotes 𝑝 gradient steps, and the loss L is similar to Eq.50. At 𝑡-th iteration
when training on the target task, they also update the parameters as 𝜽𝑝 = 𝜽˜ −𝛼 ▽𝜽 L (𝐷𝑡,ℎ
                                                                                  𝑝      𝑡𝑇
                                                                                            | 𝐷𝑡,𝑡𝑇ℎ¯ , 𝜽 ).

  This work also considers a warm start method, their work has a similar idea with [123] in Sec.6,
which also considers to use source tasks to update a set of randomly initial points. Specifically, a
set of randomly initialized points {𝒙˜ 𝑖 }𝑛𝑖=1
                                            𝑇
                                               are updated through pre-training on 𝐾 source tasks, at
𝑘-th iteration, the points are update as
                             𝑝                 𝑝                                         𝑝
                           𝒙𝑖 = 𝒙˜ 𝑖 − 𝛼 ▽𝒙𝑖 L ({𝒙˜ 𝑖 }𝑛𝑖=1
                                                         𝑇
                                                            | 𝜃 ), 𝒙˜ 𝑖 = 𝒙˜ 𝑖 + 𝜖 (𝒙𝑖 − 𝒙˜ 𝑖 )                           (54)

    In this equation, the loss can be noted as
                                  𝑛𝑇
                                  ∑︁    exp(𝛼 · 𝜇𝑘 ( 𝒙˜ 𝑖 ))                     exp(𝛼 · 𝜎 𝑘 ( 𝒙˜ 𝑖 ))
          L ({𝒙˜ 𝑖 }𝑛𝑖=1
                      𝑇
                         | 𝜃) =       Í𝑛𝑇             𝑘 ( 𝒙˜ ))
                                                                𝜇 𝑘
                                                                    ( ˜
                                                                      𝒙 𝑖 ) + Í𝑛                𝑘 ( 𝒙˜ ))
                                                                                                          𝜎 𝑘 (𝒙˜ 𝑖 ),    (55)
                                  𝑖=1  𝑗=1 exp(𝛼 · 𝜇         𝑗
                                                                                 𝑇
                                                                                𝑗=1 exp(𝛼  · 𝜎         𝑗

where along with the update of parameters 𝜃 , the mean and variance update at each iteration,
which we note the mean and variance function at 𝑘-th iteration as 𝜇𝑘 , 𝜎 𝑘 . This loss ensures that at
least one initial point has the maximum mean and variance.

5     TRANSFER LEARNING FROM THE VIEW OF ACQUISITION FUNCTION DESIGN
While the above methods consider to implement transfer learning techniques through surrogate
model, some works also consider to transfer knowledge through acquisition function. This idea
arises as the transfer surrogate methods sometimes hard to deal with scaling problems. Meanwhile,
surrogate transfer methods usually neglect the fact that as more new observations are gained
in target task, the knowledge from source tasks become less important. Transferring knowledge
through acquisition function can avoid those two problems. Previous work considers transfer
learning for acquisition function from the view of multi-task BO [76, 103], ensemble-GPs [125],
and reinforcement learning [109].

5.1    Multi-task BO acquisition function
Swersky et al. [103] consider an acquisition function for multi-task BO based on entropy search.
Specifically, they take cost into consideration, and encourage evaluating configurations with large
information gained on the target task and low evaluation costs on the current task. The information
gain per unit cost is as a variant of Eq.13, which is computed as follows:
                                     𝐻 (𝒙 ∗ | 𝐷𝑛 ) − 𝐻 (𝒙 ∗ | 𝐷𝑛+1 )
                               ∫ ∫                                  
               𝛼 𝐸𝑆𝑛 (𝒙 𝑡𝑘 ) =                                         𝑝 (𝑦 | 𝑓 )𝑝 (𝑓 | 𝒙)𝑑𝑦𝑑 𝑓 , (56)
                                                 𝑐𝑘 (𝒙)

                                                              J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

24                                                                                                  Bai et al.

where 𝐻 (·) is the entropy on the target task, and we note 𝐷𝑛+1 = 𝐷𝑛 ∪ {(𝒙, 𝜇𝑛 (𝒙))}. As multi-task
BO method considers all tasks into one GP model, 𝐷 0 contains all data from source tasks. And
𝑐𝑘 (𝒙) is the real valued cost function of evaluating 𝒙 at task 𝑡𝑘 .
   Moss et al. [76] also consider a variant of entropy search for multi-task BO, they call it MUlti-task
Max-value Bayesian Optimization (MUMBO). Their work is based on Max-value Entropy Search
(MES) [112], and they also take fidelity 𝒛 into consideration. The general form of MUMBO is
                                             𝐻 (𝑦 | 𝐷𝑛 ) − E 𝑓 (𝒙) [𝐻 (𝑦 | 𝐷𝑛 ∪ {(𝒙, 𝜇𝑛 (𝒙))})]
                    𝛼 𝑀𝑈 𝑀𝐵𝑂𝑛 (𝒙 𝑘 , 𝒛) =                                                       ,       (57)
                                                                    𝑐𝑘 (𝒙, 𝒛)
which is similar to Eq.56, 𝑐𝑘 (𝒙, 𝒛) is the real valued cost function of evaluating 𝒙 with fidelity 𝒛 at
task 𝑡𝑘 . Besides, based on MES, they give out a computational form of Eq.57, as
                                             1          1 ∑︁              𝛾 𝑓 ∗ (𝒙)𝜙 (𝑍 𝑓 ∗ (𝒙))
                   𝛼 𝑀𝑈 𝑀𝐵𝑂𝑛 (𝒙 𝑘 , 𝒛) =             ·         𝜌 (𝒙, 𝒛) 2                        −
                                         𝑐𝑘 (𝒙, 𝒛) 𝑁 ∗                         2Φ(𝛾 𝑓 ∗ (𝒙))
                                                          𝑓 ∈𝐺
                                                         "        (                          )! #   (58)
                                                                    𝛾 𝑓 ∗ (𝒙) − 𝜌 (𝒙, 𝒛)𝜃
                      log(Φ(𝛾 𝑓 ∗ (𝒙))) + E𝜃 ∼𝑍 𝑓 ∗ (𝒙,𝒛) log Φ        √︁                         ,
                                                                           1 − 𝜌 (𝒙, 𝒛) 2
where Φ is the standard normal cumulative distribution and 𝜙 is the probability density functions,
           𝑓 ∗ −𝜇 𝑓 (𝒙)
𝛾 𝑓 ∗ (𝒙) = 𝜎 𝑓 (𝒙)     and 𝑓 ∗ = max𝒙 ∈X 𝑓 (𝒙), 𝑍 𝑓 ∗ (𝒙, 𝒛) is an extended-skew Gaussian (ESG) (for details
see [76]).

5.2    Ensemble GPs-based acquisition function transfer
Wistuba et al. [125] propose a method similar to TST [124], but consider to transfer knowledge
within acquisition function instead of surrogate model, which is called Transfer Acquisition Function
(TAF). In this method, they trian the individual GP models for each source task same as TST, then
they consider to leverage these knowledge to measure the improvement of a new point 𝒙 through
a variant of EI acquisition function (introduced in Sec.2.2.2):
                                                           Í𝐾
                                            𝛽𝑇 𝛼𝑇𝐸𝐼𝑛 (𝒙) + 𝑖=1  𝛽𝑖 𝐼𝑖 (𝒙)
                               𝛼𝑇 𝐴𝐹 (𝒙) =             Í𝐾                 ,                      (59)
                                                         𝑖=1 𝛽𝑖
                                                                                𝑡𝑘
where 𝛽𝑘 is same as how TST sets, see Eq.40 and Eq.41, and 𝐼𝑘 (𝒙) = 𝑚𝑎𝑥 {𝑦𝑚𝑖𝑛        − 𝜇𝑘 (𝒙), 0}. The
acquisition function 𝛼𝑇𝐸𝐼𝑛 (𝒙) is the EI with observations from target task at 𝑛-th iteration.

5.3    Reinforcement learning-based acquisition function transfer
Volpp et al. [109] consider the condition that the objective function of the target task share similar
structure with the objective functions of the source tasks, while the source tasks are much cheaper
to evaluate. They propose a hand-designed acquisition function called Neural Acquisition Function
(NAF) to achieve meta learning from the source tasks to the target task. Concretely, NAF is
parameterized by a vector 𝜃 , noted as 𝛼𝑡,𝜃 . They use the Proximal Policy Optimization (PPO)
algorithm from Reinforcement Learning (RL) to learn the vector in NAF. Hsieh et al. [41] also
propose a method using Reinforcement Learning. Different from Volpp et al. [109], their method
relies on deep Q-network (DQN) as differentiable surrogate of AF.

6     TRANSFER LEARNING FROM THE VIEW OF INITIALIZATION DESIGN
As the efficiency of BO depends on the initial points of the searching process, some works consider
to find proper initial points based on previous knowledge. These works can be summarized as

J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

Transfer Learning for Bayesian Optimization: A Survey                                                                       25

warm-start methods. To find the proper initial points, previous works focuses on three main
direction, measuring datasets similarities and choose initial points based on meta-features [25, 28,
57], generating initial points by gradient-based learning [114, 123], or generating initial points
using evolutionary algorithm[120].

6.1   Meta-features-based initialization
Feurer et al. [28] first propose an initialization method for BO which is called Meta-learning-based
Initialization Sequential Model-based Bayesian Optimization (MI-SMBO), which can be a plug-in
component for other different BO methods. They apply their initialization technique to the stateof-the-art SMBO method at that time, Spearmint and SMAC, using a comprehensive suite of 57
classification datasets and 46 meta-features, and gain significant improvements. Their method
considers a offline training to compute the meta-features of different source tasks (one known
dataset is viewed as one source task), and the best point of each source, noted as 𝒙ˆ 𝑡1 , ..., 𝒙ˆ 𝑡𝐾 for
datasets 𝐷 𝑡1 , ..., 𝐷 𝑡𝐾 . Before training on the target task (new dataset), they first compute the metafeatures of the target task, then compute the distance between each source task and the target task
to measure the similarity between them, using meta-features with p-norm distance
                                             𝑑𝑝 (𝐷 𝑡𝑘 , 𝐷 𝑡 𝑗 ) = ∥𝒎𝑘 − 𝒎 𝑗 ∥ 𝑝 ,                                         (60)
or negative Spearman correlation coefficient (Eq.61)
                   𝑑𝑐 (𝐷 𝑡𝑘 , 𝐷 𝑡 𝑗 ) = 1 − Corr( [𝑓 𝑘 (𝒙 1 ), ..., 𝑓 𝑘 (𝒙𝑛 )], [𝑓 𝑗 (𝒙 1 ), ..., 𝑓 𝑗 (𝒙𝑛 )]),            (61)
Finally, they sort the distance from small to large, and select the top 𝑡 best points from the first 𝑡
datasets as sorted, i.e. they choose the best points from 𝑡-nearest source tasks as the initial points
for the target task.
   Feurer et al. [25] add a warm-start component to their Automated Machine Learning (AutoML)
system, where the warm-start method is quite similar to the method they proposed above [28],
which is also based on meta-features but considers its application in specific condition.
   Also relied on meta-features to measure datasets similarity, especially similarity between image
datasets, and determine 𝑡-nearest source tasks, Kim et al. [57] propose to learn meta-features over
datasets using their trained deep feature and meta-feature extractors. They first randomly sample 𝜏
data from each dataset as a subsets to reduce computational complexity. As their work considers
datasets that are all image datasets, their proposed framework first extracts features of those image
data by using a deep feature extractor M𝑑 𝑓 , which is a deep neural networks, and output deep
           𝑡𝑘
features 𝒅 1:𝜏 = {𝒅𝑖𝑡𝑘 }𝜏𝑖=1 for task 𝑡𝑘 . Then the deep features are fed into a meta-feature extractor
M , which is either Aggregation of Deep Features (ADF)
   𝑚𝑓

                                                              𝜏                𝜏
                                                             ∑︁              1 ∑︁ 𝑡𝑘
                                        𝒉𝑡𝑘 := 𝒉𝑡𝐴𝐷𝐹
                                                 𝑘
                                                     =             𝒅𝑖𝑡𝑘 𝑜𝑟        𝒅 ,                                     (62)
                                                             𝑖=1
                                                                             𝜏 𝑖=1 𝑖
or Bi-directional Long Short-Term Memory network (Bi-LSTM)
                                       𝒉𝑡𝑘 := 𝒉𝑡𝐵𝑖−𝐿𝑆𝑇
                                                𝑘                      𝑡𝑘
                                                       𝑀 = Bi − LSTM(𝒅 1:𝜏 ),                                             (63)
and the output is 𝒉𝑡𝑘 for task 𝑡𝑘 .
  Finally, there exists a fully-connected layer after the meta-feature extractor to produce a metafeature vector for each tasks as 𝒎𝑘 for task 𝑡𝑘 . The parameters inÍtheir models are trained by
minimizing ∥𝑑𝑡𝑎𝑟𝑔𝑒𝑡 (𝐷𝑖 , 𝐷 𝑗 ) −𝑑𝑚𝑓 (𝒎𝑖 , 𝒎 𝑗 ) ∥, where 𝑑𝑡𝑎𝑟𝑔𝑒𝑡 (𝐷𝑖 , 𝐷 𝑗 ) = 𝑛𝑠=1 ∥ 𝑓 𝑖 (𝒙𝑠 ) − 𝑓 𝑗 (𝒙𝑠 ) ∥, which
shows the difference between two datasets. It is obvious that this method assumes that the response
surfaces of objective function for all tasks are in a same scale.

                                                              J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

26                                                                                                                                     Bai et al.

6.2     Gradient-based learning initialization
Wistuba et al. [123] (also in [122]) propose a method that does not depend on meta-feature, but
can directly learn the optimal initial points through iteration. They learn a set of initial points by
minimizing a defined meta loss,
                                                1 ∑︁
                                 L (𝑿 𝐼 , D) =          min 𝑓 𝑘 (𝒙),                              (64)
                                               𝐾 𝑡𝑘 𝒙 ∈𝑿 𝐼
                                                                                  𝐷   ∈D

where 𝑿 𝐼        = {𝒙 1, ..., 𝒙 𝐼 } denotes the set of initial points that contains 𝐼 points, dataset D =
{𝐷 𝑡1 , ..., 𝐷 𝑡𝐾 } is the dataset that contains all 𝐾 datasets of the source tasks. This meta loss is
not differentiable, thus this work propose to use differentiable softmin function to approximate it,
                                                                                  𝐼
                                                                            1 ∑︁ ∑︁
                                                   L (𝑿 𝐼 , D) =                     𝜎𝐷 𝑡𝑘 ,𝑖 𝑓ˆ𝑘 (𝒙𝑖 ),                                   (65)
                                                                            𝐾 𝑡𝑘
                                                                              𝐷   ∈D
                                                                                 𝑖=1

                                                                                                                     exp(𝛽 𝑓ˆ𝑘 (𝒙 ))
where 𝑓ˆ𝑘 = 𝜇𝑘 is the mean function from the GP model of task 𝑡𝑘 , 𝜎𝐷 𝑡𝑘 ,𝑖 = Í𝐼                        ˆ𝑘
                                                                                                           𝑖
                                                                                                                 , in which
                                                                                            𝑗 =1 exp(𝛽 𝑓 (𝒙 𝑗 ))

they choose 𝛽 = −100 such that the summation 𝑖=1 𝜎𝐷 𝑡𝑘 ,𝑖 𝑓ˆ𝑘 (𝒙𝑖 ) is close to min{ 𝑓ˆ𝑘 (𝒙 1 ), ..., 𝑓ˆ𝑘 (𝒙 𝐼 )}.
                                                    Í𝐼
In this form, the meta loss is differentiable. And the initial points can be randomly initialized and
updated as 𝑥𝑖,𝑗 = 𝑥𝑖,𝑗 − 𝜂 𝜕𝑥𝜕𝑖,𝑗 L (𝑿 𝐼 , D), where 𝑥𝑖,𝑗 is the 𝑗-th element of the vector 𝒙𝑖 . Moreover,
they also propose an adaptive form to take dataset similarity into consideration, as the following
equation:
                                                                     𝐼
                                            1 ∑︁                    ∑︁
                         L (𝑿 𝐼 , D) =             𝑐 (𝐷 𝑡𝑘 , 𝐷 𝑡𝑇 )     𝜎𝐷 𝑡𝑘 ,𝑖 𝑓ˆ𝑘 (𝒙𝑖 ),                            (66)
                                            𝐾 𝑡𝑘                    𝑖=1
                                                                    𝐷       ∈D
                                      Í
                                                𝑠 (𝒙𝑖 ,𝒙 𝑗 ,𝐷 𝑡𝑘 ,𝐷 𝑡𝑇 )
                                 𝒙𝑖 ,𝒙 𝑗 ∈𝑿 𝐼
where 𝑐 (𝐷 𝑡𝑘 , 𝐷 𝑡𝑇 ) :=                 ∥𝑿𝑡𝐼 ∥ ( ∥𝑿𝑡𝐼 ∥−1)
                                                                           , and the similarity 𝑠 is defined by the partial relationship
𝑠 (𝒙𝑖 , 𝒙 𝑗 , 𝐷 𝑡𝑘 , 𝐷 𝑡𝑇 ) :=   1   ( 𝑓ˆ𝑘 (𝒙𝑖 ) > 𝑓ˆ𝑘 (𝒙 𝑗 ) ⊕ 𝑓 𝑇 (𝒙𝑖 ) > 𝑓 𝑇 (𝒙 𝑗 )) similar to Eq.42, which shows the
number of misranked pairs.
   As Sec.4.3 has introduced, Wei et al. [114] also propose a warm start method based on Neural
Processes model, which is similar to the method above proposed by Wistuba et al. [123], see Eq.54
for more details.

6.3     Evolutionary algorithm based initialization
Wistuba and Grabocka [120] propose a warm start method based on evolutionary algorithm. They
use an evolutionary algorithm to find a set of points that can minimize the loss on the source tasks,
as the following equation:
                                                          𝐾                                   𝐾
                                                         ∑︁                                  ∑︁           𝑓 𝑘 (𝒙) − 𝑓min
                                                                                                                     𝑘
                            𝑿 𝐼 = arg min                       L (𝑓 𝑘 , 𝑿 ) = arg min             min      𝑘 − 𝑓𝑘
                                                                                                                           ,               (67)
                                           𝑿 ⊆X                                       𝑿 ⊆X         𝒙 ∈𝑿    𝑓max
                                                         𝑘=1                                 𝑘=1                 min

where 𝑓𝑚𝑖𝑛
         𝑘 and 𝑓 𝑘
                 𝑚𝑎𝑥 are the minimum and maximum of the function values considering all points
estimated so far (in 𝑿 and 𝐷 𝑡𝑘 ), while the function value at a previously unobserved point for the
task is estimated by using the mean function from the GP surrogate model trained before for each
source tasks. Specifically, the evolutionary algorithm works as follow. They first sample a set of 𝐼
random points with sampled proportion for each point (take point 𝒙 as an example) as follows:
                                                                        !
                                                         𝑓 𝑘 (𝒙) − 𝑓min
                                                                    𝑘
                                    exp − min               𝑘 − 𝑓𝑘
                                                                          .                     (68)
                                           𝑘 ∈ {1,...,𝐾 } 𝑓max
                                                                   min

J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

Transfer Learning for Bayesian Optimization: A Survey                                                                 27

Then the traditional evolutionary algorithm works, which randomly chooses to either do mutation
for the set to replace elements with new points, or perform a crossover operation between two sets
to generate new set with elements from both sets. Thus a new set is generated and added to the
population. They repeat this process for 100, 000 steps to find the best set as an initial set for the
target task.

7     TRANSFER LEARNING FROM THE VIEW OF SPACE DESIGN
Apart from surrogate model, acquisition function and warm-starting, some works also consider to
design a promising space for the target task based on the knowledge from source tasks[63, 82, 121].

7.1    Search space pruning
Wistuba et al. [121] first consider a search space pruning technique, i.e. pruning unpromising space
using the knowledge from source tasks to avoid unnecessary function evaluations. They define a
region R by a center point 𝒙 and a diameter 𝛿. They first evaluate task similarity by computing the
Kendall tau rank correlation coefficient [54],
                                              1 ( 𝑓ˆ𝑘 (𝒙𝑖 ) > 𝑓ˆ𝑘 (𝒙 𝑗 ) ⊕ 𝑓 𝑇 (𝒙𝑖 ) > 𝑓 𝑇 (𝒙 𝑗 ))
                                        Í
                                         𝒙𝑖 ,𝒙 𝑗 ∈𝑿𝑡
                 KTRC(𝐷 𝑡𝑘 , 𝐷 𝑡𝑇 ) :=                                                  ,                (69)
                                                      ∥𝑿𝑡 ∥ (∥𝑿𝑡 ∥ − 1)
where the numerator shows the number of misranked pairs, and 𝑿𝑡 is the set of already evaluated
hyperparameter configurations on the target task after 𝑡 trials. 𝑓ˆ𝑘 is approximated by the mean
function from the GP model of task 𝑡𝑘 , which is normalized to deal with the scaling problem. They
select 𝑚 source task that are most similar to the target task and note them as 𝑇 ′ = {𝑡𝑖1, ...., 𝑡𝑖𝑚 }. Then
they compute the defined potential that shows how promising a search space is, as the following
equation:                                             ∑︁
                    potential(R = (𝒙, 𝛿), 𝑿𝑡 ) :=             𝑓ˆ𝑘 (𝒙) − max
                                                                        ′
                                                                            𝑓ˆ𝑘 (𝒙 ′),                   (70)
                                                                                𝒙 ∈𝑿𝑡
                                                        𝑘 ∈ {𝑖1,...,𝑖𝑚 }
   Based on this defined potential, they select several hyperparameters with little potential and
note the set contains them as 𝑿 ′. Note the original search space as 𝑿 , then the pruned search space
is defined as:
                              𝑿 (pruned) := {𝒙 ∈ 𝑿 | 𝑑𝑖𝑠𝑡 (𝒙, 𝒙 ′) > 𝛿, 𝒙 ′ ∈ 𝑿 ′ }.                      (71)
   Considering the promising points, the returned space is 𝑿             (𝑝𝑟𝑢𝑛𝑒𝑑)                       ′
                                                                                   ∪ {𝒙 ∈ 𝑿 | 𝑑𝑖𝑠𝑡 (𝒙, 𝒙 ) ⩽
𝛿, 𝒙 ′ ∈ 𝑿 ′ }, where the distance is defined as follows:
                                 (
                           ′       ∞           if 𝒙 and 𝒙 ′ differ in a categorical variable,
                  dist(𝒙, 𝒙 ) :=                                                                          (72)
                                   ∥𝒙 − 𝒙 ′ ∥ otherwise,
where they consider especially the condition when changing a categorical variable, which makes
the loss not smoothly changed.

7.2    Promising search space design
While Perrone et al. [82] consider to design a promising search space for the target task instead of
pruning the original search space. They transfer the search space estimation problem to a constraint
optimization problem, as the following equation:
                            min𝑞 Q (𝜽 ) such that for 𝑘 ∈ {1, ..., 𝐾 }, 𝒙 𝑡𝑘 ∗ ∈ X̂(𝜽 ),                            (73)
                           𝜃∈   R
            ∗
where 𝒙 𝒕𝒌 = arg min𝒙 ∈X𝑡𝑘 𝑓 𝑘 (𝒙), and X̂ ⊂ X is a subset of the original search space defined by a
parameter vector 𝜽 . Q (𝜽 ) is volume measure of the search space X̂(𝜽 ).

                                                        J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

28                                                                                                         Bai et al.

  Specifically, they define two shape of the search space, box or ellipsoid. For the box space, the
parameter vector is 𝜽 = (𝒍, 𝒖), and the search space is designed as X̂(𝜽 ) = {𝒙 ∈ R𝑝 | 𝒍 ⩽ 𝒙 ⩽ 𝒖}.
The constraint optimization problem in Eq.73 can be varied as:
                                      1
                              min          ∥𝒖 − 𝒍 ∥ such that for 𝑘 ∈ {1, ..., 𝐾 }, 𝒍 ⩽ 𝒙 𝑡𝑘 ∗ ⩽ 𝒖,            (74)
                        𝒍∈   R𝑝 ,𝒖 ∈R𝑝 2
where 𝒍 is the lowest bound of the box, and 𝒖 is the highest bound of the box. This optimization
has a simple form of solution, 𝒍 ∗ = 𝑚𝑖𝑛{𝒙 𝑡𝑘 ∗ }𝑘=1
                                                 𝐾 and 𝒖 ∗ = 𝑚𝑎𝑥 {𝒙 𝑡𝑘 ∗ }𝐾 . To deal with outliers in
                                                                          𝑘=1
source datasets, this work also consider to add regularization parameter and slack variables to
Eq.74, which we will not introduce in details.
   For ellipsoid search space, the parameter vector is 𝜽 = (𝑨, 𝒃), where 𝑨 ∈ R𝑝×𝑝 is a symmetric positive definite matrix, and 𝒃 ∈ R𝑝 is an offset vector. The search space is designed as a
hyperellipsoid X̂(𝜽 ) = {𝒙 ∈ R𝑝 | ∥𝑨𝒙 + 𝒃 ∥ 2 ⩽ 1}. Thus the constraint optimization is defined as:

                      min            log det(𝑨−1 ) such that for 𝑘 ∈ {1, ..., 𝐾 }, ∥𝑨𝒙 𝑡𝑘 ∗ + 𝒃 ∥ 2 ⩽ 1.       (75)
              𝑨∈   R𝑝×𝑝 ,𝑨≻0,𝒃 ∈R𝑝
  In practice, they apply a rejection sampling to guarantee uniform sampling. They first sample
points uniformly in 𝑝-dimensional ball, and then they map the points into an ellipsoid. For more
details, please refer to [82].

   Also focused on search space design, Li et al. [63] propose to leverage the information of
similarities between different datasets to design a new search space for the problem, which has an
uncertain space, different from [82] using restricted geometrical shapes. Their main idea is that the
more similar is between the source task and the target task, the more information can be leveraged
from the source task to the target task. Based on that idea, they measure the similarities between
source tasks and the target task also using the Kendall tau rank correlation coefficient [54] as Eq.69,
and then use that similarity to compute a fractile to choose points from each source task. Finally, a
voting mechanism is used to combining the information from all source tasks to decide whether a
point will be included in the new search space for the target task.

8     APPLICATION SCENARIOS
With the increasing use of Bayesian optimization in application scenarios, transfer learning-based
methods can also be of use and help to reduce time and computational resources. Following we list
some application scenarios that can take advantage of the progress of the transfer learning-based
BO methods.

8.1    AutoML Tuning
Automated machine learning (AutoML) aims at tuning hyperparameters of machine learning models
or choosing proper operations to construct task-specific neural architectures. In practice, when
we need to tune a machine learning model, it is quite often that the model has been already tuned
on various history datasets. Transferring those knowledge saves the budget for re-training the
models on the new task, especially when the evaluation cost is quite large, e.g., training deep neural
networks or using huge datasets. Among the aforementioned literature, most methods [26, 65, 69,
89, 124] demonstrate powerful performance when transferring knowledge between the tuning
knowledge of traditional machine learning models (e.g., Adaboost, SVM) on tabular datasets. With
the support of NASBench-201 [20], recent work [63, 65] shows that transfer learning also finds
well-performed neural architectures quickly based on the tuning history of other datasets.

J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

Transfer Learning for Bayesian Optimization: A Survey                                                                 29

8.2    DBMS Tuning
Modern database management systems (DBMSs) have hundreds of configuration knobs that control
their runtime behaviors (e.g., resource management, query optimizer). Given a workload, DBMS
tuning aims to judiciously adjust the values of knobs to optimize the system performance. Tuning a
DBMS is expensive, since it requires DBMS copies, computing resources, and the infrastructure to
replay workloads and the tools to collect performance metrics [130]. Therefore, transfer learning
is adopted to leverage the tuning experience from the historical tasks and accelerate the tuning
process of the new tasks. Specifically, OtterTune[1] and OnlineTune [133] utilize the observations
from similar tuning tasks to train the target surrogate, which has shown to have better performance
than tuning from scratch. ResTune [131] adopts ensemble GPs (i.e., RGPE [26]) to speedup the
target tuning task.

8.3    Computing Platform Tuning
Big data computing platforms contain a huge number of parameters, for example, Hadoop [10] and
Spark [129] each have over 200 parameters [8, 51, 95]. Meanwhile, these platforms have incredible
scale and complexity, which requires system administrators to tune hundreds to thousands of
nodes [39]. To further accelerate the tuning process, transfer learning methods are taken into
consideration. For example, Wang et al. [110] warm-start the tuning process using configurations
in similar tasks. Tuneful [24] adopts Multi-Task Gaussian Processes [103] to utilize the most similar
history task.

9     FUTURE DIRECTION
While transfer learning-based BO has gained huge progress in recent years, there are still some
problems to be solved. In this section, we outline several promising prospective research directions.

9.1    Evaluation Analysis
Most previous work usually consider specific problems and use specific tasks to analyze their
methods. They only compare themselves with a few baselines, e.g., they usually compare with
classical methods like RGPE [26], TST [89], and multi-task BO [103], or even simple BO without the
transfer learning mechanism. Meanwhile, as different methods run tasks on the different application
environments, it is hard to measure the performance gap between methods based on different
experimental setups. Therefore, a comprehensive empirical analysis or a general benchmark is
required to perform a fair comparison among transfer learning methods.

9.2    Comprehensive Framework
As introduced in Sec. 3, the transfer learning for Bayesian optimization (TLBO) framework includes
four main parts, the surrogate model, the acquisition function, the initial search points, and the
search space. Most work consider only one aspect of this framework. We notice that the four
parts are orthogonal to each other, which means that there is an opportunity to combine different
methods into a comprehensive TLBO framework. To design such a framework, more challenges on
how to combine those components can be discovered and addressed in future work.

9.3    Generalized Transferable Information
Previous TLBO methods mainly transfer the observations or information (e.g., meta-features) in
history tasks. In practice, other types of knowledge can also be potential information to transfer,
e.g., low-fidelity results [23, 66] (evaluations with a proportion of time, epoches, data, etc.). How to

                                                        J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

30                                                                                                               Bai et al.

make use of other knowledge is also a promising direction to improve the performance of transfer
learning.

9.4    Combined Transfer Learning Method
To accelerate the convergence of neural networks, various previous work [115, 134] proposes
to transfer trainable parameters from previous models to the new one. However, the model still
requires tuning hyperparameters to achieve strong performance. While sharing the same spirit of
TLBO methods that . To further improve the model performance, it’s interesting to discover how
to perform a combined transfer of both trainable parameters and hyperparameters from previous
tasks.

10    CONCLUSION
In this paper, we provided an in-depth review of the transfer learning methods for Bayesian
optimization. First, based on “what to transfer” and “how to transfer”, we systematically divide
existing transfer learning works of Bayesian optimization into four categories: initial point design-
, search space design-, surrogate model-, and acquisition function-based approaches. For each
category, we presented the methodological design and technical descriptions in detail. In addition,
we investigated a general transfer learning framework for Bayesian optimization that considers all
the four aspects, which can be a guidance for developing new approaches. Finally, we showcased the
potential application scenarios, where the transfer learning approaches for Bayesian optimization
could work well.

REFERENCES
  [1] Dana Van Aken, Andrew Pavlo, Geoffrey J. Gordon, and Bohan Zhang. 2017. Automatic Database Management
      System Tuning Through Large-scale Machine Learning. In SIGMOD Conference. ACM, 1009–1024.
  [2] Omid Alipourfard, Hongqiang Harry Liu, Jianshu Chen, Shivaram Venkataraman, Minlan Yu, and Ming Zhang.
      2017. {CherryPick}: Adaptively Unearthing the Best Cloud Configurations for Big Data Analytics. In 14th USENIX
      Symposium on Networked Systems Design and Implementation (NSDI 17). 469–482.
  [3] Alec Anderson, Sebastien Dubois, Alfredo Cuesta-Infante, and Kalyan Veeramachaneni. 2017. Sample, estimate, tune:
      Scaling bayesian auto-tuning of data science pipelines. In 2017 IEEE International Conference on Data Science and
      Advanced Analytics (DSAA). IEEE, 361–372.
  [4] Rémi Bardenet, Mátyás Brendel, Balázs Kégl, and Michele Sebag. 2013. Collaborative hyperparameter tuning. In
      International conference on machine learning. PMLR, 199–207.
  [5] Guillermo Barrenetxea, François Ingelrest, Gunnar Schaefer, Martin Vetterli, Olivier Couach, and Marc Parlange. 2008.
      Sensorscope: Out-of-the-box environmental monitoring. In 2008 International Conference on Information Processing in
      Sensor Networks (ipsn 2008). IEEE, 332–343.
  [6] James Bergstra, Rémi Bardenet, Yoshua Bengio, and Balázs Kégl. 2011. Algorithms for hyper-parameter optimization.
      Advances in neural information processing systems 24 (2011).
  [7] Felix Berkenkamp, Andreas Krause, and Angela P Schoellig. 2021. Bayesian optimization with safety constraints: safe
      and automatic parameter tuning in robotics. Machine Learning (2021), 1–35.
  [8] Muhammad Bilal and Marco Canini. 2017. Towards automatic parameter tuning of stream processing systems. In
      Proceedings of the 2017 Symposium on Cloud Computing. 189–200.
  [9] Christopher M Bishop and Nasser M Nasrabadi. 2006. Pattern recognition and machine learning. Vol. 4. Springer.
 [10] Dhruba Borthakur. 2007. The hadoop distributed file system: Architecture and design. Hadoop Project Website 11,
      2007 (2007), 21.
 [11] Leo Breiman. 2001. Random forests. Machine learning 45, 1 (2001), 5–32.
 [12] Leo Breiman, Jerome H Friedman, Richard A Olshen, and Charles J Stone. 2017. Classification and regression trees.
      Routledge.
 [13] Eric Brochu, Tyson Brochu, and Nando De Freitas. 2010. A Bayesian interactive optimization approach to procedural
      animation design. In Proceedings of the 2010 ACM SIGGRAPH/Eurographics Symposium on Computer Animation.
      103–112.

J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

Transfer Learning for Bayesian Optimization: A Survey                                                                    31

 [14] Eric Brochu, Vlad M Cora, and Nando De Freitas. 2010. A tutorial on Bayesian optimization of expensive cost functions,
      with application to active user modeling and hierarchical reinforcement learning. arXiv preprint arXiv:1012.2599
      (2010).
 [15] Emmanuel J Candès and Benjamin Recht. 2009. Exact matrix completion via convex optimization. Foundations of
      Computational mathematics 9, 6 (2009), 717–772.
 [16] Tianqi Chen, Emily Fox, and Carlos Guestrin. 2014. Stochastic gradient hamiltonian monte carlo. In International
      conference on machine learning. PMLR, 1683–1691.
 [17] Wei Chu and Zoubin Ghahramani. 2005. Preference learning with Gaussian processes. In Proceedings of the 22nd
      international conference on Machine learning. 137–144.
 [18] Andrew R Conn, Katya Scheinberg, and Luis N Vicente. 2009. Introduction to derivative-free optimization. SIAM.
 [19] Benjamin Doerr, Carola Doerr, and Franziska Ebel. 2015. From black-box complexity to designing new genetic
      algorithms. Theoretical Computer Science 567 (2015), 87–104.
 [20] Xuanyi Dong and Yi Yang. 2020. Nas-bench-201: Extending the scope of reproducible neural architecture search.
      arXiv preprint arXiv:2001.00326 (2020).
 [21] Marco Dorigo and Thomas Stützle. 2019. Ant colony optimization: overview and recent advances. Handbook of
      metaheuristics (2019), 311–351.
 [22] Nick Erickson, Jonas Mueller, Alexander Shirkov, Hang Zhang, Pedro Larroy, Mu Li, and Alexander Smola. 2020.
      Autogluon-tabular: Robust and accurate automl for structured data. arXiv preprint arXiv:2003.06505 (2020).
 [23] Stefan Falkner, Aaron Klein, and Frank Hutter. 2018. BOHB: Robust and efficient hyperparameter optimization at
      scale. In International Conference on Machine Learning. PMLR, 1437–1446.
 [24] Ayat Fekry, Lucian Carata, Thomas F. J.-M. Pasquier, Andrew Rice, and Andy Hopper. 2020. To Tune or Not to Tune?:
      In Search of Optimal Configurations for Data Analytics. In KDD. ACM, 2494–2504.
 [25] Matthias Feurer, Aaron Klein, Katharina Eggensperger, Jost Springenberg, Manuel Blum, and Frank Hutter. 2015.
      Efficient and robust automated machine learning. Advances in neural information processing systems 28 (2015).
 [26] Matthias Feurer, Benjamin Letham, and Eytan Bakshy. 2018. Scalable meta-learning for bayesian optimization using
      ranking-weighted gaussian process ensembles. In AutoML Workshop at ICML, Vol. 7.
 [27] Matthias Feurer, Benjamin Letham, Frank Hutter, and Eytan Bakshy. 2018. Practical transfer learning for Bayesian
      optimization. arXiv preprint arXiv:1802.02219 (2018).
 [28] Matthias Feurer, Jost Springenberg, and Frank Hutter. 2015. Initializing bayesian hyperparameter optimization via
      meta-learning. In Proceedings of the AAAI Conference on Artificial Intelligence, Vol. 29.
 [29] Chelsea Finn, Pieter Abbeel, and Sergey Levine. 2017. Model-agnostic meta-learning for fast adaptation of deep
      networks. In International conference on machine learning. PMLR, 1126–1135.
 [30] Peter Frazier, Warren Powell, and Savas Dayanik. 2009. The knowledge-gradient policy for correlated normal beliefs.
      INFORMS journal on Computing 21, 4 (2009), 599–613.
 [31] Jacob R Gardner, Matt J Kusner, Zhixiang Eddie Xu, Kilian Q Weinberger, and John P Cunningham. 2014. Bayesian
      optimization with inequality constraints.. In ICML, Vol. 2014. 937–945.
 [32] Marta Garnelo, Dan Rosenbaum, Christopher Maddison, Tiago Ramalho, David Saxton, Murray Shanahan, Yee Whye
      Teh, Danilo Rezende, and SM Ali Eslami. 2018. Conditional neural processes. In International Conference on Machine
      Learning. PMLR, 1704–1713.
 [33] Marta Garnelo, Jonathan Schwarz, Dan Rosenbaum, Fabio Viola, Danilo J Rezende, SM Eslami, and Yee Whye Teh.
      2018. Neural processes. arXiv preprint arXiv:1807.01622 (2018).
 [34] Daniel Golovin, Benjamin Solnik, Subhodeep Moitra, Greg Kochanski, John Karro, and David Sculley. 2017. Google
      vizier: A service for black-box optimization. In Proceedings of the 23rd ACM SIGKDD international conference on
      knowledge discovery and data mining. 1487–1495.
 [35] Stewart Greenhill, Santu Rana, Sunil Gupta, Pratibha Vellanki, and Svetha Venkatesh. 2020. Bayesian optimization for
      adaptive experimental design: a review. IEEE access 8 (2020), 13937–13948.
 [36] Arthur Gretton. 2015. Notes on mean embeddings and covariance operators.
 [37] Philipp Hennig and Christian J Schuler. 2012. Entropy Search for Information-Efficient Global Optimization. Journal
      of Machine Learning Research 13, 6 (2012).
 [38] José Miguel Hernández-Lobato, Matthew W Hoffman, and Zoubin Ghahramani. 2014. Predictive entropy search for
      efficient global optimization of black-box functions. Advances in neural information processing systems 27 (2014).
 [39] Herodotos Herodotou, Yuxing Chen, and Jiaheng Lu. 2020. A survey on automatic parameter tuning for big data
      processing systems. ACM Computing Surveys (CSUR) 53, 2 (2020), 1–37.
 [40] Samuel Horváth, Aaron Klein, Peter Richtárik, and Cédric Archambeau. 2021. Hyperparameter transfer learning with
      adaptive complexity. In International Conference on Artificial Intelligence and Statistics. PMLR, 1378–1386.
 [41] Bing-Jing Hsieh, Ping-Chun Hsieh, and Xi Liu. 2021. Reinforced few-shot acquisition function learning for bayesian
      optimization. Advances in Neural Information Processing Systems 34 (2021), 7718–7731.

                                                           J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

32                                                                                                                Bai et al.

 [42] Frank Hutter, Holger H Hoos, and Kevin Leyton-Brown. 2011. Sequential model-based optimization for general
      algorithm configuration. In International conference on learning and intelligent optimization. Springer, 507–523.
 [43] Mahdi Imani and Seyede Fatemeh Ghoreishi. 2020. Bayesian optimization objective-based experimental design. In
      2020 American Control Conference (ACC). IEEE, 3405–3411.
 [44] Tomoharu Iwata. 2021. End-to-End Learning of Deep Kernel Acquisition Functions for Bayesian Optimization. arXiv
      preprint arXiv:2111.00639 (2021).
 [45] Haifeng Jin, Qingquan Song, and Xia Hu. 2019. Auto-keras: An efficient neural architecture search system. In
      Proceedings of the 25th ACM SIGKDD international conference on knowledge discovery & data mining. 1946–1956.
 [46] Thorsten Joachims. 2002. Optimizing search engines using clickthrough data. In Proceedings of the eighth ACM
      SIGKDD international conference on Knowledge discovery and data mining. 133–142.
 [47] Hadi Samer Jomaa, Sebastian Pineda Arango, Lars Schmidt-Thieme, and Josif Grabocka. 2021. Transfer Learning for
      Bayesian HPO with End-to-End Landmark Meta-Features. In Fifth Workshop on Meta-Learning at the Conference on
      Neural Information Processing Systems.
 [48] Donald R Jones. 2001. A taxonomy of global optimization methods based on response surfaces. Journal of global
      optimization 21, 4 (2001), 345–383.
 [49] Donald R Jones, Matthias Schonlau, and William J Welch. 1998. Efficient global optimization of expensive black-box
      functions. Journal of Global optimization 13, 4 (1998), 455–492.
 [50] Tinu Theckel Joy, Santu Rana, Sunil Gupta, and Svetha Venkatesh. 2019. A flexible transfer learning framework for
      Bayesian optimization with convergence guarantee. Expert Systems with Applications 115 (2019), 656–672.
 [51] Selvi Kadirvel and José AB Fortes. 2012. Grey-box approach for performance prediction in map-reduce based platforms.
      In 2012 21st International Conference on Computer Communications and Networks (ICCCN). IEEE, 1–9.
 [52] Kirthevasan Kandasamy, Willie Neiswanger, Jeff Schneider, Barnabas Poczos, and Eric P Xing. 2018. Neural architecture
      search with bayesian optimisation and optimal transport. Advances in neural information processing systems 31 (2018).
 [53] Borhan Kazimipour, Xiaodong Li, and A Kai Qin. 2014. A review of population initialization techniques for evolutionary
      algorithms. In 2014 IEEE congress on evolutionary computation (CEC). IEEE, 2585–2592.
 [54] Maurice G Kendall. 1938. A new measure of rank correlation. Biometrika 30, 1/2 (1938), 81–93.
 [55] RS Khurmi and JK Gupta. 2005. A textbook of machine design. S. Chand publishing.
 [56] Hyunjik Kim, Andriy Mnih, Jonathan Schwarz, Marta Garnelo, Ali Eslami, Dan Rosenbaum, Oriol Vinyals, and
      Yee Whye Teh. 2019. Attentive neural processes. arXiv preprint arXiv:1901.05761 (2019).
 [57] Jungtaek Kim, Saehoon Kim, and Seungjin Choi. 2017. Learning to warm-start Bayesian hyperparameter optimization.
      arXiv preprint arXiv:1710.06219 (2017).
 [58] Aaron Klein, Stefan Falkner, Simon Bartels, Philipp Hennig, and Frank Hutter. 2017. Fast bayesian optimization of
      machine learning hyperparameters on large datasets. In Artificial intelligence and statistics. PMLR, 528–536.
 [59] Bernhard H Korte, Jens Vygen, B Korte, and J Vygen. 2011. Combinatorial optimization. Vol. 1. Springer.
 [60] Harold J Kushner. 1964. A new method of locating the maximum point of an arbitrary multipeak curve in the presence
      of noise. (1964).
 [61] Ho Chung Law, Peilin Zhao, Leung Sing Chan, Junzhou Huang, and Dino Sejdinovic. 2019. Hyperparameter learning
      via distributional transfer. Advances in Neural Information Processing Systems 32 (2019).
 [62] Yang Li, Jiawei Jiang, Jinyang Gao, Yingxia Shao, Ce Zhang, and Bin Cui. 2020. Efficient automatic cash via rising
      bandits. In Proceedings of the AAAI Conference on Artificial Intelligence, Vol. 34. 4763–4771.
 [63] Yang Li, Yu Shen, Huaijun Jiang, Tianyi Bai, Wentao Zhang, Ce Zhang, and Bin Cui. 2022. Transfer Learning based
      Search Space Design for Hyperparameter Tuning. Proceedings of the 28th ACM SIGKDD Conference on Knowledge
      Discovery & Data Mining (2022).
 [64] Yang Li, Yu Shen, Huaijun Jiang, Wentao Zhang, Jixiang Li, Ji Liu, Ce Zhang, and Bin Cui. 2022. Hyper-Tune: Towards
      Efficient Hyper-Parameter Tuning at Scale. Proc. VLDB Endow. 15, 6 (2022), 1256–1265.
 [65] Yang Li, Yu Shen, Huaijun Jiang, Wentao Zhang, Zhi Yang, Ce Zhang, and Bin Cui. 2022. TransBO: Hyperparameter
      Optimization via Two-Phase Transfer Learning. Proceedings of the 28th ACM SIGKDD Conference on Knowledge
      Discovery & Data Mining (2022).
 [66] Yang Li, Yu Shen, Jiawei Jiang, Jinyang Gao, Ce Zhang, and Bin Cui. 2021. MFES-HB: Efficient Hyperband with Multi-
      Fidelity Quality Measurements. In Proceedings of the AAAI Conference on Artificial Intelligence, Vol. 35. 8491–8500.
 [67] Yang Li, Yu Shen, Wentao Zhang, Yuanwei Chen, Huaijun Jiang, Mingchao Liu, Jiawei Jiang, Jinyang Gao, Wentao
      Wu, Zhi Yang, et al. 2021. Openbox: A generalized black-box optimization service. In Proceedings of the 27th ACM
      SIGKDD Conference on Knowledge Discovery & Data Mining. 3209–3219.
 [68] Yang Li, Yu Shen, Wentao Zhang, Jiawei Jiang, Bolin Ding, Yaliang Li, Jingren Zhou, Zhi Yang, Wentao Wu, Ce Zhang,
      and Bin Cui. 2021. VolcanoML: Speeding up End-to-End AutoML via Scalable Search Space Decomposition. Proc.
      VLDB Endow. 14 (2021), 2167–2176.

J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

Transfer Learning for Bayesian Optimization: A Survey                                                                     33

 [69] Yang Li, Yu Shen, Wentao Zhang, Ce Zhang, and Bin Cui. 2022. Efficient End-to-End AutoML via Scalable Search
      Space Decomposition. The VLDB Journal (2022).
 [70] Daniel J Lizotte, Tao Wang, Michael H Bowling, Dale Schuurmans, et al. 2007. Automatic Gait Optimization With
      Gaussian Process Regression.. In IJCAI, Vol. 7. 944–949.
 [71] Yuzhe Ma, Ziyang Yu, and Bei Yu. 2019. CAD tool design space exploration via Bayesian optimization. In 2019
      ACM/IEEE 1st Workshop on Machine Learning for CAD (MLCAD). IEEE, 1–6.
 [72] Roman Marchant and Fabio Ramos. 2012. Bayesian optimisation for intelligent environmental monitoring. In 2012
      IEEE/RSJ international conference on intelligent robots and systems. IEEE, 2242–2249.
 [73] Ruben Martinez-Cantin, Nando de Freitas, Arnaud Doucet, and José A Castellanos. 2007. Active policy learning for
      robot planning and exploration under uncertainty.. In Robotics: Science and systems, Vol. 3. 321–328.
 [74] Jonas Močkus. 1975. On Bayesian methods for seeking the extremum. In Optimization techniques IFIP technical
      conference. Springer, 400–404.
 [75] Jonas Mockus, Vytautas Tiesis, and Antanas Zilinskas. 1978. The application of Bayesian methods for seeking the
      extremum. Towards global optimization 2, 117-129 (1978), 2.
 [76] Henry B Moss, David S Leslie, and Paul Rayson. 2020. Mumbo: Multi-task max-value bayesian optimization. In Joint
      European Conference on Machine Learning and Knowledge Discovery in Databases. Springer, 447–462.
 [77] Krikamol Muandet, Kenji Fukumizu, Bharath Sriperumbudur, Bernhard Schölkopf, et al. 2017. Kernel mean embedding
      of distributions: A review and beyond. Foundations and Trends® in Machine Learning 10, 1-2 (2017), 1–141.
 [78] John A Nelder and Roger Mead. 1965. A simplex method for function minimization. The computer journal 7, 4 (1965),
      308–313.
 [79] Vu Nguyen, Sunil Gupta, Santu Rana, Cheng Li, and Svetha Venkatesh. 2019. Filtering Bayesian optimization approach
      in weakly specified search space. Knowledge and Information Systems 60, 1 (2019), 385–413.
 [80] Valerio Perrone, Rodolphe Jenatton, Matthias Seeger, and Cedric Archambeau. 2017. Multiple adaptive Bayesian
      linear regression for scalable Bayesian optimization with warm start. arXiv preprint arXiv:1712.02902 (2017).
 [81] Valerio Perrone, Rodolphe Jenatton, Matthias W Seeger, and Cédric Archambeau. 2018. Scalable hyperparameter
      transfer learning. Advances in neural information processing systems 31 (2018).
 [82] Valerio Perrone, Huibin Shen, Matthias W Seeger, Cedric Archambeau, and Rodolphe Jenatton. 2019. Learning search
      spaces for bayesian optimization: Another view of hyperparameter transfer learning. Advances in Neural Information
      Processing Systems 32 (2019).
 [83] Matthias Poloczek, Jialei Wang, and Peter I Frazier. 2016. Warm starting Bayesian optimization. In 2016 Winter
      Simulation Conference (WSC). IEEE, 770–781.
 [84] Edward O Pyzer-Knapp. 2018. Bayesian optimization for accelerated drug discovery. IBM Journal of Research and
      Development 62, 6 (2018), 2–1.
 [85] Anil Ramachandran, Sunil Gupta, Santu Rana, and Svetha Venkatesh. 2018. Selecting optimal source for transfer
      learning in Bayesian optimisation. In Pacific Rim International Conference on Artificial Intelligence. Springer, 42–56.
 [86] Carl Edward Rasmussen. 2003. Gaussian processes in machine learning. In Summer school on machine learning.
      Springer, 63–71.
 [87] Oren Rippel, Michael Gelbart, and Ryan Adams. 2014. Learning ordered representations with nested dropout. In
      International Conference on Machine Learning. PMLR, 1746–1754.
 [88] David Salinas, Huibin Shen, and Valerio Perrone. 2020. A quantile-based approach for hyperparameter transfer
      learning. In International Conference on Machine Learning. PMLR, 8438–8448.
 [89] Nicolas Schilling, Martin Wistuba, and Lars Schmidt-Thieme. 2016. Scalable hyperparameter optimization with
      products of gaussian process experts. In Joint European conference on machine learning and knowledge discovery in
      databases. Springer, 33–48.
 [90] Bobak Shahriari, Alexandre Bouchard-Côté, and Nando Freitas. 2016. Unbounded Bayesian optimization via regular-
      ization. In Artificial intelligence and statistics. PMLR, 1168–1176.
 [91] Bobak Shahriari, Kevin Swersky, Ziyu Wang, Ryan P Adams, and Nando De Freitas. 2015. Taking the human out of
      the loop: A review of Bayesian optimization. Proc. IEEE 104, 1 (2015), 148–175.
 [92] Bobak Shahriari, Ziyu Wang, Matthew W Hoffman, Alexandre Bouchard-Côté, and Nando de Freitas. 2014. An
      entropy search portfolio for Bayesian optimization. arXiv preprint arXiv:1406.4625 (2014).
 [93] Joseph E Shigley, Charles R Mischke, and Thomas Hunter Brown Jr. 2004. Standard handbook of machine design.
      McGraw-Hill Education.
 [94] Alistair Shilton, Sunil Gupta, Santu Rana, and Svetha Venkatesh. 2017. Regret bounds for transfer learning in Bayesian
      optimisation. In Artificial Intelligence and Statistics. PMLR, 307–315.
 [95] Rekha Singhal and Praveen Singh. 2018. Performance assurance model for applications on SPARK platform. In
      Performance Evaluation and Benchmarking for the Analytics Era: 9th TPC Technology Conference, TPCTC 2017, Munich,
      Germany, August 28, 2017, Revised Selected Papers 9. Springer, 131–146.

                                                            J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

34                                                                                                                Bai et al.

 [96] Jasper Snoek, Hugo Larochelle, and Ryan P Adams. 2012. Practical bayesian optimization of machine learning
      algorithms. In Advances in neural information processing systems.
 [97] Jasper Snoek, Oren Rippel, Kevin Swersky, Ryan Kiros, Nadathur Satish, Narayanan Sundaram, Mostofa Patwary,
      Mr Prabhat, and Ryan Adams. 2015. Scalable bayesian optimization using deep neural networks. In International
      conference on machine learning. PMLR, 2171–2180.
 [98] Le Song, Kenji Fukumizu, and Arthur Gretton. 2013. Kernel embeddings of conditional distributions: A unified kernel
      framework for nonparametric inference in graphical models. IEEE Signal Processing Magazine 30, 4 (2013), 98–111.
 [99] Artur Souza, Luigi Nardi, Leonardo B Oliveira, Kunle Olukotun, Marius Lindauer, and Frank Hutter. 2021. Bayesian
      Optimization with a Prior for the Optimum. In Joint European Conference on Machine Learning and Knowledge
      Discovery in Databases. Springer, 265–296.
[100] Jost Tobias Springenberg, Aaron Klein, Stefan Falkner, and Frank Hutter. 2016. Bayesian optimization with robust
      Bayesian neural networks. Advances in neural information processing systems 29 (2016).
[101] Niranjan Srinivas, Andreas Krause, Sham M Kakade, and Matthias Seeger. 2009. Gaussian process optimization in the
      bandit setting: No regret and experimental design. arXiv preprint arXiv:0912.3995 (2009).
[102] Richard S Sutton, David McAllester, Satinder Singh, and Yishay Mansour. 1999. Policy gradient methods for rein-
      forcement learning with function approximation. Advances in neural information processing systems 12 (1999).
[103] Kevin Swersky, Jasper Snoek, and Ryan P Adams. 2013. Multi-task bayesian optimization. Advances in neural
      information processing systems 26 (2013).
[104] Kei Terayama, Masato Sumita, Ryo Tamura, and Koji Tsuda. 2021. Black-box optimization for automated discovery.
      Accounts of Chemical Research 54, 6 (2021), 1334–1346.
[105] William R Thompson. 1933. On the likelihood that one unknown probability exceeds another in view of the evidence
      of two samples. Biometrika 25, 3-4 (1933), 285–294.
[106] Petru Tighineanu, Kathrin Skubch, Paul Baireuther, Attila Reiss, Felix Berkenkamp, and Julia Vinogradska. 2022.
      Transfer Learning with Gaussian Processes for Bayesian Optimization. In International Conference on Artificial
      Intelligence and Statistics. PMLR, 6152–6181.
[107] Michael E Tipping. 2001. Sparse Bayesian learning and the relevance vector machine. Journal of machine learning
      research 1, Jun (2001), 211–244.
[108] Tsuyoshi Ueno, Trevor David Rhone, Zhufeng Hou, Teruyasu Mizoguchi, and Koji Tsuda. 2016. COMBO: An efficient
      Bayesian optimization library for materials science. Materials discovery 4 (2016), 18–21.
[109] Michael Volpp, Lukas P Fröhlich, Kirsten Fischer, Andreas Doerr, Stefan Falkner, Frank Hutter, and Christian
      Daniel. 2019. Meta-learning acquisition functions for transfer learning in bayesian optimization. arXiv preprint
      arXiv:1904.02642 (2019).
[110] Runzhe Wang, Qinglong Wang, Yuxi Hu, Heyuan Shi, Yuheng Shen, Yu Zhan, Ying Fu, Zheng Liu, Xiaohai Shi, and Yu
      Jiang. 2022. Industry practice of configuration auto-tuning for cloud applications and services. In Proceedings of the
      30th ACM Joint European Software Engineering Conference and Symposium on the Foundations of Software Engineering.
      1555–1565.
[111] Zi Wang, George E Dahl, Kevin Swersky, Chansoo Lee, Zelda Mariet, Zack Nado, Justin Gilmer, Jasper Snoek, and
      Zoubin Ghahramani. 2021. Automatic prior selection for meta Bayesian optimization with a case study on tuning
      deep neural network optimizers. arXiv preprint arXiv:2109.08215 (2021).
[112] Zi Wang and Stefanie Jegelka. 2017. Max-value entropy search for efficient Bayesian optimization. In International
      Conference on Machine Learning. PMLR, 3627–3635.
[113] Zi Wang, Beomjoon Kim, and Leslie P Kaelbling. 2018. Regret bounds for meta bayesian optimization with an
      unknown gaussian process prior. Advances in Neural Information Processing Systems 31 (2018).
[114] Ying Wei, Peilin Zhao, and Junzhou Huang. 2021. Meta-learning Hyperparameter Performance Prediction with Neural
      Processes. In International Conference on Machine Learning. PMLR, 11058–11067.
[115] Karl Weiss, Taghi M Khoshgoftaar, and DingDing Wang. 2016. A survey of transfer learning. Journal of Big data 3, 1
      (2016), 1–40.
[116] Christopher K Williams and Carl Edward Rasmussen. 2006. Gaussian processes for machine learning. Vol. 2. MIT press
      Cambridge, MA.
[117] Andrew G Wilson and Zoubin Ghahramani. 2010. Copula processes. Advances in Neural Information Processing
      Systems 23 (2010).
[118] Andrew Gordon Wilson, Zhiting Hu, Ruslan Salakhutdinov, and Eric P Xing. 2016. Deep kernel learning. In Artificial
      intelligence and statistics. PMLR, 370–378.
[119] David Wipf and Srikantan Nagarajan. 2007. A new view of automatic relevance determination. Advances in neural
      information processing systems 20 (2007).
[120] Martin Wistuba and Josif Grabocka. 2021. Few-shot bayesian optimization with deep kernel surrogates. arXiv preprint
      arXiv:2101.07667 (2021).

J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.

Transfer Learning for Bayesian Optimization: A Survey                                                                    35

[121] Martin Wistuba, Nicolas Schilling, and Lars Schmidt-Thieme. 2015. Hyperparameter search space pruning–a new
      component for sequential model-based hyperparameter optimization. In Joint European Conference on Machine
      Learning and Knowledge Discovery in Databases. Springer, 104–119.
[122] Martin Wistuba, Nicolas Schilling, and Lars Schmidt-Thieme. 2015. Learning Data Set Similarities for Hyperparameter
      Optimization Initializations.. In Metasel@ pkdd/ecml. 15–26.
[123] Martin Wistuba, Nicolas Schilling, and Lars Schmidt-Thieme. 2015. Learning hyperparameter optimization initializa-
      tions. In 2015 IEEE international conference on data science and advanced analytics (DSAA). IEEE, 1–10.
[124] Martin Wistuba, Nicolas Schilling, and Lars Schmidt-Thieme. 2016. Two-stage transfer surrogate model for automatic
      hyperparameter optimization. In Joint European conference on machine learning and knowledge discovery in databases.
      Springer, 199–214.
[125] Martin Wistuba, Nicolas Schilling, and Lars Schmidt-Thieme. 2018. Scalable gaussian process-based transfer surrogates
      for hyperparameter optimization. Machine Learning 107, 1 (2018), 43–78.
[126] Jin-ke Xiao, Wei-min Li, Wei Li, and Xin-rong Xiao. 2015. Optimization on black box function optimization problem.
      Mathematical Problems in Engineering 2015 (2015).
[127] Ya Xu, Nanyu Chen, Addrian Fernandez, Omar Sinno, and Anmol Bhasin. 2015. From infrastructure to culture: A/B
      testing challenges in large scale social networks. In Proceedings of the 21th ACM SIGKDD International Conference on
      Knowledge Discovery and Data Mining. 2227–2236.
[128] Dani Yogatama and Gideon Mann. 2014. Efficient transfer learning method for automatic hyperparameter tuning. In
      Artificial intelligence and statistics. PMLR, 1077–1085.
[129] Matei Zaharia, Mosharaf Chowdhury, Michael J Franklin, Scott Shenker, Ion Stoica, et al. 2010. Spark: Cluster
      computing with working sets. HotCloud 10, 10-10 (2010), 95.
[130] Xinyi Zhang, Zhuo Chang, Yang Li, Hong Wu, Jian Tan, Feifei Li, and Bin Cui. 2021. Facilitating Database Tuning
      with Hyper-Parameter Optimization: A Comprehensive Experimental Evaluation. The VLDB Journal (2021).
[131] Xinyi Zhang, Hong Wu, Zhuo Chang, Shuowei Jin, Jian Tan, Feifei Li, Tieying Zhang, and Bin Cui. 2021. ResTune:
      Resource Oriented Tuning Boosted by Meta-Learning for Cloud Databases. In SIGMOD Conference. ACM, 2102–2114.
[132] Xinyi Zhang, Hong Wu, Yang Li, Jian Tan, Feifei Li, and Bin Cui. 2022. Towards Dynamic and Safe Configuration
      Tuning for Cloud Databases. SIGMOD (2022).
[133] Xinyi Zhang, Hong Wu, Yang Li, Jian Tan, Feifei Li, and Bin Cui. 2022. Towards Dynamic and Safe Configuration
      Tuning for Cloud Databases. In SIGMOD Conference. ACM, 631–645.
[134] Fuzhen Zhuang, Zhiyuan Qi, Keyu Duan, Dongbo Xi, Yongchun Zhu, Hengshu Zhu, Hui Xiong, and Qing He. 2020. A
      comprehensive survey on transfer learning. Proc. IEEE 109, 1 (2020), 43–76.
[135] Marc-André Zöller and Marco F Huber. 2019. Survey on automated machine learning. arXiv preprint arXiv:1904.12054
      9 (2019), 844.

                                                           J. ACM, Vol. 1, No. 1, Article . Publication date: February 2023.
