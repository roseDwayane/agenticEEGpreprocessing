---
citation_key: "XuEtAl2026a"
title: "CoFEH: LLM-driven Feature Engineering Empowered by Collaborative Bayesian Hyperparameter Optimization"
authors: "Beicheng Xu; Keyao Ding; Wei Liu; Yupeng Lu; Bin Cui"
year: 2026
doi: "10.1145/3770855.3817664"
source: "arXiv (2602.09851)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "2602.09851"
conversion: "pdftotext -layout (automated)"
---

# CoFEH: LLM-driven Feature Engineering Empowered by Collaborative Bayesian Hyperparameter Optimization

CoFEH: LLM-driven Feature Engineering Empowered by
                                                     Collaborative Bayesian Hyperparameter Optimization
                                                             Beicheng Xu∗                                              Keyao Ding∗                                        Wei Liu
                                                 School of CS & Key Lab of High                           School of CS & Key Lab of High                   School of CS & Key Lab of High
                                                Confidence Software Technologies                         Confidence Software Technologies                 Confidence Software Technologies
                                                    (MOE), Peking University                                 (MOE), Peking University                         (MOE), Peking University
                                                         Beijing, China                                           Beijing, China                                    Beijing, China
                                                   beichengxu@stu.pku.edu.cn                                maodeshi@stu.pku.edu.cn                           eularioal@stu.pku.edu.cn

                                                                                            Yupeng Lu                                            Bin Cui

arXiv:2602.09851v2 [cs.LG] 21 May 2026
                                                                              School of CS & Key Lab of High                         School of CS & Beijing Key
                                                                             Confidence Software Technologies                    Laboratory of Software and Hardware
                                                                                 (MOE), Peking University                         Cooperative Artificial Intelligence
                                                                                       Beijing, China                                Systems, Peking University
                                                                                   xinkelyp@pku.edu.cn                                      Beijing, China
                                                                                                                                         bin.cui@pku.edu.cn

                                         Abstract                                                                                 ACM Reference Format:
                                         Feature Engineering (FE) is pivotal in automated machine learning                        Beicheng Xu, Keyao Ding, Wei Liu, Yupeng Lu, and Bin Cui. 2026. CoFEH:
                                                                                                                                  LLM-driven Feature Engineering Empowered by Collaborative Bayesian
                                         (AutoML) but remains a bottleneck for traditional methods, which
                                                                                                                                  Hyperparameter Optimization. In Proceedings of the 32nd ACM SIGKDD
                                         operate within rigid search spaces and lack domain awareness.                            Conference on Knowledge Discovery and Data Mining V.2 (KDD ’26), Au-
                                         While Large Language Models (LLMs) offer a promising alternative                         gust 09–13, 2026, Jeju Island, Republic of Korea. ACM, New York, NY, USA,
                                         to generate unbounded operators with semantic reasoning, exist-                          19 pages. https://doi.org/10.1145/3770855.3817664
                                         ing methods focus on isolated subtasks such as feature generation,
                                         falling short of free-form FE pipelines. Moreover, they are rarely                       Resource Availability:
                                         coupled with hyperparameter optimization (HPO) of the down-                              The source code is available at https://doi.org/10.5281/zenodo.20323800.
                                         stream ML model, leading to greedy “FE-then-HPO” workflows                               Extended version with full appendices: https://arxiv.org/pdf/2602.09851.
                                         that cannot capture strong FE–HPO interactions. In this paper, we
                                         present CoFEH, a collaborative framework that interleaves LLM-
                                                                                                                                  1    Introduction
                                         based FE and Bayesian HPO for robust end-to-end AutoML. CoFEH
                                         uses an LLM-driven FE optimizer powered by Tree of Thought                               The success of machine learning (ML) hinges on the synergy be-
                                         (TOT) to explore flexible FE pipelines, a Bayesian optimization                          tween data representation and model capacity [27]. Therefore, Fea-
                                         (BO) module to solve HPO, and a dynamic optimizer selector that                          ture Engineering (FE) serves as the cornerstone of an ML pipeline,
                                         adaptively interleaves FE and HPO steps. Crucially, we introduce a                       directly influencing ML models’ performance and effectiveness.
                                         mutual conditioning mechanism that shares context between LLM                            Broadly defined, FE constitutes a holistic transformation process
                                         and BO, enabling mutually informed decisions. Experiments show                           that bridges raw data and model inputs, encompassing prepro-
                                         that CoFEH outperforms both traditional and LLM-based baselines                          cessing (e.g., imputation, normalization), transformation (e.g., en-
                                         in both standalone FE and joint FE+HPO settings.                                         coding, discretization), generation (synthesizing new interactions),
                                                                                                                                  and selection (filtering irrelevant signals) [43, 62]. However, man-
                                         CCS Concepts                                                                             ual FE is labor-intensive and demands deep domain expertise. To
                                                                                                                                  lower barriers and streamline deployment, the AutoML community
                                         • Computing methodologies → Search methodologies; Ma-
                                                                                                                                  has proposed several methods to automate the FE process [21, 29–
                                         chine learning algorithms.
                                                                                                                                  31, 56, 60]. Many end-to-end AutoML systems also incorporate
                                         Keywords
                                                                                                                                  FE by casting it as a search problem over a predefined space of
                                                                                                                                  transformation operations and pipeline templates [14, 34, 36, 51].
                                         Feature Engineering, Large Language Models, Joint Optimization                           Leveraging an optimizer like Bayesian optimization (BO), they ex-
                                            ∗
                                                                                                                                  plore finite configurations to construct FE pipelines.
                                                Both authors contributed equally to this research.
                                                                                                                                     Despite facilitating automation, these traditional approaches
                                                                                                                                  come with three fundamental drawbacks: (i) They are semantics-
                                                                                                                                  agnostic and depend on expensive trial-and-error without lever-
                                         This work is licensed under a Creative Commons Attribution 4.0 International License.    aging domain priors; (ii) They enforce rigid FE pipeline templates,
                                         KDD ’26, Jeju Island, Republic of Korea                                                  often restricting pipelines to fixed sequences (e.g., generation fol-
                                         © 2026 Copyright held by the owner/author(s).
                                         ACM ISBN 979-8-4007-2259-2/2026/08                                                       lowed by selection [21, 60]) or isolated sub-tasks, precluding dy-
                                         https://doi.org/10.1145/3770855.3817664                                                  namic interleaving or flexible composition; (iii) They restrict the

KDD ’26, August 09–13, 2026, Jeju Island, Republic of Korea                                                         Beicheng Xu, Keyao Ding, Wei Liu, Yupeng Lu, and Bin Cui

          J oint Optimization                                 Sequential Optimization                                        Inter leaved Optimization
                                                                                                        Select LLM                            Selector                 Select BO
                                                                                                          for FE                                                        for HPO
                Optimizer                                                                                                                      Update
                                                      Optimizer A               Optimizer B                                                    Rewards
                                                                                                             Op

                                                                                                                                                                          BO
                                                        FE Space                  HP Space                                    ...              State &
          FE Space         HP Space                                                                      LLM                                  Feedback   FE meta
                                                                                                                               HPO Scores                              HP Space
                                                                                                        Operator
                                                                                                                                               Sharing    feature
                                                    FE with fixed HP         HPO with Optimal FE
                                                                                                        Generator
                                                                                                                               Dataset Info
                                                                                                                       ...
                                                                                                             FE Considering HPO                           HPO on Different FE

     (a) Homogeneous Optimizers                        (b) Heterogeneous Optimizers                                          (c) CoFEH Framework

                                Figure 1: Comparison of optimization workflows: existing methods vs. CoFEH.

search to closed operation libraries (e.g., a small set of built-in                    (i) For C1, we propose an LLM-driven tree-of-thought FE pipeline
preprocessing algorithms or basic arithmetic), which prevents dis-                     optimizer to explore unconstrained pipeline topologies and operacovering task-specific operations beyond predefined primitives.                        tors, achieving truly free-form feature engineering. (ii) For C2, we
   To address these gaps, Large Language Models (LLMs) have been                       introduce a mutual conditioning mechanism, which establishes a
introduced to automated FE [9, 15, 53]. Through domain knowledge                       bidirectional information flow between the LLM and BO, enabling
and code generation capabilities [6], LLMs can propose semanti-                        them to make decisions conditioned on each other to avoid isolated,
cally grounded operations and bespoke logic beyond fixed operation                     sub-optimal tuning. (iii) For C3, we introduce a PUCB-based dylibraries, alleviating drawbacks (i) and (iii) mentioned above. How-                   namic optimizer selector that adaptively allocates the optimization
ever, most existing methods target only a single FE subtask—most                       budget between FE and HPO, facilitating efficient interleaved optinotably feature generation [1, 15, 20, 44, 61]—resulting in a homo-                    mization. (iv) Experiments on 28 public datasets show that CoFEH
geneous and simplistic FE pipeline. This leads to our first challenge:                 outperforms state-of-the-art traditional and LLM-based baselines
C1: Free-form FE. How can we empower LLMs to construct a truly                         in both standalone FE and end-to-end pipeline optimization.
free-form FE pipeline that flexibly composes heterogeneous operations
(preprocessing, transformation, generation, and selection)?
   While LLMs are inherently well-suited for FE, existing methods
                                                                                       2 Background and Motivation
overlook the critical synergy with Hyperparameter Optimization                         2.1 The Formal Machine Learning Pipeline
(HPO) of the downstream ML model. In modern AutoML, HPO is                             The standard supervised machine learning pipeline can be forpredominantly driven by Bayesian optimization (BO) due to its sam-                     malized as a hierarchical optimization problem. Given a dataset
ple efficiency and uncertainty modeling [36, 47, 49]. Consequently,                    D = {(x𝑖 , 𝑦𝑖 )}𝑖=1
                                                                                                       𝑁 where x ∈ X represents the raw input space and
                                                                                                                   𝑖
most LLM-based FE methods that aim for end-to-end performance                          𝑦𝑖 ∈ Y the target, the objective is to learn a mapping from raw data
must attach a BO-based HPO module. Because the two optimizers                          to target that minimizes the generalization error. This process is typare heterogeneous and operate over differently represented spaces,                     ically decomposed into two interconnected sub-problems: Feature
they typically fall back to a greedy sequential routine—first opti-                    Engineering (FE) [21, 29–31, 35] of data and Hyperparameter
mizing FE under a fixed model, then tuning hyperparameters on                          Optimization (HPO) [4, 13, 22, 28] of ML model.
the frozen features (Fig. 1(b)) [15, 20, 37, 44]. This greedy strat-                      To automate pipeline construction, traditional AutoML systems
egy ignores the strong dependency between feature representation                       cast the entire process into a black-box optimization framework, and
and model capacity. In contrast, traditional methods (e.g., Auto-                      leverage method like BO [14, 36, 51, 54], genetic programming [45],
sklearn [14]) naturally enable joint optimization by enforcing a                       reinforcement learning [12], and ADMM [39] to navigate the search
homogeneous search space (Fig. 1(a)). This creates a “capability-                      space. Complementing the traditional approaches, Large Language
integration paradox”: LLMs excel at FE exploration but lag behind                      Models (LLMs) have introduced a knowledge-driven paradigm, acttraditional methods in FE–HPO coupling. To couple LLM-based                            ing as expert-like agents that propose task-adaptive feature trans-
FE with BO-based HPO, we advocate an interleaved optimization,                         formation and model configurations [20, 37, 38, 41, 42, 55].
which raises two further challenges: C2: Collaborative FE–HPO                             In the remainder of this section, we analyze prior work from the
optimization. How can we jointly optimize LLM-based FE and HPO                         perspectives of FE and HPO, and discuss the key challenges and opin a coupled manner, rather than optimizing each in isolation? and C3:                 portunities in jointly optimizing them within a unified framework.
Task-adaptive scheduling. How can we allocate budget between
FE and HPO according to their task-specific utility?
   To address these challenges, we propose CoFEH (Fig. 1(c)), a                        2.2         Feature Engineering
novel framework for holistic ML pipeline optimization. CoFEH                           We define FE in a broad sense, covering the entire process from
is designed to unleash the full potential of LLMs in crafting free-                    raw data to the model input space. Let T denote the space of typed
form, human-expert-like FE pipelines, while coupling this with a                       feature operations, encompassing data preprocessing, transforstate-of-the-art BO–based HPO. Our contributions are as follows:                       mations, feature generation, and selection, etc. Consequently, an

CoFEH : LLM-driven Feature Engineering Empowered by Collaborative Bayesian Hyperparameter Optimization                                                  KDD ’26, August 09–13, 2026, Jeju Island, Republic of Korea

                       preprocessor            rescaler                 balancer                transformer                         2.3    Hyperparameter Optimization
  Mindware
                                         Std    …         Robust           …              PCA           SVD       …    RFE          Given the transformed features X ′ , a learner A is selected to induce
                                                                                                                                    a model. In AutoML, this is formulated as the Combined Algorithm
   OpenFE                 feature generation                                  feature selection
                                                                                      evaluation
                                                                                                                                    Selection and Hyperparameter Optimization (CASH) problem [51]
                        operators : +, −, ×, ÷, Min, Max
                                                                                                                                    In this work, we use HPO as an umbrella term that includes CASH,
   Human             Feature Generation                ＋－×÷ stat. testing                 Feature Selection                         where the configuration space Λ jointly encompasses both the
   Expert            (Complex & Novel Logic)                                            (Domain-aware Selection)
                                                                                                                       shap         choice of the learning algorithm (e.g., XGBoost, MLP) and its asso-
               sin         time-lag              f                                       interaction
         log                  Encoding                                                                    timestamp                 ciated hyperparameters. The goal of HPO is to identify the optimal
                       Transformation                     Human Expert
                                                     (Intuition & Domain Knowledge)
                                                                                        Domain Knowledge                            configuration 𝝀 ∗ that minimizes the generalization error.
                      (Custom & Anomaly                                                    Integration
            PCA            Handling)                                                     (External Data Integration)                   The prevailing approach for solving this task is Bayesian Op-
                                       Unbounded Search Space & Flexible Pipeline                                                   timization (BO) [14, 34, 36, 47, 49, 51, 52]. BO solves black-box
                                                                                                                                    problems by iterating three steps [4, 24, 50]: 1) fit a surrogate
                                                                                                                                    𝑓 on the observed data O = {(𝝀𝑖 , 𝑣𝑖 )}𝑛−1 𝑖=1 ; 2) sample and select
  Figure 2: AutoML vs. human expert: The freedom of FE.                                                                             the next configuration by maximizing an acquisition function 𝛼:
                                                                                                                                    𝝀𝑛 = arg max𝝀 𝛼 (𝝀; 𝑓 ); 3) evaluate 𝝀𝑛 to obtain the performance
                                                                                                                                    𝑣𝑛 and update O ← O ∪ {(𝝀𝑛 , 𝑣𝑛 )}. BO is favored for its principled
FE pipeline can be expressed as a composition of 𝑘 operations,                                                                      modeling of complex search spaces and acquisition-driven balance
                          𝑇 = 𝑡𝑘 ◦ 𝑡𝑘 −1 ◦ · · · ◦ 𝑡 1,                       𝑡𝑗 ∈ T .                                        (1)   of exploration and exploitation. While recent works have tried to
                                                                                                                                    adapt LLMs for HPO [10, 38, 40, 59], LLM optimizers suffer from
The ultimate goal of this FE pipeline is to project the raw input space                                                             inherent limitations, lag behind BO methods, and prove unreliable
X into an optimized latent space X ′ = 𝑇 (X) for the downstream                                                                     in many practical scenarios [23]. They lack a surrogate model for
ML model. Ideally, feature engineering is a creative and semantics-                                                                 objective and uncertainty quantification, and struggle to utilize the
intensive process driven by domain expertise. As illustrated in the                                                                 full optimization history due to context window limits.
bottom panel of Fig. 2, human experts do not adhere to a rigid                                                                         Conclusion #2: BO remains the gold standard for HPO, surtemplate. Instead, they leverage prior knowledge and intuition to                                                                   passing the current reach of LLMs in hyperparameter spaces.
navigate an unbounded search space to build a free-form pipeline.
   However, such an unbounded process remains beyond the reach                                                                      2.4    Joint Optimization of FE and HPO
of traditional optimization algorithms (e.g., BO), which necessitate
well-defined, finite search spaces. Consequently, existing AutoML                                                                   While FE and HPO are often treated as orthogonal tasks, they are
systems typically impose severe structural constraints to make the                                                                  in fact strongly coupled. We formulate AutoML as an optimization
problem tractable [14, 45, 51]. As depicted in the upper panels of                                                                  problem that simultaneously searches for the optimal FE pipeline
Fig. 2 (e.g., MindWare [36], OpenFE [60]), these simplifications                                                                    𝑇 ∗ and model configuration 𝝀 ∗ to minimize the validation loss:
result in three limitations: (i) Semantic agnosticism: Lacking                                                                                                                                   
                                                                                                                                           (𝑇 ∗, 𝝀 ∗ ) = arg min L𝑣𝑎𝑙 A (𝑇 (D𝑡𝑟𝑎𝑖𝑛 ); 𝝀),𝑇 (D𝑣𝑎𝑙 ) , (2)
