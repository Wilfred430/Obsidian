---
title: 2026-08 研究日誌
tags: [ICCAD, research-log]
date: 2026-08-01
---

# 2026-08 研究日誌

> 這是從 [[ICCAD_code/8_Winning_Strategy_and_Roadmap|8. 奪冠策略總覽]] 搬出來的
> 逐次實驗記錄，原始編號（§8.N）保留供對照舊連結。

## 本月做了哪些變更

- §8.46（2026-08-01）：GPT 5.6 Terra 新增的 v12-v15 —— v15 `GRAPH_INIT` 實測（Q 首次真的贏，但被 P 吃掉）
- §8.48（2026-08-02）：DG-RePlAce 排程方向翻轉 —— 首次乾淨雙贏,合併進新 `electro_v16`
- §8.49（2026-08-02）：`lam_ov`/`lam_bb` 排程翻轉測試 —— 強→弱不是萬用規則
- §8.50（2026-08-02）：訊號濾波式 cluster-aware 初始化（GiFt 系）—— 七組配置全部輸給現有 ML 初始化
- §8.51（2026-08-02）：PARSAC 啟發的兩個 boundary 修法 —— 高風險版確認有害、低風險版確認安全但無效
- §8.52（2026-08-02）：BADGE 式 Dirichlet 調和延拓初始化 —— 本 session 目前最佳新機制
- §8.53（2026-08-02）：slice_pack 的 cluster 虛擬超級方塊 —— Gemini 建議評估 + 負面實測結果

---

## §8.46（2026-08-01）：GPT 5.6 Terra 新增的 v12-v15 —— v15 `GRAPH_INIT` 實測（Q 首次真的贏，但被 P 吃掉）

使用者用 GPT 5.6 Terra 產出 `electro_v12` ~ `electro_v15`。先做結構盤點
（行尾正規化後比對，避免 CRLF/LF 造成的假差異）：**四個版本的
`slice_pack.py` / `analytical_place.py` / `legalize.py` / `soft_repair.py`
全部與 v11 逐位元相同**，改動集中在 `electro_optimizer.py`（與 v13/v14 的
`electro_parallel.py`），且新機制**全部預設關閉**——紀律良好。

| 版本 | 新機制 |
|---|---|
| v12 | 把既有的 `MIB_ANCHOR`/`MIB_ANCHOR_SNAP` 納入 setdefault（非新機制） |
| v13 | `ELECTRO_DUAL_SLICE_PORTFOLIO` + `ELECTRO_HPWL_POLISH` |
| v14 | `ELECTRO_LP_DISPLACEMENT_PORTFOLIO` |
| **v15** | **`ELECTRO_GRAPH_INIT`** |

**選測順序的依據**：§8.44 已證實「再加一個候選維度」會撞 runtime 牆
（+27~30%、淨負），v13/v14 都屬於這類；**v15 改的是初始化起點、不增加候選數**，
落在 §8.44 指出我們真正需要的「免費的品質」類別，因此優先驗證。

### v15 `ELECTRO_GRAPH_INIT=1` 實測（我們的最佳設定，seeds=4）

| 指標 | 基準 v7 | v15 GRAPH_INIT | |
|---|---|---|---|
| 中性 Total | **1.4376** | 1.4688 | 差 2.2% |
| 真實 Total | **1.0614** | 1.1141 | 差 5.0% |
| **Q（幾何）** | 1.1784 | **1.1725** | **v15 勝 −0.50%** |
| └ hpwl_gap | 19.49% | **19.35%** | 略勝 |
| └ area_gap | 16.19% | **15.15%** | **勝 1.04 點** |
| **P（軟違規）** | **1.2188** | 1.2505 | **v15 敗 +2.60%** |
| Vgrp / Vmib / Vbnd | 172 / 61 / 188 | 200 / 75 / **165** | Vbnd 勝，Vgrp/Vmib 大敗 |
| 平均 runtime | 2.228s | 2.562s | +15.0% |

### 判定與意義

**不採用**（中性 +2.2%、真實 +5.0%）。但這個結果的形狀值得記錄：

