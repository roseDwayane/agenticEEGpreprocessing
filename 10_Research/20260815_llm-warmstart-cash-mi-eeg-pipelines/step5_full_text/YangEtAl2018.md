---
citation_key: "YangEtAl2018"
title: "OBOE: Collaborative Filtering for AutoML Model Selection"
authors: "Chengrun Yang; Yuji Akimoto; Dae Won Kim; Madeleine Udell"
year: 2018
doi: "10.1145/3292500.3330909"
source: "arXiv (1808.03233)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "1808.03233"
conversion: "pdftotext -layout (automated)"
---

# OBOE: Collaborative Filtering for AutoML Model Selection

Oboe: Collaborative Filtering for AutoML Model Selection
                                                                           Chengrun Yang, Yuji Akimoto, Dae Won Kim, Madeleine Udell
                                                                                                              Cornell University
                                                                                                    {cy438,ya242,dk444,udell}@cornell.edu

                                         ABSTRACT                                                                          Our optimization procedure ensures these latent meta-features best
                                         Algorithm selection and hyperparameter tuning remain two of the                   predict the cross-validated errors, among all bilinear models.
                                         most challenging tasks in machine learning. Automated machine                        To find promising models for a new dataset, Oboe chooses a set
                                         learning (AutoML) seeks to automate these tasks to enable wide-                   of fast but informative models to run on the new dataset and uses
                                         spread use of machine learning by non-experts. This paper intro-                  their cross-validated errors to infer the latent meta-features of the

arXiv:1808.03233v2 [cs.LG] 20 May 2019
                                         duces Oboe, a collaborative filtering method for time-constrained                 new dataset. Given more time, Oboe repeats this procedure using
                                         model selection and hyperparameter tuning. Oboe forms a ma-                       a higher rank to find higher-dimensional (and more expressive)
                                         trix of the cross-validated errors of a large number of supervised                latent features. Using a low rank model for the error matrix is a
                                         learning models (algorithms together with hyperparameters) on                     very strong structural prior.
                                         a large number of datasets, and fits a low rank model to learn the                   This system addresses two important problems: 1) Time-constrained
                                         low-dimensional feature vectors for the models and datasets that                  initialization: how to choose a promising initial model under time
                                         best predict the cross-validated errors. To find promising models for             constraints. Oboe adapts easily to short times by using a very low
                                         a new dataset, Oboe runs a set of fast but informative algorithms                 rank and by restricting its experiments to models that will run
                                         on the new dataset and uses their cross-validated errors to infer                 very fast on the new dataset. 2) Active learning: how to improve on
                                         the feature vector for the new dataset. Oboe can find good models                 the initial guess given further computational resources. Oboe uses
                                         under constraints on the number of models fit or the total time                   extra time by allowing higher ranks and more expensive computa-
                                         budget. To this end, this paper develops a new heuristic for active               tional experiments, accumulating its knowledge of the new dataset
                                         learning in time-constrained matrix completion based on optimal                   to produce more accurate (and higher-dimensional) estimates of its
                                         experiment design. Our experiments demonstrate that Oboe deliv-                   latent meta-features.
                                         ers state-of-the-art performance faster than competing approaches                    Oboe uses collaborative filtering for AutoML, selecting models
                                         on a test bed of supervised learning problems. Moreover, the suc-                 that have worked well on similar datasets, as have many previous
                                         cess of the bilinear model used by Oboe suggests that AutoML may                  methods including [1, 9, 12, 28, 38, 45]. In collaborative filtering,
                                         be simpler than was previously understood.                                        the critical question is how to characterize dataset similarity so
                                                                                                                           that training datasets “similar” to the test dataset faithfully predict
                                         KEYWORDS                                                                          model performance. One line of work uses dataset meta-features —
                                                                                                                           simple, statistical or landmarking metrics — to characterize datasets
                                         AutoML, meta-learning, time-constrained, model selection, collabo-
                                                                                                                           [9, 12–14, 31]. Other approaches (e.g., [43]) avoid meta-features.
                                         rative filtering
                                                                                                                           Our approach builds on both of these lines of work. Oboe relies
                                                                                                                           on model performance to characterize datasets, and the low rank
                                         1    INTRODUCTION                                                                 representations it learns for each dataset may be seen (and used) as
                                         It is often difficult to find the best algorithm and hyperparameter               latent meta-features. Compared to AutoML systems that compute
                                         settings for a new dataset, even for experts in machine learning                  meta-features of the dataset before running any models, the flow
                                         or data science. The large number of machine learning algorithms                  of information in Oboe is exactly opposite: Oboe uses only the
                                         and their sensitivity to hyperparameter values make it practically                performance of various models on the datasets to compute lower
                                         infeasible to enumerate all configurations. Automated machine                     dimensional latent meta-features for models and datasets.
                                         learning (AutoML) seeks to efficiently automate the selection of                     The active learning subproblem is to gain the most informa-
                                         model (e.g., [8, 12, 14]) or pipeline (e.g., [11]) configurations, and            tion to guide further model selection. Some approaches choose a
                                         has become more important as the number of machine learning                       function class to capture the dependence of model performance
                                         applications increases.                                                           on hyperparameters; examples are Gaussian processes [3, 14, 17,
                                             We propose an algorithmic system, Oboe 1 , that provides an                   27, 33, 34, 36, 37], sparse Boolean functions [16] and decision trees
                                         initial tuning for AutoML: it selects a good algorithm and hyperpa-               [2, 20]. Oboe chooses the set of bilinear models as its function class:
                                         rameter combination from a discrete set of options. The resulting                 predicted performance is linear in each of the latent model and
                                         model can be used directly, or the hyperparameters can be tuned                   dataset meta-features.
                                         further. Briefly, Oboe operates as follows.                                          Bilinearity seems like a rather strong assumption, but confers
                                             During an offline training phase, it forms a matrix of the cross-             several advantages. Computations are fast and easy: we can find
                                         validated errors of a large number of supervised-learning models                  the global minimizer by PCA, and can infer the latent meta-features
                                         (algorithms together with hyperparameters) on a large number                      for a new dataset using least squares. Moreover, recent theoretical
                                         of datasets. It then fits a low rank model to this matrix to learn                work suggests that this model class is more general than it appears:
                                         latent low-dimensional meta-features for the models and datasets.                 roughly, and under a few mild technical assumptions, any m × n
                                         1 The eponymous musical instrument plays the initial note to tune an orchestra.

                                                  Training       Training   Training
                                                                                            index over models. We define [n] = {1, . . . , n} for n ∈ Z. Given an
                                                                                            ordered set S = {s 1 , . . . , sk } where
                                                                                                                                    s 1 < . . . < sk ∈ [n], we write
                                     Learning
                    Training
                                                                                            A:S = A:,s1 A:,s2 · · · A:,sk .
    Learning
                                                  Validation    Validation Validation

                                                     Test          Test       Test          Algorithm performance. A model A is a specific algorithm-
                   Validation                                     Meta­    Meta­
                                                Meta­training
                                                                validation test             hyperparameter combination, e.g. k-NN with k = 3. We denote by
                                                                                            A(D) the expected cross-validation error of model A on dataset D,
                     Test                               Meta­learning
                                                                                            where the expectation is with respect to the cross-validation splits.
               (a) Learning                        (b) Meta-learning
                                                                                            We refer to the model in our collection that achieves minimal error
                                                                                            on D as the best model for D. A model A is said to be observed
                                                                                            on D if we have calculated A(D) by fitting (and cross-validating)
                 Figure 1: Standard vs meta-learning.
                                                                                            the model. The performance vector e of a dataset D concatenates
                                                                                            A(D) for each model A in our collection.
                                                                                            Meta-features. We discuss two types of meta-features in this pamatrix with independent rows and columns whose entries are gen-                             per. Meta-features refer to metrics used to characterize datasets or
