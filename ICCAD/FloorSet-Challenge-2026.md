# ICCAD 2026 FloorSet 挑戰賽：數據驅動 SoC 平面規劃

這項競賽（The FloorSet Challenge）旨在利用機器學習技術革新傳統的晶片佈局流程，將耗時數天的手動調整轉化為分鐘級的自動化作業。

## 1. 一個核心目標：從「天」到「分鐘」的轉化

核心使命是利用 AI/ML 的引導，將晶片佈局（Floorplanning）自動化。

- **關鍵詞**：**Agentic AI（代理人 AI）**。
- **目標**：創造能像工程師一樣對話、思考並快速出圖的 AI 助手，在幾分鐘甚至幾秒鐘內完成佈局。

## 2. 兩大邊界條件：Fixed-Outline 的鐵律

在 [[ICCAD/Floorplanning/Outline-Characteristics|Fixed-outline 模式]] 下，所有模組必須放置在預定義的框架內。

### 硬約束 (Hard Constraints) —— 違規即判出局
- **嚴格禁重疊 (Overlap-Free)**：塊與塊之間不允許任何程度的重疊。
- **面積精準度**：軟模組 (Soft Blocks) 的面積誤差必須控制在 **1% 以內**。

### 軟約束 (Soft Constraints) —— 違規會扣分
- **邊界限制 (Boundary)**：要求貼邊或貼角。
- **成組限制 (Grouping)**：要求特定模組相鄰。
- **MIB (多實例模組)**：同一種模組的長寬必須完全一致。

## 3. 三組優化關鍵：評分標準 (Objective Function)

目標函數越小越好，由以下三者組成：

### 品質 (Quality)
- **HPWL (半周長線長)**：模組、接腳間的連線越短越好。
- **Area Gap**：在固定框內，東西塞得越緊湊、總包圍面積越小越好。

### 處罰 (Penalty)
- **指數級扣分**：軟約束違規會以 $e^v$ 指數型態暴增處罰。

### 效率 (Efficiency)
- **時間係數 (Runtime Factor)**：相較於參賽者中位數的運算速度。
- **獎懲機制**：跑得快（低於中位數），成本可打七折；跑得慢，分數無上限飆升。

## 💡 深度記憶點：FloorSet 數據集
- **數據量**：Intel 與 UC San Diego 提供 100 萬個「保證最優」的佈局樣本。
- **ML 的機會**：透過百萬樣本學習最優佈局的結構規律，克服傳統 SA 演算法在處理大型設計（60 個模組以上）時的速度瓶頸。

## 🧠 總結口訣
> 「一框定江山（Fixed-Outline），重疊必出局（Overlap-Free）。」
> 「線短分才高（HPWL），快跑省三成（Runtime Factor）。」
> 「學會百萬圖（FloorSet），代理換人工（Agentic AI）。」

---
**相關知識節點**：
- [[ICCAD/Floorplanning/Outline-Characteristics|基礎理論：Outline 種類與特性]]
- [[ICCAD/Problem Overview|ICCAD 競賽問題總覽]]
