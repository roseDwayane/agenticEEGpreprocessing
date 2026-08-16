---
citation_key: "Chakrabarty2022"
title: "Optimizing Closed-Loop Performance with Data from Similar Systems: A Bayesian Meta-Learning Approach"
authors: "Ankush Chakrabarty"
year: 2022
doi: ""
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# Optimizing Closed-Loop Performance with Data from Similar Systems: A Bayesian Meta-Learning Approach

**Authors**: Ankush Chakrabarty
**Year**: 2022

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://arxiv.org/abs/2211.00077
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

Bayesian optimization (BO) has demonstrated potential for optimizing control performance in data-limited settings, especially for systems with unknown dynamics or unmodeled performance objectives. The BO algorithm efficiently trades-off exploration and exploitation by leveraging uncertainty estimates using surrogate models. These surrogates are usually learned using data collected from the target dynamical system to be optimized. Intuitively, the convergence rate of BO is better for surrogate models that can accurately predict the target system performance. In classical BO, initial surrogate models are constructed using very limited data points, and therefore rarely yield accurate predictions of system performance. In this paper, we propose the use of meta-learning to generate an initial surrogate model based on data collected from performance optimization tasks performed on a variety of systems that are different to the target system. To this end, we employ deep kernel networks (DKNs) which are simple to train and which comprise encoded Gaussian process models that integrate seamlessly with classical BO. The effectiveness of our proposed DKN-BO approach for speeding up control system performance optimization is demonstrated using a well-studied nonlinear system with unknown dynamics and an unmodeled performance function.
