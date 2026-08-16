---
citation_key: "RealEtAl2019"
title: "Regularized Evolution for Image Classifier Architecture Search"
authors: "Esteban Real; Alok Aggarwal; Yanping Huang; Quoc V. Le"
year: 2019
doi: "10.1609/aaai.v33i01.33014780"
source: "OA PDF (https://ojs.aaai.org/index.php/AAAI/article/download/4405/42)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
conversion: "pdftotext -layout (automated)"
---

# Regularized Evolution for Image Classifier Architecture Search

The Thirty-Third AAAI Conference on Artificial Intelligence (AAAI-19)

                Regularized Evolution for Image Classifier Architecture Search

                            Esteban Real,∗ Alok Aggarwal, Yanping Huang, Quoc V. Le
                                            Google Brain, Mountain View, California, USA
                                            Correspondence to E. Real at ereal@google.com

                            Abstract                                           To do this, we make two additions to the standard evo-
  The effort devoted to hand-crafting neural network image                  lutionary process. First, we propose a change to the well-
  classifiers has motivated the use of architecture search to dis-          established tournament selection evolutionary algorithm
  cover them automatically. Although evolutionary algorithms                (Goldberg and Deb 1991) that we refer to as aging evo-
  have been repeatedly applied to neural network topologies,                lution or regularized evolution. Whereas in tournament se-
  the image classifiers thus discovered have remained inferior              lection, the best genotypes (architectures) are kept, we pro-
  to human-crafted ones. Here, we evolve an image classifier—               pose to associate each genotype with an age, and bias the
  AmoebaNet-A—that surpasses hand-designs for the first time.               tournament selection to choose the younger genotypes. We
  To do this, we modify the tournament selection evolution-                 will show that this change turns out to make a difference.
  ary algorithm by introducing an age property to favor the                 The connection to regularization will be clarified in the Dis-
  younger genotypes. Matching size, AmoebaNet-A has com-
                                                                            cussion section. Second, we implement the simplest set of
  parable accuracy to current state-of-the-art ImageNet models
  discovered with more complex architecture-search methods.                 mutations that would allow evolving in the NASNet search
  Scaled to larger size, AmoebaNet-A sets a new state-of-the-               space (Zoph et al. 2018). This search space associates con-
  art 83.9% top-1 / 96.6% top-5 ImageNet accuracy. In a con-                volutional neural network architectures with small directed
  trolled comparison against a well known reinforcement learn-              graphs in which vertices represent hidden states and labeled
  ing algorithm, we give evidence that evolution can obtain re-             edges represent common network operations (such as con-
  sults faster with the same hardware, especially at the earlier            volutions or pooling layers). Our mutation rules only alter
  stages of the search. This is relevant when fewer compute re-             architectures by randomly reconnecting the origin of edges
  sources are available. Evolution is, thus, a simple method to             to different vertices and by randomly relabeling the edges,
  effectively discover high-quality architectures.                          covering the full search space.
                                                                               Searching in the NASNet space allows a controlled com-
                        Introduction                                        parison between evolution and the original method for
Until recently, most state-of-the-art image classifier archi-               which the space was designed, reinforcement learning (RL).
tectures have been manually designed by human experts                       Thus, this paper presents the first comparative case study
(Krizhevsky, Sutskever, and Hinton 2012; Szegedy et al.                     of architecture-search algorithms for the image classifica-
2015; He et al. 2016; Huang et al. 2017; Hu, Shen, and Sun                  tion task. Within this case study, we will demonstrate that
2018). To speed up the process, researchers have looked into                evolution can attain similar results with a simpler method,
automated methods (Baker et al. 2017a; Zoph and Le 2016;                    as will be shown in the Discussion section. In particular, we
Miikkulainen et al. 2017; Real et al. 2017; Xie and Yuille                  will highlight that in all our experiments evolution searched
2017; Suganuma, Shirakawa, and Nagao 2017; Liu et al.                       faster than RL and random search, especially at the earlier
2018a; Pham et al. 2018). These methods are now col-                        stages, which is important when experiments cannot be run
lectively known as architecture-search algorithms. A tra-                   for long times due to compute resource limitations.
ditional approach is neuro-evolution of topologies (Miller,                    Despite its simplicity, our approach works well in our
Todd, and Hegde 1989; Angeline, Saunders, and Pollack                       benchmark against RL. It also evolved a high-quality model,
1994; Stanley and Miikkulainen 2002). Improved hardware                     which we name AmoebaNet-A. This model is competitive
now allows scaling up evolution to produce high-quality                     with the best image classifiers obtained by any other algoimage classifiers (Real et al. 2017; Xie and Yuille 2017;                   rithm today at similar sizes (82.8% top-1 / 96.1% top-5 Im-
Liu et al. 2018b). Yet, the architectures produced by evolu-                ageNet accuracy). When scaled up, it sets a new state-oftionary algorithms / genetic programming have not reached                   the-art accuracy (83.9% top-1 / 96.6% top-5 ImageNet acthe accuracy of those directly designed by human experts.                   curacy).
Here we evolve image classifiers that surpass hand-designs.
   ∗                                                                                                 Related Work
     E. Real, A. Aggarwal, and Y. Huang contributed equally.
Copyright c 2019, Association for the Advancement of Artificial             Review papers provide informative surveys of earlier (Yao
Intelligence (www.aaai.org). All rights reserved.                           1999; Floreano, Dürr, and Mattiussi 2008) and more recent

                                                                     4780

