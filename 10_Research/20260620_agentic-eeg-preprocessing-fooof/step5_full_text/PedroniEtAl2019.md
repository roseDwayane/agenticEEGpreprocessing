---
citation_key: "PedroniEtAl2019"
title: "Automagic: Standardized preprocessing of big EEG data"
year: 2019
access_level: "abstract-only"
source_url: "https://doi.org/10.1016/j.neuroimage.2019.06.046"
---

# Automagic: Standardized preprocessing of big EEG data

## 摘要 / Abstract
> [!abstract] Original
> Electroencephalography (EEG) recordings have been rarely included in large-scale studies. This is arguably not due to a lack of information that lies in EEG recordings but mainly on account of methodological issues. In many cases, particularly in clinical, pediatric and aging populations, the EEG has a high degree of artifact contamination and the quality of EEG recordings often substantially differs between subjects. Although a number of standardized preprocessing methods have been proposed to clean EEG from artifacts, there is currently no method to objectively quantify the quality of preprocessed EEG, which renders the procedure of excluding artifact-contaminated EEG highly subjective. The lack of standardized preprocessing and quantitative quality assessment hampers the inclusion of EEG into large-scale studies. To improve this situation we introduce Automagic, an open-source MATLAB toolbox that acts as a wrapper to run currently available preprocessing methods and offers objective standardized quality assessment for growing EEG files. We outline the functionality of Automagic and examine the effect of applying combinations of methods on a range of EEG data, finding that a combination of a pipeline of algorithms detecting artifactual channels combined with the Multiple Artifact Rejection Algorithm (MARA) is sufficient to reduce a large extent of artifacts. The quality measures provided by Automagic allow objective comparison of preprocessing methods and an automatic, reproducible, quantitative exclusion of low-quality recordings.

> [!abstract] 繁體中文摘要
> 腦電圖（EEG）紀錄很少被納入大規模研究。這可以說並非因為 EEG 紀錄缺乏資訊，而主要源於方法學問題。在許多情況下—特別是臨床、兒童與老化族群—EEG 有高度的假影污染，且各受試者間的紀錄品質常有顯著差異。雖然已有多種標準化前處理方法被提出以清除 EEG 假影，目前卻沒有方法能客觀量化前處理後 EEG 的品質，使得排除受假影污染 EEG 的程序高度主觀。缺乏標準化前處理與量化品質評估，妨礙了將 EEG 納入大規模研究。為改善此狀況，我們提出 Automagic—一個開源 MATLAB 工具箱，作為執行現有前處理方法的包裝器，並為持續增長的 EEG 檔案提供客觀、標準化的品質評估。我們概述 Automagic 的功能，並檢驗對一系列 EEG 資料套用各種方法組合的效果，發現「偵測假影通道的演算法管線」結合「多重假影剔除演算法（MARA）」足以去除大部分假影。Automagic 提供的品質量測可客觀比較前處理方法，並達成自動、可重現、量化地排除低品質紀錄。

## Key Points / 重點筆記

> [!note] What problem it solves / 解決什麼問題
> EN: Big EEG datasets are hard to use because preprocessing is non-standardized and there is no objective way to quantify the quality of cleaned EEG — so subject exclusion is subjective and irreproducible. Automagic packages established preprocessing methods into a reproducible wrapper AND, crucially, attaches objective quality metrics so that low-quality recordings can be excluded automatically and consistently.
> 中: 大型 EEG 資料集難以使用，因為前處理未標準化、且沒有客觀方式量化清理後 EEG 的品質—使得受試者排除既主觀又無法重現。Automagic 將既有前處理方法封裝為可重現的包裝器，更關鍵的是附上客觀品質指標，使低品質紀錄能被自動、一致地排除。

> [!note] Method / 方法
> EN: Automagic is an open-source MATLAB toolbox that wraps existing tools (e.g., PREP-style bad-channel detection, EOG regression, MARA-based ICA artifact rejection, robust averaging, interpolation) into configurable pipelines, processes growing collections of EEG files, and computes standardized quality metrics (e.g., overall high-amplitude data ratio, ratio of bad channels, channel-wise residual measures) that it stores alongside each file. Users can then threshold these metrics to keep/flag/exclude recordings reproducibly.
> 中: Automagic 是一個開源 MATLAB 工具箱，將既有工具（如 PREP 式壞通道偵測、EOG 迴歸、基於 MARA 的 ICA 假影剔除、穩健平均、內插）封裝為可設定的管線，處理持續增長的 EEG 檔案集合，並計算標準化品質指標（如整體高振幅資料比例、壞通道比例、逐通道殘差量測），與每個檔案一同儲存。使用者可對這些指標設閾值，以可重現方式保留/標記/排除紀錄。

