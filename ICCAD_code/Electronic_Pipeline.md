---
title: Electro Pipeline 架構圖說明 (Canvas Legend)
tags: [ICCAD, Electro, Canvas, Architecture]
date: 2026-07-28
---

# Electro Pipeline 架構圖說明

> 這篇是 **[[Electronic_Pipeline.canvas|Electronic_Pipeline 畫布]]** 的文字版對照——畫布上每個方塊在程式碼裡對應到什麼，這裡逐一說明。畫布本身只放得下短標籤，細節都寫在這裡。
>
> **維護規則**：往後每次 `electro_optimized/` 的 pipeline 有結構性改動（新增/移除一個階段、新增一個「生產預設開啟」的機制），就同步更新畫布 + 這篇說明；只是調參數數值（不改架構形狀）不需要動畫布。

對應程式碼：`d:\ICCAD-2026-C\collaborate\electro_optimized\`（`analytical_place.py` / `legalize.py` / `soft_repair.py` / `electro_parallel.py` / `electro_optimizer.py`）。完整技術細節見 [[ICCAD_code/7_Electrostatic_Placer|7. 電靜力法擺放器]] 與 `collaborate/docs/electro_placer_deep_dive.pdf`。

---

## 方塊逐一說明

### 初始化來源（三選一，決定 Adam 從哪個座標開始下降）

| 方塊 | 程式碼位置 | 說明 |
|---|---|---|
| **Random init** | `analytical_place.py`，`ELECTRO_JACOBI_MODE=off` 時 | 完全隨機的初始座標，最陽春的起點 |
| **Jacobi 暖啟動** | `analytical_place.py`，**生產預設** `ELECTRO_JACOBI_MODE=replace` | 對 b2b 連線圖做 20 輪鄰居平均（Jacobi iteration），讓「有連線的方塊」在還沒開始優化前就自然靠近，比隨機起點省下很多梯度下降的彎路 |
| **Spectral / GiFt** | `analytical_place.py`，`jacobi_init=="spectral"` 分支，opt-in `ELECTRO_SPECTRAL=1` | Anchored quadratic placement：把「最小化連線距離平方和」的目標直接轉成線性系統 `(L_b2b+D_pin+D_pre)·p = b_pin+b_pre` 一步求解，是跟 Jacobi **不同來源**的確定性排列，可以當額外的 portfolio 候選 |

### Stage 1：`analytical_place()`

方塊裡列出的每一項都是 loss function 裡的一個力場項：
- `ov`（overlap repulsion）：兩方塊重疊時的排斥力，是合法性的主要驅動力
- `bb`/`lam_out`（boundary containment）：把方塊約束在畫布邊界內
- `wl`（wirelength）：HPWL 的可微近似
- `grp`（grouping cohesion，權重 **0.4**）：拉同一 cluster 的方塊靠近平均中心，是 V_grouping 的主要防線——這個權重原本寫死 1.0，2026-07-17 才發現從未被系統性掃過，調到 0.4 是零額外 runtime 的純改善
- `mib_shape`（MIB shape guidance，權重 **0.05**）：漸增權重引導同群組方塊長寬比趨同

### Stage 2：`legalize()`

三步驟，缺一不可：
1. **順序抽取**：讀 analytical_place 收斂後的真實座標，兩兩比較決定水平/垂直的相鄰關係（`Hadj`/`Vadj`）——忠實反映梯度下降已經找到的排列，不是憑空猜的
2. **依順序壓實**：給定上面的順序約束求實際座標。**貪婪**（`_compact()`，最長路徑演算法，只保證合法不保證最優）或 **LP**（`_compact_lp()`，`ELECTRO_LEGALIZE_LP=1`，**生產預設已開**，用 `scipy.optimize.linprog` 求總位移最小化）
3. **`_cleanup()` 保底**：不管前面算得怎樣，最後強制加安全間距 `_PUSH_GAP=1e-4` 徹底消除殘留重疊——這個常數曾經是 `1e-6`（跟官方違規門檻零安全餘裕），2026-07-19 修正過，見 [[ICCAD_code/8_Winning_Strategy_and_Roadmap|第 8 篇]] §8.33

### Stage 3：`soft_repair()`

- **Boundary snap**：沿牆掃描找空位貼齊（生產內建，一直都在）
- **Boundary wide-swap**（`ELECTRO_BOUNDARY_WIDESWAP=1`，生產預設已開）：放寬互換候選對象，疊代到收斂
- **Grouping push-past**（`ELECTRO_GROUPING_PUSHPAST=1`，生產預設已開）：找不到空位時把落單成員推向最大 cluster 元件

### place-compact（橘色方塊，本季最大單一突破）

把 Stage 3 出來的緊密佈局的中心點，當作**新的初始化**，重跑一輪短版 analytical_place（`ELECTRO_PLACE_COMPACT_ITERS=400`），再重新走一次 legalize+soft_repair，產生一個額外候選。**生產預設已開**（`ELECTRO_PLACE_COMPACT=1`）。重做的是「排列」這個維度——跟既有機制作用的「密度」維度正交，所以能真正疊加成淨改善（2.1230→1.9666）。

### 困難案例才加開的候選（紅色群組，opt-in）

- **adaptive spectral**（`ELECTRO_SPECTRAL_ADAPTIVE=1`）：只有 proxy 判斷「這個案例困難」（`best_600_score ≥ THRESH`，預設 1.5）才加跑一個 spectral 候選，把運算資源集中在真正需要的案例
- **escalated adaptive-seed**（`ELECTRO_ADAPTIVE_SEED=N`）：對觸發的難案例再加 N 個額外 jacobi seed
- 兩者疊加 + 池無關 proxy，`SEED=3` 是目前最佳已驗證組合：**1.7279**（約 2× runtime，opt-in，不是預設）

### Proxy Cost 排名（黃色方塊）

逐候選算一個近似 cost，用來快速排名選誰送出。**預設**用候選池平均正規化（`hpwl/池平均hpwl + area/池平均area`）；**opt-in 池無關版本**（`ELECTRO_PROXY_ABSAREA=1 ELECTRO_PROXY_ABSHPWL=1`）改用 `area/total_block_area`、`hpwl/√total_block_area`，解決了困難案例「候選池平均被拉高、壓縮真實差異」的問題，是目前最深一層的修正。

### Portfolio 選擇（紫色方塊，終點）

整條 pipeline 的核心設計哲學：**不是調出一組最佳參數，是同時生成多個候選，用 proxy cost 逐案挑最低者送出**。這個設計反覆被驗證：多樣性候選池本身就是比單點調參更強的槓桿。

---

## 目前的「生產預設」總覽（冷啟動不帶任何環境變數就會生效）

| 機制 | 狀態 |
|---|---|
| Jacobi 暖啟動 | ✅ 開（`replace`） |
| LP-based legalizer | ✅ 開 |
| place-compact | ✅ 開 |
| Boundary wide-swap | ✅ 開 |
| Grouping push-past | ✅ 開 |
| Spectral / GiFt | ⬜ opt-in |
| Adaptive spectral / escalated seed | ⬜ opt-in |
| 池無關 proxy（absarea/abshpwl） | ⬜ opt-in |

冷啟動安全預設分數：**1.9666**。疊加所有 opt-in 機制的最佳已驗證分數：**1.7279**（約 2× runtime）。完整 Pareto frontier 見 [[ICCAD_code/8_Winning_Strategy_and_Roadmap|第 8 篇]] §8.35 或 `collaborate/docs/electro_placer_deep_dive.pdf` §5。

---
**相關筆記**：[[ICCAD_code/7_Electrostatic_Placer|7. 電靜力法擺放器]] · [[ICCAD_code/8_Winning_Strategy_and_Roadmap|8. 奪冠策略總覽]] · [[Electronic_Pipeline.canvas|架構畫布]]
**回到**：[[ICCAD/ICCAD-Dashboard|ICCAD 儀表板]]
