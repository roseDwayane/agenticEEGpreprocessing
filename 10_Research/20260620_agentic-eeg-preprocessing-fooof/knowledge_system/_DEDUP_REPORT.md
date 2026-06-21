# Dedup Report / 去重報告

> Session: 20260620_agentic-eeg-preprocessing-fooof
> Generated: 2026-06-21

## 環境 / Environment

**No `20_Knowledge/10-Points/` tree exists in this repository.** Cross-session dedup is, by design, limited to the promoted main library (`20_Knowledge/10-Points/`) — staging areas in other sessions are never scanned. Since that library is absent, **zero fuzzy-match dedup was possible: every draft card is NEW.**

本 repo 不存在 `20_Knowledge/10-Points/` 主庫，依規則跨 session 去重僅針對主庫，故**無法做 fuzzy-match 去重——所有草稿卡皆為全新**。

## 高信度撞名（≥0.85 fuzzy match）/ High-confidence name collisions
（無主庫可比對 / no main library to match against）— **none**

## 中信度（待確認）/ Medium-confidence
**none**

## 既存可引用的 Knowledge 卡 / Existing Knowledge cards to link at promote time
**none yet.** When the user first promotes from this session, these 18 Points become the seed of `20_Knowledge/10-Points/`. Future sessions will dedup against them.

## Promote-time 借鏡 / Borderline folds flagged for review
These were folded into other cards but may deserve their own P cards at promote time:
- **ASR / rASR** (BlumEtAl2019) — currently inside [[P_draft_HAPPE]] action space
- **SOUND** (MutanenEtAl2018) — abstract-only, mention <2; left out
- **SPRiNT** (WilsonEtAl2022) — folded into [[P_draft_FOOOF]]
- **AutoGen / HuggingGPT** — folded into [[P_draft_LLM_DS_Agent]] as substrate

## Estimated promote workload / 預估 promote 工作量
- 18 Points × ~4 min (review fields, assign P{NN}, apply frontmatter patches) ≈ **72 min**
- 4 Lines × ~5 min ≈ **20 min**
- 5 Planes × ~6 min ≈ **30 min**
- 1 Body × ~10 min ≈ **10 min**
- **Total ≈ 2.2 hours** for a careful first promote (one-time; later sessions reuse these as dedup targets).
