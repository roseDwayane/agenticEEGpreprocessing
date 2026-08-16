---
citation_key: "GijsbersEtAl2021"
title: "Meta-learning for symbolic hyperparameter defaults"
authors: "P. Gijsbers; Florian Pfisterer; J. V. Rijn; B. Bischl; J. Vanschoren"
year: 2021
doi: "10.1145/3449726.3459532"
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# Meta-learning for symbolic hyperparameter defaults

**Authors**: P. Gijsbers, Florian Pfisterer, J. V. Rijn, B. Bischl, J. Vanschoren
**Year**: 2021
**DOI**: 10.1145/3449726.3459532

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://doi.org/10.1145/3449726.3459532
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

Hyperparameter optimization in machine learning (ML) deals with the problem of empirically learning an optimal algorithm configuration from data, usually formulated as a black-box optimization problem. In this work, we propose a zero-shot method to meta-learn symbolic default hyperparameter configurations that are expressed in terms of the properties of the dataset. This enables a much faster, but still data-dependent, configuration of the ML algorithm, compared to standard hyperparameter optimization approaches. In the past, symbolic and static default values have usually been obtained as hand-crafted heuristics. We propose an approach of learning such symbolic configurations as formulas of dataset properties from a large set of prior evaluations on multiple datasets by optimizing over a grammar of expressions using an evolutionary algorithm. We evaluate our method on surrogate empirical performance models as well as on real data across 6 ML algorithms on more than 100 datasets and demonstrate that our method indeed finds viable symbolic defaults.
