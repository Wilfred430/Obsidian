---
title: Claude Code Skills 指令參考 (Skills Reference)
tags: [ICCAD, Tooling, Claude-Code, Meta]
date: 2026-07-28
---

# 10. Claude Code Skills 指令參考

> **跟 [[ICCAD_code/9_Research_Tool_Workflow|第 9 篇]] 的關係**：第 9 篇講「五個工具怎麼分工」，這篇只展開其中 **Claude Code** 這一格——它底下實際有哪些 skill 可以用、怎麼用、什麼時候用。目的是讓你不用每次都問我「有沒有工具可以幫忙」，自己就知道該喊哪個名字。

> [!info] **怎麼觸發**
> 大多數 skill 不是打指令，是**用自然語言講你要做的事**，Claude 會自動比對每個 skill 的觸發描述、自己決定要不要叫用（例如你說「幫我 review 這個 PR」就會自動叫 `code-review`）。少數幾個有明確的斜線指令（`/loop`、`/code-review`、`/schedule` 等），下面表格有標注的才是可以直接打 `/名稱` 的。

---

## A. 這個專題專屬

| Skill | 作用 | 什麼時候用 |
|---|---|---|
| **floorplan-guard** | 把 FloorSet-Lite 的硬/軟約束規則（overlap、area 1%、fixed-shape、preplaced、grouping/MIB/boundary）寫成檢查清單 | 每次要**寫、改、審查** `electro_optimized/`、`legalize.py`、`soft_repair.py`、`my_optimizer.py` 這類佈局/合法化程式碼之前，先過一輪，避免不小心生出 `Cost=10` 的硬約束違規 |

---

## B. 論文寫作與研究產出（對應你說的「看看 paper 不斷推進進度」）

這三個是一整組（`academic-research-skills` marketplace），彼此有明確分工，不要混著用：

| Skill | 作用 | 什麼時候用 |
|---|---|---|
| **academic-pipeline** | 總協調者，管理「研究 → 寫作 → 誠信查核 → 審查 → 修訂 → 複審 → 定稿」十階段流程，每階段結束會停下來等你確認 | 想要**從頭到尾**做一輪完整流程時用這個當入口，不要直接跳去下面兩個 |
| **academic-paper** | 12-agent 寫作團隊：配置訪談 → 文獻搜尋 → 架構設計 → 論證建構 → 全文草稿 → 引用合規 → 雙語摘要 → 格式輸出（LaTeX/DOCX/PDF） | 已經想清楚要寫什麼、需要**產出草稿**、大綱、摘要、修改既有稿件、或轉換引用格式時 |
| **academic-paper-reviewer** | 模擬 5 個審查者人格（主編 + 3 位同儕 + 魔鬼代言人）分別從方法論/領域專業/跨學科/核心論證四個角度審查 | 稿子寫完想**先被電一輪**再投稿時用；也可以拿來練習「审查者會怎麼挑你的論證漏洞」，跟我現在考你的精神一樣 |

**建議用法**：把 `d:\ICCAD-2026-C\collaborate\docs\electro_placer_deep_dive.pdf` 的內容（九個里程碑、誠實的負面結果、方法論心得）當素材，等你自己想通了之後，用 `academic-paper` 整理成正式論文骨架，再用 `academic-paper-reviewer` 校一輪。

---

## C. 知識管理（Obsidian / NotebookLM / Zotero）

| Skill | 作用 | 什麼時候用 |
|---|---|---|
| **notebooklm-connector:notebooklm-manager** | 透過 Chrome 操作 NotebookLM notebook（查詢、新增來源、列出、搜尋、啟用/停用） | 把已查證過的論文 + `CLAUDE.md`/`WINNING_STRATEGY.md` 餵進 NotebookLM 之後，要問問題或管理來源時 |
| （無專屬 skill）**Obsidian** | 你現在讀的這整個 vault | 我直接用 Read/Write/Edit 工具操作 `.md` 檔案，沒有獨立 skill，是我依你先前訂的 schema 規則手動維護 |
| （無專屬 skill）**Zotero** | 文獻管理/引用資料庫 | 目前沒有連接工具，Zotero 那端你自己維護；`academic-paper` 產出的引用格式可以手動匯入 Zotero 對照，或反過來把 Zotero 的 BibTeX 匯出貼給我核對 |

---

## D. 工程紀律（改 `electro_optimized/` 或任何程式碼時）

這一組是 `superpowers` 套件，核心精神是「先想清楚流程，再動手」：

