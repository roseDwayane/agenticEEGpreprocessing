---
citation_key: "ZhuEtAl2025"
title: "MDH-NAS: Accelerating EEG Signal Classification With Mixed-Level Differentiable and Hardware-Aware Neural Architecture Search"
authors: "Lixian Zhu; Su Wang; Xiaokun Jin; Kai Zheng; Jian Zhang; Shuting Sun; Fuze Tian; Ran Cai; Bin Hu"
year: 2025
doi: "10.1109/jiot.2025.3553450"
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# MDH-NAS: Accelerating EEG Signal Classification With Mixed-Level Differentiable and Hardware-Aware Neural Architecture Search

**Authors**: Lixian Zhu, Su Wang, Xiaokun Jin, Kai Zheng, Jian Zhang, Shuting Sun, Fuze Tian, Ran Cai, Bin Hu
**Year**: 2025
**DOI**: 10.1109/jiot.2025.3553450

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://doi.org/10.1109/jiot.2025.3553450
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

In noninvasive brain-computer interfaces (BCIs), EEG analysis plays a critical role, with neural networks serving as a cornerstone for signal decoding. Existing neural network approaches for EEG signal recognition require extensive manual design and hyperparameter tuning, leading to inefficiencies and making them impractical for embedded devices due to their large model size. To address these limitations, we propose mixed-level differentiable and hardware-aware neural architecture search (MDH-NAS), a framework that automatically generates lightweight neural networks tailored for EEG classification. Unlike traditional DARTS methods, MDH-NAS employs a hybrid optimization strategy that balances global and local search spaces, thereby accelerating and refining architecture discovery. It introduces explicit size constraints during the search process to ensure deployability on embedded devices. MDH-NAS demonstrates autonomous generation of architectures for tasks such as motor imagery (MI) and depression recognition, achieving 87.80% accuracy on the BCI-IV dataset and 90.09% on the MODMA dataset. When deployed on the EAIDK-610 board across heterogeneous tasks, it attains 85.37% accuracy on the EEG Motor Movement/Imagery dataset. This method reduces architecture discovery time by 89% and enhances prediction accuracy by 8.70% compared to baseline methods, highlighting its potential for scalable EEG analysis and real-world embedded deployment.
