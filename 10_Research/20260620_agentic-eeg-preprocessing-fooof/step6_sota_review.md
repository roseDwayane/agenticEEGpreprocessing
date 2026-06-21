---
session_id: "20260620"
topic: "Agentic EEG preprocessing pipeline with FOOOF-based signal quality optimization"
date: "2026-06-20"
step: 6
total_papers: 53
themes: 5
full_text_papers: 6
abstract_only_papers: 47
---

# State-of-the-Art Review / 文獻綜述

> Topic / 研究主題: Agentic EEG preprocessing pipeline with FOOOF-based signal quality optimization
> 53 papers across 5 themes / 53 篇論文，5 個主題

> [!info] Partial full-text synthesis / 部分全文綜整
> 6 of the most novelty-defining papers were read in full (Delorme2023, GersterEtAl2022, MartinezEtAl2023, GuoEtAl2024, TriratEtAl2024, ChiEtAl2024); the remaining 47 were synthesized from verbatim abstracts plus citation-network structure. Quantitative claims drawn from abstract-only papers are attributed conservatively.
> 6 篇最能定義創新縫隙的論文已讀全文，其餘 47 篇以摘要與引用網路結構綜整；來自僅摘要論文的定量論點以保守方式標註。

---

## Executive Summary / 摘要總覽

This review maps the literature at the intersection of three research strands that the proposed project unites for the first time: (1) **automated EEG preprocessing**, where the field has built many fixed pipelines but lacks a principled way to *choose* among their parameters; (2) **FOOOF/specparam spectral parameterization**, which gives a physiologically grounded way to separate "signal" (periodic oscillations) from "noise" (aperiodic 1/f activity) but has never been used as the *objective function* for preprocessing; and (3) **AutoML and LLM-agent pipeline optimization**, which has matured rapidly for generic ML/data-science but has barely touched neurophysiological preprocessing. The central, repeatedly-documented problem is that *EEG results are highly sensitive to preprocessing choices* (RobbinsEtAl2020), yet the field evaluates preprocessing almost exclusively by **downstream task accuracy** or by **subjective visual inspection** — both of which the proposed FOOOF-SNR metric would replace with a label-free, task-independent quality signal.

本綜述描繪三條研究脈絡交會處的文獻，而本計畫首次將其統合：(1) **自動化 EEG 預處理**——已有許多固定管線，卻缺乏有原則地「挑選」參數的方法；(2) **FOOOF/specparam 頻譜參數化**——提供以生理為基礎、分離「訊號」（週期性振盪）與「雜訊」（非週期性 1/f 活動）的方式，但從未被用作預處理的「目標函數」；(3) **AutoML 與 LLM 智能體管線最佳化**——已在通用 ML/資料科學快速成熟，卻幾乎未觸及神經生理預處理。核心且反覆被記錄的問題是：*EEG 結果對預處理選擇高度敏感*（RobbinsEtAl2020），然而領域幾乎只用**下游任務準確率**或**主觀目視檢查**來評估預處理——而本計畫提出的 FOOOF-SNR 指標正可用無標籤、任務無關的品質訊號取代兩者。

---

## Theme 1: Standardized & Automated EEG Preprocessing Pipelines / 標準化與自動化 EEG 預處理管線

**Papers / 論文:** BigdelyShamloEtAl2015, GabardDurnamEtAl2018, JasEtAl2017, PionTonachiniEtAl2019, PedroniEtAl2019, LiEtAl2022, LopezEtAl2022, MonachinoEtAl2022, KumaravelEtAl2022, MayeliEtAl2021, vilaEtAl2023, MutanenEtAl2018, BlumEtAl2019

This theme is the **action space** the proposed agent operates over. Over the past decade the community converged on a canonical sequence of preprocessing operations — robust referencing and bad-channel detection (PREP, BigdelyShamloEtAl2015), high-pass/line-noise filtering, ICA decomposition with automated component classification (ICLabel, PionTonachiniEtAl2019; its Python port LiEtAl2022), automated bad-trial rejection (Autoreject, JasEtAl2017), and Artifact Subspace Reconstruction for transient artifacts (rASR, BlumEtAl2019). These primitives are bundled into full pipelines: HAPPE and its variants for developmental/low-density/ERP data (GabardDurnamEtAl2018, LopezEtAl2022, MonachinoEtAl2022), NEAR for neonates (KumaravelEtAl2022), APPEAR for EEG-fMRI (MayeliEtAl2021), Automagic (PedroniEtAl2019), and DISCOVER-EEG for resting-state feature extraction (vilaEtAl2023).