erated according to a fixed function (here, the function computed                           models. For example, the number of data points or the performance
by training the model on the dataset) has an approximate rank that                          of simple models on a dataset can serve as meta-features of the
grows as log(m + n) [40]. Hence large data matrices tend to look                            dataset. As an example, we list the meta-features used in the Aulow rank.                                                                                   toML framework auto-sklearn in Appendix B, Table 3. In constrast
   Originally, the authors conceived of Oboe as a system to produce                         to standard meta-features, we use the term latent meta-features to
a good set of initial models, to be refined by other local search meth-                     refer to characterizations learned from matrix factorization.
ods, such as Bayesian optimization. However, in our experiments,                            Parametric hierarchy. We distinguish between three kinds of
we find that Oboe’s performance, refined by fitting models of ever                          parameters:
higher rank with ever more data, actually improves faster than                                   • Parameters of a model (e.g., the splits in a decision tree) are
competing methods that use local search methods more heavily.                                      obtained by training the model.
   One key component of our system is the prediction of model                                    • Hyperparameters of an algorithm (e.g., the maximum depth
runtime on new datasets. Many authors have previously studied                                      of a decision tree) govern the training procedure. We use
algorithm runtime prediction using a variety dataset features [21],                                the word model to refer to an algorithm together with a
via ridge regression [18], neural networks [35], Gaussian processes                                particular choice of hyperparameters.
[19], and more. Several measures have been proposed to trade-                                    • Hyper-hyperparameters of a meta-learning method (e.g., the
off between accuracy and runtime [4, 25]. We predict algorithm                                     total time budget for Oboe) govern meta-training.
runtime using only the number of samples and features in the                                Time target and time budget. The time target refers to the andataset. This model is particularly simple but surprisingly effective.                      ticipated time spent running models to infer latent features of each
   Classical experiment design (ED) [5, 22, 29, 32, 42] selects fea-                        fixed dimension and can be exceeded. However, the runtime does
tures to observe to minimize the variance of the parameter esti-                            not usually deviate much from the target since our model runtime
mate, assuming that features depend on the parameters according                             prediction works well. The time budget refers to the total time limit
to known, linear, functions. Oboe’s bilinear model fits this para-                          for Oboe and is never exceeded.
digm, and so ED can be used to select informative models. Budget                            Midsize OpenML and UCI datasets. Our experiments use OpenML
constraints can be added, as we do here, to select a small number                           [41] and UCI [10] classification datasets with between 150 and
of promising machine learning models or a set predicted to finish                           10,000 data points and with no missing entries.
within a short time budget [24, 46].
   This paper is organized as follows. Section 2 introduces notation
and terminology. Section 3 describes the main ideas we use in Oboe.                         3 METHODOLOGY
Section 4 presents Oboe in detail. Section 5 shows experiments.                             3.1 Model Performance Prediction
                                                                                            It can be difficult to determine a priori which meta-features to use
2     NOTATION AND TERMINOLOGY                                                              so that algorithms perform similarly well on datasets with similar
Meta-learning. Meta-learning is the process of learning across                              meta-features. Also, the computation of meta-features can be exindividual datasets or problems, which are subsystems on which                              pensive (see Appendix C, Figure 11). To infer model performance
standard learning is performed [26]. Just as standard machine learn-                        on a dataset without any expensive meta-feature calculations, we
ing must avoid overfitting, experiments testing AutoML systems                              use collaborative filtering to infer latent meta-features for datasets.
must avoid meta-overfitting! We divide our set of datasets into                                 As shown in Figure 2, we construct an empirical error matrix
meta-training, meta-validation and meta-test sets, and report re-                           E ∈ Rm×n , where every entry Ei j records the cross-validated ersults on the meta-test set. Each of the three phases in meta-learning                       ror of model j on dataset i. Empirically, E has approximately low
— meta-training, meta-validation and meta-test — is a standard                              rank: Figure 3 shows the singular values σi (E) decay rapidly as a
learning process that includes training, validation and test.                               function of the index i. This observation serves as foundation of
Indexing. Throughout this paper, all vectors are column vectors.                            our algorithm, and will be analyzed in greater detail in Section 5.2.
Given a matrix A ∈ Rm×n , Ai,: and A:, j denote the ith row and jth                         The value Ei j provides a noisy but unbiased estimate of the true
column of A, respectively. i is the index over datasets, and j is the                       performance of a model on the dataset: EEi j = A j (Di ).
                                                                                        2

                models                    dataset latent meta­features
                                                                                                                        we fit an independent polynomial regression model for each model:

                                                                         model latent meta­features
                                                                                                           models
                                    PCA                                                                                                           M                                              2
                                                                                                                                                  Õ
                                                           XT                                                               f j = argminf j ∈ F         f j (n Di , p Di , log(n Di )) − t jDi        , j ∈ [n]
   datasets                                     datasets
                E                                                                                          Y
                                                                                                                                                  i=1
                                 impute
                              (white entries)
                                                                                                                        where t jD is the runtime of machine learning model j on dataset
                                                                                                                        D, and F is the set of all polynomials of order no more than 3. We
Figure 2: Illustration of model performance prediction via                                                              denote this procedure by f j = fit_runtime(n, p, t).
the error matrix E (yellow blocks only). Perform PCA on                                                                   We observe that this model predicts runtime within a factor of
the error matrix (offline) to compute dataset (X ) and model                                                            two for half of the machine learning models on more than 75%
(Y ) latent meta-features (orange blocks). Given a new dataset                                                          midsize OpenML datasets, and within a factor of four for nearly all
(row with white and blue blocks), pick a subset of models to                                                            models, as shown in Section 5.2 and visualized in Figure 7.
observe (blue blocks). Use Y together with the observed models to impute the performance of the unobserved models on                                                               3.3      Time-Constrained Information Gathering
the new dataset (white blocks).                                                                                         To select a subset S of models to observe, we adopt an approach
                                                                                                                        that builds on classical experiment design: we suppose fitting each
                                                                                                                        machine learning model j ∈ [n] returns a linear measurement x T y j
                              105                                                                                       of x, corrupted by Gaussian noise. To estimate x, we would like
                              104                                                                                       to choose a set of observations y j that span Rk and form a well-
                              103                                                                                       conditioned submatrix, but that corresponds to models which are
                         σi   102                                                                                       fast to run. In passing, we note that the pivoted QR algorithm on the
                              101                                                                                       matrix Y (heuristically) finds a well conditioned set of k columns of
                              100                                                                                       Y . However, we would like to find a method that is runtime-aware.
                                                                                                                            Our experiment design (ED) procedure minimizes a scalarization
                                    0        10            20 30         40                           50
                                                           index i                                                      of the covariance of the estimated meta-features x̂ of the new dataset
                                                                                                                        subject to runtime constraints [5, 22, 29, 32, 42]. Formally, define
