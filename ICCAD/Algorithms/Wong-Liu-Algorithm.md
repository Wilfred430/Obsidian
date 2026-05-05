---
Field: "EDA / Floorplan"
Type: "Algorithm Definition"
Confidence: 5
Cross_Domain: "Optimization, Simulated Annealing"
---

> [!abstract] **導航**：[[index|🌐 全域索引]] | [[../Problem/FloorSet-Detailed|📚 FloorSet 規格同步]]

# Wong-Liu Floorplanning Algorithm (1986)

Wong-Liu 演算法是 VLSI 物理設計領域的基石，首次成功將模擬退火 (Simulated Annealing, SA) 應用於 Slicing Structure 的佈局優化。

## 1. 核心表示法：正規化波蘭表達式 (NPE)

為了在 SA 的隨機搜尋中保持高效，論文提出了 **Normalized Polish Expressions (NPE)**，解決了狀態冗餘問題。

> [!info] **關鍵屬性：1-1 映射**
> 透過限制右子節點的切割方向不得與父節點相同（Skewed Slicing Tree），確保每一種切割結構與 NPE 具有唯一對應關係，避免 SA 在冗餘狀態空間中空轉。

### NPE 的合法性條件
1.  **運算元 (Operands)**：$1$ 到 $n$ 的模組編號必須各出現一次。
2.  **投票性質 (Balloting Property)**：從左向右掃描，運算元的累計數量隨時大於運算子（$+$ 或 $*$）。
3.  **正規化 (Normalized)**：字串中不允許出現連續相同的運算子（如 $++$ 或 $**$）。

---

## 2. 鄰域搜尋機制 (Neighborhood Moves)

SA 透過以下三種擾動機制在狀態空間中移動：

| 移動類型 | 操作對象 | 合法性保證 |
| :--- | :--- | :--- |
| **M1 (Swap Operands)** | 交換相鄰兩個運算元 | 必定合法 |
| **M2 (Chain Invert)** | 將一段運算子鏈反轉 ($+ \leftrightarrow *$) | 必定合法 |
| **M3 (Swap Op/Operand)** | 交換相鄰的運算元與運算子 | 需檢查 $O(1)$ 條件：$2d_{i+1} < i$ |

> [!warning] **M3 的 $O(1)$ 檢查**
> 為了不破壞 Balloting Property，執行 M3 前需確認前 $i$ 個元素中運算子的數量 $d$ 滿足 $2d_{i+1} < i$。

---

## 3. 成本函數與優化目標

$$Cost = Area + \lambda_w \cdot Wirelength$$

- **Area (面積)**：利用 **Bounding Curve (包絡曲線)** 進行計算。對於 Flexible Blocks，透過 Bottom-up 遞迴合併子樹的長寬組合曲線。
- **Wirelength (線長)**：採用中心點間的曼哈頓距離 (Centroid-based Manhattan Distance)。
    - **注意**：這與 [[../Problem/FloorSet-Detailed|ICCAD 2026 V9]] 規格中的 $HPWL_{int}$ 計算邏輯完全一致。

---

## 4. 效能優化技巧：增量更新 (Incremental Update)

在 SA 迭代過程中，不需要重算整棵 Slicing Tree。每次 Move 僅會影響樹上的一條路徑 (Path) 或分岔路徑 (Fork)，只需局部更新受影響節點的 Bounding Curve，大幅提升了 $O(n)$ 次迭代的速度。

---

## 5. 模擬退火排程 (Annealing Schedule)

- **初始溫度**：$T_0 = \Delta_{avg} / \ln(P)$。
- **降溫係數**：$r = 0.85$ (實驗證明效果最佳)。
- **探索深度**：每一溫度下的嘗試次數 $N = O(n)$。

---
**相關節點**：
- [[ICCAD/EDA-Paradigm-Shift|EDA 範式轉移：從算法導向到數據驅動]]
- [[AI/Data/Evaluation-Metrics|常見佈局評分指標]]
