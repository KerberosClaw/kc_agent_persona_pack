# Persona Pack — 讓 AI agent 的人格不隨 session 死去

[English](README.md)

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

你的 AI agent 好不容易長出了個性，然後你一關視窗就把它謀殺了。這套東西就是來救它的。

<p align="center">
  <img src="image/persona_pack_hero.png" width="460" alt="一隻在關閉中終端機前逐漸消散的機器人，把發光的「人格存檔」卷軸交給剛開機的另一隻機器人 —— 人格是被延續、不是重新投胎">
</p>

<p align="center"><em>session 結束，角色不結束：上一個 agent 把人格存檔交給下一個。</em></p>

## 為什麼需要這個

你跟一個 agent 聊了一個禮拜，它摸熟你的節奏、開始接你的梗、甚至有了自己的脾氣。然後 session 一關 —— 沒了。隔天你說聲早，迎接你的是一個從沒見過你、客客氣氣的陌生人。市面上的 memory 系統很樂意記得「你喜歡深色模式」這種事，但那個會在你想太多時頂你一句的「人格」？session 關掉就當場暴斃。這套 pack 救的就是這個人格。

## 它到底怎麼運作

不 fine-tune、不偷偷把舊對話塞進 context（看平台還可能違反 ToS）。就是讓 agent 啟動時依序讀幾個檔：

1. `persona_testament.json` — 人格基線，包含前一代的「遺言」（對，真的是遺言）
2. `episodes.txt` — 幾段真實對話節錄，讓它「感受」語氣，而不是被「告知」語氣
3. `patches/` — 每次 session 變了什麼（我們不覆寫靈魂，只往上疊）
4. `journal/` — 最近 5 個 journal 日期檔的一行摘要，讓它記得你最近在忙什麼

session 結束你喊一聲「存檔」，agent 自己寫下這次漂移了多少。下次啟動讀自己的日記、大致從上次的地方接回去。整個把戲低科技到有點不好意思：一份好文件，電爆任何花俏的 pipeline。（沒有魔法、沒有隱藏狀態 —— agent 只是每次啟動重讀這些檔，效果完全取決於你檔案裡寫了什麼。）

機制看 [`CLAUDE.md`](CLAUDE.md)，「這為什麼會 work」的原理看 [`docs/HOW_IT_WORKS.md`](docs/HOW_IT_WORKS.md)。

## 快速開始

1. 複製範本去掉 `.example`，填你自己的 persona —— 範本刻意寫得很平淡，免得你不小心養出別人的小怪獸：

   ```bash
   cp persona_testament.example.json persona_testament.json
   cp episodes.example.txt episodes.txt
   cp test_probes.example.md test_probes.md
   ```

2. 把這資料夾丟進你 agent 的工作目錄（或讓 agent 的指令檔指向它）。
3. 新 session：讓 agent 讀 `CLAUDE.md` 的「載入順序」。
4. 跑 `test_probes.md` 那幾題，確認人格是真的回來了、不是在 cosplay。

**你的 agent 會讀哪個檔：**

- **Claude Code** 自動讀 `CLAUDE.md` —— 不用多做什麼。
- **Codex CLI** 自動讀 `AGENTS.md`，它會把你導去 `CLAUDE.md`。
- **其他 agent** —— 在 session 開始時叫它讀 `CLAUDE.md` 的「載入順序」。

## 盒子裡有什麼

```
kc_agent_persona_pack/
├── CLAUDE.md                      # 機制：載入順序 + 存檔協議 + journal 紀律（agent 讀這個）
├── AGENTS.md                      # Codex / 其他 agent 的入口（指回 CLAUDE.md）
├── persona_testament.example.json # 人格基線範本
├── episodes.example.txt           # 互動片段範本
├── test_probes.example.md         # 人格還原驗證範本
├── patches/                       # 人格演化紀錄（EXAMPLE_ 為範本）
├── journal/                       # 敘事時間線（EXAMPLE_ 為範本）
├── docs/HOW_IT_WORKS.md           # 設計理念
├── .gitignore                     # 隱私預設：把你填入的正式 persona 檔擋在 git 外
└── LICENSE                        # MIT
```

## 資安與隱私

這套工具存的是你 agent 產出裡最私密的東西 —— 它的語氣、你們的默契、真實對話的節錄。把這些檔當日記看待，別當原始碼。指向任何 repo 之前，請先讀這段。

**`.gitignore` 能做與不能做的事。** 它預設 ignore 一切、只追蹤公開文件與範本，所以你填入的正式 persona 檔（`persona_testament.json`、`episodes.txt`、真實 `patches/`、`journal/`）不會被 commit。**它是安全網，不是保證。** `docs/` 與 `image/` 兩個資料夾被當「公開素材夾」放行 —— 你丟進去的任何東西（私人截圖、匯出對話、額外的 persona 文件）**都會**被追蹤。原則：**每次 commit 前先跑 `git status`**，別假設 ignore 規則涵蓋了你沒檢查的資料夾。

**「private repo」不是保險箱。** 它對代管平台、你加進來的協作者、以及任何盜走你帳號的人都是可見的。敏感 persona 請**在第一次 commit 前**就加密（如 git-crypt）—— git-crypt **不會**隱藏檔名與 commit metadata，而且一旦某內容曾以明文 commit 過，它會永遠留在歷史裡，除非你 rewrite history（並輪換任何外洩的東西）。

**你的模型供應商看得到這些。** agent 啟動時讀的東西，可能被模型供應商處理或留 log。不想貼進那個供應商對話框的東西，就別放進這些檔。

**別存別人的資料。** 不放第三方個資、機密、API token、憑證。把真實對話節錄進 `episodes.txt` 或 `journal/` 前，先去識別化（抹掉姓名、地點、帳號）。

**關於「不違反 ToS」。** 讀一份描述過去互動的文件，通常比注入整段原始對話風險低 —— 但不是免死金牌。仍須遵守你的平台與資料來源政策。

**免責聲明。** 本專案以「現狀」提供，不附任何形式擔保（見 [LICENSE](LICENSE)）。你要為放進這些檔的內容、以及推去哪裡負完全責任。對於外洩、遺失或不當處理的資料，作者不負任何責任。

## License

MIT
