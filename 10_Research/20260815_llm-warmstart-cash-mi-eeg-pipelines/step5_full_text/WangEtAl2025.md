---
citation_key: "WangEtAl2025"
title: "LLM Agent for Hyper-Parameter Optimization"
authors: "Wanzhe Wang; Jianqiu Peng; Menghao Hu; Wei-chao Zhong; Tong Zhang; Shuai Wang; Yixin Zhang; Mingjie Shao; Wanli Ni"
year: 2025
doi: "10.1109/icccworkshops67136.2025.11148145"
source: "arXiv (2506.15167)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "2506.15167"
conversion: "pdftotext -layout (automated)"
---

# LLM Agent for Hyper-Parameter Optimization

LLM Agent for Hyper-Parameter Optimization
                                                                 Wanzhe Wang, Jianqiu Peng, Menghao Hu, Weihuang Zhong, Tong Zhang,
                                                                      Shuai Wang, Yixin Zhang, Mingjie Shao, and Wanli Ni

                                           Abstract—Hyper-parameters are essential and critical for the              Mutation (WS-PSO-CM) [3] has emerged as a promising ap-
                                        performance of communication algorithms. However, current                    proach due to its adaptability and efficiency in solving complex
                                        hyper-parameters optimization approaches for Warm-Start Par-                 UAV trajectory and communication problems. However, the
                                        ticles Swarm Optimization with Crossover and Mutation (WS-
                                        PSO-CM) algorithm, designed for radio map-enabled unmanned                   performance of WS-PSO-CM heavily relies on the selection
                                        aerial vehicle (UAV) trajectory and communication, are pri-                  of hyper-parameters, which remains a significant challenge.
                                        marily heuristic-based, exhibiting low levels of automation and                  Radio map aims to reflecting the spatial distribution char-
                                        improvable performance. In this paper, we design an Large                    acteristics of wireless channel, which can construct models
                                        Language Model (LLM) agent for automatic hyper-parameters-                   of radio signal distribution in the environment by collecting

