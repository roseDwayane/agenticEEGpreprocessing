---
citation_key: "SaadallahEtAl2026"
title: "A Meta-Knowledge-Augmented LLM Framework for Hyperparameter Optimization in Time-Series Forecasting"
authors: "Ons Saadallah; Mátyás Andó; Tamás Orosz"
year: 2026
doi: "10.48550/arxiv.2602.01445"
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# A Meta-Knowledge-Augmented LLM Framework for Hyperparameter Optimization in Time-Series Forecasting

**Authors**: Ons Saadallah, Mátyás Andó, Tamás Orosz
**Year**: 2026
**DOI**: 10.48550/arxiv.2602.01445

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://doi.org/10.48550/arxiv.2602.01445
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

Hyperparameter optimization (HPO) plays a central role in the performance of deep learning models, yet remains computationally expensive and difficult to interpret, particularly for time-series forecasting. While Bayesian Optimization (BO) is a standard approach, it typically treats tuning tasks independently and provides limited insight into its decisions. Recent advances in large language models (LLMs) offer new opportunities to incorporate structured prior knowledge and reasoning into optimization pipelines. We introduce LLM-AutoOpt, a hybrid HPO framework that combines BO with LLM-based contextual reasoning. The framework encodes dataset meta-features, model descriptions, historical optimization outcomes, and target objectives as structured meta-knowledge within LLM prompts, using BO to initialize the search and mitigate cold-start effects. This design enables context-aware and stable hyperparameter refinement while exposing the reasoning behind optimization decisions. Experiments on a multivariate time series forecasting benchmark demonstrate that LLM-AutoOpt achieves improved predictive performance and more interpretable optimization behavior compared to BO and LLM baselines without meta-knowledge.
