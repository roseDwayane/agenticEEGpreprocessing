---
session_id: "20260815"
topic: "LLM-informed warm-starting and prior injection for Bayesian optimization (CASH) of subject-specific EEG motor-imagery preprocessing pipelines, with cross-subject transfer"
date: "2026-08-15"
step: 8
selected_gap: "GAP_001"
secondary_gaps_incorporated: ["GAP_002 (secondary aim)", "GAP_003 (built-in evaluation methodology)"]
research_questions: 4
hypotheses: 3
---

# Hypothesis Specification / 假說規格書

> Topic / 研究主題: Knowledge-injected warm-starting of decomposed CASH for subject-specific EEG MI pipelines
> Selected Gap / 選定缺口: GAP_001（核心）＋ GAP_002（次要目標）＋ GAP_003（內建評估方法學）
> Date / 日期: 2026-08-15

## Executive Summary / 總覽摘要

This study addresses the empty intersection identified in GAP_001: no existing work injects transferable knowledge — search histories from other subjects, or LLM-elicited priors — into Bayesian-optimization-based CASH over full EEG preprocessing + decoding pipelines. We propose to extend a decomposition-based CASH framework (VolcanoML/SMAC lineage) with two knowledge-injection mechanisms: (a) meta-learned warm-starting from other subjects' search histories (MI-SMAC style, FeurerEtAl2015), and (b) πBO-style prior injection (HvarfnerEtAl2022) where the prior is elicited from an LLM given dataset/subject descriptors. The primary claim is that knowledge-injected search reaches the cold-start optimum with substantially fewer pipeline evaluations and yields higher anytime performance under matched budgets. As a secondary aim (GAP_002), we quantify zero-shot configuration transfer — applying a source-subjects' configuration to a new subject with no search — and test similarity gating against negative transfer. The entire evaluation follows a budget-matched, anytime-performance protocol with seeded baselines (GAP_003), pre-empting the known confound that warm-start gains can reduce to one strong default configuration (RodriguesEtAl2026).

本研究針對 GAP_001 識別的空白交集：現有工作皆未將可遷移知識——其他受試者的搜尋歷史、或 LLM 引出的先驗——注入以 BO 為基礎、涵蓋完整 EEG 前處理＋解碼管線的 CASH。我們提議擴充分解式 CASH 框架（VolcanoML/SMAC 一系），加入兩種知識注入機制：(a) 自其他受試者搜尋歷史的 meta-learning 暖啟動（MI-SMAC 式，FeurerEtAl2015）；(b) πBO 式先驗注入（HvarfnerEtAl2022），其先驗由 LLM 根據資料集／受試者描述引出。主要主張是：知識注入搜尋能以顯著更少的管線評估次數達到冷啟動最優，並在對齊預算下取得更高的 anytime performance。次要目標（GAP_002）為量化零樣本配置遷移——將來源受試者的配置不經搜尋直接套用於新受試者——並檢驗相似度門控對負遷移的防護。整體評估採預算對齊、anytime performance 與 seeded baseline 協議（GAP_003），預先回應「暖啟動增益可能化約為一個強預設配置」的已知混淆（RodriguesEtAl2026）。

---

## Selected Gap Summary / 選定缺口摘要

**Gap ID:** GAP_001 (composite 4.60) — with GAP_002 (4.00) as secondary aim and GAP_003 (3.60) as evaluation methodology
**Type / 類型:** Integration（整合缺口）

Across 124 screened papers, zero combine LLM guidance or warm-started search with EEG pipeline optimization; EEG automation runs on cold-start evolutionary search (Theme 4), cross-subject EEG work transfers model weights but never configurations (Theme 5), while the CASH systems themselves name warm-starting as their next step (LiEtAl2016, LiEtAl2020a, LiEtAl2022a). GAP_002 and GAP_003 are absorbed rather than dropped: the zero-shot-transfer question is a natural secondary endpoint of the same experimental design, and the budget-matched protocol is the methodological armor without which the primary claim would be unverifiable.

在 124 篇篩選文獻中，無任何論文將 LLM 引導或暖啟動搜尋與 EEG 管線最佳化結合；EEG 自動化依賴冷啟動演化式搜尋（主題四），跨受試者 EEG 研究只遷移模型權重、從不遷移配置（主題五），而 CASH 系統本身把暖啟動列為下一步（LiEtAl2016, LiEtAl2020a, LiEtAl2022a）。GAP_002 與 GAP_003 是被吸收而非被放棄：零樣本遷移問題是同一實驗設計的自然次要終點，預算對齊協議則是讓主要主張可驗證的方法學防護。

