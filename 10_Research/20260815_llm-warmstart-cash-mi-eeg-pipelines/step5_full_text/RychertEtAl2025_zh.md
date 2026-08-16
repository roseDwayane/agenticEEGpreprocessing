---
citation_key: "RychertEtAl2025"
title: "Reproducibility Study of Large Language Model Bayesian Optimization"
authors: "Adam Rychert; Gasper Spagnolo; Evgenii Posashkov"
year: 2025
doi: "10.48550/arxiv.2511.18891"
source: "arXiv (2511.18891)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "2511.18891"
conversion: "pdftotext -layout (automated)"
language: "bilingual-en-zh"
---

# Reproducibility Study of Large Language Model Bayesian Optimization | 大型語言模型貝葉斯最佳化之重現性研究

> [!abstract] 重點摘要
> - 本文為 LLAMBO 框架的重現性研究（reproducibility study）：LLAMBO 以純文字提示（prompting）讓大型語言模型（LLM）扮演貝葉斯最佳化（Bayesian optimization, BO）中的判別式代理模型（discriminative surrogate）與擷取最佳化器，作者在原始評估協定下重現其 Bayesmark 與 HPOBench 核心實驗。
> - 關鍵改動：以開放權重（open-weight）的 Llama 3.1 70B 取代原論文的 GPT-3.5 作為所有文字編碼組件的骨幹模型，並改用本地 ollama 呼叫、統一 JSON 輸出格式、重新實作 GP、SMAC、TPE、Random 等基線與評估繪圖流程。
> - 重現結果大致證實原論文主張：以文字化問題與超參數描述進行的情境式暖啟動（contextual warmstarting）顯著改善早期遺憾值（early regret）並降低跨執行變異；LLAMBO 的候選點取樣器持續產生比 TPE 與隨機取樣品質更高、更多樣的提案。
> - 作為單一任務迴歸器，LLAMBO 的判別式代理模型弱於 GP 與 SMAC（NRMSE 較高、不確定性低估、校準較差），其優勢來自語言模型誘發的跨任務語意先驗（cross-task semantic priors）；移除文字脈絡的消融實驗使預測準確度與校準明顯劣化。
> - 較小的骨幹模型（Gemma 27B、Llama 3.1 8B）常輸出格式錯誤或與實際表現無關的代理分數，導致最佳化迴圈不穩定，顯示可靠的代理行為需要足夠的模型容量——在目前的提示方案下僅 70B 模型穩健可用。
> - 總結：LLAMBO 架構對更換語言模型骨幹具穩健性，以 Llama 3.1 70B 實例化時依然有效；所有程式碼與設定公開於 GitHub。

---

## Abstract | 摘要

> [!quote] Original
> In this reproduction study, we revisit the LLAMBO framework of Daxberger et al. (2024), a prompting-based Bayesian optimization (BO) method that uses large language models as discriminative surrogates and acquisition optimizers via text-only interactions. We replicate the core Bayesmark and HPOBench experiments under the original evaluation protocol, but replace GPT-3.5 with the open-weight Llama 3.1 70B model for all text-encoding components.
>
> Our results broadly confirm the main claims of LLAMBO. Contextual warmstarting via textual problem and hyperparameter descriptions substantially improves early-regret behaviour and reduces variance across runs. LLAMBO's discriminative surrogate is weaker than GP or SMAC as a pure single-task regressor, yet benefits from cross-task semantic priors induced by the language model. Ablations that remove textual context markedly degrade predictive accuracy and calibration, while the LLAMBO candidate sampler consistently generates higher-quality and more diverse proposals than TPE or random sampling. Experiments with smaller backbones (Gemma 27B, Llama 3.1 8B) yield unstable or invalid predictions, suggesting insufficient capacity for reliable surrogate behaviour.
>
> Overall, our study shows that the LLAMBO architecture is robust to changing the language-model backbone and remains effective when instantiated with Llama 3.1 70B. All code and configurations are available at https://github.com/spagnoloG/llambo-reproducibility.

> [!note] 翻譯
> 在這項重現研究中，我們重新檢視 LLAMBO 框架（Daxberger et al., 2024）——一種基於提示（prompting）的貝葉斯最佳化（Bayesian optimization, BO）方法，透過純文字互動讓大型語言模型（large language models, LLM）充當判別式代理模型（discriminative surrogates）與擷取最佳化器。我們在原始評估協定下重現其核心的 Bayesmark 與 HPOBench 實驗，但將所有文字編碼組件由 GPT-3.5 替換為開放權重（open-weight）的 Llama 3.1 70B 模型。
>
> 我們的結果大致證實了 LLAMBO 的主要主張。透過文字化的問題與超參數描述進行的情境式暖啟動（contextual warmstarting）顯著改善了早期遺憾值（early-regret）行為，並降低了跨執行的變異。作為純粹的單一任務迴歸器，LLAMBO 的判別式代理模型弱於 GP 或 SMAC，但受益於語言模型所誘發的跨任務語意先驗（cross-task semantic priors）。移除文字脈絡的消融實驗使預測準確度與校準（calibration）明顯劣化，而 LLAMBO 的候選點取樣器則持續產生比 TPE 或隨機取樣品質更高、更多樣的提案。以較小骨幹模型（Gemma 27B、Llama 3.1 8B）進行的實驗產生不穩定或無效的預測，顯示其容量不足以支撐可靠的代理模型行為。
>
> 總體而言，本研究顯示 LLAMBO 架構對於更換語言模型骨幹具有穩健性，且以 Llama 3.1 70B 實例化時仍然有效。所有程式碼與設定可於 https://github.com/spagnoloG/llambo-reproducibility 取得。

---

## Introduction | 引言

