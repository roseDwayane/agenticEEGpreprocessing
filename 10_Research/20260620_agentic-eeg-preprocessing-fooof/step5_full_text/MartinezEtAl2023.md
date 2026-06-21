---
citation_key: "MartinezEtAl2023"
title: "Towards Personalized Preprocessing Pipeline Search"
year: 2023
access_level: "full-text-pdf"
source_url: "https://arxiv.org/abs/2302.14329"
---

# Towards Personalized Preprocessing Pipeline Search

## 摘要 / Abstract
> [!abstract] Original
> Feature preprocessing, which transforms raw input features into numerical representations, is a crucial step in automated machine learning (AutoML) systems. However, the existing systems often have a very small search space for feature preprocessing with the same preprocessing pipeline applied to all the numerical features. This may result in sub-optimal performance since different datasets often have various feature characteristics, and features within a dataset may also have their own preprocessing preferences. To bridge this gap, we explore personalized preprocessing pipeline search, where the search algorithm is allowed to adopt a different preprocessing pipeline for each feature. This is a challenging task because the search space grows exponentially with more features. To tackle this challenge, we propose ClusterP3S, a novel framework for Personalized Preprocessing Pipeline Search via Clustering. The key idea is to learn feature clusters such that the search space can be significantly reduced by using the same preprocessing pipeline for the features within a cluster. To this end, we propose a hierarchical search strategy to jointly learn the clusters and search for the optimal pipelines, where the upper-level search optimizes the feature clustering to enable better pipelines built upon the clusters, and the lower-level search optimizes the pipeline given a specific cluster assignment. We instantiate this idea with a deep clustering network that is trained with reinforcement learning at the upper-level, and random search at the lower level. Experiments on benchmark classification datasets demonstrate the effectiveness of enabling feature-wise preprocessing pipeline search.

> [!abstract] 繁體中文摘要
> 特徵前處理—將原始輸入特徵轉換為數值表示—是自動化機器學習（AutoML）系統中的關鍵步驟。然而現有系統的特徵前處理搜尋空間往往很小，且對所有數值特徵套用相同的前處理管線。這可能導致次佳表現，因為不同資料集常有不同的特徵特性，且資料集內各特徵也可能有自己的前處理偏好。為彌補此落差，我們探討「個人化前處理管線搜尋」，允許搜尋演算法對每個特徵採用不同的前處理管線。這是一項挑戰，因為搜尋空間隨特徵增多呈指數成長。為此，我們提出 ClusterP3S—一個透過分群進行個人化前處理管線搜尋的新框架。核心想法是學習特徵分群，使同一群內的特徵共用相同前處理管線，從而大幅縮減搜尋空間。我們提出階層式搜尋策略以聯合學習分群並搜尋最佳管線：上層搜尋最佳化特徵分群以建構更好的管線，下層搜尋則在給定分群指派下最佳化管線。我們以一個深度分群網路實作此想法，上層以強化學習訓練、下層用隨機搜尋。在基準分類資料集上的實驗證明了啟用逐特徵前處理管線搜尋的有效性。

## Key Points / 重點筆記

> [!note] What problem it solves / 解決什麼問題
> EN: Existing AutoML systems (e.g., Auto-Sklearn has only ~21 combinations) use a single, small preprocessing search space and apply the same pipeline to every numerical feature. But different features have different distributions and "preferences," so a one-size-fits-all pipeline is sub-optimal. The paper asks whether *feature-wise* (personalized) preprocessing search improves performance, and how to make the resulting exponential search space (|P|^D for D features) tractable.
> 中: 現有 AutoML 系統（如 Auto-Sklearn 僅約 21 種組合）使用單一、很小的前處理搜尋空間，並對每個數值特徵套用相同管線。但不同特徵有不同的分布與「偏好」，故一體適用的管線並非最佳。本文探討*逐特徵*（個人化）前處理搜尋是否能提升表現，以及如何讓由此產生的指數級搜尋空間（D 個特徵為 |P|^D）變得可處理。

> [!note] Method / 方法
> EN: ClusterP3S reduces the search space by clustering features and assigning one pipeline per cluster instead of per feature. It uses a bi-level (hierarchical) search: the upper level optimizes a deep clustering network (trained by reinforcement learning) that groups features so good cluster-level pipelines exist; the lower level uses random search to find the best pipeline given a cluster assignment. The two levels are learned jointly, so clustering and pipeline search co-adapt.
> 中: ClusterP3S 透過對特徵分群、並對每個群（而非每個特徵）指派一條管線，來縮減搜尋空間。它採用雙層（階層式）搜尋：上層最佳化一個深度分群網路（以強化學習訓練），將特徵分群以使群層級的好管線得以存在；下層在給定分群指派下用隨機搜尋找最佳管線。兩層聯合學習，使分群與管線搜尋相互調適。

> [!note] Key findings / 主要發現
> EN: On benchmark classification datasets, enabling feature-wise preprocessing pipeline search via ClusterP3S significantly improves performance over applying a single shared pipeline to all features. The clustering-based reduction makes the otherwise exponential search feasible, and the bi-level joint optimization outperforms naive approaches. The authors position learning feature clusters as a general principle that can motivate future AutoML designs.
> 中: 在基準分類資料集上，透過 ClusterP3S 啟用逐特徵前處理管線搜尋，顯著優於對所有特徵套用單一共享管線。基於分群的縮減使原本指數級的搜尋變得可行，雙層聯合最佳化也勝過樸素作法。作者將「學習特徵分群」定位為一個可啟發未來 AutoML 設計的通用原則。

