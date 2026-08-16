---
session_id: "20260815"
topic: "LLM-informed warm-starting and prior injection for Bayesian optimization (CASH) of subject-specific EEG motor-imagery preprocessing pipelines, with cross-subject transfer"
date: "2026-08-15"
step: 7
gaps_identified: 3
priority_weights: "severity=0.40, novelty=0.30, feasibility=0.30"
---

# Gap Analysis / 研究缺口分析

> Topic / 研究主題: LLM-informed warm-starting and prior injection for BO (CASH) of subject-specific EEG MI pipelines, with cross-subject transfer
> Papers analyzed / 分析論文數: 124 (from SOTA review; 40 full-text)
> Gaps identified / 已識別缺口: 3
> Date / 日期: 2026-08-15

## Executive Summary / 總覽摘要

The literature is strong on every *component* of the research question and empty at its *intersection*. Warm-starting mechanisms for Bayesian optimization are mature (meta-feature initialization since FeurerEtAl2015, principled prior injection in HvarfnerEtAl2022, transfer frameworks in LiEtAl2022c and BaiEtAl2023); decomposed CASH systems are mature and explicitly ask for warm-starting as their next step (LiEtAl2021b, LiEtAl2020a, LiEtAl2016); and the LLM-for-AutoML strand has converged on the exact role the research question assigns to the LLM — cross-task prior and cold-start initializer rather than standalone optimizer (LiuEtAl2024, RychertEtAl2025, RodriguesEtAl2026). On the application side, however, EEG pipeline automation runs almost entirely on cold-start evolutionary search, and cross-subject EEG work transfers model weights but never configuration or search knowledge. The three gaps below are ordered fragments of this one asymmetry: the integration gap itself (GAP_001), the zero-shot configuration-transfer question inside it (GAP_002), and the measurement protocol needed to make any claimed gain credible (GAP_003).

文獻在研究問題的每個「組件」上都很強，但在其「交集」處是空的。BO 暖啟動機制已成熟（自 FeurerEtAl2015 的 meta-feature 初始化、HvarfnerEtAl2022 的先驗注入、LiEtAl2022c 與 BaiEtAl2023 的遷移框架）；分解式 CASH 系統已成熟且明確把暖啟動列為下一步（LiEtAl2021b, LiEtAl2020a, LiEtAl2016）；LLM×AutoML 一系則已收斂到研究問題賦予 LLM 的角色——跨任務先驗與冷啟動初始化器，而非獨立最佳化器（LiuEtAl2024, RychertEtAl2025, RodriguesEtAl2026）。但在應用端，EEG 管線自動化幾乎全靠冷啟動演化式搜尋，而跨受試者 EEG 研究遷移的是模型權重、從未遷移配置或搜尋知識。以下三個缺口是同一不對稱的有序切片：整合缺口本身（GAP_001）、其中的零樣本配置遷移問題（GAP_002），以及讓任何宣稱增益可信所需的量測協議（GAP_003）。

---

## GAP_001: Knowledge-Injected Warm-Starting of Decomposed CASH for Subject-Specific EEG Pipelines / 知識注入式暖啟動應用於受試者特定 EEG 管線的分解式 CASH

**Type / 類型:** Integration（整合缺口）(secondary: Methodological 方法學)
**Priority Rank / 優先排名:** #1

### Description / 描述

No identified study injects transferable knowledge — LLM-generated priors, or search histories from other subjects — into Bayesian-optimization-based CASH over a full EEG preprocessing + feature-extraction + classification pipeline. EEG pipeline searches start cold for every subject, and mainstream AutoML's principal engine (BO with decomposition) has not crossed into EEG at all: in the 11 Theme-4 papers, evolutionary methods dominate (6/11) and BO appears only as a beaten baseline.

尚無研究將可遷移知識——LLM 生成的先驗、或其他受試者的搜尋歷史——注入以 BO 為基礎、涵蓋完整 EEG「前處理＋特徵萃取＋分類」管線的 CASH 最佳化。EEG 管線搜尋對每位受試者都是冷啟動，而主流 AutoML 的核心引擎（分解式 BO）完全未跨入 EEG：主題四的 11 篇論文中演化式方法佔 6 篇，BO 僅以被擊敗的 baseline 出現。

