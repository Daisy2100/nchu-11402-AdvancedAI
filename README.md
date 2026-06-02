# PM2.5 時間序列分析期末報告

課程：NCHU 11402 Advanced AI

## 一、下載資料與定義問題

### 1. 資料集名稱與來源

- 名稱：Taiwan Air Quality Index Data (2016–2024)
- 來源：Kaggle
- 下載位置：https://www.kaggle.com/datasets/taweilo/taiwan-air-quality-data-20162024?resource=download
- 檔案位置：data/air_quality.csv

### 2. 問題定義

本報告選擇時間序列預測問題：

- 目標：預測下一小時的 PM2.5（pm25.shift(-1)）
- 時間範圍：2016-11-25 至 2024-08-31
- 單位：小時

## 二、資料清理與觀察

### 1. 欄位確認與缺值檢查

- 主要欄位：date、sitename、county、aqi、pm2.5、so2、co、o3、no2、windspeed、winddirec、latitude、longitude
- 缺值處理：時間序列重採樣後以時間插值，並使用前後補值（bfill/ffill）補齊。

### 2. 資料整併

- 是否需整併多份原始資料：否
- 整併方式：無

### 3. 視覺化觀察

- 時間序列趨勢圖：output/figures/pm25_timeseries.png
- 分佈圖：output/figures/pm25_distribution.png
- 分佈是否不平衡：此為連續值回歸問題，非分類不平衡；分佈偏態需注意。
- 是否適合深度學習：資料量充足，可行，但需額外序列化與標準化處理。

## 三、特徵擷取

### 1. 時間特徵

- hour、dayofweek、month、is_weekend
- hour_sin、hour_cos（週期性編碼）

### 2. 延遲特徵（lag）

- pm25_lag_1、pm25_lag_2、pm25_lag_3、pm25_lag_6、pm25_lag_12、pm25_lag_24

### 3. 移動統計（rolling）

- rolling_3、rolling_6、rolling_24

特徵總數：15

## 四、模型建構與驗證

### 1. 資料切分

- 訓練集：前 80% 時序資料
- 測試集：最後 20% 時序資料
- 切分方式：時間序列切分（非隨機）

### 2. 模型設定

- 線性回歸（StandardScaler + Pipeline）
- 隨機森林回歸（n_estimators=200、max_depth=12、random_state=42）

### 3. 評估指標

- MAE、RMSE、R2

### 4. 測試結果

| 模型 | MAE | RMSE | R2 |
| --- | --- | --- | --- |
| 線性回歸 | 4.1331 | 5.7311 | 0.7489 |
| 隨機森林 | 3.9452 | 5.5219 | 0.7669 |

### 5. 特徵重要度

- pm25_lag_1（約 0.89）
- rolling_24
- hour
- pm25_lag_2
- pm25_lag_24

## 五、進一步分析

- PCA 降維觀察：output/figures/pca_visualization.png
- K-Means 分群（k=3）：output/figures/kmeans_clustering.png

## 六、討論與結論

- 主要發現：隨機森林表現優於線性回歸，顯示 PM2.5 具有非線性與短期依賴特性；近期的 pm25_lag_1 影響最大。
- 模型限制：僅使用單一測站與基本特徵，未納入天氣或其他污染物交互影響。
- 未來改進：可加入更多氣象/污染物特徵、嘗試 LSTM 或 Transformer 等序列模型。

## 七、繳交內容

- 程式碼（Code）：PM25_Time_Series_Final_Project.ipynb
- 分析報告（Word 或 PowerPoint）：report.docx

## 八、如何重現結果

### 1. 環境需求

```
Python >= 3.8
pandas
numpy
matplotlib
scikit-learn
```

### 2. 安裝

```bash
pip install pandas numpy matplotlib scikit-learn
```

### 3. 執行

```bash
jupyter notebook PM25_Time_Series_Final_Project.ipynb
```

執行完成後輸出：

- 圖表：output/figures/
- 表格：output/results/

## 附錄：專案結構

```
nchu-11402-AdvancedAI/
├─ PM25_Time_Series_Final_Project.ipynb
├─ data/
│  └─ air_quality.csv
├─ output/
│  ├─ figures/
│  └─ results/
└─ src/
```
