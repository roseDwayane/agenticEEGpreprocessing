---
citation_key: "BakerEtAl2016"
title: "Designing Neural Network Architectures using Reinforcement Learning"
authors: "Bowen Baker; Otkrist Gupta; Nikhil Naik; Ramesh Raskar"
year: 2016
doi: "10.48550/arxiv.1611.02167"
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# Designing Neural Network Architectures using Reinforcement Learning

**Authors**: Bowen Baker, Otkrist Gupta, Nikhil Naik, Ramesh Raskar
**Year**: 2016
**DOI**: 10.48550/arxiv.1611.02167

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://doi.org/10.48550/arxiv.1611.02167
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

At present, designing convolutional neural network (CNN) architectures requires both human expertise and labor. New architectures are handcrafted by careful experimentation or modified from a handful of existing networks. We introduce MetaQNN, a meta-modeling algorithm based on reinforcement learning to automatically generate high-performing CNN architectures for a given learning task. The learning agent is trained to sequentially choose CNN layers using $Q$-learning with an $ε$-greedy exploration strategy and experience replay. The agent explores a large but finite space of possible architectures and iteratively discovers designs with improved performance on the learning task. On image classification benchmarks, the agent-designed networks (consisting of only standard convolution, pooling, and fully-connected layers) beat existing networks designed with the same layer types and are competitive against the state-of-the-art methods that use more complex layer types. We also outperform existing meta-modeling approaches for network design on image classification tasks.
