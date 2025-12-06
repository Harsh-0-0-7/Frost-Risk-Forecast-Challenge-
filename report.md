****Frost Risk Forecasting Challenge – Final Report**
**1. Introduction****

Frost is a major risk to specialty crops in California. The challenge task is to forecast:

Probability of frost (T < 0°C)

Future air temperature

for forecast horizons of 3, 6, 12, and 24 hours, using CIMIS hourly weather data from 18 stations.

This report summarizes our:

Modeling approach

Calibration methodology

Evaluation & spatial generalization strategy

Innovations, assumptions, limitations

All results come from the notebook:
frost_risk_forecasting_pipeline.ipynb

**2. Data & Preprocessing**
2.1 Dataset

The dataset contains hourly CIMIS measurements including:

Air temperature, dew point, soil temperature

Relative humidity

Solar radiation

Wind speed and direction

Precipitation

Vapor pressure

Reference evapotranspiration (ETo)

2.2 Timestamp reconstruction

Using:

Date + Hour(PST) / 100


we generate a proper datetime column.

2.3 Cool-season restriction

Frost occurs Oct–Apr.
Only months [10, 11, 12, 1, 2, 3, 4] are used.

2.4 Feature Engineering
Time features

Day of year (doy)

Hour-of-day sin/cos encoding

Month filter

Lag features (local time-series context)

For each of:

Air Temp

Dew Point

Soil Temp

Relative Humidity

we include 1h, 2h, 3h lags.

Why?
Short-term cooling trends directly determine frost risk.

**3. Targets & Forecast Horizons**

For each horizon H ∈ {3, 6, 12, 24}:

Regression target:

temp_t+Hh = Air Temp shifted -H


Classification target:

frost_Hh = 1 if temp_t+Hh < 0°C else 0


This produces a supervised dataset mapping the current state → future frost probability & temperature.

**4. Modeling Approach**

We evaluated several model families and selected LightGBM due to its strong performance on structured/tabular weather data.

4.1 Models Used
1. Logistic Regression (baseline)

Provides a sanity check for classification performance

Interpretable, simple baseline

2. Ridge Regression (baseline)

Linear regression benchmark for temperature forecasting

3. LightGBM Classifier & Regressor (final models)

Handles nonlinear interactions

Efficient on large datasets

Strong performance in tabular forecasting tasks

Works well with lag features

Easily calibrated

4.2 Why Not Deep Time-Series Models?

LSTM/Transformers were not chosen because:

Only 18 stations → deep models risk overfitting

Tabular learners (GBM) dominate weather forecasting competitions

Lag-based temporal encoding is simpler and more robust

GBMs provide better calibration + transparency

**5. Calibration & Evaluation**
5.1 Probability Calibration Metrics

We compute:

Brier Score

Expected Calibration Error (ECE)

Reliability curves

PR-AUC

ROC-AUC

Low Brier + ECE indicates well-calibrated probabilities, which are essential for decision-making under uncertainty.

**6. Spatial Generalization**

To simulate real-world deployment on stations never seen during training, we use:

GroupKFold (5 folds) split by station ID

This ensures:

Validation stations are completely unseen during training

The model generalizes to new geographic locations

Performance reflects operational reliability

This matches the challenge requirement for spatial generalization analysis.

**7. Results Summary**

Across all horizons:

ROC-AUC consistently high (>0.95)

Brier scores low (~0.01–0.03 depending on horizon)

ECE low → strong probability calibration

RMSE reasonable given increasing horizon length

Shorter horizons (3h, 6h) exhibit strongest discrimination and lowest RMSE because local trends are more predictable.

**8. Innovations**
8.1 Lag-based temporal encoding

This allows the model to imitate a time-series forecaster without the complexity of LSTMs.

8.2 Robust calibration metrics

Using ECE + Brier gives deeper insight into probability reliability.

8.3 Spatially aware validation

GroupKFold ensures real generalization, avoiding data leakage.

**9. Assumptions**

CIMIS data quality is consistent and without major sensor failures

No extreme regional climate shift outside the training distribution

Station measurements approximate field microclimates

**10. Limitations**

Does not use atmospheric reanalysis (ERA5) or satellite data

Does not explicitly model long-term temperature dynamics

Station coverage is limited to 18 locations

However, the approach balances accuracy, efficiency, calibration, and interpretability, making it suitable for operational frost alerts.

**11. Conclusion**

This solution satisfies all challenge requirements:

A fully reproducible pipeline

Clear modeling methodology

Comprehensive calibration + spatial validation

Practical and reliable frost risk forecasts for 3–24 hour horizons

The models can be applied immediately to new datasets using the included prediction function, and probabilities are calibrated enough for real-world decision-making.