**① Q 首次真的被壓下去了。** 過去多次嘗試攻 Q 都失敗（§8.43 的 STEPS 全域
調高是 Q 改善但 P 吃更多；§8.45 的 v10 是 Q 直接惡化）。**`GRAPH_INIT` 是
目前唯一同時壓低 hpwl_gap 與 area_gap 的機制**（area_gap −1.04 點）——證明
「改初始化起點」這條路對 Q 真的有效。

**② 但 P 的代價是目前所有實驗中最大的**（+2.60%，Vgrp 172→200、Vmib 61→75）。
改變初始佈局讓 cluster 成員散開、MIB 群組更難統一。

**③ Q/P 零和的第 7 次現身，且這次兌換率最差**：Q −0.50% 換來 P +2.60%，
約 1 : 5.2（前幾次分別是 1:1.17、1:0.50、1:1.15）。

> 💡 **這反而強化了一個方向判斷**：`GRAPH_INIT` 證明了初始化確實能改善 Q，
> 問題純粹在於它同時破壞了 cluster/MIB 的鄰接結構。**若能設計一個「保留
> cluster 鄰接性」的圖初始化**（例如以 cluster 為單位建圖、群組內成員維持
> 綁定），就有機會拿到 Q 的收益而不付 P 的代價——這是目前最具體、最有依據
> 的下一步。

---

## §8.48（2026-08-02）：DG-RePlAce 排程方向翻轉 —— 首次乾淨雙贏,合併進新 `electro_v16`

讀 DG-RePlAce（Kahng & Wang, dataflow-driven GPU 加速佈局）找 idea 時，發現它
處理 cluster adjacency 約束用的是**隨迭代衰減**的懲罰權重（強→弱），跟本專案
`analytical_place.py` 既有的 `lam_grp`/`lam_bnd`/`lam_mib_shape` 排程方向剛好
**相反**（本專案原本是弱→強：`grp0+ (grp1-grp0)*frac`，預設 `0.2→1.8`）。
`grp0/grp1`、`bnd0/bnd1` 早已是可調環境變數，`mib_shape` 端點原本寫死，補上
`ELECTRO_MIB0`/`ELECTRO_MIB1` 使其同樣可調（向下相容，預設值不變則行為不變）。

**踩坑記錄（已寫進 [[obsidian-vault-knowledge-base]] 同等級的 skill 記憶）**：
1. 兩次以為「基準線」是可信的，結果其實漏開了 `ELECTRO_MIB_ANCHOR_SNAP`/
   `ELECTRO_MIB_ANCHOR`——這兩個 v11 血緣裡已確認的關鍵機制沒設，Vmib 因此
   虛高（117/103，而非正常的 50 幾）。
2. `electro_v14` 只是靠 sys.path 順序借用 `electro_v11`/`electro_optimized`
   模組的薄包裝，拿來疊加新實驗風險高——已把 LP-displacement-portfolio 機制
   直接合併進 `electro_v16`，自成一體（新增 `lp_legalize.py` 本機副本，不再
   跨目錄找 `electro_optimized/legalize.py`）。
3. Windows 原生 Python 沒有 `fork()`，`ELECTRO_SEEDS>1` 會靜默退化成序列執行
   （4 個 seed 變成序列跑，慢 ~4 倍且無任何錯誤訊息）——所有多 seed 驗證
   之後一律走 WSL；已寫進 `floorplan-guard` skill §6a。

**最終驗證**（`electro_v16` = v11 + v14 LP-displacement-portfolio + MIB anchor
機制合併為自足版本，這是目前已知最佳可重現配置的忠實重建，Real Total
1.0754 對比文獻記載的 1.0745，誤差 0.08%）：

| 配置 | Neutral $Q\cdot P$ | Real $Q\cdot P\cdot R$ | Vgrp | Vmib | Vbnd | 快於 median |
|---|---:|---:|---:|---:|---:|---:|
| 最佳配置，原排程（弱→強） | 1.4269 | 1.0754 | 163 | 59 | 186 | 96/100 |
| 最佳配置，翻轉排程（強→弱） | **1.4018** | **1.0578** | **148** | **55** | **167** | **99/100** |
| 變化 | **−1.76%** | **−1.64%** | −15 | −4 | −19 | +3 |

