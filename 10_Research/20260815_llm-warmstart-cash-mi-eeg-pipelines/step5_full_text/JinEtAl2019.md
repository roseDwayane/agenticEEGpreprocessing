---
citation_key: "JinEtAl2019"
title: "Auto-Keras: An Efficient Neural Architecture Search System"
authors: "Haifeng Jin; Qingquan Song; Xia Hu"
year: 2019
doi: "10.1145/3292500.3330648"
source: "OA PDF (https://dl.acm.org/doi/pdf/10.1145/3292500.3330648)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
conversion: "pdftotext -layout (automated)"
---

# Auto-Keras: An Efficient Neural Architecture Search System

Applied Data Science Track Paper                                                                                      KDD ’19, August 4–8, 2019, Anchorage, AK, USA

       Auto-Keras: An Efficient Neural Architecture Search System
                                                              Haifeng Jin, Qingquan Song, Xia Hu
                                      Department of Computer Science and Engineering, Texas A&M University
                                                        {jin,song_3134,xiahu}@tamu.edu

ABSTRACT                                                                                             learning, neural architecture search (NAS), which aims to search
Neural architecture search (NAS) has been proposed to automat-                                       for the best neural network architecture for the given learning task
ically tune deep neural networks, but existing search algorithms,                                    and dataset, has become an effective computational tool in AutoML.
e.g., NASNet [51], PNAS [29], usually suffer from expensive com-                                     Unfortunately, existing NAS algorithms are usually computationputational cost. Network morphism, which keeps the functional-                                       ally expensive. The time complexity of NAS is O(nt¯), where n is the
ity of a neural network while changing its neural architecture,                                      number of neural architectures evaluated during the search, and t¯
could be helpful for NAS by enabling more efficient training during                                  is the average time consumption for evaluating each of the n neuthe search. In this paper, we propose a novel framework enabling                                     ral networks. Many NAS approaches, such as deep reinforcement
Bayesian optimization to guide the network morphism for effi-                                        learning [2, 37, 47, 50, 51], gradient-based methods [8, 31, 33] and
cient neural architecture search. The framework develops a neural                                    evolutionary algorithms [12, 17, 30, 38, 39, 41], require a large n to
network kernel and a tree-structured acquisition function optimiza-                                  reach a good performance. Moreover, many of them train each of
tion algorithm to efficiently explores the search space. Extensive                                   the n neural networks from scratch, which is very slow.
experiments on real-world benchmark datasets have been done to                                           Initial efforts have been devoted to making use of network mordemonstrate the superior performance of the developed framework                                      phism in neural architecture search [7, 13]. It is a technique to
over the state-of-the-art methods. Moreover, we build an open-                                       morph the architecture of a neural network but keep its functionsource AutoML system based on our method, namely Auto-Keras.                                         ality [10, 45]. Therefore, we are able to modify a trained neural
The code and documentation are available at https://autokeras.com.                                   network into a new architecture using the network morphism op-
The system runs in parallel on CPU and GPU, with an adaptive                                         erations, e.g., inserting a layer or adding a skip-connection. Only a
search strategy for different GPU memory limits.                                                     few more epochs are required to further train the new architecture
                                                                                                     towards better performance. Using network morphism would re-
CCS CONCEPTS                                                                                         duce the average training time t¯ in neural architecture search. The
                                                                                                     most important problem to solve for network morphism-based NAS
• Computing methodologies → Neural networks; Supervised
                                                                                                     methods is the selection of operations, which is to select an operalearning; Discrete space search.
                                                                                                     tion from the network morphism operation set to morph an existing
KEYWORDS                                                                                             architecture to a new one. The network morphism-based NAS meth-
                                                                                                     ods are not efficient enough. They either require a large number of
Automated Machine Learning, AutoML, Neural Architecture Search,                                      training examples [7], or inefficient in exploring the large search
Bayesian Optimization, Network Morphism                                                              space [13]. How to perform efficient neural architecture search with
ACM Reference Format:                                                                                network morphism remains a challenging problem.
Haifeng Jin, Qingquan Song, Xia Hu. 2019. Auto-Keras: An Efficient Neural                                As we know, Bayesian optimization [40] has been widely adopted
Architecture Search System. In The 25th ACM SIGKDD Conference on Knowl-                              to efficiently explore black-box functions for global optimization,
edge Discovery and Data Mining (KDD ’19), August 4–8, 2019, Anchorage, AK,
                                                                                                     whose observations are expensive to obtain. For example, it has
USA. ACM, New York, NY, USA, 11 pages. https://doi.org/10.1145/3292500.
                                                                                                     been used in hyperparameter tuning for machine learning mod-
3330648
                                                                                                     els [3, 15, 21, 24, 40, 44], in which Bayesian optimization searches
1     INTRODUCTION                                                                                   among different combinations of hyperparameters. During the
                                                                                                     search, each evaluation of a combination of hyperparameters in-
Automated Machine Learning (AutoML) has become a very im-
                                                                                                     volves an expensive process of training and testing the machine
portant research topic with wide applications of machine learning
                                                                                                     learning model, which is very similar to the NAS problem. The
techniques. The goal of AutoML is to enable people with limited
                                                                                                     unique properties of Bayesian optimization motivate us to explore
machine learning background knowledge to use machine learning
                                                                                                     its capability in guiding the network morphism to reduce the nummodels easily. Work has been done on automated model selection,
                                                                                                     ber of trained neural networks n to make the search more efficient.
automated hyperparameter tunning, and etc. In the context of deep
                                                                                                         It is non-trivial to design a Bayesian optimization method for
Permission to make digital or hard copies of all or part of this work for personal or                network morphism-based NAS due to the following challenges.
classroom use is granted without fee provided that copies are not made or distributed                First, the underlying Gaussian process (GP) is traditionally used for
for profit or commercial advantage and that copies bear this notice and the full citation
on the first page. Copyrights for components of this work owned by others than ACM                   learning probability distribution of functions in Euclidean space.
must be honored. Abstracting with credit is permitted. To copy otherwise, or republish,              To update the Bayesian optimization model with observations, the
to post on servers or to redistribute to lists, requires prior specific permission and/or a
fee. Request permissions from permissions@acm.org.
                                                                                                     underlying GP is to be trained with the searched architectures and
KDD ’19, August 4–8, 2019, Anchorage, AK, USA                                                        their performances. However, the neural network architectures are
© 2019 Association for Computing Machinery.                                                          not in Euclidean space and hard to parameterize into a fixed-length
ACM ISBN 978-1-4503-6201-6/19/08. . . $15.00
https://doi.org/10.1145/3292500.3330648
                                                                                                     vector. Second, an acquisition function needs to be optimized for

                                                                                              1946

Applied Data Science Track Paper                                                                  KDD ’19, August 4–8, 2019, Anchorage, AK, USA

KDD ’19, August 4–8, 2019, Anchorage, AK, USA                                                                                              H. Jin et al.

Bayesian optimization to generate the next architecture to observe.                                θ ∗ = argmin L(f (θ ), D t r ain ).              (2)
However, in the context of network morphism, it is not to maximize                                            θ
a function in Euclidean space, but finding a node in a tree-structured           where Cost(·, ·) is the evaluation metric function, e.g., accuracy,
search space, where each node represents a neural architecture and               mean sqaured error, θ ∗ is the learned parameter of f .
each edge is a morph operation. Thus traditional gradient-based                    The search space F covers all the neural architectures, which
methods cannot be simply applied. Third, the changes caused by a                 can be morphed from the initial architectures. The details of the
network morphism operation is complicated. A network morphism                    morph operations are introduced in 3.3. Notably, the operations
operation on one layer may change the shapes of some intermediate                can change the number of filters in a convolutional layer, which
output tensors, which no longer match input shape requirements of                makes F larger than methods with fixed layer width [31].
the layers taking them as input. How to maintain such consistency
is a challenging problem.                                                        3     NETWORK MORPHISM GUIDED BY
    In this paper, an efficient neural architecture search with network                BAYESIAN OPTIMIZATION
morphism is proposed, which utilizes Bayesian optimization to
                                                                                 The key idea of the proposed method is to explore the search space
guide through the search space by selecting the most promising
                                                                                 via morphing the neural architectures guided by Bayesian optimizaoperations each time. To tackle the aforementioned challenges, an
                                                                                 tion (BO) algorithm. Traditional Bayesian optimization consists
edit-distance neural network kernel is constructed. Being consistent
                                                                                 of a loop of three steps: update, generation, and observation. In
with the key idea of network morphism, it measures how many
                                                                                 the context of NAS, our proposed Bayesian optimization algorithm
operations are needed to change one neural network to another.
                                                                                 iteratively conducts: (1) Update: train the underlying Gaussian pro-
Besides, a novel acquisition function optimizer, which is capable
                                                                                 cess model with the existing architectures and their performances;
of balancing between the exploration and exploitation, is designed
                                                                                 (2) Generation: generate the next architecture to observe by optispecially for the tree-structure search space to enable Bayesian
                                                                                 mizing a delicately defined acquisition function; (3) Observation:
