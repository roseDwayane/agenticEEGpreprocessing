---
citation_key: "Kannan2023"
title: "Can LLMs Configure Software Tools"
authors: "Jai Kannan"
year: 2023
doi: "10.48550/arxiv.2312.06121"
source: "arXiv (2312.06121)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "2312.06121"
conversion: "pdftotext -layout (automated)"
---

# Can LLMs Configure Software Tools

Using Language Models for Software Tool
                                                                  Configuration
                                                                                                      Jai Kannan
                                                                                        Applied Artificial Intelligence Institute
                                                                                                   Deakin University
                                                                                                  Burwood, Australia
                                                                                              jai.kannan@deakin.edu.au

                                            Abstract—In software engineering, the meticulous configura-        impairs the scalability and reproducibility of the process[12,

arXiv:2312.06121v1 [cs.SE] 11 Dec 2023
                                         tion of software tools is crucial in ensuring optimal performance     13, 14]. Furthermore, the high dimensionality and complexity
                                         within intricate systems. However, the complexity inherent in         introduces a challenge where a minor oversight has significant
                                         selecting optimal configurations is exacerbated by the high-
                                         dimensional search spaces presented in modern applications.           implications on software performance.
                                         Conventional trial-and-error or intuition-driven methods are             The problem of high-dimensional search space for config-
                                         both inefficient and error-prone, impeding scalability and re-        urations increases as intelligent applications with learning-
                                         producibility. In this study, we embark on an exploration of          enabled components have become ubiquitous [15]. Learning-
                                         leveraging Large-Language Models (LLMs) to streamline the             enabled components (LECs) are machine learning components
                                         software configuration process. We identify that the task of
                                         hyperparameter configuration for machine learning components          whose behaviour is derived from training data [16] which is
                                         within intelligent applications is particularly challenging due to    integrated into larger systems containing traditional compu-
                                         the extensive search space and performance-critical nature. Ex-       tational entities such as web services and operator interfaces.
                                         isting methods, including Bayesian optimization, have limitations     Despite their widespread use LEC’s fail to perform as expected
                                         regarding initial setup, computational cost, and convergence effi-    [17, 15, 18] which reduces the performance and utility of
                                         ciency. Our work presents a novel approach that employs LLMs,
                                         such as Chat-GPT, to identify starting conditions and narrow          the system. For instance, a change in a system’s operating
                                         down the search space, improving configuration efficiency. We         environment can introduce drifts in the input data for a
                                         conducted a series of experiments to investigate the variability of   machine learning (ML) component making them underperform
                                         LLM-generated responses, uncovering intriguing findings such          [17].
                                         as potential response caching and consistent behavior based              The optimal performance of an ML component depends on
                                         on domain-specific keywords. Furthermore, our results from
                                         hyperparameter optimization experiments reveal the potential          how its hyperparameters are configured during the training
                                         of LLMs in expediting initialization processes and optimizing         process [19, 20, 21]. This issue becomes particularly pro-
                                         configurations. While our initial insights are promising, they        nounced when dealing with extensive search spaces. Sub-
                                         also indicate the need for further in-depth investigations and        optimal parameter settings can lead to noticeable degradation
                                         experiments in this domain.                                           in performance. To address this challenge, a common strategy
                                            Index Terms—Language models, Software tool configuration
                                                                                                               involves employing Bayesian optimization to systematically
                                                                                                               explore the search space and identify suitable hyperparameters
                                                                I. I NTRODUCTION
                                                                                                               for retraining or fine-tuning ML components when drifts occur
                                            In software engineering, the task of configuring software          [16].
                                         tools is critical in ensuring optimal performance and function-          The fundamental concept behind Bayesian optimization is to
                                         ality of the systems they underpin [1, 2]. The configuration          utilize a Gaussian process [22] to model the intricate relation-
                                         process involves the selection and adjustment of parameters           ship between hyperparameter configurations and performance
                                         and options specific to each usecase [3, 4]. For example, Think       metrics, like validation loss. This model-driven process guides
                                         about setting up a tool that checks Python code in a smart            the identification of configurations within the specified search
                                         app combining software engineering and machine learning.              space. However, this approach comes with several limitations.
                                         To make it work well, you need to configure it properly to            First, i) the process necessitates configuring the starting
                                         handle both types of code, so it gives better results and smarter     point or initial conditions for the search space before im-
                                         insights [5]. However, selecting an optimal configuration is          plementing Bayesian optimization. Typically, this is achieved
                                         complex which is resource and compute-intensive, requiring            through intuition or referencing academic literature, often
                                         several iterations.                                                   leading to sub-optimal setups. Secondly, ii) if the search
                                            This complexity is primarily due to the high-dimensional           space is expansive, the Bayesian optimization process
                                         search space [2, 6]. Fully exploring all options is unrealistic       becomes computationally demanding. Thirdly, iii) given a
                                         [7, 8], leading engineers to often resort to trial and error or       limited budget, the approach may only explore a small
                                         intuition [9, 10]. However, this method is inefficient and error-     fraction of the vast search space, struggling to converge to
                                         prone as it heavily relies on human judgement [9, 11] and             the optimal configurations, ultimately resulting in resource

wastage [23, 24, 25] and poor performance.                          assess the feasibility of utilising Language Model Models
   In light of these challenges, we propose that Large-             (LLMs) for automated software component configuration, fo-
Language Models (LLMs) like Chat-GPT, trained on a diverse          cusing on the optimisation of machine learning models to enarray of internet data encompassing machine learning repos-         able intelligent applications. Specifically, we explore whether
itories and Python notebooks, can expedite the identification       LLMs can identify optimal starting points for hyperparameter
of starting conditions and narrow down the search space for         optimisation for an usecase, e.g. I want to deploy a model on
optimal configurations, provided the relevant context. This         a drone to detect animals in a 30-acre farm. The objective
hypothesis is motivated by the need to address the limitations      is to reduce energy consumption and the number of iterations
of existing methods and harness the potential of LLMs to            required for fine-tuning a model while adhering to a fixed
streamline the configuration process.                               budget for a particular usecase.
   The expected benefits of exploring LLM driven automated          C. Developing Research Questions to Address the Goal:
