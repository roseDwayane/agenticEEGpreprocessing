---
citation_key: "NomuraEtAl2021"
title: "Warm Starting CMA-ES for Hyperparameter Optimization"
authors: "Masahiro Nomura; Shuhei Watanabe; Youhei Akimoto; Yoshihiko Ozaki; Masaki Onishi"
year: 2021
doi: "10.1609/aaai.v35i10.17109"
source: "OA PDF (https://ojs.aaai.org/index.php/AAAI/article/download/17109/1)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
conversion: "pdftotext -layout (automated)"
---

# Warm Starting CMA-ES for Hyperparameter Optimization

The Thirty-Fifth AAAI Conference on Artificial Intelligence (AAAI-21)

                   Warm Starting CMA-ES for Hyperparameter Optimization
                         Masahiro Nomura* †1,2 , Shuhei Watanabe* ‡3 , Youhei Akimoto4,5 ,
                                    Yoshihiko Ozaki2,6 , Masaki Onishi2
                                                              1
                                                                CyberAgent, Inc.
                                             2
                                               Artificial Intelligence Research Center, AIST.
                                                           3
                                                             University of Freiburg.
                                                           4
                                                             University of Tsukuba.
                                           5
                                             RIKEN Center for Advanced Intelligence Project.
                                                                 6
                                                                   GREE, Inc.

                            Abstract                                        learning setting on HPO often appears in practical situations
                                                                            and is actively studied in HPO literature (Vanschoren 2019).
  Hyperparameter optimization (HPO), formulated as black-                      The covariance matrix adaptation evolution strategy
  box optimization (BBO), is recognized as essential for au-
  tomation and high performance of machine learning ap-
                                                                            (CMA-ES) (Hansen and Ostermeier 2001; Hansen 2016) is
  proaches. The CMA-ES is a promising BBO approach with                     one of the most powerful methods for BBO and has a high
  a high degree of parallelism, and has been applied to HPO                 degree of parallelism. The CMA-ES facilitates optimiza-
  tasks, often under parallel implementation, and shown supe-               tion by updating the parameters of the multivariate Gaus-
  rior performance to other approaches including Bayesian op-               sian distribution (MGD). Subsequently, the CMA-ES sam-
  timization (BO). However, if the budget of hyperparameter                 ples candidate solutions, which can be evaluated in paral-
  evaluations is severely limited, which is often the case for              lel, from the MGD. It has been applied widely in prac-
  end users who do not deserve parallel computing, the CMA-                 tice, including in HPO often under parallel evaluation set-
  ES exhausts the budget without improving the performance                  tings (Loshchilov and Hutter 2016; Friedrichs and Igel 2005;
  due to its long adaptation phase, resulting in being outper-              Watanabe and Le Roux 2014). The CMA-ES is particu-
  formed by BO approaches. To address this issue, we propose
  to transfer prior knowledge on similar HPO tasks through the
                                                                            larly useful for solving difficult BBO problems such as non-
  initialization of the CMA-ES, leading to significantly short-             separable, ill-conditioned, and rugged problems (Rios and
  ening the adaptation time. The knowledge transfer is designed             Sahinidis 2013); furthermore, it has shown the best per-
  based on the novel definition of task similarity, with which the          formance among more than 100 optimization methods for
  correlation of the performance of the proposed approach is                various BBO problems (Loshchilov, Schoenauer, and Sebag
  confirmed on synthetic problems. The proposed warm start-                 2013) with moderate to large evaluation budgets (> 100×
  ing CMA-ES, called WS-CMA-ES, is applied to different                     the number of variables).
  HPO tasks where some prior knowledge is available, showing                   However, the CMA-ES does not necessarily outperform
  its superior performance over the original CMA-ES as well as              Bayesian optimization (BO) (Frazier 2018) in the context
  BO approaches with or without using the prior knowledge.
                                                                            of HPO, in which the evaluation budget is severely lim-
                                                                            ited (Loshchilov and Hutter 2016). This is because the
                     1     Introduction                                     CMA-ES requires a relatively long adaptation phase to sam-
                                                                            ple solutions into promising regions, especially at the begin-
Hyperparameter optimization (HPO) is an essential for                       ning of optimization. Thus, the CMA-ES has received much
achieving effective performance in a wide range of machine                  less attention in the context of HPO, despite the excellent
learning algorithms (Feurer and Hutter 2019). HPO is for-                   performance verified empirically in BBO.
mulated as a black-box optimization (BBO) problem be-                          In this work, to address the inefficiency of the CMA-ES
cause the objective function of the task of interest (referred              when the evaluation budget is severely limited, we introduce
to as the target task) cannot be described using an algebraic               a simple and effective warm starting method WS-CMA-ES.
representation in general. One way to accelerate the opti-                  This warm starting strategy can shorten the CMA-ES adapmization for HPO on the target task is to exploit results from              tation phase significantly by utilizing the relationship bea related task (referred to as the source task). This transfer              tween source and target tasks.
   * These authors contributed equally in this work.                           We first define a promising distribution in the search
   †
     Corresponding author: nomura masahiro@cyberagent.co.jp                 space and task similarity. The proposed method is de-
   ‡
     The work was done at Artificial Intelligence Research Center,          signed to perform successful warm starting when the de-
AIST.                                                                       fined task similarity between a source task and a tar-
Copyright © 2021, Association for the Advancement of Artificial             get task is high. To warm-start the optimization, we es-
Intelligence (www.aaai.org). All rights reserved.                           timate a promising distribution of the source task. The

                                                                     9188

