---
citation_key: "EggenspergerEtAl2021"
title: "HPOBench: A Collection of Reproducible Multi-Fidelity Benchmark Problems for HPO"
authors: "Katharina Eggensperger; Philipp Müller; Neeratyoy Mallik; Matthias Feurer; René Sass; Aaron Klein; Noor H. Awad; M. Lindauer; Frank Hutter"
year: 2021
doi: ""
source: "arXiv (2109.06716)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "2109.06716"
conversion: "pdftotext -layout (automated)"
---

# HPOBench: A Collection of Reproducible Multi-Fidelity Benchmark Problems for HPO

HPOBench: A Collection of Reproducible
                                                   Multi-Fidelity Benchmark Problems for HPO

                                                 Katharina Eggensperger1∗, Philipp Müller1 , Neeratyoy Mallik1 , Matthias Feurer1 ,
                                                   René Sass2 , Aaron Klein3†, Noor Awad1 , Marius Lindauer2 , Frank Hutter1,4
                                                        1
                                                          Albert-Ludwigs-Universität Freiburg 2 Leibniz Universität Hannover
                                                                 3
                                                                   Amazon 4 Bosch Center for Artificial Intelligence

arXiv:2109.06716v3 [cs.LG] 6 Oct 2022
                                                                                          Abstract

                                                     To achieve peak predictive performance, hyperparameter optimization (HPO) is a
                                                     crucial component of machine learning and its applications. Over the last years,
                                                     the number of efficient algorithms and tools for HPO grew substantially. At
                                                     the same time, the community is still lacking realistic, diverse, computationally
                                                     cheap, and standardized benchmarks. This is especially the case for multi-fidelity
                                                     HPO methods. To close this gap, we propose HPOBench, which includes 7
                                                     existing and 5 new benchmark families, with a total of more than 100 multi-
                                                     fidelity benchmark problems. HPOBench allows to run this extendable set of
                                                     multi-fidelity HPO benchmarks in a reproducible way by isolating and packaging
                                                     the individual benchmarks in containers. It also provides surrogate and tabular
                                                     benchmarks for computationally affordable yet statistically sound evaluations. To
                                                     demonstrate HPOBench’s broad compatibility with various optimization tools,
                                                     as well as its usefulness, we conduct an exemplary large-scale study evaluating
                                                     13 optimizers from 6 optimization tools. We provide HPOBench here: https:
                                                     //github.com/automl/HPOBench.

                                        1       Introduction
                                        The plethora of design choices in modern machine learning (ML) makes research on practical and
                                        effective methods for hyperparameter optimization (HPO) ever more important. In particular, ever-
                                        growing models and datasets create a demand for new HPO methods that are more efficient and
                                        powerful than existing black-box optimization (BBO) methods. Especially if it is only feasible
                                        to evaluate very few models fully, multi-fidelity optimization methods have been shown to yield
                                        impressive results by trading off cheap-to-evaluate proxies and expensive evaluations on the real
                                        target [1–5]. They showed tremendous speedups, such as accelerating the search process in low-
                                        dimensional ML hyperparameter spaces by a factor of 10 to 1000 [2, 5]. However, the development
                                        of such methods often happens in isolation, which potentially prevents HPO research from reaching
                                        its full potential. Prior publications on new HPO methods (i) often relied on artificial test functions
                                        and low-dimensional toy problems, (ii) sometimes introduced a new set of problems, (iii) set up
                                        on different computing environments, having different requirements and interfaces, and (iv) often
                                        did not open-source their code base. All of these make it difficult to compare and develop methods,
                                        necessitating an evolving set of relevant and up-to-date benchmark problems which drives continued
                                        and quantifiable progress in the community.
                                        While there are efforts to simplify benchmarking HPO and global optimization algorithms [6–12],
                                        we are not aware of efforts to collect a diverse set of benchmarks in a single library, with a unified
                                            ∗
                                                {eggenspk,mallik,fh}@cs.uni-freiburg.de
                                            †
                                                work done prior to joining Amazon

                                        35th Conference on Neural Information Processing Systems (NeurIPS 2021) Track on Datasets and Benchmarks.

interface and countering potentially conflicting dependencies that may arise over time. The latter
is particularly important because the rapid evolution of the Python-ML ecosystem can render a
benchmark no longer usable for the community after a major release was published. This creates
a significant hurdle for contribution from the community to grow a benchmark library. To solve
this issue, we propose HPOBench, a benchmark suite for HPO problems, with a special focus
on multi-fidelity problems, licensed under a permissive OSS license (Apache 2.0) and available
at https://github.com/automl/HPOBench. HPOBench provides a common interface and an
infrastructure to isolate benchmarks in their own containers and implements 12 popular benchmark
families, each with multiple problems and preserved with its dependencies in a container for long-term
use. To enable efficient comparisons, most of these benchmarks are table- or surrogate-based, enabling
resource efficient large-scale experiments, which we demonstrate in this work. Our contributions are:

      1. The first available collection of multi-fidelity HPO problems. It contains 12 benchmark
         families with 100+ multi-fidelity HPO problems under a unified interface, comprising
         traditional HPO and neural architecture search (NAS). These benchmarks also define the
         largest collection of black-box HPO problems to date.
      2. The first collection of containerized benchmarks to ensure the longevity, maintainability
         and extensibility of benchmarks.
      3. The first set of HPO benchmarks that are available as both, the raw benchmark and the
         tabular version.
      4. The first HPO benchmark that also supports multi-objective optimization and transfer-HPO
         across datasets (and arbitrary combinations of these with multiple fidelities).
      5. We demonstrate how HPOBench can be used in an exemplary large-scale study with 13
         optimizers from 6 optimization tools, assessing whether advanced methods outperform
         random search and how effective multi-fidelity HPO is.

This paper is structured as follows. We first discuss background on HPO and multi-fidelity optimization (Section 2). Then, we discuss related work on benchmarking (Section 3). Next, we describe
the challenges for an HPO benchmark and how HPOBench alleviates them (Section 4). Then, we
conduct a large-scale comparison of existing, popular HPO methods to demonstrate the usefulness of
HPOBench (in Section 5). We conclude the paper by highlighting further advantages and potential
future work (Section 6).

2     Background on Hyperparameter Optimization
With HPOBench we aim to provide benchmarks to evaluate HPO methods. In the following, we
briefly formalize BBO for HPO and survey multi-fidelity optimization (see Feurer and Hutter [13] for
a detailed overview), both with a focus on the methods used in our experiments.

2.1   Black-box Hyperparameter Optimization

Black-box optimization (BBO) aims to find a solution arg minλ∈Λ f (λ) where f is a black-box
function, for which typically no gradients are available, we cannot make any statements about its
smoothness, convexity and noise level. In summary, the only mode of interaction with black-box
functions is querying them at given inputs λ and measuring the quantity of interest f (λ). In the
context of HPO, λ ∈ Λ is a hyperparameter configuration where the domain Λi of a hyperparameter
is often bounded and continuous, but can also be integer, ordinal or categorical. There are also
so-called conditional hyperparameters [14, 15] defining hierarchical search spaces; however, the first
version of HPOBench focuses on flat configuration spaces first as all optimizers support this.
There are three broad families of BBO methods: (i) purely explorative approaches such as Random
Search (RS) and grid search are simple but sample-inefficient; (ii) model-free Evolutionary Algorithms
(EAs) based on mutation, crossover and selection operators applied to a population of configurations
require comparably large resources to evaluate the entire population but can perform very well
given enough resources; (iii) iterative model-based methods, such as Bayesian Optimization [16],
which are guided by a predictive model trained on prior function evaluations are known as the most
sample-efficient methods. We include representative algorithms from each of these 3 families in our
exemplary experiments in Section 5.

                                                  2

2.2   Multi-fidelity Hyperparameter Optimization

To efficiently optimize today’s ever-growing ML models, multi-fidelity approaches relax the black-box
assumption by allowing cheaper queries at lower fidelities b as well (arg minλ∈Λ f (λ, b)). Examples
for these approximations include dataset subsets [2, 17, 18], feature subsets [19] or lower number of
epochs [19–21]. Multi-fidelity methods have been shown to lead to speedups of up to 1000× over
black-box methods [2, 5]. HPOBench will allow the community to compare different multi-fidelity
methods and in the following we give an overview of representative methods.
A popular multi-fidelity HPO approach that discretizes the fidelity space is Hyperband (HB [19]),
a very simple method with strong empirical performance. It randomly samples new configurations
and allocates more resources to promising configurations by repeatedly calling successive halving
(SH [4]) as a sub-algorithm. The simplicity and effectiveness of HB have been leveraged with
other popular black-box optimizers for improved performance: BOHB [22] combines HB with
Bayesian Optimization (BO) and DEHB [5] combines it with the evolutionary approach of Differential Evolution (DE [23, 24]). The non-HB-based multi-fidelity case has also been researched
extensively [2, 3, 18, 20, 21, 25–28]. Not being limited to predefined fidelity values makes these
methods very powerful, but they rely on strong models to avoid poor choices of fidelities, often
making HB-based fidelity selection more robust. To study the efficacy of multi-fidelity optimization,
in our exemplary experiments in Section 5, we primarily compared black-box optimizers against their
multi-fidelity versions (i.e., RS vs. HB, BO vs. BOHB, and DE vs. DEHB). These experiments show
large speedups of multi-fidelity optimizers in the regime of small compute budgets, whereas for large
compute budgets multi-fidelity optimization is less useful.
Besides multi-fidelity optimization, a very active field of study to speed up HPO is to use transferlearning across datasets [29–33]; we note that transfer HPO methods can also be evaluated with
HPOBench by learning across the datasets within each of its families.

3     Related Work
Proper benchmarking is hard. It is important to be aware of technical and methodological pitfalls,
e.g. comparing implementations instead of algorithms [34, 35], comparing tuned algorithms versus
untuned baselines [36, 37], to not fall for an illusion of progress [38, 39] and to know which sources
of variance exist and control for them [40]. Also, there is a rich literature on how to empirically
evaluate and compare methods in various domains, e.g. evolutionary optimization [41], planning [42],
satisfiability and constraint satisfaction [43], algorithm configuration [44], NAS [45], and also for
benchmarking optimization algorithms [46]. Our goal is not to provide further recommendations on
how and why to benchmark, but to provide concrete benchmarks to simplify development and to
improve the reproducibility and comparability of HPO and in particular multi-fidelity methods.
Furthermore, there have been a lot of efforts to provide optimization benchmarks for the community.
Having a common set of benchmark problems in a unified format fosters and guides research.
Prominent examples in the area of HPO are ACLib [47] for algorithm configuration, COCO [9] for
continuous optimization, Bayesmark [8] for Bayesian optimization, Olympus [12] for optimization of
experiment planning tasks, and HPO-B [48] for transfer-HPO methods (for more, see Appendix B).
However, no benchmark so far has multi-fidelity optimization problems, supports preserving a
diverse set of benchmarks for the longer term (containers), supports multiple objectives, and provides
cheap-to-evaluate surrogate/tabular benchmarks; we hope to close this gap with HPOBench.
Besides benchmarks, competitions are another form of focusing research effort by providing a
common goal and incentive. Famous examples are the AutoML challenges [49], the AutoDL challenge [50], the GECCO BBOB workshop series based on COCO [9] and the NeurIPS 2020 BBO
challenge [51] (for more, see Appendix C). In contrast to these, we do not focus on defining concrete
experimentation protocols, but rather on providing a flexible benchmarking environment to study,
develop and compare optimization methods.

4     HPOBench: A Benchmark Suite for Multi-Fidelity Hyperparameter
      Optimization benchmarks
In this section, we present HPOBench, a collection of HPO benchmarks defined as follows:

                                                  3

Definition 1 (HPO Benchmark) An HPO benchmark consists of a function f : λ −               → R to be
minimized and a (bounded) hyperparameter space Λ with hyperparameters [Λ1 , . . . , Λd ] of type
continuous, integer, categorical or ordinal. In the case of multi-fidelity benchmarks, f can be
queried at lower fidelities, f : λ × b −    → R, and the fidelity space B describes which low-fidelities
[B1 , . . . , Be ] of type continuous, integer or ordinal are available.

Specifically, each benchmark consists of the implementation of that function, which returns at least
one loss. Since this function typically evaluates an ML algorithm, the benchmark defines all relevant
settings, dependencies and inputs, such as datasets, splits and how to compute the loss.
In the remainder of this section, we first discuss the desiderata of a benchmark that aids HPO research
and then highlight the features of HPOBench by detailing how its design fulfills these desiderata.

4.1   Desiderata of an HPO Benchmark

One of the challenges posed to standardized HPO research lies in the varied choices of the underlying
ML components – datasets and their splits, preprocessing, hyperparameter ranges, underlying software
versions, and hardware used. Moreover, the practices applied in HPO research itself can vary along
the lines of optimization budget, number of repetitions, metrics measured and reported. This leads to
inconsistencies and difficulties in comparison of different HPO methods across publications and over
time, affecting the reproducibility of experiments that hinders continued progress in HPO research.
In order to alleviate such issues and encourage participation by the research community, benchmarks
need to standardize these practices to allow the community to be an active stakeholder in developing
and re-using benchmarks. HPOBench is designed to both allow easy, flexible use with a minimal
API that is identical for all benchmarks (see Figure 2); and have a low barrier for contributing new
benchmark problems. We, therefore, identify 3 features of a benchmark that allow its wide-scale
use and long-term applicability: (i) efficiency by providing tabular and surrogate benchmarks for
quick, efficient experiments, along with the original benchmarks; (ii) reproducibility of results by
containerizing benchmarks; and (iii) flexibility by covering different optimization landscapes and
possible use cases, e.g. multi-objective, transfer-HPO, and even multi-fidelity optimization with
multiple fidelity variables. To our knowledge, no other existing benchmarks offer these possibilities.
HPOBench provides a framework to enable standardized, principled research and experimentation.
We list all benchmarks that are included in HPOBench in Table 1 and provide a detailed description
of the respective configuration spaces in Appendix D.

4.2   Efficiency

HPO benchmarks that follow Definition 1 exhibit the drawback that they evaluate a costly function, rendering the empirical comparison of optimization algorithms expensive and ruling out such
benchmarks for interactive development of new methods. To overcome this issue, beside such raw
benchmarks, we also provide two well-established benchmark classes which alleviate this issue:

Definition 2 (Tabular Benchmark) A tabular benchmark returns values from a lookup table with
recorded function values of a raw HPO benchmark instead of evaluating f (λ). The (bounded)
hyperparameter space is restricted to only contain these values and therefore bears a form of
discretization. In the case of multi-fidelity benchmarks, each tabular benchmark has a fidelity space
and the underlying table also contains the recorded function values on the low-fidelities.

Tabular benchmarks are popular in the HPO community as they are easy to distribute and induce little
overhead [52, 30, 53–55], however, they require to discretize the hyperparameter space. Surrogate
benchmarks [56, 57] are an alternative since they provide the original hyperparameter space.

Definition 3 (Surrogate Benchmark) A surrogate benchmark returns function values predicted by
an ML model trained on a tabular benchmark or recorded function values of a raw HPO benchmark.
It reuses the original hyperparameter space and can be extended to the multi-fidelity case as well.

While surrogate benchmarks are similarly cheap to query, the surrogate’s internal ML model adds
extra complexity and the benchmark’s quality crucially depends on the quality of this model and its
training data. Because surrogate benchmarks yield a drop-in replacement for raw benchmarks, they
enjoy widespread adoption in the HPO community [22, 58, 57, 59–62].

                                                   4

                                         Container Env
                                                                         Python Env
          Data set α   Requirements a   Benchmark A
                                                         Python       Optimizer
                                         Container Env    API
          Data set β   Requirements b   Benchmark B

                                                                         Python Env

          Data set α   Requirements     Benchmark A      Python
                            S                                         Optimizer
          Data set β      a b                             API
                                        Benchmark B

