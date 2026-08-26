# 🧠 My Second Brain (Obsidian)

> [!abstract] **數位孿生哲學**
> 知識不應只是靜態筆記的堆積，而應是相互激盪、有機連結的語意網絡。
> 本倉庫是我在 **「EDA / 晶片實體設計」**、**「CIM 記憶體內計算」**、**「生成式 AI / 深度學習」** 與 **「嵌入式控制系統」** 上的核心知識中樞與研發實作大腦。

---

## 🧭 知識庫全景導航地圖 (Atlas)

```text
                                    ┌── 🏆 ICCAD 2026 Problem C (FloorSet 佈局求解器)
                                    ├── ⚡ CIM 加速器 (張添烜教授專案 / RCW-CIM 架構)
            ┌── 🚀 核心專案實作 ────┼── 🧪 銀遷移行為模擬 (Sliver Migration)
            │                       └── 🏗️ DCS 數位控制系統 (TMS320C6000 架構)
            │
            │                       ┌── 📐 VLSI 實體設計 & 最佳化理論 (Fixed-Outline / SA / LP)
My Second   ├── 📚 理論與基礎概念 ──┼── 🌊 生成式 AI (Diffusion Models / DDPM / Score-based)
  Brain     │                       └── 🔷 深度學習與 LLM (Transformer / Attention / QLoRA)
            │
            │                       ┌── 🎨 Electronic_Pipeline.canvas (SOTA 管線架構畫布)
            └── 🛠️ 工具、規範與日誌 ─┼── 📜 Electronic_Pipeline.md (全方塊架構對照說明)
                                    └── 📜 log.md (知識攝取、重構與實驗操作時間軸)
```

---

## 🚀 1. 核心研發專案 (Active Engineering Projects)

### 🏆 ICCAD 2026 Problem C — FloorSet 固定外框平面規劃
競賽等級 EDA 晶片實體設計專案，歷經三代演進（B\*-tree SA $\to$ ML Warm-Start $\to$ 連續靜電場 + 切割樹 + HiGHS 凸壓實）：
* **全域儀表板**：[[ICCAD/ICCAD-Dashboard|🏆 ICCAD 2026 競賽儀表板]]（規格演進、分數尺標與名次追蹤）
* **規格與理論**：[[ICCAD/Problem/FloorSet-Detailed|FloorSet 規格詳解]]、[[Fundamentals/FloorSet-Data-Worked-Example|真實數據解析]]、[[Fundamentals/ICCAD-Glossary|專案速查詞彙表]]
* **管線可視化畫布**：[[Electronic_Pipeline.canvas|🎨 Electronic_Pipeline SOTA 架構畫布]]
* **管線詳解文件**：[[ICCAD_code/Electronic_Pipeline|📜 Electronic_Pipeline 完整方塊說明]]、[[ICCAD_code/Electro_Parameters_Reference|參數速查手冊]]
* **12 篇核心教材庫 (`ICCAD_code/`)**：
  1. [[ICCAD_code/1_Data_Loader_and_Wrapper|1. 資料載入與 ML 介接]] & [[ICCAD_code/1b_Input_Output_Contract|1b. I/O 契約規格]]
  2. [[ICCAD_code/2_SA_Optimizer_Engine|2. SA 退火引擎與 B*-tree]]
  3. [[ICCAD_code/3_Cost_Function_and_Penalty|3. 成本函數與約束數學]]
  4. [[ICCAD_code/4_Packing_and_Evaluation|4. 拓撲打包與幾何座標推算]]
  5. [[ICCAD_code/5_ML_Coordinate_Regression|5. ML 座標回歸模型]]
  6. [[ICCAD_code/6_ML_Generative_BTree|6. 生成式 B*-Tree 與 Diffusion]]
  7. [[ICCAD_code/7_Electrostatic_Placer|7. 靜電場全域放置器 (Poisson DCT)]]
  8. [[ICCAD_code/8_Winning_Strategy_and_Roadmap|8. 奪冠策略總覽]]（搭配 [[ICCAD_code/8x_Research_Log/2026-07_Research_Log|7月日誌]] 與 [[ICCAD_code/8x_Research_Log/2026-08_Research_Log|8月日誌]]）
  9. [[ICCAD_code/9_Research_Tool_Workflow.md|9. 研究工具與自動化腳本流程]]
  10. [[ICCAD_code/10_Claude_Code_Skills_Reference|10. AI 研究與開發 Skill 指令參考]]
  11. [[ICCAD_code/11_Gemini_Deep_Dive_Electro_Pipeline|11. 靜電場管線深入診斷]]
  12. [[ICCAD_code/12_Deep_Dive_Math_Study_Guide|12. 碩士級數學深度教材（Poisson / Dirichlet / LP / Slicing）]]

### ⚡ CIM 記憶體內計算加速器 (張添烜教授實驗室專案)
針對 AI 加速晶片之運算儲存矩陣設計與架構探索：
* **核心樞紐**：[[張添烜 project/CIM/CIM-MOC|⚡ CIM 加速器知識地圖]]
* **架構機制**：[[張添烜 project/CIM/RCW-Mechanisms|RCW-CIM 架構與隱藏延遲]]、[[張添烜 project/CIM/Macro|CIM 運算儲存矩陣]]
* **優化與效能**：[[張添烜 project/CIM/Nonlinear-Operator-Fusion|非線性運算子融合]]、[[張添烜 project/CIM/Latency & Throughput|延遲與吞吐量建模]]

### 🧪 材料與物理模擬 (Sliver Migration)
* **研究核心**：[[Sliver Migration/教授回答|銀遷移行為模型、實驗紀錄與指導教授問答]]

