# ICCAD 2026 CAD Contest 題目分析與優化筆記

**Tags:** #EDA #IC_Design #MachineLearning #Algorithm #ICCAD2026
**Date:** 2026-03-02

---

## 總覽與核心思維
這四個題目涵蓋了現代電子設計自動化（EDA）最前沿的挑戰，從前段的邏輯合成、驗證，到後段的實體設計與時序收斂。身為未來的工程博士，破題的關鍵不在於死磕單一規格，而是從「系統架構」與「跨領域整合（如 AI + EDA）」的高度出發，設計出具備強健性與可擴展性的軟體工具。

---

## [cite_start]問題 A：LLM-Assisted Netlist Exploration and Transformation [cite: 828, 833, 835]

### 1. 核心方向：打造能聽懂人話的 EDA 代理人 (AI Agent)
[cite_start]解決現有 EDA 工具指令繁雜、學習曲線過陡的痛點 [cite: 834][cite_start]。開發一個系統，能接收自然語言指令，並自動將其轉換為精確的底層 Netlist 分析與修改操作 [cite: 840, 841]。

### 2. 具體內容與挑戰
* [cite_start]**輸入與輸出**：讀取單一 Module 的展平 (flattened) Gate-level Verilog 網表 [cite: 896, 906][cite_start]。根據自然語言請求進行分析，輸出文字回應或修改後的 Verilog 檔案 [cite: 897, 966]。
* [cite_start]**任務類型**：包含基礎操作（讀寫）、分析任務（如尋找特定路徑、計算深度）、以及轉換優化任務（如邏輯閘替換、Buffer 插入） [cite: 973, 989, 1002]。
* [cite_start]**嚴格限制**：所有修改絕對不能改變電路原有的邏輯功能（Functional Equivalence） [cite: 1091][cite_start]。單一基礎請求需在 60 秒內完成，其餘請求限制為 300 秒 [cite: 903]。

### 3. 博士級優化思路
* [cite_start]**API 抽象化與 Schema 設計**：LLM 本身不懂底層程式碼 [cite: 836][cite_start]。需設計一套極度簡潔且具備防呆機制的 API，並在 Prompt 中清晰定義 Schema，讓 LLM 扮演「規劃者」，程式扮演「執行者」 [cite: 894]。
* [cite_start]**Verification-in-the-loop**：必須在系統內部實作輕量級的等效性檢查演算法 [cite: 874]。當 LLM 提出修改建議時，先在沙盒中驗證邏輯等效後再正式修改。

---

## [cite_start]問題 B：Regression Failure Bucketing [cite: 525, 542]

### 1. 核心方向：巨量除錯日誌的資料科學化
[cite_start]在 RTL 驗證階段，傳統依賴人工寫規則的錯誤分類方式已無法擴展 [cite: 544][cite_start]。此題要求使用資料驅動 (Data-driven) 的方法，將同一個 Bug 造成的不同失敗案例自動且精準地分群 [cite: 545, 546]。

### 2. 具體內容與挑戰
* [cite_start]**輸入特徵**：每個失敗案例提供三個檔案：`regr.log`（RTL 與 ISS 比對的錯誤紀錄）、`sim.log`（UVM 測試平台資訊）、`trace.log`（CPU 指令執行軌跡） [cite: 592, 597, 615, 640]。
* [cite_start]**輸出目標**：為每個案例分配一個 Bucket ID [cite: 750][cite_start]。同一個 Bug 造成的失敗必須分在同一個 Bucket [cite: 587]。
* [cite_start]**評分標準**：採用 Pairwise Balanced Accuracy，評估任兩個案例是否被正確地分在同群或不同群 [cite: 763, 765]。

### 3. 博士級優化思路
* [cite_start]**多模態特徵工程 (Feature Engineering)**：針對 `sim.log` 萃取 UVM_FATAL 關鍵字 [cite: 617][cite_start]；針對 `trace.log` 將 Program Counter (PC) 序列轉換為 N-gram 或使用輕量級的 Sequence Embedding 模型 [cite: 641, 791]。
* [cite_start]**分層分群策略 (Hierarchical Clustering)**：先利用強烈特徵（如 Error 種類）進行粗分群，接著在每個子群內，利用指令軌跡的向量相似度配合 DBSCAN 或 K-Means 進行細部分群 [cite: 547]。

