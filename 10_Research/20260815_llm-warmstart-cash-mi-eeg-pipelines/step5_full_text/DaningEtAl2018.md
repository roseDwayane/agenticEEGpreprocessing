---
citation_key: "DaningEtAl2018"
title: "Using Known Information to Accelerate HyperParameters Optimization Based on SMBO"
authors: "Cheng Daning; Zhang Hanping; Xia Fen; Li Shigang; Zhang Yunquan"
year: 2018
doi: ""
source: "arXiv (1811.03322)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "1811.03322"
conversion: "pdftotext -layout (automated)"
---

# Using Known Information to Accelerate HyperParameters Optimization Based on SMBO

Using Gradient based multikernel Gaussian Process
                                         and Meta-acquisition function to Accelerate SMBO
                                                            Daning Cheng ∗† , Hanping Zhang‡, Fen Xia‡ , Shigang Li∗ and Yunquan Zhang∗
                                                                ∗ SKL of Computer Architecture, Institute of Computing Technology, CAS, China

                                                                                  Email: {chengdaning, lishigang, zyq}@ict.ac.cn
                                                                                   † University of Chinese Academy of Sciences
                                                                                                 ‡ WiseUranium corp.

                                                                                        { Xiafen, Zhanghanping }@ebrain.ai

arXiv:1811.03322v2 [cs.LG] 18 Jul 2019
                                            Abstract—Automatic machine learning (automl) is a crucial            Our main contributions are summarised as follows. (1)
                                         technology in machine learning. Sequential model-based optimi-       We propose a novelty gradient-based multikernel Gaussian
                                         sation algorithms (SMBO) (e.g., SMAC, TPE) are state-of-the-art      process regression to accelerate the SMBO. (2) We propose
                                         hyperparameter optimisation methods in automl.
                                            However, SMBO does not consider known information, like the       the meta-acquisition function which encourages SMBO to
                                         best hyperparameters high possibility range and gradients. In this   explore hyperparameters on best hyperparameters high possi-
                                         paper, we accelerate the traditional SMBO method and name our        bility range. (3) In L2 norm experiments, our method achieves
                                         method as accSMBO. In accSMBO, we build a gradient-based             convergence 140% to 300% faster in epoch than SMAC on
                                         multikernel Gaussian process with a good generalisation ability      datasets of different scales. It outperformed the previous best
                                         and we design meta-acquisition function which encourages that
                                         SMBO puts more attention on the best hyperparameters high            hyperparameter optimisation approach.
                                         possibility range. In L2 norm regularised logistic loss function                          II. R ELATED WORKS
                                         experiments, our method exhibited state-of-the-art performance.
                                                                                                                 Several automatic machine learning tools have been de-
                                           Index Terms—SMBO, black box optimisation, hyper gradient,          veloped, such as Google’s AutoML, autoWEKA[1] and au-
                                         metalearning                                                         tosklearn in recent years. Those tools contain various types
                                                                                                              of hyperparameter optimisation algorithms such as proba-
                                                               I. I NTRODUCTION                               bilistic methods (e.g., Bayesian optimization[1][2] [3] [4]),
                                            Automatic machine learning (automl) is a key technology           random optimization methods (e.g., grid search, heuristic
                                         in machine learning: Current machine learning models require         algorithms, and Neural Networks[5]), Fourier analysis (e.g.,
                                         enormous numbers of hyperparameters. Thus, hyperparameter            Harmonica[6]), and decision-theoretic methods (e.g., the Suc-
                                         optimisation is the key part of automl. Sequential model-based       cessive Halving (SH) algorithm and Hyperband[7]).
                                         optimisation (SMBO) is a state-of-the-art hyperparameter op-            SMBO algorithms[2], are currently the most widely used
                                         timisation algorithm frame. However, traditional SMBO does           Bayesian optimization in automl [1] [8][2]. SMAC[2], TPE
                                         not consider hyperparameter known information. Intuitively           [9] and Gaussian-process-based SMBO [10] are the state-of-
                                         known information can accelerate the convergence process. In         the-art SMBO algorithms. In automl, metalearning[11] and
                                         this paper, we use (1) hyperparameter gradient (2) regular pat-      gradient-based hyperparameter optimization[12][13] [14] are
                                         terns of hyperparameters’ performance to accelerate SMBO.            the hotly debated topics.
                                            In this paper, we propose a novel algorithm: accSMBO.                Problem Current SMBO algorithms do not make full use
                                         AccSMBO uses the following two methods to optimise                   of known information in their iteration processes.
                                         SMBO: (1). We use a gradient-based multikernel Gaussian                                    III. BACKGROUND
                                         process regression to fit the observed values and the observed
                                         gradient values. Consequently, SMBO builds the response              A. Problem Setting
                                         surface of the hyperparameter-performance curve faster with             Hyperparameter optimization focuses on the problem of
                                         a good generalisation ability and less computational burden.         learning a performance function f : X 7→ Y with a finite Y.
                                         (2). We propose meta-acquisition function: We build an em-           A learning algorithm A exposes hyperparameters λ ∈ Λ
                                         pirical probability density function, abbr. EPDF, based on the       that change the way the learning algorithm A(λ) operates.
                                         metalearning dataset. At each SMBO iteration, we adjust the             For a given learning algorithm A and a limited amount
                                         candidate hyperparameter towards the ranges with a high prob-        of training data D = {(x1 , h1 ), (x2 , y2 ), ..., (xm , ym )}, the
                                         ability of the best hyperparameters based on EPDF. The above         goal of hyperparameter optimization is to minimize the per-
                                         methods accelerate SMBO and achieve satisfactory results. In         formance function f , which is estimated by splitting D into
                                                                                                                                                        (i)             (i)
                                         the experiments, accSMBO improves the convergence speed              disjoint training and validation sets, Dtrain and Dvail , re-
                                         by 140% to 300% in epoch compared to the SMAC algorithm              spectively. The performance function f is applied by A with
                                                                                                                             (i)
                                         on different datasets.                                               λ∗ ∈ Λ to Dtrain ; then, the predictive performance of these

