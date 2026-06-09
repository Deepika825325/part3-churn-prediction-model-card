# Model Card: Customer Churn Prediction Model

## Model Overview

**Model Name:** Customer Churn Prediction Model

**Version:** 1.0

**Model Type:** Binary Classification

**Algorithm:** Logistic Regression

**Target Variable:** `churn_next_60d`

**Prediction Objective:** Predict whether a customer is likely to churn within the next 60 days.

**Development Date:** June 2026

---

# Business Context

Customer retention is a critical business objective for direct-to-consumer (D2C) brands because acquiring new customers is generally more expensive than retaining existing ones.

This model was developed to identify customers at elevated risk of churn before churn occurs. The predictions can be used by marketing, CRM, customer success, and retention teams to prioritize interventions such as:

* Personalized retention campaigns
* Loyalty program offers
* Re-engagement emails
* Customer support outreach
* Targeted promotional incentives

The model is designed to support decision-making and should not be treated as a fully automated customer management system.

---

# Intended Use

## Appropriate Uses

The model is intended to:

* Identify customers likely to churn within the next 60 days.
* Prioritize customers for retention campaigns.
* Support customer lifecycle management initiatives.
* Provide churn-risk insights to business stakeholders.
* Improve allocation of retention budgets.

## Intended Users

* Marketing Teams
* CRM Teams
* Retention Teams
* Customer Success Teams
* Business Analysts
* Product Managers

---

# Data Used

The model was trained using a customer-level modeling dataset containing information available at a defined customer snapshot date.

### Data Sources

* Customer Profile Data
* Transaction History
* Purchase Behavior Metrics
* Support Interaction Records
* Digital Engagement Data
* Marketing Interaction Data

### Required Model Inputs

The model expects customer-level features generated at the snapshot date, including:

* Customer profile attributes
* Transactional metrics
* Purchase behavior indicators
* Customer support interactions
* Marketing engagement signals
* Website engagement metrics

All inputs must be generated using information available on or before the snapshot date to prevent target leakage.

### Example Features

#### Customer Profile

* city_tier
* age_group
* acquisition_channel
* loyalty_tier
* preferred_category
* skin_type
* marketing_consent

#### Transactional Features

* recency
* frequency
* monetary

#### Purchase Behavior

* return_rate
* avg_discount
* category_diversity

#### Support Experience

* ticket_count
* avg_sentiment
* avg_resolution_hours

#### Customer Engagement

* sessions_30d
* campaign_clicks_30d
* email_opens_30d
* last_visit_days_ago

### Target Variable

`churn_next_60d`

* 1 = Customer churned within the next 60 days.
* 0 = Customer remained active.

### Target Distribution

The modeling dataset exhibits a relatively balanced target distribution.

| Class        | Percentage |
| ------------ | ---------- |
| Retained (0) | 53.04%     |
| Churned (1)  | 46.96%     |

Because the classes are reasonably balanced, model evaluation focused on Precision, Recall, F1-Score, and ROC-AUC rather than Accuracy alone.

---

# Leakage Prevention

Preventing target leakage was a primary modeling requirement.

The following controls were implemented:

* Only information available on or before the snapshot date was used.
* Customer identifiers were excluded from modeling.
* Snapshot date fields were excluded from modeling.
* Future transactions were not used as features.
* Future support interactions were not used as features.
* Future retention outcomes were not used as predictors.

The modeling workflow was designed to ensure realistic deployment performance.

---

# Data Splitting Strategy

The provided dataset split was used.

| Dataset    | Records |
| ---------- | ------- |
| Train      | 1,728   |
| Validation | 336     |
| Test       | 336     |

The validation set was used for:

* Model comparison
* Threshold optimization

The final model was retrained using Train + Validation data and evaluated on the unseen Test dataset.

---

# Models Evaluated

The following candidate models were evaluated:

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

This suggests that churn behavior within the available feature space is largely explained by stable linear relationships rather than highly complex non-linear interactions, making Logistic Regression both an effective and business-friendly choice for deployment.

---

# Final Model Performance

### Selected Threshold

**0.35**

The threshold was selected using validation-set threshold analysis.

A threshold of 0.35 achieved the highest F1-Score while substantially improving Recall compared to the default threshold of 0.50.

Because the primary business objective is churn prevention, Recall was prioritized to minimize missed retention opportunities.

### Test Set Performance

| Metric    | Value  |
| --------- | ------ |
| Accuracy  | 0.7946 |
| Precision | 0.7565 |
| Recall    | 0.8690 |
| F1 Score  | 0.8089 |
| ROC-AUC   | 0.8711 |

### Test Confusion Matrix

|              | Predicted Stay | Predicted Churn |
| ------------ | -------------- | --------------- |
| Actual Stay  | 121            | 47              |
| Actual Churn | 22             | 146             |

The model successfully identified the majority of future churners while maintaining acceptable precision.

---

# Key Drivers of Churn

The most influential churn predictors included:

### Higher Churn Risk

* High Recency
* Longer Time Since Last Visit
* Higher Return Rate
* Baby Care Category Preference
* Skin Care Category Preference
* Silver Loyalty Tier

### Lower Churn Risk

* Higher Purchase Frequency
* Higher Monetary Value
* More Sessions in the Last 30 Days
* Marketing Campaign Engagement
* Platinum Loyalty Tier

These insights can inform future retention strategies.

---

# Limitations

The model has several limitations:

1. Predictions are based on historical customer behavior and may not fully capture future market changes.
2. Customer motivations for churn may not always be observable through transactional and engagement data.
3. External factors such as competitor activity, pricing changes, or economic conditions are not represented.
4. Model performance may degrade over time as customer behavior evolves.
5. The model provides risk estimates and should not be interpreted as certainty.

---

# Ethical Considerations

### Potential Risks

* Over-targeting customers with retention campaigns.
* Unintended bias from historical behavioral patterns.
* Misallocation of retention incentives.

### Mitigation Measures

* Human review of campaign strategies.
* Regular fairness assessments.
* Continuous performance monitoring.
* Periodic retraining using updated data.

The model should support business decisions rather than replace human judgment.

---

# Monitoring Requirements

The following metrics should be monitored after deployment.

### Data Quality Monitoring

* Missing values
* Feature distribution changes
* Category drift

### Model Performance Monitoring

* Accuracy
* Precision
* Recall
* F1 Score
* ROC-AUC

### Recommended Retraining Strategy

The model should be retrained:

* Quarterly as part of routine model maintenance.
* Earlier if ROC-AUC decreases by more than 5%.
* When significant feature distribution drift is detected.
* After major business or customer-behavior changes.

Regular retraining helps maintain prediction quality as customer behavior evolves over time.

---

# When the Model Should Not Be Used

The model should not be used:

* As the sole basis for customer-facing decisions.
* For determining customer eligibility for services.
* For pricing decisions.
* For customer creditworthiness assessments.
* On customer populations substantially different from the training dataset.
* Without periodic monitoring and retraining.

---

# Conclusion

The Customer Churn Prediction Model provides a practical and interpretable approach for identifying customers at risk of churn within the next 60 days.

The final Logistic Regression model achieved strong predictive performance on unseen test data, particularly in terms of churn detection capability.

With a Recall of 86.90% and ROC-AUC of 87.11%, the model successfully identifies the majority of future churners while maintaining a practical balance between precision and recall.

The model can support proactive retention initiatives and help improve customer lifetime value when used alongside appropriate business processes, human oversight, and ongoing performance monitoring.
