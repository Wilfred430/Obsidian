# 📜 LLM-Wiki 操作日誌 (Log)

> [!info] **說明**：這是知識庫的演進時間軸，記錄了每一次知識攝取與重構。

## [2026-04-28] Refactor | 系統升級為 LLM-Wiki 模式
- **Action**: 根據 `@llm-wiki.md` 藍圖，建立全域索引與日誌系統。
- **Created**: `index.md`, `log.md`.
- **Integrated**: 整合 ICCAD 2026 Problem C 相關知識節點。
- **Memory**: 儲存了 Obsidian YAML Frontmatter 格式規範。

## [2026-04-28] Ingest | ICCAD 2026 FloorSet 競賽資料
- **Source**: `@Paper/ICCAD_C_2026.pdf` (規格書)。
- **Output**: 
    - [[ICCAD/Problem/FloorSet-Summary|FloorSet 快速複習 (口訣版)]]
    - [[ICCAD/Problem/FloorSet-Detailed|FloorSet 規格詳解 (完整版)]]
    - [[ICCAD/EDA-Paradigm-Shift|EDA 範式轉移理論]]
- **Update**: 在 `Problem Overview.md` 中建立雙向連結。

## [2026-04-28] Refactor | 知識庫清理與格式修正
- **Action**: 修正 `ICCAD/FloorSet-Detailed.md` 的 YAML 格式位移問題。
- **Action**: 清理根目錄冗餘檔案，刪除空檔案 `FloorSet 挑戰賽.md`。
- **歸位**: 將 `llm-wiki.md` 與 `Zotero Template.md` 移至 `Tool & Essay/` 目錄。
- **恢復**: 恢復遺失的 `ICCAD/Problem Overview.md`。

## [2026-04-28] Refactor | ICCAD 知識原子化拆分
- **Action**: 刪除 `Problem Overview.md`，減少導航層級。
- **Split**: 將原內容拆分為獨立筆記：
    - [[ICCAD/Problem-A-Bug-Classification|Problem A: RTL Bug Classification]]
    - [[ICCAD/Problem-D-Timing-Fixing|Problem D: Timing Fixing]]
- **Update**: `index.md` 現在直接連結至各競賽問題，達成「直達核心」效果。

## [2026-04-28] Refactor | 樣式優化與引用清理
- **Action**: 移除所有筆記中的 `[cite_start]` 與 `[cite: ...]` 等干擾字樣。
- **Style**: 將 Problem A 與 Problem D 升級為 Callout 視覺化樣式。
- **Status**: 知識庫目前具備高度的一致性與視覺舒適度。

## [2026-05-05] Update | ICCAD 2026 V9 規格同步與規則重構
- **Feature**: 建立 `Tool/LLM_Rules/` 規則夾。
- **Update**: 同步 ICCAD Problem C V9 變動（Fixed-shape/Preplaced 轉硬約束、中心點 HPWL）。
- **Maintenance**: 將 `SCHEMA.md` 移入 `Tool/LLM_Rules/`，修正全域路徑連結。
- **Rule**: 儲存 `log.md` 與 `README.md` 的連動操作記憶。

## [2026-05-05] Ingest | Wong-Liu Floorplanning Algorithm (1986)
- **Source**: `@ICCAD/Paper/A New Algorithm for Floorplan Design.pdf` (經典論文)。
- **Output**: [[ICCAD/Algorithms/Wong-Liu-Algorithm|Wong-Liu Algorithm 詳解筆記]]。
- **Insight**: 識別出 1986 年論文中的 HPWL 計算方式與 ICCAD 2026 V9 規格的高度一致性，為後續優化提供理論基礎。

