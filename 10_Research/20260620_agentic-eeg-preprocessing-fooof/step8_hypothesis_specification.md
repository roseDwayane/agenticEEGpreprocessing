---
session_id: "20260620"
topic: "Agentic EEG preprocessing pipeline with FOOOF-based signal quality optimization"
date: "2026-06-20"
step: 8
selected_gap: "GAP_001 + GAP_002 + GAP_003 (Option B — full program)"
dropped_gaps: []
design_constraint: "GAP_004 (aperiodic signal-vs-artifact disambiguation) folded in as a reward-design constraint, not a separate study"
research_questions: 4
hypotheses: 4
---

# Hypothesis Specification / 假說規格書

> Topic / 研究主題: Agentic EEG preprocessing pipeline with FOOOF-based signal quality optimization
> Selected Gap / 選定缺口: GAP_001 + GAP_002 + GAP_003 (locked together as one program, Option B)
> Date / 日期: 2026-06-20

## Executive Summary / 總覽摘要

This research proposes and validates an **agentic EEG preprocessing pipeline** built on three coupled contributions. First (GAP_002), we construct the first **exhaustive-search ground-truth oracle** for EEG preprocessing: across public datasets, every recording is run through a grid of preprocessing configurations and each output is scored by a label-free quality metric, yielding a per-recording best configuration. Second (GAP_001), that metric is a **FOOOF-derived signal-to-noise ratio (FOOOF-SNR)** — periodic (oscillatory) spectral power as signal, aperiodic 1/f power plus fit residual as noise — replacing downstream task accuracy as the optimization target. Third (GAP_003), we train and evaluate an **LLM-agent controller** that reads intermediate EEG signal features and selects the next preprocessing operation and its parameters, with the oracle providing both training supervision and an upper bound. The aperiodic-as-signal risk (GAP_004) is handled inside the reward via band-limited periodic SNR and goodness-of-fit gating, not as a separate study.

本研究提出並驗證一個建立在三項耦合貢獻上的**智能體式 EEG 預處理流程**。第一（GAP_002），我們建構 EEG 預處理的首個**窮舉搜尋 ground-truth oracle**：在公開資料集上，每筆記錄跑過一格預處理組態，每個輸出以無標籤品質指標評分，得到每筆的最佳組態。第二（GAP_001），該指標是 **FOOOF 衍生的訊雜比（FOOOF-SNR）**——週期（振盪）頻譜功率為訊號、非週期 1/f 功率加擬合殘差為雜訊——取代下游任務準確率作為最佳化目標。第三（GAP_003），我們訓練並評估一個 **LLM 智能體控制器**，讀取中間 EEG 訊號特徵並選擇下一個預處理操作與參數，由 oracle 同時提供訓練監督與上界。非週期即訊號的風險（GAP_004）以帶限週期 SNR 與擬合優度把關在獎勵內處理。

The expected contribution is threefold: (1) the first label-free, task-independent quality metric shown to rank EEG preprocessing outputs in agreement with expert judgment and exhaustive search; (2) a reusable benchmark dataset of per-recording optimal preprocessing configurations; and (3) the first demonstration that LLM agents transfer from tabular/code/chemistry domains to electrophysiological signal processing, recovering near-oracle quality while remaining interpretable.

預期貢獻有三：(1) 首個無標籤、任務無關、且與專家判斷及窮舉搜尋一致的 EEG 預處理品質指標；(2) 可重用的每筆最佳預處理組態基準資料集；(3) 首次展示 LLM 智能體從表格/程式碼/化學領域遷移到電生理訊號處理，回收近 oracle 品質且保持可解釋。

---

## Selected Gap Summary / 選定缺口摘要

**Gap IDs:** GAP_001 (composite 4.70), GAP_002 (4.30), GAP_003 (4.40); GAP_004 (4.00) as constraint
**Type / 類型:** Measurement + Methodological + Integration

The field has many automated EEG preprocessing pipelines but, as PedroniEtAl2019 and Delorme2023 both state, no objective label-free quality target; FOOOF (DonoghueEtAl2020b) supplies a physiologically validated signal/noise decomposition that has never been used as an optimization objective; and modern LLM agents (GuoEtAl2024, ChiEtAl2024, TriratEtAl2024) optimize pipelines for tabular/code data but have never been applied to EEG. This program occupies the empty intersection of all three. / 領域有許多自動化 EEG 預處理管線，但缺客觀無標籤品質目標；FOOOF 提供經生理驗證的訊號/雜訊分解卻從未作為最佳化目標；現代 LLM 智能體最佳化表格/程式碼管線卻從未用於 EEG。本計畫佔據三者交集的空格。

