---
citation_key: "LiEtAl2021a"
title: "OpenBox: A Generalized Black-box Optimization Service"
authors: "Yang Li; Yu Shen; Wentao Zhang; Yuan-Wei Chen; Huaijun Jiang; Mingchao Liu; Jiawei Jiang; Jinyang Gao; Wentao Wu; Zhi Yang; Ce Zhang; B. Cui"
year: 2021
doi: "10.1145/3447548.3467061"
source: "arXiv (2106.00421)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "2106.00421"
conversion: "pdftotext -layout (automated)"
---

# OpenBox: A Generalized Black-box Optimization Service

OpenBox: A Generalized Black-box Optimization Service

                                                       Yang Li† , Yu Shen†§ , Wentao Zhang† , Yuanwei Chen† , Huaijun Jiang†§ , Mingchao Liu†
                                                            Jiawei Jiang‡ , Jinyang Gao⋄, Wentao Wu∗ , Zhi Yang† , Ce Zhang‡ , Bin Cui†∇
                                                  † Key Laboratory of High Confidence Software Technologies (MOE), School of EECS, Peking University, China
                                                                            ‡ Department of Computer Science, Systems Group, ETH Zurich, Switzerland
                                                                          ∇ Institute of Computational Social Science, Peking University (Qingdao), China
                                                                          ∗ Microsoft Research, USA ⋄ Alibaba Group, China § Kuaishou Technology, China
                                                      † {liyang.cs, shenyu, wentao.zhang, yw.chen, jianghuaijun, by_liumingchao, yangzhi, bin.cui}@pku.edu.cn
                                                             ‡ {jiawei.jiang, ce.zhang}@inf.ethz.ch ∗ wentao.wu@microsoft.com ^ jinyang.gjy@alibaba-inc.com

arXiv:2106.00421v3 [cs.LG] 4 Nov 2021
                                                                                                                                               System/Package   Multi-obj.   FIOC   Constraint   History   Distributed
                                        ABSTRACT                                                                                               Hyperopt             ×         ✓         ×          ×           ✓
                                                                                                                                               Spearmint            ×         ×         ✓          ×           ×
                                        Black-box optimization (BBO) has a broad range of applications,                                        SMAC3                ×         ✓         ×          ×           ×
                                        including automatic machine learning, engineering, physics, and                                        BoTorch              ✓         ×         ✓          ×           ×
                                                                                                                                               GPflowOpt            ✓         ×         ✓          ×           ×
                                        experimental design. However, it remains a challenge for users                                         Vizier               ×         ✓         ×          △           ✓
                                        to apply BBO methods to their problems at hand with existing                                           HyperMapper          ✓         ✓         ✓          ×           ×
                                                                                                                                               HpBandSter           ×         ✓         ×          ×           ✓
                                        software packages, in terms of applicability, performance, and ef-                                     OpenBox              ✓         ✓         ✓          ✓           ✓
                                        ficiency. In this paper, we build OpenBox, an open-source and
                                        general-purpose BBO service with improved usability. The modu-                                    Table 1: A taxonomy of BBO systems/softwares. Multi-obj.
                                        lar design behind OpenBox also facilitates flexible abstraction and                               notes whether the system supports multiple objectives or
                                        optimization of basic BBO components that are common in other                                     not. FIOC indicates if the system supports all Float, Integer,
                                        existing systems. OpenBox is distributed, fault-tolerant, and scal-                               Ordinal and Categorical variables. Constraint refers to the
                                        able. To improve efficiency, OpenBox further utilizes “algorithm                                  support for inequality constraints. History represents the
                                        agnostic” parallelization and transfer learning. Our experimental                                 ability of the system to inject the prior knowledge from pre-
                                        results demonstrate the effectiveness and efficiency of OpenBox                                   vious tasks in the search. Distributed notes if it supports par-
                                        compared to existing systems.                                                                     allel evaluations under a distributed environment. △ means
                                                                                                                                          the system cannot support it for many cases. Note that,
                                                                                                                                          BoTorch, as a framework, might provide the algorithmic
                                        CCS CONCEPTS                                                                                      building blocks for a developer to implement some of these
                                        • Computing methodologies → Search methodologies; • In-                                           capacities.
                                        formation systems;
                                                                                                                                          1   INTRODUCTION
                                        KEYWORDS                                                                                          Black–box optimization (BBO) is the task of optimizing an objective
                                        Bayesian Optimization, Black-box Optimization                                                     function within a limited budget for function evaluations. “Black-
                                                                                                                                          box” means that the objective function has no analytical form so
                                        ACM Reference Format:
                                                                                                                                          that information such as the derivative of the objective function is
                                        Yang Li† , Yu Shen†§ , Wentao Zhang† , Yuanwei Chen† , Huaijun Jiang†§ ,                          unavailable. Since the evaluation of objective functions is often ex-
                                        Mingchao Liu† and Jiawei Jiang‡ , Jinyang Gao⋄ , Wentao Wu∗ , Zhi Yang† , Ce                      pensive, the goal of black-box optimization is to find a configuration
                                        Zhang‡ , Bin Cui†∇ . 2021. OpenBox: A Generalized Black-box Optimization                          that approaches the global optimum as rapidly as possible.
                                        Service. In Proceedings of the 27th ACM SIGKDD Conference on Knowledge                               Traditional BBO with a single objective has many applications:
                                        Discovery and Data Mining (KDD ’21), August 14–18, 2021, Virtual Event,                           1) automatic A/B testing, 2) experimental design [15], 3) knobs
                                        Singapore. ACM, New York, NY, USA, 12 pages. https://doi.org/10.1145/                             tuning in database [46, 48, 49], and 4) automatic hyper-parameter
                                        3447548.3467061                                                                                   tuning [6, 27, 32, 44], one of the most indispensable components
                                                                                                                                          in AutoML systems [1, 34] such as Microsoft’s Azure Machine
                                                                                                                                          Learning, Google’s Cloud Machine Learning, Amazon Machine
                                                                                                                                          Learning [35], and IBM’s Watson Studio AutoAI, where the task is
                                        Permission to make digital or hard copies of all or part of this work for personal or             to minimize the validation error of a machine learning algorithm
                                        classroom use is granted without fee provided that copies are not made or distributed
                                        for profit or commercial advantage and that copies bear this notice and the full citation         as a function of its hyper-parameters. Recently, generalized BBO
                                        on the first page. Copyrights for components of this work owned by others than ACM                emerges and has been applied to many areas such as 1) processor
                                        must be honored. Abstracting with credit is permitted. To copy otherwise, or republish,           architecture and circuit design [2], 2) resource allocation [18], and
                                        to post on servers or to redistribute to lists, requires prior specific permission and/or a
                                        fee. Request permissions from permissions@acm.org.                                                3) automatic chemical design [22], which requires more general
                                        KDD ’21, August 14–18, 2021, Virtual Event, Singapore                                             functionalities that may not be supported by traditional BBO, such
                                        © 2021 Association for Computing Machinery.                                                       as multiple objectives and constraints. As examples of applications
                                        ACM ISBN 978-1-4503-8332-5/21/08. . . $15.00
                                        https://doi.org/10.1145/3447548.3467061                                                           of generalized BBO in the software industry, Microsoft’s Smart
                                                                                                                                      1

Buildings project [36] searches for the best smart building designs                              6

by minimizing both energy consumption and construction costs                                     5

(i.e., BBO with multiple objectives); Amazon Web Service aims                                    4
to optimize the performance of machine learning models while                              Rank
                                                                                                 3
enforcing fairness constraints [39] (i.e., BBO with constraints).
                                                                                                 2
    Many software packages and platforms have been developed for
                                                                                                 1
traditional BBO (see Table 1). Yet, to the best of our knowledge, so                                                                        r
                                                                                                        Tor
                                                                                                            ch       Op
                                                                                                                        t
                                                                                                                           min
                                                                                                                               t
                                                                                                                                         ppe MAC
                                                                                                                                                3       opt
far there is no platform that is designed to target generalized BBO.                                 Bo          flow Spear           Ma            p er
                                                                                                               GP                 per
                                                                                                                                            S     Hy
                                                                                                                             H  y
The existing BBO packages have the following three limitations
when applied to general BBO scenarios:
                                                                             Figure 1: Performance rank of softwares on 25 AutoML tasks
(1) Restricted scope and applicability. Restricted by the underlying
                                                                             (lower is better). The box extends from the lower to the upper
algorithms, most existing BBO implementations cannot handle di-
                                                                             quartile values, with a line at the median. The whiskers that
verse optimization problems in a unified manner (see Table 1). For
                                                                             extend the box show the range of the data.
example, Hyperopt [6], SMAC3 [27], and HpBandSter [13] can only
deal with single-objective problems without constraints. Though              OpenBox allows users to define their tasks and access the gener-
BoTorch [3] and GPflowOpt [30] can be used, as a framework, for              alized BBO service conveniently via a task description language
developers to implement new optimization problems with multi-                (TDL) along with customized interfaces. OpenBox also introduces
objectives and constraints; nevertheless, their current off-the-shelf        a high-level parallel mechanism by decoupling basic components
supports are also limited (e.g., the support for non-continuous pa-          in common optimization algorithms, which is “algorithm agnostic”
rameters).                                                                   and enables parallel execution in both synchronous and asynchro-
(2) Unstable performance across problems. Most existing software             nous settings. Moreover, OpenBox also provides a general transfer-
                                                                             learning framework for generalized BBO, which can leverage the
packages only implement one or very few BBO algorithms. Ac-
                                                                             prior knowledge acquired from previous tasks to improve the efficording to the “no free lunch” theorem [26], no single algorithm
                                                                             ciency of the current optimization task. In terms of algorithm design,
can achieve the best performance for all BBO problems. Therefore,
                                                                             OpenBox can host most of the state-of-the-art optimization algoexisting packages would inevitably suffer from unstable perfor-
                                                                             rithms and make their performances more stable via an automatic
mance when applied to different problems. Figure 1 presents a
                                                                             algorithm selection module, which can choose proper optimization
brief example of hyper-parameter tuning across 25 AutoML tasks,
                                                                             algorithm for a given problem automatically. Furthermore, Openwhere for each problem we rank the packages according to their
                                                                             Box also supports multi-fidelity and early-stopping algorithms for
performances. We can observe that all packages exhibit unstable
                                                                             further optimization of algorithm efficiency.
