---
citation_key: "BischlEtAl2023"
title: "Hyperparameter optimization: Foundations, algorithms, best practices, and open challenges"
authors: "Bernd Bischl; Martin Binder; Michel Lang; Tobias Pielok; Jakob Richter; Stefan Coors; Janek Thomas; Theresa Ullmann; Marc Becker; Anne‐Laure Boulesteix; Difan Deng; Marius Lindauer"
year: 2023
doi: "10.1002/widm.1484"
source: "arXiv (2107.05847, preprint of Wiley WIREs version)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
long_paper: true
conversion: "pdftotext -layout (automated)"
---

# Hyperparameter optimization: Foundations, algorithms, best practices, and open challenges

Hyperparameter Optimization: Foundations, Algorithms,
                                           Best Practices and Open Challenges
                                           Bernd Bischl1,* , Martin Binder1 , Michel Lang1,2 , Tobias Pielok1 , Jakob Richter1,2 ,

arXiv:2107.05847v3 [stat.ML] 24 Nov 2021
                                           Stefan Coors1 , Janek Thomas1 , Theresa Ullmann3 , Marc Becker1 , Anne-Laure
                                           Boulesteix4 , Difan Deng5 , and Marius Lindauer5
                                           1
                                             Department of Statistics, Ludwig-Maximilians-Universität München -
                                           first.last@stat.uni-muenchen.de
                                           2
                                             Department of Statistics, TU Dortmund University -
                                           last@statistik.tu-dortmund.de
                                           3
                                             Institute for Medical Information Processing, Biometry and Epidemiology,
                                           Ludwig-Maximilians-Universität München - tullmann@ibe.med.uni-muenchen.de
                                           4
                                             Institute for Medical Information Processing, Biometry and Epidemiology,
                                           Ludwig-Maximilians-Universität München - last@ibe.med.uni-muenchen.de
                                           5
                                             Institute for Information Processing, Leibniz University Hannover -
                                           last@tnt.uni-hannover.de
                                           *
                                             Corresponding Author

                                           Abstract
                                           Most machine learning algorithms are configured by a set of hyperparameters whose values must be carefully
                                           chosen and which often considerably impact performance. To avoid a time-consuming and irreproducible manual
                                           process of trial-and-error to find well-performing hyperparameter configurations, various automatic hyperparam-
                                           eter optimization (HPO) methods – e.g., based on resampling error estimation for supervised machine learning –
                                           can be employed. After introducing HPO from a general perspective, this paper reviews important HPO meth-
                                           ods, from simple techniques such as grid or random search to more advanced methods like evolution strategies,
                                           Bayesian optimization, Hyperband and racing. This work gives practical recommendations regarding important
                                           choices to be made when conducting HPO, including the HPO algorithms themselves, performance evaluation,
                                           how to combine HPO with machine learning pipelines, runtime improvements, and parallelization. This work is
                                           accompanied by an appendix that contains information on specific software packages in R and Python, as well
                                           as information and recommended hyperparameter search spaces for specific learning algorithms. We also provide
                                           notebooks that demonstrate concepts from this work as supplementary files.

                                           1     Introduction
                                           Machine learning (ML) algorithms are highly configurable by their hyperparameters (HPs). These
                                           parameters often substantially influence the complexity, behavior, speed as well as other aspects

                                                                                                 1

of the learner, and their values must be selected with care in order to achieve optimal performance. Human trial-and-error to select these values is time-consuming, often somewhat biased,
error-prone and computationally irreproducible.
As the mathematical formalization of hyperparameter optimization (HPO) is essentially blackbox optimization, often in a higher-dimensional space, this is better delegated to appropriate
algorithms and machines to increase efficiency and ensure reproducibility. Many HPO methods
have been developed to assist in and automate the search for well-performing hyperparameter
configuration (HPCs) over the last 20 to 30 years. However, more sophisticated HPO approaches
in particular are not as widely used as they could (or should) be in practice. We postulate that
the reason for this may be a combination of the following factors:
   • poor understanding of HPO methods by potential users, who may perceive them as (too)
     complex “black-boxes”;
   • poor confidence of potential users in the superiority of HPO methods over trivial approaches
     and resulting skepticism of the expected return on (time) investment;
   • missing guidance on the choice and configuration of HPO methods for the problem at
     hand;
   • difficulty to define the search space of an HPO process appropriately.
With these obstacles in mind, this paper formally and algorithmically introduces HPO, with
many hints for practical application. Our target audience are scientists and users with a basic
knowledge of ML and evaluation.
In this article, we mainly discuss HP for supervised ML, which is arguably the default scenario for
HPO. We mainly do this to keep notation simple and to not overwhelm less experienced readers,
especially for less experienced readers. Nevertheless, all covered techniques can be applied to
practically any algorithm in ML in which the algorithm is trained on a collection of instances
and performance is quantitatively measurable – e.g., in semi-supervised learning, reinforcement
learning, and potentially even unsupervised learning1 .
Subsequent sections of this paper are organized as follows: Section 2 discusses related work.
Section 3 introduces the concept of supervised ML and discusses the evaluation of ML algorithms.
The principle of HPO is introduced in Section 4. Major classes of HPO methods are described,
including their strengths and limitations. The problem of over-tuning, the handling of noise in the
context of HPO, and the topic of threshold tuning are also addressed. Section 5 introduces the
most common preprocessing steps and the concept of ML pipelines, which enables us to include
preprocessing and model selection within HPO. Section 6 offers practical recommendations on
how to choose resampling strategies as well as define tuning search spaces, provides guidance
on which HPO algorithm to use, and describes how HPO can be parallelized. In Section 7, we
also briefly discuss how HPO directly connects to a much broader field of algorithm selection
and configuration beyond ML and other related fields. Section 8 concludes with a discussion of
relevant open issues in HPO.
The appendices contain additional material of particularly practical relevance for HPO: Appendix A lists the most popular ML algorithms, describes some of their properties, and proposes
sensible HP search spaces; Appendix B does the same for preprocessing methods; Appendix C
contains a table of common evaluation metrics; Appendix D lists relevant considerations and
  1 where the measurement of performance is arguably much less straightforward, especially via a single metric.

                                                      2

software packages for ML and HPO in the two popular ML scripting languages R (D.1) and
Python (D.2). Furthermore, we provide several R markdown notebooks as ancillary files which
demonstrate many practical HPO concepts and implement them in mlr3 (Lang et al., 2019).

2     Related Work
As one of the most studied sub-fields of automated ML (AutoML), there exist several previous
surveys on HPO. Feurer and Hutter (2019) offered a thorough overview about existing HPO
approaches, open challenges, and future research directions. In contrast to our paper, however,
that work does not focus on specific advice for issues that arise in practice. Yang and Shami
(2020) provide a very high-level overview of search spaces, HPO techniques, and tools. Although
we expect that the paper by Yang and Shami (2020) will be a more accessible paper for first-time
users of HPO compared to the survey by Feurer and Hutter (2019), it does not explain HPO’s
mathematical and algorithmic details or practical tips on how to apply HPO efficiently. Last
but not least, Andonie (2019) provides an overview about HPO methods, but with a focus on
computational complexity aspects. We see this work described here as filling the gap between
these papers by providing all necessary details both for first-time users of HPO as well as experts
in ML and data science who seek to understand the concepts of HPO in sufficient depth.
Our focus is on providing a general overview of HPO without a special focus on concrete ML
model classes. However, since the ML field has many large sub-communities by now, there
are also several specialized HPO and AutoML surveys. For example, He et al. (2021a) and
Talbi (2020) focus on AutoML for deep learning models, Khalid and Javaid (2020) on HPO for
forecasting models in smart grids, and Zhang et al. (2021) on AutoML on graph models. Bartz
et al. (2021) investigate model-based HPO and also give search spaces and examples for many
specific ML algorithms. Other more general reviews of AutoML are Yao et al. (2019), Elshawi
et al. (2019), and Yu and Zhu (2020).

3     Supervised Machine Learning
3.1     Terminology and Notations
Supervised ML addresses the problem of inferring a model from labeled training data that is
then used to predict data from the same underlying distribution with minimal error. Let D
be a labeled data set with n observations, where each observation (x(i) , y (i) ) consists of a
p-dimensional feature vector2 x(i) ∈ X                      (i)
                                          and its label y ∈ Y. Hence, we define the data
   3           (1) (1)           (n) (n)
set D = x , y            ,..., x ,y        . We assume that D has been sampled i.i.d. from an
underlying, unknown distribution, so D ∼ (Pxy )n . Each dimension of a p-dimensional x will
usually be of numerical, integer, or categorical type. While some ML algorithms can handle all of
these data types natively (e.g., tree-based methods), others can only work on numeric features
and require encoding techniques for categorical types. The most common supervised ML tasks
are regression and classification, where Y = R for regression and Y is finite and categorical for
classification with |Y| = g classes. Although we mainly discuss HPO in the context of regression
and classification, all covered concepts easily generalize to other supervised ML tasks, such as
Poisson regression, survival analysis, cost-sensitive classification, multi-output tasks, and many
more. An ML model is a function f : X → Rg that assigns a prediction in Rg to a feature vector
  2 More generally, with a slight abuse of notation, the feature vector x(i) could be taken as a tensor (for example

when the feature is an image).
  3 More precisely, D is an indexed tuple, but we will continue to use common terminology and call it a data set.

                                                         3

from X . For regression, g is 1, while in classification the output represents the g decision scores
or posterior probabilities of the g candidate classes. Binary classification is usually simplified to
g = 1, with a single decision score in R or only the posterior probability for the positive class.
The function space – usually parameterized – to which a model belongs is called the hypothesis
space and denoted as H.
The goal of supervised ML is to fit a model given n observations sampled from Pxy , so that it
generalizes well to new observations from the same data generating process. Formally, an ML
learner or inducer I configured by HPs λ ∈ Λ maps a data set D to a model fˆ or equivalently
to its associated parameter vector θ̂, i.e.,
                                I : D × Λ → H, (D, λ) 7→ fˆ,                                   (1)
             S
where D := n∈N (X × Y)n is the set of all data sets. While model parameters θ̂ are an output
of the learner I, HPs λ are an input. We also write Iλ for I or fˆD,λ for fˆ if we want to
stress that the inducer was configured with λ or that the model was learned on D by an inducer
configured by λ. A loss function L : Y × Rg → R+         0 measures the discrepancy between the
prediction and the true label. Many ML learners use the concept of empirical risk minimization
(ERM) in their training routine to produce their fitted model fˆ, i.e., they optimize Remp (f ) or
Remp (θ) over all candidate models f ∈ H
                                n
                                X                    
                   Remp (f ) :=    L y (i) , f (x(i) ) ,   fˆ = arg min Remp (f )              (2)
                                 i=1                                      f

on the training data D (c.f. Figure 1). This empirical risk is only a stochastic proxy for what
we are actually interested in, namely the theoretical risk or true generalization error R(f ) :=
E(x,y)∼Pxy [L (y, f (x))]. For many complex hypothesis spaces, Remp (f ) can become considerably
smaller than its true risk R(f ). This phenomenon is known as overfitting, which in ML is usually
addressed by either constraining the hypothesis space or regularized risk minimization, i.e., adding
a complexity penalty J(θ) to (2).

3.2    Evaluation of ML Algorithms
After training an ML model fˆ, a natural follow-up step is to evaluate its future performance
given new, unseen data. We seek to use an unbiased, high-quality statistical estimator, which
numerically quantifies the performance of our model when it is used to predict the target variable
for new observations drawn from the same data-generating process Pxy .
3.2.1 Performance Metrics
A general performance measure ρ for an arbitrary data set of size m is defined as a two-argument
function that maps the m-size vector of true labels y and the m × g matrix of prediction scores
F to a scalar performance value:
                         [                  
                     ρ:        Y m × Rm×g → R, (y, F ) 7→ ρ(y, F ).                          (3)
                           m∈N

This more general set-based definition is needed for performance measures – such as area under
the ROC curve (AUC) – or for most measures from survival time analysis, where loss values
cannot be computed with respect to only a single observation. For usual point-wise losses
L (y, f (x)), we can simply extend L to ρ by averaging over the size-m set used for testing:
                                                    m
                                               1 X
                                  ρL (y, F ) =       L(y (i) , F (i) ),                          (4)
                                               m i=1

                                                   4

where F (i) is the i-th row of F ; this corresponds to estimating the theoretical risk R(f ) corresponding to the given loss. Popular performance metrics corresponding to different loss functions
can be found in Table 8 in Appendix C.
Furthermore, the introduction of ρ allows the evaluation of a learner with respect to a different
performance metric than the loss used for risk minimization. Because of this, we call the loss
used in (2) inner loss, and ρ the outer performance measure or outer loss 4 . Both can coincide,
but quite often we select an outer performance measure based on the prediction task we would
like to solve, and opt to approximate this metric with a computationally cheaper and possibly
differentiable version during inner risk minimization.
3.2.2 Generalization Error
Due to potential overfitting, every predictive model should be evaluated on unseen test data to
ensure unbiased performance estimation. Assuming (for now) dedicated train and test data sets
Dtrain and Dtest of sizes ntrain and ntest , respectively, we define the generalization error of a
learner I with HPs λ trained on ntrain observations, w.r.t. measure ρ as
                                                                                        
           GE(I, λ, ntrain , ρ) := lim EDtrain ,Dtest ∼Pxy ρ ytest , FDtest ,I(Dtrain ,λ) ,    (5)
                                       ntest →∞

where we take the expectation over the data sets Dtrain and Dtest , both i.i.d. from Pxy , and
FDtest ,I(Dtrain ,λ) is the matrix of predictions when the model is trained on Dtrain and predicts on
Dtest . Note that in the simpler and common case of a point-wise loss L (y, f (x)), the above
trivially reduces to the more common form

                     GE(I, λ, ntrain , ρL ) = EDtrain ,(x,y)∼Pxy [L(y, Iλ (Dtrain )(x))]                      (6)

with expectation over data set Dtrain and test sample (x, y), both independently sampled from
Pxy . This corresponds to the expectation of R(f ) – which references a given, fixed model –
over all possible models fitted to different realizations of Dtrain of size ntrain (see Figure 1).
3.2.3 Data splitting and Resampling
The generalization error must usually be estimated from a single given data set D. For a simple
estimator based on a single random split, Dtrain and Dtest can be represented as index vectors
Jtrain ∈ {1, . . . , n}ntrain and Jtest ∈ {1, . . . , n}ntest , which usually partition the data set. For an
   4 Surrogate loss for the inner loss and target loss for the outer loss are also commonly used terminologies.

                                                                            Dtest

                        Dtrain              I               fˆ/θ̂

                                   minθ Remp (θ)                    ρ(yJ test , FJ test )

Figure 1: Learner I takes input data, performs ERM, and returns model fˆ and its parameters
θ̂. The GE of fˆ is evaluated on the fresh test set Dtest .

                                                        5

index vector J of length m, one can define the corresponding vector of labels yJ ∈ Y m , and the
corresponding matrix of prediction scores FJ,f ∈ Rm×g for a model f . The holdout estimator
is then:
                    dJ
                    GE                (I, λ, ntrain , ρ) = ρ(yJtest , FJtest ,I(Dtrain ,λ) ). (7)
                         train ,Jtest

The holdout approach has the following trade-off: (i) Because ntrain must be smaller than n, the
estimator is a pessimistically biased estimator of GE(I, λ, n, ρ), as we do not use all available
data for training. In a certain sense, we are estimating with respect to the wrong training set
size. (ii) If Dtrain is large, Dtest will be small, and the estimator (7) has a large variance. This
trade-off not only depends on relative sizes of ntrain and ntest , but also the absolute number of
observations, as learning with respect to ntrain sample size and test error estimation based on
ntest samples both show a saturating effect for larger sample sizes. However, a typical rule of
thumb is to choose ntrain = 32 n (Kohavi, 1995; Dobbin & Simon, 2011).
Resampling methods offer a partial solution to this dilemma. These methods repeatedly split the
available data into training and test sets, then apply an estimator (7) for each of these, and finally
aggregate over all obtained ρ performance values. Formally, we can identify a resampling strategy
with a vector of corresponding splits, i.e., J = ((Jtrain,1 , Jtest,1 ), . . . , (Jtrain,B , Jtest,B )) , where
Jtrain,i , Jtest,i are index vectors and B is the number of splits. Hence, the estimator for Eq. (5)
is:
       d J , ρ, λ) =
       GE(I,
                                                                                                           
              dJ                                                 d
       = agr GE train,1 ,Jtest,1 (I, λ, |Jtrain,1 |, ρ), . . . , GEJtrain,B ,Jtest,B (I, λ, |Jtrain,B |, ρ)     (8)
                                                                                                
       = agr ρ yJtest,1 , FJtest,1 ,I(Dtrain,1 ,λ) , . . . , ρ yJtest,B , FJtest,B ,I(Dtrain,B ,λ) ,

where the aggregator agr is often chosen to be the mean. For Eq. (8) to be a valid estimator of
Eq. (6), we must specify to what ntrain training set size an estimator refers in GE(I, λ, ntrain , ρ).
As the training set sizes can be different during resampling (they usually do not vary much), it
should at least hold that ntrain ≈ ntrain,1 ≈ · · · ≈ ntrain,B , and we could take the average for
                                                PB
such a required reference size with ntrain = B1 j=1 ntrain,j .
Resampling uses the data more efficiently than a single holdout split, as the repeated estimation
and averaging over multiple splits results in an estimate of generalization error with lower variance
(Kohavi, 1995; Simon, 2007). Additionally, the pessimistic bias of simple holdout is also kept
to a minimum and can be reduced to nearly 0 by choosing training sets of size close to n.
The most widely-used resampling technique is arguably k-fold-cross-validation (CV), which
partitions the available data in k subsets of approximately equal size, and uses each partition
to evaluate a model fitted on its complement. For small data sets, it makes sense to repeat
CV with multiple random partitions and to average the resulting estimates in order to average
out the variability, which results in repeated k-fold-cross-validation. Furthermore, note that
performance values generated from resampling splits and especially CV splits are not statistically
independent because of their overlapping traing sets, so the variance of d     GE(I, J , ρ, λ) is not
proportional to 1/B. Somewhat paradoxically, a leave-one-out strategy is not the optimal choice,
and repeated cross-validation with many (but fewer than n) folds and many repetitions is often
a better choice (Bengio & Grandvalet, 2004). An overview of existing resampling techniques
can be found in Bischl et al. (2012) or Boulesteix et al. (2008).

                                                         6

4     Hyperparameter Optimization
4.1     HPO Problem Definition
Most learners are highly configurable by HPs, and their generalization performance usually depends on this configuration in a non-trivial and subtle way. HPO algorithms automatically
identify a well-performing HPC λ ∈ Λ̃ for an ML algorithm Iλ . The search space Λ̃ ⊂ Λ
contains all considered HPs for optimization and their respective ranges:

                                          Λ̃ = Λ̃1 × Λ̃2 × · · · × Λ̃l ,                                    (9)

where Λ̃i is a bounded subset of the domain of the i-th HP Λi , and can be either continuous,
discrete, or categorical. This already mixed search space can also contain dependent HPs, leading
to a hierarchical search space: An HP λi is said to be conditional on λj if λi is only active when
λj is an element of a given subset of Λj and inactive otherwise, i.e., not affecting the resulting
learner (Thornton et al., 2013). Common examples are kernel HPs of a kernelized machine such
as the SVM, when we tune over the kernel type and its respective hyperparameters as well. Such
conditional HPs usually introduce tree-like dependencies in the search space, and may in general
lead to dependencies that may be represented by directed acyclic graphs.
The general HPO problem as visualized in Figure 2 is defined as:
                                                      d J , ρ, λ)
                          λ∗ ∈ arg min c(λ) = arg min GE(I,                                               (10)
                                   λ∈Λ̃                λ∈Λ̃

where λ∗ denotes the theoretical optimum, and c(λ) is a shorthand for the estimated generalization error when I, J , ρ are fixed. We therefore estimate and optimize the general-
              d J , ρ, λ) of a learner Iλ , w.r.t. an HPC λ, based on a resampling split
ization error GE(I,
J = ((Jtrain,1 , Jtest,1 ), . . . , (Jtrain,B , Jtest,B )).5 Note that c(λ) is a black-box, as it usually
has no closed-form mathematical representation, and hence no analytic gradient information is
available. Furthermore, the evaluation of c(λ) can take a significant amount of time. Therefore,
the minimization of c(λ) forms an expensive black-box optimization problem.
Taken together, these properties define an optimization problem of considerable difficulty. Furthermore, they rule out many popular optimization methods that require gradients or entirely
numerical search spaces or that must perform a large number of evaluations to converge to a
                                                                               d J , ρ, λ),
well-performing solution, like many meta-heuristics. Furthermore, as c(λ) = GE(I,
which is defined via resampling and evaluates λ on randomly chosen validation sets, c should
be considered a stochastic objective – although many HPO algorithms may ignore this fact or
simply handle it by assuming that we average out the randomness through enough resampling
replications.
We can thus define the HP tuner τ : (D, I, Λ̃, ρ) 7→ λ̂ that proposes its estimate λ̂ of the
true optimal configuration λ∗ given a dataset D, an inducer I with corresponding search space
Λ̃ to optimize, and a target measure ρ. The specific resampling splits J used can either be
passed into τ as well or are internally handled to facilitate adaptive splitting or multi-fidelity
optimization (e.g., as done in Klein et al., 2017b).
   5 Note again that optimizing the resampling error will result in biased estimates, which are problematic when

reporting the generalization error; use nested CV for this, see Section 4.4.

                                                        7

                                                                        Input
                                                                        Data D, Inducer I, metric ρ,
                                                                          splits J , search space Λ̃
      Repeated by Resampling
                                                                                                      Prop                      λ+
                                                                                                          ose
              λ1 , λ2 , λ3 , . . .                  Dtest                                                     n
                                                                                                                    ew

                                                                     Update Optim
                                                                                                                              C
                                                                                                                            HP

   Dtrain             Iλ             fˆ/θ̂                                       ize           HPO

                                                                                    r          Loop

                                                                                                                            g
                                                                                                                    lin
             minθ Remp (θ)                   ρ(yJ test , FJ test )                                                  m
                                                                                                                        p
                                                                                        Eva                e   sa
                                                                                              lu ate b y R                      c(λ+)
                                                                                        Return
                                                                                         HPC λ̂

                      Figure 2: General HPO loop with inner risk minimization.

4.2     Well-Established HPO Algorithms
All HPO algorithms presented here work by the same principle: they iteratively propose HPCs λ+
and then evaluate the performance on these configurations. We store these HPs and their respective evaluations successively in the so-called archive A = ((λ(1) , c(λ(1) )), (λ(2) , c(λ(2) )), . . . ),
with A[t+1] = A[t] ∪ (λ+ , c(λ+ )) if a single point is proposed by the tuner.
Many algorithms can be characterized by how they handle two different trade-offs: a) The
exploration vs. exploitation trade-off refers to how much budget an optimizer spends on either
attempting to directly exploit the currently available knowledge base by evaluating very close to
the currently best candidates (e.g., local search) or exploring the search space to gather new
knowledge (e.g., random search). b) The inference vs. search trade-off refers to how much time
and overhead is spent to induce a model from the currently available archive data in order to
exploit past evaluations as much as possible. Other relevant aspects that HPO algorithms differ
in are: Parallelizability, i.e., how many configurations a tuner can (reasonably) propose at the
same time; global vs. local behavior of the optimizer, i.e., if updates are always quite close to
already evaluated configurations; noise handling, i.e., if the optimizer takes into account that the
estimated generalization error is noisy; multifidelity, i.e., if the tuner uses cheaper evaluations,
for example on smaller subsets of the data, to infer performance on the full data; search space
complexity, i.e., if and how hierarchical search spaces as introduced in Section 5 can be handled.
4.2.1 Grid Search and Random Search
Grid search (GS) is the process of discretizing the range of each HP and exhaustively evaluating
every combination of values. Numeric and integer HP values are usually equidistantly spaced
in their box constraints. The number of distinct values per HP is called the resolution of the
grid. For categorical HPs, either a subset or all possible values are considered. A second simple
HPO algorithm is random search (RS). In its simplest form, values for each HP are drawn
independently of each other and from a pre-specified (often uniform) distribution, which works
for (box-constrained) numeric, integer, or categorical parameters (c.f. Figure 3). Due to their
simplicity, both GS and RS can handle hierarchical search spaces.

                                                       8

RS often has much better performance than GS in higher-dimensional HPO settings (Bergstra
& Bengio, 2012b). GS suffers directly from the curse of dimensionality (Bellman, 2015), as the
required number of evaluations increases exponentially with the number of HPs for a fixed grid
resolution. This seems to be true as well for RS at first glance, and we certainly require an
exponential number of points in dim(Λ̃) to cover the space well. However, in practice, HPO
problems often have low effective dimensionality (Bergstra & Bengio, 2012b): The set of HPs
that have an influence on performance is often a small subset of all available HPs. Consider
the example illustrated in Figure 3, where an HPO problem with HPs λ1 and λ2 is shown. A
GS with resolution 3 resulting in 9 HPCs is evaluated, and we discover that only HP λ1 has
any relevant influence on the performance, so only 3 of 9 evaluations provided any meaningful
information. In comparison, RS would have given us 9 different configurations for HP λ1 , which
results in a higher chance of finding the optimum. Another advantage of RS is that it can easily
be extended by further samples; in contrast, the number of points on a grid must be specified
beforehand, and refining the resolution of GS afterwards is more complicated. Altogether, this
makes RS preferable to GS and a surprisingly strong baseline for HPO in many practical settings.
Notably, there are sampling methods that attempt to cover the search space more evenly than
the uniform sampling of RS, e.g., Latin Hypercube Sampling (McKay et al., 1979), or Sobol
sequences (Antonov & Saleev, 1979). However, these do not seem to significantly outperform
naive i.i.d. sampling (Bergstra & Bengio, 2012b).

                      Grid Search                                     Random Search

     λ2                                                λ2

                             λ1                                                λ1

Figure 3: RS and GS where only HP λ1 has a strong influence on c (figure based on Bergstra
and Bengio (2012a)).

4.2.2 Evolution Strategies
Evolution strategies (ES) are a class of stochastic population-based optimization methods inspired by the concepts of biological evolution, belonging to the larger class of evolutionary
algorithms. They do not require gradients, making them generally applicable in black-box settings such as HPO. In ES terminology, an individual is a single HPC, the population is a currently
maintained set of HPCs, and the fitness of an individual is its (inverted) generalization error c(λ).
Mutation is the (randomized) change of one or a few HP values in a configuration. Crossover
creates a new HPC by (randomly) mixing the values of two other configurations. An ES follows

                                                 9