arXiv:2506.15167v2 [cs.IT] 9 Jul 2025
                                        tuning, where an iterative framework and Model Context Proto-
                                        col (MCP) are applied. In particular, the LLM agent is first                 and analyzing information about signal strength, delay, and
                                        set up via a profile, which specifies the boundary of hyper-                 channel state. Yang et al. [4] proposed a deep learning-based
                                        parameters, task objective, terminal condition, conservative or              radio map that directly generates beamforming vectors from
                                        aggressive strategy of optimizing hyper-parameters, and LLM                  location information using a task-oriented neural network. In
                                        configurations. Then, the LLM agent iteratively invokes WS-PSO-              [5], Dong et al. presented radio maps in UAV anti-interference
                                        CM algorithm for exploration. Finally, the LLM agent exits the
                                        loop based on the terminal condition and returns an optimized                communications. In [6], Zhang et al. proposed the use of
                                        set of hyperparameters. Our experiment results show that the                 radio maps for cellular network connectivity in UAV three-
                                        minimal sum-rate achieved by hyper-parameters generated via                  dimensional path planning, aiming to address the issues of
                                        our LLM agent is significantly higher than those by both human               communication quality assurance and path optimization during
                                        heuristics and random generation methods. This indicates that                UAV flight. In [7], Yang et al. proposed an end-to-end method
                                        an LLM agent with PSO and WS-PSO-CM algorithm knowledge
                                        is useful in seeking high-performance hyper-parameters.                      integrating radio map-based beamforming with reduced pilots.
                                           Index Terms—Large language model, model context protocol,                 In [8], Yuan et al. introduced radio maps into the design of
                                        radio map, unmanned aerial vehicle                                           multi-UAV relay networks and employed a joint optimization
                                                                                                                     approach using the particle swarm optimization (PSO) algo-
                                                                  I. Introduction                                    rithm to overcome the limitations of traditional models in com-
                                                                                                                     plex geographical environments. In [9], Dong et al. addressed
                                           The rapid advancement of unmanned aerial vehicles (UAV)                   the problem of dynamic multi-UAV target tracking assisted by
                                        has revolutionized wireless communication systems, offer-                    radio maps by proposing methods based on grid and particle
                                        ing flexible deployment and enhanced coverage in dynamic                     filtering techniques. In [10], the PSO is integrated with the
                                        environments [1], [2]. A critical enabler for UAV-assisted                   genetic algorithm (GA) for 3D UAV trajectory optimization,
                                        communication is the radio map, which provides essential                     considering obstacles. However, when using a dynamic radio
                                        spatial channel information to optimize trajectory planning and              map, specifically a large path-loss (PL) database instead of
                                        transmission strategies. Among the optimization algorithms,                  statistical channel models, the computational time required for
                                        Warm-Start Particle Swarm Optimization with Crossover and                    PSO combined with GA becomes excessively high, leading to
                                                                                                                     suboptimal performance when time constraints are imposed. In
                                           Wanzhe Wang, Jianqiu Peng, Weihuang Zhong are with the Harbin Institute   [3], Hu et al. introduced WS-PSO-CM algorithms, which uses
                                        of Technology (Shenzhen), Shenzhen, China (email: wangwz724@163.com,
                                        {13400053106,15986817267}@163.com).                                          statistical channel models for convex optimization to generate
                                           Menghao Hu is with the School of Information Science and Engineering,     a high-quality initial particle swarm for warm start (WS).
                                        Southeast University, China (email: 220240758@seu.edu.cn).                   Compared to the baseline algorithm PSO-CM, it significantly
                                           Tong Zhang is with the Guangdong Provincial Key Laboratory of
                                        Aerospace Communication and Networking Technology, Harbin Institute of       improves performance within the same number of iterations.
                                        Technology, Shenzhen 518055, China, and also with the National Mobile            Currently, hyper-parameter tuning for WS-PSO-CM is pre-
                                        Communications Research Laboratory, Southeast University, China (e-mail:     dominantly heuristic-based, requiring extensive domain exper-
                                        tongzhang@hit.edu.cn).
                                           Shuai Wang is with the Shenzhen Institutes of Advanced Technology,        tise and manual intervention. Such methods not only lack
                                        Chinese Academy of Sciences, Shenzhen, China (email: s.wang@siat.ac.cn).     automation but also often yield suboptimal configurations,
                                           Yixin Zhang is with the Beijing University of Posts and Telecommunica-    limiting the algorithm’s potential in real-world applications.
                                        tions, Beijing, China (email: yixin.zhang@bupt.edu.cn).
                                           Mingjie Shao is with the State Key Laboratory of Mathematical Sci-            Large Language Model (LLM), as a significant break-
                                        ences, AMSS, Chinese Academy of Sciences, Beijing, China (email:             through in artificial intelligence (AI), demonstrates powerful
                                        mingjieshao@amss.ac.cn).                                                     capabilities in knowledge representation and task inference,
                                           Wanli Ni is with the Department of Electronic Engineering, Tsinghua
                                        University, Beijing 100084, China (e-mail: niwanli@tsinghua.edu.cn).         enabling novel integrative applications across diverse do-
                                           Corresponding author: T. Zhang                                            mains [11]–[13]. In [11], Qiu et al. envisioned that the LLM

agent system enhanced the accuracy of medical diagnostics             the distance between UAVs must exceed a specified minimum
and in [12] Romera-Paredes et al. proposed that LLM enabled           threshold, i.e.,
the automated exploration of complex mathematical problems                                                   ′       ′
                                                                            m [t] − ℓm′ [t]∥2 ≥ dmin , m ̸= m , ∀m, m , t.
                                                                          ∥ℓUAV      UAV
                                                                                                                                         (1)
and algorithm optimization tasks. In [13], Ren et al. introduced
the application of LLM in industrial internet of things. The          Similar to [3], the author introduce a binary scheduling
authors of [14] proposed low-rank adaptation-based federated          variable am,n [t] ∈ {0, 1}, where am,n [t] = 1, if UGV n
fine-tuning for large models in wireless networks and derived         communicates with UAV m at time slot t, and otherwise
an optimality gap to capture the impact of rank selection             am,n [t] = 0. Furthermore, at each time slot, each UAV
and gradient aggregation distortion. Furthermore, the authors         communicates with only one UGV, i.e.,
of [15] presented multi-agent collaborative reasoning frame-                  N                         M
                                                                              X                         X
