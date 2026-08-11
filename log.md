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

---
## [2026-07-09] Optimize | 品質項優化（HPWL 微調 + boundary portfolio）+ post-hoc 天花板
- **接續**: V_rel 修好後發現 cost 主導項換成品質項（area_gap +168%、hpwl_gap ~270%）。
- **HPWL 微調** (`eval_full.py::hpwl_nudge`): 自由方塊滑向 b2b/p2b 連線重心，只准
  移到不重疊且不撐大 bbox 的空位。100-case: **3.87 → 3.66**。
- **boundary push_past on/off portfolio**: boundary「推出界外」分支做成逐 case
  自選，很多 case 保持面積緊湊、接受該違規更便宜。100-case: **3.66 → 3.53**
  （代價：boundary 打包多一倍，34s→62s/case）。修正了 6.7「面積損失無法迴避」
  的過度概化——portfolio 顆粒度要夠細才測得出收益。
- **本 session 累計: 13.77 → 3.53（−74%），全程沒動模型權重**，純打包後處理。
- **Output**: 全記進 [[ICCAD_code/6_ML_Generative_BTree|6.8/6.9/6.10 節]]（含完整
  優化階段表）；新增 `ml/hpwl_nudge`、`_boundary_repair_pass(push_past=)` 開關。
- **天花板判斷**: 剩最大失血是大 case（n≈120）area_gap +130~220%——contour 打包
  多方塊的密度極限 + 底層拓樸品質（模型只訓 150k×3ep）共同造成，post-hoc 再加
  通道也解不了。要破 3.5 逼近電靜力法 2.84，只剩 (1) 訓練拓樸模型到收斂 或
  (2) by-construction/換更密 placer（pop 的 electro/M1 方向）。post-hoc 投報比
  已低。

---
## [2026-07-21] Optimize | electro legalizer 換成 LP 求解 + 官方 Alpha 排名差距診斷
- **觸發**: 官方 Alpha 正式排名曝光（Top5 Total Score 0.879-1.100，遠低於我們的
  2.1513，且不是靠 runtime 贏），追出 `legalize.py::_compact()` 是貪婪演算法
  （只保證不重疊，不管 HPWL/面積），非最優解。
- **修法**: 新增 `legalize_lp()`（LP 版壓縮，複用完全相同的順序抽取邏輯，避開了
  先前 sequence-pair 從零建構卡住的 preplaced anchor 矛盾），opt-in
  `ELECTRO_LEGALIZE_LP`。**完整 pipeline 驗證：2.1513 → 2.1230（−1.3%）**，已設
  為新預設。隔離測試（無 portfolio）曾測到 −31.0%，但現有 portfolio 機制（多
  seed/Jacobi/wideswap）已吸收掉大部分同樣的品質缺口，兩者高度重疊。
- **負面結果（已排除，避免重複踩）**: LP 目標函數換成純 HPWL 最小化（放棄位移
  正則化）讓佈局失去緊湊性，9 案加權變差 +37.2%；`ELECTRO_EDENSITY`（含 RePlAce-ld
  局部密度權重）跟既有 `lam_ov`/`lam_bb` 機制打架，9 案加權從 2.96 暴增到 5.7-8.4。
- **Deep Search 文獻查證**: TCG、UFO、"Placement Constraints in Floorplan
  Design"、QinFer 全部確認真實存在；但 DREAMPlace 3.0 的密度權重公式是編造的、
  AutoDMP 被誤植為 RL/MDP（實際是貝葉斯優化）。
- **Output**: 全記進 [[ICCAD_code/8x_Research_Log/2026-07_Research_Log|8.34 節（已搬到 2026-07 研究日誌）]]；
  `d:\ICCAD-2026-C\AI-deep-search\research_notes.md` 是完整查證紀錄。

