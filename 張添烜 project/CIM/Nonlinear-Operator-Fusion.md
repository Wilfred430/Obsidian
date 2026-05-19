---
title: Nonlinear Operator Fusion in CIM
tags:
  - CIM
  - Optimization
  - Nonlinear-Operation
date: 2026-05-19
---

# Nonlinear Operator Fusion (非線性運算融合)

## 1. 背景與挑戰
在傳統 [[張添烜 project/CIM/Macro|CIM]] 架構中，Softmax 等非線性運算通常需要將中間結果（部分和）送回 DRAM，等全部算完再讀回處理。這種頻繁的 DRAM 存取會產生巨大延遲。

## 2. 解決方案：算子融合 (Fusion)
RCW-CIM 提出的融合機制：
- **內建處理**：透過有效的部分和累加 (Partial Accumulation) 與基於群組的近似 (Group-based Approximation)。
- **流水線化**：將非線性運算直接整合進運算流程中，不中斷流水線。
- **效益**：達成 **69.17%** 的延遲縮減。

---
## 相關連結
- [[張添烜 project/CIM/RCW-Mechanisms|RCW-CIM 架構主頁]]
- [[AI/Attention|Attention 機制]] (Softmax 應用場景)