configurations are i) reducing the cost of developing intelligent
applications, ii) faster development cycles and iii) improves the     The research goal was broken into the following research
quality of LEC’s. Ultimately, exploring this research will allow    questions.
us to enhance the state of software engineering practices.            1) RQ1: What is variability within a specific usecase?
   Our study makes a significant contribution by conducting an            This research question examines potential variations and
initial exploration into the variability of responses generated           changes in the distribution of suggested hyperparameters
by ChatGPT-4 across distinct use cases. While our findings                for individual use cases.
offer compelling insights, they also underscore the need for              To measure this question, we use statistical measures of
more comprehensive investigations. We identify interesting                standard deviation, variance and interquartile ranges to
results, including the possibility of response caching within             calculate the variability.
the Generative Pre-trained Transformer (GPT) models, as well              The investigation allows us to study the performance
as the diversity observed in responses from RQ2, suggesting               diversity of a single usecase.
consistent and predictable behaviour based on domain-specific         2) RQ2: How does the variability differ across multiple
keywords. Moreover, our findings from RQ3 indicate the po-                usecases? This research question explores how varitential of Large-Language Models to expedite hyperparameter               ability in LLM-generated hyperparameters differs across
initialization and optimization processes. These initial findings         diverse use cases.
not only provide a foundation for further research but also               To measure this research question, we used statistical
demonstrate the potential of exploring the use of Language                analysis using ANOVA’s F-statistic measure to compare
Models in software engineering practices.                                 the generated hyperparameter across diverse usecases.
                                                                          This investigation helps us understand the adaptability
                         II. M ETHOD                                      and flexibility of LLMs in meeting distinct requirements
A. Hypothesis:                                                            within each application domain.
   Using Large language models (LLMs) for hyperparameter              3) RQ3: How does the performance of LLM-configured
configuration is a superior technique compared to traditional             software tools compare with state-of-the-art methods?
methods. LLMs have the advantage of understanding context                 This research question assesses the competitive advanand generating specific configurations that fit the problem. As           tage of LLMs in configuring software tools, compar-
LLMs have been trained on internet data containing Python                 ing their performance against State-Of-The-Art (SOTA)
notebooks which often contain the results of the experiments              methods, such as Bayesian optimization.
within them. LLMs have the edge in mimicking human                        To measure this research question, we calculate the perdecision-making processes by leveraging their training data               formance of the tuned models using the two approaches
to effectively mimic the decisions of domain experts. We                  i.e. LLM-based and Bayesian optimisation on a dataset,
believe we can utilise this feature of the LLMs to bootstrap              and calculate the accuracy can compare the validation
the configurations for software tools.                                    losses for each approach.
   This section outlines our method for examining the potential           This analysis enables us to identify whether LLMs can
of Large language Models (LLMs) for configuring software                  accurately predict discrete values.
components. We employed the Goal-Question-Metric (GQM)                                   III. E XPERIMENT S ETUP
framework (Fenton et al., 2014) to define our objective,               To evaluate the research questions, selecting an LLM beformulate research questions, and quantify the outcomes of          comes crucial. LLMs represent a significant advancement in
these research questions, facilitating the measurement of goal      artificial intelligence, comprehending and generating humanachievement.”                                                       like text from the input. Their applications span across diverse
B. Defining the Research Goal:                                      domains such as natural language processing, content genera-
                                                                    tion, and text summarizations. Several LLM options available
   The overarching goal of this study is to determine the
                                                                    for consideration such as LLaMA, LLaMA21 from Meta,
feasibility of utilising Language Model Models (LLMs) to
configure software components automatically. Our aim is to            1 https://about.fb.com/news/2023/07/llama-2

BLOOM2 from Hugging Face, and ChatGPT 3 from Open-                     •  User prompt: The user prompt is the input provided
AI. For our experiment, we chose to use ChatGPT-4. Unlike                 to continue the conversation after the system prompt to
other models, Chat is specifically tailored for conversational            query the model for information.
interaction, allowing it to produce coherent and contextually           These prompts work together to create a relevant conversarelevant responses. ChatGPT is trained on a diverse range of         tion with the model. The system prompt provides the initial
internet data to generate content for various subjects. This         context, and the user prompts guide the ongoing conversations.
specialisation is crucial in applications requiring interactive      An example of the prompts is presented in Figure 1 Using
dialogue. In this experiment, we use this feature to generate
the usecases for answering the research questions.
   For the experiments, we chose the domain of computer
vision. The popularity of computer vision has seen many realworld applications and opportunities. we chose two usecases
in this domain for our experiments, which are described in the
following section. Each usecase is chosen due to their tradeoffs
while choosing and selecting the Machine learning technique
to apply in terms of resources and performance.

A. Usecase 1:
   Real-time image classification for security cameras: In           Fig. 1. Example demonstrating system and user prompts to set the role of
security applications, real-time image classification is essential   the Chat-GPT model
for the identification of potential threats. The model should
provide rapid predictions for quick decision-making. The             a combination of user and system prompts, we can guide
model should be compute efficient to execute in a resource           the GPT-4 model to provide responses that align with the
constraint environment to ensure real-time processing without        intended context and purpose of the conversation. The system
compromising image quality.                                          prompt serves as a crucial foundation, outlining the overall
                                                                     direction and scope of the conversation. It provides the initial
B. Usecase 2:                                                        context, ensuring that the model understands the topic, tone,
                                                                     and objectives of the interaction.
   Understanding financial market structures for invest-                Once the context is set by the system prompt, the user
