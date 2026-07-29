---
title: Electro Pipeline 架構圖說明 (Canvas Legend)
tags: [ICCAD, Electro, Canvas, Architecture]
date: 2026-07-29
---

# Electro Pipeline 架構圖說明

> 這篇是 **[[Electronic_Pipeline.canvas|Electronic_Pipeline 畫布]]** 的文字版對照。
> **2026-07-29 重大更新**：主力路線已從我們自己的 `electro_optimized/` 換成
> **隊友的 slice_pack 路線**（`collaborate/electro_v5/`），架構本質不同，
> 畫布與本文件已整份改寫。舊路線的 spectral/adaptive-spectral 機制**不在**
> 現行主力中，相關記錄留在 [[ICCAD_code/8_Winning_Strategy_and_Roadmap|第 8 篇]] §8.35。

對應程式碼：`d:\ICCAD-2026-C\collaborate\electro_v5\`（`analytical_place.py` /
`legalize.py` / `slice_pack.py` / `soft_repair.py` / `electro_parallel.py` /
`electro_optimizer.py`）。

---

## 現在的成績（一眼看懂進度）

| 指標 | 數值 | 說明 |
|---|---|---|
| **真實 Total Score** | **1.1567** | 把 `R` 因子真的算進去（見下方「兩種分數」） |
| 中性 Total Score | 1.4127 | 只算品質 `Q·P`，忽略 runtime |
| 平均 runtime | 3.24s/case | WSL 真平行、seeds=16 |
| **快過官方中位數** | **95/100 案** | 本輪從 43 案推到 95 案 |
| Feasible | 100/100 | 全部合法 |

**對照 Alpha Top5**：0.879 / 0.955 / 1.020 / 1.028 / 1.100
→ 我們的 1.1567 已逼近第 5 名。

> [!important] **兩種分數不要搞混**
> - **中性 Total**：假設 `R=1`，只反映佈局品質（`Q·P`）。過去數週所有比較都用這個。
> - **真實 Total**：把 `R = max(0.7, (本案runtime ÷ 該案跨隊中位數)^0.3)` 算進去。
>   **這才是比賽真正的分數。** 兩者的排名可能完全相反——實測過品質最差的配置
>   反而真實分數最好，因為它快。

---

## 方塊逐一說明

### 初始化來源（三選一，決定 Adam 從哪個座標開始）

| 方塊 | 環境變數 | 說明 |
|---|---|---|
| Random init | `ELECTRO_JACOBI_MODE=off` | 隨機初始座標 |
| **Jacobi 暖啟動** | 生產預設 `replace` | 對 b2b 連線圖做 20 輪鄰居平均，讓有連線的方塊先自然靠近 |
| **ML 暖啟動** | `ELECTRO_ML_INIT=1` | 訓練好的 Transformer 預測每個方塊的中心點當起點。**多 seed 時才有用**（seed 0 用純預測，其餘在預測附近抖動） |

### Stage 1：`analytical_place()` — 連續梯度佈局

把方塊中心座標當可微參數，用 Adam 下降。loss 各項：`ov`（重疊斥力）、
`bb`/`lam_out`（邊界約束）、`wl`（線長）、`grp`（cluster 聚合，權重 0.4）、
`mib_shape`（MIB 形狀引導，權重 0.05）。

**`ELECTRO_ITERS=300`**（原預設 600）：實測品質幾乎無損（中性 1.4020→1.4127，
−0.8%）但真實分數改善 4.3%。**注意迭代數砍半只拿到 1.24× 而非理論 2×**，
因為瓶頸會轉移到 legalize/slice_pack。

### Stage 2a：`legalize()` — 梯度式合法化（保底路徑）

三步：① 從收斂幾何抽取相鄰順序 → ② 依順序壓實 → ③ `_cleanup()` 保底消除殘留重疊。

> [!success] ⚡ **`ELECTRO_FAST_CLEANUP=1` —— 本輪最大勝利**
> `_cleanup` 原本每輪都重建完整的 n×n 重疊矩陣（n=120 時被呼叫 5,315 次、
> 每次算 14,400 個配對）。改成**增量式**：第一輪完整掃描，之後只重新檢查
> 「上一輪真的被推動過的方塊 × 全部」。
>
> **正確性論證**：若 i 跟 j 都沒動過，相對位置不變 → 重疊狀態不可能改變。
> 成本從 O(n²) 降到 O(|動過的|·n)。
>
> **效果**：legalize 階段 **2.4× 加速**、整體真實 Total **−12.2%**、
> 快過中位數 43→95 案，而**品質逐位元完全不變**（中性 Total 與三項違規數皆相同）。

### Stage 2b：`slice_pack()` — 切割式打包（**主力機制**）

> [!abstract] **這是整條路線分數的最大來源（−25.7%），但一直不在舊版畫布上**

用一個矩形，依子樹面積比例**遞迴 guillotine 切割**（Otten 1982 切割式佈局）。
關鍵洞察：官方對軟方塊**只查面積 ±1% 容差，完全沒有長寬比限制**，而 86% 的
面積屬於這種自由軟方塊——所以「面積固定、長寬比自由」的方塊用面積比例切割，
**填充率理論上可達 100%**（實測 ground truth 是 0.966）。

它**不是**從零建構的搜尋器——切割順序繼承自 analytical_place 收斂後的座標，
所以線長品質是繼承來的，本質是「解析式佈局的排列 + 切割式佈局的形狀」。

任何一步無法保證合法就整套回傳 `None`，呼叫端沿用梯度式路徑，**不可能產出
不合法的解**。

### Stage 3：`soft_repair()` — 軟約束修復

boundary snap（沿牆掃描貼齊）、wide-swap、grouping push-past。

### place-compact（額外候選）

把已壓實佈局的中心點當**新的初始化**，再跑一輪短版 analytical_place，重新
legalize + repair 後當額外候選。**`ELECTRO_PLACE_COMPACT_TOPK=3`**：只對
prerank 前 3 名重拍，砍 75% 計算量而品質幾乎無損（最佳性價比旋鈕）。

### Proxy Cost 排名

`exp(2·V_rel)·(hpwl/ha + area/aa)`，錨點**跟候選池無關**（`aa` = 方塊總面積
÷ 0.966）。池無關很重要——用池平均當基準時，多加一個候選就會改變既有候選的
相對排名。

### Portfolio 選擇（終點）

整條 pipeline 的核心哲學：**不是調出一組最佳參數，而是同時生成多個候選
（不同初始化、不同合法化路徑、不同 aspect），用 proxy cost 逐案挑最低者送出。**

---

## 目前的旗標設定（要跑出上面成績用這組）

```bash
ELECTRO_SEEDS=16              # 多起點（必須在 WSL 才有真平行）
ELECTRO_PARALLEL=1
ELECTRO_ML_INIT=1
ELECTRO_ITERS=300
ELECTRO_PLACE_COMPACT=1
ELECTRO_PLACE_COMPACT_TOPK=3
ELECTRO_MIB_PORTFOLIO=1
ELECTRO_FAST_CLEANUP=1        # ⚡ 本輪新增，−12.2%
```

> [!danger] **`ELECTRO_PARALLEL=1` 只在 Linux/WSL 有效**
> Windows 沒有 `fork()`，會**靜默**退回循序執行。同一份設定實測
> WSL 3.24s vs Windows 50s+。**所有跟 runtime 有關的實驗都必須在 WSL 跑**，
> 否則數字沒有意義。

---

## 已測試但**不採用**的機制（避免重複投入）

| 機制 | 結果 | 原因 |
|---|---|---|
| `SLICE_WALLS=0` | 真實 1.1367（小贏 1.7%） | **不穩健**：中位數上升 1.5× 時結論反轉，而品質確定損失 5.7% |
| `SLICE_BUDGET=1200` | 真實 1.1516 | **被兩端支配**，任何中位數假設下都不是最佳 |
| `_cut_options` 面積提早否決 | 逐位元等價但 **1.01×** | 空結果不是面積不足造成的，是剛性尺寸/preplaced 卡住 |
| 切點偏好（cluster / boundary） | 全部淨負 | 見 §8.37，`_order` 的軟性偏置已在良好局部最佳 |

---

## 下一步該往哪走

> [!warning] **runtime 優化已接近自然停止點**
> `R` 有 **0.7 的地板**。我們現在 95/100 案已快過中位數，**再快的邊際價值急遽
> 下降**——這也是為什麼 `SLICE_WALLS=0` 那 1.7% 不值得冒品質風險。
> **接下來的槓桿重新回到品質（`Q·P`）**，但必須在**不增加 runtime** 的前提下。

剩餘的品質空間：`V_grouping=151`、`V_mib=94`、`V_boundary=140`。

---
**相關筆記**：[[ICCAD_code/7_Electrostatic_Placer|7. 電靜力法擺放器]] ·
[[ICCAD_code/8_Winning_Strategy_and_Roadmap|8. 奪冠策略總覽]]（§8.36-§8.39 是本輪逐日記錄）·
[[Electronic_Pipeline.canvas|架構畫布]]
**回到**：[[ICCAD/ICCAD-Dashboard|ICCAD 儀表板]]
