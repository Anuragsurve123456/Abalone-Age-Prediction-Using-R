# 🐚 Abalone Age Prediction (R)

![R](https://img.shields.io/badge/R-4.x-blue)
![RMarkdown](https://img.shields.io/badge/RMarkdown-Report-success)
![ML](https://img.shields.io/badge/Machine%20Learning-Regression%20%7C%20Tree-orange)
![Status](https://img.shields.io/badge/Project-Completed-brightgreen)

**Author:** Anurag Surve  
**Goal:** Predict abalone age (via **Rings**) using physical/biological measurements using interpretable models.

---

## Project Snapshot

This project predicts **Abalone age** using the UCI Abalone dataset. Since direct age measurement is expensive and requires cutting the shell, the dataset uses **Rings** as the target (age ≈ Rings + 1.5).  
The workflow focuses on building reliable, interpretable models while addressing common regression issues like:

- Multicollinearity (highly correlated predictors)
- Heteroscedasticity (non-constant residual variance)
- Influential / high-leverage observations
- Non-normal residual behavior

Models explored:
- Multiple Linear Regression (baseline + improved)
- Log-transformed regression
- Decision Tree Regression + pruning

---

## Dataset

- Source: UCI Machine Learning Repository — Abalone dataset  
- Rows: 4177  
- Features: 9  
- Response: `Rings` (proxy for age)

### Features
**Categorical**
- `Sex` (M, F, I)

**Numeric**
- `Length`, `Diameter`, `Height`
- `Whole_weight`, `Shucked_weight`, `Viscera_weight`, `Shell_weight`
- `Rings` (target)

---

## Preprocessing & Feature Engineering

Key steps:
- Converted `Sex` to factor
- Created a simplified categorical variable:
  - `Infant = I` else `NI` (Non-Infant)
- Investigated **Height = 0** rows and retained them after inspection
- Created:
  - `weight.diff = Whole_weight - (Shucked + Viscera + Shell)`
- Removed observations where `weight.diff < 0` (likely data entry errors)

---

## Exploratory Data Analysis (EDA)

Included:
- Histograms + density overlays for all numeric variables
- Boxplots of numeric variables by `Sex`
- Q-Q plots for normality checks
- Correlation heatmap showing strong multicollinearity:
  - Example: `Length` and `Diameter` extremely correlated
  - `Whole_weight` highly correlated with component weights

Also performed:
- ANOVA tests showing significant differences across `Sex` for:
  - `Diameter`
  - `Rings`

---

## Modeling Approach

### 1) Linear Regression (Full Model)
- Started with all predictors:
  `Rings ~ Sex + Length + Diameter + Height + Whole_weight + Shucked + Viscera + Shell`

Diagnostics:
- Residuals vs fitted suggested heteroscedasticity
- VIF indicated strong multicollinearity

### 2) Tackling Multicollinearity
- Removed high-collinearity predictors (`Whole_weight`, then `Length`)
- Final reduced model:
  `Rings ~ Sex + Diameter + Height + Shucked_weight + Viscera_weight + Shell_weight`

Improved:
- Lower VIF
- More stable residual patterns

### 3) Log-Transformed Regression (Best Model)
- Applied transformation:
  `log(Rings) ~ predictors`
- Improved:
  - Normality of residuals
  - Reduced heteroscedasticity
  - Better generalization performance

### 4) Influential Point Handling (Refit Model)
- Used Cook’s distance, leverage, studentized residuals
- Refit after removing influential points
- Result: Better training fit but signs of overfitting (train vs test gap)

### 5) Decision Tree Regression
- Built regression tree using:
  `rpart(Rings ~ Sex + Diameter + Height + Shucked + Viscera + Shell)`
- Key insight:
  - `Shell_weight` was the primary splitter
- Pruning:
  - Pruned tree ended up identical to unpruned tree

---

## Model Comparison Summary

The **Log-Transformed Regression Model** provided the best balance of:
- Low and consistent Train/Test RMSE
- Strong explanatory power (R²)
- Better residual behavior (normality + variance stability)

The refit model improved training performance but showed signs of overfitting.

---

## Key Takeaways

- **Shell_weight** and **Diameter** are strong predictors of age
- Log-transforming the response improved model assumptions and generalization
- Cleaning invalid weight relationships improved dataset reliability
- Tree models were interpretable but underperformed compared to the best regression model

---

## Tech Stack

- R
- RMarkdown
- Key libraries:
  - `tidyverse`, `ggplot2`, `GGally`
  - `car`, `lmtest`, `MASS`
  - `caret`, `glmnet`
  - `rpart`, `rpart.plot`

---

## Repository Structure (Recommended)