> [!quote] Original
> Black-box function optimization appears in many real-world problems such as robotics, experimental design, drug discovery, interface design, and, in machine learning, hyperparameter tuning. Bayesian Optimization (BO) is a standard approach in this setting: it builds a surrogate model from past evaluations and uses an acquisition strategy to propose promising candidates, requiring only function evaluations but no gradients or closed-form objective. However, BO is often used in regimes where data are extremely sparse, so performance depends critically on search efficiency, the quality of the surrogate under few observations, and how well prior knowledge can be incorporated across tasks.
>
> These few-shot challenges align naturally with the strengths of large language models (LLMs). Modern LLMs are trained on massive text corpora and show strong abilities in few-shot reasoning, pattern recognition, and contextual understanding. LLAMBO explores whether these capabilities can be used to support or even replace parts of the BO pipeline by expressing the entire loop in natural language. In the original work, an LLM is prompted with dataset and model metadata, past evaluations, and the current history, and is then asked to warmstart the search, act as a discriminative surrogate, and propose new hyperparameter candidates without fitting a conventional probabilistic model.
>
> This paper presents a reproducibility study of LLAMBO in an open-model setting. We reconstruct the prompting pipeline, apply it to hyperparameter tuning tasks from Bayesmark and HPOBench, and compare performance against classical BO baselines such as GP-, SMAC-, and TPE-based optimizers. In contrast to the original study, which relies on GPT-3.5, we replace the backbone with open-weight Llama 3.1 70B and briefly probe smaller models as well. This allows us to assess both how well the original findings carry over and how sensitive LLAMBO is to the choice and capacity of the underlying language model.

> [!note] 翻譯
> 黑盒函數最佳化出現於許多真實世界的問題中，例如機器人學、實驗設計、藥物發現、介面設計，以及機器學習中的超參數調校。貝葉斯最佳化（BO）是此情境下的標準方法：它從過去的評估建立代理模型（surrogate model），並利用擷取策略（acquisition strategy）提出有潛力的候選點，僅需函數評估，而不需要梯度或閉式目標函數。然而，BO 常被應用於資料極度稀疏的情況，因此其表現關鍵取決於搜尋效率、少量觀測下代理模型的品質，以及先驗知識能否被良好地跨任務納入。
>
> 這些少樣本（few-shot）挑戰與大型語言模型（LLM）的優勢天然契合。現代 LLM 在海量文字語料上訓練，展現出強大的少樣本推理、模式辨識與脈絡理解能力。LLAMBO 探索能否藉由以自然語言表達整個迴圈，讓這些能力支援甚至取代 BO 流程中的部分組件。在原始工作中，LLM 被提供資料集與模型的中介資料（metadata）、過去的評估結果與當前歷史作為提示，並被要求為搜尋進行暖啟動（warmstart）、充當判別式代理模型，以及在不擬合傳統機率模型的情況下提出新的超參數候選點。
>
> 本文呈現在開放模型設定下對 LLAMBO 的重現性研究。我們重建其提示流程（prompting pipeline），將之應用於 Bayesmark 與 HPOBench 的超參數調校任務，並與經典 BO 基線（如基於 GP、SMAC 與 TPE 的最佳化器）比較效能。與依賴 GPT-3.5 的原始研究不同，我們將骨幹模型替換為開放權重的 Llama 3.1 70B，並簡要測試較小的模型。這使我們得以同時評估原始發現能否延續，以及 LLAMBO 對底層語言模型之選擇與容量的敏感程度。

---

## Scope of Reproducibility | 重現範圍

> [!quote] Original
> In this study, we aim to reproduce the main findings reported in the LLAMBO paper [1], which tests whether large language models can replace key components of a Bayesian optimization loop when everything is expressed in natural language. LLAMBO claims that an LLM, prompted with dataset and model metadata plus past evaluations, can warmstart the search, propose new hyperparameter candidates, and estimate their performance without fitting a conventional surrogate model.
>
> Our reproduction focuses on the following aspects:
> - Warmstarting efficiency: verify that contextual, text-based warmstarts achieve lower early regret and reduced variance compared to standard space-filling designs (Random, Sobol, Latin Hypercube).
> - Surrogate behaviour and calibration: compare LLAMBO's discriminative surrogate to GP- and RF-based baselines (GP, SMAC) in terms of predictive accuracy (NRMSE, R², regret) and uncertainty quality (LPD, coverage, sharpness).
> - Role of textual context: assess the contribution of problem descriptions and hyperparameter-name embeddings via ablations that remove these signals, and measure the impact on prediction error and calibration.
> - Candidate generation quality: evaluate the LLAMBO candidate sampler against TPE (independent and multivariate) and random sampling using regret-based metrics and diversity measures (generalized variance, log-likelihood).

> [!note] 翻譯
> 本研究旨在重現 LLAMBO 論文 [1] 所報告的主要發現。該論文檢驗當一切皆以自然語言表達時，大型語言模型能否取代貝葉斯最佳化迴圈的關鍵組件。LLAMBO 主張，以資料集與模型的中介資料加上過去的評估結果作為提示，LLM 即可為搜尋進行暖啟動、提出新的超參數候選點，並在不擬合傳統代理模型的情況下估計其表現。
>
> 我們的重現聚焦於以下面向：
> - 暖啟動效率：驗證情境式、基於文字的暖啟動相較於標準空間填充設計（Random、Sobol、拉丁超立方 Latin Hypercube）能達到更低的早期遺憾值與更小的變異。
> - 代理模型行為與校準：就預測準確度（NRMSE、R²、遺憾值）與不確定性品質（對數預測密度 LPD、覆蓋率 coverage、銳度 sharpness），將 LLAMBO 的判別式代理模型與基於 GP 及隨機森林的基線（GP、SMAC）比較。
> - 文字脈絡的作用：透過移除問題描述與超參數名稱嵌入的消融實驗，評估這些訊號的貢獻，並衡量其對預測誤差與校準的影響。
> - 候選點生成品質：以基於遺憾值的指標與多樣性度量（廣義變異數 generalized variance、對數概似 log-likelihood），將 LLAMBO 候選點取樣器與 TPE（獨立邊際與多變量）及隨機取樣比較。

---

> [!quote] Original
> We build directly on the original LLAMBO codebase, replacing all OpenAI API calls with local ollama [2] invocations so that the full pipeline can be run with open-weight models. The implementation is organised primarily through nested bash scripts, with prompts and configuration spread across multiple files, and the repository does not provide utilities for aggregating results or plotting figures. To support this study, we implemented our own evaluation and plotting scripts for Bayesmark and HPOBench, standardised the LLM outputs into a common JSON format, and reimplemented all baselines (GP, SMAC, TPE, Random) using established libraries in a unified evaluation pipeline. In addition to the original GPT-3.5 setting, we test LLAMBO with Llama 3.1 70B as the main backbone and briefly probe smaller open models, allowing us to evaluate both the original claims and the method's robustness to changes in the underlying language model.

