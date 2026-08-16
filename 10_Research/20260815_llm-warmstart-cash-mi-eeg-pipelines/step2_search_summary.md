---
session_id: "20260815"
topic: "LLM-informed warm-starting and prior injection for Bayesian optimization (CASH) of subject-specific EEG motor-imagery preprocessing pipelines, with cross-subject transfer"
date: "2026-08-15"
step: 2
---

# Search Summary / 搜尋摘要

> Topic / 研究主題: LLM-informed warm-starting and prior injection for Bayesian optimization (CASH) of subject-specific EEG motor-imagery preprocessing pipelines, with cross-subject transfer
> Sources / 資料來源: Semantic Scholar, OpenAlex, arXiv, PubMed (Q2/Q5 only)
> Date / 搜尋日期: 2026-08-15

## Results / 搜尋結果

| Query | Strategy / 策略 | Unique / 唯一 |
|-------|----------------|--------|
| Q1 | LLM × BO/HPO warm-start core terms / LLM×貝葉斯最佳化暖啟動核心詞 | 76 |
| Q2 | EEG MI pipeline automation synonyms / EEG 運動想像管線自動化同義詞 | 52 |
| Q3 | Meta-learning warm-start mechanism / meta-learning 暖啟動機制 | 32 |
| Q4 | AutoML/CASH system evaluation / AutoML 系統評估方法學 | 55 |
| Q5 | Cross-subject EEG transfer (cross-disciplinary) / 跨受試者 EEG 遷移（跨領域） | 117 |
| Snowball | Top-cited references / 高引用文獻延伸 | 25 |
| **Total / 總計** | Raw 366 → dedup 332 → +snowball | **357** |

- DOI coverage / DOI 覆蓋率: 262/357 (73%)
- Per source / 各來源命中: OpenAlex 125, arXiv 104, Semantic Scholar 79, PubMed 60
- Multi-source overlap / 多來源重疊: 11 papers（低，反映各來源索引差異）
- Note / 備註: Semantic Scholar 對多詞長查詢回傳偏少（Q2–Q4 為 0），已由 OpenAlex / arXiv / PubMed 補足，並以較短查詢補查一輪。

## Seed Papers (Snowball) / 種子文獻

1. Feurer et al. (2015) "Initializing Bayesian Hyperparameter Optimization via Meta-Learning" — warm-start 譜系起點
2. Li et al. (2021) "VolcanoML" — 分解式 CASH 系統（實驗室現用框架）
3. "Reproducibility Study of Large Language Model Bayesian Optimization" (2025) — LLAMBO 重現性研究
4. Bischl et al. (2023) "Hyperparameter optimization: Foundations..." — HPO 全景綜述
5. Craik et al. (2019) "Deep learning for EEG classification tasks: a review" — EEG 解碼端

## Hub Papers / 核心文獻（依集合內被引數）

1. "Evaluation of a Tree-based Pipeline Optimization Tool for Automating Data Science" (TPOT, Olson 2016) — in_degree 11
2. "Auto-WEKA: Automatic Model Selection and Hyperparameter Optimization in WEKA" — in_degree 10 — CASH 問題的起源系統
3. "Auto-Keras: An Efficient Neural Architecture Search System" — in_degree 7
4. "Deep learning for EEG classification tasks: a review" (Craik 2019) — in_degree 7 — EEG 解碼端的錨點
5. "Initializing Bayesian Hyperparameter Optimization via Meta-Learning" (Feurer 2015) — in_degree 6 — 暖啟動機制的奠基作
6. "Automated Machine Learning" (Hutter et al. 2019 book) — in_degree 5
7. "Hyperopt" / "Hyperband" — in_degree 5 — HPO 工具鏈基礎

## Citation Clusters / 引用聚類

- **eeg_pipeline_optimization** (105 papers) — EEG/BCI decoding pipelines: automated preprocessing, NAS/HPO for EEG, cross-subject and subject-specific adaptation / EEG 解碼管線：自動化前處理、EEG 專用 NAS/HPO、跨受試者與受試者特定調適
- **automl_systems_cash** (67 papers) — AutoML systems and CASH: auto-sklearn, TPOT, VolcanoML, SMAC, benchmarks, search-space decomposition / AutoML 系統與 CASH：基準評測與搜尋空間分解
- **llm_for_automl** (44 papers) — LLMs as optimizers, prior injectors, surrogates, or agents for pipeline search / LLM 作為最佳化器、先驗注入者、代理模型或管線搜尋 agent
- **hpo_warmstart_transfer** (43 papers) — Warm-starting, meta-learning, and transfer for BO/HPO / BO 暖啟動、meta-learning 與遷移（搜尋歷史重用、先驗、預設值）
- Peripheral / 外圍 (98 papers) — OpenAlex 全文檢索帶入的邊緣結果，將由 Step 3 篩選過濾

## Yield Assessment / 產量評估

The collection covers all four pillars of the research question: the LLM-for-AutoML line (LLAMBO, HALO, SEMBO, LangBO, tree-structured LLM+BO for CASH), the warm-start/transfer-HPO mechanism line (Feurer 2015, TransBO, πBO, few-shot BO, learned defaults), the CASH/system line (Auto-WEKA, TPOT, VolcanoML, OpenBox, Rising-Bandits CASH), and the EEG side (EEG NAS, cross-subject transfer, subject-specific decoding, HPO-for-EEG-decoding). 357 papers is intentionally over-inclusive — roughly a quarter are peripheral results from full-text matching and will be filtered by Step 3 screening. Notably, the intersection "LLM/warm-start CASH applied to EEG pipelines" appears very thin in the raw hits, which is preliminary evidence that the proposed direction is underexplored.

本次收集涵蓋研究問題的四大支柱：LLM×AutoML 路線（LLAMBO、HALO、SEMBO、LangBO、樹狀 LLM+BO for CASH）、暖啟動／遷移 HPO 機制路線（Feurer 2015、TransBO、πBO、few-shot BO、learned defaults）、CASH／系統路線（Auto-WEKA、TPOT、VolcanoML、OpenBox、Rising-Bandits CASH），以及 EEG 端（EEG NAS、跨受試者遷移、受試者特定解碼、EEG 解碼的 HPO 評估）。357 篇屬刻意寬鬆收集——約四分之一為全文檢索帶入的外圍結果，將由 Step 3 篩選階段過濾。值得注意的是，「LLM／暖啟動 CASH 應用於 EEG 管線」這個交集在原始命中中非常稀少，這是該方向尚未被充分探索的初步證據。

---

Files / 檔案: `step2_raw_papers.json`
Next step / 下一步: `/research-screen`
