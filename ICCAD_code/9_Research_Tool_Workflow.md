---
title: 研究工具分工流程 (Research Tool Workflow)
tags: [ICCAD, EDA, Research-Methodology, Tooling]
date: 2026-07-23
---

# 9. 研究工具分工流程 (Research Tool Workflow)

> **核心角色**：跟 [[ICCAD_code/1_Data_Loader_and_Wrapper|1]]–[[ICCAD_code/8_Winning_Strategy_and_Roadmap|8]] 不同，這篇不是講演算法本身，而是講**怎麼組合五個工具（Claude Code、Antigravity、Gemini Deep Research、NotebookLM、Connected Papers）做這個專題的研究與實作**，讓「查證文獻真偽」這個本 session 反覆踩到的坑有一套固定流程可以依循。

## 五個工具各自的角色

| 工具 | 角色 | 什麼時候用 |
|---|---|---|
| **Connected Papers** | 文獻探索的第一步，且天生不會幻覺 | 拿已查證為真的論文（ePlace、DREAMPlace、RePlAce、TCG、UFO、QinFer、"Placement Constraints in Floorplan Design"）當種子節點，看引用圖譜找鄰近文獻。因為它是真實引用關係的視覺化，不是生成式搜尋，**找到的論文保證是真的**，不需要再花力氣查證存在與否。 |
| **Gemini Deep Research** | 開放式、沒有好種子論文時的廣泛搜尋 | 適合問概念性、跨主題的問題，但**輸出永遠要查證**——這個工具會編造看似合理的具體公式（見下方「已知的失敗模式」）。 |
| **NotebookLM** | 在已驗證過的資料源上做有根據的問答 | 把 Connected Papers/Gemini 找到、且已查證為真的論文，加上專案自己的 `CLAUDE.md`/`WINNING_STRATEGY.md`，一起餵進一個 notebook，再針對這個可信任的語料庫問具體技術問題。因為它主要根據餵進去的資料回答，如果資料源都是已驗證的，答案可信度就高很多。 |
| **Antigravity** | 大規模實作 + 100 案驗證的執行者 | 想法已經想清楚、設計好之後，交給 Antigravity 實際寫程式碼、跑滿 100 案驗證，不是用來發想點子的工具。 |
| **Claude Code** | 陪讀、引導、審查，不是自動代勞 | 2026-07-23 起改為蘇格拉底式導師模式——陪你讀懂 codebase、一起推理、審查你寫的程式碼、用小測驗確認理解，不會再自己跑實驗或直接改 production 檔案，除非明講要它代勞。這個角色設定記在 Claude Code 自己的跨對話記憶裡（非本 vault 內容），不在此重複維護。 |

## 建議的實際流程

Connected Papers（找到保證真實的鄰近文獻）→ 需要更廣的搜尋範圍時，補上 Gemini Deep Research → 把確認過的論文丟進 NotebookLM 做深度問答 → 使用者自己（在 Claude Code 引導下）把想法想清楚並動手實作 → Antigravity 跑大規模驗證，確認實際 100-case Total Score。

> [!danger] **已知的失敗模式（不要重複踩）**：2026-07-21 這次 Deep Research 報告的 7 項引用查證結果——TCG、UFO、"Placement Constraints in Floorplan Design"、QinFer 全部確認真實存在；但**DREAMPlace 3.0 的密度權重更新公式是編造的**（報告寫的公式在真實論文中不存在），**AutoDMP 被誤植為使用 RL/MDP**（實際上是貝葉斯優化/Optuna，跟 Google 的 RL macro placer 搞混了）。教訓：**論文標題/作者存在，不代表報告引用的具體公式或技術細節就是真的**——每一條數學式、每一個「該論文用了 XX 方法」的具體宣稱，都要獨立查證，不能因為論文本身查證為真就對其餘內容照單全收。完整查證紀錄在 `d:\ICCAD-2026-C\AI-deep-search\research_notes.md`。

> [!info] **這套流程是怎麼演變出來的**：本 session 一開始只用 Gemini Deep Search → Antigravity 查證兩步，後來因為連續發現「論文標題是真的但公式是編的」這種局部幻覺，才加入 Connected Papers（從源頭避免幻覺）跟 NotebookLM（限制在已驗證語料庫內回答）這兩層。完整的方法論教訓另見 [[ICCAD_code/8_Winning_Strategy_and_Roadmap|第 8 篇]] §8.34。

---
**相關筆記**：[[ICCAD_code/8_Winning_Strategy_and_Roadmap|奪冠策略總覽]] · [[user-wants-socratic-python-mentor]]
**回到**：[[ICCAD/ICCAD-Dashboard|ICCAD 儀表板]]