> [!note] 翻譯
> 我們直接在原始 LLAMBO 程式碼庫的基礎上開發，將所有 OpenAI API 呼叫替換為本地的 ollama [2] 呼叫，使完整流程能以開放權重模型執行。原實作主要以巢狀 bash 腳本組織，提示與設定分散於多個檔案，且程式碼庫並未提供彙整結果或繪圖的工具。為支援本研究，我們自行實作了 Bayesmark 與 HPOBench 的評估與繪圖腳本，將 LLM 輸出標準化為統一的 JSON 格式，並使用成熟的函式庫在統一的評估流程中重新實作所有基線（GP、SMAC、TPE、Random）。除原始的 GPT-3.5 設定外，我們以 Llama 3.1 70B 作為主要骨幹測試 LLAMBO，並簡要測試較小的開放模型，從而得以同時評估原始主張以及該方法對底層語言模型變動的穩健性。

---

## Methodology | 方法

### LLAMBO Architecture | LLAMBO 架構

> [!quote] Original
> LLAMBO replaces the usual components of Bayesian Optimization—surrogate models and acquisition functions—with a large language model that operates entirely through structured prompts. Instead of fitting a GP or a Random Forest and optimizing an acquisition function, the system repeatedly asks the LLM to propose hyperparameters, reason about previously observed trials, and estimate the performance of new candidates. Figure 1 illustrates the full loop.
>
> At each step, LLAMBO interacts with Bayesmark in a closed loop: the LLM proposes a configuration, Bayesmark evaluates it, and the result is inserted into the next prompt. The prompting pipeline is built around three stages:
> - Zero-shot warmstarting
> - Candidate generation
> - Surrogate-style performance estimation
>
> All prompts share two core pieces of information:
> - Data Card — dataset metadata (feature dimensionality, feature types, task type, etc.)
> - Model Card — the hyperparameter search space for the current model class
>
> These cards ensure the LLM always has access to the problem context. As new trials are evaluated, their scores and hyperparameters are appended to the prompt. The LLM is instructed to return results in a fixed JSON-like format so that outputs can be parsed directly into Bayesmark.
>
> [Figure 1: Overview of the LLAMBO prompting pipeline — 資料卡（Data Card）、模型卡（Model Card）、既有觀測與任務指令組成結構化提示，引導 LLM 進行 (0) 零樣本暖啟動、(1) 候選點生成與 (2) 基於代理模型的效能估計；每個提出的配置經目標函數評估後加入歷史並回饋至後續提示，純以自然語言互動迭代精煉搜尋策略。]

> [!note] 翻譯
> LLAMBO 以一個完全透過結構化提示運作的大型語言模型，取代貝葉斯最佳化的常見組件——代理模型與擷取函數（acquisition functions）。系統不再擬合 GP 或隨機森林並最佳化擷取函數，而是反覆要求 LLM 提出超參數、就先前觀測到的試驗進行推理，並估計新候選點的表現。Figure 1 展示了完整迴圈。
>
> 在每一步，LLAMBO 與 Bayesmark 形成閉環互動：LLM 提出一個配置，Bayesmark 進行評估，結果再被插入下一輪提示。提示流程圍繞三個階段構建：
> - 零樣本暖啟動（zero-shot warmstarting）
> - 候選點生成（candidate generation）
> - 代理模型式效能估計（surrogate-style performance estimation）
>
> 所有提示共享兩項核心資訊：
> - 資料卡（Data Card）——資料集中介資料（特徵維度、特徵類型、任務類型等）
> - 模型卡（Model Card）——當前模型類別的超參數搜尋空間
>
> 這兩張卡確保 LLM 始終能取得問題脈絡。隨著新試驗被評估，其分數與超參數會被附加到提示中。LLM 被指示以固定的類 JSON 格式回傳結果，使輸出可直接解析並輸入 Bayesmark。
>
> [Figure 1：LLAMBO 提示流程概覽——資料卡、模型卡、既有觀測與任務指令組成結構化提示，引導 LLM 進行 (0) 零樣本暖啟動、(1) 候選點生成與 (2) 基於代理模型的效能估計；每個提出的配置經目標函數評估後加入歷史並回饋至後續提示，純以自然語言互動迭代精煉搜尋策略。]

---

> [!quote] Original
> **Zero-Shot Warmstarting.** In the first stage, LLAMBO asks the LLM to provide an initial hyperparameter configuration without seeing any previous evaluations [3]. The prompt contains only the Data Card, the Model Card, and a short instruction describing the goal. Based solely on this information, the LLM proposes a starting point. This acts as a drop-in replacement for the random, Sobol, or Latin Hypercube initializations used in standard BO, but relies entirely on the LLM's interpretation of the dataset and model description rather than a fitted surrogate.
>
> **Candidate Generation.** After the first evaluation, LLAMBO enters the iterative candidate-generation loop. At each iteration, the LLM is prompted with the dataset and model information together with all previously observed (hyperparameter, performance) pairs. These observations are supplied in a simple text format. The LLM is then asked to suggest new configurations that might improve performance. Unlike classical BO, no explicit acquisition function is provided—the LLM decides which regions of the search space to explore or exploit based on the textual history.
>
> **Surrogate-Based Performance Estimation.** In the final stage, the LLM is asked to estimate the performance of a candidate before Bayesmark evaluates it. The prompt again contains the dataset metadata, model details, and the full evaluation history. The LLM outputs a numerical score representing its expectation of how the candidate will perform. In a conventional BO loop, this role is played by a trained surrogate (e.g., GP or Random Forest). Here, the LLM provides these predictions directly through pattern recognition in the textual description of the task and the observed history. LLAMBO then uses these estimated scores to rank candidates and select which one to evaluate next.

