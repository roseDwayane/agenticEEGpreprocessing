---
citation_key: "TriratEtAl2024"
title: "AutoML-Agent: A Multi-Agent LLM Framework for Full-Pipeline AutoML"
year: 2024
access_level: "full-text-pdf"
source_url: "https://arxiv.org/abs/2410.02958"
---

# AutoML-Agent: A Multi-Agent LLM Framework for Full-Pipeline AutoML

## 摘要 / Abstract
> [!abstract] Original
> Automated machine learning (AutoML) accelerates AI development by automating tasks in the development pipeline, such as optimal model search and hyperparameter tuning. Existing AutoML systems often require technical expertise to set up complex tools, which is in general time-consuming and requires a large amount of human effort. Therefore, recent works have started exploiting large language models (LLM) to lessen such burden and increase the usability of AutoML frameworks via a natural language interface, allowing non-expert users to build their data-driven solutions. These methods, however, are usually designed only for a particular process in the AI development pipeline and do not efficiently use the inherent capacity of the LLMs. This paper proposes AutoML-Agent, a novel multi-agent framework tailored for full-pipeline AutoML, i.e., from data retrieval to model deployment. AutoML-Agent takes user's task descriptions, facilitates collaboration between specialized LLM agents, and delivers deployment-ready models. Unlike existing work, instead of devising a single plan, we introduce a retrieval-augmented planning strategy to enhance exploration to search for more optimal plans. We also decompose each plan into sub-tasks (e.g., data preprocessing and neural network design) each of which is solved by a specialized agent we build via prompting executing in parallel, making the search process more efficient. Moreover, we propose a multi-stage verification to verify executed results and guide the code generation LLM in implementing successful solutions. Extensive experiments on seven downstream tasks using fourteen datasets show that AutoML-Agent achieves a higher success rate in automating the full AutoML process, yielding systems with good performance throughout the diverse domains.

> [!abstract] 繁體中文摘要
> 自動化機器學習（AutoML）藉由自動化開發流程中的任務（如最佳模型搜尋與超參數調校）來加速 AI 開發。現有 AutoML 系統常需技術專業才能設定複雜工具，普遍耗時且需大量人力。因此近期研究開始運用大型語言模型（LLM）以自然語言介面降低此負擔、提升 AutoML 框架可用性，讓非專家使用者也能建構資料驅動解決方案。然而這些方法通常只針對 AI 開發流程中的特定環節設計，未能有效運用 LLM 的內在能力。本文提出 AutoML-Agent—一個專為全流程 AutoML（即從資料取得到模型部署）量身打造的多代理框架。AutoML-Agent 接收使用者任務描述，促成專門 LLM 代理間的協作，並交付可部署的模型。與既有研究不同，我們不擬定單一計畫，而是引入檢索增強的規劃策略以強化探索、搜尋更佳計畫。我們也將每個計畫分解為子任務（如資料前處理與神經網路設計），各由我們以提示建構的專門代理平行求解，使搜尋過程更有效率。此外，我們提出多階段驗證以驗證執行結果並引導程式碼生成 LLM 實作成功的解。在七項下游任務、十四個資料集上的大量實驗顯示，AutoML-Agent 在自動化全 AutoML 流程上達到更高成功率，並在多元領域中產出表現良好的系統。

## Key Points / 重點筆記

> [!note] What problem it solves / 解決什麼問題
> EN: Prior LLM-based AutoML methods cover only one stage of the pipeline (e.g., just feature engineering or just HPO) and underuse the LLM's capacity. AutoML-Agent targets the *full* pipeline — from data retrieval to deployment — and makes it accessible to non-experts via natural language, while improving robustness through exploration (multiple plans) and verification (catching failures before deployment).
> 中: 先前基於 LLM 的 AutoML 方法只涵蓋管線中單一環節（如只做特徵工程或只做 HPO），且未充分運用 LLM 能力。AutoML-Agent 鎖定*完整*管線—從資料取得到部署—並透過自然語言讓非專家可用，同時藉由探索（多計畫）與驗證（在部署前攔截失敗）提升穩健性。