## [2026-07-23] Refactor | 新增研究工具分工流程筆記 + 專案合作模式轉為蘇格拉底式導師
- **Context**: 使用者決定不再用 `/goal` 讓 Claude Code 自主跑實驗/改 production
  程式碼，改為引導式教學（先解釋邏輯、使用者自己動手、小測驗確認理解）。同時
  建立 `d:\ICCAD-2026-C\.claude\skills\floorplan-guard\` 專案級 skill，把 1% 面積
  容差、preplaced 不可移動等硬約束規則固化成 checklist。
- **Output**: 新增 [[ICCAD_code/9_Research_Tool_Workflow|9. 研究工具分工流程]]，
  整理 Claude Code / Antigravity / Gemini Deep Research / NotebookLM /
  Connected Papers 五個工具的分工與建議流程，並記錄「論文標題真實不代表引用的
  具體公式就是真的」這個查證教訓。

## [2026-07-28] Add | Claude Code Skills 指令參考 + Electro Pipeline 架構畫布
- **Context**: 使用者新裝了 `academic-research-skills` marketplace（`academic-paper`/
  `academic-paper-reviewer`/`academic-pipeline`），問這些 skill 對專題有何幫助；
  查證比賽規格 PDF 與 `SUBMISSION.md` 後確認**比賽本身不要求繳交論文**（純
  code+binary 送出），但這套工具對使用者「準備讀研究所、想把專題寫成論文」的
  個人目標仍有價值。
- **Output**: 新增 [[ICCAD_code/10_Claude_Code_Skills_Reference|10. Claude Code
  Skills 指令參考]]，依「遇到 bug / 推進論文 / 畫圖 / 存流程」等實際情境分類
  整理目前可用的 skill（含 academic-* 三件套、superpowers 工程紀律套件、
  dataviz、skill-creator 等），刻意排除跟本專題無關的 Airflow/SageMaker 類
  skill 保持精簡。
- **Add**: 新增 [[Electronic_Pipeline.canvas|Electronic_Pipeline 架構畫布]]（視覺化
  electro pipeline：初始化來源 → analytical_place → legalize → soft_repair →
  place-compact 回饋迴圈 → 困難案例才加開的 adaptive 候選 → proxy 排名 →
  portfolio 選擇）+ 對照的 [[Electronic_Pipeline|方塊說明 md]]。**使用者要求：
  往後 pipeline 有結構性改動（新增/移除階段、新增生產預設開啟的機制）時，
  同步更新這張畫布與說明文件**——已記錄為標準維護規則，寫入
  `Electronic_Pipeline.md` 開頭與 Claude Code 跨對話記憶。

**回到索引**：[[index|🌐 全域索引 >>]]

## [2026-07-29] Optimize | 發現 R 因子被忽略 + runtime 優化拿下真實分數 −12.2%
- **Context**: 隊友的 slice_pack 路線經三方驗證屬實（1.4480），取代我們自己的
  `electro_optimized/`（1.7279）成為新基準。本輪先驗證其真偽、再做切點偏好探索
  （完整否定），最後發現**全專題數週以來都在用「中性 RT」比較，完全忽略了真實
  Cost 公式裡的 `R = max(0.7, (本案runtime ÷ 該案跨隊中位數)^0.3)`**。
- **關鍵分析**: 我們的複雜度是 n^0.47、官方中位數 n^0.93（**我們成長慢一倍**）；
  加權占比極度集中（n≥96 占 87.6%，n=21-45 只占 0.17%）→ **「100/100 都快過
  中位數」是錯誤目標**。實機 cProfile 推翻兩個代碼閱讀的猜測（`_free()` 只占 1%
  不是瓶頸；真正浪費是 `_overlap_matrix` 被呼叫 5,315 次）。
- **Output**: 新增 `electro_v5/` 的增量式 `_cleanup`（`ELECTRO_FAST_CLEANUP=1`）
  ——**真實 Total 1.3173 → 1.1567（−12.2%）、快過中位數 43 → 95 案、品質逐位元
  不變**。過程中抓到一個順序依賴 bug（第一版座標差 9.33），教訓：「純粹重用計算」
  不足以保證等價，加速類改動必須逐位元驗證。
- **Update**: [[ICCAD_code/8_Winning_Strategy_and_Roadmap|第 8 篇]] §8.36-§8.39；
  **改寫** [[ICCAD_code/Electronic_Pipeline|Pipeline 說明]] 與
  [[Electronic_Pipeline.canvas|畫布]]（主力路線已換成 slice_pack，舊畫布描述的
  spectral/adaptive-spectral 已不在現行主力中；新增 slice_pack 節點——它貢獻
  −25.7% 卻一直不在圖上）；更新 [[ICCAD/ICCAD-Dashboard|Dashboard]] 現況
  （原本還停在 2026-07-01 的 Total 2.966）。

## [2026-08-04] Update | Electro pipeline 全面更新畫布：electro_v19 融合版現況 + 3 個負面研究方向收斂
- **Context**: 使用者要求依序把 RT 改善（L-BFGS 打磨）、Per-RMAP 可行性追尋、完整 ADMM 邊界一致性變數分裂這三個更大方向都試過一輪（不用 Antigravity，自主 subagent-driven-development，及時止損）。三個方向全部收斂到乾淨的負面結果。趁此機會把畫布/文件從舊版 electro_v5（slice_pack 單一路線）更新成現行真正的生產配置——electro_v19（slice_pack + electro_optimized 的 MIB anchor 血緣 + LP 位移候選的融合版），已經跟畫布上次更新（2026-07-29）時的架構有明顯落差（Jacobi 暖啟動已被 Dirichlet 調和延拓初始化取代、新增 MIB_ANCHOR/MIB_ANCHOR_SNAP、LP_DISPLACEMENT_PORTFOLIO、SLICE_ALIGN_PORTFOLIO 三個新機制）。
- **關鍵發現**: ADMM/L-BFGS/Per-RMAP 三者全部輸給更簡單的既有機制（對偶上升 boundary，`ELECTRO_DUAL_ASCENT_BND=1 K=40`，Neutral 1.3776→1.3691）。三個「更先進」方向失敗模式高度相似——不只目標項變差，連沒被直接動到的其他軟約束項也一起變差——因為它們都還是在同一個 `analytical_place()` 主迴圈裡跟其他 loss 項共用同一份梯度預算，只是換了懲罰項的數學包裝，不是真的把子問題拆成獨立變數。這是本 session 反覆驗證過的「共用梯度預算耦合」結構性發現，在第三種不同的數學形式下再次出現。
- **Output**: 全面改寫 [[ICCAD_code/Electronic_Pipeline|Pipeline 說明]]（新增 Dirichlet init、MIB anchor 兩段式機制、LP 位移候選、slice align portfolio 四個小節；新增「研究方向紀錄」表格）與 [[Electronic_Pipeline.canvas|畫布]]（重寫初始化鏈三個節點、Stage 1/2b 追加機制說明、新增 LP 位移候選節點、新增研究方向紀錄群組含 4 張卡片）。

## [2026-08-06] Refactor | 拆分過度肥大的 8. 奪冠策略總覽（228KB → 4.5KB + 月份研究日誌）
- **Context**: 使用者發現 [[ICCAD_code/8_Winning_Strategy_and_Roadmap|8. 奪冠策略總覽]] 已經膨脹到 228KB／3419 行（其他篇都是 4-12KB），因為原本只該放策略總覽的 §8.1-8.5（約 100 行）之後，被逐次實驗日誌（§8.6-§8.53，2026-07-09 到 08-02）一路往下加，違反 vault schema 的「原子化筆記」原則。
- **做法**: 用腳本依 §8.N 標題切開，按月份分桶（每個區塊的日期取自標題或內文，找不到就沿用前一區塊的月份），寫成新資料夾 `ICCAD_code/8x_Research_Log/` 底下的 `2026-07_Research_Log.md`（46 節，含已被 §8.7 取代的舊版 §8.6 stub）與 `2026-08_Research_Log.md`（7 節）；每個月份檔案開頭自動生成「本月做了哪些變更」目錄。切割前後對照 `## ` 標題總數（53）逐一核對，確認沒有內容遺失或重複。
- **Output**: `8_Winning_Strategy_and_Roadmap.md` 瘦身回 §8.1-8.5（策略本身）+ 一段指向兩篇月份日誌的索引；全 vault 掃描過 wikilink，確認沒有其他筆記連到 8 的特定章節錨點（`#8.34` 之類），只有本檔案自己一處用別名文字提到「8.34 節」，已改指到 2026-07 日誌並更新別名文字。

