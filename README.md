# PowerPulse: Household Energy Usage Forecast

Predict household energy consumption and surface actionable insights from the **Individual Household Electric Power Consumption** dataset (UCI).  
This repo/code prepares the raw text file, engineers features, and benchmarks several regression models.

> Source project brief and evaluation rubric: see the attached PDF “PowerPulse_ Household Energy Usage Forecast.pdf”. fileciteturn0file0

## 1) Dataset

- **UCI ML Repository**: Individual Household Electric Power Consumption  
  File typically named: `household_power_consumption.txt` (semicolon-separated).
- Place the raw file in the project root (same folder as your script).

## 2) Environment Setup

```bash
# (recommended) Python 3.10+
python -V

# Create & activate a virtual environment
python -m venv .venv
# Windows
.venv\Scripts\activate
# macOS/Linux
source .venv/bin/activate

# Install dependencies
pip install -r requirements.txt
```

## 3) How the provided script works

> The code you shared is a single end‑to‑end workflow. Save it as, e.g., `powerpulse.py` and run it with `python powerpulse.py` after placing the dataset in the folder.

### Steps performed

1. **Split huge CSV→Excel (multi‑sheet)**  
   - Reads `household_power_consumption.txt` with `sep=';'`  
   - Writes `household_power_consumption.xlsx` using multiple sheets so each stays under Excel’s ~1,048,576‑row limit.

2. **Load all Excel sheets & merge**  
   - Loads every sheet and concatenates into one DataFrame: `df_main`.

3. **EDA sanity checks**  
   - Prints `info()`, `describe()`, and `head()`.
   - Generates a correlation heatmap **only for numeric columns** to avoid dtype errors.

4. **Robust datetime parsing & feature engineering**  
   - Tolerant parsing of `Date` (`dayfirst=True`) and `Time` → builds `Datetime`.
   - Creates time features: `Hour`, `Day`, `Month`, `Weekday`.
   - Adds engineered features: `Is_peak_hour` (17–22), `Power_rolling_3h` (mean of last 3 readings).

5. **Numeric coercion & cleaning**  
   - Replaces `"?"` and comma‑decimals (`"4,52"→"4.52"`) and coerces to numeric.
   - Drops rows *only* when critically needed (e.g., missing target or required predictors).
   - Standardizes selected columns with `StandardScaler` (only after NA handling).

6. **Modeling**  
   - Target: `Global_active_power`  
   - Features: `Global_reactive_power`, `Voltage`, `Global_intensity`, `Hour`, `Is_peak_hour`, `Power_rolling_3h`  
   - Train/test split (80/20).  
   - Trains and evaluates:
     - Linear Regression
     - Random Forest Regressor
     - Gradient Boosting Regressor
     - HistGradientBoosting Regressor
   - Metrics reported: **RMSE**, **MAE**, **R²**

7. **Outputs**  
   - Console table with metrics sorted by RMSE (lower is better).  
   - A correlation heatmap window.

## 4) Running the script

```bash
python powerpulse.py
```

**Expected console output (example)**

```
Rows before NA filter: 2075259 | after: 204XXXX | dropped: 3XXXX
Valid Datetime rows: 20XXXXX/20XXXXX
Remaining NaNs in scale columns: {...}
Scaling complete. Shape: (N, M)

           Model  RMSE   MAE    R2
0  HistGradient...  ...   ...   ...
1  Gradient Boos... ...   ...   ...
2  Random Forest    ...   ...   ...
3  Linear Regression ...  ...   ...
```

A figure window will show the **correlation matrix** for numeric columns.

## 5) Troubleshooting

- **MemoryError / slow Excel I/O**  
  - Ensure you have disk space and RAM; consider running just the modeling section by reading the raw TXT directly (skip Excel step) if resources are limited.
- **`KeyError: 'Global_active_power'`**  
  - Headers must match; your code already uses a tolerant `find_col`, but verify the dataset hasn’t been altered.
- **Date parsing issues**  
  - This dataset is typically `DD/MM/YYYY`; `dayfirst=True` is already used.
- **Too many NaNs after cleaning**  
  - Check for `"?"` placeholders; the script converts them to `NaN` and then imputes/filters where necessary.
- **Plot window doesn’t appear**  
  - In headless environments, use `matplotlib` non‑interactive backend (e.g., `Agg`) and `plt.savefig('corr.png')` instead of `plt.show()`.
- **`openpyxl` engine errors**  
  - Make sure `openpyxl` is installed (it is in `requirements.txt`).

## 6) Extending the project (ideas)

- Cross‑validation and hyperparameter tuning (`GridSearchCV`/`RandomizedSearchCV`).  
- Add weather covariates (temperature, humidity) aligned by timestamp.  
- Save trained models with `joblib` and expose a simple CLI or Streamlit app.  
- Aggregate to hourly/daily means to reduce noise and speed up training.  
- Add feature importance plots for tree‑based models.

## 7) Evaluation criteria (from brief)

- **RMSE, MAE, R²**, clarity of insights, quality of visualizations, and explanation of influential factors.  
  See the attached brief for full rubric and deliverables. fileciteturn0file0

## 8) License & Acknowledgements

- Dataset © UCI ML Repository. Please review the dataset’s license/terms.  
