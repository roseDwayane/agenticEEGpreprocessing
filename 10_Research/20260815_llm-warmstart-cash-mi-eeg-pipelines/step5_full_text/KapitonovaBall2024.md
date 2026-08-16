---
citation_key: "KapitonovaBall2024"
title: "Human-AI Teaming Using Large Language Models: Boosting Brain-Computer Interfacing (BCI) and Brain Research"
authors: "Maryna Kapitonova; Tonio Ball"
year: 2024
doi: "10.48550/arxiv.2501.01451"
source: "arXiv (2501.01451)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
conversion: "pdftotext -layout (automated)"
---

# Human-AI Teaming Using Large Language Models: Boosting Brain-Computer Interfacing (BCI) and Brain Research

H UMAN -AI T EAMING U SING L ARGE L ANGUAGE M ODELS :
                                                  B OOSTING B RAIN -C OMPUTER I NTERFACING (BCI) AND
                                                                    B RAIN R ESEARCH

                                                                                                  P REPRINT

arXiv:2501.01451v1 [cs.HC] 30 Dec 2024
                                                                 Maryna Kapitonova*                                         Tonio Ball
                                                            Department of Neurosurgery                            Department of Neurosurgery
                                                        University Hospital Freiburg, Germany                 University Hospital Freiburg, Germany
                                                                  NeuroMentum AI                                        NeuroMentum AI
                                                          kapitonova@neuromentum.ai                                 ball@neuromentum.ai

                                                                                               January 6, 2025

                                                                                                A BSTRACT
                                                     Recently, there is an increasing interest in using artificial intelligence (AI) to automate aspects of
                                                     the research process, or even autonomously conduct the full research cycle from idea generation,
                                                     over data analysis, to composing and evaluation of scientific manuscripts. Examples of working
                                                    “AI scientist” systems have been demonstrated for computer science tasks and running molecular
                                                     biology labs. While some approaches aim for full autonomy of the scientific AI, others rather aim
                                                     for leveraging human-AI teaming. Here, we address how to adapt such approaches for boosting
                                                     Brain-Computer Interface (BCI) development, as well as brain research resp. neuroscience at large.
                                                     We argue that at this time, a strong emphasis on human-AI teaming, in contrast to fully autonomous
                                                    “AI BCI researcher,” will be the most promising way forward. We introduce the collaborative
                                                     workspaces concept for human-AI teaming based on a set of Janusian design principles, looking
                                                     both ways, to the human as well as to the AI side. Based on these principles, we present ChatBCI, a
                                                     Python-based toolbox for enabling human-AI collaboration based on interaction with Large Language
                                                     Models (LLMs), designed for BCI research and development projects. We show how ChatBCI was
                                                     successfully used in a concrete BCI project on advancing motor imagery decoding from EEG signals.
                                                     Our approach can be straightforwardly extended to broad neurotechnological and neuroscientific
                                                     topics, and may by design facilitate human expert knowledge transfer to scientific AI systems in
                                                     general.

                                         Keywords Human-AI Collaboration · AI Scientist Systems · Large Language Models (LLMs) · Brain-Computer
                                         Interface (BCI) · EEG Signal Analysis · Janusian Design

                                         1       Introduction
                                         Human–Artificial Intelligence (AI) collaborations may play an important role in promoting scientific discovery Wang
                                         et al. [2023]. Efforts to automate research and development tasks have recently led to the creation of “AI scientists”—AI
                                         agents specialized to autonomously carry out various roles in the scientific process. AI scientists may run virtual or even
                                         physical labs (self-driving labs, SDLs Rapp et al. [2024]) and thus help to “super-charge” fields including biomedical
                                         research, material science, or computer science.
                                         For example, in an effort to leverage AI for biomedical research, Swanson and colleagues have recently created a
                                         virtual laboratory that brings together multiple AI scientists—implemented as large language models (LLMs) with
                                         specialized roles—to collaborate autonomously on objectives defined by human investigators. This system rapidly
                                         proposed nearly 100 designs for antibody fragments, known as nanobodies, capable of binding to the virus responsible
                                             ∗
                                                 www.neuromentum.ai

                                   Human-AI Teaming for BCI and Brain Research                                    P REPRINT

for COVID-19—accomplishing in a short span what might otherwise take a purely human team much longer Swanson
et al. [2024]. In the field of computer science, Lu and colleagues introduced an AI scientist system that aims to automate
the complete research cycle. They also use a LLM to survey scientific literature, formulate hypotheses, test them by
running experiments, and then compile the results into a written paper. The system goes a step further by performing its
own ‘peer review,’ evaluating and refining the outputs it generates Lu et al. [2024]. However, despite these impressive
first demonstrations, substantial challenges remain for AI-driven scientific discovery in general, as well as for the
adaptation of AI-scientists to the unique requirements that the scientific domains of research and development may
impose on such a system.
For AI-scientist systems (AISS) in general, for example, one major challenge is how to integrate the vast amount
of implicit, unstructured, and often context-dependent knowledge and expertise that human scientists rely on for
experimental design, interpretation, and innovation—which may not be sufficiently represented in the available training
data. For example, Rihm and colleagues argue that current AI-driven labs are only able to incorporate explicit knowledge
such as recorded data, scientific documentation, and publications and hence only see the “tip of the iceberg” of the
relevant knowledge Rihm et al. [2024].
                                                                However, even explicit knowledge such as that expressed
                                                                in scientific publications may hold problems of its own
                                                                when used as training data for scientific AI systems. Ge-
                                                                offrey Boulton and Moumita Koley observed in a contri-
                                                                bution for the International Science Council: “Many sci-
                                                                entific journals and papers maintain high standards, too
                                                                many lack proper editorial oversight, many lack rigour
                                                                and integrity, some engage in fraudulent practices, [...],
                                                                and agreed standards for overall governance of the pro-
                                                                cess are lacking.” International Science Council [2023].
                                                                All of these point might compromise training data quality.
                                                                This situation, together with the recent “publishing explo-
                                                                sion” (47% growth between 2016 and 2022 in the global
                                                                number of published papers Hanson et al. [2024]; nearly
                                                                250% increase in the number of Google Scholar hits for
                                                                the term “Brain-Computer Interface” in the same time
                                                                interval) which may reflect a overemphasis of quantity
                                                                compared to quality, may result in quality issues with
                                                                respect to the available training data for scientific AI sys-
                                                                tems. Such problems may be more or less serious in some
                                                                research fields compared to others Bakker et al. [2012].
                                                              Thus, in addition to general challenges such as those re-
                                                              lated to training data availability and quality, adaptation
                                                              of scientific AI to a specific research field may hold its
                                                              own challenges—not only due to differences in research
                                                              standards. AI systems may face vastly different require-