Figure 3: Singular value decay of an error matrix. The entries                                                          an indicator vector v ∈ {0, 1}n , where entry v j indicates whether
are calculated by 5-fold cross validation of machine models                                                             to fit model j. Let tˆj denote the predicted runtime of model j on
(listed in Appendix A, Table 2) on midsize OpenML datasets.                                                             a meta-test dataset, and let y j denote its latent meta-features, for
                                                                                                                        j ∈ [n]. Now relax to allow v ∈ [0, 1]n to allow for non-Boolean
                                                                                                                        values and solve the optimization problem
                                                                                                                                                                Í            −1
                                                                                                                                         minimize log det          n v y y⊤
    To denoise this estimate, we approximate Ei j ≈ x i⊤y j where                                                                                                  j=1 j j j
                                                 2                                                                                                    n
x i and y j minimize m                                                                                                                                                                     (1)
                      Í Ín
                        i=1 j=1 (Ei j − x i y j ) with x i , y j ∈ R for
                                           ⊤                        k
                                                                                                                                         subject to      v j tˆj ≤ τ
                                                                                                                                                      Í
i ∈ [M] and j ∈ [N ]; the solution is given by PCA. Thus x i and y j are                                                                                   j=1
the latent meta-features of dataset i and model j, respectively. The                                                                                       v j ∈ [0, 1], ∀j ∈ [n]
rank k controls model fidelity: small ks give coarse approximations,                                                    with variable v ∈ Rn . We call this method ED (time). Scalarizing
while large ks may overfit. We use a doubling scheme to choose k                                                        the covariance by minimizing the determinant is called D-optimal
within time budget; see Section 4.2 for details.                                                                        design. Several other scalarizations can also be used, including
    Given a new meta-test dataset, we choose a subset S ⊆ [N ] of                                                       covariance norm (E-optimal) or trace (A-optimal). Replacing ti by 1
models and observe performance e j of model j for each j ∈ S. A                                                         gives an alternative heuristic that bounds the number of models fit
good choice of S balances information gain against time needed to                                                       by τ ; we call this method ED (number).
run the models; we discuss how to choose S in Section 3.3. We then                                                         Problem 1 is a convex optimization problem, and we obtain an
infer latent meta-features for the new dataset by solving the least                                                     approximate solution by rounding the largest entries of v up to
squares problem: minimize j ∈S (e j − x̂ ⊤y j )2 with x̂ ∈ Rk . For all
                              Í
                                                                                                                        1 until the selected models exceed the time limit τ . Let S ⊆ [n]
unobserved models, we predict their performance as ê j = x̂ ⊤y j for                                                   be the set of indices of e that we choose to observe, i.e. the set
j < S.                                                                                                                  such that vs rounds to 1 for s ∈ S. We denote this process by
                                                                                                                        S = min_variance_ED(tˆ, {y j }nj=1 , τ ).
3.2           Runtime Prediction
Estimating model runtime allows us to trade off between running                                                         4     THE OBOE SYSTEM
slow, informative models and fast, less informative models. We use                                                      Shown in Figure 4, the Oboe system can be divided into offline and
a simple method to estimate runtimes, using polynomial regression                                                       online stages. The offline stage is executed only once and explores
on n D and p D , the numbers of data points and features in D,                                                          the space of model performance on meta-training datasets. Time
and their logarithms, since the theoretical complexities of machine                                                     taken on this stage does not affect the runtime of Oboe on a new
learning algorithms we use are O (n D )3 , (p D )3 , (log(n D ))3 . Hence                                               dataset; the runtime experienced by user is that of the online stage.
                                                                 

                                                                                                                    3

                                                        offline stage
                                                                                compute low
                                       data              error matrix           dimensional
         training datasets
                                  preprocessing           generation             algorithm
                                                                                  features

                                                                 time­constrained online stage
                                                                                 infer
                                       data           time­constrained
            test dataset                                                    performance of            ensembling              predictions
                                  preprocessing        model selection
                                                                             other models

                                                                                                         time   No
                                                                                                       remains?

                                                                                                       Yes
                                                                              time target doubling

                                     Figure 4: Diagram of data processing flow in the Oboe system.

   One advantage of Oboe is that the vast majority of the time                 4.2    Online Stage
in the online phase is spent training standard machine learning                Recall that we repeatly double the time target of each round until we
models, while very little time is required to decide which models to           use up the total time budget. Thus each round is a subroutine of the
sample. Training these standard machine learning models requires               entire online stage and is shown as Algorithm 2, fit_one_round.
running algorithms on datasets with thousands of data points and
features, while the meta-learning task — deciding which models to
sample — requires only solving a small least-squares problem.                  • Time-constrained model selection (fit_one_round) Our ac-
                                                                                 tive learning procedure selects a fast and informative collection
4.1    Offline Stage                                                             of models to run on the meta-test dataset. Oboe uses the results
The (i, j)th entry of error matrix E ∈ Rm×n , denoted as Ei j , records          of these fits to estimate the performance of all other models as
the performance of the jth model on the ith meta-training dataset.               accurately as possible. The procedure is as follows. First pre-
We generate the error matrix using the balanced error rate met-                  dict model runtime on the meta-test dataset using fitted runtime
ric, the average of false positive and false negative rates across               predictors. Then use experiment design to select a subset S of
different classes. At the same time we record runtime of machine                 entries of e, the performance vector of the test dataset, to observe.
learning models on datasets. This is used to fit runtime predictors              The observed entries are used to compute x̂, an estimate of the
described in Section 3. Pseudocode for the offline stage is shown as             latent meta-features of the test dataset, which in turn is used to
Algorithm 1.                                                                     predict every entry of e. We build an ensemble out of models
                                                                                 predicted to perform well within the time target τ̃ by means of
                                                                                 greedy forward selection [6, 7]. We denote this subroutine as
Algorithm 1 Offline Stage                                                        Ã =ensemble_selection(S, e S , z S ), which takes as input the
Require: meta-training datasets {Di }i=1   m , models {A }n , algo-
                                                          j j=1                  set of base learners S with their cross-validation errors e S and
    rithm performance metric M                                                   predicted labels z S = {zs |s ∈ S}, and outputs ensemble learner