optimization to select from the operations. In addition, a graph-level
                                                                                 obtain the actual performance by training the generated neural arnetwork morphism is defined to address the changes in the neural
                                                                                 chitecture. There are three main challenges in designing a method
architectures based on layer-level network morphism. The proposed
                                                                                 for morphing the neural architectures with Bayesian optimization.
approach is compared with the state-of-the-art NAS methods [13,
                                                                                 We introduce three key components separately in the subsequent
22] on benchmark datasets of MNIST, CIFAR10, and FASHION-
                                                                                 sections coping with the three challenges.
MNIST. Within a limited search time, the architectures found by
our method achieves the lowest error rates on all of the datasets.
                                                                                 3.1    Edit-Distance Neural Network Kernel for
    In addition, we have developed a widely adopted open-source
AutoML system based on our proposed method, namely Auto-Keras.                          Gaussian Process
It is an open-source AutoML system, which can be download and                    The first challenge we need to address is that the NAS space is
installed locally. The system is carefully designed with a concise               not a Euclidean space, which does not satisfy the assumption of
interface for people not specialized in computer programming and                 traditional Gaussian process (GP). Directly vectorizing the neural
data science to use. To speed up the search, the workload on CPU                 architecture is impractical due to the uncertain number of layers
and GPU can run in parallel. To address the issue of different GPU               and parameters it may contain. Since the Gaussian process is a
memory, which limits the size of the neural architectures, a memory              kernel method, instead of vectorizing a neural architecture, we
adaption strategy is designed for deployment.                                    propose to tackle the challenge by designing a neural network
    The main contributions of the paper are as follows:                          kernel function. The intuition behind the kernel function is the edit-
                                                                                 distance for morphing one neural architecture to another. More
• Propose an algorithm for efficient neural architecture search                  edits needed from one architecture to another means the further
  based on network morphism guided by Bayesian optimization.                     distance between them, thus less similar they are. The proof of the
• Conduct intensive experiments on benchmark datasets to demon-                  validity of the kernel function is presented in Appendix E.
  strate the superior performance of the proposed method over the                   Kernel Definition: Suppose fa and fb are two neural networks.
  baseline methods.                                                              Inspired by Deep Graph Kernels [48], we propose an edit-distance
• Develop an open-source system, namely Auto-Keras, which is                     kernel for neural networks. Edit-distance here means how many
  one of the most widely used AutoML systems.                                    operations are needed to morph one neural network to another.
                                                                                 The concrete kernel function is defined as:
2   PROBLEM STATEMENT
                                                                                                      κ(fa , fb ) = e −ρ (d(fa , fb )) ,
                                                                                                                          2
                                                                                                                                                    (3)
The general neural architecture search problem we studied in this
paper is defined as: Given a neural architecture search space F , the            where function d(·, ·) denotes the edit-distance of two neural netinput data D divided into D t r ain and Dval , and the cost function             works, whose range is [0, +∞), ρ is a mapping function, which
Cost(·), we aim at finding an optimal neural network f ∗ ∈ F ,                   maps the distance in the original metric space to the corresponding
which could achieve the lowest cost on dataset D. The definition is              distance in the new space. The new space is constructed by emequivalent to finding f ∗ satisfying:                                            bedding the original metric space into a new one using Bourgain
                                                                                 Theorem [4], which ensures the validity of the kernel.
                  f ∗ = argmin Cost(f (θ ∗ ), Dval ),              (1)              Calculating the edit-distance of two neural networks can be
                          f ∈F                                                   mapped to calculating the edit-distance of two graphs, which is

                                                                          1947

Applied Data Science Track Paper                                                                                KDD ’19, August 4–8, 2019, Anchorage, AK, USA

Auto-Keras: An Efficient Neural Architecture Search System                                                      KDD ’19, August 4–8, 2019, Anchorage, AK, USA

Figure 1: Neural Network Kernel. Given two neural networks fa , fb , and matchings between the similar layers, the figure
shows how the layers of fa can be changed to the same as fb . Similarly, the skip-connections in fa also need to be changed to
the same as fb according to a given matching.

an NP-hard problem [49]. Based on the search space F defined in                             Since there are many ways to morph fa to fb , to find the best
Section 2, we tackle the problem by proposing an approximated                             matching between the nodes that minimizes Dl , we propose a
solution as follows:                                                                      dynamic programming approach by defining a matrix A |La |× |Lb | ,
                                                                                          which is recursively calculated as follows:
                d(fa , fb ) = Dl (La , Lb ) + λD s (S a , Sb ),              (4)
                                                                                                                                                                  (i) (j)
                                                                                             Ai, j = max[Ai−1, j + 1, Ai, j−1 + 1, Ai−1, j−1 + dl (la , lb )],                    (7)
where Dl denotes the edit-distance for morphing the layers, i.e., the
minimum edits needed to morph fa to fb if the skip-connections                                                                                        (i)
                                                                                          where Ai, j is the minimum value of Dl (La , Lb ), where La =
                                                                                                                                                            (j)             (i)
                      (1) (2)                     (1) (2)
are ignored, La = {la , la , . . .} and Lb = {lb , lb , . . .} are the                      (1) (2)       (i)           (j)          (1) (2)          (j)
                                                                                          {la , la , . . . , la } and Lb = {lb , lb , . . . , lb }.
layer sets of neural networks fa and fb , D s is the approximated
                                                                                             Calculating D s : The intuition of D s is the sum of the the editedit-distance for morphing skip-connections between two neural
                  (1) (2)                    (1) (2)                                      distances of the matched skip-connections in two neural networks
networks, S a = {sa , sa , . . .} and Sb = {sb , sb , . . .} are the skip-                into pairs. As shown in Figure 1, the skip-connections with the
connection sets of neural network fa and fb , and λ is the balancing                      same color are matched pairs. Similar to Dl (·, ·), D s (·, ·) is defined
factor between the distance of the layers and the skip-connections.                       as follows:
   Calculating Dl : We assume |La | < |Lb |, the edit-distance for
                                                                                                                         |S a |
morphing the layers of two neural architectures fa and fb is calcu-                                                      Õ
                                                                                                                                       (i)      (i)
                                                                                                 D s (S a , Sb ) = min            ds (sa , φ s (sa )) + |Sb | − |S a | ,          (8)
lated by minimizing the follow equation:
                                                                                                                         i=1
                              |L a|
                              Õ
                                          (i)      (i)                                    where we assume |S a | < |Sb |. (|Sb | − |S a |) measures the total edit-
        Dl (La , Lb ) = min           dl (la , φl (la )) + |Lb | − |La | ,   (5)          distance for non-matched skip-connections since each of the non-
                              i=1
                                                                                          matched skip-connections in Sb calls for an edit of inserting a new
where φl : La → Lb is an injective matching function of layers                            skip connection into fa . The mapping function φ s : S a → Sb is
                         (i)          (j)
satisfying: ∀i < j, φl (la ) ≺ φl (la ) if layers in La and Lb are                        an injective function. ds (·, ·) is the edit-distance for two matched
all sorted in topological order. dl (·, ·) denotes the edit-distance of                   skip-connections defined as:
widening a layer into another defined in Equation (6),                                                                |u(sa ) − u(sb )| + |δ (sa ) − δ (sb )|
                                                                                                  ds (sa , sb ) =                                               ,                 (9)
                                       |w(la ) − w(lb )|                                                            max[u(sa ), u(sb )] + max[δ (sa ), δ (sb )]
                    dl (la , lb ) =                       ,                  (6)
                                      max[w(la ), w(lb )]                                 where u(s) is the topological rank of the layer the skip-connection
where w(l) is the width of layer l.                                                       s started from, δ (s) is the number of layers between the start and
   The intuition of Equation (5) is consistent with the idea of net-                      end point of the skip-connection s.
work morphism shown in Figure 1. Suppose a matching is provided                              This minimization problem in Equation (8) can be mapped to
between the nodes in two neural networks. The sizes of the tensors                        a bipartite graph matching problem, where fa and fb are the two
are indicators of the width of the previous layers (e.g., the output                      disjoint sets of the graph, each skip-connection is a node in its
vector length of a fully-connected layer or the number of filters                         corresponding set. The edit-distance between two skip-connections
of a convolutional layer). The matchings between the nodes are                            is the weight of the edge between them. The weighted bipartite
marked by light blue. So a matching between the nodes can be seen                         graph matching problem is solved by the Hungarian algorithm
as matching between the layers. To morph fa to fb with the given                          (Kuhn-Munkres algorithm) [26].
matching, we need to first widen the three nodes in fa to the same
width as their matched nodes in fb , and then insert a new node of                        3.2    Optimization for Tree Structured Space
width 20 after the first node in fa . Based on this morphing scheme,                      The second challenge of using Bayesian optimization to guide netthe edit-distance of the layers is defined as Dl in Equation (5).                         work morphism is the optimization of the acquisition function. The

                                                                                   1948

Applied Data Science Track Paper                                                                            KDD ’19, August 4–8, 2019, Anchorage, AK, USA

KDD ’19, August 4–8, 2019, Anchorage, AK, USA                                                                                                      H. Jin et al.

