---
session_id: "20260815"
topic: "LLM-informed warm-starting and prior injection for Bayesian optimization (CASH) of subject-specific EEG motor-imagery preprocessing pipelines, with cross-subject transfer"
date: "2026-08-15"
step: 6
total_papers: 124
themes: 5
full_text_papers: 40
abstract_only_papers: 84
---

# State-of-the-Art Review / 研究現況綜述

> Topic / 研究主題: LLM-informed warm-starting and prior injection for BO (CASH) of subject-specific EEG MI pipelines, with cross-subject transfer
> Papers synthesized / 綜整論文數: 124 (40 full-text 全文, 84 abstract-only 摘要)
> Themes identified / 主題數: 5
> Date / 日期: 2026-08-15

> [!info] Synthesis basis / 綜整基礎
> 40 papers were read in full text (deterministic PDF extraction); the remaining 84 contribute at abstract level and are weighted lower in synthesis claims. / 40 篇以全文深讀（確定性 PDF 抽取），其餘 84 篇以摘要層級納入，綜整論點中權重較低。

## Executive Summary / 總覽摘要

Across 124 papers spanning 2015–2026, three research strands are converging on the same conclusion from different directions. First, the AutoML/CASH systems literature (Theme 3) has established that decomposition-based search — the VolcanoML/Rising-Bandits lineage — scales better than joint Bayesian optimization as search spaces grow, yet its own authors repeatedly name meta-learned warm-starting as the next acceleration lever (LiEtAl2016, LiEtAl2020a, LiEtAl2022a). Second, the warm-starting/transfer-BO literature (Theme 2) has matured from meta-feature-based initialization (FeurerEtAl2015) to principled prior injection (πBO, HvarfnerEtAl2022), with evidence that a good initialization often contributes more than a better search algorithm, and that prior/space-level transfer is more robust than surrogate-level transfer. Third, the LLM-for-AutoML literature (Theme 1) has settled into a nuanced position: LLMs are weak standalone surrogates but genuinely useful cross-task priors and cold-start initializers — with a 2026 budget-matched caution (RodriguesEtAl2026) that much of the apparent benefit can reduce to a good default configuration, and evidence that hybrid LLM+BO designs (CoFEH-style conditioning) outperform LLM-only optimization.

在 2015–2026 年的 124 篇文獻中，三條研究脈絡正從不同方向匯聚到同一結論。第一，AutoML/CASH 系統文獻（主題三）已確立分解式搜尋——VolcanoML／Rising Bandits 一系——在搜尋空間變大時比聯合貝葉斯最佳化（joint BO）更能擴展，而其作者群一再點名 meta-learning 暖啟動是下一個加速槓桿（LiEtAl2016, LiEtAl2020a, LiEtAl2022a）。第二，暖啟動／遷移 BO 文獻（主題二）已從基於 meta-feature 的初始化（FeurerEtAl2015）成熟到有原則的先驗注入（πBO, HvarfnerEtAl2022），並有證據顯示良好的初始化往往比更好的搜尋演算法貢獻更大，且先驗／空間層級的遷移比代理模型層級的遷移更穩健。第三，LLM×AutoML 文獻（主題一）已收斂到一個細緻的立場：LLM 作為獨立代理模型偏弱，但作為跨任務先驗與冷啟動初始化器確實有效——2026 年的預算對齊研究（RodriguesEtAl2026）提醒，表面上的效益有相當部分可化約為一個好的預設配置；且混合式 LLM+BO 設計（CoFEH 式條件化）優於純 LLM 最佳化。

On the application side, the EEG literature shows a striking asymmetry. EEG pipeline automation (Theme 4) exists but is dominated by evolutionary search over deep architectures; Bayesian optimization — the mainstream AutoML engine — barely appears, and no paper combines LLM guidance or warm-started search with EEG pipeline optimization. Cross-subject EEG research (Theme 5) transfers model weights, alignment transforms, and meta-learned initializations, but no identified paper transfers *configuration/search knowledge* across subjects to warm-start a new subject's pipeline optimization — per-subject HPO, where attempted, is cold-restarted each time at costs up to ~24 hours per setting (BerdyshevEtAl2024). The intersection targeted by this session's research question — LLM/meta-learned warm-starting of decomposed CASH for subject-specific EEG MI pipelines with zero-shot config transfer — is thus supported on every side by mature methods yet occupied by no existing work.

在應用端，EEG 文獻呈現鮮明的不對稱。EEG 管線自動化（主題四）確實存在，但由深度架構上的演化式搜尋主導；主流 AutoML 引擎——貝葉斯最佳化——幾乎缺席，且沒有任何論文把 LLM 引導或暖啟動搜尋與 EEG 管線最佳化結合。跨受試者 EEG 研究（主題五）遷移的是模型權重、對齊轉換與 meta-learning 初始化，但未發現任何論文將「配置／搜尋知識」跨受試者遷移以暖啟動新受試者的管線最佳化——現有的逐受試者 HPO 每次都是冷啟動重跑，單一設定成本可達約 24 小時（BerdyshevEtAl2024）。因此，本研究問題鎖定的交集——以 LLM／meta-learning 暖啟動分解式 CASH、用於受試者特定 EEG MI 管線並支援零樣本配置遷移——四面八方都有成熟方法支撐，卻無現有工作佔據。

## Methodology Legend / 方法學圖例

| Color / 顏色 | Methodology / 方法學 | Count / 數量 |
|-------------|---------------------|-------------|
| 🔴 Red | Experimental (controlled evaluation studies) / 實驗研究 | 25 |
| 🟠 Orange | Computational (ML methods, algorithms) / 計算方法 | 49 |
| 🟡 Yellow | Review (surveys, systematic reviews) / 文獻回顧 | 16 |
| 🟢 Green | Observational / 觀察研究 | 1 |
| 🔵 Cyan | Engineering (systems, frameworks, benchmarks) / 工程設計 | 28 |
| 🟣 Purple | Theoretical / 理論研究 | 5 |

---
# SOTA Review — Theme T1

## Theme 1: LLMs as Optimizers and Prior Injectors for AutoML/HPO / LLM 作為 AutoML/HPO 的最佳化器與先驗注入者

**Papers / 論文:** LiuEtAl2024, RodriguesEtAl2026, RychertEtAl2025, XuEtAl2026a, HuEtAl2025, MahammadliErtekin2024, ChenYi2026, GuptaEtAl2025, ZhaoEtAl2025, XuEtAl2026b, TopalisEtAl2025, Bal-GhaouiTiouti2025, YuanEtAl2026, ChenEtAl2025a, LiEtAl2025c, PatelEtAl2025, KristiadiEtAl2024, SaadallahEtAl2026, XuEtAl2025a, KobalczykEtAl2025, MenetEtAl2025, LeiCooper2026, SrinivasanMenzies2026, ChenEtAl2025b, Kannan2023, WangEtAl2025

### Consensus / 共識

Three claims recur across otherwise divergent papers. First, whatever value LLMs add to hyperparameter optimization is concentrated at the *start* of search: LLAMBO's zero-shot warm-start lowered early regret and run-to-run variance on 74 Bayesmark/HPOBench tasks, with gains most prominent before trial 5 (LiuEtAl2024); an independent reproduction with an open-weight Llama 3.1 70B backbone confirmed that contextual warm-starting "substantially improves early-regret behaviour and reduces variance", and that removing textual context markedly degrades prediction and calibration (RychertEtAl2025). Early exploratory work similarly found LLM-suggested starting conditions and narrowed search spaces produced faster validation-loss reduction than randomly initialized Bayesian optimization (Kannan2023), and an LLM-derived utility prior improved the initial BO query and enhanced optimization in 4 of 6 reaction-yield datasets (PatelEtAl2025). Second, the LLM is a poor *standalone* surrogate: both the original study and its reproduction report that the LLM discriminative surrogate is weaker than GP in calibration and weaker than SMAC as a pure single-task regressor, deriving its advantage from cross-task semantic priors rather than regression accuracy (LiuEtAl2024; RychertEtAl2025), and CASH-focused work concludes flatly that "BO remains the gold standard for HPO" (XuEtAl2026a; GuptaEtAl2025). Third, this suggests a hybrid design consensus — LLM as prior injector, proposal generator, or feedback translator wrapped around a classical BO/TPE/MCTS engine — visible in LLM-TPE samplers (MahammadliErtekin2024), interleaved LLM-FE + BO-HPO (XuEtAl2026a), LLM–BO tree search for CASH (XuEtAl2026b), meta-knowledge-augmented BO (SaadallahEtAl2026), and LLM-guided nearest-neighbour hybrids (GuptaEtAl2025). Effects also appear strongly model-capacity-dependent: Gemma 27B and Llama 3.1 8B produced unstable, malformed surrogate behaviour (RychertEtAl2025), and only 2 of 7 advisor models escaped a known exploration trap (RodriguesEtAl2026).

三項主張在立場迥異的論文間反覆出現。第一，LLM 對超參數最佳化的價值集中於搜尋的「起始階段」：LLAMBO 的零樣本暖啟動在 74 個 Bayesmark/HPOBench 任務上降低了早期遺憾與跨執行變異，增益在第 5 次試驗前最為顯著（LiuEtAl2024）；以開放權重 Llama 3.1 70B 為骨幹的獨立重現研究證實情境式暖啟動「大幅改善早期遺憾行為並降低變異」，且移除文字脈絡會明顯劣化預測與校準（RychertEtAl2025）。早期探索性研究同樣發現 LLM 建議的起始條件與縮小後的搜尋空間，比隨機初始化的貝葉斯最佳化更快降低驗證損失（Kannan2023）；由 LLM 導出的效用先驗改善了 BO 的初始查詢，並在 6 個反應產率資料集中的 4 個提升最佳化表現（PatelEtAl2025）。第二，LLM 作為「獨立」代理模型表現不佳：原研究與重現研究皆指出，LLM 判別式代理模型在校準上弱於 GP、作為純單任務迴歸器弱於 SMAC，其優勢來自跨任務語意先驗而非迴歸精度（LiuEtAl2024；RychertEtAl2025）；CASH 相關研究更直言「BO 仍是 HPO 的黃金標準」（XuEtAl2026a；GuptaEtAl2025）。第三，這顯示出一種混合式設計共識——LLM 作為先驗注入者、候選生成者或回饋轉譯者，包覆於古典 BO/TPE/MCTS 引擎之外——見於 LLM-TPE 取樣器（MahammadliErtekin2024）、交錯式 LLM 特徵工程 + BO 超參數最佳化（XuEtAl2026a）、面向 CASH 的 LLM–BO 樹搜尋（XuEtAl2026b）、後設知識增強 BO（SaadallahEtAl2026）與 LLM 引導最近鄰混合法（GuptaEtAl2025）。效果亦強烈依賴模型容量：Gemma 27B 與 Llama 3.1 8B 產生不穩定、格式錯誤的代理行為（RychertEtAl2025），且 7 個顧問模型中僅 2 個逃離已知的探索陷阱（RodriguesEtAl2026）。

### Debates / 爭議

The central dispute is whether the LLM's early-stage advantage is real or an evaluation artifact. A budget-matched, multi-seed audit on eight PMLB tabular benchmarks found the advisor's celebrated first point is a fixed default configuration evaluated *before any model call* (88.7% mean best-CV, identical across seven LLMs); the LLM's own proposals added only +0.40 pp on the CV objective and −0.01 pp (p = 0.92) on held-out test, and once classical search received the same default seed the advisor was behind by 12 evaluations (−0.37 pp) (RodriguesEtAl2026). This directly challenges the warm-start narrative of LLAMBO-style systems (LiuEtAl2024; MahammadliErtekin2024) — although the reproduction study, run under LLAMBO's own protocol, still found genuine contextual effects in ablations (RychertEtAl2025), suggesting the disagreement partly reflects protocol design (seeded controls, budget matching) rather than raw results. A second front questions whether LLMs optimize at all in-context: in scientific-discovery tasks, replacing true experimental outcomes with permuted labels had no impact on LLM-agent performance, and linear bandits and GP optimization consistently won (GuptaEtAl2025); a re-examination in software engineering reports LLMs competitive only below roughly a dozen dimensions, with Bayesian methods dominating beyond (SrinivasanMenzies2026); and molecular BO work concludes LLMs help only when pretrained or finetuned on domain data (KristiadiEtAl2024). A third, emerging debate concerns trust: LLM priors and self-reported confidence may be miscalibrated, motivating evidence-gated, falsifiable prior weighting (ChenYi2026) and showing elicited surrogate beliefs are highly sensitive to prompt wording and query protocol (LeiCooper2026), in tension with frameworks that inject LLM preferences directly into every iteration (YuanEtAl2026).

