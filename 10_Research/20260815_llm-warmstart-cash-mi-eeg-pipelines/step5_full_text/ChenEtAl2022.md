---
citation_key: "ChenEtAl2022"
title: "Towards Learning Universal Hyperparameter Optimizers with Transformers"
authors: "Yutian Chen; Xingyou Song; Chansoo Lee; Zi Wang; Qiuyi Zhang; D. Dohan; Kazuya Kawakami; Greg Kochanski; Arnaud Doucet; Marc’Aurelio Ranzato; Sagi Perel; Nando de Freitas"
year: 2022
doi: "10.48550/arxiv.2205.13320"
source: "arXiv (2205.13320)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
conversion: "pdftotext -layout (automated)"
---

# Towards Learning Universal Hyperparameter Optimizers with Transformers

Towards Learning Universal Hyperparameter
                                                      Optimizers with Transformers

                                                    Yutian Chen1 , Xingyou Song2 , Chansoo Lee2 , Zi Wang2 , Qiuyi Zhang2 ,
                                                            David Dohan2 , Kazuya Kawakami1 , Greg Kochanski2 ,
                                                    Arnaud Doucet1 , Marc’aurelio Ranzato1 , Sagi Perel2 , Nando de Freitas1
                                                                   1
                                                                     Deepmind, 2 Google Research, Brain Team

arXiv:2205.13320v2 [cs.LG] 14 Oct 2022
                                                                                      Abstract
                                                  Meta-learning hyperparameter optimization (HPO) algorithms from prior experi-
                                                  ments is a promising approach to improve optimization efficiency over objective
                                                  functions from a similar distribution. However, existing methods are restricted to
                                                  learning from experiments sharing the same set of hyperparameters. In this paper,
                                                  we introduce the O PT F ORMER, the first text-based Transformer HPO framework
                                                  that provides a universal end-to-end interface for jointly learning policy and func-
                                                  tion prediction when trained on vast tuning data from the wild, such as Google’s
                                                  Vizier database, one of the world’s largest HPO datasets. Our extensive experiments
                                                  demonstrate that the O PT F ORMER can simultaneously imitate at least 7 different
                                                  HPO algorithms, which can be further improved via its function uncertainty es-
                                                  timates. Compared to a Gaussian Process, the O PT F ORMER also learns a robust
                                                  prior distribution for hyperparameter response functions, and can thereby provide
                                                  more accurate and better calibrated predictions. This work paves the path to future
                                                  extensions for training a Transformer-based model as a general HPO optimizer.

                                         1   Introduction
                                         The emergence of public machine learning data platforms such as OpenML [1] and hyperparameter
                                         optimization (HPO) services such as Google Vizier [2], Amazon SageMaker [3] and Microsoft
                                         Azure [4] have made large-scale datasets containing hyperparameter evaluations accessible. For our
                                         use-case in this paper, Google Vizier is the de-facto HPO service across Google, having optimized
                                         some of Google’s largest products and research efforts, and contains a collection of valuable tuning
                                         data within the last 5 years. While there is growing interest in leveraging such data to meta-learn
                                         hyperparameter optimization algorithms [5–8], dealing with large datasets consisting of experimental
                                         trials in the wild can be challenging, due to large variations in HPO problems and their associated
                                         text metadata (e.g. shown later in Table 1).
                                         Thus, most meta and transfer-learning HPO methods [7–16] consider a restrictive setting where
                                         all tasks must share the same set of hyperparameters so that the input data can be represented as
                                         fixed-sized vectors. Consequently, such methods only exploit a small portion of the available data to
                                         learn priors. This drawback is more severe for large datasets which contain significant amounts of
                                         useful information.
                                         To overcome these limitations, we introduce the O PT F ORMER, a general hyperparameter optimization
                                         framework based on Transformers [17]. Transformers have demonstrated excellent performance
                                         in many data tasks, ranging from natural language [18], images [19, 20], biological data [21, 22],
                                         code [23, 24], and control [25, 26]. Here, we investigate how to use a Transformer as a universal
                                         interface for modelling experimental data and learn HPO algorithms, as given a sufficient amount of
                                         data, a Transformer can potentially learn a more complex prior distribution than standard Bayesian
                                         Optimization (BO) with Gaussian Processes (GPs), especially as the Transformer possesses certain
                                         computational advantages over GPs for large datasets.
                                            Code: https://github.com/google-research/optformer. Google AI Blog: https://
                                         ai.googleblog.com/2022/08/optformer-towards-universal.html.

                                         36th Conference on Neural Information Processing Systems (NeurIPS 2022).

                                                                                          op                            op
                                               lr= opt=      acc                   lr=       t=“      acc        lr=       t=“
                                                        “S
                                                  1e-
                                                     3     GD =0.6                    1e-
                                                                                          4
                                                                                                Ad
                                                                                                   am
                                                                                                         =0
                                                                                                            .7
                                                                                                                    3e-
                                                                                                                        4
                                                                                                                              Ad
                                                                                                                                 am
                                                             ”                                       ”                             ”

                                                                         OptFormer

           co
             n
                      op op
                         t_t t_k
                                                               lr= opt=         acc                 op
           me vne
             tric t on
                            yp w.
                                                                        “S                   lr=       t=“       acc        lr=
                 s:
                              e,
                                 ca lr, do                        1e-      GD      =0           1e-       Ad        =0         3e-
                    ac cifar       teg ub
                                        ori le,                      3                .6           4         am        .7         4
                      cu 10
                        rac ,              ca [1e
                                             l, [
                                                                              ”                                ”
                            y                    “S -6, 1
                                                   GD e-
                                                      ”, “ 2];
                                                          Ad
                                                            am
                                                               ”]
                        Metadata                                         Trial 1           …           Trial t

Figure 1: Illustration of the O PT F ORMER model over a hyperparameter optimization trajectory. It is
trained to predict both hyperparameter suggestions (in green) and response function values (in red).

We introduce a serialization scheme to convert a combination of any metadata and an optimization
trajectory into text, represented as a sequence of tokens, and formulate the HPO task as a sequence
modeling problem. We adopt a supervised learning approach, by learning to predict parameters and
hyperparameter response functions from offline tuning data (See Fig. 1). In order to further improve
optimization performance, we augment the model by utilizing its own function prediction during
inference (Section 4.3). Extensive experiments on both public and private datasets demonstrate the
O PT F ORMER’s competitive tuning and generalization abilities.
In summary, our contributions are as follows:
• We formulate, to the best of our knowledge, the first meta-learning HPO framework to learn both
  policy and function priors from data across different search spaces.
• The O PT F ORMER is capable of learning the behaviors of 7 diverse blackbox optimization algorithms
  relying on a broad class of methods (non-adaptive, evolutionary, and Bayesian).
• Furthermore, the O PT F ORMER learns the prior over objective functions and provides both accurate
  and well calibrated predictions, in many cases significantly surpassing GPs in log-predictive
  likelihood and expected calibration error (ECE) [27].
• Lastly, O PT F ORMER policies augmented with model-based optimization, such as the use of
  Expected Improvement acquisition functions, are competitive HPO algorithms. To the best of our
  knowledge, this is the first time Transformers are augmented with acquisition functions for online
  adaptation.

2     Preliminaries
2.1   Meta-learning for hyperparameter optimization

HPO aims to find a set of hyperparameters x from search space X to maximize a model performance
metric, y = f (x), often referred to as a response function. Table 1 shows an example of HPO
experimental data. Following the HPO nomenclature [2, 28], an experimental study consists of
metadata (m) and a history of trials (h). The metadata contains arbitrary unstructured information,
including but not limited to descriptions of the problem, optimization algorithm, names, types and
value ranges of hyperparameters. The history after t trials, ht = (x1 , y1 , . . . , xt , yt ), contains a
sequence of trials, each of which consists of a parameter suggestion x and function value y.
The goal of the meta-learning approach for HPO is to learn the shared knowledge among the objective
functions f from a dataset of multiple tuning experiments represented as studies and to obtain an
optimal HPO algorithm for new hyperparameter tuning tasks from a similar distribution to those in
the dataset.
An HPO algorithm π maps the metadata and history to a distribution over hyperparameter suggestions,
i.e. π(xt+1 |m, ht ). Using the terminology of offline RL [29], we refer to the algorithm used to
generate the trajectories in a dataset as the behavior policy πb .

                                                                                     2

We primarily consider search spaces X with a fixed number D of hyperparameters per task, and
hence x = (x(1) , . . . , x(D) ), with each hyperparameter x(d) being of type DOUBLE, INTEGER,
DISCRETE, or CATEGORICAL (see Appendix A.1 for details). More complex search spaces can be
supported as discussed in Section 7.
                                                                  Table 1: Example of a study
2.2 Transformer model                                             (m, h) with two parameters and
                                                                  two trials. Metadata m appears in
The Transformer model is an efficient attention-based neural blue and history h in purple.
network architecture for sequence modeling [17]. We adopt
the T5 Transformer encoder-decoder architecture [30]. The           "name": "convnet on cifar10",
encoder and decoder each consist of a stack of multi-head self- "metric": "accuracy",
attention layers which construct pairwise interactions between      "goal": "MAXIMIZE",
positions, followed by position-wise feed-forward networks. The     "algorithm": "random_search",
encoder converts a sequence of input token representations m, "parameter": {
to a sequence of continuous embeddings, which is fed to the         "name": "opt_kw.lr",
decoder to generate a sequence of output tokens h one element       "type": "DOUBLE",
at a time (see Fig. 1).                                             "min_value": 1e-6,
                                                                     "max_value":       1e-2,
                                                                     "scale_type":       "LOG"
3   Related work                                                     }
                                                                     "parameter": {
There has been a rich set of works in meta-learning and trans-       "name":     "opt_type",
fer learning by modifying specific core components of the BO         "type":     "CATEGORICAL",
pipeline, such as the acquisition function or the GP, in order to    "categories":       ["SGD", "Adam"],
tackle BO’s myopic behavior, or obtaining more information           }
from similar tasks. For instance, approaches include learning        "trial" {
new acquisition functions [31], multi-task BO [7–13] and BO          "parameter":       {
for transfer learning using contextual GPs [14–16]. [32] also        "opt_kw.lr":       0.0021237573,
studies the use of meta-BO for hyperparameter tuning tasks in        "opt_type":       "SGD"
machine learning. However, all of these works consider a fixed       }
search space.                                                        "metric":     {
A more radical meta-learning approach to non-differentiable          "accuracy":       0.69482429,
optimization trains recurrent neural networks (RNNs) as neural       }}
optimizers from scratch. [33] first proposed training an RNN         "trial" {
with gradient descent to optimize blackbox functions and hy-         "parameter":       {
perparameters while [34, 35] train RNNs using reinforcement          "opt_kw.lr":       0.00038292234,
learning (RL) to solve RL tasks. Unfortunately, prior works are      "opt_type":       "Adam"
limited to fixed search spaces and only use online generated data,   }
constraining the training objectives to be cheaply computable.       "metric":     {
                                                                     "accuracy":       0.71642583
In this work, we wish to overcome the limitations of previous       }}
works by exploiting the Transformer architecture. Numerous
works have demonstrated Transformers’ strong capabilities in flexible symbolic and numerical manipulation. On the symbolic side, Transformers have been shown able to manipulate symbolic
mathematical expressions [36–38] and generate code [23, 24]. Furthermore, on the numerical side,
Transformers have also been shown able to perform linear algebra computations [39], Bayesian
Inference [40], and offline RL [25, 26, 41]. For AutoML specifically, [42] has demonstrated Transformers’ and analogous graph neural networks’ abilities to use dataset descriptions and metadata to
generate classification and data preprocessing pipelines. However, to date, there has been little effort
in attacking the full problem of hyperparameter tuning in the blackbox optimization setting. In this
paper, the challenging task of learning algorithms from blackbox optimization trajectories can be
seen as a significant extension of both symbolic and numerical manipulation. Since the underlying
algorithm can be composed of multiple symbolic and mathematical operations with unbounded
complexity, the model must infer potentially very complex behavior over long horizons.

4   Universal interface and model for hyperparameter optimization
In this section, we provide a universal interface for modeling HPO studies with mixed textual and
numerical information as a sequence of discrete tokens. We train our O PT F ORMER as a generative

                                                   3

Table 2: Serialized study after preprocessing and tokenization. Metadata m appears in blue, normalized and quantized values of xt in green, and yt in red.
                               <name>:"convnet on cifar10",<metric>:"accuracy",<goal>:<MAXIMIZE>,
                               <algorithm>:"random_search"
                               &<name>:"opt_kw.lr",<type>:<DOUBLE>,<min_value>:1e-6,<max_value>:1e-2,
 After preprocessing           <scale_type>:<LOG>
                               &<name>:"opt_type",<type>:<CATEGORICAL>,<categories>:["SGD", "Adam"]
                               <831><0>* <0>| <645><1>* <999>
                               name : " con v net on ci far 10 ", metric : " acc u racy ",
                               goal : MAXIMIZE , algorithm : " random _ search "
                               & name : " op t _ kw . lr ", type : DOUBLE , min_value : 1 e -6 ,
      Subwords                 max_value : 1 e -2 , scale_type : LOG
  after tokenization           & name : " op t _ type ", type : CATEGORICAL ,
                               categories : [ " SG D ", " A dam " ]
                               831 0 * 0 | 645 1 * 999

