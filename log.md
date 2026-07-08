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

## [2026-06-17] Ingest | ICCAD 2026 SA Optimizer 程式碼深度解析
- **Source**: `ICCAD_code/` (Python Wrapper + C++ SA Engine)
- **Output**: 產出四份結構化與圖表化 (Mermaid) 筆記：
    - [[ICCAD_code/1_Data_Loader_and_Wrapper|1_Data_Loader_and_Wrapper]] (資料介接與 ML 預熱)
    - [[ICCAD_code/2_SA_Optimizer_Engine|2_SA_Optimizer_Engine]] (核心退火算法與 B*-Tree)
    - [[ICCAD_code/3_Cost_Function_and_Penalty|3_Cost_Function_and_Penalty]] (成本計算與數學約束)
    - [[ICCAD_code/4_Packing_and_Evaluation|4_Packing_and_Evaluation]] (拓撲打包與座標推算)
- **Insight**: 成功將複雜的 C++ B*-Tree 操作與 Simulated Annealing 機制轉化為 PM 視角的架構圖，並透過 LaTeX 提取了 Cost Function 與動態 Penalty 的核心公式，為專題報告提供強大火力支援。

---
## [2026-07-01] Refactor + Ingest | ICCAD 實作筆記同步至 Beta 現況 + 生成式模型記錄
- **Source**: `collaborate/` repo 現況（Claude Code 讀取 `src/packer.cpp`、`ml/*.py`、`WINNING_STRATEGY.md`、CLAUDE.md 等）。
- **Action（更新既有筆記,補上 6 週的落差）**：
    - [[ICCAD_code/2_SA_Optimizer_Engine|2_SA_Optimizer_Engine]]：補上 M5 (MibSync)、M7 (FixGrouping，含雙向修正)、`always_accept` 不變量說明。
    - [[ICCAD_code/3_Cost_Function_and_Penalty|3_Cost_Function_and_Penalty]]：新增 3.4 節，釐清 SA 內部 cost 與**官方 contest cost 公式**（含 $e^n$ 總分加權）的差異——原筆記只講前者，容易被誤讀成兩者相同。
    - [[ICCAD_code/4_Packing_and_Evaluation|4_Packing_and_Evaluation]]：新增 4.4 節，補上打包後的四道確定性修復通道（`compact_left_down`/`bbox_balance_pass`/`holes_fill_pass`/`grouping_repair`/`boundary_repair`），原筆記完全沒提及。
- **Output（新增四篇原子筆記）**：
    - [[ICCAD_code/5_ML_Coordinate_Regression|5_ML_Coordinate_Regression]]：座標回歸模型架構 + **Mode Collapse 診斷**（多峰解被 MSE 平均成不合法解）。
    - [[ICCAD_code/6_ML_Generative_BTree|6_ML_Generative_BTree]]：`tree_sol` schema 解密（比對 `packer.cpp` 確認 direction bit 語義）、三個 Pointer Network 的生成式模型架構、150k 筆 GPU 訓練結果（`val_ptr_acc` 0.874）。
    - [[ICCAD_code/7_Electrostatic_Placer|7_Electrostatic_Placer]]：電靜力法（ePlace 典範），目前分數最佳（Total 2.966）。
    - [[ICCAD_code/8_Winning_Strategy_and_Roadmap|8_Winning_Strategy_and_Roadmap]]：三個關鍵診斷（$e^n$ 加權/搜尋空間/Mode Collapse）+ 四階段生成式管線 + 現況時間軸。
- **Cleanup**：刪除 `ICCAD_code/` 下過時的程式碼副本（`include/`、`ml/*.py`、`src/*.o`、`my_optimizer*.py` 等，含當初複製時一併帶進來的 `*Zone.Identifier` 垃圾檔）——這些檔案已與 repo 現況脫節超過一個月。**往後原則**：筆記只解說架構,不再複製程式碼進 vault,程式碼永遠只有 git repo 一份。
- **Update**: [[ICCAD/ICCAD-Dashboard|ICCAD Dashboard]] 新增「現況」callout 與「實作深潛」章節，連到全部 8 篇筆記。
- **Insight**: `tree_sol` 這個大會資料集裡的欄位被舊版 loader 標記 unused 直接丟棄，解密後發現是完整的 B*-tree 邊表——這是本次同步中最關鍵的一個發現，直接催生了整個生成式拓樸模型路線。