### Gap Selection Rationale / 缺口選擇理由

Option B locks the three pillar gaps as a single coherent program because they are sequentially dependent, not competing: GAP_002 (oracle) cannot be built without GAP_001 (metric); GAP_003 (agent) cannot be trained without GAP_002 (oracle). No gaps were dropped. GAP_004 scored 4.00 as a standalone but is intrinsically a sub-component of GAP_001's metric validity, so it is carried as a design constraint rather than a separate research question. / 選項 B 將三個支柱缺口鎖為單一連貫計畫，因其循序相依而非競爭。未放棄任何缺口。GAP_004 雖獨立得 4.00，但本質上是 GAP_001 指標效度的子元件，故作為設計約束而非獨立研究問題。

---

## Research Questions / 研究問題

### RQ1: Validity of the metric — Does FOOOF-SNR rank preprocessing outputs in agreement with exhaustive search and expert judgment? / FOOOF-SNR 是否與窮舉搜尋及專家判斷一致地排序預處理輸出？

This is the feasibility/existence question and the project's keystone. A positive answer means a single resting-state recording can be preprocessing-scored without any labels or downstream classifier. We test whether FOOOF-SNR rankings correlate with (a) the exhaustive-search quality ordering and (b) a small set of expert-rated recordings. / 這是可行性/存在性問題，也是計畫拱心石。正面答案意味單筆靜息態記錄可在無標籤、無下游分類器下被預處理評分。

### RQ2: Oracle and headroom — How much does optimal per-recording preprocessing improve FOOOF-SNR over fixed standard pipelines, and how variable is the optimum across recordings? / 每筆最佳預處理相對固定標準管線能提升多少 FOOOF-SNR，且最佳組態跨記錄變異有多大？

The primary-outcome question. It quantifies (a) the achievable headroom the agent could capture, and (b) whether one-size-fits-all pipelines are leaving quality on the table — the premise that motivates per-recording adaptation. / 主要產出問題：量化 agent 可捕捉的可達空間，以及通用管線是否浪費品質。

### RQ3: Agent transfer — Can an LLM agent reading intermediate signal features recover near-oracle preprocessing quality, and does it generalize to held-out datasets/paradigms? / LLM 智能體讀取中間訊號特徵能否回收近 oracle 品質，並泛化到留出資料集/範式？

The transfer/generalization question. It tests whether the agentic approach beats fixed pipelines and classical search baselines (Bayesian optimization, random search) on unseen recordings. / 遷移/泛化問題：測試智能體是否在未見記錄上勝過固定管線與古典搜尋基準。

### RQ4: Interpretability and mechanism — What decision policies does the agent learn, and do they align with established preprocessing principles? / 智能體學到什麼決策策略，是否符合既有預處理原則？

The mechanism question. Because the controller is an LLM, its step-by-step rationales can be inspected; we test whether its learned policy recovers known heuristics (e.g., interpolate bad channels, high-pass before ICA) and surfaces novel, recording-specific rules. / 機制問題：因控制器是 LLM，可檢視其逐步理由，測試其學到的策略是否回收已知啟發法並浮現新規則。

---

## Hypotheses / 假說

### Primary Hypothesis / 主要假說 (from RQ2)

**H0 (Null / 虛無假說):** Optimal per-recording preprocessing configurations selected by maximizing FOOOF-SNR yield no statistically significant improvement in FOOOF-SNR over a fixed standard pipeline (e.g., PREP/HAPPE defaults) across recordings. / 以最大化 FOOOF-SNR 選出的每筆最佳組態，相對固定標準管線在 FOOOF-SNR 上無統計顯著提升。

**H1 (Alternative / 對立假說):** Optimal per-recording configurations yield a statistically significant FOOOF-SNR improvement over the best fixed standard pipeline, with the per-recording optimum varying meaningfully across recordings (i.e., no single configuration is optimal for all). Test: paired test (Wilcoxon signed-rank) across recordings; configuration-variability quantified by the entropy/spread of the optimal-config distribution. / 每筆最佳組態相對最佳固定管線有顯著 FOOOF-SNR 提升，且最佳組態跨記錄有意義地變異（無單一組態對全體最佳）。檢定：跨記錄配對檢定（Wilcoxon）；組態變異以最佳組態分布的熵/離散度量化。

> Direction & magnitude grounded in evidence, not invented: RobbinsEtAl2020 and Delorme2023 establish that preprocessing choices change EEG measures substantially and non-uniformly across recordings, supporting a directional (improvement + heterogeneity) expectation; a specific minimum effect size will be pre-registered from a pilot rather than asserted here. / 方向與量級以證據為據而非杜撰；具體最小效應量將由前導試驗預註冊。