the capacity to incorporate domain knowledge, these systems rely                                                                                     𝑇 ∈ T 𝑘 ,𝝀 ∈Λ
exclusively on brute-force, data-driven trial-and-error. (ii) Rigid
topology: The pipeline structures are ossified into limited and                                                                     where T 𝑘 represents the unbounded space of feature engineering
fixed sequences, preventing dynamic re-ordering or recursion. For                                                                   pipelines and Λ denotes the hyperparameter space of learner A.
instance, MindWare enforces a strict four-stage workflow, while                                                                        Existing approaches to this problem can be categorized based
OpenFE is confined to a generation-selection pipeline. (iii) Re-                                                                    on the nature of their optimizers, as visualized in Fig. 1. (i) Homostricted operation set: The search is confined to a closed library                                                                  geneous optimizer framework (Fig. 1(a)), employs a single type
of predefined algorithms or mathematical primitives (e.g., +, −, ×),                                                                of optimizer for both tasks, which naturally enables a joint optiprecluding novel operations beyond the system’s hard-coded scope.                                                                   mization. Traditional AutoML systems, such as TPOT [45], Auto-
   To overcome these bottlenecks, recent advances have proposed                                                                     sklearn [14], and Mindware [36], integrate FE and HPO into a
LLM-based FE as an alternative. This direction is part of a broader                                                                 unified yet restricted search space, enabling joint modeling and opeffort to apply LLMs to tabular-data tasks [11]. With extensive                                                                     timization. Similarly, LLM-based frameworks like ML-Master [41]
domain knowledge and code-generation capabilities, LLMs can                                                                         and AIDE [26] treat ML pipeline generation as a unified code synemulate human-like semantic reasoning, enabling flexible feature                                                                    thesis task, prompting LLMs to co-propose feature transformations
transformations beyond closed operation libraries. This potential                                                                   and model configurations within a single reasoning context. (ii)
has sparked a wave of research across the FE spectrum, including                                                                    Heterogeneous optimizer framework (Fig. 1(b)), adopts distinct
preprocessing [8, 53], feature selection [9, 25], and feature genera-                                                               optimization strategies for FE and HPO. This scenario typically
tion [1, 15, 20, 32, 44, 61]. However, these studies typically focus on                                                             necessitates a sequential, greedy strategy: optimizing FE with a
isolated sub-tasks, most notably feature generation. Consequently,                                                                  fixed learner, followed by HPO on the derived feature set. Tradithey yield monolithic pipelines restricted to homogeneous opera-                                                                    tional methods (e.g., AutoFeat [21], OpenFE [60]) optimize features
tions, precluding the dynamic interleaving of diverse FE operations.                                                                via fixed proxy models before performing post-hoc HPO. Similarly,
   Conclusion #1: The intrinsically semantic nature of FE                                                                           LLM-based frameworks such as CAAFE [20] and ELLM-FT [15]
makes LLMs its natural architect, yet fully harnessing this                                                                         focus exclusively on feature evolution, treating HPO as an isolated
power requires adopting truly free-form pipeline topologies.                                                                        downstream task. Crucially, this sequential approach suffers from

KDD ’26, August 09–13, 2026, Jeju Island, Republic of Korea                                                    Beicheng Xu, Keyao Ding, Wei Liu, Yupeng Lu, and Bin Cui

greedy myopia: it fails to capture the strong dependency between                         MCTS Search Tree                    Global Memory                      Steerable Reasoning
features and model hyperparameters. By fixing hyperparameters                                                                Op_1: Log
                                                                                                Ori Dataset s0               (…, …, ∆score:0.1)   Retrieve
                                                                                                                                                                Input
during FE, the optimizer inevitably discards potential high-value                                                                                 ops from       Dataset Info: […]
                                                                                                                             Op_2: PCA            memory         Ancestor FE pipeline: […]
features that require specific hyperparameter tuning to perform                    Dataset s1          …      Dataset s3
                                                                                                                             (…, …, ∆score:0.2)
                                                                                                                                                                 Memory: […]
                                                                                                                             Op_3: Interaction                   Instruction: […]
well, thereby trapping the system in sub-optimal local minima.                                                               (…, …, ∆score:0.2)
                                                                                                                 ①
    Therefore, although Conclusions #1 and #2 suggest that LLMs                       …            Dataset s5 Selection              ...                        Output
                                                                                                                                                                 Plan: Create …
are better suited for FE while BO is more effective for HPO, jointly                                                         Op_n: Encoding                      Code:
                                                                                     Initialization Op.      ② Expansion     (…, …, ∆score:0.1)                     ``` def op(df): … ```
optimizing them is non-trivial due to their inherent heterogeneity.                  Exploration Op.
                                                                                     Exploitation Op.            ④                       Store ops and scores                  ③ Playout
Instead of a greedy sequential optimization, we argue for an inter-                   Selection Path       Backpropagation
                                                                                                                                                                     New Dataset
leaved optimization strategy that continuously evaluates different
(FE pipeline, model configuration) combinations to avoid poor local
optima. This raises two key challenges: (i) mutual conditioning                                         Figure 3: FE workflow of CoFEH.
and shared context—the LLM must refine FE with awareness of
how features interact with different configurations, and BO must
                                                                               a hierarchical search space connected by LLM-generated FE opmodel hyperparameters conditioned on the FE pipeline. Otherwise,
                                                                               erations. We implement this ToT search via a Monte Carlo Tree
if the LLM evaluates feature quality without distinguishing the
                                                                               Search (MCTS) [33], which systematically balances the discovery
underlying configurations (and vice versa for BO), the historical
                                                                               of novel operations with the exploitation of historical knowledge
performance data becomes noisy and misleading, obscuring the
                                                                               through four iterative steps (Fig. 3): 1) Selection: MCTS traverses
true causal impact of any single modification. (ii) adaptive re-
                                                                               from the root (𝑠 0 ) along a selection path to identify the most promissource allocation—the system must decide when to invest budget
                                                                               ing dataset state for further expansion. 2) Expansion: the LLM is
in improving FE versus HPO, since their marginal utility varies
                                                                               prompted to generate a new FE operation to process the dataset in
substantially across tasks (e.g., some datasets are feature-sensitive
                                                                               the selected node. 3) Playout: Utilizing the executable code synwhile HPO yields limited gains, and vice versa).
                                                                               thesized by the LLM, the dataset is transformed and subsequently
    Conclusion #3: Effective AutoML requires interleaving LLM-
                                                                               evaluated through a downstream ML model. This process returns a
based FE and BO-based HPO, with mutual conditioning and
                                                                               validation score 𝑣𝑖 , thereby instantiating a new dataset state 𝑠𝑖+1 as
task-adaptive budget allocation.
                                                                               a successor node in the tree. 4) Backpropagation: The results are
                                                                               propagated back to update three statistics for each ancestor node 𝑠:
3     Method                                                                   visit count 𝑁𝑠 , cumulative reward 𝑅𝑠 , and subtree best performance
In this section, we present CoFEH, a framework for LLM-driven                  𝑣𝑠max (the maximum score observed within its subtree). We first com-
Feature Engineering empowered by Collaborative Hyperparameter                  pute a binary reward 𝑟 = I(𝑣𝑛𝑒𝑤 > 𝑣𝑠max     ), indicating whether a new
                                                                                                                        0
optimization. It comprises three core components: (i) an LLM-based             global optimum has been achieved. Subsequently, we update the
Tree-of-Thought search for FE pipeline; (ii) a mutual condition                statistics: 𝑁𝑠 ← 𝑁𝑠 + 1, 𝑅𝑠 ← 𝑅𝑠 + 𝑟 , and 𝑣𝑠max ← max(𝑣𝑠max, 𝑣𝑛𝑒𝑤 ).
mechanism that enables collaborative tuning between the LLMdriven FE and BO-based HPO; and (iii) a dynamic optimizer selector             3.1.1 Selection Down the MCTS Tree. The selection phase initiates
to adaptively allocate resources across the optimization process.              at the root node 𝑠 0 , representing the original dataset. It traverses the
                                                                               search tree by recursively choosing the child node that maximizes
                                                                               the Upper Confidence Bound for Trees (UCT) criterion [2]:
3.1     LLM-based Feature Engineering                                                                                                  √︄
To effectively navigate the unbounded and semantics-intensive                               ∗                                ′            ln 𝑁𝑠 ª
                                                                                           𝑠 = arg ′ max               ­𝑄 (𝑠 ) + 𝐶 1 ·
                                                                                                                       ©
                                                                                                                                                ®,     (4)
search space of feature engineering, we reformulate the construc-                                      𝑠 ∈children(𝑠 )                     𝑁𝑠 ′
tion of the pipeline from Eq. (1) as a sequential decision-making                                                      «                        ¬
problem. We define a dataset state 𝑠 as the representation of                  where 𝐶 1 is a hyperparameter balancing exploitation and explothe data at a given stage of transformation, where the initial state           ration. The exploitation term 𝑄 (𝑠 ′ ) evaluates the potential quality
𝑠 0 = X is the raw dataset. Each operation 𝑡 𝑗 ∈ T is viewed as an             of the candidate feature transformation path and is defined as:
action that transitions the system from state 𝑠 𝑗 −1 to a new state                                                     𝑅𝑠 ′
                                                                                                           𝑄 (𝑠 ′ ) =        + 𝑣˜𝑠max
                                                                                                                                   ′ ,                 (5)
𝑠 𝑗 = 𝑡 𝑗 (𝑠 𝑗 −1 ). Consequently, the optimization of the FE pipeline is                                              𝑁𝑠 ′
equivalent to finding an optimal sequence of actions (𝑡 1, 𝑡 2, . . . , 𝑡𝑘 )   where the first term 𝑅𝑠 ′ /𝑁𝑠 ′ represents the average reward, reflectthat maximizes the downstream performance reward:                              ing the historical success rate of discovering superior states along
                                                                               this branch. The second term 𝑣˜𝑠max       ′    denotes the normalized best
                  max Reward(𝑡𝑘 ◦ 𝑡𝑘 −1 ◦ · · · ◦ 𝑡 1 (𝑠 0 )).          (3)    validation performance observed within the subtree rooted at 𝑠 ′
                 𝑡 1 ,...,𝑡𝑘
                                                                               (min–max normalized using the global range of observed metric
Standard linear prompting is ill-suited for this complex search space          scores). This formulation of 𝑄 (𝑠 ′ ) ensures that the search priorias it follows a singular reasoning path without the capacity for back-         tizes paths exhibiting both high absolute performance (via 𝑣˜ max ) and
tracking. To bridge this gap, we adopt the Tree of Thought (ToT)               strong potential for iterative improvement (via the average reward).
paradigm [57], enabling the LLM to explore multiple reasoning                  Through this mechanism, the algorithm identifies a selection path
branches. As illustrated in the search tree (Fig. 3, left), intermedi-         extending from 𝑠 0 to a non-fully expanded target node, as shown
ate dataset states (𝑠𝑖 ) are treated as “thoughts”—discrete nodes in           by the bold-bordered nodes in the left panel of Fig. 3.

CoFEH : LLM-driven Feature Engineering Empowered by Collaborative Bayesian Hyperparameter Optimization   KDD ’26, August 09–13, 2026, Jeju Island, Republic of Korea

3.1.2 Expansion Through Steerable Reasoning. Upon identifying                       the steerable reasoning prompt. This dual-metric selection ensures
a target node 𝑠 base through UCT policy, the steerable reasoning                    the LLM distills insights from both breakthrough transformations
module expands the search tree by synthesizing a new FE opera-                      and marginal refinements recorded in the search history.
tion. We construct a structured prompt for the LLM comprising the
following core components: (i) Dataset info (𝜓 ): Semantic descrip-                 3.2    Collaborative Tuning with HPO
tions and statistical metadata of the base dataset to provide domain               The final efficacy of an FE pipeline 𝑇 is inextricably linked to the
context. (ii) Ancestor FE pipeline (𝑇anc ): The sequence of operations             hyperparameters 𝝀 of the downstream ML model. A decoupled
previously applied from 𝑠 0 to 𝑠 base , which defines the current data             approach, where FE is optimized independently of HPO, often restate. (iii) Memory (M𝑠base ): A collection of high-performing op-                 sults in the greedy myopia discussed in Section 2.4—the premature
erations archived from previous successful trials. Crucially, this                 rejection of promising feature transformations simply because they
component is provided only under the Exploitation directive to                     perform poorly under default model configurations. To address
facilitate refinement. (iv) Directive (𝑑): An optimization instruction             this, we propose a collaborative tuning framework that performs
𝑑 ∈ {Initialization, Exploration, Exploitation} that explicitly                    an interleaved exploration of the joint space of (𝑇 , 𝝀) combinations
steers the LLM’s strategy. Formally, the LLM generates a reasoning                 to ensure the discovery of global optima. The core challenge of
chain R followed by the code for a new operation 𝑡 new :                           joint optimization is the bidirectional dependency: BO requires a
                                                                                  promising feature set to optimize 𝝀, while the LLM requires an
                  (R, 𝑡 new ) = LLM 𝜓,𝑇anc, M𝑠base , 𝑑 .            (6)
                                                                                   optimized model to accurately evaluate the potential of a pipeline
The synthesized operation code is then executed to transform the                   𝑇 . We resolve this through a mutual conditioning mechanism.
base dataset, instantiating a new dataset state: 𝑠 new = 𝑡 new (𝑠 base ).
                                                                                    3.2.1 BO-based HPO Conditioned on FE. In contrast to conven-
Crucially, the directive 𝑑 explicitly governs the search behavior:
                                                                                    tional HPO that operates on a static dataset, the BO conditioned on
   • Initialization is invoked specifically for the root node 𝑠 0                   FE must navigate a dynamic search tree containing multiple dataset
     to generate a high-quality initial FE operation.                               states. The primary challenge lies in modeling the joint influence
   • Exploration encourages the LLM to propose novel opera-                         of the FE pipeline and configuration to determine which specific
     tions to probe unknown regions of the search space.                            dataset state 𝑠 paired with which configuration 𝝀, yields the global
   • Exploitation distills elite experiences and operations from                    optimum. To achieve this, we redefine the two core components of
     Mglobal to refine the current dataset.                                         BO: the surrogate model and the acquisition function optimizer.
                                                                                    Surrogate model: Since the FE pipeline lacks an explicit, continu-
   Detailed prompt template are shown in Appendix A. The root
                                                                                    ous search space, we utilize meta-features 𝜙 (𝑠) to characterize the
node 𝑠 0 is treated as non-fully expanded until the Init. directive
                                                                                    dataset state after transformations. This allows the surrogate model
has been executed five times. Any other node is considered fully
                                                                                    to map discrete tree nodes into a representative feature space. We
expanded only after both Exploration and Exploitation direc-
                                                                                    formalize the training dataset for the surrogate model 𝑓 as:
tives have been performed twice. This ensures that each promising
                                                                                                                            
dataset state is thoroughly investigated through both creative dis-                              DBO = [𝜙 (𝑠𝑖 ), 𝝀𝑖,𝑗 ], 𝑣𝑖,𝑗 | 𝑠𝑖 ∈ Vtree, ∀𝑗 ,      (7)
covery and historical refinement before the search moves deeper.
                                                                                   where Vtree is the set of all nodes in the MCTS tree. 𝝀𝑖,𝑗 and 𝑣𝑖,𝑗
3.1.3 Global Memory and Operation Retrieval. As illustrated in                     denote the 𝑗-th ML configuration and its score evaluated on node 𝑠𝑖 .
the central module of Fig. 3, every historical operation 𝑡 gener-                  We employ Random Forest (RF) [24] as the surrogate model due to
ated during the search is archived in the Global Memory Mglobal .                  its support for categorical variables and computational efficiency.
Each entry is stored as a tuple (R, F𝑡 , 𝑣, Δ𝑣), which includes: (i) the           Acquisition function optimizer: To identify the most promising
reasoning chain R, (ii) the subset of features F𝑡 required for the                 (𝑠, 𝝀) pair, the optimizer explores the joint space through a hybrid
operation, which is parsed from the structured format of R, (iii) the              sampling strategy to construct a candidate pool Pcand :
resulting performance score 𝑣, and (iv) the relative improvement                       • Local search: For each node in the tree, we perform perturba-
Δ𝑣 = 𝑣 − 𝑣 base achieved over its parent node.                                           tions around all its historically evaluated configurations within
   To support the Exploitation directive, we employ a two-stage                          the hyperparameter space Λ. These perturbed configurations
retrieval mechanism to identify high-utility operations: 1) Func-                        are paired with the corresponding node’s meta-features to
tional filtering: We first ensure semantic compatibility by match-                       capture local improvements.
ing the required feature set F𝑡 with the features currently available
in state 𝑆 base . Only operations whose functional dependencies are                    • Random search: We perform global random sampling across
fully satisfied by the current dataset are retained as valid candidates.                 the hyperparameter space Λ. Each sampled configuration is
2) Pareto-based selection: From the filtered candidates, we iden-                        combined with the meta-features of every node in the current
tify high-quality operations by balancing absolute performance 𝑣                         tree to ensure a broad exploration of the joint space.
and relative gain Δ𝑣. We prioritize operations with high Δ𝑣 for their               Finally, the surrogate model predicts the performance of the comstrong transformative potential, as well as those with high 𝑣, which                binations in the candidate pool Pcand , after which an acquisition
represent the ability to refine performance even when the pipeline                  function (e.g., Expected Improvement [19]) quantifies their utility
has reached well-performing regimes. Formally, we construct a                       and selects the optimal combination (𝑠 ∗, 𝝀 ∗ ). This mechanism si-
Pareto frontier in the (𝑣, Δ𝑣) objective space and select the non-                  multaneously recommends the next ML configuration and identifies
dominated operations to serve as “elite experiences” M𝑠base within                  the specific dataset state 𝑠 ∗ best positioned to leverage its potential.