---
## [2026-07-01] Refactor | 全庫歸納整理：消滅散落根目錄檔與斷鏈
- **Action（清理舊 code 副本）**：刪除 `ICCAD_code/` 下過時的程式碼與文件副本（`include/`、`ml/`、`src/`、`my_optimizer*.py`、`README_iccad.md`、`SA_TUNING_GUIDE.md`、`START_HERE.md`、`SUBMISSION.md`），這些原是給 Gemini CLI 寫筆記用的暫存,現已無用。`ICCAD_code/` 現在只保留 8 篇原子概念筆記。
- **Action（歸納根目錄散落檔案,落實「所有檔案都在 folder 下」）**：
    - 新建 `Fundamentals/` 資料夾收納通用 CS/理論概念。
    - `Big-endian.md`（空）→ `Fundamentals/Big-endian.md`,補寫大小端完整知識,設 `Little-endian` alias（修復 DCS 的 `[[Little-endian]]` 斷鏈）。
    - `NVMe_SSD.md`（空）→ `Fundamentals/NVMe_SSD.md`,補寫 NVMe/SSD 完整知識。
    - `Markov_Chain.md`（空,根目錄）→ 刪除,因與已填實的 [[AI/Markov-Chain|AI/Markov-Chain.md]] 重複。
    - 根目錄現在只剩 `index.md` / `log.md` / `README.md` 三個基礎設施檔。
- **Output（補寫斷鏈目標,消滅「點進去空白」）**：
    - [[Fundamentals/Optimization-Theory|Optimization-Theory]]：組合最佳化、NP-hard、Metaheuristics、局部最佳陷阱——ICCAD SA/ML 路線的理論根基。
    - [[AI/Machine-Learning|Machine-Learning]]：ML 樞紐頁 (MOC),串起判別式 vs 生成式、三大學習範式與全庫 AI 筆記。
- **Fix（斷鏈修復）**：
    - `FloorSet-Detailed.md` 的 `[[Machine Learning]]` / `[[Optimization Theory]]` → 指向新筆記。
    - `EDA-Paradigm-Shift.md` 修正壞掉的四欄表格,移除 `[[FloorSet 挑戰賽]]` 斷鏈。
    - `Outline-Characteristics.md` 的 `[[ICCAD/Problem Overview]]`（已刪除的舊筆記）→ 重新指向 [[ICCAD/ICCAD-Dashboard|Dashboard]]。
- **Update**: `index.md` 新增「基礎概念 (Fundamentals)」章節,並在 AI 領域補上 ML 樞紐頁與馬可夫鏈連結。
- **Insight**: 全庫斷鏈盤點後,ICCAD + Fundamentals 範圍內已無「點了空白」的死連結。往後維護原則:筆記只解說架構,程式碼永遠只留在 `collaborate/` git repo,不再複製進 vault。

---
## [2026-07-01] Ingest | Transformer 架構全解（回應 user 對 Transformer 的興趣）
- **Source**: 使用者對 Transformer 表達興趣；結合《Attention Is All You Need》標準架構與本庫兩個 ICCAD 模型的實際原始碼（`ml/model.py`、`ml/model_tree.py`）。
- **Output**: [[AI/Transformer|Transformer 架構全解]]——涵蓋 Positional Encoding、Encoder/Decoder 堆疊、Masked Self-Attention、Cross-Attention、Add&Norm、FFN，以及 Encoder-only / Decoder-only / Encoder-Decoder 三大家族對照表。
- **Insight**: 兩個 ICCAD 模型剛好各代表一種家族——[[ICCAD_code/5_ML_Coordinate_Regression|`model.py`]] 是 Encoder-only（同 BERT），[[ICCAD_code/6_ML_Generative_BTree|`model_tree.py`]] 是 Encoder-Decoder 變體（把輸出層從詞彙表換成 Pointer Network）。用真實跑得動的程式碼具體對照教科書架構，比純理論筆記更扎實。
- **Linking**: 雙向連結 [[AI/Attention|Attention]]（细節互補，不重複 QKV 公式）、[[AI/Machine-Learning|Machine-Learning 樞紐頁]]、`ICCAD_code/5` 與 `6` 的架構段落，並更新 `index.md`。

