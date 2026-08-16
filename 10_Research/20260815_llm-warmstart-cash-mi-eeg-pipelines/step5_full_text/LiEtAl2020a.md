---
citation_key: "LiEtAl2020a"
title: "Efficient Automatic CASH via Rising Bandits"
authors: "Yang Li; Jiawei Jiang; Jinyang Gao; Yingxia Shao; Ce Zhang; B. Cui"
year: 2020
doi: "10.1609/aaai.v34i04.5910"
source: "arXiv (2012.04371)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "2012.04371"
conversion: "pdftotext -layout (automated)"
---

# Efficient Automatic CASH via Rising Bandits

Efficient Automatic CASH via Rising Bandits

                                                        Yang Li,1 Jiawei Jiang,2 Jinyang Gao,3 Yingxia Shao,4 Ce Zhang,2 Bin Cui1
                                          1
                                              Key Laboratory of High Confidence Software Technologies (MOE), School of EECS, Peking University, Beijing, China
                                                                 2
                                                                   Department of Computer Science, Systems Group, ETH Zurich, Switzerland
                                                                     3
                                                                       Beijing University of Posts and Telecommunications, Beijing, China
                                                                                       4
                                                                                         Alibaba Group, Hangzhou, China
                                                                    {liyang.cs, bin.cui}@pku.edu.cn, {jiawei.jiang, ce.zhang}@inf.ethz.ch
                                                                               jinyang.gjy@alibaba-inc.com, shaoyx@bupt.edu.cn

arXiv:2012.04371v1 [cs.LG] 8 Dec 2020
                                                                   Abstract                                machine learning (Quanming et al. 2018; Zöller and Huber
                                                                                                           2019) has attracted lots of interest from both industry and
                                          The Combined Algorithm Selection and Hyperparameter op-
                                                                                                           academia.
                                          timization (CASH) is one of the most fundamental problems
                                          in Automatic Machine Learning (AutoML). The existing                Given a learning problem, the first thing is to decide
                                          Bayesian optimization (BO) based solutions turn the CASH         which ML algorithm should be applied – from SVM, Ad-
                                          problem into a Hyperparameter Optimization (HPO) problem         aboost, GBDT (Jiang et al. 2018; Jiang et al. 2017) to
                                          by combining the hyperparameters of all machine learning         deep neural networks. According to the No Free Lunch
                                          (ML) algorithms, and use BO methods to solve it. As a re-        theorem (Ho and Pepyne 2001), no single ML algorithm
                                          sult, these methods suffer from the low-efficiency problem       can achieve the best performance for all the learning prob-
                                          due to the huge hyperparameter space in CASH. To allevi-         lems; and there is often no golden standard to predict which
                                          ate this issue, we propose the alternating optimization frame-
                                          work, where the HPO problem for each ML algorithm and the
                                                                                                           ML algorithm performs the best. As a result, we typically
                                          algorithm selection problem are optimized alternately. In this   spend computational resources across all reasonable ML al-
                                          framework, the BO methods are used to solve the HPO prob-        gorithms, and choose the one with the best performance
                                          lem for each ML algorithm separately, incorporating a much       after the optimization of their hyperparameters and net-
                                          smaller hyperparameter space for BO methods. Furthermore,        work architectures. However, solving the algorithm selec-
                                          we introduce Rising Bandits, a CASH-oriented Multi-Armed         tion problem after sufficiently optimizing the hyperparam-
                                          Bandits (MAB) variant, to model the algorithm selection in       eters of each ML algorithm leads to inefficient usage of
                                          CASH. This framework can take the advantages of both BO          computational resources. Resources consumed by the poor-
                                          in solving the HPO problem with a relatively small hyper-        performing algorithms are greatly wasted. To this end, the
                                          parameter space and the MABs in accelerating the algorithm       Combined Algorithm Selection and Hyperparameter opti-
                                          selection. Moreover, we further develop an efficient online
                                          algorithm to solve the Rising Bandits with provably theoret-
                                                                                                           mization (CASH) problem (Feurer et al. 2015; Kotthoff et
                                          ical guarantees. The extensive experiments on 30 OpenML          al. 2017) is proposed to jointly optimize the selection of al-
                                          datasets demonstrate the superiority of the proposed approach    gorithm and its hyperparameters, which is the core focus of
                                          over the competitive baselines.                                  this paper.
                                                                                                              To solve the CASH problem, a class of methods (Komer,
                                                                                                           Bergstra, and Eliasmith 2014; Feurer et al. 2015; Kotthoff
                                                               Introduction                                et al. 2017) transform the CASH problem into a unified hy-
                                        Machine learning (ML) has made great strides in many               perparameter optimization (HPO) problem by merging the
                                        application areas, e.g., recommendation, computer vision,          hyperparameter space for all ML algorithms and treating the
                                        financial market analysis, etc (Goodfellow, Bengio, and            selection of algorithm as a new hyperparameter. Then clas-
                                        Courville 2016; He et al. 2017; Ma et al. 2019; Zhao, Shen,        sical Bayesian optimization (BO) methods (Shahriari et al.
                                        and Huang 2019). However, given a practical application,           2015) are utilized to solve this HPO problem. Consequently,
                                        it is usually knowledge-intensive and labor-intensive to de-       these methods incorporate a huge optimization space with
                                        velop customized solutions with satisfied learning perfor-         high-dimensional hyperparameters for BO methods. Past
                                        mance, where the exploration may include but is not lim-           works (Eggensperger et al. 2013) show that BO methods
                                        ited to selecting ML algorithms, configuring hyperparam-           perform well for relatively low-dimensional hyperparame-
                                        eters and network architecture searching. To facilitate the        ters. However, for high-dimensional problems, standard BO
                                        deployment of ML applications and democratize the usage            methods perform even worse than random search (Wang et
                                        of machine learning, it is of vital importance to reduce hu-       al. 2013). Thus, such a huge hyperparameter space greatly
                                        man efforts during such exploration. Naturally, automatic          hampers the efficiency of Bayesian optimization.
                                        Copyright © 2020, Association for the Advancement of Artificial       To alleviate the above issue, it is natural to consider an-
                                        Intelligence (www.aaai.org). All rights reserved.                  other paradigm where the BO methods are used to solve

the HPO problem for each ML algorithm separately, and               state-of-the-art baselines in terms of final accuracy and
the algorithm selection is responsible for determining the          efficiency. Our method can achieve an order of magnitude
allocation of resources to each ML algorithm’s HPO pro-             speedups compared with BO based solutions.
cess. Based on this idea, we propose the alternating optimization framework, where the HPO problem for each                       Preliminaries and Related Works
ML algorithm and the algorithm selection problem are opti-        We first introduce the basic notations for the CASH problem.
mized alternately. Benefiting from solving the HPO prob-          There are K candidate algorithms A = {A1 , ..., AK }. Each
lem for each ML algorithm individually, this framework            algorithm Ai has a corresponding hyperparameter space Λi .
brings a much smaller hyperparameter space for BO meth-           The algorithm Ai with a hyperparameter λ is denoted by
ods. Furthermore, within this framework, the resources can        Aiλ . Given the dataset D = {Dtrain , Dvalid } of a learning
be adaptively allocated to the HPO process of each al-            problem, the CASH problem is to find the joint algorithm
gorithm based on their performance. Intuitively, spending         and hyperparameter configuration A?λ? that minimizes the
too many resources in tuning the hyperparameters of poor-         loss metric (e.g., the validation error on Dvalid ):
performing algorithms should be avoided; instead, more resources should be allocated to the more promising ML algo-                        A∗λ∗ = argmin L(Aiλ , D).                  (1)
rithms that can achieve the best performance. Unfortunately,                              Ai ∈A,λ∈Λi
which algorithm is the best is unknown unless enough re-
                                                                    Hyperparameter optimization (HPO) is to find the hypersources are allocated to its HPO process. Therefore, solving
                                                                  parameter configuration λ? of a given algorithm Ai , which
