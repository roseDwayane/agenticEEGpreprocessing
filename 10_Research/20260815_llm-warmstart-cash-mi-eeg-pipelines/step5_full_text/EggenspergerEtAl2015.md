---
citation_key: "EggenspergerEtAl2015"
title: "Efficient Benchmarking of Hyperparameter Optimizers via Surrogates"
authors: "Katharina Eggensperger; Frank Hutter; Holger H. Hoos; Kevin Leyton‐Brown"
year: 2015
doi: "10.1609/aaai.v29i1.9375"
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# Efficient Benchmarking of Hyperparameter Optimizers via Surrogates

**Authors**: Katharina Eggensperger, Frank Hutter, Holger H. Hoos, Kevin Leyton‐Brown
**Year**: 2015
**DOI**: 10.1609/aaai.v29i1.9375

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://doi.org/10.1609/aaai.v29i1.9375
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

Hyperparameter optimization is crucial for achieving peak performance with many machine learning algorithms; however, the evaluation of new optimization techniques on real-world hyperparameter optimization problems can be very expensive. Therefore, experiments are often performed using cheap synthetic test functions with characteristics rather different from those of real benchmarks of interest. In this work, we introduce another option: cheap-to-evaluate surrogates of real hyperparameter optimization benchmarks that share the same hyperparameter spaces and feature similar response surfaces. Specifically, we train regression models on data describing a machine learning algorithm’s performance depending on its hyperparameter setting, and then cheaply evaluate hyperparameter optimization methods using the model’s performance predictions in lieu of running the real algorithm. We evaluated a wide range of regression techniques, both in terms of how well they predict the performance of new hyperparameter settings and in terms of the quality of surrogate benchmarks obtained. We found that tree-based models capture the performance of several machine learning algorithms well and yield surrogate benchmarks that closely resemble real-world benchmarks, while being much easier to use and orders of magnitude cheaper to evaluate.