---
## [2026-07-01] Ingest | Diffusion Model / U-Net 家族補完
- **Source**: 使用者對 UNet、Diffusion Model 表達興趣；既有 `AI/GenAI/DDPM.md` 與 `Markov-Chain-DDPM.md` 兩篇硬核數學筆記的「關聯網絡」全部是斷鏈（`生成式AI`/`Variational_Inference`/`U-Net`/`Langevin_Dynamics`/`Diffusion_Model` 均不存在）。
- **Output（5 篇新筆記）**：
    - [[AI/GenAI/UNet|UNet]]：架構圖解、skip connection 的必要性、時間步 FiLM 注入、現代 diffusion U-Net 混入 Attention block。
    - [[AI/GenAI/Diffusion-Model|Diffusion-Model]]：DDPM/DDIM/Score-SDE/Latent Diffusion 家族地圖 + **U-Net vs Transformer(DiT) 骨幹比較表**——這是 DDPM 與 [[AI/Transformer|Transformer]] 關係的正式落地處。
    - [[AI/GenAI/Variational-Inference|Variational-Inference]]：ELBO 推導，補上 DDPM 數學裡跳過的一步。
    - [[AI/GenAI/Langevin-Dynamics|Langevin-Dynamics]]：證明 DDPM 的雜訊預測本質是離散化朗之萬採樣，串起 Score-based 觀點。
    - [[AI/GenAI/GenAI-Overview|GenAI-Overview]]：`AI/GenAI/` 資料夾樞紐頁，修復 `生成式AI` 斷鏈。
- **Fix**: `DDPM.md`／`Markov-Chain-DDPM.md` 開頭與結尾的斷鏈全部改指向新筆記；`AI/Markov-Chain.md` 補 `Markov_Chain` alias。
- **Deferred**: `Markov-Chain.md` 自身較外圍的機率論斷鏈（`Probability_Theory`/`MCMC`/`Ergodic_Theory`/`Detailed_Balance`/`Bayes_Theorem`/`Stochastic_Process`）已在 `Markov-Chain-DDPM.md` 內用 callout 明確標註為「尚未建立」，非本次範圍。
- **Update**: `index.md` 新增「生成式 AI」章節，連到全部 7 篇 GenAI 筆記。
- **Insight**: DDPM（訓練框架）與 Transformer（骨幹架構）本是獨立技術，DiT 證明兩者可自由組合——這與本庫 [[ICCAD_code/6_ML_Generative_BTree|生成式 B*-tree 模型]]「把 Transformer 當萬用序列骨幹」是同一個故事的兩個案例。

---
## [2026-07-01] Ingest + Refactor | 補零基礎新手層 + 建立閱讀動線
- **Source**: 使用者反映「對這專案還不太熟」，並詢問既有筆記是否有助理解。診斷：
  `ICCAD_code/1-8`、`ICCAD/Problem/FloorSet-Summary`/`Detailed` 全部假設讀者已懂
  模組/網表/畫布/B*-tree 等基礎詞彙，直接切入細節，沒有「從零開始」的鋪陳層。
- **Output（2 篇新基礎筆記）**：
    - [[Fundamentals/VLSI-Floorplanning-101|VLSI-Floorplanning-101]]：真正的
      零基礎起點——晶片/模組/網表用城市/建築物比喻鋪陳，五種約束用生活比喻
      對照（冰箱/插座/一家人/雙胞胎/靠牆書架），B*-tree 用疊箱子比喻建立
      直覺，最後銜接到 Cost 評分與本庫三條路線現況。
    - [[Fundamentals/ICCAD-Glossary|ICCAD-Glossary]]：速查詞彙表，六大類
      （基本物件/約束/表示法演算法/評分公式/ML 相關）近 30 個術語，每個一句
      白話解釋 + 連到深入筆記，供讀技術筆記卡住時查閱。
- **Update**: [[ICCAD/ICCAD-Dashboard|ICCAD Dashboard]] 新增「🔰新手從這裡開始」
  callout，給出六步閱讀動線（PDF → 101 → 詞彙表 → FloorSet 規格 → 實作 1-8
  → 演算法/AI 深入）；`index.md` 的「基礎概念」章節同步補上兩篇新連結；
  [[ICCAD_code/1_Data_Loader_and_Wrapper|1_Data_Loader_and_Wrapper]] 與
  [[ICCAD/Problem/FloorSet-Summary|FloorSet-Summary]]（兩個系列的入口筆記）
  頂端各加一句新手提示，確保不是從 Dashboard 進來的讀者也能被導回正確順序。
