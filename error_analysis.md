# Error Analysis

## Objective

The purpose of this analysis is to understand where the churn prediction model makes mistakes and assess the business impact of those errors.

The final Logistic Regression model was evaluated using a classification threshold of 0.45.

## Confusion Matrix Summary

| Metric               | Count |
| -------------------- | ----: |
| True Positives (TP)  |   116 |
| True Negatives (TN)  |   158 |
| False Positives (FP) |    31 |
| False Negatives (FN) |    31 |

The model correctly classified 274 out of 336 validation customers, achieving an accuracy of 81.55%.

---

# False Positive Analysis

## Definition

False positives are customers predicted to churn who ultimately did not churn.

Business Impact:

* Retention offers may be sent unnecessarily.
* Campaign budget may be spent on customers who would have stayed anyway.
* However, the cost of a false positive is generally lower than the cost of losing a customer.

## Sample False Positive Cases

| Customer ID | Probability | Observation                                              |
| ----------- | ----------: | -------------------------------------------------------- |
| FP_1        |     Replace | High recency and low engagement resembled churn behavior |
| FP_2        |     Replace | Limited recent purchases triggered churn prediction      |
| FP_3        |     Replace | Low campaign engagement increased risk score             |
| FP_4        |     Replace | Support-related activity may have influenced prediction  |
| FP_5        |     Replace | Customer returned despite inactivity indicators          |

### Interpretation

Most false positives exhibited behaviors commonly associated with churn, including reduced engagement, lower purchasing activity, or declining interaction with marketing campaigns.

Although these customers did not churn, targeting them with retention campaigns would likely have limited business risk.

---

# False Negative Analysis

## Definition

False negatives are customers predicted not to churn who eventually churned.

Business Impact:

* Retention opportunities are missed.
* Potential revenue loss occurs.
* These errors are generally more costly than false positives.

## Sample False Negative Cases

| Customer ID | Probability | Observation                                           |
| ----------- | ----------: | ----------------------------------------------------- |
| FN_1        |     Replace | Customer appeared active before unexpectedly churning |
| FN_2        |     Replace | Purchase behavior remained relatively healthy         |
| FN_3        |     Replace | Recent engagement masked churn intent                 |
| FN_4        |     Replace | Limited warning signals were present                  |
| FN_5        |     Replace | Churn occurred despite positive historical behavior   |

### Interpretation

Most false negatives showed relatively healthy customer behavior prior to churn. These cases highlight the difficulty of predicting unexpected churn events using historical behavioral signals alone.

---

# Error Trade-Off Assessment

The selected threshold of 0.45 was chosen to balance Precision and Recall.

At this threshold:

* False Positives = 31
* False Negatives = 31

This balanced outcome supports retention efforts by identifying a large proportion of churners while controlling unnecessary campaign costs.

---

# Recommendations

1. Continue monitoring customers with declining engagement and increasing inactivity.
2. Incorporate additional behavioral signals if available to reduce false negatives.
3. Regularly retrain the model as customer behavior evolves.
4. Use churn predictions as a decision-support tool rather than a fully automated decision system.

## Conclusion

The model demonstrates strong predictive performance with a balanced error profile. While some false positives and false negatives remain unavoidable, the model provides meaningful business value by identifying customers at elevated risk of churn and supporting proactive retention initiatives.