performance, and no one consistently outperforms the others. This
poses challenges on practitioners to select the best package for a           Contributions. In summary, our main contributions are:
specific problem, which usually requires deep domain knowledge/-             C1. An open-sourced service for generalized BBO. To the best of our
expertise and is typically very time-consuming.                              knowledge, OpenBox is the first open-sourced service for efficient
(3) Limited scalability and efficiency. Most existing packages exe-          and general black-box optimization.
cute optimization in a sequential manner, which is inherently ineffi-        C2. Ease of use. OpenBox provides user-friendly interfaces, visucient and unscalable. However, extending the sequential algorithm            alization, resource-aware management, and automatic algorithm
to make it parallelizable is nontrivial and requires significant engi-       selection for consistent performance.
neering efforts. Moreover, most existing systems cannot support              C3. High efficiency and scalability. We develop scalable and general
transfer learning to accelerate the optimization on a similar task.          frameworks for transfer-learning and distributed parallel execu-
    With these challenges, in this paper we propose OpenBox, a               tion in OpenBox. These building blocks are properly integrated to
system for generalized black-box optimization. The design of Open-           handle diverse optimization scenarios efficiently.
Box follows the philosophy of providing “BBO as a service” — in-             C4. State-of-the-art performance. Our empirical evaluation demonstead of developing another software package, we opt to implement            strates that OpenBox achieves state-of-the-art performance com-
OpenBox as a distributed, fault-tolerant, scalable, and efficient ser-       pared to existing systems over a wide range of BBO tasks.
vice, which addresses the aforementioned challenges in a uniform             Moving Forward. With the above advantages and features, Openmanner and brings additional advantages such as ease of use, porta-          Box can be used for optimizing a wide variety of different applicability, and zero maintenance. In this regard, Google’s Vizier [19]           tions in an industrial setting. We are currently conducting an initial
is perhaps the only existing BBO service as far as we know that              deployment of OpenBox in Kuaishou, one of the most popular
follows the same design philosophy. Nevertheless, Vizier only                “short video” platforms in China, to automate the tedious process
supports traditional BBO, and cannot be applied to general scenar-           of hyperparameter tuning. Initial results have suggested we can
ios with multiple objectives and constraints that OpenBox aims for.          outperform human experts.
Moreover, unlike Vizier, which remains Google’s internal service
as of today, we have open-sourced OpenBox that is available at               2    BACKGROUND AND RELATED WORK
https://github.com/PKU-DAIR/open-box.
    The key novelty of OpenBox lies in both the system implemen-             Generalized Black-box Optimization (BBO). Black-box optitation and algorithm design. In terms of system implementation,              mization makes few assumptions about the problem, and is thus
                                                                             applicable in a wide range of scenarios. We define the generalized
                                                                         2

BBO problem as follows. The objective function of generalized BBO
is a vector-valued black-box function 𝒇 (𝒙) : X → R𝑝 , where X                         Service Master                                Evaluation
                                                                                                                                   Suggestion
                                                                                                                                       Server
                                                                                                                                     Server
is the search space of interest. The goal is to identify the set of
Pareto optimal solutions P ∗ = {𝒇 (𝒙) s.t.  𝒙 ′ ∈ X : 𝒇 (𝒙 ′ ) ≺ 𝒇 (𝒙)},              Task Database
                                                                                                                Multi-Objective BO
                                                                                                                 with Constraints
                                                                                                                                           Resource-oriented Service

such that any improvement in one objective means deteriorating                                                Adaptive Algorithm
                                                                                                                                     Transfer-Learning    Parallel BO
another. To approximate P ∗ , we compute the finite Pareto set P                                                  Selection

                                                                                                                                                    Suggestion Service
from observed data {(𝒙 𝒊 , 𝒚 𝒊 )}𝑛𝑖=1 . When 𝑝 = 1, the problem becomes single-objective BBO, as P = {𝑦best } where 𝑦best is defined                                                                                                       Cloud
                                                                                                                           REST API
as the best objective value observed. We also consider the case with                                                          Data privacy protection
black-box inequality constraints. Denote the set of feasible points
by C = {𝒙 : 𝑐 1 (𝒙) ≤ 0, . . . , 𝑐𝑞 (𝒙) ≤ 0}. Under this setting, we aim                                  Evaluation
                                                                                                         Evaluation
                                                                                                        Evaluation                      User
                                                                                                           Worker
                                                                                                          Worker
to identify the feasible Pareto set Pfeas = {𝒇 (𝒙) s.t. 𝒙 ∈ C,  𝒙 ′ ∈                                   Worker

X : 𝒇 (𝒙 ′ ) ≺ 𝒇 (𝒙), 𝒙 ′ ∈ C}.
                                                                                                  Figure 2: Architecture of OpenBox.
Black-box Optimization Methods. Black-box optimization has
been studied extensively in many fields, including derivative-free
                                                                                3.1     Definitions
optimization [42], Bayesian optimization (BO) [43], evolutionaray               Throughout the paper, we use the following terms to describe the
algorithms [23], multi-armed bandit algorithms [31, 45], etc. To                semantics of the system:
optimize expensive-to-evaluate black-box functions with as few                  Configuration. Also called suggestion, a vector 𝒙 sampled from the
evaluations as possible, OpenBox adopts BO, one of the most pre-                given search space X; each element in 𝒙 is an assignment of a
vailing frameworks in BBO, as the basic optimization framework.                 parameter from its domain.
BO iterates between fitting probabilistic surrogate models and de-              Trial. Corresponds to an evaluation of a configuration 𝒙, which has
termining which configuration to evaluate next by maximizing an                 three status: Completed, Running, Ready. Once a trial is completed,
acquisition function. With different choices of acquisition functions,          we can obtain the evaluation result 𝒇 (𝒙).
BO can be applied to generalized BBO problems.                                  Task. A BBO problem over a search space X. The task type is iden-
   BBO with Multiple Objectives. Many multi-objective BBO algo-                 tified by the number of objectives and constraints.
rithms have been proposed [4, 5, 25, 29, 38]. Couckuyt et. al. [7]              Worker. Refers to a process responsible for executing a trial.
propose the Hypervolume Probability of Improvement (HVPOI);
Yang et. al. [47] and Daulton et. al. [8] use the Expected Hypervol-            3.2     Goals and Principles
ume Improvement (EHVI) metrics.                                                 3.2.1 Design Goal. As mentioned before, OpenBox’s design satis-
   BBO with Black-box Constraints. Gardner et.al. [16] present Prob-
                                                                                fies the following desiderata:
ability of Feasibility (PoF), which uses GP surrogates to model the
constraints. In general, multiplying PoF with the unconstrained ac-                   • Ease of use. Minimal user effort, and user-friendly visualizaquisition function produces the constrained version of it. SCBO [12]                    tion for tracking and managing BBO tasks.
employs the trust region method and scales to large batches by ex-                    • Consistent performance. Host state-of-the-art optimization
tending Thompson sampling to constrained optimization. Other                            algorithms; choose the proper algorithm automatically.
methods handle constraints in different ways [21, 24, 40]. For multi-                 • Resource-aware management. Give cost-model based advice
objective optimization with constraints, PESMOC [17] and MES-                           to users, e.g., minimal workers or time-budget.
MOC [5] support constraints by adding the entropy of the condi-                       • Scalability. Scale to dimensions on the number of input varitioned predictive distribution.                                                         ables, objectives, tasks, trials, and parallel evaluations.
BBO Systems and Packages. Many of these algorithms have                               • High efficiency. Effective use of parallel resources, system
available open-source implementations. BoTorch, GPflowOpt and                           optimization with transfer-learning and multi-fidelities, etc.
HyperMapper implement several BO algorithms to solve mathe-                           • Fault tolerance, extensibility, and data privacy protection.
matical problems in different settings. Within the machine learn-
                                                                                3.2.2 Design Principles. We present the key principles underlying
ing community, Hyperopt, Spearmint, SMAC3 and HpBandSter
aim to optimize the hyper-parameters of machine learning models.                the design of OpenBox.
Google’s Vizier is one of the early attempts in building service                   P1: Provide convenient service API that abstracts the imfor BBO. We also note that Facebook Ax1 provides high-level API                 plementation and execution complexity away from the user.
for BBO with BoTorch as its Bayesian optimization engine.                       For ease of use, we adopt the “BBO as a service” paradigm and im-
                                                                                plement OpenBox as a managed general service for black-box opti-
                                                                                mization. Users can access this service via REST API conveniently
3    SYSTEM OVERVIEW                                                            (see Figure 2), and do not need to worry about other issues such
In this section, we provide the basic concepts in the paper, explore            as environment setup, software maintenance, programming, and
the design principles in implementing black-box optimization (BBO)              optimization of the execution. Moreover, we also provide a Web UI,
as a service, and describe the system architecture.                             through which users can easily track and manage the tasks.
                                                                                   P2: Separate optimization algorithm selection complexity
                                                                                away from the user. Users do not need to disturb themselves with
1 https://github.com/facebook/ax                                                choosing the proper algorithm to solve a specific problem via the
                                                                            3

    task_config = {                                                            4.1     Service Interfaces
      " parameter ": {
          " x1 ": { " type ": " float " , " default ": 0 ,                     4.1.1 Task Description Language. For ease of usage, we design a
             " bound ": [ -5 , 10]} ,
          " x2 ": {" type ": " int " , " bound ": [0 , 15]} ,                  Task Description Language (TDL) to define the optimization task.
          " x3 ": {" type ": " cat " , " default ": " a1 " ,                   The essential part of TDL is to define the search space, which in-
             " choice ": [" a1 " , " a2 " , " a3 "]} ,
          " x4 ": {" type ": " ord " , " default ": 1 ,                        cludes the type and bound for each parameter and the relationships
             " choice ": [1 , 2 , 3]}} ,                                       among them. The parameter types — FLOAT, INTEGER, ORDINAL
      " condition ": {
             " cdn1 ": {" type ": " equal " , " parent ": " x3 " ,             and CATEGORICAL are supported in OpenBox. In addition, users
                " child ": " x1 " , " value ": " a3 "}} ,                      can add conditions of the parameters to further restrict the search
      " number_of_tri a l s ": 200 ,
      " time_budget ": 10800 ,                                                 space. Users can also specify the time budget, task type, number of
      " task_type ": " soc " ,
      " p ara ll el_ str a t e g y ": " async " ,                              workers, parallel strategy and use of history in TDL. Figure 3 gives
      " worker_num ": 10 ,                                                     an example of TDL. It defines four parameters x1-4 of different
      " use_history ": True
      }                                                                        types and a condition cdn1, which indicates that x1 is active only
                                                                               if x3 = “a3”. The time budget is three hours, the parallel strategy
                                                                               is async, and transfer learning is enabled.
     Figure 3: An example of Task Description Language.
automatic algorithm selection module. Furthermore, an important                4.1.2 Basic Workflow. Given the TDL for a task, the basic workflow
decision is to keep our service stateless (see Figure 2), so that we can       of OpenBox is implemented as follows:
seamlessly switch algorithms during a task, i.e., dynamically choose           # Register the worker with a task .
                                                                               glob al_tas k_id = worker . CreateTask ( task_tdl )
the algorithm that is likely to perform the best for a particular task.        worker . BindTask ( glo bal_ta sk_id )
This enables OpenBox to achieve satisfactory performance once                  while not worker . TaskFinished ():
                                                                                    # Obtain a configuration to evaluate .
