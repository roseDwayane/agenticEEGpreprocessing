---
citation_key: "LiEtAl2025b"
title: "Causal-aware Graph Neural Architecture Search under Distribution Shifts"
authors: "Peiwen Li; Xin Wang; Zeyang Zhang; Yi Qin; Ziwei Zhang; Jialong Wang; Yang Li; Wenwu Zhu"
year: 2025
doi: "10.1145/3711896.3736873"
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# Causal-aware Graph Neural Architecture Search under Distribution Shifts

**Authors**: Peiwen Li, Xin Wang, Zeyang Zhang, Yi Qin, Ziwei Zhang, Jialong Wang, Yang Li, Wenwu Zhu
**Year**: 2025
**DOI**: 10.1145/3711896.3736873

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://doi.org/10.1145/3711896.3736873
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

Graph neural architecture search (NAS) has emerged as a promising approach for autonomously designing graph neural network architectures by leveraging correlations between graphs and architectures. However, existing methods merely rely on correlations, which may be spurious and vary across distributions. This reliance, without considering causal graph-architecture relationships, limits their ability to generalize under distribution shifts that are ubiquitous in real-world graph scenarios. In this paper, we propose to handle the distribution shifts in NAS process by exploiting the causal graph-architecture relationship to search for optimal architectures that can generalize under distribution shifts. Key challenges remain unexplored: discovering causal graph-architecture relationships with stable cross-distribution predictive abilities, and leveraging them to handle distribution shifts. To address these challenges, we propose a novel approach, Causal-aware Graph Neural Architecture Search (CARNAS), which is capable of capturing causal graph-architecture relationship during NAS process and discovering optimal graph architecture under distribution shifts. We propose Disentangled Causal Subgraph Identification to extract causal subgraphs with stable predictive power across distributions, followed by Graph Embedding Intervention to intervene on these subgraphs in latent space by preserving essential features while filtering out non-causal elements, and Invariant Architecture Customization to enhance their causal invariance for optimizing graph architectures. Extensive experiments on synthetic and real-world datasets show that CARNAS enhances out-of-distribution generalization by uncovering causal graph-architecture relationships during NAS.