mean vector and the covariance matrix that are the param-                    The CMA-ES is invariant with order-preserving transforeters of the MGD in the CMA-ES are initialized by mini-                   mations of the objective function because the CMA-ES uses
mizing the Kullback–Leibler (KL) divergence between the                   only a ranking of solutions, not the evaluation value itself. In
MGD and the promising distribution. In this study, we per-                addition, the CMA-ES has the affine invariance to the search
formed experiments on synthetic and HPO problems for                      space. These invariances allow us to generalize the successseveral warm starting settings. In particular, we have com-               ful empirical results of the CMA-ES to a more wide range
pared the proposed method with the original CMA-ES,                       of problems (Hansen and Auger 2014).
Gaussian process expected improvement (GP-EI) (Snoek,
Larochelle, and Adams 2012), tree-structured Parzen estimator (TPE) (Bergstra et al. 2011), multi-task Bayesian opti-             2.2   CMA-ES for Hyperparameter Optimization
mization (MTBO) (Swersky, Snoek, and Adams 2013), and
multi-task BOHAMIANN (MT-BOHAMIANN) (Springen-                            The invariances mentioned above make the CMA-ES suitberg et al. 2016).                                                        able for HPO. For example, when transferring HPO to dif-
   In summary, the contributions of this work are as follows:             ferent dataset or different objectives, the scale of each objec-
                                                                          tive may significantly vary. However, the CMA-ES is robust
• We formally defined a promising distribution and task
                                                                          to such a heterogeneous scale owing to the use of rank, not
   similarity to give us the insight required to design a warm
                                                                          the evaluation value itself. Further, hyperparameters are of-
   starting strategy for the CMA-ES.
                                                                          ten dependent on each other, such as the batch size and the
• We proposed a warm starting method called the WS-                       learning rate in deep neural networks (Keskar et al. 2017;
   CMA-ES that speeds up HPO by reducing the adaptation                   Smith et al. 2018). The CMA-ES can address this depen-
   phase of the CMA-ES.                                                   dency by learning the covariance matrix appropriately. In-
• We demonstrated that the performance of WS-CMA-ES                       deed, Loshchilov and Hutter (Loshchilov and Hutter 2016)
   is correlated with the defined task similarity through nu-             reported that the CMA-ES outperformed BO in HPO when
   merical experiments.                                                   a moderate evaluation budget was available. However, the
                                                                          evaluation budget is often severely limited and is insufficient
• We verified by synthetic problems that the WS-CMA-ES
                                                                          for the CMA-ES to adapt the covariance matrix, particularly
   is more effective than naive warm starting methods even
                                                                          for the end users whose computational resources are limited.
   when a source task and a target task are not very similar.
                                                                          In such cases, the CMA-ES does not yield better solutions
• We demonstrated that the WS-CMA-ES converges faster                     than other approaches such as BO approaches (Loshchilov
   than the existing methods for HPO problems.                            and Hutter 2016).
                                                                             The possible reason for the lower performance of the
                     2   Background                                       CMA-ES is a long adaptation phase of the covariance ma-
In this study, we considered the following BBO problem:                   trix; we explain the reason below. The CMA-ES attempts
minimizing a black-box function f : X → R over a com-                     to adapt the covariance matrix to approximate the shape of
pact measurable subset X ⊆ Rd , where d is the number of                  the level set of the objective function by that of MGD. In the
variables. Let ΛLeb be the Lebesgue measure on X . In HPO,                case of a convex quadratic objective function, the covariance
a solution x ∈ X corresponds to one hyperparameter set-                   matrix approximates the inverse Hessian of the function.
ting, and f (x) is generally a validation error of the trained            Once the covariance matrix is well-adapted, the CMA-ES
model.                                                                    exhibits a linear convergence, where the convergence speed
                                                                          is as high as the one for the spherical function, f (x) = kxk2 .
2.1   CMA-ES                                                              However, as the degree of freedom of the covariance matrix
The CMA-ES is a BBO method that uses an MGD N (m, Σ),                     is Θ(d2 ), the learning rate for the covariance matrix update
wherein m ∈ Rd , Σ ∈ Rd×d is a positive-definite sym-                     is set to Θ(1/d2 ) by default for stability. Therefore, O(d2 )
metric matrix. This algorithm generates λ solutions follow-               iterations are required to adapt the covariance matrix.
ing the MGD and evaluates each solution in every iteration,                  Two approaches can be considered to mitigate this probwhich is defined as a generation. The mean vector m and                   lem. One is increasing λ, which is the number of solutions
the covariance matrix Σ are updated according to the rank-                per iteration, and evaluating them in parallel. The number of
ing of the solutions in the latest generation, and the CMA-               iterations for the adaptation decreases as λ increases (Aki-
ES learns to sample solutions from the promising region. 1                moto and Hansen 2020). However, this approach is useful
The update of the CMA-ES is strongly related to the natural               only for those users who can afford parallel computational
gradient descent (Akimoto et al. 2010; Ollivier et al. 2017);             environments. The other approach is employing variants of
m and Σ in the CMA-ES are updated to decrease the ex-                     the CMA-ES with a restricted covariance matrix model,
pected evaluation value. For more details, see the CMA-ES                 such as the diagonal model (Ros and Hansen 2008). Betutorial (Hansen 2016).2                                                  cause the covariance matrix model has few degrees of free-
    1
      Note that we followed the standard formulation of the CMA-          dom, the learning rate can be set higher, thereby acceler-
ES. Therefore, Σ was decomposed into Σ = σ 2 C where σ > 0                ating the adaptation, while compromising rotational invariand C ∈ Rd×d .                                                            ance. Hence, we also propose another method which uses a
    2                                                                     restricted matrix model, in addition to the proposed method
      Among several versions of the CMA-ES, we use the recent
standard version described in (Hansen 2016).                              with the full covariance matrix.

                                                                   9189

            3    Warm Starting CMA-ES                                    distribution to be smooth. Therefore, the uniform distribu-