| Skill | 作用 | 什麼時候用 |
|---|---|---|
| **superpowers:brainstorming** | 開發前先探索需求/設計，而非直接寫 | 要加新功能、新機制之前 |
| **superpowers:systematic-debugging** | 系統性除錯流程，而非亂槍打鳥 | 遇到 bug、測試失敗、行為不符預期時，**先用這個，再提修法** |
| **superpowers:test-driven-development** | 先寫測試再寫實作 | 實作任何新功能/修 bug 前 |
| **superpowers:writing-plans** / **executing-plans** | 把需求寫成計畫書，再照計畫分階段執行（跨 session 也能接續） | 任務較大、需要多步驟時 |
| **superpowers:verification-before-completion** | 宣稱「完成/修好/測試通過」之前，強制先跑驗證指令、看過結果 | 任何要說「做完了」的時刻——這正是本專題整季反覆強調的「隔離測試不能直接信」紀律的 Claude Code 版本 |
| **superpowers:requesting-code-review** / **receiving-code-review** | 完成後主動請求審查／收到審查意見後如何技術性驗證而非照單全收 | 完成一個功能或修復後 |
| **superpowers:using-git-worktrees** | 開新功能時建立隔離的 git worktree，不干擾目前工作區 | 想在不動到目前 `main`/`feature` 分支的情況下實驗 |
| **superpowers:dispatching-parallel-agents** / **subagent-driven-development** | 面對多個互相獨立的任務時，平行派工給多個 agent | 例如同時整理好幾個獨立資料夾、或同時驗證好幾組參數 |
| **superpowers:finishing-a-development-branch** | 開發完成後，引導決定 merge / PR / 清理的下一步 | 一段開發工作收尾時 |

---

## E. 程式碼/PR 審查

| Skill | 作用 | 什麼時候用 |
|---|---|---|
| **code-review:code-review**（`/code-review`，加 `ultra` 可跑多 agent 雲端深度審查） | 審查一個 PR 或目前分支的變更 | 準備送出/合併程式碼前 |
| **security-review** | 專門抓安全漏洞 | 有對外介面、處理外部輸入的程式碼 |
| **simplify** | 審查程式碼的重用性/精簡度/效率，不抓 bug，只做品質清理 | 功能正確後，想順手清理程式碼時（不要跟抓 bug 的 review 混用） |

---

## F. 視覺化與成果呈現

| Skill | 作用 | 什麼時候用 |
|---|---|---|
| **dataviz** | 畫任何圖表前必讀——色彩/座標軸/標籤的一套系統化規則，而非憑感覺挑顏色 | 要做圖表、dashboard、儀表板時（`electro_placer_deep_dive.pdf` 裡的五張圖就是照這套規則做的） |
| **artifact-design** / **artifact-capabilities** | 發布互動網頁（Artifact）時的設計指南；後者是「即時資料/共享狀態」這類進階能力 | 想要一個可以分享的網頁版報告，而不是純 PDF 時 |

---

## G. Claude Code 本身的維護/設定

| Skill | 作用 | 什麼時候用 |
|---|---|---|
| **skill-creator:skill-creator** | 建立/修改/優化 skill（你的 `floorplan-guard` 就是這樣做出來的） | 想把某個重複出現的檢查清單或工作流程包成新 skill 時 |
| **claude-md-management:revise-claude-md** / **claude-md-improver** | 更新/稽核 `CLAUDE.md` | 這季發現新的 gotcha（像 TOUCH_EPS、_PUSH_GAP 那些）之後，正式寫回 `CLAUDE.md` 時 |
| **update-config** | 設定 `settings.json`（權限、hooks、環境變數） | 想調整「哪些指令不用每次都問過你」之類的權限設定 |
| **loop**（`/loop`） | 讓某個指令/流程照間隔重複執行 | 你之前用過的「自主優化模式」背後就是這個機制 |
| **schedule** | 建立/管理排程的雲端 agent（cron） | 想要「每天固定時間自動跑一次」這種排程任務 |
| **run** | 啟動並實際跑一次你的 app，確認改動真的生效 | 想親眼看到程式運作，而不只是通過測試 |
| **fewer-permission-prompts** | 掃描常見的唯讀指令，加進允許清單，減少每次都要按確認 | 覺得太常被詢問權限、想精簡流程時 |

---

## H. Hugging Face（你已經用過 HF Hub 抓 FloorSet 1M 訓練集）

| Skill | 作用 | 什麼時候用 |
|---|---|---|
| **huggingface-skills:hf-cli** | Hugging Face Hub 的 CLI 操作（下載/上傳/管理 repo、資料集、模型） | 未來需要重新抓資料集、或找其他 HF 上的資源時 |

> 其餘 `huggingface-skills:*`（SageMaker 部署、ZeroGPU、Gradio Space、模型訓練等）跟這個純本機 PyTorch 專題無關，不列入——vault 保持精簡，只留真的會用到的。

---

## 依你的實際工作流程對照

```
遇到 bug / 要改演算法
  → superpowers:systematic-debugging → floorplan-guard（過約束檢查）
  → superpowers:verification-before-completion（驗證再宣稱完成）

想推進成研究成果
  → academic-pipeline（或直接 academic-paper）→ academic-paper-reviewer 校一輪
  → 引用資料進 Zotero（手動）、深度問答用 notebooklm-manager

想要圖表/報告
  → dataviz（畫圖前必讀）→ artifact-design（若要做成網頁）

想把某個重複流程存起來
  → skill-creator

定期更新專案記憶
  → claude-md-management:revise-claude-md
```

---
**相關筆記**：[[ICCAD_code/9_Research_Tool_Workflow|9. 研究工具分工流程]] · [[ICCAD_code/8_Winning_Strategy_and_Roadmap|8. 奪冠策略總覽]]
**回到**：[[ICCAD/ICCAD-Dashboard|ICCAD 儀表板]]
