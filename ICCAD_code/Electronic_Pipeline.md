---
title: Electro Pipeline 架構圖說明 (Canvas Legend)
tags: [ICCAD, Electro, Canvas, Architecture]
date: 2026-08-04
---

# Electro Pipeline 架構圖說明

> 這篇是 **[[Electronic_Pipeline.canvas|Electronic_Pipeline 畫布]]** 的文字版對照。
> **2026-08-11 更新**：主力路線升級為 `electro_v20/`——不是另一條血緣，是
> `electro_v19/` 的**安全超集**：新機制（對偶上升、reshape portfolio）全部
> 關閉時，輸出跟 v19 逐位元相同（已用 `_dump_electro_geometry.py` 驗證）。
> 這一輪是「RT/預設值調校 campaign」，不是新演算法：重新量測既有設定 +
> 找回沒進生產的機制，見下方「研究方向紀錄」。`electro_v19/` 保留作凍結
> 對照基準，之後開發都在 `electro_v20/` 進行。

> **2026-08-15 更新**：主力路線為 `electro_v22/`。這一輪是 **MIB campaign**——
> 撿回一個 2026-07-31 寫好但主實驗從沒跑完的機制（`mib_unify` + `post_snap`），
> 並把它從「無條件套用」改成「可拒絕的候選」（`MIB_BOTH`）。
> Neutral 1.2323 → **1.2252**，Feasible 全程 100/100。詳見下方
> 「研究方向紀錄（2026-08-15 MIB campaign）」。
> 同一輪也把 FloorSet 評測器同步到上游 `aadddcc`／規格 **v10**。

> **2026-08-18～26 更新**：轉向**防崩潰/穩健性 campaign**——不再只追訓練集
> 100 案的 Neutral，而是拿 2026-08-15 真實送測隱藏測資的執行紀錄（100 案，
> 10 案不可行）回頭反查根因，補齊生產路徑的安全洞。新增 AREA GUARD（面積
> 硬約束從「上游預算假設」改成「主動檢查+校正」）、push-candidate fallback
> 重排、NaN/殘留重疊二次防護，另外也把 highspy LP 熱啟動升格為預設
> （Neutral 1.2249→**1.2236**）。詳見下方「研究方向紀錄（2026-08-18～26
> 防崩潰/穩健性 campaign）」，畫布上對應新的「保護機制」群組。

