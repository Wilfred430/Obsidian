---
title: 機器學習總覽 (Machine Learning)
tags: [AI, Machine-Learning, MOC]
date: 2026-07-01
aliases: [Machine Learning, 機器學習, ML]
---

# 機器學習總覽 (Machine Learning)

> [!abstract] **一句話**
> 機器學習是「讓程式從資料中學到規律,而非由人手寫死規則」的方法總稱。這是本知識庫 AI 領域的樞紐頁 (MOC),也是理解 [[ICCAD/EDA-Paradigm-Shift|EDA 範式轉移]]——從人工調參的傳統演算法,轉向資料驅動設計——的核心。

## 1. 三大學習範式

| 範式 | 資料形式 | 學什麼 | 本庫相關筆記 |
|---|---|---|---|
| **監督式學習** | 輸入 + 標準答案 | 從標註資料學映射 | [[ICCAD_code/5_ML_Coordinate_Regression\|座標回歸]]、[[ICCAD_code/6_ML_Generative_BTree\|生成式 B*-tree]] 的預訓練 |
| **非監督式學習** | 只有輸入 | 找資料內在結構 | 分群、表徵學習 |
| **強化學習 (RL)** | 環境 + 獎勵訊號 | 從試誤中學策略 | [[ICCAD_code/8_Winning_Strategy_and_Roadmap\|Stage 1 獎勵微調]] |

## 2. 判別式 vs. 生成式（本專案的關鍵抉擇）

> [!info] 這組對比直接決定了 ICCAD ML 路線的成敗
> - **判別式 (Discriminative)**:學「輸入 → 輸出」的直接映射。[[ICCAD_code/5_ML_Coordinate_Regression|座標回歸模型]] 就是這類——直接預測每個模組的 $(x,y)$。問題:多峰解會 **mode collapse**(把兩個合法解平均成一個不合法解)。
> - **生成式 (Generative)**:學「如何一步步建構出一個完整解」。[[ICCAD_code/6_ML_Generative_BTree|生成式 B*-tree 模型]] 逐步生成拓樸,用 cross-entropy 訓練,天生避開 mode collapse。

這個從判別式轉向生成式的抉擇,是本專案 ML 路線最重要的轉折,詳見兩篇對應筆記。

## 3. 訓練的核心要素

- **損失函數 (Loss Function)**:衡量預測與答案的差距。回歸常用 MSE/Smooth-L1(但對多峰解會出事),分類/生成常用 Cross-Entropy。
- **梯度下降 (Gradient Descent)**:靠反向傳播算出的梯度,一步步調整參數讓 loss 下降。理論上與 [[Fundamentals/Optimization-Theory|連續最佳化]] 同源。
- **過擬合 (Overfitting)**:模型死背訓練資料、無法泛化。對策:更多資料、正則化、驗證集監控。本專案用大會的 100 萬筆訓練集正是為了泛化能力。

## 4. 本庫 AI 相關筆記導航

- [[AI/Transformer|Transformer 架構全解]]:本庫兩個 ICCAD 模型的共同基礎架構,含 Encoder-only/Decoder-only/Encoder-Decoder 三大家族對照。
- [[AI/Attention|注意力機制 (Attention)]]:Transformer 的核心組件——QKV、Multi-Head 的機制細節。
- [[AI/Markov-Chain|馬可夫鏈]]:MCMC 與 [[ICCAD_code/2_SA_Optimizer_Engine|模擬退火]] 的數學基礎。
- [[AI/LLM/Instruction-Tuning|指令微調]] · [[AI/LLM/PEFT-QLoRA|參數高效微調]]:大型語言模型的訓練技術。
- [[AI/GenAI/DDPM|擴散模型 DDPM]]:生成式模型的另一典範。
- [[AI/Data/Evaluation-Metrics|評估指標]] · [[AI/Data/Imbalance-Strategies|資料不平衡對策]]。

---
**相關筆記**：[[ICCAD/EDA-Paradigm-Shift|EDA 範式轉移]] · [[ICCAD_code/8_Winning_Strategy_and_Roadmap|奪冠策略]] · [[index|🌐 全域索引]]