works to enhance efficiency of large AI models. In [16],                            am,n [t] ≤ 1,             am,n [t] ≤ 1, ∀m, n, t.    (2)
Qiu et al. employed LLM-enabled optimization for wireless                     n=1                       m=1
network planning, demonstrating that large language models               The radio map, i.e., PL map, for UGV n acting as a
can significantly enhance the optimization process in wireless        transmitter in time slot t is denoted by Hn [t] ∈ RX×Y ×Z ,
network design. The advanced capabilities of LLM agents               where the 3D physical space is divided into cubic cells
enables the development of more automated and intelligent             X × Y × Z, each with a volume of δ 3 . The PL values in map
systems capable of generating precise radio maps and planning         Hn [t] at the 3D coordinate index (xm , ym , zm ) is denoted by
wireless networks with minimal human intervention. In [17],           hn,t (ωm ), where the index ωm = (xm , ym , zm ). This index
Chatzistefanidis et al. proposed a collaborative framework            can be mapped to the actual position of a UAV using the
utilizing LLMs to achieve greater abstraction and automation          following relations: xm = ⌊(xα          m [t] − Xmin )/δ⌋ + 1, ym =
in network planning, thereby substantially streamlining the           ⌊(ymα
                                                                            [t] − Ymin )/δ⌋ + 1, and zm = ⌊(Hm [t] − Hmin )/δ⌋ + 1,
deployment process. Also, in [18], Sevim et al. adopted an            where Xmin , Ymin and Hmin serve as anchor points, and Hmin
alternative approach by developing a reinforcement learning-          also represents the minimum flight altitude of the UAVs. The
based system enhanced with LLM capabilities for wireless net-         rate between UGV n and UAV m at time slot t is calculated
work deployment. In [19], Quan et al. utilized the capability of      as Rm,n [t] = log2 (1 + am,n [t]SINRm,n [t]), where SINR
LLM for radio map generation and wireless network planning.                          h (ωm )Pn [t]
                                                                      = PN (PM n,t                                  , with N0 denoting the
   In this paper, we investigate an automatic hyper-parameters-              p̸=n  q=1 apq [t])hp,t (ωm )Pp [t]+N0

tuning LLM agent for WS-PSO-CM algorithm, specifically                additive white Gaussian noise (AWGN) power.
tailored for radio map-enabled UAV trajectory and commu-                 In order to maximize the minimal sum-rate of UGVs subject
nication. In particular, to build the LLM agent, we apply             to scheduling scheduling, power, quality of service and UAV
model context protocol (MCP), which is an open protocol that          flight constraints, we optimize the UGV-UAV scheduling set
standardizes how applications provide context to LLMs. We             A = {am,n [t]}, UAV 3D trajectory set Q = {αm [t]} and
employ an iterative optimization framework for our proposed           UGV transmit power set P = {Pn [t]}. Mathematically, this
LLM agent with PSO and WS-PSO-CM algorithm knowledge,                 problem can be formulated as the following mixed-integer
where the agent engages in sequential exploration of hyper-           programming problem:
parameter space through dynamic interactions with WS-PSO-                                           T    M
                                                                                              1 XX
CM algorithm. After a few iterations, our proposed LLM agent           (P0) max         min             Rm,n [t]                        (3a)
                                                                              A,Q,P           T t=1 m=1
outputs a set of hyper-parameters. Experiment results show
that the obtained hyper-parameters can achieve 54.33% and                        s.t.   (1), (2),
72.61% gains over that obtained by human heuristics in [3]                              Rm,n [t] ≥ am,n [t]Rmin , ∀m, n, t,             (3b)
and an uniform distribution, respectively.                                              ∥ℓUAV
                                                                                          m [t + 1] − ℓm [t]∥2 ≤ Vmax τ, t < T,(3c)
                                                                                                       UAV

                                                                                        Hmin ≤ Hm [t] ≤ Hmax , ∀m, t,                   (3d)
     II. Optimization of 3D UAV Trajectory and
Communication: Introduction of WS-PSO-CM Algorithm                                      θm [t] ≤ θmax , ∀m, t,                          (3e)
                                                                                         m [t] ∈
                                                                                        ℓUAV   / B, ∀m, t,                              (3f)
A. System Model and Problem Formulation
                                                                                        0 ≤ Pn [t] ≤ Pmax , ∀n, t,                      (3g)
   In this section, we considered the WS-PSO-CM algorithm
                                                                                        am,n [t] ∈ {0, 1} , ∀m, n, t,                   (3h)
for joint optimization of UAV trajectories and communications for low-altitude air-ground cooperation [3]. The problem        where constraint (3b) enforces the quality of service re-
WS-PSO-CM algorithm is aroused by an air-ground coop-                 quirement for each communication link, where the minimum
eration system and N unmanned ground vehicles (UGVs)                  acceptable data rate is specified by Rmin . The UAVs’ velocity
communicate with M UAVs. We represent the 3D positions                is limited to a maximum of Vmax by constraint (3c), with τ
of UAVs and UGVs at each time slot t as ℓUAV               m [t] =    denoting the length of each time slot. The UAVs’ flight altitude
(αm [t], Hm [t]) = (xα                α
                              m [t], ym [t], Hm [t]) and ℓn
                                                          UGV
                                                              [t] =   is restricted to remain within the bounds of Hmax and Hmin by
