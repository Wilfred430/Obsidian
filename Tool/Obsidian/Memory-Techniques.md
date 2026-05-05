# Obsidian 記憶增強與知識串聯技術規範

這份筆記記錄了提升大腦複習效率的四種核心形式，作為本筆記庫的排版準則。

## 1. 功能性分類 (Callouts)
利用色彩編碼縮短定位時間。
- **[!abstract] 核心定義**：用於演算法目標、數學公式。
- **[!danger] 實作地雷**：記錄硬約束、容易導致 Fail 的點。
- **[!example] 跨領域發想**：連結不同學科的概念。

## 2. 熱點記憶法 (Color Coding)
- **紅色**：關鍵限制 (NP-complete, Hard Constraints)。
- **藍色/綠色**：物理變數與公式 ($HPWL, W, H$)。
- **紫色**：技術術語與模型名稱 (`GNN`, `B*-tree`)。

## 3. 邏輯視覺化 (Mermaid)
使用 Mermaid 代碼塊呈現 CoT (Chain of Thought)，取代靜態截圖，便於理解結構。

## 4. 原子化連結 (Atomic Links)
- 概念獨立成篇。
- 使用 `up::`, `related::` 等屬性建立神經元網絡。

## 5. YAML 屬性規範
```yaml
---
Field: 學科領域
Type: 筆記類型 (Algorithm / Theory / Practice)
Confidence: 1-5 (掌握度)
Cross-Domain: [[相關領域]]
---
```
