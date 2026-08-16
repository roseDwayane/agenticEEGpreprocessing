---
citation_key: "BerdyshevEtAl2024"
title: "EEG-Reptile: An Automatized Reptile-Based Meta-Learning Library for BCIs"
authors: "Daniil A. Berdyshev; Artem M. Grachev; Sergei L. Shishkin; Bogdan L. Kozyrskiy"
year: 2024
doi: ""
source: "arXiv (2412.19725)"
access_level: "full-text-pdf"
retrieved_date: "2026-08-15"
arxiv_id: "2412.19725"
conversion: "pdftotext -layout (automated)"
language: "bilingual-en-zh"
---

# EEG-Reptile: An Automatized Reptile-Based Meta-Learning Library for BCIs | EEG-Reptile：基於 Reptile 的自動化 BCI 元學習函式庫

> [!abstract] 重點摘要
> - 提出 EEG-Reptile：一個高度自動化的元學習（meta-learning）函式庫，運用 Reptile 演算法將 EEG 神經網路分類器適應至跨受試者（inter-subject）領域，使新受試者僅需極少資料即可有效微調（fine-tuning）。
> - 函式庫包含四大模組：資料儲存（Data Storage）、超參數搜尋（Hyperparameter Search，基於 Optuna）、元學習（Meta-Learning）與微調（Fine-Tuning），自動化程度高，使用者無需深入理解元學習。
> - 提出接近 RANSAC 精神的權重初始化程序，可自動偵測並排除與群體平均差異過大的「離群」受試者（比例由 γ 控制），提升元訓練品質。
> - 對 EEGNet 提出分層優化（Opt-EEGNet）：將空間特徵萃取層與分類層分為兩組，允許獨立學習率與獨立元學習係數 β1、β2。
> - 在 BCI IV 2a（四類）與 Lee2019 MI（二類）兩個基準資料集及三種網路架構（EEGNet、FBCNet、EEG-Inception）上驗證：零樣本（zero-shot）與少樣本（few-shot）情境下，EEGNet 搭配元學習顯著優於傳統遷移學習基線（p < 0.05，Wilcoxon 符號等級檢定）。
> - BCI IV 2a 零樣本準確率達 43% ± 7%，以 16 個資料點微調後達 46% ± 5%；Lee2019 MI 零樣本達 71% ± 5%，微調後達 72% ± 7%。

---

## Abstract | 摘要

> [!quote] Original
> Meta-learning, i.e., "learning to learn", is a promising approach to enable efficient BCI classifier training with limited amounts of data. It can effectively use collections of in some way similar classification tasks, with rapid adaptation to new tasks where only minimal data are available. However, applying meta-learning to existing classifiers and BCI tasks requires significant effort. To address this issue, we propose EEG-Reptile, an automated library that leverages meta-learning to improve classification accuracy of neural networks in BCIs and other EEG-based applications. It utilizes the Reptile meta-learning algorithm to adapt neural network classifiers of EEG data to the inter-subject domain, allowing for more efficient fine-tuning for a new subject on a small amount of data. The proposed library incorporates an automated hyperparameter tuning module, a data management pipeline, and an implementation of the Reptile meta-learning algorithm. EEG-Reptile automation level allows using it without deep understanding of meta-learning. We demonstrate the effectiveness of EEG-Reptile on two benchmark datasets (BCI IV 2a, Lee2019 MI) and three neural network architectures (EEGNet, FBCNet, EEG-Inception). Our library achieved improvement in both zero-shot and few-shot learning scenarios compared to traditional transfer learning approaches.

> [!note] 翻譯
> 元學習（meta-learning），即「學習如何學習」，是在資料量有限的條件下實現高效 BCI 分類器訓練的一種有前景的方法。它能有效利用在某種意義上相似的分類任務集合，並在僅有極少資料的新任務上快速適應。然而，將元學習應用於既有分類器與 BCI 任務需要相當可觀的工作量。為解決此問題，我們提出 EEG-Reptile：一個自動化函式庫，利用元學習來提升神經網路在 BCI 及其他基於 EEG 之應用中的分類準確率。它運用 Reptile 元學習演算法，將 EEG 資料的神經網路分類器適應至跨受試者（inter-subject）領域，使新受試者能以少量資料進行更有效率的微調（fine-tuning）。所提函式庫整合了自動化超參數調校模組、資料管理管線，以及 Reptile 元學習演算法的實作。EEG-Reptile 的自動化程度使其無需深入理解元學習即可使用。我們在兩個基準資料集（BCI IV 2a、Lee2019 MI）與三種神經網路架構（EEGNet、FBCNet、EEG-Inception）上展示了 EEG-Reptile 的有效性。相較於傳統遷移學習（transfer learning）方法，本函式庫在零樣本（zero-shot）與少樣本（few-shot）學習情境中均取得改善。

---

## 1. Introduction | 引言

> [!quote] Original
> Brain-Computer Interfaces (BCIs) enable direct communication between the brain and external devices by translating neural activity into commands, bypassing neuromuscular pathways (Wolpaw et al. 2002). By allowing control of prosthetic limbs, exoskeletons, or computer interfaces through neural signals, BCIs can restore movement and autonomy, improving rehabilitation and quality of life. In particular, electroencephalography (EEG) based noninvasive BCIs employing motor imagery (MI) are increasingly used in neurorehabilitation, particularly for post-stroke patients, where they may facilitate motor function recovery via neuroplasticity (Daly and Wolpaw 2008; Murphy and Corbett 2009; Frolov et al. 2017; Cervera et al. 2018; Mane, Chouhan, et al. 2020; Mane, Z. Wu, et al. 2022). Users of the MI BCIs imagine specific movements without actual muscle activity, generating neural patterns that can be decoded into control commands.
>
> The effectiveness of an EEG based BCI greatly depends on performance of its core computational part, a classifier, which translates patterns of the recorded signals into commands. A BCI classifier needs to be trained on its user's individual data or at least fine-tuned to it, due to great intersubject variability of the neural data. This variability stems primarily from individual differences in brain anatomy and function. Even higher variability is observed among post-stroke patients, as stroke-induced damage alters neural signals in different ways. Moreover, different electrode positioning, recovering or pathological processes, slow fluctuations of a patient's physiological state and sources of artifacts often make necessary classifier re-calibration even on daily basis. However, the amount of training data which can be obtained from a single user, especially in a single session, is limited. For a range of practical reasons, especially in patients with significant disability, it is highly desirable to make procedures of training data collection as short as possible or even exclude them completely.

