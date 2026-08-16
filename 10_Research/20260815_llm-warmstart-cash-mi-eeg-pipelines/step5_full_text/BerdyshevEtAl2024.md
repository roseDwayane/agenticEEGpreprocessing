---
citation_key: "BerdyshevEtAl2024"
title: "EEG-Reptile: An Automatized Reptile-Based Meta-Learning Library for BCIs"
authors: "Daniil A. Berdyshev; Artem M. Grachev; Sergei L. Shishkin; Bogdan L. Kozyrskiy"
year: 2024
doi: ""
source: "arXiv (2412.19725)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "2412.19725"
conversion: "pdftotext -layout (automated)"
---

# EEG-Reptile: An Automatized Reptile-Based Meta-Learning Library for BCIs

EEG-Reptile: An Automatized Reptile-Based
                                         Meta-Learning Library for BCIs

arXiv:2412.19725v2 [cs.LG] 17 Jan 2025
                                                       Daniil A. Berdyshev1,3 , Artem M. Grachev2 , Sergei L.
                                                       Shishkin1 and Bogdan L. Kozyrskiy4
                                                       1
                                                         MEG Center, Moscow State University of Psychology and Education, Moscow,
                                                       Russia
                                                       2
                                                         Independent researcher, Berlin, Germany
                                                       3
                                                         Faculty of Computational Mathematics and Cybernetic, Lomonosov Moscow State
                                                       University, Moscow, Russia
                                                       4
                                                         Independent researcher, Lille, France
                                                       E-mail: danberdyshev@gmail.com

                                                       January 2025

                                                       Abstract.
                                                           Meta-learning, i.e., “learning to learn”, is a promising approach to enable efficient
                                                       BCI classifier training with limited amounts of data. It can effectively use collections of
                                                       in some way similar classification tasks, with rapid adaptation to new tasks where only
                                                       minimal data are available. However, applying meta-learning to existing classifiers and
                                                       BCI tasks requires significant effort. To address this issue, we propose EEG-Reptile,
                                                       an automated library that leverages meta-learning to improve classification accuracy
                                                       of neural networks in BCIs and other EEG-based applications. It utilizes the Reptile
                                                       meta-learning algorithm to adapt neural network classifiers of EEG data to the inter-
                                                       subject domain, allowing for more efficient fine-tuning for a new subject on a small
                                                       amount of data. The proposed library incorporates an automated hyperparameter
                                                       tuning module, a data management pipeline, and an implementation of the Reptile
                                                       meta-learning algorithm. EEG-Reptile automation level allows using it without deep
                                                       understanding of meta-learning. We demonstrate the effectiveness of EEG-Reptile
                                                       on two benchmark datasets (BCI IV 2a, Lee2019 MI) and three neural network
                                                       architectures (EEGNet, FBCNet, EEG-Inception). Our library achieved improvement
                                                       in both zero-shot and few-shot learning scenarios compared to traditional transfer
                                                       learning approaches.

                                         Keywords: BCI, meta-learning, Reptile, neural networks

                                         1. Introduction

                                         Brain-Computer Interfaces (BCIs) enable direct communication between the brain and
                                         external devices by translating neural activity into commands, bypassing neuromuscular
                                         pathways (Wolpaw et al. 2002). By allowing control of prosthetic limbs, exoskeletons, or
                                         computer interfaces through neural signals, BCIs can restore movement and autonomy,

EEG-Reptile                                                                                2

improving rehabilitation and quality of life. In particular, electroencephalography
(EEG) based noninvasive BCIs employing motor imagery (MI) are increasingly used
in neurorehabilitation, particularly for post-stroke patients, where they may facilitate
motor function recovery via neuroplasticity (Daly and Wolpaw 2008; Murphy and
Corbett 2009; Frolov et al. 2017; Cervera et al. 2018; Mane, Chouhan, et al. 2020;
Mane, Z. Wu, et al. 2022). Users of the MI BCIs imagine specific movements without
actual muscle activity, generating neural patterns that can be decoded into control
commands.
     The effectiveness of an EEG based BCI greatly depends on performance of its core
computational part, a classifier, which translates patterns of the recorded signals into
commands. A BCI classifier needs to be trained on its user’s individual data or at least
fine-tuned to it, due to great intersubject variability of the neural data. This variability
stems primarily from individual differences in brain anatomy and function. Even higher
variability is observed among post-stroke patients, as stroke-induced damage alters
neural signals in different ways. Moreover, different electrode positioning, recovering
or pathological processes, slow fluctuations of a patient’s physiological state and sources
of artifacts often make necessary classifier re-calibration even on daily basis. However,
the amount of training data which can be obtained from a single user, especially in a
single session, is limited. For a range of practical reasons, especially in patients with
significant disability, it is highly desirable to make procedures of training data collection
as short as possible or even exclude them completely.
     To address these issues, various transfer-learning techniques are being applied to