the CASH problem efficiently requires to trade off the well-
                                                                  has the best performance on the validation set,
celebrated Exploration vs. Exploitation (EvE) dilemma during algorithm selection: should we explore the HPO of dif-                       λ? = argminλ∈Λi L(Aiλ , D).                 (2)
ferent ML algorithms to find the optimal algorithm (Exploration), or give more credit to the best algorithm observed       Bayesian optimization (BO) has been successfully applied to
so far to further conduct HPO (Exploitation)?                     solve the HPO problem. Sequential Model-based Algorithm
   Since the EvE dilemma has been intensively studied in the      Configuration (SMAC) (Hutter, Hoos, and Leyton-Brown
context of Multi-Armed Bandits (MAB), here we propose to          2011), Tree-structure Parzen Estimator (TPE) (Bergstra et
solve the algorithm selection problem in the framework of         al. 2011), and Spearmint (Snoek, Larochelle, and Adams
MAB. In this setting, each arm is associated with the corre-      2012) are three well-established methods. It is important
sponding HPO process of an ML algorithm. Pulling an arm           to note that these approaches can be executed in a sequenmeans that a unit of resource is assigned to the HPO process      tial manner. That is, the HPO process is iterative. Recently,
of the corresponding algorithm, and the reward corresponds        many approaches develop some elaborated mechanisms to
to the result from the HPO process. However, the existing         allocate the HPO resources adaptively (Huang et al. 2019;
MABs cannot be directly applied to model the algorithm se-        Falkner, Klein, and Hutter 2018; Sabharwal, Samulowitz,
lection problem for two reasons: 1) the well-studied objec-       and Tesauro 2016). In addition, multi-fidelity optimization
tives of MABs (e.g., accumulated rewards) are not consis-         has been deeply studied in the framework of BO to acceltent with the target of CASH problem that aims to maximize        erate the HPO problem (Swersky, Snoek, and Adams 2013;
the observed reward; 2) because the HPO results will be im-       Klein et al. 2017; Kandasamy et al. 2017; Poloczek, Wang,
proved with the increase of the HPO resource, the reward          and Frazier 2017; Hu et al. 2019).
distribution of each arm is not stationary over time.                In the algorithm selection problem, the objective is to
   The main contributions of this paper are the following:        choose a parameterized algorithm A?λ? , which is the most
                                                                  effective with respect to a specified quality metric Q(.). This
 • We propose the alternating optimization framework to
                                                                  sub-problem can be stated as a minimization problem:
   solve the CASH problem efficiently, which optimizes the
   algorithm selection problem and the HPO problem for                        A?λ? = argmini∈[1,...,K] Q(Aiλ? , D).          (3)
   each ML algorithm in an alternating manner. It takes
   the advantages of both BO methods in solving the HPO           In practice, all candidate algorithms with some fixed hyper-
   problem with a relatively small hyperparameter space and       parameters are evaluated on the validation dataset, and the
   MABs in accelerating the algorithm selection.                  algorithm with the best performance is chosen. However,
 • We introduce a novel, CASH-oriented MAB formulation,           this method suffers from the “low accuracy” issue due to
   termed Rising Bandits, where each arm’s expected reward        the lack of the HPO: the fixed hyperparameters cannot ac-
   increases as a function of the number of times it has been     curately reflect the performance of the algorithm across dif-
   pulled. To the best of our knowledge, this is the first work   ferent problems. Moreover, many methods select algorithms
   that models the algorithm selection problem in the frame-      according to some theoretical decision rules, meta-learning
   work of non-stationary MABs.                                   methods (Abdulrhaman et al. 2015) and supervised learning
                                                                  techniques (Sun and Pfahringer 2013).
 • We present an easy-to-follow online algorithm for the Ris-        To solve the CASH problem effectively in the ML appli-
   ing Bandits, accompanied with provably theoretical guar-       cations, it is necessary to select the algorithm and its hy-
   antees.                                                        perparameters simultaneously. Auto-Weka is the first work
 • The empirical studies on 30 OpenML datasets demon-             devoted to the CASH problem, which takes the BO based
   strate the superiority of the proposed method over the         solutions. Then Auto-Sklearn and Hyperopt-Sklearn also

adopt the same BO based framework. In addition, tree-

                                                                      Validation Accuracy
                                                                                            0.9
based pipeline optimization tool (TPOT) (Olson and Moore
2019) uses genetic programming to address the CASH prob-                                    0.8
lem. Recently, Reinforcement learning method (Efimova,
                                                                                            0.7
Filchenkov, and Shalamov 2017) and MAB based meth-
                                                                                                              adaboost              gradient boosting
ods (Liu et al. 2019) have been studied to solve the CASH                                   0.6               random forest         sgd
problem. They model the rewards in the stationary environ-                                                    xgboost               libsvm svc
                                                                                            0.5
ment and ignore the objective’s difference between MABs                                                 40      80            120       160             200
                                                                                                                     Trials
and CASH. In the community of MAB, several works (Besbes, Gur, and Zeevi 2014; Jamieson and Talwalkar 2016;          Figure 1: The HPO results of 6 ML algorithms. BO method –
Heidari, Kearns, and Roth 2016; Levine, Crammer, and            SMAC is used to tune the hyperparameters of each algorithm
Mannor 2017) focus on the non-stationary bandits, but none      50 times, and the average validation accuracy across trials is
of them match the settings in CASH.                             reported.
                 The Proposed Method
In this section, we introduce the alternating optimization      best-observed validation accuracy in each trial. As the numframework, give the formulation of Rising Bandits, and de-      ber of HPO trial increases, this validation accuracy improves
scribe the online algorithm to solve this bandit problem.       gradually, and then gets saturated. Further, we can summa-
                                                                rize the following observations about the rewards from BO:
The Alternating Optimization Framework                          • For each ML algorithm Ak , the reward sequence
We reformulate the CASH problem into the following                rk (1), ..., rk (n) is increasing and bounded, and the limit
bilevel optimization problem:                                     limn→∞ rk (n) exists.
            min        Q(Aiλ∗ , D)                              • The reward sequence satisfies the decreasing marginal re-
         i∈[1,...,K]
                                                          (4)     turns approximately. Here we abuse the terminology and
          s.t.         λ∗ = argminλ∈Λi L(Aiλ , D).                refer to this as “concavity”.
                                                                Since the rewards increase monotonically across trials, it is
