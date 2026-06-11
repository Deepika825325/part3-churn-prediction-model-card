# D2C Customer Churn Prediction Model & Model Card

## Project Overview

This project develops a machine learning system to predict whether a customer is likely to churn within the next 60 days.

The objective is to help a Direct-to-Consumer (D2C) personal-care brand proactively identify at-risk customers and support targeted retention strategies instead of applying blanket retention campaigns.

This repository corresponds to **Part 3: Churn Prediction Model & Model Card** of the D2C Customer Churn Intelligence & Retention API Capstone Project.

---

## Business Objective

Customer churn directly impacts revenue and customer lifetime value.

The goal of this project is to:

* Predict customers likely to churn in the next 60 days.
* Identify key drivers of churn.
* Support retention campaign prioritization.
* Provide interpretable predictions for business stakeholders.

---

## Dataset

The dataset contains customer-level historical information available before the snapshot date.

### Data Sources

* Customer Profile Data
* Order History
* Support Ticket History
* Web/App Activity
* Campaign Engagement Data
* Churn Labels
* Modeling Snapshot

### Target Variable

| Column         | Description                                                 |
| -------------- | ----------------------------------------------------------- |
| churn_next_60d | Indicates whether a customer churns within the next 60 days |

* 1 = Churn
* 0 = Non-Churn

---

## Leakage Prevention

To prevent target leakage:

* Customer ID was excluded from modeling.
* Snapshot Date was excluded from modeling.
* Split information was excluded from modeling.
* Only features available before the snapshot date were used.
* Preprocessing was fitted only on the training dataset.

---

## Repository Structure

```text
D2C-Customer-Churn-Prediction/
│
├── data/
│   └── rfm_modeling_snapshot.csv
│
├── outputs/
│   ├── roc_curve.png
│   ├── pr_curve.png
│   ├── threshold_optimization.png
│   ├── model_comparison.csv
│   ├── model_comparison_f1.png
│   ├── feature_importance.png
│   ├── shap_summary.png
│   ├── false_positives.csv
│   └── false_negatives.csv
│
├── churn_model.ipynb
├── model.pkl
├── metrics.json
├── error_analysis.md
├── model_card.md
├── requirements.txt
└── README.md
```

---

## Modeling Workflow

The project follows the following workflow:

1. Data Loading
2. Dataset Overview
3. Leakage Prevention
4. Data Preparation
5. Feature Engineering
6. Target Analysis
7. Train / Validation / Test Split
8. Preprocessing Pipeline
9. Baseline Model (Logistic Regression)
10. Advanced Models

    * Random Forest
    * Gradient Boosting
    * XGBoost
11. Hyperparameter Tuning
12. Model Comparison
13. ROC Curve Analysis
14. Precision-Recall Curve Analysis
15. Threshold Optimization
16. Feature Importance Analysis
17. SHAP Explainability
18. Error Analysis
19. Business Recommendations
20. Model Export

---

## Models Evaluated

| Model               | Type              |
| ------------------- | ----------------- |
| Logistic Regression | Baseline          |
| Random Forest       | Ensemble          |
| Gradient Boosting   | Ensemble          |
| XGBoost             | Gradient Boosting |

---

## Final Model Selection

The final selected model is:

### Logistic Regression

Selected because it achieved the best overall validation performance across Accuracy, F1-Score, ROC-AUC, and PR-AUC while maintaining strong interpretability.

### Selected Threshold

```text
0.45
```

The threshold was optimized to balance Precision and Recall for retention campaign effectiveness.

---

## Final Model Performance

| Metric    |  Score |
| --------- | -----: |
| Accuracy  | 0.8155 |
| Precision | 0.7891 |
| Recall    | 0.7891 |
| F1 Score  | 0.7891 |
| ROC-AUC   | 0.8827 |
| PR-AUC    | 0.8676 |

Confusion Matrix Summary:

| Metric          | Count |
| --------------- | ----: |
| True Positives  |   116 |
| True Negatives  |   158 |
| False Positives |    31 |
| False Negatives |    31 |

---

## Key Churn Drivers

Important churn indicators identified through coefficient analysis and SHAP explanations include:

### Higher Churn Risk

* Higher Recency Days
* Higher Return Rate
* Higher Negative Ticket Rate
* Longer Time Since Last Visit

### Lower Churn Risk

* Higher Monetary Value
* Higher Purchase Frequency
* Higher Session Activity
* Higher Campaign Engagement
* Platinum Loyalty Membership

---

## Running the Project

### 1. Clone Repository

```bash
git clone https://github.com/Deepika825325/part3-churn-prediction-model-card.git
cd D2C-Customer-Churn-Prediction
```

### 2. Install Dependencies

```bash
pip install -r requirements.txt
```

### 3. Run Notebook

Open and execute:

```text
churn_model.ipynb
```

---

## Saved Artifacts

| File              | Purpose                              |
| ----------------- | ------------------------------------ |
| model.pkl         | Trained Logistic Regression Pipeline |
| metrics.json      | Final Evaluation Metrics             |
| error_analysis.md | Error Analysis Report                |
| model_card.md     | Model Documentation                  |
| outputs/*.png     | Generated Visualizations             |

---

## Business Recommendations

* Prioritize customers with increasing inactivity.
* Monitor customers with negative support interactions.
* Use churn scores within CRM workflows.
* Trigger targeted retention campaigns for high-risk customers.
* Retrain the model periodically as customer behavior evolves.

---

## Conclusion

A churn prediction system was successfully developed using customer demographics, purchasing behavior, support interactions, engagement activity, and campaign response signals.

Among the evaluated models, Logistic Regression achieved the strongest overall performance with a ROC-AUC of 0.8827 and an F1 Score of 0.7891.

The resulting model provides an interpretable and business-friendly solution for identifying customers at risk of churn and supporting proactive retention strategies.