EEG-based BCIs (Azab et al. 2018, Huang et al. 2022, X. Duan et al. 2023, M. Li
and Xu 2024). The core idea of transfer learning is to find common features between
various user sessions and to perform classification based on these features (D. Wu et al.
2022). However, achieving this requires a large and relatively homogeneous dataset to
effectively extract these shared features (Azab et al. 2018, Guetschel et al. 2024). In
addition, the extraction of these features often requires the modification of the original
classifier, which prevents the building of more automated solutions (Cai and Hong 2024).
     In contrast, meta-learning focuses on learning how to learn by leveraging a still large
dataset, but consisting of smaller, sometimes disparate tasks. This approach enables
rapid adaptation to entirely new tasks from minimal data. More broadly, meta-learning
(Schmidhuber 1987, Thrun and Pratt 1998), also referred to as “learning to learn,”
involves training models on a range of tasks or datasets so that they can quickly adapt
to novel tasks with minimal additional data.
     Consequently, we find a meta-learning approach more suitable for BCI problems.
The available pre-training data often comes from numerous distinct BCI users, each
with only a small amount of data, and can thus be treated as separate tasks. Instead
of searching for common features across all these users, which may not exist at all, it is
more promising to train the classifier to quickly adapt to each new user.
     Being more specific, meta-learning approach provides an initialization scheme for
neural network parameters, that allows to adapt to new BCI users using very small

EEG-Reptile                                                                               3

amount of data. This initialization scheme is constructed using data from other users.
This approach can:

  • Enhance Adaptability: Enable models to generalize across different users and
    sessions by capturing common patterns in EEG data.
  • Reduce Calibration Time: Minimize the per-user data required for effective model
    tuning.
  • Improve Performance in Variable Conditions:              Handle variability due to
    physiological differences or neural alterations.

     However, some patients exhibit neural patterns that significantly deviate from the
average due to unique physiological or pathological conditions. Even with high-quality
EEG data, their brain activity may not align with common patterns learned during
meta-training. Including such outlier data in the meta-learning pool can degrade the
model’s generalization ability. Therefore, it is crucial to detect and handle these atypical
patients separately, ensuring that the model is trained using representative data while
developing methods to adapt to individual differences during personalization.
     Despite previous efforts (X. Wu and Chan 2022, T. Duan et al. 2020) to apply metalearning algorithms for BCIs, the focus on reducing the size of training datasets has been
limited. Specifically, Reptile (Nichol et al. 2018) and Model-Agnostic Meta-Learning
(MAML) (Finn et al. 2017) have not been explored in scenarios where minimal EEG
data is used for fine-tuning or zero-shot learning. Notably, previous studies on MAML
(T. Duan et al. 2020) involved moderate reductions in dataset size, while other approach
lost model agnostic nature (Tremmel et al. 2022, Han et al. 2024). We propose that
the application of general-purpose meta-learning algorithms will enable us to reduce the
amount of training data, needed for adaptation of the neural network for a new subject.
     Recent studies have demonstrated approaches for inter-subject adaptation of
neural network classifiers for EEG data. In a recent study (Ng and Guan 2024),
researchers employed extensively redesigned MAML like meta-learning algorithm,
achieving promising results in few-shot and zero-shot scenarios. However, this approach
involved a more complex meta-learning procedure, which hindered the adoption of
new neural network architectures for this method. Furthermore, hyperparameter
optimization for the meta-learning algorithm was not automated. Meta-learning library
presented in our work enables automatic hyperparameter tuning for meta-learning,
seamless integration of new neural network architectures. It can be applied with the
amount of data available in public datasets, and it is less computationally expensive
compared to foundation models and Self-Supervised Learning (SSL) approaches.
     In this study with our proposed meta-learning library EEG-Reptile‡, we focus on
the Motor Imagery (MI) paradigm to illustrate the advantages of our approach. MI
is widely used in real-world BCIs, enabling control of external devices by imagining
movements. This is crucial for post-stroke rehabilitation, where MI can activate neural
‡ EEG-Reptile GitHub: https://github.com/gasiki/EEG-Reptile

EEG-Reptile                                                                            4

pathways associated with movement, promoting neuroplasticity and aiding recovery.
However, MI tasks present significant challenges due to high inter-subject variability
and limited per-user data. By focusing on MI data, we aim to demonstrate how metalearning can effectively handle variability and data scarcity, leading to more adaptable
and efficient EEG-based BCIs for rehabilitation purposes. Our main contributions are:
  • We propose a method to filter subjects for model pre-training, ensuring a coherent
    pre-training subset and enhancing subsequent meta-learning performance.
  • We introduce an advanced meta-learning procedure for EEGNet, a widely used
    neural network for EEG classification, enabling it to operate effectively in few-shot
    and even zero-shot training regimes.
  • We propose an approach for tuning meta-learning hyperparameters on the fly.
  • We develop a highly automated meta-learning library and demonstrate its
    effectiveness on several neural network architectures and EEG datasets.

2. Methods

In our study, we present an approach for meta-learning based on the Reptile algorithm
for BCI applications. In this section, we describe used methods, machine learning models
and datasets used for training. In addition, we present data preprocessing and algorithm
of the proposed meta-learning method.