Algorithm 1 SMBO                                                                                  Table I
  Input: hyperparameter history H, initial hyperparameter λ              S TATE OF THE ART ALGORITHM FOR SMBO ALGORITHM FRAME

                                                                                        Algorithm                         Step in SMBO
  Output: λ from H with minimal c
                                                                      MetaLearning [15] [11]                                     Step 0
                                                                      Random selection
  step 0: Choose initial value λ0 and its f (λ0 ) and add
                                                                      Random Forest & Gaussian Process(SMAC)[2]            Step 1: ML
  (λ0 , f (λ0 )) into H                                               Tree-structured Parzen Estimator(TPE)[9]
  repeat                                                              Neural Network[5]
     step 1: Update ML given H and compute acquisition                Fourier analysis[6]
                                                                      Gaussian Process [10]
     f unction.
     step 2: Gain the hyperparameter candidates from                  Probability of Improvement [16]             Step 1:
                                                                      Expected Improvement[3]                     acquisition f unction
     acquisition f unction.                                           d-KG (contains gradient information) [14]
     step 3: λ ← select the best candidate hyperparameter in          GP Upper Confidence Bound[17]
     candidates from step 2                                           hyperband[7]                                               Step 3
     step 4: ComputeSf (λ)                                            Intensify process[2]
     step 5: H ← H {(λ, f (λ))}}
  until the time budget has not been exhausted
                                                                        To make M reflect sampled points trend better, researchers
                                                                     proposed different response surface models. However, those
                 (i)                                                 models do not consider gradient or be sensitive to gradient
functions on Dvail is evaluated. This approach allows the
hyperparameter optimization problem to be written as follows:        information. In those models, random forest is the most widely
                                                                     used and state of the art methods[8]. The SMBO which uses
                                                                     random forest is SMAC. Thus, SMAC is the main benchmark
               λ∗ ∈ argmin f (λ) , g(A(λ), λ)                        in this paper.
                         λ∈Λ
                        k                                               State of the art acquisition f unctions choices have ex-
                    1   X                (i)   (i)                   pected improvement function[3] and d-KG function[14]. D-
                =           L(A(λ), Dtrain , Dvail )
                    k   1                                            KG is the acquisition f unction which uses gradient infor-
               s.t.A(λ) ∈ argmin h(λ, model)                  (1)    mation. The abbreviation of expected improvement function is
                               model∈R                               EI function in this paper. Because our method does not care
                 (i)        (i)                                      about the choice of acquisition f unctions, we choose the
   where L(A, Dtrain , Dvail ) is the loss achieved by A when
              (i)                       (i)                          most widely used EI function as our acquisition f unctions.
trained on Dtrain and evaluated on Dvail . We use k-fold cross-
                                                                        The selection of candidates is the main purpose for
validation [18], which splits the training data into k equal-sized
             (i)                                                     step 3 in algorithm 1. The best algorithms for this step
partitions Dvail
                                                                     are Hyperband[7] and the intensify process[2]. Because our
B. Basic Information                                                 method does not care about how to choose the best candidate,
   SMBO algorithm frame and its state-of-art algorithm               we use the most widely used intensify process in step 3 in our
The SMBO is a black-box optimisation algorithm frame.                benchmarks.
The SMBO algorithm frame, algorithm 1, does not possess                 Epoch In SMBO, we name one iteration/loop, as one epoch.
complete information concerning f (·). It requires only sample       One epoch is the smallest unit to measure the performance of
(λ, f (λ)) values to build f (λ)’s model ML ( In some works,         SMBO, for SMBO cost almost the same time at each iteration.
ML is named as response surface[3][4]).                                 For different code implementation of algorithm and running
   The core of SMBO is building a model ML that captures             platform, the time cost is different for an epoch. Therefore, it
the dependence on the loss function L for the various hyper-         is better to use the number of the epoch as an index instead
parameter settings and using acquisitionf unction to choose          of the time when we conduct experiments.
candidate hyperparameters. To make our paper clear, we use              Gaussian process (GP) The GP [18] is defined by the
”AC function (ac(λ))” as the abbr. of acquisition f unction.         property that any finite set of m points {(xn , yn ) ∈ X , Y}m
                                                                                                                                  n=1
   Researchers proposed different algorithms to fill the SMBO        induces a multivariate Gaussian distribution on Rm . The
algorithm frame. Most of these state of the art algorithms are       nth point is taken as the function value f (xn ), and the
presented at table I and the following descriptions.                 elegant marginalization properties of the Gaussian distribution
   To make H has a good initial value, researchers proposed          allow us to compute marginals and conditionals in closed
metalearning technology. Based on metalearning, it is possible       form. The support and properties of the resulting distribution,
to get the optimal or sub-optimal hyperparameter values at           N (m(x), var(x)), on functions are determined by a mean
the first epoch. However, to make metalearning technology            function m(x) : X 7→ R and a positive definite covariance
outperform, it is necessary to make the metalearning dataset         function k(x, x) : X ∗ X 7→ R.
large. A large metalearning dataset usually contains the best           GP regression GP based SMBO is the most commonly
hyperparameter values for more cases.                                used SMBO algorithm. In GP based SMBO, the ML in