We consider a transfer HPO setting, where we have pairs                  tion, which does not satisfy the smoothness, is not suitable
(hyperparameter, performance) on a source task. This set-                for defining the promising distribution.
ting often appears in the practical use of HPO (Vanschoren                  The second assumption is related to the local convexity of
2019). The original CMA-ES and other variants aiming to                  the promising region. From the first assumption, the inside
mitigate the problem of the long adaptation phase, which                 of the level set is likely to be more promising, and it naturally
are described in Section 2.2, do not have any mechanism to               leads to the local convexity. The γ-promising distribution
exploit such observational data on the source task.                      can represent these assumptions more appropriately than the
   In this work, we propose a simple and effective warm                  uniform distribution 1{x ∈ F γ }/ΛLeb (F γ ).
starting CMA-ES (WS-CMA-ES). First, we construct the                        Next, γ-similarity, which measures task similarity, is fordefinitions of a promising distribution and a task similarity.           mulated using the γ-promising distribution as follows:
Next, the details of WS-CMA-ES are given.                                Definition 3.2 (γ-Similarity). Suppose that f1 , f2 : X → R
                                                                         are measurable functions defined over the compact measur-
3.1   Definition of Task Similarity                                      able subset X ⊆ Rd . Let γ1 , γ2 ∈ (0, 1] and let Piγi be γi -
                                                                         promising distribution of fi for i = 1, 2 defined in Definition
To define task similarity, it is necessary to identify which
                                                                         3.1. We define γ-similarity from f1 to f2 as
parts of the objective function characterize the task. Because
the goal of optimization is to identify the best solution, one                 s(γ1 , γ2 ) := DKL (P∗ ||P2γ2 ) − DKL (P1γ1 ||P2γ2 ),   (2)
possible definition of a task feature is a promising distribution, which represents the regions wherein promising solu-               where DKL (P ||Q) is the KL divergence between P and Q,
tions exist with a higher probability. Herein, we define the             and P∗ is a non-informative prior distribution.
γ-promising distribution as follows:                                        A non-informative prior distribution is used when no
Definition 3.1 (γ-Promising Distribution). Suppose that f :              information is available on the objective function. In the
X → R is a measurable function defined over the com-                     CMA-ES, for the search space X = [0, 1]d , N (0.5, 0.22 ) is
pact measurable subset X ⊆ Rd . For γ ∈ (0, 1], let                      given as an initial distribution for each variable (Loshchilov
F γ = {x ∈ X | f (x) ≤ f γ }, where f γ is defined such that             and Hutter 2016). BO uses a uniform distribution as a non-
γ = ΛLeb (F γ )/ΛLeb (X ). We define γ-promising distribu-               informative prior distribution in many cases.
tion P γ , whose probability density function pγ is defined as              Intuitively, if s(γ1 , γ2 ) > 0, i.e., DKL (P1γ1 ||P2γ2 ) <
                                                                         DKL (P∗ ||P2γ2 ), the promising distribution P1γ1 for task 1 is
                                  kx − x0 k2                           closer to the promising distribution P2γ2 for task 2 than a
                     Z
                   1
           γ
          p (x) =            exp −                  dx0 ,   (1)          non-informative prior distribution P∗ .
                   Z x0 ∈F γ             2α2
                                         0 2
                                              
                                                                         3.2     Algorithm Construction
where Z = x∈X x0 ∈F γ exp − kx−x           k
                                                dx0 dx and α ∈
              R     R
                                      2α 2
                                                                         We assume a source task (task 1) is similar to a target
R>0 is a prior parameter.                                                task (task 2). Hence, the γ-similarity holds s(γ1 , γ2 ) >
   Our definition of the γ-promising distribution is based               0, i.e., DKL (P1γ1 ||P2γ2 ) < DKL (P∗ ||P2γ2 ). Note that the
on two HPO problem assumptions: (1) the continuity be-                   non-informative prior distribution P∗ is used as an initween hyperparameters and objective function and (2) the                 tial distribution for the CMA-ES if there is no information
local convexity of a promising region.                                   on the source task. Because we assume knowledge trans-
   The first assumption is the continuity of the objective               fer from the source task, we obtain DKL (P1γ1 ||P2γ2 ) <
function. When a hyperparameter varies slightly, its perfor-             DKL (P∗ ||P2γ2 ). Therefore, the CMA-ES can begin optimance also changes to a small extent. More precisely, the                mization from the location close to the promising region by
continuity of the objective function leads to the smooth-                exploiting the information for the promising region of the
ness of the promising distribution. Another possible defini-             source task from P1γ1 instead of P∗ . In this study, the initial
tion for the promising distribution is a uniform distribution            parameters of the MGD were estimated by minimizing the
1{x ∈ F γ }/ΛLeb (F γ ), where 1 is the indicator function.              KL divergence between the MGD and the empirical version
An advantage of the uniform distribution is its simplicity.              of P1γ1 . The empirical version of P1γ1 uses Gaussian mixture
However, the measure of the regions not within F γ becomes               models (GMM) as shown in Eq. (3).
0 when the promising distribution is based on the uniform                   This method transfers prior knowledge as follows. First,
distribution. In other words, this distribution considers the            the top γ × 100% solutions are selected from a set of soregions in F γ as promising to the same extent and consid-               lutions on a source task. Second, a GMM, i.e., a promising
ers the other regions totally unpromising. The main prob-                distribution of the source task, is built using the solutions
lem with the uniform distribution is that it ignores the im-             selected above. Finally, the parameters of the MGD are iniportance of the proximity of the boundaries around F γ . In              tialized via the approximation of the GMM. Further details
fact, the magnitudes of importance for the boundary regions              of each operation are described in the next section.
should not fluctuate greatly depending on whether the regions are inside or beyond F γ . The promising distribution              3.3     Details of Each Operation
should measure such slight variations of importance over the             Estimation of a Promising Distribution of a Source Task
entire search space. This condition requires the promising               Let N be the number of observations in a source task. Based

                                                                  9190

