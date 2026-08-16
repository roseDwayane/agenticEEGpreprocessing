---
citation_key: "RychertEtAl2025"
title: "Reproducibility Study of Large Language Model Bayesian Optimization"
authors: "Adam Rychert; Gasper Spagnolo; Evgenii Posashkov"
year: 2025
doi: "10.48550/arxiv.2511.18891"
source: "arXiv (2511.18891)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "2511.18891"
conversion: "pdftotext -layout (automated)"
---

# Reproducibility Study of Large Language Model Bayesian Optimization

UL FRI Data Science
                                                                                                                                                   Reproducibility Study

                                                                       @UL-FRI

                                         Reproducibility Study of: Large Language Model Bayesian
                                         Optimization
                                         Adam Rychert1 , Gasper Spagnolo2 and Evgenii Posashkov3

arXiv:2511.18891v1 [cs.CL] 24 Nov 2025
                                          Abstract
                                          In this reproduction study, we revisit the LLAMBO framework of Daxberger et al. (2024), a prompting-based
                                          Bayesian optimization (BO) method that uses large language models as discriminative surrogates and acquisition
                                          optimizers via text-only interactions. We replicate the core Bayesmark and HPOBench experiments under the
                                          original evaluation protocol, but replace GPT-3.5 with the open-weight Llama 3.1 70B model for all text-encoding
                                          components.
                                          Our results broadly confirm the main claims of LLAMBO. Contextual warmstarting via textual problem and
                                          hyperparameter descriptions substantially improves early-regret behaviour and reduces variance across runs.
                                          LLAMBO’s discriminative surrogate is weaker than GP or SMAC as a pure single-task regressor, yet benefits
                                          from cross-task semantic priors induced by the language model. Ablations that remove textual context markedly
                                          degrade predictive accuracy and calibration, while the LLAMBO candidate sampler consistently generates
                                          higher-quality and more diverse proposals than TPE or random sampling. Experiments with smaller backbones
                                          (Gemma 27B, Llama 3.1 8B) yield unstable or invalid predictions, suggesting insufficient capacity for reliable
                                          surrogate behaviour.
                                          Overall, our study shows that the LLAMBO architecture is robust to changing the language-model backbone
                                          and remains effective when instantiated with Llama 3.1 70B. All code and configurations are available at
                                          https://github.com/spagnoloG/llambo-reproducibility.
                                          Keywords
                                          Bayesian Optimization; Large Language Models; LLAMBO; Hyperparameter Optimization; Surrogate Modeling;
                                          Prompt-Based Optimization; Reproducibility
                                         1 ar77558@student.uni-lj.si
                                         2 gs0104@student.uni-lj.si
                                         3 ep84586@student.uni-lj.si

                                                                Introduction                                reasoning, pattern recognition, and contextual understanding.
                                                                                                            LLAMBO explores whether these capabilities can be used to
                                         Black-box function optimization appears in many real-world         support or even replace parts of the BO pipeline by express-
                                         problems such as robotics, experimental design, drug dis-          ing the entire loop in natural language. In the original work,
                                         covery, interface design, and, in machine learning, hyperpa-       an LLM is prompted with dataset and model metadata, past
                                         rameter tuning. Bayesian Optimization (BO) is a standard           evaluations, and the current history, and is then asked to warm-
                                         approach in this setting: it builds a surrogate model from         start the search, act as a discriminative surrogate, and propose
                                         past evaluations and uses an acquisition strategy to propose       new hyperparameter candidates without fitting a conventional
                                         promising candidates, requiring only function evaluations but      probabilistic model.
                                         no gradients or closed-form objective. However, BO is often
                                                                                                                This paper presents a reproducibility study of LLAMBO
                                         used in regimes where data are extremely sparse, so perfor-
                                                                                                            in an open-model setting. We reconstruct the prompting
                                         mance depends critically on search efficiency, the quality of
                                                                                                            pipeline, apply it to hyperparameter tuning tasks from Bayesmark
                                         the surrogate under few observations, and how well prior
                                                                                                            and HPOBench, and compare performance against classical
                                         knowledge can be incorporated across tasks.
                                                                                                            BO baselines such as GP-, SMAC-, and TPE-based optimiz-
                                             These few-shot challenges align naturally with the strengths   ers. In contrast to the original study, which relies on GPT-3.5,
                                         of large language models (LLMs). Modern LLMs are trained           we replace the backbone with open-weight Llama 3.1 70B and
                                         on massive text corpora and show strong abilities in few-shot      briefly probe smaller models as well. This allows us to assess

                                            Reproducibility Study of: Large Language Model Bayesian Optimization — 2/7