model on a given dataset and explain how to use the O PT F ORMER’s parameter and function prediction
abilities to implement an HPO policy.

4.1   Study tokenization

To generalize over HPO problems of different parameter sizes, types, and metadata, we propose to
serialize the study as a one-dimensional textual sequence, also advocated in [26]. Unfortunately, a
naive serialization approach, e.g. via JSON [43], will produce unnecessarily long sequences.
To improve scalability, we compress the textual representation of metadata m by removing redundant phrases and punctuation (e.g., "parameter", quotes) and encoding keywords (e.g., "name",
"algorithm") and enumerating types (e.g. "DOUBLE") into single tokens.
For the historical sequence h, we convert every DOUBLE and INTEGER parameter along with every
function value into a single token, by normalizing and discretizing them into integers, with an
quantization level of Q = 1000; e.g.
                    x̄ = int[xnorm · Q], where xnorm = (x − xmin )/(xmax − xmin ).                        (1)
The range of x is defined by the search space and the range of y is obtained from observed values in
h. For other types, we use the index in their value set.
The shortened text string is then converted to a sequence of tokens via the SentencePiece tokenizer [44]
(see Table 2 for an example). Everyh trial is represented by text,
                                                                 i which is represented as a sequence of
                                       (1)        (D)
normalized and quantized tokens, x̄t , . . . , x̄t , ?, ȳt , "|" , where the token ? separates parameter
and function values and "|" separates trials. See Appendix A.2 for further details on tokenization.

4.2   Model and training loss

After tokenization, the converted historical sequence is as follows:
                      h                                                                              i
                        (1)  (2)           (D)                         (1)   (2)           (D)
               h̄t = x̄1 , x̄1 , . . . , x̄1 , ?, ȳ1 , "|", . . . , x̄t , x̄t , . . . , x̄t , ?, ȳt .   (2)

We can now apply a Transformer model to learn the conditional distribution of tokens in h̄ using
the chain rule, given the metadata m̄, as depicted in Fig. 1. The joint distribution is presented in
Appendix D.1.
Given a dataset D of hyperparameter optimization studies, we train the O PT F ORMER by maximizing
the weighted log-likelihood for each study (m, h) ∼ D:

                         L(θ; m, h) = n wn log Pθ (h̄(n) |m̄, h̄(1:n−1) ),
                                        P
                                                                                              (3)

with wn = 0 if h̄(n) ∈ {?, "|"} and wn = 1 otherwise. That is, we mask out the separator tokens (?,
"|") and predict parameter x̄ and function tokens ȳ only. Note that h̄(n) denotes the n-th token, that
is the n-th element of the list in Equation (2), and h̄(1:n−1) denotes all tokens up to the (n − 1)-th
token. Further details and data augmentations are provided in Appendix D.2.

                                                              4

4.3   Inference and decoding
                                                                               (d)
Parameter prediction: To decode the predicted parameter token x̄t back to its original parameter
range, we truncate the output distribution to the vocabulary range corresponding to valid parameter
values [0, Q) and reverse our tokenization procedure in Section 4.1. For a DOUBLE or INTEGER
parameter x, we use a piecewise constant distribution:
            pθ (x| . . . ) = Q · Pθ (x̄| . . . )/(xmax − xmin ), if x ∈ [xmin , xmax ], otherwise 0 .    (4)
For all other parameter types, x̄ corresponds to the index of the set of feasible values. Putting these
together, we may now sample parameter xt from the model’s prior distribution and thus define an
HPO policy:
                                               D
                                                       (d)             (1:d−1)
                                               Y
                       πprior (xt |m, ht−1 ) =    pθ (xt |m, ht−1 , xt         ).                   (5)
                                                       d=1
As we use a supervised learning loss, we expect πprior to approximate the behavior policy πb .
Note that traditional BO algorithms require running Bayesian inference and then conducting a global
search in the hyperparameter space with an acquisition function. Thus the runtime complexity of
making one hyperparameter suggestion is cubic in t for a typical GP-based BO method that performs
ARD each iteration [45]. In contrast, generating one suggestion by the O PT F ORMER consists of
decoding D parameter tokens with an input sequence of (D + 3)t tokens, which are then parsed into
the D parameter values, producing a runtime of O(D2 t) linear in t, with proper caching.

Function prediction: To decode the real-valued function yt from the discrete distribution
Pθ (ȳt |m̄, h̄t−1 , x̄t ), we construct the same piecewise constant distribution as in Eq. (4) with the
range [ymin , ymax ] used in tokenization. Note that the limited support of y will not be a concern
for HPO when either the range is known or we set the range large enough compared to observed
values. For more general use as a few-shot function prediction model, one could consider adopting
the Riemann Distribution in [40], which supports an unbounded range.

Augmented HPO policies with function prediction: At best, the learned policy πprior can only
perform as well as the original policy πb when using behavioral cloning. However, we can take
advantage of the model’s simultaneous function prediction ability to improve the policy with modelbased planning or offline RL techniques. While a comprehensive study of policy improvements
for Transformers is out of the scope of this work, we consider here a simple yet effective policy
improvement operator: sampling M = 100 candidate suggestions from πprior and choosing the
suggestion with the highest score defined by an acquisition function u(·) as follows:
                                                                                 i.i.d.
       πu (xt |m, ht−1 ) = argmax u(pθ (·|m, ht−1 , x(i) )), with x(i) ∼ πprior (x|m, ht−1 ).            (6)
                              {x(i) }M
                                     i=1

Common acquisition functions include Expected Improvement (EI), Probability of Improvement
(PI), Upper Confidence Bound (UCB), and Thompson Sampling, see for example [46]. At a high
level, this approach to combining imitated policies with function prediction is reminiscent of the idea
behind the offline RL approach of BCQ [47].
Because we apply a linear mapping from the original y value to the quantized value ȳ before discretization, we can simply define the acquisition functions on the discrete distribution Pθ (ȳ|m̄, h̄t−1 , x̄t ) as
follows:
                              uEI (x|ȳ ∗ ) = EPθ (ȳ|m,ht−1 ,x) [max(ȳ − ȳ ∗ , 0)] ,                  (7)
                            uUCB (x|α) = Quantile(Pθ (ȳ|m, ht−1 , xt ), α) ,                            (8)
                                             X
                             uPI (x|ȳ ∗ ) =   Pθ (ȳ|m, ht−1 , x) ,                                     (9)
                                             ȳ>ȳ ∗

                                 uTS (x) = ȳ, with ȳ ∼ Pθ (ȳ|m, ht−1 , xt ) ,                        (10)
        ∗
where ȳ = maxτ ≤t−1 ȳτ in EI and PI is the threshold to measure improvement. We define the UCB
acquisition function with a quantile parameter α. Our TS acquisition is defined as a sampled function
value at a given location from the marginal predictive distribution. It is inspired by the traditional
Thompson Sampling method [45] but different in that the correlation between different locations is
ignored.

                                                             5

5   Data
Training the O PT F ORMER requires HPO studies with optimization trajectories. The most natural
dataset we possess is the entire Google Vizier [2] database, one of the world’s largest collections of
real world hyperparameter tuning studies, which we denote as RealWorldData. There are around
750K studies, each with on average 300 trials, covering a vast class of production and machine
learning applications at Google, ranging from vision, speech, NLP and robotics, and representing one
of the most representative distributions of HPO tasks for machine learning models in practice. These
studies were generated with a mixture of non-adaptive, evolutionary, and BO algorithms. However,
as the dataset does not contain sufficient algorithm information, we have to treat the corresponding
behavior policy as a randomly mixed algorithm πb .
In addition, we create two new datasets based on public benchmarks. HPO-B is the largest public
benchmark for HPO containing about 1.9K tuning tasks, most of which use one of 16 shared search
spaces. In the continuous evaluation setting, it fits an XGBoost model to the trial data of every tuning
task as the objective function. For further control over specific function dimensions and properties, we
use the blackbox optimization benchmark BBOB [48], consisting of 24 types of synthetic functions
with customizable properties (dimension sizes, rotations, shifts, discretizations, noise types) we
randomize over.
For each of the two public benchmarks (HPO-B and BBOB), we apply a fixed set of 7 HPO
algorithms to generate a dataset of optimization trajectories. In contrast to RealWorldData, we specify
the algorithm name in the metadata m as part of the conditioning input for our model. The controlled
algorithms used are: (1) Grid Search, (2) Shuffled Grid Search, (3) Random Search, (4) Regularized
Evolution [49], (5) Hill-Climbing, (6) Eagle Strategy [50], and (7) Vizier’s GP-UCB [2]. Appendix B
contains detailed explanations of the algorithms.
Table 3: Offline training datasets considered in this study. More details are given in Appendix C
along with examples of studies in Table 5.
                          ("R") RealWorldData            ("H") HPO-B          ("B") BBOB
           #Studies                750K                      10M                  10M
        #Trials / study      300 (on average)                 120                  300
         Study source       Google’s database              Generated            Generated
              πb                  Mixed                    Controlled           Controlled
        Obj. Functions          HPO tasks                  HPO tasks            Synthetic
         Search space        Different per task     16 shared search spaces Randomized

6   Experiments
We train a single Transformer model with 250M parameters on the union of the three datasets
described above, RealWorldData, HPO-B, and BBOB (hyperparameter details in Appendix D.2).
Each dataset contains a corresponding “test” set of functions, either using synthetic functions (BBOB)
or fitting a machine learning model to obtain the objective (RealWorldData, HPO-B). We evaluate
mainly on the two natural HPO benchmarks, RealWorldData and HPO-B. The train/test subsets of
RealWorldData are split temporally to avoid information leak (see Appendix C for details).
To aggregate results across functions with different output scaling, we normalize all the test functions.
This is standard practice in the literature [2, 5, 51–54]. We define our performance metric at trial t
as the best-so-far normalized function value maxi∈{1:t} (yi − yrand )/(ymax − yrand ), where yrand
is the median of function values randomly sampled in the search space to be robust to outliers, and
ymax is the maximum, if known, or best value found by any algorithm. For the HPO-B benchmark,
we use the recommended bounds provided in [5]. We also consider other metrics when comparing
different algorithms in Appendix E.3, including the performance profile and average ranking. We
find our results are consistent over different metrics.
Because the O PT F ORMER is trained to predict the conditional distributions of parameter and function
values, we would like to answer the following questions when evaluating on unseen test problems:

                                                   6

          1.0     Regularized Evolution                                     1.0    Regularized Evolution
                                 OptFormer

                                                 Best normalized function
          0.8                    Target policy                              0.8                                                             Grid Search
          0.6                                                                                                                               Shuffled Grid Search
Density   0.4
                                                                            0.6                                                             Random Search
                                                                                                                                            Eagle Strategy
          0.2                                                               0.4                  Target policy                              Regularized Evolution
                                                                                                 OptFormer                                  Hill-Climbing
          0.0                                                               0.2                                                             Vizier
                             x                                                                Trial                                 1.00
                       Hill-Climbing                                        1.0           Hill-Climbing
                                                                                                                                    0.95

                                                                                                                 OptFormer policy
          1.5                    OptFormer

                                                 Best normalized function
                                 Target policy                              0.8
                                                                                                                                    0.90
          1.0
Density                                                                     0.6
                                                                                                                                    0.85
          0.5                                                               0.4                  Target policy
                                                                                                 OptFormer                          0.800.80 0.85 0.90 0.95 1.00
          0.0     4     2    0       2     4                                0.20     20     40 60 80 100                                        Target poilcy
                             x                                                                Trial
                                                                                                                   (c) Average best function at trial 100
                (a) Policy distribution                                      (b) Optimization trajectory           with standard deviation.
 Figure 2: Comparing the performance of different algorithms outputted by the O PT F ORMER condi-
 tioned on the corresponding algorithm’s name.
 1. Can the O PT F ORMER learn to imitate multiple HPO algorithms with one model? (Section 6.1)
 2. Can the O PT F ORMER learn a good prior over hyperparameter response functions? (Section 6.2)
 3. Is the O PT F ORMER a competitive approach for HPO? (Section 6.3)

 6.1            Imitating HPO policies

 We first evaluate how well the O PT F ORMER can learn the conditional distribution of parameter
 suggestions given by the behavior policies in the dataset, and how well it can imitate multiple
 algorithms. As the algorithm’s name is contained in the metadata m, we can modify the behaviour of
 the policy πprior (xt+1 |m, ht ) simply by altering this variable. Fig. 2a compares two different policies
 to the O PT F ORMER, when it is conditioned on the corresponding policy name. We observe a good
 match between the imitated algorithms and the O PT F ORMER (additional algorithms are shown in
 Appendix E.1).
 In Fig. 2b we run target policies on the BBOB dataset’s test functions and compare the optimization
 trajectories of the algorithms and the O PT F ORMER. In Fig. 2c we compare the average and standard
 deviation of the best normalized function values at trial 100. Our model imitates most algorithms
 very accurately in both the mean and variance except for the most complicated algorithm, Vizier,
 where πprior is slightly worse in the LUNACEK benchmark. We expand on this in Appendix E.1.
 Because Vizier is the best performing HPO algorithm among all considered, the O PT F ORMER will
 imitate Vizier faithfully, although not perfectly, in the following experiments.

 6.2            Learning priors for hyperparameter response functions

 In this section, we assess the O PT F ORMER’s ability to learn the conditional distribution of the
 function value as a few-shot function regressor. Specifically, for every function in each test dataset,
 we repeatedly sample up to 200 random trials (x1 , y1 , . . . xt , yt ), t ≤ 200, and predict the conditional
 distribution p(yt |x1 , y1 , . . . , xt ). We compare with a GP model with output warping — details
 provided in Appendix B. We report the log-predictive likelihood log p(yt |xt , . . . ) in Table 4.
 As uncertainty estimation is important for HPO, we also evaluate how well the function predictive
                              R y a predictive distribution pθ (y| . . . ) matches the true distribution,
 distribution is calibrated. When
 the estimated CDF F (y) = −∞ pθ (y 0 | . . . )dy 0 will be uniformly distributed. In Fig. 3, we plot the
 cumulative histogram of F (y) on RealWorldData test set and check the deviation from the diagonal
 line to assess goodness-of-fit as proposed by Rosenblatt [55]. The O PT F ORMER has a smaller

                                                                                             7

                                                                                                                                           1.0         GP

                                                                                                                 Percentage of data with
    Table 4: Log-predictive likelihood (with 1-std. stan-                                                                                  0.8         OptFormer
    dard error, higher is better (↑)) and ECE (percentage
    of error, lower is better (↓)) on RealWorldData and                                                                                    0.6

                                                                                                                       CDF(y) F
    HPO-B test sets.
                        Log-predictive likelihood ↑                                                                                        0.4
         Model         RealWorldData        HPO-B
           GP            0.83(0.06)       4.03(0.04)
                                                                                                                                           0.2
     O PT F ORMER        2.12 (0.05)      6.16 (0.04)                                                                                      0.0
                            ECE (percent %) ↓                                                                                                    0.0 0.2 0.4 0.6 0.8 1.0
         Model         RealWorldData        HPO-B                                                                                                       CDF level F
           GP            5.34 (0.06)      2.39 (0.05)                                                              Figure 3: Cumulative histogram of pre-
     O PT F ORMER        1.11 (0.02)      1.89 (0.01)                                                              dicted CDF(y) on RealWorldData test
                                                                                                                   set.

                                        RealWorldData                                                            HPO-B
                                                                                                0.98                                                         Random Search
                           0.9                                                                                                                               GP-UCB

