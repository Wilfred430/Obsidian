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

> [!success] **現況（2026-07-01）**
> Alpha test 已過，進入 Beta→Final 衝刺。三條路線並存：[[ICCAD_code/2_SA_Optimizer_Engine|B*-tree+SA]]（主力，穩定）、[[ICCAD_code/6_ML_Generative_BTree|生成式拓樸模型]]（新，GPU 訓練中，`val_ptr_acc` 87%）、[[ICCAD_code/7_Electrostatic_Placer|電靜力法]]（**目前分數最佳** Total 2.966）。完整策略見 [[ICCAD_code/8_Winning_Strategy_and_Roadmap|奪冠策略總覽]]。

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
- [[ICCAD_code/7_Electrostatic_Placer|7. 電靜力法擺放器]]
- [[ICCAD_code/8_Winning_Strategy_and_Roadmap|8. 奪冠策略總覽與現況路線圖]]
- [[ICCAD_code/9_Research_Tool_Workflow|9. 研究工具分工流程（Claude Code/Antigravity/Gemini/NotebookLM/Connected Papers）]]

## 🧬 EDA 領域背景
- [[ICCAD/EDA-Paradigm-Shift|EDA 範式轉移]]：從傳統規則到 AI 驅動的轉變。
- [[ICCAD/Floorplanning/Outline-Characteristics|VLSI Outline 基礎]]：各類 Layout 特性分析。

---
**回到索引**：[[index|🌐 全域索引 >>]]