(βn [t], 0) = (xβn [t], ynβ [t], 0), respectively, where m ∈ M =      constraint (3d). Constraint (3e) imposes a limit on the UAVs’
{1, 2, ..., M }, n ∈ N = {1, 2, ..., N }. To prevent collisions,      turning angle, which is not allowed to exceed the maximum

allowable value θmax ; here, θm [t] is defined as the angle        Algorithm 1 Pseudo Code of WS-PSO-CM Algorithm
between ℓUAV m[t]−ℓUAV m[t−1] and ℓUAV m[t+1]−ℓUAV m[t].            1: Initialize: Warm start by convex optimization (see [3]),
Collision avoidance between UAVs and buildings is guaranteed             set iter = 1, and Pnum , ω, c1 , c2 , k1 , k2 , k3 , k4 ;
by constraint (3f), where B denotes the three-dimensional           2: Repeat:
space occupied by buildings. Finally, constraint (3g) ensures       3:   Update particles’ velocities according to (4a);
that the transmit power of UGVs does not exceed Pmax .              4:   Update particles’ positions according to (4b);
   The authors of [3] proposes a general WS-PSO-CM al-              5:   Update the ρBestk ;
gorithm for radio maps, which will be validated through             6:   Select gBest from ρBestk ;
simulations using the established radio map database.               7:   Particles cross and mutate according to Cross and
                                                                       Mutation strategy;
B. WS-PSO-CM Algorithm
                                                                    8:   Set iter ← iter + 1;
   The standard PSO algorithm performs global optimization          9: Until:iter = Piter ;
by simulating collective biological behavior, where particles      10: Output: gBest;
(representing candidate solutions) move through the solution
space with dynamically updated velocities based on individual                        PM PT −1                   θm [t]−θmax
and social learning experiences, gradually converging toward       i.e., A(Qk ) =      m=1      t=2 max(0,          θmax    ). C(Qk ) is
the global optimum. WS-PSO-CM algorithm enhances stan-             denoted as the sum of the amount of height of path points
dard PSO through two key modifications: a warm-start initial-                 PM CbPexceeding
                                                                   located within
                                                                                         T −1
                                                                                                     the height of the building, i.e.
ization strategy that dramatically reduces convergence time,       C(Qk ) = m=1 t=1 max (0, ∆m [t]),
and the integration of genetic algorithm operators (crossover         3) Crossover and Mutation: Crossover and mutation opand mutation) to sustain solution diversity throughout the         erations are applied periodically to the particles to introduce
optimization process.                                              genetic diversity and explore new regions of the solution space.
   WS-PSO-CM algorithm is used for optimizing the UAV                 4) Stopping Condition: The algorithm stops after a specitrajectory and communication, considering several objectives       fied number of iterations and the best solution is returned.
like minimizing path loss, avoiding collisions, adhering to           Here, Pnum , ω, c1 , c2 , k1 , k2 , k3 , k4 are hyper-parameters
speed limits, and maximizing the minimal sum-rate of com-          within the algorithm. After obtaining an optimized result from
munication links between UAVs and UGVs.                            a given initial value, the analysis of the optimization outcome
   1) Initialization: A population of particles is initialized.    is performed using LLM to adjust the hyper-parameters, which
These particles represent potential solutions.Also, we define      in turn allows for further refinement of the previous results.
that Piter and Pnum are the number of iteration and number of
                                                                                  III. MCP and Proposed LLM Agent
particles in the proposed WS-PSO-CM.
   2) Main Optimization Loop: Particles update their velocity      A. A Brief Introduction to LLM Agent
by considering their current velocity, the global best solution,      Besides LLM model, the LLM agent framework through
and their local best solution. Based on this, they adjust their    two modules (Profile, Tools) and three functions (Planning,
position accordingly. The strategy for updating both position      Action, Memory), presented in Fig. 1, which are elaborated
and velocity and the fitness function is as follows:               below.
                                                                      1) Profile: The Profile module serves as the foundational
  Vk = ω × Vk + c1 × rand1 × (pBestk − Vk )                (4a)
                                                                   context for the agent’s understanding of the user. It en-
        + c2 × rand2 × (gBest − Vk ) ,                             compasses profile context, which includes demographic, per-
  Qk = Qk + Vk ,                                           (4b)    sonality, and social information about the user, providing a
  F (Qk ) = k1 T (Qk ) + k2 S(Qk ) + k3 A(Qk ) + k4 C(Qk ),        personalized basis for task execution. The generation strategy
                                                         (4c)      within this module determines the approach by which the agent
                                                                   generates responses, utilizing methods such as handcrafted