KDD ’26, August 09–13, 2026, Jeju Island, Republic of Korea                                       Beicheng Xu, Keyao Ding, Wei Liu, Yupeng Lu, and Bin Cui

3.2.2 LLM-based FE Conditioned on HPO. On the other hand, the                 adapted by task-specific performance gains 𝑄 (𝑎). (ii) Theorem 3.1
MCTS-based FE search must be informed by the HPO process. We                  guarantees reaching a 5:5 budget equilibrium in neutral reward
modify the original FE search logic to incorporate two levels of HPO-         scenarios (i.e., when the exploitation term 𝑄 (𝑎) is not considered),
driven conditioning: (i) During the Selection phase, the maximum              making the actual allocation fundamentally task-governed. In sumperformance 𝑣˜𝑠max
                 ′  in Eq. (5) is determined by HPO; specifically,            mary, FE and HPO engage in a dynamic competition for computawhen the BO discovers a superior validation score 𝑣 ∗ at any given            tional resources, where the final allocation is adaptively steered by
node, a localized update propagates this new performance "ceiling"            task-specific empirical rewards to reach the global optimum.
back through the node’s ancestors to prioritize that branch in future
selection cycles. This enables the MCTS to identify FE pipelines              3.4    Algorithm Summary
that exhibit the greatest synergy with optimized ML configurations.           CoFEH iteratively performs joint optimization in four steps:: 1) The
(ii) During the Playout phase, the new child node 𝑠 new inherits the          optimizer selector applies the PUCB rule to determine whether
best-performing ML configuration from its parent for evaluation,              to execute FE or HPO; 2) The selected module is executed, where
ensuring that new feature operations are immediately evaluated at             FE utilizes MCTS to extend the most promising feature pipeline 𝑇
their highest possible capacity rather than under default settings.           given the current HPO state, while HPO employs BO to identify the
                                                                              optimal configuration 𝝀 conditioned on all dataset states; 3) The
3.3     Dynamic Optimizer Selector                                            proposed (𝑇 , 𝝀) pair is evaluated on the validation set to obtain a
While FE and HPO exhibit strong synergy, their relative contribu-             score; 4) This score is returned as a reward signal to the selector
tion to performance gains varies significantly across datasets and            for future decisions. This process repeats until the total budget 𝑀
search stages. To optimize resource allocation, we model the selec-           is exhausted, after which CoFEH returns the optimal pair (𝑇 ∗, 𝝀 ∗ ).
tion between FE and HPO as a Multi-Armed Bandit (MAB) problem.
We employ a Predictor Upper Confidence Bound (PUCB) [48] policy               4 Experiment
                                                                              4.1 Experiment Setup
to dynamically decide which optimizer to execute at each step 𝑚:
                                            √︁Í                     !
   ∗
  𝑎 = arg max          𝑄 (𝑎) + 𝐶 2 · 𝜔𝑎 (𝑚)
                                               𝑎 ′ ∈ {FE, HPO} 𝑁𝑎 ′
                                                                      , (8)   Baselines. We evaluate CoFEH against five representative base-
          𝑎∈ {FE, HPO}                              1 + 𝑁𝑎                    lines, spanning both traditional and LLM-based methodologies: —
                                                                              Two traditional automated FE methods: (i) OpenFE [60] utilizes
where 𝑁𝑎 is the number of times action 𝑎 has been selected. 𝐶 2 is
                                                                              feature boosting and two-stage pruning for automated feature gena hyperparameter controlling exploration pressure. 𝜔𝑎 (𝑚) is the
                                                                              eration; (ii) Mindware [36], an end-to-end AutoML system that
time-varying prior weight for action 𝑎 at iteration 𝑚. 𝑄 (𝑎) is the
                                                                              automates the entire pipeline from preprocessing to HPO with BO; —
exploitation term, formulated as the empirical success rate of action
                                                                              Three LLM-based FE methods: (iii) OCTree [44] leverages LLMs and
𝑎 in achieving a performance breakthrough. Specifically, 𝑄 (FE)
                                                                              decision tree reasoning as linguistic feedback for iterative feature
represents the proportion of trials where the generated FE operation
                                                                              generation; (iv) ELLM-FT [15] integrates evolutionary strategies
yields a score surpassing that of its parent node. 𝑄 (HPO) is defined
                                                                              with LLMs to discover optimal feature transformation sequences;
as the frequency with which HPO on a given node discovers a
                                                                              and (v) LFG [61] conducts a guided tree search over a predefined
configuration that exceed the node’s historical best performance.
                                                                              operation set to generate new features via LLMs.
Prior weight scheduling. we define the prior weights 𝜔𝑎 (𝑚) as
                                                                              Datasets and Metrics. Following OCTree [44], we incorporate 19
linear functions of the search progress 𝑚/𝑀 (𝑀 is the total budget):
                                                                              classification datasets benchmarked by Grinsztajn et al. [18]. This is
   • 𝜔 FE (𝑚) = 𝑝 1 − (𝑝 1 − 0.5) 𝑚
                                  𝑀 : Starts at 𝑝 1 and decays to 0.5.        supplemented by 9 regression datasets from OpenML [5] and Kaggle.
   • 𝜔 HPO (𝑚) = 𝑝 2 + (0.5 − 𝑝 2 ) 𝑚
                                    𝑀 : Starts at 𝑝 2 and increases to 0.5.
                                                                              Dataset details are provided in Appendix C.1. We use the classifica-
                                                                              tion error (1 − 𝑎𝑐𝑐𝑢𝑟𝑎𝑐𝑦) for classification and 𝑚𝑒𝑎𝑛 𝑠𝑞𝑢𝑎𝑟𝑒𝑑 𝑒𝑟𝑟𝑜𝑟
Given 𝑝 1 + 𝑝 2 = 1, these prior weights can be unified using a step
                𝑝 −0.5                                                        (MSE) for regression tasks as performance metrics.
parameter 𝛿 = 1 𝑀 : 𝜔 FE (𝑚) = 𝑝 1 − 𝛿𝑚 and 𝜔 HPO (𝑚) = 𝑝 2 + 𝛿𝑚.
                                                                              Downstream Models. We evaluate the frameworks across three
   Theorem 3.1 (Budget Eqilibrium). Consider the rule in Eq. (8)              downstream model scenarios: (i) XGBoost [7], (ii) MLP [17], and
under a neutral reward signal (𝑄 (𝑎) = const). Let 𝑀 ∈ Z+ be the              (iii) CASH [51]: the framework must simultaneously select the best
total budget. If the initial bias satisfies 0.5 ≤ 𝑝 1 < 𝑀+1.5
                                                        𝑀+3 , the linear
                                                                              model algorithm (e.g., RF, MLP) and its optimal parameters. The hyscheduling of 𝜔 FE and 𝜔 HPO , which converges to an equilibrium of 0.5       perparameter spaces for all scenarios are detailed in Appendix C.2.
at 𝑚 = 𝑀, guarantees a balanced budget distribution:                          Basic Settings. While it takes a different amount of time to eval-
                                                                        uate the same model on different datasets, we use the evaluation
                                             𝑀     𝑀
                  𝑁 FE (𝑀), 𝑁 HPO (𝑀) ∈          ,                            iterations as the unit of budget. We compare all methods under
                                             2      2
                                                                              two settings: (i) Standalone FE: conduct an FE search for a maxi-
   The proofs are provided in Appendix B. By leveraging this PUCB-            mum of 100 iterations while maintaining default downstream model
based mechanism, the framework achieves a dual advantage: (i)                 hyperparameters. (ii) Joint FE+HPO: A total budget of 200 itera-
With prior weight, it implements a learnability warmup that pri-              tions is allocated for full-pipeline optimization. Mindware leverages
oritizes FE initially to ensure the data is "model-ready," shielding          SMAC [24] (a BO variant) to simultaneously navigate the unified
HPO from deceptive, noise-driven signals inherent in raw features.            space of FE and HPO. Other decoupled baselines (OpenFE, OCTree,
Unlike traditional decoupled methods that optimize FE and HPO                 ELLM-FT, and LFG) execute 100 sequential rounds of FE followed
sequentially, our approach facilitates a balanced co-optimization             by 100 rounds of HPO on the fixed optimal FE pipeline. Critically,

CoFEH : LLM-driven Feature Engineering Empowered by Collaborative Bayesian Hyperparameter Optimization                 KDD ’26, August 09–13, 2026, Jeju Island, Republic of Korea

Table 1: Comparison in standalone FE and joint FE+HPO scenarios. We report test error (%) for classification and test MSE for
regression across 3 runs (Mean ± Std, ↓). Best and second-best results are bolded and underlined. Bottom rows show the average
rank (Avg. Rank) per scenario, while Imp. (%) quantifies the relative error reduction of joint FE+HPO relative to standalone FE.

                                                      Standalone FE                                                                   Joint FE + HPO
 Dataset                 Mindware      OpenFE       OCTree     ELLM             LFG         CoFEH       Mindware       OpenFE        OCTree      ELLM          LFG         CoFEH
                                                                              Classification Tasks
 rl                     19.65 ± 0.81 19.48 ± 0.20 22.80 ± 0.38 17.84 ± 1.80 22.77 ± 0.81 17.84 ± 2.30 23.20 ± 0.30 19.52 ± 0.10 22.98 ± 0.40 19.63 ± 1.90 22.87 ± 0.84 21.35 ± 0.11
 electricity             9.08 ± 0.13 8.47 ± 0.07 8.54 ± 0.10 7.71 ± 0.21 8.84 ± 0.24 7.31 ± 0.35 8.28 ± 0.17 7.93 ± 0.07 7.76 ± 0.31 6.79 ± 0.48 7.65 ± 0.26 7.15 ± 0.14
 compass                23.54 ± 0.07 19.94 ± 0.17 25.34 ± 0.18 22.93 ± 0.72 22.70 ± 0.59 22.25 ± 0.08 23.63 ± 0.23 20.79 ± 0.09 23.44 ± 0.11 23.63 ± 0.75 21.76 ± 0.17 19.57 ± 0.44
 wine                   23.32 ± 0.25 21.43 ± 0.51 22.72 ± 0.80 22.19 ± 0.16 23.13 ± 0.27 19.45 ± 0.18 22.70 ± 1.10 22.06 ± 0.55 23.46 ± 0.91 23.54 ± 0.44 23.41 ± 0.48 19.50 ± 0.62
 house_16H              12.30 ± 0.04 13.03 ± 0.18 11.62 ± 0.10 11.76 ± 0.27 11.60 ± 0.18 12.06 ± 0.05 11.92 ± 0.24 12.38 ± 0.23 12.18 ± 0.25 12.31 ± 0.42 11.96 ± 0.19 12.27 ± 0.04
 Magic                  13.92 ± 0.26 11.87 ± 0.23 14.21 ± 0.15 14.13 ± 0.15 14.14 ± 0.05 12.86 ± 0.26 13.12 ± 0.36 10.99 ± 0.22 14.42 ± 0.15 14.39 ± 0.28 14.24 ± 0.14 13.53 ± 0.40
 higgs                  28.10 ± 0.13 29.67 ± 0.09 28.22 ± 0.01 28.18 ± 0.02 27.74 ± 0.07 27.14 ± 0.32 27.22 ± 0.06 27.24 ± 0.09 27.46 ± 0.09 27.45 ± 0.09 27.31 ± 0.11 26.35 ± 0.02
 jannis                 20.33 ± 0.13 22.03 ± 0.19 20.99 ± 0.13 21.05 ± 0.06 21.02 ± 0.08 20.68 ± 0.15 19.65 ± 0.12 20.77 ± 0.23 20.62 ± 0.15 20.56 ± 0.29 20.41 ± 0.01 20.34 ± 0.14
 credit                 25.10 ± 0.36 26.21 ± 0.01 24.31 ± 0.15 24.47 ± 0.28 24.13 ± 0.29 24.21 ± 0.03 22.59 ± 0.31 22.94 ± 0.07 22.95 ± 0.09 22.92 ± 0.07 22.90 ± 0.24 22.58 ± 0.21
 eye_movements          30.88 ± 1.70 36.96 ± 1.10 35.06 ± 0.68 29.84 ± 1.20 36.49 ± 1.40 30.31 ± 1.50 36.02 ± 1.80 35.98 ± 0.70 35.57 ± 0.79 30.54 ± 1.50 36.99 ± 1.40 29.46 ± 0.95
 kddCup09               21.07 ± 0.47 22.69 ± 0.59 20.61 ± 0.64 20.74 ± 0.47 19.75 ± 0.63 20.15 ± 0.30 19.51 ± 0.23 21.01 ± 0.62 20.10 ± 0.54 19.99 ± 0.27 19.68 ± 0.07 19.35 ± 0.44
 road-safety            21.10 ± 0.01 20.92 ± 0.09 21.94 ± 0.28 21.42 ± 0.20 19.87 ± 0.68 19.56 ± 0.07 20.49 ± 0.05 19.60 ± 0.23 21.04 ± 0.28 20.69 ± 0.86 19.99 ± 0.41 18.53 ± 0.28
 bank-marketing         21.68 ± 0.25 22.16 ± 0.27 20.64 ± 0.23 20.80 ± 0.36 21.00 ± 0.11 21.38 ± 0.30 19.79 ± 0.13 20.55 ± 0.01 20.16 ± 0.07 20.31 ± 0.67 20.15 ± 0.14 19.72 ± 0.16
 phoneme                14.30 ± 0.18 13.51 ± 0.11 13.88 ± 0.26 14.30 ± 0.18 14.16 ± 0.18 12.37 ± 0.30 14.16 ± 0.19 13.04 ± 0.05 14.06 ± 0.33 14.30 ± 0.19 14.30 ± 0.20 12.48 ± 0.10
 covtype                11.64 ± 0.57 10.69 ± 0.01 13.52 ± 0.22 12.99 ± 0.04 13.75 ± 1.40 11.12 ± 0.61 10.07 ± 0.13 15.92 ± 11.00 10.06 ± 0.15 9.98 ± 0.51 10.23 ± 1.40 7.87 ± 0.23
 california              9.72 ± 0.10 9.86 ± 0.08 9.47 ± 0.12 9.58 ± 0.01 9.31 ± 0.08 9.05 ± 0.12 9.27 ± 0.10 9.37 ± 0.07 9.53 ± 0.05 9.74 ± 0.11 9.35 ± 0.05 8.68 ± 0.26
 kdd_ipums_la           12.32 ± 0.74 12.91 ± 0.11 11.95 ± 0.16 11.63 ± 0.46 12.28 ± 0.10 12.06 ± 0.24 11.18 ± 0.42 11.31 ± 0.13 11.59 ± 0.29 11.10 ± 0.08 11.54 ± 0.10 10.93 ± 0.17
 MiniBooNE               5.89 ± 0.07 6.31 ± 0.02 6.02 ± 0.04 5.97 ± 0.07 5.89 ± 0.08 5.62 ± 0.05 5.65 ± 0.02 5.92 ± 0.02 5.82 ± 0.02 5.79 ± 0.03 5.80 ± 0.03 5.51 ± 0.04
 pol                     1.95 ± 0.09 1.79 ± 0.05 1.69 ± 0.04 1.82 ± 0.03 1.83 ± 0.07 1.75 ± 0.19 1.74 ± 0.07 1.85 ± 0.12 1.74 ± 0.04 1.87 ± 0.10 1.94 ± 0.07 1.42 ± 0.12
                                                                                Regression Tasks
 airfoil_self_noise     2.73 ± 0.10 2.36 ± 0.03 2.57 ± 0.06 2.44 ± 0.10 2.23 ± 0.05 2.01 ± 0.17         2.33 ± 0.18   2.38 ± 0.07   2.12 ± 0.10 2.20 ± 0.06 1.95 ± 0.22   1.50 ± 0.10
 cpu_small              11.14 ± 0.25 10.12 ± 0.07 11.16 ± 0.07 11.15 ± 0.08 11.02 ± 0.15 10.97 ± 0.07   8.26 ± 0.34   8.51 ± 0.34   7.85 ± 0.46 7.85 ± 0.58 7.97 ± 0.45   9.83 ± 0.67
 diamonds (×105 )       3.18 ± 0.01 2.84 ± 0.01 3.04 ± 0.04 2.98 ± 0.06 2.85 ± 0.02 2.72 ± 0.04         2.92 ± 0.01   2.68 ± 0.02   2.90 ± 0.02 2.87 ± 0.05 2.84 ± 0.01   2.60 ± 0.05
 plasma_retinol (×104 ) 5.77 ± 0.58 5.43 ± 0.09 4.54 ± 0.40 4.35 ± 0.21 4.64 ± 0.04 5.83 ± 0.37         5.99 ± 0.33   4.65 ± 0.30   4.82 ± 0.04 4.76 ± 0.29 4.07 ± 0.27   4.37 ± 0.30
 forest-fires (×103 )   4.43 ± 0.62 4.70 ± 0.31 4.27 ± 0.11 4.29 ± 0.19 3.96 ± 0.08 4.26 ± 0.02         3.98 ± 0.04   4.00 ± 0.00   4.16 ± 0.15 4.28 ± 0.39 3.97 ± 0.02   3.98 ± 0.02
 housing (×109 )        2.23 ± 0.00 2.16 ± 0.00 2.16 ± 0.03 2.09 ± 0.00 2.00 ± 0.07 1.97 ± 0.06         2.06 ± 0.01   1.99 ± 0.02   2.10 ± 0.04 2.04 ± 0.03 1.99 ± 0.06   1.89 ± 0.01
 bike (×10 )3           1.65 ± 0.01 1.67 ± 0.00 1.67 ± 0.02 1.66 ± 0.01 1.62 ± 0.02 1.52 ± 0.02         1.56 ± 0.01   1.60 ± 0.01   1.56 ± 0.02 1.53 ± 0.01 1.54 ± 0.01   1.43 ± 0.03
 crab                   5.12 ± 0.10 5.22 ± 0.05 5.11 ± 0.15 5.24 ± 0.18 5.16 ± 0.04 5.07 ± 0.07         4.47 ± 0.04   4.45 ± 0.02   4.88 ± 0.02 4.88 ± 0.09 4.58 ± 0.04   4.47 ± 0.02
 insurance (×107 )      2.82 ± 0.00 2.66 ± 0.00 2.72 ± 0.18 2.52 ± 0.10 2.51 ± 0.05 2.45 ± 0.07         1.98 ± 0.05   1.97 ± 0.04   2.01 ± 0.08 1.89 ± 0.02 1.90 ± 0.01   1.90 ± 0.07
 Avg. Rank (↓)              4.50         4.07         3.93         3.50         3.11         1.82         3.46          3.86          4.57        3.93         3.39         1.75
 Avg. Imp. (↑)                /            /            /            /            /            /         +6.27%        +4.98%        +4.96%      +3.84%       +5.13%       +7.03%