Figure 1: A conceptual illustration of AI-driven scientific ments depending, for example, on the role of experimenresearch processes for Brain-Computer Interfacing (BCI) tal work, the complexity of the relevant mathematical
and brain research, based on the concept as proposed by tools and concepts, or the degree of adoption of open
Lu et al. [2024]. In contrast to their concept, we consider science practices with resulting differences in public data
numerical results and data visualization as an integral part and scientific software access. For example, adoption
of the experimental iteration loop. Also in contrast to their of scientific AI agents in subfields of machine learning
fully autonomous approach, we support flexible levels of such as diffusion modeling, transformer-based language
shared autonomy for each phase of the cycle (color coded to modeling, and learning dynamics Lu et al. [2024] benefits
reflect the example BCI project as reported here; automatic from publicly accessible and well-documented datasets
paper write-up was not addressed)                             of, for many relevant questions, adequate size and quality,
                                                              as well as from well-established benchmarks. In contrast,
                                                              fields such as EEG-based BCI research face much larger
problems of small and diverse datasets of variable data quality. Additional domain-specific challenges arise due to the
complexities of the foundation of EEG signals in multiscale brain dynamics and their relation to human cognition,
emotion, and behavior Friston [2005].
Against this background, our objective in the present work is to adopt AI scientific agents to the field of human
brain research and Brain-Computer Interfacing (BCI) Figure 1. To address the general as well as the domain-specific

                                                            2

                                      Human-AI Teaming for BCI and Brain Research                                       P REPRINT

challenges of this task, we introduce a concept for human-AI workspaces designed to foster scientific human-AI
co-learning and collaboration, summarized in our Janusian Design Principles. As an instance of this design concept,
we present the LLM-based ChatBCI toolbox.
We show how ChatBCI enabled a human-AI team to perform a prototypical scientific BCI project based on a public
EEG dataset in a very time-efficient and productive manner, addressing the full cycle of project phases from data import,
exploration, and validation, over machine learning model selection and implementation (a deep convolutional neural
network as the LLM-generated brain signal decoder), setting up a training loop with integrated data augmentation, to
the joint interpretation of the obtained results. We illustrate how our structured interaction framework flexibly supports
different levels of agent autonomy and demonstrate how it allows transfer of expert knowledge about non-obvious,
non-trivial aspects of the EEG dataset used, from the human expert to the ChatBCI AI agent. This, in turn, proved
a valuable teaching and training partner for the human side. Thus, we achieved meaningful human-AI co-learning
together with substantially faster collaborative completion of our BCI research task than would have been possible
with a purely human approach. Our approach holds great potential for teaching and training in the field of human brain
research and BCI development. LLM-generated brain signal decoders also present a novel class of AutoML for brain
signal analysis. The “AI brain researcher” or “AI BCI researcher” presented here is an instance of an “AI worker”
in general. It will be interesting to study how abilities learned to handle the complexities of the human brain, and of
the signals that can be measured from it, may also be helpful for AI workers in other domains. We anticipate that
collaborative human-AI research and development in the field of brain research and BCI, as enabled by our design
concept and ChatBCI toolbox, will pave the way toward AI systems with a deep and holistic understanding of human
brain function - a "brain-grokking AI" - with a far-reaching application potential.

2       ChatBCI toolbox overview and Janusian Design Principles
                                                                    The ChatBCI toolbox2 is fully implemented in Python,
                                                                    deep learning functionality is relying on PyTorch; in the
                                                                    present work we use GPT-4o as underlying LLM OpenAI
                                                                    [2024]. It comprises the following main components:

                                                                           • A collection of public EEG data sets in ChatBCI
                                                                             compatible format, including documented data
                                                                             validation
                                                                           • EEG and BCI knowledge base
                                                                           • Core functionality: EEG preprocessing, analy-
                                                                             sis, decoding models, training, interpretability,
                                                                             and visualization
                                                                           • Auxiliary components and supporting infrastruc-
                                                                             ture, such as for generating summaries of data
                                                                             and code directory structures and contents at
                                                                             adjustable levels of granularity.
                                                                           • LLM communication tools.

Figure 2: In the Janusian Vision in designing human-AI              ChatBCI is by-design build to fostering human-AI colworkspaces, we embrace a dual-facing approach: one face             laboration (Figure 2). To this aim, we have defined a
directed toward empowering human expertise, and the                 set of design principles which are the foundation of our
other toward amplifying AI capabilities (DALL-E render-             general framework for human-AI teams. In the following
ing of this idea).                                                  we give a summary of our current working version of
                                                                    these principles. 3 .

    2
     The toolbox and corresponding detailed documentation will be made publicly available.
    3
     Named after Janus, two-faced Roman God of duality, to express the basic aspect of our design concept of looking both ways - to
the human side, and to the AI side. We see this duality not as a contradiction but as a synthesis

                                                                3

                                   Human-AI Teaming for BCI and Brain Research                                 P REPRINT

