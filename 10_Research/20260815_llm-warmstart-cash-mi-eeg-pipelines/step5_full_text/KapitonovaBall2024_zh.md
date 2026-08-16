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
language: "bilingual-en-zh"
---

# Human-AI Teaming Using Large Language Models: Boosting Brain-Computer Interfacing (BCI) and Brain Research | 運用大型語言模型的人機協作：促進腦機介面（BCI）與腦科學研究

> [!abstract] 重點摘要
> - 主張在現階段，強調人機協作（human-AI teaming）而非全自主的「AI BCI 研究員」，是推進腦機介面（Brain-Computer Interface, BCI）研發最有前景的路徑。
> - 提出雙面向的 Janusian 設計原則（Janusian Design Principles），同時面向人類專家賦能與 AI 能力放大，並以此為基礎建構「協作工作空間」概念。
> - 開發以 Python 實作、以 GPT-4o 為底層大型語言模型（LLM）的 ChatBCI 工具箱，涵蓋 EEG 資料集、知識庫、前處理、解碼模型、訓練與視覺化等核心功能。
> - 在 BCI Competition IV 2a 運動想像（motor imagery）資料集上示範完整研究循環：從想法生成、探索性資料分析與驗證，到 LLM 生成之卷積神經網路（CNN）解碼器與訓練迴圈的實作。
> - 探索性分析發現受試者對視覺提示產生朝箭頭方向的眨眼—掃視（blink-saccade）眼動偽跡，且其快速暫態成分可通過 4-Hz 高通濾波，質疑文獻中接近 100% 解碼準確率的實質意義。
> - LLM 生成的腦訊號解碼器可視為一類新型 AutoML；結構化互動框架促成人類專家知識向 AI 的轉移，以及有意義的人機共同學習（co-learning）。

---

## Abstract | 摘要

> [!quote] Original
> Recently, there is an increasing interest in using artificial intelligence (AI) to automate aspects of the research process, or even autonomously conduct the full research cycle from idea generation, over data analysis, to composing and evaluation of scientific manuscripts. Examples of working "AI scientist" systems have been demonstrated for computer science tasks and running molecular biology labs. While some approaches aim for full autonomy of the scientific AI, others rather aim for leveraging human-AI teaming. Here, we address how to adapt such approaches for boosting Brain-Computer Interface (BCI) development, as well as brain research resp. neuroscience at large. We argue that at this time, a strong emphasis on human-AI teaming, in contrast to fully autonomous "AI BCI researcher," will be the most promising way forward. We introduce the collaborative workspaces concept for human-AI teaming based on a set of Janusian design principles, looking both ways, to the human as well as to the AI side. Based on these principles, we present ChatBCI, a Python-based toolbox for enabling human-AI collaboration based on interaction with Large Language Models (LLMs), designed for BCI research and development projects. We show how ChatBCI was successfully used in a concrete BCI project on advancing motor imagery decoding from EEG signals. Our approach can be straightforwardly extended to broad neurotechnological and neuroscientific topics, and may by design facilitate human expert knowledge transfer to scientific AI systems in general.

> [!note] 翻譯
> 近年來，使用人工智慧（AI）自動化研究流程的各個環節，甚至自主執行從想法生成、資料分析，到科學論文撰寫與評估之完整研究循環的興趣日益增加。可運作的「AI 科學家」系統已在電腦科學任務與分子生物學實驗室運營中獲得展示。部分方法追求科學 AI 的完全自主，另一些則著眼於發揮人機協作（human-AI teaming）的效益。本文探討如何調整此類方法，以促進腦機介面（Brain-Computer Interface, BCI）的開發，乃至腦科學研究與整體神經科學。我們主張，在現階段，相較於全自主的「AI BCI 研究員」，強調人機協作將是最有前景的前進方向。我們提出基於一組 Janusian 設計原則的人機協作「協作工作空間」概念——此概念雙向兼顧，同時面向人類端與 AI 端。基於這些原則，我們提出 ChatBCI：一個以 Python 實作、透過與大型語言模型（Large Language Models, LLMs）互動來實現人機協作的工具箱，專為 BCI 研究與開發專案而設計。我們展示 ChatBCI 如何成功應用於一項具體的 BCI 專案，以推進由 EEG 訊號進行運動想像（motor imagery）解碼。我們的方法可直接延伸至廣泛的神經科技與神經科學主題，且在設計上有助於將人類專家知識轉移至一般的科學 AI 系統。

---

## 1 Introduction | 引言

> [!quote] Original
> Human–Artificial Intelligence (AI) collaborations may play an important role in promoting scientific discovery Wang et al. [2023]. Efforts to automate research and development tasks have recently led to the creation of "AI scientists"—AI agents specialized to autonomously carry out various roles in the scientific process. AI scientists may run virtual or even physical labs (self-driving labs, SDLs Rapp et al. [2024]) and thus help to "super-charge" fields including biomedical research, material science, or computer science.
>
> For example, in an effort to leverage AI for biomedical research, Swanson and colleagues have recently created a virtual laboratory that brings together multiple AI scientists—implemented as large language models (LLMs) with specialized roles—to collaborate autonomously on objectives defined by human investigators. This system rapidly proposed nearly 100 designs for antibody fragments, known as nanobodies, capable of binding to the virus responsible for COVID-19—accomplishing in a short span what might otherwise take a purely human team much longer Swanson et al. [2024]. In the field of computer science, Lu and colleagues introduced an AI scientist system that aims to automate the complete research cycle. They also use a LLM to survey scientific literature, formulate hypotheses, test them by running experiments, and then compile the results into a written paper. The system goes a step further by performing its own 'peer review,' evaluating and refining the outputs it generates Lu et al. [2024]. However, despite these impressive first demonstrations, substantial challenges remain for AI-driven scientific discovery in general, as well as for the adaptation of AI-scientists to the unique requirements that the scientific domains of research and development may impose on such a system.

> [!note] 翻譯
> 人類與人工智慧（AI）的協作可能在促進科學發現方面扮演重要角色 Wang et al. [2023]。近期自動化研發任務的努力催生了「AI 科學家」——即專門化的 AI 代理（agent），可自主承擔科學流程中的各種角色。AI 科學家可以運行虛擬甚至實體實驗室（自駕實驗室，self-driving labs, SDLs；Rapp et al. [2024]），從而協助「強力加速」生醫研究、材料科學或電腦科學等領域。
>
> 例如，為了將 AI 應用於生醫研究，Swanson 及其同事近期建立了一個虛擬實驗室，匯集多個 AI 科學家——以具專門角色的大型語言模型（LLM）實作——針對人類研究者所定義的目標自主協作。該系統快速提出了近 100 種能與 COVID-19 病毒結合的抗體片段（即奈米抗體，nanobodies）設計——在短時間內完成了純人類團隊可能需時甚久的工作 Swanson et al. [2024]。在電腦科學領域，Lu 及其同事提出了一個旨在自動化完整研究循環的 AI 科學家系統。他們同樣使用 LLM 來調查科學文獻、形成假說、透過執行實驗加以檢驗，再將結果彙整為書面論文。該系統更進一步執行自身的「同儕審查」，評估並精煉其生成的產出 Lu et al. [2024]。然而，儘管這些初步展示令人印象深刻，AI 驅動的科學發現整體上仍面臨重大挑戰；將 AI 科學家調適至各科學研發領域對此類系統可能施加的獨特要求，亦是如此。

---

> [!quote] Original
> For AI-scientist systems (AISS) in general, for example, one major challenge is how to integrate the vast amount of implicit, unstructured, and often context-dependent knowledge and expertise that human scientists rely on for experimental design, interpretation, and innovation—which may not be sufficiently represented in the available training data. For example, Rihm and colleagues argue that current AI-driven labs are only able to incorporate explicit knowledge such as recorded data, scientific documentation, and publications and hence only see the "tip of the iceberg" of the relevant knowledge Rihm et al. [2024].
>
> However, even explicit knowledge such as that expressed in scientific publications may hold problems of its own when used as training data for scientific AI systems. Geoffrey Boulton and Moumita Koley observed in a contribution for the International Science Council: "Many scientific journals and papers maintain high standards, too many lack proper editorial oversight, many lack rigour and integrity, some engage in fraudulent practices, [...], and agreed standards for overall governance of the process are lacking." International Science Council [2023]. All of these point might compromise training data quality. This situation, together with the recent "publishing explosion" (47% growth between 2016 and 2022 in the global number of published papers Hanson et al. [2024]; nearly 250% increase in the number of Google Scholar hits for the term "Brain-Computer Interface" in the same time interval) which may reflect a overemphasis of quantity compared to quality, may result in quality issues with respect to the available training data for scientific AI systems. Such problems may be more or less serious in some research fields compared to others Bakker et al. [2012].

