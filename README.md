# D2C Customer Churn Prediction & Model Card

## Executive Summary

**Final Model:** Logistic Regression

**Selected Threshold:** 0.35

### Final Test Performance

| Metric    | Value  |
| --------- | ------ |
| Accuracy  | 79.46% |
| Precision | 75.65% |
| Recall    | 86.90% |
| F1 Score  | 80.89% |
| ROC-AUC   | 87.11% |

The final model successfully identified **146 out of 168 future churners** in the unseen test dataset while maintaining strong overall predictive performance.

The solution is designed to support proactive retention initiatives by identifying customers most likely to churn within the next 60 days.

---

# Overview

This project develops a machine learning solution to predict whether a customer is likely to churn within the next 60 days.

The objective is to help customer retention teams proactively identify at-risk customers and prioritize retention interventions before churn occurs.

The solution includes:

* End-to-end churn modeling workflow
* Leakage prevention checks
* Baseline and advanced model comparison
* Threshold optimization
* Feature importance analysis
* Error analysis
* Model documentation through a structured model card

---

# Business Problem

Customer acquisition is significantly more expensive than customer retention.

The business requires a predictive model that can identify customers likely to churn within the next 60 days so that marketing and retention teams can intervene before customer loss occurs.

This project evaluates multiple machine learning approaches and selects the best-performing model based on business-oriented churn metrics.

---

# Dataset

The project uses the provided customer churn dataset consisting of:

* Customer profile information
* Transaction history
* RFM metrics
* Purchase behavior
* Support interactions
* Digital engagement activity
* Marketing engagement activity

### Target Variable

`churn_next_60d`

* 1 = Customer churned within the next 60 days
* 0 = Customer remained active

### Target Distribution

| Class        | Percentage |
| ------------ | ---------- |
| Retained (0) | 53.04%     |
| Churned (1)  | 46.96%     |

The target distribution is relatively balanced, allowing evaluation to focus on business-relevant metrics such as Precision, Recall, F1-Score, and ROC-AUC.

---

# Project Workflow

## 1. Data Audit

* Dataset inspection
* Missing value analysis
* Duplicate checks
* Target distribution analysis

## 2. Leakage Prevention

Features containing future information were excluded.

Removed fields:

* customer_id
* signup_date
* snapshot_date

The modeling process only uses information available at or before the customer snapshot date.

## 3. Feature Preparation

### Customer Profile

* city_tier
* age_group
* acquisition_channel
* loyalty_tier
* preferred_category
* skin_type
* marketing_consent

### Transactional Features

* recency
* frequency
* monetary

### Purchase Behavior

* return_rate
* avg_discount
* category_diversity

### Support Features

* ticket_count
* avg_sentiment
* avg_resolution_hours

### Engagement Features

* sessions_30d
* campaign_clicks_30d
* email_opens_30d
* last_visit_days_ago

---

# Models Evaluated

## Baseline Models

* Logistic Regression
* Decision Tree

## Advanced Models

* Random Forest
* XGBoost
* Gradient Boosting
* LightGBM

---

# Model Selection

The final model was selected using validation performance.

### Validation Results

| Model               | Accuracy | Precision | Recall | F1 Score | ROC-AUC |
| ------------------- | -------- | --------- | ------ | -------- | ------- |
| Logistic Regression | 0.8036   | 0.7914    | 0.7483 | 0.7692   | 0.8753  |
| Decision Tree       | 0.7619   | 0.7081    | 0.7755 | 0.7403   | 0.8372  |
| Random Forest       | 0.7857   | 0.7863    | 0.7007 | 0.7410   | 0.8677  |
| XGBoost             | 0.7649   | 0.7267    | 0.7415 | 0.7340   | 0.8496  |
| Gradient Boosting   | 0.7976   | 0.7842    | 0.7415 | 0.7622   | 0.8643  |
| LightGBM            | 0.7827   | 0.7434    | 0.7687 | 0.7559   | 0.8497  |

Although several ensemble-based algorithms were evaluated, Logistic Regression achieved the highest validation ROC-AUC while maintaining strong interpretability.

The model also demonstrated excellent generalization performance, with validation ROC-AUC (0.8753) and test ROC-AUC (0.8711) remaining highly consistent.