---

## Research Questions / 研究問題

### RQ1 (Feasibility): Can a decomposition-based CASH framework operate over a subject-specific EEG MI preprocessing + decoding search space with practical per-subject search cost? / 分解式 CASH 框架能否以可實務接受的單受試者搜尋成本，運作於受試者特定 EEG MI 前處理＋解碼搜尋空間？

The existence question. Theme 4 showed BO-family CASH has never been the primary engine for EEG pipeline search; the lab pilot (cold-start 0.775/0.915 vs baseline 0.555/0.635 on EEGMMI S001/S002) suggests it works, but feasibility must be established across many subjects with cost accounting. A positive answer: cold-start decomposed CASH beats the fixed standard pipeline on a majority of subjects within a 50–100-evaluation budget.

存在性問題。主題四顯示 BO 家族的 CASH 從未成為 EEG 管線搜尋的主引擎；實驗室 pilot（EEGMMI S001/S002 冷啟動 0.775/0.915 對 baseline 0.555/0.635）顯示可行，但需在多受試者上以成本核算確立。正面答案：冷啟動分解式 CASH 在 50–100 次評估預算內、於多數受試者上勝過固定標準管線。

### RQ2 (Primary): Under matched evaluation budgets, does knowledge-injected warm-starting (cross-subject search-history priors and/or LLM-elicited priors) accelerate convergence and improve anytime and final decoding performance relative to cold-start search? / 在對齊評估預算下，知識注入暖啟動（跨受試者搜尋歷史先驗與／或 LLM 引出先驗）是否相對冷啟動搜尋加速收斂並提升 anytime 與最終解碼表現？

The core GAP_001 question, with both knowledge sources instantiated in the same πBO/initial-design machinery so they are directly comparable.

GAP_001 的核心問題；兩種知識來源以同一 πBO／初始設計機制實例化，可直接比較。

### RQ3 (Transfer / Secondary — GAP_002): How much decoding performance does zero-shot configuration transfer retain relative to the subject's searched optimum, and does similarity gating prevent negative transfer on outlier subjects? / 零樣本配置遷移相對受試者搜尋最優能保留多少解碼表現？相似度門控能否防止離群受試者的負遷移？

Defines the practical operating curve between "no search" and "full search". Pilot point: S002 zero-shot 0.765 sits between baseline 0.635 and searched 0.950 — retention must be characterized across the population, including documented ~30-fold-deviation outliers (ZhangEtAl2024, BerdyshevEtAl2024).

界定「不搜尋」與「完整搜尋」之間的實務操作曲線。Pilot 資料點：S002 零樣本 0.765 落在 baseline 0.635 與搜尋最優 0.950 之間——保留率需在整個族群上刻畫，包括文獻記錄的約 30 倍偏差離群受試者（ZhangEtAl2024, BerdyshevEtAl2024）。

### RQ4 (Mechanism): Where does the warm-start advantage come from — does it survive a seeded cold-start control (strong first configuration + vanilla search), and which knowledge source (histories vs LLM priors) contributes what? / 暖啟動優勢從何而來——它能否在 seeded 冷啟動對照（強首配置＋原始搜尋）下存活？兩種知識來源各貢獻多少？

Directly imports the RodriguesEtAl2026 confound as an internal control and produces the ablation that decomposes prior-mean quality vs. prior-shape guidance.

直接把 RodriguesEtAl2026 的混淆納為內部對照，並產生分解「先驗均值品質」與「先驗形狀引導」貢獻的消融。

---

## Hypotheses / 假說

### Primary Hypothesis / 主要假說 (RQ2)

**H0 (Null / 虛無假說):** Under a matched budget of B pipeline evaluations, knowledge-injected warm-starting produces no difference from cold-start search in anytime performance (area under the incumbent balanced-accuracy curve) or in evaluations-to-target. / 在對齊的 B 次管線評估預算下，知識注入暖啟動與冷啟動搜尋在 anytime performance（現任最優 balanced accuracy 曲線下面積）與達標評估次數上無差異。