Here the CASH problem is decomposed into two kinds              evident that the rewards are not identically distributed, but
of sub-problems: algorithm selection problem (the upper-        are generated by a non-stationary stochastic process.
level sub-problem) and the HPO problem for each ML algorithm (the lower-level sub-problem). We propose to solve      The Definition of Rising Bandits
this bilevel optimization problem by optimizing the upperlevel and lower-level sub-problems alternately. We name         Based on the observations about the HPO results, we give
it the alternating optimization framework. In this frame-       the formulation of Rising Bandits to model the algorithm
work, Bayesian Optimization (BO) methods are used to con-       selection problem with non-stationary rewards. In this bandit
duct HPO for each ML algorithm individually; MAB based          variant, the agent is given K arms, and at each time step
method is utilized to solve the algorithm selection problem.    t = 1, 2, ..., T one of the arm must be pulled. Each arm k
This framework brings two benefits:                             is associated with the HPO process of an ML algorithm Ak .
                                                                Pulling an arm means that a unit of resource (e.g., an HPO
• Since the hyperparameter space for each ML algorithm is       trial) is assigned to the HPO process of an algorithm, and the
  relatively small, BO methods can solve the corresponding      reward corresponds to the non-stationary HPO results.
  HPO problem efficiently.                                         In Rising Bandits, we model the non-stationary reward
• The resources can be adaptively allocated to the HPO of       sequences of the arms as follows: each arm k has a fixed
  each ML algorithm according to its HPO performance in         underlying reward function denoted by rk (.). All the reward
  the MAB framework.                                            functions are bounded within [0, 1]. When the agent pulls
                                                                arm k for the nth time, he receives an instantaneous reward
As a result, the poor-performing ML algorithms will be          rk (n). We denote the arm that is pulled at time step t as
equipped with few HPO resources (e.g., the number of tri-       i(t) ∈ [K] = [1, ..., K]. Let Nk (t) be the number of pulls of
als), and more resources are allocated to the promising algo-   arm k at time step t, not including this round’s choice, that’s,
rithms that can achieve better learning performance.            Nk (1) = 0, and Π the set of all sequence i(1), i(2), ...,
Non-stationary Rewards from Bayesian
                                                                                                                N
                                                                where i(t) ∈ [K], ∀t ∈ . i.e., π ∈ Π is a sequence of
                                                                actions (arms), also referred to as a policy. We denote the
Optimization                                                    arm that is chosen by policy π at time step t as π(t).
Before introducing the Rising Bandits, we first investigate        Instead of maximizing the accumulated rewards
                                                                PT
the rewards (HPO results) from BO methods. Given more              t=1 rπ(t) (Nπ(t) (t) + 1), the objective of the agent
HPO resources, the expected rewards (i.e., the best-observed    in CASH is to maximize the observed reward within T ,
validation accuracy) will increase. Figure 1 provides an in-    defined for policy π ∈ Π by,
tuitive example. Six ML algorithms are equipped with 200
trials to conduct HPO. The rewards r(.) correspond to the                                         J(T ; π) = max rπ(t) (Nπ(t) (t) + 1).                       (5)
                                                                                                             t=1:T

We consider the equivalent objective of minimizing the re-          Algorithm 1 Online algorithm for Rising Bandit
gret within T defined by,                                           Input: ML algorithm candidates A = {A1 , ..., AK }, the to-
           R(T ; π) = max{J(T ; π̃)} − J(T ; π).             (6)    tal time steps T , and one unit of HPO resource b̂.
                        π̃∈Π
                                                                     1: Initialize Scand = {1, 2, ..., K}, t = 0.
  Based on the observations about the non-stationary re-             2: while t < T do
wards, we introduce the following assumption:                        3:     for each k ∈ Scand do
Assumption 1. (Rising) ∀k ∈ [K], rk (n) is bounded, in-              4:       t = t + 1.
creasing, and concave in n.                                          5:       Pull arm k once: Hk ← Iterate HPO(Ak , b̂).
According to this assumption, the original objective in (5) is       6:       Calculate ωk (t) according to Hk .
equivalent to,                                                       7:       Update utk (T ) = min(yk (t) + ωk (t)(T − t), 1).
                                                                     8:       Update lkt (T ) = yk (t).
              J(T ; π) = max rk (Nk (T + 1)).                (7)
                               k                                     9:     end for
In the CASH problem, it is clear that the reward function           10:     for i 6= j ∈ Scand do
r(n) is bounded and increasing; but the concavity assump-           11:       if lit (T ) ≥ utj (T ) then
tion may not always hold. We will discuss the two situations        12:           Scand = Scand \{j}
in the following sections. Then we investigate an offline so-       13:       end if
lution for the Rising Bandits. The offline setting means that       14:     end for
the optimal arm is known to the agent before the game. Let          15: end while
π max be a policy defined by,                                       16: return the corresponding ML algorithm A? and its best
                                                                          hyperparameter configuration.
                 π max (t) ∈ argmax rk (T ).                 (8)
                                   k∈[K]

Lemma 1. π max is the optimal policy for the Rising Bandits         set of candidate arms (ML algorithms) in which the best arm
problem in the offline setting.                                     is guaranteed to lie (Line 1). At each round, it pulls all the
Proof: See Appendix A.1 of the supplementary material.              arms in the candidate set once, and it means that each corre-
If the best arm is known to the agent, the optimal policy must      sponding algorithm in the candidate set is given one unit of
pull the best arm repeatedly.                                       resource to tune its hyperparameters with BO methods. Then
                                                                    both the upper bound and lower bound of the final reward at
Online Solution for Rising Bandits                                  time step T are updated (Line 5-10). If the upper bound of
The CASH problem falls into the online setting, where the           the final reward of an arm k (algorithm Ak ) is less than or
best arm is unknown to the agent. In this circumstance, the         equal to the lower bound of another arm’s in the candidate
above Lemma 1 fails. However, it guides us to derive an ef-         set, then the arm k will be eliminated from the candidate set
ficient policy in the online setting: 1) first identify the best    (Line 11-15). The above process iterates until the time step
arm by using as few time steps as possible, and then 2) pull        T meets. Finally, the best algorithm along with the correthe best arm until the time step T meets. That is, solving the      sponding hyperparameter configuration is returned.
best arm identification problem first and then fully exploiting the best arm can efficiently optimize the objective in (7).     Rising Bandits with “Loose” Concavity
   Based on the Assumption 1, we can obtain an interval that
bounds the final reward of an arm. The reward function is           As stated previously, the concavity in Assumption 1 may not
concave, that’s, for any n > 2, we have r(n) − r(n − 1) ≥           always hold in the CASH problem. In this case, the probr(n + 1) − r(n). Suppose the arm k has been pulled n times,         lematic growth rate ωk (t) = rk (t) − rk (t − 1) may lead to
and n rewards rk (1), ..., rk (n) are observed. Given that rk (.)   a wrong upper bound. To alleviate this issue, we propose to
is increasing, bounded and concave, we have for any t > n,          use the following growth rate calculated by,

          rk (t) ≤ min(rk (n) + (t − n)ωk (n), 1),           (9)                               yk (t) − yk (t − C)
                                                                                    ωk (t) =                       ,         (10)
                                                                                                        C