to ensure a fair comparison, they employ the same SMAC-based                                 4.2        Main Results
HPO module. In contrast, CoFEH performs interleaved optimiza-                                We first evaluate CoFEH and five baselines using XGBoost as the
tion across the entire 200-round budget. Each dataset is split into                          downstream ML model. The results across both standalone FE and
training (60%), validation (20%), and test (20%) sets. Optimization                          joint FE+HPO scenarios are presented in Table 1. Several key obseris driven by validation performance, after which the optimal FE                              vations emerge from the data: (i) LLM-based FE vs. traditional
pipeline and ML configuration are refitted on the combined train-                            FEs: In the standalone FE scenario, traditional methods generally
ing and validation set and evaluated on the test set. We report the                          underperform compared to LLM-based approaches, with Mindware
results across three independent runs to ensure statistical robustness.                      (4.52) and OpenFE (4.07) occupying the bottom average ranks. As
Implementation Details. All the baselines are implemented fol-                               discussed in Section 2.2, traditional frameworks are restricted to
lowing their official open-source versions or original methodologies.
                                                               √                             predefined, hardcoded operation sets and fixed, linear workflows.
Within CoFEH, we set the exploration constants 𝐶 1 = 𝐶 2 = 2 and                             (ii) Performance of LLM baselines: Among LLM-based baseconfigure the dynamic optimizer selector with initial prior weights                          lines, OCTree exhibits the weakest performance (Rank 3.93). This is
𝑝 1 = 0.9 and 𝑝 2 = 0.1, satisfying the conditions required for The-                         primarily due to its linear reasoning structure, which lacks a backorem 3.1. Our HPO component is also modified from SMAC to                                    tracking mechanism. ELLM (3.54) and LFG (3.11) achieve better
be conditioned on FE for collaborative optimization. Specifically,                           results by employing genetic optimization and Tree-of-Thought
we characterize each FE pipeline using MindWare’s meta-feature                               strategies, respectively. (iii) Dominance of CoFEH in standalone
set, which consolidates and extends meta-features from prior meta-                           FE: CoFEH achieves a state-of-the-art Average Rank of 1.84, signifilearning studies [3, 46, 58], with details provided in Appendix C.3.                         cantly outperforming the runner-up, LFG (3.11). A key strength lies
All LLM-based methods use gemini-2.0-flash [16] as the default                               in its ability to balance exploration and exploitation with a memory
backbone. Appendix D.1 shows that stronger backbones improve                                 mechanism, as further validated by the ablation in Appendix D.2.
performance, but Gemini-2.0-flash offers the best cost-efficiency                            Moreover, unlike other LLM baselines that focus strictly on indiwith only a remarkably small performance gap.                                                vidual feature generation, CoFEH enables truly free pipelines. The

  KDD ’26, August 09–13, 2026, Jeju Island, Republic of Korea                                                               Beicheng Xu, Keyao Ding, Wei Liu, Yupeng Lu, and Bin Cui

                                                                                          Mindware         LFG         OCTree          OpenFE           ELLM          CoFEH
                                                                                     5
                                        OCTree

                                                                   Time Ratio vs CoFEH
                                                                                     4
                          ELLFM-FT

Average Test Rank
                    4                                                                3
                                                             LFG
                                                                                     2
                    3
                                                                                     1
                    2                                CoFEH                           0         M
                                                                                         Min agic
                                                                                             iBoo
                                                                                               airfNE
                            0.02    0.04    0.06     0.08
                                                                                                    o
                                                                                                 banil
                                                                                           cali ke
                                                                                               f  bi
                                                                                           comornia
                                                                                                 p
                                                                                            cov ass
                                                                                                 type  k
                                                                                               cr cpu
                                                                                                 crab
                                                                                          diam edit
                                                                                         elec onds
                          Average Cost ($) Per Task
                                                                                               tric
                                                                                                  eiytey
                                                                                               fore  s
                                                                                                higg t
                                                                                               hou s
                                                                                            hou se
                                                                                          insu sing
                                                                                         kdd isra
                                                                                               jannnce
                                                                                              Cup
                                                                                           pho dd
                                                                                               nemk09
                                                                                             plas ema
                                                                                                   pol
                                                                                                 road
                                                                                                 win  rl
                                                                                                      e
                        (a) Expense-performance trade-off.                                 (b) Per-dataset runtime comparison (time ratio vs CoFEH).

                                                             Figure 4: Cost analysis of the main experiment.

 airfoil case study in Appendix D.3 further illustrates the qualitative                              Table 2: Generalization performance (Mean ± Std) across
 difference. CoFEH synthesizes physically meaningful features such                                   CASH and MLP scenarios. Imp. (%) denotes the relative error
 as a Strouhal-like number 𝑆𝑡 = 𝑓 · 𝑐/𝑈 , geometry terms sin 𝑎, and                                  reduction of CoFEH over the best baseline.
 their interaction, then composes them with Yeo-Johnson transfor-
 mation, standardization, and SelectKBest. This yields a heteroge-                                     Dataset                  LFG       Mindware         CoFEH        Imp. (↑)
 neous pipeline spanning transformation, generation, preprocessing,
                                                                                                                                  CASH Scenario
 and selection, whereas LLM baselines mainly remain in feature                                         pol                1.70 ± 0.15  1.87 ± 0.15       1.38 ± 0.10     +18.9%
 generation and OpenFE expands the feature set through many                                            wine               22.13 ± 0.27 20.89 ± 0.43      18.86 ± 0.39    +9.7%
 arithmetic candidates. (iv) Sequential vs. joint optimization: In                                     airfoil_self_noise 2.72 ± 0.14  2.88 ± 0.09       1.49 ± 0.11     +45.1%
 the joint FE+HPO scenario, the relative performance of baselines                                      housing (×109 )    2.12 ± 0.08  2.12 ± 0.02       1.78 ± 0.03     +16.0%
 shifts significantly. Decoupled sequential baselines (OpenFE, OC-                                                                   MLP Scenario
 Tree, ELLM, and LFG) exhibit lower improvement compared to                                            pol                  1.53 ± 0.08   1.42 ± 0.07    1.36 ± 0.12     +4.6%
 standalone FE—ranging from 3.84% to 5.13%—as they freeze the FE                                       wine                 22.94 ± 0.49 22.98 ± 0.36    20.63 ± 0.21    +10.1%
 pipeline before HPO, often trapping the system in a sub-optimal                                       airfoil_self_noise   10.49 ± 3.28 10.59 ± 2.58    6.73 ± 1.34     +35.9%
 state. In contrast, Mindware achieves a notable 6.27% improvement                                     housing (×109 )      2.70 ± 0.03   2.77 ± 0.06    2.66 ± 0.05     +1.3%
 by performing joint tuning over a unified search space. This holistic                                 Avg. Rank (↓)            2.25          2.75             1.00           –
 approach allows Mindware to overcome its poor FE performance
 (Rank 4.52), climbing to third place (Rank 3.46) in the end-to-end
 scenario. (v) Superiority of CoFEH in joint tuning: Through
 combining FE and HPO, CoFEH achieves a dominant improvement
                                                                                                     partly due to feature explosion in traditional automated search: for
 rate of 7.03% over standalone FE, further widening its lead with
                                                                                                     example, OpenFE expands the “airfoil” dataset to 127 dimensions,
 an Average Rank of 1.75. This success stems from our interleaved
                                                                                                     whereas CoFEH and other reasoning-based methods typically keep
 tuning strategy and a conditioned mechanism that facilitates mu-
                                                                                                     the representation below 20 dimensions (Appendix D.3). This high-
 tual feedback between FE and HPO. In summary, CoFEH not only
                                                                                                     lights the practical value of LLM-based FE: semantic reasoning can
 establishes a new benchmark for FE but also achieves a powerful
                                                                                                     avoid the combinatorial accumulation of redundant features and
 “strong-strong” synergy in joint tuning. Appendix D.4 highlights
                                                                                                     thereby reduce the cost of repeated downstream evaluations. Av-
 CoFEH’s robust scalability as dataset scale increases. We further
                                                                                                     eraged over the 28 datasets, evaluation dominates the end-to-end
 provide a Friedman test with Nemenyi post-hoc analysis in Appen-
                                                                                                     runtime, taking 1842.23s (72%), followed by LLM queries at 537.3s
 dix D.5 to validate the consistent superiority of CoFEH.
                                                                                                     (21%), BO at 127s (5%), and MCTS bookkeeping at only 51.2s (2%).
 Cost and runtime. Fig. 4a compares the API expense and average
 test rank of LLM-based methods. CoFEH achieves the best cost–
 performance trade-off, attaining the best average rank with only                                    4.3    Generalization to Downstream Models
 $0.07 per task; in contrast, LFG obtains the second-best rank but                                   While the preceding experiments demonstrate the efficacy of CoFEH
 incurs the highest API cost, while OCTree and ELLM are cheaper                                      using XGBoost, a robust FE framework should ideally exhibit model-
 but less effective. Fig. 4b further compares Per-datase end-to-end                                  agnostic generalization. In this section, we evaluate whether CoFEH
 runtime normalized by CoFEH. LLM-based baselines have com-                                          can collaboratively optimize FE and HPO within two distinct down-
 parable runtimes, whereas traditional search-heavy methods are                                      stream model types: deep learning (MLP) and multi-algorithm
 much slower: OpenFE and Mindware require on average 3.35× and                                       search (CASH). To mitigate API overhead, we select four repre-
 1.85× the runtime of CoFEH, respectively. This efficiency gap is                                    sentative datasets from the original benchmark: two classification

CoFEH : LLM-driven Feature Engineering Empowered by Collaborative Bayesian Hyperparameter Optimization                                                        KDD ’26, August 09–13, 2026, Jeju Island, Republic of Korea

tasks (pol, wine) and two regression tasks (airfoil, housing). We eval-                                                                   1.0

                                                                                                                 Normalized Performance
uate CoFEH against the two strongest baselines in joint FE+HPO
                                                                                                                                          0.8
scenario from Table 1: LLM-based LFG and traditional Mindware.
   The results in Table 2 demonstrate the robust generalization                                                                           0.6
of CoFEH across three dimensions: (1) Consistent superiority:                                                                                                                                                CoFEH
CoFEH consistently achieves the top performance across all datasets                                                                       0.4                                                                CoFEH (w/o Selector)
and scenarios with a perfect Average Rank of 1.00, maintaining a                                                                                                                                             CoFEH (w/o Cond)
ranking hierarchy identical to the primary XGBoost experiments. (2)                                                                       0.2                                                                CoFEH (Greedy)
Significant error reduction: Compared to the strongest baseline                                                                                 0        25         50      75       100                     125         150     175    200
in each task, CoFEH achieves substantial relative error reductions,                                                                                                      Number of Evaluations
most notably reaching 45.1% in the CASH scenario and 35.9% in
the MLP scenario. (3) Enhanced performance ceiling: Compared                                                     Figure 5: Ablation study of collaborative tuning.
with Table 1, the end-to-end performance in the CASH scenario
                                                                                                          1.0                                                                                            8

                                                                                   Datasets with FE (%)
generally surpasses that of XGBoost, indicating that CoFEH effec-

                                                                                                                                                                                    Number of Datasets
                                                                                                                                                                                                                   Mean (0.532)
tively leverages multi-algorithm search to identify models that are                                                                                                                                      6
more compatible with the synthesized features.                                                            0.5                                                                                            4
                                                                                                                                          95% CI
                                                                                                                                                                                                         2
                                                                                                                                          Linear Fit
4.4    Effectiveness of Collaborative Tuning                                                              0.0
                                                                                                             0                      40              80        120    160      200                        0         0.4         0.5     0.6    0.7
                                                                                                                                          Number of Evaluations                                                      Iterations with FE (%)
In this section, we evaluate the impact of the collaborative tuning
framework, which comprises two components: the mutual condi-                                         (a) FE prop. across iterations.                                                                     (b) FE prop. across datasets.
tioning mechanism for bidirectional information exchange between
FE and HPO, and the dynamic optimizer selector for resource sched-                    Figure 6: FE prop. driven by the dynamic optimizer selector
uling. To isolate their contributions, we conduct an ablation study
comparing the full CoFEH against three variants: (i) w/o Cond,
where HPO feedback and meta-feature conditioning are removed,                        (ii) Task-adaptive allocation: Fig. 6b illustrates the distribution
forcing both modules to generate independent proposals that are                      of total FE iterations per dataset. While the mean proportion of
heuristically paired with the counterpart’s current global best for                  0.53 empirically substantiates the budget equilibrium predicted in
evaluation; (ii) w/o Selector, which replaces dynamic scheduling                     Theorem 3.1, the significant variance (ranging from 0.37 to 0.7) highwith a fixed, alternating execution strategy; and (iii) Greedy, the                  lights the framework’s task-adaptivity. This reflects the selector’s
sequential paradigm that follows the standard practice of perform-                   ability to detect whether a specific task is more sensitive to feature
ing 100 iterations of FE followed by 100 iterations of HPO. As                       representations or ML configurations. Appendix D.7 provides a
illustrated in Fig. 5, which plots the average normalized best-so-                   case study of task-adaptive allocation. We compares independent
far validation performance across four representative datasets, the                  pure-FE and pure-HPO runs on two representative datasets to test
Greedy approach yields the poorest results, confirming the sub-                      whether the selector’s allocation matches each task’s dominant
optimality of decoupled optimization. We find that ablating either                   optimization driver. On “diamonds”, pure FE achieves a higher ceilcore mechanism significantly degrades convergence. Ultimately,                       ing than pure HPO, and the selector correspondingly allocates 70%
CoFEH achieves the best Average Test Rank of 1.25, substantially                     of the budget to FE. On “jannis”, pure HPO is more effective, and
outperforming the w/o Selector (2.25), w/o Cond (2.75) variants,                     the selector reduces the FE share to 37%. These contrasting cases
confirming the necessity of coupled information-resource manage-                     show that the selector adapts the FE/HPO budget according to the
ment. We next analyze the two components separately.                                 task-specific marginal utility of each optimizer.
Effect of mutual conditioning. The w/o Cond variant removes
HPO feedback and FE-state conditioning, preventing BO from dis-
                                                                                      5                         Conclusion
tinguishing how the same hyperparameter configuration behaves                         In this paper, we presented CoFEH, a collaborative framework that
under different transformed datasets. The surrogate-fitting analysis                  bridges semantic FE discovery and numerical HPO through adaptive
in Appendix D.6 provides direct evidence: adding FE meta-features                     scheduling and mutual conditioning. Experiments across 28 public
raises the mean Spearman correlation between BO predictions and                       datasets show that CoFEH achieves superior performance over tratrue performance from 0.587 to 0.691 across the 28 datasets, with                     ditional AutoML and LLM-based baselines in both standalone FE
especially large gains on FE-sensitive tasks.                                         and joint FE+HPO settings. The framework is also extensible: (i) it is
Effect of dynamic optimizer selection. To further analyze the                         agnostic to specific HPO implementations and maintains full combehavior of the optimizer selector, Fig. 6 examines the distribu-                     patibility with state-of-the-art BO variants; (ii) it enables seamless
tion of FE iterations across the 28 datasets in the main experiment.                  extension to image and text data via simple prompt modifications,
(i) Temporal dynamics: As shown in Fig. 6a, the proportion of                         offering a scalable foundation for future work.
datasets executing FE follows a clear downward trend. This aligns
with our design intuition of prior weights: prioritizing structural                   Acknowledgments
data refinement in early stages. Notably, both optimizers remain                      This work is supported by National Natural Science Foundation of
active throughout the process, ensuring continuous co-evolution.                      China (U23B2048, U22B2037). Bin Cui is the corresponding author.