**這是本專案 Q/P 零和現象 8 次現身以來，第一次乾淨雙贏**：Neutral 分數下降
代表 Q 沒有被犧牲去換 P，三項違規（Vgrp/Vmib/Vbnd）同時下降，且 runtime 也
沒有變慢（faster-than-median 反而增加）。加權 case-level 差分
（`scripts/compare_electro_reports.py`）確認：

- 前 15 名加權改善案例集中在高權重（n≥79，多數 n≥100）案例，正好對上
  $e^{n/12}$ 權重最重的區間。
- 退步案例分散於中高 n，單筆幅度普遍小於改善案例，無單一案例災難性退步。
- 無系統性 faster→slower 翻轉（`floorplan-guard` §7 的既有戒律）。

已升格為 `electro_v16` 的預設方向（`ELECTRO_GRP0/1`、`ELECTRO_BND0/1`、
`ELECTRO_MIB0/1` 六個環境變數仍可覆寫回原方向做對照，未改動 v7/v11/v14 等
既有版本）。

> 💡 **可能的機制解讀**：本專案原本「弱→強」的設計理由是「先讓佈局自由成形，
> 再逐步收緊約束」；DG-RePlAce「強→弱」的理由是「趁佈局還自由時就先滿足
> cluster 鄰接（幾乎不用犧牲什麼），後期放鬆讓線長/密度微調不用跟約束搶
> 梯度預算」。兩種直覺都合理，但實測顯示後者在本問題上更優——這暗示「何時
> 對優化器最有利去滿足一個結構性約束」比「該用多強的懲罰」更關鍵，值得
> 對其他排程式懲罰項（`lam_ov`/`lam_bb`）也做同樣的方向測試。

---

## §8.49（2026-08-02）：`lam_ov`/`lam_bb` 排程翻轉測試 —— 強→弱不是萬用規則

§8.48 的下一步：對 `lam_ov`（擴散力，原本 0.1→2.5 弱→強）與 `lam_bb`（緊實度，原本
0.24→0.04 已經是強→弱）也做同方向翻轉測試，兩者都早已是環境變數可調，無需改碼。

| 配置 | Neutral | Real | Vgrp | Vmib | Vbnd |
|---|---:|---:|---:|---:|---:|
| 目前最佳（v16 預設，grp/bnd/mib 已翻轉，ov/bb 維持原樣） | 1.4018 | 1.0578 | 148 | 55 | 167 |
| `lam_ov` 翻轉（弱→強 → 強→弱） | **1.6706（+19.2%）** | **1.3226（+25.0%）** | 217 | 155 | 170 |
| `lam_bb` 翻轉（強→弱 → 弱→強） | 1.4061（+0.3%） | 1.0919（+3.2%） | 145 | 181 | 181 |

**`lam_ov` 翻轉是災難性退步，`lam_bb` 翻轉在雜訊邊緣、略差。兩者都不採用。**

`lam_ov` 的退步跟程式碼原有註解完全吻合：弱→強是刻意設計——早期弱讓佈局自由散開/
成形，晚期才加強擴散力清掉殘留重疊，留一點縫給 legalize 收尾。翻轉後變成早期就
硬把方塊擠開（直接撐大 bbox/HPWL），晚期又沒力氣清乾淨（legalize 要善後的重疊
暴增），兩頭空。

**修正 §8.48 的解讀**：「強→弱排程更好」不是普適規則，只對**結構性鄰接約束**
（grp/bnd/mib——決定方塊該不該貼在一起）有效；對**擴散/密度類懲罰項**
（ov/bb——決定佈局該多鬆散/緊實）無效甚至有害。兩類機制在優化過程中的角色本質
不同：前者是「要不要滿足一個離散關係」，早期滿足幾乎不用犧牲什麼；後者是「要
多用力把方塊推開/收緊」，這件事本身就跟收斂路徑（先鬆後緊 vs 先緊後鬆）強耦合，
不是「早滿足、晚放鬆」這個框架能套用的。**這條路線到此為止，不建議再對其他排程
式懲罰項盲目套用同一招——每個機制的排程方向都要照它自己的收斂角色判斷，不能
類推。**

---

## §8.50（2026-08-02）：訊號濾波式 cluster-aware 初始化（GiFt 系）—— 七組配置全部輸給現有 ML 初始化

