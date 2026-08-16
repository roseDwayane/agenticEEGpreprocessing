---
citation_key: "LiEtAl2020b"
title: "MFES-HB: Efficient Hyperband with Multi-Fidelity Quality Measurements"
authors: "Yang Li; Yu Shen; Jiawei Jiang; Jinyang Gao; Ce Zhang; B. Cui"
year: 2020
doi: "10.1609/aaai.v35i10.17031"
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# MFES-HB: Efficient Hyperband with Multi-Fidelity Quality Measurements

**Authors**: Yang Li, Yu Shen, Jiawei Jiang, Jinyang Gao, Ce Zhang, B. Cui
**Year**: 2020
**DOI**: 10.1609/aaai.v35i10.17031

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://doi.org/10.1609/aaai.v35i10.17031
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

Hyperparameter optimization (HPO) is a fundamental problem in automatic machine learning (AutoML).
However, due to the expensive evaluation cost of models (e.g., training deep learning models or training models on large datasets), vanilla Bayesian optimization (BO) is typically computationally infeasible. 
To alleviate this issue, Hyperband (HB) utilizes the early stopping mechanism to speed up configuration evaluations by terminating those badly-performing configurations in advance.
This leads to two kinds of quality measurements:
(1) many low-fidelity measurements for configurations that get early-stopped, and (2) few high-fidelity measurements for configurations that are evaluated without being early stopped.
The state-of-the-art HB-style method, BOHB, aims to combine the benefits of both BO and HB. 
Instead of sampling configurations randomly in HB, BOHB samples configurations based on a BO surrogate model, which is constructed with the high-fidelity measurements only.
However, the scarcity of high-fidelity measurements greatly hampers the efficiency of BO to guide the configuration search.

In this paper, we present MFES-HB, an efficient Hyperband method that is capable of utilizing both the high-fidelity and low-fidelity measurements to accelerate the convergence of HPO tasks.
Designing MFES-HB is not trivial as the low-fidelity measurements can be biased yet informative to guide the configuration search.
Thus we propose to build a Multi-Fidelity Ensemble Surrogate (MFES) based on the generalized Product of Experts framework, which can integrate useful information from multi-fidelity measurements effectively.
The empirical studies on the real-world AutoML tasks demonstrate that MFES-HB can achieve 3.3-8.9x speedups over the state-of-the-art approach --- BOHB.
