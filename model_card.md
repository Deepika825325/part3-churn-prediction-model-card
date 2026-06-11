# Model Card: D2C Customer Churn Prediction Model

## Model Overview

This model predicts whether a customer is likely to churn within the next 60 days.

The objective is to help the business proactively identify at-risk customers and support targeted retention campaigns instead of applying retention incentives to all customers.

---

# Model Details

| Attribute               | Value                 |
| ----------------------- | --------------------- |
| Model Type              | Logistic Regression   |
| Problem Type            | Binary Classification |
| Target Variable         | churn_next_60d        |
| Prediction Window       | Next 60 Days          |
| Final Threshold         | 0.45                  |
| Training Framework      | Scikit-Learn          |
| Feature Count           | 25 Input Features     |
| Validation Dataset Size | 336 Customers         |

---

# Intended Use

The model is intended to:

* Identify customers at risk of churning.
* Support targeted retention campaigns.
* Prioritize outreach efforts for customer success teams.
* Assist CRM systems in generating churn-risk scores.

The model should be used as a decision-support tool rather than a fully automated decision-making system.

---

# Data Used

The model was trained using customer-level historical data available on or before the snapshot date.

Feature categories include:

### Customer Profile

* City Tier
* Age Group
* Acquisition Channel
* Loyalty Tier
* Preferred Category
* Marketing Consent

### Purchase Behavior

* Recency
* Frequency
* Monetary Value
* Discount Usage
* Category Diversity

### Support Signals

* Ticket Count
* Negative Ticket Rate
* Resolution Time

### Web & App Engagement

* Sessions
* Product Views
* Cart Activity
* Wishlist Activity
* Last Visit Information

### Campaign Engagement

* Email Opens
* Campaign Clicks

---

# Leakage Prevention

Several precautions were taken to prevent target leakage:

* Customer ID was excluded from modeling.
* Snapshot Date was excluded from modeling.
* Split information was excluded from modeling.
* Churn labels were used only as the target variable.
* All features were generated using information available before the snapshot date.
* Preprocessing was fitted only on the training dataset.

---

# Model Development Process

The following models were evaluated:

1. Logistic Regression
2. Random Forest
3. Gradient Boosting
4. XGBoost

Hyperparameter tuning was performed using GridSearchCV with 5-fold cross-validation.

The final model was selected based on validation performance and business relevance.

---

# Performance Metrics

Validation Performance:

| Metric    |  Score |
| --------- | -----: |
| Accuracy  | 0.8155 |
| Precision | 0.7891 |
| Recall    | 0.7891 |
| F1 Score  | 0.7891 |
| ROC-AUC   | 0.8827 |
| PR-AUC    | 0.8676 |

Confusion Matrix:

| Metric          | Count |
| --------------- | ----: |
| True Positives  |   116 |
| True Negatives  |   158 |
| False Positives |    31 |
| False Negatives |    31 |

---

# Threshold Selection

The default threshold of 0.50 was evaluated alongside multiple alternative thresholds.

A threshold of 0.45 was selected because it produced the highest F1 Score while maintaining a balanced trade-off between Precision and Recall.

Business rationale:

* Missing a churner may result in lost revenue.
* Contacting a small number of non-churning customers is generally less costly.
* A balanced threshold improves retention effectiveness while controlling campaign costs.

---

# Important Features

Key drivers identified through coefficient analysis and SHAP explanations include:

### Increased Churn Risk

* Higher Recency Days
* Higher Return Rate
* Higher Negative Ticket Rate
* Longer Time Since Last Visit

### Reduced Churn Risk

* Higher Monetary Value
* Higher Purchase Frequency
* More Sessions
* More Campaign Clicks
* Platinum Loyalty Membership

These findings align with expected customer behavior patterns.

---

# Limitations

The model has several limitations:

* Customer behavior may change over time.
* External market factors are not included.
* Promotional activities outside the dataset are not captured.
* Unexpected churn events remain difficult to predict.
* The model should not be interpreted as a guarantee that a customer will churn.

---

# Ethical Considerations

The model should be used responsibly.

Potential risks include:

* Over-targeting customers with retention campaigns.
* Incorrect churn predictions leading to unnecessary interventions.
* Bias arising from historical customer behavior patterns.

The model should support human decision-making rather than replace it.

---

# Monitoring Recommendations

The model should be monitored regularly for:

* Accuracy degradation
* Precision and Recall changes
* Feature drift
* Customer behavior shifts
* Campaign effectiveness

Recommended retraining frequency:

* Every 3 to 6 months
* Earlier if significant performance degradation is observed

---

# When Not To Use This Model

The model should not be used:

* As the sole basis for customer decisions.
* For legal, financial, or compliance decisions.
* For evaluating customer value without additional business context.
* When customer behavior has changed significantly since training.

---

# Conclusion

The final Logistic Regression model achieved strong predictive performance with a ROC-AUC of 0.8827 and an F1 Score of 0.7891.

The model provides a practical and interpretable approach for identifying customers at risk of churn and can support targeted retention strategies through CRM and marketing systems.
