---
citation_key: "GersterEtAl2022"
title: "Separating Neural Oscillations from Aperiodic 1/f Activity: Challenges and Recommendations"
year: 2022
access_level: "full-text-html"
source_url: "https://pmc.ncbi.nlm.nih.gov/articles/PMC9588478/"
---

# Separating Neural Oscillations from Aperiodic 1/f Activity: Challenges and Recommendations

## 摘要 / Abstract
> [!abstract] Original
> Electrophysiological power spectra typically consist of two components: an aperiodic part usually following an 1/f power law P ∝ 1/f^β and periodic components appearing as spectral peaks. While the investigation of the periodic parts has a long history in neuroscience, the aperiodic part has more recently attracted increasing attention. Separating the two components is, however, non-trivial. The authors scrutinize two frequently used methods, FOOOF (Fitting Oscillations & One-Over-F) and IRASA (Irregular Resampling Auto-Spectral Analysis), that are commonly used to separate the periodic from the aperiodic component. They evaluate these methods using diverse, real-world and simulated spectra obtained with electroencephalography (EEG), magnetoencephalography (MEG), and local field potential (LFP) recordings, identify spectral features that hinder a reliable separation, quantify parameterization errors, and provide concrete recommendations for choosing the method, the fitting range, and the resampling/peak-width parameters.

> [!abstract] 繁體中文摘要
> 電生理功率譜通常由兩部分組成：一個通常遵循 1/f 冪律 P ∝ 1/f^β 的非週期性部分，以及以頻譜峰形式出現的週期性成分。雖然週期性部分在神經科學中研究歷史悠久，非週期性部分近來才日益受到關注。然而分離這兩個成分並不簡單。作者仔細檢視兩種常用方法—FOOOF（Fitting Oscillations & One-Over-F）與 IRASA（Irregular Resampling Auto-Spectral Analysis）—它們常被用來分離週期性與非週期性成分。作者以 EEG、MEG 與局部場電位（LFP）取得的多樣化真實與模擬頻譜評估這兩種方法，找出妨礙可靠分離的頻譜特徵，量化參數化誤差，並對方法選擇、擬合範圍、重抽樣/峰寬參數提供具體建議。

## Key Points / 重點筆記

> [!note] What problem it solves / 解決什麼問題
> EN: As FOOOF and IRASA became popular for extracting the aperiodic exponent (a putative E/I-balance biomarker), practitioners often apply them as black boxes. This paper is a critical "user's guide" that catalogues exactly when these separation methods produce wrong exponents, so that the aperiodic component is not mis-measured and over-interpreted. It reframes separation as an ill-posed inverse problem with no unique solution for "hard" spectra.
> 中: 隨著 FOOOF 與 IRASA 成為提取非週期性指數（推定的 E/I 平衡生物標記）的熱門工具，使用者常把它們當黑箱套用。本文是一份批判性的「使用者指南」，明確列舉這些分離方法在何時會產生錯誤的指數，以避免非週期性成分被誤量測與過度解讀。它將分離重新框定為一個不適定的反問題—對「困難」頻譜沒有唯一解。

> [!note] Method / 方法
> EN: FOOOF fits the PSD in log-log space iteratively (robust linear aperiodic fit → flatten → fit Gaussian peaks above a threshold → subtract → re-fit aperiodic), parameterized by peak-width limits, max peak count, thresholds, and fitting range. IRASA works on the time series: it up/down-samples by factors h (e.g., 1.1–1.9), takes geometric means and the median of resampled PSD pairs to estimate the smooth aperiodic part, then subtracts it. The authors test both on simulated spectra with known ground truth and on three empirical datasets (Parkinsonian MEG/LFP, an absence-epilepsy EEG, additional MEG/LFP), sweeping the fitting range, knee, and resampling factors.
> 中: FOOOF 在對數-對數空間中迭代擬合 PSD（穩健線性非週期擬合 → 拉平 → 對高於閾值的峰擬合高斯 → 扣除 → 重新擬合非週期），以峰寬限制、最大峰數、閾值與擬合範圍參數化。IRASA 作用於時間序列：以因子 h（如 1.1–1.9）上/下取樣，取重抽樣 PSD 對的幾何平均與中位數來估計平滑的非週期部分，再扣除之。作者在已知真值的模擬頻譜與三個實證資料集（帕金森氏 MEG/LFP、一例失神性癲癇 EEG、額外 MEG/LFP）上測試兩者，掃描擬合範圍、膝參數與重抽樣因子。

