---
title: Electro Pipeline 架構圖說明 (Canvas Legend)
tags: [ICCAD, Electro, Canvas, Architecture]
date: 2026-08-04
---

# Electro Pipeline 架構圖說明

> 這篇是 **[[Electronic_Pipeline.canvas|Electronic_Pipeline 畫布]]** 的文字版對照。
> **2026-08-04 更新**：主力路線已合併成 `electro_v19/`（凍結基準）——這是
> 隊友 slice_pack 路線（前身 `electro_v5`）跟我們自己 `electro_optimized/`
> 路線（MIB anchor 機制血緣）以及 LP 位移候選（v14）的**融合版**，不再是
> 單一路線。`electro_v20/` 是 `electro_v19/` 的逐位元相同副本，專門拿來做
> opt-in 的新機制實驗（對偶上升、ADMM…），不影響 `electro_v19` 這個凍結基準。

對應程式碼：`d:\ICCAD-2026-C\collaborate\electro_v19\`（`analytical_place.py` /
`dirichlet_init.py` / `legalize.py` / `lp_legalize.py` / `slice_pack.py` /
`cluster_virtualize.py` / `soft_repair.py` / `electro_parallel.py` /
`electro_optimizer.py`）。`electro_v20\` 是同一份程式碼的副本，見下方「研究
方向紀錄」。

---

## 現在的成績（一眼看懂進度）

全 100 案驗證，`electro_v19` 生產預設（`reports/v19_bndonly.xlsx`）：

| 指標 | 數值 | 說明 |
|---|---|---|
| 中性 Total Score | **1.3776** | `Q·P`，忽略 runtime，過去所有比較的基準值 |
| 平均 runtime | 2.044s/case | native/WSL 皆可（`ELECTRO_SEEDS=4`，比舊版 8/16 更省） |
| 快過官方中位數 | 99/100 案 | |
| Feasible | 100/100 | 全部合法，硬約束零違規 |
| V_grouping / V_mib / V_boundary | 143 / 51 / 174 | 見下方各機制如何壓這三個數字 |

> [!important] **這個工具（`ml.case_report_electro`）沒有算 REAL（含 RT）分數欄位**
> 只有 Neutral Total 跟 runtime/faster-than-median 這兩組數字。想knowRT
> 影響時，看 runtime 平均值跟「快過中位數」的案數，不要只看 Neutral 一項
> （舊版 `iccad2026_evaluate.py --evaluate` 才有 REAL Total 欄位，兩個工具
> 不能混用比較）。

**目前最強已知配置**（尚未寫進生產預設，見下方「研究方向紀錄」表格）：
`ELECTRO_DUAL_ASCENT_BND=1 ELECTRO_DA_K=40` → Neutral **1.3691**（-0.62%），
Vbnd 174→**140**，跑得更快（1.84s/案）。

---

## 方塊逐一說明

### 初始化來源（依優先序退回：Dirichlet → ML → Random）

不是三選一，是一條**優先序退回鏈**——後面的只在前面關閉或失敗時才會用到：

| 方塊 | 環境變數 | 說明 |
|---|---|---|
| **Dirichlet 調和延拓初始化** | `ELECTRO_DIRICHLET_INIT=1`（生產預設） | 解 graph Laplacian 的**封閉解**（BADGE/DPlanner 式），只開 boundary 訊號（`bnd_weight=2.0`，`grp_weight=mib_weight=0`——grp/mib 訊號實測都比不開還差）。全 100 案 Neutral 1.4018→1.3776（-1.73%）。**取代了舊版 Jacobi 暖啟動**（20 輪鄰居平均已經不在現行程式碼中） |
| ML 暖啟動 | `ELECTRO_ML_INIT=1`（生產預設） | Dirichlet 停用或失敗時的退回。訓練好的 Transformer 預測每個方塊的中心點當起點。**多 seed 時才有用**（seed 0 用純預測，其餘在預測附近抖動） |
| Random init | 兩者都關閉/失敗時的最終備援 | 隨機初始座標 |

### Stage 1：`analytical_place()` — 連續梯度佈局

把方塊中心座標當可微參數，用 Adam 下降。loss 各項：`ov`（重疊斥力）、
`bb`/`lam_out`（邊界約束）、`wl`（線長）、`grp`（cluster 聚合）、
`mib_shape`（MIB 形狀引導）。`ELECTRO_ITERS=300`（原預設 600，品質幾乎無損）。

> [!success] **MIB_ANCHOR_SNAP + MIB_ANCHOR（生產預設，從 `electro_optimized/` 移植回來）**
> 混合 MIB 群組（含 fixed/preplaced 錨點 + 軟成員）裡，錨點的 `(w,h)` 是釘死
> 的，軟成員形狀只要不同就一定計違規。**兩段式做法**：
> 1. `MIB_ANCHOR_SNAP`：訓練全程用漸增權重的 L2 loss，引導軟成員的
>    log-aspect 收斂向錨點的 `log(tw/th)`。
> 2. `MIB_ANCHOR`：**在輸出階段**（不是在 loss 定義的地方）直接把軟成員的
>    `(w,h)` 覆寫成錨點的精確尺寸，面積仍精確命中目標（同群組共用
>    `area_sg`），保留中心點以最小化位移，跳過驗證失敗的群組（嚴格加法式）。
>
> **關鍵教訓**：2026-07-29 試過只在輸出階段硬覆寫（不做訓練中引導）
> ——結果 **+0.8% 退步**，因為那是暴力跳變，製造大量重疊丟給 legalize
> 收拾，優化器全程不知道這個目標。也試過在 `shapes()` 一開始就鎖死長寬比
> ——單案 area_gap 暴增到 183.9%，慘敗。**兩段都要**才是成功與失敗的分界。
>
> 效果：Vmib 從舊版（`electro_optimized/` 沒有這個機制時）94-99 壓到現在
> 的 ~50。

### Stage 2a：`legalize()` — 梯度式合法化（保底路徑）

三步：① 從收斂幾何抽取相鄰順序 → ② 依順序壓實 → ③ `_cleanup()` 保底消除殘留重疊。

> [!success] ⚡ **`ELECTRO_FAST_CLEANUP=1`**
> `_cleanup` 改成增量式：第一輪完整掃描，之後只重新檢查「上一輪真的被推動
> 過的方塊 × 全部」，成本從 O(n²) 降到 O(|動過的|·n)。legalize 階段
> **2.4× 加速**，品質逐位元不變。

### Stage 2b：`slice_pack()` — 切割式打包（**主力機制之一**）

用一個矩形，依子樹面積比例**遞迴 guillotine 切割**（Otten 1982）。官方對
軟方塊只查面積 ±1% 容差、無長寬比限制，切割式因此填充率可近 100%。切割
順序繼承自 `analytical_place` 收斂後的座標，任何一步無法保證合法就整套
回傳 `None`，呼叫端沿用梯度式路徑——**不可能產出不合法的解**。

> [!success] **`ELECTRO_SLICE_ALIGN_PORTFOLIO=1`（生產預設）**
> `slice_pack` 有兩種切割方向（沿哪一軸優先切），舊版只能挑一種固定用；
> 現在 `return_pair=True` 讓兩種方向都各自產生一份候選丟進同一個池，交給
> 後面的 proxy cost 排名逐案挑，不用預先猜哪個方向比較好。

### LP 位移最小化候選（`ELECTRO_LP_DISPLACEMENT_PORTFOLIO=1`，生產預設）

對每個種子，先跑一般的梯度式 legalize 路徑拿到候選，再對排名前段
（`TOPK=1`）的候選額外跑一次 **LP（線性規劃）式最小位移 legalize**
（`lp_legalize.py`），把結果當**獨立的額外候選**丟進同一個排名池——嚴格
加法式，LP 求解失敗就什麼都不做，永遠不會比不開這個機制差。這是「v14 LP
all sizes / 4 seeds」這個配置的沿用，是這個檔案存在前已知最佳可重現的
全 100 案配置。

### Stage 3：`soft_repair()` — 軟約束修復

boundary snap（沿牆掃描貼齊）+ wide-swap（預設開）、grouping push-past
（預設開）。

### place-compact（額外候選）

把已壓實佈局的中心點當**新的初始化**，再跑一輪短版 `analytical_place`，
重新 legalize + repair 後當額外候選。`ELECTRO_PLACE_COMPACT_TOPK=3`：只對
prerank 前 3 名重拍，砍 75% 計算量而品質幾乎無損。

### Proxy Cost 排名

`exp(2·V_rel)·(hpwl/ha + area/aa)`，錨點**跟候選池無關**（`ha`=seed 0 的
梯度式候選 HPWL，`aa`=方塊總面積 ÷ 0.966）。池無關很重要——用池平均當
基準時，多加一個候選就會改變既有候選的相對排名。

### Portfolio 選擇（終點）

整條 pipeline 的核心哲學：**不是調出一組最佳參數，而是同時生成多個候選
（不同初始化、不同合法化路徑、不同切割方向、不同 aspect），用 proxy cost
逐案挑最低者送出。**

---

## 目前的旗標設定（生產預設，寫在 `electro_optimizer.py` 的 `setdefault`）

```bash
ELECTRO_CLAMP=1
ELECTRO_NONNEG=1
ELECTRO_SEEDS=4                    # 舊版是 8/16，現在更省而分數不掉
ELECTRO_PARALLEL=1
ELECTRO_ML_INIT=1
ELECTRO_ITERS=300
ELECTRO_PLACE_COMPACT=1
ELECTRO_PLACE_COMPACT_TOPK=3
ELECTRO_MIB_PORTFOLIO=1
ELECTRO_FAST_CLEANUP=1
ELECTRO_SLICE_ALIGN_PORTFOLIO=1     # 新增
ELECTRO_MIB_ANCHOR_SNAP=1           # 新增（v11 血緣移植）
ELECTRO_MIB_ANCHOR=1                # 新增（v11 血緣移植）
ELECTRO_DIRICHLET_INIT=1            # 新增，取代 Jacobi
ELECTRO_DIRICHLET_GRP_WEIGHT=0
ELECTRO_DIRICHLET_MIB_WEIGHT=0
ELECTRO_DIRICHLET_BND_WEIGHT=2.0
ELECTRO_LP_DISPLACEMENT_PORTFOLIO=1 # 新增（electro_parallel.py）
ELECTRO_LP_DISPLACEMENT_SEEDS=4
ELECTRO_LP_DISPLACEMENT_MIN_BLOCKS=0
ELECTRO_LP_DISPLACEMENT_TOPK=1
```

> [!danger] **`ELECTRO_PARALLEL=1` 只在 Linux/WSL 有真平行**
> Windows 沒有 `fork()`，會**靜默**退回循序執行。所有跟 runtime 有關的
> 實驗都必須在 WSL 跑，否則數字沒有意義。

---

## 已測試但**不採用**的機制（避免重複投入）

### 小型 slice_pack 調參（沿用自 electro_v5 世代）

| 機制 | 結果 | 原因 |
|---|---|---|
| `SLICE_WALLS=0` | 真實 1.1367（小贏 1.7%） | **不穩健**：中位數上升 1.5× 時結論反轉，品質確定損失 5.7% |
| `SLICE_BUDGET=1200` | 真實 1.1516 | **被兩端支配**，任何中位數假設下都不是最佳 |
| `_cut_options` 面積提早否決 | 逐位元等價但 **1.01×** | 空結果不是面積不足造成的，是剛性尺寸/preplaced 卡住 |
| 切點偏好（cluster / boundary） | 全部淨負 | `_order` 的軟性偏置已在良好局部最佳 |

### 研究方向紀錄（2026-08-03～08-04）：3 個負面結果 + 1 個驗證正面但未預設

這一輪依序嘗試三個更大方向（RT 改善 → Per-RMAP 可行性追尋 → 完整 ADMM
變數分裂），全部收斂到乾淨的負面結論，代表 `electro_v19` 目前的配置大概
就是這條技術路線（梯度式 + 切割式 + portfolio 排名）目前的天花板：

| 方向 | 結果 | 關鍵數字 |
|---|---|---|
| **L-BFGS 打磨階段** | 負面 | `frac≥0.7` 凍結 schedule、Adam→L-BFGS 收尾。3 組切換點全部更慢（3.35-6.54×）且品質更差。`ELECTRO_LBFGS` 預設關閉 |
| **Per-RMAP 可行性追尋** | 負面 | 拿現有 repair 函式當投影運算子＋擾動＋重設外迴圈。20 案平均 V_rel 變差 28.2%、慢 33 倍，15 輪上限內全部沒收斂到零違規。獨立標準腳本，未整合進生產管線 |
| **ADMM 邊界一致性** | 負面 | `electro_v21/`，把 `lam_bnd*bnd` 換成有號殘差增廣拉格朗日二次拉力。20 案 Neutral 變差 +8.1%，**Vgrp/Vmib/Vbnd 全部變差**（連沒被動到的 grp/mib 都變差——「共用梯度預算耦合」再次出現，只是換了數學形式）。`ELECTRO_ADMM_BND` 預設關閉 |
| **對照：對偶上升 boundary** | **驗證正面，但尚未預設** | `ELECTRO_DUAL_ASCENT_BND=1 ELECTRO_DA_K=40`（跟 ADMM 用同一個 bnd loss 槽位，互斥）。全 100 案 Neutral 1.3776→**1.3691**（-0.62%），Vbnd 174→**140**，跑得更快（2.044s→1.84s/案）。**尚未寫進 `electro_optimizer.py` 的 `setdefault`**——要手動加旗標才能重現 |

> [!info] 為什麼 ADMM/L-BFGS/Per-RMAP 這三個更「先進」的機制反而都輸給
> 簡單的對偶上升（dual ascent）？
> 這一輪反覆驗證出同一個結構性發現：**只要兩個軟約束修復共用同一個底層
> 計算基質（同一組梯度預算、同一個圖度數正規化、同一個 bbox 相對參考
> 框架、同一個遞迴切割拓樸），修好一個就會傷到另一個。** ADMM 跟 L-BFGS
> 想解決的正是這個耦合，但它們都還是在**同一個** `analytical_place()`
> 主迴圈裡跟 wl/ov/grp 共用同一份梯度預算，只是換了懲罰項的數學形式
> （二次拉力、L-BFGS 收尾）而不是真的把子問題拆開成獨立變數——這也是為什麼
> 三個方向的失敗模式高度相似（grp/mib/bnd 一起變差，不是只有目標項變差）。

---

## 下一步該往哪走

> [!warning] **這條技術路線（梯度式 + 切割式 + portfolio）大概已經到頂**
> 三個獨立方向都測過、都收斂到負面。剩餘的品質空間：`V_grouping=143`、
> `V_mib=51`、`V_boundary=174`（生產預設）或 `140`（對偶上升，尚未預設）。
> 如果還要繼續推進，大概需要跳出「同一個梯度迴圈裡加懲罰項」這個框架
> ——例如真正的變數分裂（獨立的子問題、獨立的求解器，而不是換一種
> 懲罰項包裝），或是換一種完全不同的搜尋範式。

---
**相關筆記**：[[ICCAD_code/7_Electrostatic_Placer|7. 電靜力法擺放器]] ·
[[ICCAD_code/8_Winning_Strategy_and_Roadmap|8. 奪冠策略總覽]] ·
[[Electronic_Pipeline.canvas|架構畫布]]
**回到**：[[ICCAD/ICCAD-Dashboard|ICCAD 儀表板]]
