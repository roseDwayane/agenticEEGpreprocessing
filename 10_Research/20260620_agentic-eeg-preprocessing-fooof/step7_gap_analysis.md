---
session_id: "20260620"
topic: "Agentic EEG preprocessing pipeline with FOOOF-based signal quality optimization"
date: "2026-06-20"
step: 7
gaps_identified: 4
priority_weights: "severity=0.40, novelty=0.30, feasibility=0.30"
---

# Gap Analysis / 研究缺口分析

> Topic / 研究主題: Agentic EEG preprocessing pipeline with FOOOF-based signal quality optimization
> Papers analyzed / 分析論文數: 53 (from SOTA review)
> Gaps identified / 已識別缺口: 4
> Date / 日期: 2026-06-20

## Executive Summary / 總覽摘要

The literature is mature in three areas that have evolved **in parallel but never been joined**. EEG preprocessing (Theme 1) has many automated pipelines but, as both PedroniEtAl2019 and Delorme2023 explicitly state, **no objective, label-free way to score preprocessing quality**. Spectral parameterization (Theme 2) gives a physiologically validated signal/noise split (DonoghueEtAl2020b) but is used only descriptively, never as an optimization target. Pipeline optimization (Theme 5) has leapt from Bayesian-opt AutoML to tree-search/MCTS LLM agents (GuoEtAl2024, ChiEtAl2024, TriratEtAl2024) — yet every one of those agents operates on tabular data, code, or chemistry, never on raw electrophysiology. The four gaps below sit in the empty cells between these literatures, and they correspond one-to-one to the three pillars of the proposed project plus its principal scientific risk.

文獻在三個領域已成熟，但這三者**平行演進、從未接合**。EEG 預處理（主題 1）有許多自動化管線，但如 PedroniEtAl2019 與 Delorme2023 明言——**沒有客觀、無標籤的方法來評分預處理品質**。頻譜參數化（主題 2）提供經生理驗證的訊號/雜訊分解（DonoghueEtAl2020b），卻僅作描述性使用，從未作為最佳化目標。管線最佳化（主題 5）已從貝氏最佳化 AutoML 躍進到樹搜尋/MCTS 的 LLM 智能體——然而這些智能體無一operate 於原始電生理。以下四個缺口正落在這些文獻之間的空格中，並與所提計畫的三大支柱及其主要科學風險一一對應。

