---
title: CIM 運算儲存矩陣知識地圖 (MOC)
tags:
  - Meta/MOC
  - CIM
  - Hardware-Accelerator
date: 2026-05-19
---

# 🧩 CIM 運算儲存矩陣知識地圖 (MOC)

> [!abstract] **導讀**
> 本頁面是 [[張添烜 project/CIM/|CIM 專案]] 的邏輯中心。CIM (Computing-In-Memory) 旨在打破馮紐曼瓶頸，將運算直接整合於儲存單元中。

## 1. 🏗️ 硬體基礎 (The Macro)
理解 CIM 的起點是單個運算宏單元。
- [[張添烜 project/CIM/Macro|CIM Macro 基礎]]：理解 SRAM 陣列如何執行乘加運算 (MAC)。
- **物理參數**：包含 TSMC 22nm 製程下的電壓與頻率限制。

## 2. 🚀 RCW-CIM 進階架構
針對大型語言模型 (LLM) 權重過大的挑戰提出的解決方案。
- [[張添烜 project/CIM/RCW-Mechanisms|RCW 機制 (Read-Compute/Write)]]：核心論文精華，如何隱藏權重更新延遲。
- [[張添烜 project/CIM/Nonlinear-Operator-Fusion|算子融合 (Nonlinear Operator Fusion)]]：Softmax 與部分和累加的硬體加速。

## 3. 📊 效能評估與數據流
- [[張添烜 project/CIM/Latency & Throughput|延遲與吞吐量分析]]：理解 WS-OCS 數據流對系統效能的影響。
- **目標模型**：針對 Llama2-7B 的加速表現分析。

## 🔗 外部關聯
- [[AI/LLM/PEFT-QLoRA|QLoRA 優化]]：硬體加速的主要軟體對象。
- [[AI/Attention|Attention 機制]]：非線性運算的主要應用場景。

---
**回到索引**：[[index|🌐 全域索引 >>]]
