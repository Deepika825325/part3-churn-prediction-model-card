# D2C Customer Churn Prediction Model

## Project Overview

This project develops a machine learning system to predict whether a customer is likely to churn within the next 60 days.

The objective is to help a Direct-to-Consumer (D2C) personal care brand proactively identify at-risk customers and support targeted retention strategies instead of applying blanket retention campaigns.

This repository corresponds to **Part 3: Churn Prediction Model & Model Card** of the D2C Customer Churn Intelligence & Retention API Capstone Project.

---

## Business Objective

Customer churn directly impacts revenue, retention costs, and customer lifetime value (CLV).

The objectives of this project are to:

* Predict customers likely to churn within the next 60 days.
* Identify key behavioural drivers of churn.
* Support retention campaign prioritisation.
* Enable proactive customer retention interventions.
* Provide interpretable predictions for business stakeholders.

---

## Dataset

The model was trained using a customer-level snapshot dataset containing only information available before the prediction date.

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
* 0 = Retained

### Dataset Summary

| Metric          | Value  |
| --------------- | ------ |
| Customers       | 2,400  |
| Orders          | 10,009 |
| Support Tickets | 1,921  |
| Churn Rate      | 46.96% |

---

## Leakage Prevention

The following safeguards were implemented to prevent target leakage:

* Customer ID excluded from modeling.
* Snapshot date excluded from modeling.
* Train/validation/test split indicators excluded.
* Only historical features available before the snapshot date were used.
* Preprocessing fitted exclusively on training data.
* No future information included in feature generation.

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

The project followed the end-to-end machine learning lifecycle:

1. Data Loading
2. Dataset Exploration
3. Leakage Prevention
4. Feature Engineering
5. Train / Validation / Test Split
6. Data Preprocessing Pipeline
7. Baseline Logistic Regression
8. Ensemble Models

   * Random Forest
   * Gradient Boosting
   * XGBoost
9. Hyperparameter Tuning
10. Model Comparison
11. ROC-AUC Analysis
12. Precision-Recall Analysis
13. Threshold Optimisation
14. Feature Importance Analysis
15. SHAP Explainability
16. Error Analysis
17. Fairness Assessment
18. Business Recommendations
19. Model Export

---

## Models Evaluated

### Hyperparameter Tuning Results

| Model               | Best Parameters                                    | Best CV F1 |
| ------------------- | -------------------------------------------------- | ---------- |
| Logistic Regression | C = 1                                              | 0.7783     |
| Random Forest       | n_estimators=200, max_depth=15, min_samples_leaf=2 | 0.7748     |
| Gradient Boosting   | learning_rate=0.10, n_estimators=100, max_depth=3  | 0.7759     |
| XGBoost             | learning_rate=0.05, n_estimators=200, max_depth=3  | 0.7828     |

### Model Comparison

| Model               | Accuracy | Precision | Recall | F1     | ROC-AUC | PR-AUC |
| ------------------- | -------- | --------- | ------ | ------ | ------- | ------ |
| Logistic Regression | 0.8155   | 0.8058    | 0.7619 | 0.7832 | 0.8827  | 0.8676 |
| Random Forest       | 0.8036   | 0.8000    | 0.7347 | 0.7660 | 0.8794  | 0.8585 |
| Gradient Boosting   | 0.7946   | 0.7671    | 0.7619 | 0.7645 | 0.8776  | 0.8637 |
| XGBoost             | 0.7917   | 0.7550    | 0.7755 | 0.7651 | 0.8799  | 0.8644 |

---

## Final Model Selection

### Selected Model

**Logistic Regression**

Although XGBoost achieved the highest cross-validation F1 score (0.7828), Logistic Regression achieved the strongest overall performance on the held-out evaluation set while providing significantly better interpretability.

### Selected Threshold

```text
0.45
```

The threshold was selected through threshold optimisation because it produced the highest F1 Score while balancing Precision and Recall.

---

## Final Test Set Performance

| Metric    | Score  |
| --------- | ------ |
| Accuracy  | 0.8155 |
| Precision | 0.7891 |
| Recall    | 0.7891 |
| F1 Score  | 0.7891 |
| ROC-AUC   | 0.8827 |
| PR-AUC    | 0.8676 |

### Confusion Matrix

|                  | Predicted: No Churn | Predicted: Churn |
| ---------------- | ------------------- | ---------------- |
| Actual: No Churn | 158                 | 31               |
| Actual: Churn    | 31                  | 116              |

---

## Key Churn Drivers

### Increased Churn Risk

* Higher Recency Days
* Higher Return Rate
* Higher Negative Ticket Rate
* Higher Average Discount Usage
* Longer Time Since Last Visit
* Baby Care Product Preference

### Reduced Churn Risk

* Higher Monetary Value
* Higher Purchase Frequency
* Higher Session Activity
* Higher Campaign Engagement
* Platinum Loyalty Membership
* Organic Acquisition Channel

---

## Fairness Assessment

Fairness was evaluated across Age Groups and City Tiers.

### Findings

* Predicted churn rates closely aligned with observed churn rates across demographic groups.
* No severe prediction disparities were observed.
* No evidence of material demographic bias was identified.
* Fairness monitoring is recommended during future retraining cycles.

---

## Running the Project

### Clone Repository

```bash
git clone https://github.com/Deepika825325/part3-churn-prediction-model-card.git
cd D2C-Customer-Churn-Prediction
```

### Install Dependencies

```bash
pip install -r requirements.txt
```

### Run Notebook

```text
churn_model.ipynb
```

Execute all notebook cells sequentially.

---

## Saved Artifacts

| File              | Purpose                              |
| ----------------- | ------------------------------------ |
| model.pkl         | Trained Logistic Regression Pipeline |
| metrics.json      | Final Evaluation Metrics             |
| error_analysis.md | Detailed Error Analysis              |
| model_card.md     | Full Model Documentation             |
| outputs/*.png     | Visualisations and Evaluation Charts |

---

## Business Recommendations

* Prioritise customers with increasing inactivity and purchase gaps.
* Closely monitor customers with negative support interactions.
* Use churn probability scores within CRM workflows.
* Apply targeted retention campaigns instead of blanket discounts.
* Consider lower intervention thresholds for Gold and Platinum customers.
* Retrain the model periodically as customer behaviour evolves.

---

## Conclusion

A complete churn prediction system was developed using customer demographics, purchasing behaviour, support interactions, web engagement activity, and campaign response signals.

The final Logistic Regression model achieved:

* Accuracy: 0.8155
* F1 Score: 0.7891
* ROC-AUC: 0.8827
* PR-AUC: 0.8676

Among all evaluated models, Logistic Regression provided the strongest balance of predictive performance, interpretability, and operational usability.

The resulting solution provides a production-ready foundation for proactive customer retention, churn monitoring, and CRM-driven intervention strategies.