Working version of the seven Principles of Janusian Design
      1. Speaking the Same Language: Janusian workspaces foster the development of a joint collaboration language,
         minimizing misunderstanding and confusion. This enables AI systems to act as intuitive partners, aligning
         with human intentions and promoting seamless interactions. To achieve this, the workspace leverages intuitive
         interfaces, contextual feedback, and iterative refinement, ensuring that communication between humans and
         AI becomes progressively more natural and effective.
      2. Transparency and Trust: Trust requires mutual clarity and predictability. Explainability is a cornerstone,
         allowing users to progressively build confidence in AI systems by understanding their decision-making
         processes. The framework incorporates bidirectional transparency, encouraging not only explainable
         AI (XAI) methods, but also mechanisms for AI systems to ask clarifying questions when encountering
         ambiguous inputs or tasks. This fosters a collaborative dynamic where both human and AI agents seek to
         represent/understand each other’s intentions, enhancing mutual trust and reducing the likelihood of errors.
      3. Building a Shared Knowledge Base: The workspace amplifies human and AI collaboration by creating and
         maintaining a shared knowledge base. This repository encodes specialized expert knowledge and evolves
         iteratively. The Janusian Wiki (Jiki) serves as the foundation for this knowledge base, providing a structured,
         centralized system for documenting workflows, best practices, and domain-specific knowledge. By linking
         tasks, tools, and insights, the Jiki ensures that both human collaborators and AI systems have access to the
         collective intelligence of the workspace, facilitating informed decision-making and continuous improvement.
      4. Joint Integration of Priorities: The Janusian framework acknowledges that human-AI teams must balance
         and integrate priorities jointly, addressing trade-offs such as the need for immediate actions versus long-term
         objectives, or reconciling priorities driven by strategic considerations or overarching research questions
         ("top-down") with reactive, task-specific responses ("bottom-up").
      5. Adaptive Autonomy: AI systems adjust their level of autonomy based on task demands, enabling granular
         human supervision where needed while offering higher degrees of independence for routine or low-stakes tasks.
         For example, in the ChatBCI toolbox, the adaptive autonomy principle is exemplified through Collaborative
         Test-Driven Development (TDD), where the AI autonomously generates test cases and mock data for routine
         validations, while human oversight is applied to critical or ambiguous scenarios. This flexibility ensures that
         the level of autonomy dynamically aligns with the complexity and importance of the task.
      6. Accessibility from Novice to Expert: The workspace aims to balance accessibility for novice users, such as
         those in teaching and training scenarios, with maintaining advanced functionality tailored for expert users. It
         also differentiates how information from these scenarios is processed and utilized on the AI side, ensuring that
         insights from both novice interactions and expert workflows are effectively integrated. This inclusive design
         promotes efficiency and reduces the supervision workload, enabling seamless collaboration across varying
         levels of expertise.
      7. Continuous Evolution: A shared Janusian workspace is not static, but designed to foster mutual co-learning
         between humans and AI. This principle ensures that the ChatBCI toolbox or other workspaces based by-design
         on this concept not only supports effective and scalable data analysis but also fosters a dynamic workspace
         where both human and AI partners can co-evolve to achieve aligned goals.

In summary, the Janusian framework establishes a synergistic foundation for human-AI collaboration by aligning
intuitive interfaces, transparent processes, and adaptive autonomy with evolving user needs. In the following, we
illustrate how the ChatBCI toolbox based on these design ideas can be used in a concrete project example.

3   Analyzing the BCI competition IV2a dataset using ChatBCI
As illustrated in Figure 1, we propose a conceptual framework for AI-driven scientific research processes tailored to
Brain-Computer Interfacing (BCI) and brain research. This concept builds upon the approach introduced by Lu et al.
[2024], which emphasizes fully autonomous AI systems executing all phases of the research cycle. In contrast, our
approach highlights the importance of integrating numerical results and data visualization as essential elements within
the experimental iteration loop. Furthermore, instead of aiming for complete autonomy, we advocate for flexible levels
of shared autonomy, enabling human-AI collaboration to be tailored dynamically for each phase of the cycle. The
color-coded segments in Figure 1 correspond to the phases of an example BCI project reported here, with the exception
of automatic paper write-up, which was not addressed in this work.
In the following sections, we will structure the the main part of the manuscript along the schematic depicted in Figure 1,
detailing each phase of the research process and how human-AI collaboration is implemented to maximize efficiency,
adaptability, and the transfer of expertise.

                                                            4

                                    Human-AI Teaming for BCI and Brain Research                                    P REPRINT

3.1   Idea generation

The research ideas presented in Table 1 were generated using GPT-4o, as our underlying LLM. The model was provided
with a detailed prompt to explore advancements in EEG-based motor imagery classification within the context of
Brain-Computer Interfaces (BCIs) using the BCI Competition IV 2a dataset Brunner et al. [2008]. We decided to
use this specific dataset because it has been widely used, maybe the closest to a "MNIST of EEG", providing a rich
background of published results.
The prompt included instructions to identify key challenges, research gaps, motivations, and potential approaches
across a range of topics, including domain adaptation, lightweight architectures, temporal dynamics, and explainability.
GPT-4o synthesized the list of ideas by leveraging its training on a wide range of scientific and technical texts; notably
here we did not fine-tune on more specific literature. Overall, this demonstrates the utility of LLMs in assisting
researchers by rapidly generating diverse, structured, and actionable ideas that may have merit in guiding future
investigations. Notably, the generated research ideas included indeed notoriously under-researched yet important topics
such as the role of data preprocessing (c.f. example 10 in 1). On the other hand, an expert in the field in a single glance
can find that all generated research questions have already been addressed by the research community (Peterson et al.
[2019], Meng et al. [2023], Li et al. [2022], Jin et al. [2022], Kostas et al. [2021], Kasahara et al. [2015], Echtioui et al.
[2021], Saha and Baumert [2020], Ju et al. [2020], Feng et al. [2020], Kessler et al. [2024], Duan et al. [2020], Ferrez
and Millán [2008]). Therefore, the introduction of iterative refinement in the process of idea generation (e.g. like chain
of thoughts Lu et al. [2024]) and tools for novelty verification (through Semantic Scholar API) and quantification are
necessary. Clearly, the scientific ideas generation stage itself could also benefit from more sophisticated approaches.
For instance, leveraging creative templates grounded in evolutionary computation and open-endedness principles offers
significant potential. These approaches, as exemplified in works by Lu et al. [2024] and Faldor et al. [2024], use
iterative, mutation-driven frameworks to refine and evolve novel ideas, mimicking the exploratory processes of natural
evolution. Furthermore, combining knowledge graphs with LLM, as demonstrated by Gu and Krenn [2024], allows for
the synthesis of diverse interdisciplinary ideas. Another promising approach is curiosity-driven learning, as proposed by
Pathak et al. [2017], which emphasizes exploring the unknown and prioritizing novelty over immediate utility.
However, it was also observable that many of the proposed topics were under-specified and lacked exact knowledge
of what has already been done and reported in the relevant literature. For example, the suggestion was to explore
lightweight neural networks that achieve state-of-the-art accuracy while being optimized for low-power devices, which
is a promising topic, but one that is already well-trodden in the field. Studies like the widely known EEGNet paper
Lawhern et al. [2018] have specifically addressed this challenge by designing compact, efficient neural networks tailored
to EEG data. Therefore, while this idea aligns with key goals in the field, it lacks specificity in identifying novel
directions. For instance, future advancements might focus on extending lightweight models to handle more diverse
datasets, integrating adaptive techniques that dynamically scale complexity based on resource availability, or innovating
in model interpretability for constrained devices. Without domain-specific fine-tuning, the model had also difficulties in
retrieving the published SoA in decoding accuracies on the BCI Competition IV 2a dataset. While a re-implementation
of the FBCSP winning model of the original BCI Competition IV yielded 67.8% accuracy across the 4 motor imagery
classes, and convnets allowed for a gain of approx. 4% Schirrmeister et al. [2017]. In the meanwhile, accuracies close
to 100% have been reported (e.g., 97.61% in Xie and Oniga [2023]).
On this background, we decided for the following, human-specified topic for the first ChatBCI project: What do the
close to 100% decoding accuracies reported on the BCI Competition IV 2a dataset mean? To which extent do they
reflect sophistication of the brain signal decoding methodology, or possibly also over-fitting to this (relatively small)
dataset, and/or contributions from non-brain signals (artifacts which may contain class-specific information and might
be easier to decode than brain signals)? The BCI Competition IV 2a data set is particularly interesting in this context,
as it was part of the original competition instructions that since three EOG channels were provided, it was "required
to remove EOG artifacts before the subsequent data processing using artifact removal techniques such as highpass
filtering or linear regression" Brunner et al. [2008]. The winning entry of the competition and based on their approach,
also subsequent works on the same data set used a 4-Hz high-pass filter to address this requirement Ang et al. [2012]
Tangermann et al. [2012] Schirrmeister et al. [2017].
Thus, for our first ChatBCI project, we set two objectives:
       • Perform an exploratory data analysis of the BCI Competition IV 2a data set
       • Design, implement and test a convolutional neural network (CNN) decoder with a good tradeoff of simplicity
         and hence interpretability on the one side, and meaningful decoding results in terms of decoding accuracies on
         the other, including a corresponding within-subject training loop.