核心爭議在於 LLM 的早期優勢是真實存在，還是評估方法的假象。一項在 8 個 PMLB 表格基準上、預算匹配的多種子稽核研究發現，顧問系統著名的第一個點其實是「在任何模型呼叫之前」就已評估的固定預設組態（平均最佳交叉驗證 88.7%，在 7 個 LLM 間完全相同）；LLM 自身的提案在交叉驗證目標上僅增加 +0.40 個百分點，在保留測試集上為 −0.01 個百分點（p = 0.92），且一旦古典搜尋獲得相同的預設種子，顧問系統在 12 次評估後即落後（−0.37 個百分點）（RodriguesEtAl2026）。這直接挑戰 LLAMBO 類系統的暖啟動敘事（LiuEtAl2024；MahammadliErtekin2024）——不過在 LLAMBO 原始協定下執行的重現研究，其消融實驗仍發現真實的脈絡效應（RychertEtAl2025），顯示分歧可能部分反映協定設計（種子化對照、預算匹配）而非原始結果本身。第二條戰線質疑 LLM 是否真的在情境中進行最佳化：在科學發現任務中，以隨機置換的標籤取代真實實驗結果對 LLM 代理的表現毫無影響，而線性 bandit 與 GP 最佳化持續勝出（GuptaEtAl2025）；軟體工程領域的重新檢驗指出 LLM 僅在約十餘個維度以下具競爭力，超過此範圍則貝葉斯方法佔優（SrinivasanMenzies2026）；分子 BO 研究則認為 LLM 僅在經領域資料預訓練或微調後才有幫助（KristiadiEtAl2024）。第三個新興爭議關乎信任：LLM 先驗及其自述信心可能未經校準，因而促生證據閘控、可否證的先驗加權機制（ChenYi2026），並顯示誘出的代理信念對提示措辭與查詢協定高度敏感（LeiCooper2026），這與將 LLM 偏好直接注入每次迭代的框架（YuanEtAl2026）存在張力。

### Dominant Methods / 主流方法

Five LLM roles structure the field. (1) *Warm-starter / prior injector*: zero-shot proposal of initial configurations or search-space narrowing (LiuEtAl2024; Kannan2023; PatelEtAl2025), including structured prior construction — natural-language expert knowledge converted via RAG into Dirichlet priors over the BO space (TopalisEtAl2025) and zero-shot recommendations from historical experiments plus SHAP meta-features (Bal-GhaouiTiouti2025). (2) *Surrogate / acquisition component*: in-context discriminative surrogates (LiuEtAl2024; LeiCooper2026), fixed-feature extractors feeding principled Bayesian surrogates (KristiadiEtAl2024), fine-tuned Thompson-sampling policies (MenetEtAl2025), and likelihood-free acquisition informed by foundation-model priors (ChenEtAl2025b). (3) *Candidate generator inside a classical loop*: LLM-TPE hybrid samplers (MahammadliErtekin2024), genetic-algorithm interventions that break the LLM's self-reinforcing repetition loop (XuEtAl2025a), and region-lifted preference shifts to the surrogate mean at every iteration (YuanEtAl2026). (4) *Agent / orchestrator*: MCP-based agents iteratively invoking an external optimizer (WangEtAl2025), dual-agent warm-start-plus-refinement loops (ChenEtAl2025a), instruction-tuned architecture rankers for cross-domain NAS (HuEtAl2025), and evolution-guided LLM generation of entire BO algorithms (LiEtAl2025c). (5) *Collaborative pipeline/CASH optimizers*, the clearest 2026 trend: CoFEH interleaves LLM tree-of-thought feature engineering with SMAC-derived BO under mutual conditioning (XuEtAl2026a), and LB-MCTS couples BO surrogates and LLM proposers within a shared MCTS state for CASH (XuEtAl2026b). The trajectory over time is telling: 2023–2024 enthusiasm for LLM-as-optimizer (Kannan2023; LiuEtAl2024) gave way in 2024–2026 to sober audits, reproductions, and budget-matched negative results (KristiadiEtAl2024; RychertEtAl2025; GuptaEtAl2025; RodriguesEtAl2026), and then to gated, structured hybrids that assume LLM fallibility by design (ChenYi2026; XuEtAl2026b; KobalczykEtAl2025). Dominant benchmarks remain Bayesmark/HPOBench (LiuEtAl2024; RychertEtAl2025), PMLB (RodriguesEtAl2026), OpenML-derived tabular suites (XuEtAl2026a), and BBOB (LiEtAl2025c); energy-aware NAS benchmarking is emerging (ZhaoEtAl2025).

本領域可依五種 LLM 角色歸類。(1)「暖啟動者／先驗注入者」：零樣本提出初始組態或縮小搜尋空間（LiuEtAl2024；Kannan2023；PatelEtAl2025），包括結構化先驗建構——透過 RAG 將自然語言專家知識轉為 BO 空間上的 Dirichlet 先驗（TopalisEtAl2025），以及結合歷史實驗與 SHAP 後設特徵的零樣本推薦（Bal-GhaouiTiouti2025）。(2)「代理模型／擷取函數元件」：情境內判別式代理模型（LiuEtAl2024；LeiCooper2026）、作為固定特徵擷取器餵入嚴謹貝葉斯代理模型（KristiadiEtAl2024）、微調式 Thompson 抽樣策略（MenetEtAl2025），以及由基礎模型先驗驅動的免似然擷取函數（ChenEtAl2025b）。(3)「古典迴圈內的候選生成者」：LLM-TPE 混合取樣器（MahammadliErtekin2024）、以遺傳演算法介入打破 LLM 自我強化重複迴圈（XuEtAl2025a），以及每次迭代將區域提升偏好注入代理模型均值（YuanEtAl2026）。(4)「代理人／協調者」：基於 MCP、迭代呼叫外部最佳化器的代理（WangEtAl2025）、雙代理暖啟動加精煉迴圈（ChenEtAl2025a）、跨領域 NAS 的指令微調架構排序器（HuEtAl2025），以及以演化策略引導 LLM 生成完整 BO 演算法（LiEtAl2025c）。(5)「協作式管線／CASH 最佳化器」——2026 年最明確的趨勢：CoFEH 在相互條件化機制下交錯 LLM 思維樹特徵工程與源自 SMAC 的 BO（XuEtAl2026a）；LB-MCTS 則在共享 MCTS 狀態中耦合 BO 代理模型與 LLM 提案者以解 CASH（XuEtAl2026b）。時間軌跡頗具啟示性：2023–2024 年對「LLM 即最佳化器」的熱情（Kannan2023；LiuEtAl2024），在 2024–2026 年轉為冷靜稽核、重現研究與預算匹配的否定結果（KristiadiEtAl2024；RychertEtAl2025；GuptaEtAl2025；RodriguesEtAl2026），繼而演化為在設計上即假定 LLM 可能出錯的閘控式結構化混合方法（ChenYi2026；XuEtAl2026b；KobalczykEtAl2025）。主流基準仍為 Bayesmark/HPOBench（LiuEtAl2024；RychertEtAl2025）、PMLB（RodriguesEtAl2026）、OpenML 衍生表格資料集（XuEtAl2026a）與 BBOB（LiEtAl2025c）；能耗感知 NAS 基準正在興起（ZhaoEtAl2025）。

### Key Results / 關鍵發現

| Study | Benchmark/Task | Method | Key Result |
|---|---|---|---|
| LiuEtAl2024 | Bayesmark + HPOBench, 74 HPT tasks (GPT-3.5) | LLAMBO: zero-shot warm-start + ICL surrogate + conditional sampler | Best average regret vs GP-DKL, SKOpt, Optuna-TPE, SMAC3 on 25 public + 5 private/synthetic Bayesmark tasks (5 seeds, 25 trials); warm-start gains largest at trials < 5; GP still best calibrated (coverage ≈ 0.68 target) |
| RychertEtAl2025 | Bayesmark + HPOBench, 30 tasks, 5 runs | LLAMBO reproduction with Llama 3.1 70B | Core claims confirmed: contextual warm-start lowers early regret and variance; SMAC lowest surrogate NRMSE, GP best calibration; Gemma 27B / Llama 3.1 8B backbones fail (invalid JSON, uncorrelated scores) |
| RodriguesEtAl2026 | 8 PMLB tabular datasets × 5 seeds, budget-matched | LLM-OptFlow advisor vs random/TPE/GP-BO/SH, 7-LLM panel | Default seed alone = 88.7% best-CV; LLM adds +0.40 pp CV, −0.01 pp test (p = 0.92); seeded random search overtakes by 12 evals (−0.37 pp); on vehicle, classical search gains +6.5 to +9.1 pp while the advisor stays at default |
| XuEtAl2026a | 28 OpenML/Kaggle tabular datasets, 200-eval budget | CoFEH: interleaved LLM-FE + conditioned BO (SMAC-based) | Joint FE+HPO avg rank 1.75 vs Mindware 3.46; +7.03% error reduction over standalone FE; CASH scenario: up to +45.1% error reduction (airfoil) vs best baseline; FE meta-features raise BO surrogate Spearman ρ from 0.587 to 0.691 |
| Kannan2023 | RegNet fine-tuning on filtered ObjectNet (2,000 images) | GPT-4-suggested search space seeding Hyperopt BO | LLM-configured search space yielded faster validation-loss reduction than random-initialized BO and literature-derived configurations (10 trials × 3 epochs) |
| WangEtAl2025 | Radio-map UAV trajectory/communication (WS-PSO-CM) | DeepSeek-R1 MCP agent tuning 8 hyper-parameters | Minimal sum-rate 22.28 bps/Hz within 6 iterations: +54.33% over human heuristics, +72.61% over uniform random |
| PatelEtAl2025 (abstract) | 6 chemical reaction-yield BO datasets | Survey-prompt + preference-learned LLM utility prior | Zero-shot prior showed modest correlation with true yields; improved initial BO query and enhanced optimization in 4 of 6 datasets |

**Takeaway / 重點:** Evidence converges on LLMs as cheap, capacity-sensitive warm-start and prior-elicitation devices whose benefit is real but front-loaded and sometimes attributable to trivial defaults — while classical BO retains the surrogate role, the strongest 2026 systems (CASH-scale CoFEH, LB-MCTS) gate or condition the LLM rather than trust it. / 證據匯聚顯示：LLM 是廉價但對模型容量敏感的暖啟動與先驗誘出工具，其效益真實但集中於前期、且有時可歸因於平凡的預設組態——代理模型角色仍由古典 BO 擔任，而 2026 年最強系統（CASH 規模的 CoFEH、LB-MCTS）選擇閘控或條件化 LLM，而非盲目信任。
## Theme 2: Warm-Starting and Transfer Learning for Bayesian Optimization / 貝葉斯最佳化的暖啟動與遷移學習

**Papers / 論文:** FeurerEtAl2015, OlsonEtAl2016, HvarfnerEtAl2022, BaiEtAl2023, ChenEtAl2022, LiEtAl2021a, RijnHutter2017, WistubaGrabocka2021, ArangoEtAl2021, NomuraEtAl2021, BalefEtAl2025, LiEtAl2022c, LiEtAl2022d, Vanschoren2019, BalefEggensperger2025, PerroneEtAl2019, HvarfnerEtAl2026, Garouani2025, GijsbersEtAl2021, PfistererEtAl2018, HollmannEtAl2025, BasgaluppEtAl2020, LiEtAl2024, WangEtAl2024, Chakrabarty2022, NguyenEtAl2024, RuedenEtAl2021, BossekEtAl2020

### Consensus / 共識