對應程式碼：`d:\ICCAD-2026-C\collaborate\electro_v22\`（`analytical_place.py` /
`dirichlet_init.py` / `legalize.py` / `lp_legalize.py` / `slice_pack.py` /
`cluster_virtualize.py` / `soft_repair.py` / `electro_parallel.py` /
`electro_optimizer.py`）。

---

## 現在的成績（一眼看懂進度）

全 100 案驗證，`electro_v22` 生產預設（官方 `iccad2026_evaluate.py --evaluate`，
評測器已同步到上游 `aadddcc` ／規格 v10，本地量測改用單調時鐘）：

| 指標 | 數值 | 說明 |
|---|---|---|
| 中性 Total Score | **1.2230** | `Q·P`，忽略 runtime。確定性——同設定重跑必定同值 |
| 真實 Total Score | 0.861 – 0.866 | 含 `R=max(0.7,RT^0.3)`。給範圍不給單一值，理由見下方 danger |
| Feasible | **100/100** | 全部合法，硬約束零違規 |
| 平均 runtime | 1.91 – 2.12s | 散布 10%，**不應該當成演算法的屬性報告** |

> [!danger] **只有 Neutral 是確定性的；Avg Runtime 是誤導性的摘要**
> 同一份程式碼、同一台機器，七次量測的 Avg Runtime 落在 1.91–2.12s，而 Neutral
> 每次都精確是 1.2252。更根本的是這個統計量本身就選錯了：**case 0 要付掉全部
> import／暖機成本（約 18s），它佔「平均執行時間」的 8.6%，卻只佔 Total Score
> 權重的 0.002%**——計分是逐案算 RuntimeFactor 再用 `e^(n/12)` 加權，而
> case 0 是 `n=21`。**驗收時看 Neutral 與 Feasible，那兩個對不上才是真的有問題。**

> [!info] **官方 v10 給出的分數尺標（2026-05-20）**
> 完美品質 + 中位數速度 = Total **1.00**；完美品質 + 3× 以上快（封頂）= **0.70**
> 是理論下限。我們的平均速度乘數已經是 **0.7165**，所以**速度只剩約 2.3% 可拿，
> 品質還有 20.3%**。這是「品質是唯一剩下的槓桿」最權威的佐證。

> [!info] **兩個驗證工具現在都能給 REAL 分數了**
> `ml.case_report_electro` 用官方 Alpha per-case median CSV 當 RT 基準；
> 官方 `iccad2026_evaluate.py --evaluate` 本身的 Total Score 欄位其實是
> RT-neutral（`RuntimeFactor=1.0`）placeholder，不是真 RT 分數——兩者的
> Neutral 數字應該互相印證（本輪 1.3260 vs 1.3280，差距在量測雜訊內），
> 但只有前者算出的欄位叫「REAL」。

**目前最強已知配置就是生產預設**（不用額外設環境變數）。核心組合：
`ELECTRO_DUAL_ASCENT_BND=1 ELECTRO_DA_K=40`（8/11 才確認 v19 完全沒有這段
程式碼，之前設了也無聲失效）+ `ELECTRO_RESHAPE_PORTFOLIO=1`（8/4 在 v19 上
驗證正面但因為 REAL 打平而停用的機制，這輪移植到 v20 跟對偶上升疊加，
背靠背驗證兩者沒有互相拖累，一起設為預設）。

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

### Stage 2b：`slice_pack()` — Slicing 切割樹合法化（**主力生成機制**）

用一個矩形，依子樹面積比例**遞迴 guillotine 切割**（Otten 1982）。官方對
軟方塊只查面積 ±1% 容差、無長寬比限制，切割式因此填充率可近 100%。切割
順序繼承自 `analytical_place` 收斂後的座標，任何一步無法保證合法就整套
回傳 `None`，呼叫端沿用梯度式路徑——**不可能產出不合法的解**。

> [!success] **Cluster 虛擬化複合子樹展開（`cluster_virtualize.py` / `ELECTRO_SLICE_COMPOUND=1`）**
> 在全域切割前，將同群組成員預先縮聚為單一原子複合超級塊 $M_C$；切割抵達
> 該空間後，在內部子樹依面積比例以 $W=0$（零空白）展開，
> **從結構上證明滿足 Subtree Enclosure 定理，保證 $V_{grp} \equiv 0$**。

> [!success] **`ELECTRO_SLICE_ALIGN_PORTFOLIO=1`（生產預設）**
> `slice_pack` 有兩種切割方向（沿哪一軸優先切），舊版只能挑一種固定用；
> 現在 `return_pair=True` 讓兩種方向都各自產生一份候選丟進同一個池，交給
> 後面的 proxy cost 排名逐案挑，不用預先猜哪個方向比較好。

### Convex Sizing LP 凸優化聯合壓實（`convex_sizing.py`，生產預設）

基於 Slicing 切割拓撲，建立水平與垂直方向的無重疊偏序 DAG（傳遞約簡 + CSR 稀疏矩陣）。
調用 HiGHS 線性規劃求解（`ELECTRO_CONVEX_HIGHSPY=1`，`scipy.optimize.linprog`）：
**同時優化模組長寬比分配與幾何壓實**，目標函數聯合最小化 Area Slack 與 HPWL（$\gamma=1.0$）。
支援多執行緒並行加速（`ELECTRO_CS_THREADS=4`），為 Top-K 候選提供全域最優幾何微調。

### Stage 3：`soft_repair()` — 軟約束修復

boundary snap（沿牆掃描貼齊）+ wide-swap（預設開）、grouping push-past
（預設開）。

**2026-08-15 新增三項 MIB 相關的生產預設**（`ELECTRO_MIB_UNIFY=1`、
`ELECTRO_MIB_POST_SNAP=1`、`ELECTRO_MIB_BOTH=1`）：

`mib_unify` 把同一 MIB 群組的軟方塊統一成相同形狀，放在**修復鏈之後**——
此時才知道每顆方塊周圍實際有多少空間。它是嚴格加法式的：群組必須等面積、
錨點的 `w·h` 必須精確等於群組面積、改完要通過不重疊／不出 bbox／不低於 floor，
任何一關過不了就跳過該成員。實測 100 個 MIB 群組全部等面積，所以 **V_mib 的
不可約下限是 0**。

> [!danger] **必須成對開，單獨開會退步**
> `mib_unify` 改形狀會把原本已貼齊的 boundary 方塊推歪,這正是早期判定
> 「無條件套用 mib_unify 會退步」的機制。`MIB_POST_SNAP` 就是把位移修回來的
> 那一輪 `boundary_snap`。**單旗標掃描永遠找不到這個組合**——這是一個 2026-07-31
> 就寫好、但主實驗從沒跑完而被留在預設關閉的機制。

> [!success] **`MIB_BOTH`：把變換做成「可拒絕的候選」**
> `MIB_UNIFY` 是無條件套用在每一個候選上的,所以候選池裡從來沒有「未統一」的
> 版本可選。逐案拆解顯示代價集中在權重最高的 case 99(V_rel 0.0746→0.1045,
> 而 hpwl 幾乎沒動)。`MIB_BOTH` 讓 `_finish` 把套用 MIB **之前**的幾何也交出來,
> 兩份都進候選池讓 proxy 逐案挑,成本只有每個候選多一次 `remove_overlap`。
> 結果 case 99 **分毫不差地回到起點值**,毛惡化從 +0.00888 減半到 +0.00417。
>
> **這修正了對「事後搬動一律失敗」的理解**:真正的規則不是「不能有事後階段」,
> 而是**不能無條件套用一個只盯單一目標的變換**。同一個變換做成可拒絕的候選就是
> 安全的,因為最壞情況等於不做。

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

> [!tip] **看不懂某個參數在講什麼?**
> 逐項白話解釋、為什麼是這個值、以及踩過的坑,見
> **[[ICCAD_code/Electro_Parameters_Reference|electro_v22 參數速查表]]**。

```bash
ELECTRO_CLAMP=1
ELECTRO_NONNEG=1
ELECTRO_SEEDS=4                    # 舊版是 8/16，現在更省而分數不掉
ELECTRO_PARALLEL=1
ELECTRO_ML_INIT=1
ELECTRO_ITERS=300
ELECTRO_PLACE_COMPACT=1
ELECTRO_PLACE_COMPACT_ITERS=150     # 新增（8/11），舊值 400 是調過頭不是取捨
ELECTRO_PLACE_COMPACT_TOPK=3        # 死參數：ELECTRO_PLACE_COMPACT_BEST（預設1）分支永遠先命中，這個 elif 打不到
ELECTRO_MIB_PORTFOLIO=0              # 改為關閉（8/15）：MIB_UNIFY 開啟後它只產生重複品，
                                     # 關掉反而更好（1.2277→1.2270）且候選更少
