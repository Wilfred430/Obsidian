<div align="center">
<img src="assets/Simulation%20Circuit%20design/file-20251226174910061.png" width="500">
</div>
根據 [Migration Behavior Model](Migration%20Behavior%20Model.md) 我們可以得知道描述 **silver migration** 的有以下五個變數 :

$$Z_{total} = R_s + (R_{film} \parallel C_{film}) + (R_{ct} \parallel C_{dl})$$

這些變數分別有其對應的物理意義和時變方程，但由於上面的 model 在 impedance 趨近於極大時可能沒有電流流通，這會導致 **RX** 接收到的訊號會成為 **Z** 的樣式，這並非是我想要的，為了讀取正確有意義的值我採用 [FPGA Based System for Open, Short,  and RC Impedance Measurement](FPGA%20Based%20System%20for%20Open,%20Short,%20%20and%20RC%20Impedance%20Measurement.canvas) 裡所提到的分壓方法測試，這樣當 Silver migration 發生時會將電壓 pull down 至低電壓，這樣就能觀測其行為模式。
### 圖(二)的行為模式

$$TX \rightarrow R_L \rightarrow R_S \rightarrow Node (RX) \rightarrow (R_{film}/C_{film}/R_{ct}/C_{dl} \text{ to GND})$$
### 第一部分：架構分析與勝負判定

我們正在處理的是「絕緣失效 (Insulation Failure)」問題。銀遷移意味著原本絕緣的兩個導體之間，長出了導電的樹枝狀結晶 (Dendrites)，導致**高阻抗 (Open) 變成 低阻抗 (Short)**。

#### 1. Image 2: 並聯接地架構 (Shunt Topology) —— <font color="green">勝者 (Winner)</font>

- **結構：** TX $\to$ 限流電阻 $\to$ RX $\to$ (銀遷移病灶) $\to$ GND。
    
- **物理意義：** 這是標準的「漏電流偵測」模型。銀遷移通常發生在兩條平行的導線或電極之間（一條是訊號線，一條是地線）。當枝晶生長時，它等於是在訊號線與地線之間搭起了一座「橋」。
    
    - **正常時 (Healthy)：** 銀遷移病灶 ($Z_{silver}$) 阻抗無限大。RX 點沒有電流漏到地，所以 $V_{RX} \approx V_{TX}$ (High)。
        
    - **發生遷移時 (Migration)：** $Z_{silver}$ 變小（短路）。RX 點的電流被「吸」往地端，電壓被「拉低」 (Pull-down)。
        
- **優勢：**
    
    - **高靈敏度：** 這是典型的 **分壓電路 (Voltage Divider)**。當 $Z_{silver}$ 從 $100M\Omega$ 掉到 $10M\Omega$ 時，電壓變化非常線性且明顯。
        
    - **符合直覺：** 這是「失效即短路」的標準模型。
        

#### 2. Image 1: 串聯架構 (Series Topology) —— <font color="red">不推薦 (Not Recommended)</font>

- **結構：** TX $\to R_L \to R_s \to$ (銀遷移病灶) $\to$ RX。
    
- **致命缺陷：**
    
    - **邏輯怪異：** 這個圖暗示訊號必須「穿過」銀遷移枝晶才能到達 RX。這意味著如果沒有銀遷移（絕緣良好），RX 根本收不到訊號（斷路）。這通常用於測量導線是否斷掉，而不是測量絕緣是否變差。
        
    - **浮接問題 (Floating)：** 在健康狀態下，RX 端懸空，數值會亂跳 (Unknown state)，不利於數位電路偵測。
        

---

### 第二部分：SystemVerilog 實作 (針對圖二框架)

既然確定了 Image 2，我們需要建立它的數學模型。這是一個 **RC 並聯負載的分壓電路**。

#### 1. 數學模型 (The Math)

我們要解 RX 點的節點電壓 (Node Voltage)。

- **流入電流 (Source):** $I_{in} = (V_{TX} - V_{RX}) / (R_L + R_s)$
    
- **流出電流 (Leakage to GND):** $I_{leak} = V_{RX} \cdot G_{total} + C_{total} \cdot \frac{dV_{RX}}{dt}$
    

根據 KCL ($I_{in} = I_{leak}$)，並將微分項離散化，SystemVerilog 的核心公式為：

$$V_{RX}[new] = \frac{\frac{V_{TX}}{R_{series}} + \frac{C_{total}}{\Delta t}V_{RX}[old]}{\frac{1}{R_{series}} + G_{total} + \frac{C_{total}}{\Delta t}}$$

其中：

- $R_{series} = R_L + R_s$
    
- $G_{total} = \frac{1}{R_{film}} + \frac{1}{R_{ct}}$ (總電導)
    
- $C_{total} = C_{film} + C_{dl}$ (總電容)

