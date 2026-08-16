---
citation_key: "PfistererEtAl2018"
title: "Learning multiple defaults for machine learning algorithms"
authors: "Florian Pfisterer; J. V. Rijn; Philipp Probst; Andreas Müller; B. Bischl"
year: 2018
doi: "10.1145/3449726.3459523"
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# Learning multiple defaults for machine learning algorithms

**Authors**: Florian Pfisterer, J. V. Rijn, Philipp Probst, Andreas Müller, B. Bischl
**Year**: 2018
**DOI**: 10.1145/3449726.3459523

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://doi.org/10.1145/3449726.3459523
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

Modern machine learning methods highly depend on their hyper-parameter configurations for optimal performance. A widely used approach to selecting a configuration is using default settings, often proposed along with the publication of a new algorithm. Those default values are usually chosen in an ad-hoc manner to work on a wide variety of datasets. Different automatic hyperparameter configuration algorithms which select an optimal configuration per dataset have been proposed, but despite its importance, tuning is often skipped in applications because of additional run time, complexity, and experimental design questions. Instead, the learner is often applied in its defaults. This principled approach usually improves performance but adds additional algorithmic complexity and computational costs to the training procedure. We propose and study using a set of complementary default values, learned from a large database of prior empirical results as an alternative. Selecting an appropriate configuration on a new dataset then requires only a simple, efficient, and embarrassingly parallel search over this set. To demonstrate the effectiveness and efficiency of the approach, we compare learned sets of configurations to random search and Bayesian optimization. We show that sets of defaults can improve performance while being easy to deploy in comparison to more complex methods.