2.1. Datasets
The BCI-IV (2a) (Tangermann et al. 2012) and Lee 2019 (MI) (Lee et al. 2019)
datasets were used to train ML models and evaluate performance of the proposed
library. The datasets were loaded using the MOABB library (Aristimunha et al. 2023).
Data preprocessing was performed utilizing the Braindecode toolbox (Schirrmeister et
al. 2017). Both datasets are recorded using an imaginary movement paradigm. This
paradigm is interesting due to the differences in signal between users, which complicates
the transfer learning process. It is also possible to use this paradigm to construct BCI
systems.

2.1.1. BCI IV (2a) The BCI iv 2a dataset (Tangermann et al. 2012) comprises
electroencephalographic (EEG) recordings for 4 motor imagery tasks: right hand, left
hand, both feet and tongue. The dataset consists of 22-channel EEG data from 9
subjects, recorded at a sampling rate of 250 Hz. A total of 5184 epochs are included in
the dataset, 144 epochs per class for each subject. Prior to analysis, a band-pass filter
(4-38 Hz) and exponential moving standardization were applied to each EEG channel.
The duration of each data epoch was 4.5 seconds or 1125 measurements.

EEG-Reptile                                                                             5

2.1.2. Lee2019 MI The Lee2019 MI dataset (Lee et al. 2019) represents motor imagery
tasks comprising two classes: right hand and left hand. The data consists of 62 EEG
channels, recorded at a sampling rate of 1000 Hz. The dataset contains a total of 200
epochs per class to each of 54 subjects included in the dataset.
     The dataset was preprocessed to ensure compatibility with neural network
architectures designed for EEG analysis. Specifically, it was downsampled to 250 Hz to
reduce the dimensionality of the data. Then a band-pass filter (4-38 Hz) was applied,
followed by exponential moving standardization. Each epoch has a duration of 2.5
seconds and consists of 625 time points. To minimize the dimensionality of the dataset
and reduce computational requirements, we selectively retained only 20 EEG channels
that have been previously identified as relevant for motor imagery tasks in the work by
Lee et al. The selected channels were FC5, FC3, FC1, FC2, FC4, FC6, C5, C3, C1, Cz,
C2, C4, C6, CP5, CP3, CP1, CPz, CP2, CP4, CP6.

2.2. Models
In this study, we present a meta-learning approach, which can apply meta-learning
to any neural network trained with techniques similar to Stochastic Gradient Descent
(SGD). To evaluate the efficacy of the proposed approach, we employed three distinct
neural networks commonly used for EEG signal classification in MI tasks: EEGNet
(Lawhern et al. 2018), EEG-Inception (Motor Imagery) (C. Zhang et al. 2021), and
FBCNet (Mane, Chew, et al. 2021). These networks were selected for their ability to
effectively address the challenges of EEG-based brain-computer interfaces.
     EEGNet is a compact convolutional neural network designed specifically for EEGbased brain-computer interfaces. Inspired by the work of Vernon J. Lawhern et al., our
revised architecture consists of two distinct groups of layers: spatial feature extractors
and classifiers. These groups are separated to facilitate independent training and
application of different learning rates, allowing for more efficient optimization. EEGNet
effectively encapsulates well-known EEG feature extraction concepts for brain-computer
interfaces, leveraging depthwise and separable convolutions to achieve high performance
across various BCI paradigms, including P300 visual-evoked potentials, error-related
negativity responses (ERN), movement-related cortical potentials (MRCP), and sensory
motor rhythms (SMR).
     Two other neural networks used in our study were employed in their original form.
EEG-Inception (MI), a convolutional neural network architecture designed for accurate
and robust classification of EEG-based motor imagery (MI). This network was sourced
from the Braindecode Python library (Schirrmeister et al. 2017). FBCNet, a novel Filter-
Bank Convolutional Network proposed in R. Mane et al. study, which was obtained from
the TorchEEG python library (Z. Zhang et al. 2024).

EEG-Reptile                                                                           6

2.3. Reptile meta-learning algorithm
We utilize the Reptile meta-learning algorithm to ensure robustness across different
neural network architectures. This algorithm belongs to the class of Model Agnostic
Meta-Learning algorithms(Finn et al. 2017), which are applicable to any networks
trained using gradient descent. The Reptile algorithm (Nichol et al. 2018) optimizes
the initial weights θ of a neural network f (θ) over a distribution of tasks p(T ). It
adjusts the weights to move closer to a point in the weight space that is approximately
equidistant from the optimal weights for each task during the current training step. In
the context of EEG data, we treat classification problems for individual BCI users as
separate tasks. Consequently, during meta-training, Reptile updates the initial weights
of the neural network using an averaged difference between the initial weights θ and
user-specific weights θ ′i .
     Our implementation of Reptile (Alg. 1) introduces notable features:
  • multiple learning rate coefficients β1 & β2 for distinct groups of layers within the
    neural network (layers responsible for processing EEG-specific features θ1 vs. those
    responsible for final classification θ2 ).
  • flexibility to utilize any optimizer, such as Adam, to update weights during meta-
    training.
These features enable customized optimization strategies for different parts of the
network, making model weight updates more efficient during meta-training.

EEG-Reptile                                                                           7

