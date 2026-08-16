---
citation_key: "FalknerEtAl2018"
title: "BOHB: Robust and Efficient Hyperparameter Optimization at Scale"
authors: "Stefan Falkner; Aaron Klein; Frank Hutter"
year: 2018
doi: "10.48550/arxiv.1807.01774"
source: "arXiv (1807.01774)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
conversion: "pdftotext -layout (automated)"
---

# BOHB: Robust and Efficient Hyperparameter Optimization at Scale

BOHB: Robust and Efficient Hyperparameter Optimization at Scale

                                                                              Stefan Falkner 1 Aaron Klein 1 Frank Hutter 1

                                                                  Abstract                               for a practical solution to the hyperparameter optimization
                                               Modern deep learning methods are very sensitive           (HPO) problem that fulfills many desiderata:
                                               to many hyperparameters, and, due to the long
                                               training times of state-of-the-art models, vanilla        1. Strong Anytime Performance. Since large contempo-

arXiv:1807.01774v1 [cs.LG] 4 Jul 2018
                                               Bayesian hyperparameter optimization is typi-             rary neural networks often require days or even weeks to
                                               cally computationally infeasible. On the other            train, HPO methods that view performance as a black box
                                               hand, bandit-based configuration evaluation ap-           function to be optimized require extreme resources. The
                                               proaches based on random search lack guidance             overall budget that most researchers and practitioners can
                                               and do not converge to the best configurations as         afford during development is often not much larger than that
                                               quickly. Here, we propose to combine the ben-             of fully training a handful of models, and hence practical
                                               efits of both Bayesian optimization and bandit-           HPO methods must go beyond this blackbox view to already
                                               based methods, in order to achieve the best of            yield good configurations with such a small budget.
                                               both worlds: strong anytime performance and               2. Strong Final Performance. On the other hand, what
                                               fast convergence to optimal configurations. We            matters most at deployment time is the performance of the
                                               propose a new practical state-of-the-art hyperpa-         best configuration a HPO method can find given a larger
                                               rameter optimization method, which consistently           budget. Since finding the best configurations in a large space
                                               outperforms both Bayesian optimization and Hy-            requires guidance, this is where methods based on random
                                               perband on a wide range of problem types, in-             search struggle.
                                               cluding high-dimensional toy functions, support           3. Effective Use of Parallel Resources. With the rise of
                                               vector machines, feed-forward neural networks,            parallel computing, large parallel resources are often avail-
                                               Bayesian neural networks, deep reinforcement              able (e.g., compute clusters or cloud computing), and practi-
                                               learning, and convolutional neural networks. Our          cal HPO methods need to be able to use these effectively.
                                               method is robust and versatile, while at the same
                                               time being conceptually simple and easy to imple-         4. Scalability. Modern deep neural networks require the
                                               ment.                                                     setting of a multitude of hyperparameters, including archi-
                                                                                                         tectural choices (e.g., the number and width of layers), op-
                                                                                                         timization hyperparameters (e.g., learning rate schedules,
                                        1. Introduction                                                  momentum, and batch size), and regularization hyperpa-
                                                                                                         rameters (e.g., weight decay and dropout rates). Practical
                                        Machine learning has recently achieved great successes in        modern HPO methods therefore must be able to easily han-
                                        a wide range of practical applications, but the performance      dle problems ranging from just a few to many dozens of
                                        of the most prominent methods depends more strongly than         hyperparameters.
                                        ever on the correct setting of many internal hyperparameters     5. Robustness & Flexibility. The challenges for hyperpa-
                                        (see, e.g., Henderson et al. (2017); Melis et al. (2017)). The   rameter optimization vary substantially across subfields of
                                        best-performing models for many modern applications of           machine learning; e.g., deep reinforcement learning sys-
                                        deep learning are getting ever larger and thus more com-         tems are known to be very noisy (Henderson et al., 2017),
                                        putationally expensive to train, but at the same time both       while probabilistic deep learning is often very sensitive
                                        researchers and practitioners desire to set as many hyperpa-     to a few key hyperparameters. Different hyperparameter
                                        rameters automatically as possible. These constraints call       optimization problems also give rise to different types of
                                           1
                                             Department of Computer Science, University of Freiburg,     hyperparameters (such as binary, categorical, integer, and
                                        Freiburg, Germany.     Correspondence to: Stefan Falkner         continuous), each of which needs to be handled effectively
                                        <sfalkner@informatik.uni-freiburg.de>.                           by a practical HPO method.
                                        Proceedings of the 35 th International Conference on Machine
                                        Learning, Stockholm, Sweden, PMLR 80, 2018. Copyright 2018       As we will discuss in Section 2, while there has been a lot of
                                        by the author(s).                                                recent progress in the field of hyperparameter optimization,

                                        BOHB: Robust and Efficient Hyperparameter Optimization at Scale

         10−1                                                                         eters for fully-connected neural networks.
                                                        Random Search

                      20x speed up                      Bayesian Optimization         Gaussian processes are the most commonly-used probabilis-
                                                        Hyperband
                                                                                      tic model in Bayesian optimization (Shahriari et al., 2016),
                                                        BOHB
                                                                                      since they obtain smooth and well-calibrated uncertainty
regret   10−2
                                                                                      estimates. However, Gaussian processes do not typically
                                                                                      scale well to high dimensions and exhibit cubic complex-
                                                                 55x speed up         ity in the number of data points (scalability); they also do
                                                                                      not apply to complex configuration spaces without special
         10−3
                101        102         103        104           105             106   kernels (flexibility) and require carefully-set hyperpriors
                                     wall clock time [s]                              (robustness).
                                                                                      To speed up the hyperparameter optimization of machine
Figure 1. Illustration of typical results obtained, here for optimiz-                 learning algorithms, recent methods in Bayesian optimizaing six hyperparameters of a neural network. We show the im-                          tion try to extend the traditional blackbox setting by exploitmediate regret of the best configuration found by 4 methods as
                                                                                      ing cheaper fidelities of the objective function (Swersky
a function of time. Hyperband has strong anytime performance,
but for larger budgets does not perform much better than random
                                                                                      et al., 2014; Klein et al., 2017a; Swersky et al., 2013; Kansearch. In contrast, Bayesian optimization starts slowly (like ran-                   dasamy et al., 2017; Klein et al., 2017c; Poloczek et al.,
dom search), but given enough time outperforms Hyperband. Our                         2017). For instance, multi-task Bayesian optimization
new method BOHB achieves the best of both worlds, starting fast                       (Swersky et al., 2013) exploits correlation between tasks
and also converging to the global optimum quickly.                                    to warm-start the optimization procedure. Fabolas (Klein
                                                                                      et al., 2017a) uses similar techniques to evaluate configu-
                                                                                      rations on subsets of the training data and to extrapolate
all existing methods have some strengths and weaknesses,                              their performance to the full dataset. Even though these
but none of them fulfills all of these desiderata. The key con-                       methods achieved both good anytime and final performance,
tribution of this paper is therefore to combine the strengths                         they are based on Gaussian processes, which, as described
of several methods (in particular, Hyperband (Li et al., 2017)                        above, do not satisfy all of our desiderata. Alternative modand a robust & effective variant (Bergstra et al., 2011) of                           els, such as random forests (Hutter et al., 2011) or Bayesian
Bayesian optimization (Brochu et al., 2010; Shahriari et al.,                         neural networks (Snoek et al., 2015; Springenberg et al.,
2016)) to propose a practical HPO method that fulfills all                            2016; Perrone et al., 2017), scale better with the number of
of these desiderata. We first describe Bayesian optimiza-                             dimensions, but with the exception of Klein et al. (2017c)
tion and Hyperband in more detail (Section 3) and then                                have not yet been adopted for multi-fidelity optimization.
show how to combine them in our new method BOHB, as                                   Hyperband (Li et al., 2017) is a bandit strategy that dynamwell as how to effectively parallelize the resulting system                           ically allocates resources to a set of random configurations
(Section 4). Our extensive empirical evaluation (Section 5)                           and uses successive halving (Jamieson & Talwalkar, 2016)
demonstrates that our method combines the best aspects of                             to stop poorly performing configurations. We describe this
Bayesian optimization and Hyperband: it often finds good                              in more detail in Section 3.2. Compared to Bayesian optisolutions over an order of magnitude faster than Bayesian                             mization methods that do not use multiple fidelities, Hyperoptimization and converges to the best solutions orders of                            band showed strong anytime performance, as well as flexibilmagnitudes faster than Hyperband. Figure 1 illustrates this                           ity and scalability to higher-dimensional spaces. However,
pattern in a nutshell for optimizing six hyperparameters of                           it only samples configurations randomly and does not learn
a neural network.                                                                     from previously sampled configurations. This can lead to a
                                                                                      worse final performance than model-based approaches, as
