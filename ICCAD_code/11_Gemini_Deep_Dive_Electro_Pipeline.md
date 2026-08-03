---
title: Gemini 深度學習套件 — 讀懂 Electro Pipeline 現行最強版本
tags: [ICCAD, Electro, Learning, Gemini]
date: 2026-08-04
---

# 11. Gemini 深度學習套件：electro_v19 pipeline

> **用途**：這篇不是講給 Claude 用的（Claude 有 repo 可以直接讀程式碼），是
> 給你自己拿去網頁版 Gemini（[gemini.google.com](https://gemini.google.com)，
> 建議用 2.5 Pro / Deep Research 模式，因為內容有數學跟長篇幅）**開一個新
> 對話、逐步問透**用的講義。Gemini 網頁版看不到你的 repo，所以第一則訊息
> 必須把「Part 1：架構簡報」整段貼過去當背景知識，之後才問得動。

## 需要準備什麼

1. 這篇文件（複製貼上用）。
2. 對照閱讀用（非必要，但深入時很有幫助）：
   [[ICCAD_code/Electronic_Pipeline|Pipeline 說明]]、
   [[Electronic_Pipeline.canvas|架構畫布]]——畫布是圖像化總覽，Gemini 網頁版
   吃不了 Obsidian Canvas 檔，但你可以自己對照著看，有疑問再回來問 Claude
   或截圖描述給 Gemini。
3. 一個新的 Gemini 對話（不要接著舊對話問，背景知識會被稀釋）。
4. （建議）跟 Gemini 說你要用**蘇格拉底式**學習——先讓它出小問題考你，
   你答錯再解釋，而不是一次把所有東西倒給你——這個學法比較容易真的記住，
   Part 2 的每個提示都已經照這個方式寫。

---

## Part 1：架構簡報（貼給 Gemini 當第一則訊息）

> 從這裡開始複製 ↓↓↓

我在做 ICCAD 2026 Contest C（FloorSet-Lite 電路佈局競賽）。目標是把 N 個
矩形方塊（20-120 個）放進一個平面，滿足：不重疊、部分方塊形狀/位置固定
（fixed/preplaced，硬約束）、部分方塊要求同一群組同形狀（MIB 群組）、
部分方塊要求貼齊外框某一邊（boundary code，bitmask：1=左 2=右 4=上
8=下），同時最小化線長（HPWL，質心到質心的曼哈頓距離）跟面積利用率的差距。
評分公式：

```
Cost = (1 + 0.5·(HPWL_gap + Area_gap)) · exp(2·V_rel) · max(0.7, RT^0.3)
```

`V_rel` 是三種軟約束（grouping、MIB 形狀、boundary）違規量的正規化總和，
`RT` 是跑比賽用的執行時間比例。我已經做出一個純 Python + PyTorch 的
pipeline（不用商用 EDA 工具、不用整數規劃求解器），全 100 案驗證分數
Neutral Total = 1.3776（100% 合法解）。我要你當我的老師，**用蘇格拉底式
教學**帶我讀懂這條 pipeline 用到的每一塊數學/演算法，一次一個主題，
先問我問題確認我的理解，而不是一次講完。

pipeline 的六個階段：

1. **初始化**：三選一但有優先序（Dirichlet 失敗才退回 ML，ML 失敗才退回
   random）。**Dirichlet 調和延拓初始化**是最新加的：把方塊之間的連線
   （b2b 網、pin 連線、群組同伴、boundary 對齊）都當成一個加權圖的邊，
   對每個座標軸求解 `min Σ w_ij·(x_i - x_j)^2`，其中已知位置的方塊
   （preplaced、以及虛擬的「牆」節點）是 Dirichlet 邊界條件（固定值），
   自由方塊的座標是要解的未知數——這是一個線性方程組的**封閉解**（沒有
   隨機性、沒有迭代），解出來的座標讓連在一起的方塊天生就互相靠近，
   比隨機初始化省掉大量後續梯度下降的功夫。

2. **解析式全域佈局**（`analytical_place`）：把每個方塊的中心座標當成
   PyTorch 可微參數，Adam 梯度下降 300 步，loss 是好幾項的加權和：重疊斥力
   `ov`、超出外框懲罰 `bb`、線長 `wl`、群組聚合 `grp`（cluster 內方塊的
   質心距離）、MIB 形狀引導 `mib_shape`（同群組方塊的 log 長寬比互相
   拉近）。這是連續放鬆（relaxation）——先假裝方塊可以重疊、可以有任意
   長寬比，用梯度下降找一個「軟」的好佈局，重疊等硬約束留到下一階段收拾。

3. **合法化**（`legalize`）：把上一步收斂但還有重疊的佈局，用確定性演算法
   壓成完全不重疊——先抽取一個相鄰順序，依序壓實，最後一輪增量式掃描保底
   清掉殘留重疊（`O(n²)` 降到 `O(|真的被動過的方塊|·n)`，這是一個真的靠
   profiling 抓出瓶頸、驗證正確性論證後才做的優化，不是憑感覺猜的）。

4. **切割式打包**（`slice_pack`，跟第 3 步平行的另一條路徑）：不是梯度式，
   是遞迴 guillotine 切割——把一個矩形依子樹面積比例切成兩半，遞迴下去。
   關鍵洞察：比賽規則對「軟方塊」只檢查面積誤差 ±1%，完全沒限制長寬比，
   所以「面積固定、形狀自由」的方塊拿來做面積比例切割，填充率理論上能
   逼近 100%。這條路徑失敗就整個放棄回傳 None，呼叫端沿用第 3 步的結果，
   所以它不可能讓答案變不合法，只可能讓答案變好。

5. **軟約束修復**（`soft_repair`）：沿著外框邊掃描貼齊該貼齊的方塊
   （boundary snap）、把該同群組的方塊互相推近（grouping push-past）。

6. **Portfolio 選擇**：整條 pipeline 其實不是只產生一個答案——同一個案例
   會平行跑好幾個隨機種子、每個種子又會產生「梯度式」跟「切割式（兩種
   切割方向）」等好幾份候選，外加一份用線性規劃（LP）做最小位移合法化
   的額外候選。所有候選丟進同一個池，用一個**跟候選池大小無關**的 proxy
   cost 排名（`exp(2·V_rel)·(hpwl_gap估計 + area_gap估計)`），每個案例
   獨立挑池裡最低分的送出。「跟候選池無關」很重要：如果用候選池的平均當
   排名基準，多加一個候選就會讓其他候選的相對排名跟著變動，那樣多養
   候選反而可能讓結果變差。

還有一個**這個 session 最重要的研究發現**，等你講到後面 ADMM/dual ascent
那段時我會再細問，先預告：我們發現只要兩個軟約束修復共用同一個底層計算
基質（同一份梯度預算、同一個圖度數正規化、同一個 bbox 相對參考框架），
修好一個就會傷到另一個。三個獨立測試過的「理論上更先進」的方向（L-BFGS
收尾、ADMM 變數分裂、Per-RMAP 交替投影）全部因為這個耦合而失敗，反而輸給
更簡單的對偶上升（dual ascent）機制。

先別急著解釋全部——**從第一個問題開始考我**：你覺得我對「為什麼要同時
維護解析式跟切割式兩條路徑」這件事的理解，可能漏了什麼？

> 複製到這裡結束 ↑↑↑

---

## Part 2：接下來的提問順序（一次丟一個，等 Gemini 教完再問下一個）

依照由淺入深排序，每個提示都刻意留空間讓 Gemini 反問你、出題考你：

1. **連續放鬆 (relaxation) 是什麼，為什麼佈局問題要先放鬆成可微分再收緊**
   > 「先考我：為什麼不能一開始就用整數規劃或離散演算法直接解，一定要先
   > 放鬆成連續可微分的版本？這樣做犧牲了什麼、換到了什麼？」

2. **Adam 優化器在這裡扮演的角色**
   > 「Adam 跟一般梯度下降差在哪？在這種『loss 是好幾項加權和、權重還會
   > 隨迭代次數改變』的場景下，為什麼 Adam 比較適合？」

3. **圖拉普拉斯（graph Laplacian）與調和延拓（harmonic extension）**
   > 「這是這條 pipeline 最新加的初始化機制背後的數學。考我：Dirichlet
   > 邊界條件是什麼意思？為什麼『已知位置的方塊當邊界條件、自由方塊解線性
   > 方程組』會產生一個『smooth』的初始佈局？這跟直接用隨機初始化比，
   > 理論上的好處是什麼、可能的壞處又是什麼？」
   （這個機制的原始論文：BADGE, Park & Paik, DATE 2026；DPlanner /
   "Hierarchical Graph Learning-Based Floorplanning With Dirichlet Boundary
   Conditions", Liu et al., IEEE TVLSI 2024——可以請 Gemini 直接查這兩篇，
   問它「這篇論文的核心方法是什麼，跟我描述的用法一致嗎」。）

4. **guillotine 切割 / slicing floorplan（Otten 1982）**
   > 「這是一個 1980 年代就有的組合最佳化表示法。考我：guillotine 切割
   > 為什麼保證每次切出來的都還是矩形？它能表示所有可能的矩形佈局嗎，
   > 還是有表達力上的限制？」

5. **合法化（legalization）與 O(n²) 到 O(k·n) 的增量式技巧**
   > 「這是一個經典的『兩個狀態沒變就不用重算』的攤還分析技巧。考我：
   > 什麼樣的正確性論證能保證『只重算被動過的方塊』不會漏掉新產生的重疊？」

6. **線性規劃（LP）式最小位移合法化**
   > 「這是拿 LP 解一個『把方塊移到不重疊的位置，但位移量要最小』的
   > 子問題。考我：這種問題怎麼寫成線性規劃的標準形式？跟前面的『壓實 +
   > 增量式 cleanup』比，各自的優缺點是什麼？」

7. **Proxy cost 與 portfolio 選擇（多起點 + 候選池排名）**
   > 「考我：為什麼『排名基準要跟候選池大小無關』這麼重要？如果我用池
   > 內平均當基準，具體會出什麼問題？能不能舉一個簡單的數字例子讓我
   > 直觀感受到。」

8. **對偶上升（dual ascent）與增廣拉格朗日 / ADMM 的差別**
   > 「這是我們這個 session 花最多力氣驗證的部分。考我：拉格朗日乘子法、
   > 對偶上升、增廣拉格朗日（ADMM 的核心）三者的關係是什麼？對偶上升只有
   > 線性項、ADMM 多了二次懲罰項，這個二次項理論上該帶來什麼好處？」

9. **為什麼理論上更先進的 ADMM 反而實測更差（這個 session 的核心教訓）**
   > 「我們的 ADMM 版本把邊界約束的懲罰項換成『線性對偶項 + 二次懲罰項』，
   > 20 案驗證卻是 Neutral 分數變差 8.1%，而且連完全沒被這個機制動到的
   > 其他軟約束項（grouping、MIB）都一起變差。考我：如果 ADMM 的變數分裂
   > 是『真正獨立』的子問題，為什麼還會發生『改 A 傷到 B』？我們的實作
   > 可能哪裡沒有做到教科書 ADMM 要求的『真正獨立』？」（提示：教科書 ADMM
   > 通常有獨立的 x-update、z-update、各自求解各自的子問題；我們的版本
   > 是不是其實還在同一個梯度下降迴圈裡，只是換了懲罰項的數學形式？）

10. **收尾：整條 pipeline 的哲學總結**
    > 「請你用一段話幫我總結：這條 pipeline 為什麼要『同時维護好幾種
    > 不同範式的候選生成方式（梯度式、切割式、LP式）+ 事後排名挑選』，
    > 而不是去找『一個最好的單一演算法』？這種設計哲學在最佳化領域有沒有
    > 通用的名字？」

---

## Part 3：Gemini 問到細節時可以貼的程式碼片段

Gemini 網頁版看不到你的 repo，如果它想看實際實作細節（例如問「你的 grp
loss 具體怎麼算」），可以回來這幾個檔案複製對應片段：

- Dirichlet 初始化完整實作：`collaborate/electro_v19/dirichlet_init.py`
- 解析式佈局主迴圈（loss 組成、MIB anchor 機制）：
  `collaborate/electro_v19/analytical_place.py`
- LP 式合法化：`collaborate/electro_v19/lp_legalize.py`
- 切割式打包：`collaborate/electro_v19/slice_pack.py`
- Portfolio 排名邏輯：`collaborate/electro_v19/electro_parallel.py`
  （搜尋 `anchors`、`_prerank`）
- 這個 session 的 ADMM 邊界一致性完整設計文件（含被拒絕的原因）：
  `docs/superpowers/specs/2026-08-03-admm-boundary-consensus-design.md`
  跟 `docs/superpowers/plans/2026-08-03-admm-boundary-consensus.md` 的
  Result 區段。

---

**相關筆記**：[[ICCAD_code/Electronic_Pipeline|Pipeline 說明]] ·
[[Electronic_Pipeline.canvas|架構畫布]] ·
[[ICCAD_code/8_Winning_Strategy_and_Roadmap|8. 奪冠策略總覽]] ·
[[ICCAD_code/9_Research_Tool_Workflow|9. 研究工具分工流程]]
**回到**：[[ICCAD/ICCAD-Dashboard|ICCAD 儀表板]]