此主題是所提出 agent 操作的**動作空間**。過去十年社群收斂出一套標準的預處理操作序列——穩健重參考與壞通道偵測（PREP）、高通/線雜訊濾波、ICA 分解搭配自動成分分類（ICLabel 及其 Python 版本）、自動壞試次移除（Autoreject）、以及處理瞬態 artifact 的 ASR（rASR）。這些基本元件被組裝成完整管線：HAPPE 系列、NEAR、APPEAR、Automagic、DISCOVER-EEG。

**a) Consensus / 共識.** Automation is necessary and beneficial for large/heterogeneous datasets — manual rejection does not scale and is subjective (GabardDurnamEtAl2018, PedroniEtAl2019). ICA-based component classification and ASR are now standard tools, and HAPPE-style benchmarking against alternatives is an accepted validation pattern. / 對大型異質資料而言，自動化是必要且有益的；手動移除無法規模化且主觀。ICA 成分分類與 ASR 已成標準工具。

**b) Debates / 爭議.** What counts as a "good" pipeline is unresolved. PedroniEtAl2019 explicitly notes "there is no method to objectively quantify the quality of preprocessed EEG," which fosters p-hacking via subjective subject exclusion. This **lack of an objective quality target is the gap this entire project addresses** (see Theme 2 and Theme 5). / 何謂「好」管線仍無定論。Automagic 明言「沒有方法能客觀量化預處理後 EEG 的品質」，導致透過主觀剔除受試者而 p-hacking。這個**缺乏客觀品質目標的問題正是本計畫要解決的缺口**。

**c) Dominant methods / 主要方法.** MATLAB/EEGLAB and Python/MNE ecosystems; fixed parameter defaults with at most a few tunable thresholds; validation by artifact-removal sensitivity/specificity or by comparison against manual editing. / MATLAB/EEGLAB 與 Python/MNE 生態；固定預設參數，至多少數可調閾值；以 artifact 移除敏感度/特異度或與手動編輯比較來驗證。

**d) Key point for the project / 對計畫的關鍵.** Each pipeline exposes a handful of parameters (filter cutoffs, ICA rejection thresholds, ASR cutoff `k`, bad-channel criteria). The combinatorial space of these is exactly what greedy/exhaustive search would enumerate to build ground truth, and what the agent would learn to navigate. / 每條管線都暴露少數參數（濾波截止、ICA 拒絕閾值、ASR 截止 `k`、壞通道準則）。這些的組合空間正是貪婪/窮舉搜尋用以建立 ground truth、agent 需學會導航的對象。

---

## Theme 2: FOOOF / Spectral Parameterization as a Signal–Noise Decomposition / FOOOF 頻譜參數化作為訊號–雜訊分解

**Papers / 論文:** DonoghueEtAl2020b, HallerEtAl2018, WenLiu2016, GersterEtAl2022, OstlundEtAl2022, WilsonEtAl2022, DonoghueEtAl2020a

This theme supplies the project's **quality metric**. DonoghueEtAl2020b (FOOOF / specparam) established the now-dominant framework: a neural power spectrum is modeled as an **aperiodic component** (a 1/f-like background parameterized by an offset and exponent) plus a small number of **periodic components** (Gaussian-shaped oscillatory peaks). HallerEtAl2018 is its precursor; WenLiu2016 (IRASA) is the principal alternative that separates fractal from oscillatory power by resampling. The crucial methodological caution comes from GersterEtAl2022, who systematically compared FOOOF and IRASA on EEG/MEG/LFP and quantified where each *fails* — narrow fitting ranges, overlapping peaks, and knee/bend artifacts all distort the aperiodic estimate.