the BBO algorithm is selected properly.                                             config = worker . G etSugg estion s ()
   P3: Support general distributed parallelization and trans-                       # Evaluate the objective function .
                                                                                    result = Evaluate ( config )
fer learning. We aim to provide users with full potential to improve                # Report the evaluated results to the server .
the efficiency of the BBO service. We design an “algorithm agnos-                   worker . U p d a t e O b s e r v a t i o n s ( config , result )
tic” mechanism that can parallelize the BBO algorithms (Sec. 5.1),
                                                                               Here Evaluate is the evaluation procedure of objective function
through which we do not need to re-design the parallel version for
                                                                               provided by users. By calling CreateTask, the worker obtains a
each algorithm individually. Moreover, if the optimization history
                                                                               globally unique identifier global_task_id. All workers registered
over similar tasks is provided, our transfer learning framework can
                                                                               with the same global_task_id are guaranteed to link with the
leverage the history to accelerate the current task (Sec. 5.2).
                                                                               same task, which enables parallel evaluations. While the task is
   P4: Offer resource-aware management that saves user ex-
                                                                               not finished, the worker continues to call GetSuggestions and
pense. OpenBox implements a resource-aware module and offers
                                                                               UpdateObservations to pull suggestions from the suggestion
advice to users, which can save expense or resources for users es-
                                                                               service and update their corresponding observations.
pecially in the cloud environment. Using performance-resource
extrapolation (Sec. 4.4), OpenBox can estimate 1) the minimal num-             4.1.3 Interfaces. Users can interact with the OpenBox service via
ber of workers users need to complete the current task within the              a REST API. We list the most important service calls as follows:
given time budget, or 2) the minimal time budget to finish the cur-
                                                                                     • Register: It takes as input the global_task_id, which is
rent task given a fixed number of workers. For tasks that involve
                                                                                       created when calling CreateTask from workers, and binds
expensive-to-evaluate functions, low-fidelity or early-stopped eval-
                                                                                       the current worker with the corresponding task. This allows
uations with less cost could help accelerate the convergence of the
                                                                                       for sharing the optimization history across multiple workers.
optimization process (Sec. 5.3).
                                                                                     • Suggest: It suggests the next configurations to evaluate,
                                                                                       given the historical observations of the current task.
                                                                                     • Update: This method updates the optimization history with
3.3      System Architecture                                                           the observations obtained from workers. The observations
                                                                                       include three parts: the values of the objectives, the results
Based on these design principles, we build OpenBox as depicted in
                                                                                       of constraints, and the evaluation information.
Figure 2, which includes five main components. Service Master is
                                                                                     • StopEarly: It returns a boolean value that indicates whether
responsible for node management, load balance, and fault tolerance.
                                                                                       the current evaluation should be stopped early.
Task Database holds the states of all tasks. Suggestion Service creates
                                                                                     • Extrapolate: It uses performance-resource extrapolation,
new configurations for each task. REST API establishes the bridge
                                                                                       and interactively gives resource-aware advice to users.
between users/workers and suggestion service. Evaluation workers
are provided and owned by the users.
                                                                               4.2     Automatic Algorithm Selection
                                                                               OpenBox implements a wide range of optimization algorithms to
                                                                               achieve high performance in various BBO problems. Unlike the
4     SYSTEM DESIGN                                                            existing software packages that use the same algorithm for each
In this section, we elaborate on the main features and components              task and the same setting for each algorithm, OpenBox chooses the
of OpenBox from a service perspective.                                         proper algorithm and setting according to the characteristic of the
                                                                           4

incoming task. We use the classic EI [37] for single-objective optimization task. For multi-objective problems, we select EHVI [11]
when the number of objectives is less than 5; we use MESMO [4]
algorithm for problems with a larger number of objectives, since
EHVI’s complexity increases exponentially as the number of objectives increases, which not only incurs a large computational
overhead but also accumulates floating-point errors. We select the
surrogate models in BO depending on the configuration space and
                                                                              Figure 4: An example of the Parallel Coordinates Visualizathe number of trials: If the input space has conditions, such as one
                                                                              tion for configurations when tuning LightGBM.
parameter must be less than another parameter, or there are over 50
parameters in the input space, or the number of trials exceeds 500,           expense for users. Concretely, we use a weighted cost model to
we choose the Probabilistic Random Forest proposed in [27] instead            extrapolate the performance vs. trial curve. It uses several parametof Gaussian Process (GP) as the surrogate to avoid incompatibil-              ric decreasing saturating function families as base models, and we
ity or high computational complexity of GP. Otherwise, we use                 apply MCMC inference to estimate the parameters of the model.
GP [10]. In addition, OpenBox will use the L-BFGS-B algorithm to              Given the existing observations, OpenBox trains a cost model as
optimize the acquisition function if the search space only contains           above and uses it to predict the number of trials at which the curve
FLOAT and INTEGER parameters; it applies an interleaved local and             approaches the optimum. Based on this prediction and the cost of
random search when some of the parameters are not numerical.                  each evaluation, OpenBox estimates the minimal resource needed
More details about the algorithms implemented in OpenBox are                  to reach satisfactory performance (more details in Appendix A.1).
discussed in Appendix A.2.
                                                                                 Application Example. Two interesting applications that save ex-
4.3    Parallel Infrastructure                                                pense for users are listed as follows:
OpenBox is designed to generate suggestions for a large number of             Case 1. Given a fixed number of workers, OpenBox outputs a mintasks concurrently, and a single machine would be insufficient to             imal time budget 𝐵 min to finish this task based on the estimated
handle the workload. Our suggestion service is therefore deployed             evaluation cost of workers. With this estimation, users can stop the
across several machines, called suggestion servers. Each suggestion           task in advance if the given time budget 𝐵 task > 𝐵 min ; otherwise,
server generates suggestions for several tasks in parallel, giving            users should increase the time budget to 𝐵 min .
us a massively scalable suggestion infrastructure. Another main               Case 2. Given a fixed time budget 𝐵 task and initial number of workcomponent is service master, which is responsible for managing                ers, OpenBox can suggest the minimal number of workers 𝑁 min
the suggestion servers and balancing the workload. It serves as the           to finish the current task within 𝐵 task by adjusting the number of
unified endpoint, and accepts the requests from workers; in this              workers to 𝑁 min dynamically.
way, each worker does not need to know the dispatching details.
The worker requests new configurations from the suggestion server
and the suggestion server generates these configurations based on an          4.5    Augmented Components in OpenBox
algorithm determined by the automatic algorithm selection module.             Extensibility and Benchmark Support. OpenBox’s modular design
Concretely, in this process, the suggestion server utilizes the local
                                                                              allows users to define their suggestion algorithms easily by inheritpenalization based parallelization mechanism (Sec. 5.1) and transfer-
                                                                              ing and implementing an abstract Advisor. The key abstraction
learning framework (Sec. 5.2) to improve the sample efficiency.
                                                                              method of Advisor is GetSuggestions, which receives the ob-
    One main design consideration is to maintain a fault-tolerant pro-
                                                                              servations of the current task and suggests the next configurations
duction system, as machine crash happens inevitably. In OpenBox,
                                                                              to evaluate based on the user-defined policy. In addition, OpenBox
the service master monitors the status of each server and preserves
                                                                              provides a benchmark suite of various BBO problems to benchmark
a table of active servers. When a new task comes, the service master
                                                                              the optimization algorithms.
will assign it to an active server and record this binding information.
                                                                              Data Privacy Protection. In some scenarios, the names and ranges of
If one server is down, its tasks will be dispatched to a new server by
                                                                              parameters are sensitive, e.g., in hyper-parameter tuning, the paramthe master, along with the related optimization history stored in the
                                                                              eter names may reveal the architecture details of neural networks.
task database. Load balance is one of the most important guidelines
                                                                              To protect data privacy, the REST API applies a transformation to
to make such task assignments. In addition, the snapshot of service
                                                                              anonymize the parameter-related information before sending it to
master is stored in the remote database service; if the master is
                                                                              the service. This transformation involves 1) converting the paramedown, we can recover it by restarting the node and fetching the
                                                                              ter names to some regular ones like “param1” and 2) rescaling each
snapshot from the database.
                                                                              parameter to a default range that has no semantic. The workers can
                                                                              perform an inverse transformation when receiving an anonymous
4.4    Performance-Resource Extrapolation                                     configuration from the service.
In the setting of parallel infrastructure with cloud computing, sav-          Visualization. OpenBox provides an online dashboard based on
ing expense is one of the most important concerns from users.                 TensorBoardX which enables users to monitor the optimization
OpenBox can guide users to configure their resources, e.g., the               process and check the evaluation info of the current task. Figure 4
minimal number of workers or time budget, which further saves                 visualizes the evaluation results in a hyper-parameter tuning task.
                                                                          5

         1         4          7                  1     5           8               12
                                                                                             Algorithm 1: Pseudo code for Sample configuration
                                                                                                Input: the hyper-parameter space X , configuration observations 𝐷 = { (𝒙 𝒊 , 𝒚𝒊 ) }𝑛 𝑖=1 ,
         2         5          8                  2         6   7             10
                                                                                                         configurations being evaluated 𝐶 eval , surrogate model 𝑀 , and acquisition
                                                                                                         function 𝛼 ( ·) .
         3         6          9                  3 4                   9          11        1   calculate 𝒚ˆ , the median of {𝒚𝒊 }𝑛
                                                                                                                                  𝑖=1 ;
                                                                                            2   create new observations 𝐷 new = { (𝒙 eval , ˆ
                                                                                                                                            𝒚) : 𝒙 eval ∈ 𝐶 eval };
                                                                                            3   fit a surrogate model 𝑀 (e.g., a GP) on 𝐷 aug , where 𝐷 aug = 𝐷 ∪ 𝐷 new , and build the
                Idle       Time                                            Time
                                                                                                   acquisition function 𝛼 (𝒙, 𝑀) using 𝑀 ;
                                                                                            4   return the configuration 𝒙¯ = argmax𝒙∈X 𝛼 (𝒙, 𝑀) .
Figure 5: An illustration of the synchronous (left) and asynchronous (right) parallel methods using three workers. The                                  based on 𝑀 1:𝐾 and 𝑀𝑇 , 2) we then build a transfer learning surronumbers above the horizontal lines are the configuration                                    gate by combining all base surrogates:
ids, and the short vertical lines indicate when a worker finished the evaluation of last configuration.                                                                         𝑀 TL = agg({𝑀 1, ..., 𝑀 𝐾 , 𝑀𝑇 }; w);
5 SYSTEM OPTIMIZATIONS
                                                                                            3) the surrogate 𝑀 TL is used to guide the configuration search,