algorithm 1 is the GP regression from H. The original H                   Algorithm 2 AccSMBO
is defined as follows:                                                      Input: hyperparameter history H, initial hyperparameter λ

              H = {(λ1 , f (λ1 )), ..., (λn , f (λn ), )}                   Output: λ from H with minimal c
   We set λ as a vector with d dimensions. k(x, x′ ) is                     step 0: Choose initial value λ0 and its f (λ0 ) and add
the kernel (covariance) function. We define the vector f =                  (λ0 , f (λ0 )) into H
(f (λ1 ), f (λ2 ), ..., f (λn ))T , whose dimension is (1*n). We            repeat
also define the matrix λ = (λ1 , λ2 , ..., λn )T , whose dimension             step 1: Update ML given H and compute acquisition
is (d*n), and the kernel matrix K(λ, λ), whose dimension is                    f unction.
(n ∗ n), as follows:                                                           step 2: Using metalearning dataset adjust acquisition
                                                                             f unction to meta - acquisition f unction.
                k(λ1 , λ1 )      k(λ1 , λ2 )   ···    k(λ1 , λn )              step 3: Gain the hyperparameter candidates from meta -
               k(λ2 , λ1 )      k(λ2 , λ2 )   ···    k(λ2 , λn )             acquisition f unction.
    K(λ, λ) = 
                                                                 
                    ..               ..        ..         ..                  step 4: λ ← select the best candidate hyperparameter in
                    .                .           .        .      
                                                                               candidates from step 2
                   k(λn , λ1 ) k(λn , λ2 )     · · · k(λn , λn )
                                                                               step 5: ComputeSf (λ)
   The vector K(λ∗ , λ) is defined as K(λ∗ , λ) =                              step 6: H ← H {(λ, f (λ))}}
(k(λ∗ , λ1 ), k(λ∗ , λ2 ), ..., k(λ∗ , λn )).                               until the time budget has not been exhausted
   In the traditional GP regression
                                  Pn          with noise-free observations, the mean m(λ∗ ) =                  γ
                                      i=1 i  k(λ      ∗
                                                 i , λ ), i.e., m(λ∗) =
γK(λ∗, λ). γ = K(λ, λ)−1 f . var(λ∗ ) = k(λ∗ , λ∗ ) −                     information. Figure 2 shows that for the F1 norm multi-
K(λ, λ∗ )K(λ, λ)−1 K(λ, λ∗ ). The ML for GP based SMBO                    class task, the frequency histogram and its fitting empirical
is the GP which is N (m(λ∗), var(λ∗ )).                                   probability density function for hyperparameter max feature
   Hyperparameter gradients Many works [13][12] offer                     and min samples in random forest and tol value in svc process.
gradient-based hyperparameter optimization methods. The gra-                 To accelerate SMBO and make full use of the above
dient of a hyperparameter can be calculated as follows[12]:               regular patterns, we propose an accelerated SMBO, named
                                                                          as accSMBO, which is illustrated in algorithm 2.
              ∇f = ∇2 g + (∇A)T ∇1 g                                         Compared with traditional SMBO, AccSMBO algorithm
                                                                          modifies two parts: 1.) ML . AccSMBO asks ML to reflect
                  = ∇2 g − (∇21,2 h)T (∇21 h)−1 ∇1 g                      the approximate gradients. AccSMBO builds ML which re-
   Those approaches use gradient descent methods, which are               flects general performance trends with fast speed and 2.) The
different from Bayesian optimisation methods.                             structure of SMBO. AccSMBO uses the metalearning dataset
   Meta-Learning dataset Metalearning is the key technology               in the iteration process. AccSMBO places particular emphasis
to accelerate SMBO by offer SMBO an experiential best initial             on the best hyperparameter high probability ranges: modifying
value. Researchers classify those best initial value by the               acquisition f unctions in SMBO basing on the metalearning
feature of task, objective function and dataset. Metalearning             dataset. We name our modified acquisition f unctions as
datasets record those best initial values.                                meta-acquisition f unctions, abbr. metaAC function.
   Although Metalearning gains great success in hyperpa-
                                                                                       V. M ETHODOLOGY IN ACC SMBO
rameter optimisation, for most of the cases, metalearning
technology only accelerates SMBO under the condition that                   AccSMBO uses the following two methods to accelerate the
the metalearning dataset contains the corresponding hyperpa-              SMBO algorithm frame.
rameter values. When the metalearning dataset is not complete,
                                                                          A. Using Gradient-based multikernel GP as ML
the improvement of metalearning is limited.
                                                                             The gradient-based GP regression has proven to be an
                          IV. M AIN IDEA                                  effective ML [14]. However, in traditional gradient-based GP
   We noticed that for most hyperparameters, the performances             regression, the unstable character of the local performance
of the hyperparameters present the following regular patterns:            curve would mislead the process of building ML . What is
1). Generally, the performance of the hyperparameters, like the           more, the computational load for traditional gradient-based GP
logloss-hyperparameters curve, is simple, such as a monotonic             regression is large. To deal with the locally unstable problem,
function or unimodal function. However, locally, those perfor-            we designed a new gradient-based GP. Our gradient-based
mances are unstable, i.e. full of waves, as shown in Figure 1.            multikernel GP regression is different with current multikernel
2). The distribution of the best hyperparameter is not a uniform          GP regression and gradient GP in terms of the initial idea and
distribution. Thus we know some prior, reasonable ranges and              algorithm[19] [14].
the best hyperparameter is in this range with high probability.              Extra notes The dimension of λ is d. When the gradient
It is obviously that metalearning datasets can reflect this               information can be computed, the SMBO history is as follows:

                                   Figure 1. Some of those performance functions are close to unimodal functions or monotonic functions
                                                   0.6

                                        Log Loss                                                              Log Loss
                                                                                                                         0.29
                                                   0.4
                                                                                                                         0.28
                                                     0           5       10               15       20                        0        5       10                15       20
                                                           The depth of decision tree                                            The depth of GBDT tree
                                      (a) The relationship between the decision tree (b) The relationship between the GBDT tree depth
                                      depth and the log loss on the rcv1 dataset     and the log loss on the rcv1 dataset

