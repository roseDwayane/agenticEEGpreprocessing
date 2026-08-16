---
schema_version: "1.0"
id: P_draft_Inter_Subject_Variability
type: point
name: "Inter-Subject Variability (EEG)"
description: "受試者間 EEG 特徵差異巨大——離群受試者的特徵值可偏離訓練集平均約 30 倍——使單一配置無法通用於所有受試者。"
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [eeg, variability, transfer]
domain: [AI, Neuroscience]
field: [BCI]
status: draft
created: 2026-08-15
updated: 2026-08-15
parent:
parts: []
depends_on: []
caused_by: []
causes: [P_draft_BCI_Calibration_Problem]
related_lines: []
related_planes: []
related_body: [B_draft_llm-warmstart-cash-mi-eeg-pipelines]
source: "ZhangEtAl2024 + BerdyshevEtAl2024"
year: 2024
claim_type: empirical
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-5"
    - "step5_full_text/ZhangEtAl2024.md"
    - "step5_full_text/BerdyshevEtAl2024.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# Inter-Subject Variability (EEG)

> **核心主張**：受試者間 EEG 特徵差異可達極端量級——離群受試者的 zero-crossing rate 偏離訓練集平均約 30 倍——因此任何單一模型配置都無法通用於所有受試者，且跨受試者誤判可具體追溯至這些離群個體。

## 來源
- 作者：Zhang, X., Wang, S., Xu, K., Zhao, R., & She, Y. / 年份：2024 / 出處：*Mathematical Biosciences and Engineering*, 21(3), 4779–4800（DOI: 10.3934/mbe.2024210）/ citation key: `ZhangEtAl2024`
- 佐證：Berdyshev, D. A., Grachev, A. M., Shishkin, S. L., & Kozyrskiy, B. L. / 2024 / arXiv:2412.19725 / citation key: `BerdyshevEtAl2024`

## 目的
量化「受試者間變異」這一 BCI 部署核心障礙的實際幅度，並確立它是跨受試者誤判的可追溯原因，而非籠統的背景假設。

## 核心主張（展開）
ZhangEtAl2024 在 SEED 與 DEAP 上做跨受試者情緒辨識時，對誤判個案進行特徵層級的歸因分析：被誤判的受試者其時域特徵值以數十倍的量級偏離訓練集群體統計，證明單一分類器參數組（乃至單一特徵組合）在離群受試者上系統性失效。BerdyshevEtAl2024 從神經生理角度給出機制解釋：變異主要源於腦解剖與功能的個體差異，且電極位置、病理過程與生理狀態的緩慢波動甚至使分類器需要「以天為單位」重新校正；某些受試者的神經模式偏離平均到「納入 meta-training 反而劣化泛化」的程度，因此其 EEG-Reptile 庫特別內建離群受試者過濾機制。兩篇文獻共同確立：受試者不是同分布樣本，而是彼此差異巨大的「任務」，這使負遷移（negative transfer）成為任何跨受試者知識共享的固有風險。

## 方法
ZhangEtAl2024：在 DEAP（32 人、二分類）與 SEED（15 人、三分類）上以隨機分組（DEAP 25 訓練/7 測試；SEED 12/3）進行跨受試者實驗，抽取 12 種時域/頻域/時頻域特徵組成 8 種組合，以 SSA（Sparrow Search Algorithm）動態優化 RF 的 DTN 與 LMN；對誤判受試者將其特徵值與訓練集同特徵平均值逐項比對。BerdyshevEtAl2024：以 Reptile meta-learning 在 BCI IV 2a 與 Lee2019 MI 上做跨受試者遷移，並以權重初始化程序偵測與剔除離群受試者（剔除比例 γ）。

## 發現
- SEED 誤判歸因（subject 1，正向誤判為負向）：ZCR 訓練集平均 6,930.1 vs 該受試者 218,213.6（約 30 倍差距）；SD 訓練集平均 2,880.4 vs 206,475.2。
- DEAP 誤判歸因（subject 15）：SD 平均 11.75 vs 87.27（ΔSD ≈ 75.52）；RMS 平均 16.13 vs 79.8（ΔRMS ≈ 63.69）。
- 配置最優值因資料而異：SSA 搜得的最優 DTN 在 DEAP 為 24–50、SEED 為 28–50，均偏離經驗預設值 30；SSA-RF 相對預設 RF 平均提升 DEAP +1.62%、SEED +9.85%（最高單組合 +13.96%）。
- BerdyshevEtAl2024：即使經 meta-learning，BCI IV 2a 四類 MI 零樣本準確率仍停在 43% ± 7%，遠低於完整受試者特定訓練的約 84%（SchirrmeisterEtAl2017，經 step6 Theme 5 引述）——變異大到權重層級遷移也只能部分彌合。

## 啟發
- **被啟發**：step6 Theme 5 的共識脈絡（BerdyshevEtAl2024; WuEtAl2022; AnarakiEtAl2024; AristimunhaEtAl2025 皆視受試者間變異為部署核心障礙）——本卡把該共識落到可引用的具體數字上。
- **啟發了**：[[P_draft_BCI_Calibration_Problem]] — 變異是每位新使用者都需重新校正的直接原因；[[P_draft_Subjects_as_Tasks]] — 差異大到受試者應被建模為 transfer-HPO 的任務家族，且約 30 倍偏差的離群個體使相似度門控（similarity gating）成為防範 negative transfer 的必要設計（本研究 H2 的門控變體與風險評估直接引用此數字）。