> [!note] 翻譯
> 就一般的 AI 科學家系統（AI-scientist systems, AISS）而言，一項主要挑戰在於如何整合人類科學家在實驗設計、詮釋與創新中所仰賴的大量內隱、非結構化且往往依賴脈絡的知識與專業——這些知識未必在可得的訓練資料中獲得充分呈現。例如，Rihm 及其同事主張，當前 AI 驅動的實驗室僅能納入諸如記錄資料、科學文件與出版品等外顯知識，因此只能看見相關知識的「冰山一角」Rihm et al. [2024]。
>
> 然而，即使是科學出版品所表達的外顯知識，作為科學 AI 系統的訓練資料時也可能自帶問題。Geoffrey Boulton 與 Moumita Koley 在為國際科學理事會（International Science Council）撰寫的文章中指出：「許多科學期刊與論文維持著高標準，但太多期刊缺乏適當的編輯監督，許多缺乏嚴謹性與誠信，有些甚至涉及欺詐行為，[...]，而且整體流程治理的公認標準仍付之闕如。」International Science Council [2023]。這些問題都可能損害訓練資料品質。此一情況，加上近年的「出版爆炸」（2016 至 2022 年間全球論文發表數量成長 47% Hanson et al. [2024]；同一期間 Google Scholar 上「Brain-Computer Interface」一詞的檢索命中數增加近 250%）——這可能反映了重量不重質的傾向——恐導致科學 AI 系統可用訓練資料的品質問題。此類問題在不同研究領域的嚴重程度可能有別 Bakker et al. [2012]。

---

> [!quote] Original
> Thus, in addition to general challenges such as those related to training data availability and quality, adaptation of scientific AI to a specific research field may hold its own challenges—not only due to differences in research standards. AI systems may face vastly different requirements depending, for example, on the role of experimental work, the complexity of the relevant mathematical tools and concepts, or the degree of adoption of open science practices with resulting differences in public data and scientific software access. For example, adoption of scientific AI agents in subfields of machine learning such as diffusion modeling, transformer-based language modeling, and learning dynamics Lu et al. [2024] benefits from publicly accessible and well-documented datasets of, for many relevant questions, adequate size and quality, as well as from well-established benchmarks. In contrast, fields such as EEG-based BCI research face much larger problems of small and diverse datasets of variable data quality. Additional domain-specific challenges arise due to the complexities of the foundation of EEG signals in multiscale brain dynamics and their relation to human cognition, emotion, and behavior Friston [2005].
>
> [Figure 1: A conceptual illustration of AI-driven scientific research processes for Brain-Computer Interfacing (BCI) and brain research, based on the concept as proposed by Lu et al. [2024]. In contrast to their concept, we consider numerical results and data visualization as an integral part of the experimental iteration loop. Also in contrast to their fully autonomous approach, we support flexible levels of shared autonomy for each phase of the cycle (color coded to reflect the example BCI project as reported here; automatic paper write-up was not addressed)]

> [!note] 翻譯
> 因此，除了訓練資料可得性與品質等一般性挑戰之外，將科學 AI 調適至特定研究領域亦可能帶來其自身的挑戰——且不僅是因為研究標準的差異。AI 系統可能面臨截然不同的要求，例如取決於實驗工作的角色、相關數學工具與概念的複雜度，或開放科學實踐的採納程度及其對公開資料與科學軟體取得所造成的差異。舉例而言，在機器學習的子領域——如擴散模型（diffusion modeling）、基於 transformer 的語言建模，以及學習動力學 Lu et al. [2024]——採用科學 AI 代理，受益於可公開取得、文件完善，且對許多相關問題而言規模與品質均屬適當的資料集，以及行之有年的基準測試。相形之下，基於 EEG 的 BCI 研究等領域，則面臨資料集規模小、異質性高且資料品質參差的更大問題。此外，EEG 訊號根植於多尺度腦動力學，及其與人類認知、情緒與行為之關聯的複雜性 Friston [2005]，也帶來額外的領域特定挑戰。
>
> [圖 1：針對腦機介面（BCI）與腦科學研究之 AI 驅動科學研究流程的概念示意圖，基於 Lu et al. [2024] 所提出的概念。與其概念不同，我們將數值結果與資料視覺化視為實驗迭代迴圈不可或缺的一部分。同樣與其全自主取徑不同，我們支持在循環的每個階段採取彈性的共享自主性（shared autonomy）層級（顏色編碼對應本文所報告之 BCI 專案範例；論文自動撰寫未在本研究中處理）]

---

> [!quote] Original
> Against this background, our objective in the present work is to adopt AI scientific agents to the field of human brain research and Brain-Computer Interfacing (BCI) Figure 1. To address the general as well as the domain-specific challenges of this task, we introduce a concept for human-AI workspaces designed to foster scientific human-AI co-learning and collaboration, summarized in our Janusian Design Principles. As an instance of this design concept, we present the LLM-based ChatBCI toolbox.
>
> We show how ChatBCI enabled a human-AI team to perform a prototypical scientific BCI project based on a public EEG dataset in a very time-efficient and productive manner, addressing the full cycle of project phases from data import, exploration, and validation, over machine learning model selection and implementation (a deep convolutional neural network as the LLM-generated brain signal decoder), setting up a training loop with integrated data augmentation, to the joint interpretation of the obtained results. We illustrate how our structured interaction framework flexibly supports different levels of agent autonomy and demonstrate how it allows transfer of expert knowledge about non-obvious, non-trivial aspects of the EEG dataset used, from the human expert to the ChatBCI AI agent. This, in turn, proved a valuable teaching and training partner for the human side. Thus, we achieved meaningful human-AI co-learning together with substantially faster collaborative completion of our BCI research task than would have been possible with a purely human approach. Our approach holds great potential for teaching and training in the field of human brain research and BCI development. LLM-generated brain signal decoders also present a novel class of AutoML for brain signal analysis. The "AI brain researcher" or "AI BCI researcher" presented here is an instance of an "AI worker" in general. It will be interesting to study how abilities learned to handle the complexities of the human brain, and of the signals that can be measured from it, may also be helpful for AI workers in other domains. We anticipate that collaborative human-AI research and development in the field of brain research and BCI, as enabled by our design concept and ChatBCI toolbox, will pave the way toward AI systems with a deep and holistic understanding of human brain function - a "brain-grokking AI" - with a far-reaching application potential.

> [!note] 翻譯
> 在此背景下，本研究的目標是將 AI 科學代理引入人腦研究與腦機介面（BCI）領域（圖 1）。為因應此任務的一般性與領域特定挑戰，我們提出一種旨在促進科學性人機共同學習（co-learning）與協作的人機工作空間概念，並將其總結為 Janusian 設計原則。作為此設計概念的一個實例，我們提出基於 LLM 的 ChatBCI 工具箱。
>
> 我們展示 ChatBCI 如何使人機團隊以極具時間效率且富有成效的方式，基於公開 EEG 資料集執行一項原型性的科學 BCI 專案，涵蓋專案各階段的完整循環：從資料匯入、探索與驗證，到機器學習模型的選擇與實作（以深度卷積神經網路作為 LLM 生成的腦訊號解碼器）、建立整合資料增強（data augmentation）的訓練迴圈，乃至對所得結果的共同詮釋。我們說明結構化互動框架如何彈性支援不同層級的代理自主性，並展示其如何使人類專家得以將所用 EEG 資料集中不明顯、非平凡面向的專家知識，轉移給 ChatBCI 的 AI 代理。反過來，AI 也證明是人類端寶貴的教學與訓練夥伴。因此，我們達成了有意義的人機共同學習，且協作完成 BCI 研究任務的速度遠快於純人類方式所能企及。我們的方法在人腦研究與 BCI 開發領域的教學與訓練上潛力可觀。LLM 生成的腦訊號解碼器亦構成腦訊號分析的一類新型 AutoML。本文提出的「AI 腦科學研究員」或「AI BCI 研究員」是廣義「AI 工作者」的一個實例。研究為處理人腦及其可量測訊號之複雜性所習得的能力，如何也能裨益其他領域的 AI 工作者，將是饒富興味的課題。我們預期，由我們的設計概念與 ChatBCI 工具箱所促成的腦研究與 BCI 領域人機協作研發，將為具備對人腦功能深刻而全面理解的 AI 系統——「洞澈大腦的 AI」（brain-grokking AI）——鋪路，其應用潛力深遠。

---

## 2 ChatBCI toolbox overview and Janusian Design Principles | ChatBCI 工具箱概觀與 Janusian 設計原則