ments: In the case of financial markets, unlike usecase 1, the       prompts come into play to lead the conversation forward.
application is focused on improving the accuracy of diagnos-         These prompts act as queries or instructions that steer the
ing markets using financial news articles to make informed           discussion in specific directions. By carefully constructing user
investment decisions.                                                prompts, we can extract the desired information, insights, or
   Comparing these scenarios underscores their distinct nature.      responses from the model.
The security use case prioritises speed, even at a minor
expense in accuracy, while the usecase of understanding the          D. Prompting strategies:
financial markets demands precision, allowing for slightly              For our experiments, we utilised the combination of 2 difextended inference times to ensure accurate assessments.             ferent strategies to develop the prompts for each usecase. We
These use cases exemplify the variability in hyperparameter          utilised the two different strategies as described in [26] which
configuration driven by the specific requirements of each            are 1.Instruction-Following and 2. Imitation . The combinasituation along with the model used.                                 tion of the two strategies allows the crafting of prompts that
                                                                     elicit meaningful and contextually relevant responses from the
C. Prompt design for usecases:                                       Chat-GPT model.
   To interact with Chat-GPT, we utilise a prompt. A prompt is          1) Instruction-Following: Instruction following described
a specific input query that is provided to the model to receive             by [26] is a strategy where prompts are constructed as
a response. It is used to initiate a conversation to request                clear and explicit instructions that guide the model’s
information from the model. Chat-GPT utilises two types of                  response. These instructions lay out the desired format,
prompts i.e. i)System prompt and ii) User prompt, which are                 content, or steps the model should follow when generatdescribed as follows:                                                       ing its reply. This approach is particularly useful when
  •   System prompt: The system prompt is an initial instruc-               precise and specific answers are needed.
      tion that is provided to set the context for the conver-          2) Imitation: This strategic approach uses a deliberate
      sation. The prompt provides the background information                sequence of queries intended to imitate an interactive
      and guidelines for a conversation                                     discourse with a subject expert. The primary objective
                                                                            of this strategy is to replicate the process of seeking
  2 https://huggingface.co/docs/transformers/modeldoc/bloom                 clarification and validation typically found in human-
  3 https://openai.com/gpt-4                                                machine interaction.The imitation seeks to replicate the

      dynamics of a consultation, where the user assumes the              Regnet model from Pytorch model repository. Using the Model
      role of the inquirer seeking expert insights.                       card, we provided the description for the model being used as
                                                                          well. For the objective, we stated that we were performing
E. Prompts using Imitation with Instruction Following:                    fine-tuning on a pre-trained model. Finally, we stated that we
   For designing the prompt using Instruction-following, we               wanted the hyperparameters to be outputted in a JSON format
designed a prompt structure with the following combination                for conducting our experiment. Figure 3 shows the complete
of a system prompt setting the context to imitate a subject-              prompt for usecase1.
matter expert and user prompts to then query the model. The
elements of the prompt, are shown in Figure 2:

Fig. 2. Example of the prompt structure for imitation with instructionfollowing strategy

  • System Prompt: This prompt sets the initial context for
    our experiment. In our case, we wanted the LLM to
    imitate a machine learning expert, who has conducted
    numerous experiments and possesses the knowledge from
    those experiments for our usecases. We used the follow-               Fig. 3. Prompt used for usecase 1 Real-time image classification for security
    ing system prompt to set the context for the conversation,            cameras.
    ”I want you to be a Machine Learning expert. You
    have the knowledge of training and finetuining various                   Similar to our approach for usecase 1 using the same
    machine learning models for various tasks. I want you to              imitation with instruction-following strategy, we developed
    use this knowledge to aid me in an experiment”                        the prompt for understanding financial market structures
  • Task: This describes the core task which need to be                   for investments. We decided to use the financial-phrasebank
    completed or achieved.                                                dataset [28] containing 4840 sentences from English language
  • Objective: States what the user wants to achieve with the             financial news categorised by sentiment. We decided to use
    task description or the end goal.                                     an off-the-shelf FinancialBERT [29] model from the Hugging
  • Dataset Description: Provides a brief description of the              Face model repository. We stated our objective was to fine-tune
    dataset used for the task. This can be obtained from the              this model on our dataset and queried the model to suggest
    dataset source.                                                       hyperparameters for this experiment in JSON format. Figure 4
  • Model Description: Provides a brief description of the                shows the complete prompt used for this experiment.
    model being used for the experiment obtained from the
    model cards.                                                          G. Design for research questions 1 and 2:
  • Output format: Describes how the used wants to gener-
    ate the output from the LLM.                                             To address research questions 1 and 2, we adopted the
                                                                          study design outlined in Figure 5. The prompts generated in
F. Prompts for each Usecase:                                              subsection III-F were employed to query the GPT-4 model.
   Using imitation with instruction-following strategy, we de-            For our experiments, we set the model’s temperature to 0,
veloped the following prompt for Real-time image classifi-                effectively curbing its inherent creativity. This was done to
cation for security cameras where we decided to use the                   mitigate the potential generation of content that deviates from
ObjectNet 2019 [27] containing images with unconventional                 the provided source, due to issues in encoding and decoding
views, contextual variations, and ambiguous scenarios as the              between text and representations as discussed by Kaddour et
dataset. For the model, we decided to use an off-the-shelf                al. (2023). By choosing a temperature of 0, we aimed to render

                                                                             to demonstrate if LLMs can provide a better search space
                                                                             for configuring hyperparameters. We decided to go with the
                                                                             Regnet model from usecase 1. The model is trained on the
                                                                             ImageNet 1k dataset. The ImageNet dataset is a widely used
                                                                             collection of labelled images covering thousands of object
                                                                             categories, serving as a foundational resource for training and
                                                                             evaluating computer vision models.
                                                                                In our study, we utilised a filtered version of the ObjectNet
                                                                             dataset. This choice was driven by two key factors: first,
                                                                             the dataset encompasses 113 classes that match those in
                                                                             ImageNet; and second, it rigorously assesses object recogni-
                                                                             tion models against real-world complexities, such as atypical
                                                                             angles and diverse backgrounds, with the intention of bol-
                                                                             stering their resilience in real-world situations. Our approach
                                                                             involved extracting images from ObjectNet that corresponded
                                                                             to ImageNet’s class categories, and subsequently assigning
                                                                             ImageNet’s class labels to these ObjectNet-derived images.
