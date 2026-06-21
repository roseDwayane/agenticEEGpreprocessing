---
citation_key: "DonoghueEtAl2020b"
title: "Parameterizing neural power spectra into periodic and aperiodic components"
year: 2020
access_level: "abstract-only"
source_url: "https://www.nature.com/articles/s41593-020-00744-x"
---

# Parameterizing neural power spectra into periodic and aperiodic components

## 摘要 / Abstract
> [!abstract] Original
> Electrophysiological signals exhibit both periodic and aperiodic properties. Periodic oscillations have been linked to numerous physiological, cognitive, behavioral and disease states. Emerging evidence demonstrates that the aperiodic component has putative physiological interpretations and that it dynamically changes with age, task demands and cognitive states. Electrophysiological neural activity is typically analyzed using canonically defined frequency bands, without consideration of the aperiodic (1/f-like) component. We show that standard analytic approaches can conflate periodic parameters (center frequency, power, bandwidth) with aperiodic ones (offset, exponent), compromising physiological interpretations. To overcome these limitations, we introduce an algorithm to parameterize neural power spectra as a combination of an aperiodic component and putative periodic oscillatory peaks. This algorithm requires no a priori specification of frequency bands.

> [!abstract] 繁體中文摘要
> 電生理訊號同時具有週期性與非週期性的特性。週期性振盪已被連結到眾多生理、認知、行為與疾病狀態。新興證據顯示非週期性成分具有可能的生理意義，且會隨年齡、任務需求與認知狀態動態變化。電生理神經活動通常以正規定義的頻帶來分析，而不考慮非週期性（類 1/f）成分。我們證明標準分析方法會將週期性參數（中心頻率、功率、頻寬）與非週期性參數（偏移、指數）混淆，損害生理解讀。為克服這些限制，我們提出一個演算法，將神經功率譜參數化為「一個非週期性成分」加上「若干推定的週期性振盪峰」的組合。此演算法不需事先指定頻帶。

## Key Points / 重點筆記

> [!note] What problem it solves / 解決什麼問題
> EN: Traditional spectral analysis bins power into canonical bands (delta, theta, alpha, beta, gamma) and treats any band-power change as an oscillation change. But the broadband 1/f-like aperiodic background also carries power that leaks into every band; if the aperiodic offset or exponent shifts, canonical band-power measures spuriously report an "oscillation" change. FOOOF (now "specparam") solves this by explicitly modeling and separating the aperiodic background from genuine periodic peaks, so that each is measured without contamination by the other.
> 中: 傳統頻譜分析將功率分入正規頻帶（delta、theta、alpha、beta、gamma），並把任何頻帶功率變化都當成振盪變化。但寬頻、類 1/f 的非週期性背景也帶有功率並滲入每個頻帶；若非週期性偏移或指數移動，正規頻帶功率指標就會謬誤地回報「振盪」變化。FOOOF（現稱 specparam）藉由明確建模並分離非週期性背景與真正的週期性峰來解決此問題，使兩者皆能在不被彼此污染下被量測。

> [!note] Method / 方法
> EN: The power spectrum (in log-log space) is modeled as the sum of (1) an aperiodic component L = b − log10(k + F^χ), where b is the offset, χ the exponent (steepness of the 1/f slope), and k an optional knee capturing bending in log-log space; and (2) a set of Gaussian periodic peaks, each parameterized by center frequency, power (height above the aperiodic fit), and bandwidth. Fitting is iterative: an initial aperiodic fit is subtracted to flatten the spectrum, Gaussians are fit to the remaining peaks above a noise threshold, the peaks are subtracted, and the aperiodic component is re-fit on the peak-removed spectrum. No frequency bands are specified a priori.
> 中: 功率譜（在對數-對數空間中）被建模為以下兩者之和：(1) 非週期性成分 L = b − log10(k + F^χ)，其中 b 為偏移、χ 為指數（1/f 斜率陡峭度）、k 為可選的「膝」參數以捕捉對數-對數空間中的彎曲；(2) 一組高斯週期性峰，每個以中心頻率、功率（高於非週期性擬合的高度）、頻寬參數化。擬合為迭代式：先扣除初始非週期性擬合以拉平頻譜，對殘餘高於雜訊閾值的峰擬合高斯，扣除這些峰，再於去峰後的頻譜上重新擬合非週期性成分。完全不需事先指定頻帶。