### Supporting Evidence / 支持證據

- **Theme 4 collectively (11 papers)**: Zero papers combine LLM guidance or warm-started search with EEG pipeline optimization; NakisaEtAl2018 includes TPE only as a baseline that differential evolution beats. / 主題四全體：無任何論文結合 LLM 引導或暖啟動搜尋與 EEG 管線最佳化；NakisaEtAl2018 的 TPE 僅是被差分演化擊敗的 baseline。
- **ZhangEtAl2024**: Cold-restarts its metaheuristic HPO for every dataset — search knowledge is discarded between runs. / 每個資料集都重新冷啟動其 metaheuristic HPO——搜尋知識在批次之間被丟棄。
- **BerdyshevEtAl2024**: Reports ~24 hours per automated HPO setting for BCI meta-learning, and names cutting this cost as future work. / 回報 BCI meta-learning 的自動化 HPO 每一設定約需 24 小時，並將降低此成本列為未來工作。
- **LiEtAl2016, LiEtAl2020a, LiEtAl2022a**: Hyperband's, Rising Bandits' and VolcanoML's own conclusions independently name meta-learned warm-starting/transfer as the next acceleration lever — the systems side is asking for exactly this integration. / Hyperband、Rising Bandits 與 VolcanoML 的結論各自獨立點名 meta-learning 暖啟動／遷移為下一個加速槓桿——系統端正在要求這個整合。
- **KapitonovaBall2024**: The ChatBCI LLM-agent paper's future-work section explicitly calls for adding LLM-driven hyperparameter optimization and NAS to BCI toolchains. / ChatBCI 論文的未來工作明確呼籲在 BCI 工具鏈中加入 LLM 驅動的 HPO 與 NAS。
- **FeurerEtAl2015 + HvarfnerEtAl2022**: The mechanisms (MI-SMAC meta-feature init; πBO prior injection with wrong-prior recovery) exist and are validated — on tabular/vision tasks, never on EEG subjects-as-tasks. / 機制已存在且經驗證（MI-SMAC 的 meta-feature 初始化；πBO 具錯誤先驗恢復的先驗注入）——但驗證於表格／視覺任務，從未把「EEG 受試者」當作任務。

### Counter-Evidence / 反面證據

- **AnarakiEtAl2024**: Predicts a personalized classifier from dataset structural characteristics → selection of a single component, not warm-starting of a pipeline search; no search loop involved. / 由資料集結構特徵預測個人化分類器→僅選擇單一元件，非管線搜尋的暖啟動；不涉及搜尋迴圈。
- **BerdyshevEtAl2024**: Transfers meta-learned model initializations across subjects and implicitly reuses fine-tuning hyperparameters chosen on non-target subjects → transfers weights, not search/configuration knowledge; HPO itself remains cold. / 跨受試者遷移 meta-learning 模型初始化，並隱含重用非目標受試者選出的微調超參數→遷移的是權重而非搜尋／配置知識；HPO 本身仍是冷啟動。
- **XuEtAl2026a (CoFEH)**: Demonstrates LLM-conditioned SMAC for CASH with avg rank 1.75 — but on tabular data only, with no subject/task-transfer dimension. / 展示 LLM 條件化 SMAC 的 CASH（平均排名 1.75）——但僅限表格資料，無受試者／任務遷移維度。

### Why It Matters / 重要性