Fig. 4. Prompt for usecase 2 Understanding financial market structures for   We collected 2000 images from ObjectNet with the filtering
investments.                                                                 criteria applied to the images as the dataset for our experiment.
                                                                             We did this to fine-tune the pretrained Regnet model from
                                                                             Pytorch model repository has already been trained using these
the responses predominantly deterministic, while allowing a
                                                                             labels from ImageNet to fastrack our experiments using a 70slight degree of residual variability4 .
                                                                             30 split of the dataset containing 2000 images. Furthermore,
                                                                             for each experiment we froze all the layers of the model
                                                                             except the last layer on which the training and fine-tuning
                                                                             was performed.
                                                                                We set up four experiments with the following experiments
                                                                             1) fine-tuning using standard Bayesian optimisation, 2)
              Fig. 5. Design for research questions 1 and 2.                 fine-tuning using hyperparameters from [30, 31] research
                                                                             for Bayesian optimisation 3) fine-tuning using LLM to
   The design depicted in Figure 5 guided our methodology.                   configure the search space for Bayesian optimisation for
Under these specifications, we executed the prompts for each                 hyperparameter optimisation and 4) Using the results from
use case over a span of 100 iterations. We examined the                      the experiment iii to query an LLM to suggest starting
GPT-4 model’s responses, to answer research questions 1 and                  a smaller search space for the Bayesian optimisation of
2. JSON-formatted responses were gathered from the model,                    hyperparameters. In the following subsections, we describe
providing a snapshot of hyperparameter configurations for                    the setup for each of the experiments.
each use case. This enabled us to ascertain both the extent                     1) Fine-tuning using standard Bayesian optimisation::
of response variability (RQ1) within each use case and the                   In this experiment, we adopted the standard Bayesian optidivergence in prompts across different use cases (RQ2).                      misation approach to fine-tune the pretrained Regnet model.
   To measure the significance of our findings, we employed                  We utilised the Hyperopt5 library to initialise the optimisation
appropriate statistical tests to our research questions. For                 process.
assessing response variability within each use case (RQ1),                      Hyperopt uses a search space which defines the range of
we computed variance or standard deviation for responses,                    potential values of hyperparameters that are explored during
comparing these metrics across use cases. For prompt diversity               the optimisation process. This allows the exploration of various
between use cases (RQ2), we calculated Jaccard index between                 options for each hyperparameter, allowing the optimisation
the two usecases. We applied ANOVA or Kruskal-Wallis tests                   algorithm to evaluate and determine the beast configuration
to determine if these metrics differed significantly between use             for a machine learning model. The selection of an appropriate
cases.                                                                       search space is critical, as it directly influences the effective-
H. Design for research question 3:                                           ness and efficiency of the hyperparameter tuning process.
                                                                                To execute this experiment, we use the following configu-
   To investigate research question 3, we wanted to identify                 ration for the search space presented in Table I.
if an LLM can provide a sensible search space for finetuning
                                                                                We executed the optimisation process with the configuration
hyperparameters as compared to approaches such as Bayesian
                                                                             in Table I for 10 trials containing 3 epochs each due to
optimisation. We used a computer vision as an exemplar
  4 https://platform.openai.com/docs/models/overview                           5 https://github.com/hyperopt/hyperopt

                           TABLE I                                                          TABLE III
H YPERPARAMETER CONFIGURATION FOR F INE - TUNING USING STANDARD      H YPERPARAMETER CONFIGURATION FOR F INE - TUNING USING LLM
  BAYESIAN OPTIMISATION WITH RANDOM INITIALISED SEARCH SPACE            RECOMMENDED SEARCH SPACE FOR BAYESIAN OPTIMISATION

  Parameters                     Value Ranges                        Parameters                      Value Ranges
  Learning Rate                  1e-5 , 1e5                          Learning Rate                   -4 , -2
  Momentum                       0.0 , 1.0                           Momentum                        0.001 , 0.01
  Batch Size                     32                                  Batch Size                      32
  No. of Epochs                  3                                   No. of Epochs                   3
  No. of Trials                  10                                  No. of Trials                   10
  Gamma                          1e-5 , 1e5                          Gamma                           -8 , -3
  Step Size                      0 , 20                              Step Size                       10 , 20 ,30

the resource restrictions imposed by a single threaded CPU         can improve the fine-tuning process by reducing the no. of
execution.                                                         trials required to achieve the performance from the previous
   2) Fine-tuning using hyperparameters from research pa-          experiment. The configuration used for this experiment is
pers for Bayesian optimisation:: Using research papers to          present in the Table IV.
find hyperparameters is a common approach for fine-tuning
machine learning models. For this experiment, we referred to                                TABLE IV
the research papers by [30] and [31]. We utilised these papers       H YPERPARAMETER CONFIGURATION FOR F INE - TUNING USING PRIOR
                                                                                     CONFIGURATIONS AND PERFORMANCE
as they describe the parameters used for fine-tuning the Regnet
models, same as the model we are using for our experiment.           Parameters                      Value Ranges
In Table II we describe the configuration for fine-tuning with       Learning Rate                   0.01 , 0.03
                                                                     Momentum                        0.001 , 0.01
the configurations obtained from the research papers [30, 31]
                                                                     Batch Size                      32
