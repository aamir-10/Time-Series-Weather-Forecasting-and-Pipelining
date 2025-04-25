# ⚡ Power Consumption Forecast Pipeline System

## 📌 Introduction and Objective

The objective of this project was to utilize historical information from the American Electric Power (AEP) area to develop a reliable pipeline for estimating power usage along a 168-hour lead. The primary purpose was to develop, evaluate, and compare time-series models to forecast mean load and volatility and provide meaningful information for energy planning. 

This project includes:
- Advanced time-series modeling (ARIMA, SARIMA, Exponential Smoothing, GARCH)
- Interactive forecast visualization using **Streamlit**
- Handling and transformation of long-term electricity usage data
- A robust evaluation pipeline for short-term (7-day) electricity consumption prediction

---

## 🧹 Data Collection and Preprocessing

- **Source**: `AEP_hourly.csv` with 121,273 hourly records from Jan 10, 2004 to Aug 3, 2018
- **Cleaning**:
  - Removed 4 duplicate records
  - Converted `Datetime` to proper datetime format
  - Retained 259 outliers (>3σ) for modeling
- **Feature Engineering**:
  - Created features: `Hour`, `IsWeekend`, `Lag_1`, `IsSummer`
  - Interpolated 27 missing hours due to DST → final row count: **121,296**
- **Exported**: `interpolated_dataset.pkl` for modeling

---

## 📈 Time Series Modeling and Diagnostics

### ➤ ARIMA(1,1,1)
- AIC: 1,747,740  
- Residual Std Dev: 327.54  
- Baseline model (no seasonality)

### ➤ SARIMA(1,1,1)(1,1,1,24)
- AIC: 1,602,426  
- Residual Std Dev: 183.81  
- Captured daily cycles (24-hour seasonality)

### ➤ Exponential Smoothing (Additive Trend + Seasonality)
- AIC: **1,385,098** (lowest)
- Period: 24 hours  
- Warning: Model convergence required adjustment

### ➤ GARCH(1,1) on Residuals
- AIC: -70.37  
- Rescaled input (AEP_MW / 1000)  
- Volatility Range: 1,000 – 1,500 MW  
- β₁ = 0.0178 → low, but stable volatility model

📌 **Stationarity Tests**:
- ADF p = 2.34e-30 (stationary)
- KPSS p = 0.01 (non-stationary)  
→ Justified differencing

---

## 🔮 Forecasting and Evaluation

- Forecast window: **Aug 3 – Aug 10, 2018 (168 hours)**
- **Exponential Smoothing**:
  - Best AIC (1,385,098)
- **SARIMA**:
  - Best Residual Std. Dev. (183.81)
- **GARCH**:
  - Forecasted volatility: **6–12%** of mean load (12,000–17,000 MW)
- **ARIMA**:
  - Lagged behind due to no seasonality

📉 12-month forecast dropped due to lack of post-2018 data  
✅ Validation based on in-sample metrics

---

## 🧪 Notebook Implementation Overview

Includes:
- Data cleaning, lag features, interpolation
- ADF/KPSS stationarity tests
- Model fitting via `statsmodels` and `arch`
- 168-hour forecasting with:
  - ARIMA / SARIMA
  - Exponential Smoothing
  - GARCH volatility model
- Forecast visualization and Streamlit preparation

---

## 📊 Interactive Dashboard

Streamlit-based app includes:
- Horizon slider for adjusting forecast window
- 168-hour forecast and volatility plot
- Forecast and volatility pickled for deployment

---

## 🧠 Discussion

- **Exponential Smoothing** outperformed in AIC
- **SARIMA** captured strong daily patterns
- **GARCH** accurately modeled power volatility  
- Minor convergence issues solvable via tuning
- Future improvements:
  - Use external features (temperature, holiday flags)
  - Auto-tune models (`pmdarima.auto_arima`)
  - Cloud deployment for live usage

---

## ✅ Conclusion

With an interactive dashboard and a reliable 168-hour forecast, this project delivers a strong foundation for short-term power load prediction:

- 📉 **GARCH** → Volatility
- 📈 **SARIMA + Exponential Smoothing** → Mean load
- 🌐 Dashboard ready for extension and hosting

---
