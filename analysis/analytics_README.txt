# 📊 Module 2 — Analytics Pipeline (`/analytics`)

Welcome to the **Zepto Analytics & Modeling Pipeline**.

This module implements a complete end-to-end Data Science workflow, including:

- 📥 Data Profiling
- 🧹 Data Cleaning
- 📊 Exploratory Data Analysis (EDA)
- 🤖 Machine Learning Pipeline
- 📈 Model Evaluation
- ⚖️ Class Imbalance Handling
- 🔍 Hyperparameter Tuning
- 📉 Regression Analysis

---

# 🚀 Executive Summary

## ✅ Recommended Production Model

**Model:** Tuned Random Forest Classifier

### Why this model?

Although **Logistic Regression** achieved a strong baseline ROC-AUC of **0.8601**, the **Tuned Random Forest** produced the best overall performance.

### Performance

| Metric | Value |
|---------|-------|
| ROC-AUC | **0.8655** |
| OOB Score | **81.23%** |

### Why Random Forest?

- Captures complex non-linear relationships.
- Learns feature interactions automatically.
- Performs well without manual feature engineering.
- Hyperparameter tuning reduces overfitting.

Best Parameters:

```python
max_depth = 6
n_estimators = 100
max_features = "sqrt"
```

---

# 📊 Model Performance Comparison

| Model | Accuracy | Precision | Recall | F1 Score | ROC-AUC |
|--------|---------:|----------:|--------:|---------:|---------:|
| Logistic Regression | 0.7933 | 0.7538 | 0.7101 | 0.7313 | 0.8601 |
| Decision Tree | 0.8101 | 0.8200 | 0.5942 | 0.6891 | 0.8354 |
| Random Forest | 0.7821 | 0.7246 | 0.7246 | 0.7246 | 0.8542 |
| ⭐ Tuned Random Forest | **0.8156** | **0.8235** | **0.6087** | **0.7000** | **0.8655** |

---

# 📈 Regression Performance

| Metric | Value |
|---------|------:|
| MAE | $19.16 |
| RMSE | $34.82 |
| R² Score | 0.3802 |
| Adjusted R² | 0.3392 |

---

# 🔍 Part A — Data Profiling, Cleaning & EDA

## 1. Dataset Overview

| Item | Value |
|------|------|
| Rows | 891 |
| Columns | 15 |

### Missing Values

| Column | Missing | Percentage |
|---------|---------|-----------:|
| deck | 688 | 77.10% |
| age | 177 | 19.87% |
| embarked | 2 | 0.22% |
| embark_town | 2 | 0.22% |

---

## 2. Data Cleaning Strategy

### Rule 1 (>30% Missing)

Dropped:

- deck

Reason:

77.10% of values were missing, making imputation unreliable.

---

### Rule 2 (5%–30% Missing)

Imputed:

- age

Method:

Grouped Median Imputation using

- Passenger Class
- Gender

---

### Rule 3 (<5% Missing)

Dropped rows containing

- embarked
- embark_town

Reason:

Only 0.22% of rows were affected.

---

# 📊 Outlier Analysis

## Age

| Statistic | Value |
|-----------|------:|
| Outliers | 7 |

IQR Bounds

```
Lower = -6.69
Upper = 64.81
```

---

## Fare

| Statistic | Value |
|-----------|------:|
| Outliers | 116 |

IQR Bounds

```
Lower = -26.76
Upper = 65.63
```

---

# 📉 Fare Distribution

| Statistic | Value |
|-----------|------:|
| Mean | $32.20 |
| Median | $14.45 |
| Mode | $8.05 |

### Observation

```
Mean > Median > Mode
```

This indicates that Fare is **highly right-skewed**, with a long tail caused by expensive first-class tickets.

---

# 👥 Survival Analysis

## Survival by Gender

| Gender | Survival Rate |
|---------|--------------:|
| Female | 74.20% |
| Male | 18.89% |

---

## Survival by Passenger Class

| Class | Survival Rate |
|-------|--------------:|
| First | 62.96% |
| Second | 47.28% |
| Third | 24.24% |

