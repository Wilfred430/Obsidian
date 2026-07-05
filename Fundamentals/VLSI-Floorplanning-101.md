---
title: VLSI Floorplanning 入門：從「什麼是晶片」開始
tags: [Fundamentals, EDA, VLSI, Floorplanning, Beginner]
date: 2026-07-01
aliases: [Floorplanning 101, 佈局規劃入門]
---

# VLSI Floorplanning 入門：從「什麼是晶片」開始

> [!abstract] **這篇筆記回答什麼**
> 如果你完全不懂晶片設計，讀完這篇就能看懂 [[ICCAD/ICCAD-Dashboard|ICCAD Dashboard]] 底下所有筆記在講什麼。這是整個知識庫**真正的起點**，其他筆記（包含 [[ICCAD/Problem/FloorSet-Summary|FloorSet 規格]]、[[ICCAD_code/1_Data_Loader_and_Wrapper|實作系列]]）都預設你已經懂這裡的內容。

## 1. 一顆晶片長什麼樣子

把一顆晶片想成一座**城市**：

- **Block（模組）** = 城市裡的**建築物**（工廠、住宅、商場……），每一棟都有自己的長寬尺寸。
- **Netlist（網表）** = 建築物之間的**道路/管線**——記錄哪兩棟建築要互相連通、連通的「流量」有多大（權重）。
- **Terminal / Pin（腳位）** = 城市**邊界上的對外聯絡點**（機場、港口），建築物要跟外界溝通，訊號會經過這些固定的點。
- **畫布（Canvas / Outline）** = 這座城市的**土地邊界**——面積通常是固定的，不能無限擴張。

**Floorplanning（佈局規劃）** 就是決定「每棟建築要蓋在城市的哪個座標、多大」——這件事在晶片設計流程裡發生在最早期，決定了後面所有細節設計（繞線、時序）的天花板。

## 2. 為什麼這是一個「難」的問題

> [!info] **生活比喻**
> 想像你要把 100 件不同大小的家具塞進一間公寓，要求：(a) 家具彼此不能重疊、(b) 常常一起用的家具（沙發和茶几）要放得近一點（因為要牽電線/常常搬動東西過去）、(c) 有些家具靠牆才有意義（書架）、(d) 整個公寓的外框大小最好越小越好。

這就是 floorplanning 的核心：

- **目標 1：面積最小**——城市土地是有限資源，建築物排得越緊湊，晶片越省成本。
- **目標 2：連線越短越好**——`HPWL`（Half-Perimeter Wirelength，半周長線長）衡量「建築物之間的道路總長」，線越短，訊號傳得越快、耗電越少。這是本庫最常出現的一個詞，之後看到 `HPWL` 就想成「總管線長度」。

這兩個目標常常互相拉扯（緊湊排列不一定線最短），而且**候選擺法的數量隨模組數暴增**（見 [[Fundamentals/Optimization-Theory|最佳化理論筆記]]的組合爆炸一節）——這就是為什麼 floorplanning 是一個需要演算法（不能靠人工排）的困難問題。

## 3. 「限制」從哪裡來（現實世界的規矩）

真實晶片設計不是隨便排，有些建築物有硬性規矩：

| 規矩 | 生活比喻 | 本庫術語 |
|---|---|---|
| 有些建築物尺寸不能改 | 冰箱就是那個大小，搬去哪都一樣大 | **Fixed-shape**（固定形狀） |
| 有些建築物連位置都不能動 | 插座釘在牆上的那個位置，不能搬 | **Preplaced**（預先擺放） |
| 有些建築物必須靠邊/靠角 | 書架一定要靠牆才站得住 | **Boundary**（邊界約束） |
| 有些建築物要住在一起 | 一家人要住同一層樓 | **Grouping**（分組聚集） |
| 有些建築物尺寸要完全一樣 | 雙胞胎的房間裝潢必須一模一樣 | **MIB**（Multi-Instantiated Block，同型模組） |
| 其餘建築物只要求「多大」，長寬比例可以自己選 | 你自己家的沙發，長寬比隨你挑，只要坐得下那麼多人 | **Soft block**（軟模組），僅限定面積 |