Figure 2. The frequency histogram and its fitting empirical probability density function of hyperparameter value for f1 norm multi-class task on sparse dataset.
The information is collected from the meta-learning dataset

    Frequency                                                                                                                                 Frequency
                                                                                                        Statistical Frequency

                                                                           Frequency
                0.6               Statistical Frequency                            0.6                                                                                         Statistical Frequency
                                                                                                                                                          0.6
                0.4               Fitting Function                                 0.4                  Fitting Function                                                       Fitting Function
                                                                                                                                                          0.4
                0.2                                                                0.2                                                                    0.2
                 0                                                                   0                                                                      0
                      0.4         0.6                     0.8                         0            5         10                  15                              0     0.005    0.01       0.015      0.02
                                   Value                                                                Value                                                                  Value
                      (a) random forest:max features                                      (b) random forest:min samples                                         (c) liblinear svc preprocessor:tol

                                                                Figure 3. Accurate gradient fitting would mislead regression function

                                                                        original function                                 20
                20                                                                                                                                                              original function
                                                                        regression function                                                                                     regression function
                                                                                                                          15

                10                                                                                                        10

                                                                                                                           5
                 0
                                                                                                                           0
                  1         1.5   2         2.5            3      3.5     4            4.5     5
                                                                                                                            0             1                 2             3            4              5

(a) Regression function with accurate gradient fitting lose function general (b) Regression function with approximate gradient fitting presents function
trend                                                                        general trend

                                                                                                               In this process, we extract λ, ∇f and f from H. Based
                                                                                                            on that information, we want to build the gradient-based
     H = {(λ1 , f (λ1 ), ∇f (λ1 )), ..., (λn , f (λn ), ∇f (λn ))}                                          multikernel GP regression whose mean function’s gradient is
  We        define         the        vector   ∇f            =                                              closest to the observations.
(∇f (λ1 ), ∇f (λ2 ), ..., ∇f (λn ))T , whose dimension is (d                                                   The essence of our method, i.e. using the combination kernel
* n). We also define the matrix λ = (λ1 , λ2 , ..., λn ), whose                                             on the mean function, is the ordinary GP combination, i.e.,
dimension is (d*n), the kernel matrix K(λ, λ), whose
dimension is (n*n), and the gradient kernel matrix ∇K(λ, λ)
                                                                                                                           ML = GP combine = GP k1 + GP k2 + ... + GP kd+1
whose dimension is (n*(d*n)) as follows:

                                                               where GP k1 denotes the GP which is regressed by kernel
             ∇k(λ1 , λ1 )                                ∇k(λ1 , λ2 )
                                               ∇k(λ1 , λn )                ···
                                                              k1 (·, ·). The above view shows that observations are produced
            ∇k(λ2 , λ1 )                                ∇k(λ2 , λ2 )
                                               ∇k(λ2 , λn )               ···
                                                             by the sum of the different GPes, which are regressed by
∇K(λ, λ) = 
           
                 ..                                ..        ..            ..
                                                             different kernels.
                 .                                 .        .               .
                                                                 Compared with the traditional GP, which is regressed by
               ∇k(λn , λ1 ) ∇k(λn , λ2 ) · · · ∇k(λn , λn )
                                                              only one kernel, the GP combination has higher degrees of
  And Km:n = (Km , Km+1 , Km+2 , ..., Kn ) is the abbr. of freedom: d+1 variable α. Those extra degrees of freedom
combination of matrix.                                        allow our method to fit the observed information such as the
  Gradient-based multikernel GP regression In this paper, observed gradient and point values.
we offer an innovative gradient-based GP regression. The         Mean function In the traditional GP, the mean function
solving process is shown in the algorithm 3.                  m(λ) is a fitting function that uses kernel functions as a basis.

   To implement the basic idea presented above, we initially          the less number of kernels we should use. Eq. 2 and Eq. 3 are
combined different kernels, linear independent kernels, with          the difference in status when using the approximation method.
different coefficients to fit the equations 3 and 2. When the         For most cases, the performance waves locally would exert
number of kernels is equal to the dimension of ∇f , the mean          more influence on the gradient information, Eq. 3 instead of
function can reflect all the observed information, including          points information, Eq. 2. Thus, we expected that Eq. 2 is
point and gradient observed values.                                   accurate and the Eq. 3 is approximated, i.e. using the least
                                                                      squares method to address the gradient equation (i.e., Eq. 3)
                                                                      in the subspace of Eq. 2. The Eq. 4 in algorithm 3 shows this
   f = K1 (λ, λ)α1 + K2 (λ, λ)α2 ...Kd+1 (λ, λ)αd+1            (2)    process. We omit the proof of our process for the limitation
   ∇f = ∇K1 (λ, λ)α1 + ...∇Kd+1 (λ, λ)αd+1                     (3)    of pages and the obviousness of the proof.
                                                                         Thus, considering the property of the hyperparameter curve
   When the Eq. 3 and 2 are solved, we can obtain
                                                                      and computational load, our gradient-based multikernel GP
α1 , α2 , · · · , αd+1 . The mean
                             Pd+1function, m(x), of the GP can        regression is a state-of-the-art choice for SMBO.
