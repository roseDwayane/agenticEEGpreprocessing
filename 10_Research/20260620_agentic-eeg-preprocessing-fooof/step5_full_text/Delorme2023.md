---
citation_key: "Delorme2023"
title: "EEG is better left alone"
year: 2023
access_level: "full-text-html"
source_url: "https://www.nature.com/articles/s41598-023-27528-0"
---

# EEG is better left alone

## 摘要 / Abstract
> [!abstract] Original
> Automated preprocessing methods are critically needed to process the large publicly-available EEG databases, but the optimal approach remains unknown because we lack data quality metrics to compare them. Here, the percentage of significant channels between two experimental conditions was used as a data quality metric to evaluate a range of standard EEG preprocessing operations. With high-pass filtering and bad channel interpolation as exceptions, automated data corrections (including re-referencing, baseline removal, artifact rejection via ICA/ICLabel, ASR, automated bad-segment and bad-channel rejection, and line-noise removal) had no effect on, or significantly decreased, the percentage of significant channels. In other words, beyond high-pass filtering and interpolating a small number of bad channels, additional automated "cleaning" generally did not improve, and frequently degraded, the statistical signal in event-related EEG data.

> [!abstract] 繁體中文摘要
> 為了處理大型公開 EEG 資料庫，自動化前處理方法是迫切需要的，但因為缺乏可比較不同方法的資料品質指標，最佳作法仍然未知。本研究以「兩個實驗條件間達到顯著差異的電極百分比」作為資料品質指標，評估了一系列標準 EEG 前處理操作。除了高通濾波與壞通道內插這兩個例外，自動化的資料校正（包括重新參考、基線移除、以 ICA/ICLabel 進行假影去除、ASR、自動壞片段與壞通道剔除、以及線雜訊移除）對顯著電極百分比都「沒有效果」或「顯著降低」。換言之，除了高通濾波與內插少數壞通道之外，額外的自動化「清理」通常無助於改善、且常常反而劣化事件相關 EEG 資料中的統計訊號。

## Key Points / 重點筆記

> [!note] What problem it solves / 解決什麼問題
> EN: The field lacks an objective, automatically-computable metric to decide whether a given EEG preprocessing step actually improves data quality. Delorme proposes using statistical power — the percentage of channels showing a significant difference between two experimental conditions (within an optimal post-stimulus window, via bootstrap resampling) — as that missing quality metric. With this yardstick, he systematically benchmarks the most common preprocessing operations to ask: does cleaning help?
> 中: 此領域缺乏一個客觀、可自動計算的指標，來判斷某個 EEG 前處理步驟是否真的改善資料品質。Delorme 提議用「統計檢定力」—即在最佳刺激後時窗內、透過 bootstrap 重抽樣計算的、兩個實驗條件間達顯著差異的電極百分比—作為這個缺失的品質指標。以此為量尺，他系統性地基準測試最常見的前處理操作，問一個核心問題：清理真的有幫助嗎？

> [!note] Method / 方法
> EN: Three event-related datasets were used: Go/No-go (14 participants, animal vs. distractor), Face (18 participants, familiar vs. scrambled), and Oddball (13 participants, rare 1000 Hz vs. frequent 500 Hz tones). The quality metric measured the percentage of significant channels within a 100-ms post-stimulus window, using bootstrap resampling of 50 trials across 20,000 iterations. Each preprocessing operation (high-pass filtering, re-referencing, baseline removal, channel/segment rejection, ASR, ICA+ICLabel eye/muscle removal, line-noise removal) was applied individually and as full pipelines (EEGLAB, FieldTrip, Brainstorm, MNE, HAPPE) and scored against this metric.
> 中: 使用三個事件相關資料集：Go/No-go（14 人，動物 vs. 干擾物）、Face（18 人，熟悉 vs. 打亂臉孔）、Oddball（13 人，罕見 1000 Hz vs. 頻繁 500 Hz 音調）。品質指標衡量刺激後 100 毫秒時窗內的顯著電極百分比，以 50 個 trial 進行 bootstrap 重抽樣、共 20,000 次迭代。每個前處理操作（高通濾波、重新參考、基線移除、通道/片段剔除、ASR、ICA+ICLabel 去眼動/肌電、線雜訊移除）皆個別套用，並組成完整管線（EEGLAB、FieldTrip、Brainstorm、MNE、HAPPE）後依此指標評分。

> [!note] Key findings / 主要發現
> EN: High-pass filtering was by far the most beneficial step, improving performance by 13% (Face), 47% (Oddball), and 57% (Go/No-go), with optimal cutoffs of 0.1–0.75 Hz. Bad-channel interpolation (clean_rawdata at 0.9 correlation) gave modest 2–15% gains. Almost everything else hurt or did nothing: re-referencing consistently decreased performance; baseline removal was neutral-to-harmful once data were filtered; ASR, ICA/ICLabel, FieldTrip z-thresholding, MNE Autoreject, and line-noise removal (notch, cleanline, Zapline-plus) gave inconsistent or negative results. Only the EEGLAB pipeline beat simple 0.5 Hz filtering (by 5–18%); other software pipelines showed no improvement over plain filtering.
> 中: 高通濾波是迄今最有益的步驟，分別改善 13%（Face）、47%（Oddball）、57%（Go/No-go），最佳截止頻率介於 0.1–0.75 Hz。壞通道內插（clean_rawdata，相關 0.9）帶來 2–15% 的小幅增益。其餘幾乎所有操作都有害或無效：重新參考一致地降低表現；資料一旦濾波後基線移除為中性到有害；ASR、ICA/ICLabel、FieldTrip z 閾值、MNE Autoreject、以及線雜訊移除（notch、cleanline、Zapline-plus）結果不一致或為負。只有 EEGLAB 管線勝過簡單的 0.5 Hz 濾波（高 5–18%）；其他軟體管線相對於純濾波沒有改善。