Best normalized function                                             Best normalized function
                                                                                                0.96                                                         Vizier
                           0.8                                                                                                                               GP *
                                                                                                                                                             DGP *
                                                                                                0.94                                                         ABLR
                           0.7                                                                                                                               FSBO
                                                                                                0.92                                                         HyperBO
                                                                                                                                                             OptFormer
                           0.6                                                                                                                               OptFormer (EI)
                                 0    20   40        60   80   100                              0.900       20   40        60                     80   100
                                                Trial                                                                 Trial
    Figure 4: Higher is better. Best normalized function value averaged over 16 RealWorldData test
    functions (left) and over 86 HPO-B test functions (right) with 1-std confidence interval from 5
    runs. GP* and DGP* results are provided by [5]. The transfer learning methods ABLR, FSBO and
    HyperBO cannot be applied to RealWorldData.

    deviation than the GP almost across the entire range. We also compare calibration performance
    using the expected calibration error (ECE) [27]. Readers are referred to [27] and Appendix E.2
    for a detailed explanation of ECE. We observe from Table 4 that the O PT F ORMER achieves better
    predictive likelihood and ECE than the GP on both datasets.

    6.3                          Augmenting a prior policy with function prediction

    We evaluate the O PT F ORMER as a hyperparameter optimization algorithm on two benchmarks,
    RealWorldData and HPO-B. We compare our prior policy, the O PT F ORMER, and an augmented
    policy with Expected Improvement, the O PT F ORMER (EI), against standard HPO baselines, including
    Random Search, our implementation of GP-UCB, and the well-tuned Vizier service. For HPO-B,
    we also include the GP (not to be confused with our GP-UCB) and DGP (GP with deep kernel)
    baseline results provided by the original paper [5]. Additionally, we include three recent transfer-
    learning methods based on multi-task GP models: ABLR [12, 56], FSBO [7], and HyperBO [57, 58]
    (implementation details in Appendix B). Please note that all of these transfer learning methods require
    learning GPs on multiple tasks sharing the same search space. Therefore, none of them apply to the
    RealWorldData benchmark where every study has its own search space.

    We show the trajectory of the best normalized function value averaged over all functions from each
    benchmark in Fig. 4. While the prior policy returned by the O PT F ORMER does not perform as well
    as Vizier, it is comparable or slightly better than our GP-UCB baseline and ABLR.

                                                                                                        8

                                              Training data                                                                             HPO-B
                           0.98                                                                                    0.98

Best normalized function                                                                Best normalized function
                           0.96                                                                                    0.96

                           0.94                                HyperBO                                             0.94
                                                               OptFormer (EI)                                                                       OptFormer
                           0.92                                OptFormer-H (EI)                                    0.92                             OptFormer (EI)
                                                               OptFormer-R (EI)                                                                     OptFormer-min
                                                               OptFormer-B (EI)                                                                     OptFormer-min (EI)
                           0.900        20      40           60       80      100                                  0.900     20        40           60       80      100
                                                     Trial                                                                                  Trial
                                    (a) Ablation on training data.                                                         (b) Ablation on metadata.
                                               Prior policy                                                                       Acquisition function
                           0.98                                                                                    0.98

Best normalized function                                                                Best normalized function
                           0.96                                                                                    0.96

                           0.94                                                                                    0.94                              HyperBO
                                                                                                                                                     OptFormer
                                                             OptFormer                                                                               OptFormer (EI)
                           0.92                              OptFormer (EI)                                        0.92                              OptFormer (TS)
                                                             Random Search                                                                           OptFormer (PI)
                                                             Random Search (EI)                                                                      OptFormer (UCB)
                           0.900        20      40           60       80      100                                  0.900     20        40           60       80      100
                                                     Trial                                                                                  Trial
                                   (c) Ablation on the prior policy.                                                (d) Ablation on the acquisition function.
    Figure 5: Best normalized function values averaged over HPO-B test functions with 1-std confidence
    interval. Ablation curves are shown with markers. (a) The more similar the training dataset, the
    better the transfer. Here, the suffix with "H", "R", "B" indicates training on HPO-B, RealWorldData,
    and BBOB respectively. (b) Removing the majority of metadata hurts function prediction. (c) The
    prior policy improves performance with or without the Expected Improvement acquisition function.
    (d) All acquisition functions provide a significant improvement.

    The most significant improvement is achieved when we augment our prior policy with the Expected
    Improvement acquisition function. The resulting O PT F ORMER (EI) outperforms all baselines across
    the board on both benchmarks. This illustrates that the O PT F ORMER is able to learn the distribution
    of functions in the meta-training split and transfers to the meta-testing split.
    It is worth noting that to run 100 trials for about half of the test functions, the required history token
    sequence is longer than the 1024-token length used in training, with the maximum length about twice
    the training horizon. The superior performance of the O PT F ORMER (EI) thus demonstrates its good
    generalization performance beyond the optimization horizon it is trained for.

    6.4                       Ablations

    We provide further ablations on three important components for our policy:

    Training dataset. To understand the impact of the training datasets on the O PT F ORMER, we train
    three variants on individual datasets (O PT F ORMER-"R","H","B" respectively for RealWorldData,
    HPO-B, BBOB) and study their transfer learning performances on HPO-B. Fig. 5a verifies that
    training with in-domain data ("H") gives better performance than training over the more diverse
    across-domain RealWorldData HPO dataset ("R"), which is better than training over the synthetic
    BBOB data ("B"). Nonetheless, training on RealWorldData is enough to give comparable performance
    to the best transfer learning baseline at the end of 100 trials. Lastly, training on all of the datasets
    (O PT F ORMER) gives a further advantage over O PT F ORMER -H. This suggests that more data does
    not hurt the model’s performance but rather may improve it, even if the extra data is out-of-domain.

                                                                                    9

Meta-data m. We have demonstrated how the O PT F ORMER’s behavior can be controlled by the
algorithm name in metadata m in Section 6.1. Here we study whether the O PT F ORMER learns
to depend on other meta information. At inference time, we provide minimum information in m
(O PT F ORMER-min) by excluding all textual information and parameter value ranges. We only keep
necessary information such as parameter types and algorithm names. Fig. 5b shows that the prior
policy of O PT F ORMER-min performs comparably with the O PT F ORMER, partly due to the use of data
augmentation (see Appendix D.2). The augmented policy O PT F ORMER-min (EI) (dashed orange)
improves upon the prior policy but is significantly worse than the full model, suggesting that the
missing metadata impacts the model’s predictions on function values.

Prior policy. Section 6.3 demonstrated the benefit of adding an acquisition function to the prior
policy. A natural question is whether a good prior policy is needed at all. In Fig. 5c, we replace the
prior policy in the O PT F ORMER (EI) with random search (Random Search (EI), dashed blue line).
While adding Expected Improvement still improves this random search policy’s performance, the
best method requires both a good prior policy and the acquisition function.

Choice of acquisition function. In Fig. 5d, we compare the Expected Improvement (EI) with
Thompson Sampling (TS), Probability of Improvement (PI), and Upper Confidence Bound (UCB)
with a confidence level of 0.9. We observe that the prior policy is improved by all the acquisition
functions. Particularly, O PT F ORMER (EI) is the best among all the choices though the difference is
relatively small compared to the advantage over other baselines and O PT F ORMER prior policy. We
provide additional analysis with results on both the RealWorldData and HPO-B datasets, as well as
other evaluation metrics in Appendix E.4.

7   Limitations and future extensions
We list a few limitations of this work and discuss some potential extensions. (1) We did not consider
parameters that do not always apply or are subject to dynamic constraints depending on other
parameter values. Such parameters are common in AutoML [59] and NAS applications [60]. Our
work can be extended to support these applications, by providing the conditional specifications as
text in metadata m. (2) We also considered only sequential optimization with a batch size of 1. To
support parallel suggestions, one could apply random masking to input function value observations to
simulate scenarios with parallel pending trials [33]. (3) While we trained the Transformer to clone
the behavior policy offline, there are extensive literature on offline RL [29] that could be applied
here [25, 47, 61–64]. One could also consider meta-training acquisition functions as in [31] within
the same model and online fine-tuning as in [7, 41]. (4) We considered a single objective function,
though multiple objectives can be easily included by outputting multiple function tokens in a trial. (5)
The maximum sequence length is limited by the quadratic memory size requirement of a Transformer,
which could be mitigated with more scalable architecture variants such as Performer [65].

8   Conclusion
We presented first step to learning a universal Transformer model for hyperparameter optimization
from large scale datasets containing tuning experiments with vastly different search spaces and
experiment descriptions. By training on a diverse set of synthetic and real-world tuning trajectories,
we demonstrated the capacity of a single Transformer model to imitate 7 fundamentally different
HPO policies, learn to make well calibrated few-shot function predictions, and provide competitive
optimization performance on unseen test functions comparable with the existing, long-tried GP-based
baselines. Many extensions are readily conceivable for future exploration.

Acknowledgments
We would like to thank Chris Dyer, Luke Metz, Kevin Murphy, Yannis Assael, and Esteban Real for
providing valuable feedback during their reviews of this paper. We further thank Sebastian Pineda
Arango for technical discussions on the HPO-B benchmark and Christof Angermueller on biological
benchmarks. In addition, we thank Daniel Golovin, Daiyi Peng, Yingjie Miao, Jack Parker-Holder,
Jie Tan, Lucio Dery, and Aleksandra Faust for multiple useful conversations.

                                                  10

References
 [1] Joaquin Vanschoren, Jan N Van Rijn, Bernd Bischl, and Luis Torgo. OpenML: Networked
     science in machine learning. ACM SIGKDD Explorations Newsletter, 15(2):49–60, 2014.
 [2] Daniel Golovin, Benjamin Solnik, Subhodeep Moitra, Greg Kochanski, John Karro, and
     D. Sculley. Google Vizier: A service for black-box optimization. In ACM SIGKDD International
     Conference on Knowledge Discovery and Data Mining, pages 1487–1495, 2017.
 [3] Valerio Perrone, Huibin Shen, Aida Zolic, Iaroslav Shcherbatyi, Amr Ahmed, Tanya Bansal,
     Michele Donini, Fela Winkelmolen, Rodolphe Jenatton, Jean Baptiste Faddoul, Barbara
     Pogorzelska, Miroslav Miladinovic, Krishnaram Kenthapadi, Matthias W. Seeger, and Cédric
     Archambeau. Amazon SageMaker automatic model tuning: Scalable black-box optimization.
     CoRR, abs/2012.08489, 2020.
 [4] Deepak Mukunthu, Parashar Shah, and Wee Hyong Tok. Practical Automated Machine Learning
     on Azure: Using Azure Machine Learning to Quickly Build AI Solutions. O’Reilly Media, 2019.
 [5] Sebastian Pineda-Arango, Hadi S. Jomaa, Martin Wistuba, and Josif Grabocka. HPO-B: A large-
     scale reproducible benchmark for black-box HPO based on openml. CoRR, abs/2106.06257,
     2021.
 [6] Hyunghun Cho, Yongjin Kim, Eunjung Lee, Daeyoung Choi, Yongjae Lee, and Wonjong Rhee.
     Basic enhancement strategies when using bayesian optimization for hyperparameter tuning of
     deep neural networks. IEEE Access, 8:52588–52608, 2020.
 [7] Martin Wistuba and Josif Grabocka. Few-shot Bayesian optimization with deep kernel surro-
     gates. In International Conference on Learning Representations, 2021.
 [8] Matthias Feurer, Benjamin Letham, and Eytan Bakshy. Scalable meta-learning for Bayesian
     optimization using ranking-weighted Gaussian process ensembles. In AutoML Workshop at
     ICML, volume 7, 2018.
 [9] Kevin Swersky, Jasper Snoek, and Ryan P Adams. Multi-task Bayesian optimization. In
     Advances in Neural Information Processing Systems, 2013.
