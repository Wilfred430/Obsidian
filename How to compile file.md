
```
vcs -full64 -sverilog -debug_access+all -kdb -R tb_silver_migration.sv silver_migration_model.sv
```
- **vcs** :  代表使用 **Synopsys VCS (Verilog Compiler Simulator)** 來編譯與執行 SystemVerilog 程式。

- **-full64** :  指定使用 **64-bit 模式**來編譯與執行，確保能處理大型設計與記憶體需求。

- **-sverilog** : 啟用 **SystemVerilog 語法支援**，因為你的檔案 (`tb_silver_migration.sv` 與 `silver_migration_model.sv`) 是 SystemVerilog 格式。

- **-debug_access+all** : 
	- 開啟 **完整的除錯存取**，允許在模擬時透過除錯工具（例如 DVE）存取所有變數、訊號、層級。
	- 這樣你可以在波形檔裡看到所有訊號，不會被最佳化掉。

- **-kdb** : 啟用 **Kernel Debug Database**，讓模擬器建立除錯資料庫，方便後續用 **DVE** 或其他工具進行波形分析與除錯。

- **-R** : 編譯完成後 **直接執行模擬**（Run）。- 如果沒有 `-R`，VCS 只會編譯，必須再手動執行 `./simv` 才能跑模擬。