for the experiment.                                                  No. of Epochs                   3
                                                                     No. of Trials                   10
                         TABLE II                                    Gamma                           0.0001 , 0.001
  H YPERPARAMETER CONFIGURATION FOR F INE - TUNING USING LLM         Step Size                       15 , 25
     RECOMMENDED SEARCH SPACE FOR BAYESIAN OPTIMISATION

  Parameters                     Value Ranges
                                                                      5) Metrics collection:: To measure the significance of our
  Learning Rate                  0.9                               findings, we employed appropriate statistical tests tailored
  Momentum                       0.015                             to our research questions. For assessing response variability
  Batch Size                     32                                within each usecase (RQ1), we computed variance or standard
  No. of Epochs                  3
  No. of Trials                  10                                deviation for responses, comparing these metrics across use
  Gamma                          0.1                               cases. For prompt diversity between use cases (RQ2), we
  Step Size                      8, 12                             applied ANOVA tests to determine if these metrics differed
                                                                   significantly between use cases.
   3) Fine-tuning using LLM to configure the search space             For (RQ3) we calculated the validation loss and validation
for Bayesian optimisation for hyperparameter optimisation::        accuracy for each of the experiments described in subsec-
In this experiment, we queried an LLM to suggest the               tion III-H. We visualised the results for all the experiments
starting conditions for Bayesian optimisation. We provided         and reported our findings in subsubsection IV-E1.
the LLM with a prompt similar to the prompt described in
subsection III-F where we described the task and objective                           IV. E XPERIMENTAL R ESULTS
utilising the imitation with instruction-following strategy. The
exception in this case is that we queried the search space            In this section, we present the outcomes of our experiments
initialisation using the ChatGPT UI. The following Table III       aimed at addressing the research questions outlined in the
shows the search space configurations suggested by the GPT-4       previous sections. The results are organised according to each
Model.                                                             research question, highlighting the key findings.
   4) Using the results from the experiment iii to query an
                                                                   A. RQ1: What is variability within a specific usecase?
LLM to suggest starting a smaller search space for the
Bayesian optimisation.: In our fourth experiment, we aimed            To investigate the variability within responses generated
to explore whether an LLM could leverage prior knowledge           by the ChatGPT-4 model, we analysed the JSON-formatted
of fine-tuning or training procedures to identify enhanced         responses obtained from 100 iterations of each use case.
search spaces for hyperparameter configuration in model fine-         To investigate the variability within specific use cases, we
tuning. For this experiment, we utilised the results i.e. the      conducted a thorough analysis of selected columns pertaining
validation loss and the validation accuracy from the previous      to different attributes. The subsequent sections present the
experiment along with the hyperparameters used to query an         outcomes of our statistical tests, highlighting the standard
LLM to identify a new set of hyperparameters by which we           deviations and variances observed within each use case.

   1) Usecase 1: Real-time image classification for security      parameter to 0, effectively reducing randomness during text
cameras.: For Usecase 1, we examined the standard devia-          generation, appears to accentuate this effect. The use of a
tions and variances of the following parameters ’Learning         temperature parameter controls the level of randomness in
Rate’, ’Momentum’, ’Batch Size’, ’Num Epochs’,                    the generated output. A value of 0 enforces a deterministic
’Step Size’, and ’Gamma’. The results indicate a negli-           response, which could align with the observed uniformity.
gible variability within these attributes. Specifically:             2) Impact of keywords: Furthermore, We tried to reword
   • Learning Rate: The standard deviation was found to be        and rephrase the prompts in an attempt to introduce diver-
     approximately 6.505 × 10−19 , with a variance of about       sity into the generated responses. Despite these efforts, the
     4.232 × 10−37 .                                              Language Model (LM) consistently provided similar responses
   • Momentum: Similarly, the standard deviation was close        when presented with prompts related to computer vision across
     to 7.772 × 10−16 , accompanied by a variance of roughly      different use cases. This finding is intriguing as it suggests a
     6.040 × 10−31 .                                              degree of consistency and predictability in the LM’s responses
   • Batch Size, Num Epochs, and Step Size: These at-             for specific domains or topics or the use of specific keywords
     tributes exhibited a standard deviation and variance of      such as ”Classification”, ”Fine-tuning” or a description of a
     0, suggesting a lack of variability.                         particular task.
   • Gamma: The standard deviation was approximately
     1.943 × 10−16 , with a variance of about 3.775 × 10−32 .     C. RQ2: How does the variability differ across multiple
   2) Usecase2: Understanding financial market structures for     usecases?
investments.: Similar to the previous usecase we conducted
the same statistical tests to calculate the standard devia-          The investigation into variability across multiple usecases
tions and variances for the usecase. Similar to usecase 1         was carried out through an Analysis of Variance (ANOVA)
we examined the following parameters ’Learning Rate’,             test, probing the differences in variability among selected
’Momentum’, ’Batch Size’, ’Num Epochs’, ’Step                     attributes. This section presents the outcomes of the ANOVA
Size’, and ’Gamma’.                                               test:
   • Learning Rate: The standard deviation was calculated           • Attribute: Learning Rate
     to be approximately 3.727 × 10−20 , with a corresponding         The ANOVA analysis for the ’Learning Rate’ attribute
     variance of about 1.389 × 10−39 .                                produced a remarkably high F-statistic, implying a sub-
   • Momentum: Akin to the previous use case, the standard            stantial variation in variability across different usecases.
     deviation was close to 7.772 × 10−16 , accompanied by a          The corresponding p-value of 0.0 indicates a significant
     variance of roughly 6.040 × 10−31 .                              departure from the null hypothesis, signifying notable
   • Batch Size, Num Epochs, and Step Size: These at-                 differences in the dispersion of data points among the
     tributes consistently exhibited a standard deviation and         usecases.
     variance of 0, indicative of low variability.                  • Attribute: Batch Size
   • Gamma: The standard deviation aligned closely with the           In parallel to the ’Learning Rate’, the ANOVA test
     previous use case, at approximately 1.943 × 10−16 , with         applied to the ’Batch Size’ attribute demonstrated a re-
     a variance of about 3.775 × 10−32 .                              markably elevated F-statistic and a p-value of 0.0. These
   The consistent trend of low standard deviations and vari-          results identify substantial differences in variability across
