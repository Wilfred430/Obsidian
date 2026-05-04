---
Field: "EDA / Physical Design"
Type: "Competition Specification"
Confidence: 4
Cross_Domain: "Machine Learning, Optimization Theory"
---

> [!abstract] **導航**：[[ICCAD/FloorSet-Summary|⚡ 快速複習 (口訣版)]] | [[ICCAD/FloorSet-Detailed|📚 規格詳解 (完整版)]]

# 📚 ICCAD 2026 Problem C：FloorSet 挑戰賽完整規格

> [!info] 相關領域：[[Machine Learning]], [[Optimization Theory]]

> [!info] 競賽核心
> 這是一場關於「算力與數據博弈」的競賽，旨在解決傳統 EDA 演算法在現代 SoC 複雜約束下的效率瓶頸。

## 1. 競賽背景與動機
- **技術趨勢**：本競賽體現了 [[ICCAD/EDA-Paradigm-Shift|EDA 範式轉移：從規則導向轉向數據驅動]] 的重大變革。
- **現狀**：傳統方法 (SA) 處理 60+ 模組時收斂緩慢 (數小時)。
- **突破**：利用 Intel 提供之 **FloorSet 數據集** (100 萬樣本) 開發 AI 模型。
- **目標**：在「分鐘級」時間內達成高品質的工業級佈局。

## 2. 問題定義 (Problem Definition)
- **輸入**：固定畫布尺寸 (W, H)、模組集合 B、連線網表 N。
- **輸出**：
    1. 每個模組的左下角座標 (x, y)。
    2. 每個模組的實體尺寸 (w, h)。

## 3. 模組類型與約束 (Detailed Constraints)

> [!danger] 硬約束 (Hard Constraints) —— 違規即判無效 ($M=10$)
> - **Overlap-free**：塊與塊之間絕對不能有物理交疊。
> - **Fixed-outline**：所有組件必須嚴格限制在 (W, H) 邊界內。
> - **Fixed-shape Immutability**：**[V9 更新]** 模組尺寸必須精確匹配輸入，不可縮放。
> - **Preplaced Immutability**：**[V9 更新]** 模組的位置 $(x, y)$ 與尺寸 $(w, h)$ 必須精確匹配輸入，不可移動。
> - **Area Budget**：軟模組 (Soft blocks) 實作面積與目標面積誤差須 $\le 1\%$。

> [!warning] 設計規則 (Design Rules) —— 指數級懲罰
> - **Boundary Constraints**：指定模組必須貼住畫布特定邊 (Edge) 或角 (Corner)。
> - **Grouping**：指定模組集合必須形成物理上的鄰接群組（連通分量為 1）。
> - **MIB (Multi-Instantiation Blocks)**：同組模組的尺寸 $(w, h)$ 最終必須完全一致。

## 4. 評分機制與目標函數 (Objective Function)

競賽使用多目標成本函數來衡量佈局質量、約束滿足度與執行效率。

### 4.1 核心公式 (Equation 2)

$$Cost = \begin{cases} \left(1 + \alpha \cdot (HPWL^{gap} + Area^{gap}_{bbox})\right) \times e^{\beta \cdot Violations^{relative}} \times \max(0.7, RuntimeFactor^\gamma), & \text{if feasible} \\ M = 10, & \text{if infeasible} \end{cases}$$

**參數定義：**
- $\alpha = 0.5$：質量權重（HPWL 與 Area 的佔比）。
- $\beta = 2.0$：軟約束違規的指數擴大係數（確保違規代價極高）。
- $\gamma = 0.3$：運行時間的衰減係數（降低運行時間對總分的過度敏感）。
- $\max(0.7, \cdot)$：時間獎勵上限（最多減分 30%）。

---

### 4.2 變數深度解析

#### A. 品質增量 (Quality Gaps)
用來衡量您的方案與「基準最優解 (Baseline)」的差距。
- **$HPWL^{gap}$**：連線長度差距比。
    - 公式：$\frac{(HPWLint + HPWLext) - HPWL^{baseline}}{HPWL^{baseline}}$
    - $HPWL_{int}$ (模組間)：$\sum_{i=1}^k \sum_{j>i} W^{(int)}_{ij} (\Delta x_{ij} + \Delta y_{ij})$，其中 $\Delta x$ 為兩塊 Bounding Box 的最小水平間距。
    - $HPWL_{ext}$ (與引腳)：$\sum_{i=1}^k \sum_{j=1}^r W^{(ext)}_{ij} (|x_{i,center} - x_{t_j}| + |y_{i,center} - y_{t_j}|)$。