> [!quote] Original
> The ChatBCI toolbox is fully implemented in Python, deep learning functionality is relying on PyTorch; in the present work we use GPT-4o as underlying LLM OpenAI [2024]. It comprises the following main components:
>
> - A collection of public EEG data sets in ChatBCI compatible format, including documented data validation
> - EEG and BCI knowledge base
> - Core functionality: EEG preprocessing, analysis, decoding models, training, interpretability, and visualization
> - Auxiliary components and supporting infrastructure, such as for generating summaries of data and code directory structures and contents at adjustable levels of granularity.
> - LLM communication tools.
>
> ChatBCI is by-design build to fostering human-AI collaboration (Figure 2). To this aim, we have defined a set of design principles which are the foundation of our general framework for human-AI teams. In the following we give a summary of our current working version of these principles.
>
> [Figure 2: In the Janusian Vision in designing human-AI workspaces, we embrace a dual-facing approach: one face directed toward empowering human expertise, and the other toward amplifying AI capabilities (DALL-E rendering of this idea)]

> [!note] 翻譯
> ChatBCI 工具箱完全以 Python 實作，深度學習功能依賴 PyTorch；本研究使用 GPT-4o 作為底層 LLM OpenAI [2024]。其主要組件如下：
>
> - 一組轉換為 ChatBCI 相容格式的公開 EEG 資料集，並附有文件化的資料驗證
> - EEG 與 BCI 知識庫
> - 核心功能：EEG 前處理、分析、解碼模型、訓練、可解釋性與視覺化
> - 輔助組件與支援性基礎設施，例如以可調整的顆粒度層級生成資料與程式碼目錄結構及內容的摘要
> - LLM 通訊工具。
>
> ChatBCI 在設計上即以促進人機協作為宗旨（圖 2）。為此，我們定義了一組設計原則，作為人機團隊通用框架的基礎。以下摘述這些原則目前的工作版本。
>
> [圖 2：在設計人機工作空間的 Janusian 願景中，我們採取雙面向取徑：一面朝向賦能人類專業，另一面朝向放大 AI 能力（此構想之 DALL-E 渲染圖）]

---

> [!quote] Original
> **Working version of the seven Principles of Janusian Design**
>
> 1. **Speaking the Same Language**: Janusian workspaces foster the development of a joint collaboration language, minimizing misunderstanding and confusion. This enables AI systems to act as intuitive partners, aligning with human intentions and promoting seamless interactions. To achieve this, the workspace leverages intuitive interfaces, contextual feedback, and iterative refinement, ensuring that communication between humans and AI becomes progressively more natural and effective.
> 2. **Transparency and Trust**: Trust requires mutual clarity and predictability. Explainability is a cornerstone, allowing users to progressively build confidence in AI systems by understanding their decision-making processes. The framework incorporates bidirectional transparency, encouraging not only explainable AI (XAI) methods, but also mechanisms for AI systems to ask clarifying questions when encountering ambiguous inputs or tasks. This fosters a collaborative dynamic where both human and AI agents seek to represent/understand each other's intentions, enhancing mutual trust and reducing the likelihood of errors.
> 3. **Building a Shared Knowledge Base**: The workspace amplifies human and AI collaboration by creating and maintaining a shared knowledge base. This repository encodes specialized expert knowledge and evolves iteratively. The Janusian Wiki (Jiki) serves as the foundation for this knowledge base, providing a structured, centralized system for documenting workflows, best practices, and domain-specific knowledge. By linking tasks, tools, and insights, the Jiki ensures that both human collaborators and AI systems have access to the collective intelligence of the workspace, facilitating informed decision-making and continuous improvement.

> [!note] 翻譯
> **Janusian 設計七原則之工作版本**
>
> 1. **說共同的語言（Speaking the Same Language）**：Janusian 工作空間促進共同協作語言的發展，將誤解與混淆減至最低。這使 AI 系統得以成為直覺的夥伴，與人類意圖對齊並促進無縫互動。為達此目標，工作空間運用直覺化介面、情境回饋與迭代精煉，確保人與 AI 之間的溝通日益自然且有效。
> 2. **透明與信任（Transparency and Trust）**：信任需要相互的清晰與可預測性。可解釋性是基石，使用者能藉由理解 AI 系統的決策過程，逐步建立對其的信心。此框架納入雙向透明性：不僅鼓勵可解釋 AI（explainable AI, XAI）方法，也鼓勵 AI 系統在遭遇模糊輸入或任務時提出澄清問題的機制。這促成一種協作動態，使人類與 AI 代理雙方皆致力於表徵／理解彼此的意圖，增進互信並降低錯誤發生的可能。
> 3. **建立共享知識庫（Building a Shared Knowledge Base）**：工作空間藉由建立並維護共享知識庫來放大人與 AI 的協作。此儲存庫編碼專門的專家知識並持續迭代演進。Janusian Wiki（Jiki）是此知識庫的基礎，提供結構化、集中式的系統，用以記錄工作流程、最佳實踐與領域特定知識。透過連結任務、工具與洞見，Jiki 確保人類協作者與 AI 系統皆能取用工作空間的集體智慧，促進知情決策與持續改進。

---

> [!quote] Original
> 4. **Joint Integration of Priorities**: The Janusian framework acknowledges that human-AI teams must balance and integrate priorities jointly, addressing trade-offs such as the need for immediate actions versus long-term objectives, or reconciling priorities driven by strategic considerations or overarching research questions ("top-down") with reactive, task-specific responses ("bottom-up").
> 5. **Adaptive Autonomy**: AI systems adjust their level of autonomy based on task demands, enabling granular human supervision where needed while offering higher degrees of independence for routine or low-stakes tasks. For example, in the ChatBCI toolbox, the adaptive autonomy principle is exemplified through Collaborative Test-Driven Development (TDD), where the AI autonomously generates test cases and mock data for routine validations, while human oversight is applied to critical or ambiguous scenarios. This flexibility ensures that the level of autonomy dynamically aligns with the complexity and importance of the task.
> 6. **Accessibility from Novice to Expert**: The workspace aims to balance accessibility for novice users, such as those in teaching and training scenarios, with maintaining advanced functionality tailored for expert users. It also differentiates how information from these scenarios is processed and utilized on the AI side, ensuring that insights from both novice interactions and expert workflows are effectively integrated. This inclusive design promotes efficiency and reduces the supervision workload, enabling seamless collaboration across varying levels of expertise.
> 7. **Continuous Evolution**: A shared Janusian workspace is not static, but designed to foster mutual co-learning between humans and AI. This principle ensures that the ChatBCI toolbox or other workspaces based by-design on this concept not only supports effective and scalable data analysis but also fosters a dynamic workspace where both human and AI partners can co-evolve to achieve aligned goals.
>
> In summary, the Janusian framework establishes a synergistic foundation for human-AI collaboration by aligning intuitive interfaces, transparent processes, and adaptive autonomy with evolving user needs. In the following, we illustrate how the ChatBCI toolbox based on these design ideas can be used in a concrete project example.

> [!note] 翻譯
> 4. **優先事項的共同整合（Joint Integration of Priorities）**：Janusian 框架承認人機團隊必須共同權衡並整合優先事項，處理諸如即時行動需求與長期目標之間的取捨，或調和由策略考量或總體研究問題驅動的優先事項（「由上而下」）與反應式、任務特定的回應（「由下而上」）。
> 5. **自適應自主性（Adaptive Autonomy）**：AI 系統依任務需求調整其自主程度，在需要時實現細緻的人類監督，同時對例行或低風險任務給予較高程度的獨立性。例如在 ChatBCI 工具箱中，自適應自主性原則體現於協作式測試驅動開發（Test-Driven Development, TDD）：AI 自主生成例行驗證所需的測試案例與模擬資料，而關鍵或含糊情境則施以人類監督。此彈性確保自主程度與任務的複雜度及重要性動態對齊。
> 6. **從新手到專家的可及性（Accessibility from Novice to Expert）**：工作空間力求在新手使用者（例如教學與訓練情境中的使用者）的可及性，與為專家使用者量身打造的進階功能之間取得平衡。它亦區分這些情境的資訊在 AI 端如何被處理與運用，確保來自新手互動與專家工作流程的洞見皆獲有效整合。此包容性設計提升效率並減輕監督負擔，使不同專業程度之間的協作得以無縫進行。
> 7. **持續演化（Continuous Evolution）**：共享的 Janusian 工作空間並非靜態，而是設計來促進人與 AI 之間的相互共同學習。此原則確保 ChatBCI 工具箱或其他以此概念為設計基礎的工作空間，不僅支援有效且可擴展的資料分析，也培育一個人類與 AI 夥伴得以共同演化、達成一致目標的動態工作空間。
>
> 總結而言，Janusian 框架藉由使直覺化介面、透明流程與自適應自主性對齊於不斷演變的使用者需求，為人機協作奠定協同增效的基礎。以下我們透過一個具體專案範例，說明基於這些設計理念的 ChatBCI 工具箱如何使用。