## [2026-05-05] Ingest | Fixed-Outline Floorplanning: Enabling Hierarchical Design
- **Source**: `@ICCAD/Paper/Fixed-Outline Floorplanning- Enabling Hierarchical Design.pdf` (2003)。
- **Output**: [[ICCAD/Algorithms/Fixed-Outline-Floorplanning|固定輪廓佈局核心概念]]。
- **Key Insight**: 釐清了 Whitespace 在 Fixed-die 流程中作為「約束」而非「優化目標」的本質轉換，並確立了 Penalty-based Cost Function 的重要性。

## [2026-05-11] Refactor | 簡化 DCS 檔案命名
- **Action**: 移除 `DCS/TMS320C6000/` 資料夾下所有檔案的 `TMS320C6000_` 前綴，以縮短檔名並提升閱讀效率。
- **Updated Files**:
    - `中斷機制_Interrupt.md`
    - `核心架構與Pipeline.md`
    - `EDMA_背景搬運.md`
    - `Memory_Map與EMIF.md`
    - `Timer計時器.md`
- **Link Maintenance**: 同步更新所有筆記內的雙向連結，確保 Wiki 結構完整性。

---
---
## [2026-05-13] Ingest | DSP 中斷機制原子化拆分
- **Output**: [[DCS/TMS320C6000/中斷處理機制_ISR_IST_ISTP_ISFP|中斷處理機制核心 (ISR/IST/ISTP/ISFP)]]
- **Update**: 在 [[DCS/TMS320C6000/中斷機制_Interrupt|中斷機制主頁面]] 建立雙向連結。
- **Action**: 針對 ISR、IST、ISTP 與 ISFP 建立高質量的概念隱喻與運作流程詳解，強化 DCS 領域的知識深度。

---
## [2026-05-19] Refactor | 全面庫整理與知識原子化
- **Metadata**: 修正全庫 10+ 份筆記的 YAML Frontmatter 格式，確保從第一行開始。
- **Atomization**: 將 Zotero 中的 RCW-CIM 研究精煉為獨立知識節點：
    - [[張添烜 project/CIM/RCW-Mechanisms|RCW-CIM 架構與隱藏延遲]]
    - [[張添烜 project/CIM/Nonlinear-Operator-Fusion|非線性運算算子融合]]
- **Linking**: 完成「AI/LLM」與「CIM 加速器」專案間的雙向連結，特別是在 [[張添烜 project/CIM/Latency & Throughput|Latency 分析]] 中導入硬體解決方案。
- **Index**: 重構 `index.md`，新增 AI 核心知識與 CIM 專案章節，提升導航效率。
- **Cleanup**: 修正 `PEFT-QLoRA.md` 與 `Instruction-Tuning.md` 的標題冗餘與格式問題。

---
## [2026-05-19] Ingest | B*-tree Floorplanning 演算法剖析
- **Output**: [[ICCAD/Algorithms/B-Star-Tree|B*-tree Floorplanning 技術筆記]]
- **Content**: 整合拓樸、幾何、Packing 流程與模擬退火操作的完整教學手冊與 Mermaid 概念圖。
- **Update**: 於 [[ICCAD/ICCAD-Dashboard|ICCAD 儀表板]] 新增演算法導航連結。

## [2026-06-17] Ingest | ICCAD 2026 SA Optimizer 程式碼深度解析
- **Source**: `ICCAD_code/` (Python Wrapper + C++ SA Engine)
- **Output**: 產出四份結構化與圖表化 (Mermaid) 筆記：
    - [[ICCAD_code/1_Data_Loader_and_Wrapper|1_Data_Loader_and_Wrapper]] (資料介接與 ML 預熱)
    - [[ICCAD_code/2_SA_Optimizer_Engine|2_SA_Optimizer_Engine]] (核心退火算法與 B*-Tree)
    - [[ICCAD_code/3_Cost_Function_and_Penalty|3_Cost_Function_and_Penalty]] (成本計算與數學約束)
    - [[ICCAD_code/4_Packing_and_Evaluation|4_Packing_and_Evaluation]] (拓撲打包與座標推算)