## [2026-08-06] Update | 全面同步 vault 到 electro_v19 現況 + 新增第 5 個研究方向紀錄
- **Context**: 使用者要求把 Obsidian 裡多處停留在舊版本（`electro_submission`／`electro_v5`，Total 2.966 或真實 1.1567）的筆記，換成目前最強、已驗證的版本（electro_v19，中性 1.3776／真實 0.9801）當基準繼續往下改。
- **做法**: [[ICCAD_code/7_Electrostatic_Placer|第 7 篇]]頂部現況欄位改成 electro_v19 數字，並修正一個因為第 8 篇拆分而失效的段落連結（原本指到「第 8 篇 §8.36-§8.39」，現在該內容已搬到 [[ICCAD_code/8x_Research_Log/2026-07_Research_Log|2026-07 研究日誌]]）；[[ICCAD_code/8_Winning_Strategy_and_Roadmap|第 8 篇]] §8.1 路線表與 §8.5 時間軸補上 2026-07-29 至今的里程碑（slice_pack 三方驗證、R 因子發現、electro_v19 定案、本輪 5 個研究方向的結論）。用 `electro-pipeline-canvas` skill 在畫布的「研究方向」群組新增第 5 張卡片：**合法化長寬比彈性**（`legalize_qinfer_reshape`）——Neutral 改善最多（-2.1%）但 REAL Total Score 幾乎打平（+12-14% runtime 抵銷掉品質增益），維持預設關閉；過程中意外揪出並修好一個 WSL-only 的浮點精度 bug（`config_75` 案在 WSL 上曾誤判為不可行）。
- **Output**: 畫布群組從 4 卡擴成 5 卡（群組高度 500→780，標籤日期範圍延到 08-06），[[ICCAD_code/Electronic_Pipeline|Pipeline 說明]]研究方向表格新增對應列＋標題更新為「3 負面 + 1 正面未預設 + 1 Neutral 佳但 REAL 打平」。全 vault 掃過還停在 2026-07-29 現況（`electro_v5`／真實 1.1567）的地方，發現 [[ICCAD-Dashboard|Dashboard]] 頂部現況欄位也是舊的（連對照 Alpha Top5 的名次都沒更新），一併換成 electro_v19 數字並修正同一個失效的「§8.38-§8.39」章節連結，改指到 2026-07 研究日誌。

