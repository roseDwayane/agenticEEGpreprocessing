---
citation_key: "LeiCooper2026"
title: "Elicitation Matters: How Prompts and Query Protocols Shape LLM Surrogates under Sparse Observations"
authors: "Ge Lei; Samuel J. Cooper"
year: 2026
doi: ""
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# Elicitation Matters: How Prompts and Query Protocols Shape LLM Surrogates under Sparse Observations

**Authors**: Ge Lei, Samuel J. Cooper
**Year**: 2026

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://arxiv.org/abs/2605.04764
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

Large language models are increasingly used as surrogate models for low-data optimization, but their optimizer-facing prediction and its uncertainty remain poorly understood. We study the surrogate belief elicited from an LLM under sparse observations, showing that it depends strongly on prompt text and query protocol. We introduce an uncertainty-alignment criterion that measures whether model uncertainty tracks residual ambiguity among sample-consistent functions. Across controlled inference tasks and Bayesian optimization studies, we find that structural prompts act as effective priors, POINTWISE and JOINT querying induce different beliefs, and sequential evidence leads to non-monotonic, order-sensitive confidence updates. These effects change downstream acquisition decisions and regret, showing that elicitation protocol is part of the LLM surrogate specification, not a formatting detail.