Algorithm 1 Reptile algorithm
Require: p(T ): distribution over tasks, α, β: meta step size hyperparameters, M:
number of meta-learning epochs
Initialize the initial parameter vector θ and cleaned p′ (T ) using the Algorithm 2
 1: for i = 0 to M do
 2:    Sample batch of tasks Ti ∼ p′ (T ) with length N
 3:    for all Ti do
 4:        Sample K data points {x(k) , y(k) } from Ti
 5:        Evaluate ∇θ LTi (fθ ) w.r.t. K data points
 6:        θ ′i = θ − α∇θ LTi (fθ )
 7:    end for
 8:    if double meta weights β1 & β2 then
                                     P ′
 9:        Update: θ1 ← θ1 + βN1 (θ1 i − θ1 )
                                  β2 P
                                     N
                                         ′
10:        Update: θ2 ← θ2 + N (θ2 i − θ2 )
                                    N
11:    else
                            P ′
12:       Update: θ ← θ + Nβ (θ i − θ)
                                N
13:    end if
14: end for
15: return θ or θ1 & θ2

     In short, the Reptile implementation in our library leverages the agnostic nature
of this meta-learning algorithm and introduces adaptability through the use of multiple
coefficients and optimizers, enabling effective adaptation across diverse neural network
architectures and tasks.

2.4. EEG-Reptile Library
We introduce EEG-Reptile library, a collection of modules designed for using metalearning with neural networks for EEG classification, handling EEG datasets necessary
for meta-learning, hyperparameter tuning, and model fine-tuning. The library consists
of four primary modules: Data Storage, Hyperparameter Search, Meta-Learning, and
Fine-Tuning. A simplified scheme of the proposed library is shown on Figure 1.
     The Data Storage module facilitates the storage and retrieval of preprocessed EEG
data from multiple subjects, along with their associated metadata. This module provides
prepared datasets for other components within the library.
     The Hyperparameter Search module leverages the Optuna (Akiba et al. 2019)
library to perform hyperparameter tuning for both meta-learning and fine-tuning. For
meta-learning, this module searches optimal parameters, including:
  • Number of epochs for meta-learning.
  • Number of epochs within each meta-step.

EEG-Reptile                                                                           8

  • Learning rate for training the base model within each meta-step.
  • Multiple meta-learning rates (β).
  • Number of data points used in each meta-step (N).
For fine-tuning, this module searches optimal parameters, including:
  • Learning rate.
  • Linear approximation of the dependence between the number of epochs and the
    available data points.
Linear approximation is necessary because it is impossible to know the size of the finetuning set for the target subject in advance and to select a large enough validation
set to apply early stopping. In our case, during the selection of hyperparameters on
a non-target subject that did not participate in meta-training, we select such linear
approximation coefficients a and b so that mean classification quality is maximized for
a different size of fine-tuning set.
      The Meta-Learning module facilitates meta-learning on multiple subjects in various
regimes. This module initializes the model weights θ by computing the average weights
θ ′ of models trained for a few epochs on each subject. The proposed initialization
procedure helps exclude “outlier” subjects whose models have significantly different
optimal weight values from the mean of p(T ). The proportion of “outliers” removed is
denoted by γ (Alg. 2).The training regimes vary based on the chosen parameters for
the base meta-learning algorithm and the data partitioning strategy. Furthermore, this
module supports preparing a network pre-trained on the same datasets using standard
Transfer Learning.
      The Fine-Tuning module enables additional training of a previously meta-trained
model on a specific subject. It also gathers statistics on the fine-tuning process and
performs testing to assess the fine-tuning performance.

EEG-Reptile                                                                        9

                     Figure 1: Structure of EEG-Reptile library.

Algorithm 2 Weight Initialization Algorithm
Input: p(T ): a distribution over N tasks, γ: outlier removal rate
Output: θ ′ : final weight vector, p′ (T )
 1: Initialize θ randomly
              ′
 2: Initialize θ = 0 (same shape as θ)
 3: for each task Ti in p(T ) do
 4:    Train a model from θ on Ti to obtain θ i
 5:    θ ′ ← θ ′ + N1 θ i
 6: end for
 7: for each θ i do
 8:    di = | mean(θ ′ − θ i )|
 9: end for
10: n = ⌊γN⌋                                           ⊲ Number of outliers to remove
              ′
11: Obtain p (T ) by removing n tasks with the largest di from p(T )
            ′
12: Reset θ = 0
13: for Ti in p′ (T ) do
14:    θ ′ ← θ ′ + (N −n)
                       1
                          θi
15: end for
16: return θ ′ , p′ (T )

EEG-Reptile                                                                            10

                             Figure 2: Experiment design.

2.5. Experimental Setup
All experiments were conducted using the EEG-Reptile library. Prior to conducting
the experiments, we randomly selected five subjects from each dataset and designated
them as ”test subjects.” For each individual experiment, we began by removing data
from one test subject in each dataset. We then performed hyperparameter optimization
for meta-learning on the remaining subjects’ data. After completing the hyperparameter
tuning, we carried out meta-training and transfer learning, the latter serving as our
baseline approach. Next, we conducted another hyperparameter search to prepare for
the fine-tuning stage. Subsequently, we fine-tuned the model on the previously unseen
test subject and evaluated its classification accuracy without any additional training
(Zero-shot). The full experimental design is illustrated in Figure 2.
     The model’s performance in each experiment was evaluated using accuracy. This