Ensure: error matrix E, runtime matrixT , fitted runtime predictors              Ã. The hyperparameters used by models in the ensemble can
    { f j }nj=1                                                                  be tuned further, but in our experiments we did not observe
 1: for i = 1, 2, . . . , m do                                                   substantial improvements from further hyperparameter tuning.
 2:       n Di , p Di ← number of data points and features in Di               • Time target doubling To select rank k, Oboe starts with a small
 3:       for j = 1, 2, . . . , n do                                             initial rank along with a small time target, and then doubles the
 4:             Ei j ← error of model A j on dataset Di according to             time target for fit_one_round until the elapsed time reaches
    metric M                                                                     half of the total budget. The rank k increments by 1 if the valida-
 5:            Ti j ← observed runtime for model A j on dataset Di               tion error of the ensemble learner decreases after doubling the
 6:       end for                                                                time target, and otherwise does not change. Since the matrices
 7: end for                                                                      returned by PCA with rank k are submatrices of those returned
 8: for j = 1, 2, . . . , n do                                                   by PCA with rank l for l > k, we can compute the factors as
 9:       fit f j = fit_runtime(n, p,T j )                                       submatrices of the m-by-n matrices returned by PCA with full
10: end for                                                                      rank min(m, n) [15]. The pseudocode is shown as Algorithm 3.

                                                                          4

Algorithm 2 fit_one_round({y j }nj=1 , { f j }nj=1 , Dt r , τ̃ )                 pre-process all datasets in the same way: one-hot encode categor-
                                                                                 ical features and then standardize all features to have zero mean
Require: model latent meta-features {y j }nj=1 , fitted runtime pre-
                                                                                 and unit variance. These pre-processed datasets are used in all the
   dictors { f j }nj=1 , training fold of the meta-test dataset Dtr , num-       experiments.
    ber of best models N to select from the estimated performance
    vector, time target for this round τ̃                                        5.1    Performance Comparison across AutoML
Ensure: ensemble learner Ã
                                                                                        Systems
 1: for j = 1, 2, . . . , n do
 2:     tˆj ← f j (n Dtr , p Dtr )                                               We compare AutoML systems that are able to select among different
 3: end for                                                                      algorithm types under time constraints: Oboe (with error matrix
 4: S = min_variance_ED(tˆ, {y j }n                                              generated from midsize OpenML datasets), auto-sklearn [12], prob-
                                          j=1 , τ̃ )
 5: for k = 1, 2, . . . , |S| do
                                                                                 abilistic matrix factorization (PMF) [14], and a time-constrained
 6:     e Sk ← cross-validation error of model A Sk on Dtr                       random baseline. The time-constrained random baseline selects
 7: end for
                                                                                 models to observe randomly from those predicted to take less time
             h                            i⊤                                     than the remaining time budget until the time limit is reached.
 8: x̂ ← ( y S1 y S2          · · · y S|S| )†e S
 9: ê ← y 1 y 2
           
                         · · · yn x̂
                                    ⊤                                           5.1.1 Comparison with PMF. PMF and Oboe differ in the surrogate
10: T ← the N models with lowest predicted errors in ê
                                                                                 models they use to explore the model space: PMF incrementally
11: for k = 1, 2, . . . , |T | do
                                                                                 picks models to observe using Bayesian optimization, with model
12:     e Tk , z Tk ← cross-validation error of model A Tk on Dtr                latent meta-features from probabilistic matrix factorization as fea-
13: end for
                                                                                 tures, while Oboe models algorithm performance as bilinear in
14: Ã ←ensemble_selection(T , e T , z T )
                                                                                 model and dataset meta-features.
                                                                                    PMF does not limit runtime, hence we compare it to Oboe us-
                                                                                 ing either QR or ED (number) to decide the set S of models (see
                                                                                 Section 3.3). Figure 5 compares the performance of PMF and Oboe
Algorithm 3 Online Stage
                                                                                 (using QR and ED (number) to decide the set S of models) on our
Require: error matrix E, runtime matrix T , meta-test dataset D,                 collected error matrix to see which is best able to predict the small-
    total time budget τ , fitted runtime predictors { f j }nj=1 , initial        est entry in each row. We show the regret: the difference between
    time target τ̃0 , initial approximate rank k 0                               the minimal entry in each row and the one found by the AutoML
Ensure: ensemble learner Ã                                                      method. In PMF, N 0 = 5 models are chosen from the best algorithms
 1: x i , y j ← arg min m
                         Í Ín                       2         min(m,n) for
                           i=1 j=1 (Ei j − x i y j ) , x i ∈ R
                                             ⊤
                                                                                 on similar datasets (according to dataset meta-features shown in
       i ∈ [M] , y j ∈ Rmin(m,n) for j ∈ [N ]                                    Appendix B, Table 3) are used to warm-start Bayesian optimization,
    2: Dtr , Dval , Dte ← training, validation and test folds of D               which then searches for the next model to observe. Oboe does not
    3: τ̃ ← τ̃0                                                                  require this initial information before beginning its exploration.
    4: k ← k 0                                                                   However, for a fair comparison, we show both "warm" and "cold"
    5: while τ̃ ≤ τ /2 do                                                        versions. The warm version observes both the models chosen by
    6:     {ỹ j }nj=1 ← k-dimensional subvectors of {y j }nj=1                  meta-features and those chosen by QR or ED; the number of ob-
                                                                                 served entries in Figure 5 is the sum of all observed models. The
    7:    Ã ← fit_one_round({ỹ j }nj=1 , { f j }nj=1 , Dtr , τ̃ )
                                                                                 cold version starts from scratch and only observes models chosen
    8: e ′ ← Ã(Dval )                                                           by QR and ED.
         Ã
 9:    if e ′ < e Ã then                                                           (Standard ED also performs well; see Appendix D, Figure 12.)
            Ã
10:         k ←k +1                                                                 Figure 5 shows the surprising effectiveness of the low rank model
11:    end if                                                                    used by Oboe:
12:    τ̃ ← 2τ̃                                                                  1 Meta-features are of marginal value in choosing new models to
13:    e Ã ← e ′                                                                observe. For QR, using models chosen by meta-features helps when
                Ã
14: end while                                                                    the number of observed entries is small. For ED, there is no benefit
                                                                                 to using models chosen by meta-features.
                                                                                 2 The low rank structure used by QR and ED seems to provide a
5        EXPERIMENTAL EVALUATION                                                 better guide to which models will be informative than the Gaussian
                                                                                 process prior used by PMF: the regret of PMF does not decrease as
We ran all experiments on a server with 128 Intel® Xeon® E7-4850                 fast as Oboe using either QR or ED.
v4 2.10GHz CPU cores. The process of running each system on
a specific dataset is limited to a single CPU core. Code for the                 5.1.2 Comparison with auto-sklearn. The comparison with PMF
Oboe system is at https://github.com/udellgroup/oboe; code for                   assumes we can use the labels for every point in the entire dataset
experiments is at https://github.com/udellgroup/oboe-testing.                    for model selection, so we can compare the performance of every
   We test different AutoML systems on midsize OpenML and UCI                    model selected and pick the one with lowest error. In contrast, our
datasets, using standard machine learning models shown in Ap-                    comparison with auto-sklearn takes place in a more challenging, rependix A, Table 2. Since data pre-processing is not our focus, we                alistic setting: when doing cross-validation on the meta-test dataset,
                                                                             5

               3.5