on the definition of γ-promising distribution, a probability               is the coefficient for each problem. The sphere function is
density function p(x) that represents a promising distribu-                a simple problem. On the other hand, the characteristics
tion of the source task is estimated using the top γ × 100 %               of the rotated ellipsoid function are non-separable and illsolutions as follows:                                                      conditioned. Non-separable characteristic is related to the
                               Nγ                                          dependencies between the variables, and the ill-conditioned
                         1 X                                               characteristic is that the contribution to the objective func-
                 p(x) =        N (x | xi , α2 I),            (3)
                        Nγ i=1                                             tion varies widely depending on each variable.
                                                                              We optimized each synthetic problem using two methwhere α ∈ R>0 is a prior parameter, I ∈ Rd×d is the iden-                  ods: the WS-CMA-ES and the original CMA-ES. The tartity matrix, Nγ = bγ · N c, and xi , which is an observation               get task for each problem is the function with a coefficient
in the source task, is sorted so that f (x1 ) ≤ f (x2 ) ≤ · · · ≤          b = 0.6. As prior knowledge for both settings, we evaluated
f (xNγ ) ≤ · · · ≤ f (xN ). The robustness of these two pa-                each function with a coefficient from b = 0.4, · · · , 0.8 in inrameters, γ and α, is shown in Appendix.                                   crements of 0.1. In other words, we optimized the synthetic
                                                                           problems by the WS-CMA-ES using each prior knowledge
Transferring Prior Knowledge to the CMA-ES Based                           for each case (for b = 0.4, 0.5, · · · , 0.8). Each optimization
on the aformentioned promising distribution definition, we                 was performed 20 times. We employed N (0.5, 0.22 ), which
introduced the estimation method for the initial parameters                is a non-informative distribution used in Definition 3.2, as
of the MGD for the CMA-ES. The initial parameters were                     an initial distribution for each variable in the CMA-ES. In
determined by minimizing the KL divergence between the                     all the experiments, α and γ in WS-CMA-ES were set to 0.1
promising distribution p(x) and the MGD q(x) = N (x |                      for each. For more details, see Appendices A and B.
m, Σ).                                                                        The results are presented in Figure 1. To visualize the cor-
   Based on Theorem 3.2 and Eqs. (2)–(4) of (Runnalls                      relation between the performance improvement and the γ-
2007), we can easily identify the parameters that minimize                 similarity, we measured the γ-similarity between the cases
the KL divergence as follows:                                              of b = 0.4, · · · , 0.8 and b = 0.6 and plotted it along with
                     Nγ                                                    the results. In both settings, the variation of the γ-similarity
                  1 X                                                      almost corresponds to that of the improvement achieved by
          m∗ =          xi ,                                 (4)
                 Nγ i=1                                                    WS-CMA-ES compared with the CMA-ES with respect to
                               Nγ
                                                                           the value b. In brief, WS-CMA-ES successfully transferred
                           1 X                       >                     prior knowledge when a source task resembled the target
          Σ∗ = α 2 I +           (xi − m∗ )(xi − m∗ ) .      (5)           task in terms of γ-similarity; in contrast, when the task sim-
                          Nγ i=1
                                                                           ilarity was low, the WS-CMA-ES did not perform well.
   We can observe that Eq. (5) agrees with the formula for
the maximum a posteriori estimation (Bishop 2006), which                   4.2   When Naı̈ve Transfer Fails
implies that the first term in Eq. (5) has the effect of the               If we know in advance that the source and target task is simregularization. We can also derive a variant that restricts the            ilar enough, transferring the knowledge of the source task is
covariance matrix to a diagonal: Σ∗ = diag(l1 , · · · , ld ). For          relatively easy. For example, one intuitive and naı̈ve method,
j ∈ {1, · · · , d}, it can be easily calculated as follows, con-           in this case, is to sample a solution near the solution with
sidering the independence of the variables:                                good performance in the source task. Alternatively, if the
                                Nγ                                         CMA-ES is performed for the source task, we can reuse the
                       1 X                                                 final MGD obtained on the source task as the initial MGD
             lj = α +2
                             ([xi ]j − [m∗ ]j )2 ,           (6)
                      Nγ i=1                                               for the target task. The assumption that the tasks are simi-
                                                                           lar is reasonable in practical cases (Vanschoren 2019). Howwhere [x]j denotes the j-th element of the vector x. We call               ever, it is difficult to guarantee it before performing optithis restricted variant WS-sep-CMA-ES; its performance is                  mization. Therefore, it is desirable to alleviate dramatic pervalidated in Section 4.2.                                                  formance degradation even when these tasks are not very
                                                                           similar. To confirm the robustness of our proposed warm
      4    Experiments on Synthetic Problems                               starting method in such situations, we compare the behavior
                                                                           of the proposed method with the following naı̈ve transfer-
4.1   Performance Depending on Task Similarity
                                                                           ring methods:
As defined in the previous section, WS-CMA-ES is expected
to achieve faster convergence on problems with higher γ-                   • ReuseGMM : This method samples solutions from the
similarity (i.e. s(γ1 , γ2 ) > 0). To confirm this correlation,              GMM which represents a promising distribution estiwe measured the γ-similarity and the performance of WS-                      mated on the source task; that is, the solutions are sam-
CMA-ES using two synthetic problems:                                         pled from the distribution defined in Eq. (3) throughout
                                                                             the optimization.
 • Sphere Function: f (x) = (x1 − b)2 + (x2 − b)2
                                                                           • ReuseNormal : This method uses the final mean and co-
 • Rotated Ellipsoid Function: f (x) = fell (Rx)                             variance matrix obtained on the source task as the initial
