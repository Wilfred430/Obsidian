---
Field: EDA / Physical Design
Type: Review Summary
Confidence: 5
---

> [!abstract] **導航**：[[ICCAD/FloorSet-Summary|⚡ 快速複習 (口訣版)]] | [[ICCAD/FloorSet-Detailed|📚 規格詳解 (完整版)]]

# ⚡ ICCAD 2026 FloorSet：一、二、三 框架 (口訣版)

> [!info] 背景知識：[[ICCAD/EDA-Paradigm-Shift|EDA 範式轉移 (從傳統規則到 AI 驅動)]]

## 1. 一個核心目標
> [!abstract] **Agentic AI**
> 從「天」到「分鐘」的自動化佈局革命。

## 2. 兩大邊界條件 (Fixed-Outline)
> [!danger] **硬約束 (不可違規)**
> - **Overlap-Free**：絕對零重疊。
> - **Area Accuracy**：軟模組誤差 < 1%。

> [!warning] **軟約束 (違規重罰)**
> - **Boundary** (貼邊)、**Grouping** (聚集)、**MIB** (形狀一致)。

## 3. 三組優化關鍵 (Score)
> [!success] **評分組成**
> - **品質 (Quality)**：線長 (HPWL) 與緊湊度 (Area Gap)。
> - **處罰 (Penalty)**：軟約束違規採指數級 $e^v$ 扣分。
> - **效率 (Efficiency)**：快跑享七折獎勵，慢跑分數飆升。

## 🧠 總結口訣
> [!quote] **心法**
> 「一框定江山，重疊必出局。」
> 「線短分才高，快跑省三成。」
> 「學會百萬圖，代理換人工。」

---
**連結**：[[ICCAD/FloorSet-Detailed|查看完整競賽規格與開發建議 >>]]