---

## Survival by Gender & Class

| Group | Survival |
|--------|----------:|
| First Class Female | 96.81% |
| Third Class Male | 13.54% |

---

# 🔥 Correlation Analysis

Top correlations:

| Features | Correlation |
|-----------|------------:|
| Pclass vs Fare | -0.549 |
| Pclass vs Age | -0.417 |

### Interpretation

- Higher-class passengers paid higher fares.
- Older passengers were more likely to travel in higher classes.

---

# 📊 Visual Insights

Generated visualizations include:

### 1. Survival by Gender and Passenger Class

Shows:

- Women had much higher survival rates.
- First-class passengers had the highest survival.

---

### 2. Log Fare Distribution

Shows:

Higher fare-paying passengers survived more frequently.

---

### 3. Age vs Fare Scatter Plot

Shows:

- Most third-class passengers paid low fares.
- High-fare passengers formed separate clusters.

---

### 4. Family Size vs Survival

Shows:

Families of size **2–4** had the highest survival probability.

---

# ⚙️ Feature Standardization

Standardized Features:

- Age
- Fare

Results:

| Metric | Value |
|--------|------:|
| Mean | ≈ 0 |
| Standard Deviation | ≈ 1 |

---

# 🤖 Part B — Machine Learning

## Train-Test Split

Split Ratio

```
80% Training
20% Testing
```

Method:

**Stratified Split**

Reason:

Preserves class distribution.

---

# 🔒 Leakage-Free Pipeline

Pipeline Components

- Median Imputation
- Standard Scaling
- One-Hot Encoding
- ColumnTransformer
- Scikit-Learn Pipeline

All preprocessing steps were fitted only on the training data to prevent data leakage.

---

# ⚖️ Class Imbalance Comparison

| Method | Precision | Recall | F1 Score |
|---------|----------:|--------:|---------:|
| Baseline | 0.7538 | 0.7101 | 0.7313 |
| Class Weight | 0.7067 | **0.7681** | **0.7361** |
| SMOTE | 0.7123 | 0.7536 | 0.7324 |

### Best Choice

✅ `class_weight='balanced'`

Reason:

Improved Recall and F1 without introducing synthetic samples.

---

# 🔍 Hyperparameter Tuning

Best Parameters

```python
{
    "classifier__max_depth": 6,
    "classifier__max_features": "sqrt",
    "classifier__n_estimators": 100
}
```

Out-of-Bag Score

```
81.23%
```

OOB provides an unbiased estimate of model performance using bootstrap samples.

---

# 📉 Regression Analysis

Target Variable

```
Fare
```







































Performance

| Metric | Value |
|---------|------:|
| MAE | $19.16 |
| RMSE | $34.82 |
| R² | 0.3802 |
| Adjusted R² | 0.3392 |

### Residual Analysis

The residual plot shows **heteroscedasticity**, meaning prediction errors increase as fare values become larger.

Lower fares are predicted accurately, while luxury fares exhibit greater variance.

---

# ▶️ How to Run

## Option 1 — Jupyter Notebooks

Run in the following order:

```
01_eda.ipynb
```

Then

```
02_modeling.ipynb
```

---

## Option 2 — Python Script

From the repository root, execute:

```bash
python analytics/analytics.py
```

---

# 📂 Output Files

The pipeline generates:

- `titanic.csv`
- `fitted_pipeline.joblib`
- EDA Charts
- Correlation Heatmap
- ROC Curve
- Confusion Matrix
- Residual Plot
- Feature Importance Plot

---

# ✅ Final Recommendation

**Recommended Production Model**

🏆 **Tuned Random Forest Classifier**

### Final Performance

| Metric | Value |
|---------|------:|
| Accuracy | **81.56%** |
| Precision | **82.35%** |
| Recall | **60.87%** |
| F1 Score | **70.00%** |
| ROC-AUC | **86.55%** |
| OOB Score | **81.23%** |

The tuned Random Forest demonstrated the best balance between predictive performance, robustness, and generalization, making it the preferred model for deployment.