where fell (x) = (x1 − b)2 + 52 (x2 − b)2 , R ∈ R2×2                         MGD on the target task. This method is the same as the
is a rotation matrix rotating π/6 around the origin, and b                   (WS-)CMA-ES except for the initialization of MGD.

                                                                    9191

                            0.5                                                                                                                                                           2
                                                                                                                                0.5

  f¯best − f¯best                                                                                     f¯best − f¯best
                                                                                  1

                  (×10−3)                                                                                             (×10−2)
    cma      ws                                                                                         cma      ws

                                                                                       γ-Similarity                                                                                            γ-Similarity
                            0.0                                                   0                                             0.0                                                       0

                                                                                  −1
                     −0.5                                                                                                −0.5
                                                                f¯best
                                                                  cma
                                                                       − f¯best
                                                                           ws
                                                                                                                                                                        f¯best
                                                                                                                                                                          cma
                                                                                                                                                                               − f¯best
                                                                                                                                                                                   ws     −2
                                                                                  −2
                                                                γ-Similarity                                                                                            γ-Similarity
                                                                                                                         −1.0
                     −1.0
                                  0.4   0.5      0.6          0.7           0.8                                                       0.4       0.5        0.6       0.7            0.8
                                                  b                                                                                                        b
                                        (a) Sphere Function                                                                                 (b) Rotated Ellipsoid Function

Figure 1: Results of the experiments to confirm the correlation between γ-similarity and performance. The horizontal axis
represents the prior parameter b used for each warm starting setting where the prior knowledge was the result with b = 0.6. The
vertical axis for red and blue lines denote the subtraction f¯best
                                                              cma
                                                                   − f¯best
                                                                       ws
                                                                            of the mean of the best evaluation value in the CMA-ES
and the WS-CMA-ES (20 runs for each) and γ-similarity in Definition 3.2, respectively. f¯best     cma
                                                                                                       − f¯best
                                                                                                           ws
                                                                                                                > 0 implies that the
result of the WS-CMA-ES is better than that of the CMA-ES.

   Random search is used as the optimization of a source                                                                         5      Experiments for Hyperparameter
task for all methods except for ReuseNormal; in ReuseNor-                                                                                       Optimization
mal, the result of the CMA-ES is used as the source task. We
consider the sphere function and the rotated ellipsoid func-                                                We applied WS-CMA-ES to several HPO problems to vertion defined in Section 4.1; the experimental settings remain                                               ify its effectiveness on HPO. The experiments comprise the
the same.                                                                                                   following two practical scenarios:
                                                                                                              • Warm starting using a result of HPO for a subset of a
   In addition to these transferring methods, we experi-                                                        dataset (Section 5.1), and
ment with the CMA-ES and the sep-CMA-ES, which are                                                            • Warm starting using a result of HPO for another dataset
not transferring methods, as references. When the offset                                                        (Section 5.2).
changes largely between the source and target tasks, these
non-transferring methods become advantageous, as shown                                                      As the baseline methods, we select the (1) CMA-ES, (2) ranin Section 4.                                                                                               dom search (RS) (Bergstra and Bengio 2012), (3) random
                                                                                                            sampling from the initial MGD used in WS-CMA-ES (WS-
                                                                                                            only), (4) GP-EI (Snoek, Larochelle, and Adams 2012), (5)
   Figure 2 presents the results of the experiments over 20
                                                                                                            TPE (Bergstra et al. 2011), (6) MTBO (Swersky, Snoek, and
runs. As expected, ReuseNormal shows the best perfor-
                                                                                                            Adams 2013), and (7) MT-BOHAMIANN (Springenberg
mance on offset b = 0.6 where the source and target tasks
                                                                                                            et al. 2016). MTBO, which is an extension of GP-EI, and
are the same. However, the performance of ReuseNormal
                                                                                                            MT-BOHAMIANN are warm starting methods for BO. TPE
deteriorates drastically when the offset is changed. This is
                                                                                                            is known to provide strong performance in HPO. Note that
because ReuseNormal converges more than necessary near
                                                                                                            we do not use WS-sep-CMA-ES because the performance is
the optimal solution of the source task even when the op-
                                                                                                            similar to WS-CMA-ES in the severely limited budget settimal solution of the target task is largely different. In this
                                                                                                            ting, which is confirmed in Section 4.2. We evaluated 100
case, it takes significant time to move from the promising re-
                                                                                                            hyperparameter settings by RS as prior knowledge in all the
gion estimated by the source task, which impairs the perfor-
                                                                                                            experiments to allow every method to transfer the same data
mance of ReuseNormal. In contrast, the proposed methods,
                                                                                                            fairly. Each optimization was run 12 times. Details of the
WS-CMA-ES and WS-sep-CMA-ES, are less dependent
                                                                                                            experimental settings are shown in Appendix.
on how long the optimization is performed on the source
task, which leads to relatively less performance degradation even in such cases. Similar to the case of ReuseNor-                                                   5.1                  Warm Starting using a Result of a Subset
mal, ReuseGMM, which does not adapt during optimiza-                                                        We evaluated hyperparameter settings of each machine
tion, is strongly affected by the dissimilarity of the tasks.                                               learning algorithm trained on 10% of a full dataset. This re-
This demonstrates the necessity of the adaptation toward the                                                sult was considered as the source task and was used by the
optimal solution direction of the target task by the CMA-ES                                                 warm starting methods.
(or sep-CMA-ES). In conclusion, compared with the naı̈ve
transferring methods, which strongly assume that the tasks                                                  LightGBM on Multilabel Classification LightGBM (Ke
are similar, the proposed method is more robust and efficient                                               et al. 2017) is used as an ML model. Six hyperparameters
to the difference between the source and target tasks.                                                      shown in Appendix were optimized in the experiments. We

                                                                                                 9192

                              0.0030                                                                               0.030
                                             CMA-ES
                              0.0025         sep-CMA-ES                                                            0.025

      best evaluation value                                                                best evaluation value
                                             WS-CMA-ES
                              0.0020         WS-sep-CMA-ES                                                         0.020
                                             ReuseGMM
                              0.0015         ReuseNormal
                                                                                                                   0.015

                              0.0010                                                                               0.010

                              0.0005                                                                               0.005

                              0.0000                                                                               0.000
                                       0.4         0.5         0.6      0.7   0.8                                          0.4         0.5         0.6        0.7   0.8
                                                             offset b                                                                            offset b

                                                 (a) Sphere Function                                                             (b) Rotated Ellipsoid Function