Per-subject pipeline re-optimization is the computational face of the BCI calibration problem: subject-specific pipelines demonstrably outperform pooled ones (Theme 5's inter-subject variability evidence), but nobody can afford a cold 24-hour search per user. If knowledge injection cuts evaluations-to-target the way it does in tabular AutoML (MI-SMAC's gain exceeding SMAC-vs-random; πBO's 12.5× speedup), subject-specific pipeline optimization becomes deployable rather than academic. The gap also carries methodological weight beyond BCI: EEG subjects form a natural family of related-but-shifted tasks — a realistic testbed for transfer-HPO that tabular benchmarks cannot provide (EggenspergerEtAl2021 names transfer-HPO benchmarking as an open problem).

逐受試者管線重新最佳化是 BCI 校正問題的計算面：受試者特定管線明顯優於合併管線（主題五的受試者間變異證據），但沒有人負擔得起每位使用者 24 小時的冷啟動搜尋。若知識注入能如表格 AutoML 中那樣降低達標評估次數（MI-SMAC 的增益超過 SMAC 對隨機搜尋的增益；πBO 的 12.5× 加速），受試者特定管線最佳化就從學術題變成可部署技術。此缺口也有超出 BCI 的方法學價值：EEG 受試者構成天然的「相關但偏移」任務家族——是表格基準無法提供的 transfer-HPO 現實測試場（EggenspergerEtAl2021 將 transfer-HPO 基準列為開放問題）。

### Priority Score / 優先分數

| Axis / 評估軸 | Score / 分數 | Rationale / 理由 |
|--------------|-------------|-----------------|
| Severity / 嚴重性 | 4 | Cold-start cost is the named bottleneck on both the systems side and the BCI side / 冷啟動成本是系統端與 BCI 端共同點名的瓶頸 |
| Novelty / 新穎性 | 5 | Zero papers at the intersection across 124 screened works / 124 篇文獻中交集為零 |
| Feasibility / 可行性 | 5 | All components open-source (SMAC3/OpenBox/VolcanoML, πBO mechanism, MOABB/EEGMMI datasets); pilot data already exists in this lab / 全部元件開源；本實驗室已有 pilot 數據 |

**Composite / 綜合分:** 4 × 0.40 + 5 × 0.30 + 5 × 0.30 = 1.60 + 1.50 + 1.50 = **4.60**

---

## GAP_002: Zero-Shot and Similarity-Gated Configuration Transfer Across Subjects / 跨受試者的零樣本與相似度門控配置遷移

**Type / 類型:** Methodological（方法學缺口）(secondary: Population 族群)
**Priority Rank / 優先排名:** #2

### Description / 描述

No study quantifies how far a *configuration* (pipeline + hyperparameters) searched on source subjects carries to an unseen subject with zero further search, nor when transfer should be gated or decayed. Zero-calibration EEG decoding exists at the model-weight level (Riemannian alignment, domain adaptation, meta-learning), but the configuration level — which would eliminate search cost entirely for acceptable-accuracy use cases — is unmeasured, despite documented outlier subjects whose features deviate up to ~30-fold from group means, which makes ungated transfer risky.

尚無研究量化在來源受試者上搜尋所得的「配置」（管線＋超參數）零樣本套用到未見過受試者能達到什麼水準，也未探討遷移何時應被門控或衰減。零校正 EEG 解碼在模型權重層級已存在（Riemannian 對齊、域適應、meta-learning），但配置層級——對可接受準確率的使用情境可完全省去搜尋成本——仍未被量測；且文獻記錄了特徵偏離群體均值可達約 30 倍的離群受試者，使無門控的遷移具有風險。

### Supporting Evidence / 支持證據

- **Theme 5 collectively**: All transfer operates on model weights/representations (alignment, domain adaptation, Reptile-style initialization); no paper transfers configurations. / 主題五全體：所有遷移都在模型權重／表徵層級，無論文遷移配置。
- **BerdyshevEtAl2024**: Few-shot meta-learning ceiling is sobering (4-class MI zero-shot 43%±7%, 16-sample fine-tune 46%±5% vs ~84% full subject-specific in SchirrmeisterEtAl2017) — model-level transfer alone leaves a large gap that configuration-level adaptation could attack from the other side. / few-shot 天花板令人警醒——純模型層級遷移留下大缺口，配置層級調適可從另一側進攻。
- **ZhangEtAl2024 + BerdyshevEtAl2024**: Both document extreme outlier subjects (feature deviations up to ~30-fold), directly motivating similarity gating before transfer. / 兩者都記錄極端離群受試者，直接支持遷移前的相似度門控。
- **NomuraEtAl2021**: Similarity-gated warm-starting with safe fallback exists as a mechanism (warm-CMA-ES) but has never been instantiated with subjects as tasks. / 相似度門控暖啟動機制已存在（warm-CMA-ES），但從未以受試者作為任務實例化。
- **HvarfnerEtAl2026**: Shows multi-task GP surrogates misestimate cross-task correlation even for affinely related tasks — negative transfer is partly structural, favoring prior/space-level over surrogate-level transfer in high-variability settings like EEG. / 顯示多任務 GP 即使在仿射相關任務上也會錯估跨任務相關——負遷移部分是結構性的，在 EEG 這類高變異情境更支持先驗／空間層級而非代理模型層級的遷移。

### Counter-Evidence / 反面證據

- **RUNet-style zero-calibration decoders (Theme 5, 2026)**: Achieve calibration-free MI decoding → but transfer trained models, not searched configurations; they bypass rather than answer the configuration-transfer question. / 達成免校正 MI 解碼→但遷移的是訓練好的模型而非搜尋所得配置；是繞過而非回答配置遷移問題。
- **AnarakiEtAl2024**: Personalized classifier prediction from dataset characteristics is a coarse form of zero-shot configuration recommendation → single component, no hyperparameters, no gating analysis. / 由資料特徵預測個人化分類器是粗粒度的零樣本配置推薦→僅單一元件、無超參數、無門控分析。

### Why It Matters / 重要性

This gap defines the practical operating curve between "no search" and "full search" for a new BCI user. The lab's own pilot (S002: zero-shot 0.765 vs warm-started search 0.950 vs baseline 0.635) illustrates exactly the trade-off the literature cannot currently characterize. Answering it yields deployment guidance (when is zero-shot enough? how many evaluations does gated warm-starting need?) and directly tests the transfer-HPO community's gating mechanisms in the hardest realistic regime.

此缺口定義了新 BCI 使用者在「不搜尋」與「完整搜尋」之間的實務操作曲線。本實驗室的 pilot（S002：零樣本 0.765、暖啟動搜尋 0.950、baseline 0.635）正是文獻目前無法刻畫的權衡。回答它能產生部署指引（零樣本何時足夠？門控暖啟動需要多少評估？），並在最困難的現實情境中直接檢驗 transfer-HPO 社群的門控機制。

### Priority Score / 優先分數

| Axis / 評估軸 | Score / 分數 | Rationale / 理由 |
|--------------|-------------|-----------------|
| Severity / 嚴重性 | 4 | Calibration burden is the primary practical blocker for subject-specific MI-BCI / 校正負擔是受試者特定 MI-BCI 的首要實務障礙 |
| Novelty / 新穎性 | 4 | Model-level zero-calibration is heavily explored; configuration-level is untouched / 模型層級已被大量探索，配置層級未被觸及 |
| Feasibility / 可行性 | 4 | Needs multi-subject search histories (compute-heavy) but standard public datasets suffice / 需多受試者搜尋歷史（計算量大），但標準公開資料集即足夠 |

**Composite / 綜合分:** 4 × 0.40 + 4 × 0.30 + 4 × 0.30 = 1.60 + 1.20 + 1.20 = **4.00**

---

## GAP_003: Budget-Matched, Anytime-Performance Evaluation Protocol for Knowledge-Injected Pipeline Search on EEG / EEG 知識注入管線搜尋的預算對齊 anytime 評估協議

**Type / 類型:** Measurement（量測缺口）(secondary: Methodological 方法學)
**Priority Rank / 優先排名:** #3

### Description / 描述

No EEG pipeline-search study reports anytime performance under matched evaluation budgets against seeded classical baselines, and the statistical significance of HPO effects in deep-learning EEG decoding remains untested. Meanwhile the AutoML literature warns that optimizer differences can vanish under rigorous protocols and that apparent LLM warm-start gains can reduce to a good default configuration. Without an EEG-adapted budget-matched protocol, any claimed warm-start gain in this domain is unverifiable.

尚無 EEG 管線搜尋研究在對齊的評估預算下、對照有 seed 的傳統 baseline 回報 anytime performance，且深度學習 EEG 解碼中 HPO 效果的統計顯著性仍未被檢驗。同時 AutoML 文獻警告：在嚴謹協議下最佳化器差異可能消失，而表面的 LLM 暖啟動增益可能化約為一個好的預設配置。缺少 EEG 版的預算對齊協議，此領域任何宣稱的暖啟動增益都無法驗證。

### Supporting Evidence / 支持證據

- **RodriguesEtAl2026**: Budget-matched protocol shows LLM advisor gains reduce to the fixed first configuration (+0.40 pp CV, −0.01 pp test, p=0.92); seeded classical search overtakes within 12 evaluations — the exact confound an EEG study must control. / 預算對齊協議顯示 LLM 顧問增益化約為固定首配置；有 seed 的傳統搜尋 12 次評估內反超——EEG 研究必須控制的混淆正是這個。
- **ZollerHuber2019**: Across 114 datasets, CASH optimizers were statistically indistinguishable and random search not worse — optimizer claims need careful protocols to be meaningful. / 114 個資料集上 CASH 最佳化器統計上無法區分、隨機搜尋不劣——最佳化器宣稱需要嚴謹協議才有意義。
- **CooneyEtAl2020**: Explicitly flags that statistical significance of HPO effects in DL-EEG is uncertain and under-tested. / 明確指出 DL-EEG 中 HPO 效果的統計顯著性不確定且未被充分檢驗。
- **GijsbersEtAl2019 + EggenspergerEtAl2021**: Name anytime-performance reporting and meta-learning train/test contamination as open benchmark problems — contamination is acute when warm-start priors are learned from subjects in the same dataset. / 點名 anytime performance 回報與 meta-learning 訓練／測試污染為開放基準問題——當暖啟動先驗學自同一資料集的受試者時，污染問題尤其尖銳。
- **Theme 4 collectively**: None of the 11 EEG automation papers reports evaluations-to-target or budget-matched comparisons. / 主題四 11 篇 EEG 自動化論文皆未回報達標評估次數或預算對齊比較。

### Counter-Evidence / 反面證據

- **EggenspergerEtAl2021 (HPOBench)**: Provides reproducible multi-fidelity benchmark infrastructure that such a protocol can build on → but is tabular/vision-focused; no EEG task family, no subject-transfer dimension. / 提供可重現的多保真基準設施可供借鑑→但聚焦表格／視覺，無 EEG 任務家族、無受試者遷移維度。

### Why It Matters / 重要性

This gap is instrumental: GAP_001 and GAP_002 cannot produce credible answers without it. It is also independently publishable — an EEG-subjects-as-tasks benchmark with leave-one-subject-out prior learning, anytime curves, and seeded baselines would give the transfer-HPO community the realistic task family EggenspergerEtAl2021 asks for, and give BCI researchers the first statistically grounded answer to whether pipeline search pays at all on small EEG datasets (LeonEtAl2020's skepticism).

此缺口具工具性：沒有它，GAP_001 與 GAP_002 無法產生可信答案。它也可獨立發表——以 EEG 受試者為任務、leave-one-subject-out 先驗學習、anytime 曲線與有 seed 的 baseline 的基準，能給 transfer-HPO 社群 EggenspergerEtAl2021 所要求的現實任務家族，也給 BCI 研究者第一個有統計基礎的答案：管線搜尋在小型 EEG 資料集上究竟值不值得（LeonEtAl2020 的質疑）。

### Priority Score / 優先分數

| Axis / 評估軸 | Score / 分數 | Rationale / 理由 |
|--------------|-------------|-----------------|
| Severity / 嚴重性 | 3 | The field functions, but claims in this area are currently unverifiable / 領域仍運作，但此區域的宣稱目前無法驗證 |
| Novelty / 新穎性 | 3 | Protocols exist in AutoML; their EEG instantiation does not / 協議在 AutoML 已存在，EEG 版不存在 |
| Feasibility / 可行性 | 5 | Pure protocol design + compute; no new mechanisms required / 純協議設計＋計算；不需新機制 |

**Composite / 綜合分:** 3 × 0.40 + 3 × 0.30 + 5 × 0.30 = 1.20 + 0.90 + 1.50 = **3.60**

---

## Priority Ranking / 優先排名

| Rank / 排名 | Gap ID | Title / 標題 | Type / 類型 | Severity / 嚴重性 | Novelty / 新穎性 | Feasibility / 可行性 | **Composite / 綜合分** |
|------------|--------|-------------|-----------|-----------------|----------------|--------------------|-----------------------|
| 1 | GAP_001 | Knowledge-injected warm-started CASH for EEG pipelines / 知識注入暖啟動 CASH 用於 EEG 管線 | Integration | 4 | 5 | 5 | **4.60** |
| 2 | GAP_002 | Zero-shot / gated config transfer across subjects / 跨受試者零樣本／門控配置遷移 | Methodological | 4 | 4 | 4 | **4.00** |
| 3 | GAP_003 | Budget-matched anytime evaluation protocol for EEG / EEG 預算對齊 anytime 評估協議 | Measurement | 3 | 3 | 5 | **3.60** |

---

## Gap Landscape Summary / 缺口全景

### Coverage Strength / 覆蓋強項

The field robustly covers: warm-start/transfer mechanisms for BO (Theme 2, 28 papers), decomposition-based CASH systems and their benchmarking (Theme 3, 48 papers), LLM-in-the-optimization-loop designs on tabular tasks (Theme 1, 26 papers), and model-level cross-subject EEG transfer (Theme 5). These strengths are what make the gaps feasible — every mechanism the gaps require already exists somewhere.

領域已穩固覆蓋：BO 暖啟動／遷移機制（主題二，28 篇）、分解式 CASH 系統與其基準評測（主題三，48 篇）、表格任務上的 LLM 入環設計（主題一，26 篇）、模型層級的跨受試者 EEG 遷移（主題五）。這些強項正是缺口可行的原因——缺口所需的每個機制都已在某處存在。

### Methodology Distribution / 方法學分布

Computational (49) and engineering (28) dominate; reviews are ample (16); observational work is nearly absent (1) but appropriately so for this topic. The telling absence is *within* the EEG themes: experimental EEG papers exist, but none uses BO-family optimizers, and no EEG paper carries the cyan "system" character of Theme 3 — EEG pipeline search has no equivalent of auto-sklearn/VolcanoML.

計算方法（49）與工程設計（28）主導；綜述充足（16）；觀察研究幾乎缺席（1）但對此主題屬合理。關鍵的缺席在 EEG 主題「內部」：實驗性 EEG 論文存在，但無一使用 BO 家族最佳化器，也沒有任何 EEG 論文具有主題三那種「系統」性格——EEG 管線搜尋沒有自己的 auto-sklearn／VolcanoML。

### Cross-Gap Patterns / 跨缺口模式

All three gaps share one root cause: EEG pipeline research and mainstream AutoML machinery evolved in parallel without contact — EEG subjects have never been treated as "tasks" in the transfer-HPO sense. Consequently the gaps chain naturally into one research program: GAP_003's protocol is the measurement foundation, GAP_001 is the core integration contribution, and GAP_002 is its deployment-facing extension.

三個缺口共享同一根源：EEG 管線研究與主流 AutoML 機制平行演化、互不接觸——EEG 受試者從未被當作 transfer-HPO 意義下的「任務」。因此三個缺口自然串成一個研究計畫：GAP_003 的協議是量測基礎，GAP_001 是核心整合貢獻，GAP_002 是其面向部署的延伸。

---

> **Checkpoint 3: 戰場選擇與價值裁定**
>
> Review the gaps above. Select which gap to pursue based on your lab's capabilities, budget, ethics timeline, and equipment access.
>
> 請審核上述缺口。根據您實驗室的能力、預算、倫理審查時程與設備，選擇要鎖定的缺口。
>
> To proceed, specify: "Lock GAP_{N}, drop GAP_{N}" — then I'll generate a hypothesis from the selected gap.
>
> 請指示：「鎖定 GAP_{N}，放棄 GAP_{N}」——然後我將根據選定缺口生成假說。

---

Files / 檔案: `step7_gap_analysis.md`
Next step / 下一步: `/research-hypothesis`