**H1 (Alternative / 對立假說):** Knowledge-injected warm-starting reaches the cold-start run's best-in-budget balanced accuracy in at most half the evaluations (≥2× speedup in evaluations-to-target), and improves fixed-budget final balanced accuracy, across subjects. / 知識注入暖啟動以至多一半的評估次數達到冷啟動在預算內的最佳 balanced accuracy（達標評估次數 ≥2× 加速），且在固定預算下提升最終 balanced accuracy（跨受試者）。

**Expected Direction & Magnitude / 預期方向與幅度:** Direction: warm > cold. Magnitude anchors: MI-SMAC's warm-start gain exceeded SMAC-vs-random gain and persisted at 50 evaluations (FeurerEtAl2015); πBO reports up to 12.5× time-to-accuracy with a merely default-centered prior (HvarfnerEtAl2022); lab pilot shows warm 0.950 vs cold 0.915 at budget ~30 on EEGMMI S002. The ≥2× speedup threshold is deliberately conservative relative to πBO's 12.5×, acknowledging EEG inter-subject variability. / 方向：warm > cold。幅度錨點：FeurerEtAl2015、HvarfnerEtAl2022（12.5×）、實驗室 pilot（0.950 vs 0.915）。≥2× 門檻相對 πBO 的 12.5× 刻意保守，以反映 EEG 受試者間變異。

**Suggested Statistical Approach / 建議統計方法:** Wilcoxon signed-rank tests across subjects (paired, non-parametric; N=109 EEGMMI + 9 BCI-IV-2a gives strong power) on (a) anytime AUC and (b) evaluations-to-target; Holm–Bonferroni correction across the two endpoints and across budgets B ∈ {25, 50, 100}; effect size as matched-pairs rank-biserial correlation. / 跨受試者 Wilcoxon 符號等級檢定（成對、無母數）於 anytime AUC 與達標評估次數；Holm–Bonferroni 校正；效果量採 rank-biserial correlation。

### Secondary Hypothesis H2 / 次要假說 H2 (RQ3 — GAP_002)

**H0:** Zero-shot transferred configurations do not outperform the fixed standard pipeline (8–30 Hz band-pass + CSP + LDA) on unseen subjects. / 零樣本遷移配置在未見過受試者上不優於固定標準管線。

**H1:** Zero-shot transferred configurations outperform the fixed standard pipeline on unseen subjects, retaining a substantial fraction of the searched optimum's improvement; similarity-gated transfer eliminates cases where ungated transfer falls below the standard pipeline. / 零樣本遷移配置在未見過受試者上優於固定標準管線，保留搜尋最優改善量的可觀比例；相似度門控消除無門控遷移低於標準管線的個案。

**Expected Direction & Magnitude:** Pilot retention: (0.765−0.635)/(0.950−0.635) ≈ 41% of the searched improvement recovered with zero evaluations. Magnitude stated as exploratory — no prior EEG work measures configuration-level retention (the gap itself); the pilot provides the only anchor. / Pilot 保留率約 41%（零評估）。幅度屬探索性——尚無 EEG 先行研究量測配置層級保留率（此即缺口本身），pilot 為唯一錨點。

**Statistical Approach:** Wilcoxon signed-rank (zero-shot vs standard pipeline, across held-out subjects); negative-transfer rate (fraction of subjects where transfer < standard) compared between gated and ungated variants via McNemar's test. / Wilcoxon 檢定＋以 McNemar 檢定比較門控與無門控的負遷移率。

### Secondary Hypothesis H3 / 次要假說 H3 (RQ4)

**H0:** The warm-start advantage disappears when the cold-start baseline is seeded with the prior's single best configuration as its first evaluation (i.e., the gain is fully explained by one strong default). / 當冷啟動 baseline 以先驗的單一最佳配置作為首次評估（seeded）後，暖啟動優勢消失。

**H1:** Knowledge-injected warm-starting retains a significant anytime-AUC advantage over the seeded cold-start control, demonstrating that prior *shape* guides the search beyond the first configuration. / 知識注入暖啟動相對 seeded 冷啟動對照仍保有顯著 anytime-AUC 優勢，顯示先驗「形狀」的引導超越首配置。

**Expected Direction & Magnitude:** Uncertain by design — RodriguesEtAl2026 found seeded classical search overtakes LLM advisors within 12 evaluations on tabular tasks; whether EEG's larger conditional space behaves differently is precisely what this tests. Stated directionally without magnitude. / 方向刻意存疑——RodriguesEtAl2026 在表格任務發現 seeded 傳統搜尋 12 次評估內反超 LLM 顧問；EEG 較大的條件式空間是否不同，正是本檢定目的。僅陳述方向、不設幅度。