(Elsken, Metzen, and Hutter 2018) literature on image clas-             therefore, is in line with our goal of keeping the method as
sifier architecture search, including successful RL studies             simple as possible. In particular, our method remains simi-
(Zoph and Le 2016; Baker et al. 2017a; Zoph et al. 2018;                lar to nature (where the young are less likely to die than the
Liu et al. 2018a; Zhong, Yan, and Liu 2018; Cai et al. 2018)            very old) and it requires no additional meta-parameters.
and evolutionary studies like those mentioned in the Introduction. Other methods have also been applied: cascade-                                          Methods
correlation (Fahlman and Lebiere 1990), boosting (Cortes et             This section contains a readable description of the methods.
al. 2017), hill-climbing (Elsken, Metzen, and Hutter 2017),             The Methods Details section gives additional information.
MCTS (Negrinho and Gordon 2017), SMBO (Mendoza et
al. 2016; Liu et al. 2018a), and random search (Bergstra and            Search Space
Bengio 2012), and grid search (Zagoruyko and Komodakis
2016). Some methods even forewent the idea of independent               All experiments use the NASNet search space (Zoph et al.
architectures (Saxena and Verbeek 2016). There is much                  2018). This is a space of image classifiers, all of which have
architecture-search work beyond image classification too,               the fixed outer structure indicated in Figure 1 (left): a feedbut that is outside our scope.                                          forward stack of Inception-like modules called cells. Each
   Even though some methods stand out due to their effi-                cell receives a direct input from the previous cell (as deciency (Suganuma, Shirakawa, and Nagao 2017; Pham et                    picted) and a skip input from the cell before it (Figure 1,
al. 2018), many approaches use large amounts of resources.              right). The cells in the stack are of two types: the normal
Several recent papers reduced the compute cost through                  cell and the reduction cell. All normal cells are constrained
progressive-complexity search stages (Liu et al. 2018a), hy-            to have the same architecture, as are reduction cells, but the
pernets (Brock et al. 2018), accuracy prediction (Baker et al.          architecture of the normal cells is independent of that of the
2017b; Klein et al. 2017; Domhan, Springenberg, and Hutter              reduction cells. Other than this, the only difference between
2017), warm-starting and ensembling (Feurer et al. 2015),               them is that every application of the reduction cell is folparallelization, reward shaping and early stopping (Zhong,              lowed by a stride of 2 that reduces the image size, whereas
Yan, and Liu 2018) or Net2Net transformations (Cai et al.               normal cells preserve the image size. As can be seen in the
2018). Most of these methods could in principle be applied              figure, normal cells are arranged in three stacks of N cells.
to evolution too, but this is beyond the scope of this paper.           The goal of the architecture-search process is to discover the
                                                                        architectures of the normal and reduction cells.
   A popular approach to evolution has been through generational algorithms, e.g. NEAT (Stanley and Miikkulainen
2002). In these, all models in the population must finish
training before the next generation is computed. Generational evolution becomes inefficient in a distributed environment where a different machine is used to train each model:
machines that train faster models finish earlier and must wait
idle until all machines are ready. Real-time algorithms address this issue, e.g. rtNEAT (Stanley, Bryant, and Miikkulainen 2005) and tournament selection (Goldberg and Deb
1991). Unlike the generational algorithms, however, these
discard models according to their performance or do not discard them at all, resulting in models that remain alive in the
population for a long time—even for the whole experiment.
We will present evidence that the finite lifetimes of aging
evolution can give better results than direct tournament selection, while retaining its efficiency.
   An existing paper (Hornby 2006) uses a concept of age
but in a very different way than we do. In that paper, age              Figure 1: NASNet Search Space outer structure (Zoph et al.
is assigned to genes to divide a constant-size population               2018). LEFT: the full outer structure, omitting skip inputs
into groups called age-layers. Each layer contains individ-             for clarity. RIGHT: detailed view with the skip inputs.
uals with genes of similar ages. Only after the genes have
survived a certain age-gap, they can make it to the next                   As depicted in Figure 1 (right) and Figure 2, each cell
layer. The goal is to restrict competition (the newly intro-            has two input activation tensors and one output. The very
duced genes cannot be immediately out-competed by highly                first cell takes two copies of the input image. After that, the
selected older ones). Their algorithm requires the introduc-            inputs are the outputs of the previous two cells.
tion of two additional meta-parameters (size of the age-gap                Both normal and reduction cells must conform to the foland number of age-layers). In contrast, in our algorithm, an            lowing construction. The two cell input tensors are conage is assigned to the individuals (not the genes) and is only          sidered hidden states “0” and “1”. More hidden states are
used to track which is the oldest individual in the popula-             then constructed through pairwise combinations. A pairwise
tion. This permits removing such oldest individual at each              combination is depicted in Figure 2 (inside dashed circle).
cycle (keeping a constant population size). Our approach,               It consists in applying an operation (or op) to an existing

                                                                 4781

                                                                          Algorithm 1 Aging Evolution (i.e. Regularized Evolution)
                                                                            population ← empty queue                . The population.
                                                                            history ← ∅                     . Will contain all models.
                                                                            while |population| < P do          . Initialize population.
                                                                                model.arch ← R ANDOM A RCHITECTURE()
                                                                                model.accuracy ← T RAINA ND E VAL(model.arch)
                                                                                add model to right of population
                                                                                add model to history
                                                                            end while
                                                                            while |history| < C do             . Evolve for C cycles.
                                                                                sample ← ∅                        . Parent candidates.
                                                                                while |sample| < S do
                                                                                    candidate ← random element from population
                                                                                               . The element stays in the population.
                                                                                    add candidate to sample
                                                                                end while
                                                                                parent ← highest-accuracy model in sample
                                                                                child.arch ← M UTATE(parent.arch)
                                                                                child.accuracy ← T RAINA ND E VAL(child.arch)
                                                                                add child to right of population
Figure 2: NASNet Search Space cell structure (Zoph et al.                       add child to history
2018). Example of a cell. Dotted line demarcates a pairwise                     remove dead from left of population           . Oldest.
combination.                                                                    discard dead
                                                                            end while
                                                                            return highest-accuracy model in history