The recommended battle (highest composite) is **GAP_001 — using FOOOF-derived SNR as the objective function for EEG preprocessing** — because it is simultaneously the most severe (it unblocks the whole field's "how do we judge preprocessing?" problem), the most novel (no paper does it), and reasonably feasible (FOOOF and the pipelines are off-the-shelf). GAP_002 (greedy-search ground truth) and GAP_003 (LLM-agent selection) are the engineering arms that operationalize it; GAP_004 (aperiodic signal-vs-artifact) is the risk that must be controlled for the metric to be valid.

建議的戰場（最高綜合分）是 **GAP_001——以 FOOOF 衍生的 SNR 作為 EEG 預處理的目標函數**——因為它同時最嚴重、最新穎且相當可行。GAP_002（貪婪搜尋 ground truth）與 GAP_003（LLM 智能體選擇）是將其operationalize 的工程之臂；GAP_004（非週期訊號 vs artifact）是指標有效所必須控制的風險。

---

## GAP_001: FOOOF-Derived SNR as the Objective Function for Preprocessing / 以 FOOOF 衍生 SNR 作為預處理目標函數

**Type / 類型:** Measurement (primary) + Integration (secondary)
**Priority Rank / 優先排名:** #1

### Description / 描述

No study uses the FOOOF periodic/aperiodic decomposition as the **quality metric that preprocessing is optimized against**. The literature evaluates preprocessing either by downstream task accuracy (most of Theme 5's EEG-adjacent work) or by condition-contrast statistics requiring labeled events (Delorme2023). A FOOOF-based SNR — periodic (oscillatory) power treated as signal, aperiodic 1/f + fit residual treated as noise — would be **task-independent, label-free, and computable on a single resting-state recording**, enabling preprocessing to be scored and optimized without any downstream classifier.

沒有研究將 FOOOF 週期/非週期分解用作**預處理所最佳化的品質指標**。文獻評估預處理不是靠下游任務準確率，就是靠需標註事件的條件對比統計（Delorme2023）。以 FOOOF 為基礎的 SNR——週期（振盪）功率視為訊號、非週期 1/f + 擬合殘差視為雜訊——將**任務無關、無標籤、且可在單筆靜息態記錄上計算**，使預處理無需任何下游分類器即可評分與最佳化。

### Supporting Evidence / 支持證據

- **PedroniEtAl2019 (2019)**: States outright that "there is no method to objectively quantify the quality of preprocessed EEG," and that this drives p-hacking via subjective subject exclusion. The named absence is exactly this gap. / 明言「沒有方法能客觀量化預處理後 EEG 的品質」，此被點名的空白正是此缺口。

- **Delorme2023 (2023)**: Builds an objective preprocessing-quality metric — but it is a condition-contrast channel metric requiring two task conditions, so it cannot score resting-state or unlabeled data. It proves the *demand* for an objective metric while leaving the label-free case open. / 建立客觀預處理品質指標，但需兩個任務條件，無法評分靜息態或無標籤資料；證明了對客觀指標的*需求*，卻留下無標籤情形未解。

- **DonoghueEtAl2020b (2020)** + **DonoghueEtAl2020a (2020)**: Establish that periodic and aperiodic power are separable and that conflating them produces spurious results — providing the principled decomposition an SNR metric would stand on, yet never proposing it as a preprocessing objective. / 確立週期與非週期功率可分離且混淆會產生假結果——提供 SNR 指標所立足的有原則分解，卻從未提議將其作為預處理目標。

- **RobbinsEtAl2020 (2020)**: Demonstrates that downstream measures shift substantially with preprocessing choices, so an objective target to optimize against is needed — but uses amplitude-domain measures, not spectral SNR. / 證明下游指標隨預處理選擇大幅變動，故需要可最佳化的客觀目標——但用振幅域指標，非頻譜 SNR。

### Counter-Evidence / 反面證據

- **Delorme2023 (2023)**: Partially addresses the gap by introducing *an* objective preprocessing metric → but it is label-dependent (needs conditions) and not spectral, so the FOOOF-SNR, label-free formulation remains entirely open. / 引入*一個*客觀預處理指標，但依賴標籤且非頻譜，FOOOF-SNR 無標籤的形式仍完全開放。

- **MutanenEtAl2018 (2018)** (SOUND): Frames denoising in explicit SNR terms → but its SNR is a sensor-level noise estimate, not a periodic/aperiodic spectral ratio, and it is a single algorithm, not an optimization target. / 以明確 SNR 框定去噪，但其 SNR 是感測器層級雜訊估計，非週期/非週期頻譜比值，且為單一演算法而非最佳化目標。

### Why It Matters / 重要性

This is the conceptual keystone of the entire project. If a FOOOF-SNR reliably ranks preprocessing outputs the way an expert would, it unblocks *everything*: ground-truth construction (GAP_002), agent training (GAP_003), and reproducible, classifier-free preprocessing QC for the whole field. If it does not, the project's premise fails early and cheaply — making this both the highest-value and the right first thing to test. / 這是整個計畫的概念拱心石。若 FOOOF-SNR 能可靠地像專家一樣排序預處理輸出，就解鎖了*一切*：ground truth 建構、agent 訓練、以及全領域可重現、免分類器的預處理品管。若不能，計畫前提便早而廉價地失敗——使其既是最高價值、也是該最先測試的事。

### Priority Score / 優先分數

| Axis / 評估軸 | Score / 分數 | Rationale / 理由 |
|--------------|-------------|-----------------|
| Severity / 嚴重性 | 5 | Two independent papers name the missing objective metric as a field-level problem / 兩篇獨立論文點名此缺失指標為領域級問題 |
| Novelty / 新穎性 | 5 | No paper uses FOOOF as a preprocessing objective function / 無論文以 FOOOF 作為預處理目標函數 |
| Feasibility / 可行性 | 4 | FOOOF + pipelines are off-the-shelf; main risk is metric validity (GAP_004) / FOOOF 與管線皆現成；主要風險是指標效度 |

**Composite / 綜合分:** 5 × 0.40 + 5 × 0.30 + 4 × 0.30 = 2.00 + 1.50 + 1.20 = **4.70**

---

## GAP_002: A Greedy / Exhaustive-Search Ground-Truth Oracle for EEG Preprocessing / EEG 預處理的貪婪/窮舉搜尋 Ground-Truth Oracle

**Type / 類型:** Methodological (primary) + Measurement (secondary)
**Priority Rank / 優先排名:** #3

### Description / 描述

No published resource exhaustively runs the EEG preprocessing parameter space and records which configuration is best per recording under a quality metric. CoelliEtAl2023 manually compares a few methods per step; AutoML works (FeurerEtAl2015, MartinezEtAl2023) search hyperparameter spaces but for non-EEG data and without spectral quality. A **labeled ground-truth dataset** — every recording × every plausible pipeline configuration → FOOOF-SNR score → best config — does not exist, yet it is the prerequisite for any supervised or imitation-learning agent. / 沒有已發表資源窮舉執行 EEG 預處理參數空間並記錄每筆記錄在品質指標下的最佳組態。此**標註 ground-truth 資料集**並不存在，卻是任何監督或模仿學習 agent 的前提。

### Supporting Evidence / 支持證據

- **CoelliEtAl2023 (2023)**: Performs exactly this per-step comparison but **manually and on a handful of methods**, explicitly to find best practices — demonstrating both the value and the un-scaled state of the approach. / 正執行此逐步比較，但**手動且僅少數方法**，顯示此法的價值與未規模化狀態。

- **RobbinsEtAl2020 (2020)**: Maps preprocessing sensitivity across pipelines, the closest thing to a search, but reports aggregate effects rather than a per-recording optimal configuration. / 描繪跨管線預處理敏感度，最接近搜尋，但報告整體效應而非每筆最佳組態。

- **MartinezEtAl2023 (2023)**: Searches per-instance preprocessing pipelines — the methodological template — but for generic ML data and optimizing task loss, not EEG/FOOOF-SNR. / 搜尋逐實例預處理管線——方法學範本——但針對通用 ML 資料且最佳化任務損失，非 EEG/FOOOF-SNR。

### Counter-Evidence / 反面證據

- **HAPPE-family & Automagic (GabardDurnamEtAl2018, PedroniEtAl2019)**: Provide quality *reports* per recording → but apply a fixed pipeline, never enumerating the configuration space to find a per-recording optimum. / 提供每筆品質*報告*，但套用固定管線，從不列舉組態空間以找每筆最佳。

### Why It Matters / 重要性

This is Pillar 1 of the project and the bridge from GAP_001 (a metric) to GAP_003 (an agent). Without an exhaustive-search oracle there is no ground truth to imitate, no upper bound to measure the agent against, and no way to quantify how much of the achievable quality the agent recovers. It is also independently publishable as the first benchmark of its kind. / 這是計畫的支柱 1，是從 GAP_001（指標）到 GAP_003（agent）的橋樑。無窮舉搜尋 oracle 就沒有可模仿的 ground truth、沒有衡量 agent 的上界、也無法量化 agent 回收多少可達品質。它本身也可作為同類首個基準獨立發表。

### Priority Score / 優先分數

| Axis / 評估軸 | Score / 分數 | Rationale / 理由 |
|--------------|-------------|-----------------|
| Severity / 嚴重性 | 4 | Prerequisite for the learning agent; field lacks any such benchmark / agent 學習的前提；領域缺此基準 |
| Novelty / 新穎性 | 5 | No exhaustive FOOOF-scored preprocessing oracle exists for EEG / EEG 無窮舉 FOOOF 評分的預處理 oracle |
| Feasibility / 可行性 | 4 | Pure compute on public data; cost grows with parameter-grid size / 公開資料上的純計算；成本隨參數網格增長 |

**Composite / 綜合分:** 4 × 0.40 + 5 × 0.30 + 4 × 0.30 = 1.60 + 1.50 + 1.20 = **4.30**

---

## GAP_003: LLM-Agent Selection of EEG Preprocessing Parameters / LLM 智能體選擇 EEG 預處理參數

**Type / 類型:** Integration (primary) + Methodological (secondary)
**Priority Rank / 優先排名:** #2

### Description / 描述

LLM agents that reason over intermediate state, call tools, and search (tree/MCTS/case-based) are proven on tabular ML, code, and chemistry — but **none has been applied to neurophysiological preprocessing**. No system feeds EEG signal features (spectra, channel stats, ICA component labels, intermediate FOOOF estimates) to an LLM controller that then selects the next preprocessing operation and its parameters. This integration of Theme 5's agent machinery with Theme 1's action space, judged by Theme 2's metric, is unoccupied. / 會推理中間狀態、呼叫工具、做搜尋的 LLM 智能體已在表格 ML、程式碼、化學證明——但**無一被用於神經生理預處理**。沒有系統把 EEG 訊號特徵餵給 LLM 控制器再選下一個預處理操作與參數。此整合無人佔據。

### Supporting Evidence / 支持證據

- **GuoEtAl2024 (DS-Agent)、ChiEtAl2024 (SELA)、TriratEtAl2024 (AutoML-Agent)、JiangEtAl2025 (AIDE)**: State-of-the-art LLM-agent ML-engineering systems — all evaluated on tabular/code benchmarks, none on EEG or any signal-processing domain. The domain transfer is unmade. / 最先進的 LLM 智能體 ML 工程系統——全在表格/程式碼基準評估，無一在 EEG 或任何訊號處理領域。領域遷移尚未完成。

- **BashashatiEtAl2016 (2016)**: The only EEG-pipeline optimizer in the collection — but uses Bayesian optimization (pre-LLM, pre-FOOOF) and optimizes task accuracy, showing the EEG-specific precedent stops a full generation behind the agent literature. / 收藏中唯一的 EEG 管線最佳化器——但用貝氏最佳化（前 LLM、前 FOOOF）且最佳化任務準確率，顯示 EEG 專屬前例落後 agent 文獻整整一代。

- **HollmannEtAl2023 (CAAFE)**: LLM makes data-side (feature) decisions → demonstrates LLMs can make data-preparation choices, but for tabular features, not EEG operations. / LLM 做資料端（特徵）決策→顯示 LLM 能做資料準備選擇，但針對表格特徵，非 EEG 操作。

### Counter-Evidence / 反面證據

- **MartinezEtAl2023 (2023)**: Automates per-instance preprocessing selection → but via classical search, not an LLM agent reasoning over signal features, and not on EEG. Shows the search half is feasible; the agentic+EEG half is open. / 自動化逐實例預處理選擇，但用古典搜尋，非 LLM 智能體推理訊號特徵，且非 EEG。顯示搜尋這半可行；智能體+EEG 那半開放。

### Why It Matters / 重要性

This is Pillar 3 — the project's namesake "agentic" contribution. With GAP_002's ground truth in hand, an LLM agent can be trained/evaluated to recover near-optimal preprocessing per recording while remaining interpretable (it can explain why it chose each step). Success would be the first demonstration that LLM agents transfer to electrophysiological signal processing — a result of interest well beyond EEG. / 這是支柱 3——計畫同名的「智能體」貢獻。有了 GAP_002 的 ground truth，LLM 智能體可被訓練/評估以每筆回收近最佳預處理，同時保持可解釋。成功將是 LLM 智能體遷移到電生理訊號處理的首次展示——其意義遠超 EEG。

### Priority Score / 優先分數

| Axis / 評估軸 | Score / 分數 | Rationale / 理由 |
|--------------|-------------|-----------------|
| Severity / 嚴重性 | 5 | The defining "agentic" contribution; nothing comparable exists for EEG / 定義性的「智能體」貢獻；EEG 無可比者 |
| Novelty / 新穎性 | 5 | First LLM-agent for electrophysiological preprocessing / 首個用於電生理預處理的 LLM 智能體 |
| Feasibility / 可行性 | 3 | Depends on GAP_002 ground truth + nontrivial agent/tooling engineering / 依賴 GAP_002 ground truth 及非平凡的 agent/工具工程 |

**Composite / 綜合分:** 5 × 0.40 + 5 × 0.30 + 3 × 0.30 = 2.00 + 1.50 + 0.90 = **4.40**

---

## GAP_004: Disambiguating Aperiodic-as-Physiology from Aperiodic-as-Artifact in the Reward / 在獎勵中區分「非週期即生理」與「非週期即 artifact」

**Type / 類型:** Measurement (primary) + Methodological (secondary)
**Priority Rank / 優先排名:** #4

### Description / 描述

A naive FOOOF-SNR ("maximize periodic, minimize aperiodic") risks rewarding preprocessing that **destroys legitimate aperiodic neural signal**, because the aperiodic component is itself physiologically meaningful (Theme 3). No work defines how a preprocessing-quality reward should separate genuine aperiodic physiology (E/I balance, arousal) from aperiodic *contamination* (broadband EMG, drift, channel noise). This measurement-design gap must be resolved for GAP_001's metric to be valid. / 天真的 FOOOF-SNR（「最大化週期、最小化非週期」）有獎勵*破壞正當非週期神經訊號*的風險，因非週期成分本身具生理意義（主題 3）。無工作定義預處理品質獎勵該如何區分真實非週期生理與非週期*污染*。此測量設計缺口須解決，GAP_001 的指標才有效。

### Supporting Evidence / 支持證據

- **LendnerEtAl2020, HillEtAl2022, MerkinEtAl2022, ThuwalEtAl2021**: Collectively establish the aperiodic component as real, behaviorally-meaningful signal — so it cannot simply be minimized as "noise." / 共同確立非週期成分為真實、有行為意義的訊號——故不能僅當「雜訊」最小化。

- **GersterEtAl2022 (2022)**: Quantifies exactly when FOOOF mis-estimates the aperiodic part (fitting range, knees, overlapping peaks) — the failure modes a reward function must guard against with goodness-of-fit gating. / 量化 FOOOF 何時錯估非週期部分——獎勵函數須以擬合優度把關防範的失效模式。

### Counter-Evidence / 反面證據

> No paper in the collection addresses how to build a preprocessing reward that distinguishes physiological from artifactual aperiodic power. This is a genuinely open measurement-design problem — novel, but warranting care because it is the project's main validity risk.
>
> 文獻中無論文處理如何建構區分生理性與 artifact 性非週期功率的預處理獎勵。這是真正開放的測量設計問題——新穎，但需謹慎，因為它是計畫的主要效度風險。

### Why It Matters / 重要性

This is not a separate research program so much as the **risk that determines whether GAP_001 succeeds**. Surfaced only by reading the aperiodic-as-signal literature (Theme 3) against the FOOOF-as-metric idea (Theme 2), it is the kind of cross-theme tension that, if ignored, would silently invalidate the whole metric. Addressing it (e.g., band-limited periodic SNR, artifact-frequency-aware noise terms, fit-quality gating) is a required sub-contribution of GAP_001. / 這與其說是獨立研究計畫，不如說是**決定 GAP_001 成敗的風險**。若忽略，會悄悄使整個指標失效。處理它是 GAP_001 的必要子貢獻。

### Priority Score / 優先分數

| Axis / 評估軸 | Score / 分數 | Rationale / 理由 |
|--------------|-------------|-----------------|
| Severity / 嚴重性 | 4 | Determines validity of the core metric; not standalone-publishable / 決定核心指標效度；非獨立可發表 |
| Novelty / 新穎性 | 4 | Unaddressed, but a sub-problem rather than a new field / 未被處理，但屬子問題而非新領域 |
| Feasibility / 可行性 | 4 | Tractable via simulation + known artifact signatures / 可透過模擬與已知 artifact 特徵處理 |

**Composite / 綜合分:** 4 × 0.40 + 4 × 0.30 + 4 × 0.30 = 1.60 + 1.20 + 1.20 = **4.00**

---

## Gap Ranking / 缺口排名

| Rank | Gap | Type | Severity | Novelty | Feasibility | **Composite** | Project Pillar |
|------|-----|------|:--------:|:-------:|:-----------:|:-------------:|----------------|
| **1** | **GAP_001** FOOOF-SNR as objective function | Measurement+Integration | 5 | 5 | 4 | **4.70** | The metric (Pillar 2) |
| 2 | **GAP_003** LLM-agent preprocessing selection | Integration+Methodological | 5 | 5 | 3 | **4.40** | The agent (Pillar 3) |
| 3 | **GAP_002** Greedy-search ground-truth oracle | Methodological+Measurement | 4 | 5 | 4 | **4.30** | The ground truth (Pillar 1) |
| 4 | **GAP_004** Aperiodic signal-vs-artifact reward | Measurement+Methodological | 4 | 4 | 4 | **4.00** | The validity risk |

These four gaps are **not competing alternatives** — they are the components of one coherent program, which is itself the project. GAP_001 is the keystone; GAP_002 and GAP_003 are its two engineering arms (Pillars 1 and 3); GAP_004 is the risk GAP_001 must internalize to be valid.

這四個缺口**並非互斥替代方案**——它們是一個連貫計畫的組成部分，而該計畫本身就是本研究。GAP_001 是拱心石；GAP_002 與 GAP_003 是其兩個工程之臂；GAP_004 是 GAP_001 為求有效須內化的風險。

---

> [!important] ⛳ Checkpoint 3: 戰場選擇 / Battlefield Selection
>
> Choose which gap to lock as the research focus for the hypothesis step (Step 8). You have two natural options:
>
> **Option A — Lock GAP_001 alone** (narrow, low-risk first paper): scope the project to "FOOOF-SNR is a valid, label-free preprocessing quality metric," validated against the greedy-search oracle (GAP_002) as supporting evidence, deferring the LLM agent. Fastest path to a publishable result; de-risks the premise before building the agent.
>
> **Option B — Lock GAP_001 + GAP_002 + GAP_003 as one program** (the full vision): the complete agentic pipeline, with GAP_004 handled as a design constraint inside the metric. Higher ambition and impact, longer timeline, depends on the metric holding up.
>
> 請選擇要鎖定哪個缺口作為假說步驟（Step 8）的研究焦點：
> **選項 A——只鎖 GAP_001**（窄、低風險的第一篇）：先驗證「FOOOF-SNR 是有效、無標籤的預處理品質指標」，以貪婪搜尋 oracle 為佐證，暫緩 LLM 智能體。最快達到可發表結果，先去風險再建 agent。
> **選項 B——鎖 GAP_001 + GAP_002 + GAP_003 為一個計畫**（完整願景）：完整智能體式管線，GAP_004 作為指標內的設計約束處理。企圖與影響更大、時程更長、取決於指標是否成立。
>
> Tell me e.g. **"Lock GAP_001"** or **"Lock GAP_001 + GAP_002 + GAP_003"** (or your own combination), and I'll carry it into Step 8 — but per your instruction this run **stops here at Step 7**.
>
> 告訴我例如 **「Lock GAP_001」** 或 **「Lock GAP_001 + GAP_002 + GAP_003」**，我會帶入 Step 8——但依你的指示，本次執行**在 Step 7 停止**。

> Files / 檔案: `step7_gap_analysis.md`
> Next step (when you resume) / 下一步（恢復時）: `/research-hypothesis` (Step 8, Checkpoint 4)