- **$Area^{gap}_{bbox}$**：畫布利用率差距比。
    - 公式：$\frac{Area_{bbox} - Area^{baseline}_{bbox}}{Area^{baseline}_{bbox}}$
    - $Area_{bbox}$ 是由所有放置塊定義的最小外接矩形面積。

#### B. 軟約束違規 ($Violations^{relative}$)
這是一個歸一化到 $[0, 1]$ 區間的數值，計算方式如下：
$$Violations^{relative} = \frac{V_{fixed} + V_{preplaced} + V_{grouping} + V_{boundary} + V_{mib}}{N_{soft}}$$

**歸一化因子 ($N_{soft}$)** 是關鍵的估計基礎：
$$N_{soft} = |B_{fixed}| + |B_{preplaced}| + |B_{boundary}| + \sum_{p=1}^P (|G_p| - 1) + \sum_{q=1}^Q (|M_q| - 1)$$
- **解釋**：這代表了系統中「所有可能出錯的點」的總數。例如，一個包含 5 個模組的 Grouping 約束，其最大違規數為 4（當 5 塊完全分離時）。

**各項違規判定 ($V_{type}$)：**
1. **$V_{fixed} / V_{preplaced}$**：若尺寸或位置不符，該塊計為 1。
2. **$V_{boundary}$**：若未觸碰指定的邊或角，計為 1。
3. **$V_{grouping}$**：$\sum (c_p - 1)$，$c_p$ 為該組模組形成的連通分量個數（$c_p=1$ 代表完全連接）。
4. **$V_{mib}$**：$\sum (s_q - 1)$，$s_q$ 為該 MIB 組中出現的不同形狀 (w, h) 種類數。

#### C. 時間因子 ($RuntimeFactor$)
$$RuntimeFactor = \frac{\text{Your Runtime}}{\text{Median Runtime of All Submissions}}$$
- 這是與所有參賽者的中位數做比較。如果您的速度比中位數快 3 倍以上 ($<0.31$)，則獎勵封頂在 $0.7$。

---

### 4.3 得分預估參考
| 方案情境 | 預計得分 | 說明 |
| :--- | :--- | :--- |
| **完美方案 (10x 速度)** | **0.70** | $1.0 \times 1.0 \times 0.7$ |
| **完美方案 (中位數速度)** | **1.00** | $1.0 \times 1.0 \times 1.0$ |
| **10% 品質缺口 + 25% 違規** | **1.24** | $1.05 \times 1.65 \times 0.7$ (假設 10x 速度) |
| **100% 軟約束違規** | **9.09** | 指數項 $e^{2.0} \approx 7.39$ 導致分數飆升 |
| **任一硬約束違規** | **10.00** | 直接判定為 Infeasible |

---

## 5. 數據集分級 (Dataset)
- **Training Set**：100 萬個樣本 (21-120 塊)，包含最優解標籤。
- **Lite Dataset**：全矩形 (Rectangular) 模組，專注於硬塊放置。
- **Evaluation**：100 個測試案例，總分採**指數加權平均**：
    $$\text{Total Score} = \sum_{i=21}^{120} Cost[i] \cdot \frac{e^{n_i}}{\sum e^{n_j}}$$
    - $n_i$ 為塊數。這意味著 **大規模案例 (120 塊) 的權重遠高於小規模案例**。

## 6. 核心策略建議
- **Agentic Flow**：利用 LLM 閱讀規格、生成 Seed。
- **混合策略**：ML 模型預測粗略位置 (Rough Placement) + 解析式方法消弭重疊 (Legalization)。
- **算力優化**：使用 CUDA 加速 HPWL 計算或 GNN 網路。

---
**連結**：[[ICCAD/FloorSet-Summary|<< 回到快速複習 (口訣版)]]