iterative steps to find individuals with high fitness values (c.f. Figure 4): (i) An initial population
is sampled at random. (ii) The fitness of each individual is evaluated. (iii) A set of individuals
is selected as parents for reproduction.6 (iv) The population is enlarged through crossover and
mutation of the parents. (v) The offspring is evaluated. (vi) The top-k fittest individuals are
selected.7 (vii) Steps (ii) to (v) are repeated until a termination condition is reached. For a
more comprehensive introduction to ES, see Beyer and Schwefel (2002).

               INITIALIZATION                                                                                                                       CROSSOVER

                              λ1   λ2    λ3        λ4
                       λ(1)                             c = 0.2

                                                                                                                                                       ⊕
                       λ(2)                             c = 0.8                                                                                                  ...
                                                                                 PARENT SELECTION
                                              ..                                                                                                       =
                   λ(pop)                               c = 0.6                             11   10   9    8    7
                                                                                       12                            6
                                                                                  13                                      5
                                                                             14                                                 4
                                                                     16                                                              3
                                                                   15                                                                    2
                                                                  17                   SELECTION TYPE                                    1                                MUTATION
                                                            p
                                                                  18                          uniform                                        0
                                                         po                                 tournament
                                                   .,✓
                                                                  19                                                                     35
                                                                                              roulette
                                          ,..
                                                                   20                                                                 34
                                                                       21
                                        ✓1                                                                                          33

                                                        STOP?
                                                                            22
                                                                                  23
                                                                                                                          31
                                                                                                                               32
                                                                                                                                                                            ≈        ...
                                                                                         25
                                                                                       24                            30
                                                                                                 26   27   28   29
                              λ1   λ2    λ3        λ4
        ✓       λ(1)                                    c = 0.2
                                              ..
        ✗     λ(pop)                                    c = 0.8

        ✓ λ(pop+1)                                      c = 0.3                                                                          λ(pop+1)               c = 0.3
                                              ..                                                                                                        ..
        ✗ λ(pop+off)                                    c = 0.7                                                                      λ(pop+off)                 c = 0.7

                                    SELECTION                                                                                                         EVALUATION

Figure 4: Schematic representation of a single iteration of an ES as a four dimensional discrete
problem. Parameter values are symbolized by geometric shapes.

ES were limited to numeric spaces in their original formulation, but they can easily be extended
to handle mixed spaces by treating components of different types independently, e.g., by adding
a normally distributed random value to real-valued HPs while adding the difference of two
geometrically distributed values to integer-valued HPs (R. Li et al., 2013). By defining mutation
and crossover operations that operate on tree structures or graphs, it is even possible to perform
optimization of preprocessing pipelines (Olson et al., 2016; Escalante et al., 2009) or neural
network architectures (Real et al., 2019) using evolutionary algorithms. The properties of ES
can be summarized as follows: ES have a low likelihood to get stuck in local minima, especially if
so-called nested ES are used (Beyer & Schwefel, 2002). They can be straightforwardly modified
to be robust to noise (Beyer & Sendhoff, 2006), and can also be easily extended to multiobjective settings (Coello Coello et al., 2007). Additionally, ES can be applied in settings with
complex search spaces and can therefore work with spaces where other optimizers may fail (He
et al., 2021b). ES are more efficient than RS and GS but still often require a large number of
   6 Either completely at random, or with a probability according to their fitness, the most popular variants being

roulette wheel and tournament selection.
   7 In ES-terminology this is called a (µ + λ) elite survival selection, where µ denotes the population size, and

λ the number of offspring; other variants like (µ, λ) selection exist.

                                                                                                  10

iterations to find good solutions, which makes them unsatisfactory for expensive optimization
settings like HPO.
4.2.3 Bayesian Optimization
Bayesian optimization (BO) has become increasingly popular as a global optimization technique
for expensive black-box functions, and specifically for HPO (Jones et al., 1998; Hutter, Hoos,
& Leyton-Brown, 2011; Snoek, Larochelle, & Adams, 2012).
BO is an iterative algorithm whose key strategy is to model the mapping λ 7→ c(λ) based on
observed performance values found in the archive A via (non-linear) regression. This approximating model is called a surrogate model, for which a Gaussian process or a random forest are
typically used. BO starts on an archive A filled with evaluated configurations, typically sampled
randomly, using Latin Hypercube Sampling or the Sobol sampling (Bossek et al., 2020). BO
then uses the archive to fit the surrogate model, which for each λ produces both an estimate
of performance ĉ(λ) as well as an estimate of prediction uncertainty σ̂(λ), which then gives rise
to a predictive distribution for one test HPC or a joint distribution for a set of HPCs. Based
on the predictive distribution, BO establishes a cheap-to-evaluate acquisition function u(λ) that
encodes a trade-off between exploitation and exploration: The former means that the surrogate
model predicts a good, low c value for a candidate HPC λ, while the latter implies that the
surrogate is very uncertain about c(λ), likely because the surrounding area has not been explored
thoroughly.
Instead of working on the true expensive objective, the acquisition function u(λ) is then optimized
in order to generate a new candidate λ+ for evaluation. The optimization problem u(λ) inherits
most characteristics from c(λ); so it is often still multi-modal and defined on a mixed, hierarchical
search space. Therefore, u(λ) may still be quite complex, but it is at least cheap to evaluate.
This allows the usage of more budget-demanding optimizers on the acquisition function. If the
space is real-valued and the combination of surrogate model and acquisition function supports
it, even gradient information can be used.
Among the possible optimization methods are: iterated local search (as used by Hutter et al.
(2009)), evolutionary algorithms (as in White et al. (2021)), ES using derivatives (as used by
Sekhon and Mebane (1998) and Roustant et al. (2012)), and a focusing RS called DIRECT
(Jones, 2009).
The true objective value c(λ+ ) of the proposed HPC λ+ – generated by optimization of u(λ)
– is finally evaluated and added to the archive A. The surrogate model is updated, and BO
iterates until a predefined budget is exhausted, or a different termination criterion is reached.
These steps are summarized in Algorithm 1. BO methods can use different ways of deciding
which λ to return, referred to as the identification step by Jalali et al. (2017). This can either
be the best observed λ during optimization, the best (mean, or quantile) predicted λ from the
archive according to the surrogate model (Picheny et al., 2013; Jalali et al., 2017), or the best
predicted λ overall (Scott et al., 2011). The latter options serve as a way of smoothing the
observed performance values and reducing the influence of noise on the choice of λ̂.

Surrogate model The choice of surrogate model has great influence on BO performance and
is often linked to properties of Λ̃. If Λ̃ is purely real-valued, Gaussian process (GP) regression
(Rasmussen & Williams, 2006) – sometimes referred to as Kriging – is used most often. In its
basic form, BO with a GP does not support HPO with non-numeric or conditional HPs, and tends

                                                 11

 Algorithm 1: BO for a black-box objective c(λ).
 1 Generate λ(1) , . . . , λ(k) with sampling scheme or fixed design
                          [0]
 2 Initialize archive A       = ((λ(1) , c(λ(1) )), . . . , (λ(k) , c(λ(k) )))
 3 for t = 1, 2, 3, . . . until termination do
 4      1: Fit surrogate model (ĉ(λ), σ̂(λ)) on A[t−1]
 5      2: Build acquisition function u(λ) from (ĉ(λ), σ̂(λ))
 6      3: Obtain proposal λ+ by optimizing u: λ+ ∈ arg maxλ∈Λ̃ u(λ)
 7      4: Evaluate c(λ+ )
 8      5: Obtain A[t] by augmenting A[t−1] with (λ+ , c(λ+ ))
 9 end

   Result: λ̂: Best-performing λ from archive or according to surrogates prediction.

to show deteriorating performance when Λ̃ has more than roughly ten dimensions. Dealing with
integer-valued or categorical HPs requires special care (Garrido-Merchán & Hernández-Lobato,
2020). Extensions for mixed-hierarchical spaces that are based on special kernels (Swersky,
Duvenaud, et al., 2014) exist, and the use of random embeddings has been suggested for highdimensional spaces (Wang et al., 2016; Nayebi et al., 2019). Most importantly, standard GPs
have runtime complexity that is cubic in the number of samples, which can result in a significant
overhead when the archive A becomes large.

                True function
                Surrogate
                Uncertainty
                Acquisition
                          A[t−1]                                   σ̂(λ)

                                 ĉ(λ)
c(λ)
                                                 observation

        observation       c(λ)
                                         acquisition max

                            λ+
                                               u(λ)

                                                      λ
Figure 5: Illustration of how BO generates the proposal by maximizing an acquisition function
(figure inspired by Hutter et al. (2019)).

                                                 12

McIntire et al. (2016) propose to use an adapted, sparse GP that restrains training data from
uninteresting areas. Local Bayesian optimization (Eriksson et al., 2019) is implemented in the
TuRBO algorithm and has been successfully applied to various black-box problems.
Random forests, most notably used in SMAC (Hutter, Hoos, & Leyton-Brown, 2011), have
also shown good performance as surrogate models for BO. Their advantage is their native
ability to handle discrete HPs and, with minor modifications, e.g., in Hutter, Hoos, and Leyton-
Brown (2011), even dependent HPs without the need for preprocessing. Standard random forest
implementations are still able to handle dependent HPs by treating infeasible HP values as
missing and performing imputation. Random forests tend to work well with larger archives and
introduce less overhead than GPs. SMAC uses the standard deviation of tree predictions as
a heuristic uncertainty estimate σ̂(λ) (Hutter, Hoos, & Leyton-Brown, 2011). However, more
sophisticated alternatives exist to provide unbiased estimates (Sexton & Laake, 2009). Since
trees are not distance-based spatial models, the uncertainty estimator does not increase the
further we extrapolate away from observed training points. This might be one explanation as to
why tree-based surrogates are outperformed by GP regression on purely numerical search spaces
(Eggensperger et al., 2013).
Neural networks (NNs) have shown good performance in particular with nontrivial input spaces,
and they are thus increasingly considered as surrogate models for BO (Snoek et al., 2015).
Discrete inputs can be handled by one-hot encoding or by automatic techniques, e.g., entity
embedding where a dense representation is learned from the output of a simple, direct encoding,
such as one-hot encoding by the NN. (Hancock & Khoshgoftaar, 2020). NNs offer efficient and
versatile implementations that allow the use of gradients for more efficient optimization of the
acquisition function. Uncertainty bounds on the predictions can be obtained, for example, by
using Bayesian neural networks (BNNs), which combine NNs with a probabilistic model of the
network weights or adaptive basis regression where only a Bayesian linear regressor is added to
the last layer of the NN (Snoek et al., 2015).

Acquisition function The acquisition function balances out the surrogate model’s prediction
ĉ(λ) and its posterior uncertainty σ̂(λ) to ensure both exploration of unexplored regions of Λ̃, as
well as exploitation of regions that have performed well in previous evaluations. A very popular
acquisition function is the expected improvement (EI) (Jones et al., 1998):

             uEI (λ) = EC∼N (ĉ(λ), σ̂(λ)2 ) [max {cmin − C(λ), 0}]
                                                                                 
                                             cmin − ĉ(λ)              cmin − ĉ(λ)            (11)
                     = (cmin − ĉ(λ)) Φ                     + σ̂(λ)φ                  ,
                                                σ̂(λ)                     σ̂(λ)

where cmin denotes the best observed outcome of c so far, and Φ and φ are the cumulative
distribution function and density of the standard normal distribution, respectively. The EI was
introduced in connection with GPs that have a Bayesian interpretation, expressing the posterior
distribution of the true performance value given already observed values as a Gaussian random
variable C(λ) with C(λ) ∼ N (ĉ(λ), σ̂(λ)2 ). Under this condition, Eq. (11) can be analytically
expressed as above, and the resulting formula is often heuristically applied to other surrogates
that supply ĉ(λ) and σ̂(λ).
A further, very simple acquisition function is the lower confidence bound (LCB) (Jones, 2001):

                              uLCB (λ) = (−1) · (ĉ(λ) − κ · σ̂(λ)) ,                          (12)

                                                13

here negated to yield a maximization problem for Algorithm 1. The LCB treats local uncertainty
as an additive bonus at each λ to enforce exploration, with κ being a control parameter that is
not easy to set.

Adaptive Explore-Exploit Tradeoffs In a theoretical analysis without a limit on evaluations,
Srinivas et al. (2010) suggest to increase the κ-parameter of LCB over time to encourage exploration in later phases of optimization. However, in the context of practical HPO with a finite
budget for evaluations, it seems plausible to decrease κ over time to enforce exploration in the
beginning and exploitation at the end in a similar cooldown scheme as in simulated annealing,
such as e.g. suggested by Zheng et al. (2016), which uses a further cyclical scheme to escape local
minima. Sasena et al. (2002) propose a cooldown scheme for expected improvement: They use
                                                                                  g
the “generalized expected improvement” uGEI (λ) = E [max {cmin − C(λ), 0} ], where larger
values for exponent g also enforce more exploration. They suggest to start with a large g value
in the beginning and to gradually decrease it to enforce exploitation at the end. Jasrasaria
and Pyzer-Knapp (2018) propose to dynamically give exploitation more weight as a function
of mean model-uncertainty in what they call “contextual improvement”. This has a similar effect of encouraging late exploitation, as model uncertainty generally decreases over the course
of optimization. Finally, De Ath et al. (2021) show that a very simply -greedy BO strategy
can perform well or even better than established acquisition functions. They simply propose
HPCs that maximize the predicted mean, but interleave random HPCs with  probability. They
show that this strategy performs well in settings with a low evaluation budget or with many
dimensions, which is consistent with the other proposed methods, which adaptively emphasize
exploitation more when remaining budget is low.

Multi-point proposal In its original formulation, BO only proposes one candidate HPC per
iteration and then waits for the performance evaluation of that configuration to conclude. However, in many situations, it is preferable to evaluate multiple HPCs in parallel by proposing
multiple configurations at once, or by asynchronously proposing HPCs while other proposals are
still being evaluated.
While in the sequential variant, the best point can be determined unambiguously from the full
information of the acquisition function. In the parallel variant, many points must be proposed at
the same time without information about how the other points will perform. The objective here
is to some degree to ensure that the proposed points are sufficiently different from each other.
The proposal of nbatch > 1 configurations in one BO iteration is called batch proposal or synchronous parallelization and works well if the runtimes of all black-box evaluations are somewhat
homogeneous. If the runtimes are heterogeneous, one may seek to spontaneously generate new
proposals whenever an evaluation thread finishes in what is called asynchronous parallelization.
This offers some advantages to synchronous parallelization, but is more complicated to implement in practice.
The simplest option to obtain nbatch proposals is to use the LCB criterion in Eq. (12) with
different values for κ. For this so-called qLCB (also referred to as qUCB) approach, Hutter et al.
(2012) propose to draw κ from an exponential distribution with rate parameter 1. This can
work relatively well in practice but has the potential drawback of creating proposals that are too
similar to each other (Bischl et al., 2014). Bischl et al. (2014) instead propose to maximize both
ĉ(λ) and σ̂(λ) simultaneously, using multi-objective optimization, and to choose nbatch points
from the approximated Pareto-front. Further ways to obtain nbatch proposals are constant liar,

                                                14

Kriging believer (both described in Ginsbourger et al., 2010), and q-EI (Chevalier & Ginsbourger,
2013). Constant liar sets fake constant response values for the first points proposed in the batch
to generate additional one via the normal EI principle and the approach; Kriging believer does
the same but uses the GP model’s mean prediction as fake value instead of a constant. The
qEI optimizes a true multivariate EI criterion and is computationally expensive for larger batch
sizes, but Balandat et al. (2020) implement methods to efficiently calculate the qEI (and qNEI
for noisy observations) through MC simulations.

Efficient Performance Evaluation While BO models only optimize the HPC prediction performance in its standard setup, there are several extensions that aim to make optimization more
efficient by considering runtime or resource usage. These extensions mainly modify the acquisition function to influence the HPCs that are being proposed. Snoek, Larochelle, and Adams
(2012) suggests the expected improvement per second (EIPS) as a new acquisition function.
The EIPS includes a second surrogate model that predicts the runtime of evaluating a HPC
in order to compromise between expected improvement and required runtime for evaluation.
Most methods that trade off between runtime and information gain fall under the category of
multi-fidelity methods, which is further discussed in Section 4.2.4. Acquisition functions that
are especially relevant here consider information gain-based criteria like Entropy Search (Hennig
& Schuler, 2012) or Predictive Entropy Search (Hernández-Lobato et al., 2016). These acquisition functions can be used for selective subsample evaluation (Klein et al., 2017b), reducing
the number of necessary resampling iterations (Swersky, Snoek, & Adams, 2013), and stopping
certain model classes, such as NNs, early.
4.2.4 Multifidelity and Hyperband
The multifidelity (MF) concept in HPO refers to all tuning approaches that can efficiently
handle a learner I(D, λ) with a fidelity HP λfid as a component of λ, which influences the
computational cost of the fitting procedure in a monotonically increasing manner. Higher λfid
values imply a longer runtime of the fit. This directly implies that the lower we set λfid , the
more points we can explore in our search space, albeit with much less reliable information w.r.t.
their true performance. If λfid has a linear relationship with the true computational costs, we can
directly sum the λfid values for all evaluations to measure the computational costs of a complete
optimization run. We assume to know box-constraints of λfid in form of a lower and upper
                           upp
limit, so λfid ∈ [λlow
                    fid , λfid ], where the upper limit implies the highest fidelity returning values
closest to the true objective value at the highest computational cost. Usually, we expect higher
values of λfid to be better in terms of predictive performance yet naturally more computationally
expensive. However, overfitting can occur at some point, for example when λfid controls the
number of training epochs when fitting an NN. Furthermore, we assume that the relationship
of the fidelity to the prediction performance changes somewhat smoothly. Consequently, when
evaluating multiple HPCs with small λfid , this at least indicates their true ranking. Typically,
this implies a sequential fitting procedure, where λfid is, for example, the number of (stochastic)
gradient descent steps or the number of sequentially added (boosting) ensemble members. A
further, generally applicable option is to subsample the training data from a small fraction to
100% before training and to treat this as a fidelity control (Klein et al., 2017b). HPO algorithms
that exploit such a λfid parameter – usually by spending budget on cheap HPCs with low λfid
values earlier for exploration, and then concentrating on the most promising ones later – are called
multifidelity methods. One can define two versions of the MF-HPO problem. (a) If overfitting
can occur with higher values of λfid (e.g., if it encodes training iterations), simply minimizing
minλ∈Λ̃ c(λ) is already appropriate. (b) If the assumption holds that a higher fidelity always

                                                 15

results in a better model (e.g., if λfid controls the size of the training set), we are interested
in finding the configuration λ∗ for which the inducer will return the best model given the full
budget, so minλ∈Λ̃,λfid =λupp c(λ). Of course, in both versions, the optimizer can make use of
                          fid
cheap HPCs with low settings of λfid on its path to its result.
Hyperband (L. Li et al., 2018) can best be understood as repeated execution of the successive
halving (SH) procedure (Jamieson & Talwalkar, 2016). SH assumes a fidelity-budget B for the
sum of λfid for all evaluations. It starts with a given, fixed number of candidates λ(i) that we
denote with p[0] and “races them down” in stages t to a single best candidate by repeatedly
evaluating all candidates with increased fidelity in a certain schedule. Typically, this is controlled
by the ηHB control multiplier of Hyperband with ηHB > 1 (typically set to 2 or 3): After each
batch evaluation t of the current population of size p[t] , we reduce the population to the best
 1
ηHB fraction and set the new fidelity for a candidate evaluation to ηHB × λfid . Thus, promising
HPCs are assigned a higher fidelity overall, and sub-optimal ones are discarded early on. The
                   [0]
starting fidelity λfid and the number of stages s + 1 are computed in a way such that each batch
evaluation of an SH population has approximately B/(s + 1) amount of fidelity units spent.
Overall, this ensures that approximately, but not more than, B fidelity units are spent in SH:

                                        Xj
                                        s              k
                                                    −t    [0] t
                                              p[0] ηHB   λfid ηHB ≤ B.                                    (13)
                                        t=0

However, the efficiency of SH strongly depends on a sensible choice of the number of starting
configurations and the resulting schedule. If we assume a fixed fidelity-budget for HPO, the
user has the choice of running either (a) more configurations but with less fidelity, or (b) fewer
configurations, but with higher fidelity. While the former naturally explores more, the latter
schedules evaluations with stronger correlation to the true objective value and more informative
evaluations. As an example, consider how λ(6) is discarded in favor of λ(8) at 25% in Figure 6. Because their performance lines would have crossed close to 100%, λ(6) is ultimately
the better configuration. However, in this case, the superiority of λ(6) was only observable
after full evaluation. As we often have no prior knowledge regarding this effect, HB simply
                                                             [0]
runs SH for different numbers of starting configurations ps , and each SH run or schedule is
called a bracket. As input, HB takes ηHB and the maximum fidelity λupp     fid > ηHB . HB then
constructs the target fidelity budget B for each bracket by considering the    most explorative
                                                                                             
bracket: Here, the number of batch evaluations smax + 1 is chosen to be logηHB (λupp    fid ) + 1
              [0]                                       [s   ]
for which λfid = λupp          −smax
                          fid ηHB    ∈ (ηHB −1
                                               , ηHB ), λfidmax = λupp    fid , and we collect these values in
        upp −smax       upp −smax +1    upp −smax +2
r = (λfid ηHB , λfid ηHB             , λfid ηHB        , . . . , λupp
                                                                   fid ) ∈ R
                                                                              smax +1
                                                                                      . Since we want to spend
approximately the same total fidelity and reduce the candidates to one winning HPC in every batch evaluation, the fidelity budget of each bracket is B = (smax + 1)λupp                  fid . For every
                                                                                        [0]
s ∈ {0, . . . , smax }, a bracket is defined by setting the starting fidelity λfid ≥ λlow     fid of the bracket
to r (1+smax −s) , resulting in smax + 1 brackets and an overall fidelity budget of (smax + 1)B spent
by HB. Consequently, every bracket s consists of s + 1 batch evaluations, and the starting pop-
                 [0]
ulation size ps is the maximum value that fulfills Eq. (13). The full algorithm is outlined in
Algorithm 2, and the bracket design of HB with λupp             fid = 8 and ηHB = 2 is shown in Figure 6.
Starting configurations are usually sampled uniformly, but L. Li et al., 2018 also show that any
stationary sampling distribution is valid. Because HB is a random-sampling-based method, it
can trivially handle hierarchical HP spaces in the same manner as RS.

                                                      16

c(λ)                                                                             bracket 3
                                                                                  [t]      [t]
                                                                            t    λbudget p3
1
                                                                            0        1     8
                                                                            1        2     4
                                                                            2        4     2
                                                                            3        8     1
                                                                                 bracket 2
                                                              λ(1)          t
                                                                                  [t]
                                                                                 λbudget p2
                                                                                           [t]
                                                              λ(2)
                                                                            0        2     6
                                                              λ(3)
                                                              λ(4)
                                                                            1        4     3
                                                              λ(5)          2        8     1
                                                              λ(6)
                                                                                 bracket 1
                                                              λ(7)                [t]      [t]
                                                              λ(8)          t    λbudget p1
                                                                            0        4     4
                                                                            1        8     2
                                                                                 bracket 0
                                                                                  [t]      [t]
                                                                            t    λbudget p0
0
                                                        [t]
                                                      λbudget               0        8     4
       12% 25%          50%                    100%

Figure 6: Right: Bracket design of HB with λuppfid = 8 and ηHB = 2 (resulting in four brackets).
Left: Exemplary bracket run (figure inspired by Hutter et al. (2019)). Faint lines represent future
performance of HPCs that were discarded early.

Multifidelity Bayesian Optimization The idea behind Hyperband – trying to discard HPCs
that do not perform well early on – is somewhat orthogonal to the idea behind BO, i.e. intelligently proposing HPCs that are likely to improve performance or to otherwise gain information
about the location of the optimum. It is therefore natural to combine these two methods. This
has first been achieved with BOHB by Falkner et al. (2018), who progressively increase λfid of
suggested HPCs as in Hyperband. However, instead of proposing HPCs randomly, they use a
model-based approach equivalent to maximizing expected improvement. They show that BOHB
performs similar to HB in the low-budget regime, where it is superior to normal BO methods, but
outperforms HB and perform similar or better to BO when enough budget for tens of full-budget
evaluations are available. BOHB was later extended to A-BOHB (Tiao et al., n.d.) to efficiently
perform asynchronously parallelized optimization by sampling possible outcomes of evaluations
currently under way.
Hyperband-based multi-fidelity methods have a control parameter that functions similar to ηHB
described above, which determines the fraction of configurations that are discarded at every λfid
value for which evaluations are performed. However, the optimal proportion of configurations
to discard may vary depending on how strong the correlation is between performance values at
different fidelities. An alternative approach is to use the surrogate model from BO to make
adaptive decisions about what λfid values to use, or what HPCs to discard. Algorithms following

                                                17

 Algorithm 2: Hyperband algorithm (L. Li et al., 2018) where
  • get HPCs(p) uses a stationary sampling distribution to generate the initial HPC popu-
     lation of size p,
  • top k(Λs , C, k) selects the k HPCs in Λs associated to the k best performances in C
     as the next HPC population.
 1 input: maximum fidelity  per HPC        λupp
                                              fid , ηHB
                                         upp                             upp
 2 initialization: smax = logη (λfid ) , B = (smax + 1)λfid
                                    HB

 3       r = (λupp   −smax   upp −smax +1
                fid ηHB , λfid ηHB          , λupp    −smax +2
                                                 fid ηHB       , . . . , λupp
                                                                          fid )
 4 for s = smax , smax − 1, . . . , 0 do
             l         m
                       s
                  B ηHB
 5      ps =     λupp s+1
                  fid
          [0]                                (1)         (p )     (i)
 6      Λs = get HPCs(ps ) (= {λs , . . . , λs s }, λs ∈ Λ̃)
 7      // Successive Halving inner loop
 8      for t = 0, . . . , s do
             [t]    −t 
 9          ps = ps ηHB
                                                        [t]
10          Set λfid components of entries of Λs to r (1+smax −s+t)               (= (λupp  −s      t
                                                                                       fid ηHB ) · ηHB )
                                      [t]
11          C [t] = {c(λ) : λ ∈ Λs } j              k
                [t+1]            [t]          [t]
12          Λs          = top k(Λs , C [t] , ps /ηHB )
13      end
14   end
     Result: HPC with best performance

this approach typically use a method first proposed by Swersky, Snoek, and Adams (2013), who
use a surrogate model for both the performance, as well as the resources used to evaluate an
HPC. Entropy search (Hennig & Schuler, 2012) is then used to maximize the information gained
about the maximum for when λfid = λuppfid per unit of predicted resource expenditure. Low-fidelity
HPCs are evaluated whenever they contribute disproportionately large amounts of information to
the maximum compared to their needed resources. A special challenge that needs to be solved
by these methods is the modeling of performance with varying λfid , which often has a different
influence than other HPs and is therefore often considered as a separate case. An early HPO
method building on this concept is freeze-thaw BO (Swersky, Snoek, & Adams, 2014), which
considers optimization of iterative ML methods such as deep learning that can be suspended
(“frozen”) and continued (“thawed”). Another HPO method that specifically considers the
training set size as fidelity HP is FABOLAS (Klein et al., 2017a), which actively decides the
training set size for each evaluation by trading off computational cost of an evaluation with a
lot of data against the information gain on the potential optimal configuration.
In general, there could be other proposal mechanisms instead of random sampling as in Hyperband or BO as in BOHB. For example, Awad et al. (2021) showed that differential evolution can
perform even better; however the evolution of population members across fidelities needs to be
adjusted accordingly.
4.2.5 Iterated Racing
The iterated racing (IR, Birattari et al., 2010) procedure is a general algorithm configuration
method that optimizes for a configuration of a general (not necessarily ML) algorithm that

                                                        18

