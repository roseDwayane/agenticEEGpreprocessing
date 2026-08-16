---
citation_key: "FeurerEtAl2015"
title: "Initializing Bayesian Hyperparameter Optimization via Meta-Learning"
authors: "Matthias Feurer; Jost Tobias Springenberg; Frank Hutter"
year: 2015
doi: "10.1609/aaai.v29i1.9354"
source: "AAAI OJS"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
conversion: "pdftotext -layout (automated)"
---

# Initializing Bayesian Hyperparameter Optimization via Meta-Learning

Proceedings of the Twenty-Ninth AAAI Conference on Artificial Intelligence

         Initializing Bayesian Hyperparameter Optimization via Meta-Learning

                      Matthias Feurer and Jost Tobias Springenberg and Frank Hutter
                                             {feurerm,springj,fh}@cs.uni-freiburg.de
                                        Computer Science Department, University of Freiburg
                                                     Georges-Köhler-Allee 52
                                                    79110 Freiburg, Germany

                            Abstract                                         performs combined algorithm selection and hyperparameter
                                                                             optimization in the space of algorithms defined by the WEKA
  Model selection and hyperparameter optimization is crucial in              package (Hall et al. 2009).
  applying machine learning to a novel dataset. Recently, a sub-
  community of machine learning has focused on solving this
                                                                                However, as a generic function optimization framework,
  problem with Sequential Model-based Bayesian Optimization                  SMBO requires a substantial number of evaluations to de-
  (SMBO), demonstrating substantial successes in many appli-                 tect high-performance regions when started on a new opti-
  cations. However, for computationally expensive algorithms                 mization problem. The resulting overhead is computationally
  the overhead of hyperparameter optimization can still be pro-              infeasible for expensive-to-evaluate machine learning algo-
  hibitive. In this paper we mimic a strategy human domain                   rithms. A promising approach to combat this problem is to
  experts use: speed up optimization by starting from promising              apply meta-learning (Brazdil et al. 2008) to the hyperparam-
  configurations that performed well on similar datasets. The                eter search problem. The key concept behind meta-learning
  resulting initialization technique integrates naturally into the           for hyperparameter search is to suggest good configurations
  generic SMBO framework and can be trivially applied to any                 for a novel dataset based on configurations that are known
  SMBO method. To validate our approach, we perform exten-
  sive experiments with two established SMBO frameworks
                                                                             to perform well on similar, previously evaluated, datasets.
  (Spearmint and SMAC) with complementary strengths; opti-                   We follow this strategy to yield a simple and effective ini-
  mizing two machine learning frameworks on 57 datasets. Our                 tialization procedure that applies generically to all variants
  initialization procedure yields mild improvements for low-                 of SMBO; we refer to the resulting SMBO approach with
  dimensional hyperparameter optimization and substantially                  meta-learning-based initialization as MI-SMBO. Importantly,
  improves the state of the art for the more complex combined                MI-SMBO does not require any adaptation of the underlying
  algorithm selection and hyperparameter optimization problem.               SMBO procedure. It is hence easy to implement and can be
                                                                             readily applied to off-the-shelf hyperparameter optimizers.
                                                                                We empirically studied the impact of our meta-learning-
                        Introduction                                         based initialization procedure on two SMBO variants, using
Hyperparameter optimization is a crucial step in the process                 a comprehensive suite of 57 classification datasets and 46
of applying machine learning algorithms in practice. Find-                   metafeatures. First, we applied our method to a combined aling good hyperparameter settings manually is often a time-                   gorithm selection and hyperparameter (CASH) optimization
consuming, tedious process requiring many ad-hoc choices                     problem on this benchmark: choosing between three classiby the practitioner. As a result, much recent work in machine                fiers from the prominent scikit-learn package (Pedregosa et
learning has focused on the development of better hyperpa-                   al. 2011) and simultaneously optimizing their hyperparamerameter optimization methods (Hutter, Hoos, and Leyton-                      ters. Second, to demonstrate the generality of our approach,
Brown 2011; Bergstra et al. 2011; Snoek, Larochelle, and                     we applied MI-SMBO to the lower-dimensional problem
Adams 2012; Bergstra and Bengio 2012).                                       of optimizing the 2 hyperparameters of a support vector
   Recently, Sequential Model-based Bayesian Optimization                    machine (SVM) on the same datasets. We found that for
(SMBO, see, e.g., Brochu, Cora, and de Freitas (2010) for                    the lower-dimensional problem our MI-Spearmint variant of
an overview) has emerged as a successful hyperparameter                      Spearmint (Snoek, Larochelle, and Adams 2012) (a stateoptimization method in machine learning. SMBO has been                       of-the-art approach for low-dimensional hyperparameter opconclusively shown to yield better performance than both                     timization) yielded mild improvements. For the more chalgrid and random search and matched or outperformed the                       lenging CASH problem our MI-SMAC variant of the SMBO
state-of-the-art performance for several challenging machine                 method SMAC (Hutter, Hoos, and Leyton-Brown 2011) (a
learning problems (Snoek, Larochelle, and Adams 2012;                        state-of-the-art approach for CASH optimization) yielded
Bergstra et al. 2011; Bergstra, Yamins, and Cox 2013). It                    substantial improvements, significantly outperforming the
has also enabled AutoWEKA (Thornton et al. 2013), which                      previous state of the art for this problem.
Copyright c 2015, Association for the Advancement of Artificial                  This paper is an improved and extended version of a previous
Intelligence (www.aaai.org). All rights reserved.                            workshop submission (Feurer, Springenberg, and Hutter 2014)

                                                                      1128

                         Foundations                                         Algorithm 1: Generic Sequential Model-based Optimiza-