We addressed these objectives using the ChatBCI toolbox in a human-AI collaborative framework (Stage 2 "Experiment
Iteration" according to our generic research cycle plan 1).

                                                              5

                                    Human-AI Teaming for BCI and Brain Research                                  P REPRINT

Table 1: Generated research questions, gaps, motivations, and approaches for advancing EEG-based motor imagery
classification (10 consecutively generated examples).

 ID    Research Question             Gap                           Motivation                    Approach
 1     What are the optimal          Inconsistent findings on      Personalization can im-       Perform detailed fre-
       EEG frequency bands for       band contributions.           prove performance.            quency band analysis.
       decoding, and how do
       they vary across sub-
       jects?
 2     How can adversarial ro-       Limited research on ad-       Reliable BCIs are essen-      Simulate adversarial at-
       bustness techniques im-       versarial robustness in       tial for sensitive domains.   tacks and develop de-
       prove the reliability of      BCIs.                                                       fenses.
       EEG classifiers?
 3     Can graph-based repre-        EEG relationships are un-     Graphs can capture            Use GNNs and evaluate
       sentations of EEG signals     derutilized in flat feature   spatial-temporal relation-    performance.
       improve motor imagery         models.                       ships.
       classification?
 4     How can self-supervised       Labeled data is scarce        Self-supervised methods Apply techniques like
       learning reduce the need      and expensive.                can leverage large unla- contrastive learning and
       for labeled data in EEG                                     beled datasets.          test results.
       decoding?
 5     What role does individ-       EEG decoding often as-        Personalized BCIs can         Analyze variability and
       ual variability in brain      sumes uniformity across       improve accuracy.             develop normalization
       anatomy play in decod-        individuals.                                                strategies.
       ing motor imagery?
 6     How can dynamic ensem-        Static ensembles do not       Dynamic ensembles can         Develop adaptive ensem-
       ble methods improve the       adapt to data properties.     tailor predictions to the     ble methods and test per-
       robustness and accuracy                                     data.                         formance.
       of motor imagery classifi-
       cation?
 7     What is the impact of         Session variability af-       Reliable BCIs need to         Apply adaptation tech-
       session-to-session vari-      fects model performance.      function   consistently       niques and evaluate per-
       ability, and how can it be                                  over time.                    formance improvements.
       mitigated?
 8     Can federated learning        Most approaches require       Privacy-preserving train-     Implement    federated
       improve decoding while        centralized data.             ing can enable collabora-     learning  frameworks
       preserving privacy?                                         tive BCIs.                    and test cross-subject
                                                                                                 models.
 9     How can cross-frequency       CFC is underexplored in       CFC can reveal richer         Extract CFC features and
       coupling (CFC) features       motor imagery.                brain dynamics.               integrate into models.
       improve decoding accu-
       racy?
 10    What are the effects of       No consensus on the best      Standardization can im-       Compare pipelines and
       different EEG prepro-         preprocessing pipeline.       prove reproducibility.        evaluate their effects.
       cessing pipelines on de-
       coding performance?
 11    Can few-shot learning         Most models require sub-      Few-shot learning re-         Implement       few-shot
       enable accurate decod-        stantial data, impractical    duces the burden of data      methods like prototypical
       ing with minimal training     for new users.                collection.                   networks.
       data?
 12    How can real-time feed-       Feedback     is   mini-       Real-time       feedback      Simulate real-time feed-
       back loops improve mo-        mally studied in offline      could help users refine       back and test its impact
       tor imagery classifica-       datasets.                     mental strategies.            on decoding.
       tion during online experi-
       ments?
                                                            6

                                    Human-AI Teaming for BCI and Brain Research                                   P REPRINT

3.2     Experimental interation

Basic steps such as such data epoching, re-referencing, or filtering could be conveniently implemented, typically
with a few prompts per step. The key functionality of the ChatBCI toolbox enabling this smooth workflow is the
knowledge base with the relevant background knowledge and specific instructions, provided at different levels of detail
and aggregation, such that the relevant information can be efficiently kept in the LLM context.
More fine-grained instructions were (as expected) needed for data visualization. Figures 3, 4 (A and B) and 5 are
collaboratively ChatBCI-generated. Code generation and running the experiments were the two steps with the highest
level of AI autonomy (see Fig. 1)

3.2.1    Exploratory data analysis and data validation

Analysis for data exploration and validation let to several interesting observations. ChatBCI suggested initially three
starting steps for data validation and exploration. First, examine the basic signal statistics to understand the distribution
and variability of the EEG data across classes. This involves calculating the mean, standard deviation, and variance for
each channel and class, identifying systematic differences in signal variance, and detecting outliers, which may indicate
artefacts. Second, analyze event-related potentials (ERPs) to investigate time-locked neural activity by segmenting the
data around key events, calculating average ERP waveforms for each class, and identifying distinctive features such as
P300 peaks while ensuring the observed differences are neural rather than artefact-related. Third, explore class-specific
differences in the frequency domain by computing power spectral density (PSD) for each class and channel, identifying
frequency bands where power varies, and detecting potential artefacts such as line noise or muscle activity.

Figure 3: ERP waveforms across all trials of all subjects’ training data. Trial timing: The cue in the form of an arrow
pointing either to the left, right, down or up, corresponding to one of the four classes left hand, right hand, foot or
tongue) appeared and stayed on the screen for the duration indicated by the black box (1.25 s). Grey box: Time window
of fixation cross presentation.

