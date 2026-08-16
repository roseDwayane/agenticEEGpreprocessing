---
citation_key: "BossekEtAl2020"
title: "Initial design strategies and their effects on sequential model-based optimization: an exploratory case study based on BBOB"
authors: "Jakob Bossek; Carola Doerr; P. Kerschke"
year: 2020
doi: "10.1145/3377930.3390155"
source: "arXiv (2003.13826)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "2003.13826"
conversion: "pdftotext -layout (automated)"
---

# Initial design strategies and their effects on sequential model-based optimization: an exploratory case study based on BBOB

Initial Design Strategies and their Effects on
                                                                       Sequential Model-Based Optimization
                                                                                              An Exploratory Case Study Based on BBOB

                                                            Jakob Bossek                                     Carola Doerr                                 Pascal Kerschke
                                                 School of Computer Science                          Sorbonne University, CNRS, LIP6             Information Systems and Statistics
                                             The University of Adelaide, Australia                            Paris, France                       University of Münster, Germany

                                         ABSTRACT                                                                        last decade. SMBO forms today an integral part of the state-of-the-

arXiv:2003.13826v1 [cs.NE] 30 Mar 2020
                                         Sequential model-based optimization (SMBO) approaches are algo-                 art heuristic solvers. Its probably best-known representatives are
                                         rithms for solving problems that require computationally or other-              Bayesian Optimization (see surveys by [40, 46, 49]) and, in particular,
                                         wise expensive function evaluations. The key design principle of                Efficient Global Optimization (EGO, [30]).
                                         SMBO is a substitution of the true objective function by a surrogate,              The generic SMBO method works as follows. An initial design
                                         which is used to propose the point(s) to be evaluated next.                     of points is sampled and evaluated with the true objective function.
                                            SMBO algorithms are intrinsically modular, leaving the user                  The eponymous sequential part iteratively (1) builds a surrogate
                                         with many important design choices. Significant research efforts go             of the true objective function (on basis of the already evaluated
                                         into understanding which settings perform best for which type of                samples), (2) proposes new samples by optimizing a so-called infill-
                                         problems. Most works, however, focus on the choice of the model,                criterion (which is sometimes referred to as acquisition function),
                                         the acquisition function, and the strategy used to optimize the latter.         (3) evaluates these additional samples, and (4) integrates these sam-
                                         The choice of the initial sampling strategy, however, receives much             ples, together with their quality indicators (“function values”, “fit-
                                         less attention. Not surprisingly, quite diverging recommendations               ness”) into the memory. Each of these steps offers a great variety of
                                         can be found in the literature.                                                 design choices, which all may affect the performance of the SMBO
                                            We analyze in this work how the size and the distribution of the             procedure. Which surrogate model should be used? Which of the
                                         initial sample influences the overall quality of the efficient global op-       countless infill criteria to use? What method should be used to
                                         timization (EGO) algorithm, a well-known SMBO approach. While,                  create the initial sample and what proportion of the overall budget
                                         overall, small initial budgets using Halton sampling seem preferable,           should be spent on the initial design? While a large body of works
                                         we also observe that the performance landscape is rather unstruc-               addresses the first two questions (see surveys mentioned above),
                                         tured. We furthermore identify several situations in which EGO                  the latter two questions are treated rather poorly. In this work we
                                         performs unfavorably against random sampling. Both observations                 aim to shed light on the relevance of a suitably chosen initial sam-
                                         indicate that an adaptive SMBO design could be beneficial, making               pling strategy. More precisely, we study how the size of the initial
                                         SMBO an interesting test-bed for automated algorithm design.                    design and the strategy used to generate it affects the performance
                                                                                                                         of SMBO. As a well-established benchmark environment offering
                                         CCS CONCEPTS                                                                    a great variety of different numerical optimization problems, we
                                                                                                                         chose the 24 noiseless BBOB functions (in different dimensions) as
                                         • Computing methodologies → Continuous space search.
                                                                                                                         test-bed for our investigation.
                                         KEYWORDS                                                                           Our setup comprises of varying the initial design strategy (clas-
                                                                                                                         sical uniform and Latin-Hypercube-Sampling (LHS) as the most
                                         Sequential Model-Based Optimization, Design of Experiments, Ini-                frequently used methods and quasi-random Halton and Sobol’ se-
                                         tial Design, Continuous Black-Box Optimization                                  quences), the total budget, and the fraction of this total budget that
                                                                                                                         is used to build the initial sample. We study a total of 720 problems,
                                         1    INTRODUCTION                                                               which are evaluated against 40 different initial design strategies.
                                         Sequential Model-Based Optimization (SMBO) algorithms are tech-                    Our general observation is that SMBO performance tends to
                                         niques for the optimization of problems for which the evalua-                   decrease with increasing initial design ratio, which is in line with
                                         tion of solution candidates is resource-intensive, such as prob-                the general expectation that adaptive search should outperform
                                         lems requiring real physical experiments or problems that require               non-adaptive sampling. This may justify extreme settings such as
                                         computationally-expensive simulations. The latter are particularly              the singleton initial design used in the SMAC parameter tuning
                                         present in almost any application of Artificial Intelligence, most no-          framework [28]. As always in simulation-based optimization, we are
                                         tably in terms of parameter tuning problems – a problem that is also            confronted with the important trade-off between the exploitation
                                         omnipresent in Evolutionary Computation [37]. SMBO-based tech-                  of already acquired knowledge (through adaptive sampling) and
                                         niques are among the most successfully applied hyper-parameter                  the reduction of uncertainty in regions of the search space that
                                         tuning methods [1, 18, 28, 35], so that research on this family of it-          are currently not well covered with already evaluated samples.
                                         erative optimization heuristics has gained significant traction in the          Sampling in the latter regions of high uncertainty – commonly
                                         GECCO ’20, July 8–12, 2020, CancÃžn, Mexico
                                                                                                                         referred to as exploration – can help to identify other promising
                                         2020. ACM ISBN 978-1-4503-7128-5/20/07. . . $15.00                              regions of the search space. In our experiments, we observe indeed
                                         https://doi.org/10.1145/3377930.3390155
                                                                                                                     1

GECCO ’20, July 8–12, 2020, CancÃžn, Mexico                                                               Jakob Bossek, Carola Doerr, and Pascal Kerschke

that small initial designs are not always preferable. In fact, we even       well on parameter tuning challenges. From experiments on hyperidentify cases in which pure (quasi-)random sampling outperforms             parameter tuning of evolutionary computation techniques, they
any of the tested SMBO-based techniques.                                     conclude that LHS sampling is, in general, to be preferred over uni-
   We use our huge database also to investigate advantages of long           form sampling. They thereby disagree with statements previously
runs vs. restarted ones. That is, we address the question whether            made in [48], which argues that LHS designs do not gain much
one should use the full budget for one long run, or whether two              over uniform sampling, and that quasi-random sampling strategies
shorter runs of smaller budget are preferable. We identify several           should be used instead. The recommendation in [48] is, however,
cases in which restarts seem preferable, giving another indication           to be understood in terms of general design of experiments setting,
that an adaptive design of SMBO techniques could be preferable.              and not specifically addressing SMBO initialization.
   The evaluation and analysis of the dataset (which comprises                   Brockhoff et al. [11] studied the difference between random
more than 500 000 experiments) has been particularly challenging,            sampling and LHS designs for Matlab’s MATSuMoTo model-based
as no clear pattern between the performance of the different designs         optimizer [42]. In contrast to our work, they fix the total budget of
and the parameters of the problem (such as its dimension, its high-          function evaluations to n = 50 · d (whereas we use n = 24 , . . . , 29 )
level features, or even its function ID) were observable. Our data           and compared only four initial designs: LHS with 2 · (d + 1) · k for
suggests that machine-trained algorithm configuration techniques             k = 1, 2, 10 and random sampling with 4 · (d + 1) points. Results are
should be able to outperform state-of-the-art SMBO designs by                compared against SMAC [28] and pure random sampling. Their exlarge margins. The appropriateness of the BBOB dataset for finding           periments are also across all 24 BBOB functions in d = 2, 3, 5, 10, 20
generalizable patterns has been shown in [4, 33].                            dimensions (we study d = 2, 3, 4, 5, 10). Their performance measure
                                                                             is a fixed-target measure, more precisely they study the expected
   Paper Organization. This work is structured as follows. Below,            running time (ERT) for target values that are chosen individually
we continue with an overview of related work and give information            for each function and they also compare the anytime performance
about the availability of our data. Section 2 details the SMBO ap-           in terms of ECDF curves. Based on their experiments, Brockhoff et
proach. In Section 3 we describe our experimental setup including            al. conclude that for this setting, no clear advantage of LHS designs
considered benchmark problems, parameter choices and perfor-                 can be observed and that large initial samples seem detrimental.
mance measures. Results are presented in Sections 4 to 6. We con-                Morar et al. [41] also compare LHS and uniform sampling, but fix
clude with final remarks and visions for incorporating the acquired          the size of the initial design to 2 ·d and rather focus on the interplay
knowledge into improved SMBO approaches.                                     between initial design distribution and the infill criteria used in
                                                                             the adaptive steps of the SMBO framework. They compare perfor-
   Related Work. For surveys on Bayesian optimization and, more              mances on two variants of the Branin function, a classic benchmark
generally, SMBO approaches we refer the interested reader to the             in SMBO research, and on two parameter tuning problems. They
already mentioned surveys [40, 46, 49]. Our work builds on EGO,              conclude that the total budget has an important influence on the
originally suggested by Jones, Schonlau, and Welch [30]. EGO is              ranking of the different SMBO algorithms. In line with our observacharacterized by using a flexible Kriging, i.e., a Gaussian process          tions and conclusions, they recommend tuning of the SMBO design
surrogate model which offers a natural uncertainty estimate and              if one is likely to see similar types of problems several times.
the widely used quasi-standard expected improvement (EI) infill                  More recently, Lindauer et al. [36] analyze the sensitivity of
criterion which balances exploitation of the model and exploration           Bayesian optimization heuristics w.r.t. its own hyper-parameters.
of uncertain regions of the model [29].                                      This study, however, puts a much stronger emphasis on the various
   Our key interest is an analysis of the influence of the initial de-       design choices, and details for the initial sampling strategy are not
