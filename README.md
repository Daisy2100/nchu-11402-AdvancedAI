# PM2.5 時間序列預測

> 課程：NCHU 11402 Advanced AI
> 目標：以台灣空品時間序列（2016–2024）預測下一小時的 PM2.5。

## 專案概述

本專案建置 PM2.5 的時間序列預測流程，包含：

- 資料清理與每小時重採樣
- 時間與歷史資訊的特徵工程
- 傳統回歸模型訓練與評估
- 趨勢與分群的視覺化分析

## 專案結構

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

## 資料集

來源：Kaggle Taiwan Air Quality Index Data (2016–2024)

- 檔案：data/air_quality.csv
- 規模：約 840 MB，約 5.88M 筆、25 欄
- 主要欄位：date、sitename、county、aqi、pm2.5、so2、co、o3、no2、windspeed、winddirec、latitude、longitude

## 資料處理流程

Notebook 主要步驟：

1. 解析日期、清理 pm2.5、移除異常值
2. 篩選測站與時間區間
3. 使用 pandas 進行每小時重採樣
4. 建立 lag 與 rolling 統計特徵
5. 依時間切分訓練/測試（80/20）
6. 訓練模型並評估 MAE/RMSE/R2

## 特徵工程

目標：下一小時 PM2.5（pm25.shift(-1)）。

時間特徵：

- hour、dayofweek、month、is_weekend
- hour_sin、hour_cos（週期性編碼）

延遲特徵（lag）：

- pm25_lag_1、pm25_lag_2、pm25_lag_3、pm25_lag_6、pm25_lag_12、pm25_lag_24

移動統計（rolling）：

- rolling_3、rolling_6、rolling_24

特徵總數：15

## 模型

- 線性回歸（StandardScaler + Pipeline）
- 隨機森林回歸（n_estimators=200、max_depth=12、random_state=42）

## 結果

測試集指標：

| 模型 | MAE | RMSE | R2 |
| --- | --- | --- | --- |
| 線性回歸 | 4.1331 | 5.7311 | 0.7489 |
| 隨機森林 | 3.9452 | 5.5219 | 0.7669 |

隨機森林特徵重要度前幾名：

- pm25_lag_1（約 0.89）
- rolling_24
- hour
- pm25_lag_2
- pm25_lag_24

## 進一步分析

- PCA 降維觀察
- 在 PCA 空間做 K-Means 分群（k=3）

## 輸出

圖表：output/figures/

- pm25_timeseries.png
- pm25_distribution.png
- pca_visualization.png
- kmeans_clustering.png
- prediction_result.png
- feature_importance.png

表格：output/results/

- model_metrics.csv
- feature_importance.csv
- pca_result.csv
- kmeans_cluster_result.csv
- prediction_results.csv

## 需求環境

```
Python >= 3.8
pandas
numpy
matplotlib
scikit-learn
```

安裝：

```bash
pip install pandas numpy matplotlib scikit-learn
```

## 執行方式

1. 下載資料集並放到 data/air_quality.csv。
2. 開啟 Notebook：

```bash
jupyter notebook PM25_Time_Series_Final_Project.ipynb
```

3. 如需指定路徑，請在 Cell 1 設定 PROJECT_ROOT。
4. 全部執行後會輸出到 output/figures/ 與 output/results/。

## 備註

- 使用時間序列切分（非隨機切分）。
- 短期預測中 lag 特徵的重要度通常最高。
- 若更換測站或時間區間，請重新檢查重採樣與缺值處理。
