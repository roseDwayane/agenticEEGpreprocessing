---
citation_key: "YuanEtAl2026"
title: "Unleashing LLMs in Bayesian Optimization: Preference-Guided Framework for Scientific Discovery"
authors: "Xinzhe Yuan; Zhuo Chen; Jianshu Zhang; Huan Xiong; Nanyang Ye; Yuqiang Li; Qinying Gu"
year: 2026
doi: "10.48550/arxiv.2605.17976"
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# Unleashing LLMs in Bayesian Optimization: Preference-Guided Framework for Scientific Discovery

**Authors**: Xinzhe Yuan, Zhuo Chen, Jianshu Zhang, Huan Xiong, Nanyang Ye, Yuqiang Li, Qinying Gu
**Year**: 2026
**DOI**: 10.48550/arxiv.2605.17976

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://doi.org/10.48550/arxiv.2605.17976
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

Scientific discovery is increasingly constrained by costly experiments and limited resources, underscoring the need for efficient optimization in AI for science. Bayesian Optimization (BO), though widely adopted for balancing exploration and exploitation, often exhibits slow cold-start performance and poor scalability in high-dimensional settings, limiting its applicability in real-world scientific problems. To overcome these challenges, we propose LLM-Guided Bayesian Optimization (LGBO), the first LLM preference-guided BO framework that continuously integrates the semantic reasoning of large language models (LLMs) into the optimization loop. Unlike prior works that use LLMs only for warm-start initialization or candidate generation, LGBO introduces a region-lifted preference mechanism that embeds LLM-driven preferences into every iteration, shifting the surrogate mean in a stable and controllable way. Theoretically, we prove that LGBO does not perform significantly worse than standard BO in the worst case, while achieving significantly faster convergence when preferences align with the objective. Empirically, LGBO consistently outperforms existing methods across diverse dry benchmarks in physics, chemistry, biology, and materials science. Most notably, in a new wet-lab optimization of Fe-Cr battery electrolytes, LGBO attains \textbf{90\% of the best observed value within 6 iterations}, whereas standard BO and existing LLM-augmented baselines require more than 10. Together, these results suggest that LGBO offers a promising direction for integrating LLMs into scientific optimization workflows.