讀 GiFt（ICCAD'24）與 Bridging-the-Initialization-Gap（2025）找 idea：佈局目標
（最小化線長）數學上等價於 graph Laplacian 二次型（訊號在圖上的平滑度），
免訓練的一次性低通濾波即可生成「本來就平滑」的初始佈局，GiFt 論文本身宣稱
減少 33-46% 迭代/runtime。後者補上「帶符號虛擬邊注入額外資訊」的手法（負權重
=排斥、正權重=吸引），啟發了「把 grouping/MIB 同群成員的正權重邊塞進同一張
平滑化圖」這個嘗試，目標是同時解決 v15 GRAPH_INIT 留下的「Q 贏、P 輸」缺口。

**實作**：新增 `electro_v17`（v16 的自足複製），`gsp_init.py` 建構 b2b/p2b
連線圖 + clust_id/mib_id 同群正權重邊 + bcode 邊界虛擬牆節點（吸引式），做
symmetric-normalized-Laplacian 特徵分解 + 低通濾波，接入既有的 `init_centers`
hook（`ELECTRO_GSP_INIT=1`，opt-in）。

**七組全 100-case 配置結果**（Neutral，基準線 = 現有 `ELECTRO_ML_INIT` 訓練模型
初始化）：

| 配置 | Neutral | Vgrp | Vmib | Vbnd |
|---|---:|---:|---:|---:|
| 基準線 | **1.4018** | 148 | 55 | 167 |
| 純 GSP（無額外邊） | 1.4587 | 143 | 50 | 181 |
| + grp only | 1.4619 | 155 | 46 | 180 |
| + mib only | 1.4305 | **135** | 44 | 179 |
| + bnd only | 1.4482 | 146 | 71 | 172 |
| + grp+mib | 1.4548 | 153 | **41** | 184 |
| + grp+mib+bnd | 1.4940 | 152 | 54 | 182 |

**七組沒有一組贏過基準線，此方向不採用。** 但拆解出三個訊號各自的真實效果：

1. **mib 邊是唯一穩定正貢獻**：不只自己把 Vmib 壓到最低（41，grp+mib 組合），
   單獨開 mib 邊時 Vgrp 也意外變成全場最佳（135，比基準線的 148 還低）——
   MIB 群組與 grouping cluster 在這份資料集裡顯著相關。
2. **bnd 邊是唯一明確扯後腿**：不只沒改善 Vbnd（bnd-only 172，仍輸基準線
   167），還把 Vmib 直接推壞到 71（比不加任何額外邊的純 GSP 的 50 更差）
   ——在同一張圖裡加邊界虛擬節點會稀釋正規化 Laplacian 的度數，跟其他
   訊號搶「平滑化預算」，是本 session 反覆出現的「同一份資源被多個機制
   競爭」模式的又一次現身。
3. **即使是最好的單一配置（mib-only, 1.4305）仍輸基準線 2.05%**：根本原因
   推測是 `ELECTRO_ML_INIT` 已經是一個在真實訓練資料上學過的模型，隱含
   了邊界/約束的樣式；GSP 濾波純數學、免訓練，先天沒有這份學到的資訊。
   GiFt 論文自己的對照組是傳統無學習的 RePlAce/DREAMPlace，不是已經
   訓練過的模型——這個對照組落差可能正是這裡翻不了本的原因。

**可複用教訓**：把多個「應該有幫助」的訊號塞進同一個共享機制（同一張圖、同一
個懲罰函數、同一個候選池排名……）時，必須逐一拆解測試，不能只測全部疊加的
版本——本例若只測過 grp+mib+bnd 全開（最差組合），會誤判整個 GSP 方向沒救，
實際上 mib 訊號本身有真實、獨立可驗證的正面效果，只是被 bnd 訊號的副作用蓋
過去了。

---

## §8.51（2026-08-02）：PARSAC 啟發的兩個 boundary 修法 —— 高風險版確認有害、低風險版確認安全但無效