Figure 2: Comparing the proposed methods with naive transferring methods. The mean (line) and the standard error (shadow)
over 20 runs are shown. The results of the CMA-ES and the sep-CMA-ES, which are not transferring methods, are included as
references.

used the Toxic Comment Classification Challenge data3 as                                   the results of the experiments. We observe that a higher suba dataset. As a metric in the experiments, the mean column-                                set ratio tends to result in faster convergence. The results
wise area under the receiving operating characteristic curve                               imply that the sets of relatively good hyperparameters in
(ROC AUC) was used. Note that this measurement is better                                   the source tasks are spatially closer to those in the target
when the value is higher, so we used 1 – AUC as the objec-                                 tasks, while the best values may not be close, as is claimed
tive function.                                                                             in (Swersky, Snoek, and Adams 2013).
MLP on MNIST and Fashion-MNIST The proposed                                                5.2                         Warm Starting using a Result of Another
method was applied to the HPO of multilayer perceptrons                                                                Dataset
(MLPs). We used the MNIST handwritten digits dataset (Le-
Cun et al. 1998) and the Fashion-MNIST clothing articles                                   This section examines what happens when prior knowledge
dataset (Xiao, Rasul, and Vollgraf 2017). We optimized                                     of a different dataset is utilized by warm starting methods.
eight hyperparameters as shown in Appendix.                                                We carried out experiments to demonstrate the effectiveness
                                                                                           of the proposed method in such a practical situation.
CNN on CIFAR-100 We further applied the proposed                                           Using the Knowledge of MLP on MNIST for MLP on
method to more sophisticated 8-layer convolutional neural                                  Fashion-MNIST We first trained MLPs on MNIST and
networks (CNNs). The CNNs were trained on the CIFAR-                                       then transferred the result to the HPO of Fashion-MNIST.
100 dataset (Krizhevsky 2009) and have ten types of hyper-                                 The architecture of the MLPs and their hyperparameters are
parameters as described in Appendix.                                                       the same as those described in Section 5.1.
Results and Discussion on Knowledge Transfer of a Sub-                                     Using the Knowledge of CNN on SVHN for CNN on
set Figure 3 shows the experiment results. In each experi-                                 CIFAR-10 We optimized the 8-layer CNNs. Hyperparamment, the proposed method and the WS-only identified bet-                                  eters for this model are the same as those of the model
ter objective metrics much faster than the CMA-ES did. Fur-                                optimized earlier (see Section 5.1). CNNs initially learned
ther, we found that MTBO yielded better solutions quickly                                  the Street View House Numbers (SVHN) dataset (Netzer
than GP-EI. Clearly, there was high task similarity between                                et al. 2011). Next, the warm starting methods employed the
the given tasks that could be exploited by warm starting                                   knowledge to obtain the optimal hyperparameter settings for
methods. WS-CMA-ES and WS-only found better hyperpa-                                       CNNs trained on CIFAR-10.
rameter settings in the earlier stage of the optimizations than
the others. In the later stage of the optimization, WS-CMA-                                Results and Discussion on the Knowledge Transfer of
ES adapted successfully and converged to better solutions                                  Another dataset Figure 5 shows the results of the experithan that of WS-only. Figure 3 (a) shows that WS-CMA-ES                                    ments. The proposed method exhibited outstanding converand WS-only behave similarly. This is because the evalua-                                  gence speed in the experiments and found better hyperpation budget is quite limited, and the update of MGD only                                   rameter settings far more quickly than the CMA-ES. Alhappens a few times.                                                                       though MTBO also successfully found better solutions than
   To observe the correlation between the performance of the                               GP-EI, the performance of MTBO was not considerably bet-
WS-CMA-ES and a subset ratio, we applied WS-CMA-ES                                         ter than that of RS. In fact, MTBO required approximately
using prior knowledge of MNIST and Fashion-MNIST of                                        25 evaluations to find better hyperparameter settings than
different subset ratios 2%, 10%, and 50%. Figure 4 shows                                   GP-EI in the results described in Figure 3 (b), (d). Accord-
                                                                                           ing to Figure 5, however, it required approximately 40 eval-
    3                                                                                      uations in these experiments. Contrarily, the WS-CMA-ES
      https://www.kaggle.com/c/jigsaw-toxic-commentclassification-challenge                                                                   identified better hyperparameter settings than the CMA-ES

                                                                                    9193

        (a) HPO of LightGBM on full Toxic Challenge data.                           (b) HPO of MLPs on full MNIST.

             (c) HPO of MLPs on full Fashion-MNIST.                                (d) HPO of CNNs on full CIFAR-100.

Figure 3: Experiments with warm starting optimization using a result of the HPO for a subset of each dataset. Warm starting
methods used a result of the HPO on 1/10th of each dataset as prior knowledge. The horizontal axis represents the number of
evaluations. We plotted the mean and the standard error of the best evaluation value over 12 runs.

in approximately 25 and 30 evaluations in the experiments             tational complexity of WS-CMA-ES does not depend on the
using a small dataset and experiments using another dataset,          number of observations. This enables users to implement the
respectively. This is probably because knowledge transfer             method even when numerous results are available. Although
from other datasets is more difficult than knowledge trans-           the meta-feature based warm starting (Feurer, Springenberg,
fer from a subset of a dataset. MTBO obtains promising so-            and Hutter 2015) can alleviate this computational problem,
lutions using the approximation of the entire search space,           it is not always possible to prepare such meta-feature for
but the WS-CMA-ES obtains promising solutions using that              the dataset. The method of initializing the search space usof only the promising region. The former approximation re-            ing the result of the source task does not incur extra comquires more observations to yield promising solutions com-            putational complexity and can be used without such a meta
pared with the latter. This may be the reason for the effec-          feature (Wistuba, Schilling, and Schmidt-Thieme 2015; Pertiveness of WS-CMA-ES in knowledge transfer from an-                  rone et al. 2019).
other dataset. This behavior can also be confirmed with the              Another difference is that it is usually challenging for
transfer HPO experiments with other datasets, which are               most BO approaches to handle the scale variation of obprovided in Appendix.                                                 jective functions across tasks. This situation often appears
                                                                      when exploiting prior knowledge in transfer HPO; for exam-
        6    Related Work and Discussion                              ple, the validation error may significantly change across dif-
                                                                      ferent datasets. This situation also appears when transferring
