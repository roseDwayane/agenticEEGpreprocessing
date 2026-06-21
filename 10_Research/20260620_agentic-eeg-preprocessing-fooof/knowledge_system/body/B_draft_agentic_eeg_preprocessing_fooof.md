---
id: B_draft_agentic_eeg_preprocessing_fooof
type: body
body_role: prescriptive
name: "Agentic EEG preprocessing pipeline with FOOOF-based signal quality optimization"
modules: [F_draft_T1_EEG_Preprocessing_Pipelines, F_draft_T2_FOOOF_Spectral, F_draft_T3_Aperiodic_Signal, F_draft_T4_Preproc_Quality, F_draft_T5_Pipeline_Optimization]
feedback_loops:
  - "metric-first de-risk: validate FOOOF-SNR (H2) before building oracle/agent"
  - "oracle ↔ agent: oracle supervises agent; agent regret measured against oracle upper bound"
  - "aperiodic guard: reward design iterates against simulated artifacts with known aperiodic ground truth"
based_on: []
status: draft
created: 2026-06-21
updated: 2026-06-21
provenance:
  session_id: "20260620"
  source_files:
    - "step8_hypothesis_specification.md"
    - "step8_journal_recommendations.md"
    - "step9_manuscript/01_intro.tex"
    - "step7_gap_analysis.md"
  generated_at: 2026-06-21
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# Body (prescriptive): Agentic EEG preprocessing pipeline with FOOOF-based signal quality optimization / 智能體式 EEG 預處理流程（FOOOF 訊號品質最佳化）

> 本卡是計畫的可執行藍圖（prescriptive body）——使用者自己的研究計畫，而非對既有系統的描述性重建。

## Q1 計畫核心目標（主觀）
建造並驗證一個**智能體式 EEG 預處理流程**，統合三個耦合貢獻：(1) 以 FOOOF 衍生 SNR 取代下游準確率作為預處理品質指標；(2) 以窮舉/貪婪搜尋建立每筆記錄最佳組態的 ground-truth oracle；(3) 訓練 LLM agent 從中間訊號特徵挑選預處理操作與參數。核心主觀目標：**讓預處理從「被套用」變成「被最佳化」**，且全程無標籤、可解釋。

## Q2 主要工作項（IN scope）
- 定義並驗證 [[P_draft_FOOOF_SNR]]（帶限週期 SNR + 擬合優度把關）
- 建 [[P_draft_Greedy_Oracle]]：預處理格（濾波截止、line-noise、壞通道準則、ICA+ICLabel 閾值、ASR 截止、重參考）× 公開資料集 → 每筆最佳組態
- 建 [[P_draft_LLM_Preproc_Agent]]：讀頻譜/通道統計/IC 標籤/中間 FOOOF 估計 → 工具呼叫選步驟
- 對手：固定管線（[[P_draft_PREP_Pipeline]]、[[P_draft_HAPPE]]、Automagic 預設）、隨機/預設、古典搜尋（貝氏最佳化、隨機搜尋）

## Q3 前置/後置（時程）
靜態記錄、離線批次、可重現。管線順序：**指標（先驗證）→ oracle（建 ground truth）→ agent（學習）→ 評估（regret / 泛化 / 可解釋性）**。GAP_004 的非週期防護內建於指標，不是獨立階段。

## Q4 每階段 I/O（from step9 contributions）
| 階段 | 輸入 | 輸出 |
|---|---|---|
| 指標 | 預處理後功率譜 | FOOOF-SNR 分數 + 與專家/oracle 一致性（H2） |
| Oracle | 原始 EEG × 預處理格 | 每筆最佳組態（可重用基準資料集） |
| Agent | 中間訊號特徵 + oracle 監督 | agent 策略 + 逐步理由（H3） |
| 評估 | agent vs 固定/搜尋 baselines | regret、查詢成本、留出泛化、可解釋性指標（H4） |

## Q5 階段間互相影響（from step8 Risk Assessment）
- **指標→全部**：若 FOOOF-SNR 與專家/oracle 不一致（指標無效），oracle 與 agent 皆失去意義 → **故先測 H2**（最便宜的去風險）。
- **非週期風險→指標**：天真 SNR 懲罰真實非週期 → 帶限 SNR + 擬合把關（見 [[P_draft_Aperiodic_Artifact_Risk]]）。
- **搜尋成本→oracle**：格太大則計算爆炸 → 先貪婪/座標搜尋 + 粗格 + 快取中間階段 + 明示剪枝。
- **oracle→agent**：無 oracle 則 agent 無監督、無上界。

## Q6 回饋循環（from step8 mitigation + step9 approach）
1. **metric-first 去風險迴圈**：先廉價測指標效度，不過關就早停或改用複合指標（FOOOF-SNR + Delorme 式對比，若有標籤）。
2. **oracle↔agent 迴圈**：oracle 提供監督；以 regret（agent 距 oracle 上界）量化 agent 回收多少可達品質。
3. **非週期防護迴圈**：以已知非週期 ground truth 模擬 artifact，迭代獎勵設計使其只懲罰污染、不懲罰生理。

## Q7 解決什麼真實問題？（from step9 motivation + journal audience）
EEG 預處理品質目前靠下游準確率（需標籤+分類器）或主觀目視判斷，使大型公開靜息態語料無法有原則、可重現地品管（[[P_draft_Automagic_QC]]、[[P_draft_Delorme_Quality_Metric]] 皆點名此缺口）。本計畫提供無標籤、任務無關、單筆即可算的品質訊號，並首次展示 LLM agent 遷移到電生理訊號處理。受眾：神經工程（Journal of Neural Engineering、NeuroImage、IEEE TNSRE）與 ML/agent（NeurIPS D&B / TMLR）。

## 風險與替代路徑（verbatim from step8 Risk Table）
| 風險 | 機率 | 衝擊 | 緩解 |
|---|---|---|---|
| FOOOF-SNR 與專家/oracle 不一致（指標無效） | 中 | 高 | 先測 RQ1/H2；預註冊相關門檻；退而求複合指標 |
| 天真 SNR 懲罰真實非週期（GAP_004） | 中 | 高 | 帶限週期 SNR；依 GersterEtAl2022 擬合把關；模擬已知非週期 ground truth |
| 窮舉搜尋計算爆炸 | 高 | 中 | 貪婪/座標搜尋 + 粗格；快取；剪枝並明示 |
| LLM agent 未勝古典搜尋 | 中 | 中 | 定位為可解釋 + 推論更便宜；古典搜尋仍為有效基準貢獻 |
| 公開資料異質性破壞管線 | 中 | 中 | 限 BIDS-EEG；標準化 montage/重採樣 |

## 連到的 Outputs（step8 publication targets）
- 主路徑：神經方法論文 → **Journal of Neural Engineering**（IF ~4.16, Q1）
- 進取：**NeuroImage**（IF ~4.5）；穩健：**IEEE TNSRE**
- 平行：**NeurIPS D&B / TMLR**（oracle 作為新基準 + agent 遷移）
- 建議（dedup-side action，不自動執行）：若 Knowledge 端有 `40-Body/B99_Research_Programme_Roadmap` 之類傘狀 body，於 promote 時 patch `related_body`。

## 模組（Planes）與回饋循環摘要
- modules: T1 預處理管線 / T2 FOOOF 分解 / T3 非週期即訊號 / T4 客觀品質 / T5 管線最佳化
- 三面交會（T1 動作空間 × T2 品質指標 × T5 搜尋/agent）即計畫本身；T3↔T2 張力是核心科學風險。
