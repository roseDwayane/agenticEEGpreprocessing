---
id: F_draft_T2_FOOOF_Spectral
type: plane
name: "FOOOF / Spectral Parameterization as Signal–Noise Decomposition"
member_points: [P_draft_FOOOF, P_draft_IRASA, P_draft_FOOOF_SNR]
adjacent_planes: [F_draft_T3_Aperiodic_Signal, F_draft_T4_Preproc_Quality]
status: draft
created: 2026-06-20
updated: 2026-06-20
provenance:
  session_id: "20260620"
  source_files:
    - "step6_sota_review.md#theme-2"
  generated_at: 2026-06-20
source_session: "20260620_agentic-eeg-preprocessing-fooof"
---
# Plane T2: FOOOF / Spectral Parameterization as Signal–Noise Decomposition / FOOOF 頻譜參數化作為訊號–雜訊分解

## Q1 解決什麼問題？
如何有原則地把神經功率譜分離為「訊號」（週期振盪）與「雜訊樣」（非週期 1/f 背景）——這供應計畫的**品質指標**。

## Q2 核心概念有哪些？
- [[P_draft_FOOOF]] — 1/f 背景 + 高斯峰參數化
- [[P_draft_IRASA]] — 以重採樣分離碎形/振盪
- [[P_draft_FOOOF_SNR]] — 把分解用作預處理目標函數（本計畫創新）

## Q3 概念之間關係？
FOOOF 與 IRASA 是競爭的分解後端（見 [[D_draft_FOOOF_vs_IRASA]]）；FOOOF-SNR 建立在 FOOOF 之上，把描述性分解反轉為最佳化目標。

## Q4 常用方法？
log-log 功率譜的迭代擬合（FOOOF）或不規則重採樣中位數（IRASA）；取 exponent、offset、峰中心頻率/功率/頻寬；以擬合優度把關。

## Q5 常見錯誤？（verbatim from step7 + step6 debates）
> GersterEtAl2022：特定頻譜特徵（過窄擬合範圍、重疊峰、knee/bend）會妨礙分離、扭曲非週期估計。
> 文獻中無任何論文將 FOOOF 用作**目標函數**——皆為描述性使用（報告 exponent/offset）。
> （關鍵警示，與 T3 對讀）天真的「最小化非週期」會破壞真實訊號——見 [[A_draft_Caveat_x_Aperiodic]]。

## 與其他 Plane 的關係
與 T3（非週期即訊號）緊密耦合——分解出的「雜訊」其實部分是真實訊號；與 T4（客觀品質）橋接於 [[A_draft_Quality_x_Spectral]]。