Various types of warm starting methods for BO have been               between different objectives, such as transferring between
actively studied in the HPO context. These methods model              the result of misclassification error and that of cross entropy.
the relationship between tasks using a variety of ways, such          Salinas et al. introduced a sophisticated semi-parametric apas a Gaussian process (Swersky, Snoek, and Adams 2013;                proach to deal with such a heterogeneous scale (Salinas,
Poloczek, Wang, and Frazier 2017; Feurer, Letham, and                 Shen, and Perrone 2020).
Bakshy 2018), deep neural networks (Springenberg et al.
2016; Kim, Kim, and Choi 2017), and Bayesian linear
regression (Perrone et al. 2018). However, the CMA-ES,
                                                                               7     Conclusion and Future Work
which shows outstanding performance in BBO, has not been              We proposed the WS-CMA-ES, a simple and effective warm
thoroughly considered in HPO.                                         starting strategy for the CMA-ES. The proposed method was
   One difference between our method and the warm start-              designed based on the theoretical definitions of a promising methods for BO is in the usage of the source tasks’ re-           ing distribution and task similarity. It initializes MGD in the
sult. Most warm starting methods for BO repeatedly con-               CMA-ES by approximating the promising distribution on a
struct a probabilistic model using prior knowledge in each            source task. This knowledge transfer performs well espeiteration. In contrast, WS-CMA-ES uses prior knowledge                cially when a target task is similar to a source task in terms
only at the inception of optimization. Therefore, the compu-          of the defined task similarity, which is confirmed by our ex-

                                                               9194

                  (a) MLPs trained on full MNIST                                 (b) MLPs trained on full Fashion-MNIST

Figure 4: Relationship between task similarity and the performance of WS-CMA-ES over 12 times. As the size of the dataset
approaches that of the complete dataset, the WS-CMA-ES attains faster convergence.

   (a) MLPs trained on Fashion-MNIST. As prior knowledge, the       (b) CNNs trained on CIFAR-10. As prior knowledge, the result
   result of HPO of MLPs trained on MMIST was used.                 of HPO of CNNs trained on SVHN was used.

             Figure 5: Experiments with warm starting optimizations using the result of HPO on another dataset.

periments. Experiments with synthetic and HPO problems                                         References
confirm that WS-CMA-ES is effective, even with low bud-                 Akimoto, Y.; and Hansen, N. 2020. Diagonal Acceleragets or when the source and target tasks are not very similar.          tion for Covariance Matrix Adaptation Evolution Strategies.
   The main limitation of this study is the assumption of task          Evolutionary computation 28(3): 405–435.
similarity. From our experiments and the desirable results              Akimoto, Y.; Nagata, Y.; Ono, I.; and Kobayashi, S. 2010.
of warm starting methods that assume task similarity (e.g.,             Bidirectional Relation between CMA Evolution Strategies
(Bardenet et al. 2013; Yogatama and Mann 2014)), we hy-                 and Natural Evolution Strategies. In International Conferpothesize that HPO tasks are often similar as long as so are            ence on Parallel Problem Solving from Nature, 154–163.
they intuitively. However, WS-CMA-ES can be worse than
                                                                        Bardenet, R.; Brendel, M.; Kégl, B.; and Sebag, M. 2013.
the CMA-ES when the similarity between the source and
                                                                        Collaborative hyperparameter tuning. In International contarget tasks is low, as shown in Figure 1. Automatic detec-
                                                                        ference on machine learning, 199–207.
tion of task dissimilarity and switching back to the original
CMA-ES is essential for this method to be more convincing               Bergstra, J.; and Bengio, Y. 2012. Random Search for
and reliable.                                                           Hyper-Parameter Optimization. Journal of Machine Learn-
                                                                        ing Research 13(Feb): 281–305.
                                                                        Bergstra, J. S.; Bardenet, R.; Bengio, Y.; and Kégl, B. 2011.
                  Acknowledgements                                      Algorithms for Hyper-Parameter Optimization. In Advances
                                                                        in neural information processing systems, 2546–2554.
                                                                        Bishop, C. M. 2006. Pattern recognition and machine learn-
The authors thank Shota Yasui, Yuki Tanigaki, Yoshiaki                  ing. springer.
Bando for valuable feedback and suggestion. This paper is
based on the results obtained from a project commissioned               Feurer, M.; and Hutter, F. 2019. Hyperparameter Optimizaby the New Energy and Industrial Technology Development                 tion. In Automated Machine Learning, 3–33.
Organization (NEDO). Computational resource of AI Bridg-                Feurer, M.; Letham, B.; and Bakshy, E. 2018. Scalable
ing Cloud Infrastructure (ABCI) provided by National Insti-             Meta-Learning for Bayesian Optimization using Rankingtute of Advanced Industrial Science and Technology (AIST)               Weighted Gaussian Process Ensembles. In AutoML Workwas used.                                                               shop at ICML.

                                                                 9195

Feurer, M.; Springenberg, J. T.; and Hutter, F. 2015. Ini-            Perrone, V.; Shen, H.; Seeger, M. W.; Archambeau, C.; and
tializing Bayesian Hyperparameter Optimization via Meta-              Jenatton, R. 2019. Learning search spaces for Bayesian oplearning. In Twenty-Ninth AAAI Conference on Artificial In-           timization: Another view of hyperparameter transfer learntelligence, 1128–1135.                                                ing. In Advances in Neural Information Processing Systems,
Frazier, P. I. 2018. A Tutorial on Bayesian Optimization.             12751–12761.
arXiv preprint arXiv:1807.02811 .                                     Poloczek, M.; Wang, J.; and Frazier, P. 2017. Multi-
                                                                      Information Source Optimization. In Advances in Neural