sign’s size and distribution. We assess four different distributions:        explicitly mentioned, although Table 3 in their work suggests that
uniform sampling, LHS, Halton points, and Sobol’ sequences. For              this has been varied as well.
each of these designs we test ten different initial sample sizes. Recommendations on which initial design should be favored vary quite               Availability of Project Data. While this report highlights a few of
significantly within the community, see [2, 41] for a discussion. In         our key findings, and demonstrates which statistics are possible to
terms of design size, SMAC [28] makes an extreme choice in that it           obtain with the data, the full data base offers much more than we
uses only one randomly sampled initial design point, whereas other           can touch upon in a single conference paper. Not only can our data
commonly found SMBO implementations typically operate with                   be used to zoom further into the various settings described below,
an initial design of size 10 · d [30, 41], where d denotes the search        but it also offers additional information about the function value
space dimension (i.e., the optimization problem can be modeled as            of the best initial design point and of the first point queried in an
a function f : S ⊆ Rd → R). In terms of design distribution, LHS             adaptive fashion, as well as the distance of these points and of the
and uniform sampling are routinely used in SMBO applications,                best solution to the optimal solution (in the decision space [−5, 5]d ,
while quasi-random designs, like Halton and Sobol’ designs, are less         measured in terms of the L2 norm).
commonly found – despite several indications that their even distri-            Please note that most of the results reported below are based on
bution may be beneficial for maximizing the initial exploration [48].        median values per (dimension, function, total budget, initial budget
   We next summarize the main works which explicitly address the             ratio, design) combination. This is to avoid correcting factors for the
question how to chose the initial design.                                    comparison between the Halton designs (for which we have 5 runs
   Bartz-Beielstein and Preuss study in [2] suitable initial designs         for each of the 7 200 considered settings) and the other three designs
for SPOT [1], an SMBO algorithm specifically designed to perform             (for which we have 25 independent runs per setting, i.e., 5 SMBO
                                                                         2

Effects of Initial Design Strategies on SMBO Performance                                                                     GECCO ’20, July 8–12, 2020, CancÃžn, Mexico

runs for each of the 5 random samples from the design). Detailed                                 Our study is based on the classical EGO algorithm by Jones.
results for each experiment are available in the data base, so that
one can easily perform statistical tests, or use other aggregation                           3     EXPERIMENTAL SETUP
methods. An interactive evaluation of the data is possible with the                          Our study investigates the effect of the total budget, the size of the
very recently released tool HiPlot [25], which essentially produces                          initial design (i.e., the number of evaluations prior to building the
parallel coordinate plots through which one can easily navigate by                           first surrogate), and the distribution of this initial design on the
zooming and/or highlighting different parts of the data.                                     quality of the final recommendation made by an off-the-shelf SMBO
   The interested reader can find all our project data on [10].                              algorithm. Below, we summarize the benchmark problems and so-
                                                                                             lution strategies (Section 3.1), as well as the performance measures
2    SEQUENTIAL MODEL-BASED                                                                  that we used to evaluate the different strategies (Section 3.2).
     OPTIMIZATION                                                                               All our experiments are implemented in the R programming envi-
In many real-world applications like production engineering, nu-                             ronment [45]. To be more precise: the SMBO framework mlrMBO [6]
merical simulations, or hyper-parameter tuning, the objective func-                          serves as the working horse for our experimental study, the smooftion f at hand is often of black-box nature. That is, (a) there is little                    package [8] is used for an interface to the BBOB functions and the
or no knowledge about the structure of f (in particular, we typically                        interface package dandy [9] is used to generate the initial designs.
do not have derivatives), and (b) function evaluations are expensive                         The latter delegates to packages qrng [26] and randtoolbox [14],
in terms of computational and/or monetary resources (days of com-                            which implement quasi-random sequence generators as well as to
putation time or actual physical experiments). As a consequence, in                          package lhs [13] for the LHS designs.
the course of problem solving, one tries to keep the number of true
function evaluations low. In such settings, sequential model-based                           3.1    Benchmark Problems and Solvers
optimization (SMBO, [27]) – also known under the term Bayesian                               We use the following setup for our experimental analysis:
optimization1 – advanced to the state-of-the-art in recent years and                         • The objective function f . As mentioned in the introduction, we
is used extensively in many fields of research, e.g., within versatile                         focus on the 24 functions from the (noiseless and single-objective)
tools for automatic algorithm configuration [28].                                              BBOB test suite [22]. An overview of these functions is available
    In a nutshell, the key idea of SMBO is as follows: a regression                            in [23]. We consider the first instance of each function, whose dmodel, i.e., an approximation fˆ to the true optimization problem f ,                          dimensional variant we denote by f (d ) . We let F d be the collection
is fitted to the evaluated points of an initial design. Subsequently,                          of these 24 functions. We study minimization as objective.
the model fˆ serves as a cheap surrogate for the expensive true                              • The problem dimension d. We consider five different search
objective function and is used to determine the next point(s) worth                            space dimensions: d ∈ D := {2, 3, 4, 5, 10}.
being evaluated through the actual problem f . These points are                              • Total budget n. The total number of function evaluations. We
determined by optimizing a so-called infill criterion (also referred to                        consider six different budgets: n ∈ N := {2x | x ∈ {4, . . . , 9}}.
as acquisition function) which keeps balance between exploiting the                          • Initial design ratio k: We consider initial designs of size ⌈k · n⌉
model (in the sense of striving to high-quality points) and exploring                          with k ∈ K := {0.1, 0.2, . . . , 1.0}.
the search-space regions which lack a good model fit (i.e., regions                          • Sampling design s. We study four different distributions from
with a high uncertainty about the quality of approximation fˆ). Note                           which the d-dimensional initial design of size ⌈k · n⌉ is sampled:
that the optimization of the acquisition function itself is an (often                          – uniform sampling: R’s default random number generator
highly multimodal) optimization problem, which is typically solved                               (Mersenne-Twister [38]) to generate uniform samples.
by state-of-the art solvers such as CMA-ES [24], Nelder-Mead [43],                             – Latin Hypercube Sampling (LHS [39]): “improved” LHS design
or simply by standard Newton methods, if the surrogate model fˆ                                   as suggested in [3].
allows. The key here is that those algorithms now operate on fˆ                                – Sobol’ sequences [50]: randtoolbox implementation with
and not on f , which can be evaluated much more efficiently. The                                  scrambling as proposed by Owen [44], and Faure & Tezuka [19].
points proposed from the optimization of the acquisition function                              – Halton designs [21] randtoolbox implementation with default
are then evaluated through f and the surrogate fˆ is updated to                                   parameters.
account for the new information. The process is repeated until the                             More detailed definitions, motivations, and applications of these
available budget of time or function evaluations is depleted.                                  distributions can be found, for example, in [15].
    Jones [30] was the first who used this approach in his Efficient                         • Random seed r i - initial design. While the Halton point sets
Global Optimization (EGO) algorithm. Therein, Gaussian processes                               are deterministic, the other designs produce random points. To
serve as the surrogate and expected improvement (EI) is adopted as                             account for this randomness, we sample Ri = 5 instances from
infill criterion. Following Jones’ seminal contribution, a plethora of                         each of the three random (i.e., non-Halton) designs.
extensions were proposed by the community including multi-point                              • Random seed r A - SMBO randomness. Finally, to compensate
proposal [7] and multi-objective SMBO (e.g., [34]) making SMBO a                               for the randomness of the SMBO algorithm (note that the SMBO
highly flexible framework with many interchangeable components                                 process is stochastic itself, e.g., by means of a stochastic procedure
and facets. We refer the interested reader to [27] (and references                             used to search the infill-criterion), we do R A = 5 independent
therein) for a comprehensive overview.                                                         runs per each of the settings fixed through the decisions above.
1 Originally, Bayesian Optimization only referred to SMBO approaches with Bayesian             It should be noted that we do not vary the infill criterion (also
priors, but nowadays the term is often used to denote the whole class of SMBO methods.       known as acquisition function), nor any other component of the
                                                                                         3

GECCO ’20, July 8–12, 2020, CancÃžn, Mexico                                                                                        Jakob Bossek, Carola Doerr, and Pascal Kerschke

                                                                                                      Best sampling design            Halton    LHS      Sobol     Uniform
SMBO, but use the default variant of mlrMBO v1.1.4 with expected
improvement as infill criterion and a Kriging surrogate.                                                   Best initial ratio
                                                                                                                                0.1 0.2 0.3 0.4 0.5 0.6 0.7 0.8 0.9 1.0
   With the notation above, we consider a total number of |F | ·
|D| · |N | = 24 · 5 · 6 = 720 different problems, and for each                                 2
                                                                                               3
of these problems we consider |S | · |K | = 4 · 10 = 40 different                              4
                                                                                               5
                                                                                                                                                                             16

solution strategies. Here we consider the budget as integral part                             10
                                                                                               2
of a problem, since SMBO algorithms are typically applied when                                 3
                                                                                               4                                                                             32
the budget is fixed a priori. We therefore distinguish between the                             5
function f (d ) that is to be optimized, and the problem (f , d, n) of                        10
                                                                                               2
minimizing f (d ) with a given budget n.                                                       3
                                                                                               4                                                                             64
   As mentioned above, on each problem we perform 5 runs of each                               5
                                                                                              10
strategy which is based on Halton designs and we perform 25 runs                               2
for all other strategies. Our total number of experiments is thus                              3
                                                                                               4                                                                             128
               |F | · |D| · |N | · |K | · (1 + (|S | − 1) · Ri )) · R A                        5
                                                                                              10
                                                                                               2
             =24 · 5 · 6 · 10 · (1 + (3 · 5)) · 5 = 576 000.                                   3
                                                                                               4                                                                             256
Not all of these runs terminated successfully, due to problems with                            5
                                                                                              10
the Kriging implementation used by mlrMBO. The problems occur                                  2
                                                                                               3
in particular with high total budget and low initial design ratio.                             4                                                                             512
                                                                                               5
Here, the Kriging-routine obviously runs into problems when many                              10
points are sampled close to each other as it often is the case when                                1 2 3   4 5    6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24
                                                                                                                                   BBOB function
SMBO runs converge into a (local) optimum. While for each n ≤ 128
there are at least 99.8% successful runs this number reduces to                           Figure 1: Overview of the virtual best solver (VBS), i.e., the
94% for n = 256 and 85.4% for n = 512. In total, we had 555 598                           strategy (k, s) that achieved the best median performance on
(96.5%) successful runs. In all computations below we only consider                       the respective problem (f , d, n).
(f , d, n, k, s) combinations for which at least three runs terminated
successfully, i.e., provided their recommendation.
                                                                                          Fig. 1 shows which strategy defined the VBS for which problem(s).
3.2     Performance Measures and VBS                                                      A first visual interpretation suggests that this data is relatively
                                                                                          unstructured; we will come back to this point further below.