The questions whether we can indeed expect a P300 as the dominating effect in the ERPs based on this dataset, and
what we can expect, revealed a mixture of accurate and wrong assumptions (e.g., correctly expecting visually and
auditory-evoked potentials (VEPs, AEPs) elecited by the respective cues, but also expecting a readiness potential which
in fact is associated with self-paced tasks Ball et al. [1999]), illustrating the necessity of expert knowledge transfer.
ERP results (EEG common average rereferenced, 40 Hz low-pass filtered) are shown in Fig. 3, for 5 EEG and 2 EOG
channels.

                                                             7

                                   Human-AI Teaming for BCI and Brain Research                                 P REPRINT

Next, we asked for an interpretation of this plot with the prompt "this figure shows plots of the ERPs (mean across
all trials of all subjects for each of the 4 classes). What do we see here, how can we interpret this result?" with the
response which we fully reproduce here (in blue font):

"The provided figure shows event-related potentials (ERPs) averaged across all trials and subjects for each of the 4
motor imagery classes (tongue, both feet, right hand, and left hand). Each subplot corresponds to a different electrode
or channel (e.g., EOG1, Fz, C3, Cz, C4, Pz), with voltage plotted as a function of time (0–4000 ms).

Key Observations
1. Cue-Evoked Potentials:

       • For channels like Fz, Cz, and Pz, there is a clear deflection around 200–300 ms after the cue onset, which
         could represent the early processing of the cue. This is consistent with components such as N1/P2 (cue-related
         processing).
       • These components are visible across all classes, indicating that these potentials are not class-specific but
         instead reflect the shared cognitive processing of the presented cue.

2. Motor-Related Activity:

       • Channels C3, Cz, and C4 are primarily associated with motor areas:
           – For C3 (left motor cortex), the right hand class shows a small deviation compared to other classes,
               consistent with contralateral activity.
           – For C4 (right motor cortex), the left hand class exhibits a subtle difference, though less pronounced.
       • Both feet and tongue motor imagery do not show distinct deflections in these motor channels, likely due to
         their bilateral or non-cortical representation.

3. Eye Movement Artefacts:

       • EOG1 and EOG3 channels clearly show larger deviations, especially for the tongue and both feet classes.
         This suggests that these classes may involve stronger or unintended eye movement artefacts, particularly in
         the early time window (0–1000 ms).
       • These artefacts could partially overlap with class-specific ERP components and may need to be addressed
         (e.g., through filtering or artifact rejection).

4. Overall Class Differences:

       • While class-specific differences are subtle, the right hand and left hand classes show slight distinctions in the
         C3 and C4 channels, corresponding to their expected motor areas.
       • The both feet and tongue classes show fewer clear class-specific ERP features, and their differences may
         instead emerge more clearly in the frequency domain (e.g., mu/beta desynchronization).

Interpretation
       • Cue-Related Potentials: These are robust across all classes, primarily seen in midline channels (e.g., Fz, Cz,
         Pz), reflecting the shared processing of visual stimuli.
       • Motor-Specific Activity: Subtle differences in the motor-related channels (C3, C4) align with the expected
         contralateral activation for hand motor imagery tasks.
       • Artefact Contribution: Large deviations in EOG channels suggest that some ERPs might be influenced by
         eye movement artefacts, particularly for classes like tongue and both feet."

This interpretation addresses many of the relevant aspects and in our opinion demonstrates a very promising starting
point for a useful visual understanding of results in the EEG and BCI research context of. The three major signal
sources that need to be considered (cue-evoked, movement-related, and ocular artifacts) are correctly identified and
discussed, with several valid points such as that class differences can be expected to be clearer in the frequency domain.
However, the conclusion that ocular effects mainly show in the tongue and feet movement classes is not likely given the

                                                            8

                                      Human-AI Teaming for BCI and Brain Research                                         P REPRINT

Figure 4: (A) Zoom-in from Fig. 3. (B) Same with 4-Hz high-pass filter. (C) Direction of the arrows used as cues for
the 4 classes and colored corresponding to (A) and (B); placement of the EOG1 and EOG3 channels as described in
Brunner et al. [2008].

observations that can here be made. The direction of the arrows used as cues 4 together with the placement of the EOG
channels (see Fig, 4 (C)) suggest that subjects reacted to visual cue presentation with a blink-saccade towards the tip
of the different presented arrows; see also Fig. 4 (A). The fast transients at the onset of the presumed blink-saccade
movement survived a 4-Hz high-pass filter (Fig. 4 (B)).
In summary, already in its current early stage, ChatBCI proved highly useful for a practical example of a EEG data
exploration, allowing for a substantially accelerated workflow. Analyzing a widely used motor imagery data set turned
out to be a rich "learning opportunity" for ChatBCI (exemplarily showing that cues may induce class-specific effects,
how to detect differential eye movements, etc.). At the current stage, our solution to make such learning experiences
persistent is to include corresponding text documents in the ChatBCI knowledge base. In future versions, this approach
may be supplemented by fine-tuning the underlying language models, and by equipping models with memory Wolters
et al. [2024] Packer et al. [2024].
One next step from here would be to check the published BCI literature to which extend our observations have already
been reported, and to this end, implementing abilities for automated literature search and aggregation in ChatBCI. The
results so far also support our initial choice of research questions and objectives (see Section 3.1). In the following, we
briefly describe the first step with respect to Objective 2, design, implement and test a CNN EEG decoder.

3.2.2   LLM-generated deep neural network decoder and training loop

