---
tags: [StochasticProcesses, Math, Probability, MCMC]
aliases: [馬可夫鏈, Markov Chain, Discrete-time Markov Chain, DTMC]
---

> 關聯網絡： [[Stochastic_Process]] | [[Probability_Theory]] | [[MCMC]] | [[Ergodic_Theory]] | [[Detailed_Balance]]

# 馬可夫鏈 (Markov Chain)

馬可夫鏈（Markov Chain）是隨機過程（Stochastic Processes）中最基礎且最重要的數學模型之一。它描述了一個系統在給定狀態空間（State Space）中，隨著時間推移而發生狀態轉換的機率演化過程。其核心基石為「無記憶性」，即系統未來的演化僅取決於當下狀態，而與過去的歷史軌跡完全獨立。

---

## 1. 核心定義與馬可夫性質

設 $\{X_n, n = 0, 1, 2, \dots \}$ 為一個隨機序列，其狀態空間為可數集合 $S$（通常為整數集 $\mathbb{Z}$ 或其子集）。若對於所有時間步 $n \geq 0$ 以及任意狀態序列 $i_0, i_1, \dots, i_{n-1}, i, j \in S$，該隨機過程滿足以下條件機率等式：

$$ P(X_{n+1} = j \mid X_n = i, X_{n-1} = i_{n-1}, \dots, X_0 = i_0) = P(X_{n+1} = j \mid X_n = i) $$

則稱 $\{X_n\}$ 為一個**離散時間馬可夫鏈 (Discrete-Time Markov Chain, DTMC)**。此等式即為嚴格的**馬可夫性質 (Markov Property)**，在物理上表徵了系統的**無記憶性 (Memoryless Property)**。

若轉移機率不隨時間 $n$ 改變，即 $P(X_{n+1} = j \mid X_n = i) = p_{ij}$ 對所有 $n$ 皆成立，則稱此為**齊次馬可夫鏈 (Time-Homogeneous Markov Chain)**。

---

## 2. 轉移機率矩陣與查普曼-科爾莫戈羅夫等式

### 單步轉移矩陣 (One-Step Transition Matrix)
對於齊次馬可夫鏈，狀態 $i$ 轉移至狀態 $j$ 的單步轉移機率定義為 $p_{ij}$。所有狀態間的轉移機率可構成一個矩陣 $\mathbf{P} = [p_{ij}]$，稱為**轉移機率矩陣 (Transition Probability Matrix)**。該矩陣必須滿足隨機矩陣 (Stochastic Matrix) 的兩大公理：
1. 非負性：$p_{ij} \geq 0, \quad \forall i, j \in S$
2. 列和為一：$\sum_{j \in S} p_{ij} = 1, \quad \forall i \in S$

### 查普曼-科爾莫戈羅夫方程式 (Chapman-Kolmogorov Equations)
令 $p_{ij}^{(n)} = P(X_{n+k} = j \mid X_k = i)$ 表示從狀態 $i$ 經過 $n$ 步轉移到狀態 $j$ 的機率。根據全機率定理與馬可夫性質，可推導出極為重要的 Chapman-Kolmogorov 等式：

$$ p_{ij}^{(n+m)} = \sum_{k \in S} p_{ik}^{(n)} p_{kj}^{(m)} $$

將其寫成矩陣形式，即為 $\mathbf{P}^{(n+m)} = \mathbf{P}^{(n)} \mathbf{P}^{(m)}$。這暗示了 $n$ 步轉移矩陣純粹是單步轉移矩陣的 $n$ 次方：
$$ \mathbf{P}^{(n)} = \mathbf{P}^n $$

---

## 3. 狀態分類的代數拓撲 (Classification of States)

為了分析馬可夫鏈的長期行為（漸近性質），必須對狀態空間進行嚴格的分類：