> [!tip] Relevance to "Agentic EEG preprocessing + FOOOF-SNR" project / 與本計畫的關聯
> EN: This is the methodological template for "personalized preprocessing search," translated from tabular features to EEG. (c) Greedy-search ground truth: ClusterP3S's bi-level search (cluster → pipeline) maps onto our problem if we treat EEG channels (or recordings) as the entities to be clustered and per-cluster preprocessing pipelines as the search target — its clustering trick is exactly how we could tame the combinatorial explosion when different channels/subjects need different cleaning, making greedy ground-truth generation cheaper. (a) Action space: it formalizes a per-entity pipeline as an ordered sequence of preprocessing primitives drawn from a primitive set, which is the same structure as our EEG action sequence (filter → bad-channel → ASR → ICA → …). (b) FOOOF-SNR: ClusterP3S optimizes a downstream task metric (classification accuracy); our contribution is to swap that for a task-independent FOOOF-SNR reward, so the same RL/search machinery can personalize EEG preprocessing without labels. The key contrast: ClusterP3S personalizes *across features with no per-feature ground truth*, exactly the regime where an unsupervised FOOOF-SNR signal is most valuable.
> 中: 這是「個人化前處理搜尋」的方法學範本，可從表格特徵移轉到 EEG。(c) 貪婪搜尋真值：ClusterP3S 的雙層搜尋（分群→管線）若把 EEG 通道（或紀錄）視為待分群實體、把每群前處理管線視為搜尋目標，便對應到我們的問題—其分群技巧正是當不同通道/受試者需要不同清理時、馴服組合爆炸的方法，使貪婪真值產生更省成本。(a) 動作空間：它將每個實體的管線形式化為從基元集合中抽取的有序前處理基元序列，這與我們的 EEG 動作序列（濾波→壞通道→ASR→ICA→…）結構相同。(b) FOOOF-SNR：ClusterP3S 最佳化下游任務指標（分類準確率）；我們的貢獻是把它換成不依賴任務的 FOOOF-SNR 獎勵，使同一套 RL/搜尋機制能在無標籤下個人化 EEG 前處理。關鍵對比：ClusterP3S 在*跨特徵且無逐特徵真值*下做個人化，正是無監督 FOOOF-SNR 訊號最有價值的情境。

## Full Text / 全文

### Abstract
Feature preprocessing is a crucial step in AutoML, but existing systems use a small search space and apply the same pipeline to all numerical features, which is sub-optimal because features differ. The paper explores personalized (feature-wise) preprocessing pipeline search. Because the search space is |P|^D (exponential in the number of features D), the authors propose **ClusterP3S**, which learns feature clusters so the same pipeline is shared within a cluster, drastically reducing the space. A hierarchical (bi-level) search jointly learns the clusters and the pipelines: an upper-level deep clustering network trained by reinforcement learning optimizes the clustering, and a lower-level random search optimizes the pipeline per cluster. Benchmark classification experiments show feature-wise search is effective.

### 1 Introduction
Feature preprocessing transforms raw inputs into numerical representations through primitives such as imputation and normalization; ~50% of ML-building time is spent on preprocessing. Existing AutoML systems (e.g., Auto-Sklearn, AlphaD3M) have very small preprocessing search spaces (Auto-Sklearn: only ~21 combinations) and apply the same primitives to all numerical features. This is sub-optimal because, e.g., some numerical features have few values (better encoded categorically) and different features have different value distributions needing different scalers. The authors propose allowing a specific pipeline per feature, investigating: *Can we improve performance by enabling feature-wise preprocessing pipeline search?* Two challenges: (1) the search space grows exponentially (|P|^D); (2) many pipelines are invalid (e.g., a mean imputer on strings).

### 2 Problem Formulation
Defines the personalized preprocessing pipeline search problem: for each of D features choose a pipeline from a set P of possible preprocessing pipelines, to maximize downstream model performance; the joint space is |P|^D.

### 3 Methodology
**ClusterP3S** key idea: learn feature clusters and share one pipeline within each cluster, reducing |P|^D to |P|^C (C clusters, C ≪ D). A hierarchical search:
- **Upper level:** a deep clustering network groups features; trained with reinforcement learning to produce clusterings that admit good pipelines.
- **Lower level:** given a cluster assignment, random search finds the best pipeline per cluster.
The two levels are optimized jointly so clustering and pipeline search co-adapt; invalid pipelines are handled within the search.

### 4 Experiments
On benchmark classification datasets, ClusterP3S (feature-wise, clustered search) significantly outperforms baselines that apply a single shared pipeline to all numerical features, demonstrating that feature-wise preprocessing search is effective and that clustering makes the exponential search tractable.

### 5 Related Work
Reviews AutoML preprocessing search spaces (Auto-Sklearn, AlphaD3M, etc.), deep clustering, and reinforcement-learning-based search, positioning ClusterP3S as the first to enable personalized, feature-wise preprocessing pipeline search via learned clustering.

### 6 Conclusions
Enabling feature-wise preprocessing pipeline search can significantly improve performance; ClusterP3S jointly learns clusters and searches pipelines via an RL-trained deep clustering network. The insight of learning feature clusters can motivate future AutoML system designs.

### 7 Limitations and Broader Impact
The work only searches preprocessing pipelines; combining with model selection and HPO could improve further. Future directions include fairness-aware preprocessing search and more interpretable search. No negative societal impact is identified.