metric was computed with varying amounts of data per class during the fine-tuning
stage. Specifically, we measured accuracy as we retrained the model on different numbers
of EEG data points (and their corresponding class labels) per class.
     Each experiment was repeated five times for each test subject, with different random
subsets of training data selected in each repetition. For evaluation, we used a fixed test
set composed of the last 20% of each dataset, ensuring equal amounts of data per
class. This approach allowed for a fair and consistent performance comparison across
all experiments.

3. Results

We conducted a comprehensive evaluation of classification performance on the BCI
Competition IV 2a dataset (four classes) and the Lee2019 (MI) dataset (two classes).
This evaluation was based on the average performance for five randomly selected test
subjects from each dataset. The results are presented in Figure 3.
    Our experimental design ensured that each test subject remained entirely unseen

EEG-Reptile                                                                            11

                                          BCI IV(2a)
             0.6

             0.5

             0.4

  Accuracy
             0.3

             0.2

             0.1

              0
                       0           4                          8         16

                                       Number of datapoints

                                         Lee2019 MI
             0.8

             0.7

             0.6

             0.5

  Accuracy
             0.4

             0.3

             0.2

             0.1

              0
                   0         2                  4                 8          16

                                       Number of datapoints

Figure 3: Comparison of group average Accuracy with 95% confidence intervals on BCI
IV 2a (four classes) and Lee2019 (MI) (two classes) datasets for baseline algorithms and
algorithms with meta-learning fine-tuned on subsets with different sizes and zero-shot.

during the meta-learning and zero-shot testing phases. This approach allowed us to
rigorously assess the effectiveness of the machine learning methods without the influence
of any fine-tuning steps.
     For all experiments, fine-tuning was performed on small data subsets of the target
subject, with different subset sizes shown on the x-axis. Each individual subset with
specified size was randomly chosen five times. Our results demonstrate that metalearning and transfer learning-based methods exceed performance of random guessing
(random guessing would give accuracy of 25% for 4 class and 50 % for 2 class), even
without fine-tuning on a new subject.
     Our analysis shows that achieving satisfactory classification quality for EEG
data from previously unseen subjects remains challenging in MI classification tasks.
This finding highlights the limitations of current state-of-the-art inter-subject transfer
learning methods. In particular, we observed that meta-learning approaches still fall
short of the standards required for reliable integration into a BCI system, highlighting

EEG-Reptile                                                                                                                       12

                                            BCI IV(2a)                                                     Lee2019 MI
                         0.1

                                                                                            0.1

  Accuracy difference                                               Accuracy difference
                        0.05                                                               0.05

                                                                                             0
                          0
                                                                                          −0.05

                               0            4        8         16                                 0    2       4        8    16

                                       Number of datapoints                                           Number of datapoints

                                   FBCNet             EEGNet                   EEG-inception (MI)

Figure 4: Comparison of group average of difference for Accuracy with 95% confidence
intervals on BCI IV 2a (four classes) and Lee2019 (MI) (two classes) datasets between
baseline algorithms and algorithms with meta-learning fine-tuned on subsets with
different sizes and zero-shot.

the need for further research.
     For the BCI IV 2a dataset, we found that meta-learning with the EEGNet model
achieved an average classification accuracy of 43% ± 7% without fine-tuning (zero-shot).
Notably, this result was surpassed by fine-tuning on small data subsets, which reached
a peak classification accuracy of 46% ± 5% when trained on only 16 data points (4 per
class).
     For the Lee2019 MI dataset, we observed that the highest zero-shot classification
accuracy was also achieved using EEGNet with meta-learning, at 71% ± 5%.
Furthermore, fine-tuning on small data subsets led to a modest improvement in
classification performance, reaching 72% ± 7%, when trained on 16 data points (8
per class).
     We present a graphical comparison (Fig. 4) illustrating the differences in average
classification accuracy for each model and dataset under both meta-learning and
baseline (transfer learning) conditions. For both datasets, EEGNet with meta-learning
significantly outperformed the baseline algorithm (p < 0.05, Wilcoxon signed-rank test).
Moreover, the observed improvement in average classification accuracy was statistically
significant at the 95% confidence level.
     For both datasets, we also observed that when fine-tuned on small data subsets (16
data points), EEGNet with meta-learning again outperformed the baseline algorithm
at a significance level of p < 0.05 and a confidence interval of 95%. This suggests that
EEGNet with meta-learning is not only effective for zero-shot classification but also
robust when fine-tuned on small data subsets.

EEG-Reptile                                                                            13

              0 

              0.4

   Accuracy
              0 

              0.2

              0.1

               0
                    0        4            8           16

                          Number of datapoints

Figure 5: Comparison of group average Accuracy with 95% confidence intervals on BCI
IV 2a (four classes) dataset between baseline algorithms and algorithms with metalearning applied with EEGNet optimization and without, fine-tuned on subsets with
different sizes and zero-shot.

     For other models, we found that two approaches exhibited positive differences in
