---
citation_key: "KristiadiEtAl2024"
title: "A Sober Look at LLMs for Material Discovery: Are They Actually Good for Bayesian Optimization Over Molecules?"
authors: "Agustinus Kristiadi; Felix Strieth-Kalthoff; Marta Skreta; Pascal Poupart; Alán Aspuru-Guzik; Geoff Pleiss"
year: 2024
doi: ""
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# A Sober Look at LLMs for Material Discovery: Are They Actually Good for Bayesian Optimization Over Molecules?

**Authors**: Agustinus Kristiadi, Felix Strieth-Kalthoff, Marta Skreta, Pascal Poupart, Alán Aspuru-Guzik, Geoff Pleiss
**Year**: 2024

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://arxiv.org/abs/2402.05015
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

Automation is one of the cornerstones of contemporary material discovery. Bayesian optimization (BO) is an essential part of such workflows, enabling scientists to leverage prior domain knowledge into efficient exploration of a large molecular space. While such prior knowledge can take many forms, there has been significant fanfare around the ancillary scientific knowledge encapsulated in large language models (LLMs). However, existing work thus far has only explored LLMs for heuristic materials searches. Indeed, recent work obtains the uncertainty estimate -- an integral part of BO -- from point-estimated, non-Bayesian LLMs. In this work, we study the question of whether LLMs are actually useful to accelerate principled Bayesian optimization in the molecular space. We take a sober, dispassionate stance in answering this question. This is done by carefully (i) viewing LLMs as fixed feature extractors for standard but principled BO surrogate models and by (ii) leveraging parameter-efficient finetuning methods and Bayesian neural networks to obtain the posterior of the LLM surrogate. Our extensive experiments with real-world chemistry problems show that LLMs can be useful for BO over molecules, but only if they have been pretrained or finetuned with domain-specific data.