### Secondary Hypothesis H2 / 次要假說 H2 (from RQ1 — metric validity)

**H0:** FOOOF-SNR rankings of preprocessing outputs are uncorrelated with the exhaustive-search quality ordering and with expert ratings. / FOOOF-SNR 排序與窮舉搜尋順序及專家評分無相關。
**H1:** FOOOF-SNR rankings are positively and significantly correlated with both the exhaustive-search ordering and expert ratings (Spearman ρ > 0, pre-registered threshold). / FOOOF-SNR 排序與兩者皆顯著正相關（Spearman ρ，預註冊門檻）。

### Secondary Hypothesis H3 / 次要假說 H3 (from RQ3 — agent transfer)

**H0:** The LLM-agent controller does not achieve higher FOOOF-SNR than fixed pipelines or classical search baselines on held-out recordings. / LLM 智能體在留出記錄上未勝過固定管線或古典搜尋基準。
**H1:** The LLM-agent controller achieves FOOOF-SNR significantly closer to the oracle than fixed pipelines, and at least competitive with classical search at lower per-recording query cost, on held-out datasets/paradigms. / LLM 智能體在留出資料集上顯著比固定管線更接近 oracle，且以更低每筆查詢成本與古典搜尋至少相當。

### Secondary Hypothesis H4 / 次要假說 H4 (from RQ4 — interpretability)

**H0:** The agent's learned decisions are unrelated to established preprocessing principles. / 智能體決策與既有預處理原則無關。
**H1:** The agent's decision policy recovers established heuristics at above-chance rates and its deviations are systematically associated with recording-level signal features (e.g., chooses aggressive ASR when high-frequency aperiodic power is elevated). / 智能體策略以高於隨機率回收既有啟發法，且其偏離與記錄層級訊號特徵系統性相關。

---

## Scope Boundaries (IN / OUT) / 範圍界定

### IN Scope / 納入範圍

| Dimension | Specification |
|-----------|---------------|
| **Population / 對象** | Human scalp EEG from public datasets; **resting-state and task/ERP paradigms** where periodic structure is expected; adult cohorts primarily / 公開資料集人類頭皮 EEG；靜息態與任務/ERP 範式；以成人為主 |
| **Intervention / Method** | (a) FOOOF-SNR quality metric; (b) exhaustive/greedy search over a defined preprocessing grid (filter cutoffs, line-noise, bad-channel criteria, ICA + ICLabel thresholds, ASR cutoff, re-reference); (c) LLM-agent controller with tool calls over the same grid / FOOOF-SNR 指標；預處理格的窮舉/貪婪搜尋；LLM 智能體控制器 |
| **Comparison / Control** | Fixed standard pipelines (PREP, HAPPE/Automagic defaults), random/default parameters, and classical search (Bayesian optimization, random search) — the agent's baselines / 固定標準管線、隨機/預設參數、古典搜尋 |
| **Outcomes / 產出** | Primary: FOOOF-SNR. Secondary: agreement with oracle (regret), agreement with expert ratings, query/compute cost, policy-interpretability metrics / 主要：FOOOF-SNR；次要：與 oracle 一致性（regret）、與專家一致性、查詢/計算成本、可解釋性指標 |
| **Setting / 場景** | Offline, reproducible batch preprocessing on public data / 離線、可重現、公開資料批次處理 |
| **Design / 設計** | Computational methods study: oracle construction + metric validation + agent training/evaluation with held-out generalization / 計算方法研究：oracle 建構 + 指標驗證 + 智能體訓練/評估與留出泛化 |
| **Timeframe / 時程** | Static recordings (no longitudinal follow-up); per-recording windows for time-resolved scoring where relevant / 靜態記錄；必要時逐記錄窗化評分 |

### OUT Scope / 排除範圍