- **Insight**: 成功將複雜的 C++ B*-Tree 操作與 Simulated Annealing 機制轉化為 PM 視角的架構圖，並透過 LaTeX 提取了 Cost Function 與動態 Penalty 的核心公式，為專題報告提供強大火力支援。

---
## [2026-07-01] Refactor + Ingest | ICCAD 實作筆記同步至 Beta 現況 + 生成式模型記錄
- **Source**: `collaborate/` repo 現況（Claude Code 讀取 `src/packer.cpp`、`ml/*.py`、`WINNING_STRATEGY.md`、CLAUDE.md 等）。
- **Action（更新既有筆記,補上 6 週的落差）**：
    - [[ICCAD_code/2_SA_Optimizer_Engine|2_SA_Optimizer_Engine]]：補上 M5 (MibSync)、M7 (FixGrouping，含雙向修正)、`always_accept` 不變量說明。
    - [[ICCAD_code/3_Cost_Function_and_Penalty|3_Cost_Function_and_Penalty]]：新增 3.4 節，釐清 SA 內部 cost 與**官方 contest cost 公式**（含 $e^n$ 總分加權）的差異——原筆記只講前者，容易被誤讀成兩者相同。
    - [[ICCAD_code/4_Packing_and_Evaluation|4_Packing_and_Evaluation]]：新增 4.4 節，補上打包後的四道確定性修復通道（`compact_left_down`/`bbox_balance_pass`/`holes_fill_pass`/`grouping_repair`/`boundary_repair`），原筆記完全沒提及。
- **Output（新增四篇原子筆記）**：
    - [[ICCAD_code/5_ML_Coordinate_Regression|5_ML_Coordinate_Regression]]：座標回歸模型架構 + **Mode Collapse 診斷**（多峰解被 MSE 平均成不合法解）。
    - [[ICCAD_code/6_ML_Generative_BTree|6_ML_Generative_BTree]]：`tree_sol` schema 解密（比對 `packer.cpp` 確認 direction bit 語義）、三個 Pointer Network 的生成式模型架構、150k 筆 GPU 訓練結果（`val_ptr_acc` 0.874）。
    - [[ICCAD_code/7_Electrostatic_Placer|7_Electrostatic_Placer]]：電靜力法（ePlace 典範），目前分數最佳（Total 2.966）。
    - [[ICCAD_code/8_Winning_Strategy_and_Roadmap|8_Winning_Strategy_and_Roadmap]]：三個關鍵診斷（$e^n$ 加權/搜尋空間/Mode Collapse）+ 四階段生成式管線 + 現況時間軸。
- **Cleanup**：刪除 `ICCAD_code/` 下過時的程式碼副本（`include/`、`ml/*.py`、`src/*.o`、`my_optimizer*.py` 等，含當初複製時一併帶進來的 `*Zone.Identifier` 垃圾檔）——這些檔案已與 repo 現況脫節超過一個月。**往後原則**：筆記只解說架構,不再複製程式碼進 vault,程式碼永遠只有 git repo 一份。
- **Update**: [[ICCAD/ICCAD-Dashboard|ICCAD Dashboard]] 新增「現況」callout 與「實作深潛」章節，連到全部 8 篇筆記。
- **Insight**: `tree_sol` 這個大會資料集裡的欄位被舊版 loader 標記 unused 直接丟棄，解密後發現是完整的 B*-tree 邊表——這是本次同步中最關鍵的一個發現，直接催生了整個生成式拓樸模型路線。