A decade of evidence converges on one claim: injecting knowledge from prior tasks into Bayesian optimization (BO) yields its largest gains precisely where cold-start search is weakest — the first tens of evaluations in large, conditional search spaces. The founding result is MI-SMBO: initializing SMAC with configurations that succeeded on meta-feature-similar datasets made MI-SMAC significantly better than cold-start SMAC on 35% of 57 OpenML datasets (worse on only 7%) even after 50 evaluations, and the margin over SMAC exceeded SMAC's own margin over random search (20%) — while on a 2-hyperparameter SVM problem plain Spearmint caught up after roughly 10 evaluations (FeurerEtAl2015). The pattern replicates across mechanisms: πBO's prior-weighted acquisition delivered a 12.5× time-to-accuracy speedup on ImageNette (matching vanilla BO's 50-iteration result by iteration 4) and 2.5× on U-Net medical segmentation (HvarfnerEtAl2022); WS-CMA-ES reached better configurations than cold-start CMA-ES within ~25–30 evaluations (NomuraEtAl2021); and on the HPO-B benchmark all four transfer methods tested (FSBO, RGPE, TST-R, TAF-R) significantly outperformed all four non-transfer baselines (ArangoEtAl2021; WistubaGrabocka2021). Even coarse priors help: fANOVA-derived sampling priors beat uniform Hyperband on ~60% of datasets with statistically significant overall improvement (RijnHutter2017), and OpenBox's transfer framework outperformed both Vizier's and non-transfer SMAC3 on 25 leave-one-out LightGBM tasks (LiEtAl2021a). A second, more cautionary consensus is equally firm: transfer helps only in proportion to task similarity, and unguarded transfer can hurt — naive reuse of a source-task distribution degrades drastically when the optimum shifts (NomuraEtAl2021), and Feurer et al.'s own plots show datasets where meta-learning decreased SMAC's performance (FeurerEtAl2015). Consequently, virtually every modern method builds in a decay or safety mechanism: πBO's β/n prior decay with proven vanilla-EI convergence rates (HvarfnerEtAl2022), TransBO's non-decreasing target weight with a no-worse-than-no-transfer guarantee (LiEtAl2022c), and similarity-adaptive region sizing ("safeness") in space transfer (LiEtAl2022d).

十年來的證據匯聚於一個論點：將先前任務的知識注入貝葉斯最佳化（BO），其最大收益恰好出現在冷啟動搜尋最弱之處——大型、具條件結構搜尋空間中的最初數十次評估。奠基性結果是 MI-SMBO：以在元特徵相似資料集上表現良好的組態初始化 SMAC，使 MI-SMAC 在 57 個 OpenML 資料集中的 35% 上顯著優於冷啟動 SMAC（僅 7% 較差），即使在 50 次評估後亦然，且其對 SMAC 的優勢幅度超過 SMAC 對隨機搜尋的優勢（20%）；而在僅有 2 個超參數的 SVM 問題上，原始 Spearmint 約 10 次評估後即迎頭趕上（FeurerEtAl2015）。此模式在各種機制中重現：πBO 的先驗加權擷取函數在 ImageNette 上帶來 12.5 倍的達標時間加速（第 4 次迭代即達到原始 BO 第 50 次迭代的水準），在 U-Net 醫學分割上為 2.5 倍（HvarfnerEtAl2022）；WS-CMA-ES 在約 25–30 次評估內即找到優於冷啟動 CMA-ES 的組態（NomuraEtAl2021）；在 HPO-B 基準上，四種遷移方法（FSBO、RGPE、TST-R、TAF-R）全部顯著優於四種非遷移基線（ArangoEtAl2021; WistubaGrabocka2021）。即使粗糙的先驗也有幫助：由 fANOVA 導出的取樣先驗在約 60% 的資料集上優於均勻 Hyperband，整體改進具統計顯著性（RijnHutter2017）；OpenBox 的遷移框架在 25 個留一法 LightGBM 任務上優於 Vizier 與非遷移的 SMAC3（LiEtAl2021a）。第二項同樣穩固但更具警示性的共識是：遷移的助益與任務相似度成正比，而缺乏防護的遷移可能有害——當最優解偏移時，天真地重用來源任務分布會造成劇烈退化（NomuraEtAl2021），Feurer 等人自己的圖表也顯示元學習在某些資料集上反而降低了 SMAC 的表現（FeurerEtAl2015）。因此，幾乎所有現代方法都內建衰減或安全機制：πBO 的 β/n 先驗衰減並證明保有原始 EI 的收斂速率（HvarfnerEtAl2022）、TransBO 目標權重非遞減且理論上不劣於無遷移（LiEtAl2022c），以及空間遷移中依相似度自適應調整區域大小的「安全性」設計（LiEtAl2022d）。

### Debates / 爭議

The field's central unresolved question is *where* to inject transfer. BaiEtAl2023 formalizes four orthogonal channels — initial-point design, search-space design, surrogate transfer, and acquisition transfer — and explicitly notes that almost no work combines them, leaving the "comprehensive framework" an open problem. Advocates of surrogate transfer point to FSBO's dominance on HPO-B (WistubaGrabocka2021; ArangoEtAl2021); advocates of space pruning counter that it is "universal" — combinable with any optimizer, and additive on top of RGPE/TST (10.1%/22.6% further error reduction) (LiEtAl2022d; PerroneEtAl2019; WangEtAl2024); prior injection claims the practicality high ground because it needs no source observations at all, only a belief distribution (HvarfnerEtAl2022). A second debate concerns similarity estimation: meta-features (46 of them in FeurerEtAl2015) versus on-the-fly ranking-based similarity, with the KDD-line papers arguing meta-features are "often hard to obtain and need careful manual design" (LiEtAl2022c; LiEtAl2022d), and RijnHutter2017 offering priors that need no meta-features. Third, negative transfer is contested at a deeper level than usually admitted: HvarfnerEtAl2026 argues (abstract-only) that the textbook multi-task GP misestimates cross-task correlation even for affinely related tasks — suggesting some reported transfer failures are structural, not merely "dissimilar tasks". Finally, whether sequential warm-started search is needed at all is newly disputed: zero-shot symbolic defaults (GijsbersEtAl2021; PfistererEtAl2018, both abstract-only) and TabPFN's 2.8-second in-context predictions that beat 4-hour tuned ensembles (HollmannEtAl2025) challenge the BO loop itself, while bandit decompositions of CASH sidestep transfer entirely yet beat combined SMAC search early on (BalefEtAl2025). BasgaluppEtAl2020 adds a caution that meta-level procedures can themselves overfit ("meta-overfitting" in Auto-WEKA).

該領域核心的未解問題是遷移應注入*何處*。BaiEtAl2023 將其形式化為四個正交管道——初始點設計、搜尋空間設計、代理模型遷移與擷取函數遷移——並明確指出幾乎沒有工作將其結合，「綜合框架」仍是開放問題。代理模型遷移的支持者以 FSBO 在 HPO-B 上的優勢為據（WistubaGrabocka2021; ArangoEtAl2021）；空間剪枝的支持者則反駁其具「普適性」——可與任何最佳化器結合，且能疊加於 RGPE/TST 之上（進一步降低誤差 10.1%/22.6%）（LiEtAl2022d; PerroneEtAl2019; WangEtAl2024）；先驗注入則主張其實用性最高，因為完全不需要來源觀測，只需一個信念分布（HvarfnerEtAl2022）。第二項爭議是相似度估計：元特徵（FeurerEtAl2015 用了 46 個）對上即時的排序式相似度，KDD 系列論文主張元特徵「往往難以取得且需精細人工設計」（LiEtAl2022c; LiEtAl2022d），RijnHutter2017 則提供無需元特徵的先驗。第三，負遷移的爭論比通常承認的更深層：HvarfnerEtAl2026（僅摘要）指出教科書式多任務 GP 即使在仿射相關的任務上也會錯估跨任務相關性——暗示某些遷移失敗是結構性的，而非僅是「任務不相似」。最後，是否需要序列式暖啟動搜尋本身也開始受質疑：零樣本符號式預設值（GijsbersEtAl2021; PfistererEtAl2018，皆僅摘要）與 TabPFN 以 2.8 秒的情境內預測擊敗調參 4 小時的集成模型（HollmannEtAl2025）挑戰了 BO 迴圈本身，而 CASH 的賭博機分解完全繞過遷移，卻在早期擊敗聯合式 SMAC 搜尋（BalefEtAl2025）。BasgaluppEtAl2020 並警示元層級程序本身也可能過擬合（Auto-WEKA 的「元過擬合」）。

### Dominant Methods / 主流方法

The methodological arc runs from hand-crafted meta-knowledge toward learned, then pretrained, transfer. **2015–2018**: meta-feature distances select warm-start configurations (MI-SMBO; FeurerEtAl2015), complementary default portfolios replace single defaults (PfistererEtAl2018), and fANOVA across OpenML yields importance rankings and value priors (RijnHutter2017), all canonized by the Vanschoren2019 survey. **2019–2021**: transfer moves into BO's structure — learned bounding-box/ellipsoid search spaces (PerroneEtAl2019), KL-matched initial distributions for CMA-ES (NomuraEtAl2021), weighted surrogate ensembles (TST, RGPE) deployed in production services (LiEtAl2021a), and few-shot meta-learned deep-kernel GPs (FSBO; WistubaGrabocka2021), with HPO-B providing the first common large-scale benchmark (ArangoEtAl2021). **2022–2023**: principled aggregation and injection — TransBO learns two-phase transfer weights by constrained optimization (LiEtAl2022c), GPC-based adaptive space design supersedes geometric shapes (LiEtAl2022d), πBO makes arbitrary user priors a one-line acquisition modification (HvarfnerEtAl2022), OptFormer shows a single transformer trained on Google's Vizier database can imitate 7 HPO algorithms and, with EI on its own predictions, outperform GP baselines (ChenEtAl2022); BaiEtAl2023 and RuedenEtAl2021 supply the organizing taxonomies. **2024–2026**: pretrained in-context inference — MCTS-based adaptive space transfer (WangEtAl2024, abstract-only), learning-curve-aware algorithm selection (NguyenEtAl2024, abstract-only), MaxUCB extreme bandits for decomposed CASH (BalefEtAl2025), PFN-based posterior sampling over pipelines (BalefEggensperger2025, abstract-only), and tabular foundation models (HollmannEtAl2025) — alongside a corrective turn scrutinizing multi-task GP foundations (HvarfnerEtAl2026). For EEG-MI pipeline optimization, the transferable lesson is that prior-injection (πBO-style) and few-shot surrogates (FSBO-style) are the two mechanisms proven to work when source tasks (other subjects) are plentiful but target budgets are tens of evaluations.

方法論的軌跡是從手工元知識走向可學習、再到預訓練的遷移。**2015–2018**：以元特徵距離挑選暖啟動組態（MI-SMBO；FeurerEtAl2015）、以互補式預設值組合取代單一預設值（PfistererEtAl2018）、跨 OpenML 的 fANOVA 產生重要性排序與取值先驗（RijnHutter2017），並由 Vanschoren2019 的綜述加以典律化。**2019–2021**：遷移進入 BO 的結構內部——學習式包圍盒／橢球搜尋空間（PerroneEtAl2019）、以 KL 匹配初始化 CMA-ES 分布（NomuraEtAl2021）、加權代理模型集成（TST、RGPE）部署於生產級服務（LiEtAl2021a）、少樣本元學習深度核 GP（FSBO；WistubaGrabocka2021），HPO-B 則提供了首個共同的大規模基準（ArangoEtAl2021）。**2022–2023**：有原則的聚合與注入——TransBO 以約束最佳化學習兩階段遷移權重（LiEtAl2022c）、基於 GPC 的自適應空間設計取代幾何形狀（LiEtAl2022d）、πBO 讓任意使用者先驗成為擷取函數的一行修改（HvarfnerEtAl2022）、OptFormer 顯示在 Google Vizier 資料庫上訓練的單一 Transformer 可模仿 7 種 HPO 演算法，並在自身預測上搭配 EI 後超越 GP 基線（ChenEtAl2022）；BaiEtAl2023 與 RuedenEtAl2021 提供了統整性的分類架構。**2024–2026**：預訓練的情境內推論——基於 MCTS 的自適應空間遷移（WangEtAl2024，僅摘要）、考量學習曲線的演算法選擇（NguyenEtAl2024，僅摘要）、用於分解式 CASH 的 MaxUCB 極值賭博機（BalefEtAl2025）、基於 PFN 的管線後驗取樣（BalefEggensperger2025，僅摘要）、以及表格式基礎模型（HollmannEtAl2025）——同時出現檢視多任務 GP 根基的修正轉向（HvarfnerEtAl2026）。對 EEG-MI 管線最佳化而言，可移植的啟示是：當來源任務（其他受試者）充足而目標預算僅數十次評估時，先驗注入（πBO 式）與少樣本代理模型（FSBO 式）是兩種已被證明有效的機制。

### Key Results / 關鍵發現

| Study | Setting | Transfer Mechanism | Key Result |
|---|---|---|---|
| FeurerEtAl2015 | CASH over scikit-learn (10 hyperparams, 1,623 configs), 57 OpenML datasets | Meta-feature-based warm-start initialization (t=20 configs) of SMAC | After 50 evals: significantly better than cold SMAC on 35% of datasets, worse on 7%; gain over SMAC exceeds SMAC's 20% gain over random search |
| HvarfnerEtAl2022 | DL pipelines (U-Net 6D, ImageNette 6D), 50-iteration budget | πBO: prior-weighted acquisition, decay β/n | 12.5× time-to-accuracy speedup on ImageNette (iter 4 ≥ vanilla BO iter 50); 2.5× on U-Net; final 94.14% vs prior SOTA 93.55%; provably recovers from wrong priors |
| NomuraEtAl2021 | LightGBM/MLP/CNN HPO, transfer across subsets and datasets | KL-minimizing warm start of CMA-ES mean/covariance | Beats cold CMA-ES within ~25–30 evals; gain correlates with γ-similarity; naive ReuseNormal degrades drastically off-similarity |
| WistubaGrabocka2021 | 3 metadata sets (SVM grid, AdaBoost, +1) | Few-shot meta-learned deep-kernel GP surrogate + evolutionary warm-start init | Outperforms all transfer baselines (incl. ABLR, MetaBO) on all 3 metadata sets, statistically significant except vs GP(WS) on AdaBoost |
| ArangoEtAl2021 | HPO-B: 176 meta-datasets from OpenML | Benchmark comparing FSBO, RGPE, TST-R, TAF-R vs 4 non-transfer BO | All transfer methods significantly beat all non-transfer methods; FSBO significantly best, RGPE second |
| LiEtAl2022c | 30 dynamic tuning tasks; NAS-Bench-201 | TransBO: two-phase learned surrogate aggregation | Top-2 on 22.25/30 dynamic tasks; >5× speedup over state-of-the-art NAS methods |
| LiEtAl2022d | RF on 20 OpenML tasks; ResNet on 3 vision tasks; NAS-Bench-201 | GPC-learned promising-region search-space design with similarity-adaptive size | Cuts NCE of second-best (Box+GP) by 23.7% on Tiny-ImageNet; stacked on RGPE/TST reduces NCE a further 10.1%/22.6%; 36.0% better than Ellipsoid+GP at 200 trials |
| RijnHutter2017 | SVM/RF/AdaBoost across 100 OpenML datasets | fANOVA importance + KDE priors from top-10 configs per dataset, fed to Hyperband | Data-driven priors significantly better than uniform sampling; wins on ~60% of datasets |
| LiEtAl2021a | LightGBM on 25 OpenML datasets, leave-one-out | RGPE-based general transfer framework in OpenBox service | Better average rank than Vizier transfer and non-transfer SMAC3 |
| ChenEtAl2022 | Vizier database, RealWorldData, HPO-B, BBOB | OptFormer: text-based transformer imitating HPO policies + EI on predicted function values | Imitates ≥7 HPO algorithms in one model; OptFormer(EI) outperforms all baselines (incl. GP) on both real-data benchmarks |
| BalefEtAl2025 | 4 CASH benchmarks (TabRepo, YaHPOGym, etc.), SMAC as lower-level HPO | MaxUCB extreme bandit over model classes (no cross-task transfer) | Beats combined-search SMAC: 24/0/6 wins/ties/losses on TabRepoRaw; bandit methods superior at small budgets (T=50) |
| HollmannEtAl2025 (abstract) | Tabular classification ≤10k samples | TabPFN: prior-data fitted network, zero-shot in-context learning | In 2.8 s outperforms an ensemble of strongest baselines tuned for 4 h |
| BossekEtAl2020 | EGO on 720 BBOB problems × 40 initial designs | (Cold-start baseline evidence) initial-design size/type study | Small initial designs preferable overall, but every one of 40 designs is best on ≥1 problem — no universal initializer |

**Takeaway / 重點:** Warm-starting reliably converts other tasks' tuning history into a 2–12× early-budget speedup for BO on CASH-scale spaces, provided a decay or similarity-adaptive safeguard bounds negative transfer; the frontier has shifted from meta-feature lookup toward learned priors and pretrained in-context optimizers, which is precisely the channel an LLM-informed or cross-subject warm start for EEG-MI pipelines would exploit.
**重點：** 只要有衰減或相似度自適應的防護機制來抑制負遷移，暖啟動便能可靠地將其他任務的調參歷史轉化為 CASH 規模空間上 BO 的 2–12 倍早期預算加速；研究前沿已從元特徵查表轉向學習式先驗與預訓練的情境內最佳化器，而這正是 LLM 引導或跨受試者暖啟動 EEG-MI 管線所能利用的管道。
## Theme 3: CASH, AutoML Systems, and Benchmarking Infrastructure / CASH、AutoML 系統與基準評測基礎設施

**Papers / 論文:** KotthoffEtAl2019, LiEtAl2016, BischlEtAl2023, LiEtAl2021b, ZollerHuber2019, EggenspergerEtAl2021, EricksonEtAl2020, HutterEtAl2019, BergstraEtAl2015, OlsonMoore2019, LiEtAl2022a, AkibaEtAl2019, YangEtAl2018, ShenEtAl2023, LiuEtAl2019, GijsbersEtAl2019, LiEtAl2020a, RealEtAl2019, FalknerEtAl2018, JinEtAl2019, LindauerEtAl2021, AvvalEtAl2025, TruongEtAl2019, HuEtAl2019, BischlEtAl2021, EggenspergerEtAl2015, GuyonEtAl2019, WeverEtAl2021, KleinEtAl2019, Morales-HernandezEtAl2022, WaringEtAl2020, BarbudoEtAl2023, HeEtAl2019, KleinEtAl2016, LiuEtAl2018, BakerEtAl2016, ZelaEtAl2018, VincentJidesh2023, KarlEtAl2023, LiEtAl2020b, LiEtAl2022b, ElshawiEtAl2019, LiEtAl2025b, XuEtAl2025b, ThomasEtAl2018, ZhongEtAl2025, ChenEtAl2023, DaningEtAl2018

### Consensus / 共識

Four points command broad agreement. First, CASH — jointly selecting an algorithm and its hyperparameters — is the canonical formalization of pipeline optimization, treated as bilevel/black-box optimization solvable by Bayesian optimization (BO) since Auto-WEKA (KotthoffEtAl2019; ElshawiEtAl2019; LindauerEtAl2021; HutterEtAl2019). Second, adaptive optimizers reliably beat naive baselines *on average but not universally*: on HPOBench (12 benchmark families, 100+ problems, 13 optimizers, 32 seeds), 4/5 black-box optimizers significantly outperformed random search by sign test, yet only 2/4 multi-fidelity methods beat plain Hyperband, and even advanced methods lost to random search on individual benchmarks (EggenspergerEtAl2021; FalknerEtAl2018; BischlEtAl2023). Third, multi-fidelity/early-stopping gains concentrate in the low-budget regime: Hyperband is 5–30× faster than BO (LiEtAl2016), BOHB reaches Hyperband's final quality up to 100× faster (FalknerEtAl2018), Optuna's ASHA pruning raised evaluated trials from 35.8 to 1,278.6 in equal wall-time (AkibaEtAl2019), but black-box methods catch up given large budgets (EggenspergerEtAl2021; LiEtAl2020b; KleinEtAl2016). Fourth, for large end-to-end spaces, *decomposition beats joint search*: VolcanoML outperforms auto-sklearn on 25/30 classification and 17/20 regression tasks and its rank advantage grows with search-space size (avg rank 1.65 vs 3.57 at 100 hyperparameters) (LiEtAl2021b; LiEtAl2022a); alternating BO+bandit (Rising Bandits) yields ~12.6× fewer trials than SMAC (LiEtAl2020a); ADMM decomposition wins on 50% of datasets vs auto-sklearn's 27% and TPOT's 20% (LiuEtAl2019; HuEtAl2019). Finally, warm-starting from prior tasks is already embedded in mature systems — auto-sklearn's meta-learning, VolcanoML's RGPE/RankNet transfer, OBOE's collaborative-filtering cold-start — and consistently reported as beneficial (GijsbersEtAl2019; LiEtAl2022a; YangEtAl2018; DaningEtAl2018), directly motivating this review's question of whether LLM-derived priors can substitute for such history-based transfer.

四點獲得廣泛共識。第一，CASH（同時選擇演算法與其超參數）是管線最佳化的典範形式化，自 Auto-WEKA 以來被視為可由貝葉斯最佳化（BO）求解的雙層／黑箱最佳化問題（KotthoffEtAl2019; ElshawiEtAl2019; LindauerEtAl2021; HutterEtAl2019）。第二，自適應最佳化器**平均而言**可靠地勝過樸素基線，但並非普遍如此：在 HPOBench（12 個基準家族、100+ 問題、13 個最佳化器、32 個隨機種子）上，符號檢定顯示 4/5 的黑箱最佳化器顯著優於隨機搜尋，但僅 2/4 的多保真方法勝過純 Hyperband，且進階方法在個別基準上甚至輸給隨機搜尋（EggenspergerEtAl2021; FalknerEtAl2018; BischlEtAl2023）。第三，多保真／提早停止的增益集中於低預算階段：Hyperband 比 BO 快 5–30 倍（LiEtAl2016），BOHB 以最快 100 倍達到 Hyperband 的最終品質（FalknerEtAl2018），Optuna 的 ASHA 剪枝使等時評估試驗數從 35.8 增至 1,278.6（AkibaEtAl2019），但在大預算下黑箱方法會趕上（EggenspergerEtAl2021; LiEtAl2020b; KleinEtAl2016）。第四，對大型端到端空間而言，**分解優於聯合搜尋**：VolcanoML 在 25/30 分類與 17/20 迴歸任務上勝過 auto-sklearn，且其排名優勢隨搜尋空間增大而擴大（100 個超參數時平均排名 1.65 對 3.57）（LiEtAl2021b; LiEtAl2022a）；交替式 BO+bandit（Rising Bandits）比 SMAC 少約 12.6 倍試驗（LiEtAl2020a）；ADMM 分解在 50% 資料集上最佳，對比 auto-sklearn 的 27% 與 TPOT 的 20%（LiuEtAl2019; HuEtAl2019）。最後，基於先前任務的暖啟動已內建於成熟系統——auto-sklearn 的元學習、VolcanoML 的 RGPE/RankNet 遷移、OBOE 的協同過濾冷啟動——且一致被報告為有益（GijsbersEtAl2019; LiEtAl2022a; YangEtAl2018; DaningEtAl2018），這直接支撐本綜述關於 LLM 先驗能否替代此類基於歷史之遷移的核心問題。

### Debates / 爭議

**Joint vs. decomposed search spaces.** Joint-space BO with structure-aware surrogates (random forests handling conditional hierarchies) remains the SMAC3/auto-sklearn position (LindauerEtAl2021; KotthoffEtAl2019; BergstraEtAl2015), but the decomposition camp shows joint BO degrades as dimensionality grows — SMAC's accuracy on PC4 fell from 95.02% to 93.63% as candidate algorithms grew from 1 to 16, while Rising Bandits held 95.02% (LiEtAl2020a; LiuEtAl2019; LiEtAl2021b; WeverEtAl2021). Which decomposition (conditioning, alternating, ADMM, cascaded bandits) is best remains unsettled, and automatic "plan generation" is explicitly open (LiEtAl2021b; HuEtAl2019). **Is CASH even necessary?** AutoGluon achieved rank 1.84 vs auto-sklearn's 3.81 with *no* CASH, relying on multi-layer stacking (EricksonEtAl2020), and ThomasEtAl2018 argues a single well-tuned learner class often suffices; conversely DivBO and PSEO contend the search itself should be made ensemble/diversity-aware (ShenEtAl2023; XuEtAl2025b). **How much does the optimizer matter?** Zöller & Huber found CASH optimizers statistically indistinguishable on most of 114 datasets (mean differences <1.9%; random search not worse), and CASH solvers beat full AutoML frameworks on 48% of shared datasets despite 5× less time (ZollerHuber2019; GijsbersEtAl2019 similarly found no consistently best system and cases where none beat a tuned random forest). **Benchmark validity** is itself contested: synthetic functions are deemed unsuited to CASH (ZollerHuber2019), surrogate/tabular benchmarks trade realism for statistical power (EggenspergerEtAl2015; KleinEtAl2019; EggenspergerEtAl2021), anytime-performance curves are missing from most comparisons, and meta-learning contamination of test datasets is an unresolved fairness issue (GijsbersEtAl2019).

**聯合 vs. 分解搜尋空間。** 以結構感知代理模型（隨機森林處理條件層級）的聯合空間 BO 仍是 SMAC3/auto-sklearn 的立場（LindauerEtAl2021; KotthoffEtAl2019; BergstraEtAl2015），但分解陣營顯示聯合 BO 隨維度增長而退化——當候選演算法從 1 增至 16 時，SMAC 在 PC4 上的準確率由 95.02% 降至 93.63%，而 Rising Bandits 維持 95.02%（LiEtAl2020a; LiuEtAl2019; LiEtAl2021b; WeverEtAl2021）。哪種分解（條件化、交替式、ADMM、串接式 bandit）最佳仍無定論，自動「執行計畫生成」被明確列為開放問題（LiEtAl2021b; HuEtAl2019）。**CASH 是否必要？** AutoGluon 完全不做 CASH、僅靠多層堆疊即達平均排名 1.84（auto-sklearn 為 3.81）（EricksonEtAl2020），ThomasEtAl2018 主張單一強學習器類別往往已足夠；反之 DivBO 與 PSEO 主張搜尋本身應具集成／多樣性感知（ShenEtAl2023; XuEtAl2025b）。**最佳化器有多重要？** Zöller & Huber 發現在 114 個資料集上多數 CASH 最佳化器統計上無法區分（平均差異 <1.9%；隨機搜尋不遜色），且 CASH 求解器在 48% 共同資料集上以五分之一時間勝過完整 AutoML 框架（ZollerHuber2019；GijsbersEtAl2019 同樣發現無一致最佳系統，且存在無系統勝過調參隨機森林的案例）。**基準效度**本身亦有爭議：合成函數被認為不適用於 CASH（ZollerHuber2019），代理／表格式基準以真實性換取統計檢定力（EggenspergerEtAl2015; KleinEtAl2019; EggenspergerEtAl2021），多數比較缺乏任意時間（anytime）性能曲線，而元學習對測試資料集的污染仍是未解的公平性問題（GijsbersEtAl2019）。

### Dominant Methods / 主流方法

The 2015→2026 trajectory shows four waves. (1) **Joint BO backbones (2013–2016):** SMAC-style random-forest BO and TPE powering Auto-WEKA, Hyperopt-sklearn, and auto-sklearn (KotthoffEtAl2019; BergstraEtAl2015; LindauerEtAl2021), with evolutionary pipeline search as the main alternative (TPOT, significantly better than a basic analysis on 21/150 tasks; OlsonMoore2019; VincentJidesh2023) and NAS branching off via RL, differentiable relaxation, morphism-guided BO, and regularized evolution (BakerEtAl2016; LiuEtAl2018; JinEtAl2019; RealEtAl2019; ZelaEtAl2018; AvvalEtAl2025; LiEtAl2025b; ZhongEtAl2025). (2) **Bandits and multi-fidelity (2016–2020):** Hyperband/successive halving (LiEtAl2016), hybridized with model-based sampling in BOHB, MFES-HB, Fabolas, and Hyper-Tune (FalknerEtAl2018; LiEtAl2020b; KleinEtAl2016; LiEtAl2022b), and productized as ASHA pruning in define-by-run Optuna (AkibaEtAl2019). (3) **Decomposition for CASH at scale (2019–2022):** ADMM operator splitting (LiuEtAl2019), cascaded ER-UCB bandits (HuEtAl2019), alternating Rising Bandits (LiEtAl2020a), and VolcanoML's composable joint/conditioning/alternating blocks with RGPE- and RankNet-based meta-learned transfer (LiEtAl2021b; LiEtAl2022a). (4) **Ensemble-centric and benchmark-mature era (2020–2026):** stack-ensembling without CASH (EricksonEtAl2020), diversity-aware and stacking-optimized CASH (ShenEtAl2023; XuEtAl2025b), evolved program-space search (ChenEtAl2023), and consolidated infrastructure — OpenML-based AutoML Benchmark, HPOBench containers, surrogate/meta-surrogate benchmarking, and the ChaLearn challenges (GijsbersEtAl2019; EggenspergerEtAl2021; EggenspergerEtAl2015; KleinEtAl2019; GuyonEtAl2019) — alongside surveys codifying best practice and multi-objective extensions (BischlEtAl2023; BischlEtAl2021; ZollerHuber2019; KarlEtAl2023; Morales-HernandezEtAl2022; HeEtAl2019; BarbudoEtAl2023; ElshawiEtAl2019; TruongEtAl2019; WaringEtAl2020; HutterEtAl2019).

2015→2026 的軌跡呈現四波浪潮。（1）**聯合 BO 骨幹（2013–2016）：** SMAC 式隨機森林 BO 與 TPE 驅動 Auto-WEKA、Hyperopt-sklearn 與 auto-sklearn（KotthoffEtAl2019; BergstraEtAl2015; LindauerEtAl2021），演化式管線搜尋為主要替代方案（TPOT 在 150 個任務中的 21 個顯著優於基礎分析；OlsonMoore2019; VincentJidesh2023），NAS 則經由強化學習、可微鬆弛、網路態射引導之 BO 與正則化演化分支發展（BakerEtAl2016; LiuEtAl2018; JinEtAl2019; RealEtAl2019; ZelaEtAl2018; AvvalEtAl2025; LiEtAl2025b; ZhongEtAl2025）。（2）**Bandit 與多保真（2016–2020）：** Hyperband／逐次減半（LiEtAl2016），與模型式取樣混合為 BOHB、MFES-HB、Fabolas 與 Hyper-Tune（FalknerEtAl2018; LiEtAl2020b; KleinEtAl2016; LiEtAl2022b），並在 define-by-run 的 Optuna 中以 ASHA 剪枝產品化（AkibaEtAl2019）。（3）**大規模 CASH 分解（2019–2022）：** ADMM 算子分裂（LiuEtAl2019）、串接式 ER-UCB bandit（HuEtAl2019）、交替式 Rising Bandits（LiEtAl2020a），以及 VolcanoML 可組合的 joint/conditioning/alternating 區塊，佐以 RGPE 與 RankNet 元學習遷移（LiEtAl2021b; LiEtAl2022a）。（4）**集成中心與基準成熟期（2020–2026）：** 無 CASH 的堆疊集成（EricksonEtAl2020）、多樣性感知與堆疊最佳化的 CASH（ShenEtAl2023; XuEtAl2025b）、演化程式空間搜尋（ChenEtAl2023），以及整合的基礎設施——基於 OpenML 的 AutoML Benchmark、HPOBench 容器、代理／元代理基準與 ChaLearn 挑戰賽（GijsbersEtAl2019; EggenspergerEtAl2021; EggenspergerEtAl2015; KleinEtAl2019; GuyonEtAl2019）——並有彙整最佳實務與多目標延伸的綜述（BischlEtAl2023; BischlEtAl2021; ZollerHuber2019; KarlEtAl2023; Morales-HernandezEtAl2022; HeEtAl2019; BarbudoEtAl2023; ElshawiEtAl2019; TruongEtAl2019; WaringEtAl2020; HutterEtAl2019）。

### Key Results / 關鍵發現

| Study | Benchmark | System/Method | Key Result |
|---|---|---|---|
| LiEtAl2021b | 60 OpenML datasets, auto-sklearn space | VolcanoML (decomposed execution plan) | Beats auto-sklearn on 25/30 CLS & 17/20 REG tasks; avg rank 1.65 vs 3.57 (auto-sklearn) on 100-HP space; Higgs 27.2% test error in 4h vs baselines' 24h |
| LiEtAl2020a | 30 OpenML datasets; 16 algorithms, 78 HPs | Rising Bandits (alternating BO+MAB) | Best validation accuracy on 26/30 datasets; avg 12.6× fewer trials than SMAC; holds 95.02% as K grows 1→16 while SMAC drops to 93.63%; up to 15.7× speedup vs BOHB |
| LiuEtAl2019 | 30 binary UCI/OpenML datasets | ADMM decomposition | Best on 50% of datasets vs auto-sklearn 27% / TPOT 20%; >10× speedup, ~10% improvement in many cases |
| ShenEtAl2023 | 15 OpenML datasets, 100-HP space | DivBO (diversity-aware CASH) | Best avg ranks 1.82 (val) / 1.73 (test) of 10 methods; best test error on 10/15 datasets; 1.52–2.53× speedup vs RB-ES/BO-ES |
| EricksonEtAl2020 | 39 AutoML Benchmark + 11 Kaggle datasets, 4h | AutoGluon (stacking, no CASH) | Avg rank 1.84 vs TPOT 3.38 / auto-sklearn 3.81 / Auto-WEKA 5.09; champion on 23/39; beats 99.3% of Kaggle teams (otto, 24h) |
| FalknerEtAl2018 | Surrogate + DL/RL benchmarks | BOHB (Hyperband + TPE) | Up to 55× speedup over random search; matches Hyperband's final quality 100× faster; CIFAR-10 2.78%±0.09% in 33 GPU-days |
| LiEtAl2016 | Deep-learning & kernel HPO tasks | Hyperband | 5–30× faster than popular BO methods; order-of-magnitude speedup over competitor set |
| EggenspergerEtAl2021 | HPOBench: 12 families, 100+ problems, 13 optimizers, 32 seeds | Containerized multi-fidelity suite | 4/5 black-box optimizers beat RS (sign test p<0.05); only 2/4 multi-fidelity beat HB; MF advantage significant at 1% budget, vanishing at 100% |
| GijsbersEtAl2019 | 39 OpenML datasets, 1h/4h, 10-fold CV | Open AutoML Benchmark (4 systems) | No system consistently best; on dionis/helena all frameworks worse than tuned random forest; Auto-WEKA overfits at 4h |
| ZollerHuber2019 | 114 datasets (CASH) / 73 (frameworks) | Benchmark + survey | CASH optimizers within 1.9% mean accuracy of each other (random search not worse); frameworks within 2.2%; CASH beats frameworks on 48% of shared datasets in 12 min vs 1h |
| AkibaEtAl2019 | Simplified AlexNet on SVHN, 4h | Optuna (ASHA pruning) | 1,278.6 trials with pruning vs 35.8 without (TPE); ASHA outperforms median pruning; linear scaling to 8 workers |
| YangEtAl2018 | Midsize OpenML/UCI datasets | OBOE (collaborative filtering + D-optimal design) | D-optimal cold-start beats A-/E-optimal and Alors; >70% of ensembles ≤5 learners; matches AutoML peers faster under time constraints |
| DaningEtAl2018 | L2-regularized logistic regression | accSMBO (gradient GP + meta-acquisition) | 140–300% faster epoch-wise convergence than SMAC |

**Takeaway / 重點：** Decomposition, multi-fidelity scheduling, and history-based warm-starting each deliver roughly order-of-magnitude trial savings over cold-start joint BO, yet benchmark studies suggest final-accuracy differences between mature optimizers are small — implying that for subject-specific EEG pipelines the decisive lever is likely *acceleration to a good region* (where LLM or meta-learned priors act), not the asymptotic optimizer choice. / 分解、多保真排程與基於歷史的暖啟動各自相對冷啟動聯合 BO 帶來約一個數量級的試驗節省，但基準研究顯示成熟最佳化器之間的最終精度差異很小——這暗示對受試者特定的 EEG 管線而言，決定性的槓桿很可能是**加速抵達良好區域**（LLM 或元學習先驗作用之處），而非漸近的最佳化器選擇。
# Theme 4 note on evidence level / 證據層級說明
*All 11 papers in this theme were available at abstract level only (the four items flagged "full-text" in the manifest resolved to abstract-only notes); claims below are therefore restricted to numbers and statements explicitly reported in abstracts, and should be weighted accordingly.*
*本主題 11 篇論文實際上皆僅有摘要層級的內容（清單中標記為全文的四篇檔案實為摘要），以下論述僅限摘要中明確報告的數字與主張，權重應相應調降。*

## Theme 4: Automated Optimization of EEG Decoding Pipelines / EEG 解碼管線的自動化最佳化

**Papers / 論文:** WangEtAl2026, ZhuEtAl2025, DuanEtAl2023, LiEtAl2025d, MirandaEtAl2022, LiEtAl2025a, BirdLotfi2023, CraikEtAl2019, BirdEtAl2020, NakisaEtAl2018, LeonEtAl2020

### Consensus / 共識

Four claims recur across otherwise heterogeneous papers. First, automated search reliably beats hand-configured EEG models by non-trivial margins: MDH-NAS reports +8.70% accuracy over baselines while cutting architecture-discovery time by 89% (ZhuEtAl2025); differential-evolution HPO of an LSTM adds 14 percentage points, reaching 77.68% for four-quadrant emotion recognition (NakisaEtAl2018); genetic-programming pipelines reach 90.52% versus an 86.48% Gaussian-process baseline for fall-compensation detection (BirdLotfi2023); GA-based HPO "improves accuracy in all models" tested (LeonEtAl2020); GP-AutoML produces solutions both more accurate and more interpretable than ad hoc literature models (MirandaEtAl2022); and FBNAS outperforms six state-of-the-art deep baselines with 79.78% cross-session accuracy on BCIC-IV-2a (WangEtAl2026). Second, inter-subject variability is the field's core justification for automation: WangEtAl2026 and DuanEtAl2023 both argue a universal architecture is impractical and instead search per individual, with DuanEtAl2023 claiming the first per-subject structure-customization analysis. Third, search escapes human priors — optimal hyperparameters lie "well outside the bounds of batch or manual searches" (BirdLotfi2023), and one-convolutional-layer CNNs can match a 6-layer deep belief network, with model size uncorrelated with accuracy (LeonEtAl2020). Fourth, search cost is treated as a first-class constraint, motivating hardware-aware size-constrained search (ZhuEtAl2025), energy-time accounting (LeonEtAl2020), training-free genetic search (LiEtAl2025d), and acknowledgment that DE is computationally expensive (NakisaEtAl2018).

四項主張在異質的論文間反覆出現。第一，自動化搜尋以不容忽視的幅度穩定勝過人工配置的 EEG 模型：MDH-NAS 較基線提升 8.70% 準確率並縮短 89% 的架構搜尋時間 (ZhuEtAl2025)；差分演化對 LSTM 的超參數最佳化帶來 14 個百分點的提升，四象限情緒辨識達 77.68% (NakisaEtAl2018)；基因規劃管線在跌倒代償偵測達 90.52%，優於 86.48% 的高斯過程基線 (BirdLotfi2023)；基於 GA 的超參數最佳化「在所有受測模型上皆提升準確率」(LeonEtAl2020)；GP-AutoML 產生比文獻中特設模型更準確且更可解釋的解 (MirandaEtAl2022)；FBNAS 在 BCIC-IV-2a 跨會期達 79.78%，勝過六個最先進深度基線 (WangEtAl2026)。第二，受試者間變異是此領域自動化的核心理由：WangEtAl2026 與 DuanEtAl2023 皆主張通用架構不切實際而改為逐受試者搜尋，DuanEtAl2023 並自稱首次進行逐受試者結構客製化分析。第三，搜尋能跳脫人類先驗——最佳超參數「遠超出批次或手動搜尋的範圍」(BirdLotfi2023)，單卷積層 CNN 可比肩六層深度信念網路，且模型大小與準確率無關 (LeonEtAl2020)。第四，搜尋成本被視為一級約束，催生了硬體感知的尺寸受限搜尋 (ZhuEtAl2025)、能耗–時間權衡分析 (LeonEtAl2020)、免訓練的基因搜尋 (LiEtAl2025d)，以及對 DE 計算昂貴的坦承 (NakisaEtAl2018)。

### Debates / 爭議

The sharpest tension is whether searched complexity pays. LeonEtAl2020 shows deliberately shallow, GA-tuned networks matching deeper models and attributes deep models' struggles to overfitting on small EEG datasets — an implicit challenge to the NAS programs of WangEtAl2026, ZhuEtAl2025, and DuanEtAl2023, which spend substantial compute discovering deep architectures. A second debate concerns optimizer family: this EEG corpus is dominated by evolutionary methods, and in the only head-to-head comparison, DE beat PSO, simulated annealing, random search, and TPE (NakisaEtAl2018) — striking because Bayesian optimization (SMAC-style CASH) never appears as a primary engine in any of the 11 papers, only as a defeated baseline. Third, per-subject versus pooled search: per-subject NAS multiplies compute by cohort size (WangEtAl2026, DuanEtAl2023), while cross-task shared search spaces (DuanEtAl2023) and low-code fixed pipelines (LiEtAl2025a) amortize it; the abstracts do not resolve which is cost-effective. Fourth, interpretability: GP camps stress readable pipelines (MirandaEtAl2022, BirdLotfi2023) against black-box differentiable NAS (ZhuEtAl2025). Critically for the present research question, **no paper in this theme combines LLM guidance or warm-started/meta-learned search initialization with EEG pipeline optimization** — the nearest neighbors are weight-level transfer (+29.95 points from EMG-pretrained initialization; BirdEtAl2020) and training-free proxies derived from pretrained networks (LiEtAl2025d), both of which transfer model parameters or signals, not optimizer priors or configurations. Zero-shot *configuration* transfer is untested here.

最尖銳的張力在於搜尋出的複雜度是否划算。LeonEtAl2020 顯示刻意淺層、GA 調校的網路可比肩更深的模型，並將深度模型的困境歸因於小型 EEG 資料集上的過擬合——這隱含挑戰了 WangEtAl2026、ZhuEtAl2025 與 DuanEtAl2023 花費大量算力搜尋深層架構的路線。第二項爭議是最佳化器流派：本 EEG 文獻集由演化式方法主導，且在唯一的正面比較中，DE 勝過 PSO、模擬退火、隨機搜尋與 TPE (NakisaEtAl2018)——值得注意的是，貝葉斯最佳化（SMAC 式 CASH）在 11 篇論文中從未作為主要引擎出現，僅以被擊敗的基線登場。第三，逐受試者對池化搜尋：逐受試者 NAS 使算力隨群體規模倍增 (WangEtAl2026, DuanEtAl2023)，而跨任務共享搜尋空間 (DuanEtAl2023) 與低程式碼固定管線 (LiEtAl2025a) 則可攤提成本；摘要層級無法判定何者更具成本效益。第四，可解釋性：GP 陣營強調可讀的管線 (MirandaEtAl2022, BirdLotfi2023)，與黑箱可微分 NAS (ZhuEtAl2025) 相對。對本研究問題至關重要的是：**本主題沒有任何論文將 LLM 引導或暖啟動／元學習的搜尋初始化與 EEG 管線最佳化結合**——最接近的是權重層級遷移（EMG 預訓練初始化帶來 +29.95 個百分點；BirdEtAl2020）與源自預訓練網路的免訓練代理 (LiEtAl2025d)，兩者遷移的都是模型參數或訊號，而非最佳化器先驗或組態。零樣本「組態」遷移在此完全未被檢驗。

### Dominant Methods / 主流方法

What gets searched splits into three tiers: (i) neural architectures — filter-bank temporal cells with dilated convolutions (WangEtAl2026), DARTS-style mixed-level cells under explicit size constraints (ZhuEtAl2025), cross-task cell spaces (DuanEtAl2023), and spiking CNN/LSTM topologies (LiEtAl2025d); (ii) hyperparameters of fixed families — LSTM (NakisaEtAl2018), shallow CNN/FFNN/RNN (LeonEtAl2020), MLP/CNN topologies (BirdEtAl2020); and (iii) full pipelines spanning feature extraction, dimensionality reduction, and classifier choice (MirandaEtAl2022, BirdLotfi2023) — the closest analog to CASH, though never framed as such. Optimizers are overwhelmingly evolutionary (GA, DE, GP, hyperheuristic multi-objective, training-free genetic search: six of eleven papers), with differentiable NAS (ZhuEtAl2025) and multi-path NAS (WangEtAl2026) as the gradient-based minority; BO appears only as a TPE baseline (NakisaEtAl2018). Benchmarks center on BCI Competition IV-2a (WangEtAl2026, ZhuEtAl2025), plus OpenBMI/SEED (WangEtAl2026), PhysioNet EEGMMI on-device (85.37%; ZhuEtAl2025), emotion corpora FACED/DEAP/DREAMER (LiEtAl2025d), and bespoke wearable datasets (NakisaEtAl2018, BirdEtAl2020). Evaluation protocols vary — cross-session (WangEtAl2026), within-subject (LiEtAl2025a), embedded deployment (ZhuEtAl2025) — hindering direct comparison; CraikEtAl2019's review of 90 studies supplies the manual-design baseline (CNN/RNN/DBN outperform SAE/MLP) these searches aim to automate away.

搜尋對象分三層：(i) 神經架構——含膨脹卷積的濾波器組時間單元 (WangEtAl2026)、顯式尺寸約束下的 DARTS 式混合層級單元 (ZhuEtAl2025)、跨任務單元空間 (DuanEtAl2023)、脈衝 CNN/LSTM 拓撲 (LiEtAl2025d)；(ii) 固定模型族的超參數——LSTM (NakisaEtAl2018)、淺層 CNN/FFNN/RNN (LeonEtAl2020)、MLP/CNN 拓撲 (BirdEtAl2020)；(iii) 涵蓋特徵萃取、降維與分類器選擇的完整管線 (MirandaEtAl2022, BirdLotfi2023)——最接近 CASH 的類比，但從未以此框架表述。最佳化器壓倒性地屬演化式（GA、DE、GP、超啟發式多目標、免訓練基因搜尋：11 篇中佔 6 篇），梯度式屬少數（可微分 NAS ZhuEtAl2025；多路徑 NAS WangEtAl2026）；貝葉斯最佳化僅以 TPE 基線出現 (NakisaEtAl2018)。基準集中於 BCI Competition IV-2a (WangEtAl2026, ZhuEtAl2025)，另有 OpenBMI/SEED (WangEtAl2026)、裝置端 PhysioNet EEGMMI（85.37%；ZhuEtAl2025）、情緒語料 FACED/DEAP/DREAMER (LiEtAl2025d) 與自建穿戴式資料集 (NakisaEtAl2018, BirdEtAl2020)。評估協定各異——跨會期 (WangEtAl2026)、受試者內 (LiEtAl2025a)、嵌入式部署 (ZhuEtAl2025)——阻礙直接比較；CraikEtAl2019 對 90 篇研究的回顧（CNN/RNN/DBN 優於 SAE/MLP）則提供了這些搜尋欲取代的人工設計基線。

### Key Results / 關鍵發現

| Study | Dataset | Search Target | Optimizer | Key Result |
|---|---|---|---|---|
| WangEtAl2026 | BCIC-IV-2a, OpenBMI, SEED | Per-subject filter-bank CNN architecture | Multi-path NAS | Cross-session 79.78% / 70.66% / 68.38%; beats 6 SOTA deep baselines |
| ZhuEtAl2025 | BCI-IV, MODMA, PhysioNet EEGMMI | Lightweight architecture, size-constrained | Mixed-level differentiable NAS | 87.80% (BCI-IV), 90.09% (MODMA); −89% search time, +8.70% acc.; 85.37% on EAIDK-610 board |
| DuanEtAl2023 | MI + emotion tasks | Cross-task architecture, per-subject variants | Constrained NAS | SOTA on MI and emotion; first per-subject structure analysis (no numbers in abstract) |
| LiEtAl2025d | FACED, DEAP, DREAMER | Spiking CNN + spiking LSTM architecture | Training-free genetic search | Outperforms SOTA SNN methods (no numbers in abstract) |
| MirandaEtAl2022 | EEG time-series classification | End-to-end pipeline (features + model) | Genetic programming | More accurate and interpretable than ad hoc literature models |
| LiEtAl2025a | EEG-fNIRS MI; EEG-ECG sleep | Low-code multimodal pipeline framework | None (framework) | 95.5% within-subject MI; 80.2% sleep staging, deployed as executable |
| BirdLotfi2023 | Fall-compensation EEG | DNN topology + ML pipeline | Evolutionary algorithms + GP | GP 90.52% vs 86.48% GP baseline; 0.3 ms inference for 89.79% pipeline |
| BirdEtAl2020 | Muse EEG / Myo EMG | MLP/CNN hyperparameters + weight init | Hyperheuristic multi-objective EA | EEG 62.37% → 93.82% (+29.95 pts) with EMG-pretrained weights |
| NakisaEtAl2018 | Wearable EEG+BVP emotion | LSTM hyperparameters | DE (vs PSO, SA, random, TPE) | +14% accuracy; best 77.68%; DE beats all incl. TPE |
| LeonEtAl2020 | EEG motor imagery | Shallow CNN/FFNN/RNN hyperparameters | Genetic algorithm | GA improves all models; 1-conv-layer CNN ≥ 6-layer DBN |
| CraikEtAl2019 | Review of 90 studies | — (manual design survey) | — | CNN/RNN/DBN > SAE/MLP; per-task hyperparameter guidance |

**Takeaway / 重點:** Automated search consistently adds several accuracy points to EEG decoding and can shrink search cost by an order of magnitude (ZhuEtAl2025, NakisaEtAl2018, BirdLotfi2023), but the field runs on evolutionary and differentiable engines, evaluates per-subject NAS without warm-starting, and contains no LLM-informed or Bayesian-CASH work — the exact intersection this research question targets is empty.
自動化搜尋穩定為 EEG 解碼增加數個百分點的準確率，並可將搜尋成本縮減一個數量級 (ZhuEtAl2025, NakisaEtAl2018, BirdLotfi2023)；但此領域仰賴演化式與可微分引擎，逐受試者 NAS 皆無暖啟動，且完全沒有 LLM 引導或貝葉斯 CASH 的工作——本研究問題所瞄準的交集正是空白。
## Theme 5: Cross-Subject Transfer and Subject-Specific Adaptation in EEG / EEG 跨受試者遷移與受試者特定調適

**Papers / 論文:** CooneyEtAl2020, SchirrmeisterEtAl2017, AristimunhaEtAl2025, DingEtAl2024, HuangEtAl2025, AnarakiEtAl2024, WuEtAl2022, BerdyshevEtAl2024, ZhangEtAl2024, WeiEtAl2023, KapitonovaBall2024

### Consensus / 共識

Inter-subject variability is treated across this corpus as the defining obstacle to deployable EEG decoding (BerdyshevEtAl2024; ZhangEtAl2024; WuEtAl2022; AnarakiEtAl2024; AristimunhaEtAl2025). Its magnitude is concrete: ZhangEtAl2024 traced cross-subject misclassifications to outlier individuals whose zero-crossing rate (218,213.6 vs. a training-set mean of 6,930.1, a ~30-fold gap) and SD (206,475.2 vs. 2,880.4) dwarfed group statistics, while BerdyshevEtAl2024 found even meta-learned zero-shot four-class MI accuracy plateaus at 43% ± 7% on BCI IV 2a — far below the ~84% deep ConvNets reach with full subject-specific training (SchirrmeisterEtAl2017). A second consensus is that transfer from other subjects genuinely reduces calibration cost: EEG-Reptile reached 46% ± 5% (BCI IV 2a) and 72% ± 7% (Lee2019 MI) after fine-tuning on only 16 samples, significantly outperforming plain transfer learning (p < 0.05, Wilcoxon), and the tutorial evidence in WuEtAl2022 (abstract-only) likewise attributes large calibration savings to TL applied across multiple pipeline components. Third, optimal configurations are subject- and dataset-dependent: SSA-optimized random-forest hyperparameters diverged from defaults (DTN 24–50 vs. the empirical 30) and yielded +1.62% (DEAP) and +9.85% (SEED) average gains (ZhangEtAl2024), consistent with the claim that HPO has been neglected yet consequential in DL-EEG (CooneyEtAl2020, abstract-only) and that the best classifier varies per individual (AnarakiEtAl2024, abstract-only).

跨受試者變異性在本主題文獻中被視為 EEG 解碼落地部署的核心障礙（BerdyshevEtAl2024; ZhangEtAl2024; WuEtAl2022; AnarakiEtAl2024; AristimunhaEtAl2025）。其量級相當具體：ZhangEtAl2024 將跨受試者誤判追溯至離群個體——其過零率（218,213.6，訓練集平均僅 6,930.1，相差約 30 倍）與標準差（206,475.2 對 2,880.4）遠超群體統計；BerdyshevEtAl2024 則發現即使經元學習，BCI IV 2a 四類 MI 的零樣本準確率仍停留在 43% ± 7%——遠低於深度 ConvNet 在完整受試者特定訓練下可達的約 84%（SchirrmeisterEtAl2017）。第二項共識是：來自其他受試者的遷移確實能降低校正成本——EEG-Reptile 僅用 16 筆樣本微調即達 46% ± 5%（BCI IV 2a）與 72% ± 7%（Lee2019 MI），顯著優於一般遷移學習（p < 0.05, Wilcoxon）；WuEtAl2022（僅摘要）的教學性證據亦將大幅校正節省歸因於施加在多個管線元件的 TL。第三，最佳組態依受試者與資料集而異：SSA 優化的隨機森林超參數偏離預設值（DTN 24–50 對經驗值 30），在 DEAP 與 SEED 分別帶來平均 +1.62% 與 +9.85% 的增益（ZhangEtAl2024），這與「HPO 在 DL-EEG 中被忽視但影響重大」（CooneyEtAl2020，僅摘要）以及「最佳分類器因人而異」（AnarakiEtAl2024，僅摘要）的主張一致。

### Debates / 爭議

The sharpest tension is zero-calibration versus subject-specific adaptation. HuangEtAl2025 (abstract-only) claims zero-shot generalization retaining 93.7% of within-subject classification performance, and AristimunhaEtAl2025 institutionalizes zero-shot decoding of unseen subjects as a community challenge; yet BerdyshevEtAl2024, with full experimental detail, concludes that "meta-learning approaches still fall short of the standards required for reliable integration into a BCI system" — its few-shot gains over zero-shot were modest (43%→46%; 71%→72%), suggesting neither pole is settled. A second debate concerns what should transfer: model weights and initializations (BerdyshevEtAl2024; DingEtAl2024; HuangEtAl2025), multi-component alignment across the whole signal chain (WuEtAl2022), or configurations themselves — channel sets (WeiEtAl2023, abstract-only) and classifier choices predicted from dataset meta-features (AnarakiEtAl2024). Third, the automation route is contested: per-dataset re-optimization from scratch (ZhangEtAl2024; CooneyEtAl2020) versus meta-learned transfer (BerdyshevEtAl2024) versus LLM-generated decoders as a "novel class of AutoML" (KapitonovaBall2024) — the latter promising but demonstrably below state of the art without expert steering.

最尖銳的張力在於「零校正」對上「受試者特定調適」。HuangEtAl2025（僅摘要）宣稱零樣本泛化可保留 93.7% 的受試者內分類效能，AristimunhaEtAl2025 更將對未見受試者的零樣本解碼制度化為社群挑戰賽；然而具備完整實驗細節的 BerdyshevEtAl2024 結論是「元學習方法仍未達到可靠整合進 BCI 系統所需的標準」——其少樣本相對零樣本的增益有限（43%→46%；71%→72%），顯示兩端立場皆未有定論。第二項爭議是「該遷移什麼」：模型權重與初始化（BerdyshevEtAl2024; DingEtAl2024; HuangEtAl2025）、貫穿整條訊號鏈的多元件對齊（WuEtAl2022），或組態本身——通道集合（WeiEtAl2023，僅摘要）與由資料集後設特徵預測的分類器選擇（AnarakiEtAl2024）。第三，自動化路線亦有分歧：每個資料集從零重新優化（ZhangEtAl2024; CooneyEtAl2020）、元學習遷移（BerdyshevEtAl2024），或將 LLM 生成解碼器視為「新型 AutoML」（KapitonovaBall2024）——後者有潛力，但在缺乏專家引導下明顯低於最先進水準。

### Dominant Methods / 主流方法

Transfer of MODEL PARAMETERS dominates decisively. Six of eleven papers move weights or representations across subjects: Reptile meta-initialization with outlier-subject filtering (BerdyshevEtAl2024), pretrained individual-adaptation modules (HuangEtAl2025), architecture-level neurophysiological priors (DingEtAl2024), foundation-model-style pretraining at 3,000+ subject scale (AristimunhaEtAl2025), and alignment-plus-weights pipelines (WuEtAl2022). Transfer of CONFIGURATIONS is rare and mostly implicit: WeiEtAl2023 transfers channel selections across subjects, AnarakiEtAl2024 predicts a personalized classifier from structural dataset characteristics — the closest analogue to meta-feature-based warm-starting — and BerdyshevEtAl2024 quietly transfers fine-tuning hyperparameters (learning rate and an epochs-vs-data-size schedule) selected on a non-target subject. Critically, no paper in this theme transfers search histories, pipeline configurations, or surrogate priors to warm-start an optimization run for a new subject: ZhangEtAl2024 re-runs its sparrow-search HPO cold on every dataset, and BerdyshevEtAl2024's own automated HPO costs ~24 h on a Tesla P100 per setting. Config-level cross-subject transfer for CASH-style search therefore remains an open seam in this literature.

模型「參數」的遷移佔絕對主導。11 篇中有 6 篇在受試者之間搬移權重或表徵：帶離群受試者過濾的 Reptile 元初始化（BerdyshevEtAl2024）、預訓練的個體調適模組（HuangEtAl2025）、架構層級的神經生理先驗（DingEtAl2024）、逾 3,000 名受試者規模的基礎模型式預訓練（AristimunhaEtAl2025），以及對齊加權重的管線（WuEtAl2022）。「組態」的遷移則罕見且多屬隱性：WeiEtAl2023 跨受試者遷移通道選擇；AnarakiEtAl2024 由資料集結構特性預測個人化分類器——這是最接近基於後設特徵暖啟動的類比；BerdyshevEtAl2024 則不張揚地遷移了在非目標受試者上選定的微調超參數（學習率與「訓練輪數對資料量」排程）。關鍵在於：本主題沒有任何論文遷移搜尋歷史、管線組態或代理模型先驗來為新受試者的優化暖啟動——ZhangEtAl2024 在每個資料集上都冷啟動重跑麻雀搜尋 HPO，而 BerdyshevEtAl2024 自身的自動化 HPO 在 Tesla P100 上每組設定約需 24 小時。因此，面向 CASH 式搜尋的組態層級跨受試者遷移，在此文獻中仍是一道未被填補的縫隙。

### Key Results / 關鍵發現

| Study | Dataset | Transfer Mechanism | Key Result |
|---|---|---|---|
| BerdyshevEtAl2024 | BCI IV 2a (4-class); Lee2019 MI (2-class) | Reptile meta-initialization of weights + Optuna HPO; fine-tune HPs chosen on non-target subject | Zero-shot 43% ± 7% / 71% ± 5%; 16-sample fine-tune 46% ± 5% / 72% ± 7%; beats plain TL (p < 0.05); HPO ≈ 24 h (Tesla P100) |
| ZhangEtAl2024 | DEAP (2-class); SEED (3-class) | None — cold per-dataset SSA re-optimization of RF hyperparameters | 76.81% (DEAP) / 75.96% (SEED) avg; +1.62% and +9.85% over default RF; optimal DTN 24–50 vs. default 30 |
| SchirrmeisterEtAl2017 | MI/executed-movement EEG (within-subject) | None — subject-specific training baseline | Deep ConvNets 84.0% vs. FBCSP 82.1% mean decoding accuracy |
| HuangEtAl2025 | Multi-dataset EEG visual decoding | Pretrained Individual Adaptation Module (weights) | Retains 93.7% of within-subject classification and 92.4% of reconstruction quality zero-shot; SSIM 0.352 cross-task |
| KapitonovaBall2024 | BCI IV 2a | LLM (GPT-4o) generates decoder + training loop | Working CNN in <10 prompts; above chance but below reported SoA (FBCSP 67.8%; up to 97.61% claimed elsewhere) |
| AristimunhaEtAl2025 | HBN-style corpus, 128 ch, 3,000+ subjects | Zero-shot cross-subject/cross-task challenge (weights) | Multi-terabyte benchmark established; generalization posed as unsolved |

**Takeaway / 重點:** Cross-subject transfer in EEG overwhelmingly means transferring model weights; the few-shot ceiling remains low for MI (46% four-class), and configuration/search knowledge is re-derived from scratch for each new subject or dataset — precisely the cold-start cost that meta-learned or LLM-informed warm-starting of CASH would target. / EEG 的跨受試者遷移幾乎等同於權重遷移；MI 的少樣本天花板仍低（四類 46%），而組態／搜尋知識在每個新受試者或資料集上都得從零重建——這正是元學習或 LLM 引導的 CASH 暖啟動所要削減的冷啟動成本。
---

## Cross-Theme Analysis / 跨主題分析

### Methodological Trends Over Time / 方法學時間趨勢

The collection traces a clear arc. 2015–2019 built the CASH/HPO foundations: meta-feature warm-starting (FeurerEtAl2015), bandit-based multi-fidelity methods (LiEtAl2016, FalknerEtAl2018), and system-level AutoML (Auto-WEKA, TPOT, auto-sklearn lineage in Theme 3). 2020–2022 shifted toward structure and priors: search-space decomposition (LiEtAl2020a, LiEtAl2021b), transfer-BO consolidation (LiEtAl2022c, BaiEtAl2023), and user-belief priors (HvarfnerEtAl2022). 2023–2026 is the LLM period: from feasibility probes (Kannan2023) through LLM-as-surrogate (LiuEtAl2024) to budget-matched skepticism and hybrid conditioning designs (RodriguesEtAl2026, XuEtAl2026a). The EEG themes lag this arc: Theme 4's optimizers are still predominantly evolutionary (6/11 papers), and BO-based pipeline search common in Theme 3 has not crossed over.

文獻集呈現清晰的發展弧線。2015–2019 年建立 CASH/HPO 基礎：meta-feature 暖啟動（FeurerEtAl2015）、bandit 式多保真方法（LiEtAl2016, FalknerEtAl2018）與系統級 AutoML（主題三的 Auto-WEKA、TPOT、auto-sklearn 一系）。2020–2022 年轉向結構與先驗：搜尋空間分解（LiEtAl2020a, LiEtAl2021b）、遷移 BO 的整合（LiEtAl2022c, BaiEtAl2023）與使用者信念先驗（HvarfnerEtAl2022）。2023–2026 年是 LLM 時期：從可行性探測（Kannan2023）、LLM 作為代理模型（LiuEtAl2024），到預算對齊的質疑與混合條件化設計（RodriguesEtAl2026, XuEtAl2026a）。EEG 主題落後於此弧線：主題四的最佳化器仍以演化式為主（11 篇中 6 篇），主題三常見的 BO 式管線搜尋尚未跨界過來。

### Converging Findings / 匯聚發現

Three findings converge across themes. (1) **Initialization dominates search refinement**: MI-SMAC's gain over cold-start SMAC exceeded SMAC's gain over random search (FeurerEtAl2015, Theme 2); πBO reaches vanilla BO's iteration-50 accuracy by iteration 4 given only a default-centered prior (HvarfnerEtAl2022); and budget-matched LLM advisor gains reduce largely to a strong first configuration (RodriguesEtAl2026, Theme 1). (2) **Decomposition scales where joint search stalls**: Rising Bandits and VolcanoML both widen their advantage over joint BO as spaces grow (LiEtAl2020a, LiEtAl2021b, Theme 3). (3) **Transfer needs a safety mechanism**: wrong-prior recovery in πBO, similarity-gated warm-starting (NomuraEtAl2021), and structurally biased multi-task surrogates (HvarfnerEtAl2026) all point to decay/gating as a prerequisite for robust transfer — directly relevant to transferring priors across EEG subjects with high inter-subject variability (Theme 5).

三項發現跨主題匯聚。（1）**初始化的貢獻壓過搜尋精煉**：MI-SMAC 相對冷啟動 SMAC 的增益超過 SMAC 相對隨機搜尋的增益（FeurerEtAl2015，主題二）；πBO 僅憑以預設值為中心的先驗，在第 4 次迭代即達到原始 BO 第 50 次迭代的準確率（HvarfnerEtAl2022）；預算對齊下 LLM 顧問的增益大部分可化約為一個強的首個配置（RodriguesEtAl2026，主題一）。（2）**分解式搜尋在聯合搜尋停滯處持續擴展**：Rising Bandits 與 VolcanoML 都隨空間增大而擴大對聯合 BO 的優勢（LiEtAl2020a, LiEtAl2021b，主題三）。（3）**遷移需要安全機制**：πBO 的錯誤先驗恢復、相似度門控暖啟動（NomuraEtAl2021）、多任務代理模型的結構性偏誤（HvarfnerEtAl2026），皆指向衰減／門控是穩健遷移的前提——這與跨受試者變異極大的 EEG 情境（主題五）直接相關。

### Diverging Findings / 分歧發現

Two live tensions cut across themes. First, **does the optimizer even matter?** Theme 3 contains both the strongest pro-decomposition evidence and the strongest null result: across 114 datasets CASH optimizers were statistically indistinguishable and random search was not worse (ZollerHuber2019), while AutoGluon outperformed CASH-centric frameworks with no CASH at all (EricksonEtAl2020). This suggests optimizer gains are regime-dependent — largest in large, conditional spaces under tight budgets, precisely the subject-specific EEG regime. Second, **is automated search worth it on small EEG data?** Theme 4 reports both large NAS gains (+8.70% accuracy with 89% search-time reduction, ZhuEtAl2025) and a counterexample where a GA-tuned single-conv-layer CNN matched a six-layer DBN (LeonEtAl2020), with overfitting of searched models on small datasets a recurring worry (CooneyEtAl2020 flags untested statistical significance of HPO effects in DL-EEG).

兩個活躍的張力橫跨主題。第一，**最佳化器究竟重不重要？** 主題三同時包含最強的支持分解證據與最強的虛無結果：在 114 個資料集上各 CASH 最佳化器統計上無法區分、隨機搜尋不劣（ZollerHuber2019），而 AutoGluon 完全不做 CASH、僅靠堆疊即勝過 CASH 中心的框架（EricksonEtAl2020）。這顯示最佳化器的增益依情境而定——在大型、條件式空間與緊預算下最大，而這正是受試者特定 EEG 的情境。第二，**在小樣本 EEG 資料上自動搜尋值得嗎？** 主題四同時報告大幅 NAS 增益（準確率 +8.70%、搜尋時間 −89%，ZhuEtAl2025）與反例（GA 調校的單卷積層 CNN 追平六層 DBN，LeonEtAl2020），且搜尋所得模型在小資料集上的過擬合是反覆出現的疑慮（CooneyEtAl2020 指出 DL-EEG 中 HPO 效果的統計顯著性仍未被檢驗）。

### Theme Interactions / 主題交互

The bridge structure of the collection points in one direction. Theme 1's most effective designs inject LLM knowledge *into* Theme 2/3 machinery rather than replacing it (LiuEtAl2024 → πBO-style priors; XuEtAl2026a → FE-conditioned SMAC), and Theme 3's systems name Theme 2's mechanisms as their roadmap (VolcanoML's RGPE extension, LiEtAl2022a). The EEG side supplies the missing application: BerdyshevEtAl2024 (Theme 5) already transfers *meta-learned model initializations* across subjects and implicitly reuses fine-tuning hyperparameters chosen on non-target subjects, while AnarakiEtAl2024 predicts personalized classifiers from dataset characteristics — the two closest precedents to cross-subject *configuration* transfer, neither of which touches the search loop itself. KapitonovaBall2024 (LLM agents operating BCI toolchains) provides the interface through which LLM priors could reach EEG pipelines, and its own future-work section calls for exactly the LLM-driven HPO/NAS this session's research question proposes.

