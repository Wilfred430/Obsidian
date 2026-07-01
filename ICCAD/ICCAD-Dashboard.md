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
> 對應 `collaborate/` repo 的實際程式碼架構,原子筆記系列 1-8。
- [[ICCAD_code/1_Data_Loader_and_Wrapper|1. Data Loader 與 Python 封裝架構]]
- [[ICCAD_code/2_SA_Optimizer_Engine|2. 核心退火引擎與 B*-Tree]]
- [[ICCAD_code/3_Cost_Function_and_Penalty|3. Cost Function 與動態懲罰機制]]
- [[ICCAD_code/4_Packing_and_Evaluation|4. 拓撲打包與座標推算]]
- [[ICCAD_code/5_ML_Coordinate_Regression|5. ML 座標回歸與 Mode Collapse 診斷]]
- [[ICCAD_code/6_ML_Generative_BTree|6. 生成式 B*-tree 拓樸模型]]
- [[ICCAD_code/7_Electrostatic_Placer|7. 電靜力法擺放器]]
- [[ICCAD_code/8_Winning_Strategy_and_Roadmap|8. 奪冠策略總覽與現況路線圖]]

## 🧬 EDA 領域背景
- [[ICCAD/EDA-Paradigm-Shift|EDA 範式轉移]]：從傳統規則到 AI 驅動的轉變。
- [[ICCAD/Floorplanning/Outline-Characteristics|VLSI Outline 基礎]]：各類 Layout 特性分析。

---
**回到索引**：[[index|🌐 全域索引 >>]]
