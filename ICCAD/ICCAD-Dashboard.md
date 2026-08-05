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

> [!success] **現況（2026-08-06 更新）**
> **主力路線是 electro_v19**（slice_pack 切割式打包 + electro_optimized 的
> MIB anchor 兩段式機制 + Dirichlet 調和延拓初始化 + LP 位移候選的融合版，
> `collaborate/electro_v19/`），我們自己原本的 `electro_optimized/` 路線
> （1.7279）與生成式 B*-tree（3.3185）都已被超越。
>
> | 指標 | 數值 |
> |---|---|
> | **真實 Total Score**（含 runtime 因子 R） | **0.9801** |
> | 中性 Total Score（只算品質） | 1.3776 |
> | 快過官方逐案中位數 | **99/100 案** |
> | Feasible | 100/100 |
>
> 對照 Alpha Top5（0.879 / 0.955 / 1.020 / 1.028 / 1.100）→ 已逼近第 2 名。
>
> **8/3-8/6 這輪**：依序驗證 5 個更大方向（L-BFGS 打磨、Per-RMAP、ADMM 邊界
> 變數分裂、對偶上升 boundary、合法化長寬比彈性），3 個負面、1 個驗證正面
> 但未預設、1 個 Neutral 改善最多（-2.1%）但 REAL 分數打平。過程中也修好
> 一個 WSL-only 的浮點精度 bug。詳見
> [[ICCAD_code/8x_Research_Log/2026-07_Research_Log|2026-07 研究日誌]] §8.38-§8.39
> （R 因子被忽略的發現）與
> [[ICCAD_code/Electronic_Pipeline|Pipeline 說明]]「研究方向紀錄」，
> 架構現況見同一份 Pipeline 說明 +
> [[Electronic_Pipeline.canvas|畫布]]。

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