5.1 Local Penalization based Parallelization                                                instead of the original 𝑀𝑇 . Concretely, we combine the multiple
Most proposed Bayesian optimization (BO) approaches only allow                              base surrogates (agg) linearly, and the parameters w are calculated
the exploration of the parameter space to occur sequentially. To                            based on the ranking of configurations, which reflects the similarity
fully utilize the computing resources in a parallel infrastructure, we                      between the source and target task (see details in Appendix A.3).
provide a mechanism for distributed parallelization, where multiple                         Scalability discussion A more intuitive alternative is to obtain
configurations can be evaluated concurrently across workers. Two                            a transfer learning surrogate by using all observations from 𝐾 + 1
parallel settings are considered (see Figure 5):                                            tasks, and this incurs a complexity of O (𝑘 3𝑛 3 ) for 𝑘 tasks with 𝑛
1) Synchronous parallel setting. The worker pulls new configuration                         trials each (since GP has O (𝑛 3 ) complexity). Therefore, it is hard
from suggestion server to evaluate until all the workers have finished                      to scale to a larger number of source tasks (a large 𝑘). By training
their last evaluations.                                                                     base surrogates individually, the proposed framework is a more
2) Asynchronous parallel setting. The worker pulls a new configu-                           computation-efficient solution that has O (𝑘𝑛 3 ) complexity.
ration when the previous evaluation is completed.
   Our main concern is to design an algorithm-agnostic mechanism                            5.3       Additional Optimizations
that can parallelize the optimization algorithms under the sync and
                                                                                            OpenBox also includes two additional optimizations that can be
async settings easily, so we do not need to implement the parallel
                                                                                            applied to improve the efficiency of black-box optimizations.
version for each algorithm individually. To this end, we propose
a local penalization based parallelization mechanism, the goal of                           5.3.1 Multi-Fidelity Support and Applications. During each evaluwhich is to sample new configurations that are promising and far                            ation in the multi-fidelity setting [33, 41], the worker receives an
enough from the configurations being evaluated by other work-                               additional parameter, indicating how many resources are used to
ers. This mechanism can handle the well-celebrated exploration vs.                          evaluate this configuration. The resource type needs to be speciexploitation trade-off, and meanwhile prevent workers from explor-                          fied by users. For example, in hyper-parameter tuning, it can be
ing similar configurations. Algorithm 1 gives the pseudo-code of                            the number of iterations for an iterative algorithm and the size
sampling a new configuration under the sync/async settings. More                            of dataset subset. The trial with partial resource returns a lowdiscussion about this is provided in Appendix A.4.                                          fidelity result with a cheap evaluation cost. Though not as precise
                                                                                            as high-fidelity results, the low-fidelity results can provide some
5.2     General Transfer-Learning Framework                                                 useful information to guide the configuration search. In OpenBox,
When performing BBO, users often run tasks that are similar to                              we have implemented several multi-fidelity algorithms, such as
previous ones. This fact can be used to speed up the current task.                          MFES-HB [33].
Compared with Vizier, which only provides limited transfer learning functionality for single-objective BBO problems, OpenBox em-                            5.3.2 Early-Stopping Strategy. Orthogonal to the above optimizaploys a general transfer learning framework with the following                              tion, early-stopping strategies aim to stop a poor trial in advance
advantages: 1) support for the generalized black-box optimization                           based on its intermediate results. In practice, a worker can periodiproblems, and 2) compatibility with most BO methods.                                        cally ask suggestion service whether it should terminate the current
   OpenBox takes as input observations from 𝐾 + 1 tasks: 𝐷 1 , ...,                         evaluation early. In OpenBox, we provide two early-stopping strate-
𝐷 for 𝐾 previous tasks and 𝐷𝑇 for the current task. Each 𝐷 𝑖 =
  𝐾                                                                                         gies: 1) learning curve extrapolation based methods [9, 28] that stop
{(𝒙 𝑖𝑗 , 𝒚𝑖𝑗 )}𝑛𝑗=1
                 𝑖
                    , 𝑖 = 1, ..., 𝐾, includes a set of observations. Note that,             the poor configurations by estimating the future performance, and
𝒚 is an array, including multiple objectives for configuration 𝒙.                           2) mean or median termination rules based on comparing the cur-
   For multi-objective problems with 𝑝 objectives, we propose to                            rent result with previous ones.
transfer the knowledge about 𝑝 objectives individually. Thus, the
transfer learning of multiple objectives is turned into 𝑝 single-                           6     EXPERIMENTAL EVALUATION
objective transfer learning processes. For each dimension of the                            In this section, we compare the performance and efficiency of Openobjectives, we take RGPE [14] as the base method. 1) We first train                         Box against existing software packages on multiple kinds of blacka surrogate model 𝑀 𝑖 on 𝐷 𝑖 for the 𝑖 𝑡ℎ prior task and 𝑀𝑇 on 𝐷𝑇 ;                         box optimization tasks, including tuning tasks in AutoML.
                                                                                        6

                                                                                                                          20
                        1.50                                Random                   CMA-ES                                                           Random             CMA-ES                                                                        Random               CMA-ES                                   3.0                                 Random               CMA-ES
                                                            2×Random                 BoTorch                                                          2×Random           BoTorch                                   4                                   2×Random             BoTorch                                                                      2×Random             BoTorch
                        1.25

   Optimality Gap                                                                                        Optimality Gap                                                                           Optimality Gap                                                                                    Optimality Gap
                                                            SMAC3                    HyperMapper                          15                          SMAC3              HyperMapper                                                                   SMAC3                HyperMapper                              2.5                                 SMAC3                HyperMapper
                        1.00                                Hyperopt                 OpenBox                                                          Hyperopt           OpenBox                                   3                                   Hyperopt             OpenBox                                                                      Hyperopt             OpenBox
                                                                                                                                                                                                                                                                                                                     2.0
                                                            GPflowOpt                                                                                 GPflowOpt                                                                                        GPflowOpt                                                                                         GPflowOpt
                        0.75                                                                                              10
                                                                                                                                                                                                                   2                                                                                                 1.5
                        0.50
                                                                                                                           5                                                                                                                                                                                         1.0
                        0.25                                                                                                                                                                                       1
                                                                                                                                                                                                                                                                                                                     0.5
                        0.00
                                                                                                                           0                                                                                       0
                                                                                                                                                                                                                                                                                                                     0.0
                    −0.25
                                 0   25   50         75     100    125         150      175        200                         0   25   50    75     100     125   150      175        200                             0    25         50     75      100      125   150         175      200                                0   25         50   75      100      125   150      175        200
                                                          Trials                                                                                    Trials                                                                                           Trials                                                                                            Trials

                                          (a) 2d-Branin                                                                                  (b) 2d-Ackley                                                                                      (c) 2d-Beale                                                                               (d) 6d-Hartmann
                                                                                              Figure 6: Results for four black-box problems with single objective.
                        25                                  Random                   CMA-ES                                                           Random             CMA-ES                                                                        Random               CMA-ES                                                                       Random               CMA-ES
                                                            2×Random                 BoTorch                                                          2×Random           BoTorch                                                                       2×Random             BoTorch                                                                      2×Random             BoTorch

       Optimality Gap                                                                                    Optimality Gap                                                                          Optimality Gap                                                                                 Optimality Gap
                        20                                  SMAC3                    HyperMapper                          30                          SMAC3              HyperMapper                               30                                  SMAC3                HyperMapper                          30                                      SMAC3                HyperMapper
                                                            Hyperopt                 OpenBox                                                          Hyperopt           OpenBox                                                                       Hyperopt             OpenBox                                                                      Hyperopt             OpenBox
                        15                                  GPflowOpt                                                                                 GPflowOpt
                                                                                                                          20                                                                                       20                                                                                            20

                        10

                                                                                                                          10                                                                                       10                                                                                            10
                         5

                         0                                                                                                 0                                                                                        0                                                                                                0
                             0       50        100         150          200          250           300                         0   50   100   150    200     250   300      350        400                              0        100           200            300          400            500                            0            100        200            300       400               500
                                                          Trials                                                                                    Trials                                                                                           Trials                                                                                            Trials

                                          (a) 4d-Ackley                                                                                  (b) 8d-Ackley                                                                                  (c) 16d-Ackley                                                                                      (d) 32d-Ackley
                                                                              Figure 7: Scalability results on solving Ackley with different input dimensions.
6.1                              Experimental Setup                                                                                                                                                                 6.1.4 Parameter Settings. For both OpenBox and the considered
6.1.1 Baselines. Besides the systems mentioned in Table 1, we also                                                                                                                                                  baselines, we use the default setting. Each experiment is repeated
use CMA-ES [23], Random Search and 2×Random Search (Ran-                                                                                                                                                            10 times, and we compute the mean and variance for visualization.
dom Search with double budgets) as baselines. To evaluate transfer
learning, we compare OpenBox with Google Vizier. For multi-                                                                                                                                                            6.2         Results and Analysis
fidelity experiments, we compare OpenBox against HpBandSter
and BOHB, the details of which are in Appendix A.5.                                                                                                                                                                 6.2.1 Single-Objective Problems without Constraints. Figure 6 illus-
                                                                                                                                                                                                                    trates the results of OpenBox on different single-objective problems