where ω, c1 and c2 represent the inertia weight, cogni-            techniques, LLM generation approaches, or dataset alignment
tive coefficient, and social coefficient, respectively, which      methods to tailor output according to specific user needs.
influence the update of the particles’ position and veloc-            2) Tools: The Tools module provides external application
ity. pBestk and gBest are the kth particle’s position and          interface (API) integration for enhanced agent capabilities. It
the best particle’s position at current iteration. rand1 and       is the agent’s interface with external systems, enabling it to
rand2 are two random variables ranging from [0, 1] with            perform operations beyond its native LLM capabilities. It acts
uniform distribution. Also, where kP  1 , k2P
                                            , k3 , k4 are the      as a secure, structured gateway for integrating APIs, databases,
                                         T     M
weight coefficients. T (Qk ) = min t=1 m=1 Rm,n [t] is             and computational services while maintaining control over
denoted as the min sum-rate. The sum of the normalized             execution.
velocities exceeding theP
                        maximum
                              PT speed limitvm Vmax is denoted        3) Planning: In Planning functionality, the agent formulates
                          M                     [t]−Vmax
by S(Qk ), S(Qk ) =       m=1    t=2 max(0,      Vmax    ) with    strategies for task execution. This process can occur without
          |ℓUAV [t]−ℓUAV [t−1]|
vm [t] = m      τ
                  m
                        ; A(Qk ) is denoted as the sum of          Feedback, where the agent employs single-path reasoning,
the normalized turning angle exceeding the maximum limit,          multi-path reasoning, or external planning to generate an initial

                                           Fig. 1: LLM agent modules and functionalities.

action plan. Alternatively, planning with feedback integrates          functions as a centralized tool runtime controller, responsible
feedback from the environment, human input, or the model               for authenticating, scheduling, and monitoring registered tools
itself, allowing the agent to refine its plan and adjust its           (e.g., APIs), whereas MCP client acts as a tool invocation
approach dynamically in response to changing conditions.               proxy that initiates requests and formats tool inputs/outputs
   4) Action: The Action functionality involves the execution          according to protocol specifications. While MCP server mainof the agent’s formulated plans. The action target determines          tains tool metadata repositories and enforces execution polithe target of the action, which can range from task completion         cies (e.g., rate-limiting, dependency resolution), MCP client
and communication to exploration. Action production is driven          focuses on context-aware tool selection and lightweight result
by agent memory recall, where it generates concrete actions            post-processing, delegating heavy tool operations to MCP
based on prior knowledge. The action space defines the scope           server.
of possible actions, incorporating tools and the agent’s own              Also, MCP optimizes communication paths and transmisknowledge to facilitate the execution of the plan.                     sion formats, reducing communication latency in distributed
   5) Memory: The Memory functionality is central to the               environments, especially when handling large-scale gradient
agent’s ability to store and recall relevant information over          updates and model parameters. It supports both synchronous
time. It is structured around a memory structure, which can            and asynchronous training modes, allowing flexible adjusteither be unified or hybrid, depending on the system’s re-             ments based on training needs, thereby improving training
quirements for knowledge management. The memory formats                efficiency while ensuring model accuracy. Additionally, the
include various types of stored information, such as languages,        MCP has a fault tolerance mechanism, ensuring stable opdatabases, embeddings, and lists, each serving a distinct pur-         eration of the training process even in the event of network
pose for task execution. The memory operation allows the               fluctuations or hardware failures, with automatic recovery in
agent to interact with stored knowledge through processes like         case of abnormalities, preventing the entire training process
memory reading, writing, and reflection, enabling dynamic              from being interrupted due to a single point of failure.
updating and retrieval of information during task completion.
                                                                          Security and privacy protection are a major highlight of
B. MCP and Proposed LLM Agent                                          the MCP protocol, especially in scenarios such as federated
   1) MCP Architecture: MCP introduces a standardized com-             learning. When multiple institutions or entities collaborate