讀 PARSAC（同一批 FloorSet 作者）找 idea：論文開宗明義就是本專案量了 8 次的
Q/P 零和（「單純把約束併進 SA 目標函數會導致次佳甚至非法解」），並明確區分
grouping（懲罰項就夠）vs boundary（沒有專門修復移動幾乎必然違規）——跟我們
的實測排序（Vbnd167 > Vgrp148 > Vmib55）吻合。對照 PARSAC 的 CA-SA 跟我們
`soft_repair.py::boundary_snap` 的既有實作，找到兩處落差，依使用者指示先做
高風險再做低風險。

**新增 `electro_v18`（v16 的自足複製）。**

**① `boundary_extend`（高風險：接受 bbox 變大）**：PARSAC Fig.3 描述一種
死結——牆整面被「其他」boundary-constrained 方塊佔滿，`boundary_snap` 的
slide/swap 找不到自由格、也沒有無約束鄰居可換，正確地放棄。`boundary_extend`
模仿把違規方塊硬插到佔位隊伍最外側，即使因此撐大 bbox。**實測結果：只要
真的觸發，Vbnd 每次都變差**（config_42: 2→4、config_71: 2→5、config_100:
3→7、config_110: 6→9、config_120: 4→12，全 100-case 該候選從未被 proxy
排名選中，跟基準線 Neutral 1.4018 完全一致）。**根因**：`V_boundary` 是
相對「當下 bbox 邊界」算的，把方塊推向牆外側會讓 bbox 邊界跟著移動，原本
已經貼在舊邊界上的其他方塊全部連帶失格——這正是 PARSAC 圖 3 描述的問題本身
（不是他們的解法）；論文用 SA 的隨機接受機制去闖這個死結（可暫時變差、
靠後續搜尋修回來），我們的確定性單趟修復沒有這個安全網，模仿不了。
**不採用；因為做成嚴格加法式 portfolio 候選，從未被選中，沒有造成任何傷害。**

**② swap 候選優先序（低風險：改成優先選 `bcode==0` 的無約束方塊）**：邏輯
正確、且已有 `_v()` 驗證閘門保護正確性。**實測結果：全 100-case 與基準線
逐位元相同**（Neutral/Vgrp/Vmib/Vbnd 完全一致）。診斷confirmed 抽樣 20 案例
中確實有 5 次遇到「混合候選」（同時有約束與無約束的鄰居可換），但因為
`_v()` 驗證的是「違規總數有沒有變好」而非「換的是誰」，多個候選只要都能
修好同一個違規，最終違規計數就相同，只是底層座標細節不同——**是個安全但
無效的 no-op**。

**可複用教訓**：任何會改變 bbox 邊界的修復移動都要特別小心——`V_boundary`
是相對邊界算的，「修好一個違規」跟「移動 bbox 邊界本身」在效果上是耦合的，
移動 bbox 邊界會連帶讓其他已合格的方塊變成新的違規源。這解釋了為什麼
`boundary_snap` 原本的設計刻意用 `_bbox_same` 把 swap 限制在 bbox 不變的
範圍內——不是保守，是必要。

---

## §8.52（2026-08-02）：BADGE 式 Dirichlet 調和延拓初始化 —— 本 session 目前最佳新機制

透過 Connected Papers 衍生著作掃描（哪些新論文引用了我們已收藏的種子論文），
找到同一批 GiFt 作者的後續工作：**BADGE**（Park & Paik, DATE'26）與
**DPlanner**（Liu et al., IEEE TVLSI 2024，論文標題《Hierarchical Graph
Learning-Based Floorplanning With Dirichlet Boundary Conditions》）。兩篇都
在付費牆後、無公開全文（已依專案既有紀律只用 DOI 建中繼資料存進 Zotero，
`7VFIKFBV` / `26G5NR3G`，不繞過付費牆），但摘要 + Semantic Scholar 的方法
描述已足夠重建核心數學。

**跟 §8.50（GSP 濾波，已否決）的關鍵差異**：GiFt 的機制是對**正規化** graph
Laplacian 的特徵分解做低通濾波，套用在**隨機**訊號上；額外訊號（grouping/
MIB/boundary）用加虛擬邊的方式塞入，會稀釋正規化的度數，§8.50 實測合開多個
訊號時互搶「平滑化預算」。BADGE/DPlanner 的手法本質不同：對**未正規化**的
Laplacian 解**精確**的 Dirichlet 調和延拓（harmonic extension）——固定節點
（preplaced 方塊、pin/terminal 位置）的值已知，自由節點的座標由最小化
`Σ_edges w_ij·(x_i-x_j)²`（跟線長同構的二次能量）在邊界條件下的**封閉解**
決定，不是隨機訊號 + 濾波。額外訊號只是替同一個能量函數多加幾項，沒有度數
稀釋的副作用。

