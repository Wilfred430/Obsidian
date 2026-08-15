---
title: ICCAD 2026 競賽儀表板 (Dashboard)
tags:
  - Meta/Dashboard
  - ICCAD
  - EDA
date: 2026-07-01
---

# 🏆 ICCAD 2026 競賽儀表板 (Dashboard)

> [!info] **說明**
> 彙整 ICCAD 2026 各項問題規格、演算法研究與 EDA 理論背景，作為參賽的戰略中心。

> [!success] **現況（2026-08-11 更新）**
> **主力路線升級為 electro_v20**（`electro_v19` 的安全超集——關掉新機制時
> 逐位元輸出相同，`collaborate/electro_v20/`）。這一輪是「RT/預設值調校
> campaign」：不發明新演算法，純粹靠重新量測既有設定 + 找回沒進生產的機制
> （邊界對偶上升早就驗證正面卻只存在 v20，從沒被設成預設），外加把
> 8/4 那輪在 v19 上測出「session 當時最大單一機制改善」但因 REAL 打平而
> 停用的 `legalize_qinfer_reshape()` 移植到 v20、跟對偶上升疊加重測。
>
> **2026-08-15 再更新（MIB campaign，主力為 `electro_v22`）**：
>
> | 指標 | 數值 |
> |---|---|
> | 中性 Total Score（只算品質） | **1.2252**（七次獨立全 100 案量測都精確落在此值） |
> | 真實 Total Score（對官方 Alpha 逐案中位數） | 0.878 – 0.895 |
> | Feasible | **100/100** |
> | 平均 runtime | 1.91 – 2.12s（散布 10%，**不是演算法屬性**，見 Pipeline 說明） |
>
> 對照 Alpha Top5（0.8789 / 0.9551 / 1.0197 / 1.0282 / 1.0997）：
> 我們的代理分數落在**第 1 名同一個量級**（0.8779–0.8951 vs 0.8789），
> 總 runtime 約 200s 也比第 1 名的 266.8s 更快。
>
> > [!warning] **這不等於「我們是第一名」**
> > Alpha 榜是官方在自己的測資上、用**真實跨隊中位數**算出來的；我們的數字是
> > 在**公開 100 案驗證集**上、拿 Alpha 逐案中位數當代理算出來的。兩者不是
> > 同一個量測。能說的是「同一個量級」，不能說名次。
>
> **這輪新增生產預設**：`PLACE_COMPACT_ITERS` 400→150（調過頭，不是取捨）、
> `REPAIR_ROUNDS` 3→2（第 3 輪逐位元無作用）、`DUAL_ASCENT_BND=1 DA_K=40`
> （v19 一直沒有這段程式碼，設了也無聲失效）、`RESHAPE_PORTFOLIO=1`（新
> 移植，Vgrp/Vbnd 都改善、REAL 不降反微升，代價是 Vmib +16%）。另外用同一
> session 的參數掃描否決了 9 個方向（`TARGET_UTIL` 是乾淨的 Q/P 對撞盤、
> `DA_BND_CEIL` 確認是死參數等），詳見
> [[ICCAD_code/Electronic_Pipeline|Pipeline 說明]]「研究方向紀錄」。
> 架構現況見同一份 Pipeline 說明 + [[Electronic_Pipeline.canvas|畫布]]。

