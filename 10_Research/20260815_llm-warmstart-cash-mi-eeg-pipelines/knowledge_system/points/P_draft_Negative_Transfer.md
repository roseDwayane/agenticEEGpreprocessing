---
schema_version: "1.0"
id: P_draft_Negative_Transfer
type: point
name: "Negative Transfer"
description: "來源知識可能傷害目標任務表現；教科書式 multi-task GP 即使在仿射相關的任務上也會因結構性機制錯估跨任務相關，顯示部分遷移失敗並非任務不相似所致。"
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [negative-transfer, multi-task-gp, transfer-learning, risk]
domain: [AI]
field: [AutoML]
status: draft
created: 2026-08-15
updated: 2026-08-15
parent:
parts: []
depends_on: []
caused_by: []
causes: [P_draft_Similarity_Gating]
related_lines: []
related_planes: []
related_body: [B_draft_llm-warmstart-cash-mi-eeg-pipelines]
source: "HvarfnerEtAl2026"
year: 2026
claim_type: empirical
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-2"
    - "step5_full_text/HvarfnerEtAl2026.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# Negative Transfer

> **核心主張**：來源任務的知識可能使目標任務表現變差；且此風險比通常承認的更深層——教科書式 multi-task GP 即使在「仿射相關」這種理應必然成功的最簡非平凡情形下，也會因兩個獨立的結構性機制錯估跨任務相關性，意味著部分遷移失敗是方法結構性的，而非僅「任務不相似」。

## 來源
- 作者：Hvarfner, C., Daulton, S., Balandat, M., & Bakshy, E. / 年份：2026 / 出處：arXiv preprint（Pitfalls and remedies for multi-task Bayesian optimization；本 session 僅得 abstract）/ citation key: `HvarfnerEtAl2026`

## 目的
檢驗「以 multi-task GP 作為 warm-start 預設 surrogate」這個教科書做法是否可靠：在可控的仿射相關 source/target 設定下，任何可用的遷移方法都應當成功——若連此處都失敗，則失敗原因是結構性的。

## 核心主張（展開）
負遷移在本文獻脈絡中有兩層。第一層是任務層面的經典觀察：遷移助益與任務相似度成正比，相似度低時天真重用來源分布會劇烈退化（NomuraEtAl2021 的 ReuseNormal），FeurerEtAl2015 自己的圖表也顯示 meta-learning 在部分資料集上反而降低 SMAC 表現。第二層是 HvarfnerEtAl2026 揭示的結構層面：即使 source 與 target 僅差一個仿射變換，multi-task GP 仍錯估跨任務相關。追溯出兩個獨立機制——(1) per-task standardization（教科書式處理仿射切片模糊性的修正）會把有限樣本的對齊誤差傳播進恢復出的相關係數；(2) marginal likelihood 本身只以 per-sample 速率識別相關性，而不重疊的實驗設計（non-overlapping designs）進一步稀釋之。這暗示部分文獻報告的遷移失敗不是「任務不相似」，而是 surrogate 結構本身的錯估——為外掛式的相似度防護機制提供了獨立理由。

## 方法
（僅 abstract，細節待補。）在受控的合成 multi-task 問題與 surrogate-based 超參數調校遷移上重訪 multi-task GP 預設做法；由分析導出三個保守補救：(1) 把 per-task 均值與尺度提升為模型參數、(2) 將 task covariance 限制為非負相關、(3) 讓部分 source 與 target 設計點共置（co-locate）。

## 發現
- (abstract 無量化數字，具體數據待補。)
- 質性結果：三個補救在簡單案例上可恢復到 target-only 基線的水準；但在較難的案例與多數 rank-based 及 latent-context 變體上，較廣泛的失敗仍然存在——即結構性錯估未被完全解決。
- 佐證（step6 Theme 2）：NomuraEtAl2021 顯示 ReuseNormal / ReuseGMM 在來源—目標最優解偏移時劇烈退化；FeurerEtAl2015 的 CASH 實驗中 meta-learning 初始化在 7% 資料集上使 SMAC 變差。

## 啟發
- **被啟發**：multi-task BO 傳統（Swersky et al. 2013 一系）— 本卡是對其教科書 surrogate 的結構性審視。
- **啟發了**：[[P_draft_Similarity_Gating]] — 負遷移風險（尤其是 surrogate 層面無法自我察覺的結構性錯估）直接創造了「以外部相似度訊號門控暖啟動強度、低相似度時退回冷啟動」的需求（causes 關係）；本研究據此將負遷移率列為正式實驗終點而非 nuisance。