be described as m(x) = i=1 Ki (λ∗ , λ)αi
   variance function The variance function of those GPes
                                                                      B. Using Meta-acquisition f unctions in SMBO process
reflects the distance of the prediction point λ∗ and the
observed point λi . Thus, for GP ki , the variance function              Researchers proposed SMBO for the case where the users
is vari (λ∗ ) = ki (λ∗ , λ∗ ) − Ki (λ, λ∗ )Ki (λ, λ)−1 Ki (λ, λ∗ ).   do not possess any information about the objective func-
    Pd+1 the sumPofd+1GPes is still a GP, GP combine is
Because                                                               tion. However, hyperparameter optimisation is not suited to
N ( i=1 mi (λ∗ ), i=1 vari (λ∗ ))                                     this case. We often know some prior information about
   Generalization, approximate and reduce the computa-                hyperparameter. The metalearning datasets often reflect this
tional load Although the radial basis function (RBF) kernel           prior information. We propose meta-acquisition f unction to
satisfies all the requirements listed above (such as polyhar-         use that information. Meta-acquisition f unction makes the
monic spline functions), we still need to address the compu-          acquisition f unctions high at the best hyperparameters high
tational load and generalisation problem. As we mentioned in          probability range
the section ”Main Idea”, the performance curve is simple but             1) meta-acquisition f unction: To make the acquisition
full of waves. The gradients reflect local information rather         f unctions high at the best hyperparameters high probability
than the general trend of the performance curve. Accurate             range, we propose the following method to adjust acquisition
gradient information and fitting introduce a negative influence       f unctions, and we named our method, i.e. step 2 in algorithm
in GP regression and increase the computational load. In              2 as meta-acquisition f unctions, abbr. metaAC function.
particular, when the gradient of the sampled point is against the        Empirical probability density function Before we design
general trend, the negative influence of the regression process       metaAC function, it is necessary to fit an empirical probability
would be significant, shown in Figure 3. Thus, an appropriate         density function.
approximation is needed before running the algorithm.                    We build frequency histogram based on the metalearning
                                                                      dataset as our adjustment reference. AccSMBO does not ask
Algorithm 3 Gradient-based Gaussian process regression in             we have a complete metalearning dataset which contains
Algorithm 2                                                           every initial hyperparameter values for different situations. Our
  Input: kernel K1 , K2 ,..,Kn ; History H, which contains λ          methods only ask that the metalearning dataset reflects the
  and f                                                               trend of distribution. To make the following step clearly, we fit
  Output: Gaussian process GP, with the mean function                 above frequency histogram into empirical probability density
  m(x) and covariance function var(x)                                 function, abbr. EPDF, p(x). This step is shown in subfigure
  Compute the vector α and β:                                         (b) in Figure 4.
       (∇K2:n (λ, λ) − K2:n (λ, λ)K1−1 (λ, λ)∇K1 (λ, λ))α                Design metaAC function To make use of p(x), we expect
       = ∇f − K2:n (λ, λ)K1−1 (λ, λ)∇K1 (λ, λ)f                 (4)   that accSMBO builds metaAC function follows two principles:
       K1 (λ, λ)β = f − K2:n (λ, λ)α                                     1). The p(x) encourages SMBO samples more hyperparam-
                                                                      eter at the best hyperparameter high probability ranges. As we
                                                                      mentioned above, the distribution of the best hyperparameter
  The Eq. 4 is an overdetermined equation that can be solved          is not a uniform distribution. We should explore the best
  via the least squares method.                                       hyperparameter high probability ranges firstly.
  m(λ∗ ) = K2:n (λ∗ , λ)α + K1 (λ∗ , λ)β
                                     Pn                                  2). With the algorithm processing, the influence of p(x) is
                                                 ∗   ∗
  var(λ∗ )             =               i=1 (ki (λ , λ )   −           decreasing. Overly depending on p(λ) would largely break
          ∗    −1               ∗
  Ki (λ, λ )Ki (λ, λ)Ki (λ, λ ))                                      the exploitation and exploration trade-off which would keep
  return N (m(λ∗ ), var(λ∗ ))                                         SMBO from seeing all scope of objective function: After
                                                                      exploring the best hyperparameter high probability ranges,
  In our method, we approximate gradient information by               we still should sample hyperparameters at other ranges. This
reducing the number of kernels. In practice, the more waves,          requirement ensures that SMBO gains the whole scope of the

                                                Figure 4. The change of AC function to Meta-AC function

                                                    Objective Function             1
                                                                                           EPDF based on Metalearning dataset
                                                    Sampled Point
                                                    AC Function
                                                                                0.5

                                                                                   0
                  2            4            6               8       10                       2          4           6           8             10
               (a) oringinal objective function and AC function             (b) Empirical Probability Density Function (EPDF) based on Metalearn-
                                                                            ing Dataset

          6                                                                       10
                                                                                                                        Objective Function
                 MetaAC function                                                   8
                                                                                                                        Sampled Point
          4
                                                                                   6                                    Meta AC Function

                                                                                   4
          2
                                                                                   2

          0                                                                        0
           1      2      3         4    5       6       7       8    9              1       2     3     4     5     6      7    8     9       10

                             (c) Meta-AC function                                      (d) original objective function and Mata-AC function

objective function and gains the best hyperparameters with the                  (2) The metalearning dataset must be large. When the metepoch’s going to infinite.                                                   alearning dataset is too small, metalearning fails to accelerate
  Thus, we design following metaAC(λ, epoch, p(x), ac(λ))                    hyperparameter optimisation: 1) A small metalearning dataset
