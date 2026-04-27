---
Field: EDA / Physical Design
Type: Competition Specification
Confidence: 4
Cross-Domain: [[Machine Learning]], [[Optimization Theory]]
---

> [!abstract] **導航**：[[ICCAD/FloorSet-Summary|⚡ 快速複習 (口訣版)]] | [[ICCAD/FloorSet-Detailed|📚 規格詳解 (完整版)]]

# 📚 ICCAD 2026 Problem C：FloorSet 挑戰賽完整規格

> [!info] 競賽核心
> 這是一場關於「算力與數據博弈」的競賽，旨在解決傳統 EDA 演算法在現代 SoC 複雜約束下的效率瓶頸。

## 1. 競賽背景與動機
- **技術趨勢**：本競賽體現了 [[ICCAD/EDA-Paradigm-Shift|EDA 範式轉移：從規則導向轉向數據驅動]] 的重大變革。
- **現狀**：傳統方法 (SA) 處理 60+ 模組時收斂緩慢 (數小時)。
- **突破**：利用 Intel 提供之 **FloorSet 數據集** (100 萬樣本) 開發 AI 模型。
- **目標**：在「分鐘級」時間內達成高品質的工業級佈局。

## 2. 問題定義 (Problem Definition)
- **輸入**：固定畫布尺寸 $(W, H)$、模組集合 $B$、連線網表 $N$。
- **輸出**：
    1. 每個模組的左下角座標 $(x_i, y_i)$。
    2. 每個模組的實體尺寸 $(w_i, h_i)$。

## 3. 模組類型與約束 (Detailed Constraints)

> [!danger] 硬約束 (Hard Constraints) —— 違規即判無效
> - **Overlap-free**：塊與塊之間絕對不能有物理交疊。
> - **Fixed-outline**：所有組件必須嚴格限制在 $(W, H)$邊界內。
> - **Fixed-shape / Preplaced**：I/O 或記憶體等硬核位置與形狀鎖定。
> - **Area Budget**：軟模組實作面積與目標面積誤差須 $< 1\%$。

> [!warning] 設計規則 (Design Rules) —— 指數級懲罰
> - **MIB (Multi-Instantiation Blocks)**：同組模組的 $w, h$ 最終必須完全一致。
> - **Boundary Constraints**：指定模組必須貼住畫布特定邊 (Edge) 或角 (Corner)。
> - **Grouping**：指定模組集合必須形成物理上的鄰接群組。

## 4. 評分機制 (Scoring Function)
$$Score = (\text{Quality Cost}) \times (\text{Penalty Factor}) \times (\text{Runtime Factor})$$

- **Quality Cost**：
    - **HPWL**：所有連線的半周長總和。
    - **Area Gap**：畫布邊界與最外圍模組間的剩餘空間。
- **Penalty Factor**：軟約束違規採指數級 ($e^v$) 扣分。
- **Runtime Factor**：
    - 快於中位數：獲得 $0.7 \sim 1.0$ 的獎勵乘數。
    - 慢於中位數：分數將被加倍懲罰。

## 5. 數據集分級
- **Lite Dataset**：全矩形 (Rectangular) 模組，適合基礎開發。
- **Prime Dataset**：引入**非矩形 (Rectilinear)** 模組 (L型、T型)，考驗零白空間堆疊能力。

## 6. 核心策略建議
- **Agentic Flow**：利用 LLM 閱讀規格、生成 Seed。
- **混合策略**：ML 模型預測粗略位置 (Rough Placement) + 解析式方法消弭重疊 (Legalization)。
- **算力優化**：使用 CUDA 加速 HPWL 計算或 GNN 網路。

---
**連結**：[[ICCAD/FloorSet-Summary|<< 回到快速複習 (口訣版)]]
