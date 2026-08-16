---
schema_version: "1.0"
id: A_Weight_Init_vs_Config_Init
type: line
relation_type: analogy
name: "Analogy: Weight Initialization ≅ Configuration Initialization"
description: "跨受試者遷移的同構結構出現在兩個抽象層：EEG-Reptile 遷移權重初始化 ≅ MI-SMAC 遷移配置初始化——後者在 EEG 是空位"
endpoints: [P_EEG_Reptile, P_MI_SMAC, P_Subjects_as_Tasks]
tags: [analogy, transfer, meta-learning]
status: active
created: 2026-08-15
updated: 2026-08-15
related_planes: [F_T2_Warmstart_Transfer_BO, F_T5_CrossSubject_EEG]
related_body: [B_llm-warmstart-cash-mi-eeg-pipelines]
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-5"
    - "step6_knowledge_graph.canvas (edge: BerdyshevEtAl2024 -> FeurerEtAl2015)"
    - "step7_gap_analysis.md#gap_001"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# 類比：權重初始化 ≅ 配置初始化

## 結構映射表

| 結構角色 | EEG-Reptile（權重層） | MI-SMAC（配置層） |
|---|---|---|
| 任務 | 一位 EEG 受試者 | 一個表格資料集 |
| 遷移資產 | meta-learned 模型初始權重 | 過往任務的最優配置 |
| 適應機制 | few-shot fine-tuning | BO 後續搜尋 |
| 相似度座標 | (隱式，由 meta-training 分布決定) | dataset meta-features |
| 失敗模式 | 離群受試者 few-shot 天花板 (43%±7%) | 低相似任務的 negative transfer |

## 為什麼是同構

兩者都是「**從任務家族學初始化，讓新任務的適應更便宜**」：把 [[P_EEG_Reptile]] 敘述中的「權重」替換為「配置」、「fine-tuning」替換為「BO 搜尋」，就得到 [[P_MI_SMAC]] 的敘述。canvas 邊（BerdyshevEtAl2024 → FeurerEtAl2015, "meta-learned weight initialization parallels meta-feature configuration init"）在 Step 6 已自動偵測到此同構。

## 映射的極限

- 權重空間連續高維、配置空間條件式混合——遷移的幾何完全不同。
- EEG-Reptile 的適應需要目標受試者的標註樣本；配置遷移的 zero-shot 版本（[[P_Zero_Shot_Configuration_Transfer]]）不需要梯度、只需要一次套用。
- 因此兩層可以疊加而非互斥：權重層與配置層的遷移可在同一系統中共存。

## 對 Cary 的意義

這張類比卡是 GAP_001 的一句話證明：**同構結構的左半邊（權重層）在 EEG 已被佔據，右半邊（配置層）是空位**——[[P_Subjects_as_Tasks]] 就是把右半邊補上的概念橋。Introduction 的 gap statement 可直接引用此映射表。
