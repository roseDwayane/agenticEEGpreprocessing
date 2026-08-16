---
citation_key: "RijnHutter2017"
title: "Hyperparameter Importance Across Datasets"
authors: "J. V. Rijn; Frank Hutter"
year: 2017
doi: "10.1145/3219819.3220058"
source: "arXiv (1710.04725)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "1710.04725"
conversion: "pdftotext -layout (automated)"
---

# Hyperparameter Importance Across Datasets

Hyperparameter Importance Across Datasets
                                                                           Jan N. van Rijn                                                              Frank Hutter
                                                              Albert-Ludwigs-Universität Freiburg                                           Albert-Ludwigs-Universität Freiburg
                                                                      Freiburg, Germany                                                              Freiburg, Germany
                                                                  vanrijn@cs.uni-freiburg.de                                                       fh@cs.uni-freiburg.de

                                           ABSTRACT                                                                                  Based on these methods, it is now possible to build reliable auto-
                                           With the advent of automated machine learning, automated hyper-                        matic machine learning (AutoML) systems [12, 39], which – given a
                                           parameter optimization methods are by now routinely used in data                       new dataset D – determine a custom combination of algorithm and

arXiv:1710.04725v2 [stat.ML] 29 May 2018
                                           mining. However, this progress is not yet matched by equal progress                    hyperparameters that performs well on D. However, this recent
                                           on automatic analyses that yield information beyond performance-                       rapid progress in hyperparameter optimization and AutoML carries
                                           optimizing hyperparameter settings. In this work, we aim to answer                     a risk with it: if researchers and practitioners rely exclusively on
                                           the following two questions: Given an algorithm, what are generally                    automated methods for finding performance-optimizing configu-
                                           its most important hyperparameters, and what are typically good                        rations, they do not obtain any intuition or information beyond
                                           values for these? We present methodology and a framework to an-                        the single configuration chosen. To still provide such intuition in
                                           swer these questions based on meta-learning across many datasets.                      the age of automation, we advocate the development of automated
                                           We apply this methodology using the experimental meta-data avail-                      methods that provide high-level insights into an algorithm’s hyper-
                                           able on OpenML to determine the most important hyperparameters                         parameters, based on a wide range of datasets.
                                           of support vector machines, random forests and Adaboost, and to                           When using a new algorithm on a given dataset, it is typically a
                                           infer priors for all their hyperparameters. The results, obtained                      priori unknown which hyperparameters should be tuned, what are
                                           fully automatically, provide a quantitative basis to focus efforts in                  good ranges for these, and which values in these ranges are most
                                           both manual algorithm design and in automated hyperparameter                           likely to yield high performance. Currently these decisions are typi-
                                           optimization. The conducted experiments confirm that the hyper-                        cally made based on a combination of intuition about the algorithm
                                           parameters selected by the proposed method are indeed the most                         and trial & error. While various post-hoc analysis techniques exist
                                           important ones and that the obtained priors also lead to statistically                 that, for a given dataset and algorithm, determine what were the
                                           significant improvements in hyperparameter optimization.                               most important hyperparameters and which of their values tended
                                                                                                                                  to yield good performance, in this work we study the same ques-
                                           CCS CONCEPTS                                                                           tion across many datasets. For many well-known algorithms, there
                                                                                                                                  already exists some intuition about which hyperparameters impact
                                           • Computing methodologies → Supervised learning by classifi-
                                                                                                                                  performance most. For example, for support vector machines, it is
                                           cation; Batch learning;
                                                                                                                                  commonly believed that the gamma and complexity hyperparame-
                                                                                                                                  ters are most important, and that a certain trade-off exists between
                                           KEYWORDS
                                                                                                                                  these two. However, the empirical evidence for this is limited to a
                                           Hyperparameter Optimization; Hyperparameter Importance; meta-                          few datasets and therefore rather anecdotal.
                                           learning                                                                                  In this work, given an algorithm, we aim to answer the following
                                           ACM Reference Format:                                                                  two questions:
                                           Jan N. van Rijn and Frank Hutter. 2018. Hyperparameter Importance Across
                                           Datasets. In KDD ’18: The 24th ACM SIGKDD International Conference on
                                                                                                                                     (1) Which of the algorithm’s hyperparameters matter most for
                                           Knowledge Discovery & Data Mining, August 19–23, 2018, London, United
                                           Kingdom. ACM, New York, NY, USA, 10 pages. https://doi.org/10.1145/
                                                                                                                                         empirical performance?
                                           3219819.3220058                                                                           (2) Which values of these hyperparameters are likely to yield
                                                                                                                                         high performance?
                                           1    INTRODUCTION
                                           The performance of modern machine learning and data mining                             We will introduce methods to answer these questions across datasets
                                           methods highly depends on their hyperparameter settings. As a                          and demonstrate these methods for three commonly used classi-
                                           consequence, there has been a lot of recent work and progress                          fiers: support vector machines (SVMs), random forests and Ad-
                                           on hyperparameter optimization, with methods including random                          aboost. Specifically, we apply the post-hoc analysis technique of
                                           search [2], Bayesian optimization [1, 20, 26, 34, 38], evolutionary                    functional ANOVA [22] to each of the aforementioned classifiers
                                           optimization [29], meta-learning [6, 16, 30, 32, 40, 41] and bandit-                   on a wide range of datasets, drawing on the experimental data
                                           based methods [23, 28].                                                                available on OpenML [43]. Using the same available experimental
                                                                                                                                  data, we also infer prior distributions over which hyperparameter
                                           © 2018 Copyright held by the owner/author(s). Publication rights licensed to the       values work well. Several experiments demonstrate that the trends
                                           Association for Computing Machinery. This is the author’s version of the work. It
                                           is posted here for your personal use, not for redistribution. The definitive Version
                                                                                                                                  we find (about which hyperparameters tend to be important and
                                           of Record was published in Proceedings of the 24th ACM SIGKDD International            which values tend to perform well) generalize to new datasets.
                                           Conference on Knowledge Discovery and Data Mining.                                        Our contributions are as follows:
                                           https://doi.org/10.1145/3219819.3220058

KDD ’18, August 19–23, 2018, London, United Kingdom                                                             Jan N. van Rijn and Frank Hutter

    (1) We present a methodology and a framework that leverage               In a preliminary study, we already reported on important hyper-
        functional ANOVA to study hyperparameter importance                parameters of random forests and Adaboost [42].
        across datasets.
                                                                               Priors. The field of meta-learning (e.g., Brazdil et al. [6]) is im-
    (2) We apply this to analyze the importance of SVMs, random
                                                                           plicitly based on priors: a model is trained on data characteris-
        forests and Adaboost on 100 datasets from OpenML, and
                                                                           tics (so-called meta-features) and performance data from similar
        confirm that the hyperparameters determined as the most
                                                                           datasets, and the resulting predictions are used to recommend
        important ones indeed are the most important ones to opti-
                                                                           a configuration for the dataset at hand. These techniques have
        mize.
                                                                           been successfully used to recommend good hyperparameter set-
    (3) Using the same experimental data, we infer priors over which
                                                                           tings [30, 35], to warm-start optimization procedures [13] or prune
        values of these hyperparameters perform well and confirm
                                                                           search spaces [44]. However, it is hard to select an adequate set of
        that these priors yield statistically significant improvements
                                                                           meta-features. Moreover, obtaining good meta-features comes at
        for a modern hyperparameter optimization method.
                                                                           the cost of run time. This work can be seen as an alternative ap-
    (4) In order to make this study reproducible, all experimental
                                                                           proach to meta-learning that does not require the aforementioned
        data is made available on OpenML. The results of all analyses
                                                                           meta-features.
        are available in a separate Jupyter Notebook.
                                                                               Multi-task Bayesian optimization [38] offers a different approach
    (5) Overall, this work is the first to provide quantitative evidence
                                                                           to meta-learning that alleviates meta-features. A multi-task model
        for which hyperparameters are important and which values
                                                                           (typically a Gaussian Process [5]) is fitted on the outcome of clas-
        should be considered, providing a better scientific basis for
                                                                           sifiers to determine correlations between tasks, which can be ex-
        the field than previous knowledge based mainly on intuition.
                                                                           ploited for hyperparameter optimization on a new task. However,
   The remainder of this paper is organized as follows. In Section 2       this approach suffers from the cubic complexity of Gaussian prowe position our contributions with respect to similar works in the         cesses. While a recent more scalable alternative for multi-task