Friedrichs, F.; and Igel, C. 2005. Evolutionary Tuning of
                                                                      Information Processing Systems, 4288–4298.
Multiple SVM Parameters. Neurocomputing 64: 107–117.
                                                                      Rios, L. M.; and Sahinidis, N. V. 2013. Derivative-free
Hansen, N. 2016. The CMA Evolution Strategy: A Tutorial.
                                                                      optimization: A review of algorithms and comparison of
arXiv preprint arXiv:1604.00772 .
                                                                      software implementations. Journal of Global Optimization
Hansen, N.; and Auger, A. 2014. Principled Design of Con-             56(3): 1247–1293.
tinuous Stochastic Search: From Theory to Practice. In The-           Ros, R.; and Hansen, N. 2008. A Simple Modification in
ory and principled methods for the design of metaheuristics,          CMA-ES Achieving Linear Time and Space Complexity. In
145–180.                                                              International Conference on Parallel Problem Solving from
Hansen, N.; and Ostermeier, A. 2001. Completely Deran-                Nature, 296–305.
domized Self-Adaptation in Evolution Strategies. Evolution-           Runnalls, A. R. 2007. A Kullback-Leibler Approach
ary computation 9(2): 159–195.                                        to Gaussian Mixture Reduction. IEEE Transactions on
Ke, G.; Meng, Q.; Finley, T.; Wang, T.; Chen, W.; Ma, W.;             Aerospace and Electronic Systems 43(3): 989–999.
Ye, Q.; and Liu, T.-Y. 2017. LightGBM: A Highly Efficient             Salinas, D.; Shen, H.; and Perrone, V. 2020. A Quantile-
Gradient Boosting Decision Tree. In Advances in neural                based Approach for Hyperparameter Transfer Learning. In
information processing systems, 3146–3154.                            International conference on machine learning, 7706–7716.
Keskar, N. S.; Mudigere, D.; Nocedal, J.; Smelyanskiy, M.;            Smith, S. L.; Kindermans, P.-J.; Ying, C.; and Le, Q. V.
and Tang, P. T. P. 2017. On Large-Batch Training for Deep             2018. Don’t Decay the Learning Rate, Increase the Batch
Learning: Generalization Gap and Sharp Minima. Interna-               Size. In International Conference on Learning Representational Conference on Learning Representations .                       tions.
Kim, J.; Kim, S.; and Choi, S. 2017. Learning to Warm-Start           Snoek, J.; Larochelle, H.; and Adams, R. P. 2012. Practical
Bayesian Hyperparameter Optimization. arXiv preprint                  Bayesian Optimization of Machine Learning Algorithms. In
arXiv:1710.06219 .                                                    Advances in neural information processing systems, 2951–
Krizhevsky, A. 2009. Learning Multiple Layers of Features             2959.
from Tiny Images. Master’s thesis, University of Tront .              Springenberg, J. T.; Klein, A.; Falkner, S.; and Hutter, F.
LeCun, Y.; Bottou, L.; Bengio, Y.; Haffner, P.; et al. 1998.          2016. Bayesian Optimization with Robust Bayesian Neural
Gradient-Based Learning Applied to Document Recogni-                  Networks. In Advances in Neural Information Processing
tion. Proceedings of the IEEE 86(11): 2278–2324.                      Systems, 4134–4142.
Loshchilov, I.; and Hutter, F. 2016. CMA-ES for Hyperpa-              Swersky, K.; Snoek, J.; and Adams, R. P. 2013. Multi-Task
rameter Optimization of Deep Neural Networks. In ICLR                 Bayesian Optimization. In Advances in neural information
Workshop.                                                             processing systems, 2004–2012.
Loshchilov, I.; Schoenauer, M.; and Sebag, M. 2013. Bi-               Vanschoren, J. 2019. Meta-Learning. In Automated Machine
population CMA-ES Algorithms with Surrogate Models and                Learning, 35–61.
Line Searches. In Proceedings of the 15th annual confer-              Watanabe, S.; and Le Roux, J. 2014. Black box optimizaence companion on Genetic and evolutionary computation,               tion for automatic speech recognition. In 2014 IEEE Inter-
1177–1184.                                                            national Conference on Acoustics, Speech and Signal Pro-
Netzer, Y.; Wang, T.; Coates, A.; Bissacco, A.; Wu, B.; and           cessing (ICASSP), 3256–3260.
Ng, A. Y. 2011. Reading Digits in Natural Images with Un-             Wistuba, M.; Schilling, N.; and Schmidt-Thieme, L. 2015.
supervised Feature Learning. In NIPS Workshop on Deep                 Hyperparameter Search Space Pruning–A New Component
Learning and Unsupervised Feature Learning 2011.                      for Sequential Model-Based Hyperparameter Optimization.
Ollivier, Y.; Arnold, L.; Auger, A.; and Hansen, N. 2017.             In Joint European Conference on Machine Learning and
Information-Geometric Optimization Algorithms: A Unify-               Knowledge Discovery in Databases, 104–119.
ing Picture via Invariance Principles. The Journal of Ma-             Xiao, H.; Rasul, K.; and Vollgraf, R. 2017. Fashion-MNIST:
chine Learning Research 18(1): 564–628.                               a Novel Image Dataset for Benchmarking Machine Learning
                                                                      Algorithms. arXiv preprint arXiv:1708.07747 .
Perrone, V.; Jenatton, R.; Seeger, M. W.; and Archambeau,
C. 2018. Scalable Hyperparameter Transfer Learning. In                Yogatama, D.; and Mann, G. 2014. Efficient Transfer Learn-
Advances in Neural Information Processing Systems, 6845–              ing Method for Automatic Hyperparameter Tuning. In Arti-
6855.                                                                 ficial intelligence and statistics, 1077–1085.

                                                               9196
