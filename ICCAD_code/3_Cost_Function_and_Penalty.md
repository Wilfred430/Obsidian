---
title: Cost Function 與動態懲罰機制 (Evaluation)
tags: [ICCAD, EDA, Cost-Function, Penalty, Math]
date: 2026-06-17
---

# 3. Cost Function 與數學約束 (Cost Function & Penalty)

> **核心角色**：在 `cost.hpp` 中，程式定義了如何評估當前 B*-Tree 狀態的「好壞」。在 Simulated Annealing 中，如果新的狀態 Cost 變小，就會無條件接受；如果變大，則以機率 $e^{-\Delta / T}$ 接受。為了確保 SA 能朝向「合法 (Feasible)」且「高品質」的佈局前進，我們必須將多個互相衝突的物理指標（面積、線長、各類約束）揉合進一個單一標量 (Scalar)。

## 3.1 雙重 Cost 架構

在專案設計上，我們將 Cost 分為兩種：
1. **SA Cost (連續性平滑函數)**：給退火引擎看。它將原本非黑即白的「違規 (Violation)」轉化為連續的「重疊面積 (Overlap Area)」或「形變誤差量」，讓 SA 能夠感受到「漸入佳境」的梯度 (Gradient Descent)。
2. **Contest Cost (官方最終評分)**：嚴格依照 ICCAD 2026 v9 規格書的公式計算。只要出現硬違規 (Hard Violation)，該項分數直接噴到無限大 ($M=10$)。

## 3.2 基礎 Cost：面積與線長 (Area & HPWL)

### A. 面積 (Area_bbox)
評估整個晶片的佔地面積。公式為所有 Block 的外接矩形 (Bounding Box) 面積：
$$ \text{Area} = \max(x_i + w_i) \times \max(y_i + h_i) $$

### B. 半周長線長 (Half-Perimeter Wirelength, HPWL)
用來評估模組間連線（Netlist）的擁擠程度。我們將 HPWL 拆分成內部與外部兩種，以分別賦予不同的權重 ($W_{int}$ 與 $W_{ext}$)：
- **內部連線 ($HPWL_{int}$)**：Block 到 Block 之間的連線。
- **外部連線 ($HPWL_{ext}$)**：Block 到外部固定腳位 (Terminal / Pin) 的連線。這是唯一能將整個 Floorplan「錨定 (Anchor)」在特定區域的力量。

$$ HPWL = \sum_{net \in N} \left( \max_{i \in net}(x_i) - \min_{i \in net}(x_i) + \max_{i \in net}(y_i) - \min_{i \in net}(y_i) \right) $$

### C. 為什麼需要 Baseline (正規化)？
**報告必考題**：如果直接相加 $\text{Cost} = W_a \cdot \text{Area} + W_h \cdot \text{HPWL}$，會發生什麼事？
- **面積**的數值可能高達 $50000 \mu m^2$。
- **線長**的數值可能只有 $5 \mu m$。
- 在 SA 中，面積的變化量 $\Delta$ 會完全主導 $\Delta E$，導致 SA 變成在做「純縮小面積」的瞎忙，完全忽略了連線長度。
- **解決方案**：導入 `BASELINE_AREA` 與 `BASELINE_HPWL`，將數值**正規化 (Normalize)** 到 $O(1 \sim 10)$ 的數量級。

$$ \text{Normalized Cost} = W_a \left( \frac{\text{Area}}{\text{Baseline Area}} \right) + W_{h} \left( \frac{HPWL}{\text{Baseline HPWL}} \right) $$

## 3.3 軟性與硬性約束懲罰 (Penalties)

ICCAD 2026 競賽有許多棘手的實體約束，如果違反，必須在 `sa_cost` 中施加極大的懲罰權重 (Adaptive/High Penalty)。

### A. Soft Constraints (軟約束)
這些是違反不會死，但會扣分的項目：
1. **Grouping (群組相鄰)**：同群組的 Block 若不相鄰，記錄一次 $V_g$。權重極高 ($W_{group}=500$)。
2. **MIB (Macro 共享形狀)**：同 MIB 內的 Block 形狀若不一致，記錄一次 $V_m$。
3. **Boundary (邊界緊貼)**：規定要靠邊的沒靠邊，記錄一次 $V_b$。

官方的懲罰是呈**指數增長 (Exponential)** 的：
$$ \text{Penalty}_{soft} = \exp\left( 2 \times \frac{V_g + V_m + V_b}{N_{soft}} \right) $$

### B. Hard Constraints (硬約束)
違反這些項目，官方直接判出局 ($M=10$)：
1. **Overlap (重疊)**：在 SA Cost 中，我們不只看「有沒有重疊」，而是計算「**重疊了多少面積 (overlap_area)**」，並乘上 $W_{overlap} = 5000$，迫使 SA 強烈排斥重疊狀態。
2. **Area Tolerance (軟模組形變)**：Soft block 的面積誤差超過 $1\%$。我們計算誤差比例，乘上 $W_{softarea} = 5000$。

### C. 固定輪廓 (Fixed-Outline / Aspect Ratio)
在實體設計中，晶片長寬比 (Aspect Ratio) 也是痛點。雖然 v9 放寬了硬性 AR 限制，但我們可以透過 `w_outline` 權重介入：
$$ \text{Cost}_{outline} = W_{outline} \times \left| \log\left( \frac{\text{bbox\_w}}{\text{bbox\_h}} \right) \right| $$
當寬高越接近（正方形），$\log(1) = 0$，懲罰越小。但通常我們會讓 **外部腳位 ($HPWL_{ext}$)** 自動引導 SA 長出最適合外圍腳位的形狀，而非強制壓成正方形。