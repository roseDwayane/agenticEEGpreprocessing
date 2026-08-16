---
citation_key: "LiuEtAl2019"
title: "An ADMM Based Framework for AutoML Pipeline Configuration"
authors: "Sijia Liu; Parikshit Ram; Deepak Vijaykeerthy; Djallel Bouneffouf; Gregory Bramble; Horst Samulowitz; Dakuo Wang; A. Conn; Alexander G. Gray"
year: 2019
doi: "10.1609/aaai.v34i04.5926"
source: "arXiv (1905.00424)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "1905.00424"
conversion: "pdftotext -layout (automated)"
---

# An ADMM Based Framework for AutoML Pipeline Configuration

An ADMM Based Framework for AutoML Pipeline Configuration

                                                Sijia Liu,* Parikshit Ram,* Deepak Vijaykeerthy, Djallel Bouneffouf, Gregory Bramble,
                                                            Horst Samulowitz, Dakuo Wang, Andrew Conn, Alexander Gray
                                                                                  IBM Research AI
                                                                                                   *
                                                                                                       Equal contributions

arXiv:1905.00424v5 [cs.LG] 6 Dec 2019
                                                                    Abstract                                      multiple variables & constraints) into simpler sub-problems
                                                                                                                  (Boyd and others 2011)
                                          We study the AutoML problem of automatically configuring
                                          machine learning pipelines by jointly selecting algorithms and          Contributions. Starting with a combinatorially large set
                                          their appropriate hyper-parameters for all steps in supervised          of algorithm candidates and their collective set of hyper-
                                          learning pipelines. This black-box (gradient-free) optimization         parameters, we utilize ADMM to decompose the AutoML
                                          with mixed integer & continuous variables is a challenging              problem into three problems: (i) HPO with a small set of
                                          problem. We propose a novel AutoML scheme by leveraging                 only continuous variables & constraints, (ii) a closed-form
                                          the alternating direction method of multipliers (ADMM). The             Euclidean projection onto an integer set, and (iii) a combi-
                                          proposed framework is able to (i) decompose the optimization            natorial problem of algorithm selection. Moreover, we ex-
                                          problem into easier sub-problems that have a reduced number             ploit the ADMM framework to handle any black-box con-
                                          of variables and circumvent the challenge of mixed variable             straints alongside the black-box objective (loss) function –
                                          categories, and (ii) incorporate black-box constraints along-
                                          side the black-box optimization objective. We empirically
                                                                                                                  the above decomposition seamlessly incorporates such con-
                                          evaluate the flexibility (in utilizing existing AutoML tech-            straints while retaining almost the same sub-problems.
                                          niques), effectiveness (against open source AutoML toolkits),              Our contributions are: (i) We explicitly model the coupling
                                          and unique capability (of executing AutoML with practically             between hyper-parameters and available algorithms, and ex-
                                          motivated black-box constraints) of our proposed scheme on              ploit the hidden structure in the AutoML problem (Section
                                          a collection of binary classification data sets from UCI ML             3). (ii) We employ ADMM to decompose the problem into
                                          & OpenML repositories. We observe that on an average our                a sequence of sub-problems (Section 4.1), which decouple
                                          framework provides significant gains in comparison to other             the difficulties in AutoML and can each be solved more effi-
                                          AutoML frameworks (Auto-sklearn & TPOT), highlighting                   ciently and effectively, demonstrating over 10× speedup and
                                          the practical advantages of this framework.
                                                                                                                  10% improvement in many cases. (iii) We present the first
                                                                                                                  AutoML framework that explicitly handles general black-box
                                                             1    Introduction                                    constraints (Section 4.2). (iv) We demonstrate the flexibility
                                        Automated machine learning (AutoML) research has re-                      and effectiveness of the ADMM-based scheme empirically
                                        ceived increasing attention. The focus has shifted from hyper-            against popular AutoML toolkits Auto-sklearn (Feurer et al.
                                        parameter optimization (HPO) for the best configuration of a              2015) & TPOT (Olson and Moore 2016) (Section 5), per-
                                        single machine learning (ML) algorithm (Snoek, Larochelle,                forming best on 50% of the datasets; Auto-sklearn performed
                                        and Adams 2012), to configuring multiple stages of a ML                   best on 27% and TPOT on 20%.
                                        pipeline (e.g., transformations, feature selection, predictive
                                        modeling) (Feurer et al. 2015). Among the wide-range of                                      2    Related work
                                        research challenges offered by AutoML, we focus on the                    Black-box optimization in AutoML. Beyond grid-search
                                        automatic pipeline configuration problem (that is, joint algo-            for HPO, random search is a very competitive baseline be-
                                        rithm selection and HPO), and tackle it from the perspective              cause of its simplicity and parallelizability (Bergstra and
                                        of mixed continuous-integer black-box nonlinear program-                  Bengio 2012). Sequential model-based optimization (SMBO)
                                        ming. This problem has two main challenges: (i) the tight                 (Hutter, Hoos, and Leyton-Brown 2011) is a common tech-
                                        coupling between the ML algorithm selection & HPO; and                    nique with different ‘models’ such as Gaussian processes
                                        (ii) the black-box nature of optimization objective lacking               (Snoek, Larochelle, and Adams 2012), random forests (Hut-
                                        any explicit functional form and gradients – optimization                 ter, Hoos, and Leyton-Brown 2011) and tree-parzen estima-
                                        feedback is only available in the form of function evalua-                tors (Bergstra et al. 2011). However, black-box optimization
                                        tions. We propose a new AutoML framework to address these                 is a time consuming process because the expensive black-
                                        challenges by leveraging the alternating direction method of              box function evaluation involves model training and scoring
                                        multipliers (ADMM). ADMM offers a two-block alternating                   (on a held-out set). Efficient multi-fidelity approximations
                                        optimization procedure that splits an involved problem (with              of the black-box function based on some budget (training
                                        Copyright c 2020, Association for the Advancement of Artificial           samples/epochs) combined with bandit learning can skip un-
                                        Intelligence (www.aaai.org). All rights reserved.                         promising candidates early via successive halving (Jamieson

and Talwalkar 2016; Sabharwal and others 2016) and Hy-            We will also briefly discuss how this formulation facilities
perBand (Li et al. 2018). However, these schemes essen-           extension to other flexible pipelines.
tially perform an efficient random search and are well suited        Problem statement. For N functional modules (e.g., prefor search over discrete spaces or discretized continuous         processor, transformer, estimator) with a choice of Ki algospaces. BOHB (Falkner, Klein, and Hutter 2018) combines           rithms in each, let zi ∈ {0, 1}Ki denote the algorithm choice
                                                                                                                PKi
SMBO (with TPE) and HyperBand for improved optimiza-              in module i, with the constraint 1> zi =         j=1 zij = 1
tion. Meta-learning (Vanschoren 2018) leverages past expe-        ensuring that only a single algorithm is chosen from each
riences in the optimization with search space refinements         module. Let z = {z1 , . . . , zN }. Assuming that categorical
and promising starting points. The collaborative filtering        hyper-parameters can be encoded as integers (using standard
based methods (Yang et al. 2019) are examples of meta-            techniques), let θ ij be the hyper-parameters of algorithm j
                                                                                                      c
learning, where information from past evaluation on other         in module i, with θ cij ∈ Cij ⊂ Rmij as the continuous hyperdatasets is utilized to pick pipelines for any new datasets.                                                                            d

Compared to the recent works on iterative pipeline construc-      parameters (constrained to the set Cij ) and θ dij ∈ Dij ⊂ Zmij
tion using tree search (Mohr, Wever, and Hüllermeier 2018;        as the integer hyper-parameters (constrained to Dij ). Condi-
Rakotoarison, Schoenauer, and Sebag 2019), we provide a           tional hyper-parameters can be handled with additional connatural yet formal primal-dual decomposition of autoML            straints θ ij ∈ Eij or by “flattening” the hyper-parameter tree
                                                                  and considering each leaf as a different algorithm. For simpipeline configuration problems.                                  plicity of exposition, we assume that the conditional hyper-
Toolkits. Auto-WEKA (Thornton et al. 2012; Kotthoff and           parameters are flattened into additional algorithm choices.
others 2017) and Auto-sklearn (Feurer et al. 2015) are the        Let θ = {θ ij , ∀i ∈ [N ], j ∈ [Ki ]}, where [n] = {1, . . . , n}
main representatives of SBMO-based AutoML. Both apply             for n ∈ N. Let f (z, θ; A) represent some notion of loss
the general purpose framework SMAC (Sequential Model-             of a ML pipeline corresponding to the algorithm choices as
based Algorithm Configuration) (Hutter, Hoos, and Leyton-         per z with the hyper-parameters θ on a learning task with
Brown 2011) to find optimal ML pipelines. Both consider           data A (such as the k-fold cross-validation or holdout validaa fixed shape of the pipeline with functional modules (pre-       tion loss). The optimization problem of automatic pipeline
processing, transforming, modeling) and automatically select      configuration is stated as:
a ML algorithm and its hyper-parameters for each module.             min f (z, θ; A)
Auto-sklearn improves upon Auto-WEKA with two inno-                   z,θ
                                                                                   zi ∈ {0, 1}Ki , 1> zi = 1, ∀i ∈ [N ],               (1)
                                                                                
vations: (i) a meta-learning based preprocessing step that           subject to
uses ‘meta-features’ of the dataset to determine good initial                      θ cij ∈ Cij , θ dij ∈ Dij , ∀i ∈ [N ], j ∈ [Ki ].
pipeline candidates based on past experience to warm start           We highlight 2 key differences of problem (1) from the
the optimization, (ii) an greedy forward-selection ensembling     conventional CASH formulation: (i) we use explicit Boolean
(Caruana et al. 2004) of the pipeline configurations found dur-   variables z to encode the algorithm selection, (ii) we differing the optimization as an independent post-processing step.      entiate continuous variables/constraints from discrete ones
Hyperopt-sklearn (Komer, Bergstra, and Eliasmith 2014) uti-       for a possible efficient decomposition between continuous
lizes TPE as the SMBO. TPOT (Olson and Moore 2016) and            optimization and integer programming. These features better
ML-Plan (Mohr, Wever, and Hüllermeier 2018) use genetic           characterize the properties of the problem and thus enable
algorithm and hierarchical task networks planning respec-         more effective joint optimization. For any given (z, θ) and
tively to optimize over the pipeline shape and the algorithm      data A, the objective (loss) function f (z, θ; A) is a blackchoices, but require discretization of the hyper-parameter        box function – it does not have an analytic form with respect
space (which can be inefficient in practice as it leads perfor-   to (z, θ) (hence no derivatives). The actual evaluation of f
mance degradation). AlphaD3M (Drori, Krishnamurthy, and           usually involves training, testing and scoring the ML pipeline
others 2018) integrates reinforcement learning with Monte-        corresponding to (z, θ) on some split of the data A.
Carlo tree search (MCTS) for solving AutoML problems                 AutoML with black-box constraints. With the increasbut without imposing efficient decomposition over hyperpa-        ing adoption of AutoML, the formulation (1) may not be
rameters and model selection. AutoStacker (Chen, Wu, and          sufficient. AutoML may need to find ML pipelines with high
others 2018) focuses on ensembling and cascading to gener-        predictive performance (low loss) that also explicitly satate complex pipelines and the actual algorithm selection and      isfy application specific constraints. Deployment constraints
hyper-parameter optimization happens via random search.           may require the pipeline to have prediction latency or size
                                                                  in memory below some threshold (latency ≤ 10µs, mem-
                                                                  ory ≤ 100MB). Business specific constraints may desire
 3    An Optimization Perspective to AutoML                       pipelines with low overall classification error and an explicit
                                                                  upper bound on the false positive rate – in a loan default risk
We focus on the joint algorithm selection and HPO for a fixed     application, false positives leads to loan denials to eligible
pipeline – a ML pipeline with a fixed sequence of functional      candidates, which may violate regulatory requirements. In
modules (preprocessing → missing/categorical handling →           the quest for fair AI, regulators may explicitly require the
transformations → feature selection → modeling) with a set        ML pipeline to be above some predefined fairness threshold
of algorithm choices in each module – termed asthe CASH           (Friedler and others 2019). Furthermore, many applications
(combined algorithm selection and HPO) problem (Thornton          have very domain specific metric(s) with corresponding conet al. 2012; Feurer et al. 2015) and solved with toolkits such    straints – custom metrics are common in Kaggle competitions.
as Auto-WEKA and Auto-sklearn. We extend this formula-            We incorporate such requirements by extending AutoML fortion by explicitly expressing the combinatorial nature of the     mulation (1) to include M black-box constraints:
algorithm selection with Boolean variables and constraints.                            gi (z, θ; A) ≤ i , i ∈ [M ].                   (2)

These functions have no analytic form with respect to (z, θ),                  is defined (hence can only be evaluated) on the integer sets
in constrast to the analytic constraints in problem (1). One ap-               Dij s. Ergo, problem (3) can be equivalently posed as
proach is to incorporate these constraints into the black-box                                  n
                                                                                                        ed ; A
                                                                                                           o 
                                                                                       min  fe z, θ c , θ
objective with a penalty
                     P      function p, Q where the new objec-                         c ed
                                                                                  z,θ ,θ ,δ
tive becomes f + i p(gi , i ) or f · i p(gi , i ). However,                                
                                                                                                zi ∈ {0, 1}Ki , 1> zi = 1, ∀i ∈ [N ]
these schemes are very sensitive to the choice of the penalty                                                                                     (5)
                                                                                             
                                                                                                              edij ∈ D
                                                                                             
                                                                                              c
                                                                                                θ ij ∈ Cij , θ       eij , ∀i ∈ [N ], j ∈ [Ki ]
function and do not guarantee feasible solutions.                                 subject to
                                                                                                δ ij ∈ D  ij , ∀i ∈ [N ], j ∈ [Ki ]
   Generalization for more flexible pipelines. We can ex-
                                                                                             
                                                                                              d
                                                                                             
tend the problem formulation (1) to enable optimization over                                    θ ij = δ ij , ∀i ∈ [N ], j ∈ [Ki ],
                                                                                                e
the ordering of the functional modules. For example, we can                    where the equivalence between problems (3) & (5) is eschoose between ‘preprocessor → transformer → feature se-                                                            ed = δ ij ∈ Dij , im-
                                                                               tablished by the equality constraint θ        ij
lector’ OR ‘feature selector → preprocessor → transformer’.
The ordering of T ≤ N modules can be optimized by in-                          plying PDij (θ   ed ) = θ   ed ∈ Dij and fe(z, {θ c , θed }; A) =
                                                                                                  ij        ij
troducing T 2 Boolean variables o = {oik : i, k ∈ [T ]},                                    ed }; A). The continuous surrogate loss (4) is key
                                                                               f (z, {θ c , θ
where oik = 1 indicates that module i is placed at po-                         in being able to perform theoretically grounded operator splitsition k. The following constraints are then needed: (i)
P                                                                              ting (via ADMM) over mixed continuous/integer variables in
   k∈[T ] oik = 1, ∀i ∈ [T ] P
                             indicates that module i is placed at              the AutoML problem (3).
a single position, and (ii) i∈[T ] oik = 1∀k ∈ [T ] enforces                      Operator splitting from ADMM. Using the notation that
that only one module is placed at position k. These variables                  IX (x) = 0 if x ∈ X else +∞, and defining the sets Z =
can be added to z in problem (1) (z = {z1 , . . . , zN , o}).                  {z : z = {zi }, zi ∈ {0, 1}Ki , 1> zi = 1, ∀i ∈ [N ]}, C =
The resulting formulation still obeys the generic form of (1),                 {θ c : θ c = {θ cij }, θ cij ∈ Cij , ∀i ∈ [N ], j ∈ [Ki ]}, D =
which as will be evident later, can be efficiently solved by an                {δ : δ = {δ ij }, δ ij ∈ Dij , ∀i ∈ [N ], j ∈ [Ki ]} and D     e =
operator splitting framework like ADMM (Boyd and others                           d     d         d    d
2011).                                                                         {θe :θ e = {θ    e }, θ
                                                                                                  ij
                                                                                                      e ∈D
                                                                                                       ij
                                                                                                               eij , ∀i ∈ [N ], j ∈ [Ki ]}, we can
                                                                               re-write problem (5) as
                                                                                                           ed ; A + IZ (z) + IC (θ c ) + I e (θ
                                                                                                                                              ed )
                                                                                                  n         o 
         4      ADMM-Based Joint Optimizer                                            min      fe z, θ c , θ                              D
                                                                                         d
                                                                                 z,θ c ,θ
                                                                                        e ,δ
ADMM provides a general effective optimization framework
to solve complex problems with mixed variables and multiple                                                         ed = δ.
                                                                                               + ID (δ); subject to θ                             (6)
constraints (Boyd and others 2011; Liu et al. 2018). We utilize                with the corresponding augmented Lagrangian function
this framework to decompose problem (1) without and with                                   ed , δ, λ) := fe z, θ c , θ
                                                                                                            n
                                                                                                                     ed ; A + IZ (z) + IC (θ c )
                                                                                                                       o 
black-box constraints (2) into easier sub-problems.                             L(z, θ c , θ
                                                                                                                                       2
                                                                                           ed ) + ID (δ) + λ> θ e −δ + ρ θ
                                                                                                                d
                                                                                                                               ed − δ , (7)
                                                                                                                        
4.1 Efficient operator splitting for AutoML                                       + IDe (θ
                                                                                                                            2          2

In what follows, we focus on solving problem (1) with an-                      where λ is the Lagrangian multiplier, and ρ > 0 is a penalty
alytic constraints. The handling of black-box constraints                      parameter for the augmented term.
will be elaborated on in the next section. Denoting θ c =                         ADMM (Boyd and others 2011) alternatively minimizes
{θ cij , ∀i ∈ [N ], j ∈ [Ki ]} as all the continuous hyper-                    the augmented Lagrangian function (7) over two blocks of
parameters and θ d (defined correspondingly) as all the integer                variables, leading to an efficient operator splitting framework
hyper-parameters, we re-write problem (1) as:                                  for nonlinear programs with nonsmooth objective function
                      n          o                                           and equality constraints. Specifically, ADMM solves problem
       min          f z, θ c , θ d ; A                                         (1) by alternatively minimizing (7) over variables {θ c , θ ed },
   z,θ={θ c ,θ d
                }
                     zi ∈ {0, 1}Ki , 1> zi = 1, ∀i ∈ [N ],
                                                                         (3)   and {δ, z}. This can be equivalently converted into 3 sub-
   subject to                                                                                                  ed }, δ and z, respectively. We
                     θ cij ∈ Cij , θ dij ∈ Dij , ∀i ∈ [N ], j ∈ [Ki ].         problems over variables {θ c , θ
                                                                               refer readers to Algorithm 1 for simplified sub-problems and
   Introduction of continuous surrogate loss. With D         eij               Appendix 11 for detailed derivation.
as the continuous relaxation of the integer space Dij (if                         The rationale behind the advantage of ADMM is that it
Dij includes integers ranging from {l, . . . , u} ⊂ Z, then                    decomposes the AutoML problem into sub-problems with
                         ed as the continuous surrogates for                   smaller number of variables: This is crucial in black-box op-
Deij = [l, u] ⊂ R), and θ
                                                                               timization where convergence is strongly dependent on the
θ d with θ
         eij ∈ Deij (corresponding to θ ij ∈ Dij ), we utilize a               number of variables. For example, the number of black-box
surrogate loss function fe for problem (3) defined solely over                 evaluations needed for critical point convergence is typically
the continuous domain with respect to θ:                                       O(n ∼ n3 ) for n variables (Larson, Menickelly, and Wild
                                                                               2019). In what follows, we show that the easier sub-problems
                  ed ; A := f z, θ c , PD θ
         n         o        n          d o 
      fe z, θ c , θ                       e    ;A ,                      (4)   in Algorithm 1 yield great interpretation of the AutoML prob-
                                                                               lem (1) and suggest efficient solvers in terms of continuous
           ed ) = {PD (θ   ed ), ∀i ∈ [N ], j ∈ [Ki ]} is the                  hyper-parameter optimization, integer projection operation,
where PD (θ             ij  ij                                                 and combinatorial algorithm selection.
projection of the continuous surrogates onto the integer set.
                                                                                  1
This projection is necessary since the black-box function                             Appendices are at https://arxiv.org/pdf/1905.00424.pdf

Algorithm 1 Operator splitting from ADMM to solve problem (5)

                                                                                                            2                       1 (t)
                       d
                                                                                                      ed − b ,
                                                                                         ed ) + (ρ/2) θ
                                                               ed ; A + IC (θ c ) + I e (θ
        n                        o                     n        o 
            θ c(t+1) , θ
                       e (t+1)       = arg min fe z(t) , θ c , θ                     D                               b := δ (t) −     λ ,      (θ-min)
                                          c ed
                                        θ ,θ
                                                                                                                2                   ρ
                                                                                  d
                           δ (t+1) = arg min ID (δ) + (ρ/2) ka − δk22 , a := θ
                                                                             e (t+1) + (1/ρ)λ(t) ,                                             (δ-min)
                                        δ

                                                             ed (t+1) ; A + IZ (z),
                                                n                   o 
                           z(t+1) = arg min fe z, θ c(t+1) , θ                                                                                 (z-min)
                                          z

                                                                                                              ed (t+1) − δ (t+1) ).
  where (t) represents the iteration index, and the Lagrangian multipliers λ are updated as λ(t+1) = λ(t) + ρ(θ

                                                                                                                                      QN
   Solving θ-min. Problem (θ-min) can be rewritten as                           is a black-box integer program solved exactly with i=1 Ki
                                                           2                    evaluations of fe. However, this is generally not feasible.
                                 ed ; A + ρ θ     ed − b
                          n        o 
             min fe z(t) , θ c , θ                                              Beyond random sampling, there are a few ways to lever-
              c
             θ ,θ
                e d                           2            2
                         c
                          θ ij ∈ Cij                                   (8)      age existing AutoML schemes: (i) Combinatorial multi-
             subject to   edij ∈ Deij , ∀i ∈ [N ], j ∈ [Ki ],                   armed bandits. – Problem (12) can be interpreted through
                          θ                                                     combinatorial bandits as the selection of the optimal N
                                                                                                                        PN
                      ed are continuous optimization vari-                      arms (in this case, algorithms) from i=1 Ki arms based
where both θ c and θ                                                            on bandit feedback and can be efficiently solved with
ables. Since the algorithm selection scheme z(t) is fixed for                   Thompson sampling (Durand and Gagné 2014) (ii) Multithis problem, fe in problem (8) only depends on the hyper-                      fidelity approximation of black-box evaluations – Techniques
parameters of the chosen algorithms – the active set of con-                    such as successive halving (Jamieson and Talwalkar 2016;
                           ed ) where zij (t) = 1. This splits
tinuous variables (θ cij , θ                                                    Li et al. 2018) or incremental data allocation (Sabharwal
                             ij
problem (8) even further into two problems. The inactive set                    and others 2016) can efficiently search over a discrete set of
                                                                                QN
problem reduces to the following for all i ∈ [N ], j ∈ [Ki ]                       i=1 Ki candidates. (iii) Genetic algorithms – Genetic prosuch that zij = 0:                                                              gramming can perform this discrete black-box optimization
                  ρ ed                                                          starting from a randomly generated population and building
             min kθ            2
                     ij − bij k2          subject to   edij ∈ D
                                                       θ      eij ,    (9)      the next generation based on the ‘fitness’ of the pipelines and
               d
             θ ij 2
             e
                                                                                random ‘mutations’ and ‘crossovers’.
which is solved by a Euclidean projection of bij onto D   eij .
                                                                                4.2 ADMM with black-box constraints
                                                    d
   For the active set of variables S = {(θ cij , θ e ) : θc ∈
                                                    ij    ij                    We next consider problem (3) in the presence of black-box
                                                                                constraints (2). Without loss of generality, we assume that
Cij , θ ij ∈ Dij , zij = 1, ∀i ∈ [N ], j ∈ [Ki ]}, problem (8)
      e      e
                                                                                i ≥ 0 for i ∈ [M ]. By introducing scalars ui ∈ [0, i ], we
reduces to the following black-box optimization with only                       can reformulate the inequality constraint (2) as the equality
the small active set of continuous variables2                                   constraint together with a box constraint
                                                  2
                                 ed ; A + ρ θ
                                            ed − b .
                         n        o 
                 fe z(t) , θ c , θ
                                                                                         n          o 
         min                                                          (10)            gi z, θ c , θ d ; A − i + ui , ui ∈ [0, i ], i ∈ [M ].     (13)
         c   d
       (θ ,θ )∈S
           e                              2       2

The above problem can be solved using Bayesian optimiza-                        We then introduce a continuous surrogate black-box functions
tion (Shahriari et al. 2016), direct search (Larson, Menickelly,                gei for gi , ∀i ∈ [M ] in a similar manner to fe given by (4).
and Wild 2019), or trust-region based derivative-free opti-                     Following the reformulation of (3) that lends itself to the
mization (Conn, Scheinberg, and Vicente 2009).                                  application of ADMM, the version with black-box constraints
   Solving δ-min. According to the definition of D, problem                     (13) can be equivalently transformed into
(δ-min) can be rewritten as
                                                                                                          ed ; A
                                                                                                 n          o 
    ρ                                                                             min         fe z, θ c , θ
 min kδ−ak22            subject to δ ij ∈ Dij , ∀i ∈ [N ], j ∈ [Ki ], (11)             e d ,δ
                                                                                z,θ c ,θ
  δ 2
                                                                                                  zi ∈ {0, 1}Ki , 1> zi = 1, ∀i ∈ [N ]
                                                                                               
                                                                                               
                                                                                                                edij ∈ D
                                                                                               
                                                                                                  θ cij ∈ Cij , θ        eij , ∀i ∈ [N ], j ∈ [Ki ]
                                                                                               
and solved in closed form by projecting a onto D
                                               e and then                                      
                                                                                               
                                                                                                                                                    (14)
                                                                                               
                                                                                                δ ij ∈ Dij , ∀i ∈ [N ], j ∈ [Ki ]
                                                                                               
rounding to the nearest integer in D.
  Solving z-min. Problem (z-min) rewritten as                                   subject to           d
                                                                                               
                                                                                                 θ ij = δ ij , ∀i ∈ [N ], j ∈ [Ki ]
                                                                                                  e
                                                                                               
                                                                                                 ui ∈ [0,
                                                                                                          n i ], ∀id o∈ [M]
                                  ed (t+1) ; A
                    n                    o 
             min fe z, θ c(t+1) , θ
                                                                                               
                                                                                               
                                                                                                ge z, θ c , θ
                                                                                                                e ; A − i + ui = 0, ∀i ∈ [M ].
               z                                                      (12)                         i
             subject to zi ∈ {0, 1}Ki , 1> zi = 1, ∀i ∈ [N ]
   2
                                                                                Compared to problem (5), the introduction of auxiliary vari-
     For the AutoML problems we consider in our empirical evalu-                ables {ui } enables ADMM to incorporate black-box equality
                           edij | ≈ 100 while the largest possible active
tations, |θ| = |θ cij | + |θ                                                    constraints as well as elementary white-box constraints. Simset S is less than 15 and typically less than 10.                               ilar to Algorithm 1, the ADMM solution to problem (14) can

Algorithm 2 Operator splitting from ADMM to solve problem (14) (with black-box constraints)

                                                                                                 M                         2
        n
           c(t+1) ed (t+1)    (t+1)
                                    o                 ρ ed   2
                                                                     c           d            ρX                     µi (t)
         θ       ,θ        ,u         = arg min f + e   θ − b + IC (θ ) + IDe (θ ) + IU (u) +
                                                                               e                     gei + ui − i +           ,
                                             e d ,u
                                        θ c ,θ
                                                      2      2                                2 i=1                   ρ
                                                 ρ
                             δ (t+1) = arg min     kδ − ak22 + ID (δ),
                                          δ      2
                                                               M                              2
                                                            ρX                          1
                             z(t+1) = arg min fe + IZ (z) +        gei − i + ui (t+1) + µi (t) ,
                                         z                  2 i=1                       ρ

  where the arguments of f˜ and g̃i are omitted for brevity, a and b have been defined in Algorithm 1, U = {u : u = {ui },
  and µi is the Lagrangian multiplier corresponding to the equality constraint g̃i − i + ui = 0 in (14) and updated as
                                                  ed (t+1) }; A) − i + ui (t+1) ) for ∀i ∈ [M ].
                        gi (z(t+1) , {θ c (t+1) , θ
  µi (t+1) = µi (t) + ρ(e

be achieved by solving three sub-problems of similar nature,          knowledge. Empirically, we will demonstrate the improved
summarized in Algorithm 2 and derived in Appendix 2.                  convergence of the proposed scheme against baselines in the
   We remark that the integration of ADMM and gradient-               following section.
free operations was also considered in (Liu et al. 2018) and
(Ariafar et al. 2017; 2019), where the former used random-                          5    Empirical Evaluations
ized gradient estimator when optimizing a black-box smooth
objective function, and the latter used Bayesian optimization         In this evaluation of our proposed framework, we demon-
(BO) as the internal solver to solve black-box optimization           strate three important characteristics: (i) the empirical perproblems with black-box constraints. However, the afore-              formance against existing AutoML toolkits, highlighting the
mentioned works cannot directly be applied to tackling our            empirical competitiveness of the theoretical formalism, (ii)
considered AutoML problem, which requires a more involved             the systematic capability to handle black-box constraints,
splitting over hypeparameters and model selection variables.          enabling AutoML to address real-world ML tasks, and (iii)
Moreover, different from (Ariafar et al. 2019), we handle             the flexibility to incorporate various learning procedures and
the black-box inequality constraint through the reformulated          solvers for the sub-problems, highlighting that our proposed
equality constraint (13). By contrast, the work (Ariafar et           scheme is not a single algorithm but a complete framework
al. 2019) introduced an indicator function for a black-box            for AutoML pipeline configuration.
constraint and further handled it by modelling as a Bernoulli         Data and black-box objective function. We consider 30 birandom variable.                                                      nary classification3 datasets from the UCI ML (Asuncion and
                                                                      Newman 2007) & OpenML repositories (Bischl and others
4.3 Implementation and convergence                                    2017), and Kaggle. We consider a subset of OpenML100
                                                                      limited to binary classification and small enough to allow for
We highlight that our ADMM based scheme is not a single               meaningful amount of optimization for all baselines in the
AutoML algorithm but rather a framework that can be used to           allotted 1 hour to ensure that we are evaluating the optimizers
mix and match different existing black-box solvers. This is es-       and not the initialization heuristics. Dataset details are in Appecially useful since this enables the end-user to plug-in effi-      pendix 6. We consider (1 − AUROC) (area under the ROC
cient solvers tailored for the sub-problems (HPO & algorithm          curve) as the black-box objective and evaluate it on a 80-20%
selection in our case). In addition to the above, the ADMM            train-validation split for all baselines. We consider AUROC
decomposition allows us to solve simpler sub-problems with            since it is a meaningful predictive performance metric rea smaller number of optimization variables (a significantly re-       gardless of the class imbalance (as opposed to classification
duced search space since (θ-min) only requires optimization           error).
over the active set of continuous variables). Unless specified        Comparing ADMM to AutoML baselines. Here we evalotherwise, we adopt Bayesian optimization (BO) to solve the           uate the proposed ADMM framework against widely used
HPO (θ-min), e.g., (10). We use customized Thompson sam-              AutoML systems Auto-sklearn (Feurer et al. 2015) and TPOT
pling to solve the combinatorial multi-armed bandit problem,          (Olson and Moore 2016). This comparison is limited to blacknamely, the (z-min) for algorithm selection. We refer readers         box optimization with analytic constraints only given by (1)
to Appendix 3 and 4 for more derivation and implementation            since existing AutoML toolkits cannot handle black-box condetails. In Appendix 5, we demonstrate the generalizability           straints explicitly. We consider SMAC based vanilla Autoof ADMM to different solvers for (θ-min) and (z-min).The              sklearn ASKL4 (disabling ensembles and meta-learning),
theoretical convergence guarantees of ADMM have been                  random search RND, and TPOT with a population of 50
established under certain assumptions, e.g., convexity or
smoothness (Boyd and others 2011; Hong and Luo 2017;                     3
                                                                           Our scheme applies to multiclass classification & regression.
Liu et al. 2018). Unfortunately, the AutoML problem vi-                  4
                                                                           Meta-learning and ensembling in ASKL are preprocessing and
olates these restricted assumptions. Even for non-ADMM                postprocessing steps respectively to the actual black-box optimizabased AutoML pipeline search, there is no theoretical conver-         tion and can be applied to any optimizer. We demonstrate this for
gence established in the existing baselines to the best of our        ADMM in Appendix 8. So we skip these aspects of ASKL here.

(instead of the default 100) to ensure that TPOT is able to
process multiple generations of the genetic algorithm in the
allotted time on all data sets. For ADMM, we utilize BO for
(θ-min) and CMAB for (z-min) – ADMM(BO,Ba)5 .

                                                                                       (a) Varying tp , dI = 0.07               (b) Varying dI , tp = 10µs

                                                                                    Figure 2: Best objective achieved by any constraint satisfying
                                                                                    pipeline from running the optimization for 1 hour seconds with
                                                                                    varying thresholds for the two constraints – lower is better (please
                                                                                    view in color). Note the log-scale on the vertical axis.

                                (a) All methods                                     Feurer et al. (2015). With enough time, all schemes outper-
                                                                                    form random search RND. TPOT50 performs worst in the
                                                                                    beginning because of the initial start-up time involved in the
                                                                                    genetic algorithm. ASKL and ADMM(BO,Ba) have compa-
                                                                                    rable performance initially. As the optimization continues,
                                                                                    ADMM(BO,Ba) significantly outperforms all other baselines.
                                                                                    We present the pairwise performance of ADMM with ASKL
                                                                                    (figure 1b) & TPOT50 (figure 1c).
          (b) ASKL vs. ADMM                  (c) TPOT50 vs. ADMM                    AutoML with black-box constraints. To demonstrate the
                                                                                    capability of the ADMM framework to incorporate real-world
Figure 1: Average rank (across 30 datasets) of mean performance                     black-box constraints, we consider the recent Home Credit
across 10 trials – lower rank is better.                                            Default Risk Kaggle challenge8 with the black-box objec-
                                                                                    tive of (1 − AUROC), and 2 black-box constraints: (i) (de-
   For all optimizers, we use scikit-learn algorithms                               ployment) Prediction latency tp enforcing real-time predic-
(Pedregosa, Varoquaux, and others 2011). The functional                             tions, (ii) (fairness) Maximum pairwise disparate impact
modules and the algorithms (with their hyper-parameters)                            dI (Calders and Verwer 2010) across all loan applicant age
are presented in Table A3 in Appendix 7. We maintain par-                           groups enforcing fairness across groups (see Appendix 12).
ity6 across the various AutoML baselines by searching over                             We run a set of experiments for each of the constraints:
the same set of algorithms (see Appendix 7). For each                               (i) fixing dI = 0.7, we optimize for each of the thresholds
scheme, the algorithm hyper-parameter ranges are set us-                            tp = {1, 5, 10, 15, 20} (in µs), and (ii) fixing tp = 10µs and
ing Auto-sklearn as the reference7 . We optimize for 1 hour                         we optimize for each of dI = {0.05, 0.075, 0.1, 0.125, 0.15}.
& generate time vs. incumbent black-box objective curves                            Note that the constraints get less restrictive as the thresholds
aggregated over 10 trials. Details on the complete setup are                        increase. We apply ADMM to the unconstrained problem
in Appendix 10. The optimization convergence for all 30                             (UCST) and post-hoc filter constraint satisfying pipelines to
datasets are in Appendix 11. At completion, ASKL achieves                           demonstrate that these constraints are not trivially satisfied.
the lowest mean objective (across trials) in 6/30 datasets,                         Then we execute ADMM with these constraints (CST). Using
TPOT50 in 8/30, RND in 3/30 and ADMM(BO,Ba) in 15/30,                               BO for (θ-min) & CMAB for (z-min), we get two variants –
showcasing ADMM’s effectiveness.                                                    UCST(BO,Ba) & CST(BO,Ba). This results in (5 + 5) × 2 =
   Figure 1 presents the overall performance of the different                       20 ADMM executions, each repeated 10×.
AutoML schemes versus optimization time. Here we consider                              Figure 2 presents the objective achieved by the optimizer
the relative rank of each scheme (with respect to the mean                          when limited only to constraint satisfying pipelines. Figure
objective over 10 trials) for every timestamp, and average                          2a presents the effect of relaxing the constraint on tp while
this rank across 30 data sets similar to the comparison in                          Figure 2b presents the same for the constraint on dI . As
                                                                                    expected, the objective improves as the constraints relax. In
   5
      In this setup, ADMM has 2 parameters: (i) the penalty ρ on                    both cases, CST outperforms UCST, with UCST approaching
the augmented term, (ii) the loss upper-bound fˆ in the CMAB                        CST as the constraints relax. Figure 3 presents the constraint
algorithm (Appendix 4). We evaluate the sensitivity of ADMM on                      satisfying capability of the optimizer by considering the fracthese parameters in Appendix 9. The results indicate that ADMM is                   tion of constraint-satisfying pipelines found (Figure 3a & 3b
fairly robust to these parameters, and hence set ρ = 1 and fˆ = 0.7                 for varying tp & dI respectively). CST again significantly outthroughout. We start the ADMM optimization with λ(0) = 0.                           performs UCST, indicating that the constraints are non-trivial
    6
      ASKL and ADMM search over the same search space of fixed                      to satisfy, and that ADMM is able to effectively incorporate
pipeline shape & order. TPOT also searches over different pipeline                  the constraints for improved performance.
shapes & orderings because of the nature of its genetic algorithm.
   7                                                                                   8
       github.com/automl/auto-sklearn/tree/master/autosklearn/pipeline/components          www.kaggle.com/c/home-credit-default-risk

Flexibility & benefits from ADMM operator splitting. It
is common in ADMM to solve the sub-problems to higher
approximation in the initial iterations and to an increasingly
lower approximation as ADMM progresses (instead of the
same approximation throughout) (Boyd and others 2011). We
demonstrate (empirically) that this adaptive ADMM produces
expected gains in the AutoML problem. Moreover, we show
the empirical gains of ADMM from (i) splitting the AutoML
problem (1) into smaller sub-problems which are solved in
an alternating fashion, & (ii) using different solvers for the
differently structured (θ-min) and (z-min).
   First we use BO for both (θ-min) and (z-min). For
ADMM with a fixed approximation level (fixed ADMM),
we solve the sub-problems with BO to a fixed number                     Figure 4: Optimization time (in seconds) vs. median validation
I = 16, 32, 64, 128 iterations, denoted by ADMMI(BO,BO)                 performance with the inter-quartile range over 10 trials on fri-c2
                                                                        dataset – lower is better (please view in color). Note the log scale
(e.g., ADMM16(BO,BO)). For adaptive ADMM, we start                      on both axes. See Appendix 13 for additional results.
with 16 BO iterations for the sub-problems and progressively increase it with an additive factor F = 8 & 16 with
every ADMM iteration until 128 denoted by AdADMM-
F8(BO,BO) & AdADMM-F16(BO,BO) respectively. We optimize for 1 hour and aggregate over 10 trials.
   Figure 4 presents optimization convergence for 1 dataset
(fri-c2). We see the expected behavior – fixed ADMM with
small I dominate for small time scales but saturate soon;
large I require significant start-up time but dominate for
larger time scales. Adaptive ADMM (F = 8 & 16) is able
to match the performance of the best fixed ADMM at every
time scale.Please refer to Appendix 13 for additional results.
   Next, we illustrate the advantage of ADMM on operator
splitting. We consider 2 variants, AdADMM-F16(BO,BO)
and AdADMM-F16(BO,Ba), where the latter uses CMAB
                                                                        Figure 5: Optimization time vs. median validation performance with
for (z-min). For comparison, we solve the complete joint                the inter-quartile range over 10 trials on fri-c2 dataset – lower is
problem (1) with BO, leading to a Gaussian Process with a               better (please view in color). Note the log scale on both axes. See
large number of variables, denoted as JOPT(BO).                         Appendix 14 for additional results.
   Figure 5 shows the optimization convergence for 1 dataset
(fri-c2). The results indicate that the operator splitting in
ADMM provides significant improvements over JOPT(BO),                   (eliding “-F16”) respectively to reach the best objective of
with ADMM reaching the final objective achieved by JOPT                 JOPT, and similarly use IBa and IBO to represent the objecwith significant speedup, and then further improving upon               tive improvement at the final converged point. Table 1 shows
that final objective significantly. These improvements of               that between AdADMM(BO,BO) and AdADMM(BO,Ba),
ADMM over JPOT on 8 datasets are summarized in Table 1,                 the latter provides significantly higher speedups, but the forindicating significant speedup (over 10× in most cases) and             mer provides higher additional improvement in the final obfurther improvement (over 10% in many cases).                           jective. This demonstrates ADMM’s flexibility, for example,
   Let us use SBa and SBO to represent the temporal speedup             allowing choice between faster or more improved solution.
achieved by AdADMM(BO,Ba) and AdADMM(BO,BO)
                                                                                              6    Conclusions
                                                                        Posing the problem of joint algorithm selection and HPO
                                                                        for automatic pipeline configuration in AutoML as a formal
                                                                        mixed continuous-integer nonlinear program, we leverage
                                                                        the ADMM optimization framework to decompose this prob-
                                                                        lem into 2 easier sub-problems: (i) black-box optimization
                                                                        with a small set of continuous variables, and (ii) a combinato-
                                                                        rial optimization problem involving only Boolean variables.
                                                                        These sub-problems can be effectively addressed by existing
                                                                        AutoML techniques, allowing ADMM to solve the overall
   (a) Varying tp , dI = 0.07         (b) Varying dI , tp = 10µs        problem effectively. This scheme also seamlessly incorpo-
                                                                        rates black-box constraints alongside the black-box objective.
Figure 3: Fraction of pipelines found satisfying constraints with       We empirically demonstrate the flexibility of the proposed
optimization for 1 hour with varying thresholds for the 2 constraints   ADMM framework to leverage existing AutoML techniques
– higher is better. Note the log-scale on the vertical axis.            and its effectiveness against open-source baselines.

             Dataset      SBa    SBO    IBa    IBO                  Feurer, M.; Klein, A.; Eggensperger, K.; Springenberg, J.;
             Bank8FM      10×     2×     0%     5%                  Blum, M.; and Hutter, F. 2015. Efficient and robust automated
             CPU small     4×     5×     0%     5%
             fri-c2      153×    25×    56%    64%                  machine learning. In NeurIPS.
             PC4          42×     5×     8%    13%                  Friedler, S. A., et al. 2019. A comparative study of fairness-
             Pollen       25×     7×     4%     3%                  enhancing interventions in machine learning. In Proceedings
             Puma8NH      11×     4×     1%     1%
             Sylvine       9×     2×     9%    26%
                                                                    of the Conference on Fairness, Accountability, and Trans-
             Wind         40×     5×     0%     5%                  parency, 329–338. ACM.
                                                                    Hong, M., and Luo, Z.-Q. 2017. On the linear convergence of
Table 1: Comparing ADMM schemes to JOPT(BO), we list                the alternating direction method of multipliers. Mathematical
the speedup SBa & SBO achieved by AdADMM(BO,Ba) &                   Programming 162(1):165–199.
AdADMM(BO,BO) respectively to reach the best objective of JOPT,
and the final objective improvement IBa & IBO (respectively) over   Hutter, F.; Hoos, H. H.; and Leyton-Brown, K. 2011. Sethe JOPT objective. These numbers are generated using the aggre-    quential Model-based Optimization for General Algorithm
gate performance of JOPT and AdADMM over 10 trials.                 Configuration. In International Conference on Learning and
                                                                    Intelligent Optimization. Springer-Verlag.
                                                                    Jamieson, K., and Talwalkar, A. 2016. Non-stochastic best
                         References                                 arm identification and hyperparameter optimization. In AIS-
Agrawal, S., and Goyal, N. 2012. Analysis of thompson               TATS.
sampling for the multi-armed bandit problem. In Conference          Komer, B.; Bergstra, J.; and Eliasmith, C. 2014. Hyperopton Learning Theory, 39–1.                                           sklearn: automatic hyperparameter configuration for scikit-
Ariafar, S.; Coll-Font, J.; Brooks, D.; and Dy, J. 2017.            learn. In ICML workshop on AutoML.
An admm framework for constrained bayesian optimization.            Kotthoff, L., et al. 2017. Auto-weka 2.0: Automatic model
NIPS Workshop on Bayesian Optimization.                             selection and hyperparameter optimization in weka. JMLR.
Ariafar, S.; Coll-Font, J.; Brooks, D.; and Dy, J. 2019. Ad-        Larson, J.; Menickelly, M.; and Wild, S. M. 2019. Derivativemmbo: Bayesian optimization with unknown constraints us-            free optimization methods. Acta Numerica 28:287–404.
ing admm. Journal of Machine Learning Research 20(123):1–           Li, L.; Jamieson, K.; DeSalvo, G.; Rostamizadeh, A.; and Tal-
26.                                                                 walkar, A. 2018. Hyperband: A novel bandit-based approach
Asuncion, A., and Newman, D. 2007. UCI ML Repository.               to hyperparameter optimization. JMLR 18(185):1–52.
Bergstra, J., and Bengio, Y. 2012. Random search for hyper-         Liu, S.; Kailkhura, B.; Chen, P.-Y.; Ting, P.; Chang, S.; and
parameter optimization. JMLR 13(Feb):281–305.                       Amini, L. 2018. Zeroth-order stochastic variance reduction
Bergstra, J. S.; Bardenet, R.; Bengio, Y.; and Kégl, B. 2011.       for nonconvex optimization. In NeurIPS.
Algorithms for hyper-parameter optimization. In NeurIPS.            Mohr, F.; Wever, M.; and Hüllermeier, E. 2018. ML-Plan:
Bischl, B., et al. 2017. OpenML benchmarking suites and             Automated machine learning via hierarchical planning. Mathe OpenML100. arXiv:1708.03731.                                    chine Learning 107(8-10):1495–1515.
Boyd, S., et al. 2011. Distributed optimization and statistical     Olson, R. S., and Moore, J. H. 2016. TPOT: A tree-based
learning via the alternating direction method of multipliers.       pipeline optimization tool for automating machine learning.
Foundations and Trends R in Machine Learning 3(1):1–122.            In Workshop on AutoML.
Calders, T., and Verwer, S. 2010. Three naive bayes ap-             Pedregosa, F.; Varoquaux, G.; et al. 2011. Scikit-learn:
proaches for discrimination-free classification. Data Mining        Machine learning in Python. JMLR.
and Knowledge Discovery 21(2):277–292.                              Rakotoarison, H.; Schoenauer, M.; and Sebag, M. 2019.
Caruana, R.; Niculescu-Mizil, A.; Crew, G.; and Ksikes, A.          Automated Machine Learning with Monte-Carlo Tree Search.
2004. Ensemble selection from libraries of models. In ICML.         In IJCAI.
Chen, B.; Wu, H.; et al. 2018. Autostacker: A compositional         Sabharwal, A., et al. 2016. Selecting near-optimal learners
evolutionary learning system. In Proceedings of the Genetic         via incremental data allocation. In AAAI.
and Evolutionary Computation Conference, 402–409. ACM.              Shahriari, B.; Swersky, K.; Wang, Z.; Adams, R. P.; and
Conn, A. R.; Scheinberg, K.; and Vicente, L. N. 2009. Intro-        De Freitas, N. 2016. Taking the human out of the loop: A
duction to derivative-free optimization. SIAM.                      review of bayesian optimization. Proceedings of the IEEE.
Costa, A., and Nannicini, G. 2018. Rbfopt: an open-source           Snoek, J.; Larochelle, H.; and Adams, R. P. 2012. Practical
library for black-box optimization with costly function evalu-      bayesian optimization of machine learning algorithms. In
ations. Mathematical Programming Computation 10(4).                 NeurIPS.
Drori, I.; Krishnamurthy, Y.; et al. 2018. Alphad3m: Machine        Thornton, C.; Hoos, H. H.; Hutter, F.; and Leyton-Brown, K.
learning pipeline synthesis. In AutoML Workshop at ICML.            2012. Auto-weka: Automated selection and hyper-parameter
Durand, A., and Gagné, C. 2014. Thompson sampling for               optimization of classification algorithms. arXiv:1208.3719.
combinatorial bandits and its application to online feature         Vanschoren, J.          2018.     Meta-learning: A survey.
selection. In AAAI Workshops.                                       arXiv:1810.03548.
Falkner, S.; Klein, A.; and Hutter, F. 2018. BOHB: Robust           Williams, C. K., and Rasmussen, C. E. 2006. Gaussian
and efficient hyperparameter optimization at scale. In ICML.        processes for machine learning. MIT Press Cambridge, MA.

Yang, C.; Akimoto, Y.; Kim, D. W.; and Udell, M. 2019.
OBOE: Collaborative filtering for AutoML model selection.
In KDD.
Zhu, C.; Byrd, R. H.; Lu, P.; and Nocedal, J. 1997. Algorithm
778: L-bfgs-b: Fortran subroutines for large-scale boundconstrained optimization. ACM TOMS 23(4):550–560.

          Appendices of ‘An ADMM Based Framework for AutoML Pipeline Configurations’
                          1 Derivation of ADMM sub-problems in Table 1
ADMM decomposes the optimization variables into two blocks and alternatively minimizes the augmented Lagrangian function
(7) in the following manner at any ADMM iteration t
                                            ed (t+1) = arg min L z(t) , θ c , θ
                                                                              ed , δ (t) , λ(t)
                                n                   o                                          
                                 θ c(t+1) , θ                                                                     (A15)
                                                                        d
                                                               θ c ,θ
                                                                    e

                                                                               ed (t+1) , δ, λ(t)
                                   n                o                                            
                                    δ (t+1) , z(t+1) = arg min L z, θ c(t+1) , θ                                       (A16)
                                                                 δ,z
                                                                  d               
                                                λ(t+1) = λ(t) + ρ θ
                                                                  e (t+1) − δ (t+1) .                                  (A17)
Problem (A15) can be simplified by removing constant terms to get
                            ed (t+1) = arg min fe z(t) , θ c , θ
                                                               ed ; A + IC (θ c ) + I e (θ
                                                                                         ed )
                n                   o                  n        o 
                 θ c(t+1) , θ                                                        D                                 (A18)
                                                    d
                                           θ c ,θ
                                                e
                                                                                     2
                                                            e − δ (t) + ρ θ
                                                            d
                                                                          ed − δ (t) ,
                                                                     
                                                  + λ(t)> θ
                                                                        2            2
                                                                                                     2
                                                               d
                                                                                     ed ) + ρ θ
                                                                                              ed − b
                                                        n       o 
                                                     (t)    c               c
                                                              e ; A + IC (θ ) + I e (θ
                                      = arg min fe z , θ , θ                      D                                    (A19)
                                              ed
                                         θ c ,θ
                                                                                            2        2

                                                                         1
                                                        where b = δ (t) − λ(t) .
                                                                         ρ
A similar treatment to problem (A16) gives us
                                                                  ed (t+1) ; A + IZ (z)
                     n                o             n                    o 
                      δ (t+1) , z(t+1) = arg min fe z, θ c(t+1) , θ                                                    (A20)
                                               δ,z
                                                                                                   2
                                                                     e (t+1) − δ + ρ θ ed (t+1) − δ ,
                                                                     d         
                                                  + ID (δ) + λ(t)> θ
                                                                                    2              2
                                                         c(t+1) ed (t+1)
                                                    n                   o 
                                       = arg min f z, θ
                                                 e             ,θ         ; A + IZ (z)                                 (A21)
                                               δ,z
                                                                            ρ        2           ed (t+1) + 1 λ(t) .
                                                        + ID (δ) +            ka − δk2 where a = θ
                                                                            2                               ρ
                                                                                                                       (A22)
This simplification exposes the independence between z and δ, allowing us to solve problem (A16) independently for z and δ as:
                                                        ρ            2
                              δ (t+1) = arg min ID (δ) + ka − δk2 where a = θ    ed (t+1) + 1 λ(t) ,                   (A23)
                                           δ            2                                   ρ
                                                                ed (t+1) ; A + IZ (z).
                                                   n                   o   
                              z(t+1) = arg min fe z, θ c(t+1) , θ                                                      (A24)
                                           z
So we are able to decompose problem (3) into problems (A19), (A23) and (A24) which can be solved iteratively along with the
λ(t) updates (see Table 1).                                                                                              

                               2    Derivation of ADMM sub-problems in Table 2
Defining U = {u : u = {ui ∈ [0, i ]∀i ∈ [M ]}}, we can go through the mechanics of ADMM to get the augmented Lagrangian
with λ and µi ∀i ∈ [M ] as the Lagrangian multipliers and ρ > 0 as the penalty parameter as follows:
                             ed , δ, u, λ, µ = fe z, θ c , θed ; A + IZ (z) + IC (θ c ) + I e (θ
                                                                                               ed ) + ID (δ)
                                                 n         o 
                  L z, θ c , θ                                                              D
                                                                                  2
                                                         e −δ + ρ θ
                                                        d
                                                                        ed − δ
                                                                
                                                 + λ> θ
                                                                     2            2
                                                          M
                                                          X       n           d
                                                                                  o                              (A25)
                                                IU (u) +      µi gei z, θ c , θ
                                                                              e ; A − i + u i
                                                                 i=1
                                                             M                                  2
                                                            ρX        n
                                                                               e d ; A − i + u i .
                                                                                  o 
                                                        +         gei z, θ c , θ
                                                            2 i=1

ADMM decomposes the optimization variables into two blocks for alternate minimization of the augmented Lagrangian in the
following manner at any ADMM iteration t
                            ed (t+1) , u(t+1) = arg min L z(t) , θ c , θ
                                                                       ed , δ (t) , u, λ(t) , µ(t)
               n                             o                                                    
                 θ c(t+1) , θ                                                                                    (A26)
                                                             d
                                                        θ c ,θ
                                                             e ,u

                                                                             ed (t+1) , δ, u(t+1) , λ(t) , µ(t)
                                 n                o                                                            
                                  δ (t+1) , z(t+1) = arg min L z, θ c(t+1) , θ                                            (A27)
                                                           δ,z
                                                             d                  
                                       λ(t+1) = λ(t) + ρ θ   e (t+1) − δ (t+1)                                            (A28)

                                                                                       ed (t+1) }; A) − i + ui (t+1) .
                                                                                                                    
                            ∀i ∈ [M ], µi (t+1) = µi (t) + ρ gei (z(t+1) , {θ c(t+1) , θ                                  (A29)

Note that, unlike the unconstrained case, the update of the augmented Lagrangian multiplier µi requires the evaluation of the
black-box function for the constraint gi .
   Simplifying problem (A26) gives us
                                            ed ; A
                                    n        o 
                     min    fe z(t) , θ c , θ
                        d
                   θ c ,θ
                        e ,u
                                            "                M                                            2 #
                                          ρ ed           2                                            1 (t)
                                                                               c ed
                                                            X                n      o 
                                                                        (t)
                                       +       θ −b +              gei z , θ , θ ; A − i + ui + µi
                                          2              2
                                                            i=1
                                                                                                      ρ                   (A30)
                                           c
                                           θ ij ∈ Cij ∀i ∈ [N ], j ∈ [Ki ],                   1
                               subject to    ed ∈ D
                                             θ      eij ∀i ∈ [N ], j ∈ [Ki ], where b = δ (t) − λ(t) ,
                                               ij                                              ρ
                                             ui ∈ [0, i ],
                                          

which can be further split into active and inactive set of continuous variables based on the z(t) as in the solution of problem
(A19) (the θ-min problem). The main difference from the unconstrained case in problem (A19) (the θ-min problem) to note here
is that the black-box optimization with continuous variables now has M new variables ui (M is the total number of black-box
constraints) which are active in every ADMM iteration. This problem (A30) can be solved in the same manner as problem (A19)
(θ-min) using SMBO or TR-DFO techniques.
   Simplifying and utilizing the independence of z and δ, we can split problem (A27) into the following problem for δ

                  min
                             ρ
                               kδ − ak22                                                            ed (t+1) + 1 λ(t) ,
                                               subject to δ ij ∈ Dij ∀i ∈ [N ], j ∈ [Ki ] where a = θ                     (A31)
                    δ        2                                                                                 ρ
which remains the same as problem (A23) (the δ-min problem) in the unconstrained case, while the problem for z becomes
                                                  ed (t+1) }; A)
                            min fe(z, {θ c(t+1) , θ
                             z
                                                  M                                                               2
                                               ρX                        ed (t+1) }; A) − i + ui (t+1) + 1 µi (t)        (A32)
                                           +         gei (z, {θ c(t+1) , θ
                                               2 i=1                                                      ρ
                                  subject to zi ∈ {0, 1}Ki , 1> zi = 1, ∀i ∈ [N ].
The problem for z is still a black-box integer programming problem, but now with an updated black-box function and can
be handled with techniques proposed for the combinatorial problem (A24) in the absence of black-box constraints (the z-min
problem).                                                                                                               

                3    Bayesian Optimization for solving the (θ-min) problem on the active set
Problem (10) ((θ-min) on the active set) is a HPO problem. This can be solved with Bayesian optimization (BO) (Shahriari
et al. 2016). BO has become a core component of various AutoML systems (Snoek, Larochelle, and Adams 2012). For any
black-box objective function f (θ) defined on continuous variables θ ∈ C, BO assumes a statistical model, usually a Gaussian
process (GP), for f . Based on the observed function values y = [f (θ (0) ), . . . , f (θ (t) )]> , BO updates the GP and determines the
next query point θ (t+1) by maximizing the expected improvement (EI) over the posterior GP model. Specifically the objective
f (θ) is modeled as a GP with a prior distribution f (·) ∼ N (µ(·), κ(·, ·)), where κ(·, ·) is a positive definite kernel. Given the
observed function values y, the posterior probability of a new function evaluation f (θ) at iteration t + 1 is modeled as a Gaussian
distribution with mean µ(θ) and variance σ 2 (θ) (Shahriari et al. 2016, Sec. III-A), where

                            µ(θ̂) = κ> [Γ + σn2 I]−1 y     and    σ 2 (θ̂) = κ(θ̂, θ̂) − κ> [Γ + σn2 I]−1 κ,                     (A33)

where κ is a vector of covariance terms between θ and {θ (i) }ti=0 , and Γ denotes the covariance of {θ (i) }ti=0 , namely, Γij =
κ(θ (i) , θ (j) ), and σn2 is a small positive number to model the variance of the observation noise.
Remark 1 To determine the GP model (A33), we choose the kernel function κ(·, ·) as the ARD Matérn 5/2 kernel (Snoek,
Larochelle, and Adams 2012; Shahriari et al. 2016),
                                                                    √          √       5
                                           κ(x, x0 ) = τ02 exp(− 5r)(1 + 5r + r2 )                                           (A34)
                                                                                       3
                                   Pd
for two vectors x, x0 , where r2 = i=1 (xi −x0i )2 /τi2 , and {τi }di=0 are kernel parameters. We determine the GP hyper-parameters
ψ = {{τi }di=0 , σn2 } by minimizing the negative log marginal likelihood log p(y|ψ) (Shahriari et al. 2016),
                                                                                         −1
                                        minimize log det(Γ + σn2 I) + y> Γ + σn2 I           y.                              (A35)
                                            ψ

With the posterior model (A33), the desired next query point θ (t+1) maximizes the EI acquisition function
                               θ (t+1) = arg max EI(θ) := y + − f (θ) I(f (θ) ≤ y + )
                                                                         
                                                                                                                                 (A36)
                                                {θ∈C}
                                                                    +          +   
                                                                    y −µ         y −µ
                                          = arg max (y + − µ)Φ             + σφ         ,                                        (A37)
                                                {θ∈C}                  σ            σ

where y + = mini∈[t] f (θ (i) ), namely, the minimum observed value, I(f (θ) ≤ y + ) = 1 if f (θ) ≤ y + , and 0 otherwise
(indicating that the desired next query point θ should yield a smaller loss than the observed minimum loss), and µ & σ 2 are
defined in (A33), Φ denotes the cumulative distribution function (CDF) of the standard normal distribution, and φ is its probability
distribution function (PDF). This is true because substituting (A33) into (A36) allows us to simplify the EI acquisition function
as follows:
                                                                                    y+ − µ
                                            f (θ)−µ
                                                                                            
                                       f 0=     σ          +    0             0
                                 EI(θ)      =       Ef 0 (y − f σ − µ)I f ≤
                                                                                         σ
                                                         +
                                                                                             y+ − µ
                                                                                                 
                                              +           y −µ                0        0
                                       = (y − µ)Φ                 − σEf 0 f I f ≤
                                                            σ                                    σ
                                                         +             y + −µ
                                                          y −µ
                                                                      Z σ
                                       = (y + − µ)Φ               −σ             f 0 φ(f 0 )df 0
                                                            σ          −∞
                                                         +            +            
                                                          y −µ            y −µ
                                       = (y + − µ)Φ               + σφ                   ,
                                                            σ                  σ
                                    R
where the last equality holds since xφ(x)dx = −φ(x) + C for some constant C. Here we omitted the constant C since it does
not affect the solution to the EI maximization problem (A37). With the aid of (A37), EI can be maximized via projected gradient
ascent. In practice, a customized bound-constrained L-BFGS-B solver (Zhu et al. 1997) is often adopted.

              4    Combinatorial Multi-Armed Bandit (CMAB) for (z-min) (problem (A24))

Algorithm A1 Thompson Sampling for CMAB with probabilistic rewards
 1: Input: Beta distribution priors α0 and δ0 , maximum iterations L, upper bound fˆ of loss f .
 2: Set: nj (k) and rj (k) as the cumulative counts and rewards respectively of arm j pulls at bandit iteration k.
 3: for k ← 1, 2, . . . , L do
 4:     for all arms j ∈ [K] do
 5:         αj (k) ← α0 + rj (k), δj (k) ← δ0 + nj (k) − rj (k).
 6:         Sample ωj ∼ Beta(αj (k), δj (k)).
 7:     end for
 8:     Determine the arm selection scheme z(k) by solving
                                           N
                                           X
                                  maximize   (zi )> ω i subject to zi ∈ {0, 1}Ki , 1> zi = 1, i ∈ [N ],                   (A38)
                                      z
                                             i=1

        where ω = [(ω 1 )> , . . . , (ω N )> ]> is the vector of {ωj }, and ω i is its subvector limited to module i.
 9:     Apply strategy z(k) and observe continuous reward re
                                                                                       
                                                                          f (k + 1)
                                                  re = 1 − min max                    ,0 ,1                               (A39)
                                                                              fˆ
        where f (k + 1) is the loss value after applying z(k).
10:    Observe binary reward r ∼ Bernoulli(e r).
11:    for all arms j ∈ [K] do
12:        Update nj (k + 1) ← nj (k) + zj (k).
13:        Update rj (k + 1) ← rj (k) + zj (k)r.
14:    end for
15: end for

                                                                                                    QN
   As mentioned earlier, problem (A24) can be solved as an integer program, but has two issues: (i) i=1 Ki black-box function
queries would be needed in each ADMM iteration, and (ii) integer programming is difficult with the equality constraints
PKi
   j=1 zij = 1∀i ∈ [N ].
   We propose a customized combinatorial multi-armed bandit (CMAB) algorithm as a query-efficient alternative by interpreting
problem (A23) through combinatorial bandits: We are considering bandits due to the stochasticity in the algorithm selection
arising from the fact that we train the algorithm in a subset of pipelines and not the complete combinatorially large set of all
pipelines – the basic idea is to project an optimistic upper bound on the accuracy of the full set of pipelines using Thompson
                                                                                 PN
sampling. We wish to select the optimal N algorithms (arms) from K =               i=1 Ki algorithms based on bandit feedback
(‘reward’) r inversely proportional to the loss f . CMAB problems can be efficiently solved with Thompson sampling (TS)
(Durand and Gagné 2014). However, the conventional algorithm utilizes binary rewards, and hence is not directly applicable to
our case of continuous rewards (with r ∝ 1 − f where the loss f ∈ [0, 1] denotes the black-box objective). We address this issue
by using “probabilistic rewards” (Agrawal and Goyal 2012).
   We present the customized CMAB algorithm in Algorithm A1. The closed-form solution of problem (A38) is given by zji = 1
for j = arg maxj∈[Ki ] ωji , and zji = 0 otherwise. Step 9 of Algorithm A1 normalizes the continuous loss f with respect to its
upper bound fˆ (assuming the lower bound is 0), and maps it to the continuous reward re within [0, 1]. Step 10 of Algorithm A1
converts a probabilistic reward to a binary reward. Lastly, steps 11-12 of Algorithm A1 update the priors of TS for combinatorial
bandits (Durand and Gagné 2014). For our experiments, we set α0 = δ0 = 10. We study the effect of fˆ on the solution of the
(z-min) problem in Appendix 5.

                             5    ADMM with different solvers for the sub-problems
We wish to demonstrate that our ADMM based scheme is not a single AutoML algorithm but rather a framework that can be
used to mix and match different existing (and future new) black-box solvers. First we demonstrate the ability to plug in different
solvers for the continuous black-box optimization involved in problem (10) (θ-min on the active set). We consider a search space
containing 39 scikit-learn (Pedregosa, Varoquaux, and others 2011) ML algorithms allowing for over 6000 algorithm
combinations. The 4 different modules and the algorithms (along with their number and types of hyper-parameters) in each
of those modules is listed in Table A2 in section 7 of the supplement. For the solvers, we consider random search (RND), an
off-the-shelf Gaussian process based Bayesian optimization (Williams and Rasmussen 2006) using scikit-optimize (BO),
our implementation of a Gaussian process based Bayesian optimization (BO*)(see section 3 in the supplement for details), and
RBFOpt (Costa and Nannicini 2018). We use a randomized algorithm selection scheme (z-min) – from each functional module,
we randomly select an algorithm from the set of choices, and return the best combination found. The penalty parameter ρ for the
augmented Lagrangian term in ADMM is set 1.0 throughout this evaluation.

         (i) Oil spill              (ii) Sonar             (iii) Ionosphere              (iv) PC3                   (v) PC4

     Figure A1: Average performance (across 10 runs) of different solvers for the ADMM sub-problem (A19) (Please view in color).

         (i) Oil spill              (ii) Sonar             (iii) Ionosphere              (iv) PC3                   (v) PC4

        Figure A2: Performance inter-quartile range of different solvers for the ADMM sub-problem (A19) (Please view in color).

   We present results for 5 of the datasets in the form of convergence plots showing the incumbent objective (the best objective
value found till now) against the wall clock time. Here tmax = 2048, n = 128, R = 10. The results are presented in figures A1
& A2. The results indicate that the relative performance of the black-box solvers vary between data sets. However, our goal here
is not to say which is best, but rather to demonstrate that our proposed ADMM based scheme is capable of utilizing any solver
for the (θ-min) sub-problem to search over a large space pipeline configurations.
   For the algorithm selection combinatorial problem (z-min), we compare random search to a Thompson sampling (Durand and
Gagné 2014) based combinatorial multi-armed bandit (CMAB) algorithm. We developed a customized Thompson sampling
scheme with probabilistic rewards. We detail this CMAB scheme in Appendix 4 (Algorithm A1) and believe that this might be of
independent interest. Our proposed CMAB scheme has two parameters: (i) the beta distribution priors α0 , δ0 (set to 10), and (ii)
the loss upper bound fˆ (which we vary as 0.3, 0.5, 0.7).

         (i) Oil spill              (ii) Sonar             (iii) Ionosphere              (iv) PC3                   (v) PC4

     Figure A3: Average performance (across 10 runs) of different solvers for the ADMM sub-problem (A24) (please view in color).

    We again consider results in the form of convergence plots showing the incumbent objective (the best objective value found
till now) against the number of pipeline combinations tried (number of “arms pulled”) in figures A3 & A4. The results indicate

         (i) Oil spill              (ii) Sonar             (iii) Ionosphere              (iv) PC3                   (v) PC4

        Figure A4: Performance inter-quartile range of different solvers for the ADMM sub-problem (A24) (Please view in color).

for large number of pulls, all schemes perform the same. However, on 2/5 datasets, CMAB(0.7) (and other settings) outperforms
random search for small number of pulls by a significant margin. Random search significantly outperforms CMAB on the
Ionosphere dataset. The results indicate that no one method is best for all data sets, but ADMM is not tied to a single solver, and
is able to leverage different solvers for the (z-min) step.

                                                         6     Details on the data
We consider data sets corresponding to the binary classification task from the UCI machine learning repository (Asuncion and
Newman 2007), OpenML and Kaggle. The names, sizes and sources of the data sets are presented in Table A1. There are couple
of points we would like to explicitly mention here:
• While we are focusing on binary classification, the proposed ADMM based scheme is applicable to any problem (such as
   multiclass & multi-label classification, regression) since it is a black-box optimization scheme and can operate on any problem
   specific objective.
• We consider a subset of OpenML100 limited to binary classification and small enough to allow for meaningful amount of
   optimization for all baselines in the allotted 1 hour to ensure that we are evaluating the optimizers and not the initialization
   heuristics.
The HCDR data set from Kaggle is a subset of the data presented in the recent Home Credit Default Risk competition
(https://www.kaggle.com/c/home-credit-default-risk). We selected the subset of 10000 rows and 24 features using the following
steps:
• We only considered the public training set since that is only set with labels available
• We kept all columns with keyword matches to the terms “HOME”, “CREDIT”, “DEFAULT”, “RISK”, “AGE”, “INCOME”,
  “DAYS”, “AMT”.
• In addition, we selected the top 10 columns most correlated to the labels column.
• For this set of features, we randomly selected 10000 rows with ≤ 4 missing values in each rows while maintaining the original
   class ratio in the dataset.

Table A1: Details of the data sets used for the empirical evaluations. The ‘Class ratios’ column corresponds to the ratio of the two classes in the
data set, quantifying the class imbalance in the data.

                                  Data                       # rows   # columns       Source        Class ratio
                                  Sonar                        208        61          UCI           1 : 0.87
                                  Heart statlog                270        14          UCI           1 : 0.8
                                  Ionosphere                   351        35          UCI           1 : 1.79
                                  Oil spill                    937        50          OpenML        1 : 0.05
                                  fri-c2                      1000        11          OpenML        1 : 0.72
                                  PC3                         1563        38          OpenML        1 : 0.11
                                  PC4                         1458        38          OpenML        1 : 0.14
                                  Space-GA                    3107         7          OpenML        1 : 0.98
                                  Pollen                      3848         6          OpenML        1:1
                                  Ada-agnostic                4562        48          OpenML        1 : 0.33
                                  Sylvine                     5124        21          OpenML        1:1
                                  Page-blocks                 5473        11          OpenML        1 : 8.77
                                  Optdigits                   5620        64          UCI           1 : 0.11
                                  Wind                        6574        15          OpenML        1 : 1.14
                                  Delta-Ailerons              7129         6          OpenML        1 : 1.13
                                  Ringnorm                    7400        21          OpenML        1 : 1.02
                                  Twonorm                     7400        21          OpenML        1:1
                                  Bank8FM                     8192         9          OpenML        1 : 1.48
                                  Puma8NH                     8192         9          OpenML        1 : 1.01
                                  CPU small                   8192        13          OpenML        1 : 0.43
                                  Delta-Elevators             9517         7          OpenML        1 : 0.99
                                  Japanese Vowels             9961        13          OpenML        1 : 0.19
                                  HCDR                       10000        24          Kaggle        1 : 0.07
                                  Phishing websites          11055        31          UCI           1 : 1.26
                                  Mammography                11183         7          OpenML        1 : 0.02
                                  EEG-eye-state              14980        15          OpenML        1 : 0.81
                                  Elevators                  16598        19          OpenML        1 : 2.24
                                  Cal housing                20640         9          OpenML        1 : 1.46
                                  MLSS 2017 CH#2             39948        12          OpenML        1 : 0.2
                                  2D planes                  40768        11          OpenML        1:1
                                  Electricity                45312         9          OpenML        1 : 0.74

                            7     Search space: Algorithm choices and hyper-parameters
In this section, we list the different search spaces we consider for the different empirical evaluations in section 5 of the paper.

Larger search space
For the empirical evaluation of black-box constraints (section 5 (ii)), ADMM flexibity (section 5 (iii)) and Appendix 5, we
consider 5 functional modules – feature preprocessors, feature scalers, feature transformers, feature selectors, and finally
estimators. The missing handling and the categorical handling is always applied if needed. For the rest of the modules, there
are 8, 11, 7 and 11 algorithm choices respectively, allowing for 6776 possible pipeline combinations. We consider a total of 92
hyperparamters across all algorithms. The algorithm hyper-parameter ranges are set using Auto-sklearn as the reference ( see
https://github.com/automl/auto-sklearn/tree/master/autosklearn/pipeline/components).

Table A2: Overview of the scikit-learn feature preprocessors, feature transformers, feature selectors and estimators used in our empirical
evaluation. The preprocessing is always applied so there is no choice there. Barring that, we are searching over a total of 8×11×7×11 = 6776
possible pipeline compositions.

                                     Module                      Algorithm                       # parameters
                                                                  Imputer                             1d
                                  Preprocessors               OneHotEncoder                          none
                                                                   None∗                             none
                                                                Normalizer                           none
                                                           QuantileTransformer
                                                                                                      2d†
                                                              MinMaxScaler
                                   Scalers ×8                 StandardScaler
                                                                                                     none
                                                                                                     none
                                                               RobustScaler
                                                                 Binarizer                          2c† , 2d
                                                             KBinsDiscretizer                         2d
                                                                   None                               none
                                                         SparseRandomProjection                      1c, 1d
                                                        GaussianRandomProjection                       1d
                                                               RBFSampler                            1c, 1d
                                                                 Nystroem                            2c, 3d
                                 Transformer ×11              TruncatedSVD                             2d
                                                                KernelPCA                            2c, 4d
                                                                  FastICA                              5d
                                                              FactorAnalysis                           3d
                                                                   PCA                               1c, 1d
                                                           PolynomialFeatures                          3d
                                                                   None                               none
                                                              SelectPercentile                         1d
                                                                 SelectFpr                             1c
                                   Selector ×7                   SelectFdr                             1c
                                                                 SelectFwe                             1c
                                                            VarianceThreshold                          1c
                                                                GaussianNB                            none
                                                       QuadraticDiscriminantAnalysis                   1c
                                                        GradientBoostingClassifier                   3c, 6d
                                                           KNeighborsClassifier                        3d
                                                          RandomForestClassifier                     1c, 5d
                                  Estimator ×11            ExtraTreesClassifier                      1c, 5d
                                                            AdaBoostClassifier                       1c, 2d
                                                          DecisionTreeClassifier                     3c, 3d
                                                         GaussianProcessClassifier                     2d
                                                            LogisticRegression                       2c, 3d
                                                               MLPClassifier                         2c, 5d
                             ∗
                              None means no algorithm is selected and corresponds to a empty set of hyper-
                             parameters. † ‘d’ and ‘c’ represents discrete and continuous variables, respectively.

Smaller search space for comparing to AutoML baselines
We choose a relatively smaller search space in order to keep an efficient fair comparison across all baselines, auto-sklearn, TPOT
and ADMM, with the same set of operators, including all imputation and rescaling. However, there is a technical issue – many
of the operators in Auto-sklearn are custom preprocessors and estimators (kitchen sinks, extra trees classifier preprocessor, linear
svc preprocessors, fastICA, KernelPCA, etc) or have some custom handling in there (see https://github.com/automl/auto-sklearn/
tree/master/autosklearn/pipeline/components). Inclusion of these operators makes it infeasible to have a fair comparison across
all methods. Hence, we consider a reduced search space, detailed in Table A3. It represents 4 functional modules with a choice of
6×3×6 = 108 possible method combinations (contrast to Table A2). For each scheme, the algorithm hyper-parameter ranges are
set using Auto-sklearn as the reference (see https://github.com/automl/auto-sklearn/tree/master/autosklearn/pipeline/components).

Table A3: Overview of the scikit-learn preprocessors, transformers, and estimators used in our empirical evaluation comparing ADMM,
auto-sklearn, TPOT. We consider a choice of 6 × 3 × 6 = 108 possible method combinations (see text for further details).

                                   Module                       Algorithm                      # parameters
                                                                 Imputer                            1d
                                 Preprocessors                OneHotEncoder                        none
                                                                                                   none
                                                                   None∗                           none
                                                                Normalizer
                                                                                                    2d†
                                                            QuantileTransformer
                                  Scalers ×6                  MinMaxScaler
                                                                                                   none
                                                                                                   none
                                                              StandardScaler
                                                               RobustScaler                       2c† , 2d
                                                                                                    2d
                                                                 None                              none
                                Transformer ×3                   PCA                              1c, 1d
                                                          PolynomialFeatures                      1c, 2d
                                                              GaussianNB                           none
                                                     QuadraticDiscriminantAnalysis                  1c
                                                      GradientBoostingClassifier                  3c, 6d
                                 Estimator ×6            KNeighborsClassifier                       3d
                                                        RandomForestClassifier                    1c, 5d
                                                         ExtraTreesClassifier                     1c, 5d
                            ∗
                             None means no algorithm is selected and corresponds to a empty set of hyper-
                            parameters. † ‘d’ and ‘c’ represents discrete and continuous variables, respectively.

Note on parity between baselines. With a fixed pipeline shape and order, ADMM & ASKL are optimizing over the same
search space by making a single selection from each of the functional modules to generate a pipeline. In contrast, TPOT can
use multiple methods from the same functional module within a single pipeline with stacking and chaining due to the nature
of the splicing/crossover schemes in its underlying genetic algorithm. This gives TPOT access to a larger search space of
more complex pipelines featuring longer as well as parallel compositions, rendering the comparison somewhat biased towards
TPOT. Notwithstanding this caveat, we consider TPOT as a baseline since it is a competitive open source AutoML alternative to
ASKL, and is representative of the genetic programming based schemes for AutoML. We provide some examples of the complex
pipelines found by TPOT in Appendix 16.

                                           8      Learning ensembles with ADMM
We use the greedy selection based ensemble learning scheme proposed in Caruana et al. (2004) and used in Auto-sklearn as
a post-processing step (Feurer et al. 2015). We run ASKL and ADMM(BO, Ba) for tmax = 300 seconds and then utilize the
following procedure to compare the ensemble learning capabilities of Auto-sklearn and our proposed ADMM based optimizer:
• We consider different ensemble sizes e1 = 1 < e2 = 2 < e3 = 4 . . . < emax = 32.
• We perform library pruning on the pipelines found during the optimization run for a maximum search time tmax by picking
   only the emax best models (best relative to their validation score found during the optimization phase).
• Starting with the pipeline with the best ŝ as the first member of the ensemble, for each ensemble size ej , we greedily add the
   pipeline (with replacement) which results in the best performing bagged ensemble (best relative to the performance ŝ0j on the
   validation set Sv after being trained on the training set St ).
• Once the ensemble members (possibly with repetitions) are chosen for any ensemble size ej , the ensemble members are
   retrained on the whole training set (the training + validation set) and the bagged ensemble is then evaluated on the unseen
   held-out test set Sh to get s0j . We follow this procedure since the ensemble learning uses the validation set and hence cannot
   be used to generate a fair estimate of the generalization performance of the ensemble.
• Plot the (ej , s0j ) pairs.
• The whole process is repeated R = 10 times for the same T and ej s to get error bars for s0j .
For ADMM(BO,Ba), we implement the Caruana et al. (2004) scheme ourselves. For ASKL:SMAC3, we use the post-processing
ensemble-learning based on the example presented in their documentation at https://automl.github.io/auto-sklearn/master/
examples/example_sequential.html.

            (i) Bank8FM                        (ii) CPU small                (iii) Delta Ailerons             (iv) Japanese Vowels

          (v) Page blocks                       (vi) Sylvine                   (vii) Twonorm                       (viii) Wind

Figure A5: Ensemble size vs. median performance on the test set and the inter-quartile range (please view in color). The Aquamarine and Blue
curves correspond to ADMM(BO,Ba) and ASKL respectively.

   The inter-quartile range (over 10 trials) of the test performance of the post-processing ensemble learning for a subset of the
data sets in Table A1 is presented in Figure A5. The results indicate that the ensemble learning with ADMM is able to improve
the performance similar to the ensemble learning in Auto-sklearn. The overall performance is driven by the starting point (the
test error of the best single pipeline, corresponding to an ensemble of size 1) – if ADMM and Auto-sklearn have test objective
values that are close to each other (for example, in Page-blocks and Wind), their performance with increasing ensemble sizes are
very similar as well.

                                     9   Parameter sensitivity check for ADMM
We investigate how sensitive our proposed approach is to the ADMM parameter ρ and CMAB parameter fˆ. For each parameter
combination of ρ ∈ {0.001, 0.01, 0.1, 1, 10} and fˆ ∈ {0.5, 0.6, 0.7, 0.8, 0.9}, in Figure A6 we present the validation error
(averaged over 10 trials) by running our approach on the HCDR dataset (see Appendix 6).

        Figure A6: Validation error of our proposed ADMM-based approach against ADMM parameter ρ and CMAB parameter fˆ

   For this experiment, the results indicate that a large ρ yields a slightly better performance. However, in general, our approach
is not very sensitive to the choice of ρ and fˆ – the range of the objectives achieved are in a very small range. Based on this
observation, we set ρ = 1 and fˆ = 0.7 in all our empirical evaluations of ADMM(BO,Ba) unless otherwise specified.

                                10    Details on the baselines and evaluation scheme
Evaluation scheme. The optimization is run for some maximum runtime T where each proposed configuration is trained on a
set St and evaluated on Sv and the obtained score ŝ is the objective that is being minimized by the optimizer. We ensure that
all the optimizers use the same train-validation split. Once the search is over, the history of attempted configurations is used to
generate a search time vs. holdout performance curve in the following manner for N timestamps:
• For each timestamp ti , i = 1, . . . , N, tN = T , we pick the best validation score ŝi obtained by any configuration found by
   time ti from the start of the optimization (the incumbent best objective).
• Then we plot the (ti , ŝi ) pairs.
• The whole above process is repeated R times for the same T, N and ti s to get inter-quartile ranges for the curves.
For the presented results, T = 3600 seconds, N = 256 and R = 10.

Parity with baselines. First we ensure that the operations (such as model training) are done single-threaded (to the extent possible) to remove the effects of parallelism in the execution time. We set OPENBLAS_NUM_THREADS and OMP_NUM_THREADS
to 1 before the evaluation of ADMM and the other baselines. ADMM can take advantage of the parallel model-training much like
the other systems, but we want to demonstrate the optimization capability of the proposed scheme independent of the underlying
parallelization in model training. Beyond this, there are some details we note here regarding comparison of methods based on
their internal implementation:
• For any time ti , if no predictive performance score (the objective being minimized) is available, we give that method the worst
   objective of 1.0 for ranking (and plotting purposes). After the first score is available, all following time stamps report the best
   incumbent objective. So comparing the different baselines at the beginning of the optimization does not really give a good
   view of the relative optimization capabilities – it just illustrates the effect of different starting heuristics.
• For ADMM, the first pipeline tried is Naive Bayes, which is why ADMM always has some reasonable solution even at the
   earliest timestamp.
• The per configuration run time and memory limits in Auto-sklearn are removed to allow Auto-sklearn to have access to the
   same search space as the ADMM variants.
• The ensembling and meta-learning capabilities of Auto-sklearn are disabled. The ensembling capability of Auto-sklearn is
   discussed further in Appendix 8.
• For ASKL, the first pipeline tried appears to be a Random Forest with 100 trees, which takes a while to be run. For this reason,
   there is no score (or an objective of 1.0) for ASKL until its objective suddenly drops to a more competitive level since Random
   Forests are very competitive out of the box.
• For TPOT, the way the software is set up (to the best of our understanding and trials), scores are only available at the end
   of any generation of the genetic algorithm. Hence, as with ASKL, TPOT do not report any scores until the first generation
   is complete (which implies worst-case objective of 1.0), and after that, the objective drops significantly. For the time limit
   considered (T = 3600 seconds), the default population size of 100 set in TPOT is unable to complete a multiple generations
   on most of the datasets. So we reduce the population size to 50 to complete a reasonable number of generations within the set
   time.
• As we have discussed earlier, TPOT has an advantage over ASKL and ADMM – TPOT is allowed to use multiple estimators,
   transformers and preprocessors within a single pipeline via stacking and chaining due to the nature of the splicing and crossover
   schemes in its underlying genetic algorithm. This gives TPOT access to a larger search space of more complex pipelines
   featuring longer as well as parallel compositions; all the remaining baselines are allowed to only use a single estimator,
   transformers and preprocessor. Hence the comparison is somewhat biased towards TPOT, allowing TPOT to potentially find a
   better objective in our experimental set up. If TPOT is able to execute a significant number of generations, we have observed
   in many cases that TPOT is able to take advantage of this larger search space and produce the best performance.
• Barring the number of generations (which is guided by the maximum run time) and the population size (which is set to 50
   to give us TPOT50), the remaining parameters of mutation rate, crossover rate, subsample fraction and number of parallel
   threads to the default values of 0.9, 0.1, 1.0 and 1 respectively.
   Random search (RND) is implemented based on the Auto-sklearn example for random search at https://automl.github.io/autosklearn/master/examples/example_random_search.html.

Compute machines. All evaluations were run single-threaded on a 8 core 8GB CentOS virtual machines.

                           11   Convergence plots for all data sets for all AutoML baselines.

         (i) Sonar                (ii) Heart-Statlog           (iii) Ionosphere              (iv) Oil spill                (v) fri-c2

         (vi) PC3                     (vii) PC4               (viii) Space GA                (ix) Pollen               (x) Ada-agnostic

        (xi) Sylvine              (xii) Page-blocks            (xiii) Optdigits              (xiv) Wind              (xv) Delta-Ailerons

      (xvi) Ringnorm               (xvii) Twonorm             (xviii) Bank8FM              (xix) Puma8NH               (xx) CPU small

   (xxi) Delta elevators        (xxii) Japanese Vowels         (xxiii) HCDR           (xxiv) Phishing websites      (xxv) Mammography

   (xxvi) EEG-eye-state           (xxvii) Elevators         (xxviii) Cal housing        (xxix) MLSS2017#2              (xxx) Electricity

Figure A7: Search/optimization time vs. median validation performance with the inter-quartile range over 10 trials (please view in color). The
curves colored Aquamarine, Grey, Blue and Black correspond respectively to ADMM(BO,Ba), RND, ASKL and TPOT50.

      12     Computing the group-disparity fairness metric with respect to classification metric ε
Computing the black-box function. The black-box objective f (z, θ, A) is computed as follows for holdout-validation with
some metric ε (the metric ε can be anything such as zero-one loss or area under the ROC curve):
• Let m be the pipeline specified by (z, θ)
• Split data set A into training set At and validation set Av
• Train the pipeline m with training set At to get mAt
• Evaluate the trained pipeline mAt on the validation set Av as follows:
                                           ε (At , Av ) = ε ({(y, mAt (x)) ∀(x, y) ∈ Av }) ,                               (A40)
  where mAt (x) is the prediction of the trained pipeline mAt on any test point x with label y and
                                                       f (z, θ, A) = ε (At , Av ) .                                        (A41)
For k-fold cross-validation, using the above notation, the objective is computed as follows:
• Split data set A into training set Ati and validation set Avi for each of the i = 1, . . . , k folds
• For a pipeline m specified with (z, θ), the objective is computed as
                                                                        k
                                                                     1X
                                                   f (z, θ, A) =           ε (Ati , Avi ) .                                (A42)
                                                                     k i=1

NOTE. The splitting of the data set A in training/validation pairs (At , Av ) should be the same across all evaluations of (z, θ).
Similarly, the k-fold splits should be the same across all evaluations of (z, θ).

Computing group disparate impact. Continuing with the notation defined in the previous subsection, for any given
(test/validation) set Av , assume that we have a (probably user specified) “protected” feature d and a grouping Gd (Av ) =
{A1 , A2 , . . .} of the set Av based on this feature (generally, Aj ∩ Ak = ∅∀j 6= k and ∪Aj ∈Gd (A) Aj = Av ). Then, given the
objective function f corresponding to the metric ε, the corresponding group disparate impact with holdout validation is given as
                                   p(z, θ, A) =      max         ε (At , Aj ) −      min         ε (At , Aj )              (A43)
                                                  Aj ∈Gd (Av )                    Aj ∈Gd (Av )

For k-fold cross-validated group disparate impact with the grouping per fold as Gd (Avi ) = {Ai,1 , Ai,2 , . . .}, we use the
following:
                                        k                                                                  
                                     1X
                        p(z, θ, A) =            max        ε (Ati , Ai,j ) −      min        ε (Ati , Ai,j )           (A44)
                                     k i=1 Ai,j ∈Gd (Avi )                   Ai,j ∈Gd (Avi )

Example considered here:
• Dataset A: Home credit default risk Kaggle challenge
• Metric ε: Area under ROC curve
• Protected feature d: DAYS_BIRTH
• Grouping Gd based on d: Age groups 20-30, 30-40, 40-50, 50-60, 60-70

                                             13    Benchmarking Adaptive ADMM
It is common in ADMM to solve the sub-problems to higher level of approximation in the initial ADMM iterations and to
an increasingly smaller levels of approximation as the ADMM progresses (instead of the same level of approximation for all
ADMM iterations). We make use of this same adaptive ADMM and demonstrate that, empirically, the adaptive scheme produces
expected gains in the AutoML problem as well.
   In this empirical evaluation, we use BO for solving both the (θ-min) and the (z-min) problems. For ADMM with a fixed level
of approximation (subsequently noted as fixed ADMM), we solve the sub-problems to a fixed number I of BO iterations with
I = 16, 32, 64, 128 (also 256 for the artificial objective described in Appendix 15)) denoted by ADMMI(BO,BO) (for example,
ADMM16(BO,BO)). For ADMM with varying level of approximation, we start with 16 BO iterations for the sub-problems and
progressively increase it with an additive factor F = 8 or 16 with every ADMM iteration until 128 (until 256 for the artificial
objective) denoted by AdADMM-F8(BO,BO) and AdADMM-F16(BO,BO) respectively. The optimization is run for 3600
seconds for all the data sets and for 1024 seconds for the artificial objective function. The convergence plots are presented in
Figure A8.

        (i) Artificial black-box objective

         (ii) Bank8FM                        (iii) CPU small                        (iv) fri-c2                           (v) PC4

           (vi) Pollen                       (vii) Puma8NH                       (viii) Sylvine                        (ix) Wind

Figure A8: Search/optimization time (in seconds) vs. median validation performance with the inter-quartile range over 10 trials (please view in
color and note the log scale on both the horizontal and vertical axes).

   The figures indicate the expected behavior – fixed ADMM with small I dominate for small optimization time scale but saturate
soon while fixed ADMM with large I require a significant amount of startup time but then eventually lead to the best performance
for the larger time scales. Adaptive ADMM (for both values of F ) appears to somewhat match the performance of the best fixed
ADMM for every time scale. This behavior is exemplified with the artificial black-box objective (described in Appendix 15) but
is also present on the AutoML problem with real datasets.

                             14     Evaluating the benefits of problem splitting in ADMM
In this empirical evaluation, we wish to demonstrate the gains from (i) splitting the AutoML problem (1) into smaller subproblems which are solved in an alternating fashion, and (ii) using different solvers for the differently structured (θ-min) and
(z-min) problems. First, we attempt to solve the complete joint optimization problem (1) with BO, leading to a Gaussian Process
with a large number of variables. We denote this as JOPT(BO). Then we utilize adaptive ADMM where we use BO for each
of the (θ-min) and (z-min) problems in each of the ADMM iteration, denoted as AdADMM-F16(BO,BO). Finally, we use
adaptive ADMM where we use BO for each of the (θ-min) problem and Combinatorial Multi-Armed Bandits (CMAB) for the
(z-min) problem, denoted as AdADMM-F16(BO,Ba). For the artificial black-box objective (described in Appendix 15), the
optimization is run for 1024 seconds. For the AutoML problem with the actual data sets, the optimization is run for 3600 seconds.
The convergence of the different optimizers are presented in Figure A9.

        (i) Artificial black-box objective

         (ii) Bank8FM                        (iii) CPU small                      (iv) fri-c2                          (v) PC4

           (vi) Pollen                       (vii) Puma8NH                     (viii) Sylvine                       (ix) Wind

Figure A9: Search/optimization time vs. median validation performance with the inter-quartile range over 10 trials (please view in color and
note the log scale on both the horizontal and vertical axes).

   The results for the artificial objective is a case where the black-box optimization dominates the optimization time (since
the black-box evaluation is cheap). In this case, both versions of the adaptive ADMM significantly outperforms the single
BO (JOPT(BO)) for the whole problem 2 seconds onwards, demonstrating the advantage of the problem splitting in ADMM.
Between the two versions of the adaptive ADMM, AdADMM(BO,Ba) (Bandits for (z-min)) outperforms AdADMM(BO,BO)
(BO for (z-min)). This is potentially because BO is designed for continuous variables and is mostly suited for the (θ-min)
problem, whereas the Bandits interpretation is better suited for the (z-min) problem. By the end of the optimization time budget,
AdADMM(BO,Ba) improves the objective by around 31% over JOPT(BO) (5% over AdADMM(BO,BO)), and achieves the
objective reached by JOPT(BO) with a 108× speedup (AdADMM(BO,BO) with a 4× speedup).
   On the AutoML problem with real data sets, the optimization time is mostly dominated by the black-box evaluation, but even
in this case, the problem splitting with ADMM demonstrates significant gains over JOPT(BO). For example, on the fri-c2 dataset,

the results indicate that the operator splitting in ADMM allows it to reach the final objective achieved by JOPT with over 150×
speedup, and then further improves upon that final objective by over 50%. On the Pollen dataset, we observe a speedup of around
25× with a further improvement of 4%. Table A4 & A5 summarize the significant gains from the problem splitting in ADMM.

                                             Dataset       Speedup     Improvement
                                             Artificial      108×             31%
                                             Bank8FM          10×              0%
                                             CPU small         4×              0%
                                             fri-c2          153×             56%
                                             PC4              42×              8%
                                             Pollen           25×              4%
                                             Puma8NH          11×              1%
                                             Sylvine           9×              9%
                                             Wind             40×              0%

Table A4: Comparing AdADMM(BO,Ba) to JOPT(BO), we list the speedup achieved by AdADMM(BO,Ba) to reach the best objective
of JOPT(BO), and any further improvement in the objective. These numbers are generated using the aggregate performance of JOPT and
AdADMM over 10 trials.

                                             Dataset       Speedup     Improvement
                                             Artificial       39×             27%
                                             Bank8FM           2×              5%
                                             CPU small         5×              5%
                                             fri-c2           25×             64%
                                             PC4               5×             13%
                                             Pollen            7×              3%
                                             Puma8NH           4×              1%
                                             Sylvine           2×             26%
                                             Wind              5×              5%

Table A5: Comparing AdADMM(BO,BO) to JOPT(BO), we list the speedup achieved by AdADMM(BO,BO) to reach the best objective
of JOPT(BO), and any further improvement in the objective. These numbers are generated using the aggregate performance of JOPT and
AdADMM over 10 trials.

                                            15    Artificial black-box objective
We wanted to devise an artificial black-box objective to study the behaviour of the proposed scheme that matches the properties
 of the AutoML problem (1) where
1. The same pipeline (the same algorithm choices z and the same hyperparameters θ always gets the same value.
2. The objective is not convex and possibly non-continuous.
3. The objective captures the conditional dependence between zi and θ ij – the objective is only dependent on the hyper-parameters
    θ ij if the corresponding zij = 1.
4. Minor changes in the hyper-parameters θ ij can cause only small changes in the objective.
5. The output of module i is dependent on its input from module i − 1.

Novel artificial black-box objective. To this end, we propose the following novel black-box objective:
• For each (i, j), i ∈ [N ], j ∈ [Ki ], we fix a weight vector wij (each entry is a sample from N (0, 1)) and a seed sij .
• We set f0 = 0.
• For each module i, we generate a value
                                                                        >
                                                              X       wij θ ij
                                                         vi =     zij T
                                                               j
                                                                       1 θ ij
  which only depends on the θ ij corresponding to the zij = 1, and the denominator ensures that the number (or range) of the
  hyper-parameters does not bias the objective towards (or away from) any particular algorithm.
• We generate n samples {fi,1 , . . . , fi,n } ∼ N (fi−1 , vi ) with the fixed seed sij , ensuring that the same value will be produced
  for the same pipeline.
• fi = max |fi,m |.
        m=1,...,n

The basic idea behind this objective is that, for each operator, we create a random (but fixed) weight vector wij and take a
weighted normalized sum of the hyper-parameters θ ij and use this sum as the scale to sample from a normal distribution (with
a fixed seed sij ) and pick the maximum absolute of n (say 10) samples. For the first module in the pipeline, the mean of the
distribution is f0 = 0.0. For the subsequent modules i in the pipeline, the mean fi−1 is the output of the previous module i − 1.
This function possesses all the aforementioned properties of the AutoML problem (1).
   In black-box optimization with this objective, the black-box evaluations are very cheap in contrast to the actual AutoML
problem where the black-box evaluation requires a significant computational effort (and hence time). However, we utilize
this artificial objective to evaluate ADMM (and other baselines) when the computational costs are just limited to the actual
derivative-free optimization.

                        16    TPOT pipelines: Variable length, order and non-sequential
  The genetic algorithm in TPOT does stitches pipelines together to get longer length as well as non-sequential pipelines, using the
  same module multiple times and in different ordering. Given the abilities to
  i. have variable length and variable ordering of modules,
 ii. reuse modules, and
iii. have non-sequential parallel pipelines,
  TPOT does have access to a much larger search space than Auto-sklearn and ADMM. Here are some examples for our
  experiments:

 Sequential, length 3 with 2 estimators
 Input --> PolynomialFeatures --> KNeighborsClassifier --> GaussianNB

 GaussianNB(
   KNeighborsClassifier(
     PolynomialFeatures(
        input_matrix,
        PolynomialFeatures__degree=2,
        PolynomialFeatures__include_bias=False,
        PolynomialFeatures__interaction_only=False
     ),
     KNeighborsClassifier__n_neighbors=7,
     KNeighborsClassifier__p=1,
     KNeighborsClassifier__weights=uniform
   )
 )

 Sequential, length 4 with 3 estimators
 Input
   --> PolynomialFeatures
      --> GaussianNB
         --> KNeighborsClassifier
            --> GaussianNB

 GaussianNB(
   KNeighborsClassifier(
     GaussianNB(
        PolynomialFeatures(
          input_matrix,
          PolynomialFeatures__degree=2,
          PolynomialFeatures__include_bias=False,
          PolynomialFeatures__interaction_only=False
        )
     ),
     KNeighborsClassifier__n_neighbors=7,
     KNeighborsClassifier__p=1,
     KNeighborsClassifier__weights=uniform
   )
 )

Sequential, length 5 with 4 estimators
Input
  --> RandomForestClassifier
      --> RandomForestClassifier
           --> GaussianNB
                --> RobustScaler
                    --> RandomForestClassifier

RandomForestClassifier(
  RobustScaler(
    GaussianNB(
      RandomForestClassifier(
        RandomForestClassifier(
          input_matrix,
          RandomForestClassifier__bootstrap=False,
          RandomForestClassifier__criterion=gini,
          RandomForestClassifier__max_features=0.68,
          RandomForestClassifier__min_samples_leaf=16,
          RandomForestClassifier__min_samples_split=13,
          RandomForestClassifier__n_estimators=100
        ),
        RandomForestClassifier__bootstrap=False,
        RandomForestClassifier__criterion=entropy,
        RandomForestClassifier__max_features=0.9500000000000001,
        RandomForestClassifier__min_samples_leaf=2,
        RandomForestClassifier__min_samples_split=18,
        RandomForestClassifier__n_estimators=100
      )
    )
  ),
  RandomForestClassifier__bootstrap=False,
  RandomForestClassifier__criterion=entropy,
  RandomForestClassifier__max_features=0.48,
  RandomForestClassifier__min_samples_leaf=2,
  RandomForestClassifier__min_samples_split=8,
  RandomForestClassifier__n_estimators=100
)

Non-sequential
Combine[
  Input,
  Input --> GaussianNB --> PolynomialFeatures --> Normalizer
] --> RandomForestClassifier

RandomForestClassifier(
  CombineDFs(
    input_matrix,
    Normalizer(
      PolynomialFeatures(
        GaussianNB(
          input_matrix
        ),
        PolynomialFeatures__degree=2,
        PolynomialFeatures__include_bias=True,
        PolynomialFeatures__interaction_only=False
      ),
      Normalizer__copy=True,
      Normalizer__norm=l2
    )
  ),
  RandomForestClassifier__bootstrap=False,
  RandomForestClassifier__criterion=entropy,
  RandomForestClassifier__max_features=0.14,
  RandomForestClassifier__min_samples_leaf=7,
  RandomForestClassifier__min_samples_split=8,
  RandomForestClassifier__n_estimators=100
)