both how well the original findings carry over and how sensitive LLAMBO is to the choice and capacity of the underlying
language model.

             Scope of Reproducibility
In this study, we aim to reproduce the main findings reported       Figure 1. Overview of the LLAMBO prompting pipeline.
in the LLAMBO paper [1], which tests whether large lan-             A dataset description (Data Card), model specification
guage models can replace key components of a Bayesian               (Model Card), prior observations, and task instructions are
optimization loop when everything is expressed in natural lan-      combined into structured prompts that guide the LLM
guage. LLAMBO claims that an LLM, prompted with dataset             through (0) zero-shot warmstarting, (1) candidate generation,
and model metadata plus past evaluations, can warmstart the         and (2) surrogate-based performance estimation. Each
search, propose new hyperparameter candidates, and estimate         proposed hyperparameter configuration is evaluated by the
their performance without fitting a conventional surrogate          objective function, added to the data history, and reintroduced
model.                                                              into subsequent prompts, enabling iterative refinement of the
    Our reproduction focuses on the following aspects:              search strategy purely through natural-language interactions.

    • Warmstarting efficiency: verify that contextual, text-
      based warmstarts achieve lower early regret and re-                                 Methodology
      duced variance compared to standard space-filling de-
                                                                    LLAMBO Architecture
      signs (Random, Sobol, Latin Hypercube).
                                                                    LLAMBO replaces the usual components of Bayesian Opti-
                                                                    mization—surrogate models and acquisition functions—with
    • Surrogate behaviour and calibration: compare
                                                                    a large language model that operates entirely through struc-
      LLAMBO’s discriminative surrogate to GP- and RF-
                                                                    tured prompts. Instead of fitting a GP or a Random Forest
      based baselines (GP, SMAC) in terms of predictive
                                                                    and optimizing an acquisition function, the system repeatedly
      accuracy (NRMSE, R2 , regret) and uncertainty quality
                                                                    asks the LLM to propose hyperparameters, reason about pre-
      (LPD, coverage, sharpness).
                                                                    viously observed trials, and estimate the performance of new
                                                                    candidates. Figure 1 illustrates the full loop.
    • Role of textual context: assess the contribution of prob-         At each step, LLAMBO interacts with Bayesmark in a
      lem descriptions and hyperparameter-name embeddings           closed loop: the LLM proposes a configuration, Bayesmark
      via ablations that remove these signals, and measure          evaluates it, and the result is inserted into the next prompt.
      the impact on prediction error and calibration.               The prompting pipeline is built around three stages:

    • Candidate generation quality: evaluate the LLAMBO                 • Zero-shot warmstarting
      candidate sampler against TPE (independent and multi-
      variate) and random sampling using regret-based met-              • Candidate generation
      rics and diversity measures (generalized variance, log-           • Surrogate-style performance estimation
      likelihood).
                                                                       All prompts share two core pieces of information:
    We build directly on the original LLAMBO codebase, replacing all OpenAI API calls with local ollama [2] invocations          • Data Card — dataset metadata (feature dimensionality,
so that the full pipeline can be run with open-weight models.             feature types, task type, etc.)
The implementation is organised primarily through nested
                                                                        • Model Card — the hyperparameter search space for
bash scripts, with prompts and configuration spread across
                                                                          the current model class
