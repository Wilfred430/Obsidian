---
title: RCW-CIM Architecture
tags:
  - CIM
  - LLM-Accelerator
  - Hardware-Design
date: 2026-05-19
---

# RCW-CIM Architecture: Read-Compute/Write

## 1. 核心機制：隱藏權重更新延遲 (Hiding Latency)
傳統 CIM 架構在權重容量不足時，必須停下運算來載入新權重。RCW 架構透過 **Weight Buffer** 實現了動態流水線 (Pipeline)：
- **運算階段**：系統使用「當前權重」進行矩陣運算。
- **寫入階段**：同時，Weight Buffer 悄悄將「下一批權重」寫入陣列備用。
- **結果**：成功將權重更新的等待時間隱藏，在 Llama2-7B 模型上減少了 21.59% 的解碼運算延遲。

## 2. 數據流優化：WS-OCS
結合了 **Weight-Stationary (WS)** 與 **Output-Column-Stationary (OCS)**：
- **WS (權重固定)**：減少權重更新頻率。
- **OCS (輸出列固定)**：將「部分和 (Partial Sums)」留在晶片內計算，避免頻繁存取外部 DRAM。
- **效益**：在 1024 token 的預填 (Prefill) 階段，減少了 51.6% 的 DRAM 存取與 87.6% 的權重更新。

---
## 相關節點
- [[張添烜 project/CIM/Latency & Throughput|延遲與吞吐量分析]]
- [[張添烜 project/CIM/Macro|CIM Macro 基礎]]
- [[AI/LLM/PEFT-QLoRA|LLM 量化技術]] (硬體加速對象)