---

## 3 Analyzing the BCI competition IV2a dataset using ChatBCI | 使用 ChatBCI 分析 BCI Competition IV 2a 資料集

> [!quote] Original
> As illustrated in Figure 1, we propose a conceptual framework for AI-driven scientific research processes tailored to Brain-Computer Interfacing (BCI) and brain research. This concept builds upon the approach introduced by Lu et al. [2024], which emphasizes fully autonomous AI systems executing all phases of the research cycle. In contrast, our approach highlights the importance of integrating numerical results and data visualization as essential elements within the experimental iteration loop. Furthermore, instead of aiming for complete autonomy, we advocate for flexible levels of shared autonomy, enabling human-AI collaboration to be tailored dynamically for each phase of the cycle. The color-coded segments in Figure 1 correspond to the phases of an example BCI project reported here, with the exception of automatic paper write-up, which was not addressed in this work.
>
> In the following sections, we will structure the the main part of the manuscript along the schematic depicted in Figure 1, detailing each phase of the research process and how human-AI collaboration is implemented to maximize efficiency, adaptability, and the transfer of expertise.

> [!note] 翻譯
> 如圖 1 所示，我們提出一個為腦機介面（BCI）與腦科學研究量身打造的 AI 驅動科學研究流程概念框架。此概念建立於 Lu et al. [2024] 所提出的方法之上——該方法強調由全自主 AI 系統執行研究循環的所有階段。相對地，我們的方法強調將數值結果與資料視覺化整合為實驗迭代迴圈中的關鍵要素。此外，我們不以完全自主為目標，而是提倡彈性的共享自主性層級，使人機協作得以針對循環的每個階段動態調整。圖 1 中以顏色編碼的區段對應本文所報告之 BCI 專案範例的各階段，惟論文自動撰寫除外，本研究未處理該項。
>
> 在後續各節中，我們將依循圖 1 所示的示意圖來組織本文的主體部分，詳述研究流程的每個階段，以及人機協作如何實作以最大化效率、適應性與專業知識的轉移。

---

### 3.1 Idea generation | 想法生成

> [!quote] Original
> The research ideas presented in Table 1 were generated using GPT-4o, as our underlying LLM. The model was provided with a detailed prompt to explore advancements in EEG-based motor imagery classification within the context of Brain-Computer Interfaces (BCIs) using the BCI Competition IV 2a dataset Brunner et al. [2008]. We decided to use this specific dataset because it has been widely used, maybe the closest to a "MNIST of EEG", providing a rich background of published results.
>
> The prompt included instructions to identify key challenges, research gaps, motivations, and potential approaches across a range of topics, including domain adaptation, lightweight architectures, temporal dynamics, and explainability. GPT-4o synthesized the list of ideas by leveraging its training on a wide range of scientific and technical texts; notably here we did not fine-tune on more specific literature. Overall, this demonstrates the utility of LLMs in assisting researchers by rapidly generating diverse, structured, and actionable ideas that may have merit in guiding future investigations. Notably, the generated research ideas included indeed notoriously under-researched yet important topics such as the role of data preprocessing (c.f. example 10 in 1). On the other hand, an expert in the field in a single glance can find that all generated research questions have already been addressed by the research community (Peterson et al. [2019], Meng et al. [2023], Li et al. [2022], Jin et al. [2022], Kostas et al. [2021], Kasahara et al. [2015], Echtioui et al. [2021], Saha and Baumert [2020], Ju et al. [2020], Feng et al. [2020], Kessler et al. [2024], Duan et al. [2020], Ferrez and Millán [2008]). Therefore, the introduction of iterative refinement in the process of idea generation (e.g. like chain of thoughts Lu et al. [2024]) and tools for novelty verification (through Semantic Scholar API) and quantification are necessary. Clearly, the scientific ideas generation stage itself could also benefit from more sophisticated approaches. For instance, leveraging creative templates grounded in evolutionary computation and open-endedness principles offers significant potential. These approaches, as exemplified in works by Lu et al. [2024] and Faldor et al. [2024], use iterative, mutation-driven frameworks to refine and evolve novel ideas, mimicking the exploratory processes of natural evolution. Furthermore, combining knowledge graphs with LLM, as demonstrated by Gu and Krenn [2024], allows for the synthesis of diverse interdisciplinary ideas. Another promising approach is curiosity-driven learning, as proposed by Pathak et al. [2017], which emphasizes exploring the unknown and prioritizing novelty over immediate utility.

> [!note] 翻譯
> 表 1 所列的研究想法係以我們的底層 LLM——GPT-4o 生成。我們向模型提供了詳細的提示（prompt），要求其在腦機介面（BCI）脈絡下，運用 BCI Competition IV 2a 資料集 Brunner et al. [2008] 探索基於 EEG 之運動想像分類的進展。我們選擇此特定資料集，是因為它已被廣泛使用——或許是最接近「EEG 界的 MNIST」的資料集——提供了豐富的已發表結果背景。
>
> 提示中包含的指令，要求在領域自適應（domain adaptation）、輕量化架構、時間動態與可解釋性等一系列主題中，辨識關鍵挑戰、研究缺口、動機與潛在方法。GPT-4o 藉由其在廣泛科學與技術文本上的訓練，綜合出這份想法清單；值得注意的是，我們並未針對更特定的文獻進行微調（fine-tune）。整體而言，這展示了 LLM 協助研究者的效用：快速生成多樣、結構化且可操作的想法，對引導未來研究可能具有價值。值得注意的是，生成的研究想法確實包含了長期研究不足卻十分重要的主題，例如資料前處理的角色（參見表 1 之例 10）。另一方面，領域專家一眼即可發現，所有生成的研究問題其實均已被研究社群處理過（Peterson et al. [2019]、Meng et al. [2023]、Li et al. [2022]、Jin et al. [2022]、Kostas et al. [2021]、Kasahara et al. [2015]、Echtioui et al. [2021]、Saha and Baumert [2020]、Ju et al. [2020]、Feng et al. [2020]、Kessler et al. [2024]、Duan et al. [2020]、Ferrez and Millán [2008]）。因此，有必要在想法生成過程中引入迭代精煉（例如類似思維鏈（chain of thoughts）的做法 Lu et al. [2024]），以及新穎性驗證（透過 Semantic Scholar API）與量化的工具。顯然，科學想法生成階段本身也能受益於更精緻的方法。例如，運用根植於演化計算（evolutionary computation）與開放式演化（open-endedness）原則的創意模板，潛力可觀。這些方法——如 Lu et al. [2024] 與 Faldor et al. [2024] 的工作所示——使用迭代、突變驅動的框架來精煉並演化新穎想法，模仿自然演化的探索過程。此外，如 Gu and Krenn [2024] 所展示，將知識圖譜（knowledge graph）與 LLM 結合，可綜合多元的跨領域想法。另一個有前景的方法是 Pathak et al. [2017] 提出的好奇心驅動學習（curiosity-driven learning），其強調探索未知，並將新穎性置於即時效用之上。

---

> [!quote] Original
> However, it was also observable that many of the proposed topics were under-specified and lacked exact knowledge of what has already been done and reported in the relevant literature. For example, the suggestion was to explore lightweight neural networks that achieve state-of-the-art accuracy while being optimized for low-power devices, which is a promising topic, but one that is already well-trodden in the field. Studies like the widely known EEGNet paper Lawhern et al. [2018] have specifically addressed this challenge by designing compact, efficient neural networks tailored to EEG data. Therefore, while this idea aligns with key goals in the field, it lacks specificity in identifying novel directions. For instance, future advancements might focus on extending lightweight models to handle more diverse datasets, integrating adaptive techniques that dynamically scale complexity based on resource availability, or innovating in model interpretability for constrained devices. Without domain-specific fine-tuning, the model had also difficulties in retrieving the published SoA in decoding accuracies on the BCI Competition IV 2a dataset. While a re-implementation of the FBCSP winning model of the original BCI Competition IV yielded 67.8% accuracy across the 4 motor imagery classes, and convnets allowed for a gain of approx. 4% Schirrmeister et al. [2017]. In the meanwhile, accuracies close to 100% have been reported (e.g., 97.61% in Xie and Oniga [2023]).

