# Error Analysis Report

## Objective

The purpose of this analysis is to examine prediction errors made by the final churn prediction model and understand their potential business impact.

While overall performance metrics provide a summary of model effectiveness, error analysis helps identify situations where the model may fail and highlights opportunities for future improvement.

The final model selected for deployment was a Logistic Regression classifier with a decision threshold of 0.35.

---

# Error Types Evaluated

Two error categories were analyzed:

### False Positives

Customers predicted to churn who actually remained active.

### False Negatives

Customers predicted to remain active who actually churned.

From a business perspective, False Negatives are generally more costly because they represent missed opportunities to retain customers before churn occurs.

---

# Model Performance Context

## Final Test Performance

| Metric    | Value  |
| --------- | ------ |
| Accuracy  | 79.46% |
| Precision | 75.65% |
| Recall    | 86.90% |
| F1 Score  | 80.89% |
| ROC-AUC   | 87.11% |

## Test Confusion Matrix

|              | Predicted Stay | Predicted Churn |
| ------------ | -------------- | --------------- |
| Actual Stay  | 121            | 47              |
| Actual Churn | 22             | 146             |

The model successfully identified the majority of future churners while maintaining a practical balance between precision and recall.

---

# False Positive Analysis

## Definition

False Positives are customers who were predicted to churn but ultimately remained active.

Although these customers were incorrectly classified, the business consequence is generally limited to unnecessary retention actions such as marketing offers, discount campaigns, or customer outreach.

---

## Sample False Positive Cases

| Customer ID | Recency | Frequency | Monetary | Sessions (30d) | Campaign Clicks | Predicted Probability |
| ----------- | ------- | --------- | -------- | -------------- | --------------- | --------------------- |
| CUST00006   | 51      | 5         | 3770.16  | 2              | 0               | 0.471                 |
| CUST00027   | 70      | 1         | 2128.34  | 11             | 2               | 0.743                 |
| CUST00048   | 61      | 4         | 2450.45  | 12             | 0               | 0.398                 |
| CUST00100   | 70      | 1         | 372.37   | 1              | 0               | 0.672                 |
| CUST00165   | 103     | 3         | 1825.77  | 2              | 0               | 0.640                 |

---

## Observations

Several false positive customers exhibited characteristics commonly associated with churn:

* High recency values.
* Low purchase frequency.
* Limited recent engagement.
* Reduced interaction with marketing campaigns.

For example:

### CUST00165

This customer had a recency value of 103 days and only two sessions in the previous 30 days. These characteristics strongly resemble customers who eventually churn, making the prediction reasonable despite the customer remaining active.

### CUST00100

This customer showed low spending activity, low frequency, and minimal engagement, all of which are historically associated with churn behavior.

These examples suggest that many false positives are borderline cases rather than completely incorrect predictions.

---

## Business Impact of False Positives

Potential consequences include:

* Unnecessary promotional discounts.
* Additional marketing communication costs.
* Reduced campaign efficiency.

However, these customers remain active, meaning no direct customer loss occurs.

### Risk Level

**Moderate**

False positives increase retention spending but do not result in lost revenue.

---

# False Negative Analysis

## Definition

False Negatives are customers who actually churned but were predicted to remain active.

This represents the most costly error type because no retention intervention would have been triggered before churn occurred.

---

## Sample False Negative Cases

| Customer ID | Recency | Frequency | Monetary | Sessions (30d) | Campaign Clicks | Predicted Probability |
| ----------- | ------- | --------- | -------- | -------------- | --------------- | --------------------- |
| CUST00093   | 85      | 1         | 759.64   | 16             | 4               | 0.285                 |
| CUST00145   | 30      | 1         | 502.35   | 0              | 0               | 0.309                 |
| CUST00157   | 0       | 5         | 2269.91  | 3              | 1               | 0.148                 |
| CUST00188   | 29      | 7         | 5810.17  | 11             | 0               | 0.085                 |
| CUST00267   | 29      | 4         | 4026.23  | 8              | 0               | 0.131                 |

---

## Observations

False negative customers often displayed behaviors typically associated with loyal customers.

### CUST00188

This customer had:

* High purchase frequency
* High spending
* Recent activity
* Strong engagement

Despite these positive indicators, the customer ultimately churned.

### CUST00157

This customer showed zero recency and multiple purchases, which strongly suggested retention rather than churn.

These examples indicate that some churn decisions may be driven by factors not captured in the available dataset, such as:

* Competitor activity
* Product dissatisfaction
* Personal preference changes
* External economic factors

---

## Business Impact of False Negatives

Potential consequences include:

* Lost customer lifetime value.
* Reduced repeat purchases.
* Missed retention opportunities.
* Lower effectiveness of retention campaigns.

Unlike false positives, these customers are lost without intervention.

### Risk Level

**High**

False negatives directly impact revenue and customer retention performance.

---

# Error Pattern Summary

## Common Characteristics of False Positives

* High recency.
* Low purchase frequency.
* Reduced engagement.
* Limited campaign interaction.

## Common Characteristics of False Negatives

* Recent purchase activity.
* Higher spending behavior.
* Moderate to high engagement.
* Behavior resembling retained customers.

These findings suggest that the model captures most observable churn signals but may struggle with churn caused by external factors not represented in the available features.

---

# Recommendations

Based on the error analysis, several improvements could further enhance model performance:

### 1. Incorporate Additional Behavioral Signals

Potential features include:

* Product review activity.
* Subscription interactions.
* Customer feedback patterns.
* Loyalty program redemption behavior.

### 2. Monitor False Negative Rates

Because false negatives represent lost customers, monitoring recall should remain a priority after deployment.

### 3. Periodic Model Retraining

Customer behavior evolves over time. Regular retraining will help maintain predictive performance.

### 4. Retention Campaign Optimization

Retention teams should focus on customers with elevated churn probabilities while continuously evaluating campaign effectiveness.

---

# Conclusion

The final churn prediction model demonstrates strong overall performance and successfully identifies the majority of future churners.

False positive errors primarily result in additional retention costs, while false negative errors represent missed opportunities to prevent customer loss.

The analysis suggests that most prediction errors occur in borderline customer segments whose behavior closely resembles the opposite class. Despite these challenges, the model provides meaningful business value and can support proactive customer retention initiatives when combined with appropriate operational processes and periodic performance monitoring.
