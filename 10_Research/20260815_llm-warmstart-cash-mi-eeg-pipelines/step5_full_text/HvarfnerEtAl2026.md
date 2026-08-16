---
citation_key: "HvarfnerEtAl2026"
title: "Pitfalls and Remedies for Multi-Task Bayesian Optimization"
authors: "Carl Hvarfner; Sam Daulton; Max Balandat; Eytan Bakshy"
year: 2026
doi: ""
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# Pitfalls and Remedies for Multi-Task Bayesian Optimization

**Authors**: Carl Hvarfner, Sam Daulton, Max Balandat, Eytan Bakshy
**Year**: 2026

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://arxiv.org/abs/2607.09073
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

Bayesian optimization routinely warm-starts a target experiment with data from related source tasks, and the multi-task Gaussian process is the textbook surrogate for the job. We revisit this default in a controlled setting and find that it misestimates the cross-task correlation even in the simplest non-trivial case, affinely related source and target tasks, where a working transfer learning method should obviously succeed. We trace the failure to two independent structural mechanisms. Per-task standardization, the textbook fix for the affine slice ambiguity, propagates a finite-sample alignment error into the recovered correlation. The marginal likelihood itself identifies the correlation only at a per-sample rate that a Gaussian process at non-overlapping designs further dilutes. We propose three conservative remedies that follow from the analysis: promoting per-task means and scales to model parameters, restricting the task covariance to non-negative correlations, and co-locating part of the source and target designs. Across synthetic multi-task problems and surrogate-based hyperparameter tuning transfer, these remedies recover the target-only baseline on the simple instances, while the broader failure persists on harder instances and across most rank-based and latent-context variants.