[10] Dani Yogatama and Gideon Mann. Efficient transfer learning method for automatic hyperpa-
     rameter tuning. In Artificial Intelligence and Statistics, 2014.
[11] Matthias Poloczek, Jialei Wang, and Peter Frazier. Multi-information source optimization. In
     Advances in Neural Information Processing Systems, 2017.
[12] Valerio Perrone, Rodolphe Jenatton, Matthias Seeger, and Cédric Archambeau. Scalable
     hyperparameter transfer learning. In Advances in Neural Information Processing Systems, pages
     6846–6856, 2018.
[13] Jonas Rothfuss, Vincent Fortuin, Martin Josifoski, and Andreas Krause. PACOH: Bayes-optimal
     meta-learning with PAC-guarantees. In International Conference on Machine Learning, pages
     9116–9126. PMLR, 2021.
[14] Andreas Krause and Cheng S Ong. Contextual Gaussian process bandit optimization. In
     Advances in Neural Information Processing Systems, 2011.
[15] Rémi Bardenet, Mátyás Brendel, Balázs Kégl, and Michele Sebag. Collaborative hyperparameter
     tuning. In International Conference on Machine Learning, 2013.
[16] Matthias Poloczek, Jialei Wang, and Peter I Frazier. Warm starting Bayesian optimization. In
     Winter Simulation Conference (WSC). IEEE, 2016.
[17] Ashish Vaswani, Noam Shazeer, Niki Parmar, Jakob Uszkoreit, Llion Jones, Aidan N. Gomez,
     Lukasz Kaiser, and Illia Polosukhin. Attention is all you need. In Isabelle Guyon, Ulrike von
     Luxburg, Samy Bengio, Hanna M. Wallach, Rob Fergus, S. V. N. Vishwanathan, and Roman
     Garnett, editors, Advances in Neural Information Processing Systems, pages 5998–6008, 2017.
[18] Jacob Devlin, Ming-Wei Chang, Kenton Lee, and Kristina Toutanova. BERT: pre-training of
     deep bidirectional transformers for language understanding. In Jill Burstein, Christy Doran, and
     Thamar Solorio, editors, Association for Computational Linguistics, pages 4171–4186, 2019.
[19] Alexey Dosovitskiy, Lucas Beyer, Alexander Kolesnikov, Dirk Weissenborn, Xiaohua Zhai,
     Thomas Unterthiner, Mostafa Dehghani, Matthias Minderer, Georg Heigold, Sylvain Gelly,
     Jakob Uszkoreit, and Neil Houlsby. An image is worth 16x16 words: Transformers for image

                                                 11

     recognition at scale. In International Conference on Learning Representations. OpenReview.net,
     2021.
[20] Mark Chen, Alec Radford, Rewon Child, Jeffrey Wu, Heewoo Jun, David Luan, and Ilya
     Sutskever. Generative pretraining from pixels. In International Conference on Machine
     Learning, volume 119, pages 1691–1703.
[21] Alexander Rives, Joshua Meier, Tom Sercu, Siddharth Goyal, Zeming Lin, Jason Liu, Demi Guo,
     Myle Ott, C. Lawrence Zitnick, Jerry Ma, and Rob Fergus. Biological structure and function
     emerge from scaling unsupervised learning to 250 million protein sequences. Proceedings of
     the National Academy of Sciences, 118(15):e2016239118, 2021.
[22] Ananthan Nambiar, Maeve Heflin, Simon Liu, Sergei Maslov, Mark Hopkins, and Anna Ritz.
     Transforming the language of life: Transformer neural networks for protein prediction tasks.
     Association for Computing Machinery, 2020.
[23] Yujia Li, David H. Choi, Junyoung Chung, Nate Kushman, Julian Schrittwieser, Rémi Leblond,
     Tom Eccles, James Keeling, Felix Gimeno, Agustin Dal Lago, Thomas Hubert, Peter Choy,
     Cyprien de Masson d’Autume, Igor Babuschkin, Xinyun Chen, Po-Sen Huang, Johannes Welbl,
     Sven Gowal, Alexey Cherepanov, James Molloy, Daniel J. Mankowitz, Esme Sutherland Robson,
     Pushmeet Kohli, Nando de Freitas, Koray Kavukcuoglu, and Oriol Vinyals. Competition-level
     code generation with alphacode. CoRR, abs/2203.07814, 2022.
[24] Mark Chen, Jerry Tworek, Heewoo Jun, Qiming Yuan, Henrique Ponde de Oliveira Pinto, Jared
     Kaplan, Harrison Edwards, Yuri Burda, Nicholas Joseph, Greg Brockman, Alex Ray, Raul
     Puri, Gretchen Krueger, Michael Petrov, Heidy Khlaaf, Girish Sastry, Pamela Mishkin, Brooke
     Chan, Scott Gray, Nick Ryder, Mikhail Pavlov, Alethea Power, Lukasz Kaiser, Mohammad
     Bavarian, Clemens Winter, Philippe Tillet, Felipe Petroski Such, Dave Cummings, Matthias
     Plappert, Fotios Chantzis, Elizabeth Barnes, Ariel Herbert-Voss, William Hebgen Guss, Alex
     Nichol, Alex Paino, Nikolas Tezak, Jie Tang, Igor Babuschkin, Suchir Balaji, Shantanu Jain,
     William Saunders, Christopher Hesse, Andrew N. Carr, Jan Leike, Joshua Achiam, Vedant
     Misra, Evan Morikawa, Alec Radford, Matthew Knight, Miles Brundage, Mira Murati, Katie
     Mayer, Peter Welinder, Bob McGrew, Dario Amodei, Sam McCandlish, Ilya Sutskever, and
     Wojciech Zaremba. Evaluating large language models trained on code. CoRR, abs/2107.03374,
     2021.
[25] Lili Chen, Kevin Lu, Aravind Rajeswaran, Kimin Lee, Aditya Grover, Michael Laskin, Pieter
     Abbeel, Aravind Srinivas, and Igor Mordatch. Decision transformer: Reinforcement learning
     via sequence modeling. In Marc’Aurelio Ranzato, Alina Beygelzimer, Yann N. Dauphin, Percy
     Liang, and Jennifer Wortman Vaughan, editors, Advances in Neural Information Processing
     Systems, pages 15084–15097, 2021.
[26] Scott Reed, Konrad Zolna, Emilio Parisotto, Sergio Gomez Colmenarejo, Alexander Novikov,
     Gabriel Barth-Maron, Mai Gimenez, Yury Sulsky, Jackie Kay, Jost Tobias Springenberg, Tom
     Eccles, Jake Bruce, Ali Razavi, Ashley Edwards, Nicolas Heess, Yutian Chen, Raia Hadsell,
     Oriol Vinyals, Mahyar Bordbar, and Nando de Freitas. A generalist agent. arXiv preprint
     arXiv:2205.06175, 2022.
[27] Mahdi Pakdaman Naeini, Gregory Cooper, and Milos Hauskrecht. Obtaining well calibrated
     probabilities using Bayesian binning. In AAAI Conference on Artificial Intelligence, 2015.
[28] Richard Liaw, Eric Liang, Robert Nishihara, Philipp Moritz, Joseph E. Gonzalez, and Ion Stoica.
     Tune: A research platform for distributed model selection and training. CoRR, abs/1807.05118,
     2018.
[29] Sergey Levine, Aviral Kumar, George Tucker, and Justin Fu. Offline reinforcement learning:
     Tutorial, review, and perspectives on open problems. CoRR, abs/2005.01643, 2020.
[30] Colin Raffel, Noam Shazeer, Adam Roberts, Katherine Lee, Sharan Narang, Michael Matena,
     Yanqi Zhou, Wei Li, and Peter J. Liu. Exploring the limits of transfer learning with a unified
     text-to-text transformer. Journal of Machine Learning Research, 21(140):1–67, 2020.
[31] Michael Volpp, Lukas P. Fröhlich, Kirsten Fischer, Andreas Doerr, Stefan Falkner, Frank Hutter,
     and Christian Daniel. Meta-learning acquisition functions for transfer learning in Bayesian
     optimization. In International Conference on Learning Representations. OpenReview.net, 2020.
[32] Matthias Feurer, Jost Springenberg, and Frank Hutter. Initializing Bayesian hyperparameter
     optimization via meta-learning. In AAAI Conference on Artificial Intelligence, 2015.

                                                12

[33] Yutian Chen, Matthew W Hoffman, Sergio Gómez Colmenarejo, Misha Denil, Timothy P
     Lillicrap, Matt Botvinick, and Nando Freitas. Learning to learn without gradient descent by
     gradient descent. In International Conference on Machine Learning, pages 748–756. PMLR,
     2017.
[34] Yan Duan, John Schulman, Xi Chen, Peter L. Bartlett, Ilya Sutskever, and Pieter Abbeel. Rl$ˆ2$:
     Fast reinforcement learning via slow reinforcement learning. CoRR, abs/1611.02779, 2016.
[35] Jane X. Wang, Zeb Kurth-Nelson, Hubert Soyer, Joel Z. Leibo, Dhruva Tirumala, Rémi Munos,
     Charles Blundell, Dharshan Kumaran, and Matt M. Botvinick. Learning to reinforcement learn.
     In CogSci, 2017.
[36] Mojtaba Valipour, Bowen You, Maysum Panju, and Ali Ghodsi. SymbolicGPT: A generative
     transformer model for symbolic regression. CoRR, abs/2106.14131, 2021.
[37] Guillaume Lample and François Charton. Deep learning for symbolic mathematics. In Interna-
     tional Conference on Learning Representations. OpenReview.net, 2020.
[38] Vishesh Agarwal, Somak Aditya, and Navin Goyal. Analyzing the nuances of transform-
     ers’ polynomial simplification abilities. 1st Mathematical Reasoning in General Artificial
     Intelligence Workshop at ICLR, 2021.
[39] François Charton. Linear algebra with transformers. CoRR, abs/2112.01898, 2021.
[40] Samuel Müller, Noah Hollmann, Sebastian Pineda-Arango, Josif Grabocka, and Frank Hutter.
     Transformers can do Bayesian inference. 2022.
[41] Qinqing Zheng, Amy Zhang, and Aditya Grover. Online decision transformer. arXiv preprint
     arXiv:2202.05607, 2022.
[42] Nikhil Singh, Brandon Kates, Jeff Mentch, Anant Kharkar, Madeleine Udell, and Iddo Drori.
     Privileged zero-shot automl. CoRR, abs/2106.13743, 2021.
[43] Pierre Bourhis, Juan L Reutter, Fernando Suárez, and Domagoj Vrgoč. Json: data model, query
     languages and schema specification. In Proceedings of the 36th ACM SIGMOD-SIGACT-SIGAI
     symposium on principles of database systems, pages 123–135, 2017.
[44] Taku Kudo and John Richardson. Sentencepiece: A simple and language independent subword
     tokenizer and detokenizer for neural text processing. In Proceedings of the 2018 Conference
     on Empirical Methods in Natural Language Processing: System Demonstrations, pages 66–71,
     2018.
[45] Roman Garnett. Bayesian Optimization. Cambridge University Press, 2022.
[46] Bobak Shahriari, Kevin Swersky, Ziyu Wang, Ryan P Adams, and Nando De Freitas. Taking
     the human out of the loop: A review of Bayesian optimization. Proceedings of the IEEE, 104
     (1):148–175, 2015.
[47] Scott Fujimoto, David Meger, and Doina Precup. Off-policy deep reinforcement learning
     without exploration. In International Conference on Machine Learning, pages 2052–2062.
     PMLR, 2019.
[48] Ouassim Ait ElHara, Konstantinos Varelas, Duc Manh Nguyen, Tea Tusar, Dimo Brockhoff,
     Nikolaus Hansen, and Anne Auger. COCO: The large scale black-box optimization benchmark-
     ing (bbob-largescale) test suite. ArXiv, abs/1903.06396, 2019.
[49] Esteban Real, Alok Aggarwal, Yanping Huang, and Quoc V Le. Regularized evolution for image
     classifier architecture search. In Proceedings of the aaai conference on artificial intelligence,
     volume 33, pages 4780–4789, 2019.
[50] Xin-She Yang and Suash Deb. Eagle strategy using Lévy walk and firefly algorithms for
     stochastic optimization. In Nature inspired cooperative strategies for optimization (NICSO
     2010), pages 101–111. Springer, 2010.
[51] Ryan Turner, David Eriksson, Michael McCourt, Juha Kiili, Eero Laaksonen, Zhen Xu, and
     Isabelle Guyon. Bayesian optimization is superior to random search for machine learning
     hyperparameter tuning: Analysis of the black-box optimization challenge 2020. In NeurIPS
     2020 Competition and Demonstration Track, pages 3–26, 2021.