此主題提供計畫的**品質指標**。FOOOF/specparam（DonoghueEtAl2020b）建立了當前主流框架：神經功率譜被建模為一個**非週期成分**（以 offset 與 exponent 參數化的 1/f 背景）加上少數**週期成分**（高斯形振盪峰）。HallerEtAl2018 為其前身；IRASA（WenLiu2016）是以重採樣分離碎形與振盪功率的主要替代法。關鍵的方法學警示來自 GersterEtAl2022——系統比較 FOOOF 與 IRASA，量化各自失效之處（過窄擬合範圍、重疊峰、knee/bend artifact 都會扭曲非週期估計）。

**a) Consensus / 共識.** The aperiodic component is not "noise to be ignored" — it is physiologically meaningful and dynamically modulated. Conflating it with oscillatory power produces spurious findings: DonoghueEtAl2020a shows the classic theta/beta band-ratio is actually driven mostly by aperiodic differences, not oscillations. / 非週期成分並非「該被忽略的雜訊」——它具生理意義且會動態調節。將其與振盪功率混淆會產生假發現：theta/beta 比值其實主要由非週期差異驅動。

**b) Debates / 爭議.** FOOOF vs IRASA, and how robust either is to preprocessing and fitting choices (GersterEtAl2022, OstlundEtAl2022). This matters directly: **if the aperiodic/periodic split is sensitive to preprocessing, then using it as a preprocessing-quality metric requires care** — the metric and the thing it judges are coupled. The project must therefore define the FOOOF-SNR carefully (e.g., fixed fitting range, knee handling, goodness-of-fit gating per GersterEtAl2022). / FOOOF 與 IRASA 之爭，以及兩者對預處理與擬合選擇的穩健度。這直接相關：**若非週期/週期分解對預處理敏感，則用它作為預處理品質指標需謹慎**——指標與被評估對象耦合。計畫須謹慎定義 FOOOF-SNR（固定擬合範圍、knee 處理、依 GersterEtAl2022 做擬合優度把關）。

**c) Reframing for the project / 計畫的重新框定.** No paper in this theme uses FOOOF as an *objective function*. They use it descriptively (parameterize a spectrum, report exponent/offset). The project's novelty is to invert this: treat **periodic power as signal, aperiodic+residual as noise, and their ratio as an SNR reward** that preprocessing should maximize. WilsonEtAl2022 (SPRiNT, time-resolved parameterization) is relevant because it enables windowed quality scoring rather than a single static estimate. / 此主題無任何論文將 FOOOF 用作*目標函數*；皆為描述性使用。計畫的創新是反轉此用途：將**週期功率視為訊號、非週期+殘差視為雜訊，其比值作為預處理應最大化的 SNR 獎勵**。SPRiNT 的時間解析參數化讓窗化品質評分成為可能。

---

## Theme 3: Aperiodic Activity as a Validated Neural Signal / 非週期活動作為已驗證的神經訊號

**Papers / 論文:** LendnerEtAl2020, HillEtAl2022, MerkinEtAl2022, ThuwalEtAl2021 (+ borderline biomarker papers)

This theme provides the **external validity argument** for the FOOOF-SNR idea: the aperiodic component carries real, behaviorally- and clinically-meaningful information, which is why preserving it (rather than filtering it away as "noise") matters. LendnerEtAl2020 shows the 1/f slope distinguishes wakefulness from anesthesia and sleep stages; HillEtAl2022 and MerkinEtAl2022 show systematic aperiodic changes across development and aging; ThuwalEtAl2021 links periodic and aperiodic components to distinct facets of cognition.

此主題為 FOOOF-SNR 構想提供**外部效度論證**：非週期成分攜帶真實、與行為及臨床相關的資訊，因此「保留它」（而非當作雜訊濾除）很重要。1/f 斜率可區分清醒、麻醉與睡眠階段；非週期成分隨發展與老化系統性變化；週期與非週期成分連結到認知的不同面向。

**a) Consensus / 共識.** The aperiodic exponent/offset is a genuine neural marker (arousal, age, E/I balance), reproducible across labs and recording modalities. / 非週期 exponent/offset 是真實的神經標記（喚醒、年齡、E/I 平衡），跨實驗室與記錄模態可重現。