For each of our experiments we record the value of the best solution                         By design, some of the BBOB functions are much “harder” than
that has been evaluated during the entire run. We denote this value                       others, so that we see substantial differences in the target precision
by f (d, n, k, s, r i , r A ). Since the BBOB functions have quite diverse                that can be achieved with a fixed budget n. To compensate for that in
ranges of function values, we do not study these function values                          our aggregations, we will frequently study the relative performance
directly, but rather follow standard practice in BBOB studies and                         of a strategy (k, s) compared to the VBS. To this end, we set
focus on the target precision, i.e., the gap to the global optimum,
                                                                                                      R(f , d, n, k, s) := M(f , d, n, k, s)/VBS(f , d, n)
        p(f , d, n, k, s, r i , r A ) := f (d, n, k, s, r i , r A ) − inf f (d ) .
  As mentioned above, we will restrict most of our analyses to the                        and refer to R(f , d, n, k, s) as the relative target precision of strategy
median performance of each strategy on each problem. Our main                             (k, s) on problem (f , d, n). Note that these values are at least one,
performance criteria is therefore                                                         where R(f , d, n, k, s) = 1 implies that strategy (k, s) achieved the
                                                                                          best median target precision among all the 40 different strategies.
M(f , d, n, k, s) = M ({p(f , d, n, k, s, r i , r A ) | r i ∈ Ri (s), r A ∈ [5]}) ,
where M denotes the median and where we use the convention                                4    AGGREGATED RESULTS
that Ri (Halton) = {1} and Ri (s) = {1, 2, . . . , 5} =: [5] for the other                As shown in Fig. 1, it is not possible to derive simple rules that define
sampling designs s ∈ S \ {Halton}.                                                        which strategy achieves the best performance on each of the BBOB
    Virtual Best Solver and Relative Target Precision. An important                       functions. In Fig. 2 we therefore count how often each strategy
concept in comparing portfolios of algorithms is the virtual best                         forms the VBS. Therein, we observe a clear advantage for Halton
solver (VBS). This VBS describes a hypothetical algorithm that for                        designs (it has the most “hits” for any given initial ratio except for
each problem (i.e., each (f , d, n) combination in our case) selects an                   k = 100%), and we further observe a clear tendency towards small
algorithm A from a given portfolio A that achieves the best perfor-                       initial ratios. However, we also see that each strategy “wins” at least
mance [32]. In our case, the algorithm portfolio is the collection of                     one problem. Neither the simple counting statistics in Fig. 2 nor
all 40 (k, s) combinations. As we consider median performance, the                        the more detailed overview in Fig. 1 provide any information about
VBS is defined by selecting for each problem (f , d, n) the strategy                      the magnitude of the advantage. We thus plot in Fig. 3 the distribu-
(k, s) that achieved the best median function value. For notational                       tion of the relative target precision R(f , d, n, k, s) of each strategy
convenience, we omit the explicit mention of the median and set                           (k, s), aggregated again over all 720 problems. This plot confirms
                                                                                          the tendency that spending a larger ratio of the total budget on
         VBS(f , d, n) := min{M(f , d, n, k, s) | s ∈ S, k ∈ K }.                         generating the initial design results in worse overall performance.
                                                                                      4

Effects of Initial Design Strategies on SMBO Performance                                                                                                                                                                      GECCO ’20, July 8–12, 2020, CancÃžn, Mexico

                                                               Number of problems (f, d, n) as VBS
                                                                                                                     1               3                  10          30             100+
                                                                                                                                                                                              functions) relative performance; i.e., the value in each cell repre-
                                                                                                                                                                                              sents M ({R(f , d, n, k, s) | f ∈ {1, 2, ..., 24}}) for the given dimen-

Sampling design
                                          Total                 225     147            92           75       52          37          33          26           16      17       720
                                        Uniform                 35      23             18           12        8          6           7           4             1      4        118            sion, budget, and strategy. We observe that in most cases the per-
                                         Sobol                  36      17             5            8         9           1           4           4            1      6        91
                                           LHS                  43      22             10           12        4          4           3           5             4      2        109            formance worsens with increasing initial budget ratio k, and
                                         Halton                 111
                                                                0.1
                                                                        85
                                                                            0.2
                                                                                       59
                                                                                       0.3
                                                                                                    43
                                                                                                    0.4
                                                                                                             31
                                                                                                             0.5
                                                                                                                         26
                                                                                                                         0.6
                                                                                                                                     19
                                                                                                                                     0.7
                                                                                                                                                 13
                                                                                                                                                 0.8
                                                                                                                                                              10
                                                                                                                                                              0.9
                                                                                                                                                                      5
                                                                                                                                                                      1.0
                                                                                                                                                                               402
                                                                                                                                                                              Total
                                                                                                                                                                                              this consistently for each problem dimension d and total budget n.
                                                                                                             Initial design ratio                                                                 The values in the rows labeled “Total” are the median values over
Figure 2: Number of problems (f , d, n) for which the respec-                                                                                                                                 all budgets (last row per dimension) and dimensions (bottom-most
tive strategy forms the VBS.                                                                                                                                                                  rows), respectively. Noticeably, the influence of the sampling de-
                                                                                                                                                                                              sign vanishes with increasing dimension – independent from
                                                                      Sampling design                     Halton            LHS               Sobol            Uniform                        the budget ratio. Aggregated over all dimensions, the differences

Relative performance R(f, d, n, k, s)
                                                         0.1          0.2          0.3              0.4       0.5           0.6            0.7           0.8         0.9           1.0        between the designs are small, as already observed in Fig. 3.
                                        10                                                                                                                                                        Remember that the values in Fig. 5 are always scaled by the VBS
                                                                                                                                                                                              that is specific for problem (f , d, n), but independent from strategy
                                          7                                                                                                                                                   (k, s). This implies that the rows are computed against the same
                                                                                                                                                                                              VBS, but different rows compare against different strategies. Values
                                          4                                                                                                                                                   in different rows should therefore only be compared with care.

                                          1                                                                                                                                                   5    PERFORMANCE BY FUNCTION
                                                                                                          Sampling design
                                                                                                                                                                                              After having studied values that were aggregated across all 24
Figure 3: Boxplots of relative performances R(f , d, n, k, s)                                                                                                                                 BBOB functions (see Section 4), we now take a closer look at the
across all 600 problems (f , d, n) with budget n ≤ 256, shown                                                                                                                                 differences between the different strategies on each of the functions.
for all 40 different strategies (k, s). The y-axis is capped at 10.
                                                                                                                                                                                                  Influence of the Total Budget. Fig. 4 reports the median target pre-
We also observe that although the Halton design generated with                                                                                                                                cision (shown on a log-scale) achieved by Halton and Sobol’ designs
k = 10% of the total budget has the best median performance,                                                                                                                                  with k = 10% initial budget, in dependence of function f and total
the actual differences between the four designs are rather small.                                                                                                                             budget n. The plot reveals the functions that are easy (e.g., functions
                                                                                                                                                                                              1, 21, 22) and difficult (functions 10 and 12) for SMBO. Note that
                                                                            Logarithmic target precision log10(M(f, d, n, k, s))                                                              the performance convergence is not always monotonically decreas-
                                                                                                                                                                                              ing with increasing total budget size. This might result from the
                                                                        -3.50                  -1.75                 0.00                  1.75                 3.50+                         small number of repetitions (5 for the Halton design, 25 for Sobol’).
                                                                                                                                                                                              However, the differences are fairly small. Fig. 6 extends Fig. 4 to all
                                                                                   Halton                                                          Sobol
                                                        24      1.9     1.6        1.6        1.5      1.4     1.3             1.8     1.7        1.6         1.4    1.4     1.3
                                                                                                                                                                                              40 strategies (k, s). That is, for each 5-dimensional problem (f , 5, n)
                                                        23      0.8     0.4        0.6        0.6      0.4                     0.8     0.7        0.6         0.5    0.5     0.4              a heatmap of the relative performances R(f , d, n, k, s) is shown for
                                                        22      1.0     0.7        0.3       -1.6     -2.4    -3.1             1.4     1.1       -1.0        -1.2   -2.0    -3.0
                                                        21      0.8     0.9       -0.9        0.0     -0.1    -3.0             1.1     0.9       -0.3        -1.3   -2.2    -3.5              all pairs of sampling design s and initial design ratio k. We observe
                                                        20      0.7     0.6        0.4        0.3      0.2                     2.1     0.5        0.4         0.4    0.3     0.3
                                                        19      0.9     0.7        0.2        0.5      0.4     0.4             1.0     0.8        0.6         0.5    0.4     0.3              that, in particular for functions 15, 19, 23 and 24, the differences
                                                        18      1.7     1.3        1.2        1.0      0.7     0.6             1.5     1.3        1.0         0.8    0.7     0.6
                                                        17      0.9     0.7        0.6        0.1     -0.1    -0.1             0.9     0.7        0.5         0.2    0.1     0.0              between the different initial budgets are comparatively small. This
                                                        16      1.4     1.5        1.2        1.1      1.0     0.7             1.5     1.3        1.1         1.0    0.9     0.9
                                                                                                                                                                                              likely results from the functions’ highly multimodal landscapes,

                                        BBOB function
                                                        15      1.8     1.6        1.6        1.5      1.5     1.4             1.8     1.6        1.6         1.5    1.4     1.4
                                                        14      0.7     0.2        0.2       -0.4     -1.1    -1.5             0.7     0.4       -0.1        -0.6   -0.1    -1.6              which hinder SMBO from training reasonable surrogates.
                                                        13      2.5     2.0        1.8        1.5      1.3                     2.5     2.0        1.7         1.5    1.3     1.2
                                                        12      6.9     6.3        5.7        6.2      5.7     6.1             6.8     6.5        5.1         5.9    5.9     6.0
                                                        11
                                                        10
                                                                3.9
                                                                5.0
                                                                        3.5
                                                                        5.0
                                                                                   1.9
                                                                                   3.8
                                                                                              1.7
                                                                                              3.9
                                                                                                       1.5
                                                                                                       3.3
                                                                                                               1.5
                                                                                                               2.4
                                                                                                                               3.6
                                                                                                                               5.2
                                                                                                                                       2.6
                                                                                                                                       4.6
                                                                                                                                                  2.0
                                                                                                                                                  4.0
                                                                                                                                                              1.8
                                                                                                                                                              3.4
                                                                                                                                                                     1.5
                                                                                                                                                                     3.0
                                                                                                                                                                             1.3
                                                                                                                                                                             2.4
                                                                                                                                                                                                 Influence of the initial sample size k and design s. Fig. 7 shows
                                                         9      3.2     2.7        1.7        1.7      0.8                     3.1     2.5        2.2         1.8    1.5     0.8              the relative median target precision R(f , d, n, k, s) for all 24 BBOB
                                                         8      3.3     2.6        1.7        1.2      0.7    -0.3             3.2     2.6        2.0         1.5    0.7    -0.0
                                                         7      1.5     1.3        1.2        0.2     -0.2    -0.6             1.6     1.3        0.9         0.1   -0.2    -0.5              functions, for a fixed budget of 128 function evaluations and variable
                                                         6      2.0     2.0        1.9        1.1      1.1     0.8             2.4     1.8        1.9         1.7    1.1     1.1
                                                         5     -0.1     0.1       -0.2       -0.4     -0.3    -0.4             0.1    -0.1       -0.3        -0.3   -0.2    -0.3              dimension (columns) and strategies (rows). We recall that the VBS
                                                         4      2.3     2.1        1.8        1.5      1.3     1.0             2.2     2.1        1.9         1.7    1.4     1.0
                                                         3      1.7     1.5        1.4        1.3      1.3     1.2             1.8     1.6        1.5         1.3    1.2     1.1              is defined per column, i.e., each column has at least one strategy
                                                         2
                                                         1
                                                                4.6
                                                                1.0
                                                                        4.0
                                                                       -1.7
                                                                                   3.0
                                                                                  -2.6
                                                                                              2.8
                                                                                             -2.7
                                                                                                       2.0
                                                                                                      -2.9
                                                                                                               1.5             4.8
                                                                                                                               0.5
                                                                                                                                       3.6
                                                                                                                                      -1.6
                                                                                                                                                  3.3
                                                                                                                                                 -2.5
                                                                                                                                                              2.8
                                                                                                                                                             -2.7
                                                                                                                                                                     2.0
                                                                                                                                                                    -2.9
                                                                                                                                                                             1.6
                                                                                                                                                                            -2.9
                                                                                                                                                                                              with R(f , d, n, k, s) = 1 (see Fig. 1).
                                                                16     32         64         128      256     512              16        32      64          128    256     512                  We observe that the benefit of small initial budgets is important
                                                                                                             Total budget                                                                     for functions with at most medium-sized indices. This finding is
                                                                                                                                                                                              very plausible, as the first 14 functions mainly are separable and/or