> [!note] 翻譯
> **零樣本暖啟動。** 在第一階段，LLAMBO 要求 LLM 在未見任何先前評估的情況下提供初始超參數配置 [3]。提示僅包含資料卡、模型卡與一段描述目標的簡短指令。僅憑這些資訊，LLM 便提出一個起始點。這可作為標準 BO 中隨機、Sobol 或拉丁超立方初始化的直接替代（drop-in replacement），但完全仰賴 LLM 對資料集與模型描述的詮釋，而非擬合出的代理模型。
>
> **候選點生成。** 首次評估之後，LLAMBO 進入迭代的候選點生成迴圈。在每次迭代中，LLM 的提示包含資料集與模型資訊，以及所有先前觀測到的（超參數, 效能）配對；這些觀測以簡單的文字格式提供。接著要求 LLM 建議可能改善效能的新配置。與經典 BO 不同，此處不提供顯式的擷取函數——LLM 依據文字化的歷史自行決定要探索或利用搜尋空間的哪些區域。
>
> **基於代理模型的效能估計。** 在最後階段，LLM 被要求在 Bayesmark 評估之前先估計候選點的表現。提示同樣包含資料集中介資料、模型細節與完整的評估歷史。LLM 輸出一個數值分數，代表其對該候選點表現的預期。在傳統 BO 迴圈中，此角色由訓練好的代理模型（如 GP 或隨機森林）扮演；在此，LLM 透過對任務文字描述與觀測歷史的模式辨識，直接提供這些預測。LLAMBO 隨後利用這些估計分數對候選點排序，並選出下一個要評估的對象。

---

### Datasets | 資料集

> [!quote] Original
> Our experiments follow the original LLAMBO setup and use the five tabular datasets included in the Bayesmark hyperparameter optimization benchmark. These datasets form the basis of the Data Card component shown in Figure 1. Each dataset is provided in a standardized format that includes the feature matrix X, labels y, train–test splits, and metadata describing feature types, dimensionality, and task specification. This structure allows the dataset information to be inserted directly into the Data Card for every prompt, ensuring that the LLM always has access to the relevant problem context.
>
> The five datasets cover a range of supervised learning problems. The Breast Cancer dataset is a binary classification task with 569 samples and 30 continuous features, making it suitable for evaluating LLAMBO's warmstarting behaviour. The Diabetes dataset provides a regression problem with 442 instances and 10 clinical predictors, which is challenging for early surrogate estimation. The Digits dataset contains 1,797 samples of handwritten digits represented by 64 pixel-intensity features and tests LLAMBO's ability to navigate a higher-dimensional multiclass setting. The Iris dataset is small and low-dimensional (150 samples, four botanical measurements), providing a scenario where the model must rely more heavily on prior knowledge. Finally, the Wine dataset contains 178 instances and 13 chemical attributes and serves as a moderately sized multiclass benchmark. Combined with the five Bayesmark model classes (Random Forest, AdaBoost, SVM, Logistic Regression, and a simple neural network), these datasets define the 25 optimization tasks reproduced in our study.

> [!note] 翻譯
> 我們的實驗遵循原始 LLAMBO 設定，使用 Bayesmark 超參數最佳化基準所含的五個表格式資料集。這些資料集構成 Figure 1 中資料卡組件的基礎。每個資料集皆以標準化格式提供，包含特徵矩陣 X、標籤 y、訓練—測試切分，以及描述特徵類型、維度與任務規格的中介資料。此結構使資料集資訊得以直接插入每個提示的資料卡，確保 LLM 始終能取得相關的問題脈絡。
>
> 這五個資料集涵蓋一系列監督式學習問題。Breast Cancer 資料集為二元分類任務，含 569 個樣本與 30 個連續特徵，適合評估 LLAMBO 的暖啟動行為。Diabetes 資料集提供一個含 442 筆實例與 10 個臨床預測變數的迴歸問題，對早期代理模型估計具有挑戰性。Digits 資料集包含 1,797 個以 64 個像素強度特徵表示的手寫數字樣本，用以檢驗 LLAMBO 在較高維多類別情境中的導航能力。Iris 資料集小而低維（150 個樣本、四個植物學量測），提供了模型必須更加仰賴先驗知識的情境。最後，Wine 資料集含 178 筆實例與 13 個化學屬性，作為中等規模的多類別基準。與五個 Bayesmark 模型類別（Random Forest、AdaBoost、SVM、邏輯迴歸與簡單神經網路）相組合，這些資料集定義了本研究所重現的 25 個最佳化任務。

---

> [!quote] Original
> In the LLAMBO pipeline, each dataset enters the optimization loop through the Data Card and conditions all three components of the hypothesis generator. During zero-shot warmstarting, the Data Card is paired with the Model Card and instructions that guide the LLM to propose an initial hyperparameter configuration based on dataset characteristics such as dimensionality, class structure, and feature types. As the optimization progresses, the same dataset description is used in the candidate-generation prompt, where the LLM receives a history of evaluated configurations ("performance: sᵢ, hyperparameters: hᵢ") and reasons about which regions of the search space may be promising. In the surrogate-estimation stage, the LLM again leverages the dataset metadata to predict how new candidates might perform, interpreting interactions between hyperparameters and dataset structure. For consistent evaluation across all methods, LLAMBO also uses Bayesmark's precomputed performance statistics to compute regret in a standardized way.

> [!note] 翻譯
> 在 LLAMBO 流程中，每個資料集經由資料卡進入最佳化迴圈，並制約假說生成器的全部三個組件。在零樣本暖啟動階段，資料卡與模型卡及指令搭配，引導 LLM 依據資料集特性（如維度、類別結構與特徵類型）提出初始超參數配置。隨最佳化推進，相同的資料集描述被用於候選點生成提示：LLM 接收已評估配置的歷史（「performance: sᵢ, hyperparameters: hᵢ」），並推理搜尋空間中哪些區域可能有潛力。在代理模型估計階段，LLM 再次利用資料集中介資料預測新候選點的可能表現，詮釋超參數與資料集結構之間的交互作用。為使所有方法的評估一致，LLAMBO 亦使用 Bayesmark 預先計算的效能統計量，以標準化的方式計算遺憾值。

---

### Baseline Optimizers | 基線最佳化器