6.1.2 Problems. We use 12 black-box problems (mathematical func-                                                                                                                                                    compared with competitive baselines while Figure 7 displays the
tions) from [50] and two AutoML optimization problems on 25                                                                                                                                                         performance with the growth of input dimensions. In particular,
OpenML datasets. In particular, 2d-Branin, 2d-Beale, 6d-Hartmann                                                                                                                                                    Figure 6 shows that OpenBox, HyperMapper and BoTorch are
and (2d, 4d, 8d, 16d, 32d)-Ackley are used for single-objective opti-                                                                                                                                               capable of optimizing these low-dimensional functions stably. Howmization; 2d-Townsend, 2d-Mishra, 4d-Ackley and 10d-Keane are                                                                                                                                                       ever, when the dimensions of the parameter space grow larger, as
used for constrained single-objective optimization; 3d-ZDT2 with                                                                                                                                                    shown in Figure 7, only OpenBox achieves consistent and excellent
two objectives and 6d-DTLZ1 with five objectives are used for                                                                                                                                                       results while the other baselines fail, which demonstrates its scalamulti-objective optimization; 2d-CONSTR and 2d-SRN with two                                                                                                                                                         bility on input dimensions. Note that, OpenBox achieves more than
objectives are used for constrained multi-objective optimization.                                                                                                                                                   10-fold speedups over the baselines when solving Ackley with 16
All the parameters for mathematical problems are of the FLOAT type                                                                                                                                                  and 32-dimensional inputs.
and the maximum trials of each problem depend on its difficulty,
which ranges from 80 to 500. For AutoML problems on 25 datasets,                                                                                                                                                    6.2.2 Single-Objective Problems with Constraints. Figure 8 shows
we split each dataset and search for the configuration with the                                                                                                                                                     the results of OpenBox along with the baselines on four constrained
best validation performance. Specifically, we tune LightGBM and                                                                                                                                                     single-objective problems. Besides Random Search, we compare
LibSVM with the linear kernel, where the parameters of LightGBM                                                                                                                                                     OpenBox with three of the software packages that support conare of the FLOAT type while LibSVM contains CATEGORICAL and                                                                                                                                                         straints. OpenBox surpasses all the considered baselines on the conconditioned parameters.                                                                                                                                                                                             vergence result. Note that on the 10-dimensional Keane problem in
6.1.3 Metrics. We employ the three metrics as follows.                                                                                                                                                              which the ground-truth optimal value is hard to locate, OpenBox
1. Optimality gap is used for single-objective mathematical prob-                                                                                                                                                   is the only method that successfully optimizes this function while
lem. That is, if 𝑥 ∗ optimizes 𝑓 , and 𝑥ˆ is the best configuration found                                                                                                                                           the other methods fail to suggest sufficient feasible configurations.
by the method, then |𝑓 ( ˆ  𝑥) − 𝑓 (𝑥 ∗ )| measures the success of the
method on that function. In rare cases, we report the objective value                                                                                                                                                  6.2.3 Multi-Objective Problems without Constraints. We compare
if the ground-truth optimal 𝑥 ∗ is extremely hard to obtain.                                                                                                                                                           OpenBox with three baselines that support multiple objectives and
2. Hypervolume indicator given a reference point 𝒓 measures                                                                                                                                                            the results are depicted in Figure 9(a) and 9(b). In Figure 9(a), the hythe quality of a Pareto front in multi-objective problems. We report                                                                                                                                                   pervolume difference of GPflowOpt and Hypermapper decreases
the difference between the hypervolume of the ideal Pareto front                                                                                                                                                       slowly as the number of trials grow, while BoTorch and OpenBox
P ∗ and that of the estimated Pareto front P by a given algorithm,                                                                                                                                                     obtain a satisfactory Pareto Front quickly within 50 trials. In Figwhich is 𝐻𝑉 (P ∗, 𝒓) − 𝐻𝑉 (P, 𝒓).                                                                                                                                                                                      ure 9(b) where the number of objectives is 5, BoTorch meets the
3. Metric for AutoML. For single-objective AutoML problems, we                                                                                                                                                         bottleneck of optimizing the Pareto front while OpenBox tackles
report the validation error. To measure the results across different                                                                                                                                                   this problem easily by switching its inner algorithm from EHVI to
datasets, we use Rank as the metric.                                                                                                                                                                                   MESMO; GPflowOpt is missing due to runtime errors.
                                                                                                                                                                                             7

                                   1.2                                                                                           40                                                                                          6
                                                                                                                                                                                                                                                                                                                                                                              0.0
                                                               Random              GPflowOpt                                                                Random              GPflowOpt                                                                                                        Random                        GPflowOpt                                                                                                   Random                        BoTorch
                                   1.0                         2×Random            BoTorch                                                                  2×Random            BoTorch                                      5                                                                   2×Random                      BoTorch                                                                                                     2×Random                      OpenBox
                                                                                                                                                                                                                                                                                                                                                                         −0.1

                  Optimality Gap                                                                            Optimality Gap                                                                                  Optimality Gap                                                                                                                            Objective Value
                                                               HyperMapper         OpenBox                                       30                         HyperMapper         OpenBox                                                                                                          HyperMapper                   OpenBox                                                                                                     HyperMapper
                                   0.8                                                                                                                                                                                       4
                                                                                                                                                                                                                                                                                                                                                                         −0.2
                                   0.6                                                                                           20
                                                                                                                                                                                                                             3
                                                                                                                                                                                                                                                                                                                                                                         −0.3
                                   0.4
                                                                                                                                 10                                                                                          2
                                                                                                                                                                                                                                                                                                                                                                         −0.4
                                   0.2
                                                                                                                                                                                                                             1
                                                                                                                                                                                                                                                                                                                                                                         −0.5
                                   0.0                                                                                            0
                                                                                                                                                                                                                             0                                                                                                                                           −0.6
                                         0   10    20   30     40     50     60      70        80                                     0   25   50   75    100     125     150    175        200                                  0                                25         50          75          100         125         150          175                                       0                         100               200                300                 400              500
                                                             Trials                                                                                      Trials                                                                                                                               Trials                                                                                                                                  Trials

                                                  (a) 2d-Townsend                                                                               (b) 2d-Mishra                                                                                                                (c) 4d-Ackley                                                                                                                          (d) 10d-Keane
                                                                 Figure 8: Results for solving four single-objective black-box problems with constraints.
                                                                                                                                 12                                                                                          1.0
                                                               HyperMapper         BoTorch                                                                  HyperMapper         OpenBox                                                                                                          HyperMapper                   OpenBox                                       4.50                                                          HyperMapper                   OpenBox

            Hv Difference (log)                                                                            Hv Difference (log)                                                                        Hv Difference (log)                                                                                                                              Hv Difference (log)
                                   1.5                         GPflowOpt           OpenBox                                                                  BoTorch                                                          0.8                                                                 BoTorch                                                                                                                                   BoTorch
                                                                                                                                 11                                                                                                                                                                                                                                          4.25
                                   1.0                                                                                                                                                                                       0.6
                                                                                                                                                                                                                                                                                                                                                                             4.00
                                   0.5                                                                                           10
                                                                                                                                                                                                                             0.4                                                                                                                                             3.75
                                   0.0
                                                                                                                                  9                                                                                          0.2                                                                                                                                             3.50
                                  −0.5
                                                                                                                                                                                                                             0.0                                                                                                                                             3.25
                                                                                                                                  8
                                  −1.0
                                                                                                                                                                                                                                                                                                                                                                             3.00
                                                                                                                                                                                                                            −0.2
                                  −1.5                                                                                            7
                                         0   25    50   75     100    125    150    175        200                                    0   25   50   75    100     125     150    175        200                                      0                             25        50         75      100        125         150         175         200                                  0                    25         50         75          100        125        150         175        200
                                                             Trials                                                                                      Trials                                                                                                                               Trials                                                                                                                                  Trials

                                                    (a) 3d-ZDT2                                                                                 (b) 6d-DTLZ1                                                                                                                 (c) 2d-CONSTR                                                                                                                               (d) 2d-SRN
                                                         Figure 9: Results on multi-objective problems without (a and b) and with (c and d) constraints.
        7                                                                                                                                                                                                                                                       0.0140                                                                                                                             2.3                                 OpenBox                   SMAC3                  VIZIER
                                                                                                                                                                                                                                                                                                                   Seq-1                  Async-2

                                                                                                                                                                                                                                     Average Validation Error
                                                                                                       5                                                                                                                                                                                                           Sync-2                 Async-4
        6                                                                                                                                                                                                                                                                                                                                                                                          2.2
                                                                                                                                                                                                                                                                0.0135                                             Sync-4                 Async-8
        5                                                                                              4

                                                                                                                                                                                                                                                                                                                                                                                    Average Rank
                                                                                                                                                                                                                                                                                                                   Sync-8                 Random-8                                                 2.1

 Rank                                                                                           Rank
                                                                                                                                                                                                                                                                0.0130
        4                                                                                                                                                                                                                                                                                                                                                                                          2.0
                                                                                                       3
        3                                                                                                                                                                                                                                                       0.0125                                                                                                                             1.9
                                                                                                       2
        2                                                                                                                                                                                                                                                                                                                                                                                          1.8
                                                                                                                                                                                                                                                                0.0120
        1                                                                                              1                                                                                                                                                                                                                                                                                           1.7
                                                        r                                                                                         r                                                                                                             0.0115
                   Tor
                       ch        Op
                                    t
                                        min
                                            t
                                                     ppe MAC
                                                             3        opt        ox
                                                                                                              Tor
                                                                                                                  ch        Op
                                                                                                                              t
                                                                                                                                   min
                                                                                                                                      t
                                                                                                                                               ppe MAC
                                                                                                                                                       3        opt        ox
                Bo           flow Spear           Ma              per        enB                           Bo           flow Spear          Ma              p er       enB                                                                                                                                                                                                                         1.6
                          GP                  per
                                                        S      Hy         Op                                         GP                 per
                                                                                                                                                  S      Hy         Op
                                          Hy                                                                                         Hy                                                                                                                                  0        100          200         300           400             500         600                                                  0    5    10    15   20     25   30    35   40    45   50     55   60    65   70    75
                                                                                                                                                                                                                                                                                               Wall Clock Time (s)                                                                                                                               Trials

 (a) AutoML Benchmark on LightGBM                                                                    (b) AutoML Benchmark on LibSVM                                                                                                      (a) Parallel Experiments on Optdigits                                                                                                                                     (b) Transfer Learning
Figure 10: Performance rank on 25 datasets (the lower is the                                                                                                                                                                     Figure 11: Average validation error under two parallel setbetter). The box extends from the lower to upper quartile                                                                                                                                                                        tings (left figure) and average rank of tuning LightGBM with
values, with a line at the median. The whiskers extend from                                                                                                                                                                      transfer learning (right figure). “Seq”, “Sync” and “Async” rethe box to show the range of the data.                                                                                                                                                                                           fer to the sequential, sync and async mode respectively. The
6.2.4 Multi-Objective Problems with Constraints. We compare Open-                                                                                                                                                                number of parallel workers is given after ‘-’.
Box with Hypermapper and BoTorch on constrained multi-objective                                                                                                                                                                  although the synchronous mode brings a certain improvement over
problems (See Figure 9(c) and 9(d)). Figure 9(c) demonstrates the                                                                                                                                                                the sequential mode in the beginning, the convergence result is
performance on a simple problem, in which the convergence result                                                                                                                                                                 usually worse than the asynchronous mode due to stragglers.
of OpenBox is slightly better than the other two baselines. However, in Figure 9(d) where the constraints are strict, BoTorch and
                                                                                                                                                                                                                             6.3.3 Transfer Learning Experiment. In this experiment, we remove
Hypermapper fail to suggest sufficient feasible configurations to up-
                                                                                                                                                                                                                             all baselines except Vizier, which provides the transfer learning
date the Pareto Front. Compared with BoTorch and Hypermapper,
                                                                                                                                                                                                                             functionality for the traditional black-box optimization. We also
OpenBox has more stable performance when solving multi-objective
                                                                                                                                                                                                                             add SMAC3 that provides a non-transfer reference. In addition, this
problems with constraints.
                                                                                                                                                                                                                             experiment involves tuning LightGBM on 25 OpenML datasets,
                                                                                                                                                                                                                             and it is performed in a leave-one-out fashion, i.e, we tune the