**b) Tension for the project / 對計畫的張力.** This is a double-edged sword. Because the aperiodic part is *real signal*, a naive "maximize periodic / minimize aperiodic" SNR could wrongly reward preprocessing that *destroys* legitimate aperiodic information. The project must distinguish **aperiodic-as-physiology** from **aperiodic-as-artifact** (e.g., EMG raises high-frequency aperiodic power; line noise adds periodic peaks). This nuance — surfaced by reading Theme 3 against Theme 2 — is a key design constraint for the reward function. / 這是雙面刃。因非週期部分是*真實訊號*，天真的「最大化週期/最小化非週期」SNR 可能錯誤獎勵*破壞*正當非週期資訊的預處理。計畫須區分**非週期即生理**與**非週期即 artifact**（如 EMG 抬升高頻非週期功率；線雜訊加入週期峰）。此細節是獎勵函數的關鍵設計約束。

---

## Theme 4: Preprocessing Sensitivity & Objective Quality Assessment / 預處理敏感度與客觀品質評估

**Papers / 論文:** RobbinsEtAl2020, Delorme2023, CoelliEtAl2023, MutanenEtAl2018

This theme is the **direct motivation** for the whole project and the closest prior art on "evaluating preprocessing without downstream accuracy." RobbinsEtAl2020 (IEEE TNSRE) quantifies how much downstream EEG measures change as a function of preprocessing choices — establishing that the choice *matters* and is currently made ad hoc. Delorme2023 ("EEG is better left alone") is the pivotal paper: it builds an explicit, label-light quality metric (percentage of significant channels in a post-stimulus window) and uses it to compare optimized pipelines across EEGLAB/FieldTrip/MNE/Brainstorm — finding, provocatively, that **most automated correction steps either don't help or actively hurt**, with only high-pass filtering and bad-channel interpolation reliably beneficial. CoelliEtAl2023 manually performs the per-step comparison the project would automate, proposing quantitative indices for choosing among methods.

此主題是整個計畫的**直接動機**，也是「不靠下游準確率評估預處理」的最接近前作。RobbinsEtAl2020 量化預處理選擇如何改變下游 EEG 指標；Delorme2023（「EEG is better left alone」）是樞紐論文：建立明確、近乎無標籤的品質指標，比較各軟體的最佳化管線，挑釁地發現**多數自動校正步驟不是沒幫助就是有害**，僅高通濾波與壞通道內插可靠有益。CoelliEtAl2023 手動執行計畫欲自動化的逐步比較。

**a) Consensus / 共識.** Preprocessing choices materially change results; a quality metric is needed; and "more correction" is not monotonically better (Delorme2023). / 預處理選擇實質改變結果；需要品質指標；「更多校正」並非單調更好。

**b) Debates / 爭議.** Which quality metric? Delorme2023 uses a condition-contrast channel metric (requires two conditions / events); the project's FOOOF-SNR is **task- and label-free**, applicable to resting-state and single recordings — a complementary and arguably more general target. Whether automated correction helps at all (Delorme2023's skeptical finding) is itself a hypothesis the project's ground-truth search can re-test at scale. / 該用哪個品質指標？Delorme2023 用條件對比的通道指標（需兩個條件/事件）；計畫的 FOOOF-SNR **任務與標籤無關**，適用靜息態與單筆記錄——互補且可說更通用。自動校正是否有幫助（Delorme2023 的懷疑發現）本身就是計畫 ground-truth 搜尋可大規模重新檢驗的假設。

**c) Bridge / 橋接.** This theme bridges Theme 1 (the pipelines) and Theme 2 (the metric): it is the small body of work already asking "how do we judge preprocessing objectively?" — but none of it uses FOOOF, and none of it *searches* or *learns* the parameters. / 此主題橋接主題 1（管線）與主題 2（指標）：已在問「如何客觀評判預處理」，但無一使用 FOOOF，也無一*搜尋*或*學習*參數。

---

## Theme 5: Pipeline Optimization — From AutoML/HPO to LLM Agents / 管線最佳化——從 AutoML/HPO 到 LLM 智能體

