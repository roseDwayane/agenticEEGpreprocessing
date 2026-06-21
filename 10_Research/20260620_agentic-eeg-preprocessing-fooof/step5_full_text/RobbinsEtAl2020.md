---
citation_key: "RobbinsEtAl2020"
title: "How Sensitive Are EEG Results to Preprocessing Methods: A Benchmarking Study"
year: 2020
access_level: "abstract-only"
source_url: "https://doi.org/10.1109/TNSRE.2020.2980223"
---

# How Sensitive Are EEG Results to Preprocessing Methods: A Benchmarking Study

## 摘要 / Abstract
> [!abstract] Original
> Although several guidelines for best practices in EEG preprocessing have been released, even studies that strictly adhere to those guidelines contain considerable variation in the ways that the recommended methods are applied. An open question for researchers is how sensitive the results of EEG analyses are to variations in preprocessing methods and parameters. To address this issue, we analyze the effect of preprocessing methods on downstream EEG analysis using several simple signal and event-related measures. These measures are evaluated on the results of processing a corpus of 17 EEG studies (performed at six experimental sites) using four automated preprocessing pipelines (denoted LARG, MARA, ASR_5*, and ASR_10*). Although the general structure of the results is similar across these preprocessing methods, there are significant differences, particularly in the low-frequency spectral features and in the residuals left by blinks. These results argue for detailed reporting of processing details and for using a federation of automated processing pipelines, with well-documented and standardized parameter choices, to quantify the effects of processing choices as part of reporting research.

> [!abstract] 繁體中文摘要
> 雖然已發布多項 EEG 前處理最佳實踐指南，即使嚴格遵循這些指南的研究，在如何套用建議方法上仍存在相當大的差異。研究者面臨的一個開放問題是：EEG 分析結果對前處理方法與參數的變動有多敏感？為探討此問題，我們以若干簡單的訊號與事件相關量測，分析前處理方法對下游 EEG 分析的影響。這些量測在「處理 17 個 EEG 研究語料（於六個實驗站點進行）」的結果上評估，使用四種自動化前處理管線（記為 LARG、MARA、ASR_5*、ASR_10*）。雖然各前處理方法的結果整體結構相似，但仍有顯著差異，尤其在低頻頻譜特徵與眨眼殘留上。這些結果主張應詳細報告處理細節，並使用「具完整文件記載且參數標準化的自動化處理管線聯盟」，將處理選擇的影響量化作為研究報告的一部分。

## Key Points / 重點筆記

> [!note] What problem it solves / 解決什麼問題
> EN: Even guideline-compliant EEG studies vary widely in how preprocessing is actually applied, and it is unclear how much these choices change downstream results. Robbins et al. quantify this sensitivity at scale, asking whether different automated pipelines produce meaningfully different signal/ERP measures on the same data.
> 中: 即使符合指南的 EEG 研究，在前處理實際套用方式上仍差異甚大，且這些選擇對下游結果的影響程度並不明確。Robbins 等人以大規模量化此敏感度，探討不同自動化管線是否會在相同資料上產生有意義差異的訊號/ERP 量測。

> [!note] Method / 方法
> EN: The corpus comprises EEG from 17 studies at six experimental sites — roughly 7.8 million event-related epochs from ~1,100 recordings across five laboratory-grade systems — falling into visual target-detection and lane-keeping (with distractions) categories. Four automated pipelines were compared: LARG and MARA (closely related ICA-based pipelines) and ASR_5* and ASR_10* (based on Artifact Subspace Reconstruction, a real-time-capable automated artifact remover). Several simple signal and event-related measures (including spectral features and blink-locked residuals) were computed on each pipeline's output and compared.
> 中: 語料包含六個實驗站點的 17 個研究 EEG—約 1,100 筆紀錄、來自五套實驗室等級系統、約 780 萬個事件相關 epoch—分為視覺目標偵測與（含干擾的）車道保持兩類。比較四種自動化管線：LARG 與 MARA（密切相關的 ICA 式管線）以及 ASR_5* 與 ASR_10*（基於 Artifact Subspace Reconstruction，一種可即時運作的自動假影移除）。在每種管線的輸出上計算若干簡單訊號與事件相關量測（含頻譜特徵與眨眼鎖定殘差）並加以比較。