> [!abstract] **🔰 新手從這裡開始（零基礎閱讀動線）**
> 這個 vault 的其他筆記大多假設你已經懂晶片設計基礎——如果不是，照下面順序讀，不要跳著看：
> 1. **大局觀**：`collaborate/新手入門_專案總覽.pdf`（生活比喻版簡報，一次掌握全貌，適合印出來看）
> 2. **建立基礎詞彙**：[[Fundamentals/VLSI-Floorplanning-101|VLSI Floorplanning 入門]] —— 什麼是晶片、什麼是模組/網表、為什麼難、B*-tree 的直覺
> 2.5. **想看真實數字**：[[Fundamentals/FloorSet-Data-Worked-Example|資料實例解析]] —— 打開一個真實驗證集案例，把 area/constraint/b2b/p2b 換成具體數字對照著看
> 3. **卡住就查**：[[Fundamentals/ICCAD-Glossary|速查詞彙表]] —— 讀後面的技術筆記遇到看不懂的詞，回來這裡查一句話解釋
> 4. **比賽規則**：[[ICCAD/Problem/FloorSet-Summary|FloorSet 快速複習]] →（想看細節再讀）[[ICCAD/Problem/FloorSet-Detailed|FloorSet 規格詳解]]
> 5. **實作深潛，照 1→8 編號順序讀**（見下方「實作深潛」區塊）——這個順序本身就是設計過的：資料怎麼進來 → SA 怎麼搜 → 怎麼打分 → 怎麼拼出座標 → 舊 ML 為什麼失敗 → 新 ML 怎麼做 → 電靜力法 → 最後總覽策略
> 6. **想深入某個演算法/理論**：從技術筆記裡的連結點出去（[[ICCAD/Algorithms/B-Star-Tree|B*-tree]]、[[AI/Transformer|Transformer]]、[[Fundamentals/Optimization-Theory|最佳化理論]] 等）

## 📋 競賽問題規格 (Problems)
- [[ICCAD/Problem/FloorSet-Detailed|🏆 Problem C：FloorSet 規格詳解]] (重點關注 V9 更新)
- [[ICCAD/Problem/FloorSet-Summary|⚡ FloorSet 快速複習 (口訣版)]]
- [[ICCAD/Problem-A-Bug-Classification|Problem A：RTL Bug Classification]]
- [[ICCAD/Problem-D-Timing-Fixing|Problem D：Timing Fixing]]

## 🧠 佈局演算法研究 (Algorithms)
- [[ICCAD/Algorithms/B-Star-Tree|B*-tree Floorplanning 技術筆記]]：將拓樸與幾何分離的精巧佈局資料結構。
- [[ICCAD/Algorithms/Wong-Liu-Algorithm|Wong-Liu Algorithm (1986)]]：經典退火演算法與 NPE 表示法。
- [[ICCAD/Algorithms/Fixed-Outline-Floorplanning|Fixed-Outline Floorplanning (2003)]]：現代分層設計與固定輪廓約束。

## 🔧 實作深潛 (Implementation Deep-Dive)
> 對應 `collaborate/` repo 的實際程式碼架構,原子筆記系列 1-9。
- [[ICCAD_code/1_Data_Loader_and_Wrapper|1. Data Loader 與 Python 封裝架構]]
- [[ICCAD_code/1b_Input_Output_Contract|1b. Input/Output 完整合約（資料存在哪、格式是什麼）]]
- [[ICCAD_code/2_SA_Optimizer_Engine|2. 核心退火引擎與 B*-Tree]]
- [[ICCAD_code/3_Cost_Function_and_Penalty|3. Cost Function 與動態懲罰機制]]
- [[ICCAD_code/4_Packing_and_Evaluation|4. 拓撲打包與座標推算]]
- [[ICCAD_code/5_ML_Coordinate_Regression|5. ML 座標回歸與 Mode Collapse 診斷]]
- [[ICCAD_code/6_ML_Generative_BTree|6. 生成式 B*-tree 拓樸模型]]
- [[ICCAD_code/7_Electrostatic_Placer|7. 電靜力法擺放器]]（含 [[Electronic_Pipeline.canvas|架構畫布]] + [[Electronic_Pipeline|畫布方塊說明]]，pipeline 有結構性改動時同步更新）
- [[ICCAD_code/8_Winning_Strategy_and_Roadmap|8. 奪冠策略總覽與現況路線圖]]
- [[ICCAD_code/9_Research_Tool_Workflow|9. 研究工具分工流程（Claude Code/Antigravity/Gemini/NotebookLM/Connected Papers）]]
- [[ICCAD_code/10_Claude_Code_Skills_Reference|10. Claude Code Skills 指令參考（有哪些 skill、怎麼用、什麼時候用）]]

## 🧬 EDA 領域背景
- [[ICCAD/EDA-Paradigm-Shift|EDA 範式轉移]]：從傳統規則到 AI 驅動的轉變。
- [[ICCAD/Floorplanning/Outline-Characteristics|VLSI Outline 基礎]]：各類 Layout 特性分析。

---
**回到索引**：[[index|🌐 全域索引 >>]]