munication framework that simplifies the development of                on training, the protocol ensures that data is not directly
LLM-based agents. By providing a unified protocol, it enables          shared while employing encryption methods to protect data
seamless integration of language models into agent systems.            privacy during communication. In response to the diversity of
The core idea of MCP is to abstract the communication                  modern computing resources, the MCP protocol also offers
between models and external systems into a client-server               intelligent resource scheduling and load balancing mechaarchitecture, enabling dynamic context passing and flexible            nisms, enabling different computing nodes to work efficiently
tool invocation through standardized interfaces (e.g., JSON-           together, maximizing the use of computational resources and
RPC-based communication). This architecture allows a MCP               avoiding resource wastage and performance bottlenecks.
client to establish connections with multiple servers, thereby            The interaction process between the MCP client and the
facilitating flexible context transfer and functional extensibility.   MCP server is structured and systematic. Initially, the MCP
   Fig. 2 shows the MCP architecture, responsible for enabling         client is configured to establish a connection with a desigsecure bidirectional communication between MCP client and              nated MCP server. Upon receiving a user’s prompt, the MCP
MCP server, which is developed by Anthropic. MCP server                client formulates a structured prompt that incorporates both

                                                                                                                                       Action
   MCP Clients     MCP Client                                MCP Server   MCP Servers
                                    ① initial request

                                    ② initial response                                        Human               LLM Call                       Environment
                                    ③ notification

                                Ready for message exchange
                                                                                                                                      Feedback

                   MCP Client          ……                    MCP Server
                                                                                                                       Stop

                  Fig. 2: Illustration of MCP.                                                   Fig. 3: The framework of proposed LLM agent.

                                                                                                 TABLE I: Hyper-Parameters during Iterations
the user’s intent and the tools made available by the MCP
                                                                                         Index    Pnum   k1     k2       k3    k4         w       c1      c2
server. This composite input is then submitted to the LLM
                                                                                           1       46    0.12   0.65    0.15   0.06       0.68    1.55    1.45
for interpretation and task planning. The LLM interprets the                               2       50     0.1    0.7     0.1    0.1      0.729   1.494   1.494
prompt and generates corresponding invocation instructions.                                3       40     0.1    0.7     0.1   0.05        0.7     1.5     1.5
These instructions are then transmitted from the client to the                             4       50    0.15    0.6     0.3    0.1        0.6     1.8     1.8
                                                                                           5       40    0.15    0.6     0.2   0.05       0.65     1.6     1.4
MCP server. Upon receipt, the MCP server parses the request,                               6       40    0.12   0.63     0.2   0.02       0.68     1.7     1.3
executes the appropriate operations.
   Overall, the MCP protocol not only provides an efficient
communication means for the training of large-scale deep
                                                                                          • Baseline 1 (Human Heuristics in [3]): These hyperlearning models but also promotes the application of multi-
                                                                                            parameters are used in [3] based on human heuristics,
party collaboration and privacy protection technologies, laying
                                                                                            where Pnum = 100, ω = 0.5, c1 = 2, c2 = 2, k1 = 2,
a solid foundation for the future development of AI.
                                                                                            k2 = 0.5, k3 = 5, k4 = 5.
   2) Proposed LLM Agent: As shown in Fig. 3, we propose an
                                                                                          • Baseline 2 (Random Generation via Uniform Distribuinteractive LLM agent that orchestrates collaboration between
                                                                                            tion): Generate hyper-parameters via uniform distributhe model, humans, and the environment, namely WS-PSO-
                                                                                            tion, where Pnum = 58, ω = 0.8765, c1 = 5.4321,
CM algorithm. In the profile, we define the boundary of hyper-
                                                                                            c2 = 9.8765, k1 = 3.7284, k2 = 8.1235, k3 = 1.9823,
parameters, task objective, terminal condition, conservative
                                                                                            k4 = 6.5432.
or aggressive strategy of optimizing hyper-parameters, and
LLM configurations. Tools include two MCP servers. The first                               Since we only consider iterations within a few rounds, we
server specializes in hyper-parameter transmission, delivering                          do not include Grid Search and Bayesian Optimization in the
optimized parameters to numerical computation software for                              comparison.
WS-PSO-CM operations. The second server focuses on hyper-                                  In Fig. 4, it shows the minimal sum-rate improvement over
parameter management and historical record maintenance.                                 iterations by LLM agent. In each iteration, the recommended
   Once the proposed LLM agent runs, the LLM generates                                  hyper-parameters are given in TABLE I. It can be seen that