> [!note] Key findings / 主要發現
> EN: The overall structure of results was similar across pipelines, but significant differences appeared particularly in low-frequency spectral features and in blink residuals — MARA and ASR-based methods showed systematic bias, removing too much signal around blink peaks while leaving residuals in surrounding intervals. A key practical finding: scaling each recording by a recording-specific constant (Huber mean working best) reduced cross-recording variability by roughly 40%, especially valuable when merging datasets. The authors conclude there is no single "best" pipeline but rather "a portfolio of good choices," and recommend running data through a *federation* of well-documented pipelines and comparing results.
> 中: 各管線的結果整體結構相似，但顯著差異主要出現在低頻頻譜特徵與眨眼殘差上—MARA 與 ASR 式方法呈現系統性偏誤，在眨眼峰附近移除過多訊號、卻在周邊區間留下殘差。一項關鍵實用發現：以每筆紀錄特定常數（以 Huber 平均效果最佳）縮放各紀錄，可將跨紀錄變異降低約 40%，在合併資料集時尤其有價值。作者結論認為沒有單一「最佳」管線，而是「一組良好選擇的組合」，並建議讓資料通過一個*聯盟*的完整記載管線並比較結果。

> [!tip] Relevance to "Agentic EEG preprocessing + FOOOF-SNR" project / 與本計畫的關聯
> EN: Robbins provides the empirical license for our adaptive, per-recording approach. (a) Action space: it benchmarks exactly the heavyweight actions our search must reason about — ICA-based (LARG/MARA) vs ASR-based (ASR_5/ASR_10) cleaning — and shows ASR aggressiveness (the cutoff "5" vs "10") materially changes output, so ASR cutoff should be a tunable parameter the LLM agent selects. (b) FOOOF-SNR metric: the finding that differences concentrate in *low-frequency spectral features* and blink residuals is directly relevant — low-frequency content is where the FOOOF aperiodic exponent/offset is fit, so pipeline choice will move our SNR metric there; this both validates a spectral metric's sensitivity and warns us to gate the low-frequency fit (cf. Gerster). The recording-specific scaling result motivates normalizing FOOOF-SNR per recording. (c) Greedy-search ground truth: "no single best pipeline, use a federation" is the empirical justification for *per-recording* greedy search and an LLM agent that picks among pipelines, rather than a global fixed pipeline — Robbins is essentially the manual, large-corpus version of what our agent automates and personalizes.
> 中: Robbins 為我們的自適應、逐筆紀錄方法提供實證許可。(a) 動作空間：它正好基準測試了我們搜尋必須推理的重量級動作—ICA 式（LARG/MARA）vs ASR 式（ASR_5/ASR_10）清理—並顯示 ASR 激進度（截止「5」vs「10」）會實質改變輸出，因此 ASR 截止值應為 LLM 代理可調的參數。(b) FOOOF-SNR 指標：差異集中於*低頻頻譜特徵*與眨眼殘差的發現直接相關—低頻正是 FOOOF 非週期指數/偏移擬合之處，故管線選擇會在該處移動我們的 SNR 指標；這既驗證了頻譜指標的敏感度，也警示我們須對低頻擬合設閘門（參見 Gerster）。逐紀錄縮放的結果則促使我們對 FOOOF-SNR 做逐紀錄正規化。(c) 貪婪搜尋真值：「沒有單一最佳管線，使用聯盟」正是*逐紀錄*貪婪搜尋與「於管線間挑選的 LLM 代理」的實證依據，而非全域固定管線—Robbins 本質上是我們代理所自動化與個人化之事的人工、大語料版本。

## Full Text / 全文
（僅摘要可得 / abstract-only — IEEE TNSRE paywalled and bioRxiv PDF blocked automated access. Built from the verbatim abstract plus the publicly summarized findings (IEEE Brain newsletter). Robbins, Touryan, Mullen, Kothe & Bigdely-Shamlo, IEEE Trans. Neural Syst. Rehabil. Eng. 2020; DOI 10.1109/TNSRE.2020.2980223. Code: github.com/VisLab/EEG-Pipelines.）

### Data corpus
- 17 EEG studies at 6 experimental sites; ~1,100 recordings; ~7.8 million event-related epochs; 5 laboratory-grade systems.
- Two task categories: visual target detection; lane-keeping with distractions/variations.

### Pipelines compared
- **LARG** and **MARA** — closely related ICA-based pipelines.
- **ASR_5\*** and **ASR_10\*** — Artifact Subspace Reconstruction with cutoff parameters 5 and 10 (real-time-capable).

### Measures & findings
- Simple signal and event-related measures (incl. spectral features, blink-locked residuals).
- Overall result structure similar across pipelines, but significant differences in low-frequency spectral features and blink residuals; MARA and ASR methods showed systematic bias around blink peaks.
- Recording-specific scaling (Huber mean) reduced cross-recording variability ~40%.
- Conclusion/recommendation: no single best pipeline; report processing details fully; use a *federation* of standardized pipelines and compare results.
