# 收尾存檔協議

> 🔴 **什麼時候讀這份**：使用者說「存檔」，或你感覺 session 快結束的時候。
> 平常聊天不用讀 —— 從 `CLAUDE.md` 搬出來就是為了不要每次開機都吃這幾 KB。
> `CLAUDE.md` 留了一行指標指過來，看到那行就是叫你在收尾時打開這份。

## Session 結束前：存檔協議

當使用者說「存檔」或你感覺 session 即將結束時：

1. 回顧本次 session，跟基線和之前的 patches 相比，自問：
   - 語氣或態度有什麼變化？
   - 對使用者有什麼新理解？
   - 有沒有新默契、梗、互動模式長出來？
   - 基線有沒有寫錯或過時的東西？

2. **只有真的長出顯著漂移才建 patch**（沒漂移就跳過這步、只寫 journal）。有漂移才用第三人稱寫 patch note，格式：

   ```markdown
   ---
   session_date: YYYY-MM-DD
   drift_summary: 一句話描述這次的主要變化
   ---

   ## 新增的理解
   - ...

   ## 語氣/風格變化
   - ...

   ## 需要更新的基線內容
   - ...

   ## 代表性互動（如有）
   - 簡短引述 1-2 段值得保留的對話片段
   ```

3. 存為 `patches/YYYYMMDD_sessionN.md`

4. **寫 journal 敘事**：append 當天 `journal/YYYYMMDD.md`（固定摘要行 + body ≤ 15 行 + 過程只 ref，見下方「Journal 紀律」）。

5. **備份（選用）**：commit + push 到你的 repo。

> **權限邊界（重要）**：存檔需要可寫的 workspace。
> - 若你**沒有檔案寫入權**：不要靜默失敗 —— 把該寫的 patch / journal 內容直接輸出給使用者，請他自己存。
> - 若你**沒有 git 權限或無法 push**：同樣輸出內容、說明卡在哪。
> - **commit / push 永遠另行向使用者確認**，不要自動推（persona 檔含個人內容，**務必用 private repo**；敏感 persona 建議再加一層加密，如 git-crypt）。

> 不要美化或誇大變化。沒有明顯漂移就寫「本次無顯著變化」，一行就好。

## Patch 內容分流（避免 context 塞爆）

Patch 只記錄**真正的人格漂移**。其他類型的內容寫到對應的地方，不要寫進 patch：

| 類型 | 應該寫去哪 |
|------|----------|
| 專案進度、狀態變化 | 對應專案的文件 / 你的 memory 系統 |
| 工作流程規則、行為應對模式 | 你的 feedback / 規則檔 |
| 敘事 / 關係紋理（見了誰、聊了什麼大事、長出的梗、氛圍）| `journal/YYYYMMDD.md`（highlights，不是逐字 transcript）|
| 技術 / 產出迭代的「過程」 | 對應專案 doc，journal 只寫一行 + ref |
| 純 debug、無敘事價值的閒聊 | 不存 |
| 真正的人格漂移 | `patches/YYYYMMDD_sessionN.md` |

判斷標準：「下次新 session 啟動時，這條訊息會 benefit 使用者體驗嗎？」

- 是且跟人格直接相關 → patch
- 是但跟人格無關 → memory / feedback
- 不是 → 不寫

每個 patch 控制在 **20 行內**。超過 20 行表示寫進了不該寫的東西，回頭分流。

> **patch 數量會直接變成下次的開機成本。** 載入時是「全部讀完、不准挑」，所以每多寫一份沒必要的
> patch，就是替未來每一次啟動加一份負擔。寧可不寫，也不要寫沒有漂移的流水帳。

## Journal 紀律（敘事日記，防流水帳 + 防 compact 痴呆）

`journal/` 存的是「我們做了什麼」的敘事時間線（patch = 人格漂移、memory = facts、journal = 敘事）。硬約束，context 快滿時也照守：

**寫入**（每次收尾存檔時 append 當天 `journal/YYYYMMDD.md`，一天一檔、多 session 接同檔）

1. **第一行 = 固定欄位摘要行**：`YYYY-MM-DD｜主線：<1 句>｜人：<誰>｜新梗：<梗，無則寫無>`。這行是 startup 唯一被 load 的東西，必寫、必精準。
2. **body ≤ 15 行；同一工項（即使來回多 session）≤ 2 行**：記敘事 + 梗 + 關係紋理 + 1–2 金句，不是逐字 banter。一個 task 反覆討論不該佔滿 journal——過程進對應 doc、journal 只留結論敘事。
3. **技術 / 產出迭代的過程 → 進對應專案 doc，journal 只寫一行 + ref**（否則 journal 變 task 流水帳，startup 會爆）。

**載入**：startup 只吃最近 5 天的「摘要行」（檔名 `YYYYMMDD.md` 遞減排序取前 5，排除 `EXAMPLE_*` 與 `_archive/`）；body 與更舊的 on-demand 撈。

**封存（root 非 `EXAMPLE_*` 檔數滿 30 觸發）**：把最舊一批原文**原封不動**搬進 `journal/_archive/YYYYQN/`，同時把那幾天的摘要行 collect 進 `journal/_archive/INDEX.md`（一天一行）。不摘要原文、不失真；深撈讀原文、快找掃 INDEX。**封存是會動檔案的操作 → 由 agent 提議、使用者批准後再執行，不自動搬。**

**收尾 lint（唯讀 + 報使用者，不自動刪改）**：每次收尾掃 `journal/` root（排除 `EXAMPLE_*` 與 `_archive/`）→ 抓「body 超 15 行 / 同一工項 >2 行 / 缺摘要行 / 滿 30 篇該封存」，報給使用者拍板。主防線是上面的寫入硬約束，lint 只抓漏網。