multiple files, and the repository does not provide utilities for
aggregating results or plotting figures. To support this study,         These cards ensure the LLM always has access to the
we implemented our own evaluation and plotting scripts for          problem context. As new trials are evaluated, their scores and
Bayesmark and HPOBench, standardised the LLM outputs                hyperparameters are appended to the prompt. The LLM is
into a common JSON format, and reimplemented all base-              instructed to return results in a fixed JSON-like format so that
lines (GP, SMAC, TPE, Random) using established libraries           outputs can be parsed directly into Bayesmark.
in a unified evaluation pipeline. In addition to the original
GPT-3.5 setting, we test LLAMBO with Llama 3.1 70B as the           Zero-Shot Warmstarting
main backbone and briefly probe smaller open models, allow-         In the first stage, LLAMBO asks the LLM to provide an initial
ing us to evaluate both the original claims and the method’s        hyperparameter configuration without seeing any previous
robustness to changes in the underlying language model.             evaluations [3]. The prompt contains only the Data Card, the

                                           Reproducibility Study of: Large Language Model Bayesian Optimization — 3/7

Model Card, and a short instruction describing the goal. Based     as a moderately sized multiclass benchmark. Combined with
solely on this information, the LLM proposes a starting point.     the five Bayesmark model classes (Random Forest, AdaBoost,
This acts as a drop-in replacement for the random, Sobol, or       SVM, Logistic Regression, and a simple neural network),
Latin Hypercube initializations used in standard BO, but relies    these datasets define the 25 optimization tasks reproduced in
entirely on the LLM’s interpretation of the dataset and model      our study.
description rather than a fitted surrogate.                            In the LLAMBO pipeline, each dataset enters the opti-
                                                                   mization loop through the Data Card and conditions all three
Candidate Generation                                               components of the hypothesis generator. During zero-shot
After the first evaluation, LLAMBO enters the iterative candidate- warmstarting, the Data Card is paired with the Model Card
generation loop. At each iteration, the LLM is prompted with       and instructions that guide the LLM to propose an initial hythe dataset and model information together with all previously     perparameter configuration based on dataset characteristics
observed (hyperparameter, performance) pairs. These obser- such as dimensionality, class structure, and feature types. As
vations are supplied in a simple text format. The LLM is           the optimization progresses, the same dataset description is
then asked to suggest new configurations that might improve        used in the candidate-generation prompt, where the LLM reperformance. Unlike classical BO, no explicit acquisition          ceives a history of evaluated configurations (“performance: si ,
function is provided—the LLM decides which regions of the          hyperparameters: hi ”) and reasons about which regions of the
search space to explore or exploit based on the textual history. search space may be promising. In the surrogate-estimation
                                                                   stage, the LLM again leverages the dataset metadata to pre-
Surrogate-Based Performance Estimation
                                                                   dict how new candidates might perform, interpreting inter-
In the final stage, the LLM is asked to estimate the perfor- actions between hyperparameters and dataset structure. For
mance of a candidate before Bayesmark evaluates it. The            consistent evaluation across all methods, LLAMBO also uses
prompt again contains the dataset metadata, model details, Bayesmark’s precomputed performance statistics to compute
and the full evaluation history. The LLM outputs a numerical       regret in a standardized way.
score representing its expectation of how the candidate will
perform. In a conventional BO loop, this role is played by a       Baseline Optimizers
trained surrogate (e.g., GP or Random Forest). Here, the LLM       To evaluate LLAMBO in a full end-to-end hyperparameter
provides these predictions directly through pattern recognition    optimization (HPO) setting, the original paper benchmarks the
in the textual description of the task and the observed history. method against four widely used and methodologically diverse
LLAMBO then uses these estimated scores to rank candidates         baseline optimizers. These baselines represent the dominant
and select which one to evaluate next.                             approaches in modern surrogate-based Bayesian Optimiza-
                                                                   tion, including classical Gaussian Processes(GPs), neural-
Datasets                                                           augmented GPs, density-estimation methods, and random-
Our experiments follow the original LLAMBO setup and use           forest surrogates. All baselines are run under identical condithe five tabular datasets included in the Bayesmark hyperpa-       tions to ensure a fair comparison.
rameter optimization benchmark. These datasets form the
basis of the Data Card component shown in Figure 1. Each           GP-DKL (Deep Kernel Learning Gaussian Process).             GPdataset is provided in a standardized format that includes the     DKL is an advanced Gaussian Process model implemented in
feature matrix X, labels y, train–test splits, and metadata de-    BoTorch. It combines a deep neural network with a GP kernel:
scribing feature types, dimensionality, and task specification.    the network learns a nonlinear feature embedding, and the
This structure allows the dataset information to be inserted       GP operates on this learned space [4]. This hybrid surrogate
directly into the Data Card for every prompt, ensuring that the    retains calibrated uncertainty while offering greater flexibility
LLM always has access to the relevant problem context.             in medium- and high-dimensional search spaces. Because of
    The five datasets cover a range of supervised learning         its strong modeling capacity, GP-DKL is considered one of