average rank
               3.0

               2.5

                                                                                rank (mean ± standard error)
                                                                                                  4.0                                                                0.5                                                           0.5

                                                                                                                                               balanced error rate                                           balanced error rate
                  regret (mean ± standard error)
                                      0.035
                                      0.030                                                                                                                          0.4                                                           0.4
                                                                                                  3.5
               2.0 0.025                                                                          3.0
                                                                                                                                                                     0.3                                                           0.3
                                      0.020                                                                                                                          0.2                                                           0.2
                                      0.015                                                       2.5                                                                0.1                                                           0.1
                                      0.010
               1.5 0.005                                        2.0                                                                                                         1.0 2.0 4.0 8.0 16.0 32.0 64.0
                                                                                                                                                                                                                                   0.0
                                                                                                                                                                                                                                               1.0 2.0 4.0 8.0 16.0 32.0 64.0
                  5(2%)
                   0.000
                                      15(6%)                  25(11%)
                                                                1.5
                                                                                 35(15%)                                                                                           runtime budget (s)                                                 runtime budget (s)
                       5(2%) 15(6%) 25(11%) 35(15%)                 5(2%) 15(6%) 25(11%) 35(15%)
                        number         (percentage)
                       number (percentage) of observed entries  ofnumber (percentage)entries
                                                                     observed         of observed entries                                                            (a) OpenML (meta-LOOCV)                                                    (b) UCI (meta-test)

                                                                                                                                             rank (mean ± standard error)                                       rank (mean ± standard error)
                                                    ED (number)                                                QR
                                                    ED (number) with meta-features                             QR with meta-features                                                                                               2.6
                                                                                                                                                                 2.2
                                                    PMF                                                                                                                                                                            2.4
                                                                                                                                                                 2.1                                                               2.2
                                                                                                                                                                 2.0                                                               2.0
               Figure 5: Comparison of sampling schemes (QR or ED) in                                                                                            1.9                                                               1.8
                                                                                                                                                                                                                                   1.6
               Oboe and PMF. "QR" denotes QR decomposition with col-                                                                                             1.8                                                               1.4
               umn pivoting; "ED (number)" denotes experiment design                                                                                                        1.0 2.0 4.0 8.0 16.0 32.0 64.0                                     1.0 2.0 4.0 8.0 16.0 32.0 64.0
                                                                                                                                                                                   runtime budget (s)                                                 runtime budget (s)
               with number of observed entries constrained. The left plot
               shows the regret of each AutoML method as a function of                                                                                       (c) OpenML (meta-LOOCV)                                                            (d) UCI (meta-test)
               number of entries; the right shows the relative rank of each
               AutoML method in the regret plot (1 is best and 5 is worst).                                                                Figure 6: Comparison of AutoML systems in a time-
                                                                                                                                           constrained setting, including Oboe with experiment design
                                                                                                                                           (red), auto-sklearn (blue), and Oboe with time-constrained
               we do not know the labels of the validation fold until we evaluate                                                          random initializations (green). OpenML and UCI denote
               performance of the ensemble we built within time constraints on                                                             midsize OpenML and UCI datasets. "meta-LOOCV" denotes
               the training fold.                                                                                                          leave-one-out cross-validation across datasets. In 6a and 6b,
                  Figure 6 shows the error rate and ranking of each AutoML                                                                 solid lines represent medians; shaded areas with correspond-
               method as the runtime repeatedly doubles. Again, Oboe’s simple                                                              ing colors represent the regions between 75th and 25th per-
               bilinear model performs surprisingly well’2 :                                                                               centiles. Until the first time the system can produce a model,
                                                                                                                                           we classify every data point with the most common class la-
               1 Oboe on average performs as well as or better than auto-sklearn                                                           bel. Figures 6c and 6d show system rankings (1 is best and 3
               (Figures 6c and 6d).                                                                                                        is worst).
               2 The quality of the initial models computed by Oboe and by auto-
               sklearn are comparable, but Oboe computes its first nontrivial
               model more than 8× faster than auto-sklearn (Figures 6a and 6b).                                                            Low rank under different metrics. Oboe uses balanced error
               In contrast, auto-sklearn must first compute meta-features for each                                                         rate to construct the error matrix, and works on the premise that
               dataset, which requires substantial computational time, as shown                                                            the error matrix can be approximated by a low rank matrix. How-
               in Appendix C, Figure 11.                                                                                                   ever, there is nothing special about the balanced error rate metric:
               3 Interestingly, the rate at which the Oboe models improves with                                                            most metrics result in an approximately low rank error matrix.
               time is also faster than that of auto-sklearn: the improvement Oboe                                                         For example, when using the AUC metric to measure error, the
               makes before 16s matches that of auto-sklearn from 16s to 64s. This                                                         418-by-219 error matrix from midsize OpenML datasets has only 38
               indicates that the large time budget may be better spent in fitting                                                         eigenvalues greater than 1% of the largest, and 12 greater than 3%.
               more models than optimizing over hyperparameters, to which auto-                                                            (Nonnegative) low rank structure of the error matrix. The
               sklearn devotes the remaining time.                                                                                         features computed by PCA are dense and in general difficult to
               4 Experiment design leads to better results than random selection                                                           interpret. In contrast, nonnegative matrix factorization (NMF) pro-
               in almost all cases.                                                                                                        duces sparse positive feature vectors and is thus widely used for
                                                                                                                                           clustering and interpretability [23, 39, 44]. We perform NMF on
               5.2                                 Why does Oboe Work?                                                                     the error matrix E to find nonnegative factors W ∈ Rm×k and
               Oboe performs well in comparison with other AutoML methods                                                                  H ∈ Rk ×n so that E ≈ W H . Cluster membership of each model is
               despite making a rather strong assumption about the structure                                                               given by the largest entry in its corresponding column in H .
               of model performance across datasets: namely, bilinearity. It also                                                             Figure 8 shows the heatmap of algorithms in clusters when
               requires effective predictions for model runtime. In this section, we                                                       k = 12 (the number of singular values no smaller than 3% of the
               perform additional experiments on components of the Oboe system                                                             largest one). Algorithm types are sparse in clusters: each cluster
               to elucidate why the method works, whether our assumptions are                                                              contains at most 3 types of algorithm. Also, models belonging to the
               warranted, and how they depend on detailed modeling choices.                                                                same kinds of algorithms tend to aggregate into the same clusters:
                                                                                                                                           for example, Clusters 1 and 4 mainly consist of tree-based models;
               2 Auto-sklearn’s GitHub Issue #537 says “Do not start auto-sklearn for time limits less
                                                                                                                                           Cluster 10 of linear models; and Cluster 12 of neighborhood models.
               than 60s". These plots should not be taken as criticisms of auto-sklearn, but are used                                      Runtime prediction performance. Runtimes of linear models
               to demonstrate Oboe’s ability to select a model within a short time.                                                        are among the most difficult to predict, since they depend strongly
                                                                                                                                       6

                                                      7

