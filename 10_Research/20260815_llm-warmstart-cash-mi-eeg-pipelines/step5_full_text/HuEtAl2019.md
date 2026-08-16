---
citation_key: "HuEtAl2019"
title: "Cascaded Algorithm-Selection and Hyper-Parameter Optimization with Extreme-Region Upper Confidence Bound Bandit"
authors: "Yi-Qi Hu; Yang Yu; Jun-Da Liao"
year: 2019
doi: ""
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# Cascaded Algorithm-Selection and Hyper-Parameter Optimization with Extreme-Region Upper Confidence Bound Bandit

**Authors**: Yi-Qi Hu, Yang Yu, Jun-Da Liao
**Year**: 2019

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://arxiv.org/abs/1905.13703
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

An automatic machine learning (AutoML) task is to select the best algorithm and its hyper-parameters simultaneously. Previously, the hyper-parameters of all algorithms are joint as a single search space, which is not only huge but also redundant, because many dimensions of hyper-parameters are irrelevant with the selected algorithms. In this paper, we propose a cascaded approach for algorithm selection and hyper-parameter optimization. While a search procedure is employed at the level of hyper-parameter optimization, a bandit strategy runs at the level of algorithm selection to allocate the budget based on the search feedbacks. Since the bandit is required to select the algorithm with the maximum performance, instead of the average performance, we thus propose the extreme-region upper confidence bound (ER-UCB) strategy, which focuses on the extreme region of the underlying feedback distribution. We show theoretically that the ER-UCB has a regret upper bound $O\left(K \ln n\right)$ with independent feedbacks, which is as efficient as the classical UCB bandit. We also conduct experiments on a synthetic problem as well as a set of AutoML tasks. The results verify the effectiveness of the proposed method.
