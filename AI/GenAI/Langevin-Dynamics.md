---
title: 朗之萬動力學 (Langevin Dynamics)
tags: [GenerativeAI, Math, Physics, StochasticProcesses, Diffusion]
aliases: [Langevin_Dynamics, Langevin Dynamics, 朗之萬動力學]
date: 2026-07-01
---

# 朗之萬動力學 (Langevin Dynamics)

> [!abstract] **一句話**
> 朗之萬動力學原本是物理學描述「布朗運動」的方程——一顆花粉粒子在水中同時受到「確定性的力」與「隨機的碰撞」影響。把這個方程搬到機率分佈上，就變成一套「從任意起點出發，隨機遊走最終會收斂到目標分佈」的採樣演算法——這正是 [[AI/GenAI/Diffusion-Model|Score-based Diffusion]] 的數學骨幹。

## 1. 物理原型

$$ \frac{dx}{dt} = -\nabla U(x) + \sqrt{2}\, \eta(t) $$

- $-\nabla U(x)$：確定性的力，把粒子推向能量低的地方（位能 $U$ 的下坡方向）。
- $\eta(t)$：隨機的熱擾動（白雜訊）。

粒子最終會停在「位能低（力的吸引）」與「雜訊擾動（探索）」的平衡態——這個平衡分佈剛好是 $p(x) \propto e^{-U(x)}$（Boltzmann 分佈）。

## 2. 搬到機率採樣：Langevin Monte Carlo

如果我們想從一個機率分佈 $p(x)$ 採樣，只要知道它的 **score function**（對數機率密度的梯度）$\nabla_x \log p(x)$，就能套用離散化的朗之萬方程迭代採樣：

$$ x_{t+1} = x_t + \frac{\epsilon}{2} \nabla_x \log p(x_t) + \sqrt{\epsilon}\, z_t, \quad z_t \sim \mathcal{N}(0, \mathbf{I}) $$

> [!info] **這步驟不需要知道 $p(x)$ 本身，只需要知道它的梯度方向**
> 這是關鍵优势：機率密度的正規化常數（分母那個算不出來的積分，見 [[AI/GenAI/Variational-Inference|變分推斷筆記]]的問題起源）在取梯度、取 log 之後直接消失了——$\nabla_x \log p(x)$ 完全不需要知道 $p(x)$ 準確的縮放比例。這正是為什麼「訓練一個網路預測 score $\nabla_x \log p(x)$」是可行的：不用管歸一化常數，直接用 Score Matching 目標函數訓練即可。

## 3. 跟 DDPM 的關係：兩條路殊途同歸

[[AI/GenAI/DDPM|DDPM]] 訓練網路預測**雜訊 $\epsilon_\theta$**；Score-based 模型訓練網路預測**score $s_\theta(x) \approx \nabla_x \log p(x)$**。表面上是兩個不同的研究脈絡（一個源自變分推斷、一個源自朗之萬採樣），但可以證明：

$$ \nabla_{x_t} \log q(x_t \vert x_0) = -\frac{\epsilon}{\sqrt{1-\bar\alpha_t}} $$

也就是說，**DDPM 預測的雜訊 $\epsilon_\theta$ 跟 score $\nabla \log p$ 只差一個已知的縮放常數**。這代表 DDPM 的反向去噪步驟，本質上就是在做「離散化的朗之萬採樣」——每一步都往「機率密度上升（score 方向）」走一點，同時加一點探索用的雜訊。這也是[[AI/GenAI/Diffusion-Model|Diffusion Model 總覽]]裡提到的「Score-based / SDE」統一視角的數學基礎：DDPM 是這個連續時間 SDE 框架下的一個離散化特例。

## 4. 直覺理解

想像在濃霧瀰漫的山區找山頂（機率密度最高點）：
- $\nabla \log p(x)$ 告訴你「往哪個方向走地勢會變高」（score 指引方向）。
- $z_t$ 的隨機擾動讓你不會死板地走同一條路，有機會探索到更高的山頭（避免卡在小丘陵=局部最大值，概念上跟 [[Fundamentals/Optimization-Theory|SA 用溫度跳出局部最佳]]是同一種精神）。
- 反覆走很多小步之後，你的位置分佈會收斂到「山頂附近機率高、山谷機率低」的目標分佈本身。

---
**相關筆記**：[[AI/GenAI/Diffusion-Model|Diffusion Model 總覽]] · [[AI/GenAI/DDPM|DDPM]] · [[AI/GenAI/Variational-Inference|變分推斷]] · [[Fundamentals/Optimization-Theory|最佳化理論（局部最佳陷阱）]]