[52] Alexander I Cowen-Rivers, Wenlong Lyu, Rasul Tutunov, Zhi Wang, Antoine Grosnit,
     Ryan Rhys Griffiths, Alexandre Max Maraval, Hao Jianye, Jun Wang, Jan Peters, et al. Hebo:

                                                 13

     Pushing the limits of sample-efficient hyper-parameter optimisation. Journal of Artificial
     Intelligence Research, 74:1269–1349, 2022.
[53] Luke Metz, Niru Maheswaranathan, Ruoxi Sun, C Daniel Freeman, Ben Poole, and Jascha
     Sohl-Dickstein. Taskset: A dataset of optimization tasks. 2020.
[54] Mikhail Evchenko, Joaquin Vanschoren, Holger H Hoos, Marc Schoenauer, and Michèle Sebag.
     Frugal machine learning. arXiv preprint arXiv:2111.03731, 2021.
[55] Murray Rosenblatt. Remarks on a multivariate transformation. The Annals of Mathematical
     Statistics, 23(3):470 – 472, 1952.
[56] Christopher M Bishop and Nasser M Nasrabadi. Pattern recognition and machine learning,
     volume 4. Springer, 2006.
[57] Zi Wang, Beomjoon Kim, and Leslie Pack Kaelbling. Regret bounds for meta Bayesian
     optimization with an unknown gaussian process prior. In Advances in Neural Information
     Processing Systems, 2018.
[58] Zi Wang, George E Dahl, Kevin Swersky, Chansoo Lee, Zelda Mariet, Zachary Nado, Justin
     Gilmer, Jasper Snoek, and Zoubin Ghahramani. Pre-trained Gaussian processes for Bayesian
     optimization. arXiv preprint arXiv:2109.08215, 2022.
[59] James Bergstra, Dan Yamins, David D Cox, et al. Hyperopt: A python library for optimizing
     the hyperparameters of machine learning algorithms. In Proceedings of the 12th Python in
     science conference, volume 13, page 20, 2013.
[60] Daiyi Peng, Xuanyi Dong, Esteban Real, Mingxing Tan, Yifeng Lu, Gabriel Bender, Hanxiao
     Liu, Adam Kraft, Chen Liang, and Quoc Le. PyGlove: Symbolic programming for automated
     machine learning. Advances in Neural Information Processing Systems, 33:96–108, 2020.
[61] Aviral Kumar, Aurick Zhou, George Tucker, and Sergey Levine. Conservative Q-learning
     for offline reinforcement learning. In Hugo Larochelle, Marc’Aurelio Ranzato, Raia Hadsell,
     Maria-Florina Balcan, and Hsuan-Tien Lin, editors, Advances in Neural Information Processing
     Systems 33: Annual Conference on Neural Information Processing Systems 2020, NeurIPS
     2020, December 6-12, 2020, virtual, 2020.
[62] Caglar Gulcehre, Sergio Gómez Colmenarejo, Ziyu Wang, Jakub Sygnowski, Thomas Paine,
     Konrad Zolna, Yutian Chen, Matthew Hoffman, Razvan Pascanu, and Nando de Freitas. Regu-
     larized behavior value estimation. arXiv preprint arXiv:2103.09575, 2021.
[63] Jürgen Schmidhuber. Reinforcement learning upside down: Don’t predict rewards - just map
     them to actions. CoRR, abs/1912.02875, 2019.
[64] Julian Schrittwieser, Thomas Hubert, Amol Mandhane, Mohammadamin Barekatain, Ioannis
     Antonoglou, and David Silver. Online and offline reinforcement learning by planning with a
     learned model. Advances in Neural Information Processing Systems, 34, 2021.
[65] Krzysztof Choromanski, Valerii Likhosherstov, David Dohan, Xingyou Song, Andreea Gane,
     Tamas Sarlos, Peter Hawkins, Jared Davis, Afroz Mohiuddin, Lukasz Kaiser, et al. Rethinking
     attention with performers. arXiv preprint arXiv:2009.14794, 2020.
[66] Daniel Golovin, John Karro, Greg Kochanski, Chansoo Lee, Xingyou Song, and Qiuyi (Richard)
     Zhang. Gradientless descent: High-dimensional zeroth-order optimization. In International
     Conference on Learning Representations. OpenReview.net, 2020.
[67] James Kennedy and Russell Eberhart. Particle swarm optimization. In ICNN’95 international
     conference on neural networks, volume 4, pages 1942–1948, 1995.
[68] Alexander I Cowen-Rivers, Wenlong Lyu, Rasul Tutunov, Zhi Wang, Antoine Grosnit,
     Ryan Rhys Griffiths, Alexandre Max Maraval, Hao Jianye, Jun Wang, Jan Peters, et al. An
     empirical study of assumptions in Bayesian optimisation. arXiv preprint arXiv:2012.03826,
     2020.
[69] Elizabeth D Dolan and Jorge J Moré. Benchmarking optimization software with performance
     profiles. Mathematical Programming, 91(2):201–213, 2002.
[70] Xuanyi Dong and Yi Yang. Nas-bench-201: Extending the scope of reproducible neural
     architecture search. In 8th International Conference on Learning Representations, ICLR
     2020, Addis Ababa, Ethiopia, April 26-30, 2020. OpenReview.net, 2020. URL https://
     openreview.net/forum?id=HJxyZkBKDr.

                                               14

Checklist
    1. For all authors...
        (a) Do the main claims made in the abstract and introduction accurately reflect the pa-
            per’s contributions and scope? [Yes] Abstract and introduction point out the specific
            contributions in bullet points, which we have covered throughout or methodology and
            experiments.
        (b) Did you describe the limitations of your work? [Yes] See Section 7.
        (c) Did you discuss any potential negative societal impacts of your work? [Yes] The main
            potential negative societal impacts of our work come from the inherent associated risks
            of using large Transformer models, such as bias. However, currently this does not apply
            to our work, as our data only contains numerical optimization data, and we are not
            using language models as of yet. Such a risk may grow however, if in future works, we
            e.g. use pretrained language models for warm-starting in our O PT F ORMER framework.
        (d) Have you read the ethics review guidelines and ensured that your paper conforms to
            them? [Yes] The most relevant potential ethics concern is our use of RealWorldData.
            However, as we mention in Appendix C, we anonymized the data to prevent users from
            being identified in the model.
    2. If you are including theoretical results...
        (a) Did you state the full set of assumptions of all theoretical results? [N/A] Not applicable;
            no theoretical results.
        (b) Did you include complete proofs of all theoretical results? [N/A] Not applicable; no
            theoretical results.
    3. If you ran experiments...
        (a) Did you include the code, data, and instructions needed to reproduce the main experi-
            mental results (either in the supplemental material or as a URL)? [Yes] We included
            the link to the primary T5x codebase in Appendix D.2, along with relevant hyperpa-
            rameters. But we do not include the training data. The datasets generated on public
            benchmarks, BBOB and HPO-B, can be reproduced by running publicly available HPO
            algorithms.
        (b) Did you specify all the training details (e.g., data splits, hyperparameters, how they were
            chosen)? [Yes] These are specified in Appendix A, Appendix C, and Appendix D.2.
        (c) Did you report error bars (e.g., with respect to the random seed after running experi-
            ments multiple times)? [Yes] In experiments, we plotted error bars using 1 standard
            deviation over 5 runs.
        (d) Did you include the total amount of compute and the type of resources used (e.g., type
            of GPUs, internal cluster, or cloud provider)? [Yes] See Appendix D.2.
    4. If you are using existing assets (e.g., code, data, models) or curating/releasing new assets...
        (a) If your work uses existing assets, did you cite the creators? [Yes] We have cited [48] for
            our use of BBOB dataset, [5] for the HPO-B dataset, and [30] for training T5 models.
        (b) Did you mention the license of the assets? [Yes] For data, we mentioned the use of
            Apache 2.0 License when using the HPO-B dataset.
        (c) Did you include any new assets either in the supplemental material or as a URL? [Yes]
            We cited and linked the T5x codebase (see Appendix D.2) throughout this paper. We
        (d) Did you discuss whether and how consent was obtained from people whose data you’re
            using/curating? [Yes] The BBOB dataset is synthetic, and the HPO-B dataset is public.
            For the RealWorldData dataset, users by default are agreeing to the condition that their
            tuning data may be used for research purposes. We have also ensured anonymity over
            the data by removing personally identifiable information.
        (e) Did you discuss whether the data you are using/curating contains personally identifiable
            information or offensive content? [Yes] As discussed in Appendix C, we anonymized
            user details from studies in the RealWorldData dataset.
    5. If you used crowdsourcing or conducted research with human subjects...
        (a) Did you include the full text of instructions given to participants and screenshots, if
            applicable? [N/A] Not applicable.

                                                15

(b) Did you describe any potential participant risks, with links to Institutional Review
    Board (IRB) approvals, if applicable? [N/A] Not applicable.
(c) Did you include the estimated hourly wage paid to participants and the total amount
    spent on participant compensation? [N/A] Not applicable.

                                     16

A PPENDIX
A     Preprocessing and tokenization details
A.1   Search space primitives

Below are the exact descriptions of the hyperparameter primitives used to define a given X (d) .

       • Double: Specifies a continuous range of possible values in the closed interval [xmin , xmax ]
         for some real values xmin ≤ xmax .
       • Integer: Specifies an integer range of possible values in [xmin , xmax ] ∈ Z for some
         integers xmin ≤ xmax .
       • Discrete: Specifies a finite, ordered set of values from R.
       • Categorical: Specifies an unordered list of strings.

A.2   Data preprocessing and tokenization

We list out the full set of preprocessing steps (from Section 4.1) below:

       • Omit parameter and metric names in all trials, remove redundant keywords ("parameter",
         "trial", etc.), order trial parameters according to those in metadata m, and add key-
         words (e.g., "name", "algorithm") and enumerating types (e.g. "DOUBLE") in the tokenizer
         vocabulary so that the original keywords are encoded into single tokens.
           – List of keywords: name, metric, goal, type, algorithm, min_value, max_value,
              scale_type, categories.
           – Enumerating values for the parameter type: DOUBLE, INTEGER, DISCRETE, CAT-
              EGORICAL.
           – Enumerating values for the scale_type: LINEAR, LOG.
       • Insert short separator symbols, e.g. ? between parameter/metrics in a trial, "|" between trials,
         and "&" between experiment description and parameter configurations in metadata.
       • Convert all values in history h to single integers.
           – Represent discrete and categorical parameters with their index in the set of values.
                                                                    (d)
            – Normalize float and integer parameter values in xt with their value range and the
              function values yt with their minimum and maximum seen values in the entire study.
              Then quantize the normalized values to an integer, e.g., “0.12345" → "123" with a
              quantization level of Q = 1000. More formally, we apply the following transformation
              q(·):
                        q(z) = int[znorm ∗ Q], where znorm = (z − zmin )/(zmax − zmin )             (11)

The shortened text string is then converted to a sequence of tokens via the SentencePiece tokenizer
[44] with a vocabulary of 33000 words. Quantized numbers in h are always converted into single
tokens. As long as Q is sufficiently large, there is no concern from the loss of precision over numerical
quantizations, and thus the serialized study contains nearly the same amount of information as the
original data. For comparison, the naive tokenization for the example of Table 1 with t = 100 trials
will produce 8221 tokens which can overload GPU memory, while our proposed tokenization will
only produce 584 tokens, a 14x reduction.

                                                   17

B     Algorithm and baseline details
B.1   Dataset algorithms

Grid Search: DOUBLE parameters are first log-transformed if specified. They are then converted
into DISCRETE parameters by discretizing their ranges into 100 equidistant points. Suggestions
are outputted using lexicographic ordering from the cartesian product over all parameters’ feasible
points. The traversal order follows the alphabetical ordering of parameter names. That is, given
two parameters "Foo" and "Bar", both in [0,1] range, the sequence of trials looks like: {"Foo":
0, "Bar":0} , {"Foo": 0, "Bar":0.01}, ..., {"Foo": 0, "Bar":1},
{"Foo": 0.01, "Bar":0}, {"Foo": 0.01, "Bar":0.01}, ....

Shuffled Grid Search: Shuffled grid search is the same as Grid Search in how it handles DOUBLE
parameters. Instead of traversing the grid in a deterministic order, it selects without replacement a
random point from the grid at each iteration.

Regularized Evolution [49]: In summary, this algorithm at every iteration randomly selects a
tournament subset from the current population, and mutates the argmax member of the tournament.
When inserting a new trial, the oldest trial will be removed. We use a population size of 25 and
tournament size of 5. The mutation operation uniformly at random selects one of the parameters
x(r) from x, and mutateshx(r) based on
                                     i the following: for DOUBLE, INTEGER, the new value is
                           (r)   (r)
uniformly sampled from xmin , xmax , while for DISCRETE, CATEGORICAL, the new value is
uniformly sampled from the feasible list.

Hill Climbing: This is a naive implementation, where at every iteration t, the current xpivot
is mutated (using the same operation as Regularized Evolution) to xmutated , and evaluated. If
f (xmutated ) > f (xpivot ), then we reassign xpivot to be the mutated xmutated . An extension of this
method can be "batched", as seen in [66], although we not include this for the sake of clarity and
presentation.