**硬規矩（Fixed-shape / Preplaced / 不重疊 / 不超出畫布 / 面積誤差 ≤1%）違反了直接判定「不合法」，等於零分**（本庫術語：**infeasible**，相對地，合法解叫 **feasible**）。**軟規矩（Boundary / Grouping / MIB）違反不會判死刑，但會被扣分，而且扣分是指數成長的**（詳見 [[ICCAD_code/3_Cost_Function_and_Penalty|Cost Function 筆記]]）。

## 4. 怎麼「表示」一個擺法：B\*-tree 的直覺

決定每棟建築的座標 $(x, y)$ 有無限多種可能（連續數字），電腦很難直接在這種空間裡搜尋。聰明的做法是換一種「食譜」式的表示法：

> [!info] **生活比喻：疊箱子**
> 想像你不是直接講「這個箱子在座標 (10, 20)」，而是講「這個箱子疊在**那個**箱子的右邊」或「疊在**那個**箱子的正上方」。只要知道每個箱子跟誰相鄰、往哪個方向疊，把箱子的實際大小代進去，就能**唯一算出**每個箱子的絕對座標——不需要直接猜座標數字。

這種「食譜」在學術上叫 **B\*-tree**：一棵二元樹，每個節點代表一個 block，左子節點=「貼在右邊」、右子節點=「疊在上面」。改食譜（換一種樹的長相）比直接亂猜座標更容易系統化地搜尋——這是本庫 Approach A（[[ICCAD_code/2_SA_Optimizer_Engine|SA 引擎]]）與 Approach B/新（[[ICCAD_code/6_ML_Generative_BTree|生成式模型]]）共同的核心資料結構。完整細節見 [[ICCAD/Algorithms/B-Star-Tree|B*-tree 技術筆記]]。

## 5. 怎麼知道一個擺法「好不好」：評分的直覺

有了一個具體擺法後，怎麼打分數？三個方向的加權組合：

1. **面積打得多小**（跟 baseline，也就是「近乎最優的參考答案」比）。
2. **管線（HPWL）打得多短**。
3. **有沒有違反規矩**（硬規矩：直接死當；軟規矩：指數扣分）。
4. **跑得多快**（有一點點速度加分，但封頂；跑太慢無上限扣分）。

這四點被組合成一個公式叫 **Cost**（越低越好，1 分表示「打平參考答案」），完整公式與每個符號的意義見 [[ICCAD_code/3_Cost_Function_and_Penalty|Cost Function 筆記]]。

## 6. 這個知識庫在做什麼（承接下一步）

本專案（ICCAD 2026 Contest Problem C）要對 21～120 個模組的晶片案例，自動找出「Cost 越低越好」的擺法，而且**大案例的分數權重極高**（見 [[ICCAD_code/8_Winning_Strategy_and_Roadmap|奪冠策略總覽]]），所以真正的戰場在處理上百個模組的複雜案例。有三條並行的解法路線，摘要見 [[ICCAD/ICCAD-Dashboard|Dashboard 的「現況」區塊]]。

**讀完這篇，你已經有足夠的背景知識進入下一步**——想直接看這些概念對應到的真實數字，讀 [[Fundamentals/FloorSet-Data-Worked-Example|資料實例解析]]；想繼續照順序讀，回到 [[ICCAD/ICCAD-Dashboard|Dashboard]] 依照「新手從這裡開始」的順序繼續。

---
**相關筆記**：[[Fundamentals/ICCAD-Glossary|速查詞彙表]] · [[Fundamentals/FloorSet-Data-Worked-Example|資料實例解析（真實數字版）]] · [[ICCAD/Problem/FloorSet-Summary|FloorSet 規格快速複習]] · [[ICCAD/Algorithms/B-Star-Tree|B*-tree 技術筆記]] · [[Fundamentals/Optimization-Theory|最佳化理論]] · [[ICCAD/ICCAD-Dashboard|回到 Dashboard]]
