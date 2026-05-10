# TMS320C6000 核心架構與 Pipeline

> [!info] 核心概念：VLIW (Very Long Instruction Word)
> [[TMS320C6000]] 系列採用 TI 開發的 [[VelociTI]] 架構，這是一種進階的 [[VLIW]] 設計。與傳統超純量 (Superscalar) 架構不同，[[VLIW]] 將「指令排程」與「資源分配」的複雜度從硬體移轉到了編譯器 (Compiler)。
> **優勢：**
> 1. **簡化硬體設計**：無需複雜的指令亂序執行 (Out-of-order execution) 邏輯，大幅降低功耗與晶片面積。
> 2. **可預測性 (Determinism)**：Pipeline 的行為在編譯時即確定，對於即時 (Real-time) 系統至關重要。
> 3. **極高並行度**：單個時鐘週期最多可執行 8 條 32-bit 指令，達成強大的運算吞吐量。

---

## 1. 運算單元與資料路徑 (Data Paths)

[[TMS320C6000]] 核心分為兩個對稱的側邊：**Path A** 與 **Path B**。每個路徑包含四個運算單元與一個 [[Register File]] (32 個 32-bit 暫存器)。

### 八個運算單元分工表

| 單元名稱 | 主要功能 | 關鍵操作 |
| :--- | :--- | :--- |
| **.L1 / .L2** | 算術邏輯單元 (ALU) | 整數/浮點算術、邏輯運算、[[Doubleword]] 操作。 |
| **.S1 / .S2** | 輔助 ALU / 移位器 | 位元移位、分支指令 (Branch)、比較運算、常數生成。 |
| **.M1 / .M2** | 乘法器 (Multiplier) | 16x16 或 32x32 位元乘法、複數乘法運算。 |
| **.D1 / .D2** | 資料處理單元 | 加載 (Load)、儲存 (Store)、位址計算、整數加減法。 |

### 資料交叉路徑 (Cross Paths)
為了讓 Path A 的單元能存取 Path B 的暫存器，硬體設計了 **1X** 與 **2X** 交叉路徑。
> [!warning] 隱藏陷阱
> 每路徑在單一時鐘週期內僅能支援一個交叉存取 (Cross-path Read)。若編譯器嘗試同時讓兩個單元從對側讀取，將產生編譯錯誤或資源衝突 (Resource Conflict)。

---

## 2. Pipeline 階段流程詳解

[[TMS320C6000]] 的流水線非常深，這使得時脈頻率能大幅提升。整體分為三個大階段：擷取、解碼、執行。

### 階段定義
1. **擷取 (Fetch, 4 stages)**：
   - **PG (Program Generate)**：產生程式計數器 (PC) 位址。
   - **PS (Program Send)**：將位址發送到記憶體系統。
   - **PW (Program Wait)**：等待記憶體回應。
   - **PR (Program Ready)**：指令到達 [[Fetch Packet]] 暫存器。
2. **解碼 (Decode, 2 stages)**：
   - **DP (Dispatch)**：將 [[Fetch Packet]] 分配為多個 [[Execute Packet]]。
   - **DC (Decode)**：解析指令指令，指派到對應的運算單元。
3. **執行 (Execute, 5+ stages)**：
   - **E1~E5**：指令實際執行的階段。注意，並非所有指令都需要 E5 才能完成。例如整數加法在 E1 即可完成，而 [[Load]] 指令則需到 E5 才能獲取資料。

### Pipeline 流程圖 (Mermaid)

```mermaid
graph LR
    subgraph Fetch_Phase [擷取階段]
        PG[PG: 位址產生] --> PS[PS: 位址發送]
        PS --> PW[PW: 等待]
        PW --> PR[PR: 指令就緒]
    end

    subgraph Decode_Phase [解碼階段]
        PR --> DP[DP: 指令分配]
        DP --> DC[DC: 指令解碼]
    end

    subgraph Execute_Phase [執行階段]
        DC --> E1[E1: 開始執行]
        E1 --> E2[E2]
        E2 --> E3[E3]
        E3 --> E4[E4]
        E4 --> E5[E5: 結果寫回]
    end

    style Fetch_Phase fill:#f9f,stroke:#333
    style Decode_Phase fill:#bbf,stroke:#333
    style Execute_Phase fill:#bfb,stroke:#333
```

---

## 3. Memory Stall 機制

### 什麼是 [[Memory Stall]]？
當 CPU 需要資料進行運算，但資料尚未從記憶體（如 L2 SRAM 或外部 SDRAM）準備好時，Pipeline 就會強行停止，稱為 **Stall**。

### 為什麼會發生？
1. **Cache Miss**：資料不在 L1D 快取中，需向 L2 或外部存取。
2. **Bank Conflict**：當多個單元（如 .D1 與 .D2）同時存取同一記憶體 Bank 的不同位址時。
3. **資源衝突**：如上述的交叉路徑存取限制。

> [!warning] 設計考量
> C6000 是一台「強時序性」機器。如果發生 Stall，整個硬體 Pipeline 會「凍結」，直到資料就緒。這會嚴重破壞編譯器預測的效能。

---

## 4. Single vs. Multiple Assignment

在編寫 [[DSP]] 組合語言或進行編譯優化時，暫存器的賦值模式至關重要。

### Single Assignment (單次賦值)
- **定義**：在一個指令的 [[Delay Slot]] 期間，不對目標暫存器進行第二次寫入。
- **優點**：程式行為易於理解，Pipeline 不會發生寫回衝突 (Write-back Conflict)。

### Multiple Assignment (多次賦值)
- **定義**：在一個長週期指令（如 Load, E5 完成）尚未寫回暫存器前，另一個短週期指令（如 ADD, E1 完成）已經嘗試寫入同一個暫存器。
- **後果**：舊的指令結果（原本該在較晚時間點到達）會覆蓋掉新的指令結果，造成邏輯錯誤。

> [!example] 為什麼 ISTP 要對齊 1KB？
> 在 [[TMS320C6000]] 中，中斷服務表指標 ([[ISTP]]) 必須 1KB 對齊。
> **原因**：
> 1. 每個中斷向量由一個 [[Fetch Packet]] 組成 (8 條指令 = 32 bytes)。
> 2. 系統共支援 32 個中斷。
> 3. 總空間 = $32 \times 32 = 1024$ Bytes (1KB)。
> 為了讓硬體能透過簡單的位元拼接（中斷編號左移 5 位元）來定位跳躍位址，而不需要複雜的加法器，將基底位址對齊 1KB 是最優化的硬體設計。

---
**相關連結：**
- [[Memory_Map與EMIF]]
- [[EDMA_控制器原理]]
- [[DSP_最佳化技巧]]