> [!note] 翻譯
> 腦機介面（Brain-Computer Interfaces, BCIs）透過將神經活動轉譯為指令，繞過神經肌肉路徑，實現大腦與外部裝置之間的直接溝通（Wolpaw et al. 2002）。藉由以神經訊號控制義肢、外骨骼或電腦介面，BCI 能夠恢復行動能力與自主性，改善復健成效與生活品質。特別是採用運動想像（motor imagery, MI）的非侵入式腦電圖（EEG）BCI，日益廣泛地應用於神經復健——尤其針對中風後患者——可能透過神經可塑性（neuroplasticity）促進運動功能的恢復（Daly and Wolpaw 2008；Murphy and Corbett 2009；Frolov et al. 2017；Cervera et al. 2018；Mane, Chouhan, et al. 2020；Mane, Z. Wu, et al. 2022）。MI BCI 的使用者在沒有實際肌肉活動的情況下想像特定動作，產生可被解碼為控制指令的神經模式。
>
> 基於 EEG 的 BCI 的有效性，在很大程度上取決於其核心計算部分——分類器——的效能；分類器負責將記錄訊號的模式轉譯為指令。由於神經資料存在巨大的受試者間變異性（intersubject variability），BCI 分類器需要在其使用者的個人資料上訓練，或至少針對其微調。此變異性主要源於腦部解剖構造與功能的個體差異。中風後患者的變異性甚至更高，因為中風造成的損傷以不同方式改變神經訊號。此外，不同的電極擺放位置、恢復或病理過程、患者生理狀態的緩慢波動以及偽跡來源，往往使分類器必須重新校正，甚至需每日進行。然而，可從單一使用者——尤其在單一場次（session）內——取得的訓練資料量是有限的。基於一系列實務理由，特別是對於重度失能的患者，將訓練資料蒐集程序盡可能縮短、甚至完全省略，是非常值得追求的目標。

---

> [!quote] Original
> To address these issues, various transfer-learning techniques are being applied to EEG-based BCIs (Azab et al. 2018, Huang et al. 2022, X. Duan et al. 2023, M. Li and Xu 2024). The core idea of transfer learning is to find common features between various user sessions and to perform classification based on these features (D. Wu et al. 2022). However, achieving this requires a large and relatively homogeneous dataset to effectively extract these shared features (Azab et al. 2018, Guetschel et al. 2024). In addition, the extraction of these features often requires the modification of the original classifier, which prevents the building of more automated solutions (Cai and Hong 2024).
>
> In contrast, meta-learning focuses on learning how to learn by leveraging a still large dataset, but consisting of smaller, sometimes disparate tasks. This approach enables rapid adaptation to entirely new tasks from minimal data. More broadly, meta-learning (Schmidhuber 1987, Thrun and Pratt 1998), also referred to as "learning to learn," involves training models on a range of tasks or datasets so that they can quickly adapt to novel tasks with minimal additional data.
>
> Consequently, we find a meta-learning approach more suitable for BCI problems. The available pre-training data often comes from numerous distinct BCI users, each with only a small amount of data, and can thus be treated as separate tasks. Instead of searching for common features across all these users, which may not exist at all, it is more promising to train the classifier to quickly adapt to each new user.

> [!note] 翻譯
> 為解決這些問題，各種遷移學習技術正被應用於基於 EEG 的 BCI（Azab et al. 2018、Huang et al. 2022、X. Duan et al. 2023、M. Li and Xu 2024）。遷移學習的核心思想是在不同使用者場次之間尋找共同特徵，並基於這些特徵進行分類（D. Wu et al. 2022）。然而，要達成這一點，需要大型且相對同質的資料集，才能有效萃取這些共享特徵（Azab et al. 2018、Guetschel et al. 2024）。此外，這些特徵的萃取往往需要修改原始分類器，因而阻礙了更自動化解決方案的建構（Cai and Hong 2024）。
>
> 相對地，元學習著眼於「學習如何學習」：它同樣利用大型資料集，但該資料集由較小、有時彼此差異頗大的任務組成。此方法能以極少資料快速適應全新的任務。更廣泛而言，元學習（Schmidhuber 1987、Thrun and Pratt 1998），亦稱「學習如何學習」（learning to learn），是指在一系列任務或資料集上訓練模型，使其能以最少的額外資料快速適應新任務。
>
> 因此，我們認為元學習方法更適合 BCI 問題。可用的預訓練資料往往來自為數眾多、彼此不同的 BCI 使用者，每人僅有少量資料，因而可視為各自獨立的任務。與其在所有使用者之間搜尋可能根本不存在的共同特徵，訓練分類器使其能快速適應每一位新使用者，是更有前景的做法。

---

> [!quote] Original
> Being more specific, meta-learning approach provides an initialization scheme for neural network parameters, that allows to adapt to new BCI users using very small amount of data. This initialization scheme is constructed using data from other users. This approach can:
>
> - Enhance Adaptability: Enable models to generalize across different users and sessions by capturing common patterns in EEG data.
> - Reduce Calibration Time: Minimize the per-user data required for effective model tuning.
> - Improve Performance in Variable Conditions: Handle variability due to physiological differences or neural alterations.
>
> However, some patients exhibit neural patterns that significantly deviate from the average due to unique physiological or pathological conditions. Even with high-quality EEG data, their brain activity may not align with common patterns learned during meta-training. Including such outlier data in the meta-learning pool can degrade the model's generalization ability. Therefore, it is crucial to detect and handle these atypical patients separately, ensuring that the model is trained using representative data while developing methods to adapt to individual differences during personalization.

> [!note] 翻譯
> 更具體地說，元學習方法為神經網路參數提供一種初始化方案，使模型能以極少量資料適應新的 BCI 使用者。此初始化方案係利用其他使用者的資料建構而成。此方法可以：
>
> - 增強適應性：藉由捕捉 EEG 資料中的共同模式，使模型能在不同使用者與場次間泛化。
> - 縮短校正時間：將有效模型調校所需的每位使用者資料量降至最低。
> - 改善變動條件下的效能：處理因生理差異或神經改變所致的變異性。
>
> 然而，部分患者因獨特的生理或病理狀況，其神經模式與平均值有顯著偏離。即使 EEG 資料品質良好，其腦活動也可能與元訓練期間所學得的共同模式不相符。將此類離群（outlier）資料納入元學習資料池，可能損害模型的泛化能力。因此，關鍵在於分別偵測並處理這些非典型患者，確保模型以具代表性的資料訓練，同時發展在個人化階段適應個體差異的方法。

---

> [!quote] Original
> Despite previous efforts (X. Wu and Chan 2022, T. Duan et al. 2020) to apply meta-learning algorithms for BCIs, the focus on reducing the size of training datasets has been limited. Specifically, Reptile (Nichol et al. 2018) and Model-Agnostic Meta-Learning (MAML) (Finn et al. 2017) have not been explored in scenarios where minimal EEG data is used for fine-tuning or zero-shot learning. Notably, previous studies on MAML (T. Duan et al. 2020) involved moderate reductions in dataset size, while other approach lost model agnostic nature (Tremmel et al. 2022, Han et al. 2024). We propose that the application of general-purpose meta-learning algorithms will enable us to reduce the amount of training data, needed for adaptation of the neural network for a new subject.
>
> Recent studies have demonstrated approaches for inter-subject adaptation of neural network classifiers for EEG data. In a recent study (Ng and Guan 2024), researchers employed extensively redesigned MAML like meta-learning algorithm, achieving promising results in few-shot and zero-shot scenarios. However, this approach involved a more complex meta-learning procedure, which hindered the adoption of new neural network architectures for this method. Furthermore, hyperparameter optimization for the meta-learning algorithm was not automated. Meta-learning library presented in our work enables automatic hyperparameter tuning for meta-learning, seamless integration of new neural network architectures. It can be applied with the amount of data available in public datasets, and it is less computationally expensive compared to foundation models and Self-Supervised Learning (SSL) approaches.