2. Related Work on Model-based                                                        we show empirically in Section 5.
   Hyperparamter Optimization                                                         Concurrently to our work, two other groups (Bertrand
Bayesian optimization has been successfully applied to op-                            et al., 2017; Wang et al., 2018) also attempted to combine
timize hyperparameters of neural networks in many works:                              Bayesian optimization with Hyperband. However, neither of
Snoek et al. (2012) obtained state-of-the-art performance                             them achieve the consistent and large speedups our method
on CIFAR-10 by optimizing the hyperparameters of convo-                               achieves. Furthermore, the method of Bertrand et al. (2017)
lutional neural networks; Bergstra et al. (2014) used TPE                             is based on Gaussian processes and thus shares the limita-
(Bergstra et al., 2011) to optimize a highly parameterized                            tions discussed above. We discuss differences between our
three layer convolutional neural network; and Mendoza et al.                          work and these two papers in more detail in Appendix B.
(2016) won 3 datasets in the 2016 AutoML challenge by
automatically finding the right architecture and hyperparam-

                               BOHB: Robust and Efficient Hyperparameter Optimization at Scale

3. Bayesian Optimization and Hyperband                                Algorithm 1: Pseudocode for Hyperband using Suc-
The validation performance of machine learning algorithms             cessiveHalving (SH) as a subroutine.
can be modelled as a function f : X → R of their hy-                    input :budgets bmin and bmax , η
                                                                                       b
perparameters x ∈ X . We note that the hyperparameter                 1 smax = blogη bmax c
                                                                                         min

configuration space X can include both discrete and contin-           2 for s ∈ {smax , smax − 1, . . . , 0} do
uous dimensions. The hyperparameter optimization (HPO)                3     sample n = d smax  +1
                                                                                             s+1  · η s e configurations
problem is then defined as finding x? ∈ arg minx∈X f (x).             4     run SH on them with η s · bmax as initial budget
Due to the intrinsic randomness of most machine learning
algorithms (e. g. stochastic gradient descent), we assume
that we cannot observe f (x) directly but rather only trough
noisy observations y(x) = f (x)+, with  ∼ N (0, σnoise
                                                       2
                                                           ).        learning model with the specified hyperparameters), in most
We now discuss the two methods for tackling this optimiza-           applications it is possible to define cheap-to-evaluate aption problem in more detail that we will use as components           proximate versions f˜(·, b) of f (·) that are parameterized by
of our new method: Bayesian optimization and Hyperband.              a so-called budget b ∈ [bmin , bmax ]. With the maximum
                                                                     budget b = bmax , we have f˜(·, bmax ) = f (·), whereas with
3.1. Bayesian Optimization                                           b < bmax , f˜(·, b) is only an approximation of f (·) whose
                                                                     quality typically increases with b. In our experiments, we
In each iteration i, Bayesian optimization (BO) uses a               will use this budget to encode the number of iterations for
probabilistic model p(f |D) to model the objective func-             an iterative algorithm, the number of data points used, the
tion f based on the already observed data points D =                 number of steps in an MCMC chain, and the number of
{(x0 , y0 ), . . . , (xi−1 , yi−1 )}. BO uses an acquisition func-   trials in deep reinforcement learning.
tion a : X → R based on the current model p(f |D) that
                                                                     Hyperband (HB) (Li et al., 2017) is a multi-armed bantrades off exploration and exploitation. Based on the model
                                                                     dit strategy for hyperparameter optimization that takes adand the acquisition function, it iterates the following three
                                                                     vantage of these different budgets b by repeatedly calling
steps: (1) select the point that maximizes the acquisition
                                                                     SuccessiveHalving (SH) (Jamieson & Talwalkar, 2016) to
function xnew = arg maxx∈X a(x), (2) evaluate the objec-
                                                                     identify the best out of n randomly sampled configurations.
tive function ynew = f (xnew )+, and (3) augment the data
                                                                     It balances very agressive evaluations with many configu-
D ← D ∪ (xnew , ynew ) and refit the model. A common
                                                                     rations on the smallest budget, and very conservative runs
acquisition function is the expected improvement (EI) over
                                                                     that are directly evaluated on bmax . The exact procedure
the currently best observed value α = min{y0 , . . . , yn }:
                       Z                                             for this trade-off is shown in Algorithm 1 (with pseudocode
                                                                     for SH shown in Appendix C). Line 1 computes the geo-
           a(x) = max(0, α − f (x))dp(f |D).                   (1)
                                                                     metrically spaced budget ∈ [bmin , bmax ]. The number of
                                                                     configurations sampled in line 3 is chosen such that ev-
Tree Parzen Estimator. The Tree Parzen Estimator                     ery SH run requires the same total budget. SH internally
(TPE) (Bergstra et al., 2011) is a Bayesian optimization             evaluates configurations on a given budget, ranks them by
method that uses a kernel density estimator to model the             their performance, and continues the top η −1 (usually the
densities                                                            best-performing third) on a budget η times larger. This is
                 l(x) = p(y < α|x, D)                                repeated until the maximum budget is reached. In prac-
                                                     (2)
                 g(x) = p(y > α|x, D)                                tice, HB works very well and typically outperforms random
over the input configuration space instead of modeling the           search and Bayesian optimization methods operating on
objective function f directly by p(f |D). To select a new            the full function evaluation budget quite easily for small
candidate xnew to evaluate, it maximizes the ratio l(x)/g(x);        to medium total budgets. However, its convergence to the
Bergstra et al. (2011) showed that this is equivalent to max-        global optimum is limited by its reliance on randomly-drawn
imizing EI in Equation (1). Due to the nature of kernel              configurations, and with large budgets its advantage over
density estimators, TPE easily supports mixed continuous             random search typically diminishes.
and discrete spaces, and model construction scales linearly
in the number of data points (in contrast to the cubic-time          4. Model-Based Hyperband
Gaussian processes (GPs) predominant in the BO literature).
                                                                     We now introduce our new practical HPO method, which we
                                                                     dub BOHB since it combines Bayesian optimization (BO)
3.2. Hyperband
                                                                     and Hyperband (HB). We designed BOHB to satisfy all the