> [!note] Key findings / 主要發現
> EN: Across resting and task-based EEG, applying a pipeline of algorithms that detect artifactual channels combined with MARA was sufficient to remove a large fraction of artifacts. The objective quality measures enabled direct, quantitative comparison of different preprocessing combinations and supported automatic, reproducible exclusion of low-quality recordings — replacing subjective visual rejection.
> 中: 在靜息與任務型 EEG 上，套用「偵測假影通道的演算法管線」結合 MARA 足以去除大部分假影。客觀品質量測使不同前處理組合能被直接、量化地比較，並支援自動、可重現地排除低品質紀錄—取代主觀的目視剔除。

> [!tip] Relevance to "Agentic EEG preprocessing + FOOOF-SNR" project / 與本計畫的關聯
> EN: Automagic is the closest prior art to our project's spirit and its main contrast point. (b) Quality metric: it independently establishes that *an objective quality metric attached to each recording* is the missing piece for scalable preprocessing — exactly our argument, but our FOOOF-SNR is a spectrally-grounded, task-independent metric whereas Automagic's metrics are amplitude/bad-channel ratios; we can adopt its metric-as-gate philosophy and benchmark FOOOF-SNR against Automagic's quality scores. (a) Action space: the concrete wrapped operations (bad-channel detection, EOG/ICA-MARA rejection, robust referencing, interpolation) define a ready-made, validated set of preprocessing actions for our search space. (c) Greedy-search / LLM-agent: Automagic uses fixed, hand-chosen pipeline combinations; our greedy search and LLM agent generalize this by *adaptively selecting* the combination per recording and scoring it, rather than applying one configured pipeline to all subjects — Automagic provides both the baseline pipeline to beat and the demonstration that combination choice materially affects artifact removal.
> 中: Automagic 是與本計畫精神最接近的先前研究，也是主要對照點。(b) 品質指標：它獨立地確立「為每筆紀錄附上客觀品質指標」是可擴展前處理所缺的一塊—正是我們的論點，但我們的 FOOOF-SNR 是以頻譜為基礎、不依賴任務的指標，而 Automagic 的指標是振幅/壞通道比例；我們可採用其「指標即閘門」哲學，並以 FOOOF-SNR 對標 Automagic 的品質分數。(a) 動作空間：其封裝的具體操作（壞通道偵測、EOG/ICA-MARA 剔除、穩健參考、內插）為我們的搜尋空間提供一組現成、已驗證的前處理動作。(c) 貪婪搜尋/LLM 代理：Automagic 採用固定、手選的管線組合；我們的貪婪搜尋與 LLM 代理將其推廣為*依每筆紀錄自適應選擇*組合並評分，而非對所有受試者套用同一設定管線—Automagic 既提供待超越的基準管線，也示範了組合選擇對假影去除有實質影響。

## Full Text / 全文
（僅摘要可得 / abstract-only — NeuroImage paywalled; built from the verbatim abstract and the publicly documented toolbox. Pedroni, Bahreini & Langer, NeuroImage 2019; DOI 10.1016/j.neuroimage.2019.06.046. Toolbox: github.com/methlabUZH/automagic.）

### Summary of documented functionality
- **Wrapper architecture:** runs existing preprocessing methods (bad-channel detection à la PREP, EOG-based ocular correction, ICA + MARA for artifact components, robust average referencing, channel interpolation) on growing EEG file collections.
- **Objective quality assessment:** computes standardized per-recording quality metrics (e.g., proportion of high-amplitude/timepoints, ratio of bad/interpolated channels, residual channel-wise measures) stored with each file.
- **Reproducible exclusion:** thresholds on these metrics enable automatic, reproducible exclusion of low-quality recordings, replacing subjective visual inspection.
- **Empirical finding:** a pipeline detecting artifactual channels combined with MARA removed a large extent of artifacts in both resting and task-based EEG; quality measures allowed objective comparison across preprocessing combinations.