**Statistical Approach:** Same Wilcoxon protocol as H1, warm vs seeded-cold. / 與 H1 相同的 Wilcoxon 協議（warm 對 seeded-cold）。

---

## Scope Boundaries / 範圍界定

### IN Scope / 範圍內

| Dimension / 維度 | Specification / 規格 |
|-----------------|---------------------|
| **Population / 族群** | Subject-specific MI decoding on public datasets: PhysioNet EEGMMI (109 subjects) as primary; BCI Competition IV-2a (9 subjects) as external validation / 公開資料集上的受試者特定 MI 解碼：EEGMMI（109 名）為主、BCI IV-2a（9 名）外部驗證 |
| **Intervention / Method / 介入方法** | Decomposed CASH (VolcanoML-style execution plans, SMAC3/OpenBox backend) over a 5-stage classical pipeline space (resampling; FIR/IIR band-pass with l_freq/h_freq; re-referencing; epoching window; CSP n_components; classifier ∈ {LDA, SVM, RF, LR, QDA} + hyperparameters); knowledge injection via (a) leave-one-subject-out search-history priors and (b) LLM-elicited πBO priors; similarity-gated variants of both / 分解式 CASH 於 5 階段傳統管線空間；知識注入：(a) LOSO 搜尋歷史先驗、(b) LLM 引出的 πBO 先驗；兩者皆含相似度門控變體 |
| **Comparison / Control / 對照** | Cold-start SMAC/VolcanoML; random search; **seeded cold-start** (prior's best config as first evaluation); fixed standard pipeline (8–30 Hz FIR + CSP + LDA) / 冷啟動、隨機搜尋、**seeded 冷啟動**、固定標準管線 |
| **Primary Outcome / 主要結果** | Anytime AUC of incumbent balanced accuracy; evaluations-to-target; fixed-budget final balanced accuracy (B ∈ {25, 50, 100}) / 現任最優 balanced accuracy 的 anytime AUC、達標評估次數、固定預算最終 balanced accuracy |
| **Secondary Outcome / 次要結果** | Zero-shot transfer accuracy and retention ratio; negative-transfer rate (gated vs ungated); knowledge-source ablation deltas / 零樣本遷移準確率與保留率、負遷移率（門控 vs 無門控）、知識來源消融差異 |
| **Setting / 場域** | Offline computational experiments; within-subject 5-fold CV for evaluation; strict LOSO separation for prior learning (contamination control per EggenspergerEtAl2021) / 離線計算實驗；受試者內 5-fold CV；先驗學習嚴格 LOSO 分離 |
| **Design / 設計** | Budget-matched within-subject comparative study (RodriguesEtAl2026 protocol), all conditions consuming identical evaluation counts / 預算對齊的受試者內比較研究，所有條件消耗相同評估次數 |
| **Timeframe / 時間框架** | Per-subject search budget 25–100 evaluations; total compute bounded by evaluation caching and parallel per-subject runs / 單受試者 25–100 次評估；以評估快取與平行執行控制總計算量 |

### OUT Scope / 範圍外

| Exclusion / 排除項目 | Rationale / 學術理由 |
|---------------------|---------------------|
| Online / closed-loop BCI experiments / 線上閉環 BCI 實驗 | Offline validation must precede online deployment; online adds hardware, ethics, and latency dimensions that would confound the optimization question. Positioned as the follow-up study. / 離線驗證須先於線上部署；線上引入硬體、倫理與延遲維度，會混淆最佳化問題本身。定位為後續研究。 |
| Deep end-to-end architectures and NAS / 深度端到端架構與 NAS | Theme 4 evidence (LeonEtAl2020, CooneyEtAl2020) shows searched deep models overfit small per-subject EEG data and HPO significance is untested there; the classical pipeline space is where CASH decomposition applies cleanly and where preprocessing–classifier coupling (the motivating phenomenon) lives. DL-NAS for EEG is a distinct, active field (T4). / 主題四證據顯示深度模型在小樣本上過擬合；傳統管線空間才是 CASH 分解的乾淨適用域，且承載「前處理—分類器耦合」這一動機現象。 |
| Clinical / patient populations / 臨床族群 | Public healthy-adult datasets ensure reproducibility and statistical power (109 subjects); clinical generalization requires separate ethics and data agreements. / 公開健康成人資料集確保可重現性與統計檢定力；臨床推廣需另行倫理與資料協議。 |
| Non-MI paradigms (P300, SSVEP) / 非 MI 範式 | Transfer assumes a homogeneous task family; mixing paradigms would conflate task shift with subject shift. MI is where preprocessing sensitivity is best documented (Fathima et al. review lineage). / 遷移假設同質任務家族；混合範式會把任務偏移與受試者偏移混為一談。 |
| LLM fine-tuning; sub-70B local models as prior sources / LLM 微調；以 70B 以下本地模型作先驗來源 | Prompt-based elicitation only, using API-scale models: RychertEtAl2025 shows sub-70B backbones produce malformed/uncorrelated outputs under current prompting; fine-tuning adds cost and reproducibility burden orthogonal to the hypothesis. / 僅用提示式引出與 API 級模型：RychertEtAl2025 顯示 70B 以下骨幹輸出畸形或無相關；微調的成本與重現性負擔與假說無關。 |
| Commercial AutoML platforms as baselines / 商用 AutoML 平台作 baseline | Licensing, opaque versioning, and irreproducibility (VolcanoML's own industrial comparison already exists, LiEtAl2021b). / 授權、版本不透明與不可重現。 |

### Scope Rationale / 範圍邏輯

The scope is engineered around one auditability principle: every claimed gain must survive the strongest known deflationary explanation. Hence seeded cold-start is IN (deflates "it's just a good default"), LOSO prior learning is IN (deflates "meta-learning contamination"), and budget matching is IN (deflates "it just ran longer"). The exclusions concentrate statistical power where the gap actually is: classical-pipeline CASH on a large homogeneous subject pool. A reviewer asking "why not deep models / online BCI / patients?" is answered by the same logic — those extensions inherit whatever this study establishes, and each requires a design that would dilute this one.

範圍圍繞一個可稽核原則設計：每個宣稱的增益都必須挺過已知最強的通縮解釋。因此 seeded 冷啟動在範圍內（破解「只是好預設」）、LOSO 先驗學習在範圍內（破解「meta-learning 污染」）、預算對齊在範圍內（破解「只是跑比較久」）。排除項目把統計檢定力集中在缺口實際所在：大型同質受試者池上的傳統管線 CASH。審稿人問「為何不做深度模型／線上／病人」的答案同理——那些延伸繼承本研究所確立的結論，且各自需要會稀釋本設計的另一套設計。

---

## Conceptual Framework / 概念框架

```
                        ┌─ Knowledge Sources 知識來源 ─────────────────┐
                        │  (a) LOSO search histories  (b) LLM elicitation │
                        │      of N−1 subjects            (πBO prompt)    │
                        └──────────────┬────────────────────┬────────────┘
                                       ▼                    ▼
                          ┌─ Prior Construction 先驗建構 ────────────┐
                          │  per-hyperparameter priors + top-k       │
                          │  initial designs; similarity gate g(s)   │
                          │  from subject meta-features (band power, │
                          │  CSP eigen-spectrum, baseline acc)       │
                          └──────────────┬───────────────────────────┘
              zero-shot path             ▼
   ┌──────────────────────┐   ┌─ Decomposed CASH loop (SMAC3/OpenBox, ─┐
   │ apply prior's best   │   │  VolcanoML execution plan)             │
   │ config directly (B=0)│   │  Stage1 filter → Stage2 epoch →        │
   └──────────┬───────────┘   │  Stage3 CSP → Stage4 classifier        │
              │               │  budget-matched B ∈ {25,50,100}        │
              ▼               └──────────────┬─────────────────────────┘
   ┌─ Evaluation 評估 (per target subject, 5-fold CV) ─────────────────┐
   │  anytime incumbent curve · evals-to-target · final bal. acc       │
   │  vs cold-start / random / seeded-cold / fixed standard pipeline   │
   └───────────────────────────────────────────────────────────────────┘
```

The design treats each EEG subject as a task in the transfer-HPO sense; the similarity gate g(s) decays the prior toward uniform when the target subject's meta-features are far from the source pool (πBO's wrong-prior recovery provides the safety floor).

本設計把每位 EEG 受試者視為 transfer-HPO 意義下的任務；當目標受試者的 meta-features 遠離來源池時，相似度門控 g(s) 使先驗衰減向均勻分布（πBO 的錯誤先驗恢復提供安全底線）。

---

## Risk Assessment / 風險評估

| Risk / 風險 | Likelihood / 可能性 | Impact / 影響 | Mitigation / 緩解策略 |
|------------|-------------------|-------------|---------------------|
| Negative transfer on outlier subjects (~30-fold feature deviations documented) / 離群受試者負遷移 | Medium | High | Similarity gating + πBO decay; report negative-transfer rate as an endpoint, not a nuisance / 相似度門控＋πBO 衰減；將負遷移率列為正式終點 |
| Warm-start gain reduces to a strong default (RodriguesEtAl2026 confound) / 增益化約為強預設 | Medium | High | Seeded cold-start control built into H3; anytime AUC not just final accuracy / H3 內建 seeded 對照；採 anytime AUC |
| LLM prior quality unstable across prompts/backbones (RychertEtAl2025) / LLM 先驗品質不穩 | Medium | Medium | API-scale models only; fixed prompt templates released for reproducibility; history-based priors as the LLM-independent arm / 僅用 API 級模型；發布固定提示模板；歷史先驗作為不依賴 LLM 的對照臂 |
| Compute cost of building 109 subjects' search histories / 建立 109 名受試者搜尋歷史的計算成本 | High | Medium | Evaluation caching; parallel per-subject runs; histories double as the (reusable, releasable) benchmark asset of GAP_003 / 評估快取；平行執行；歷史本身即 GAP_003 可釋出的基準資產 |
| Optimizer-indistinguishability regime (ZollerHuber2019) — effects too small to detect / 效應過小 | Low–Medium | Medium | Power from N=109 paired subjects; pre-registered endpoints; if H1 fails, the anytime benchmark itself (GAP_003) remains a publishable contribution / N=109 成對檢定力；預註冊終點；若 H1 不成立，anytime 基準本身仍可發表 |

---

## Gap-to-Hypothesis Traceability / 缺口到假說追溯

| Element / 元素 | Source / 來源 | Evidence / 證據 |
|---------------|-------------|----------------|
| RQ1 | GAP_001 description | BO-family CASH absent from EEG pipeline search (Theme 4: 0/11 papers) |
| RQ2 | GAP_001 + SOTA Themes 1–3 | Warm-start machinery mature but never applied to subjects-as-tasks (FeurerEtAl2015, HvarfnerEtAl2022, LiEtAl2022c) |
| RQ3 | GAP_002 | Config-level zero-shot transfer unmeasured; model-level analogues only (Theme 5) |
| RQ4 | GAP_003 + RodriguesEtAl2026 | Budget-matched study shows LLM gains can reduce to fixed first config |
| H1 direction | FeurerEtAl2015; lab pilot | MI-SMAC > cold SMAC at 50 evals; pilot warm 0.950 > cold 0.915 |
| H1 magnitude (≥2× evals-to-target) | HvarfnerEtAl2022 | πBO 12.5× time-to-accuracy → 2× is a conservative EEG-adjusted floor |
| H2 direction | Lab pilot | Zero-shot 0.765 > baseline 0.635 (S002); retention ≈ 41% |
| H3 design | RodriguesEtAl2026 | Seeded classical search overtakes within 12 evaluations on tabular tasks |
| IN: LOSO prior learning | EggenspergerEtAl2021 | Meta-learning train/test contamination named as open benchmark problem |
| OUT: deep NAS | LeonEtAl2020, CooneyEtAl2020 | Searched deep models overfit small EEG data; HPO significance untested |
| OUT: sub-70B LLMs | RychertEtAl2025 | Sub-70B backbones produce malformed/uncorrelated surrogate outputs |

---

> **Checkpoint 4: 護城河最終確認**
>
> Review the IN/OUT boundaries above. Verify that:
> 1. Every OUT exclusion has an academically defensible rationale
> 2. The scope matches your actual time, budget, and resource constraints (notably: compute for 109-subject search histories)
> 3. The hypotheses are testable with your available methods and data
>
> 請審核上述 IN/OUT 邊界。確認：
> 1. 每個 OUT 排除項目都有可在學術上辯護的理由
> 2. 範圍符合實際時間、預算與資源限制（特別是 109 名受試者搜尋歷史的計算量）
> 3. 假說可用現有方法與數據檢驗
>
> To proceed, confirm: "Scope approved / 範圍核准" — then move to `/research-write`.

---

Files / 檔案: `step8_hypothesis_specification.md`, `step8_journal_recommendations.md`
Next step / 下一步: `/research-write`