ances across both use cases displays a pattern of minimal             usecases with respect to the ’Batch Size’.
variability within the selected attributes. These findings sug-     • Attribute: Num Epochs

gest that the examined attributes demonstrate a high degree of        Analogously, the ’Num Epochs’ attribute revealed signifconsistency and lack of deviation from their respective means.        icant variability differences across usecases, as indicated
                                                                      by the exceedingly high F-statistic and the associated p-
B. Findings from RQ1:                                                 value of 0.0.
                                                                    • Attribute: Step Size
   In this section we discuss the findings from our experiments
and draw possible conclusions.                                        Further reinforcing the theme of variability disparities
   1) Lack of Variance and Potential Explanations: The con-           across usecases, the ANOVA analysis for the ’Step Size’
sistent pattern of minimal variability observed within the            attribute yielded a conspicuously high F-statistic and
selected attributes across both use cases prompts us to explore       a p-value of 0.0. This underscores the importance of
potential explanations for this phenomenon. One plausible             accounting for diverse requirements in terms of step sizes
explanation for the consistently low variability is the possi-        for different application contexts.
bility of response caching within the Generative Pre-trained         The results from the ANOVA tests identify variability across
Transformer (GPT) models. It is conceivable that the model        multiple usecases. Attributes such as ’Learning Rate’, ’Batch
may have learned and cached certain responses, leading to a       Size’, ’Num Epochs’, and ’Step Size’ display pronounced
uniformity of outcomes for specific prompts. This behaviour       differences in variability, signifying the distinct contextual
is further reinforced by the fact that setting the temperature    demands associated with different application scenarios

D. Findings from RQ2:
   In this section we discuss our findings from RQ2.
   The investigation into variability across multiple usecases
has revealed how Language Model (LM) discerns differences
between various application scenarios. The analysis of selected
attributes has showcased substantial variations in variability,
shedding light on the LM’s capacity to adapt its responses in
accordance with specific usecases.
   Notably, attributes such as ’Learning Rate’, ’Batch Size’,
’Num Epochs’, and ’Step Size’ exhibited differences in variability across different usecases.
   1) Observations Regarding ’Momentum’ and ’Gamma’:
An interesting observation concerning the ’Momentum’ and
’Gamma’ attributes. It is to be noted that these attributes        Fig. 7. Validation losses after dropping the configurations obtained from the
demonstrated consistent variability patterns across both use-      research papers.
cases. This uniformity prompts us to consider a compelling
hypothesis: the LM might have learned these specific parameter values as commonly used defaults in notebooks and paper           Figure Figure 7 identifies interesting observations. In an
implementations.                                                   additional experiment, we inquired the LLM for hyperparam-
                                                                   eters and fine-tuned the model following the prompt structure
E. RQ3: How does the performance of LLM-configured                 outlined in Figure Figure 3, without Bayesian optimization.
software tools compare with state-of-the-art methods?              This inquiry is represented by the yellow line in the graph.
   For this experiment we ran for experiments with different       Furthermore, it becomes evident that when utilizing the LLM
hyperparameter configurations. For the first set of experiments    to derive initial conditions and search spaces for Bayesian
outlined in subsection III-H we compared the the validation        optimization, as indicated by the green line in Figure Figure 7,
losses initialising the i)Bayesian optimisation process with a     a more rapid reduction in validation loss is observed, indicative
random start, ii)parameters obtained from a research paper,        of a better model convergence.
iii) using an LLM to suggest the start conditions and search          1) Findings from RQ3: In this section we discuss our
space for the optimisation process and finally using the outputs   findings from RQ3.
collected from step 3 to inform, finding a new search space           In the context of machine learning a higher validation loss
using an LLM. The results of the experiment are displayed          and training time are inter-related in the following manner.
in Figure 6. Analysis of Figure Figure 6 reveals distinct          From Figure 6 a high validation loss for hyper parameters ob-
                                                                   tained from the paper suggests that the model is not performing
                                                                   well on unseen data. This could be because the model has
                                                                   learnt the noise from the training data and is not performing
                                                                   well. To address this issue the model will require more training
                                                                   epoch to converge onto a solution.
                                                                      Another reason we suspect this behaviour is that the hy-
                                                                   perparameters obtained are from two research papers. From
                                                                   research [30] we obtained the learning rate and the momentum
                                                                   for the experiment while the step size and gamma values wer
                                                                   obtained from [31]. These parameters may not be compatible
                                                                   with each other as the [30] did not publish the configurations
                                                                   for gamma and step size.
                                                                      From these initial experiments we conducted we can say
                                                                   that the LLMs are sensible to identify hyperparameters for
            Fig. 6. Validation losses for each experiment          an experiment. However we need to further investigate this
                                                                   claim by performing additional experiments utilising different
trends among the hyperparameter configurations. Notably, the       models and across different machine learning domains.
configuration stemming from research papers exhibits the
highest validation loss, while the remaining configurations                                    V. D ISCUSSION
closely align with each other in terms of validation loss during
the fine-tuning process. To delve deeper into the fine-tuning         Our study provides valuable insights into the utility of
process, we excluded the results from the research paper           Language Models (LLMs) for hyperparameter configuration
configurations and examined the results in Figure Figure 7.        in fine-tuning tasks. However, certain observations from our
                                                                   experiments requires attention. Notably, we found that LLMs