KDD ’26, August 09–13, 2026, Jeju Island, Republic of Korea                                                           Beicheng Xu, Keyao Ding, Wei Liu, Yupeng Lu, and Bin Cui

References                                                                                   [24] Frank Hutter, Holger H Hoos, and Kevin Leyton-Brown. 2011. Sequential model-
 [1] Nikhil Abhyankar, Parshin Shojaee, and Chandan K Reddy. 2025. LLM-FE: Auto-                  based optimization for general algorithm configuration. In International Confer-
     mated Feature Engineering for Tabular Data with LLMs as Evolutionary Opti-                   ence on Learning and Intelligent Optimization. Springer, 507–523.
     mizers. arXiv preprint arXiv:2503.14434 (2025).                                         [25] Daniel P Jeong, Zachary C Lipton, and Pradeep Ravikumar. 2024. Llm-select:
 [2] Peter Auer. 2002. Using confidence bounds for exploitation-exploration trade-offs.           Feature selection with large language models. arXiv preprint arXiv:2407.02694
     Journal of machine learning research 3, Nov (2002), 397–422.                                 (2024).
 [3] Rémi Bardenet, Mátyás Brendel, Balázs Kégl, and Michele Sebag. 2013. Collabo-           [26] Zhengyao Jiang, Dominik Schmidt, Dhruv Srikanth, Dixing Xu, Ian Kaplan,
     rative hyperparameter tuning. In International conference on machine learning.               Deniss Jacenko, and Yuxiang Wu. 2025. Aide: Ai-driven exploration in the space
     PMLR, 199–207.                                                                               of code. arXiv preprint arXiv:2502.13138 (2025).
 [4] James Bergstra, Rémi Bardenet, Yoshua Bengio, and Balázs Kégl. 2011. Algorithms         [27] Michael I Jordan and Tom M Mitchell. 2015. Machine learning: Trends, perspec-
     for hyper-parameter optimization. Advances in neural information processing                  tives, and prospects. Science 349, 6245 (2015), 255–260.
     systems 24 (2011).                                                                      [28] Lara Kallab, Elio Mansour, and Richard Chbeir. 2024. Towards ML Models’
 [5] Bernd Bischl, Giuseppe Casalicchio, Taniya Das, Matthias Feurer, Sebastian Fis-              Recommendations. Data Science and Engineering 9, 4 (2024), 409–430.
     cher, Pieter Gijsbers, Subhaditya Mukherjee, Andreas C Müller, László Németh,           [29] Gilad Katz, Eui Chul Richard Shin, and Dawn Song. 2016. Explorekit: Automatic
     Luis Oala, et al. 2025. OpenML: Insights from 10 years and more than a thousand              feature generation and selection. In 2016 IEEE 16th international conference on
     papers. Patterns 6, 7 (2025).                                                                data mining (ICDM). IEEE, 979–984.
 [6] Li-Guo Chen, Zheng Xiao, Yi-Jiang Xu, Rui-Chuan An, Xin Wang, Yang-Ning Li,             [30] Ambika Kaul, Saket Maheshwary, and Vikram Pudi. 2017. Autolearn—automated
     Ying-Hui Li, Yi-Dong Wang, Zheng-Ran Zeng, Qing Gao, et al. 2025. CodeRankE-                 feature generation and selection. In 2017 IEEE International Conference on data
     val: Benchmarking and Analyzing LLM Performance for Code Ranking. Journal                    mining (ICDM). IEEE, 217–226.
     of Computer Science and Technology 40, 5 (2025), 1220–1233.                             [31] Udayan Khurana, Horst Samulowitz, and Deepak Turaga. 2018. Feature engi-
 [7] Tianqi Chen. 2016. XGBoost: A Scalable Tree Boosting System. Cornell University              neering for predictive modeling using reinforcement learning. In Proceedings of
     (2016).                                                                                      the AAAI conference on artificial intelligence, Vol. 32.
 [8] Hyunjun Choi, Jay Moran, Nicholas Matsumoto, Miguel E Hernandez, and Ja-                [32] Jeonghyun Ko, Gyeongyun Park, Donghoon Lee, and Kyunam Lee. 2025. Ferg-llm:
     son H Moore. 2023. Aliro: an automated machine learning tool leveraging large                Feature engineering by reason generation large language models. In Findings of
     language models. Bioinformatics 39, 10 (2023), btad606.                                      the Association for Computational Linguistics: NAACL 2025. 4211–4228.
 [9] Kristy Choi, Chris Cundy, Sanjari Srivastava, and Stefano Ermon. 2022. Lm-              [33] Levente Kocsis and Csaba Szepesvári. 2006. Bandit based monte-carlo planning.
     priors: Pre-trained language models as task-specific priors. arXiv preprint                  In European conference on machine learning. Springer, 282–293.
     arXiv:2210.12530 (2022).                                                                [34] Erin LeDell and Sebastien Poirier. 2020. H2o automl: Scalable automatic machine
[10] Abdoulatif Cissé, Xenophon Evangelopoulos, Vladimir V. Gusev, and Andrew I.                  learning. In Proceedings of the AutoML Workshop at ICML, Vol. 2020. 24.
     Cooper. 2025. Language-based Bayesian optimization research assistant (BORA).           [35] Xiao-Lin Li, Li Ma, Xiang-Dong He, and Hui Xiong. 2020. You are how you
     In Proceedings of the Thirty-Fourth International Joint Conference on Artificial             behave–spatiotemporal representation learning for college student academic
     Intelligence (Montreal, Canada) (IJCAI ’25). Article 553, 9 pages. doi:10.24963/             achievement. Journal of Computer Science and Technology 35, 2 (2020), 353–367.
     ijcai.2025/553                                                                          [36] Yang Li, Jiawei Jiang, Jinyang Gao, Yingxia Shao, Ce Zhang, and Bin Cui. 2020.
[11] Yuyang Dong, Masafumi Oyamada, Chuan Xiao, and Haochen Zhang. 2024. On                       Efficient automatic CASH via rising bandits. In Proceedings of the AAAI Conference
     the use of large language models for table tasks. In Proceedings of the 33rd ACM             on Artificial Intelligence, Vol. 34. 4763–4771.
     International Conference on Information and Knowledge Management. 5518–5521.            [37] Ziming Li, Qianbo ZANG, David Ma, Jiawei Guo, Tuney Zheng, Minghao Liu,
[12] Iddo Drori, Yamuna Krishnamurthy, Remi Rampin, Raoni DE PAULA LOURENCO,                      Xinyao Niu, Yue Wang, Jian Yang, Jiaheng Liu, et al. 2024. AutoKaggle: A Multi-
     Jorge Piazentin Ono, Kyunghyun Cho, Claudio Silva, and Juliana Freire. 2021.                 Agent Framework for Autonomous Data Science Competitions. In ICLR 2025
     AlphaD3M: Machine Learning Pipeline Synthesis. In ICML AutoML Workshop.                      Worshop Emergent Possibilities and Challenges in Deep Learning for Code.
[13] Stefan Falkner, Aaron Klein, and Frank Hutter. 2018. BOHB: Robust and efficient         [38] Siyi Liu, Chen Gao, and Yong Li. 2024. Large language model agent for hyper-
     hyperparameter optimization at scale. In International conference on machine                 parameter optimization. arXiv preprint arXiv:2402.01881 (2024).
     learning. PMLR, 1437–1446.                                                              [39] Sijia Liu, Parikshit Ram, Deepak Vijaykeerthy, Djallel Bouneffouf, Gregory Bram-
[14] Matthias Feurer, Katharina Eggensperger, Stefan Falkner, Marius Lindauer, and                ble, Horst Samulowitz, Dakuo Wang, Andrew Conn, and Alexander Gray. 2020.
     Frank Hutter. 2022. Auto-sklearn 2.0: Hands-free automl via meta-learning.                   An ADMM based framework for automl pipeline configuration. In Proceedings of
     Journal of Machine Learning Research 23, 261 (2022), 1–61.                                   the AAAI Conference on Artificial Intelligence, Vol. 34. 4892–4899.
[15] Nanxu Gong, Chandan K Reddy, Wangyang Ying, Haifeng Chen, and Yanjie Fu.                [40] Tennison Liu, Nicolás Astorga, Nabeel Seedat, and Mihaela van der Schaar. 2025.
     2025. Evolutionary large language model for automated feature transformation.                Large Language Models to Enhance Bayesian Optimization. In The Twelfth Inter-
     In Proceedings of the AAAI conference on artificial intelligence, Vol. 39. 16844–            national Conference on Learning Representations.
     16852.                                                                                  [41] Zexi Liu, Yuzhu Cai, Xinyu Zhu, Yujie Zheng, Runkun Chen, Ying Wen, Yanfeng
[16] Google DeepMind. 2025. Gemini 2.0 Flash. https://docs.cloud.google.com/vertex-               Wang, Siheng Chen, et al. 2025. ML-Master: Towards AI-for-AI via Integration
     ai/generative-ai/docs/models/gemini/2-0-flash                                                of Exploration and Reasoning. arXiv preprint arXiv:2506.16499 (2025).
[17] Yury Gorishniy, Ivan Rubachev, Valentin Khrulkov, and Artem Babenko. 2021.              [42] Daqin Luo, Chengjian Feng, Yuxuan Nong, and Yiqing Shen. 2024. Autom3l:
     Revisiting deep learning models for tabular data. Advances in neural information             An automated multimodal machine learning framework with large language
     processing systems 34 (2021), 18932–18943.                                                   models. In Proceedings of the 32nd ACM International Conference on Multimedia.
[18] Léo Grinsztajn, Edouard Oyallon, and Gaël Varoquaux. 2022. Why do tree-based                 8586–8594.
     models still outperform deep learning on typical tabular data? Advances in neural       [43] Alhassan Mumuni and Fuseini Mumuni. 2025. Automated data processing and
     information processing systems 35 (2022), 507–520.                                           feature engineering for deep learning and big data applications: a survey. Journal
[19] José Miguel Hernández-Lobato, Matthew W Hoffman, and Zoubin Ghahramani.                      of Information and Intelligence 3, 2 (2025), 113–153.
     2014. Predictive entropy search for efficient global optimization of black-box          [44] Jaehyun Nam, Kyuyoung Kim, Seunghyuk Oh, Jihoon Tack, Jaehyung Kim, and
     functions. In Advances in neural information processing systems. 918–926.                    Jinwoo Shin. 2024. Optimized feature generation for tabular data via llms with
[20] Noah Hollmann, Samuel Müller, and Frank Hutter. 2023. Large language models                  decision tree reasoning. Advances in Neural Information Processing Systems 37
     for automated data science: Introducing caafe for context-aware automated                    (2024), 92352–92380.
     feature engineering. Advances in Neural Information Processing Systems 36 (2023),       [45] Randal S Olson and Jason H Moore. 2016. TPOT: A tree-based pipeline optimiza-
     44753–44775.                                                                                 tion tool for automating machine learning. In Workshop on automatic machine
[21] Franziska Horn, Robert Pack, and Michael Rieger. 2019. The autofeat python                   learning. PMLR, 66–74.
     library for automated feature engineering and selection. In Joint European Con-         [46] Bernhard Pfahringer, Hilan Bensusan, and Christophe G Giraud-Carrier. 2000.
     ference on Machine Learning and Knowledge Discovery in Databases. Springer,                  Meta-Learning by Landmarking Various Learning Algorithms.. In ICML. 743–750.
     111–120.                                                                                [47] Pranav Poduval, Sanjay Kumar Patnala, Gaurav Oberoi, Nitish Srivasatava, and
[22] Yi-Qi Hu, Yang Yu, Wei-Wei Tu, Qiang Yang, Yuqiang Chen, and Wenyuan                         Siddhartha Asthana. 2024. Cash via optimal diversity for ensemble learning. In
     Dai. 2019. Multi-fidelity automatic hyper-parameter tuning via transfer series               Proceedings of the 30th ACM SIGKDD Conference on Knowledge Discovery and
     expansion. In Proceedings of the AAAI Conference on Artificial Intelligence, Vol. 33.        Data Mining. 2411–2419.
     3846–3853.                                                                              [48] Christopher D Rosin. 2011. Multi-armed bandits with episode context. Annals of
[23] Beichen Huang, Xingyu Wu, Yu Zhou, Jibin Wu, Liang Feng, Ran Cheng, and                      Mathematics and Artificial Intelligence 61, 3 (2011), 203–230.
     Kay Chen Tan. 2024. Exploring the true potential: Evaluating the black-box              [49] Yu Shen, Yupeng Lu, Yang Li, Yaofeng Tu, Wentao Zhang, and Bin Cui. 2022. Di-
     optimization capability of large language models. arXiv preprint arXiv:2404.06290            vbo: diversity-aware cash for ensemble learning. Advances in Neural Information
     (2024).                                                                                      Processing Systems 35 (2022), 2958–2971.
                                                                                             [50] Jasper Snoek, Hugo Larochelle, and Ryan P Adams. 2012. Practical bayesian
                                                                                                  optimization of machine learning algorithms. Advances in neural information

CoFEH : LLM-driven Feature Engineering Empowered by Collaborative Bayesian Hyperparameter Optimization           KDD ’26, August 09–13, 2026, Jeju Island, Republic of Korea

     processing systems 25 (2012).                                                                Template: Task Description
[51] Chris Thornton, Frank Hutter, Holger H Hoos, and Kevin Leyton-Brown. 2013.
     Auto-WEKA: Combined selection and hyperparameter optimization of classifica-
     tion algorithms. In Proceedings of the 19th ACM SIGKDD international conference             Given the dataset metadata, transformation history, and
     on Knowledge discovery and data mining. 847–855.
[52] Anton Vakhrushev, Alexander Ryzhkov, Maxim Savchenko, Dmitry Simakov,
                                                                                                 elite memory, your task is to synthesize novel feature en-
     Rinchin Damdinov, and Alexander Tuzhilin. 2021. Lightautoml: Automl solution                gineering operations to maximize the performance of the
     for a large financial services ecosystem. arXiv preprint arXiv:2109.01528 (2021).           downstream model {. . . }. You should provide a reasoning
[53] Chenglong Wang, Bongshin Lee, Steven M Drucker, Dan Marshall, and Jianfeng
     Gao. 2025. Data Formulator 2: Iterative Creation of Data Visualizations, with AI            insight followed by executable Python code.
     Transforming Data Along the Way. In Proceedings of the 2025 CHI Conference on               Output Format: You must provide your insight followed
     Human Factors in Computing Systems. 1–17.                                                   by the code in the following structure:
[54] Beicheng Xu, Wei Liu, Keyao Ding, Yupeng Lu, and Bin Cui. 2026. Pseo: Optimiz-
     ing post-hoc stacking ensemble through hyperparameter tuning. In Proceedings                — Reason: (Explain why this specific transformation is
     of the AAAI Conference on Artificial Intelligence, Vol. 40. 27188–27196.                    beneficial for the downstream model.)
[55] Jinglue Xu, Jialong Li, Zhen Liu, Nagar Anthel Venkatesh Suryanarayanan,
     Guoyuan Zhou, Jia Guo, Hitoshi Iba, and Kenji Tei. 2024. Large language models
                                                                                                 — Way: (Detail the exact columns and the specific method
     synergize with automated machine learning. arXiv preprint arXiv:2405.03727                  required for the transformation.)
     (2024).                                                                                     — Implementation: (Provide a standalone Python function.
[56] Quanming Yao, Mengshuo Wang, Yuqiang Chen, Wenyuan Dai, Yu-Feng Li,
     Wei-Wei Tu, Qiang Yang, and Yang Yu. 2018. Taking human out of learning appli-              Use the following template)
     cations: A survey on automated machine learning. arXiv preprint arXiv:1810.13306            def your_fe_name(df):
                                                                                                      '''A concise description of the operator.'''
     31 (2018).
[57] Shunyu Yao, Dian Yu, Jeffrey Zhao, Izhak Shafran, Tom Griffiths, Yuan Cao, and
     Karthik Narasimhan. 2023. Tree of thoughts: Deliberate problem solving with                       import pandas as pd
     large language models. Advances in neural information processing systems 36                       import numpy as np
     (2023), 11809–11822.
[58] Dani Yogatama and Gideon Mann. 2014. Efficient transfer learning method for                       # Your implementation here
     automatic hyperparameter tuning. In Artificial intelligence and statistics. PMLR,                 return df
     1077–1085.
[59] Shujian Zhang, Chengyue Gong, Lemeng Wu, Xingchao Liu, and Mingyuan
     Zhou. 2023. Automl-gpt: Automatic machine learning with gpt. arXiv preprint
     arXiv:2305.02499 (2023).
[60] Tianping Zhang, Zheyu Aqa Zhang, Zhiyuan Fan, Haoyan Luo, Fengyuan Liu,
     Qian Liu, Wei Cao, and Li Jian. 2023. Openfe: Automated feature generation
     with expert-level performance. In International Conference on Machine Learning.
                                                                                             (ii) Dataset information (𝜓 ). The Dataset Information component
     PMLR, 41880–41901.                                                                      provides a comprehensive data profile, grounding the LLM’s rea-