> [!note] 翻譯
> 儘管先前已有將元學習演算法應用於 BCI 的嘗試（X. Wu and Chan 2022、T. Duan et al. 2020），針對縮減訓練資料集規模的著墨仍屬有限。具體而言，Reptile（Nichol et al. 2018）與模型無關元學習（Model-Agnostic Meta-Learning, MAML）（Finn et al. 2017）尚未在「以極少 EEG 資料進行微調或零樣本學習」的情境中被探索。值得注意的是，先前關於 MAML 的研究（T. Duan et al. 2020）僅涉及資料集規模的中度縮減，而其他方法則喪失了模型無關（model agnostic）的特性（Tremmel et al. 2022、Han et al. 2024）。我們主張，應用通用型元學習演算法將使我們得以減少神經網路適應新受試者所需的訓練資料量。
>
> 近期研究已展示 EEG 資料神經網路分類器的跨受試者適應方法。在一項近期研究（Ng and Guan 2024）中，研究者採用經大幅重新設計、類 MAML 的元學習演算法，在少樣本與零樣本情境中取得可觀成果。然而，該方法涉及較複雜的元學習程序，妨礙了新神經網路架構對此方法的採用。此外，該元學習演算法的超參數最佳化並未自動化。本研究提出的元學習函式庫可為元學習自動調校超參數，並無縫整合新的神經網路架構。它能以公開資料集所提供的資料量運作，且相較於基礎模型（foundation models）與自監督學習（Self-Supervised Learning, SSL）方法，其計算成本更低。

---

> [!quote] Original
> In this study with our proposed meta-learning library EEG-Reptile, we focus on the Motor Imagery (MI) paradigm to illustrate the advantages of our approach. MI is widely used in real-world BCIs, enabling control of external devices by imagining movements. This is crucial for post-stroke rehabilitation, where MI can activate neural pathways associated with movement, promoting neuroplasticity and aiding recovery. However, MI tasks present significant challenges due to high inter-subject variability and limited per-user data. By focusing on MI data, we aim to demonstrate how meta-learning can effectively handle variability and data scarcity, leading to more adaptable and efficient EEG-based BCIs for rehabilitation purposes. Our main contributions are:
>
> - We propose a method to filter subjects for model pre-training, ensuring a coherent pre-training subset and enhancing subsequent meta-learning performance.
> - We introduce an advanced meta-learning procedure for EEGNet, a widely used neural network for EEG classification, enabling it to operate effectively in few-shot and even zero-shot training regimes.
> - We propose an approach for tuning meta-learning hyperparameters on the fly.
> - We develop a highly automated meta-learning library and demonstrate its effectiveness on several neural network architectures and EEG datasets.

> [!note] 翻譯
> 在本研究中，我們以所提出的元學習函式庫 EEG-Reptile，聚焦於運動想像（MI）典範，以說明本方法的優勢。MI 廣泛用於真實世界的 BCI，使用者可透過想像動作來控制外部裝置。這對中風後復健至關重要：MI 能激活與運動相關的神經路徑，促進神經可塑性並輔助恢復。然而，MI 任務因跨受試者變異性高、每位使用者資料有限，而構成重大挑戰。藉由聚焦 MI 資料，我們旨在展示元學習如何有效處理變異性與資料稀缺，從而為復健用途打造更具適應性、更高效的基於 EEG 之 BCI。我們的主要貢獻為：
>
> - 提出一種為模型預訓練篩選受試者的方法，確保預訓練子集的一致性，並提升後續元學習的效能。
> - 為 EEG 分類中廣泛使用的神經網路 EEGNet 引入進階的元學習程序，使其能在少樣本、甚至零樣本訓練情境中有效運作。
> - 提出一種即時（on the fly）調校元學習超參數的方法。
> - 開發一個高度自動化的元學習函式庫，並在多種神經網路架構與 EEG 資料集上展示其有效性。

---

## 2. Methods | 方法

> [!quote] Original
> In our study, we present an approach for meta-learning based on the Reptile algorithm for BCI applications. In this section, we describe used methods, machine learning models and datasets used for training. In addition, we present data preprocessing and algorithm of the proposed meta-learning method.

> [!note] 翻譯
> 在本研究中，我們提出一種基於 Reptile 演算法、面向 BCI 應用的元學習方法。本節描述所使用的方法、機器學習模型與訓練所用的資料集。此外，我們也介紹資料前處理以及所提元學習方法的演算法。

---

### 2.1 Datasets | 資料集

> [!quote] Original
> The BCI-IV (2a) (Tangermann et al. 2012) and Lee 2019 (MI) (Lee et al. 2019) datasets were used to train ML models and evaluate performance of the proposed library. The datasets were loaded using the MOABB library (Aristimunha et al. 2023). Data preprocessing was performed utilizing the Braindecode toolbox (Schirrmeister et al. 2017). Both datasets are recorded using an imaginary movement paradigm. This paradigm is interesting due to the differences in signal between users, which complicates the transfer learning process. It is also possible to use this paradigm to construct BCI systems.
>
> **2.1.1. BCI IV (2a)** The BCI iv 2a dataset (Tangermann et al. 2012) comprises electroencephalographic (EEG) recordings for 4 motor imagery tasks: right hand, left hand, both feet and tongue. The dataset consists of 22-channel EEG data from 9 subjects, recorded at a sampling rate of 250 Hz. A total of 5184 epochs are included in the dataset, 144 epochs per class for each subject. Prior to analysis, a band-pass filter (4-38 Hz) and exponential moving standardization were applied to each EEG channel. The duration of each data epoch was 4.5 seconds or 1125 measurements.
>
> **2.1.2. Lee2019 MI** The Lee2019 MI dataset (Lee et al. 2019) represents motor imagery tasks comprising two classes: right hand and left hand. The data consists of 62 EEG channels, recorded at a sampling rate of 1000 Hz. The dataset contains a total of 200 epochs per class to each of 54 subjects included in the dataset.
>
> The dataset was preprocessed to ensure compatibility with neural network architectures designed for EEG analysis. Specifically, it was downsampled to 250 Hz to reduce the dimensionality of the data. Then a band-pass filter (4-38 Hz) was applied, followed by exponential moving standardization. Each epoch has a duration of 2.5 seconds and consists of 625 time points. To minimize the dimensionality of the dataset and reduce computational requirements, we selectively retained only 20 EEG channels that have been previously identified as relevant for motor imagery tasks in the work by Lee et al. The selected channels were FC5, FC3, FC1, FC2, FC4, FC6, C5, C3, C1, Cz, C2, C4, C6, CP5, CP3, CP1, CPz, CP2, CP4, CP6.