> [!note] 翻譯
> 然而，同樣可以觀察到，許多被提出的主題界定不足，且缺乏對相關文獻中已完成與已報告工作的確切掌握。例如，模型建議探索在低功耗裝置上最佳化、同時達到最先進（state-of-the-art）準確率的輕量化神經網路——這雖是有前景的主題，卻已是該領域的老生常談。諸如廣為人知的 EEGNet 論文 Lawhern et al. [2018] 等研究，即已透過設計專為 EEG 資料打造的精簡高效神經網路來因應此挑戰。因此，此想法雖與該領域的關鍵目標一致，卻缺乏辨識新穎方向的特定性。舉例而言，未來的進展或可著眼於擴展輕量化模型以處理更多樣的資料集、整合依資源可得性動態調整複雜度的自適應技術，或在受限裝置的模型可解釋性上創新。在缺乏領域特定微調的情況下，模型也難以檢索 BCI Competition IV 2a 資料集上已發表的最先進解碼準確率。原始 BCI Competition IV 冠軍模型 FBCSP 的重新實作在 4 個運動想像類別上取得 67.8% 準確率，而卷積網路（convnets）帶來約 4% 的提升 Schirrmeister et al. [2017]。與此同時，文獻中已有接近 100% 的準確率報告（例如 Xie and Oniga [2023] 的 97.61%）。

---

> [!quote] Original
> Table 1: Generated research questions, gaps, motivations, and approaches for advancing EEG-based motor imagery classification (10 consecutively generated examples).
>
> | ID | Research Question | Gap | Motivation | Approach |
> |----|-------------------|-----|------------|----------|
> | 1 | What are the optimal EEG frequency bands for decoding, and how do they vary across subjects? | Inconsistent findings on band contributions. | Personalization can improve performance. | Perform detailed frequency band analysis. |
> | 2 | How can adversarial robustness techniques improve the reliability of EEG classifiers? | Limited research on adversarial robustness in BCIs. | Reliable BCIs are essential for sensitive domains. | Simulate adversarial attacks and develop defenses. |
> | 3 | Can graph-based representations of EEG signals improve motor imagery classification? | EEG relationships are underutilized in flat feature models. | Graphs can capture spatial-temporal relationships. | Use GNNs and evaluate performance. |
> | 4 | How can self-supervised learning reduce the need for labeled data in EEG decoding? | Labeled data is scarce and expensive. | Self-supervised methods can leverage large unlabeled datasets. | Apply techniques like contrastive learning and test results. |
> | 5 | What role does individual variability in brain anatomy play in decoding motor imagery? | EEG decoding often assumes uniformity across individuals. | Personalized BCIs can improve accuracy. | Analyze variability and develop normalization strategies. |
> | 6 | How can dynamic ensemble methods improve the robustness and accuracy of motor imagery classification? | Static ensembles do not adapt to data properties. | Dynamic ensembles can tailor predictions to the data. | Develop adaptive ensemble methods and test performance. |
> | 7 | What is the impact of session-to-session variability, and how can it be mitigated? | Session variability affects model performance. | Reliable BCIs need to function consistently over time. | Apply adaptation techniques and evaluate performance improvements. |
> | 8 | Can federated learning improve decoding while preserving privacy? | Most approaches require centralized data. | Privacy-preserving training can enable collaborative BCIs. | Implement federated learning frameworks and test cross-subject models. |
> | 9 | How can cross-frequency coupling (CFC) features improve decoding accuracy? | CFC is underexplored in motor imagery. | CFC can reveal richer brain dynamics. | Extract CFC features and integrate into models. |
> | 10 | What are the effects of different EEG preprocessing pipelines on decoding performance? | No consensus on the best preprocessing pipeline. | Standardization can improve reproducibility. | Compare pipelines and evaluate their effects. |
> | 11 | Can few-shot learning enable accurate decoding with minimal training data? | Most models require substantial data, impractical for new users. | Few-shot learning reduces the burden of data collection. | Implement few-shot methods like prototypical networks. |
> | 12 | How can real-time feedback loops improve motor imagery classification during online experiments? | Feedback is minimally studied in offline datasets. | Real-time feedback could help users refine mental strategies. | Simulate real-time feedback and test its impact on decoding. |

> [!note] 翻譯
> 表 1：為推進基於 EEG 之運動想像分類所生成的研究問題、缺口、動機與方法（連續生成的範例）。
>
> | ID | 研究問題 | 缺口 | 動機 | 方法 |
> |----|---------|------|------|------|
> | 1 | 解碼的最佳 EEG 頻帶為何？其如何因受試者而異？ | 各頻帶貢獻的研究結果不一致。 | 個人化可提升效能。 | 進行詳細的頻帶分析。 |
> | 2 | 對抗魯棒性（adversarial robustness）技術如何提升 EEG 分類器的可靠性？ | BCI 領域的對抗魯棒性研究有限。 | 可靠的 BCI 對敏感應用領域至關重要。 | 模擬對抗攻擊並發展防禦方法。 |
> | 3 | 基於圖（graph-based）的 EEG 訊號表徵能否改善運動想像分類？ | 平坦特徵模型未充分利用 EEG 的關聯結構。 | 圖可捕捉空間—時間關係。 | 使用圖神經網路（GNN）並評估效能。 |
> | 4 | 自監督學習（self-supervised learning）如何降低 EEG 解碼對標註資料的需求？ | 標註資料稀缺且昂貴。 | 自監督方法可利用大量未標註資料。 | 應用對比學習（contrastive learning）等技術並檢驗結果。 |
> | 5 | 腦部解剖構造的個體差異在運動想像解碼中扮演何種角色？ | EEG 解碼常假設個體間具一致性。 | 個人化 BCI 可提升準確率。 | 分析變異性並發展正規化策略。 |
> | 6 | 動態集成（dynamic ensemble）方法如何提升運動想像分類的魯棒性與準確率？ | 靜態集成無法適應資料特性。 | 動態集成可針對資料量身打造預測。 | 發展自適應集成方法並測試效能。 |
> | 7 | 跨場次（session-to-session）變異性的影響為何？如何緩解？ | 場次變異性影響模型效能。 | 可靠的 BCI 須能長期穩定運作。 | 應用適應技術並評估效能改善。 |
> | 8 | 聯邦學習（federated learning）能否在保護隱私的同時改善解碼？ | 多數方法需要集中式資料。 | 隱私保護訓練可促成協作式 BCI。 | 實作聯邦學習框架並測試跨受試者模型。 |
> | 9 | 跨頻耦合（cross-frequency coupling, CFC）特徵如何提升解碼準確率？ | CFC 在運動想像中的探索不足。 | CFC 可揭示更豐富的腦動態。 | 萃取 CFC 特徵並整合至模型。 |
> | 10 | 不同 EEG 前處理管線對解碼效能有何影響？ | 對最佳前處理管線尚無共識。 | 標準化可提升可重現性。 | 比較各管線並評估其效果。 |
> | 11 | 少樣本學習（few-shot learning）能否以極少訓練資料實現準確解碼？ | 多數模型需要大量資料，對新使用者不切實際。 | 少樣本學習可減輕資料蒐集負擔。 | 實作原型網路（prototypical networks）等少樣本方法。 |
> | 12 | 即時回饋迴圈如何在線上實驗中改善運動想像分類？ | 離線資料集中對回饋的研究極少。 | 即時回饋有助於使用者精進心智策略。 | 模擬即時回饋並測試其對解碼的影響。 |

---

> [!quote] Original
> On this background, we decided for the following, human-specified topic for the first ChatBCI project: What do the close to 100% decoding accuracies reported on the BCI Competition IV 2a dataset mean? To which extent do they reflect sophistication of the brain signal decoding methodology, or possibly also over-fitting to this (relatively small) dataset, and/or contributions from non-brain signals (artifacts which may contain class-specific information and might be easier to decode than brain signals)? The BCI Competition IV 2a data set is particularly interesting in this context, as it was part of the original competition instructions that since three EOG channels were provided, it was "required to remove EOG artifacts before the subsequent data processing using artifact removal techniques such as highpass filtering or linear regression" Brunner et al. [2008]. The winning entry of the competition and based on their approach, also subsequent works on the same data set used a 4-Hz high-pass filter to address this requirement Ang et al. [2012] Tangermann et al. [2012] Schirrmeister et al. [2017].
>
> Thus, for our first ChatBCI project, we set two objectives:
>
> - Perform an exploratory data analysis of the BCI Competition IV 2a data set
> - Design, implement and test a convolutional neural network (CNN) decoder with a good tradeoff of simplicity and hence interpretability on the one side, and meaningful decoding results in terms of decoding accuracies on the other, including a corresponding within-subject training loop.
>
> We addressed these objectives using the ChatBCI toolbox in a human-AI collaborative framework (Stage 2 "Experiment Iteration" according to our generic research cycle plan 1).