traditional acquisition functions are defined in Euclidean space.                        Algorithm 1 Optimize Acquisition Function
The optimization methods are not applicable to the tree-structured                        1: Input: H , r , Tlow
search via network morphism. To optimize our acquisition func-                            2: T ← 1, Q ← PriorityQueue()
tion, we need a method to efficiently optimize the acquisition func-                      3: cmin ← lowest c in H
tion in the tree-structured space. To deal with this problem, we                          4: for (f , θ f , c) ∈ H do
propose a novel method to optimize the acquisition function on                            5:   Q.Push(f )
tree-structured space.                                                                    6: end for
   Upper-confidence bound (UCB) [1] is selected as our acquisition                        7: while Q , ∅ and T > Tlow       do
function, which is defined as:                                                            8:   T ← T × r , f ← Q.Pop()
                                                                                          9:   for o ∈ Ω(f ) do
                         α(f ) = µ(yf ) − βσ (yf ),                       (10)
                                                                                         10:     f ′ ← M(f , {o})
                                                                                                       cmin −α (f ′ )
where yf = Cost(f , D), β is the balancing factor, µ(yf ) and σ (yf )                    11:     if e    T       > Rand() then
are the posterior mean and standard deviation of variable yf pre-                        12:        Q.Push(f ′ )
dicted by the Gaussian process. It has two important properties,                         13:     end if
which fit our problem. First, it has an explicit balance factor β for ex-                14:     if cmin > α(f ′ ) then
ploration and exploitation. Second, α(f ) is directly comparable with                    15:        cmin ← α(f ′ ), fmin ← f ′
the cost function value c (i) in search history H = {(f (i) , θ (i) , c (i) )}.          16:     end if
The UCB estimates the lowest possible cost given the neural net-                         17:   end for
work f . fˆ = arдmin f α(f ) is the generated neural architecture for                    18: end while
next observation.                                                                        19: Return The nearest ancestor of fmin in H , the operation se-
    The tree-structured space is defined as follows. During the op-                          quence to reach fmin
timization of α(f ), fˆ should be obtained from f (i) and O, where
f (i) is an observed architecture in the search history H , O is a
sequence of operations to morph the architecture into a new one.                         Following the setting in A* search, in each iteration, the architec-
Morph f to fˆ with O is denoted as fˆ ← M(f , O), where M(·, ·) is                       ture with the lowest acquisition function value is popped out to be
the function to morph f with the operations in O. Therefore, the                         expanded on line 8 to 10, where Ω(f ) is all the possible operations
search can be viewed as a tree-structured search, where each node                        to morph the architecture f , M(f , o) is the function to morph the
is a neural architecture, whose children are morphed from it by                          architecture f with the operation sequence o. However, not all the
network morphism operations.                                                             children are pushed into the priority queue for exploration purpose.
    The most common defect of network morphism is it only grows                          The decision of whether it is pushed into the queue is made by
the size of the architecture instead of shrinking them. Using network                                                             cmin −α (f ′ )
morphism for NAS may end up with a very large architecture                               simulated annealing on line 11, where e   T     is a typical accepwithout enough exploration on the smaller architectures. However,                        tance function in simulated annealing. cmin and fmin are updated
in our tree-structure search, we not only expand the leaves but also                     from line 14 to 16, which record the minimum acquisition function
the inner nodes, which means the smaller architectures found in                          value and the corresponding architecture.
the early stage can be selected multiple times to morph to more
comparatively small architectures.                                                       3.3    Graph-Level Network Morphism
    Inspired by various heuristic search algorithms for exploring                        The third challenge is to maintain the intermediate output tensor
the tree-structured search space and optimization methods balanc-                        shape consistency when morphing the architectures. Previous work
ing between exploration and exploitation, a new method based                             showed how to preserve the functionality of the layers the operators
on A* search [19] and simulated annealing [23] is proposed. A*                           applied on, namely layer-level morphism. However, from a graphalgorithm is widely used for tree-structure search. It maintains a                       level view, any change of a single layer could have a butterfly effect
priority queue of nodes and keeps expanding the best node in the                         on the entire network. Otherwise, it would break the input and
queue. Since A* always exploits the best node, simulated annealing                       output tensor shape consistency. To tackle the challenge, a graphis introduced to balance the exploration and exploitation by not                         level morphism is proposed to find and morph the layers influenced
selecting the estimated best architecture with a probability.                            by a layer-level operation in the entire network.
    As shown in Algorithm 1, the algorithm takes minimum temper-                            Follow the four network morphism operations on a neural netature Tlow , temperature decreasing rate r for simulated annealing,                      work f ∈ F defined in [13], which can all be reflected in the change
and search history H described in Section 2 as the input. It outputs                     of the computational graph G. The first operation is inserting a
a neural architecture f ∈ H and a sequence of operations O to                            layer to f to make it deeper denoted as deep(G, u), where u is
morph f into the new architecture. From line 2 to 6, the searched                        the node marking the place to insert the layer. The second one is
architectures are pushed into the priority queue, which sorts the                        widening a node in f denoted as wide(G, u), where u is the node
elements according to the cost function value or the acquisition                         representing the intermediate output tensor to be widened. Widen
function value. Since UCB is chosen as the acquisiton function,                          here could be either making the output vector of the previous fully-
α(f ) is directly comparable with the history observation values c (i) .                 connected layer of u longer, or adding more filters to the previous
From line 7 to 18, it is the loop optimizing the acquisition function.                   convolutional layer of u, depending on the type of the previous

                                                                                  1949

Applied Data Science Track Paper                                                                      KDD ’19, August 4–8, 2019, Anchorage, AK, USA

Auto-Keras: An Efficient Neural Architecture Search System                                            KDD ’19, August 4–8, 2019, Anchorage, AK, USA

layer. The third is adding an additive connection from node u to
node v denoted as add(G, u, v). The fourth is adding an concatenative connection from node u to node v denoted as concat(G, u, v).
For deep(G, u), no other operation is needed except for initializing
the weights of the newly added layer. However, for all other three
operations, more changes are required to G.
    First, we define an effective area of wide(G, u 0 ) as γ to better
describe where to change in the network. The effective area is a
set of nodes in the computational graph, which can be recursively
defined by the following rules: 1. u 0 ∈ γ . 2. v ∈ γ , if ∃eu→v < Ls ,
u ∈ γ . 3. v ∈ γ , if ∃ev→u < Ls , u ∈ γ . Ls is the set of fully-connected
layers and convolutional layers. Operation wide(G, u 0 ) needs to
change two set of layers, the previous layer set Lp = {eu→v ∈
Ls |v ∈ γ }, which needs to output a wider tensor, and next layer
set Ln = {eu→v ∈ Ls |u ∈ γ }, which needs to input a wider tensor.
Second, for operator add(G, u 0 , v 0 ), additional pooling layers may
be needed on the skip-connection. u 0 and v 0 have the same number
of channels, but their shape may differ because of the pooling layers                Figure 2: Auto-Keras System Overview. (1) The user calls the
between them. So we need a set of pooling layers whose effect is                     API. (2) The Searcher generates neural architectures on CPU.
the same as the combination of all the pooling layers between                        (3) Graph builds real neural networks with parameters on
u 0 and v 0 , which is defined as Lo = {e ∈ Lpool |e ∈ pu0 →v0 }.                    RAM from the neural architectures. (4) The neural network
where pu0 →v0 could be any path between u 0 and v 0 , Lpool is the                   is copied to GPU for training. (5) Trained neural networks
pooling layer set. Another layer Lc is used after to pooling layers                  are saved on storage devices. The Searcher is updated based
to process u 0 to the same width as v 0 . Third, in concat(G, u 0 , v 0 ),           on the training results. Step (2) to (5) will repeat until it
the concatenated tensor is wider than the original tensor v 0 . The                  reaches the time limit.
concatenated tensor is input to a new layer Lc to reduce the width
back to the same width as v 0 . Additional pooling layers are also
needed for the concatenative connection.                                                Although, there are several AutoML services available on large
                                                                                     cloud computing platforms, three things are prohibiting the users
                                                                                     from using them. First, cloud services are not free to use, which
3.4    Time Complexity Analysis
                                                                                     may not be affordable for everyone who wants to use AutoML
As described at the start of Section 3, Bayesian optimization can                    techniques. Second, the cloud-based AutoML usually requires combe roughly divided into three steps: update, generation, and obser-                  plicated configurations of Docker containers and Kubernetes, which
vation. The bottleneck of the algorithm efficiency is observation,                   is not easy for people without a rich computer science background.
which involves the training of the generated neural architecture.                    Third, the AutoML service providers are honest-but-curious [9],
Let n be the number of architectures in the search history. The time                 which cannot guarantee the security and privacy of the data. An
complexity of the update is O(n2 log2 n). In each generation, the ker-               open-source software, which is easily downloadable and runs lonel is computed between the new architectures during optimizing                      cally, would solve these problems and make the AutoML accessible
acquisition function and the ones in the search history, the number                  to everyone. To bridge the gap, we developed Auto-Keras.
of values in which is O(nm), where m is the number of architectures                     It is challenging, to design an easy-to-use and locally deploycomputed during the optimization of the acquisition function. The                    able system. First, we need a concise and configurable application
time complexity for computing d(·, ·) once is O(l 2 + s 3 ), where l                 programming interface (API). For the users who don’t have rich
and s are the number of layers and skip-connections. So the overall                  experience in programming, they could easily learn how to use the
time complexity is O(nm(l 2 + s 3 ) + n2 log2 n). The magnitude of                   API. For the advanced users, they can still configure the details of
these factors is within the scope of tens. So the time consumption                   the system to meet their requirements. Second, local computation
of update and generation is trivial comparing to the observation.                    resources may be limited. We need to make full use of the local
                                                                                     computation resources to speed up the search. Third, the available
