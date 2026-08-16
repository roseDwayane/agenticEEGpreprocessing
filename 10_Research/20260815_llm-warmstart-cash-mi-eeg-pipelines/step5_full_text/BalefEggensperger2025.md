---
citation_key: "BalefEggensperger2025"
title: "In-Context Decision Making for Optimizing Complex AutoML Pipelines"
authors: "Amir Rezaei Balef; Katharina Eggensperger"
year: 2025
doi: ""
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# In-Context Decision Making for Optimizing Complex AutoML Pipelines

**Authors**: Amir Rezaei Balef, Katharina Eggensperger
**Year**: 2025

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://arxiv.org/abs/2508.13657
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

Combined Algorithm Selection and Hyperparameter Optimization (CASH) has been fundamental to traditional AutoML systems. However, with the advancements of pre-trained models, modern ML workflows go beyond hyperparameter optimization and often require fine-tuning, ensembling, and other adaptation techniques. While the core challenge of identifying the best-performing model for a downstream task remains, the increasing heterogeneity of ML pipelines demands novel AutoML approaches. This work extends the CASH framework to select and adapt modern ML pipelines. We propose PS-PFN to efficiently explore and exploit adapting ML pipelines by extending Posterior Sampling (PS) to the max k-armed bandit problem setup. PS-PFN leverages prior-data fitted networks (PFNs) to efficiently estimate the posterior distribution of the maximal value via in-context learning. We show how to extend this method to consider varying costs of pulling arms and to use different PFNs to model reward distributions individually per arm. Experimental results on one novel and two existing standard benchmark tasks demonstrate the superior performance of PS-PFN compared to other bandit and AutoML strategies. We make our code and data available at https://github.com/amirbalef/CASHPlus.
