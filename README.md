
# Spotter Freight Rate ML Assessment

## Overview
This project predicts freight posted rates using historical load data.

The development dataset contains 48,000 labeled loads. Because the
evaluation period occurs after the development period, model validation
was performed using time-based splits rather than a random split.

## Data Quality
Key issues identified during EDA:
- Missing values in weight and market_index.
- Negative weight values, corrected using absolute magnitude.
- A right-skewed posted_rate target distribution.
- Rare high-rate loads were retained because there was insufficient
  evidence that they were invalid observations.

## Feature Engineering
Features included:
- Geographic coordinates
- Distance and weight
- Market and quote signals
- Calendar features derived from date
- Equipment type
- Pickup and delivery locations
- Route = pickup + delivery

## Validation Strategy
Primary holdout:
- Train: January–September 2025
- Validation: October 2025

Additional rolling backtesting:
- Train through July → Validate August
- Train through August → Validate September
- Train through September → Validate October

## Model Results

| Model | MAE | RMSE | R² |
|---|---:|---:|---:|
| Mean Baseline | 1179.80 | 1528.57 | ~0 |
| Median Baseline | 1146.79 | 1567.97 | -0.052 |
| Random Forest | 219.78 | 712.29 | 0.7829 |
| Linear Regression | 141.58 | 651.31 | 0.8184 |
| CatBoost | 121.08 | 647.96 | 0.8203 |
| CatBoost + Route | 118.72 | 647.10 | 0.8208 |
| Tuned CatBoost + Route | 116.17 | 646.75 | 0.8210 |

## Rolling Validation

| Validation Month | MAE | RMSE | R² |
|---|---:|---:|---:|
| August 2025 | 135.51 | 618.63 | 0.8241 |
| September 2025 | 105.75 | 614.98 | 0.8370 |
| October 2025 | 118.72 | 647.10 | 0.8208 |

The rolling validation above was performed on the CatBoost + Route
configuration before the final small hyperparameter tuning step.

## Final Model
The final model is a CatBoostRegressor using native categorical
features and the engineered route feature.

Final selected configuration:
- depth: 9
- learning_rate: 0.05
- iterations: 164
- GPU training

## Error Analysis
Most predictions had relatively small errors. The largest errors were
concentrated in a very small number of unusually high-rate loads,
especially loads above $7,000. These rows were retained rather than
removed as outliers.

## December Forecast
The supplied December scenario did not include all features used by the
final model. City coordinates were obtained from the development data.
market_index and quote_signal were filled using development-data
medians. These assumptions are documented because they may limit the
December scenario accuracy.

## Repository Structure
- `notebooks/`: full analysis and modeling workflow
- `outputs/`: required prediction CSV files
- `figures/`: December forecast chart

## Running
Place the assessment data files in the expected data directory and run
the notebook from top to bottom.

## Loom Walkthrough
[Add Loom link here]
