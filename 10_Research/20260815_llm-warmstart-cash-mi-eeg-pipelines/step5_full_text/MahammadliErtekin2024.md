---
citation_key: "MahammadliErtekin2024"
title: "Sequential Large Language Model-Based Hyper-parameter Optimization"
authors: "Kanan Mahammadli; Seyda Ertekin"
year: 2024
doi: ""
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# Sequential Large Language Model-Based Hyper-parameter Optimization

**Authors**: Kanan Mahammadli, Seyda Ertekin
**Year**: 2024

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://arxiv.org/abs/2410.20302
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

This study introduces SLLMBO, an innovative framework leveraging large language models (LLMs) for hyperparameter optimization (HPO), incorporating dynamic search space adaptability, enhanced parameter space exploitation, and a novel LLM-tree-structured parzen estimator (LLM-TPE) sampler. By addressing limitations in recent fully LLM-based methods and traditional bayesian optimization (BO), SLLMBO achieves more robust optimization. This comprehensive benchmarking evaluates multiple LLMs, including GPT-3.5-Turbo, GPT-4o, Claude-Sonnet-3.5, and Gemini-1.5-Flash, extending prior work and establishing SLLMBO as the first framework to benchmark a diverse set of LLMs for HPO. By integrating LLMs' established strengths in parameter initialization with the exploitation abilities demonstrated in this study, alongside TPE's exploration capabilities, the LLM-TPE sampler achieves a balanced exploration-exploitation trade-off, reduces API costs, and mitigates premature early stoppings for more effective parameter searches. Across 14 tabular tasks in classification and regression, the LLM-TPE sampler outperformed fully LLM-based methods and achieved superior results over BO methods in 9 tasks. Testing early stopping in budget-constrained scenarios demonstrated competitive performance, indicating that LLM-based methods generally benefit from extended iterations for optimal results. This work lays the foundation for future research exploring open-source LLMs, reproducibility of LLM results in HPO, and benchmarking SLLMBO on complex datasets, such as image classification, segmentation, and machine translation.