problems. The Breast Cancer dataset is a binary classification     the most powerful GP-based baselines in modern Bayesian
task with 569 samples and 30 continuous features, making it        Optimization pipelines.
suitable for evaluating LLAMBO’s warmstarting behaviour.           SKOpt (Gaussian Process from Scikit-Optimize). It pro-
The Diabetes dataset provides a regression problem with 442        vides a classical implementation of Gaussian Process Bayesian
instances and 10 clinical predictors, which is challenging         Optimization [5]. It uses stationary kernels (typically Matern
for early surrogate estimation. The Digits dataset contains        5/2) and standard acquisition functions such as Expected Im-
1,797 samples of handwritten digits represented by 64 pixel-       provement or Lower Confidence Bound. This baseline is
intensity features and tests LLAMBO’s ability to navigate          known for its stability, interpretability, and reliable perfora higher-dimensional multiclass setting. The Iris dataset is       mance on low-dimensional and smooth objective landscapes,
small and low-dimensional (150 samples, four botanical mea-        making it a canonical reference point in HPO studies.
surements), providing a scenario where the model must rely
more heavily on prior knowledge. Finally, the Wine dataset         Optuna (Tree-structured Parzen Estimator).     Optuna’s decontains 178 instances and 13 chemical attributes and serves       fault optimizer implements the Tree-structured Parzen Esti-

                                             Reproducibility Study of: Large Language Model Bayesian Optimization — 4/7

mator (TPE), a non-parametric density-based approach to           Baseline Optimizers
Bayesian Optimization. Rather than modeling the objec-            Before showing the reproduced LLAMBO results, we made
tive function directly, TPE models two conditional densities:     sure that our baseline implementation was completely in line
one over high-performing configurations and one over low-         with the setup described in [1]. We followed the original baseperforming ones. New candidates are drawn from regions            line protocol exactly to make sure that the methods were the
where the ratio of these densities is favorable. This method      same. This verification step showed that our implementation
scales well, handles categorical and conditional search spaces    works the same way as the baselines that were published and
naturally, and is one of the most widely used HPO algorithms      the results from a parallel reproduction.
in practical machine learning systems [6].                            After confirming equivalence, we also implemented a per-
                                                                  task min-max normalization scheme. Bayesmark’s global
SMAC3 (Random-Forest Surrogate).         SMAC3 uses a Ran-
                                                                  scaling sets limits on all datasets, so regret curves never reach
dom Forest regression model as its surrogate, with uncertainty
                                                                  zero. In contrast, per-task normalization changes the perestimated from tree-wise variance. This makes the method
                                                                  formance of each task based on its observed minimum and
robust to non-smooth, noisy, and heterogeneous response sur-
                                                                  maximum. This creates regret curves that stop at zero once
faces. SMAC has a long history of strong performance in
                                                                  the best configuration has been seen. This shows bigger dif-
AutoML, and excels especially in hierarchical or irregular
                                                                  ferences between optimizers at the beginning while keeping
search spaces where GP-based methods may struggle [7].
                                                                  the overall ranking the same. Importantly, we found that both
Evaluation Setup.    All optimizers, including LLAMBO, are        normalization choices lead to the same qualitative ordering of
evaluated under the same conditions:                              the baselines.
    • 5 randomly sampled initial points                                                                     SKOpt (GP)       GP DKL (BoTorch)            OPTUNA    SMAC

    • 25 optimization trials after initialization
                                                                                                                    A Baseline Regret (Bayesmark public tasks)
    • 5 independent runs per task to reduce variance                                              1.0

