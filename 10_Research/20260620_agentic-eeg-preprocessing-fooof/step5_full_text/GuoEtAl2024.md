---
citation_key: "GuoEtAl2024"
title: "DS-Agent: Automated Data Science by Empowering Large Language Models with Case-Based Reasoning"
year: 2024
access_level: "full-text-html"
source_url: "https://arxiv.org/html/2402.17453v5"
---

# DS-Agent: Automated Data Science by Empowering Large Language Models with Case-Based Reasoning

## 摘要 / Abstract
> [!abstract] Original
> In this work, we investigate the potential of large language models (LLMs) based agents to automate data science tasks, with the goal of comprehending task requirements, then building and training the best-fit machine learning models. Despite their widespread success, existing LLM agents are hindered by generating unreasonable experiment plans within this scenario. To this end, we present DS-Agent, a novel automatic framework that harnesses LLM agent and case-based reasoning (CBR). In the development stage, DS-Agent follows the CBR framework to structure an automatic iteration pipeline, which can flexibly capitalize on the expert knowledge from Kaggle, and facilitate consistent performance improvement through the feedback mechanism. Moreover, DS-Agent implements a low-resource deployment stage with a simplified CBR paradigm to adapt past successful solutions from the development stage for direct code generation, significantly reducing the demand on foundational capabilities of LLMs. Empirically, DS-Agent with GPT-4 achieves 100% success rate in the development stage, while attaining 36% improvement on average one pass rate across alternative LLMs in the deployment stage. In both stages, DS-Agent achieves the best rank in performance, costing $1.60 and $0.13 per run with GPT-4, respectively.

> [!abstract] 繁體中文摘要
> 本研究探討以大型語言模型（LLM）為基礎的代理在自動化資料科學任務上的潛力，目標是理解任務需求，進而建立並訓練最適配的機器學習模型。儘管 LLM 代理已廣泛成功，現有代理在此情境下會生成不合理的實驗計畫而受阻。為此，我們提出 DS-Agent—一個結合 LLM 代理與案例式推理（CBR）的新型自動框架。在開發階段，DS-Agent 依循 CBR 框架建構自動迭代管線，能靈活運用來自 Kaggle 的專家知識，並透過回饋機制促成一致的表現提升。此外，DS-Agent 以簡化的 CBR 範式實作低資源的部署階段，將開發階段的過往成功解轉用於直接程式碼生成，大幅降低對 LLM 基礎能力的需求。實證上，DS-Agent 搭配 GPT-4 在開發階段達到 100% 成功率，在部署階段於各替代 LLM 上平均 one-pass 率提升 36%。兩階段中 DS-Agent 皆取得最佳表現排名，搭配 GPT-4 每次執行成本分別為 1.60 與 0.13 美元。

## Key Points / 重點筆記

> [!note] What problem it solves / 解決什麼問題
> EN: Generic LLM agents fail at end-to-end data science because they generate unreasonable experiment plans from scratch and have low task completion rates. DS-Agent grounds the agent in *case-based reasoning*: instead of inventing pipelines, it retrieves and adapts proven expert solutions (Kaggle technical reports/code), then iteratively revises them using execution feedback — raising reliability and cutting cost.
> 中: 通用 LLM 代理在端到端資料科學上失敗，因為它們從零生成不合理的實驗計畫、任務完成率低。DS-Agent 以*案例式推理*為基礎：不憑空發明管線，而是檢索並改編已驗證的專家解（Kaggle 技術報告/程式碼），再用執行回饋迭代修訂—提高可靠度並降低成本。

