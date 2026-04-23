# Week 9 Homework — ARIA v6.0 The Validated Auditor
---

## 專案簡介

本作業採用 **ARIA v6.0（Automated Remote Imaging Auditor）** 框架，
對馬太鞍堰塞湖災害事件進行驗證型遙測分析。
以 Sentinel-2 L2A 多時期影像為基礎，結合 SCL 雲遮罩、光譜指標計算、
門檻最佳化與地面驗證，輸出具備精度指標與操作信心的災害評估報告。

---

## 資料夾結構

```
Week9_ARIA_v60_chengzong/
├── Week9_ARIA_v60_integrated.ipynb   # 主要作業 notebook
├── .env                              # 影像 ID 與環境設定（不上傳 GitHub）
├── .env.example                      # .env 範本
├── data/
│   └── validation_points.geojson    # 教師提供之 60 個地面驗證點
└── output/
    ├── HW_Task1_difference_maps_integrated.png
    ├── HW_Task2_threshold_optimization_integrated.png
    ├── HW_Task3_confusion_matrix_integrated.png
    ├── HW_Task4A_phantom_water_integrated.png
    └── HW_Task4B_confidence_map_integrated.png
```

---

## 環境設定

### 1. 安裝套件

```bash
pip install numpy pandas geopandas matplotlib seaborn scikit-learn \
            pystac-client stackstac planetary-computer pyproj python-dotenv
```

### 2. 設定 .env

複製範本並填入影像 ID：

```bash
cp .env.example .env
```

`.env` 內容格式：

```
PRE_ITEM_ID=S2A_MSIL2A_20250615T023141_R046_T51QUG_20250615T070417
MID_ITEM_ID=S2C_MSIL2A_20250911T022551_R046_T51QUG_20250911T055914
POST_ITEM_ID=S2B_MSIL2A_20251016T022559_R046_T51QUG_20251016T042804
BBOX_WEST=121.28
BBOX_SOUTH=23.56
BBOX_EAST=121.52
BBOX_NORTH=23.76
THRESHOLD_BEST=-0.15
```

### 3. 執行 Notebook

從 Cell 0 依序執行，各 Task 的輸出圖檔會自動儲存至 `output/` 資料夾。

---

## 作業架構（7 Tasks）

| Task | 內容 | 配分 |
|------|------|------|
| 1 | 光譜指標計算（ΔNDVI / ΔNDWI / ΔBSI）+ SCL 雲遮罩 | 20% |
| 2 | 門檻值優化（10 個門檻掃描，F1 最大化） | 20% |
| 3 | 混淆矩陣與精度評估（OA / PA / UA / Kappa / F1） | 20% |
| 4 | 幽靈水體比較圖 + 三區信心分類圖 | 15% |
| 5 | ARIA v6.0 驗證型災害評估報告 | 15% |
| 6 | AI 顧問輸入（LLM prompt + 回應 + 個人反思） | 10% |
| 7 | Week 8 vs. Week 9 比較分析 | 10% |

---

## 主要結果摘要

| 指標 | 數值 |
|------|------|
| 最佳 ΔNDVI 門檻 | **-0.15** |
| Overall Accuracy (OA) | **0.800** |
| Producer's Accuracy (PA) | **0.643** |
| User's Accuracy (UA) | **0.947** |
| Kappa Coefficient | **0.602** |
| F1-score | **0.766** |
| High Confidence Zone | **25.97 km²** |
| Low Confidence Zone | **17.09 km²** |

---

## 注意事項

- 影像資料透過 Microsoft Planetary Computer STAC API 存取，需要網路連線
- Cell 0 載入影像需約 **2–5 分鐘**，請耐心等待

---

## 參考資源

- [scikit-learn Metrics](https://scikit-learn.org/stable/modules/model_evaluation.html)
- [Planetary Computer STAC](https://planetarycomputer.microsoft.com/)
- [Sentinel-2 SCL 說明](https://sentinels.copernicus.eu/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm)
- [Confusion Matrix](https://en.wikipedia.org/wiki/Confusion_matrix)