where ωk (n) equals rk (n) − rk (n − 1), and we name ω(n)
as the growth rate at the nth step. We refer to the right-hand      where C is a constant. Intuitively, by averaging the latest C
side of Inequality 9 as the upper bound uk (t) of rk (t). Nat-      growth rates, this smooth growth rate is more robust to the
urally, the lower bound lk (t) of rk (t) is rk (n). If the arm      case with “loose” concavity. In the next section, we provide
i has the lower bound li (t) that is no less than the upper         theoretical guarantees for the proposed methods.
bound uk (s) of the arm k, the arm k cannot be the optimal
arm. By using this elimination criterion, we can gradually                Theoretical Analysis and Dissussions
dismiss the arm that cannot be the optimal arm. After finding the best arm, this arm will be pulled repeatedly until the      For the coming theorem, we define a quantity that captures
game ends.                                                          the time steps required to distinguish the optimal arm from
   Algorithm 1 illustrates both the pseudo-code of the pro-         the others. More precisely, we define γ(T ) = maxk γk (T ),
posed online algorithm and its usage in the alternating op-         where
timization framework. It operates as follows: it maintains a                    γk (T ) = arg min{utk (T ) ≤ lkt ∗T (T )}   (11)
                                                                                                 t

and k∗T is the optimal arm in the given T . Thus γk (T ) spec-    Relationship and Comparison with Previous Works
ifies the smallest number of time steps needed to pull both       Our approach takes an adaptive resource allocation scheme.
arm k and the optimal arm so that the sub-optimal arms can        From a theoretical perspective, our method is similar,
be distinguished from the optimal arm.                            in spirit, to some previous works (Huang et al. 2019;
   We prove that the upper bound of the policy regret of the      Falkner, Klein, and Hutter 2018; Sabharwal, Samulowitz,
proposed algorithm exists.                                        and Tesauro 2016). In addition, one work (Heidari, Kearns,
Theorem 1. Suppose Assumption 1 holds. The proposed al-           and Roth 2016) also supports concave reward functions.
gorithm achieves regret bounded by,                               Compared with these works, our main contribution is to ap-
      R(T ; π̄) ≤ rk∗T (T ) − rk∗T (T − (n − 1)γ(T )).    (12)    ply a similar methodology to a new application, i.e., CASH.
Proof: See Appendix A.2 of the supplementary material.            In the CASH problem, we find some additional structures
This bound contains a problem-dependent term γ(T ). If            that we can use, e.g., CASH has the concave structure in
identifying the optimal arm is easier, γ(T ) will be smaller.     the reward function. Furthermore, instead of optimizing the
                                                                  accumulated regrets in Heidari, Kearns, and Roth, CASH
Compare with Average Policy                                       focuses more on identifying the best arm. These additional
An intuitive policy πuni is to pull each arm K   T
                                                    times. The    structures allow us to perform significantly better over simregret of this policy is,                                         ply applying these previous approaches to CASH.
                                                T                    Compared with BO based solutions, our method explic-
            R(T ; πuni ) = rk∗T (T ) − max rk ( ).         (13)   itly reduces the hyperparameter space in the CASH problem
                                          k     K                 by dismissing the poor algorithms successively. Without any
We now establish the regret connection between the pro-           modification, this method can also be used to solve the reposed algorithm and the average policy.                           gression tasks by mapping the loss into [0, 1]. In addition, the
Corollary 1. When the problem-dependent term γ(T ) satis-         proposed approach can handle the cost-aware CASH; how-
                KT −T
fies: γ(T ) ≤ K(K−1)    , the regret of the proposed algorithm    ever, the existing solutions for the CASH problem do not
will not be worse than the average strategy’s.                    take the evaluation cost into consideration.
                    R(T ; π̄) ≤ R(T ; πuni ).              (14)
Proof: See Appendix A.3 of the supplementary material.                          Experiments and Results
                                                                  In the evaluation of the proposed method, we demonstrate
Theoretical Guarantee for “Loose” Concavity                       its superiority from the following three perspectives: 1) the
Here we provide a theoretical guarantee for the smooth            efficiency compared with the state-of-the-art BO based solugrowth rate. For any reward sequence yk (1), ..., we can          tions, 2) the empirical performance compared with all confind a reward function rk (.) that satisfies the Assumption       sidered baselines in terms of final accuracy and efficiency,
1. At each time step t, rk (t) ≥ yk (t), and they have the        and 3) practicability and effectiveness in the AutoML syssame limit. We denote the bias between rk (.) and yk (.) by       tems.
∆k (t) = rk (t) − yk (t).                                            We compared our method with the following baselines,
Theorem 2. If the following condition holds, the proposed         including the BO based methods and the traditional bandit
algorithm with smooth growth rate can be used to identify         based methods in the MAB community:
the optimal arm without any loss,                                 AVG The average policy that allocates the same HPO re-
                   ∆k (t)         T −t                               sources to each ML algorithm.
                             ≤              .          (15)
                 ∆k (t − C)     T −t+C                            SMAC BO based method that uses a modified random for-
Proof: See Appendix A.4 of the supplementary material.               est as the surrogate model to conduct BO.
                                                                  TPE BO based method that utilizes the tree-structured
Towards Cost-Aware CASH                                              Parzen density estimators as the surrogate model.
In the previous sections, the limited resource is the num-        CMAB Bandit based method that models the stationary
ber of HPO trials, and here we consider the time B as the            reward and maximizes the accumulated rewards with
limited resource. Both the algorithm’s performance and its           Thompson sampling (Russo et al. 2018; Liu et al. 2019).
HPO evaluation cost in runtime should be taken into consideration. In CASH, conducting an HPO trial for different ML        UCB UCB policy is used to solve the traditional MAB.
algorithms usually takes a different time cost. For example,      Softmax Softmax policy (Sutton and Barto 2018) is leverfor large datasets, training linear models is much faster than       aged to solve the traditional MAB.
the tree-based model such as gradient boosting. To solve the      BOHB This method takes an adaptive strategy to conduct
cost-aware CASH, we develop a variant of Algorithm 1. For            HPO (Falkner, Klein, and Hutter 2018).
each ML algorithm, we first compute its average time overhead ck in each HPO trial; then we predict the upper bound        In addition, to investigate its practicability and the empiriof the final reward within the given time B by,                   cal performance in the AutoML systems, we also take the
                                          0                       following AutoML systems into account:
                                       B
                  utk (B) = rk (t) + ωk ,                 (16)    Auto-Sklearn (ASK) The state-of-the-art AutoML system.
                                       ck                            It utilizes the BO based solution – SMAC to solve the
         0
