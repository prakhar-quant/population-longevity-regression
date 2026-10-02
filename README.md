
# Population Longevity Determinant Modeling Using Regularized Linear Regression

A statistically rigorous regression pipeline that predicts **country-level life expectancy** from health, demographic and economic indicators, and identifies the factors that drive longevity across **183 countries**.

![Python](https://img.shields.io/badge/Python-3.9%2B-blue)
![scikit-learn](https://img.shields.io/badge/scikit--learn-ML-orange)
![statsmodels](https://img.shields.io/badge/statsmodels-OLS-green)
![Status](https://img.shields.io/badge/status-complete-brightgreen)

---

## Overview

Life expectancy is a core indicator of national health. This project builds and compares **OLS, Ridge, Lasso and Elastic Net** models to explain and predict it, with an emphasis on **assumption checking** rather than only chasing accuracy.

The raw data contain repeated yearly observations per country (2000 to 2015). Treating these as independent would violate OLS assumptions, so the final analysis is deliberately **cross-sectional (year 2010)**.

## Key Results

| Metric | Value |
|---|---|
| Countries modeled | 183 (146 train / 37 test) |
| Final OLS adjusted R² (train) | 0.853 |
| Maximum VIF | 3.95 |
| Breusch-Pagan p-value | 0.459 (no heteroscedasticity) |
| Jarque-Bera p-value | < 0.001 before, 0.739 after Yeo-Johnson |
| Durbin-Watson | 1.84 |

**Out-of-sample performance (2010 test set)**

| Model | R² | MAE (years) | RMSE (years) |
|---|---|---|---|
| Ridge | 0.723 | 3.70 | 4.86 |
| Elastic Net | 0.722 | 3.71 | 4.86 |
| Lasso | 0.716 | 3.77 | 4.92 |
| Refined OLS | 0.716 | 3.77 | 4.92 |

## Key Findings

- **HIV/AIDS prevalence** and **adult mortality** are the strongest negative drivers of life expectancy.
- **Income composition of resources** and **health expenditure** are significant positive drivers.
- Developed-status countries show a significant life-expectancy advantage after controlling for other indicators.
- Regularized models gave only a small improvement over refined OLS, so a well-diagnosed linear model is already competitive here.

## Methodology

1. **Data cleaning** : duplicate removal, numeric coercion, column standardization.
2. **Missingness audit by year** : used to select the most complete year (2010) and screen out features with more than 25% missing values.
3. **Leakage-free preprocessing** : median imputation fitted on the training set only, then applied to the test set.
4. **Multicollinearity control** : dropped near-duplicate predictors (infant deaths vs under-five deaths, r = 0.997; thinness 5-9 vs 1-19 years, r = 0.974).
5. **Feature transformation** : log1p applied to heavily right-skewed predictors (Measles, GDP, Population, HIV/AIDS and others).
6. **Response transformation** : original, log and Yeo-Johnson responses compared on residual diagnostics. Yeo-Johnson fixed residual non-normality.
7. **Assumption checks** : VIF, Breusch-Pagan, Jarque-Bera, Durbin-Watson, residual vs fitted and Q-Q plots.
8. **Influence analysis** : R-student, leverage and Cook's distance.
9. **Regularized models** : Ridge, Lasso and Elastic Net tuned with 5-fold `GridSearchCV` on RMSE.
10. **Evaluation** : R², MAE and RMSE on held-out countries, stratified by development status.

## Repository Structure

```
population-longevity-regression/
├── data/
│   └── Life_Expectancy_Data.csv
├── notebooks/
│   └── Life_Expectancy_Linear_Regression_Project.ipynb
├── images/                  # exported plots (optional)
├── requirements.txt
├── .gitignore
├── LICENSE
└── README.md
```

## How to Run

```bash
git clone https://github.com/prakhar-quant/population-longevity-regression.git
cd population-longevity-regression
pip install -r requirements.txt
jupyter notebook notebooks/Life_Expectancy_Linear_Regression_Project.ipynb
```

Make sure the notebook's `pd.read_csv(...)` path points to `data/Life_Expectancy_Data.csv`.

## Tech Stack

Python, Pandas, NumPy, SciPy, scikit-learn, statsmodels, Matplotlib, Seaborn, Jupyter

## Limitations and Future Work

- The test set is small (37 countries), so metrics carry sampling uncertainty. Repeated or nested cross-validation would give more stable estimates.
- A single-year cross-section ignores time trends. A panel model with country fixed effects could use all years.
- Try non-linear models such as XGBoost or Random Forest with SHAP for interpretability.
- Extend the Lasso alpha grid, since the best value sat at the edge of the search range.

## Author

**Prakhar Gupta**
M.Sc. Statistics and Computing, Banaras Hindu University
GitHub: [prakhar-quant](https://github.com/prakhar-quant)
