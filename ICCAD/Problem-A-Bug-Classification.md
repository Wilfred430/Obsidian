---
Field: ICCAD
Type: Research Overview
Confidence: 4
---

# 🏆 ICCAD 問題 A：RTL Bug Classification

> [!abstract] **核心方向：巨量除錯日誌的資料科學化**
> 在 RTL 驗證階段，傳統依賴人工寫規則的錯誤分類方式已無法擴展。此題要求使用**資料驅動 (Data-driven)** 的方法，將同一個 Bug 造成的不同失敗案例自動且精準地分群。

## 📌 具體內容與挑戰

> [!info] **輸入特徵 (Input)**
> 每個失敗案例提供三個檔案：
> 1. `regr.log`：RTL 與 ISS 比對的錯誤紀錄。
> 2. `sim.log`：UVM 測試平台資訊。
> 3. `trace.log`：CPU 指令執行軌跡。

> [!success] **目標與評分**
> - **輸出目標**：為每個案例分配一個 **Bucket ID**。同一個 Bug 造成的失敗必須分在同一個 Bucket。
> - **評分標準**：採用 **Pairwise Balanced Accuracy**，評估案例間是否被正確地分在同群或不同群。

## 💡 博士級優化思路

> [!tip] **多模態特徵工程 (Feature Engineering)**
> - 針對 `sim.log` 萃取 `UVM_FATAL` 關鍵字。
> - 針對 `trace.log` 將 Program Counter (PC) 序列轉換為 N-gram 或使用輕量級的 **Sequence Embedding** 模型。

> [!tip] **分層分群策略 (Hierarchical Clustering)**
> 先利用強烈特徵（如 Error 種類）進行**粗分群**，接著在每個子群內，利用指令軌跡的向量相似度配合 **DBSCAN** 或 **K-Means** 進行細部分群。

---
**回到索引**：[[index|🌐 全域索引 >>]]
