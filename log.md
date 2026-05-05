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
    - [[ICCAD/FloorSet-Summary|FloorSet 快速複習 (口訣版)]]
    - [[ICCAD/FloorSet-Detailed|FloorSet 規格詳解 (完整版)]]
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

---
**回到索引**：[[index|🌐 全域索引 >>]]
