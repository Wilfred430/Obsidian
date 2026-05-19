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

**回到索引**：[[index|🌐 全域索引 >>]]