This suggests that churn behavior within the available feature space is largely explained by stable linear relationships rather than highly complex non-linear interactions.

---

# Threshold Optimization

Multiple probability thresholds were evaluated.

### Selected Threshold

```text
0.35
```

A threshold of 0.35 achieved the highest F1-Score while substantially improving Recall compared to the default threshold of 0.50.

The selected threshold intentionally prioritizes Recall because the cost of missing a future churner is typically greater than the cost of contacting a customer who ultimately remains active.

This threshold reduced false negatives and increased the number of at-risk customers identified for retention campaigns.

---

# Final Test Performance

| Metric    | Value  |
| --------- | ------ |
| Accuracy  | 79.46% |
| Precision | 75.65% |
| Recall    | 86.90% |
| F1 Score  | 80.89% |
| ROC-AUC   | 87.11% |

### Test Confusion Matrix

|              | Predicted Stay | Predicted Churn |
| ------------ | -------------- | --------------- |
| Actual Stay  | 121            | 47              |
| Actual Churn | 22             | 146             |

The model successfully identifies the majority of future churners while maintaining strong overall predictive performance.

---

# Project Deliverables

The repository contains all required submission artifacts:

* churn_model.ipynb
* model.pkl
* metrics.json
* error_analysis.md
* model_card.md
* requirements.txt
* README.md

The `metrics.json` file stores the final model evaluation metrics and selected decision threshold used during testing.

---

# Repository Structure

```text
part3-churn-prediction-model-card/
│
├── data/
│   └── rfm_modeling_snapshot.csv
│
├── outputs/
│   ├── roc_curve.png
│   ├── pr_curve.png
│   ├── confusion_matrix.png
│   ├── final_test_confusion_matrix.png
│   ├── feature_importance.png
│   ├── false_positive_examples.csv
│   └── false_negative_examples.csv
│
├── churn_model.ipynb
├── model.pkl
├── metrics.json
├── error_analysis.md
├── model_card.md
├── requirements.txt
├── .gitignore
└── README.md
```

---

# Installation

Clone the repository:

```bash
git clone https://github.com/Deepika825325/part3-churn-prediction-model-card.git
cd part3-churn-prediction-model-card
```

Create a virtual environment:

```bash
python -m venv venv
```

Activate the environment:

### Windows

```bash
venv\Scripts\activate
```

### Linux / macOS

```bash
source venv/bin/activate
```

Install dependencies:

```bash
pip install -r requirements.txt
```

---

# Running the Notebook

Launch Jupyter Notebook:

```bash
jupyter notebook
```

Open:

```text
churn_model.ipynb
```

Run all cells sequentially.

---

# Loading the Saved Model

```python
import joblib

model = joblib.load("model.pkl")
```

Generate predictions:

```python
predictions = model.predict(X)
```

Generate churn probabilities:

```python
probabilities = model.predict_proba(X)[:, 1]
```

---

# Outputs

The project generates:

* Model performance metrics
* ROC Curve
* Precision-Recall Curve
* Confusion Matrix
* Feature Importance Analysis
* Error Analysis Examples
* Trained Model Artifact

---

# Troubleshooting

## Missing Package Errors

Example:

```text
ModuleNotFoundError: No module named 'xgboost'
```

Solution:

```bash
pip install -r requirements.txt
```

---

## Model Loading Error

Example:

```text
FileNotFoundError: model.pkl
```

Solution:

* Ensure `model.pkl` exists in the repository root.
* Run the notebook completely before loading the model.

---

## Notebook Execution Issues

If variables appear undefined or outputs are missing:

* Restart the notebook kernel.
* Run all notebook cells sequentially from top to bottom.

---

# Key Business Insights

### Strongest Churn Indicators

* High recency
* Long gaps since website visits
* Higher return rates
* Lower loyalty engagement

### Strongest Retention Indicators

* Higher purchase frequency
* Higher spending behavior
* Increased website engagement
* Active loyalty program participation

These insights can help retention teams design more targeted and cost-effective customer engagement strategies.

---

# Author

**Deepika Kumari**

Academic Project Submission

**Part 3: Churn Prediction Model & Model Card**

D2C Customer Churn Intelligence Project