> [!note] Key findings / 主要發現
> EN: Three failure modes dominate. (1) High-frequency spectral plateaus (amplifier noise, EMG, spiking) flatten the 1/f law — including the plateau in the fit drove a true β=2 down to β=0.70. (2) Oscillations crossing the fitting-range borders (especially broad delta) bias the exponent — they show ~18% error in either direction when delta power varies but the true aperiodic part is constant; recommend higher lower-border frequencies (40–60 Hz) for clean exponent estimation. (3) Heavily overlapping peaks/harmonics (e.g., 3 Hz spike-wave in seizure) have no unique decomposition — FOOOF reported β jumping 1.52→2.31 despite constant ground truth (β=1.8). For IRASA, the *evaluated* frequency range exceeds the fitting range by the resampling factors, so it can leak into filter stopbands/plateaus; broad peaks need large h_max (conflicting with the need to keep h_max small). FOOOF is ~50–100× faster than IRASA. The authors classify spectra as "easy" (straight in log-log, narrow well-separated peaks — methods agree) vs. "hard" (nonlinear, broad/overlapping peaks, plateau — methods diverge, e.g., β_FOOOF=0.82 vs β_IRASA=1.10) and recommend separation only for "easy" spectra.
> 中: 三種失效模式為主。(1) 高頻頻譜平台（放大器雜訊、肌電、放電）會拉平 1/f 律—把平台納入擬合會使真值 β=2 降到 β=0.70。(2) 跨越擬合範圍邊界的振盪（尤其寬的 delta）會偏誤指數—當 delta 功率變動但真實非週期部分不變時，誤差達任一方向約 18%；建議用較高的下界頻率（40–60 Hz）做乾淨的指數估計。(3) 嚴重重疊的峰/諧波（如癲癇中的 3 Hz 棘慢波）沒有唯一分解—FOOOF 回報 β 從 1.52 跳到 2.31，儘管真值固定（β=1.8）。對 IRASA，*被評估的*頻率範圍會因重抽樣因子超出擬合範圍，因而可能洩漏進濾波止帶/平台；寬峰需要大的 h_max（與需保持 h_max 小相衝突）。FOOOF 比 IRASA 快約 50–100 倍。作者將頻譜分為「容易」（對數-對數呈直線、窄且分離良好的峰—兩法一致）與「困難」（非線性、寬/重疊峰、平台—兩法分歧，如 β_FOOOF=0.82 vs β_IRASA=1.10），並建議只對「容易」頻譜進行分離。

> [!tip] Relevance to "Agentic EEG preprocessing + FOOOF-SNR" project / 與本計畫的關聯
> EN: This paper is the safety manual for our FOOOF-SNR metric. (b) FOOOF-SNR metric: it tells us the metric is only trustworthy on "easy" spectra; we must add a *fit-validity gate* before scoring — check fit R²/error, exclude plateau-contaminated high-frequency ranges, avoid letting delta or overlapping harmonics dominate the fitting range. This directly shapes how we compute aperiodic(noise) vs periodic(signal): use a stable lower border (e.g., 1–2 Hz only if delta is clean, else higher) and an upper border below the plateau onset. (a) Action space: preprocessing actions that change the plateau (line-noise/EMG handling, low-pass filtering) or distort low frequencies (high-pass cutoff choice) will move the apparent exponent — so the agent must be told that a "better" FOOOF-SNR could be an artifact of a distorted fit, not real denoising. (c) Greedy-search ground truth: when ranking actions, we should penalize actions that push spectra from "easy" into "hard" (rising fit error, exponent instability across conditions) — this gives the greedy search and LLM agent a principled guardrail so they do not chase spurious SNR gains caused by FOOOF mis-fitting.
> 中: 本文是我們 FOOOF-SNR 指標的安全手冊。(b) FOOOF-SNR 指標：它告訴我們該指標僅在「容易」頻譜上可信；評分前必須加上*擬合有效性閘門*—檢查擬合 R²/誤差、排除受平台污染的高頻範圍、避免讓 delta 或重疊諧波主導擬合範圍。這直接形塑我們如何計算非週期(雜訊) vs 週期(訊號)：採用穩定的下界（如 delta 乾淨時才用 1–2 Hz，否則更高）與低於平台起點的上界。(a) 動作空間：改變平台的前處理動作（線雜訊/肌電處理、低通濾波）或扭曲低頻者（高通截止選擇）會移動表觀指數—因此必須告知代理「更好」的 FOOOF-SNR 可能是擬合扭曲的假象、而非真正去噪。(c) 貪婪搜尋真值：排序動作時，應懲罰把頻譜從「容易」推向「困難」的動作（擬合誤差上升、跨條件指數不穩）—這給貪婪搜尋與 LLM 代理一個有原則的護欄，使其不致追逐由 FOOOF 誤擬合造成的虛假 SNR 增益。

