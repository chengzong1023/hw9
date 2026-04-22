# Week 9 Homework: ARIA v6.0 

## Event Timeline / 事件時間軸

| Date | Event |
|------|-------|
| Jun 15, 2025 | Pre-event baseline — dense forest, no anomaly |
| Aug 2025 | Typhoon Colo triggers landslide → barrier lake forms |
| Sep 11, 2025 | Lake reaches peak area ~86 ha |
| Oct 16, 2025 | Lake drained; fresh sediment deposited at Guangfu township |

---

## Reproducible STAC Item IDs

| Act | Item ID | Date | Cloud Cover | Key Feature |
|-----|---------|------|-------------|-------------|
| PRE  | S2A_MSIL2A_20250615T023141_R046_T51QUG | 2025/06/15 | 1.4%  | Dense forest, no anomaly |
| MID  | S2C_MSIL2A_20250911T022551_R046_T51QUG | 2025/09/11 | 21.8% | Barrier lake peak ~86 ha |
| POST | S2B_MSIL2A_20251016T022559_R046_T51QUG | 2025/10/16 | 8.9%  | Lake drained; Guangfu sediment |

Item IDs are loaded from `.env` file (not committed to GitHub).

---

## Detection Results / 偵測結果

### Spectral Indices (Task 1)

| Index | Min | Mean | Max | Key Observation |
|-------|-----|------|-----|-----------------|
| ΔNDVI Pre→Mid  | -0.886 | -0.027 | 0.686 | Vegetation loss at lake site |
| ΔNDVI Pre→Post | -0.744 | -0.036 | 0.595 | Persistent loss post-drainage |
| ΔNDWI Pre→Mid  | -0.544 | +0.019 | 0.970 | Water increase confirmed |
| ΔBSI  Pre→Mid  | -0.670 | -0.004 | 0.510 | Exposed soil at landslide scar |

### Threshold Optimization (Task 2)

Best ΔNDVI threshold = **-0.55** (F1 = 0.85)  
Sweep range: -0.05 to -0.75 | Metrics: F1, PA (Recall), UA (Precision)

### Accuracy Assessment (Task 3)

| Metric | Value | Interpretation |
|--------|-------|----------------|
| Overall Accuracy | 92.7% | 51/55 valid points correct |
| Producer's Accuracy | 73.3% | 11/15 real changes detected |
| User's Accuracy | 100.0% | Zero false alarms |
| Kappa Coefficient | 0.800 | Substantial agreement |
| F1 Score | 84.6% | Balanced precision-recall |

Note: 5 validation points excluded (fell on cloud-masked pixels).

### Confidence Zones (Task 4)

| Zone | Criterion | Area | Action |
|------|-----------|------|--------|
| High Confidence | \|ΔNDVI\| > 0.66 | **0.52 km²** | Evacuate immediately |
| Low Confidence  | 0.55–0.66 | **1.94 km²** | Ground survey required |
| No Detection    | \|ΔNDVI\| ≤ 0.55 | 417.25 km² | Safe for reconstruction |
| Cloud Masked    | SCL excluded | 135.20 km² | Reacquire imagery |

---

## Cloud Masking / 雲遮罩

Mid-event scene had **21.8% cloud cover**.  
SCL (Scene Classification Layer) masking was applied using clear classes [2,4,5,6,7,11].  
Without masking, clouds create artificial "phantom water" signals in ΔNDWI.  
See `HW_Task4_phantom_water.png` for before/after comparison.

---

## AI Diagnostic Log / AI 顧問紀錄

**Prompt sent to Claude (Anthropic):**
> Given OA=92.7%, PA=73.3%, UA=100%, Kappa=0.800, High Confidence Area=0.52 km²,
> what confidence level would you assign to operational decisions?
> What additional data would improve confidence?

**Key LLM findings:**
- High Confidence zone → suitable for immediate evacuation orders
- PA=73.3% is the critical weakness → ~27% of true impact may be missed
- Recommended improvements: SAR data (cloud-penetrating), larger validation sample (n≥100), UAV survey of Low Confidence zone

**My reflection:**  
The LLM correctly identified PA as the primary risk metric for disaster response,
consistent with course teaching. The suggestion to integrate Sentinel-1 SAR is
particularly relevant given the 21.8% cloud cover in the Mid-event scene.

---

## Week 8 vs Week 9 Comparison

| Layer | W8 Finding | W9 Validated | Agreement |
|-------|-----------|--------------|-----------|
| Vegetation Impact | Loss at lake site | ΔNDVI confirms, High Conf. = 0.52 km² | ✅ Yes |
| Water Inundation | ~86 ha visual estimate | 52 ha validated (UA=100%) | ⚠️ W8 overstated |
| Debris / Bare Soil | Landslide scar visible | ΔBSI max = +0.51 confirmed | ✅ Yes |
| Post Recovery | Lake drained Oct 2025 | ΔNDVI Pre→Post = -0.036 (not recovered) | ✅ Yes |
| Cloud Masking | Not assessed (40% allowed) | 21.8% removed as phantom water | ➕ W9 improves |

---

## Output Files
Week9_ARIA_v60_chengzong.ipynb        # Main analysis notebook
data/
validation_points.geojson           # 60 instructor-corrected ground-truth points
output/
HW_Task1_difference_maps.png        # 2×2 ΔNDVI / ΔNDWI / ΔBSI panels
HW_Task2_threshold_optimization.png # F1 / PA / UA vs threshold curve
HW_Task3_confusion_matrix.png       # Confusion matrix heatmap
HW_Task4_phantom_water.png          # Cloud masking before/after comparison
HW_Task4_confidence_map.png         # 3-zone confidence map
.gitignore                            # Excludes .env from version control
---

## Setup / 環境設置

Install dependencies:

    pip install pystac-client stackstac scikit-learn planetary-computer python-dotenv seaborn matplotlib numpy pandas

Create a `.env` file in the project root (NOT committed to GitHub):

    PRE_ITEM_ID=S2A_MSIL2A_20250615T023141_R046_T51QUG_20250615T070417
    MID_ITEM_ID=S2C_MSIL2A_20250911T022551_R046_T51QUG_20250911T055914
    POST_ITEM_ID=S2B_MSIL2A_20251016T022559_R046_T51QUG_20251016T042804
    THRESHOLD_BEST=-0.55
    VALIDATION_DATA=data/validation_points.geojson

Run the notebook top to bottom. Loading satellite data takes ~2 minutes.

---

*ARIA v6.0 | NTU Remote Sensing Lab | 2026-04-22*