> [!quote] Original
> To evaluate LLAMBO in a full end-to-end hyperparameter optimization (HPO) setting, the original paper benchmarks the method against four widely used and methodologically diverse baseline optimizers. These baselines represent the dominant approaches in modern surrogate-based Bayesian Optimization, including classical Gaussian Processes (GPs), neural-augmented GPs, density-estimation methods, and random-forest surrogates. All baselines are run under identical conditions to ensure a fair comparison.
>
> **GP-DKL (Deep Kernel Learning Gaussian Process).** GP-DKL is an advanced Gaussian Process model implemented in BoTorch. It combines a deep neural network with a GP kernel: the network learns a nonlinear feature embedding, and the GP operates on this learned space [4]. This hybrid surrogate retains calibrated uncertainty while offering greater flexibility in medium- and high-dimensional search spaces. Because of its strong modeling capacity, GP-DKL is considered one of the most powerful GP-based baselines in modern Bayesian Optimization pipelines.
>
> **SKOpt (Gaussian Process from Scikit-Optimize).** It provides a classical implementation of Gaussian Process Bayesian Optimization [5]. It uses stationary kernels (typically Matern 5/2) and standard acquisition functions such as Expected Improvement or Lower Confidence Bound. This baseline is known for its stability, interpretability, and reliable performance on low-dimensional and smooth objective landscapes, making it a canonical reference point in HPO studies.

> [!note] 翻譯
> 為了在完整的端到端超參數最佳化（HPO）情境中評估 LLAMBO，原論文將該方法與四個廣泛使用、方法學上多樣的基線最佳化器進行基準比較。這些基線代表了現代基於代理模型的貝葉斯最佳化的主流途徑，包括經典高斯過程（GP）、神經增強 GP、密度估計方法與隨機森林代理模型。所有基線皆在相同條件下執行，以確保比較公平。
>
> **GP-DKL（深度核學習高斯過程，Deep Kernel Learning Gaussian Process）。** GP-DKL 是在 BoTorch 中實作的先進高斯過程模型。它將深度神經網路與 GP 核結合：網路學習非線性特徵嵌入，GP 則在此學得的空間上運作 [4]。這種混合式代理模型保留了經校準的不確定性，同時在中高維搜尋空間中提供更大的彈性。憑藉其強大的建模能力，GP-DKL 被視為現代貝葉斯最佳化流程中最強大的 GP 類基線之一。
>
> **SKOpt（Scikit-Optimize 的高斯過程）。** 其提供高斯過程貝葉斯最佳化的經典實作 [5]，使用平穩核（通常為 Matern 5/2）與標準擷取函數，如期望改進量（Expected Improvement）或下信賴界（Lower Confidence Bound）。此基線以其穩定性、可解釋性，以及在低維且平滑目標地形上的可靠表現著稱，是 HPO 研究中的典型參考點。

---

> [!quote] Original
> **Optuna (Tree-structured Parzen Estimator).** Optuna's default optimizer implements the Tree-structured Parzen Estimator (TPE), a non-parametric density-based approach to Bayesian Optimization. Rather than modeling the objective function directly, TPE models two conditional densities: one over high-performing configurations and one over low-performing ones. New candidates are drawn from regions where the ratio of these densities is favorable. This method scales well, handles categorical and conditional search spaces naturally, and is one of the most widely used HPO algorithms in practical machine learning systems [6].
>
> **SMAC3 (Random-Forest Surrogate).** SMAC3 uses a Random Forest regression model as its surrogate, with uncertainty estimated from tree-wise variance. This makes the method robust to non-smooth, noisy, and heterogeneous response surfaces. SMAC has a long history of strong performance in AutoML, and excels especially in hierarchical or irregular search spaces where GP-based methods may struggle [7].
>
> **Evaluation Setup.** All optimizers, including LLAMBO, are evaluated under the same conditions:
> - 5 randomly sampled initial points
> - 25 optimization trials after initialization
> - 5 independent runs per task to reduce variance
>
> The experiments cover a total of 30 tasks:
> - 25 Bayesmark tasks (five datasets crossed with five model classes),
> - 3 private datasets not seen during LLM pretraining,
> - 2 synthetic datasets designed to probe behavior on controlled objective landscapes.
>
> This unified evaluation protocol ensures that differences in performance reflect the behavior of the optimization strategies themselves rather than differences in initialization or experimental configuration.

> [!note] 翻譯
> **Optuna（樹狀結構 Parzen 估計器，Tree-structured Parzen Estimator）。** Optuna 的預設最佳化器實作 TPE——一種非參數、基於密度的貝葉斯最佳化方法。TPE 不直接對目標函數建模，而是建模兩個條件密度：一個對應高表現配置，另一個對應低表現配置；新候選點從兩密度比值有利的區域抽取。此方法擴展性佳，能自然處理類別型與條件式搜尋空間，是實務機器學習系統中最廣泛使用的 HPO 演算法之一 [6]。
>
> **SMAC3（隨機森林代理模型）。** SMAC3 以隨機森林迴歸模型作為代理模型，並以各樹間變異數估計不確定性。這使該方法對非平滑、含雜訊且異質的反應曲面具穩健性。SMAC 在 AutoML 領域長期表現優異，尤其擅長 GP 類方法可能吃力的階層式或不規則搜尋空間 [7]。
>
> **評估設定。** 所有最佳化器（包括 LLAMBO）皆在相同條件下評估：
> - 5 個隨機抽取的初始點
> - 初始化後 25 次最佳化試驗
> - 每個任務 5 次獨立執行以降低變異
>
> 實驗共涵蓋 30 個任務：
> - 25 個 Bayesmark 任務（五個資料集 × 五個模型類別），
> - 3 個 LLM 預訓練期間未見過的私有資料集，
> - 2 個為在受控目標地形上探測行為而設計的合成資料集。
>
> 此統一評估協定確保效能差異反映的是最佳化策略本身的行為，而非初始化或實驗設定的差異。

---

## Results | 結果

> [!quote] Original
> In this section, we present a comprehensive reproduction of the LLAMBO framework using the Llama 3.1 70B model as the backbone for all textual encodings. Our goal was to validate the core claims of the original paper regarding warmstarting efficiency, surrogate modeling behavior, the role of contextual embeddings, and candidate point sampling. The reproduced results consistently align with the reported trends: contextual information embedded via the Llama 3.1 70B encoder provides a strong prior that substantially accelerates optimization, improves cross-task structure, and enables high-quality candidate generation. Across Figures, we observe the same characteristic signature of LLAMBO—effective warmstarts, meta-learned structure in surrogate predictions, clear degradation when textual information is removed, and state-of-the-art candidate quality—demonstrating that LLAMBO's behavior is robust even under a different, larger language model backbone.