**Papers / 論文 (AutoML/HPO):** ThorntonEtAl2012, FeurerEtAl2015, FeurerEtAl2020, BischlEtAl2023, GreenhillEtAl2020, SantuEtAl2020, CooneyEtAl2020, BashashatiEtAl2016, NeutatzEtAl2022, SiddiqiEtAl2023, MartinezEtAl2023
**Papers / 論文 (LLM agents):** WangEtAl2024, WuEtAl2023, ShenEtAl2023, HuangEtAl2023, HollmannEtAl2023, GuoEtAl2024, JiangEtAl2025, TriratEtAl2024, ChiEtAl2024, HongEtAl2024, ZhangEtAl2023, TsaiEtAl2023, BoikoEtAl2023, BranEtAl2024, LuEtAl2024

This theme supplies the project's **method machinery** and shows a clear two-generation trajectory. **Generation 1 — classical AutoML/HPO:** Auto-WEKA (ThorntonEtAl2012) and Auto-sklearn (FeurerEtAl2015, FeurerEtAl2020) frame model+hyperparameter selection as a single Bayesian-optimization search, with BischlEtAl2023 as the modern HPO reference. Critically, two papers extend this to *data-side* decisions: NeutatzEtAl2022 ("would an optimizer choose to clean?") and SiddiqiEtAl2023 (SAGA, optimizing data-cleaning pipelines) — the closest analogues to optimizing preprocessing. BashashatiEtAl2016 already applied Bayesian optimization to *per-user BCI pipelines*, the nearest EEG-specific precedent. MartinezEtAl2023 ("Towards Personalized Preprocessing Pipeline Search") is the single most on-target prior work: it searches a *per-instance* preprocessing pipeline — but for generic ML data, not EEG, and without a FOOOF-style quality target.

此主題提供計畫的**方法機制**，並呈現清楚的兩代軌跡。**第一代——古典 AutoML/HPO：** Auto-WEKA 與 Auto-sklearn 將模型+超參數選擇框定為單一貝氏最佳化搜尋。關鍵地，兩篇延伸到*資料端*決策：NeutatzEtAl2022 與 SAGA（SiddiqiEtAl2023）——最接近「最佳化預處理」的類比。BashashatiEtAl2016 已將貝氏最佳化用於*每使用者 BCI 管線*，是最接近的 EEG 專屬前例。MartinezEtAl2023（「個人化預處理管線搜尋」）是最切題的單一前作：搜尋*逐實例*預處理管線——但針對通用 ML 資料而非 EEG，且無 FOOOF 式品質目標。

**Generation 2 — LLM agents.** The field is rapidly moving from fixed search to **LLM-driven agents that reason, use tools, and explore**. WangEtAl2024 surveys the design space; WuEtAl2023 (AutoGen) and ShenEtAl2023 (HuggingGPT) provide multi-agent / tool-orchestration substrates; HuangEtAl2023 (MLAgentBench) benchmarks them. For data science specifically: HollmannEtAl2023 (CAAFE, LLM feature engineering), GuoEtAl2024 (DS-Agent, case-based-reasoning loop), HongEtAl2024 (Data Interpreter), and three search-augmented agents — JiangEtAl2025 (AIDE, tree search over code), TriratEtAl2024 (AutoML-Agent, multi-agent with verification), ChiEtAl2024 (SELA, MCTS-guided LLM agent). The autonomous-science archetypes BoikoEtAl2023 (Coscientist), BranEtAl2024 (ChemCrow), and LuEtAl2024 (AI Scientist) show LLM agents closing the full hypothesize→experiment→evaluate loop with domain tools.

**第二代——LLM 智能體。** 領域正從固定搜尋快速轉向**會推理、用工具、能探索的 LLM 驅動智能體**。AutoGen、HuggingGPT 提供多智能體/工具編排基底；MLAgentBench 做基準。資料科學專門：CAAFE、DS-Agent、Data Interpreter，以及三個搜尋增強智能體——AIDE（程式碼樹搜尋）、AutoML-Agent（多智能體含驗證）、SELA（MCTS 引導）。自主科學原型 Coscientist、ChemCrow、AI Scientist 展示 LLM 智能體閉合「假設→實驗→評估」迴圈。

**a) Consensus / 共識.** Pipeline/hyperparameter selection is automatable and benefits from search; LLM agents with tools + search (tree/MCTS/CBR) now match or beat hand-tuned ML-engineering on benchmarks. / 管線/超參數選擇可自動化且受益於搜尋；具工具+搜尋的 LLM 智能體在基準上已匹敵或超越手調。

