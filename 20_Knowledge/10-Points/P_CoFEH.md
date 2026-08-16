---
schema_version: "1.0"
id: P_CoFEH
type: point
name: "CoFEH"
description: "CoFEH interleaves LLM tree-of-thought feature engineering with FE-conditioned SMAC-based BO, reaching average rank 1.75 in the CASH setting and raising surrogate Spearman correlation from 0.587 to 0.691."
asjc: []
asjc_label: []
cpc: []
cpc_label: []
tags: [llm, feature-engineering, hpo]
domain: [AI]
field: [AutoML]
status: active
created: 2026-08-15
updated: 2026-08-15
parent:
parts: []
depends_on: [P_SMAC]
caused_by: []
causes: []
related_lines: []
related_planes: []
related_body: [B_llm-warmstart-cash-mi-eeg-pipelines]
source: "XuEtAl2026a"
year: 2026
claim_type: methodological
provenance:
  session_id: "20260815"
  source_files:
    - "step6_sota_review.md#theme-1"
    - "step5_full_text/XuEtAl2026a.md"
  generated_at: 2026-08-15
source_session: "20260815_llm-warmstart-cash-mi-eeg-pipelines"
---

# CoFEH

> **核心主張**：讓 LLM 特徵工程與 FE-條件化的 SMAC 式 BO 交錯（interleaved）並相互條件化，而非「先 FE 再 HPO」的貪婪序列，可在 CASH 情境取得平均排名 1.75，並把 BO surrogate 與真實表現的 Spearman 相關從 0.587 提升到 0.691。

## 來源
- 作者：Xu, B., Ding, K., Liu, W., Lu, Y., & Cui, B. / 年份：2026 / 出處：*Proceedings of the 32nd ACM SIGKDD Conference on Knowledge Discovery and Data Mining V.2 (KDD '26)*（arXiv:2602.09851） / citation key: `XuEtAl2026a`

## 目的
回答「LLM 擅長 FE 探索但難與 BO-based HPO 耦合（capability-integration paradox），如何讓兩個異質最佳化器聯合而非孤立地最佳化」的問題。

## 核心主張（展開）
既有 LLM-FE 方法多退回貪婪的「FE-then-HPO」序列流程，忽略特徵表示與模型容量的強依賴；傳統系統（auto-sklearn）雖能聯合最佳化，卻受限於封閉的操作庫與剛性模板。CoFEH 以三個機制解決：(1) LLM-driven tree-of-thought（TOT）FE optimizer——探索不受模板限制的自由形式 FE pipeline 拓撲與操作（preprocessing、transformation、generation、selection 自由組合）；(2) mutual conditioning——在 LLM 與 BO 之間建立雙向資訊流：LLM 做 FE 決策時看得到 HPO 分數，BO 做 HPO 時以 FE meta-features 條件化其 surrogate，避免孤立的次佳調校；(3) PUCB-based dynamic optimizer selector——依任務效用自適應地在 FE 步與 HPO 步之間分配預算。這是 step6 綜述指認的 2026 年最明確趨勢（協作式 pipeline/CASH 最佳化器）的代表作，其立場是「BO 仍是 HPO 的黃金標準」——LLM 被條件化與閘控，而非取代 BO。

## 方法
LLM 作為 operator generator 在 TOT 搜尋中展開 FE pipeline；HPO 由源自 SMAC 的 BO 模組求解（HPO 為含 CASH 的傘狀詞：組態空間同時涵蓋演算法選擇與其超參數）；selector 以 PUCB 依據回饋獎勵動態選擇下一步呼叫 LLM-FE 或 BO-HPO。實驗於 28 個 OpenML/Kaggle 表格資料集、200 次評估預算，比較傳統 AutoML（含 Mindware）與 LLM-based 基線（OpenFE、OCTree、ELLM、LFG 等），主實驗以 XGBoost 為下游模型並測試跨模型泛化，各設定重複 3 次。

## 發現
- 聯合 FE+HPO 情境：平均排名 1.75，對 Mindware 的 3.46（step6；CD plot 亦標示 CoFEH [1.75] vs OCTree [4.57]）。
- 相對單獨 FE：聯合最佳化平均帶來 +7.03% 誤差降低（step6）。
- CASH 情境：最高 +45.1% 誤差降低（airfoil_self_noise：2.72 → 1.49）優於最佳基線。
- mutual conditioning 消融：FE meta-features 使 BO 預測與真實表現的平均 Spearman 相關由 0.587 升至 0.691（28 個資料集）。
- 成本效益：以每任務約 $0.07 的 token 成本取得最佳平均排名，優於排名第二的 LFG。

## 啟發
- **被啟發**：[[P_LLAMBO]] — 承接「LLM 補足 BO、增益來自語意先驗」的定位，但把 LLM 的角色從 surrogate/取樣器收縮為受條件化的 FE 探索者
- **被啟發**：[[P_SMAC]] — HPO 引擎直接源自 SMAC 的 BO，並示範如何以 meta-features 條件化其 surrogate