> [!note] 翻譯
> 基於上述背景，我們為第一個 ChatBCI 專案選定了以下由人類指定的主題：BCI Competition IV 2a 資料集上所報告之接近 100% 的解碼準確率究竟意味著什麼？它們在多大程度上反映腦訊號解碼方法的精進，抑或可能也包含對此（相對較小的）資料集的過度擬合（over-fitting），及／或非腦訊號的貢獻（偽跡（artifacts）可能含有類別特異的資訊，且可能比腦訊號更容易解碼）？在此脈絡下，BCI Competition IV 2a 資料集格外值得關注，因為原始競賽指示即載明：既然提供了三個 EOG（眼電圖）通道，參賽者「必須在後續資料處理之前，使用諸如高通濾波或線性迴歸等偽跡去除技術移除 EOG 偽跡」Brunner et al. [2008]。競賽的冠軍隊伍——以及以其方法為基礎、在同一資料集上的後續研究——皆使用 4-Hz 高通濾波器來滿足此要求 Ang et al. [2012]、Tangermann et al. [2012]、Schirrmeister et al. [2017]。
>
> 因此，我們為第一個 ChatBCI 專案設定了兩項目標：
>
> - 對 BCI Competition IV 2a 資料集執行探索性資料分析
> - 設計、實作並測試一個卷積神經網路（CNN）解碼器，在「簡潔性（因而具可解釋性）」與「解碼準確率上有意義的結果」之間取得良好折衷，並包含對應的受試者內（within-subject）訓練迴圈。
>
> 我們在人機協作框架下，使用 ChatBCI 工具箱處理這些目標（依我們的通用研究循環規劃（圖 1），屬第二階段「實驗迭代」）。

---

### 3.2 Experimental iteration | 實驗迭代

> [!quote] Original
> Basic steps such as such data epoching, re-referencing, or filtering could be conveniently implemented, typically with a few prompts per step. The key functionality of the ChatBCI toolbox enabling this smooth workflow is the knowledge base with the relevant background knowledge and specific instructions, provided at different levels of detail and aggregation, such that the relevant information can be efficiently kept in the LLM context.
>
> More fine-grained instructions were (as expected) needed for data visualization. Figures 3, 4 (A and B) and 5 are collaboratively ChatBCI-generated. Code generation and running the experiments were the two steps with the highest level of AI autonomy (see Fig. 1)

> [!note] 翻譯
> 資料切段（epoching）、重參考（re-referencing）或濾波等基本步驟皆可便捷地實作，通常每個步驟只需數個提示。使 ChatBCI 工具箱得以支撐此流暢工作流程的關鍵功能，是其知識庫——以不同的細節與彙整層級提供相關背景知識與特定指示，使相關資訊能有效率地保留於 LLM 的上下文（context）之中。
>
> 資料視覺化則（一如預期）需要更細緻的指示。圖 3、圖 4（A 與 B）及圖 5 皆為 ChatBCI 協作生成。程式碼生成與實驗執行是 AI 自主程度最高的兩個步驟（見圖 1）。

---

#### 3.2.1 Exploratory data analysis and data validation | 探索性資料分析與資料驗證