where B is the time left, and ωk is the growth rate at time t.       CASH problem.

                        0.95                                                                                                                                                                                                                                                  0.95
                                                                                                                                                                                                   Ours

  Validation Accuracy                                                                                                                                                                                                                                   Validation Accuracy
                                                                                           Percentage
                                                                                                        0.4
                                                                                                                                                                                                   SMAC
                        0.94
                                                                                                                                                                                                                                                                              0.94
                                                                                                        0.2
                        0.93
                                                                              Ours                                                                                                                                                                                            0.93
                                                                                                        0.0
                                                                                                                                                                                                                        PA         RF
                                                                                                                                                                                  KNN
                                                                                                                                                                                                                   LR
                                                                                                                                                                                        LDA                                  QDA        SGD
                                                                                                                                                                                                                                              XGBoost
                                                                                                                                                                                                          Libsvm
                                                                                                                                         Extra Trees                 Grad Boost
                                                                                                              Adaboost
                                                                                                                                                       Gaussian NB
                                                                                                                                                                                              Liblinear
                                                                                                                         Decision Tree
                        0.92                                                  AVG                                                                                                                                                                                                                                          Ours
                                                                              SMAC                                                                                                                                                                                                                                         SMAC
                        0.91                                                                                                                                                                                                                                                  0.92
                            0   50   100   150   200    250 300   350   400    450   500                                                                                                                                                                                          0   500 1000 1500 2000 2500 3000 3500 4000 4500 5000
                                                       Trials                                                                                                                                                                                                                                           Trials

                                 Figure 2: Performance comparison between BO based solutions and the proposed method on PC4 dataset.

Hyperopt-Sklearn (HPSK) Similar to Auto-Sklearn, it                                                                                                                                                        High-dimensional Hyperparameter Space. Here we
 also adopts the BO based solution, and it uses TPE to con-                                                                                                                                                demonstrated that the proposed method still works well
 duct HPO instead.                                                                                                                                                                                         when the hyperparameter space becomes large. We evalu-
TPOT It leverages the genetic algorithm to solve CASH.                                                                                                                                                     ated the following three methods on OpenML PC4 dataset
                                                                                                                                                                                                           with 500 trials: average policy (AVG), SMAC and our ap-
CASH space, Datasets and Basic Settings In all experi-                                                                                                                                                     proach (OURS). The hyperparameter space of CASH probments, the optimization space of the CASH problem is the                                                                                                                                                   lem is gradually augmented by adding more and more ML
same as the one in Auto-Sklearn. It comprises 16 ML clas-                                                                                                                                                  algorithms into the algorithm candidate A with |A| = K.
sification algorithms with 78 hyperparameters. More details                                                                                                                                                The performance of each method is tested with different Ks:
about the space can be found in Appendix B of the supple-                                                                                                                                                  K = [1, 2, 4, 8, 12, 16]. When K equals to 1, the hyperpamental materials. We considered 30 classification datasets                                                                                                                                                 rameter space only includes the hyperparameters of the opfrom the OpenML repositories. These datasets are widely                                                                                                                                                    timal algorithm; if K is set to 16, the hyperparameter space
used in the related works (Feurer et al. 2015; Efimova,                                                                                                                                                    contains the hyperparameters of all ML algorithms and the
Filchenkov, and Shalamov 2017; Olson and Moore 2019;                                                                                                                                                       algorithm selection hyperparameter. As illustrated in Table
Liu et al. 2019), and the details are listed in Appendix C.                                                                                                                                                1, SMAC suffers from the low-efficiency issue. With the in-
For each run, the original dataset will be partition into three                                                                                                                                            crease of K, it is infeasible for BO methods to learn a sursubsets: training set, validation set and test set, in the pro-                                                                                                                                            rogate model that models this huge optimization space acportion of 64%, 16%, 20%. Accuracy is used as the metric                                                                                                                                                   curately within 500 trials. Consequently, the validation acof the objective. We repeated each method 10 times on each                                                                                                                                                 curacy drops from 95.02% to 93.63%. In contrast, the prodataset and reported the average accuracy. For the sake of                                                                                                                                                 posed method utilizes the elimination criterion to dismiss
fairness, we assured that all compared methods use the data                                                                                                                                                the poor-performing algorithms from the candidate set, thus
with the same preprocessing operations. That is, we pro-                                                                                                                                                   decreasing the dimension of hyperparameter space automatcessed the raw datasets with the necessary operations only                                                                                                                                                 ically. Hence our method still can achieve the best accuracy
(e.g., label encoder, one-hot encoding); and we disabled the                                                                                                                                               - 95.02% when K equals to 16.
original preprocessing module in Auto-Sklearn and TPOT.
Like Auto-Sklearn and Auto-Weka, the proposed method                                                                                                                                                                                                    K                            AVG    SMAC        OURS
leverages SMAC to solve the HPO problem for each ML al-                                                                                                                                                                                                 1                        95.02       95.02       95.02
gorithm individually. In the following experiments, we used                                                                                                                                                                                             2                        94.68       94.79       95.01
the initial version of our method (in Algorithm 1) by default                                                                                                                                                                                           4                        94.31       94.06       95.02
(except when specified the concrete version). The parame-                                                                                                                                                                                               8                        93.91       93.60       95.02
                                                                                                                                                                                                                                                        12                       93.50       93.48       95.01
ter C for computing the smooth growth rate is set to 7. Our
                                                                                                                                                                                                                                                        16                       93.39       93.63       95.02
method is not sensitive to the choice of C, and the detailed
sensitivity analysis can be found in Appendix D.
                                                                                                                                                                                                           Table 1: The validation accuracy (%) with different Ks in
More Results about the Concave Rewards We ran ex-                                                                                                                                                          the CASH problem.
periments on 5 datasets, and analyzed the reward functions
for different ML algorithms. Ten figures in the supplemen-                                                                                                                                                 Resource Allocation Figure 2 (a) depicts the validation
tary materials illustrate the rewards functions for each algo-                                                                                                                                             accuracy of three methods across trials, where 500 trials are
rithm in details. We found that the concave behavior about                                                                                                                                                 used to solve the CASH problem with K = 16. In the first
the reward function is largely consistent with the result we                                                                                                                                               100 trials, SMAC and the proposed method behave simishowed in Figure 1.                                                                                                                                                                                        larly, and both of them explore the performance distribution
                                                                                                                                                                                                           over the optimization space. Then our method starts to iden-