## Full Text / 全文

### Abstract
Electrophysiological power spectra consist of an aperiodic 1/f-like component (P ∝ 1/f^β) and periodic spectral peaks. The authors scrutinize FOOOF and IRASA for separating these, evaluate them on EEG/MEG/LFP and simulated spectra, identify spectral features that hinder separation, quantify errors, and give recommendations.

### Introduction
Neural spectra mix oscillatory peaks (periodic) and continuous 1/f background (aperiodic, parameterized by exponent β and offset). The aperiodic part is now of interest as a putative E/I-balance biomarker. Standard bandpass filtering conflates periodic and aperiodic activity; proper separation is needed but non-trivial.

### Methods
**FOOOF** (on PSD, log-log): robust linear aperiodic fit → flatten → iteratively fit Gaussian peaks (center, amplitude, bandwidth) above a relative threshold (default 2 SD) → subtract → re-fit aperiodic. Key params: peak-width limits (default 0.5–12 Hz), max peaks, thresholds, fitting range.
**IRASA** (on time series): up/down-sample by factors h (e.g., 1.1–1.9), take geometric means of up/down pairs, median across pairs → aperiodic estimate; subtract for oscillatory part; fit aperiodic in log-log. Note: the *evaluated* range = fitting_min/h_max to fitting_max×h_max.
**Testing:** simulated spectra with known parameters + three empirical datasets (Parkinsonian MEG/LFP; absence-epilepsy EEG; further MEG/LFP).

### Results — FOOOF challenges
1. **Spectral plateau disrupts the 1/f law:** high-frequency plateaus (amplifier noise, EMG, spiking) make β too small; e.g., true β=2 estimated as 0.70 when the range extends into the plateau, vs 1.97 in plateau-free ranges. Recommendation: choose the lowest feasible upper border; determine plateau onset per condition.
2. **Oscillations crossing fitting-range borders:** partial peaks (esp. broad delta crossing the lower border) cause ~18% exponent error in either direction when delta power varies but true aperiodic is constant. Recommendation: for clean exponent, use higher lower borders (40–60 Hz); for aperiodic removal, accept the limitation and fit broad ranges.
3. **Overlapping peaks cannot be reliably characterized:** absence-seizure 3 Hz spike-wave + harmonics → FOOOF β=2.31 (seizure) vs 1.52 (pre-seizure) despite constant ground truth (β=1.8). Recommendation: avoid fitting over overlapping-peak ranges.

### Results — IRASA challenges
1. **Evaluated range exceeds fitting range:** min_eval = fit_min/h_max, max_eval = fit_max×h_max; large h_max can leak into filter stopbands or plateaus (e.g., low-pass-filtered spectrum gives β=1.09 wrong vs 1.38 correct when matched to FOOOF's evaluated range). Recommendation: compute evaluated frequencies; keep h_max as small as possible.
2. **Broad peaks need large resampling factors:** separation depends on logarithmic peak width; widths of 0.1 log(Hz) need h_max≥2, but 0.3 log(Hz) need h_max≥35 — conflicting with challenge 1. If irreconcilable, IRASA cannot be applied.
3. **Overlapping peaks:** IRASA degrades more gracefully than FOOOF on harmonics (β 2.24→2.46 in seizure) but artificial overlapping alpha+beta peaks pushed β to 3.05 vs ground truth 1.8.

### Discussion & Recommendations
- Plateaus bias exponent low, risking misinterpretation as increased E/I ratio; ultra-low-noise MEG shifts plateaus to kHz.
- No universal fitting range; literature ranges vary wildly; higher lower borders avoid delta contamination.
- The knee parameter cannot capture plateau onsets that differ across conditions.
- 1/f exponent as an E/I proxy remains a hypothesis (e.g., REM > NREM β contradicts simple interpretations); validate with independent methods.
- FOOOF ≈ 50–100× faster than IRASA.
- **"Easy" spectra** (straight in log-log, narrow well-separated peaks, no plateau, >4 orders of magnitude): methods agree (β ≈ 1.53–1.71). **"Hard" spectra** (nonlinear, broad/overlapping peaks, plateau, ~1 order of magnitude): methods diverge (β_FOOOF=0.82, β_IRASA=1.10, β_straight=0.62). Use separation only for "easy" spectra; otherwise avoid.