| Excluded | Rationale (reviewer-defensible) |
|----------|---------------------------------|
| **Online / real-time preprocessing** / 線上即時預處理 | The oracle requires offline exhaustive search; real-time is a separate engineering problem with different constraints / oracle 需離線窮舉；即時是不同約束的另一工程問題 |
| **Invasive / iEEG / LFP, MEG** / 侵入式、iEEG/LFP、MEG | Different noise/artifact structure and aperiodic interpretation; would dilute the scalp-EEG focus (and the FOOOF caveats differ per GersterEtAl2022) / 雜訊/artifact 結構與非週期解讀不同；會稀釋頭皮 EEG 焦點 |
| **Pediatric/neonatal cohorts as primary** / 以兒童/新生兒為主 | High-artifact developmental data needs specialized pipelines (HAPPE/NEAR); a separate validation study / 高 artifact 發展資料需專用管線，屬另一驗證研究 |
| **Downstream task accuracy as the objective** / 以下游任務準確率為目標 | The entire premise is to replace accuracy with a label-free metric; accuracy is reported only as an external sanity check, not the target / 整個前提是以無標籤指標取代準確率；準確率僅作外部 sanity check |
| **Clinical deployment / diagnosis claims** / 臨床部署/診斷宣稱 | Pilot-stage methods research; no regulatory or clinical-validation scope / 前導期方法研究；無法規或臨床驗證範圍 |
| **Reinforcement-learning agent variant** / RL 智能體變體 | Locked on the LLM-agent route per the user's decision; RL is an acknowledged alternative left for future work / 依使用者決定鎖定 LLM-agent 路線；RL 為未來工作 |

---

## Conceptual Framework / 概念架構

```
                    PUBLIC EEG DATASETS (resting-state + task/ERP)
                                   │
            ┌──────────────────────┼──────────────────────┐
            ▼                      ▼                      ▼
   ┌─────────────────┐   ┌──────────────────┐   ┌────────────────────┐
   │ GAP_002 ORACLE  │   │ GAP_001 METRIC   │   │ GAP_003 LLM-AGENT  │
   │ exhaustive grid │   │ FOOOF-SNR =      │   │ reads features →   │
   │ search over     │──▶│ periodic power / │◀──│ picks next op +    │
   │ preprocessing   │   │ (aperiodic 1/f + │   │ params via tools   │
   │ configurations  │   │  fit residual)   │   │ (search-augmented) │
   └─────────────────┘   │ + GAP_004 guard: │   └────────────────────┘
            │            │ band-limited SNR │            │
            │            │ + fit-quality    │            │
            ▼            │   gating         │            ▼
   per-recording best   └──────────────────┘   agent policy + rationale
   config (ground truth) ───────────┬───────────────────┘
                                     ▼
                     EVALUATION: regret vs oracle, vs fixed
                     pipelines, vs Bayesian-opt/random search;
                     held-out generalization; interpretability
```

The three pillars form a pipeline: the **metric** (GAP_001) scores outputs; the **oracle** (GAP_002) uses the metric to label each recording's best configuration; the **agent** (GAP_003) learns to reach those configurations cheaply and interpretably. GAP_004's guard lives inside the metric. / 三支柱構成管線：指標評分輸出；oracle 用指標標註每筆最佳組態；智能體學會廉價且可解釋地達到該組態。GAP_004 的防護置於指標內。

## Risk Assessment / 風險評估

| Risk / 風險 | Likelihood | Impact | Mitigation / 緩解 |
|-------------|:----------:|:------:|-------------------|
| FOOOF-SNR does not agree with expert/oracle quality (metric invalid) — the keystone fails | Medium | High | Test RQ1/H2 FIRST and cheaply before building the agent; pre-register correlation threshold; fall back to composite metric (FOOOF-SNR + Delorme2023-style contrast where labels exist) / 先廉價測 RQ1；預註冊門檻；必要時用複合指標 |
| Aperiodic-as-signal penalized by naive SNR (GAP_004) | Medium | High | Band-limited periodic SNR; goodness-of-fit gating per GersterEtAl2022; simulate artifacts with known aperiodic ground truth / 帶限週期 SNR；擬合優度把關；以已知非週期 ground truth 模擬 artifact |
| Exhaustive search compute blows up (grid too large) | High | Medium | Start with greedy/coordinate search + a coarse grid; cache intermediate stages; bound the parameter grid; report what was pruned / 先貪婪/座標搜尋+粗格；快取中間階段；明示剪枝 |
| LLM agent fails to beat classical search (transfer doesn't hold) | Medium | Medium | Frame agent as interpretable + cheaper-at-inference even if not strictly best; classical search remains a valid baseline contribution / 將智能體定位為可解釋且推論更便宜；古典搜尋仍為有效基準 |
| Public-dataset heterogeneity (montages, sampling rates) breaks the pipeline | Medium | Medium | Restrict to BIDS-EEG datasets; standardize montage/resampling; report dataset inclusion criteria / 限 BIDS-EEG；標準化導程/重採樣 |

> Files / 檔案: `step8_hypothesis_specification.md`, `step8_journal_recommendations.md`
> Next step / 下一步: `/research-write` (Step 9 manuscript)
