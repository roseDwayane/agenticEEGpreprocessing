---
id: F_draft_T1_EEG_Preprocessing_Pipelines
type: plane
name: "Standardized & Automated EEG Preprocessing Pipelines"
member_points: [P_draft_PREP_Pipeline, P_draft_HAPPE, P_draft_ICLabel, P_draft_Autoreject, P_draft_Automagic_QC]
adjacent_planes: [F_draft_T4_Preproc_Quality]
status: draft
created: 2026-06-20
updated: 2026-06-20
provenance:
  session_id: "20260620"
  source_files:
    - "step6_sota_review.md#theme-1"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# Plane T1: Standardized & Automated EEG Preprocessing Pipelines / 標準化與自動化 EEG 預處理管線

## Q1 解決什麼問題？
如何對大型、異質的 EEG 資料做一致、可重現、可規模化的 artifact 移除與預處理——手動清理無法規模化且主觀。這是計畫 agent 操作的**動作空間**。

## Q2 核心概念有哪些？
- [[P_draft_PREP_Pipeline]] — 穩健參考 + 壞通道偵測
- [[P_draft_HAPPE]] — 發展/高 artifact 自動管線（+ 變體）
- [[P_draft_ICLabel]] — 自動 IC 分類
- [[P_draft_Autoreject]] — 自動試次拒絕/修復
- [[P_draft_Automagic_QC]] — 標準化品質評估（及其點名的缺口）

## Q3 概念之間關係？
HAPPE 重用 PREP 的穩健參考、整合 Autoreject 與 ICLabel（見 frontmatter_patches）。各管線是「帶參數操作的序列」——其參數即搜尋對象（見 [[A_draft_Search_x_EEG]]）。

## Q4 常用方法？
MATLAB/EEGLAB 與 Python/MNE 生態；固定預設參數加少數可調閾值（濾波截止、ICA 拒絕閾值、ASR 截止 k、壞通道準則）；驗證以 artifact 移除敏感度/特異度或與人工編輯比較。

## Q5 常見錯誤？（verbatim from step7）
> GAP（Automagic, PedroniEtAl2019）：「there is no method to objectively quantify the quality of preprocessed EEG」——缺乏客觀品質目標導致透過主觀剔除受試者而 p-hacking。
> 每條管線都套用**固定**組態：參數設為預設、未逐記錄搜尋最佳。

## 升格/降格判斷
T1 是成熟、可獨立成面的領域。可考慮把 ASR/rASR、SOUND 升格為 P 卡（目前折入動作空間）。