- **Insight**: 「參考索引」式的知識庫組織（按主題分類）跟「新手引導」式組織
  （線性閱讀動線）是兩種不同需求，本庫原本只服務前者；本次新增的是後者，
  兩者並存而非取代——熟悉專案後仍應以 Dashboard 的主題分類快速查找。

---
## [2026-07-04] Ingest | 與 AI 協作的自律準則
- **Source**: 使用者在 ICCAD 專題進行到一半時，反思自己在專案裡幾乎所有
  技術決策都由 AI 做出，擔心長期下來喪失獨立判斷力，尤其目標是博士生，
  需要能形成並捍衛自己立場的能力。
- **Output**: [[Tool/AI-Collaboration-Discipline|AI-Collaboration-Discipline]]——
  給「自己」的自律準則（相對於 `Tool/LLM_Rules/` 是給 AI 的規則）。核心
  原則：不是「別用 AI」，是「別把判斷外包」；具體協定包含問前先寫猜測、
  答後自己重新解釋一遍、決策日誌格式、定期無 AI 時段、博班 quals 視角的
  提醒、自我檢查清單。
- **Insight**: 回顧本次對話實際紀錄，使用者並非「完全沒有判斷力」——多次
  展現質疑與驗證的本能（糾正 AI 對 GitHub repo 歸屬的誤判、要求對 Fable
  的說法做原始碼查證而非照單全收、抓出數學渲染錯誤）。真正的落差在於
  「判斷力大多用在檢查 AI 有沒有錯」而非「先形成自己的假設再看 AI 的
  答案」——後者才是這篇筆記要練的具體技能，範圍更精確也更可操作。

---
## [2026-07-04] Refactor | AI 協作自律準則補上難度分層
- **Source**: 使用者回饋原版建議「找 AI 之前先猜」校準錯難度——原本舉的例子
  （座標回歸 vs 生成式模型）是博班等級的判斷，大三生本來就猜不出來，逼自己
  硬猜只會挫折。
- **Update**: [[Tool/AI-Collaboration-Discipline|AI-Collaboration-Discipline]]
  新增 §0「先分層」——區分「前沿級」（需要特定文獻/經驗，坦然承認沒基礎
  即可，不用硬猜）與「構得到的」（用現有課堂知識能推理，這才是該練習先猜
  的地方）。並補上「猜不出來時改成標出卡住的具體點」的重新框架（半猜測 /
  精確定位知識邊界，比籠統的「我不會」有診斷價值），以及「不知所措是正常
  的，因為專題難度超出正常課綱進度」的明確安撫。自我檢查清單同步更新。
- **Insight**: 「先猜」這個習慣本身沒有錯，但必須先教會自己判斷「這個問題
  現在的我夠不夠格猜」，否則會把一個漸進式的能力養成過程，錯誤地當成
  「立刻該有全面判斷力」的一次性要求。

---
## [2026-07-05] Ingest | FloorSet 資料實例解析（真實數字版）
- **Source**: 使用者要求把 `ICCAD_code/1_Data_Loader_and_Wrapper` 裡「大會測資」
  的抽象方塊圖換成真實數字說明——實際打開驗證集 `config_21`（21 blocks）
  用 Python 解析 `blocks`/`b2b`/`p2b`/`pins_pos` tensor，挑具體 block 出來對照。
- **Output**: [[Fundamentals/FloorSet-Data-Worked-Example|FloorSet-Data-Worked-Example]]——
  用真實數字逐一對照：block 12（`area=270`，無任何約束的純 soft block）、
  block 6（`boundary_code=5`=左上角，並實測驗證真實解確實 `x_min=0`且
  `y_max=H`）、cluster_id=3 的分組（block 5/8/17，驗證出「不是每個都互貼，
  是靠 block 5 當橋樑連成一個連通分量」這個常被誤解的細節）、mib_id=1 的
  7 個 block（驗證全部 `w=18.0, h=26.0` 完全一致）、block 12 的 b2b/p2b
  權重與腳位座標。
