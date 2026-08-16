---
citation_key: "PatelEtAl2025"
title: "Distilling and exploiting quantitative insights from Large Language Models for enhanced Bayesian optimization of chemical reactions"
authors: "Roshan Patel; Saeed Moayedpour; Louis De Lescure; Lorenzo Kogler-Anele; Alan Cherney; Sven Jager; Yasser Jangjou"
year: 2025
doi: ""
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# Distilling and exploiting quantitative insights from Large Language Models for enhanced Bayesian optimization of chemical reactions

**Authors**: Roshan Patel, Saeed Moayedpour, Louis De Lescure, Lorenzo Kogler-Anele, Alan Cherney, Sven Jager, Yasser Jangjou
**Year**: 2025

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://arxiv.org/abs/2504.08874
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

Machine learning and Bayesian optimization (BO) algorithms can significantly accelerate the optimization of chemical reactions. Transfer learning can bolster the effectiveness of BO algorithms in low-data regimes by leveraging pre-existing chemical information or data outside the direct optimization task (i.e., source data). Large language models (LLMs) have demonstrated that chemical information present in foundation training data can give them utility for processing chemical data. Furthermore, they can be augmented with and help synthesize potentially multiple modalities of source chemical data germane to the optimization task. In this work, we examine how chemical information from LLMs can be elicited and used for transfer learning to accelerate the BO of reaction conditions to maximize yield. Specifically, we show that a survey-like prompting scheme and preference learning can be used to infer a utility function which models prior chemical information embedded in LLMs over a chemical parameter space; we find that the utility function shows modest correlation to true experimental measurements (yield) over the parameter space despite operating in a zero-shot setting. Furthermore, we show that the utility function can be leveraged to focus BO efforts in promising regions of the parameter space, improving the yield of the initial BO query and enhancing optimization in 4 of the 6 datasets studied. Overall, we view this work as a step towards bridging the gap between the chemistry knowledge embedded in LLMs and the capabilities of principled BO methods to accelerate reaction optimization.