> [!note] 翻譯
> 我們使用 BCI-IV (2a)（Tangermann et al. 2012）與 Lee2019 (MI)（Lee et al. 2019）兩個資料集來訓練機器學習模型並評估所提函式庫的效能。資料集透過 MOABB 函式庫（Aristimunha et al. 2023）載入，資料前處理則使用 Braindecode 工具箱（Schirrmeister et al. 2017）完成。兩個資料集皆以想像運動典範記錄。此典範之所以值得關注，在於使用者之間的訊號差異使遷移學習過程更加複雜；此典範亦可用於建構 BCI 系統。
>
> **2.1.1. BCI IV (2a)**　BCI IV 2a 資料集（Tangermann et al. 2012）包含 4 種運動想像任務的腦電圖（EEG）記錄：右手、左手、雙腳與舌頭。資料集由 9 位受試者的 22 通道 EEG 資料組成，取樣率為 250 Hz。資料集共含 5184 個資料段（epochs），每位受試者每類 144 段。分析之前，對每個 EEG 通道施加帶通濾波（4–38 Hz）與指數移動標準化（exponential moving standardization）。每個資料段長 4.5 秒，即 1125 個量測點。
>
> **2.1.2. Lee2019 MI**　Lee2019 MI 資料集（Lee et al. 2019）為包含兩類的運動想像任務：右手與左手。資料由 62 個 EEG 通道組成，取樣率為 1000 Hz。資料集中 54 位受試者每人每類共 200 個資料段。
>
> 為確保與針對 EEG 分析設計的神經網路架構相容，我們對該資料集進行了前處理。具體而言，先降取樣至 250 Hz 以降低資料維度，接著施加帶通濾波（4–38 Hz），再進行指數移動標準化。每個資料段長 2.5 秒，包含 625 個時間點。為最小化資料集維度並降低計算需求，我們僅選擇性保留 Lee et al. 先前已確認與運動想像任務相關的 20 個 EEG 通道，即 FC5、FC3、FC1、FC2、FC4、FC6、C5、C3、C1、Cz、C2、C4、C6、CP5、CP3、CP1、CPz、CP2、CP4、CP6。

---

### 2.2 Models | 模型

> [!quote] Original
> In this study, we present a meta-learning approach, which can apply meta-learning to any neural network trained with techniques similar to Stochastic Gradient Descent (SGD). To evaluate the efficacy of the proposed approach, we employed three distinct neural networks commonly used for EEG signal classification in MI tasks: EEGNet (Lawhern et al. 2018), EEG-Inception (Motor Imagery) (C. Zhang et al. 2021), and FBCNet (Mane, Chew, et al. 2021). These networks were selected for their ability to effectively address the challenges of EEG-based brain-computer interfaces.
>
> EEGNet is a compact convolutional neural network designed specifically for EEG-based brain-computer interfaces. Inspired by the work of Vernon J. Lawhern et al., our revised architecture consists of two distinct groups of layers: spatial feature extractors and classifiers. These groups are separated to facilitate independent training and application of different learning rates, allowing for more efficient optimization. EEGNet effectively encapsulates well-known EEG feature extraction concepts for brain-computer interfaces, leveraging depthwise and separable convolutions to achieve high performance across various BCI paradigms, including P300 visual-evoked potentials, error-related negativity responses (ERN), movement-related cortical potentials (MRCP), and sensory motor rhythms (SMR).
>
> Two other neural networks used in our study were employed in their original form. EEG-Inception (MI), a convolutional neural network architecture designed for accurate and robust classification of EEG-based motor imagery (MI). This network was sourced from the Braindecode Python library (Schirrmeister et al. 2017). FBCNet, a novel Filter-Bank Convolutional Network proposed in R. Mane et al. study, which was obtained from the TorchEEG python library (Z. Zhang et al. 2024).

> [!note] 翻譯
> 在本研究中，我們提出的元學習方法可應用於任何以類似隨機梯度下降（Stochastic Gradient Descent, SGD）技術訓練的神經網路。為評估所提方法的成效，我們採用了三種常用於 MI 任務 EEG 訊號分類的神經網路：EEGNet（Lawhern et al. 2018）、EEG-Inception（Motor Imagery）（C. Zhang et al. 2021）與 FBCNet（Mane, Chew, et al. 2021）。選擇這些網路，是因其能有效因應基於 EEG 之腦機介面的挑戰。
>
> EEGNet 是專為基於 EEG 之腦機介面設計的精簡卷積神經網路。受 Vernon J. Lawhern 等人工作的啟發，我們修訂後的架構由兩組不同的層組成：空間特徵萃取器與分類器。將這兩組分離，有助於獨立訓練並施用不同的學習率，從而實現更高效的最佳化。EEGNet 有效封裝了腦機介面領域廣為人知的 EEG 特徵萃取概念，利用深度卷積（depthwise convolution）與可分離卷積（separable convolution），在多種 BCI 典範中取得高效能，包括 P300 視覺誘發電位、錯誤相關負波（error-related negativity, ERN）、運動相關皮質電位（movement-related cortical potentials, MRCP）與感覺運動節律（sensorimotor rhythms, SMR）。
>
> 本研究使用的另外兩個神經網路則採用其原始形式。EEG-Inception (MI) 是為精確且魯棒地分類基於 EEG 之運動想像（MI）而設計的卷積神經網路架構，取自 Braindecode Python 函式庫（Schirrmeister et al. 2017）。FBCNet 是 R. Mane 等人研究中提出的新型濾波器組卷積網路（Filter-Bank Convolutional Network），取自 TorchEEG Python 函式庫（Z. Zhang et al. 2024）。

---

### 2.3 Reptile meta-learning algorithm | Reptile 元學習演算法

> [!quote] Original
> We utilize the Reptile meta-learning algorithm to ensure robustness across different neural network architectures. This algorithm belongs to the class of Model Agnostic Meta-Learning algorithms (Finn et al. 2017), which are applicable to any networks trained using gradient descent. The Reptile algorithm (Nichol et al. 2018) optimizes the initial weights θ of a neural network f(θ) over a distribution of tasks p(T). It adjusts the weights to move closer to a point in the weight space that is approximately equidistant from the optimal weights for each task during the current training step. In the context of EEG data, we treat classification problems for individual BCI users as separate tasks. Consequently, during meta-training, Reptile updates the initial weights of the neural network using an averaged difference between the initial weights θ and user-specific weights θ′ᵢ.
>
> Our implementation of Reptile (Alg. 1) introduces notable features:
>
> - multiple learning rate coefficients β1 & β2 for distinct groups of layers within the neural network (layers responsible for processing EEG-specific features θ1 vs. those responsible for final classification θ2).
> - flexibility to utilize any optimizer, such as Adam, to update weights during meta-training.
>
> These features enable customized optimization strategies for different parts of the network, making model weight updates more efficient during meta-training.

> [!note] 翻譯
> 我們採用 Reptile 元學習演算法，以確保在不同神經網路架構間的魯棒性。此演算法屬於模型無關元學習（Model Agnostic Meta-Learning）演算法類別（Finn et al. 2017），適用於任何以梯度下降訓練的網路。Reptile 演算法（Nichol et al. 2018）在任務分布 p(T) 上最佳化神經網路 f(θ) 的初始權重 θ。它調整權重，使其在權重空間中趨近一個與當前訓練步驟中各任務最佳權重約略等距的點。在 EEG 資料的脈絡下，我們將個別 BCI 使用者的分類問題視為各自獨立的任務。因此，在元訓練期間，Reptile 使用初始權重 θ 與使用者特定權重 θ′ᵢ 之間的平均差來更新神經網路的初始權重。
>
> 我們的 Reptile 實作（演算法 1）引入了以下顯著特點：
>
> - 為神經網路中不同的層群組設置多個學習率係數 β1 與 β2（負責處理 EEG 特定特徵的層 θ1，相對於負責最終分類的層 θ2）。
> - 可彈性採用任何最佳化器（如 Adam）在元訓練期間更新權重。
>
> 這些特點使網路的不同部分得以採用客製化的最佳化策略，令元訓練期間的模型權重更新更有效率。

---