---
## [2026-07-01] Refactor | 全庫歸納整理：消滅散落根目錄檔與斷鏈
- **Action（清理舊 code 副本）**：刪除 `ICCAD_code/` 下過時的程式碼與文件副本（`include/`、`ml/`、`src/`、`my_optimizer*.py`、`README_iccad.md`、`SA_TUNING_GUIDE.md`、`START_HERE.md`、`SUBMISSION.md`），這些原是給 Gemini CLI 寫筆記用的暫存,現已無用。`ICCAD_code/` 現在只保留 8 篇原子概念筆記。
- **Action（歸納根目錄散落檔案,落實「所有檔案都在 folder 下」）**：
    - 新建 `Fundamentals/` 資料夾收納通用 CS/理論概念。
    - `Big-endian.md`（空）→ `Fundamentals/Big-endian.md`,補寫大小端完整知識,設 `Little-endian` alias（修復 DCS 的 `[[Little-endian]]` 斷鏈）。
    - `NVMe_SSD.md`（空）→ `Fundamentals/NVMe_SSD.md`,補寫 NVMe/SSD 完整知識。
    - `Markov_Chain.md`（空,根目錄）→ 刪除,因與已填實的 [[AI/Markov-Chain|AI/Markov-Chain.md]] 重複。
    - 根目錄現在只剩 `index.md` / `log.md` / `README.md` 三個基礎設施檔。
- **Output（補寫斷鏈目標,消滅「點進去空白」）**：
    - [[Fundamentals/Optimization-Theory|Optimization-Theory]]：組合最佳化、NP-hard、Metaheuristics、局部最佳陷阱——ICCAD SA/ML 路線的理論根基。
    - [[AI/Machine-Learning|Machine-Learning]]：ML 樞紐頁 (MOC),串起判別式 vs 生成式、三大學習範式與全庫 AI 筆記。
- **Fix（斷鏈修復）**：
    - `FloorSet-Detailed.md` 的 `[[Machine Learning]]` / `[[Optimization Theory]]` → 指向新筆記。
    - `EDA-Paradigm-Shift.md` 修正壞掉的四欄表格,移除 `[[FloorSet 挑戰賽]]` 斷鏈。
    - `Outline-Characteristics.md` 的 `[[ICCAD/Problem Overview]]`（已刪除的舊筆記）→ 重新指向 [[ICCAD/ICCAD-Dashboard|Dashboard]]。
- **Update**: `index.md` 新增「基礎概念 (Fundamentals)」章節,並在 AI 領域補上 ML 樞紐頁與馬可夫鏈連結。
- **Insight**: 全庫斷鏈盤點後,ICCAD + Fundamentals 範圍內已無「點了空白」的死連結。往後維護原則:筆記只解說架構,程式碼永遠只留在 `collaborate/` git repo,不再複製進 vault。

---
## [2026-07-01] Ingest | Transformer 架構全解（回應 user 對 Transformer 的興趣）
- **Source**: 使用者對 Transformer 表達興趣；結合《Attention Is All You Need》標準架構與本庫兩個 ICCAD 模型的實際原始碼（`ml/model.py`、`ml/model_tree.py`）。
- **Output**: [[AI/Transformer|Transformer 架構全解]]——涵蓋 Positional Encoding、Encoder/Decoder 堆疊、Masked Self-Attention、Cross-Attention、Add&Norm、FFN，以及 Encoder-only / Decoder-only / Encoder-Decoder 三大家族對照表。
- **Insight**: 兩個 ICCAD 模型剛好各代表一種家族——[[ICCAD_code/5_ML_Coordinate_Regression|`model.py`]] 是 Encoder-only（同 BERT），[[ICCAD_code/6_ML_Generative_BTree|`model_tree.py`]] 是 Encoder-Decoder 變體（把輸出層從詞彙表換成 Pointer Network）。用真實跑得動的程式碼具體對照教科書架構，比純理論筆記更扎實。
- **Linking**: 雙向連結 [[AI/Attention|Attention]]（细節互補，不重複 QKV 公式）、[[AI/Machine-Learning|Machine-Learning 樞紐頁]]、`ICCAD_code/5` 與 `6` 的架構段落，並更新 `index.md`。

**回到索引**：[[index|🌐 全域索引 >>]]
