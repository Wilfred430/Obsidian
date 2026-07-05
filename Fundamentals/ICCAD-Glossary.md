---
title: ICCAD 專案速查詞彙表 (Glossary)
tags: [Fundamentals, EDA, VLSI, Floorplanning, Glossary]
date: 2026-07-01
aliases: [Glossary, 詞彙表, 名詞解釋]
---

# ICCAD 專案速查詞彙表 (Glossary)

> [!abstract] **用法**：讀 [[ICCAD_code/1_Data_Loader_and_Wrapper|技術筆記]]時遇到看不懂的詞，回來這裡查。每個詞只給一句白話解釋 + 連到深入筆記。沒有先讀過 [[Fundamentals/VLSI-Floorplanning-101|VLSI Floorplanning 入門]] 的話，建議先讀那篇建立大局觀。

## 基本物件

| 術語 | 白話解釋 | 深入筆記 |
|---|---|---|
| **Block（模組）** | 城市裡的一棟建築物，要決定它的座標跟大小 | [[Fundamentals/VLSI-Floorplanning-101\|Floorplanning 101]] |
| **Netlist（網表）** | 建築物之間的道路/管線清單，記錄誰跟誰要連通 | 同上 |
| **Terminal / Pin（腳位）** | 城市邊界上的固定對外聯絡點 | 同上 |
| **b2b / p2b** | block-to-block / pin-to-block，兩種網表連線（模組對模組、腳位對模組） | [[ICCAD_code/1_Data_Loader_and_Wrapper\|1. Data Loader]] |
| **Soft block（軟模組）** | 只限定面積，長寬比可以自己選的模組 | [[Fundamentals/VLSI-Floorplanning-101\|Floorplanning 101]] |

## 約束（規矩）

| 術語 | 白話解釋 | 深入筆記 |
|---|---|---|
| **Fixed-shape** | 尺寸鎖死不可變（硬約束） | [[Fundamentals/VLSI-Floorplanning-101\|Floorplanning 101]] §3 |
| **Preplaced** | 位置+尺寸都鎖死（硬約束） | 同上 |
| **Boundary** | 必須貼指定的邊/角（軟約束） | 同上 |
| **Grouping** | 同群組模組必須物理相鄰成一塊（軟約束） | 同上 |
| **MIB** | Multi-Instantiated Block，同群組模組尺寸必須一致（軟約束） | 同上 |
| **Feasible / Infeasible** | 合法解／不合法解。違反任何硬約束 = infeasible = 直接判 Cost=10 | [[ICCAD_code/3_Cost_Function_and_Penalty\|3. Cost Function]] |

## 表示法與演算法

| 術語 | 白話解釋 | 深入筆記 |
|---|---|---|
| **B\*-tree** | 用「誰貼在誰右邊/上面」描述擺法的二元樹「食譜」，取代直接猜座標 | [[ICCAD/Algorithms/B-Star-Tree\|B*-tree 技術筆記]] |
| **Contour（輪廓線/天際線）** | 打包時記錄「目前疊到多高」的資料結構，決定新模組掉落的 y 座標 | [[ICCAD_code/4_Packing_and_Evaluation\|4. Packing]] |
| **Packing（打包）** | 把 B*-tree 食譜轉換成實際 (x,y,w,h) 座標的過程 | 同上 |
| **Legalize（合法化）** | 把一個接近合法但有小瑕疵的解，修正成完全合法 | [[ICCAD_code/7_Electrostatic_Placer\|7. 電靜力法]] |
| **SA（Simulated Annealing，模擬退火）** | 允許「暫時變差」來跳出局部最佳解的搜尋演算法，本專案主力方法 | [[ICCAD_code/2_SA_Optimizer_Engine\|2. SA 引擎]] |
| **Move（微擾動作）** | SA 每一步嘗試的小修改（旋轉、搬移、交換……） | 同上 |
| **always_accept** | 少數幾種「只做不看溫度、無條件接受」的 Move，專門修約束違規 | 同上 |
| **Warm-start（熱啟動）** | 用一個「還不錯的猜測」當搜尋起點，而不是從隨機狀態開始 | [[ICCAD_code/1_Data_Loader_and_Wrapper\|1. Data Loader]] |

## 評分公式

| 術語 | 白話解釋 | 深入筆記 |
|---|---|---|
| **HPWL** | Half-Perimeter Wirelength，衡量連線總長度的指標（中心點到中心點的曼哈頓距離） | [[ICCAD_code/3_Cost_Function_and_Penalty\|3. Cost Function]] |
| **Baseline** | 資料集自帶的近似最優解，Cost 公式拿你的解跟它比差距 | 同上 |
| **HPWL_gap / Area_gap** | 你的解跟 baseline 的相對差距（有正負號，負的代表你比較好） | 同上 |
| **V_rel** | 軟約束違規的相對比例（0～1），數值越高懲罰指數放大 | 同上 |
| **RuntimeFactor (RT)** | 你的耗時 ÷ 該 test case 所有參賽隊伍耗時的中位數（逐 case 獨立算，跨隊伍） | 同上 |
| **Cost** | 綜合面積/線長/違規/速度的總分公式，越低越好，1.0 = 打平 baseline | 同上 |
| **Total Score** | 100 個 test case 的 Cost 依 `e^(n/12)` 加權平均（n=模組數），大案例權重極高 | [[ICCAD_code/8_Winning_Strategy_and_Roadmap\|8. 奪冠策略總覽]] |

## ML 相關

| 術語 | 白話解釋 | 深入筆記 |
|---|---|---|
| **Mode Collapse** | 多個合法解被回歸模型「平均」成一個不合法解的失敗模式 | [[ICCAD_code/5_ML_Coordinate_Regression\|5. ML 座標回歸]] |
| **tree_sol** | 資料集裡附的「近似最優 B*-tree 食譜」標籤 | [[ICCAD_code/6_ML_Generative_BTree\|6. 生成式模型]] |
| **Pointer Network** | 輸出「指向某個已知選項」而非「分類/生成新詞彙」的神經網路架構 | 同上 |
| **Teacher Forcing** | 訓練生成式模型時，直接餵標準答案的生成順序當輸入 | 同上 |
| **Transformer / Attention** | 讓模型一次看完整個序列、互相「對話」的神經網路架構，本庫兩個模型的骨幹 | [[AI/Transformer\|Transformer 架構全解]] |

---
**相關筆記**：[[Fundamentals/VLSI-Floorplanning-101|VLSI Floorplanning 入門]] · [[Fundamentals/FloorSet-Data-Worked-Example|資料實例解析（真實數字版）]] · [[ICCAD/ICCAD-Dashboard|回到 Dashboard]]