Before we describe our MI-SMBO approach in detail we                         tion. SMBO(f D , T , Θ, θ1:t )
formally describe hyperparameter optimization and SMBO.                       Input: Target function f D ; limit T ; hyperparameter
                                                                                     space Θ; initial design θ1:t = hθ1 , . . . , θt i
Hyperparameter Optimization                                                   Result: Best hyperparameter configuration θ ∗ found
                                                                                                                   D
                                                                            1 for i ← 1 to t do yi ← Evaluate f (θi )
Let θ1 , . . . , θn denote the hyperparameters of a machine
                                                                            2 for j ← t + 1 to T do
learning algorithm, and let Θ1 , . . . , Θn denote their respective domains. The algorithm’s hyperparameter space is then                  3     M ← fit model on performance data hθi , yi ij−1   i=1
defined as Θ = Θ1 × · · · × Θn . When trained with θ ∈ Θ on                 4     Select θj ∈ arg maxθ∈Θ a(θ, M)
data Dtrain , we denote the algorithm’s validation error on data            5     yj ← Evaluate f D (θj )
Dvalid as V(θ, Dtrain , Dvalid ). Using k-fold cross-validation,                       ∗
                                                                            6 return θ ∈ arg minθj ∈{θ1 ,...,θT } yj
the hyperparameter optimization problem for a given dataset
D is to minimize:
                            k
                         1X          (i)      (i)                           by all three SMBO methods we discuss) is the expected posi-
             f D (θ) =         V(θ, Dtrain , Dvalid ).        (1)           tive improvement (EI) over the best input found so far (Jones,
                         k i=1
                                                                            Schonlau, and Welch 1998):
   Hyperparameters θi can be numerical (real or integer, as,                                    Z ∞
e.g., the strength of a regularizer) or categorical (unordered,
with finite domain, as, e.g., the choice between different                      aEI (θ, M) =          max(y ∗ − y, 0)pM (y|θ)dy .         (2)
                                                                                                 −∞
kernels). Furthermore, there can be conditional hyperparameters, which are only active if another hyperparameter takes                   Several different model types can be used inside of SMBO.
a certain value; for example, setting the “number of princi-                The most popular choice, used for example by Spearmint, are
pal components” is conditioned on the usage of PCA as a                     Gaussian processes (Rasmussen and Williams 2006) because
preprocessing method.                                                       they provide good predictions in low-dimensional numeri-
   The most frequently used hyperparameter optimization                     cal input spaces and allow the computation of the posterior
method is grid search, which often performs poorly and does                 Gaussian process model in closed form. The other popular
not scale to high dimensions. Therefore, a large body of                    model type are tree-based approaches, which are particurecent work has focused on better-performing methods, in                    larly well suited to handle high-dimensional and partially
particular SMBO, which we describe in the following section.                categorical input spaces. In particular, SMAC uses random
                                                                            forests (Breiman 2001) modified to yield an uncertainty esti-
Sequential Model-based Bayesian Optimization                                mate (Hutter et al. 2014). Another tree-based approach is the
                                                                            Tree Parzen Estimator (TPE) (Bergstra et al. 2011), which
Sequential Model-based Bayesian Optimization (SMBO)                         constructs a density estimate over good and bad instantiations
(Jones, Schonlau, and Welch 1998; Brochu, Cora, and de Fre-                 of each hyperparameter.
itas 2010; Hutter, Hoos, and Leyton-Brown 2011) is a pow-                      The final degree of freedom in SMBO is its initialization.
erful method for global optimization of expensive blackbox                  A classic approach is to initialize SMBO with a space-filling
functions f . As described in Algorithm 1, SMBO starts by                   design (Jones, Schonlau, and Welch 1998). While this can
querying the function f at the t values in an initial design and            greatly improve the quality of the model, the correspondrecording the resulting hinput, outputi pairs hθi , f (θi )iti=1 .          ing function evaluations are also costly and for expensive
Afterwards, it iterates the following three phases: (1) fit a               hyperparameter optimization problems a cheaper solution
probabilistic model M to the hinput, outputi pairs collected                is needed. To date, this initialization component has not reso far; (2) use the probabilistic model M to select a promis-               ceived much attention, and it is typically instantiated in a
ing input θ to evaluate next by quantifying the desirability                fairly ad-hoc manner: Spearmint evaluates f at the first two
of obtaining the function value at arbitrary inputs θ ∈ Θ                   points of a Sobol sequence, SMAC evaluates it at a userthrough a so-called acquisition function a(θ, M ); (3) evalu-               defined ‘default’ input, and TPE evaluates 20 points selected
ate the function at the new input θ.                                        at random according to a user-defined prior distribution. It is
   The SMBO framework offers several degrees of freedom to                  this initialization component that our MI-SMBO approach