While the objective function f : X → R is typically ex-              desiderata described in the introduction. HB already satispensive to evaluate (since it requires training a machine            fies most of these desiderata (in particular, strong anytime

                             BOHB: Robust and Efficient Hyperparameter Optimization at Scale

performance, scalability, robustness and flexibility), and we    the input space. To fit useful KDEs (in line 4 of Algorithm
combine it with BO to also satisfy the desideratum of strong     2), we require a minimum number of data points Nmin ; this
final performance in BOHB. We also describe how to extend        is set to d + 1 for our experiments, where d is the number of
BOHB to make effective use of parallel resources.                hyperparameters. To build a model as early as possible, we
                                                                 do not wait until Nb = |Db |, the number of observations for
In the design of BOHB’s BO component, on top of the five
                                                                 budget b, is large enough to satisfy q · Nb ≥ Nmin . Instead,
desiderata above, we also followed two additional ones:
                                                                 after initializing with Nmin + 2 random configurations (line
                                                                 3), we choose the
6. Simplicity. Simplicity is a virtue, since simple approaches can be easily verified, have less components that                      Nb,l = max(Nmin , q · Nb )
can break, and can be easily reimplemented in different                                                                     (3)
                                                                               Nb,g = max(Nmin , Nb − Nb,l )
frameworks. HB is very simple, but standard GP-BO methods are not: they tend to require complex approximations,        best and worst configurations, respectively, to model the
complex MCMC sampling over hyperparameters, and for              two densities. This ensures that both models have enough
good performance also data-dependent choices of kernel           datapoints and have the least overlap when only a limited
functions and hyperpriors.                                       number of observations is available. We used the KDE im-
7. Computational efficiency. Since our HB component al-          plementation from statsmodels (Seabold & Perktold, 2010),
lows us to carry out many function evaluations at small          estimating the KDE’s bandwidth with the default estimation
budgets, the cubic complexity of standard GPs, and even          procedure (Scott’s rule of thumb), which is efficient and
the lower complexity of approximate GPs would become             performed well in our experience. Details on our KDE are
problematic. Furthermore, compared to these cheap func-          given in Appendix D.
tion evaluations, the complexity of computing sophisticated      As the optimization progresses, more configurations are
acquisition functions may also become a bottleneck, espe-        evaluated on bigger budgets. Given that the goal is to opticially when parallelization effectively reduces the cost of      mize on the largest budget, BOHB always uses the model
function evaluations.                                            for the largest budget for which enough observations are
                                                                 available (line 2). This enables it to overcome wrong con-
For these reasons, along with the reasons of scalability,        clusions drawn on smaller budgets by eventually relying on
robustness & flexibility, we based BOHB’s BO component           results with the highest fidelity only.
on the simple TPE method discussed above. As reliable
GP-based BO methods become available that satisfy all the        To optimize EI (lines 5-6), we sample Ns points from l0 (x),
desiderata above, it would be easy to replace TPE with them.     which is the same KDE as l(x) but with all bandwidths
                                                                 multiplied by a factor bw to encourage more exploration
                                                                 around the promising configurations. We observed that
4.1. Algorithm description
                                                                 this improves convergence especially in the late stages of
BOHB relies on HB to determine how many configurations           the optimization, when the model on the biggest budget is
to evaluate with which budget, but it replaces the random        queried frequently but updated rarely.
selection of configurations at the beginning of each HB it-
                                                                 In order to keep the theoretical guarantees of HB, we also
eration by a model-based search. Once the desired number
                                                                 sample a constant fraction ρ of the configurations uniformly
of configurations for the iteration is reached, the standard
                                                                 at random (line 1). Besides global exploration, this guarsuccessive halving procedure is carried out using these con-
                                                                 antees that after m · (smax + 1) SH runs, our method has
figurations. We keep track of the performance of all function
                                                                 (on average) evaluated ρ · m · (smax + 1) random configevaluations g(x, b) +  of configurations x on all budgets b
                                                                 urations on bmax . As every SH run consumes a budget of
to use as a basis for our models in later iterations.
                                                                 at most (smax + 1) · bmax , in the same time random search
We follow HB’s way of choosing the budgets and continue          evaluates (ρ−1 · (smax + 1))-times as many configuration
to use SH, but we replace the random sampling by a BO            on the largest budget. This means, that in the worst case
component to guide the search. We construct a model and          (when the lower fidelities are misleading), BOHB is at most
use BO to select a new configuration, based on the configu-      this factor times slower than RS, but it is still guaranteed to
rations evaluated so far. In the remainder of this section, we   converge eventually. The same argument holds for HB, but
will explain this procedure summarized by the pseudocode         in practice both HB and BOHB substantially outperform RS
in Algorithm 2.                                                  in our experiments.
The BO part of BOHB closely resembles TPE, with one              No optimizer is free of hyperparameters itself, and their
major difference: we opted for a single multidimensional         effects have to be studied carefully. We therefore include a
KDE compared to the hierarchy of one-dimensional KDEs            detailed empirical analysis of BOHB’s hyperparameters in
used in TPE in order to better handle interaction effects in     Appendix G that shows each hyperparameter’s effect when

                            BOHB: Robust and Efficient Hyperparameter Optimization at Scale
                                                                                                   letter
                                                                         100
all others are fixed to their default values (these are also                                                         n=1
listed there). We find that BOHB is quite insensitive to its                                                         n=2

hyperparameters, with the default working robustly across                −1
                                                                        10
                                                                                                                     n=4
                                                                                                                     n=8
different scenarios.
                                                               regret
                                                                                                                     n = 16
                                                                                                                     n = 32

4.2. Parallelization                                                    10−2

Modern optimizers must be able to take advantage of parallel resources effectively and efficiently. BOHB achieves              10−3 0
                                                                           10     101        102            103   104         105
that by inheriting properties from both TPE and HB. The
                                                                                           wall clock time [s]
parallelism in TPE is achieved by limiting the number of
samples to optimize EI, purposefully not optimizing it fully
to obtain diversity. This ensures that consecutive sugges-     Figure 2. Performance of our method with different number of
                                                               parallel workers on the letter surrogate benchmark (see Sec. 5) for
tions by the model are diverse enough to yield near-linear
                                                               128 iterations. The speedup for two and four workers is close to
speedups when evaluationed in parallel. On the other hand,
                                                               linear, for more workers it becomes sublinear. For example, the
HB can be parallelized by (a) starting different iterations    speedup to achieve a regret of 10−2 for one vs. 32 workers is ca.
at the same time (a parallel for loop in Alg. 1), and (b)      2000s/130s ≈ 15. We plot the mean and twice the standard error
evaluating configurations concurrently within each SH run.     of the mean over 128 runs.
Our parallelization strategy of BOHB is as follows. We
start with the first SH run that sequential HB would perform
(the most aggressive one, starting from the lowest budget),
sampling configurations with the strategy outlined in Algorithm 2 until either (a) all workers are busy, or (b) enough
configurations have been sampled for this SH run. In case      running its SH runs in parallel. In contrast to this approach
(a), we simply wait for a worker to free up and then sample    of parallelizing HB by having separate pools of workers for
a new configuration. In case (b), we start the next SH run     each SH run, we rather join all workers into a single pool,
in parallel, sampling the configurations to run for it also    and whenever a worker becomes available preferentially
according to Algorithm 2; observations D (and therefore        execute waiting runs with smaller budgets. New SH runs
the resulting models) are shared across all SH runs. BOHB      are only started when the SH runs currently executed are not
is an anytime algorithm that at each point in time keeps       waiting for a worker to free up. This strategy (a) allows us to
track of the configuration that achieved the best validation   achieve better speedups by using all workers in the most agperformance; it can also be given a maximum budget of SH       gressive (and often most effective) bracket first, and (b) also
runs.                                                          takes full advantage of models built on smaller budgets. Fig-
                                                               ure 2 demonstrates that our method of parallelization can
We note that SH has also been parallelized in (so far un-      effectively exploit many parallel workers.
published) independent work (Li et al., 2018). Next to
parallelizing SH runs (by filling the next free worker with
the ready-to-be-executed run with the largest budget), that    5. Experiments
work mentioned that HB can trivially be parallelized by        We now comprehensively evaluate BOHB’s empirical per-
                                                               formance in a wide range of tasks, including a high-
                                                               dimensional toy function, as well as optimizing the hy-
                                                               perparameters of support vector machines, feed-forward
 Algorithm 2: Pseudocode for sampling in BOHB
                                                               neural networks, Bayesian neural networks, deep reinforce-
   input :observations D, fraction of random runs ρ,           ment learning agents and convolutional neural networks.
            percentile q, number of samples Ns ,               Code for BOHB and our benchmarks is publicly available
            minimum number of points Nmin to build a           at https://github.com/automl/HpBandSter
            model, and bandwidth factor bw
   output :next configuration to evaluate                      To compare against TPE, we used the Hyperopt package
 1 if rand() < ρ then return random configuration              (Bergstra et al., 2011), and for all GP-BO methods we used
 2 b = arg max {Db : |Db | ≥ Nmin + 2}                         the RoBO python package (Klein et al., 2017b). In all exper-
 3 if b = ∅ then return random configuration                   iments we set η = 3 for HB and BOHB as recommended by
 4 fit KDEs according to Eqs. (2) and (3)                      Li et al. (2017). If not stated otherwise, for all methods we
 5 draw Ns samples according to l (x) (see text)
                                   0                           report the mean performance and the standard error of the
 6 return sample with highest ratio l(x)/g(x)                  mean of the best observed configuration so far (incumbent)
                                                               at a given budget.

                                BOHB: Robust and Efficient Hyperparameter Optimization at Scale

5.1. Artificial Toy Function: Counting Ones
In this experiment we investigated BOHB’s behavior in
high-dimensional mixed continuous / categorical configuration spaces. Since GP-BO methods do not work well on
such configuration spaces (Eggensperger et al., 2013) we
do not include them in this experiment. However, we do
use SMAC (Hutter et al., 2011), since its random forest
are known to perform well in high-dimensional categorical
spaces (Eggensperger et al., 2013).
Given a set of Ncat categorical variables x ∈ {0, 1} and
Ncont continuous variables x ∈ [0, 1], we defined the counting one problem as:
                N              NcatX
                                   +Ncont
                                                                 Figure 3. Results for the counting ones problem in 16 dimensional
                Xcat
                                                                 space with 8 categorical and 8 continuous hyperparameters. In
     f (x) = −(         xi +                EX∼Bj (X) [X]).
                                                                 higher dimensional spaces RS-based methods need exponentially
                  i=0          j=Ncat +1
                                                                 more samples to find good solutions.
The expectation is taken with respect to a Bernoulli distribution Bj with parameter p = xj . As a budget for HB and         we again computed the immediate regret of the incumbent.
BOHB, we used the number of samples b ∈ [9, 729] allowed
to approximate this expectation; all other methods always        5.2.1. S UPPORT V ECTOR M ACHINE ON MNIST
evaluated on the full budget, i. e., b = 729.
                                                                 To compare against GP-BO, we used the support vector
For each method, we performed 512 independent runs and           machine on MNIST surrogate from Klein et al. (2017a).
report the immediate regret |f (xinc ) − f (x∗ )| where x∗ ∈     This surrogate imitates the hyperparameter optimization
arg min f (x) and xinc is the incumbent at a specific time       of a support vector machine with a RBF kernel with two
step. Figure 3 shows the results for a 16-dimensional space      hyperparameters: the regularization parameter C and the
with Ncat = 8 and Ncont = 8 parameters. The results for          kernel parameter γ. The budget is given by the number of
other dimensions can be found in Appendix H.                     training datapoints, where the minimum budget is 1/512 of
Random search worked very poorly on this benchmark and           the training data and the maximum budget is the full training
was quickly dominated by the model-based methods SMAC            data. For further details, we refer to Klein et al. (2017a).
and TPE. Even though HB was faster in the beginning,             Figure 4 compares BOHB to various BO methods, such as
SMAC and TPE clearly outperformed it after having ob-            Fabolas (Klein et al., 2017a), multi-task Bayesian optimizatained a sufficiently informative model. BOHB worked as          tion (MTBO) (Swersky et al., 2013), GP-BO with expected
well as HB in the beginning and then quickly started to          improvement (Snoek et al., 2012; Klein et al., 2017b), RS
perform better and — as the only method — converged in           and HB. BOHB achieved similar performance as Fabolas
the time budget. We obtained similar results for other di-       and worked slightly better than HB. We note that this is a
mensionalities (see Figure 7 in the supplementary material).     low-dimensional continuous problem, for which it is well
However, we note that with as many as 64 dimensions, TPE         known that GP-BO methods usually work better than other
and SMAC started to perform better than BOHB since the           methods, such as kernel density estimators (Eggensperger
noise grows and evaluating configurations on a smaller bud-      et al., 2013).
get does not help to build better models for the full budget.
                                                                 5.2.2. F EED - FORWARD N EURAL N ETWORKS ON
5.2. Comprehensive Experiments on Surrogate                             O PEN ML DATASETS
     Benchmarks
                                                                 We optimized six hyperparameters that control the training
For the next experiments we constructed a set of surrogate       procedure (initial learning rate, batch size, dropout, exponenbenchmarks based on offline data following Eggensperger          tial decay factor for learning rate) and the architecture (numet al. (2015). Optimizing a surrogate instead of the real        ber of layers, units per layer) of a feed forward neural netobjective function is substantially cheaper, which allows us     work for six different datasets gathered from OpenML (Vanto afford many independent runs for each optimizer and to        schoren et al., 2014): Adult (Kohavi, 1996), Higgs (Baldi
draw statistically more meaningful conclusions. A more de-       et al., 2014), Letter (Frey & Slate, 1991), MNIST (LeCun
tailed discussion of how we generated these surrogates can       et al., 2001), Optdigits (Lichman, 2013), and Poker (Cattral
be found in Appendix I in the supplementary material. To         et al., 2002). A detailed description of all hyperparameter
better compare the convergence towards the true optimum,         ranges and training budgets can be found in Appendix I.

                              BOHB: Robust and Efficient Hyperparameter Optimization at Scale

                                                                                                           poker
                                                                             100

                                                                            10−1
                                                                                         RS

                                                                   regret   10−2         TPE
                                                                                         GP-BO
                                                                                         HB
                                                                             −3
                                                                            10           HB-LCNet
                                                                                         BOHB

                                                                            10−4
                                                                                   102         103      104        105     106   107
                                                                                                     wall clock time [s]

                                                                   Figure 5. Optimizing six hyperparameter of a feed-forward neural
Figure 4. Comparison on the SVM on MNIST surrogates as de-         network on featurized datasets; results are based on surrogate
scribed in Klein et al. (2017a). BOHB works similarly to Fabolas   benchmarks. Results for the other 5 datasets are qualitatively
on this two dimensional benchmark and outperforms MTBO and         similar and are shown in Figure 1 in the supplementary material.
HB.

We ran random search (RS), TPE, HB, GP-BO, Hyperband
                                                                   our knowledge this is the first application of hyperparameter
with LC-Net (HB-LCNet, see Klein et al. (2017c)) and
                                                                   optimization for Bayesian neural networks.
BOHB on all six datasets and summarize the results for one
of them in Figure 5. Figures for the other datasets are shown      As tunable hyperparameters, we exposed the step length,
in Appendix E.                                                     the length of the burn-in period, the number of units in each
                                                                   layer, and the decay parameter of the momentum variable. A
We note that HB initially performed much better than the
                                                                   detailed description of the configuration space can be found
vanilla BO methods and achieved a roughly three-fold
                                                                   in Appendix J. We used the Bayesian neural network imspeedup over RS. However, for large enough budgets TPE
                                                                   plementation provided in the RoBO python package (Klein
and GP-BO caught up in all cases, and in the end found
                                                                   et al., 2017b) as described by Springenberg et al. (2016).
better configurations than HB and RS. HB and BOHB
started out identically, but BOHB achieved the same final          We considered two different UCI (Lichman, 2013) regresperformance as HB 100 times faster, while at the same              sion datasets, Boston housing and protein structure as detime yielding a final result that was better than that of          scribed by Hernández-Lobato & Adams (2015) and report
the other BO methods. All model-based methods substan-             the negative log-likelihood of the validation data. For BOHB
tially outperformed RS at the end of their budget, whereas         and HB, we set the minimum budget to 500 MCMC steps
HB approached the same performance. Interestingly, the             and the maximum budget to 10000 steps. RS and TPE
speedups that TPE and GP-BO achieved over RS are com-              evaluated each configuration on the maximum budget. For
parable to the speedups that BOHB achieved over HB. Fi-            each hyperparameter optimization method, we performed
nally, HB-LCNet performed somewhat better than HB alone,           50 independent runs to obtain statistically significant results.
but consistently worse than BOHB, even when tuning HB-
                                                                   As Figure 6 shows, HB initially performed better than TPE,
LCNet. We only compare to HB-LCNet on this benchmark,
                                                                   but TPE caught up given enough time. BOHB converged
since it is the only one that includes full learning curves
                                                                   faster than both HB and TPE and even found a better con-
(for which the parametric functions in HB-LCNet were de-
                                                                   figuration than the baselines on the Boston housing dataset.
signed). Also, HB-LCNet requires access to performance
values for all budgets, which we do not obtain when, e.g.,
using data subset sizes as a budget, and we thus expect            5.4. Reinforcement Learning
HB-LCNet to perform poorly in the other cases.                     Next, we optimized eight hyperparameters of proximal pol-
                                                                   icy optimization (PPO) (Schulman et al., 2017) to learn
5.3. Bayesian Neural Networks                                      the cartpole swing-up task. For PPO, we used the imple-
                                                                   mentation from the TensorForce framework developed by
For this experiment we optimized the hyperparameters and
                                                                   Schaarschmidt et al. (2017) and we used the implementation
the architecture of a two-layer fully connected Bayesian
                                                                   from OpenAI Gym (Brockman et al., 2016) for the cartpole
neural network trained with Markov Chain Monte-Carlo
                                                                   environment. The configuration space for this experiment
(MCMC) sampling. We used stochastic gradient Hamilto-
                                                                   can be found in Appendix K.
nian Monte-Carlo sampling (SGHMC) (Chen et al., 2014)
with scale adaption (Springenberg et al., 2016) to sample          To find a configuration that not only converges quickly but
the parameter vector of the network. Note that to the best of      also works robustly, for each function evaluation we ran a

                                                    BOHB: Robust and Efficient Hyperparameter Optimization at Scale

                                                 Boston Housing                      5.5. Convolutional Neural Networks on CIFAR-10
                           9
                                                                                     For a final evaluation, we optimized the hyperparameters of

negative log-likelihood
                           8                                                         a medium-sized residual network (depth 20 and basewidth of
                           7                                                         64; roughly 8.5M parameters) with Shake-Shake (Gastaldi,
                                                                                     2017) and Cutout (DeVries & Taylor, 2017) regularization.
                           6                                                         To perform hyperparameter optimization, we split off 5 000
                                    RS
                           5       TPE                                               training images as a validation set. As hyperparameters,
                                    HB                                               we optimized learning rate, momentum, weight decay, and
                           4
                                   BOHB                                              batch size.
                           3 4
                           10                       105                       106    We ran BOHB with budgets of 22, 66, 200, and 600 epochs,
                                                  MCMC steps                         using 19 parallel workers. Each worker used 2 NVIDIA
                                                                                     TI 1080 GPUs for parallel training, which resulted in runs
                                                                                     with the longest budget taking approximately 7 hours (on 2
Figure 6. Optimization of 5 hyperparameters of a Bayesian neural
                                                                                     GPUs). The complete BOHB run of 16 iterations required a
network trained with SGHMC. Many random hyperparameter
configurations lead to negative log-likelihoods orders of magnitude
                                                                                     total of 33 GPU days (corresponding to a cost of less than
higher than the best performing ones. We clip the y-axis at 9 to                     3 full function evaluations on each of the 19 workers) and
ensure visibility in the plot.                                                       achieved a test error of 2.78% ± 0.09% (which is better
                                                                                     than the error Gastaldi (2017) obtained with a slightly larger
                                                    Cartpole                         network). While we note that the performance numbers
                           104                                                       from different papers are not directly comparable due to the

epochs until convergence
                                                                                     use of different optimization and regularization approaches,
                                                                                     it is still instructive to compare this result to others in the
                                                                                     literature. Our result is better than that of last year’s state-of-
                           103                                                       the-art neural architecture search by reinforcement learning
                                     RS
                                     TPE
                                                                                     (3.65% (Zoph & Le, 2017)) and the recent paper on progres-
                                     HB                                              sive neural architecture search (3.41% (Liu et al., 2017)),
                                    BOHB                                             but it does not quite reach the state-of-the-art performance
                           102 1                                                     of 2.4% and 2.1% reported in recent arXiv papers on re-
                             10            102         103        104        105     inforcement learning (Zoph et al., 2017) and evolutionary
                                                     time [s]                        search (Real et al., 2018). However, since these approaches
                                                                                     used 60 to 95 times more compute resources (2 000 and
Figure 7. Hyperparameter optimization of 8 hyperparameters of                        3 150 GPU days, respectively!), as well as networks with
PPO on the cartpole task. BOHB starts as well as HB but converges                    3-4 more parameters, we believe that our results are a strong
to a much better configuration.                                                      indication of the practical usefulness of BOHB for resource-
                                                                                     constrained optimization.

configuration for nine individual trials with a different seed
for the random number generator. We returned the average
                                                                                     6. Conclusions
number of episodes until PPO has converged to the opti-                              We introduced BOHB, a simple yet effective method for
mum, defining convergence to mean that the reinforcement                             hyperparameter optimization satisfying the desiderata outlearning agent achieved the highest possible reward for 20                           lined above: it is robust, flexible, scalable (to both high diconsecutive episodes. For each hyperparameter configura-                             mensions and parallel resources), and achieves both strong
tion we stopped training after the agent has either converged                        anytime performance and strong final performance. We
or ran for a maximum of 3000 episodes. The minimum                                   thoroughly evaluated its performance on a diverse set of
budget for BOHB and HB was one trial and the maximum                                 benchmarks and demonstrated its improved performance
budget were nine trials, and all other methods used a fixed                          compared to a wide range of other state-of-the-art apnumber of nine trials. As in the previous benchmark, for                             proaches. Our easy-to-use open-source implementation
each hyperparameter optimization method we performed 50                              (available under https://github.com/automl/HpBandSter)
independent runs.                                                                    should allow the community to effectively use our method
Figure 7 shows that HB and BOHB worked equally well in                               on new problems. To further improve BOHB, we will conthe beginning, but BOHB converged to better configurations                           sider an automatic adaptation of the budgets used to alleviate
in the end. Apparently, the budget for this benchmark was                            the problem of misspecification by the user while maintainnot sufficient for TPE to find the same configuration.                               ing the versatility and robustness of the current version.

                               BOHB: Robust and Efficient Hyperparameter Optimization at Scale

Acknowledgements                                                      DeVries, T. and Taylor, G. W. Improved regularization of
                                                                        convolutional neural networks with cutout. arXiv preprint
We thank Ilya Loshchilov for suggesting to track the best               arXiv:1708.04552, 2017.
hyperparameter setting across different budgets (already in
                                                                      Eggensperger, K., Feurer, M., Hutter, F., Bergstra, J., Snoek, J.,
late 2015), which influenced our thoughts about the problem             Hoos, H., and Leyton-Brown, K. Towards an empirical founand ultimately the development of BOHB. This work has                   dation for assessing Bayesian optimization of hyperparameters.
partly been supported by the European Research Council                  In NIPS Workshop on Bayesian Optimization in Theory and
(ERC) under the European Union’s Horizon 2020 research                  Practice (BayesOpt’13), 2013.
and innovation programme under grant no. 716721, by the               Eggensperger, K., Hutter, F., Hoos, H., and Leyton-Brown, K.
European Commission under grant no. H2020-ICT-645403-                   Efficient benchmarking of hyperparameter optimizers via sur-
ROBDREAM, and by the German Research Foundation                         rogates. In Bonet, B. and Koenig, S. (eds.), Proceedings of the
(DFG) under Priority Programme Autonomous Learning                      Twenty-nineth National Conference on Artificial Intelligence
(SPP 1527, grant BR 3815/8-1 and HU 1900/3-1) Fur-                      (AAAI’15), pp. 1114–1120. AAAI Press, 2015.
thermore, the authors acknowledge support by the state of             Frey, P. W. and Slate, D. J. Letter recognition using holland-style
Baden-Württemberg through bwHPC and the DFG through                      adaptive classifiers. Machine Learning, 6(2):161–182, Mar
grant no INST 39/963-1 FUGG.                                            1991.
                                                                      Gastaldi, X. Shake-shake regularization.           arXiv preprint
References                                                              arXiv:1705.07485, 2017.

Proceedings of the International Conference on Learning Repre-        Henderson, P., Islam, R., Bachman, P., Pineau, J., Precup, D., and
  sentations (ICLR’17), 2017. Published online: iclr.cc.                Meger, D. Deep reinforcement learning that matters. arXiv
                                                                        preprint arXiv:1709.06560, 2017.
Bach, F. and Blei, D. (eds.). Proceedings of the 32nd International
  Conference on Machine Learning (ICML’15), volume 37, 2015.          Hernández-Lobato, J. and Adams, R. Probabilistic backpropaga-
  Omnipress.                                                            tion for scalable learning of Bayesian neural networks. In Bach
                                                                        & Blei (2015).
Baldi, P., Sadowski, P., and Whiteson, D. Searching for exotic
  particles in high-energy physics with deep learning. Nature         Hutter, F., Hoos, H., and Leyton-Brown, K. Sequential model-
  communications, 5, 2014.                                              based optimization for general algorithm configuration. In
                                                                        Coello, C. (ed.), Proceedings of the Fifth International Con-
Bergstra, J., Bardenet, R., Bengio, Y., and Kégl, B. Algorithms for
                                                                        ference on Learning and Intelligent Optimization (LION’11),
  hyper-parameter optimization. In Shawe-Taylor, J., Zemel, R.,
                                                                        volume 6683 of Lecture Notes in Computer Science, pp. 507–
  Bartlett, P., Pereira, F., and Weinberger, K. (eds.), Proceedings
                                                                        523. Springer-Verlag, 2011.
  of the 25th International Conference on Advances in Neural
  Information Processing Systems (NIPS’11), pp. 2546–2554,            Jamieson, K. and Talwalkar, A. Non-stochastic best arm identifi-
  2011.                                                                 cation and hyperparameter optimization. In Proceedings of the
Bergstra, J., Yamins, D., and Cox, D. Making a science of model         Seventeenth International Conference on Artificial Intelligence
  search: Hyperparameter optimization in hundreds of dimensions         and Statistics (AISTATS), 2016.
  for vision architectures. In Dasgupta, S. and McAllester, D.        Kandasamy, K., Dasarathy, G., Schneider, J., and Poczos, B. Multi-
  (eds.), Proceedings of the 30th International Conference on           fidelity bayesian optimisation with continuous approximations.
  Machine Learning (ICML’13), pp. 115–123. Omnipress, 2014.             arXiv preprint arXiv:1703.06240, 2017.
Bertrand, H., Ardon, R., Perrot, M., and Bloch, I. Hyperparameter
                                                                      Klein, A., Falkner, S., Bartels, S., Hennig, P., and Hutter, F. Fast
  optimization of deep neural networks: Combining hyperband
                                                                        Bayesian hyperparameter optimization on large datasets. Elec-
  with Bayesian model selection. Proceedings of Conférence sur
                                                                        tron. J. Statist., 11(2):4945–4968, 2017a.
  l’Apprentissage Automatique (CAP 2017), 2017.
Brochu, E., Cora, V., and de Freitas, N. A tutorial on Bayesian       Klein, A., Falkner, S., Mansur, N., and Hutter, F. Robo: A flexible
  optimization of expensive cost functions, with application to         and robust bayesian optimization framework in python. In NIPS
  active user modeling and hierarchical reinforcement learning.         2017 Bayesian Optimization Workshop, December 2017b.
  arXiv:1012.2599, 2010.                                              Klein, A., Falkner, S., Springenberg, J. T., and Hutter, F. Learning
Brockman, G., Cheung, V., Pettersson, L., Schneider, J., Schulman,      curve prediction with Bayesian neural networks. In Proceedings
  J., Tang, J., and Zaremba, W. Openai gym, 2016.                       of the International Conference on Learning Representations
                                                                        (ICLR’17) icl (2017). Published online: iclr.cc.
Cattral, R., Oppacher, F., and Deugo, D. Evolutionary data min-
  ing with automatic rule generalization. Recent Advances in          Kohavi, R. Scaling up the accuracy of naive-bayes classifiers: A
  Computers, Computing and Communications, 1(1):296–300,                decision-tree hybrid. In KDD, volume 96, pp. 202–207, 1996.
  2002.
                                                                      LeCun, Y., Bottou, L., Bengio, Y., and Haffner, P. Gradient-based
Chen, T., Fox, E., and Guestrin, C. Stochastic gradient Hamiltonian     learning applied to document recognition. In Haykin, S. and
  Monte Carlo. In Xing, E. and Jebara, T. (eds.), Proceedings           Kosko, B. (eds.), Intelligent Signal Processing, pp. 306–351.
  of the 31th International Conference on Machine Learning,             IEEE Press, 2001. URL http://www.iro.umontreal.
  (ICML’14). Omnipress, 2014.                                           ca/~lisa/pointeurs/lecun-01a.pdf.

                                 BOHB: Robust and Efficient Hyperparameter Optimization at Scale

Li, L., Jamieson, K., DeSalvo, G., Rostamizadeh, A., and Tal-             Springenberg, J., Klein, A., Falkner, S., and Hutter, F. Bayesian
   walkar, A. Hyperband: Bandit-based configuration evaluation              optimization with robust bayesian neural networks. In Lee,
   for hyperparameter optimization. In Proceedings of the Interna-          D., Sugiyama, M., von Luxburg, U., Guyon, I., and Garnett,
   tional Conference on Learning Representations (ICLR’17) icl              R. (eds.), Proceedings of the 30th International Conference on
   (2017). Published online: iclr.cc.                                       Advances in Neural Information Processing Systems (NIPS’16),
                                                                            2016.
Li, L., Jamieson, K., Rostamizadeh, A., Gonina, K., Hardt, M.,
   Recht, B., and Talwalkar, A. Massively parallel hyperparameter         Swersky, K., Snoek, J., and Adams, R. Multi-task Bayesian op-
   tuning, 2018. URL https://openreview.net/forum?                          timization. In Burges, C., Bottou, L., Welling, M., Ghahra-
   id=S1Y7OOlRZ.                                                            mani, Z., and Weinberger, K. (eds.), Proceedings of the 27th
                                                                            International Conference on Advances in Neural Information
Lichman, M. UCI machine learning repository, 2013. URL http:                Processing Systems (NIPS’13), pp. 2004–2012, 2013.
  //archive.ics.uci.edu/ml.
                                                                          Swersky, K., Snoek, J., and Adams, R. Freeze-thaw bayesian
Liu, C., Zoph, B., Shlens, J., Hua, W., Li, L.-J., Fei-Fei, L., Yuille,     optimization. arXiv:1406.3896, 2014.
  A., Huang, J., and Murphy, K. Progressive neural architecture
  search. arXiv preprint arXiv:1712.00559, 2017.                          Vanschoren, J., van Rijn, J., Bischl, B., and Torgo, L. OpenML:
                                                                            Networked science in machine learning. SIGKDD Explor.
Melis, G., Dyer, C., and Blunsom, P. On the state of the                    Newsl., 15(2):49–60, June 2014.
 art of evaluation in neural language models. arXiv preprint
 arXiv:1707.05589, 2017.                                                  Wang, J., Xu, J., and Wang, X. Combination of hyperband and
                                                                            bayesian optimization for hyperparameter optimization in deep
Mendoza, H., Klein, A., Feurer, M., Springenberg, J., and Hutter,           learning. arXiv preprint arxiv:1801.01596, 01 2018.
 F. Towards automatically-tuned neural networks. In ICML 2016
 AutoML Workshop, 2016.                                                   Zoph, B. and Le, Q. V. Neural architecture search with reinforce-
                                                                            ment learning. In Proceedings of the International Conference
Perrone, V., Jenatton, R., Seeger, M., and Archambeau, C. Mul-              on Learning Representations (ICLR’17) icl (2017). Published
  tiple adaptive bayesian linear regression for scalable bayesian           online: iclr.cc.
  optimization with warm start. arXiv preprint arXiv:1712.02902,
  2017.                                                                   Zoph, B., Vasudevan, V., Shlens, J., and Le, Q. V. Learning
                                                                            transferable architectures for scalable image recognition. In
Poloczek, M., Wang, J., and Frazier, P. Multi-information source            arXiv:1707.07012 [cs.CV], 2017.
  optimization. In Advances in Neural Information Processing
  Systems, pp. 4291–4301, 2017.

Real, E., Aggarwal, A., Huang, Y., and Le, Q. V. Regular-
  ized Evolution for Image Classifier Architecture Search. In
  arXiv:1802.01548 [cs], February 2018.

Schaarschmidt, M., Kuhnle, A., and Fricke, K. Tensorforce: A
  tensorflow library for applied reinforcement learning. Web page,
  2017.     URL https://github.com/reinforceio/
  tensorforce.

Schulman, J., Wolski, F., Dhariwal, P., Radford, A., and Klimov,
  O. Proximal policy optimization algorithms. arXiv preprint
  arXiv:1707.06347, 2017.

Seabold, S. and Perktold, J. Statsmodels: Econometric and statisti-
  cal modeling with python. In 9th Python in Science Conference,
  2010.

Shahriari, B., Swersky, K., Wang, Z., Adams, R., and de Freitas,
  N. Taking the human out of the loop: A review of Bayesian
  optimization. Proceedings of the IEEE, 104(1):148–175, 2016.

Snoek, J., Larochelle, H., and Adams, R. P. Practical Bayesian
  optimization of machine learning algorithms. In Bartlett, P.,
  Pereira, F., Burges, C., Bottou, L., and Weinberger, K. (eds.),
  Proceedings of the 26th International Conference on Advances
  in Neural Information Processing Systems (NIPS’12), pp. 2960–
  2968, 2012.

Snoek, J., Rippel, O., Swersky, K., Kiros, R., Satish, N., Sundaram,
  N., Patwary, M., Prabhat, and Adams, R. Scalable Bayesian
  optimization using deep neural networks. In Bach & Blei (2015),
  pp. 2171–2180.

                                                                 Supplementary material for:
                                                BO-HB: Robust and Efficient Hyperparameter Optimization at Scale

                                                                            Stefan Falkner 1 Aaron Klein 1 Frank Hutter 1

                                        A. Available Software                                           benefit from any of the evaluations in previous iterations.
                                                                                                        In contrast, BOHB collects all evaluations on all budgets
                                        To promote reproducible science and enable other re-            and uses the largest budget with enough evaluations (admit-
                                        searchers to use our method, we provide an open-source

arXiv:1807.01774v1 [cs.LG] 4 Jul 2018
                                                                                                        tedly a heuristic, but we would argue a reasonable one) as
                                        implementation of BOHB and Hyperband. It is available           a base for future evaluations. This way, BOHB aggregates
                                        under https://github.com/automl/HpBandSter. The bench-          more knowledge into its models for the different budgets
                                        marks and our scripts used to produce the data shown in the     as the optimization progresses. We believe this to be a cru-
                                        paper can be found in the icml_2018 branch.                     cial part of the strong performance of our method. Empiri-
                                                                                                        cally, Wang et al. (2018) did not achieve the consistent and
                                        B. Comparison to other Combinations of                          large speedups across a wide range of applications BOHB
                                           Bayesian optimization and Hyperband                          achieved in our experiments.

                                        Here we discuss the differences between our method and
                                        the related approaches of Bertrand et al. (2017) and Wang       C. Successive Halving
                                        et al. (2018) in more detail. We note that these works are      SuccessiveHalving is a simple heuristic to allocate more
                                        independent and concurrent; our work extends our prelim-        resources to promising candidates. For completeness, we
                                        inary short workshop papers at NIPS 2017 (?) and ICLR           provide pseudo code for it in Algorithm 1. It is initialized
                                        2018 (?).                                                       with a set of configurations, a minimum and maximum
                                        While the general idea of combining Hyperband and               budget, and a scaling parameter η. In the first stage all
                                        Bayesian optimization by Bertrand et al. (2017) is the same     configurations are evaluated on the smallest budget (line
                                        as in our work, they use a Gaussian process for modeling        3). The losses are then sorted and only the best 1/η con-
                                        the performance. The budget is modeled like any other di-       figurations are kept in the set C (line 4). For the following
                                        mension of the search space, without any special treatment.     stage, the budget is increased by a factor of η (line 5). This
                                        Based on our experience with Fabolas (Klein et al., 2017),      is repeated until the maximum budget for a single configura-
                                        we expect that the squared exponential kernel might not         tion is reached (line 2). Within Hyperband, the budgets are
                                        extrapolate well, which would hinder performance. Also,         chosen such that all SuccessiveHalving executions require a
                                        the small evaluation provided by Bertrand et al. (2017) does    similar total budget.
                                        not allow strong conclusions about the performance of their
                                        method.                                                          Algorithm 1: Pseudocode for SuccessiveHalving used
                                                                                                         by Hyperband as a subroutine.
                                        Wang et al. (2018) also independently combined TPE and
                                        Hyperband, but in a slightly different way than we did. In         input :initial budget b0 , maximum budget bmax , set
                                        their method, TPE is used as a subroutine in every itera-                  of n configurations C = {c1 , c2 , . . . cn }
                                                                                                         1 b = b0
                                        tion of Hyperband. In particular, a new model is built from
                                                                                                         2 while b ≤ bmax do
                                        scratch at the beginning of every SuccessiveHalving run
                                        (Algorithm 3, line 8 in Wang et al. (2018)). This means          3    L = {f˜(c, b) : c ∈ C}
                                        that in later iterations of the algorithm, the model does not    4    C = topk (C, L, b|C|/η)c
                                                                                                         5    b=η·b
                                            1
                                              Department of Computer Science, University of Freiburg,
                                        Freiburg, Germany.     Correspondence to: Stefan Falkner
                                        <sfalkner@informatik.uni-freiburg.de>.

                                        Proceedings of the 35 th International Conference on Machine    D. Details on the Kernel Density Estimator
                                        Learning, Stockholm, Sweden, PMLR 80, 2018. Copyright 2018
                                        by the author(s).
                                                                                                        We used the MultivariateKDE from the statsmodels package
                                                                                                        (Seabold & Perktold, 2010), which constructs a factorized

                    Supplementary material for: BO-HB: Robust and Efficient Hyperparameter Optimization at Scale

                                           adult                                                                         higgs
               10−1                                                                   100
                                                           RS                                                                               RS
                                                           TPE                                                                              TPE
                                                           GP-BO                                                                            GP-BO
                                                           HB
                                                                                     10−1                                                   HB

      regret                                                                regret
                                                           HB-LCNet                                                                         HB-LCNet
               10−2
                                                           BOHB                                                                             BOHB
                                                                                     10−2

               10−3                                                                  10−3
                      101   102      103            104   105         106                      101         102         103         104     105         106
                                  wall clock time [s]                                                             wall clock time [s]
                                           letter                                                                        mnist
                100                                                                   100
                                                           RS                                                                               RS
                                                           TPE                                                                              TPE
                                                                                          −1
                                                           GP-BO                     10                                                     GP-BO
               10−1                                        HB                                                                               HB

      regret                                                                regret
                                                           HB-LCNet                                                                         HB-LCNet
                                                                                     10−2
                                                           BOHB                                                                             BOHB
               10−2                                                                       −3
                                                                                     10

               10−3 0                                                                10−4 0
                  10        101     102             103   104         105               10            101        102         103     104    105        106
                                  wall clock time [s]                                                             wall clock time [s]
                                      optdigits                                                                          poker
                100                                                                   100
                                                           RS
                                                           TPE
                                                           GP-BO                     10−1
               10−1                                        HB                                        RS

      regret                                                                regret
                                                           HB-LCNet
                                                                                     10−2            TPE
                                                           BOHB                                      GP-BO
                −2
               10                                                                     −3
                                                                                                     HB
                                                                                     10              HB-LCNet
                                                                                                     BOHB

               10−3                                                                  10−4
                      100   101      102            103   104         105                      102         103         104         105     106         107
                                  wall clock time [s]                                                             wall clock time [s]

Figure 1. Mean performance on the surrogates for all six datasets. As uncertainties, we show the standard error of the mean based on 512
runs (except for GP-BO, which has only 50 runs).

kernel, with a one-dimensional kernel for each dimension.                       the worst optimizer across all datasets when the budget is
Note that using this product of 1-d kernels differs from the                    large enough for GP-BO and TPE to leverage their model.
original TPE, which uses a pdf that is the product of 1-d                       Hyperband and the two methods based on it (HB-LCNet)
pdfs. For the continuous parameters a Gaussian kernel is                        and BOHB improve much more quickly due to the smaller
used, whereas the Aitchison-Aitken kernel is the default                        budgets used. On all surrogate benchmarks, BOHB starts
for categorical parameters. We used Scott’s rule for ef-                        to outperform HB after the first couple of iterations (someficient bandwidth estimation, as preliminary experiments                        times even earlier, e.g., on dataset letter). The same dataset
with maximum-likelihood based bandwidth selection did                           also shows that traditional BO methods can still have an
not yield better performance but caused a significant over-                     advantage for very large budgets, since in these late stages
head.                                                                           of the optimization process the low fidelity evaluations of
                                                                                BOHB can cause a constant overhead without any gain.
E. Performance of all methods on all
   surrogates                                                                   F. Performance of parallel runs
Figure 1 shows the performance of all methods we evaluated                      Figure 2 shows the performance of BOHB when run in
on all our surrogate benchmarks. Random search is clearly                       parallel on all our surrogate benchmarks. The speed-ups

                    Supplementary material for: BO-HB: Robust and Efficient Hyperparameter Optimization at Scale

                                           adult                                                                      higgs
               10−1                                                                  100
                                                            n=1                                                                             n=1
                                                            n=2                                                                             n=2
                                                            n=4                                                                             n=4
                                                            n=8                     10−1                                                    n=8

      regret   10−2                                                        regret
                                                            n = 16                                                                          n = 16
                                                            n = 32                                                                          n = 32
                                                                                    10−2

               10−3                                                                 10−3
                      101   102      103            104   105        106                   101      102           103         104      105           106
                                  wall clock time [s]                                                           wall clock time [s]
                                           letter                                                                     mnist
                100                                                                  100
                                                            n=1                                                                             n=1
                                                            n=2                                                                             n=2
                                                                                     −1
                −1                                          n=4                     10                                                      n=4
               10                                           n=8                                                                             n=8

      regret                                                               regret   10−2
                                                            n = 16                                                                          n = 16
                                                            n = 32                                                                          n = 32
               10−2                                                                  −3
                                                                                    10

               10−3 0                                                               10−4
                  10        101     102             103   104        105                   101      102           103         104      105           106
                                  wall clock time [s]                                                           wall clock time [s]
                                      optdigits                                                                       poker
                100                                                                  100
                                                            n=1
                                                            n=2
                                                            n=4                     10−1
               10−1                                         n=8

      regret                                                               regret   10−2
                                                            n = 16
                                                            n = 32
               10−2                                                                              n=1
                                                                                    10−3         n = 16
                                                                                                 n = 32

               10−3                                                                 10−4
                      100   101      102            103   104        105                   102            103           104           105            106
                                  wall clock time [s]                                                           wall clock time [s]

Figure 2. Mean performance on the surrogates for all six datasets with different numbers of workers n. As uncertainties, we show the
standard error of the mean based on 128 runs. Because we simulated them in real time to capture the true behavior, poker is too expensive
to evaluate with less than 16 workers within a day.

are quite consistent, and almost linear for a small number                     actually ran in real time. For this reason, we decided to not
of workers (2-8). For more workers, more random config-                        evaluate all possible numbers of workers for dataset poker,
urations are evaluated in parallel before the first model is                   for which each run with less than 16 workers would have
built, which degrades performance. But even for 32 work-                       taken more than a day, and we do not expect any different
ers, linear speedups are possible (see, e.g., dataset letter, for              behavior compared to the other datasets.
reaching a regret of 2 × 10−3 ).
We note that in order to carry out this evaluation of par-                     G. Evaluating the hyperparameters of BOHB
allel performance, we actually simulated the parallel opti-
                                                                               In this section, we evaluate the importance of the individual
mization by making each worker wait for the given budget
                                                                               hyperparameters of BOHB, namely the number of samples
before returning the corresponding performance value of
                                                                               used to optimize the acquisition function (Figure 3), the
our surrogate benchmark. (The case of one worker is an
                                                                               fraction of purely random configuration ρ (Figure 4), the
exception, where we can simply reconstruct the trajectory
                                                                               scaling parameter η (Figure 5), and the bandwidth factor
because all configurations are evaluated serially.) By using
                                                                               used to encourage exploration (Figure 6).
this approach in connection with threads, each evaluation
of a parallel algorithm still only used 1 CPU, but the run                     Additionally, we want to discuss the importance of η, bmin

              Supplementary material for: BO-HB: Robust and Efficient Hyperparameter Optimization at Scale

                    Figure 3. Performance on the surrogates for all six datasets for different number of samples

and bmax already present in HB. The parameter η controls            being optimized.
how aggressively SH cuts down the budget and the number of configurations evaluated. Like HB (Li et al., 2017),         H. Counting Ones
BOHB is also quite insensitive to this choice in a reasonable
range. For our experiments, we use the same default value           We now show results for the counting ones function for
(η = 3) for HB and BOHB.                                            different dimensions. Figure 7 shows the mean performance
                                                                    of all applicable methods in d = 8, 16, 32 and 64 dimensions
More important for the optimization are bmin and bmax ,
                                                                    for a budget of 8192 full function evaluations.
which are problem specific and inputs to both HB and
BOHB. While the maximum budget is often naturally de-               We draw the following conclusions from the results:
fined, or is constrained by compute resources, the situation
for the minimum budget is often different. To get substan-            1. Despite its simple definition, this problem is quite chaltial speedups, an evaluation with a budget of bmin should                lenging for the methods we applied to it. RS and HB
contain some information about the quality of a configura-               both suffer from the fact that drawing configurations
tion with larger budgets; for example, when subsampling                  at random performs quite poorly in this space. The
the data, the smallest subset should not be one datum, but               model-based approaches SMAC and TPE performed
rather enough points to fit a meaningful model. This re-                 substantially better, especially with large budgets. They
quires knowledge about the benchmark and the algorithm                   required a larger number of samples before converging

            Supplementary material for: BO-HB: Robust and Efficient Hyperparameter Optimization at Scale

                    Figure 4. Performance on the surrogates for all six datasets for different random fractions

   to the true optimum than BOHB. However, we would                      TPE’s univariate KDE to perform better than BOHB’s
   like to mention that SMAC and TPE treated the prob-                   multivariate one.
   lem as a blackbox optimization problem; the results for
   SMAC could likely be improved by treating individual             I. Surrogates
   samples as “instances” and using SMAC’s intensifi-
   cation mechanism to reject poor configurations based             I.1. Constructing the Surrogates
   on few samples and evaluate promising configurations
                                                                   To build a surrogate, we sampled 10 000 random configu-
   with more samples.
                                                                   rations for each dataset, trained them for 50 epochs, and
2. BOHB struggles in the very high dimensional case. We            recorded their classification error after each epoch, along
   attribute this to the fact that the noise is substantially      with their total training time. We fitted two independent ran-
   higher in this case, such that larger budgets are required      dom forests that predict these two quantities as a function of
   to build a good model. Therefore, given a large enough          the hyperparameter configuration used. This enabled us to
   budget, BOHB’s evaluations on small budgets lead to             predict the classification error as a function of time with suf-
   a constant overhead over only using the more reliable           ficient accuracy. As almost all networks converged within
   evaluations on larger budgets. Since the optimization           the 50 epochs, we extend the curves by the last obtained
   problem is perfectly separable (there are no interac-           value if the budget would allow for more epochs.
   tion effects between any dimensions), we also expect

             Supplementary material for: BO-HB: Robust and Efficient Hyperparameter Optimization at Scale

                       Figure 5. Performance on the surrogates for all six datasets for different values of η.

The surrogates enable cheap benchmarking, allowing us to                  ently noisy process, i.e. two evaluations of the same
run each algorithm 256 times. Since evaluating a configura-               configuration can result in different performances. This
tion with the random forest is inexpensive, we used a global              is not at all reflected by our surrogates, making them a
optimizer (differential evolution) to find the true optimum.              potentially easier to optimize than the true benchmark
We allowed the optimizer 10 000 iterations which should be                they are based on.
sufficient to find the true optimum.
                                                                      (d) By fixing the budgets (see below) and having determin-
Besides these positive aspects of benchmarking with sur-                  istic surrogates, the global minima might be the result
rogates, there are also some drawbacks that we want to                    of some small fluctuations in the classification error
mention explicitly:                                                       in the surrogates’ training data. That means that the
                                                                          surrogate’s minimizer might not be the true minimizer
 (a) There is no guarantee that the surrogate actually re-                of the real benchmark.
     flects the important properties of the true benchmark.
                                                                     None of these downsides necessarily have substantial im-
(b) The presented results show the optimized classification
                                                                     plications for comparing different optimizers; they simply
    error on the validation set used during training. There
                                                                     show that the surrogate benchmarks are not perfect models
    is no test performance that could indicate overfitting.
                                                                     for the real benchmark they mimic. Nevertheless, we be-
 (c) Training with stochastic gradient descent is an inher-          lieve that, especially for development of novel algorithms,

               Supplementary material for: BO-HB: Robust and Efficient Hyperparameter Optimization at Scale

                      Figure 6. Performance on the surrogates for all six datasets for different bandwidth factors.

Table 1. The hyperparameters and architecture choices for the fully
connected networks.                                                   Table 2. The budgets used by HB and BOHB; random search and
      Hyperparameter                 Range         Log-transform      TPE only used the last budget
                                                                            Dataset     Budgets in seconds for HB and BOHB
        batch size                  [23 , 28 ]            yes
                                                                            Adult                   9, 27, 81, 243
       dropout rate                 [0, 0.5]              no
                                                                            Higgs                   9, 27, 81, 243
   initial learning rate         [10−6 , 10−2 ]           yes
                                                                            Letter                   3, 9, 27, 81
 exponential decay factor         [−0.185, 0]             no
                                                                            Poker                81, 243, 729, 2187
     # hidden layers             {1, 2, 3, 4, 5}          no
     # units per layer              [24 , 28 ]            yes

the positive aspects outweigh the negative ones.
                                                                      training time. We chose the closest power of 3 (because
                                                                      we also used η = 3 for HB and BOHB) to achieve that
I.2. Determining the budgets
                                                                      performance. We chose the smallest budget for HB such
To choose the largest budget for training, we looked at               that most configurations had finished at least one epoch.
the best configuration as predicted by the surrogate and its          Table 2 lists the budgets used for all datasets.

              Supplementary material for: BO-HB: Robust and Efficient Hyperparameter Optimization at Scale

Figure 7. Mean performance of BOHB, HB, TPE, SMAC and RS on the mixed domain counting ones function with different dimensions.
As uncertainties, we show the standard error of the mean based on 512 runs.

Table 3. The hyperparameters for the Bayesian neural network         Table 4. The hyperparameters for the PPO Cartpole task.
task.                                                                 Hyperparameter               Range        Log-transform
   Hyperparameter            Range        Log-transform                 # units layer 1           [23 , 27 ]         yes
    # units layer 1         [24 , 29 ]         yes                      # units layer 2           [23 , 27 ]         yes
    # units layer 2         [24 , 29 ]         yes                         batch size             [23 , 28 ]         yes
      step length        [10−6 , 10−1 ]        yes                       learning rate         [10−7 , 10−1 ]        yes
        burn in              [0, .8]            no                          discount               [0, 1]             no
   momentum decay             [0, 1]            no                likelihood ratio clipping        [0, 1]             no
                                                                   entropy regularization          [0, 1]             no

                                                                 K. Reinforcement Learning

J. Bayesian Neural Networks                                      Table 4 shows the hyperparameters we optimized for the
                                                                 PPO Cartpole task.
We optimized the hyperparameters described in Table 3
for a Bayesian neural network trained with SGHMC on              References
two UCI regression datasets: Boston Housing and Protein
Structure. The budget for this benchmark was the number          Bertrand, H., Ardon, R., Perrot, M., and Bloch, I. Hyperof steps for the MCMC sampler. We set the minimum                  parameter optimization of deep neural networks: Combudget to 500 steps and the maximum budget to 10000                bining hyperband with Bayesian model selection. Prosteps. After sampling 100 parameter vectors, we computed           ceedings of Conférence sur l’Apprentissage Automatique
the log-likelihood on the validation dataset by averaging          (CAP 2017), 2017.
the predictive mean and variances of the individual models.
The performance of all methods for both datasets is shown        Klein, A., Falkner, S., Bartels, S., Hennig, P., and Hutter,
in Figure 8.                                                       F. Fast Bayesian optimization of machine learning hy-

                                    Supplementary material for: BO-HB: Robust and Efficient Hyperparameter Optimization at Scale

                                                 Boston Housing                                                             Protein
                                9                                                                            9

      negative log-likelihood                                                      negative log-likelihood
                                8                                                                            8
                                7                                                                            7
                                6                                                                            6
                                        RS                                                                          RS
                                5       TPE                                                                  5     TPE
                                        HB                                                                          HB
                                4                                                                            4
                                       BOHB                                                                        BOHB
                                3 4                                                                          3 4
                                10                   105                     106                             10             105        106
                                                   MCMC steps                                                             MCMC steps

Figure 8. Mean performance of TPE, RS, HB and BOHB for optimizing the 5 hyperparameters of a Bayesian neural network on two
different UCI datasets. As uncertainties, we show the stardard error of the mean based on 50 runs.

  perparameters on large datasets. In Proceedings of the
  Seventeenth International Conference on Artificial Intelli-
  gence and Statistics (AISTATS), 2017.
Li, L., Jamieson, K., DeSalvo, G., Rostamizadeh, A.,
  and Talwalkar, A. Hyperband: Bandit-based configu-
  ration evaluation for hyperparameter optimization. In
  Proceedings of the International Conference on Learn-
  ing Representations (ICLR’17), 2017. Published online:
  iclr.cc.
Seabold, S. and Perktold, J. Statsmodels: Econometric and
  statistical modeling with python. In 9th Python in Science
  Conference, 2010.
Wang, J., Xu, J., and Wang, X. Combination of hyperband
 and bayesian optimization for hyperparameter optimiza-
 tion in deep learning. arXiv preprint arxiv:1801.01596,
 01 2018.