## [2026-08-08] Add | 新增第 12 篇：electro_v19 數學深度教材（取代不可靠的 NotebookLM 報告）
- **Context**: 使用者拿 NotebookLM 生成的架構報告回來，內容有明確編造/矛盾的地方（不存在的重疊懲罰指數 δ=1.5、util 數字同一份報告兩處互相矛盾 0.98 vs 0.60、把 `legalize_qinfer_reshape` 誤植成「鎖死 V_rel=0」——實際驗證是 V_mib 變差、把 −2.1% 的分數改善錯誤歸因到另一個機制的 bug 修復）。使用者要求直接由我撰寫一份逐一對照真實程式碼驗證過的教材，目標是從大專生程度建立到有能力做研究的碩士生程度。
- **做法**: 直接重讀 `dirichlet_init.py`、`analytical_place.py`、`lp_legalize.py`、`slice_pack.py`、`electro_parallel.py` 的實際程式碼跟公式（不是轉述既有筆記），逐一撰寫並附上檔案位置佐證：電靜力類比與重疊懲罰真實公式、Dirichlet 調和延拓的線性方程式推導、log-aspect 參數化的面積不變性證明（含今天新機制為什麼要改用相對版本才不會有 bug）、MIB 兩段式引導的數學原因、guillotine 切割的四個限制條件、LP 合法化的標準形式、proxy cost 排名的 IIA 類比。第 9 節特別把「確認真實引用」跟「我自己的教學類比」明確分開標示，避免重蹈 NotebookLM 報告的覆轍。
- **Output**: 新增 [[ICCAD_code/12_Deep_Dive_Math_Study_Guide|12. electro_v19 深度教材]]，含 3 層次自我檢驗題組（複誦定義→證明推導→診斷設計）與現況驗證數字表。