6.3                                      Results on AutoML Tuning Tasks                                                                                                                                                      hyperparameters of LightGBM on a dataset (target problem), while
6.3.1 AutoML Tuning on 25 OpenML datasets. Figure 11 demon-                                                                                                                                                                  taking the tuning history on the remaining datasets as prior obstrates the universality and stability of OpenBox in 25 AutoML                                                                                                                                                               servations. Figure 11(b) shows the average rank for each baseline.
tuning tasks. We compare OpenBox with SMAC3 and Hyperopt                                                                                                                                                                     We observe that 1) Vizier and OpenBox show improved sample
on LibSVM since only these two baselines support CATEGORICAL                                                                                                                                                                 efficiency relative to SMAC3 that cannot use prior knowledge from
parameters with conditions. In general, OpenBox is capable of han-                                                                                                                                                           source problems, and 2) the proposed transfer learning framework
dling different types of input parameters while achieving the best                                                                                                                                                           in OpenBox performs better than the transfer learning algorithm
median performance among the baselines considered.                                                                                                                                                                           used in Vizier. Furthermore, it is worth mentioning that Open-
                                                                                                                                                                                                                             Box also supports transfer learning for the generalized black-box
6.3.2 Parallel Experiments. To evaluate OpenBox with parallel                                                                                                                                                                optimization, while Vizier does not.
settings, we conduct an experiment to tune the hyper-parameters
of LightGBM on Optdigits with a budget of 600 seconds. Figure
11(a) shows the average validation error with different parallel                                                                                                                                                                 7                                  CONCLUSION
settings. We observe that the asynchronous mode with 8 workers                                                                                                                                                                   In this paper, we have introduced a service that aims for solving
achieves the best results and outperforms Random Search with                                                                                                                                                                     generalized BBO problems – OpenBox, which is open-sourced and
8 workers by a wide margin. It brings a speedup of 8× over the                                                                                                                                                                   highly efficient. We have presented new principles from a service
sequential mode, which is close to the ideal speedup. In addition,                                                                                                                                                               perspective that drive the system design, and we have proposed
                                                                                                                                                                                                  8

efficient frameworks for accelerating BBO tasks by leveraging local-                               [17] Eduardo C Garrido-Merchán and Daniel Hernández-Lobato. 2019. Predictive
penalization based parallelization and transfer learning. OpenBox                                       entropy search for multi-objective bayesian optimization with constraints. Neu-
                                                                                                        rocomputing 361 (2019), 50–68.
hosts lots of state-of-the-art optimization algorithms with consis-                                [18] Michael Adam Gelbart. 2015. Constrained Bayesian Optimizationand Applications.
tent performance, via adaptive algorithm selection. It also offers                                      Ph.D. Dissertation. Harvard University, Graduate School of Arts & Sciences.
                                                                                                   [19] Daniel Golovin, Benjamin Solnik, Subhodeep Moitra, Greg Kochanski, John
a set of advanced features, such as performance-resource extrapo-                                       Karro, and D Sculley. 2017. Google vizier: A service for black-box optimization.
lation, multi-fidelity optimization, automatic early stopping, and                                      In Proceedings of the 23rd ACM SIGKDD. ACM, 1487–1495.
data privacy protection. Our experimental evaluations have also                                    [20] Javier González, Zhenwen Dai, Philipp Hennig, and Neil Lawrence. 2016. Batch
                                                                                                        bayesian optimization via local penalization. In AISTATS 2016. arXiv:1505.08052
showcased the performance and efficiency of OpenBox on a wide                                      [21] Robert B Gramacy, Genetha A Gray, Sébastien Le Digabel, Herbert KH Lee,
range of BBO tasks.                                                                                     Pritam Ranjan, Garth Wells, and Stefan M Wild. 2016. Modeling an augmented
                                                                                                        Lagrangian for blackbox constrained optimization. Technometrics 58, 1 (2016),
                                                                                                        1–11.
                                                                                                   [22] Ryan-Rhys Griffiths and José Miguel Hernández-Lobato. 2020. Constrained
ACKNOWLEDGMENTS                                                                                         Bayesian optimization for automatic chemical design using variational autoen-
                                                                                                        coders. Chem. Sci. 11 (2020).
This work is supported by the National Key Research and Develop-                                   [23] N. Hansen and A. Ostermeier. 2001. Completely derandomized self-adaptation
ment Program of China (No.2018YFB1004403), NSFC (No.61832001,                                           in evolution strategies.
U1936104), Beijing Academy of Artificial Intelligence (BAAI), and                                  [24] José Miguel Hernández-Lobato, Michael Gelbart, Matthew Hoffman, Ryan Adams,
                                                                                                        and Zoubin Ghahramani. 2015. Predictive entropy search for bayesian optimiza-
Kuaishou-PKU joint program. Bin Cui is the corresponding author.                                        tion with unknown constraints. In International conference on machine learning.
                                                                                                        PMLR, 1699–1707.
                                                                                                   [25] José Miguel Hernández-Lobato, Michael A. Gelbart, Ryan P. Adams, Matthew W.
                                                                                                        Hoffman, and Zoubin Ghahramani. 2016. A General Framework for Constrained
REFERENCES                                                                                              Bayesian Optimization using Information-based Search. Journal of Machine
 [1] Leonel Aguilar Melgar, David Dao, Shaoduo Gan, Nezihe M. Gürel, Nora Hol-                          Learning Research 17, 160 (2016), 1–53.
     lenstein, Jiawei Jiang, Bojan Karlaš, Thomas Lemmin, Tian Li, Yang Li, Susie                  [26] Yu-Chi Ho and David L Pepyne. 2001. Simple explanation of the no free lunch
     Rao, Johannes Rausch, Cedric Renggli, Luka Rimanic, Maurice Weber, Shuai                           theorem of optimization. In Proceedings of the 40th IEEE Conference on Decision
     Zhang, Zhikuan Zhao, Kevin Schawinski, Wentao Wu, and Ce Zhang. 2021. In                           and Control (Cat. No. 01CH37228), Vol. 5. IEEE, 4409–4414.
     Proceedings of the Annual Conference on Innovative Data Systems Research (CIDR),              [27] Frank Hutter, Holger H Hoos, and Kevin Leyton-Brown. 2011. Sequential model-
     2021. CIDR.                                                                                        based optimization for general algorithm configuration. In International Confer-
 [2] Omid Azizi, Aqeel Mahesri, Benjamin C. Lee, Sanjay J. Patel, and Mark Horowitz.                    ence on Learning and Intelligent Optimization. Springer, 507–523.
     2010. Energy-Performance Tradeoffs in Processor Architecture and Circuit                      [28] Aaron Klein, Stefan Falkner, Jost Tobias Springenberg, and Frank Hutter. 2017.
     Design: A Marginal Cost Analysis. In Proceedings of the 37th Annual International                  Learning Curve Prediction With Bayesian Neural Networks. ICLR (2017).
     Symposium on Computer Architecture. Association for Computing Machinery,                      [29] Joshua Knowles. 2006. ParEGO: A hybrid algorithm with on-line landscape
     New York, NY, USA.                                                                                 approximation for expensive multiobjective optimization problems. IEEE Trans-
 [3] Maximilian Balandat, Brian Karrer, Daniel R. Jiang, Samuel Daulton, Benjamin                       actions on Evolutionary Computation (2006).
     Letham, Andrew Gordon Wilson, and Eytan Bakshy. 2020. BoTorch: A Framework                    [30] Nicolas Knudde, Joachim van der Herten, Tom Dhaene, and Ivo Couckuyt. 2017.
     for Efficient Monte-Carlo Bayesian Optimization. In NeurIPS.                                       GPflowOpt: A Bayesian Optimization Library using TensorFlow. arXiv preprint –
 [4] Syrine Belakaria, Aryan Deshwal, and Janardhan Rao Doppa. 2019. Max-value                          arXiv:1711.03845 (2017).
     entropy search for multi-objective Bayesian optimization. In NeurIPS.                         [31] Lisha Li, Kevin Jamieson, Giulia DeSalvo, Afshin Rostamizadeh, and Ameet
 [5] Syrine Belakaria, Aryan Deshwal, Nitthilan Kannappan Jayakodi, and Janard-                         Talwalkar. 2018. Hyperband: A novel bandit-based approach to hyperparameter
     han Rao Doppa. 2020. Uncertainty-aware search framework for multi-objective                        optimization. Proceedings of the ICLR (2018), 1–48.
     Bayesian optimization. In AAAI, Vol. 34. 10044–10052.                                         [32] Yang Li, Jiawei Jiang, Jinyang Gao, Yingxia Shao, Ce Zhang, and Bin Cui. 2020.
 [6] James S Bergstra, Rémi Bardenet, Yoshua Bengio, and Balázs Kégl. 2011. Al-                         Efficient Automatic CASH via Rising Bandits. In AAAI, Vol. 34. 4763–4771.
     gorithms for hyper-parameter optimization. In Advances in neural information                  [33] Yang Li, Yu Shen, Jiawei Jiang, Jinyang Gao, Ce Zhang, and Bin Cui. 2021. MFES-
     processing systems. 2546–2554.                                                                     HB: Efficient Hyperband with Multi-Fidelity Quality Measurements. Proceedings
 [7] Ivo Couckuyt, Dirk Deschrijver, and Tom Dhaene. 2014. Fast Calculation of                          of the AAAI Conference on Artificial Intelligence 35, 10 (May 2021), 8491–8500.
     Multiobjective Probability of Improvement and Expected Improvement Criteria                   [34] Yang Li, Yu Shen, Wentao Zhang, Jiawei Jiang, Bolin Ding, Yaliang Li, Jingren
     for Pareto Optimization. J. of Global Optimization 60, 3 (2014), 575–594.                          Zhou, Zhi Yang, Wentao Wu, Ce Zhang, and Bin Cui. 2021. VolcanoML: Speeding
 [8] Samuel Daulton, Maximilian Balandat, and Eytan Bakshy. 2020. Differentiable                        up End-to-End AutoML via Scalable Search Space Decomposition. Proc. VLDB
     Expected Hypervolume Improvement for Parallel Multi-Objective Bayesian Opti-                       Endow. 14 (2021), 2167–2176.
     mization. arXiv preprint arXiv:2006.05078 (2020).                                             [35] Edo Liberty, Zohar Karnin, Bing Xiang, Laurence Rouesnel, Baris Coskun, Ramesh
 [9] Tobias Domhan, Jost Tobias Springenberg, and Frank Hutter. 2015. Speeding up                       Nallapati, Julio Delgado, Amir Sadoughi, Yury Astashonok, Piali Das, et al. 2020.
     automatic hyperparameter optimization of deep neural networks by extrapolation                     Elastic Machine Learning Algorithms in Amazon SageMaker. In Proceedings of the
     of learning curves. In IJCAI International Joint Conference on Artificial Intelligence.            2020 ACM SIGMOD International Conference on Management of Data. 731–737.