- **Update**: 串連進 [[ICCAD_code/1_Data_Loader_and_Wrapper|1_Data_Loader_and_Wrapper]]、
  [[Fundamentals/VLSI-Floorplanning-101|VLSI-Floorplanning-101]]、
  [[Fundamentals/ICCAD-Glossary|ICCAD-Glossary]]、`index.md`、Dashboard 閱讀動線
  （新增步驟 2.5）。
- **Insight**: grouping 約束「不需要全員互貼、只需要整體連通」這個細節，
  用真實資料實測驗證後比純文字定義更容易記住——這也印證了[[Tool/AI-Collaboration-Discipline|
  自律準則]]裡「構得到的問題」這一類：讀懂 tensor 欄位定義、動手跑程式碼
  驗證約束是否滿足，是大三生現有能力範圍內就能做、且能建立真實判斷力的
  練習，不需要等到有博班程度的背景才能開始。

---
## [2026-07-05] Ingest | Input/Output 完整合約
- **Source**: 使用者反映對 ICCAD C 的 input 資料格式不清楚，要求說明資料存放
  位置、output 格式、以及對應的檔案。診斷後發現這其實牽涉三層容易混淆的
  「輸入」：(1) 原始資料集檔案 (2) contest 框架呼叫 `solve()` 的 API 格式
  (3) 本專案內部 `.txt`/`.sol` 中介格式——過去筆記沒有把這三層拆開講清楚。
- **Action（原始碼查證，不是憑印象寫）**：讀
  `iccad2026contest/iccad2026_evaluate.py::FloorplanOptimizer.solve()` 確認
  官方 API 簽名（`area_targets`/`constraints[N,5]`/`target_positions[N,4]`
  等，注意 `constraints` 少了 area 那一欄，跟原始 `blocks[N,6]` 不同形狀）；
  讀 `my_optimizer.py::_write_txt()`/`_parse_sol()` 確認 `.txt`/`.sol` 實際
  格式；一度誤寫 `.sol` 由 `main.cpp` 寫出，查證後訂正為
  `parser.cpp::save_solution()`（`main.cpp` 只是呼叫它）。
- **Output**: [[ICCAD_code/1b_Input_Output_Contract|1b_Input_Output_Contract]]——
  全局 Mermaid 流程圖（三層轉換一次看完）+ 各層對照表 + `.txt`/`.sol` 真實
  格式範例 + `FLOORPLANNER_KEEP=1` 環境變數教怎麼自己打開中介檔案看。
- **Update**: 串連進 [[ICCAD_code/1_Data_Loader_and_Wrapper|1_Data_Loader_and_Wrapper]]、
  Dashboard 實作深潛清單（新增「1b」）。
- **Insight**: 「三層輸入格式」的混淆是很典型的新手困惑點——原始檔案格式、
  框架 API、專案內部格式三者形狀相近但不相同（尤其 area 欄位的位置），
  沒有人指出來很容易誤以為是同一份東西。

---
## [2026-07-08] Refactor + Ingest | 補齊 packer 修復管線，推翻上一輪悲觀結論
- **Source**: `/goal` 設定「持續優化專題，找更好的 strategy 降低 cost，值得更新就記
  進 Obsidian」的 session 目標。延續上一輪 100-case 實測（`ml/pack_tree.py` 只有
  `compact_left_down`，area_gap +125%、Total 13.77）與 pop 的 M1 文件警告
  （「contour 規則無法重現 GT 咬合拼磚」），著手驗證這是否為 contour 表示法的
  結構性死路，還是修復管線本身沒補齊。
- **Action（同時修正 contest_cost.py 的一個正確性 bug）**：讀 spec PDF 截圖確認
  官方 `compute_cost` 第 322 行對每個 gap 做 `max(0,·)` clamp（贏過 baseline
  完全不加分，Q 恆 ≥1，0.7 是真地板）——`ml/contest_cost.py` 原本漏了這個
  clamp，已補上；同步訂正 `WINNING_STRATEGY.md`/`FABLE_BRIEF_cost0.7.md`
  裡「贏過 baseline 可壓破 0.7」的錯誤說法。
- **Action（移植 packer.cpp 剩餘的修復通道到 `ml/pack_tree.py`）**：新增
  `_bbox_balance_pass`（修長條狀 bbox）、`_holes_fill_pass`（補 L 形死空白）、
  `_grouping_repair_pass`、`_boundary_repair_pass`，忠實照抄
  [[ICCAD_code/4_Packing_and_Evaluation|`src/packer.cpp`]] 的演算法邏輯。