hidden state, applying another op to another existing hidden
state, and adding the results to produce a new hidden state.
Ops belong to a fixed set of common convnet operations
such as convolutions and pooling layers. Repeating hidden                 parent. A new architecture, called the child, is constructed
states or operations within a combination is permitted. In the            from the parent by the application of a transformation called
cell example of Figure 2, the first pairwise combination ap-              a mutation. A mutation causes a simple and random modiplies a 3x3 average pool op to hidden state 0 and a 3x3 max               fication of the architecture and is described in detail below.
pool op to hidden state 1, in order to produce hidden state 2.            Once the child architecture is constructed, it is then trained,
The next pairwise combination can now choose from hidden                  evaluated, and added to the population. This process is called
states 0, 1, and 2 to produce hidden state 3 (chose 0 and 1               tournament selection (Goldberg and Deb 1991).
in Figure 2), and so on. After exactly five pairwise combina-                It is common in tournament selection to keep the poputions, any hidden states that remain unused (hidden states 5              lation size fixed at the initial value P. This is often accomand 6 in Figure 2) are concatenated to form the output of the             plished with an additional step within each cycle: discarding
cell (hidden state 7).                                                    (or killing) the worst model in the random S-sample. We will
   A given architecture is fully specified by the five pairwise           refer to this approach as non-aging evolution. In contrast,
combinations that make up the normal cell and the five that               in this paper we prefer a novel approach: killing the oldest
make up the reduction cell. Once the architecture is speci-               model in the population—that is, removing from the popufied, the model still has two free parameters that can be used            lation the model that was trained the earliest (“remove dead
to alter its size (and its accuracy): the number of normal cells          from left of pop” in Algorithm 1). This favors the newer
per stack (N) and the number of output filters of the convo-              models in the population. We will refer to this approach as
lution ops (F). N and F are determined manually.                          aging evolution. In the context of architecture search, aging
                                                                          evolution allows us to explore the search space more, instead
Evolutionary Algorithm                                                    of zooming in on good models too early, as non-aging evo-
The evolutionary method we used is summarized in Algo-                    lution would (see Discussion section for details).
rithm 1. It keeps a population of P trained models through-                  In practice, this algorithm is parallelized by distributing
out the experiment. The population is initialized with models             the “while |history|” loop in Algorithm 1 over multiple
with random architectures (“while |population|” in Algo-                  workers. Intuitively, the mutations can be thought of as prorithm 1). All architectures that conform to the search space              viding exploration, while the parent selection provides exdescribed are possible and equally likely.                                ploitation. The parameter S controls the aggressiveness of
   After this, evolution improves the initial population in cy-           the exploitation: S = 1 reduces to a type of random search
cles (“while |history|” in Algorithm 1). At each cycle, it                and 2 ≤ S ≤ P leads to evolution of varying greediness.
samples S random models from the population, each drawn                      New models are constructed by applying a mutation to
uniformly at random with replacement. The model with the                  existing models, transforming their architectures in ranhighest validation fitness within this sample is selected as the          dom ways. To navigate the NASNet search space described

                                                                   4782

above, we use two main mutations that we call the hidden                  Experimental Setup
state mutation and the op mutation. A third mutation, the                 We ran controlled comparisons at scale, ensuring identical
identity, is also possible. Only one of these mutations is ap-            conditions for evolution, RL and random search (RS). In
plied in each cycle, choosing between them at random.                     particular, all methods used the same computer code for net-
                                                                          work construction, training and evaluation. Experiments al-
                                                                          ways searched on the CIFAR-10 dataset (Krizhevsky and
                                                                          Hinton 2009).
                                                                             As in the baseline study, we first performed architec-
                                                                          ture search over small models (i.e. small N and F) until
                                                                          20k models were evaluated. After that, we used the model
                                                                          augmentation trick (Zoph et al. 2018): we took architec-
                                                                          tures discovered by the search (e.g. the output of an evo-
                                                                          lutionary experiment) and turned them into a full-size, ac-
                                                                          curate models. To accomplish this, we enlarged the mod-
                                                                          els by increasing N and F so the resulting model sizes
                                                                          would match the baselines, and we trained the enlarged
                                                                          models for a longer time on the CIFAR-10 or the Ima-
                                                                          geNet classification datasets (Krizhevsky and Hinton 2009;
                                                                          Deng et al. 2009). For ImageNet, a stem was added at the
                                                                          input of the model to reduce the image size, as shown in
                                                                          Figure 6 (left). This is the same procedure as in the baseline
                                                                          study. To produce the largest model (see last paragraph of
      Figure 3: Illustration of the two mutation types.                   Results section), we increased N and F until we ran out of
                                                                          memory. Actual values of N and F for all models are listed
                                                                          in the Methods Details section.
   The hidden state mutation consists of first making a random choice of whether to modify the normal cell or the re-                                    Methods Details
duction cell. Once a cell is chosen, the mutation picks one of
the five pairwise combinations uniformly at random. Once                  This section complements the Methods section with the dethe pairwise combination is picked, one of the two elements               tails necessary to reproduce our experiments. Possible ops:
of the pair is chosen uniformly at random. The chosen ele-                none (identity); 3x3, 5x5 and 7x7 separable (sep.) convolument has one hidden state. This hidden state is now replaced              tions (convs.); 3x3 average (avg.) pool; 3x3 max pool; 3x3
with another hidden state from within the cell, subject to the            dilated (dil.) sep. conv.; 1x7 then 7x1 conv. Evolved with
constraint that no loops are formed (to keep the feed-forward             P =100, S=25. CIFAR-10 dataset (Krizhevsky and Hinton
nature of the convnet). Figure 3 (top) shows an example.                  2009) with 5k withheld examples for validation. Standard
   The op mutation behaves like the hidden state mutation                 ImageNet dataset (Deng et al. 2009), 1.2M 331x331 images
as far as choosing one of the two cells, one of the five pair-            and 1k classes; 50k examples withheld for validation; stanwise combinations, and one of the two elements of the pair.               dard validation set used for testing. During the search phase,
Then it differs in that it modifies the op instead of the hidden          each model trained for 25 epochs; N=3/F=24, 1 GPU. Each
state. It does this by replacing the existing op with a random            experiment ran on 450 K40 GPUs for 20k models (approx.
choice from a fixed list of ops (see Methods Details). Fig-               7 days). To optimize evolution, we tried 5 configurations
ure 3 (bottom) shows an example.                                          with P/S of: 100/2, 100/50, 20/20, 100/25, 64/16, best was
                                                                          100/25. The probability of the identity mutation was fixed at
                                                                          the small, arbitrary value of 0.05 and was not tuned. Other
