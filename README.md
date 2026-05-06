# Fencing Hot Hand Analysis

## Overview
End-to-end data pipeline and statistical analysis testing the “hot hand” hypothesis in competitive fencing pool bouts using data from the February 2026 NAC tournament.

This project transforms raw fencing pool-grid results into a clean fencer-bout-level dataset, validates raw pool inputs, engineers sequential performance features, joins pre-tournament rating data as a skill proxy, and uses logistic regression as a downstream analysis.

## Pipeline
1. **Ingestion** — Raw pool-grid results are parsed from compact fencing notation such as `V5` and `D3` into structured bout records.
2. **Validation** — Pool inputs are checked for missing matchups, malformed result cells, self-matches, and inconsistent win/loss pairs across opposite cells.
3. **Transformation** — One row is created per fencer per bout, with win/loss outcome, bout order, score margin, and opponent information.
4. **Feature Engineering** — Sequential and skill-based features are created, including `prev_win`, `recent_form_3`, and `strength_diff`.
5. **Modeling** — Logistic regression is used to test whether previous-bout outcome predicts the next result after controlling for skill.
6. **Output** — Cleaned datasets are exported for both full fencer-bout-level analysis and model-ready regression analysis.

## Key Finding
A raw hot-hand effect appears descriptively: fencers won 61.2% of bouts after a win compared to 39.9% after a loss, a +21.3 percentage point gap.

After controlling for skill differential with logistic regression, the model-predicted gap shrinks to about +3.4 percentage points. Previous-bout outcome is not statistically significant in the full logistic regression model (`p = 0.381`), while strength difference is the dominant predictor, with an in-sample AUC of 0.875.

## Data
- Source: February 2026 NAC tournament pool sheets
- 13 pools
- 546 raw fencer-bout observations
- 331 model-ready observations after feature engineering and rating joins
- Pre-tournament USA Fencing ratings used as a skill proxy

## Data Engineering Highlights
- Parsed compact fencing pool-grid notation into structured fencer-bout-level records
- Added validation checks for missing matchups, malformed result cells, self-matches, and inconsistent win/loss pairs
- Joined external rating data to create skill-adjusted features
- Engineered sequential features for downstream modeling
- Exported both full cleaned and model-ready datasets

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
- `data/processed/fencer_bout_level_data.csv` — full cleaned fencer-bout-level dataset after parsing and feature engineering
- `data/processed/model_ready_dataset.csv` — filtered dataset with complete features used for logistic regression
- `hot_hand_win_rate.png` — visualization of the raw hot-hand effect
- `skill_controlled_predicted_win_rate.png` — model-based visualization of the skill-controlled hot-hand effect

### Note
Each bout appears twice in the fencer-bout-level dataset, once from each fencer’s perspective. This is useful for creating per-fencer sequential features, but it means observations are not fully independent. The statistical results should therefore be interpreted as exploratory rather than causal.

This project is intended as a first end-to-end pipeline prototype. The current analysis uses 13 pools from one tournament, so the model results should be interpreted as exploratory. The pipeline structure can be extended by adding more tournaments, additional rating sources, and larger samples of pool bouts.