be instantiated, including the procedure’s initialization, the ac-          improves by starting from a list of hyperparameter configuraquisition function to use, and the type of probabilistic model.             tions suggested by meta-learning.
We discuss three prominent hyperparameter optimization
methods in terms of these components: SMAC (Hutter, Hoos,                       Initializing SMBO With Configurations
and Leyton-Brown 2011), Spearmint (Snoek, Larochelle, and
Adams 2012), and TPE (Bergstra et al. 2011).                                          Suggested by Meta-Learning
   The role of the acquisition function a(θ, M) is to trade off             Building on the foundations from the previous section we
exploration in hyperparameter regions where the model M is                  now describe our proposed MI-SMBO method.
uncertain with exploitation in regions with low predicted val-                The core idea behind MI-SMBO is to follow the common
idation error. The most commonly acquisition function (used                 practice machine learning experts employ when applying a

                                                                     1129

 Algorithm 2: SMBO with Meta-Learning Initialization.                          mi = (mi1 , . . . , miF ). We discuss the metafeatures we used
                                                                               in the next section. In practice, we precompute the metafea-
 MI-SMBO(DN +1 , f DN +1 , D1:N , θ̂ 1:N , d, t, T , Θ)                        tures for all training datasets D1 , . . . , DN , along with the best
  Input: new dataset DN +1 ; target function f DN +1 ;                         configurations (θ̂ 1 , . . . , θ̂ N ). Given a new dataset DN +1 , we
         training datasets D1:N = (D1 , . . . , DN ); best                     then measure its distances to all previous datasets Di using a
         configurations for training datasets,                                 distance measure d : D × D → R.
         θ̂ 1:N = θ̂ 1 , . . . , θ̂ N ; distance metric d; number                 We experimented with two different instantiations of this
         of configurations to include in initial design, t;                    distance measure d(·, ·). The first measure (denoted as dp ) we
         limit T ; hyperparameter space Θ                                      used is the commonly-used p-norm of the difference between
  Result: Best hyperparameter configuration θ ∗ found                          the datasets’ metafeatures:
1 Sort dataset indices π(1), . . . π(N ) by increasing
                                                                                                dp (Di , Dj ) = kmi − mj kp .                       (3)
  distance to DN +1 , i.e.:
  (π(i) ≤ π(j)) ⇔ (d(DN +1 , Di ) ≤ d(DN +1 , Dj ))                            Next to this standard metric we aimed for a metric that reflects
2 for i ← 1 to t do θi ← θ̂
                                     π(i)                                      how similar the datasets are with respect to the performance
    ∗              D
3 θ ← SMBO(f , T , Θ, θ1:t )
                                                                               of different hyperparameter settings. The measure we use
4 return θ
            ∗                                                                  (in the following denoted as dc ) is the negative Spearman
                                                                               correlation coefficient between the ranked results of a fixed
                                                                               set of n hyperparameter configurations on both datasets1 :
                                                                                  dc (Di , Dj ) = 1 − Corr([f Di (θ1 ), . . . , f Di (θn )],
known machine learning method to a new dataset DN +1 :                                                                                              (4)
they first study DN +1 , relating it to datasets they previously                                               [f Dj (θ1 ), . . . , f Dj (θn )]).
experienced. When manually optimizing hyperparameters                             Of course, this distance measure cannot be computed difor DN +1 , they would begin the search with hyperparameter                    rectly for the new dataset DN +1 since we have not yet evalconfigurations that were optimal for the most similar pre-                     uated f DN +1 (θ1 ), . . . , f DN +1 (θN ). However, we can comvious datasets (see, e.g., Dahl, Sainath, and Hinton (2013);                   pute dc (Di , Dj ) for all 1 ≤ i, j ≤ N and use regression to
Goodfellow et al. (2013)). Our MI-SMBO method automates                        learn a function R : RF × RF → R, mapping from pairs of
this approach and uses it to initialize an SMBO method.                        meta-features hmi , mj i to dc (Di , Dj ).
    Formally, MI-SMBO can be stated as follows. Let                               Using this pre-trained regressor the distance metric can
θ̂ 1 , . . . , θ̂ N denote the best known hyperparameters for the              then be approximated as
previously encountered datasets D1 , . . . , DN , respectively.
These may originate from an arbitrary source, e.g., a man-                                   dc (DN +1 , Dj ) ≈ R(mN +1 , mj ).                     (5)
ual search or the application of an SMBO method during                         In our experiments, we implemented R using a random forest
an offline training phase. Further, let DN +1 denote a new                     because of its robustness and speed.
dataset, let d denote a distance metric between datasets, and
let π denote a permutation of (1, . . . , N ) sorted by increas-               Implemented Metafeatures
ing distance between DN +1 and Di (i.e., (π(i) ≤ π(j)) ⇔                       To evaluate our approach in a realistic setting we imple-
(d(DN +1 , Di ) ≤ d(DN +1 , Dj ))). Then, MI-SMBO with an                      mented 46 metafeatures from the literature; they are listed
initial design of t configurations initializes SMBO with                       in Table 1. Based on their types and underlying assumptions,
configurations θ̂ π(1) , . . . , θ̂ π(t) . Algorithm 2 provides pseu-          these metafeatures can be divided into at least five groups:
docode for the approach.
                                                                               • Simple metafeatures, such as the number of features, pat-
    We would like to highlight the fact that MI-SMBO is agnos-                    terns or classes, describe the basic dataset structure (Michie
tic of the SMBO algorithm used, as long as the algorithm’s                        et al. 1994; Kalousis 2002)
implementation accepts an initial design as input or can be
warmstarted with a given list of performance data hθi , yi iti=1 .             • PCA metafeatures (Bardenet et al. 2013) compute various
All of SMAC, TPE, and Spearmint fulfill these criteria. Fur-                      statistics of the datasets principal components.
thermore, in contrast to existing approaches that initialize di-               • The information-theoretic metafeature measures the class
rect search algorithms via meta-learning (Gomes et al. 2012;                      entropy in the data (Michie et al. 1994).
Reif, Shafait, and Dengel 2012), SMBO is a particularly good                   • Statistical metafeatures (Michie et al. 1994) characterize
match for initialization with meta-learning as it can make                        the data via descriptive statistics such as the kurtosis or the
effective use of all performance data it receives as input. In                    dispersion of the label distribution.
practice, this procedure replaces SMACs and Spearmints initialization and delays the start of the actual SMBO procedure                  • Landmarking metafeatures (Pfahringer, Bensusan, and
                                                                                  Giraud-Carrier 2000) are computed by running several
until all configurations θ̂ π(1) , . . . , θ̂ π(t) are evaluated.
                                                                                  fast machine learning algorithms on the dataset. Based on
    The last component needed to implement MI-SMBO is                             their learning scheme they can capture different properties
the definition of a distance metric between datasets. This                        of the dataset, like e.g. linear separability.
problem was, to our knowledge, first discussed by Soares and
Brazdil (2000). For the purpose of this work we assume that                       1
                                                                                    In practice, we used all n hyperparameter configurations for
each dataset Di can be described by a set of F metafeatures                    which we had results available on all training datasets.

                                                                        1130

 Simple metafeatures:                         Statistical metafeatures:                Component    Hyperparameter                           Values   # Values
 number of patterns                           min # categorical values
                                                                                       Main                   θclassifier   {RF, SVM, LinearSVM}            3
 log number of patterns                       max # categorical values
                                                                                       Main           preprocessing                  {PCA, None}            2
 number of classes                            mean # categorical values
                                                                                       SVM                 log2 (C)            {−5, −4, . . . , 15}        21
 number of features                           std # categorical values
                                                                                       SVM                  log2 (γ)          {−15, −14, . . . , 3}        19
 log number of features                       total # categorical values
                                                                                       LinearSVM           log2 (C)          {−15, −14, . . . , 15}        21
 number of patterns with missing values       kurtosis min
                                                                                       LinearSVM              penalty                    {L1 , L2 }         2
 percentage of patterns with missing values   kurtosis max
                                                                                       RF                 min splits               {1, 2, 4, 7, 10}         5
 number of features with missing values       kurtosis mean
                                                                                       RF              max features         {1%, 4%, . . . , 100%}         10
 percentage of features with missing values   kurtosis std
                                                                                       RF                    criterion             {Gini, Entropy}          2
 number of missing values                     skewness min
                                                                                       PCA          variance to keep                {80%, 90%}              2
 percentage of missing values                 skewness max
 number of numeric features                   skewness mean
 number of categorical features               skewness std                          Table 2: Hyperparameters for the CASH problem in scikit-
 ratio numerical to categorical                                                     learn. All hyperparameters except θclassifier and preprocessing
 ratio categorical to numerical               PCA metafeatures:                     are conditional. Hyperparameters not mentioned were set to
 dataset dimensionality                       pca 95%                               their default value.
 log dataset dimensionality                   pca skewness first pc
 inverse dataset dimensionality               pca kurtosis first pc
 log inverse dataset dimensionality
 class probability min                        Landmarking metafeatures:             one top-level hyperparameter θclassifier for choosing between
 class probability max                        One Nearest Neighbor                  classification algorithms, and set all hyperparameters of clas-
 class probability mean                       Linear Discriminant Analysis          sification algorithm Ai as conditional on θclassifier being set to
 class probability std                        Naive Bayes                           Ai . This CASH problem is of high practical relevance since
                                              Decision Tree                         it describes precisely the problem an end user faces when
 Information-theoretic                        Decision Node Learner                 given a new dataset.3 To keep the computation bearable and
 metafeature:                                 Random Node Learner                   the results interpretable, we only included three classifica-
 class entropy
                                                                                    tion algorithms: an SVM with an RBF kernel, a linear SVM,
                                                                                    and random forests. Since we expected noise and redundan-
          Table 1: List of implemented metafeatures                                 cies in the training data, we also allowed the optimization
                                                                                    procedure to use Principal Component Analysis (PCA) for
                                                                                    preprocessing; with the number of PCA components being
   For each dataset, metafeatures are only computed on the                          conditional on PCA being applied. In total this lead to 10
training set. In our experiments, for each dataset this required                    hyperparameters, as detailed in Table 2. We discretized these
less than one minute and less than the average time it took to                      10 hyperparameters to obtain a manageable number of 1 623
evaluate one hyperparameter configuration on that dataset.                          hyperparameter configurations that allowed the exhaustive
                                                                                    precomputation of classification errors for the entire grid.
 Application to Machine Learning Algorithms                                            We performed a second experiment to test the suitability
We now discuss the machine learning algorithms and their                            of our method for a low-dimensional hyperparameter ophyperparameters we optimized, as well as the datasets we                            timization problem: optimizing the complexity penalty C
used in our experiments.                                                            and the kernel width γ of an SVM using an RBF kernel. As
                                                                                    above, we discretized these two hyperparameters to a grid of
ML Algorithms and Hyperparameters                                                   19 · 21 = 399 combinations; these constitute a subset of the
We empirically evaluated our MI-SMBO approach to opti-                              configurations considered in the CASH problem above.
mize two practically relevant machine learning frameworks.
   We focused on supervised classification because it is the                        Datasets and Preprocessing
most widely studied problem in metalearning, with a large
                                                                                    For our experiments, we aimed for a large number of
body of literature and readily available metafeatures and
                                                                                    high-quality classification datasets. We found the OpenML
datasets.2
                                                                                    project (Vanschoren et al. 2013) to be the best source of
   The large configuration space for our main experiment
                                                                                    datasets and used the 60 classification datasets it contained
is spanned by a range of machine learning algorithms from
                                                                                    in April 2014. For computational reasons we had to exclude
scikit-learn (Pedregosa et al. 2011). We combined all algo-
                                                                                    three datasets, leaving us with a total of 57. We first shufrithms into a single hierarchical optimization problem using
                                                                                    fled each dataset and then split it in stratified fashion into
the Combined Algorithm Selection and Hyperparameter opti-
                                                                                    2/3 training and 1/3 test data. Then, we computed the valmization (CASH) setting by Thornton et al. (2013): we used
                                                                                    idation performance for Bayesian optimization by ten-fold
    2
      We note, however, that in principle, our procedure is applicable              crossvalidation on the training dataset.
to every optimization problem that is concerned with minimizing a                      To use the same dataset for each classification algorithm,
measurable objective and has a set of metafeatures describing the
                                                                                       3
problem. For example, one possible use in the field of unsupervised                      We note the existence of previous work on CASH variants
learning could be representation learning, with reconstruction error                for scitkit-learn (Hoffman, Shahriari, and de Freitas 2014; Komer,
as the objective.                                                                   Bergstra, and Eliasmith 2014).

                                                                             1131

          dataset: liver-disorders                        dataset: heart-h                               dataset: hepatitis

Figure 1: Difference in validation error between hyperparameters found by SMBO and the best value obtained via full grid search
for three datasets with scikit-learn. (20,d,X) stands for MI-SMAC with an initial design of t = 20 configurations suggested by
meta-learning with distance measure d using metafeatures X.

we coded categorical features using a one-hot (aka 1-in-k)                to dc in several experiments to avoid clutter in the plots. Our
encoding, replacing each categorical feature f with domain                experiments with different metafeatures showed that there
{v1 , . . . , vk } by k binary variables, only the i-th of which          is no general best set of metafeatures; thus, we only report
is set to true for data points where f is set to vi . To retain           results using all metafeatures.
sparsity, we replaced any missing values with zero. Finally,
we scaled numerical features linearly to the range [0, 1].                Warmstarting SMAC for Optimizing scikit-learn
                                                                          We now report our results for solving the CASH problem
                       Experiments                                        in scikit-learn. First, we evaluated the base performance of
                                                                          the hyperparameter optimization procedures random search,
Experimental Setup                                                        TPE, and SMAC (note that for TPE the prior distributions
We precomputed the 10-fold crossvalidation error on all 57                were uniform) on all 57 datasets and then added metadatasets for each of the 1 623 hyperparameter configurations              learning-initialization to the best of these. Due to the condiin our CASH problem. Because the configurations for the                   tional hyperparameters in the scikit-learn space we excluded
SVM benchmark form a subset of these configurations, the                  Spearmint, which – without modification – is known to percorresponding results were reused for the second experi-                  form poorly in their presence (Eggensperger et al. 2013).
ment. Although the classification datasets were no larger                    Figure 1 presents the qualitative performance of all optithan medium-sized (< 20 000 data points), calculating the                 mizers on three representative datasets. The plots show the
grid took up to three days per dataset on a modern CPU.                   mean of the best function values for one optimizer obtained
This extensive precomputation allowed us to run all our ex-               up to a given number of function evaluations. Overall, we
periments in simulation, by using a lookup table in lieu of               found SMAC to outperform both TPE and random search
running an actual algorithm.                                              for this large hyperparameter space, confirming the results
   We evaluated our MI-SMBO approach in a leave-one-                      of Eggensperger et al. (2013). We thus applied our metadataset-out fashion: to evaluate it on one dataset, we assumed            learning initialization to SMAC, but would also expect TPE
knowledge of the other 56 datasets and their best hyperpa-                to benefit from it.
rameter settings. Because Bayesian optimization contains                     Figure 1 also compares qualitative results of MI-SMAC
random factors, we repeated each optimization run ten times               to the three baselines. In the left plot, the meta-learning sugon each dataset. In total, we thus executed each optimization             gestions were reasonable and thus lead to MI-SMAC sucprocedure 570 times.                                                      cessively improving them over time. In the middle plot the
   Our meta-learning initialization approach has several free             second configuration suggested by meta-learning was already
design choices we had to instantiate for our experiments.                 the best, leaving no room for improvement by SMAC. The
These are: the distance metric d, the used metafeatures                   right plot highlights the fact that meta-learning can also fail
(we experimented with several subsets suggested in the lit-               and decrease SMAC’s performance.
erature (Pfahringer, Bensusan, and Giraud-Carrier 2000;                      Next, we analyzed MI-SMAC’s performance using the
Bardenet et al. 2013; Yogatama and Mann 2014)) and the                    same ranking-based evaluation as Bardenet et al. (2013) to
number t ∈ {5, 10, 20, 25} of configurations used for initial-            aggregate over datasets. For each dataset and each function
izing SMBO. In total, we evaluated 40 different instantia-                evaluation, we computed the ranks of the three baselines and
tions of our meta-learning procedure. Due to space restric-               the two MI-SMAC variants. More precisely, since we had 10
tions, we only report results for the best of these instantia-            runs of each of the five methods available for each dataset
tions; for more results, please see the supplementary material:           (which give rise to 105 possible combinations), we drew a
www.automl.org/aaai2015-mi-smbo-supplementary.pdf                         bootstrap sample of 1000 joint runs of the five optimizers and
   Concerning distance measures, we found the results with                computed the average ranks across these runs. We then further
dp and dc distance to be qualitatively similar, with slightly             averaged these average ranks across the 57 datasets and show
better results for the dc measure. We thus restrict the plots             the results in Figure 2. We remind the reader that the rank

                                                                   1132

                                                                         the statistically significant losses. Both of these quantities are
                                                                         plotted over time, as the function evaluation budget increases.
                                                                            Compared to the optimizers without meta-learning, MI-
                                                                         SMAC performed much better from the start. Even after 50
                                                                         iterations, it performed significantly better than TPE on 28%
                                                                         of the datasets (in 11% worse), better than SMAC on 35% of
                                                                         the datasets (in 7% worse), and better than random search on
                                                                         43% of the datasets (in 9% worse). We would like to point
                                                                         out that the improvement MI-SMAC yielded over SMAC is
                                                                         larger than the improvement that SMAC yielded over ran-
                                                                         dom search (in 20% better). We attribute this success to the
                                                                         large search space for this problem, which not even SMAC
                                                                         can effectively search in as little as 50 function evaluations.
Figure 2: Ranks of various optimizers averaged over all                  Leveraging successful optimizations from previous datasets
datasets for the CASH problem in scikit-learn.                           clearly helped SMAC in this complex search space.

                                                                         Warmstarting Spearmint for Optimizing SVMs
                                                                         To test the generality of our approach we performed an addi-
                                                                         tional experiment on a lower dimensional problem; optimiz-
                                                                         ing the hyperparameters of an SVM on all 57 datasets using
                                                                         Spearmint. We expected Spearmint to yield the best results
                                                                         for this problem as it is known to perform well in cases where
                                                                         the hyperparameters are few and real-valued (Eggensperger
                                                                         et al. 2013). A statistical analysis using a two-sided t-test on
                                                                         the performances for each of the 57 datasets confirms this
                                                                         hypothesis, as Spearmint indeed significantly outperformed
                                                                         TPE, SMAC, and random search in 32%, 44%, and 52% of
                                                                         the datasets, respectively, and only lost in 7%, 8%, and 9%
                                                                         of the cases, respectively.
Figure 3: Ranks of SMAC and various MI-SMAC variants                        The ranking plot in Figure 5 shows the performance
averaged over all datasets for the CASH problem in scikit-               of Spearmint and two MI-Spearmint variants compared to
learn.                                                                   SMAC, TPE and random search. As this plot shows, the three
                                                                         variants of Spearmint performed best, converging to a similar
                                                                         rank with larger function evaluation budgets. While metais a measure of performance relative to the performance of               learning yielded considerably better results for small functhe other optimizers; thus, a method’s rank can increase over            tion evaluation budgets, after about 10 evaluations Spearmint
time (with larger function evaluation budgets), even though              caught up.
its error decreases, if the other methods achieve greater error             As for the scikit-learn benchmark, we also evaluated the
reductions. Furthermore, we note that the ranks do not reflect           effect of using different values of t and plotted these in Figure
the magnitude of the difference between raw function values.             6. In contrast to the results for scikit-learn, for this benchmark
   As Figure 2 shows, the two variants of MI-SMAC per-                   it was better to use less configurations suggested by metaformed best, converging to similar ranks with larger function            learning. In both benchmarks, however, MI-SMBO yielded
evaluation budgets; and meta-learning yielded dramatically               substantial performance gains over SMBO during the first
better results for very small function evaluation budgets. We            function evaluations.
also note that even after 50 function evaluations no SMBO
method had fully caught up to the MI-SMBO results. This                       Related Work and Possible Extensions
indicates that meta-learning initialization provided not only            Existing work on using meta-learning for hyperparameter
good performance with few function evaluations but also a                optimization roughly follows two different directions of regood basis for SMAC to improve upon further.                             search. Firstly, Leite, Brazdil, and Vanschoren (2012) devel-
   To demonstrate the effect of varying the number of initial            oped Active Testing, a method similar to SMBO that reasons
configurations t selected by meta-learning, we plotted the               across datasets. In contrast to SMBO, Active Testing is a
ranks of different instantiations of MI-SMAC in Figure 3.                pure algorithm selection method which does not model the
We observe that within the range of t we studied MI-SMAC                 effect of hyperparameters (and algorithms) on the results
performs better with more initial configurations.                        and is limited to a finite number of algorithms. Secondly,
   To complement the above ranking analysis, Figure 4 (top)              meta-learning was used to initialize model-free hyperparamequantifies on how many datasets MI-SMAC with a learned                   ter optimization methods with configurations that previously
distance performed significantly better than the other methods           yielded good performance on similar datasets (Reif, Shafait,
according to a two-sided t-test, while Figure 4 (bottom) shows           and Dengel 2012; Gomes et al. 2012). While similar to our

                                                                  1133

Figure 4: Percentage of wins of MI-SMAC with an initial design of t = 20 configurations suggested by meta-learning using
the learned distance on all metafeatures. The upper plot shows the number of significant wins of MI-SMAC over competing
approaches according to the two-sided t-test while the lower plot shows the statistically significant losses.

Figure 5: Ranks of various optimizers averaged over all                Figure 6: Ranks of Spearmint and various MI-Spearmint
datasets for optimizing the SVM.                                       variants averaged over all datasets for optimizing the SVM.

work, these methods were limited by their search mechanism             Arbelaez, Hamadi, and Sebag 2010).
and did not improve the state of the art in hyperparameter
optimization.                                                                                 Conclusion
   There also exist first attempts to formalize SMBO across            We have presented a simple, yet effective, method for improvseveral datasets. These collaborative SMBO methods (Bar-               ing Sequential Model-based Bayesian Optimization (SMBO)
denet et al. 2013; Swersky, Snoek, and Adams 2013; Yo-                 by leveraging knowledge from previous optimization runs.
gatama and Mann 2014) address the knowledge transfer di-               Our method combines SMBO with configurations suggested
rectly in the SMBO procedure. However, to date they are                by a meta-learning procedure. It is agnostic of the actual
limited to small-scale problems with few continuous hyper-             SMBO method used and can thus be applied to the method
parameters and a handful of meta-features. In contrast to              best suited for a particular problem.
MI-SMBO they are dependent on the specific SMBO im-
                                                                          We demonstrated MI-SMBO’s efficacy by improving the