> [!note] Method / 方法
> EN: A multi-agent framework coordinated by an **Agent Manager** routing among specialized LLM agents: a **Prompt Agent** (instruction-tuned to parse the user's request into a standardized format), a **Data Agent** (data retrieval/preprocessing/augmentation/analysis), a **Model Agent** (model search, HPO, profiling, candidate ranking), and an **Operation Agent** (implements the verified solution into deployment-ready code). Two core innovations: (1) **Retrieval-Augmented Planning (RAP)** — instead of one plan, it retrieves relevant knowledge to generate and explore multiple candidate plans; each plan is decomposed into sub-tasks solved by specialized agents *in parallel*. (2) **Multi-stage verification** — verifies executed results at multiple points and guides the code-generation LLM toward successful, deployment-ready implementations.
> 中: 一個由**Agent Manager**協調、在專門 LLM 代理間路由的多代理框架：**Prompt Agent**（指令微調以將使用者請求解析為標準格式）、**Data Agent**（資料取得/前處理/擴增/分析）、**Model Agent**（模型搜尋、HPO、剖析、候選排序）、**Operation Agent**（將已驗證的解實作為可部署程式碼）。兩項核心創新：(1) **檢索增強規劃（RAP）**—不只一個計畫，而是檢索相關知識以生成並探索多個候選計畫；每個計畫被分解為子任務，由專門代理*平行*求解。(2) **多階段驗證**—在多個環節驗證執行結果，引導程式碼生成 LLM 邁向成功、可部署的實作。

> [!note] Key findings / 主要發現
> EN: Across seven downstream tasks and fourteen datasets, AutoML-Agent achieves a higher success rate in automating the full AutoML pipeline and produces well-performing systems across diverse domains, outperforming single-stage LLM-AutoML baselines. The retrieval-augmented multi-plan exploration plus parallel specialized agents makes the search both more thorough and more efficient, and the multi-stage verification materially raises the rate of producing deployment-ready code.
> 中: 在七項下游任務、十四個資料集上，AutoML-Agent 在自動化全 AutoML 管線上達到更高成功率，並在多元領域產出表現良好的系統，勝過單階段 LLM-AutoML 基準。檢索增強的多計畫探索加上平行專門代理，使搜尋既更徹底又更有效率，而多階段驗證實質提升了產出可部署程式碼的比率。

> [!tip] Relevance to "Agentic EEG preprocessing + FOOOF-SNR" project / 與本計畫的關聯
> EN: AutoML-Agent supplies the multi-agent architecture blueprint for our preprocessing agent. (c) LLM-agent parameter selection: its Agent-Manager + specialized-agent decomposition maps cleanly onto EEG — a "Signal-Quality Agent" computing FOOOF-SNR, a "Cleaning Agent" proposing preprocessing actions/parameters (filter cutoffs, ASR cutoff, ICA component thresholds), and a Manager that routes and verifies — letting us parallelize the per-action search rather than running one monolithic plan. (a) Action space: "decompose each plan into sub-tasks (data preprocessing, …)" is exactly our preprocessing-step decomposition; we adopt its sub-task-per-agent structure for ordered EEG operations. The **Retrieval-Augmented Planning** idea lets the agent explore *several* candidate pipelines (not one), which is the LLM analog of the greedy search's breadth. (b) FOOOF-SNR + verification: AutoML-Agent's **multi-stage verification** is the slot where our FOOOF-SNR + Gerster fit-validity gate live — verification accepts a pipeline only if SNR improves and the FOOOF fit stays valid, preventing the agent from "deploying" a pipeline that produced a spurious metric gain. This directly counters the Delorme finding that aggressive cleaning often degrades quality: verification gives the agent a stop/accept criterion grounded in signal quality, not just code execution success.
> 中: AutoML-Agent 為我們的前處理代理提供多代理架構藍圖。(c) LLM 代理參數選擇：其 Agent-Manager＋專門代理的分解可乾淨對應到 EEG—一個計算 FOOOF-SNR 的「訊號品質代理」、一個提出前處理動作/參數（濾波截止、ASR 截止、ICA 成分閾值）的「清理代理」、以及一個負責路由與驗證的 Manager—讓我們可平行化逐動作搜尋，而非執行單一龐大計畫。(a) 動作空間：「將每個計畫分解為子任務（資料前處理、…）」正是我們的前處理步驟分解；我們採用其「每子任務一代理」結構來處理有序的 EEG 操作。**檢索增強規劃**讓代理探索*數個*候選管線（而非一個），這是貪婪搜尋廣度的 LLM 對應物。(b) FOOOF-SNR＋驗證：AutoML-Agent 的**多階段驗證**正是我們 FOOOF-SNR＋Gerster 擬合有效性閘門所在之處—驗證僅在 SNR 提升且 FOOOF 擬合保持有效時才接受管線，防止代理「部署」產生虛假指標增益的管線。這直接抗衡 Delorme 的發現（激進清理常劣化品質）：驗證給代理一個以訊號品質為基礎、而不僅是程式碼執行成功的 停止/接受 準則。

## Full Text / 全文

### Abstract
AutoML automates model search and HPO but existing systems need expertise; LLM-based methods lower the barrier but cover only one pipeline stage. **AutoML-Agent** is a multi-agent LLM framework for *full-pipeline* AutoML (data retrieval → deployment). It parses user task descriptions, coordinates specialized LLM agents, and delivers deployment-ready models. Innovations: **retrieval-augmented planning** (explore multiple plans instead of one), **plan decomposition into sub-tasks solved by parallel specialized agents**, and **multi-stage verification** guiding code generation. Experiments on 7 tasks / 14 datasets show higher success rates and good performance across domains.

### 1 Introduction
AutoML reduces expertise/labor by automating feature engineering, model selection, HPO, etc., but configuring tools needs programming skill, barring non-experts. Prior LLM-based frameworks use natural-language interfaces but consider limited tasks (only one pipeline process) due to restricted designs. This paper presents the first task-agnostic LLM AutoML framework spanning data retrieval to deployment, delivering a deployment-ready model and inference endpoint.

### 2 Related Work
Reviews traditional AutoML and recent LLM-based ML/DS agents (including case-based and hierarchical-graph approaches), noting each prior method covers only part of the pipeline. Table 1 summarizes differences vs. AutoML-Agent (full pipeline, retrieval-augmented planning, multi-stage verification).

### 3 AutoML-Agent
Agents coordinated by an **Agent Manager (A_mgr)** to complete the user's instruction and deliver a deployment-ready model:
- **Agent Manager (A_mgr):** core interface; routes work, aggregates results, runs verification.
- **Prompt Agent (A_p):** instruction-tuned LLM that parses the user's instructions into a standardized format.
- **Data Agent (A_d):** data retrieval, preprocessing, augmentation, analysis; its analysis informs the Model Agent.
- **Model Agent (A_m):** model search, HPO, model profiling, candidate ranking; results returned to the Manager for verification.
- **Operation Agent (A_o):** implements the verified Data/Model solution into deployment-ready code.

**3.4 Retrieval-Augmented Planning (RAP).** Instead of a single plan, A_mgr generates and explores multiple candidate plans using retrieved knowledge; each plan p_i is decomposed into data sub-tasks (sd_i, for the Data Agent: retrieval, preprocessing, augmentation, analysis — "Pseudo Data Analysis") and model sub-tasks, which specialized agents solve **in parallel**, making the search more efficient.

**Multi-stage verification.** Executed results are verified at multiple stages; before deployment, an implementation verification ensures the code is deployment-ready. If verification fails, the framework iterates/repairs before proceeding.

### 4 Experiments
Across 7 downstream tasks and 14 datasets, AutoML-Agent attains a higher success rate in automating the full pipeline and yields good-performing systems across diverse domains, outperforming single-stage LLM-AutoML baselines; ablations show retrieval-augmented planning and multi-stage verification each contribute.

### 5 Conclusion
AutoML-Agent is a multi-agent LLM framework for full-pipeline AutoML with retrieval-augmented planning, parallel sub-task decomposition, and multi-stage verification, delivering deployment-ready models with higher success rates across domains.