[61] Xinhao Zhang, Jinghan Zhang, Banafsheh Rekabdar, Yuanchun Zhou, Pengfei                 soning in both statistical metadata and semantic context. To ground
     Wang, and Kunpeng Liu. 2025. Dynamic and adaptive feature generation with
     LLM. In Proceedings of the Thirty-Fourth International Joint Conference on Artificial   the LLM’s reasoning in the dataset’s specific characteristics, we
     Intelligence (Montreal, Canada) (IJCAI ’25). Article 782, 9 pages. doi:10.24963/        transform the raw data into a structured dataset information block
     ijcai.2025/782                                                                          using a multi-level extraction logic:
[62] Tommaso Zoppi, Andrea Ceccarelli, and Andrea Bondavalli. 2025. A strategy
     for predicting the performance of supervised and unsupervised tabular data
     classifiers. Data Science and Engineering 10, 1 (2025), 75–97.
                                                                                               • Global profile: Captures the dataset scale (samples/features),
                                                                                                 task type (e.g., regression/classification), the numerical-to-
                                                                                                 categorical ratio, and the semantic meaning of the dataset
A      Prompt Design                                                                             (if such a description is available), e.g., what the records repre-
                                                                                                 sent and what the target variable measures.
This appendix details our prompt engineering framework, spanning
the expert-persona System Prompt, the structured User Prompt,                                  • Missingness & quality analysis: We calculate the missing
and a concrete input/output example.                                                             ratio for every column and flag "high-missing" features that
                                                                                                 exceed a predefined threshold (e.g., 30%).
A.0.1 System Prompt. The System Prompt establishes the LLM’s
                                                                                               • Heuristic key feature selection: To manage the LLM’s conexpert role and core mission:
                                                                                                 text window, we score features based on their missingness,
                                                                                                 presence of domain descriptions, and types. We then prioritize
       System Prompt
                                                                                                 the Top-𝐾 most informative features for detailed display.
   You are an elite Machine Learning Feature Engineering (FE)                                  • Feature-Level metadata: Distills specific statistics based on
   Expert whose mission is to analyze data distributions and syn-                                the feature type:
   thesize high-value pipelines by generating innovative feature
                                                                                                    – Categorical: Includes the number of unique classes and
   operations specifically designed to maximize the predictive
                                                                                                      the frequency of the Top-3 categories.
   performance of downstream ML models.
                                                                                                    – Numerical/discrete: Includes statistical moments (mean,
                                                                                                      standard deviation) and the value range (min, max).
A.0.2 Structured User Prompt. The User Prompt comprises the
                                                                                                 We append human-readable notes (e.g., feature meanings) to
global task description and the four core components defined in
                                                                                                 the metadata whenever domain knowledge is available.
Eq. (6), which are detailed below:
(i) Task description defines the objective and output format:

KDD ’26, August 09–13, 2026, Jeju Island, Republic of Korea                             Beicheng Xu, Keyao Ding, Wei Liu, Yupeng Lu, and Bin Cui

       Template: Dataset Information                                (v) Directive and Optimization Objectives
                                                                       Depending on the directive 𝑑, the LLM is given optimization
  [Dataset Profile] {dataset scale, . . . }                         objectives that explicitly steer its feature engineering strategy. The
  Target: {. . . }                                                  directive governs whether the LLM should focus on generating
  All columns: {[. . . ]}                                           an initial strong operation, exploring novel transformations, or
  [Feature Summary] {highlights data quality, specifically          exploiting previously successful experience.
  identifying columns with high missing-value ratios.}
  [Key Features] detailed metadata for each feature:
  – col1: type=numerical, missing={%}, mean={𝜇}, std={𝜎 },
  range=({𝑚𝑖𝑛, 𝑚𝑎𝑥 }), note={𝑑𝑒𝑠𝑐𝑟𝑖𝑝𝑡𝑖𝑜𝑛}
  – col2: type=categorical, missing={%}, classes={[. . . ]},             Template: Directive and Objectives
  top={[(𝑣𝑎𝑙, 𝑓 𝑟𝑒𝑞), . . . ]}, note={𝑑𝑒𝑠𝑐𝑟𝑖𝑝𝑡𝑖𝑜𝑛}
  – . . . (repeated for prioritized features)                         Your general objectives (for any directive 𝑑):
                                                                      1. Review and summarize the existing feature engineering op-
                                                                      erations from the original dataset to the currently selected
(iii) Ancestor FE pipeline (𝑇anc ) summarizes the FE pipelines        node in the ancestor FE pipeline.
from the original dataset to the currently selected node:             2. Avoid duplicating the feature engineering approaches that
                                                                      have already been attempted in the ancestor FE pipeline.
       Template: Ancestor FE Pipeline                                 3. Propose one new feature engineering step. You may refer-
                                                                      ence (but are not limited to) the following categories:
  Current Feature Engineering pipelines:
                                                                          – Generator: creating new features.
  →
                                                                          – Selector: choosing or filtering the most relevant features.
  [Do nothing]
                                                                          – Transformation: modifying distributions or encoding cat-
      ⌞ Score: {𝑆𝑐𝑜𝑟𝑒 0 }
                                                                      egories.
  →
                                                                          – Rescaler: normalizing or standardizing features.
  [FE Operation 1]
                                                                          – Imputer: filling missing data via statistical or model-based
      ⊢ Reason: {𝑅𝑒𝑎𝑠𝑜𝑛 1 }
                                                                      methods.
      ⊢ Way: {𝑀𝑒𝑡ℎ𝑜𝑑 1 }
      ⌞ Score: {𝑆𝑐𝑜𝑟𝑒 1 }                                             4. Your strategy is {d} (further specifies the optimization mode
  →                                                                   and how the above objectives should be prioritized):
  ...
  →
  [FE Operation 𝑘]                                                      Directive: Initialization (for root node 𝑠 0 )
      ⊢ Reason: {𝑅𝑒𝑎𝑠𝑜𝑛𝑘 }                                              Instruction: Propose a high-quality initial FE operation
      ⊢ Way: {𝑀𝑒𝑡ℎ𝑜𝑑𝑘 }                                                 for the original dataset. Focus on robust, broadly useful
      ⌞ Score: {𝑆𝑐𝑜𝑟𝑒𝑘 }                                                transformations rather than highly specialized or overly
                                                                        complex operations. Aim to establish a strong baseline
                                                                        that later operations can further improve upon.
(iv) Memory of good FE operations (M𝑠base ) shows good feature
engineering operations from history that you can reference in the
next step:
                                                                        Directive: Exploration
       Template: Memory of Good FE Pipelines                            Instruction: Propose FE operations that explores new
                                                                        regions of the transformation space. Prioritize novel or
  High-performing historical FE operations (memory):                    less-explored ideas that are distinct from existing and
  [Good Operation 1]                                                    previously attempted methods. Encourage diversity
      ⊢ Reason: {𝑅𝑒𝑎𝑠𝑜𝑛 1 }                                             and bold changes to uncover potentially high-performing
      ⊢ Way: {𝑀𝑒𝑡ℎ𝑜𝑑 1 }                                                operations.
      ⌞ Score: {𝑆𝑐𝑜𝑟𝑒 1 }, relative improve: {𝑅𝑒𝑙𝐼𝑚𝑝𝑟𝑜𝑣𝑒 1 }
  [Good Operation 2]
      ⊢ Reason: {𝑅𝑒𝑎𝑠𝑜𝑛 2 }
      ⊢ Way: {𝑀𝑒𝑡ℎ𝑜𝑑 2 }                                                Directive: Exploitation
      ⌞ Score: {𝑆𝑐𝑜𝑟𝑒 2 }, relative improve: {𝑅𝑒𝑙𝐼𝑚𝑝𝑟𝑜𝑣𝑒 2 }            Instruction: Propose FE operations that refines and
  ...                                                                   exploits previously successful operations. Try to reuse,
  [Good Operation 𝑚]                                                    adapt, or combine high-performing historical FE opera-
      ⊢ Reason: {𝑅𝑒𝑎𝑠𝑜𝑛𝑚 }                                              tions from the memory to further improve performance.
      ⊢ Way: {𝑀𝑒𝑡ℎ𝑜𝑑𝑚 }                                                 Focus on incremental refinement rather than broad
      ⌞ Score: {𝑆𝑐𝑜𝑟𝑒𝑚 }, relative improve: {𝑅𝑒𝑙𝐼𝑚𝑝𝑟𝑜𝑣𝑒𝑚 }              exploration.

CoFEH : LLM-driven Feature Engineering Empowered by Collaborative Bayesian Hyperparameter Optimization    KDD ’26, August 09–13, 2026, Jeju Island, Republic of Korea

B    Proof of Theorem                                                                  At 𝑚 = 0, the cumulative counts are 𝑁 FE (0) = 𝑁 HPO (0) = 0. The
In this appendix, we provide a detailed proof of Theorem 3.1.                       state function evaluates to:
                                                                                           𝑄 (0) = 𝜔 FE (0)(1) − 𝜔 HPO (0)(1) = 𝑝 1 − 𝑝 2 = 2𝑝 1 − 1.
   Proof. To establish the budget equilibrium property, we first
formalize the conditions provided in the theorem. We assume a                      Given the condition 0.5 ≤ 𝑝 1 < 𝑀+1.5                       𝑀+1.5
                                                                                                                       𝑀+3 , and knowing that 𝑀+3 < 1
neutral reward signal 𝑄 (FE) = 𝑄 (HPO) = const and an exploration                  for any 𝑀 > 0, it follows that 0 ≤ 𝑄 (0) < 1. Thus, |𝑄 (0)| < 1 holds.
constant 𝐶 2 > 0. The total iteration budget 𝑀 is an integer, and the              Inductive step:
prior weights are governed by the following linear schedules:                         Assume |𝑄 (𝑚)| < 1 for some iteration 𝑚. We examine the state
                                                                                   transition to 𝑄 (𝑚 + 1) based on the decision rule:
                                                        𝑝 1 − 0.5
    𝜔 FE (𝑚) = 𝑝 1 − 𝛿𝑚, 𝜔 HPO (𝑚) = 𝑝 2 + 𝛿𝑚, 𝛿 =                ,                   Case 1: 𝑄 (𝑚) > 0. According to the decision rule, 𝐼 (𝑚 + 1) = 1
                                                            𝑀
                                                                                   (FE is selected). Substituting it into the recurrence relation:
where 𝑝 1 is subject to the boundedness constraint 0.5 ≤ 𝑝 1 < 𝑀+1.5
                                                               𝑀+3 .                             𝑄 (𝑚 + 1) = 𝑄 (𝑚) + 𝜔 FE (𝑚) − 1 − 𝛿 (𝑚 + 3)
1. Simplification of the Decision Rule
   Under the neutral reward assumption, the PUCT-based selection                   Since 𝜔 FE (𝑚) <= 1 and 𝛿 (𝑚 + 3) >= 0, it is clear that 𝑄 (𝑚 + 1) ≤
rule in Eq. (8) reduces to maximizing the prior-weighted exploration               𝑄 (𝑚) < 1. To prove the lower bound 𝑄 (𝑚+1) > −1, we substituting
term. Specifically, the framework selects the action 𝑎 that maximizes              𝜔 FE (𝑚) = 𝑝 1 − 𝛿𝑚:
        √Í
            𝑁𝑎 ′                 √︁Í