Figure 7: Runtime prediction performance on different machine learning algorithms, on midsize OpenML datasets.

                                                                                  accuracy percentage
                                                                                                                                               40%
                          1                                    30                                35%
                                                                                                 30%                                           30%
                          2                                                                      25%
                          3                                    25                                                                      ratio
                                                                                (mean ± standard error)
                                                                                                 20%                                           20%
                          4                                                                      15%

         cluster index
                                                                                                                                               10%
                          5                                    20                                10%
                                                                                                  5%
                          6                                                                       0%
                                                                                                                                               0%
                                                                                                                                                     0      5      10   15     20    25
                          7                                    15                                          2   4     6     8     10                      number of models in ensemble
                                                                                                          number of best entries
                          8
                          9                                    10                                           D-optimal
                                                                                                            A-optimal
                                                                                                                           E-optimal
                                                                                                                           Alors       Figure 10: Histogram of
                         10
                         11                                    5                                                                       Oboe ensemble size. The
                         12                                                     Figure 9: Comparison of                                ensembles were built in exe-
                                                               0                cold-start methods.                                    cutions on midsize OpenML
                                      Decision Tree
                                        Extra Trees                                                                                    datasets in Section 5.1.2.
                                    Random Forest
                                 Gradient Boosting
                              Gaussian Naive Bayes
                                          Adaboost                              larger than 1% of the largest, which is 38 here. The time limit
                                       Kernel SVM
                                       Linear SVM                               in experiment design implementation is set to be 4 seconds; the
                                Logistic Regression                             nonlinear regressor used in Alors implementation is the default
                                         Perceptron
                              Multilayer Perceptron                             RandomForestRegressor in scikit-learn 0.19.2 [30].
                                               kNN                                 The horizontal axis is the number of models selected; the verti-
                                                                                cal axis is the percentage of best-ranked models shared between
Figure 8: Algorithm heatmap in clusters. Each block is col-                     true and predicted performance vectors. D-optimal design robustly
ored by the number of models of the corresponding algo-                         outperforms.
rithm type in that cluster. Numbers next to the scale bar re-                   Ensemble size. As shown in Figure 10, more than 70% of the
fer to the numbers of models.                                                   ensembles constructed on midsize OpenML datasets have no more
                                                                                than 5 base learners. This parsimony makes our ensembles easy to
Table 1: Runtime prediction accuracy on OpenML datasets                         implement and interpret.

                                                                                6                 SUMMARY
 Algorithm type                         Runtime prediction accuracy
                                    within factor of 2 within factor of 4       Oboe is an AutoML system that uses collaborative filtering and
 Adaboost                                       83.6%              94.3%        optimal experiment design to predict performance of machine learn-
 Decision tree                                  76.7%              88.1%        ing models. By fitting a few models on the meta-test dataset, this
 Extra trees                                    96.6%              99.5%        system transfers knowledge from meta-training datasets to select a
 Gradient boosting                              53.9%              84.3%        promising set of models. Oboe naturally handles different algorithm
 Gaussian naive Bayes                           89.6%              96.7%        and hyperparameter types and can match state-of-the-art perfor-
 kNN                                            85.2%              88.2%        mance of AutoML systems much more quickly than competing
 Logistic regression                            41.1%              76.0%        approaches.
 Multilayer perceptron                          78.9%              96.0%           This work demonstrates the promise of collaborative filtering
 Perceptron                                     75.4%              94.3%        approaches to AutoML. However, there is much more left to do.
 Random Forest                                  94.4%              98.2%        Future work is needed to adapt Oboe to different loss metrics, bud-
 Kernel SVM                                     59.9%              86.7%        get types, sparsely observed error matrices, and a wider range of
 Linear SVM                                     30.1%              73.2%        machine learning algorithms. Adapting a collaborative filtering
                                                                                approach to search for good machine learning pipelines, rather than
                                                                                individual algorithms, presents a more substantial challenge. We
on the conditioning of the problem. Our runtime prediction ac-                  also hope to see more approaches to the challenge of choosing
curacy on midsize OpenML datasets is shown in Table 1 and in                    hyper-hyperparameter settings subject to limited computation and
Figure 7. We can see that our empirical prediction of model run-                data: meta-learning is generally data(set)-constrained. With contime is roughly unbiased. Thus the sum of predicted runtimes on                 tinuing efforts by the AutoML community, we look forward to a
multiple models is a roughly good estimate.                                     world in which domain experts seeking to use machine learning
Cold-start. Oboe uses D-optimal experiment design to cold-start                 can focus on data quality and problem formulation, rather than on
model selection. In Figure 9, we compare this choice with A- and                tasks — such as algorithm selection and hyperparameter tuning —
E-optimal design and nonlinear regression in Alors [28], by means               which are suitable for automation.
of leave-one-out cross-validation on midsize OpenML datasets. We
measure performance by the relative RMSE ∥e − ê ∥2 /∥e ∥2 of the               ACKNOWLEDGMENTS
predicted performance vector and by the number of correctly pre-                This work was supported in part by DARPA Award FA8750-17-2dicted best models, both averaged across datasets. The approximate              0101. The authors thank Christophe Giraud-Carrier, Ameet Talrank of the error matrix is set to be the number of eigenvalues                 walkar, Raul Astudillo Marban, Matthew Zalesak, Lijun Ding and
                                                                            8

Davis Wertheimer for helpful discussions, thank Jack Dunn for a                                  [27] David JC MacKay. 1992. Information-based objective functions for active data
script to parse UCI Machine Learning Repository datasets, and also                                    selection. Neural Computation 4, 4 (1992), 590–604.
                                                                                                 [28] Mustafa Mısır and Michèle Sebag. 2017. Alors: An algorithm recommender
thank several anonymous reviewers for useful comments.                                                system. Artificial Intelligence 244 (2017), 291–314.
                                                                                                 [29] Alexander M Mood et al. 1946. On Hotelling’s weighing problem. The Annals of
                                                                                                      Mathematical Statistics 17, 4 (1946), 432–446.
                                                                                                 [30] F. Pedregosa, G. Varoquaux, A. Gramfort, V. Michel, B. Thirion, O. Grisel, M.