Comparison with BO based Methods                                                                                                                                                                           tify and dismiss the poor-performing algorithms by lever-
The empirical evaluation of BO methods shows that SMAC                                                                                                                                                     aging the known HPO results. More resources (trials) are
performs best on the benchmarks with the high-dimensional                                                                                                                                                  allocated to the more promising algorithms, and this prohyperparameter space, closely followed by TPE. In this ex-                                                                                                                                                 cedure brings significant performance improvement. Due to
periment, we evaluated the performance of both the pro-                                                                                                                                                    the huge hyperparameter space, SMAC cannot model the
posed method and SMAC on the CASH problem.                                                                                                                                                                 performance for each ML algorithm effectively. Therefore,

                                     Validation Performance (%)                               Test Performance (%)
              Dataset ID
                           TPE     SMAC    UCB     CMAB    SFMX    OURS        TPE     SMAC      UCB     CMAB     SFMX        OURS
              1049         94.02   93.85   94.27   94.20   94.10   95.26       90.42   90.64    90.75    90.92    90.98       91.13
              917          94.62   95.00   94.38   94.81   94.19   95.00       84.35   84.25    84.50    84.50    84.35       85.40
              847          87.42   87.41   87.43   87.39   87.38   87.49       86.27   86.23    86.20    86.20    86.36       86.30
              54           86.10   85.96   86.03   86.03   85.81   86.18       86.00   85.7     86.00    86.06    86.06       86.47
              31           79.88   79.94   79.81   79.94   80.06   80.06       72.95   73.65    73.45    73.45    74.35       74.35
              181          57.18   56.93   57.02   56.72   56.85   57.23       60.03   59.93    59.83    59.33    59.56       59.63
              40670        97.76   97.73   97.90   97.98   97.84   98.10       96.55   96.60    96.63    96.69    96.68       96.77
              40984        99.20   99.16   99.19   99.22   99.08   99.24       96.80   97.25    96.80    97.14    97.23       97.14
              46           97.63   97.48   97.24   97.32   97.36   97.44       95.44   95.27    95.11    95.56    95.44       95.44
              772          60.95   60.20   60.49   61.03   60.46   61.20       53.19   53.76    54.06    54.36    54.01       53.85
              310          99.00   98.97   98.97   98.98   99.00   99.02       98.67   98.71    98.75    98.65    98.67       98.71
              40691        70.76   70.66   71.05   71.02   70.74   71.95       66.50   66.09    65.03    65.97    66.00       66.66
              1501         95.25   95.22   94.98   94.86   95.02   95.33       96.71   95.49    96.43    96.30    96.43       96.80
              1557         67.49   67.52   67.37   67.58   67.22   67.85       61.71   62.05    62.09    61.99    61.99       62.68
              182          91.99   92.14   92.03   91.95   91.90   92.04       91.33   91.40    91.25    91.52    91.32       91.50
              823          98.53   98.50   98.56   98.54   98.55   98.60       98.08   98.04    98.01    98.03    98.04       98.10
              1116         99.75   99.73   99.72   99.51   99.72   99.87       99.36   98.98    99.36    99.44    99.44       99.50
              151          93.51   93.41   93.28   93.42   93.31   94.01       93.31   93.32    93.26    93.27    93.08       93.95
              1430         85.72   85.69   85.75   85.62   85.69   85.85       85.09   85.02    85.13    85.01    85.06       85.17
              32           99.55   99.53   99.45   99.42   99.47   99.63       99.30   99.25    99.55    99.41    99.34       99.60
              354          84.80   84.95   79.18   80.80   79.06   87.93       85.00   80.87    80.98    79.12    79.37       87.99
              60           86.81   86.88   86.74   86.65   86.76   86.90       86.54   86.52    86.55    86.44    86.28       86.65
              846          90.14   90.12   90.15   90.05   90.16   90.19       89.01   89.00    88.74    88.90    89.04       89.07
              28           98.85   98.81   98.77   98.59   98.78   98.87       98.84   98.73    98.84    98.84    98.81       98.85
              1471         97.99   97.93   97.84   97.50   97.93   98.28       97.75   97.38    98.08    97.83    97.74       97.61
              9976         87.02   87.02   86.54   86.97   85.82   86.83       85.85   86.62    85.65    85.46    85.58       86.60
              23512        72.96   73.12   72.96   72.80   72.90   73.20       72.60   72.29    72.55    72.46    72.60       72.86
              41082        97.89   97.74   97.65   97.10   97.74   98.10       97.54   97.10    97.56    97.54    97.55       97.62
              389          87.73   86.60   86.80   86.66   86.60   87.70       87.56   86.37    86.98    87.22    87.38       87.51
              184          89.33   89.12   89.23   89.17   89.19   89.65       88.34   88.20    88.21    88.18    88.22       88.78

         Table 2: Average validation accuracy and test accuracy for all considered methods on 30 OpenML datasets.

its performance improves very slowly, and it is even worse                       Dataset ID    Val Acc    #SMAC      #OURS        Speedups
than the average policy. To further compare our method with                      1049            94.81     5000       250             20.0x
SMAC, Figure 2 (b) illustrates their percentages of the HPO                      40691           71.38     5000       395             12.7x
trials for each ML algorithm respectively. In this problem                       40670           97.86     5000       230             21.7x
(dataset), Adaboost is the optimal algorithm. As can be seen,                    847             87.48     5000       480             10.4x
                                                                                 32              99.61     5000       450             11.1x
our method identifies and terminates 13 unpromising ML al-                       151             93.94     5000       350             14.3x
gorithms by using 20% trials. Another 30% of trials are used                     184             89.63     4000       500             8.00x
to dismiss the left two algorithms that have a near-optimal                      354             87.53     5000       427             11.7x
performance. Almost 50% of trials are spent on tuning the                        1471            98.20     5000       500             10.0x
optimal algorithm – Adaboost. In contrast, most of the trials                    41082           98.10     3000       500             6.20x
in SMAC are used to tune the poor-performing algorithms.                         Average           -         -            -           12.6x

                                                                                Table 3: Speedup results on 10 OpenML datasets.

