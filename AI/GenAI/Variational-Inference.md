---
title: 變分推斷 (Variational Inference)
tags: [GenerativeAI, Math, Probability, Bayesian]
aliases: [Variational_Inference, Variational Inference, 變分推斷]
date: 2026-07-01
---

# 變分推斷 (Variational Inference)

> [!abstract] **一句話**
> 貝氏推論常常需要算一個「積分到爆炸、無解析解」的機率分佈（如 [[AI/GenAI/DDPM|DDPM]] 反向過程的邊際機率 $q(x_t)$）。變分推斷的策略是：**放棄求精確解，改成在一群「好算」的簡單分佈裡，找一個跟真實分佈最像的來當替身**。

## 1. 問題起源：後驗機率通常算不出來

貝氏定理給出後驗分佈：
$$ p(z \vert x) = \frac{p(x \vert z) p(z)}{p(x)}, \quad p(x) = \int p(x \vert z) p(z)\, dz $$

分母 $p(x)$（證據 / evidence）需要對所有可能的潛變數 $z$ 積分——當 $z$ 是高維連續變數（例如一張圖的潛在表徵），這個積分**沒有解析解，數值積分也算不動**。這正是 [[AI/GenAI/Markov-Chain-DDPM|DDPM 筆記第 4 節]]卡住的同一個問題。

## 2. 解法：用簡單分佈 $q_\phi(z)$ 去逼近

變分推斷引入一個帶參數 $\phi$、**故意選得很簡單**（通常是高斯）的分佈 $q_\phi(z \vert x)$，把「求後驗」轉換成「調整 $\phi$，讓 $q_\phi$ 盡量接近真實後驗 $p(z\vert x)$」的**最佳化問題**——從「積分」變成「[[Fundamentals/Optimization-Theory|梯度下降]]」，這是關鍵的範式轉換。

衡量「像不像」用 KL 散度：
$$ \phi^* = \arg\min_\phi D_{KL}\big(q_\phi(z\vert x) \,\|\, p(z\vert x)\big) $$

但這個 KL 散度本身還是含有算不出來的 $p(z\vert x)$。展開後可以證明它等價於**最大化證據下界 (Evidence Lower Bound, ELBO)**：

$$ \log p(x) \geq \text{ELBO} = \mathbb{E}_{q_\phi(z\vert x)}[\log p(x\vert z)] - D_{KL}\big(q_\phi(z\vert x) \,\|\, p(z)\big) $$

ELBO 完全由算得出來的量組成，且是 $\log p(x)$ 的下界——最大化 ELBO 就是在間接讓 $q_\phi$ 逼近真實後驗。

## 3. 這跟 DDPM 的關係

[[AI/GenAI/DDPM|DDPM 的 $L_{VLB}$]] 正是 ELBO 的一個特化版本：把 $z$ 換成整條擴散軌跡 $x_{1:T}$，把 $q_\phi$ 換成**固定不需訓練**的前向擴散過程 $q(x_{1:T}\vert x_0)$（一般 VAE 裡 $q_\phi$ 是要訓練的 Encoder，DDPM 的巧妙之處是把它設計成不需訓練、直接用高斯公式算的過程）。

## 4. 另一個經典應用：VAE

變分自編碼器 (Variational Autoencoder) 是變分推斷最著名的深度學習應用：Encoder 網路輸出 $q_\phi(z\vert x)$ 的均值/變異數，Decoder 網路是 $p_\theta(x\vert z)$，訓練目標就是最大化 ELBO——這也是為什麼 DDPM 論文常被拿來跟 VAE 類比：**兩者都是隱變數生成模型，都靠變分推斷/ELBO 訓練，差別在於 DDPM 的「隱變數」是一整條長度 $T$ 的馬可夫鏈，而不是 VAE 的單一潛向量**。

---
**相關筆記**：[[AI/GenAI/DDPM|DDPM]] · [[AI/GenAI/Markov-Chain-DDPM|馬可夫鏈與 DDPM]] · [[AI/GenAI/Diffusion-Model|Diffusion Model 總覽]] · [[Fundamentals/Optimization-Theory|最佳化理論]]