plementation and cannot be readily applied to off-the-shelf
                                                                       initialization of two SMBO methods on a collection of 57
hyperparameter optimizers.
                                                                       datasets. For a low-dimensional hyperparameter optimization
   Our method’s generality opens several avenues for future            problem, for small optimization budgets MI-Spearmint imwork. Here, we evaluated MI-SMBO on small and medium-                  proved upon the current state of the art algorithm Spearmint.
sized hyperparameter optimiziation problems, and an im-                For a large configuration space describing a CASH problem
portant open research question is to extend it to even larger          in scikit-learn, MI-SMAC substantially improved over the
configuration spaces, such as those of Auto-WEKA (Thorn-               current state of the art CASH algorithm SMAC (and all other
ton et al. 2013) and Hyperopt-Sklearn (Komer, Bergstra, and            tested optimizers), showing the potential of our approach
Eliasmith 2014). We also plan to extend collaborative SMBO             especially for large-scale hyperparameter optimization.
methods to overcome their limitation to small-scale problems. Finally, it would be interesting to extend our work to
general algorithm configuration (Hutter, Hoos, and Leyton-                               Acknowledgments.
Brown 2011) and to the life-long learning setting (Gagli-              This work was supported by the German Research Foundaolo and Schmidhuber 2005; Hutter and Hamadi 2005;                      tion (DFG) under grant HU 1900/3-1.

                                                                1134

                         References                                         Hutter, F.; Hoos, H. H.; and Leyton-Brown, K. 2011. Sequential
Arbelaez, A.; Hamadi, Y.; and Sebag, M. 2010. Continuous                    model-based optimization for general algorithm configuration.
search in constraint programming. In Proc. ICTAI, 53–60.                    In Proc. of LION-5, 507–523.
Bardenet, R.; Brendel, M.; Kégl, B.; and Sebag, M. 2013. Col-              Jones, D.; Schonlau, M.; and Welch, W. 1998. Efficient global
laborative hyperparameter tuning. In Proc. of ICML, 199–207.                optimization of expensive black box functions. Journal of
                                                                            Global Optimization 13:455–492.
Bergstra, J., and Bengio, Y. 2012. Random search for hyperparameter optimization. JMLR 13:281–305.                                    Kalousis, A. 2002. Algorithm Selection via Meta-Learning.
                                                                            University of Geneve, Department of Computer Science. Ph.D.
Bergstra, J.; Bardenet, R.; Bengio, Y.; and Kégl, B. 2011. Al-             Dissertation, University of Geneve.
gorithms for hyper-parameter optimization. In Proc. of NIPS,
2546–2554.                                                                  Komer, B.; Bergstra, J.; and Eliasmith, C. 2014. Hyperopt-
                                                                            sklearn: Automatic hyperparameter configuration for scikit-
Bergstra, J.; Yamins, D.; and Cox, D. D. 2013. Making a                     learn. In ICML workshop on AutoML.
science of model search: Hyperparameter optimization in hundreds of dimensions for vision architectures. In Proc. of ICML.             Leite, R.; Brazdil, P.; and Vanschoren, J. 2012. Selecting clas-
                                                                            sification algorithms with active testing. In Machine Learning
Brazdil, P.; Giraud-Carrier, C.; Soares, C.; and Vilalta, R. 2008.          and Data Mining in Pattern Recognition. Springer. 117–131.
Metalearning: Applications to Data Mining. Springer Publishing Company, Incorporated, 1 edition.                                       Michie, D.; Spiegelhalter, D. J.; Taylor, C. C.; and Campbell, J.,
                                                                            eds. 1994. Machine Learning, Neural and Statistical Classifi-
Breiman, L. 2001. Random forests. Machine Learning 45:5–                    cation. Ellis Horwood.
32.
                                                                            Pedregosa, F.; Varoquaux, G.; Gramfort, A.; Michel, V.;
Brochu, E.; Cora, V. M.; and de Freitas, N. 2010. A tutorial on             Thirion, B.; Grisel, O.; Blondel, M.; Prettenhofer, P.; Weiss,
Bayesian optimization of expensive cost functions, with appli-              R.; Dubourg, V.; Vanderplas, J.; Passos, A.; Cournapeau, D.;
cation to active user modeling and hierarchical reinforcement               Brucher, M.; Perrot, M.; and Duchesnay, E. 2011. Scikit-learn:
learning. CoRR abs/1012.2599.                                               Machine learning in Python. JMLR 12:2825–2830.
Dahl, G. E.; Sainath, T. N.; and Hinton, G. E. 2013. Improving              Pfahringer, B.; Bensusan, H.; and Giraud-Carrier, C. 2000.
deep neural networks for LVCSR using rectified linear units and             Meta-learning by landmarking various learning algorithms. In
dropout. In Proc. of ICASSP, 8609–8613.                                     Proc. of ICML, 743–750.
Eggensperger, K.; Feurer, M.; Hutter, F.; Bergstra, J.; Snoek, J.;          Rasmussen, C. E., and Williams, C. K. I. 2006. Gaussian Pro-
Hoos, H. H.; and Leyton-Brown, K. 2013. Towards an empiri-                  cesses for Machine Learning. The MIT Press.
cal foundation for assessing bayesian optimization of hyperpa-
                                                                            Reif, M.; Shafait, F.; and Dengel, A. 2012. Meta-learning
rameters. In NIPS workshop on Bayesian Optimization.
                                                                            for evolutionary parameter optimization of classifiers. Machine
Feurer, M.; Springenberg, T.; and Hutter, F. 2014. Using meta-              Learning 87:357–380.
learning to initialize bayesian optimization of hyperparameters.
                                                                            Snoek, J.; Larochelle, H.; and Adams, R. 2012. Practical
In ECAI workshop on Metalearning and Algorithm Selection
                                                                            bayesian optimization of machine learning algorithms. In Proc.
(MetaSel), 3–10.
                                                                            of NIPS, 2951–2959.
Gagliolo, M., and Schmidhuber, J. 2005. Towards life-long
                                                                            Soares, C., and Brazdil, P. 2000. Zoomed ranking: Selection
meta learning. Inductive Transfer : 10 Years Later — NIPS
                                                                            of classification algorithms based on relevant performance in-
2005 workshop.
                                                                            formation. In Proc. of PKDD’00. Springer. 126–135.
Gomes, T.; Prudêncio, R.; Soares, C.; Rossi, A.; and Carvalho,
                                                                            Swersky, K.; Snoek, J.; and Adams, R. 2013. Multi-task
A. 2012. Combining meta-learning and search techniques to
                                                                            bayesian optimization. In Proc. of NIPS, 2004–2012.
select parameters for support vector machines. Neurocomputing
75(1):3–12.                                                                 Thornton, C.; Hutter, F.; Hoos, H. H.; and Leyton-Brown, K.
                                                                            2013. Auto-WEKA: combined selection and hyperparameter
Goodfellow, I. J.; Warde-Farley, D.; Mirza, M.; Courville, A.;
                                                                            optimization of classification algorithms. In Proc. of KDD’13,
and Bengio, Y. 2013. Maxout networks. In Proc. of ICML,
                                                                            847–855.
1319–1327.
                                                                            Vanschoren, J.; van Rijn, J. N.; Bischl, B.; and Torgo, L. 2013.
Hall, M.; Frank, E.; Holmes, G.; Pfahringer, B.; Reutemann, P.;
                                                                            OpenML: Networked science in machine learning. SIGKDD
and Witten, I. 2009. The WEKA data mining software: an
                                                                            Explorations 15(2):49–60.
update. ACM SIGKDD Explorations Newsletter 11(1):10–18.
                                                                            Yogatama, D., and Mann, G. 2014. Efficient transfer learning
Hoffman, M.; Shahriari, B.; and de Freitas, N. 2014. On correla-            method for automatic hyperparameter tuning. In Proc. of AIStion and budget constraints in model-based bandit optimization              TATS, 1077–1085.
with application to automatic machine learning. In Proc. of 15th
AISTATS, volume 33, 365–374.
Hutter, F., and Hamadi, Y. 2005. Parameter adjustment based
on performance prediction: Towards an instance-aware problem solver. Technical Report MSR-TR-2005-125, Microsoft
Research, Cambridge, UK.
Hutter, F.; Xu, L.; Hoos, H. H.; and Leyton-Brown, K. 2014.
Algorithm runtime prediction: Methods and evaluation. JAIR
206(0):79 – 111.

                                                                     1135