[10] Katharina Eggensperger, Frank Hutter, Holger H Hoos, and Kevin Leyton-Brown.                  [36] Microsoft. 2020. Smart buildings: From design to reality. https://azure.microsoft.
     2015. Efficient Benchmarking of Hyperparameter Optimizers via Surrogates.. In                      com/en-us/resources/smart-buildings-from-design-to-reality/.
     AAAI. 1114–1120.                                                                              [37] J Močkus. 1975. On Bayesian methods for seeking the extremum. In Optimization
[11] M. T. M. Emmerich, K. C. Giannakoglou, and B. Naujoks. 2006. Single- and                           Techniques IFIP Technical Conference. Springer, 400–404.
     multiobjective evolutionary optimization assisted by Gaussian random field                    [38] Biswajit Paria, Kirthevasan Kandasamy, and Barnabás Póczos. 2020. A flexible
     metamodels. IEEE Transactions on Evolutionary Computation (2006).                                  framework for multi-objective Bayesian optimization using random scalariza-
[12] David Eriksson and Matthias Poloczek. 2021. Scalable constrained bayesian                          tions. In Uncertainty in Artificial Intelligence. PMLR, 766–776.
     optimization. In International Conference on Artificial Intelligence and Statistics.          [39] Valerio Perrone, Michele Donini, Muhammad Bilal Zafar, Robin Schmucker, Kr-
     PMLR, 730–738.                                                                                     ishnaram Kenthapadi, and Cédric Archambeau. 2020. Fair bayesian optimization.
[13] Stefan Falkner, Aaron Klein, and Frank Hutter. 2018. BOHB: Robust and efficient                    arXiv preprint arXiv:2006.05109 (2020).
     hyperparameter optimization at scale. arXiv preprint arXiv:1807.01774 (2018).                 [40] Victor Picheny, Robert B Gramacy, Stefan M Wild, and Sebastien Le Digabel. 2016.
[14] Matthias Feurer, Benjamin Letham, and Eytan Bakshy. 2018. Scalable meta-                           Bayesian optimization under mixed constraints with a slack-variable augmented
     learning for bayesian optimization using ranking-weighted gaussian process                         Lagrangian. arXiv preprint arXiv:1605.09466 (2016).
     ensembles. In AutoML Workshop at ICML.                                                        [41] Matthias Poloczek, Jialei Wang, and Peter Frazier. 2017. Multi-information source
[15] Adam Foster, Martin Jankowiak, Eli Bingham, Paul Horsfall, Yee Whye Teh, Tom                       optimization. In Advances in Neural Information Processing Systems. 4288–4298.
     Rainforth, and Noah Goodman. 2019. Variational bayesian optimal experimental                  [42] L. M. Rios and N. Sahinidis. 2013. Derivative-free optimization: a review of
     design. arXiv preprint arXiv:1903.05480 (2019).                                                    algorithms and comparison of software implementations. Journal of Global
[16] Jacob R. Gardner, Matt J. Kusner, Zhixiang Xu, Kilian Q. Weinberger, and John P.                   Optimization 56 (2013), 1247–1293.
     Cunningham. 2014. Bayesian Optimization with Inequality Constraints. In Pro-                  [43] Bobak Shahriari, Kevin Swersky, Ziyu Wang, Ryan P. Adams, and Nando De Fre-
     ceedings of the 31st International Conference on International Conference on Ma-                   itas. 2016. Taking the human out of the loop: A review of Bayesian optimization.
     chine Learning - Volume 32 (ICML’14). JMLR.org.
                                                                                               9

[44] Jasper Snoek, Hugo Larochelle, and Ryan P Adams. 2012. Practical bayesian
     optimization of machine learning algorithms. In NIPS. 2951–2959.
[45] N. Srinivas, Andreas Krause, Sham M. Kakade, and Matthias W. Seeger. 2010.
     Gaussian Process Optimization in the Bandit Setting: No Regret and Experimental
     Design. In ICML.
[46] Dana Van Aken, Andrew Pavlo, Geoffrey J Gordon, and Bohan Zhang. 2017.
     Automatic database management system tuning through large-scale machine
     learning. In Proceedings of the 2017 SIGMOD. 1009–1024.
[47] Kaifeng Yang, Michael Emmerich, André Deutz, and Thomas Bäck. 2019. Multi-
     Objective Bayesian Global Optimization using expected hypervolume improve-
     ment gradient. Swarm and evolutionary computation 44 (2019), 945–956.
[48] Ji Zhang, Yu Liu, Ke Zhou, Guoliang Li, Zhili Xiao, Bin Cheng, Jiashu Xing,
     Yangtao Wang, Tianheng Cheng, Li Liu, et al. 2019. An end-to-end automatic
     cloud database tuning system using deep reinforcement learning. In Proceedings
     of the 2019 International Conference on Management of Data. 415–432.
[49] Xinyi Zhang, Zhuo Chang, Yang Li, Hong Wu, Jian Tan, Feifei Li, and Bin Cui.
     2021. Facilitating Database Tuning with Hyper-Parameter Optimization: A
     Comprehensive Experimental Evaluation. ArXiv abs/2110.12654 (2021).
[50] Eckart Zitzler, Kalyanmoy Deb, and Lothar Thiele. 2000. Comparison of Multiob-
     jective Evolutionary Algorithms: Empirical Results. Evolutionary Computation 8,
     2 (2000), 173–195.

                                                                                       10

A APPENDIX                                                                         to probabilistic random forest proposed in [27], which incurs less
                                                                                   complexity.
A.1 Performance-Resource Extrapolation
                                                                                      Acquisition Functions. By default, OpenBox uses Expected Im-
While optimizing various black-box problems, we observe that the
                                                                                   provement (EI) [37] for single-objective optimization, Expected
optimization curve (performance vs. trials) is often saturating, i.e.,
                                                                                   Hypervolume Improvement (EHVI) [11] for multi-objective optiafter a certain number of trials, more evaluations will not cause a
                                                                                   mization, and Probability of Feasibility (PoF) [16] for constraints.
meaningful improvement 𝛿 > 0 in performance. OpenBox applies
                                                                                   OpenBox computes these acquisition functions analytically [47] (by
a combined learning curve extrapolation method inspired by [9],
                                                                                   default) or through Monte Carlo integration [8]. In addition, Openwhich early stops the training procedure of neural networks when
                                                                                   Box includes multiple acquisition functions to meet the needs of difthe performance of the network becomes less likely to improve.
                                                                                   ferent problem settings. For single-objective optimization, Expected
   We measure the performance by negative hypervolume indica-
                                                                                   Improvement per second (EIPS) [44] can be used to find a good contor (HV) of the Pareto set P bounded above by reference point 𝑟 ,
                                                                                   figuration as quickly as possible, and Expected Improvement with
denoted by 𝐻𝑉 (P, 𝑟 ). In single-objective case, P = {𝑦best }. Note
                                                                                   Local Penalization (LP-EI) [20] utilizes local penalizers to propose
that in both cases, the performance is decreasing.
                                                                                   batches of configurations simultaneously. For multi-objective opti-
   Denote the performance at timestep 𝑡 by 𝑧𝑡 . Given observed
                                                                                   mization, Max-value Entropy Search for Multi-objective Optimizadata 𝑧 1:𝑛 := {𝑧 1, . . . , 𝑧𝑛 }, a natural idea is to estimate whether the
                                                                                   tion (MESMO) [4] and Uncertainty-aware Search framework [5]
performance at a future timestep 𝑡 > 𝑛 will exceed the current best
                                                                                   for Multi-objective Optimization (USeMO) work efficiently when
performance 𝑧𝑛 . We extrapolate the performance curve 𝑧𝑡 with a
                                                                                   the number of objectives is large. Other implemented acquisition
weighted probabilistic model
                                                                                   functions include Probability of Improvement (PI), and Upper Con-
                                      𝐾
                                     ∑︁                                            fidence Bound (UCB) [45].
                    𝑔comb (𝑡 |𝚯) =         𝑤𝑘 𝑔𝑘 (𝑡 |𝜽 𝒌 ) + 𝜀,                       Acquisition Function Optimizers. To support generic surrogate
                                     𝑘=1                                           models that are not differentiable, we maximize the acquisition
where each of 𝑔1, . . . , 𝑔𝐾 is a parametric family of decreasing satu-            function via the following two methods: 1) interleaved local and
rating functions, and 𝜀 ∼ N (0, 𝜎 2 ). We estimate 𝚯 = (𝑤 1, . . . , 𝑤 𝐾 ,         random search (gradient-free) which can handle categorical param-
𝜃 1, . . . , 𝜃 𝐾 , 𝜎 2 ) using Markov Chain Monte Carlo (MCMC) inference.          eters, and 2) multi-start staged optimizer of random search and
The prior and posterior distribution over 𝚯 are as follows                         L-BFGS-B from Scipy (estimate gradient by 2-point finite differ-
             𝐾                                                                    ence) which can locate the global optimum in high dimensional
                   𝑝 (𝑤𝑘 )𝑝 (𝜽 𝒌 ) 𝑝 (𝜎 2 ) 1 (𝑔comb (1|𝚯) > 𝑔comb (𝑡 |𝚯)),
             Ö                    
  𝑝 (𝚯) ∝                                                                          design space efficiently.
             𝑘=1
                                                                                   A.3    Transfer Learning Details
                       𝑃 (𝚯|𝑧 1:𝑛 ) ∝ 𝑃 (𝑧 1:𝑛 |𝚯)𝑃 (𝚯),
                                                                                   In OpenBox, we expand RGPE [14], a state-of-the-art transfer learnwhere 𝑡 > 𝑛.                                                                       ing method on single-objective problems, into generalized settings.
  We sample 𝚯 from the posterior and compute 𝑃 (𝑧𝑡 < 𝑧𝑛 −𝛿 |𝑧 1:𝑛 ),                                                                      𝑖
                                                                                      First, for each prior task 𝑖, we train surrogates 𝑀1:𝑚 for 𝑚 objecwhich is the probability that the optimization procedure yields a                  tives on the corresponding observations from 𝐷 𝑖 . Then we build
meaningful improvement 𝛿 at timestep 𝑡.                                            surrogates 𝑀1:𝑚TL to guide the optimization instead of using the orig-
                                                                                                      𝑇 fitted on 𝐷𝑇 only. For ease of description, we
                                                                                   inal surrogates 𝑀1:𝑚
A.2     Bayesian Optimization Algorithms                                           assume there is only one surrogate 𝑀 TL since the method of build-
The BO algorithms in OpenBox include three parts: surrogate mod-                   ing surrogate for each objective is exactly the same. The prediction
els, acquisition functions, and acquisition function optimizers. Note              of 𝑀 TL at point 𝒙 is given by 𝑦 ∼ N ( 𝑖 𝜇 TL (𝒙), 𝜎TL2 (𝒙)), where
                                                                                                                            Í
that, partial implementations for single-objective BBO without
                                                                                                                ∑︁
