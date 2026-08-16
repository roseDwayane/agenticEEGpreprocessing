---
citation_key: "LiEtAl2024"
title: "Efficient Automatic Tuning for Data-driven Model Predictive Control via Meta-Learning"
authors: "Baoyu Li; William Edwards; Kris Hauser"
year: 2024
doi: ""
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# Efficient Automatic Tuning for Data-driven Model Predictive Control via Meta-Learning

**Authors**: Baoyu Li, William Edwards, Kris Hauser
**Year**: 2024

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://arxiv.org/abs/2404.00232
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

AutoMPC is a Python package that automates and optimizes data-driven model predictive control. However, it can be computationally expensive and unstable when exploring large search spaces using pure Bayesian Optimization (BO). To address these issues, this paper proposes to employ a meta-learning approach called Portfolio that improves AutoMPC's efficiency and stability by warmstarting BO. Portfolio optimizes initial designs for BO using a diverse set of configurations from previous tasks and stabilizes the tuning process by fixing initial configurations instead of selecting them randomly. Experimental results demonstrate that Portfolio outperforms the pure BO in finding desirable solutions for AutoMPC within limited computational resources on 11 nonlinear control simulation benchmarks and 1 physical underwater soft robot dataset.
