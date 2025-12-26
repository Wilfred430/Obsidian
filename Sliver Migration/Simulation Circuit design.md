<div align="center">
<img src="assets/Simulation%20Circuit%20design/file-20251226174910061.png" width="500">
</div>
根據 [Migration Behavior Model](Migration%20Behavior%20Model.md) 我們可以得知道描述 **silver migration** 的有以下五個變數 :

$$Z_{total} = R_s + (R_{film} \parallel C_{film}) + (R_{ct} \parallel C_{dl})$$

這些變數分別有其對應的物理意義和時變方程，但由於上面的 model 在 impedance 趨近於極大時可能沒有電流流通，這會導致 **RX** 接收到的訊號會成為 **Z** 的樣式，這並非是我想要的，為了讀取正確有意義的值我採用 [FPGA Based System for Open, Short,  and RC Impedance Measurement](FPGA%20Based%20System%20for%20Open,%20Short,%20%20and%20RC%20Impedance%20Measurement.canvas) 裡所提到的分壓方法測試，這樣當 Silver migration 發生時會將電壓 pull down 至低電壓，這樣就能觀測其行為模式。

### 圖(二)的行為模式
