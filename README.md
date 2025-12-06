# Frost-Risk-Forecast-Challenge-
This Repository is made for the Frost Risk Forecast Challenge
**Environment & Dependencies**
pandas
numpy
scikit-learn
lightgbm
joblib
matplotlib
Python version recommended : 3.10-3.12

**How to Run this code?**

**1. Launch Jupyter**
jupyter notebook

**2. Open:**
frost_risk_forecasting_pipeline.ipynb

**3. Place the dataset:**

Ensure the file is in the same folder:

cimis_all_stations_clean.csv

**4. Run all cells**

The notebook will:

Load & clean the dataset

Build features (time-of-day, lags, seasonal predictors)

Train models for forecast horizons 3h, 6h, 12h, 24h

Perform GroupKFold spatial cross-validation

Compute classification & regression metrics:

Brier Score

Expected Calibration Error (ECE)

ROC-AUC

PR-AUC

RMSE

Train final LightGBM models

Predict on new datasets using the provided function:

predict_for_dataset(csv_path, models_by_horizon, feature_cols)

**5. Outputs**

A printed metrics table for each horizon

A dictionary of models for each horizon

Predictions DataFrame containing:

frost_prob_3h, temp_pred_3h, target_time_3h

frost_prob_6h, …

frost_prob_12h, …

frost_prob_24h, …

You may save predictions manually via:

preds.to_csv("predictions.csv", index=False)

**🧪 Spatial Generalization**

The pipeline automatically uses GroupKFold split by station, ensuring:

No station appears in both train & validation sets

Evaluation closely reflects real-world deployment to unseen stations
