# PEFT 與 QLoRA 技術筆記

## 技術核心概念
由於 Full Fine-tuning 對顯存要求極高，對於一般 GPU 資源，採用參數高效微調（Parameter-Efficient Fine-Tuning, PEFT）是必要手段。

## QLoRA (Low-Rank Adaptation with Quantization)
1. 量化 (Quantization)：
   - 使用 bnb-4bit (BitsAndBytes) 技術。
   - 將 16-bit 浮點數壓縮為 4-bit，大幅降低記憶體占用，使 3B 等級模型能在消費級 GPU 上訓練。

2. LoRA (Low-Rank Adaptation)：
   - 不更新模型原始的數十億個參數。
   - 在原始模型旁「外掛」微小的神經網路矩陣 (Adapter)。
   - 訓練時僅更新此 Adapter，最終繳交物僅需 Adapter Checkpoint 權重，檔案體積小且效率高。

## 實作超參數
- r (Rank)：通常設定為 16 或 32。
- lora_alpha：LoRA 的縮放係數。
- target_modules：指定要應用 LoRA 的模型層（如 q_proj, v_proj 等）。

## 推薦工具
- Unsloth：針對 Qwen2.5 等模型優化的 QLoRA 加速框架。
- SFTTrainer：用於執行監督式微調 (Supervised Fine-tuning) 的訓練器。