REFERENCES                                                                                            Blondel, P. Prettenhofer, R. Weiss, V. Dubourg, J. Vanderplas, A. Passos, D. Cour-
 [1] Rémi Bardenet, Mátyás Brendel, Balázs Kégl, and Michele Sebag. 2013. Collabo-                    napeau, M. Brucher, M. Perrot, and E. Duchesnay. 2011. Scikit-learn: Machine
     rative hyperparameter tuning. In ICML. 199–207.                                                  Learning in Python. Journal of Machine Learning Research 12 (2011), 2825–2830.
 [2] Thomas Bartz-Beielstein and Sandor Markon. 2004. Tuning search algorithms                   [31] Bernhard Pfahringer, Hilan Bensusan, and Christophe G Giraud-Carrier. 2000.
     for real-world applications: A regression tree based approach. In Congress on                    Meta-Learning by Landmarking Various Learning Algorithms. In ICML. 743–750.
     Evolutionary Computation, Vol. 1. IEEE, 1111–1118.                                          [32] Friedrich Pukelsheim. 1993. Optimal design of experiments. Vol. 50. SIAM.
 [3] James S Bergstra, Rémi Bardenet, Yoshua Bengio, and Balázs Kégl. 2011. Al-                  [33] Carl Edward Rasmussen and Christopher KI Williams. 2006. Gaussian processes
     gorithms for hyper-parameter optimization. In Advances in Neural Information                     for machine learning. the MIT Press.
     Processing Systems. 2546–2554.                                                              [34] Paola Sebastiani and Henry P Wynn. 2000. Maximum entropy sampling and
 [4] Bernd Bischl, Jakob Richter, Jakob Bossek, Daniel Horn, Janek Thomas, and Michel                 optimal Bayesian experimental design. Journal of the Royal Statistical Society:
     Lang. 2017. mlrMBO: A modular framework for model-based optimization of                          Series B (Statistical Methodology) 62, 1 (2000), 145–157.
     expensive black-box functions. arXiv preprint arXiv:1703.03373 (2017).                      [35] Kate Smith-Miles and Jano van Hemert. 2011. Discovering the suitability of opti-
 [5] Stephen Boyd and Lieven Vandenberghe. 2004. Convex optimization. Cambridge                       misation algorithms by learning from evolved instances. Annals of Mathematics
     University Press.                                                                                and Artificial Intelligence 61, 2 (2011), 87–104.
 [6] Rich Caruana, Art Munson, and Alexandru Niculescu-Mizil. 2006. Getting the                  [36] Jasper Snoek, Hugo Larochelle, and Ryan P Adams. 2012. Practical Bayesian
     most out of ensemble selection. In ICDM. IEEE, 828–833.                                          optimization of machine learning algorithms. In Advances in Neural Information
 [7] Rich Caruana, Alexandru Niculescu-Mizil, Geoff Crew, and Alex Ksikes. 2004.                      Processing Systems. 2951–2959.
     Ensemble selection from libraries of models. In ICML. ACM, 18.                              [37] Niranjan Srinivas, Andreas Krause, Sham Kakade, and Matthias Seeger. 2010.
 [8] Boyuan Chen, Harvey Wu, Warren Mo, Ishanu Chattopadhyay, and Hod Lipson.                         Gaussian Process Optimization in the Bandit Setting: No Regret and Experimental
     2018. Autostacker: A compositional evolutionary learning system. In Proceedings                  Design. In ICML. 1015–1022.
     of the Genetic and Evolutionary Computation Conference. ACM, 402–409.                       [38] David H Stern, Horst Samulowitz, Ralf Herbrich, Thore Graepel, Luca Pulina,
 [9] Tiago Cunha, Carlos Soares, and André C. P. L. F. de Carvalho. 2018. CF4CF:                      and Armando Tacchella. 2010. Collaborative Expert Portfolio Management. In
     Recommending Collaborative Filtering Algorithms Using Collaborative Filtering.                   AAAI. 179–184.
     In Proceedings of the 12th ACM Conference on Recommender Systems (RecSys ’18).              [39] Ali Caner Türkmen. 2015. A review of nonnegative matrix factorization methods
     ACM, New York, NY, USA, 357–361. https://doi.org/10.1145/3240323.3240378                         for clustering. arXiv preprint arXiv:1507.03194 (2015).
[10] Dua Dheeru and Efi Karra Taniskidou. 2017. UCI Machine Learning Repository.                 [40] Madeleine Udell and Alex Townsend. 2019. Why Are Big Data Matrices Approx-
     http://archive.ics.uci.edu/ml                                                                    imately Low Rank? SIAM Journal on Mathematics of Data Science 1, 1 (2019),
[11] Iddo Drori, Yamuna Krishnamurthy, Remi Rampin, Raoni de Paula Lourenco,                          144–160.
     Jorge Piazentin Ono, Kyunghyun Cho, Claudio Silva, and Juliana Freire. 2018.                [41] Joaquin Vanschoren, Jan N. van Rijn, Bernd Bischl, and Luis Torgo. 2013. OpenML:
     AlphaD3M: Machine learning pipeline synthesis. In AutoML Workshop at ICML.                       Networked Science in Machine Learning. SIGKDD Explorations 15, 2 (2013), 49–60.
[12] Matthias Feurer, Aaron Klein, Katharina Eggensperger, Jost Springenberg, Manuel                  https://doi.org/10.1145/2641190.2641198
     Blum, and Frank Hutter. 2015. Efficient and robust automated machine learning.              [42] Abraham Wald. 1943. On the efficient design of statistical investigations. The
     In Advances in Neural Information Processing Systems. 2962–2970.                                 Annals of Mathematical Statistics 14, 2 (1943), 134–140.
[13] Matthias Feurer, Jost Tobias Springenberg, and Frank Hutter. 2014. Using meta-              [43] M. Wistuba, N. Schilling, and L. Schmidt-Thieme. 2015. Learning hyperparameter
     learning to initialize Bayesian optimization of hyperparameters. In International                optimization initializations. In IEEE International Conference on Data Science and
     Conference on Meta-learning and Algorithm Selection. Citeseer, 3–10.                             Advanced Analytics. 1–10. https://doi.org/10.1109/DSAA.2015.7344817
[14] Nicolo Fusi, Rishit Sheth, and Melih Elibol. 2018. Probabilistic matrix factorization       [44] Wei Xu, Xin Liu, and Yihong Gong. 2003. Document clustering based on non-
     for automated machine learning. In Advances in Neural Information Processing                     negative matrix factorization. In Proceedings of the 26th annual international
     Systems. 3352–3361.                                                                              ACM SIGIR conference on Research and development in informaion retrieval. ACM,
[15] Gene H Golub and Charles F Van Loan. 2012. Matrix computations. JHU Press.                       267–273.
[16] Elad Hazan, Adam Klivans, and Yang Yuan. 2018. Hyperparameter optimization:                 [45] Dani Yogatama and Gideon Mann. 2014. Efficient transfer learning method for
     a spectral approach. In ICLR. https://openreview.net/forum?id=H1zriGeCZ                          automatic hyperparameter tuning. In Artificial Intelligence and Statistics. 1077–
[17] Ralf Herbrich, Neil D Lawrence, and Matthias Seeger. 2003. Fast sparse Gauss-                    1085.
     ian process methods: The informative vector machine. In Advances in Neural                  [46] Yuyu Zhang, Mohammad Taha Bahadori, Hang Su, and Jimeng Sun. 2016. FLASH:
     Information Processing Systems. 625–632.                                                         fast Bayesian optimization for data analytic pipelines. In Proceedings of the 22nd
[18] Ling Huang, Jinzhu Jia, Bin Yu, Byung-Gon Chun, Petros Maniatis, and Mayur                       ACM SIGKDD International Conference on Knowledge Discovery and Data Mining.
     Naik. 2010. Predicting execution time of computer programs using sparse polyno-                  ACM, 2065–2074.
     mial regression. In Advances in Neural Information Processing Systems. 883–891.
[19] Frank Hutter, Youssef Hamadi, Holger H Hoos, and Kevin Leyton-Brown. 2006.
     Performance prediction and automated tuning of randomized and parametric
     algorithms. In International Conference on Principles and Practice of Constraint
     Programming. Springer, 213–228.
[20] Frank Hutter, Holger H Hoos, and Kevin Leyton-Brown. 2011. Sequential Model-
     Based Optimization for General Algorithm Configuration. LION 5 (2011), 507–
     523.
[21] Frank Hutter, Lin Xu, Holger H Hoos, and Kevin Leyton-Brown. 2014. Algorithm
     runtime prediction: Methods & evaluation. Artificial Intelligence 206 (2014),
     79–111.
