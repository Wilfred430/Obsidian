---
tags: [GenerativeAI, DeepLearning, StochasticProcesses, Diffusion, Math]
aliases: [馬可夫鏈與擴散模型, Markov Chain in DDPM]
---

# 馬可夫鏈與擴散模型（DDPM）的數學交織

在去噪擴散概率模型（Denoising Diffusion Probabilistic Models, DDPM）中，馬可夫鏈（Markov Chain）是構築整個生成理論與熱力學擴散物理意義的基石。本篇筆記將深入解構馬可夫性質，並剖析其如何透過高等機率論與重參數化技巧推動擴散模型的運作。

---

## 1. 馬可夫鏈基礎定理

在隨機過程（Stochastic Processes）中，馬可夫鏈描述了一個在離散時間或連續時間下，狀態空間轉移的數學模型。其最核心的基石為**無記憶性（Memoryless Property）**，又稱**馬可夫性質（Markov Property）**。

數學定義如下：設 $\{X_t, t = 0, 1, 2, \dots \}$ 為一隨機過程，若對於任意時間步 $t$ 與任意狀態序列 $x_0, x_1, \dots, x_t, x_{t+1}$，給定當下狀態 $x_t$ 的條件下，未來狀態 $x_{t+1}$ 的條件機率分佈，僅依賴於當下狀態 $x_t$，而與過去的歷史狀態完全獨立（條件獨立），即：

$$ P(X_{t+1} = x_{t+1} \vert X_t = x_t, X_{t-1} = x_{t-1}, \dots, X_0 = x_0) = P(X_{t+1} = x_{t+1} \vert X_t = x_t) $$

在機率密度函數表示法中，聯合機率分佈可根據馬可夫性質被極大地簡化為一連串單步條件機率的乘積：

$$ q(x_{1:T} \vert x_0) = \prod_{t=1}^T q(x_t \vert x_{t-1}) $$

這種無記憶性使得高維度時間序列數據的建模在數學分析與計算上成為可能。

---

## 2. 前向加噪：轉移概率的級聯

在 DDPM 的前向過程（Forward Process）中，我們定義從原始數據 $x_0$ 出發，每一步向數據注入微量的高斯雜訊，直到最終時間步 $T$ 數據轉化為純高斯白雜訊 $x_T$。這個過程被嚴格定義為一個**線性高斯馬可夫鏈（Linear Gaussian Markov Chain）**。

給定前一時刻狀態 $x_{t-1}$，向下一時刻 $x_t$ 的單步轉移條件概率分佈 $q(x_t \vert x_{t-1})$ 定義為：

$$ q(x_t \vert x_{t-1}) = \mathcal{N}(x_t; \sqrt{1 - \beta_t} x_{t-1}, \beta_t \mathbf{I}) $$

其中，$\beta_t \in (0, 1)$ 是預先給定的變異數時間表（Variance Schedule），代表在時間步 $t$ 注入雜訊的強度。因為 $x_t$ 的生成完全且僅依賴於 $x_{t-1}$ 以及當下注入的獨立高斯雜訊，前向加噪過程完美符合馬可夫性質。

---

## 3. 馬可夫鏈的關鍵突破：重參數化技巧

若要訓練擴散模型，我們需要從分佈中採樣任意時間步的 $x_t$。如果嚴格遵照馬可夫鏈的定義，我們必須從 $x_0$ 開始，計算 $x_1, x_2, \dots$ 一步步迭代至 $x_t$。這在計算複雜度上是極為低效甚至無法承受的。

幸運的是，得益於高斯分佈線性組合的優良封閉性質（Closed-form property），我們可以透過**重參數化技巧（Reparameterization Trick）**繞過這層限制。

定義 $\alpha_t = 1 - \beta_t$ 為單步訊號保留率，$\bar{\alpha}_t = \prod_{i=1}^t \alpha_i$ 為累積訊號保留率。我們將轉移機率改寫為代數形式，其中 $\epsilon_{t-1} \sim \mathcal{N}(0, \mathbf{I})$：

