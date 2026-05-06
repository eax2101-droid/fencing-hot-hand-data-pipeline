# Fencing Hot Hand Analysis

## Overview
End-to-end data pipeline and statistical analysis testing the “hot hand” hypothesis in competitive fencing pool bouts using data from the February 2026 NAC tournament.

The project transforms raw fencing pool-grid results into a clean fencer-bout-level dataset, engineers sequential performance features, joins pre-tournament rating data as a skill proxy, and uses logistic regression as a downstream analysis.

## Pipeline
1. **Ingestion** — Raw pool-grid results are parsed from compact fencing notation such as `V5` and `D3` into structured bout records.
2. **Validation** — Pool results are checked for missing matchups, malformed result cells, and inconsistent win/loss pairs.
3. **Transformation** — One row is created per fencer per bout, with win/loss outcome, bout order, score margin, and opponent information.
4. **Feature Engineering** — Sequential and skill-based features are created, including `prev_win`, rolling recent win rate, and `strength_diff`.
5. **Modeling** — Logistic regression is used to test whether previous-bout outcome predicts the next result after controlling for skill.
6. **Output** — Cleaned dataset exported to `data/processed/final_dataset.csv`.

## Key Finding
A raw hot-hand effect appears descriptively: fencers won more often after a win than after a loss, with a +21.3 percentage point gap. After controlling for skill differential, the estimated gap shrinks to about +4.0 percentage points and is not statistically significant in the close-match robustness check. Strength difference is the dominant predictor, with an in-sample AUC of 0.875.

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