> [!note] 翻譯
> 本節呈現以 Llama 3.1 70B 模型作為所有文字編碼骨幹、對 LLAMBO 框架的全面重現。我們的目標是驗證原論文有關暖啟動效率、代理模型行為、情境嵌入之作用與候選點取樣的核心主張。重現結果與原報告趨勢一致：經 Llama 3.1 70B 編碼器嵌入的情境資訊提供了強力先驗，顯著加速最佳化、改善跨任務結構，並促成高品質的候選點生成。在各圖中，我們觀察到 LLAMBO 相同的特徵標誌——有效的暖啟動、代理模型預測中的元學習結構、移除文字資訊後的明顯劣化，以及最先進的候選點品質——顯示即使換用不同且更大的語言模型骨幹，LLAMBO 的行為依然穩健。

---

### Baseline Optimizers | 基線最佳化器結果

> [!quote] Original
> Before showing the reproduced LLAMBO results, we made sure that our baseline implementation was completely in line with the setup described in [1]. We followed the original baseline protocol exactly to make sure that the methods were the same. This verification step showed that our implementation works the same way as the baselines that were published and the results from a parallel reproduction.
>
> After confirming equivalence, we also implemented a per-task min-max normalization scheme. Bayesmark's global scaling sets limits on all datasets, so regret curves never reach zero. In contrast, per-task normalization changes the performance of each task based on its observed minimum and maximum. This creates regret curves that stop at zero once the best configuration has been seen. This shows bigger differences between optimizers at the beginning while keeping the overall ranking the same. Importantly, we found that both normalization choices lead to the same qualitative ordering of the baselines.
>
> Figure 2 offers an additional analysis to the global normalization curves. The original evaluation employs fixed Bayesmark-wide score bounds; however, our supplementary per-task min-max scaling uncovers more pronounced early-regret disparities and generates regret curves that plateau at zero upon reaching the task-specific optimum. This demonstrates that they are stable under alternative normalization schemes. It also highlights the importance of considering normalization choices when comparing Bayesian optimization methods.
>
> [Figure 2: Baseline Regret on Bayesmark Public Tasks and Private + Synthetic Tasks — 比較 SKOpt (GP)、GP-DKL (BoTorch)、Optuna 與 SMAC 之正規化平均遺憾值隨試驗次數的變化。]

> [!note] 翻譯
> 在展示重現的 LLAMBO 結果之前，我們先確認基線實作與 [1] 所述設定完全一致。我們嚴格遵循原始基線協定，以確保方法相同。此驗證步驟顯示，我們的實作與已發表的基線及一項平行重現的結果表現一致。
>
> 確認等價性之後，我們另外實作了逐任務 min-max 正規化方案。Bayesmark 的全域縮放對所有資料集設定固定上下限，因此遺憾值曲線永遠不會到達零；相對地，逐任務正規化依各任務觀測到的最小值與最大值調整其表現，使遺憾值曲線在最佳配置出現後即停在零。這在保持整體排名不變的同時，凸顯了最佳化器在初期的更大差異。重要的是，我們發現兩種正規化選擇對基線得出相同的定性排序。
>
> Figure 2 提供了對全域正規化曲線的補充分析。原始評估採用固定的 Bayesmark 全域分數界限；而我們補充的逐任務 min-max 縮放揭示了更顯著的早期遺憾值差異，並產生在達到任務特定最優後即趨平於零的遺憾值曲線。這證明結果在不同的正規化方案下皆穩定，也突顯了在比較貝葉斯最佳化方法時考慮正規化選擇的重要性。
>
> [Figure 2：Bayesmark 公開任務與私有＋合成任務上的基線遺憾值——比較 SKOpt (GP)、GP-DKL (BoTorch)、Optuna 與 SMAC 之正規化平均遺憾值隨試驗次數的變化。]

---

### Warmstarting Strategies in Bayesian Optimization | 貝葉斯最佳化中的暖啟動策略

> [!quote] Original
> Figures 3–5 report the reproduced comparison of warmstarting strategies in Bayesian Optimization using the LLAMBO framework with a Llama 3.1 70B text encoder. We compare classical space-filling initialization schemes (Random, Sobol, Latin Hypercube) against contextual warmstarts that leverage problem descriptions and hyperparameter semantics (No Context, Partial Context, Full Context).
>
> Figure 3 shows the average simple regret over optimization trials. The reproduced curves match the qualitative behavior of the original work: classical designs (Random, Sobol, LHCube) yield consistently higher regret, especially in the early stages, indicating weaker priors and slower convergence towards the task optimum. In contrast, contextual warmstarts exhibit uniformly lower regret throughout the optimization horizon, with the Full Context variant performing best. The shaded regions further indicate reduced variability across runs for contextual methods, confirming a more stable and reliable optimization trajectory.

> [!note] 翻譯
> Figures 3–5 報告了以 Llama 3.1 70B 文字編碼器搭配 LLAMBO 框架、對貝葉斯最佳化暖啟動策略的重現比較。我們將經典的空間填充初始化方案（Random、Sobol、拉丁超立方）與利用問題描述及超參數語意的情境式暖啟動（No Context、Partial Context、Full Context）進行比較。
>
> Figure 3 顯示最佳化試驗過程中的平均簡單遺憾值（simple regret）。重現曲線與原始工作的定性行為吻合：經典設計（Random、Sobol、LHCube）的遺憾值持續較高，尤其在早期階段，顯示其先驗較弱、向任務最優收斂較慢。相對地，情境式暖啟動在整個最佳化區間內呈現一致較低的遺憾值，其中 Full Context 變體表現最佳。陰影區域進一步顯示情境式方法跨執行的變異較小，證實其最佳化軌跡更穩定可靠。

---

