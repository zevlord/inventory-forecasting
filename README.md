# Alcohol Sales Analysis & Demand Forecasting for Inventory Planning

A Python-based data analytics project that explores 2016 retail alcohol sales transactions and applies time-series forecasting techniques to identify sales patterns and support demand and inventory planning.

This project was developed as part of the **IS388 Data Analysis Final Project (Semester Ganjil 2025/2026)**.

## Project Overview

The project analyzes historical retail sales data to understand product performance, sales trends, transaction patterns, and daily demand fluctuations. It combines data preprocessing, exploratory data analysis (EDA), statistical testing, and time-series forecasting.

Two forecasting approaches are implemented:

- **Facebook Prophet** to model trend and seasonal patterns.
- **SARIMA** to model daily sales as a seasonal time series.

The resulting analysis is intended to provide insights that can inform demand forecasting, restocking considerations, and operational planning. It is a sales-based planning analysis, not a complete inventory optimization system, because actual stock levels and supplier lead-time data are not included.

## Objectives

- Clean and standardize transaction and product-size data.
- Explore daily and monthly sales patterns.
- Identify high-performing products by sales value and quantity.
- Examine relationships among selected transaction variables.
- Test time-series stationarity using the Augmented Dickey-Fuller (ADF) test.
- Build and compare Prophet and SARIMA forecasting models.
- Translate analytical findings into practical business considerations.

## Project Workflow

### 1. Data Loading

The notebook reads `salesFinal2016.csv` in chunks of 100,000 rows and combines the chunks into a Pandas DataFrame.

### 2. Data Preprocessing

The preprocessing steps in the notebook include:

- Removing the `Classification` column.
- Cleaning product-size text by standardizing case and removing extra spaces and quotation marks.
- Extracting numeric size and pack-count information.
- Converting product sizes into milliliters (mL), using unit multipliers for mL, liters, and ounces.
- Creating `True Size/ml` and `TotalSize` fields.
- Removing records with non-positive standardized product sizes.
- Renaming `SalesDollars` to `TotalSales`.
- Creating `CleanProfit` as `TotalSales - ExciseTax`.

> **Metric clarification:** `CleanProfit` is the variable name used in the original notebook. Since the calculation subtracts only excise tax and does not account for cost of goods sold or operating expenses, it should not be interpreted as actual profit. A more accurate business label is **sales after excise tax**.

### 3. Exploratory Data Analysis (EDA)

The notebook explores the data through:

- Daily sales trends.
- Monthly sales trends.
- Top 10 product descriptions by sales value.
- Top 10 product descriptions by sales quantity.
- Sales distribution by product size.
- Correlation heatmap for selected numerical variables.
- Scatter plot examining unit sales price and sales value.

### 4. Time-Series Preparation and Stationarity

Daily sales are aggregated by `SalesDate`. The notebook then:

- Applies the Augmented Dickey-Fuller (ADF) test.
- Uses first-order differencing to assess whether the series becomes stationary.
- Prepares daily observations for time-series modeling.

### 5. Forecasting Models

#### Facebook Prophet

The notebook configures Prophet with:

- Daily seasonality: disabled.
- Weekly seasonality: enabled.
- Yearly seasonality: enabled.
- Country holidays: United Kingdom (`UK`), as specified in the original notebook.
- Forecast horizon: **720 days** in the current code.

The forecast horizon should be changed to `365` if the intended forecast period is one year. The holiday calendar should also be verified against the actual market represented by the dataset.

#### SARIMA

The notebook uses the following model configuration:

| Parameter | Value |
|---|---|
| Non-seasonal order `(p, d, q)` | `(1, 1, 1)` |
| Seasonal order `(P, D, Q, s)` | `(1, 1, 1, 7)` |
| Forecast horizon | 30 days |
| Data frequency | Daily |

The seasonal period of 7 represents a weekly pattern.

### 6. Model Evaluation

The notebook calculates the following metrics:

- **MAE** — Mean Absolute Error.
- **MSE** — Mean Squared Error.
- **RMSE** — Root Mean Squared Error.
- **R²** — Coefficient of Determination.

The current notebook evaluates fitted predictions against observations used during model fitting. These are **in-sample results** and should not be treated as a reliable estimate of future forecasting performance. A chronological train-test split or rolling time-series cross-validation is recommended before making claims about model accuracy.

## Technology Stack