hyper-parameters based on the profile. The algorithm’s op-                              by reading the WS-PSO-CM algorithm and PSO background,
timization results are fed back to the LLM for iterative                                LLM agent yields satisfactory hyper-parameters within 6 itrefinement. The LLM agent terminates the loop according to                              erations. Note that, the human heuristics hyper-parameters in
prescribed conditions and returns a set of hyper-parameters.                            [3] are with 100 particles. Meanwhile, TABLE I demonstrates
                                                                                        that the recommended number of particles does not exceed 50.
                           IV. Experiments                                              Next, by observing the output of WS-PSO-CM algorithm, the
                                                                                        LLM agent keeps refining the hyper-parameters. It stops until a
   In this experiment, to build LLM agent, we choose deepseek                           minimal sum-rate of 22.28 bps/Hz is achieved. This shows that
R1 as MCP client. Our simulation scenario covers the teach-                             with PSO background and WS-PSO-CM algorithm, the LLM
ing building area and hall of HITSZ, with dimensions of                                 agent explores the landscape of the optimization problem, and
240 × 400 × 60 m3 . A corresponding digital twin is then                                gives high-performance hyper-parameters eventually.
constructed in mathematical software for numerical calcula-                                In Fig. 4, it shows that our proposed LLM agent achieves
tions using PL map data. Our simulation parameters are the                              54.33% and 72.61% gains over that by hyper-parameters via
same as that in [3], except the number of UGVs is 8, the                                human heuristics in [3] and an uniform distribution, respecnumber of UAVs is 4, the speed of UGV 1, 2, 3, 4, 5, 6,                                 tively. This demonstrates that the LLM agent equipped with
7, 8 are 18.0km/h, 18.0km/h, 13.8km/h, 13.8km/h, 12.5km/h,                              PSO and WS-PSO-CM algorithm knowledge possesses strong
17.0km/h, 13.0km/h, 19.0km/h, respectively. In this experi-                             capabilities in finding high-performance hyper-parameters,
ment, the hyper-parameters awaiting tuning are as follows:                              which are crucial for optimizing WS-PSO-CM algorithm
Pnum , ω, c1 , c2 , k1 , k2 , k3 , k4 . We adopt the following two                      performance. With the hyper-parameters by LLM agent, the
baselines for comparison:                                                               optimized UAV 3D trajectory is visualized in Fig. 6.

                                      25

          minimal sum-rate (bps/Hz)
                                                                                       22.26   22.28

                                                                               20.73

                                      20
                                                               18.34
                                                    18.01

                                           16.33

                                      15
                                            1        2           3        4              5      6
                                                              Iteration Index

  Fig. 4: Minimal sum-rate over iterations by LLM agent.

                                      30

          minimal sum-rate (bps/Hz)
                                      25
                                                     22.28

                                      20                                                               Fig. 6: Visualization of the WS-PSO-CM optimized UAV 3D
                                                                                                       trajectories by proposed hyper-parameters.
                                      15                               14.44
                                                                                       12.91

                                      10
                                                                                                        [6] S. Zhang and R. Zhang, “Radio map-based 3D path planning for
                                       5                                                                    cellular-connected UAV,” IEEE Trans. Wireless Commun., vol. 20, no. 3,
                                                                                                            pp. 1975–1989, 2021.
                                       0                                                                [7] B. Yang, W. Wang, and W. Zhang, “Radio map-based beamforming
                                                   Proposed Baseline 1 Baseline 2                           assisted with reduced pilots,” IEEE Trans. Wireless Commun., pp. 1–1,
                                                                                                            2025.
     Fig. 5: Performance comparison against baselines.                                                  [8] X. Yuan, Y. Hu, J. Gross, and A. Schmeink, “Radio-map-based UAV
                                                                                                            placement design for UAV-assisted relaying networks,” in Proc. IEEE
                                                                                                            SSP, pp. 286–290, 2021.
                                                                                                        [9] Y. Dong, C. He, and Z. J. Wang, “Dynamic object tracking by multi-
                                                         V. Conclusion                                      UAV with time-variant radio maps,” IEEE Trans. Wireless Commun.,
   The proposed LLM agent for hyper-parameter optimiza-                                                     vol. 23, no. 7, pp. 7471–7487, 2024.
                                                                                                       [10] H. Pan, Y. Liu, G. Sun, J. Fan, S. Liang, and C. Yuen, “Joint power and
tion in the WS-PSO-CM algorithm significantly enhanced                                                      3D trajectory optimization for UAV-enabled wireless powered commu-
UAV trajectory and communication optimization, achieving                                                    nication networks with obstacles,” IEEE Trans. Commun., vol. 71, no. 4,
performance gains of 54.33% and 72.61% over heuristic                                                       pp. 2364–2380, 2023.
                                                                                                       [11] J. Qiu, K. Lam, G. Li, A. Acharya, T. Y. Wong, A. Darzi, W. Yuan, and