> [!tip] Relevance to "Agentic EEG preprocessing + FOOOF-SNR" project / 與本計畫的關聯
> EN: This is the foundational "negative result" that motivates the whole project's framing. (a) Action space: it tells us most aggressive cleaning actions (re-referencing, baseline removal, ASR, ICLabel, line-noise removal) are NOT reliably beneficial — so a greedy search must be allowed to choose "do nothing," and high-pass filtering + bad-channel interpolation should be near-default/cheap actions. (b) Quality metric: Delorme's "% significant channels" is a *task-dependent* discriminability metric; our FOOOF-SNR (aperiodic-noise vs. periodic-signal) is proposed precisely as a *task-independent* alternative that does not need two experimental conditions — making it usable on resting-state and single-condition data where Delorme's metric fails. (c) Greedy-search ground truth: this paper validates the methodology of scoring each preprocessing operation by an objective metric and ranking them, which is exactly what the greedy-search ground-truth generator does; it also warns that the "optimal" pipeline is dataset-specific (different cutoffs per dataset), justifying a per-recording adaptive/LLM-agent search rather than one fixed pipeline.
> 中: 這是促成整個計畫框架的基礎性「負面結果」。(a) 動作空間：它告訴我們大多數激進的清理動作（重新參考、基線移除、ASR、ICLabel、線雜訊移除）並非可靠有益—因此貪婪搜尋必須允許選擇「什麼都不做」，而高通濾波＋壞通道內插應為近乎預設/低成本的動作。(b) 品質指標：Delorme 的「顯著電極百分比」是*依賴任務*的可分性指標；我們的 FOOOF-SNR（非週期-雜訊 vs. 週期-訊號）正是被提出作為*不依賴任務*的替代方案，不需要兩個實驗條件—使其可用於 Delorme 指標失效的靜息態與單一條件資料。(c) 貪婪搜尋真值：本文驗證了「以客觀指標為每個前處理操作評分並排序」的方法學，這正是貪婪搜尋真值產生器所做的；它也警告「最佳」管線是資料集特定的（每個資料集最佳截止頻率不同），佐證了採用每筆紀錄自適應/LLM 代理搜尋、而非單一固定管線的必要性。

## Full Text / 全文

### Abstract
Automated preprocessing methods are critically needed to process the large publicly-available EEG databases, but the optimal approach remains unknown because we lack data quality metrics to compare them. With high-pass filtering and bad channel interpolation as exceptions, automated data corrections had no effect on or significantly decreased the percentage of significant channels (the data quality metric used).

### Introduction
EEG preprocessing lacks consensus. Manual inspection by expert researchers remains the gold standard but is time-consuming and subjective. The author proposes using statistical power — the percentage of significant channels between experimental conditions — as an objective, automatically computable quality metric, then systematically benchmarks common preprocessing operations against it.

### Methods
Three event-related datasets were analyzed:
- **Go/No-go** (14 participants): Visual categorization, animals vs. distractors.
- **Face** (18 participants): Symmetry judgment, familiar vs. scrambled faces.
- **Oddball** (13 participants): Auditory, infrequent 1000 Hz tones vs. frequent 500 Hz tones.

The quality metric measured the percentage of significant channels between two experimental conditions within optimal 100-ms post-stimulus windows, using bootstrap resampling of 50 trials across 20,000 iterations.

### Results
**High-pass filtering (most impactful):** improved performance by 13% (Face), 47% (Oddball), 57% (Go/No-go); optimal cutoffs 0.1 Hz (Face), 0.5 Hz (Oddball), 0.75 Hz (Go/No-go).

**Referencing (detrimental):** all re-referencing methods decreased or showed a non-significant decrease in performance — contradicting standard practice.

**Baseline removal (harmful when filtering applied):** baseline subtraction showed no effect or a negative effect; a 400-ms baseline decreased performance 3–6%; compared with 0.01 Hz filtering + 200-ms baseline, the 0.5 Hz filter without baseline gave a 30–42% performance advantage.

**Artifact rejection (inconsistent/weak):** clean_rawdata channel rejection (0.9 correlation) gave significant but modest 2–15% improvements; ASR rejection gave no significant improvement when using all remaining trials; ICA/ICLabel eye/muscle removal was unreliable; FieldTrip z-thresholding mixed/sometimes harmful; Brainstorm bad-segment detection helped only Go/No-go; MNE Autoreject no significant improvement.

**Line-noise removal:** notch filtering — no change; cleanline and Zapline-plus actually decreased performance.

**Optimized pipeline comparison:** EEGLAB pipeline performed best (5–18% over simple 0.5 Hz filtering); FieldTrip, Brainstorm, MNE, HAPPE showed no improvement over plain filtering for most datasets.

### Discussion
The counterintuitive result — that most preprocessing harms rather than helps — likely stems from examining relatively clean laboratory data; results may differ for noisier real-world recordings. The consistent harm from re-referencing warrants more research into analog vs. digital referencing, especially combined with high-pass filtering. When data are high-pass filtered at ≥0.5 Hz, subtracting mean baseline activity should be omitted for event-related analyses.
