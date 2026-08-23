# Electricity Demand Forecasting & Anomaly Detection

A time-series forecasting and anomaly detection project using historical electricity consumption data. The project compares **Seasonal Naive, ARIMA, SARIMA, and XGBoost** forecasting approaches and uses residual-based analysis to identify unusual electricity demand observations.

---

## 📌 Project Overview

Electricity demand varies over time due to factors such as day of the week, seasonal patterns, consumer behavior, and other external effects. Accurate electricity demand forecasting can support energy planning, resource allocation, and operational decision-making.

This project develops a **time-series forecasting and anomaly detection system** using historical electricity consumption data.

The project compares four forecasting approaches:

* Seasonal Naive Forecasting
* ARIMA
* SARIMA
* XGBoost with time-series lag features

In addition, **residual-based anomaly detection** is performed to identify unusual electricity demand observations.

---

## 🎯 Objectives

The main objectives of this project are:

1. Load and preprocess historical electricity consumption data.
2. Convert high-frequency electricity measurements into daily electricity demand.
3. Explore the time series using visualization and seasonal decomposition.
4. Analyze autocorrelation and partial autocorrelation.
5. Test the stationarity of the time series.
6. Build and compare different forecasting models.
7. Evaluate forecasting performance using MAE, RMSE, and MAPE.
8. Detect unusual electricity demand observations using residual-based anomaly detection.
9. Identify an effective forecasting approach based on model performance.

---

## 📊 Dataset

This project uses the **Electricity Load Diagrams 2011–2014 dataset** from the UCI Machine Learning Repository.

The dataset contains electricity consumption measurements recorded at short time intervals for multiple customers.

The dataset is automatically downloaded from UCI:

https://archive.ics.uci.edu/static/public/321/electricityloaddiagrams20112014.zip

The original measurements are aggregated across customers and converted into daily electricity demand measured in **MWh**.

The analysis uses the complete years **2012–2014**, resulting in:

```text
1096 daily observations
```

---

## 🛠️ Technologies & Libraries

### Programming Language

* Python

### Libraries

* NumPy
* Pandas
* Matplotlib
* Statsmodels
* pmdarima
* XGBoost
* Scikit-learn

### Statistical & Machine Learning Techniques

* Time Series Analysis
* Seasonal Decomposition
* ACF
* PACF
* Augmented Dickey-Fuller (ADF) Test
* KPSS Test
* ARIMA
* SARIMA
* XGBoost Regression
* Lag Feature Engineering
* Rolling Mean
* MAE
* RMSE
* MAPE
* Residual-Based Anomaly Detection

---

## 🔄 Project Workflow

```text
Raw Electricity Data
        ↓
Data Loading
        ↓
Data Cleaning
        ↓
Datetime Conversion
        ↓
Aggregation Across Customers
        ↓
Daily Electricity Demand
        ↓
Exploratory Data Analysis
        ↓
Seasonal Decomposition
        ↓
ACF & PACF Analysis
        ↓
Stationarity Testing
        ↓
Train / Test Split
        ↓
┌─────────────────────────────────┐
│        Forecasting Models       │
├─────────────────────────────────┤
│ Seasonal Naive                  │
│ ARIMA                           │
│ SARIMA                          │
│ XGBoost                         │
└─────────────────────────────────┘
        ↓
Model Evaluation
        ↓
Forecast Comparison
        ↓
Residual-Based Anomaly Detection
```

---

# 📈 Forecasting Models

## 1. Seasonal Naive Forecasting

A seasonal naive model is used as the baseline forecasting approach.

The model repeats the electricity demand pattern from the previous week to forecast the next 30 days.

This provides a simple benchmark against which the more advanced forecasting models can be compared.

---

## 2. ARIMA

An automatic ARIMA model is fitted using `auto_arima`.

ARIMA models capture temporal dependencies in the electricity demand series through autoregressive, differencing, and moving-average components.

The model is used to generate a 30-day forecast.

---

## 3. SARIMA

A seasonal ARIMA model is fitted with a weekly seasonal period:

```text
m = 7
```

SARIMA extends ARIMA by explicitly modeling seasonal patterns.

This makes it suitable for electricity demand data where weekly patterns may be present.

---

## 4. XGBoost Forecasting

XGBoost is used as a machine-learning-based forecasting model.

Several time-series features are created.

### Calendar Features

* Day of week
* Month

### Lag Features

* Lag 1
* Lag 7
* Lag 14

### Rolling Feature

* 7-day rolling mean

The XGBoost model is then used to generate forecasts recursively over the 30-day test period.

---

# 🔬 Exploratory Data Analysis

The project analyzes the electricity demand series using several time-series techniques.

## Seasonal Decomposition

An additive seasonal decomposition with a **7-day period** is performed to separate the series into:

```text
Observed
Trend
Seasonal
Residual
```

This helps identify weekly patterns and irregular fluctuations.