[22] RC St John and Norman R Draper. 1975. D-optimality for regression designs: a
     review. Technometrics 17, 1 (1975), 15–23.
[23] Jingu Kim and Haesun Park. 2008. Sparse nonnegative matrix factorization for
     clustering. Technical Report. Georgia Institute of Technology.
[24] Andreas Krause, Ajit Singh, and Carlos Guestrin. 2008. Near-optimal sensor
     placements in Gaussian processes: Theory, efficient algorithms and empirical
     studies. Journal of Machine Learning Research 9, Feb (2008), 235–284.
[25] Rui Leite, Pavel Brazdil, and Joaquin Vanschoren. 2012. Selecting classification
     algorithms with active testing. In International Workshop on Machine Learning
     and Data Mining in Pattern Recognition. Springer, 117–131.
[26] Christiane Lemke, Marcin Budka, and Bogdan Gabrys. 2015. Metalearning: a
     survey of trends and technologies. Artificial Intelligence Review 44, 1 (2015),
     117–130.
                                                                                             9

                                                                                                                          Table 2: Base Algorithm and Hyperparameter Settings

                                        Algorithm type                   Hyperparameter names (values)
                                        Adaboost                         n_estimators (50,100), learning_rate (1.0,1.5,2.0,2.5,3)
                                        Decision tree                    min_samples_split (2,4,8,16,32,64,128,256,512,1024,0.01,0.001,0.0001,1e-05)
                                        Extra trees                      min_samples_split (2,4,8,16,32,64,128,256,512,1024,0.01,0.001,0.0001,1e-05),
                                                                         criterion (gini,entropy)
                                        Gradient boosting                learning_rate (0.001,0.01,0.025,0.05,0.1,0.25,0.5), max_depth (3, 6), max_features
                                                                         (null,log2)
                                        Gaussian naive Bayes             -
                                        kNN                              n_neighbors (1,3,5,7,9,11,13,15), p (1,2)
                                        Logistic regression              C (0.25,0.5,0.75,1,1.5,2,3,4), solver (liblinear,saga), penalty (l1,l2)
                                        Multilayer perceptron            learning_rate_init     (0.0001,0.001,0.01),  learning_rate    (adaptive),    solver
                                                                         (sgd,adam), alpha (0.0001, 0.01)
                                        Perceptron                       -
                                        Random forest                    min_samples_split (2,4,8,16,32,64,128,256,512,1024,0.01,0.001,0.0001,1e-05),
                                                                         criterion (gini,entropy)
                                        Kernel SVM                       C (0.125,0.25,0.5,0.75,1,2,4,8,16), kernel (rbf,poly), coef0 (0,10)
                                        Linear SVM                       C (0.125,0.25,0.5,0.75,1,2,4,8,16)

   For reproducibility, please refer to our GitHub repositories (the                                                                                         D                      COMPARISON OF EXPERIMENT DESIGN
Oboe system: https://github.com/udellgroup/oboe; experiments:                                                                                                                       WITH DIFFERENT CONSTRAINTS
https://github.com/udellgroup/oboe-testing). Additional informa-
                                                                                                                                                             In Section 5.1.1, our experiments compare QR and PMF to a variant
tion is as follows.
                                                                                                                                                             of experiment design (ED) with a constraint on the number of
                                                                                                                                                             observed entries, since QR and PMF admit a similar constraint.
A                                      MACHINE LEARNING MODELS
                                                                                                                                                             Figure 12 shows that the regret of ED with a runtime constraint
Shown in Table 2, the hyperparameter names are the same as those                                                                                             (Equation 1) is not too much larger.
in scikit-learn 0.19.2.

                                                                                                                                                              regret (mean ± standard error)
B                                      DATASET META-FEATURES
Dataset meta-features used throughout the experiments are listed                                                                                                                  0.05
in Table 3 (next page).
                                                                                                                                                                                  0.04
C                                      META-FEATURE CALCULATION TIME
On a number of not very large datasets, the time taken to calculate                                                                                                               0.03
meta-features in the previous section are already non-negligible,
as shown in Figure 11. Each dot represents one midsize OpenML                                                                                                                     0.02
dataset.
                                                                                                                                                                                  0.01

    Metafeature calculation time (s)                                          Metafeature calculation time (s)
                                                                                                                                                                                  0.00
                                       15                                                                        15                                                                  5(2%) 15(6%) 25(11%) 35(15%)
                                       10                                                                        10
                                                                                                                                                                                     number (percentage) of observed entries
                                                                                                                                                                                               ED (time)                      ED (number) with meta-features
                                        5                                                                         5                                                                            ED (time) with meta-features   PMF
                                                                                                                                                                                               ED (number)
                                        0                                                                         0
                                            0          5000           10000                                           0        100       200      300
                                                Number of data points                                                        Number of features
                                                                                                                                                             Figure 12: Comparison of different versions of ED with PMF.
                                                                                                                                                             "ED (time)" denotes ED with runtime constraint, with time
Figure 11: Meta-feature calculation time and corresponding
                                                                                                                                                             limit set to be 10% of the total runtime of all available moddataset sizes of the midsize OpenML datasets. The collection
                                                                                                                                                             els; "ED (number)" denotes ED with the number of entries
of meta-features is the same as that used by auto-sklearn
                                                                                                                                                             constrained.
[12]. We can see some calculation times are not negligible.

                                                                                                                                                        10

                                                Table 3: Dataset Meta-features

Meta-feature name                             Explanation
number of instances                           number of data points in the dataset
log number of instances                       the (natural) logarithm of number of instances
number of classes
number of features
log number of features                        the (natural) logarithm of number of features
number of instances with missing values
percentage of instances with missing values
number of features with missing values
percentage of features with missing values
number of missing values
percentage of missing values
number of numeric features
number of categorical features
ratio numerical to nominal                    the ratio of number of numerical features to the number of categorical features
ratio numerical to nominal
dataset ratio                                 the ratio of number of features to the number of data points
log dataset ratio                             the natural logarithm of dataset ratio
inverse dataset ratio
log inverse dataset ratio
class probability (min, max, mean, std)       the (min, max, mean, std) of ratios of data points in each class
symbols (min, max, mean, std, sum)            the (min, max, mean, std, sum) of the numbers of symbols in all categorical features
kurtosis (min, max, mean, std)
skewness (min, max, mean, std)
class entropy                                 the entropy of the distribution of class labels (logarithm base 2)

landmarking [31] meta-features
LDA
decision tree                                 decision tree classifier with 10-fold cross validation
decision node learner                         10-fold cross-validated decision tree classifier with criterion="entropy", max_depth=1,
                                              min_samples_split=2, min_samples_leaf=1, max_features=None
random node learner                           10-fold cross-validated decision tree classifier with max_features=1 and the same above for
                                              the rest
1-NN
PCA fraction of components for 95% variance   the fraction of components that account for 95% of variance
PCA kurtosis first PC                         kurtosis of the dimensionality-reduced data matrix along the first principal component
PCA skewness first PC                         skewness of the dimensionality-reduced data matrix along the first principal component

                                                                 11