Baseline Algorithms                                                       mutation probabilities were uniform, as described in the
                                                                          Methods. To optimize RL, started with parameters already
Our main baseline is the application of RL to the same                    tuned in the baseline study and further optimized learning
search space. RL was implemented using the algorithm and                  rate in 8 configurations: 0.00003, 0.00006, 0.00012, 0.0002,
code in the baseline study (Zoph et al. 2018). An LSTM con-               0.0004, 0.0008, 0.0016, 0.0032; best was 0.0008. To avoid
troller outputs the architectures, constructing the pairwise              selection bias, plots do not include optimization runs, as was
combinations one at a time, and then gets a reward for each               decided a priori. Best few (20) models were selected from
architecture by training and evaluating it. More detail can be            each experiment and augmented to N=6/F=32, as in basefound in the baseline study. We also compared against ran-                line study; batch 128, SGD with momentum rate 0.9, L2
dom search (RS). In our RS implementation, each model is                  weight decay 5×10−4 , initial lr 0.024 with cosine decay, 600
constructed randomly so that all models in the search space               epochs, ScheduledDropPath to 0.7 prob; auxiliary softmax
are equally likely, as in the initial population in the evolu-            with half-weight of main softmax. For Table 1, we used N/F
tionary algorithm. In other words, the models in RS exper-                of 6/32 and 6/36. For ImageNet table, N/F were 6/190 and
iments are not constructed by mutating existing models, so                6/448 and standard training methods (Szegedy et al. 2017):
as to make new models independent from previous ones.                     distributed sync SGD with 100 P100 GPUs; RMSProp opti-

                                                                   4783

mizer with 0.9 decay and =0.1, 4 × 10−5 weight decay, 0.1                RL compared favorably against RS. It is important to note
label smoothing, auxiliary softmax weighted by 0.4; dropout               that the vertical axis of Figure 4 does not present the comprobability 0.5; ScheduledDropPath to 0.7 probability (as in              pute cost of the models, only their accuracy. Next, we will
baseline—note that this trick only contributes 0.3% top-1                 consider their compute cost as well.
ImageNet acc.); 0.001 initial lr, decaying every 2 epochs by                 As in the baseline study, the architecture-search experi-
0.97. Largest model used N=6/F=448. F always refers to the                ments above were performed over small models, to be able
number of filters of convolutions in the first stack; after each          to train them quicker. We then used the model augmentation
reduction cell, this number is doubled. Wherever applicable,              trick (Zoph et al. 2018) by which we take an architecture
we used the same conditions as the baseline study.                        discovered by the search (e.g. the output of an evolutionary
                                                                          experiment) and turn it into a full-size, accurate model, as
                           Results                                        described in the Methods.
Comparison With RL and RS Baselines
Currently, reinforcement learning (RL) is the predominant
method for architecture search. In fact, today’s state-ofthe-art image classifiers have been obtained by architecture search with RL (Zoph et al. 2018; Liu et al. 2018a).
Here we seek to compare our evolutionary approach against
their RL algorithm. We performed large-scale side-by-side
architecture-search experiments on CIFAR-10. We first optimized the hyper-parameters of the two approaches independently (details in Methods Details section). Then we ran
5 repeats of each of the two algorithms—and also of random
search (RS).

                                                                          Figure 5: Final augmented models from 5 identical
                                                                          architecture-search experiments for each algorithm, on
                                                                          CIFAR-10. Each marker corresponds to the top models from
                                                                          one experiment.

                                                                             Figure 5 compares the augmented top models from the
                                                                          three sets of experiments. It shows test accuracy and model
                                                                          compute cost. The latter is measured in FLOPs, by which
                                                                          we mean the total count of operations in the forward pass,
                                                                          so lower is better. Evolved architectures had higher accuracy
                                                                          (and similar FLOPs) than those obtained with RS, and lower
                                                                          FLOPs (and similar accuracy) than those obtained with RL.
                                                                          Number of parameters showed similar behavior to FLOPs.
Figure 4: Time-course of 5 identical large-scale experiments              Therefore, evolution occupied the ideal relative position in
for each algorithm (evolution, RL, and RS), showing ac-                   this graph within the scope of our case study.
curacy before augmentation on CIFAR-10. All experiments
were stopped when 20k models were evaluated, as done in
the baseline study. Note this plot does not show the compute              Table 1: CIFAR-10 testing set results for AmoebaNet-A,
cost of models, which was higher for the RL ones.                         compared to top model reported in the baseline study.
                                                                           Model                        # Params Test Error (%)
   Figure 4 shows the model accuracy as the experiments
                                                                           NASNet-A (baseline)                3.3 M          3.41
progress, highlighting that evolution yielded more accurate
                                                                           AmoebaNet-A (N=6, F=32)            2.6 M       3.40 ± 0.08
models at the earlier stages, which could become important
                                                                           AmoebaNet-A (N=6, F=36)            3.2 M       3.34 ± 0.06
in a resource-constrained regime where the experiments may
have to be stopped early (for example, when 450 GPUs for
7 days is too much). At the later stages, if we allow to run                 So far we have been comparing evolution with our reprofor the full 20k models (as in the baseline study), evolution             duction of the experiments in the baseline study, but it is also
produced models with similar accuracy. Both evolution and                 informative to compare directly against the results reported

                                                                   4784

Figure 6: AmoebaNet-A architecture. The overall model (Zoph et al. 2018) (LEFT) and the AmoebaNet-A normal cell (MID-
DLE) and reduction cell (RIGHT).