4     AUTO-KERAS                                                                     GPU memory may be of different sizes in different environments.
Based on the proposed neural architecture search method, we de-                      We need to adapt the neural architecture sizes to the GPU memory
veloped an open-source AutoML system, namely Auto-Keras. It                          during the search.
is named after Keras [11], which is known for its simplicity in
creating neural networks. Similar to SMAC [21], TPOT [35], Auto-                     4.1    System Overview
WEKA [44], and Auto-Sklearn [15], the goal is to enable domain                       The system architecture of Auto-Keras is shown in Figure 2. We
experts who are not familiar with machine learning technologies                      design this architecture to fully make use of the computational
to use machine learning techniques easily. However, Auto-Keras                       resource of both CPU and GPU, and utilize the memory efficiently
is focusing on the deep learning tasks, which is different from the                  by only placing the currently useful information on the RAM, and
systems focusing on the shallow models mentioned above.                              save the rest on the storage devices, e.g., hard drives. The top part

                                                                              1950

Applied Data Science Track Paper                                                                    KDD ’19, August 4–8, 2019, Anchorage, AK, USA

KDD ’19, August 4–8, 2019, Anchorage, AK, USA                                                                                               H. Jin et al.

is the API, which is directly called by the users. It is responsible                 Searcher             Queue               GPU               CPU
for calling corresponding middle-level modules to complete certain
functionalities. The Searcher is the module of the neural architec-                          Pop Graph
ture search algorithm containing Bayesian Optimizer and Gaussian
Process. These search algorithms run on CPU. The Model Trainer is                                              Train Model
                                                                                                                                    Generate
a module responsible for the computation on GPUs. It trains given                                                                    Graph
neural networks with the training data in a separate process for
parallelism. The Graph is the module processing the computational
                                                                                                                                    Generated
graphs of neural networks, which is controlled by the Searcher for
                                                                                                                                     Graph
the network morphism operations. The current neural architecture                             Push Graph
in the Graph is placed on RAM for faster access. The Model Storage                                                Update GP
is a pool of trained models. Since the size of the neural networks
are large and cannot be stored all in memory, the model storage
saves all the trained models on the storage devices.
    A typical workflow for the Auto-Keras system is as follows. The
                                                                                   Figure 3: CPU and GPU Parallelism. The Searcher obtains
user initiated a search for the best neural architecture for the dataset.
                                                                                   the next neural architecture to be trained and starts the
The API received the call, preprocess the dataset, and pass it to the
                                                                                   training on GPU in a separate process. Then, instead of wait-
Searcher to start the search. The Bayesian Optimizer in the Searcher
                                                                                   ing for the training to finish, it directly starts to generate the
would generate a new architecture using CPU. It calls the Graph
                                                                                   next neural architecture on CPU.
module to build the generated neural architecture into a real neural
network in the RAM. The new neural architecture is copied the
GPU for the Model Trainer to train with the dataset. The trained
                                                                                   4.3    CPU and GPU Parallelism
model is saved in the Model Storage. The performance of the model
is feedback to the Searcher to update the Gaussian Process.                        To make full use of the limited local computation resources, the pro-
                                                                                   gram can run in parallel on the GPU and the CPU at the same time.
                                                                                   If we do the observation (training of the current neural network),
4.2    Application Programming Interface                                           update, and generation of Bayesian optimization in sequential or-
The design of the API follows the classic design of the Scikit-Learn               der. The GPUs will be idle during the update and generation. The
API [6, 36], which is concise and configurable. The training of a                  CPUs will be idle during the observation. To improve efficiency,
neural network requires as few as three lines of code calling the                  the observation is run in parallel with the generation in separated
constructor, the fit and predict function respectively. To accommo-                processes. A training queue is maintained as a buffer for the Model
date the needs of different users, we designed two levels of APIs.                 Trainer. Figure 3 shows the Sequence diagram of the parallelism
The first level is named as task-level. The users only need to know                between the CPU and the GPU. First, the Searcher requests the
their task, e.g., Image Classification, Text Regression, to use the API.           queue to pop out a new graph and pass it to GPU to start training.
The second level is named search-level, which is for advanced users.               Second, while the GPU is busy, the searcher requests the CPU to
The user can search for a specific type of neural network architec-                generate a new graph. At this time period, the GPU and the CPU
tures, e.g., multi-layer perceptron, convolutional neural network.                 work in parallel. Third, the CPU returns the generated graph to the
To use this API, they need to preprocess the dataset by themselves                 searcher, who pushes the graph into the queue. Finally, the Model
and know which type of neural network, e.g., CNN or MLP, is the                    Trainer finished training the graph on the GPU and returns it to the
best for their task.                                                               Searcher to update the Gaussian process. In this way, the idle time
   Several accommodations have been implemented to enhance the                     of GPU and CPU are dramatically reduced to improve the efficiency
user experience with the Auto-Keras package. First, the user can                   of the search process.
restore and continue a previous search which might be accidentally
killed. From the users’ perspective, the main difference of using                  4.4    GPU Memory Adaption
Auto-Keras comparing with the AutoML systems aiming at shallow                     The size of the neural networks needs to be limited according to
models is the much longer time consumption, since a number of                      the GPU memory. Otherwise, the system would crash because of
deep neural networks are trained during the neural architecture                    running out of GPU memory. Many approaches have been taken
search. It is possible for some accident to happen to kill the pro-                to search for memory-efficient neural architectures [42]. In Autocess before the search finishes. Therefore, the search outputs all                 Keras, we implement a memory estimation function on our own
the searched neural network architectures with their trained pa-                   data structure for the neural architectures. An integer value is
rameters into a specific directory on the disk. As long as the path                used to mark the upper bound of the neural architecture size. Any
to the directory is provided, the previous search can be restored.                 new computational graph whose estimated size exceeds the upper
Second, the user can export the search results, which are neural                   bound is discarded. However, the system may still crash because
architectures, as saved Keras models for other usages. Third, for                  the management of the GPU memory is very complicated, which
advanced users, they can specify all kinds of hyperparameters of                   cannot be precisely estimated. So whenever it runs out of GPU
the search process and neural network optimization process by the                  memory, the upper bound is lowered down to further limit the size
default parameters in the interface.                                               of the generated neural networks.

                                                                            1951

Applied Data Science Track Paper                                                                 KDD ’19, August 4–8, 2019, Anchorage, AK, USA

Auto-Keras: An Efficient Neural Architecture Search System                                       KDD ’19, August 4–8, 2019, Anchorage, AK, USA

5   EXPERIMENTS                                                                                Table 1: Classification Error Rate
In the experiments, we aim at answering the following questions.
1) How effective is the search algorithm with limited running time?                        Methods      MNIST      CIFAR10     FASHION
2) How much efficiency is gained from Bayesian optimization and                            RANDOM       1.79%       16.86%      11.36%
network morphism? 3) Does the proposed kernel function correctly                           GRID         1.68%       17.17%      10.28%
measure the similarity among neural networks in terms of their                             SPMT         1.36%       14.68%       9.62%
actual performance?                                                                        SMAC         1.43%       15.04%      10.87%
   Datasets Three benchmark datasets, MNIST [27], CIFAR10 [25],                            SEAS         1.07%       12.43%       8.05%
and FASHION [46] are used in the experiments to evaluate our                               NASBOT        NA         12.30%        NA
method. They prefer very different neural architectures to achieve                         BFS          1.56%       13.84%       9.13%
good performance.                                                                          BO           1.83%       12.90%       7.99%
   Baselines Four categories of baseline methods are used for com-                         AK           0.55%       11.44%       7.42%
parison, which are elaborated as follows:                                                  AK-DP        0.60%        3.60%       6.72%

• Straightforward Methods: random search (RAND) and grid search
  (GRID). They search the number of convolutional layers and the
                                                                                rest of the methods. Except for AK-DP, all other methods are fairly
  width of those layers.
                                                                                compared using the same initial architecture to start the search.
• Conventional Methods: SPMT [40] and SMAC [21]. Both SPMT
  and SMAC are designed for general hyperparameters tuning
  tasks of machine learning models instead of focusing on the deep              5.1    Evaluation of Effectiveness
  neural networks. They tune the 16 hyperparameters of a three-                 We first evaluate the effectiveness of the proposed method. The
  layer convolutional neural network, including the width, dropout              results are shown in Table 1. The following conclusions can be
  rate, and regularization rate of each layer.                                  drawn based on the results.