field. Section 3 covers relevant background information about func-        Bayesian optimization is to use Bayesian neural networks [37],
tional ANOVA. Section 4 formally introduces the methods that we            to the best of our knowledge, this approach has not been evaluated
propose, and Section 5 defines the algorithms and hyperparameters          at large scale yet.
upon which we apply them. We then conduct two experiments:                     The class of Estimation of Distribution (EDA) algorithms (e.g. Lar-
Section 6 covers the experiments that show which hyperparameters           raanaga and Lozano [27]) optimizes a given function by iteratively
are important across datasets; and Section 7 covers the experiments        fitting a probability distribution to points in the input space with
that show how to use the experimental data on OpenML to infer              high performance and using this probability distribution as a prior
good priors. Section 8 concludes.                                          to sample new points from. Drawing on this, the method we pro-
                                                                           pose determines priors over good hyperparameter values by using
2    RELATED WORK                                                          hyperparameter performance data on different datasets.
We review related work on hyperparameter importance and priors.
                                                                           3    BACKGROUND: FUNCTIONAL ANOVA
   Hyperparameter Importance. Various techniques exists that               The functional ANOVA framework for analyzing the importance
allow for the assessment of hyperparameter importance. Breiman [7]         of hyperparameters introduced by Hutter et al. [22] is based on a
showed in his seminal paper how random forests can be used to as-          regression model that yields predictions ŷ for the performance of
sess attribute importance: if removing an attribute from the dataset       arbitrary hyperparameter settings. It determines how much each
yields a drop in performance, this is an indication that the attribute     hyperparameter (and each combination of hyperparameters) conwas important. Forward selection [21] is based on this principle. It       tributes to the variance of ŷ across the algorithm’s hyperparameter
predicts the performance of a classifier based on a subset of hyper-       space Θ. Since we will use this technique as part of the proposed
parameters that is initialized empty and greedily filled with the next     method, we now discuss it in more detail.
most important hyperparameter. Ablation Analysis [3, 11] requires
                                                                               Notation. Algorithm A has n hyperparameters with domains
a default setting and an optimized setting and calculates a so-called
                                                                           Θ1 , . . . , Θn and configuration space Θ = Θ1 × . . . × Θn . Let N =
ablation trace, which embodies how much the hyperparameters
                                                                           {1, . . . , n} be the set of all hyperparameters of A. An instantiation
contributed towards the difference in performance between the
                                                                           of A is a vector θ = ⟨θ 1 , . . . , θ n ⟩ with θ i ∈ Θi (this is also called
two settings. Functional ANOVA (as explained in detail in the next
                                                                           a configuration of A). A partial instantiation of A is a vector θ U =
section) is a powerful framework that can detect the importance of
                                                                           ⟨θ i , . . . , θ j ⟩ with a subset U ⊆ N of the hyperparameters fixed, and
both individual hyperparameters and interaction effects between ar-
                                                                           the values for other hyperparameters unspecified. (Note that from
bitrary subsets of hyperparameters. Although all of these methods
                                                                           this it follows that θ N = θ ).
are very useful in their own right, none of these has yet been applied to analyze hyperparameters across datasets. We will base our            Efficient marginal predictions. The marginal performance
work in this realm on functional ANOVA since it is computationally         âU (θ U ) is defined as the average performance of all complete infar more efficient than forward selection, can detect interaction          stantiations θ that agree with θ U in the instantiations of hypereffects, and (unlike ablation analysis) does not rely on a specific        parameters U . To illustrate the concept of marginal predictions,
default configuration. The proposed methods are, however, by no            Figure 1 shows marginal predictions for two hyperparameters of
means limited to functional ANOVA.                                         SVMs and their union. We note that such marginals average over all

Hyperparameter Importance Across Datasets                                                 KDD ’18, August 19–23, 2018, London, United Kingdom

                    (a) Gamma                                       (b) Complexity                                (c) Gamma vs. Complexity

Figure 1: Marginal predictions for a SVM with RBF kernel on the letter dataset. The hyperparameter values are on a log scale.

instantiations of the hyperparameters not in U , and as such depend           models, this is an efficient operation requiring only seconds in the
on a very large number of terms (even for finite hyperparameter               experiments for this paper. Overall, based on the performance data
ranges, this number of terms is exponential in the remaining num-                        K , functional ANOVA thus provides us with the relative
                                                                              ⟨θ i , yi ⟩k=1
ber of hyperparameters N \ U ). However, for the predictions ŷ of a          variance contributions of each individual hyperparameter (with the
tree-based model, the average over these terms can be computed                relative variance contributions of all subsets of hyperparameters
exactly by a procedure that is linear in the number of leaves in the          summing to one).
model [22].                                                                       This leads to the notion of hyperparameter importance. When a
                                                                              hyperparameter is responsible for a large fraction of the variance,
  Functional ANOVA.. Functional ANOVA [18, 19, 25, 36] de-
                                                                              setting this hyperparameter correctly is important for obtaining
composes a function ŷ : Θ1 × · · · × Θn → R into additive compo-
                                                                              good performance, and it should be tuned properly. When a hypernents that only depend on subsets of the hyperparameters N :
                               Õ                                              parameter is not responsible for a lot of variance, it is deemed less
                      ŷ(θ ) =        fˆU (θ U )              (1)             important.
                                U ⊆N                                              Besides attributing the variance to single hyperparameters, func-