Not surprisingly, given the well-documented abilities of LLMs in code generation (Rozière et al. [2024]), ChatBCI was
able to design and implement both deep networks for EEG decoding and working training loops, including plausibly
motivated data-augmentation strategies. Figure 5 shows results obtained with a simple network consisting of a sequence
of spatial and temporal convolutions, using batch normalization, dropout, and a SWISH activation function, with
within-subject training using the original competition data split.
Results showed clear learning on the validation set across all subjects, reaching accuracies, on the one hand, clearly
above chance, and on the other hand, also substantially below previous reports Zhang et al. [2021]. We consider our
simple models as a good starting point for, in a next step, implementing interpretability tools, and gradually moving
to more complex decoding models. Setting up the network as well as the training loop used in this example, in total
required less than 10 prompts.

    4
      Placement and coloring of the arrows in 4 serves for illustration, not implying a similar placement or coloring in the original
study, where arrows were, based on the available information, all paced centrally and in the same color, but pointing to different
directions as show Brunner et al. [2008]

                                                                 9

                                    Human-AI Teaming for BCI and Brain Research                                 P REPRINT

Another next logical step would be the implementation of hyperparameter optimization and neural architecture search
functionality in ChatBCI, for advancing to models with SoA accuracy or beyond. Note that this would potentially enable
novel approaches for AutoML leveraging LLMs, without the necessity for expert knowledge of underlying AutoML
libraries and toolboxes. We foresee that ChatBCI would also be particularly useful in BCI decoding benchmarking
tasks, which require structured large-scale approaches which may especially align with the specific strengths of artificial
compared to human intelligence.

Figure 5: (A) Zoom-in from Fig. 3. (B) Same with 4-Hz high-pass filter. (C) Direction of the arrows used as cues for
the 4 classes and colored corresponding to (A) and (B); placement of the EOG1 and EOG3 channels.

4   Conclusions
This paper introduced the ChatBCI toolbox as a practical and innovative solution to foster human-AI collaboration in
the fields of Brain-Computer Interface (BCI) research and brain signal analysis. By adopting our Janusian Design
Principles, we demonstrated how human-AI teaming can address challenges in BCI development, such as small
datasets, complex signal dynamics, and knowledge transfer. Specifically:

      1. Collaborative Human-AI Workspaces: The ChatBCI framework was built to integrate the strengths of both
         human expertise and AI capabilities, providing an adaptable, interactive workspace that supports all phases of
         scientific research, from idea generation to experimental analysis and result interpretation.
      2. Application to Motor Imagery Decoding: Using the ChatBCI toolbox, we explored the BCI Competition IV
         2a dataset and implemented a deep learning decoder in a collaborative, time-efficient manner. This process
         highlighted the utility of ChatBCI for accelerating workflows, identifying domain-specific challenges, and
         fostering human-AI co-learning.
      3. Transfer of Expertise and Knowledge: The structured interaction framework enabled effective transfer of
         non-obvious, domain-specific knowledge from human experts to the AI, ensuring meaningful co-learning. The
         AI, in turn, proved to be a valuable partner, suggesting analyses, generating code, and adapting to user inputs.
      4. Scalable Design for Broader Neurotechnological Applications: While demonstrated for a specific EEG
         dataset, the principles and methods behind ChatBCI are extensible to a wide range of neuroscience and
         neurotechnology challenges, laying a foundation for future advancements in brain research and AI-driven
         discovery.

By merging the adaptability of AI systems with the nuanced understanding of human researchers, ChatBCI represents a
step forward in achieving efficient and collaborative research processes. Our findings indicate that human-AI teaming, as
enabled by ChatBCI, holds significant promise for accelerating scientific discovery while maintaining the interpretability
and trust essential for impactful advancements in neuroscience and BCI.
We envision a future where in some scenarios brain-computer interfaces (BCIs) transcend traditional keyboard inputs,
replacing them with direct interaction through physiological signals—a transformative goal of BCI research and
development. For instance, advances like Meta’s silent speech decoding demonstrate the potential of such technologies
to enable seamless communication between humans and machines Défossez et al. [2023]. This innovation aligns
perfectly with the evolving paradigm of human-AI collaboration, where neural interfacing could ensure seamless

                                                            10

                                   Human-AI Teaming for BCI and Brain Research                               P REPRINT

interaction and integration of human-AI teams. By integrating robust privacy and security measures (Kapitonova et al.
[2022]), such systems could enable richer societal interactions and more immersive experiences in scientific discovery,
healthcare, and beyond. The convergence of neurotechnology and AI promises not only to amplify human cognitive
capabilities but also to unlock new paradigms for how humans and machines collaborate to solve complex problems.
Through the type of collaborative teamwork on brain data enabled by ChatBCI, AI systems may progressively develop
a deep and holistic understanding of human brain function. By engaging with the complexities of brain signals,
their multiscale dynamics, and their connections to cognition, behavior, and emotion, such AI systems may transcend
conventional analytical approaches. This emerging paradigm has potential not only in neuroscience and neurotechnology
but also fields such as medicine, psychology, and education. Furthermore, a "brain-grokking AI" could unlock significant
performance advantages in areas that rely on human brain function, including optimized mental health interventions
and tailored cognitive training, underscoring the importance of human-AI collaboration as a cornerstone for advancing
knowledge and unlocking new possibilities in science and technology.

References
Kai Keng Ang, Zhi Yang Chin, Chuanchu Wang, Cuntai Guan, and Haihong Zhang. Filter bank common spatial pattern
  algorithm on bci competition iv datasets 2a and 2b. Frontiers in Neuroscience, 6:39, 2012.
Marjan Bakker, Annette Van Dijk, and Jelte M. Wicherts. The Rules of the Game Called Psychological Sci-
 ence. Perspectives on Psychological Science, 7(6):543–554, November 2012. ISSN 1745-6916, 1745-6924.
 doi:10.1177/1745691612459060. URL https://journals.sagepub.com/doi/10.1177/1745691612459060.
Tonio Ball, Andreas Schreiber, Bertram Feige, Martin Wagner, Carl H. Lücking, and Ralitza Kristeva-Feige. The role
  of higher-order motor areas in voluntary movement as revealed by high-resolution eeg and fmri. Neuroimage, 10(6):
  682–694, 1999.