demonstrated the capacity to propose sensible initial hyperpa-    field of interest that occurred after that date are outside the
rameter configurations, as highlighted in subsubsection IV-E1.    model’s awareness. This limitation can impact the accuracy
Particularly significant is the trend where, within a con-        and relevance of LLM-generated suggestions, particularly in
strained scope of epochs and trials, the hyperparameter set-      rapidly evolving domains where recent information is crucial
tings suggested by LLMs yielded more favourable validation        for optimal hyperparameter configurations or utilising model
loss outcomes when compared to alternative strategies. This       and datasets curated after September of 2021.
underscores the potential effectiveness of LLMs in enhancing
hyperparameter optimisation, especially in scenarios where                              VI. C ONCLUSION
limited trials and epochs are available for a specific use           Our study emphasises the significance of leveraging LLMs
case. Nevertheless, due to the exploratory nature of this         to automate and streamline the configuration process, reducing
study and resource limitations involving a single-threaded        the reliance on manual intervention and human expertise. By
CPU, it’s possible that the hyperparameters derived from the      harnessing the power of LLMs, software engineers can benefit
reference papers exhibited suboptimal performance. Future         from their adaptability, customisation potential, and ability to
investigations involving larger datasets and increased trial      generate scenario-specific configurations.
numbers are warranted to validate these findings. By doing           However, it is important to note that while LLMs show
so, we aim to ascertain the robustness and generalizability       promise in automating the configuration process, the observed
of this phenomenon. it is important to acknowledge several        variability in weight distributions necessitates further validalimitations and engage in a thoughtful discussion of their        tion and refinement. In conclusion, this study contributes to
implications.                                                     the advancement of intelligent and adaptive software systems
                                                                  by demonstrating the effectiveness of LLMs in autonomously
A. Limitations                                                    configuring software tools. The inherent variability of config-
   1) Resource Limitations:: Our study was conducted with         urations within a given scenario and the variability of configuresource constraints, utilising a single-threaded CPU. This       rations across different scenarios highlight the adaptability and
might have impacted the performance and thoroughness of           customisation potential of LLMs. This research opens up new
the experiments, potentially influencing the effectiveness of     possibilities for efficient and optimised software engineering
hyperparameter recommendations.                                   practices, facilitating the deployment of software systems that
   2) Limited Exploration:: The experiments were carried out      align closely with the specific needs and requirements of
with a restricted number of trials and epochs due to resource     diverse application domains.
constraints. This limitation could have hindered the comprehensive exploration of the hyperparameter search space,                                 R EFERENCES
possibly affecting the generalizability of the findings.           [1] Y. Zhang, H. He, O. Legunsen, S. Li, W. Dong, and
   3) Model Specificity:: The outcomes of this study are par-          T. Xu, “An evolutionary study of configuration deticularly relevant to the specific LLM model used (ChatGPT-4)          sign and implementation in cloud systems,” in 2021
and might not be directly transferable to other LLMs or models         IEEE/ACM 43rd International Conference on Software
with distinct characteristics.                                         Engineering (ICSE). IEEE, 2021, pp. 188–200.
                                                                   [2] N. Siegmund, N. Ruckel, and J. Siegmund, “Dimensions
B. Assumption of Expertise:                                            of software configuration: on the configuration context
   The imitation-based prompts assume a certain level of               in modern software development,” in Proceedings of the
expertise from LLMs, which might not be accurate in all                28th ACM Joint Meeting on European Software Engisituations.                                                            neering Conference and Symposium on the Foundations
   1) Hallucination and Inaccurate Information:: An impor-             of Software Engineering, 2020, pp. 338–349.
tant limitation arises from the potential for LLMs to generate     [3] Z. Wan, X. Xia, D. Lo, and G. C. Murphy, “How
responses that contain incorrect or fabricated information, a          does machine learning change software development
phenomenon known as ”hallucination.” This limitation stems             practices?” IEEE Transactions on Software Engineering,
from the model’s training data, which might include mis-               vol. 47, no. 9, pp. 1857–1871, 2021.
information, bias, or inaccuracies present in online content.      [4] S. Fahmy, A. Deraman, J. Yahaya, and A. R. Hamdan,
Consequently, LLM-generated suggestions could inadvertently            “Human competency assessment for software configuralead to suboptimal hyperparameter configurations that might            tion management,” Annals of Emerging Technologies in
appear sensible but are based on inaccurate premises.                  Computing (AETiC), vol. 5, no. 5, pp. 69–78, 2021.
                                                                   [5] J. Kannan, S. Barnett, L. Cruz, A. Simmons, and
C. Knowledge Cutoff and Currency:                                      A. Agarwal, “Mlsmellhound: a context-aware code anal-
   An inherent limitation of GPT-4 is its knowledge cutoff,            ysis tool,” in Proceedings of the ACM/IEEE 44th Internawhich restricts its familiarity with events, developments, and         tional Conference on Software Engineering: New Ideas
information beyond a certain date. Since GPT-4’s training data         and Emerging Results, 2022, pp. 66–70.
includes information only up to a specific point in time (e.g.,    [6] G. Blumenschein, “Monitoring builds in a devops infras-
September 2021), any advancements, trends, or changes in the           tructure/submitted by georg blumenschein,” 2023.

 [7] R. Krishna, M. S. Iqbal, M. A. Javidian, B. Ray, and               Computing, 2022.
     P. Jamshidi, “Cadet: Debugging and fixing misconfigu-         [19] D. Jin, Z. Jin, Z. Hu, O. Vechtomova, and R. Mihalcea,
     rations using counterfactual reasoning,” arXiv preprint            “Deep learning for text style transfer: A survey,” Com-
     arXiv:2010.06061, 2020.                                            putational Linguistics, vol. 48, no. 1, pp. 155–205, 2022.
 [8] M. S. Iqbal, R. Krishna, M. A. Javidian, B. Ray,              [20] M. Shafiq and Z. Gu, “Deep residual learning for image
     and P. Jamshidi, “Unicorn: reasoning about configurable            recognition: A survey,” Applied Sciences, vol. 12, no. 18,
     system performance through the lens of causality,” in              p. 8972, 2022.
     Proceedings of the Seventeenth European Conference on         [21] G. Hinton, L. Deng, D. Yu, G. E. Dahl, A.-r. Mo-
     Computer Systems, 2022, pp. 199–217.                               hamed, N. Jaitly, A. Senior, V. Vanhoucke, P. Nguyen,
 [9] K. F. Tomasdottir, M. Aniche, and A. Van Deursen, “Why             T. N. Sainath et al., “Deep neural networks for acoustic
     and how JavaScript developers use linters,” in ASE 2017            modeling in speech recognition: The shared views of
     - Proceedings of the 32nd IEEE/ACM International Con-              four research groups,” IEEE Signal processing magazine,
     ference on Automated Software Engineering. Institute               vol. 29, no. 6, pp. 82–97, 2012.
     of Electrical and Electronics Engineers Inc., nov 2017,       [22] C. E. Rasmussen, “Gaussian processes in machine learn-
     pp. 578–589.                                                       ing,” in Summer school on machine learning. Springer,
