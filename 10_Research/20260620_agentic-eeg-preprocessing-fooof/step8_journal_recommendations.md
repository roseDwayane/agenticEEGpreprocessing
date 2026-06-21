---
session_id: "20260620"
topic: "Agentic EEG preprocessing pipeline with FOOOF-based signal quality optimization"
date: "2026-06-20"
step: 8
---

# Journal Recommendations / 目標期刊建議

> Profile: a computational **methods** paper combining EEG signal processing, a novel spectral quality metric, and an LLM-agent — straddling neuroengineering and ML. Strategy: one aspirational, two moderate, one accessible, plus one ML-venue wildcard. / 結合 EEG 訊號處理、新頻譜品質指標與 LLM 智能體的計算方法論文，橫跨神經工程與 ML。

## Ranked Recommendations / 排序建議

### 1. Journal of Neural Engineering (IOP) — **Primary target / 首選**
- **Impact Factor:** ~4.16 (2024, computed 2025), Q1; CiteScore 8.4
- **Scope fit / 契合度:** Explicitly covers "neural signal processing", "neuroinformatics", and computational/methods work on EEG/BCI — a near-perfect home for a preprocessing-methods + agent paper. Already publishes EEG methods and foundation-model work. / 明確涵蓋神經訊號處理、神經資訊學與 EEG/BCI 計算方法——預處理方法+智能體論文的近乎完美歸宿。
- **Why / 理由:** Methods-friendly, neuroengineering audience that cares about reproducible pipelines; the "agentic" angle fits its neurotechnology framing. BashashatiEtAl2016 (BO for BCI pipelines, in our collection) is a JNE-adjacent precedent. / 對方法友善的神經工程讀者；智能體切入點契合其神經科技框架。
- **OA / 開放取用:** Hybrid OA available (APC applies). Typical first decision: ~6–10 weeks.

### 2. NeuroImage (Elsevier) — **Aspirational / 進取目標**
- **Impact Factor:** ~4.5 (2025), Q1; 5-yr ~5.9
- **Scope fit / 契合度:** The canonical venue for EEG/MEG methods — **Autoreject (JasEtAl2017), ICLabel (PionTonachiniEtAl2019), Automagic (PedroniEtAl2019), HAPPILEE (LopezEtAl2022)** all published here. Editors/reviewers are primed for preprocessing-pipeline contributions. / EEG/MEG 方法的標竿期刊；我們收藏中多篇關鍵管線論文皆發表於此。
- **Why / 理由:** Highest topical authority for the preprocessing community; a positive result on the FOOOF-SNR metric would land squarely in its readership. Bar is high (rigorous validation expected). / 對預處理社群最具權威；門檻高，需嚴謹驗證。
- **OA / 開放取用:** Hybrid OA (high APC). First decision: ~1–3 months.

### 3. IEEE Transactions on Neural Systems & Rehabilitation Engineering (TNSRE) — **Moderate / 穩健**
- **Impact Factor:** ~4.5–4.9 (recent), Q1/Q2
- **Scope fit / 契合度:** Published **RobbinsEtAl2020 ("How Sensitive Are EEG Results to Preprocessing")** — the paper that most directly motivates our work. Strong fit for benchmark + systems-engineering framing of the oracle and agent. / 發表了最直接motivate 本研究的 RobbinsEtAl2020；契合 oracle 與智能體的基準+系統工程框架。
- **Why / 理由:** Receptive to quantitative benchmarking and engineered systems; the per-recording-optimal benchmark (GAP_002) is a natural TNSRE contribution. / 對量化基準與工程系統友善。
- **OA / 開放取用:** Hybrid OA. First decision: ~2–4 months.

### 4. Journal of Neuroscience Methods (Elsevier) — **Accessible / 可達**
- **Impact Factor:** ~2.3 (2025), Q3
- **Scope fit / 契合度:** Dedicated methods venue; routinely publishes EEG preprocessing tools/metrics. Lower bar, still reputable and indexed. / 專門方法期刊；常發表 EEG 預處理工具/指標；門檻較低仍具聲譽。
- **Why / 理由:** A safe landing spot if the headline result is solid but incremental, or for a tool/benchmark release paper accompanying the main study. / 若主結果穩健但偏漸進，或作為工具/基準釋出論文的落點。
- **OA / 開放取用:** Hybrid OA. First decision: ~1–2 months.

### 5. (Wildcard, ML venue) Transactions on Machine Learning Research (TMLR) / a NeurIPS Datasets & Benchmarks track — **For the agent/benchmark framing / 智能體+基準框架**
- **Scope fit / 契合度:** If the contribution is framed primarily as "LLM agents transfer to a new scientific domain + a new benchmark", an ML venue reaches the agent community (the GuoEtAl2024/ChiEtAl2024/TriratEtAl2024 audience). NeurIPS D&B specifically rewards new benchmark datasets like the GAP_002 oracle. / 若主貢獻框定為「LLM 智能體遷移到新科學領域+新基準」，ML 場域可觸及智能體社群。
- **Why / 理由:** Higher novelty reward for the agentic angle; trade-off is less neuro-domain depth scrutiny. Consider a two-paper strategy (methods paper → neuro venue; benchmark/agent → ML venue). / 對智能體切入的新穎性獎勵更高；可考慮雙論文策略。

## Strategy Note / 策略建議

The work has **two sellable identities**: a *neuro methods* paper (metric + preprocessing benchmark → JNE/NeuroImage/TNSRE) and an *ML/agent* paper (LLM agent transfers to electrophysiology + new benchmark → TMLR/NeurIPS D&B). Recommended path: validate the metric (RQ1) and build the oracle first, target **Journal of Neural Engineering** for the integrated story, and keep the NeurIPS D&B benchmark release as a parallel option. / 本研究有兩種可售身分：神經方法論文與 ML/智能體論文。建議先驗證指標並建 oracle，以 JNE 投整合故事，並保留 NeurIPS D&B 基準釋出為平行選項。