Clemens Brunner, Robert Leeb, Gernot Müller-Putz, Alois Schlögl, and Gert Pfurtscheller. Bci competition iv dataset 2a:
  A motor imagery dataset for brain-computer interface research. Institute for Knowledge Discovery, Graz University
  of Technology, 2008. URL https://www.bbci.de/competition/iv/. BCI Competition 2008 – Graz Data Set
  A.
Lili Duan, Jie Li, Hongfei Ji, Zilong Pang, Xuanci Zheng, Rongrong Lu, Maozhen Li, and Jie Zhuang. Zero-
  Shot Learning for EEG Classification in Motor Imagery-Based BCI System. IEEE Transactions on Neural
  Systems and Rehabilitation Engineering, 28(11):2411–2419, November 2020. ISSN 1534-4320, 1558-0210.
  doi:10.1109/TNSRE.2020.3027004. URL https://ieeexplore.ieee.org/document/9207741/.
Alexandre Défossez, Charlotte Caucheteux, Jérémy Rapin, Ori Kabeli, and Jean-Rémi King. Decoding speech
  perception from non-invasive brain recordings. Nature Machine Intelligence, 5(10):1097–1107, October 2023. ISSN
  2522-5839. doi:10.1038/s42256-023-00714-5. URL http://dx.doi.org/10.1038/s42256-023-00714-5.
Amira Echtioui, Wassim Zouch, Mohamed Ghorbel, Chokri Mhiri, and Habib Hamam. A Novel Ensemble Learning
 Approach for Classification of EEG Motor Imagery Signals. In 2021 International Wireless Communications and
 Mobile Computing (IWCMC), pages 1648–1653, Harbin City, China, June 2021. IEEE. ISBN 978-1-72818-616-0.
 doi:10.1109/IWCMC51323.2021.9498833. URL https://ieeexplore.ieee.org/document/9498833/.
Maxence Faldor, Jenny Zhang, Antoine Cully, and Jeff Clune. Omni-epic: Open-endedness via models of human notions
 of interestingness with environments programmed in code, 2024. URL https://arxiv.org/abs/2405.15568.
Naishi Feng, Fo Hu, Hong Wang, and Mohamed Amin Gouda. Decoding of voluntary and involuntary upper-limb
  motor imagery based on graph fourier transform and cross-frequency coupling coefficients. Journal of Neural
  Engineering, 17(5):056043, October 2020. ISSN 1741-2560, 1741-2552. doi:10.1088/1741-2552/abc024. URL
  https://iopscience.iop.org/article/10.1088/1741-2552/abc024.
Pierre W. Ferrez and José del R. Millán. Simultaneous Real-Time Detection of Motor Imagery and Error-Related
  Potentials for Improved BCI Accuracy. 2008. URL https://api.semanticscholar.org/CorpusID:11621347.
Karl Friston. A theory of cortical responses. Philosophical Transactions of the Royal Society B: Biological Sciences,
  360(1456):815–836, April 2005. ISSN 0962-8436, 1471-2970. doi:10.1098/rstb.2005.1622. URL https://
  royalsocietypublishing.org/doi/10.1098/rstb.2005.1622.
Xuemei Gu and Mario Krenn. Interesting scientific idea generation using knowledge graphs and llms: Evaluations with
 100 research group leaders, 2024. URL https://arxiv.org/abs/2405.17044.
Mark A. Hanson, Pablo Gómez Barreiro, Paolo Crosetto, and Dan Brockington. The strain on scientific publishing.
 Quantitative Science Studies, 5(4):823–843, November 2024. ISSN 2641-3337. doi:10.1162/qss_a_00327. URL
 https://direct.mit.edu/qss/article/5/4/823/124269/The-strain-on-scientific-publishing.

                                                          11

                                  Human-AI Teaming for BCI and Brain Research                              P REPRINT

International Science Council. More is not better: The developing crisis of scientific publishing. https://council.
   science/blog/more-is-not-better-the-developing-crisis-of-scientific-publishing/,                           2023.
  Accessed: 2024-12-29.
Jing Jin, Hao Sun, Ian Daly, Shurui Li, Chang Liu, Xingyu Wang, and Andrzej Cichocki. A Novel Classification
   Framework Using the Graph Representations of Electroencephalogram for Motor Imagery Based Brain-Computer
   Interface. IEEE Transactions on Neural Systems and Rehabilitation Engineering, 30:20–29, 2022. ISSN 1534-4320,
  1558-0210. doi:10.1109/TNSRE.2021.3139095. URL https://ieeexplore.ieee.org/document/9664515/.
Ce Ju, Dashan Gao, Ravikiran Mane, Ben Tan, Yang Liu, and Cuntai Guan. Federated Transfer Learning for EEG
  Signal Classification. In 2020 42nd Annual International Conference of the IEEE Engineering in Medicine &
  Biology Society (EMBC), pages 3040–3045, Montreal, QC, Canada, July 2020. IEEE. ISBN 978-1-72811-990-8.
  doi:10.1109/EMBC44109.2020.9175344. URL https://ieeexplore.ieee.org/document/9175344/.
Maryna Kapitonova, Philipp Kellmeyer, Simon Vogt, and Tonio Ball. A framework for preserving privacy and
 cybersecurity in brain-computer interfacing applications, 2022. URL https://arxiv.org/abs/2209.09653.
Kazumi Kasahara, Charles Sayo DaSalla, Manabu Honda, and Takashi Hanakawa.            Neuroanatomical
  correlates of brain–computer interface performance.    NeuroImage, 110:95–100, April 2015.    ISSN
 10538119. doi:10.1016/j.neuroimage.2015.01.055. URL https://linkinghub.elsevier.com/retrieve/pii/
  S1053811915000816.
Roman Kessler, Alexander Enge, and Michael A. Skeide. How EEG preprocessing shapes decoding performance,
  December 2024. URL http://arxiv.org/abs/2410.14453. arXiv:2410.14453 [q-bio].
Demetres Kostas, Stéphane Aroca-Ouellette, and Frank Rudzicz. BENDR: Using Transformers and a Contrastive
  Self-Supervised Learning Task to Learn From Massive Amounts of EEG Data. Frontiers in Human Neuroscience,
 15:653659, June 2021. ISSN 1662-5161. doi:10.3389/fnhum.2021.653659. URL https://www.frontiersin.
  org/articles/10.3389/fnhum.2021.653659/full.