[10] C. Vassallo, S. Panichella, F. Palomba, S. Proksch,                2003, pp. 63–71.
     H. C. Gall, and A. Zaidman, “How developers                   [23] S. Wang, T. Tuor, T. Salonidis, K. K. Leung, C. Makaya,
     engage with static analysis tools in different contexts,”          T. He, and K. Chan, “When edge meets learning: Adap-
     Empirical Software Engineering, vol. 25, no. 2, pp.                tive control for resource-constrained distributed machine
     1419–1457, mar 2020. [Online]. Available: https:                   learning,” in IEEE INFOCOM 2018-IEEE conference on
     //link.springer.com/article/10.1007/s10664-019-09750-5             computer communications. IEEE, 2018, pp. 63–71.
[11] K. F. Tomasdottir, M. Aniche, and A. Van Deursen, “The        [24] I. Standard, “Green AI : Do deep learning frameworks
     Adoption of JavaScript Linters in Practice: A Case Study           have different costs,” 2019.
     on ESLint,” IEEE Transactions on Software Engineering,        [25] C.-j. W. Ramya, R. Udit, G. Bilge, A. Newsha, A. Kiwan,
     vol. 46, no. 8, pp. 863–891, aug 2020.                             G. Chang, F. Aga, B. James, H. Charles, B. Michael,
[12] K. Wood and E. Pereira, “Impact of misconfiguration                G. Anurag, M. Ott, A. Melnikov, S. Candido, D. Brooks,
     in cloud–investigation into security challenges,” Interna-         G. Chauhan, B. Lee, H.-h. S. L. Bugra, A. Max, B. Joe,
     tional Journal Multimedia and Image Processing, vol. 1,            S. Ravi, J. Mike, and R. Kim, “Sustainable AI: Envi-
     no. 1, pp. 17–25, 2011.                                            ronmental Implications, Challenges and Opportunities,”
[13] F. Hutter, M. Lindauer, A. Balint, S. Bayless, H. Hoos,            2022.
     and K. Leyton-Brown, “The configurable sat solver chal-       [26] J. Kaddour, J. Harris, M. Mozes, H. Bradley, R. Raileanu,
     lenge (cssc),” Artificial Intelligence, vol. 243, pp. 1–25,        and R. McHardy, “Challenges and applications of large
     2017.                                                              language models,” arXiv preprint arXiv:2307.10169,
[14] M. Bilal, M. Canini, and R. Rodrigues, “Finding the right          2023.
     cloud configuration for analytics clusters,” in Proceed-      [27] A. Barbu, D. Mayo, J. Alverio, W. Luo, C. Wang,
     ings of the 11th ACM Symposium on Cloud Computing,                 D. Gutfreund, J. Tenenbaum, and B. Katz, “Objectnet:
     2020, pp. 208–222.                                                 A large-scale bias-controlled dataset for pushing the
[15] M. A. Langford, K. H. Chan, J. E. Fleck, P. K. McKinley,           limits of object recognition models,” Advances in neural
     and B. H. Cheng, “Modalas: addressing assurance for                information processing systems, vol. 32, 2019.
     learning-enabled autonomous systems in the face of            [28] P. Malo, A. Sinha, P. Korhonen, J. Wallenius, and
     uncertainty,” Software and Systems Modeling, pp. 1–21,             P. Takala, “Good debt or bad debt: Detecting semantic
     2023.                                                              orientations in economic texts,” Journal of the Associ-
[16] M. Casimiro, P. Romano, D. Garlan, and L. Rodrigues,               ation for Information Science and Technology, vol. 65,
     “Towards a Framework for Adapting Machine Learn-                   2014.
     ing Components,” Proceedings - 2022 IEEE Interna-             [29] A. Hazourli, “Financialbert-a pretrained language model
     tional Conference on Autonomic Computing and Self-                 for financial text mining,” Technical report, Tech. Rep.,
     Organizing Systems, ACSOS 2022, pp. 131–140, 2022.                 2022.
[17] J. Gesi, X. Shen, Y. Geng, Q. Chen, and I. Ahmed,             [30] A. Kumar, R. Shen, S. Bubeck, and S. Gunasekar, “How
     “Leveraging feature bias for scalable misprediction ex-            to fine-tune vision models with sgd,” arXiv preprint
     planation of machine learning models,” in Proceedings              arXiv:2211.09359, 2022.
     of the 45th International Conference on Software Engi-        [31] P. Goyal, Q. Duval, I. Seessel, M. Caron, I. Misra,
     neering (ICSE), 2023.                                              L. Sagun, A. Joulin, and P. Bojanowski, “Vision mod-
[18] Y. Xiao, I. Beschastnikh, Y. Lin, R. S. Hundal, X. Xie,            els are more robust and fair when pretrained on un-
     D. S. Rosenblum, and J. S. Dong, “Self-checking deep               curated images without supervision,” arXiv preprint
     neural networks for anomalies and adversaries in deploy-           arXiv:2202.08360, 2022.
     ment,” IEEE Transactions on Dependable and Secure