- **Python**
- **Jupyter Notebook**
- **Pandas** — data manipulation and aggregation
- **NumPy** — numerical operations
- **Matplotlib** and **Seaborn** — data visualization
- **SciPy** — statistical processing, including Z-score filtering
- **Statsmodels** — ARIMA/SARIMA modeling and ADF stationarity testing
- **Prophet** — time-series forecasting
- **Scikit-learn** — model evaluation metrics

## Dataset

The dataset contains retail alcohol sales transaction fields, including:

`InventoryId`, `Store`, `Brand`, `Description`, `Size`, `SalesQuantity`, `SalesDollars`, `SalesPrice`, `SalesDate`, `Volume`, `Classification`, `ExciseTax`, `VendorNo`, and `VendorName`.

### Dataset availability

The ZIP attachment contains a reduced dataset due to file-size limitations:

- **Attached CSV:** 822,346 rows.
- **Date coverage of attached CSV:** January 1–31, 2016.
- **Full dataset:** The accompanying project note describes a Google Drive dataset of approximately 1.6 GB and more than 12 million rows.

[Access the full dataset on Google Drive](https://drive.google.com/file/d/1KgxLllN4kJt8fLFOXQBbsdi0vMYGAJUa/view?usp=sharing)

The notebook expects the CSV file to be named `salesFinal2016.csv` and located in the same directory as the notebook. Download the full dataset and place it there to reproduce the full-year analysis. The reduced attached CSV is suitable for checking the workflow, but its January-only date range will not reproduce full-year outputs.

**Note:** The full dataset is not included in this repository because of its large file size. Please check the dataset source's terms before redistributing it.

## Getting Started

### Prerequisites

- Python 3
- pip
- Jupyter Notebook
- Sufficient memory for the dataset being analyzed

The notebook concatenates all CSV chunks into memory. Although it reads the file in chunks, the full dataset is ultimately loaded into a single DataFrame, so a large amount of RAM may be required for the full dataset.

### 1. Clone the repository

```bash
git clone https://github.com/<your-username>/<your-repository>.git
cd <your-repository>
```

### 2. Create and activate a virtual environment (recommended)

**Windows**

```bash
python -m venv .venv
.venv\Scripts\activate
```

**macOS / Linux**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install dependencies

```bash
pip install pandas numpy matplotlib seaborn scipy scikit-learn statsmodels prophet jupyter
```

### 4. Add the dataset

Download the dataset and place `salesFinal2016.csv` in the same folder as the notebook.

### 5. Launch Jupyter Notebook

```bash
jupyter notebook
```

Open:

```text
EL_IS388_Lab_WilliamEkaSaputra_00000112825.ipynb
```

Run the notebook cells sequentially.

## Repository Structure

```text
.
├── EL_IS388_Lab_WilliamEkaSaputra_00000112825.ipynb
├── salesFinal2016.csv                 # Local dataset; not required to be committed
└── README.md
```

The actual repository may also include a `requirements.txt`, `.gitignore`, or supporting project documentation.

## Important Notes and Future Improvements

The notebook represents the original project implementation. The following improvements are recommended before using the results for operational decisions:

1. **Validate product-size parsing.** Review package formats such as `3/100mL` and size values written as `Liter` to ensure that all products are converted correctly.
2. **Use accurate metric names.** Rename `CleanProfit` to a label such as `SalesAfterExciseTax` unless cost and expense data are added to calculate actual profit.
3. **Correct the Prophet forecast horizon.** The current code forecasts 720 days, while the chart title refers to one year.
4. **Validate the holiday calendar.** Confirm that the UK holiday calendar is appropriate for the dataset's market.
5. **Improve model validation.** Use chronological holdout data or rolling time-series cross-validation, and tune model parameters where appropriate.
6. **Review EDA labels and categories.** For example, the top-products chart groups by `Description`, so it represents product descriptions rather than distinct brands.
7. **Add inventory data for direct optimization.** Stock-on-hand, purchase orders, supplier lead times, replenishment quantities, and stockout records are needed to develop a complete inventory optimization model.

## Author

**William Eka Saputra**  
IS388 Data Analysis — Final Project  
Semester Ganjil 2025/2026

---

*This repository documents an academic data analytics project. Forecasts and business recommendations should be validated with appropriate out-of-sample testing and operational inventory data before being used in real-world decisions.*