### 🏗️ DCS 數位控制系統 (TMS320C6000 DSP)
* **架構與管線**：[[DCS/TMS320C6000/核心架構與Pipeline|DSP 核心管線]]、[[DCS/TMS320C6000/中斷機制_Interrupt|中斷機制核心 (ISR/IST/ISTP)]]
* **記憶體與周邊**：[[DCS/TMS320C6000/Memory_Map與EMIF|記憶體映射與 EMIF]]、[[DCS/TMS320C6000/EDMA_背景搬運|EDMA 背景資料搬運]]、[[DCS/TMS320C6000/Timer計時器|Timer 定時器]]

---

## 📚 2. 學術領域與理論體系 (Knowledge Domains)

### 📐 物理設計與最佳化 (Physical Design & Optimization)
* [[Fundamentals/VLSI-Floorplanning-101|🔰 VLSI Floorplanning 零基礎入門]]
* [[ICCAD/EDA-Paradigm-Shift|EDA 範式轉移：從傳統啟發式到連續解析與 ML]]
* [[ICCAD/Algorithms/Wong-Liu-Algorithm|Wong-Liu 經典演算法]]、[[ICCAD/Algorithms/B-Star-Tree|B*-tree 拓撲理論]]
* [[ICCAD/Algorithms/Fixed-Outline-Floorplanning|Fixed-Outline 固定外框佈局理論]]
* [[Fundamentals/Optimization-Theory|最佳化理論、凸優化 (LP) 與組合爆炸難題]]

### 🌊 生成式 AI 與擴散模型 (Generative AI & Diffusion)
* [[AI/GenAI/GenAI-Overview|🎨 生成式 AI 知識樞紐]]
* [[AI/GenAI/Diffusion-Model|Diffusion Models 家族地圖 (U-Net vs. DiT)]]
* [[AI/GenAI/DDPM|DDPM 去噪擴散機率模型完整數學推導]]
* [[AI/GenAI/Markov-Chain-DDPM|馬可夫鏈與 DDPM 的交織關係]]
* [[AI/GenAI/Variational-Inference|變分推斷 (ELBO) 與資訊論]]
* [[AI/GenAI/Langevin-Dynamics|朗之萬動力學 (Score-Based SGM)]]
* [[AI/GenAI/UNet|U-Net 特徵提取與跳躍連接架構]]

### 🔷 深度學習與大語言模型 (Deep Learning & LLM)
* [[AI/Machine-Learning|🧠 機器學習核心概念]]、[[AI/Markov-Chain|馬可夫鏈 (SA 數學根基)]]
* [[AI/Transformer|Transformer 現代骨幹架構]]、[[AI/Attention|注意力機制 (Self-Attention)]]
* [[AI/LLM/Instruction-Tuning|指令微調 (Instruction Tuning)]]
* [[AI/LLM/PEFT-QLoRA|參數量化高效微調 (PEFT / QLoRA)]]
* [[AI/Data/Evaluation-Metrics|數據科學評估指標]]、[[AI/Data/Imbalance-Strategies|數據不平衡對策]]

---

## 🛠️ 3. 知識庫管理規範與日誌 (Maintenance & Meta)

本知識庫嚴格遵循 **LLM-Wiki 現代知識庫治理規範**：

| 規範 / 檔案 | 說明 |
| :--- | :--- |
| **[[index|🌐 全域索引 (Index)]]** | 扁平化 MOC (Map of Content) 導航樞紐，點擊直達各知識節點 |
| **[[log|📜 操作日誌 (Log)]]** | 完整記錄每一次知識攝取 (Ingest)、架構重構 (Refactor) 與實驗更新 |
| **[[Tool/LLM_Rules/SCHEMA|📜 Wiki 維護規範]]** | 定義「原子化筆記」、「目錄層級 $\le 3$ 層」、「YAML Frontmatter」標準 |
| **[[Tool/LLM_Rules/System_Instructions|🤖 LLM 指令集]]** | 規範 AI 助理在閱讀與編輯筆記時的行為邊界 |
| **[[Tool/AI-Collaboration-Discipline|🧭 AI 協作自律準則]]** | 保持批判性思考，杜絕盲目貼代碼，落實實證驗證 |

---

## 📁 倉庫目錄結構速覽 (Repository Tree)

```text
.
├── 📂 AI/                  # 機器學習、深度學習、大模型與生成式擴散模型 (Diffusion/DDPM)
├── 📂 DCS/                 # 數位控制系統 (DSP TMS320C6000 核心/中斷/EDMA)
├── 📂 Fundamentals/        # VLSI 實體設計入門、最佳化理論、詞彙表與實例解析
├── 📂 ICCAD/               # ICCAD 競賽規格書、論文摘要、Dashboard 與演算法論證
├── 📂 ICCAD_code/          # ICCAD 求解器深度教材 (1~12章)、參數手冊、研究日誌
│   └── 📂 8x_Research_Log/ # 按月份歸檔的演算法實驗日誌
├── 📂 Sliver Migration/    # 銀遷移物理模型與模擬筆記
├── 📂 Tool/                # LLM-Wiki 規範 (SCHEMA)、指令集與協作準則
├── 📂 張添烜 project/      # 教授研究室專案 (CIM 運算儲存矩陣、RCW 架構)
├── 🎨 Electronic_Pipeline.canvas  # SOTA 求解管線架構畫布
├── 📖 Electronic_Pipeline.md      # 畫布文字對照說明與生產旗標速查
├── 🌐 index.md             # 全域雙向連結索引
├── 📜 log.md               # 系統演進與實驗操作日誌
└── 📘 README.md            # 本導覽手冊
```

---
*最後重構同步日期：2026-08-26 | 依據 LLM-Wiki 規範維護*