**實作**：新增 `electro_v19`（v16 的自足複製，即已包含 §8.48 排程翻轉的
最佳配置），`dirichlet_init.py` 建構 b2b/p2b 連線圖，preplaced/pin 節點設為
Dirichlet 固定值，grouping/MIB/boundary 各自可調權重的額外吸引邊，解
`L_UU x_U = -L_UF x_F`（加小量 ridge 正則化避免孤立節點的退化解），接入既有
`init_centers` hook（`ELECTRO_DIRICHLET_INIT=1`，opt-in）。

**六組全 100-case 配置結果**（基準線 = v16/v19 預設，即已含 §8.48 排程翻轉）：

| 配置 | Neutral | Vgrp | Vmib | Vbnd |
|---|---:|---:|---:|---:|
| 基準線 | 1.4018 | 148 | 55 | 167 |
| 純 Dirichlet（無額外訊號） | 1.4183 | 148 | **36** | 187 |
| + grp only | 1.4096 | **140** | 59 | 170 |
| + mib only | 1.4137 | 150 | 57 | 175 |
| **+ bnd only** | **1.3776（−1.73%）** | 143 | 51 | 174 |
| + grp+mib+bnd | 1.3835（−1.31%） | 141 | 53 | **161** |

**只有 bnd-only 與三合一贏過基準線，而且 bnd-only 自己比三合一更好**——
grp-only、mib-only、純 Dirichlet 單獨都比基準線差，合在一起反而拖累了 bnd
訊號單獨的威力（跟 §8.50 grp+mib 合開比單開更好正好相反的耦合方向，再次
證明「這類訊號互動必須逐一拆解測試，不能只看合開結果」的教訓仍然成立）。

**已用加權 case-level 差分驗證**（`scripts/compare_electro_reports.py`，
基準線 vs bnd-only）：改善集中在高權重案例（n≥82 為主），退步分散在中高 n
且單筆幅度普遍較小（最大改善 −0.00583 vs 最大退步 +0.00421），無系統性
faster→slower 翻轉（99/100 兩邊一致）。**這是本 session 目前找到的最佳新
結果，且跟 §8.48 的排程翻轉是疊加關係（v19 建立在已包含該修正的 v16 之
上），不是互斥選擇。**

**附帶發現（純 Dirichlet 的意外之喜）**：不需要任何 MIB 專屬訊號，純線長
調和延拓就讓 Vmib 從 55 降到 36（−35%）——MIB 群組成員通常電氣連接緊密，
精確線長解自然把它們拉近，這是 GiFt 式隨機訊號 + 濾波做不到的（隨機初始化
沒有這個「精確解」保證）。

**建議下一步**：把 bnd-only 配置（`ELECTRO_DIRICHLET_INIT=1`,
`ELECTRO_DIRICHLET_GRP_WEIGHT=0`, `ELECTRO_DIRICHLET_MIB_WEIGHT=0`,
`ELECTRO_DIRICHLET_BND_WEIGHT=2.0`）升格為新的預設候選（比照 §8.48 的
`electro_v16` 升格模式），並評估是否值得對 `bnd_weight` 本身做數值掃描
（目前只測過 0 vs 2.0 兩個點）。

---

## §8.53（2026-08-02）：slice_pack 的 cluster 虛擬超級方塊 —— Gemini 建議評估 + 負面實測結果

使用者貼了 Gemini 對本專案的「五個突破維度」建議。逐條核對現有程式碼後：①
（Lagrangian loss 入侵）、③（ML 起點預測）、④（多起點 portfolio）三點**都
已經是既有機制**（`analytical_place.py` 的 `lam_grp`/`lam_bnd`、
`ELECTRO_ML_INIT`、`ELECTRO_SLICE_ASPECTS` 等），被包裝成新建議端出來；
⑤（`/teamwork-preview`、`/grill-me`）是不存在的 slash command；**只有②
（cluster 圖論預打包 / virtual super-module）是真正沒做過、值得試的方向**。