- **新增 `ml/eval_full.py`**：全 100-case A/B 評估工具，soft block 長寬做全域
  aspect ratio 掃描（含正方形選項，保證優化後不劣於優化前），用真實 Cost 公式
  排名。
- **實測結果（100-case，e^(n/12) 加權 Total Score）**：

  | 修復管線 | 平均 area_gap | Total Score |
  |---|---|---|
  | 只有 `compact_left_down` | +125% | 13.77 → 12.40（形狀優化 −9.9%） |
  | **+ `bbox_balance` + `holes_fill`** | **+24.9%** | **8.41 → 7.77（−39% vs 原本）** |

  加上最後兩道後 `area_gap` 從 +25% 漲回 +63%（拉去貼群組/邊界重新打開一些
  空隙），但 Cost 仍大降——因為 $\exp(2V_{rel})$ 是指數項，犧牲一點面積換
  $V_{rel}$ 大降是淨賺。**總計只靠移植 C++ 早就有的修復通道（沒動任何模型
  權重），Total Score 降了 62.7%。**
- **Insight（最重要的一條）**：上一輪的「contour 打包有結構性密度天花板」是
  **下得太早的結論**——只用了四道修復通道中的一道（`compact_left_down`）就
  判定整個表示法不行，沒有先排除「修復管線不完整」這個變因。補齊其中兩道，
  area_gap 就掉了 5 倍。這是[[Tool/AI-Collaboration-Discipline|自律準則]]
  「先猜再驗證，猜錯了就訂正」的活教材——上次的悲觀結論已經在
  [[ICCAD_code/6_ML_Generative_BTree|6.6 節]]留下訂正紀錄，而不是默默改掉
  假裝沒發生過。

---
## [2026-07-09] Optimize | 攻 V_rel：MIB 歸零 + boundary 大降，並確認 post-hoc 修復到頂
- **Source**: `/goal` 持續優化 + 使用者指定目標「feasible + V_rel=0，壓最低 cost」。
- **方法（先診斷再對症）**: 寫 `ml/diag_vrel.py` 逐 case 拆解軟約束違規來源，
  發現 boundary 佔 74%（141/191），先打它。
- **戰果**:
    - **MIB 9→0（by construction）**: 修好 `dims_with_aspect` 的 bug——MIB 群組
      soft 成員被 aspect 掃成跟群組 fixed 成員不同形狀。強制跟隨後歸零。
    - **boundary 141→~12**: 強化 `_boundary_repair_pass`（沿牆掃描找空位，
      LEFT/BOTTOM 保證貼到）。
    - **Total Score 4.67→3.87**（100-case，e^(n/12) 加權；3.91 是未加最終
      壓實回收的版本，加了 final-compact 回收面積後定案 3.87），本 session 累計
      13.77→3.87（−72%），全程沒動模型權重。
- **代價與邊界結論**: 強力 boundary 把內部方塊抬到邊界，area_gap 從 +63% 爆到
  +168%。但 portfolio 測試（同時打包面積優先版讓 cost 逐 case 選）證明面積優先版
  幾乎從不勝出——即 area 損失在 cost 上是「正確定價」的（$\exp(2V_{rel})$ 指數項
  主導）。grouping 用 union-find 聚集也卡在每 case ~4–5 降不下去（密集佈局沒空間
  聚集）。**兩者都指向同一結論：post-hoc 修復已到頂，再往下的 ceiling-breaker 是
  by-construction 約束感知擺放（super-block grouping、boundary-feasible 拓樸），
  正是 pop 的 electro/M1 在做的方向。**
- **Output**: 全部記進 [[ICCAD_code/6_ML_Generative_BTree|6.6/6.7 節]]、
  `WINNING_STRATEGY.md` T7/T8；新增 `ml/diag_vrel.py`、`ml/eval_full.py`。
- **Insight**: 「先量化違規來源再對症下藥」比亂修有效率得多（一眼看出 boundary
  是主力）；但也踩到「追個別違規數（雜訊指標）容易陷入打地鼠」的坑——真正的
  裁判是 100-case Total Score。誠實記錄了 union-find grouping 強化反而讓數字變差
  的失敗嘗試，沒有默默改掉。

**回到索引**：[[index|🌐 全域索引 >>]]