by the baseline study. We select our evolved architecture                                     Discussion
with highest validation accuracy and call it AmoebaNet-A
(Figure 6). Table 1 compares its test accuracy with the top           This section will suggest directions for future work, which
model of the baseline study, NASNet-A. Such a comparison              we will motivate by speculating about the evolutionary prois not entirely controlled, as we have no way of ensuring             cess and by summarizing additional minor results. The dethe network training code was identical and that the same             tails of these minor results have been relegated to the supplenumber of experiments were done to obtain the final model.            ments, as they are not necessary to understand or reproduce
The table summarizes the results of training AmoebaNet-A              our main results above.
at sizes comparable to a NASNet-A version, showing that                  Scope of results. Some of our findings may be restricted
AmoebaNet-A is slightly more accurate (when matching                  to the search spaces and datasets we used. A natural direcmodel size) or considerably smaller (when matching accu-              tion for future work is to extend the controlled comparison
racy). We did not train our model at larger sizes on CIFAR-           to more search spaces, datasets, and tasks, to verify general-
10. Instead, we moved to ImageNet to do further compar-               ity, or to more algorithms. Supplement A presents prelimiisons in the next section.                                            nary results, performing evolutionary and RL searches over
                                                                      three search spaces (SP-I: same as in the Results section;
                                                                      SP-II: like SP-I but with more possible ops; SP-III: like SP-
ImageNet Results                                                      II but with more pairwise combinations) and three datasets
                                                                      (gray-scale CIFAR-10, MNIST, and gray-scale ImageNet),
                                                                      at a small-compute scale (on CPU, F =8, N =1). Evolution
Following the accepted standard, we compare our top                   reached equal or better accuracy in all cases (Figure 7, top).
model’s classification accuracy on the popular ImageNet                  Algorithm speed. In our comparison study, Figure 4 sugdataset against other top models from the literature. Again,          gested that both RL and evolution are approaching a comwe use AmoebaNet-A, the model with the highest validation             mon accuracy asymptote. That raises the question of which
accuracy on CIFAR-10 among our evolution experiments.                 algorithm gets there faster. The plots indicate that evolution
We highlight that the model was evolved on CIFAR-10 and               reaches half-maximum accuracy in roughly half the time.
then transferred to ImageNet, so the evolved architecture             We abstain, nevertheless, from further quantifying this efcannot have overfit the ImageNet dataset. When re-trained             fect since it depends strongly on how speed is measured (the
on ImageNet, AmoebaNet-A performs comparably to the                   number of models necessary to reach accuracy a depends on
baseline for the same number of parameters (Table 2, model            a; the natural choice of a = amax /2 may be too low to be
with F=190).                                                          informative; etc.). Algorithm speed may be more important
   Finally, we focused on AmoebaNet-A exclusively and en-             when exploring larger spaces, where reaching the optimum
larged it, setting a new state-of-the-art accuracy on Ima-            can require more compute than is available. We saw an exgeNet of 83.9%/96.6% top-1/5 accuracy with 469M param-                ample of this in the SP-III space, where evolution stood out
eters (Table 2, model with F=448). Such high parameter                (Figure 7, bottom-right). Therefore, future work could excounts may be beneficial in training other models too but             plore evolving on even larger spaces, where the initial relawe have not managed to do this yet.                                   tive speed of evolution may be even more significant.

                                                               4785

Table 2: ImageNet classification results for AmoebaNet-A compared to hand-designs (top rows) and other automated methods
(middle rows). The evolved AmoebaNet-A architecture (bottom rows) reaches the current state of the art (SOTA) at similar
model sizes and sets a new SOTA at a larger size. All evolution-based approaches are marked with a ∗ . We omitted Squeezeand-Excite-Net because it was not benchmarked on the same ImageNet dataset version.
       Model                                          # Parameters # Multiply-Adds Top-1 / Top-5 Accuracy (%)
       Incep-ResNet V2 (Szegedy et al. 2017)               55.8M                13.2B                    80.4 / 95.3
       ResNeXt-101 (Xie et al. 2017)                       83.6M                31.5B                    80.9 / 95.6
       PolyNet (Zhang et al. 2017)                         92.0M                34.7B                    81.3 / 95.8
       Dual-Path-Net-131 (Chen et al. 2017)                79.5M                32.0B                    81.5 / 95.8
       GeNet-2 (Xie and Yuille 2017)∗                      156M                   –                      72.1 / 90.4
       Block-QNN-B (Zhong, Yan, and Liu 2018)∗               –                    –                      75.7 / 92.6
       Hierarchical (Liu et al. 2018b)∗                     64M                   –                      79.7 / 94.8
       NASNet-A (Zoph et al. 2018)                         88.9M                23.8B                    82.7 / 96.2
       PNASNet-5 (Liu et al. 2018a)                        86.1M                25.0B                    82.9 / 96.2
       AmoebaNet-A (N=6, F=190)∗                           86.7M                23.1B                    82.8 / 96.1
       AmoebaNet-A (N=6, F=448)∗                           469M                 104B                     83.9 / 96.6

                                                                       of their slower peers. Verifying this speculation could be the
                                                                       subject of future work. As mentioned in the Related Work
                                                                       section, in this work we only considered asynchronous al-
                                                                       gorithms (as opposed to generational evolutionary methods)
                                                                       to ensure high resource utilization. Future work may ex-
                                                                       plore how asynchronous and generational algorithms com-
                                                                       pare with regard to model accuracy.
                                                                          Benefits of aging evolution. Aging evolution also seemed
                                                                       advantageous in small-compute-scale experiments, shown in
                                                                       Figure 8 and presented in more detail in Supplement B.

Figure 7: TOP: Comparison of the final model accuracy
in five different contexts, from left to right: G-CIFAR/SP-
I, G-CIFAR/SP-II, G-CIFAR/SP-III, MNIST/SP-I and G-
ImageNet/SP-I. Each circle marks the top test accuracy at
the end of one experiment. BOTTOM: Search progress of
the experiments in the case of G-CIFAR/SP-II (LEFT, best
for RL) and G-CIFAR/SP-III (RIGHT, best for evolution).

   Model speed. The speed of individual models produced is