Figure 4: Logarithmic median target precision                                                                                                                                                 unimodal – i.e., functions whose structure can be well exploited by
log10 (M(f , d, n, k, s)) depending on the total budget. Re-                                                                                                                                  SMBO. However, for the group of multimodal functions (IDs
sults are shown for Halton (left) and Sobol (right) designs                                                                                                                                   15 to 24), with the notable exception of functions 21 and 22, the
with an initial budget of 10% of the total budget and across                                                                                                                                  differences between the different initial ratios are rather small, indiall 5-dimensional BBOB functions. Gray boxes are due to                                                                                                                                       cating that SMBO does not perform much better than (quasi-)
missing data (less than 3 successful runs, see Section 3.1).                                                                                                                                  random sampling in the initial phases of the optimization process.
                                                                                                                                                                                                 We also see interesting cases in which larger ratios of initial
  A more detailed picture about the relative performances is                                                                                                                                  budget result even in better performance than small initial ratios.
provided in Fig. 5. Here, we plot the median (over all 24 BBOB                                                                                                                                An extreme case is function 12 in dimension d = 2. Its situation is
                                                                                                                                                                                          5

GECCO ’20, July 8–12, 2020, CancÃžn, Mexico                                                                                                                                                                                            Jakob Bossek, Carola Doerr, and Pascal Kerschke

                                                              Median relative performance of R(f, d, n, k, s)
                                                                                                                                                1.0                           2.0                          3.0                            4.0                            5.0+

                                 0.1                           0.2                       0.3                          0.4                             0.5                        0.6                         0.7                            0.8                            0.9                             1.0
                 16    2.6    2.9   2.4    3.3       2.1   2.4   2.5   3.6       2.4   3.4   3.5   3.1       2.5    3.1   3.5   3.1       2.7    4.1    3.3   3.3       3.5    3.3   4.4   4.0       4.3    5.2 6.5 4.4           4.2 4.4 4.2 5.6                4.4    7.5    6.8   11.1         4.2 5.1 9.2 4.0
                 32    2.2    2.4   2.6    2.8       3.3   2.8   3.4   3.2       3.2   3.5   3.6   3.5       3.6    3.5   4.0   4.1       4.9    4.0    5.0   4.4       6.0    4.3   4.7   3.3       7.6   6.5 4.2 4.4           10.3 7.2 6.4 7.5               11.3    7.4    8.7    8.2        14.1 8.7 15.1 10.6
                 64    1.8    2.7   2.2    2.1       2.2   2.3   2.6   3.3       3.8   3.4   3.0   3.2       5.7    4.6   4.0   4.5       5.3    5.8    7.4   4.2       4.4    8.4   7.0   6.3       4.6   8.5 10.4 11.0          7.6 15.0 9.2 11.6             19.8   29.6   23.9   18.1        28.3 23.9 38.7 19.7
                128    1.9    2.2   2.4    3.0       2.7   3.0   2.7   2.7       2.2   3.3   3.4   3.4       2.7    3.2   3.9   3.7       4.2    4.6    4.1   4.4       5.1    5.2   4.8   5.2       6.1   6.4 5.7 6.5           14.6 8.8 13.1 10.0             14.3   18.8   12.1   14.6        22.7 43.5 27.9 47.2      2
                256    2.1    2.3   2.3    2.4       3.6   2.7   2.9   2.9       4.8   3.8   3.9   3.7       4.3    4.1   4.2   3.8       6.1    4.7    4.7   4.8       5.4    5.4   6.0   5.4       4.4    6.8 6.7 7.4           7.2 8.9 10.6 10.3             16.3   18.5   20.3   25.4        70.1 93.7 79.2 103.1
                512    1.8    2.2   2.3    2.1       3.4   2.2   2.7   3.0       2.5   3.2   3.1   3.2       3.0    3.6   3.9   3.3       2.9    5.2    5.0   5.6       6.0    7.7   6.3   6.6       5.7   10.0 8.4 8.3           8.9 13.7 13.5 10.2            16.2   25.4   14.8   33.2       168.3 142.2 130.9 201.9
               Total   2.0    2.3   2.3    2.5       2.6   2.5   2.7   3.2       2.7   3.5   3.2   3.5       3.4    3.6   3.9   3.8       4.3    4.7    4.4   4.4       5.1    5.5   5.6   5.1       5.7   6.8 7.0 6.8            7.3 8.9 8.4 9.5               10.8   14.8   12.5   13.6        21.8 27.0 33.5 24.9

                 16    2.5    2.2   2.3    2.8       1.9   1.9   2.1   2.3       1.9   2.6   3.7   2.3       3.1    2.0   1.8   3.3       2.7    3.3    2.4   3.3       2.8    2.7   2.1   2.7       2.6   3.8   3.3   3.6       2.7    2.9     3.5   3.6       5.8    5.5    4.8     8.1        4.6 4.8 4.3 6.1
                 32    1.9    1.9   1.7    1.9       2.0   2.2   2.0   2.2       2.4   2.2   2.5   2.1       2.2    3.1   2.0   3.2       2.0    2.7    3.7   2.9       2.7    3.9   3.5   4.5       3.1   4.5   3.6   3.7       5.2    3.1     3.9   4.0       9.7    3.4    4.8     6.2        7.1 7.3 8.3 6.0
                 64    1.4    1.5   1.4    1.7       1.4   1.6   1.6   1.8       1.9   2.1   2.0   1.9       2.0    2.1   2.1   2.9       2.5    2.8    2.6   3.1       3.3    3.2   3.6   3.3       5.3   3.4   3.2   4.3       4.3    3.8     5.1   5.4       4.3    5.1    5.6     4.3       10.2 5.5 4.9 6.4
                128    1.5    1.5   1.5    1.7       1.8   1.8   1.7   1.7       1.7   1.9   2.1   2.1       1.8    2.2   2.5   2.7       2.9    2.8    2.9   3.3       2.6    3.1   3.2   2.5       3.5   2.5   3.0   4.0       4.1    4.7     4.1   4.6       6.8    6.2    6.9     5.6        9.2 22.0 25.7 18.8       3
                256    1.6    1.6   1.5    1.5       2.0   1.9   1.7   2.1       2.1   2.4   2.5   2.5       2.3    2.9   2.3   2.7       3.7    3.0    3.2   3.3       3.0    3.8   4.2   4.2       4.3   5.3   4.8   4.2       4.5    6.1     5.2   8.0       5.8    8.5    7.9    13.1       31.3 42.3 36.0 43.9
                512    1.7    1.6   1.7    1.5       1.7   1.8   2.0   2.0       2.2   2.3   2.4   2.2       2.6    2.9   2.4   2.5       2.6    3.1    2.8   3.1       2.6    2.7   3.2   3.6       3.0   3.9   3.8   4.5       4.5    4.3     4.7   5.0       4.6    5.9    5.7     6.2       44.5 48.8 55.0 36.4
               Total   1.7    1.6   1.6    1.8       1.8   1.8   1.8   2.1       2.0   2.2   2.3   2.2       2.3    2.4   2.2   2.8       2.7    2.9    2.7   3.2       3.0    3.2   3.2   3.4       3.5   3.8   3.6   4.1       4.3    3.7     4.5   4.8       5.8    5.6    6.1     6.8        8.7 9.7 9.8 9.7

                 16    2.2    1.9   1.8    2.0       1.8   2.0   2.1   1.8       1.8   2.4   2.0   1.8       2.1    2.1   2.0   1.8       2.4    2.2    1.6   2.0       2.1    2.0   2.3   2.2       2.2   3.3   2.5   2.7       2.1    2.9     3.6   3.6       2.3    4.8    3.4    4.8         2.4 4.0 4.2 2.3
                 32    1.6    1.5   1.4    1.5       1.6   1.6   1.4   1.3       1.8   1.6   1.4   1.5       1.5    1.6   1.8   1.6       1.7    2.0    2.1   1.8       2.2    1.9   2.7   2.3       1.9   2.3   1.7   1.8       2.4    2.1     2.8   2.6       2.7    3.5    3.1    2.8         5.5 6.8 3.7 6.9
                 64    1.5    1.6   1.9    1.5       1.5   1.6   2.0   1.7       2.0   1.9   2.1   2.2       2.3    2.2   2.2   2.1       2.2    2.2    2.3   2.8       2.3    2.8   3.0   3.1       3.0   3.2   3.5   3.3       3.5    5.0     3.4   3.4       4.7    4.5    4.6    4.1         6.1 7.9 8.1 9.7
                128    1.3    1.5   1.7    1.4       1.5   1.7   1.7   1.8       1.9   2.0   2.0   2.0       1.9    2.0   2.4   2.5       2.1    2.7    2.5   2.8       2.6    3.1   3.5   2.9       2.4   3.4   3.1   3.4       4.6    4.5     4.2   4.9       5.7    5.1    6.4    5.3        12.8 10.0 10.1 9.2        4

Total budget
                256    1.4    1.5   1.5    1.5       1.7   1.7   1.6   1.8       1.9   1.7   2.0   2.1       1.8    2.1   1.8   2.0       2.0    2.2    2.3   2.1       2.6    2.2   2.9   2.6       3.3   3.0   3.2   2.8       4.0    3.3     3.3   3.5       5.5    4.2    5.6    4.3        12.7 13.6 7.7 9.0
                512    1.5    1.3   1.3    1.3       1.6   1.7   1.7   1.6       2.0   2.0   2.1   1.8       1.8    1.9   2.0   2.2       2.1    2.5    2.4   2.5       2.8    2.6   2.7   3.1       4.1   3.1   3.1   3.0       3.9    4.2     3.7   3.7       5.0    6.1    4.9    4.5        20.5 16.7 17.9 17.2
               Total   1.5    1.5   1.6    1.5       1.6   1.7   1.8   1.7       1.9   1.9   2.0   1.9       2.0    2.0   2.0   2.0       2.1    2.3    2.2   2.3       2.5    2.5   2.7   2.6       2.6   3.1   2.8   3.0       2.9    3.4     3.4   3.6       4.0    4.4    4.4    4.2         6.8 7.8 6.7 7.1

                 16    1.4    1.8   1.7    1.6       1.8   1.7   1.8   1.8       1.7   1.9   1.6   1.6       1.8    1.6   1.6   1.4       2.0    1.8    1.7   1.8       1.9    1.9   1.8   2.1       2.3   1.9   2.1   2.5       2.1    2.4     2.4   2.5       2.7    1.9    2.5    2.6         2.5   2.1   2.4   2.3
                 32    1.7    1.5   1.6    1.6       1.5   1.6   1.4   1.3       1.8   1.6   1.8   1.7       1.6    1.6   1.8   1.7       1.9    2.0    1.6   2.0       1.8    1.7   1.9   1.8       1.9   2.4   1.9   2.5       2.1    2.4     2.5   2.4       2.7    2.3    2.8    2.7         3.9   4.4   4.1   3.6
                 64    1.5    1.5   1.5    1.5       1.7   1.7   1.8   1.8       1.8   1.9   1.9   1.9       1.9    1.7   1.8   1.8       2.1    1.9    2.1   2.2       2.4    2.3   2.3   2.0       2.1   2.6   2.1   2.5       2.8    3.2     2.6   3.1       2.9    3.7    4.0    3.8         4.3   4.1   6.4   5.8
                128    1.4    1.4   1.4    1.3       1.5   1.4   1.5   1.4       1.6   1.5   1.5   1.6       2.0    1.6   1.6   1.7       1.7    1.6    1.9   1.7       1.8    1.8   1.9   2.1       2.0   2.1   2.1   2.5       2.3    2.5     2.5   2.5       3.0    3.5    2.8    2.7         5.9   5.6   5.9   8.2    5
                256    1.3    1.4   1.5    1.4       1.7   1.4   1.4   1.5       1.9   1.6   1.7   1.8       1.9    1.7   1.6   2.0       2.1    1.8    1.9   2.0       2.0    1.9   2.3   2.2       2.4   2.3   2.9   2.4       2.7    2.9     2.8   3.2       2.8    3.2    3.1    3.5         6.8   7.8   5.4   6.4
                512    1.6    1.5   1.5    1.3       1.7   1.7   1.5   1.8       2.0   1.6   1.8   2.0       2.7    1.9   2.1   2.0       2.0    2.1    2.1   2.4       2.3    2.7   2.6   2.5       3.1   3.1   2.3   2.3       3.5    2.9     3.0   3.1       3.8    3.1    3.2    3.5         5.7   5.5   7.0   7.6
               Total   1.4    1.5   1.5    1.4       1.6   1.6   1.5   1.6       1.8   1.6   1.7   1.8       1.9    1.7   1.7   1.8       2.0    1.9    1.9   2.0       2.0    2.0   2.0   2.1       2.3   2.3   2.2   2.5       2.4    2.6     2.6   2.8       3.0    3.0    3.0    3.0         4.5   4.4   4.4   5.2

                 16    1.6    1.6   1.4    1.5       1.5   1.4   1.4   1.6       1.6   1.5   1.5   1.3       1.6    1.4   1.4   1.5       1.4    1.4    1.5   1.5       1.4    1.4   1.6   1.6       1.5   1.8   1.8   1.4       1.4    1.7     1.5   1.6       1.7    2.0    1.9    1.7         1.7   1.9   2.0   1.7
                 32    1.3    1.4   1.3    1.5       1.4   1.5   1.4   1.3       1.4   1.3   1.4   1.4       1.4    1.5   1.5   1.5       1.5    1.5    1.5   1.5       2.0    1.6   1.7   1.5       1.7   1.7   2.0   1.9       2.4    1.8     2.3   1.9       2.6    2.4    2.5    2.5         2.8   3.0   2.7   2.5
                 64    1.5    1.4   1.5    1.6       1.4   1.5   1.4   1.5       1.9   1.5   1.6   1.4       1.7    1.5   1.6   1.5       1.9    1.5    1.6   1.7       1.9    1.9   1.8   1.7       2.0   2.0   1.9   1.9       2.4    2.0     2.4   2.3       3.5    3.3    3.2    3.3         4.1   3.5   3.4   3.8
                128    1.1    1.3   1.2    1.3       1.3   1.4   1.3   1.2       1.5   1.6   1.5   1.4       1.4    1.3   1.4   1.3       1.4    1.5    1.4   1.5       1.6    1.5   1.6   1.6       1.8   1.6   1.6   1.6       1.8    2.3     2.0   1.8       2.1    2.4    2.2    2.9         2.7   3.7   3.3   2.8    10
                256    1.1    1.2   1.2    1.3       1.2   1.2   1.4   1.2       1.4   1.3   1.3   1.3       1.4    1.4   1.3   1.5       1.5    1.4    1.4   1.5       1.5    1.5   1.5   1.5       1.6   1.8   1.6   1.8       2.1    1.9     2.1   2.0       2.1    2.7    2.2    2.5         3.6   3.4   3.9   3.4
                512    1.3    1.2   1.2    1.2       1.2   1.5   1.4   1.3       1.3   1.4   1.4   1.5       1.3    1.4   1.4   1.4       1.5    1.5    1.4   1.5       1.6    1.5   1.5   1.6       2.1   1.6   1.6   1.6       2.2    2.0     2.0   1.8       2.2    2.5    2.6    2.3         3.5   4.1   4.0   4.2
               Total   1.3    1.4   1.3    1.4       1.4   1.4   1.4   1.4       1.5   1.4   1.5   1.4       1.5    1.4   1.4   1.5       1.5    1.5    1.5   1.5       1.6    1.6   1.6   1.6       1.8   1.8   1.8   1.7       2.0    1.9     2.2   1.8       2.2    2.3    2.3    2.4         3.0   3.0   3.0   3.0

                 16    2.0    1.9   1.8    2.2       1.8   1.8   2.0   2.0       1.8   2.0   2.0   1.8       2.2    1.9   1.9   1.9       2.2    2.1    2.1   1.9       2.2    2.0   2.2   2.2       2.5   2.7   2.5   2.6       2.4    2.7     2.6   2.9       3.2    3.3    3.1    3.4         2.9 3.1 3.1 2.5
                 32    1.6    1.7   1.6    1.8       1.6   1.7   1.6   1.7       1.9   1.8   1.9   1.9       1.8    1.9   1.9   1.9       1.9    2.0    2.0   1.9       2.5    2.1   2.4   2.2       2.4   2.4   2.3   2.7       3.0    2.9     2.9   2.9       4.0    3.3    3.5    3.3         5.6 4.8 4.3 3.9
                 64    1.5    1.6   1.6    1.7       1.6   1.7   1.8   1.8       2.0   1.9   2.2   2.0       2.2    2.0   2.1   2.1       2.4    2.2    2.3   2.7       2.7    2.7   2.7   2.9       2.8   3.0   2.8   3.3       3.4    3.7     3.4   3.4       4.3    4.5    4.6    4.3         6.3 5.3 6.0 6.3
                128    1.4    1.4   1.5    1.5       1.6   1.6   1.7   1.6       1.7   1.7   1.8   1.9       2.0    1.9   2.0   2.2       2.2    2.4    2.3   2.4       2.2    2.5   2.7   2.4       3.0   2.9   3.0   3.2       3.4    3.3     3.7   3.7       4.3    4.7    4.1    4.4         8.0 7.6 7.9 8.6          Total
                256    1.5    1.5   1.5    1.5       1.7   1.7   1.7   1.9       2.0   1.9   2.1   2.1       2.1    2.4   2.1   2.1       2.4    2.4    2.4   2.4       2.5    2.7   3.1   3.2       3.0   3.2   3.3   3.2       3.6    4.1     3.7   3.8       4.9    4.9    4.8    5.1        10.1 11.7 8.8 12.4
                512    1.5    1.5   1.5    1.4       1.7   1.7   1.8   1.7       2.0   2.0   2.1   2.0       2.0    2.1   2.2   2.2       2.4    2.3    2.4   2.5       2.6    2.7   2.6   2.9       3.2   3.4   2.9   2.9       3.7    4.0     3.4   3.8       4.5    4.9    5.2    4.9        13.5 15.4 14.0 15.6
               Total   1.6    1.6   1.6    1.6       1.7   1.7   1.8   1.8       1.9   2.0   2.0   1.9       2.0    2.0   2.0   2.1       2.2    2.2    2.3   2.2       2.5    2.4   2.6   2.5       2.8   2.9   2.8   3.0       3.2    3.3     3.2   3.4       4.2    4.1    4.1    4.3         5.9 5.8 5.8 6.0

                   al
                       n     S   So              al
                                                     n     S   So            al      LH  n
                                                                                                         al      LH  n
                                                                                                                                      al      LH  n
                                                                                                                                                                    al      LH  n
                                                                                                                                                                                                 al      LH  n
                                                                                                                                                                                                                             al
                                                                                                                                                                                                                                 n      S  So               al
                                                                                                                                                                                                                                                                n      S  So                al
                                                                                                                                                                                                                                                                                                n      S  So
                     to    LH   U bol              to    LH   U bol            to So     S                 to So     S                  to So     S                   to So     S                  to So     S                 to    LH   U bol               to    LH   U bol                to    LH   U bol
                  H              ni fo           H             ni fo         H   U bol
                                                                                  ni                     H   U bol
                                                                                                              ni                      H   U bol
                                                                                                                                           ni                       H   U bol
                                                                                                                                                                         ni                      H   U bol
                                                                                                                                                                                                      ni                     H             ni fo            H             ni fo             H             ni fo
                                       rm                            rm              fo rm                       fo rm                        fo rm                         fo rm                        fo rm                                   rm                             rm                              rm
                                                                                                                                                       Sampling design

Figure 5: Median (over all 24 BBOB functions) relative performance of R(f , d, n, k, s), by dimension and budget (rows) and
strategy (k, s) (columns).

as follows. The VBS is defined by the (30%, Halton) strategy. The                                                                                                       whether one should rather start two shorter runs of budget n/2
differences between the Halton designs with k > 30% are rather                                                                                                          each, or four runs of budget n/4, etc.
small, whereas for the other strategies smaller initial budgets are
preferable. By studying the absolute values in more detail, we find                                                                                                        Distribution of the Target Precisions. Crucial for the consideration
that the Halton strategy identifies a point with absolute target                                                                                                        of restarts are the distributions of the function values (or, equivaprecision 24.9 when k ≥ 30%. SMBO does not manage to find a                                                                                                             lently, the distributions of the target precisions) achieved by the
better point in any of its 128 − ⌈30% · 128⌉ = 89 adaptive evaluations.                                                                                                 different strategies (k, s). For reasons of space, we cannot go in
The best median target precision of any of the other strategies                                                                                                         much detail here, but Fig. 8 demonstrates how these boxplots look
has target precision 58.6 – achieved by the (10%, LHS) strategy.                                                                                                        like. Note that this figure is for one specific combination of function
Looking further into the results of the 800 individual runs, we find                                                                                                    (f = 17), dimension (d = 5) and budget (n = 128). It aggregates the
that 126 of them find a point of target precision smaller than 24.9.                                                                                                    target precision of all 40 strategies, i.e., of 800 runs in total. Our data
The distribution of their initial ratios is not unanimous, as can be                                                                                                    base contains one such plot for each of the 720 (f , d, n) problems.
see in the following table, which counts how often each initial ratio                                                                                                      Note that the dispersion of Halton designs are smaller, but this is
k appears among these 126 runs. These results show how difficult                                                                                                        due to the fact that we do not perform resampling for this sequence.
it is to give a general advice for the optimization of this function –                                                                                                  For all pairs of (k,Halton) strategies with k ≥ 50% the target precieven when the budget is fixed and the function ID known.                                                                                                                sion of the best initial design point is slightly above 3. For k ≥ 80%
                                                                                                                                                                        none of the SMBO runs starting in this best initial design point
                   k         0.1          0.2        0.3         0.4         0.5        0.6        0.7             0.8      0.9            1                            finds a solution of better target precision. For k ∈ {60%, 70%}, only
                   #         14           12          8          13          21          7         12              14       10            15                            one of the five runs each finds a better solution. Note that the length
                                                                                                                                                                        of each of these SMBO runs is n − ⌈k · n⌉, which for k = 0.6 corre-
                                                                                                                                                                        sponds to 51 adaptive SMBO steps. Such detailed information could
6                 RESTARTS VS. LONG RUNS                                                                                                                                be very useful to identify weaknesses of the EGO approach,
In the previous paragraph, we have started to look into the dis-                                                                                                        and, hopefully, contribute towards better SMBO designs.
tribution of the target precisions. We now demonstrate how such
information can be used to study whether it is beneficial to use                                                                                                           Computing median target precision of restarting SMBO. To investhe total budget of n function evaluations for a single long run, or                                                                                                    tigate if, for a given problem (f , d, n), a restart strategy is beneficial
                                                                                                                                                               6

Effects of Initial Design Strategies on SMBO Performance                                                                                                                                GECCO ’20, July 8–12, 2020, CancÃžn, Mexico

                                                                       Relative approximation error R(f, d, n, k, s)
                                                                                                                         1.0                1.5               2.0              2.5               3.0+

                                              16                                     32                             64                                  128                             256                                512
                       1.0

                                                                                                                                                                                                                                                 Halton
                       0.7
                       0.4
                       0.1
                       1.0

Initial design ratio
                                                                                                                                                                                                                                                 LHS
                       0.7
                       0.4
                       0.1
                       1.0

                                                                                                                                                                                                                                                 Sobol
                       0.7
                       0.4
                       0.1
                       1.0

                                                                                                                                                                                                                                                 Uniform
                       0.7
                       0.4
                       0.1
                             m 2 11
                                 13
                                 15
                                 17
                                 19
                                 21
                              ed 3
                                ia 1
                                   3
                                   5
                                   7
                                   9
                                   n
                                   1
                                   3
                                   5
                                   7
                             m 2 11
                                 13
                                 15
                                 17
                                 19
                                 21
                              ed 3
                                ia
                                 11
                                 13
                                 15
                                 179
                                   n
                                   1
                                   3
                                   5
                                   7
                                   9
                             m 2 19
                                 21
                              ed 3
                             m 2ia
                                 11
                                 13
                                 15
                                 17
                                 19
                                 21
                              ed 3
                                ia n
                                   1
                                   3
                                   5
                                   7
                                   9
                                   n
                             m 2 11
                                 13
                                 15
                                 17
                                 19
                                 21
                              ed 3
                                ia 1
                                   3
                                   5
                                   7
                                   9
                                   n
                                   1
                                   3
                                   5
                                   7
                             m 2 11
                                 13
                                 15
                                 17
                                 19
                                 21
                              ed 3
                                ia 9
                                   n
                                                                                                                               BBOB function

Figure 6: Heatmap visualization of relative performances R(f , d, n, k, s) by function, total budget, and strategy (k, s) for fixed
dimension d = 5. Values are capped at 3.
                                                                       Relative approximation error R(f, d, n, k, s)
                                                                                                                         1.0                1.5               2.0              2.5               3.0+

                                                   2                                         3                                          4                                      5                                      10
                       1.0

                                                                                                                                                                                                                                                 Halton
                       0.7
                       0.4
                       0.1
                       1.0

Initial design ratio
                                                                                                                                                                                                                                                 LHS
                       0.7
                       0.4
                       0.1
                       1.0

                                                                                                                                                                                                                                                 Sobol
                       0.7
                       0.4
                       0.1
                       1.0

                                                                                                                                                                                                                                                 Uniform
                       0.7
                       0.4
                       0.1
                             1   3   5   7    9
                                             11        15    19   m 23       3   5   7     9
                                                                                          11     15   19    m 23       3   5   7    9
                                                                                                                                   11       15    19   m 23       3   5   7    9
                                                                                                                                                                              11   15   19    m 23       3   5   7    9
                                                                                                                                                                                                                     11   15     19        21
                                             13        17    21    ed ia                  13     17   21     ed ia                 13       17    21    ed ia                 13   17   21     ed ia                 13   17
                                                                                                                                                                                                                                      m 23
                                                                         n                                         n                                          n                                      n                                 ed ia
                                                                         1                                         1                                          1                                      1                                       n
                                                                                                                               BBOB function

Figure 7: Heatmap visualization of the relative performance R(f , d, n, k, s) by dimension, function, and design type for a fixed
total budget of n = 128 function evaluations. Values are capped at 3.

                                 Sampling design                    Halton            LHS         Sobol         Uniform                     over a single long run, we need to extend our previous focus on
                                                                                                                                            median target precision to different percentiles. To this end, let
                                                                             17
                                                                                                                                            Pq (f , d, n, k, s) := Pq ({p(f , d, n, k, s, r i , r A ) | r i ∈ Ri (s), r A ∈ [5]}) ,
                       6                                                                                                                    the q-th percentile of the target precisions achieved by strategy (k, s)

 Target precision
                                                                                                                                            on problem (f , d, n) across all 5 (Halton) or 25 (Sobol’, LHS, uniform
                                                                                                                                            designs) runs, respectively. For a fair comparison of one run of the
                       4
                                                                                                                                            full budget n with two runs of budget n/2 (of the same strategy), we
                                                                                                                                            compare the p   median M(f , d, n, k, s) (i.e., the 50-th percentile) with
                       2                                                                                                                    the q := 1 − 1/2-th percentile Pq (f , d, n/2, k, s). With this value
                                                                                                                                            of q, the probability that (at least) one of the two runs achieves a
                                                                                                                                            target precision that is at least as good as Pq (f , d, n/2, k, s) equals
                       0
                                 0.1         0.2       0.3    0.4      0.5           0.6    0.7       0.8     0.9        1.0                1 − (1 − q)2 = 1/2. This is identical to the probability that one
                                                                  Initial design ratio                                                      long run achieves a target precision that is at least as good as
                                                                                                                                            M(f , d, n, k, s). Note that we disregard a small bias in our data,
Figure      8:       Boxplots         of    the   target precisions                                                                         which results from the fact that we do not have 25 completely
p(17, 5, 128, k, s, r i , r A )-values for function f = 17, dimen-                                                                          independent runs. Instead, we use the same initial design sample
sion 5, and total budget n = 128, grouped by initial budget                                                                                 for five independent SMBO runs each – but, we ignore this effect in
ratio k and design s.                                                                                                                       the following computations. Also, given the small number of runs,
                                                                                                                                            all numbers should be taken with care – the smaller the percentile,
                                                                                                                                    7

GECCO ’20, July 8–12, 2020, CancÃžn, Mexico                                                                                       Jakob Bossek, Carola Doerr, and Pascal Kerschke

the larger the uncertainty around the values. We nevertheless show                                    Logarithmic percentile ratio
                                                                                                                                        0.0        0.5          1.0        1.5          2.0+
this example to demonstrate how one could systematically address
the question how to split a given budget into possibly parallel runs.                                       1                       2                      3                       4

   Fig. 9 illustrates an example for the relevant percentiles when                         0.50 4.1 2.4 1.0 0.6 0.5 0.4 4.6 3.1 2.7 2.0 1.1 0.6 5.1 5.1 4.5 1.5 0.7 0.3 4.8 4.4 3.8 2.4 1.5 0.6
                                                                                           0.29 3.8 2.2 0.8 0.4 0.3 0.2 4.1 2.7 2.4 1.8 1.0 0.3 5.0 4.9 4.1 0.9 0.3 0.1 4.7 4.2 3.5 1.8 1.1 0.4
comparing one long run of budget n with two short ones of budget                           0.16 3.5 1.7 0.6 0.1 0.1 0.0 3.8 2.5 1.9 1.3 0.9 0.0 4.9 4.8 4.0 0.8 0.1 0.0 4.6 4.1 3.3 1.6 0.9 0.0
n/2, and four even shorter ones of budget n/4. More precisely, we                                           5                       6                      7                       8
                                                                                           0.50 0.5 0.4 0.4 0.3 0.2 0.4 2.5 2.4 2.0 2.3 1.0 0.2 7.3 7.3 6.3 5.3 0.9 0.7 4.1 3.2 2.6 2.0 1.3 0.5
fix in this figure the strategy to (10%,LHS) and the dimension to                          0.29 0.4 0.4 0.2 0.2 0.1 0.3 2.3 1.9 1.8 2.2 0.7 0.1 7.3 7.1 5.9 5.3 0.5 0.6 3.6 3.1 2.5 1.6 1.1 0.2
d = 5, and we show log-scaled relative data. Each 3 × 6 box cor-                           0.16 0.3 0.2 0.0 0.0 0.0 0.2 2.3 1.9 1.4 1.8 0.3 0.0 7.1 6.3 5.9 1.1 0.1 0.0 3.1 3.0 2.2 1.3 0.6 0.0
                                                                                                            9                      10                      11                      12
responds to one of the 24 BBOB functions. As we scale the values
                                                                                           0.50 4.0 2.9 2.7 1.9 1.6 0.7 3.5 2.8 1.7 1.4 0.7 0.5 3.9 2.9 2.4 1.6 1.2 0.8 3.5 2.5 1.7 1.1 0.7 0.4
within a box by its VBS, and afterwards show the percentile ratios                         0.29 3.6 2.7 2.4 1.6 1.4 0.3 3.3 2.5 1.5 1.2 0.5 0.1 3.7 2.8 1.8 1.2 1.0 0.3 3.3 2.1 1.3 0.6 0.0 0.3

                                                                                Quantile
                                                                                           0.16 3.4 2.4 2.3 1.4 1.0 0.0 3.0 1.9 1.3 0.8 0.2 0.0 3.5 2.5 1.4 1.0 0.8 0.0 2.5 1.6 1.3 0.4 0.0 0.2
on a log-scale, the field with value 0.0 represents the combination
                                                                                                           13                      14                      15                      16
achieving the best target precision (i.e., the VBS) among the dis-                         0.50 2.6 2.5 1.9 1.3 0.8 0.5 3.4 2.4 2.0 1.4 0.8 0.5 2.5 2.2 2.2 2.1 1.8 1.1 4.4 3.7 2.9 2.8 2.2 1.2
played combinations.      Not surprisingly, for most functions this is                     0.29 2.6 2.1 1.7 1.2 0.7 0.0 3.3 2.2 1.7 1.0 0.4 0.1 2.3 2.1 2.1 2.0 1.7 0.5 3.8 3.4 2.5 2.5 1.8 0.7
                                                                                           0.16 2.2 1.9 1.5 0.8 0.5 0.0 2.9 2.0 1.3 0.8 0.1 0.0 2.1 1.5 2.1 1.9 1.6 0.0 3.5 3.3 2.2 2.2 1.7 0.0
the (1 − 4 1/2)-th percentile of the full budget n = 512. Let P f∗ be the
         p
                                                                                                           17                      18                      19                      20

target precision of this (percentile, budget) combination for a given                      0.50 1.2 0.6 0.7 0.4 0.5 0.3 1.0 0.9 0.3 0.4 0.3 0.2 1.7 1.5 1.2 1.0 0.7 0.4 3.2 3.2 3.0 2.8 2.7 0.5
                                                                                           0.29 0.9 0.4 0.5 0.2 0.3 0.1 1.0 0.7 0.2 0.4 0.2 0.1 1.7 1.2 0.9 0.9 0.4 0.1 3.1 3.0 3.0 2.8 2.5 0.1
function f . A value φ in field (q, n) is then to be read as follows:                      0.16 0.7 0.4 0.2 0.1 0.1 0.0 0.9 0.6 0.0 0.2 0.1 0.0 1.6 1.0 0.7 0.6 0.2 0.0 2.6 2.9 2.8 2.7 2.0 0.0
the target precision Pq (n) := Pq (f , d = 5, n, 10%, LHS) satisfies                                       21                      22                      23                      24

Pq (n) = 10φ · P f∗ . Smaller values are therefore better. We see that                     0.50 8.1 7.3 6.1 3.2 3.1 0.9 6.7 6.3 6.1 5.1 2.7 0.8 1.0 0.7 0.6 0.5 0.5 0.2 0.6 0.6 0.5 0.4 0.3 0.2
                                                                                           0.29 7.9 6.8 5.5 2.1 2.4 0.3 6.4 5.1 5.3 4.3 1.0 0.2 0.9 0.6 0.6 0.4 0.3 0.1 0.6 0.5 0.4 0.2 0.2 0.2
                                                                                           0.16 7.0 6.2 5.2 1.6 1.5 0.0 6.1 4.5 4.6 3.7 0.4 0.0 0.7 0.5 0.5 0.3 0.1 0.0 0.6 0.4 0.3 0.1 0.1 0.0
for f = 5,for example, our data suggests that a total budget of 512                              16                      16                      16                      16
                                                                                                 32                      32                      32                      32
evaluations (value 0.4 when used as single run) is better used for
                                                                                                 64
                                                                                                12
                                                                                                25 8
                                                                                                   6                     64
                                                                                                                        12
                                                                                                                        25 8
                                                                                                                           6                     64
                                                                                                                                                12
                                                                                                                                                25 8
                                                                                                                                                   6                     64
                                                                                                                                                                        12
                                                                                                                                                                        25 8
                                                                                                                                                                           6
                                                                                                51 2                    51 2                    51 2                    51 2
                                                                                                                                        Total budget
four runs of budget 128 each (value 0.1). We have marked in this
matrix all fields for which the long run compares unfavorably with
a restart strategy – the one corresponding to the neighboring field             Figure 9: Percentiles Pq (f , d, n, k, s) of target precisions across
on the lower left diagonal. Overall, we see that several such cases             the 25 SMBO runs per function and dimension using an LHS
exist, which confirms our previous finding that EGO does not                    design with 10% initial budget and for the 2-dimensional
always compare favorably against quasi-random sampling.                         problems. The percentiles are scaled by the respective func-
                                                                                tion’s best percentile, and the resulting ratios are shown on a
                                                                                capped log10-scale. Red boxes indicate that the correspond-
7    CONCLUSIONS                                                                ing strategy performs unfavorably against a restart strategy
In this paper we have presented a database for data-driven investiga-           (the one to the lower left).
tions of the sequential model-based optimization (SMBO) strategy
EGO [30]. The focus of our work is on analyzing the influence of
the (size and type of) initial design on the overall performance of                Typically, the budget of common SMBO applications is too small
EGO. Our data base contains data for 720 different problems, which              for a classical a priori (i.e., offline) landscape-aware selection of the
are evaluated against a total of 40 different initial design strategies.        optimizer design based on supervised learning approaches (see [32]
   While we clearly observed that small initial designs are prefer-             for a survey). However, in case high-level properties – such as the
able at a high-level view, we also found that each of the 40 con-               degree of (multi-)modality or the sizes of the problem’s attraction
sidered combinations of design type and size achieved best per-                 basins – are known for the problem at hand, or can be guessed by
formance on at least one of the 720 problems. Our findings thus                 an expert, selecting a suitable initial design strategy is feasible.
confirm that an automated strategy selection method – like the                     Finally, we have seen that the performance of the different deproof-of-concept approach presented in [47] – might indeed be                   signs was often quite comparable. To investigate the differences
profitable. Moreover, we even identified cases in which the usage of            in more detail, we suggest to consider the different strategies as a
EGO does not provide any benefits over the initial (quasi-)random               portfolio of different algorithms. With this viewpoint, one could ansample – especially in case of highly multimodal problems.                      alyze the marginal contributions [51] or Shapley values [20] of the
   Our long-term vision are SMBO approaches that dynamically                    different designs, and leverage the information contained therein.
decide whether to take the next sample from a (quasi-)random distribution or whether to derive it from the surrogate model. Going               ACKNOWLEDGMENTS
one step further, we believe that an adaptive choice of the acquisi-            This work was supported by the Paris Ile-de-France Region and the
tion function, and possibly even of the solver used to optimize the             European Research Center for Information Systems (ERCIS).
latter, should bring substantial performance gains – in particular in
the case in which the total budget is known in advance. Hence, we               REFERENCES
need to “train” a final recommendation (last evaluation) instead of              [1] Thomas Bartz-Beielstein. 2010. SPOT: An R Package For Automatic and Interac-
                                                                                     tive Tuning of Optimization Algorithms by Sequential Parameter Optimization.
achieving good anytime performance. These two mentioned ques-                        CoRR abs/1006.4645 (2010). arXiv:1006.4645 http://arxiv.org/abs/1006.4645
tions fall under the umbrella of dynamic algorithm configuration,                [2] Thomas Bartz-Beielstein and Mike Preuss. 2006. Considerations of Budget Allocawhich has been an important driver for the field of evolutionary                     tion for Sequential Parameter Optimization (SPO). In Proc. Workshop on Empirical
                                                                                     Methods for the Analysis of Algorithms (EMAA’06). 35–40.
computation for the last decades [12, 16, 17, 31], and which has                 [3] Brian Beachkofski and Ramana Grandhi. 2002. Improved Distributed Hypercube
recently also gained interest in machine learning communities [5].                   Sampling. In 43rd AIAA/ASME/ASCE/AHS/ASC Structures, Structural Dynamics,
                                                                            8

Effects of Initial Design Strategies on SMBO Performance                                                                              GECCO ’20, July 8–12, 2020, CancÃžn, Mexico

     and Materials Conference. American Institute of Aeronautics and Astronautics.              [29] Donald R. Jones. 2001. A Taxonomy of Global Optimization Methods Based on
 [4] Nacim Belkhir, Johann Dréo, Pierre Savéant, and Marc Schoenauer. 2017. Per                      Response Surfaces. Journal of Global Optimization 21, 4 (01 Dec 2001), 345–383.
     instance algorithm configuration of CMA-ES with limited budget. In Proc. of                [30] Donald R. Jones, Matthias Schonlau, and William J. Welch. 1998. Efficient Global
     Genetic and Evolutionary Computation Conference (GECCO’17). ACM, 681–688.                       Optimization of Expensive Black-Box Functions. Journal of Global Optimization
 [5] André Biedenkapp, H. Furkan Bozkurt, Frank Hutter, and Marius Lindauer. 2019.                   13, 4 (1998), 455–492.
     Towards White-box Benchmarks for Algorithm Control. CoRR abs/1906.07644                    [31] Giorgos Karafotias, Mark Hoogendoorn, and Ágoston Endre Eiben. 2015. Parame-
     (2019). arXiv:1906.07644 http://arxiv.org/abs/1906.07644                                        ter Control in Evolutionary Algorithms: Trends and Challenges. IEEE Transactions
 [6] Bernd Bischl, Jakob Richter, Jakob Bossek, Daniel Horn, Janek Thomas, and Michel                on Evolutionary Computation 19 (2015), 167–187.
     Lang. 2016. mlrMBO: A Modular Framework for Model-Based Optimization of                    [32] Pascal Kerschke, Holger H. Hoos, Frank Neumann, and Heike Trautmann. 2019.
     Expensive Black-Box Functions. (2016). arXiv:stat/1703.03373 http://arxiv.org/                  Automated Algorithm Selection: Survey and Perspectives. Evolutionary Compu-
     abs/1703.03373                                                                                  tation 27, 1 (2019), 3–45.
 [7] Bernd Bischl, Simon Wessing, Nadja Bauer, Klaus Friedrichs, and Claus Weihs.               [33] Pascal Kerschke and Heike Trautmann. 2019. Automated Algorithm Selection on
     2014. MOI-MBO: Multiobjective Infill for Parallel Model-Based Optimization. In                  Continuous Black-Box Problems By Combining Exploratory Landscape Analysis
     Learning and Intelligent Optimization, Panos M. Pardalos, Mauricio G.C. Resende,                and Machine Learning. Evolutionary Computation (ECJ) 27, 1 (2019), 99 – 127.
     Chrysafis Vogiatzis, and Jose L. Walteros (Eds.). Springer International Publishing,       [34] Joshua Knowles. 2006. ParEGO: a hybrid algorithm with on-line landscape approx-
     Cham, 173–186.                                                                                  imation for expensive multiobjective optimization problems. IEEE Transactions
 [8] Jakob Bossek. 2017. smoof: Single-and Multi-Objective Optimization Test Func-                   on Evolutionary Computation 10 (2006), 50–66.
     tions. The R Journal 9, 1 (2017), 103–113. https://journal.r-project.org/archive/          [35] Lars Kotthoff, Chris Thornton, Holger H. Hoos, Frank Hutter, and Kevin Leyton-
     2017/RJ-2017-004/RJ-2017-004.pdf                                                                Brown. 2019. Auto-WEKA: Automatic Model Selection and Hyperparameter
 [9] Jakob Bossek. 2020. dandy: Designs and Discrepancy. https://github.com/                         Optimization in WEKA. In Automated Machine Learning - Methods, Systems,
     jakobbossek/dandy R package version 1.0.0.0000.                                                 Challenges. Springer, 81–95.
[10] Jakob Bossek. 2020. Public data repository with project data. https://github.              [36] Marius Lindauer, Matthias Feurer, Katharina Eggensperger, André Biedenkapp,
     com/jakobbossek/GECCO2020-smboinitial                                                           and Frank Hutter. 2019. Towards Assessing the Impact of Bayesian Optimization’s
[11] Dimo Brockhoff, Bernd Bischl, and Tobias Wagner. 2015. The Impact of Initial                    Own Hyperparameters. In IJCAI 2019 DSO Workshop.
     Designs on the Performance of MATSuMoTo on the Noiseless BBOB-2015 Testbed:                [37] Fernando G. Lobo, Cláudio F. Lima, and Zbigniew Michalewicz (Eds.). 2007. Pa-
     A Preliminary Study. In Proc. of Genetic and Evolutionary Computation Conference                rameter Setting in Evolutionary Algorithms. Studies in Computational Intelligence,
     (GECCO’15). ACM, 1159–1166.                                                                     Vol. 54. Springer.
[12] Edmund K. Burke, Michel Gendreau, Matthew R. Hyde, Graham Kendall, Gabriela                [38] Makoto Matsumoto and Takuji Nishimura. 1998. Mersenne Twister: A 623-
     Ochoa, Ender Özcan, and Rong Qu. 2013. Hyper-heuristics: a survey of the state                  Dimensionally Equidistributed Uniform Pseudo-Random Number Generator.
     of the art. JORS 64, 12 (2013), 1695–1724.                                                      ACM Trans. Model. Comput. Simul. 8, 1 (Jan. 1998), 3âĂŞ30.
[13] Rob Carnell. 2019. lhs: Latin Hypercube Samples. https://CRAN.R-project.org/               [39] Michael D. McKay, Richard J. Beckman, and William J. Conover. 1979. A Com-
     package=lhs R package version 1.0.1.                                                            parison of Three Methods for Selecting Values of Input Variables in the Analysis
[14] Dutang Christophe and Savicky Petr. 2019. randtoolbox: Generating and Testing                   of Output from a Computer Code. Technometrics 21 (1979), 239–245.
     Random Numbers. R package version 1.30.0.                                                  [40] Jonas Mockus (Ed.). 1989. Bayesian Approach to Global Optimization. Springer.
[15] Josef Dick and Friedrich Pillichshammer. 2010. Digital Nets and Sequences. Cam-            [41] Marius Tudor Morar, Joshua Knowles, and Sandra Sampaio. 2017. Initialization
     bridge University Press.                                                                        of Bayesian Optimization Viewed as Part of a Larger Algorithm Portfolio. In Proc.
[16] Benjamin Doerr and Carola Doerr. 2020. Theory of Parameter Control for Dis-                     of the international workshop in Data Science meets Optimization (DSO at CEC
     crete Black-Box Optimization: Provable Performance Gains Through Dynamic                        and CPAIOR 2017).
     Parameter Choices. In Theory of Evolutionary Computation: Recent Developments              [42] Juliane Mueller. 2014. MATSuMoTo: The MATLAB Surrogate Model Tool-
     in Discrete Optimization. Springer, 271–321.                                                    box For Computationally Expensive Black-Box Global Optimization Problems.
[17] Ágoston Endre Eiben, Robert Hinterding, and Zbigniew Michalewicz. 1999. Pa-                     arXiv:math.OC/1404.4261
     rameter control in evolutionary algorithms. IEEE Transactions on Evolutionary              [43] John Ashworth Nelder and Roger Mead. 1965. A Simplex Method for Function
     Computation 3 (1999), 124–141.                                                                  Minimization. Comput. J. 7 (1965), 308–313.
[18] Stefan Falkner, Aaron Klein, and Frank Hutter. 2018. BOHB: Robust and Efficient            [44] Art B. Owen. 1995. Randomly Permuted (t,m,s)-Nets and (t, s)-Sequences. In
     Hyperparameter Optimization at Scale. In ICML. 1436–1445.                                       Monte Carlo and Quasi-Monte Carlo Methods in Scientific Computing, Harald
[19] Henri Faure and Shu Tezuka. 2002. Another Random Scrambling of Digital (t,s)-                   Niederreiter and Peter Jau-Shyong Shiue (Eds.). Springer New York, New York,
     Sequences. In Monte Carlo and Quasi-Monte Carlo Methods 2000, Kai-Tai Fang,                     NY, 299–317.
     Harald Niederreiter, and Fred J. Hickernell (Eds.). Springer Berlin Heidelberg,            [45] R Core Team. 2018. R: A Language and Environment for Statistical Computing. R
     Berlin, Heidelberg, 242–256.                                                                    Foundation for Statistical Computing, Vienna, Austria. https://www.R-project.
[20] Alexandre Fréchette, Lars Kotthoff, Tomasz P. Michalak, Talal Rahwan, Holger H.                 org/
     Hoos, and Kevin Leyton-Brown. 2016. Using the Shapley Value to Analyze                     [46] Carl Edward Rasmussen and Christopher K. I. Williams (Eds.). 2006. Gaussian
     Algorithm Portfolios. In Proceedings of the Thirtieth AAAI Conference on Artificial             Processes for Machine Learning. The MIT Press.
     Intelligence, February 12-17, 2016, Phoenix, Arizona, USA. AAAI, 3397–3403.                [47] Bhupinder Singh Saini, Manuel López-Ibáñez, and Kaisa Miettinen. 2019. Au-
[21] John H. Halton. 1960. On the efficiency of certain quasi-random sequences of                    tomatic Surrogate Modelling Technique Selection Based on Features of Opti-
     points in evaluating multi-dimensional integrals. Numer. Math. 2 (1960), 84–90.                 mization Problems. In Proceedings of the Genetic and Evolutionary Computation
[22] Nikolaus Hansen, Anne Auger, Olaf Mersmann, Tea Tušar, and Dimo Brockhoff.                      Conference (GECCO) Companion. ACM, 1765 – 1772.
     2016. COCO: A Platform for Comparing Continuous Optimizers in a Black-Box                  [48] T.J. Santner, B.J. Williams, and W.I. Notz. 2003. The Design and Analysis of
     Setting. ArXiv e-prints arXiv:1603.08785 (2016).                                                Computer Experiments. Springer.
[23] Nikolaus Hansen, Steffen Finck, Raymond Ros, and Anne Auger. 2009. Real-                   [49] Bobak Shahriari, Kevin Swersky, Ziyu Wang, Ryan P. Adams, and Nando de Fre-
     Parameter Black-Box Optimization Benchmarking 2009: Noiseless Functions Defi-                   itas. 2016. Taking the Human Out of the Loop: A Review of Bayesian Optimization.
     nitions. Technical Report RR-6829. INRIA. https://hal.inria.fr/inria-00362633/                  Proc. IEEE 104, 1 (2016), 148–175.
     document                                                                                   [50] Ilya Meyerovich Sobol. 1967. On the distribution of points in a cube and the
[24] Nikolaus Hansen and Andreas Ostermeier. 2001. Completely Derandomized                           approximate evaluation of integrals. U. S. S. R. Comput. Math. and Math. Phys. 7,
     Self-Adaptation in Evolution Strategies. Evol. Computation 9, 2 (2001), 159–195.                4 (Jan. 1967), 86–112.
[25] Daniel Haziza, Jérémy Rapin, and Gabriel Synnaeve. 2020. HiPlot - High dimen-              [51] Lin Xu, Frank Hutter, Holger H. Hoos, and Kevin Leyton-Brown. 2012. Evaluating
     sional Interactive Plotting. https://github.com/facebookresearch/hiplot.                        Component Solver Contributions to Portfolio-Based Algorithm Selectors. In Proc.
[26] Marius Hofert and Christiane Lemieux. 2019. qrng: (Randomized) Quasi-Random                     of Theory and Applications of Satisfiability Testing (SAT’12) (Lecture Notes in
     Number Generators. https://CRAN.R-project.org/package=qrng R package                            Computer Science), Vol. 7317. Springer, 228–241.
     version 0.0-7.
[27] Daniel Horn, Tobias Wagner, Dirk Biermann, Claus Weihs, and Bernd Bischl. 2015.
     Model-Based Multi-objective Optimization: Taxonomy, Multi-Point Proposal,
     Toolbox and Benchmark. In Evolutionary Multi-Criterion Optimization. Springer
     International Publishing, Cham, 64–78.
[28] Frank Hutter, Holger H. Hoos, and Kevin Leyton-Brown. 2011. Sequential model-
     based optimization for general algorithm configuration. In LION. Springer, 507–
     523.

                                                                                            9
