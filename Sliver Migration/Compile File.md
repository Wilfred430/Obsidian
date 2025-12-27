
### How to compile system Verilog file
```
vcs -full64 -sverilog -debug_access+all -kdb -R tb_silver_migration.sv silver_migration_model.sv
```
1.  **vcs** :  代表使用 **Synopsys VCS (Verilog Compiler Simulator)** 來編譯與執行 SystemVerilog 程式。

2.  **-full64** :  指定使用 **64-bit 模式**來編譯與執行，確保能處理大型設計與記憶體需求。

3.  **-sverilog** : 啟用 **SystemVerilog 語法支援**，因為你的檔案 (`tb_silver_migration.sv` 與 `silver_migration_model.sv`) 是 SystemVerilog 格式。

4. **-debug_access+all** : 
	- 開啟 **完整的除錯存取**，允許在模擬時透過除錯工具（例如 DVE）存取所有變數、訊號、層級。
	- 這樣你可以在波形檔裡看到所有訊號，不會被最佳化掉。

5.  **-kdb** : 啟用 **Kernel Debug Database**，讓模擬器建立除錯資料庫，方便後續用 **DVE** 或其他工具進行波形分析與除錯。

6.  **-R** : 編譯完成後 **直接執行模擬**（Run）。- 如果沒有 `-R`，VCS 只會編譯，必須再手動執行 `./simv` 才能跑模擬。

### How to open Verdi file
```
verdi -ssf silver_migration_wave.fsdb &
```
1. `verdi` : 呼叫 **Verdi**，這是 Synopsys 的 **波形瀏覽與除錯工具**，常用來分析 VCS 產生的模擬結果。

2. `-ssf silver_migration_wave.fsdb`
	- `-ssf` 代表 **指定波形檔 (Signal Save File)**。
	- `silver_migration_wave.fsdb` 是模擬器輸出的 **FSDB 格式波形檔**，裡面記錄了所有訊號的時間變化。
	- Verdi 會讀取這個檔案，讓你在 GUI 裡瀏覽波形、追蹤訊號、做除錯。

3. `&`
	- 在 Linux shell 裡，`&` 表示 **背景執行**。
	- 這樣你執行 Verdi 後，終端機不會被佔住，可以繼續輸入其他指令