- **可達性 (Accessibility) 與 互通性 (Communication)**：若存在 $n \geq 0$ 使得 $p_{ij}^{(n)} > 0$，稱狀態 $j$ 可由狀態 $i$ 到達，記為 $i \to j$。若 $i \to j$ 且 $j \to i$，稱兩狀態互通，記為 $i \leftrightarrow j$。互通性是一個等價關係，可將狀態空間劃分為多個等價類 (Communication Classes)。
- **不可推約性 (Irreducibility)**：若狀態空間 $S$ 中只有一個等價類（即所有狀態皆互相可達），則稱該馬可夫鏈為不可推約的 (Irreducible)。
- **常返 (Recurrence) 與 暫態 (Transience)**：
  定義首達時間 (First Hitting Time) $T_i = \inf \{n \geq 1 : X_n = i\}$。令 $f_{ii}$ 為從狀態 $i$ 出發，最終能回到狀態 $i$ 的機率：
  $$ f_{ii} = P(T_i < \infty \mid X_0 = i) = \sum_{n=1}^\infty P(T_i = n \mid X_0 = i) $$
  - 若 $f_{ii} = 1$，稱狀態 $i$ 為**常返態 (Recurrent State)**。這保證了系統會無限次返回該狀態（$\sum_{n=1}^\infty p_{ii}^{(n)} = \infty$）。
  - 若 $f_{ii} < 1$，稱狀態 $i$ 為**暫態 (Transient State)**。系統最終將永遠離開該狀態。
- **正常返 (Positive Recurrent) 與 零常返 (Null Recurrent)**：對於常返態 $i$，定義其平均返回時間 $\mu_i = \mathbb{E}[T_i \mid X_0 = i]$。若 $\mu_i < \infty$，則為正常返；若 $\mu_i = \infty$，則為零常返。
- **週期性 (Periodicity)**：狀態 $i$ 的週期 $d(i)$ 定義為能從 $i$ 回到 $i$ 的所有步數的最大公因數：
  $$ d(i) = \gcd \{ n \geq 1 : p_{ii}^{(n)} > 0 \} $$
  若 $d(i) = 1$，稱該狀態為**非週期的 (Aperiodic)**。

---

## 4. 平穩分佈與極限定理 (Stationary Distribution & Limit Theorems)

馬可夫鏈最深奧的理論在於探討時間趨向無窮大時，系統機率分佈的收斂性。

### 平穩分佈 (Stationary Distribution)
若存在一個機率向量 $\pi = [\pi_1, \pi_2, \dots]$ 滿足：
1. $\pi_j \geq 0$ 且 $\sum_{j \in S} \pi_j = 1$
2. 全域平衡方程式 (Global Balance Equations)：
   $$ \pi = \pi \mathbf{P} \implies \pi_j = \sum_{i \in S} \pi_i p_{ij}, \quad \forall j \in S $$
則稱 $\pi$ 為該馬可夫鏈的平穩分佈。在物理上，這代表當系統一旦進入平穩分佈，其後的任何時間步，狀態機率分佈將不再隨時間改變。

### 馬可夫鏈的極限定理 (Ergodic Theorem)
若一個馬可夫鏈同時滿足以下三個條件：
1. **不可推約 (Irreducible)**
2. **非週期 (Aperiodic)**
3. **正常返 (Positive Recurrent)**

則稱該馬可夫鏈為**遍歷的 (Ergodic)**。對於遍歷馬可夫鏈，極限分佈必然存在、唯一，且精確等於平穩分佈：
$$ \lim_{n \to \infty} p_{ij}^{(n)} = \pi_j, \quad \forall i, j \in S $$
且 $\pi_j = \frac{1}{\mu_j}$（平穩機率與平均返回時間成反比）。這意味著無論系統的初始狀態 $X_0$ 為何，經過漫長時間的演化，系統會「忘記」初始條件，最終收斂至唯一的全域平穩分佈 $\pi$。

---

> [!IMPORTANT] 細緻平衡條件 (Detailed Balance) 與可逆性
> 在高階應用（如蒙地卡羅馬可夫鏈 MCMC 方法）中，我們會尋找滿足**細緻平衡條件 (Detailed Balance Equations)** 的狀態分佈：
> $$ \pi_i p_{ij} = \pi_j p_{ji}, \quad \forall i, j \in S $$
> 該條件表示系統在穩態下，由狀態 $i$ 轉移至狀態 $j$ 的機率通量，嚴格等於由狀態 $j$ 轉移回狀態 $i$ 的機率通量。
> **數學定理**：若一個機率分佈 $\pi$ 滿足細緻平衡條件，則 $\pi$ 必然是該馬可夫鏈的平穩分佈（將上式對 $i$ 總和即得 $\sum_i \pi_i p_{ij} = \pi_j \sum_i p_{ji} = \pi_j$）。
> 滿足此條件的馬可夫鏈稱為**時間可逆馬可夫鏈 (Time-Reversible Markov Chain)**。此一極其強大的代數性質是 Metropolis-Hastings 演算法與許多物理統計模型建立的理論命脈。