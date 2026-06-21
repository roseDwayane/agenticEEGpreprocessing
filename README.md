# Agentic EEG Preprocessing Pipeline
### 智能體式 EEG 預處理流程 — 以 FOOOF 訊號品質為最佳化目標

> An LLM-agent that learns to choose EEG preprocessing parameters, optimized against a label-free spectral quality metric (FOOOF-SNR) instead of downstream task accuracy.
>
> 一個會學習挑選 EEG 預處理參數的 LLM 智能體，以無標籤的頻譜品質指標（FOOOF-SNR）為最佳化目標，取代傳統的下游任務準確率。

---

## 1. The Big Idea / 核心構想

EEG preprocessing is a sequence of choices — filter cutoffs, ICA thresholds, bad-channel criteria, artifact-rejection levels — and **those choices materially change the results** ([Robbins et al., 2020](https://doi.org/10.1109/tnsre.2020.2980223)). Yet the field has **no objective, label-free way to decide whether one preprocessing run is better than another** ([Pedroni et al., 2019](https://doi.org/10.1016/j.neuroimage.2019.06.046); [Delorme, 2023](https://doi.org/10.1038/s41598-023-27528-0)). Today this is judged by downstream classifier accuracy (needs labels) or subjective visual inspection.

EEG 預處理是一連串選擇——濾波截止、ICA 閾值、壞通道準則、artifact 拒絕強度——而**這些選擇會實質改變結果**。然而領域裡**沒有客觀、無標籤的方法來判斷哪一次預處理比較好**。目前只能靠下游分類器準確率（需要標籤）或主觀目視檢查。

This project proposes to fix that with **three coupled ideas**:

本計畫以**三個耦合的構想**來解決：

| # | Pillar / 支柱 | What / 內容 |
|---|---------------|-------------|
| **1** | **Ground-truth oracle** (greedy search) | Exhaustively run every preprocessing configuration over public EEG, score each, and record the **best config per recording**. 用貪婪/窮舉搜尋跑遍所有預處理組態，逐筆評分，記錄每筆記錄的最佳組態。 |
| **2** | **FOOOF-SNR quality metric** | Score quality by the ratio of **periodic power (signal)** to **aperiodic 1/f + residual (noise)** from [FOOOF](https://doi.org/10.1038/s41593-020-00744-x) — task-free, label-free, computable on a single resting-state recording. 以 FOOOF 分解出的**週期功率（訊號）**對**非週期 1/f + 殘差（雜訊）**的比值評分品質——任務無關、無標籤、單筆靜息態即可算。 |
| **3** | **LLM-agent controller** | With ground truth in hand, an LLM agent reads intermediate signal features and **learns to pick preprocessing parameters**, recovering near-optimal quality while staying interpretable. 有了 ground truth 後，LLM 智能體讀取中間訊號特徵，**學會挑選預處理參數**，回收近最佳品質且保持可解釋。 |

### Why FOOOF-SNR instead of accuracy? / 為何用 FOOOF-SNR 而非準確率？

A neural power spectrum splits into two parts:
神經功率譜可分為兩部分：

```
         log power
            │   ╭─ periodic peaks  (oscillations = SIGNAL / 訊號)
            │  ╱╲      ╭╮
            │ ╱  ╲    ╱  ╲
            │╱    ╲__╱    ╲___
            │ ╲                  ← aperiodic 1/f background (NOISE-like / 雜訊樣)
            │   ╲___
            └─────────────────── log frequency

   FOOOF-SNR  =  periodic power  /  (aperiodic 1/f  +  fit residual)
```

Accuracy needs a labelled downstream task; FOOOF-SNR needs only the recording itself. That makes preprocessing **optimizable** — you can search for, and learn to predict, the parameters that maximize it.

準確率需要有標籤的下游任務；FOOOF-SNR 只需要記錄本身。這讓預處理**可被最佳化**——你可以搜尋、並學習預測能最大化它的參數。

> ⚠️ **Key design risk / 關鍵設計風險:** the aperiodic component is *real neural signal* ([Donoghue et al., 2020](https://doi.org/10.1038/s41593-020-00744-x); [Lendner et al., 2020](https://doi.org/10.7554/eLife.55092)), not pure noise. A naïve "minimize aperiodic" reward could destroy legitimate physiology. The reward must distinguish **aperiodic-as-physiology** (E/I balance, arousal) from **aperiodic-as-artifact** (EMG, drift) using band-limited scoring + goodness-of-fit gating. See [`step7_gap_analysis.md` GAP_004](10_Research/20260620_agentic-eeg-preprocessing-fooof/step7_gap_analysis.md).
>
> 非週期成分是*真實神經訊號*，不是純雜訊。天真的「最小化非週期」獎勵可能破壞正當生理。獎勵必須以帶限評分 + 擬合優度把關，區分非週期生理（E/I、喚醒）與非週期 artifact（EMG、漂移）。

---

## 2. Project Status / 計畫狀態

This repository currently contains a **complete literature-research phase** (the `/research` pipeline, Steps 1–9) plus a **knowledge-system extraction**. The implementation (the actual oracle + metric + agent code) is the **next phase**.

本 repo 目前包含**完整的文獻研究階段**（`/research` pipeline，Steps 1–9）與**知識系統萃取**。實作（真正的 oracle + 指標 + agent 程式碼）是**下一階段**。

| Phase / 階段 | Status / 狀態 | Location / 位置 |
|--------------|:-------------:|-----------------|
| 📚 Literature research (PICO → manuscript) | ✅ Done | [`10_Research/.../`](10_Research/20260620_agentic-eeg-preprocessing-fooof/) |
| 🧠 Knowledge cards (Point/Line/Plane/Body) | ✅ Done | [`.../knowledge_system/`](10_Research/20260620_agentic-eeg-preprocessing-fooof/knowledge_system/) |
| 🔬 Hypothesis + scope + target journals | ✅ Done | [`step8_hypothesis_specification.md`](10_Research/20260620_agentic-eeg-preprocessing-fooof/step8_hypothesis_specification.md) |
| 📝 Manuscript intro + related work (LaTeX) | ✅ Done | [`step9_manuscript/`](10_Research/20260620_agentic-eeg-preprocessing-fooof/step9_manuscript/) |
| ⚙️ Implementation (oracle / metric / agent) | ⬜ Next | _to be built_ |
| 🧪 Experiments + results | ⬜ Future | _to be built_ |

**Headline finding from the research phase:** the project sits in an empty cell — *no prior work combines (FOOOF-SNR as objective) × (agentic search) × (EEG preprocessing)*. Two independent papers ([Pedroni 2019](https://doi.org/10.1016/j.neuroimage.2019.06.046), [Delorme 2023](https://doi.org/10.1038/s41598-023-27528-0)) independently complain about the same missing metric — the strongest single justification for the work.

**研究階段的關鍵發現：** 計畫落在一個空格——*沒有前作結合（FOOOF-SNR 為目標）×（智能體搜尋）×（EEG 預處理）*。兩篇獨立論文各自抱怨同一個缺失的指標——這是計畫最強的單一正當理由。

---

## 3. Repository Layout / 檔案結構

```
agenticEEGpreprocessing/
├── README.md                          ← (this file)
└── 10_Research/
    └── 20260620_agentic-eeg-preprocessing-fooof/
        ├── step0_session_config.json      PICO + queries + metadata
        ├── step1_search_queries.md        5 search strategies (bilingual)
        ├── step2_raw_papers.json          69 deduplicated papers + citation network
        ├── step2_search_summary.md        yield, themes, hub papers
        ├── step3_screening_results.md     53 included / 13 borderline / 3 excluded
        ├── step3_shortlist.json           the 53 scored, included papers
        ├── step4_references.bib           53-entry BibTeX (validated)
        ├── step4_references_apa.md         APA-7 reference list
        ├── step4_citation_keys.md          AuthorYear → paper lookup
        ├── step5_full_text/                9 bilingual reading notes (6 full-text)
        ├── step6_sota_review.md            5-theme state-of-the-art synthesis
        ├── step6_knowledge_graph.canvas    59-node Obsidian Canvas graph
        ├── step7_gap_analysis.md           4 evidence-scored research gaps
        ├── step8_hypothesis_specification.md  4 RQs, 4 hypotheses, IN/OUT scope, risks
        ├── step8_journal_recommendations.md   5 target venues w/ verified IFs
        ├── step9_manuscript/               01_intro.tex, 02_relatedwork.tex, references.bib
        └── knowledge_system/               Point/Line/Plane/Body knowledge cards
            ├── points/   (18 P_draft cards)
            ├── lines/    (4 standalone + frontmatter_patches.md)
            ├── planes/    (5 F_draft theme cards)
            ├── body/      (1 B_draft research-programme card)
            ├── _MANIFEST.md
            └── _DEDUP_REPORT.md
```

---

## 4. How the Research Was Built — Step by Step / 研究如何建立（逐步）

The literature phase ran the 9-step `/research` pipeline. Each step's output feeds the next.

文獻階段跑了 9 步的 `/research` pipeline，每一步的產出餵給下一步。

| Step | Name | What it did / 做了什麼 | Output |
|:----:|------|------------------------|--------|
| **1** | init | Parsed the topic into a **PICO** frame + 5 search queries | `step0`, `step1` |
| **2** | search | Searched OpenAlex / arXiv / WebSearch → **69 unique papers**, 98.6% DOI coverage, 4 citation clusters | `step2_*` |
| **3** | screen | Scored every paper on relevance × quality × recency → **53 included** | `step3_*` |
| **4** | export | Generated **APA-7 + BibTeX + citation keys**, cross-validated | `step4_*` |
| **5** | fulltext | Fetched **9 priority papers** in full + wrote bilingual notes | `step5_full_text/` |
| **6** | sota | Synthesized a **5-theme** state-of-the-art review + knowledge graph | `step6_*` |
| **7** | gaps | Identified **4 evidence-grounded gaps**, scored & ranked | `step7_*` |
| **8** | hypothesis | Wrote **4 RQs + 4 hypotheses + IN/OUT scope + 5 journals** | `step8_*` |
| **9** | write | Drafted **LaTeX intro + related work**, zero phantom citations | `step9_manuscript/` |

The 5 SOTA themes / 五個 SOTA 主題:
1. **Standardized EEG preprocessing pipelines** (PREP, HAPPE, Autoreject, ICLabel) — the agent's *action space*
2. **FOOOF / spectral parameterization** — the *quality metric*
3. **Aperiodic activity as validated signal** — the *design constraint*
4. **Preprocessing sensitivity & objective quality** — the *motivation* (closest prior art)
5. **AutoML → LLM agents** — the *method machinery*

---

## 5. The Research Plan — What Comes Next / 研究計畫（下一步要做什麼）

From [`step8_hypothesis_specification.md`](10_Research/20260620_agentic-eeg-preprocessing-fooof/step8_hypothesis_specification.md). The implementation should be built in this order, because each stage depends on the previous one.

實作應依此順序建立，因為每階段都依賴前一階段。

```
   PUBLIC EEG DATASETS (resting-state + task/ERP, BIDS-EEG)
                         │
   ┌─────────────────────┼─────────────────────┐
   ▼                     ▼                     ▼
 STAGE A               STAGE B               STAGE C
 FOOOF-SNR metric      Ground-truth oracle   LLM-agent controller
 (validate FIRST)      (exhaustive search)   (learn to pick params)
   │                     │                     │
   │  H2: rank agrees     │  H1: per-recording   │  H3: recovers
   │  with oracle+expert  │  optimum > fixed      │  near-oracle, cheap
   ▼                     ▼                     ▼
              EVALUATION: regret vs oracle, vs fixed pipelines,
              vs Bayesian-opt / random search; held-out generalization;
              interpretability of the agent's policy (H4)
```

### Stage A — Build & validate the FOOOF-SNR metric (do this first to de-risk)
**階段 A — 建立並驗證 FOOOF-SNR 指標（先做，去風險）**

1. Implement `FOOOF-SNR = periodic power / (aperiodic 1/f + fit residual)` with band-limited scoring + goodness-of-fit gating (the GAP_004 guard).
2. Test **H2**: does FOOOF-SNR rank preprocessing outputs in agreement with (a) exhaustive search and (b) a small set of expert-rated recordings? (Spearman ρ, pre-registered threshold.)
3. ✅ Gate: if the metric doesn't agree with experts, **stop here** — cheap failure before building the agent.

### Stage B — Build the ground-truth oracle (greedy/exhaustive search)
**階段 B — 建立 ground-truth oracle（貪婪/窮舉搜尋）**

1. Define the preprocessing grid: filter cutoffs, line-noise, bad-channel criteria, ICA + ICLabel thresholds, ASR cutoff `k`, re-reference.
2. Run greedy/coordinate search over public datasets; cache intermediate stages; **log what was pruned**.
3. Record the **best config per recording** → a reusable benchmark dataset.
4. Test **H1**: do per-recording optima beat the best fixed pipeline, and how variable is the optimum across recordings?

### Stage C — Train & evaluate the LLM-agent controller
**階段 C — 訓練並評估 LLM 智能體控制器**

1. Build an agent that reads intermediate features (spectra, channel stats, IC labels, interim FOOOF estimates) and calls preprocessing tools to select the next operation + parameters.
2. Train/evaluate against the oracle (supervision + upper bound).
3. Test **H3**: does the agent reach near-oracle quality on **held-out** datasets, at lower per-recording cost than Bayesian-opt/random search?
4. Test **H4**: does its learned policy recover known heuristics (interpolate bad channels, high-pass before ICA) and surface recording-specific rules?

### Baselines to beat / 要勝過的對手
Fixed standard pipelines (PREP, HAPPE, Automagic defaults) · random/default parameters · classical search (Bayesian optimization, random search).

---

## 6. Getting Started / 開始使用

### Prerequisites / 環境需求

```bash
# Python 3.10+ (this repo was developed on the `drug` conda env)
# Already installed: numpy, scipy, scikit-learn, torch, pandas, anthropic, openai
# Still needed for implementation:
pip install mne fooof          # or:  pip install mne specparam   (FOOOF is now 'specparam')
pip install mne-icalabel       # automated ICA component labelling
# Optional: pyprep (PREP), autoreject, asrpy
```

> **Note / 註:** the `fooof` package was renamed to `specparam`. New code should `import specparam`; legacy examples use `from fooof import FOOOF`.

### Reading the research (no code needed yet) / 閱讀研究（暫不需程式碼）

```bash
# Start with the spine of the argument:
open 10_Research/20260620_agentic-eeg-preprocessing-fooof/step6_sota_review.md      # what the field knows
open 10_Research/20260620_agentic-eeg-preprocessing-fooof/step7_gap_analysis.md     # the 4 gaps
open 10_Research/20260620_agentic-eeg-preprocessing-fooof/step8_hypothesis_specification.md  # the plan

# Visual knowledge graph (open in Obsidian):
open 10_Research/20260620_agentic-eeg-preprocessing-fooof/step6_knowledge_graph.canvas
```

### Suggested public datasets / 建議的公開資料集
- **BIDS-EEG** formatted resting-state corpora on [OpenNeuro](https://openneuro.org/)
- Restrict to BIDS-EEG to standardize montages and sampling rates (a documented risk in the scope).

---

## 7. Key Concepts (Quick Reference) / 關鍵概念速查

| Term | Meaning / 意義 |
|------|----------------|
| **FOOOF / specparam** | Decomposes a power spectrum into aperiodic 1/f background + periodic Gaussian peaks. 把功率譜分解為非週期 1/f 背景 + 週期高斯峰。 |
| **Aperiodic component** | The 1/f background (offset + exponent). *Real signal*, not just noise. 1/f 背景（offset + exponent）；是真實訊號。 |
| **Periodic component** | Oscillatory peaks above the background = the "signal" in FOOOF-SNR. 背景之上的振盪峰 = FOOOF-SNR 的「訊號」。 |
| **FOOOF-SNR** | This project's label-free quality metric = periodic / (aperiodic + residual). 本計畫無標籤品質指標。 |
| **Oracle / ground truth** | Best preprocessing config per recording, found by exhaustive search. 由窮舉搜尋找到的每筆最佳預處理組態。 |
| **Regret** | How far the agent's choice falls below the oracle's optimum. agent 選擇距 oracle 最佳的差距。 |
| **IRASA** | Alternative to FOOOF; separates fractal from oscillatory power by resampling. FOOOF 的替代法。 |

For the full structured knowledge base, see [`knowledge_system/`](10_Research/20260620_agentic-eeg-preprocessing-fooof/knowledge_system/) (18 concept cards, 5 theme planes, 1 programme body).

---

## 8. Target Venues / 目標期刊

From [`step8_journal_recommendations.md`](10_Research/20260620_agentic-eeg-preprocessing-fooof/step8_journal_recommendations.md) (impact factors verified 2025):

| Venue | IF | Role |
|-------|----|------|
| **Journal of Neural Engineering** | ~4.16 (Q1) | 🎯 Primary — methods + neuroengineering fit |
| **NeuroImage** | ~4.5 (Q1) | Aspirational — home of Autoreject/ICLabel/Automagic |
| **IEEE TNSRE** | ~4.5 | Moderate — published the motivating Robbins 2020 paper |
| **J. Neuroscience Methods** | ~2.3 | Accessible fallback |
| **NeurIPS D&B / TMLR** | — | Wildcard — the oracle-as-benchmark + agent-transfer angle |

A two-paper strategy is viable: a *neuro-methods* paper (metric + benchmark) and an *ML/agent* paper (LLM agent transfers to electrophysiology).
