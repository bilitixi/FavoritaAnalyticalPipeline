# Grocery Sales Forecasting with XGBoost

## Project Overview

This project focuses on forecasting grocery sales using the **Corporación Favorita Grocery Sales Forecasting Dataset**. The workflow includes data extraction, cleaning, feature engineering, model training using **XGBoost**, and exporting prediction results for **Power BI visualization**.

The project demonstrates a complete end-to-end machine learning pipeline for handling large-scale retail sales data. 

*Note that this README.md file explain the steps executed in prediction-pipeline.ipynb*

---

# Project Workflow

## I. Import Libraries

The following libraries were installed and imported:

* `pandas`
* `numpy`
* `os`
* `shutil`
* `py7zr`
* `xgboost`
* `sklearn.metrics`

### Additional Setup

* Installed `py7zr` to extract `.7z` compressed dataset files.

---

# II. Load Dataset

## 1. Convert Dataset Files to CSV

All compressed `.7z` files from the Favorita dataset were extracted, including:

* `train`
* `test`
* `stores`
* `items`
* `holidays_events`
* `transactions`
* `oil`
* `sample_submission`

---

## 2. Load CSV Files into DataFrames

Datasets loaded into pandas DataFrames:

* `train.csv`
* `test.csv`
* `stores.csv`
* `items.csv`
* `holidays_events.csv`

### Dataset Inspection

Performed:

* Shape inspection
* Preview using `.head()`
* Data type checking with `.info()`
* Missing value analysis

### Key Findings

* `train.csv` contained:

  * **125,497,040 rows**
  * **6 columns**
* Found:

  * **21,657,651 missing values** in the `onpromotion` column

---

# III. Data Cleaning

## 3. Clean Train and Test Datasets

### Operations Performed

* Converted `date` column to datetime format
* Filtered dataset to only include the last year of records:

  ```python
  cutoff = max(date) - 365 days
  ```
* Replaced negative sales values with `0`
* Applied logarithmic transformation using:

  ```python
  log1p(unit_sales)
  ```

  to reduce skewness
* Converted test dataset dates to datetime format

---

## 4. Clean Stores Dataset

### Dataset Information

* Total stores: **54**
* Columns:

  * `store_nbr`
  * `city`
  * `state`
  * `type`
  * `cluster`

### Result

* No missing values found

---

## 5. Clean Items Dataset

### Dataset Information

* Total items: **4,100**
* Columns:

  * `item_nbr`
  * `family`
  * `class`
  * `perishable`

### Operations

* Checked for missing values
* Converted `family` column to `category` dtype for memory optimization

### Result

* No missing values found

---

## 6. Clean Holidays Dataset

### Operations Performed

* Converted `date` column to datetime
* Removed transferred holidays
* Created holiday indicator feature:

  ```python
  is_holiday
  ```
* Removed duplicate holiday dates

### Result

* Found and removed **38 duplicated holiday dates**

---

# IV. Data Merging and Preparation

## 7. Merge Datasets

### Steps Performed

* Added missing `unit_sales` column to test dataset
* Filled test `unit_sales` with `NA`
* Combined train and test datasets

### Merged With

* `stores`
* `items`
* `holidays_events`

### Missing Value Handling

* Filled missing `is_holiday` values with `0`
* Filled missing `onpromotion` values with `"None"`

### Duplicate Check

Checked duplicates using:

```python
[date, store_nbr, item_nbr]
```

### Results

* No duplicates found
* Final merged dataset:

  * **40,825,299 rows**
  * **14 columns**
* Missing values remaining:

  * `3,370,464` missing `unit_sales` values (from test dataset)

---

## 8. Save Cleaned Dataset

* Created output directory
* Saved cleaned merged dataset as CSV

---

# V. Feature Engineering

## 9. Feature Engineering Pipeline

### Data Sorting

Sorted dataset by:

```python
[store_nbr, item_nbr, date]
```

---

## Date Features

Generated:

* `year`
* `month`
* `day`
* `dayofweek`

---

## Lag Features

Created:

* `lag_7`
* `lag_14`

Using shifted historical sales values.

---

## Rolling Features

Created:

* `rolling_mean_7`

Using a 7-day rolling average of lagged sales.

---

## Hierarchical Missing Value Strategy

Missing values in lag and rolling features were filled using the following hierarchy:

1. Same store + same item median
2. Same item median
3. Same store median
4. Global median

---

## Encode Categorical Variables

Applied one-hot encoding to:

* `family`
* `city`
* `state`
* `type`

Using:

```python
drop_first=True
```

---

## Split Train and Test Datasets

Separated datasets based on availability of `unit_sales`.

### Final Dataset Shapes

| Dataset | Shape           |
| ------- | --------------- |
| Train   | 37,454,835 × 89 |
| Test    | 3,370,464 × 89  |

### Final Validation

* No missing feature values
* Missing `unit_sales` in test dataset remained as expected

---

# VI. Model Training

## 10. Train XGBoost Models

### Training Strategy

* Trained a separate XGBoost model for each store
* Total models trained:

  * **54 store-specific models**

---

## Hyperparameters

```python
n_estimators = 100
max_depth = 5
learning_rate = 0.1
subsample = 0.8
colsample_bytree = 0.7
n_jobs = 2
```

---

## Features Used

Excluded:

```python
['unit_sales', 'date', 'id', 'store_nbr']
```

### Target Variable

```python
unit_sales
```

### Model Storage

Models were stored in a dictionary using:

```python
store_nbr
```

as the key.

---

# VII. Export Results for Power BI

## 11. Export Forecast Results

### Sales by Store

Steps:

* Generated predictions using corresponding store models
* Aggregated:

  * Actual sales
  * Predicted sales
* Grouped by:

  * `date`
  * `store_nbr`

### Output File

```python
powerbi_data.csv
```

---

## Sales by Product Family

Steps:

* Generated predictions
* Reconstructed product family from one-hot encoded columns
* Aggregated by:

  * `date`
  * `store_nbr`
  * `family`

### Output File

```python
powerbi_data1.csv
```

---

## Export Packaging

* Created ZIP archive containing Power BI CSV outputs

---

# VIII. Power BI Visualization

## 12. Dashboard Visualization

Power BI dashboards were prepared for:

* Predicted sales visualization
* Actual sales comparison
* Store-level sales analysis
* Product family sales analysis

---

# Technologies Used

* Python
* Pandas
* NumPy
* XGBoost
* Scikit-learn
* Py7zr
* Power BI

---

# Machine Learning Approach

The project uses:

* Time-series feature engineering
* Lag-based forecasting
* Rolling statistical features
* Store-specific gradient boosting models

The use of separate models per store allows the system to better capture:

* Local sales behavior
* Regional demand differences
* Store-specific purchasing patterns

---

# Key Outcomes

* Successfully processed over **125 million rows**
* Engineered time-series forecasting features
* Trained scalable XGBoost forecasting models
* Generated prediction datasets for business intelligence reporting
* Integrated outputs into Power BI dashboards for visualization and analysis

---

# Future Improvements

Possible future enhancements include:

* Hyperparameter tuning using GridSearch or Optuna
* Cross-validation for time-series forecasting
* Incorporating oil prices and transaction datasets
* Using advanced models such as:

  * LightGBM
  * CatBoost
  * LSTM Neural Networks
* Deploying the forecasting pipeline as a web application or API
