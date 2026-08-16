---
schema_version: "1.0"
id: P_draft_Zero_Shot_Configuration_Transfer
type: point
name: "Zero-Shot Configuration Transfer"
description: "不經任何搜尋、直接把來源任務的最優配置套用到新任務；其在 EEG 管線配置層級的保留率是文獻未測的空白。"
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [zero-shot, configuration-transfer, warm-start, eeg]
domain: [AI]
field: [AutoML]
status: draft
created: 2026-08-15
updated: 2026-08-15
parent: P_draft_Warm_Start_Initialization
parts: []
depends_on: [P_draft_MI_SMAC]
caused_by: []
causes: []
related_lines: []
related_planes: []
related_body: [B_draft_llm-warmstart-cash-mi-eeg-pipelines]
source: "Concept (anchors: FeurerEtAl2015 + lab pilot)"
year: 2015-2026
claim_type: conceptual
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-2"
    - "step8_hypothesis_specification.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# Zero-Shot Configuration Transfer

> **核心主張**：零樣本配置遷移——不在目標任務上做任何搜尋、直接套用來源任務的最優配置——是 warm-start 的極限情形（搜尋預算 = 0），其能保留多少搜尋最優的改善量，在 EEG 管線的「配置層級」是一個未被測量的空白。

## 來源
- 作者：概念卡（無單一原始論文）/ 年份：2015–2026 / 出處：錨點為 FeurerEtAl2015 的 meta-learning 初始設計（配置直接重用的原型）、step6 Theme 2/4/5 的空白盤點、與本 session lab pilot（step8）/ citation key: `FeurerEtAl2015`（機制錨點）

## 目的
界定「不搜尋」與「完整搜尋」之間的實務操作曲線：若來源受試者的最優管線配置零樣本套用即可保留可觀的效能，逐受試者搜尋的成本（文獻記錄單一設定可達約 24 小時，BerdyshevEtAl2024）便可依場景彈性削減；此保留率同時是 warm-start 增益的下界參照點。

## 核心主張（展開）
零樣本配置遷移是配置知識遷移光譜的端點：MI-SMBO 的 t 個 meta-建議配置在 SMBO 開始前先被逐一評估——那本質上就是「零樣本配置遷移 + 少量驗證」，且 FeurerEtAl2015 圖 1 中圖顯示有資料集第 2 個 meta-建議配置已是全域最優，搜尋無可再改進。近年文獻把這個端點推得更遠：符號式預設值（GijsbersEtAl2021; PfistererEtAl2018）與 TabPFN 的 2.8 秒 in-context 預測擊敗調參 4 小時的集成（HollmannEtAl2025），質疑序列式搜尋迴圈本身的必要性。然而在 EEG 領域此問題完全未測：跨受試者研究遷移的是模型權重、對齊轉換與 meta-learning 初始化，「配置/搜尋知識」的跨受試者零樣本遷移沒有任何已識別論文做過（step6 Theme 4/5：「Zero-shot *configuration* transfer is untested here」）。EEG 配置層級的保留率因此是本研究 GAP_002 的直接標的。

## 方法
（概念卡，無單一原始實驗。）操作化定義（本研究 RQ3/H2）：以 leave-one-subject-out 將來源受試者搜尋所得最優配置直接套用於未見過的目標受試者，量測 (1) zero-shot 準確率相對固定標準管線（8–30 Hz band-pass + CSP + LDA）與相對該受試者搜尋最優的位置；(2) retention ratio（保留搜尋最優改善量的比例）；(3) 負遷移率（zero-shot 低於標準管線的受試者比例），並比較相似度門控與無門控變體（Wilcoxon signed-rank + McNemar's test）。

## 發現
- Lab pilot 單點（step8）：受試者 S002 zero-shot 0.765，落在 baseline 0.635 與搜尋最優 0.950 之間，retention ≈ 41%——方向支持 H2，但僅為單一受試者，須跨族群刻畫。
- 文獻對照：EEG 族群存在約 30 倍特徵偏差的離群受試者（ZhangEtAl2024，step6 Theme 5），保留率分布預期有重尾；meta-learned 零樣本「權重層級」遷移在 BCI IV 2a 四類 MI 上停留在 43% ± 7%（BerdyshevEtAl2024）——配置層級是否更穩健，無資料 (待補，即 GAP_002 本身)。
- 一般 AutoML 端的參照：TabPFN 2.8 秒勝過 4 小時調參集成（HollmannEtAl2025，abstract-only）。

## 啟發
- **被啟發**：[[P_draft_MI_SMAC]] — meta-learning 初始設計的前 t 個配置即零樣本遷移的原型；[[P_draft_Meta_Features]] 提供「向誰借配置」的相似度座標系。
- **啟發了**：本研究 H2 — 將零樣本配置遷移的保留率與門控後的負遷移率列為正式次要終點，定義「不搜尋 vs 完整搜尋」的操作曲線。