---

## [cite_start]問題 C：Data-Driven SoC Floorplanning [cite: 184, 190, 191]

### 1. 核心方向：打破古典演算法極限的機器學習佈局
[cite_start]傳統 SoC Floorplanning 是 NP-Complete 問題，面對龐大區塊與複雜限制往往難以快速收斂 [cite: 190, 198][cite_start]。此題旨在利用機器學習技術，將探索最佳解的時間從數天縮短至數分鐘內 [cite: 200, 201]。

### 2. 具體內容與挑戰
* [cite_start]**輸入限制**：處理具有面積預算的軟性區塊 (Soft blocks)、固定形狀區塊與預先放置的區塊 [cite: 235, 249, 252]。
* [cite_start]**硬性限制 (Hard Constraints)**：必須嚴格遵守「零重疊 (Zero Overlap)」以及「面積誤差 $\le 0.01$」的幾何限制，違規即視為無效解並給予重罰 [cite: 258, 262, 266]。
* [cite_start]**多目標最佳化**：成本函數需同時最小化半周長線長 (HPWL)、邊界框面積 (Bounding-box Area)，並考量軟性限制違規次數與程式執行時間 [cite: 196, 282]。

### 3. 博士級優化思路
* [cite_start]**混合式架構 (Hybrid Solver)**：純神經網路難以保證剛性幾何限制 [cite: 224][cite_start]。可考慮使用圖神經網路 (GNN) 或強化學習決定區塊的「相對拓樸關係」（如 Sequence-Pair），再將初始解交給解析法或線性規劃 (LP) 進行合法化 (Legalization) [cite: 232]。
* **極速碰撞偵測**：實作基於掃描線 (Sweep-line) 或區間樹 (Interval Tree) 的 $O(n \log n)$ 重疊偵測機制，大幅提升演算法迭代速度。

---

## [cite_start]問題 D：Timing Fixing by Useful Skew [cite: 1, 2, 7]

### 1. 核心方向：榨出每一奈秒效能的時脈樹工程
[cite_start]時序收斂 (Timing Closure) 是晶片設計後段的最大挑戰 [cite: 7][cite_start]。此題要求利用「有用時脈偏差 (Useful Skew)」，透過在時脈樹 (Clock Tree) 中插入 Buffer，刻意延遲特定正反器 (FF) 的時脈，以修復時序違規 [cite: 8]。

### 2. 具體內容與挑戰
* [cite_start]**操作限制**：給定初始時脈樹、Buffer 元件庫與 Data path delay [cite: 11, 12][cite_start]。僅允許「插入新 Buffer」或「調整現有 Buffer 尺寸」 [cite: 11]。
* [cite_start]**多情境考量 (Multi-Corner)**：必須同時滿足 SS corner（最慢環境，檢查 Setup time 與 WNS/TNS）與 FF corner（最快環境，檢查 Hold time 與 WNS/TNS） [cite: 41]。
* [cite_start]**權衡指標**：評分公式考量了 SS 與 FF corner 下的 Total Negative Slack (TNS) 與 Worst Negative Slack (WNS) 改善程度，並會扣除增加的晶片面積成本 [cite: 172]。

### 3. 博士級優化思路
* **動態規劃 (Dynamic Programming)**：從時脈樹的葉節點 (SINK) 往 Root 建立候選解集合，記錄不同 Buffer 組合下的 (Delay, Area) Pareto Frontier，類似 Van Ginneken 演算法的概念。
* **線性規劃輔助時間預算 (Delay Budgeting)**：下手修改前，先建立 LP 模型算出每個節點「理想的到達時間」目標值，再用啟發式演算法挑選最接近該目標值的 Buffer 組合，避免盲目搜尋。