> [!quote] Original
> **Algorithm 1 — Reptile algorithm**
> Require: p(T): distribution over tasks; α, β: meta step size hyperparameters; M: number of meta-learning epochs
> Initialize the initial parameter vector θ and cleaned p′(T) using the Algorithm 2
> 1: for i = 0 to M do
> 2: &nbsp;&nbsp;Sample batch of tasks Tᵢ ∼ p′(T) with length N
> 3: &nbsp;&nbsp;for all Tᵢ do
> 4: &nbsp;&nbsp;&nbsp;&nbsp;Sample K data points {x⁽ᵏ⁾, y⁽ᵏ⁾} from Tᵢ
> 5: &nbsp;&nbsp;&nbsp;&nbsp;Evaluate ∇θ L_{Tᵢ}(fθ) w.r.t. K data points
> 6: &nbsp;&nbsp;&nbsp;&nbsp;θ′ᵢ = θ − α∇θ L_{Tᵢ}(fθ)
> 7: &nbsp;&nbsp;end for
> 8: &nbsp;&nbsp;if double meta weights β1 & β2 then
> 9: &nbsp;&nbsp;&nbsp;&nbsp;Update: θ1 ← θ1 + (β1/N) Σ(θ1′ᵢ − θ1)
> 10: &nbsp;&nbsp;&nbsp;Update: θ2 ← θ2 + (β2/N) Σ(θ2′ᵢ − θ2)
> 11: &nbsp;&nbsp;else
> 12: &nbsp;&nbsp;&nbsp;Update: θ ← θ + (β/N) Σ(θ′ᵢ − θ)
> 13: &nbsp;&nbsp;end if
> 14: end for
> 15: return θ or θ1 & θ2
>
> In short, the Reptile implementation in our library leverages the agnostic nature of this meta-learning algorithm and introduces adaptability through the use of multiple coefficients and optimizers, enabling effective adaptation across diverse neural network architectures and tasks.

> [!note] 翻譯
> **演算法 1——Reptile 演算法**
> 輸入：p(T)：任務分布；α、β：元步長（meta step size）超參數；M：元學習輪數（epochs）
> 使用演算法 2 初始化初始參數向量 θ 與清理後的任務分布 p′(T)
> 1：for i = 0 到 M do
> 2：　從 p′(T) 抽樣長度為 N 的任務批次 Tᵢ
> 3：　for 所有 Tᵢ do
> 4：　　從 Tᵢ 抽樣 K 個資料點 {x⁽ᵏ⁾, y⁽ᵏ⁾}
> 5：　　針對 K 個資料點計算 ∇θ L_{Tᵢ}(fθ)
> 6：　　θ′ᵢ = θ − α∇θ L_{Tᵢ}(fθ)
> 7：　end for
> 8：　if 使用雙元權重 β1 與 β2 then
> 9：　　更新：θ1 ← θ1 + (β1/N) Σ(θ1′ᵢ − θ1)
> 10：　更新：θ2 ← θ2 + (β2/N) Σ(θ2′ᵢ − θ2)
> 11：else
> 12：　更新：θ ← θ + (β/N) Σ(θ′ᵢ − θ)
> 13：end if
> 14：end for
> 15：回傳 θ 或 θ1 與 θ2
>
> 簡言之，本函式庫中的 Reptile 實作利用此元學習演算法的模型無關特性，並透過多重係數與最佳化器的使用引入適應性，使其能在多樣的神經網路架構與任務間有效適應。

---

### 2.4 EEG-Reptile Library | EEG-Reptile 函式庫

> [!quote] Original
> We introduce EEG-Reptile library, a collection of modules designed for using meta-learning with neural networks for EEG classification, handling EEG datasets necessary for meta-learning, hyperparameter tuning, and model fine-tuning. The library consists of four primary modules: Data Storage, Hyperparameter Search, Meta-Learning, and Fine-Tuning. A simplified scheme of the proposed library is shown on Figure 1.
>
> The Data Storage module facilitates the storage and retrieval of preprocessed EEG data from multiple subjects, along with their associated metadata. This module provides prepared datasets for other components within the library.
>
> The Hyperparameter Search module leverages the Optuna (Akiba et al. 2019) library to perform hyperparameter tuning for both meta-learning and fine-tuning. For meta-learning, this module searches optimal parameters, including:
>
> - Number of epochs for meta-learning.
> - Number of epochs within each meta-step.
> - Learning rate for training the base model within each meta-step.
> - Multiple meta-learning rates (β).
> - Number of data points used in each meta-step (N).
>
> For fine-tuning, this module searches optimal parameters, including:
>
> - Learning rate.
> - Linear approximation of the dependence between the number of epochs and the available data points.
>
> Linear approximation is necessary because it is impossible to know the size of the fine-tuning set for the target subject in advance and to select a large enough validation set to apply early stopping. In our case, during the selection of hyperparameters on a non-target subject that did not participate in meta-training, we select such linear approximation coefficients a and b so that mean classification quality is maximized for a different size of fine-tuning set.

> [!note] 翻譯
> 我們介紹 EEG-Reptile 函式庫：一組專為「以神經網路進行 EEG 分類時運用元學習」而設計的模組集合，負責處理元學習所需的 EEG 資料集、超參數調校與模型微調。函式庫由四個主要模組組成：資料儲存（Data Storage）、超參數搜尋（Hyperparameter Search）、元學習（Meta-Learning）與微調（Fine-Tuning）。所提函式庫的簡化架構如圖 1 所示。
>
> 資料儲存模組負責多位受試者之前處理後 EEG 資料及其關聯詮釋資料（metadata）的儲存與檢索。此模組為函式庫內的其他組件提供備妥的資料集。
>
> 超參數搜尋模組利用 Optuna（Akiba et al. 2019）函式庫，為元學習與微調兩者執行超參數調校。針對元學習，此模組搜尋的最佳參數包括：
>
> - 元學習的輪數（epochs）。
> - 每個元步驟（meta-step）內的輪數。
> - 每個元步驟內訓練基礎模型的學習率。
> - 多重元學習率（β）。
> - 每個元步驟所使用的資料點數（N）。
>
> 針對微調，此模組搜尋的最佳參數包括：
>
> - 學習率。
> - 輪數與可用資料點數之間依存關係的線性近似。
>
> 線性近似之所以必要，是因為無法事先得知目標受試者微調集的大小，也無法選出夠大的驗證集來套用提前停止（early stopping）。在我們的做法中，於未參與元訓練的非目標受試者上選擇超參數時，我們選取使「不同微調集大小下的平均分類品質」最大化的線性近似係數 a 與 b。

---

