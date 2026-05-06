# Fencing Hot Hand Analysis

## Overview
End-to-end data pipeline and statistical analysis testing the “hot hand” hypothesis in competitive fencing pool bouts using data from the February 2026 NAC tournament.

This project transforms raw fencing pool-grid results into a clean fencer-bout-level dataset, engineers sequential performance features, joins pre-tournament rating data as a skill proxy, and uses logistic regression as a downstream analysis.

## Pipeline
1. **Ingestion** — Raw pool-grid results are parsed from compact fencing notation such as `V5` and `D3` into structured bout records.
2. **Transformation** — One row is created per fencer per bout, with win/loss outcome, bout order, score margin, and opponent information.
3. **Feature Engineering** — Sequential and skill-based features are created, including `prev_win`, `recent_form_3`, and `strength_diff`.
4. **Modeling** — Logistic regression is used to test whether previous-bout outcome predicts the next result after controlling for skill.
5. **Output** — The cleaned model-ready dataset is exported to `data/processed/final_dataset.csv`.

## Key Finding
A raw hot-hand effect appears descriptively: fencers won 61.2% of bouts after a win compared to 39.9% after a loss, a +21.3 percentage point gap.

After controlling for skill differential in the full logistic regression model, previous-bout outcome is not statistically significant (`p = 0.381`). Strength difference is the dominant predictor, and the model has an in-sample AUC of 0.875.

## Data
- Source: February 2026 NAC tournament pool sheets
- 13 pools
- 546 raw fencer-bout observations
- 331 model-ready observations after feature engineering and rating joins
- Pre-tournament USA Fencing ratings used as a skill proxy

## Stack
- `pandas` — data wrangling and feature engineering
- `scikit-learn` — logistic regression and AUC scoring
- `statsmodels` — coefficient-level inference and p-values
- `matplotlib` — visualization

## How to Run
1. Install dependencies:

```bash
pip install pandas numpy matplotlib scikit-learn statsmodels
```

2. Open the notebook in JupyterLab:

```bash
jupyter lab fencing_hot_hand_analysis.ipynb
```

3. Run all cells:

```text
Kernel → Restart & Run All
```

## Output
- `data/processed/final_dataset.csv` — cleaned model-ready dataset with engineered features
- `hot_hand_win_rate.png` — visualization of the raw hot-hand effect

## Note
Each bout appears twice in the fencer-bout-level dataset, once from each fencer’s perspective. This is useful for creating per-fencer sequential features, but it means observations are not fully independent. The statistical results should therefore be interpreted as exploratory rather than causal.