文獻集的橋接結構指向同一方向。主題一最有效的設計是把 LLM 知識「注入」主題二／三的機制，而非取而代之（LiuEtAl2024 → πBO 式先驗；XuEtAl2026a → FE 條件化 SMAC）；而主題三的系統把主題二的機制列為自己的路線圖（VolcanoML 的 RGPE 擴充，LiEtAl2022a）。EEG 端則補上缺少的應用場景：BerdyshevEtAl2024（主題五）已跨受試者遷移 meta-learning 的模型初始化，並隱含地重用在非目標受試者上選出的微調超參數；AnarakiEtAl2024 則由資料集特徵預測個人化分類器——這是與跨受試者「配置」遷移最接近的兩個先例，但都未觸及搜尋迴圈本身。KapitonovaBall2024（操作 BCI 工具鏈的 LLM agent）提供了 LLM 先驗進入 EEG 管線的介面，其未來工作章節正好呼籲本研究問題所提議的 LLM 驅動 HPO/NAS。

---

## Paper–Theme Mapping / 論文主題對照

| Citation Key / 引用鍵 | Theme / 主題 | Methodology / 方法學 | Bridge? / 跨主題? |
|----------------------|-------------|---------------------|------------------|
| `LiuEtAl2024` | T1 | experimental | — |
| `RodriguesEtAl2026` | T1 | experimental | T2 |
| `RychertEtAl2025` | T1 | experimental | — |
| `XuEtAl2026a` | T1 | engineering | T3 |
| `HuEtAl2025` | T1 | engineering | — |
| `MahammadliErtekin2024` | T1 | experimental | — |
| `ChenYi2026` | T1 | theoretical | — |
| `GuptaEtAl2025` | T1 | experimental | — |
| `ZhaoEtAl2025` | T1 | engineering | — |
| `XuEtAl2026b` | T1 | engineering | T3 |
| `TopalisEtAl2025` | T1 | engineering | T2 |
| `Bal-GhaouiTiouti2025` | T1 | experimental | T2 |
| `YuanEtAl2026` | T1 | engineering | — |
| `ChenEtAl2025a` | T1 | engineering | — |
| `LiEtAl2025c` | T1 | computational | — |
| `PatelEtAl2025` | T1 | experimental | — |
| `KristiadiEtAl2024` | T1 | experimental | — |
| `SaadallahEtAl2026` | T1 | engineering | T2 |
| `XuEtAl2025a` | T1 | experimental | — |
| `KobalczykEtAl2025` | T1 | engineering | — |
| `MenetEtAl2025` | T1 | theoretical | — |
| `LeiCooper2026` | T1 | experimental | — |
| `SrinivasanMenzies2026` | T1 | experimental | — |
| `ChenEtAl2025b` | T1 | engineering | — |
| `Kannan2023` | T1 | experimental | — |
| `WangEtAl2025` | T1 | engineering | — |
| `FeurerEtAl2015` | T2 | experimental | — |
| `OlsonEtAl2016` | T2 | engineering | T3 |
| `HvarfnerEtAl2022` | T2 | experimental | — |
| `BaiEtAl2023` | T2 | review | — |
| `ChenEtAl2022` | T2 | computational | T1 |
| `LiEtAl2021a` | T2 | engineering | T3 |
| `RijnHutter2017` | T2 | observational | — |
| `WistubaGrabocka2021` | T2 | experimental | T5 |
| `ArangoEtAl2021` | T2 | engineering | — |
| `NomuraEtAl2021` | T2 | experimental | — |
| `BalefEtAl2025` | T2 | experimental | T3 |
| `LiEtAl2022c` | T2 | experimental | — |
| `LiEtAl2022d` | T2 | experimental | — |
| `Vanschoren2019` | T2 | review | — |
| `BalefEggensperger2025` | T2 | computational | T1 |
| `PerroneEtAl2019` | T2 | experimental | — |
| `HvarfnerEtAl2026` | T2 | theoretical | — |
| `Garouani2025` | T2 | review | — |
| `GijsbersEtAl2021` | T2 | computational | — |
| `PfistererEtAl2018` | T2 | computational | — |
| `HollmannEtAl2025` | T2 | computational | T1 |
| `BasgaluppEtAl2020` | T2 | experimental | T3 |
| `LiEtAl2024` | T2 | experimental | — |
| `WangEtAl2024` | T2 | computational | — |
| `Chakrabarty2022` | T2 | computational | T5 |
| `NguyenEtAl2024` | T2 | computational | — |
| `RuedenEtAl2021` | T2 | review | — |
| `BossekEtAl2020` | T2 | experimental | — |
| `KotthoffEtAl2019` | T3 | engineering | — |
| `LiEtAl2016` | T3 | theoretical | — |
| `BischlEtAl2023` | T3 | review | — |
| `LiEtAl2021b` | T3 | engineering | — |
| `ZollerHuber2019` | T3 | computational | — |
| `EggenspergerEtAl2021` | T3 | computational | — |
| `EricksonEtAl2020` | T3 | engineering | — |
| `HutterEtAl2019` | T3 | review | — |
| `BergstraEtAl2015` | T3 | engineering | — |
| `OlsonMoore2019` | T3 | engineering | — |
| `LiEtAl2022a` | T3 | engineering | T2 |
| `AkibaEtAl2019` | T3 | engineering | — |
| `YangEtAl2018` | T3 | computational | T2 |
| `ShenEtAl2023` | T3 | computational | — |
| `LiuEtAl2019` | T3 | computational | — |
| `GijsbersEtAl2019` | T3 | computational | — |
| `LiEtAl2020a` | T3 | computational | — |
| `RealEtAl2019` | T3 | computational | — |
| `FalknerEtAl2018` | T3 | computational | — |
| `JinEtAl2019` | T3 | engineering | — |
| `LindauerEtAl2021` | T3 | engineering | — |
| `AvvalEtAl2025` | T3 | review | — |
| `TruongEtAl2019` | T3 | computational | — |
| `HuEtAl2019` | T3 | theoretical | — |
| `BischlEtAl2021` | T3 | review | — |
| `EggenspergerEtAl2015` | T3 | computational | — |
| `GuyonEtAl2019` | T3 | computational | — |
| `WeverEtAl2021` | T3 | computational | — |
| `KleinEtAl2019` | T3 | computational | T2 |
| `Morales-HernandezEtAl2022` | T3 | review | — |
| `WaringEtAl2020` | T3 | review | T4 |
| `BarbudoEtAl2023` | T3 | review | — |
| `HeEtAl2019` | T3 | review | — |
| `KleinEtAl2016` | T3 | computational | — |
| `LiuEtAl2018` | T3 | computational | — |
| `BakerEtAl2016` | T3 | computational | — |
| `ZelaEtAl2018` | T3 | computational | — |
| `VincentJidesh2023` | T3 | computational | — |
| `KarlEtAl2023` | T3 | review | — |
| `LiEtAl2020b` | T3 | computational | — |
| `LiEtAl2022b` | T3 | engineering | — |
| `ElshawiEtAl2019` | T3 | review | — |
| `LiEtAl2025b` | T3 | computational | — |
| `XuEtAl2025b` | T3 | computational | — |
| `ThomasEtAl2018` | T3 | computational | — |
| `ZhongEtAl2025` | T3 | computational | — |
| `ChenEtAl2023` | T3 | computational | T1 |
| `DaningEtAl2018` | T3 | computational | T2 |
| `WangEtAl2026` | T4 | computational | — |
| `ZhuEtAl2025` | T4 | computational | — |
| `DuanEtAl2023` | T4 | computational | — |
| `LiEtAl2025d` | T4 | computational | — |
| `MirandaEtAl2022` | T4 | computational | T2 |
| `LiEtAl2025a` | T4 | engineering | — |
| `BirdLotfi2023` | T4 | computational | — |
| `CraikEtAl2019` | T4 | review | — |
| `BirdEtAl2020` | T4 | experimental | T3 |
| `NakisaEtAl2018` | T4 | experimental | — |
| `LeonEtAl2020` | T4 | computational | — |
| `CooneyEtAl2020` | T5 | computational | T2 |
| `SchirrmeisterEtAl2017` | T5 | computational | — |
| `AristimunhaEtAl2025` | T5 | engineering | — |
| `DingEtAl2024` | T5 | computational | — |
| `HuangEtAl2025` | T5 | computational | — |
| `AnarakiEtAl2024` | T5 | computational | T3 |
| `WuEtAl2022` | T5 | review | — |
| `BerdyshevEtAl2024` | T5 | engineering | — |
| `ZhangEtAl2024` | T5 | computational | T2 |
| `WeiEtAl2023` | T5 | computational | — |
| `KapitonovaBall2024` | T5 | engineering | T1 |
---

Files / 檔案: `step6_sota_review.md`, `step6_knowledge_graph.canvas`
Next step / 下一步: `/research-gaps`
