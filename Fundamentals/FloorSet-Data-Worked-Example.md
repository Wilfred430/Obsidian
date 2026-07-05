---
title: FloorSet 資料實例解析：用真實數字看懂 Area / Constraint / B2B / P2B
tags: [Fundamentals, EDA, VLSI, Floorplanning, Beginner, Worked-Example]
date: 2026-07-05
aliases: [資料實例解析, Tensor Walkthrough]
---

# FloorSet 資料實例解析：用真實數字看懂 Area / Constraint / B2B / P2B

> [!abstract] **這篇筆記回答什麼**
> [[Fundamentals/VLSI-Floorplanning-101|VLSI Floorplanning 入門]]跟 [[ICCAD_code/1_Data_Loader_and_Wrapper|1. Data Loader 筆記]]講的是「資料長什麼形狀」（tensor 的維度、欄位定義），這篇是**打開一個真實的驗證集案例，把每個欄位換成具體數字**——看完會知道 `area=270` 實際上是什麼意思、一個真正的 boundary/grouping/MIB 約束長怎樣、b2b/p2b 的權重數字代表什麼。

> [!info] **資料來源**：`ICCAD-C-FloorSet-official/LiteTensorDataTest/config_21/`——驗證集裡最小的案例，21 個 block，方便一次看完全部。對應 [[ICCAD_code/1_Data_Loader_and_Wrapper|Pipeline 圖]]裡最左邊「大會測資」那三個方塊的真實內容。

## 1. `blocks` tensor：21 個模組，每個模組 6 個數字

每一列格式是 `[area, is_fixed, is_preplaced, mib_id, cluster_id, boundary_code]`，實際跑出來的前幾列：

| block | area | is_fixed | is_preplaced | mib_id | cluster_id | boundary_code |
|---|---|---|---|---|---|---|
| 3 | 522.0 | 0 | 0 | 0 | 0 | 0 |
| 6 | 364.0 | 0 | 0 | 0 | 0 | **5** |
| 12 | 270.0 | 0 | 0 | 0 | 0 | 0 |
| 15 | 468.0 | **1** | 0 | 1 | 1 | 0 |
| 17 | 468.0 | 0 | **1** | 1 | 3 | 8 |
| 18 | 468.0 | **1** | 0 | 1 | 0 | 1 |

`mib_id=0` / `cluster_id=0` / `boundary_code=0` 代表「沒有這項約束」——不是 id 為 0 的群組，是「不屬於任何群組」。

## 2. Area（面積）：block 12，一個最單純的例子

**Block 12：`area=270.0`，其餘全 0**——這是全案例裡最乾淨的一個模組：沒有固定形狀、沒有預先擺放、不屬於任何 MIB/群組、不用貼邊界。它是純粹的 **soft block**：只規定「面積要是 270」，長寬比自己選，唯一的限制是 [[ICCAD_code/3_Cost_Function_and_Penalty|實作出來的 w×h 跟 270 的誤差不能超過 1%]]。

## 3. Constraint（約束）：每一種都挑一個真實模組來看

### 3.1 Fixed-shape（固定形狀）——block 15 / block 18
`is_fixed=1`：長寬鎖死，不能改。這兩個 block 剛好都在 MIB 群組裡（下面會解釋為什麼這不衝突）。

### 3.2 Preplaced（預先擺放）——block 17
`is_preplaced=1`。查真實解的座標：**x=[71, 89]，y=[0, 26]**——這個位置是**固定死的輸入值**，不是演算法算出來的，任何偏差都是硬違規。

### 3.3 Boundary（邊界）——block 6，`boundary_code=5`
`5 = 1(左) + 4(上)`，代表「必須貼左上角」。查真實解：block 6 的座標是 **x=[0, 28]，y=[52, 65]**，而整個畫布大小是 **W=107, H=65**。驗證一下：`x_min=0` ✓ 貼到左邊；`y_max=65=H` ✓ 貼到頂邊。**真實解真的完美滿足這個約束**——這不是巧合，是因為 baseline 本身就是（近似）最優解，見 [[ICCAD_code/3_Cost_Function_and_Penalty|Cost Function 筆記]]對 baseline 的說明。

### 3.4 Grouping（分組聚集）——cluster_id=3：block 5、8、17
三個 block 同屬 cluster 3。查真實解的相鄰關係：

| | block 5 | block 8 | block 17 |
|---|---|---|---|
| **block 5** | — | 貼邊 ✅ | 貼邊 ✅ |
| **block 8** | 貼邊 ✅ | — | **沒貼邊** ❌ |
| **block 17** | 貼邊 ✅ | **沒貼邊** ❌ | — |