The components fˆU (θ U ) are defined as follows:                             tional ANOVA also determines the interaction effects of sets of
                   (                                                          hyperparameters. This potentially gives insights in which hyperpa-
        ˆ            fˆ∅                          if U = ∅.                   rameters can be tuned independently and which are dependent on
       fU (θ U ) =                                                  (2)
                    âU (θ U ) − W ⊊U fˆW (θ W ) otherwise,
                                Í
                                                                              each other and should thus be tuned together. In the hypothetical
                                                                              case where there are no interaction effects between any of the hywhere the constant fˆ∅ is the mean value of the function over its             perparameters, all hyperparameters could be tuned individually by
domain. Our main interest is the result of the unary functions                means of a simple hill-climbing algorithm.
fˆ{j } (θ {j } ), which capture the effect of varying hyperparameter j,           By design, functional ANOVA operates on the result of a single
averaging across all possible values of all other hyperparameters.            hyperparameter optimization procedure on a single dataset. This
Additionally, the functions fˆU (θ U ) for |U | > 1 capture the interac-      leaves room for questions, such as: (i) Which hyperparameters are
tion effects between all variables in U (excluding effects of subsets         important in general? (ii) Are the same hyperparameters often im-
W ⊊ U ).                                                                      portant, or does this vary per dataset? (iii) Given a new dataset, on
    Given the individual components, functional ANOVA decom-                  which a hyperparameter procedure is to be ran, which hyperpaposes the variance V of ŷ into the contributions by all subsets of           rameters should be optimized and what are sensible ranges? We
hyperparameters VU :                                                          will investigate these questions in the next section.
                                               ∫
              Õ                           1
     V=            VU , with VU =                 fˆU (θ U )2dθ U ,  (3)
                                       ||ΘU ||
         U ⊂N
                                                                              4      METHODS
where | |Θ1 | | is the probability density of the uniform distribution        We address the following problem. Given
          U
across ΘU .
    To apply functional ANOVA, we first collect performance data
           K that captures the performance y (e.g., accuracy or AUC                  • an algorithm with configuration space Θ
⟨θ i , yi ⟩k=1                               i
                                                                                     • a large number of datasets D (1) , . . . , D (M ) , with M being
score) of an algorithm A with hyperparameter settings θ i . We then
                                                                                       the number of datasets
fit a random forest model to this data and use functional ANOVA to
                                                                                     • for each of the datasets, a set of empirical performance meadecompose the variance of each of the forest’s trees ŷ into contribu-                                      K for different hyperparameter settings
                                                                                       surements ⟨θ i , yi ⟩i=1
tions due to each subset of hyperparameters. Importantly, based on
                                                                                       θ i ∈ Θ,
the fast prediction of marginal performance available for tree-based

KDD ’18, August 19–23, 2018, London, United Kingdom                                                             Jan N. van Rijn and Frank Hutter

we aim to determine which hyperparameters affect the algorithm’s                Formally, for each hyperparameter θ j we measure y ∗j, f as the
empirical performance most, and which values are likely to yield            result of a random search for maximizing accuracy, fixing θ j to a
good performance.                                                           given value f ∈ F j . (For categorical θ j with domain Θj , we used
                                                                            F j = Θj ; for numeric θ j , we set F j to a set of k = 10 values spread
                                                                            uniformly over θ j ’s range.) We then compute y ∗j = |F1 | f ∈F j y ∗j, f ,
                                                                                                                                         Í
4.1    Important Hyperparameters                                                                                                       j

Current knowledge about hyperparameter importance is mainly                 representing the score when not optimizing hyperparameter θ j ,
based on a combination of intuition, own experience and folklore            averaged over fixing θ j to various values it can take. Hyperparamknowledge. To instead provide a data-driven quantitative basis for          eters with lower y ∗j are then judged to be more important, since
this knowledge, in this section we introduce methodology for deter-         performance deteriorates more when they are set sub-optimally.
mining which hyperparameters are generally important, measured
across datasets.                                                            4.2    Priors for Good Hyperparameter Values
                                                                            Knowing what are the important hyperparameters, an obvious
   Determining Important Hyperparameters. For a given al-                   next question is what are good values for these hyperparameters.
                                                                      K
gorithm A and a given dataset, we use the performance data ⟨θ i , yi ⟩i=1   These values can be used to define defaults, or to sample from in
collected for A on this dataset to fit functional ANOVA’s random            hyperparameter optimization.
forests to. Functional ANOVA then returns the variance contribution Vj /V of every hyperparameter j ∈ N , with high values                   Determining Useful Priors. We aim to build priors based on
indicating high importance. We then study the distribution of these         the performance data observed across datasets. There are several
variance contributions across datasets to obtain empirical data re-         existing methods for achieving this on a single dataset that we
garding which hyperparameters tend to be most important.                    drew inspiration from. In hyperparameter optimization, the Tree-
   It is possible that a given set of hyperparameters is responsible        structured Parzen Estimator (TPE) by Bergstra et al. [1] keeps track
for a high variance on many datasets, but the best performance is           of an algorithm’s best observed hyperparameter configurations
typically achieved with the same set of values. We note that this           Θbest on a given dataset and for each hyperparameter fits a 1method will flag such hyperparameters as important, although it             dimensional Parzen Estimator to the values it took in Θbest . Simicould be argued that they have appropriate defaults and do not              larly, as an analysis technique to study which values of a hyperpaneed to be tuned. Whether this is the case can be determined by             rameter perform well, Loshchilov and Hutter [29] proposed to fit
various procedures, for example the one introduced in Section 4.2.          kernel density estimators (see, e.g., [33]) to these values. Here, we
   For some datasets, the measured performance values yi are con-           follow this latter procedure, but instead of using the hyperparamestant, indicating that none of the hyperparameters are important;           ter configurations that performed well on a given dataset, we used
we therefore removed these datasets from the respective experi-             the top n configurations observed for each of the datasets; in our
ments.                                                                      experiments, we set n = 10. We only used 1-dimensional density
                                                                            estimators in this work, because the amount of data required to
    Verification. Functional ANOVA uses a mathematically clearly            adequately fit these is known to be reasonable. We note that this is
defined quantity (Vj /V) to define a hyperparameter’s importance,           merely one possible choice, and that future work could focus on
but it is important to verify whether this agrees with other, poten-        fitting other types of distributions to this data.
tially more intuitive, notions of hyperparameter importance. To
confirm the results of functional ANOVA, we therefore propose to               Verification. As in the case of hyperparameter importance, we
verify in an expensive, post-hoc analysis to what extent its results        propose an expensive post-hoc analysis to verify whether the priors
align with an intuitive notion of how important a hyperparameter            over good hyperparameter values identified above are useful and
is in hyperparameter optimization.                                          generalize across datasets. Specifically, as a quantifiable notion of
    One intuitive way to measure the importance of a hyperparame-           usefulness, we propose to evaluate the impact of using the prior
ter θ is to assess the performance obtained in an optimization pro-         distributions defined above within a hyperparameter optimization
cess that leaves θ fixed. However, similar to ablation analysis [11],       procedure. For this, we use the popular bandit-based hyperparamethe outcome of this approach depends strongly on the value that θ           ter optimization method Hyperband [28]. Hyperband is based on
is fixed to; e.g., fixing a very important hyperparameter to a good         the procedure of successive halving [23], which evaluates a large
default value would result in labelling it not important (this in-          number of randomly-chosen configurations using only a small buddeed happened in various cases in our preliminary experiments).             get, and iteratively increases this budget, at each step only retaining
To avoid this problem and to instead quantify the importance of             a fraction of configurations that are best so far. For each dataset,
setting θ to a good value in its range, we perform k runs of the            we propose to run two versions of this optimization procedure:
optimization process of all hyperparameters but θ , each time fixing        one sampling uniformly from the hyperparameter space and one
θ to a different value spread uniformly over its range; in the end,         sampling from the obtained priors. If the priors are indeed useful
we average the results of these k runs. Leaving out an important            and generalize across datasets, the optimizer that uses them should
hyperparameter θ is then expected to yield worse results than leav-         obtain better results on the majority of the datasets. Of course, for
ing out an unimportant hyperparameter θ ′ . As a hyperparameter             each dataset on which this experiment is performed, the priors
optimization procedure for this verification procedure, we simply           should be obtained on empirical performance data that was not
use random search, to avoid any biases.                                     obtained from this dataset.

Hyperparameter Importance Across Datasets                                              KDD ’18, August 19–23, 2018, London, United Kingdom

   We note that – due to differences between datasets – there are            For each of these, to not incur any bias from our choice of hybound to be datasets for which using priors from other datasets           perparameters and ranges, we used exactly the same hyperparamdeteriorates performance. However, since human engineers have             eters and ranges as the automatic machine learning system Autosuccessfully used prior knowledge to define typical ranges to con-        sklearn [12].1 The hyperparameters, ranges and scales are listed in
sider, our hypothesis is that the data-driven priors will improve the     Tables 1–3.
optimization procedure’s results on most datasets.
                                                                              Preprocessing. We used the same data preprocessing steps for
                                                                          all algorithms. Missing values are imputed (categorical features
                                                                          with the mode; for numerical features, the imputation strategy was
4.3    Algorithm Performance Data                                         one of the hyperparameters), categorical hyperparameters are one-
The proposed methods do not crucially rely on how exactly the             hot-encoded, and constant features are removed. As support vector
training performance data was obtained. We note, however, that for        machine’s are sensitive to the scale of the input variables, the input
all training datasets the data should be gathered with a wide range       variables for the SVM’s are scaled to have unit variance. Of course,
of hyperparameter configurations (to allow the construction of            all these operations are performed based on information obtained
predictive performance models for functional ANOVA) and should            from the training data.
contain close-to-optimal configurations (to allow the construction
of good priors).                                                             Datasets. We performed all experiments on the datasets from
    We note that for many common algorithms, the open machine             the OpenML100 [4]. The OpenML100 is a curated benchmark suite,
learning environment OpenML [43] already contains very compre-            containing 100 datasets from various domains. The datasets contain
hensive performance data for different hyperparameter configu-            between 500 and 100,000 data points, are generally well-balanced
rations on a wide range of datasets. OpenML also defines curated          and are all linked to a scientific publication. These criteria ensure
benchmarking suites, such as the OpenML100 [4]. We therefore              that the datasets pose a challenging and meaningful classification
believe that the proposed methods can in principle be used directly       task, and the results are comparable to earlier studies.
on top of OpenML to automatically provide and refine insights as
more data becomes available.                                              6     HYPERPARAMETER IMPORTANCE
    In our experiments, which involve classifiers with up to six hy-      We now discuss the results of the experiment for determining the
perparameters, we indeed used data from OpenML. We ensured that           most important hyperparameters per classifier. All together, this
for each dataset at least 150 runs with different hyperparameters         analysis is based on the performance data of 250,195 algorithm
were available to make functional ANOVA’s model reliable enough.          runs over the 100 datasets using 3,184 CPU days to generate. All
We generated additional runs for classifiers that did not meet this re-   performance data we used is publicly available on OpenML2 .
quirement by executing random configurations on a large compute              We show the results for each classifier as a set of three figures.
cluster. We note that for larger hyperparameter spaces, more sophis-      The top figure (e.g., Figure 2(a)) shows violinplots of each hyperticated data gathering strategies are likely required to accurately       parameter’s variance contribution, across all datasets. The x-axis
model the performance of the best configurations.                         shows the hyperparameter j under investigation, and each data
                                                                          point represents Vj /V for one dataset; a high value implies that
                                                                          this hyperparameter accounted for a large fraction of variance on
4.4    Computational Complexity of Analysis                               this dataset, and therefore would account for high accuracy-loss if
       Techniques                                                         not set properly. We also show for each classifier the three most
                                                                          important interaction effects between groups of hyperparameters.
While we also propose the use of expensive, post-hoc verification            The middle figure (e.g., Figure 2(b)) shows the results of the
methods to confirm the results of our analysis, we would like to          verification experiment. It shows the average rank of each run of
emphasize that the proposed analysis techniques themselves are            random search, labeled with the hyperparameter whose value was
computationally very efficient. Their complexity is dominated by          fixed to a default value. A high rank implies poor performance
the cost of fitting functional ANOVA’s random forest to the per-          compared to the other configurations, meaning that tuning this
formance data observed for each of the datasets. The cost of the          hyperparameter would have been important.
remainder of functional ANOVA, and of fitting the Gaussian kernel            The bottom figure (e.g., Figure 2(c)) shows the result of a Nedensity estimator is negligible. In the experiments we conducted,         menyi test over the average ranks of the hyperparameters (for
given an algorithm’s performance data, performing the analyses            details, see [10]). A statistically significant difference was measured
required only a few seconds.                                              for every pair of classifiers that are not connected by the hori-
                                                                          zontal black line. The interaction effects are left out to meet the
                                                                          independent input assumptions of the Nemenyi test.
5     ALGORITHMS AND HYPERPARAMETERS
                                                                             SVM Results. We analyze SVMs with RBF and sigmoid kernels
We analyze the hyperparameters of three classifiers implemented
                                                                          in Figures 2 and 3, respectively.
in scikit-learn [8, 31]: random forests [7], Adaboost (using decision
trees as base-classifier) [14] and SVMs [9]. The SVMs are analysed        1 There was one exception: For technical reasons, in random forests, we modelled the
with two different kernel types: radial basis function (RBF) and          maximal number of features for a split as a fraction of the number of available features
sigmoid.                                                                  (with range [0.1, 0.9]).
                                                                          2 Full details: https://www.openml.org/s/71

KDD ’18, August 19–23, 2018, London, United Kingdom                                                            Jan N. van Rijn and Frank Hutter

                                                       Table 1: SVM Hyperparameters.

  hyperparameter        values                        description
  complexity            [2−5 , 215 ] (log-scale)      Soft-margin constant, controlling the trade-off between model simplicity and model fit.
  coef0                 [−1, 1]                       Additional coefficient used by the kernel (sigmoid kernel only).
  gamma                 [2−15 , 23 ] (log-scale)      Length-scale of the kernel function, determining its locality.
  imputation            {mean, median, mode}          Strategy for imputing missing numeric variables.
  shrinking             {true, false}                 Determines whether to use the shrinking heuristic (introduced in [24]).
  tolerance             [10−5 , 10−1 ] (log-scale)    Determines the tolerance for the stopping criterion.

                                               Table 2: Random Forest Hyperparameters.

  hyperparameter        values                        description
  bootstrap             {true, false}                 Whether to train on bootstrap samples or on the full train set.
  max. features         [0.1, 0.9]                    Fraction of random features sampled per node.
  min. samples leaf     [1, 20]                       The minimal number of data points required in order to create a leaf.
  min. samples split    [2, 20]                       The minimal number of data points required to split an internal node.
  imputation            {mean, median, mode}          Strategy for imputing missing numeric variables.
  split criterion       {entropy, gini}               Function to determine the quality of a possible split.

                                                     Table 3: Adaboost Hyperparameters.

  hyperparameter        values                        description
  algorithm             {SAMME, SAMME.R}              Determines which boosting algorithm to use.
  imputation            {mean, median, mode}          Strategy for imputing missing numeric variables.
  iterations            [50, 500]                     Number of estimators to build.
  learning rate         [0.01, 2.0] (log-scale)       Learning rate shrinks the contribution of each classifier.
  max. depth            [1, 10]                       The maximal depth of the decision trees.

   The results show a clear picture: The most important hyperpa-            were most important. Both of these hyperparameters were signifirameter to tune in both cases was gamma, followed by complexity.            cantly more important than the others according to the Nemenyi
Both of these hyperparameters were significantly more important             test. Only in a few cases, bootstrap was the most important hythan the others according to the Nemenyi test. This conclusion is           perparameter (datasets ‘balance-scale’, ‘credit-a’, ‘kc1’, ‘Australian’,
supported by the random search experiment: not optimizing the               ‘profb’ and ‘climate-model-simulation-crashes’) and the split critegamma parameter obtained the worst performance, making it the               rion only once (dataset ‘scene’). Again, the results from functional
most important hyperparameter, followed by the complexity hy-               ANOVA agree with the results from the random search experiperparameter. Interestingly, according to Figure 3(a), when using           ment and our intuition. It is well-known that ensembles perform
the sigmoid kernel, the interaction effect between gamma and com-           well when two conditions are met [7, 17]: (i) the individual models
plexity was even more important than the complexity parameter               perform better than random guessing, and (ii) the errors of the inby itself.                                                                  dividual models are uncorrelated. Both hyperparameters influence
   We note that while it is well-known that gamma and complexity            the variance among trees, uncorrelating their predictions.
are important SVM hyperparameters, to the best of our knowledge,               At first sight, the minimal samples per split and minimal samples
this is the first study that provides systematic empirical evidence         per leaf hyperparameters seem quite similar, but at closer inspection
for their importance on a wide range of datasets. The fact that the         they are not: logically, minimal samples per split is overshadowed
proposed methods recovered these known most important hyper-                by minimal samples per leaf.
parameters also acts as additional verification that the proposed
methodology works as expected. The least important hyperparam-                 Adaboost Results. Figure 5 shows the results for Adaboost.
eter for the accuracy of SVMs was whether to use the shrinking              Again, most of the variance can be explained by a small set of hyheuristic. As this heuristic is intended to decrease computational          perparameters, in this case the maximal depth of the decision tree
resources rather than improve predictive performance, our data              and, to a lesser degree, the learning rate. Both of these hyperparamsuggests that it is safe to enable this feature.                            eters were significantly more important than the others according
   Random Forest Results. Figure 4 shows the results for ran-               to the Nemenyi test. There were only a few exceptions, in which
dom forests. The results reveal that most of the variance could be          the boosting algorithm was the most important hyperparameter
attributed to a small set of hyperparameters: the minimum samples           (datasets ‘madelon’, ‘diabetes’ and ‘hill-valey’). The results were
per leaf and maximal number of features for determining the split           again confirmed by the verification experiment.

Hyperparameter Importance Across Datasets                                                                                                                                  KDD ’18, August 19–23, 2018, London, United Kingdom

                                     1.00                                                                                                                                           1.00

             Variance Contribution
                                     0.75

                                                                                                                                                            Variance Contribution
                                                                                                                        ●
                                                                                                                                                                                    0.75

                                     0.50                                                                                                                                           0.50
                                                                                                                   ●

                                                                                                                                                                                                                                                                              ●

                                                                                                                                                                                                                                                                              ●
                                                                                                                                                                                                                                                                              ●

                                                                                                                                                                                                                                                                 ●            ●
                                                                                                                                                                                                                                                                              ●
                                                                                                                                                                                                                                                                 ●

                                     0.25                                                             ●
                                                                                                                                                                                    0.25                               ●
                                                                                                                                                                                                                                 ●
                                                                                                                                                                                                                                                         ●       ●
                                                                                                                                                                                                                                                                 ●

                                                                          ●                                                                                                                                                      ●                               ●

                                                                                                      ●                                                                                                                                                  ●
                                                                                                                                                                                                                                                                 ●
                                                                                                                                                                                                                                                                 ●
                                                                                                                                                                                                                                                                 ●
                                                                                                                                                                                                                                                                 ●
                                                                                                                                                                                                                                            ●            ●
                                                                                                                                                                                                                       ●
                                                                                                                                                                                                                                 ●
                                                                                                                                                                                                                       ●
                                                                                                      ●                                                                                                                                     ●
                                                                                                                                                                                                                                            ●            ●
                                                                                                      ●                                                                                                                                                  ●
                                                                                                                                                                                                                                 ●          ●
                                                                                                                                                                                                                                            ●
                                                                                                      ●
                                                                                                      ●                                                                                                                          ●                       ●
                                                                                      ●                                                                                                                                                     ●
                                                                                      ●
                                                                                      ●               ●                                                                                                                ●         ●
                                                                                                                                                                                                                                 ●          ●
                                                                          ●                           ●                                                                                                                          ●
                                                                                      ●                                                                                                                                                     ●
                                                                                      ●               ●                                                                                                                ●         ●
                                                                                                                                                                                                                                 ●
                                                                          ●
                                                                          ●                                                                                                                                            ●
                                                                          ●                                                                                                                                            ●
                                                                                                                                                                                                                       ●
                                                                                                                                                                                                                       ●
                                                                                                                                                                                                                       ●

                                     0.00
                                                                 ●
                                                                 ●

                                                                                                                                                                                    0.00
                                                  ●
                                                  ●              ●                                                                                                                                           ●
                                                                                                                                                                                               ●
                                                                                                                                                                                               ●             ●
                                                                                                                                                                                                             ●

                                             ki
                                                  g         tio n         l
                                                                         to        to l           to  l
                                                                                                               m   a   C          m   a                                                        g             n         l
                                                                                                                                                                                                                      to     ef          to l          a         C            a              a
                                                n                                a/              a/        /g                   ga                                                         ki n         tio                    0                    m                     m              m
                                            rin            ta
                                                                                                             am                   m                                                   sh                ta                  co         a/       /g                   /g               ga
                                        sh                pu                  m               m                                                                                          rin           pu                            m            am                   am                m
                                                      im                  /g              ga              C                                                                                        im                            ga
                                                                            am               m                                                                                                                                     m     co
                                                                                                                                                                                                                                                0                    C
                                                                         C                                                                                                                                                                 ef

                                                          (a) Marginal contribution per dataset                                                                                                         (a) Marginal contribution per dataset

                                                                                                                            complexity                                                                                                                                            coef0
                                                                                                                            gamma                                              5.5                                                                                                complexity
                                4.5                                                                                         imputation                                                                                                                                            gamma
                                                                                                                            shrinking                                          5.0                                                                                                imputation
                                4.0                                                                                         tolerance                                                                                                                                             shrinking
                                                                                                                                                                                                                                                                                  tolerance
                                                                                                                                                                               4.5

       Average Rank                                                                                                                                   Average Rank
                                3.5                                                                                                                                            4.0

                                3.0                                                                                                                                            3.5

                                                                                                                                                                               3.0
                                2.5
                                                                                                                                                                               2.5
                                2.0
                                                                                                                                                                               2.0
                                1.5
                                        0                       10            20                          30           40                 50                                           0                     10               20                    30                   40                      50
                                                                         Number of Iterations                                                                                                                              Number of Iterations
                                       (b) Random Search, excluding one parameter at a time                                                                                           (b) Random Search, excluding one parameter at a time

                                                           CD                                                                                                                                           CD

                                                      1              2                    3                    4            5                                                                      1              2              3              4            5                    6

                           gamma                                                                                                 shrinking
                                                                                                                                                                         gamma                                                                                                        shrinking
                       complexity                                                                                                imputation
                                                                                                                                                                      complexity                                                                                                      imputation
                         tolerance
                                                                                                                                                                           coef0                                                                                                      tolerance

                                            (c) Ranked hyperparameter importance, α = 0.05.
                                                                                                                                                                                           (c) Ranked hyperparameter importance, α = 0.05.

                                                          Figure 2: SVM (RBF kernel).
                                                                                                                                                                                               Figure 3: SVM (sigmoid kernel).

   One interesting observation is that, in contrast to other ensemble                                                                          ‘KDDCup09 upselling’, ‘sick’ and ‘profb’, all of which have many
techniques, the number of iterations did not seem to influence                                                                                 missing values. Imputation is clearly important (as classifiers do
performance too much. The minimum value (50) appears to already                                                                                not function on undefined data), but which strategy to use for the
be large enough to ensure good performance, and increasing it does                                                                             imputation does not matter much according to the data.
not lead to significantly better results.                                                                                                          We note that the results presented in this section do by no means
   General Conclusions. For all classifiers, it appears that a small                                                                           imply that it suffices to tune just the set of most important hyperset of hyperparameters are responsible for most variation in perfor-                                                                           parameters. While the results by Hutter et al. [22] showed that this
mance. In many cases, this is the same set of hyperparameters across                                                                           can indeed lead to faster improvements, they also indicated that it
datasets. Knowing which hyperparameters are important is rele-                                                                                 is still advisable to tune all hyperparameters when enough budget
vant in a variety of contexts, ranging from experimental setups to                                                                             is available. In the next experiment, as a complementary analysis,
automated hyperparameter optimization procedures. Furthermore,                                                                                 we will study which values are likely to yield good performance.
knowing which hyperparameters are important is interesting as a
scientific endeavor in itself, and can provide guidance for algorithm                                                                          7   GOOD HYPERPARAMETER VALUES
developers.                                                                                                                                    Now that we know which hyperparameters are important, the next
   Interestingly, the hyperparameter determining the imputation                                                                                natural question is which values they should be set to in order to
strategy did not seem to matter for any of the classifiers, even                                                                               likely obtain good performance. We now discuss the results of the
though the selected benchmarking suite contains datasets such as                                                                               experiment for answering this question.

KDD ’18, August 19–23, 2018, London, United Kingdom                                                                                                                                                                                                                                      Jan N. van Rijn and Frank Hutter

              Variance Contribution
                                      0.75                                                                                                                                                                               0.75

                                                                                                                                                                                                 Variance Contribution
                                                                   ●

                                                                                                                                                                                                                                                                                                                                       ●

                                      0.50                                                                                                                                                                                                                                                                                             ●

                                                                                                                                                                                                                         0.50
                                                                                                                                            ●                                                                                                                                                                                          ●
                                                                                                                                                                                                                                                                                                                                       ●

                                                                                                                                                                                                                                                                                                                                       ●

                                                                                                                                                                                                                                                                                                         ●

                                                                                                                                                                                                                                                                                                         ●
                                                                                                                             ●              ●

                                                                                                                                                                                                                                                                                                                        ●
                                                                                                                                            ●
                                                                                                                             ●
                                                                                                                             ●                                                                                                                                           ●

                                      0.25
                                                                                                               ●
                                                                                                                             ●

                                                                                                                                            ●
                                                                                                                                            ●
                                                                                                               ●                            ●                                                                                                                            ●

                                                                                                                                                                                                                         0.25
                                                                                                                                                                                                                                                                                                                        ●
                                                                                                                                                                                                                                                                                                                        ●
                                                                                                                                                                                                                                                                                                         ●
                                                                   ●                                           ●                                                                                                                                                                                                        ●
                                                                                                               ●                                                                                                                                                                                                        ●

                                                                                                               ●
                                                                                                                                                                                                                                                                         ●
                                                                                                                                                                                                                                                                                                         ●
                                                                                                                                                                                                                                                                                                         ●
                                                                                                                                                                                                                                                                         ●                               ●
                                                                                                 ●                                                                                                                                                                                       ●
                                                                   ●                                                                                                                                                                                                     ●
                                                                                                                                                                                                                                                                         ●
                                                                   ●
                                                                   ●                             ●
                                                                                                 ●                                                                                                                                                                                       ●
                                                                   ●                             ●
                                                                                                 ●
                                                   ●
                                                                   ●
                                                                   ●
                                                                   ●
                                                   ●                                                                                                                                                                                                                                     ●
                                                                   ●
                                                                   ●
                                                                   ●               ●                                                                                                                                                                                                     ●

                                      0.00                                         ●                                                                                                                                                                    ●                                ●
                                                   ●
                                                                                                                                                                                                                                                        ●
                                                                                                                                                                                                                                                        ●

                                             tio  n         er                  it             af          af           ur             ap        ea
                                                                                                                                                       s            f
                                                                                                                                                                   ea                                                    0.00          ●
                                                                                                                                                                                                                                       ●
                                                                                                                                                                                                                                       ●

                                                              ion
                                                                            sp l            le            le               es         str          tu         es
                                             ta         cr              es                es          es            ea            ot                 re          l
                                         pu                it          pl              pl            pl            .f  t         bo             .f            pl                                                                  at io           tio
                                                                                                                                                                                                                                                     ns          rit
                                                                                                                                                                                                                                                                     m                 th
                                                                                                                                                                                                                                                                                                   ep
                                                                                                                                                                                                                                                                                                        th
                                                                                                                                                                                                                                                                                                                   ep
                                                                                                                                                                                                                                                                                                                       th          rate             pt h
                                       im              it          am              sa            am                                                    .s                                                                              n                             h        ax                                                                de
                                                  sp                                  m                        ax                           ax           am                                                                     pu             ite           go                 .d               ax               .d             ing
                                                     l          .s              ./             .s          /m                             m                                                                                        t              ra        al                    ep               .d         ax            ar               ax .
                                                              in            ea              in
                                                                                                          ap
                                                                                                                                                     in                                                                    im                                                /m              /m              /m                n           m
                                                            m                  t          /m                                                      m                                                                                                                                                                         le
                                                                        .f                           str                                                                                                                                                             r.                hm            ra
                                                                       ax          ures              ot                                                                                                                                                           ng               rit                 te
                                                                /m              fe             bo                                                                                                                                                            ar                   go              ng
                                                              p                   at                                                                                                                                                                           ni             al              ar
                                                            str         ax                                                                                                                                                                                  le                                  ni
                                                               a           .                                                                                                                                                                           go                                    le
                                                       bo               m                                                                                                                                                                                ./
                                                         ot                                                                                                                                                                                            al

                                                            (a) Marginal contribution per dataset                                                                                                                                              (a) Marginal contribution per dataset

                                 5.5                                                                                                            bootstrap                                                           4.5                                                                                                                    algorithm
                                                                                                                                                criterion                                                                                                                                                                                  imputation
                                 5.0                                                                                                            imputation                                                                                                                                                                                 iterations
                                                                                                                                                max. features                                                       4.0                                                                                                                    learning rate
                                                                                                                                                min. samples leaf                                                                                                                                                                          max. depth
                                 4.5                                                                                                            min. samples split

        Average Rank                                                                                                                                                                       Average Rank
                                                                                                                                                                                                                    3.5
                                 4.0

                                 3.5                                                                                                                                                                                3.0

                                 3.0
                                                                                                                                                                                                                    2.5
                                 2.5

                                 2.0                                                                                                                                                                                2.0

                                         0                         10                          20                       30                        40                    50                                                  0                          10                         20                         30                       40                   50
                                                                                       Number of Iterations                                                                                                                                                          Number of Iterations
                                        (b) Random Search, excluding one parameter at a time                                                                                                                               (b) Random Search, excluding one parameter at a time

                                                             CD                                                                                                                                                                                 CD

                                                       1                    2                  3                   4                  5                   6                                                                                1                     2                           3                     4                        5

      min. samples leaf                                                                                                                                        imputation                        max. depth                                                                                                                                     imputation
         max. features                                                                                                                                         criterion                        learning rate                                                                                                                                   iterations
             bootstrap                                                                                                                                         min. samples split                  algorithm

                                             (c) Ranked hyperparameter importance, α = 0.05.                                                                                                                                     (c) Ranked hyperparameter importance, α = 0.05.

                                                              Figure 4: Random Forest.                                                                                                                                                                      Figure 5: Adaboost.

                                                                                                                                                                                    from the 99 other datasets. Figure 7 and Table 4 report results com-
   Figure 6 shows the kernel density estimators for the most im-                                                                                                                    paring Hyperband with a uniform prior vs. the data-driven prior.
portant hyperparameters per classifier. It becomes clear that for                                                                                                                   Hyperband was ran with the following hyperparameters: 5 brackrandom forests the minimal number of data points per leaf has                                                                                                                       ets, smax = 4, η = 2 and R = |D (i) | (the number of data points
a good default and should typically be set to quite small values.                                                                                                                   of dataset D (i) ). Each optimizer was ran with 10 different random
This is in line with the results reported by Geurts et al. [15] (albeit                                                                                                             seeds, and we report the average of their results.
for the variant of ‘Extremely Randomized Trees’). Likewise, the                                                                                                                        For each dataset, Figure 7 shows the difference in predictive
maximum depth of the decision tree in Adaboost should typically                                                                                                                     accuracy between the two procedures: values greater than 0 indibe set to a large value. Both hyperparameters are commonly used                                                                                                                     cate that sampling according to the data-driven priors was better
for regularization, but the empirical data indicates that this should                                                                                                               by this amount, and vice versa. These per-dataset differences are
only be applied in moderation. For both types of SVMs, the best per-                                                                                                                aggregated using a violinplot. The results indicate that on many
formance can typically be achieved with low values of the gamma                                                                                                                     datasets the data-driven priors were indeed better, especially for
hyperparameter.                                                                                                                                                                     random forests.
   Next, we report the results of the experiment for verifying the                                                                                                                     When evaluating experiments across a wide range of datasets,
usefulness of these priors in hyperparameter optimization. We do                                                                                                                    performance scales become a confounding factor. For example, for
this in a leave-one-out setting: for each dataset under investiga-                                                                                                                  several datasets a performance improvement of 0.01 already makes
tion, we build the priors based on the empirical performance data                                                                                                                   a great difference, whereas for others an improvement of 0.05 is

Hyperparameter Importance Across Datasets                                                                                                                                   KDD ’18, August 19–23, 2018, London, United Kingdom

 0.35                                                                                0.18                                                         0.14                                              0.12

 0.30                                                                                0.16                                                         0.12                                              0.10

 0.25                                                                                0.14                                                         0.10
                                                                                                                                                                                                    0.08
 0.20
                                                                                     0.12
                                                                                                                                                  0.08
                                                                                                                                                                                                    0.06
 0.15
                                                                                     0.10
                                                                                                                                                  0.06
 0.10                                                                                                                                                                                               0.04
                                                                                     0.08
                                                                                                                                                  0.04
 0.05
                                                                                     0.06                                                                                                           0.02
 0.00                                                                                                                                             0.02
         1   2   3   4   5   6   7   8   9 10 11 12 13 14 15 16 17 18 19 20                 1     2   3   4     5       6     7      8   9   10              10 4    10 3     10 2   10 1   100            10 4   10 3   10 2   10 1   100

                 (a) RF: min. samples per leaf                                                  (b) Adaboost: max. depth of tree                             (c) SVM (RBF kernel): gamma                      (d) SVM (sigmoid): gamma

Figure 6: Obtained priors for the hyperparameter found to be most important for each classifier. The x-axis represents the
value, the y-axis represents the probability that this value will be sampled (integer parameters will be rounded).

                                                                                                                                                            The results of this test are presented in Table 4. We observe that
                                                                                                               0.175
                                                                                                                                                         the data-driven priors significantly improved performance over
  0.02
                                            0.10
                                                                              0.02                             0.150                                     using uniform priors for all classifiers.3 The fact that the priors we
                                                                                                               0.125
                                                                                                                                                         obtained with a straightforward density estimator already yielded
                                            0.08
  0.01
                                                                              0.01
                                                                                                                                                         statistically significant improvements shows great promise. We
                                            0.06
                                                                                                               0.100
                                                                                                                                                         see these simple estimators only as a first step and believe that
  0.00
                                                                                                               0.075
                                                                                                                                                         better methods (e.g., based on traditional meta-learning and/or
                                            0.04
                                                                              0.00                                                                       more sophisticated density estimators) are likely to yield even better
                                                                                                               0.050
  0.01
                                                                                                                                                         results.
                                            0.02
                                                                                                               0.025
                                                                              0.01
  0.02

                                                                                                               0.000
                                                                                                                                                         8          CONCLUSIONS AND FUTURE WORK
                                            0.00

  0.03
                                                                                                                                                         In this work we addressed the questions which of a classifier’s
                                                                                                                                                         hyperparameters are most important, and what tend to be good
    (a) SVM (RBF)                                  (b) Sigmoid                       (c) Adaboost                           (d) RF
                                                                                                                                                         values for these hyperparameters. In order to identify important
                                                                                                                                                         hyperparameters, we applied functional ANOVA to a collection of
Figure 7: Difference in performance between two instances                                                                                                100 datasets. The results indicate that the same hyperparameters
of Hyperband, one sampling based on the obtained priors                                                                                                  are typically important for many datasets. For SVMs, the gamma
and one using uniform sampling. Values bigger than zero                                                                                                  and complexity hyperparameters are most important, for Adaboost
indicate superior performance for the procedure sampling                                                                                                 the maximum depth and learning rate, and for random forests
based on the priors, and vice-versa.                                                                                                                     the minimum number of samples per leaf and maximum features
                                                                                                                                                         available for a split. To the best of our knowledge, this is the first
                                                                                                                                                         methodological attempt to demonstrate these findings across many
Table 4: Results of Nemenyi test (α = 0.05, CD ≈ 0.20). We re-                                                                                           datasets. In order to verify these findings, we conducted a largeport ranks across M datasets (max. 100), boldface the better                                                                                             scale optimization experiment, for each classifier optimizing all
approach (lower rank) and show whether the improvement                                                                                                   but one hyperparameter. The results of this experiment are in line
is significant.                                                                                                                                          with the functional ANOVA results and largely agree with popular
                                                                                                                                                         belief (for example, confirming the common belief that the gamma
                         Classifier                            M          Uniform                     Priors           Sig.                              and complexity hyperparameters are the most important hyper-
                         random forest                        100             1.72                     1.28            yes                               parameters for SVMs). One surprising outcome of this analysis is
                         Adaboost                              92             1.71                     1.29            yes                               that the strategy of data imputation hardly influences performance;
                         SVM (sigmoid)                         86             1.73                     1.27            yes                               investigating this matter further could warrant a whole study on
                         SVM (RBF)                             89             1.60                     1.40            yes                               its own, ideally leading to additional data imputation techniques.
                                                                                                                                                             In order to determine which hyperparameter values tend to yield
                                                                                                                                                         good performance, we fitted kernel density estimators to hyperpaconsidered quite small. In order to alleviate this problem we conduct                                                                                    rameter values that performed well on other datasets. This simple
a statistical test, in this case the Nemenyi test, as recommended                                                                                        method already shows great promise based on the power of usby Demšar [10]. For each dataset, the Hyperband procedures are                                                                                           ing data from many datasets: sampling from data-driven priors in
ranked by their final performance on the test set (the best pro-                                                                                         hyperparameter optimization performed significantly better than
cedure obtaining the lower rank, and an equal rank in case of a                                                                                          sampling from a uniform prior. We strove to keep all aspects of this
draw). If the ranks averaged over all datasets differ by more than a
critical distance CD, the procedure with the lower rank performs                                                                                         3 For the case of SVMs with RBF kernel, we note that the difference does not visually

statistically significant better.                                                                                                                        appear significant in Figure 7, but using priors was better in 60% of the datasets.

KDD ’18, August 19–23, 2018, London, United Kingdom                                                                                     Jan N. van Rijn and Frank Hutter

work reproducible by anyone; we uploaded all the algorithm per-                                   support vector machines. Neurocomputing 75, 1 (2012), 3–13.
formance data to OpenML, including a Notebook for reproducing                                [17] L.K. Hansen and P. Salamon. 1990. Neural Network Ensembles. Pattern Analysis
                                                                                                  and Machine Intelligence, IEEE Transactions on 12, 10 (1990), 993–1001.
all results and figures in this paper.                                                       [18] G. Hooker. 2007. Generalized functional anova diagnostics for high-dimensional
    In future work we plan to apply this analysis techniques to a                                 functions of dependent variables. Journal of Computational and Graphical Statis-
                                                                                                  tics 16, 3 (2007), 709–732.
wider range of classifiers. While in this work we focused on more                            [19] J. Z. Huang. 1998. Projection estimation in multiple regression with application
established types of classifiers to develop the methodology, quanti-                              to functional ANOVA models. The annals of statistics 26, 1 (1998), 242–272.
fying important hyperparameters and good hyperparameter ranges                               [20] F. Hutter, H. H. Hoos, and K. Leyton-Brown. 2011. Sequential model-based
                                                                                                  optimization for general algorithm configuration. In International Conference on
of modern techniques, such as deep neural networks and extreme                                    Learning and Intelligent Optimization. Springer, 507–523.
gradient boosting classifiers, could provide a useful empirical foun-                        [21] F. Hutter, H. H. Hoos, and K. Leyton-Brown. 2013. Identifying key algorithm
dation to the field. Furthermore, the developed methodology is by                                 parameters and instance features using forward selection. In International Con-
                                                                                                  ference on Learning and Intelligent Optimization. Springer, 364–381.
no means restricted to the classification setting; in future work, we                        [22] F. Hutter, H. H. Hoos, and K. Leyton-Brown. 2014. An efficient approach for
plan to also apply it to regression and clustering algorithms. Finally,                           assessing hyperparameter importance. In Proc. of ICML 2014. 754–762.
                                                                                             [23] K. Jamieson and A. Talwalkar. 2016. Non-stochastic Best Arm Identification and
we aim to employ recent advances in meta-learning to identify                                     Hyperparameter Optimization. In Proc. of AISTATS 2016, Vol. 51. PMLR, 240–248.
similar datasets and base the priors only on these in order to yield                         [24] T. Joachims. 1999. Making Large-scale Support Vector Machine Learning Practical.
dataset-specific priors for hyperparameter optimization.                                          In Advances in Kernel Methods, Bernhard Schölkopf, Christopher J. C. Burges,
                                                                                                  and Alexander J. Smola (Eds.). MIT Press, Cambridge, MA, USA, 169–184.
                                                                                             [25] D. R. Jones, M. Schonlau, and W. J. Welch. 1998. Efficient global optimization
Acknowledgements. We would like to thank Ilya Loshchilov for                                      of expensive black-box functions. Journal of Global optimization 13, 4 (1998),
the valuable discussion that led to the methods we used for (i) the                               455–492.
                                                                                             [26] A. Klein, S. Falkner, S. Bartels, P. Hennig, and F. Hutter. 2017. Fast Bayesian
verification of hyperparameter importance and (ii) determining                                    Optimization of Machine Learning Hyperparameters on Large Datasets. In Proc.
priors over good hyperparameter values. This work has been sup-                                   of AISTATS 2017, Vol. 54. PMLR, 528–536.
                                                                                             [27] P. Larraanaga and J. A. Lozano. 2001. Estimation of Distribution Algorithms: A
ported by the European Research Council (ERC) under the European                                  New Tool for Evolutionary Computation. Kluwer Academic Publishers, Norwell,
Union’s Horizon 2020 research and innovation programme under                                      MA, USA.
grant no. 716721, the German state of Baden-Württemberg through                              [28] L. Li, K. Jamieson, G. DeSalvo, A. Rostamizadeh, and A. Talwalkar. 2017. Hyper-
                                                                                                  band: Bandit-Based Configuration Evaluation for Hyperparameter Optimization.
bwHPC and the German Research Foundation (DFG) through grant                                      In Proc. of ICLR 2017. 15 pages.
no. INST 39/963-1 FUGG.                                                                      [29] I. Loshchilov and F. Hutter. 2016. CMA-ES for Hyperparameter Optimization of
                                                                                                  Deep Neural Networks. In Proc. of ICLR 2016 Workshop. 8 pages.
                                                                                             [30] P. B. C. Miranda, R. B. C. Prudêncio, A. P. L. F. De Carvalho, and C. Soares. 2014.
REFERENCES                                                                                        A hybrid meta-learning architecture for multi-objective optimization of SVM
 [1] J. Bergstra, R. Bardenet, Y. Bengio, and B. Kégl. 2011. Algorithms for Hyper-                parameters. Neurocomputing 143 (2014), 27–43.
     Parameter Optimization. In Advances in Neural Information Processing Systems            [31] F. Pedregosa, G. Varoquaux, A. Gramfort, V. Michel, B. Thirion, O. Grisel, M.
     24. Curran Associates, Inc., 2546–2554.                                                      Blondel, P. Prettenhofer, R. Weiss, V. Dubourg, J. Vanderplas, A. Passos, D. Cour-
 [2] J. Bergstra and Y. Bengio. 2012. Random search for hyper-parameter optimization.             napeau, M. Brucher, M. Perrot, and E. Duchesnay. 2011. Scikit-learn: Machine
     Journal of Machine Learning Research 13, Feb (2012), 281–305.                                Learning in Python. Journal of Machine Learning Research 12 (2011), 2825–2830.
 [3] A. Biedenkapp, M. Lindauer, K. Eggensperger, C. Fawcett, H. H. Hoos, and F. Hut-        [32] M. Reif, F. Shafait, and A. Dengel. 2012. Meta-learning for evolutionary parameter
     ter. 2017. Efficient Parameter Importance Analysis via Ablation with Surrogates.             optimization of classifiers. Machine learning 87, 3 (2012), 357–380.
     In Proc. of AAAI 2017. AAAI Press, 773–779.                                             [33] D. W. Scott. 2015. Multivariate density estimation: theory, practice, and visualiza-
 [4] B. Bischl, G. Casalicchio, M. Feurer, F. Hutter, M. Lang, R. G. Mantovani, J. N. van         tion. John Wiley & Sons.
     Rijn, and J. Vanschoren. 2017. OpenML Benchmarking Suites and the OpenML100.            [34] J. Snoek, H. Larochelle, and R. P. Adams. 2012. Practical Bayesian optimization
     ArXiv [stat.ML] 1708.03731v1 (2017), 6 pages.                                                of machine learning algorithms. In Advances in neural information processing
 [5] E. V. Bonilla, K. M. Chai, and C. Williams. 2008. Multi-task Gaussian Process                systems 25. ACM, 2951–2959.
     Prediction. In Advances in Neural Information Processing Systems 20, J. C. Platt,       [35] C. Soares, P. Brazdil, and P. Kuba. 2004. A meta-learning method to select the
     D. Koller, Y. Singer, and S. T. Roweis (Eds.). Curran Associates, Inc., 153–160.             kernel width in support vector regression. Machine learning 54, 3 (2004), 195–209.
 [6] P. Brazdil, C. Giraud-Carrier, C. Soares, and R. Vilalta. 2008. Metalearning: Appli-    [36] I. M. Sobol. 1993. Sensitivity estimates for nonlinear mathematical models.
     cations to Data Mining (1 ed.). Springer Publishing Company, Incorporated.                   Mathematical Modelling and Computational Experiments 1, 4 (1993), 407–414.
 [7] L. Breiman. 2001. Random Forests. Machine learning 45, 1 (2001), 5–32.                  [37] J. T. Springenberg, A. Klein, S. Falkner, and F. Hutter. 2016. Bayesian optimization
 [8] L. Buitinck, G. Louppe, M. Blondel, F. Pedregosa, A. Mueller, O. Grisel, V. Niculae,         with robust Bayesian neural networks. In Advances in Neural Information Process-
     P. Prettenhofer, A. Gramfort, J. Grobler, R. Layton, J. VanderPlas, A. Joly, B. Holt,        ing Systems 29, D. D. Lee, M. Sugiyama, U. V. Luxburg, I. Guyon, and R. Garnett
     and G. Varoquaux. 2013. API design for machine learning software: experiences                (Eds.). Curran Associates, Inc., 4134–4142.
     from the scikit-learn project. In ECML PKDD Workshop: Languages for Data                [38] K. Swersky, J. Snoek, and R. Adams. 2013. Multi-task Bayesian optimization. In
     Mining and Machine Learning. 108–122.                                                        Advances in Neural Information Processing Systems 26. 2004–2012.
 [9] C. Chang and C. Lin. 2011. LIBSVM: A library for support vector machines. ACM           [39] C. Thornton, F. Hutter, H. Hoos, and K. Leyton-Brown. 2013. Auto-WEKA:
     Transactions on Intelligent Systems and Technology 2 (2011), 27:1–27:27. Issue 3.            combined selection and hyperparameter optimization of classification algorithms.
[10] J. Demšar. 2006. Statistical Comparisons of Classifiers over Multiple Data Sets.             In Proc. of ACM SIGKDD conference on Knowledge Discovery and Data Mining
     Journal of Machine Learning Research 7 (2006), 1–30.                                         (KDD). 847–855.
[11] C. Fawcett and H. H. Hoos. 2016. Analysing differences between algorithm                [40] J. N. van Rijn. 2016. Massively Collaborative Machine Learning. Ph.D. Dissertation.
     configurations through ablation. Journal of Heuristics 22, 4 (2016), 431–458.                Leiden University.
[12] M. Feurer, A. Klein, K. Eggensperger, J. T. Springenberg, M. Blum, and F. Hutter.       [41] J. N. van Rijn, S. M. Abdulrahman, P. Brazdil, and J. Vanschoren. 2015. Fast Algo-
     2015. Efficient and Robust Automated Machine Learning. In Advances in Neural                 rithm Selection using Learning Curves. In Advances in Intelligent Data Analysis
     Information Processing Systems 28. Curran Associates, Inc., 2962–2970.                       XIV. Springer, 298–309.
[13] M. Feurer, J. T. Springenberg, and F. Hutter. 2015. Initializing Bayesian Hyper-        [42] J. N. van Rijn and F. Hutter. 2017. An Empirical Study of Hyperparameter
     parameter Optimization via Meta-Learning. In Proc. of AAAI 2015. AAAI Press,                 Importance Across Datasets. In Proc. of AutoML 2017 @ ECML-PKDD. CEUR-WS,
     1128–1135.                                                                                   97–104.
[14] Y. Freund and R. E. Schapire. 1995. A desicion-theoretic generalization of on-line      [43] J. Vanschoren, J. N. van Rijn, B. Bischl, and L. Torgo. 2014. OpenML: networked
     learning and an application to boosting. In European conference on computational             science in machine learning. ACM SIGKDD Explorations Newsletter 15, 2 (2014),
     learning theory. Springer, 23–37.                                                            49–60.
[15] P. Geurts, D. Ernst, and L. Wehenkel. 2006. Extremely randomized trees. Machine         [44] M. Wistuba, N. Schilling, and L. Schmidt-Thieme. 2015. Hyperparameter search
     learning 63, 1 (2006), 3–42.                                                                 space pruning–a new component for sequential model-based hyperparameter
[16] T. A. F. Gomes, R. B. C. Prudêncio, C. Soares, A. L. D. Rossi, and A. Carvalho.              optimization. In Proc. of ECML/PKDD 2015. Springer, 104–119.
     2012. Combining meta-learning and search techniques to select parameters for