> [!note] Key findings / 主要發現
> EN: On simulated spectra with known ground truth, the algorithm accurately recovered both peak parameters and aperiodic parameters across noise levels, and performed comparably to expert human raters on real EEG and LFP data. Crucially, the authors demonstrate that changing the aperiodic exponent alone produces apparent narrowband power changes under conventional band analysis even when no true oscillation changed — directly showing how band-power conflates the two. The aperiodic exponent has putative links to cortical excitation/inhibition (E/I) balance and changes with age, task, and state.
> 中: 在已知真值的模擬頻譜上，該演算法在各種雜訊水準下都能準確還原峰參數與非週期性參數，並在真實 EEG 與 LFP 資料上表現與專家人工評分者相當。關鍵在於作者證明：僅改變非週期性指數，在傳統頻帶分析下即會產生看似窄頻的功率變化，即使沒有任何真正振盪改變—直接展示頻帶功率如何混淆兩者。非週期性指數被認為與皮質興奮/抑制（E/I）平衡相關，並隨年齡、任務與狀態變化。

> [!tip] Relevance to "Agentic EEG preprocessing + FOOOF-SNR" project / 與本計畫的關聯
> EN: This is the core algorithm that defines our quality metric. (b) FOOOF-SNR metric: the project's signal/noise decomposition maps directly onto FOOOF's periodic vs. aperiodic split — the periodic peaks (oscillatory power above the 1/f fit) are treated as "signal," and the aperiodic component (offset + exponent) is treated as the "noise" background; a FOOOF-SNR can be defined as periodic power / aperiodic power, or via fit-error / R². (a) Action space: because FOOOF cleanly separates the two, we can evaluate whether a preprocessing action increases genuine oscillatory SNR vs. merely changing the broadband floor (e.g., line-noise removal should not change χ; over-filtering can distort the exponent). (c) Greedy-search ground truth: FOOOF parameters (exponent stability, peak count, fit R²) give a *task-independent, single-recording* score for ranking preprocessing actions, complementing Delorme's task-dependent metric — this is exactly the scoring function the greedy search and LLM agent optimize. The companion Gerster paper warns about when this fit becomes unreliable.
> 中: 這是定義我們品質指標的核心演算法。(b) FOOOF-SNR 指標：本計畫的訊號/雜訊分解直接對應 FOOOF 的週期性 vs. 非週期性切分—週期性峰（高於 1/f 擬合的振盪功率）視為「訊號」，非週期性成分（偏移＋指數）視為「雜訊」背景；FOOOF-SNR 可定義為 週期性功率 / 非週期性功率，或透過擬合誤差 / R²。(a) 動作空間：因 FOOOF 乾淨地分離兩者，我們能評估某前處理動作是真的提升振盪 SNR，還是僅改變寬頻基底（例如線雜訊移除不應改變 χ；過度濾波可能扭曲指數）。(c) 貪婪搜尋真值：FOOOF 參數（指數穩定性、峰數、擬合 R²）提供一個*不依賴任務、單筆紀錄*的分數來排序前處理動作，與 Delorme 的依賴任務指標互補—這正是貪婪搜尋與 LLM 代理所最佳化的評分函數。姊妹篇 Gerster 論文則警示此擬合何時變得不可靠。

## Full Text / 全文
（僅摘要可得 / abstract-only — Nature Neuroscience is paywalled; this note is built from the verbatim abstract plus the publicly described algorithm. Published in Nature Neuroscience 23:1655–1665, 2020. Software: the FOOOF / specparam toolbox, github.com/fooof-tools/fooof.）

### Algorithm (as described)
- **Aperiodic component:** L = b − log10(k + F^χ); b = offset, χ = exponent (1/f slope), k = optional knee.
- **Periodic component:** Gaussian peaks, each with center frequency, power (height above aperiodic fit), and bandwidth.
- **Iterative fit:** (1) fit initial aperiodic component; (2) flatten by subtracting it; (3) fit Gaussians to peaks above a relative threshold (default ~2 SD); (4) subtract fitted peaks; (5) re-fit aperiodic component on the peak-removed spectrum; (6) combine into final model and compute goodness-of-fit (R², error). No a priori frequency bands.

### Validation & key results
- Accurate recovery of ground-truth peak and aperiodic parameters on simulated spectra across noise levels.
- Performance comparable to human raters on real EEG and LFP data.
- Demonstration that conventional band-power analysis conflates aperiodic and periodic features: shifting the aperiodic exponent alone produces spurious narrowband power changes.
- The aperiodic exponent has putative physiological interpretation (E/I balance) and changes with age, task demands, and cognitive state.