average classification accuracy between meta-learning and baseline algorithms, but these
differences were smaller than the confidence interval of 95%. For the Lee2019 MI dataset,
we found that the results for FBCNet and EEG-inception (MI) pre-trained with metalearning were comparable to those of the baseline algorithm.
     Given the significant difference in classification accuracy between the EEG-
Net architecture and the other models, we evaluated EEGNet with the proposed
optimizations (hereafter referred to as Opt-EEGNet) to assess the effectiveness of these
modifications. As described previously, the key distinction between the two architectures
is that Opt-EEGNet separates convolutional and fully connected layers into distinct
groups. This arrangement enables the use of independent learning rates during finetuning, the allocation of individual β coefficients for meta-learning, and even the option
to freeze weights in one group while fine-tuning the other.
     To evaluate the effect of this modification, we performed a similar experiment
using the unmodified EEGNet (see Fig. 5). The results show that separating the
layers into distinct groups improves classification accuracy, even under a straightforward
transfer learning approach. Notably, both EEGNet and Opt-EEGNet benefit from
meta-learning, indicating that this optimization strategy can enhance the quality of
inter-subject knowledge transfer and meta-learning.

EEG-Reptile                                                                             14

4. Discussion

The results demonstrate that the proposed meta-learning library, EEG-Reptile, can
be used to improve classification performance. Furthermore, it allows relatively
autonomous operation, making it an attractive solution for various applications.
     Experiments without fine-tuning and with fine-tuning on small datasets (zero- and
few-shot) both showed higher accuracy using EEG-Reptile, particularly when paired
with an optimized network. This improvement holds even for small dataset sizes,
suggesting that our library can enhance classification accuracy given a sufficiently large,
though not excessive, amount of training data for neural networks. This is consistent
with results demonstrated in work (Berdyshev et al. 2023). There, it is shown that such
outcomes are achievable using meta-learning. It is possible to achieve similar or higher
accuracy improvement by utilizing EEG-Reptile library in such case.
     Results for optimized and not optimized EEGNet have demonstrated that such
optimization could improve classification accuracy in meta-learning and transfer
learning. The separation of layers into distinct groups may provide a beneficial
optimization strategy for other neural architectures as well. Further research is necessary
to fully explore the potential benefits of this approach and its applicability to different
models and tasks.
     One of the key features of EEG-Reptile is its ability to filter and select subjects
that are too different from the others, enabling automatic exclusion of overly specific
subjects from the meta training set. This feature is provided by the weight initialization
procedure presented in this work, which is close to the RANSAC method (Fischler and
Bolles 1981). As far as the authors know, such a method has not been applied for this
purpose in meta-learning approaches within the context of BCI. This capability can lead
to improved mean performance in inter-subject transfer learning.
     A recent study (Han et al. 2024) demonstrated higher classification accuracy
compared to our experiments; however, the key difference is that their models were
trained on all available data for each user. Our results, on the other hand, show a
significant improvement in classification performance when using a small number of EEG
epochs for unseen users. This was achieved through fully automated hyperparameter
optimization for meta-learning and fine-tuning. This stands in contrast to previous
works (Ahuja and Sethia 2024, X. Wu and Chan 2022, J. Li et al. 2023), where
researchers failed to demonstrate improved classification accuracy on small datasets
with cross-subject transfer learning. Such small datasets, in turn, represent the primary
use case for many practical BCI applications.
     One of the main challenges associated with meta-learning is the size of available
datasets in the sense of number of users. Collecting more even small sessions
may improve the performance of our approach. Another downside of the fully
automated meta-optimization could be an extensive search for hyperparameters. In
our experiments, it took approximately 24 hours with NVIDIA Tesla P100 GPU.

REFERENCES                                                                              15

5. Conclusion

In this article, we introduce EEG-Reptile, a meta-learning library designed to enhance
classification accuracy in BCI tasks with EEG data. Application of the Reptile
meta-learning algorithm using this library improved classification performance on two
benchmark datasets, BCI IV 2a and Lee2019 MI, in both zero-shot and few-shot learning
scenarios in comparison with a straightforward transfer learning approach. EEG-Reptile
provides a practical solution to improve the classification accuracy of EEG data, in the
cases of an insufficient amount of data for the target subject, which is essential for
various applications such as brain-computer interfaces and cognitive research.
     The proposed library has additional advantages over existing solutions. Firstly,
EEG-Reptile allows mostly autonomous operation, making it a valuable tool for
researchers and practitioners who may not have extensive expertise in deep learning or
meta-learning. Secondly, the proposed weights initialization procedure enables exclusion
of “outlier” subjects from the training set of tasks for meta-learning. Lastly, it can be
easily used with various neural network architectures, making it a versatile solution for
EEG-based applications.

Acknowledgments

This research was funded by the Russian Science Foundation, grant 22-19-00528.
Special thanks to E.I. Chetkin for his contribution in improving the readability of this
paper.

References