## ACF and PACF

Autocorrelation Function (ACF) and Partial Autocorrelation Function (PACF) plots are used to study the dependence between observations at different lags.

---

# 📉 Stationarity Analysis

Two statistical tests are used to investigate stationarity.

## Augmented Dickey-Fuller Test

The ADF test is used to test for the presence of a unit root.

For the analyzed series:

```text
ADF Statistic = -1.8519
p-value       = 0.3550
```

## KPSS Test

The KPSS test is also used to assess stationarity.

For the analyzed series:

```text
KPSS Statistic = 0.4440
p-value        = 0.0582
```

These tests provide complementary evidence about the time-series behavior before forecasting.

---

# 🧪 Train-Test Split

The final **30 days** are reserved as the test set.

```text
Training observations = 1066 days
Testing observations  = 30 days
Forecast horizon      = 30 days
```

The forecasting models are trained using the historical training data and evaluated on the unseen 30-day test period.

---

# 📊 Model Evaluation

The forecasting models are evaluated using three metrics.

### MAE

**Mean Absolute Error** measures the average absolute difference between actual and predicted demand.

### RMSE

**Root Mean Squared Error** gives greater importance to larger prediction errors.

### MAPE

**Mean Absolute Percentage Error** measures prediction error in percentage terms.

---

## Model Comparison

| Model          |    MAE |       RMSE |      MAPE |
| -------------- | -----: | ---------: | --------: |
| Seasonal Naive | 170.30 | **399.22** | **4.75%** |
| XGBoost        | 190.11 |     405.25 |     5.14% |
| ARIMA          | 179.20 |     408.82 |     4.97% |
| SARIMA         | 174.68 |     417.92 |     4.91% |

Based on RMSE, the **Seasonal Naive model achieved the lowest error** among the tested models for this particular 30-day test period.

XGBoost achieved competitive performance, while ARIMA and SARIMA provided additional statistical forecasting benchmarks.

---

# 🚨 Anomaly Detection

A residual-based anomaly detection approach is implemented.

The residual component obtained from the seasonal decomposition is standardized using a Z-score:

```text
Z = (Residual - Mean Residual) / Standard Deviation
```

Observations satisfying:

```text
|Z| > 3
```

are classified as potential anomalies.

The analysis detected:

```text
10 potential anomaly points
```

These observations represent unusually high or low electricity demand relative to the residual behavior of the decomposed series.

---

# 📌 Key Results

The project demonstrates a complete workflow for electricity demand forecasting and anomaly detection.

* **1096** daily observations were analyzed.
* Weekly seasonality was investigated using decomposition and autocorrelation analysis.
* ADF and KPSS tests were used for stationarity assessment.
* Four forecasting approaches were compared.
* The final 30 days were used as an out-of-sample test set.
* **Seasonal Naive achieved the lowest RMSE of 399.22.**
* XGBoost achieved an RMSE of **405.25**.
* Residual-based anomaly detection identified **10 potential anomalies**.

---

# 💼 Business Applications

Electricity demand forecasting and anomaly detection can support:

* Power generation planning
* Energy resource allocation
* Grid management
* Demand planning
* Operational decision-making
* Identification of unusual consumption behavior
* Energy efficiency analysis

---

# 🚀 Future Improvements

The current project can be extended by incorporating:

* Temperature and weather variables
* Holidays and special events
* Public holidays
* Hourly forecasting
* Prophet or other forecasting models
* LightGBM/CatBoost comparison
* Hyperparameter tuning
* Walk-forward validation
* Prediction intervals
* More advanced anomaly detection techniques
* Real-time electricity demand forecasting

---

# 📁 Repository Structure

```text
electricity-demand-forecasting-anomaly-detection/
│
├── Electricity_Demand_Forecasting.ipynb
├── README.md
├── requirements.txt
├── .gitignore
│
└── results/
    ├── demand_forecast.png
    └── anomaly_detection.png
```

---

# ▶️ How to Run the Project

## Option 1: Google Colab

Open the notebook in Google Colab and run the cells sequentially.

The notebook automatically downloads the required electricity dataset.

## Option 2: Local Python Environment

Clone the repository:

```bash
git clone https://github.com/YOUR_USERNAME/electricity-demand-forecasting-anomaly-detection.git
```

Move into the project directory:

```bash
cd electricity-demand-forecasting-anomaly-detection
```

Install the required packages:

```bash
pip install -r requirements.txt
```

Open the notebook using Jupyter Notebook or VS Code.

---

# 📦 Requirements

Create a `requirements.txt` file containing:

```text
numpy
pandas
matplotlib
statsmodels
pmdarima
xgboost
scikit-learn
```

---

# 👩‍💻 Author

**Sejal Verma**

*M.Sc. Statistics*

This project was developed as a practical application of statistical time-series analysis and machine learning for electricity demand forecasting and anomaly detection.