Eagle Strategy [50]: Eagle strategy is a metaheuristics algorithm that is a slight variation of Particle
Swarm Optimization [67].
The algorithm is originally formulated for continuous search spaces only. The reason is that it involves
a subroutine (move step) where we take a convex combination of a particle (called firefly in [50]) and
another particle that has a better objective value. Mathematically, given two particle vectors x and x0
and the coefficient c ∈ [0, 1], the move step generates cx + (1 − c)x0 .
The algorithm is extended to support DISCRETE and CATEGORICAL parameters by applying a
separate move operation for each non-continuous dimension d:
                                       (d)
                                      x             with probability (1 − α)c
           move(x(d) , x0(d) , c, α) = x0(d)         with probability (1 − α)(1 − c)
                                      
                                        random value with probability α
where α is a small perturbation coefficient that decreases in the dimension of the search space.

Vizier [2]: Vizier’s default algorithm is available via Google Cloud as Vertex Vizier. We have
contacted the authors of the algorithm and received the the following details on its implementation.
In summary, the algorithm uses a particular implementation of GP-UCB with trust regions. The GP
regressor model consists of the following:
       • α ∼ TruncatedLogNormal controls the amplitude of Matern5/2 kernel.
       • λi ∼ TruncatedLogNormal (i.i.d. for each dimension i) controls the length scale for the
         i-th dimension.
       • σ ∼ TruncatedLogNormal controls the Gaussian noise.
       • z ∼ Normal(0, σ) is the observation noise.
       • f ∼ GP(λ, α) is the function.

                                                  18

       • y(x) ∼ f (x) + z is the noisy function.

where the prior distribution parameters are chosen depending on the user’s estimate of the observation
noise.
The algorithm then uses gradient descent with line search for step sizes to obtain the MAP estimate
of α, λ and σ. Furthermore, the algorithm uses a variation of Eagle Strategy (explained above) to
optimize the UCB acquisition function with coefficient of 1.8. In order to prevent overexploration
that may result from the large UCB coefficient, the algorithm optimizes acquisition functions inside
trust region. The trust region is the union of L∞ -norm balls around explored points. The radius of
the L∞ -norm ball grows in the number of explored points. The algorithm also starts at the center of
the search space (unless user specifies an alternative initial batch).

GP-UCB: It is the same as Vizier’s GP-UCB, except for the model definition. We used the model
definition from the github repository of the authors of "Heteroscedastic and Evolutionary Bayesian
Optimisation solver" (HEBO) [68], the winner of 2020 Blackbox Optimization challenge [51]. It is
worth noting that HEBO uses multi-dimensional acquisition functions derived from the GP model.
The priors over hyperparameters are thus not tuned to optimize the performance of GP-UCB algorithm,
which explains its suboptimal performance.

B.2   Gaussian Process for uncertainty estimation

We use the same GP model as GP-UCB.
When comparing the function prediction performance with the O PT F ORMER, we choose [ymin , ymax ]
to normalize function value token based on the range of observed value in the sampled sequence
(x1 , y1 , . . . xt , yt ), and therefore the real value of yt always resides in the prediction support of the
O PT F ORMER.
To compensate for the fact that GP’s distribution is wider than the real support used by the Transformer,
we truncate the GP’s prediction into [ymin , ymax ] for a fairer comparison.

B.3   Transfer learning baselines

We use the following methods as transfer-learning baselines for the HPO-B dataset from Section 6.3:

ABLR [12, 56]: BO with multi-task adaptive Bayesian linear regression. Our implementation of
ABLR is equivalent to a GP with 0 mean and a dot-product kernel with learned basis functions.
We use a neural net (NN) with (128, 128) hidden layers and tanh activation as the basis functions.
We then train ABLR by optimizing the negative log likelihood (NLL) over NN weights θ as well
covariance matrix SS > and bias parameters δ 2 that define the dot-product kernel k, i.e.
                                  k(x, x0 ) = φϑ (x)> SS > φϑ (x0 ) + δ 2 ,                              (12)
where matrix S ∈ R128×256 , basis function φθ is parameterized by NN weights ϑ and δ ∈ R.

FSBO [7]: Bare-bone few-shot BO. We did not include data-driven initialization due to lack of
reproducing details. Following [7], our implementation of FSBO is equivalent to BO using a GP
with 0 mean and a squared-exponential kernel on top of a NN with (128, 128) hidden layers and tanh
activation functions. We train the NN weights as well as the parameters in the squared-exponential
kernel.

HyperBO [57, 58]: BO with pre-trained GPs. Following [58], we pre-train a GP with Matérn32
kernel on top of a NN with one hidden layer of width 2 × D and tanh activation functions. Here D is
the input dimension of the search space.
For training, we use the Adam optimizer with learning rate 0.001 and batch size 50 for all the
transfer-learning baselines. Notice that these transfer-learning methods require “pre-training” a GP
on the same search space. We sample 10000 random data points on each HPO-B surrogate functions
from each search space. We train a separate GP for each search space.

                                                     19

C     Data details
C.1   Dataset details

RealWorldData dataset: The RealWorldData dataset contains a total of 750K studies collected
from Google Vizier users over a span of 5 years (starting from 2017), and each study has a variable
number of trials. Since some user studies can potentially have an excessive number of trials (e.g.
10K+), for all studies we only consider the first 300 trials for experiments. Since the dataset also
includes Google employee usernames, we made sure to anonymize every study first.
We split the dataset in temporal order to avoid information leak, use most studies for training, and
select 16 studies generated by a different set of users for testing. All training studies were generated
before Feb 2020. The test studies were created by users who started to use the hyperparameter tuning
service after that date. To bootstrap these studies into actual functions to be evaluated, we fit a GP
for each study and output the function value as the GP’s median function (due to the use of output
warping).

HPO-B dataset: For HPO-B dataset, a tuning task is identified with a (search space id, dataset id)
pair, which refers to tuning the hyperparameters defined in a search space for some machine learning
model trained on a dataset. we use the "v3-augmented" meta-training/validation/test splits that
includes all the 16 test search spaces as well as less frequent search spaces in the meta-training split.
There are uniquely 1730, 91, and 86 tasks for training, validation and testing respectively. For every
tuning task, [5] fits an XGBoost model to the trial data of every tuning task as the objective function.
Similar to the BBOB dataset, we generate 10M, 500K studies for training and validation respectively, along with the same set of controlled algorithms. For each of the test tuning task, we run 5
optimizations each with a different initial set of observations provided in [5].
The HPO-B uses the Apache 2.0 open-source license.

BBOB dataset: The BBOB dataset contains a total of 10M studies for training, each containing
exactly 300 trials. An additional 500K studies (using different randomization seeds) are used for
validation. While the number of studies can be freely generated and effectively unlimited, we found
that 10M studies were sufficient for the Transformer to train properly.
The functions we use for data are from [48], and consist of separable functions (Sphere,
Ellipsoid Separable, Rastrigin Separable, Bueche Rastrigin,
{Linear Slope}), moderately conditioned, potentially multi-modal functions (Attractive
Sector, Step Elllipsoid, {Rosenbrock Rotated}), ill-conditioned functions
(Discus, Bent Cigar, Sharp Ridge, {Sum of Powers}), multi-modal functions
(Weierstrass, Schaffers F7, Schaffers F7 Illconditioned, {Greiwank
Rosenbrock}), and functions with weak global structures (Schwefel, Gallagher 21,
Gallagher 101, Katsuura, {Lunacek}). The functions noted with the extra "{}" are for
testing and excluded from the training data. We apply significant randomization over the functions
for both the training dataset and test-time evaluation. In order, we randomize the following:

       • Function dimension D, which is uniformly selected from a range. For training data genera-
         tion, this range is [1, 20].
       • Orthonormal rotation matrix Γ, which is applied to the input first, i.e. producing a new
         function f 0 (x) = f (Γx).
       • Shift vector xshif t which is also applied to the input first, i.e. producing a new function
         f 0 (x) = f (x − xshif t ), where xshif t has all of its coordinate-wise entries sampled from
         [−4, 4], while the domain is [−5, 5].
       • Discretization, in which the parameter space X (d) is uniformly at random chosen to be either
         a DOUBLE, DISCRETE, CATEGORICAL parameter. The DOUBLE parameter "discretiza-
         tion" is actually a no-op, as it allows the original continuous space X (d) ⊂ R. Otherwise, a
         number L of feasible points is uniformly selected from the range [2, 8], and used to divide
         the original [−5, 5] range into L equally-spaced points. If DISCRETE was chosen, then the
         ordering of the grid points is preserved, otherwise if CATEGORICAL was chosen, then all
         of the gridpoints become effectively unordered strings.

                                                  20

      • Noise Type, in which one of 10 noise settings (including no noise) is uniformly chosen.
        Noise consists of either Gaussian (multiplier sampled from a random Gaussian of varying
        scale is applied), Uniform (multiplier sampled from uniform distribution of varying scale is
        applied), or Cauchy (additive noise which only occurs at a probabilistic frequency, with a
        varying fixed strength is applied).

For evaluation, we randomly sample 100 configurations for each of the five test functions, resulting
in 500 optimization trajectories in total.
For BBOB, as all parameters are named as "x_i" with i ∈ [0, D) and always have value range in
[−5, 5], significantly different from the other two datasets, we omit their parameter names and value
in the metadata m and only keep parameter type information.

                                                 21

            Table 5: Example of studies in RealWorldData (left), BBOB (middle) and HPO-B (right).
"name": "gan1d 500 iters -          "name": "SCHAFFERS_F7",               "name": "5859_145853",
"2022-05-18"                        "algorithm": "gp",                    "algorithm": "GP UCB",
"parameter": {                      "parameter": {                        "parameter": {
  "name": "learning_rate",            "name": "x0",                          "name": "minsplit",
  "min_value": 1e-06,                 "type": "CATEGORICAL",                 "max_value": 60.0,
  "max_value": 0.01,                  "categories": ["0.0", "5.0",           "type": "DOUBLE",
  "type": "DOUBLE",                                   "-5.0"],               "scale_type": "LINEAR",
  "scale_type": "LOG",              },                                    }
}                                   "parameter": {                        "parameter": {,
"parameter": {                        "name": "x1",                          "name": "minsplit.na",
  "name": "modifier",                 "min_value": -5.0,                     "max_value": 1.0,
  "min_value": 0.1,                   "max_value": 5.0,                      "type": "DOUBLE",
  "max_value": 1000000.0,             "type": DOUBLE,                     }
  "type": "DOUBLE",                   "scale_type": UNIT_LINEAR_SCALE,    "parameter": {
  "scale_type": "LOG",                                                       "name": "minbucket",
}                                   },                                       "min_value": 1.0,
"parameter": {                      "parameter": {                           "max_value": 60.0,
  "name": "weight_init_std",          "name": "x2",                          "type": "DOUBLE",
  "min_value": 0.01,                  "min_value": -5.0,                     "scale_type": "LINEAR",
  "max_value": 2.0,                   "max_value": 5.0,                   }
  "type": "DOUBLE",                   "type": DOUBLE,                     "parameter": {
}                                     "scale_type": UNIT_LINEAR_SCALE,       "name": "cp",
"parameter": {                                                               "min_value": 0.000100788830221,
  "name": "optimizer",              },                                       "max_value": 1.000092678873241,
  "type": "CATEGORICAL",            "parameter": {                           "type": "DOUBLE",
  "categories": "sgd",                "name": "x3",                          "scale_type": "LOG",
  "categories": "adam",               "type": DISCRETE,                   }
  "categories": "rmsprop",            "values": [-5.0, 5.0],              "parameter": {
}                                   },                                       "name": "maxdepth",
"goal": "MINIMIZE",                 "parameter": {                           "max_value": 29.0,
"max_num_trials": 500,                "name": "x4",                          "type": "DOUBLE",
"metric": "",                         "type": CATEGORICAL,                   "scale_type": "LINEAR",
"observation_noise": "HIGH",          "categories": ["5.0",               }
"trial": {                                "-1.66666666667",               "parameter": {
 "parameter": {                                       "-5.0",                "name": "maxdepth.na",
    "learning_rate": 0.0001,              "1.666666666667"],                 "max_value": 1.0,
    "modifier":                     },                                       "type": "DOUBLE",
          316.2277660168381,        "parameter": {                        }
    "optimizer": "sgd",               "name": "x5",                       "observation_noise": AUTOMATIC,
    "weight_init_std": 1.005,         "min_value": -5.0,                  "metric": "objective_value",
 }                                    "max_value": 5.0,                   "goal": "MAXIMIZE"
 "metric": {                          "type": DOUBLE,                     "trial": {
    "": -0.946908021738347,           "scale_type": UNIT_LINEAR_SCALE,     "parameter": {
 }                                                                            "minsplit": 4.0,
}                                   }                                         "minsplit.na": 0.0,
"trial": {                          "metric": "",                             "minbucket": 18.0,
 "parameter": {                     "goal": MAXIMIZE,                         "cp": 0.7342895964927976,
    "learning_rate": 0.000504,      "observation_noise": HIGH                 "maxdepth": 3.0,
    "modifier":                     "trial": {                                "maxdepth.na": 0.0,
           12.346786652749216,       "parameter": {                        }
    "optimizer": "rmsprop",             "x0": "0.0",                       "metric": {
    "weight_init_std":                  "x1": 0.0,                            "objective_value": 0.500024080276,
           1.2192566347109868,          "x2": 0.0,                         }
 }                                      "x3": 5.0,                        }
 "metric": {                            "x4": "-5.0",                     "trial": {
    "": -1.5144472008077585,            "x5": 0.0,                         "parameter": {
 }                                   }                                        "minsplit": 8.0,
}                                    "metric": {                              "minsplit.na": 0.0,
...                                     "": -334.4782223514127,               "minbucket": 32.0,
                                     }                                        "cp": 0.30972302652187583,
                                    }                                         "maxdepth": 4.0,
                                    "trial": {                                "maxdepth.na": 0.0,
                                     "parameter": {                        }
                                        "x0": "5.0",                       "metric": {
                                        "x1": -1.9867479768748013,            "objective_value": 0.50002408028,
                                        "x2": -1.7665621302793095,         }
                                        "x3": -5.0,                       }
                                        "x4": "1.666666666666667",        ...
                                        "x5": -1.7634306558106605,
                                     }
                                     "metric": {
                                        "": -323.84900527589326,
                                     }
                                    }
                                    ...

                                                     22

