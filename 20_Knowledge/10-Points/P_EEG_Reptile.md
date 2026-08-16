---
schema_version: "1.0"
id: P_EEG_Reptile
type: point
name: "EEG-Reptile"
description: "跨受試者 meta-learning 權重初始化的自動化庫；few-shot 天花板 43%±7%（zero-shot）/46%±5%（16-shot）對比約 84% 的完整受試者特定訓練，顯示權重層級遷移的極限。"
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [meta-learning, eeg, few-shot]
domain: [AI, Neuroscience]
field: [BCI]
status: active
created: 2026-08-15
updated: 2026-08-15
parent:
parts: []
depends_on: []
caused_by: []
causes: []
related_lines: []
related_planes: []
related_body: [B_llm-warmstart-cash-mi-eeg-pipelines]
source: "BerdyshevEtAl2024"
year: 2024
claim_type: methodological
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-5"
    - "step5_full_text/BerdyshevEtAl2024.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# EEG-Reptile

> **核心主張**：EEG-Reptile 是把 Reptile meta-learning 自動化應用於 BCI 分類器的開源庫——它把「每位受試者視為一個任務」並跨受試者遷移權重初始化，顯著優於一般 transfer learning（p < 0.05），但 few-shot 天花板仍低（四類 MI zero-shot 43%±7%、16-shot 46%±5%，對比約 84% 的完整受試者特定訓練），顯示權重層級遷移不足以解決校正問題。

## 來源
- 作者：Berdyshev, D. A., Grachev, A. M., Shishkin, S. L., & Kozyrskiy, B. L. / 年份：2024 / 出處：arXiv preprint（arXiv:2412.19725; GitHub: gasiki/EEG-Reptile）/ citation key: `BerdyshevEtAl2024`

## 目的
以自動化庫的形式讓非 meta-learning 專家也能對任意 SGD 類神經網路做跨受試者 meta-initialization，降低新受試者所需的校正資料量（few-shot 乃至 zero-shot）。

## 核心主張（展開）
EEG-Reptile 由四個模組構成：Data Storage（多受試者 EEG 資料與 metadata 管理）、Hyperparameter Search（以 Optuna 對 meta-learning 與 fine-tuning 兩階段自動調參）、Meta-Learning（Reptile 演算法本體）與 Fine-Tuning。其 Reptile 實作有兩項特色：(1) 對網路的不同層群（EEG 特徵抽取層 θ1 vs 分類層 θ2）使用獨立的 meta 學習率 β1/β2；(2) 權重初始化程序會計算每位受試者權重與平均權重的距離，剔除比例 γ 的離群受試者（作者類比 RANSAC），確保 meta-training 池的一致性。方法學上的關鍵框架選擇是：「把個別 BCI 使用者的分類問題視為獨立任務」——與其在所有使用者間尋找可能根本不存在的共同特徵，不如訓練分類器快速適應每位新使用者。值得注意的是，其 fine-tuning 的 hyperparameters（學習率、epochs-對-資料量的線性排程）是在非目標受試者上選定後沿用——step6 Theme 5 指出這是一種「不張揚的」組態層級遷移，但搜尋迴圈本身仍未被遷移。

## 方法
在 BCI IV 2a（9 人、22 通道、250 Hz、四類 MI；4–38 Hz band-pass + exponential moving standardization）與 Lee2019 MI（54 人、二類；降採樣至 250 Hz、選 20 個運動相關通道）上，對三種架構（EEGNet、FBCNet、EEG-Inception (MI)）進行評估：每資料集隨機選 5 位測試受試者，逐位完全排除於 meta-training 與 HPO 之外；比較 meta-learning 與傳統 transfer learning（baseline）在 zero-shot 與 few-shot（每類 1–8 筆、共 2–16 筆資料點）下的準確率；每實驗重複 5 次，以最後 20% 資料為固定測試集。

## 發現
- BCI IV 2a（四類）：EEGNet + meta-learning zero-shot 43% ± 7%；16 筆資料點 fine-tune 後 46% ± 5%（峰值）。
- Lee2019 MI（二類）：zero-shot 71% ± 5%；16 筆資料點後 72% ± 7%。
- 相對一般 transfer learning 的提升在兩資料集上皆統計顯著（p < 0.05, Wilcoxon signed-rank；95% 信賴區間）。
- 對比天花板：完整受試者特定訓練的 deep ConvNets 約 84% 平均解碼準確率（SchirrmeisterEtAl2017，經 step6 Theme 5 引述）——few-shot 增益幅度有限（43%→46%；71%→72%）。
- 層群分離優化（Opt-EEGNet）本身即提升準確率，且 meta-learning 與 transfer learning 皆受益。
- 全自動 HPO 成本約 24 小時／設定（Tesla P100）。

## 啟發
- **被啟發**：Reptile / MAML 一系的 model-agnostic meta-learning（Nichol et al. 2018; Finn et al. 2017）——EEG-Reptile 把此線的通用演算法首次以自動化庫形式帶進 BCI 的 few-shot/zero-shot 情境。
- **啟發了**：[[P_Subjects_as_Tasks]] — 其「受試者＝任務」的框架是本研究類比論證的一半：EEG-Reptile 在權重空間做 subjects-as-tasks，本研究把同一類比搬到配置／搜尋空間（transfer-HPO），並以其 43%→46% 的低 few-shot 天花板論證權重遷移之外還需要配置層級的遷移；[[P_BCI_Calibration_Problem]] — 其約 24 小時 HPO 成本同時是校正問題卡的核心證據。
