---
citation_key: "XuEtAl2025a"
title: "EvoContext: Evolving Contextual Examples by Genetic Algorithm for Enhanced Hyperparameter Optimization Capability in Large Language Models"
authors: "Yutian Xu; Guozhong Qin; Yanhao Wang; Panfeng Chen; Xibin Wang; Wei Zhou; Mei Chen; Hui Li"
year: 2025
doi: "10.3390/electronics14112253"
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# EvoContext: Evolving Contextual Examples by Genetic Algorithm for Enhanced Hyperparameter Optimization Capability in Large Language Models

**Authors**: Yutian Xu, Guozhong Qin, Yanhao Wang, Panfeng Chen, Xibin Wang, Wei Zhou, Mei Chen, Hui Li
**Year**: 2025
**DOI**: 10.3390/electronics14112253

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://doi.org/10.3390/electronics14112253
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

Hyperparameter Optimization (HPO) is an important and challenging problem in machine learning. Traditional HPO methods require substantial evaluations to search for superior configurations. Recent Large Language Model (LLM)-based approaches leverage domain knowledge and few-shot learning proficiency to discover promising configurations with minimal human effort. However, the repetition issues causes LLMs to generate configurations similar to context examples, which may confine the optimization process to local regions. Moreover, since LLMs rely on the examples they generate for a few-shot learning, a self-reinforcing loop is formed, hindering LLMs from escaping local optima. In this work, we propose EvoContext, which aims to intentionally generate configurations that differ significantly from examples via external interventions and actively breaks the self-reinforcing effect for a more efficient approximation of the global optimum. Our EvoContext method involves two phases: (i) initial example generation through cold or warm starting and (ii) iterative optimization that integrates genetic operations for updating examples to enhance global exploration capabilities. Additionally, it employs LLMs in-context learning to generate configurations based on competitive examples for local refinement. Experiments on several real-world datasets show that EvoContext outperforms traditional and other LLM-driven approaches on HPO.