also relevant. Figure 5 demonstrated that evolved models are           Figure 8: Small-compute-scale comparison between our agfaster (lower FLOPs). We speculate that asynchronous evo-              ing tournament selection variant and the non-aging variant,
lution may be reducing the FLOPs because it is indirectly              for different population sizes (P) and sample sizes (S). Agoptimizing for speed even when training for a fixed number             ing tends to be beneficial (most markers above the y=x line).
of epochs: fast models may do well because they “reproduce” quickly even if they initially lack the higher accuracy            In Supplement B, we also show that the benefits of aging

                                                                4786

evolution tend to hold when varying the dataset or the search              evolution. The sophisticated nature of the RL alternative inspace. In order to reduce compute requirements, all these                  troduces complexity in its implementation: it requires backadditional experiments were carried out on CPU instead of                  propagation and poses challenges to parallelization (Sali-
GPU and used a gray-scale version of CIFAR-10.                             mans et al. 2017). Even different implementations of the
   Understanding aging evolution and regularization. We                    same algorithm have been shown to produce different results
can speculate that aging may help navigate the training                    (Henderson et al. 2018). Finally, evolution is also simple in
noise in evolutionary experiments, as follows. Noisy training              that it has few meta-parameters, most of which do not need
means that models may sometimes reach high accuracy just                   tuning (Real et al. 2017). In our study, we only adjusted 2
by luck. In non-aging evolution (NAE, i.e. standard tourna-                meta-parameters and only through a handful of attempts (see
ment selection), such lucky models may remain in the popu-                 Methods Details section). In contrast, note that the RL baselation for a long time—even for the whole experiment. One                  line requires training an agent/controller which is often itself
lucky model, therefore, can produce many children, caus-                   a neural network with many weights (such as an LSTM), and
ing the algorithm to focus on it, reducing exploration. Under              its optimization has more meta-parameters to adjust: learnaging evolution (AE), on the other hand, all models have                   ing rate schedule, greediness, batching, replay buffer parama short lifespan, so the population is wholly renewed fre-                 eters, etc. (These meta-parameters are all in addition to the
quently, leading to more diversity and more exploration. In                weights and training parameters of the image classifiers beaddition, another effect may be in play, which we describe                 ing searched, which are present in both approaches.) It is
next. In AE, because models die quickly, the only way an                   possible that through careful tuning, RL could be made to
architecture can remain in the population for a long time                  produce even better models than evolution, but such tunis by being passed down from parent to child through the                   ing would likely involve running many experiments, makgenerations. Each time an architecture is inherited it must                ing it more costly. Evolution did not require much tuning,
be re-trained. If it produces an inaccurate model when re-                 as described. It is also possible that random search would
trained, that model is not selected by evolution and the ar-               produce equally good models if run for a very long time,
chitecture disappears from the population. The only way for                which would be very costly. Finally, the evolutionary algoan architecture to remain in the population for a long time is             rithm could be improved through additional complexity; for
to re-train well repeatedly. In other words, AE can only im-               example, the mutation probabilities could be learned to improve a population through the inheritance of architectures                prove speed.
that re-train well. (In contrast, NAE can improve a popu-                     Interpreting architecture search. Another important dilation by accumulating architectures/models that were lucky                rection for future work is that of analyzing architecturewhen they trained the first time). That is, AE is forced to pay            search experiments (regardless of the algorithm used) to try
attention to architectures rather than models. In other words,             to discover new neural network design patterns. Anecdothe addition of aging involves introducing additional infor-               tally, for example, we found that architectures with high outmation to the evolutionary process: architectures should re-               put vertex fan-in (number of edges into the output vertex)
train well. This additional information prevents overfitting               tend to be favored in all our experiments. In fact, the modto the training noise, which makes it a form of regulariza-                els in the final evolved populations have a mean fan-in value
tion in the broader mathematical sense1 . Regardless of the                that is 3 standard deviations above what would be expected
exact mechanism, in Supplement C we perform experiments                    from randomly generated models. We verified this pattern
to verify the plausibility of the conjecture that aging helps              by training various models with different fan-in values and
navigate noise. There we construct a toy search space where                the results confirm that accuracy increases with fan-in, as
the only difficulty is a noisy evaluation. If our conjecture is            had been found in ResNeXt (Xie et al. 2017). Discovering
true, AE should be better in that toy space too. We found this             broader patterns may require designing search spaces specifto be the case. We leave further verification of the conjecture            ically for this purpose.
to future work, noting that theoretical results may prove use-                Additional AmoebaNets. Using variants of the evoful here.                                                                  lutionary process described, we obtained three additional
   Simplicity of aging evolution. A desirable feature of evo-              models, which we named AmoebaNet-B, AmoebaNet-C, and