D     Model and training details
The open-sourced T5 model codebase we use can be found at https://github.com/
google-research/t5x.

D.1    Conditional probability decomposition

From Section 4.2, the joint distribution of the optimization history h conditioned on metadata m can
be written using the chain rule as

                                                                                                  
                  (1)  (2)           (D)                         (1)   (2)           (D)
 P (h̄|m̄) = P x̄1 , x̄1 , . . . , x̄1 , ?, ȳ1 , "|", . . . , x̄T , x̄T , . . . , x̄T , ?, ȳT |m̄
      T    D
                                                !
                                             
                   (d)                (1:d−1)
     Y     Y                                                                                               
 =            P x̄t |m̄, h̄t−1 , x̄t                P ?|m̄, h̄t−1 , x̄t P ȳt |m̄, h̄t−1 , x̄t P "|"|m̄, h̄t
      t=1    d=1
                                                                                                          (13)

We note that this correctly
                           formalizes the prediction
                                                     of objects we are most interested in, which are
                       (d)            (1:d−1)                                             
parameter values P x̄t |m̄, h̄t−1 , x̄t         and function values P ȳt |m̄, h̄t−1 , x̄t .

D.2    Training

During training, the encoder (denoted as Eθ ) input sequence length is selected to be the maximum
length of the tokenized metadata m̄ from a dataset, ranging from 256 to 1024. The decoder (denoted
as Dθ ) input sequence is fixed at 1024, which means it can model up to 1024//(D + 3) trials where
D is the number of parameters per trial. We use Adam optimizer with a rsqrt learning rate schedule
and a mini-batch size of 256, and train each model up to 1M steps, with early stopping according to
the validation loss. Each model is trained with a 4x4 TPU-v3 slice.
Thus the prediction for h̄(n) is:
                                                h                         i
                   Pθ h̄(n) m, h̄(1:n−1) = SoftMax Dθ (Eθ (m̄), h̄(1:n−1) )                               (14)

D.3    Data augmentation

We adopt the following three data augmentations to reduce overfitting to the offline datasets:

      1. In order for the model to be invariant to parameter ordering, we apply random parameter
         permutations over metadata m̄ and every suggestion x̄t .
      2. In order for the model to be robust to a different normalization range given a new function,
         we apply random scaling and shifting to the normalized function value ynorm = (y −
         ymin )/(ymax − ymin ) before quantization:
                       0
                      ynorm = ynorm ∗ s + c, s ∼ Uniform[0.3, 1], c ∼ Uniform[0, 1 − s]                   (15)
                      0
            and thus ynorm ∈ [c, c + s] ⊆ [0, 1] after transformation.
      3. Randomly drop textual and parameter value range information in metadata.

D.4    Inference

At inference time, we choose the decoder input sequence length according to the maximum number of
trials to run. E.g. to optimize a function with 18 parameters (highest possible dimension D over our
test functions) over 105 trials, we set the input sequence length to be at least (18 + 3) ∗ 105 = 2205.
We compute the (ymin , ymax ) range for function value normalization in the tokenization process with
the current minimal and maximum observations. We set c = 0.2, s = 0.6 so that all normalized
                                       0
observations fall in the range of ynorm  ∈ [0.2, 0.8], and the model’s y value predicted distribution
support, [0, 1], is sufficiently large.

                                                      23

We also use a softmax temperature hyperparameter when predicting function values. We choose the
temperature to maximize the log-likelihood of the validation split of each dataset seperately. On
RealWorldData, the function prediction temperature is set as 1.1 and on HPO-B it is 1.5. The policy
prediction temperature is always set to be 1.

                                                24

    E                                             Additional experimental results
   We provide additional experimental results in this section.

    E.1                                            Imitating HPO policies
                                                  Grid Search         Shuffled Grid Search                                  Random Search         Regularized Evolution                                    Hill-Climbing       0.3 Eagle Strategy                                       Vizier GP-UCB
                                                                                                                                                  0.6                                              1.0                                                                          1.5           OptFormer
                                                                                                                                                                                                                                                                                              Target policy
                     1.5                                           0.10                                              0.10
                                                                                                                                                  0.4                                                                          0.2                                              1.0
Density              1.0
                                                                                                                     0.05
                                                                                                                                                                                                   0.5
                     0.5                                           0.05                                                                           0.2                                                                          0.1                                              0.5
                     0.0 5                              0        5 0.00 5       0                          5 0.00 5                  0     5      0.0 5        0                               5   0.0 5         0         5   0.0 5                               0        5   0.0 5         0               5
                                                       x1                      x1                                                   x1                        x1                                                x1                                                x1                         x1
                                                                                                                                                                                                   1.0                                                                          1.5
                     1.5                                           0.10                                              0.10                         0.3                                                                          0.3
                                                                                                                                                                                                                                                                                1.0
Density              1.0                                                                                                                          0.2                                              0.5                         0.2
                                                                   0.05                                              0.05                                                                                                                                                       0.5
                     0.5                                                                                                                          0.1                                                                          0.1
                     0.0 5                              0        5 0.00 5       0                          5 0.00 5                  0     5      0.0 5        0                               5   0.0 5         0         5   0.0 5                               0        5   0.0 5         0               5
                                                       x2                      x2                                                   x2                        x2                                                x2                                                x2                         x2

                                                                                                                             (d)                        (1:d−1)
    Figure 6: Policy distribution p(x40 |m, h39 , x40                                                                                                                                    ) for d = 1, 2 on a 2D GRIEWANK ROSEN-
    BROCK function.
                                  100                        Grid Search                                         1.0        Shuffled Grid Search                                      1.0           Random Search                                               1.0     Eagle Strategy

Best normalized function                                                              Best normalized function                                             Best normalized function                                                  Best normalized function
                                                                    Target policy
                                             50                     OptFormer                                    0.8                                                                  0.8                                                                       0.8
                                              0                                                                  0.6                                                                  0.6                                                                       0.6
                                             50                                                                  0.4                      Target policy                               0.4                        Target policy                                  0.4                   Target policy
                                                                                                                                          OptFormer                                                              OptFormer                                                            OptFormer
                                  1000                 20      40 60        80 100                               0.20        20     40 60 80 100                                      0.20         20      40 60 80 100                                         0.20   20       40 60 80 100
                                                                 Trial                                                                Trial                                                                  Trial                                                                Trial
                                         1.0          Regularized Evolution                                      1.0              Hill-Climbing                                       1.0                  Vizier

              Best normalized function                                                Best normalized function                                             Best normalized function
                                         0.8                                                                     0.8                                                                  0.8
                                         0.6                                                                     0.6                                                                  0.6
                                         0.4                         Target policy                               0.4                      Target policy                               0.4            Target policy
                                                                     OptFormer                                                            OptFormer                                                  OptFormer
                                         0.20          20      40 60 80 100                                      0.20        20     40 60 80 100                                      0.20         20 40 60            80 100
                                                                 Trial                                                                Trial                                                                Trial

    Figure 7: Best normalized function value with std, averaged over 5 test functions each with 100 runs.
                                                             Grid Search                                            1.2 Shuffled Grid Search                                            1.2         Random Search                                               1.2     Eagle Strategy
                                  0.75

Best normalized function                                                                 Best normalized function                                            Best normalized function                                                Best normalized function
                                                                                                                    1.0                                                                 1.0                                                                     1.0
                                  0.50
                                                                                                                    0.8                                                                 0.8                                                                     0.8
                                  0.25
                                                                                                                    0.6                                                                 0.6                                                                     0.6
                                  0.00                                                                              0.4                                                                 0.4                                                                     0.4
                                  0.25                                Target policy                                                  Target policy                                                               Target policy                                                        Target policy
                                                                      OptFormer                                     0.2              OptFormer                                          0.2                      OptFormer                                      0.2                   OptFormer
                                  0.50                                                                              0.00 20 40 60 80 100                                                0.00                                                                    0.00
                                                  0     20      40 60 80 100                                                                                                                       20      40 60 80 100                                                20       40 60 80 100
                                                                  Trial                                                          Trial                                                                       Trial                                                                Trial
                                             1.2 Regularized Evolution                                              1.2           Hill-Climbing                                         1.2                 Vizier

                  Best normalized function                                               Best normalized function                                            Best normalized function
                                             1.0                                                                    1.0                                                                 1.0
                                             0.8                                                                    0.8                                                                 0.8
                                             0.6                                                                    0.6                                                                 0.6
                                             0.4                                                                    0.4                                                                 0.4
                                                              Target policy                                                                Target policy                                                         Target policy
                                             0.2              OptFormer                                             0.2                    OptFormer                                    0.2                      OptFormer
                                             0.00 20 40 60 80 100                                                   0.00     20      40 60 80 100                                       0.00       20      40 60 80 100
                                                          Trial                                                                        Trial                                                                 Trial

                           Figure 8: Best normalized function value of LINEAR SLOPE with std, averaged over 100 runs.

                                                                                                                                                          25

                                                      Grid Search                                                1.2 Shuffled Grid Search                                            1.2      Random Search                                           1.2      Eagle Strategy
                                     2.5

Best normalized function                                                              Best normalized function                                            Best normalized function                                         Best normalized function
                                                                                                                 1.0                                                                 1.0                                                              1.0
                                     0.0                                                                         0.8                                                                 0.8                                                              0.8
                                     2.5                                                                         0.6                                                                 0.6                                                              0.6
                                     5.0                                                                         0.4                                                                 0.4                                                              0.4
                                                    Target policy                                                                 Target policy                                                         Target policy                                                    Target policy
                                                    OptFormer                                                    0.2              OptFormer                                          0.2                OptFormer                                     0.2                OptFormer
                                     7.5
                                             0    20 40 60           80 100                                      0.00 20 40 60 80 100                                                0.00   20    40 60 80 100                                        0.00   20    40 60 80 100
                                                          Trial                                                               Trial                                                                 Trial                                                            Trial
                                     1.2 Regularized Evolution                                                   1.2         Hill-Climbing                                           1.2            Vizier

          Best normalized function                                                    Best normalized function                                            Best normalized function
                                     1.0                                                                         1.0                                                                 1.0
                                     0.8                                                                         0.8                                                                 0.8
                                     0.6                                                                         0.6                                                                 0.6
                                     0.4                                                                         0.4                                                                 0.4
                                                      Target policy                                                                   Target policy                                           Target policy
                                     0.2              OptFormer                                                  0.2                  OptFormer                                      0.2      OptFormer
                                     0.00 20 40 60 80 100                                                        0.00   20      40 60 80 100                                         0.00   20 40 60          80 100
                                                  Trial                                                                           Trial                                                             Trial

    Figure 9: Best normalized function value of ROSENBROCK ROTATED with std, averaged over 100
    runs.

                                                      Grid Search                                                1.2 Shuffled Grid Search                                            1.2      Random Search                                           1.2      Eagle Strategy

Best normalized function                                                              Best normalized function                                            Best normalized function                                         Best normalized function
                                     2.5                                                                         1.0                                                                 1.0                                                              1.0
                                     0.0                                                                         0.8                                                                 0.8                                                              0.8
                                     2.5                                                                         0.6                                                                 0.6                                                              0.6
                                     5.0                                                                         0.4                                                                 0.4                                                              0.4
                                                    Target policy                                                                 Target policy                                                         Target policy                                          Target policy
                                                    OptFormer                                                    0.2              OptFormer                                          0.2                OptFormer                                     0.2      OptFormer
                                     7.5
                                             0    20 40 60           80 100                                      0.00 20 40 60 80 100                                                0.00   20    40 60 80 100                                        0.00   20 40 60          80 100
                                                          Trial                                                               Trial                                                                 Trial                                                            Trial
                                     1.2 Regularized Evolution                                                   1.2         Hill-Climbing                                           1.2            Vizier

          Best normalized function                                                    Best normalized function                                            Best normalized function
                                     1.0                                                                         1.0                                                                 1.0
                                     0.8                                                                         0.8                                                                 0.8
                                     0.6                                                                         0.6                                                                 0.6
                                     0.4                                                                         0.4                                                                 0.4
                                                      Target policy                                                                   Target policy                                           Target policy
                                     0.2              OptFormer                                                  0.2                  OptFormer                                      0.2      OptFormer
                                     0.00 20 40 60 80 100                                                        0.00   20      40 60 80 100                                         0.00   20 40 60          80 100
                                                  Trial                                                                           Trial                                                             Trial

     Figure 10: Best normalized function value of SUM OF POWERS with std, averaged over 100 runs.

                                     2              Grid Search                                           1.2 Shuffled Grid Search                                              1.2          Random Search                                          1.2        Eagle Strategy

 Best normalized function                                                      Best normalized function                                              Best normalized function                                            Best normalized function
                                                                                                          1.0                                                                   1.0                                                                 1.0
                                     0
                                                                                                          0.8                                                                   0.8                                                                 0.8
                                     2                                                                    0.6                                                                   0.6                                                                 0.6
                                     4                                                                    0.4                                                                   0.4                                                                 0.4
                                                   Target policy                                                           Target policy                                                                Target policy                                          Target policy
                                                   OptFormer                                              0.2              OptFormer                                            0.2                     OptFormer                                   0.2        OptFormer
                                     6
                                         0       20 40 60           80   100                              0.00 20 40 60 80 100                                                  0.00        20    40 60 80 100                                      0.00     20 40 60          80   100
                                                         Trial                                                         Trial                                                                        Trial                                                            Trial
                           1.2 Regularized Evolution                                                      1.2                Hill-Climbing                                      1.2                Vizier