functions:                                                                   cannot offer the best hyperparameter initial values for some
                                                                             cases. 2) In the SMBO process, to trade off the exploration
                                                                             and exploitation problem, ML and traditional acquisition
   metaAC(λ, epoch, p(x), ac(λ))
                                                                             f unctions encourage SMBO to explore unsampled ranges,
    = ac(λ) ∗ (rate ∗ p(λ)e−epoch + 1 − rate ∗ e−epoch )                     which may be the best hyperparameters low probability range.
   where rate is the parameter which rate ∈ [0, 1] and                       Thus, traditional acquisition f unctions are high at the best
decides the influence of p(λ). rate should be larger when                    hyperparameters low probability range. This process would be
the metalearning dataset is complete.                                        shown in (a) subfigure in Figure 4: For unsampled range [2,6],
   Above descriptions can be shown in (c) subfigure in Figure                the AC function is high, because this range is unexplored.
4. In this case, rate = 1.                                                      Overcome current metalearning shortage Our metaAC
   Convergence proof of metaAC Our method is a modifica-                     function overcomes the shortages of current metalearning
tion of original AC function, and with the algorithm process,                technology:
metaAC degenerates into original AC function. In another                        (1) MetaAC function is part of the optimisation process.
word, the convergent character of metaAC is the same as the                     (2) In metaAC function, the metalearning dataset can be
original AC function with epoch’s going to infinite. Thus, the               small. MetaAC only requires that the metalearning datasets
proof of convergent would be the same as the AC function.                    reflect the trend of the best hyperparameters. The missing
   Acceleration for SMBO In practice, we are more likely                     of some records cannot change the characters of the whole
to meet the cases where the best hyperparameter value is in                  hyperparameter trend.
the high probability ranges. As we can see from (a) subfigure
                                                                                                         VI. E XPERIMENT
and (d) subfigure in Figure 4, after the adjustment by p(x),
SMBO pays more attention to the high probability ranges.                       We use the HOAG experimental framework [12] in the
Thus, metaAC function would accelerate the SMBO process.                     experiments.
   2) The limitation of current metalearning: Metalearning
has been combined into SMBO in recent years. However,                        A. Experiment Settings
current metalearning technology has two shortages.                             Dataset First, we choose a small dataset (pc4 in openML),
   (1) Current automl algorithms only use metalearning in                    a medium-sized dataset (rcv1) and a large dataset (real-sim).
offering an optimal/sub-optimal initial hyperparameter. Met-                 Information concerning these datasets is listed in Table II.
alearning is unable to exert influence on the process of                       We assume that the gradient in the dataset is valid. In all
optimisation.                                                                cases, the dataset is randomly split into two parts: a training

set containing 70% of the dataset samples and a valid set                   SM AC, a state-of-the-art method, performs SMBO using
containing 30% of the dataset samples.                                   a random forest. SMAC is the core algorithm in autosklearn.
                                                                         The initial hyperparameter for this method is λ = 1. We
                                Table II                                 select four challengers in each epoch using the intensify
                         D ATASET INFORMATION                            process to choose the single best challenger (this is the default
  dataset   #features      #size       feature range   sparsity
                                                                         setting for autosklearn). The acquisition function used in these
 real-sim    20,958       72,309            (0,1)       sparse           experiments is the expected improvement function. As we
   rcv1      47,236       20,242            (0,1)       sparse           mentioned in the background section, SMAC is the main
   pc4         38          1,458         (0,10,000)     dense            benchmark for our AccSMBO.
                                                                            AccSM BO In this paper, we modified the autosklearn
   Problem In our experiment, we will solve the problem                  framework. In the setting of our experiment, we set rate in
of determining the regularisation parameter in the L2 norm               metaAC function as 1. To make p(x) accurate, we build p(x)
logistic regression model because it is easy to acquire the              on the metalearning data after deciding object, task and the
hyperparameter gradient of the L2 norm[13]. In this case, the            feature of the dataset. For example, in this experiment cases,
loss function of the inner optimisation problem, i.e., h(λ), is          we build p(x) based on the metalearning data for the case
the regularised logistic loss function. For classification, the          where 1). The objective function is logloss, 2) the task is
outer cost function is the logistic loss function (i.e., Eq. 5).         binary classification and 3). The dataset is sparse dataset.
The logistic loss function overcomes the problem that zero-one              We select four challengers in each epoch and use the
loss is a non-smooth loss function[13]:                                  intensify process to choose the single best challenger (this is
                                                                         also the default setting for autosklearn).
                        X
                        m
                                                                            Grid Search, common method. We adopt the method
   argmin f (λ) =             Φ(xi yiT A(λ))
      λ∈Λ                                                                we presented in this paper, with an exponentially decreasing
                        i=1
                              X
                              m                                          tolerance sequence. In the interval [0, 1], we sample 20 λ
   s.t.A(λ) ∈ argmin                Φ(xi yiT model) + λ kmodelk2   (5)   hyperparameter uniformly; then, we compute the performance
               model∈R
                              i=1                                        of f (λ) serially from λ = 1 to λ = 0.
                                                                            Random Search, common method. This random search
   where Φ is the logistic loss, i.e., Φ(t) = log(1 + e−t ). The         method samples the hyperparameters from a predefined dissolver used for the inner optimization problem of the logistic           tribution. We choose the samples from a uniform distribution
regression problem is stochastic gradient descent[20]. In our            in the interval [0, 1]. To ensure that all methods have similar
problem, we set the search range to λ ∈ [0, 1].                          initial values, we compute the performance in the first epoch
   Kernel In this paper, the hyperparameter dimension, i.e.,             with λ = 1.