$$ 
\begin{align}
x_t &= \sqrt{\alpha_t} x_{t-1} + \sqrt{1 - \alpha_t} \epsilon_{t-1} \\
&= \sqrt{\alpha_t} (\sqrt{\alpha_{t-1}} x_{t-2} + \sqrt{1 - \alpha_{t-1}} \epsilon_{t-2}) + \sqrt{1 - \alpha_t} \epsilon_{t-1} \\
&= \sqrt{\alpha_t \alpha_{t-1}} x_{t-2} + \sqrt{\alpha_t(1 - \alpha_{t-1}) + (1 - \alpha_t)} \bar{\epsilon}_{t-2} \\
&= \sqrt{\alpha_t \alpha_{t-1}} x_{t-2} + \sqrt{1 - \alpha_t \alpha_{t-1}} \bar{\epsilon}_{t-2} \\
&= \dots \\
&= \sqrt{\bar{\alpha}_t} x_0 + \sqrt{1 - \bar{\alpha}_t} \epsilon
\end{align}
$$

其中，$\epsilon \sim \mathcal{N}(0, \mathbf{I})$。透過上述推導，我們獲得了一個極其優雅的封閉形式解。這意味著我們可以直接構造條件邊緣分佈 $q(x_t \vert x_0)$：

$$ q(x_t \vert x_0) = \mathcal{N}(x_t; \sqrt{\bar{\alpha}_t} x_0, (1 - \bar{\alpha}_t)\mathbf{I}) $$

此結果徹底打破了馬可夫鏈「必須逐布模擬」的計算障礙，使得深度學習框架能夠在單一步驟內並行生成大規模的隨機時間步訓練資料。

---

## 4. 反向過程：為什麼逆向馬可夫鏈無法直接求解？

生成過程需要從純高斯雜訊 $x_T$ 開始，執行**逆向馬可夫鏈（Reverse Markov Chain）** 以還原真實數據：即計算 $q(x_{t-1} \vert x_t)$。

若要直接透過解析解求出逆向轉移概率，我們必須動用貝氏定理（Bayes' Theorem）：

$$ q(x_{t-1} \vert x_t) = \frac{q(x_t \vert x_{t-1}) q(x_{t-1})}{q(x_t)} $$

在這條公式中：
1. $q(x_t \vert x_{t-1})$ 是已知的（前向加噪轉移機率）。
2. 但 $q(x_{t-1})$ 與邊緣概率（Marginal Probability）$q(x_t)$ 則是**不可解的（Intractable）**。

為何不可解？因為求取邊緣概率 $q(x_t)$ 需要針對全域真實資料分佈進行高維度積分：

$$ q(x_t) = \int q(x_t \vert x_0) q(x_0) dx_0 $$

由於真實資料分佈 $q(x_0)$（例如全世界所有可能的人臉圖像分佈）是極端複雜、高度非線性且未知的，上述多重積分在數學上完全無法直接計算。

這就是擴散模型為何必須引入深度學習模型的原因。我們無法直接計算 $q(x_{t-1} \vert x_t)$，因此我們引入一組具有可學習參數 $\theta$ 的神經網路 $p_\theta$ 來**近似（Approximate）** 這個逆向馬可夫轉移機率：

$$ p_\theta(x_{t-1} \vert x_t) = \mathcal{N}(x_{t-1}; \mu_\theta(x_t, t), \Sigma_\theta(x_t, t)) $$

透過變分推斷（Variational Inference）與對數似然變分下界（VLB）的優化，我們驅動神經網路學習出接近真實逆向分佈的參數，藉以避開高維度積分的數學死胡同。

---

> [!MATH] 物理前提與 Feller 定理的制約
> 在逆向過渡中，我們假設 $q(x_{t-1} \vert x_t)$ 也能由高斯分佈來近似。這在數學上並非總是成立。
> 根據隨機過程與 Feller 定理（以及相關的 Langevin Dynamics 分析），唯有當**單步轉移的變異數（即時間步步長 $\beta_t$）趨近於無窮小（Infinitesimal）**時，正向擴散與逆向擴散的機率分佈族才會同構。也就是說，唯有在 $\beta_t \to 0$ 且步數 $T \to \infty$ 的極限條件下，真實的逆向分佈才會嚴格保持為高斯分佈。這是為何擴散模型通常需要極大的時間步數（如 $T=1000$）來確保每個微小轉移的穩定性與高斯假設的合理性。

---
關聯節點：[[Diffusion_Model]] | [[Markov_Chain]] | [[Bayes_Theorem]] | [[Stochastic_Process]]