lutionary algorithms is their simplicity. By design, the appli-            AmoebaNet-D. We describe these models and the process
cation of a mutation causes a random change. The process                   that led to them in detail in Supplement D, but we summarize
of constructing new architectures, therefore, is entirely ran-             here. AmoebaNet-B was obtained through through platformdom. What makes evolution different from random search is                  aware architecture search over a larger version of the NASthat only the good models are selected to be mutated. This                 Net space. AmoebaNet-C is simply a model that showed
selection tends to improve the population over time. In this               promise early on in the above experiments by reaching high
sense, evolution is simply “random search plus selection”. In              accuracy with relatively few parameters; we mention it here
outline, the process can be described briefly: “keep a popula-             for completeness, as it has been referenced in other work
tion of N models and proceed in cycles: at each cycle, copy-               (Cubuk et al. 2018). AmoebaNet-D was obtained by manmutate the best of S random models and kill the oldest in                  ually extrapolating the evolutionary process and optimizing
the population”. Implementation-wise, we believe the meth-                 the resulting architecture for training speed. It is very effiods of this paper are sufficient for a reader to understand                cient: AmoebaNet-D won the Stanford DAWNBench com-
                                                                           petition for lowest training cost on ImageNet (Coleman et
   1
       https://en.wikipedia.org/wiki/Regularization (mathematics)          al. 2018).

                                                                    4787

                        Supplements                                      Baker, B.; Gupta, O.; Naik, N.; and Raskar, R. 2017a.
The supplements can be found online at:                                  Designing neural network architectures using reinforcement
https://arxiv.org/abs/1802.01548                                         learning. In ICLR.
                                                                         Baker, B.; Gupta, O.; Raskar, R.; and Naik, N. 2017b. Ac-
                         Conclusion                                      celerating neural architecture search using performance pre-
This paper used an evolutionary algorithm to discover image              diction. ICLR Workshop.
classifier architectures. Our contributions are the following:           Bergstra, J., and Bengio, Y. 2012. Random search for hyper-
• We proposed aging evolution, a variant of tournament se-               parameter optimization. JMLR.
   lection by which genotypes die according to their age, fa-            Brock, A.; Lim, T.; Ritchie, J. M.; and Weston, N. 2018.
   voring the young. This improved upon standard tourna-                 Smash: one-shot model architecture search through hyper-
   ment selection while still allowing for efficiency at scale           networks. In ICLR.
   through asynchronous population updating. We open-
   sourced the code.2 We also implemented simple muta-                   Cai, H.; Chen, T.; Zhang, W.; Yu, Y.; and Wang, J. 2018.
   tions that permit the application of evolution to the popu-           Efficient architecture search by network transformation. In
   lar NASNet search space.                                              AAAI.
• We presented the first controlled comparison of algo-                  Chen, Y.; Li, J.; Xiao, H.; Jin, X.; Yan, S.; and Feng, J. 2017.
   rithms for image classifier architecture search in a case             Dual path networks. In NIPS.
   study of evolution, RL and random search. We showed                   Ciregan, D.; Meier, U.; and Schmidhuber, J. 2012. Multi-
   that evolution had somewhat faster search speed and stood             column deep neural networks for image classification. In
   out in the regime of scarcer resources / early stopping.              CVPR.
   Evolution also matched RL in final model quality, em-                 Coleman, C.; Kang, D.; Narayanan, D.; Nardi, L.; Zhao,
   ploying a simpler method.                                             T.; Zhang, J.; Bailis, P.; Olukotun, K.; Re, C.; and Zaharia,
• We evolved AmoebaNet-A (Figure 6), a competitive im-                   M. 2018. Analysis of dawnbench, a time-to-accuracy
   age classifier. On ImageNet, it is the first evolved model            machine learning performance benchmark. arXiv preprint
   to surpass hand-designs. Matching size, AmoebaNet-A                   arXiv:1806.01427.
   has comparable accuracy to top image-classifiers discov-              Cortes, C.; Gonzalvo, X.; Kuznetsov, V.; Mohri, M.; and
   ered with other architecture-search methods. At large size,           Yang, S. 2017. Adanet: Adaptive structural learning of arti-
   it sets a new state-of-the-art accuracy. We open-sourced              ficial neural networks. In ICML.
   code and checkpoint.3
                                                                         Cubuk, E. D.; Zoph, B.; Mane, D.; Vasudevan, V.; and Le,
                    Acknowledgments                                      Q. V. 2018. Autoaugment: Learning augmentation policies
                                                                         from data. arXiv.
We wish to thank Megan Kacholia, Vincent Vanhoucke, Xiaoqiang Zheng and especially Jeff Dean for their support                 Deng, J.; Dong, W.; Socher, R.; Li, L.-J.; Li, K.; and Feiand valuable input; Chris Ying for his work helping tune                 Fei, L. 2009. Imagenet: A large-scale hierarchical image
AmoebaNet models and for his help with specialized hard-                 database. In CVPR.
ware, Barret Zoph and Vijay Vasudevan for help with the                  Domhan, T.; Springenberg, J. T.; and Hutter, F. 2017. Speedcode and experiments used in their paper (Zoph et al. 2018),             ing up automatic hyperparameter optimization of deep neuas well as Jiquan Ngiam, Jacques Pienaar, Arno Eigenwillig,              ral networks by extrapolation of learning curves. In IJCAI.
Jianwei Xie, Derek Murray, Gabriel Bender, Golnaz Ghiasi,                Elsken, T.; Metzen, J.-H.; and Hutter, F. 2017. Simple and
Saurabh Saxena and Jie Tan for other coding contributions;               efficient architecture search for convolutional neural net-
Jacques Pienaar, Luke Metz, Chris Ying, Andrew Selle and                 works. ICLR Workshop.
the anonymous reviewers for manuscript comments, all the
above and Patrick Nguyen, Samy Bengio, Geoffrey Hinton,                  Elsken, T.; Metzen, J. H.; and Hutter, F. 2018. Neural archi-
Risto Miikkulainen, Jeff Clune, Kenneth Stanley, Yifeng Lu,              tecture search: A survey. arXiv.
David Dohan, David So, David Ha, Vishy Tirumalashetty,                   Fahlman, S. E., and Lebiere, C. 1990. The cascade-
Yoram Singer, and Ruoming Pang for helpful discussions;                  correlation learning architecture. In NIPS.
and the larger Google Brain team.                                        Feurer, M.; Klein, A.; Eggensperger, K.; Springenberg, J.;
                                                                         Blum, M.; and Hutter, F. 2015. Efficient and robust auto-
                         References                                      mated machine learning. In NIPS.
Angeline, P. J.; Saunders, G. M.; and Pollack, J. B. 1994.
                                                                         Floreano, D.; Dürr, P.; and Mattiussi, C. 2008. Neuroevo-
An evolutionary algorithm that constructs recurrent neural
                                                                         lution: from architectures to learning. Evolutionary Intellinetworks. IEEE transactions on Neural Networks.
                                                                         gence.
    2
      https://colab.research.google.com/github/google-research/          Goldberg, D. E., and Deb, K. 1991. A comparative analysis
google-research/blob/master/evolution/regularized evolution
algorithm/regularized evolution.ipynb
                                                                         of selection schemes used in genetic algorithms. FOGA.
    3                                                                    He, K.; Zhang, X.; Ren, S.; and Sun, J. 2016. Deep residual
      https://tfhub.dev/google/imagenet/amoebanet a n18 f448/
classification/1                                                         learning for image recognition. In CVPR.

                                                                  4788

Henderson, P.; Islam, R.; Bachman, P.; Pineau, J.; Precup,               Stanley, K. O., and Miikkulainen, R. 2002. Evolving neural
D.; and Meger, D. 2018. Deep reinforcement learning that                 networks through augmenting topologies. Evol. Comput.
matters. AAAI.                                                           Stanley, K. O.; Bryant, B. D.; and Miikkulainen, R. 2005.
Hornby, G. S. 2006. Alps: the age-layered population struc-              Real-time neuroevolution in the nero video game. TEVC.
ture for reducing the problem of premature convergence. In               Suganuma, M.; Shirakawa, S.; and Nagao, T. 2017. A
GECCO.                                                                   genetic programming approach to designing convolutional
Hu, J.; Shen, L.; and Sun, G. 2018. Squeeze-and-excitation               neural network architectures. In GECCO.
networks. CVPR.                                                          Szegedy, C.; Liu, W.; Jia, Y.; Sermanet, P.; Reed, S.;
Huang, G.; Liu, Z.; Weinberger, K. Q.; and van der Maaten,               Anguelov, D.; Erhan, D.; Vanhoucke, V.; and Rabinovich,
L. 2017. Densely connected convolutional networks. In                    A. 2015. Going deeper with convolutions. In CVPR.
CVPR.                                                                    Szegedy, C.; Ioffe, S.; Vanhoucke, V.; and Alemi, A. A.
Klein, A.; Falkner, S.; Springenberg, J. T.; and Hutter, F.              2017. Inception-v4, inception-resnet and the impact of resid-
2017. Learning curve prediction with bayesian neural net-                ual connections on learning. In AAAI.
works. ICLR.                                                             Wan, L.; Zeiler, M.; Zhang, S.; Le Cun, Y.; and Fergus, R.
Krizhevsky, A., and Hinton, G. 2009. Learning multiple                   2013. Regularization of neural networks using dropconnect.
layers of features from tiny images. Master’s thesis, Dept.              In ICML.
of Computer Science, U. of Toronto.                                      Xie, L., and Yuille, A. 2017. Genetic CNN. In ICCV.
Krizhevsky, A.; Sutskever, I.; and Hinton, G. E. 2012.                   Xie, S.; Girshick, R.; Dollár, P.; Tu, Z.; and He, K. 2017. Ag-
Imagenet classification with deep convolutional neural net-              gregated residual transformations for deep neural networks.
works. In NIPS.                                                          In CVPR.
Liu, C.; Zoph, B.; Shlens, J.; Hua, W.; Li, L.-J.; Fei-Fei, L.;          Yao, X. 1999. Evolving artificial neural networks. IEEE.
Yuille, A.; Huang, J.; and Murphy, K. 2018a. Progressive                 Zagoruyko, S., and Komodakis, N. 2016. Wide residual
neural architecture search. ECCV.                                        networks. In BMVC.
Liu, H.; Simonyan, K.; Vinyals, O.; Fernando, C.; and                    Zhang, X.; Li, Z.; Loy, C. C.; and Lin, D. 2017. Polynet:
Kavukcuoglu, K. 2018b. Hierarchical representations for                  A pursuit of structural diversity in very deep networks. In
efficient architecture search. In ICLR.                                  CVPR.
Mendoza, H.; Klein, A.; Feurer, M.; Springenberg, J. T.; and             Zhong, Z.; Yan, J.; and Liu, C.-L. 2018. Practical network
Hutter, F. 2016. Towards automatically-tuned neural net-                 blocks design with q-learning. In AAAI.
works. In Workshop on Automatic Machine Learning.                        Zoph, B., and Le, Q. V. 2016. Neural architecture search
Miikkulainen, R.; Liang, J.; Meyerson, E.; Rawal, A.; Fink,              with reinforcement learning. In ICLR.
D.; Francon, O.; Raju, B.; Navruzyan, A.; Duffy, N.; and                 Zoph, B.; Vasudevan, V.; Shlens, J.; and Le, Q. V.
Hodjat, B. 2017. Evolving deep neural networks. arXiv.                   2018. Learning transferable architectures for scalable im-
Miller, G. F.; Todd, P. M.; and Hegde, S. U. 1989. Designing             age recognition. In CVPR.
neural networks using genetic algorithms. In ICGA.
Negrinho, R., and Gordon, G. 2017. Deeparchitect: Automatically designing and training deep architectures. arXiv.
Pham, H.; Guan, M. Y.; Zoph, B.; Le, Q. V.; and Dean, J.
2018. Faster discovery of neural architectures by searching
for paths in a large model. ICLR Workshop.
Real, E.; Moore, S.; Selle, A.; Saxena, S.; Suematsu, Y. L.;
Le, Q.; and Kurakin, A. 2017. Large-scale evolution of
image classifiers. In ICML.
Salimans, T.; Ho, J.; Chen, X.; and Sutskever, I. 2017. Evolution strategies as a scalable alternative to reinforcement
learning. arXiv.
Saxena, S., and Verbeek, J. 2016. Convolutional neural fabrics. In NIPS.
Simmons, J. P.; Nelson, L. D.; and Simonsohn, U. 2011.
False-positive psychology: Undisclosed flexibility in data
collection and analysis allows presenting anything as significant. Psychological Science.
Srivastava, N.; Hinton, G.; Krizhevsky, A.; Sutskever, I.; and
Salakhutdinov, R. 2014. Dropout: A simple way to prevent
neural networks from overfitting. JMLR.

                                                                  4789