λ, is one; thus, we can choose either of two kernels for                    HOAG is a state-of-the-art method which uses gradients to
our gradient GP method: the Gaussian radial basis function               find the minimum of the objective function, similar to gradient
k2 (x1 , x2 ) = exp(− kx1 − x2 k2 ) and the cubic radial basis           descent. In each epoch, the hyperparameter is adjusted toward
function k1 (x1 , x2 ) = kx1 − x2 k3 [4]. In our experiment,             the gradient direction. Here, we use the modified HOAG
the inverse K2 matrix calculation is the key aspect of the               framework from the work[12]. In the initial hyperparameter for
algorithm. However, the accuracy of K2−1 based on the cubic              this method, λ = 1. To achieve the fastest convergence speed
radial basis function is reduced when λ ∈ [0, 1]. Thus, we               in each epoch, we set ǫk = 10−12 . To measure the HOAG’s
choose the Gaussian radial basis function, k2 (·, ·).                    convergence speed, we learned the performance curve before
   Metalearning Dataset The only open metalearning dataset               the HOAG experiments. Based on the Lipschitz constant in
we can find is the metalearning dataset in auto-sklearn[1].              [0, 1], the step length in HOAG is fixed to 10−3 in all the
This metalearning dataset is built by the OpenML dataset.                experiments.
To make our experiments persuasive, we randomly delete                      Above benchmarks would show that accSMBO is better
20% contents in this dataset, because our experiment dataset             than the widely used method (random search, grid search),
is also from OpenML. We named our dataset as the half-                   gradient-based method (HOAG) and other SMBO algorithm(
metalearning dataset. The half-metalearning dataset cannot be            SMAC).
used in offering the best initial hyperparameters for it misses
the best hyperparameters for some situations. However, half-             B. Experimental results and analyses
metalearning dataset still can be used in our metaAC function               Figure 5 shows the experimental results on the pc4 dataset.
for it does not lose the trend character.                                Our methods achieve the fastest convergence speed and find
   Comparison with other hyperparameter optimisation                     the best hyperparameter, while the output from SMAC is
methods In this section, we compare accSMBO against the                  suboptimal. Our method achieves convergence 400% faster in
following five existing hyperparameter optimisation methods.             epoch than SMAC. When the dataset scale is small, the perfor-
To make the convergence process clear, we turn off metalearn-            mance curve characteristic is unstable and has a multimodal
ing technology in choosing initial value step and all initial            function. HOAG also results in poor performance. The output
value λ is set as 1.                                                     of HOAG does not appear to have converged. The performance

curve on the pc4 dataset is unstable and non-smooth, and the                                                    VII. C ONCLUSION
gradient information does not indicate the trend of the curve.                           In this paper, we use two methods to accelerate SMBO.
Because HOAG uses an accurate gradient as its optimisation                             1) We use a gradient-based multikernel GP to build ML ,
direction, it fails to find the best hyperparameter in this case.                      2) We design and use the metaAC function. In the exper-
Bayesian optimisation shows its advantages here because it                             iments, our methods achieved state-of-the-art performances,
ignores the ”noise” in the curve. Our method finds the curve                           converging 140% to 300% faster than SMAC algorithm on the
function tendency and approximates the gradient more quickly.                          pc4, real-sim and rcv1 datasets. In many cases, our method
                                                                                       outperformed the previous best hyperparameter optimisation
                           Figure 5. Algorithm performances on the Pc4 dataset         approach.
                                                                             SMAC
                                              pc4 dataset
                                                                             Grid                                   R EFERENCES
                     0.23                                                    Random
                    0.225
                                                                             HOAG       [1] C. Thornton, F. Hutter, H. H. Hoos, and K. Leytonbrown, “Auto-weka:
                                                                             accSMBO        combined selection and hyperparameter optimization of classification
   Logloss
                     0.22                                                                   algorithms,” knowledge discovery and data mining, pp. 847–855, 2013.
                    0.215                                                               [2] F. Hutter, H. H. Hoos, and K. Leyton-Brown, “Sequential model-
                     0.21
                                                                                            based optimization for general algorithm configuration,” in Learning
                                                                                            and Intelligent Optimization - International Conference, Lion 5, Rome,
                             1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21
                                                                                            Italy, January 17-21, 2011. Selected Papers, 2012, pp. 507–523.
                                                      Epoch                             [3] D. R. Jones, M. Schonlau, and W. J. Welch, “Efficient global optimiza-
                                                                                            tion of expensive black-box functions,” Journal of Global Optimization,
                                                                                            vol. 13, no. 4, pp. 455–492, 1998.
   Figure 6 shows the experimental results on the rcv1 dataset.                         [4] R. G. Regis and C. A. Shoemaker, “Constrained global optimization of
                                                                    SMAC
Again, our methods achieve      Rcv1 the
                                     datasetfastest convergence          speed              expensive black box functions using radial basis functions,” Journal of
                                                                    Grid                    Global Optimization, vol. 31, no. 1, pp. 153–171, 2005.
      0.19 the best hyperparameter. In this case, the perforand find                                                            Random
                                                                   SMAC                 [5] H. Mendoza, A. Klein, M. Feurer, J. T. Springenberg, and F. Hutter,
      0.17                      pc4 dataset
mance0.15 curve   characteristics  are   relatively   stable    andGrid
                                                                      close to
                                                                    HOAG                    “Towards automatically-tuned neural networks.” pp. 58–65, 2016.

   LoglossLo Loss
        0.23                                                       Random
                                                                    accSMBO             [6] E. Hazan, A. Klivans, and Y. Yuan, “Hyperparameter optimization: A
