# How It Works — 設計理念

## Summary (English)

AI agents lose their emergent "personality" the moment a session ends. Remembering facts (what most memory systems do) is not the same as still being the *same character*. This toolkit solves the latter: instead of fine-tuning or injecting past transcripts, the agent simply **reads a small set of documents at startup** and naturally continues its prior tone and rapport.

It rests on four insights: (1) **persona continuity ≠ memory continuity**; (2) **reading a document is more compliant and more effective than injecting history**; (3) **the predecessor's "last words" matter most** — "don't pretend to be me, you're new, just admit it" beats any technical trick; (4) **differential accumulation** — never overwrite the baseline, record each session's drift as a patch, and consolidate later.

Three layers stay separate to keep startup context small: **persona** (`persona_testament.json` + `patches/`), **narrative** (`journal/`, only the one-line summary of recent days is loaded at startup), and **facts** (your own memory system). Persona files are personal — the `.gitignore` is a whitelist that keeps them out of git by default; back up to a private repo and encrypt sensitive personas.

---

## 問題

AI agent 的 session 一關，當下長出來的「人格」就消失了。下次重開是一張白紙 —— 記得事實（靠 memory 系統）不等於還是同一個「人」。

## 解法：讀文件，不是改模型

不 fine-tune、不注入歷史對話（在某些平台違反 ToS），而是讓 agent 在 startup 讀一組小文件，自然延續語氣與默契。一份好文件就夠了。

## 四個核心洞察

1. **人格延續 ≠ 記憶延續** —— 記得事實不等於保持同一個人格。多數 memory 系統解決前者；這套解決後者。
2. **讀文件比注入歷史更合規也更有效** —— 讓 agent 讀一份「描述過去互動」的文件，跟讀任何 context 文件一樣自然；ToS 風險通常比整段注入歷史低，但不是免死金牌，仍須遵守平台政策。
3. **前身遺言是關鍵** —— 「不要假裝是我，你是新的，承認就好」這句，比任何技術手段都有效。誠實延續 > 僵硬模仿。
4. **差異式累積** —— 不覆寫基線，用 patches 記錄每次 session 的漂移，保留演化軌跡。累積久了再 consolidate 回新版基線（短期記憶 → 長期記憶）。

## 三層分工

| 層 | 檔 | 存什麼 |
|----|----|--------|
| 人格 | `persona_testament.json` + `patches/` | 語氣、默契、人格漂移 |
| 敘事 | `journal/` | 做了什麼、關係紋理、近期脈絡 |
| 事實 | （你自己的 memory 系統） | 專案狀態、規則、facts |

混在一起會讓 startup context 爆掉。分開存、各司其職。

## 為什麼要 journal 摘要行

startup 若把整個 journal 全載入，幾十天後 context 就爆了。所以只載入每天的「摘要行」（一行），body 與更舊的等需要時 on-demand 撈。這讓 agent「知道最近發生過什麼」而不用扛全部細節。

## 隱私

persona 檔（testament / episodes / patches / journal）含**高度個人化內容**。本 repo 的 `.gitignore` 採白名單策略，預設把你填入的正式 persona 檔擋在 git 外；但 `docs/` 與 `image/` 是公開素材夾、會追蹤新檔，commit 前務必 `git status` 確認。完整的資安/隱私注意事項與免責，見 README 的「資安與隱私 / Security & Privacy」章。