## [2026-08-11] Update | RT/預設值調校 campaign：electro_v20 定案為主力 + 兩個機制轉正 + 全面同步 vault
- **Context**: 使用者要求「盡全力壓低 real total score 和減少 RT」，之後用 `/goal` 設成持續優化的 session 級目標。這輪不是發明新演算法，是重新量測既有設定 + 找回沒進生產的機制。過程中也發現使用者實際手動跑分的資料夾是 WSL home 下的 `~/FloorSet/iccad2026contest/`，跟 repo 裡的 `ICCAD-C-FloorSet-official/` 是兩個獨立副本，容易搞混（已修正並記住往後同步兩處）。
- **關鍵發現**：① `ELECTRO_PLACE_COMPACT_ITERS`（400→150）與 `ELECTRO_REPAIR_ROUNDS`（3→2）兩個舊預設是調過頭，不是取捨——新值品質更好還更快。② `ELECTRO_DUAL_ASCENT_BND` 早就在 8/6 驗證正面，但那段程式碼**只存在 `electro_v20`，`electro_v19`（當時的主力）完全沒有**——之前「已驗證但未預設」實際上是「不在生產版本裡，設了也無聲失效」。③ 把 8/4 因為 REAL 打平而停用的 `legalize_qinfer_reshape` 從 v19 移植到 v20、跟對偶上升疊加，同批次背靠背驗證：Neutral 1.3612→1.3260（-2.6%，本 session 至今最佳單一改善），REAL 不降反微升（1.0005→0.9987）——兩個機制疊加沒有互相拖累。④ 另外用參數掃描否決 9 個方向，其中 `ELECTRO_TARGET_UTIL` 確認是乾淨的 Q/P 對撞盤（填越滿 area_gap 越好但 V_grouping 越差，淨值全部比預設差），`ELECTRO_DA_BND_CEIL` 確認是死參數。完整證據見 repo 內 `docs/superpowers/2026-08-11-rt-and-default-retuning-campaign.md`。
- **視覺化額外發現**：用官方 `my_visualize.py` 畫出目前最好/最差各 5 案（存在 `~/FloorSet/iccad2026contest/images/{top_5,floor_5}/`）發現：即使是 ground truth 本身，同一個 cluster 也常被拆成不相連的幾坨——grouping 軟約束的定義本來就不要求物理相鄰；worst case（多集中在 n=82-91）同時出現邊界超出、MIB 沒統一、空白過多，三個症狀是同一個「共用梯度預算耦合」瓶頸的不同表現，不是三個獨立 bug。
- **Update**: [[ICCAD_code/7_Electrostatic_Placer|第 7 篇]]頂部現況欄位、[[ICCAD_code/8_Winning_Strategy_and_Roadmap|第 8 篇]] §8.1 路線表 + §8.5 時間軸、[[ICCAD-Dashboard|Dashboard]] 現況欄位全部換成 v20 數字（中性 1.3260／真實 0.9987）。[[ICCAD_code/Electronic_Pipeline|Pipeline 說明]]全面改寫：主力路線標記從 v19 換成 v20（並說明是安全超集，非另一條血緣）、成績表更新、旗標清單新增 5 個 8/11 新預設、研究方向表格把對偶上升 boundary 與合法化長寬比彈性兩張卡片的狀態從「驗證正面未預設」/「Neutral 佳 REAL 打平」改成「已納入生產預設」、新增一段完整記錄這輪 campaign 的 2 個確定改動 + 9 個否決方向。[[Electronic_Pipeline.canvas|畫布]]同步更新標題節點（v19→v20，補上 REAL 分數）與這兩張卡片的文字內容+顏色（node-075 從無色改成綠色標記已採用）。
- **Note**: `ICCAD_code/8x_Research_Log/` 月份日誌本輪未新增章節——這輪屬於「重新量測+找回機制」，記錄粒度落在 Pipeline 說明與畫布已足夠，沒有需要拆進月份日誌的獨立子實驗序列。
