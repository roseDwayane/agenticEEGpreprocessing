---
citation_key: "BirdEtAl2020"
title: "Cross-Domain MLP and CNN Transfer Learning for Biological Signal Processing: EEG and EMG"
authors: "Jordan J. Bird; Jhonatan Kobylarz; Diego R. Faria; Anikó Ekárt; Eduardo Parente Ribeiro"
year: 2020
doi: "10.1109/access.2020.2979074"
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# Cross-Domain MLP and CNN Transfer Learning for Biological Signal Processing: EEG and EMG

**Authors**: Jordan J. Bird, Jhonatan Kobylarz, Diego R. Faria, Anikó Ekárt, Eduardo Parente Ribeiro
**Year**: 2020
**DOI**: 10.1109/access.2020.2979074

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://doi.org/10.1109/access.2020.2979074
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

In this work, we show the success of unsupervised transfer learning between Electroencephalographic (brainwave) classification and Electromyographic (muscular wave) domains with both MLP and CNN methods. To achieve this, signals are measured from both the brain and forearm muscles and EMG data is gathered from a 4-class gesture classification experiment via the Myo Armband, and a 3-class mental state EEG dataset is acquired via the Muse EEG Headband. A hyperheuristic multi-objective evolutionary search method is used to find the best network hyperparameters. We then use this optimised topology of deep neural network to classify both EMG and EEG signals, attaining results of 84.76% and 62.37% accuracy, respectively. Next, when pre-trained weights from the EMG classification model are used for initial distribution rather than random weight initialisation for EEG classification, 93.82%(+29.95) accuracy is reached. When EEG pre-trained weights are used for initial weight distribution for EMG, 85.12% (+0.36) accuracy is achieved. When the EMG network attempts to classify EEG, it outperforms the EEG network even without any training (+30.25% to 82.39% at epoch 0), and similarly the EEG network attempting to classify EMG data outperforms the EMG network (+2.38% at epoch 0). All transfer networks achieve higher pre-training abilities, curves, and asymptotes, indicating that knowledge transfer is possible between the two signal domains. In a second experiment with CNN transfer learning, the same datasets are projected as 2D images and the same learning process is carried out. In the CNN experiment, EMG to EEG transfer learning is found to be successful but not vice-versa, although EEG to EMG transfer learning did exhibit a higher starting classification accuracy. The significance of this work is due to the successful transfer of ability between models trained on two different biological signal domains, reducing the need for building more computationally complex models in future research.
