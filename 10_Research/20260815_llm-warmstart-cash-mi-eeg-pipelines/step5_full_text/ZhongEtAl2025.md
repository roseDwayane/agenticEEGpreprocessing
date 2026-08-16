---
citation_key: "ZhongEtAl2025"
title: "Dual-Level Cross-Modality Neural Architecture Search for Guided Image Super-Resolution"
authors: "Zhiwei Zhong; Xianming Liu; Junjun Jiang; Debin Zhao; Shiqi Wang"
year: 2025
doi: "10.1109/tpami.2025.3578468"
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# Dual-Level Cross-Modality Neural Architecture Search for Guided Image Super-Resolution

**Authors**: Zhiwei Zhong, Xianming Liu, Junjun Jiang, Debin Zhao, Shiqi Wang
**Year**: 2025
**DOI**: 10.1109/tpami.2025.3578468

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://doi.org/10.1109/tpami.2025.3578468
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

Guided image super-resolution (GISR) aims to reconstruct a high-resolution (HR) target image from its low-resolution (LR) counterpart with the guidance of a HR image from another modality. Existing learning-based methods typically employ symmetric two-stream networks to extract features from both the guidance and target images, and then fuse these features at either an early or late stage through manually designed modules to facilitate joint inference. Despite significant performance, these methods still face several issues: i) the symmetric architectures treat images from different modalities equally, which may overlook the inherent differences between them; ii) lower-level features contain detailed information while higher-level features capture semantic structures. However, determining which layers should be fused and which fusion operations should be selected remain unresolved; iii) most methods achieve performance gains at the cost of increased computational complexity, so balancing the trade-off between computational complexity and model performance remains a critical issue. To address these issues, we propose a Dual-level Cross-modality Neural Architecture Search (DCNAS) framework to automatically design efficient GISR models. Specifically, we propose a dual-level search space that enables the NAS algorithm to identify effective architectures and optimal fusion strategies. Moreover, we propose a supernet training strategy that employs a pairwise ranking loss trained performance predictor to guide the supernet training process. To the best of our knowledge, this is the first attempt to introduce the NAS algorithm into GISR tasks. Extensive experiments demonstrate that the discovered model family, DCNAS-Tiny and DCNAS, achieve significant improvements on several GISR tasks, including guided depth map super-resolution, guided saliency map super-resolution, guided thermal image super-resolution, and pan-sharpening. Furthermore, we analyze the architectures searched by our method and provide some new insights for future research.