ELECTRO_MIB_UNIFY=1                  # 新增（8/15），必須跟 POST_SNAP 成對
ELECTRO_MIB_POST_SNAP=1              # 新增（8/15），修 mib_unify 造成的 boundary 位移
ELECTRO_MIB_BOTH=1                   # 新增（8/15），統一前的版本也進候選池讓 proxy 挑
ELECTRO_HPWL_ANCHOR=1                # 新增（8/16），校準過的線長排名錨點。舊錨點比真實
                                     # H_baseline 大 2079 倍，讓 proxy 幾乎看不見線長。
                                     # 只有在 MIB_BOTH 開著時才贏（見參數速查表）
ELECTRO_FAST_CLEANUP=1
ELECTRO_SLICE_ALIGN_PORTFOLIO=1
ELECTRO_MIB_ANCHOR_SNAP=1
ELECTRO_MIB_ANCHOR=1
ELECTRO_DIRICHLET_INIT=1
ELECTRO_DIRICHLET_GRP_WEIGHT=0
ELECTRO_DIRICHLET_MIB_WEIGHT=0
ELECTRO_DIRICHLET_BND_WEIGHT=2.0
ELECTRO_REPAIR_ROUNDS=2              # 新增（8/11），第3輪逐位元無作用，舊值3白花時間
ELECTRO_DUAL_ASCENT_BND=1            # 新增（8/11），只存在 v20，v19 設了無聲失效
ELECTRO_DA_K=40
ELECTRO_RESHAPE_PORTFOLIO=1          # 新增（8/11），8/4 在 v19 上驗證正面的機制移植進來
ELECTRO_LP_DISPLACEMENT_PORTFOLIO=1
ELECTRO_LP_DISPLACEMENT_SEEDS=4
ELECTRO_LP_DISPLACEMENT_MIN_BLOCKS=0
ELECTRO_LP_DISPLACEMENT_TOPK=1
ELECTRO_AREA_TOL_GUARD=0.0098        # 新增（8/18），面積硬約束最終強制執行門檻
ELECTRO_MIB_PERBLOCK_AREA=1          # 新增（8/25），AREA GUARD 觸發率降 99.65%
ELECTRO_CONVEX_HIGHSPY=1             # 新增（8/26），LP 熱啟動，Neutral 1.2249→1.2236
```

> [!info] NaN 防護（`isfinite` 檢查）和 push-candidate 二次重疊驗證不是
> 環境變數旗標，是寫死在 `electro_parallel.py` 裡的無條件檢查，沒有開關。

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

### 研究方向紀錄（2026-08-03～08-06）：3 個負面結果 + 2 個之後被納入生產預設

這一輪依序嘗試三個更大方向（RT 改善 → Per-RMAP 可行性追尋 → 完整 ADMM
變數分裂），全部收斂到乾淨的負面結論，代表梯度式 + 切割式 + portfolio 排名
這條技術路線在「同一個 `analytical_place()` 主迴圈裡加懲罰項」這個框架下
大概已經到頂：

| 方向 | 結果 | 關鍵數字 |
|---|---|---|
| **L-BFGS 打磨階段** | 負面 | `frac≥0.7` 凍結 schedule、Adam→L-BFGS 收尾。3 組切換點全部更慢（3.35-6.54×）且品質更差。`ELECTRO_LBFGS` 預設關閉 |
| **Per-RMAP 可行性追尋** | 負面 | 拿現有 repair 函式當投影運算子＋擾動＋重設外迴圈。20 案平均 V_rel 變差 28.2%、慢 33 倍，15 輪上限內全部沒收斂到零違規。獨立標準腳本，未整合進生產管線 |
| **ADMM 邊界一致性** | 負面 | `electro_v21/`，把 `lam_bnd*bnd` 換成有號殘差增廣拉格朗日二次拉力。20 案 Neutral 變差 +8.1%，**Vgrp/Vmib/Vbnd 全部變差**（連沒被動到的 grp/mib 都變差——「共用梯度預算耦合」再次出現，只是換了數學形式）。`ELECTRO_ADMM_BND` 預設關閉 |
| **對偶上升 boundary** | ✅ **8/11 納入生產預設** | `ELECTRO_DUAL_ASCENT_BND=1 ELECTRO_DA_K=40`。8/6 驗證正面時卡在「只存在 v20 程式碼、v19 完全沒有這段」——之前的「已驗證但未預設」其實是「不在生產版本裡，設了也無聲失效」。8/11 promote v20 為主力後正式啟用 |
| **合法化長寬比彈性** | ✅ **8/11 移植到 v20 後納入生產預設** | `legalize_qinfer_reshape`：在 v19 上驗證 Neutral 改善最多（-2.1%）但 REAL 打平（0.9801→0.9816），當時停用。8/11 移植到 v20、跟對偶上升疊加，同批次背靠背重測：Neutral 1.3612→**1.3260**（-2.6%，比 v19 上更好），REAL **不降反微升**（1.0005→0.9987），Vgrp/Vbnd 都改善，代價是 Vmib +16%（43→50）——沒有淨負面，設為預設。過程中意外揪出一個 **WSL-only 浮點精度 bug** 並修復：非可調形狀方塊的 log/exp 往返運算讓形狀漂移 ~4e-6，重新打開跟鄰居的重疊（`config_75` 在 WSL 上曾誤判 `Cost=10`） |

### 研究方向紀錄（2026-08-11 RT/預設值調校 campaign）：2 個確定改動 + 9 個否決方向

不發明新演算法，純粹靠重新量測既有設定、找回沒進生產的機制。詳細證據見
repo 內 `docs/superpowers/2026-08-11-rt-and-default-retuning-campaign.md`。

**兩個確定的預設值改動**（獨立於上面的對偶上升/reshape）：

| 改動 | 結果 |
|---|---|
| `PLACE_COMPACT_ITERS` 400→150 | 舊值是調過頭不是取捨——150 比 400 品質更好（Neutral 1.3776→1.3370）**還更快**（2.218s→2.156s） |
| `REPAIR_ROUNDS` 3→2 | 第 3 輪逐位元無作用（全 100 案 Neutral 與三個違規數完全一致），只白花 ~3.5% 時間 |

**9 個否決方向**（都測過，淨值全部比預設差）：

| 方向 | 結果 |
|---|---|
| `ELECTRO_TARGET_UTIL` 0.90/0.92/0.95（預設 0.85） | **乾淨的 Q/P 對撞盤**：填越滿 area_gap 越好，但擠壓空間直接壓縮排 cluster 的自由度，V_grouping 系統性變差，淨 Neutral 全部輸給預設 |
| `ELECTRO_HPWL_POLISH=1` | 生產路徑上根本不可達（`return_pair` 提早 return），強制打通後實測反而更差，不值得修 |
| `ELECTRO_OV1` 3.5/1.8（預設 2.5） | 兩個方向都更差 |
| `ELECTRO_GROW_END=0.85`（預設 0.7） | Neutral 更差，V_grouping 暴增 |
| `ELECTRO_AREA_GROW=0.25`（預設 0.1） | 更差 |
| `ELECTRO_DA_BND_CEIL=10.0`（預設 6.0） | **死參數**：違規數與 Neutral 逐位元不變，代表現有案例從沒真的撞到 6.0 這個上限，調高純粹白花 14% 時間 |
| `DUAL_ASCENT_GRP=1` | Neutral 變差，V_mib 43→72——修 grouping 打壞 MIB，「共用梯度預算耦合」第四種數學形式再現 |
| seeds 自適應 / seeds>8 | 無據可依 / REAL 反升 |
| 5-way 參數掃描其餘方向 | 見 campaign 文件完整表格 |

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

### 研究方向紀錄（2026-08-15 MIB campaign）：4 個預設改動 + 5 個否決方向

**Neutral 1.2323 → 1.2252（−0.58%），Feasible 全程 100/100。**

| 方向 | 結果 | 說明 |
|---|---|---|
| **MIB_UNIFY + MIB_POST_SNAP** | ✅ **納入生產預設** | 2026-07-31 就寫好、主實驗從沒跑完的機制。**必須成對**：單獨開是拿 V_boundary 換 V_mib，配對才淨賺。全 100 案 1.2323→1.2277 |
| **MIB_PORTFOLIO 改為關閉** | ✅ **納入生產預設** | UNIFY 開啟後,它產生的「額外候選」是同一份幾何統一兩次的重複品,只增加 proxy 雜訊與工作量。1.2277→1.2270,兩次重跑都精確重現 |
| **MIB_BOTH** | ✅ **納入生產預設** | 統一前的版本也進候選池。case 99 分毫不差復原,毛惡化減半。1.2270→1.2252 |
| 逐子樹 util | 負面 | Ji et al. (COR 2017) 證明軟方塊 λ=∞ ⟹ 任兩個永遠能零死區合併,死區只該出現在含剛性方塊的子樹。**實測全滅**:收走 100%/75%/50%/25% 的軟方塊餘裕,Feasible 各為 18/17/16/17（滿分 21）。定理沒錯,**套用對象錯了**——留白在這條管線裡不是堆疊死區,是修復階段的活動空間 |
| mib_unify 後補 grouping_repair | 負面 | 1.2226→1.2236 且更慢。**「傷害有兩半、兩半都修」的推理是錯的**,因為第二個修復本身也會造成傷害。「共用預算耦合」第六次確認 |
| `DUAL_ASCENT_GRP` + MIB 配對 | 負面 | 假設:GRP 當初輸只因為打壞 MIB,而 mib_unify 能事後修。實測 MIB 的修復力被 GRP **摧毀 82%**（基準上值 −0.34%,疊在 GRP 上只剩 −0.06%）。**耦合會消耗掉下游機制的作用條件**,不只是分數取捨 |
| `SLICE_CLUSTER_VIRTUALIZE` | 負面 | 21 案只剩 17 案可行,RT 從 3.1s 暴增到 8.0s |
| `MIB_PORTFOLIO_SNAP`（在候選路徑補 snap） | 零效果 | 四位小數分毫不變。UNIFY 開時 `_finish` 已 snap 過是 no-op；UNIFY 關時統一版候選根本不會被選中 |

> [!abstract] **這一輪最重要的方法論轉向**
> 參數空間掃到約 46 個維度、只有 2 個正向之後,改問一個不同的問題:
> **倉庫裡有沒有「寫好了、有守衛、但主實驗從沒跑完」的機制?**
> 第一個命中就是 MIB 配對——它預設關閉不是因為輸,是因為實驗被放棄。
> `- [ ]` 沒勾完的 plan 文件是一份現成的候選清單,每一項都已經有設計、有守衛、
> 有明確驗收條件,比從零想新機制便宜得多,也比再掃一輪參數命中率高得多。

> [!danger] **量測紀律:REAL 被修正了三次,Neutral 一次都沒錯**
> −2.6%（對跨階段舊紀錄）→ −1.15%（配對重跑但**時鐘壞掉**）→ −0.27%（配對 + 單調時鐘）。
> **三次錯誤全在帶 RT 的指標,而且每次都往樂觀方向錯**——這是結構性的,因為
> RT 的每個雜訊源（時鐘倒退、負 runtime 被夾到速度下限）都偏向讓分數變好。
> 規則:**確定性的指標拿來下判斷,帶雜訊的指標只在儀器被驗證過之後才拿來報數字。**
> 驗證儀器的兩個方法:讓它重現一個已知真值、以及把同一設定跑兩次建立雜訊底線。

---

### 研究方向紀錄（2026-08-18～26 防崩潰/穩健性 campaign）

觸發點：`official guidelines/` 裡有 2026-08-15 一次真實送測隱藏測資
（100 案）留下的完整執行紀錄（`beta_evaluation_results.json` +
`eval_op_wrapper.log`），Total Score 1.5766，**10/100 不可行**。這一輪不是
在訓練集上調參數，是拿這份真實失敗紀錄逐一回查根因、補洞。

| 機制 | 觸發條件 / 保護內容 | 結果 |
|---|---|---|
| **AREA GUARD**（`electro_optimizer.py` ~1235-1280，2026-08-18） | 面積硬約束 `\|w·h−a\|/a≤0.01` 原本只靠「上游預算假設」（`area_tol + iters·delta² ≤ 0.0098`）保證，沒人真正檢查。改成主動檢查：超標的軟方塊等比例縮放（`s=√(a/(w·h))`，以中心為錨、長寬比不變）回精確目標面積 | 比對隱藏測資紀錄：**10 個不可行案例的 `max_area_drift` 100% 全部 >0.01（10 對 10），其餘 90 案全部精確等於 0**——不是巧合，是這個假設在隱藏測資上失守。本地 100 案 0 案觸發（純加法式 no-op），訓練集配對驗證觸發率隨下一項機制再降 99.65% |
| **MIB 群組面積互斥性門檻鬆弛**（`ELECTRO_MIB_PERBLOCK_AREA=1`，2026-08-25） | 放寬 MIB 群組 ρ_M>1.0202 的面積互斥性門檻，從根本減少會逼近面積上限的佈局 | 訓練集 2,000 案配對驗證：AREA GUARD 觸發率 1149/2000 → **4/2000（-99.65%）**，Vbnd/Vgrp 大幅改善、Vmib 持平、可行性維持 100% |
| **Fallback Ladder 重排** — push candidate 提到第一級 | `slice_pack` 在所有 seed/aspect/wall 組合都失敗時的救援路徑，原本要等 3 級全部 fallback 完才會嘗試 push candidate，浪費 RT 又晚救援 | 2 個真實抓到的崩潰案例：cost 2.32/2.24→**1.99/2.00**，RT ~7s→**4.7-4.8s**（同時省 RT + 提升品質） |
| **`place()` 輸出 NaN 防護**（`electro_parallel.py`） | `place()` 輸出後立即 `isfinite()` 檢查，不通過就丟棄該候選，阻斷 NaN 往下游 `legalize()` 傳染 | 關掉一條理論上可能的靜默壞資料路徑 |
| **Push-Candidate 二次重疊驗證** | `legalize()` 後補 `_overlap_pairs()` 檢查，沒清乾淨的候選整個作廢——補上 `legalize.py`「保證零重疊」承諾裡漏未排除的 preplaced-preplaced 重疊 | 關掉一條 push-candidate 專屬的重疊逃逸路徑 |
| **highspy LP 熱啟動**（`ELECTRO_CONVEX_HIGHSPY=1`，2026-08-26） | 同一候選內相鄰兩輪 SLP 重用上一輪單純形法基底（不是防崩潰，是品質+RT 雙贏，一併升格） | Neutral 1.2249→**1.2236**（確定性），RT 中位數降約 6.6% |

**2026-08-26 針對性迴歸驗證**：從訓練集在那 10 個歷史不可行的 block_count
（23, 25, 27, 28, 34, 37, 45, 53, 64, 76）各抓 4 案（共 40 案，新增進
`collaborate/scripts/default_stress_cases.json` 的 `beta_hidden_known_risky_n`
類別），用現在的生產程式碼跑：**40 案全部可行，`max_area_drift` 全部乾淨
落在門檻（0.0098）下方的 0.0091**，沒有一案觸發 AREA GUARD 硬矯正。第一次
能對著真實隱藏測資的失敗案例做直接迴歸驗證，不只是訓練集配對統計。

> [!success] **AREA GUARD 是本輪的核心心法**：把「假設」換成「檢查」
> 硬約束不能只靠上游步驟的數學預算保證（那個預算在沒見過的資料分布上會
> 失守），必須在輸出前有一道主動驗證+校正。這條原則同樣適用於
> NaN 防護和殘留重疊二次驗證——三者都是「原本靠上游步驟的隱含保證，
> 現在補一道明確檢查」的同一種修法。

---

## 下一步該往哪走

> [!warning] **「同一個梯度迴圈裡加懲罰項」這個框架大概已經到頂**
> L-BFGS/Per-RMAP/ADMM/`DUAL_ASCENT_GRP` 四個獨立方向都測過、都收斂到
> 「修好一個軟約束就拖累另一個」的負面結果（「共用梯度預算耦合」）。
> `ELECTRO_TARGET_UTIL` 掃描也證實 Q（HPWL/area）跟 V_grouping 是同一份
> 幾何自由度的兩端，不是能各自獨立優化的目標。剩餘品質空間：
> `V_grouping=111`、`V_mib=50`、`V_boundary=128`（8/11 生產預設）。
> 如果還要繼續推進，大概需要跳出這個框架——例如真正的變數分裂（獨立的
> 子問題、獨立的求解器，而不是換一種懲罰項包裝），或是換一種完全不同的
> 搜尋範式。

---
**相關筆記**：[[ICCAD_code/7_Electrostatic_Placer|7. 電靜力法擺放器]] ·
[[ICCAD_code/8_Winning_Strategy_and_Roadmap|8. 奪冠策略總覽]] ·
[[Electronic_Pipeline.canvas|架構畫布]]
**回到**：[[ICCAD/ICCAD-Dashboard|ICCAD 儀表板]]