The experiments cover a total of 30 tasks:                                                        0.8

                                                                      Normalized Average Regret
    • 25 Bayesmark tasks (five datasets crossed with five
                                                                                                  0.6
      model classes),
                                                                                                  0.4
    • 3 private datasets not seen during LLM pretraining,
    • 2 synthetic datasets designed to probe behavior on                                          0.2

      controlled objective landscapes.
                                                                                                  0.0
                                                                                                        0    3           6     9         12         15        18   21     24
    This unified evaluation protocol ensures that differences                                                                              Trials
in performance reflect the behavior of the optimization strate-                                                                    B Private + Synthetic
                                                                                                  1.0
gies themselves rather than differences in initialization or
experimental configuration.                                                                       0.8

                                                                      Normalized Average Regret
                         Results                                                                  0.6

In this section, we present a comprehensive reproduction of                                       0.4

the LLAMBO framework using the Llama 3.1 70B model
as the backbone for all textual encodings. Our goal was to                                        0.2

validate the core claims of the original paper regarding warm-
                                                                                                  0.0
starting efficiency, surrogate modeling behavior, the role of                                           0    3           6     9         12         15        18   21     24
                                                                                                                                           Trials
contextual embeddings, and candidate point sampling. The
reproduced results consistently align with the reported trends:   Figure 2. Baseline Regret on Bayesmark Public Tasks and
contextual information embedded via the Llama 3.1 70B en-         Private + Synthetic Tasks.
coder provides a strong prior that substantially accelerates
optimization, improves cross-task structure, and enables high-        Figures 2 offers an additional analysis to the global norquality candidate generation. Across Figures, we observe the      malization curves. The original evaluation employs fixed
same characteristic signature of LLAMBO—effective warm-           Bayesmark-wide score bounds; however, our supplementary
starts, meta-learned structure in surrogate predictions, clear    per-task min-max scaling uncovers more pronounced earlydegradation when textual information is removed, and state-       regret disparities and generates regret curves that plateau at
of-the-art candidate quality—demonstrating that LLAMBO’s          zero upon reaching the task-specific optimum. This demonbehavior is robust even under a different, larger language        strates that they are stable under alternative normalization
model backbone.                                                   schemes. It also highlights the importance of considering nor-

                                             Reproducibility Study of: Large Language Model Bayesian Optimization — 5/7

malization choices when comparing Bayesian optimization
methods.

Warmstarting strategies in Bayesian Optimization
Figures 3–5 report the reproduced comparison of warmstarting strategies in Bayesian Optimization using the LLAMBO
framework with a Llama 3.1 70B text encoder. We compare
classical space-filling initialization schemes (Random, Sobol,        Figure 4. Correlation structure of initial designs. Pairwise
Latin Hypercube) against contextual warmstarts that lever-            correlation matrices of normalized hyperparameters for
age problem descriptions and hyperparameter semantics (No             different warmstarting strategies on a representative
Context, Partial Context, Full Context).                              dataset/model.
    Figure 3 shows the average simple regret over optimization trials. The reproduced curves match the qualitative behavior of the original work: classical designs (Random, Sobol,           onto a narrow region of the search space, but instead produces
LHCube) yield consistently higher regret, especially in the           diverse yet semantically informed starting points.
early stages, indicating weaker priors and slower convergence
towards the task optimum. In contrast, contextual warmstarts
exhibit uniformly lower regret throughout the optimization
horizon, with the Full Context variant performing best. The
shaded regions further indicate reduced variability across runs
for contextual methods, confirming a more stable and reliable
optimization trajectory.

                                                                      Figure 5. Diversity of initial designs. Generalized variance
                                                                      of normalized hyperparameters for each warmstarting
                                                                      strategy (higher is more diverse).

                                                                          Overall, this reproduction confirms that warmstarting with
                                                                      contextual embeddings substantially improves sample effi-
                                                                      ciency and early-regret performance in Bayesian Optimiza-
                                                                      tion, while maintaining high diversity in the initial design and
                                                                      introducing task-aware structure into the explored hyperpa-
                                                                      rameter space.
