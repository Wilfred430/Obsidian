---
tags: [GenerativeAI, DeepLearning, ComputerVision, Diffusion]
aliases: [DDPM, Denoising Diffusion Probabilistic Model, 去噪擴散概率模型]
---

> 關聯網絡： [[AI/GenAI/GenAI-Overview|生成式AI]] | [[AI/Markov-Chain|Markov_Chain]] | [[AI/GenAI/Variational-Inference|Variational_Inference]] | [[AI/GenAI/UNet|U-Net]] | [[AI/GenAI/Langevin-Dynamics|Langevin_Dynamics]] | [[AI/GenAI/Diffusion-Model|Diffusion Model 總覽]]

# 去噪擴散概率模型 (DDPM)

去噪擴散概率模型 (Denoising Diffusion Probabilistic Models, DDPM) 是一種基於非平衡熱力學與馬可夫鏈 (Markov Chain) 的參數化生成式潛量變數模型 (Latent Variable Model)。其核心思想是透過一個固定的前向過程 (Forward Process) 逐步向數據中添加高斯雜訊，直到數據完全破壞成為純高斯雜訊；隨後，訓練一個類神經網絡來學習其反向過程 (Reverse Process)，從純雜訊中逐步去噪，最終重建出符合原始數據分佈的高維度樣本。

---

## 圖模型符號與變量辭典

為了嚴謹描述馬可夫鏈的狀態轉移與機率圖模型，以下定義 DDPM 數學推導中的核心符號：

| 符號 / 變量 | 嚴謹定義 | 空間維度與物理意義 |
| :--- | :--- | :--- |
| $x_0$ | 原始無雜訊數據 (Original Data) | $x_0 \sim q(x_0)$，通常為 $\mathbb{R}^{C \times H \times W}$ 的影像張量。 |
| $x_t$ | 第 $t$ 時間步的潛變數 (Latent Variable) | 與 $x_0$ 維度相同 ($\mathbb{R}^{C \times H \times W}$)，代表被添加部分雜訊的狀態。 |
| $x_T$ | 最終時間步的潛變數 | 漸近於標準常態分佈 $\mathcal{N}(0, \mathbf{I})$ 的純雜訊。 |
| $t$ | 時間步 (Time Step) | 離散整數 $t \in \{1, 2, \dots, T\}$，通常 $T = 1000$。 |
| $q(x_t \vert x_{t-1})$ | 前向轉移機率分佈 (Forward Transition) | 固定參數的高斯轉移核 (Gaussian Transition Kernel)。 |
| $p_\theta(x_{t-1} \vert x_t)$ | 反向轉移機率分佈 (Reverse Transition) | 參數為 $\theta$ 的神經網絡近似的反向去噪轉移核。 |
| $\beta_t$ | 雜訊變異數表 (Variance Schedule) | 隨時間步遞增的微小正數 $\beta_1 < \beta_2 < \dots < \beta_T$。 |
| $\alpha_t$ | 訊號保留率 | 定義為 $\alpha_t = 1 - \beta_t$。 |
| $\bar{\alpha}_t$ | 累積訊號保留率 | 定義為 $\bar{\alpha}_t = \prod_{i=1}^t \alpha_i$。 |
| $\epsilon$ | 標準高斯雜訊 | $\epsilon \sim \mathcal{N}(0, \mathbf{I})$，用於重參數化。 |
| $\epsilon_\theta(x_t, t)$ | 雜訊預測網絡 | 參數為 $\theta$ 的模型 (通常為 U-Net)，預測 $x_t$ 中蘊含的雜訊。 |

---

## 核心機制

DDPM 的運作機制可嚴格拆解為兩個互相對應的馬可夫過程：前向加噪與反向去噪。

### 1. 前向過程 (Forward Process / Diffusion Process)
前向過程被定義為一個固定不需訓練的馬可夫鏈。我們從數據分佈 $x_0 \sim q(x_0)$ 採樣，並在每個時間步 $t$ 加入微小的高斯雜訊。轉移機率矩陣定義如下：

$$ q(x_t \vert x_{t-1}) = \mathcal{N}(x_t; \sqrt{1 - \beta_t} x_{t-1}, \beta_t \mathbf{I}) $$

這意味著 $x_t$ 是以 $\sqrt{1 - \beta_t} x_{t-1}$ 為平均值、$\beta_t \mathbf{I}$ 為變異數的高斯分佈。給定初始數據 $x_0$，整個前向軌跡的聯合機率為：

$$ q(x_{1:T} \vert x_0) = \prod_{t=1}^T q(x_t \vert x_{t-1}) $$

### 2. 反向過程 (Reverse Process / Denoising Process)
反向過程的目標是從純高斯雜訊 $x_T \sim \mathcal{N}(0, \mathbf{I})$ 開始，逐步去噪還原出 $x_0$。根據貝氏定理，若 $\beta_t$ 夠小，反向轉移 $q(x_{t-1} \vert x_t)$ 亦會近似於高斯分佈。由於我們無法直接獲取全局分佈，我們使用神經網絡 (參數化模型 $p_\theta$) 來近似這個反向馬可夫鏈：

$$ p_\theta(x_{t-1} \vert x_t) = \mathcal{N}(x_{t-1}; \mu_\theta(x_t, t), \Sigma_\theta(x_t, t)) $$

反向軌跡的聯合機率為：

$$ p_\theta(x_{0:T}) = p(x_T) \prod_{t=1}^T p_\theta(x_{t-1} \vert x_t) $$

---

## 重建公式與重參數化技巧