> [!note] Method / 方法
> EN: DS-Agent implements the classic CBR cycle — Retrieve, Reuse, Revise, Retain — over a case bank of Kaggle "human insight" cases. In the **development stage**, given a new task it: (1) Retrieves top-k cases by cosine similarity between task and case embeddings; (2) Reuses them to draft an experiment plan; (3) executes, gets feedback, and Revises iteratively; (4) Retains successful solutions back into the case bank — a learning mechanism without gradient updates. The agent comprises specialized roles (Planner, Programmer, Debugger, Logger, RankReviser/Adapter). In the **deployment stage**, a *simplified* CBR (retrieve + reuse only, no iterative feedback) adapts past successful development-stage solutions for one-shot code generation at low cost.
> 中: DS-Agent 在 Kaggle「人類洞見」案例庫上實作經典 CBR 循環—檢索（Retrieve）、重用（Reuse）、修訂（Revise）、保留（Retain）。在**開發階段**，給定新任務時：(1) 以任務與案例嵌入的餘弦相似度檢索 top-k 案例；(2) 重用以草擬實驗計畫；(3) 執行、取得回饋並迭代修訂；(4) 將成功解保留回案例庫—一種無需梯度更新的學習機制。代理包含專門角色（Planner、Programmer、Debugger、Logger、RankReviser/Adapter）。在**部署階段**，以*簡化*的 CBR（僅檢索＋重用、無迭代回饋）改編開發階段的過往成功解，以低成本進行一次性程式碼生成。

> [!note] Key findings / 主要發現
> EN: With GPT-4, DS-Agent reached a 100% success rate in the development stage and ranked best in performance, at $1.60/run. The deployment stage, using the simplified CBR to reuse retained solutions, achieved a ~36% average improvement in one-pass rate across alternative (weaker) LLMs and cut cost to $0.13/run — a ~91% cost reduction — showing that retained cases transfer foundational capability from strong to weak models. The authors distinguish CBR from RAG: CBR additionally *revises* via feedback and *retains* good solutions, enabling consistent improvement rather than one-shot retrieval.
> 中: 搭配 GPT-4，DS-Agent 在開發階段達到 100% 成功率並取得最佳表現排名，每次成本 1.60 美元。部署階段以簡化 CBR 重用保留的解，在各替代（較弱）LLM 上平均 one-pass 率提升約 36%，成本降至每次 0.13 美元—約 91% 成本縮減—顯示保留的案例能將基礎能力從強模型轉移到弱模型。作者區分 CBR 與 RAG：CBR 額外透過回饋*修訂*並*保留*好的解，達成一致提升而非一次性檢索。

> [!tip] Relevance to "Agentic EEG preprocessing + FOOOF-SNR" project / 與本計畫的關聯
> EN: DS-Agent is the closest LLM-agent design pattern for our preprocessing-selection agent. (c) LLM-agent parameter selection: its Retrieve→Reuse→Revise→Retain loop is directly transplantable — our agent can keep a case bank of (EEG-recording-signature → successful preprocessing pipeline + parameters) cases, retrieve the most similar past recording, reuse its pipeline, then revise based on the measured FOOOF-SNR feedback, and retain pipelines that improved SNR. (b) FOOOF-SNR as the feedback/evaluator: DS-Agent's CBR loop needs an evaluator p_E that produces execution feedback; in our setting the FOOOF-SNR (and Gerster fit-validity gate) *is* that evaluator, replacing Kaggle leaderboard score — this gives the LLM agent a cheap, label-free reward signal to revise pipelines. (a) Action space / greedy-search: the development-vs-deployment split mirrors our two phases — an expensive greedy search builds the "case bank" of ground-truth-best pipelines per recording type, then a cheap deployment agent retrieves and adapts them to new EEG recordings, exactly the cost-reduction story DS-Agent demonstrates (strong→weak transfer = greedy-search ground truth → fast LLM agent).
> 中: DS-Agent 是與我們前處理選擇代理最接近的 LLM 代理設計範式。(c) LLM 代理參數選擇：其 檢索→重用→修訂→保留 循環可直接移植—我們的代理可維護一個（EEG 紀錄特徵→成功前處理管線＋參數）的案例庫，檢索最相似的過往紀錄、重用其管線、再依據量測到的 FOOOF-SNR 回饋修訂，並保留改善了 SNR 的管線。(b) FOOOF-SNR 作為回饋/評估器：DS-Agent 的 CBR 循環需要一個產生執行回饋的評估器 p_E；在我們的設定中，FOOOF-SNR（加上 Gerster 擬合有效性閘門）*正是*該評估器，取代 Kaggle 排行榜分數—使 LLM 代理擁有便宜、無標籤的獎勵訊號來修訂管線。(a) 動作空間/貪婪搜尋：開發 vs 部署的切分對應我們的兩個階段—昂貴的貪婪搜尋為每種紀錄類型建立「案例庫」（真值最佳管線），接著便宜的部署代理檢索並改編至新 EEG 紀錄，正是 DS-Agent 所示範的成本縮減故事（強→弱轉移＝貪婪搜尋真值→快速 LLM 代理）。

