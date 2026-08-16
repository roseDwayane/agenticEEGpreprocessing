---
citation_key: "XuEtAl2026b"
title: "Tree-Structured Synergy of Large Language Models and Bayesian Optimization for Efficient CASH"
authors: "Beicheng Xu; Wei Qian; Lingching Tung; Yupeng Lu; Bin Cui"
year: 2026
doi: ""
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# Tree-Structured Synergy of Large Language Models and Bayesian Optimization for Efficient CASH

**Authors**: Beicheng Xu, Wei Qian, Lingching Tung, Yupeng Lu, Bin Cui
**Year**: 2026

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://www.semanticscholar.org/paper/773ba305b07a4c2af76423f51dbcb30586fc9c96
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

To lower the expertise barrier in machine learning, the AutoML community has focused on the CASH problem, which jointly automates algorithm selection and hyperparameter tuning. While traditional methods like Bayesian Optimization (BO) struggle with cold-start issues, Large Language Models (LLMs) can mitigate these through semantic priors. However, existing LLM-based optimizers generalize poorly to high-dimensional, structured CASH spaces. In this paper, we propose LB-MCTS, a trajectory-structured optimization framework that uses a Monte Carlo Tree Search tree as a shared state for algorithm selection, hyperparameter refinement, and BO-LLM proposer synergy. Within this shared state, BO provides algorithm-specific surrogate modeling for quantitative search, while the LLM exploits path-aware selective memory to generate semantic proposals and reflections. As the surrogate model improves, a reliability-aware proposer policy adaptively shifts from LLM-driven to BO-driven proposals within a unified search trajectory. Experiments on 104 AMLB datasets demonstrate that LB-MCTS consistently outperforms BO-based, LLM-based, and hybrid baselines.
