# Carbon Estimation with Machine Learning (Part 2)

## Context

In my [previous work](https://github.com/camillefagbedji69-cpu/carbone_ml_shap), carbon estimation was heavily driven by Land Use/Land Cover (LULC) data. While effective, that approach risked a "tautological" bias, as the InVEST framework calculates carbon primarily as a linear function of land classes and areas.

In this second part, I challenge the model to predict carbon storage using **exclusively biophysical and climatic variables**. By removing LULC data, the objective is to uncover the underlying environmental drivers of carbon sequestration and build a more "process-oriented" predictive tool.

## Methodology

* **Data:** Carbon stock was first estimated via the InVEST Carbon Storage model, then aggregated on a  km² grid.
* **Features:** * **Climatic:** Annual Precipitations, Land Surface Temperature (LST).
* **Biophysical:** NDVI (Vegetation greenness), NDWI (Water content/Stress).


* **Spatial Validation:** To account for spatial autocorrelation, I implemented a **GroupKFold cross-validation** using the grid ID (`plot_id`), ensuring the model generalizes to unseen geographical areas rather than just memorizing locations.
* **Pipeline:** Systematic use of `StandardScaler` within a machine learning pipeline to handle varying feature scales.

## Key Results

### 1. Model Performance

| Models | $R^2$ | RMSE (tC/ha) |
| --- | --- | --- |
| **Random Forest** | **0.72** | **212.52** |
| XGBoost | 0.62 | 247.94 |
| AdaBoost | 0.45 | 296.00 |
| Linear Regression | 0.21 | 354.64 |

The **Random Forest** model outperformed others, capturing the non-linear relationships between climate and biomass.

### 2. Interpreting the Drivers (SHAP Analysis)

| Variables | Importance (SHAP) | Ecological Role |
| --- | --- | --- |
| **Precipitations** | **+222.40** | Main driver of Primary Productivity. |
| **LST** | **-76.18** | Thermal stress; accelerates SOM mineralization. |
| **NDWI** | **-31.97** | Ecohydrological trade-off (Water consumption). |
| **NDVI** | +4.93 | Secondary proxy for instantaneous greenness. |

## Scientific Discussion

* **The dominance of Water:** Precipitation is the primary limiting factor for carbon sequestration in the study area.
* **The Thermal Brake:** The negative impact of LST aligns with the literature (Robert & Saugier, 2003), where high temperatures accelerate the decomposition of soil organic matter (SOC).
* **The Carbon-Water Trade-off:** The negative importance of NDWI reflects the "cost of growth." High carbon sequestration often correlates with higher evapotranspiration, reducing available surface water—a trade-off well-documented in forest hydrology (McNulty et al., 2012).