Figure 3. Warmstarting regret curves. Average simple
regret over trials for classical space-filling methods (Random,       Discriminative Surrogate Models evaluation
Sobol, LHCube) and contextual warmstarts (No Context,                 Figure 6 presents the reproduced evaluation of the discrimi-
Partial Context, Full Context).                                       native surrogate models under varying numbers of observed
                                                                      data points. The metrics follow the original LLAMBO study
    To better understand how these warmstarts populate the            and assess both predictive accuracy (NRMSE, R2 , regret) and
search space, Figure 4 visualizes the pairwise correlation struc-     uncertainty quality (LPD, coverage, sharpness). The reproture of the initial designs for a representative task (breast, RF).   duced results show the same characteristic pattern: SMAC
Random sampling yields the lowest average absolute corre-             achieves the strongest pure regression performance with the
lation, consistent with nearly independent draws. In contrast,        lowest NRMSE and highest R2 , while Gaussian Processes
contextual warmstarts induce more structured correlation pat-         remain the best calibrated, achieving near-ideal coverage and
terns between hyperparameters, reflecting task-specific priors        stable sharpness. In contrast, LLAMBO and its Monte Carlo
encoded by the language model rather than purely indepen-             variant exhibit weaker single-task regression performance at
dent sampling.                                                        low data regimes and systematically under-estimated uncer-
    Figure 5 complements this view by quantifying the overall         tainty, reflected in higher LPD, low coverage, and overly sharp
diversity of initial designs via the generalized variance of          predictive intervals. However, both LLAMBO variants imthe normalized hyperparameters. Latin Hypercube sampling              prove steadily as more observations become available. These
achieves the highest diversity, as expected from a stratified         results confirm that LLAMBO is not optimized to serve as a
design. However, contextual warmstarts attain diversity levels        standalone surrogate but instead derives its advantage from
comparable to LHCube and substantially higher than Random             cross-task contextualization and meta-learned priors rather
and Sobol, while still encoding meaningful structure. This            than raw single-task predictive accuracy.
indicates that contextual initialization is not merely collapsing

                                         Reproducibility Study of: Large Language Model Bayesian Optimization — 6/7

                                                               Candidate point sampling
                                                               Figure 8 shows the reproduced comparison of candidate gen-
                                                               eration strategies used during acquisition optimization.
                                                                   We evaluate four methods: Random sampling, TPE with
                                                               independent marginals, multivariate TPE, and the LLAMBO
                                                               candidate sampler. The reproduced results closely follow the
                                                               original findings. LLAMBO consistently achieves the lowest
                                                               average regret across candidate sets, indicating that it pro-
                                                               poses high-quality points aligned with promising regions of
                                                               the search space. In terms of best-regret performance, all
                                                               methods eventually identify strong candidates, but LLAMBO
                                                               reaches low-regret solutions earlier and with lower variance.
                                                               The generalized variance results demonstrate that LLAMBO
                                                               maintains a balanced level of diversity—avoiding the collapse
        Figure 6. Discriminative Surrogate models              observed in TPE-based methods while remaining more tar-
                                                               geted than random sampling. Finally, LLAMBO achieves the
                                                               highest log-likelihood under the surrogate density, confirming