### 前向過程的邊際分佈與重參數化 (Reparameterization Trick)
為了在訓練時不必逐層執行馬可夫鏈，我們利用高斯分佈的可加性，直接推導出任意時間步 $t$ 給定 $x_0$ 的條件分佈 $q(x_t \vert x_0)$。

令 $\alpha_t = 1 - \beta_t$，且 $\bar{\alpha}_t = \prod_{i=1}^t \alpha_i$。我們可將單步轉移展開為連續遞迴：

$$ 
\begin{align}
x_t &= \sqrt{\alpha_t} x_{t-1} + \sqrt{1 - \alpha_t} \epsilon_{t-1} \\
&= \sqrt{\alpha_t} (\sqrt{\alpha_{t-1}} x_{t-2} + \sqrt{1 - \alpha_{t-1}} \epsilon_{t-2}) + \sqrt{1 - \alpha_t} \epsilon_{t-1} \\
&= \sqrt{\alpha_t \alpha_{t-1}} x_{t-2} + \sqrt{\alpha_t (1 - \alpha_{t-1}) + (1 - \alpha_t)} \bar{\epsilon}_{t-2} \\
&= \dots \\
&= \sqrt{\bar{\alpha}_t} x_0 + \sqrt{1 - \bar{\alpha}_t} \epsilon
\end{align}
$$

其中 $\epsilon, \epsilon_{t-1}, \epsilon_{t-2} \sim \mathcal{N}(0, \mathbf{I})$。這推導出了一個極度優雅的解析解，使我們能以單一公式直接從 $x_0$ 採樣出任意深度的 $x_t$：

$$ q(x_t \vert x_0) = \mathcal{N}(x_t; \sqrt{\bar{\alpha}_t} x_0, (1 - \bar{\alpha}_t)\mathbf{I}) $$

### 變分下界 (Variational Lower Bound, VLB) 與目標函數
DDPM 透過最大化對數概似函數的變分下界 (Evidence Lower Bound, ELBO) 來訓練模型：

$$ \mathbb{E}[-\log p_\theta(x_0)] \leq L_{VLB} = \mathbb{E}_q \left[ D_{KL}(q(x_T \vert x_0) \parallel p(x_T)) + \sum_{t=2}^T D_{KL}(q(x_{t-1} \vert x_t, x_0) \parallel p_\theta(x_{t-1} \vert x_t)) - \log p_\theta(x_0 \vert x_1) \right] $$

> [!IMPORTANT] VLB 的物理意義與優化盲點
> 在上述 $L_{VLB}$ 公式中，第一項 $L_T$ 由於前向過程是固定的且沒有可訓練參數，因此為常數。真正的核心在於第二項 $L_{t-1}$，它要求網絡預測的轉移分佈 $p_\theta(x_{t-1} \vert x_t)$ 必須盡可能貼近真實後驗分佈 $q(x_{t-1} \vert x_t, x_0)$。
> 理論上我們應直接預測均值 $\mu_\theta$，但實證指出，這種做法在訓練初期極度不穩定。

### 損失函數簡化 (Simplified Loss $L_{simple}$)
透過貝氏定理與重參數化展開 $q(x_{t-1} \vert x_t, x_0)$，我們發現其均值為 $\tilde{\mu}_t = \frac{\sqrt{\bar{\alpha}_{t-1}}\beta_t}{1 - \bar{\alpha}_t}x_0 + \frac{\sqrt{\alpha_t}(1 - \bar{\alpha}_{t-1})}{1 - \bar{\alpha}_t}x_t$。

與其讓神經網絡預測 $\tilde{\mu}_t$，Ho et al. (2020) 提出改為讓神經網絡預測加入的「雜訊 $\epsilon$」。將 $x_0 = \frac{x_t - \sqrt{1 - \bar{\alpha}_t}\epsilon}{\sqrt{\bar{\alpha}_t}}$ 代入後，損失函數可以被極大地簡化為真實雜訊 $\epsilon$ 與網絡預測雜訊 $\epsilon_\theta$ 之間的均方誤差 (MSE)：

$$ L_{simple}(\theta) = \mathbb{E}_{t, x_0, \epsilon} \left[ \left\| \epsilon - \epsilon_\theta(x_t, t) \right\|^2 \right] $$

$$ L_{simple}(\theta) = \mathbb{E}_{t, x_0, \epsilon} \left[ \left\| \epsilon - \epsilon_\theta(\sqrt{\bar{\alpha}_t}x_0 + \sqrt{1-\bar{\alpha}_t}\epsilon, t) \right\|^2 \right] $$

> [!NOTE] 損失函數的學術意涵
> $L_{simple}$ 放棄了 VLB 中各個時間步的嚴格權重係數 (即忽略了 $\frac{\beta_t^2}{2\sigma_t^2 \alpha_t (1-\bar{\alpha}_t)}$)，這等同於在較大的 $t$ (雜訊主導期) 給予較小的權重，這促使網絡將注意力集中在較小 $t$ 時的高頻特徵去噪上。這種數學上的「不嚴謹」反而賦予了模型生成極高質量影像的能力。

---
**相關筆記**：[[AI/GenAI/Markov-Chain-DDPM|馬可夫鏈與 DDPM 的數學交織（本篇的證明細節）]] · [[AI/GenAI/UNet|U-Net（$\epsilon_\theta$ 的骨幹網路）]] · [[AI/GenAI/Variational-Inference|變分推斷（VLB 的來源）]] · [[AI/GenAI/Diffusion-Model|Diffusion Model 總覽]] · [[AI/GenAI/GenAI-Overview|生成式 AI 總覽]]