Best normalized function                                                       Best normalized function                                              Best normalized function
                           1.0                                                                            1.0                                                                   1.0
                           0.8                                                                            0.8                                                                   0.8
                           0.6                                                                            0.6                                                                   0.6
                           0.4                                                                            0.4                                                                   0.4
                                            Target policy                                                                            Target policy                                            Target policy
                           0.2              OptFormer                                                     0.2                        OptFormer                                  0.2           OptFormer
                           0.00 20 40 60 80 100                                                           0.00          20     40 60 80 100                                     0.00        20 40 60          80   100
                                        Trial                                                                                    Trial                                                              Trial

  Figure 11: Best normalized function value of GRIEWANK ROSENBROCK with std, averaged over
  100 runs.

                                                                                                                                                 26

                                                               Grid Search                                       1.2    Shuffled Grid Search                                     1.2     Random Search                                         1.2     Eagle Strategy
                                  1000

Best normalized function                                                              Best normalized function                                        Best normalized function                                      Best normalized function
                                                                     Target policy
                                                                     OptFormer                                   1.0                                                             1.0                                                           1.0
                                                                                                                 0.8                                                             0.8                                                           0.8
                                                  0
                                                                                                                 0.6                                                             0.6                                                           0.6
                                                                                                                 0.4                                                             0.4                                                           0.4
                                  1000                                                                                                Target policy                                                 Target policy                                                Target policy
                                                                                                                 0.2                  OptFormer                                  0.2                OptFormer                                  0.2               OptFormer
                                                      0   20     40 60       80 100                              0.00    20     40 60 80 100                                     0.00   20    40 60 80 100                                     0.00   20   40 60 80 100
                                                                   Trial                                                          Trial                                                         Trial                                                        Trial
                                                 1.2 Regularized Evolution                                       1.2          Hill-Climbing                                      1.2           Vizier

                      Best normalized function                                        Best normalized function                                        Best normalized function
                                                 1.0                                                             1.0                                                             1.0
                                                 0.8                                                             0.8                                                             0.8
                                                 0.6                                                             0.6                                                             0.6
                                                 0.4                                                             0.4                                                             0.4
                                                                  Target policy                                                       Target policy                                       Target policy
                                                 0.2              OptFormer                                      0.2                  OptFormer                                  0.2      OptFormer
                                                 0.00 20 40 60 80 100                                            0.00    20     40 60 80 100                                     0.00   20 40 60          80 100
                                                              Trial                                                               Trial                                                         Trial

                                            Figure 12: Best normalized function value for LUNACEK with std, averaged over 100 runs.

                                                                                                                                                27

E.2   Learning priors for hyperparameter response functions

We apply the same goodness-of-fit analysis on function prediction from Section 6.2 to the test split of
HPO-B. The results are shown in Fig. 13.

                                                  1.0        GP

                        Percentage of data with
                                                  0.8        OptFormer
                                                  0.6

                              CDF(y) F
                                                  0.4
                                                  0.2
                                                  0.0
                                                        0.0 0.2 0.4 0.6 0.8 1.0
                                                               CDF level F
                    Figure 13: Fitness of predicted CDF(y) on HPO-B test set.
The ECE metric is defined for a classification model. To obtain a similar measurement for a continuous
regression model, we convert the continuous regression problem into a multi-class classification
problem by discretizing the range [ymin , ymax ] for each study into 100 equal intervals. Then, we
follow the definition of ECE in [27] and estimate the metric using 10 confidence bins.

E.3   Augmenting a prior policy with function prediction

Transfer learning results on HPO-B Fig. 4 shows the best normalized function values observed
so far at each trial. Though HyperBO uses a smaller NN for feature extraction, HyperBO has a
flexible mean function, which captures important information that benefits BO in beginning trials.
While we implemented a bare-bone FSBO, its performance is still better than ABLR in part thanks to
FSBO’s use of a squared exponential kernel instead of a dot-product one. Compared to a dot-product
kernel with a finite feature space, a squared exponential kernel introduces infinite features.
In Fig. 14 and Fig. 15, we show the performance profiles of all compared methods over 2 different
metrics: outperforming 90% of the best function value obtained by all methods at the 50th iteration,
and outperforming the median of the best function values obtained by each method at the 50th
iteration.
Performance profiling is a performance evaluation tool to compare optimization methods, which is
widely used in optimization [69]. In our case, the y-axis is the fraction of tasks that each method
succeeds in at different BO iterations (x-axis). The criteria of success depends on the problem itself,
and we present performance profiles based on 2 different metrics: outperforming 90% of the best
function value obtained by all methods at the 50th iteration, and outperforming the median of the
best function values obtained by each method at the 50th iteration.
Despite the relatively better performance of HyperBO, FSBO, and ABLR especially during earlier
trials as shown by Fig. 4, these methods do not achieve a high percentage success rate on the 86
HPO-B test functions as reflected by Fig. 15. As pointed out by Wang et al. [58], ABLR, FSBO can
be viewed as special cases of HyperBO with specific settings of kernel and mean functions. These
methods have guarantees only if each function (corresponding to each task) is an i.i.d. sample from
the same GP. However, for some search spaces in HPO-B, there exist surrogate functions that return
constant values. The constant surrogate function is unlikely to be an i.i.d. sample from the same
GP as other surrogates in the same search space. This means ABLR, FSBO, and HyperBO can be
sensitive to how the data is generated and outliers in the training data.
Summarizing the results in Fig. 4, Fig. 14 and Fig. 15, HyperBO is able to achieve very good overall
performance on a subset of all search spaces, which leads to a better averaged best normalized
function values. It is likely that these search spaces have surrogate functions that meet the i.i.d
function sample assumption from Wang et al. [58]. However, if we only look at the fraction of tasks

                                                               28

                                  RealWorldData                                                             HPO-B
                                                                                                                                      Random Search
                    0.8                                                                                                               GP-UCB
                                                                                        0.9                                           Vizier

Fraction of tasks                                                   Fraction of tasks
                    0.6                                                                                                               GP *
                                                                                        0.8                                           DGP *
                    0.4                                                                                                               ABLR
                                                                                                                                      FSBO
                    0.2                                                                 0.7                                           HyperBO
                                                                                                                                      OptFormer
                    0.0                                                                                                               OptFormer (EI)
                          0     20 40 60 80 100                                               0     20 40 60 80 100
                          BO Iters needed to outperform 90%                                   BO Iters needed to outperform 90%
                          best function value at the 50th iter.                               best function value at the 50th iter.
   Figure 14: Performance profile on RealWorldData and HPO-B test functions with success threshold:
   90% best function value at 50th iteration.

                                   RealWorldData                                                           HPO-B
                    1.0                                                                                                               Random Search
                                                                                      0.8                                             GP-UCB
                    0.8                                                                                                               Vizier

Fraction of tasks                                                 Fraction of tasks
                                                                                                                                      GP *
                    0.6                                                               0.6                                             DGP *
                    0.4                                                                                                               ABLR
                                                                                                                                      FSBO
                    0.2                                                               0.4                                             HyperBO
                                                                                                                                      OptFormer
                    0.0                                                               0.20                                            OptFormer (EI)
                      0      20 40 60 80 100                                                    20 40 60 80 100
                     BO Iters needed to outperform median                               BO Iters needed to outperform median
                      best function value at the 50th iter.                              best function value at the 50th iter.
   Figure 15: Performance profile on RealWorldData and HPO-B test functions with success threshold:
   median best function value at 50th iteration.

   each method surpasses a success metric, HyperBO may not be a method with superior performance
   that is comparable to the O PT F ORMER. This reveals another benefit of the O PT F ORMER: robustness
   to function outliers.

   HPO-B plotting We further compare the augmented policies from Section 6.3 to the provided
   baselines for HPO-B in [5], using the same plotting format from [5] for fair comparison.

   Figure 16: (Lower is better) Aggregated comparisons of normalized regret and mean ranks across all
   search spaces on the continuous search spaces of HPO-B-v3.

   E.4                    Ablation on acquisition functions

  We provide additional ablations on acquisition function choices on both the RealWorldData and
  HPO-B datasets.

                                                                                                29

                           0.900          RealWorldData                                                                    HPO-B
                                                                                                         0.98
                           0.875

Best normalized function                                                      Best normalized function
                                                                                                         0.97                                         HyperBO
                           0.850                                                                                                                      Vizier
                                                                                                                                                      OptFormer
                           0.825                                                                         0.96                                         OptFormer (EI)
                                                                                                                                                      OptFormer (TS)
                           0.800                                                                         0.95                                         OptFormer (PI)
                                                                                                                                                      OptFormer (UCB)
                           0.775                                                                         0.94
                           0.7500    20      40        60   80   100                                     0.930       20    40        60   80   100
                                                  Trial                                                                         Trial
    Figure 17: Ablation on the choice of acquisition functions. The plot shows the best normalized
    function values averaged over HPO-B test functions. Ablation curves are shown with markers.

                                                                                                                                                     Random Search
                                      RealWorldData                                                                       HPO-B                      GP-UCB
                           1.0                                                                                                                       Vizier
                           0.8                                                           0.8                                                         GP *

Fraction of tasks                                                    Fraction of tasks
                                                                                                                                                     DGP *
                           0.6                                                                                                                       ABLR
                                                                                         0.6                                                         FSBO
                           0.4                                                                                                                       HyperBO
                                                                                         0.4                                                         OptFormer
                           0.2                                                                                                                       OptFormer (EI)
                           0.0                                                                                                                       OptFormer (TS)
                             0      20 40 60 80 100                                                  0      20 40 60 80 100                          OptFormer (PI)
                            BO Iters needed to outperform median                                    BO Iters needed to outperform median             OptFormer (UCB)
                             best function value at the 50th iter.                                   best function value at the 50th iter.
    Figure 18: Ablation on the choice of acquisition functions. The plot shows the performance profile
    metric with success threshold: median best function value at 50th iteration.

    In Fig. 17, we compare the Expected Improvement (EI) used in the main body with Thompson
    Sampling (TS), Probability of Improvement (PI), and Upper Confidence Bound (UCB) with a
    confidence level of 0.9. We also include the best performing standalone baseline, Vizier, and transfer
    learning baseline, HyperBO, for reference. We observe that the prior policy is improved by all the
    acquisition functions. Particularly, O PT F ORMER (EI) is the best among all acquisition functions
    and clearly outperforms all the baseline methods (HyperBO and Vizier) on both datasets across all
    trial steps. O PT F ORMER (UCB) finds good parameter settings as quickly as EI initially, but then
    becomes saturated early, suggesting a less exploratory behavoir than EI. The performance of PI and
    TS increases more slowly, but keeps improving compared to UCB.
    To further bolster this hypothesis, we also compare using performance profiles. As this metric
    depends on the set of methods being compared, we include all baselines from the main body. As we
    can see, Fig. 18 demonstrates that augmented O PT F ORMER policies, especially O PT F ORMER (EI),
    produce superior performance compared to other baselines.

                                                                                                                30

E.5     Out-of-Distribution functions

Fig. 19 compares the optimization trajectories of the prior policy O PT F ORMER, augmented policies
with EI (O PT F ORMER (EI)) and Thompson Sampling (O PT F ORMER (TS)), against Vizier and
Random Search on 5 hold-out test function families from the BBOB benchmark. This assesses
their performance on a few commonly used test functions for general black-box optimization. Both
variants of the augmented policy obtain comparable or better performance than Vizier on most test
functions except O PT F ORMER (TS) on the family of Linear Slope functions.

Figure 19: Best normalized function value of a test function in BBOB averaged over 100 runs with
std of the mean estimate.
In Fig. 20 and Fig. 21, we further ablate the OptFormer on two machine learning tuning tasks: neural
architecture search via NASBench-201 [70] and tuning the learning rate schedule hyperparameters
over a live CIFAR-10 training setup using a ResNet-50 from the init2winit benchmark 1 . This assesses
their performance on out-of-domain machine learning HPO tasks from the training datasets. Again,
O PT F ORMER (EI) and O PT F ORMER (TS) perform comparably or even better than Vizier. This
demonstrate their robust generalization performance over unseen tasks.

   1
       https://github.com/google/init2winit

                                                 31

                                   1.0

        Best normalized function
                                   0.9

                                   0.8                Random Search
                                                      Vizier
                                   0.7                GP-UCB
                                                      OptFormer (TS)
                                                      OptFormer (EI)
                                   0.60   50    100        150       200
                                                Trial
Figure 20: Best normalized function value of NASBench averaged over 10 runs with std of the mean
estimate.

Figure 21: Best CIFAR10 validation accuracy averaged over 10 runs with 25/50/75th percentiles
shown.

                                               32
