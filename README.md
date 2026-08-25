# Electricity Demand Forecasting & Anomaly Detection

A time-series forecasting and anomaly detection project using historical **PJM East (PJME) hourly electricity demand data**. The project converts hourly electricity load into daily demand, performs statistical time-series analysis, compares multiple forecasting models, and detects unusual demand observations using residual-based Z-score analysis.

---

## 📌 Project Overview

Electricity demand changes over time due to weekly patterns, seasonal effects, consumer behavior, and other factors. Accurate demand forecasting can help in energy planning, resource allocation, and operational decision-making.

This project develops an end-to-end forecasting and anomaly detection workflow using historical **PJME electricity demand data**.

The project compares four forecasting approaches:

* Seasonal Naive Forecasting
* ARIMA
* SARIMA
* XGBoost Regression

In addition, **residual-based Z-score anomaly detection** is applied to identify unusually high or low electricity demand observations.

---

## 🎯 Objectives

The main objectives of this project are:

1. Load and preprocess historical hourly electricity demand data.
2. Handle duplicate timestamps and convert the data into a time-series format.
3. Aggregate hourly electricity load into daily electricity demand.
4. Analyze the time series using visualization and seasonal decomposition.
5. Study autocorrelation using ACF and PACF.
6. Test stationarity using ADF and KPSS tests.
7. Build and compare statistical and machine-learning forecasting models.
8. Evaluate forecasting performance using MAE, RMSE, and MAPE.
9. Detect unusual demand observations using residual-based Z-score analysis.

---

## 📊 Dataset

The project uses the **PJME hourly electricity consumption dataset**, containing historical electricity demand measurements for the PJM East (PJME) region.

### Dataset File

```text
PJME_hourly.csv
```

### Columns

| Column     | Description                                        |
| ---------- | -------------------------------------------------- |
| `Datetime` | Timestamp of the electricity demand observation    |
| `PJME_MW`  | Electricity demand/load measured in megawatts (MW) |

The raw dataset contains approximately **145K+ hourly observations**.

The notebook removes duplicate timestamps, converts the timestamp column to datetime format, and aggregates the hourly load into daily electricity demand.

The analysis uses the complete years **2015–2017**, resulting in:

```text
1096 daily observations
```

Daily demand is calculated by summing the hourly `PJME_MW` values for each day.

---

## 🔄 Project Workflow

```text
PJME Hourly Electricity Data
            ↓
       Data Loading
            ↓
      Data Cleaning
            ↓
   Datetime Conversion
            ↓
    Duplicate Removal
            ↓
 Hourly → Daily Aggregation
            ↓
  2015–2017 Data Selection
            ↓
 Exploratory Data Analysis
            ↓
 Seasonal Decomposition
            ↓
      ACF & PACF
            ↓
   ADF & KPSS Tests
            ↓
     Train / Test Split
            ↓
   ┌───────────────────────┐
   │   Forecasting Models  │
   ├───────────────────────┤
   │ Seasonal Naive        │
   │ ARIMA                 │
   │ SARIMA                │
   │ XGBoost               │
   └───────────────────────┘
            ↓
    Model Evaluation
            ↓
   Forecast Comparison
            ↓
 Residual-Based Z-Score
   Anomaly Detection
```

---

## 🔬 Exploratory Data Analysis

### Seasonal Decomposition

An additive seasonal decomposition with a **7-day period** is performed to separate the daily electricity demand series into:

* Observed
* Trend
* Seasonal
* Residual

The 7-day period is used to investigate weekly demand patterns.

### ACF & PACF

Autocorrelation Function (ACF) and Partial Autocorrelation Function (PACF) are used to examine the dependence between electricity demand observations at different time lags.

These plots help understand temporal dependence and support model selection.

---

## 📉 Stationarity Analysis

Two statistical tests are used to examine the stationarity of the daily electricity demand series.

### Augmented Dickey-Fuller (ADF) Test

```text
ADF Statistic = -3.1672
p-value       = 0.0220
```

### KPSS Test

```text
KPSS Statistic = 0.1891
p-value        = 0.1000
```

The ADF and KPSS tests provide complementary evidence about the stationarity characteristics of the series before forecasting.

---

## 🧪 Train-Test Split

The final **30 days** are reserved as an unseen test set.

```text
Total observations = 1096 days
Training set       = 1066 days
Testing set        = 30 days
Forecast horizon   = 30 days
```

All forecasting models are evaluated on the same 30-day test period.

---

# 📈 Forecasting Models

## 1. Seasonal Naive Forecasting

Seasonal Naive forecasting is used as a baseline model.

The model uses the demand pattern from the previous week to forecast the next 30 days.

This provides a simple benchmark for comparison with more advanced forecasting methods.

---

## 2. ARIMA

An automatic ARIMA model is fitted using `auto_arima`.

