![](assets/Mobaxterm%20Tool%20List/file-20251224205906777.png)
# 🧰 EDA Server Environment & Toolchain Strategy
**Context:** #VLSI #EDA #Research #PhD_Roadmap

---

## 1. Digital IC Design Flow (Cell-Based)
*適用於：CPU 架構、DSP 模組、加速器設計 (RTL to GDSII)*

### 前端設計與驗證 (Front-End)
- **模擬 (Simulation):**
    - `VCS` (Synopsys): 編譯與模擬引擎。 [[Industry Standard]]
    - `Verdi` (Synopsys): 波形除錯 (Debug)、Trace Code 神器。
    - *Alternative:* `XCELIUM` (Cadence, evolved from NC-Verilog).
- **語法與品質檢查 (Linting):**
    - `Spyglass` (Synopsys): 在合成前檢查 CDC (跨時鐘域) 與語法規範。

### 後端合成與實作 (Back-End)
- **邏輯合成 (Synthesis):**
    - `Design Compiler` (Synopsys, DC): 將 RTL 轉為 Gate-level netlist 的核心工具。
- **實體設計 (Place & Route):**
    - `INNOVUS` (Cadence): 先進製程強勢，擁塞度 (Congestion) 解能力強。
    - `IC Compiler 2` (Synopsys, ICC2): 與 DC/VCS 相容性極佳 (Common UI)。
- **靜態時序分析 (Sign-off STA):**
    - `PrimeTime` (Synopsys, PT): 下線前的時序黃金標準 (Setup/Hold check)。

---

## 2. Analog / Full-Custom Design Flow
*適用於：類比電路、記憶體、Standard Cell 繪製、電晶體層級設計*

### 電路設計與模擬 (Schematic & Sim)
- **設計平台:** `Virtuoso` (Cadence, listed as IC6/ICADVM). [[Industry Standard]]
- **模擬引擎:**
    - `SPECTRE` (Cadence): 與 Virtuoso 整合度最高的模擬器。
    - `HSPICE` (Synopsys): 傳統黃金標準，適合純 Netlist 模擬。

### 佈局與驗證 (Layout & Verification)
- **佈局 (Layout):**
    - `Virtuoso Layout XL`: 現代主流，支援強大的自動化輔助。
    - `LAKER` (Synopsys): 台灣部分老牌產線仍使用，Magic Cell 功能強。
- **物理驗證 (DRC/LVS):**
    - `Calibre` (Siemens/Mentor): **絕對權威**。不管前面用什麼畫圖，最後都要過這關。

---

## 3. Environment & Scripting
- **Python:** `Python 3.6.8` (可用於自動化腳本或機器學習輔助 EDA)。
- **License Path:** `@lshc` / `@lstc` (需配置於環境變數)。

---

## 💡 Engineering PhD Insights (博學工程師視角)

> [!TIP] Tool Selection Strategy
> 1. **數位設計 (Digital):** 推薦全套 **Synopsys Flow** (VCS -> DC -> ICC2 -> PT)，資料格式 (`.ndm`, `.upf`) 轉換最少，除錯最快。唯獨 P&R 如果追求極致效能，可考慮 **Innovus**。
> 2. **類比設計 (Analog):** 推薦全套 **Cadence Flow** (Virtuoso + Spectre)，最後用 **Calibre** 做驗證。這是全球通用的標準語言。
> 3. **除錯思維:** 不要只依賴波形，要學會看 `Log file` 和 `Reports`。工具報錯通常都有詳細原因。