• State-of-the-art Methods: SEAS [13], NASBOT [22]. We carefully                   (1) AK-DP is evaluated to show the final performance of our
  implemented the SEAS as described in their paper. For NAS-                    system, which shows the deployed system (AK-DP) achieved state-
  BOT, since the experimental settings are very similar, we directly            of-the-art performance on all three datasets.
  trained their searched neural architecture in the paper. They did                (2) The proposed method AK achieves the lowest error rate on
  not search architectures for MNIST and FASHION dataset, so the                all the three datasets, which demonstrates that AK is able to find
  results are omitted in our experiments.                                       simple but effective architectures on small datasets (MNIST) and can
• Variants of the proposed method: BFS and BO. Our proposed                     explore more complicated structures on larger datasets (CIFAR10).
  method is denoted as AK. BFS replaces the Bayesian optimization                  (3) The straightforward approaches and traditional approaches
  in AK with the breadth-first search. BO is another variant, which             perform well on the MNIST dataset, but poorly on the CIFAR10
  does not employ network morphism to speed up the training. For                dataset. This may come from the fact that: naive approaches like
  AK, β is set to 2.5, while λ is set to 1 according to the parameter           random search and grid search only try a limited number of archi-
  sensitivity analysis.                                                         tectures blindly while the two conventional approaches are unable
                                                                                to change the depth and skip-connections of the architectures.
In addition, the performance of the deployed system of Auto-Keras                  (4) Though the two state-of-the-art approaches achieve accept-
(AK-DP) is also evaluated in the experiments. The difference from               able performance, SEAS could not beat our proposed model due
the AK above is that AK-DP uses various advanced techniques                     to its subpar search strategy. The hill-climbing strategy it adopts
to improve the performance including learning rate scheduling,                  only takes one step at each time in morphing the current best archimultiple manually defined initial architectures.                                tecture, and the search tree structure is constrained to be unidirec-
   Experimental Setting The general experimental setting for                    tionally extending. Comparatively speaking, NASBOT possesses
evaluation is described as follows: First, the original training data           stronger search expandability and also uses Bayesian optimization
of each dataset is further divided into training and validation sets            as our proposed method. However, the low efficiency in training the
by 80-20. Second, the testing data of each dataset is used as the               neural architectures constrains its power in achieving comparable
testing set. Third, the initial architecture for SEAS, BO, BFS, and             performance within a short time period. By contrast, the network
AK is a three-layer convolutional neural network with 64 filters in             morphism scheme along with the novel searching strategy ensures
each layer. Fourth, each method is run for 12 hours on a single GPU             our model to achieve desirable performance with limited hardware
(NVIDIA GeForce GTX 1080 Ti) on the training and validation set                 resources and time budges.
with batch size of 64. Fifth, the output architecture is trained with              (5) For the two variants of AK, BFS preferentially considers
both the training and validation set. Sixth, the testing set is used            searching a vast number of neighbors surrounding the initial archito evaluate the trained architecture. Error rate is selected as the             tecture, which constrains its power in reaching the better architecevaluation metric since all the datasets are for classification. For a          tures away from the initialization. By comparison, BO can jump
fair comparison, the same data processing and training procedures               far from the initial architecture. But without network morphism, it
are used for all the methods. The neural networks are trained for 200           needs to train each neural architecture with a much longer time,
epochs in all the experiments. Notably, AK-DP uses a real deployed              which limits the number of architectures it can search within a
system setting, whose result is not directly comparable with the                given time.

                                                                         1952

Applied Data Science Track Paper                                                                    KDD ’19, August 4–8, 2019, Anchorage, AK, USA

KDD ’19, August 4–8, 2019, Anchorage, AK, USA                                                                                                  H. Jin et al.

Figure 4: Evaluation of Efficiency. The two figures plot the
same result with different X-axis. BFS uses network mor-                                   (a) Kernel Matrix             (b) Performance Similarity
phism. BO uses Bayesian optimization. AK uses both.
                                                                                  Figure 5: Kernel and Performance Matrix Visualization. (a)
                                                                                  shows the proposed kernel matrix. (b) is a matrix of similar-
                                                                                  ity in the performance of the neural architectures.
5.2    Evaluation of Efficiency
In this experiment, we try to evaluate the efficiency gain of the proposed method in two aspects. First, we evaluate whether Bayesian                     To show the quality of the edit-distance neural network kernel,
optimization can really find better solutions with a limited number               we investigate the difference between the two matrices K and P.
of observations. Second, we evaluated whether network morphism                    K ∈ Rn×n is the kernel matrix, where K i, j = κ(f (i) , f (j) ). P ∈
can enhance training efficiency.                                                  Rn×n describes the similarity of the actual performance between
   We compare the proposed method AK with its two variants, BFS                   neural networks, where P i, j = −|c (i) − c (j) |, where c (i) is the cost
and BO, to show the efficiency gains from Bayesian optimization                   function value in the search history H described in Section 3. We
and network morphism, respectively. BFS does not adopt Bayesian                   use CIFAR10 as an example here, and adopt error rate as the cost
optimization but only network morphism, and use breadth-first                     metric. Since the values in K and P are in different scales, both
search to select the network morphism operations. BO does not                     matrices are normalized to the range [−1, 1]. The difference between
employ network morphism but only Bayesian optimization. Each                      K and P are measured quantitatively with mean square error, which
of the three methods is run on CIFAR10 for twelve hours. The two                  is 1.12 × 10−1 .
figures in Figure 4 shows the same results but with different X-axes.                K and P are visualized in Figure 5a and 5b. Lighter color means
The Y-axis is the lowest error rate achieved. The X-axes are the                  larger values. There are two patterns can be observed in the figures.
number of neural networks searched and the searching time.                        First, the white diagonal of Figure 5a and 5b. According to the
   Two conclusions can be drawn by comparing BFS and AK. First,                   definiteness property of the kernel, κ(f x , f x ) = 1, ∀f x ∈ F , thus
Bayesian optimization can efficiently find better architectures with              the diagonal of K is always 1. It is the same for P since no difference
a limited number of observations. When searched the same num-                     exists in the performance of the same neural network. Second,
ber of neural architectures, AK could achieve a much lower error                  there is a small light square area on the upper left of Figure 5a.
rate than BFS. It demonstrates that Bayesian optimization could                   These are the initial neural architectures to train the Bayesian
effectively guide the search in the right direction, which is much                optimizer, which are neighbors to each other in terms of network
more efficient in finding good architectures than the naive BFS                   morphism operations. A similar pattern is reflected in Figure 5b,
approach. Second, the overhead created by Bayesian optimization                   which indicates that when the kernel measures two architectures
during the search is low. In the left part of Figure 4, it shows BFS and          as similar, they tend to have similar performance.
AK searched similar numbers of neural networks within twelve
hours. BFS is a naive search strategy, which does not consume                     6    CONCLUSION AND FUTURE WORK
much time during the search besides training the neural networks.                 In this paper, a novel method for efficient neural architecture search
AK searched slightly less neural architectures than BFS because of                with network morphism is proposed. It enables Bayesian optimizahigher time complexity.                                                           tion to guide the search by designing a neural network kernel, and
   Two conclusions can be drawn by comparing BO and AK. First,                    an algorithm for optimizing acquisition function in tree-structured
network morphism does not negatively impact search performance.                   space. The proposed method is wrapped into an open-source Au-
In the left part of Figure 4, when BO and AK search a similar                     toML system, namely Auto-Keras, which can be easily downloaded
number of neural architectures, they achieve similar lowest error                 and used with an extremely simple interface. The method has shown
rates. Second, network morphism increases training efficiency, thus               good performance in the experiments and outperformed several
improve the performance. As shown in the left part of Figure 4,                   traditional hyperparameter-tuning methods and state-of-the-art
AK could search much more architectures than BO within the                        neural architecture search methods. The following open questions
same amount of time due to the adoption of network morphism.                      may be studied in future work. (1) Tune the neural architecture
Since network morphism does not degrade the search performance,                   and the hyperparameters of the training process jointly. (2) Design
searching more architectures results in finding better architectures.             task-oriented NAS to solve specific machine learning problems,
This could also be confirmed in the right part of Figure 4. At the                e.g., image segmentation [28], object detection [16, 32], network
end of the searching time, AK achieves lower error rate than BO.                  analysis [20, 43].

                                                                           1953

Applied Data Science Track Paper                                                                                     KDD ’19, August 4–8, 2019, Anchorage, AK, USA

Auto-Keras: An Efficient Neural Architecture Search System                                                           KDD ’19, August 4–8, 2019, Anchorage, AK, USA

ACKNOWLEDGMENTS                                                                                     optimization in WEKA. Journal of Machine Learning Research (2016).
                                                                                               [25] Alex Krizhevsky and Geoffrey Hinton. 2009. Learning multiple layers of features
The authors thank the anonymous reviewers for their helpful com-                                    from tiny images. Technical Report. Citeseer.
ments, and all the contributors from the open-source community.                                [26] Harold W Kuhn. 1955. The Hungarian method for the assignment problem. Naval
                                                                                                    Research Logistics (1955).