Ahuja, Chirag and Divyashikha Sethia (2024). “Harnessing Few-Shot Learning for EEG
     signal classification: a survey of state-of-the-art techniques and future directions”.
     Frontiers in Human Neuroscience 18, 1421922.
Akiba, Takuya, Shotaro Sano, Toshihiko Yanase, et al. (July 25, 2019). “Optuna:
     A Next-generation Hyperparameter Optimization Framework”. Proceedings of
     the 25th ACM SIGKDD International Conference on Knowledge Discovery &
     Data Mining. KDD ’19: The 25th ACM SIGKDD Conference on Knowledge
     Discovery and Data Mining. Anchorage AK USA: ACM, pp. 2623–2631. url:
     https://dl.acm.org/doi/10.1145/3292500.3330701.
Aristimunha, Bruno, Igor Carrara, Pierre Guetschel, et al. (2023). Mother of all BCI
    Benchmarks. Version 1.0.0. url: https://github.com/NeuroTechX/moabb.
Azab, Ahmed M, Jake Toth, Lyudmila S Mihaylova, et al. (2018). “A review on transfer
     learning approaches in brain–computer interface”. Signal processing and machine
     learning for brain-machine interfaces, 81–98.

REFERENCES                                                              16

Berdyshev, Daniil A., Artem M. Grachev, Sergei L. Shishkin, et al. (Sept. 28, 2023).
     “Meta-Optimization of Initial Weights for More Effective Few- and Zero-Shot
     Learning in BCI Classification”. 2023 IEEE Ural-Siberian Conference on Computa-
     tional Technologies in Cognitive Science, Genomics and Biomedicine (CSGB). 2023
     IEEE Ural-Siberian Conference on Computational Technologies in Cognitive Sci-
     ence, Genomics and Biomedicine (CSGB). Novosibirsk, Russian Federation: IEEE,
     pp. 263–267. url: https://ieeexplore.ieee.org/document/10329624/.
Cai, Miao and Jie Hong (2024). “Joint multi-feature extraction and transfer
     learning in motor imagery brain computer interface”. Computer Methods in
     Biomechanics and Biomedical Engineering 0(0). PMID: 39286921, 1–12. eprint:
     https://doi.org/10.1080/10255842.2024.2404541. url: https://doi.org/10.1080/10255842.2
Cervera, Marı́a A, Surjo R Soekadar, Junichi Ushiba, et al. (2018). “Brain-computer
     interfaces for post-stroke motor rehabilitation: a meta-analysis”. Annals of clinical
     and translational neurology 5(5), 651–663.
Daly, Janis J and Jonathan R Wolpaw (2008). “Brain–computer interfaces in
     neurological rehabilitation”. The Lancet Neurology 7(11), 1032–1043.
Duan, Tiehang, Mohammad Abuzar Shaikh, Mihir Chauhan, et al. (2020). “Meta
     Learn on Constrained Transfer Learning for Low Resource Cross Subject EEG
     Classification”. IEEE Access 8, 224791–224802.
Duan, Xu, Songyun Xie, Yanxia Lv, et al. (Jan. 2023). “A transfer learning-based feed-
     back training motivates the performance of SMR-BCI”. Journal of Neural Engi-
     neering 20(1), 016026. url: https://dx.doi.org/10.1088/1741-2552/acaee7.
Finn, Chelsea, Pieter Abbeel, and Sergey Levine (Aug. 6, 2017). “Model-Agnostic
     Meta-Learning for Fast Adaptation of Deep Networks”. Proceedings of the 34th
     International Conference on Machine Learning. Ed. by Doina Precup and Yee Whye
     Teh. Vol. 70. Proceedings of Machine Learning Research. PMLR, pp. 1126–1135.
     url: https://proceedings.mlr.press/v70/finn17a.html.
Fischler, Martin A. and Robert C. Bolles (June 1981). “Random sample consensus:
     a paradigm for model fitting with applications to image analysis and
     automated cartography”. Communications of the ACM 24(6), 381–395. url:
     https://dl.acm.org/doi/10.1145/358669.358692.
Frolov, Alexander A, Olesya Mokienko, Roman Lyukmanov, et al. (2017). “Post-stroke
     rehabilitation training with a motor-imagery-based brain-computer interface (BCI)-
     controlled hand exoskeleton: a randomized controlled multicenter trial”. Frontiers
     in neuroscience 11, 400.
Guetschel, Pierre, Sara Ahmadi, and Michael Tangermann (2024). “Review of deep
     representation learning techniques for brain–computer interfaces”. Journal of
     Neural Engineering 21(6), 061002.
Han, Ji-Wung, Soyeon Bak, Jun-Mo Kim, et al. (2024). “Meta-eeg: Meta-learning-
     based class-relevant eeg representation learning for zero-calibration brain–computer
     interfaces”. Expert Systems with Applications 238, 121986.

REFERENCES                                                                17

Huang, Xiuyu, Shuang Liang, Yuanpeng Zhang, et al. (2022). “Relation Learning
     Using Temporal Episodes for Motor Imagery Brain-Computer Interfaces”. IEEE
     Transactions on Neural Systems and Rehabilitation Engineering 31, 530–543.