Figure 1: Overview of benchmark environments with (upper)                             Figure 2: Code example initializand without (lower) using containers.                                                 ing and evaluating a benchmark.

Furthermore, while HPOBench puts a strong focus on multi-fidelity benchmarks, it also facilitates
evaluating black-box optimization algorithms. In fact, a multi-fidelity benchmark with k different
fidelity levels can be used to define k separate (yet related) benchmarks for black-box optimization.
As such, HPOBench defines more than 400 black-box HPO benchmarks.

4.3        Reproducibility

One of the challenges that come with many new benchmarks is their one-off development and their
lack of maintenance. This means that any new update to the benchmark or its dependencies can easily
lead to conflicts and inconsistencies with respect to software dependencies and possibly old published
results (see Appendix D.1 for examples). While in practice the very same problem, also known as
dependency hell, can also occur on the optimizer side, in this paper we focus on the benchmark side.
HPOBench circumvents such issues through the containerization of benchmarks using Singularity [63]
containers.3 Each benchmark and its dependencies are packaged as a separate container, which isolates
benchmarks from each other and also from the host system. Figure 1 illustrates the advantages that
containerization provides, especially when running multiple benchmarks in the same environment.
Note that without containers, the environment needs to satisfy the union of all of its benchmarks’
requirements (which may actually be mutually exclusive!), while with containers the dependencies
for any given benchmark only need to be satisfied once: for the creation of the container. Importantly,
the dependencies do not need to be satisfied again for using the benchmarks. Each benchmark is
uploaded as a container to a GitLab container registry to provide the history of different versions
of the benchmark. Hence, any benchmark created under the HPOBench paradigm remains usable
without additional bookkeeping or installation overheads for long-term usage. Additionally, no effort
is required for maintaining already containerized benchmarks, as long as the API does not change.
Although not recommended, each benchmark can also be installed locally along with its specific
dependencies without using the containers. We provide a short code sample in Figure 2.
Our notion of reproducibility follows the Claerbout/Donoho/Peng convention as summarized by
Barba [64]. We preserve benchmarks as containers, so that they can be used without installing all
dependencies to obtain the same results. This does not immediately lead to replicability on the
level of the optimization results. Users need to make sure to for example run a sufficient number
of seed replicates to avoid unstable results [65] and to take hardware differences into account when
comparing optimizer overhead. Our work differs from other efforts to provide reproducible research.
We do not aim to make a single experiment reproducible as repo2docker [66] and we also do not aim
to package and distribute the whole runtime or workflows as Jupyter Notebooks [67] or R’s knittr [68].

4.4        Flexibility