**設計**：`slice_pack.py` 目前對 grouping 只有「軟」處理——用群心當排序主軸，
讓成員在序列中連續、有較高機率落入同一子樹（見該檔案自己的模組說明），不是
保證。新增 `cluster_virtualize.py`（`ELECTRO_SLICE_CLUSTER_VIRTUALIZE=1`，
opt-in）：

1. **收合**：資格判定為「乾淨」的 cluster（全體成員都是純軟方塊，無
   fixed/preplaced/boundary/MIB）collapse 成一個虛擬超級方塊（面積=成員
   總和，位置=成員面積加權群心），餵給外層遞迴——這個虛擬節點跟一般軟
   方塊走同一條「面積精確、長寬比隨區域彈性」的路徑，`_place_leaf` 完全
   不用改。
2. **展開**：外層遞迴結束後，虛擬方塊拿到的 `(x,y,w,h)`（面積精確等於
   成員總和）直接餵給**同一套** `_dissect()` guillotine 遞迴，重新對成員
   切一次——guillotine 對一個矩形的任意切割，切出來的每一塊必然跟至少
   一個手足share一條切線，整批必然連通，V_group=0 by construction，
   面積精確不需要額外修復。
3. 完全沒有修改 `slice_pack.py` 本體一行程式碼，純粹是外掛的
   reduce→呼叫既有函式→expand 三段式包裝，失敗就整個候選作廢（沿用該
   檔案自己的「任何一步不合法就回傳 None」紀律），跟一般 `slice_pack` 的
   結果一起丟進候選池（嚴格加法式）。

**實測（全 100-case，疊加在 v19 目前最佳預設 bnd-only Dirichlet 之上）**：

| 配置 | Neutral | Vgrp | Vmib | Vbnd |
|---|---:|---:|---:|---:|
| v19 預設（無 cluster 虛擬化） | 1.3776 | 143 | 51 | 174 |
| + cluster virtualize（不限制個數） | 1.3806（+0.22%） | **152**（+9） | 54 | 169 |
| + cluster virtualize（每案最多 1 個） | 1.3806（完全相同） | 152 | 54 | 169 |

**輕微負面，不採用**。診斷確認 28/100 案例有符合資格的乾淨 cluster（共 30 組、
164 個成員，不是稀少到可忽略），機制真的有起作用，但「每案最多虛擬化 1 個」
的修正**完全沒改變結果**——推翻了「多個虛擬 cluster 互相干擾」的假設（診斷
顯示 28 案中只有 2 案有 2 個以上符合資格的 cluster，cap 對其餘 26 案根本
不起作用，但退步散佈在遠比 2 案更廣的範圍）。

**真正的根因更根本**：把 N 個分散的成員換成 1 個集中點餵給外層遞迴，這件事
本身就會改變同一案例裡**其他所有方塊**（包括沒被虛擬化的 cluster）在排序/
分割時的決策——不需要兩個虛擬節點互相干擾，一個就足以擾動整棵遞迴樹的切法。
這是本 session 反覆出現的「共享決策路徑」耦合模式的第三種變體（§8.48/49 的
梯度預算、§8.50 的正規化 Laplacian 度數、這次是遞迴切割的拓樸決策序列）：
局部修好一處，經常在共享路徑上讓別處變差。

**真正的修法**（未實作，工程量明顯更大）：讓虛擬節點在 `_order`/`_wall_groups`
的排序決策裡仍然反映原始成員的分散位置（而不是塌縮成單一群心點），只在
`_place_leaf`/面積計算時當作原子實體——這需要動到 `slice_pack.py` 本體，
不再是純外掛包裝，暫不建議投入。

**可複用教訓**：驗證修法時要對「假設的根因」設計能真正證偽的對照組（這次
「cap 到 1」原本設計來測試多 cluster 互搶，結果乾淨地推翻了那個假設，指向
更根本的原因），而不是改了參數看分數變好就收工——這次改完分數沒變，反而
是更有資訊量的結果。

---
---
**回到**：[[ICCAD/ICCAD-Dashboard|ICCAD 儀表板]]