performs well over a given distribution of (arbitrary) problems. In most HPO algorithms, HPCs
are evaluated using a resampling procedure such as CV, so a noisy function (error estimate for
single resampling iterations) is evaluated multiple times and averaged. In order to connect racing
to HPO, we now define a problem as a single holdout resampling split for a given ML data set,
as suggested in Thornton et al. (2013) and Lang et al. (2015), and we will from now on describe
racing only in terms of HPO.
The fundamental idea of racing (Maron & Moore, 1994) is that HPCs that show particularly
poor performance when evaluated on the first problem instances (in our case: resampling folds)
are unlikely to catch up in later folds and can be discarded early to save computation time for
more interesting HPCs. This is determined by running a (paired) statistical test w.r.t. HPC
performance values on folds. This allows for an efficient and dynamic allocation of the number
of folds in the computation of c(λ) – a property of IR that is unique, at least when compared
to the algorithms covered in this article.
Racing is similar to HB in that it discards poorly-performing HPCs early. Like HB, racing must
also be combined with a sampling metaheuristic to initialize a race. Particularly well-suited
for HPO are iterated races (López-Ibáñez et al., 2016), and we will use the terminology of
that implementation to explain the main control parameters of IR. IR starts by racing down an
initial population of randomly sampled HPCs and then uses the surviving HPCs of the race to
stochastically initialize the population of the subsequent race to focus on interesting regions of
the search space.
Sampling is performed by first selecting a parent configuration λ among the N elite survivors
of the previous generation, according to a categorical distribution with probabilities pλ =
2(N elite − rλ + 1)/(N elite (N elite + 1)), where rλ is the rank of the configuration λ. A new HPC
is then generated from this parent by mutating numeric HPs using a truncated normal distribution, always centered at the numeric HP value of the parent. Discrete parameters use a discrete
probability distribution. This is visualized in Figure 7. The parameters of these distributions are
updated as the optimization continues: The standard deviation of the Gaussian is narrowed to
enforce exploitation and convergence, and the categorical distribution is updated to more strongly
favor the values of recent ancestors. IR is able to handle search spaces with dependencies by
sampling HPCs that were inactive in a parent configuration from the initial (uniform) distribution. This algorithmic principle of having a distribution that is centered around well-performing
candidates, is continuously sampled from and updated, is close to an estimation-of-distribution
algorithm (EDA), a well-known template for ES (Larrañaga & Lozano, 2001). Therefore, IR
could be described as an EDA with racing for noise handling.
IR has several control parameters that determine how the racing experiments are executed. We
only describe the most important ones here; many of these have heuristic defaults set in the
implementation introduced by López-Ibáñez et al. (2016). N iter (nbIterations) determines
the number of performed races, defaulting to b2 + log2 dim(Λ̃)c (with dim(Λ̃) the number
of HPs being optimized). Within a race, each HPC is first evaluated on T first (firstTest)
folds before a first comparison test is made. Subsequent tests are then made after every T each
(eachTest) evaluation. IR can be performed as elitist, which means surviving configurations
from a generation are part of the next generation. The statistical test that discards individuals
can be the Friedman test or the t-test (Birattari et al., 2002), the latter possibly with multiple
testing correction. López-Ibáñez et al. (2016) recommend the t-test when when performance
values for evaluations on different instances are commensurable and the tuning objective is the
mean over instances, which is usually the case for our resampled performance metric where

                                                19

                        λ(1) λ(2) λ(3) λ(4) λ(5) λ(6) λ(7) λ(8)
                   1

 resampling fold                                                              elite configuration
                   2                                              =
                   3                                              −

                                                                                                                                 elite configuration
                   4                                              −
                                                                                                            num             λ1
                   5                                              −                                    x     µ      x

                   6
                                                                                                            cat             λ2
                                                                                                                                                               num             λ1
                                                                                                    lvl 1   lvl 2   lvl 3
                                                                                                                                                          x     µ      x

                                                                                                                                                               cat             λ2

                                                                                                                                                       lvl 1   lvl 2   lvl 3

Figure 7: Scheme of the iterated racing algorithm (figure based on López-Ibáñez et al. (2016)).

instances are simply resampling folds.

4.3                    HPO - A Bilevel Inference Perspective
As discussed in Section 4.1, HPO produces an approximately optimal HPC λ̂ = τ (D, I, Λ̃, ρ)
                                                             d J , ρ, λ). This is still risk
by optimizing it w.r.t. the resampled performance c(λ) = GE(I,
minimization w.r.t. (hyper)parameters, where we search for optimal parameters λ̂ so that the
risk of our predictor fˆλ̂,θ̂ becomes minimal when measured on validation data via
                                                                       m
                                                                       X                    
                                                                                         (i)
                                                   ρL (y, Fλ̂,θ̂ ) =         L y (i) , Fλ̂,θ̂ ,                                                                            (14)
                                                                       i=1

where fˆλ̂,θ̂ = I(Dtrain , λ̂) and Fλ̂,θ̂ is the prediction matrix of fˆ on validation data, for a
pointwise loss function. The above is formulated for a single holdout split (Jtrain , Jtest ) in
order to demonstrate the tight connection between (first level) risk minimization and HPO;
Eq. (8) provides the generalization for arbitrary resampling with multiple folds. This is somewhat
obfuscated and complicated by the fact that we cannot evaluate Eq. (8) in one go, but must
rather fit one or multiple models fˆ during its computation (hence also its black-box nature). It
is useful to conceptualize this as a bilevel inference mechanism; while the parameters θ̂ of f for a
given HPC are estimated in the first level, in the second level we infer the HPs λ̂. However, both
levels are conceptually very similar in the sense that we are optimizing a risk function for model
parameters which should be optimal for the the data distribution at hand. In case of the second
                                                                                            d An
level, this risk function is not Remp (θ), but the harder-to-evaluate generalization error GE.
intuitive, alternative term for HPO is second level inference (Guyon et al., 2010), visualized in
Figure 8.
There are mainly two reasons why such a bilevel optimization is preferable to a direct, joint risk
minimization of parameters and HPs (Guyon et al., 2010):

                                                                       20

                                               Input
                                                 Training Data Dtrain , Inducer I,
                                                metric ρ, splits J , search space Λ̃

          Self-Tuning learner TI,Λ̃,ρ,J
             Tuning/2nd Level Inference

                                                    Pro
                                                       po
                                                                             λ+
                                                         se
                                                                  n
                                                                      ew

                 Update Opti
                                                                             C
                                                                           HP
                            mi
                              ze          HPO                                          1st Level Inference
                                r                                                     for configuration λ+
                                          Loop
                                                                                      J = ((Jtrain,1 , Jtest,1 ), . . . , (Jtrain,B , Jtest,B ))
                                                                                      for k = 1, . . . , B
                                                                                         fˆθ̂,k = Iλ+ (Dtrain,k )
                                                                  g                                       P
                                                                      in                 = arg minf ∈H (x,y)∈Dtrain,k L(y, fθ (x)),
                                                                 pl          c(λ+)
                                    Eva                   sa m                        where λ+ configures the above e.g. by
                                          lu ate b y Re
                                                                                      modifying H, L or aspects of the optimizer.

                                                                                      λ̂ = τ (D, I, Λ̃, ρ)
                                                                                      ∈
                                                                                      arg minλ∈Λ̃ c(λ)

                                                           Final Model Fit
                                                                  Fit I with λ̂ on Dtrain

                                                           Return
                                                                           Model fˆ, HPC λ̂

       Figure 8: Self-Tuning learner with integrated HPO wrapped around the inducer.

   • Typically, learners are constructed in such a way that optimized first-level parameters can
     be more efficiently computed for fixed HPs, e.g., often the first-level problem is convex,
     while the joint problem is not.
   • Since the generalization error is eventually optimized for the bilevel approach, the resulting
     model should be less prone to overfitting.
Thus, we can define a learner with integrated tuning as a mapping TI,Λ̃,ρ,J : D → H, D →
Iτ (D,I,Λ̃,ρ) (D), which maps a data set D to the model fˆλ̂ that has the HPC set to λ̂ as optimized
by τ on D and is then itself trained on the whole of D; all for a given inducer I, performance
measure ρ, and search space Λ̃. Algorithmically, this learner has a 2-step training procedure (see
Figure 8), where tuning is performed before the final model fit. This “self-tuning” learner T
“shadows” the tuned HPs of its search space Λ̃ from the original learner and integrates their
configuration into the training procedure 8 . If such a learner is cross-validated, we naturally
  8 The self-tuning learner actually also adds new HPs that are the control parameters of the HPO procedure.

                                                                                 21

arrive at the concept of nested CV, which is discussed in the following Section 4.4.

4.4                        Nested Resampling and Meta-Overfitting
As discussed in Section 3.2, the evaluation of any learner should always be performed via resampling on independent test sets to ensure non-biased estimation of its generalization error. This
is necessary because evaluating fˆ on the data set D that was used for its construction would
lead to an optimistic bias. In the general HPO problem as in Eq. (10), we already minimize this
generalization error by resampling:
                                                                     d J , ρ, λ).
                                         λ̂ ∈ arg min c(λ) = arg min GE(I,                                  (15)
                                                λ∈Λ̃               λ∈Λ̃

If we simply report the estimated c(λ̂) value of the returned best HPC, this also creates an
optimistically biased estimator of the generalization error, as we have violated the fundamental
“untouched test set” principle by optimizing on the test set(s) instead.

                    0.50

                                                                                               nested resampling

Tuned Performance
                                                                                               resampling

                    0.45

                                                                                           Data Dimension
                                                                                               100
                                                                                               200
                                                                                               500

                    0.40

                           0           25                 50                75       100
                                     Number of tried hyperparameter configurations

Figure 9: While nested resampling delivers correct results for the performance around 0.5, taking
the tuning result directly results in a biased, optimistic estimator, especially on smaller data sets.

To better understand the necessity of an additional resampling step, we consider the following
example in Figure 9, introduced by Bischl et al. (2012). Assume a balanced binary classification
task and an inducer Iλ that ignores the data. Hence, λ has no effect, but rather “predicts” the
class labels in a balanced but random manner. Such a learner always has a true misclassification
error of GE(I, λ, ntrain , ρ) = 0.5 (using ρCE as a metric), and any normal CV-based estimator
will provide an approximately correct value as long as our data set is not too small. We now
“tune” this learner, for example, by RS – which is meaningless, as λ has no effect. The more
tuning iterations are performed, the more likely it becomes that some model from our archive
will produce partially correct labels simply by random chance, and the (only randomly) “best”
of these is selected by our tuner at the end. The more we tune, the smaller our data set, or the
more variance our GE estimator exhibits, the more expressed this optimistic bias will be.

                                                                 22

To avoid this bias, we introduce an additional outer resampling loop around this inner HPOresampling procedure – or as discussed in Section 4.3, we simply regard this as cleanly crossvalidating the self-tuned learner TI,Λ̃,ρ,J . This is called nested resampling, which is illustrated
in Figure 10.

                        (outer)                          (outer)                         (outer)        (outer)      (outer)          (outer)
I.                 Dtrain                              Dtrain                        Dtest         II. Dtrain      Dtest            Dtrain

                                                                                                                      ..
                                                  Pro
                                                     po
                                                       se
                                                                 ne
                                                                      w

     Update Optim
                                                                                C
                                                                              HP
                                      Learner I
                                                                                                         (outer)     (outer)          (outer)
                                       inner CV
                                                                                                   III. Dtest      Dtrain           Dtrain
                 ize
                         c(λ)[1]

                                             ..
                    r
                         c(λ)[3]

                                                                     ng                                                        ..
                                                                          i
                                                                     pl
                                                            s   am
                                   E v alu             Re
                                             ate b y

                                                            fˆλ̂ /λ̂/Prediction

Figure 10: Nested CV with 3 inner and outer folds: each HPC is evaluated on an inner CV,
while the resulting tuned model is evaluated on the outer test set.

The procedure works as follows: In the outer loop, an outer model-building or training set is
selected, and an outer test set is cleanly set aside. Each proposed HPC λ+ during tuning is
evaluated via inner resampling on the outer training set. The best performing HPC λ̂ returned
by tuning is then used to fit a final model for the current outer loop on the outer training set,
and this model is then cleanly evaluated on the test set. This is repeated for all outer loops, and
all outer test performances are aggregated at the end.
Some further comments on this general procedure: (i) Any resampling scheme is possible on
the inside and outside, and these schemes can be flexibly combined based on statistical and
computational considerations. Nested CV and nested holdout are most common. (ii) Nested
holdout is often called the train-validation-test procedure, with the respective terminology for
the generated three data sets resulting from the 3-way split. (iii) Many users often wonder
which “optimal” HPC λ̂ they are supposed to report or study if nested CV is performed, with
multiple outer loops, and hence multiple outer HPCs λ̂. However, the learned HPs that result
from optimizations within CV are considered temporary objects that merely exist in order to
          d J , ρ, λ). The comparison to first-level risk minimization from Section 4.3 is