a unimodal
      00.225
        13      function.  The   output    from   HOAG       is  suboptimal.
                                                                   HOAG
                                                                   accSMBOthan              spectral approach,” 2017.
Our 0.11
     method
        0.22
                 achieves  convergence      140%     faster   in  epoch                 [7] L. Li, K. Jamieson, G. Desalvo, A. Rostamizadeh, and A. Talwalkar,
      0.09
HOAG      and   200%  faster  in  epoch   than   SMAC.      SMAC’s
       0.215 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21
                                                                         result             “Hyperband: A novel bandit-based approach to hyperparameter optiis almost    the same  as a random     search   because    SMAC       requires              mization,” Journal of Machine Learning Research, vol. 18, pp. 1–52,
        0.21                            Epoch                      SMAC                     2016.
more sample                     pc4 dataset
               1 2points
                    3 4 5 to6 build
                               7 8 9 the
                                       10 11information      for
                                             12 13 14 15 16 17     the20 21
                                                                18 19
                                                                   Grid  entire         [8] M. Feurer, A. Klein, K. Eggensperger, J. T. Springenberg, M. Blum,
curve. 0.23                             Epoch
                                                                   Random                   and F. Hutter, “Efficient and robust automated machine learning,” pp.
                                                                             HOAG
                    0.225                                                                   2962–2970, 2015.
                                                                             accSMBO
     Logloss
                     0.22Figure 6.                                                      [9] J. Bergstra and Y. Bengio, “Algorithms for hyper-parameter optimiza-
                                     Algorithm performances on the rcv1 datasetSMAC
                                            Real sim dataset                                tion,” in International Conference on Neural Information Processing
     0.215                                                           Grid
                                                                    SMAC
     0.13                       Rcv1 dataset                                                Systems, 2011, pp. 2546–2554.
       0.21
     0.12                                                            Random
                                                                    Grid               [10] J. Snoek, H. Larochelle, and R. P. Adams, “Practical bayesian optimiza-
     0.19
     0.11                                                            HOAG                   tion of machine learning algorithms,” in International Conference on

       LogLoss
              1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19       Random
                                                                        20 21

   LogLoss
     0.17
      0.1
                                          Epoch
                                                                     accSMBO
                                                                    HOAG                    Neural Information Processing Systems, 2012, pp. 2951–2959.
     0.15
     0.09                                                           accSMBO            [11] P. Brazdil, “Metalearning: Applications to data mining,” Cognitive
     0 .13
     0.08                                                                                   Technologies, 2009.
     0.07
     0.11
                                                                                       [12] F. Pedregosa, “Hyperparameter optimization with approximate gradient,”
     0.09 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21                             in International Conference on International Conference on Machine
                                                                    SMAC
             1 2 3 4 5 6 7 Rcv1  8 9dataset
                                       10Epoch
                                           11 12 13 14 15 16 17 18 19
                                                                    Grid20 21               Learning, 2016, pp. 737–746.
     0.19                                                                              [13] C. B. Do, C. S. Foo, and A. Y. Ng, “Efficient multiple hyperparameter
                                         Epoch                      Random
     0.17                                                                                   learning for log-linear models,” in International Conference on Neural
                                                                    HOAG

    Lo Loss
     0.15                                                                                   Information Processing Systems, 2007, pp. 377–384.
                                                                    accSMBO
     0 13                                                                              [14] J. Wu, M. Poloczek, A. G. Wilson, and P. I. Frazier, “Bayesian
  Figure
     0.11   7   shows   the   experimental        results   on  the    real-sim             optimization with gradients,” 2017.
                                                                    SMAC
dataset.
     0.09Here, HOAG and         our
                              Real    methods achieve the fastest
                                   sim dataset                              con-       [15] J. Vanschoren, “Meta-learning: A survey,” 2018.
                                                                    Grid
vergence
     0.13 speed
             1 2 3 and
                     4 5 find
                            6 7 the
                                 8 9best
                                       10 11hyperparameter.
                                              12 13 14 15 16 17 18 accSMBO
                                                                    19 20 21           [16] H. J. Kushner, “A new method of locating the maximum point of an
     0.12                                                           Random                  arbitrary multipeak curve in the presence of noise,” Journal of Basic
achieves
     0.11 convergence 300% faster in epoch than SMAC.
                                         Epoch
                                                                    HOAG                    Engineering, vol. 86, no. 1, pp. 97–106, 1964.
    LogLoss
       0.1                                                          accSMBO            [17] N. Srinivas, A. Krause, S. M. Kakade, and M. Seeger, “Gaussian process
     0.09                                                                                   bandits without regret: An experimental design approach,” 2009.
     0.08Figure  7. Algorithm  performances    on  the real-sim dataset
                                                                                       [18] C. E. Rasmussen and H. Nickisch, Gaussian Processes for Machine
     0.07                                                            SMAC
                                            Real!sim dataset                                Learning (GPML) Toolbox. JMLR.org, 2010.
                            1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21
                                                                          Grid         [19] Y. Jie, K. Chen, and M. M. Rashid, “A bayesian model averaging based
                    0.13
                    0.12                              Epoch                  Random         multi-kernel gaussian process regression framework for nonlinear state
                    0.11                                                     HOAG           estimation and quality prediction of multiphase batch processes with
    LogLoss
                     0.1                                                     accSMBO        transient dynamics and uncertainty,” Chemical Engineering Science,
                    0.09                                                                    vol. 93, no. 4, pp. 96–109, 2013.
                    0.08                                                               [20] J. C. Duchi, Introductory Lectures on Stochastic Convex Optimization.
                    0.07                                                                    Park City Mathematics Institute, Graduate Summer School Lectures,
                            1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21           2016.
                                                      Epoch