> [!quote] Original
> The Meta-Learning module facilitates meta-learning on multiple subjects in various regimes. This module initializes the model weights θ by computing the average weights θ′ of models trained for a few epochs on each subject. The proposed initialization procedure helps exclude "outlier" subjects whose models have significantly different optimal weight values from the mean of p(T). The proportion of "outliers" removed is denoted by γ (Alg. 2). The training regimes vary based on the chosen parameters for the base meta-learning algorithm and the data partitioning strategy. Furthermore, this module supports preparing a network pre-trained on the same datasets using standard Transfer Learning.
>
> The Fine-Tuning module enables additional training of a previously meta-trained model on a specific subject. It also gathers statistics on the fine-tuning process and performs testing to assess the fine-tuning performance.
>
> **Algorithm 2 — Weight Initialization Algorithm**
> Input: p(T): a distribution over N tasks, γ: outlier removal rate
> Output: θ′: final weight vector, p′(T)
> 1: Initialize θ randomly
> 2: Initialize θ′ = 0 (same shape as θ)
> 3: for each task Tᵢ in p(T) do
> 4: &nbsp;&nbsp;Train a model from θ on Tᵢ to obtain θᵢ
> 5: &nbsp;&nbsp;θ′ ← θ′ + (1/N) θᵢ
> 6: end for
> 7: for each θᵢ do
> 8: &nbsp;&nbsp;dᵢ = |mean(θ′ − θᵢ)|
> 9: end for
> 10: n = ⌊γN⌋ &nbsp;&nbsp;(Number of outliers to remove)
> 11: Obtain p′(T) by removing n tasks with the largest dᵢ from p(T)
> 12: Reset θ′ = 0
> 13: for Tᵢ in p′(T) do
> 14: &nbsp;&nbsp;θ′ ← θ′ + (1/(N−n)) θᵢ
> 15: end for
> 16: return θ′, p′(T)

> [!note] 翻譯
> 元學習模組支援在多位受試者上以多種模式進行元學習。此模組透過計算「在每位受試者上訓練數輪之模型」的平均權重 θ′ 來初始化模型權重 θ。所提出的初始化程序有助於排除「離群」受試者——其模型的最佳權重值與 p(T) 的平均值差異顯著。被移除之「離群者」的比例以 γ 表示（演算法 2）。訓練模式依基礎元學習演算法所選參數與資料切分策略而異。此外，此模組亦支援以標準遷移學習方式，在相同資料集上準備預訓練網路。
>
> 微調模組使先前經元訓練的模型得以在特定受試者上進行額外訓練。它同時蒐集微調過程的統計資訊，並執行測試以評估微調效能。
>
> **演算法 2——權重初始化演算法**
> 輸入：p(T)：N 個任務上的分布；γ：離群者移除比例
> 輸出：θ′：最終權重向量；p′(T)
> 1：隨機初始化 θ
> 2：初始化 θ′ = 0（形狀與 θ 相同）
> 3：for p(T) 中的每個任務 Tᵢ do
> 4：　從 θ 出發在 Tᵢ 上訓練模型，得到 θᵢ
> 5：　θ′ ← θ′ + (1/N) θᵢ
> 6：end for
> 7：for 每個 θᵢ do
> 8：　dᵢ = |mean(θ′ − θᵢ)|
> 9：end for
> 10：n = ⌊γN⌋　（欲移除的離群者數量）
> 11：自 p(T) 移除 dᵢ 最大的 n 個任務，得到 p′(T)
> 12：重設 θ′ = 0
> 13：for p′(T) 中的 Tᵢ do
> 14：　θ′ ← θ′ + (1/(N−n)) θᵢ
> 15：end for
> 16：回傳 θ′、p′(T)

---

### 2.5 Experimental Setup | 實驗設置

> [!quote] Original
> All experiments were conducted using the EEG-Reptile library. Prior to conducting the experiments, we randomly selected five subjects from each dataset and designated them as "test subjects." For each individual experiment, we began by removing data from one test subject in each dataset. We then performed hyperparameter optimization for meta-learning on the remaining subjects' data. After completing the hyperparameter tuning, we carried out meta-training and transfer learning, the latter serving as our baseline approach. Next, we conducted another hyperparameter search to prepare for the fine-tuning stage. Subsequently, we fine-tuned the model on the previously unseen test subject and evaluated its classification accuracy without any additional training (Zero-shot). The full experimental design is illustrated in Figure 2.
>
> The model's performance in each experiment was evaluated using accuracy. This metric was computed with varying amounts of data per class during the fine-tuning stage. Specifically, we measured accuracy as we retrained the model on different numbers of EEG data points (and their corresponding class labels) per class.
>
> Each experiment was repeated five times for each test subject, with different random subsets of training data selected in each repetition. For evaluation, we used a fixed test set composed of the last 20% of each dataset, ensuring equal amounts of data per class. This approach allowed for a fair and consistent performance comparison across all experiments.

> [!note] 翻譯
> 所有實驗皆使用 EEG-Reptile 函式庫進行。實驗開始前，我們自每個資料集隨機選取五位受試者，指定為「測試受試者」。在每一次個別實驗中，我們先自各資料集移除一位測試受試者的資料，接著在其餘受試者的資料上執行元學習的超參數最佳化。完成超參數調校後，我們進行元訓練與遷移學習——後者作為我們的基線方法。其後，我們再進行一次超參數搜尋，為微調階段做準備。隨後，我們在先前未見過的測試受試者上微調模型，並在不做任何額外訓練的情況下評估其分類準確率（零樣本，Zero-shot）。完整的實驗設計如圖 2 所示。
>
> 每次實驗中模型的效能以準確率（accuracy）評估。此指標係於微調階段以每類不同的資料量計算。具體而言，我們在以每類不同數量的 EEG 資料點（及其對應類別標籤）重新訓練模型時量測準確率。
>
> 每項實驗對每位測試受試者重複五次，每次重複均選取不同的隨機訓練資料子集。評估時，我們使用由各資料集最後 20% 組成的固定測試集，確保每類資料量相等。此做法使所有實驗之間得以進行公平且一致的效能比較。

---

## 3. Results | 結果

> [!quote] Original
> We conducted a comprehensive evaluation of classification performance on the BCI Competition IV 2a dataset (four classes) and the Lee2019 (MI) dataset (two classes). This evaluation was based on the average performance for five randomly selected test subjects from each dataset. The results are presented in Figure 3.
>
> Our experimental design ensured that each test subject remained entirely unseen during the meta-learning and zero-shot testing phases. This approach allowed us to rigorously assess the effectiveness of the machine learning methods without the influence of any fine-tuning steps.
>
> For all experiments, fine-tuning was performed on small data subsets of the target subject, with different subset sizes shown on the x-axis. Each individual subset with specified size was randomly chosen five times. Our results demonstrate that meta-learning and transfer learning-based methods exceed performance of random guessing (random guessing would give accuracy of 25% for 4 class and 50% for 2 class), even without fine-tuning on a new subject.
>
> [Figure 3: Comparison of group average Accuracy with 95% confidence intervals on BCI IV 2a (four classes) and Lee2019 (MI) (two classes) datasets for baseline algorithms and algorithms with meta-learning fine-tuned on subsets with different sizes and zero-shot.]

> [!note] 翻譯
> 我們在 BCI Competition IV 2a 資料集（四類）與 Lee2019 (MI) 資料集（二類）上，對分類效能進行了全面評估。此評估基於自每個資料集隨機選取之五位測試受試者的平均效能。結果如圖 3 所示。
>
> 我們的實驗設計確保每位測試受試者在元學習與零樣本測試階段完全未被見過。此做法使我們得以在不受任何微調步驟影響的情況下，嚴謹地評估各機器學習方法的有效性。
>
> 在所有實驗中，微調均在目標受試者的小型資料子集上進行，不同的子集大小標示於 x 軸。每個指定大小的子集均隨機選取五次。我們的結果顯示，即使未在新受試者上微調，基於元學習與遷移學習的方法均超越隨機猜測的表現（隨機猜測在四類下的準確率為 25%，二類下為 50%）。
>
> [圖 3：在 BCI IV 2a（四類）與 Lee2019 (MI)（二類）資料集上，基線演算法與搭配元學習之演算法在不同大小子集微調及零樣本情境下之組平均準確率（含 95% 信賴區間）比較。]

