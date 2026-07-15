# Time-Series-Modelling-Case-Study---German-Electricity-Load

This project forecasts German electricity demand using historical load data, comparing benchmark, SARIMAX, feature-based, Bayesian, and neural forecasting models.

## Project aim / research questions

The aim is to forecast weekly German electricity demand and compare how well different forecasting approaches perform.

1. How well do simple benchmark methods forecast weekly electricity demand?
2. Does a SARIMAX model improve on seasonal benchmarks?
3. Do temperature and holiday covariates improve forecast accuracy?
4. Do feature-based or neural models justify their additional complexity?
5. Which model would be most appropriate for an operational forecasting setting?

## Data description

The data comes from Open Power System Data (electricity load) and Open-Meteo (Berlin temperature). The original data is hourly electricity load, cleaned and aggregated to weekly average load, converted from MW to GW.

Target variable: `load_gw`

Covariates: `temp_mean`, `heating_degree_days`, `cooling_degree_days`, `holiday_days`, `has_holiday`

Temperature is not known in advance in a real setting — it needs a weather forecast, so results using it are treated as conditional forecasts. Holidays are known in advance, so they're safe to use as future covariates.

## Repository structure

```text
electricity-demand-forecasting/
│
├── README.md
├── requirements.txt
├── environment.yml
│
├── data/
├── src/electricity_demand/
│   ├── config.py
│   ├── pipeline.py
│   ├── data.py
│   ├── features.py
│   ├── evaluation.py
│   ├── plotting.py
│   └── models/
│       ├── benchmarks.py
│       ├── sarimax.py
│       ├── feature_models.py
│       ├── bayesian.py
│       └── neural.py
│
├── scripts/
│   ├── download_data.py
│   ├── make_features.py
│   ├── run_pipeline.py
│   └── evaluate_models.py
│
├── outputs/
├── reports/
└── tests/
```

## How to install it

```bash
python -m venv .venv
source .venv/bin/activate      # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

## How to run it

```bash
python scripts/run_pipeline.py
```

This produces:

```text
outputs/forecasts/all_forecasts.csv
outputs/metrics/model_comparison.csv
outputs/figures/forecast_comparison.png
outputs/figures/error_diagnostics.png
```

## Models used

- **Benchmarks:** Mean, Naive, Seasonal Naive, Drift.
- **SARIMAX:** seasonal ARIMA model with weekly seasonality, using temperature as an exogenous input.
- **Feature-based:** Random Forest using lag, rolling, calendar, holiday, and temperature features.
- **Bayesian:** Bayesian regression with posterior predictive intervals.
- **Neural:** LSTM trained on hourly data.

## Evaluation

All models are evaluated on the same held-out test period (final 104 weeks) using MAE, RMSE, MASE, and Bias, and compared against the Seasonal Naive benchmark.

## Avoiding data leakage

- Lag/rolling features only use past information.
- Test-period forecasts are generated recursively, not from true future values.
- Feature scaling is fit on training data only.
- Observed future temperature is labelled a conditional forecast, not a true one.

## Outputs and report

Results are saved in `outputs/forecasts/`, `outputs/metrics/`, and `outputs/figures/`. The full written report is in `reports/`.

## Running tests

```bash
pytest
```
