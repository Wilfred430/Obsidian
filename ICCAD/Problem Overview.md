---
Field: ICCAD
Type: Research Overview
Confidence: 5
---

# 🏆 ICCAD 競賽問題總覽

此檔案記錄了 ICCAD 相關競賽題目的核心分析與優化思路。

---

## [cite_start]問題 A：RTL Bug Classification [cite: 544, 545]
(內容保留，省略細節...)

---

## [cite_start]問題 C：Data-Driven SoC Floorplanning [cite: 184, 190, 191]

### 1. 核心挑戰：Agentic AI 驅動的自動化佈局
[cite_start]傳統佈局需數天人工迭代，此題要求利用 Intel 提供之 **FloorSet**（100 萬個樣本）訓練 ML 模型，在數分鐘內達成高品質佈局 [cite: 363, 386]。

### 2. 核心架構：FloorSet 挑戰賽深層解析
* [[ICCAD/FloorSet-Summary|⚡ 快速複習 (一目標、兩邊界、三關鍵)]]
* [[ICCAD/FloorSet-Detailed|📚 競賽規格完整詳解版 (詳盡約束與評分公式)]]

### 3. 博士級優化思路
* [cite_start]**混合式架構 (Hybrid Solver)**：純神經網路難以保證剛性幾何限制 [cite: 224][cite_start]。可考慮使用圖神經網路 (GNN) 或強化學習決定區塊的「相對拓樸關係」，再將初始解交給解析法或線性規劃 (LP) 進行合法化 [cite: 232]。
* **極速碰撞偵測**：實作基於掃描線 (Sweep-line) 或區間樹 (Interval Tree) 的重疊偵測機制。

---

## [cite_start]問題 D：Timing Fixing by Useful Skew [cite: 1, 2, 7]

### 1. 核心方向：榨出每一奈秒效能的時脈樹工程
(內容保留，省略細節...)