---

> [!quote] Original
> Our analysis shows that achieving satisfactory classification quality for EEG data from previously unseen subjects remains challenging in MI classification tasks. This finding highlights the limitations of current state-of-the-art inter-subject transfer learning methods. In particular, we observed that meta-learning approaches still fall short of the standards required for reliable integration into a BCI system, highlighting the need for further research.
>
> For the BCI IV 2a dataset, we found that meta-learning with the EEGNet model achieved an average classification accuracy of 43% ± 7% without fine-tuning (zero-shot). Notably, this result was surpassed by fine-tuning on small data subsets, which reached a peak classification accuracy of 46% ± 5% when trained on only 16 data points (4 per class).
>
> For the Lee2019 MI dataset, we observed that the highest zero-shot classification accuracy was also achieved using EEGNet with meta-learning, at 71% ± 5%. Furthermore, fine-tuning on small data subsets led to a modest improvement in classification performance, reaching 72% ± 7%, when trained on 16 data points (8 per class).

> [!note] 翻譯
> 我們的分析顯示，在 MI 分類任務中，要對先前未見過之受試者的 EEG 資料達到令人滿意的分類品質，仍具挑戰性。此發現凸顯了當前最先進跨受試者遷移學習方法的侷限。特別是，我們觀察到元學習方法距離可靠整合至 BCI 系統所需的標準仍有差距，凸顯進一步研究的必要。
>
> 就 BCI IV 2a 資料集而言，我們發現搭配 EEGNet 模型的元學習在未微調（零樣本）情況下達到 43% ± 7% 的平均分類準確率。值得注意的是，在小型資料子集上微調後超越了此結果：僅以 16 個資料點（每類 4 個）訓練時，達到 46% ± 5% 的分類準確率峰值。
>
> 就 Lee2019 MI 資料集而言，最高的零樣本分類準確率同樣由搭配元學習的 EEGNet 取得，為 71% ± 5%。此外，在小型資料子集上微調帶來分類效能的小幅提升：以 16 個資料點（每類 8 個）訓練時達到 72% ± 7%。

---

> [!quote] Original
> We present a graphical comparison (Fig. 4) illustrating the differences in average classification accuracy for each model and dataset under both meta-learning and baseline (transfer learning) conditions. For both datasets, EEGNet with meta-learning significantly outperformed the baseline algorithm (p < 0.05, Wilcoxon signed-rank test). Moreover, the observed improvement in average classification accuracy was statistically significant at the 95% confidence level.
>
> For both datasets, we also observed that when fine-tuned on small data subsets (16 data points), EEGNet with meta-learning again outperformed the baseline algorithm at a significance level of p < 0.05 and a confidence interval of 95%. This suggests that EEGNet with meta-learning is not only effective for zero-shot classification but also robust when fine-tuned on small data subsets.
>
> [Figure 4: Comparison of group average of difference for Accuracy with 95% confidence intervals on BCI IV 2a (four classes) and Lee2019 (MI) (two classes) datasets between baseline algorithms and algorithms with meta-learning fine-tuned on subsets with different sizes and zero-shot.]
>
> For other models, we found that two approaches exhibited positive differences in average classification accuracy between meta-learning and baseline algorithms, but these differences were smaller than the confidence interval of 95%. For the Lee2019 MI dataset, we found that the results for FBCNet and EEG-inception (MI) pre-trained with meta-learning were comparable to those of the baseline algorithm.

> [!note] 翻譯
> 我們以圖示比較（圖 4）呈現各模型與資料集在元學習與基線（遷移學習）兩種條件下平均分類準確率的差異。在兩個資料集上，搭配元學習的 EEGNet 均顯著優於基線演算法（p < 0.05，Wilcoxon 符號等級檢定）。此外，所觀察到的平均分類準確率提升在 95% 信心水準下具統計顯著性。
>
> 在兩個資料集上，我們也觀察到：在小型資料子集（16 個資料點）上微調時，搭配元學習的 EEGNet 再次以 p < 0.05 的顯著水準與 95% 的信賴區間優於基線演算法。這顯示搭配元學習的 EEGNet 不僅在零樣本分類上有效，在小型資料子集上微調時亦具魯棒性。
>
> [圖 4：在 BCI IV 2a（四類）與 Lee2019 (MI)（二類）資料集上，基線演算法與搭配元學習之演算法在不同大小子集微調及零樣本情境下之準確率差異的組平均（含 95% 信賴區間）比較。]
>
> 至於其他模型，我們發現有兩種方法在元學習與基線演算法之間呈現正向的平均分類準確率差異，但這些差異小於 95% 信賴區間。就 Lee2019 MI 資料集而言，以元學習預訓練的 FBCNet 與 EEG-Inception (MI) 的結果與基線演算法相當。

---

> [!quote] Original
> Given the significant difference in classification accuracy between the EEGNet architecture and the other models, we evaluated EEGNet with the proposed optimizations (hereafter referred to as Opt-EEGNet) to assess the effectiveness of these modifications. As described previously, the key distinction between the two architectures is that Opt-EEGNet separates convolutional and fully connected layers into distinct groups. This arrangement enables the use of independent learning rates during fine-tuning, the allocation of individual β coefficients for meta-learning, and even the option to freeze weights in one group while fine-tuning the other.
>
> To evaluate the effect of this modification, we performed a similar experiment using the unmodified EEGNet (see Fig. 5). The results show that separating the layers into distinct groups improves classification accuracy, even under a straightforward transfer learning approach. Notably, both EEGNet and Opt-EEGNet benefit from meta-learning, indicating that this optimization strategy can enhance the quality of inter-subject knowledge transfer and meta-learning.
>
> [Figure 5: Comparison of group average Accuracy with 95% confidence intervals on BCI IV 2a (four classes) dataset between baseline algorithms and algorithms with meta-learning applied with EEGNet optimization and without, fine-tuned on subsets with different sizes and zero-shot.]

> [!note] 翻譯
> 鑑於 EEGNet 架構與其他模型在分類準確率上的顯著差異，我們評估了施加所提優化的 EEGNet（以下稱 Opt-EEGNet），以檢驗這些修改的有效性。如前所述，兩種架構的關鍵區別在於 Opt-EEGNet 將卷積層與全連接層分為不同的組。此安排使微調期間得以使用獨立的學習率、為元學習分配個別的 β 係數，甚至可選擇凍結其中一組的權重而僅微調另一組。
>
> 為評估此修改的效果，我們使用未經修改的 EEGNet 進行了類似實驗（見圖 5）。結果顯示，即使在簡單的遷移學習方法下，將層分為不同的組亦能提升分類準確率。值得注意的是，EEGNet 與 Opt-EEGNet 皆受益於元學習，顯示此優化策略能提升跨受試者知識轉移與元學習的品質。
>
> [圖 5：在 BCI IV 2a（四類）資料集上，基線演算法與搭配元學習之演算法（分別施加與未施加 EEGNet 優化）在不同大小子集微調及零樣本情境下之組平均準確率（含 95% 信賴區間）比較。]

---

## 4. Discussion | 討論