> [!success] **重點：grouping 不是「每個都要互相貼」，是「整體連成一塊」**
> block 8 跟 17 彼此沒有貼在一起，但透過 block 5 當「橋樑」，三個 block 仍然形成**一個連通分量**——這就合法。[[ICCAD_code/3_Cost_Function_and_Penalty|`V_group` 的定義]]是「連通分量數 − 1」，這裡連通分量數=1，所以 `V_group=0`。如果 block 8 自己孤立在別處沒有連到任何同群夥伴，連通分量數就會變成 2，`V_group=1`，觸發指數懲罰。

### 3.5 MIB（同型模組）——mib_id=1：7 個 block 共 7 份，尺寸完全一致
`{1, 10, 13, 14, 15, 17, 18}` 全部 `area=468`，而且查真實解的實際長寬：**全部都是 `w=18.0, h=26.0`，一模一樣**。这印证了 [[Fundamentals/VLSI-Floorplanning-101|入門筆記]]的比喻——雙胞胎的房間裝潢必須完全一致。（順便注意：block 15/18 的 `is_fixed=1` 剛好也在這個 MIB 群組裡——固定形狀 跟 MIB 共享形狀，兩個約束同時套用在同一個 block 上完全合理，不衝突。）

## 4. B2B（block-to-block 連線）：block 12 的真實連線

Block 12 有 8 條 b2b 連線：

| 連到 block | weight |
|---|---|
| 9 | **0.0052**（最強） |
| 4 | 0.0029 |
| 19 | 0.0029 |
| 3 | 0.0023（×2，兩條獨立網路都連這兩個 block） |
| 5 | 0.0017 |
| 16 | 0.0017 |
| 1 | 0.0006（最弱） |

`weight` 代表這條「管線」的重要程度——越大代表這兩個 block 之間的訊號流量/重要性越高，[[ICCAD_code/3_Cost_Function_and_Penalty|算 HPWL 時]]會拿這個權重乘上兩個 block 中心點的曼哈頓距離。block 12 跟 block 9 之間權重最高（0.0052），代表**佈局時應該優先讓這兩個 block 靠近**，其次才是跟 block 4、19 這些權重較低的鄰居。

## 5. P2B（pin-to-block 連線）：block 12 連到外部腳位

Block 12 有 4 條 p2b 連線，全部指向畫布邊界上的固定腳位：

| pin id | pin 座標 | weight |
|---|---|---|
| 0 | (58.0, 68.0) | 0.0006 |
| 7 | (69.0, 68.0) | 0.0006 |
| 14 | (63.0, 68.0) | 0.0006 |
| 22 | (65.0, 68.0) | 0.0006 |

注意這四個 pin 的 y 座標都是 68——它們是**畫布同一條邊上**的固定對外聯絡點（見 [[Fundamentals/VLSI-Floorplanning-101|入門筆記]]的「機場/港口」比喻）。跟 b2b 一樣，`weight` 會乘上「block 12 中心點到這個 pin 座標」的距離，加進 `HPWL_ext`。

## 6. 串起來：這些數字最後怎麼變成一個分數

1. 演算法要決定 21 個 block 各自的 `(x, y, w, h)`。
2. 上面看到的每一種約束都要滿足（硬約束不能違反，軟約束違反會扣分）。
3. 用這些 b2b/p2b 的 weight 乘上距離，加總成 HPWL。
4. 面積用整個 bbox（這個案例真實解是 `107 × 65`）。
5. 全部餵進 [[ICCAD_code/3_Cost_Function_and_Penalty|Cost 公式]]，得到這個 case 的分數。

**這就是 [[ICCAD_code/1_Data_Loader_and_Wrapper|Pipeline 圖]]裡「大會測資」三個方塊的真實內容**——現在回頭看那張圖，`Block Area`/`Constraints`/`Netlist B2B/P2B` 應該不再是抽象名詞了。

---
**相關筆記**：[[Fundamentals/VLSI-Floorplanning-101|VLSI Floorplanning 入門]] · [[Fundamentals/ICCAD-Glossary|速查詞彙表]] · [[ICCAD_code/1_Data_Loader_and_Wrapper|1. Data Loader 與 Python 封裝架構]] · [[ICCAD_code/3_Cost_Function_and_Penalty|3. Cost Function 與動態懲罰機制]] · [[ICCAD/ICCAD-Dashboard|回到 Dashboard]]
