---
citation_key: "WangEtAl2024"
title: "Monte Carlo Tree Search based Space Transfer for Black-box Optimization"
authors: "Shukuan Wang; Ke Xue; Lei Song; Xiaobin Huang; Chao Qian"
year: 2024
doi: ""
source: "abstract-only"
access_level: "abstract-only"
retrieved_date: "2026-08-15"
---

# Monte Carlo Tree Search based Space Transfer for Black-box Optimization

**Authors**: Shukuan Wang, Ke Xue, Lei Song, Xiaobin Huang, Chao Qian
**Year**: 2024

> **Note / 備註**: Not in the full-text priority set (top-composite + manually included); fetch on demand. Try institutional access: https://arxiv.org/abs/2412.07186
> 全文未能以自動化開放管道取得，請嘗試機構存取。

---

## Abstract

Bayesian optimization (BO) is a popular method for computationally expensive black-box optimization. However, traditional BO methods need to solve new problems from scratch, leading to slow convergence. Recent studies try to extend BO to a transfer learning setup to speed up the optimization, where search space transfer is one of the most promising approaches and has shown impressive performance on many tasks. However, existing search space transfer methods either lack an adaptive mechanism or are not flexible enough, making it difficult to efficiently identify promising search space during the optimization process. In this paper, we propose a search space transfer learning method based on Monte Carlo tree search (MCTS), called MCTS-transfer, to iteratively divide, select, and optimize in a learned subspace. MCTS-transfer can not only provide a well-performing search space for warm-start but also adaptively identify and leverage the information of similar source tasks to reconstruct the search space during the optimization process. Experiments on synthetic functions, real-world problems, Design-Bench and hyper-parameter optimization show that MCTS-transfer can demonstrate superior performance compared to other search space transfer methods under different settings. Our code is available at \url{https://github.com/lamda-bbo/mcts-transfer}.
