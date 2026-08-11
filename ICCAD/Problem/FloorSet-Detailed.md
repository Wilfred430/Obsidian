---
Field: "EDA / Physical Design"
Type: "Competition Specification"
Confidence: 4
Cross_Domain: "Machine Learning, Optimization Theory"
---

> [!abstract] **導航**：[[FloorSet-Summary|⚡ 快速複習 (口訣版)]] | [[FloorSet-Detailed|📚 規格詳解 (完整版)]]

# 📚 ICCAD 2026 Problem C：FloorSet 挑戰賽完整規格

> [!info] 相關領域：[[AI/Machine-Learning|Machine Learning]], [[Fundamentals/Optimization-Theory|Optimization Theory]]

> [!info] 競賽核心
> 這是一場關於「算力與數據博弈」的競賽，旨在解決傳統 EDA 演算法在現代 SoC 複雜約束下的效率瓶頸。

## 1. 競賽背景與動機
- **技術趨勢**：本競賽體現了 [[../EDA-Paradigm-Shift|EDA 範式轉移：從規則導向轉向數據驅動]] 的重大變革。
- **現狀**：傳統方法 (SA) 處理 60+ 模組時收斂緩慢 (數小時)。
- **突破**：利用 Intel 提供之 **FloorSet 數據集** (100 萬樣本) 開發 AI 模型。
- **目標**：在「分鐘級」時間內達成高品質的工業級佈局。

## 2. 問題定義 (Problem Definition)
- **輸入**：
    1. 模組集合 $B=\{b_1,...,b_k\}$，每塊 soft-block 帶一個目標面積 $a_i$。
    2. 終端腳位集合 $T=\{t_1,...,t_r\}$，位置在輸入中固定給定，全程不可變。
    3. 模組間連線權重矩陣 $W^{(int)} \in \mathbb{R}^{k \times k}$、模組對腳位連線權重矩陣 $W^{(ext)} \in \mathbb{R}^{k \times r}$。
- **輸出**：
    1. 每個模組的左下角座標 (x, y)。
    2. 每個模組的實體尺寸 (w, h)。

> [!info] **官方規格裡沒有「預先給定的固定畫布 (W, H)」這個輸入**——晶片外框大小是**由你自己擺出來的解反推**：`Area_bbox` 用所有模組實際座標的 `(xmax-xmin)×(ymax-ymin)` 算出來，再拿去跟 baseline 的面積比差距（`Area_gap`）扣分，不是「超出某個固定框就判死」。真正**鎖死**畫面上位置的只有 preplaced/fixed-shape 模組本身跟終端腳位 $T$ 的座標，不是整張畫布的外框尺寸。

## 3. 模組類型與約束 (Detailed Constraints)

> [!danger] 硬約束 (Hard Constraints) —— 違規即判無效 ($M=10$)，官方規格明確只有以下 4 類，沒有第 5 類
> - **Overlap-free**：任兩塊模組的交集面積必須為 0（可以貼邊共享邊界，不算重疊）。
> - **Area Targets and Dimensionality**：軟模組 (Soft blocks) 實作面積與目標面積誤差須 $\le 1\%$；preplaced/fixed-shape 模組的尺寸則完全比照下面兩項鎖死。
> - **Fixed-shape Immutability**：**[V9 更新，原為軟約束]** 模組尺寸必須精確匹配輸入，不可縮放。
> - **Preplaced Immutability**：**[V9 更新，原為軟約束]** 模組的位置 $(x, y)$ 與尺寸 $(w, h)$ 必須精確匹配輸入，不可移動。
>
> **這裡沒有「畫布必須落在固定 (W, H) 範圍內」這一條**——官方規格從沒把畫布尺寸當輸入或硬約束，晶片外框大小是解出來的 bounding box，用 `Area_gap` 這個品質分項扣分，不是判死題。

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
    - **$HPWL_{int}$ (模組間)**：**[V9 重大變更]** 改採中心點 (Centroid) 曼哈頓距離計算。
      $$HPWL_{int} = \sum_{i=1}^k \sum_{j>i} W^{(int)}_{ij} (|c_i^x - c_j^x| + |c_i^y - c_j^y|)$$
      其中 $c_i^x = x_i + w_i/2$，$c_i^y = y_i + h_i/2$。
    - **$HPWL_{ext}$ (與引腳)**：$\sum_{i=1}^k \sum_{j=1}^r W^{(ext)}_{ij} (|c_i^x - x_{t_j}| + |c_i^y - y_{t_j}|)$。
- **$Area^{gap}_{bbox}$**：畫布利用率差距比。
    - 公式：$\frac{Area_{bbox} - Area^{baseline}_{bbox}}{Area^{baseline}_{bbox}}$

#### B. 軟約束違規 ($Violations^{relative}$)
**[V9 更新]** 僅包含邊界、群組與 MIB 違規（Fixed/Preplaced 已移至硬約束）：
$$Violations^{relative} = \frac{V_{grouping} + V_{boundary} + V_{mib}}{N_{soft}}$$

**歸一化因子 ($N_{soft}$)**：
$$N_{soft} = |B_{boundary}| + \sum_{p=1}^P (|G_p| - 1) + \sum_{q=1}^Q (|M_q| - 1)$$

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
| **50% 軟約束違規（中位數速度）** | **2.72** | $1.0 \times e^{1.0} \approx 2.72$ |
| **100% 軟約束違規 + 慢 2 倍** | **9.09** | $1.0 \times e^{2.0}(\approx 7.39) \times 1.23$，光違規本身（中位數速度）只到 7.39，要疊上跑慢的懲罰才到 9.09 |
| **任一硬約束違規** | **10.00** | 直接判定為 Infeasible |

---

## 5. 數據集分級 (Dataset)
- **Training Set**：100 萬個樣本 (21-120 塊)，包含最優解標籤（`get_training_dataloader()` 自動下載）。
- **Validation Set**：100 個樣本（21~120 塊各一個），開放給參賽者自行驗證（`get_validation_dataloader()`）。
- **Test Set**：100 個樣本（21~120 塊各一個），對參賽者隱藏，正式送出評分用。
- **Lite Dataset**：全矩形 (Rectangular) 模組，專注於硬塊放置。
- **Evaluation**：100 個測試案例，總分採**指數加權平均**（**注意分母是 $n_i/12$，不是單純 $n_i$**）：
    $$\text{Total Score} = \sum_{i=21}^{120} Cost[i] \cdot \frac{e^{n_i/12}}{\sum_{j=21}^{120} e^{n_j/12}}$$
    - $n_i$ 為塊數。**大規模案例 (120 塊) 的權重遠高於小規模案例**——120 塊 case 的權重是 21 塊 case 的 $e^{(120-21)/12} = e^{8.25} \approx 3820$ 倍。這個係數是 **每 12 個 block 的加權倍率**，不要誤植成純 $e^{n_i}$（那樣算出來會是 $e^{99}\approx 8\times10^{42}$ 倍，錯了 39 個數量級，2026-07-01 已用官方 spec PDF 截圖核對訂正過）。
    - 一個「所有 case 都打平 baseline、且用中位數 runtime」的完美方案，Total Score 會落在 **1.0** 附近。

## 6. 核心策略建議
- **Agentic Flow**：利用 LLM 閱讀規格、生成 Seed。
- **混合策略**：ML 模型預測粗略位置 (Rough Placement) + 解析式方法消弭重疊 (Legalization)。
- **算力優化**：使用 CUDA 加速 HPWL 計算或 GNN 網路。

---
**連結**：[[FloorSet-Summary|<< 回到快速複習 (口訣版)]]