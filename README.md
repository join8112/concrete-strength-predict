# Concrete Compressive Strength Prediction

Predicting the compressive strength of concrete (MPa) from its mix proportions and curing age, and comparing eight regression models. I built this while following an online course.

## Data

`compresive_strength_concrete+2.csv`: 1,030 concrete samples from the UCI Concrete Compressive Strength dataset.

- **Features:** cement, blast furnace slag, fly ash, water, superplasticizer, coarse aggregate, fine aggregate (kg per m³ of mixture), and age (days)
- **Target:** compressive strength (MPa)

## Workflow

1. Exploratory analysis: distributions, pair plots, correlation heatmap
2. Outlier checks with IQR and z-scores
3. 70/30 train/test split with z-score scaling
4. Eight models with default settings: Random Forest, Gradient Boosting, AdaBoost, KNN, Bagging, linear SVR, XGBoost, Decision Tree
5. 20-fold cross-validation for each model, and feature importance from the decision tree

## Results

| Model | Test R² |
| --- | --- |
| XGBoost | 0.882 |
| Gradient Boosting | 0.881 |
| Random Forest | 0.879 |
| Bagging | 0.866 |
| KNN (k = 3) | 0.754 |
| AdaBoost | 0.753 |
| Decision Tree | 0.743 |
| SVR (linear kernel) | 0.655 |

The notebook labels these scores "accuracy"; they are R² on the test set.

The most important features in the decision tree are age (0.37), cement (0.31) and water (0.12), which matches how concrete behaves: strength grows with curing time and depends on the cement and water content.

## What I would change now

- **Metrics:** report R² under its own name, together with RMSE or MAE in MPa.
- **Cross-validation folds:** `KFold` is used without shuffling, and the rows are grouped by mix, so the cross-validation scores come out lower than the test scores. In a quick re-run on the raw data, shuffling the folds raised Random Forest's mean R² from about 0.79 to 0.92.
- **Decision Tree cross-validation:** the two Decision Tree cross-validation cells reuse the XGBoost scores (`results_9`), so those numbers are not valid.
- **Outliers and scaling:** outliers were replaced by the median on the full dataset before the split. This included curing ages of 270 to 365 days, which are real test ages, not errors. Scaling was also fitted on the full data. Both steps belong inside the training folds, for example in a scikit-learn `Pipeline`.
- **Choosing k for KNN:** k was chosen by the share of predictions that differ from the true value. That measure is not meaningful for a continuous target; RMSE should be used instead.
- **Hyperparameters:** all models use default settings, so tuning is a natural next step.

## Files

- `concrete_strength_predict.ipynb`: the full analysis, written in Google Colab. Update the CSV path in the first cells to run it elsewhere.
- `compresive_strength_concrete+2.csv`: the dataset.
