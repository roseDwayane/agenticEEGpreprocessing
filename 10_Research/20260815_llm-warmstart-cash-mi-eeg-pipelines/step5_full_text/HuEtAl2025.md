---
citation_key: "HuEtAl2025"
title: "LM-Searcher: Cross-domain Neural Architecture Search with LLMs via Unified Numerical Encoding"
authors: "Yuxuan Hu; Jihao Liu; Ke Wang; Jinliang Zhen; Weikang Shi; Manyuan Zhang; Q. Dou; Rui Liu; Aojun Zhou; Hongsheng Li"
year: 2025
doi: "10.48550/arxiv.2509.05657"
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# LM-Searcher: Cross-domain Neural Architecture Search with LLMs via Unified Numerical Encoding

**Authors**: Yuxuan Hu, Jihao Liu, Ke Wang, Jinliang Zhen, Weikang Shi, Manyuan Zhang, Q. Dou, Rui Liu, Aojun Zhou, Hongsheng Li
**Year**: 2025
**DOI**: 10.48550/arxiv.2509.05657

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://doi.org/10.48550/arxiv.2509.05657
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

Recent progress in Large Language Models (LLMs) has opened new avenues for solving complex optimization problems, including Neural Architecture Search (NAS). However, existing LLM-driven NAS approaches rely heavily on prompt engineering and domain-specific tuning, limiting their practicality and scalability across diverse tasks. In this work, we propose LM-Searcher, a novel framework that leverages LLMs for cross-domain neural architecture optimization without the need for extensive domain-specific adaptation. Central to our approach is NCode, a universal numerical string representation for neural architectures, which enables cross-domain architecture encoding and search. We also reformulate the NAS problem as a ranking task, training LLMs to select high-performing architectures from candidate pools using instruction-tuning samples derived from a novel pruning-based subspace sampling strategy. Our curated dataset, encompassing a wide range of architecture-performance pairs, encourages robust and transferable learning. Comprehensive experiments demonstrate that LM-Searcher achieves competitive performance in both in-domain (e.g., CNNs for image classification) and out-of-domain (e.g., LoRA configurations for segmentation and generation) tasks, establishing a new paradigm for flexible and generalizable LLM-based architecture search. The datasets and models will be released at https://github.com/Ashone3/LM-Searcher.