Lawhern, Vernon J, Amelia J Solon, Nicholas R Waytowich, et al. (Oct. 1,
     2018). “EEGNet: a compact convolutional neural network for EEG-based
     brain–computer interfaces”. Journal of Neural Engineering 15(5), 056013. url:
     https://iopscience.iop.org/article/10.1088/1741-2552/aace8c.
Lee, Min-Ho, O-Yeon Kwon, Yong-Jeong Kim, et al. (2019). “EEG dataset and
     OpenBMI toolbox for three BCI paradigms: An investigation into BCI illiteracy”.
     GigaScience 8(5), giz002.
Li, Jingcong, Fei Wang, Haiyun Huang, et al. (June 2023). “A novel semi-supervised
     meta learning method for subject-transfer brain–computer interface”. Neural Net-
     works 163, 195–204. url: https://linkinghub.elsevier.com/retrieve/pii/S0893608023001740.
Li, Mingai and Dongqin Xu (2024). “Transfer learning in motor imagery brain computer
     interface: a review”. Journal of Shanghai Jiaotong University (Science) 29(1), 37–
     59.
Mane, Ravikiran, Effie Chew, Karen Chua, et al. (2021). FBCNet: A Multi-
     view Convolutional Neural Network for Brain-Computer Interface. arXiv:
     2104.01233 [cs.OH]. url: https://arxiv.org/abs/2104.01233.
Mane, Ravikiran, Tushar Chouhan, and Cuntai Guan (2020). “BCI for stroke
     rehabilitation: motor and beyond”. Journal of neural engineering 17(4), 041001.
Mane, Ravikiran, Zhenzhou Wu, and David Wang (2022). “Poststroke motor, cognitive
     and speech rehabilitation with brain–computer interface: a perspective review”.
     Stroke and vascular neurology 7(6).
Murphy, Timothy H and Dale Corbett (2009). “Plasticity during stroke recovery: from
     synapse to behaviour”. Nature reviews neuroscience 10(12), 861–872.
Ng, Han Wei and Cuntai Guan (2024). “Subject-independent meta-learning framework
     towards optimal training of eeg-based classifiers”. Neural Networks 172, 106108.
Nichol, Alex, Joshua Achiam, and John Schulman (2018). On First-Order Meta-Learning
     Algorithms. arXiv: 1803.02999 [cs.LG]. url: https://arxiv.org/abs/1803.02999.
Schirrmeister, Robin Tibor, Jost Tobias Springenberg, Lukas Dominique Josef Fiederer,
     et al. (2017). “Deep learning with convolutional neural networks for EEG decoding
     and visualization”. Human brain mapping 38(11), 5391–5420.
Schmidhuber, Jürgen (1987). “Evolutionary principles in self-referential learning, or on
     learning how to learn: The meta-meta-. hook”. MA thesis. Technical University of
     Munich, Germany. url: https://mediatum.ub.tum.de/813180.
Tangermann, Michael, Klaus-Robert Müller, Ad Aertsen, et al. (2012). “Review of the
     BCI competition IV”. Frontiers in neuroscience 6, 55.
Thrun, Sebastian and Lorien Y. Pratt, eds. (1998). Learning to Learn. Springer. url:
     https://doi.org/10.1007/978-1-4615-5529-2.

REFERENCES                                                                        18

Tremmel, Christoph, Jacobo Fernandez-Vargas, Dimitris Stamos, et al. (2022). “A meta-
    learning BCI for estimating decision confidence”. Journal of Neural Engineering
    19(4), 046009.
Wolpaw, Jonathan R, Niels Birbaumer, Dennis J McFarland, et al. (2002). “Brain–
    computer interfaces for communication and control”. Clinical neurophysiology
    113(6), 767–791.
Wu, Dongrui, Xue Jiang, and Ruimin Peng (2022). “Transfer learning for motor imagery
    based brain–computer interfaces: A tutorial”. Neural Networks 153, 235–253. url:
    https://www.sciencedirect.com/science/article/pii/S0893608022002131.
Wu, Xiaoli and Rosa H. M. Chan (July 11, 2022). “Does Meta-Learning Improve
    EEG Motor Imagery Classification?” 2022 44th Annual International Conference
    of the IEEE Engineering in Medicine & Biology Society (EMBC). 2022 44th
    Annual International Conference of the IEEE Engineering in Medicine & Biology
    Society (EMBC). Glasgow, Scotland, United Kingdom: IEEE, pp. 4048–4051. url:
    https://ieeexplore.ieee.org/document/9871035/.
Zhang, Ce, Young-Keun Kim, and Azim Eskandarian (Aug. 1, 2021). “EEG-
    inception: an accurate and robust end-to-end neural network for EEG-based
    motor imagery classification”. Journal of Neural Engineering 18(4), 046014. url:
    https://iopscience.iop.org/article/10.1088/1741-2552/abed81.
Zhang, Zhi, Sheng-hua Zhong, and Yan Liu (2024). “TorchEEGEMO: A deep
    learning toolbox towards EEG-based emotion recognition”. Expert Systems with
    Applications 249, 123550.