This work is, in part, supported by DARPA (#FA8750-17- 2-0116) and                             [27] Yann LeCun, Léon Bottou, Yoshua Bengio, and Patrick Haffner. 1998. Gradient-
NSF (#IIS-1718840 and #IIS-1750074). The views, opinions, and/or                                    based learning applied to document recognition. Proc. IEEE (1998).
findings expressed are those of the author(s) and should not be                                [28] Chenxi Liu, Liang-Chieh Chen, Florian Schroff, Hartwig Adam, Wei Hua, Alan
                                                                                                    Yuille, and Li Fei-Fei. 2019. Auto-DeepLab: Hierarchical Neural Architecture
interpreted as representing the official views or policies of the De-                               Search for Semantic Image Segmentation. arXiv preprint arXiv:1901.02985 (2019).
partment of Defense or the U.S. Government.                                                    [29] Chenxi Liu, Barret Zoph, Jonathon Shlens, Wei Hua, Li-Jia Li, Li Fei-Fei, Alan
                                                                                                    Yuille, Jonathan Huang, and Kevin Murphy. 2017. Progressive neural architecture
                                                                                                    search. In European Conference on Computer Vision.
REFERENCES                                                                                     [30] Hanxiao Liu, Karen Simonyan, Oriol Vinyals, Chrisantha Fernando, and Koray
 [1] Peter Auer, Nicolo Cesa-Bianchi, and Paul Fischer. 2002. Finite-time analysis of               Kavukcuoglu. 2017. Hierarchical representations for efficient architecture search.
     the multiarmed bandit problem. Machine learning (2002).                                        arXiv preprint arXiv:1711.00436 (2017).
 [2] Bowen Baker, Otkrist Gupta, Nikhil Naik, and Ramesh Raskar. 2016. Design-                 [31] Hanxiao Liu, Karen Simonyan, and Yiming Yang. 2018. Darts: Differentiable
     ing neural network architectures using reinforcement learning. arXiv preprint                  architecture search. arXiv preprint arXiv:1806.09055 (2018).
     arXiv:1611.02167 (2016).                                                                  [32] Wei Liu, Dragomir Anguelov, Dumitru Erhan, Christian Szegedy, Scott Reed,
 [3] James Bergstra, Dan Yamins, and David D Cox. 2013. Hyperopt: A python library                  Cheng-Yang Fu, and Alexander C Berg. 2016. Ssd: Single shot multibox detector.
     for optimizing the hyperparameters of machine learning algorithms. In Python                   In European Conference on Computer Vision.
     in Science Conference.                                                                    [33] Renqian Luo, Fei Tian, Tao Qin, Enhong Chen, and Tie-Yan Liu. 2018. Neural
 [4] Jean Bourgain. 1985. On Lipschitz embedding of finite metric spaces in Hilbert                 architecture optimization. In Advances in Neural Information Processing Systems.
     space. Israel Journal of Mathematics (1985).                                              [34] Hiroshi Maehara. 2013. Euclidean embeddings of finite metric spaces. Discrete
 [5] Andrew Brock, Theodore Lim, James M Ritchie, and Nick Weston. 2017. SMASH:                     Mathematics (2013).
     one-shot model architecture search through hypernetworks. arXiv preprint                  [35] Randal S. Olson, Nathan Bartley, Ryan J. Urbanowicz, and Jason H. Moore. 2016.
     arXiv:1708.05344 (2017).                                                                       Evaluation of a Tree-based Pipeline Optimization Tool for Automating Data
 [6] Lars Buitinck, Gilles Louppe, Mathieu Blondel, Fabian Pedregosa, Andreas                       Science. In Genetic and Evolutionary Computation Conference 2016.
     Mueller, Olivier Grisel, Vlad Niculae, Peter Prettenhofer, Alexandre Gramfort,            [36] Fabian Pedregosa, Gaël Varoquaux, Alexandre Gramfort, Vincent Michel,
     Jaques Grobler, et al. 2013. API design for machine learning software: experi-                 Bertrand Thirion, Olivier Grisel, Mathieu Blondel, Peter Prettenhofer, Ron Weiss,
     ences from the scikit-learn project. In ECML PKDD Workshop: Languages for Data                 Vincent Dubourg, et al. 2011. Scikit-learn: Machine Learning in Python. Journal
     Mining and Machine Learning.                                                                   of Machine Learning Research (2011).
 [7] Han Cai, Tianyao Chen, Weinan Zhang, Yong Yu, and Jun Wang. 2018. Efficient               [37] Hieu Pham, Melody Y Guan, Barret Zoph, Quoc V Le, and Jeff Dean. 2018.
     architecture search by network transformation. In AAAI Conference on Artificial                Efficient Neural Architecture Search via Parameter Sharing. arXiv preprint
     Intelligence.                                                                                  arXiv:1802.03268 (2018).
 [8] Han Cai, Ligeng Zhu, and Song Han. 2019. ProxylessNAS: Direct neural architec-            [38] Esteban Real, Alok Aggarwal, Yanping Huang, and Quoc V Le. 2018. Reg-
     ture search on target task and hardware. In International Conference on Learning               ularized Evolution for Image Classifier Architecture Search. arXiv preprint
     Representations.                                                                               arXiv:1802.01548 (2018).
 [9] Qi Chai and Guang Gong. 2012. Verifiable symmetric searchable encryption for              [39] Esteban Real, Sherry Moore, Andrew Selle, Saurabh Saxena, Yutaka Leon Sue-
     semi-honest-but-curious cloud servers. In International Conference on Communi-                 matsu, Quoc Le, and Alex Kurakin. 2017. Large-scale evolution of image
     cations.                                                                                       classifiers, In International Conference on Machine Learning. arXiv preprint
[10] Tianqi Chen, Ian Goodfellow, and Jonathon Shlens. 2015. Net2net: Accelerating                  arXiv:1703.01041.
     learning via knowledge transfer. arXiv preprint arXiv:1511.05641 (2015).                  [40] Jasper Snoek, Hugo Larochelle, and Ryan P Adams. 2012. Practical bayesian
[11] François Chollet et al. 2015. Keras. https://keras.io.                                         optimization of machine learning algorithms. In Advances in Neural Information
[12] Travis Desell. 2017. Large scale evolution of convolutional neural networks                    Processing Systems.
     using volunteer computing. In Genetic and Evolutionary Computation Conference             [41] Masanori Suganuma, Shinichi Shirakawa, and Tomoharu Nagao. 2017. A genetic
     Companion.                                                                                     programming approach to designing convolutional neural network architectures.
[13] Thomas Elsken, Jan-Hendrik Metzen, and Frank Hutter. 2017. Simple And Ef-                      In Genetic and Evolutionary Computation Conference.
     ficient Architecture Search for Convolutional Neural Networks. arXiv preprint             [42] Mingxing Tan, Bo Chen, Ruoming Pang, Vijay Vasudevan, and Quoc V Le. 2018.
     arXiv:1711.04528 (2017).                                                                       Mnasnet: Platform-aware neural architecture search for mobile. arXiv preprint
[14] Thomas Elsken, Jan Hendrik Metzen, and Frank Hutter. 2018. Neural Architecture                 arXiv:1807.11626 (2018).
     Search: A Survey. arXiv preprint arXiv:1808.05377 (2018).                                 [43] Qiaoyu Tan, Ninghao Liu, and Xia Hu. 2019. Deep Representation Learning for
[15] Matthias Feurer, Aaron Klein, Katharina Eggensperger, Jost Springenberg, Manuel                Social Network Analysis. arXiv preprint arXiv:1904.08547 (2019).
     Blum, and Frank Hutter. 2015. Efficient and robust automated machine learning.            [44] Chris Thornton, Frank Hutter, Holger H Hoos, and Kevin Leyton-Brown. 2013.
     In Advances in Neural Information Processing Systems.                                          Auto-WEKA: Combined selection and hyperparameter optimization of classifi-
[16] Golnaz Ghiasi, Tsung-Yi Lin, Ruoming Pang, and Quoc V Le. 2019. NAS-FPN:                       cation algorithms. In International Conference on Knowledge Discovery and Data
     Learning Scalable Feature Pyramid Architecture for Object Detection. arXiv                     Mining.
     preprint arXiv:1904.07392 (2019).                                                         [45] Tao Wei, Changhu Wang, Yong Rui, and Chang Wen Chen. 2016. Network
[17] Zichao Guo, Xiangyu Zhang, Haoyuan Mu, Wen Heng, Zechun Liu, Yichen                            morphism. In International Conference on Machine Learning.
     Wei, and Jian Sun. 2019. Single Path One-Shot Neural Architecture Search with             [46] Han Xiao, Kashif Rasul, and Roland Vollgraf. 2017.              Fashion-MNIST:
     Uniform Sampling. arXiv preprint arXiv:1904.00420 (2019).                                      a Novel Image Dataset for Benchmarking Machine Learning Algorithms.
[18] Bernard Haasdonk and Claus Bahlmann. 2004. Learning with distance substitu-                    arXiv:cs.LG/cs.LG/1708.07747
     tion kernels. In Joint Pattern Recognition Symposium.                                     [47] Sirui Xie, Hehui Zheng, Chunxiao Liu, and Liang Lin. 2019. SNAS: stochastic
[19] Peter E Hart, Nils J Nilsson, and Bertram Raphael. 1968. A formal basis for the                neural architecture search. In International Conference on Learning Representa-
     heuristic determination of minimum cost paths. IEEE transactions on Systems                    tions.
     Science and Cybernetics (1968).                                                           [48] Pinar Yanardag and SVN Vishwanathan. 2015. Deep graph kernels. In Interna-
[20] Xiao Huang, Qiangquan Song, Fan Yang, and Xia Hu. 2019. Large-scale hetero-                    tional Conference on Knowledge Discovery and Data Mining.
     geneous feature embedding. In AAAI Conference on Artificial Intelligence.                 [49] Zhiping Zeng, Anthony KH Tung, Jianyong Wang, Jianhua Feng, and Lizhu Zhou.
[21] Frank Hutter, Holger H Hoos, and Kevin Leyton-Brown. 2011. Sequential Model-                   2009. Comparing stars: On approximating graph edit distance. In International
     Based Optimization for General Algorithm Configuration. In International Con-                  Conference on Very Large Data Bases.
     ference on Learning and Intelligent Optimization.                                         [50] Zhao Zhong, Junjie Yan, and Cheng-Lin Liu. 2017. Practical Network Blocks
[22] Kirthevasan Kandasamy, Willie Neiswanger, Jeff Schneider, Barnabas Poczos,                     Design with Q-Learning. arXiv preprint arXiv:1708.05552 (2017).
     and Eric Xing. 2018. Neural Architecture Search with Bayesian Optimisation and            [51] Barret Zoph and Quoc V Le. 2016. Neural architecture search with reinforcement
     Optimal Transport. Advances in Neural Information Processing Systems (2018).                   learning. In International Conference on Learning Representations.
[23] Scott Kirkpatrick, C Daniel Gelatt, and Mario P Vecchi. 1983. Optimization by
     simulated annealing. science (1983).
[24] Lars Kotthoff, Chris Thornton, Holger H Hoos, Frank Hutter, and Kevin Leyton-
     Brown. 2016. Auto-WEKA 2.0: Automatic model selection and hyperparameter

                                                                                        1954

Applied Data Science Track Paper                                                                KDD ’19, August 4–8, 2019, Anchorage, AK, USA

KDD ’19, August 4–8, 2019, Anchorage, AK, USA                                                                                           H. Jin et al.

APPENDIX: REPRODUCIBILITY                                                     parameters of the corresponding operation are sampled accord-
In this section, we provide the details of our implementation and             ingly. If it is the deep operation, we need to decide the location to
proofs for reproducibility.                                                   insert the layer. In our implementation, any location except right
                                                                              after a skip-connection. Moreover, we support inserting not only
    • The default architectures used to initialized are introduced.           convolutional layers, but activation layers, batch-normalization
    • The details of the implementation of the four network mor-              layers, dropout layer, and fully-connected layers as well. They are
      phism operations are provided.                                          randomly sampled with equally likely probability. If it is the wide
    • The details of preprocessing the datasets are shown.                    operation, we need to choose the layer to be widened. It can be any
    • The details of the training process are described.                      convolutional layer or fully-connected layer, which are randomly
    • The proof of the validity of the kernel function is provided.           sampled with equally likely probability. If it is the skip operations,
    • The process of using ρ(·) to distort the approximated edit-             we need to decide if it is add or concat. The start point and end
      distance of the neural architectures d(·, ·) is introduced.             point of a skip-connection can be the output of any layer except
Notably, the code and detailed documentation are available at Auto-           the already-exist skip-connection layers. So all the possible skip-
Keras official website (https://autokeras.com).                               connections are generated in the form of tuples of the start point,
                                                                              end point and type (add or concat), among which we randomly
A    DEFAULT ARCHITECTURES                                                    sample a skip-connection with equally likely probability.
As we introduced in the experiment section, for all other methods
except AK-DP, are using the same three-layer convolutional neural             C    PREPROCESSING THE DATASETS
network as the default architecture. The AK-DP is initialized with            The benchmark datasets, e.g., MNIST, CIFAR10, FASHION, are pre-
ResNet, DenseNet and the three-layer CNN. In the current imple-               processed before the neural architecture search. It involves normentation, ResNet18 and DenseNet121 specifically are chosen as                malization and data augmentation. We normalize the data to the
the among all the ResNet and DenseNet architectures.                          standard normal distribution. For each channel, a mean and a stan-
   The three-layer CNN is constructed as follows. Each convolu-               dard deviation are calculated since the values in different channels
tional layer is actually a convolutional block of a ReLU layer, a             may have different distributions. The mean and standard deviation
batch-normalization layer, the convolutional layer, and a pooling             are calculated using the training and validation set together. The
layer. All the convolutional layers are with kernel size equal to             testing set is normalized using the same values. The data augmenthree, stride equal to one, and number of filters equal to 64.                tation includes random crop, random horizontal flip, and cutout,
   All the default architectures share the same fully-connected               which can improve the robustness of the trained model.
layers design. After all the convolutional layers, the output tensor
passes through a global average pooling layer followed by a dropout           D    PERFORMANCE ESTIMATION
layer, a fully-connected layer of 64 neurons, a ReLU layer, another
                                                                              During the observation phase, we need to estimate the perforfully-connected layer, and a softmax layer.
                                                                              mance of a neural architecture to update the Gaussian process
                                                                              model in Bayesian optimization. Since the quality of the observed
B    NETWORK MORPHISM IMPLEMENTATION                                          performances of the neural architectures is essential to the neural
The implementation of the network morphism is introduced from                 architecture search algorithm, we propose to train the neural artwo aspects. First, we describe how the new weights are initial-              chitectures instead of using the performance estimation strategies
ized. Second, we introduce a pool of possible operations which the            used in literatures [5, 14, 37]. The quality of the observations is
Bayesian optimizer can select from, e.g. the possible start and end           essential to the neural architecture search algorithm. So the neural
points of a skip connection.                                                  architectures are trained during the search in our proposed method.
   The four network morphism operations all involve adding new                    There two important requirements for the training process. First,
weights during inserting new layers and expanding existing lay-               it needs to be adaptive to different architectures. Different neuers. We initialize the newly added weights with zeros. However,               ral networks require different numbers of epochs in training to
it would create a symmetry prohibiting the newly added weights                converge. Second, it should not be affected by the noise in the
to learn different values during backpropagation. We follow the               performance curve. The final metric value, e.g., mean squared er-
Net2Net [10] to add noise to break the symmetry. The amount of                ror or accuracy, on the validation set is not the best performance
noise added is the largest noise possible not changing the output.            estimation since there is random noise in it.
   There are a large amount of possible network morphism op-                      To be adaptive to architectures of different sizes, we use the same
erations we can choose. Although there are only four types of                 strategy as the early stop criterion in the multi-layer perceptron
operations we can choose, a parameter of the operation can be set             algorithm in Scikit-Learn [36]. It sets a maximum threshold τ . If the
to a large number of different values. For example, when we use the           loss of the validation set does not decrease in τ epochs, the training
deep(G, u) operation, we need to choose the location u to insert the          stops. Comparing with the methods using a fixed number of training
layer. In the tree-structured search, we actually cannot exhaust all          epochs, it is more adaptive to different neural architectures.
the operations to get all the children. We will keep sampling from                To avoid being affected by the noise in the performance, the
the possible operations until we reach eight children for a node.             mean of metric values of the last τ epochs on the validation set is
For the sampling, we randomly sample an operation from deep,                  used as the estimated performance for the given neural architecture.
wide and skip (add and concat), with equally likely probability. The          It is more accurate than the final metric value on the validation set.

                                                                       1955

Applied Data Science Track Paper                                                                                           KDD ’19, August 4–8, 2019, Anchorage, AK, USA

Auto-Keras: An Efficient Neural Architecture Search System                                                                  KDD ’19, August 4–8, 2019, Anchorage, AK, USA

E     VALIDITY OF THE KERNEL                                                                                From the definition of Dl (·, ·), with the current matching func-
Theorem 1. d(fa , fb ) is a metric space distance.                                                      tions φl :a→c and φl :c→b , Dl (La , Lc ) = Dl (La1 , Lc1 )+ Dl (La2 , Lc2 )
    Proof of Theorem 1:                                                                                 and Dl (Lc , Lb ) = Dl (Lc1 , Lb1 )+ Dl (Lc2 , Lb2 ). First, ∀la ∈ La1 is
    Theorem 1 is proved by proving the non-negativity, definiteness,                                    matched to lb = φl :c→b (φl :a→c (la )) ∈ Lb . Since the triangle insymmetry, and triangle inequality of d.                                                                 equality property of dl (·, ·), Dl (La1 , Lb1 ) ≤ Dl (La1 , Lc1 )+ Dl (Lc1 ,
    Non-negativity:                                                                                     Lb1 ). Second, the rest of the la ∈ La and lb ∈ Lb are free to match
    ∀f x fy ∈ F , d(f x , fy ) ≥ 0.                                                                     with each other.
    From the definition of w(l) in Equation (6), ∀l, w(l) > 0. ∴                                            Let La21 = { l | φl :a→c (l) , ∅ ∧ φl :c→b (φl :c→a (l)) = ∅},
∀l x ly , dl (l x , ly ) ≥ 0. ∴ ∀L x Ly , Dl (L x , Ly ) ≥ 0. Similarly, ∀s x sy ,                      Lb21 = { l | l = φl :c→b (l ′ ) , ∅, l ′ ∈ Lc2 }, Lc21 = { l | l =
ds (s x , sy ) ≥ 0, and ∀S x Sy , D s (S x , Sy ) ≥ 0. In conclusion, ∀f x fy ∈                         φl :a→c (l ′ ) , ∅, l ′ ∈ La2 }, La22 = La2 − La21 , Lb22 = Lb2 − Lb21 ,
F , d(f x , fy ) ≥ 0.                                                                                   Lc22 = Lc2 − Lc21 .
    Definiteness:                                                                                           From the definition of Dl (·, ·), with the current matching func-
    fa = fb ⇐⇒ d(fa , fb ) = 0 .                                                                        tions φl :a→c and φl :c→b , Dl (La2 , Lc2 ) = Dl (La21 , Lc21 ) +Dl (La22 ,
    fa = fb =⇒ d(fa , fb ) = 0 is trivial. To prove d(fa , fb ) = 0 =⇒                                  Lc22 ) and Dl (Lc2 , Lb2 ) = Dl (Lc22 , Lb21 ) + Dl (Lc21 , Lb22 ).
fa = fb , let d(fa , fb ) = 0. ∵ ∀L x Ly , Dl (L x , Ly ) ≥ 0 and ∀S x Sy ,                                 ∵ Dl (La22 , Lc22 ) + Dl (Lc21 , Lb22 ) ≥ |La2 |
D s (S x , Sy ) ≥ 0. Let La and Lb be the layer sets of fa and fb . Let S a                                 and Dl (La21 , Lc21 ) +Dl (Lc22 , Lb21 ) ≥ |Lb2 |
and Sb be the skip-connection sets of fa and fb .                                                           ∴ Dl (La2 , Lb2 ) ≤ |La2 | + |Lb2 | ≤ Dl (La2 , Lc2 ) + Dl (Lc2 , Lb2 ).
    ∴ Dl (La , Lb ) = 0 and D s (S a , Sb ) = 0. ∵ ∀l x ly , dl (l x , ly ) ≥ 0                             So Dl (La , Lb ) ≤ Dl (La , Lc ) + Dl (Lc , Lb ).
and ∀s x sy , ds (s x , sy ) ≥ 0. ∴ |La | = |Lb |, |S a | = |Sb |, ∀la ∈ La ,                               Similarly, D s (S a , Sb ) ≤ D s (S a , Sc ) + D s (Sc , Sb ).
lb = φl (la ) ∈ Lb , dl (la , lb ) = 0, ∀sa ∈ S a , sb = φ s (sa ) ∈ Sb ,                                   Finally, ∀f x fy fz ∈ F , d(f x , fy ) ≤ d(f x , fz ) + d(fz , fy ).
ds (sa , sb ) = 0. According to Equation (6), each of the layers in fa                                      In conclusion, d(fa , fb ) is a metric space distance.                   □
has the same width as the matched layer in fb , According to the                                            Theorem 2. κ(fa , fb ) is a valid kernel.
restrictions of φl (·), the matched layers are in the same order, and all                                   Proof of Theorem 2: The kernel matrix of generalized RBF ker-
                                                                                                        nel in the form of e −γ D (x,y) is positive definite if and only if there
                                                                                                                                      2
the layers are matched, i.e. the layers of the two networks are exactly
the same. Similarly, the skip-connections in the two neural networks                                    is an isometric embedding in Euclidean space for the metric space
are exactly the same. ∴ fa = fb . So d(fa , fb ) = 0 =⇒ fa = fb , let                                   with metric D [18]. Any finite metric space distance can be isometd(fa , fb ) = 0. Finally, fa = fb ⇐⇒ d(fa , fb ) .                                                      rically embedded into Euclidean space by changing the scale of the
    Symmetry:                                                                                           distance measurement [34]. By using Bourgain theorem [4], metric
    ∀f x fy ∈ F , d(f x , fy ) = d(fy , f x ).                                                          space d is embedded to Euclidean space with distortion. ρ(d(fa , fb ))
                                                                                                        is the embedded distance for d(fa , fb ). Therefore, e −ρ (d(fa , fb )) is
                                                                                                                                                                           2
    Let fa and fb be two neural networks in F , Let La and Lb be the
layer sets of fa and fb . If |La | , |Lb |, Dl (La , Lb ) = Dl (Lb , La ) since                         always positive definite. So κ(fa , fb ) is a valid kernel.                  □
it will always swap La and Lb if La has more layers. If |La | = |Lb |,
Dl (La , Lb ) = Dl (Lb , La ) since φl (·) is undirected, and dl (·, ·) is                              F    DISTANCE DISTORTION
symmetric. Similarly, D s (·, ·) is symmetric. In conclusion, ∀f x fy ∈                                 In this section, we introduce how Bourgain theorem is used to
F , d(f x , fy ) = d(fy , f x ).                                                                        distort the learned calculated edit-distance into an isometrically
    Triangle Inequality:                                                                                embeddable distance for Euclidean space in the Bayesian optimiza-
    ∀f x fy fz ∈ F , d(f x , fy ) ≤ d(f x , fz ) + d(fz , fy ).                                         tion process.
    Let l x , ly , lz be neural network layers of any width. If w(l x ) <                                  From Bourgain theorem, a Bourgain embedding algorithm is
                                        w (ly )−w (l x )      w (l x )+w (ly )
w(ly ) < w(lz ), dl (l x , ly ) =           w (ly )      = 2−      w (ly )          ≤ 2−                designed. The input for the algorithm is a metric distance matrix.
w (l x )+w (ly )                                                                                        Here we use the edit-distance matrix of neural architectures. The
     w (l z )      = dl (l x , lz ) + dl (lz , ly ). If w(l x ) ≤ w(lz ) ≤ w(ly ),                      outputs of the algorithm are some vectors in Euclidean space cor-
                  w (ly )−w (l x )   w (ly )−w (l z ) w (l z )−w (l x )     w (ly )−w (l z )            responding to the instances. In our case, the instances are neural
dl (l x , ly ) =       w (ly )     =      w (ly )     + w (l )          ≤       w (ly )      +
                                                               y
w (l z )−w (l x )
                                                                                                        architectures. From these vectors, we can calculate a new distance
     w (l z )      = dl (l x , lz ) + dl (lz , ly ). If w(lz ) ≤ w(l x ) ≤ w(ly ),                      matrix using Euclidean distance. The objective of calculating these
                  w (ly )−w (l x )         w (l )      w (l )           w (l )     w (l )
dl (l x , ly ) =       w (ly )     = 2 − w (ly ) − w (lx ) ≤ 2 − w (l z ) − w (lx ) ≤                   vectors is to minimize the difference between the new distance
                                               y           y                x          y
      w (l )      w (l )
                                                                                                        matrix and the input distance matrix, i.e., minimize the distortions
2 − w (l z ) − w (l z ) = dl (l x , lz ) + dl (lz , ly ). By the symmetry property                      on the distances.
           x          y
of dl (·, ·), the rest of the orders of w(l x ), w(ly ) and w(lz ) also satisfy                            We apply this Bourgain algorithm during the update process of
the triangle inequality. ∴ ∀l x ly lz , dl (l x , ly ) ≤ dl (l x , lz ) +dl (lz , ly ).                 the Bayesian optimization. The edit-distance matrix of previous
    ∀La Lb Lc , given φl :a→c and φl :c→b used to compute Dl (La ,                                      training examples, i.e., the neural architectures, is stored in memory.
Lc ) and Dl (Lc , Lb ), we are able to construct φl :a→b to compute                                     Whenever new examples are used to train the Bayesian optimiza-
Dl (La , Lb ) satisfies Dl (La , Lb ) ≤ Dl (La , Lc ) + Dl (Lc , Lb ).                                  tion, the edit-distance is expanded to include the new distances.
    Let La1 = { l | φl :a→c (l) , ∅ ∧ φl :c→b (φl :c→a (l)) , ∅}.                                       The distorted distance matrix is computed using Bourgain algo-
Lb1 = { l | l = φl :c→b (φl :a→c (l ′ )), l ′ ∈ La1 }, Lc1 = { l | l =                                  rithm from the expanded edit-distance matrix. It is isometrically
φl :a→c (l ′ ) , ∅, l ′ ∈ La1 }, La2 = La − La1 , Lb2 = Lb − Lb1 ,                                      embeddable to the Euclidean space. The kernel matrix computed
Lc2 = Lc − Lc1 .                                                                                        using the distorted distance matrix is a valid kernel.

                                                                                                 1956