Ablation study                                                 that its candidates are well-aligned with the learned model
Figure 7 presents the ablation study comparing the full LLAMBO structure. Overall, this reproduction validates that LLAMBO
model with an uninformed variant that excludes both the prob- provides superior candidate generation, combining quality,
lem description and hyperparameter-name embeddings. The        diversity, and surrogate-consistency more effectively than exreproduced results clearly show that removing textual con- isting sampling strategies.
text significantly degrades surrogate quality across all data
regimes. In terms of predictive accuracy, the uninformed
model exhibits consistently higher NRMSE, with the gap
largest when only a small number of observations are available, indicating impaired generalization and weaker inductive bias. The same trend appears in the uncertainty metrics:
the full LLAMBO model achieves substantially lower LPD,
while the uninformed surrogate produces poorly calibrated
and overconfident predictions. As the number of observations
increases, both models improve, but the full LLAMBO surrogate retains a clear advantage. This ablation confirms that
textual context encoded via the Llama 3.1 70B backbone is
essential for LLAMBO’s performance, providing semantic
structure that enables effective few-shot surrogate modeling.
                                                                           Figure 8. Candidate Point Sampling

                                                                                      Discussion
                                                                Our reproduction confirms that the main qualitative claims
                                                                of LLAMBO hold when GPT-3.5 is replaced by a strong
                                                                open-weight model. Using Llama 3.1 70B as the backbone,
                                                                we recover the reported benefits of contextual warmstarting:
                                                                early-regret is consistently lower, variance across runs is
                                                                reduced, and the optimization trajectories are more stable
                                                                than for classical space-filling designs. At the same time,
                                                                we observe that LLAMBO’s discriminative surrogate is not
                                                                the strongest single-task regressor—Gaussian Processes and
                                                                SMAC typically achieve lower NRMSE and better-calibrated
                                                                uncertainty when trained in isolation. LLAMBO’s advantage
                 Figure 7. Ablation study                       instead arises from cross-task semantic priors encoded in the
                                                                text representations of problem descriptions and hyperparam-
                                                                eter names, which improve sample efficiency once contextual
                                                                information is available.

                                           Reproducibility Study of: Large Language Model Bayesian Optimization — 7/7

    The ablation study further supports this interpretation.
Removing textual context (problem descriptions and hyperparameter semantics) significantly degrades predictive accuracy and calibration, and weakens the warmstarting effect.
Nevertheless, the LLAMBO candidate sampler still produces
high-quality proposals: across benchmarks, its suggestions
are more diverse and better aligned with the underlying objective than those produced by TPE or random sampling, even
when the surrogate is not numerically optimal on a single task.
    In contrast, our attempts to run the full LLAMBO pipeline
with smaller or weaker language models were not successful.
Gemma 27B and Llama 3.1 8B, evaluated under the same
prompts and parsing logic, frequently returned malformed
outputs (invalid JSON, missing hyperparameters) and surrogate scores that did not correlate with observed performance.
This led to unstable optimization loops with constraint violations and highly inconsistent rankings of nearly identical
candidates. These failures indicate that LLAMBO places
non-trivial demands on the underlying LLM: reliable surrogate behaviour requires strong instruction-following, basic
numerical reasoning, and reasonably calibrated scoring. In
our setup, these properties only emerged robustly with the 70B
model, suggesting that—at least with the current prompting
scheme—LLAMBO is not yet robust to substantial reductions
in model capacity.

                        References
[1]   Tennison Liu, Nicolás Astorga, Nabeel Seedat, and Mi-
      haela van der Schaar. Large Language Models to Enhance
      Bayesian Optimization. 2024. Published as a conference
      paper at ICLR 2024.
[2]   Ollama. Ollama, 2024. Accessed: 2025-11-24.
[3]   Neeratyoy Mallik, Maciej Janowski, Johannes Hog, Her-
      ilalaina Rakotoarison, Aaron Klein, Josif Grabocka, and
      Frank Hutter. Warmstarting for scaling language models.
      arXiv preprint arXiv:2402.06184, 2024.
[4]   Martin Wistuba and Josif Grabocka. Few-shot bayesian
      optimization with deep kernel surrogates. 2021.
[5]   F. Pedregosa, G. Varoquaux, A. Gramfort, V. Michel,
      B. Thirion, O. Grisel, M. Blondel, P. Prettenhofer,
      R. Weiss, V. Dubourg, J. Vanderplas, A. Passos, D. Cour-
      napeau, M. Brucher, M. Perrot, and E. Duchesnay. Scikit-
      learn: Machine learning in python. Journal of Machine
      Learning Research, 12:2825–2830, 2011.
[6]   Takuya Akiba, Shotaro Sano, Toshihiko Yanase, Toshiaki
      Ohta, and Masanori Koyama. Optuna: A next-generation
      hyperparameter optimization framework. 2019.
[7]   Marius Lindauer, Katharina Eggensperger, Matthias
      Feurer, André Biedenkapp, Daniel Deng, Carolina Ben-
      jamins, Tanja Ruhkopf, Rojan Sass, and Frank Hutter.
      Smac3: A versatile bayesian optimization package for hy-
      perparameter optimization. Journal of Machine Learning
      Research, 23(1):2475–2483, 2022.
