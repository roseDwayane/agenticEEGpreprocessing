---
schema_version: "1.0"
id: B_llm-warmstart-cash-mi-eeg-pipelines
type: body
body_role: prescriptive
name: "Knowledge-Injected Subject-Specific EEG Pipeline Optimization System"
description: "以知識注入暖啟動分解式 CASH，把新受試者 EEG 解碼管線最佳化從冷啟動完整搜尋壓到近零校正的研究計畫"
tags: [research-programme, transfer-hpo, bci, llm]
status: active
created: 2026-08-15
updated: 2026-08-15
modules: [F_T4_EEG_Pipeline_Automation, F_T3_CASH_Systems_Benchmarks, F_T2_Warmstart_Transfer_BO, F_T1_LLM_for_AutoML, F_T5_CrossSubject_EEG]
feedback_loops: ["search-history flywheel", "H3 ablation → injection-level selection", "gating calibration loop"]
based_on: []
provenance:
  session_id: "20260815"
  source_files:
    - "step8_hypothesis_specification.md"
    - "step7_gap_analysis.md"
    - "step8_journal_recommendations.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# 知識注入式受試者特定 EEG 管線最佳化系統（研究計畫 Body）

## 1. 計畫核心目標（主觀）

把新受試者的 EEG MI 解碼管線最佳化成本，從「每人冷啟動完整搜尋」（現況：單一 HPO 設定可達 ~24h，BerdyshevEtAl2024）壓到「暖啟動快速搜尋，可選近零校正」，且每個增益宣稱都經得起最強通縮解釋（seeded 對照、budget-matching、LOSO 防污染）。對應 step7 GAP_001（composite 4.60）＋GAP_002（次要）＋GAP_003（方法學）。

## 2. 主要工作項（模組）

- **M1 搜尋空間定義**（[[F_T4_EEG_Pipeline_Automation]]）：5 階段條件式管線空間（resample → FIR/IIR band-pass → re-reference → epoch → CSP → classifier×HPs）
- **M2 搜尋引擎**（[[F_T3_CASH_Systems_Benchmarks]]）：VolcanoML 式分解 + SMAC3/OpenBox
- **M3 知識注入**（[[F_T2_Warmstart_Transfer_BO]]）：(a) LOSO 搜尋歷史先驗 (b) LLM 先驗（πBO 注入口）＋相似度門控
- **M4 LLM 知識源**（[[F_T1_LLM_for_AutoML]]）：API 級模型的先驗引出 prompt 模板
- **M5 評估**（內建 GAP_003 協議）：anytime AUC、evaluations-to-target、seeded 對照、Wilcoxon＋Holm–Bonferroni
- **M6 零樣本遷移**（[[F_T5_CrossSubject_EEG]]）：配置直接套用＋門控分析（H2）

## 3. 前置 / 後置（時程）

```
M1 空間定義 → M2 引擎(冷啟動 RQ1) → M3 注入(H1) → M6 零樣本(H2)
                        ↑                    ↓
                M5 評估協議(實驗設計期就前置決定) ← H3 消融
```

M1、M2 是前置（沒有空間與引擎，注入無處著力）；M5 名義上後置、**實際必須在設計期凍結**（endpoint 預註冊）；M4 可與 M3(a) 平行開發。

## 4. 每階段 I/O

| 階段 | 輸入 | 輸出 |
|---|---|---|
| M1 | MI 訊號處理領域知識 | 條件式搜尋空間定義（ConfigSpace） |
| M2 | 空間＋預算 B∈{25,50,100} | 冷啟動 incumbent 曲線＋**搜尋歷史**（109 受試者） |
| M3 | N−1 受試者歷史 / LLM 先驗＋meta-features | 門控後 prior＋初始設計 → 暖啟動 incumbent 曲線 |
| M4 | 資料集/受試者描述 prompt | π(x) 先驗分布（可重現模板） |
| M6 | 先驗的 top-1 配置 | 零樣本準確率＋保留率＋負遷移率 |
| M5 | 各條件曲線 | H1–H3 裁決＋可釋出 anytime 基準（GAP_003 資產） |

(step9 尚未產出——Contributions 對應之 I/O 細化 (待補))

## 5. 階段間互相影響

- M1 空間大小 ↔ M2 是否分解（D_Does_The_Optimizer_Matter 的裁決條件）
- [[P_Inter_Subject_Variability]]（~30 倍離群偏差）→ M3 門控強度校準
- M5 的 seeded 對照結果 → M4 的投資決策（LLM 臂是否值得 API 成本）
- M2 的歷史品質（每受試者搜尋深度）→ M3 先驗品質 → M6 零樣本上限

## 6. 回饋循環

1. **搜尋歷史飛輪**：每位新受試者的搜尋 → 歷史池擴大 → 先驗更準 → 下一位搜尋更便宜；歷史池本身即 GAP_003 可釋出的 transfer-HPO 基準資產（反向輸出給 AutoML 社群）。
2. **消融回饋**：H3 若 seeded 追平 → 注入層級降到初始設計即可，砍 LLM 成本；若 warm 勝出 → prior 形狀有價值，加碼 M4。
3. **門控校準迴路**：M6 的負遷移個案 → 回頭調整 meta-features 與門控閾值 → 重新驗證。

## 7. 解決什麼真實問題

- **純應用**：BCI 校正時間壓縮——受試者特定管線的實務化（目標讀者：JNE/TNSRE 社群）。
- **跨域應用（最強形態）**：把 EEG 受試者端給 AutoML 社群當 transfer-HPO 的現實任務家族（EggenspergerEtAl2021 點名缺的東西）——雙向 latticework：借 AutoML 的體填 EEG 的縫，同時以 EEG 反哺 AutoML 的基準生態。

## 風險與替代路徑（verbatim from step8 Risk Assessment）

| 風險 | 可能性 | 影響 | 緩解 |
|---|---|---|---|
| 離群受試者負遷移 | Medium | High | 門控＋πBO 衰減；負遷移率列為正式終點 |
| 增益化約為強預設 | Medium | High | H3 內建 seeded 對照；anytime AUC |
| LLM 先驗不穩（骨幹敏感） | Medium | Medium | 僅 API 級模型；固定 prompt 模板；歷史臂為對照 |
| 109 人歷史建置計算成本 | High | Medium | 評估快取；平行執行；歷史＝基準資產 |
| 效應過小（indistinguishability 情境） | Low–Medium | Medium | N=109 成對檢定力；預註冊；H1 不成立時基準仍可發表 |

## 連到的 Outputs

- 主論文：JNE（首選）/ TNSRE / CIBM / AEI（方法學敘事時）— step8_journal_recommendations.md
- 衍生：anytime 基準短文（GAP_003 資產獨立可發表）
- (step9 manuscript 產出後補連結 (待補))