## Full Text / 全文

### Abstract
LLM agents can automate data science but generate unreasonable experiment plans. DS-Agent combines an LLM agent with **case-based reasoning (CBR)**. The **development stage** uses CBR to build an automatic iteration pipeline that exploits Kaggle expert knowledge with feedback-driven improvement. The **deployment stage** uses a simplified CBR to adapt past successful solutions for direct code generation at low cost. With GPT-4: 100% success in development; +36% average one-pass rate across alternative LLMs in deployment; best performance rank in both; $1.60 and $0.13 per run respectively. Code: github.com/guosyjlu/DS-Agent.

### 1 Introduction
Goal: LLM agents that comprehend a data-science task and build/train the best-fit ML model. Existing agents generate unreasonable plans and have low completion rates. Kaggle, the world's largest data-science competition platform, offers a vast repository of expert technical reports and code. The authors adopt **case-based reasoning (CBR)** — retrieve similar past problems, reuse their solutions, evaluate effectiveness, revise the solution, retain successful solutions — to let LLM agents analyze, extract and reuse expert solution patterns and iteratively revise based on execution feedback, with high sample/compute efficiency.

### 2 Preliminary
**CBR-based LLMs** comprise: (i) a retriever p_R returning a distribution over the case database given task τ and feedback l; (ii) an LLM p_LLM generating solution y from τ, l and retrieved case c; (iii) an evaluator p_E producing feedback l of solution y. This forms an iteration loop where the solution distribution is marginalized over execution feedback and the retrieved case. Contrast with **RAG**, which has only a retriever + LLM (single latent variable c, no revise/retain). CBR additionally adjusts the retrieved case, revises the solution via feedback, and retains good solutions — a flexible learning mechanism yielding consistent improvement without back-propagation.

### 3 The DS-Agent
**3.1 Development Stage — Automatic Iteration Pipeline.** Given a new task, DS-Agent:
- **Step 1 Retrieve:** computes cosine similarity sim(τ,c) = cos(E(τ), E(c)) between task description and each case in the human-insight case bank C, retrieving the top-k cases.
- **Reuse/Plan, Program, Execute, Debug, Log:** specialized roles (Planner, Programmer, Debugger, Logger) draft and run an experiment plan.
- **Revise (RankReviser/Adapter):** iteratively adjusts the retrieved case and revises the plan based on execution feedback for consistent improvement.
- **Retain:** stores successful solutions to the case bank for future reuse.

**3.2 Deployment Stage — Learning from Past Cases.** A simplified CBR (retrieve + reuse, no iterative feedback) for low-resource, one-shot code generation: retrieves and reuses past successful solutions from the development stage in the same task distribution, requiring only minor modifications, thus reducing the demand on the LLM's foundational capability.

### 4 Experiments
**Development stage:** GPT-4 achieves 100% success rate and best performance rank; cost $1.60/run. Ablations confirm CBR retrieval and the feedback loop each contribute.
**Deployment stage:** simplified CBR yields ~36% average improvement in one-pass rate across alternative LLMs, best rank, at $0.13/run (~91% cheaper than development). Further analyses cover error modes and case studies.

### 5 Related Work
Positions DS-Agent against general LLM agents and AutoML, and against RAG-based LLMs, arguing CBR's revise+retain steps are what enable consistent improvement in data-science automation.

### 6 Conclusion
DS-Agent integrates CBR into LLM agents for automated data science, achieving high success rates and strong cost-efficiency in both development and deployment stages, and enabling knowledge transfer from strong to weaker LLMs via retained cases.