> [!quote] Original
> The results demonstrate that the proposed meta-learning library, EEG-Reptile, can be used to improve classification performance. Furthermore, it allows relatively autonomous operation, making it an attractive solution for various applications.
>
> Experiments without fine-tuning and with fine-tuning on small datasets (zero- and few-shot) both showed higher accuracy using EEG-Reptile, particularly when paired with an optimized network. This improvement holds even for small dataset sizes, suggesting that our library can enhance classification accuracy given a sufficiently large, though not excessive, amount of training data for neural networks. This is consistent with results demonstrated in work (Berdyshev et al. 2023). There, it is shown that such outcomes are achievable using meta-learning. It is possible to achieve similar or higher accuracy improvement by utilizing EEG-Reptile library in such case.
>
> Results for optimized and not optimized EEGNet have demonstrated that such optimization could improve classification accuracy in meta-learning and transfer learning. The separation of layers into distinct groups may provide a beneficial optimization strategy for other neural architectures as well. Further research is necessary to fully explore the potential benefits of this approach and its applicability to different models and tasks.

> [!note] 翻譯
> 結果顯示，所提出的元學習函式庫 EEG-Reptile 可用於提升分類效能。此外，它允許相對自主的運作，使其成為多種應用中具吸引力的解決方案。
>
> 無微調的實驗與在小型資料集上微調的實驗（零樣本與少樣本）均顯示，使用 EEG-Reptile 可獲得較高的準確率，尤其是搭配經優化的網路時。此改善即使在資料集規模較小時依然成立，顯示只要為神經網路提供足夠大（但不必過量）的訓練資料，本函式庫即可提升分類準確率。這與（Berdyshev et al. 2023）研究中展示的結果一致——該研究顯示此類成果可透過元學習達成；在此情況下，利用 EEG-Reptile 函式庫可達到類似或更高的準確率提升。
>
> 優化與未優化 EEGNet 的結果顯示，此類優化可在元學習與遷移學習中提升分類準確率。將層分為不同的組，對其他神經架構或許也是一項有益的優化策略。要充分探索此方法的潛在效益及其對不同模型與任務的適用性，仍需進一步研究。

---

> [!quote] Original
> One of the key features of EEG-Reptile is its ability to filter and select subjects that are too different from the others, enabling automatic exclusion of overly specific subjects from the meta training set. This feature is provided by the weight initialization procedure presented in this work, which is close to the RANSAC method (Fischler and Bolles 1981). As far as the authors know, such a method has not been applied for this purpose in meta-learning approaches within the context of BCI. This capability can lead to improved mean performance in inter-subject transfer learning.
>
> A recent study (Han et al. 2024) demonstrated higher classification accuracy compared to our experiments; however, the key difference is that their models were trained on all available data for each user. Our results, on the other hand, show a significant improvement in classification performance when using a small number of EEG epochs for unseen users. This was achieved through fully automated hyperparameter optimization for meta-learning and fine-tuning. This stands in contrast to previous works (Ahuja and Sethia 2024, X. Wu and Chan 2022, J. Li et al. 2023), where researchers failed to demonstrate improved classification accuracy on small datasets with cross-subject transfer learning. Such small datasets, in turn, represent the primary use case for many practical BCI applications.
>
> One of the main challenges associated with meta-learning is the size of available datasets in the sense of number of users. Collecting more even small sessions may improve the performance of our approach. Another downside of the fully automated meta-optimization could be an extensive search for hyperparameters. In our experiments, it took approximately 24 hours with NVIDIA Tesla P100 GPU.

> [!note] 翻譯
> EEG-Reptile 的關鍵特點之一，是其能篩選並挑出與其他受試者差異過大的受試者，從而自動將過度特異的受試者排除於元訓練集之外。此功能由本研究提出的權重初始化程序提供，該程序在精神上接近 RANSAC 方法（Fischler and Bolles 1981）。就作者所知，在 BCI 脈絡下的元學習方法中，尚無以此方法用於此目的之先例。此能力可提升跨受試者遷移學習的平均效能。
>
> 一項近期研究（Han et al. 2024）展示了比我們實驗更高的分類準確率；然而，關鍵差異在於其模型係以每位使用者的全部可用資料訓練。相對地，我們的結果顯示：對未見過的使用者僅使用少量 EEG 資料段時，分類效能可獲顯著提升。這是透過元學習與微調的全自動超參數最佳化達成的。這與先前的研究（Ahuja and Sethia 2024、X. Wu and Chan 2022、J. Li et al. 2023）形成對比——那些研究未能在小型資料集上以跨受試者遷移學習展示分類準確率的提升。而此類小型資料集，正是許多實務 BCI 應用的主要使用情境。
>
> 元學習相關的主要挑戰之一，是可用資料集在「使用者數量」意義上的規模。即使蒐集更多的小型場次，也可能改善本方法的效能。全自動元最佳化的另一項缺點，可能是超參數的大規模搜尋。在我們的實驗中，使用 NVIDIA Tesla P100 GPU 約需 24 小時。

---

## 5. Conclusion | 結論

> [!quote] Original
> In this article, we introduce EEG-Reptile, a meta-learning library designed to enhance classification accuracy in BCI tasks with EEG data. Application of the Reptile meta-learning algorithm using this library improved classification performance on two benchmark datasets, BCI IV 2a and Lee2019 MI, in both zero-shot and few-shot learning scenarios in comparison with a straightforward transfer learning approach. EEG-Reptile provides a practical solution to improve the classification accuracy of EEG data, in the cases of an insufficient amount of data for the target subject, which is essential for various applications such as brain-computer interfaces and cognitive research.
>
> The proposed library has additional advantages over existing solutions. Firstly, EEG-Reptile allows mostly autonomous operation, making it a valuable tool for researchers and practitioners who may not have extensive expertise in deep learning or meta-learning. Secondly, the proposed weights initialization procedure enables exclusion of "outlier" subjects from the training set of tasks for meta-learning. Lastly, it can be easily used with various neural network architectures, making it a versatile solution for EEG-based applications.

> [!note] 翻譯
> 在本文中，我們介紹了 EEG-Reptile：一個旨在提升 EEG 資料 BCI 任務分類準確率的元學習函式庫。透過此函式庫應用 Reptile 元學習演算法，相較於簡單的遷移學習方法，在 BCI IV 2a 與 Lee2019 MI 兩個基準資料集上，零樣本與少樣本學習情境的分類效能均獲改善。EEG-Reptile 為「目標受試者資料量不足」情況下提升 EEG 資料分類準確率提供了務實的解決方案，這對腦機介面與認知研究等多種應用至關重要。
>
> 所提函式庫相較既有解決方案還有其他優勢。首先，EEG-Reptile 允許幾乎自主的運作，對可能不具備深度學習或元學習深厚專業的研究者與實務工作者而言，是一項有價值的工具。其次，所提出的權重初始化程序能將「離群」受試者排除於元學習的任務訓練集之外。最後，它可輕易搭配各種神經網路架構使用，是基於 EEG 之應用的多用途解決方案。

---

## Acknowledgments | 致謝

> [!quote] Original
> This research was funded by the Russian Science Foundation, grant 22-19-00528. Special thanks to E.I. Chetkin for his contribution in improving the readability of this paper.

> [!note] 翻譯
> 本研究由俄羅斯科學基金會（Russian Science Foundation）資助，計畫編號 22-19-00528。特別感謝 E.I. Chetkin 為改善本文可讀性所做的貢獻。

---

## References | 參考文獻

> [!info] References omitted / 參考文獻略
