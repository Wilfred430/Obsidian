# 資料不平衡處理與評估指標

## 類別不平衡 (Class Imbalance) 策略
針對 15:1 的極端比例，單純訓練會導致模型傾向預測多數類別。

### 資料層面優化
- 欠採樣 (Undersampling)：減少多數類別的樣本數。
- 過採樣/資料擴增 (Data Augmentation)：
    - 翻譯回譯 (Back-translation)。
    - 同義詞替換。
    - 增加少數類別的樣本合成。

### Prompt 層面優化
- 在指令中明確強調少數類別的特徵。
- 提供少數類別的 Few-shot examples 以強化模型的辨識能力。

## 評估指標：Macro F1 Score
- 原理：將各類別的 F1 分數獨立計算後進行等權重平均。
- 必要性：在不平衡資料集中，Accuracy 會因為模型無腦預測多數類別而呈現虛高，Macro F1 能強迫模型必須在少數類別也取得良好表現。

## 資料前處理品質 (PDF Parsing)
- 核心原則：Garbage In, Garbage Out (GIGO)。
- 處理重點：清理多餘換行符、頁首頁尾、參考文獻雜亂字元。
- 推薦工具：PyMuPDF, pdfplumber。