> [!quote] Original
> Analysis for data exploration and validation let to several interesting observations. ChatBCI suggested initially three starting steps for data validation and exploration. First, examine the basic signal statistics to understand the distribution and variability of the EEG data across classes. This involves calculating the mean, standard deviation, and variance for each channel and class, identifying systematic differences in signal variance, and detecting outliers, which may indicate artefacts. Second, analyze event-related potentials (ERPs) to investigate time-locked neural activity by segmenting the data around key events, calculating average ERP waveforms for each class, and identifying distinctive features such as P300 peaks while ensuring the observed differences are neural rather than artefact-related. Third, explore class-specific differences in the frequency domain by computing power spectral density (PSD) for each class and channel, identifying frequency bands where power varies, and detecting potential artefacts such as line noise or muscle activity.
>
> [Figure 3: ERP waveforms across all trials of all subjects' training data. Trial timing: The cue in the form of an arrow pointing either to the left, right, down or up, corresponding to one of the four classes left hand, right hand, foot or tongue) appeared and stayed on the screen for the duration indicated by the black box (1.25 s). Grey box: Time window of fixation cross presentation.]
>
> The questions whether we can indeed expect a P300 as the dominating effect in the ERPs based on this dataset, and what we can expect, revealed a mixture of accurate and wrong assumptions (e.g., correctly expecting visually and auditory-evoked potentials (VEPs, AEPs) elecited by the respective cues, but also expecting a readiness potential which in fact is associated with self-paced tasks Ball et al. [1999]), illustrating the necessity of expert knowledge transfer. ERP results (EEG common average rereferenced, 40 Hz low-pass filtered) are shown in Fig. 3, for 5 EEG and 2 EOG channels.

> [!note] 翻譯
> 資料探索與驗證的分析帶來了數項有趣的觀察。ChatBCI 起初建議了三個資料驗證與探索的起始步驟。第一，檢視基本訊號統計量，以理解 EEG 資料在各類別間的分布與變異性。這包括計算每個通道與類別的平均值、標準差與變異數，辨識訊號變異數的系統性差異，並偵測可能顯示偽跡的離群值。第二，分析事件相關電位（event-related potentials, ERPs），透過在關鍵事件前後切分資料、計算各類別的平均 ERP 波形，並辨識諸如 P300 峰等特徵性成分，同時確保觀察到的差異來自神經活動而非偽跡，以探究時間鎖定（time-locked）的神經活動。第三，在頻域探索類別特異的差異：計算各類別與通道的功率譜密度（power spectral density, PSD），辨識功率有所變化的頻帶，並偵測諸如電源線雜訊或肌肉活動等潛在偽跡。
>
> [圖 3：所有受試者訓練資料所有試次（trials）的 ERP 波形。試次時序：以箭頭形式呈現的提示（cue）指向左、右、下或上，分別對應左手、右手、腳或舌頭四類之一；箭頭出現並停留於螢幕上的時間長度以黑框標示（1.25 秒）。灰框：注視十字（fixation cross）呈現的時間窗。]
>
> 針對「基於此資料集，我們是否確實可以預期 P300 是 ERP 中的主導效應，以及我們可以預期什麼」的提問，揭示了正確與錯誤假設的混合（例如：正確地預期由相應提示誘發的視覺與聽覺誘發電位（VEPs, AEPs），但也預期了準備電位（readiness potential）——後者實際上與自主步調（self-paced）任務相關 Ball et al. [1999]），這說明了專家知識轉移的必要性。ERP 結果（EEG 經共同平均重參考、40 Hz 低通濾波）如圖 3 所示，涵蓋 5 個 EEG 通道與 2 個 EOG 通道。

---

> [!quote] Original
> Next, we asked for an interpretation of this plot with the prompt "this figure shows plots of the ERPs (mean across all trials of all subjects for each of the 4 classes). What do we see here, how can we interpret this result?" with the response which we fully reproduce here (in blue font):
>
> "The provided figure shows event-related potentials (ERPs) averaged across all trials and subjects for each of the 4 motor imagery classes (tongue, both feet, right hand, and left hand). Each subplot corresponds to a different electrode or channel (e.g., EOG1, Fz, C3, Cz, C4, Pz), with voltage plotted as a function of time (0–4000 ms).
>
> Key Observations
> 1. Cue-Evoked Potentials:
> - For channels like Fz, Cz, and Pz, there is a clear deflection around 200–300 ms after the cue onset, which could represent the early processing of the cue. This is consistent with components such as N1/P2 (cue-related processing).
> - These components are visible across all classes, indicating that these potentials are not class-specific but instead reflect the shared cognitive processing of the presented cue.
>
> 2. Motor-Related Activity:
> - Channels C3, Cz, and C4 are primarily associated with motor areas:
>   - For C3 (left motor cortex), the right hand class shows a small deviation compared to other classes, consistent with contralateral activity.
>   - For C4 (right motor cortex), the left hand class exhibits a subtle difference, though less pronounced.
> - Both feet and tongue motor imagery do not show distinct deflections in these motor channels, likely due to their bilateral or non-cortical representation.

> [!note] 翻譯
> 接著，我們以如下提示要求對此圖進行詮釋：「此圖顯示 ERP 的曲線（4 個類別中每一類、所有受試者所有試次的平均）。我們在此看到什麼？該如何詮釋此結果？」以下完整重現其回應（原文以藍色字體標示）：
>
> 「所提供的圖顯示 4 個運動想像類別（舌頭、雙腳、右手與左手）各自跨所有試次與受試者平均的事件相關電位（ERPs）。每個子圖對應不同的電極或通道（如 EOG1、Fz、C3、Cz、C4、Pz），縱軸為電壓、橫軸為時間（0–4000 毫秒）。
>
> 主要觀察
> 1. 提示誘發電位（Cue-Evoked Potentials）：
> - 在 Fz、Cz 與 Pz 等通道，提示出現後約 200–300 毫秒有明顯的偏轉，可能代表對提示的早期處理。這與 N1/P2 等成分（提示相關處理）一致。
> - 這些成分在所有類別中皆可見，顯示這些電位並非類別特異，而是反映對所呈現提示的共同認知處理。
>
> 2. 運動相關活動（Motor-Related Activity）：
> - C3、Cz 與 C4 通道主要與運動區相關：
>   - 在 C3（左側運動皮質），右手類別相較其他類別呈現小幅偏移，與對側（contralateral）活動一致。
>   - 在 C4（右側運動皮質），左手類別呈現細微差異，惟較不明顯。
> - 雙腳與舌頭運動想像在這些運動通道未顯示明確偏轉，可能因其表徵為雙側或非皮質性所致。

---

> [!quote] Original
> 3. Eye Movement Artefacts:
> - EOG1 and EOG3 channels clearly show larger deviations, especially for the tongue and both feet classes. This suggests that these classes may involve stronger or unintended eye movement artefacts, particularly in the early time window (0–1000 ms).
> - These artefacts could partially overlap with class-specific ERP components and may need to be addressed (e.g., through filtering or artifact rejection).
>
> 4. Overall Class Differences:
> - While class-specific differences are subtle, the right hand and left hand classes show slight distinctions in the C3 and C4 channels, corresponding to their expected motor areas.
> - The both feet and tongue classes show fewer clear class-specific ERP features, and their differences may instead emerge more clearly in the frequency domain (e.g., mu/beta desynchronization).
>
> Interpretation
> - Cue-Related Potentials: These are robust across all classes, primarily seen in midline channels (e.g., Fz, Cz, Pz), reflecting the shared processing of visual stimuli.
> - Motor-Specific Activity: Subtle differences in the motor-related channels (C3, C4) align with the expected contralateral activation for hand motor imagery tasks.
> - Artefact Contribution: Large deviations in EOG channels suggest that some ERPs might be influenced by eye movement artefacts, particularly for classes like tongue and both feet."

> [!note] 翻譯
> 3. 眼動偽跡（Eye Movement Artefacts）：
> - EOG1 與 EOG3 通道明顯呈現較大偏移，尤以舌頭與雙腳類別為甚。這暗示這些類別可能涉及較強或非意圖的眼動偽跡，特別是在早期時間窗（0–1000 毫秒）。
> - 這些偽跡可能與類別特異的 ERP 成分部分重疊，或許需要加以處理（例如透過濾波或偽跡剔除）。
>
> 4. 整體類別差異：
> - 雖然類別特異差異細微，右手與左手類別在 C3 與 C4 通道呈現些微區別，對應其預期的運動區。
> - 雙腳與舌頭類別的類別特異 ERP 特徵較不明顯，其差異或許會在頻域中更清楚地浮現（例如 mu/beta 去同步化（desynchronization））。
>
> 詮釋
> - 提示相關電位：在所有類別中皆穩健存在，主要見於中線通道（如 Fz、Cz、Pz），反映對視覺刺激的共同處理。
> - 運動特異活動：運動相關通道（C3、C4）的細微差異，與手部運動想像任務預期的對側激活一致。
> - 偽跡貢獻：EOG 通道的大幅偏移暗示部分 ERP 可能受眼動偽跡影響，尤以舌頭與雙腳等類別為然。」

---

> [!quote] Original
> This interpretation addresses many of the relevant aspects and in our opinion demonstrates a very promising starting point for a useful visual understanding of results in the EEG and BCI research context of. The three major signal sources that need to be considered (cue-evoked, movement-related, and ocular artifacts) are correctly identified and discussed, with several valid points such as that class differences can be expected to be clearer in the frequency domain. However, the conclusion that ocular effects mainly show in the tongue and feet movement classes is not likely given the observations that can here be made. The direction of the arrows used as cues together with the placement of the EOG channels (see Fig. 4 (C)) suggest that subjects reacted to visual cue presentation with a blink-saccade towards the tip of the different presented arrows; see also Fig. 4 (A). The fast transients at the onset of the presumed blink-saccade movement survived a 4-Hz high-pass filter (Fig. 4 (B)).
>
> [Figure 4: (A) Zoom-in from Fig. 3. (B) Same with 4-Hz high-pass filter. (C) Direction of the arrows used as cues for the 4 classes and colored corresponding to (A) and (B); placement of the EOG1 and EOG3 channels as described in Brunner et al. [2008].]
>
> In summary, already in its current early stage, ChatBCI proved highly useful for a practical example of a EEG data exploration, allowing for a substantially accelerated workflow. Analyzing a widely used motor imagery data set turned out to be a rich "learning opportunity" for ChatBCI (exemplarily showing that cues may induce class-specific effects, how to detect differential eye movements, etc.). At the current stage, our solution to make such learning experiences persistent is to include corresponding text documents in the ChatBCI knowledge base. In future versions, this approach may be supplemented by fine-tuning the underlying language models, and by equipping models with memory Wolters et al. [2024] Packer et al. [2024].
>
> One next step from here would be to check the published BCI literature to which extend our observations have already been reported, and to this end, implementing abilities for automated literature search and aggregation in ChatBCI. The results so far also support our initial choice of research questions and objectives (see Section 3.1). In the following, we briefly describe the first step with respect to Objective 2, design, implement and test a CNN EEG decoder.

> [!note] 翻譯
> 此詮釋觸及許多相關面向，我們認為它展現了在 EEG 與 BCI 研究脈絡中，對結果進行有用視覺理解的一個非常有前景的起點。需要考慮的三大訊號來源（提示誘發、運動相關與眼動偽跡）均獲正確辨識與討論，並提出若干有效論點，例如類別差異預期在頻域中會更清楚。然而，「眼動效應主要出現在舌頭與腳部類別」的結論，就此處可得的觀察而言並不可信。作為提示的箭頭方向，加上 EOG 通道的擺放位置（見圖 4 (C)），暗示受試者對視覺提示的反應是朝所呈現各箭頭尖端方向的眨眼—掃視（blink-saccade）；另見圖 4 (A)。推定之眨眼—掃視動作起始處的快速暫態，通過了 4-Hz 高通濾波器而殘留（圖 4 (B)）。
>
> [圖 4：(A) 圖 3 的局部放大。(B) 同圖，但施加 4-Hz 高通濾波。(C) 4 個類別作為提示之箭頭方向，顏色對應 (A) 與 (B)；EOG1 與 EOG3 通道的擺放位置如 Brunner et al. [2008] 所述。]
>
> 總結而言，即使處於目前的早期階段，ChatBCI 已在一個 EEG 資料探索的實務範例中證明高度有用，使工作流程大幅加速。分析一個被廣泛使用的運動想像資料集，對 ChatBCI 而言成為豐富的「學習機會」（例如展示提示可能誘發類別特異效應、如何偵測差異性眼動等）。在現階段，我們讓此類學習經驗得以持存的解決方案，是將相應的文字文件納入 ChatBCI 知識庫。在未來版本中，此做法或可輔以底層語言模型的微調，以及為模型配備記憶（memory）Wolters et al. [2024]、Packer et al. [2024]。
>
> 由此出發的下一步，將是查核已發表的 BCI 文獻，以確認我們的觀察在多大程度上已被報告；為此，需在 ChatBCI 中實作自動化文獻搜尋與彙整的能力。目前為止的結果也支持我們最初對研究問題與目標的選擇（見第 3.1 節）。以下我們簡述針對目標二——設計、實作並測試 CNN EEG 解碼器——的第一步。

---

#### 3.2.2 LLM-generated deep neural network decoder and training loop | LLM 生成之深度神經網路解碼器與訓練迴圈

> [!quote] Original
> Not surprisingly, given the well-documented abilities of LLMs in code generation (Rozière et al. [2024]), ChatBCI was able to design and implement both deep networks for EEG decoding and working training loops, including plausibly motivated data-augmentation strategies. Figure 5 shows results obtained with a simple network consisting of a sequence of spatial and temporal convolutions, using batch normalization, dropout, and a SWISH activation function, with within-subject training using the original competition data split.
>
> Results showed clear learning on the validation set across all subjects, reaching accuracies, on the one hand, clearly above chance, and on the other hand, also substantially below previous reports Zhang et al. [2021]. We consider our simple models as a good starting point for, in a next step, implementing interpretability tools, and gradually moving to more complex decoding models. Setting up the network as well as the training loop used in this example, in total required less than 10 prompts.
>
> Another next logical step would be the implementation of hyperparameter optimization and neural architecture search functionality in ChatBCI, for advancing to models with SoA accuracy or beyond. Note that this would potentially enable novel approaches for AutoML leveraging LLMs, without the necessity for expert knowledge of underlying AutoML libraries and toolboxes. We foresee that ChatBCI would also be particularly useful in BCI decoding benchmarking tasks, which require structured large-scale approaches which may especially align with the specific strengths of artificial compared to human intelligence.

> [!note] 翻譯
> 鑑於 LLM 在程式碼生成方面已有充分文獻記載的能力（Rozière et al. [2024]），不出所料，ChatBCI 能夠設計並實作用於 EEG 解碼的深度網路以及可運作的訓練迴圈，包括動機合理的資料增強策略。圖 5 顯示以一個簡單網路取得的結果：該網路由一連串空間與時間卷積組成，使用批次正規化（batch normalization）、dropout 與 SWISH 激活函數，並採用原始競賽的資料切分進行受試者內訓練。
>
> 結果顯示，所有受試者在驗證集上皆有明確的學習，達到的準確率一方面明顯高於隨機水準（chance level），另一方面也遠低於先前的報告 Zhang et al. [2021]。我們認為這些簡單模型是良好的起點，下一步可在其上實作可解釋性工具，並逐步邁向更複雜的解碼模型。本例中網路與訓練迴圈的建置，總計所需提示不到 10 個。
>
> 另一個合乎邏輯的下一步，是在 ChatBCI 中實作超參數最佳化（hyperparameter optimization）與神經架構搜尋（neural architecture search）功能，以推進至具最先進準確率或更佳的模型。值得注意的是，這有可能促成利用 LLM 的新型 AutoML 方法，而無需具備底層 AutoML 函式庫與工具箱的專家知識。我們預見 ChatBCI 在 BCI 解碼基準測試任務中也將格外有用——此類任務需要結構化的大規模方法，而這尤其契合人工智慧相對於人類智慧的特定強項。

---

## 4 Conclusions | 結論

> [!quote] Original
> This paper introduced the ChatBCI toolbox as a practical and innovative solution to foster human-AI collaboration in the fields of Brain-Computer Interface (BCI) research and brain signal analysis. By adopting our Janusian Design Principles, we demonstrated how human-AI teaming can address challenges in BCI development, such as small datasets, complex signal dynamics, and knowledge transfer. Specifically:
>
> 1. **Collaborative Human-AI Workspaces**: The ChatBCI framework was built to integrate the strengths of both human expertise and AI capabilities, providing an adaptable, interactive workspace that supports all phases of scientific research, from idea generation to experimental analysis and result interpretation.
> 2. **Application to Motor Imagery Decoding**: Using the ChatBCI toolbox, we explored the BCI Competition IV 2a dataset and implemented a deep learning decoder in a collaborative, time-efficient manner. This process highlighted the utility of ChatBCI for accelerating workflows, identifying domain-specific challenges, and fostering human-AI co-learning.
> 3. **Transfer of Expertise and Knowledge**: The structured interaction framework enabled effective transfer of non-obvious, domain-specific knowledge from human experts to the AI, ensuring meaningful co-learning. The AI, in turn, proved to be a valuable partner, suggesting analyses, generating code, and adapting to user inputs.
> 4. **Scalable Design for Broader Neurotechnological Applications**: While demonstrated for a specific EEG dataset, the principles and methods behind ChatBCI are extensible to a wide range of neuroscience and neurotechnology challenges, laying a foundation for future advancements in brain research and AI-driven discovery.

> [!note] 翻譯
> 本文提出 ChatBCI 工具箱，作為在腦機介面（BCI）研究與腦訊號分析領域促進人機協作的一項務實且創新的解決方案。透過採納我們的 Janusian 設計原則，我們展示了人機協作如何因應 BCI 開發中的挑戰，例如小型資料集、複雜的訊號動態與知識轉移。具體而言：
>
> 1. **協作式人機工作空間**：ChatBCI 框架旨在整合人類專業與 AI 能力二者之長，提供一個可調適、可互動的工作空間，支援從想法生成到實驗分析與結果詮釋的科學研究所有階段。
> 2. **運動想像解碼之應用**：我們使用 ChatBCI 工具箱探索 BCI Competition IV 2a 資料集，並以協作且具時間效率的方式實作深度學習解碼器。此過程凸顯 ChatBCI 在加速工作流程、辨識領域特定挑戰與促進人機共同學習方面的效用。
> 3. **專業與知識的轉移**：結構化互動框架使人類專家得以將不明顯、領域特定的知識有效轉移給 AI，確保有意義的共同學習。反過來，AI 也證明是寶貴的夥伴：建議分析、生成程式碼並因應使用者輸入而調整。
> 4. **面向更廣神經科技應用的可擴展設計**：雖然本文以特定 EEG 資料集進行展示，ChatBCI 背後的原則與方法可延伸至廣泛的神經科學與神經科技挑戰，為腦科學研究與 AI 驅動發現的未來進展奠定基礎。

---

> [!quote] Original
> By merging the adaptability of AI systems with the nuanced understanding of human researchers, ChatBCI represents a step forward in achieving efficient and collaborative research processes. Our findings indicate that human-AI teaming, as enabled by ChatBCI, holds significant promise for accelerating scientific discovery while maintaining the interpretability and trust essential for impactful advancements in neuroscience and BCI.
>
> We envision a future where in some scenarios brain-computer interfaces (BCIs) transcend traditional keyboard inputs, replacing them with direct interaction through physiological signals—a transformative goal of BCI research and development. For instance, advances like Meta's silent speech decoding demonstrate the potential of such technologies to enable seamless communication between humans and machines Défossez et al. [2023]. This innovation aligns perfectly with the evolving paradigm of human-AI collaboration, where neural interfacing could ensure seamless interaction and integration of human-AI teams. By integrating robust privacy and security measures (Kapitonova et al. [2022]), such systems could enable richer societal interactions and more immersive experiences in scientific discovery, healthcare, and beyond. The convergence of neurotechnology and AI promises not only to amplify human cognitive capabilities but also to unlock new paradigms for how humans and machines collaborate to solve complex problems.
>
> Through the type of collaborative teamwork on brain data enabled by ChatBCI, AI systems may progressively develop a deep and holistic understanding of human brain function. By engaging with the complexities of brain signals, their multiscale dynamics, and their connections to cognition, behavior, and emotion, such AI systems may transcend conventional analytical approaches. This emerging paradigm has potential not only in neuroscience and neurotechnology but also fields such as medicine, psychology, and education. Furthermore, a "brain-grokking AI" could unlock significant performance advantages in areas that rely on human brain function, including optimized mental health interventions and tailored cognitive training, underscoring the importance of human-AI collaboration as a cornerstone for advancing knowledge and unlocking new possibilities in science and technology.

> [!note] 翻譯
> 透過融合 AI 系統的適應性與人類研究者細緻入微的理解，ChatBCI 代表朝向高效協作研究流程邁出的一步。我們的發現顯示，由 ChatBCI 促成的人機協作，在加速科學發現的同時，仍能維持神經科學與 BCI 領域中具影響力進展所不可或缺的可解釋性與信任，前景可觀。
>
> 我們展望這樣的未來：在某些情境中，腦機介面（BCI）將超越傳統鍵盤輸入，代之以透過生理訊號的直接互動——這是 BCI 研發的一項變革性目標。例如，Meta 的無聲語音解碼等進展，展示了此類技術促成人機無縫溝通的潛力 Défossez et al. [2023]。此創新與持續演進的人機協作典範完美契合：神經介面（neural interfacing）可確保人機團隊的無縫互動與整合。透過整合健全的隱私與安全措施（Kapitonova et al. [2022]），此類系統可在科學發現、醫療照護等領域促成更豐富的社會互動與更沉浸的體驗。神經科技與 AI 的匯流，不僅有望放大人類認知能力，更可望開啟人與機器協作解決複雜問題的新典範。
>
> 透過 ChatBCI 所促成的這類針對腦資料的協作團隊工作，AI 系統或可逐步發展出對人腦功能深刻而全面的理解。藉由投入腦訊號的複雜性、其多尺度動態，及其與認知、行為和情緒的關聯，此類 AI 系統或能超越傳統分析方法。此新興典範不僅在神經科學與神經科技領域具有潛力，在醫學、心理學與教育等領域亦然。此外，「洞澈大腦的 AI」（brain-grokking AI）可在仰賴人腦功能的領域釋放顯著的效能優勢，包括最佳化的心理健康介入與量身打造的認知訓練，凸顯人機協作作為推進知識、開啟科學與技術新可能之基石的重要性。

---

## References | 參考文獻

> [!info] References omitted / 參考文獻略