> [!quote] Original
> To better understand how these warmstarts populate the search space, Figure 4 visualizes the pairwise correlation structure of the initial designs for a representative task (breast, RF). Random sampling yields the lowest average absolute correlation, consistent with nearly independent draws. In contrast, contextual warmstarts induce more structured correlation patterns between hyperparameters, reflecting task-specific priors encoded by the language model rather than purely independent sampling.
>
> Figure 5 complements this view by quantifying the overall diversity of initial designs via the generalized variance of the normalized hyperparameters. Latin Hypercube sampling achieves the highest diversity, as expected from a stratified design. However, contextual warmstarts attain diversity levels comparable to LHCube and substantially higher than Random and Sobol, while still encoding meaningful structure. This indicates that contextual initialization is not merely collapsing onto a narrow region of the search space, but instead produces diverse yet semantically informed starting points.
>
> Overall, this reproduction confirms that warmstarting with contextual embeddings substantially improves sample efficiency and early-regret performance in Bayesian Optimization, while maintaining high diversity in the initial design and introducing task-aware structure into the explored hyperparameter space.
>
> [Figure 4: Correlation structure of initial designs. / Figure 5: Diversity of initial designs (generalized variance; higher is more diverse).]

> [!note] 翻譯
> 為更好地理解這些暖啟動如何佈點於搜尋空間，Figure 4 將一個代表性任務（breast, RF）之初始設計的成對相關結構可視化。隨機取樣的平均絕對相關最低，符合近乎獨立抽樣的預期；相對地，情境式暖啟動在超參數之間誘發更具結構的相關模式，反映語言模型所編碼的任務特定先驗，而非純粹的獨立取樣。
>
> Figure 5 以正規化超參數的廣義變異數量化初始設計的整體多樣性，補足上述觀察。拉丁超立方取樣如預期（分層設計）達到最高多樣性；然而，情境式暖啟動達到與 LHCube 相當、且顯著高於 Random 與 Sobol 的多樣性水準，同時仍編碼了有意義的結構。這顯示情境式初始化並非僅僅塌縮至搜尋空間中的狹窄區域，而是產生多樣且富含語意訊息的起始點。
>
> 整體而言，此重現證實：以情境嵌入進行暖啟動可顯著改善貝葉斯最佳化的樣本效率與早期遺憾值表現，同時在初始設計中保持高多樣性，並將任務感知的結構引入所探索的超參數空間。
>
> [Figure 4：初始設計的相關結構。／Figure 5：初始設計的多樣性（廣義變異數；越高越多樣）。]

---

### Discriminative Surrogate Models Evaluation | 判別式代理模型評估

> [!quote] Original
> Figure 6 presents the reproduced evaluation of the discriminative surrogate models under varying numbers of observed data points. The metrics follow the original LLAMBO study and assess both predictive accuracy (NRMSE, R², regret) and uncertainty quality (LPD, coverage, sharpness). The reproduced results show the same characteristic pattern: SMAC achieves the strongest pure regression performance with the lowest NRMSE and highest R², while Gaussian Processes remain the best calibrated, achieving near-ideal coverage and stable sharpness. In contrast, LLAMBO and its Monte Carlo variant exhibit weaker single-task regression performance at low data regimes and systematically under-estimated uncertainty, reflected in higher LPD, low coverage, and overly sharp predictive intervals. However, both LLAMBO variants improve steadily as more observations become available. These results confirm that LLAMBO is not optimized to serve as a standalone surrogate but instead derives its advantage from cross-task contextualization and meta-learned priors rather than raw single-task predictive accuracy.
>
> [Figure 6: Discriminative Surrogate models.]

> [!note] 翻譯
> Figure 6 呈現在不同觀測資料點數下、對判別式代理模型的重現評估。指標沿用原始 LLAMBO 研究，同時衡量預測準確度（NRMSE、R²、遺憾值）與不確定性品質（LPD、覆蓋率、銳度）。重現結果顯示相同的特徵模式：SMAC 以最低的 NRMSE 與最高的 R² 取得最強的純迴歸表現；高斯過程仍是校準最佳者，達到近乎理想的覆蓋率與穩定的銳度。相對地，LLAMBO 及其蒙地卡羅（Monte Carlo）變體在低資料量情況下的單一任務迴歸表現較弱，且系統性地低估不確定性——反映為較高的 LPD、偏低的覆蓋率與過度銳利的預測區間。然而，隨著可用觀測增加，兩個 LLAMBO 變體均穩定改善。這些結果證實 LLAMBO 並非被最佳化為獨立的代理模型，其優勢來自跨任務情境化與元學習先驗，而非原始的單一任務預測準確度。
>
> [Figure 6：判別式代理模型。]

---

### Ablation Study | 消融實驗

> [!quote] Original
> Figure 7 presents the ablation study comparing the full LLAMBO model with an uninformed variant that excludes both the problem description and hyperparameter-name embeddings. The reproduced results clearly show that removing textual context significantly degrades surrogate quality across all data regimes. In terms of predictive accuracy, the uninformed model exhibits consistently higher NRMSE, with the gap largest when only a small number of observations are available, indicating impaired generalization and weaker inductive bias. The same trend appears in the uncertainty metrics: the full LLAMBO model achieves substantially lower LPD, while the uninformed surrogate produces poorly calibrated and overconfident predictions. As the number of observations increases, both models improve, but the full LLAMBO surrogate retains a clear advantage. This ablation confirms that textual context encoded via the Llama 3.1 70B backbone is essential for LLAMBO's performance, providing semantic structure that enables effective few-shot surrogate modeling.
>
> [Figure 7: Ablation study.]

> [!note] 翻譯
> Figure 7 呈現消融實驗，比較完整的 LLAMBO 模型與一個同時排除問題描述及超參數名稱嵌入的「無資訊」（uninformed）變體。重現結果清楚顯示，移除文字脈絡會在所有資料量情況下顯著劣化代理模型品質。就預測準確度而言，無資訊模型的 NRMSE 持續較高，且在僅有少量觀測時差距最大，顯示其泛化能力受損、歸納偏置（inductive bias）較弱。不確定性指標亦呈現相同趨勢：完整的 LLAMBO 模型達到明顯較低的 LPD，而無資訊代理模型則產生校準不佳且過度自信的預測。隨觀測數增加，兩個模型皆有改善，但完整的 LLAMBO 代理模型仍保有明顯優勢。此消融實驗證實：經 Llama 3.1 70B 骨幹編碼的文字脈絡對 LLAMBO 的表現至關重要，其提供的語意結構使有效的少樣本代理建模成為可能。
>
> [Figure 7：消融實驗。]

