---
citation_key: "MenetEtAl2025"
title: "Thompson Sampling via Fine-Tuning of LLMs"
authors: "Nicolas Menet; Aleksandar Terzić; Michael Hersche; Andreas Krause; Abbas Rahimi"
year: 2025
doi: ""
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# Thompson Sampling via Fine-Tuning of LLMs

**Authors**: Nicolas Menet, Aleksandar Terzić, Michael Hersche, Andreas Krause, Abbas Rahimi
**Year**: 2025

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://arxiv.org/abs/2510.13328
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

Bayesian optimization in large unstructured discrete spaces is often hindered by the computational cost of maximizing acquisition functions due to the absence of gradients. We propose a scalable alternative based on Thompson sampling that eliminates the need for acquisition function maximization by directly parameterizing the probability that a candidate yields the maximum reward. Our approach, Thompson Sampling via Fine-Tuning (ToSFiT) leverages the prior knowledge embedded in prompt-conditioned large language models, and incrementally adapts them toward the posterior. Theoretically, we derive a novel regret bound for a variational formulation of Thompson Sampling that matches the strong guarantees of its standard counterpart. Our analysis reveals the critical role of careful adaptation to the posterior probability of maximality -- a principle that underpins our ToSFiT algorithm. Empirically, we validate our method on three diverse tasks: FAQ response refinement, thermally stable protein search, and quantum circuit design. Within a collection of methods covering in-context Bayesian optimization, reinforcement learning, and evolutionary search, ToSFiT exhibits both state-of-the-art sample efficiency and computational efficiency.
