---
Field: "EDA / Physical Design"
Type: "Concept Deep-Dive"
Confidence: 5
Cross_Domain: "Hierarchical Design, Optimization"
---

> [!abstract] **導航**：[[index|🌐 全域索引]] | [[ICCAD/Algorithms/Wong-Liu-Algorithm|📑 經典 SA 算法]] | [[ICCAD/FloorSet-Detailed|📚 FloorSet 規格]]

# Fixed-Outline Floorplanning: 餘裕空間的藝術與約束

在現代 SoC 設計中，佈局規劃 (Floorplanning) 已經從單純的「拼圖遊戲」演變成極其複雜的「餘裕空間管理」。本筆記基於 Adya & Markov (2003) 的研究，探討為何固定輪廓 (Fixed-Outline) 是階層化設計的基石。

## 1. 思維轉換：從「最小化面積」到「餘裕最佳分配」

傳統佈局與固定輪廓佈局在設計哲學上有本質的差異：

| 特性 | 傳統佈局 (Outline-Free) | 固定輪廓佈局 (Fixed-Outline) |
| :--- | :--- | :--- |
| **目標 (Objective)** | 最小化包絡矩形面積 ($Area_{min}$) | 最小化連線長度 ($HPWL$) |
| **餘裕角色** | 越少越好 (被視為浪費) | **預定義約束** (不可變動的空間預算) |
| **邊界限制** | 畫布可隨模組大小伸縮 | 畫布尺寸 $(W_*, H_*)$ 固定，需滿足 $AR$ |
| **設計場景** | 早期單層佈局 | **頂層驅動的階層化設計** |

> [!info] **為何轉變？**
> 在 Fixed-die 流程中，晶片面積早已由製程與成本決定。此時，佈局目標不再是縮小晶片，而是如何在既有的空間內，透過調整模組位置，將「餘裕」留給最需要的地方。

---

## 2. 餘裕 (Whitespace) 的雙面刃

在固定輪廓下，餘裕並非單純的「空地」，它對物理設計有深遠影響：

- **正向影響 (Buffer Islands)**：餘裕可作為 Routing 軌道、插入 Buffer 的緩衝區 (Buffer Islands)，以及解決擁塞 (Congestion) 的逃生路徑。
- **負面影響 (Fragmentation)**：
    - **過碎化**：若餘裕被切割得太細碎，後續無法放入大型 IP 或 Buffer。
    - **時序疑慮**：模組間餘裕過大會強迫連線拉長，導致信號延遲 (RC Delay) 增加，破壞 Timing Closure。

---

## 3. 演算法因應：成本函數與硬性限制

經典演算法（如 SA 搭配 Sequence Pair）在處理 Fixed-Outline 時，最大的挑戰在於搜尋空間中「合法解」極其稀少。

### A. 傳統成本函數的失效
傳統公式 $\Psi = A + \lambda W$ 會讓演算法在邊界不符的情況下仍試圖減小面積。

### B. 現代成本函數 (Penalty-based)
Adya 建議將邊界違反量化為懲罰項：
$$\Psi = \lambda_w \cdot HPWL + \text{Penalty}_{outline}$$
其中 $\text{Penalty}_{outline}$ 常定義為超出部分的線性或平方和：
$$Penalty = \max(W - W_*, 0) + \max(H - H_*, 0)$$

### C. 基於「餘裕 (Slack)」的擾動機制 (Moves)
演算法不再隨機移動，而是計算模組在 $X/Y$ 方向的**佈局餘裕 (Floorplan Slack)**：
- **Slack-based Move**：優先移動處於「關鍵路徑 (Critical Path)」上（即 Slack 為 0）的模組，試圖壓縮該方向的 Span 以進入固定輪廓。

---

## 4. 階層化設計 (Hierarchical Design) 的關聯

Whitespace 的掌控是實現 Top-down 流程的關鍵：

1.  **空間預留**：頂層佈局時，必須為底層子模組預留約 $15\% \sim 20\%$ 的 Whitespace，以應對後續邏輯優化與時序修復的空間膨脹。
2.  **形狀約定**：固定輪廓為子模組設定了「牆壁」。如果頂層沒規劃好餘裕分配，底層子模組將面臨 $M=10$ (Infeasible) 的困境。
3.  **並行開發**：正確的 Fixed-Outline 讓不同團隊能在各自的牆壁內並行工作，而不會干擾全域佈局。

---
**研究連結**：
- [[ICCAD/EDA-Paradigm-Shift|從人工調校到 AI 自動化佈局]]
- [[ICCAD/Algorithms/Wong-Liu-Algorithm|Wong-Liu：最早的佈局優化框架]]
