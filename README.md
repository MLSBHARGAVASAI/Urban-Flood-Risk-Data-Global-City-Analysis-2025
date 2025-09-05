# Climate Risk & Disaster Management(Week-1):
## 🌊 Urban Flood Risk Data: Global City Analysis 2025

## 📌 Project Overview
Urban flooding is one of the most pressing global risks, driven by **climate change, rapid urbanization, and inadequate drainage systems**.  

This project uses the **[Urban Flood Risk Dataset (Kaggle)](https://www.kaggle.com/datasets/pratyushpuri/urban-flood-risk-data-global-city-analysis-2025/data)** to analyze **flood risk across 63 global cities**.  

The dataset contains **2,963 urban segments** with attributes such as:  
- 🌍 **Geography** → City, ward, latitude, longitude  
- 🏞️ **Topography** → Elevation, DEM source  
- 🏘️ **Land use & soil type**  
- 🚰 **Drainage infrastructure** → density, proximity, type  
- 🌧️ **Rainfall data** → source, intensity, return period  
- ⚠️ **Risk labels** → ponding hotspots, low-lying areas, sparse drainage, extreme rainfall events  

**Objective:**  
To identify **high-risk urban segments** and uncover **patterns of vulnerability** that can guide **climate resilience & disaster management strategies**.  

---

## 📌**Problem Statement**

Urban flooding is an escalating threat worldwide due to climate change, rapid urbanization, and inadequate drainage infrastructure. Understanding which areas are most vulnerable is critical for effective disaster management and climate resilience.  

This analysis focuses on **urban flood risk segments** across global cities to identify high-risk areas and uncover patterns using:  

- Segment elevation and topography  
- Land-use types and soil infiltration  
- Storm drainage infrastructure  
- Historical rainfall intensity and return periods  
- Multi-label risk indicators (e.g., ponding hotspots, low-lying areas, sparse drainage)  

The insights from this study will help **urban planners and policymakers** prioritize mitigation strategies, improve infrastructure, and reduce flood-related hazards.


---

## 📂 Dataset Information  
- **Rows (segments):** 2,963  
- **Columns (features):** 17  
- **Unique Cities:** 63  
- **File Source:** [Kaggle Dataset](https://www.kaggle.com/datasets/pratyushpuri/urban-flood-risk-data-global-city-analysis-2025/data)  

### Key Columns  
- `segment_id` → Unique ID  
- `city_name` → City + Country  
- `elevation_m` → Elevation (m)  
- `land_use` → Residential, Commercial, Roads, etc.  
- `soil_group` → Hydrologic soil group (A–D)  
- `storm_drain_proximity_m` → Distance to nearest drain  
- `historical_rainfall_intensity_mm_hr` → Rainfall (mm/hr)  
- `risk_labels` → Multi-label tags (ponding_hotspot, low_lying, sparse_drainage, monitor, etc.)  

---

## 🛠️ Steps Completed (Week 1)  

### 1. Import Libraries  
- Pandas, NumPy (data handling)  
- Matplotlib, Seaborn, Plotly (visualization)  

### 2. Load Dataset  
```python
df = pd.read_csv('/content/urban_pluvial_flood_risk_dataset.csv') 
```

### 3. Dataset Overview  
- **Shape:** (2,963, 17)  
- **Unique Cities:** 63  
- **Example Cities:** Colombo 🇱🇰, Chennai 🇮🇳, Hong Kong 🇨🇳, Durban 🇿🇦  

### 4. Dataset Info  
- Checked data types, non-null counts, and memory usage.  

### 5. Summary Statistics  
- **Elevation:** -3m (below sea level) to 266m  
- **Rainfall intensity:** avg 43.8 mm/hr  
- **Drainage density:** avg 6.3 km/km²  

### 6. Missing Values  
```python
missing = df.isnull().sum().sort_values(ascending=False)
missing_percent = (missing / len(df)) * 100
pd.DataFrame({'Missing Values': missing, 'Percentage (%)': missing_percent})
```
- **Soil Group** → 12.2% missing  
- **Rainfall Source** → 10.6% missing  
- **Drainage Density** → 9.6% missing  
- **Storm Drain Type** → 6.0% missing
-  
## 📌 Week 2 – EDA, Feature Engineering & Modeling

### 7. Exploratory Data Analysis (EDA)  
- ✅ **Top 10 Cities with Most Segments** → Manila, San Francisco, Philadelphia, Rotterdam, Athens…  
- ✅ **Elevation Distribution** → Most areas between 0–100m, some below sea level.  
- ✅ **Land Use Distribution** → Residential (27.9%), Roads (20.2%), Commercial (16.6%).  
- ✅ **Risk Labels Frequency** → ponding_hotspot, low_lying, sparse_drainage are dominant tags.  

### 8. Correlation Analysis  
- A heatmap of numeric features was generated.  
- **Key findings:**  
  - **Elevation ⬆️** → **Flood Risk ⬇️** (negative correlation)  
  - **Rainfall Intensity ⬆️** → **Flood Risk ⬆️** (positive correlation)  
  - **Storm Drain Proximity** (higher distance) → **Flood Risk ⬆️**  
  - **Drainage Density** (higher) → **Flood Risk ⬇️**  

This confirms that low elevation, sparse drainage, and high rainfall intensity are strong drivers of flooding risk.



### 9. Exploratory Data Analysis (EDA)
- **City Distribution**: Bar chart of top 10 cities.
- **Elevation**: Histogram showing most roads lie at lower elevations.
- **Land Use**: Pie chart of land use categories.
- **Risk Labels**: Frequency of each risk factor (e.g., `low_lying`, `sparse_drainage`).

---

### 10. Correlation Analysis
- Computed correlations among numeric features:
  - Elevation vs. rainfall
  - Drainage density vs. storm drain proximity
- Heatmap visualization showed weak/moderate correlations.

---

### 11. Feature Engineering
Created new features for better modeling:
- `rainfall_intensity_log`: log-transformed rainfall
- `is_low_lying`: binary flag for elevation ≤ 5m
- `drain_prox_bin`: binned storm drain proximity
- `drainage_sparse`: binary flag for low drainage density
- Catchment-level aggregates: mean elevation, rainfall, drainage density, segment count
- `spatial_cluster`: KMeans cluster on latitude/longitude

---

### 12. Data Transformation
- **Numerical features** → scaled using `StandardScaler`
- **Categorical features** → one-hot encoded using `OneHotEncoder`
- Built preprocessing pipeline with `ColumnTransformer`

---

### 13. Model Training
- Base model: **Random Forest Classifier** (200 trees, max depth = 12)
- Wrapped with `MultiOutputClassifier` to support multiple labels.
- Train-test split: 80% training, 20% testing.

---

### 14. Model Evaluation
- Used **F1-score** (better for imbalanced datasets).
- Results:
  - `monitor`: 0.999
  - `low_lying`: 0.996
  - `sparse_drainage`: 1.000
  - `ponding_hotspot`: 0.625
  - `extreme_rain_history`: 0.991
  - `event_YYYY-MM-DD`: 0.0 (rare labels, not enough samples)

👉 Average F1 = low due to rare event labels, but strong on general risk categories.

---

### 15. Feature Importance
- Top predictors: elevation, rainfall intensity (log), storm drain proximity
- Catchment-level rainfall & elevation were also strong signals.

---

### 16. Visualization – Ponding Hotspots
Mapped all `ponding_hotspot` segments on **OpenStreetMap**:

```python
mask = df['risk__ponding_hotspot'] == True
fig = px.scatter_mapbox(df[mask], lat='latitude', lon='longitude', hover_name='segment_id',
                        hover_data=['city_name','land_use','elevation_m','historical_rainfall_intensity_mm_hr'],
                        zoom=3, height=600)
fig.update_layout(mapbox_style='open-street-map', title='Ponding Hotspots Locations')
fig.show()