HPOBench is a flexible framework that can be used to validate existing HPO research, and develop
and improve HPO algorithms, with a focus on multi-fidelity methods. It consists of two sets of
benchmarks, which we describe in turn: 22 existing multi-fidelity benchmarks from 7 families that
we collected from the multi-fidelity literature (Section 4.4.1); and 88 new benchmarks from 5 families
we created to allow a much more flexible use of HPOBench (Section 4.4.2).
      3
    We chose Singularity over the popular Docker (https://www.docker.com/) alternative as it (1) does not
require super user access and (2) is available on the computer clusters we have access to.

                                                                  5

Table 1: Overview of raw (3), surrogate (7) and tabular ((3)) benchmarks. We report the number of
benchmarks per family (#benchs), the number of continuous (#cont), integer (#int), categorical (#cat),
ordinal (#ord) hyperparameters and how many are log-scaled. Furthermore, we report the fidelity, the
optimization budget and the number of configurations for tabular and surrogate benchmarks.
 Family         #benchs   #cont(log) #int(log) #cat #ord    fidelity      type opt. budget #confs       Ref.
 Cartpole         1          4(1)      3(3)     -    -     repetitions     3        1d           -      [22]
  BNN             2          3(1)      2(2)     -    -      samples        3        1d           -      [22]
  Net             6           5         1       -    -        time         7        7d           -      [22]
                                                                                    7
  NBHPO           4            -         -     3     6      epochs        (3)     10 sec      62 208    [69]
                               -        -      26    -
 NB101            3            -        -      14    -      epochs        (3)     107 sec      423k     [54]
                              21        1      5     -
 NB201            3            -         -     6     -      epochs        (3)     107 sec     15 625    [70]
                               -         -      9    -                                          6 240
 NB1Shot1         3            -         -     9     -      epochs        (3)     107 sec      29 160   [71]
                               -         -     11    -                                        363 648
 LogReg           20         2(2)       -       -    -        iter       3, (3) 100×           625      new
 SVM              20         2(2)       -       -    -       data        3, (3) average        441      new
 RandomForest     20          1        3(2)     -    -      #trees       3, (3) runtime on     10k      new
 XGBoost          20         3(2)      1(1)     -    -      #trees       3, (3) the highest    10k      new
 MLP               8         2(2)      3(2)     -    -      epochs       3, (3) fidelity       30k      new

4.4.1   Existing Community Benchmarks
Firstly, to allow comparability with previous experiments, we collected 22 existing multi-fidelity
benchmarks from 7 families from the multi-fidelity literature; HPOBench preserves these benchmarks
by containerizing them and encapsulating them all under a common API (which was not the case
before). This not only ensures important previous work to remain accessible, but it also bypasses
dependency issues enabling long term usage (see Appendix D.1).
Specifically, these benchmarks comprise raw benchmarks tuning a reinforcement learning agent
(PPO on Cartpole [22]) and a Bayesian neural network (BNN [22]), a random forest-based surrogate
benchmark tuning an MLP (Net [22]) and four popular NAS benchmark families (NBHPO [69],
NB101 [54], NB201 [70], and NB1Shot1 [71]). However, these existing community benchmarks also
have certain limitations: they are only of limited use for transfer HPO (since there are only between 1
and 6 benchmarks per family), they only offer a single fidelity dimension, and they only evaluate a
single metric. We therefore augmented them with 5 new families of benchmarks we describe next.

4.4.2   New Benchmarks
To substantially increase the range of possible applications of HPOBench, we defined 5 new benchmark families with up to 20 different datasets per family, comprising a total of 88 new multi-fidelity
benchmarks. These new benchmarks also provide multiple metrics and multiple fidelity dimensions
to go beyond the aforementioned limitations of the community benchmarks.
Our new benchmarks are based on the following popular ML algorithms: SVM, LogReg, XGBoost,
RandomForest, and MLP. All of them evaluate the respective ML algorithm as implemented in
scikit-learn [72] and XGBoost [73] on 20 publicly available datasets (8 for the MLP due to its high
computational cost) from the OpenML AutoML benchmark [74]. We give the OpenML [75] task IDs
in Table 7 in Appendix D, which provide fixed train-test splits; for each such task, we used 33% of
the training set as the validation split, determined through stratified sampling under a fixed seed. The
entire objective function then consists of preprocessing, training the model on the remaining 67% of
the fixed OpenML training split, prediction on the fixed validation split, evaluating 4 different metrics
(see Appendix D.3), and recording model fit and inference times. The fidelities are algorithm-specific
if possible (number of trees, iterations, epochs) or dataset subsets otherwise (which is used for SVM).
These benchmarks are available both as raw and tabular versions, have the same API and exist in

                                                    6

independent, non-conflicting containers; for the tabular versions, we discretized each hyperparameter
(and fidelity) and evaluated 5 different seeds for each configuration of the resulting grid.
Also, four of our new benchmark families (LogReg, RandomForest, XGBoost, MLP) allow up to
two fidelity dimensions. This enables the development and benchmarking of methods for multifidelity optimization with multiple fidelity dimensions, a direction that we deem very promising yet
understudied. Similarly, our tabular data collected over multiple datasets (up to 20) allows the effective
use of these benchmarks for transfer-HPO, and the recording of multiple evaluation metrics also
allows these benchmarks to be used for multi-objective optimization. Moreover, each configuration
is recorded on different fidelities with their associated costs, which further lends HPOBench great
potential in future research in cost-based meta-learning or multi-fidelity multi-objective optimization.
To demonstrate the diversity of our new benchmarks, we show the empirical cumulative distribution
function (ECDF) for each family in Figure 3. Each line corresponds to one dataset and shows how the
objective values are distributed. From the varying amounts of well and badly performing normalized
regrets we can conclude that the benchmarks yield different landscapes and thus are diverse in
smoothness, resulting in varying algorithm performance. Moreoever, the 5 new spaces vary in their
dimensionality (up to 5 for MLP), in the hyperparameter data types and their range (see Appendix D).

       LogReg            RandomForest               SVM                 XGBoost                 MLP

Figure 3: Empirical cumulative distribution. Each plot corresponds to one ML algorithm, and each
line within a plot corresponds to one dataset. The lines show the ECDF of the normalized regret of
all evaluated configurations of the respective ML algorithm on the respective dataset.

5     Experiments

Now, we turn to an exemplary use of our benchmarks in order to demonstrate some features of
HPOBench and its utility for HPO research. We used our benchmark suite to run a large-scale
empirical study comparing 13 optimization methods on our 12 benchmark families (we report
detailed results in Appendix H). We first give details on the experimental setup and then study the
following two exemplary research questions: (RQ1) Do advanced methods improve over random
baselines? and (RQ2) Do multi-fidelity methods improve over single-fidelity methods?

5.1   Experimental Setup

For each benchmark and optimizer, we conducted 32 repetitions with different seeds to avoid reliance
on individual seeds [65]. For our new benchmarks, which have multiple metrics, we minimized
1−accuracy. For each run, we allowed an optimization budget as described in Table 1 and accumulate
time taken by the benchmark (recorded time for tabular benchmarks, predicted time for surrogate
benchmarks and wallclock time for raw benchmarks; for our new benchmarks, we used the tabular
versions to avoid unnecessary compute costs and CO2 exhaustion) and the optimizer (wallclock time).
We kept track of all evaluations and computed trajectories, i.e., the best-seen value at each time step,
as follows: If for an evaluation we cannot find another evaluation conducted on the same or a higher
fidelity, we treat it as the best-seen value; if it is on the highest fidelity evaluated so far, we treat it as
the best seen value if it has a lower loss than the best-seen so far on that fidelity; otherwise, we do not
consider this evaluation for the trajectory. This decision reflects the multi-fidelity setting, where a
higher budget results in a better estimate of the actual value of interest but can cause jumps in the
optimization trajectory, (e.g., when a configuration is the first to be evaluated on a higher budget but
is worse than the best configuration on a lower budget). To aggregate and report results, we use either
the final performance (per benchmark, see Appendix H), performance-over-time (per benchmark, see
Appendix H) or rank-over-time (across multiple benchmarks). For tabular and surrogate benchmarks

                                                      7

we report optimization regret (the difference between the best-found value and the best-known value)
and for the other benchmarks, we report the actual optimized objective value.4
We give details on the hardware and required compute resources in Appendix E and F and release
code for the experiments here: https://github.com/automl/HPOBenchExperimentUtils.

5.2       Considered Optimizers

We evaluated a wide set of optimizers including baselines for black-box and multi-fidelity optimization. Our selection of optimizers does not aim at finding the best optimization algorithm, but to study
a broad range of different implementations and tools (for more details see Appendix G). As black-box
optimizers, which only access the highest fidelity, we considered random search (RS), differential
evolution (DE [23, 24]) and BO with different models: a Gaussian Process model (BOGP [76–78]), a
random forest (BORF [79]), a kernel density estimator (KDE) (BOKDE [22]). Lastly, we also used
the winning solution of the NeurIPS BBO challenge (HEBO [80, 51]). For multi-fidelity optimization,
we used multi-fidelity extensions of some methods mentioned above: Hyperband (HB [19]) and
its combination with KDE-based BO (BOHB [22]), with RF-based BO (SMAC-HB [78]) and with
DE (DEHB [5]). Additionally, we use Dragonfly [81] using a GP with multi-fidelity optimization
and combinations of optimization and multi-fidelity algorithms implemented in Optuna [82] (see
appendix). 5

5.3       RQ1: Do advanced methods improve over random search?

To demonstrate the validity of our benchmarks, we independently replicate the findings of the 1st
NeurIPS Blackbox Optimization challenge [51]: “decisively showing that BO and similar methods
are superior choices over RS and grid search for tuning hyperparameters of ML models”. While
this question has already been studied before [14, 83, 36, 15, 33, 84, 85], we will also study it
w.r.t. multi-fidelity optimization and using the popular HB baseline. We leave out grid search as RS
has been shown to be superior [83] and as there is no multi-fidelity version of grid search.
We report ranks-over-time in Figure 4, comparing black-box (DE, BOGP , BORF , HEBO, BOKDE ;
1st column) and multi-fidelity (BOHB, DEHB, SMAC-HB, DF; 2nd column) optimizers on existing
community (top row) and new (bottom row) benchmarks. On both benchmark sets, most black-box
and multi-fidelity optimizers clearly outperform the respective baseline (RS (blue) and HB (light
green)) on average. We also observe that BO improves over the evolutionary algorithm DE in
the beginning, but, except for HEBO, looses to it in the very long run on the existing community
benchmarks [86, 60, 5]. This does not happen on the new benchmarks, as their time limits are set
more aggressively and the methods developed for this setting (HEBO [80], BOGP , BORF and SMAC-
HB [77]) achieve lower ranks. Considering per-benchmark results (Appendix H), we also observe
that methods which appear clearly inferior in the ranking plots perform very well on individual
benchmarks (e.g. DF 6 on NB201). Finally, we find HEBO to substantially improve over all other
black-box methods.
Besides qualitative measures, we also quantitatively measure whether the advanced methods outperform the respective baselines by counting the number of wins, ties and losses and using the sign test
to verify significance [87] on the existing community benchmarks in Table 2 (the new benchmarks
yield similar results; see Appendix H). We can observe that four out of five black-box methods are
significantly better than RS. In the multi-fidelity case, only two out of four methods are significantly
better than HB and one method is consistently worse than HB. Overall, we conclude that advanced
methods consistently outperform random search.

      4
       Since we study optimizers, we report optimization performance (in the case of ML the validation performance, which is the objective value seen by the optimizer. We note that HPOBench in principle allows to
compute test performance (the loss computed on a separate test set on the highest fidelity).
     5
       We include this framework to show compatibility of HPOBench with popular frameworks, but note that it
expects to freeze and thaw evaluations. HPOBench implements a stateless objective function and, thus, runs
that could be thawed and continued instead get accounted the full costs of rerunning them, which slows down
optimization. We defer stateful benchmarks to future work.
     6
       We would like to note that the bad rank of DF for some benchmarks is due to its overhead which prevented
it from spending sufficient budget on function evaluations; see Section F for details.

                                                      8

Table 2: P-value of a sign test for the hypothesis that advanced methods outperform the baseline RS
for black-box optimization and HB for multi-fidelity optimization. We underline p-values that are
below α = 0.05 and boldface p-values that are below α = 0.05 after multiple comparison correction
(dividing α by the number of comparisons, i.e. 5 and 4; boldface/underlined implies that the advanced
method is better than RS/HB). We also give the wins/ties/losses of RS and HB against the challengers.
                                           DE        BOGP         BORF         HEBO    BOKDE
        p-value against RS            0.00043      0.01330     0.00001      0.00217    0.06690
        wins/ties/losses against RS     18/2/2      15/3/4       19/3/0       17/2/3    13/4/5
                                          BOHB       DEHB     SMAC-HB            DF
        p-value against HB              0.06690   0.00001      0.00011       0.99783
        wins/ties/losses against HB      13/4/5     20/2/0       18/3/1       5/0/17

        black-box                multi-fidelity     black-box + multi-fidelity          subsets

Figure 4: Mean rank-over-time across 32 repetitions of different sets of optimizers (lower is better).
The left part shows rank across all existing community (upper row) and new (lower row) benchmarks .
The right part reports results on the existing community benchmarks only for subsets of optimizers.

5.4 RQ2: Do multi-fidelity methods improve over black-box methods?

Next, we study whether multi-fidelity optimization methods are able to consistently improve over
black-box optimization methods given a fixed time budget. For this, we look again at ranking-overtime in Figure 4. We first compare black-box methods with their respective multi-fidelity extension,
i.e., DE vs. DEHB and BOKDE vs. BOHB in the two plots in the rightmost column. We can see
that in the beginning HB and the multi-fidelity optimizers perform very similarly and consistently
outperform RS and the respective black-box version. After a while, the multi-fidelity versions improve
over the HB baseline, and given enough time, the black-box versions catch up. Second, we compare
all optimizers on the existing community (3rd column, top) and new (3rd column, bottom) benchmarks.
Here, we can observe a similar pattern in that HB is a very competitive baseline in the beginning but
is outperformed first by the advanced multi-fidelity methods and then also by the black-box methods.
This is less pronounced on the new benchmarks, which we attribute to the tighter time limits.
Similarly to RQ1, we counted wins, ties and losses and used the sign test to verify significance [87] on
the community benchmarks for 100%, 10% and 1% of the total budget in Table 3 (the new benchmarks
yield similar results; see Appendix H). We can observe that only HB is able to outperform its blackbox counterpart for all three budgets we check. For three multi-fidelity methods there is a significant
improvement over the black-box methods for 1% of the budget. For the full budget we can no longer
state that any of the advanced multi-fidelity methods is statistically better than their counterpart, but
judging by the wins and losses, the advanced multi-fidelity methods are still competitive.
Overall, multi-fidelity optimizers outperform black-box optimizers for relatively small compute
budgets. Given enough budget, black-box optimizers become competitive with their multi-fidelity

                                                   9

versions; in particular, DE and BORF performed very well in the end. However, we need to take into
account that for the existing community benchmarks the potential catch-up (if at all) only happens after
a very substantial amount of (simulated) wallclock time (e.g., 10 Mio. seconds). Hence, multi-fidelity
methods are crucial to efficiently tackle real, expensive optimization problems.

Table 3: P-values of a sign test for the hypothesis that multi-fidelity outperform their black-box
counterparts. We boldface p-values that are below α = 0.05 (implying that multi-fidelity is better).
       Budget               HB vs RS    DEHB vs DE      BOHB vs BOKDE        SMAC-HB vs BORF
                 p-values   0.00074          0.73827              0.14314               0.73827
       100%
                    w/t/l     16/5/1           6/8/8               12/4/6                 6/8/8
                 p-values   0.00845          0.09462              0.14314               0.50000
       10%
                    w/t/l     16/2/4          10/9/3               12/4/6                 8/7/7
                 p-values   0.00074         0.03918               0.06690              0.03918
       1%
                    w/t/l     17/3/2          14/3/5               14/2/6                13/5/4

To conclude, in general when low-fidelities are available and they are representative of the true
objective function, multi-fidelity methods are clearly beneficial. In practice, we found that DEHB
and SMAC-HB are reliable multi-fidelity optimizers that work well across the whole collection of
benchmarks, while other multi-fidelity optimizers are not able to improve over HB consistently. By
exploring a very broad range of benchmarks, we also found an existence proof that black-box methods
can outperform multi-fidelity methods for very high budgets and that even advanced methods can
be outperformed by RS in individual benchmarks. We pose it as a challenge to the field to develop
methods that do not exhibit poor performance in any of the many benchmarks in HPOBench.

6   Discussion and Future Work
We proposed HPOBench, a library for multi-fidelity HPO benchmarks. It serves two purposes: (a) to
provide benchmarks with a unified API, and (b) to make them easy to install and use by containerizing
them and thus enable rapid prototyping and the development of new multi-fidelity methods that
are crucial for ML research and applications. Finally, our library is open-source and we welcome
contributions of new benchmarks to keep the library up-to-date and evolve it.
On the technical side, so far, we focused on developing a benchmark library, but we see a large
potential in connecting our library with other benchmarking frameworks (e.g. COCO [9] and
Bayesmark [8]), optimization frameworks (e.g. Nevergrad [10] and Sherpa [88]) and extending it
with further benchmarks [11, 12, 60, 62, 89–91] to increase diversity and to simplify evaluation and
comparison of optimizers. For this, it would be interesting to also containerize the optimizers since
they can suffer from the same issues as benchmarks. Furthermore, so far, HPOBench only contains
stateless benchmarks starting a single container. We would like to extend the library to also support
optimizers requiring stateful benchmarks (to freeze and thaw evaluations) or running in parallel.
Our set of benchmarks already covers raw, tabular, and surrogate benchmarks, but it would be
useful to have all three versions available for all benchmarks, and to automatically generate tabular
and surrogate-based benchmarks from raw benchmarks. Also, our new benchmarks can be used
to evaluate multi-objective (multiple metrics) and meta-learning (across datasets) methods or even
meta-learned multi-fidelity multi-objective methods. We hope for the community to play a large
role in defining the protocols for the different special cases; e.g., budgets need to be set differently
for black-box multi-objective optimization and single-objective hyperparameter transfer learning.
Additionally, it would be interesting to study hierarchical search spaces to cover work in AutoML.
Furthermore, there is a large potential in automatically creating multi-fidelity benchmarks from any
ML algorithm by using data subsets as a low-fidelity.
We also conducted a large-scale study evaluating 13 algorithm implementations to demonstrate
compatibility with a wide range of optimization tools, and we thus believe that our library is well
suited for future research on multi-fidelity optimization. We showed that advanced HPO methods are
preferable over RS and HB baselines, and that multi-fidelity extensions of popular optimizers improve
over their black-box version. Lastly, to reduce computational effort, we would like to study whether
we can learn which benchmarks are hard and whether there is a representative subset of them [92].

                                                  10

Acknowledgments and Disclosure of Funding
We would like to thank Stefan Stäglich and Archit Bansal for code contributions and Stefan Falkner
for useful discussions and comments on an early draft of this project. This work has partly been
supported by the European Research Council (ERC) under the European Union’s Horizon 2020
research and innovation programme under grant no. 716721, and by TAILOR, a project funded
by EU Horizon 2020 research and innovation programme under GA No 952215. Robert Bosch
GmbH is acknowledged for financial support. The authors also acknowledge support by the state of
Baden-Württemberg through bwHPC and the German Research Foundation (DFG) through grant no
INST 39/963-1 FUGG.

References
  [1] A. Forrester, A. Sóbester, and A. Keane. Multi-fidelity optimization via surrogate modelling.
      Proceedings of the royal society A: mathematical, physical and engineering sciences, 463
      (2088):3251–3269, 2007.
  [2] A. Klein, S. Falkner, S. Bartels, P. Hennig, and F. Hutter. Fast Bayesian optimization of machine
      learning hyperparameters on large datasets. In A. Singh and J. Zhu, editors, Proceedings of
      the Seventeenth International Conference on Artificial Intelligence and Statistics (AISTATS),
      volume 54. Proceedings of Machine Learning Research, 2017.
  [3] K. Kandasamy, G. Dasarathy, J. Schneider, and B. Póczos. Multi-fidelity Bayesian optimisation
      with continuous approximations. In D. Precup and Y. Teh, editors, Proceedings of the 34th
      International Conference on Machine Learning (ICML’17), volume 70, pages 1799–1808.
      Proceedings of Machine Learning Research, 2017.
  [4] K. Jamieson and A. Talwalkar. Non-stochastic best arm identification and hyperparameter
      optimization. In A. Gretton and C. Robert, editors, Proceedings of the Seventeenth Interna-
      tional Conference on Artificial Intelligence and Statistics (AISTATS), volume 51. Proceedings
      of Machine Learning Research, 2016.
  [5] N. Awad, N. Mallik, and F. Hutter. DEHB: Evolutionary hyberband for scalable, robust
      and efficient hyperparameter optimization. In Z. Zhou, editor, Proceedings of the Thirtieth
      International Joint Conference on Artificial Intelligence, IJCAI-21, pages 2147–2153. ijcai.org,
      2021.
  [6] K. Eggensperger, M. Feurer, F. Hutter, J. Bergstra, J. Snoek, H. Hoos, and K. Leyton-Brown.
      Towards an empirical foundation for assessing Bayesian optimization of hyperparameters. In
      NeurIPS Workshop on Bayesian Optimization in Theory and Practice (BayesOpt’13), 2013.
  [7] C. Doerr, H. Wang, F. Ye, S. van Rijn, and T. Bäck. Iohprofiler: A benchmarking and profiling
      tool for iterative optimization heuristics. arXiv:1810.05281 [cs.NE], 2018.
  [8] R. Turner and D. Eriksson. Bayesmark: Benchmark framework to easily compare bayesian
      optimization methods on real machine learning tasks. github.com/uber/bayesmark, 2019.
  [9] N. Hansen, A. Auger, R. Ros, O. Mersman, T. Tušar, and D. Brockhoff. COCO: A platform for
      comparing continuous optimizers in a black-box setting. Optimization Methods and Software,
      2020.
 [10] J. Rapin and O. Teytaud. Nevergrad - A gradient-free optimization platform. https://
      GitHub.com/FacebookResearch/Nevergrad, 2018.
 [11] L. Bliek, A. Guijt, R. Karlsson, S. Verwer, and M. de Weerdt. EXPObench: Benchmarking
      surrogate-based optimisation algorithms on expensive black-box functions. arXiv:2106.04618
      [cs.LG], 2021.
 [12] F. Häse, M. Aldeghi, R. Hickman, L. Roch, M. Christensen, E. Liles, J. Hein, and A. Aspuru-
      Guzik. Olympus: a benchmarking framework for noisy optimization and experiment planning.
      Machine Learning: Science and Technology, 2(3), 2021.

                                                 11

[13] M. Feurer and F. Hutter. Hyperparameter optimization. In Hutter et al. [93], chapter 1, pages
     3–38. Available for free at http://automl.org/book.
[14] J. Bergstra, R. Bardenet, Y. Bengio, and B. Kégl. Algorithms for hyper-parameter optimization.
     In J. Shawe-Taylor, R. Zemel, P. Bartlett, F. Pereira, and K. Weinberger, editors, Proceedings
     of the 24th International Conference on Advances in Neural Information Processing Systems
     (NeurIPS’11), pages 2546–2554. Curran Associates, 2011.
[15] C. Thornton, F. Hutter, H. Hoos, and K. Leyton-Brown. Auto-WEKA: combined selection and
     hyperparameter optimization of classification algorithms. In I. Dhillon, Y. Koren, R. Ghani,
     T. Senator, P. Bradley, R. Parekh, J. He, R. Grossman, and R. Uthurusamy, editors, The 19th
     ACM SIGKDD International Conference on Knowledge Discovery and Data Mining (KDD’13),
     pages 847–855. ACM Press, 2013.
[16] B. Shahriari, K. Swersky, Z. Wang, R. Adams, and N. de Freitas. Taking the human out of the
     loop: A review of Bayesian optimization. Proceedings of the IEEE, 104(1):148–175, 2016.
[17] J. Petrak. Fast subsampling performance estimates for classification algorithm selection.
     Technical Report TR-2000-07, Austrian Research Institute for Artificial Intelligence, 2000.
[18] K. Swersky, J. Snoek, and R. Adams. Multi-task Bayesian optimization. In C. Burges,
     L. Bottou, M. Welling, Z. Ghahramani, and K. Weinberger, editors, Proceedings of the 26th In-
     ternational Conference on Advances in Neural Information Processing Systems (NeurIPS’13),
     pages 2004–2012. Curran Associates, 2013.
[19] L. Li, K. Jamieson, G. DeSalvo, A. Rostamizadeh, and A. Talwalkar. Hyperband: A novel
     bandit-based approach to hyperparameter optimization. Journal of Machine Learning Research,
     18(185):1–52, 2018.
[20] K. Swersky, J. Snoek, and R. Adams. Freeze-thaw Bayesian optimization. arXiv:1406.3896
     [stats.ML], 2014.
[21] T. Domhan, J. Springenberg, and F. Hutter. Speeding up automatic hyperparameter op-
     timization of deep neural networks by extrapolation of learning curves. In Q. Yang and
     M. Wooldridge, editors, Proceedings of the 25th International Joint Conference on Artificial
     Intelligence (IJCAI’15), pages 3460–3468, 2015.
[22] S. Falkner, A. Klein, and F. Hutter. BOHB: Robust and efficient hyperparameter optimization
     at scale. In J. Dy and A. Krause, editors, Proceedings of the 35th International Conference
     on Machine Learning (ICML’18), volume 80, pages 1437–1446. Proceedings of Machine
     Learning Research, 2018.
[23] R. Storn and K. Price. Differential evolution – a simple and efficient heuristic for global
     optimization over continuous spaces. Journal of Global Optimization, 11:341–359, 1997.
[24] N. Awad, N. Mallik, and F. Hutter. Differential evolution for neural architecture search. In
     Proceedings of the 1st workshop on neural architecture search@ICLR’20, 2020.
[25] D. Golovin, B. Solnik, S. Moitra, G. Kochanski, J. Karro, and D. Sculley. Google Vizier: A
     service for black-box optimization. In S. Matwin, S. Yu, and F. Farooq, editors, Proceedings of
     the 23rd ACM SIGKDD International Conference on Knowledge Discovery and Data Mining
     (KDD), pages 1487–1495. ACM Press, 2017.
[26] J. Wu, S. Toscano-Palmerin, P. Frazier, and A. Wilson. Practical multi-fidelity Bayesian
     optimization for hyperparameter tuning. In R. Adams and V. Gogate, editors, Proceedings
     of The 35th Uncertainty in Artificial Intelligence Conference (UAI’20), volume 115, pages
     788–798. Proceedings of Machine Learning Research, 2020.
[27] S. Takeno, H. Fukuoka, Y. Tsukada, T. Koyama, M. Shiga, I. Takeuchi, and M. Karasuyama.
     Multi-fidelity Bayesian optimization with max-value entropy search and its parallelization.
     In H. Daume III and A. Singh, editors, Proceedings of the 37th International Conference
     on Machine Learning (ICML’20), volume 98, pages 9334–9345. Proceedings of Machine
     Learning Research, 2020.

                                                12

[28] J. Song, Y. Chen, and Y. Yue. A general framework for multi-fidelity Bayesian optimization
     with Gaussian processes. In K. Chaudhuri and M. Sugiyama, editors, Proceedings of the 22nd
     International Conference on Artificial Intelligence and Statistics (AISTATS), pages 3158–3167.
     Proceedings of Machine Learning Research, 2019.
[29] J. Vanschoren. Meta-learning. In Hutter et al. [93], chapter 2, pages 35–61. Available for free
     at http://automl.org/book.
[30] R. Bardenet, M. Brendel, B. Kégl, and M. Sebag. Collaborative hyperparameter tuning. In
     Dasgupta and McAllester [94], pages 199–207.
[31] D. Yogatama and G. Mann. Efficient transfer learning method for automatic hyperparameter
     tuning. In S. Kaski and J. Corander, editors, Proceedings of the Seventeenth International
     Conference on Artificial Intelligence and Statistics (AISTATS), volume 33, pages 1077–1085.
     Proceedings of Machine Learning Research, 2014.
[32] M. Feurer, J. Springenberg, and F. Hutter. Initializing Bayesian hyperparameter optimization
     via meta-learning. In Bonet and Koenig [95], pages 1128–1135.
[33] M. Wistuba, N. Schilling, and L. Schmidt-Thieme. Scalable Gaussian process-based transfer
     surrogates for hyperparameter optimization. Machine Learning, 107(1):43–78, 2018.
[34] H. Kriegel, E. Schubert, and A. Zimek. The (black) art of runtime evaluation: Are we
     comparing algorithms or implementations? Knowledge Information System, 52(2):341–378,
     2017.
[35] S. Narang, H. Chung, Y. Tay, W. Fedus, T. Fevry, M. Matena, K. Malkan, N. Fiedel, N. Shazeer,
     Z. Lan, Y. Zhou, W. Li, N. Ding, J. Marcus, A. Roberts, and C. Raffel. Do transformer
     modifications transfer across implementations and applications? arxiv:2102.11972 [cs.LG],
     2021.
[36] J. Bergstra, D. Yamins, and D. Cox. Making a science of model search: Hyperparameter
     optimization in hundreds of dimensions for vision architectures. In Dasgupta and McAllester
     [94], pages 115–123.
[37] G. Melis, C. Dyer, and P. Blunsom. On the state of the art of evaluation in neural language mod-
     els. In Proceedings of the International Conference on Learning Representations (ICLR’18),
     2018. Published online: iclr.cc.
[38] D. Hand. Classifier Technology and the Illusion of Progress. Statistical Science, 21(1):1 – 14,
     2006.
[39] M. Dacrema, P. Cremonesi, and D. Jannach. Are we really making much progress? a worrying
     analysis of recent neural recommendation approaches. In RecSys ’19: Proceedings of the
     13th ACM Conference on Recommender Systems, page 101–109. Association for Computing
     Machinery, 2019.
[40] X. Bouthillier, P. Delaunay, M. Bronzi, A. Trofimov, B. Nichyporuk, J. Szeto, N. Mohammadi
     Sepahvand, E. Raff, K. Madan, V. Voleti, S. Ebrahimi Kahou, V. Michalski, T. Arbel, C. Pal,
     G. Varoquaux, and P. Vincent. Accounting for variance in machine learning benchmarks. In
     A. Smola, A. Dimakis, and I. Stoica, editors, Proceedings of Machine Learning and Systems 3,
     volume 3, pages 747–769, 2021.
[41] T. Weise, R. Chiong, and K. Tang. Evolutionary optimization: Pitfalls and booby traps. Journal
     of Computer Science and Technology, 27:907–936, 2012.
[42] A. Howe and E. Dahlman. A critical assessment of benchmark comparison in planning.
     Journal of Artificial Intelligence Research, 17:1–33, 2002.
[43] I. Gent, S. Grant, E. MacIntyre, P. Prosser, P. Shaw, B. Smith, and T. Walsh. How not to do it.
     Technical Report 97.92, University of Leeds, 1997.
[44] K. Eggensperger, M. Lindauer, and F. Hutter. Pitfalls and best practices in algorithm configu-
     ration. Journal of Artificial Intelligence Research, pages 861–893, 2019.

                                                13

[45] M. Lindauer and F. Hutter. Best practices for scientific research on neural architecture search.
     Journal of Machine Learning Research, 21:1–18, 2020.
[46] T. Bartz-Beielstein, C. Doerr, J. Bossek, S. Chandrasekaran, T. Eftimov, A. Fischbach,
     P. Kerschke, M. López-Ibáñez, K. Malan, J. Moore, B. Naujoks, P. Orzechowski, V. Volz,
     M. Wagner, and T. Weise. Benchmarking in optimization: Best practice and open issues.
     arXiv:2007.03488v2 [cs.NE], 2020.
[47] F. Hutter, M. López-Ibánez, C. Fawcett, M. Lindauer, H. Hoos, K. Leyton-Brown, and T. Stüt-
     zle. AClib: a benchmark library for algorithm configuration. In P. Pardalos and M. Resende,
     editors, Proceedings of the Eighth International Conference on Learning and Intelligent
     Optimization (LION’14), Lecture Notes in Computer Science, pages 36–40. Springer, 2014.
[48] S. Pineda, H. Jomaa, M. Wistuba, and J. Grabocka. HPO-B: A large-scale reproducible
     benchmark for black-box HPO based on OpenML. In Vanschoren et al. [96].
[49] I. Guyon, L. Sun-Hosoya, M. Boullé, H. Escalante, S. Escalera, Z. Liu, D. Jajetic, B. Ray,
     M. Saeed, M. Sebag, A. Statnikov, W. Tu, and E. Viegas. Analysis of the AutoML Challenge
     Series 2015-2018. In Hutter et al. [93], chapter 10, pages 177–219. Available for free at
     http://automl.org/book.
[50] Z. Liu, A. Pavao, Z. Xu, S. Escalera, F. Ferreira, I. Guyon, S. Hong, F. Hutter, R. Ji, J. Jacques
     Junior, G. Li, M. Lindauer, Z. Luo, M. Madadi, T. Nierhoff, K. Niu, C. Pan, D. Stoll, S. Treguer,
     J. Wang, P. Wang, C. Wu, , Y. Xiong, A. Zela, and Y. Zhang. Winning solutions and post-
     challenge analyses of the ChaLearn AutoDL challenge 2019. hal-02957135, 2020.
[51] R. Turner, D. Eriksson, M. McCourt, J. Kiili, E. Laaksonen, Z. Xu, and I. Guyon. Bayesian
     optimization is superior to random search for machine learning hyperparameter tuning: Analy-
     sis of the black-box optimization challenge 2020. In H. Escalante and K. Hofmann, editors,
     Proceedings of the NeurIPS 2020 Competition and Demonstration Track, volume 133 of
     Proceedings of Machine Learning Research, pages 3–26, 2021.
[52] J. Snoek, H. Larochelle, and R. Adams. Practical Bayesian optimization of machine learning
     algorithms. In P. Bartlett, F. Pereira, C. Burges, L. Bottou, and K. Weinberger, editors, Pro-
     ceedings of the 25th International Conference on Advances in Neural Information Processing
     Systems (NeurIPS’12), pages 2960–2968. Curran Associates, 2012.
[53] M. Wistuba, N. Schilling, and L. Schmidt-Thieme. Two-stage transfer surrogate model for
     automatic hyperparameter optimization. In F. Paolo, N. Landwehr, G. Manco, and J. Vreeken,
     editors, Machine Learning and Knowledge Discovery in Databases (ECML/PKDD’16), Lec-
     ture Notes in Computer Science, pages 199–214. Springer, 2016.
[54] C. Ying, A. Klein, E. Real, E. Christiansen, K. Murphy, and F. Hutter. NAS-Bench-101:
     Towards reproducible neural architecture search. In Chaudhuri and Salakhutdinov [97], pages
     7105–7114.
[55] L. Metz, N. Maheswaranathan, R. Sun, C. Freeman, B. Poole, and J. Sohl-Dickstein. Using
     a thousand optimization tasks to learn hyperparameter search strategies. arXiv:2002.11887
     [cs.LG], 2020.
[56] K. Eggensperger, F. Hutter, H. Hoos, and K. Leyton-Brown. Efficient benchmarking of
     hyperparameter optimizers via surrogates. In Bonet and Koenig [95], pages 1114–1120.
[57] K. Eggensperger, M. Lindauer, H. H. Hoos, F. Hutter, and K. Leyton-Brown. Efficient
     benchmarking of algorithm configurators via model-based surrogates. Machine Learning, 107
     (1):15–41, 2018.
[58] V. Perrone, R. Jenatton, M. Seeger, and C. Archambeau. Scalable hyperparameter transfer
     learning. In S. Bengio, H. Wallach, H. Larochelle, K. Grauman, N. Cesa-Bianchi, and
     R. Garnett, editors, Proceedings of the 31st International Conference on Advances in Neural
     Information Processing Systems (NeurIPS’18), pages 12751–12761. Curran Associates, 2018.
[59] R. Martinez-Cantin. Funneled Bayesian optimization for design, tuning and control of au-
     tonomous systems. IEEE Transactions on Cybernetics, 49(4):1489–1500, 2019.

                                                 14

[60] A. Klein, Z. Dai, F. Hutter, N. Lawrence, and J. Gonzalez. Meta-surrogate benchmarking for
     hyperparameter optimization. In Wallach et al. [98], pages 6267–6277.
[61] E. Daxberger, A. Makarova, M. Turchetta, and A. Krause. Mixed-variable bayesian optimiza-
     tion. In C. Bessiere, editor, Proceedings of the Twenty-Ninth International Joint Conference
     on Artificial Intelligence (IJCAI-20), pages 2633–2639. ijcai.org, 2020.
[62] J. Siems, L. Zimmer, A. Zela, J. Lukasik, M. Keuper, and F. Hutter. NAS-Bench-301 and
     the case for surrogate benchmarks for neural architecture search. arXiv:2008.09777 [cs.LG],
     2020.
[63] G. Kurtzer, V. Sochat, and M. Bauer. Singularity: Scientific containers for mobility of compute.
     PLOS ONE, 12(5), 2017.
[64] L. Barba. Terminologies for reproducible research. arXiv:1802.03311 [cs.DL], 2018.
[65] X. Bouthillier, C. Laurent, and P. Vincent. Unreproducible research is reproducible. In
     Chaudhuri and Salakhutdinov [97], pages 725–734.
[66] J. Forde, T. Head, C. Holdgraf, Y. Panda, G. Nalvarete, B. Ragan-Kelley, and E. Sundell.
     Reproducible research environments with repo2docker. In ICML workshop on Reproducible
     Machine Learning, 2018.
[67] T. Kluyver, B. Ragan-Kelley, F. Pérez, B. Granger, M. Bussonnier, J. Frederic, K. Kelley,
     J. Hamrick, J. Grout, S. Corlay, P. Ivanov, C. Avila, S. Abdalla, C. Willing, and the Jupyter
     development team. Jupyter notebooks - a publishing format for reproducible computational
     workflows. In F. Loizides and B. Scmidt, editors, Positioning and Power in Academic Publish-
     ing: Players, Agents and Agendas, pages 87–90. IOS Press, 2016.
[68] B. Callahan, D. Proctor, D. Relman, J. Fukuyama, and S. Holmes. Reproducible research
     workflow in R for the analysis of personalized human microbiome data. In Pacific Symposium
     on Biocomputing, volume 21, 2016.
[69] A. Klein and F. Hutter. Tabular benchmarks for joint architecture and hyperparameter opti-
     mization. arXiv:1905.04970 [cs.LG], 2019.
[70] X. Dong and Y. Yang. NAS-Bench-201: Extending the scope of reproducible neural archi-
     tecture search. In Proceedings of the International Conference on Learning Representations
     (ICLR’20) icl [99]. Published online: iclr.cc.
[71] A. Zela, J. Siems, and F. Hutter. NAS-Bench-1Shot1: Benchmarking and dissecting one-
     shot neural architecture search. In Proceedings of the International Conference on Learning
     Representations (ICLR’20) icl [99]. Published online: iclr.cc.
[72] F. Pedregosa, G. Varoquaux, A. Gramfort, V. Michel, B. Thirion, O. Grisel, M. Blondel,
     P. Prettenhofer, R. Weiss, V. Dubourg, J. Vanderplas, A. Passos, D. Cournapeau, M. Brucher,
     M. Perrot, and E. Duchesnay. Scikit-learn: Machine learning in Python. Journal of Machine
     Learning Research, 12:2825–2830, 2011.
[73] T. Chen and C. Guestrin. Xgboost: A scalable tree boosting system. In B. Krishnapuram,
     M. Shah, A. Smola, C. Aggarwal, D. Shen, and R. Rastogi, editors, Proceedings of the 22nd
     ACM SIGKDD International Conference on Knowledge Discovery and Data Mining (KDD),
     pages 785–794. ACM Press, 2016.
[74] P. Gijsbers, E. LeDell, S. Poirier, J. Thomas, B. Bischl, and J. Vanschoren. An open source
     automl benchmark. In K. Eggensperger, M. Feurer, F. Hutter, and J. Vanschoren, editors, ICML
     workshop on Automated Machine Learning (AutoML workshop 2019), 2019.
[75] J. Vanschoren, J. van Rijn, B. Bischl, and L. Torgo. OpenML: Networked science in machine
     learning. SIGKDD Explor. Newsl., 15(2):49–60, 2014.
[76] D. Jones, M. Schonlau, and W. Welch. Efficient global optimization of expensive black box
     functions. Journal of Global Optimization, 13:455–492, 1998.

                                                15

[77] M. Lindauer, M. Feurer, K. Eggensperger, A. Biedenkapp, and F. Hutter. Towards assessing the
     impact of Bayesian optimization’s own hyperparameters. In P. De Causmaecker, M. Lombardi,
     and Y. Zhang, editors, IJCAI 2019 DSO Workshop, 2019.
[78] M. Lindauer, K. Eggensperger, M. Feurer, A. Biedenkapp, D. Deng, C. Benjamins, T. Ruhkopf,
     R. Sass, and F. Hutter. SMAC3: A versatile Bayesian optimization package for hyperparameter
     optimization. Journal of Machine Learning Research (JMLR) – MLOSS, 23(54):1–9, 2022.
[79] F. Hutter, H. Hoos, and K. Leyton-Brown. Sequential model-based optimization for general
     algorithm configuration. In C. Coello, editor, Proceedings of the Fifth International Confer-
     ence on Learning and Intelligent Optimization (LION’11), volume 6683 of Lecture Notes in
     Computer Science, pages 507–523. Springer, 2011.
[80] A. Cowen-Rivers, W. Lyu, R. Tutunov, Z. Wang, A. Grosnit, R. Griffiths, A. Maraval, H. Jianye,
     J. Wang, J. Peters, and H. Ammar. HEBO: Pushing the limits of sample-efficient hyper-
     parameter optimisation. Journal of Artificial Intelligence Research, 2022.
[81] K. Kandasamy, K. Vysyaraju, W. Neiswanger, B. Paria, C. Collins, J. Schneider, B. Poczos,
     and E. Xing. Tuning hyperparameters without grad students: Scalable and robust Bayesian
     optimisation with Dragonfly. Journal of Machine Learning Research, 21(81):1–27, 2020.
[82] T. Akiba, S. Sano, T. Yanase, T. Ohta, and M. Koyama. Optuna: A next-generation hyper-
     parameter optimization framework. In A. Teredesai, V. Kumar, Y. Li, R. Rosales, E. Terzi,
     and G. Karypis, editors, Proceedings of the 25th ACM SIGKDD International Conference on
     Knowledge Discovery & Data Mining, KDD’19, pages 2623–2631. ACM Press, 2019.
[83] J. Bergstra and Y. Bengio. Random search for hyper-parameter optimization. Journal of
     Machine Learning Research, 13:281–305, 2012.
[84] D. Eriksson, M. Pearce, J. Gardner, R. Turner, and M. Poloczek. Scalable global optimization
     via local bayesian optimization. In Wallach et al. [98].
[85] M. Balandat, B. Karrer, D. Jiang, S. Daulton, B. Letham, A. Wilson, and E. Bakshy. BoTorch:
     A Framework for Efficient Monte-Carlo Bayesian Optimization. In H. Larochelle, M. Ranzato,
     R. Hadsell, M.-F. Balcan, and H. Lin, editors, Proceedings of the 33rd International Conference
     on Advances in Neural Information Processing Systems (NeurIPS’20). Curran Associates,
     2020.
[86] F. Hutter and M. Osborne. A kernel for hierarchical parameter spaces. arXiv:1310.5738v1
     [stats.ML], 2013.
[87] J. Demšar. Statistical comparisons of classifiers over multiple data sets. Journal of Machine
     Learning Research, 7:1–30, 2006.
[88] L. Hertel, J. Collado, P. Sadowski, J. Ott, and P. Baldi. Sherpa: Robust hyperparameter
     optimization for machine learning. SoftwareX, 12:100591, 2020.
[89] Y. Xiao, E. Xing, and W. Neiswanger. Amortized auto-tuning: Cost-efficient transfer optimiza-
     tion for hyperparameter recommendation. arXiv:2106.09179 [cs.LG], 2021.

[90] K. Šehić, A. Gramfort, J. Salmon, and L. Nardi. LassoBench: A high-dimensional hyperpa-
     rameter optimization benchmark suite for lasso. arXiv:2111.02790 [cs.LG], 2021.
[91] F. Pfisterer, L. Schneider, J. Moosbauer, M. Binder, and B. Bischl. YAHPO Gym – design
     criteria and a new multifidelity benchmark for hyperparameter optimization. arXiv:2109.03670
     [cs.LG], 2021.
[92] L. Cardoso, V. Santos, R. Francês, R. Prudêncio, and R. Alves. Data vs classifiers, who wins?
     arXiv:2107.07451 [cs.LG], 2021.
[93] F. Hutter, L. Kotthoff, and J. Vanschoren, editors. Automated Machine Learning: Methods,
     Systems, Challenges, volume 5 of The Springer Series on Challenges in Machine Learning.
     Springer, 2019. Available for free at http://automl.org/book.

                                               16

 [94] S. Dasgupta and D. McAllester, editors. Proceedings of the 30th International Conference on
      Machine Learning (ICML’13), 2013. Omnipress.
 [95] B. Bonet and S. Koenig, editors. Proceedings of the Twenty-ninth National Conference on
      Artificial Intelligence (AAAI’15), 2015. AAAI Press.
 [96] J. Vanschoren, S. Yeung, and M. Xenochristou, editors. Proceedings of the Neural Information
      Processing Systems Track on Datasets and Benchmarks, 2021.
 [97] K. Chaudhuri and R. Salakhutdinov, editors. Proceedings of the 36th International Confer-
      ence on Machine Learning (ICML’19), volume 97, 2019. Proceedings of Machine Learning
      Research.
 [98] H. Wallach, H. Larochelle, A. Beygelzimer, F. d’Alché-Buc, E. Fox, and R. Garnett, edi-
      tors. Proceedings of the 32nd International Conference on Advances in Neural Information
      Processing Systems (NeurIPS’19), 2019. Curran Associates.
 [99] Proceedings of the International Conference on Learning Representations (ICLR’20), 2020.
      Published online: iclr.cc.
[100] T. Gebru, J. Morgenstern, B. Vecchione, J. Vaughan, H. Wallach, H. Daumé III, and K. Craw-
      ford. Datasheets for datasets. arXiv:1803.09010 [cs.DB], 2020.
[101] G. Brockman, V. Cheung, L. Pettersson, J. Schneider, J. Schulman, J. Tang, and W. Zaremba.
      OpenAI Gym. arXiv:1606.01540 [cs.LG], 2016.
[102] B. Bischl, G. Casalicchio, M. Feurer, F. Hutter, M. Lang, R. Mantovani, J. van Rijn, and
      J. Vanschoren. Openml benchmarking suites. In Vanschoren et al. [96].
[103] D. Molina, A. Latorre, and F. Herrera. An insight into bio-inspired and evolutionary algorithms
      for global optimization: Review, analysis, and lessons learnt over a decade of competitions.
      Cognitive Computation, 10:517–544, 2018.
[104] A. Kuhnle, M. Schaarschmidt, and K. Fricke. Tensorforce: a TensorFlow library for applied
      reinforcement learning. Web page, 2017. URL https://github.com/tensorforce/
      tensorforce.
[105] J. Schulman, F. Wolski, P. Dhariwal, A. Radford, and O. Klimov. Proximal policy optimization
      algorithms. arXiv:1707.06347 [cs.LG], 2017.
[106] T. Chen, E. Fox, and C. Guestrin. Stochastic gradient Hamiltonian Monte Carlo. In E. Xing
      and T. Jebara, editors, Proceedings of the 31th International Conference on Machine Learning,
      (ICML’14). Omnipress, 2014.
[107] J. Springenberg, A. Klein, S. Falkner, and F. Hutter. Bayesian optimization with robust
      Bayesian neural networks. In D. Lee, M. Sugiyama, U. von Luxburg, I. Guyon, and R. Garnett,
      editors, Proceedings of the 29th International Conference on Advances in Neural Information
      Processing Systems (NeurIPS’16). Curran Associates, 2016.
[108] D. Dua and C. Graff. UCI machine learning repository, 2019. URL http://archive.ics.
      uci.edu/ml.
[109] S. Dieleman, J. Schlüter, C. Raffel, E. Olson, S. Sønderby, D. Nouri, D. Maturana, M. Thoma,
      E. Battenberg, J. Kelly, J. De Fauw, M. Heilman, diogo149, B. McFee, H. Weideman,
      takacsg84, peterderivaz, Jon, instagibbs, K. Rasul, CongLiu, Britefury, and J. Degrave.
      Lasagne: First release., 2015.
[110] Theano Development Team. Theano: A Python framework for fast computation of mathemati-
      cal expressions. arXiv:1605.02688 [cs.SC], 2016.
[111] F. Hutter. Automated Configuration of Algorithms for Solving Hard Computational Problems.
      PhD thesis, University of British Columbia, Department of Computer Science, Vancouver,
      Canada, 2009.

                                                 17

A    Maintenance
In this section we present a maintenance plan that is adapted from the datasheets for datasets [100].
      • Who is maintaining the benchmarking library? HPOBench is developed and maintained
        by the Machine Learning Lab at the University of Freiburg.
      • How can the maintainer of the dataset be contacted(e.g., email address)? Questions
        should be submitted via an issue on the Github repository at https://github.com/
        automl/HPOBench.
      • Is there an erratum? No.
      • Will the benchmarking library be updated? We consider adding new benchmarking
        problems and potentially fix existing issues with existing benchmarks. Such changes will be
        communicated via release notes in Github releases.
      • Will older versions of the benchmarking library continue to be sup-
        ported/hosted/maintained? Older versions of the benchmarking code are available via
        the underlying git repository. Containers are versioned and available via Gitlab. We aim
        to answer questions on a best-effort basis, but will not do so for older versions of the
        benchmarking library.
      • If others want to extend/augment/build on/contribute to the dataset, is there a mech-
        anism for them to do so? We allow contributions from the community via a pro-
        cess that is currently described at https://github.com/automl/HPOBench/wiki/
        How-to-add-a-new-benchmark-step-by-step.
      • Any other comments? No.

B    Benchmarking efforts
In addition to Section 3 of the main paper, we provide here a non-exhaustive list of further benchmarking libraries in the area of HPO consisting of not only a publication but which constitute or
constituted a long-running effort to compare methods:
      • HPOLib [6] to benchmark global optimization methods
      • ACLib [47] to benchmark algorithm configuration methods
      • OpenAI Gym [101] to benchmark RL methods
      • COCO [9] to compare continuous optimization methods
      • Bayesmark [8] to benchmark Bayesian optimization methods
      • OpenML benchmarking suites [102] provide a set of datasets for supervised classification
      • Olympus [12] provides a set of experiment planning tasks to evaluate optimization algorithms
      • HPO-B [48] provides a tabular benchmark to compare black-box HPO methods
      • ExpoBench [11] provides expensive benchmark problems for HPO

C    Benchmarking competitions
In addition to Section 3 of the main paper, we provide here a non-exhaustive list of benchmarking
competitions on HPO and related topics:
      • The AutoML challenges [49]
      • The AutoDL challenge [50]
      • NeurIPS 2020 Black-Box optimization challenge [51]
      • The KDD cup (see https://www.kdd.org/kdd-cup)
      • Challenges in Machine Learning (CIML) workshop series (see https://ciml.chalearn.
        org/)
      • Black-box Optimization Benchmarking (BBOB) workshop series [103] (see https://
        numbbo.github.io/workshops/)

                                                 18

D     More Details on Considered Benchmarks

In addition to the main paper, here we provide further details on our benchmarks collected. We start
with issues we faced during collection and then briefly describe the existing community benchmarks
(Section D.2) and the new benchmarks (Section D.3).

D.1   Conflicting Dependencies.

During benchmark collection, we also encountered a few examples of conflicting dependencies and
updated interfaces making long-term maintenance of non-containerized benchmarks hard: Net [22]
was built with the latest version of scikit-learn [72] (0.18) when it was developed but is incompatible
with the current version (0.24); the Cartpole benchmark does not run with the latest version of
TensorForce [104] due to a change in the API; NB201 [70] changed its interface as well as the
underlying data from its initial release. Additionally, in total, none of the existing community
benchmarks we collected for this paper had a full list of dependencies given.

D.2   Existing Community Benchmarks

Here, we provide more details on the existing community benchmarks currently in HPOBench and
list their hyperparameter and fidelity spaces in Table 4.
Cartpole [22] A highly stochastic benchmark having 7 hyperparameters of the proximal policy
optimization [105] algorithm implemented in TensorForce [104] for the cartpole swing-up task
implemented in the OpenAI Gym [101]. The number of repetitions is used as the fidelity and this
benchmark is available only as a raw benchmark.
BNN [22] The Bayesian neural network benchmark is a 4-hyperparameter tuning task to minimize
the negative log-likelihood of a Bayesian neural network trained with stochastic gradient Hamilton
Monte-Carlo [106] with scale adaption [107] on two different regression datasets from the UCI
repository ([108], Protein Structure and YearPredictionMSD). It is implemented with Lasagne [109]
and Theano [110]. It uses the number of MCMC sampling steps and is available only as a raw
benchmark.
Net [22] This benchmark has 6 architectural and training hyperparameters to train a feed-forward
neural network on six different datasets from OpenML [75]: Adult, Higgs, Letter, MNIST, Optdigits
and Poker. As fidelity it uses the number of training epochs for the neural networks. This is a surrogate
benchmark and uses a random forest, which is trained on 10K randomly samples configurations.
NBHPO. [69] This benchmark is a joint neural architecture search and HPO for a 2-layer feedforward
neural network. The output layer was designed as a linear layer with parameterized architecture
details and training parameters while the search space is a large grid of configurations on four popular
UCI datasets for regression: protein structure, slice localization, naval propulsion and parkinsons
telemonitoring.
NB101. [54] This was the first introduced NAS benchmark based on tabular lookup, designed for
reproducibility in NAS research. Each architecture is represented as a stack of architectural cells,
where each such cell is represented as directed acyclic graphs (DAGs). The benchmarks offers a
search space that includes nearly 423k unique architectures by parameterizing the nodes and edges of
the DAGs. The lookup table allows to query performance of architectures on the Cifar-10 dataset.
Additionally, queries can be made for intermediate training epochs too, thereby allowing multi-fidelity
optimization. In contrast to the original implementation, we always return the average across the
three repetitions as a score.
NB1Shot1. [71] The NAS-Bench-1shot1 was derived from the large architecture space of NAS-
Bench-101, such that, weight-sharing based one-shot NAS methods can be applied for this tabular
lookup. The cell-level encoding was modified to yield 3 variants of the architecture space which
contains around 6k (search space 1), 29k (search space 2), 300k (search space 3) architectures. In
contrast to the original implementation we always return the average across the three repetitions as a
score.
NB201. [70] To further aid the use of weight sharing algorithms to NAS Benchmarks, this benchmark
introduced a fixed cell search space wherein a DAG has only 4 nodes that define the cell architecture.

                                                   19

Table 4: Hyperparameter spaces of our benchmarks. For each benchmark, we report the hyperparameter names, type, whether they are on a log scale, and their respective range for each benchmark.
Additionally, we report the same information for the fidelity space. If the spaces are different for
different benchmarks within one family, we report them separately.
benchmark              name                             type    log                                     range
                       batch_size                       int     3                                     [8, 256]
                       discount                         float   7                                   [0.0, 1.0]
                       entropy_regularization           float   7                                   [0.0, 1.0]
Cartpole               learning_rate                    float   3                                [1e−07 , 0.1]
                       likelihood_ratio_clipping        float   7                                 [1e−7 , 1.0]
                       n_units_{1,2}*                   int     3                                     [8, 128]
                       repetitions                      int     7                                       [1, 9]
                       burn_in                          float   7                                   [0.0, 0.8]
                       l_rate                           float   3                                 [1e−6 , 0.1]
BNN                    mdecay                           float   7                                   [0.0, 1.0]
                       n_units_{1,2}*                   int     3                                   [16, 512]
                       epochs                           int     7                               [500, 10000]
                       average_units_per_layer_log2     float   7                                   [4.0, 8.0]
                       batch_size_log2                  float   7                                   [3.0, 8.0]
                       dropout                          float   7                                   [0.0, 0.5]
Net
                       final_lr_fraction_log2           float   7                                 [−4.0, 0.0]
                       initial_lr_log10                 float   7                               [−6.0, −2.0]
                       num_layers                       int     7                                       [1, 5]
 adult, higgs, mnist                                    int     7                                    [9, 243]
 letter                                                 int     7                                      [3, 81]
                       epochs
 optdigits                                              int     7                                      [1, 27]
 poker                                                  int     7                                  [81, 2187]
                       activation_fn_{1, 2}*            cat     -                                {tanh, relu}
                       batch_size                       ord     -                             {8, 16, 32, 64}
                       dropout_{1, 2}*                  ord     -                              {0.0, 0.3, 0.6}
NBHPO                  init_lr                          ord     -     {0.0005, 0.001, 0.005, 0.01, 0.05, 0.1}
                       lr_schedule                      cat     -                            {cosine, const}
                       n_units_{1, 2}*                  ord     -                {16, 32, 64, 128, 256, 512}
                       epochs                           int     7                                    [3, 100]
                       1<-0                             cat     -                     {none, skip_connect,
                       2<-{0,1}∗                        cat     -             nor_conv_1x1, nor_conv_3x3,
NB201
                       3<-{0,1,2}∗                      cat     -                          avg_pool_3x3}
                       epochs                           int     7                                   [12, 200]
                       edge_{0, 1, ..., 20}*            cat     -                                  {0, 1}
                       op_node_{0, 1, .., 4}*           cat     -       {conv1x1-bn-relu, conv3x3-bn-relu,
NB101Cf 10A
                                                                                             maxpool3x3}
                       epochs                           ord     7                           {[4, 12, 36, 108}
                       edge_{0, 1, ..., 8}*             cat     -                          {0, 1, 2, ..., 20}
NB101Cf 10B            op_node_{0, 1, ..., 4}*          cat     -       {conv1x1-bn-relu, conv3x3-bn-relu,
                                                                                             maxpool3x3}
                       epochs                           ord     7                            {4, 12, 36, 108}
                       edge_{0, 1, ..., 20}*            float   7                                [0.0, 1.0]
                       num_edges                        int     7                                    [0, 9]
NB101Cf 10C
                       op_node_{0, 1, ..., 4}*          cat     -       {conv1x1-bn-relu, conv3x3-bn-relu,
                                                                                             maxpool3x3}
                       epochs                           ord     7                            {4, 12, 36, 108}

                                                   20

                                     Table 5: Table 4 continued
               choice_block_{1,2,3,4}_op*      cat        -              {conv1x1-bn-relu, conv3x3-bn-relu,
                                                                                                  maxpool3x3}
               choice_block_1_parents          cat        -                                                 {(0,)}
               choice_block_2_parents          cat        -                                               {(0,1)}
 NB1Shot11
               choice_block_3_parents          cat        -                                 {(0,1), (0,2), (1,2)}
               choice_block_4_parents          cat        -             {(0,1), (0,2), (0,3), (1,2), (1,3), (2,3)}
               choice_block_5_parents          cat        -             {(0,1), (0,2), (0,3), (0,4), (1,2), (1,3),
                                                                                (2,3), (1,4), (2,3), (2,4), (3,4)}
               epochs                          ord        7                                      {4, 12, 36, 108}
               choice_block_{1,2,3,4}_op*      cat        -               {conv1x1-bn-relu, conv3x3-bn-relu,
                                                                                                     maxpool3x3}
               choice_block_1_parents          cat        -                                                    {(0,)}
               choice_block_2_parents          cat        -                                              {(0,), (1,)}
 NB1Shot12
               choice_block_3_parents          cat        -                                 {(0, 1), (0, 2), (1, 2)}
               choice_block_4_parents          cat        -         {(0, 1), (0, 2), (0, 3), (1, 2), (1, 3), (2, 3)}
               choice_block_5_parents          cat        -   {(0, 1, 2), (0, 1, 3), (0, 1, 4), (0, 2, 3), (0, 2, 4),
                                                               (0, 3, 4), (1, 2, 3), (1, 2, 4), (1, 3, 4), (2, 3, 4)}
               epochs                          ord        7                                      {4, 12, 36, 108}
               choice_block_{1,2,3,4,5}_op*    cat        -               {conv1x1-bn-relu, conv3x3-bn-relu,
                                                                                                    maxpool3x3}
               choice_block_1_parents          cat        -                                                      (0,)
               choice_block_2_parents          cat        -                                             {(0,), (1,)}
               choice_block_3_parents          cat        -                                       {(0,), (1,), (2,)}
               choice_block_4_parents          cat        -         {(0, 1), (0, 2), (0, 3), (1, 2), (1, 3), (2, 3)}
 NB1Shot13
               choice_block_5_parents          cat        -         {(0, 1), (0, 2), (0, 3), (0, 4), (1, 2), (1, 3),
                                                                                     (1, 4), (2, 3), (2, 4), (3, 4)}
               choice_block_6_parents          cat        -         {(0, 1), (0, 2), (0, 3), (0, 4), (0, 5), (1, 2),
                                                                      (1, 3), (1, 4), (1, 5), (2, 3), (2, 4), (2, 5),
                                                                                             (3, 4), (3, 5), (4, 5)}
               epochs                          ord        7                                      {4, 12, 36, 108}

Whereas the edges define the operations. Thus, creating a search space of around 15k unique
architectures. NAS-Bench-201 provides a lookup table for Cifar-10, Cifar-100, and ImageNet16-120.
In contrast to the original implementation we always return the average across the three repetitions as
a score.

D.3   New Benchmarks

Here, we provide more details on the new benchmarks and list their hyperparameter and fidelity
spaces in Table 6.
SVM A 2-dimensional benchmark for a SVM model with an RBF kernel with the regularization
and the kernel coefficient gamma as available hyperparameters to tune. It uses the dataset subset
fraction as the fidelity and is available as both raw and tabular benchmarks. For the tabular version,
we discretized each hyperparameter into 21 bins for 441 unique hyperparameter configurations and
evaluated each of these on 20 datasets from the AutoML benchmark [74].
LogReg This benchmark has 2 hyperparameters – learning rate and regularization for a logistic
regression model trained using Stochastic Gradient Descent (SGD). It uses dataset fraction and/or
the number of SGD iterations as the fidelity and is available as both a raw and tabular benchmark.
For the tabular version we evaluated a grid of 625 configurations on 20 datasets from the AutoML
benchmark [74].
XGBoost This benchmark has 4 hyperparameters that tune the maximum depth per tree, the features
subsampled per tree, the learning rate and the L2 regularization for the XGBoost model. It uses
dataset fraction and/or the number of boosting iterations as fidelities and is available as both a raw
and tabular benchmark. For the tabular version we discretized each hyperparameter into 10 bins and
evaluated the resulting grid of 10k configurations on 20 datasets from the AutoML benchmark [74].

                                                     21

RandomForest This benchmark has 4 hyperparameters that tune the maximum depth per tree, the
maximum features subsampled per split, the minimum number of samples required for splitting a
node, and the minimum number of samples required in each leaf node for a random forest model. It
uses dataset fraction and/or the number of trees as fidelities and is available as both a raw and tabular
benchmark. For the tabular version we discretized each hyperparameter into 10 bins and evaluated
the resulting grid of 10k configurations on 20 datasets from the AutoML benchmark [74].
MLP This benchmark has 5 hyperparameters – two hyperparameters that determine the depth and
width of the network; three more hyperparameters tune the batch size, L2 regularization and the
initial learning rate for Adam. It uses dataset fraction and/or the number of epochs as fidelities
and is available as both a raw and tabular benchmark. For the tabular version, we discretized
each hyperparameter into 10 bins and evaluated the resulting grid of 1k configurations for each of
30 different architectures, resulting in 30k configurations in total, on 8 datasets from the AutoML
benchmark [74].
To collect the data for the tabular benchmark, we evaluated every configuration-fidelity pair in the
discretized space on 5 different seeds; each such repetition is evaluated on the following 4 metrics:
accuracy, balanced accuracy, precision, f1.

Table 6: Table detailing the configuration spaces for the new         Table 7: OpenML Task IDs used
benchmarks included in HPOBench. For each model, we report            from the AutoML benchmark for
the hyperparameters and their ranges (top part) and fidelities        SVM, LogReg, XGBoost and Ranand their ranges (bottom part).                                       domForest. MLP uses only the
                                                                      first 8 task IDs. The table shows
     benchmark     name                 type log          range       the total number of instances
                   C                    float 3     [2−10
                                                           , 210 ]    available (train + test) (#obs), and
     SVM           gamma                float 3     [2 −10
                                                           , 210 ]    the total number of features prior
                                                                      to preprocessing (#feat).
                   subsample            float 7       [0.1, 1.0]
                   alpha                float 3 [1e − 05, 1.0]         name              tid #obs #feat
                   eta0                 float 3 [1e − 05, 1.0]         blood-transf.. 10101    748     4
     LogReg
                   iter                 int 7        [10, 1000]        vehicle           53    846    18
                   subsample            float 7       [0.1, 1.0]       Australian    146818    690    14
                                                                       car           146821   1728     6
                   colsample_bytree     float 7       [0.1, 1.0]       phoneme         9952   5404     5
                   eta                  float 3     [2−10 , 1.0]       segment       146822   2310    19
     XGBoost       max_depth            int 3            [1, 50]       credit-g          31   1000    20
                   reg_lambda           float 3     [2−10 , 210 ]      kc1             3917   2109    22
                   n_estimators         int 7        [50, 2000]        sylvine       168912 5124      20
                   subsample            float 7       [0.1, 1.0]       kr-vs-kp           3 3196      36
                  max_depth         int 3                [1, 50]       jungle_che.. 167119 44819       6
                  max_features      float 7           [0.0, 1.0]       mfeat-factors     12 2000     216
                  min_samples_leaf int 7                   [1, 2]      shuttle       146212 58000      9
     RandomForest                                                      jasmine       168911 2984     145
                  min_samples_split int 3               [2, 128]
                                                                       cnae-9          9981 1080     856
                   n_estimators         int 7         [16, 512]        numerai28.6 167120 96320       21
                   subsample            float 7       [0.1, 1.0]       bank-mark.. 14965 45211        16
                   alpha                float 3 [1.0e−08 , 1.0]        higgs         146606 98050     28
                   batch_size           int 3         [4, 256]         adult           7592 48842     14
                   depth                int 7             [1, 3]       nomao           9977 34465    118
     MLP           learning_rate_init   float 3 [1.0e−05 , 1.0]
                   width                int 3       [16, 1024]
                   epochs               int 7           [3, 243]
                   subsample            float 7          [0.1, 1]

E    Details on Hardware Used for Experiments

For our benchmark study we ran all jobs on a compute cluster equipped with Intel(R) Xeon(R) Gold
6242 CPU @ 2.80GHz. If not stated otherwise, we run all job on 1 CPU with up to 6GB RAM for at

                                                     22

most 4 days or till the benchmark budget was exhausted. For runs that needed more memory to load
data, we allowed up to 12GB RAM (NB101, NB1Shot1, NB201). For collecting tabular data for the
new benchmarks, we ran all jobs on a compute cluster equipped with Intel(R) Broadwell E5-2630v4
@ 2.2GHz with up to 6GB RAM.

F     Details on Runtime

Running all optimizers on the raw versions of the existing community benchmarks would take more
than 1500 CPU years, but the use of tabular and surrogate-based benchmarks in HPOBench reduces
this amount to only 22.5 CPU years. While this is still a lot, we emphasize that most of this time is
used by the optimizers (and not the benchmarks). For developing and evaluating a new multi-fidelity
method and comparing it to computationally cheap baselines, e.g. sequentially evaluating both RS
and DE on all tabular and surrogate benchmarks took < 10 CPU days, HB took around 50 CPU days
and DEHB needed around 40 CPU days. To further explain the amount of time it took to obtain
results for our empirical study, we look at statistics of our runs. In Table 8, we report the average
runtime (in hours, maximum 96, however, we only record the last call to our objective function, so a
runtime of, e.g. 95 could also mean that the optimizer did not call the objective function for 2 hours
and was then forcefully terminated) and the number of calls/100 to the objective function for one
exemplary benchmark per family. The last two rows show the total time spent on obtaining results for
all raw benchmarks and surrogate plus tabular benchmarks per optimizers. Additionally, we give the
overall amount of compute spent on our empirical study.
Looking at the first part of the table, we favourably see, that most optimizers on average took less
than two hours to spend the simulated optimization budget. However, there are some exceptions
like BOGP and DF mostly hitting the optimization budget of 4 days resulting in fewer calls to the
objective function and worse performance.
Additionally, these statistics also allow to study some failure cases of the optimizers. For DF on
NB1Shot1, it only evaluated 90 configurations while taking less than 1 hour. Here DF stopped
right after the initial design, because it could not construct a model, the same happened for the
BNN benchmarks and thus the total runtime for the raw benchmarks is substantially lower. Finally,
RS called NB101Cf 10A three times more often than other black-box optimizers, because the table
underlying this benchmark does not cover the complete hyperparameter space and thus returns a loss
of 1 and costs of 0 for configurations not in the table. More advanced search algorithms avoid these
seemingly badly performing regions and thus sample more costly evaluations.

Table 8: We report the median wallclock time (in hours) and number of calls/100 to the objective
function for all optimizers and one benchmark per benchmark family.
                    NetAdult       NBHPOSlice       NB101Cf 10A       NB201Cf 100        NB1Shot11   total time
    optimizer   t       #c     t        #c      t        #c       t        #c        t        #c   raw     tab+sur
    RS           0      25      0       47       0       118       0        9        0        23    2295      102
    DE           0      25      0       29       0        31       0        7        0        13    2290       48
    BOKDE        0      25      1       31       0        31       0        8        0        27    2297      716
    BOGP        82      13     96       12      96        7        9        7       96        12    2296    46566
    BORF         2      25      2       43       2        39       0        8        1        21    2299     7326
    HEBO        84      13     96       12      96       11       26        7       96        12    2300    48735
    HB         0        108 3           209      1       242       0        21       0         95   2300     1299
    BOHB       0        108 2           130      0       104       0        20       1        110   2298     1508
    DEHB       0        108 0           126      0       118       0        17       0         63   2255     1031
    SMAC-HB 8           105 8           202      8       166       0        20       2         96   2298    12370
    DF        93        10 92            9      90        6       94        10       0         1    532     41867
    Optunahb
          tpe 0         109 0           109      0        75       0        31       0         55   2297     1606
    Optunamd
          tpe 4         287 0           111      0        81       0        15       0         78   2278     5694
                                                                                sum in CPU years     3.2    19.3

                                                          23

G     More Details on Considered Optimizers

Here, we provide additional details on the optimizers used in this work. We provide an overview in
Table 9 and then briefly explain our baselines, black-box and multi-fidelity optimizers in detail. We
note that we used the default settings for all tools and implementations.

Table 9: Overview of HPO optimizers considered in this study. For each optimizer we list the model
type, what types of hyperparameters and fidelities it can handle (the tool can either handle it natively
(3), not handle it (7) or we could transform the type ((3))), a link to the codebase and references.
                                  types           fidelities
      name         model                                          link      reference     version
                           cont     cat   log   disc. cont.
      RS             -      3      3      3      7         7       -           [83]
      BOGP          GP      3      3      3      7         7    SMAC3        [79, 78]       1.0.1
      BORF          RF      3      3      3      7         7    SMAC3        [79, 78]       1.0.1
      BOKDE        KDE      3      3      3      7         7   HpBandSter      [22]         0.7.4
      DE             -      3      3      3      7         7     DEHB          [24]     git commit
      HEBO          GP      3      3      3      7         7     HEBO          [80]         0.1.0
      HB             -      3      3       3    (3)        3   HpBandSter      [19]         0.7.4
      BOHB         KDE      3      3       3    (3)        3   HpBandSter      [22]         0.7.4
      DEHB           -      3      3       3    (3)        3      DEHB          [5]     git commit
      SMAC-HB       RF      3      3       3    (3)        3     SMAC3       [79, 78]       1.0.1
      DF            GP      3      3      (3)    3         3    Dragonfly      [81]        0.1.5
      Optunamd
            tpe    TPE      3      3       3     3         7     Optuna        [82]         2.8.0
      Optunahb
            tpe    TPE      3      3       3     3         7     Optuna        [82]         2.8.0

G.1    Baselines

Random Search (RS) is a simple baseline that samples new configurations uniformly at random
from a prior. It was proposed as an improved baseline over grid search [83] as it can handle low
intrinsic dimensionality and is easier to run in parallel.
Hyperband (HB) [19] is a bandit algorithm for the pure-exploration, non-stochastic infinite-armed
bandit problem which we described in Section 2. We will use it as a random search baseline for
multi-fidelity optimization.

G.2    Black-box Optimizers

BOGP is an implementation of traditional Gaussian process-based BO with a Matérn kernel [52]
and a SOBOL sequence initial design [76]. For categorical hyperparameters it uses a Hamming
kernel [111] and is implemented in the SMAC toolbox [78], thus it is using local search for acquisition
function optimization [79]. Its hyperparameters were tuned for good average performance over 50
function evaluations using meta-optimization [77].
BORF is similar to BOGP but uses random forests as suggested in the original SMAC publication [79].
In contrast to the original hyperparameter setting of SMAC with random forests, this version uses
a SOBOL sequence initial design [76] and only 20% interleaved random samples instead of 50%.
These hyperparameter settings were found via meta-optimization [77] for good average performance
over 50 function evaluations.
BOKDE is a re-implementation of the TPE algorithm using multi-dimensional kernel density estimators as used by the BOHB algorithm [22]. Instead of modeling the objective function as p(y|x),
it models two densities, p(x|ygood ) and p(x|ybad ), and uses their ratio that is proportional to the
expected improvement acquisition function [14].
DE. We use the canonical DE with rand/1 as the mutation strategy and binomial crossover. We set
the mutation factor F and crossover rate CR to 0.5 each and the population size N P to 20 [24].
HEBO is a GP-based BO algorithm that uses input warping and output warping, an ensemble of
acquisition functions [80] and won the recent NeurIPS Blackbox Optimization challenge [51].

                                                      24

G.3   Multi-fidelity Optimizers

BOHB [22] combines BO and HB with the goal of both algorithms complementing each other. It
follows the regular HB scheme, but instead of sampling configurations at random it uses BO. For BO
it uses a KDE model as described above. To handle multiple fidelities it builds an independent model
per fidelity, but only if there is sufficient (number of hyperparameters + 1) training data available, to
then always use the model from the highest fidelity for which a model is available.
SMAC-HB [78] is a straight-forward re-implementation of the BOHB algorithm using the BORF
building blocks described in the previous section.
DEHB [5] is a new model-free successor of BOHB which uses the evolutionary optimization method
DE instead of BO. For each fidelity, DEHB maintains a subpopulation and runs a separate DE
evolution while the information about good configurations flows from subpopulations at lower
fidelities to those at higher fidelities through a modified mutation strategy. The mutation allows the
use of these good configurations from lower fidelities to be selected as parents to evolve the new
subpopulation at a higher fidelity. The hyperparameters of the DE-part of DEHB are set exactly as
for DE described above.
Dragonfly (DF) [81] is a BO algorithm which implements an improved version of the BOCA
algorithm [3], which uses Gaussian processes and the upper confidence bound acquisition function to
first decide a location to query before deciding the fidelity to query.
Optunamd tpe is implemented in the Optuna framework [82], which is a high level optimization framework that allows to combine sampling (to propose new configurations to evaluate) and pruning (to stop
configurations if they are not promising) strategies to construct optimization algorithms. Optunamd
                                                                                                  tpe
uses TPE as a sampling algorithm and the median stopping [25] rule as a pruning algorithm. It fits a
Gaussian Mixture Model on the best so far seen configurations. The pruner stops a configuration if
its best intermediate result is worse compared to the median of the other configurations on the same
fidelity level.
                         md
Optunahb
       tpe is like Optunatpe implemented in the Optuna framework [82] and uses TPE for sampling,
but HB as a pruning algorithm.

H     More Results
Here, we give more results on our large-scale empirical study. First, we report results for all optimizers
in Table 10, 11 for the existing community benchmarks, and in Tables 12- 21 for the new benchmarks.
Second, we report statistical tests for RQ1 and RQ2 similar to the ones in the main paper for the new
benchmarks in Tables 22 and 23. Third, we report average ranking-over-time for each benchmark
family in Figure 5, 6 and 7. Finally, we show performance-over-time plots for all existing community
benchmarks in Figure 8, 9 and 10.

                                                   25

Table 10: Final performance of each black-box optimizer (lower is better). We report median
performance (regret for tabular/surrogate benchmarks and function values for raw benchmarks) across
32 repetitions per existing community benchmark. We boldface the best result per row.
                benchmark    black-box optimizers
                            RS      DE          BOGP    BORF     BOKDE      HEBO
                Cartpole     786.444 851.72222 826.444 227.056 381.72222 191.833
                BNN P rotein 3.17763 3.05335   3.10514 3.09424 3.04537   3.08331
                BNN Y ear    4.07933 4.01501   3.97039 3.88006 3.86969   3.77791
                NetAdult     0.00258 0.00072   0.00141 0.00110 0.00068   0.00006
                NetHiggs     0.00390 0.00194   0.00250 0.00277 0.00256   0.00193
                NetLetter    0.00263 0.00000   0.00095 0.00055 0.00101   0.00038
                NetM N IST   0.00097 0.00014   0.00040 0.00019 0.00027   0.00015
                NetOptDig    0.00229 0.00048   0.00225 0.00137 0.00129   0.00121
                NetP oker    0.00099 0.00054   0.00051 0.00024 0.00035   0.00004
                NBHPON aval 0.00000 0.00000    0.00001 0.00000 0.00000   0.00000
                NBHPOP ark 0.00000 0.00000     0.00092 0.00000 0.00000   0.00000
                NBHPOP rot 0.00328 0.00000     0.00000 0.00000 0.00104   0.00000
                NBHPOSlice 0.00004 0.00000     0.00006 0.00000 0.00001   0.00000
                NB101Cf 10A 0.00638 0.00417    0.00638 0.00497 0.00638   0.00497
                NB101Cf 10B 0.00638 0.00497    0.00638 0.00454 0.00603   0.00497
                NB101Cf 10C 0.00638 0.00491    0.00638 0.00638 0.00604   0.00180
                NB201Cf 100 0.86667 0.00000    0.00000 0.00000 1.09833   0.00000
                NB201Cf 10V 0.16667 0.00000    0.00000 0.00000 0.18667   0.00000
                NB201IN et 0.86667 0.45556     0.00000 0.27222 1.21667   0.00000
                NB1Shot11    0.00033 0.00060   0.00000 0.00000 0.00087   0.00073
                NB1Shot12    0.00107 0.00000   0.00000 0.00000 0.00107   0.00160
                NB1Shot13    0.00249 0.00114   0.00177 0.00177 0.00307   0.00250

Table 11: Final performance of each multi-fidelity optimizer (lower is better). We report median
performance (regret for tabular/surrogate benchmarks and function values for raw benchmarks) across
32 repetitions per existing community benchmark. We boldface the best result per row.
    benchmark      multi-fidelity optimizers
                 HB           BOHB        DEHB    SMAC-HB DF        Optunamd         hb
                                                                           tpe Optunatpe
    Cartpole     724.88889 232.94444 593.83333 211.33333 1004.38889 702.33333 523.66667
    BNN P rotein 3.14047      3.03529     3.07514 3.06393 9.65112   3.03252    3.08817
    BNN Y ear    4.11971      3.92703     4.03676 3.88357 12.30007  3.91723    4.02678
    NetAdult     0.00232      0.00060     0.00062 0.00067 0.00298   0.00067    0.00059
    NetHiggs     0.00373      0.00232     0.00206 0.00278 0.00469   0.00212    0.00209
    NetLetter    0.00197      0.00140     0.00032 0.00075 0.00240   0.00147    0.00045
    NetM N IST   0.00075      0.00032     0.00018 0.00023 0.00117   0.00026    0.00018
    NetOptDig    0.00201      0.00153     0.00101 0.00161 0.00394   0.00153    0.00056
    NetP oker    0.00072      0.00018     0.00031 0.00008 0.00053   0.00020    0.00028
    NBHPON aval 0.00000       0.00000     0.00000 0.00000 0.00001   0.00001    0.00005
    NBHPOP ark 0.00000        0.00000     0.00000 0.00000 0.00246   0.00359    0.00149
    NBHPOP rot 0.00104        0.00414     0.00000 0.00000 0.00000   0.00423    0.00162
    NBHPOSlice 0.00001        0.00001     0.00000 0.00000 0.00008   0.00009    0.00004
    NB101Cf 10A 0.00638       0.00619     0.00482 0.00476 0.00921   0.00863    0.00638
    NB101Cf 10B 0.00638       0.00497     0.00497 0.00442 0.00775   0.00838    0.00608
    NB101Cf 10C 0.00638       0.00497     0.00486 0.00638 0.00773   0.00861    0.00638
    NB201Cf 100 0.76000       0.86333     0.00000 0.00000 0.00000   0.87333    9.99667
    NB201Cf 10V 0.06267       0.10200     0.00000 0.01933 0.00000   0.27267    4.66800
    NB201IN et 0.71111        0.63611     0.27222 0.27222 0.28889   0.57222    11.29444
    NB1Shot11    0.00007      0.00154     0.00000 0.00040 0.00387   0.00544    0.00224
    NB1Shot12    0.00100      0.00107     0.00000 0.00090 0.00569   0.00392    0.00140
    NB1Shot13    0.00210      0.00210     0.00154 0.00177 0.00651   0.00651    0.00224

                                                26

Table 12: Final performance of each black-box optimizers (lower is better). We report the median
normalized regret across 32 repetitions for each new benchmarks collected for SVM. We boldface the
best result per row.
          optimizer   RS          DE      BOGP        BORF    Rayhyp    BOKDE HEBO
          svm_10101 0.00e+00 0.00e+00 0.00e+00 0.00e+00 0.034     0.023    0.00e+00
          svm_53     0.003    0.003    0.003    0.003    0.006    0.004    0.013
          svm_146818 0.013    0.013    0.013    0.013    0.019    0.013    0.039
          svm_146821 0.001    0.00e+00 0.00e+00 0.00e+00 0.001    0.001    0.001
          svm_9952 0.019      0.003    0.00e+00 0.00e+00 0.005    0.019    0.00e+00
          svm_146822 0.004    0.003    0.00e+00 0.00e+00 0.004    0.004    0.005
          svm_31     0.015    0.015    0.015    0.015    0.015    0.015    0.176
          svm_3917 0.042      0.038    0.008    0.008    0.046    0.042    0.115
          svm_168912 7.60e-04 0.00e+00 0.00e+00 0.00e+00 0.00e+00 0.003    0.00e+00
          svm_3      0.001    0.00e+00 0.00e+00 9.08e-04 9.08e-04 0.001    9.08e-04
          svm_167119 0.001    2.64e-04 2.64e-04 2.64e-04 2.64e-04 4.40e-04 2.64e-04
          svm_12     3.20e-17 3.20e-17 3.20e-17 3.20e-17 3.20e-17 3.20e-17 3.20e-17
          svm_146212 2.72e-04 0.00e+00 0.00e+00 0.00e+00 0.00e+00 8.17e-05 0.00e+00
          svm_168911 0.012    0.012    0.004    0.004    0.012    0.015    0.013
          svm_9981 0.02       0.016    8.16e-04 8.16e-04 0.018    0.046    0.02
          svm_167120 0.846    0.843    0.843    0.843    0.843    0.846    0.843
          svm_14965 0.008     0.00e+00 0.00e+00 0.00e+00 0.00e+00 0.009    0.00e+00
          svm_146606 0.54     0.539    0.539    0.539    0.539    0.543    0.539
          svm_7592 0.41       0.41     0.41     0.41     0.41     0.41     0.41
          svm_9977 0.085      0.085    0.085    0.085    0.085    0.086    0.085

Table 13: Final performance of each multi-fidelity optimizers (lower is better). We report the median
normalized regret across 32 repetitions for each new benchmarks collected for SVM. We boldface the
best result per row.
   optimizer    HB         BOHB    DEHB      SMAC-HB DF           Rayasha
                                                                     hyp    Optunamd        hb
                                                                                  tpe Optunatpe

   svm_10101 0.00e+00 0.023     0.00e+00 0.023          0.172    0.103    0.069        -
   svm_53     0.003    0.004    0.003    0.003          0.079    0.006    0.003        -
   svm_146818 0.013    0.013    0.013    0.013          0.049    0.019    0.013        -
   svm_146821 0.001    0.001    0.00e+00 0.00e+00       0.088    0.001    0.00e+00     -
   svm_9952 0.005      0.019    0.00e+00 0.00e+00       0.029    0.00e+00 0.00e+00     -
   svm_146822 0.003    0.004    0.00e+00 0.00e+00       0.022    0.003    0.00e+00     -
   svm_31     0.015    0.015    0.015    0.015          0.338    0.015    0.015        -
   svm_3917 0.038      0.046    0.038    0.038          0.137    0.069    0.038        -
   svm_168912 0.00e+00 7.60e-04 0.00e+00 0.00e+00       0.00e+00 0.00e+00 0.00e+00     -
   svm_3      9.08e-04 0.002    4.54e-04 9.08e-04       0.009    0.00e+00 0.00e+00     -
   svm_167119 4.40e-04 4.40e-04 2.64e-04 2.64e-04       2.64e-04 2.64e-04 2.64e-04     -
   svm_12     3.20e-17 3.20e-17 3.20e-17 3.20e-17       3.89e-04 3.20e-17 3.20e-17     -
   svm_146212 8.17e-05 2.18e-04 0.00e+00 0.00e+00       0.00e+00 0.00e+00 0.00e+00     -
   svm_168911 0.014    0.015    0.004    0.012          0.017    0.012    0.004        -
   svm_9981 0.014      0.018    8.16e-04 8.16e-04       0.207    8.16e-04 8.16e-04     -
   svm_167120 0.843    0.846    0.843    0.843          0.843    0.843    0.843        -
   svm_14965 0.00e+00 0.008     0.005    0.00e+00       0.005    0.00e+00 0.008        -
   svm_146606 0.539    0.54     0.539    0.539          0.539    0.539    0.539        -
   svm_7592 0.41       0.41     0.41     0.41           0.41     0.41     0.41         -
   svm_9977 0.085      0.085    0.085    0.085          0.085    0.085    0.085        -

                                                 27

Table 14: Final performance of each black-box optimizers (lower is better). We report the median
normalized regret across 32 repetitions for each new benchmarks collected for LogReg. We boldface
the best result per row.
           optimizer RS          DE      BOGP      BORF      Rayhyp    BOKDE HEBO
           lr_10101 0.035     0.035    0.035    0.035    0.035    0.115         0.028
           lr_53     0.007    0.008    0.008    0.008    0.008    0.125         0.007
           lr_146818 0.012    0.012    0.009    0.01     0.015    0.095         0.012
           lr_146821 0.018    0.016    0.002    0.002    0.012    0.485         0.002
           lr_9952 0.059      0.061    0.059    0.055    0.062    0.123         0.054
           lr_146822 0.002    0.005    0.00e+00 0.00e+00 0.00e+00 0.07          0.00e+00
           lr_31     0.061    0.061    0.05     0.05     0.061    0.172         0.061
           lr_3917 0.021      0.014    0.011    0.014    0.016    0.074         0.014
           lr_168912 9.47e-04 9.47e-04 6.31e-04 3.16e-04 6.31e-04 0.033         0.00e+00
           lr_3      4.62e-04 4.57e-17 0.00e+00 0.00e+00 0.00e+00 0.127         0.00e+00
           lr_167119 0.004    0.001    9.84e-05 1.97e-04 2.95e-04 0.055         9.84e-05
           lr_12     4.11e-04 4.11e-04 0.00e+00 0.00e+00 2.54e-17 0.021         0.00e+00
           lr_146212 0.00e+00 0.00e+00 0.00e+00 0.00e+00 0.00e+00 0.132         0.00e+00
           lr_168911 0.00e+00 0.00e+00 0.00e+00 0.00e+00 0.00e+00 0.047         0.00e+00
           lr_9981 0.003      0.003    0.002    0.002    0.002    0.015         0.002
           lr_167120 0.00e+00 7.72e-04 0.00e+00 0.00e+00 0.00e+00 0.746         0.00e+00
           lr_14965 0.001     0.001    0.001    8.77e-04 0.001    0.051         7.31e-04
           lr_146606 0.001    0.001    0.001    9.20e-04 9.91e-04 0.255         8.02e-04
           lr_7592 4.93e-04 5.31e-04 1.33e-04 1.52e-04 1.52e-04 0.039           0.00e+00
           lr_9977 4.95e-04 4.95e-04 1.41e-04 7.07e-05 1.41e-04 0.096           0.00e+00

Table 15: Final performance of each multi-fidelity optimizers (lower is better). We report the median
normalized regret across 32 repetitions for each new benchmarks collected for LogReg. We boldface
the best result per row.
         optimizer HB     BOHB DEHB        SMAC-HB DF        Rayasha
                                                                hyp    Optunamd        hb
                                                                             tpe Optunatpe

         lr_10101 0.122 0.122     0.049    0.035      0.08 0.035     0.035        0.035
         lr_53     0.238 0.238    0.007    0.007      0.074 0.007    0.007        0.007
         lr_146818 0.107 0.107    0.006    0.01       0.073 0.015    0.012        0.012
         lr_146821 0.517 0.517    0.013    0.004      0.326 0.013    0.002        0.01
         lr_9952 0.097 0.097      0.054    0.054      0.118 0.056    0.055        0.055
         lr_146822 0.103 0.103    0.002    0.00e+00   0.031 0.003    0.00e+00     0.00e+00
         lr_31     0.217 0.217    0.047    0.056      0.139 0.05     0.044        0.044
         lr_3917 0.196 0.196      0.014    0.018      0.076 0.018    0.014        0.014
         lr_168912 0.071 0.071    3.16e-04 6.31e-04   0.014 9.47e-04 6.31e-04     7.89e-04
         lr_3      0.143 0.143    0.00e+00 0.00e+00   0.062 0.00e+00 0.00e+00     0.00e+00
         lr_167119 0.041 0.041    9.84e-05 2.95e-04   0.034 9.84e-05 1.48e-04     1.97e-04
         lr_12     0.035 0.035    0.00e+00 1.27e-17   0.016 8.22e-04 0.00e+00     2.06e-04
         lr_146212 0.171 0.171    0.00e+00 0.00e+00   0.111 0.00e+00 0.00e+00     0.00e+00
         lr_168911 0.083 0.083    0.005    0.00e+00   0.033 0.005    0.002        0.00e+00
         lr_9981 0.022 0.022      0.004    0.004      0.014 0.006    0.005        0.005
         lr_167120 0.513 0.513    0.005    0.00e+00   0.752 0.004    0.00e+00     0.00e+00
         lr_14965 0.072 0.072     0.001    0.001      0.023 0.001    0.001        0.001
         lr_146606 0.28 0.28      9.44e-04 9.20e-04   0.151 0.003    0.001        0.001
         lr_7592 0.07 0.07        5.69e-04 4.17e-04   0.02 7.21e-04 4.93e-04      4.55e-04
         lr_9977 0.092 0.092      3.54e-04 2.83e-04   0.047 7.43e-04 6.72e-04     3.54e-04

                                                 28

Table 16: Final performance of each black-box optimizers (lower is better). We report the median
normalized regret across 32 repetitions for each new benchmarks collected for XGBoost. We boldface
the best result per row.
                  optimizer    RS    DE     BOGP BORF Rayhyp BOKDE HEBO
                  xgb_10101 1.18 1.18 1.2            1.18    1.18      1.18    1.2
                  xgb_53     0.786 0.782 0.782       0.782   0.782     0.786   0.782
                  xgb_146818 0.778 0.778 0.778       0.778   0.778     0.778   0.778
                  xgb_146821 0.198 0.199 0.198       0.198   0.198     0.201   0.198
                  xgb_9952 0.614 0.614 0.614         0.614   0.614     0.614   0.613
                  xgb_146822 0.677 0.668 0.659       0.668   0.668     0.677   0.659
                  xgb_31     0.975 0.975 0.975       0.975   0.971     0.975   0.971
                  xgb_3917 1.07 1.07 1.05            1.05    1.05      1.06    1.05
                  xgb_168912 0.697 0.68 0.675        0.702   0.675     0.698   0.675
                  xgb_3      0.212 0.212 0.212       0.212   0.212     0.212   0.21
                  xgb_167119 0.406 0.405 0.402       0.402   0.402     0.404   0.402
                  xgb_12     1.68 1.63 1.63          1.63    1.63      1.67    1.63
                  xgb_146212 0.053 0.053 0.053       0.053   0.053     0.053   0.053
                  xgb_168911 0.964 0.957 0.964       0.964   0.956     0.964   0.964
                  xgb_9981 0.458 0.458 0.458         0.458   0.458     0.458   0.458
                  xgb_167120 1.05 1.05 1.05          1.05    1.05      1.05    1.05
                  xgb_14965 0.932 0.932 0.932        0.932   0.932     0.932   0.932
                  xgb_146606 0.881 0.881 0.881       0.88    0.88      0.881   0.881
                  xgb_7592 0.868 0.866 0.866         0.866   0.866     0.866   0.866
                  xgb_9977 0.501 0.5     0.494       0.494   0.494     0.497   0.494

Table 17: Final performance of each multi-fidelity optimizers (lower is better). We report the median
normalized regret across 32 repetitions for each new benchmarks collected for XGBoost. We boldface
the best result per row.
         optimizer   HB       BOHB DEHB SMAC-HB DF             Rayasha
                                                                  hyp  Optunamd        hb
                                                                             tpe Optunatpe

         xgb_10101 1.18 1.2         1.18    1.19         1.8   1.2        1.2          1.2
         xgb_53     0.786 0.786     0.79    0.782        0.872 0.828      0.817        0.786
         xgb_146818 0.778 0.778     0.778   0.778        0.861 0.778      0.778        0.778
         xgb_146821 0.203 0.203     0.203   0.198        0.254 0.208      0.208        0.203
         xgb_9952 0.614 0.614       0.614   0.614        0.864 0.614      0.615        0.614
         xgb_146822 0.673 0.677     0.659   0.659        0.765 0.664      0.677        0.659
         xgb_31     0.975 0.975     0.979   0.975        1.06 0.984       0.984        0.975
         xgb_3917 1.07 1.07         1.05    1.05         1.29 1.09        1.09         1.07
         xgb_168912 0.689 0.702     0.68    0.68         0.819 0.675      0.675        0.675
         xgb_3      0.212 0.212     0.212   0.212        0.259 0.212      0.212        0.212
         xgb_167119 0.402 0.403     0.402   0.402        0.54 0.406       0.405        0.406
         xgb_12     1.67 1.67       1.63    1.63         2.06 1.63        1.65         1.63
         xgb_146212 0.053 0.053     0.053   0.053        0.129 0.053      0.057        0.053
         xgb_168911 0.964 0.964     0.964   0.957        1.01 0.957       0.964        0.964
         xgb_9981 0.458 0.458       0.458   0.458        0.6   0.458      0.458        0.458
         xgb_167120 1.05 1.05       1.05    1.05         1.09 1.05        1.05         1.05
         xgb_14965 0.933 0.935      0.933   0.932        0.976 0.942      0.944        0.935
         xgb_146606 0.881 0.882     0.881   0.881        0.921 0.881      0.881        0.881
         xgb_7592 0.871 0.866       0.866   0.867        0.967 0.915      0.879        0.869
         xgb_9977 0.494 0.494       0.494   0.494        0.55 0.494       0.494        0.494

                                                    29

Table 18: Final performance of each black-box optimizers (lower is better). We report the median
normalized regret across 32 repetitions for each new benchmarks collected for RandomForest. We
boldface the best result per row.
                   optimizer RS     DE     BOGP BORF Rayhyp BOKDE HEBO
                   rf_10101 0.932 0.961 0.951      0.951   0.917      0.961   0.971
                   rf_53     0.452 0.421 0.452     0.461   0.424      0.479   0.412
                   rf_146818 0.404 0.408 0.402     0.41    0.397      0.41    0.375
                   rf_146821 0.587 0.53 0.551      0.583   0.56       0.718   0.496
                   rf_9952 0.435 0.398 0.423       0.433   0.421      0.49    0.375
                   rf_146822 0.133 0.12 0.135      0.137   0.14       0.169   0.103
                   rf_31     0.949 0.937 0.941     0.954   0.929      0.957   0.901
                   rf_3917 0.946 0.95 0.934        0.941   0.941      0.962   0.976
                   rf_168912 0.81 0.819 0.814      0.835   0.802      0.873   0.8
                   rf_3      0.108 0.097 0.085     0.112   0.103      0.127   0.091
                   rf_167119 0.32 0.309 0.312      0.32    0.305      0.36    0.33
                   rf_12     0.048 0.046 0.045     0.045   0.046      0.048   0.045
                   rf_146212 0.001 0.001 0.001     0.001   0.001      0.001   0.001
                   rf_168911 0.675 0.677 0.667     0.667   0.672      0.672   0.667
                   rf_9981 0.062 0.065 0.053       0.052   0.053      0.067   0.052
                   rf_167120 0.985 0.984 0.984     0.984   0.984      0.984   0.984
                   rf_14965 0.747 0.746 0.743      0.743   0.744      0.746   0.74
                   rf_146606 0.493 0.492 0.492     0.492   0.492      0.493   0.491
                   rf_7592 0.468 0.468 0.466       0.463   0.465      0.468   0.463
                   rf_9977 0.09 0.088 0.086        0.086   0.086      0.09    0.086

Table 19: Final performance of each multi-fidelity optimizers (lower is better). We report the median
normalized regret across 32 repetitions for each new benchmarks collected for RandomForest. We
boldface the best result per row.
          optimizer HB     BOHB DEHB SMAC-HB DF               Rayasha
                                                                 hyp  Optunamd        hb
                                                                            tpe Optunatpe

          rf_10101 0.956 0.956     0.835   0.864        0.99 0.947       0.961        0.961
          rf_53     0.511 0.511    0.379   0.379        0.467 0.471      0.454        0.454
          rf_146818 0.435 0.435    0.372   0.379        0.435 0.408      0.402        0.404
          rf_146821 0.726 0.726    0.244   0.212        0.821 0.655      0.652        0.652
          rf_9952 0.488 0.488      0.357   0.354        0.471 0.438      0.438        0.434
          rf_146822 0.181 0.181    0.095   0.091        0.16 0.166       0.14         0.14
          rf_31     0.964 0.964    0.86    0.871        0.985 0.949      0.947        0.944
          rf_3917 0.962 0.962      0.91    0.917        0.955 0.964      0.95         0.945
          rf_168912 0.875 0.875    0.719   0.694        0.908 0.868      0.831        0.829
          rf_3      0.13 0.13      0.045   0.045        0.135 0.128      0.112        0.09
          rf_167119 0.364 0.364    0.292   0.293        0.525 0.318      0.311        0.305
          rf_12     0.048 0.048    0.046   0.045        0.081 0.046      0.046        0.046
          rf_146212 0.001 0.002    0.001   0.001        0.012 0.001      0.001        0.001
          rf_168911 0.673 0.672    0.67    0.67         0.784 0.672      0.677        0.673
          rf_9981 0.058 0.055      0.054   0.052        0.409 0.053      0.053        0.053
          rf_167120 0.985 0.984    0.984   0.984        0.994 0.986      0.987        0.984
          rf_14965 0.746 0.747     0.743   0.743        0.918 0.742      0.744        0.744
          rf_146606 0.494 0.493    0.493   0.492        0.629 0.495      0.495        0.494
          rf_7592 0.467 0.467      0.465   0.463        0.823 0.464      0.464        0.464
          rf_9977 0.088 0.087      0.086   0.086        0.215 0.086      0.086        0.086

                                                   30

Table 20: Final performance of each black-box optimizers (lower is better). We report the median
normalized regret across 32 repetitions for each new benchmarks collected for MLP. We boldface the
best result per row.
                    optimizer RS      DE     BOGP BORF Rayhyp BOKDE HEBO
                    nn_10101 0.128 0.127 0.125        0.125   0.127     0.127     0.127
                    nn_53     0.185 0.181 0.181       0.18    0.18      0.184     0.181
                    nn_146818 0.188 0.186 0.182       0.186   0.185     0.186     0.186
                    nn_146821 0.023 0.023 0.021       0.02    0.021     0.023     0.024
                    nn_9952 0.175 0.172 0.17          0.172   0.171     0.172     0.17
                    nn_146822 0.048 0.047 0.045       0.045   0.045     0.046     0.046
                    nn_31     0.374 0.372 0.366       0.372   0.371     0.375     0.374
                    nn_3917 0.155 0.154 0.152         0.153   0.152     0.155     0.152

Table 21: Final performance of each multi-fidelity optimizers (lower is better). We report the median
normalized regret across 32 repetitions for each new benchmarks collected for MLP. We boldface the
best result per row.
          optimizer HB        BOHB DEHB SMAC-HB DF              Rayasha
                                                                   hyp  Optunamd        hb
                                                                              tpe Optunatpe

          nn_10101 0.132 0.127       0.127   0.132        0.196 0.132      0.137          0.132
          nn_53     0.183 0.185      0.183   0.183        0.297 0.185      0.185          0.184
          nn_146818 0.189 0.186      0.185   0.188        0.249 0.188      0.192          0.189
          nn_146821 0.024 0.024      0.024   0.024        0.107 0.025      0.025          0.024
          nn_9952 0.173 0.175        0.173   0.173        0.329 0.173      0.172          0.173
          nn_146822 0.049 0.048      0.047   0.049        0.105 0.05       0.051          0.049
          nn_31     0.373 0.375      0.377   0.375        0.428 0.363      0.377          0.375
          nn_3917 0.157 0.155        0.155   0.156        0.181 0.16       0.159          0.157

Table 22: P-value of a sign test for the hypothesis that advanced methods outperform the baseline
RS for black-box optimization and HB for multi-fidelity optimization for the new benchmarks. We
underline p-values that are below α = 0.05 and also boldface p-values that are below α = 0.05
after multiple comparison correction (dividing α by the number of comparisons, i.e. 5 and 4;
boldface/underlined implies that the advanced method is better). We also give the wins/ties/losses of
RS and HB against the challengers.
                                           DE       BOGP          BORF             HEBO       BOKDE
       p-value against RS             0.00058    0.00000       0.00000           0.00000      0.99909
       wins/ties/losses against RS   43/33/12    56/27/5       55/24/9          55/22/11     15/30/43
                                        BOHB       DEHB        SMAC-HB               DF
       p-value against HB             0.94535    0.00000        0.00000          0.99992
       wins/ties/losses against HB   10/54/24    56/28/4        57/28/3          24/6/58

                                                     31

Table 23: P-values of a sign test for the hypothesis that multi-fidelity outperform their black-box
counterparts for the new benchmarks. We boldface p-values that are below α = 0.05 (boldface
implies that the multi-fidelity method is better).
      Budget               HB vs RS   DEHB vs DE     BOHB vs BOKDE       SMAC-HB vs BORF
                p-values    0.97350       0.02650             0.99516               0.85820
      100%
                   w/t/l   24/23/41      37/33/18            16/33/39              16/47/25
                p-values   0.04284        0.00000             0.16870               0.00008
      10%
                   w/t/l   50/5/33        68/8/12            43/12/33              57/10/21
                p-values   0.00000        0.00000             0.00000              0.00000
      1%
                   w/t/l   76/2/10          79/0/9              78/5/5               81/1/6

         Cartpole                  BNN                     Net                  NBHPO

           NB101                NB1Shot1                 NB201                    SVM

           LogReg             RandomForest              XGBoost                   MLP

Figure 5: Median rank over time. We report the median rank of the performance across all benchmarks
of a benchmark family (see Table 1) for all optimizers.

                                                32

         Cartpole                  BNN                     Net                  NBHPO

          NB101                 NB1Shot1                 NB201                    SVM

          LogReg              RandomForest              XGBoost                   MLP

Figure 6: Median rank over time. We report the median rank of the performance across all benchmarks
of a benchmark family (see Table 1) for black-box optimizers.

         Cartpole                  BNN                     Net                  NBHPO

          NB101                 NB1Shot1                 NB201                    SVM

          LogReg              RandomForest              XGBoost                   MLP

Figure 7: Median rank over time. We report the median rank of the performance across all benchmarks
of a benchmark family (see Table 1) for multi-fidelity optimizers.

                                                33

  Cartpole              BNN P rotein             BNN Y ear

  NetAdult                NetHiggs               NetLetter                NetM N IST

 NetOptDig                NetP oker

NB101Cf 10A             NB101Cf 10B            NB101Cf 10C

NBHPON aval             NBHPOP ark             NBHPOP rot             NBHPOSlice

NB201Cf 10V             NB201Cf 100             NB201IN et

 NB1Shot11               NB1Shot12              NB1Shot13

             Figure 8: Median performance-over-time for all optimizers.

                                         34

  Cartpole            BNN P rotein            BNN Y ear

  NetAdult             NetHiggs               NetLetter            NetM N IST

 NetOptDig             NetP oker

NB101Cf 10A          NB101Cf 10B            NB101Cf 10C

NBHPON aval          NBHPOP ark             NBHPOP rot             NBHPOSlice

NB201Cf 10V           NB201Cf 100            NB201IN et

 NB1Shot11            NB1Shot12              NB1Shot13

       Figure 9: Median performance-over-time for black-box optimizers.

                                      35

  Cartpole            BNN P rotein             BNN Y ear

  NetAdult              NetHiggs               NetLetter             NetM N IST

 NetOptDig              NetP oker

NB101Cf 10A           NB101Cf 10B            NB101Cf 10C

NBHPON aval           NBHPOP ark             NBHPOP rot             NBHPOSlice

NB201Cf 10V           NB201Cf 100             NB201IN et

 NB1Shot11             NB1Shot12              NB1Shot13

     Figure 10: Median performance-over-time for multi-fidelity optimizers.

                                       36