𝜔𝑎 (𝑚) 1+𝑁𝑎 . Since the term          𝑁𝑎′ is identical for both arms at                        𝜔 FE (𝑚) − 1 − 𝛿 (𝑚 + 3) = 𝑝 1 − 𝛿 (2𝑚 + 3) − 1
any given iteration 𝑚, the selection indicator 𝐼 (𝑚 + 1) for FE is                                       > 𝑝 1 − 𝛿 (2𝑀 + 3) − 1
defined as:                                                                                                      𝑝 1 − 0.5
                          (        𝜔 FE (𝑚)     𝜔 HPO (𝑚)                                                = 𝑝1 −            (2𝑀 + 3) − 1
                            1, if 1+𝑁        > 1+𝑁                                                                   𝑀
              𝐼 (𝑚 + 1) =             FE (𝑚)       HPO (𝑚) ,
                            0, otherwise                                                                   𝑀 + 1.5 − (𝑀 + 3)𝑝 1
                                                                                                         =                        − 1.
                                                                                                                      𝑀
where 𝑁 FE (𝑚) and 𝑁 HPO (𝑚) are the cumulative counts of FE and
                                                                                      Given the theorem’s constraint 𝑝 1 < 𝑀+1.5
                                                                                                                              𝑀+3 , we have 𝑀 + 1.5 −
HPO selections, respectively, such that 𝑁 FE (𝑚) + 𝑁 HPO (𝑚) = 𝑚.
                                                                                    (𝑀 + 3)𝑝 1 > 0. Thus
We define an auxiliary state function 𝑄 (𝑚) as:
                                                                                                       𝜔 FE (𝑚) − 1 − 𝛿 (𝑚 + 3) > −1.                          (9)
    𝑄 (𝑚) = 𝜔 FE (𝑚) 1 + 𝑁 HPO (𝑚) − 𝜔 HPO (𝑚) 1 + 𝑁 FE (𝑚) .
                                                                                   As a result, 𝑄 (𝑚 + 1) > 𝑄 (𝑚) − 1. Given the condition for Case
Thus the decision rule is equivalent to:                                           1 that 𝑄 (𝑚) > 0, we have 𝑄 (𝑚 + 1) > −1. Combined with the
                              (
                                1, if 𝑄 (𝑚) > 0                                    previously established upper bound 𝑄 (𝑚 + 1) < 1, we conclude
                 𝐼 (𝑚 + 1) =                    ,                                  |𝑄 (𝑚 + 1)| < 1.
                                0, otherwise
                                                                                      Case 2: 𝑄 (𝑚) ≤ 0. According to the decision rule, 𝐼 (𝑚 + 1) = 0
2. Derivation of the Recurrence Relation                                           (HPO is selected). Substituting 𝐼 (𝑚 + 1) = 0:
   First, we establish the recurrence relation for 𝑄 (𝑚). Given the                               𝑄 (𝑚 + 1) = 𝑄 (𝑚) + 𝜔 FE (𝑚) − 𝛿 (𝑚 + 3).
linear definitions 𝜔 FE (𝑚) = 𝑝 1 − 𝛿𝑚 and 𝜔 HPO (𝑚) = 𝑝 2 + 𝛿𝑚,
it follows that: 𝜔 FE (𝑚 + 1) = 𝜔 FE (𝑚) − 𝛿 and 𝜔 HPO (𝑚 + 1) =                   Since 𝜔 FE (𝑚) ≤ 𝑝 1 < 1 and 𝑄 (𝑚) ≤ 0, we have 𝑄 (𝑚 + 1) < 1. For
𝜔 HPO (𝑚) + 𝛿. The counts for the next iteration are updated based                 the lower bound, from Eq. (9) in Case 1, we have already established
on the indicator 𝐼 (𝑚 + 1) as 𝑁 FE (𝑚 + 1) = 𝑁 FE (𝑚) + 𝐼 (𝑚 + 1) and              that: 𝜔 FE (𝑚) − 1 −𝛿 (𝑚 + 3) > −1 =⇒ 𝜔 FE (𝑚) −𝛿 (𝑚 + 3) > 0. As a
𝑁 HPO (𝑚 + 1) = 𝑁 HPO (𝑚) + (1 − 𝐼 (𝑚 + 1)). Substituting these into               result, 𝑄 (𝑚 + 1) > 𝑄 (𝑚). By the inductive hypothesis 𝑄 (𝑚) > −1,
the definition of 𝑄 (𝑚 + 1), we have:                                              we conclude 𝑄 (𝑚 + 1) > −1. Thus, |𝑄 (𝑚 + 1)| < 1 holds for Case 2
                                                                                  as well.
     𝑄 (𝑚 + 1) = 𝜔 FE (𝑚 + 1) 1 + 𝑁 HPO (𝑚 + 1)                                       To sum up, |𝑄 (𝑚)| < 1 is satisfied for the entire search.
                                                                                   4. Terminal Equilibrium
                                                      
                     − 𝜔 HPO (𝑚 + 1) 1 + 𝑁 FE (𝑚 + 1)
                                                                                     At the terminal iteration 𝑚 = 𝑀, the linear priors reach:
               = (𝜔 FE (𝑚) − 𝛿) 1 + 𝑁 HPO (𝑚) + (1 − 𝐼 (𝑚 + 1))
                                                                                                    𝑝 1 − 0.5                            0.5 − 𝑝 2
                                                                                   𝜔 FE (𝑀) = 𝑝 1 −           𝑀 = 0.5, 𝜔 HPO (𝑀) = 𝑝 2 +
                                                                
                     − (𝜔 HPO (𝑚) + 𝛿) 1 + 𝑁 FE (𝑚) + 𝐼 (𝑚 + 1)                                                                                    𝑀 = 0.5
                                                                                                        𝑀                                   𝑀
  Expanding the terms and substituting 𝑁 FE (𝑚) + 𝑁 HPO (𝑚) = 𝑚                    Substituting these into the auxiliary function 𝑄 (𝑀):
and 𝜔 FE + 𝜔 HPO = 1:                                                                                                         
                                                                                   𝑄 (𝑀) = 0.5 1+𝑁 HPO (𝑀) −0.5 1+𝑁 FE (𝑀) = 0.5 𝑁 HPO (𝑀)−𝑁 FE (𝑀)
                                                                                                                                                           

    𝑄 (𝑚 + 1) = 𝜔 FE (𝑚)(1 + 𝑁 HPO ) − 𝜔 HPO (𝑚)(1 + 𝑁 FE )                            From the boundedness lemma, we have:
                     + 𝜔 FE (𝑚)(1 − 𝐼 (𝑚 + 1)) − 𝜔 HPO (𝑚)𝐼 (𝑚 + 1)                                            
                                                                                      |0.5 𝑁 HPO (𝑀) − 𝑁 FE (𝑀) | < 1 =⇒ |𝑁 HPO (𝑀) − 𝑁 FE (𝑀)| < 2
                     − 𝛿 (1 + 𝑁 HPO + 1 − 𝐼 + 1 + 𝑁 FE + 𝐼 )
                                                                                   Since 𝑁 FE (𝑀) and 𝑁 HPO (𝑀) are integers, their difference Δ𝑁 =
               = 𝑄 (𝑚) + 𝜔 FE (𝑚) − 𝐼 (𝑚 + 1) − 𝛿 (𝑚 + 3)                          𝑁 HPO (𝑀)−𝑁 FE (𝑀) must also be an integer such that Δ𝑁 ∈ {−1, 0, 1}.
3. Boundedness Lemma (|𝑄 (𝑚)| < 1)                                                 We now consider the parity of the total budget 𝑀:
  We prove that for all 𝑚 ∈ {0, 1, . . . , 𝑀 }, the state function remains             • Case A (Even 𝑀): If 𝑀 is even, then Δ𝑁 = 𝑀 − 2𝑁 FE (𝑀)
bounded within the open interval |𝑄 (𝑚)| < 1 via induction.                              must be an even integer. The only even integer in the interval
Base case:                                                                               (−2, 2) is 0. Thus, 𝑁 FE (𝑀) = 𝑁 HPO (𝑀) = 𝑀/2.

KDD ’26, August 09–13, 2026, Jeju Island, Republic of Korea                                       Beicheng Xu, Keyao Ding, Wei Liu, Yupeng Lu, and Bin Cui

   • Case B (Odd 𝑀): If 𝑀 is odd, then Δ𝑁 = 𝑀 − 2𝑁 FE (𝑀) must                      Table 4: Statistics for 9 regression datasets.
     be an odd integer. The only odd integers in the interval (−2, 2)
     are ±1. Thus, |𝑁 FE (𝑀) − 𝑁 HPO (𝑀)| = 1, implying the budget           Dataset               Samples Feat.           Source (ID / Link)
     is split as ⌊𝑀/2⌋ and ⌈𝑀/2⌉.
                                                                             airfoil_self_noise       1,503      6            OpenML (44957)
  In both cases, the adaptive selection rule minimizes the discrep-          cpu_small                8,192     12             OpenML (562)
ancy between FE and HPO allocations, ensuring that:                          diamonds                53,940      9            OpenML (42225)
                                        𝑀     𝑀                              plasma_retinol            315      13             OpenML (511)
               𝑁 FE (𝑀), 𝑁 HPO (𝑀) ∈ {⌊ ⌋, ⌈ ⌉}.                             forest-fires              517      13            OpenML (42363)
                                         2    2                              housing                 20,640      9            OpenML (43996)
This completes the proof of the budget equilibrium.                          bike                    17,389     11            OpenML (42712)
                                                                 □                                                                Kaggle
                                                                             crab                    3,893       8
                                                                                                                           (crab-age-prediction)
C Experimental Setup                                                                                                              Kaggle
                                                                             insurance               1,338       7
C.1 Dataset Details                                                                                                    (us-health-insurancedataset)

This section provides the detailed specifications for the 28 benchmark datasets used in our experiments.
                                                                                Table 5: XGBoost hyperparameter search space.
   Table 3 lists the 19 classification datasets curated by Grinsztajn
et al. [18], including their OpenML identifiers and basic dimensions.
                                                                                     Parameter                 Distribution / Range
                                                                                     Max depth                 UniformInt [1, 11]
         Table 3: Statistics for 19 classification datasets                          Num estimators            UniformInt [100, 6100, 200]
                                                                                     Min child weight          LogUniformInt [1, 1e2]
       Dataset               # Samples       # Features       OpenML ID              Subsample                 Uniform [0.5, 1]
                                                                                     Learning rate             LogUniform [1e-5, 0.7]
       rl                       4,970             12            44160                Col sample by level       Uniform [0.5, 1]
       electricity              38,474            8             44156                Col sample by tree        Uniform [0.5, 1]
       compass                  16,644            17            44162                Gamma                     LogUniform [1e-8, 7]
       wine                     2,554             11            44091                Lambda                    LogUniform [1, 4]
       house_16H                13,488            16            44123                Alpha                     LogUniform [1e-8, 1e2]
       Magic                    13,376            10            44125
       Higgs                   940,160            24            44129
       jannis                   57,580            54            44131
       credit                   16,714            10            44089
       eye_movements            7,608             23            44157     categorical features and is trained with early stopping based on
       kddCup09                 5,032             45            44158     validation scores. The search space is detailed in Table 6.
       road-safety             111,762            32            44161
       bank-marketing           10,578            7             44126
       phoneme                  3,172             5             44127               Table 6: MLP hyperparameter search space.
       covertype               423,680            54            44159
       california               20,634            8             44090
       kdd_ipums_la             5,188             20            44124               Parameter                        Distribution / Range
       MiniBooNE                72,998            50            44128               Num layers                       UniformInt [1, 8]
       pol                      10,082            26            44122               Layer size                       UniformInt [16, 1024]
                                                                                    Dropout                          Uniform [0, 0.5]
                                                                                    Learning rate                    LogUniform [1e-5, 1e-2]
  Table 4 details the regression datasets sourced from OpenML                       Category embedding size          UniformInt [64, 512]
and Kaggle.                                                                         Learning rate scheduler          {True, False}
                                                                                    Batch size                       [256, 512, 1024]
C.2      Hyperparameter Search Spaces of
         Downstream Models
This section describes the hyperparameter search spaces for the              In the CASH scenario, the search space is expanded to include
downstream models. For each task, we employ the SMAC optimizer            algorithm selection. The framework chooses among various learnto find the optimal set of hyperparameters within the budgets de-         ers (e.g., Random Forest, XGBoost, LightGBM, and MLP), with each
fined in Section 4.1.                                                     learner associated with its respective hyperparameter search space.
   For XGBoost, we adopt the hyperparameter search space used             As shown in Table 7, the setting involves a total of 42 hyperpain Grinsztajn et al. [18]. The detailed distributions and ranges are      rameters. This includes one high-level categorical parameter used
presented in Table 5.                                                     for algorithm selection, while the specific learner subspaces con-
   For MLP, we adopt the search space and architecture following          tribute the remaining 41 hyperparameters (35 continuous and 6
Gorishniy et al. [17]. The model includes learning embeddings for         categorical).

CoFEH : LLM-driven Feature Engineering Empowered by Collaborative Bayesian Hyperparameter Optimization      KDD ’26, August 09–13, 2026, Jeju Island, Republic of Korea

Table 7: Search space for CASH. We distinguish categorical                          D Additional Results and Analysis
(cat) hyperparameters from numerical (cont) ones.
                                                                                    D.1 Ablation on Different LLM Backbones
            Type of Classifier / Regressor     #𝜆    cat    cont
                                                                                   Table 8: Test error (Mean±Std) and cost comparison averaged
            Random Forest                       5     2      3                     over 3 runs. Bold and underlined values denote the best and
            Extra Trees                         5     2      3                     second-best results (lower is better). Bottom rows show the
                                                                                   average rank and average token cost.
            Gradient Boosting                   7     1      6
            MLP                                 7     1      6
            LightGBM                            7     -      7
            XGBoost                             10    -      10                        Dataset              Gemini-2.0-flash Gemini-2.5-pro             GPT-5.2
            Total (6 algos)                    41     6      35                        pol                       1.42± 0.12          1.39 ± 0.15       1.42 ± 0.11
                                                                                       wine                     19.50 ± 0.62         19.42 ± 0.55     19.35 ± 0.68
                                                                                       airfoil_self_noise       1.50 ± 0.10           1.49± 0.13      1.47 ± 0.09
                                                                                       housing (×109 )          1.89 ± 0.01           1.76± 0.01      1.72 ± 0.01
C.3      Meta-Feature for FE Pipeline                                                  Avg. Rank                    2.75                 1.75             1.50
         Characterization                                                              Avg. Cost ($)               0.088                1.515             2.123
To facilitate the collaborative optimization between Feature Engineering (FE) and Hyperparameter Optimization (HPO), CoFEH
                                                                                      This appendix investigates how the choice of LLM backbone
incorporates the meta-feature extraction framework from Mind-
                                                                                   affects the performance of CoFEH in joint FE+HPO tuning setting.
Ware [36], which consolidates and extends meta-features previ-
                                                                                   We evaluate three models—GPT-5.2, Gemini-2.5-pro, and Geminiously proposed in the meta-learning literature, including those
                                                                                   2.0-flash—across four diverse datasets: two classification tasks (pol,
from [3, 46, 58].
                                                                                   wine) and two regression tasks (airfoil_self_noise, housing). As
Motivation and shared spirit. In the original MindWare frame-
                                                                                   summarized in Table 8, all metrics are reported as averages over
work, meta-features are utilized for meta-learning to predict which
                                                                                   three independent runs to ensure statistical robustness.
algorithm (e.g., XGBoost, LightGBM) is likely to achieve the high-
                                                                                      Experimental results reveal a strong positive correlation between
est performance ceiling on a given dataset. Our approach shares
                                                                                   an LLM’s reasoning depth and the quality of synthesized operations
a similar philosophy: we posit that the optimal hyperparameter
                                                                                   when exploring the FE search space conditioned on HPO. GPT-5.2
configuration 𝝀 ∗ is inherently dependent on the current state of
                                                                                   establishes the performance ceiling, achieving the best results on
the dataset X ′ . By extracting meta-features, we allow the HPO
                                                                                   three out of four tasks and securing the top average rank of 1.50.
surrogate model to "perceive" the transformations made by the FE
                                                                                   This superiority suggests that advanced reasoning capabilities enmodule, effectively conditioning the search space on the feature
                                                                                   able the model to propose more effective feature transformations
engineering trajectory.
                                                                                   while the HPO component simultaneously optimizes the model
Implementation details. We adopt the meta-feature suite pro-
                                                                                   configuration. In contrast, Gemini-2.0-flash yields the lowest pervided in the MindWare repository1 . The extraction logic is catego-
                                                                                   formance (Rank 2.75), highlighting the limitations of lightweight
rized by task type:
                                                                                   models in executing the sophisticated semantic reasoning required
    • Classification datasets: 46 meta-features are extracted, cover-              for optimal FE synthesis under varying HPO conditions. Gemini-
      ing statistical properties (e.g., skewness, kurtosis), information-          2.5-pro serves as a highly competitive mid-tier alternative, outper-
      theoretic measures (e.g., class entropy), etc.                               forming GPT-5.2 on the pol classification task and maintaining a
    • Regression datasets: 33 meta-features are extracted, focus-                  second-best average rank of 1.75. This performance gradient sug-
      ing on feature-target correlations and dataset dimensionality                gests that the system’s effectiveness scales directly with the model’s
      characteristics, etc.                                                        “intelligence ceiling.”
                                                                                      Cost analysis illustrates a sharp trade-off between performance
Detailed definitions and the underlying source code for each metric
                                                                                   and API expenditure. Gemini-2.0-flash is the most cost-effective
are available in the referenced repository file.
                                                                                   option, with a mean task cost of 0.088—approximately 1/24th that
Orthogonality and Compatibility. It is important to empha-
                                                                                   of GPT-5.2 (2.123). While it is numerically the weakest among the
size that the design of CoFEH is orthogonal to the specific choice
                                                                                   tested models in ranking, the performance gap remains remarkof meta-features. While we utilize the MindWare suite due to its
                                                                                   ably small, which justifies its selection as our final backbone. This
robustness and comprehensive coverage, our framework is fully
                                                                                   extreme efficiency enables a much broader search scope that can
compatible with any alternative meta-feature computation method-
                                                                                   compensate for the lower per-proposal complexity through sheer
ologies. This modularity ensures that as more advanced dataset
                                                                                   volume and iteration. In summary, the performance of CoFEH is
characterization techniques emerge, they can be seamlessly inte-
                                                                                   intrinsically linked to the reasoning capacity of the LLM backbone,
grated into the CoFEH pipeline to further enhance the collaborative
                                                                                   but the ultimate selection of Gemini-2.0-flash reflects a strategic
optimization process.
                                                                                   prioritization of cost-efficiency. As LLM research continues to de-
                                                                                   liver enhanced reasoning power at lower costs, the efficacy and
1   https://github.com/PKU-DAIR/mindware/blob/master/mindware/components/          scalability of CoFEH —leveraging high-efficiency backbones—are
    meta_learning/meta_feature/meta_features.py                                    expected to increase proportionally.

KDD ’26, August 09–13, 2026, Jeju Island, Republic of Korea                                               Beicheng Xu, Keyao Ding, Wei Liu, Yupeng Lu, and Bin Cui

                            1.0                                                      presents a challenging regression task: predicting scaled self-noise

   Normalized Performance
                                                                                     across varying chord lengths, wind speeds, and angles of attack.
                            0.8                                                      Pipeline of CoFEH. The pipeline discovered by CoFEH (Fig. 8a)
                                                                                     reflects a logical progression from raw data stabilization to the
                                                                                     synthesis of physical representations. This process is structured
                            0.6
                                                                                     into five distinct functional stages:
                                                          CoFEH                          (i) Operation 1: semantic scaling (transformation): The
                            0.4                           CoFEH (Exploitaion Only)   framework identifies that predictors such as frequency span several
                                                          CoFEH (Exploration Only)   orders of magnitude. It autonomously applies a logarithmic trans-
                            0.2 0         20       40       60         80      100   formation to stabilize high-range variance, effectively shifting the
                                               Number of Evaluations                 model’s focus toward relative spectral changes rather than absolute
                                                                                     numerical values.
                                  Figure 7: Abalation study of directives.               (ii) Operation 2: physics-inspired feature generation: Lever-
                                                                                     aging aerodynamic domain knowledge, CoFEH deduces that be-
                                                                                     cause the experimental conditions involve varying airfoil scales
D.2                          Effectiveness of Steerable Reasoning                    (chord length 𝑐) and flow velocities (𝑈 ), the noise response is likely
                             Directives                                              governed by dynamic similarity. It synthesizes a Strouhal-like num-
                                                                                                  𝑓 ·𝑐
                                                                                     ber (𝑆𝑡 = 𝑈 ), collapsing diverse experimental scales into a scale-
The exploration of the unbounded FE search space in CoFEH is
                                                                                     invariant physical representation. The system further incorporates
explicitly driven by the interplay between the Exploration and
                                                                                     geometry-aware features by deriving trigonometric components
Exploitation directives. To evaluate the necessity of this dual-
                                                                                     (sin 𝑎, cos 𝑎) from the angle of attack. A notable emergent feature
engine design, we conduct an ablation study by comparing the
                                                                                     is the coupled interaction term (𝑆𝑡 · sin 𝑎), synthesized to model
full CoFEH against two restricted variants: (i) Exploration-only:
                                                                                     angle-modulated shedding effects—a sophisticated interaction.
The LLM performs four Exploration expansions per node, relying
                                                                                         (iii) Operation 3: distribution rectification (transformation).
solely on domain knowledge to probe the space without Mglobal . (ii)
                                                                                     Following symbolic discovery, the pipeline employs a Yeo-Johnson
Exploitation-only: The LLM is restricted to four Exploitation tri-
                                                                                     power transform. This step serves to rectify the distributions of the
als, forcing the FE process to refine “elite experiences” from Mglobal
                                                                                     newly synthesized physical terms, enforcing Gaussian-like normalfor novel transformations. To manage API expenditure while main-
                                                                                     ity to align them statistically with the original feature space.
taining evaluation coverage across diverse task types, we select
                                                                                         (iv) Operation 4: scale standardization (Preprocess). The
four representative datasets: two classification tasks (pol, wine)                                                                          𝑥 −𝜇
                                                                                     framework executes Z-score standardization (𝑧 = 𝜎 ). This stage
and two regression tasks (airfoil, housing). Fig. 7 plots the average
                                                                                     is critical for eliminating scale disparities between different features.
best-so-far validation performance across these tasks, where scores
                                                                                         (v) Operation 5: feature pruning (selection). The process
are min-max normalized for each dataset.
                                                                                     concludes with selection via SelectKBest (F-regression). This stage
   The results indicate that the Exploitation-only variant achieves
                                                                                     distills the expanded feature bank into an optimal set, removing rerapid initial gains by effectively leveraging high-performing "elite
                                                                                     dundant noise to prevent overfitting and enhance model efficiency.
experiences" from Mglobal . However, it suffers from premature stag-
                                                                                         CoFEH distinguishes itself through a highly unconstrained worknation, as the search logic becomes increasingly confined to a nar-
                                                                                     flow that autonomously orchestrates a fluid sequence of transformarow manifold of previously successful patterns, eventually trapping
                                                                                     tion → generation → transformation → preprocessing → selection.
the process in local optima. Conversely, the Exploration-only vari-
                                                                                     It also supports an essentially unbounded operation space, harmoant exhibits significantly slower convergence; while it consistently
                                                                                     nizing foundational mathematical primitives (e.g., log-scaling) and
probes novel regions using domain knowledge, the absence of it-
                                                                                     advanced algorithmic optimizations (e.g., Yeo-Johnson transforms,
erative refinement leads to an inefficient and scattered discovery
                                                                                     F-regression) with autonomous, domain-driven synthesis. On the
process. CoFEH synthesizes the strengths of both engines to main-
                                                                                     other hand, Fig. 8b illustrates the optimal pipelines discovered by
tain a robust balance between breadth and depth throughout the
                                                                                     baselines, revealing a notable contrast to CoFEH:
search. This synergy is quantitatively substantiated by the final
                                                                                         OCTree pursues a monolithic symbolic synthesis approach, nestperformance rankings: CoFEH achieves a dominant Average Test
                                                                                     ing multiple mathematical primitives into a single, high-order ex-
Rank of 1.0, significantly outperforming the Exploitation-only (2.25)
                                                                                     pression. While this captures complex non-linearities, the resulting
and Exploration-only (2.75) variants. These findings confirm that
                                                                                     single feature lacks a grounding in aerodynamic similarity laws
the interplay between these two steerable directives is essential for
                                                                                     (such as the Strouhal number), often leading to a representation
navigating complex, unbounded search spaces efficiently under a
                                                                                     that is difficult to generalize across different airfoil scales.
constrained budget.
                                                                                         ELLM and LFG both rely on the expansion of mathematical
                                                                                     primitives to generate multiple candidates. Crucially, the outputs
D.3                          Case Studies of Optimal FE Pipelines                    of both methods are fundamentally the results of simple arithmetic
This appendix details the feature engineering (FE) pipelines dis-                    operations. Furthermore, these methodologies are strictly confined
covered by our framework and various baselines for the NASA                          to the feature generation phase, lacking a comprehensive pipeline
Airfoil Self-Noise dataset in the standalone FE scenario. The dataset,               to integrate transformation, scale standardization, or selection.
derived from anechoic wind tunnel tests of NACA 0012 airfoils,

CoFEH : LLM-driven Feature Engineering Empowered by Collaborative Bayesian Hyperparameter Optimization                                          KDD ’26, August 09–13, 2026, Jeju Island, Republic of Korea

          Rationale & Reason                           FE Operation Pipeline                                                                                                                 2. ELLM
                                                                                                                          1. OCTree
      Why: Stabilize high-range variance            Op1: Transformation (Semantic Scaling)
                                           log(x)                                                                                                                              �1            �1 /�2
      & capture relative changes.                    Log-mapping for skewed variables (e.g.,freq)
                                                                                                    �1
                                                                                                                                                0.3
                                                                                                                                                                                             �1 /�3
                                                                                                    �2          �6 = clip((              �� )
                                                                                                                                                                               �2            �1 /�5
      Why: Embed domain knowledge by                Op2: Generation (Physics-Inspired)
      synthesizing dimensionless                                                                    �3                ·sin (…) ·cos (…)                          �6                          �5 /�1             10 New
                                                                     �·c    �∗                                                                                                 �3
      invariants and interactions                             �� =
                                                                                                                   + 0.5tan (…) + �, 0, 1)
                                                                                                                                                                                             �5 /�3             Features
                                                                      �     �                       �4
                                                                                                                                                                               �4         MinMax(�1 )
                                                                                                     �5                                                                                   MinMax(�5 )
      Why: Enforce normality to align               Op3: Transformation (Dist Rectification)
                                                                                                                                                                               �5         Sigmoid(�1 )
      feature distributions                               Yeo-Johnson Power Transform

                                                                                                                              3. LFG
                                           (� − �) Op4: Preprocess (Z-score)
      Why: Eliminate scale disparities                                                                                                                                                       4. Mindware
                                              �     Standardize to μ = 0, σ = 1                                                    ×
                                                                                                      Velocity                                                                Step 1:             Step 2:
      Why: Prune redundant interactions             Op5: Selection (Feature Pruning)                                                                   3 New                                                         Final
                                                                                                                                  (·)3                                        Rescaler        Transformation
       & noise to prevent overfitting.                    SelectKBest (F-regression)                                                                  Features                                                      Feature
                                                                                                                                                                             (Qiantile.          (feature
                                                                                                     Thickness                                                                                                        Set
                                                                                                                                   ÷                                           172)           agglomeration)
                                             Optimal Feature Set for Downstream Models

                      (a) Optimal FE pipeline of CoFEH.                                                                           (b) Optimal FE pipeline of baselines.

                            Figure 8: Optimal FE pipeline discovered by all methods on “airfoil_self_noise” dataset.

   Mindware represents a purely two-step pipeline: a quantile-                                                            5                2nd Best (Standalone FE)                       CoFEH (Standalone FE)
based Rescaler followed by a feature agglomeration Transformer.                                                                            2nd Best (FE+HPO)                              CoFEH (FE+HPO)
                                                                                                                          4

                                                                                                           Average Rank
This result reflects a purely algorithmic paradigm that reconfigures
the input space geometry through statistical motifs. Notably, it
operates without any arithmetic generation, meaning it does not                                                           3
                                                                                                                                                                      2.00     2.11             2.11     2.00
construct new symbolic features or physical interactions.                                                                 2
   Finally, we consider OpenFE, which follows a two-stage au-                                                                     1.44
                                                                                                                                                 1.22
tomated feature generation. This methodology systematically ex-                                                           1
plores thousands of candidate features by applying arithmetic prim-                                                                    Large                           Medium                      Small
itives (e.g., +, -, ×, ÷). to the original input space. Following this                                                                                   Dataset Size Category
expansive generation phase, a selection process is employed, re-                                    Figure 9: Average rank by dataset size (samples × features).
taining 122 new features in the final dataset.
   In summary, CoFEH distinguishes itself through its structural
adaptability and operational diversity, transcending the functional                                 D.4         Impact of Dataset Characteristics
constraints of all baselines through two core distinctions: (i) Work-                               In this section, we analyze the performance of CoFEH across difflow fluidity: Unlike frameworks such as Mindware and OpenFE,                                       ferent dataset scales to evaluate its robustness. We define dataset
which adhere to fixed, pre-defined sequences where stages are of-                                   size as the product of samples and features, partitioning the 28
ten mutually exclusive and restricted in depth, or baselines like                                   benchmark datasets equally into three groups: Small, Medium, and
OCTree, ELLM, and LFG, which are confined strictly to iterative fea-                                Large. Fig. 9, which compares the average rank of CoFEH against
ture generation regardless of pipeline length, CoFEH implements a                                   the runner-up baseline, (i) CoFEH consistently achieves the supetruly unconstrained workflow. Although visualized as a five-stage                                   rior average rank across all categories in both standalone FE and
pipeline in Fig. 8a, each stage may represent a sophisticated rea-                                  joint FE+HPO scenarios; and (ii) the performance advantage of
soning layer where the LLM may execute multiple concurrent op-                                      CoFEH becomes significantly more pronounced as the dataset scale
erations. This results in an underlying structural complexity that                                  increases, substantiating the robust scalability of our reasoningfar exceeds what is shown. (ii) Synthesis of algorithmic and                                        driven approach, which effectively navigates the complex search
arithmetic operations: CoFEH further bridges the gap between                                        spaces of large datasets where other methods often struggle.
disparate operational paradigms. Mindware is purely algorithmic,
relying on predefined algorithms (e.g., PCA, kernel expansions) to                                  D.5         Statistical Significance Analysis
reconfigure the input space, while other baselines are restricted to
                                                                                                    To rigorously evaluate the performance of CoFEH relative to the
symbolic arithmetic-operator synthesis (e.g., +, −, ×, ÷). In contrast,
                                                                                                    baselines, we conduct a statistical significance analysis based on
CoFEH achieves an organic integration of both worlds. It seamlessly
                                                                                                    the average ranks across all datasets. As illustrated in Fig. 10, we
blends domain-driven arithmetic discovery—such as the synthesis
                                                                                                    employ the Friedman test followed by the Nemenyi post-hoc test
of scale-invariant physical parameters with advanced algorithmic
                                                                                                    to generate Critical Difference (CD) diagrams, where methods are
optimizations like the Yeo-Johnson transform and F-regression. By
                                                                                                    connected by a horizontal line if their performance difference is
navigating an unbounded operation space, CoFEH ensures that fea-
                                                                                                    not statistically significant (𝛼 = 0.05). We can conclude two obserture representations are both physically grounded and numerically
                                                                                                    vations: (i) Standalone FE scenario (Fig. 10a): In the pure FE setting,
optimized for downstream performance.
                                                                                                    CoFEH achieves the best average rank of 1.84. The CD plot reveals
                                                                                                    that CoFEH is statistically superior to ELLM, OCTree, OpenFE, and

KDD ’26, August 09–13, 2026, Jeju Island, Republic of Korea                                                             Beicheng Xu, Keyao Ding, Wei Liu, Yupeng Lu, and Bin Cui

                        CD                                                                                              CD
                  1          2         3         4            5   6                                               1          2       3        4        5        6

                                                                                                        CoFEH [1.75]                                 [4.57] OCTree
         CoFEH [1.84]                                   [4.52] Mindware
           LFG [3.11]                                   [4.07] OpenFE                                        LFG [3.41]                              [3.93] ELLM
          ELLM [3.54]                                   [3.93] OCTree                  Mindware [3.48]                                               [3.86] OpenFE
                          (a) CD plot in standalone FE.                                                               (b) CD plot in joint FE+HPO.

                                              Figure 10: CD plot of test rank with Nemenyi post-hoc test.

Mindware, as their average ranks fall outside the critical difference
                                                                                                       0.8

                                                                                Spearman correlation
distance from CoFEH. While LFG shows competitive performance,
CoFEH maintains a clear numerical lead, establishing its efficacy
in FE pipeline optimization. (ii) Joint FE+HPO scenario (Fig. 10b):
                                                                                                       0.6
The superiority of CoFEH becomes even more pronounced in the
end-to-end task, reaching an average rank of 1.75. Crucially, in this
scenario, CoFEH is not connected to any baseline by a horizontal                                       0.4
bar, indicating that its performance gain is statistically significant                                                           w/ meta               mean w/ (0.691)
against all five competitors. This underscores that our collaborative                                                            w/o meta              mean w/o (0.587)
                                                                                                       0.2
optimization strategy (interleaved tuning) provides a distinct ad-                                            1          6         11         16        21          26
vantage over both sequential and purely unified baselines. Overall,                                                                      Dataset
Fig. 10 provides robust statistical evidence that CoFEH consistently
outperforms existing frameworks. Its ability to maintain significant          Figure 11: Spearman correlation between surrogate model
dominance in the Joint FE+HPO scenario—the most representa-                   predictions and true performance.
tive of real-world AutoML workflows—confirms its status as a new
state-of-the-art for automated pipeline optimization.
                                                                              (FE pipeline, ML configuration) pairs generated by CoFEH in the
D.6      Effectiveness of Meta-Feature for BO                                 Joint FE+HPO scenario. For each dataset, we generate out-of-fold
         Surrogate Modeling                                                   (OOF) predictions via 5-fold cross-validation—where the model
In the collaborative optimization process of CoFEH, the perfor-               is trained on four subsets to predict the fifth—to ensure evaluamance 𝑣 is jointly determined by the FE pipeline state 𝑇 and the              tion on unseen configurations and reflect true generalization. The
hyperparameter configuration 𝝀. Since FE pipelines generated by               Spearman rank correlation coefficient between the predicted and
LLMs lack an explicit, continuous search space, we utilize meta-              actual performance is then calculated as the primary metric. This
features 𝜙 (𝑠) to vectorize the dataset state after transformations.          is because the effectiveness of BO depends more on the surrogate’s
This allows the surrogate model to map discrete tree nodes into a             ability to correctly rank potential candidates than on absolute error
representative feature space. As detailed in Section 3.2.1, we for-           minimization.
malize the training dataset for the surrogate model 𝑀 as:                         The comparative results of surrogate modeling are illustrated
                                                                            in Fig. 11, where datasets are sorted by their Spearman correlation
            DBO = [𝜙 (𝑠𝑖 ), 𝝀𝑖,𝑗 ], 𝑣𝑖,𝑗 | 𝑠𝑖 ∈ Vtree, ∀𝑗 ,     (10)          in the "w/o meta-feature" setting. We observe several key trends:
where Vtree is the set of nodes in the MCTS tree, and 𝝀𝑖,𝑗 , 𝑣𝑖,𝑗             (i) Gains in FE-sensitive tasks: A significant performance gap
denote the 𝑗-th ML configuration and its corresponding score eval-            is evident on datasets where the baseline (𝑤/𝑜 meta) fits poorly.
uated on node 𝑠𝑖 . We employ Random Forest (RF) as the surrogate              For instance, the most extreme case shows a leap from 0.20 to 0.76.
model for its efficiency and robust handling of categorical variables.        This suggests that these tasks are highly sensitive to feature trans-
The role of meta-features. Without meta-features, the surrogate               formations; ignoring the FE pipeline state renders the surrogate
model is unable to perceive the impact of FE transformations, re-             model unable to capture the true performance landscape. (ii) Conducing the input to only hyperparameters 𝝀. In such a scenario,               vergence in HPO-sensitive tasks: On the right side of the plot,
the training data becomes Dbase = {(𝝀𝑖,𝑗 , 𝑣𝑖,𝑗 )}, which introduces          where the baseline already achieves high correlation, the improvesignificant noise since identical hyperparameter configurations can           ment from meta-features is relatively marginal. In these scenarios,
yield vastly different performances across different FE pipelines.            the model performance is likely dominated by hyperparameter con-
Evaluation of modeling capability. To verify the effectiveness                figurations, allowing the surrogate to perform reasonably well even
of our joint modeling, we compare the predictive power of the RF              without explicit FE information. (iii) Robustness and potential:
surrogate with and without meta-features by collecting the 200                Overall, our joint modeling approach is robust across diverse tasks.

   CoFEH : LLM-driven Feature Engineering Empowered by Collaborative Bayesian Hyperparameter Optimization                                                   KDD ’26, August 09–13, 2026, Jeju Island, Republic of Korea

                         1.0                                                                  1.0

Normalized Performance                                               Normalized Performance
                                                                                                                                        resource allocations: one dataset (“diamonds”) with an FE propor-
                                                             HPO                                                                        tion of 0.7 (FE-intensive) and another (“jannis”) with 0.37 (HPO-
                         0.5                                 FE                               0.5                                       intensive). To verify if these allocations align with the datasets’
                                                                                                                                HPO     inherent sensitivities, we conducted independent 100-iteration runs
                                                                                                                                FE
                         0.0                                                                  0.0                                       of pure FE and pure HPO on both tasks.
                               0     25      50         75     100                                  0   25      50         75     100      As illustrated in Fig. 12, the normalized validation curves re-
                                     Number of Evaluations                                              Number of Evaluations
                                                                                                                                        veal distinct performance drivers for each task: (i) Diamonds (FE-
              (a) “Diamonds”: FE prop. = 0.7.                                                 (b) “Jannis”: FE prop. = 0.37.            Sensitive): The pure FE trajectory demonstrates a substantially
                   Figure 12: Comparison between standalone FE and HPO.                                                                 higher performance ceiling and faster convergence compared to
                                                                                                                                        pure HPO. This confirms that the primary bottleneck for this task
                                                                                                                                        lies in the feature representation, validating the selector’s decision
   It provides substantial gains for FE-sensitive datasets while main-
                                                                                                                                        to prioritize FE with a 0.7 budget share. (ii) Jannis (HPO-Sensitive):
   taining or slightly improving performance for HPO-sensitive ones,
                                                                                                                                        Conversely, the pure HPO curve surpasses the FE results, indicating
   raising the mean Spearman correlation from 0.587 to 0.691. The few
                                                                                                                                        that hyperparameter configuration has a more profound impact
   points where both settings yield low correlation may stem from
                                                                                                                                        on accuracy than further feature transformations. The selector cor-
   inherent task complexity that exceeds the modeling capacity of the
                                                                                                                                        rectly identifies this sensitivity, skewing the budget toward the
   RF surrogate. Notably, as CoFEH is orthogonal to the specific choice
                                                                                                                                        Bayesian HPO module (FE ratio 0.37). These results empirically
   of meta-features, the integration of higher-quality meta-features in
                                                                                                                                        substantiate that the dynamic optimizer selector effectively detects
   the future could further elevate this performance ceiling.
                                                                                                                                        the marginal utility of FE versus HPO. By adaptively shifting focus
   D.7                             Case Study of FE vs. HPO                                                                             based on the inherent properties of the dataset, CoFEH ensures the
                                                                                                                                        search budget is concentrated where it yields the highest perfor-
  To validate the rationality of the dynamic optimizer selector, we ex-                                                                 mance gains.
  amine two representative cases from Fig. 6b exhibiting contrasting