Speedups We evaluated the achievable speedups of our
method against the baseline - SMAC on 10 OpenML                            Comparison with All Considered Methods
datasets. Continued with the previous settings, 5000 trials                In this experiment, we compared the proposed method with
in total are given to SMAC. The speedup is measured with                   all considered baselines in terms of two perspectives: 1) fithe number of trials (#) that each method needs to reach the               nal accuracy, and 2) the efficiency, i.e., the number of trials
same validation accuracy (%). Table 3 depicts the speedup                  one needs to reach the same validation accuracy. In the first
results. As can be seen, our method is more efficient than                 part, each method is given 500 trials, and the average accu-
SMAC in terms of the number of trials one needs to reach                   racy across 10 runs is reported. Table 2 lists both the averthe same validation accuracy. To derive a more clear illus-                age validation accuracy and the average test accuracy on 30
tration about this, we plotted the validation accuracy curve               OpenML datasets. In order to evaluate the generalization of
of these two methods across trials on the PC4 dataset. As                  the corresponding model, we also compared the accuracy on
shown in Figure 2 (c), the final validation accuracy of SMAC               the test set. As can be seen, the proposed method achieves
is still worse than the one that our approach achieves within              the best validation accuracy on 26 out of 30 datasets, and
250 trials. The empirical results demonstrate that the pro-                it also reaches the highest test accuracy on 20 out of 30
posed method can outperform the existing CASH algorithm                    datasets. This gives that the ML models obtained by our
- SMAC by over an order of magnitude speedups.                             method generalize well. Although our method does not get

    Dataset ID   Heidari et al   BOHB    OURS    Speedups against BOHB            Dataset          ASK    HPSK    TPOT     OURS
    1049            94.26        94.31   95.25            8.0x
    40691           71.05        71.06   71.95            7.5x
                                                                                  AMAZON          72.33   73.67   75.45    82.60
    40670           97.82        97.79   98.10            8.6x                    POKER           84.91   84.83   81.59    85.92
    847             87.35        87.42   87.49            2.4x                    WINE            65.69   65.61   65.54    66.76
    32              99.42        99.52   99.63            3.9x                    FBIS-WC         86.17   86.21   86.61    87.30
    151             93.25        93.64   94.01            5.3x                    OPTDIGITS       98.79   98.78   99.16    99.10
    184             89.18        89.40   89.65            4.5x
    354             80.79        85.18   87.90           15.7x
                                                                                  SEMEION         96.55   96.57   96.36    96.99
    1471            97.99        97.87   98.28            5.7x                    HIGGS           71.98   71.81   71.58    72.20
    41082           97.69        97.96   98.12            3.5x                    PC4             91.16   91.07   90.94    91.21
                                                                                  USPS            96.42   96.57   97.47    97.66
Table 4: Average validation accuracy (%) and speedups                             MUSK            99.29   99.20   99.63    99.73
                                                                                  ELEVATORS       88.64   88.71   88.86    89.01
compared with the considered methods.                                             ELECTRICITY     93.11   92.98   90.16    93.84

                                                                          Table 5: Average test accuracy (%) of compared AutoML
the highest accuracy on a few datasets, its result is very close          systems on 12 OpenML datasets.
to the best one. It is worth noting that, on most datasets, our
method outperforms both the existing bandit-based methods (CMAB, UCB, and Softmax) and BO-based methods                                                Conclusion
in terms of the final accuracy in solving the CASH problem.
                                                                          In this paper, we proposed an alternating optimization
   In the second part, we took another two related works
                                                                          framework to accelerate the CASH problem, where a novel
into consideration: Heidari et al. (Heidari, Kearns, and Roth
                                                                          MAB variant is introduced to conduct algorithm selection
2016) and BOHB (Falkner, Klein, and Hutter 2018). First
                                                                          and the Bayesian optimization methods are used to conduct
we ran these two methods on 10 datasets with 500 trials,
                                                                          HPO for each ML algorithm individually. Moreover, we preand the result is reported in Table 4. Although Heidari et al.
                                                                          sented an online algorithm to solve the Rising Bandits prob-
(2016) leverage the concave reward function, this method
                                                                          lem with provably theoretical guarantees. We evaluated the
cannot outperform the solution found by our approach be-
                                                                          performance of the proposed method on a number of Aucause it tries to maximize the accumulated rewards. As men-
                                                                          toML tasks and demonstrated its superiority over the comtioned previously, the objective in CASH focuses more on
                                                                          petitive baselines. In the future work, we plan to leverage the
identifying the optimal arm, instead of optimizing the accu-
                                                                          meta-learning techniques to speed up the CASH problem.
mulated rewards. Similar to our approach, BOHB adopts an
adaptive mechanism to conduct hyperparameter optimization. The reason why this method cannot beat our method is                                   Acknowledgments
that it does not use the structure information about the con-             This work is supported by the National Key Research and
cave rewards in CASH. By contrast, our method, with the                   Development Program of China (No.2018YFB1004403),
Rising Bandits, absorbs the advantages of these two kinds                 NSFC (No.61832001, 61702015, 61702016, 61572039),
of methods, and avoids their drawbacks successfully. Fur-                 Beijing Academy of Artificial Intelligence (BAAI), and
thermore, similar to the last section about speedups, we gave             Alibaba-PKU joint program. Jiawei Jiang is the correspondthe baseline - BOHB enough trials, enabling it to reach the               ing author.
same validation accuracy that our method gets within 500
trials (that is, the fourth column in Table 4). Finally, we ob-                                   References
tained the speedups against BOHB, and illustrated the result             [Abdulrhaman et al. 2015] Abdulrhaman, S. M.; Brazdil, P.;
in Table 4. It exhibits that the CASH-oriented Rising Ban-                Van Rijn, J. N.; and Vanschoren, J. 2015. Algorithm sedits are more efficient than the existing adaptive method in              lection via meta-learning and sample-based active testing.
solving the CASH problem.                                                 Algorithm Selection Workshop ECMLPKDD.
                                                                         [Bergstra et al. 2011] Bergstra, J. S.; Bardenet, R.; Bengio,
Comparison with AutoML Systems
                                                                          Y.; and Kégl, B. 2011. Algorithms for hyper-parameter op-
To investigate the practicality and effectiveness of our                  timization. In Advances in neural information processing
method in the AutoML systems, we implemented the pro-                     systems, 2546–2554.
posed method based on the components of Auto-Sklearn and
                                                                         [Besbes, Gur, and Zeevi 2014] Besbes, O.; Gur, Y.; and
compared it with three popular AutoML systems. Each sys-
                                                                          Zeevi, A. 2014. Stochastic multi-armed-bandit problem
tem is given 2 hours, and the average test accuracy across
                                                                          with non-stationary rewards. In Advances in neural infor-
10 runs is reported. The cost-aware variant of our method
                                                                          mation processing systems, 199–207.
is used to solve the CASH problems. Because the three AutoML systems do not take the evaluation cost into account,               [Efimova, Filchenkov, and Shalamov 2017] Efimova,         V.;
they only optimize the performance, instead of optimizing                 Filchenkov, A.; and Shalamov, V. 2017. Fast automated
both efficiency and performance together. As a result, given              selection of learning algorithm and its hyperparameters by
a limited time, these AutoML systems suffer from the low-                 reinforcement learning. In International Conference on
efficiency problem. The empirical results in Table 5 demon-               Machine Learning AutoML Workshop.
strate that the proposed method is more efficient than the               [Eggensperger et al. 2013] Eggensperger, K.; Feurer, M.;
existing AutoML systems on the 12 OpenML datasets.                        Hutter, F.; Bergstra, J.; Snoek, J.; Hoos, H.; and Leyton-

 Brown, K. 2013. Towards an empirical foundation for as-           [Klein et al. 2017] Klein, A.; Falkner, S.; Bartels, S.; Hen-
 sessing bayesian optimization of hyperparameters. In NIPS          nig, P.; and Hutter, F. 2017. Fast bayesian optimization of
 workshop on Bayesian Optimization in Theory and Practice,          machine learning hyperparameters on large datasets. In AIS-
 volume 10, 3.                                                      TATS, 528–536.
[Falkner, Klein, and Hutter 2018] Falkner, S.; Klein, A.; and      [Komer, Bergstra, and Eliasmith 2014] Komer, B.; Bergstra,
 Hutter, F. 2018. Bohb: Robust and efficient hyperparam-            J.; and Eliasmith, C. 2014. Hyperopt-sklearn: automatic hy-
 eter optimization at scale. In International Conference on         perparameter configuration for scikit-learn. In ICML work-
 Machine Learning, 1436–1445.                                       shop on AutoML, volume 9. Citeseer.
[Feurer et al. 2015] Feurer, M.; Klein, A.; Eggensperger, K.;      [Kotthoff et al. 2017] Kotthoff, L.; Thornton, C.; Hoos,
 Springenberg, J.; Blum, M.; and Hutter, F. 2015. Efficient         H. H.; Hutter, F.; and Leyton-Brown, K. 2017. Auto-weka
 and robust automated machine learning. In Advances in neu-         2.0: Automatic model selection and hyperparameter opti-
 ral information processing systems, 2962–2970.                     mization in weka. The Journal of Machine Learning Re-
                                                                    search 18(1):826–830.
[Goodfellow, Bengio, and Courville 2016] Goodfellow, I.;
 Bengio, Y.; and Courville, A. 2016. Deep learning. MIT            [Levine, Crammer, and Mannor 2017] Levine, N.; Crammer,
 press.                                                             K.; and Mannor, S. 2017. Rotting bandits. In Advances in
                                                                    NIPS, 3074–3083.
[He et al. 2017] He, X.; Liao, L.; Zhang, H.; Nie, L.; Hu, X.;
 and Chua, T.-S. 2017. Neural collaborative filtering. In          [Liu et al. 2019] Liu, S.; Ram, P.; Bouneffouf, D.; Bram-
 Proceedings of the 26th international conference on world          ble, G.; Conn, A. R.; Samulowitz, H.; and Gray, A. G.
 wide web, 173–182.                                                 2019. Automated machine learning via ADMM. CoRR
                                                                    abs/1905.00424.
[Heidari, Kearns, and Roth 2016] Heidari, H.; Kearns, M. J.;
 and Roth, A. 2016. Tight policy regret bounds for improving       [Ma et al. 2019] Ma, J.; Wen, J.; Zhong, M.; Chen, W.; and
 and decaying bandits. In IJCAI, 1562–1570.                         Li, X. 2019. Mmm: Multi-source multi-net micro-video
                                                                    recommendation with clustered hidden item representation
[Ho and Pepyne 2001] Ho, Y.-C., and Pepyne, D. L. 2001.             learning. Data Science and Engineering 4(3):240–253.
 Simple explanation of the no free lunch theorem of opti-
                                                                   [Olson and Moore 2019] Olson, R. S., and Moore, J. H.
 mization. In Proceedings of the 40th IEEE Conference on
                                                                    2019. Tpot: A tree-based pipeline optimization tool for au-
 Decision and Control (Cat. No. 01CH37228), volume 5,
                                                                    tomating machine learning. In Automated Machine Learn-
 4409–4414. IEEE.
                                                                    ing. Springer. 151–160.
[Hu et al. 2019] Hu, Y.-Q.; Yu, Y.; Tu, W.-W.; Yang, Q.;
                                                                   [Poloczek, Wang, and Frazier 2017] Poloczek, M.; Wang, J.;
 Chen, Y.; and Dai, W. 2019. Multi-fidelity automatic hyper-
                                                                    and Frazier, P. 2017. Multi-information source optimiza-
 parameter tuning via transfer series expansion. In Proceed-
                                                                    tion. In Advances in Neural Information Processing Sys-
 ings of the 33rd AAAI Conference on Artificial Intelligence.
                                                                    tems, 4288–4298.
[Huang et al. 2019] Huang, S.; Wang, C.; Ding, B.; and             [Quanming et al. 2018] Quanming, Y.; Mengshuo, W.;
 Chaudhuri, S. 2019. Efficient identification of approximate        Hugo, J. E.; Isabelle, G.; Yi-Qi, H.; Yu-Feng, L.; Wei-Wei,
 best configuration of training in large datasets. In Proceed-      T.; Qiang, Y.; and Yang, Y. 2018. Taking human out of
 ings of the AAAI Conference on Artificial Intelligence, vol-       learning applications: A survey on automated machine
 ume 33, 3862–3869.                                                 learning. arXiv preprint arXiv:1810.13306.
[Hutter, Hoos, and Leyton-Brown 2011] Hutter, F.; Hoos,            [Russo et al. 2018] Russo, D. J.; Van Roy, B.; Kazerouni, A.;
 H. H.; and Leyton-Brown, K. 2011. Sequential model-based           Osband, I.; Wen, Z.; et al. 2018. A tutorial on thompson
 optimization for general algorithm configuration. In Interna-      sampling. Foundations and Trends® in Machine Learning
 tional conference on learning and intelligent optimization,        11(1):1–96.
 507–523. Springer.
                                                                   [Sabharwal, Samulowitz, and Tesauro 2016] Sabharwal, A.;
[Jamieson and Talwalkar 2016] Jamieson, K., and Talwalkar,          Samulowitz, H.; and Tesauro, G. 2016. Selecting near-
 A. 2016. Non-stochastic best arm identification and hyper-         optimal learners via incremental data allocation. In Thirtieth
 parameter optimization. In AISTATS, 240–248.                       AAAI Conference on Artificial Intelligence.
[Jiang et al. 2017] Jiang, J.; Jiang, J.; Cui, B.; and Zhang, C.   [Shahriari et al. 2015] Shahriari, B.; Swersky, K.; Wang, Z.;
 2017. Tencentboost: a gradient boosting tree system with           Adams, R. P.; and De Freitas, N. 2015. Taking the human out
 parameter server. In 2017 IEEE 33rd ICDE, 281–284. IEEE.           of the loop: A review of bayesian optimization. Proceedings
[Jiang et al. 2018] Jiang, J.; Cui, B.; Zhang, C.; and Fu, F.       of the IEEE 104(1):148–175.
 2018. Dimboost: Boosting gradient boosting decision tree to       [Snoek, Larochelle, and Adams 2012] Snoek, J.; Larochelle,
 higher dimensions. In Proceedings of the 2018 International        H.; and Adams, R. P. 2012. Practical bayesian optimization
 Conference on Management of Data, 1363–1376. ACM.                  of machine learning algorithms. In Advances in NIPS, 2951–
[Kandasamy et al. 2017] Kandasamy, K.; Dasarathy, G.;               2959.
 Schneider, J.; and Póczos, B. 2017. Multi-fidelity bayesian      [Sun and Pfahringer 2013] Sun, Q., and Pfahringer, B. 2013.
 optimisation with continuous approximations. In ICML,              Pairwise meta-rules for better meta-learning-based algo-
 1799–1808.                                                         rithm ranking. Machine learning 93(1):141–161.

[Sutton and Barto 2018] Sutton, R. S., and Barto, A. G.
 2018. Reinforcement learning: An introduction. MIT press.
[Swersky, Snoek, and Adams 2013] Swersky, K.; Snoek, J.;
 and Adams, R. P. 2013. Multi-task bayesian optimization. In
 Advances in neural information processing systems, 2004–
 2012.
[Wang et al. 2013] Wang, Z.; Zoghi, M.; Hutter, F.; Mathe-
 son, D.; and De Freitas, N. 2013. Bayesian optimization in
 high dimensions via random embeddings. In Twenty-Third
 IJCAI.
[Zhao, Shen, and Huang 2019] Zhao, Y.; Shen, Y.; and
 Huang, Y. 2019. Dmdp: A dynamic multi-source default
 probability prediction framework. Data Science and Engi-
 neering 4(1):3–13.
[Zöller and Huber 2019] Zöller, M., and Huber, M. F.
 2019. Survey on automated machine learning. CoRR
 abs/1904.12054.
