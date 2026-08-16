---
citation_key: "DingEtAl2024"
title: "EmT: A Novel Transformer for Generalized Cross-Subject EEG Emotion Recognition"
authors: "Yi Ding; Chengxuan Tong; Shuailei Zhang; Muyun Jiang; Yong Li; K. Lim; Cuntai Guan"
year: 2024
doi: "10.1109/tnnls.2025.3552603"
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# EmT: A Novel Transformer for Generalized Cross-Subject EEG Emotion Recognition

**Authors**: Yi Ding, Chengxuan Tong, Shuailei Zhang, Muyun Jiang, Yong Li, K. Lim, Cuntai Guan
**Year**: 2024
**DOI**: 10.1109/tnnls.2025.3552603

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://doi.org/10.1109/tnnls.2025.3552603
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

Integrating prior knowledge of neurophysiology into neural network architecture enhances the performance of emotion decoding. While numerous techniques emphasize learning spatial and short-term temporal patterns, there has been a limited emphasis on capturing the vital long-term contextual information associated with emotional cognitive processes. In order to address this discrepancy, we introduce a novel transformer model called emotion transformer (EmT). EmT is designed to excel in both generalized cross-subject electroencephalography (EEG) emotion classification and regression tasks. In EmT, EEG signals are transformed into a temporal graph format, creating a sequence of EEG feature graphs using a temporal graph construction (TGC) module. A novel residual multiview pyramid graph convolutional neural network (RMPG) module is then proposed to learn dynamic graph representations for each EEG feature graph within the series, and the learned representations of each graph are fused into one token. Furthermore, we design a temporal contextual transformer (TCT) module with two types of token mixers to learn the temporal contextual information. Finally, the task-specific output (TSO) module generates the desired outputs. Experiments on four publicly available datasets show that EmT achieves higher results than the baseline methods for both EEG emotion classification and regression tasks. The code is available at https://github.com/yi-ding-cs/EmT.
