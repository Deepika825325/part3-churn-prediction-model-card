# Error Analysis Report

## Objective

The purpose of this analysis is to evaluate prediction errors made by the final churn prediction model and assess their potential business impact.

While aggregate performance metrics provide an overall measure of model effectiveness, error analysis helps identify situations where the model may struggle and highlights opportunities for future improvement.

The final model selected for deployment was a Logistic Regression classifier using a decision threshold of **0.35**, chosen to prioritize churn detection and minimize missed retention opportunities.

---

# Error Types Evaluated

Two categories of prediction errors were examined:

### False Positives

Customers predicted to churn who ultimately remained active.

### False Negatives

Customers predicted to remain active but ultimately churned.

From a business perspective, False Negatives are generally more costly because they represent missed opportunities to intervene before customer loss occurs.

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

### Error Distribution Summary

The final model produced:

* True Positives: 146
* True Negatives: 121
* False Positives: 47
* False Negatives: 22

The relatively low number of False Negatives is particularly important because missed churners represent direct revenue loss. The selected threshold of 0.35 intentionally prioritizes Recall, resulting in fewer missed churners at the cost of additional False Positives.

---

# False Positive Analysis

## Definition

False Positives are customers who were predicted to churn but ultimately remained active.

Although these customers were incorrectly classified, the business consequence is generally limited to unnecessary retention actions such as promotional offers, discount campaigns, or customer outreach efforts.

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

Several False Positive customers exhibited characteristics commonly associated with churn:

* High recency values.
* Low purchase frequency.
* Limited recent engagement.
* Reduced interaction with marketing campaigns.

### CUST00165

This customer had a recency value of 103 days and only two sessions during the previous 30 days. These characteristics closely resemble those of customers who eventually churn, making the prediction understandable despite the customer remaining active.

### CUST00100

This customer demonstrated low spending, low purchase frequency, and minimal engagement activity. These patterns are historically associated with elevated churn risk and therefore contributed to the model's prediction.

These examples indicate that the model is often making reasonable predictions based on observable churn signals. In several cases, the customers displayed inactivity and engagement patterns that closely resembled those of actual churners. From an operational perspective, these customers would still be valid candidates for low-cost retention interventions.

---

## Business Impact of False Positives

Potential consequences include:

* Unnecessary promotional discounts.
* Additional marketing communication costs.
* Reduced campaign efficiency.
* Lower retention campaign return on investment.

However, these customers remain active, meaning no direct customer loss occurs.

### Risk Level

**Moderate**

False Positives increase retention spending but generally do not result in lost revenue.

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

False Negative customers often displayed behaviors typically associated with retained customers.

### CUST00188

This customer demonstrated:

* High purchase frequency.
* High monetary value.
* Recent purchase activity.
* Strong engagement behavior.

Despite these positive indicators, the customer ultimately churned.

### CUST00157

This customer showed very recent purchasing activity, multiple purchases, and moderate engagement levels, all of which reduced the predicted churn probability.

These cases highlight an important limitation of behavioral churn modeling. Some customers who appear highly engaged and valuable may still churn due to factors outside the available data. This suggests that customer churn is influenced by both observable behavioral signals and external factors that are not captured in the current feature set.

Potential examples include:

* Competitor activity.
* Product dissatisfaction.
* Personal preference changes.
* Pricing sensitivity.
* External economic conditions.

---

## Business Impact of False Negatives

Potential consequences include:

* Lost customer lifetime value.
* Reduced repeat purchases.
* Missed retention opportunities.
* Lower effectiveness of retention programs.
* Direct revenue loss.

Unlike False Positives, these customers are lost without intervention.

### Risk Level

**High**

False Negatives directly impact revenue and customer retention performance.

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
* Behavioral patterns resembling retained customers.

These findings suggest that the model captures most observable churn signals but may struggle with churn caused by external factors not represented in the available features.

---

# Recommendations

Based on the error analysis, several opportunities exist to further improve model performance.

### 1. Incorporate Additional Behavioral Signals

Potential features include:

* Product review activity.
* Subscription interactions.
* Customer feedback patterns.
* Loyalty program redemption behavior.
* Customer service escalation history.

Additional behavioral signals may help distinguish genuinely loyal customers from customers who appear engaged but are at risk of leaving.

### 2. Monitor False Negative Rates

Because False Negatives represent missed retention opportunities, Recall should remain a primary monitoring metric after deployment.

Regular review of missed churners can help identify emerging churn patterns not currently captured by the model.

### 3. Periodic Model Retraining

Customer preferences, marketing strategies, and purchasing behavior evolve over time.

The model should be retrained on a quarterly basis and monitored for performance degradation, particularly in Recall and ROC-AUC, to ensure continued effectiveness.

### 4. Retention Campaign Optimization

Retention teams should prioritize customers with elevated churn probabilities while continuously evaluating campaign effectiveness and intervention costs.

The selected threshold can also be adjusted over time based on changing business objectives and retention budgets.

---

# Conclusion

The final churn prediction model demonstrates strong overall performance and successfully identifies the majority of future churners.

False Positive errors primarily result in additional retention costs, while False Negative errors represent missed opportunities to prevent customer loss and therefore carry greater business risk.

The analysis suggests that most prediction errors occur within borderline customer segments whose behavior closely resembles the opposite class. Despite these challenges, the model provides meaningful business value and offers a practical framework for supporting proactive customer retention initiatives.

When combined with ongoing monitoring, periodic retraining, and business oversight, the model can serve as an effective decision-support tool for customer retention programs.