estimate GE(I,
instructive here: The formal goal of nested CV is simply to produce the performance distribution
on outer test sets; the λ̂ can be considered as the fitted HPs of the self-tuned learner T . If the

                                                                                    23

parameters of a final model are of interest for further study, the tuner T should be fitted one
final time on the complete data set. This would imply a final tuning run on the complete data
set for second-level inference.
Nested resampling ensures unbiased outer evaluation of the HPO process, but, as CV for the
first level, it is only a process that is used to estimate performance – it does not directly help
in constructing a better model. The biased estimation of performance values is not a problem
for the optimization itself, as long as all evaluated HPCs are still ranked correctly. But after a
considerably large amount of evaluations, wrong HPCs might be selected due to stochasticity or
overfitting to the splits of the inner resampling. This effect has been called either overtuning,
meta-overfitting or oversearching (Ng, 1997; Quinlan & Cameron-Jones, 1995). At least parts
of this problem seem directly related to the problem of multiple hypothesis testing. However, it
has not been analysed very well yet, and unlike regularization for (first level) ERM, not many
counter measures are currently known for HPO.

4.5     Threshold Tuning
Most classifiers do not directly output class labels, but rather probabilities or real-valued decision
scores, although many metrics require predicted class labels. A score is converted to a predicted
label by comparing it to a threshold t so that ŷ = [f (x) ≥ t], where we use the Iverson bracket
[] with ŷ = 1 if f (x) ≥ t and ŷ = 0 in all other cases. For binary classification, the default
thresholds are t = 0.5 for probabilities and t = 0 for scores. However, depending on the metric
and the classifier, different thresholds can be optimal. Threshold tuning (Sheng & Ling, 2006) is
the practice of optimizing the classification threshold t for a given model to improve performance.
Strictly speaking, the threshold constitutes a further HP that must be chosen carefully. However,
since the threshold can be varied freely after a model has been built, it does not need to be tuned
jointly with the remaining HPs and can be optimized in a separate, subsequent, and cheaper
step for each proposed HPC λ+ .
After models have been fitted and predictions for all test sets have been obtained when c(λ+ ) is
computed via resampling, the vector of joint test set scores F̃ can be compared against the joint
vector of test set labels y to optimize for an optimal threshold t̂ = arg mint ρ(ỹ, [F̃ ≥ t]), where
the Iverson bracket is evaluated component-wise and t ∈ [0, 1] for a binary probabilistic classifier
and t ∈ R for a score classifier. Since t is scalar and evaluations are cheap, t̂ can be found easily
via a line search. This two-step approach ensures that every HPC is coupled with its optimal
threshold. c(λ+ ) is then defined as the optimal performance value for λ+ in combination with
t̂.
The procedure can be generalized to multi-class classification. Class probabilities or scores
π̂k (x), k = 1, . . . , g are divided by threshold weights wk , k = 1, . . . , g, and the k that yields the
maximal π̂kw(x)
             k
                  is chosen as the predicted class label. The weights wk , k = 1, . . . , g are optimized
in the same way as t in the binary case. Generally, threshold tuning can be performed jointly
with any HPO algorithm. In practice, threshold tuning is implemented as a post-processing step
of an ML pipeline (Sections 5.1 & 5.2).

5     Pipelining, Preprocessing, and AutoML
ML typically involves several data transformation steps before a learner can be trained. If one
or multiple preprocessing steps are executed successively, the data flows through a linear graph,
also called pipeline. Subsection 5.1 explains why a pipeline forms an ML algorithm itself, and

                                                    24

why its performance should be evaluated accordingly through resampling. Finally, Subsection 5.2
introduces the concept of flexible pipelines via hierarchical spaces.

5.1      Linear Pipelines
In the following, we will extend the HPO problem for a specific learner towards configuring a full
pipeline including preprocessing. A linear ML pipeline is a succession of preprocessing methods
followed by a learner at the end, all arranged as nodes in a linear graph. Each node acts in a very
similar manner as the learner, and has an associated training and prediction procedure. During
training, each node learns its parameters (based on the training data) and sends a potentially
transformed version of the training data to its successor. Afterwards, a pipeline can be used
to obtain predictions: The nodes operate on new data according to their model parameters.
Figure 11 shows a simple example. Pipelines are important to properly embed the full model

                                                                                                                                       Prop                               λ+
                        Factor    Feature                                                                                                  ose
Dtrain       Scaling   Encoding   Filtering   Learner                                                                                                         n
                                                                                                                                                              ew

                                                                          Update Optim
                                                                                                                                                                        C
                                                                                                                                                                      HP

                                                                                      ize
                                                                                                                 Factor    Feature
                                                                                             Dtrain   Scaling   Encoding   Filtering   Learner

                                                                                         r
                                                                                                                 Factor    Feature
                                                                                                      Scaling   Encoding   Filtering   Learner

                                                                                             Dtest                                               Prediction
                                                                                                      Scaling    Factor    Selected    Learner

                        Factor    Feature                                                             Factors    Levels    Features     Model

             Scaling   Encoding   Filtering   Learner

 Dtest                                                       Prediction                                                                                       lin
                                                                                                                                                                      g
             Scaling    Factor    Selected    Learner                                                                                                    m
                                                                                                                                                                  p
             Factors    Levels    Features     Model                                                  Eva                                        esa
                                                                                                                lu ate b y R                                              c(λ+)

              Figure 11: Example of a linear pipeline, including node parameters.

building procedure, including preprocessing, into cross-validation, so every aspect of the model is
only inferred from the training data. This is necessary to avoid overfitting and biased performance
evaluation (Bischl et al., 2012; Hornung et al., 2015), as it is for basic ML. As each node
represents a configurable piece of code, each node can have HPs, and the HPs of the pipeline
are simply the joint set of all HPs of its contained nodes. Therefore, we can model the whole
pipeline as a single HPO problem with the combined search space Λ̃ = Λ̃op,1 × · · · × Λ̃op,k × Λ̃I .

5.2      Operator Selection and AutoML
More flexible pipelining, and especially the selection of appropriate nodes in a data-driven manner
via HPO, can be achieved by representing our pipeline as a directed acyclic graph. Usually, this
implies a single source node that accepts the original data and a single sink node that returns
the predictions. Each node represents a preprocessing operation, a learner, a postprocessing
operation, or a directive operation that directs how the data is passed to the child node(s).
One instance of such a pipeline is illustrated in Figure 12.
Here, we consider the choice between multiple mutually exclusive preprocessing steps, as well
as the choice between different ML algorithms. Such a choice is represented by a branching
operator, which can be configured through a categorical parameter and can determine the flow
of the data, resulting in multiple “modeling paths” in our graph.
The HP space induced by a flexible pipeline is potentially more complex. Depending on the
setting of the branching HP, different nodes and therefore different HPs are active, resulting in

                                                        25

         Figure 12: Example for a graph pipeline with operator selection via branches.

a very hierarchical search space. If we build a graph that includes a sufficiently large selection
of preprocessing steps combined with sufficiently many ML models, the result can be flexible
enough to work well on a large number of data sets – assuming it is correctly configured in a
data-dependent manner. Combining such a graph with an efficient tuner is the key principle
of AutoML (Feurer, Klein, Eggensperger, Springenberg, et al., 2015; Mohr et al., 2018; Olson
et al., 2016).

6     Practical Aspects of HPO
In this section, we discuss practical aspects of HPO, which are more qualitative in nature and
less often discussed in an academic context. Some of these recommendations are more “rules
of thumb”, based solely on experience, while others are at least partially confirmed by empirical
benchmarks – keeping in mind that empirical benchmarks are only as good as the selection of
data sets. Even if a proper empirical study cannot be cited for every piece of advice in this
section, we still propose that such a compilation is highly valuable for HPO beginners.

6.1    Choosing Resampling and Performance Metrics
A resampling procedure is usually chosen based on two fundamental properties of the available
data: (i) the number of observations, i.e., to what degree do we face a small sample size
situation, and (ii) whether the i.i.d. assumption for our data sampling process is violated.
For smaller data sets, e.g., n < 500, repeated CV with a high number of repetitions should be
used to reduce the variance while keeping the pessimistic bias small (Bischl et al., 2012). The
larger the data set, the fewer splits are necessary. Consequently, for data sets of “medium” size
with 500 ≤ n ≤ 50000, usually 5- or 10-fold CV is recommended. Beyond that, simple holdout
might be sufficient. Note that even for large n, sample size problems can occur, for example,
when data is imbalanced. In that case, repeated resampling might still be required to obtain
properly accurate performance estimates. Stratified sampling, which ensures that the relative
class frequencies for each train/test split are consistent with the original data set, helps in such

                                                26

a case.
                                                                                              i.i.d
One fundamental assumption about our data is that observations are i.i.d., i.e., x(i) , y (i) ∼
Pxy . This assumption is often violated in practice. A typical example is repeated measurements,
where observations occur in “blocks” of multiple, correlated data, e.g., from different hospitals,
cities or persons. In such a scenario, we are usually interested in the ability of the model to
generalize to new blocks. We must then perform CV with respect to the blocks, e.g., “leave one
block out”. A related problem occurs if data are collected sequentially over a period of time.
In such a setting, we are usually interested in how the model will generalize in the future, and
the rolling or expanding window forecast must be used for evaluation (Bergmeir et al., 2018)
instead of regular CV. However, discussing these special cases is out of scope for this work.
In HPO, resampling strategies must be specified for the inner as well as the outer level of nested
resampling. The outer level is simply regular ML evaluation, and all comments from above hold.
We advise readers to study further material such as Japkowicz and Shah (2011). The inner
level concerns the evaluation of c(λ) through resampling during tuning. While the same general
comments from above apply, in order to reduce runtime, repetitions can also be scaled down.
We are not particularly interested in very accurate numerical performance estimates at the inner
level, and we must only ensure that HPCs are properly ranked during tuning to achieve correct
selection, as discussed in Section 4.4. Hence, it might be appropriate to use a 10-fold CV on
the outside to ensure proper generalization error estimation of the tuned learner, but to use
only 2 folds or simple holdout on the inside. In general, controlling the number of resampling
repetitions on the inside should be considered an aspect of the tuner and should probably be
automated away from the user (without taking away flexible control in cases of i.i.d. violations,
or other deviations from standard scenarios). However, not many current tuners provide this,
although racing is one of the attractive exceptions.
The choice of the performance measure should be guided by the costs that suboptimal predictions
by the model and subsequent actions in the real-word context of applying the model would incur.
Often, popular but simple measures like accuracy do not meet this requirement. Misclassification
of different classes can imply different costs. For example, failing to detect an illness may have a
higher associated cost than mistakenly admitting a person to the hospital. There exists a plethora
of performance measures that attempt to emphasize different aspects of misclassification with
respect to prediction probabilities and class imbalances, c.f. Japkowicz and Shah (2011)and
many listed in Table 8 in Appendix C. For other applications, it might be necessary to design a
performance measure from scratch or based on underlying business key performance indicators
(KPIs).
While a further discussion of metrics is again out of scope for this article, two pieces of advice
are pertinent. First, as HPO is a black-box, no real constraints exist regarding the mathematical
properties of ρ (or the associated outer loss). For first-level risk minimization, on the other
hand, we usually require differentiability and convexity of L. If this is not fulfilled, we must
approximate the KPI with a more convenient version. Second, for many applications, it is quite
unclear whether there is a single metric that captures all aspects of model quality in a balanced
manner. In such cases, it can be preferable to optimize multiple measures simultaneously,
resulting in a multi-criteria optimization problem (Horn & Bischl, 2016).

6.2       Choosing a Pipeline and Corresponding Search Space
For HPO, it is necessary to define the search space Λ̃ over which the optimization is to be
performed. This choice can have a large impact on the performance of the tuned model. A simple

                                                27

search space is a (lower dimensional) Cartesian product of individual HP sets that are either
numeric (continuous or integer-valued) or categorical. Encoding categorical values as integers is
a common mistake that degrades the performance of optimizers that rely on information about
distances between HPCs, such as BO. The search intervals of numeric HPs typically must be
bounded within a region of plausibly well-performing values for the given method and data set.
Many numeric HPs are often either bounded in a closed interval (e.g., [0, 1]) or bounded from
below (e.g., [0, ∞)). The former can usually be tuned without modifications. HPs bounded by
a left-closed interval should often be tuned on a logarithmic scale with a generous upper bound,
as the influence of larger values often diminishes. For example, the decision whether k-NN uses
k = 2 vs. 3 neighbors will have a larger impact than whether it uses k = 102 vs. k = 103
neighbors. The logarithmic scale can either be defined in the tuning software or must be set
up manually by adjusting the algorithm to use transformations: If the desired range of the HP
is [a, b], the tuner optimizes on [log a, log b], and any proposed value is transformed through an
exponential function before it is passed to the ML algorithm. The logarithm and exponentiation
must refer to the same base here, but which base is chosen does not influence the tuning process.
The size of the search space will also considerably influence the quality of the resulting model
and the necessary budget of HPO. If chosen too small, the search space may not contain a
particularly well-performing HPC. Choosing too wide HP intervals or including inadequate HPs
in the search space can have an adverse effect on tuning outcomes in the given budget. If Λ̃ is
simply too large, it is more difficult for the optimizer to find the optimum or promising regions
within the given budget. Furthermore, restricting the bounds of an HP may be beneficial to
avoid values that are a priori known to cause problems due to unstable behavior or large resource
consumption. If multiple HPs lead to poor performance throughout a large part of their range
– for example, by resulting in a degenerate ML model or a software crash – the fraction of the
search space that leads to fair performance then shrinks exponentially in the number of HPs
with this problem. This effect can be viewed as a further manifestation of the so-called curse of
dimensionality.
Due to this curse of dimensionality and the considerable runtime costs of HPO, we would
like to tune as few HPs as possible. If no prior knowledge from earlier experiments or expert
knowledge exists, it is common practice to leave other HPs at their software default values with
the assumption that the developers of the algorithm chose values that work well under a wide
range of conditions, which is not necessarily given and its often not documented how these
defaults were specified. Recent approaches have studied how to empirically find optimal default
values, tuning ranges and HPC prior distributions based on extensive meta-data (Wistuba et al.,
2015b, 2015a; Feurer, Klein, Eggensperger, Springenberg, et al., 2015; Pfisterer et al., 2021;
Van Rijn & Hutter, 2018; Probst et al., 2019; Perrone et al., 2019; Gijsbers et al., 2021).
It is possible to optimize several learning algorithms in combination, as described in Section 5.2,
but this introduces HP dependencies. The question then arises of which of the large number of
ML algorithms (or preprocessing operations) should be considered. However, Fernández-Delgado
et al. (2014) showed that in many cases, only a small but diverse set of learners is necessary to
choose one ML algorithm that performs sufficiently well.

6.3    Choosing an HPO Algorithm
The number of HPs considered for optimization has a large influence on the difficulty of an HPO
problem. Particularly large search spaces typically arise from optimizing over large pipelines
or multiple learners (Section 5). With very few HPs (up to about 2-3) and well-functioning

                                                28

discretization, GS may be useful due to its interpretable, deterministic, and reproducible nature;
however, it is not recommended beyond this (Bergstra & Bengio, 2012b). BO with GPs work well
for up to around 10 HPs. However, more HPs typically require more function evaluations – which
in turn is problematic runtime-wise, since GPs scale cubically with the number of dimensions.
On the other hand, BO with RFs have been used successfully on search spaces with hundreds
of HPs (Thornton et al., 2013) and can usually handle mixed hierarchical search spaces better.
Pure sampling-based methods, such as RS and Hyperband, work well even for very large HP
spaces as long as the “effective” dimension (i.e., the number of HPs that have a large impact
on performance) is low, which is often observed in ML models (Bergstra & Bengio, 2012b).
Evolutionary algorithms (and those using similar metaheuristics, such as racing) can also work
with truly large search spaces, and even with search spaces of arbitrarily complex structure if
one is willing do use non-custom mutation and crossover operators. Evolutionary algorithms
may also require fewer objective evaluations than RS. Therefore, they occupy a middle ground
between (highly complex, sample-efficient) BO and (very simple but possibly wasteful) RS.
Another property of algorithms that is especially relevant to practitioners with access to large
computational resources is parallelizability, which is discussed in Subsection 6.7.2. Furthermore,
HPO algorithms differ in their simplicity, both in terms of algorithmic principle and in terms
of usability of available implementations, which can often have implications for their usefulness
in practice. While more complex optimization methods, such as those based on BO, are often
more sample-efficient or have other desirable properties compared to simple methods, they also
have more components that can fail. When performance evaluations are cheap and the search
space is small, it may therefore be beneficial to fall back on simple and robust approaches
such as RS, Hyperband, or any tuner with minimal inference overhead. The availability of any
implementation at all (and the quality of that implementation) is also important; there may be
optimization algorithms based on beautiful theoretical principles that have a poorly maintained
implementation or are based on out-of-date software. The additional cost of having to port an
algorithm to the software platform being used, or even implement it from scratch, could be spent
on running more evaluations with an existing algorithm.
One might wish to select an HPO algorithm that performed best on previous benchmarks. However, no single benchmark exists which includes all relevant scenarios and whose results generalize
to all possible applications. Specific benchmark results can therefore only indicate how well an algorithm works for a selected set of data sets, a predefined budget, specific parallelization, specific
learners, and search spaces. Even worse, extensive comparison studies are missing in the current
literature, although efforts have been made to establish unified benchmarks. Eggensperger et al.,
2013 showed that (i) BO with GPs were a strong approach for small continuous spaces with few
evaluations, (ii) BO with Random Forests performed well for mixed spaces with a few hundred
evaluations, and (iii) for large spaces and many evaluations, ES were the best optimizers.

6.4    Choosing an Implementation
In addition to the choice of algorithm, the choice of implementation is also important in practice.
We note that a thorough comparison of all available software packages and frameworks is not
within the scope of this article. Nevertheless, we provide a brief list of popular implementations
and frameworks as reasonable starting points for newcomers.
Simple and established tuning algorithms for HPO are usually shipped with general purpose ML
frameworks such as Scikit-learn (Pedregosa et al., 2011), Tensorflow9 , PyTorch (Paszke
  9 https://www.tensorflow.org/

                                                 29

et al., 2019), MXNet (T. Chen et al., 2015), mlr3 (Lang et al., 2019; Binder et al., 2021),
tidymodels10 or h2o.ai11 . Although modern state-of-the-art algorithms often build on, extend,
or connect to such an ML framework, they are usually developed in independent software projects.
For Python, there exist a plethora of HPO toolkits, e.g., Spearmint (Snoek, Larochelle, &
Adams, 2012), SMAC12 (Hutter, Hoos, et al., 2011), BoTorch (Balandat et al., 2020), Dragonfly
(Kandasamy et al., 2020), or Orı́on13 . Multiple HPO methods are supported by toolkits like
Hyperopt (Bergstra et al., 2013), Optuna (Akiba et al., 2019) or Weights & Biases14 . A popular
framework that combines modern HPO approaches with the Scikit-learn toolbox for ML in
Python is auto-sklearn (Feurer, Klein, Eggensperger, Springenberg, et al., 2015).
The availability of HPO implementations in R is comparably smaller. However, more established
HPO tuners are either already shipped with the ML framework, or can be found, e.g., in the
packages mlrMBO15 , irace (López-Ibáñez et al., 2016), DiceOptim (Roustant et al., 2012), or
rBayesianOptimization16 .
See Appendix D for more information

6.5    When to Terminate HPO
Choosing an appropriate budget for HPO or dynamically terminating HPO based on collected
archive data is a difficult practical challenge that is not readily solved. The user typically has
these options: (i) A certain amount of runtime is specified before HPO is started, solely based
on practical considerations and intuition. This is what currently happens nearly exclusively in
practice. A simple rule-of-thumb might be scaling the budget of HPC evaluations with search
space dimensionality in a linear fashion like 50 × l or 100 × l, which would also define the
number of full budget units in a multi-fidelity setup. The downside is that while it is usually
simpler to define when a result is needed, it is nearly impossible to determine whether this
computational budget is enough to solve the HPO problem reasonably well. (ii) The user can
set a lower bound regarding the required generalization error, i.e., what model quality is deemed
good enough in order to put the resulting model to practical use. Notably, if this lower bound is
set too optimistically, the HPO process might never terminate or simply take too long. (iii) If no
(significant) progress is observed for a certain amount of time during tuning, HPO is considered
to have converged or stagnated and stopped. This criterion bears the risk of terminating too
early, especially for large and complex search spaces. Furthermore, this approach is conceptually
problematic, as it mainly makes sense for iterative, local optimizers. No HPO algorithm from this
article, except for maybe ES, belongs to this category, as they all contain a strong exploratory,
global search component. This often implies that search performance can flatline for a longer
time and then abruptly change. (iv) For BO, a small maximum value of the EI acquisition
function (see Eq. 11) indicates that further progress is estimated to be unlikely (Jones et al.,
1998). This criterion could even be combined with other, no-BO type tuners. It implies that
the surrogate model is trustworthy enough for such an estimate, including its local uncertainty
estimate. This is somewhat unlikely for large, complex search spaces. In practice, it is probably
prudent to set up a combination of all mentioned criteria (if possible) as an “and” or “or”
  10 https://www.tidymodels.org/
  11 https://github.com/h2oai/h2o-3
  12 https://github.com/automl/SMAC3
  13 https://github.com/Epistimio/orion
  14 https://www.wandb.com/
  15 https://github.com/mlr-org/mlrMBO
  16 https://github.com/yanyachen/rBayesianOptimization

                                                   30

combination, with somewhat liberal thresholds to continue the optimization for (ii), (iii), (iv),
and (v), and an absolute bound on the maximal runtime for (i).
With many HPO tuners, it is possible to continue the optimization even after it has been
terminated. One can even use (part of) the archive as an initialization of the next HPO run,
e.g., as an initial design for BO or as the initial population for an ES.
Some methods, e.g., ES and under some circumstances BO, may get stuck in a subspace of
the search space and fail to explore other regions. While some means of mitigation exist, such
as interleaving proposed points with randomly generated points, there is always the possibility
of re-starting the optimization from scratch multiple times, and selecting the best performance
from the combined runs as the final result. The decision regarding the termination criterion is
itself always a matter of cost-benefit calculation of potential increased performance against cost
of additional evaluations. One should also consider the possibility that, for more local search
strategies like ES, terminating early and restarting the optimization can be more cost-effective
than letting a single optimization run continue for much longer. For more global samplers like
BO and HB, it is much less clear whether and how such an efficient restart could be executed.

6.6    Warm-Starts
HPO may require substantial computational resources, as a large optimization search space may
require many performance evaluations, and individual performance evaluations can also be very
computationally expensive. One way to reduce the computational time are warm-starts, where
information from previous experiments are used as a starting solution.

Warm-Starting Evaluations Certain model classes may offer specific methods that reduce
the computational resources needed for training a single model by transferring model parameters
from other, similar configurations that were already trained. NNs with similar architectures
can be initialized from trained networks to reduce training time – a concept known as weight
sharing. Some neural architecture search algorithms are specifically designed to make use of this
fact (Elsken et al., 2019).

Warm-Starting Optimization Many HPO algorithms do not take any input regarding the
relative merit of different HPCs beyond box-constraints on the search space Λ̃. However, it is
often the case that some HPCs work relatively well on a large variety of data sets (Probst et al.,
2019). At other times, HPO may have been performed on other problems that are similar to the
problem currently being considered. In both cases, it is possible to warm-start the HPO process
itself – for example, by choosing the initial design as HPCs that have performed well in the past
or by choosing HPCs that are known to perform well in general (Lindauer & Hutter, 2018). This
can also be regarded as a transfer learning mechanism for HPO. Large systematic collections
of HPC performance on different data sets, such as those collected in Binder, Pfisterer, and
Bischl (2020) or on OpenML (Van Rijn et al., 2013), can be used to build these initial designs
(Pfisterer et al., 2021; Feurer, Klein, Eggensperger, Springenberg, et al., 2015).

6.7    Control of Execution
HP tuning is usually computationally expensive, and running optimization in parallel is one way
to reduce the overall time needed for a satisfactory result. During parallelization, computational
jobs (which can be arbitrary function calls) are mapped to a pool of workers (usually distributed
among individual physical CPU cores, or remote computational nodes). In practice, a parallelization backend orchestrates starting up, shutting down, and communication with the workers. The

                                               31

achieved speedup can be proportional to the number of workers in ideal cases, but less than linear
scaling is typically observed, depending on the algorithm used (Amdahl’s law, Rodgers, 1985).
Besides parallelization, ensuring a fail-safe program flow is often also mandatory, and requires
special care.
6.7.1 Job Hierarchy for HPO with Nested Resampling
HPO can be parallelized at different granularity or parallelization levels. These levels result from
the nested loop outlined in Algorithm 3 and described in detail in the following (from coarse to
fine granularity):

 Algorithm 3: Nested resampling as nested loops.
 1 foreach outer resampling iteration do
 2     Initialize archive A = {};
 3     foreach tuning iteration do
 4          {λ+,1 . . . . , λ+,nbatch } = propose points(A);
 5          foreach i ∈ {1, . . . , nbatch } do
 6              c = {};
 7              foreach inner resampling iteration do
 8                   c = c ∪ eval(λ+,i , Dinner train , Dinner test );
 9              end
10              A = A ∪ (λ+,i , agr(c));
11          end
12     end
13     λ∗ = get best(A);
14     eval(λ∗ , Dtrain , Dtest );
15 end

 (a) One iteration of the outer resampling loop (Line 1), i.e., tuning an ML algorithm on
     the respective training set of the outer resampling split. The result is the outer test set
     performance of the best HPC found and trained on the outer training set.
 (b) The execution of one tuning iteration (Line 3), i.e., the proposal and evaluation of a batch
     of new HPCs. The result is an updated optimization archive.
 (c) One evaluation of a single proposed HPC (Line 5), i.e., one inner resampling with config-
     uration λ+,i . The result is an aggregated performance score.
 (d) One iteration of the inner resampling loop (Line 7). The result is the performance score
     for a single inner train / test split.
 (e) The model fit for the evaluation of the model itself is sometimes also parallelizable (Line 8
     and 14). For example, the individual trees of a random forest can be fitted independently
     and as such are an obvious target for parallelization.
Note that the kinner resampling iterations created in (d) are independent between the nbatch
HPC evaluations created in (c). Therefore, they form nbatch · kinner independent jobs that can
be executed in parallel.
An HPO problem can now be distributed to the workers in several ways. For example, if one
wants to perform a 10-fold (outer) CV of BO that proposes one point per tuning iteration,

                                                      32

with a budget of 50 HP evaluations done via a 3-fold CV (inner resampling), one can decide to
(i) spawn 10 parallel jobs (level (a)) with a long runtime once, or to (ii) spawn 3 parallel jobs
(level (d)) with a short runtime 50 times.
Consider another example with an ES running for 50 generations with an offspring size of 20.
This translates to 50 tuning iterations with 20 HPC proposals per iteration. If this is evaluated
using nested CV, with 10 outer and 3 inner resampling loops, then the parallelization options
are: (i) spawn 10 parallel jobs with a long runtime once (level (a)), (ii) spawn 20 parallel jobs
(level (c)) 10 · 50 times with a medium runtime, (iii) spawn 3 parallel jobs (level (d)) with a short
runtime 10 · 50 · 20 times, or (iv) spawn 20 · 3 = 60 parallel jobs (level (d) and (c) combined)
with a short runtime 10 · 50 times.
6.7.2 Parallelizability
These examples demonstrate that the choice of the parallelization level also depends on the
choice of the tuner. Parallelization for RS and GS is so straightforward that it is also called
“embarrassingly parallel”, since all HPCs to be evaluated can be determined in advance as they
have only one (trivial) tuning iteration (level (b)). Algorithms based on iterations over batch
proposals (ES, BO, IR, Hyperband) are limited in how many parallel workers they can use and
profit from: they can be parallelized up to their (current) batch size, which decreases for HB
and IR during a bracket/race. The situation looks very different for BO; by construction, it is
a sequential algorithm with a batch size of 1. Multi-point proposals (see 4.2.3) must be used
to parallelize multiple configurations in each iteration (level (c)). However, parallelization does
not scale very well for this level. If possible, a different parallelization level should be chosen to
achieve a similar speedup but avoid the problems of multi-point proposal.
Therefore, if a truly large number of parallel resources is available, then the best optimization
method may be RS due to its relatively boundless parallelizability and low synchronization overhead. With fewer parallel resources, more efficient algorithms have a larger relative advantage.
6.7.3 Parallelization Tweaks
The more jobs generated, the more workers can be utilized in general, and the better the tuning
tends to scale with available computational resources. However, there are some caveats and
technical difficulties to consider. First, the process of starting a job, communicating the inputs
to the worker, and communicating the results back often comes with considerable overhead,
depending on the parallelization backend used. To maximize utilization and minimize overhead,
some backends therefore allow chunking multiple jobs together into groups that are calculated
sequentially on the workers. Second, whenever a batch of parallel jobs is started, the main
process must wait for all of them to finish, which leads to synchronization overhead if nodes
that finish early are left idling because they wait for longer running jobs to finish. This can be
a particular problem for HPO, where it is likely that the jobs have heterogeneous runtimes. For
example, fitting a boosting model with 10 iterations will be significantly faster than fitting a
boosting model with 10,000 iterations. Unwittingly chunking many jobs together can exacerbate
synchronization overhead and lead to a situation where most workers idle and wait for one worker
to finish a relatively large chunk of jobs. While there are approaches to mitigate such problems,
as briefly discussed in Section 4.2.3 for BO, it is often sufficient – as a rule of thumb – to aim
for as many jobs as possible, as long as each individual job has an expected average runtime of
≥ 5 minutes. Additionally, randomizing the order of jobs can increase the utilization of search
strategies such as GS.

                                                 33

Budget and Overhead For an unbiased comparison, it is mandatory that all tuners are granted
the same budget. However, if the budget is defined by a fixed number evaluations, it is unclear
how to count multi-fidelity evaluations and how to compare them against “regular” evaluations.
We recommend comparing evaluations by wall-clock time, and to explicitly report overhead for
model inference and point proposal.

Anytime Behavior In practice, it is unclear how long tuners will and should be run. Thus,
when benchmarking tuners, the performance of the optimization process should be considered
at many stages. To achieve this objective, one should not (only) compare the final performance
at the maximum budget, but rather compare the whole optimization traces from initialization
to termination. Furthermore, tuning runs must be terminated if the wall-clock time is exceeded,
which may leave the tuner in an undefined state and incapable of extracting a final result.

Parallelization The effect of parallelization must be factored in17 . While some tuners scale
linearly with the number of workers without a degradation in performance (e.g., GS), other
tuners (e.g., BO) show diminishing returns from excessive parallelism. A fair comparison w.r.t.
a time budget is also hampered by technical aspects like cache interference or difficult to control
parallelization of the ML algorithms to tune.

6.8     Simple Sequential Approach to Model Selection
In practical model selection, it is often the case that practitioners care not only about predictive
performance, but also about other aspects of the model such as its simplicity and interpretability.
If a practitioner considers only a low number of different model classes, then a practical alternative
to AutoML on a full pipeline space of different ML models is to construct a manual sequence
of preferred models of increasing complexity, to separately tune them, and to compare their
performance. An informed choice can then be made about how much additional complexity
is acceptable for a certain amount of improved predictive performance. Hastie et al. (2009)
suggest the “one-standard-error rule” and use the simplest model within one standard error of
the performance of the best model. The specific sequence of learners will differ for each use case
but might look something like this: (1) linear model with only a single feature or tree stump,
(2) L1/L2 regularized linear model or a CART tree, (3) component-wise boosting of a GAM, (4)
random forest, (5) gradient tree boosting, and (6) full AutoML search space with pipelines. If
even more focus is put on predictive performance (multi-level), stacking a diverse set of models
can give a final (often rather small) boost (Wolpert, 1992).

6.9     Benchmarking HPO Algorithms
While few well-designed large-scale benchmarks of HPO exist (Klein et al., 2019; Eggensperger
et al., 2018; Eggensperger et al., 2013), there is still an ongoing endeavour of the community
for better and more comprehensive studies. Usually, tuners are compared on fixed sets of data
sets and search spaces or cheaper approximations via empirical performance models / surrogates
(Eggensperger et al., 2018). It is still unclear how an ideal and representative benchmark set for
HPO that also enables efficient benchmarking might look like.
  17 At least if the budget is defined by available wall-clock time

                                                        34

7    Related Problems
Besides HPO, there are many related scientific fields that face very similar problems on an
abstract level and nowadays resort to techniques that are very similar to those described in
this work. Although detailed coverage of these related areas is out of scope for this paper, we
nevertheless briefly summarize and compare them to HPO.

Neural Architecture Search A specific type of HPO is neural architecture search (NAS)
(Elsken et al., 2019), where the task is to find a well-performing architecture of a deep NN for a
given data set. Although NAS can also be formulated as an HPO problem (e.g., Zimmer et al.,
2021), it is usually approached as a bi-level optimization problem that can be simultaneously
solved while training the NN (Liu et al., 2019). Although it is common practice to optimize architecture and HPCs in sequence, recent evidence suggests that they should be jointly optimized
(Zela et al., 2018; Lindauer & Hutter, 2020).

Algorithm Selection and Traditional Meta-Learning In HPO, we actively search for a wellperforming HPC through iterative optimization. In the related problem of algorithm selection
(Rice, 1976), we usually train a meta-learning model offline to select an optimal element from
a finite set of algorithms or HPCs (Bischl et al., 2016). This model can be trained on empirical
meta-data of the candidates’ performances on a large set of problem instances or data sets.
While this allows an instantaneous prediction of a model class and/or a configuration for a
new data set without any time investment, these meta-models are rarely sufficiently accurate
in practice, and modern approaches use them mainly to warmstart HPO (Feurer, Springenberg,
et al., 2015) in a hybrid manner.

Algorithm Configuration Algorithm configuration (AC) is the general task of searching for
a well-performing parameter configuration of an arbitrary algorithm for a given, finite, arbitrary
set of problem instances. We assume that the performance of a configuration can be quantified
by some scoring metric for any given instance (Hutter et al., 2006). Usually, this performance
can only be accessed empirically in a black-box manner and comes at the cost of running
this algorithm. This is sometimes called offline configuration for a set of instances, and the
obtained configuration is then kept fixed for all future problem instances from the same domain.
Alternatively, algorithms can also be configured per instance. AC is much broader than HPO,
and has a particularly rich history regarding the configuration of discrete optimization solvers
(Xu et al., 2008). In HPO, we typically search for a well-performing HPC on a single data set,
which can be seen as a case of per-instance configuration. The case of finding optimal pipelines
or default configurations (Pfisterer et al., 2021) across a larger domain of ML tasks is much
closer to traditional AC across sets of instances. The field of AutoML as introduced in Section 5
originally stems from AC and was initially introduced as the Combined Algorithm Selection and
Hyperparameter Optimization (CASH) problem (Thornton et al., 2013).

Dynamic Algorithm Configuration In HPO (and AC), we assume that th e HPC is chosen
once for the entire training of an ML model. However, many HPs can actually be adapted
while training. A well-known example is the learning rate of an NN optimizer, which might
be adapted via heuristics (Kingma & Ba, 2015) or pre-defined schedules (Loshchilov & Hutter,
2017). However, both are potentially sub-optimal. A more adaptive view on HPO is called
dynamic algorithm configuration (Biedenkapp et al., 2020). This allows a policy to be learned
(e.g., based on reinforcement learning) for mapping a state of the learner to an HPC, e.g.,

                                               35

the learning rate (Daniel et al., 2016). We note that dynamic algorithm configuration is a
generalization of algorithm selection and configuration (and thus HPO) (Speck et al., 2021).

Learning to Learn and to Optimize Y. Chen et al. (2017) and K. Li and Malik (2017)
considered methods beyond simple HPO for fixed hyperparameters, and proposed replacing
entire components of learners and optimizers. For example, Y. Chen et al. (2017) proposed
using an LSTM to learn how to update the weights of an NN. In contrast, K. Li and Malik
(2017) applied reinforcement learning to learn where to sample next in black-box optimization
such as HPO. Both of these approaches attempt to learn these meta-learners on diverse sets
of applications. While it is already non-trivial to select an HPO algorithm for a given problem,
such meta-learning approaches face the even greater challenge of generalizing to new (previously
unseen) tasks.

8    Conclusion and Open Challenges
This work has sought to provide an overview of the fundamental concepts and algorithms for
HPO in ML. Although HPO can be applied to a single ML algorithm, state-of-the-art systems
typically optimize the entire predictive pipeline – including pre-processing, model selection, and
post-processing. By using a structured search space, even complex optimization tasks can be
efficiently optimized by HPO techniques. This final section now outlines several open challenges
in HPO that we deem particularly important.

General vs. Narrow HPO Frameworks For HPO tools, there is a general trade-off between
handling many tasks (such as auto-sklearn (Feurer, Klein, Eggensperger, Springenberg, et al.,
2015)) and a specializing for few and narrow-focused tasks. The former has the advantage
that it can be applied more flexibly, but comes with the disadvantages that (i) it requires
more development time to initially set it up and (ii) that the search space is larger such that
the efficiency might be sub-optimal compared to a task-specific approach. The latter has the
advantage that a more specialized search space can lead to a higher efficiency on a specific task
but might not be applicable to a different task. When a specialized tool for an HPO problem
can be found, it can often preferred to generalized tools.

Interactive HPO It is still unclear how HPO tools can be fully integrated into the exploratory
and prototype-driven workflow of data scientists and ML experts. On the one hand, it is desirable
to support these experts in tuning HPs to avoid this tedious and error-prone task. On the other
hand, this can lead to lengthy periods of waiting that interrupt the workflow of the data scientist,
ranging from a few minutes on smaller data sets and simpler search spaces, or for hours or
even days for large-scale data and complex pipelines. Therefore, the open problems remains of
how HPO approaches can be designed so that users can monitor progress in an efficient and
transparent manner and how to enable interactions with a running HPO tool in case the desired
progress or solutions are not achieved.

HPO for Deep Learning and Reinforcement Learning For many applications of reinforcement learning and deep learning, especially in the field of natural language processing and large
computer vision models, expensive training often prohibits several several training runs with different HPCs. Iterative HPO for such computationally extremely expensive tasks is infeasible even
with efficient approaches like BO and multifidelity variants. There are three ways to address this

                                                36

issue. First, gradient-based approaches can directly make use of gradient-information on how to
update HPs (Maclaurin et al., 2015; Franceschi et al., 2017; Thiede & Parlitz, 2019). However,
gradient-based HPO is only applicable to a few HPs and requires specific gradient information
which is often only available in deep NNs. Second, some models do not have to be trained
from scratch by applying transfer learning or few-shot learning, e.g., Finn et al. (2017). This
allows cost-effective applications of these models without incredibly expensive training. Using
meta-learning techniques for HPO is an option and can also be further improved if gradientbased HPO or NAS is feasible (Franceschi et al., 2018; Elsken et al., 2020). Third, dynamic
configuration approaches allow application of HPO on the fly while training. A very prominent
example is population-based training (PBT) (A. Li et al., 2019), which uses a population of
training runs with different settings and applies ideas from evolutionary algorithms (especially
mutation and tournament selection) from time to time while the model training makes progress.
The disadvantage is that this method requires a substantial amount of computational resources
for training several models in parallel. This can be reduced by combining PBT with ideas from
bandits and BO (Parker-Holder et al., 2020).

Overtuning and Regularization for HPO As discussed in 4.4, long HPO runs can lead to
biased performance estimators, which in the end can also lead to incorrect HPC selection. It
seems plausible that this effect is increasingly exacerbated the smaller the data and the less
iterations of resampling the user has configured.18 It seems even more plausible that better
testing of results (on even more separate validation data) can mitigate or resolve the issue, but
this makes data usage even less efficient in HPO, as we are already operating with 3-ways splits.
HPO tools should probably more intelligently control resampling by increasing the number of
folds more and more the longer the tuning process runs. However, determining how to ideally
set up such a schedule seems to be an under-explored issue.

Interpretability of HPO Process Many HPO approaches mainly return a well-performing
HPC and leave users without insights into decisions of the optimization process. Not all data
scientists trust the outcome of an AutoML system due to the lack of transparency (Drozdal
et al., 2020). Consequently, they might not deploy an AutoML model despite all performance
gains. In addition, larger performance gains could potentially be generated (especially by avoiding
meta-configuration problems of HPO) if a user is presented with a better understanding of the
functional landscape of the objective c(λ), the sampling evolution of the HPO algorithm, the
importance of HPs, or their effects on the resulting performance. Hence, in future work, HPO
may need to be combined more often with approaches from interpretable ML and sensitivity
analysis.

Multi-Criteria HPO and Model Simplicity Until here, we mainly considered the scenario
of having one well-defined metric for predictive performance available to guide HPO. However,
in practical applications, there is often an unknown trade-off between multiple metrics at play,
even when only considering predictive performance (e.g., consider an imbalanced multiclass task
with unknown misclassification costs). Additionally, there often exists an inherent preference
towards simple, interpretable, efficient, and sparse solutions. Such solutions are easier to debug,
deploy and maintain, as well as assist in overcoming the “last-mile” problem of integrating
ML systems in business workflows (Chui et al., 2018). Since the optimal trade-off between
   18 Issues like imbalance of a classification task and non-standard resampling and evaluation metrics can com-

plicate this matter further.

                                                      37

predictive performance and simplicity is usually unknown a-priori, an attractive approach is
multi-criteria HPO in order to learn the HPCs of all Pareto optimal trade-offs (Horn & Bischl,
2016; Binder, Moosbauer, et al., 2020). Such multi-criteria approaches introduce a variety of
additional challenges for HPO that go beyond the scope of this paper. Recently, model distillation
approaches have been proposed to extract a simple(r) model from a complex ensemble (Fakoor
et al., 2020).

Optimizing for Interpretable Models and Sparseness A different challenge regarding interpretability is to bias the HPO process towards more interpretable models and return them
if their predictive performance is not significantly and/or relevantly worse than that of a less
understandable model. To integrate the search for interpretable models into HPO, it would be
beneficial if the interpretability of an ML model could be quantitatively measured. This would
allow direct optimization against such a metric (Molnar et al., 2019), e.g., using multi-criteria
HPO methods.

HPO Beyond Supervised Learning This paper discussed HPO for supervised ML. Most
algorithms from other sub-fields of ML, such as clustering (unsupervised) or anomaly detection
(semi- or unsupervised), are also configurable by HPs. In these settings, the HPO techniques
discussed in this paper can hardly be used effectively, since performance evaluation, especially
with a single, well-defined metric, is much less clear. Nevertheless, HPO is ready to be used.
With the seminal paper by Bergstra and Bengio (2012b), HPO again gained exceptional attention
in the last decade. Furthermore, this development sparked tremendous progress both in terms
of efficiency and in applicability of HPO. Currently, there are many HPO packages in various
programming languages readily available that allow easy application of HPO to a large variety
of problems.

Funding Resources
The authors of this work take full responsibilities for its content. This work was supported by
the Federal Statistical Office of Germany; the Deutsche Forschungsgemeinschaft (DFG) within
the Collaborative Research Center SFB 876, A3; the German Federal Ministry of Education and
Research (BMBF) under Grant No. 01IS18036A; and the Bavarian Ministry for Economic Affairs,
Infrastructure, Transport and Technology through the Center for Analytics-Data-Applications
(ADA-Center) within the framework of “BAYERN DIGITAL II”.

Acknowledgments
We would like to thank Eyke Hüllermeier, Lars Kothoff, Jürgen Branke and Eduardo C. Garrido-
Merchán and for their valuable feedback on the manuscript.

References
Akiba, T., Sano, S., Yanase, T., Ohta, T., & Koyama, M. (2019). Optuna: A Next-Generation
        Hyperparameter Optimization Framework. Proceedings of the 25th ACM SIGKDD In-
        ternational Conference on Knowledge Discovery & Data Mining, 2623–2631.
Andonie, R. (2019). Hyperparameter optimization in learning systems. J. Membr. Comput., 1 (4),
        279–291.

                                               38

Antonov, I. A., & Saleev, V. (1979). An economic method of computing LPτ -sequences. USSR
         Computational Mathematics and Mathematical Physics, 19 (1), 252–256.
Awad, N., Mallik, N., & Hutter, F. (2021). DEHB: Evolutionary Hyberband for Scalable, Robust
         and Efficient Hyperparameter Optimization. In Z.-H. Zhou (Ed.), Proceedings of the
         Thirtieth International Joint Conference on Artificial Intelligence, IJCAI (pp. 2147–
         2153). ijcai.org.
Balandat, M., Karrer, B., Jiang, D. R., Daulton, S., Letham, B., Wilson, A. G., & Bakshy, E.
         (2020). BoTorch: A Framework for Efficient Monte-Carlo Bayesian Optimization. In H.
         Larochelle, M. Ranzato, R. Hadsell, M. F. Balcan, & H. Lin (Eds.), Advances in Neural
         Information Processing Systems.
Bartz, E., Zaefferer, M., Mersmann, O., & Bartz-Beielstein, T. (2021). Experimental Investiga-
         tion and Evaluation of Model-based Hyperparameter Optimization.
Bellman, R. E. (2015). Adaptive Control Processes. Princeton University Press.
Bengio, Y., & Grandvalet, Y. (2004). No unbiased estimator of the variance of k-fold cross-
         validation. Journal of Machine Learning Research, 5 (Sep), 1089–1105.
Bergmeir, C., Hyndman, R. J., & Koo, B. (2018). A note on the validity of cross-validation
         for evaluating autoregressive time series prediction. Computational Statistics & Data
         Analysis, 120, 70–83.
Bergstra, J., & Bengio, Y. (2012a). Random Search for Hyper-Parameter Optimization. Journal
         of Machine Learning Research, 13, 281–305.
Bergstra, J., & Bengio, Y. (2012b). Random Search for Hyper-Parameter Optimization. Journal
         of Machine Learning Research, 13 (10), 281–305.
Bergstra, J., Yamins, D., & Cox, D. (2013). Making a Science of Model Search: Hyperparameter
         Optimization in Hundreds of Dimensions for Vision Architectures. In S. Dasgupta &
         D. McAllester (Eds.), Proceedings of the 30th International Conference on Machine
         Learning (pp. 115–123). PMLR.
Beyer, H.-G., & Schwefel, H.-P. (2002). Evolution strategies - A comprehensive introduction.
         Natural Computing, 1, 3–52.
Beyer, H.-G., & Sendhoff, B. (2006). Evolution Strategies for Robust Optimization. 2006 IEEE
         International Conference on Evolutionary Computation, 1346–1353.
Biedenkapp, A., Bozkurt, H. F., Eimer, T., Hutter, F., & Lindauer, M. (2020). Dynamic Al-
         gorithm Configuration: Foundation of a New Meta-Algorithmic Framework. In G. D.
         Giacomo, A. Catala, B. Dilkina, M. Milano, S. Barro, A. Bugarin, & J. Lang (Eds.),
         ECAI 2020 - 24th European Conference on Artificial Intelligence, 29 August-8 September
         2020, Santiago de Compostela, Spain, August 29 - September 8, 2020 - Including 10th
         Conference on Prestigious Applications of Artificial Intelligence (PAIS 2020) (pp. 427–
         434). IOS Press.
Binder, M., Moosbauer, J., Thomas, J., & Bischl, B. (2020). Multi-objective hyperparameter
         tuning and feature selection using filter ensembles. Proceedings of the 2020 Genetic and
         Evolutionary Computation Conference, 471–479.
Binder, M., Pfisterer, F., & Bischl, B. (2020). Collecting Empirical Data About Hyperparame-
         ters for Data Driven AutoML. Proceedings of the 7th ICML Workshop on Automated
         Machine Learning (AutoML 2020).
Binder, M., Pfisterer, F., Lang, M., Schneider, L., Kotthoff, L., & Bischl, B. (2021). mlr3pipelines
         - Flexible Machine Learning Pipelines in R. Journal of Machine Learning Research,
         22 (184), 1–7.

                                                39

Birattari, M., Stützle, T., Paquete, L., & Varrentrapp, K. (2002). A Racing Algorithm for Con-
         figuring Metaheuristics. Proceedings of the 4th Annual Conference on Genetic and
         Evolutionary Computation, 11–18.
Birattari, M., Yuan, Z., Balaprakash, P., & Stützle, T. (2010). F-Race and iterated F-Race: An
         overview. In T. Bartz-Beielstein, M. Chiarandini, L. Paquete, & M. Preuss (Eds.), Ex-
         perimental methods for the analysis of optimization algorithms (pp. 311–336). Springer
         Berlin Heidelberg.
Bischl, B., Mersmann, O., Trautmann, H., & Weihs, C. (2012). Resampling Methods for Meta-
         Model Validation with Recommendations for Evolutionary Computation. Evolutionary
         Computation, 20 (2), 249–275.
Bischl, B., Kerschke, P., Kotthoff, L., Lindauer, M., Malitsky, Y., Fréchette, A., Hoos, H. H.,
         Hutter, F., Leyton-Brown, K., Tierney, K., & Vanschoren, J. (2016). ASlib: A benchmark
         library for algorithm selection. Artif. Intell., 237, 41–58.
Bischl, B., Wessing, S., Bauer, N., Friedrichs, K., & Weihs, C. (2014). MOI-MBO: Multiobjec-
         tive Infill for Parallel Model-Based Optimization. Learning and Intelligent Optimization
         Conference, 173–186.
Bossek, J., Doerr, C., & Kerschke, P. (2020). Initial Design Strategies and Their Effects on
         Sequential Model-Based Optimization: An Exploratory Case Study Based on BBOB.
         Proceedings of the 2020 Genetic and Evolutionary Computation Conference, 778–786.
Boulesteix, A.-L., Strobl, C., Augustin, T., & Daumer, M. (2008). Evaluating microarray-based
         classifiers: an overview. Cancer Informatics, 6, 77–97.
Chen, T., Li, M., Li, Y., Lin, M., Wang, N., Wang, M., Xiao, T., Xu, B., Zhang, C., & Zhang, Z.
         (2015). MXNet: A Flexible and Efficient Machine Learning Library for Heterogeneous
         Distributed Systems. CoRR, abs/1512.01274.
Chen, Y., Hoffman, M. W., Colmenarejo, S. G., Denil, M., Lillicrap, T. P., Botvinick, M., & de
         Freitas, N. (2017). Learning to Learn without Gradient Descent by Gradient Descent.
         In D. Precup & Y. W. Teh (Eds.), Proceedings of the 34th International Conference on
         Machine Learning, ICML 2017, Sydney, NSW, Australia, 6-11 August 2017 (pp. 748–
         756). PMLR.
Chevalier, C., & Ginsbourger, D. (2013). Fast Computation of the Multi-Points Expected Im-
         provement with Applications in Batch Selection. Learning and Intelligent Optimization,
         59–69.
Chui, M., Manyika, J., Miremadi, M., Henke, N., Chung, R., Nel, P., & Malhotra, S. (2018).
         Notes from the AI frontier: Insights from hundreds of use cases. McKinsey Global Insti-
         tute.
Coello Coello, C. A., Lamont, G. B., & Van Veldhuizen, D. A. (2007). Evolutionary algorithms
         for solving multi-objective problems. Springer.
Daniel, C., Taylor, J., & Nowozin, S. (2016). Learning Step Size Controllers for Robust Neural
         Network Training. In D. Schuurmans & M. P. Wellman (Eds.), Proceedings of the
         Thirtieth AAAI Conference on Artificial Intelligence, February 12-17, 2016, Phoenix,
         Arizona, USA (pp. 1519–1525). AAAI Press.
De Ath, G., Everson, R. M., Rahat, A. A., & Fieldsend, J. E. (2021). Greed is good: Exploration
         and exploitation trade-offs in Bayesian optimisation. ACM Transactions on Evolutionary
         Learning and Optimization, 1 (1), 1–22.
Dobbin, K. K., & Simon, R. M. (2011). Optimally splitting cases for training and testing high
         dimensional classifiers. BMC medical genomics, 4 (1), 1–8.
Drozdal, J., Weisz, J. D., Wang, D., Dass, G., Yao, B., Zhao, C., Muller, M. J., Ju, L., &
         Su, H. (2020). Trust in AutoML: exploring information needs for establishing trust in

                                               40

         automated machine learning systems. In F. Paternò, N. Oliver, C. Conati, L. D. Spano, &
         N. Tintarev (Eds.), IUI ’20: 25th International Conference on Intelligent User Interfaces,
         Cagliari, Italy, March 17-20, 2020 (pp. 297–307). ACM.
Eggensperger, K., Feurer, M., Hutter, F., Bergstra, J., Snoek, J., Hoos, H., & Leyton-Brown,
         K. (2013). Towards an empirical foundation for assessing bayesian optimization of hy-
         perparameters. NIPS workshop on Bayesian Optimization in Theory and Practice, 10,
         3.
Eggensperger, K., Lindauer, M., Hoos, H. H., Hutter, F., & Leyton-Brown, K. (2018). Efficient
         benchmarking of algorithm configurators via model-based surrogates. Mach. Learn.,
         107 (1), 15–41.
Elshawi, R., Maher, M., & Sakr, S. (2019). Automated Machine Learning: State-of-The-Art and
         Open Challenges.
Elsken, T., Staffler, B., Metzen, J. H., & Hutter, F. (2020). Meta-Learning of Neural Archi-
         tectures for Few-Shot Learning. 2020 IEEE/CVF Conference on Computer Vision and
         Pattern Recognition, CVPR 2020, Seattle, WA, USA, June 13-19, 2020, 12362–12372.
Elsken, T., Metzen, J. H., & Hutter, F. (2019). Neural Architecture Search: A Survey. Journal
         of Machine Learning Research, 20 (55), 1–21.
Eriksson, D., Pearce, M., Gardner, J., Turner, R. D., & Poloczek, M. (2019). Scalable Global Op-
         timization via Local Bayesian Optimization. Advances in Neural Information Processing
         Systems, 5496–5507.
Escalante, H. J., Montes, M., & Sucar, L. E. (2009). Particle swarm model selection. Journal of
         Machine Learning Research, 10 (2).
Fakoor, R., Mueller, J. W., Erickson, N., Chaudhari, P., & Smola, A. J. (2020). Fast, Accurate,
         and Simple Models for Tabular Data via Augmented Distillation. Advances in Neural
         Information Processing Systems, 33.
Falkner, S., Klein, A., & Hutter, F. (2018). BOHB: Robust and Efficient Hyperparameter Opti-
         mization at Scale (J. Dy & A. Krause, Eds.). 80, 1437–1446.
Fernández-Delgado, M., Cernadas, E., Barro, S., & Amorim, D. (2014). Do We Need Hundreds
         of Classifiers to Solve Real World Classification Problems? The Journal of Machine
         Learning Research, 15 (1), 3133–3181.
Feurer, M., & Hutter, F. (2019). Hyperparameter Optimization. In F. Hutter, L. Kotthoff, &
         J. Vanschoren (Eds.), AutoML: Methods, Sytems, Challenges (pp. 3–33). Springer.
Feurer, M., Klein, A., Eggensperger, K., Springenberg, J., Blum, M., & Hutter, F. (2015).
         Efficient and Robust Automated Machine Learning (C. Cortes, N. Lawrence, D. Lee,
         M. Sugiyama, & R. Garnett, Eds.), 2962–2970.
Feurer, M., Klein, A., Eggensperger, K., Springenberg, J. T., Blum, M., & Hutter, F. (2015). Ef-
         ficient and Robust Automated Machine Learning. Proceedings of the 28th International
         Conference on Neural Information Processing Systems - Volume 2, 2755–2763.
Feurer, M., Springenberg, J. T., & Hutter, F. (2015). Initializing Bayesian Hyperparameter
         Optimization via Meta-Learning. In B. Bonet & S. Koenig (Eds.), Proceedings of the
         Twenty-Ninth AAAI Conference on Artificial Intelligence, January 25-30, 2015, Austin,
         Texas, USA (pp. 1128–1135). AAAI Press.
Finn, C., Abbeel, P., & Levine, S. (2017). Model-agnostic meta-learning for fast adaptation of
         deep networks (D. Precup & Y. Teh, Eds.). 70, 1126–1135.
Franceschi, L., Donini, M., Frasconi, P., & Pontil, M. (2017). Forward and Reverse Gradient-
         Based Hyperparameter Optimization (D. Precup & Y. Teh, Eds.). 70, 1165–1173.

                                                41

Franceschi, L., Frasconi, P., Salzo, S., Grazzi, R., & Pontil, M. (2018). Bilevel Programming
         for Hyperparameter Optimization and Meta-Learning (J. Dy & A. Krause, Eds.). 80,
         1568–1577.
Garrido-Merchán, E. C., & Hernández-Lobato, D. (2020). Dealing with categorical and integer-
         valued variables in bayesian optimization with gaussian processes. Neurocomputing, 380,
         20–35.
Gijsbers, P., Pfisterer, F., van Rijn, J., Bischl, B., & Vanschoren, J. (2021). Meta-learning for
         symbolic hyperparameter defaults, 151–152.
Ginsbourger, D., Le Riche, R., & Carraro, L. (2010). Kriging Is Well-Suited to Parallelize Op-
         timization. In Y. Tenne & C.-K. Goh (Eds.), Computational Intelligence in Expensive
         Optimization Problems (pp. 131–162). Springer Berlin Heidelberg.
Guyon, I., Saffari, A., Dror, G., & Cawley, G. (2010). Model selection: Beyond the Bayesian/frequentist
         divide. The Journal of Machine Learning Research, 11 (3), 61–87.
Hancock, J. T., & Khoshgoftaar, T. M. (2020). Survey on categorical data for neural networks.
         Journal of Big Data, 7 (1).
Hastie, T., Tibshirani, R., & Friedman, J. (2009). The elements of statistical learning: data
         mining, inference, and prediction. Springer Science & Business Media.
He, X., Zhao, K., & Chu, X. (2021a). AutoML: A survey of the state-of-the-art. Knowl. Based
         Syst., 212, 106622.
He, X., Zhao, K., & Chu, X. (2021b). AutoML: A survey of the state-of-the-art. Knowledge-
         Based Systems, 212, 106622.
Hennig, P., & Schuler, C. J. (2012). Entropy Search for Information-Efficient Global Optimiza-
         tion. Journal of Machine Learning Research, 13 (6), 1809–1837.
Hernández-Lobato, D., Hernandez-Lobato, J., Shah, A., & Adams, R. (2016). Predictive entropy
         search for multi-objective bayesian optimization. International Conference on Machine
         Learning, 1492–1501.
Horn, D., & Bischl, B. (2016). Multi-objective parameter configuration of machine learning algo-
         rithms using model-based optimization. 2016 IEEE symposium series on computational
         intelligence (SSCI), 1–8.
Hornung, R., Bernau, C., Truntzer, C., Wilson, R., Stadler, T., & Boulesteix, A.-L. (2015).
         A measure of the impact of CV incompleteness on prediction error estimation with
         application to PCA and normalization. BMC Medical Research Methodology, 15, 95.
Hutter, F., Hoos, H., & Leyton-Brown, K. (2011). Sequential Model-Based Optimization for
         General Algorithm Configuration (C. Coello, Ed.). 6683, 507–523.
Hutter, F., Kotthoff, L., & Vanschoren, J. (Eds.). (2019). Automated Machine Learning: Meth-
         ods, Systems, Challenges. Springer.
Hutter, F., Hamadi, Y., Hoos, H. H., & Leyton-Brown, K. (2006). Performance prediction and
         automated tuning of randomized and parametric algorithms. International Conference
         on Principles and Practice of Constraint Programming, 213–228.
Hutter, F., Hoos, H. H., & Leyton-Brown, K. (2011). Sequential model-based optimization for
         general algorithm configuration. In C. A. Coello Coello (Ed.), International conference
         on learning and intelligent optimization (pp. 507–523). Springer.
Hutter, F., Hoos, H. H., & Leyton-Brown, K. (2012). Parallel Algorithm Configuration. In Y.
         Hamadi & M. Schoenauer (Eds.), Learning and Intelligent Optimization (pp. 55–70).
         Springer Berlin Heidelberg.
Hutter, F., Hoos, H. H., Leyton-Brown, K., & Stützle, T. (2009). ParamILS: An Automatic
         Algorithm Configuration Framework. J. Artif. Intell. Res., 36, 267–306.

                                               42

Jalali, H., Van Nieuwenhuyse, I., & Picheny, V. (2017). Comparison of kriging-based algorithms
          for simulation optimization with heterogeneous noise. European Journal of Operational
          Research, 261 (1), 279–301.
Jamieson, K., & Talwalkar, A. (2016). Non-stochastic Best Arm Identification and Hyperpa-
          rameter Optimization. Proceedings of the 19th International Con-ference on Artificial
          Intelligence and Statistics (AISTATS), 240–248.
Japkowicz, N., & Shah, M. (2011). Evaluating Learning Algorithms: A Classification Perspective.
          Cambridge University Press.
Jasrasaria, D., & Pyzer-Knapp, E. O. (2018). Dynamic Control of Explore/Exploit Trade-Off in
          Bayesian Optimization. Science and Information Conference, 1–15.
Jones, D. R. (2001). A Taxonomy of Global Optimization Methods Based on Response Surfaces.
          Journal of Global Optimization, 21 (4), 345–383.
Jones, D. R. (2009). Direct Global Optimization Algorithm. Encyclopedia of optimization, 1 (1),
          431–440.
Jones, D. R., Schonlau, M., & Welch, W. J. (1998). Efficient Global Optimization of Expensive
          Black-Box Functions. Journal of Global Optimization, 13 (4), 455–492.
Kandasamy, K., Vysyaraju, K. R., Neiswanger, W., Paria, B., Collins, C. R., Schneider, J., Poczos,
          B., & Xing, E. P. (2020). Tuning Hyperparameters without Grad Students: Scalable and
          Robust Bayesian Optimisation with Dragonfly. Journal of Machine Learning Research,
          21 (81), 1–27.
Khalid, R., & Javaid, N. (2020). A survey on hyperparameters optimization algorithms of fore-
          casting models in smart grid. Sustainable Cities and Society, 61, 102275.
Kingma, D. P., & Ba, J. (2015). Adam: A Method for Stochastic Optimization. In Y. Bengio
          & Y. LeCun (Eds.), 3rd International Conference on Learning Representations, ICLR
          2015, San Diego, CA, USA, May 7-9, 2015, Conference Track Proceedings.
Klein, A., Falkner, S., Bartels, S., Hennig, P., & Hutter, F. (2017a). Fast Bayesian Optimization
          of Machine Learning Hyperparameters on Large Datasets (A. Singh & J. Zhu, Eds.).
          54.
Klein, A., Dai, Z., Hutter, F., Lawrence, N., & Gonzalez, J. (2019). Meta-surrogate benchmarking
          for hyperparameter optimization. Advances in Neural Information Processing Systems,
          32, 6270–6280.
Klein, A., Falkner, S., Bartels, S., Hennig, P., & Hutter, F. (2017b). Fast bayesian optimization
          of machine learning hyperparameters on large datasets. In A. Singh & J. Zhu (Eds.),
          Proceedings of the 20th International Conference on Artificial Intelligence and Statistics
          (pp. 528–536). PMLR.
Kohavi, R. (1995). A study of cross-validation and bootstrap for accuracy estimation and model
          selection. International Joint Conference on Artificial Intelligence (IJCAI), 1137–1143.
Lang, M., Kotthaus, H., Marwedel, P., Weihs, C., Rahnenführer, J., & Bischl, B. (2015). Au-
          tomatic model selection for high-dimensional survival analysis. Journal of Statistical
          Computation and Simulation, 85 (1), 62–76.
Lang, M., Binder, M., Richter, J., Schratz, P., Pfisterer, F., Coors, S., Au, Q., Casalicchio, G.,
          Kotthoff, L., & Bischl, B. (2019). mlr3: A modern object-oriented machine learning
          framework in R. Journal of Open Source Software, 4 (44), 1903.
Larrañaga, P., & Lozano, J. A. (2001). Estimation of distribution algorithms: A new tool for
          evolutionary computation (Vol. 2). Springer Science & Business Media.
Li, A., Spyra, O., Perel, S., Dalibard, V., Jaderberg, M., Gu, C., Budden, D., Harley, T., & Gupta,
          P. (2019). A Generalized Framework for Population Based Training. Proceedings of the

                                                43

         25th ACM SIGKDD International Conference on Knowledge Discovery & Data Mining,
         KDD 2019, Anchorage, AK, USA, August 4-8, 2019, 1791–1799.
Li, K., & Malik, J. (2017). Learning to Optimize. 5th International Conference on Learning
         Representations, ICLR 2017, Toulon, France, April 24-26, 2017, Conference Track Pro-
         ceedings.
Li, L., Jamieson, K., DeSalvo, G., Rostamizadeh, A., & Talwalkar, A. (2018). Hyperband: A
         Novel Bandit-Based Approach to Hyperparameter Optimization. Journal of Machine
         Learning Research, 18 (185), 1–52.
Li, R., Emmerich, M. T., Eggermont, J., Bäck, T., Schütz, M., Dijkstra, J., & Reiber, J. H.
         (2013). Mixed integer evolution strategies for parameter optimization. Evolutionary
         Computation, 21 (1), 29–64.
Lindauer, M., & Hutter, F. (2018). Warmstarting of Model-Based Algorithm Configuration.
         Proceedings of the AAAI Conference on Artificial Intelligence, 32 (1).
Lindauer, M., & Hutter, F. (2020). Best Practices for Scientific Research on Neural Architecture
         Search. Journal of Machine Learning Research, 21 (243), 1–18.
Liu, H., Simonyan, K., & Yang, Y. (2019). DARTS: Differentiable Architecture Search. 7th
         International Conference on Learning Representations, ICLR 2019, New Orleans, LA,
         USA, May 6-9, 2019.
López-Ibáñez, M., Dubois-Lacoste, J., Pérez Cáceres, L., Stützle, T., & Birattari, M. (2016).
         The irace package: Iterated Racing for Automatic Algorithm Configuration. Operations
         Research Perspectives, 3, 43–58.
Loshchilov, I., & Hutter, F. (2017). SGDR: Stochastic Gradient Descent with Warm Restarts.
         5th International Conference on Learning Representations, ICLR 2017, Toulon, France,
         April 24-26, 2017, Conference Track Proceedings.
Maclaurin, D., Duvenaud, D., & Adams, R. (2015). Gradient-based Hyperparameter Optimiza-
         tion through Reversible Learning (F. Bach & D. Blei, Eds.). 37, 2113–2122.
Maron, O., & Moore, A. W. (1994). Hoeffding races: Accelerating model selection search for
         classification and function approximation. Advances in Neural Information Processing
         Systems, 6, 59–66.
McIntire, M., Ratner, D., & Ermon, S. (2016). Sparse Gaussian Processes for Bayesian Op-
         timization. Proceedings of the Thirty-Second Conference on Uncertainty in Artificial
         Intelligence, 517–526.
McKay, M., Beckman, R., & Conover, W. (1979). Comparison of Three Methods for Selecting
         Values of Input Variables in the Analysis of Output from a Computer Code. Techno-
         metrics, 21 (2), 239–245.
Mohr, F., Wever, M., & Hüllermeier, E. (2018). ML-Plan: Automated machine learning via
         hierarchical planning. Machine Learning, 107 (8-10), 1495–1515.
Molnar, C., Casalicchio, G., & Bischl, B. (2019). Quantifying model complexity via functional de-
         composition for better post-hoc interpretability. Joint European Conference on Machine
         Learning and Knowledge Discovery in Databases, 193–204.
Nayebi, A., Munteanu, A., & Poloczek, M. (2019). A Framework for Bayesian Optimization in
         Embedded Subspaces. In K. Chaudhuri & R. Salakhutdinov (Eds.), Proceedings of the
         36th International Conference on Machine Learning (pp. 4752–4761). PMLR.
Ng, A. Y. (1997). Preventing “overfitting” of cross-validation data. ICML, 97, 245–253.
Olson, R. S., Bartley, N., Urbanowicz, R. J., & Moore, J. H. (2016). Evaluation of a Tree-based
         Pipeline Optimization Tool for Automating Data Science. Proceedings of the Genetic
         and Evolutionary Computation Conference 2016, 485–492.

                                                44

Parker-Holder, J., Nguyen, V., & Roberts, S. J. (2020). Provably Efficient Online Hyperpa-
         rameter Optimization with Population-Based Bandits. Advances in Neural Information
         Processing Systems 33: Annual Conference on Neural Information Processing Systems.
Paszke, A., Gross, S., Massa, F., Lerer, A., Bradbury, J., Chanan, G., Killeen, T., Lin, Z.,
         Gimelshein, N., Antiga, L., Desmaison, A., Kopf, A., Yang, E., DeVito, Z., Raison, M.,
         Tejani, A., Chilamkurthy, S., Steiner, B., Fang, L., . . . Chintala, S. (2019). PyTorch: An
         Imperative Style, High-Performance Deep Learning Library (H. Wallach, H. Larochelle,
         A. Beygelzimer, F. d’Alché-Buc, E. Fox, & R. Garnett, Eds.), 8024–8035.
Pedregosa, F., Varoquaux, G., Gramfort, A., Michel, V., Thirion, B., Grisel, O., Blondel, M.,
         Prettenhofer, P., Weiss, R., Dubourg, V., Vanderplas, J., Passos, A., Cournapeau, D.,
         Brucher, M., Perrot, M., & Duchesnay, E. (2011). Scikit-learn: Machine Learning in
         Python. Journal of Machine Learning Research, 12, 2825–2830.
Perrone, V., Shen, H., Seeger, M. W., Archambeau, C., & Jenatton, R. (2019). Learning search
         spaces for Bayesian optimization: Another view of hyperparameter transfer learning. In
         H. Wallach, H. Larochelle, A. Beygelzimer, F. d’Alché-Buc, E. Fox, & R. Garnett (Eds.),
         Advances in Neural Information Processing Systems. Curran Associates, Inc.
Pfisterer, F., van Rijn, J. N., Probst, P., Müller, A. C., & Bischl, B. (2021). Learning Multiple
         Defaults for Machine Learning Algorithms. Proceedings of the Genetic and Evolutionary
         Computation Conference Companion, 241–242.
Picheny, V., Wagner, T., & Ginsbourger, D. (2013). A benchmark of kriging-based infill criteria
         for noisy optimization. Structural and Multidisciplinary Optimization, 48 (3), 607–626.
Probst, P., Boulesteix, A.-L., & Bischl, B. (2019). Tunability: Importance of Hyperparameters
         of Machine Learning Algorithms. Journal of Machine Learning Research, 20 (53), 1–32.
Quinlan, J., & Cameron-Jones, R. (1995). Oversearching and layered search in empirical learning.
         breast cancer, 286, 2–7.
Rasmussen, C. E., & Williams, C. K. I. (2006). Gaussian processes for machine learning. MIT
         Press.
Real, E., Aggarwal, A., Huang, Y., & Le, Q. V. (2019). Regularized Evolution for Image Classifier
         Architecture Search. Proceedings of the AAAI Conference on Artificial Intelligence, 33,
         4780–4789.
Rice, J. R. (1976). The Algorithm Selection Problem. Adv. Comput., 15, 65–118.
Rodgers, D. P. (1985). Improvements in multiprocessor system design. ACM SIGARCH Computer
         Architecture News, 13 (3), 225–231.
Roustant, O., Ginsbourger, D., & Deville, Y. (2012). DiceKriging, DiceOptim: Two R packages
         for the analysis of computer experiments by kriging-based metamodeling and optimiza-
         tion. Journal of Statistical Software, 51.
Sasena, M. J., Papalambros, P., & Goovaerts, P. (2002). Exploration of metamodeling sampling
         criteria for constrained global optimization. Engineering optimization, 34 (3), 263–278.
Scott, W., Frazier, P., & Powell, W. (2011). The correlated knowledge gradient for simulation
         optimization of continuous parameters using gaussian process regression. SIAM Journal
         on Optimization, 21 (3), 996–1026.
Sekhon, J. S., & Mebane, W. R. (1998). Genetic optimization using derivatives. Political Analysis,
         7, 187–210.
Sexton, J., & Laake, P. (2009). Standard errors for bagged and random forest estimators. Com-
         putational Statistics & Data Analysis, 53 (3), 801–811.
Sheng, V. S., & Ling, C. X. (2006). Thresholding for making classifiers cost-sensitive. AAAI’06:
         Proceedings of the 21st national conference on artificial intelligence, 6, 476–481.

                                                45

Simon, R. (2007). Resampling Strategies for Model Assessment and Selection. In W. Dubitzky,
         M. Granzow, & D. Berrar (Eds.), Fundamentals of Data Mining in Genomics and Pro-
         teomics (pp. 173–186). Springer.
Snoek, J., Larochelle, H., & Adams, R. (2012). Practical Bayesian Optimization of Machine
         Learning Algorithms (P. Bartlett, F. Pereira, C. Burges, L. Bottou, & K. Weinberger,
         Eds.), 2960–2968.
Snoek, J., Larochelle, H., & Adams, R. P. (2012). Practical Bayesian Optimization of Machine
         Learning Algorithms. In F. Pereira, C. J. C. Burges, L. Bottou, & K. Q. Weinberger
         (Eds.), Advances in Neural Information Processing Systems 25 (pp. 2951–2959). Curran
         Associates, Inc.
Snoek, J., Rippel, O., Swersky, K., Kiros, R., Satish, N., Sundaram, N., Patwary, M. M. A.,
         Prabhat, & Adams, R. P. (2015). Scalable Bayesian Optimization Using Deep Neural
         Networks. In F. R. Bach & D. M. Blei (Eds.), Proceedings of the 32nd International
         Conference on Machine Learning, ICML 2015, Lille, France, 6-11 July 2015 (pp. 2171–
         2180). JMLR.org.
Speck, D., Biedenkapp, A., Hutter, F., Mattmüller, R., & Lindauer, M. (2021). Learning Heuristic
         Selection with Dynamic Algorithm Configuration. Proceedings of the 31st International
         Conference on Automated Planning and Scheduling (ICAPS’21).
Srinivas, N., Krause, A., Kakade, S., & Seeger, M. (2010). Gaussian Process Optimization
         in the Bandit Setting: No Regret and Experimental Design. Proceedings of the 27th
         International Conference on International Conference on Machine Learning, 1015–1022.
Swersky, K., Snoek, J., & Adams, R. (2013). Multi-task Bayesian optimization (C. Burges, L.
         Bottou, M. Welling, Z. Ghahramani, & K. Weinberger, Eds.), 2004–2012.
Swersky, K., Duvenaud, D., Snoek, J., Hutter, F., & Osborne, M. A. (2014). Raiders of the
         lost architecture: Kernels for Bayesian optimization in conditional parameter spaces.
         NeurIPS workshop on Bayesian Optimization in Theory and Practice.
Swersky, K., Snoek, J., & Adams, R. P. (2013). Multi-Task Bayesian Optimization. In C. J. C.
         Burges, L. Bottou, M. Welling, Z. Ghahramani, & K. Q. Weinberger (Eds.), Advances
         in Neural Information Processing Systems (pp. 2004–2012). Curran Associates, Inc.
Swersky, K., Snoek, J., & Adams, R. P. (2014). Freeze-thaw bayesian optimization. arXiv: 1406.
         3896. Retrieved March 17, 2021, from http://arxiv.org/abs/1406.3896
Talbi, E.-G. (2020). Optimization of deep neural networks: a survey and unified taxonomy.
         https://hal.inria.fr/hal-02570804
Thiede, L., & Parlitz, U. (2019). Gradient based hyperparameter optimization in Echo State
         Networks. Neural Networks, 115, 23–29.
Thornton, C., Hutter, F., Hoos, H. H., & Leyton-Brown, K. (2013). Auto-WEKA: Combined
         Selection and Hyperparameter Optimization of Classification Algorithms. Proceedings
         of the 19th ACM SIGKDD International Conference on Knowledge Discovery and Data
         Mining, 847–855.
Tiao, L. C., Klein, A., Archambeau, C., & Seeger, M. (n.d.). Model-based Asynchronous Hy-
         perparameter Optimization. arXiv: 2003.10865. Retrieved April 2, 2021, from https:
         //arxiv.org/abs/2003.10865
Van Rijn, J. N., Bischl, B., Torgo, L., Gao, B., Umaashankar, V., Fischer, S., Winter, P.,
         Wiswedel, B., Berthold, M. R., & Vanschoren, J. (2013). OpenML: A collaborative
         science platform. Joint european conference on machine learning and knowledge dis-
         covery in databases, 645–649.

                                               46

Van Rijn, J. N., & Hutter, F. (2018). Hyperparameter importance across datasets. Proceedings
         of the 24th ACM SIGKDD International Conference on Knowledge Discovery & Data
         Mining, 2367–2376.
Wang, Z., Hutter, F., Zoghi, M., Matheson, D., & de Feitas, N. (2016). Bayesian optimization in
         a billion dimensions via random embeddings. Journal of Artificial Intelligence Research,
         55, 361–387.
White, C., Neiswanger, W., & Savani, Y. (2021). BANANAS: Bayesian Optimization with Neural
         Architectures for Neural Architecture Search. Proceedings of the AAAI Conference on
         Artificial Intelligence.
Wistuba, M., Schilling, N., & Schmidt-Thieme, L. (2015a). Hyperparameter Search Space Prun-
         ing - A New Component for Sequential Model-Based Hyperparameter Optimization (A.
         Appice, P. Rodrigues, V. Costa, J. Gama, A. Jorge, & C. Soares, Eds.). 9285, 104–119.
Wistuba, M., Schilling, N., & Schmidt-Thieme, L. (2015b). Learning hyperparameter optimiza-
         tion initializations, 1–10.
Wolpert, D. H. (1992). Stacked generalization. Neural networks, 5 (2), 241–259.
Xu, L., Hutter, F., Hoos, H. H., & Leyton-Brown, K. (2008). SATzilla: portfolio-based algorithm
         selection for SAT. Journal of artificial intelligence research, 32, 565–606.
Yang, L., & Shami, A. (2020). On hyperparameter optimization of machine learning algorithms:
         Theory and practice. Neurocomputing, 415, 295–316.
Yao, Q., Wang, M., Chen, Y., Dai, W., Li, Y.-F., Tu, W.-W., Yang, Q., & Yu, Y. (2019). Taking
         Human out of Learning Applications: A Survey on Automated Machine Learning.
Yu, T., & Zhu, H. (2020). Hyper-Parameter Optimization: A Review of Algorithms and Appli-
         cations.
Zela, A., Klein, A., Falkner, S., & Hutter, F. (2018). Towards Automated Deep Learning: Efficient
         Joint Neural Architecture and Hyperparameter Search. ICML 2018 AutoML Workshop.
Zhang, Z., Wang, X., & Zhu, W. (2021). Automated Machine Learning on Graphs: A Survey.
         In Z.-H. Zhou (Ed.), Proceedings of the Thirtieth International Joint Conference on
         Artificial Intelligence, IJCAI 2021, Virtual Event / Montreal, Canada, 19-27 August
         2021 (pp. 4704–4712). ijcai.org.
Zheng, J., Li, Z., Gao, L., & Jiang, G. (2016). A parameterized lower confidence bounding
         scheme for adaptive metamodel-based design optimization. Engineering Computations.
Zimmer, L., Lindauer, M., & Hutter, F. (2021). Auto-PyTorch Tabular: Multi-Fidelity Met-
         aLearning for Efficient and Robust AutoDL. IEEE Transactions on Pattern Analysis and
         Machine Intelligence, 1–12.

                                               47

Appendix A             Learners and Search Spaces
In this appendix, we list suggested HP spaces for a variety of popular ML algorithms. All covered
learners can be used for both classification and regression. In Tables 1 to 7, important HP names,
package default values, and recommended tuning ranges are given. Although the names and
default values relate to the particular R implementation, they can usually be easily matched with
names in other implementations. HP data types are listed as: logical (L), integer-valued (I), realvalued (R), or categorical (C). Some HPs are typically optimized on a logarithmic scale, which
yields a higher resolution for smaller values compared to larger ones. This can be achieved by
using a transformation function before a proposed HP is passed on to the learning algorithm,
indicated by the column “Trafo” in the HP tables. In this case, the transformation must be
applied to the values taken from “Typical range”. The default values, on the other hand, should
be interpreted as default HP values after transformation.
It should be noted that the selection of learners, HPs, and especially their recommended ranges
is somewhat subjective, because (i) many learners and implementations exist and we can only list
a small subset due to space constraints, and because (ii) it is an open problem which parameters
should be tuned and in what ranges. The ranges presented here are mostly based on experience
and should work well for a wide range of data sets, but – especially for non-standard situations
– adaptation may be necessary.

A.1     Nearest Neighbors
Concept The k-Nearest-Neighbors (k-NN) algorithm (Cover & Hart, 1967) is a simple method
where model fitting only consists of storing the data. To predict a new data point, a distance
function, e.g., Euclidean distance, to all existing points in the training set is calculated, and
the k “nearest neighbors” are determined. For classification, posterior probabilities are simply estimated by computing class proportions among the nearest neighbors; for regression, the
prediction is a simple average.
k-NN can be extended by introducing a kernel function that weights the nearest neighbors
according to their distances to the prediction point (Hechenbichler & Schliep, 2004).

Hyperparameters See Table 1. k mainly controls how local the k-NN model becomes: small
values make k-NN more flexible but susceptible to overfitting, while larger values create a
smoother albeit potentially underfitted prediction function.

A.2     Regularized Linear Models
Concept Methods in this category extend (generalized) linear regression models. The regression coefficients are “shrunk” in order to prevent overfitting and therefore improve prediction
accuracy. In the simplest case, the least squares criterion of ordinary regression is extended by
a penalty term (using the notation of Friedman et al., 2010):
                                                                        
                            1                     1
                    minp      ky − Xθk22 + λreg     (1 − α)kθk22 + αkθk1      ,              (16)
                    θ∈R    2n                     2
with λreg as the regularization parameter. For α = 1, this is known as the lasso (Tibshirani,
1996), for α = 0 it is ridge regression (Hoerl & Kennard, 1970). For α ∈ (0, 1), the elastic net
is obtained (Zou & Hastie, 2005).
While both the ridge and the lasso penalty cause shrinkage of the regression coefficients, the
lasso penalty also features selection by shrinking some coefficients to be exactly zero, leading

                                                48

       HP          Type         Typical Range Trafo Default Description
       k              I       [log(1), log(50)] bex c          7 number of nearest neigh-
                                                                  bors
       distance       R                   [1, 5]        –      2 parameter p of the
                                                                  Minkowski         distance
                                                                  kx − ykp
       kernel         C           {rectangular,         – optimal distance weighting func-
                                       optimal,                   tion; “rectangular” corre-
                                epanechnikov,                     sponds to ordinary k-NN
                           biweight, triweight,
                            cos, inv, gaussian,
                                         rank}

                      Table 1: Important HPs for k-NN in the kknn package.

to sparse models. In the case of a group of highly correlated features, the lasso tends to pick
only one feature from this group. Moreover, in the p > n case, the lasso can select at most n
non-zero coefficients. The elastic net overcomes these limitations by combining the lasso with
the ridge penalty.
Lasso and ridge regression as well as the elastic net can be extended to a regularized generalized
linear model (GLM, see Nelder and Wedderburn (1972) and Dunn and Smyth (2018)), simply
by plugging in the objective of the GLM instead of least squares.

Hyperparameters See Table 2. α ∈ [0, 1] controls the respective weighting of the lasso and
ridge penalties. λreg controls the strength of the regularization in general.

        HP      Type Typical Range Trafo Default Description
        s         R         [−12, 12]     2x            1 controls regularization, but can
                                                          also be optimized internally
        alpha     R             [0, 1]      -           1 determines mixture of the lasso
                                                          and ridge penalties. α = 1 is lasso

                Table 2: Important HPs for elastic net in the glmnet package.

A.3    Support Vector Machines
Concept The basic idea of the Support Vector Machine (SVM, Boser et al. (1992) and Cortes
and Vapnik (1995)) can best be understood by considering the case of binary classification: The
SVM places a hyperplane in the feature space between both classes such that the margin, i.e.,
the distance of the training points that lie closest to the hyperplane (the support vectors), is
maximized, allowing for few margin violations. This geometric interpretation can equivalently be
formulated as regularized risk minimization under hinge loss and ridge penalty, meaning the linear
SVM is similar to a regularized GLM. Class prediction for a new data point can be performed
by checking which side of the hyperplane the new data point lies on. Posterior probabilities are
usually obtained by re-scaling the decision value through a logistic function (Platt, 1999).

                                                   49

In order to achieve non-linear separation, data points are implicitly mapped to a higher dimensional feature space by a kernel function, where the same linear procedure is employed.
The SVM can be (somewhat non-trivially) extended to multiclass classification as well as to
regression (Support Vector Regression, SVR, see Drucker et al. (1996) and Schölkopf et al.
(2000)).

Hyperparameters See Table 3. The SVM is mainly influenced by its regularization control
parameter, its type of kernel (e.g., linear, polynomial, sigmoid, radial basis function, etc.), and
its kernel HPs.

        HP       Type         Typical Range Trafo Default Description
        cost       R               [−12, 12]       2x          1 cost of margin violations
        kernel     C       {radial, sigmoid,        –      radial type of kernel function
                         polynomial, linear}
        degree     I             {2, . . . , 5}        –      3 degree of polynomial ker-
                                                                nel (only if kernel is of type
                                                                polynomial)
        gamma      R                [−12, 12]      2x       1/p controls shape of kernel
                                                                function, e.g., for radial the
                                                                inverse width (not needed if
                                                                kernel is of type linear)

Table 3: Important HPs for SVM in the e1071 package (LIBSVM). Note that by default, gamma
is set to a value that depends on the number of features p.

A.4     Decision Trees
Concept Although many variants of decision trees exist, we only focus on the widely used
CART algorithm here (Breiman et al., 1984). A decision tree is a decision rule constructed from
the training set by recursively partitioning (“splitting”) the space of features in such a way that
the resulting nodes are as homogeneous as possible regarding the target variable of interest.

Hyperparameters See Table 4. CART is mainly influenced by (i) the splitting criterion / loss
function used to select the best feature and split point for each internal node, such as Gini or
entropy; (ii) the stopping criteria for splitting, which determine the size of the tree and hence
regularize it; (iii) the procedure used to assign a predicted value to each leaf node.

A.5     Random Forests
Concept The random forest (RF) algorithm (Breiman, 2001) is an ensemble technique. It
aggregates a large number of decision trees in order to reduce prediction error and therefore
smoothes out prediction variance through bagging. It also further de-correlates the contained
trees by expanding them to a large depth and subsampling the candidate features to be considered
(“tried”) at each split.

Hyperparameters See Table 5. RFs have a relatively large number of HPs. These include
the parameters controlling the construction of the single decision trees and also the parameters
controlling the structure and size of the forest. The latter include the number of trees in

                                                  50

        HP           Type Typical Range Trafo             Default Description
        minsplit       I        {1, . . . , 7}    2x           20 minimum number of ob-
                                                                  servations a parent node
                                                                  must have to be split
        minbucket      I        {0, . . . , 6}    2x bminsplit/3e minimum number of ob-
                                                                  servations in a leaf node
        cp             R          [−4, −1]       10x         0.01 complexity parameter
                                                                  that prevents splits that
                                                                  reduce the overall loss
                                                                  by a fraction less than
                                                                  this

                   Table 4: Important HPs for CART in the rpart package.

the forest and parameters determining its randomness: the number of variables considered as
candidate splitting variables at each split, or the sampling scheme used to generate the data sets
on which the trees are built (the proportion of randomly drawn observations, and whether they
are drawn with or without replacement). Note that the choice of the number of trees results
from a compromise between performance and computation time rather than from the search for
an optimal value; except for relatively rare exceptions, employing more trees will lead to better
performance at the cost of requiring more computation and memory (Probst & Boulesteix, 2017).
The number of trees is thus not a tuneable HP in the classical sense.
RF has been shown to work reasonably well with default parameters for a wide range of applications and may require less tuning compared to other algorithms (Fernández-Delgado et al.,
2014; Probst, Boulesteix, & Bischl, 2019). However, in some cases, HP tuning can lead to a
substantial performance improvement (Probst, Wright, et al., 2019).

       HP                  Type Typical Range                      Default Description
                                                                     √ 
       mtry                 I        {1, . . . , p}                     p number of fea-
                                                                           tures considered
                                                                           as candidate split-
                                                                           ting variables at
                                                                           each split
       replace              L                                        TRUE are observations
                                                                           drawn with or
                                                                           without replace-
                                                                           ment?
       sample.fraction R                    [0.1, 1] 1 (if replace=TRUE) proportion of ran-
                                                                  or 0.632 domly drawn ob-
                                                                           servations
       num.trees            I    {1, . . . , 2000}                     500 number of trees

Table 5: In addition to the individual tree parameters in Table 4, the most important HPs for
RF in the ranger package. Note that, by default, mtry is set to a value that depends on the
number of features p.

                                                  51

A.6     Boosting
Concept Boosting is an ensemble learning technique and refers to the general idea of improving
the prediction performance of a “weak” learning algorithm by sequentially training multiple
models on re-weighted versions of the data and then combining their predictions (Schapire,
1990; Freund, 1995).
A popular example is the AdaBoost algorithm for classification (Freund & Schapire, 1997).
Friedman et al. (2000) show that AdaBoost can be reformulated and generalized to gradient
boosting (Friedman, 2001). Gradient boosting uses a backfitting procedure to fit the next
learner, which is added to the ensemble in a greedy stagewise manner by fitting it against the
pseudo-residuals of the current ensemble. This not only generalizes the boosting principle for
arbitrary losses of classification and regression (as long as they are differentiable), but also allows
boosting in nearly any supervised ML task, e.g., survival analysis.
In principle, the idea of (gradient) boosting works for any base learner, but the most frequent
choices are (shallow) decision trees or (regularized) linear models.

Hyperparameters See Table 6. The HPs of gradient boosting can be roughly divided into
two categories: (i) choice of base learner and its HPs, e.g., HPs of decision trees in Table 4,
and (ii) HPs of the boosting process, such as the choice of the loss function and the number
of boosting iterations M . Several HPs from both categories are related to regularization, and
tuning them is therefore necessary to avoid overfitting, e.g., (i) choosing M not too large or
using early stopping; (ii) adapting the learning rate, which controls the contribution of each
boosting iteration and interacts with M . Typically, a smaller learning rate increases the optimal
value for M (Friedman, 2001).

A.7     Neural Networks
Concept Artificial neural networks (NNs) are inspired by biological networks of neurons that
communicate with each other by sending action potential signals. A NN generally consists of
non-linearly transformed weighted sums of inputs, called neurons, that are typically organized
in layers. They are trained by stochastic gradient descent variants on small batches of training
samples. Modern NNs are adapted to many data modalities, such as, e.g., computer vision and
natural language processing tasks. Pre-defined architectures for a variety of these tasks exist.

Hyperparameters See Table 7. The HPs of NNs can be split into two categories: (i) optimization and regularization parameters, and (ii) architectural parameters that define the type,
amount, structure, and connections of neurons.
For the first category of HPs, the optimization techniques introduced in this paper can be used
straightforwardly. The second category of HPs is much more difficult to optimize, and the field
of neural architecture search (NAS) provides highly customized strategies to find well-working
network architectures (Elsken et al., 2019). However, we can define a function that can construct
a network architecture given specific simple HPs while not being able to construct all possible
network architectures. A simple example would be an HP that controls the number of hidden
layers. However, especially for non-tabular data, NNs usually have more complex architectures.
In such cases, one could parameterize the layout of a single cell within an architecture of stacked
cells (Zoph et al., 2018).

                                                  52

        HP                       Type Typical Range Trafo Default Description
        eta                       R            [−4, 0]       10x       0.3 learning rate (also
                                                                           called ν) shrinks
                                                                           contribution of each
                                                                           boosting update
        nrounds                   I    {1, . . . , 5000}          –      – number of boosting
                                                                           iterations. Can also
                                                                           be optimized with
                                                                           early stopping.
        max depth                 I      {1, . . . , 20}          –      6 maximum depth of a
                                                                           tree
        colsample bytree          R            [0.1, 1]           –      1 subsample ratio of
                                                                           columns for each
                                                                           tree
        colsample -               R            [0.1, 1]           –      1 subsample ratio of
        bylevel                                                            columns for each
                                                                           depth level
        lambda                    R         [−10, 10]            2x      1 L2      regularization
                                                                           term on weights
        alpha                     R         [−10, 10]            2x      0 L1      regularization
                                                                           term on weights
        subsample                 R            [0.1, 1]           –      1 subsample        ratio
                                                                           of     the    training
                                                                           instances

Table 6: Important HPs for gradient boosting in xgboost package. The HPs given here assume
that a tree base learner is used.

        HP                 Type Typical Range Trafo Default Description
        learning rate        R             [−5, 0]         10x        – gradient descent learn-
                                                                        ing rate, also called η
                                                                        or α
        regularizer -        R           [−7, −4]          10x        – L2 penalty on weights.
        l2
        epochs               I            {1, . . . }        –        – Very problem depen-
                                                                        dent, usually optimized
                                                                        with early stopping.

Table 7: Optimizer HPs for a NN, as used in the keras package. Unlike other methods presented
here, NNs in torch and keras are constructed programmatically, so their configuration in many
regards does not consist of HPs in the form of explicit function arguments.

Appendix B            Preprocessing
Before applying a learner, it may be helpful or even necessary to modify the data before training
in a step called preprocessing. Preprocessing serves mainly two purposes: (i) it either transforms

                                                    53

the data to fit the technical requirements of a specific learner, e.g., by converting categorical
features to numerical features or by imputing missing values; or (ii) it modifies the data to
improve predictive performance, e.g., by bringing features on a common scale or by extracting
components of a date column.

B.1     Missing Data Imputation
Imputation describes the task of replacing missing values with feasible ones, since many ML
algorithms do not natively handle missing values. In practice, data is often not missing fully at
random, and therefore the fact that part of the data is missing itself might be informative for
predicting the target label. Hence, it is generally recommended to preserve the information of
whether a certain value has been imputed. For categorical features, we often simply introduce a
new level for missing values. For numerical or integer features, the simplest techniques impute
with a constant mean or median value per feature column or sample from the empirical univariate
distribution of the feature. To preserve the missing information in this case, an additional binary
dummy column can be added per imputed feature column. When tree-based methods are used,
it is often preferred to impute missing values with very large or very small values outside the range
of the training data (Ding & Simonoff, 2010), as the tree can then split between available and
imputed data, thereby preserving the information about the fact that data is missing without
the need for an extra column. Much more elaborate methods exist, often based on multiple
imputation, but these are mainly geared towards ensuring proper inference. For purely predictive
tasks, it is still somewhat unclear how beneficial these more elaborate imputation methods are.
More detailed discussions are available in Garciarena and Santana (2017), Jadhav et al. (2019),
and Woźnica and Biecek (2020).

B.2     Encoding of Categorical Features
Many ML algorithms operate on purely numerical inputs, and hence, we often need an additional
numerical encoding procedure for categorical features. Dummy encoding, also called one-hot
encoding, is arguably the most popular choice. It replaces a categorical k-level feature with k
or k − 1 binary indicator features. The latter case avoids multicollinearity of the newly created
features by using the k-th level as a reference level, encoding it by setting all indicator features to
0. For data sets with many categorical features and data sets with categorical features with many
levels (high-cardinality features), this leads to a considerable increase in dimensionality, resulting
in increased runtime and memory requirements. In such cases, methods like impact encoding –
also called target encoding (Sweeney & Ulveling, 1972) – replace the categorical feature with
only a single numeric feature, based on the average outcome value for all observations for which
that level has been observed. Pargent et al. (2021) have shown that target encoding using
generalized linear mixed models (GLMMs) (Micci-Barreca, 2001) tends to outperform other
common encoding methods in settings where categorical feature cardinality is high.
Problems arise if factor levels occur during prediction that have not been encountered during
training. To some extent, this can be addressed by merging infrequent factor levels to a new
level with value “other”, or by stratifying on the levels during resampling, but can never be
completely ruled out. Since the new level is unknown to the model, it can be considered a
special case of missing data and could therefore be handled by imputation. A typical HP of the
feature encoding process would be the threshold that decides when to perform target encoding
instead of dummy encoding, depending on the number of levels of a feature.
Several benchmarks of encoding methods have been performed, typically with different emphasis
on setting, such as the work by Pargent et al. (2021) and the references contained therein.

                                                  54

B.3     Feature Filtering
Feature filtering is used to select a subset of features prior to model fitting. Subsetting features
can serve two purposes: (i) either to reduce the computational cost, or (ii) to eliminate noise
variables that may deteriorate model performance. Feature filters often assign a numerical score
to each feature, usually based on simple dependency measures between each feature and the
outcome, e.g., mutual information or correlation. The percentage of features to keep is typically
an HP. Note that feature filtering is one of several possibilities to perform feature selection. See
Guyon and Elisseeff (2003) for a description of feature wrappers and embedded feature selection
methods.
See Wah et al. (2018) for a benchmark assessing filter methods applied to simulated datasets and
the correctness of selected features. See also Bommert et al. (2020) as well as Xue et al. (2015)
for a benchmark assessing filters with respect to predictive performance as well as secondary
aspects of interest such as runtime. Binder et al. (2020) evaluates a variety of feature filters
with respect to their similarity and their suitability for constructing a feature filter ensemble,
which can subsequently be tuned for a data set at hand with BO.

B.4     Data Augmentation and Sampling
Data augmentation has the goal of improving predictive performance by adding or removing
rows of the training data. A common use-case for data augmentation is classification with
imbalanced class labels. Here, we can add additional observations by oversampling, i.e., repeating
observations of the minority class. A more flexible alternative is SMOTE (“Synthetic Minority
Oversampling Technique”, Chawla et al., 2002), where convex combinations of observations from
the minority class are added to the training data. When the available dataset is very large, it is
also possible to undersample in order to correct for class imbalance, i.e., filtering out observations
of the majority class. Also, subsampling the data set as a whole is an old trick to reduce training
time during HPO, and modern multifidelity approaches exploit this in a more principled manner.
Sampling and data augmentation methods differ from many other preprocessing methods in
that they only affect the training phase of a model. Typical HPs include the under-, over-, or
subsampling ratio or the number of observations to use for the convex combinations in SMOTE.
For a more thorough description of how to treat imbalanced data in ML, see Krawczyk (2016).
Current challenges and areas of research are described in He and Ma (2013).

B.5     Feature Extraction
A crucial step in improving predictive performance of a model is feature extraction or feature
engineering. The key idea is to add new features to the data that the model can exploit better
than the original data. This is often both domain-specific and model-specific, and therefore is
hard to generalize. For linear models, for example, adding polynomial terms can provide a better
view on the data, effectively allowing the model to capture non-linear effects and higher-order
interactions. Simple tree-based methods might profit from methods like principal component
analysis, which rotate all or a subset of numerical features to align feature dimensions with the
(orthogonal) directions of major variance. On the other hand, such simple extractions techniques
very often do not provide competitive benefit in terms of predictive performance when many nonparametric models are included in model selection and HPO (Bischl et al., 2014).
More beneficial is often domain-specific feature extraction for more complex data types. For
example, for functional data features, where a collection of columns represents a curve, one can
either extract generic characteristics (mean value, maximum value, average slope, number of

                                                 55

peaks, etc.) or run domain-specific extraction, e.g., we might extract mel-frequency cepstral
coefficients (MFCCs) (Sahidullah & Saha, 2012) for an audio signal. For a practical guide to
using feature extraction in predictive modeling, see e.g. Kuhn and Johnson (2019).
All extracted features can either be used instead or in addition to the original features. As
it is often highly unclear how to perform ideal feature extraction for a specific task, it seems
particularly attractive if an ML software system allows the user to embed custom extraction code
into the preprocessing with exposed custom HPs so that a flexible feature extraction piece of
code can be automatically configured by the tuner in a data-dependent manner.

                                              56

     Appendix C                 Evalution Metrics
     Name                               Formula                                             Direction   Range     Description
     Performance measures for regression
                                    Pntest  (i)       2
                                 1
     Mean Squared Error (MSE) ntest    i=1  y − ŷ (i)                                        min       [0, ∞)    Mean of the squared distances between the target variable y and
                                                                                                                  the predicted target ŷ.
                                                Pntest
     Mean Absolute Error (MAE)            1
                                        ntest     i=1      y (i) − ŷ (i)                     min       [0, ∞)    More robust than MSE, since it is less influenced by large errors.
                                                Pntest                 2
                                                  (y −ŷ(i) )
                                                         (i)
     R2                                 1 − Pi=1
                                              ntest  (i) −ȳ 2
                                                                                              max       (−∞, 1] Compare the sum of squared errors (SSE) of the model to a con-
                                              i=1 (y        )
                                                                                                                stant baseline model.
     Performance measures for classification based on class labels
                                  1
                                       Pntest
     Accuracy (ACC)             ntest    i=1 I{y (i) =ŷ (i) }                                max        [0, 1]   Proportion of correctly classified observations.
                                1
                                  P   g      1
                                                  P
     Balanced Accuracy (BA)     g     k=1 ntest,k    y (i) :y (i) =k I{y (i) =ŷ (i) }        max        [0, 1]   Variant of the accuracy that accounts for imbalanced classes.
                                  1
                                       P ntest
     Classification Error (CE)  ntest    i=1    I{y 6=ŷ(i) }
                                                   (i)                                        min        [0, 1]   CE = 1 − ACC is the proportion of incorrect predictions.
                                                TP
     ROC measures                       TPR = TP+FN                                           max        [0, 1]   True Positive Rate: how many observations of the positive class 1
                                                                                                                  are predicted as 1?
                                                FP
                                        FPR = TN+FP                                           min        [0, 1]   False Positive Rate: how many observations of the negative class
                                                                                                                  0 are falsely predicted as 1?
                                               TN
                                        TNR = TN+FP                                           max        [0, 1]   True Negative Rate: how many observations of the negative class
57                                                                                                                0 are predicted as 0?
                                                FN
                                        FNR = TP+FN                                           min        [0, 1]   False Negative Rate: how many observations of the positive class
                                                                                                                  1 were falsely predicted as 0?
                                                  TP
                                        PPV = TP+FP                                           max        [0, 1]   Positive Predictive Value: how likely is a predicted 1 a true 1?
                                                  TN
                                        NPV = FN+TN                                           max        [0, 1]   Negative Predictive Value: how likely is a predicted 0 a true 0?
                                          PPV·TPR
     F1                                 2 PPV+TPR                                             max        [0, 1]   F1 is the harmonic mean of PPV and TPR. Especially useful for
                                                                                                                  imbalanced classes.
                                        Pntest
     Cost measure                          i=1    C(y (i) , ŷ (i) )                          min       [0, ∞)    Cost of incorrect predictions based on a (usually non-negative) cost
                                                                                                                  matrix C ∈ Rg,g .
     Performance measures for classification based on class probabilities
                                      Pntest Pg                         2
                                  1                      (i)        (i)
     Brier Score (BS)           ntest   i=1    k=1 π̂k (x ) − σk (y     )                     min        [0, 1]   Measures squared distances of probabilities from the one-hot en-
                                                                                                                  coded class labels.
                                              Pntest  Pg                               
     Log-Loss (LL)                        1
                                        ntest  i=1    − k=1 σk (y (i) ) log(π̂k (x(i) ))      min       [0, ∞)    A.k.a. Bernoulli, binomial or cross-entropy loss
     AUC                                                                                      max        [0, 1]   Area under the ROC curve.
     ŷ (i) denotes the predicted label for observation x(i) . ACC, BA, CE, BS, and LL can be used for multi-class classification with g classes. For AUC, multiclass extensions exist as well.
     The notation I{·} denotes the indicator function. σk (y) = I{y=k} is 1 if y is class k, 0 otherwise (multi-class one-hot encoding). ntest,k is the number of observations in the test set
     with class k. π̂k (x) is the estimated probability for observation x(i) of belonging to class k. T P is the number of true positives (observations of class 1 with predicted class 1), F P
     is the number of false positives (observations of class 0 with predicted class 1), T N is the number of true negatives (observations of class 0 with predicted class 0), and F N is the
     number of false negatives (observations of class 1 with predicted class 0).

                                           Table 8: Popular performance measures used for ML, assuming an arbitrary test set of size ntest .

Appendix D               Software
This section lists relevant software packages in both R and Python. Libraries providing popular
machine learning algorithms are listed first, followed by additional information about useful
packages and relevant considerations for HPO in both languages.

D.1     Software in R
D.1.1 Important Machine Learning Algorithms
Here we present a curated list of learners that work well in general and are well integrated with
ML frameworks in R (see Section D.1.3). For a comprehensive list of R-packages that offer ML
models, categorized by the type of model implemented, see the “CRAN Task View” for ML and
Statistical Learning19 .

k-NN The basic k-NN algorithm is implemented in the class package that comes installed
with R. The distance-weighted k-NN is implemented in the kknn package (Schliep & Hechenbichler, 2016). For large data sets, one may prefer the approximate algorithms (Arya et al.,
1998) in the FNN package (Beygelzimer et al., 2019).

Regularized Linear Models The elastic net is implemented in the glmnet package (Friedman
et al., 2010), which also offers an internal, fast optimization of its regularization HPs via the
cv.glmnet() function. Both biglasso (Zeng & Breheny, 2018) and LiblineaR implement
regularized linear models specifically optimized for large datasets.

Support Vector Machines A frequently used implementation of the SVM for R can be found
in the e1071 package (Meyer et al., 2019), which uses the LIBSVM library internally (Chang
& Lin, 2011). kernlab (Karatzoglou et al., 2004) is a more comprehensive package with an
emphasis on flexibility.

Decision Trees The basic CART algorithm is implemented in the rpart R package (Therneau
& Atkinson, 2019), while a more flexible implementation of decision trees is included in partykit
(Hothorn & Zeileis, 2015)

Random Forests Three of the most widely used R implementations of RF are provided in
the packages (i) randomForest, implementing the original version (Breiman, 2001), (ii) party,
implementing (unbiased) conditional inference forests (Hothorn et al., 2006), and (iii) ranger,
providing a newer implementation of RF, including additional variants and a wide range of
options, optimized for use on high-dimensional data (Wright & Ziegler, 2017).

Boosting Popular implementations of gradient boosting with tree base learners can be found
in gbm (Greenwell et al., 2020) and implemented more efficiently in xgboost (Chen & Guestrin,
2016; Chen et al., 2020). Gradient boosting of generalized linear and additive models is implemented in xgboost as well as in mboost (Hothorn et al., 2010), the latter being more efficiently
implemented in compboost (Schalk et al., 2018).
  19 https://cran.r-project.org/view=MachineLearning

                                                       58

Artificial Neural Networks Modern and fast implementations of NNs are provided by the
keras (Allaire & Chollet, 2020) and torch (Falbel & Luraschi, 2020) packages. torch is
more low-level than keras, and some properties that are explicit HPs of keras need to be
implemented by the user through R code in torch.
D.1.2 Black-Box and HP Optimizers
Evolution Strategies Popular R packages that implement different ES are rgenoud (Mebane,
Jr. & Sekhon, 2011), cmaes (Trautmann et al., 2011), adagio (Borchers, 2018), DEoptim
(Mullen et al., 2011), mco (Mersmann, 2014), and ecr (Bossek, 2017) and mosmafs (Binder
et al., 2020).

Bayesian Optimization tune, rBayesianOptimization (Yan, 2016) and DiceOptim (Roustant et al., 2012) are R packages that implement BO with Gaussian processes and for purely
numerical search spaces. mlrMBO (Bischl et al., 2017) and its successor mlr3mbo offer to construct a flexible class of BO variants with arbitrary regression models and acquisition functions.
These packages can work with mixed and hierarchical search spaces.

Other Methods Hyperband is implemented in the package mlr3hyperband (Gruber & Bischl,
2019). Iterated F-racing is provided by irace (López-Ibáñez et al., 2016).
D.1.3 ML Frameworks
There are many technical difficulties and intricacies to attend to when practically combining a
black-box optimizer with different ML algorithms, especially pipelines, to perform HPO. Although
it would be possible to use one of the algorithms shown above and write an objective function
that performs performance evaluation, this is not recommended. Instead, one of several ML
frameworks should be used. These frameworks simplify the process of model fitting, evaluation,
and optimization while managing the most common pitfalls and providing robust and parallel
technical execution. In the following paragraphs, we summarize the tuning capabilities of the
most important ML frameworks for R. The feature matrix in Table 9 gives an encompassing
overview of the implemented capabilities.

                                        mlr         mlr3      caret tidymodels          h2o
           Tuners
           - Random Search            3         3               3         3              3
           - Grid Search              3         3               3         3              3
           - Evolutionary             3         3               7          7             7
           - Bayesian Optimization    3         3               7         3              7
           - Hyperband                7         3               7          7             7
           - Racing                   3         ∗               3         3              7
           - Other Tuners          NSGA2, SA NLOpt, SA          -         SA             -
           Search Space
           - Transformations             3            3         7          3             3
           - Default Search Spaces       3            7         i          3             3
           Joint Preproc Tuning          3            3         7          3             3
           ∗: not yet released on CRAN but prototype available, i: see description in main text.

             Table 9: Feature matrix of tuning capabilities of R’s ML frameworks.

mlr (Bischl et al., 2016) supports a broad range of optimizers: random search, grid search,
CMA-ES via cmaes, BO via mlrMBO (Bischl et al., 2017), iterated F-racing via irace, NSGA2 via

                                                    59

mco, and simulated annealing via GenSA (Xiang et al., 2013). Random search, grid search, and
NSGA2 also support multi-criteria optimization. Arbitrary search spaces can be defined using the
ParamHelpers package, which also supports HP transformations. HP spaces of preprocessing
steps and ML algorithms can be jointly tuned with the mlrCPO package.

mlr3 (Lang et al., 2019) superseded mlr in 2019. This new package is designed with a more
modular structure in mind and offers a much improved system for pipelines in mlr3pipelines
(Binder et al., 2021). mlr3tuning implements grid search, random search, simulated annealing via GenSA, CMA-ES via cmaes, and non-linear optimization via nloptr (Johnson, 2014).
Additionally, mlr3hyperband provides Hyperband for multifidelity HPO, and the miesmuschel
package implements a flexible toolbox for optimization using ES. BO is available through mlrMBO
when the mlrintermbo compatibility bridge package is used. A further extension for BO is currently in development20 . mlr3 uses the paradox package, a re-implementation of ParamHelpers
with a comparable set of features.

caret (Kuhn, 2008) ships with grid search, random search, and adaptive resampling (AR)
(Kuhn, 2014) – a racing approach where an iterative optimization algorithm favors regions of
empirically good predictive performance. Default HPs to tune are encoded in the respective call
of each ML algorithm. However, if forbidden regions, transformations, or custom search spaces
are required, the user must specify a custom design to evaluate. Embedding preprocessing into
the tuning process is possible. However, tuning over the HPs of preprocessing methods is not
supported.

tidymodels (Kuhn & Wickham, 2020) is the successor of caret. This package ships with
grid search and random search, and the recently released tune package comes with Bayesian
optimization. The AR racing approach and simulated annealing can be found in the finetune
package. HP defaults and transformations are supported via dials. HPs of preprocessing
operations using tidymodels’ recipes pipeline system can be tuned jointly with the HPs of the
ML algorithm.

h2o (LeDell et al., 2020) connects to the H2O cross-platform ML framework written in Java.
Unlike the other discussed frameworks, which connect third-party packages from CRAN, h2o
ships with its own implementations of ML models. The package supports grid search and random
grid search, a variant of random search where points to be evaluated are randomly sampled from
a discrete grid. The possibilities for preprocessing are limited to imputation, different encoders
for categorical features, and correcting for class imbalances via under- and oversampling. H2O
automatically constructs a search space for a given set of learning algorithms and preprocessing
methods, and HPs of both can be tuned jointly. It is worth mentioning that h2o was developed
with a strong focus on AutoML and offers the functionality to perform random search over a
pre-defined grid, evaluating configurations of generalized linear models with elastic net penalty,
xgboost models, gradient boosting machines, and deep learning models. The best performing
configurations found by the AutoML system are then stacked together (van der Laan et al.,
2007) for the final model.
  20 https://github.com/mlr-org/mlr3mbo

                                               60

D.2     Software in Python
D.2.1 Machine Learning Package in Python
Scikit-learn (Pedregosa et al., 2011, sklearn) is a general ML framework implemented in
Python and the default path for ML projects without the need for deep learning (DL). The
framework provides the most important traditional ML models (incl. SVM, RFs, Boosting, etc.).
This is further complemented by a variety of preprocessing and post-processing methods (e.g.,
ensembling). The clean and well-documented API allows users to easily build fairly complex
pipelines, which can provide strong performance on tabular data, e.g., see results on autosklearn (Feurer et al., 2015).
Modern DL frameworks such as Tensorflow (Abadi et al., 2015), PyTorch (Paszke et al.,
2019) and MXNet (Chen et al., 2015) accelerate large-scale computation task (in particular
matrix multiplication) by executing them on GPUs. Furthermore, GPflow (de G. Matthews
et al., 2017) and GPyTorch (Gardner et al., 2018) are two GP libraries built on top of the two
DL frameworks respectively and thus can be again used as surrogate models for BO.
D.2.2 Open-Source HPO Packages in Python
The rapid development of the package landscape in Python has enabled many developers to also
provide HPO tools in Python. Here, we will give an selective overview of commonly used and
well-known packages.
Spearmint (Snoek et al., 2012; Snoek, n.d.) was one of the first successful open-source BO
packages for HPO. As proposed by Snoek et al. (2012), Spearmint implements standard BO with
Markov chain Monte Carlo (MCMC) integration of the acquisition function for a fully Bayesian
treatment of GP’s HPs. Additionally, the second version of Spearmint allowed warping the
input space with a Beta cumulative distribution function (Snoek et al., 2014) to deal with nonstationary loss landscapes. Spearmint also supports constrained BO (Gelbart et al., 2014) and
parallel optimization (Snoek et al., 2012).
Hyperopt (Bergstra et al., 2013), as indicated by its name, is a distributed HPO framework.
Unlike other frameworks that estimate the potential performance given a configuration, Hyperopt implements a Tree of Parzen Estimators (TPE) (Bergstra et al., 2011) that estimates the
distribution of configurations given their performance. This approach allows it to easily run
different configurations in parallel, as multiple density estimators can be executed at the same
time.
Scikit-optimize (skopt; n.d.) is a simple yet efficient library to optimize expensive and noisy
black-box functions with BO. Skopt trains RF (including decision trees and grading-boosting
trees) and GP models based on sklearn (Pedregosa et al., 2011) as surrogate models. Similar to
sklearn, skopt allows for several ways of pre-processing input-features, e.g. one-hot encoding,
log transformation, normalization, label encoding, etc.
SMAC (Hutter et al., 2011; Lindauer et al., 2017) was originally developed as a tool for algorithm
configurations (Eggensperger et al., 2019). However, in recent years, it has also been successfully
used as a key component of several AutoML Tools, such as auto-sklearn (Feurer et al., 2015)
and Auto-PyTorch (Zimmer et al., 2021). SMAC implements both RF and GP as surrogate
models to handle various sorts of HP spaces. Additionally, BOHB (Falkner et al., 2018) is
implemented as a multi-fidelity approach in SMAC; however, instead of the TPE model in
BOHB, SMAC utilized RF as a surrogate model for BO part. The most recent version of SMAC
(Lindauer et al., 2021) also allows for parallel optimization with dask (“dask”, n.d.).

                                                61

Similarly, HyperMapper (Nardi et al., 2019) also builds an RF as a surrogate model; thus,
it also supports mixed and structured HP configuration spaces. However, HyperMapper does
not implement a Bayesian Optimization approach in a strict sense. In addition, HyperMapper
incorporates previous knowledge by rescaling the samples with a beta distribution and handles
unknown constraints by training another RF model as a probabilistic classifier. Furthermore, it
supports multi-objective optimization. As HyperMapper cannot be allocated to a distributed
computing environment, it might not be applicable to large-scale HPO problems.
OpenBox (Y. Li et al., 2021) is a general framework for black-box optimization – including
HPO – and supports multi-objective optimization, multi-fidelity, early-stopping, transfer learning, and parallel BO via distributed parallelization under both synchronous parallel settings and
asynchronous parallel settings. OpenBox further implements an ensemble surrogate model to
integrate the information from previously seen similar tasks.
Dragonfly (Kandasamy et al., 2020) is an open source library for scalable BO. Dragonfly extends
standard BO in the following ways to scale to higher dimensional and expensive HPO problems:
it implements GPs with additive kernels and additive GP-UCB (Kandasamy et al., 2015) as an
acquisition function. In addition, it supports multi-fidelity approach to scale to expensive HPO
problems. To increase the robustness of the system w.r.t. its HPs, Dragonfly acquires its GP HP
by either sampling a set of GP HPs from the posterior, conditioned on the data or optimizing
the likelihood to handle different degrees of smoothness of the optimized loss landscape. In
addition, Dragonfly maximizes its acquisition function with an evolutionary algorithm, which
enables the system to work on different sorts of configuration spaces, e.g., different variable
types and constraints.
The previously described BO-based HPO tools fix their search space during the optimization
phases. The package Bayesian Optimization (Nogueira, 2014) can concentrate a domain
around the current optimal values and adjust this domain dynamically (Stander & Craig, 2002).
A similar idea is implemented in TurBO (Eriksson et al., 2019), which only samples inside a trust
region while keeping the configuration space fixed. The trust region shrinks or extends based on
the performance of TurBO’s suggested configuration.
GPflowOpt (Knudde et al., n.d.) and BoTorch (Balandat et al., 2020) are the BO-based HPO
frameworks that build on top of GPflow and GPyTorch, respectively. The auto-grading system
facilitates the users to freely extend their own ideas to existing models. Ax adds an easy-to-use
API on top of BoTorch.
DEHB (Awad et al., n.d.) uses differential evolution (DE) and combines it with a multi-fidelity
HPO framework, inspired by BOHB’s combination of hyperband (L. Li et al., 2018) and BO.
DEHB overcomes several drawbacks of BOHB: it does not require a surrogate model, and thus, its
runtime overhead does not grow over time; DEHB can have stronger final performance compared
to BOHB, especially for discrete-valued and high-dimensional problems, where BO usually fails,
as it tends to suggest points on the boundary of the search space (Oh et al., 2018); additionally,
it is simpler to implement an efficient parallel DE algorithm compared to a parallel BO-based
approach. For instance, DEAP (Fortin et al., 2012) and Nevergrad (Rapin & Teytaud, 2018)
have many EA implementations that enable parallel computation.
There are several other general HPO tools. For instance, Optuna (Akiba et al., 2019) is an
automatic HP optimization software framework that allows to dynamically construct the search
space (Define-by-run API) and thus makes it much easier for users to construct a highly
complex search space. Orı́on (Bouthillier et al., 2020) is an asynchronous framework for black-

                                               62

box function optimization. Finally, Tune (Liaw et al., n.d.) is a scalable HP tuning framework
that provides APIs for several optimizers mentioned before, e.g., Dragonfly, SKopt, or HyperOpt.
D.2.3 AutoML tools
So far, we discussed several HPO tools in Python, which allow for flexible applications of different
algorithms, search spaces and data sets. Here, we will briefly discuss several AutoML-packages
of which HPO is a part.
Auto-Weka (Thornton et al., 2013) is one of the first AutoML tools that automates the design
choice of entire ML packages, namely Weka, with SMAC. Similarly, the same optimizer is applied to
Auto-sklearn (Feurer et al., 2015), while a meta-learning and ensemble approach further boost
the performance of auto-sklearn. Similarly, Auto-keras (Jin et al., 2019) and Auto-PyTorch
(Zimmer et al., 2021) use BO to automate the design choices of deep NNs. TPOT (Olson et
al., 2016) automates the design choice of an ML pipeline with genetic algorithms, while a
probabilistic grammatical evolution is implemented in AutoGOAL (Estévez-Velarde et al., 2020)
for HPO problems. H2O automl (LeDell & Poirier, 2020) and AutoGluon (Erickson et al., n.d.)
train a set of base models and ensemble them with a single layer (H2O) or multi-layer stacks
(AutoGluon). Additionally, TransmogrifAI (Moore et al., n.d.) is an AutoML tool built on the
top of Apache Spark and MLBox (Aronio de Romblay et al., n.d.).

References
Abadi, M., Agarwal, A., Barham, P., Brevdo, E., Chen, Z., Citro, C., Corrado, G., Davis, A.,
          Dean, J., Devin, M., Ghemawat, S., Goodfellow, I., Harp, A., Irving, G., Isard, M., Jia,
          Y., Jozefowicz, R., Kaiser, L., Kudlur, M., . . . Zheng, X. (2015). TensorFlow: Large-
          Scale Machine Learning on Heterogeneous Systems. https://www.tensorflow.org/
Akiba, T., Sano, S., Yanase, T., Ohta, T., & Koyama, M. (2019). Optuna: A Next-Generation
          Hyperparameter Optimization Framework. Proceedings of the 25th ACM SIGKDD In-
          ternational Conference on Knowledge Discovery & Data Mining, 2623–2631.
Allaire, J., & Chollet, F. (2020). keras: R Interface to ’Keras’. https://CRAN.R- project.org/
          package=keras
Aronio de Romblay, A., Cherel, N., Maskani, M., & Gerard, H. (n.d.). MLBox. https://mlbox.
          readthedocs.io/en/latest/index.html
Arya, S., Mount, D. M., Netanyahu, N. S., Silverman, R., & Wu, A. Y. (1998). An optimal
          algorithm for approximate nearest neighbor searching fixed dimensions. Journal of the
          ACM (JACM), 45 (6), 891–923.
Awad, N., Mallik, N., & Hutter, F. (n.d.). DEHB: Evolutionary Hyperband for Scalable, Robust
          and Efficient Hyperparameter Optimization. https://github.com/automl/DEHB
Balandat, M., Karrer, B., Jiang, D. R., Daulton, S., Letham, B., Wilson, A. G., & Bakshy, E.
          (2020). BoTorch: A Framework for Efficient Monte-Carlo Bayesian Optimization. In H.
          Larochelle, M. Ranzato, R. Hadsell, M. F. Balcan, & H. Lin (Eds.), Advances in Neural
          Information Processing Systems.
Bergstra, J., Bardenet, R., Bengio, Y., & Kégl, B. (2011). Algorithms for Hyper-Parameter
          Optimization (J. Shawe-Taylor, R. Zemel, P. Bartlett, F. Pereira, & K. Weinberger,
          Eds.), 2546–2554.
Bergstra, J., Yamins, D., & Cox, D. (2013). Making a Science of Model Search: Hyperparameter
          Optimization in Hundreds of Dimensions for Vision Architectures. In S. Dasgupta &
          D. McAllester (Eds.), Proceedings of the 30th International Conference on Machine
          Learning (pp. 115–123). PMLR.

                                                63

Beygelzimer, A., Kakadet, S., Langford, J., Arya, S., Mount, D., & Li, S. (2019). FNN: Fast
         Nearest Neighbor Search Algorithms and Applications. https://CRAN.R-project.org/
         package=FNN
Binder, M., Moosbauer, J., Thomas, J., & Bischl, B. (2020). Multi-objective hyperparameter
         tuning and feature selection using filter ensembles. Proceedings of the 2020 Genetic and
         Evolutionary Computation Conference, 471–479.
Binder, M., Pfisterer, F., Lang, M., Schneider, L., Kotthoff, L., & Bischl, B. (2021). mlr3pipelines
         - Flexible Machine Learning Pipelines in R. Journal of Machine Learning Research,
         22 (184), 1–7.
Bischl, B., Lang, M., Kotthoff, L., Schiffner, J., Richter, J., Studerus, E., Casalicchio, G., &
         Jones, Z. M. (2016). mlr: Machine Learning in R. Journal of Machine Learning Research,
         17 (170), 1–5.
Bischl, B., Richter, J., Bossek, J., Horn, D., Thomas, J., & Lang, M. (2017). mlrMBO: A
         Modular Framework for Model-Based Optimization of Expensive Black-Box Functions.
         arXiv: 1703.03373. Retrieved March 17, 2021, from http://arxiv.org/abs/1703.03373
Bischl, B., Schiffner, J., & Weihs, C. (2014). Benchmarking classification algorithms on high-
         performance computing clusters. Data Analysis, Machine Learning and Knowledge Dis-
         covery (pp. 23–31). Springer.
Bommert, A., Sun, X., Bischl, B., Rahnenführer, J., & Lang, M. (2020). Benchmark for filter
         methods for feature selection in high-dimensional classification data. Computational
         Statistics & Data Analysis, 143, 106839.
Borchers, H. W. (2018). adagio: Discrete and Global Optimization Routines. https://CRAN.R-
         project.org/package=adagio
Boser, B. E., Guyon, I. M., & Vapnik, V. N. (1992). A training algorithm for optimal margin
         classifiers. Proceedings of the fifth annual workshop on Computational learning theory,
         144–152.
Bossek, J. (2017). ecr 2.0: A modular framework for evolutionary computation in R. Proceedings
         of the Genetic and Evolutionary Computation Conference Companion, 1187–1193.
Bouthillier, X., Tsirigotis, C., Corneau-Tremblay, F., Schweizer, T., Delaunay, P., Bronzi, M.,
         Dong, L., Askari, R., Suhubdy, D., Bertrand, H., Noukhovitch, M., Bergeron, A.,
         Serdyuk, D., Henderson, P., Lamblin, P., & Beckham, C. (2020). Epistimio/orion: Plot-
         ting API and Database commands (Version v0.1.14). Zenodo. https : / / doi . org / 10 .
         5281/zenodo.3478592
Breiman, L. (2001). Random forests. Machine Learning, 45 (1), 5–32.
Breiman, L., Friedman, J., Stone, C. J., & Olshen, R. A. (1984). Classification and regression
         trees. CRC press.
Chang, C.-C., & Lin, C.-J. (2011). LIBSVM: A library for support vector machines. ACM Trans-
         actions on Intelligent Systems and Technology, 2, 27:1–27:27.
Chawla, N. V., Bowyer, K. W., Hall, L. O., & Kegelmeyer, W. P. (2002). SMOTE: Synthetic
         Minority Over-sampling Technique. Journal of Artificial Intelligence Research, 16, 321–
         357.
Chen, T., Li, M., Li, Y., Lin, M., Wang, N., Wang, M., Xiao, T., Xu, B., Zhang, C., & Zhang, Z.
         (2015). MXNet: A Flexible and Efficient Machine Learning Library for Heterogeneous
         Distributed Systems. CoRR, abs/1512.01274.
Chen, T., & Guestrin, C. (2016). XGBoost: A Scalable Tree Boosting System. Proceedings of
         the 22nd ACM SIGKDD International Conference on Knowledge Discovery and Data
         Mining, 785–794.

                                                64

Chen, T., He, T., Benesty, M., Khotilovich, V., Tang, Y., Cho, H., Chen, K., Mitchell, R., Cano,
         I., Zhou, T., Li, M., Xie, J., Lin, M., Geng, Y., & Li, Y. (2020). XGBoost: Extreme
         Gradient Boosting. https://CRAN.R-project.org/package=xgboost
Cortes, C., & Vapnik, V. (1995). Support-vector networks. Machine Learning, 20 (3), 273–297.
Cover, T., & Hart, P. (1967). Nearest neighbor pattern classification. IEEE Transactions on
         Information Theory, 13 (1), 21–27.
dask. (n.d.). https://dask.org/
de G. Matthews, A. G., van der Wilk, M., Nickson, T., Fujii, K., Boukouvalas, A., León-Villagrá,
         P., Ghahramani, Z., & Hensman, J. (2017). GPflow: A Gaussian process library using
         TensorFlow. Journal of Machine Learning Research, 18 (40), 1–6.
Ding, Y., & Simonoff, J. S. (2010). An Investigation of Missing Data Methods for Classification
         Trees Applied to Binary Response Data. Journal of Machine Learning Research, 11 (6),
         131–170.
Drucker, H., Burges, C. J. C., Kaufman, L., Smola, A., & Vapnik, V. (1996). Support Vec-
         tor Regression Machines. Proceedings of the 9th International Conference on Neural
         Information Processing Systems, 155–161.
Dunn, P., & Smyth, G. (2018). Generalized Linear Models With Examples in R. Springer.
Eggensperger, K., Lindauer, M., & Hutter, F. (2019). Pitfalls and Best Practices in Algorithm
         Configuration. Journal of Artificial Intelligence Research, 861–893.
Elsken, T., Metzen, J. H., & Hutter, F. (2019). Neural Architecture Search: A Survey. Journal
         of Machine Learning Research, 20 (55), 1–21.
Erickson, N., Mueller, J., Shirkov, A., Zhang, H., Larroy, P., Li, M., Smola, A., Klein, A., Tiao,
         L., Lienart, T., Archambeau, C., Seeger, M., Perrone, V., Donini, M., Zafar, B. M.,
         Schmucker, R., Kenthapadi, K., & Archambeau, C. (n.d.). AutoGluon.
Eriksson, D., Pearce, M., Gardner, J., Turner, R. D., & Poloczek, M. (2019). Scalable Global Op-
         timization via Local Bayesian Optimization. Advances in Neural Information Processing
         Systems, 5496–5507.
Estévez-Velarde, S., Gutiérrez, Y., Almeida-Cruz, Y., & Montoyo, A. (2020). General-purpose
         hierarchical optimisation of machine learning pipelines with grammatical evolution. In-
         formation Sciences.
Falbel, D., & Luraschi, J. (2020). torch: Tensors and Neural Networks with ’GPU’ Acceleration.
         https://CRAN.R-project.org/package=torch
Falkner, S., Klein, A., & Hutter, F. (2018). BOHB: Robust and Efficient Hyperparameter Opti-
         mization at Scale (J. Dy & A. Krause, Eds.). 80, 1437–1446.
Fernández-Delgado, M., Cernadas, E., Barro, S., & Amorim, D. (2014). Do We Need Hundreds
         of Classifiers to Solve Real World Classification Problems? The Journal of Machine
         Learning Research, 15 (1), 3133–3181.
Feurer, M., Klein, A., Eggensperger, K., Springenberg, J., Blum, M., & Hutter, F. (2015).
         Efficient and Robust Automated Machine Learning (C. Cortes, N. Lawrence, D. Lee,
         M. Sugiyama, & R. Garnett, Eds.), 2962–2970.
Fortin, F., De Rainville, F., Gardner, M., Parizeau, M., & Gagné, C. (2012). DEAP: Evolutionary
         Algorithms Made Easy. Journal of Machine Learning Research, 13, 2171–2175.
Freund, Y. (1995). Boosting a weak learning algorithm by majority. Information and Computa-
         tion, 121 (2), 256–285.
Freund, Y., & Schapire, R. E. (1997). A Decision-Theoretic Generalization of On-Line Learning
         and an Application to Boosting. Journal of Computer and System Sciences, 55 (1), 119–
         139.

                                               65

Friedman, J. (2001). Greedy function approximation: a gradient boosting machine. The Annals
         of Statistics, 29 (5), 1189–1232.
Friedman, J., Hastie, T., & Tibshirani, R. (2000). Additive logistic regression: a statistical view
         of boosting (with discussion and a rejoinder by the authors). The Annals of Statistics,
         28 (2), 337–407.
Friedman, J., Hastie, T., & Tibshirani, R. (2010). Regularization Paths for Generalized Linear
         Models via Coordinate Descent. Journal of Statistical Software, 33 (1), 1–22.
Garciarena, U., & Santana, R. (2017). An extensive analysis of the interaction between miss-
         ing data types, imputation methods, and supervised classifiers. Expert Systems with
         Applications, 89, 52–65.
Gardner, J. R., Pleiss, G., Bindel, D., Weinberger, K. Q., & Wilson, A. G. (2018). GPyTorch:
         Blackbox Matrix-Matrix Gaussian Process Inference with GPU Acceleration. Advances
         in Neural Information Processing Systems.
Gelbart, M., Snoek, J., & Adams, R. (2014). Bayesian Optimization with Unknown Constraints
         (N. Zhang & J. Tian, Eds.), 250–258.
Greenwell, B., Boehmke, B., Cunningham, J., & GBM Developers. (2020). gbm: Generalized
         Boosted Regression Models. https://CRAN.R-project.org/package=gbm
Gruber, S., & Bischl, B. (2019). mlr3hyperband: Hyperband for ’mlr3’. https://mlr3hyperband.
         mlr-org.com
Guyon, I., & Elisseeff, A. (2003). An Introduction to Variable and Feature Selection. The Journal
         of Machine Learning Research, 3, 1157–1182.
He, H., & Ma, Y. (2013). Imbalanced learning: foundations, algorithms, and applications. Wiley-
         IEEE Press.
Hechenbichler, K., & Schliep, K. (2004). Weighted k-nearest-neighbor techniques and ordinal
         classification (tech. rep.). SFB 386, Ludwig-Maximilians University, Munich.
Hoerl, A. E., & Kennard, R. W. (1970). Ridge regression: Biased estimation for nonorthogonal
         problems. Technometrics, 12 (1), 55–67.
Hothorn, T., Buehlmann, P., Kneib, T., Schmid, M., & Hofner, B. (2010). Model-based Boosting
         2.0. Journal of Machine Learning Research, 11, 2109–2113.
Hothorn, T., Hornik, K., & Zeileis, A. (2006). Unbiased recursive partitioning: A conditional
         inference framework. Journal of Computational and Graphical Statistics, 15 (3), 651–
         674.
Hothorn, T., & Zeileis, A. (2015). partykit: A modular toolkit for recursive partytioning in R.
         The Journal of Machine Learning Research, 16 (1), 3905–3909.
Hutter, F., Hoos, H., & Leyton-Brown, K. (2011). Sequential Model-Based Optimization for
         General Algorithm Configuration (C. Coello, Ed.). 6683, 507–523.
Jadhav, A., Pramod, D., & Ramanathan, K. (2019). Comparison of Performance of Data Im-
         putation Methods for Numeric Dataset. Applied Artificial Intelligence, 33 (10), 913–
         933.
Jin, H., Song, Q., & Hu, X. (2019). Auto-Keras: An Efficient Neural Architecture Search Sys-
         tem. Proceedings of the 25th ACM SIGKDD International Conference on Knowledge
         Discovery & Data Mining, 1946–1956.
Johnson, S. G. (2014). The NLopt nonlinear-optimization package. https : / / github . com /
         stevengj/nlopt
Kandasamy, K., Schneider, J., & Póczos, B. (2015). High Dimensional Bayesian Optimisation
         and Bandits via Additive Models (F. Bach & D. Blei, Eds.). 37, 295–304.
Kandasamy, K., Vysyaraju, K. R., Neiswanger, W., Paria, B., Collins, C. R., Schneider, J., Poczos,
         B., & Xing, E. P. (2020). Tuning Hyperparameters without Grad Students: Scalable and

                                                66

         Robust Bayesian Optimisation with Dragonfly. Journal of Machine Learning Research,
         21 (81), 1–27.
Karatzoglou, A., Smola, A., Hornik, K., & Zeileis, A. (2004). kernlab – An S4 Package for Kernel
         Methods in R. Journal of Statistical Software, 11 (9), 1–20.
Knudde, N., van der Herten, J., Dhaene, T., & Couckuyt, I. (n.d.). GPflowOpt. https : / /
         gpflowopt.readthedocs.io/en/latest/index.html
Krawczyk, B. (2016). Learning from imbalanced data: open challenges and future directions.
         Progress in Artificial Intelligence, 5 (4), 221–232.
Kuhn, M. (2008). Building Predictive Models in R Using the caret Package. Journal of Statistical
         Software, 28 (5), 1–26.
Kuhn, M. (2014). Futility Analysis in the Cross-Validation of Machine Learning Models. arXiv:
         1405.6974. Retrieved March 17, 2021, from http://arxiv.org/abs/1405.6974
Kuhn, M., & Johnson, K. (2019). Feature engineering and selection: A practical approach for
         predictive models. CRC Press.
Kuhn, M., & Wickham, H. (2020). tidymodels: a collection of packages for modeling and machine
         learning using tidyverse principles. https://www.tidymodels.org
Lang, M., Binder, M., Richter, J., Schratz, P., Pfisterer, F., Coors, S., Au, Q., Casalicchio, G.,
         Kotthoff, L., & Bischl, B. (2019). mlr3: A modern object-oriented machine learning
         framework in R. Journal of Open Source Software, 4 (44), 1903.
LeDell, E., & Poirier, S. (2020). H2O AutoML: Scalable Automatic Machine Learning. 7th ICML
         Workshop on Automated Machine Learning (AutoML).
LeDell, E., Gill, N., Aiello, S., Fu, A., Candel, A., Click, C., Kraljevic, T., Nykodym, T., Aboyoun,
         P., Kurka, M., & Malohlava, M. (2020). h2o: R Interface for the ’H2O’ Scalable Machine
         Learning Platform. https://CRAN.R-project.org/package=h2o
Li, L., Jamieson, K., DeSalvo, G., Rostamizadeh, A., & Talwalkar, A. (2018). Hyperband: A
         Novel Bandit-Based Approach to Hyperparameter Optimization. Journal of Machine
         Learning Research, 18 (185), 1–52.
Li, Y., Shen, Y., Zhang, W., Chen, Y., Jiang, H., Liu, M., Jiang, J., Gao, J., Wu, W., Yang, Z.,
         Zhang, C., & Cui, B. (2021). OpenBox: A Generalized Black-box Optimization Service.
         KDD, 3209–3219.
Liaw, R., Liang, E., Nishihara, R., Moritz, P., Gonzalez, J. E., & Stoica, I. (n.d.). Tune. https:
         //docs.ray.io/en/master/tune/index.html
Lindauer, M., K.Eggensperger, Feurer, M., Falkner, S., Biedenkapp, A., & Hutter, F. (2017).
         SMAC v3: Algorithm Configuration in Python. https://github.com/automl/SMAC3
Lindauer, M., Eggensperger, K., Feurer, M., Biedenkapp, A., Deng, D., Benjamins, C., Sass, R.,
         & Hutter, F. (2021). SMAC3: A Versatile Bayesian Optimization Package for Hyperpa-
         rameter Optimization.
López-Ibáñez, M., Dubois-Lacoste, J., Pérez Cáceres, L., Stützle, T., & Birattari, M. (2016).
         The irace package: Iterated Racing for Automatic Algorithm Configuration. Operations
         Research Perspectives, 3, 43–58.
Mebane, Jr., W. R., & Sekhon, J. S. (2011). Genetic Optimization Using Derivatives: The
         rgenoud Package for R. Journal of Statistical Software, 42 (11), 1–26.
Mersmann, O. (2014). mco: Multiple Criteria Optimization Algorithms and Related Functions.
         https://CRAN.R-project.org/package=mco
Meyer, D., Dimitriadou, E., Hornik, K., Weingessel, A., & Leisch, F. (2019). e1071: Misc Func-
         tions of the Department of Statistics, Probability Theory Group (Formerly: E1071), TU
         Wien. https://CRAN.R-project.org/package=e1071

                                                 67

Micci-Barreca, D. (2001). A preprocessing scheme for high-cardinality categorical attributes in
          classification and prediction problems. ACM SIGKDD Explorations Newsletter, 3 (1),
          27–32.
Moore, K., Kan, K. F., McGuire, L., Tovbin, M., Ovsiankin, M., Loh, M., Weil, M., Nabar, S.,
          Gordon, V., & Patryshev, V. (n.d.). TransmogrifAI. https://github.com/salesforce/
          TransmogrifAI
Mullen, K., Ardia, D., Gil, D., Windover, D., & Cline, J. (2011). DEoptim: An R Package for
          Global Optimization by Differential Evolution. Journal of Statistical Software, 40 (6),
          1–26.
Nardi, L., Koeplinger, D., & Olukotun, K. (2019). Practical design space exploration. 2019 IEEE
          27th International Symposium on Modeling, Analysis, and Simulation of Computer and
          Telecommunication Systems (MASCOTS), 347–358.
Nelder, J. A., & Wedderburn, R. W. (1972). Generalized linear models. Journal of the Royal
          Statistical Society: Series A (General), 135 (3), 370–384.
Nogueira, F. (2014). Bayesian Optimization: Open source constrained global optimization tool
          for Python. https://github.com/fmfn/BayesianOptimization
Oh, C., Gavves, E., & Welling, M. (2018). BOCK : Bayesian Optimization with Cylindrical
          Kernels (J. Dy & A. Krause, Eds.). 80, 3865–3874.
Olson, R., Bartley, N., Urbanowicz, R., & Moore, J. (2016). Evaluation of a Tree-based Pipeline
          Optimization Tool for Automating Data Science (T. Friedrich, Ed.), 485–492.
Pargent, F., Pfisterer, F., Thomas, J., & Bischl, B. (2021). Regularized target encoding outper-
          forms traditional methods in supervised machine learning with high cardinality features.
Paszke, A., Gross, S., Massa, F., Lerer, A., Bradbury, J., Chanan, G., Killeen, T., Lin, Z.,
          Gimelshein, N., Antiga, L., Desmaison, A., Kopf, A., Yang, E., DeVito, Z., Raison, M.,
          Tejani, A., Chilamkurthy, S., Steiner, B., Fang, L., . . . Chintala, S. (2019). PyTorch: An
          Imperative Style, High-Performance Deep Learning Library (H. Wallach, H. Larochelle,
          A. Beygelzimer, F. d’Alché-Buc, E. Fox, & R. Garnett, Eds.), 8024–8035.
Pedregosa, F., Varoquaux, G., Gramfort, A., Michel, V., Thirion, B., Grisel, O., Blondel, M.,
          Prettenhofer, P., Weiss, R., Dubourg, V., Vanderplas, J., Passos, A., Cournapeau, D.,
          Brucher, M., Perrot, M., & Duchesnay, E. (2011). Scikit-learn: Machine Learning in
          Python. Journal of Machine Learning Research, 12, 2825–2830.
Platt, J. (1999). Probabilistic outputs for support vector machines and comparisons to regularized
          likelihood methods. In A. J. Smola, P. Bartlett, B. Schölkopf, & D. Schuurmans (Eds.),
          Advances in Large Margin Classifiers (pp. 61–74). MIT Press.
Probst, P., & Boulesteix, A.-L. (2017). To tune or not to tune the number of trees in random
          forest. Journal of Machine Learning Research, 18 (1), 6673–6690.
Probst, P., Boulesteix, A.-L., & Bischl, B. (2019). Tunability: Importance of Hyperparameters
          of Machine Learning Algorithms. Journal of Machine Learning Research, 20 (53), 1–32.
Probst, P., Wright, M. N., & Boulesteix, A.-L. (2019). Hyperparameters and tuning strategies
          for random forest. WIREs Data Mining and Knowledge Discovery, 9 (3), e1301.
Rapin, J., & Teytaud, O. (2018). Nevergrad - A gradient-free optimization platform. https :
          //GitHub.com/FacebookResearch/Nevergrad
Roustant, O., Ginsbourger, D., & Deville, Y. (2012). DiceKriging, DiceOptim: Two R packages
          for the analysis of computer experiments by kriging-based metamodeling and optimiza-
          tion. Journal of Statistical Software, 51.
Sahidullah, M., & Saha, G. (2012). Design, analysis and experimental evaluation of block based
          transformation in MFCC computation for speaker recognition. Speech communication,
          54 (4), 543–565.

                                                 68

Schalk, D., Thomas, J., & Bischl, B. (2018). compboost: Modular Framework for Component-
         Wise Boosting. Journal of Open Source Software, 3 (30), 967.
Schapire, R. E. (1990). The strength of weak learnability. Machine Learning, 5 (2), 197–227.
Schliep, K., & Hechenbichler, K. (2016). kknn: Weighted k-Nearest Neighbors. https://CRAN.R-
         project.org/package=kknn
Schölkopf, B., Smola, A. J., Williamson, R. C., & Bartlett, P. L. (2000). New support vector
         algorithms. Neural Computation, 12 (5), 1207–1245.
scikit-optimize. (n.d.). https://scikit-optimize.github.io/
Snoek, J. (n.d.). Spearmint. https://github.com/HIPS/Spearmint/
Snoek, J., Larochelle, H., & Adams, R. (2012). Practical Bayesian Optimization of Machine
         Learning Algorithms (P. Bartlett, F. Pereira, C. Burges, L. Bottou, & K. Weinberger,
         Eds.), 2960–2968.
Snoek, J., Swersky, K., Zemel, R., & Adams, R. (2014). Input Warping for Bayesian Optimization
         of Non-stationary Functions (E. Xing & T. Jebara, Eds.), 1674–1682.
Stander, N., & Craig, K. (2002). On the robustness of a simple domain reduction scheme for
         simulation-based optimization. Engineering Computations, 19, 431–450.
Sweeney, R. E., & Ulveling, E. F. (1972). A Transformation for Simplifying the Interpretation
         of Coefficients of Binary Variables in Regression Analysis. The American Statistician,
         26 (5), 30–32.
Therneau, T., & Atkinson, B. (2019). rpart: Recursive Partitioning and Regression Trees. https:
         //CRAN.R-project.org/package=rpart
Thornton, C., Hutter, F., Hoos, H., & Leyton-Brown, K. (2013). Auto-WEKA: combined selec-
         tion and hyperparameter optimization of classification algorithms (I. Dhillon, Y. Koren,
         R. Ghani, T. Senator, P. Bradley, R. Parekh, J. He, R. Grossman, & R. Uthurusamy,
         Eds.), 847–855.
Tibshirani, R. (1996). Regression shrinkage and selection via the lasso. Journal of the Royal
         Statistical Society: Series B (Methodological), 58 (1), 267–288.
Trautmann, H., Mersmann, O., & Arnu, D. (2011). cmaes: Covariance Matrix Adapting Evolution
         Strategy. https://CRAN.R-project.org/package=cmaes
van der Laan, M. J., Polley, E. C., & Hubbard, A. E. (2007). Super Learner. Statistical Applica-
         tions in Genetics and Molecular Biology, 6 (1), Article 25.
Wah, Y. B., Ibrahim, N., Hamid, H. A., Abdul-Rahman, S., & Fong, S. (2018). Feature Se-
         lection Methods: Case of Filter and Wrapper Approaches for Maximising Classification
         Accuracy. Pertanika Journal of Science & Technology, 26 (1).
Woźnica, K., & Biecek, P. (2020). Does imputation matter? Benchmark for predictive models.
         arXiv: 2007.02837. Retrieved March 17, 2021, from http://arxiv.org/abs/2007.02837
Wright, M., & Ziegler, A. (2017). ranger: A fast implementation of random forests for high
         dimensional data in C++ and R. Journal of Statistical Software, 77 (1), 1–17.
Xiang, Y., Gubian, S., Suomela, B., & Hoeng, J. (2013). Generalized Simulated Annealing for
         Efficient Global Optimization: the GenSA Package for R. The R Journal, 5 (1), 13–28.
Xue, B., Zhang, M., & Browne, W. N. (2015). A comprehensive comparison on evolutionary
         feature selection approaches to classification. International Journal of Computational
         Intelligence and Applications, 14 (02), 1550008.
Yan, Y. (2016). rBayesianOptimization: Bayesian Optimization of Hyperparameters. https://
         CRAN.R-project.org/package=rBayesianOptimization
Zeng, Y., & Breheny, P. (2018). The biglasso Package: A Memory- and Computation-Efficient
         Solver for Lasso Model Fitting with Big Data in R.

                                               69

Zimmer, L., Lindauer, M., & Hutter, F. (2021). Auto-PyTorch Tabular: Multi-Fidelity Met-
        aLearning for Efficient and Robust AutoDL. IEEE Transactions on Pattern Analysis and
        Machine Intelligence, 1–12.
Zoph, B., Vasudevan, V., Shlens, J., & Le, Q. V. (2018). Learning Transferable Architectures
        for Scalable Image Recognition, 8697–8710.
Zou, H., & Hastie, T. (2005). Regularization and variable selection via the elastic net. Journal
        of the Royal Statistical Society: Series B (Statistical Methodology), 67 (2), 301–320.

                                              70