**b) The unfilled cell / 未填的格子.** Across all 26 method papers, **none targets EEG/neurophysiological preprocessing, and none uses a spectral signal-quality reward.** Generation-1 EEG work (BashashatiEtAl2016) predates FOOOF and optimizes for task accuracy; Generation-2 agents operate on tabular/code/chemistry, never on raw electrophysiology. This is the precise seam the project occupies. / 在全部 26 篇方法論文中，**無一鎖定 EEG/神經生理預處理，也無一使用頻譜訊號品質作為獎勵。** 第一代 EEG 工作早於 FOOOF 且以任務準確率最佳化；第二代智能體operate 於表格/程式碼/化學，從不在原始電生理上。這正是計畫佔據的縫隙。

---

## Cross-Theme Synthesis / 跨主題綜整

**a) Methodological trend over time / 方法隨時間趨勢.** Preprocessing (Theme 1) matured 2015–2022 into fixed automated pipelines; spectral parameterization (Theme 2) became standard after 2020; pipeline optimization (Theme 5) shifted from Bayesian-opt AutoML (2012–2020) to LLM agents (2023–2025). The three timelines have run in **parallel and disconnected** — the project connects their leading edges (FOOOF metric + LLM-agent search + EEG preprocessing action space). / 三條時間線**平行而未連接**——計畫連接其前緣。

**b) Converging finding / 收斂發現.** Two independent literatures (Theme 1's PedroniEtAl2019 and Theme 4's Delorme2023) converge on the same complaint: *there is no objective, label-free quality target for EEG preprocessing.* This convergence is the strongest single justification for the project. / 兩個獨立文獻收斂於同一抱怨：*EEG 預處理沒有客觀、無標籤的品質目標。* 這是計畫最強的單一正當理由。

**c) Diverging finding / 分歧發現.** Theme 3 says "preserve the aperiodic component, it's real signal"; a naive reading of Theme 2's SNR framing says "suppress the aperiodic component, it's noise." Resolving this tension — defining a FOOOF-SNR that rewards artifact removal *without* penalizing legitimate aperiodic physiology — is the core scientific design problem the project must solve. / 主題 3 說「保留非週期成分」；對主題 2 的天真解讀說「抑制非週期成分」。化解此張力是計畫須解決的核心科學設計問題。

**d) Bridge papers / 橋接論文.** MartinezEtAl2023 (Theme 5 ↔ Theme 1: pipeline search applied to preprocessing, but non-EEG), Delorme2023 (Theme 4 ↔ Theme 2: objective preprocessing quality, but non-FOOOF), GersterEtAl2022 (Theme 2 ↔ Theme 3: how the metric can break). These three define the project's coordinates. / 這三篇定義計畫座標。

---

## Implications for Gap Analysis (Step 7) / 對缺口分析的啟示

1. **Empty intersection cell** — FOOOF-SNR × agentic search × EEG preprocessing is unoccupied. / FOOOF-SNR × 智能體搜尋 × EEG 預處理的交集為空。
2. **No label-free preprocessing reward in use** — current evaluation is accuracy- or contrast-based (RobbinsEtAl2020, Delorme2023). / 目前評估皆基於準確率或條件對比。
3. **No ground-truth oracle via exhaustive search exists for EEG preprocessing** — the greedy-search dataset the project would build does not exist in the literature. / 文獻中不存在以窮舉搜尋建立的 EEG 預處理 ground-truth oracle。
4. **Aperiodic-as-signal vs aperiodic-as-noise must be disambiguated** in any FOOOF reward (GersterEtAl2022 + Theme 3). / 任何 FOOOF 獎勵都須消解非週期即訊號 vs 即雜訊。
5. **LLM agents are proven on tabular/code/chemistry but untested on electrophysiological preprocessing** — transfer is plausible but unproven. / LLM 智能體已在表格/程式碼/化學證明，但未在電生理預處理測試。

> Files / 檔案: `step6_sota_review.md`, `step6_knowledge_graph.canvas`
> Next step / 下一步: `/research-gaps` (Checkpoint 3: 戰場選擇)