ARIMA captures temporal dependencies through:

* Autoregressive terms
* Differencing
* Moving-average terms

The model generates forecasts for the 30-day test period.

---

## 3. SARIMA

A Seasonal ARIMA model is fitted with a weekly seasonal period:

```text
m = 7
```

SARIMA extends ARIMA by explicitly incorporating seasonal patterns, making it suitable for electricity demand data with weekly behavior.

---

## 4. XGBoost

XGBoost is used as a machine-learning-based forecasting approach.

The following features are created:

### Calendar Features

* Day of week
* Month

### Lag Features

* Lag 1
* Lag 7
* Lag 14

### Rolling Feature

* 7-day rolling mean

The model generates forecasts recursively over the 30-day test period.

---

# 📊 Model Evaluation

The forecasting models are evaluated using:

### MAE

Mean Absolute Error measures the average absolute difference between actual and predicted electricity demand.

### RMSE

Root Mean Squared Error gives greater weight to larger forecasting errors.

### MAPE

Mean Absolute Percentage Error measures forecasting error in percentage terms.

---

## Model Comparison

| Model          |           MAE |           RMSE |       MAPE |
| -------------- | ------------: | -------------: | ---------: |
| **ARIMA**      | **85,117.68** | **108,558.83** | **10.04%** |
| SARIMA         |     89,510.49 |     115,711.81 |     10.52% |
| Seasonal Naive |    109,867.43 |     136,883.25 |     13.06% |
| XGBoost        |    114,851.83 |     142,211.86 |     13.66% |

### Best Performing Model

**ARIMA achieved the best overall forecasting performance**, obtaining the lowest MAE, RMSE, and MAPE among the four tested models.

```text
Best Model : ARIMA
MAE        : 85,117.68
RMSE       : 108,558.83
MAPE       : 10.04%
```

---

# 🚨 Anomaly Detection

A **residual-based Z-score approach** is used to identify unusual electricity demand observations.

The residuals obtained from the seasonal decomposition are standardized using:

```text
Z = (Residual - Mean Residual) / Standard Deviation
```

Observations satisfying:

```text
|Z| > 3
```

are classified as potential anomalies.

### Result

```text
Detected potential anomalies = 8
Z-score threshold             = |Z| > 3
```

The detected points represent unusually high or low electricity demand relative to the residual behavior of the decomposed time series.

---

# 📌 Key Results

* Analyzed **1096 daily electricity demand observations** from 2015–2017.
* Performed **7-day seasonal decomposition** to study weekly demand patterns.
* Applied **ACF and PACF** analysis to examine temporal dependencies.
* Conducted **ADF and KPSS stationarity tests**.
* Compared **Seasonal Naive, ARIMA, SARIMA, and XGBoost** forecasting models.
* **ARIMA achieved the best performance** with MAE of **85,117.68**, RMSE of **108,558.83**, and MAPE of **10.04%**.
* Applied **residual-based Z-score anomaly detection** with a threshold of **|Z| > 3**.
* Detected **8 potential electricity demand anomalies**.

---

# 💼 Business Applications

Electricity demand forecasting and anomaly detection can support:

* Power generation planning
* Energy resource allocation
* Grid management
* Demand planning
* Operational decision-making
* Identification of unusual consumption patterns
* Energy efficiency analysis

---

# 🛠️ Technologies & Libraries

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
* Residual-Based Z-score Anomaly Detection

---

# 📁 Repository Structure

```text
Electricity_Demand_Forecasting_Anomaly_Detection/
│
├── Electricity-demand-forecasting-anomaly-detection.ipynb
├── PJME_hourly.csv
├── README.md
└── LICENSE
```

---

# ▶️ How to Run

## Option 1: Google Colab

1. Open the notebook in Google Colab.
2. Upload `PJME_hourly.csv` to the Colab environment.
3. Run the notebook cells sequentially.
4. The notebook performs preprocessing, EDA, forecasting, evaluation, and anomaly detection.

The notebook expects the dataset at:

```python
/content/PJME_hourly.csv
```

## Option 2: Local Python Environment

Clone the repository:

```bash
git clone https://github.com/sejal2510-sv/Electricity_Demand_Forecasting_Anomaly_Detection.git
```

Move into the project directory:

```bash
cd Electricity_Demand_Forecasting_Anomaly_Detection
```

Install the required libraries:

```bash
pip install numpy pandas matplotlib statsmodels pmdarima xgboost scikit-learn
```

Open the notebook using Jupyter Notebook, JupyterLab, VS Code, or another compatible environment.

---

# 📄 License

This project is licensed under the MIT License.

---

# 👩‍💻 Author

**Sejal Verma**

M.Sc. Statistics

This project was developed as a practical application of statistical time-series analysis and machine learning for electricity demand forecasting and anomaly detection.