Vernon J Lawhern, Andrew J Solon, Nicholas R Waytowich, Stephen M Gordon, Chou P Hung, and Brent J Lance.
  Eegnet: a compact convolutional neural network for eeg-based brain–computer interfaces. Journal of neural
  engineering, 15(5):056013, 2018.
Yunhuan Li, Xi Yu, Shujian Yu, and Badong Chen. Adversarial Training for the Adversarial Robustness of
  EEG-Based Brain-Computer Interfaces. In 2022 IEEE 32nd International Workshop on Machine Learning
  for Signal Processing (MLSP), pages 1–6, Xi’an, China, August 2022. IEEE. ISBN 978-1-66548-547-0.
  doi:10.1109/MLSP55214.2022.9943479. URL https://ieeexplore.ieee.org/document/9943479/.
Chen Lu, Chun Lu, Rebecca T. Lange, Jakob Foerster, Jeff Clune, and David Ha. The ai scientist: Towards fully
  automated open-ended scientific discovery. arXiv preprint arXiv:2408.06292, 2024.
Lubin Meng, Xue Jiang, and Dongrui Wu. Adversarial robustness benchmark for EEG-based brain–computer interfaces.
  Future Generation Computer Systems, 143:231–247, June 2023. ISSN 0167739X. doi:10.1016/j.future.2023.01.028.
  URL https://linkinghub.elsevier.com/retrieve/pii/S0167739X23000353.
OpenAI. Gpt-4o model (december 30 version). https://openai.com/, 2024.
Charles Packer, Sarah Wooders, Kevin Lin, Vivian Fang, Shishir G. Patil, Ion Stoica, and Joseph E. Gonzalez. Memgpt:
  Towards llms as operating systems, 2024. URL https://arxiv.org/abs/2310.08560.
Deepak Pathak, Pulkit Agrawal, Alexei A. Efros, and Trevor Darrell. Curiosity-driven exploration by self-supervised
  prediction, 2017. URL https://arxiv.org/abs/1705.05363.
Victoria Peterson, Dominik Wyser, Olivier Lambercy, Ruben Spies, and Roger Gassert. A penalized time-frequency band
  feature selection and classification procedure for improved motor intention decoding in multichannel EEG. Journal
  of Neural Engineering, 16(1):016019, February 2019. ISSN 1741-2560, 1741-2552. doi:10.1088/1741-2552/aaf046.
  URL https://iopscience.iop.org/article/10.1088/1741-2552/aaf046.
Jacob T. Rapp, Bennett J. Bremer, and Philip A. Romero. Self-driving laboratories to autonomously navigate the protein
  fitness landscape. Nature Chemical Engineering, 1(1):97–107, January 2024. ISSN 2948-1198. doi:10.1038/s44286-
  023-00002-4. URL https://www.nature.com/articles/s44286-023-00002-4.
Simon D. Rihm, Jiaru Bai, Aleksandar Kondinski, Sebastian Mosbach, Jethro Akroyd, and Markus Kraft.
  Transforming research laboratories with connected digital twins. Nexus, 1(1):100004, March 2024. ISSN
  29501601. doi:10.1016/j.ynexs.2024.100004. URL https://linkinghub.elsevier.com/retrieve/pii/
  S2950160124000020.
Baptiste Rozière, Jonas Gehring, Fabian Gloeckle, Sten Sootla, Itai Gat, Xiaoqing Ellen Tan, Yossi Adi, Jingyu Liu,
  Romain Sauvestre, Tal Remez, Jérémy Rapin, Artyom Kozhevnikov, Ivan Evtimov, Joanna Bitton, Manish Bhatt,

                                                         12

                                   Human-AI Teaming for BCI and Brain Research                                  P REPRINT

  Cristian Canton Ferrer, Aaron Grattafiori, Wenhan Xiong, Alexandre Défossez, Jade Copet, Faisal Azhar, Hugo
  Touvron, Louis Martin, Nicolas Usunier, Thomas Scialom, and Gabriel Synnaeve. Code llama: Open foundation
  models for code, 2024. URL https://arxiv.org/abs/2308.12950.
Simanto Saha and Mathias Baumert. Intra- and Inter-subject Variability in EEG-Based Sensorimotor Brain Com-
  puter Interface: A Review. Frontiers in Computational Neuroscience, 13:87, January 2020. ISSN 1662-
  5188. doi:10.3389/fncom.2019.00087. URL https://www.frontiersin.org/article/10.3389/fncom.2019.
  00087/full.
Robin Tibor Schirrmeister, Jost Tobias Springenberg, Lukas Dominik Josef Fiederer, Martin Glasstetter, Katharina
  Eggensperger, Michael Tangermann, Frank Hutter, Wolfram Burgard, and Tonio Ball. Deep learning with convolu-
  tional neural networks for eeg decoding and visualization. Human Brain Mapping, 38(11):5391–5420, 2017.
Kyle Swanson, Wesley Wu, Nash L. Bulaong, John E. Pak, and James Zou. The Virtual Lab: AI Agents Design New
  SARS-CoV-2 Nanobodies with Experimental Validation, November 2024. URL http://biorxiv.org/lookup/
  doi/10.1101/2024.11.11.623004.
Michael Tangermann, Klaus-Robert Müller, Ad Aertsen, Niels Birbaumer, Christoph Braun, Clemens Brunner, and
  Benjamin Blankertz. Review of the bci competition iv. Frontiers in Neuroscience, 6:55, 2012.
H. Wang, T. Fu, Y. Du, W. Gao, K. Huang, Z. Liu, and M. Zitnik. Scientific discovery in the age of artificial intelligence.
  Nature, 620(7972):47–60, 2023.
C. Wolters, X. Yang, U. Schlichtmann, and T. Suzumura. Memory is all you need: An overview of compute-in-memory
  architectures for accelerating large language model inference. arXiv preprint arXiv:2406.08413, 2024.
Yuchen Xie and Stefan Oniga. Classification of motor imagery eeg signals based on data augmentation and convolutional
  neural networks. Sensors, 23(4):1932, 2023.
Ce Zhang, Young-Keun Kim, and Azim Eskandarian. EEG-inception: an accurate and robust end-to-end neural network
  for EEG-based motor imagery classification. Journal of Neural Engineering, 18(4):046014, August 2021. ISSN
  1741-2560, 1741-2552. doi:10.1088/1741-2552/abed81. URL https://iopscience.iop.org/article/10.
  1088/1741-2552/abed81.

                                                           13
