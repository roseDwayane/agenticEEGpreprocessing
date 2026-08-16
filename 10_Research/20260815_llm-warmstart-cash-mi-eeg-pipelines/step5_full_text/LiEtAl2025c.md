---
citation_key: "LiEtAl2025c"
title: "LLaMEA-BO: A Large Language Model Evolutionary Algorithm for Automatically Generating Bayesian Optimization Algorithms"
authors: "Wenhu Li; Niki van Stein; Thomas Bäck; Elena Raponi"
year: 2025
doi: ""
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# LLaMEA-BO: A Large Language Model Evolutionary Algorithm for Automatically Generating Bayesian Optimization Algorithms

**Authors**: Wenhu Li, Niki van Stein, Thomas Bäck, Elena Raponi
**Year**: 2025

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://arxiv.org/abs/2505.21034
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

Bayesian optimization (BO) is a powerful class of algorithms for optimizing expensive black-box functions, but designing effective BO algorithms remains a manual, expertise-driven task. Recent advancements in Large Language Models (LLMs) have opened new avenues for automating scientific discovery, including the automatic design of optimization algorithms. While prior work has used LLMs within optimization loops or to generate non-BO algorithms, we tackle a new challenge: Using LLMs to automatically generate full BO algorithm code. Our framework uses an evolution strategy to guide an LLM in generating Python code that preserves the key components of BO algorithms: An initial design, a surrogate model, and an acquisition function. The LLM is prompted to produce multiple candidate algorithms, which are evaluated on the established Black-Box Optimization Benchmarking (BBOB) test suite from the COmparing Continuous Optimizers (COCO) platform. Based on their performance, top candidates are selected, combined, and mutated via controlled prompt variations, enabling iterative refinement. Despite no additional fine-tuning, the LLM-generated algorithms outperform state-of-the-art BO baselines in 19 (out of 24) BBOB functions in dimension 5 and generalize well to higher dimensions, and different tasks (from the Bayesmark framework). This work demonstrates that LLMs can serve as algorithmic co-designers, offering a new paradigm for automating BO development and accelerating the discovery of novel algorithmic combinations. The source code is provided at https://github.com/Ewendawi/LLaMEA-BO.
