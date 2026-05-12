# Grocery Sales Forecasting & Analytics Pipeline

## Project Overview

This project is an end-to-end machine learning and analytics pipeline built using the Favorita Grocery Sales Forecasting dataset. The objective of the project is to predict grocery sales across multiple stores using time-series forecasting techniques and prepare analytical datasets for Power BI visualization.

The project covers the complete data science workflow including:

* Data extraction and preprocessing
* Data cleaning and transformation
* Feature engineering
* Machine learning model training
* Forecast generation
* Business intelligence reporting preparation

The pipeline was designed to handle large-scale transactional data containing over 125 million sales records.

---

## Objectives

* Forecast grocery sales for multiple stores
* Build scalable preprocessing and feature engineering workflows
* Improve prediction accuracy using time-series features
* Generate datasets suitable for Power BI dashboards and business reporting
* Practice real-world machine learning and analytics workflows on large datasets

---

# Technologies Used

* Python
* Pandas
* NumPy
* XGBoost
* Scikit-learn
* Jupyter Notebook
* Power BI
* py7zr

---

# Dataset

The project uses the **Favorita Grocery Sales Forecasting** dataset from Kaggle.

Datasets included:

* train.csv
* test.csv
* stores.csv
* items.csv
* holidays_events.csv
* transactions.csv
* oil.csv
* sample_submission.csv

---

# Project Workflow

## 1. Data Extraction & Loading

* Extracted compressed `.7z` files using `py7zr`
* Loaded datasets into Pandas DataFrames
* Performed exploratory data inspection and null value analysis

### Key Findings

* `train.csv` contained over 125 million rows
* Missing values identified in `onpromotion`
* Large-scale dataset required memory-conscious preprocessing

---

## 2. Data Cleaning

### Train & Test Dataset

* Converted `date` columns to datetime format
* Filtered data to the most recent 12 months
* Handled negative sales values
* Applied `log1p` transformation to reduce skewness

### Stores Dataset

* Verified store metadata integrity
* No missing values detected

### Items Dataset

* Converted categorical fields for memory optimization
* Checked data consistency

### Holidays Dataset

* Removed transferred holidays
* Created holiday indicator flags
* Removed duplicate holiday dates

---

## 3. Data Merging

Merged datasets into a unified analytical dataset:

* Sales data
* Store metadata
* Item metadata
* Holiday information

### Missing Value Handling

* Filled holiday indicators with `0`
* Filled missing promotion values
* Preserved test target values as null

---

## 4. Feature Engineering

### Date Features

Generated:

* Year
* Month
* Day
* Day of week

### Lag Features

Created:

* `lag_7`
* `lag_14`

### Rolling Features

Created:

* `rolling_mean_7`

### Missing Value Strategy

Implemented hierarchical filling:

1. Same store + same item median
2. Same item median
3. Same store median
4. Global median

### Encoding

Applied one-hot encoding to categorical variables:

* Family
* City
* State
* Store type

---

## 5. Model Training

### Machine Learning Model

Used:

* XGBoost Regressor

### Training Strategy

* Trained separate models for each store
* Total models trained: 54

### Features Used

Excluded:

* `unit_sales`
* `date`
* `id`
* `store_nbr`

### Hyperparameters

```python
n_estimators = 100
max_depth = 5
learning_rate = 0.1
subsample = 0.8
colsample_bytree = 0.7
```

---

# 6. Forecasting & Export

## Prediction Pipeline

* Generated predictions for each store
* Converted predictions back from log scale
* Aggregated forecasts by:

  * Store
  * Date
  * Product family

## Power BI Export

Generated:

* `powerbi_data.csv`
* `powerbi_data1.csv`

Prepared datasets for:

* Actual vs predicted sales analysis
* Store performance dashboards
* Product family trend analysis

---

# Power BI Dashboard Features

The exported datasets support dashboards such as:

* Sales forecasting by store
* Product family performance
* Trend analysis over time
* Actual vs predicted comparison
* Operational sales insights

---

# Key Skills Demonstrated

* Machine Learning
* Time-Series Forecasting
* Data Engineering
* Feature Engineering
* Data Cleaning
* Predictive Analytics
* Business Intelligence
* Large-Scale Data Processing
* Power BI Reporting

---

# Results

* Successfully processed and modeled over 125 million sales records
* Built scalable forecasting workflows
* Generated analytical outputs for business intelligence reporting
* Developed store-specific predictive models using XGBoost

---

# Future Improvements

* Hyperparameter optimization
* Cross-validation for time-series forecasting
* Deployment using Flask or FastAPI
* Real-time prediction dashboards
* Deep learning forecasting models (LSTM/Transformer)

---