constraints, including probabilistic random forest surrogate, ei op-                             𝜇 TL (𝒙) = (        𝜇𝑖 (𝒙)w𝑖 𝜎𝑖−2 (𝒙))𝜎TL
                                                                                                                                        2
                                                                                                                                           (𝒙),
timization, are inheriting from the SMAC32 package directly.                                                    𝑖
   Surrogate Models. OpenBox selects different surrogate models
                                                                                                                ∑︁
                                                                                                  2
                                                                                                 𝜎TL (𝒙) = (         w𝑖 𝜎𝑖−2 (𝒙)) −1,
based on the number of trials. For tasks with under 500 trials, Open-                                           𝑖
Box defaults to using Gaussian Process (GP) from scikit-optimize
                                                                                   where w𝑖 is the weight of base surrogate 𝑀 𝑖 , and 𝜇𝑖 and 𝜎𝑖2 are the
package. We use a Matérn kernel with automatic relevance determination (ARD) for continuous parameters and a Hamming kernel                      predictive mean and variance from base surrogate 𝑀 𝑖 . The weight
for categorical parameters. When both continuous and categorical                   w𝑖 reflects the similarity between the previous task and current
parameters exist, we use the product of these two kernels. The pa-                 task. Therefore, 𝑀 TL carries the knowledge of the prior tasks, which
rameters of GP are fitted by optimizing the marginal log-likelihood                could greatly accelerate the convergence of the optimization on the
with the gradient-based method (as default) or MCMC sampling.                      current task. We then use the following ranking loss function 𝐿, i.e.,
Due to the high computational complexity O (𝑛 3 ), GP cannot scale                 the number of misranked pairs, to measure the similarity between
well to the setting with too many trials (a large 𝑛). Therefore, for               previous tasks and current task:
tasks with more than 500 trials, the surrogate model is switched                                          𝑛𝑇 ∑︁
                                                                                                             𝑛𝑇
                                                                                                                    1 ((𝑀 𝑖 (𝒙 𝑗 ) < (𝒙𝑘 ) ⊕ (𝑦 𝑗 < 𝑦𝑘 )),
                                                                                                          ∑︁
                                                                                         𝐿(𝑀 𝑖 , 𝐷𝑇 ) =                                                      (1)
2 https://github.com/automl/SMAC3
                                                                                                          𝑗=1 𝑘=1
                                                                              11

where ⊕ is the exclusive-or operator, 𝑛𝑇 = |𝐷𝑇 |, 𝒙 𝑗 and 𝑦 𝑗 are the                                                  0.114                                                                                                                              0.174

                                                                                            Average Validation Error                                                                                                           Average Validation Error
                                                                                                                                                                                    SMAC3              OpenBox                                                                                   SMAC3            OpenBox
                                                                                                                       0.113                                                                                                                              0.173
                                                                                                                                                                                    Hyperopt                                                                                                     Hyperopt
sampled point and its performance in 𝐷𝑇 , and 𝑀 𝑖 (𝒙 𝑗 ) means the                                                     0.112                                                                                                                              0.172

                                                                                                                       0.111                                                                                                                              0.171
prediction of 𝑀 𝑖 on the point 𝒙 𝑗 . Based on the ranking loss function,                                               0.110
                                                                                                                                                                                                                                                          0.170

                                                                                                                                                                                                                                                          0.169
the weight w𝑖 is set to the probability that 𝑀 𝑖 has the smallest                                                      0.109

                                                                                                                       0.108
                                                                                                                                                                                                                                                          0.168

ranking loss on 𝐷𝑇 , that is, w𝑖 = 𝑃 (𝑖 = argmin 𝑗 𝐿(𝑀 𝑗 , 𝐷𝑇 )). This                                                 0.107
                                                                                                                                                                                                                                                          0.167

                                                                                                                                                                                                                                                          0.166
                                                                                                                               0   25   50                            75      100       125      150     175     200                                              0    25    50     75     100       125    150    175      200
probability can be estimated using the MCMC sampling.                                                                                                                       Trials                                                                                                       Trials

                                                                                                                               (a) LightGBM on Puma32H                                                                                                            (b) LightGBM on Puma8NH
A.4     Discussions about Local Penalization based                                                                0.480                                                                                                                           0.142

                                                                                       Average Validation Error                                                                                                        Average Validation Error
                                                                                                                                                                                    SMAC3              OpenBox                                                                                  SMAC3             OpenBox
        Parallelization                                                                                           0.478                                                             Hyperopt
                                                                                                                                                                                                                                                  0.141
                                                                                                                                                                                                                                                                                                Hyperopt

                                                                                                                  0.476

Algorithm 1 parallelizes BO algorithms by imputing the config-                                                    0.474                                                                                                                           0.140

urations being evaluated with the median of the evaluated data                                                    0.472

                                                                                                                  0.470
                                                                                                                                                                                                                                                  0.139

𝐷𝑛 = {𝒙 𝒊 , 𝒚 𝒊 }𝑛𝑖=1 . For notational simplicity, we discuss the single-                                         0.468
                                                                                                                                                                                                                                                  0.138

objective case with EI as acquisition function. Denote the median                                                 0.466
                                                                                                                           0       25   50                           75      100
                                                                                                                                                                            Trials
                                                                                                                                                                                       125       150    175      200
                                                                                                                                                                                                                                                  0.137
                                                                                                                                                                                                                                                              0       25    50     75     100
                                                                                                                                                                                                                                                                                         Trials
                                                                                                                                                                                                                                                                                                    125     150    175      200

of observed values {𝑦𝑖 }𝑛𝑖=1 by ˆ   𝑦, and the smallest observed value                                                             (c) LibSVM on Pollen                                                                                                               (d) LibSVM on Wind
by 𝜂. Define 𝑢 = 𝑓 (𝒙), 𝑢 ∼ N (𝜇𝑛 (𝒙), 𝜎𝑛2 (𝒙)), where 𝜇𝑛 (𝒙) and
                                                                                     Figure 12: Performance of two AutoML tasks on 4 datasets.
𝜎𝑛2 (𝒙) are the mean and variance of the posterior distribution of
the surrogate model trained on 𝐷𝑛 . The expected improvement is                      obtains a 3.8 × speedup over SMAC3 when achieving the comparable
                                                                                     convergence performance.
             𝛼 EI (𝒙; 𝐷𝑛 ) = E𝑢 [(𝜂 − 𝑢) 1 (𝑢 < 𝜂)]                                                                                                                 0.060
                                                                         (2)                                                                                                                                                 HpBandSter                                           SMAC3

                                                                                                                                         Average Validation Error
                          = (𝜂 − 𝜇𝑛 (𝒙))Φ(𝑧) + 𝜎𝑛 (𝒙)𝜙 (𝑧)                                                                                                          0.055                                                    BOHB                                                 OpenBox

                                                                                                                                                                    0.050
when 𝜎𝑛 > 0 and vanishes otherwise. Here, Φ and 𝜙 are the CDF                                                                                                       0.045
                                                          𝜂−𝜇 (𝒙)
and PDF of the standard normal distribution, 𝑧 = 𝜎 𝑛(𝒙) .                                                                                                           0.040
                                                            𝑛
   We first show that, with our imputation strategy, 𝛼 EI (𝒙; 𝐷 aug )                                                                                               0.035

will be sufficiently small if 𝒙 is close to some 𝒙 eval ∈ 𝐷 aug , i.e.,                                                                                             0.030

locally penalized near 𝒙 eval . For all probabilistic surrogate models,                                                                                                             2700       5400    8100 10800 13500 16200 18900 21600 24300 27000
                                                                                                                                                                                                         Wall Clock Time (s)
𝜇𝑛 (𝒙) = 𝑓 (𝒙), 𝜎𝑛 (𝒙) = 0 if 𝒙 ∈ 𝐷𝑛 , which means 𝛼 EI (𝒙) = 0, ∀𝒙 ∈
                                                                                     Figure 13: Multi-fidelity experiment on tuning hyper-
𝐷𝑛 . By augmenting 𝐷𝑛 with 𝐷 new = {(𝒙 eval, ˆ       𝑦) : 𝒙 eval ∈ 𝐶 eval },
                                                                                     parameters of LightGBM.
we have 𝛼 EI (𝒙 eval ) = 0, ∀𝒙 eval ∈ 𝐶 eval . Since 𝛼 𝐸𝐼 (𝒙; 𝐷 aug ) is continuous if the surrogate is GP and flat if the surrogate is random
forest, when 𝒙 is close to some 𝒙 eval ∈ 𝐶 eval , 𝜂 − 𝜇𝑛 (𝒙) ≈ 𝜂 − 𝑦ˆ and
𝑧 = (𝜂 − 𝜇𝑛 (𝒙))/𝜎𝑛 (𝒙) are negative and sufficiently small. Hence,
                                                                                     A.6                                           Reproduction Instructions
both terms in (2) are small and 𝒙 is unlikely to be the maximum of                   We run our experiments on 2 machines with 56 Intel(R) Xeon(R)
𝛼 EI . This conclusion can be naturally extended to cases with multi-                CPU E5-2680 v4 @ 2.40GHz. The versions of baselines are 1) BoTorch
ple objectives, and more generally, other acquisition functions.                     0.3.3, 2) GPflowOpt 0.1.1, 3) HyperMapper master branch 3 , 4)
   Moreover, although Algorithm 1 changes the posterior distribu-                    SMAC3 0.8.0, 5) Hyperopt 0.2.3 and 6) Spearmint master branch 4 .
tion of the surrogate by imposing a local penalty, it helps avoid                    The source code of OpenBox is written in Python 3.7 and is already
over-exploitation. Considering the configurations evaluated at the                   available in Github 5 . We place the code for reproduction under
same time as a "batch", Algorithm 1 simplified the complex joint op-                 the directory test/reproduction. For example, to run single-objective
timization problem by assigning a different region for each worker                   experiment on Branin, the script is as follows:
to explore. From the experiment results shown in Figure 11(a), we                    python test/reproduction/so/benchmark_xxx.py
observe that Algorithm 1 is a highly efficient, as well as widely                    –problem branin –n 200.
applicable parallelization heuristic.

A.5     More Experimental Results
   AutoML Performance. Besides the rank of convergence results
shown in Figure 11, we present Figure 12 that demonstrates the optimization process of OpenBox on AutoML tasks. OpenBox achieves
2.0-3.3× speedups over the best baseline in each task.
   Muiti-fidelity Acceleration. Figure 13 shows the acceleration of
OpenBox using multi-fidelity optimization compared with SMAC3
and two other multi-fidelity packages, HpBandSter and BOHB. The
dataset used in this experiment is Covtype, which is a large-scale
dataset with over 580k samples. We observe that though HpBandSter                    3 https://github.com/luinardi/hypermapper
and BOHB accelerates the optimization in the beginning, their con-                   4 https://github.com/JasperSnoek/spearmint

vergence results are worse than that of SMAC3. However, OpenBox                      5 https://github.com/PKU-DAIR/open-box

                                                                                12