---

### Candidate Point Sampling | 候選點取樣

> [!quote] Original
> Figure 8 shows the reproduced comparison of candidate generation strategies used during acquisition optimization.
>
> We evaluate four methods: Random sampling, TPE with independent marginals, multivariate TPE, and the LLAMBO candidate sampler. The reproduced results closely follow the original findings. LLAMBO consistently achieves the lowest average regret across candidate sets, indicating that it proposes high-quality points aligned with promising regions of the search space. In terms of best-regret performance, all methods eventually identify strong candidates, but LLAMBO reaches low-regret solutions earlier and with lower variance. The generalized variance results demonstrate that LLAMBO maintains a balanced level of diversity—avoiding the collapse observed in TPE-based methods while remaining more targeted than random sampling. Finally, LLAMBO achieves the highest log-likelihood under the surrogate density, confirming that its candidates are well-aligned with the learned model structure. Overall, this reproduction validates that LLAMBO provides superior candidate generation, combining quality, diversity, and surrogate-consistency more effectively than existing sampling strategies.
>
> [Figure 8: Candidate Point Sampling.]

> [!note] 翻譯
> Figure 8 顯示對擷取最佳化過程中所用候選點生成策略的重現比較。
>
> 我們評估四種方法：隨機取樣、獨立邊際的 TPE、多變量 TPE，以及 LLAMBO 候選點取樣器。重現結果與原始發現高度吻合。LLAMBO 在各候選集合中持續達到最低的平均遺憾值，顯示其提出的高品質候選點與搜尋空間中的有望區域相符。就最佳遺憾值表現而言，所有方法最終都能找到強力候選點，但 LLAMBO 更早達到低遺憾解、且變異更低。廣義變異數結果顯示 LLAMBO 維持了均衡的多樣性水準——既避免了 TPE 類方法出現的塌縮，又比隨機取樣更具針對性。最後，LLAMBO 在代理密度下達到最高的對數概似，證實其候選點與所學模型結構高度一致。整體而言，此重現驗證了 LLAMBO 提供更優越的候選點生成，比既有取樣策略更有效地兼顧品質、多樣性與代理模型一致性。
>
> [Figure 8：候選點取樣。]

---

## Discussion | 討論

> [!quote] Original
> Our reproduction confirms that the main qualitative claims of LLAMBO hold when GPT-3.5 is replaced by a strong open-weight model. Using Llama 3.1 70B as the backbone, we recover the reported benefits of contextual warmstarting: early-regret is consistently lower, variance across runs is reduced, and the optimization trajectories are more stable than for classical space-filling designs. At the same time, we observe that LLAMBO's discriminative surrogate is not the strongest single-task regressor—Gaussian Processes and SMAC typically achieve lower NRMSE and better-calibrated uncertainty when trained in isolation. LLAMBO's advantage instead arises from cross-task semantic priors encoded in the text representations of problem descriptions and hyperparameter names, which improve sample efficiency once contextual information is available.
>
> The ablation study further supports this interpretation. Removing textual context (problem descriptions and hyperparameter semantics) significantly degrades predictive accuracy and calibration, and weakens the warmstarting effect. Nevertheless, the LLAMBO candidate sampler still produces high-quality proposals: across benchmarks, its suggestions are more diverse and better aligned with the underlying objective than those produced by TPE or random sampling, even when the surrogate is not numerically optimal on a single task.

> [!note] 翻譯
> 我們的重現證實：當 GPT-3.5 被替換為強大的開放權重模型時，LLAMBO 的主要定性主張依然成立。以 Llama 3.1 70B 作為骨幹，我們重現了原報告中情境式暖啟動的效益：早期遺憾值持續較低、跨執行變異降低，且最佳化軌跡比經典空間填充設計更穩定。與此同時，我們觀察到 LLAMBO 的判別式代理模型並非最強的單一任務迴歸器——單獨訓練時，高斯過程與 SMAC 通常能達到更低的 NRMSE 與校準更佳的不確定性。LLAMBO 的優勢反而來自編碼於問題描述與超參數名稱文字表徵中的跨任務語意先驗，一旦情境資訊可用，便能改善樣本效率。
>
> 消融實驗進一步支持此詮釋。移除文字脈絡（問題描述與超參數語意）會顯著劣化預測準確度與校準，並削弱暖啟動效果。儘管如此，LLAMBO 候選點取樣器仍能產生高品質的提案：在各基準中，即使代理模型在單一任務上數值並非最優，其建議仍比 TPE 或隨機取樣所產生者更多樣、且與底層目標更為契合。

---

> [!quote] Original
> In contrast, our attempts to run the full LLAMBO pipeline with smaller or weaker language models were not successful. Gemma 27B and Llama 3.1 8B, evaluated under the same prompts and parsing logic, frequently returned malformed outputs (invalid JSON, missing hyperparameters) and surrogate scores that did not correlate with observed performance. This led to unstable optimization loops with constraint violations and highly inconsistent rankings of nearly identical candidates. These failures indicate that LLAMBO places non-trivial demands on the underlying LLM: reliable surrogate behaviour requires strong instruction-following, basic numerical reasoning, and reasonably calibrated scoring. In our setup, these properties only emerged robustly with the 70B model, suggesting that—at least with the current prompting scheme—LLAMBO is not yet robust to substantial reductions in model capacity.

> [!note] 翻譯
> 相對地，我們以更小或更弱的語言模型執行完整 LLAMBO 流程的嘗試並未成功。在相同提示與解析邏輯下評估的 Gemma 27B 與 Llama 3.1 8B，經常回傳格式錯誤的輸出（無效的 JSON、缺漏超參數），以及與觀測表現不相關的代理分數。這導致最佳化迴圈不穩定、出現約束違反，且對幾乎相同的候選點給出高度不一致的排名。這些失敗顯示 LLAMBO 對底層 LLM 有著不容小覷的要求：可靠的代理模型行為需要強大的指令遵循能力、基本的數值推理，以及合理校準的評分。在我們的設定中，這些性質僅在 70B 模型上穩健地展現，這表明——至少在目前的提示方案下——LLAMBO 尚無法在模型容量大幅縮減時保持穩健。

---

## References | 參考文獻

> References omitted / 參考文獻略