and random approaches, respectively. The success of this                                                    E. J. Topol, “LLM-based agentic systems in medicine and healthcare,”
framework highlighted its broader applicability to related                                                  Nature Mach. Intell., vol. 6, no. 12, pp. 1418–1420, 2024.
problems, such as multi-UAV coordination, dynamic resource                                             [12] B. Romera-Paredes, M. Barekatain, A. Novikov, M. Balog, M. P. Kumar,
                                                                                                            E. Dupont, F. J. Ruiz, J. S. Ellenberg, P. Wang, O. Fawzi, et al.,
allocation, radio map construction, and reinforcement learning                                              “Mathematical discoveries from program search with large language
tasks, where automated and intelligent tuning can replace                                                   models,” Nature, vol. 625, no. 7995, pp. 468–475, 2024.
manual intervention. By integrating LLM capabilities with                                              [13] Y. Ren, H. Zhang, F. R. Yu, W. Li, P. Zhao, and Y. He, “Industrial
                                                                                                            internet of things with large language models (LLMs): an intelligenceoptimization algorithms, LLM agents offered a scalable and                                                  based reinforcement learning approach,” IEEE Trans. Mobile Comput.,
efficient solution for complex systems, paving the way for                                                  2024.
advanced automation in wireless communication, autonomous                                              [14] H. Sun, H. Tian, W. Ni, J. Zheng, D. Niyato, and P. Zhang, “Federated
                                                                                                            low-rank adaptation for large models fine-tuning over wireless networks,”
systems, and beyond.                                                                                        IEEE Trans. Wireless Commun., vol. 24, no. 1, pp. 659–675, 2025.
                                                                                                       [15] W. Ni, H. Sun, H. Ao, and H. Tian, “Federated intelligence: When large
                                                             References                                     AI models meet federated fine-tuning and collaborative reasoning at the
[1] Z. Yang, C. Pan, K. Wang, and M. Shikh-Bahaei, “Energy efficient                                        network edge,” arXiv preprint arXiv:2503.21412, 2025.
    resource allocation in UAV-enabled mobile edge computing networks,”                                [16] K. Qiu, S. Bakirtzis, I. Wassell, H. Song, J. Zhang, and K. Wang,
    IEEE Trans. Wireless Commun., vol. 18, no. 9, pp. 4576–4589, 2019.                                      “Large language model-based wireless network design,” IEEE Wireless
[2] M. Hu, T. Zhang, S. Wang, G. Li, Y. Chen, Q. Li, and G. Chen,                                           Commun. Lett., 2024.
    “Integrated robotics networks with co-optimization of drone placement                              [17] I. Chatzistefanidis, A. Leone, and N. Nikaein, “Maestro: LLM-driven
    and air-ground communications,” in Proc. VTC-Fall, pp. 1–5, 2023.                                       collaborative automation of intent-based 6G networks,” IEEE Netw. Lett.,
[3] M. Hu, T. Zhang, S. Wang, C. Zhang, C. She, G. Chen, and M. Wen,                                        vol. 6, no. 4, pp. 227–231, 2024.
    “Radio map-enabled 3D trajectory and communication optimization for                                [18] N. Sevim, M. Ibrahim, and S. Ekin, “Large language models (LLMs)
    low-altitude air-ground cooperation,” arXiv preprint arXiv:2505.06944,                                  assisted wireless network deployment in urban settings,” in Proc. IEEE
    2025. [Online]. Available: https://arxiv.org/abs/2505.06944.                                            VTC-Fall, pp. 1–7, 2024.
[4] W. Wang, B. Yang, and W. Zhang, “Deep learning-based radio map for                                 [19] H. Quan, W. Ni, T. Zhang, X. Ye, Z. Xie, S. Wang, Y. Liu, and H. Song,
    MIMO-OFDM downlink precoding,” J. Commun. Inform. Netw., vol. 8,                                        “Large language model agents for radio map generation and wireless
    no. 3, pp. 203–211, 2023.                                                                               network planning,” IEEE Netw. Lett., pp. 1–1, 2025.
[5] Y. Dong, C. He, Z. Wang, and L. Zhang, “Radio map assisted path
    planning for UAV anti-jamming communications,” IEEE Signal Process.
    Lett., vol. 29, pp. 607–611, 2022.
